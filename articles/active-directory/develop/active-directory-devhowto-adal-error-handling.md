---
title: Błąd podczas obsługi najlepsze rozwiązania dla klientów interfejsów Azure Active Directory Authentication Library (ADAL)
description: Zapewnia obsługę wskazówek i najlepszych rozwiązań dla aplikacji klienckich ADAL błędów.
services: active-directory
documentationcenter: ''
author: danieldobalian
manager: mtillman
ms.author: celested
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.custom: ''
ms.openlocfilehash: 27315262ff64b640acc3af16a26fc3887d852a00
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="error-handling-best-practices-for-azure-active-directory-authentication-library-adal-clients"></a>Błąd podczas obsługi najlepsze rozwiązania dla klientów interfejsów Azure Active Directory Authentication Library (ADAL)

Ten artykuł zawiera wskazówki dotyczące typu błędów, że deweloperzy mogą wystąpić, gdy używa biblioteki ADAL do uwierzytelniania użytkowników. Podczas korzystania z biblioteki ADAL, istnieje kilka przypadków, w których deweloper może wymagać Wkrocz do obsługi błędów. Obsługa błędów prawidłowego zapewnia dużą końcowego i ogranicza liczbę razy, który użytkownik końcowy musi się zalogować.

W tym artykule firma Microsoft Poznaj szczególnych przypadkach dla każdej platformy obsługiwane przez ADAL i jak aplikacja może obsłużyć każdego przypadku poprawnie. Wskazówki dotyczące błędu jest podzielony na dwie kategorie szerszych, na podstawie wzorców nabycia token dostarczony przez interfejsy API biblioteki ADAL:

- **AcquireTokenSilent**: klient próbuje uzyskać token w trybie dyskretnym (bez interfejsu użytkownika) i może się nie powieść, jeśli nie powiedzie się biblioteki ADAL. 
- **AcquireToken**: klient może spróbować dyskretnej przejęcia, ale można również wykonywać interaktywnych żądań, które wymagają logowania.

