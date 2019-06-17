---
title: Sesja zarządzania — narzędzie do modelowania zagrożeń firmy Microsoft — Azure | Dokumentacja firmy Microsoft
description: środki zaradcze dotyczące zagrożeń, widoczne w narzędziu do modelowania zagrożeń
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: e8f3cf3889b3f79e930630ff0e768a0c4875eec6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60611687"
---
# <a name="security-frame-session-management"></a>Ramka zabezpieczeń: Zarządzanie sesjami
| Produkt/usługę | Artykuł |
| --------------- | ------- |
| **Azure AD**    | <ul><li>[Implementowanie odpowiednie wylogowania przy użyciu metod biblioteki ADAL, korzystając z usługi Azure AD](#logout-adal)</li></ul> |
| Urządzenia IoT | <ul><li>[Użyj skończoną okresy istnienia tokenów sygnatur dostępu współdzielonego wygenerowanym](#finite-tokens)</li></ul> |
| **Azure Document DB** | <ul><li>[Użyj minimalnej okresów istnienia tokenu dla generowanych tokenach zasobów](#resource-tokens)</li></ul> |
| **ADFS** | <ul><li>[Implementowanie odpowiednie wylogowania przy użyciu metod WsFederation, korzystając z usług AD FS](#wsfederation-logout)</li></ul> |
| **Tożsamość serwera** | <ul><li>[Implementowanie odpowiednie wylogowania, podczas korzystania z tożsamości serwera](#proper-logout)</li></ul> |
| **Aplikacja sieci Web** | <ul><li>[Aplikacje dostępne za pośrednictwem protokołu HTTPS, należy użyć bezpiecznych plików cookie](#https-secure-cookies)</li><li>[Należy określić http tylko w przypadku definicji plików cookie w wszystkich aplikacji oparty na protokole http](#cookie-definition)</li><li>[Łagodzi skutki ataków fałszerstwo żądania Międzywitrynowego Międzywitrynowych na stronach sieci web platformy ASP.NET](#csrf-asp)</li><li>[Konfigurowanie sesji dla okresu istnienia braku aktywności](#inactivity-lifetime)</li><li>[Implementowanie odpowiednie wylogowania z aplikacji](#proper-app-logout)</li></ul> |
| **Interfejs API sieci Web** | <ul><li>[Łagodzi skutki ataków fałszerstwo żądania Międzywitrynowego Międzywitrynowych na interfejsy API sieci Web platformy ASP.NET](#csrf-api)</li></ul> |

## <a id="logout-adal"></a>Implementowanie odpowiednie wylogowania przy użyciu metod biblioteki ADAL, korzystając z usługi Azure AD

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure AD | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Jeśli aplikacja zależy od token dostępu wystawiony przez usługę Azure AD, powinien wywoływać wylogowania programu obsługi zdarzeń |

### <a name="example"></a>Przykład
```csharp
HttpContext.GetOwinContext().Authentication.SignOut(OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType)
```

### <a name="example"></a>Przykład
Należy również zniszczyć sesji użytkownika, wywołując metodę Session.Abandon(). Następującej metody pokazuje bezpiecznej implementacji wylogowania użytkownika:
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

## <a id="finite-tokens"></a>Użyj skończoną okresy istnienia tokenów sygnatur dostępu współdzielonego wygenerowanym

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Urządzenia IoT | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Tokeny sygnatur dostępu współdzielonego, generowany dla uwierzytelniania w usłudze Azure IoT Hub powinny mieć okresu wygaśnięcia. Zachowaj okresów istnienia tokenu sygnatury dostępu współdzielonego do minimum, aby ograniczyć ilość czasu, który może być powtórzone, w przypadku naruszenia zabezpieczeń tokenów.|

## <a id="resource-tokens"></a>Użyj minimalnej okresów istnienia tokenu dla generowanych tokenach zasobów

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Document DB | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Zmniejsz przedział czasu, tokenów zasobów na minimalną wartość wymaganą. Tokeny zasobów mają prawidłowy przedział czasu domyślnej równej 1 godz.|

## <a id="wsfederation-logout"></a>Implementowanie odpowiednie wylogowania przy użyciu metod WsFederation, korzystając z usług AD FS

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | ADFS | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Jeśli aplikacja zależy od usługi STS token wystawiony przez usługi AD FS, program obsługi zdarzeń wylogowania powinna wywołać metodę WSFederationAuthenticationModule.FederatedSignOut() wylogować użytkownika. Również należy zniszczyć bieżącej sesji, a wartość tokenu sesji należy zresetować i zniesione.|

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

## <a id="proper-logout"></a>Implementowanie odpowiednie wylogowania, podczas korzystania z tożsamości serwera

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Tożsamość serwera | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Federacyjna IdentityServer3 Wyloguj się](https://identityserver.github.io/Documentation/docsv2/advanced/federated-signout.html) |
| **Kroki** | IdentityServer obsługuje możliwość federowania z zewnętrzny dostawca tożsamości. Gdy użytkownik wyloguje się z dostawcą tożsamości nadrzędnego, w zależności od używanego protokołu, może być możliwe, aby otrzymać powiadomienie po wylogowaniu się użytkownika. Umożliwia ona IdentityServer do powiadamiania klientów, dzięki czemu użytkownik one również się wylogować. Zapoznaj się z dokumentacją w sekcji odwołań dla szczegółów implementacji.|

## <a id="https-secure-cookies"></a>Aplikacje dostępne za pośrednictwem protokołu HTTPS, należy użyć bezpiecznych plików cookie

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | EnvironmentType — lokalnego |
| **Odwołania**              | [httpCookies — Element (ASP.NET Settings Schema)](https://msdn.microsoft.com/library/ms228262(v=vs.100).aspx), [HttpCookie.Secure właściwości](https://msdn.microsoft.com/library/system.web.httpcookie.secure.aspx) |
| **Kroki** | Pliki cookie są zwykle dostępne jedynie do domeny, dla którego zostały zakresu. Niestety definicji "domena" nie zawierać nazwę protokołu, więc pliki cookie, które są tworzone przy użyciu protokołu HTTPS są dostępne za pośrednictwem protokołu HTTP. Do przeglądarki "bezpieczne" atrybut wskazuje, czy plik cookie należy dokonywać tylko dostępne za pośrednictwem protokołu HTTPS. Upewnij się, że wszystkie pliki cookie za pośrednictwem protokołu HTTPS, użyj **bezpiecznego** atrybutu. Przez ustawienie atrybutu wartość parametru requireSSL na wartość true w pliku web.config można wymusić wymagania. Jest preferowana, ponieważ wymuszają **bezpiecznego** atrybutu dla wszystkich bieżących i przyszłych plików cookie bez konieczności zmiany dodatkowego kodu.|

### <a name="example"></a>Przykład
```csharp
<configuration>
  <system.web>
    <httpCookies requireSSL="true"/>
  </system.web>
</configuration>
```
To ustawienie jest wymuszana, nawet jeśli HTTP jest używany do uzyskania dostępu do aplikacji. Jeśli HTTP jest używany do uzyskania dostępu do aplikacji, to ustawienie dzieli aplikacji, ponieważ pliki cookie są konfigurowane przy użyciu atrybutu secure i przeglądarki nie będzie wysyłać je do aplikacji.

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Web Forms, MVC5 |
| **Atrybuty**              | EnvironmentType — lokalnego |
| **Odwołania**              | ND  |
| **Kroki** | Aplikacja sieci web jest uzależniona, gdy dostawca tożsamości jest serwerem usług AD FS, można skonfigurować tokenu FedAuth bezpieczny atrybut, ustawiając wartość parametru requireSSL na wartość True w `system.identityModel.services` sekcja pliku Web.config:|

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

## <a id="cookie-definition"></a>Należy określić http tylko w przypadku definicji plików cookie w wszystkich aplikacji oparty na protokole http

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Atrybut bezpiecznego pliku Cookie](https://en.wikipedia.org/wiki/HTTP_cookie#Secure_cookie) |
| **Kroki** | Aby zmniejszyć ryzyko ujawnienia informacji o ataku skryptów między witrynami (XSS), nowy atrybut — httpOnly — została wprowadzona do plików cookie i jest obsługiwana przez wszystkie popularne przeglądarki. Ten atrybut określa, czy plik cookie nie jest dostępny za pośrednictwem skryptu. Za pomocą plików cookie HttpOnly, aplikacji sieci web ogranicza możliwość, że poufne dane zawarte w pliku cookie mogą skradziony za pomocą skryptów i wysyłany do witryny internetowej osoby atakującej. |

### <a name="example"></a>Przykład
Wszystkie aplikacje oparte na protokole HTTP, które używają plików cookie, należy określić HttpOnly w definicji plików cookie przez zaimplementowanie następujących konfiguracji w pliku web.config:
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
| **Odpowiednich technologii** | Formularze sieci Web |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Właściwość FormsAuthentication.RequireSSL](https://msdn.microsoft.com/library/system.web.security.formsauthentication.requiressl.aspx) |
| **Kroki** | Wartość właściwości wartość parametru RequireSSL jest ustawiana w pliku konfiguracji dla aplikacji ASP.NET przy użyciu atrybutu wartość parametru requireSSL elementu konfiguracji. Można określić w pliku Web.config dla aplikacji ASP.NET czy protokół SSL (Secure Sockets Layer) jest wymagane do zwrócenia pliku cookie uwierzytelniania formularzy z serwerem, ustawiając atrybut wartość parametru requireSSL.|

### <a name="example"></a>Przykład 
Poniższy przykład kodu ustawia atrybut wartość parametru requireSSL w pliku Web.config.
```XML
<authentication mode="Forms">
  <forms loginUrl="member_login.aspx" cookieless="UseCookies" requireSSL="true"/>
</authentication>
```

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | MVC5 |
| **Atrybuty**              | EnvironmentType — lokalnego |
| **Odwołania**              | [Konfiguracja programu Windows Identity Foundation (WIF) — część II](https://blogs.msdn.microsoft.com/alikl/2011/02/01/windows-identity-foundation-wif-configuration-part-ii-cookiehandler-chunkedcookiehandler-customcookiehandler/) |
| **Kroki** | Aby ustawić atrybut httpOnly plików cookie FedAuth, wartość atrybutu hideFromCsript powinna być równa wartości True. |

### <a name="example"></a>Przykład
Następująca konfiguracja przedstawiono prawidłowej konfiguracji:
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

## <a id="csrf-asp"></a>Łagodzi skutki ataków fałszerstwo żądania Międzywitrynowego Międzywitrynowych na stronach sieci web platformy ASP.NET

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Fałszowanie żądań między witrynami (CSRF lub XSRF) jest typem ataku, w którym osoba atakująca może wykonywać akcje w kontekście zabezpieczeń ustanowienie sesji innego użytkownika w witrynie sieci web. Celem jest można zmodyfikować lub usunąć zawartość, jeśli docelowa witryna sieci web, który opiera się wyłącznie na pliki cookie z sesji do odebrane żądanie uwierzytelnienia. Osoba atakująca może wykorzystać tę lukę w zabezpieczeniach przez innego użytkownika przeglądarki, aby załadować adresu URL za pomocą polecenia z lokacji narażony, na którym użytkownik jest już zalogowany. Istnieje wiele sposobów, osoba atakująca to zrobić, takie jak inna witryna sieci web, służącym do ładowania zasobu z serwer na ataki, lub pobrać użytkownika, kliknij łącze. Ataków można zapobiec, jeśli serwer wysyła do klienta, dodatkowy token, wymaga od klienta, które mają zostać objęte ten token wszystkich przyszłych żądań i sprawdza się, że wszystkie przyszłe żądania zawierać token, które odnoszą się do bieżącej sesji, takie jak za pomocą platformy ASP.NET AntiForgeryToken lub stanu widoku. |

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | MVC5, MVC6 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Zapobieganie XSRF/CSRF w ASP.NET MVC i Web Pages](https://www.asp.net/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) |
| **Kroki** | Anti-CSRF i formularzy platformy ASP.NET MVC — użyj `AntiForgeryToken` metody pomocnika dla widoków; put `Html.AntiForgeryToken()` do formularza, na przykład|

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
W tym samym czasie Html.AntiForgeryToken() zapewnia obiekt odwiedzający pliku cookie o nazwie __RequestVerificationToken z taką samą wartość jak losową wartość ukrytego przedstawionych powyżej. Następnie sprawdź poprawność przychodzącego post formularza, Dodaj filtr [ValidateAntiForgeryToken] do metody akcji docelowego. Na przykład:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Filtr autoryzacji, który sprawdza, czy:
* Żądanie przychodzące ma pliku cookie o nazwie __RequestVerificationToken
* Żądanie przychodzące ma `Request.Form` wpis o nazwie __RequestVerificationToken
* Te pliki cookie i `Request.Form` dopasowania wartości, zakładając, że wszystkie jest dobrze, żądanie przechodzi w zwykły sposób. Ale jeśli nie, następnie wystąpił błąd autoryzacji z komunikatem "wymagany token zabezpieczający przed sfałszowaniem nie został podany lub jest nieprawidłowy". 

### <a name="example"></a>Przykład
I AJAX CSRF chroniących: Tokenu formularza może być problemem w przypadku żądań AJAX, ponieważ żądanie AJAX może wysyłać dane JSON, a nie dane formularza HTML. Rozwiązanie polega na wysyłanie tokenów w niestandardowy nagłówek HTTP. Poniższy kod używa składni Razor do generowania tokenów, a następnie dodaje tokenów z żądaniem AJAX. 
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
Podczas przetwarzania żądania prowadzenie nagłówek żądania tokenów. Następnie wywołaj metodę AntiForgery.Validate do weryfikacji tokenów. Metoda weryfikacji zgłasza wyjątek, jeśli tokeny nie są prawidłowe.
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
| **Odpowiednich technologii** | Formularze sieci Web |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Korzystanie z funkcji wbudowanych w platformy ASP.NET w celu zapobieżenia Off atakami z sieci Web](https://msdn.microsoft.com/library/ms972969.aspx#securitybarriers_topic2) |
| **Kroki** | CSRF ataków w aplikacjach na podstawie formularza sieci Web może być ograniczona przez ustawienie ViewStateUserKey losowy ciąg, który jest różny dla każdego użytkownika — nazwa użytkownika lub jeszcze lepiej identyfikatora sesji. Wiele możliwych przyczyn technicznych i społecznościowych identyfikator sesji jest znacznie lepsze dopasowanie, ponieważ sesji, którego identyfikator jest nieprzewidywalne, upłynie limit czasu i zmienia się na poszczególnych użytkowników.|

### <a name="example"></a>Przykład
Poniżej przedstawiono kod, który musi być w wszystkie strony:
```csharp
void Page_Init (object sender, EventArgs e) {
   ViewStateUserKey = Session.SessionID;
   :
}
```

## <a id="inactivity-lifetime"></a>Konfigurowanie sesji dla okresu istnienia braku aktywności

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [HttpSessionState.Timeout Property](https://msdn.microsoft.com/library/system.web.sessionstate.httpsessionstate.timeout(v=vs.110).aspx) |
| **Kroki** | Limit czasu sesji reprezentuje zdarzenie występuje, gdy użytkownik nie wykonuje żadnych działań w witrynie sieci web interwale (zdefiniowanej przez serwer sieci web). Zdarzenia po stronie serwera, Zmień stan sesji użytkownika do "nieprawidłowy" (na przykład "nie już używać") i serwer sieci web, aby zniszczyć (usunięcie wszystkich danych znajdujących się w nim). Poniższy przykład kodu ustawia atrybut limitu czasu sesji do 15 minut w pliku Web.config.|

### <a name="example"></a>Przykład
```XML 
<configuration>
  <system.web>
    <sessionState mode="InProc" cookieless="true" timeout="15" />
  </system.web>
</configuration>
```

## <a id="threat-detection"></a>Włącz wykrywanie zagrożeń w usłudze Azure SQL

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Formularze sieci Web |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Element Form dla uwierzytelniania (ASP.NET Settings Schema)](https://msdn.microsoft.com/library/1d3t3c61(v=vs.100).aspx) |
| **Kroki** | Ustaw limit czasu plików cookie biletu uwierzytelniania formularzy do 15 minut|

### <a name="example"></a>Przykład
```XML
<forms  name=".ASPXAUTH" loginUrl="login.aspx"  defaultUrl="default.aspx" protection="All" timeout="15" path="/" requireSSL="true" slidingExpiration="true"/>
</forms>
```

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Web Forms, MVC5 |
| **Atrybuty**              | EnvironmentType — lokalnego |
| **Odwołania**              | [asdeqa](https://skf.azurewebsites.net/Mitigations/Details/wefr) |
| **Kroki** | Gdy aplikacja sieci web jest zaufania jednostek uzależnionych i usług AD FS jest usługi STS, okres istnienia plików cookie uwierzytelniania — tokeny FedAuth — można ustawić przez następującą konfigurację w pliku web.config:|

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
Również SAML wydane oświadczeń ADFS okres istnienia tokenu powinna być ustawiona na 15 minut, wykonując następujące polecenie programu powershell na serwerze usług AD FS:
```csharp
Set-ADFSRelyingPartyTrust -TargetName “<RelyingPartyWebApp>” -ClaimsProviderName @(“Active Directory”) -TokenLifetime 15 -AlwaysRequireAuthentication $true
```

## <a id="proper-app-logout"></a>Implementowanie odpowiednie wylogowania z aplikacji

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Wykonaj odpowiednie Wyloguj z aplikacji, naciśnięcie przez użytkownika po zalogowaniu się przycisk. Po wylogowania aplikacji należy zniszczyć sesji użytkownika, resetowanie oraz zniweczyć wartość pliku cookie sesji, wraz z resetowania i rozpoczynającą wartość pliku cookie uwierzytelniania. Ponadto gdy wiele sesji są powiązane z pojedynczej tożsamości użytkownika, ich musi być zbiorczo zakończona po stronie serwera na limit czasu lub Wyloguj. Ponadto upewnij się, że funkcje wylogowania dostępny na każdej stronie. |

## <a id="csrf-api"></a>Łagodzi skutki ataków fałszerstwo żądania Międzywitrynowego Międzywitrynowych na interfejsy API sieci Web platformy ASP.NET

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Fałszowanie żądań między witrynami (CSRF lub XSRF) jest typem ataku, w którym osoba atakująca może wykonywać akcje w kontekście zabezpieczeń ustanowienie sesji innego użytkownika w witrynie sieci web. Celem jest można zmodyfikować lub usunąć zawartość, jeśli docelowa witryna sieci web, który opiera się wyłącznie na pliki cookie z sesji do odebrane żądanie uwierzytelnienia. Osoba atakująca może wykorzystać tę lukę w zabezpieczeniach przez innego użytkownika przeglądarki, aby załadować adresu URL za pomocą polecenia z lokacji narażony, na którym użytkownik jest już zalogowany. Istnieje wiele sposobów, osoba atakująca to zrobić, takie jak inna witryna sieci web, służącym do ładowania zasobu z serwer na ataki, lub pobrać użytkownika, kliknij łącze. Ataków można zapobiec, jeśli serwer wysyła do klienta, dodatkowy token, wymaga od klienta, które mają zostać objęte ten token wszystkich przyszłych żądań i sprawdza się, że wszystkie przyszłe żądania zawierać token, które odnoszą się do bieżącej sesji, takie jak za pomocą platformy ASP.NET AntiForgeryToken lub stanu widoku. |

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | MVC5, MVC6 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Cross-Site Request Forgery (CSRF) ataku we wzorcu ASP.NET Web API](https://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks) |
| **Kroki** | I AJAX CSRF chroniących: Tokenu formularza może być problemem w przypadku żądań AJAX, ponieważ żądanie AJAX może wysyłać dane JSON, a nie dane formularza HTML. Rozwiązanie polega na wysyłanie tokenów w niestandardowy nagłówek HTTP. Poniższy kod używa składni Razor do generowania tokenów, a następnie dodaje tokenów z żądaniem AJAX. |

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
Podczas przetwarzania żądania prowadzenie nagłówek żądania tokenów. Następnie wywołaj metodę AntiForgery.Validate do weryfikacji tokenów. Metoda weryfikacji zgłasza wyjątek, jeśli tokeny nie są prawidłowe.
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
Anti-CSRF i formularzy platformy ASP.NET MVC — należy użyć metody pomocniczej AntiForgeryToken w widokach; na przykład umieścić Html.AntiForgeryToken() w formularzu
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
}
```

### <a name="example"></a>Przykład
W powyższym przykładzie będą danych wyjściowych podobny do poniższego:
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Przykład
W tym samym czasie Html.AntiForgeryToken() zapewnia obiekt odwiedzający pliku cookie o nazwie __RequestVerificationToken z taką samą wartość jak losową wartość ukrytego przedstawionych powyżej. Następnie sprawdź poprawność przychodzącego post formularza, Dodaj filtr [ValidateAntiForgeryToken] do metody akcji docelowego. Na przykład:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Filtr autoryzacji, który sprawdza, czy:
* Żądanie przychodzące ma pliku cookie o nazwie __RequestVerificationToken
* Żądanie przychodzące ma `Request.Form` wpis o nazwie __RequestVerificationToken
* Te pliki cookie i `Request.Form` dopasowania wartości, zakładając, że wszystkie jest dobrze, żądanie przechodzi w zwykły sposób. Ale jeśli nie, następnie wystąpił błąd autoryzacji z komunikatem "wymagany token zabezpieczający przed sfałszowaniem nie został podany lub jest nieprawidłowy".

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | MVC5, MVC6 |
| **Atrybuty**              | Dostawca tożsamości dostawcę — usługi AD FS, tożsamości — usłudze Azure AD |
| **Odwołania**              | [Zabezpieczanie interfejsu API sieci Web za pomocą indywidualnych kont i logowania lokalnego we wzorcu ASP.NET Web API 2.2](https://www.asp.net/web-api/overview/security/individual-accounts-in-web-api) |
| **Kroki** | Jeśli internetowy interfejs API jest zabezpieczone przy użyciu protokołu OAuth 2.0, następnie go oczekuje, że token elementu nośnego w nagłówku żądania autoryzacji i nieograniczony dostęp do żądania, tylko wtedy, gdy token jest prawidłowy. W przeciwieństwie do uwierzytelniania na podstawie pliku cookie przeglądarki nie dołączać do żądania tokenów elementu nośnego. Klienta należy jawnie dołączyć tokenu elementu nośnego w nagłówku żądania. W związku z tym dla platformy ASP.NET internetowych interfejsów API chronione za pomocą protokołu OAuth 2.0, tokenów elementu nośnego są traktowane jako środek obrony przed atakami CSRF. Należy pamiętać, że jeśli MVC część aplikacji korzysta z uwierzytelniania formularzy (czyli pliki cookie używa), tokeny zabezpieczające przed fałszerstwem muszą być używane przez aplikację sieci web MVC. |

### <a name="example"></a>Przykład
Internetowy interfejs API musi być powiadamiane polegać tylko na tokenów elementu nośnego i pliki cookie nie w. Może to nastąpić przez następującą konfigurację w `WebApiConfig.Register` metody:

```csharp
config.SuppressDefaultHostAuthentication();
config.Filters.Add(new HostAuthenticationFilter(OAuthDefaults.AuthenticationType));
```

Metoda SuppressDefaultHostAuthentication informuje interfejsu API sieci Web, aby zignorować wszelkie uwierzytelniania, który znajduje się przed żądanie osiąga potok składnika Web API, usług IIS lub oprogramowania pośredniczącego OWIN. Dzięki temu będziemy ograniczać interfejsu API sieci Web do uwierzytelniania tylko za pomocą tokenów elementu nośnego.
