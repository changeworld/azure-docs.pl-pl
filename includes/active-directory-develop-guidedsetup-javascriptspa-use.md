## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Zaloguj użytkownika za pomocą biblioteki uwierzytelniania firmy Microsoft (MSAL)

1.  Utwórz plik o nazwie `app.js`. Jeśli używasz programu Visual Studio, wybierz projekt (folder główny projekt), kliknij prawym przyciskiem myszy i wybierz: `Add`  >  `New Item`  >  `JavaScript File`:
2.  Dodaj następujący kod do Twojej `app.js` pliku:

```javascript
// Graph API endpoint to show user profile
var graphApiEndpoint = "https://graph.microsoft.com/v1.0/me";

// Graph API scope used to obtain the access token to read user profile
var graphAPIScopes = ["https://graph.microsoft.com/user.read"];

// Initialize application
var userAgentApplication = new Msal.UserAgentApplication(msalconfig.clientID, null, loginCallback, {
    redirectUri: msalconfig.redirectUri
});

//Previous version of msal uses redirect url via a property
if (userAgentApplication.redirectUri) {
    userAgentApplication.redirectUri = msalconfig.redirectUri;
}

window.onload = function () {
    // If page is refreshed, continue to display user info
    if (!userAgentApplication.isCallback(window.location.hash) && window.parent === window && !window.opener) {
        var user = userAgentApplication.getUser();
        if (user) {
            callGraphApi();
        }
    }
}

/**
 * Call the Microsoft Graph API and display the results on the page. Sign the user in if necessary
 */
function callGraphApi() {
    var user = userAgentApplication.getUser();
    if (!user) {
        // If user is not signed in, then prompt user to sign in via loginRedirect.
        // This will redirect user to the Azure Active Directory v2 Endpoint
        userAgentApplication.loginRedirect(graphAPIScopes);
        // The call to loginRedirect above frontloads the consent to query Graph API during the sign-in.
        // If you want to use dynamic consent, just remove the graphAPIScopes from loginRedirect call.
        // As such, user will be prompted to give consent when requested access to a resource that 
        // he/she hasn't consented before. In the case of this application - 
        // the first time the Graph API call to obtain user's profile is executed.
    } else {
        // If user is already signed in, display the user info
        var userInfoElement = document.getElementById("userInfo");
        userInfoElement.parentElement.classList.remove("hidden");
        userInfoElement.innerHTML = JSON.stringify(user, null, 4);

        // Show sign-out button
        document.getElementById("signOutButton").classList.remove("hidden");

        // Now Call Graph API to show the user profile information:
        var graphCallResponseElement = document.getElementById("graphResponse");
        graphCallResponseElement.parentElement.classList.remove("hidden");
        graphCallResponseElement.innerText = "Calling Graph ...";

        // In order to call the Graph API, an access token needs to be acquired.
        // Try to acquire the token used to query Graph API silently first:
        userAgentApplication.acquireTokenSilent(graphAPIScopes)
            .then(function (token) {
                //After the access token is acquired, call the Web API, sending the acquired token
                callWebApiWithToken(graphApiEndpoint, token, graphCallResponseElement, document.getElementById("accessToken"));

            }, function (error) {
                // If the acquireTokenSilent() method fails, then acquire the token interactively via acquireTokenRedirect().
                // In this case, the browser will redirect user back to the Azure Active Directory v2 Endpoint so the user 
                // can reenter the current username/ password and/ or give consent to new permissions your application is requesting.
                // After authentication/ authorization completes, this page will be reloaded again and callGraphApi() will be executed on page load.
                // Then, acquireTokenSilent will then get the token silently, the Graph API call results will be made and results will be displayed in the page.
                if (error) {
                    userAgentApplication.acquireTokenRedirect(graphAPIScopes);
                }
            });
    }
}

/**
 * Callback method from sign-in: if no errors, call callGraphApi() to show results.
 * @param {string} errorDesc - If error occur, the error message
 * @param {object} token - The token received from sign-in
 * @param {object} error - The error string
 * @param {string} tokenType - The token type: For loginRedirect, tokenType = "id_token". For acquireTokenRedirect, tokenType:"access_token".
 */
function loginCallback(errorDesc, token, error, tokenType) {
    if (errorDesc) {
        showError(msal.authority, error, errorDesc);
    } else {
        callGraphApi();
    }
}

/**
 * Show an error message in the page
 * @param {string} endpoint - the endpoint used for the error message
 * @param {string} error - Error string
 * @param {string} errorDesc - Error description
 */
function showError(endpoint, error, errorDesc) {
    var formattedError = JSON.stringify(error, null, 4);
    if (formattedError.length < 3) {
        formattedError = error;
    }
    document.getElementById("errorMessage").innerHTML = "An error has occurred:<br/>Endpoint: " + endpoint + "<br/>Error: " + formattedError + "<br/>" + errorDesc;
    console.error(error);
}

```

