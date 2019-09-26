---
title: Microsoft Identity platform JavaScript — szybki start — Azure
description: Dowiedz się, w jaki sposób aplikacje JavaScript mogą wywołać interfejs API, który wymaga tokenów dostępu przy użyciu platformy tożsamości firmy Microsoft.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev, identityplatformtop40
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.collection: M365-identity-device-management
ms.openlocfilehash: 988c73236d9f5ef360ded03bca36a4bb24ebd308
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71290818"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-single-page-application"></a>Szybki start: Logowanie użytkowników i uzyskiwanie tokenu dostępu z jednostronicowej aplikacji JavaScript

W tym przewodniku szybki start dowiesz się, jak używać przykładu kodu, który pokazuje, jak aplikacja obsługująca skrypty JavaScript (single-page) może logować użytkowników z kont osobistych, kont służbowych i szkolnych. SPA może również uzyskać token dostępu, aby wywołać interfejs API Microsoft Graph lub dowolny internetowy interfejs API.

![Jak działa Przykładowa aplikacja w tym przewodniku Szybki Start](media/quickstart-v2-javascript/javascriptspa-intro.svg)

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki Start wymaga następującej konfiguracji:
* Aby uruchomić projekt z serwerem Node. js, Pobierz i zainstaluj program [Node. js](https://nodejs.org/en/download/).
* Aby edytować pliki projektu, Pobierz i zainstaluj [Visual Studio Code](https://code.visualstudio.com/download).
* Aby uruchomić projekt jako rozwiązanie programu Visual Studio, Pobierz i zainstaluj [program Visual studio 2019](https://visualstudio.microsoft.com/downloads/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Rejestrowanie i pobieranie aplikacji Szybki start
> Aby uruchomić aplikację szybkiego startu, użyj jednej z następujących opcji.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opcja 1 (Express): zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu
>
> 1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
> 1. Jeśli Twoje konto zapewnia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu w dzierżawie usługi Azure Active Directory (Azure AD), której chcesz użyć.
> 1. Przejdź do nowego okienka [Azure Portal-rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) .
> 1. Wprowadź nazwę aplikacji, a następnie wybierz pozycję **zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2 (ręczna): zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Zarejestruj swoją aplikację
>
> 1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
>
> 1. Jeśli Twoje konto zapewnia dostęp do więcej niż jednej dzierżawy, wybierz swoje konto w prawym górnym rogu, a następnie ustaw sesję portalu z dzierżawą usługi Azure AD, której chcesz użyć.
> 1. Przejdź do strony Microsoft Identity Platform for Developers [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) .
> 1. Wybierz pozycję **Nowa rejestracja**.
> 1. Po wyświetleniu strony **Rejestrowanie aplikacji** wprowadź nazwę aplikacji.
> 1. W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**.
> 1. Z listy rozwijanej w sekcji **Identyfikator URI przekierowania** Wybierz platformę **sieci Web** , a następnie ustaw wartość na `http://localhost:30662/`.
> 1. Wybierz pozycję **Zarejestruj**. Na stronie **Przegląd** aplikacji Zanotuj wartość **identyfikatora aplikacji (klienta)** do późniejszego użycia.
> 1. Ten przewodnik Szybki start wymaga włączenia [przepływu niejawnego udzielenia](v2-oauth2-implicit-grant-flow.md). W lewym okienku zarejestrowanej aplikacji wybierz pozycję **uwierzytelnianie**.
> 1. W sekcji **Ustawienia zaawansowane** w obszarze niejawne przyznanie zaznacz pola wyboru **tokeny identyfikatorów** i **tokeny dostępu** . Tokeny identyfikatorów i tokeny dostępu są wymagane, ponieważ ta aplikacja musi zalogować użytkowników i wywołać interfejs API.
> 1. W górnej części okienka wybierz pozycję **Zapisz**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1: Konfigurowanie aplikacji w witrynie Azure Portal
> Aby przykład kodu dla tego przewodnika Szybki Start działał, należy dodać identyfikator URI `http://localhost:30662/` przekierowania i włączyć niejawny przydział.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź zmiany automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowano](media/quickstart-v2-javascript/green-check.png) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów.

#### <a name="step-2-download-the-project"></a>Krok 2: Pobieranie projektu

Wybierz opcję odpowiednią dla Twojego środowiska programistycznego:

* Aby uruchomić projekt z serwerem sieci Web przy użyciu środowiska Node. js, [Pobierz podstawowe pliki projektu](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip). Aby otworzyć pliki, użyj edytora, takiego jak [Visual Studio Code](https://code.visualstudio.com/).

* Obowiązkowe Aby uruchomić projekt z serwerem IIS, [Pobierz projekt programu Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip). Wyodrębnij plik zip do folderu lokalnego (na przykład *C:\Azure-Samples*).



#### <a name="step-3-configure-your-javascript-app"></a>Krok 3: Konfigurowanie aplikacji języka JavaScript

> [!div renderon="docs"]
> W folderze *JavaScriptSPA* Edytuj *plik index. html* `clientID` i ustaw wartości i `authority` w obszarze `msalConfig`.

> [!div class="sxs-lookup" renderon="portal"]
> W folderze *JavaScriptSPA* Edytuj *plik index. html*i Zastąp `msalConfig` go następującym kodem:

```javascript
var msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_info_here",
        redirectURI: "http://localhost:30662/"
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
> Gdzie:
> - Enter_the_Application_Id_here > to **Identyfikator aplikacji (klienta)** dla zarejestrowanej aplikacji.  *\<*
> - Enter_the_Tenant_info_here > jest ustawiona na jedną z następujących opcji:  *\<*
>    - Jeśli aplikacja obsługuje *konta w tym katalogu organizacyjnym*, Zastąp tę wartość **identyfikatorem dzierżawy** lub **nazwą dzierżawy** (na przykład *contoso.Microsoft.com*).
>    - Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym*, Zastąp tę wartość **organizacją**.
>    - Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym i osobistych kontach Microsoft*, Zastąp tę wartość **wspólnym**. Aby ograniczyć obsługę *tylko do osobistych kont Microsoft*, Zastąp tę wartość **odbiorcom**.
>
> > [!TIP]
> > Aby znaleźć wartości **Identyfikator aplikacji (klienta)** , **Identyfikator katalogu (dzierżawy)** i **Obsługiwane typy kont**, przejdź do strony **Przegląd** w witrynie Azure Portal.
>

#### <a name="step-4-run-the-project"></a>Krok 4: Uruchamianie projektu

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
        redirectURI: "http://localhost:30662/"
    },
    cache: {
        cacheLocation: "localStorage",
        storeAuthStateInCookie: true
    }
};

var myMSALObj = new Msal.UserAgentApplication(msalConfig);
```

> |Gdzie  |  |
> |---------|---------|
> |`clientId`     | Identyfikator aplikacji, która jest zarejestrowana w Azure Portal.|
> |`authority`    | Obowiązkowe Adres URL urzędu obsługujący typy kont, zgodnie z opisem wcześniej w sekcji konfiguracji. Domyślny urząd to `https://login.microsoftonline.com/common`. |
> |`redirectURI`     | Skonfigurowany identyfikator URI odpowiedzi/przekierowania dla rejestracji aplikacji. W takim przypadku `http://localhost:30662/`. |
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

> |Gdzie  |  |
> |---------|---------|
> | `scopes`   | Obowiązkowe Zawiera zakresy, które są wymagane do wyrażania zgody użytkownika w czasie logowania. Na przykład zakres `[ "user.read" ]` dla programu Microsoft Graph lub zakres `[ "<Application ID URL>/scope" ]` dla niestandardowych interfejsów internetowych API (czyli `api://<Application ID>/access_as_user`). |

> [!TIP]
> Alternatywnie możesz chcieć użyć `loginRedirect` metody, aby przekierować bieżącą stronę do strony logowania zamiast okna podręcznego.

### <a name="request-tokens"></a>Żądanie tokenów

MSAL używa trzech metod do uzyskiwania tokenów `acquireTokenRedirect`: `acquireTokenPopup`, i`acquireTokenSilent`

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

> |Gdzie  |  |
> |---------|---------|
> | `scopes`   | Zawiera zakresy żądane na potrzeby zwracania w tokenie dostępu dla interfejsu API. Na przykład zakres `[ "user.read" ]` dla programu Microsoft Graph lub zakres `[ "<Application ID URL>/scope" ]` dla niestandardowych interfejsów internetowych API (czyli `api://<Application ID>/access_as_user`).|

#### <a name="get-a-user-token-interactively"></a>Interaktywne pobieranie tokenu użytkownika

Istnieją sytuacje, w których należy wymusić, aby użytkownicy mogli korzystać z punktu końcowego platformy tożsamości firmy Microsoft. Na przykład:
* Może być konieczne ponowne wprowadzenie poświadczeń przez użytkowników, ponieważ ich hasło wygasło.
* Aplikacja żąda dostępu do dodatkowych zakresów zasobów, do których użytkownik musi wyrazić zgodę.
* Wymagane jest uwierzytelnianie dwuskładnikowe.

Typowym zalecanym wzorcem dla większości aplikacji jest `acquireTokenSilent` Wywołaj pierwsze, a następnie Przechwyć wyjątek, `acquireTokenPopup` a następnie `acquireTokenRedirect`Wywołaj (lub), aby uruchomić żądanie interaktywne.

Wywoływanie `acquireTokenPopup` wyników w oknie podręcznym do logowania. (Lub `acquireTokenRedirect` wyniki przekierowywania użytkowników do punktu końcowego platformy tożsamości firmy Microsoft). W tym oknie Użytkownicy muszą mieć możliwość działania przez potwierdzenie poświadczeń, udzielenie zgody na wymagane zasoby lub zakończenie uwierzytelniania dwuskładnikowego.

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
> Ten przewodnik Szybki Start `loginRedirect` używa `acquireTokenRedirect` metod i programu Microsoft Internet Explorer ze względu na [znany problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) związany z obsługą okien podręcznych przez Internet Explorer.

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
