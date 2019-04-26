---
title: Program Microsoft identity platformy JavaScript Przewodnik Szybki Start | Azure
description: Dowiedz się, jak wywołać interfejs API, które wymagają tokenów dostępu przez platforma tożsamości usługi Microsoft aplikacji JavaScript.
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
ms.openlocfilehash: 2021c5028637a6f7e732df61b6f7c034ef79324f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60443006"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-single-page-application-spa"></a>Szybki start: Logowania użytkowników i uzyskiwanie tokenu dostępu z poziomu aplikacji jednostronicowej JavaScript (SPA)

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

W tym przewodniku Szybki Start dowiesz się, jak użyć przykładowego kodu, który demonstruje, jak JavaScript aplikacja jednostronicowa (SPA) Zaloguj się na kontach osobistych, praca i kont służbowych i uzyskania tokenu dostępu do wywołania interfejsu API programu Microsoft Graph lub dowolnego interfejsu API sieci web.

![Pokazuje, jak działa przykładowej aplikacji wygenerowane przez ten przewodnik Szybki Start](media/quickstart-v2-javascript/javascriptspa-intro.svg)

## <a name="prerequisites"></a>Wymagania wstępne

W tym przewodniku Szybki Start potrzebne są następujące ustawienia:
* Aby uruchomić projekt za pomocą serwera node.js
    * Zainstalować środowisko [Node.js](https://nodejs.org/en/download/).
    * Zainstaluj [programu Visual Studio Code](https://code.visualstudio.com/download) edytowania plików projektu
* Aby uruchomić projekt jako rozwiązanie w programie Visual Studio, zainstaluj [programu Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Rejestrowanie i pobieranie aplikacji Szybki start
> Istnieją dwie opcje uruchamiania aplikacji Szybki start:
> * [Ekspresowo] [Opcja 1: zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Ręcznie] [Opcja 2: zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opcja 1: zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu
>
> 1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
> 1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
> 1. Przejdź do nowego [witryna Azure portal — rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) okienka.
> 1. Wprowadź nazwę aplikacji i kliknij pozycję **Zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby jednym kliknięciem pobrać i automatycznie skonfigurować nową aplikację.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2: zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Rejestrowanie aplikacji
>
> 1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
> 1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
> 1. Przejdź do platforma tożsamości firmy Microsoft dla deweloperów [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) strony.
> 1. Wybierz **nowej rejestracji**.
> 1. Po wyświetleniu strony **Rejestrowanie aplikacji** wprowadź nazwę aplikacji.
> 1. W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**.
> 1. Wybierz platformę **Internet** w sekcji **Identyfikator URI przekierowania** i ustaw wartość na `http://localhost:30662/`.
> 1. Po zakończeniu wybierz pozycję **Rejestruj**.  Na stronie **Przegląd** aplikacji zanotuj wartość **Identyfikator aplikacji (klienta)**.
> 1. Ten przewodnik Szybki start wymaga włączenia [przepływu niejawnego udzielenia](v2-oauth2-implicit-grant-flow.md). W okienku nawigacji po lewej stronie w zarejestrowanej aplikacji wybierz pozycję **Uwierzytelnianie**.
> 1. Na stronie **Ustawienia zaawansowane** w obszarze **Niejawne udzielenie** zaznacz obydwa pola wyboru: **Tokeny identyfikatorów** i **Tokeny dostępu**. Tokeny identyfikatorów i tokeny dostępu są wymagane, ponieważ ta aplikacja musi zalogować użytkowników i wywołać interfejs API.
> 1. Wybierz pozycję **Zapisz**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1: Konfigurowanie aplikacji w witrynie Azure Portal
> Aby kod przykładowy z tego przewodnika Szybki start działał, dodaj identyfikator URI przekierowania w formacie `http://localhost:30662/` i włącz pozycję **Niejawne udzielenie**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź zmiany automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowano](media/quickstart-v2-javascript/green-check.png) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów.

#### <a name="step-2-download-the-project"></a>Krok 2: Pobieranie projektu

Możesz wybrać jedną z poniższych opcji, właściwą dla środowiska deweloperskiego.
* [Pobierz podstawowe pliki projektu core — dla serwera internetowego, takiego jak Node.js](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)
* [Pobierz projekt programu Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)

Wyodrębnij plik zip do folderu lokalnego, na przykład **C:\Azure-Samples**.
Aby otworzyć pliki w folderze, użyj edytorem takim jak [programu Visual Studio Code](https://code.visualstudio.com/).

#### <a name="step-3-configure-your-javascript-app"></a>Krok 3: Konfigurowanie aplikacji języka JavaScript

> [!div renderon="docs"]
> W folderze *JavaScriptSPA*, Edytuj `index.html` i ustaw `clientID` i `authority` wartości w obszarze `applicationConfig`.

> [!div class="sxs-lookup" renderon="portal"]
> W folderze *JavaScriptSPA*, Edytuj `index.html` i Zastąp `applicationConfig` za pomocą:

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

#### <a name="step-4-run-the-project"></a>Krok 4: Uruchamianie projektu

* Jeśli używasz [Node.js](https://nodejs.org/en/download/):

    1. Uruchom następujące polecenie z katalogu projektu w celu uruchomienia serwera:

        ```batch
        npm install
        node server.js
        ```

    1. Otwórz przeglądarkę internetową i przejdź pod adres `http://localhost:30662/`.
    1. Kliknij przycisk **Zaloguj się**, aby rozpocząć logowanie, a następnie wywołaj interfejsu API programu Microsoft Graph.


* Jeśli przy użyciu [programu Visual Studio](https://visualstudio.microsoft.com/downloads/), upewnij się, że wybierz rozwiązanie projektu, a następnie naciśnij klawisz **F5** Aby uruchomić projekt.

Po załadowaniu aplikacji przeglądarki kliknij **Sign In**.  Przy pierwszym logowaniu się, zostanie wyświetlony monit Twojej zgody, aby umożliwić aplikacji dostęp do Twojego profilu i logowanie się w. Na temat pomyślnego logowania powinny być widoczne wyświetlany na stronie informacji o profilu użytkownika.

## <a name="more-information"></a>Więcej informacji

### <a name="msaljs"></a>*msal.js*

Biblioteka MSAL jest biblioteki używane na potrzeby logowania użytkowników i żądania tokenów umożliwiający dostęp do interfejsu API chronionego przez platforma tożsamości usługi Microsoft. Plik *index.html* tego przewodnika Szybki start zawiera odwołanie do biblioteki:

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.2.4/js/msal.min.js"></script>
```

Jeśli masz zainstalowane środowisko Node, możesz ją pobrać za pośrednictwem menedżera npm:

```batch
npm install msal
```

### <a name="msal-initialization"></a>Inicjowanie biblioteki MSAL

Kod w tym przewodniku Szybki start pokazuje również, jak zainicjować bibliotekę:

```javascript
var myMSALObj = new Msal.UserAgentApplication(applicationConfig.clientID, applicationConfig.authority, acquireTokenRedirectCallBack, {storeAuthStateInCookie: true, cacheLocation: "localStorage"});
```

> |Lokalizacja  |  |
> |---------|---------|
> |`ClientId`     |Identyfikator aplikacji z aplikacji zarejestrowanej w witrynie Azure Portal|
> |`authority`    |Jest to adres URL urzędu. Przekazanie wartości *null* powoduje ustawienie domyślnego urzędu na `https://login.microsoftonline.com/common`. Jeśli aplikacja obsługuje pojedynczą dzierżawę (konta docelowe tylko w jednym katalogu), ustaw tę wartość na `https://login.microsoftonline.com/<tenant name or ID>`|
> |`tokenReceivedCallback`| Metoda wywołania zwrotnego wywołana po uwierzytelnieniu powoduje przekierowanie z powrotem do aplikacji. W tym miejscu jest przekazywana wartość `acquireTokenRedirectCallBack`. W przypadku użycia elementu loginPopup jest to wartość null.|
> |`options`  |Kolekcja parametrów opcjonalnych. W tym przypadku elementy `storeAuthStateInCookie` i `cacheLocation` stanowią konfigurację opcjonalną. Zobacz stronę [wiki](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/MSAL-basics#configuration-options), aby uzyskać więcej informacji na temat opcji. |

### <a name="sign-in-users"></a>Logowanie użytkowników

Poniższy fragment kodu pokazuje, jak logować użytkowników:

```javascript
myMSALObj.loginPopup(applicationConfig.graphScopes).then(function (idToken) {
    //Callback code here
})
```

> |Lokalizacja  |  |
> |---------|---------|
> | `scopes`   | (Opcjonalnie) Zawiera żądane zakresy wymagające zgody użytkownika podczas logowania. Na przykład zakres `[ "user.read" ]` dla programu Microsoft Graph lub zakres `[ "<Application ID URL>/scope" ]` dla niestandardowych interfejsów internetowych API (czyli `api://<Application ID>/access_as_user`). W tym miejscu jest przekazywana wartość `applicationConfig.graphScopes`. |

> [!TIP]
> Alternatywnie możesz użyć metody `loginRedirect` w celu przekierowania bieżącej strony do strony logowania, zamiast okna wyskakującego.

### <a name="request-tokens"></a>Żądanie tokenów

Biblioteka MSAL oferuje trzy metody uzyskiwania tokenów: `acquireTokenRedirect`, `acquireTokenPopup` i `acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>Dyskretne pobieranie tokenu użytkownika

Metoda `acquireTokenSilent` obsługuje uzyskiwanie i odnawianie tokenów bez żadnej interakcji z użytkownikiem. Po wykonaniu metody `loginRedirect` lub `loginPopup` po raz pierwszy często stosuje się metodę `acquireTokenSilent` do uzyskiwania tokenów, które są używane w celu uzyskiwania dostępu do chronionych zasobów w kolejnych wywołaniach. Wywołania żądania lub odnowienia tokenów są wykonywane dyskretnie.

```javascript
myMSALObj.acquireTokenSilent(applicationConfig.graphScopes).then(function (accessToken) {
    // Callback code here
})
```

> |Lokalizacja  |  |
> |---------|---------|
> | `scopes`   | Zawiera zakresy żądane na potrzeby zwracania w tokenie dostępu dla interfejsu API. Na przykład zakres `[ "user.read" ]` dla programu Microsoft Graph lub zakres `[ "<Application ID URL>/scope" ]` dla niestandardowych interfejsów internetowych API (czyli `api://<Application ID>/access_as_user`). W tym miejscu jest przekazywana wartość `applicationConfig.graphScopes`.|

#### <a name="get-a-user-token-interactively"></a>Interaktywne pobieranie tokenu użytkownika

Istnieją sytuacje, w których należy wymusić użytkownikom na interakcję z punktem końcowym platforma tożsamości firmy Microsoft. Na przykład:
* Użytkownicy muszą ponownie wprowadzić poświadczenia, ponieważ hasło wygasło.
* Aplikacja żąda dostępu do dodatkowych zakresów zasobów wymagających zgody użytkownika.
* Wymagane jest uwierzytelnianie dwuetapowe.

W przypadku większości aplikacji zwykle zalecane jest wywołanie najpierw metody `acquireTokenSilent`, przechwycenie wyjątku, a następnie wywołanie metody `acquireTokenRedirect` (lub `acquireTokenPopup`) w celu uruchomienia żądania.

Wywoływanie `acquireTokenPopup(scope)` wyniki w oknie podręcznym, aby zalogować się (lub `acquireTokenRedirect(scope)` skutkuje przekierowywanie użytkowników do endpoint platforma tożsamości firmy Microsoft) w przypadku, gdy użytkownicy muszą wchodzić w interakcje, sprawdzając swoich poświadczeń, zapewniając zgody wymagane zasób lub korzystanie z uwierzytelniania dwuskładnikowego.

```javascript
myMSALObj.acquireTokenPopup(applicationConfig.graphScopes).then(function (accessToken) {
    // Callback code here
})
```

> [!NOTE]
> W tym przewodniku Szybki start są używane metody `loginRedirect` i `acquireTokenRedirect`, jeśli stosowana przeglądarka to program Internet Explorer, ze względu na [znany problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) związany z obsługą okien wyskakujących przez przeglądarkę Internet Explorer.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać bardziej szczegółowe wskazówki krok po kroku dotyczące kompilowania aplikacji dla tego przewodnika Szybki start, wypróbuj poniższy samouczek dla języka JavaScript.

### <a name="learn-the-steps-to-create-the-application-for-this-quickstart"></a>Dowiedz się więcej na temat czynności tworzenia aplikacji użytej w tym przewodniku Szybki start

> [!div class="nextstepaction"]
> [Samouczek logować się i wywołania MS Graph](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

### <a name="browse-the-msal-repo-for-documentation-faq-issues-and-more"></a>Przejrzyj repozytorium biblioteki MSAL, aby znaleźć dokumentację, odpowiedzi na często zadawane pytania, opis problemów i inne informacje

> [!div class="nextstepaction"]
> [Repozytorium MSAL.js GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js)
