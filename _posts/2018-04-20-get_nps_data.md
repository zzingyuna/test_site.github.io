---
layout: post
---
국민연금 데이터 가져오기

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
	
	// 가져온 데이터를 DB에 맞게 변환해준다
	List<WkplSaveModel> saveitems = ConvertdataToSavedata(fulldata, regionStrcs);

	// 데이터 최종 DB저장
	SaveCompInfoData(saveitems);
	
}

```

### 법정동코드 값 가져오기 

<p>국민연금 api 데이터를 가져오는 부분은 법정동코드별로 루프 돌리면서 가져오거나, 사업장코드, 사업장명을 입력해서 가져오는 경우 3가지가 있다.</p>
<p>우리나라의 모든 사업장 정보(기업정보)를 가져오기 위해서 사업장 코드별로 호출하면 사업장코드 6자리 입력 경우의 수가 법정동 코드별 입력수보다 많아서 법정동코드별로 가져오는 방식을 선택했다.</p>
<p>법정동코드값은 [행정표준코드관리시스템](https://www.code.go.kr/stdcode/regCodeL.do)에서 법정동 코드자료를 다운받아 사용했다.</p>

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