<!--start-collapse-->
### <a name="more-information"></a>Więcej informacji

Po kliknięciu *"Wywołaj Microsoft interfejsu API programu Graph"* przycisku po raz pierwszy, `callGraphApi` wywołania metody `loginRedirect` do logowania użytkownika. Ta metoda powoduje przekierowanie użytkownika do *punktu końcowego programu Microsoft Azure Active Directory v2* Monituj i sprawdzić poprawności poświadczeń użytkownika. W wyniku pomyślnego logowania, zostanie przekierowany użytkownik w wrócić do oryginalnego *index.html* strony i token odebraniu przetworzonych przez `msal.js` i informacji zawartych w tokenie są buforowane. Token ten jest nazywany *token Identyfikatora* i zawiera podstawowe informacje o użytkowniku, takie jak nazwa wyświetlana użytkownika. Jeśli planujesz używać danych udostępniane przez ten token do celów, musisz upewnij się, że ten token jest zweryfikowany przez serwer wewnętrznej bazy danych, aby zagwarantować, że token został wystawiony dla prawidłowego użytkownika dla aplikacji.

SPA wygenerowane przez ten przewodnik nie oznacza, że użycie bezpośrednio tokenu identyfikator — zamiast tego wywołuje `acquireTokenSilent` i/lub `acquireTokenRedirect` uzyskanie *token dostępu* wykorzystywane do badania interfejsu API programu Microsoft Graph. Przykładowe, która weryfikuje token Identyfikatora, należy przyjrzeć [to](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "Github active-directory-javascript-singlepageapp-dotnet-webapi-v2 próbki") ASP używa przykładowej aplikacji w usłudze GitHub — przykład Interfejs API sieci web .NET dla tokenu weryfikacji.

#### <a name="getting-a-user-token-interactively"></a>Pobieranie tokenu użytkownika interakcyjnego

Po początkowej logowania, czy chcesz o konieczności ponownego uwierzytelnienia za każdym razem, należy uzyskać token dostępu do zasobu — tak *acquireTokenSilent* powinny być używane w większości przypadków do uzyskania tokenów. Istnieje jednak sytuacji, należy wymusić użytkownikom na interakcję z punktem końcowym w wersji 2 usługi Azure Active Directory — Oto kilka przykładów:
- Użytkownicy mogą potrzebować ponownie wprowadzić swoje poświadczenia, ponieważ hasło wygasło
- Żąda dostępu do zasobu, który użytkownik musi wyrazić zgodę na aplikację
- Uwierzytelnianie dwuskładnikowe jest wymagana

Wywoływanie *acquireTokenRedirect(scope)* spowodować przekierowaniu użytkowników do punktu końcowego usługi Azure Active Directory w wersji 2 (lub *acquireTokenPopup(scope)* wynik w oknie podręcznym) gdzie użytkownicy muszą wchodzić w interakcje Potwierdzenie poświadczeń, zapewniając zgody wymaganego zasobu lub kończenie uwierzytelniania dwuskładnikowego.

