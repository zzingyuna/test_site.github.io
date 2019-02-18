---
layout: post
---

# 가위바위보 게임

자바스크립트로 만든 게임 - 가위바위보 게임

```
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <meta name="description" content="Free Web tutorials">
  <meta name="keywords" content="HTML,CSS,XML,JavaScript">
  <meta name="author" content="John Doe">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Page Title</title>
  <style>
body {
  background-color: aliceblue;
}

h1 {
  color: darkcyan;
  text-align: center;
}

p {
  font-family: verdana;
  font-size: 20px;
}

.butt1
{
 background: url(https://t1.daumcdn.net/cfile/tistory/13045F404D7C365D23) no-repeat 0px 0px #FF8800;
 width: 140px;
 height: 155px;
}
.butt2
{
 background: url(https://t1.daumcdn.net/cfile/tistory/13045F404D7C365D23) no-repeat -150px 0px #FF8800;
 width: 130px;
 height: 155px;
}
.butt3
{
 background: url(https://t1.daumcdn.net/cfile/tistory/13045F404D7C365D23) no-repeat -275px 0px #FF8800;
 width: 170px;
 height: 155px;
}

.opp
{
 transform: rotate(180deg);
}
  </style>
  <script src="https://code.jquery.com/jquery-1.10.2.js"></script>
</head>
<body>

<h1>가위 바위 보!!</h1>
<p>컴퓨터랑 가위바위보 게임하기</p>
<br />

<p>남은 시간 : <span id="time"></span></p>
<p>점수 : <span id="score"></span></p>

<button onclick="Start()">Start</button>
<br />
<br />

<div id="computer"></div>

<button class="butt1" onclick="EventClick(this);"></button>
<button class="butt2" onclick="EventClick(this);"></button>
<button class="butt3" onclick="EventClick(this);"></button>

<script>
	var score = 0; //점수
	var time = 30; //남은시간
	var buttNum = 1; //가위:2, 바위:1, 보:3
	var RockPaperScissorsTime = 200; //컴퓨터가 가위바위보 돌리는 시간(0.2초)
	var game; //컴퓨터가 가위바위보 돌리는 setInterval
	var finish; //남은시간 체크하는 setInterval

	// 게임시작
	function Start(){
		$("#score").text(score);
		$("#time").text(time);
	
		game = setInterval(RockPaperScissors, RockPaperScissorsTime);
		finish = setInterval(StartTimeCheck, 1000);
	}

	// 시간줄이기
	function StartTimeCheck(){
		if(time > 0){
			time = time-1; 
			$("#time").text(time);
		}else{
			clearInterval(finish);
			clearInterval(game);
			if(confirm("시간이 종료되었습니다.\n새 게임을 시작하시겠습니까?")){
				score = 0;
				time = 30;
				Start();
			}
		}
	}

	// 가위,바위,보 클릭시
	function EventClick(item){
		clearInterval(game);
		/*
		-- 컴퓨터가 가위바위보 낼때 랜덤으로 내게하기..려고 했으나 주석!
		$("#computer").removeClass($("#computer").attr('class'));
		var num = Math.round(Math.random()*2)+1;
		$("#computer").addClass("butt"+num+" opp");
		*/
		var computerNum = parseInt($("#computer").attr('class').replace("butt",""));
		var userNum = parseInt(item.className.replace("butt",""));
		sleep(1000);
		if((computerNum-userNum) == -2 || (computerNum-userNum) == 1){
			alert("이겼습니다.");
			score = score+1;
			$("#score").text(score);
		}
		else if((computerNum-userNum) == 0){
			alert("비겼습니다.");
		}
		else{
			alert("졌습니다.");
		}

		game = setInterval(RockPaperScissors, RockPaperScissorsTime);
	}

	// 컴퓨터 가위바위보 노출
	function RockPaperScissors(){
		// 가위바위보가 순서대로 노출되되도록 증가값 설정
		$("#computer").removeClass("butt"+buttNum+" opp");
		buttNum = (buttNum+1 > 3 ? 1 : buttNum+1);
		$("#computer").addClass("butt"+buttNum+" opp");
	}
	
	//[1/1000초]
	function sleep(num){
	 var now = new Date();
	   var stop = now.getTime() + num;
	   while(true){
		 now = new Date();
		 if(now.getTime() > stop)return;
	   }
	}

</script>
</body>
</html>

```

<font color="white"> 

USE [EVENT]
GO

--DROP TABLE EVENT_508_CODE_TB
--DROP TABLE EVENT_508_TB

