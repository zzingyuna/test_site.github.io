---
layout: post
---

# 랜덤 당첨 (classic ASP)

```
<!DOCTYPE html> 
<html lang="en" xmlns:fb="ogp.me/ns/fb#">
<head> 
 <meta http-equiv="Content-Type" content="text/html; charset=utf-8"> 
</head>
<%
'-------------------------------------------------
'	랜덤 당첨 테스트
'-------------------------------------------------
Dim people
people = request.QueryString("people")
if people = "" then
 people = 1000
end if 

Randomize
Dim ranNum 
ranNum = Rnd

Dim l_p1name, l_p1ratio, l_p1count
Dim l_p2name, l_p2ratio, l_p2count
Dim l_p3name, l_p3ratio, l_p3count
Dim l_p4name, l_p4ratio, l_p4count
Dim l_p5name, l_p5ratio, l_p5count
Dim i

response.write FormatDateTime(Now(),4) & "/" & Replace(FormatDateTime(Now(),4), ":","") & "/" & people
response.write "<br />"

'response.write 23-CInt(hour(now())) & 58-CInt(Minute(now()))
response.write "<br />"

Dim timeNum
'timeNum = CInt(replace(FormatDateTime(Now(),4), ":",""))
timeNum = 2359
'timeNum = CInt(replace(FormatDateTime(Now(),4), ":",""))

l_p1name = "여행상품권"
l_p1ratio = CDbl(1/timeNum)
l_p1count = 1

l_p2name = "애플워치"
l_p2ratio = CDbl(3/timeNum)
l_p2count = 3

l_p3name = "닌텐도"
l_p3ratio = CDbl(15/timeNum)
l_p3count = 15

l_p4name = "모스버거 쿠폰"
l_p4ratio = CDbl(20/timeNum)
l_p4count = 20

l_p5name = "올영쿠폰"
l_p5ratio = CDbl(50/timeNum)
l_p5count = 50

response.write timeNum
%>
<table border="1">
 <tr>
  <td>datetime</td>
  <td>user</td>
  <td>random num</td>
  <td>result</td>
 </tr>
<%
for i = 0 To CInt(people)

	if 0 < ranNum and ranNum <= CDbl(l_p1ratio) then
	 if l_p1count > 0 then
	%>
 <tr>
  <td><%=timeNum%></td>
  <td>사용자<%=i%></td>
  <td><%=ranNum%></td>
  <td>1등 당첨 : <%=l_p1name%></td>
  <%
	  l_p1count = l_p1count - 1
	 end if 
	elseif ranNum <= CDbl(l_p2ratio) then
	 if l_p2count > 0 then
	  %>
 <tr>
  <td><%=timeNum%></td>
  <td>사용자<%=i%></td>
  <td><%=ranNum%></td>
  <td>2등 당첨 : <%=l_p2name%></td>
  <%
	  l_p2count = l_p2count - 1
	 end if 
	elseif ranNum <= CDbl(l_p3ratio) then
	 if l_p3count > 0 then
	  %>
 <tr>
  <td><%=timeNum%></td>
  <td>사용자<%=i%></td>
  <td><%=ranNum%></td>
  <td>3등 당첨 : <%=l_p3name%></td>
  <%
	  l_p3count = l_p3count - 1
	 end if 
	elseif ranNum <= CDbl(l_p4ratio) then
	 if l_p4count > 0 then
	  %>
 <tr>
  <td><%=timeNum%></td>
  <td>사용자<%=i%></td>
  <td><%=ranNum%></td>
  <td>4등 당첨 : <%=l_p4name%></td>
  <%
	  l_p4count = l_p4count - 1
	 end if 
	elseif ranNum <= CDbl(l_p5ratio) then
	 if l_p5count > 0 then
	  %>
 <tr>
  <td><%=timeNum%></td>
  <td>사용자<%=i%></td>
  <td><%=ranNum%></td>
  <td>5등 당첨 : <%=l_p5name%></td>
  <%
	  l_p5count = l_p5count - 1
	 end if 
	end if
	%></td>
 </tr><%
 ranNum = Rnd
next 
%>
</table>


<%
'----------------------------------------------------------------------- 2번째
'timeNum = 1259
timeNum = 23-CInt(hour(now())) & 59-CInt(Minute(now()))
response.write timeNum
l_p1ratio = CDbl(1/timeNum)
l_p2ratio = CDbl(3/timeNum)
l_p3ratio = CDbl(15/timeNum)
l_p4ratio = CDbl(20/timeNum)
l_p5ratio = CDbl(50/timeNum)

%>
<table border="1">
 <tr>
  <td>datetime</td>
  <td>user</td>
  <td>random num</td>
  <td>result</td>
 </tr>
<%
for i = 0 To CInt(people)

	if 0 < ranNum and ranNum <= CDbl(l_p1ratio) then
	 if l_p1count > 0 then
	%>
 <tr>
  <td><%=timeNum%></td>
  <td>사용자<%=i%></td>
  <td><%=ranNum%></td>
  <td>1등 당첨 : <%=l_p1name%></td>
  <%
	  l_p1count = l_p1count - 1
	 end if 
	elseif ranNum <= CDbl(l_p2ratio) then
	 if l_p2count > 0 then
	  %>
 <tr>
  <td><%=timeNum%></td>
  <td>사용자<%=i%></td>
  <td><%=ranNum%></td>
  <td>2등 당첨 : <%=l_p2name%></td>
  <%
	  l_p2count = l_p2count - 1
	 end if 
	elseif ranNum <= CDbl(l_p3ratio) then
	 if l_p3count > 0 then
	  %>
 <tr>
  <td><%=timeNum%></td>
  <td>사용자<%=i%></td>
  <td><%=ranNum%></td>
  <td>3등 당첨 : <%=l_p3name%></td>
  <%
	  l_p3count = l_p3count - 1
	 end if 
	elseif ranNum <= CDbl(l_p4ratio) then
	 if l_p4count > 0 then
	  %>
 <tr>
  <td><%=timeNum%></td>
  <td>사용자<%=i%></td>
  <td><%=ranNum%></td>
  <td>4등 당첨 : <%=l_p4name%></td>
  <%
	  l_p4count = l_p4count - 1
	 end if 
	elseif ranNum <= CDbl(l_p5ratio) then
	 if l_p5count > 0 then
	  %>
 <tr>
  <td><%=timeNum%></td>
  <td>사용자<%=i%></td>
  <td><%=ranNum%></td>
  <td>5등 당첨 : <%=l_p5name%></td>
  <%
	  l_p5count = l_p5count - 1
	 end if 
	end if
	%></td>
 </tr><%
 ranNum = Rnd
next 
%>
</table>


<%
'----------------------------------------------------------------------- 3번째 3시간 59분 남았을때!!
timeNum = 59
response.write timeNum

l_p1ratio = CDbl(1/timeNum)
l_p2ratio = CDbl(3/timeNum)
l_p3ratio = CDbl(15/timeNum)
l_p4ratio = CDbl(20/timeNum)
l_p5ratio = CDbl(50/timeNum)

%>
<table border="1">
 <tr>
  <td>datetime</td>
  <td>user</td>
  <td>random num</td>
  <td>result</td>
 </tr>
<%
for i = 0 To CInt(people)

	if 0 < ranNum and ranNum <= CDbl(l_p1ratio) then
	 if l_p1count > 0 then
	%>
 <tr>
  <td><%=timeNum%></td>
  <td>사용자<%=i%></td>
  <td><%=ranNum%></td>
  <td>1등 당첨 : <%=l_p1name%></td>
  <%
	  l_p1count = l_p1count - 1
	 end if 
	elseif ranNum <= CDbl(l_p2ratio) then
	 if l_p2count > 0 then
	  %>
 <tr>
  <td><%=timeNum%></td>
  <td>사용자<%=i%></td>
  <td><%=ranNum%></td>
  <td>2등 당첨 : <%=l_p2name%></td>
  <%
	  l_p2count = l_p2count - 1
	 end if 
	elseif ranNum <= CDbl(l_p3ratio) then
	 if l_p3count > 0 then
	  %>
 <tr>
  <td><%=timeNum%></td>
  <td>사용자<%=i%></td>
  <td><%=ranNum%></td>
  <td>3등 당첨 : <%=l_p3name%></td>
  <%
	  l_p3count = l_p3count - 1
	 end if 
	elseif ranNum <= CDbl(l_p4ratio) then
	 if l_p4count > 0 then
	  %>
 <tr>
  <td><%=timeNum%></td>
  <td>사용자<%=i%></td>
  <td><%=ranNum%></td>
  <td>4등 당첨 : <%=l_p4name%></td>
  <%
	  l_p4count = l_p4count - 1
	 end if 
	elseif ranNum <= CDbl(l_p5ratio) then
	 if l_p5count > 0 then
	  %>
 <tr>
  <td><%=timeNum%></td>
  <td>사용자<%=i%></td>
  <td><%=ranNum%></td>
  <td>5등 당첨 : <%=l_p5name%></td>
  <%
	  l_p5count = l_p5count - 1
	 end if 
	end if
	%></td>
 </tr><%
 ranNum = Rnd
next 
%>
</table>

<br />
남은 상품 <br />
- 1등 : <%=l_p1count%><br />
- 2등 : <%=l_p2count%><br />
- 3등 : <%=l_p3count%><br />
- 4등 : <%=l_p4count%><br />
- 5등 : <%=l_p5count%><br /><br /><br />


```

