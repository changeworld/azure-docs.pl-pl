---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 68598d4bb7fb9fd928a7b664e6ce0c02220ca4bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60297972"
---
## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Biblioteka Microsoft Authentication Library (MSAL) umożliwia logowanie użytkownika

1. Dodaj następujący kod, aby Twoje `index.html` plików w ramach `<script></script>` tagi:

```javascript
//Pass null for default authority (https://login.microsoftonline.com/common)
var myMSALObj = new Msal.UserAgentApplication(applicationConfig.clientID, null, acquireTokenRedirectCallBack,
    {storeAuthStateInCookie: true, cacheLocation: "localStorage"});

function signIn() {
    myMSALObj.loginPopup(applicationConfig.graphScopes).then(function (idToken) {
        //Login Success
        showWelcomeMessage();
        acquireTokenPopupAndCallMSGraph();
    }, function (error) {
        console.log(error);
    });
}

function acquireTokenPopupAndCallMSGraph() {
    //Call acquireTokenSilent (iframe) to obtain a token for Microsoft Graph
    myMSALObj.acquireTokenSilent(applicationConfig.graphScopes).then(function (accessToken) {
        callMSGraph(applicationConfig.graphEndpoint, accessToken, graphAPICallback);
    }, function (error) {
        console.log(error);
        // Call acquireTokenPopup (popup window) in case of acquireTokenSilent failure due to consent or interaction required ONLY
        if (error.indexOf("consent_required") !== -1 || error.indexOf("interaction_required") !== -1 || error.indexOf("login_required") !== -1) {
            myMSALObj.acquireTokenPopup(applicationConfig.graphScopes).then(function (accessToken) {
                callMSGraph(applicationConfig.graphEndpoint, accessToken, graphAPICallback);
            }, function (error) {
                console.log(error);
            });
        }
    });
}

function graphAPICallback(data) {
    //Display user data on DOM
    var divWelcome = document.getElementById('WelcomeMessage');
    divWelcome.innerHTML += " to Microsoft Graph API!!";
    document.getElementById("json").innerHTML = JSON.stringify(data, null, 2);
}

function showWelcomeMessage() {
    var divWelcome = document.getElementById('WelcomeMessage');
    divWelcome.innerHTML += 'Welcome ' + myMSALObj.getUser().name;
    var loginbutton = document.getElementById('SignIn');
    loginbutton.innerHTML = 'Sign Out';
    loginbutton.setAttribute('onclick', 'signOut();');
}

// This function can be removed if you do not need to support IE
function acquireTokenRedirectAndCallMSGraph() {
    //Call acquireTokenSilent (iframe) to obtain a token for Microsoft Graph
    myMSALObj.acquireTokenSilent(applicationConfig.graphScopes).then(function (accessToken) {
      callMSGraph(applicationConfig.graphEndpoint, accessToken, graphAPICallback);
    }, function (error) {
        console.log(error);
        //Call acquireTokenRedirect in case of acquireToken Failure
        if (error.indexOf("consent_required") !== -1 || error.indexOf("interaction_required") !== -1 || error.indexOf("login_required") !== -1) {
            myMSALObj.acquireTokenRedirect(applicationConfig.graphScopes);
        }
    });
}

function acquireTokenRedirectCallBack(errorDesc, token, error, tokenType) {
    if (tokenType === "access_token") {
        callMSGraph(applicationConfig.graphEndpoint, token, graphAPICallback);
    } else {
        console.log("token type is:"+tokenType);
    }
}


// Browser check variables
var ua = window.navigator.userAgent;
var msie = ua.indexOf('MSIE ');
var msie11 = ua.indexOf('Trident/');
var msedge = ua.indexOf('Edge/');
var isIE = msie > 0 || msie11 > 0;
var isEdge = msedge > 0;

//If you support IE, our recommendation is that you sign-in using Redirect APIs
//If you as a developer are testing using Microsoft Edge InPrivate mode, please add "isEdge" to the if check
if (!isIE) {
    if (myMSALObj.getUser()) {// avoid duplicate code execution on page load in case of iframe and popup window.
        showWelcomeMessage();
        acquireTokenPopupAndCallMSGraph();
    }
} else {
    document.getElementById("SignIn").onclick = function () {
        myMSALObj.loginRedirect(applicationConfig.graphScopes);
    };
    if (myMSALObj.getUser() && !myMSALObj.isCallback(window.location.hash)) {// avoid duplicate code execution on page load in case of iframe and popup window.
        showWelcomeMessage();
        acquireTokenRedirectAndCallMSGraph();
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>Więcej informacji

Po kliknięciu **Sign In** przycisku po raz pierwszy `signIn` wywołania metody `loginPopup` do logowania użytkownika. Ta metoda powoduje otwarcie okna podręcznego z *punktu końcowego platformy tożsamości firmy Microsoft* Monituj i sprawdzanie poprawności poświadczeń użytkownika. W wyniku pomyślne logowanie, użytkownik jest przekierowany z powrotem do oryginalnego *index.html* strony i token odebraniu przetworzonych przez `msal.js` i informacje zawarte w tokenie są buforowane. Token ten jest znany jako *tokenu Identyfikacyjnego* i zawiera podstawowe informacje o użytkowniku, takie jak nazwa wyświetlana użytkownika. Jeśli planujesz użyć wszystkie dane udostępniane przez ten token do żadnych celów, musisz upewnij się, że token ten jest weryfikowane przez serwer wewnętrznej bazy danych w celu zagwarantowania, że token został wystawiony do prawidłowego użytkownika dla aplikacji.

SPA wygenerowane przez ten przewodnik dotyczący wywołania `acquireTokenSilent` i/lub `acquireTokenPopup` uzyskania *token dostępu* umożliwia tworzenie zapytań dotyczących interfejsu API programu Microsoft Graph, aby uzyskać informacje o profilu użytkownika. Przykład sprawdza poprawność tokenu Identyfikatora, należy spojrzeć na [to](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "przykładowe active-directory-javascript-singlepageapp-dotnet-webapi-v2 GitHub") ASP korzysta z przykładowej aplikacji w usłudze GitHub — przykład Interfejs API sieci web platformy .NET dla walidacji tokenów.

#### <a name="getting-a-user-token-interactively"></a>Interaktywne pobieranie tokenu użytkownika

Po początkowej logowania, nie chcesz o konieczności ponownego uwierzytelnienia za każdym razem, gdy potrzebują do wysłania żądania tokenu dostępu do zasobu — więc *acquireTokenSilent* powinny być używane w większości przypadków do uzyskania tokenów. Istnieją jednak sytuacje, trzeba wymusić użytkownikom na interakcję z punktem końcowym platforma tożsamości firmy Microsoft — niektóre przykłady to:

- Użytkownicy muszą ponownie wprowadzić poświadczenia, ponieważ hasło wygasło.
- Aplikacja żąda dostępu do zasobów wymagającego zgody użytkownika.
- Wymagane jest uwierzytelnianie dwuetapowe.

Wywoływanie *acquireTokenPopup(scope)* wyniki w oknie podręcznym (lub *acquireTokenRedirect(scope)* skutkuje przekierowywanie użytkowników do endpoint platforma tożsamości firmy Microsoft) gdzie użytkownicy musieli współpracują ze sobą, potwierdzenie poświadczeń, zapewniając zgody do wymaganych zasobów albo ukończenie uwierzytelniania dwuskładnikowego.

#### <a name="getting-a-user-token-silently"></a>Dyskretne pobieranie tokenu użytkownika

Metoda `acquireTokenSilent` obsługuje uzyskiwanie i odnawianie tokenów bez żadnej interakcji z użytkownikiem. Po `loginPopup` (lub `loginRedirect`) jest wykonywana po raz pierwszy `acquireTokenSilent` jest metodą, często używane do uzyskiwania tokenów, które umożliwiają dostęp do chronionych zasobów dla kolejnych wywołań — jak do żądania lub odnowienia tokenów wywołań dyskretnie.
`acquireTokenSilent` może się nie powieść w niektórych przypadkach — na przykład użytkownika hasło wygasło. Aplikacja może obsłużyć ten wyjątek na dwa sposoby:

1. Wywołanie `acquireTokenPopup` natychmiast, które powoduje monitowanie użytkownika do logowania. Ten wzorzec jest często używana w aplikacji w trybie online w przypadku, gdy brak nieuwierzytelnione zawartości w aplikacji dostępne dla użytkownika. W przykładzie, wygenerowane za pomocą tego Instalatora z przewodnikiem użyto tego wzorca.

2. Aplikacje może być wizualne oznaczenie do użytkownika, który interakcyjnego logowania jest wymagana, dzięki czemu użytkownik może wybrać w odpowiednim czasie, aby zalogować się lub aplikacji można ponowić próbę `acquireTokenSilent` w późniejszym czasie. Jest to często używane, po użytkownik może użyć innych funkcji aplikacji bez zakłócana — na przykład jest nieuwierzytelnione zawartość dostępna w aplikacji. W tym przypadku użytkownik może wybrać, gdy mają logować się do uzyskania dostępu do chronionego zasobu lub odświeżyć nieaktualne informacje.

> [!NOTE]
> Powyższy kod używa `loginRedirect` i `acquireTokenRedirect` metody, gdy okno przeglądarki, używany jest program Internet Explorer ze względu na [znany problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) związane z obsługi okna podręcznego przez przeglądarkę Internet Explorer.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Wywołanie interfejsu API programu Microsoft Graph przy użyciu tokenu, który został uzyskany

Dodaj następujący kod, aby Twoje `index.html` plików w ramach `<script></script>` tagi:

```javascript
function callMSGraph(theUrl, accessToken, callback) {
    var xmlHttp = new XMLHttpRequest();
    xmlHttp.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200)
            callback(JSON.parse(this.responseText));
    }
    xmlHttp.open("GET", theUrl, true); // true for asynchronous
    xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
    xmlHttp.send();
}
```
<!--start-collapse-->

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Więcej informacji na temat wywołania chronionego interfejsu API REST

W przykładowej aplikacji utworzony w tym przewodniku `callMSGraph()` metoda umożliwia HTTP `GET` żądania względem chronionego zasobu, który wymaga tokenu, a następnie wróć zawartości do obiektu wywołującego. Metoda ta umożliwia dodanie uzyskano token w *nagłówek autoryzacji HTTP*. Dla przykładowej aplikacji utworzone przez tego przewodnika, zasób jest interfejsu API programu Microsoft Graph *mnie* punktu końcowego — Wyświetla informacje o profilu użytkownika.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Dodaj metodę, aby się wylogować użytkownika

Dodaj następujący kod, aby Twoje `index.html` plików w ramach `<script></script>` tagi:

```javascript
/**
 * Sign out the user
 */
 function signOut() {
     myMSALObj.logout();
 }
```
