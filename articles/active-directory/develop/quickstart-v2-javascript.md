---
title: Szybki Start platformy Azure AD w wersji 2 JavaScript | Dokumentacja firmy Microsoft
description: Dowiedz się, jak aplikacji JavaScript może wywołać interfejs API, które wymagają tokenów dostępu przez punktu końcowego v2.0 usługi Azure Active Directory
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 99ea7e7db9d0cc80bfd37a256fc1be388feaa530
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54043893"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-application"></a>Szybki start: Logowania użytkowników i uzyskiwanie tokenu dostępu z poziomu aplikacji JavaScript

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

W tym przewodniku Szybki Start dowiesz się, jak użyć przykładowego kodu, który demonstruje, jak JavaScript, aplikacja jednostronicowa (SPA) można się na kontach osobistych, pracy i kont służbowych, uzyskania tokenu dostępu do wywołania interfejsu API programu Microsoft Graph lub dowolnego interfejsu API sieci web.

![Jak działa przykładowa aplikacja wygenerowana w tym przewodniku Szybki start](media/quickstart-v2-javascript/javascriptspa-intro.png)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Rejestrowanie i Pobierz aplikację Szybki Start
> Istnieją dwie opcje uruchamiania aplikacji Szybki start:
> * [Ekspresowo] [Opcja 1: zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Ręcznie] [Opcja 2: zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opcja 1: zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu
>
> 1. Przejdź do witryny [Azure Portal — Rejestracja aplikacji (wersja zapoznawcza)](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs).
> 1. Wprowadź nazwę aplikacji i kliknij pozycję **Zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby jednym kliknięciem pobrać i automatycznie skonfigurować nową aplikację.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2: zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Rejestrowanie aplikacji
>
> 1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) zarejestrować aplikację.
> 1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
> 1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory**, a następnie pozycję **Rejestracje aplikacji (wersja zapoznawcza) > Nowa rejestracja**.
> 1. Gdy **rejestrowania aplikacji** zostanie wyświetlona strona, wprowadź nazwę aplikacji.
> 1. W obszarze **obsługiwane typy kont**, wybierz opcję **kont w dowolnym katalogu organizacji i osobistych kont Microsoft**.
> 1. Wybierz **Web** platformy w obszarze **identyfikator URI przekierowania** sekcji, a następnie ustaw wartość `http://localhost:30662/`.
> 1. Po zakończeniu wybierz pozycję **Rejestruj**.  W aplikacji **Przegląd** strony, zanotuj **identyfikator aplikacji (klienta)** wartość.
> 1. Ten przewodnik Szybki Start wymaga [niejawne udzielić przepływ](v2-oauth2-implicit-grant-flow.md) włączenia. W okienku nawigacji po lewej stronie w zarejestrowanej aplikacji wybierz **uwierzytelniania**.
> 1. W **Zaawansowane ustawienia**w obszarze **przyznawanie niejawne**, włączyć zarówno **tokeny Identyfikatora** i **tokeny dostępu** pola wyboru. Identyfikator tokenów i tokenów dostępu są wymagane, ponieważ ta aplikacja wymaga logowania użytkowników i wywoływać interfejs API.
> 1. Wybierz pozycję **Zapisz**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1: Konfigurowanie aplikacji w witrynie Azure Portal
> Dla przykładu kodu dla tego przewodnika Szybki Start do pracy, należy dodać przekierowania URI jako `http://localhost:30662/` i włączyć **przyznawanie niejawne**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź zmiany automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowano](media/quickstart-v2-javascript/green-check.png) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów.

#### <a name="step-2-download-the-project"></a>Krok 2: Pobieranie projektu

Można jedna z tych opcji, które są odpowiednie do swojego środowiska projektowego.
* [Pobierz pliki projektów core - dla serwera sieci web, takich jak Node.js](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)
* [Pobierz projekt programu Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)

Wyodrębnij plik zip do folderu lokalnego, na przykład **C:\Azure-Samples**.

#### <a name="step-3-configure-your-javascript-app"></a>Krok 3: Konfigurowanie aplikacji języka JavaScript

> [!div renderon="docs"]
> Edytuj `index.html` i ustaw `clientID` i `authority` wartości w obszarze `applicationConfig`.

> [!div class="sxs-lookup" renderon="portal"]
> Edytuj `index.html` i Zastąp `applicationConfig` za pomocą:

