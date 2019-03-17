---
layout: post
---

# Selenium WebDriver를 이용한 크롬창 띄우기

#### 크롬창으로 google 로그인 후 photo가져오기

```
using System;
using System.Collections.Generic;
using System.Threading;
using OpenQA.Selenium;
using OpenQA.Selenium.Chrome;

namespace ConsoleApp1
{
	class Program
	{
		static void Main(string[] args)
		{
			using (IWebDriver driver = new ChromeDriver())
			{
				driver.Url = "https://accounts.google.com/o/oauth2/v2/auth?scope=https%3A%2F%2Fwww.googleapis.com%2Fauth%2Fphotoslibrary%20https%3A%2F%2Fwww.googleapis.com%2Fauth%2Fphotoslibrary.readonly&access_type=offline&include_granted_scopes=true&state=state_parameter_passthrough_value&redirect_uri=http%3A%2F%2Flocalhost%2Fstudy%2Ftest.html&response_type=code&client_id=307558157269-tfuep5e4ece7hj0lun4gog6lki2mpht2.apps.googleusercontent.com";

				IWebElement txtmail = driver.FindElement(By.Id("identifierId"));//driver.FindElement(By.Name("q"));
				txtmail.SendKeys("kimyuna9944@gmail.com");

				IWebElement btnNext = driver.FindElement(By.Id("identifierNext"));
				btnNext.Click();

				Thread.Sleep(1000);

				IWebElement txtpw = driver.FindElement(By.Name("password"));
				txtpw.SendKeys("비번");

				IWebElement btnLogin = driver.FindElement(By.Id("passwordNext"));
				btnLogin.Click();

				Thread.Sleep(6000);

				IWebElement getPhotos = driver.FindElement(By.Id("linkPhoto"));
				getPhotos.Click();

				Thread.Sleep(7000);

				ICollection<IWebElement> imgPhotos = driver.FindElements(By.ClassName("googlePhotos"));//driver.FindElement(By.Name("q"));
				Console.WriteLine(imgPhotos.Count);
				foreach (IWebElement item in imgPhotos)
				{
					Console.WriteLine(item.GetAttribute("src"));
				}
			}

		}
		
	}
}

```
아래 드라이버 Nuget 설치할것! (OpenQA.Selenium 사용을 위해 필요)  
![alt text](https://zzingyuna.github.io/image/Selenium_WebDriver.JPG)  


