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


### 소스 시작..
## Main(string[] args)
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

	log.Debug("법정동코드 정보 가져오기");
	List<AddrCode> regionStrcs = GetRegionStructure();

	log.Debug("법정동코드를 기준으로 모든 데이터를 조회해서 가져온다");
	GetRgstList(servicekey, pageSize, regionStrcs);
	CreateRgstListFile(fulldata, regionStrcs, "list");

	log.Debug("이미 저장된 데이터는 제외처리");
	MakeRgstList();
	CreateRgstListFile(fulldata, regionStrcs, "clean_list");

	log.Debug("가져온 리스트 데이터에 상세내용 을 넣어준다");
	int detailCnt = SetDetailData(servicekey);

	log.Debug("가져온 리스트 데이터에 현황정보를 넣어준다");
	int sttusCnt = SetPdAcctoSttusData(servicekey);
	CreateRgstListFile(fulldata, regionStrcs, "sttus");

	log.Debug("가져온 데이터를 DB에 맞게 변환해준다");
	List<WkplSaveModel> saveitems = ConvertdataToSavedata(fulldata, regionStrcs);

	log.Debug("DB insert Start..");
	SaveCompInfoData(saveitems);

	log.Debug("Finish..!!");
}

```

