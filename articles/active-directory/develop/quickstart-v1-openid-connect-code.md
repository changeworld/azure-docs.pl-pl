---
title: Wprowadzenie do aplikację internetową środowiska Node.js usługi AD platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację internetową Node.js Express MVC, która integruje się z usługą Azure AD podczas logowania.
services: active-directory
documentationcenter: nodejs
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 81deecec-dbe2-4e75-8bc0-cf3788645f99
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 04/20/2018
ms.author: celested
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8bba58c3493bc8adc17c5d4bca103326808d5b8b
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39506640"
---
# <a name="azure-ad-nodejs-web-app-getting-started"></a>Wprowadzenie do aplikację internetową środowiska Node.js usługi AD platformy Azure
Tutaj używamy usługi Passport w celu:

* Zalogować użytkownika do aplikacji w usłudze Azure Active Directory (Azure AD).
* Wyświetl informacje o użytkowniku.
* Zaloguj się użytkownika z aplikacji.

Usługa Passport jest oprogramowanie pośredniczące uwierzytelniania dla środowiska Node.js. Elastyczne i modułowe oprogramowanie, usługi Passport można dyskretnie porzucić każdym Express opartą na lub restify aplikacji sieci web. Kompleksowy zestaw strategii obsługuje uwierzytelnianie, który używa nazwy użytkownika i hasło, Facebook i Twitter. Opracowaliśmy strategię dla usługi Microsoft Azure Active Directory. Firma Microsoft zainstalowaniu tego modułu, a następnie dodaj Microsoft Azure Active Directory `passport-azure-ad` wtyczki.

Aby to zrobić, wykonaj następujące czynności:

1. Rejestrowanie aplikacji.
2. Konfigurowanie aplikacji do użycia `passport-azure-ad` strategii.
3. Użyć programu Passport do wysyłania do usługi Azure AD żądań zalogowania się i wylogowania.
4. Drukowanie danych o użytkowniku.

Kod używany w tym samouczku jest przechowywany [ w serwisie GitHub](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS). W tym samouczku możesz [pobrać szkielet aplikacji w formie pliku .zip](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip) lub sklonować szkielet:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

Ukończona aplikacja znajduje się na końcu tego samouczka, jak również.

## <a name="step-1-register-an-app"></a>Krok 1: Rejestrowanie aplikacji
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W menu w górnej części strony wybierz swoje konto. W obszarze **katalogu** wybierz dzierżawę usługi Active Directory, w którym chcesz zarejestrować aplikację.

3. Wybierz **wszystkich usług** w menu po lewej stronie ekranu, a następnie wybierz **usługi Azure Active Directory**.

4. Wybierz **rejestracje aplikacji**, a następnie wybierz pozycję **Dodaj**.

5. Postępuj zgodnie z monitami, aby utworzyć **aplikacji sieci Web** i/lub **WebAPI**.
  * **Nazwa** aplikacji będzie opisywać aplikację użytkownikom.

  * **Adres URL logowania** jest podstawowy adres URL aplikacji. Domyślnie szkielet `http://localhost:3000/auth/openid/return`.

6. Po rejestracji usługi Azure AD przypisze aplikacji Unikatowy identyfikator aplikacji. Ta wartość będzie potrzebna w poniższych sekcjach, więc skopiuj ją ze strony aplikacji.
7. Z **ustawienia** -> **właściwości** stronie aplikacji, zaktualizuj identyfikator URI Identyfikatora aplikacji. **Identyfikator URI Identyfikatora aplikacji** to unikatowy identyfikator aplikacji. Konwencji jest użycie formatu `https://<tenant-domain>/<app-name>`, na przykład: `https://contoso.onmicrosoft.com/my-first-aad-app`.

8. Z **ustawienia** -> **adresy URL odpowiedzi** stronie dla aplikacji, Dodaj adres URL dodane w adresie URL logowania jednokrotnego z kroku 5 i kliknij pozycję Zapisz.