#### <a name="getting-a-user-token-silently"></a>Pobieranie tokenu użytkownika dyskretnej
` acquireTokenSilent` Metoda obsługuje przejęć tokenu i wznowienie bez interakcji użytkownika. Po `loginRedirect` (lub `loginPopup`) jest wykonywana po raz pierwszy `acquireTokenSilent` jest metoda często używane do uzyskiwania tokenów umożliwiają dostęp do chronionych zasobów w kolejnych wywołaniach — jako wywołania żądania lub odnowić tokeny zostały wprowadzone w trybie dyskretnym.
`acquireTokenSilent` może się nie powieść w niektórych przypadkach — na przykład hasło użytkownika wygasło. Aplikacja może obsłużyć tego wyjątku na dwa sposoby:

1.  Wywoływania `acquireTokenRedirect` natychmiast, która powoduje monitowanie użytkownika do logowania. Ten wzorzec jest często używana w aplikacji online w przypadku, gdy brak nieuwierzytelnione zawartości w aplikacji dostępne dla użytkownika. Przykładowe wygenerowane za pomocą tego Instalatora z przewodnikiem używa tego wzorca.

2. Aplikacje można wprowadzić oznaczenia wizualne dla użytkownika, który interakcyjnego logowania jest wymagana, więc użytkownik może wybrać, właściwym czasie zalogować się lub aplikacji można ponowić próbę `acquireTokenSilent` w późniejszym czasie. Jest ona powszechnie stosowana, gdy użytkownik może użyć innych funkcji aplikacji bez są zakłócone — na przykład Brak dostępnej zawartości nieuwierzytelnione w aplikacji. W takim przypadku użytkownik może zdecydować, chcąc Zaloguj się do uzyskania dostępu do zabezpieczonych zasobów lub Odśwież nieaktualne informacje.

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Wywołanie interfejsu API programu Microsoft Graph przy użyciu tokenu, który został uzyskany

Dodaj następujący kod do Twojej `app.js` pliku:

```javascript
/**
 * Call a Web API using an access token.
 * @param {any} endpoint - Web API endpoint
 * @param {any} token - Access token
 * @param {object} responseElement - HTML element used to display the results
 * @param {object} showTokenElement = HTML element used to display the RAW access token
 */
function callWebApiWithToken(endpoint, token, responseElement, showTokenElement) {
    var headers = new Headers();
    var bearer = "Bearer " + token;
    headers.append("Authorization", bearer);
    var options = {
        method: "GET",
        headers: headers
    };

    fetch(endpoint, options)
        .then(function (response) {
            var contentType = response.headers.get("content-type");
            if (response.status === 200 && contentType && contentType.indexOf("application/json") !== -1) {
                response.json()
                    .then(function (data) {
                        // Display response in the page
                        console.log(data);
                        responseElement.innerHTML = JSON.stringify(data, null, 4);
                        if (showTokenElement) {
                            showTokenElement.parentElement.classList.remove("hidden");
                            showTokenElement.innerHTML = token;
                        }
                    })
                    .catch(function (error) {
                        showError(endpoint, error);
                    });
            } else {
                response.json()
                    .then(function (data) {
                        // Display response as error in the page
                        showError(endpoint, data);
                    })
                    .catch(function (error) {
                        showError(endpoint, error);
                    });
            }
        })
        .catch(function (error) {
            showError(endpoint, error);
        });
}
```
<!--start-collapse-->

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Więcej informacji na temat nawiązywania połączenia przed chronionego interfejsu API REST

W przykładowej aplikacji utworzonych przez ten przewodnik `callWebApiWithToken()` używa metody w celu HTTP `GET` żądania względem chronionego zasobu, który wymaga tokenu, a następnie wróć zawartości do obiektu wywołującego. Ta metoda dodaje token nabyte w *nagłówek autoryzacji HTTP*. Dla przykładowej aplikacji utworzone przez tego przewodnika, zasób jest interfejsu API programu Microsoft Graph *mnie* punktu końcowego — Wyświetla informacje o profilu użytkownika.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Dodaj metodę, aby się wylogować użytkownika

Dodaj następujący kod do Twojej `app.js` pliku:

```javascript
/**
 * Sign out the user
 */
function signOut() {
    userAgentApplication.logout();
}
```
