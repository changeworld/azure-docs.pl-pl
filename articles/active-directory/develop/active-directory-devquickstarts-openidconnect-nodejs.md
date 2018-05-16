---
title: Azure aplikacji sieci web AD Node.js wprowadzenie | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia aplikacji sieci web Node.js Express MVC, która integruje się z usługą Azure AD dla logowania.
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
ms.openlocfilehash: 8068e193ac267ef23bdaab1fc23bc7bf9b0d330b
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="azure-ad-nodejs-web-app-getting-started"></a>Wprowadzenie aplikacji sieci web AD Node.js Azure
W tym miejscu możemy użyć usługi Passport:

* Zalogować użytkownika do aplikacji w usłudze Azure Active Directory (Azure AD).
* Wyświetl informacje o użytkowniku.
* Zaloguj użytkownika poza aplikacją.

Usługa Passport to uwierzytelniające oprogramowanie pośredniczące dla środowiska Node.js. Elastyczne i moduły, usługa Passport można dyskretnie usunięty każdym Express opartą na lub restify aplikacji sieci web. Kompleksowy zestaw strategii obsługuje uwierzytelniania przy użyciu nazwy użytkownika i hasła, Facebook i Twitter. Opracowaliśmy strategię dla usługi Microsoft Azure Active Directory. Firma Microsoft zainstalowaniu tego modułu, a następnie dodaj Microsoft Azure Active Directory `passport-azure-ad` wtyczki.

W tym celu należy wykonać następujące czynności:

1. Rejestrowanie aplikacji.
2. Konfigurowanie aplikacji do użycia `passport-azure-ad` strategii.
3. Użyć programu Passport do wysyłania do usługi Azure AD żądań zalogowania się i wylogowania.
4. Wydrukować dane o użytkowniku.

Kod używany w tym samouczku jest przechowywany [ w serwisie GitHub](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS). Aby z niego skorzystać, można [pobrać szkielet aplikacji w pliku .zip](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip) lub sklonować szkielet:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

Ukończona aplikacja znajduje się na końcu również w tym samouczku.

## <a name="step-1-register-an-app"></a>Krok 1: Rejestrowanie aplikacji
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W menu w górnej części strony wybierz konto. W obszarze **katalogu** wybierz dzierżawy usługi Active Directory, które chcesz zarejestrować aplikację.

3. Wybierz **wszystkie usługi** w menu po lewej stronie ekranu, a następnie wybierz **usługi Azure Active Directory**.

4. Wybierz **rejestracji aplikacji**, a następnie wybierz **Dodaj**.

5. Postępuj zgodnie z monitami, aby utworzyć **aplikacji sieci Web** i/lub **WebAPI**.
  * **Nazwa** aplikacji opisuje aplikacji dla użytkowników.

  * **Adres URL logowania** jest podstawowy adres URL aplikacji. Domyślnie szkielet `http://localhost:3000/auth/openid/return`.

6. Po zarejestrowaniu, usługi Azure AD przypisuje aplikacji identyfikatora aplikacji Potrzebujesz tej wartości w poniższych sekcjach, dlatego skopiuj go ze strony aplikacji.
7. Z **ustawienia** -> **właściwości** strony dla aplikacji, zaktualizuj identyfikator URI aplikacji. **Identyfikator URI aplikacji** to unikatowy identyfikator aplikacji. Konwencji jest użycie formatu `https://<tenant-domain>/<app-name>`, na przykład: `https://contoso.onmicrosoft.com/my-first-aad-app`.

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

2. Ponadto należy `passport-azure-ad`:
    * `npm install passport-azure-ad`

Spowoduje to zainstalowanie bibliotek który `passport-azure-ad` zależy.

## <a name="step-3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>Krok 3: Konfigurowanie aplikacji do użycia strategii passport-node-js
W tym miejscu możemy skonfigurować Express do używania protokołu uwierzytelniania OpenID Connect. Usługa Passport umożliwia wykonywanie różnych czynności, włącznie z żądaniami logowania i wylogowywania problem, zarządzanie sesji użytkownika i uzyskać informacje o użytkowniku.

1. Aby rozpocząć, otwórz `config.js` plików w katalogu głównym projektu, a następnie wprowadź wartości konfiguracji aplikacji w `exports.creds` sekcji.

  * `clientID` Jest **identyfikator aplikacji** przypisany do aplikacji w portalu rejestracji.

  * `returnURL` Jest **identyfikator Uri przekierowania** wprowadzony w portalu.

  * `clientSecret` Jest klucz tajny, który wygenerował w portalu.

2. Następnie otwórz folder `app.js` w katalogu głównym projektu. Następnie dodaj następujące wywołanie do wywołania `OIDCStrategy` strategii dołączoną `passport-azure-ad`.

    ```JavaScript
    var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

    // add a logger

    var log = bunyan.createLogger({
        name: 'Microsoft OIDC Example Web Application'
    });
    ```

3. Po wykonaniu tej Użyj strategii możemy właśnie w odwołaniu do obsługi żądań logowania naszych.

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
Usługa Passport używa podobnego wzorca do wszystkich swoich strategii (Twitter, Facebook itd.) zgodne ze wszyscy twórcy kodu strategii. Spojrzenie na strategii, możesz sprawdzić, czy możemy przekazać je funkcję, która ma token i gotowe jako parametry. Strategia zostanie zwrócona do nas po jego działa. Następnie chcemy przechować dane użytkownika i Zapisz token, więc nie należy zażądać go ponownie.