```javascript
var applicationConfig = {
    clientID: "Enter_the_Application_Id_here",
    authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
> [!div renderon="docs"]
>
> Gdzie:
> - `Enter_the_Application_Id_here` jest **identyfikatorem aplikacji (klienta)** dla zarejestrowanej aplikacji.
> - `Enter_the_Tenant_Info_Here` to wartość ustawiana na jedną z następujących opcji:
>   - Jeśli aplikacja obsługuje tryb **Konta w tym katalogu organizacyjnym**, zastąp tę wartość za pomocą wartości **Identyfikator dzierżawy** lub **Nazwa dzierżawy** (na przykład contoso.microsoft.com)
>   - Jeśli aplikacja obsługuje tryb**Konta w dowolnym katalogu organizacyjnym**, zastąp tę wartość za pomocą wartości `organizations`
>   - Jeśli aplikacja obsługuje tryb **Konta w moim katalogu organizacyjnym i osobiste konta Microsoft**, zastąp tę wartość za pomocą wartości `common`
>
> > [!TIP]
> > Aby znaleźć wartości **Identyfikator aplikacji (klienta)**, **Identyfikator katalogu (dzierżawy)** i **Obsługiwane typy kont**, przejdź do strony **Przegląd** w witrynie Azure Portal.

> [!NOTE]
> Serwer jest skonfigurowany do nasłuchiwania na porcie 30662 w *server.js* w pliku [Node.js](https://nodejs.org/en/download/) projektu i *.csproj* w pliku [programu Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)projektu.
>

#### <a name="step-4-run-the-project"></a>Krok 4: Uruchamianie projektu

* Jeśli używasz środowiska Node.js:

    1. Uruchom następujące polecenie z katalogu projektu do uruchomienia serwera:

        ```batch
        npm install
        node server.js
        ```

    1. Otwórz przeglądarkę internetową i przejdź do `http://localhost:30662/`.
    1. Kliknij przycisk **Sign In** przycisk, aby rozpocząć, zaloguj się, a następnie wywołać interfejsu API Microsoft Graph.


* Jeśli używasz programu Visual Studio, upewnij się, że wybierz rozwiązanie projektu, a następnie naciśnij klawisz **F5** Aby uruchomić projekt.

## <a name="more-information"></a>Więcej informacji

### <a name="msaljs"></a>*msal.js*

