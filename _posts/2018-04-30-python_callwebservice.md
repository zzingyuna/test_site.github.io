---
layout: post
---
# 파이썬에서 웹서비스 호출(.asmx)


``<url주소1 : http://gw0.job.co.kr/DeadLinkSolutionGW.asmx?WSDL>
<url주소2 : http://gw0.job.co.kr/DeadLinkSolutionGW.asmx>
``<토큰키값 : egYELBe4oLcb0sZxUEjhw==>


````no-highlight

from suds.client import Client
from suds.xsd.doctor import Import, ImportDoctor
import logging
import base64

url = 'url주소1'
imp = Import('http://www.w3.org/2001/XMLSchema')    # the schema to import.
imp.filter.add('url주소2')  # the schema to import into.
d = ImportDoctor(imp)
client = Client(url, doctor=d)

tokenKey = "토큰키값"

res = client.service.GetLoadPattern(tokenKey)
dataset = res[1]
datatable = dataset[0]
i = 0

for dt in datatable[0]:
	deadLinkPattern = dt['Dead_Link_Pattern1'].encode('euc-kr')
	sourceSiteNm = dt['SourceSiteNm']
	print(sourceSiteNm)


```
