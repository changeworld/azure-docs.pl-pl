---
title: Inicjowanie aplikacji klienckich (Biblioteka Microsoft Authentication Library dla języka JavaScript) | Azure
description: Więcej informacji na temat inicjowania aplikacji klienckich za pomocą Biblioteka Microsoft Authentication Library dla języka JavaScript (MSAL.js).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd26f36356affbc8c272bd093757a8482773baf2
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544022"
---
# <a name="initialize-client-applications-using-msaljs"></a>Inicjowanie aplikacji klienckich za pomocą MSAL.js
W tym artykule opisano inicjowanie Biblioteka Microsoft Authentication Library dla języka JavaScript (MSAL.js) przy użyciu wystąpienia aplikacji agenta użytkownika. Aplikacja użytkownika agenta jest formularz aplikacji publicznych klienta, w którym kod klienta jest wykonywane w agenta użytkownika, takie jak przeglądarki sieci web. Tacy klienci nie należy przechowywać klucze tajne, ponieważ kontekst przeglądarki jest dostępna w sposób jawny. Aby dowiedzieć się więcej na temat typów aplikacji klienckich i opcje konfiguracji aplikacji, przeczytaj [Przegląd](msal-client-applications.md).

## <a name="prerequisites"></a>Wymagania wstępne
Przed inicjowania aplikacji, musisz najpierw [zarejestrowanie go za pomocą witryny Azure portal](scenario-spa-app-registration.md) tak, aby aplikację można zintegrować z platformą Microsoft identity. Po zarejestrowaniu może być konieczne następujące informacje (które można znaleźć w witrynie Azure portal):

- Identyfikator klienta (ciąg reprezentujący identyfikator GUID aplikacji)
- Adres URL dostawcy tożsamości (nazwanego wystąpienia) i grupy odbiorców logowania dla swojej aplikacji. Te dwa parametry są nazywane zbiorczo urzędu.
- Identyfikator dzierżawy, jeśli piszesz aplikację line-of-business wyłącznie na potrzeby Twojej organizacji (również o nazwie aplikacja jednej dzierżawy).
- W przypadku aplikacji sieci web należy również ustawić parametr redirectUri którym dostawca tożsamości zwróci się do aplikacji przy użyciu tokenów zabezpieczających.

## <a name="initializing-applications"></a>Inicjowanie aplikacji

Używając MSAL.js w następujący sposób w zwykły aplikacji JavaScript/Typescript. Zainicjuj kontekst uwierzytelniania biblioteki MSAL przez utworzenie wystąpienia `UserAgentApplication` za pomocą obiektu konfiguracji. Minimalnej konfiguracji wymagane do zainicjowania MSAL.js jest clientID aplikacji, które należy pobrać w portalu rejestracji aplikacji.

Dla metody uwierzytelniania za pomocą przekierowania przepływów (`loginRedirect` i `acquireTokenRedirect`), musisz jawnie zarejestrować wywołanie zwrotne dla powodzenia lub błędu, za pośrednictwem `handleRedirectCallback()` metody. Jest to niezbędne, ponieważ przepływy przekierowania nie zwracają obietnic, tak jak metody przy użyciu wyskakującego środowiska.

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

MSAL.js jest przeznaczona do ma pojedyncze wystąpienie i konfiguracji `UserAgentApplication` do reprezentowania kontekstu jednorazowego uwierzytelniania. Wiele wystąpień nie jest zalecane, ponieważ powodują konflikt wpisy w pamięci podręcznej i zachowanie w przeglądarce.

## <a name="configuration-options"></a>Opcje konfiguracji

MSAL.js ma konfigurację obiektu pokazane poniżej zawiera grupowanie konfigurowalne opcje dostępne dla tworzenia wystąpienia obiektu `UserAgentApplication`.

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

Poniżej przedstawiono zbiorze konfigurowalne opcje, które są obecnie obsługiwane w przypadku obiektu konfiguracji:

- **clientID**: Wymagany. ClientID aplikacji, należy pobrać to w portalu rejestracji aplikacji.

