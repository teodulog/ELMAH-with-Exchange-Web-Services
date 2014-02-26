ELMAH-with-Exchange-Web-Services
================================

You can find the NuGet package here:

http://www.nuget.org/packages/elmah.ews/


The current implementation of ELMAH (v1.2 SP2) can only send email through SMTP, which is not natively supported in Azure. (Probably because it would turn into a huge SPAM engine).

Since ELMAH is included in the project as an HTTP Module I actually had to download & modify the source to handle Exchange Online Web Services to get email working in our Azure Cloud Service. The code is very clean so I only had to modify ErrorMailModule.cs. I added in the appropriate web.config input parameters and then created a new Send method inserting it like so (preserving backwards compatibility):

````C#
ReportError(Error error)
{
  ...

 OnMailing(args);
 if (UseExchangeWebServices)  //pulled from web.config
 {
     SendExchangeMail(mail);
 }
 else
 {
     SendMail(mail);
 }

....
}
````

````xml
//Sample web.config
<elmah>
<errorMail to="WebMaster@sample.com"
           subject="Sample - Elmah Exception"
           async="true"
           exchangeUserName="Admin@sample.onmicrosoft.com"
           exchangePassword="supersecretpassword"
           autoDiscoverUrl="Admin@sample.onmicrosoft.com"
           useExchangeWebServices="true"
           exchangeVersion="Exchange2010_SP2"/>
</elmah>
````

The web.config Exchange Version works off of the existing Microsoft.Exchange.WebServices.Data.ExchangeVersion enum (the string equivalent is required):

````C#
namespace Microsoft.Exchange.WebServices.Data
{
    public enum ExchangeVersion
    {
        Exchange2007_SP1 = 0,
        Exchange2010 = 1,
        Exchange2010_SP1 = 2,
        Exchange2010_SP2 = 3,
        Exchange2013 = 4,
    }
}
````
