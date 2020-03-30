---
title: Inicjowanie aplikacji klienckich MSAL.js | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o inicjowaniu aplikacji klienckich przy użyciu biblioteki uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL.js).
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
ms.openlocfilehash: a0a2c5fc971c3f1f3283d95c5617bdf1e88a6a58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084044"
---
# <a name="initialize-client-applications-using-msaljs"></a>Inicjowanie aplikacji klienckich przy użyciu pliku MSAL.js
W tym artykule opisano inicjowanie biblioteki uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL.js) z wystąpieniem aplikacji agenta użytkownika. Aplikacja user-agent jest formą publicznej aplikacji klienckiej, w której kod klienta jest wykonywany w agencie użytkownika, takim jak przeglądarka internetowa. Klienci ci nie przechowują wpisów tajnych, ponieważ kontekst przeglądarki jest otwarty. Aby dowiedzieć się więcej o typach aplikacji klienckich i opcjach konfiguracji aplikacji, przeczytaj [omówienie](msal-client-applications.md).

## <a name="prerequisites"></a>Wymagania wstępne
Przed zainicjowaniem aplikacji należy najpierw [zarejestrować ją w witrynie Azure portal,](scenario-spa-app-registration.md) aby aplikacja mogła być zintegrowana z platformą tożsamości firmy Microsoft. Po rejestracji mogą być potrzebne następujące informacje (które można znaleźć w witrynie Azure portal):

- Identyfikator klienta (ciąg reprezentujący identyfikator GUID dla aplikacji)
- Adres URL dostawcy tożsamości (o nazwie wystąpienie) i grupa odbiorców logowania dla aplikacji. Te dwa parametry są łącznie znane jako organ.
- Identyfikator dzierżawy, jeśli piszesz aplikację biznesową wyłącznie dla twojej organizacji (również o nazwie aplikacja z jedną dzierżawą).
- W przypadku aplikacji sieci web należy również ustawić redirectUri, gdzie dostawca tożsamości powróci do aplikacji za pomocą tokenów zabezpieczających.

## <a name="initializing-applications"></a>Inicjowanie aplikacji

W zwykłej aplikacji JavaScript/Typescript można używać w następujący sposób. Inicjowanie kontekstu uwierzytelniania `UserAgentApplication` MSAL przez wystąpienie z obiektem konfiguracji. Minimalna wymagana konfiguracja do zainicjowania msal.js jest clientID aplikacji, które należy uzyskać z portalu rejestracji aplikacji.

W przypadku metod uwierzytelniania z przepływami przekierowania (`loginRedirect` i `acquireTokenRedirect`), należy `handleRedirectCallback()` jawnie zarejestrować wywołanie zwrotne dla sukcesu lub błędu za pomocą metody. Jest to konieczne, ponieważ przepływy przekierowania nie zwracają obietnic, jak metody z wyskakującym doświadczeniem.

```javascript
// Configuration object constructed
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
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

MSAL.js ma na celu jedno wystąpienie `UserAgentApplication` i konfigurację do reprezentowania kontekstu uwierzytelniania pojedynczego. Wiele wystąpień nie są zalecane, ponieważ powodują one konflikt wpisów pamięci podręcznej i zachowanie w przeglądarce.

## <a name="configuration-options"></a>Opcje konfiguracji

Msal.js ma obiekt konfiguracji pokazany poniżej, który zapewnia grupowanie konfigurowalnych opcji dostępnych do tworzenia wystąpienia `UserAgentApplication`.

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

Poniżej znajduje się całkowity zestaw konfigurowalnych opcji, które są obecnie obsługiwane w obiekcie konfiguracji:

- **clientID**: Wymagane. Identyfikator klienta aplikacji, należy uzyskać to z portalu rejestracji aplikacji.

- **urząd**: Fakultatywne. Adres URL wskazujący katalog, z którym usługa MSAL może żądać tokenów. Wartość domyślna `https://login.microsoftonline.com/common`to: .
    * W usłudze Azure AD jest to&lt;formularz&gt;/&lt;&gt;https:// &lt;odbiorców&gt; wystąpienia , gdzie wystąpienie `https://login.microsoftonline.com`jest &lt;&gt; domeną dostawcy tożsamości (na przykład ) a grupa odbiorców jest identyfikatorem reprezentującym odbiorców logowania. Mogą to być następujące wartości:
        * `https://login.microsoftonline.com/<tenant>`- dzierżawa jest domeną skojarzoną z dzierżawą, taką jak contoso.onmicrosoft.com `TenantID` lub identyfikator guiD reprezentujący właściwość katalogu używanego tylko do logowania użytkowników określonej organizacji.
        * `https://login.microsoftonline.com/common`- Służy do logowania użytkowników za pomocą kont służbowych i szkolnych lub konta osobistego Microsoft.
        * `https://login.microsoftonline.com/organizations/`- Służy do logowania użytkowników z kontami służbowymi i szkolnymi.
        * `https://login.microsoftonline.com/consumers/`- Służy do logowania użytkowników przy użyciu tylko osobistego konta Microsoft (na żywo).
    * W usłudze Azure AD B2C `https://<instance>/tfp/<tenant>/<policyName>/`jest to formularz , gdzie wystąpienie jest domeną usługi Azure AD B2C, czyli {twoja nazwa dzierżawy}.b2clogin.com, dzierżawa jest nazwą dzierżawy usługi Azure AD B2C, czyli {twoja-nazwa dzierżawy}.onmicrosoft.com, policyName jest nazwą zasad B2C do zastosowania.


