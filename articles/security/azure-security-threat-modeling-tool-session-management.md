---
title: Azure Management - narzędzie Microsoft Threat modelowania — sesji | Dokumentacja firmy Microsoft
description: środki zaradcze w przypadku zagrożeń widoczne w narzędziu modelowania zagrożeń
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 24bd0e8eff616920dba0eb5353f983444e3161cd
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2018
ms.locfileid: "28019963"
---
# <a name="security-frame-session-management--articles"></a>Ramka zabezpieczeń: Zarządzanie sesjami | Artykuły 
| Produktów i usług | Artykuł |
| --------------- | ------- |
| **Azure AD**    | <ul><li>[Implementuje prawidłowego wylogowania przy użyciu metod ADAL w przypadku korzystania z usługi Azure AD](#logout-adal)</li></ul> |
| Urządzenia IoT | <ul><li>[Użyj ograniczone okresy istnienia generowanych tokenach SaS](#finite-tokens)</li></ul> |
| **Dokumentów w usłudze Azure DB** | <ul><li>[Minimalny okres istnienia tokenu na użytek generowanych tokenach zasobów](#resource-tokens)</li></ul> |
| **ADFS** | <ul><li>[Implementuje prawidłowego wylogowania przy użyciu metod WsFederation, korzystając z usług AD FS](#wsfederation-logout)</li></ul> |
| **Tożsamości serwera** | <ul><li>[Implementuje prawidłowego wylogowania przy użyciu tożsamości serwera](#proper-logout)</li></ul> |
| **Aplikacja sieci Web** | <ul><li>[Aplikacje dostępne za pośrednictwem protokołu HTTPS muszą używać bezpiecznych plików cookie](#https-secure-cookies)</li><li>[Wszystkie aplikacji oparty na protokole http należy określić http tylko dla definicji pliku cookie](#cookie-definition)</li><li>[Ograniczenia przed atakami sfałszowaniem żądań Cross-Site (CSRF) na stronach sieci web ASP.NET](#csrf-asp)</li><li>[Konfigurowanie sesji dla okresu istnienia aktywności](#inactivity-lifetime)</li><li>[Implementuje prawidłowego wylogowania z aplikacji](#proper-app-logout)</li></ul> |
| **Interfejs API sieci Web** | <ul><li>[Ograniczenia przed atakami sfałszowaniem żądań Cross-Site (CSRF) na interfejsów API sieci Web ASP.NET](#csrf-api)</li></ul> |

## <a id="logout-adal"></a>Implementuje prawidłowego wylogowania przy użyciu metod ADAL w przypadku korzystania z usługi Azure AD

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure AD | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Jeśli aplikacja opiera się na token dostępu wystawiony przez usługę Azure AD, powinny wywoływać wylogowania programu obsługi zdarzeń |

### <a name="example"></a>Przykład
```csharp
HttpContext.GetOwinContext().Authentication.SignOut(OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType)
```

### <a name="example"></a>Przykład
Należy także zniszczyć sesji użytkownika, wywołując metodę Session.Abandon(). Następujące metody przedstawia bezpiecznej implementacji wylogowania użytkownika:
```csharp
    [HttpPost]
        [ValidateAntiForgeryToken]
        public void LogOff()
        {
            string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
            AuthenticationContext authContext = new AuthenticationContext(Authority + TenantId, new NaiveSessionCache(userObjectID));
            authContext.TokenCache.Clear();
            Session.Clear();
            Session.Abandon();
            Response.SetCookie(new HttpCookie("ASP.NET_SessionId", string.Empty));
            HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
        } 
```

## <a id="finite-tokens"></a>Użyj ograniczone okresy istnienia generowanych tokenach SaS

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Urządzenia IoT | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Tokeny sygnatury dostępu współdzielonego wygenerowane do uwierzytelniania w usłudze Azure IoT Hub powinny mieć okresu wygaśnięcia. Zachowaj okresy istnienia tokenu sygnatury dostępu współdzielonego do minimum, aby ograniczyć ilość czasu, który może być odtwarzany w przypadku naruszenia zabezpieczeń tokeny.|

## <a id="resource-tokens"></a>Minimalny okres istnienia tokenu na użytek generowanych tokenach zasobów

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dokumentów w usłudze Azure DB | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Zmniejsz zakres czasu tokenu zasobów minimalną wartość wymaganą. Tokeny zasobów ma prawidłowy obiekt timespan domyślnej 1 godz.|

## <a id="wsfederation-logout"></a>Implementuje prawidłowego wylogowania przy użyciu metod WsFederation, korzystając z usług AD FS

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | ADFS | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Jeśli aplikacja używa tokenu Zabezpieczającego wydane przez usługi AD FS, program obsługi zdarzeń wylogowania powinien wywoływać metodę WSFederationAuthenticationModule.FederatedSignOut() się wylogować użytkownika. Również zostać zniszczone w bieżącej sesji, a wartość tokenu sesji należy zresetować i zniweczone.|

### <a name="example"></a>Przykład
```csharp
        [HttpPost, ValidateAntiForgeryToken]
        [Authorization]
        public ActionResult SignOut(string redirectUrl)
        {
            if (!this.User.Identity.IsAuthenticated)
            {
                return this.View("LogOff", null);
            }

            // Removes the user profile.
            this.Session.Clear();
            this.Session.Abandon();
            HttpContext.Current.Response.Cookies.Add(new System.Web.HttpCookie("ASP.NET_SessionId", string.Empty)
                {
                    Expires = DateTime.Now.AddDays(-1D),
                    Secure = true,
                    HttpOnly = true
                });

            // Signs out at the specified security token service (STS) by using the WS-Federation protocol.
            Uri signOutUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Issuer);
            Uri replyUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Realm);
            if (!string.IsNullOrEmpty(redirectUrl))
            {
                replyUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Realm + redirectUrl);
            }
           //     Signs out of the current session and raises the appropriate events.
            var authModule = FederatedAuthentication.WSFederationAuthenticationModule;
            authModule.SignOut(false);
        //     Signs out at the specified security token service (STS) by using the WS-Federation
        //     protocol.            
            WSFederationAuthenticationModule.FederatedSignOut(signOutUrl, replyUrl);
            return new RedirectResult(redirectUrl);
        }
```

## <a id="proper-logout"></a>Implementuje prawidłowego wylogowania przy użyciu tożsamości serwera

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Tożsamości serwera | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Federacyjna IdentityServer3 wylogowaniu](https://identityserver.github.io/Documentation/docsv2/advanced/federated-signout.html) |
| **Kroki** | IdentityServer obsługuje możliwości utworzenia federacji z dostawców tożsamości zewnętrznych. Gdy użytkownik zaloguje się poza dostawcy tożsamości nadrzędnego, w zależności od używanego protokołu, może być możliwe otrzymywać powiadomienia po wylogowaniu się użytkownika. Umożliwia on IdentityServer do powiadamiania klientów, ich można również wylogowanie użytkownika. Zapoznaj się z dokumentacją w sekcji odwołań dla szczegóły implementacji.|

## <a id="https-secure-cookies"></a>Aplikacje dostępne za pośrednictwem protokołu HTTPS muszą używać bezpiecznych plików cookie

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | EnvironmentType - lokalnego programu |
| **Odwołania**              | [httpCookies — Element (schemat ustawień programu ASP.NET)](http://msdn.microsoft.com/library/ms228262(v=vs.100).aspx), [HttpCookie.Secure właściwości](http://msdn.microsoft.com/library/system.web.httpcookie.secure.aspx) |
| **Kroki** | Pliki cookie są zwykle dostępne jedynie do domeny, dla którego zostały zakres. Niestety definicji "domeny" nie zawierają protokołu, dlatego plików cookie, które są tworzone przy użyciu protokołu HTTPS są dostępne za pośrednictwem protokołu HTTP. W przeglądarce atrybutu "secure" wskazuje, czy plik cookie powinien tylko udostępniane za pośrednictwem protokołu HTTPS. Upewnij się, że wszystkie pliki cookie ustawione za pośrednictwem protokołu HTTPS, użyj **bezpiecznego** atrybutu. To wymaganie można wymusić w pliku web.config przez ustawienie atrybutu parametru requireSSL na wartość true. Jest to preferowane rozwiązanie, ponieważ wymuszają **bezpiecznego** atrybutu dla wszystkich plików cookie obecne i przyszłe bez konieczności zmiany dodatkowy kod.|

### <a name="example"></a>Przykład
```csharp
<configuration>
  <system.web>
    <httpCookies requireSSL="true"/>
  </system.web>
</configuration>
```
Ustawienia zaczynają obowiązywać nawet wtedy, gdy HTTP jest używany do uzyskania dostępu do aplikacji. Jeśli HTTP jest używany do uzyskania dostępu do aplikacji, ustawienia dzieli aplikacji, ponieważ pliki cookie są konfigurowane przy użyciu atrybutu secure i przeglądarka nie będzie wysyłać je do aplikacji.

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Web Forms, MVC5 |
| **Atrybuty**              | EnvironmentType - lokalnego programu |
| **Odwołania**              | ND  |
| **Kroki** | Gdy aplikacja sieci web jest uzależniona oraz rozszerzeniu IdP serwera usług AD FS, atrybut bezpiecznego tokenu FedAuth można skonfigurować przez ustawienie parametru requireSSL na wartość True w `system.identityModel.services` sekcji w pliku Web.config:|

### <a name="example"></a>Przykład
```csharp
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:20:0" />
    ....  
    </federationConfiguration>
  </system.identityModel.services>
```

## <a id="cookie-definition"></a>Wszystkie aplikacji oparty na protokole http należy określić http tylko dla definicji pliku cookie

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Atrybut bezpiecznego pliku Cookie](https://en.wikipedia.org/wiki/HTTP_cookie#Secure_cookie) |
| **Kroki** | Aby zmniejszyć ryzyko ujawnienia informacji z atak skryptów między witrynami (XSS), nowy atrybut — httpOnly - wprowadzono w celu pliki cookie i jest obsługiwana przez wszystkie główne przeglądarki. Ten atrybut określa, czy plik cookie nie jest dostępny za pośrednictwem skryptu. Za pomocą plików cookie HttpOnly, aplikacji sieci web zmniejsza prawdopodobieństwo poufne dane zawarte w pliku cookie może być kradzieży za pomocą skryptu i wysyłany do witryny internetowej osoby atakującej. |

### <a name="example"></a>Przykład
Wszystkie aplikacje oparte na protokole HTTP, które używają plików cookie należy określić HttpOnly w definicji pliku cookie, implementując następujących konfiguracji w pliku web.config:
```XML
<system.web>
.
.
   <httpCookies requireSSL="false" httpOnlyCookies="true"/>
.
.
</system.web>
```

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Formularze sieci Web |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Właściwość FormsAuthentication.RequireSSL](https://msdn.microsoft.com/library/system.web.security.formsauthentication.requiressl.aspx) |
| **Kroki** | Wartość parametru RequireSSL właściwości jest ustawiana w pliku konfiguracji dla aplikacji ASP.NET przy użyciu atrybutu parametru requireSSL elementu konfiguracji. Można określić w pliku Web.config dla aplikacji ASP.NET czy SSL (Secure Sockets Layer) jest wymagany do zwrócenia pliku cookie uwierzytelniania formularzy do serwera przez ustawienie atrybutu parametru requireSSL.|

### <a name="example"></a>Przykład 
Poniższy przykład kodu ustawia atrybut parametru requireSSL w pliku Web.config.
```XML
<authentication mode="Forms">
  <forms loginUrl="member_login.aspx" cookieless="UseCookies" requireSSL="true"/>
</authentication>
```

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | MVC5 |
| **Atrybuty**              | EnvironmentType - lokalnego programu |
| **Odwołania**              | [Windows Identity Foundation (WIF) Konfiguracja — część II](https://blogs.msdn.microsoft.com/alikl/2011/02/01/windows-identity-foundation-wif-configuration-part-ii-cookiehandler-chunkedcookiehandler-customcookiehandler/) |
| **Kroki** | Aby ustawić atrybut httpOnly plików cookie FedAuth, wartość atrybutu hideFromCsript powinien mieć ustawioną wartość True. |

### <a name="example"></a>Przykład
Następująca konfiguracja zawiera prawidłowej konfiguracji:
```XML
<federatedAuthentication>
<cookieHandler mode="Custom"
                       hideFromScript="true"
                       name="FedAuth"
                       path="/"
                       requireSsl="true"
                       persistentSessionLifetime="25">
</cookieHandler>
</federatedAuthentication>
```

## <a id="csrf-asp"></a>Ograniczenia przed atakami sfałszowaniem żądań Cross-Site (CSRF) na stronach sieci web ASP.NET

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Fałszowanie żądań między witrynami (CSRF lub XSRF) jest typem ataku, w którym atakujący może wykonywać akcje w kontekście zabezpieczeń ustanowienie sesji innego użytkownika w witrynie sieci web. Celem jest modyfikowania lub usuwania zawartości, jeśli docelowa witryna sieci web opiera się wyłącznie na pliki cookie z sesji do odebrane żądanie uwierzytelnienia. Osoba atakująca może wykorzystać tę lukę w zabezpieczeniach, pobierając przeglądarki innego użytkownika, aby załadować adresu URL za pomocą polecenia z lokacji narażony, na którym użytkownik jest już zalogowany. Istnieje wiele sposobów, atakujący może to zrobić, takich jak inna witryna sieci web, ładowaną zasobu na serwerze narażony, lub pobierania użytkownika, kliknięcie łącza. Ataków można zapobiec, jeśli serwer wysyła dodatkowe token do klienta, wymaga od klienta do dołączenia token wszystkich przyszłych żądań i sprawdza, czy wszystkich przyszłych żądań zawierać token, który odnoszą się do bieżącej sesji, takie jak przy użyciu platformy ASP.NET AntiForgeryToken lub ViewState. |

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | MVC5, MVC6 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Zapobieganie XSRF/CSRF w platformie ASP.NET MVC i stron sieci Web](http://www.asp.net/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) |
| **Kroki** | Użyj Anti-CSRF i formularzy platformy ASP.NET MVC — `AntiForgeryToken` metody pomocniczej w widokach; ujmij `Html.AntiForgeryToken()` w formularzu, na przykład|

### <a name="example"></a>Przykład
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
```

### <a name="example"></a>Przykład
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Przykład
W tym samym czasie Html.AntiForgeryToken() zapewnia obiekt odwiedzający pliku cookie o nazwie __RequestVerificationToken z taką samą wartość jak losowych wartości ukryte przedstawionych powyżej. Następnie można sprawdzić poprawności przychodzącego post formularza, Dodaj filtr [ValidateAntiForgeryToken] do docelowego metody akcji. Na przykład:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Filtr autoryzacji, która sprawdza, czy:
* Żądanie przychodzące ma pliku cookie o nazwie __RequestVerificationToken
* Żądanie przychodzące ma `Request.Form` wpisu o nazwie __RequestVerificationToken
* Te pliki cookie i `Request.Form` jest dobrze zakładając, że wszystkie wartości są zgodne, żądanie przechodzi przez normalnego. Ale jeśli nie, następnie wystąpił błąd autoryzacji z komunikatem "wymagany token zabezpieczający przed sfałszowaniem nie został podany lub jest nieprawidłowy". 

### <a name="example"></a>Przykład
Anti-CSRF i AJAX: tokenu formularza może to stanowić problem dla żądania AJAX, ponieważ żądanie AJAX może wysyłać dane JSON, a nie dane formularza HTML. Rozwiązanie polega na wysyłanie tokenów w niestandardowy nagłówek HTTP. Poniższy kod używa składni Razor do generowania tokenów, a następnie dodaje tokenów na żądanie AJAX. 
```csharp
<script>
    @functions{
        public string TokenHeaderValue()
        {
            string cookieToken, formToken;
            AntiForgery.GetTokens(null, out cookieToken, out formToken);
            return cookieToken + ":" + formToken;                
        }
    }

    $.ajax("api/values", {
        type: "post",
        contentType: "application/json",
        data: {  }, // JSON data goes here
        dataType: "json",
        headers: {
            'RequestVerificationToken': '@TokenHeaderValue()'
        }
    });
</script>
```

### <a name="example"></a>Przykład
Podczas przetwarzania żądania, Wyodrębnij tokenów w nagłówku żądania. Następnie wywołaj metodę AntiForgery.Validate do weryfikacji tokenów. Metoda weryfikacji zgłasza wyjątek, jeśli tokenów nie są prawidłowe.
```csharp
void ValidateRequestHeader(HttpRequestMessage request)
{
    string cookieToken = "";
    string formToken = "";

    IEnumerable<string> tokenHeaders;
    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
    {
        string[] tokens = tokenHeaders.First().Split(':');
        if (tokens.Length == 2)
        {
            cookieToken = tokens[0].Trim();
            formToken = tokens[1].Trim();
        }
    }
    AntiForgery.Validate(cookieToken, formToken);
}
```

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Formularze sieci Web |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Korzystać z funkcji wbudowanych ASP.NET w celu zapobieżenia poza atakami z sieci Web](https://msdn.microsoft.com/library/ms972969.aspx#securitybarriers_topic2) |
| **Kroki** | Ataki CSRF w aplikacjach na podstawie formularza sieci Web można zminimalizować przez ustawienie ViewStateUserKey losowy ciąg, który jest różny dla każdego użytkownika — identyfikator użytkownika lub jeszcze lepiej identyfikator sesji. Istnieje wiele możliwych przyczyn technicznych i społecznościowych identyfikator sesji jest znacznie lepszym rozwiązaniem, ponieważ identyfikator jest nieoczekiwane, sesji upłynie limit czasu i zmienia się na poszczególnych użytkowników.|

### <a name="example"></a>Przykład
Oto kod, który musi być w wszystkie strony:
```csharp
void Page_Init (object sender, EventArgs e) {
   ViewStateUserKey = Session.SessionID;
   :
}
```

## <a id="inactivity-lifetime"></a>Konfigurowanie sesji dla okresu istnienia aktywności

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [HttpSessionState.Timeout Property](https://msdn.microsoft.com/library/system.web.sessionstate.httpsessionstate.timeout(v=vs.110).aspx) |
| **Kroki** | Limit czasu sesji reprezentuje zdarzenie występuje, gdy użytkownik nie wykonywania dowolnych akcji w witrynie sieci web dla interwału (zdefiniowane przez serwer sieci web). Zdarzenia po stronie serwera, Zmień stan sesji użytkownika do "nieprawidłowe" (na przykład "nie już używać") i serwer sieci web do zniszczenia (usunięcie wszystkich danych znajdujących się w niej). Poniższy przykład kodu ustawia atrybut limitu czasu sesji do 15 minut, w pliku Web.config.|

### <a name="example"></a>Przykład
"" Kod XML <configuration> < system.web > <sessionState mode="InProc" cookieless="true" timeout="15" /> < /system.web ></configuration>
```

## <a id="threat-detection"></a>Enable Threat detection on Azure SQL
```

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Formularze sieci Web |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Tworzy Element uwierzytelniania (schemat ustawień programu ASP.NET)](https://msdn.microsoft.com/library/1d3t3c61(v=vs.100).aspx) |
| **Kroki** | Ustaw limit czasu plików cookie biletu uwierzytelniania formularzy do 15 minut|

### <a name="example"></a>Przykład
"" Kod XML<forms  name=".ASPXAUTH" loginUrl="login.aspx"  defaultUrl="default.aspx" protection="All" timeout="15" path="/" requireSSL="true" slidingExpiration="true"/>
</forms>
```

| Title                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Web Application | 
| **SDL Phase**               | Build |  
| **Applicable Technologies** | Web Forms, MVC5 |
| **Attributes**              | EnvironmentType - OnPrem |
| **References**              | [asdeqa](https://skf.azurewebsites.net/Mitigations/Details/wefr) |
| **Steps** | When the web application is Relying Party and ADFS is the STS, the lifetime of the authentication cookies - FedAuth tokens - can be set by the following configuration in web.config:|

### Example
```XML
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:15:0" />
      <!-- Set requireHttps=true; -->
      <wsFederation passiveRedirectEnabled="true" issuer="http://localhost:39529/" realm="https://localhost:44302/" reply="https://localhost:44302/" requireHttps="true"/>
      <!--
      Use the code below to enable encryption-decryption of claims received from ADFS. Thumbprint value varies based on the certificate being used.
      <serviceCertificate>
        <certificateReference findValue="4FBBBA33A1D11A9022A5BF3492FF83320007686A" storeLocation="LocalMachine" storeName="My" x509FindType="FindByThumbprint" />
      </serviceCertificate>
      -->
    </federationConfiguration>
  </system.identityModel.services>
```

### <a name="example"></a>Przykład
Również usług AD FS wystawiony SAML oświadczeń okres istnienia tokenu powinien być ustawiony na 15 minut, wykonując następujące polecenia programu powershell na serwerze usług AD FS:
```csharp
Set-ADFSRelyingPartyTrust -TargetName “<RelyingPartyWebApp>” -ClaimsProviderName @(“Active Directory”) -TokenLifetime 15 -AlwaysRequireAuthentication $true
```

## <a id="proper-app-logout"></a>Implementuje prawidłowego wylogowania z aplikacji

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Wykonaj odpowiednie Wyloguj z aplikacji, gdy użytkownik naciśnie Wyloguj się przycisk. Podczas wylogowania aplikacji należy zniszczyć sesji użytkownika, resetowanie oraz zniesienia wartość pliku cookie sesji, oraz resetowanie i rozpoczynającą wartość pliku cookie uwierzytelniania. Ponadto jeśli wiele sesji są powiązane z tożsamością pojedynczego użytkownika, one musi być zbiorczo zakończona po stronie serwera na limitu czasu lub Wyloguj. Ponadto upewnij się, że wylogowania funkcje są dostępne na każdej stronie. |

## <a id="csrf-api"></a>Ograniczenia przed atakami sfałszowaniem żądań Cross-Site (CSRF) na interfejsów API sieci Web ASP.NET

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Fałszowanie żądań między witrynami (CSRF lub XSRF) jest typem ataku, w którym atakujący może wykonywać akcje w kontekście zabezpieczeń ustanowienie sesji innego użytkownika w witrynie sieci web. Celem jest modyfikowania lub usuwania zawartości, jeśli docelowa witryna sieci web opiera się wyłącznie na pliki cookie z sesji do odebrane żądanie uwierzytelnienia. Osoba atakująca może wykorzystać tę lukę w zabezpieczeniach, pobierając przeglądarki innego użytkownika, aby załadować adresu URL za pomocą polecenia z lokacji narażony, na którym użytkownik jest już zalogowany. Istnieje wiele sposobów, atakujący może to zrobić, takich jak inna witryna sieci web, ładowaną zasobu na serwerze narażony, lub pobierania użytkownika, kliknięcie łącza. Ataków można zapobiec, jeśli serwer wysyła dodatkowe token do klienta, wymaga od klienta do dołączenia token wszystkich przyszłych żądań i sprawdza, czy wszystkich przyszłych żądań zawierać token, który odnoszą się do bieżącej sesji, takie jak przy użyciu platformy ASP.NET AntiForgeryToken lub ViewState. |

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | MVC5, MVC6 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Zapobieganie Cross-Site (CSRF) Fałszerstwie żądania w składniku ASP.NET Web API](http://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks) |
| **Kroki** | Anti-CSRF i AJAX: tokenu formularza może to stanowić problem dla żądania AJAX, ponieważ żądanie AJAX może wysyłać dane JSON, a nie dane formularza HTML. Rozwiązanie polega na wysyłanie tokenów w niestandardowy nagłówek HTTP. Poniższy kod używa składni Razor do generowania tokenów, a następnie dodaje tokenów na żądanie AJAX. |

### <a name="example"></a>Przykład
```Javascript
<script>
    @functions{
        public string TokenHeaderValue()
        {
            string cookieToken, formToken;
            AntiForgery.GetTokens(null, out cookieToken, out formToken);
            return cookieToken + ":" + formToken;                
        }
    }
    $.ajax("api/values", {
        type: "post",
        contentType: "application/json",
        data: {  }, // JSON data goes here
        dataType: "json",
        headers: {
            'RequestVerificationToken': '@TokenHeaderValue()'
        }
    });
</script>
```

### <a name="example"></a>Przykład
Podczas przetwarzania żądania, Wyodrębnij tokenów w nagłówku żądania. Następnie wywołaj metodę AntiForgery.Validate do weryfikacji tokenów. Metoda weryfikacji zgłasza wyjątek, jeśli tokenów nie są prawidłowe.
```csharp
void ValidateRequestHeader(HttpRequestMessage request)
{
    string cookieToken = "";
    string formToken = "";

    IEnumerable<string> tokenHeaders;
    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
    {
        string[] tokens = tokenHeaders.First().Split(':');
        if (tokens.Length == 2)
        {
            cookieToken = tokens[0].Trim();
            formToken = tokens[1].Trim();
        }
    }
    AntiForgery.Validate(cookieToken, formToken);
}
```

### <a name="example"></a>Przykład
Anti-CSRF i formularzy platformy ASP.NET MVC — użyj metody pomocnika AntiForgeryToken widoków; na przykład umieścić Html.AntiForgeryToken() w formularzu
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
}
```

### <a name="example"></a>Przykład
W powyższym przykładzie dane wyjściowe obejmują postać zbliżoną do następującej:
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Przykład
W tym samym czasie Html.AntiForgeryToken() zapewnia obiekt odwiedzający pliku cookie o nazwie __RequestVerificationToken z taką samą wartość jak losowych wartości ukryte przedstawionych powyżej. Następnie można sprawdzić poprawności przychodzącego post formularza, Dodaj filtr [ValidateAntiForgeryToken] do docelowego metody akcji. Na przykład:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Filtr autoryzacji, która sprawdza, czy:
* Żądanie przychodzące ma pliku cookie o nazwie __RequestVerificationToken
* Żądanie przychodzące ma `Request.Form` wpisu o nazwie __RequestVerificationToken
* Te pliki cookie i `Request.Form` jest dobrze zakładając, że wszystkie wartości są zgodne, żądanie przechodzi przez normalnego. Ale jeśli nie, następnie wystąpił błąd autoryzacji z komunikatem "wymagany token zabezpieczający przed sfałszowaniem nie został podany lub jest nieprawidłowy".

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | MVC5, MVC6 |
| **Atrybuty**              | Dostawca tożsamości dostawca — usługi AD FS, tożsamość — usługi Azure AD |
| **Odwołania**              | [Zabezpieczanie interfejsu API sieci Web z indywidualnych kont i logowania lokalnego w składniku ASP.NET Web API 2.2](http://www.asp.net/web-api/overview/security/individual-accounts-in-web-api) |
| **Kroki** | Jeśli interfejs API sieci Web jest zabezpieczone przy użyciu protokołu OAuth 2.0, następnie je oczekuje tokenu elementu nośnego w nagłówku żądania autoryzacji i nieograniczony dostęp do żądania tylko wtedy, gdy token jest prawidłowy. W przeciwieństwie do uwierzytelniania opartego na plikach cookie przeglądarki nie dołączyć do żądania tokenów elementu nośnego. Klienta należy Dołącz w sposób jawny tokenu elementu nośnego w nagłówku żądania. W związku z tym dla programu ASP.NET interfejsów API sieci Web chronionych przy użyciu protokołu OAuth 2.0, tokenów elementu nośnego są traktowane jako ochrona przed atakami CSRF. Należy pamiętać, że jeśli część aplikacji MVC korzysta z uwierzytelniania formularzy (np. pliki cookie używane), tokenów zabezpieczających przed sfałszowaniem musi być używany przez aplikację sieci web MVC. |

### <a name="example"></a>Przykład
Interfejs API sieci Web musi mieć świadomość, aby polegać wyłącznie na tokenów elementu nośnego i pliki cookie nie w. Mogą to robić przez następującą konfigurację w `WebApiConfig.Register` — metoda: ' ' config kodu C Sharp. SuppressDefaultHostAuthentication(); Konfiguracja. Filters.Add (nowe HostAuthenticationFilter(OAuthDefaults.AuthenticationType));
```
The SuppressDefaultHostAuthentication method tells Web API to ignore any authentication that happens before the request reaches the Web API pipeline, either by IIS or by OWIN middleware. That way, we can restrict Web API to authenticate only using bearer tokens.