> [!IMPORTANT]
Poprzedni kod obejmuje wszystkich użytkowników, która do uwierzytelniania serwera. Jest to nazywane rejestracji automatycznej. Zaleca się, że nie zezwala każdy uwierzytelnienia na serwerze produkcyjnym bez uprzedniego je zarejestrować za pośrednictwem procesu, który podjęciu decyzji o. Jest to zwykle wzorzec, które są widoczne w aplikacjach komercyjnych, dzięki której można zarejestrować w usłudze Facebook, ale następnie wyświetlony monit o podanie dodatkowych informacji. Jeśli to nie zostały przykładowej aplikacji, być może wyodrębniono adres e-mail użytkownika z obiektu tokena, który został zwrócony, a następnie monit użytkownika o podanie dodatkowych informacji. Ponieważ jest to serwer testowy, możemy dodać je do bazy danych w pamięci.


4. Następnie możemy dodać metody, które umożliwiają śledzenie zalogowanych użytkowników zgodnie z wymaganiami programu Passport. Te metody obejmują serializację i deserializację informacji użytkownika.

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

5. Następnie możemy dodać kod, aby załadować aparat Express. W tym miejscu użyjemy /views domyślne i zapewnia wzorzec /routes Express.

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

6. Ponadto możemy dodać trasy, które przekazują rzeczywiste żądania rejestrowania do `passport-azure-ad` aparatu:

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


## <a name="step-4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>Krok 4: Użyj Passport do wysyłania żądań zalogowania się i wylogowania do usługi Azure AD
Aplikacji jest teraz prawidłowo skonfigurowana do komunikowania się z punktem końcowym przy użyciu protokołu uwierzytelniania OpenID Connect. `passport-azure-ad` ma podjąć obsługę wszystkich szczegółów obsługuje tworzenie komunikatów uwierzytelniania, sprawdzanie poprawności tokenów z usługi Azure AD i pozostawienie sesji użytkownika. Wszystkie punkty, które pozostaje przyznawanie użytkownikom możliwość zalogować się i wylogowywania i zbieranie dodatkowych informacji na temat zalogowanych użytkowników.

1. Po pierwsze Dodajmy domyślną, logowania, konta i wylogowania metody naszych `app.js` pliku:

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

2. Przejrzyj te szczegółowo umożliwia:

  * `/`Trasa przekierowuje do widoku index.ejs przekazanie użytkownika w żądaniu (jeśli istnieje).
  * `/account` Trasy najpierw *temu firma Microsoft są uwierzytelniani* (wdrożymy który w poniższym przykładzie) i następnie przekazuje użytkownika w żądaniu, dzięki czemu możemy uzyskać dodatkowe informacje o użytkowniku.
  * `/login` Trasy wywołuje naszych uwierzytelniania azuread openidconnect z `passport-azuread`. Jeśli które się nie powiedzie, przekierowuje użytkownika do /login.
  * `/logout` Po prostu wywołania logout.ejs (i trasę), które powoduje wyczyszczenie plików cookie, a następnie wróci użytkownika index.ejs trasy.

3. Ostatnia część `app.js`, Dodajmy **EnsureAuthenticated** metodę, która jest używana w `/account`, jak pokazano wcześniej.

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


## <a name="step-5-to-display-our-user-in-the-website-create-the-views-and-routes-in-express"></a>Krok 5: Aby wyświetlić naszych użytkowników w witrynie internetowej, tworzenie widoków i tras w Express
Teraz `app.js` została ukończona. Należy dodać trasy i widoki, które zawierają informacje, możemy uzyskać dostęp do użytkownika, jak również obsługiwać `/logout` i `/login` tras, na których utworzyliśmy.

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

3. Utwórz widok `/views/index.ejs` w katalogu głównym. Jest to prosta strona, która wywołuje metody naszych zalogowania i wylogowania i umożliwia firmie Microsoft w celu pobrania informacji o koncie. Powiadomienie, że możemy użyć spójnika `if (!user)` jako użytkownik, są przekazywane w żądaniu jest dowód mamy zalogowanego użytkownika.

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

4. Utwórz `/views/account.ejs` wyświetlać w katalogu głównym, co możemy wyświetlić dodatkowe informacje który `passport-azure-ad` umieścił w żądaniu użytkownika.

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

5. Upewnijmy to wygląd dobrej przez dodanie układ. Utwórz ' / views/layout.ejs' widoku w katalogu głównym.

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

Zaloguj się za pomocą osobistego konta Microsoft lub konta służbowego i zwróć uwagę, jak tożsamość użytkowników jest odzwierciedlone na liście /account. Masz teraz zabezpieczone przy użyciu standardowych protokołach branżowych, uwierzytelniających użytkowników przy użyciu ich osobistych i pracy/służbowego konta aplikacji sieci web.

Gotowa próbka (bez wartości konfiguracji) [jest dostępna w pliku .zip](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/complete.zip). Alternatywnie można ją sklonować z serwisu GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

Możesz teraz przejść do bardziej zaawansowanych tematów. Można spróbować:

[Zabezpieczanie interfejsu API za pomocą usługi Azure AD w sieci Web](active-directory-devquickstarts-webapi-nodejs.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