- **validateAuthority**: Opcjonalnie.  Sprawdź poprawność wystawcy tokenów. Wartość domyślna to `true`. W przypadku aplikacji B2C, ponieważ wartość urzędu jest znana i może być inna dla `false`zasad, sprawdzanie poprawności urzędu nie będzie działać i musi być ustawione na .

- **redirectUri**: Opcjonalnie.  Identyfikator URI przekierowania aplikacji, w którym aplikacja może wysyłać i odbierać odpowiedzi na uwierzytelnianie. Musi dokładnie odpowiadać jednemu z URI przekierowania zarejestrowanym w portalu. Wartość domyślna to `window.location.href`.

- **postLogoutRedirectUri**: Opcjonalnie.  Przekierowuje `postLogoutRedirectUri` użytkownika do po wylogowywaniu się. Wartość domyślna to `redirectUri`.

- **navigateToLoginRequestUrl**: Opcjonalnie. Możliwość wyłączenia domyślnej nawigacji, aby rozpocząć stronę po zalogowaniu. Wartość domyślna to „true”. Jest to używane tylko dla przepływów przekierowania.

- **cacheLocation**: Opcjonalnie.  Ustawia pamięć masową `localStorage` `sessionStorage`przeglądarki na jedną lub . Wartość domyślna to `sessionStorage`.

- **storeAuthStateInCookie**: Opcjonalnie.  Ta flaga została wprowadzona w programie MSAL.js v0.2.2 jako poprawka dotycząca [problemów z pętlą uwierzytelniania](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) w programach Microsoft Internet Explorer i Microsoft Edge. Włącz flagę `storeAuthStateInCookie` do true, aby skorzystać z tej poprawki. Gdy ta opcja jest włączona, plik MSAL.js będzie przechowywać stan żądania eru wymagany do sprawdzania poprawności przepływów eru w plikach cookie przeglądarki. Domyślnie ta flaga `false`jest ustawiona na .

- **rejestrator:** Opcjonalnie.  Obiekt Logger z wystąpieniem wywołania zwrotnego, które mogą być dostarczone przez dewelopera do korzystania i publikowania dzienników w sposób niestandardowy. Aby uzyskać szczegółowe informacje na temat przekazywania obiektu rejestratora, zobacz [rejestrowanie za pomocą pliku msal.js](msal-logging.md).

- **loadFrameTimeout**: Opcjonalnie.  Liczba milisekund braku aktywności przed odpowiedzią na odnowienie tokenu z usługi Azure AD należy rozważyć upłynie limit czasu. Wartość domyślna to 6 sekund.

- **tokenRenewalOffsetSekundy:** Opcjonalnie. Liczba milisekund, która ustawia okno przesunięcia potrzebne do odnowienia tokenu przed wygaśnięciem. Wartość domyślna to 300 milisekund.

- **navigateFrameWait**: Opcjonalnie. Liczba milisekund, która ustawia czas oczekiwania przed ukrytymi ramkami iframe, aby przejść do miejsca docelowego. Wartość domyślna to 500 milisekund.

Mają one zastosowanie tylko do przekazania z biblioteki otoki kątowej MSAL:
- **niechronioneŹródło:** Opcjonalnie.  Tablica identyfikatorów URI, które są zasobami niechronionymi. Usługa MSAL nie dołączy tokenu do żądań wychodzących, które mają te identyfikatory URI. Wartość domyślna to `null`.

- **protectedResourceMap**: Opcjonalnie.  Jest to mapowanie zasobów do zakresów używanych przez msal do automatycznego dołączania tokenów dostępu w wywołaniach interfejsu API sieci web. Token pojedynczego dostępu jest uzyskiwany dla zasobu. Możesz więc mapować określoną ścieżkęhttps://graph.microsoft.com/v1.0/mezasobu w następujący sposób: {" ", ["user.read"]}, lub adres URL aplikacji zasobu jako: {"https://graph.microsoft.com/", ["user.read", "mail.send"]}. Jest to wymagane w przypadku połączeń CORS. Wartość domyślna to `null`.
