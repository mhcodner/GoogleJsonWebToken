## What does this do?
This is a C# class that I modified from [this blog post][1].
It is modified to use the `MachineKeySet` instead of the `UserKeySet`
which allows this class to run on Azure Websites.

To quote the original blog post, this is used
> To support scenarios where an unattended application accesses Google data,
Google introduced the concept of Service Accounts which allows for unattended
log in using [JWT][2] (JSON Web Token).

### Why the modification to `MachineKeySet`?
The detailed answer can be found on [this blog post][3] but the TL;DR is that
the user profile doesn't exist in Azure Websites.

## How do I use it?
First you need

1. A Google account
2. A Google Service Account with the appropriate API access for what you want
to do. This can be obtained from the [Google Developer console][4].
  * Create a Service Account from the `APIs & auth / Credentials` page. Take note
   of the email address in the form of `foo@developer.gserviceaccount.com`.
  * Download the p12 key for the Service Account. Preferably place it somewhere
   in your project that isn't publicly accessible such as `~/App_Data/`.

Then you can request an access token using
```
var auth = GoogleJsonWebToken.GetAccessToken(
    "foo@developer.gserviceaccount.com",
    Server.MapPath("~/App_Data/key.p12"),
    GoogleJsonWebToken.SCOPE_CALENDAR);
var token = auth["access_token"];

// Then you can start making authorised API calls
string response;
using (WebClient client = new WebClient())
{
    response = client.DownloadString(String.Format("https://www.googleapis.com/calendar/v3/calendars/{0}/events?access_token={1}&singleEvents=true", CalendarId, token));
}
```
 
 [1]: https://zavitax.wordpress.com/2012/12/17/logging-in-with-google-service-account-in-c-jwt/
 [2]: http://tools.ietf.org/html/draft-ietf-oauth-json-web-token-05
 [3]: http://blog.tylerdoerksen.com/2013/08/23/pfx-certificate-files-and-windows-azure-websites/
 [4]: https://console.developers.google.com/
