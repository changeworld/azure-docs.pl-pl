---
title: Instalator usługi Azure AD v 2.0 z przewodnikiem po stronie jednostronicowej aplikacji (SPA) | Microsoft Docs
description: Jak aplikacje SPA w języku JavaScript mogą wywołać interfejs API, który wymaga tokenów dostępu przez punkt końcowy Azure Active Directory v 2.0
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: cee0884ef20ef9cfd63d81d6f223705d34c65ccb
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69511784"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Logowanie użytkowników i wywoływanie interfejsu API Microsoft Graph z aplikacji JavaScript jednostronicowej (SPA)

W tym przewodniku pokazano, jak aplikacja obsługująca skrypty JavaScript (single-page) może logować się do osobistych, służbowych i szkolnych kont, uzyskać token dostępu i wywoływać interfejs API Microsoft Graph lub inne interfejsy API, które wymagają tokenów dostępu z punktu końcowego platformy tożsamości firmy Microsoft.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak działa Przykładowa aplikacja generowana przez ten przewodnik

![Pokazuje, jak działa Przykładowa aplikacja generowana przez te samouczki](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>Więcej informacji

Przykładowa aplikacja utworzona przez ten przewodnik umożliwia wykonywanie zapytań do interfejsu API Microsoft Graph lub interfejsu API sieci Web, który akceptuje tokeny z punktu końcowego platformy tożsamości firmy Microsoft. W tym scenariuszu po zalogowaniu się użytkownika token dostępu zostanie zaproszony i dodany do żądań HTTP za pośrednictwem nagłówka autoryzacji. Pozyskiwanie i odnawianie tokenów jest obsługiwane przez bibliotekę uwierzytelniania firmy Microsoft (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Biblioteki

W tym przewodniku jest stosowana następująca Biblioteka:

|Biblioteka|Opis|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Biblioteka uwierzytelniania firmy Microsoft dla wersji zapoznawczej języka JavaScript|

> [!NOTE]
> *msal. js* wskazuje *punkt końcowy platformy tożsamości firmy Microsoft* , który umożliwia konto osobiste, szkolne i służbowe do logowania i uzyskiwania tokenów. *Punkt końcowy platformy tożsamości firmy Microsoft* ma [pewne ograniczenia](azure-ad-endpoint-comparison.md#limitations).
> Aby zrozumieć różnice między punktami końcowymi v 1.0 i v 2.0, Przeczytaj [Przewodnik porównywania punktów końcowych](azure-ad-endpoint-comparison.md).

<!--end-collapse-->

## <a name="set-up-your-web-server-or-project"></a>Konfigurowanie serwera lub projektu sieci Web

> Wolisz pobrać projekt tego przykładu? Wykonaj jedną z następujących czynności:
> 
> - Aby uruchomić projekt z lokalnym serwerem sieci Web, takim jak Node. js, [Pobierz pliki projektu](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
>
> - Obowiązkowe Aby uruchomić projekt z serwerem IIS, [Pobierz projekt programu Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).
>
> Aby skonfigurować przykładowy kod przed jego wykonaniem, przejdź do [kroku konfiguracji](#register-your-application).

## <a name="prerequisites"></a>Wymagania wstępne

* Aby uruchomić ten samouczek, musisz mieć lokalny serwer sieci Web, taki jak [Node. js](https://nodejs.org/en/download/), [.net core](https://www.microsoft.com/net/core)lub IIS Express integrację z programem [Visual Studio 2017](https://www.visualstudio.com/downloads/).

* Jeśli używasz środowiska Node. js do uruchamiania projektu, zainstaluj zintegrowane środowisko programistyczne (IDE), takie jak [Visual Studio Code](https://code.visualstudio.com/download), aby edytować pliki projektu.

* Instrukcje zawarte w tym przewodniku są oparte na platformach Node. js i Visual Studio 2017, ale można użyć dowolnego innego środowiska programistycznego lub serwera sieci Web.

## <a name="create-your-project"></a>Tworzenie projektu

> ### <a name="option-1-nodejs-or-other-web-servers"></a>Option 1: Node. js lub inne serwery sieci Web
> Upewnij się, że zainstalowano program [Node. js](https://nodejs.org/en/download/), a następnie wykonaj następujące czynności:
> - Utwórz folder do hostowania aplikacji.
>
> ### <a name="option-2-visual-studio"></a>Opcja 2: Visual Studio
> Jeśli używasz programu Visual Studio i tworzysz nowy projekt, wykonaj następujące czynności:
> 1. W programie Visual Studio, wybierz **pliku** > **New** > **projektu**.
> 1. W pozycji **Visual C#\Internet** wybierz opcję **Aplikacja internetowa ASP.NET (.NET Framework)** .
> 1. Wprowadź nazwę aplikacji, a następnie wybierz przycisk **OK**.
> 1. W obszarze **Nowa aplikacja sieci Web ASP.NET**wybierz pozycję **puste**.

## <a name="create-the-spa-ui"></a>Tworzenie interfejsu użytkownika SPA
1. Utwórz plik *index. html* dla spa w języku JavaScript. Jeśli używasz programu Visual Studio, wybierz projekt (folder główny projektu), kliknij prawym przyciskiem myszy i wybierz polecenie **Dodaj** > **nowy element** > **HTML**i Nazwij plik *index. html*.

1. W pliku *index. html* Dodaj następujący kod:

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Quickstart for MSAL JS</title>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
   </head>
   <body>
       <h2>Welcome to MSAL.js Quickstart</h2><br/>
       <h4 id="WelcomeMessage"></h4>
       <button id="SignIn" onclick="signIn()">Sign In</button><br/><br/>
       <pre id="json"></pre>
       <script>
           //JS code
       </script>
   </body>
   </html>
   ```

   > [!TIP]
   > Wersję MSAL. js w powyższym skrypcie można zastąpić najnowszymi wersjami w sekcji releases [MSAL. js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Logowanie użytkownika przy użyciu biblioteki Microsoft Authentication Library (MSAL)

1. Dodaj następujący kod do `index.html` pliku `<script></script>` w tagach:

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

Gdy użytkownik kliknie przycisk **Zaloguj** po raz pierwszy, `signIn` wywołuje `loginPopup` metodę logowania użytkownika. Ta metoda powoduje otwarcie okna podręcznego z *punktem końcowym platformy tożsamości firmy Microsoft* w celu wyświetlenia monitu i zweryfikowania poświadczeń użytkownika. W wyniku pomyślnego zalogowania użytkownik zostanie przekierowany z powrotem do oryginalnej strony *index. html* i zostanie odebrany token przetwarzany przez `msal.js` i informacje zawarte w tokenie są buforowane. Token ten jest znany jako *token ID* i zawiera podstawowe informacje o użytkowniku, takie jak nazwa wyświetlana użytkownika. Jeśli planujesz używać dowolnych danych z tego tokenu do dowolnych celów, musisz upewnić się, że ten token jest zweryfikowany przez serwer wewnętrznej bazy danych, aby zagwarantować, że token został wystawiony dla prawidłowego użytkownika aplikacji.

Spa wygenerowane przez ten przewodnik wywołuje `acquireTokenSilent` i/lub `acquireTokenPopup` uzyskuje *token dostępu* używany do wykonywania zapytań dotyczących interfejsu API Microsoft Graph informacji o profilu użytkownika. Jeśli potrzebujesz przykładu, który sprawdza poprawność tokenu identyfikatora, zapoznaj się []z przykładową aplikacją przykładową(https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "usługi Active Directory-JavaScript-singlepageapp-dotnet-WebAPI-v2") w serwisie GitHub — przykład korzysta z interfejsu API sieci Web ASP.NET na potrzeby sprawdzania poprawności tokenu.

#### <a name="getting-a-user-token-interactively"></a>Interaktywne pobieranie tokenu użytkownika

Po wstępnym logowaniu nie chcesz monitować użytkowników o ponowne uwierzytelnienie przy każdym zażądaniu tokenu w celu uzyskania dostępu do zasobu, więc *acquireTokenSilent* powinien być używany w większości czasu do uzyskania tokenów. Istnieją jednak sytuacje, w których użytkownicy mogą korzystać z punktu końcowego platformy tożsamości firmy Microsoft — niektóre przykłady obejmują:

- Użytkownicy muszą ponownie wprowadzić poświadczenia, ponieważ hasło wygasło.
- Aplikacja żąda dostępu do zasobów wymagającego zgody użytkownika.
- Wymagane jest uwierzytelnianie dwuetapowe.

Wywołanie *acquireTokenPopup* wyniki w oknie podręcznym (lub *acquireTokenRedirect* wyniki przekierowywania użytkowników do punktu końcowego platformy tożsamości firmy Microsoft), w którym użytkownicy muszą korzystać z potwierdzenia ich poświadczeń, co oznacza, że wyraża zgodę na wymagane zasoby lub kończąc uwierzytelnianie dwuskładnikowe.

#### <a name="getting-a-user-token-silently"></a>Dyskretne pobieranie tokenu użytkownika

Metoda `acquireTokenSilent` obsługuje uzyskiwanie i odnawianie tokenów bez żadnej interakcji z użytkownikiem. Gdy `loginPopup` (lub `loginRedirect`) jest wykonywane po raz pierwszy, `acquireTokenSilent` jest to metoda często używana do uzyskiwania tokenów używanych do uzyskiwania dostępu do chronionych zasobów dla kolejnych wywołań — jako wywołania żądania lub odnowienia tokeny są wykonywane w trybie dyskretnym.
`acquireTokenSilent`może zakończyć się niepowodzeniem w niektórych przypadkach — na przykład hasło użytkownika wygasło. Aplikacja może obsłużyć ten wyjątek na dwa sposoby:

1. Wykonaj wywołanie `acquireTokenPopup` natychmiast, co spowoduje wyświetlenie monitu o zalogowanie użytkownika. Ten wzorzec jest często używany w aplikacjach online, w których aplikacja nie ma dostępnej nieuwierzytelnionej zawartości. Przykład generowany przez tę konfigurację z przewodnikiem używa tego wzorca.

2. Aplikacje mogą także wprowadzać wizualizację do użytkownika, że wymagane jest logowanie interaktywne, aby użytkownik mógł wybrać odpowiedni czas na zalogowanie się, a aplikacja może ponowić próbę `acquireTokenSilent` w późniejszym czasie. Jest to często używane, gdy użytkownik może korzystać z innych funkcji aplikacji bez zakłócania — na przykład w aplikacji jest dostępna nieuwierzytelniona zawartość. W takim przypadku użytkownik może zdecydować się na zalogowanie się w celu uzyskania dostępu do chronionego zasobu lub odświeżenie nieaktualnych informacji.

> [!NOTE]
> Ten przewodnik Szybki Start `loginRedirect` używa `acquireTokenRedirect` metod i, gdy używana przeglądarka to Internet Explorer z powodu [znanego problemu](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) związanego z obsługą okien podręcznych przez przeglądarkę Internet Explorer.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Wywoływanie interfejsu API Microsoft Graph przy użyciu właśnie uzyskanego tokenu

Dodaj następujący kod do `index.html` pliku `<script></script>` w tagach:

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

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Więcej informacji na temat tworzenia wywołania REST w ramach chronionego interfejsu API

W przykładowej aplikacji utworzonej przez ten przewodnik `callMSGraph()` Metoda jest używana do wykonania żądania HTTP `GET` względem chronionego zasobu, który wymaga tokenu, a następnie zwraca zawartość do obiektu wywołującego. Ta metoda dodaje token uzyskany w *nagłówku autoryzacji http*. Dla przykładowej aplikacji utworzonej w tym przewodniku zasób jest punktem końcowym Microsoft Graph API *Me* , który wyświetla informacje o profilu użytkownika.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Dodawanie metody w celu wylogowania użytkownika

Dodaj następujący kod do `index.html` pliku `<script></script>` w tagach:

```javascript
/**
 * Sign out the user
 */
 function signOut() {
     myMSALObj.logout();
 }
```

## <a name="register-your-application"></a>Zarejestruj swoją aplikację

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Jeśli Twoje konto zapewnia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu z dzierżawą usługi Azure AD, której chcesz użyć.
1. Przejdź do strony Microsoft Identity Platform for Developers [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) .
1. Po wyświetleniu strony **Rejestrowanie aplikacji** wprowadź nazwę aplikacji.
1. W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**.
1. W obszarze **Identyfikator URI przekierowania** na liście rozwijanej Wybierz platformę **sieci Web** , a następnie ustaw wartość na adres URL aplikacji oparty na serwerze sieci Web. 

   Aby uzyskać informacje dotyczące ustawiania i uzyskiwania adresu URL przekierowania w programie Visual Studio i Node. js, zobacz następne dwie sekcje.

1. Wybierz pozycję **Zarejestruj**.
1. Na stronie **Przegląd** aplikacji Zanotuj wartość **identyfikatora aplikacji (klienta)** do późniejszego użycia.
1. Ten przewodnik Szybki start wymaga włączenia [przepływu niejawnego udzielenia](v2-oauth2-implicit-grant-flow.md). W lewym okienku zarejestrowanej aplikacji wybierz pozycję **uwierzytelnianie**.
1. W obszarze **Ustawienia zaawansowane**w obszarze niejawne przyznanie zaznacz pola wyboru tokeny **identyfikatorów** i **tokeny dostępu** . Tokeny identyfikatorów i tokeny dostępu są wymagane, ponieważ ta aplikacja musi zalogować użytkowników i wywołać interfejs API.
1. Wybierz pozycję **Zapisz**.

> #### <a name="set-a-redirect-url-for-nodejs"></a>Ustawianie adresu URL przekierowania dla środowiska Node. js
> W przypadku środowiska Node. js można ustawić port serwera sieci Web w pliku *Server. js* . W tym samouczku jest używany port 30662 do celów referencyjnych, ale można użyć dowolnego innego dostępnego portu. 
>
> Aby skonfigurować adres URL przekierowania w informacjach rejestracyjnych aplikacji, Wróć do okienka **rejestracji aplikacji** i wykonaj jedną z następujących czynności:
>
> - Ustaw *`http://localhost:30662/`* jako **adres URL przekierowania**.
> - Jeśli używasz niestandardowego portu TCP, użyj *`http://localhost:<port>/`* (gdzie  *\<Port >* jest niestandardowym numerem portu TCP).
>
> #### <a name="set-a-redirect-url-for-visual-studio"></a>Ustawianie adresu URL przekierowania dla programu Visual Studio
> Aby uzyskać adres URL przekierowania dla programu Visual Studio, wykonaj następujące czynności:
> 1. W **Eksploratora rozwiązań**, wybierz projekt.
>
>    Zostanie otwarte okno **Właściwości** . Jeśli nie jest otwarty, naciśnij klawisz **F4**.
>
>    ![Projekt JavaScriptSPA okno Właściwości](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)
>
> 1. Skopiuj wartość **adresu URL** .
 
> 1. Wróć do okienka **rejestracji aplikacji** i wklej skopiowaną wartość jako **adres URL przekierowania**.

#### <a name="configure-your-javascript-spa"></a>Konfigurowanie protokołu JavaScript SPA

1. W pliku *index. html* , który został utworzony podczas instalacji projektu, Dodaj informacje o rejestracji aplikacji. W górnej części pliku, w `<script></script>` znacznikach, Dodaj następujący kod:

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "<Enter_the_Application_Id_here>",
            authority: "https://login.microsoftonline.com/<Enter_the_Tenant_info_here>"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    Gdzie:
    - Enter_the_Application_Id_here > to **Identyfikator aplikacji (klienta)** dla zarejestrowanej aplikacji.  *\<*
    - Enter_the_Tenant_info_here > jest ustawiona na jedną z następujących opcji:  *\<*
       - Jeśli aplikacja obsługuje *konta w tym katalogu organizacyjnym*, Zastąp tę wartość **identyfikatorem dzierżawy** lub **nazwą dzierżawy** (na przykład *contoso.Microsoft.com*).
       - Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym*, Zastąp tę wartość **organizacją**.
       - Jeśli aplikacja obsługuje *konta w dowolnym katalogu organizacyjnym i osobistych kontach Microsoft*, Zastąp tę wartość **wspólnym**. Aby ograniczyć obsługę *tylko do osobistych kont Microsoft*, Zastąp tę wartość **odbiorcom**.

## <a name="test-your-code"></a>testowanie kodu

Przetestuj swój kod przy użyciu jednego z następujących środowisk.

### <a name="test-with-nodejs"></a>Testowanie przy użyciu środowiska Node. js

Jeśli nie korzystasz z programu Visual Studio, upewnij się, że serwer sieci Web został uruchomiony.

1. Skonfiguruj serwer do nasłuchiwania na porcie TCP, który jest oparty na lokalizacji pliku *index. html* . W przypadku środowiska Node. js Uruchom serwer sieci Web, aby nasłuchiwać na porcie, uruchamiając następujące polecenia w wierszu polecenia z folderu aplikacji:

    ```bash
    npm install
    node server.js
    ```
1. W przeglądarce wprowadź **http://\<span\<>/span > localhost: 30662** lub **\<http://span >\</span > localhost: {Port}** , gdzie *port* jest portem, do którego należy serwer sieci Web trwa nasłuchiwanie. Powinna zostać wyświetlona zawartość pliku *index. html* i przycisku **Zaloguj** .

### <a name="test-with-visual-studio"></a>Testowanie za pomocą programu Visual Studio

Jeśli używasz programu Visual Studio, wybierz rozwiązanie projektu, a następnie wybierz klawisz F5, aby uruchomić projekt. Przeglądarka otworzy się w lokalizacji http://<span></span>localhost: {port}, a przycisk **Zaloguj** powinien być widoczny.

## <a name="test-your-application"></a>Testowanie aplikacji

Po załadowaniu przez przeglądarkę pliku *index. html* wybierz pozycję **Zaloguj**. Zostanie wyświetlony monit o zalogowanie się za pomocą punktu końcowego platformy tożsamości firmy Microsoft:

![Okno logowania do konta SPA w języku JavaScript](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Wyrażanie zgody na dostęp do aplikacji

Po pierwszym zalogowaniu się do aplikacji zostanie wyświetlony monit o udzielenie dostępu do Twojego profilu i zalogowanie się:

![Okno "wymagane uprawnienia"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Wyświetlanie wyników aplikacji

Po zalogowaniu informacje o profilu użytkownika zostaną zwrócone w odpowiedzi interfejsu API Microsoft Graph, która jest wyświetlana na stronie.

![Wyniki wywołania interfejsu API Microsoft Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Więcej informacji na temat zakresów i uprawnień delegowanych

Interfejs API Microsoft Graph wymaga, aby *użytkownik. Read* miał zakres do odczytu profilu użytkownika. Ten zakres jest automatycznie dodawany domyślnie w każdej aplikacji, która jest zarejestrowana w portalu rejestracji. Inne interfejsy API dla Microsoft Graph, a także niestandardowe interfejsy API dla serwera zaplecza mogą wymagać dodatkowych zakresów. Na przykład interfejs API Microsoft Graph wymaga programu *Calendars. Read* , aby wyświetlić kalendarze użytkownika.

Aby uzyskać dostęp do kalendarzy użytkownika w kontekście aplikacji, Dodaj *kalendarze. Odczytaj* delegowane uprawnienia do informacji rejestracyjnych aplikacji. Następnie Dodaj do `acquireTokenSilent` wywołania zakres *Calendars. Read* .

>[!NOTE]
>Użytkownik może zostać poproszony o dodatkowe przesłanie w miarę zwiększania liczby zakresów.

Jeśli interfejs API zaplecza nie wymaga zakresu (niezalecane), można użyć *clientId* jako zakresu w wywołaniach w celu uzyskania tokenów.

<!--end-collapse-->

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Pomóż nam ulepszyć platformę tożsamości firmy Microsoft. Powiedz nam, co myślisz, wykonując krótką ankietę z dwoma pytaniami.

> [!div class="nextstepaction"]
> [Microsoft Identity platform — ankieta](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)