---
title: Inicjuj aplikacje klienckie (Biblioteka uwierzytelniania firmy Microsoft dla języka JavaScript)
titleSuffix: Microsoft identity platform
description: Informacje na temat inicjowania aplikacji klienckich przy użyciu biblioteki uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL. js).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c4cd3e2ab18c05c8adfda1a54ea4f7820916eab5
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934884"
---
# <a name="initialize-client-applications-using-msaljs"></a>Inicjuj aplikacje klienckie przy użyciu MSAL. js
W tym artykule opisano inicjowanie biblioteki uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL. js) przy użyciu wystąpienia aplikacji agent użytkownika. Aplikacja agent użytkownika jest formą publicznej aplikacji klienckiej, w której kod klienta jest wykonywany w agencie użytkownika, takim jak przeglądarka sieci Web. Ci klienci nie przechowują wpisów tajnych, ponieważ kontekst przeglądarki jest otwarty. Aby dowiedzieć się więcej na temat typów aplikacji klienta i opcji konfiguracji aplikacji, zapoznaj się z [omówieniem](msal-client-applications.md).

## <a name="prerequisites"></a>Wymagania wstępne
Przed zainicjowaniem aplikacji należy najpierw [zarejestrować ją w Azure Portal](scenario-spa-app-registration.md) , aby umożliwić integrację aplikacji z platformą tożsamości firmy Microsoft. Po zarejestrowaniu mogą być potrzebne następujące informacje (które można znaleźć w Azure Portal):

- Identyfikator klienta (ciąg reprezentujący identyfikator GUID dla aplikacji)
- Adres URL dostawcy tożsamości (nazywany wystąpieniem) i odbiorcy logowania dla aplikacji. Te dwa parametry są określane zbiorczo jako urząd.
- Identyfikator dzierżawy, jeśli piszesz aplikację biznesową wyłącznie dla Twojej organizacji (nazywaną również aplikacją z jedną dzierżawą).
- W przypadku usługi Web Apps trzeba również ustawić redirectUri, w którym dostawca tożsamości zwróci się do aplikacji przy użyciu tokenów zabezpieczających.

## <a name="initializing-applications"></a>Inicjowanie aplikacji

Można użyć MSAL. js w następujący sposób w zwykłej aplikacji JavaScript/TypeScript. Inicjowanie kontekstu uwierzytelniania MSAL przez utworzenie wystąpienia `UserAgentApplication` z obiektem konfiguracji. Minimalną wymaganą konfiguracją do zainicjowania MSAL. js jest clientID aplikacji, którą należy uzyskać z portalu rejestracji aplikacji.

W przypadku metod uwierzytelniania z przepływami przekierowania (`loginRedirect` i `acquireTokenRedirect`) należy jawnie zarejestrować wywołanie zwrotne dla sukcesu lub błędu za pomocą metody `handleRedirectCallback()`. Jest to wymaganie, ponieważ przepływy przekierowania nie zwracają niesie obietnice zwiększenia jako metod z wyskakującym doświadczeniem.

```javascript
// Configuration object constructed
const config = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
    }
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

// (optional when using redirect methods) register redirect call back for Success or Error
myMSALObj.handleRedirectCallback(authCallback);
```

MSAL. js ma takie samo wystąpienie i konfigurację `UserAgentApplication` do reprezentowania jednego kontekstu uwierzytelniania. Nie zaleca się stosowania wielu wystąpień, ponieważ powodują one konflikt wpisów w pamięci podręcznej i zachowanie w przeglądarce.

## <a name="configuration-options"></a>Opcje konfiguracji

MSAL. js zawiera poniższy obiekt konfiguracji, który oferuje grupę konfigurowalnych opcji dostępnych do tworzenia wystąpienia `UserAgentApplication`.

```javascript
type storage = "localStorage" | "sessionStorage";

// Protocol Support
export type AuthOptions = {
    clientId: string;
    authority?: string;
    validateAuthority?: boolean;
    redirectUri?: string | (() => string);
    postLogoutRedirectUri?: string | (() => string);
    navigateToLoginRequestUrl?: boolean;
};

// Cache Support
export type CacheOptions = {
    cacheLocation?: CacheLocation;
    storeAuthStateInCookie?: boolean;
};

// Library support
export type SystemOptions = {
    logger?: Logger;
    loadFrameTimeout?: number;
    tokenRenewalOffsetSeconds?: number;
    navigateFrameWait?: number;
};

// Developer App Environment Support
export type FrameworkOptions = {
    isAngular?: boolean;
    unprotectedResources?: Array<string>;
    protectedResourceMap?: Map<string, Array<string>>;
};

// Configuration Object
export type Configuration = {
    auth: AuthOptions,
    cache?: CacheOptions,
    system?: SystemOptions,
    framework?: FrameworkOptions
};
```

Poniżej znajduje się całkowity zbiór konfigurowalnych opcji, które są obecnie obsługiwane w obiekcie konfiguracji:

- **clientID**: wymagane. ClientID aplikacji należy uzyskać z portalu rejestracji aplikacji.

