---
title: Inicjowanie MSAL.NET aplikacji klienckich | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o inicjowaniu publicznych aplikacji klienckich i poufnych przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/12/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 57ce6ab31421cd4016f7e204eeabce82f2f7e6a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083997"
---
# <a name="initialize-client-applications-using-msalnet"></a>Inicjowanie aplikacji klienckich przy użyciu MSAL.NET
W tym artykule opisano inicjowanie publicznych aplikacji klienckich klienckich i klientów korzystających z biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).  Aby dowiedzieć się więcej o typach aplikacji klienckich i opcjach konfiguracji aplikacji, przeczytaj [omówienie](msal-client-applications.md).

W przypadku MSAL.NET 3.x zalecanym sposobem tworzenia wystąpienia aplikacji jest użycie konstruktorów aplikacji: `PublicClientApplicationBuilder` i `ConfidentialClientApplicationBuilder`. Oferują one zaawansowany mechanizm, aby skonfigurować aplikację albo z kodu lub z pliku konfiguracyjnego, a nawet przez mieszanie obu podejść.

## <a name="prerequisites"></a>Wymagania wstępne
Przed zainicjowaniem aplikacji należy najpierw [ją zarejestrować,](quickstart-register-app.md) aby aplikacja mogła być zintegrowana z platformą tożsamości firmy Microsoft.  Po rejestracji mogą być potrzebne następujące informacje (które można znaleźć w witrynie Azure portal):

- Identyfikator klienta (ciąg reprezentujący identyfikator GUID)
- Adres URL dostawcy tożsamości (o nazwie wystąpienie) i grupa odbiorców logowania dla aplikacji. Te dwa parametry są łącznie znane jako organ.
- Identyfikator dzierżawy, jeśli piszesz wiersz aplikacji biznesowej wyłącznie dla twojej organizacji (również o nazwie aplikacja z jedną dzierżawą).
- Klucz tajny aplikacji (tajny ciąg klienta) lub certyfikat (typu X509Certificate2), jeśli jest to aplikacja klienta poufnego.
- W przypadku aplikacji sieci web, a czasami dla publicznych aplikacji klienckich (w szczególności gdy aplikacja musi używać brokera), należy również ustawić redirectUri, gdzie dostawca tożsamości skontaktuje się z powrotem aplikacji za pomocą tokenów zabezpieczających.

## <a name="ways-to-initialize-applications"></a>Sposoby inicjowania aplikacji
Istnieje wiele różnych sposobów tworzenia wystąpienia aplikacji klienckich.

### <a name="initializing-a-public-client-application-from-code"></a>Inicjowanie publicznej aplikacji klienckiej na podstawie kodu

Poniższy kod zawiera wystąpienia publicznej aplikacji klienckiej, logujących się użytkowników w chmurze publicznej platformy Microsoft Azure, z ich kontami służbowymi i szkolnymi lub osobistymi kontami Microsoft.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>Inicjowanie poufnej aplikacji klienckiej z kodu

W ten sam sposób poniższy kod zawiera wystąpienia poufnej `https://myapp.azurewebsites.net`aplikacji (aplikacji sieci Web znajdującej się przy ) obsługi tokenów od użytkowników w chmurze publicznej platformy Microsoft Azure, z ich kontami służbowymi i szkolnymi lub osobistymi kontami Microsoft. Aplikacja jest identyfikowana z dostawcą tożsamości przez udostępnianie klucza tajnego klienta:

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

Jak być może wiesz, w produkcji, zamiast przy użyciu klucza tajnego klienta, można udostępnić za pomocą usługi Azure AD certyfikatu. Kod będzie następnie następujące:

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>Inicjowanie publicznej aplikacji klienckiej z opcji konfiguracji

Poniższy kod wystąpienia publicznej aplikacji klienckiej z obiektu konfiguracji, który może być wypełniony programowo lub odczytywany z pliku konfiguracji:

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>Inicjowanie poufnej aplikacji klienckiej z opcji konfiguracji

Ten sam rodzaj wzorca ma zastosowanie do poufnych aplikacji klienckich. Można również dodać inne `.WithXXX` parametry za pomocą modyfikatorów (tutaj certyfikat).

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>Modyfikatory konstruktorów

