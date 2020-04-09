---
title: Najważniejsze wskazówki dotyczące obsługi błędów aplikacji klienckiej ADAL | Azure
description: Zawiera wskazówki dotyczące obsługi błędów i najlepsze rozwiązania dotyczące aplikacji klienckich ADAL.
services: active-directory
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: conceptual
ms.workload: identity
ms.date: 02/27/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 8973412b2d6575d524874ba05b34af7661655e19
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80981073"
---
# <a name="error-handling-best-practices-for-azure-active-directory-authentication-library-adal-clients"></a>Najważniejsze wskazówki dotyczące obsługi błędów dla klientów biblioteki uwierzytelniania usługi Azure Active Directory (ADAL)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Ten artykuł zawiera wskazówki dotyczące typu błędów, które deweloperzy mogą napotkać podczas korzystania z usługi ADAL do uwierzytelniania użytkowników. Podczas korzystania z usługi ADAL, istnieje kilka przypadków, w których deweloper może trzeba krok i obsługi błędów. Prawidłowa obsługa błędów zapewnia doskonałe środowisko użytkownika końcowego i ogranicza liczbę razy, w tym gdy użytkownik końcowy musi się zalogować.

W tym artykule możemy zbadać konkretne przypadki dla każdej platformy obsługiwane przez usługi ADAL i jak aplikacja może prawidłowo obsługiwać każdy przypadek. Wskazówki dotyczące błędów są podzielone na dwie szersze kategorie, na podstawie wzorców pozyskiwania tokenów dostarczonych przez interfejsy API ADAL:

- **AcquireTokenSilent:** Klient próbuje uzyskać token po cichu (bez interfejsu użytkownika) i może zakończyć się niepowodzeniem, jeśli usługa ADAL zakończy się niepowodzeniem. 
- **AcquireToken**: Klient może próbować cichego nabycia, ale może również wykonywać interaktywne żądania, które wymagają logowania.