Biblioteka MSAL jest biblioteki używane na potrzeby logowania użytkowników i żądania tokenów umożliwiający dostęp do interfejsu API chronionego przez Microsoft Azure Active Directory (Azure AD). Tego przewodnika Szybki Start *index.html* zawiera odwołanie do biblioteki:

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.2.3/js/msal.min.js"></script>
```

Alternatywnie Jeśli masz zainstalowany węzeł, możesz ją pobrać za pośrednictwem Menedżera npm:

```batch
npm install msal
```

### <a name="msal-initialization"></a>Inicjowanie biblioteki MSAL

Kodu szybkiego startu pokazuje również, jak zainicjować biblioteki:

```javascript
var myMSALObj = new Msal.UserAgentApplication(applicationConfig.clientID, applicationConfig.authority, acquireTokenRedirectCallBack, {storeAuthStateInCookie: true, cacheLocation: "localStorage"});
```

> |Lokalizacja  |  |
> |---------|---------|
> |`ClientId`     |Identyfikator aplikacji z aplikacji zarejestrowanej w witrynie Azure Portal|
> |`authority`    |Jest to adres URL urzędu. Przekazywanie *null* ustawia domyślnego urzędu `https://login.microsoftonline.com/common`. Jeśli Twoja aplikacja jest pojedynczej dzierżawy (docelowych kont w tylko jednym katalogu), ustaw tę wartość na `https://login.microsoftonline.com/<tenant name or ID>`|
> |`tokenReceivedCallback`| Metoda wywołania zwrotnego wywoływana po uwierzytelniania przekierowuje do aplikacji. W tym miejscu `acquireTokenRedirectCallBack` jest przekazywany. Jest to wartość null, jeśli za pomocą loginPopup.|
> |`options`  |Kolekcja parametrów opcjonalnych. W tym przypadku `storeAuthStateInCookie` i `cacheLocation` są opcjonalne dla konfiguracji. Zobacz [wiki](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/MSAL-basics#configuration-options) Aby uzyskać więcej informacji na temat opcji. |

### <a name="sign-in-users"></a>Logowanie użytkowników

Poniższy fragment kodu pokazuje, jak logować użytkowników:

```javascript
myMSALObj.loginPopup(applicationConfig.graphScopes).then(function (idToken) {
    //Callback code here
})
```

> |Lokalizacja  |  |
> |---------|---------|
> | `scopes`   | (Opcjonalnie) Zawiera zakresy są żądane zgody użytkownika podczas logowania. Na przykład `[ "user.read" ]` dla programu Microsoft Graph lub `[ "<Application ID URL>/scope" ]` dla niestandardowych interfejsów API sieci Web (czyli `api://<Application ID>/access_as_user` ). W tym miejscu `applicationConfig.graphScopes` jest przekazywany. |

> [!TIP]
> Alternatywnie, możesz chcieć użyć `loginRedirect` metodę, aby przekierować bieżącej strony do strony logowania, zamiast oknie podręcznym.

### <a name="request-tokens"></a>Żądania tokenów

Biblioteka MSAL ma trzy metody używane do uzyskania tokenów: `acquireTokenRedirect`, `acquireTokenPopup` i `acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>Dyskretne pobieranie tokenu użytkownika

`acquireTokenSilent` Obsługiwała pozyskanie tokenu i wznowienie bez żadnej interakcji użytkownika. Po `loginRedirect` lub `loginPopup` metoda jest wykonywana po raz pierwszy `acquireTokenSilent` jest metodą, często używane do uzyskiwania tokenów, które są używane do dostępu do chronionych zasobów dla kolejnych wywołań. Do żądania lub odnowienia tokenów wywołań dyskretnie.

```javascript
myMSALObj.acquireTokenSilent(applicationConfig.graphScopes).then(function (accessToken) {
    // Callback code here
})
```

> |Lokalizacja  |  |
> |---------|---------|
> | `scopes`   | Zawiera zakresy żądanego ma zostać zwrócone w tokenie dostępu dla interfejsu API. Na przykład `[ "user.read" ]` dla programu Microsoft Graph lub `[ "<Application ID URL>/scope" ]` dla niestandardowych interfejsów API sieci Web (czyli `api://<Application ID>/access_as_user`). W tym miejscu `applicationConfig.graphScopes` jest przekazywany.|

#### <a name="get-a-user-token-interactively"></a>Interaktywne pobieranie tokenu użytkownika

Istnieją sytuacje, w których należy wymusić użytkownikom na interakcję z punktu końcowego v2.0 usługi Azure AD. Na przykład:
* Użytkownicy może być konieczne ponowne wprowadzenie poświadczeń, ponieważ ich hasło wygasło
* Aplikacja żąda dostępu do zakresów dodatkowych zasobów, które użytkownik musi wyrazić zgodę na
* Wymagane jest uwierzytelnianie dwuetapowe.

Zwyczajowego wzorca zalecane w przypadku większości aplikacji jest wywołanie `acquireTokenSilent` po pierwsze, następnie przechwycić wyjątek, a następnie wywołać `acquireTokenRedirect` (lub `acquireTokenPopup`) do uruchomienia interaktywnego żądania.

Wywoływanie `acquireTokenPopup(scope)` wyniki w oknie podręcznym, aby zalogować się (lub `acquireTokenRedirect(scope)` skutkuje przekierowywanie użytkowników do punktu końcowego v2.0 usługi Azure AD) w przypadku, gdy użytkownicy muszą wchodzić w interakcje potwierdzając swoje poświadczenia, zapewniając zgody do wymaganego zasobu lub Kończenie uwierzytelniania dwuskładnikowego.

```javascript
myMSALObj.acquireTokenPopup(applicationConfig.graphScopes).then(function (accessToken) {
    // Callback code here
})
```

> [!NOTE]
> Ten przewodnik Szybki Start używa `loginRedirect` i `acquireTokenRedirect` metody, gdy okno przeglądarki, używany jest program Internet Explorer ze względu na [znany problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) związane z obsługi okna podręcznego przez przeglądarkę Internet Explorer.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać bardziej szczegółowy przewodnik krok po kroku na temat tworzenia aplikacji dla tego przewodnika Szybki Start Wypróbuj samouczek JavaScript poniżej.

### <a name="learn-the-steps-to-create-the-application-for-this-quickstart"></a>Opis czynności niezbędnych do tworzenia aplikacji dla tego przewodnika Szybki Start

> [!div class="nextstepaction"]
> [Call Graph API tutorial (Samouczek dotyczący wywoływania interfejsu API programu Graph)](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

### <a name="browse-the-msal-repo-for-documentation-faq-issues-and-more"></a>Przeglądaj repozytorium biblioteki MSAL dla dokumentacji, często zadawane pytania, problemy i

> [!div class="nextstepaction"]
> [repozytorium GitHub msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)