-- =================================================================
--  이벤트 테이블 생성 (이벤트 번호 508)
-- =================================================================
-- EVENT_508_CODE_TB : 상품 정보, 공유 정보, 가위바위보 결과 정보 저장
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
SET ANSI_PADDING ON
GO
CREATE TABLE [dbo].[EVENT_508_CODE_TB]
(
	IDX			INT	IDENTITY(1, 1)	NOT NULL
,	CODE		VARCHAR(1)		NOT NULL
,	CODENO		VARCHAR(5)		NOT NULL
,	COIN		INT				NOT NULL
,	CONTENT		NVARCHAR(300)	NOT NULL
,	CONSTRAINT [PK_EVENT_508_CODE_TB] PRIMARY KEY CLUSTERED (IDX)
)
GO
SET ANSI_PADDING OFF
GO





-- EVENT_508_TB : 가위바위보 결과 저장, 코인 적립 및 차감 저장
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
SET ANSI_PADDING ON
GO
CREATE TABLE [dbo].[EVENT_508_TB]
(
	IDX		INT IDENTITY(1, 1)	NOT NULL
,	USERID	VARCHAR(30)			NOT NULL
,	CODE	VARCHAR(1)			NOT NULL
,	CODENO	VARCHAR(5)			NOT NULL
,	COIN	INT					NOT NULL
,	CONTENT	NVARCHAR(300)		NOT NULL
,	REGIP	VARCHAR(15)			NOT NULL
,	REGDT	DATETIME			NOT NULL	DEFAULT(GETDATE())
,	CONSTRAINT [PK_EVENT_508_TB] PRIMARY KEY CLUSTERED (IDX ASC)
)
GO
SET ANSI_PADDING OFF
GO
CREATE NONCLUSTERED INDEX [IX_EVENT_508_TB_USERID_CODE_REGDT] ON [dbo].[EVENT_508_TB]
(
	USERID ASC,
	CODE ASC,
	REGDT ASC
)
--INCLUDE ([SCORE], [APPPUSH_YN])
WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
GO




-- =================================================================
--   기본 데이터 입력
-- =================================================================\
-- EVENT_508_CODE_TB : 상품 정보, 공유 정보, 가위바위보 결과 정보 저장
-- TRUNCATE TABLE EVENT_508_CODE_TB
INSERT INTO EVENT_508_CODE_TB (CODE, CODENO, COIN, CONTENT)
VALUES
--가위바위보
('A', 'WIN' , 2, '승리 +2코인'),
('A', 'LOSE', -1, '실패 -1코인'),
('A', 'DRAW', 1, '비김 1코인'),

--상품응모
('P', '1', -30, '아이패드 프로 11형 2명'),
('P', '2', -20, '신세계 백화점 상품권 3만원권'),
('P', '3', -15, '영화예매권 1인 2매'),
('P', '4', -5, '이디야 ICE 카라멜 마키아또'),

-- 공유
('S', 'F', 0, 'FACEBOOK SHARE'),
('S', 'T', 0, 'TWITTER SHARE'),
('S', 'L', 0, 'LINK COPY')

USE [EVENT]
GO

SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
-- drop proc EVENT_508_TB_INS_PROC_20190228
/*******************************************************************************
' 단 위 업 무 명	: 508 가위바위보 이벤트 코인 적립 및 소진 처리
' 설          명	: EVENT_508_TB 테이블에 코인 적립, 소진, SNS공유 데이터 INSERT
					  가위바위보 승리하면 +2코인, 비기면 +1코인, 지면 -2코인
					  SNS 공유하면 로그 기록,
					  경품 응모하기 하면 코인 소진
					* @RETURNTYPE값 정의
					> 'SUCCESS:(WIN/LOSE/DRAW)'	: 성공 (받은 코인개수 : 가위바위보 결과)
					  'OVER'	: 중복요청
					  'LACK'	: 경품응모시 보유코인 부족
					  'ERR'		: DB에러
' 주  의  사  항	:
' 사  용  소  스	:
' 이		  력    :
	Ver		Date		Author				Description
	------	----------  -----------------	------------------------------------
	1.0		2019-02-28	김윤아				1.CREATE


' 예          제	:
EXEC EVENT_508_TB_INS_PROC_20190228 'test', 'A', '1', '사용자가 바위를 냄'	, '12.123.12.123'
EXEC EVENT_508_TB_INS_PROC_20190228 'test', 'A', '2', '사용자가 가위를 냄'	, '12.123.12.123'
EXEC EVENT_508_TB_INS_PROC_20190228 'test', 'A', '3', '사용자가 보를 냄'	, '12.123.12.123'
EXEC EVENT_508_TB_INS_PROC_20190228 'test', 'P', '2', '상품2 응모'		, '12.123.12.123'
EXEC EVENT_508_TB_INS_PROC_20190228 'test', 'S', 'F', '페이스북 공유'	, '12.123.12.123'

' 권          한	:
GRANT EXECUTE ON EVENT_508_TB_INS_PROC_20190228 TO EventUser
GRANT EXECUTE ON EVENT_508_TB_INS_PROC_20190228 TO albaman
*******************************************************************************/
CREATE PROC [dbo].[EVENT_508_TB_INS_PROC_20190228]
(
	@USERID		VARCHAR(30)
,	@CODE		VARCHAR(1)
,	@CODENO		VARCHAR(5)
,	@CONTENT	NVARCHAR(300)
,	@REGIP		VARCHAR(15)
)
AS
SET NOCOUNT ON
SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED
SET XACT_ABORT ON
BEGIN

	DECLARE @RETURNTYPE NVARCHAR(30)	= ''	-- 결과 내용
	DECLARE @RETURNCOIN INT				= 0		-- 결과 코인
	DECLARE @RESULTGAME NVARCHAR(10)	= ''	-- 가위바위보 결과


	IF @CODE = 'A'
	BEGIN

		-- 가위바위보 게임 횟수 체크
		DECLARE @REMAINDCNT INT	= 0

		SELECT @REMAINDCNT = COUNT(*)
		FROM EVENT_508_TB
		WHERE USERID = @USERID AND CODE = 'A' AND REGDT BETWEEN CAST(GETDATE() AS date) AND CAST(GETDATE()+1 AS date)

		IF (10-@REMAINDCNT) < 1
		BEGIN
			SET @RETURNTYPE = 'OVER'
		END
		ELSE
		BEGIN

			-- 두번연속 지면 또 지지 않도록 다시 게임진행
			DECLARE @GOCONTINUE INT = 1
			WHILE (@GOCONTINUE > 0)
			BEGIN
				-- 가위바위보 승리여부 체크
				-- 1:바위(묵), 2:가위(찌), 3:보(빠)
				DECLARE @COMPUTER INT
				SELECT @COMPUTER = CAST(3 * RAND()+1 AS INT)


				IF @COMPUTER = @CODENO
				BEGIN
					SET @RESULTGAME = 'DRAW'
					--PRINT '비김'
				END
				ELSE IF (@CODENO = 1 AND @COMPUTER = 3)
				BEGIN
					SET @RESULTGAME = 'LOSE'
					--PRINT '짐'
				END
				ELSE IF (@CODENO = 3 AND @COMPUTER = 1)
				BEGIN
					SET @RESULTGAME = 'WIN'
					--PRINT '이김'
				END
				ELSE IF (@CODENO < @COMPUTER)
				BEGIN
					SET @RESULTGAME = 'WIN'
					--PRINT '이김'
				END
				ELSE
				BEGIN
					SET @RESULTGAME = 'LOSE'
					--PRINT '짐'
				END


				-- 두번연속 졌는지 체크
				DECLARE @TEMPTB TABLE (COIN INT)
				DECLARE @ISTWICELOSE INT = 0
				INSERT @TEMPTB
				SELECT TOP 2 COIN
				FROM EVENT_508_TB WHERE USERID = @USERID AND CODE = 'A'
				ORDER BY IDX DESC

				SELECT @ISTWICELOSE = SUM(COIN)
				FROM @TEMPTB


				IF (@ISTWICELOSE > -2) OR @RESULTGAME != 'LOSE'
				BEGIN
					SET @GOCONTINUE = 0
					SELECT @RETURNCOIN = COIN
						 , @CONTENT = CONTENT
							+ ' /사용자:'
							+ CASE @CODENO WHEN 1 THEN '바위'
										   WHEN 2 THEN '가위'
										   WHEN 3 THEN '보' END
							+' /컴퓨터:'
							+ CASE @COMPUTER WHEN 1 THEN '바위'
											 WHEN 2 THEN '가위'
											 WHEN 3 THEN '보' END
					FROM EVENT_508_CODE_TB
					WHERE CODE = @CODE AND CODENO = @RESULTGAME
				END

			END
		END

	END
	ELSE IF @CODE = 'P'
	BEGIN

		-- 사용가능 코인 체크
		SELECT @RETURNCOIN = COIN
			 , @CONTENT = CONTENT
		FROM EVENT_508_CODE_TB
		WHERE CODE = @CODE AND CODENO = @CODENO

		DECLARE @MYCOIN INT = 0
		SELECT @MYCOIN = ISNULL(SUM(COIN),0)
		FROM EVENT_508_TB
		WHERE USERID = @USERID

		IF (@MYCOIN + @RETURNCOIN) < 0
		BEGIN
			SET @RETURNTYPE = 'LACK'
			SET @RETURNCOIN = (@MYCOIN + @RETURNCOIN)
		END

	END


	IF @RETURNTYPE = ''
	BEGIN
		-- 데이터 입력 처리
		BEGIN TRY
			BEGIN TRAN


				INSERT INTO EVENT_508_TB (USERID, CODE, CODENO, COIN, CONTENT, REGIP, REGDT)
				VALUES (@USERID, @CODE, @CODENO, @RETURNCOIN, @CONTENT, @REGIP, GETDATE())

			COMMIT TRAN
			SET @RETURNTYPE = 'SUCCESS' + ':' + @RESULTGAME
		END TRY
		BEGIN CATCH
			ROLLBACK TRAN
			SET @RETURNTYPE = 'ERR'
		END CATCH
	END

	-- 결과 리턴
	SELECT @RETURNTYPE AS RETURNTYPE, @RETURNCOIN AS COIN