> [!TIP]
> Jest dobrym pomysłem jest gdy, są rejestrowane wszystkie błędy i wyjątki przy użyciu biblioteki ADAL i Azure AD. Dzienniki nie są tylko przydatne dla zrozumienia ogólnej kondycji aplikacji, ale są równie ważne podczas debugowania szerszych problemów. Gdy aplikacja może odzyskać z niektórych błędów, mogą one wskazówki w szerszym problemów projektu, które wymagają zmiany kodu w celu rozwiązania. 
> 
> Podczas implementowania warunki błędów w tym dokumencie, kod błędu i opis powinien dziennika z powodu omówionych wcześniej przyczyn. Zobacz [błąd i rejestrowanie odwołanie](#error-and-logging-reference) przykłady kodu rejestrowania. 
>

## <a name="acquiretokensilent"></a>AcquireTokenSilent

AcquireTokenSilent podejmuje próbę pobrania tokenu z gwarancji, że użytkownik nie widzi interfejsu użytkownika (UI). Istnieje kilka przypadków, w którym dyskretnej nabycia może zakończyć się niepowodzeniem i musi być obsługiwane za pośrednictwem interaktywnych żądań lub przez domyślną procedurę obsługi. Firma Microsoft Poznaj szczegółowe informacje, kiedy i jak stosować każdorazowo w kolejnych sekcjach.

Istnieje zestaw o błędach wygenerowanych przez system operacyjny, który może wymagać błąd obsługi specyficzne dla aplikacji. Aby uzyskać więcej informacji, zobacz sekcję błędy "System operacyjny" w [błąd i rejestrowanie odwołanie](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Scenariusze aplikacji

- [Aplikacja Native client](active-directory-dev-glossary.md#native-client) aplikacji (z systemem iOS, Android, .NET pulpitu i Xamarin)
- [Klient sieci Web](active-directory-dev-glossary.md#web-client) aplikacji podczas wywoływania [zasobów](active-directory-dev-glossary.md#resource-server) (.NET)

### <a name="error-cases-and-actionable-steps"></a>W przypadku wystąpienia błędów i kroki można wykonać

Zasadniczo istnieją dwa przypadki AcquireTokenSilent błędów:

| Przypadek | Opis |
|------|-------------|
| **Przypadek 1**: błąd jest rozpoznawalna przy interakcyjnego logowania | Błędy spowodowane brakiem prawidłowych tokenów interakcyjne żądanie jest konieczne. W szczególności wyszukiwania w pamięci podręcznej i token odświeżania nieprawidłowy/wygasły wymagają wywołanie AcquireToken do rozpoznania.<br><br>W takich przypadkach użytkownik końcowy musi zostać wyświetlony monit o zalogowanie. Aplikację można czy interaktywnych żądań natychmiast, po interakcje użytkownika końcowego (na przykład naciśnięcie przycisku logowania) lub nowszym. Wybór zależy od żądanego zachowania aplikacji.<br><br>Zobacz kod w poniższej sekcji tym konkretnym przypadku i błędy, które diagnozowanie go.|
| **Przypadek 2**: błąd nie jest rozpoznawalna przy interakcyjnego logowania | Sieci i przejściowy/tymczasowe błędy lub inne błędy wykonywanie interakcyjne żądania AcquireToken nie rozwiązać problem. Niepotrzebne interakcyjnego logowania monity również może frustrować użytkowników końcowych. Biblioteka ADAL automatycznie podejmie pojedynczego ponownych prób dla większości błędów na AcquireTokenSilent błędów.<br><br>Aplikacja kliencka może również podejmować ponowna próba w pewnym momencie nowsze, ale kiedy i jak to zrobić, jest zależna od zachowanie aplikacji oraz żądany przez użytkownika końcowego. Na przykład aplikacja może wykonać AcquireTokenSilent spróbuj ponownie za kilka minut lub w odpowiedzi na akcję użytkownika końcowego. Natychmiastowego ponawiania spowoduje aplikacja ograniczane i nie są sprawdzane.<br><br>Kolejne ponowna próba niepowodzeniem z powodu błędu tego samego oznacza to, że klient zrobić interakcyjne żądania przy użyciu AcquireToken, nie jest rozpoznawany błędu.<br><br>Zobacz kod w poniższej sekcji tym konkretnym przypadku i błędy, które diagnozowanie go. |

### <a name="net"></a>.NET

Przedstawionych w tym temacie przedstawiono przykłady obsługi błędów w połączeniu z metodami ADAL: 

- acquireTokenSilentAsync(…)
- acquireTokenSilentSync(…) 
- acquireTokenSilent(...) [przestarzałe]
- acquireTokenByRefreshToken(...) [przestarzałe] 

Kod będzie realizowane w następujący sposób:

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

Przedstawionych w tym temacie przedstawiono przykłady obsługi błędów w połączeniu z metodami ADAL: 

- acquireTokenSilentSync(…)
- acquireTokenSilentAsync(...)
- acquireTokenSilent(...) [przestarzałe]

Kod będzie realizowane w następujący sposób:

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

Przedstawionych w tym temacie przedstawiono przykłady obsługi błędów w połączeniu z metodami ADAL: 

- acquireTokenSilentWithResource(…)

Kod będzie realizowane w następujący sposób:

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

AcquireToken jest domyślną metodę ADAL używaną do uzyskania tokenów. W przypadku, gdy tożsamość użytkownika jest wymagane AcquireToken próbuje pobrać token pierwszy w trybie dyskretnym, a następnie wyświetla interfejsu użytkownika, w razie potrzeby (o ile nie została przekazana PromptBehavior.Never). W przypadku, gdy tożsamość aplikacji jest wymagany AcquireToken podejmuje próbę pobrania tokenu, ale nie wyświetla interfejsu użytkownika, ponieważ nie ma żadnego użytkownika końcowego. 

Podczas obsługi błędów AcquireToken, obsługa błędów jest zależny od platformy i próbuje uzyskać scenariusz aplikacji. 

System operacyjny można również tworzyć zestaw błędów, które wymagają obsługi zależne od określonych aplikacji błędów. Aby uzyskać więcej informacji, zobacz "Błędy systemu operacyjnego" w [błąd i rejestrowanie odwołanie](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Scenariusze aplikacji

- Aplikacja Native client aplikacji (z systemem iOS, Android, .NET pulpitu i Xamarin)
- Aplikacje sieci Web, które wywołują zasobu interfejsu API (.NET)
- Aplikacje jednej strony (JavaScript)
- Aplikacje Service-to-Service (.NET, Java)
  - Wszystkie scenariusze, w tym w imieniu — z
  - W imieniu — z określonych scenariuszy

### <a name="error-cases-and-actionable-steps-native-client-applications"></a>W przypadku wystąpienia błędów i kroki można wykonać: aplikacje Native client

Jeśli tworzysz aplikację native client, istnieje kilka przypadków obsługi błędu wziąć pod uwagę, które dotyczą problemów z siecią, błędów przejściowych i inne błędy specyficzne dla platformy. W większości przypadków aplikacji nie należy wykonywać natychmiastowego ponownych prób, ale zamiast czekać interakcje użytkownika końcowego, które monituje logowania. 

Istnieje kilka szczególnych przypadkach, w których pojedynczy ponowna próba może rozwiązać problem. Na przykład po użytkownik odpowiada za włączanie danych na urządzeniu, lub ukończona brokera usługi Azure AD Pobierz po niepowodzeniu początkowej. 

W przypadku awarii aplikacja może ona interfejsu użytkownika, aby umożliwić użytkownikom końcowym wykonywać niektórych interakcji, które monituje ponowna próba. Na przykład jeśli urządzenia nie powiodła się dla błędu w trybie offline, monitowania AcquireToken przycisk "Spróbuj zalogować się ponownie" Ponów zamiast natychmiast ponawianie próby awarii. 

Obsługa błędów w natywnych aplikacji można definiować w obu przypadkach:

|  |  |
|------|-------------|
| **Przypadek 1**:<br>Błąd niepowtarzający (w większości przypadków) | 1. Nie należy podejmować natychmiastowego ponawiania. Przedstawia użytkowników końcowych, interfejsu użytkownika oparta na błędu, który wywołuje ponowna próba ("Ponów logowania", "Pobieranie programu Azure AD brokera aplikacji", itp.). |
| **Przypadek 2**:<br>Błąd powtarzający operację | 1. Wykonaj jeden ponownych prób jako użytkownik końcowy może wprowadzono stanie, który powoduje sukcesu.<br><br>2. W przypadku niepowodzenia ponownych prób przedstawia użytkowników końcowych, interfejsu użytkownika oparta na błędu, który wywołuje ponowna próba ("Spróbuj ponownie zaloguj się", "Aplikacja brokera pobieranie programu Azure AD" itp.). |

> [!IMPORTANT]
> Jeśli konto użytkownika zostanie przekazany do biblioteki ADAL w trybie dyskretnym wywołania i kończy się niepowodzeniem, kolejne żądania interakcyjne umożliwia użytkownikowi końcowemu zalogować się przy użyciu innego konta. Po pomyślnym AcquireToken, używając konta użytkownika aplikacji należy sprawdzić, czy zalogowany użytkownik odpowiada obiektu użytkownika lokalnego aplikacji. Niezgodność nie generuje wyjątku (z wyjątkiem w języku Objective C), ale powinny być traktowane w przypadkach, gdy użytkownik jest znana lokalnie przed żądania uwierzytelniania (na przykład nie powiodło się wywołanie dyskretnej).
>

#### <a name="net"></a>.NET

Przedstawionych w tym temacie przedstawiono przykłady obsługi błędów w połączeniu z wszystkich innych niż silent AcquireToken(...) Metody ADAL *z wyjątkiem*: 

- AcquireTokenAsync(…, IClientAssertionCertification, …)
- AcquireTokenAsync(…,ClientCredential, …)
- AcquireTokenAsync (..., ClientAssertion,...)
- AcquireTokenAsync(...,UserAssertion,...)   

Kod będzie realizowane w następujący sposób:

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
> ADAL .NET ma dodatkowe brany pod uwagę, jak PromptBehavior.Never, którego zachowanie jest takie jak AcquireTokenSilent.
>

Przedstawionych w tym temacie przedstawiono przykłady obsługi błędów w połączeniu z metodami ADAL: 

- acquireToken(…, PromptBehavior.Never)

Kod będzie realizowane w następujący sposób:

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

Przedstawionych w tym temacie przedstawiono przykłady obsługi błędów w połączeniu z wszystkich innych niż silent AcquireToken(...) Metody ADAL. 

Kod będzie realizowane w następujący sposób:

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

Przedstawionych w tym temacie przedstawiono przykłady obsługi błędów w połączeniu z wszystkich innych niż silent AcquireToken(...) Metody ADAL. 

Kod będzie realizowane w następujący sposób:

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

### <a name="error-cases-and-actionable-steps-web-applications-that-call-a-resource-api-net"></a>W przypadku wystąpienia błędów i kroki można wykonać: aplikacji, które wywołują zasobu interfejsu API (.NET)

Jeśli tworzysz aplikację sieci web .NET, która wywołuje pobiera tokenu przy użyciu kodu autoryzacji do zasobu, tylko kod wymagany jest domyślny program obsługi w przypadku ogólnych. 

Przedstawionych w tym temacie przedstawiono przykłady obsługi błędów w połączeniu z metodami ADAL: 

- AcquireTokenByAuthorizationCodeAsync(…)

Kod będzie realizowane w następujący sposób:

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

### <a name="error-cases-and-actionable-steps-single-page-applications-adaljs"></a>W przypadku wystąpienia błędów i kroki można wykonać: aplikacje jednostronicowe (adal.js)

Jeśli tworzysz aplikację jednej strony, przy użyciu adal.js z AcquireToken błąd kod obsługi jest podobny do typowych wywołania dyskretnej. W szczególności w adal.js AcquireToken nigdy nie zostanie wyświetlony interfejs użytkownika. 

AcquireToken nie powiodło się ma w następujących przypadkach:

|  |  |
|------|-------------|
| **Przypadek 1**:<br>Rozpoznawalną z żądaniem interakcyjne | 1. W przypadku niepowodzenia: login() nie wykonuj natychmiastowego ponawiania. Tylko ponownie po akcji użytkownika wyświetla monit o ponowienie próby.|
| **Przypadek 2**:<br>Nie Resolvable z żądaniem interaktywnego. Błąd jest powtarzający operację. | 1. Jako użytkownik końcowy główne zostały wprowadzone stanie, który powoduje sukcesu, należy wykonać pojedynczego ponów próbę.<br><br>2. W przypadku niepowodzenia ponownych prób dostarczyć użytkownikowi za pomocą działania oparte na błędu, który można wywołać ponowna próba ("Spróbuj zalogować się ponownie"). |
| **Przypadek 3**:<br>Nie Resolvable z żądaniem interaktywnego. Błąd nie jest powtarzający operację. | 1. Nie należy podejmować natychmiastowego ponawiania. Bieżąca użytkownika końcowego z czynności w oparciu o błędu, który można wywołać ponowna próba ("Spróbuj zalogować się ponownie"). |

Kod będzie realizowane w następujący sposób:

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

### <a name="error-cases-and-actionable-steps-service-to-service-applications-net-only"></a>W przypadku wystąpienia błędów i kroki można wykonać: service-to-service aplikacji (tylko .NET)

Jeśli tworzysz aplikacji do usługi, która używa AcquireToken istnieje kilka błędów kluczy, który musi obsługiwać kodu. Tylko odwołanie do wystąpienia błędu jest zwrócenie błędu powrót do wywoływania aplikacji (dla przypadków o imieniu z) lub Zastosuj strategii ponów próbę. 

#### <a name="all-scenarios"></a>Wszystkie scenariusze

Aby uzyskać *wszystkie* scenariuszy aplikacji do usługi, w tym w imieniu — z:

- Nie należy podejmować natychmiastowego ponawiania. ADAL prób pojedynczy ponów dla niektórych niepomyślnych żądań. 
- Nadal ponowieniem próby wykonania akcji użytkownika lub aplikacji po monity ponowienie próby. Na przykład aplikacji demon, która działa na pewnego interwału zestaw należy czekać do momentu następnym interwale czasowym, aby ponowić próbę.

Przedstawionych w tym temacie przedstawiono przykłady obsługi błędów w połączeniu z metodami ADAL: 

- AcquireTokenAsync(…, IClientAssertionCertification, …)
- AcquireTokenAsync(…,ClientCredential, …)
- AcquireTokenAsync (..., ClientAssertion,...)
- AcquireTokenAsync (..., UserAssertion,...)

Kod będzie realizowane w następujący sposób:

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

#### <a name="on-behalf-of-scenarios"></a>Scenariusze w imieniu — z

Aby uzyskać *w imieniu — z* scenariuszy aplikacji do usługi.

Przedstawionych w tym temacie przedstawiono przykłady obsługi błędów w połączeniu z metodami ADAL: 

- AcquireTokenAsync (..., UserAssertion,...)

Kod będzie realizowane w następujący sposób:

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

Firma Microsoft został skompilowany [kompletnego przykładu](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) pokazano, w tym scenariuszu.

## <a name="error-and-logging-reference"></a>Odwołanie do rejestrowania błędów i

### <a name="logging-personal-identifiable-information-pii--organizational-identifiable-information-oii"></a>Dane osobowe rejestrowania (dane osobowe) i informacje umożliwiające identyfikację użytkowników w organizacji (OII)
Domyślnie rejestrowanie ADAL przechwytywania lub nie rejestrować wszystkie dane osobowe lub OII. Biblioteka umożliwia deweloperom aplikacji włączenie tej funkcji za pomocą metody ustawiającej klasy rejestratora. Przez włączenie dane osobowe lub OII, aplikacja bierze odpowiedzialność za bezpiecznie Obsługa bardzo ważne dane i wszelkich wymagań przepisami.

### <a name="net"></a>.NET

#### <a name="adal-library-errors"></a>Błędy biblioteki ADAL

Aby zapoznać się z określonych błędów ADAL kodu źródłowego w [repozytorium azure-activedirectory — Biblioteka do dotnet](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/8f6d560fbede2247ec0e217a21f6929d4375dcaa/src/ADAL.PCL/Utilities/Constants.cs#L58) najlepsze numer błędu.

#### <a name="guidance-for-error-logging-code"></a>Wskazówki dotyczące kod błędu rejestrowania:

Rejestrowanie ADAL .NET zmian w zależności od platformy wykorzystywanej na. Zapoznaj się [wiki rejestrowania](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Logging-in-ADAL.Net) dla kodu na temat włączania rejestrowania.

### <a name="android"></a>Android

#### <a name="adal-library-errors"></a>Błędy biblioteki ADAL

Aby zapoznać się z określonych błędów ADAL kodu źródłowego w [repozytorium azure-activedirectory — Biblioteka for-android](https://github.com/AzureAD/azure-activedirectory-library-for-android/blob/dev/adal/src/main/java/com/microsoft/aad/adal/ADALError.java#L33) najlepsze numer błędu.

#### <a name="operating-system-errors"></a>Błędy systemu operacyjnego

Android błędy systemu operacyjnego są dostępne za pośrednictwem authenticationexception — w ADAL, są oznaczone jako "SERVER_INVALID_REQUEST" i może być dalsze szczegółowe za pośrednictwem opisów błędów. 

Aby uzyskać pełną listę typowych błędów i jakie kroki w sytuacji, gdy aplikację lub użytkownicy końcowi napotkania ich odwoływać się do [ADAL dla systemu Android typu Wiki](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki). 

#### <a name="guidance-for-error-logging-code"></a>Wskazówki dotyczące kod błędu rejestrowania:

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

Aby zapoznać się z określonych błędów ADAL kodu źródłowego w [repozytorium azure-activedirectory — Biblioteka do objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc/blob/dev/ADAL/src/ADAuthenticationError.m#L295) najlepsze numer błędu.

#### <a name="operating-system-errors"></a>Błędy systemu operacyjnego

iOS błędy mogą wystąpić podczas podczas logowania, gdy użytkownicy używają widoki sieci web i rodzaj uwierzytelniania. Może to być spowodowane warunków, takich jak błędy protokołu SSL, limity czasu lub błędy sieci:

- Uprawnienia udostępniania logowania nie są trwałe i pamięci podręcznej jest pusta. Można rozwiązać, dodając następujący wiersz kodu do łańcucha kluczy: `[[ADAuthenticationSettings sharedInstance] setSharedCacheKeychainGroup:nil];`
- Dla zestawu NsUrlDomain błędów akcji zmienia się zależnie od logiki aplikacji. Zobacz [NSURLErrorDomain dokumentacji](https://developer.apple.com/documentation/foundation/nsurlerrordomain#declarations) dla określonych wystąpień, które są obsługiwane.
- Zobacz [ADAL Obj C typowych problemów z](https://github.com/AzureAD/azure-activedirectory-library-for-objc#adauthenticationerror) listę typowych błędów obsługiwanego przez zespół ADAL Objective-C.

#### <a name="guidance-for-error-logging-code"></a>Wskazówki dotyczące kod błędu rejestrowania:

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

### <a name="guidance-for-error-logging-code---javascript"></a>Wskazówki dotyczące rejestrowania błędów kodu - JavaScript 

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

* [Przewodnik dewelopera usługi Azure AD][AAD-Dev-Guide]
* [Biblioteki uwierzytelniania usługi Azure AD][AAD-Auth-Libraries]
* [Scenariusze uwierzytelniania usługi Azure AD][AAD-Auth-Scenarios]
* [Integrowanie aplikacji z usługą Azure Active Directory][AAD-Integrating-Apps]

Użyj sekcji komentarze poniżej, aby przekazać opinie i pomóc nam dostosować i kształtu zawartość.

[![Zaloguj się przycisk][AAD-Sign-In]][AAD-Sign-In]
<!--Reference style links -->
[AAD-Auth-Libraries]: ./active-directory-authentication-libraries.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AZURE-portal]: https://portal.azure.com

<!--Image references-->
[AAD-Sign-In]:./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png