- **Urząd**: opcjonalny. Adres URL wskazujący katalog, z którego MSAL może żądać tokenów. Wartość domyślna to: `https://login.microsoftonline.com/common`.
    * W usłudze Azure AD ma postać https://&lt;wystąpienia&gt;/&lt;odbiorców&gt;, gdzie &lt;wystąpienie&gt; to domena dostawcy tożsamości (na przykład `https://login.microsoftonline.com`), a &lt;&gt; odbiorców to identyfikator reprezentowania odbiorców logowania. Mogą to być następujące wartości:
        * `https://login.microsoftonline.com/<tenant>`-dzierżawca to domena skojarzona z dzierżawcą, na przykład contoso.onmicrosoft.com lub identyfikator GUID reprezentujący `TenantID` właściwości katalogu używanego tylko do logowania użytkowników określonej organizacji.
        * `https://login.microsoftonline.com/common`— służy do logowania użytkowników przy użyciu kont służbowych lub konta osobistego firmy Microsoft.
        * `https://login.microsoftonline.com/organizations/`— służy do logowania użytkowników przy użyciu kont służbowych.
        * `https://login.microsoftonline.com/consumers/` — służy do logowania użytkowników tylko przy użyciu konto Microsoft osobistych (Live).
    * W Azure AD B2C ma postać `https://<instance>/tfp/<tenant>/<policyName>/`, gdzie wystąpienie jest domeną Azure AD B2C, dzierżawcą jest nazwą dzierżawy Azure AD B2C, PolicyName jest nazwą zasad B2C, które mają zostać zastosowane.


- **validateAuthority**: opcjonalne.  Sprawdź poprawność wystawcy tokenów. Wartość domyślna to `true`. W przypadku aplikacji B2C, ponieważ wartość urzędu jest znana i może być różna dla zasad, weryfikacja urzędu nie będzie działać i musi być ustawiona na `false`.

- **redirectUri**: opcjonalne.  Identyfikator URI przekierowania aplikacji, w którym odpowiedzi uwierzytelniania mogą być wysyłane i odbierane przez aplikację. Musi dokładnie pasować do jednego z identyfikatorów URI przekierowania zarejestrowanych w portalu. Wartość domyślna to `window.location.href`.

- **postLogoutRedirectUri**: opcjonalne.  Przekierowuje użytkownika do `postLogoutRedirectUri` po wylogowaniu. Wartość domyślna to `redirectUri`.

- **navigateToLoginRequestUrl**: opcjonalne. Możliwość wyłączenia domyślnej nawigacji na stronie początkowej po zalogowaniu się. Wartość domyślna to true. Jest on używany tylko w przypadku przepływów przekierowywania.

- **cacheLocation**: opcjonalne.  Ustawia magazyn przeglądarki na `localStorage` lub `sessionStorage`. Wartość domyślna to `sessionStorage`.

- **storeAuthStateInCookie**: opcjonalne.  Ta flaga została wprowadzona w MSAL. js v 0.2.2 jako poprawka do problemów z [pętlą uwierzytelniania](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) w programie Microsoft Internet Explorer i Microsoft Edge. Aby skorzystać z tej poprawki, Włącz dla flagi `storeAuthStateInCookie` wartość true. Gdy ta funkcja jest włączona, MSAL. js będzie przechowywać stan żądania uwierzytelnienia wymagany do weryfikacji przepływów uwierzytelniania w plikach cookie w przeglądarce. Domyślnie ta flaga jest ustawiona na `false`.

- **Rejestrator**: opcjonalny.  Obiekt rejestratora z wystąpieniem wywołania zwrotnego, które może zostać dostarczone przez dewelopera w celu korzystania z dzienników i publikowania ich w sposób niestandardowy. Aby uzyskać szczegółowe informacje dotyczące przekazywania obiektu rejestratora, zobacz [Rejestrowanie przy użyciu msal. js](msal-logging.md).

- **loadFrameTimeout**: opcjonalne.  Liczba milisekund braku aktywności przed upływem limitu czasu odpowiedzi na odnowienie tokenu z usługi Azure AD. Wartość domyślna to 6 sekund.

- **tokenRenewalOffsetSeconds**: opcjonalne. Liczba milisekund, która ustawia przedział czasu wymagany do odnowienia tokenu przed wygaśnięciem. Wartość domyślna to 300 milisekund.

- **navigateFrameWait**: opcjonalne. Liczba milisekund, która ustawia czas oczekiwania przed ukryciem ukrytych elementów iframe do ich lokalizacji docelowej. Wartość domyślna to 500 milisekund.

Są one stosowane tylko do przekazywania z biblioteki otoki kątowej MSAL:
- **unprotectedResources**: opcjonalne.  Tablica identyfikatorów URI, które nie są chronionymi zasobami. MSAL nie będzie dołączać tokenu do żądań wychodzących, które mają ten identyfikator URI. Wartość domyślna to `null`.

- **protectedResourceMap**: opcjonalne.  Jest to mapowanie zasobów do zakresów używanych przez MSAL do automatycznego dołączania tokenów dostępu w wywołaniach interfejsu API sieci Web. Dla zasobu uzyskano pojedynczy token dostępu. Aby można było zmapować określoną ścieżkę zasobu w następujący sposób: {"https://graph.microsoft.com/v1.0/me", ["User. Read"]} lub adres URL aplikacji zasobu: {"https://graph.microsoft.com/", ["User. Read", "mail. Send"]}. Jest to wymagane dla wywołań mechanizmu CORS. Wartość domyślna to `null`.
