---
title: Zarządzanie sesjami — narzędzie Microsoft Threat Modeling Tool — Azure | Dokumenty firmy Microsoft
description: zagrożeniach ujawnionych w narzędziu do modelowania zagrożeń
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 5d9dc1595e3cc812ba060d958b6e981867500ae2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73161514"
---
# <a name="security-frame-session-management"></a>Ramka zabezpieczeń: Zarządzanie sesjami
| Produkt/usługa | Artykuł |
| --------------- | ------- |
| **Azure AD**    | <ul><li>[Implementowanie prawidłowego wylogowania przy użyciu metod ADAL podczas korzystania z usługi Azure AD](#logout-adal)</li></ul> |
| Urządzenie IoT | <ul><li>[Użyj skończonego okresu istnienia dla wygenerowanych tokenów SaS](#finite-tokens)</li></ul> |
| **Baza danych dokumentów platformy Azure** | <ul><li>[Użyj minimalnych okresów istnienia tokenu dla wygenerowanych tokenów zasobów](#resource-tokens)</li></ul> |
| **ADFS** | <ul><li>[Implementowanie prawidłowego wylogowania przy użyciu metod WsFederation podczas korzystania z usługi ADFS](#wsfederation-logout)</li></ul> |
| **Serwer tożsamości** | <ul><li>[Implementowanie prawidłowego wylogowania podczas korzystania z serwera tożsamości](#proper-logout)</li></ul> |
| **Aplikacja internetowa** | <ul><li>[Aplikacje dostępne za pośrednictwem protokołu HTTPS muszą używać bezpiecznych plików cookie](#https-secure-cookies)</li><li>[Wszystkie aplikacje oparte na http powinny określać http tylko dla definicji plików cookie](#cookie-definition)</li><li>[Łagodzenie ataków między site request fagerować (CSRF) na ASP.NET stronach internetowych](#csrf-asp)</li><li>[Konfigurowanie sesji dla okresu istnienia braku aktywności](#inactivity-lifetime)</li><li>[Implementowanie prawidłowego wylogowania z aplikacji](#proper-app-logout)</li></ul> |
| **Interfejs API sieci Web** | <ul><li>[Łagodzenie ataków między witrynami (CSRF) na ASP.NET interfejsów API sieci Web](#csrf-api)</li></ul> |

## <a name="implement-proper-logout-using-adal-methods-when-using-azure-ad"></a><a id="logout-adal"></a>Implementowanie prawidłowego wylogowania przy użyciu metod ADAL podczas korzystania z usługi Azure AD

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure AD | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Jeśli aplikacja opiera się na tokenie dostępu wystawionym przez usługę Azure AD, należy wywołać program obsługi zdarzeń wylogowania |

### <a name="example"></a>Przykład
```csharp
HttpContext.GetOwinContext().Authentication.SignOut(OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType)
```

### <a name="example"></a>Przykład
Należy również zniszczyć sesji użytkownika, wywołując Session.Abandon() metody. Poniższa metoda pokazuje bezpieczną implementację wylogowania użytkownika:
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

## <a name="use-finite-lifetimes-for-generated-sas-tokens"></a><a id="finite-tokens"></a>Użyj skończonego okresu istnienia dla wygenerowanych tokenów SaS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Urządzenie IoT | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Tokeny SaS wygenerowane do uwierzytelniania w usłudze Azure IoT Hub powinny mieć ograniczony okres wygaśnięcia. Utrzymuj okresy istnienia tokenu SaS do minimum, aby ograniczyć czas, przez jaki mogą być odtwarzane w przypadku naruszenia zabezpieczeń tokenów.|

## <a name="use-minimum-token-lifetimes-for-generated-resource-tokens"></a><a id="resource-tokens"></a>Użyj minimalnych okresów istnienia tokenu dla wygenerowanych tokenów zasobów

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Baza danych dokumentów platformy Azure | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Zmniejsz czas tokenu zasobu do minimalnej wymaganej wartości. Tokeny zasobów mają domyślny prawidłowy czas 1 godziny.|

## <a name="implement-proper-logout-using-wsfederation-methods-when-using-adfs"></a><a id="wsfederation-logout"></a>Implementowanie prawidłowego wylogowania przy użyciu metod WsFederation podczas korzystania z usługi ADFS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | ADFS | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Jeśli aplikacja opiera się na tokenie STS wystawionym przez usługą ADFS, program obsługi zdarzeń wylogowania powinien wywołać metodę WSFederationAuthenticationModule.FederatedSignOut() w celu wylogowania użytkownika. Również bieżąca sesja powinna zostać zniszczona, a wartość tokenu sesji powinna zostać zresetowana i unieważniona.|

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

## <a name="implement-proper-logout-when-using-identity-server"></a><a id="proper-logout"></a>Implementowanie prawidłowego wylogowania podczas korzystania z serwera tożsamości

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Serwer tożsamości | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [IdentityServer3-Federated wylogować](https://identityserver.github.io/Documentation/docsv2/advanced/federated-signout.html) |
| **Kroki** | IdentityServer obsługuje możliwość federacji z zewnętrznymi dostawcami tożsamości. Gdy użytkownik wyloguje się z dostawcy tożsamości nadrzędnego, w zależności od używanego protokołu, może być możliwe odebranie powiadomienia, gdy użytkownik się wyloguje. Umożliwia IdentityServer powiadomić swoich klientów, dzięki czemu mogą również podpisać użytkownika. Szczegółowe informacje na temat implementacji można znaleźć w dokumentacji w sekcji odwołań.|

## <a name="applications-available-over-https-must-use-secure-cookies"></a><a id="https-secure-cookies"></a>Aplikacje dostępne za pośrednictwem protokołu HTTPS muszą używać bezpiecznych plików cookie

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | EnvironmentType - OnPrem |
| **Odwołania**              | [HttpCookies Element (ASP.NET Ustawienia Schemat)](https://msdn.microsoft.com/library/ms228262(v=vs.100).aspx), [HttpCookie.Secure Właściwość](https://msdn.microsoft.com/library/system.web.httpcookie.secure.aspx) |
| **Kroki** | Pliki cookie są zwykle dostępne tylko dla domeny, dla której zostały objęte zakresem. Niestety definicja "domeny" nie zawiera protokołu, więc pliki cookie, które są tworzone za pośrednictwem protokołu HTTPS, są dostępne za pośrednictwem protokołu HTTP. Atrybut "bezpieczne" wskazuje przeglądarce, że plik cookie powinien być udostępniany tylko za pośrednictwem protokołu HTTPS. Upewnij się, że wszystkie pliki cookie ustawione za pośrednictwem protokołu HTTPS używają **bezpiecznego** atrybutu. Wymaganie można wymusić w pliku web.config, ustawiając atrybut requireSSL na true. Jest to preferowane podejście, ponieważ wymusi **bezpieczny** atrybut dla wszystkich bieżących i przyszłych plików cookie bez konieczności wprowadzania dodatkowych zmian kodu.|

### <a name="example"></a>Przykład
```csharp
<configuration>
  <system.web>
    <httpCookies requireSSL="true"/>
  </system.web>
</configuration>
```
Ustawienie jest wymuszane, nawet jeśli protokół HTTP jest używany do uzyskiwania dostępu do aplikacji. Jeśli protokół HTTP jest używany do uzyskiwania dostępu do aplikacji, ustawienie przerywa aplikację, ponieważ pliki cookie są ustawione z atrybutem secure i przeglądarka nie wyśle ich z powrotem do aplikacji.

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Formularze internetowe, MVC5 |
| **Atrybuty**              | EnvironmentType - OnPrem |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Gdy aplikacja sieci web jest jednostką uzależnioną, a IdP jest serwerem usługi ADFS, atrybut bezpiecznego tokenu FedAuth można skonfigurować, ustawiając identyfikator TheSSL na True w `system.identityModel.services` sekcji web.config:|

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

## <a name="all-http-based-application-should-specify-http-only-for-cookie-definition"></a><a id="cookie-definition"></a>Wszystkie aplikacje oparte na http powinny określać http tylko dla definicji plików cookie

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Atrybut Bezpiecznego pliku cookie](https://en.wikipedia.org/wiki/HTTP_cookie#Secure_cookie) |
| **Kroki** | Aby zmniejszyć ryzyko ujawnienia informacji za pomocą ataku skryptów krzyżowych (XSS), nowy atrybut - httpOnly - został wprowadzony do plików cookie i jest obsługiwany przez wszystkie główne przeglądarki. Atrybut określa, że plik cookie nie jest dostępny za pośrednictwem skryptu. Korzystając z plików cookie HttpOnly, aplikacja internetowa zmniejsza możliwość kradzieży poufnych informacji zawartych w pliku cookie za pomocą skryptu i wysłania ich na stronę internetową osoby atakującej. |

### <a name="example"></a>Przykład
Wszystkie aplikacje oparte na http, które używają plików cookie, powinny określać httpOnly w definicji plików cookie, implementując następującą konfigurację w web.config:
```XML
<system.web>
.
.
   <httpCookies requireSSL="false" httpOnlyCookies="true"/>
.
.
</system.web>
```

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Formularze sieci Web |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [FormsAuthentication.RequireSSL Właściwość](https://msdn.microsoft.com/library/system.web.security.formsauthentication.requiressl.aspx) |
| **Kroki** | Wartość właściwości RequireSSL jest ustawiana w pliku konfiguracyjnym dla aplikacji ASP.NET przy użyciu atrybutu requireSSL elementu konfiguracji. W pliku Web.config można określić w pliku Web.config dla aplikacji ASP.NET, czy SSL (Secure Sockets Layer) jest wymagane do zwrócenia pliku cookie uwierzytelniania formularzy do serwera, ustawiając atrybut requireSSL.|

### <a name="example"></a>Przykład 
Poniższy przykład kodu ustawia atrybut requireSSL w pliku Web.config.
```XML
<authentication mode="Forms">
  <forms loginUrl="member_login.aspx" cookieless="UseCookies" requireSSL="true"/>
</authentication>
```

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | MVC5 |
| **Atrybuty**              | EnvironmentType - OnPrem |
| **Odwołania**              | [Konfiguracja programu Windows Identity Foundation (WIF) — część II](https://blogs.msdn.microsoft.com/alikl/2011/02/01/windows-identity-foundation-wif-configuration-part-ii-cookiehandler-chunkedcookiehandler-customcookiehandler/) |
| **Kroki** | Aby ustawić atrybut httpOnly dla plików cookie FedAuth, hideFromCsript wartość atrybutu powinna być ustawiona na True. |

### <a name="example"></a>Przykład
Następująca konfiguracja pokazuje prawidłową konfigurację:
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

## <a name="mitigate-against-cross-site-request-forgery-csrf-attacks-on-aspnet-web-pages"></a><a id="csrf-asp"></a>Łagodzenie ataków między site request fagerować (CSRF) na ASP.NET stronach internetowych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Fałszowanie żądań między lokacjami (CSRF lub XSRF) jest rodzajem ataku, w którym osoba atakująca może wykonywać akcje w kontekście zabezpieczeń ustalonej sesji innego użytkownika w witrynie sieci Web. Celem jest zmodyfikowanie lub usunięcie zawartości, jeśli docelowa strona internetowa opiera się wyłącznie na plikach cookie sesji w celu uwierzytelnienia odebranego żądania. Osoba atakująca może wykorzystać tę lukę, uzyskując przeglądarkę innego użytkownika, aby załadować adres URL z poleceniem z witryny, w której użytkownik jest już zalogowany. Osoba atakująca może to zrobić na wiele sposobów, na przykład hostując inną witrynę sieci web, która ładuje zasób z serwera podlegającego uschunku, lub nakłaniając użytkownika do kliknięcia łącza. Atak można zapobiec, jeśli serwer wysyła dodatkowy token do klienta, wymaga klienta do uwzględnienia tego tokenu we wszystkich przyszłych żądań i sprawdza, czy wszystkie przyszłe żądania zawierają token, który odnosi się do bieżącej sesji, takich jak przy użyciu ASP.NET AntiForgeryToken lub ViewState. |

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | MVC5, MVC6 |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Zapobieganie atakom XSRF/CSRF we wzorcach ASP.NET MVC i Web Pages](https://www.asp.net/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) |
| **Kroki** | Formularze ASP.NET I ASP.NET MVC — `AntiForgeryToken` użyj metody pomocnika w widokach; umieścić `Html.AntiForgeryToken()` w formularzu, na przykład,|

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
W tym samym czasie Html.AntiForgeryToken() daje odwiedzającym plik cookie o nazwie __RequestVerificationToken, o tej samej wartości co losowa ukryta wartość pokazana powyżej. Następnie, aby sprawdzić poprawność wpisu formularza przychodzącego, dodaj filtr [ValidateAntiForgeryToken] do metody akcji docelowej. Przykład:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Filtr autoryzacji, który sprawdza, czy:
* Żądanie przychodzące ma plik cookie o nazwie __RequestVerificationToken
* Żądanie przychodzące `Request.Form` ma wpis o nazwie __RequestVerificationToken
* Te pliki `Request.Form` cookie i wartości są zgodne Przy założeniu, że wszystko jest dobrze, żądanie przechodzi normalnie. Ale jeśli nie, to błąd autoryzacji z komunikatem "Wymagany token antypodaczacyjny nie został dostarczony lub był nieprawidłowy". 

### <a name="example"></a>Przykład
Anti-CSRF i AJAX: Token formularza może być problemem dla żądań AJAX, ponieważ żądanie AJAX może wysyłać dane JSON, a nie dane formularza HTML. Jednym z rozwiązań jest wysyłanie tokenów w niestandardowym nagłówku HTTP. Poniższy kod używa składni Razor do generowania tokenów, a następnie dodaje tokeny do żądania AJAX. 
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
Podczas przetwarzania żądania wyodrębnić tokeny z nagłówka żądania. Następnie wywołać AntiForgery.Validate metody, aby sprawdzić poprawność tokenów. Metoda Validate zgłasza wyjątek, jeśli tokeny nie są prawidłowe.
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

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Formularze sieci Web |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Skorzystaj z wbudowanych funkcji ASP.NET, aby odeprzeć ataki internetowe](https://msdn.microsoft.com/library/ms972969.aspx#securitybarriers_topic2) |
| **Kroki** | Ataki CSRF w aplikacjach opartych na webformie można złagodzić, ustawiając ViewStateUserKey na losowy ciąg, który różni się dla każdego użytkownika — identyfikator użytkownika lub, jeszcze lepiej, identyfikator sesji. Z wielu powodów technicznych i społecznych identyfikator sesji jest znacznie lepiej dopasowany, ponieważ identyfikator sesji jest nieprzewidywalny, limit czasu i zmienia się w zależności od użytkownika.|

### <a name="example"></a>Przykład
Oto kod, który musisz mieć na wszystkich stronach:
```csharp
void Page_Init (object sender, EventArgs e) {
   ViewStateUserKey = Session.SessionID;
   :
}
```

## <a name="set-up-session-for-inactivity-lifetime"></a><a id="inactivity-lifetime"></a>Konfigurowanie sesji dla okresu istnienia braku aktywności

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Właściwość HttpSessionState.Timeout](https://msdn.microsoft.com/library/system.web.sessionstate.httpsessionstate.timeout(v=vs.110).aspx) |
| **Kroki** | Limit czasu sesji reprezentuje zdarzenie występujące, gdy użytkownik nie wykonuje żadnej akcji w witrynie sieci web w interwale (zdefiniowanym przez serwer sieci web). Zdarzenie po stronie serwera zmienia stan sesji użytkownika na "nieprawidłowy" (na przykład "nie jest już używany") i instruuje serwer sieci web, aby go zniszczył (usuwając wszystkie zawarte w nim dane). Poniższy przykład kodu ustawia atrybut sesji limitu czasu na 15 minut w pliku Web.config.|

### <a name="example"></a>Przykład
```XML 
<configuration>
  <system.web>
    <sessionState mode="InProc" cookieless="true" timeout="15" />
  </system.web>
</configuration>
```

## <a name="enable-threat-detection-on-azure-sql"></a><a id="threat-detection"></a>Włącz wykrywanie zagrożeń w usłudze Azure SQL

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Formularze sieci Web |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Element Formularzy do uwierzytelniania (ASP.NET schemat ustawień)](https://msdn.microsoft.com/library/1d3t3c61(v=vs.100).aspx) |
| **Kroki** | Ustawianie limitu czasu na plik cookie biletu uwierzytelniania formularzy na 15 minut|

### <a name="example"></a>Przykład
```XML
<forms  name=".ASPXAUTH" loginUrl="login.aspx"  defaultUrl="default.aspx" protection="All" timeout="15" path="/" requireSSL="true" slidingExpiration="true"/>
</forms>
```

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Formularze internetowe, MVC5 |
| **Atrybuty**              | EnvironmentType - OnPrem |
| **Odwołania**              | [asdeqa](https://skf.azurewebsites.net/Mitigations/Details/wefr) |
| **Kroki** | Gdy aplikacja internetowa jest jednostką uzależnioną, a usługa ADFS jest usługą STS, okres istnienia plików cookie uwierzytelniania — tokeny FedAuth — można ustawić przez następującą konfigurację w witrynie web.config:|

### <a name="example"></a>Przykład
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
Również okres istnienia tokenu oświadczenia SAML wystawiony przez usługę ADFS powinien być ustawiony na 15 minut, wykonując następujące polecenie programu PowerShell na serwerze usługi ADFS:
```csharp
Set-ADFSRelyingPartyTrust -TargetName "<RelyingPartyWebApp>" -ClaimsProviderName @("Active Directory") -TokenLifetime 15 -AlwaysRequireAuthentication $true
```

## <a name="implement-proper-logout-from-the-application"></a><a id="proper-app-logout"></a>Implementowanie prawidłowego wylogowania z aplikacji

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Wykonaj odpowiednie wylogowanie z aplikacji, gdy użytkownik naciśnie przycisk wylogowania. Po wylogowaniu aplikacja powinna zniszczyć sesję użytkownika, a także zresetować i zniweczyć wartość pliku cookie sesji, a także zresetować i zniwelować wartość pliku cookie uwierzytelniania. Ponadto gdy wiele sesji są powiązane z tożsamością jednego użytkownika, muszą być wspólnie zakończone po stronie serwera w przesunięciu limitu czasu lub wylogowania. Nareszcie upewnij się, że funkcja wylogowywki jest dostępna na każdej stronie. |

## <a name="mitigate-against-cross-site-request-forgery-csrf-attacks-on-aspnet-web-apis"></a><a id="csrf-api"></a>Łagodzenie ataków między witrynami (CSRF) na ASP.NET interfejsów API sieci Web

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Fałszowanie żądań między lokacjami (CSRF lub XSRF) jest rodzajem ataku, w którym osoba atakująca może wykonywać akcje w kontekście zabezpieczeń ustalonej sesji innego użytkownika w witrynie sieci Web. Celem jest zmodyfikowanie lub usunięcie zawartości, jeśli docelowa strona internetowa opiera się wyłącznie na plikach cookie sesji w celu uwierzytelnienia odebranego żądania. Osoba atakująca może wykorzystać tę lukę, uzyskując przeglądarkę innego użytkownika, aby załadować adres URL z poleceniem z witryny, w której użytkownik jest już zalogowany. Osoba atakująca może to zrobić na wiele sposobów, na przykład hostując inną witrynę sieci web, która ładuje zasób z serwera podlegającego uschunku, lub nakłaniając użytkownika do kliknięcia łącza. Atak można zapobiec, jeśli serwer wysyła dodatkowy token do klienta, wymaga klienta do uwzględnienia tego tokenu we wszystkich przyszłych żądań i sprawdza, czy wszystkie przyszłe żądania zawierają token, który odnosi się do bieżącej sesji, takich jak przy użyciu ASP.NET AntiForgeryToken lub ViewState. |

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | MVC5, MVC6 |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Zapobieganie atakom fałszerskości żądań między witrynami (CSRF) w ASP.NET interfejsie API sieci Web](https://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks) |
| **Kroki** | Anti-CSRF i AJAX: Token formularza może być problemem dla żądań AJAX, ponieważ żądanie AJAX może wysyłać dane JSON, a nie dane formularza HTML. Jednym z rozwiązań jest wysyłanie tokenów w niestandardowym nagłówku HTTP. Poniższy kod używa składni Razor do generowania tokenów, a następnie dodaje tokeny do żądania AJAX. |

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
Podczas przetwarzania żądania wyodrębnić tokeny z nagłówka żądania. Następnie wywołać AntiForgery.Validate metody, aby sprawdzić poprawność tokenów. Metoda Validate zgłasza wyjątek, jeśli tokeny nie są prawidłowe.
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
Formularze ASP.NET I ASP.NET MVC — użyj metody pomocnika AntiForgeryToken w widokach; html.AntiForgeryToken() w formularzu, na przykład,
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
}
```

### <a name="example"></a>Przykład
Powyższy przykład spowoduje wysuń coś następującego:
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Przykład
W tym samym czasie Html.AntiForgeryToken() daje odwiedzającym plik cookie o nazwie __RequestVerificationToken, o tej samej wartości co losowa ukryta wartość pokazana powyżej. Następnie, aby sprawdzić poprawność wpisu formularza przychodzącego, dodaj filtr [ValidateAntiForgeryToken] do metody akcji docelowej. Przykład:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Filtr autoryzacji, który sprawdza, czy:
* Żądanie przychodzące ma plik cookie o nazwie __RequestVerificationToken
* Żądanie przychodzące `Request.Form` ma wpis o nazwie __RequestVerificationToken
* Te pliki `Request.Form` cookie i wartości są zgodne Przy założeniu, że wszystko jest dobrze, żądanie przechodzi normalnie. Ale jeśli nie, to błąd autoryzacji z komunikatem "Wymagany token antypodaczacyjny nie został dostarczony lub był nieprawidłowy".

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | MVC5, MVC6 |
| **Atrybuty**              | Dostawca tożsamości — usługa ADFS, dostawca tożsamości — usługa Azure AD |
| **Odwołania**              | [Zabezpieczanie interfejsu API sieci Web za pomocą kont indywidualnych i logowania lokalnego w interfejsie API ASP.NET sieci Web 2.2](https://www.asp.net/web-api/overview/security/individual-accounts-in-web-api) |
| **Kroki** | Jeśli interfejs API sieci Web jest zabezpieczony przy użyciu OAuth 2.0, oczekuje tokenu na okaziciela w nagłówku żądania autoryzacji i udziela dostępu do żądania tylko wtedy, gdy token jest prawidłowy. W przeciwieństwie do uwierzytelniania opartego na plikach cookie przeglądarki nie dołączają tokenów nośnych do żądań. Klient żądający musi jawnie dołączyć token na okaziciela w nagłówku żądania. W związku z tym dla ASP.NET interfejsów API sieci Web chronionych przy użyciu OAuth 2.0 tokeny nośnika są uważane za ochronę przed atakami CSRF. Należy pamiętać, że jeśli część MVC aplikacji korzysta z uwierzytelniania formularzy (tj. używa plików cookie), tokeny zapobiegawczego fałszerstwa muszą być używane przez aplikację internetową MVC. |

### <a name="example"></a>Przykład
Internetowy interfejs API musi być informowany, aby polegać TYLKO na tokenach nośnika, a nie na plikach cookie. Można to zrobić za pomocą `WebApiConfig.Register` następującej konfiguracji w metodzie:

```csharp
config.SuppressDefaultHostAuthentication();
config.Filters.Add(new HostAuthenticationFilter(OAuthDefaults.AuthenticationType));
```

Metoda Pominiastego HostostAuthentication nakazuje interfejsowi API sieci Web zignorowanie uwierzytelniania, które ma miejsce, zanim żądanie dotrze do potoku interfejsu API sieci Web przez usługi IIS lub oprogramowanie pośredniczące OWIN. W ten sposób możemy ograniczyć interfejs API sieci Web do uwierzytelniania tylko przy użyciu tokenów nośnika.
