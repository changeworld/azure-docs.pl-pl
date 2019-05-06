---
title: Inicjowanie aplikacji klienckich (Biblioteka Microsoft Authentication Library dla platformy .NET) | Azure
description: Więcej informacji na temat inicjowania publicznego klienci i aplikacje poufne klienta przy użyciu Biblioteka Microsoft Authentication Library for .NET (platformy MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6197d472bdfaf03c9f99baa7691354e735cc91e
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075808"
---
# <a name="initialize-client-applications-using-msalnet"></a>Inicjowanie aplikacji klienckich przy użyciu platformy MSAL.NET
W tym artykule opisano, inicjowanie publicznych klienta i poufne klienta aplikacji przy użyciu Biblioteka Microsoft Authentication Library for .NET (platformy MSAL.NET).  Aby dowiedzieć się więcej na temat typów aplikacji klienckich i opcje konfiguracji aplikacji, przeczytaj [Przegląd](msal-client-applications.md).

Przy użyciu platformy MSAL.NET 3.x, zalecanym sposobem tworzenia wystąpienia aplikacji polega na użyciu twórców aplikacji: `PublicClientApplicationBuilder` i `ConfidentialClientApplicationBuilder`. Oferują one zaawansowanym mechanizmem służącym do konfigurowania aplikacji, od kodu, lub z pliku konfiguracji lub nawet przez mieszanie oba podejścia.

## <a name="prerequisites"></a>Wymagania wstępne
Przed inicjowania aplikacji, musisz najpierw [go zarejestrować](quickstart-register-app.md) tak, aby aplikację można zintegrować z platformą Microsoft identity.  Po zarejestrowaniu może być konieczne następujące informacje (które można znaleźć w witrynie Azure portal):

- Identyfikator klienta (ciąg reprezentujący identyfikator GUID)
- Adres URL dostawcy tożsamości (nazwanego wystąpienia) i grupy odbiorców logowania dla swojej aplikacji. Te dwa parametry są nazywane zbiorczo urzędu.
- Identyfikator dzierżawy, jeśli piszesz linię aplikacji biznesowej wyłącznie na potrzeby Twojej organizacji (również o nazwie aplikacja jednej dzierżawy).
- Klucz tajny aplikacji (ciąg klucza tajnego klienta) lub certyfikatu (typ X509Certificate2), jeśli jest to aplikacja poufne klienta.
- Dla aplikacji sieci web, a czasami dla aplikacji publicznych klienta (w szczególności, gdy Twoja aplikacja wymaga pod kątem wykorzystania brokera) będzie również ustawiono element redirectUri gdzie dostawcy tożsamości skontaktuje się z powrotem aplikacji przy użyciu tokenów zabezpieczających.

## <a name="ways-to-initialize-applications"></a>Sposoby Inicjowanie aplikacji
Istnieje wiele różnych sposobów, aby utworzyć wystąpienie aplikacji klienckich.

### <a name="initializing-a-public-client-application-from-code"></a>Inicjowanie aplikacji publicznych klienta, od kodu

Poniższy kod tworzy wystąpienie aplikacji publicznych klienta, logowanie użytkowników w chmurze publicznej Microsoft Azure z pracą i kont służbowych lub ich osobistych kont Microsoft.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>Inicjowanie aplikacji zawierających poufne dane klienta z poziomu kodu

Tak samo jak poniższy kod tworzy wystąpienie aplikacji zawierających poufne dane (aplikacja sieci Web znajdujących się na `https://myapp.azurewebsites.net`) Obsługa tokeny od użytkowników w chmurze publicznej Microsoft Azure z pracą i kont służbowych lub ich osobistych kont Microsoft. Aplikacja jest identyfikowane za pomocą dostawcy tożsamości, udostępniając klucz tajny klienta:

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

Być może wiesz, w środowisku produkcyjnym, a nie za pomocą klucza tajnego klienta, można udostępniać z usługą Azure AD certyfikat. Następnie byłby następujący kod:

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>Inicjowanie aplikacji klienckiej publicznych z opcji konfiguracji

Poniższy kod tworzy wystąpienie aplikacji klienckiej publicznych z obiektu konfiguracji, które mogą być wypełniane programowo lub odczytu z pliku konfiguracji:

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>Inicjowanie aplikacji zawierających poufne dane klienta z opcji konfiguracji

Tego samego rodzaju wzorzec ma zastosowanie do aplikacji poufne klienta. Możesz także dodać inne parametry przy użyciu `.WithXXX` Modyfikatory (tutaj certyfikatu).

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>Modyfikatory konstruktora

W fragmenty kodu przy użyciu twórców aplikacji, szereg `.With` metody mogą być stosowane jako modyfikatory (na przykład `.WithCertificate` i `.WithRedirectUri`). 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>Modyfikatory wspólne dla aplikacji klienckich publicznych i poufne

Modyfikatorów, których można ustawić na publiczny klienta lub Konstruktor aplikacji zawierających poufne dane klienta są następujące:

|Parametr | Opis|
|--------- | --------- |
|`.WithAuthority()` zastąpienia 7 | Ustawia domyślnego urzędu aplikacji urzędu usługi Azure AD, z możliwością wybierania chmury platformy Azure, odbiorców, dzierżawę (dzierżawę Identyfikatora lub nazwy domeny), albo udostępniające bezpośrednio identyfikator URI urzędu.|
|`.WithAdfsAuthority(string)` | Ustawia domyślnego urzędu aplikacji jako urzędu usług ADFS.|
|`.WithB2CAuthority(string)` | Ustawia domyślnego urzędu aplikacji jako urząd usługi Azure AD B2C.|
|`.WithClientId(string)` | Zastępuje identyfikator klienta.|
|`.WithComponent(string)` | Określa nazwę biblioteki przy użyciu platformy MSAL.NET (ze względów telemetrii). |
|`.WithDebugLoggingCallback()` | Jeśli wywołany, aplikacja będzie wywoływać `Debug.Write` po prostu Włączanie ślady debugowania. Zobacz [rejestrowania](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) Aby uzyskać więcej informacji.|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | Ustawianie parametrów zapytania dodatkowego poziomu aplikacji, które będą wysyłane do wszystkich żądań uwierzytelnienia. Jest to możliwym do zastąpienia na każdym poziomie metody tokenu (o takiej samej `.WithExtraQueryParameters pattern`).|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | Włącza zaawansowane scenariusze, takie jak konfigurowanie dla serwera proxy HTTP lub wymusić biblioteki MSAL do użycia określonego HttpClient (na przykład w aplikacji sieci web platformy ASP.NET Core/API).|
|`.WithLogging()` | Jeśli o nazwie, aplikacja będzie wywoływać wywołanie zwrotne ślady debugowania. Zobacz [rejestrowania](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) Aby uzyskać więcej informacji.|
|`.WithRedirectUri(string redirectUri)` | Zastępuje domyślny identyfikator URI przekierowania. W przypadku aplikacji publicznych klienta są to przydatne w scenariuszach obejmujących brokera.|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | Ustawia delegata używany do wysyłania danych telemetrycznych.|
|`.WithTenantId(string tenantId)` | Powoduje zastąpienie Identyfikatora dzierżawy lub opisu dzierżawcy.|

### <a name="modifiers-specific-to-xamarinios-applications"></a>Modyfikatory specyficzne dla aplikacji platformy Xamarin.iOS

Modyfikatorów, których można ustawić na publicznych klienta konstruktora aplikacji na platformy Xamarin.iOS są następujące:

|Parametr | Opis|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **Tylko Xamarin.iOS**: Ustawia grupę zabezpieczeń dla systemu iOS łańcucha kluczy (na potrzeby stanu trwałego pamięci podręcznej).|

### <a name="modifiers-specific-to-confidential-client-applications"></a>Modyfikatory specyficzne dla aplikacji zawierających poufne dane klienta

Modyfikatorów, których można ustawić na Konstruktor aplikacji zawierających poufne dane klienta są następujące:

|Parametr | Opis|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | Ustawia certyfikat identyfikujący aplikację w usłudze Azure AD.|
|`.WithClientSecret(string clientSecret)` | Ustawia klucz tajny klienta (hasła aplikacji) identyfikuje aplikację w usłudze Azure AD.|

Następujące Modyfikatory są wzajemnie się wykluczają. Jeśli podasz zarówno MSAL zgłosi wyjątek zrozumiałe.

### <a name="example-of-usage-of-modifiers"></a>Przykład użycia modyfikatorów

Załóżmy, że aplikacja jest aplikacją "line of business" jest przeznaczony tylko dla Twojej organizacji.  Następnie można napisać:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

Gdzie staje się interesujące jest teraz uprościła programowania chmur krajowych. Jeśli chcesz, aby aplikacja ma być aplikacji z wieloma dzierżawami chmury krajowe, można napisać, na przykład:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

Dostępna jest również przesłonięcie dla usług AD FS (2019 usług AD FS nie jest obecnie obsługiwane):
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Na koniec Jeśli jesteś twórcą usługi Azure AD B2C, należy określić dzierżawy następująco:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```
