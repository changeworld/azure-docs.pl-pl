---
title: Szybki Start z języka JavaScript platformy tożsamości firmy Microsoft — Azure
description: Dowiedz się, jak wywołać interfejs API, który wymaga tokenów dostępu przy użyciu platforma tożsamości usługi Microsoft aplikacji JavaScript.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2182708ea459b578a2a9a94213ab41e76821aefc
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514343"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-single-page-application"></a>Szybki start: Logowania użytkowników i uzyskiwanie tokenu dostępu z poziomu aplikacji jednostronicowej JavaScript

W tym przewodniku Szybki Start dowiesz się, jak użyć przykładowego kodu, który demonstruje, jak JavaScript aplikacja jednostronicowa (SPA) można logowania użytkowników, kont osobistych, kont służbowych i kont służbowych. JavaScript SPA można również uzyskać token dostępu do wywołania interfejsu API programu Microsoft Graph lub dowolnego interfejsu API sieci web.

![Jak działa przykładową aplikację w tym przewodniku Szybki Start](media/quickstart-v2-javascript/javascriptspa-intro.svg)

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki Start wymaga następujących konfiguracji:
* Aby uruchomić projekt za pomocą serwera Node.js, Pobierz i zainstaluj [Node.js](https://nodejs.org/en/download/).
* Aby edytować pliki projektu, Pobierz i zainstaluj [programu Visual Studio Code](https://code.visualstudio.com/download).
* Aby uruchomić projekt jako rozwiązanie w programie Visual Studio, Pobierz i zainstaluj [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Rejestrowanie i pobieranie aplikacji Szybki start
> Aby uruchomić aplikację Szybki Start, użyj jednej z następujących opcji.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opcja 1 (Express). zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu
>
> 1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu służbowego lub konta służbowego lub osobistego konta Microsoft.
> 1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu do dzierżawy usługi Azure Active Directory (Azure AD) chcesz użyć.
> 1. Przejdź do nowego [witryna Azure portal — rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) okienka.
> 1. Wprowadź nazwę aplikacji, a następnie wybierz pozycję **zarejestrować**.
> 1. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować swoją nową aplikację.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2 (ręcznie). zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Rejestrowanie aplikacji
>
> 1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu służbowego lub konta służbowego lub osobistego konta Microsoft.
>
> 1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz swoje konto w prawym górnym rogu, a następnie ustaw sesję portalu do dzierżawy usługi Azure AD chcesz użyć.
> 1. Wybierz pozycję platforma tożsamości firmy Microsoft dla deweloperów [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) strony.
> 1. Wybierz **nowej rejestracji**.
> 1. Po wyświetleniu strony **Rejestrowanie aplikacji** wprowadź nazwę aplikacji.
> 1. W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**.
> 1. W obszarze **identyfikator URI przekierowania** sekcji z listy rozwijanej wybierz **Web** platformy, a następnie ustaw wartość na `http://localhost:30662/`.
> 1. Wybierz pozycję **Zarejestruj**. W aplikacji **Przegląd** stronie **identyfikator aplikacji (klienta)** wartości do późniejszego użycia.
> 1. Ten przewodnik Szybki start wymaga włączenia [przepływu niejawnego udzielenia](v2-oauth2-implicit-grant-flow.md). W okienku po lewej stronie zarejestrowanej aplikacji wybierz **uwierzytelniania**.
> 1. W **Zaawansowane ustawienia** sekcji w obszarze **przyznawanie niejawne**, wybierz opcję **tokeny Identyfikatora** i **tokeny dostępu** pola wyboru. Identyfikator tokenów i tokenów dostępu są wymagane, ponieważ ta aplikacja wymaga logowania użytkowników i wywoływać interfejs API.
> 1. W górnej części okienka wybierz **Zapisz**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1: Konfigurowanie aplikacji w witrynie Azure Portal
> Dla przykładu kodu dla tego przewodnika Szybki Start do pracy, należy dodać przekierowania URI jako `http://localhost:30662/` i włączyć **przyznawanie niejawne**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź zmiany automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowano](media/quickstart-v2-javascript/green-check.png) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów.

#### <a name="step-2-download-the-project"></a>Krok 2: Pobieranie projektu

Wybierz opcję, która jest odpowiednia do swojego środowiska projektowego:

* Aby uruchomić projekt z serwerem sieci web przy użyciu środowiska Node.js, [pobrać pliki projektów core](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip). Do otwierania plików, użyj edytora, takiego jak [programu Visual Studio Code](https://code.visualstudio.com/).

* (Opcjonalnie) Aby uruchomić projekt na serwerze usług IIS [pobrać projekt programu Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip). Wyodrębnij plik zip do folderu lokalnego (na przykład *C:\Azure-Samples*).



#### <a name="step-3-configure-your-javascript-app"></a>Krok 3: Konfigurowanie aplikacji języka JavaScript

> [!div renderon="docs"]
> W *JavaScriptSPA* folderu, Edytuj *index.html*i ustaw `clientID` i `authority` wartości w obszarze `msalConfig`.

> [!div class="sxs-lookup" renderon="portal"]
> W *JavaScriptSPA* folderu, Edytuj *index.html*i Zastąp `msalConfig` następującym kodem:

```javascript
var msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_info_here"
    },
    cache: {
        cacheLocation: "localStorage",
        storeAuthStateInCookie: true
    }
};

```
> [!div renderon="portal"]
> > [!NOTE]
> > Ten przewodnik szybkiego startu obsługuje Enter_the_Supported_Account_Info_Here.


> [!div renderon="docs"]
>
> Gdzie:
> - *\<Enter_the_Application_Id_here >* jest **identyfikator aplikacji (klienta)** dla aplikacji został zarejestrowany.
> - *\<Enter_the_Tenant_info_here >* jest ustawiony na jedną z następujących opcji:
>    - Jeśli aplikacja obsługuje *kont w tym katalogu organizacji*, Zastąp tę wartość za pomocą **identyfikator dzierżawy** lub **nazwa dzierżawy** (na przykład  *contoso.microsoft.com*).
>    - Jeśli aplikacja obsługuje *kont w dowolnym katalogu organizacji*, Zastąp tę wartość za pomocą **organizacje**.
>    - Jeśli aplikacja obsługuje *kont w dowolnym katalogu organizacji i osobistych kont Microsoft*, Zastąp tę wartość za pomocą **wspólnej**. Aby ograniczyć obsługę do *tylko konta osobiste firmy Microsoft*, Zastąp tę wartość za pomocą **konsumentów**.
>
> > [!TIP]
> > Aby znaleźć wartości **Identyfikator aplikacji (klienta)** , **Identyfikator katalogu (dzierżawy)** i **Obsługiwane typy kont**, przejdź do strony **Przegląd** w witrynie Azure Portal.
>

#### <a name="step-4-run-the-project"></a>Krok 4: Uruchamianie projektu

* Jeśli używasz [Node.js](https://nodejs.org/en/download/):

    1. Aby uruchomić serwer, uruchom następujące polecenie z katalogu projektu:

        ```batch
        npm install
        node server.js
        ```

    1. Otwórz przeglądarkę internetową i przejdź do `http://localhost:30662/`.
    1. Wybierz **Sign In** logowania, a następnie wywoływania interfejsu API programu Microsoft Graph.


* Jeśli używasz [programu Visual Studio](https://visualstudio.microsoft.com/downloads/)wybierz projekt rozwiązanie, a następnie naciśnij klawisz F5, aby uruchomić projekt.

Po załadowaniu aplikacji przeglądarki wybierz **Sign In**. Przy pierwszym logowaniu się, zostanie wyświetlony monit Twojej zgody, aby umożliwić aplikacji dostęp do Twojego profilu i logowanie się w. Po Zalogowano pomyślnie, informacji o profilu użytkownika powinien być wyświetlany na stronie.

## <a name="more-information"></a>Więcej informacji

### <a name="msaljs"></a>msal.js

Biblioteka MSAL użytkownikom zalogowanie się i żądań tokenów, które umożliwiają dostęp do interfejsu API, który jest chroniony przez platformą Microsoft identity. Samouczek Szybki Start *index.html* plik zawiera odwołanie do biblioteki:

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.min.js"></script>
```
> [!TIP]
> Możesz zastąpić poprzednią najnowszą wersję oficjalną w obszarze [zwalnia MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).


Alternatywnie Jeśli masz zainstalowane środowisko Node.js, możesz pobrać najnowszej wersji przy użyciu środowiska Node.js Package Manager (npm):

```batch
npm install msal
```

### <a name="msal-initialization"></a>Inicjowanie biblioteki MSAL

Kod szybkiego startu pokazuje również, jak można zainicjować biblioteki MSAL:

```javascript
var msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
    },
    cache: {
        cacheLocation: "localStorage",
        storeAuthStateInCookie: true
    }
};

var myMSALObj = new Msal.UserAgentApplication(msalConfig);
```

> |Lokalizacja  |  |
> |---------|---------|
> |`ClientId`     | Identyfikator aplikacji, która jest zarejestrowana w witrynie Azure portal.|
> |`authority`    | (Opcjonalnie) Adres URL urzędu, która obsługuje typy kont, jak opisano wcześniej w sekcji konfiguracji. Jest domyślnego urzędu `https://login.microsoftonline.com/common`. |
> |`cacheLocation`  | (Opcjonalnie) Ustawia magazynu przeglądarki stanu uwierzytelniania. Wartość domyślna to sessionStorage.   |
> |`storeAuthStateInCookie`  | (Opcjonalnie) Biblioteka, która przechowuje stan żądania uwierzytelniania, które są wymagane do weryfikacji przepływów uwierzytelniania w plikach cookie przeglądarki. Ten plik cookie jest ustawiona dla przeglądarki IE i urządzeniami brzegowymi w celu złagodzenie niektórych [znane problemy dotyczące](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues). |

Aby uzyskać więcej informacji o dostępnych opcjach można skonfigurować, zobacz [zainicjować aplikacje klienckie](msal-js-initializing-client-applications.md).

### <a name="sign-in-users"></a>Logowanie użytkowników

Poniższy fragment kodu pokazuje, jak logować użytkowników:

```javascript
var requestObj = {
    scopes: ["user.read"]
};

myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
    //Login Success callback code here
}).catch(function (error) {
    console.log(error);
});
```

> |Lokalizacja  |  |
> |---------|---------|
> | `scopes`   | (Opcjonalnie) Zawiera zakresy, które są żądane o zgodę użytkownika podczas logowania. Na przykład zakres `[ "user.read" ]` dla programu Microsoft Graph lub zakres `[ "<Application ID URL>/scope" ]` dla niestandardowych interfejsów internetowych API (czyli `api://<Application ID>/access_as_user`). |

> [!TIP]
> Alternatywnie, możesz chcieć użyć `loginRedirect` metodę, aby przekierować bieżącej strony do strony logowania, zamiast oknie podręcznym.

### <a name="request-tokens"></a>Żądanie tokenów

Biblioteka MSAL trzy sposoby uzyskania tokenów: `acquireTokenRedirect`, `acquireTokenPopup`, i `acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>Dyskretne pobieranie tokenu użytkownika

Metoda `acquireTokenSilent` obsługuje uzyskiwanie i odnawianie tokenów bez żadnej interakcji z użytkownikiem. Po wykonaniu metody `loginRedirect` lub `loginPopup` po raz pierwszy często stosuje się metodę `acquireTokenSilent` do uzyskiwania tokenów, które są używane w celu uzyskiwania dostępu do chronionych zasobów w kolejnych wywołaniach. Wywołania żądania lub odnowienia tokenów są wykonywane dyskretnie.

```javascript
var requestObj = {
    scopes: ["user.read"]
};

myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
    // Callback code here
    console.log(tokenResponse.accessToken);
}).catch(function (error) {
    console.log(error);
});
```

> |Lokalizacja  |  |
> |---------|---------|
> | `scopes`   | Zawiera zakresy żądane na potrzeby zwracania w tokenie dostępu dla interfejsu API. Na przykład zakres `[ "user.read" ]` dla programu Microsoft Graph lub zakres `[ "<Application ID URL>/scope" ]` dla niestandardowych interfejsów internetowych API (czyli `api://<Application ID>/access_as_user`).|

#### <a name="get-a-user-token-interactively"></a>Interaktywne pobieranie tokenu użytkownika

Istnieją sytuacje, w których należy wymusić użytkownikom na interakcję z punktem końcowym platforma tożsamości firmy Microsoft. Na przykład:
* Użytkownicy może być konieczne ponowne wprowadzenie poświadczeń, ponieważ ich hasło wygasło.
* Aplikacja żąda dostępu do zakresów dodatkowych zasobów, które użytkownik musi wyrazić zgodę na.
* Wymagane jest uwierzytelnianie dwuskładnikowe.

Zwyczajowego wzorca zalecane w przypadku większości aplikacji jest wywołanie `acquireTokenSilent` po pierwsze, następnie przechwycić wyjątek, a następnie wywołaj `acquireTokenPopup` (lub `acquireTokenRedirect`) do uruchomienia interaktywnego żądania.

Wywoływanie `acquireTokenPopup` wyniki w oknie podręcznym logowania. (Lub `acquireTokenRedirect` skutkuje przekierowywanie użytkowników do endpoint platforma tożsamości firmy Microsoft.) W tym oknie Użytkownicy muszą wchodzić w interakcje potwierdzenie poświadczeń, zapewniając zgody do wymaganych zasobów lub ukończenie uwierzytelniania dwuskładnikowego.

```javascript
var requestObj = {
    scopes: ["user.read"]
};

myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
    // Callback code here
    console.log(tokenResponse.accessToken);
}).catch(function (error) {
    console.log(error);
});
```

> [!NOTE]
> Ten przewodnik Szybki Start używa `loginRedirect` i `acquireTokenRedirect` metod za pomocą programu Microsoft Internet Explorer z powodu [znany problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) związanych z obsługą okna podręcznego przez program Internet Explorer.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać bardziej szczegółowy przewodnik krok po kroku dotyczących tworzenia aplikacji dla tego przewodnika Szybki Start zobacz:

> [!div class="nextstepaction"]
> [Samouczek logować się i wywołania MS Graph](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

Aby odszukać repozytorium biblioteki MSAL dla dokumentacji, często zadawane pytania, problemy i uzyskać więcej informacji, zobacz:

> [!div class="nextstepaction"]
> [Repozytorium MSAL.js GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js)