W fragmentach kodu przy użyciu konstruktorów `.With` aplikacji można zastosować szereg metod jako `.WithCertificate` `.WithRedirectUri`modyfikatory (na przykład i ). 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>Modyfikatory wspólne dla publicznych i poufnych aplikacji klienckich

Modyfikatory, które można ustawić na kliencie publicznym lub konstruktora aplikacji klienta poufne są:

|Modyfikator | Opis|
|--------- | --------- |
|`.WithAuthority()`7 przesłonięcia | Ustawia domyślny urząd aplikacji do urzędu usługi Azure AD, z możliwością wyboru usługi Azure Cloud, odbiorców, dzierżawy (identyfikator dzierżawy lub nazwa domeny) lub dostarczanie bezpośrednio identyfikatora URI urzędu.|
|`.WithAdfsAuthority(string)` | Ustawia domyślny urząd aplikacji jako urząd usługi ADFS.|
|`.WithB2CAuthority(string)` | Ustawia domyślny urząd aplikacji jako urząd usługi Azure AD B2C.|
|`.WithClientId(string)` | Zastępuje identyfikator klienta.|
|`.WithComponent(string)` | Ustawia nazwę biblioteki przy użyciu MSAL.NET (ze względów telemetrycznych). |
|`.WithDebugLoggingCallback()` | Jeśli wywołana, aplikacja `Debug.Write` wywoła po prostu włączenie śledzenia debugowania. Zobacz [Rejestrowanie, aby](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) uzyskać więcej informacji.|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | Ustaw dodatkowe parametry zapytania na poziomie aplikacji, które będą wysyłane we wszystkich żądaniach uwierzytelniania. Jest to możliwe do zastąpienia na każdym poziomie `.WithExtraQueryParameters pattern`metody pozyskiwania tokenu (z tym samym).|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | Umożliwia zaawansowane scenariusze, takie jak konfigurowanie serwera proxy HTTP lub wymuszanie używania określonego protokołu HttpClient (na przykład w ASP.NET podstawowych aplikacji sieci Web/interfejsów API).|
|`.WithLogging()` | Jeśli wywołana, aplikacja wywoła wywołanie zwrotne ze śladami debugowania. Zobacz [Rejestrowanie, aby](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) uzyskać więcej informacji.|
|`.WithRedirectUri(string redirectUri)` | Zastępuje domyślny identyfikator URI przekierowania. W przypadku publicznych aplikacji klienckich będzie to przydatne w przypadku scenariuszy z udziałem brokera.|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | Ustawia pełnomocnika używanego do wysyłania danych telemetrycznych.|
|`.WithTenantId(string tenantId)` | Zastępuje identyfikator dzierżawy lub opis dzierżawy.|

### <a name="modifiers-specific-to-xamarinios-applications"></a>Modyfikatory specyficzne dla aplikacji Xamarin.iOS

Modyfikatory, które można ustawić w konstruktorze aplikacji klienta publicznego na xamarin.iOS są:

|Modyfikator | Opis|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **Tylko xamarin.iOS:** Ustawia grupę zabezpieczeń łańcucha kluczy systemu iOS (dla trwałości pamięci podręcznej).|

### <a name="modifiers-specific-to-confidential-client-applications"></a>Modyfikatory specyficzne dla poufnych aplikacji klienckich

Modyfikatory, które można ustawić w konstruktorze aplikacji klienta poufne są:

|Modyfikator | Opis|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | Ustawia certyfikat identyfikujący aplikację za pomocą usługi Azure AD.|
|`.WithClientSecret(string clientSecret)` | Ustawia klucz tajny klienta (hasło aplikacji) identyfikujący aplikację za pomocą usługi Azure AD.|

Modyfikatory te wzajemnie się wykluczają. Jeśli podasz oba, MSAL zda znaczący wyjątek.

### <a name="example-of-usage-of-modifiers"></a>Przykład użycia modyfikatorów

Załóżmy, że aplikacja jest aplikacją biznesową, która jest tylko dla Twojej organizacji.  Następnie możesz napisać:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

Interesujące jest to, że programowanie chmur krajowych zostało uproszczone. Jeśli chcesz, aby aplikacja była aplikacją wielodostępną w chmurze krajowej, możesz napisać, na przykład:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

Istnieje również zastąpienie usługi ADFS (ADFS 2019 nie jest obecnie obsługiwany):
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Na koniec, jeśli jesteś deweloperem usługi Azure AD B2C, możesz określić dzierżawę w ten sposób:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```
