---
layout: post
---

#국민연금 데이터 가져오기

### app.config 파일

```no-highlight
 <appSettings>
    <add key="ADDR_FILE_PATH" value="C:\Users\사용자\Desktop\log\" />
    <add key="XML_FILE_PATH" value="C:\Users\사용자\Desktop\log\xmls\" />
    <add key="API_URL" value="http://apis.data.go.kr/B552015/NpsBplcInfoInqireService/" />
    <add key="ServiceKey" value="서비스인증키값" />
    <add key="PAGE_SIZE" value="1000" />
    <add key="START_MONTH" value="201707" />
    <add key="END_MONTH" value="201802" />
    <add key="IS_CALL_API" value="1" />
    <add key="ADD_END_COMP" value="0" /><!-- 1보다 작으면 탈퇴된 기업정보 제외 -->
  </appSettings>
```


## 소스 시작..
### Main(string[] args)
```no-highlight

public static bool isGetListError { get; set; }
public static bool isGetDetailError { get; set; }
public static bool isGetSttusError { get; set; }

/// <summary>
/// 모든 메서드가 접근 가능한 모델, 해당 모델에 데이터를 모두 담는 그릇으로 사용
/// </summary>
public static List<WkplModel> fulldata = new List<WkplModel>();

static void Main(string[] args)
{
	/*
	 * ldong_addr_mgpl_dg_cd : 시도(행정자치부 법정동 주소코드 참조)
	 * ldong_addr_mgpl_sggu_cd : 시군구(행정자치부 법정동 주소코드 참조)
	 * ldong_addr_mgpl_sggu_emd_cd :   읍면동(행정자치부 법정동 주소코드 참조) 
	 * wkpl_nm : 사업장명
	 * bzowr_rgst_no : 사업자등록번호(앞에서 6자리)
	 * pageNo : 페이지번호
	 * numOfRows : 행갯수
	 * wkpl_styl_dvcd : 사업장형태구분코드 (1:법인, 2:개인)
	 */
	isGetListError = false;
	isGetDetailError = false;
	isGetSttusError = false;

	string servicekey = ConfigurationSettings.AppSettings["ServiceKey"];
	int pageSize = int.Parse(ConfigurationSettings.AppSettings["PAGE_SIZE"]);

	// 법정동코드 파일을 읽어와 법정동코드 리스트를 만든다
	List<AddrCode> regionStrcs = GetRegionStructure();

	// 법정동코드별로 seq list정보 가져오기
	GetRgstList(servicekey, pageSize, regionStrcs);
	// 가져온 법정동코드별 seq count값을 기록
	CreateRgstListFile(fulldata, regionStrcs, "list");

	// 기존에 저장된 seq번호는 fulldata리스트에서 제외처리 한다
	MakeRgstList();
	
	// 가져온 리스트 데이터에 상세내용을 넣어준다
	int detailCnt = SetDetailData(servicekey);

	// 가져온 리스트 데이터에 현황정보를 넣어준다 (월별 입,퇴사자)
	int sttusCnt = SetPdAcctoSttusData(servicekey);
	
	// 가져온 데이터를 DB에 맞게 변환해준다 (메소드 상세소스 불포함)
	List<WkplSaveModel> saveitems = ConvertdataToSavedata(fulldata, regionStrcs);

	// 데이터 최종 DB저장 (메소드 상세소스 불포함)
	SaveCompInfoData(saveitems);
	
}

```

### 법정동코드 값 가져오기 

