---
title: Błąd najlepsze praktyki dotyczące obsługi dla klientów interfejsów Azure Active Directory Authentication Library (ADAL)
description: Zapewnia obsługę wskazówek i najlepszych rozwiązań dla aplikacji klienckich biblioteki ADAL błędów.
services: active-directory
documentationcenter: ''
author: danieldobalian
manager: mtillman
ms.author: celested
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e92c3b302ab18aaaf20d187d61a488603ce81a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60411459"
---
# <a name="error-handling-best-practices-for-azure-active-directory-authentication-library-adal-clients"></a>Błąd najlepsze praktyki dotyczące obsługi dla klientów interfejsów Azure Active Directory Authentication Library (ADAL)

Ten artykuł zawiera wskazówki od rodzaju błędów, że deweloperzy mogą wystąpić, za pomocą biblioteki ADAL do uwierzytelniania użytkowników. Korzystając z biblioteki ADAL, istnieje kilka przypadków, gdy deweloper może być konieczne wejść do funkcji i obsługi błędów. Obsługa błędów odpowiednie zapewnia doskonałe użytkownika końcowego i ogranicza liczbę przypadków, gdy użytkownik końcowy musi się zalogować.

W tym artykule omówimy szczególnych przypadkach dla każdej platformy obsługiwane przez biblioteki ADAL i jak aplikacja może obsługiwać każde wystąpienie case prawidłowo. Wskazówki dotyczące błędu jest podzielony na dwie kategorie szersze, na podstawie wzorców uzyskanie tokenu, dostarczone przez interfejsy API biblioteki ADAL:

- **AcquireTokenSilent**: Klient próbuje uzyskać token w trybie dyskretnym (nie interfejsu użytkownika) i może się nie powieść, jeśli nie powiedzie się biblioteki ADAL. 
- **AcquireToken**: Klient może spróbować dyskretnej nabycia, ale można również wykonać interaktywnych żądań, które wymagają logowania.