END

GO
USE [EVENT]
GO

SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
-- drop proc EVENT_508_TB_SEL_PROC_20190228
/*******************************************************************************
' 단 위 업 무 명	: 508 가위바위보 이벤트 로그인한 사용자 정보 리턴
' 설          명	: 로그인한 사용자의 남은 가위바위보 진행횟수, 남은 코인 등 정보 리턴
' 주  의  사  항	:
' 사  용  소  스	:
' 이		  력    :
	Ver		Date		Author				Description
	------	----------  -----------------	------------------------------------
	1.0		2019-02-28	김윤아				1.CREATE


' 예          제	:
EXEC EVENT_508_TB_SEL_PROC_20190228 't_kya7637'

' 권          한	:
GRANT EXECUTE ON EVENT_508_TB_SEL_PROC_20190228 TO EventUser
GRANT EXECUTE ON EVENT_508_TB_SEL_PROC_20190228 TO albaman
*******************************************************************************/
CREATE PROC [dbo].[EVENT_508_TB_SEL_PROC_20190228](
	@USERID VARCHAR(30)
)
AS
SET NOCOUNT ON
SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED
BEGIN

	-- 가위바위보 남은 횟수 표기 (1일 10회)
	DECLARE @REMAINCNT INT
	SELECT @REMAINCNT = COUNT(IDX)
	FROM EVENT_508_TB
	WHERE USERID = @USERID AND REGDT BETWEEN CAST(GETDATE() AS date) AND CAST(GETDATE()+1 AS date)

	-- 가위바위보 남은 횟수 표기 (1일 10회)
	DECLARE @REMAINCOIN INT
	SELECT @REMAINCOIN = SUM(COIN)
	FROM EVENT_508_TB
	WHERE USERID = @USERID

	-- 경품 응모내역 표기
	SELECT	@REMAINCNT	AS REMAINCNT	-- 가위바위보 남은 횟수
		,	@REMAINCOIN	AS REMAINCOIN	-- 보유 코인
		,	CODE						-- 상품 코드
		,	CODENO						-- 상품 코드 번호
		,	CONTENT						-- 상품 내용
		,	COIN						-- 사용 코인
		,	REGDT						-- 상품 응모 일자
	FROM EVENT_508_TB
	WHERE USERID = @USERID AND CODE = 'P'
	ORDER BY REGDT DESC

END

GO
USE [EVENT]
GO

SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
-- drop proc EVENT_508_TB_SEL_PRODUCTS_PROC_20190228
/*******************************************************************************
' 단 위 업 무 명	: 508 가위바위보 이벤트 상품별 응모한 사용자 COUNT
' 설          명	: 상품별 응모한 사용자 COUNT (ID별 중복 포함)
' 주  의  사  항	:
' 사  용  소  스	:
' 이		  력    :
	Ver		Date		Author				Description
	------	----------  -----------------	------------------------------------
	1.0		2019-02-28	김윤아				1.CREATE


' 예          제	:
EXEC EVENT_508_TB_SEL_PRODUCTS_PROC_20190228 't_kya7637'

' 권          한	:
GRANT EXECUTE ON EVENT_508_TB_SEL_PRODUCTS_PROC_20190228 TO EventUser
GRANT EXECUTE ON EVENT_508_TB_SEL_PRODUCTS_PROC_20190228 TO albaman
*******************************************************************************/
CREATE PROC [dbo].[EVENT_508_TB_SEL_PRODUCTS_PROC_20190228]
AS
SET NOCOUNT ON
SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED
BEGIN

	-- 경품 응모내역 표기
	SELECT	CODE						-- 상품 코드
		,	CODENO						-- 상품 코드 번호
		,	CONTENT						-- 상품 내용
		,	COUNT(IDX)	AS CNT			-- 상품 응모 COUNT
	FROM EVENT_508_TB
	WHERE CODE = 'P'
	GROUP BY CODE, CODENO, CONTENT

END

GO

</font>