국민연금 api 데이터를 가져오는 부분은 법정동코드별로 루프 돌리면서 가져오거나, 사업장코드, 사업장명을 입력해서 가져오는 경우 3가지가 있다.
우리나라의 모든 사업장 정보(기업정보)를 가져오기 위해서 사업장 코드별로 호출하면 사업장코드 6자리 입력 경우의 수가 법정동 코드별 입력수보다 많아서 법정동코드별로 가져오는 방식을 선택했다.
법정동코드값은 [행정표준코드관리시스템](https://www.code.go.kr/stdcode/regCodeL.do)에서 법정동 코드자료를 다운받아 사용했다.

#### GetRegionStructure 법정동코드 값 가져오기
```

/// <summary>
/// 법정동코드 값 가져오기
/// </summary>
/// <returns></returns>
private static List<AddrCode> GetRegionStructure()
{
	List<AddrCode> returnlist = new List<AddrCode>();
	string addrFilePath = ConfigurationSettings.AppSettings["ADDR_FILE_PATH"] + "AddressCode.txt";

	string[] textVal = System.IO.File.ReadAllLines(addrFilePath, Encoding.UTF8);
	if (textVal.Length > 0)
	{
		for (int i = 0; i < textVal.Length; i++)
		{
			try
			{
				string[] valSplt = textVal[i].Split('\t');
				AddrCode addItem = new AddrCode();
				addItem.sido_code = valSplt[0];
				addItem.sigungu_code = valSplt[1];
				addItem.eubmyeondong_code = valSplt[2];
				addItem.fullCode = valSplt[3];
				addItem.code = addItem.fullCode.Substring(0, 8);
				addItem.codeName = valSplt[4];
				addItem.isDel = valSplt[5];
				returnlist.Add(addItem);
			}
			catch (Exception e)
			{
				log.Error(string.Format("GetRegionStructure ERROR message:{0}", e.Message));
			}

		}
	}

	return returnlist;
}

```


#### GetRgstList seq 목록 가져오기
```

/// <summary>
/// 모든 사업자번호를 조회하여 가져온다
/// ldong_addr_mgpl_dg_cd - 시도(행정자치부 법정동 주소코드 참조)
/// ldong_addr_mgpl_sggu_cd - 시군구(행정자치부 법정동 주소코드 참조)
/// ldong_addr_mgpl_sggu_emd_cd - 읍면동(행정자치부 법정동 주소코드 참조)
/// </summary>
/// <param name="servicekey"></param>
/// <param name="pageSize"></param>
/// <param name="addrCodeList"></param>
/// <returns></returns>
static int GetRgstList(string servicekey, int pageSize, List<AddrCode> addrCodeList)
{
	// 가져온 지역코드값 리스트를 루프돌리면서 파라미터로 제공
	foreach (AddrCode addrCode in addrCodeList)
	{
		int rgstCnt = 0;
		int page = 1;
		bool isContinue = true;

		// 에러나는 페이지는 건너뛰고 다음 페이지 계속 호출해서 진행.
		while (isContinue)
		{
			try
			{
				XmlDocument xml = new XmlDocument();
				string paramStr = string.Format("ldong_addr_mgpl_dg_cd={0}&ldong_addr_mgpl_sggu_cd={1}&ldong_addr_mgpl_sggu_emd_cd={2}&pageNo={3}&numOfRows={4}", addrCode.sido_code, addrCode.sigungu_code, addrCode.eubmyeondong_code, page, pageSize);
				xml = CallApiData('L', servicekey, "", paramStr);

				// api호출시 에러가 나면 그대로 작업을 멈춘다
				var errorNode = xml.GetElementsByTagName("errMsg");
				if (errorNode != null && errorNode.Count > 0)
				{
					log.Debug(xml.InnerText);
					if (xml.InnerText.Contains("LIMITED_NUMBER_OF_SERVICE_REQUESTS_EXCEEDS_ERROR"))
					{
						isGetListError = true;
					}
					//return -1;
				}
				else
				{

					int dataCnt = xml.GetElementsByTagName("item").Count;
					if (dataCnt > 0)
					{
						isContinue = SetModelData(xml);
						log.Debug(string.Format("page:{0}, page size:{1}, count:{2}", page, pageSize, dataCnt));
						page = page + 1;
						if (xml.GetElementsByTagName("totalCount").Count > 0)
						{
							rgstCnt = int.Parse(xml.GetElementsByTagName("totalCount")[0].InnerText);
						}
						if (page > Math.Ceiling((double)rgstCnt / pageSize))
						{
							isContinue = false;
						}
					}
					else
					{
						isContinue = false;
						log.Debug(string.Format("no items.."));
					}
				}

			}
			catch (Exception e)
			{
				log.Error(string.Format("GetRgstList ERROR : {0} / 시도 + 시군구 + 읍면동:{1}", e.Message, addrCode.code));
				isContinue = false;
			}
		}
		// 사업자번호 별로 건수 데이터 기록
		SaveBzowrRgstCnt(addrCode.code, rgstCnt);
	}

	fulldata = fulldata.OrderBy(p => p.dataCrtYm).ToList();
	return fulldata.Count;
	//return returnlist;
}

```


#### CallApiData api호출해서 데이터 저장하기

```

/// <summary>
/// api 데이터 가져오기..
/// apis.data.go.kr/B552015/NpsBplcInfoInqireService/getBassInfoSearch?serviceKey={key}&bzowr_rgst_no={사업자번호}&pageNo={페이지 번호}&numOfRows={한번에 보여줄 갯수}
/// apis.data.go.kr/B552015/NpsBplcInfoInqireService/getDetailInfoSearch?serviceKey={key}seq={식별번호}
/// apis.data.go.kr/B552015/NpsBplcInfoInqireService/getPdAcctoSttusInfoSearch?serviceKey={key}&seq={식별번호}&data_crt_ym=
/// </summary>
/// <param name="callType">L:list-사업장 정보조회 서비스, D:detail-상세 정보조회, S:status-기간별 현황 정보조회</param>
/// <param name="servicekey">api 사이트에서 발급받은 키값</param>
/// <param name="seq">식별번호</param>
/// <param name="paramStr"></param>
/// <returns></returns>
static XmlDocument CallApiData(char callType, string servicekey, string seq, string paramStr)
{
	XmlDocument xml = new XmlDocument();
	string xmlFilePath = ConfigurationSettings.AppSettings["XML_FILE_PATH"];
	string adsimpleFullUrl = ConfigurationSettings.AppSettings["API_URL"];
	string isCallApi = ConfigurationSettings.AppSettings["IS_CALL_API"];

	if (callType == 'L')
	{
		//LIMITED_NUMBER_OF_SERVICE_REQUESTS_EXCEEDS_ERROR 에러 예방을 위해 이미 가져온 api주소는 제외한다
		string[] strSplt = paramStr.Split('&');
		string codeStr = "";
		codeStr = strSplt[0].Substring(strSplt[0].IndexOf("=") + 1, strSplt[0].Length - (strSplt[0].IndexOf("=") + 1));
		codeStr = codeStr + strSplt[1].Substring(strSplt[1].IndexOf("=") + 1, strSplt[1].Length - (strSplt[1].IndexOf("=") + 1));
		codeStr = codeStr + strSplt[2].Substring(strSplt[2].IndexOf("=") + 1, strSplt[2].Length - (strSplt[2].IndexOf("=") + 1));
		string pageStr = strSplt[3].Substring(strSplt[3].IndexOf("=") + 1, strSplt[3].Length - (strSplt[3].IndexOf("=") + 1));
		xmlFilePath = xmlFilePath + string.Format("list_{0}_{1}.xml", codeStr, pageStr);
		if (System.IO.File.Exists(xmlFilePath))
		{
			xml.Load(xmlFilePath);
			return xml;
		}

		adsimpleFullUrl = adsimpleFullUrl + string.Format("getBassInfoSearch?{1}&serviceKey={0}", servicekey, paramStr);
	}
	else if (callType == 'D')
	{
		xmlFilePath = xmlFilePath + string.Format("detail_{0}.xml", seq);
		if (System.IO.File.Exists(xmlFilePath))
		{
			xml.Load(xmlFilePath);
			return xml;
		}
		adsimpleFullUrl = adsimpleFullUrl + string.Format("getDetailInfoSearch?seq={1}&serviceKey={0}", servicekey, seq);
	}
	else
	{
		xmlFilePath = xmlFilePath + string.Format("sttus_{0}.xml", seq);
		if (System.IO.File.Exists(xmlFilePath))
		{
			xml.Load(xmlFilePath);
			return xml;
		}
		adsimpleFullUrl = adsimpleFullUrl + string.Format("getPdAcctoSttusInfoSearch?seq={1}&data_crt_ym=&serviceKey={0}", servicekey, seq);
	}

	try
	{
		Uri url = new Uri(adsimpleFullUrl);
		if (isCallApi == "1")
		{
			if (callType == 'L' && !isGetListError)
			{
				log.Debug(string.Format("call <LIST> api : {0}", url));
				var client = new HttpClient();
				Task<HttpResponseMessage> getStringTask = client.GetAsync(url);
				HttpResponseMessage urlcontents = getStringTask.Result;
				var jsonString = urlcontents.Content.ReadAsStringAsync();
				xml.LoadXml(jsonString.Result);
				client.Dispose();
			}
			else if (callType == 'D' && !isGetDetailError)
			{
				log.Debug(string.Format("call <DETAIL> api : {0}", url));
				var client = new HttpClient();
				Task<HttpResponseMessage> getStringTask = client.GetAsync(url);
				HttpResponseMessage urlcontents = getStringTask.Result;
				var jsonString = urlcontents.Content.ReadAsStringAsync();
				xml.LoadXml(jsonString.Result);
				client.Dispose();
			}
			else if (callType == 'S' && !isGetSttusError)
			{
				log.Debug(string.Format("call <STTUS> api : {0}", url));
				var client = new HttpClient();
				Task<HttpResponseMessage> getStringTask = client.GetAsync(url);
				HttpResponseMessage urlcontents = getStringTask.Result;
				var jsonString = urlcontents.Content.ReadAsStringAsync();
				xml.LoadXml(jsonString.Result);
				client.Dispose();
			}

			if (xml.GetElementsByTagName("item").Count > 0)
			{
				xml.Save(xmlFilePath);
			}
		}
	}
	catch (Exception e)
	{
		log.Error(string.Format("CallApiData ERROR : {0} / url:{1}", e.Message, adsimpleFullUrl));
		//throw;
	}

	return xml;
}

```


#### SetModelData xml 데이터를 리스트 model에 맞게 넣어준다

```

/// <summary>
/// xml 데이터를 model에 담는다
/// </summary>
/// <param name="xml"></param>
/// <param name="returnlist"></param>
/// <returns></returns>
private static bool SetModelData(XmlDocument xml)
{
	int startMonth = int.Parse(ConfigurationSettings.AppSettings["START_MONTH"]);
	int endMonth = int.Parse(ConfigurationSettings.AppSettings["END_MONTH"]);
	int addEndComp = int.Parse(ConfigurationSettings.AppSettings["ADD_END_COMP"]);

	XmlNodeList xmlDataList = xml.GetElementsByTagName("item");
	// 만약 하위 노드가 없다면! 데이터가 없다면 xml 데이터 담는 작업 중지!
	if (xmlDataList.Count < 1)
	{
		log.Debug("SetModelData Stop : rootNode.ChildNodes.Count < 1");
		return false;
	}

	foreach (XmlNode adNode in xmlDataList)
	{
		try
		{
			WkplModel saveItem = new WkplModel();
			foreach (XmlNode child in adNode.ChildNodes)
			{
				switch (child.Name)
				{
					case "bzowrRgstNo":
						saveItem.bzowrRgstNo = child.InnerText;
						break;
					case "dataCrtYm":
						saveItem.dataCrtYm = child.InnerText;
						break;
					case "ldongAddrMgplDgCd":
						saveItem.ldongAddrMgplDgCd = child.InnerText;
						break;
					case "ldongAddrMgplSgguCd":
						saveItem.ldongAddrMgplSgguCd = child.InnerText;
						break;
					case "ldongAddrMgplSgguEmdCd":
						saveItem.ldongAddrMgplSgguEmdCd = child.InnerText;
						break;
					case "seq":
						saveItem.seq = child.InnerText;
						break;
					case "wkplJnngStcd":
						saveItem.wkplJnngStcd = child.InnerText;
						break;
					case "wkplNm":
						saveItem.wkplNm = child.InnerText;
						break;
					case "wkplRoadNmDtlAddr":
						saveItem.wkplRoadNmDtlAddr = child.InnerText;
						break;
					case "wkplStylDvcd":
						saveItem.wkplStylDvcd = child.InnerText;
						break;
					default: break;
				}
			}

			if (addEndComp < 1)
			{
				// 삭제된 기업정보 제외
				if (saveItem.wkplJnngStcd == "1")
				{
					// start, end 월에 해당하는 기간의 데이터만 넣기
					if (int.Parse(saveItem.dataCrtYm) >= startMonth && int.Parse(saveItem.dataCrtYm) <= endMonth)
					{
						fulldata.Add(saveItem);
					}
				}
			}
			else
			{
				// 사업장 종료가 된 데이터도 넣음
				// start, end 월에 해당하는 기간의 데이터만 넣기
				if (int.Parse(saveItem.dataCrtYm) >= startMonth && int.Parse(saveItem.dataCrtYm) <= endMonth)
				{
					fulldata.Add(saveItem);
				}
			}

		}
		catch (Exception e)
		{
			log.Error(string.Format("SetModelData ERROR : {0}", e.Message));
			return false;
		}

	}

	return true;
}

```

#### MakeRgstList 상세데이터 api 호출시 기존에 저장된 데이터는 중복호출하지 않도록...

DB에 데이터 저장할때 seq값을 _savesItems.txt 파일에 저장해둔다
다시 api 데이터를 호출할때는 저장했던 seq번호를 중복 호출하지 않도록 (작업시간 단축..)

```

/// <summary>
/// 국민연금 데이터 정리
/// 불필요한 seq데이터는 제외 
/// 1. 삭제된 기업정보 제외
/// 2. config에서 설정한 기간에 해당하지 않는 데이터 제외
/// 3. 이미 DB에 저장된 데이터 제외
/// </summary>
private static void MakeRgstList()
{
	log.Debug("1+2 del fulldata count : " + fulldata.Count);

	List<string> delItems = new List<string>();
	string savedPath = ConfigurationSettings.AppSettings["ADDR_FILE_PATH"] + "_savedItems.txt";
	try
	{
		string[] textVal = System.IO.File.ReadAllLines(savedPath, Encoding.UTF8);
		if (textVal.Length > 0)
		{
			for (int i = 0; i < textVal.Length; i++)
			{
				delItems.Add(textVal[i]);
			}
		}
	}
	catch (Exception e)
	{
		log.Error(string.Format("MakeRgstList ERROR : {0}", e.Message));
	}

	// 3. 이미 db에 저장된 데이터 제외
	log.Debug("1+2+3 del items : " + delItems.Count);
	foreach (string seq in delItems)
	{
		var delitem = fulldata.Where(p => p.seq == seq).FirstOrDefault();
		fulldata.Remove(delitem);
	}
	log.Debug("fulldata count : " + fulldata.Count);
}

```

#### SetDetailData 상세데이터 가져오기

```

/// <summary>
/// 상세 데이터를 담는다
/// </summary>
/// <param name="returnlist"></param>
/// <param name="servicekey"></param>
/// <returns></returns>
static int SetDetailData(string servicekey)
{
	if (fulldata.Count < 1)
	{
		log.Debug("SetDetailData Debug : no Detail Data..");
		return 0;
	}

	List<WkplModel> delItems = new List<WkplModel>();

	foreach (WkplModel saveItem in fulldata)
	{
		string errorSeq = "";
		bool isfail = true;
		try
		{
			if (saveItem.seq.Length < 1)
				continue;

			XmlDocument detailXml = CallApiData('D', servicekey, saveItem.seq, "");

			// api호출시 에러가 나면 그대로 작업을 멈춘다
			var errorNode = detailXml.GetElementsByTagName("errMsg");
			if (errorNode != null && errorNode.Count > 0)
			{
				log.Debug(detailXml.InnerXml);
				if (detailXml.InnerText.Contains("LIMITED_NUMBER_OF_SERVICE_REQUESTS_EXCEEDS_ERROR"))
				{
					isGetDetailError = true;
				}
				//return 0;
				continue;
			}

			errorSeq = saveItem.seq;
			XmlNodeList detailXmlDataList = detailXml.GetElementsByTagName("item");
			XmlNode detailRootNode = detailXmlDataList[0];
			if (detailRootNode != null && detailRootNode.ChildNodes.Count > 0)
			{
				XmlNodeList detailAdNodes = detailRootNode.ChildNodes;
				foreach (XmlNode item in detailAdNodes)
				{
					switch (item.Name)
					{
						case "adptDt":
							saveItem.adptDt = item.InnerText;
							break;
						case "bzowrRgstNo":
							saveItem.bzowrRgstNo = item.InnerText;
							break;
						case "crrmmNtcAmt":
							saveItem.crrmmNtcAmt = double.Parse(item.InnerText);
							break;
						case "jnngpCnt":
							saveItem.jnngpCnt = int.Parse(item.InnerText);
							break;
						case "ldongAddrMgplDgCd":
							saveItem.ldongAddrMgplDgCd = item.InnerText;
							break;
						case "ldongAddrMgplSgguCd":
							saveItem.ldongAddrMgplSgguCd = item.InnerText;
							break;
						case "ldongAddrMgplSgguEmdCd":
							saveItem.ldongAddrMgplSgguEmdCd = item.InnerText;
							break;
						case "scsnDt":
							saveItem.scsnDt = item.InnerText;
							break;
						case "vldtVlKrnNm":
							saveItem.vldtVlKrnNm = item.InnerText;
							break;
						case "wkplIntpCd":
							saveItem.wkplIntpCd = item.InnerText;
							break;
						case "wkplJnngStcd":
							saveItem.wkplJnngStcd = item.InnerText;
							break;
						case "wkplNm":
							saveItem.wkplNm = item.InnerText;
							break;
						case "wkplRoadNmDtlAddr":
							saveItem.wkplRoadNmDtlAddr = item.InnerText;
							break;
						case "wkplStylDvcd":
							saveItem.wkplStylDvcd = item.InnerText;
							break;
						default: break;
					}
				} // foreach

				saveItem.hasDetail = true;
				isfail = false;
			}

			log.Debug(string.Format("<{0}> get detail.. seq={1}", isfail ? "Fail" : "Success", saveItem.seq));
			if (isfail)
				delItems.Add(saveItem);
		}
		catch (Exception e)
		{
			log.Error(string.Format("SetDetailData ERROR : {0} / seq:{1}", e.Message, errorSeq));
		}

	} // foreach (AdListModel saveItem in saveItems)

	// 삭제해야할 데이터 제외
	foreach (WkplModel delItem in delItems)
	{
		fulldata.Remove(delItem);
	}

	return fulldata.Where(p => p.hasDetail == true).Count();
	//return returnlist;
}

```

#### SetPdAcctoSttusData 월별 입사자/퇴사자 정보 가져오기

```

/// <summary>
/// 기간별 현황 정보조회 데이터를 담는다
/// </summary>
/// <param name="detailData"></param>
/// <param name="servicekey"></param>
/// <returns></returns>
static int SetPdAcctoSttusData(string servicekey)
{
	if (fulldata.Count < 1)
	{
		log.Debug("SetPdAcctoSttusData Debug : no Detail Data..");
		return 0;
	}

	foreach (WkplModel saveItem in fulldata)
	{
		string errorSeq = "";
		bool isfail = true;
		try
		{
			if (saveItem.seq.Length < 1)
				continue;

			XmlDocument detailXml = CallApiData('S', servicekey, saveItem.seq, "");

			// api호출시 에러가 나면 그대로 작업을 멈춘다
			var errorNode = detailXml.GetElementsByTagName("errMsg");
			if (errorNode != null && errorNode.Count > 0)
			{
				log.Debug(detailXml.InnerXml);
				if (detailXml.InnerText.Contains("LIMITED_NUMBER_OF_SERVICE_REQUESTS_EXCEEDS_ERROR"))
				{
					isGetSttusError = true;
				}
				//return 0;
				continue;
			}

			errorSeq = saveItem.seq;
			XmlNodeList detailXmlDataList = detailXml.GetElementsByTagName("item");
			XmlNode detailRootNode = detailXmlDataList[0];
			if (detailRootNode != null && detailRootNode.ChildNodes.Count > 0)
			{
				XmlNodeList detailAdNodes = detailRootNode.ChildNodes;
				foreach (XmlNode item in detailAdNodes)
				{
					switch (item.Name)
					{
						case "lssJnngpCnt":
							saveItem.lssJnngpCnt = item.InnerText.Length < 1 || item.InnerText == "" ? 0 : int.Parse(item.InnerText);
							saveItem.hasCnt = true;
							break;
						case "nwAcqzrCnt":
							saveItem.nwAcqzrCnt = item.InnerText.Length < 1 || item.InnerText == "" ? 0 : int.Parse(item.InnerText);
							saveItem.hasCnt = true;
							break;
						default:
							break;
					}
				} // foreach

				saveItem.hasCnt = true;
				isfail = false;
			}

			log.Debug(string.Format("<{0}> get sttus.. seq={1}", isfail ? "Fail" : "Success", saveItem.seq));

		} // if (saveItem.seq.Length > 0)
		catch (Exception e)
		{
			log.Error(string.Format("SetPdAcctoSttusData ERROR : {0} / seq:{1}", e.Message, errorSeq));
		}

	} // foreach (AdListModel saveItem in saveItems)

	return fulldata.Where(p => p.hasCnt == true).Count();
	//return returnlist;
}

```

#### SetPdAcctoSttusData 월별 입사자/퇴사자 정보 가져오기

```

/// <summary>
/// 기간별 현황 정보조회 데이터를 담는다
/// </summary>
/// <param name="detailData"></param>
/// <param name="servicekey"></param>
/// <returns></returns>
static int SetPdAcctoSttusData(string servicekey)
{
	if (fulldata.Count < 1)
	{
		log.Debug("SetPdAcctoSttusData Debug : no Detail Data..");
		return 0;
	}

	foreach (WkplModel saveItem in fulldata)
	{
		string errorSeq = "";
		bool isfail = true;
		try
		{
			if (saveItem.seq.Length < 1)
				continue;

			XmlDocument detailXml = CallApiData('S', servicekey, saveItem.seq, "");

			// api호출시 에러가 나면 그대로 작업을 멈춘다
			var errorNode = detailXml.GetElementsByTagName("errMsg");
			if (errorNode != null && errorNode.Count > 0)
			{
				log.Debug(detailXml.InnerXml);
				if (detailXml.InnerText.Contains("LIMITED_NUMBER_OF_SERVICE_REQUESTS_EXCEEDS_ERROR"))
				{
					isGetSttusError = true;
				}
				//return 0;
				continue;
			}

			errorSeq = saveItem.seq;
			XmlNodeList detailXmlDataList = detailXml.GetElementsByTagName("item");
			XmlNode detailRootNode = detailXmlDataList[0];
			if (detailRootNode != null && detailRootNode.ChildNodes.Count > 0)
			{
				XmlNodeList detailAdNodes = detailRootNode.ChildNodes;
				foreach (XmlNode item in detailAdNodes)
				{
					switch (item.Name)
					{
						case "lssJnngpCnt":
							saveItem.lssJnngpCnt = item.InnerText.Length < 1 || item.InnerText == "" ? 0 : int.Parse(item.InnerText);
							saveItem.hasCnt = true;
							break;
						case "nwAcqzrCnt":
							saveItem.nwAcqzrCnt = item.InnerText.Length < 1 || item.InnerText == "" ? 0 : int.Parse(item.InnerText);
							saveItem.hasCnt = true;
							break;
						default:
							break;
					}
				} // foreach

				saveItem.hasCnt = true;
				isfail = false;
			}

			log.Debug(string.Format("<{0}> get sttus.. seq={1}", isfail ? "Fail" : "Success", saveItem.seq));

		} // if (saveItem.seq.Length > 0)
		catch (Exception e)
		{
			log.Error(string.Format("SetPdAcctoSttusData ERROR : {0} / seq:{1}", e.Message, errorSeq));
		}

	} // foreach (AdListModel saveItem in saveItems)

	return fulldata.Where(p => p.hasCnt == true).Count();
	//return returnlist;
}

```