> [!TIP]
> To dobry pomysł, aby rejestrować wszystkie błędy i wyjątki podczas korzystania z biblioteki ADAL i usługa Azure AD. Dzienniki nie są tylko pomaga w zrozumieniu ogólną kondycję aplikacji, ale również są ważne, przy debugowaniu problemów szersze. Gdy aplikacja może odzyskać z niektórych błędów, może być lodowej szersze problemów projektowych, które wymagają wprowadzania zmian w kodzie, aby rozwiązać. 
> 
> Podczas implementowania warunków błędów w tym dokumencie, należy rejestrować i opis kod błędu z powodu omówionych wcześniej przyczyn. Zobacz [błąd i odwołanie do rejestrowania](#error-and-logging-reference) przykłady kodu rejestrowania. 
>

## <a name="acquiretokensilent"></a>AcquireTokenSilent

AcquireTokenSilent podejmie próbę pobrania tokenu z gwarancją, że użytkownik końcowy nie widzi interfejsu użytkownika (UI). Istnieje kilka przypadków, które mogą spowodować awarię dyskretnej pozyskiwania i musi być obsługiwane za pośrednictwem żądań interakcyjnego lub przez domyślny program obsługi. Przejdziemy do szczegółowych informacji, kiedy i jak stosować każdego przypadku w kolejnych sekcjach.

Istnieje zestaw błędy generowane przez system operacyjny, który może wymagać dodanymi komentarzami specyficzne dla aplikacji. Aby uzyskać więcej informacji zawiera sekcja "System operacyjny" błędy w [błąd i odwołanie do rejestrowania](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Scenariusze aplikacji

- [Natywny klient](developer-glossary.md#native-client) aplikacje (iOS, Android, .NET Desktop lub Xamarin)
- [Klient sieci Web](developer-glossary.md#web-client) aplikacje podczas wywoływania [zasobów](developer-glossary.md#resource-server) (.NET)

### <a name="error-cases-and-actionable-steps"></a>W przypadku wystąpienia błędów i kroki z możliwością działania

Zasadniczo istnieją dwa przypadki błędów AcquireTokenSilent:

| Przypadek | Opis |
|------|-------------|
| **Przypadek 1**: Błąd jest rozpoznawalna za pomocą logowania interaktywnego | Błędy spowodowane brakiem prawidłowy tokenów interaktywne żądanie jest konieczne. W szczególności przeszukiwania pamięci podręcznej i token odświeżania nieprawidłowy/wygasły wymagają wywołanie AcquireToken w celu rozwiązania.<br><br>W takich przypadkach użytkownik końcowy musi zostać wyświetlony monit o zalogowanie. Aplikację można wybrać opcję wykonaj żądanie interaktywne natychmiast, po interakcje użytkownika końcowego (na przykład naciśnięcie przycisku logowania) lub nowszej. Wybór zależy od żądane zachowanie aplikacji.<br><br>Zobacz kod w poniższej sekcji, w tym konkretnym przypadku i błędów, które zdiagnozowaniu problemu.|
| **Przypadek 2**: Błąd nie jest rozpoznawalna za pomocą logowania interaktywnego | Sieci i błędy przejściowe/tymczasowa lub inne błędy wykonywanie interaktywne żądania AcquireToken usuną problemu. Niepotrzebne interaktywne monity o logowanie można również frustrować użytkowników końcowych. Biblioteka ADAL automatycznie ponawia pojedynczego ponawiania prób dla większości błędów na AcquireTokenSilent błędów.<br><br>Aplikacja kliencka może również próbę ponowienia w pewnym momencie później, ale kiedy i jak to zrobić zależy sposób działania aplikacji i żądany przez użytkownika końcowego. Na przykład aplikacja może wykonać AcquireTokenSilent ponownych prób po upływie kilku minut lub w odpowiedzi na akcję użytkownika końcowego. Natychmiastowe ponowienie próby spowoduje aplikacji jest ograniczone i nie należy podjąć.<br><br>Kolejnym ponowieniem próby kończy się niepowodzeniem z powodu błędu w tym samym nie oznacza, że klient zrobić interaktywne żądanie przy użyciu AcquireToken, nie rozwiąże ten błąd.<br><br>Zobacz kod w poniższej sekcji, w tym konkretnym przypadku i błędów, które zdiagnozowaniu problemu. |

### <a name="net"></a>.NET

Poniższe wskazówki zawiera przykłady obsługi błędów w połączeniu z metodami biblioteki ADAL: 

- acquireTokenSilentAsync(…)
- acquireTokenSilentSync(…) 
- acquireTokenSilent(...) [przestarzałe]
- acquireTokenByRefreshToken(...) [przestarzałe] 

Twój kod będzie realizowane w następujący sposób:

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

Poniższe wskazówki zawiera przykłady obsługi błędów w połączeniu z metodami biblioteki ADAL: 

- acquireTokenSilentSync(…)
- acquireTokenSilentAsync(...)
- acquireTokenSilent(...) [przestarzałe]

Twój kod będzie realizowane w następujący sposób:

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

Poniższe wskazówki zawiera przykłady obsługi błędów w połączeniu z metodami biblioteki ADAL: 

- acquireTokenSilentWithResource(…)

Twój kod będzie realizowane w następujący sposób:

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

## <a name="acquiretoken"></a>AcquireToken

AcquireToken jest domyślną metodę ADAL używaną do uzyskania tokenów. W przypadku, gdy tożsamość użytkownika jest wymagana AcquireToken próbuje pobrać dyskretnie pierwszy token, a następnie wyświetla interfejsu użytkownika, jeśli to konieczne (chyba że jest przekazywany PromptBehavior.Never). W przypadku, gdy tożsamość aplikacji jest wymagany AcquireToken próbuje pobrać tokenu, ale nie wyświetla interfejsu użytkownika, ponieważ nie ma żadnego użytkownika końcowego. 

Podczas obsługi błędów AcquireToken, obsługa błędów jest zależna od platformy i scenariuszu aplikacja próbuje osiągnąć. 

System operacyjny można również wygenerować zestaw błędów, które wymagają obsługi zależy od konkretnej aplikacji błędów. Aby uzyskać więcej informacji, zobacz "Błędy systemu operacyjnego" w [błąd i odwołanie do rejestrowania](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Scenariusze aplikacji

- Natywne aplikacje klienckie (z systemem iOS, Android, .NET Desktop lub Xamarin)
- Aplikacje sieci Web, które wywołują zasobu interfejsu API (.NET)
- Aplikacje jednej strony (JavaScript)
- Aby usługa aplikacji (.NET, Java)
  - Wszystkie scenariusze, w tym w imieniu z
  - W imieniu użytkownika z określonych scenariuszy

### <a name="error-cases-and-actionable-steps-native-client-applications"></a>W przypadku wystąpienia błędów i kroki z możliwością działania: Natywne aplikacje klienckie

Jeśli tworzysz natywnej aplikacji klienckiej, istnieje kilka przypadków obsługi błędów do rozważenia, które odnoszą się do problemów z siecią, przejściowe awarie i inne błędy specyficzne dla platformy. W większości przypadków aplikacja nie powinna natychmiastowe ponawianie jest przeprowadzane, ale zamiast czekać na interakcje użytkownika końcowego, który monituje logowania. 

Istnieje kilka specjalne przypadki, w których pojedynczy ponownych prób może rozwiązać problem. Na przykład po użytkownik musi włączyć dane na urządzeniu, lub ukończone brokera usługi Azure AD Pobierz po niepowodzeniu początkowej. 

W przypadku awarii aplikacja może powodować interfejsu użytkownika, aby zezwolić na użytkownika końcowego do wykonywania niektórych interakcji, który monituje ponowienie próby. Na przykład jeśli urządzenia nie powiodła się dla błędu w trybie offline, przycisk "Spróbuj ponownie zarejestrować" monitowania AcquireToken ponów próbę wykonania zamiast natychmiastowe ponawianie próby awarii. 

Obsługa błędów w natywnych aplikacjach można zdefiniować w dwóch przypadkach:

|  |  |
|------|-------------|
| **Przypadek 1**:<br>Błąd niepowtarzający (w większości przypadków) | 1. Nie należy podejmować natychmiastowe ponowienie próby. Przedstawia użytkowników końcowych, interfejsu użytkownika oparta na konkretny błąd, który wywołuje ponawiania ("Spróbuj ponownie zaloguj się", "Aplikacja brokera pobieranie programu Azure AD" itp.). |
| **Przypadek 2**:<br>Błąd umożliwiający ponowienie próby | 1. Pojedynczy ponawiania należy wykonać, ponieważ użytkownik końcowy może wprowadzono stanu, który skutkuje sukcesu.<br><br>2. W przypadku niepowodzenia ponownych prób przedstawia użytkowników końcowych, interfejsu użytkownika oparta na konkretny błąd, który wywołuje ponawiania ("Spróbuj ponownie zaloguj się", "Pobieranie programu Azure AD aplikacji brokera" itp.). |

> [!IMPORTANT]
> Jeśli konto użytkownika jest przekazywany do biblioteki ADAL w dyskretnej wywołania i kończy się niepowodzeniem, kolejne żądanie interaktywne umożliwia użytkownikowi zalogowanie się przy użyciu innego konta. Po pomyślnym AcquireToken, przy użyciu konta użytkownika aplikacja musi Sprawdź, czy aplikacje użytkownika lokalnego obiektu pasuje do zalogowanego użytkownika. Niezgodność nie generuje wyjątek (z wyjątkiem w języku Objective C), ale powinny być traktowane w przypadkach, w którym użytkownik jest znana lokalnie przed żądania uwierzytelniania (np. wywołanie nie powiodło się dyskretnym).
>

#### <a name="net"></a>.NET

Poniższe wskazówki zawiera przykłady obsługi błędów w połączeniu z wszystkich innych silent AcquireToken(...) Metody biblioteki ADAL, *z wyjątkiem*: 

- AcquireTokenAsync(…, IClientAssertionCertification, …)
- AcquireTokenAsync(…,ClientCredential, …)
- AcquireTokenAsync (..., ClientAssertion,...)
- AcquireTokenAsync(...,UserAssertion,...)   

Twój kod będzie realizowane w następujący sposób:

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
> Biblioteki ADAL platformy .NET ma dodatkowe kwestia obsługuje PromptBehavior.Never, która ma takie AcquireTokenSilent zachowanie.
>

Poniższe wskazówki zawiera przykłady obsługi błędów w połączeniu z metodami biblioteki ADAL: 

- acquireToken(…, PromptBehavior.Never)

Twój kod będzie realizowane w następujący sposób:

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

Poniższe wskazówki zawiera przykłady obsługi błędów w połączeniu z wszystkich innych silent AcquireToken(...) Metody biblioteki ADAL. 

Twój kod będzie realizowane w następujący sposób:

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

Poniższe wskazówki zawiera przykłady obsługi błędów w połączeniu z wszystkich innych silent AcquireToken(...) Metody biblioteki ADAL. 

Twój kod będzie realizowane w następujący sposób:

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

### <a name="error-cases-and-actionable-steps-web-applications-that-call-a-resource-api-net"></a>W przypadku wystąpienia błędów i kroki z możliwością działania: Aplikacje sieci Web, które wywołują zasobu interfejsu API (.NET)

Jeśli tworzysz aplikacji internetowej platformy .NET, która wywołuje pobiera token za pomocą kodu autoryzacji dla zasobu, tylko kod wymagany jest domyślny program obsługi w przypadku ogólnych. 

Poniższe wskazówki zawiera przykłady obsługi błędów w połączeniu z metodami biblioteki ADAL: 

- AcquireTokenByAuthorizationCodeAsync(…)

Twój kod będzie realizowane w następujący sposób:

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

### <a name="error-cases-and-actionable-steps-single-page-applications-adaljs"></a>W przypadku wystąpienia błędów i kroki z możliwością działania: Aplikacje jednej strony (adal.js)

Jeśli tworzysz aplikacji jednostronicowej przy adal.js AcquireToken błąd kodu obsługującego przypomina ten typowy wywołania dyskretnej. W szczególności w adal.js AcquireToken nigdy nie zostanie wyświetlony interfejs użytkownika. 

Nie powiodło się AcquireToken ma następujących przypadkach:

|  |  |
|------|-------------|
| **Przypadek 1**:<br>Możliwej do rozpoznania z żądaniem interaktywne | 1. W przypadku niepowodzenia: login() nie należy wykonywać natychmiastowe ponowienie próby. Ponów próbę tylko po akcji przez użytkownika wyświetla monit o ponowienie próby.|
| **Przypadek 2**:<br>Nie Resolvable z żądaniem interaktywne. Błąd jest powtarzający operację. | 1. Pojedynczy ponawiania należy wykonać, ponieważ główne użytkownika końcowego wprowadzono stanu, który skutkuje sukcesu.<br><br>2. W przypadku niepowodzenia ponownych prób dostarczyć użytkownikowi z akcją oparte na konkretny błąd, który można wywołać ponowna próba ("Spróbuj zalogować się ponownie"). |
| **Przypadek 3**:<br>Nie Resolvable z żądaniem interaktywne. Błąd nie jest powtarzający operację. | 1. Nie należy podejmować natychmiastowe ponowienie próby. Obecne użytkownika końcowego z akcją oparte na konkretny błąd, który można wywołać ponowna próba ("Spróbuj zalogować się ponownie"). |

Twój kod będzie realizowane w następujący sposób:

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

### <a name="error-cases-and-actionable-steps-service-to-service-applications-net-only"></a>W przypadku wystąpienia błędów i kroki z możliwością działania: Usługa Usługa aplikacji (tylko platforma .NET)

Jeśli tworzysz aplikacji do usługi, która używa AcquireToken, istnieje kilka błędów kluczy, który musi obsługiwać kodu. Tylko odwołanie się do niepowodzenia jest zwrócenie błędu do wywoływania aplikacji (dla przypadków o imieniu z) lub zastosować strategia ponawiania prób. 

#### <a name="all-scenarios"></a>Wszystkie scenariusze

Aby uzyskać *wszystkich* scenariuszy aplikacji do usługi, w tym w imieniu z:

- Nie należy podejmować natychmiastowe ponowienie próby. Żądań zakończonych niepowodzeniem prób biblioteki ADAL, jeden dla niektórych ponów próbę wykonania. 
- Tylko nadal ponowieniem próby wykonania akcji użytkownika lub aplikacji po monity o ponowienie próby. Na przykład aplikacji demona, który działa na niektórych Ustaw interwał powinien zaczekać następnym interwale czasowym, aby spróbować ponownie.

Poniższe wskazówki zawiera przykłady obsługi błędów w połączeniu z metodami biblioteki ADAL: 

- AcquireTokenAsync(…, IClientAssertionCertification, …)
- AcquireTokenAsync(…,ClientCredential, …)
- AcquireTokenAsync (..., ClientAssertion,...)
- AcquireTokenAsync (..., UserAssertion,...)

Twój kod będzie realizowane w następujący sposób:

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

#### <a name="on-behalf-of-scenarios"></a>Scenariusze w imieniu z

Aby uzyskać *w imieniu z* scenariuszy aplikacji do usługi.

Poniższe wskazówki zawiera przykłady obsługi błędów w połączeniu z metodami biblioteki ADAL: 

- AcquireTokenAsync (..., UserAssertion,...)

Twój kod będzie realizowane w następujący sposób:

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

Utworzyliśmy [pełny przykład](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) pokazuje, w tym scenariuszu.

## <a name="error-and-logging-reference"></a>Odwołanie do rejestrowania błędów i

### <a name="logging-personal-identifiable-information-pii--organizational-identifiable-information-oii"></a>Rejestrowanie osobowe (PII) i organizacyjnych danych osobowych (OII)
Domyślnie rejestrowanie biblioteki ADAL przechwytywania lub nie rejestrować wszelkie dane osobowe lub OII. Biblioteka pozwala deweloperom aplikacji ją włączyć za pomocą metody ustawiającej w klasie rejestratora. Włączając dane osobowe lub OII, aplikacja odpowiada za bezpiecznie obsługiwać bardzo poufnych danych i spełniają wszelkie wymagania prawne.

### <a name="net"></a>.NET

#### <a name="adal-library-errors"></a>Błędy biblioteki ADAL

Aby zapoznać się z określonych błędów biblioteki ADAL, kod źródłowy w [repozytorium azure-activedirectory biblioteki for-dotnet](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/8f6d560fbede2247ec0e217a21f6929d4375dcaa/src/ADAL.PCL/Utilities/Constants.cs#L58) jest najlepsze odwołanie błędu.

#### <a name="guidance-for-error-logging-code"></a>Wskazówki dotyczące kod błędu logowania:

Zmiany logowania w programie .NET biblioteki ADAL w zależności od platformy, w której są wykonywane prace. Zapoznaj się [wiki rejestrowania](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Logging-in-ADAL.Net) kodu o sposobach włączania rejestrowania zdarzeń.

### <a name="android"></a>Android

#### <a name="adal-library-errors"></a>Błędy biblioteki ADAL

Aby zapoznać się z określonych błędów biblioteki ADAL, kod źródłowy w [repozytorium azure-activedirectory-library-for-android](https://github.com/AzureAD/azure-activedirectory-library-for-android/blob/dev/adal/src/main/java/com/microsoft/aad/adal/ADALError.java#L33) jest najlepsze odwołanie błędu.

#### <a name="operating-system-errors"></a>Błędy systemu operacyjnego

Dla systemu android błędy systemu operacyjnego są udostępniane za pośrednictwem authenticationexception — w bibliotece ADAL, są oznaczone jako "SERVER_INVALID_REQUEST" i może być dodatkowo szczegółową za pośrednictwem opisów błędów. 

Aby uzyskać pełną listę typowych błędów i jakie kroki do wykonania w przypadku aplikacji lub użytkownicy końcowi je napotka dotyczą [biblioteki ADAL dla systemu Android Wiki](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki). 

#### <a name="guidance-for-error-logging-code"></a>Wskazówki dotyczące kod błędu logowania:

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

Aby zapoznać się z określonych błędów biblioteki ADAL, kod źródłowy w [repozytorium azure-activedirectory biblioteki do objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc/blob/dev/ADAL/src/ADAuthenticationError.m#L295) jest najlepsze odwołanie błędu.

#### <a name="operating-system-errors"></a>Błędy systemu operacyjnego

błędy z systemem iOS mogą wystąpić podczas logowania, gdy użytkownicy używają widoki sieci web i rodzaju uwierzytelniania. Może to być spowodowane warunkami, takie jak błędy protokołu SSL, przekroczenia limitu czasu lub błędy sieci:

- Uprawnienia udostępniania identyfikatory logowania nie są trwałe i pamięci podręcznej pojawi się pusta. Można rozwiązać, dodając następujący wiersz kodu do pęku kluczy: `[[ADAuthenticationSettings sharedInstance] setSharedCacheKeychainGroup:nil];`
- Aby uzyskać zestaw NsUrlDomain błędy, zmiany akcję, zależnie od logiki aplikacji. Zobacz [dokumentację referencyjną NSURLErrorDomain](https://developer.apple.com/documentation/foundation/nsurlerrordomain#declarations) dla konkretnych wystąpień, które są obsługiwane.
- Zobacz [ADAL typowych problemów z Obj C](https://github.com/AzureAD/azure-activedirectory-library-for-objc#adauthenticationerror) listę typowych błędów utrzymywane przez zespół usługi ADAL języka Objective-C.

#### <a name="guidance-for-error-logging-code"></a>Wskazówki dotyczące kod błędu logowania:

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

### <a name="guidance-for-error-logging-code---javascript"></a>Wskazówki dotyczące rejestrowania błędów kodu — JavaScript 

```javascript
0: Error1: Warning2: Info3: Verbose
window.Logging = {
    level: 3,
    log: function (message) {
        console.log(message);
    }
};
```
## <a name="related-content"></a>Powiązana zawartość

* [Przewodnik dewelopera usługi Azure AD][AAD-Dev-Guide]
* [Biblioteki uwierzytelniania usługi Azure AD][AAD-Auth-Libraries]
* [Scenariusze uwierzytelniania usługi Azure AD][AAD-Auth-Scenarios]
* [Integrowanie aplikacji z usługą Azure Active Directory][AAD-Integrating-Apps]

Użyj sekcji komentarzy, poniżej, aby przekazać opinie i Pomóż nam analizy i połącz kształt naszej zawartości.

[![Zaloguj się w przycisk][AAD-Sign-In]][AAD-Sign-In]
<!--Reference style links -->

[AAD-Auth-Libraries]: ./active-directory-authentication-libraries.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AZURE-portal]: https://portal.azure.com

<!--Image references-->
[AAD-Sign-In]:./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png