9. Aby utworzyć klucz tajny, przejdź do kroku 4 w [dodać poświadczenia aplikacji lub uprawnienia dostępu do interfejsów API sieci web](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#to-add-application-credentials-or-permissions-to-access-web-apis).

   > [!IMPORTANT]
   > Skopiuj wartość klucza aplikacji. Jest to wartość `clientSecret`, które będą potrzebne dla **kroku 3** poniżej. 

## <a name="step-2-add-prerequisites-to-your-directory"></a>Krok 2: Dodawanie wymagań wstępnych do katalogu
1. W wierszu polecenia Zmień katalog na folder główny, jeśli nie masz już istnieje, a następnie uruchom następujące polecenia:

    * `npm install express`
    * `npm install ejs`
    * `npm install ejs-locals`
    * `npm install restify`
    * `npm install mongoose`
    * `npm install bunyan`
    * `npm install assert-plus`
    * `npm install passport`

2. Ponadto konieczne `passport-azure-ad`:
    * `npm install passport-azure-ad`

Spowoduje to zainstalowanie bibliotek, `passport-azure-ad` zależy.

## <a name="step-3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>Krok 3: Skonfigurować aplikację do używania strategii passport-node-js
W tym miejscu możemy skonfigurować Express do używania protokołu uwierzytelniania OpenID Connect. Usługa Passport umożliwia wykonywanie różnych czynności, tym realizowania żądań logowania i wylogowywania problem, zarządzanie sesji użytkownika i uzyskiwanie informacji o użytkowniku.

1. Aby rozpocząć, otwórz `config.js` plików w katalogu głównym projektu, a następnie wprowadź wartości konfiguracji aplikacji w `exports.creds` sekcji.

  * `clientID` Jest **identyfikator aplikacji** przypisany do aplikacji w portalu rejestracji.

  * `returnURL` Jest **adres URL odpowiedzi** wprowadzony w portalu.

  * `clientSecret` Jest klucz tajny, który został wygenerowany w portalu.

2. Następnie otwórz `app.js` plik w folderze głównym projektu. Następnie dodaj następujące wywołanie do wywołania `OIDCStrategy` strategii, dostarczanej z narzędziem `passport-azure-ad`.

    ```JavaScript
    var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

    // add a logger

    var log = bunyan.createLogger({
        name: 'Microsoft OIDC Example Web Application'
    });
    ```

3. Następnie należy użyć strategii, firma Microsoft właśnie w odwołaniu do obsługi żądań logowania w naszym.

    ```JavaScript
    // Use the OIDCStrategy within Passport. (Section 2)
    //
    //   Strategies in passport require a `validate` function that accepts
    //   credentials (in this case, an OpenID identifier), and invokes a callback
    //   with a user object.
    passport.use(new OIDCStrategy({
        callbackURL: config.creds.returnURL,
        realm: config.creds.realm,
        clientID: config.creds.clientID,
        clientSecret: config.creds.clientSecret,
        oidcIssuer: config.creds.issuer,
        identityMetadata: config.creds.identityMetadata,
        skipUserProfile: config.creds.skipUserProfile,
        responseType: config.creds.responseType,
        responseMode: config.creds.responseMode
    },
    function(iss, sub, profile, accessToken, refreshToken, done) {
        if (!profile.email) {
        return done(new Error("No email found"), null);
        }
        // asynchronous verification, for effect...
        process.nextTick(function () {
        findByEmail(profile.email, function(err, user) {
            if (err) {
            return done(err);
            }
            if (!user) {
            // "Auto-registration"
            users.push(profile);
            return done(null, profile);
            }
            return done(null, user);
        });
        });
    }
    ));
    ```
   Usługa Passport używa podobnego wzorca dla wszystkich swoich strategii (Twitter, Facebook itd.), które wszystkich autorów. Patrząc strategii, zobaczysz, że przekazanie jego funkcję, która ma token i gotowe jako parametry. Strategia zostanie zwrócona dla nas po wykonaniu pracy. Następnie chcemy przechować dane użytkownika i Zapisz token więc nie potrzebujemy zażądać go ponownie.

   > [!IMPORTANT]
   > Powyższy kod przyjmuje każdy użytkownik, który ma miejsce do uwierzytelniania serwera. Jest to nazywane rejestracji automatycznej. Zaleca się, że nie przegap każdy uwierzytelniać się na serwerze produkcyjnym, bez konieczności ich zarejestrować przy użyciu procesu, który użytkownik zdecyduje się na. Jest to zazwyczaj wzorzec, które są widoczne w aplikacjach komercyjnych, dzięki której można zarejestrować za pomocą usługi Facebook, ale następnie poprosić o podanie dodatkowych informacji. Jeśli to nie przykładowej aplikacji, moglibyśmy wyodrębnić adres e-mail użytkownika z obiektu tokena, który został zwrócony, a następnie monit użytkownika o podanie dodatkowych informacji. Ponieważ jest to serwer testowy, dodamy ich do bazy danych w pamięci.


4. Następnie Dodajmy metody, które umożliwiają śledzenie zalogowanych użytkowników zgodnie z wymogami usługi Passport. Te metody obejmują serializację i deserializację informacji o użytkowniku.

    ```JavaScript
    // Passport session setup. (Section 2)

    //   To support persistent sign-in sessions, Passport needs to be able to
    //   serialize users into the session and deserialize them out of the session. Typically,
    //   this is done simply by storing the user ID when serializing and finding
    //   the user by ID when deserializing.
    passport.serializeUser(function(user, done) {
        done(null, user.email);
    });

    passport.deserializeUser(function(id, done) {
        findByEmail(id, function (err, user) {
            done(err, user);
        });
    });

    // array to hold signed-in users
    var users = [];

    var findByEmail = function(email, fn) {
        for (var i = 0, len = users.length; i < len; i++) {
            var user = users[i];
            log.info('we are using user: ', user);
            if (user.email === email) {
                return fn(null, user);
            }
        }
        return fn(null, null);
    };
    ```

5. Następnie możemy dodać kod, aby załadować aparat Express. W tym miejscu użyjemy /views domyślne i zawiera wzorzec /routes Express.

    ```JavaScript
    // configure Express (section 2)

        var app = express();
        app.configure(function() {
      app.set('views', __dirname + '/views');
      app.set('view engine', 'ejs');
      app.use(express.logger());
      app.use(express.methodOverride());
      app.use(cookieParser());
      app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
      app.use(bodyParser.urlencoded({ extended : true }));
      // Initialize Passport!  Also use passport.session() middleware, to support
      // persistent login sessions (recommended).
      app.use(passport.initialize());
      app.use(passport.session());
      app.use(app.router);
      app.use(express.static(__dirname + '/../../public'));
    });
    ```

6. Na koniec możemy dodać trasy, które przekazują rzeczywiste żądania rejestrowania do `passport-azure-ad` aparatu:

    ```JavaScript
    // Our Auth routes (section 3)

    // GET /auth/openid
    //   Use passport.authenticate() as route middleware to authenticate the
    //   request. The first step in OpenID authentication involves redirecting
    //   the user to their OpenID provider. After authenticating, the OpenID
    //   provider redirects the user back to this application at
    //   /auth/openid/return.
    app.get('/auth/openid',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {
        log.info('Authentication was called in the Sample');
        res.redirect('/');
    });

    // GET /auth/openid/return
    //   Use passport.authenticate() as route middleware to authenticate the
    //   request. If authentication fails, the user is redirected back to the
    //   sign-in page. Otherwise, the primary route function is called,
    //   which, in this example, redirects the user to the home page.
    app.get('/auth/openid/return',
      passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
      function(req, res) {
        log.info('We received a return from AzureAD.');
        res.redirect('/');
      });

    // POST /auth/openid/return
    //   Use passport.authenticate() as route middleware to authenticate the
    //   request. If authentication fails, the user is redirected back to the
    //   sign-in page. Otherwise, the primary route function is called,
    //   which, in this example, redirects the user to the home page.
    app.post('/auth/openid/return',
      passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
      function(req, res) {
        log.info('We received a return from AzureAD.');
        res.redirect('/');
      });
    ```


## <a name="step-4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>Krok 4: Korzystanie z usługi Passport do wysyłania żądań logowania i wylogowania do usługi Azure AD
Aplikacja jest prawidłowo skonfigurowany do komunikowania się z punktem końcowym za pomocą protokołu uwierzytelniania OpenID Connect. `passport-azure-ad` ma uwzględnić wszystkie szczegóły obsługuje tworzenie komunikatów uwierzytelniania, sprawdzanie poprawności tokenów z usługi Azure AD i podtrzymywanie sesji użytkowników. Wszystkie opcje, które pozostają jest przyznawanie użytkownikom sposób logować się i wyloguj się i gromadzenia dodatkowych informacji na temat zalogowanych użytkowników.

1. Najpierw Dodajmy domyślną, logowania, konta i wylogowania metody do naszych `app.js` pliku:

    ```JavaScript
    //Routes (section 4)

    app.get('/', function(req, res){
      res.render('index', { user: req.user });
    });

    app.get('/account', ensureAuthenticated, function(req, res){
      res.render('account', { user: req.user });
    });

    app.get('/login',
      passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
      function(req, res) {
        log.info('Login was called in the Sample');
        res.redirect('/');
    });

    app.get('/logout', function(req, res){
      req.logout();
      res.redirect('/');
    });
    ```

2. Omówmy teraz te szczegóły:

  * `/`Trasa przekierowuje do widoku index.ejs przekazanie użytkownika w żądaniu (jeśli istnieje).
  * `/account` Trasy najpierw *zapewnia firma Microsoft jest uwierzytelniana* (wdrażamy, w poniższym przykładzie), a następnie przekazuje użytkownika w żądaniu, dzięki czemu firma Microsoft może uzyskać dodatkowe informacje o użytkowniku.
  * `/login` Trasy wywołuje naszych uwierzytelniania openidconnect usługi Azure AD z `passport-azuread`. Jeśli, nie powiedzie, przekierowuje użytkownika do /login.
  * `/logout` Po prostu wywołuje logout.ejs (i trasę), które powoduje wyczyszczenie plików cookie, a następnie wróci użytkownika index.ejs trasy.

3. Ostatnia część `app.js`, możemy dodać **EnsureAuthenticated** metodę, która jest używana w `/account`, jak pokazano wcześniej.

    ```JavaScript
    // Simple route middleware to ensure user is authenticated. (section 4)

    //   Use this route middleware on any resource that needs to be protected. If
    //   the request is authenticated (typically via a persistent sign-in session),
    //   the request proceeds. Otherwise, the user is redirected to the
    //   sign-in page.
    function ensureAuthenticated(req, res, next) {
      if (req.isAuthenticated()) { return next(); }
      res.redirect('/login')
    }
    ```

4. Na koniec Utwórz sam serwer w `app.js`:

```JavaScript
app.listen(3000);
```


## <a name="step-5-to-display-our-user-in-the-website-create-the-views-and-routes-in-express"></a>Krok 5: Aby wyświetlić użytkowników w witrynie sieci Web, tworzenie widoków i tras w wersji Express
Teraz `app.js` zostało zakończone. Po prostu należy dodać trasy i widoki, które zawierają informacje o możemy przejść do użytkownika, a także obsługiwać `/logout` i `/login` tras, które utworzyliśmy.

1. Utwórz trasę `/routes/index.js` w obszarze katalogu głównego.

    ```JavaScript
    /*
     * GET home page.
     */

    exports.index = function(req, res){
      res.render('index', { title: 'Express' });
    };
    ```

2. Utwórz trasę `/routes/user.js` w obszarze katalogu głównego.

    ```JavaScript
    /*
     * GET users listing.
     */

    exports.list = function(req, res){
      res.send("respond with a resource");
    };
    ```

 Te przekazują żądania do naszych widoków, w tym użytkownika, jeśli jest obecny.

3. Utwórz widok `/views/index.ejs` w katalogu głównym. Jest to prosta strona, która wywołuje metody naszych zalogowania i wylogowania i pozwala nam w celu pobrania informacji o koncie. Zwróć uwagę, że możemy używać warunkową `if (!user)` jako użytkownik, są przekazywane w żądaniu jest dowód mamy zalogowanego użytkownika.

    ```JavaScript
    <% if (!user) { %>
        <h2>Welcome! Please log in.</h2>
        <a href="/login">Log In</a>
    <% } else { %>
        <h2>Hello, <%= user.displayName %>.</h2>
        <a href="/account">Account Info</a></br>
        <a href="/logout">Log Out</a>
    <% } %>
    ```

4. Tworzenie `/views/account.ejs` Wyświetl w katalogu głównym, dzięki czemu możemy wyświetlić dodatkowe informacje, `passport-azure-ad` umieścił w żądaniu użytkownika.

    ```Javascript
    <% if (!user) { %>
        <h2>Welcome! Please log in.</h2>
        <a href="/login">Log In</a>
    <% } else { %>
    <p>displayName: <%= user.displayName %></p>
    <p>givenName: <%= user.name.givenName %></p>
    <p>familyName: <%= user.name.familyName %></p>
    <p>UPN: <%= user._json.upn %></p>
    <p>Profile ID: <%= user.id %></p>
  ##Next steps  <p>Full Claimes</p>
    <%- JSON.stringify(user) %>
    <p></p>
    <a href="/logout">Log Out</a>
    <% } %>
    ```

5. Upewnijmy się, to wygląd dobre, dodając układu. Utwórz widok `/views/layout.ejs` w katalogu głównym.

    ```HTML

    <!DOCTYPE html>
    <html>
        <head>
            <title>Passport-OpenID Example</title>
        </head>
        <body>
            <% if (!user) { %>
                <p>
                <a href="/">Home</a> |
                <a href="/login">Log In</a>
                </p>
            <% } else { %>
                <p>
                <a href="/">Home</a> |
                <a href="/account">Account</a> |
                <a href="/logout">Log Out</a>
                </p>
            <% } %>
            <%- body %>
        </body>
    </html>
    ```

## <a name="next-steps"></a>Kolejne kroki
Na koniec Skompiluj i uruchom aplikację. Uruchom `node app.js`, a następnie przejdź do `http://localhost:3000`.

Zaloguj się przy użyciu osobistego konta Microsoft lub konta służbowego lub szkolnego i zwróć uwagę, jak tożsamość użytkownika jest odzwierciedlana na liście/account. Masz teraz aplikację sieci web, która jest zabezpieczony za pomocą standardowych w branży protokołów, które można uwierzytelniać użytkowników przy użyciu ich osobistych, jak i służbowe kont.

Gotowa próbka (bez wartości konfiguracji) [jest dostępna w pliku .zip](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/complete.zip). Alternatywnie możesz go sklonować z usługi GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

Możesz teraz przejść do bardziej zaawansowanych tematów. Można spróbować:

[Zabezpieczanie interfejsu Web API z usługą Azure AD](active-directory-devquickstarts-webapi-nodejs.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
