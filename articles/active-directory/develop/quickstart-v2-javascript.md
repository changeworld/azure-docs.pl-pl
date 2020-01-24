---
title: Logowanie użytkowników w JavaScript aplikacje jednostronicowe | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, w jaki sposób aplikacja JavaScript może korzystać z interfejsu API, który wymaga tokenów dostępu przy użyciu platformy tożsamości firmy Microsoft.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.openlocfilehash: 954fd6c200e1165f27549dcf95502e9bfeed0267
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76703358"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa"></a>Szybki Start: Logowanie użytkowników i uzyskiwanie tokenu dostępu w usłudze JavaScript SPA

W tym przewodniku szybki start użyjesz przykładowego kodu, aby dowiedzieć się, jak aplikacja obsługująca skrypty JavaScript (single-page) może logować użytkowników z kont osobistych, kont służbowych i szkolnych. SPA może również uzyskać token dostępu, aby wywołać interfejs API Microsoft Graph lub dowolny internetowy interfejs API. (Zobacz [, jak działa przykład](#how-the-sample-works) dla ilustracji).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Środowisko [Node.js](https://nodejs.org/en/download/).
* [Visual Studio Code](https://code.visualstudio.com/download) (Aby edytować pliki projektu) lub [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (ro uruchamia projekt jako rozwiązanie Visual Studio).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Rejestrowanie i pobieranie aplikacji Szybki start
> Aby uruchomić aplikację szybkiego startu, użyj jednej z następujących opcji.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opcja 1 (Express): Zarejestruj i automatycznie Skonfiguruj aplikację, a następnie Pobierz przykład kodu
>
> 1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
> 1. Jeśli Twoje konto zapewnia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu w dzierżawie usługi Azure Active Directory (Azure AD), której chcesz użyć.
> 1. Przejdź do nowego okienka [Azure Portal-rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) .
> 1. Wprowadź nazwę aplikacji, a następnie wybierz pozycję **zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2 (ręczna): Zarejestruj i ręcznie skonfiguruj aplikację oraz przykład kodu
>
> #### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji
>
> 1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
>
> 1. Jeśli Twoje konto zapewnia dostęp do więcej niż jednej dzierżawy, wybierz swoje konto w prawym górnym rogu, a następnie ustaw sesję portalu z dzierżawą usługi Azure AD, której chcesz użyć.
> 1. Przejdź do strony Microsoft Identity Platform for Developers [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) .
> 1. Wybierz pozycję **Nowa rejestracja**.
> 1. Po wyświetleniu strony **Rejestrowanie aplikacji** wprowadź nazwę aplikacji.
> 1. W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**.
> 1. W obszarze **Identyfikator URI przekierowania** , na liście rozwijanej Wybierz platformę **sieci Web** , a następnie ustaw wartość na `http://localhost:30662/`.
> 1. Wybierz pozycję **Zarejestruj**. Na stronie **Przegląd** aplikacji Zanotuj wartość **identyfikatora aplikacji (klienta)** do późniejszego użycia.
> 1. Ten przewodnik Szybki start wymaga włączenia [przepływu niejawnego udzielenia](v2-oauth2-implicit-grant-flow.md). W lewym okienku zarejestrowanej aplikacji wybierz pozycję **uwierzytelnianie**.
> 1. W sekcji **Ustawienia zaawansowane** w obszarze **niejawne przyznanie**zaznacz pola wyboru **tokeny identyfikatorów** i **tokeny dostępu** . Tokeny identyfikatorów i tokeny dostępu są wymagane, ponieważ ta aplikacja musi zalogować użytkowników i wywołać interfejs API.
> 1. W górnej części okienka wybierz pozycję **Zapisz**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1. Konfigurowanie aplikacji w witrynie Azure Portal
> Aby przykład kodu dla tego przewodnika szybkiego startu działał, należy dodać `redirectUri` jako `http://localhost:30662/` i włączyć **niejawne przyznanie**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź zmiany automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowano](media/quickstart-v2-javascript/green-check.png) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów.

#### <a name="step-2-download-the-project"></a>Krok 2. Pobieranie projektu

Wybierz opcję odpowiednią dla Twojego środowiska programistycznego:

* Aby uruchomić projekt z serwerem sieci Web przy użyciu środowiska Node. js, [Pobierz podstawowe pliki projektu](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip). Aby otworzyć pliki, użyj edytora, takiego jak [Visual Studio Code](https://code.visualstudio.com/).

* Obowiązkowe Aby uruchomić projekt z serwerem IIS, [Pobierz projekt programu Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip). Wyodrębnij plik zip do folderu lokalnego (na przykład *C:\Azure-Samples*).

#### <a name="step-3-configure-your-javascript-app"></a>Krok 3. Konfigurowanie aplikacji JavaScript

> [!div renderon="docs"]
> W folderze *JavaScriptSPA* Edytuj *plik index. html*i ustaw wartości `clientID` i `authority` w obszarze `msalConfig`.

> [!div class="sxs-lookup" renderon="portal"]
> W folderze *JavaScriptSPA* Edytuj *plik index. html*i Zastąp `msalConfig` następującym kodem:

```javascript
var msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_info_here",
        redirectUri: "http://localhost:30662/"
    },
    cache: {
        cacheLocation: "localStorage",
        storeAuthStateInCookie: true
    }
};

```
> [!div renderon="portal"]
> > [!NOTE]
> > Ten przewodnik Szybki Start obsługuje Enter_the_Supported_Account_Info_Here.


> [!div renderon="docs"]
>
> Miejsce:
> - *\<Enter_the_Application_Id_here >* to **Identyfikator aplikacji (klienta)** dla zarejestrowanej aplikacji.
> - *\<Enter_the_Tenant_info_here >* jest ustawiona na jedną z następujących opcji:
>    - Jeśli aplikacja obsługuje *konta w tym katalogu organizacyjnym*, Zastąp tę wartość **identyfikatorem dzierżawy** lub **nazwą dzierżawy** (na przykład *contoso.Microsoft.com*).
>    - Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym*, Zastąp tę wartość **organizacją**.
>    - Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym i osobistych kontach Microsoft*, Zastąp tę wartość **wspólnym**. Aby ograniczyć obsługę *tylko do osobistych kont Microsoft*, Zastąp tę wartość **odbiorcom**.
>
> > [!TIP]
> > Aby znaleźć wartości **Identyfikator aplikacji (klienta)** , **Identyfikator katalogu (dzierżawy)** i **Obsługiwane typy kont**, przejdź do strony **Przegląd** w witrynie Azure Portal.
>

#### <a name="step-4-run-the-project"></a>Krok 4. uruchamianie projektu

* Jeśli używasz środowiska [Node. js](https://nodejs.org/en/download/):

    1. Aby uruchomić serwer, uruchom następujące polecenie w katalogu projektu:

        ```batch
        npm install
        node server.js
        ```

    1. Otwórz przeglądarkę internetową i przejdź do `http://localhost:30662/`.
    1. Wybierz pozycję **Zaloguj** się, aby rozpocząć logowanie, a następnie wywołaj Microsoft Graph API.


* Jeśli używasz [programu Visual Studio](https://visualstudio.microsoft.com/downloads/), wybierz rozwiązanie projektu, a następnie wybierz klawisz F5, aby uruchomić projekt.

Po załadowaniu przez przeglądarkę aplikacji wybierz pozycję **Zaloguj**. Gdy logujesz się po raz pierwszy, zostanie wyświetlony monit o zezwolenie aplikacji na dostęp do Twojego profilu i zalogowanie się. Po pomyślnym zalogowaniu się na stronie zostaną wyświetlone informacje o profilu użytkownika.

## <a name="more-information"></a>Więcej informacji

### <a name="how-the-sample-works"></a>Jak działa przykład

![Jak działa Przykładowa aplikacja w tym przewodniku Szybki Start](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal.js

Biblioteka MSAL rejestruje użytkowników i żąda tokenów, które są używane w celu uzyskania dostępu do interfejsu API chronionego przez platformę tożsamości firmy Microsoft. Plik *index. html* szybkiego startu zawiera odwołanie do biblioteki:

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.min.js"></script>
```
> [!TIP]
> Można zastąpić poprzednią wersję najnowszą wersją wydaną w ramach wersji [MSAL. js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).


Alternatywnie, jeśli masz zainstalowany program Node. js, możesz pobrać najnowszą wersję za pomocą Menedżera pakietów Node. js (npm):

```batch
npm install msal
```

### <a name="msal-initialization"></a>Inicjowanie biblioteki MSAL

Kod szybkiego startu pokazuje również, jak zainicjować bibliotekę MSAL:

```javascript
var msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here",
        redirectUri: "http://localhost:30662/"
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
> |`clientId`     | Identyfikator aplikacji, która jest zarejestrowana w Azure Portal.|
> |`authority`    | Obowiązkowe Adres URL urzędu obsługujący typy kont, zgodnie z opisem wcześniej w sekcji konfiguracji. Domyślny Urząd jest `https://login.microsoftonline.com/common`. |
> |`redirectUri`     | Skonfigurowana odpowiedź/redirectUri rejestracji aplikacji. W tym przypadku `http://localhost:30662/`. |
> |`cacheLocation`  | Obowiązkowe Ustawia magazyn przeglądarki dla stanu uwierzytelniania. Wartość domyślna to sessionStorage.   |
> |`storeAuthStateInCookie`  | Obowiązkowe Biblioteka, w której jest przechowywany stan żądania uwierzytelniania, który jest wymagany do weryfikacji przepływów uwierzytelniania w plikach cookie w przeglądarce. Ten plik cookie jest ustawiany dla przeglądarki IE i programu Edge, aby wyeliminować pewne [znane problemy](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues). |

Aby uzyskać więcej informacji na temat dostępnych opcji konfigurowalnych, zobacz [Inicjowanie aplikacji klienckich](msal-js-initializing-client-applications.md).

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
> | `scopes`   | Obowiązkowe Zawiera zakresy, które są wymagane do wyrażania zgody użytkownika w czasie logowania. Na przykład zakres `[ "user.read" ]` dla programu Microsoft Graph lub zakres `[ "<Application ID URL>/scope" ]` dla niestandardowych interfejsów internetowych API (czyli `api://<Application ID>/access_as_user`). |

> [!TIP]
> Alternatywnie możesz chcieć użyć metody `loginRedirect`, aby przekierować bieżącą stronę do strony logowania zamiast okna podręcznego.

### <a name="request-tokens"></a>Żądanie tokenów

MSAL używa trzech metod do uzyskiwania tokenów: `acquireTokenRedirect`, `acquireTokenPopup`i `acquireTokenSilent`

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

Istnieją sytuacje, w których należy wymusić, aby użytkownicy mogli korzystać z punktu końcowego platformy tożsamości firmy Microsoft. Przykład:
* Może być konieczne ponowne wprowadzenie poświadczeń przez użytkowników, ponieważ ich hasło wygasło.
* Aplikacja żąda dostępu do dodatkowych zakresów zasobów, do których użytkownik musi wyrazić zgodę.
* Wymagane jest uwierzytelnianie dwuskładnikowe.

Typowym zalecanym wzorcem dla większości aplikacji jest Wywołaj `acquireTokenSilent` pierwsze, a następnie Przechwyć wyjątek, a następnie Wywołaj `acquireTokenPopup` (lub `acquireTokenRedirect`), aby uruchomić żądanie interaktywne.

Wywołanie `acquireTokenPopup` skutkuje oknem podręcznym logowania. (Lub `acquireTokenRedirect` wyniki przekierowywania użytkowników do punktu końcowego platformy tożsamości firmy Microsoft). W tym oknie Użytkownicy muszą mieć możliwość działania przez potwierdzenie poświadczeń, udzielenie zgody na wymagane zasoby lub zakończenie uwierzytelniania dwuskładnikowego.

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
> Ten przewodnik Szybki Start używa metod `loginRedirect` i `acquireTokenRedirect` z programem Microsoft Internet Explorer ze względu na [znany problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) związany z obsługą okien podręcznych przez Internet Explorer.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać bardziej szczegółowy przewodnik krok po kroku dotyczący tworzenia aplikacji dla tego samouczka szybkiego startu, zobacz:

> [!div class="nextstepaction"]
> [Samouczek umożliwiający zalogowanie się i Wywołaj program MS Graph](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

Aby przejrzeć repozytorium MSAL w celu uzyskania dokumentacji, często zadawane pytania i problemów, zobacz:

> [!div class="nextstepaction"]
> [Repozytorium GitHub MSAL. js](https://github.com/AzureAD/microsoft-authentication-library-for-js)

Pomóż nam ulepszyć platformę tożsamości firmy Microsoft. Powiedz nam, co myślisz, wykonując krótką ankietę z dwoma pytaniami.

> [!div class="nextstepaction"]
> [Microsoft Identity platform — ankieta](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