> [!TIP]
> Dobrym pomysłem jest rejestrowanie wszystkich błędów i wyjątków podczas korzystania z usługi ADAL i usługi Azure AD. Dzienniki są nie tylko pomocne w zrozumieniu ogólnej kondycji aplikacji, ale są również ważne podczas debugowania szerszych problemów. Podczas gdy aplikacja może odzyskać z niektórych błędów, mogą one wskazywać na szersze problemy z projektem, które wymagają zmian kodu w celu rozwiązania. 
> 
> Podczas implementowania warunków błędu omówionych w tym dokumencie, należy zarejestrować kod błędu i opis z powodów omówionych wcześniej. Zobacz [odwołanie do błędu i rejestrowania](#error-and-logging-reference) przykłady kodu rejestrowania. 
>

## <a name="acquiretokensilent"></a>AcquireTokenSilent (AcquireTokenSilent)

AcquireTokenSilent próbuje uzyskać token z gwarancją, że użytkownik końcowy nie widzi interfejsu użytkownika (UI). Istnieje kilka przypadków, w których ciche pozyskiwanie może zakończyć się niepowodzeniem i musi być obsługiwane za pośrednictwem żądań interaktywnych lub przez domyślny program obsługi. Zagłębiamy się w szczegóły, kiedy i jak zatrudniać każdy przypadek w kolejnych sekcjach.

Istnieje zestaw błędów generowanych przez system operacyjny, które mogą wymagać obsługi błędów specyficzne dla aplikacji. Aby uzyskać więcej informacji, zobacz sekcję Błędy systemu operacyjnego w sekcji [Błąd i odwołanie do rejestrowania](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Scenariusze aplikacji

- [Natywne aplikacje klienckie](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#native-client) (iOS, Android, .NET Desktop lub Xamarin)
- Aplikacje [klienckie w sieci Web](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#web-client) wywołujące [zasób](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#resource-server) (.NET)

### <a name="error-cases-and-actionable-steps"></a>Przypadki błędów i kroki zasłaniania

Zasadniczo istnieją dwa przypadki błędów AcquireTokenSilent:

| Sprawa | Opis |
|------|-------------|
| **Przypadek 1:** Błąd można rozwiązać za pomocą interaktywnego logowania | W przypadku błędów spowodowanych brakiem prawidłowych tokenów konieczne jest żądanie interaktywne. W szczególności wyszukiwanie w pamięci podręcznej i nieprawidłowy/wygasły token odświeżania wymagają wywołania AcquireToken, aby rozwiązać.<br><br>W takich przypadkach użytkownik końcowy musi zostać poproszony o zalogowanie się. Aplikacja może wybrać opcję natychmiastowego wykonania żądania interaktywnego po interakcji z użytkownikiem końcowym (na przykład naciśnięcie przycisku logowania) lub później. Wybór zależy od pożądanego zachowania aplikacji.<br><br>Zobacz kod w poniższej sekcji dla tego konkretnego przypadku i błędy, które go zdiagnozować.|
| **Przypadek 2:** Błąd nie można rozwiązać za pomocą interaktywnego logowania | W przypadku błędów sieciowych i przejściowych/tymczasowych lub innych błędów wykonanie interaktywnego żądania AcquireToken nie rozwiązuje problemu. Niepotrzebne interaktywne monity logowania mogą również frustrować użytkowników końcowych. ADAL automatycznie próbuje ponowić próbę dla większości błędów w błędach AcquireTokenSilent.<br><br>Aplikacja kliencka może również podjąć próbę ponowienia próby w pewnym późniejszym momencie, ale kiedy i jak zależy od zachowania aplikacji i pożądanego środowiska użytkownika końcowego. Na przykład aplikacja może wykonać AcquireTokenSilent ponowie próbę po kilku minutach lub w odpowiedzi na niektóre działania użytkownika końcowego. Natychmiastowe ponowienie próby spowoduje, że aplikacja zostanie ograniczona i nie należy podejmować prób.<br><br>Kolejna ponowna próba niepowodzenia z tym samym błędem nie oznacza, że klient powinien wykonać interaktywne żądanie przy użyciu AcquireToken, ponieważ nie rozwiązuje błędu.<br><br>Zobacz kod w poniższej sekcji dla tego konkretnego przypadku i błędy, które go zdiagnozować. |

### <a name="net"></a>.NET

Poniższe wskazówki zawierają przykłady obsługi błędów w połączeniu z metodami ADAL: 

- acquireTokenSilentAsync(...)
- acquireTokenSilentSync(...) 
- [przestarzałe] acquireTokenSilent(...)
- [przestarzały] acquireTokenByRefreshToken(...) 

Twój kod zostanie zaimplementowany w następujący sposób:

```csharp
try{
    AcquireTokenSilentAsync(…);
} 

catch (AdalSilentTokenAcquisitionException e) {
    // Exception: AdalSilentTokenAcquisitionException
    // Caused when there are no tokens in the cache or a required refresh failed. 

    // Action: Case 1, resolvable with an interactive request. 
} 

catch(AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Action: Case 2, not resolvable with an interactive request.
    // Attempt retry after a timed interval or user action.
} 
    
catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET. 
    // e.ErrorCode contains the error code. 

    // Action: Case 2, not resolvable with an interactive request.
    // Attempt retry after a timed interval or user action.
    // Example Error: network_not_available, default case.
}
```

### <a name="android"></a>Android

Poniższe wskazówki zawierają przykłady obsługi błędów w połączeniu z metodami ADAL: 

- acquireTokenSilentSync(...)
- acquireTokenSilentAsync(...)
- [przestarzałe] acquireTokenSilent(...)

Twój kod zostanie zaimplementowany w następujący sposób:

```java
// *Inside callback*
public void onError(Exception e) {

    if (e instanceof AuthenticationException) {
        // Exception: AdalException
        // Represents a library exception generated by ADAL Android.
        // Error Code: e.getCode().

        // Errors: ADALError.ERROR_SILENT_REQUEST,
        // ADALError.AUTH_REFRESH_FAILED_PROMPT_NOT_ALLOWED,
        // ADALError.INVALID_TOKEN_CACHE_ITEM
        // Description: Request failed due to no tokens in
        // cache or failed a required refresh. 

        // Action: Case 1, resolvable with an interactive request. 

        // Action: Case 2, not resolvable with an interactive request.
        // Attempt retry after a timed interval or user action.
        // Example Errors: default case,
        // DEVICE_CONNECTION_IS_NOT_AVAILABLE, 
        // BROKER_AUTHENTICATOR_ERROR_GETAUTHTOKEN,
    }
}
```

### <a name="ios"></a>iOS

Poniższe wskazówki zawierają przykłady obsługi błędów w połączeniu z metodami ADAL: 

- acquireTokenSilentWithResource(...)

Twój kod zostanie zaimplementowany w następujący sposób:

```objc
[context acquireTokenSilentWithResource:[ARGS], completionBlock:^(ADAuthenticationResult *result) {
    if (result.status == AD_FAILED) {
        if ([error.domain isEqualToString:ADAuthenticationErrorDomain]){
            // Exception: AD_FAILED 
            // Represents a library error generated by ADAL Objective-C.
            // Error Code: result.error.code

            // Errors: AD_ERROR_SERVER_REFRESH_TOKEN_REJECTED, AD_ERROR_CACHE_NO_REFRESH_TOKEN
            // Description: No tokens in cache or failed a required token refresh failed. 
            // Action: Case 1, resolvable with an interactive request. 

            // Error: AD_ERROR_CACHE_MULTIPLE_USERS
            // Description: There was ambiguity in the silent request resulting in multiple cache items.
            // Action: Special Case, application should perform another silent request and specify the user using ADUserIdentifier. 
            // Can be caused in cases of a multi-user application. 

            // Action: Case 2, not resolvable with an interactive request.
            // Attempt retry after some time or user action.
            // Example Errors: default case,
            // AD_ERROR_CACHE_BAD_FORMAT
        }
    }
}]
```

## <a name="acquiretoken"></a>AcquireToken (AcquireToken)

AcquireToken jest domyślną metodą ADAL używaną do uzyskiwania tokenów. W przypadkach, gdy wymagana jest tożsamość użytkownika AcquireToken próbuje najpierw po cichu uzyskać token, a następnie wyświetla interfejs użytkownika w razie potrzeby (chyba że promptbehavior.Never nie jest przekazywana). W przypadkach, gdy wymagana jest tożsamość aplikacji AcquireToken próbuje uzyskać token, ale nie pokazuje interfejsu użytkownika, ponieważ nie ma użytkownika końcowego. 

Podczas obsługi błędów AcquireToken obsługa błędów zależy od platformy i scenariusza, który aplikacja próbuje osiągnąć. 

System operacyjny może również generować zestaw błędów, które wymagają obsługi błędów w zależności od określonej aplikacji. Aby uzyskać więcej informacji, zobacz "Błędy systemu operacyjnego" w [obszarze Błąd i odwołanie do rejestrowania](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Scenariusze aplikacji

- Natywne aplikacje klienckie (iOS, Android, .NET Desktop lub Xamarin)
- Aplikacje sieci Web wywołujące interfejs API zasobów (.NET)
- Aplikacje jednostronicowe (JavaScript)
- Aplikacje usługi do usługi (.NET, Java)
  - Wszystkie scenariusze, w tym w imieniu
  - W imieniu konkretnych scenariuszy

### <a name="error-cases-and-actionable-steps-native-client-applications"></a>Przypadki błędów i kroki, które można wykonać: natywne aplikacje klienckie

Jeśli budujesz natywną aplikację kliencką, istnieje kilka przypadków obsługi błędów, które należy wziąć pod uwagę, które odnoszą się do problemów z siecią, błędów przejściowych i innych błędów specyficznych dla platformy. W większości przypadków aplikacja nie powinna wykonywać natychmiastowych ponownych prób, ale raczej czekać na interakcję użytkownika końcowego, który monituje logowania. 

Istnieje kilka szczególnych przypadków, w których pojedyncza ponowna próba może rozwiązać problem. Na przykład, gdy użytkownik musi włączyć dane na urządzeniu lub ukończone pobieranie brokera usługi Azure AD po początkowym niepowodzeniu. 

W przypadku awarii aplikacja może przedstawić interfejs użytkownika, aby umożliwić użytkownikowi końcowemu wykonanie niektórych interakcji, która monituje o ponowną próbę. Na przykład jeśli urządzenie nie powiodło się dla błędu w trybie offline, przycisk "Spróbuj zalogować się ponownie" monitowanie AcquireToken ponowić próbę, a nie natychmiast ponowić próbę błędu. 

Obsługa błędów w aplikacjach natywnych może być zdefiniowana przez dwa przypadki:

|  |  |
|------|-------------|
| **Przypadek 1**:<br>Błąd nieuprzepróbowalny (w większości przypadków) | 1. Nie próbuj natychmiastowej próby. Przedstaw interfejs użytkownika końcowego na podstawie określonego błędu, który wywołuje ponowną próbę (na przykład "Spróbuj zalogować się ponownie" lub "Pobierz aplikację brokera usługi Azure AD"). |
| **Przypadek 2**:<br>Błąd ponowialny | 1. Wykonaj jedną próbę ponowienia, ponieważ użytkownik końcowy mógł wprowadzić stan, który kończy się sukcesem.<br><br>2. Jeśli ponowna próba nie powiedzie się, przedstaw interfejs użytkownika końcowego na podstawie określonego błędu, który wywołuje ponowną próbę ("Spróbuj zalogować się ponownie", "Pobierz aplikację brokera usługi Azure AD" itp.). |

> [!IMPORTANT]
> Jeśli konto użytkownika jest przekazywane do usługi ADAL w wywołaniu cichym i kończy się niepowodzeniem, kolejne żądanie interaktywne umożliwia użytkownikowi końcowemu zalogowanie się przy użyciu innego konta. Po pomyślnym acquireToken przy użyciu konta użytkownika, aplikacja musi sprawdzić zalogowany użytkownik pasuje do obiektu użytkownika lokalnego aplikacji. Niezgodność nie generuje wyjątek (z wyjątkiem celu C), ale należy wziąć pod uwagę w przypadkach, gdy użytkownik jest znany lokalnie przed żądania uwierzytelniania (jak nieudane wywołanie niemne).
>

#### <a name="net"></a>.NET

Poniższe wskazówki zawierają przykłady obsługi błędów w połączeniu ze wszystkimi niedyskretnymi usługami AcquireToken(...) Metody ADAL, *z wyjątkiem:* 

- AcquireTokenAsync(..., IClientAssertionCertification, ...)
- AcquireTokenAsync(..., ClientCredential, ...)
- AcquireTokenAsync(..., ClientAssertion, ...)
- AcquireTokenAsync(..., UserAssertion,...)   

Twój kod zostanie zaimplementowany w następujący sposób:

```csharp
try {
    AcquireTokenAsync(…);
} 
    
catch(AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.
    
    // Design time consideration: Certain errors may be caused at development and exposed through this exception. 
    // Looking inside the description will give more guidance on resolving the specific issue. 

    // Action: Case 1: Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: default case

    } 

catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET.
    // e.ErrorCode contains the error code

    // Action: Case 1, Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: network_not_available, default case
}
```

> [!NOTE]
> ADAL .NET ma dodatkowe uwagę, ponieważ obsługuje PromptBehavior.Never, który ma zachowanie jak AcquireTokenSilent.
>

Poniższe wskazówki zawierają przykłady obsługi błędów w połączeniu z metodami ADAL: 

- acquireToken(..., PromptBehavior.Never)

Twój kod zostanie zaimplementowany w następujący sposób:

```csharp
    try {acquireToken(…, PromptBehavior.Never);
    } 

catch(AdalServiceException e) {
    // Exception: AdalServiceException represents 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Action: Case 1: Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: default case

} catch (AdalException e) {
    // Error Code: e.ErrorCode == "user_interaction_required"
    // Description: user_interaction_required indicates the silent request failed 
    // in a way that's resolvable with an interactive request.
    // Action: Resolvable with an interactive request. 

    // Action: Case 1, Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: network_not_available, default case
}
```

#### <a name="android"></a>Android

Poniższe wskazówki zawierają przykłady obsługi błędów w połączeniu ze wszystkimi niedyskretnymi usługami AcquireToken(...) metody ADAL. 

Twój kod zostanie zaimplementowany w następujący sposób:

```java
AcquireTokenAsync(…);

// *Inside callback*
public void onError(Exception e) {
    if (e instanceof AuthenticationException) {
        // Exception: AdalException 
        // Represents a library exception generated by ADAL Android.
        // Error Code: e.getCode();

        // Error: ADALError.BROKER_APP_INSTALLATION_STARTED
        // Description: Broker app not installed, user will be prompted to download the app. 

        // Action: Case 2, Retriable Error 
        // Perform a single retry. If that fails, only try again after user action. 

        // Action: Case 1, Non-Retriable 
        // Do not perform an immediate retry. Only retry after user action. 
        // Example Errors: default case, DEVICE_CONNECTION_IS_NOT_AVAILABLE
    }
}
```

#### <a name="ios"></a>iOS

Poniższe wskazówki zawierają przykłady obsługi błędów w połączeniu ze wszystkimi niedyskretnymi usługami AcquireToken(...) metody ADAL. 

Twój kod zostanie zaimplementowany w następujący sposób:

```objc
[context acquireTokenWithResource:[ARGS], completionBlock:^(ADAuthenticationResult *result) {
    if (result.status == AD_FAILED) {
        if ([error.domain isEqualToString:ADAuthenticationErrorDomain]){
            // Exception: AD_FAILED 
            // Represents a library error generated by ADAL ObjC.
            // Error Code: result.error.code 

            // Error: AD_ERROR_SERVER_WRONG_USER
            // Description: App passed a user into ADAL and the end user signed in with a different account. 
            // Action: Case 1, Non-retriable (as is) and up to the application on how to handle this case. 
            // It can attempt a new request without specifying the user, or use UI to clarify the user account to sign in. 

            // Action: Case 1, Non-Retriable 
            // Do not perform an immediate retry. Only retry after user action. 
            // Example Errors: default case
        }
    }
}]
```

### <a name="error-cases-and-actionable-steps-web-applications-that-call-a-resource-api-net"></a>Przypadki błędów i kroki, które można wykonać: aplikacje sieci Web, które wywołują interfejs API zasobów (.NET)

Jeśli budujesz aplikację sieci web .NET, która wywołuje pobiera token przy użyciu kodu autoryzacji dla zasobu, tylko kod wymagany jest domyślny program obsługi dla przypadku ogólnego. 

Poniższe wskazówki zawierają przykłady obsługi błędów w połączeniu z metodami ADAL: 

- AcquireTokenByAuthorizationCodeAsync(...)

Twój kod zostanie zaimplementowany w następujący sposób:

```csharp
try {
    AcquireTokenByAuthorizationCodeAsync(…);
} 

catch (AdalException e) {
    // Exception: AdalException
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action or wait until much later. 
    // Example Errors: default case
}
```

### <a name="error-cases-and-actionable-steps-single-page-applications-adaljs"></a>Przypadki błędów i kroki, które można wykonać: aplikacje jednostronicowe (adal.js)

Jeśli budujesz aplikacji jednostronicowej przy użyciu adal.js z AcquireToken, kod obsługi błędów jest podobny do typowego wywołania dyskretnego. W szczególności w adal.js AcquireToken nigdy nie pokazuje interfejsu użytkownika. 

Nieudane acquireToken ma następujące przypadki:

|  |  |
|------|-------------|
| **Przypadek 1**:<br>Możliwość ponownego rozpalnej za pomocą interaktywnego żądania | 1. Jeśli login() nie powiedzie się, nie należy wykonywać natychmiastowej próby. Ponów próbę tylko po wyświetlenie ponów po ponów próby użytkownika.|
| **Przypadek 2**:<br>Nie można rozwiązać z żądaniem interaktywnym. Błąd jest ponawiany. | 1. Wykonaj jedną próbę ponawiania, ponieważ główny użytkownik końcowy wszedł w stan, który kończy się sukcesem.<br><br>2. Jeśli ponowna próba nie powiedzie się, przedstawić użytkownikowi końcowemu akcję opartą na określonym błędzie, który może wywołać ponowną próbę ("Spróbuj zalogować się ponownie"). |
| **Przypadek 3**:<br>Nie można rozwiązać z żądaniem interaktywnym. Błąd nie jest ponawiany. | 1. Nie próbuj natychmiastowej próby. Przedstaw użytkownikowi końcowemu akcję opartą na określonym błędzie, który może wywołać ponowną próbę ("Spróbuj zalogować się ponownie"). |

Twój kod zostanie zaimplementowany w następujący sposób:

```javascript
AuthContext.acquireToken(…, function(error, errorDesc, token) {
    if (error || errorDesc) {
        // Represents any token acquisition failure that occurred. 
        // Error Code: error.indexOf("<ERROR_STRING>")

        // Errors: if (error.indexOf("interaction_required"))
        //         if (error.indexOf("login required"))
        // Description: ADAL wasn't able to silently acquire a token because of expire or fresh session. 
        // Action: Case 1, Resolvable with an interactive login() request. 

        // Error: if (error.indexOf("Token Renewal Failed")) 
        // Description: Timeout when refreshing the token.
        // Action: Case 2, Not resolvable interactively, error is retriable.
        // Perform a single retry. Only try again after user action.

        // Action: Case 3, Not resolvable interactively, error is not retriable. 
        // Do not perform an immediate retry. Only retry after user action.
        // Example Errors: default case
    }
}
```

### <a name="error-cases-and-actionable-steps-service-to-service-applications-net-only"></a>Przypadki błędów i kroki, które można wykonać: aplikacje usługi do usługi (tylko.NET)

Jeśli budujesz aplikację usługi do usługi, która używa AcquireToken, istnieje kilka błędów klucza kod musi obsłużyć. Jedynym odwołaniem się do niepowodzenia jest zwrócenie błędu z powrotem do aplikacji wywołującej (w przypadku w imieniu) lub zastosowanie strategii ponawiania. 

#### <a name="all-scenarios"></a>Wszystkie scenariusze

Dla *wszystkich* scenariuszy aplikacji usługi do usługi, w tym w imieniu:

- Nie próbuj natychmiastowej próby. ADAL próbuje ponowić próbę dla niektórych żądań nie powiodło się. 
- Kontynuuj ponawianie próby tylko po tym, jak akcja użytkownika lub aplikacji zostanie wyświetle monit o ponowną próbę. Na przykład aplikacja demona, który działa w pewnym przedziale zestawu należy poczekać do następnego interwału, aby ponowić próbę.

Poniższe wskazówki zawierają przykłady obsługi błędów w połączeniu z metodami ADAL: 

- AcquireTokenAsync(..., IClientAssertionCertification, ...)
- AcquireTokenAsync(...,ClientCredential, ...)
- AcquireTokenAsync(...,ClientAssertion, ...)
- AcquireTokenAsync(...,UserAssertion, ...)

Twój kod zostanie zaimplementowany w następujący sposób:

```csharp
try {
    AcquireTokenAsync(…);
} 

catch (AdalException e) {
    // Exception: AdalException
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action (if applicable) or wait until much later. 
    // Example Errors: default case
}  
```

#### <a name="on-behalf-of-scenarios"></a>Scenariusze w imieniu

W przypadku scenariuszy aplikacji usługi do usługi *w imieniu.*

Poniższe wskazówki zawierają przykłady obsługi błędów w połączeniu z metodami ADAL: 

- AcquireTokenAsync(..., UserAssertion, ...)

Twój kod zostanie zaimplementowany w następujący sposób:

```csharp
try {
AcquireTokenAsync(…);
} 

catch (AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Error: On-Behalf-Of Error Handler
    if (e.ErrorCode == "interaction_required") {
        // Description: The client needs to perform some action due to a config from admin. 
        // Action: Capture `claims` parameter inside ex.InnerException.InnerException.
        // Generate HTTP error 403 with claims, throw back HTTP error to client.
        // Wait for client to retry. 
    }
} 
        
catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action (if applicable) or wait until much later. 
    // Example Error: default case
}
```

Stworzyliśmy kompletny [przykład,](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) który pokazuje ten scenariusz.

## <a name="error-and-logging-reference"></a>Odwołanie do błędu i rejestrowania

### <a name="logging-personal-identifiable-information--organizational-identifiable-information"></a>Rejestrowanie danych osobowych umożliwiających identyfikację & identyfikowalnych informacji organizacji 
Domyślnie rejestrowanie ADAL nie przechwytuje ani nie rejestruje żadnych osobistych informacji umożliwiających identyfikację ani informacji umożliwiających identyfikację organizacji. Biblioteka umożliwia deweloperom aplikacji, aby włączyć to za pośrednictwem ustawiacza w Logger klasy. Rejestrując dane osobowe lub informacje umożliwiające identyfikację organizacji, aplikacja ponosi odpowiedzialność za bezpieczne przetwarzanie bardzo wrażliwych danych i spełnianie wszelkich wymogów regulacyjnych.

### <a name="net"></a>.NET

#### <a name="adal-library-errors"></a>Błędy biblioteki ADAL

Aby eksplorować określone błędy ADAL, kod źródłowy w [repozytorium azure-activedirectory-library-for-dotnet](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/8f6d560fbede2247ec0e217a21f6929d4375dcaa/src/ADAL.PCL/Utilities/Constants.cs#L58) jest najlepszym odwołaniem do błędu.

#### <a name="guidance-for-error-logging-code"></a>Wskazówki dotyczące kodu rejestrowania błędów

Rejestrowanie usługi ADAL .NET zmienia się w zależności od platformy, nad na która pracuje. Zapoznaj się z [wiki rejestrowania](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Logging-in-ADAL.Net) kodu, jak włączyć rejestrowanie.

### <a name="android"></a>Android

#### <a name="adal-library-errors"></a>Błędy biblioteki ADAL

Aby eksplorować określone błędy ADAL, kod źródłowy w [repozytorium azure-activedirectory-library-for-android](https://github.com/AzureAD/azure-activedirectory-library-for-android/blob/dev/adal/src/main/java/com/microsoft/aad/adal/ADALError.java#L33) jest najlepszym odwołaniem do błędu.

#### <a name="operating-system-errors"></a>Błędy systemu operacyjnego

Błędy systemu operacyjnego Systemu Android są udostępniane za pośrednictwem AuthenticationException w ADAL, są identyfikowalne jako "SERVER_INVALID_REQUEST" i mogą być dalej szczegółowe za pomocą opisów błędów. 

Aby uzyskać pełną listę typowych błędów i jakie kroki należy podjąć, gdy aplikacja lub użytkownicy końcowi napotkać je, zobacz [ADAL Android Wiki](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki). 

#### <a name="guidance-for-error-logging-code"></a>Wskazówki dotyczące kodu rejestrowania błędów

```java
// 1. Configure Logger
Logger.getInstance().setExternalLogger(new ILogger() {    
    @Override   
    public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) { 
    // …
    // You can write this to logfile depending on level or errorcode. 
    writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);    
    }
}

// 2. Set the log level
Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);

// By default, the `Logger` does not capture any PII or OII

// PII or OII will be logged
Logger.getInstance().setEnablePII(true);

// To STOP logging PII or OII, use the following setter
Logger.getInstance().setEnablePII(false);


// 3. Send logs to logcat.
adb logcat > "C:\logmsg\logfile.txt";
```

### <a name="ios"></a>iOS

#### <a name="adal-library-errors"></a>Błędy biblioteki ADAL

Aby eksplorować określone błędy ADAL, kod źródłowy w [repozytorium azure-activedirectory-library-for-objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc/blob/dev/ADAL/src/ADAuthenticationError.m#L295) jest najlepszym odwołaniem do błędu.

#### <a name="operating-system-errors"></a>Błędy systemu operacyjnego

Błędy systemu iOS mogą wystąpić podczas logowania, gdy użytkownicy korzystają z widoków sieci Web i charakteru uwierzytelniania. Może to być spowodowane warunkami, takimi jak błędy TLS, limity czasu lub błędy sieciowe:

- W przypadku udostępniania uprawnień logowania nie są trwałe, a pamięć podręczna jest pusta. Można rozwiązać, dodając następujący wiersz kodu do pęku kluczy:`[[ADAuthenticationSettings sharedInstance] setSharedCacheKeychainGroup:nil];`
- W przypadku zestawu błędów NsUrlDomain akcja zmienia się w zależności od logiki aplikacji. Zobacz [dokumentację referencyjną NSURLErrorDomain](https://developer.apple.com/documentation/foundation/nsurlerrordomain#declarations) dla określonych wystąpień, które mogą być obsługiwane.
- Zobacz [ADAL Obj-C typowe problemy](https://github.com/AzureAD/azure-activedirectory-library-for-objc#adauthenticationerror) dla listy typowych błędów obsługiwanych przez zespół ADAL Objective-C.

#### <a name="guidance-for-error-logging-code"></a>Wskazówki dotyczące kodu rejestrowania błędów

```objc
// 1. Enable NSLogging
[ADLogger setNSLogging:YES];

// 2. Set the log level (if you want verbose)
[ADLogger setLevel:ADAL_LOG_LEVEL_VERBOSE];

// 3. Set up a callback block to simply print out
[ADLogger setLogCallBack:^(ADAL_LOG_LEVEL logLevel, NSString *message, NSString *additionalInformation, NSInteger errorCode, NSDictionary *userInfo) {
     NSString* log = [NSString stringWithFormat:@"%@ %@", message, additionalInformation];    
     NSLog(@"%@", log);
}];
```

### <a name="guidance-for-error-logging-code---javascript"></a>Wskazówki dotyczące kodu rejestrowania błędów - JavaScript 

```javascript
0: Error1: Warning2: Info3: Verbose
window.Logging = {
    level: 3,
    log: function (message) {
        console.log(message);
    }
};
```

## <a name="related-content"></a>Zawartość pokrewna

* [Biblioteki uwierzytelniania usługi Azure AD][AAD-Auth-Libraries]
* [Scenariusze uwierzytelniania usługi Azure AD][AAD-Auth-Scenarios]
* [Integrowanie aplikacji z usługą Azure Active Directory][AAD-Integrating-Apps]

Skorzystaj z następującej sekcji komentarzy, aby przekazać opinię i pomóc nam udoskonalić i kształtować naszą zawartość.

[![Pokazuje przycisk "Zaloguj się za pomocą firmy Microsoft"][AAD-Sign-In]][AAD-Sign-In]
<!--Reference style links -->

[AAD-Auth-Libraries]: ./active-directory-authentication-libraries.md
[AAD-Auth-Scenarios]:v1-authentication-scenarios.md
[AAD-Integrating-Apps]:../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json
[AZURE-portal]: https://portal.azure.com

<!--Image references-->
[AAD-Sign-In]:./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png

