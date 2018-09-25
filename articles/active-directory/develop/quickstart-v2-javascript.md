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
ms.openlocfilehash: 1b884571707aab71e8a8d124ba68f938e5a63a43
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063748"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-application"></a>Szybki Start: Logowania użytkowników i uzyskiwanie tokenu dostępu z poziomu aplikacji JavaScript

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

W tym przewodniku Szybki Start dowiesz się, jak użyć przykładowego kodu, który demonstruje, jak JavaScript, w aplikacji jednostronicowej (SPA) można się na kontach osobistych, pracy i kont służbowych, uzyskania tokenu dostępu do wywołania interfejsu API programu Microsoft Graph lub dowolnego interfejsu API sieci web.

![Jak działa przykładowej aplikacji wygenerowane przez ten przewodnik Szybki Start](media/quickstart-v2-javascript/javascriptspa-intro.png)

> [!div renderon="docs"]
> ## <a name="register-your-application-and-download-your-quickstart-app"></a>Zarejestruj swoją aplikację i Pobierz aplikację Szybki Start
>
> ### <a name="step-1-register-your-application"></a>Krok 1: Rejestrowanie aplikacji
>
> 1. Przejdź do [portalu rejestracji aplikacji Microsoft](https://apps.dev.microsoft.com/portal/register-app) zarejestrować aplikację.
> 1. W **Nazwa aplikacji** wprowadź nazwę aplikacji.
> 1. Upewnij się, że **instrukcje konfiguracji** pole wyboru jest wyczyszczone, a następnie wybierz **Utwórz**.
> 1. Kliknij przycisk **Dodaj platformy**, a następnie wybierz **Web**.
> 1. Upewnij się, że **Zezwalaj na niejawny przepływ** jest *zaznaczone*.
> 1. W obszarze **adresy URL przekierowania** Dodaj `http://localhost:30662/`.
> 1. Kliknij pozycję **Zapisz**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Krok 1: Konfigurowanie aplikacji w witrynie Azure portal
> Dla przykładu kodu dla tego przewodnika Szybki Start do pracy, należy dodać przekierowania URI jako `http://localhost:30662/` i włączyć **przyznawanie niejawne**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadzenie tych zmian]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowane](media/quickstart-v2-javascript/green-check.png) aplikacja jest skonfigurowana za pomocą tych atrybutów

#### <a name="step-2-download-the-project"></a>Krok 2: Pobieranie projektu

Można jedna z tych opcji, które są odpowiednie do swojego środowiska projektowego.
* [Pobierz pliki projektów core - dla serwera sieci web, takich jak Node.js](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)
* [Pobieranie projektu programu Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)

Wyodrębnij plik zip do folderu lokalnego (na przykład **C:\Azure-Samples**).

#### <a name="step-3-configure-your-javascript-app"></a>Krok 3: Konfigurowanie aplikacji języka JavaScript

> [!div renderon="docs"]
> Edytuj `index.html` i Zastąp `Enter_the_Application_Id_here` w obszarze `applicationConfig` z Identyfikatorem aplikacji w aplikacji, które właśnie zostało zarejestrowane.

> [!div class="sxs-lookup" renderon="portal"]
> Edytuj `index.html` i Zastąp `applicationConfig` za pomocą:

```javascript
var applicationConfig = {
    clientID: "Enter_the_Application_Id_here",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
> [!NOTE]
>Jeśli używasz [Node.js](https://nodejs.org/en/download/), *server.js* plik jest skonfigurowany dla serwera, aby rozpocząć nasłuchiwania na porcie 30662.
> Jeśli używasz [programu Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), przykładowy kod *.csproj* plik jest skonfigurowany dla serwera, aby rozpocząć nasłuchiwania na porcie 30662.
>

#### <a name="step-4-run-the-project"></a>Krok 4: Uruchom projekt

Jeśli przy użyciu środowiska Node.js, w wierszu polecenia można uruchomić następujące z katalogu projektu do uruchomienia serwera:
 ```batch
 npm install
 node server.js
 ```
Otwórz przeglądarkę internetową i przejdź do `http://localhost:30662/`. Kliknij przycisk **Sign In** przycisk, aby rozpocząć, zaloguj się, a następnie wywołać interfejsu API Microsoft Graph.

Jeśli używasz programu Visual Studio, upewnij się, że wybierz rozwiązanie projektu, a następnie naciśnij klawisz **F5** Aby uruchomić projekt.

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
var myMSALObj = new Msal.UserAgentApplication(applicationConfig.clientID, null, acquireTokenRedirectCallBack, {storeAuthStateInCookie: true, cacheLocation: "localStorage"});
```

> |Lokalizacja  |  |
> |---------|---------|
> |`ClientId`     |Identyfikator aplikacji z aplikacji zarejestrowany w witrynie Azure portal|
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
> | `scopes`   | (Opcjonalnie) Zawiera zakresy są żądane zgody użytkownika w czasie logowania (np: `[ "user.read" ]` dla programu Microsoft Graph lub `[ "<Application ID URL>/scope" ]` dla niestandardowych interfejsów API sieci Web (czyli `api://<Application ID>/access_as_user` ). W tym miejscu `applicationConfig.graphScopes` jest przekazywany. |

> [!TIP]
> Alternatywnie, możesz chcieć użyć `loginRedirect` metodę, aby przekierować bieżącej strony do strony logowania, zamiast oknie podręcznym.


### <a name="request-tokens"></a>Żądania tokenów

Biblioteka MSAL ma trzy metody używane do uzyskania tokenów: `acquireTokenRedirect`, `acquireTokenPopup` i `acquireTokenSilent`:

#### <a name="get-a-user-token-silently"></a>Pobierz token użytkownika dyskretnie

`acquireTokenSilent` Obsługiwała pozyskanie tokenu i wznowienie bez żadnej interakcji użytkownika. Po `loginRedirect` lub `loginPopup` metoda jest wykonywana po raz pierwszy `acquireTokenSilent` jest metodą, często używane do uzyskiwania tokenów, które są używane do dostępu do chronionych zasobów dla kolejnych wywołań. Do żądania lub odnowienia tokenów wywołań dyskretnie.

```javascript
myMSALObj.acquireTokenSilent(applicationConfig.graphScopes).then(function (accessToken) {
    // Callback code here
})
```

> |Lokalizacja  |  |
> |---------|---------|
> | `scopes`   | Zawiera zakresy żądanego ma zostać zwrócone w tokenie dostępu dla interfejsu API (np: `[ "user.read" ]` dla programu Microsoft Graph lub `[ "<Application ID URL>/scope" ]` dla niestandardowych interfejsów API sieci Web (czyli `api://<Application ID>/access_as_user` ). W tym miejscu `applicationConfig.graphScopes` jest przekazywany.|

#### <a name="get-a-user-token-interactively"></a>Pobierz token użytkownika interaktywnego

 Istnieją sytuacje, w których należy wymusić użytkownikom na interakcję z punktu końcowego v2.0 usługi Azure AD. Na przykład:
* Użytkownicy może być konieczne ponowne wprowadzenie poświadczeń, ponieważ ich hasło wygasło
* Aplikacja żąda dostępu do zakresów dodatkowych zasobów, które użytkownik musi wyrazić zgodę na
* Uwierzytelnianie dwuskładnikowe jest wymagana

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
> [Wywołanie interfejsu API programu Graph w ramach samouczka](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

### <a name="browse-the-msal-repo-for-documentation-faq-issues-and-more"></a>Przeglądaj repozytorium biblioteki MSAL dla dokumentacji, często zadawane pytania, problemy i

> [!div class="nextstepaction"]
> [repozytorium GitHub msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