- **Urząd**: Opcjonalny. Adres URL wskazujący katalog, w jakiej biblioteki MSAL może żądać tokenów z. Wartość domyślna to: `https://login.microsoftonline.com/common`.
    * W usłudze Azure AD jest formę https://&lt;wystąpienia&gt;/&lt;odbiorców&gt;, gdzie &lt;wystąpienia&gt; jest domena dostawcy tożsamości (na przykład `https://login.microsoftonline.com`) i &lt;odbiorców&gt; jest identyfikator reprezentujący odbiorców logowania. Może to być następujące wartości:
        * `https://login.microsoftonline.com/<tenant>`-dzierżawy jest domeną skojarzone z tą dzierżawą, np. contoso.onmicrosoft.com lub identyfikator GUID, reprezentujący `TenantID` właściwość katalog służący wyłącznie do logowania użytkowników konkretnej organizacji.
        * `https://login.microsoftonline.com/common`— Umożliwia logowanie użytkowników mających pracy i konta służbowe lub osobiste konto Microsoft.
        * `https://login.microsoftonline.com/organizations/`— Używane do logowania użytkowników przy użyciu kont służbowych.
        * `https://login.microsoftonline.com/consumers/` — Umożliwia logowanie użytkowników mających tylko osobistego konta Microsoft (live).
    * W usłudze Azure AD B2C, jest w formie `https://<instance>/tfp/<tenant>/<policyName>/`, gdzie wystąpienie jest domeną usługi Azure AD B2C, dzierżawa jest nazwą dzierżawy usługi Azure AD B2C, policyName nazywa się zasadami B2C do zastosowania.


- **validateAuthority**: Opcjonalny.  Sprawdzanie poprawności wystawcy tokenów. Wartość domyślna to `true`. Dla aplikacji B2C, ponieważ wartość urzędu jest znany i może różnić się zgodnie z zasadami, weryfikacja urzędu nie będzie działać i musi być równa `false`.

- **redirectUri**: Opcjonalny.  Przekierowania URI aplikacji, gdzie odpowiedzi uwierzytelniania mogą być wysyłane i odbierane przez aplikację. Jego musi dokładnie odpowiadać jeden zarejestrowany w portalu, identyfikatory URI przekierowania z tym wyjątkiem, że musi być zakodowane w adresie URL. Wartość domyślna to `window.location.href`.

- **postLogoutRedirectUri**: Opcjonalny.  Przekierowuje użytkownika do `postLogoutRedirectUri` po wylogowaniu. Wartość domyślna to `redirectUri`.

- **navigateToLoginRequestUrl**: Opcjonalny. Możliwość wyłączenia nawigacji domyślnej strony startowej po zalogowaniu. Domyślna to true. To jest używana tylko w przypadku przepływów przekierowania.

- **cacheLocation**: Opcjonalny.  Ustawia magazynu przeglądarki albo `localStorage` lub `sessionStorage`. Wartość domyślna to `sessionStorage`.

- **storeAuthStateInCookie**: Opcjonalny.  Ta flaga została wprowadzona w MSAL.js v0.2.2 jako rozwiązanie [problemy z uwierzytelnianiem pętli](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) Microsoft Internet Explorer i Microsoft Edge. Włącz flagę `storeAuthStateInCookie` wartość true, skorzystaj z zalet to naprawić. Gdy ta opcja jest włączona, MSAL.js zapisze stan żądania uwierzytelniania, które są wymagane do weryfikacji przepływów uwierzytelniania w plikach cookie przeglądarki. Domyślnie ta flaga jest ustawiona na `false`.

- **Rejestrator**: Opcjonalny.  Obiekt z podanym przez dewelopera w celu umożliwienia użycia i publikowanie dzienników w niestandardowy sposób wystąpieniem wywołania zwrotnego za pomocą rejestratora. Aby uzyskać szczegółowe informacje dotyczące przekazywania obiektu rejestratora, zobacz [rejestrowanie za pomocą msal.js](msal-logging.md).

- **loadFrameTimeout**: Opcjonalny.  Przekroczono wyrażony w milisekundach czas braku aktywności przed włączeniem należy rozważyć odnowienia tokenu odpowiedź z usługi Azure AD. Domyślna to 6 sekund.

- **tokenRenewalOffsetSeconds**: Opcjonalny. Liczba milisekund ustawiająca okna przesunięcie potrzebnych do odnowienia tokenu przed wygaśnięciem. Domyślna to 300 MS.

Te dotyczą tylko być przekazana z biblioteki MSAL Angular otoki:
- **unprotectedResources**: Opcjonalny.  Tablica identyfikatorów URI, które są niechronionych zasobach. Biblioteka MSAL nie zostanie dołączona token do żądania wychodzące, które mają te identyfikatora URI. Wartość domyślna to `null`.

- **protectedResourceMap**: Opcjonalny.  Jest to mapowanie zasobów do zakresów używane przez biblioteki MSAL dla automatyczne dołączanie tokenów dostępu w wywołaniach interfejsu API sieci web. Jednego tokenu dostępu są uzyskiwane dla zasobu. Dzięki czemu można mapować ścieżki zasobu określonego w następujący sposób: {"https://graph.microsoft.com/v1.0/me", ["user.read"]}, lub adres URL zasobu jako: {"https://graph.microsoft.com/", ["user.read", "mail.send"]}. Jest to wymagane dla wywołań mechanizmu CORS. Wartość domyślna to `null`.
