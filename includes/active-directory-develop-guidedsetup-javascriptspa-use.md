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
ms.openlocfilehash: 8785f89a335f0ae7d983f267176da1656aee57a0
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65199335"
---
## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Biblioteka Microsoft Authentication Library (MSAL) umożliwia logowanie użytkownika

1. Dodaj następujący kod, aby Twoje `index.html` plików w ramach `<script></script>` tagi:

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "Enter_the_Application_Id_here"
            authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };

    var graphConfig = {
        graphMeEndpoint: "https://graph.microsoft.com/v1.0/me"
    };

    // this can be used for login or token request, however in more complex situations
    // this can have diverging options
    var requestObj = {
        scopes: ["user.read"]
    };

    var myMSALObj = new Msal.UserAgentApplication(msalConfig);
    // Register Callbacks for redirect flow
    myMSALObj.handleRedirectCallback(authRedirectCallBack);


    function signIn() {

        myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
            //Login Success
            showWelcomeMessage();
            acquireTokenPopupAndCallMSGraph();
        }).catch(function (error) {
            console.log(error);
        });
    }

    function acquireTokenPopupAndCallMSGraph() {
        //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
        myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
            callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
        }).catch(function (error) {
            console.log(error);
            // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
            // Call acquireTokenPopup(popup window)
            if (requiresInteraction(error.errorCode)) {
                myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
                    callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
                }).catch(function (error) {
                    console.log(error);
                });
            }
        });
    }


    function graphAPICallback(data) {
        document.getElementById("json").innerHTML = JSON.stringify(data, null, 2);
    }


    function showWelcomeMessage() {
        var divWelcome = document.getElementById('WelcomeMessage');
        divWelcome.innerHTML = 'Welcome ' + myMSALObj.getAccount().userName + "to Microsoft Graph API";
        var loginbutton = document.getElementById('SignIn');
        loginbutton.innerHTML = 'Sign Out';
        loginbutton.setAttribute('onclick', 'signOut();');
    }


    //This function can be removed if you do not need to support IE
    function acquireTokenRedirectAndCallMSGraph() {
         //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
         myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
             callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
         }).catch(function (error) {
             console.log(error);
             // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
             // Call acquireTokenRedirect
             if (requiresInteraction(error.errorCode)) {
                 myMSALObj.acquireTokenRedirect(requestObj);
             }
         });
     }


    function authRedirectCallBack(error, response) {
        if (error) {
            console.log(error);
        }
        else {
            if (response.tokenType === "access_token") {
                callMSGraph(graphConfig.graphEndpoint, response.accessToken, graphAPICallback);
            } else {
                console.log("token type is:" + response.tokenType);
            }
        }
    }

    function requiresInteraction(errorCode) {
        if (!errorCode || !errorCode.length) {
            return false;
        }
        return errorCode === "consent_required" ||
            errorCode === "interaction_required" ||
            errorCode === "login_required";
    }

    // Browser check variables
    var ua = window.navigator.userAgent;
    var msie = ua.indexOf('MSIE ');
    var msie11 = ua.indexOf('Trident/');
    var msedge = ua.indexOf('Edge/');
    var isIE = msie > 0 || msie11 > 0;
    var isEdge = msedge > 0;
    //If you support IE, our recommendation is that you sign-in using Redirect APIs
    //If you as a developer are testing using Edge InPrivate mode, please add "isEdge" to the if check
    // can change this to default an experience outside browser use
    var loginType = isIE ? "REDIRECT" : "POPUP";

    if (loginType === 'POPUP') {
        if (myMSALObj.getAccount()) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenPopupAndCallMSGraph();
        }
    }
    else if (loginType === 'REDIRECT') {
        document.getElementById("SignIn").onclick = function () {
            myMSALObj.loginRedirect(requestObj);
        };
        if (myMSALObj.getAccount() && !myMSALObj.isCallback(window.location.hash)) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenRedirectAndCallMSGraph();
        }
    } else {
        console.error('Please set a valid login type');
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

Wywoływanie *acquireTokenPopup* wyniki w oknie podręcznym (lub *acquireTokenRedirect* skutkuje przekierowywanie użytkowników do endpoint platforma tożsamości firmy Microsoft) gdzie użytkownicy muszą wchodzić w interakcje przez Potwierdzenie poświadczeń, zapewniając zgody do wymaganych zasobów lub korzystanie z uwierzytelniania dwuskładnikowego.

#### <a name="getting-a-user-token-silently"></a>Dyskretne pobieranie tokenu użytkownika

Metoda `acquireTokenSilent` obsługuje uzyskiwanie i odnawianie tokenów bez żadnej interakcji z użytkownikiem. Po `loginPopup` (lub `loginRedirect`) jest wykonywana po raz pierwszy `acquireTokenSilent` jest metodą, często używane do uzyskiwania tokenów, które umożliwiają dostęp do chronionych zasobów dla kolejnych wywołań — jak do żądania lub odnowienia tokenów wywołań dyskretnie.
`acquireTokenSilent` może się nie powieść w niektórych przypadkach — na przykład użytkownika hasło wygasło. Aplikacja może obsłużyć ten wyjątek na dwa sposoby:

1. Wywołanie `acquireTokenPopup` natychmiast, które powoduje monitowanie użytkownika do logowania. Ten wzorzec jest często używana w aplikacji w trybie online w przypadku, gdy brak nieuwierzytelnione zawartości w aplikacji dostępne dla użytkownika. W przykładzie, wygenerowane za pomocą tego Instalatora z przewodnikiem użyto tego wzorca.

2. Aplikacje może być wizualne oznaczenie do użytkownika, który interakcyjnego logowania jest wymagana, dzięki czemu użytkownik może wybrać w odpowiednim czasie, aby zalogować się lub aplikacji można ponowić próbę `acquireTokenSilent` w późniejszym czasie. Jest to często używane, po użytkownik może użyć innych funkcji aplikacji bez zakłócana — na przykład jest nieuwierzytelnione zawartość dostępna w aplikacji. W tym przypadku użytkownik może wybrać, gdy mają logować się do uzyskania dostępu do chronionego zasobu lub odświeżyć nieaktualne informacje.

> [!NOTE]
> Ten przewodnik Szybki Start używa `loginRedirect` i `acquireTokenRedirect` metody, gdy okno przeglądarki, używany jest program Internet Explorer ze względu na [znany problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) związane z obsługi okna podręcznego przez przeglądarkę Internet Explorer.
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
