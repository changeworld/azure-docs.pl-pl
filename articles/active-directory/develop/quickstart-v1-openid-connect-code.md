---
title: Tworzenie aplikacji internetowej Node.js Express służącej do logowania się w usłudze Azure Active Directory i wylogowywania się z niej | Microsoft Docs
description: Dowiedz się, jak utworzyć aplikację internetową MVC platformy Node.js Express, która integruje się z usługą Azure AD na potrzeby logowania.
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
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 19563f76c261fda1fca53babcb553f2dceeaa345
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990097"
---
# <a name="quickstart-build-a-nodejs-express-web-app-for-sign-in-and-sign-out-with-azure-active-directory"></a>Szybki start: tworzenie aplikacji internetowej Node.js Express służącej do logowania się w usłudze Azure Active Directory i wylogowywania się z niej

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

Usługa Passport to uwierzytelniające oprogramowanie pośredniczące dla środowiska Node.js. Jest ono elastyczne oraz modułowe i można go dyskretnie używać w dowolnej aplikacji internetowej opartej na module Express lub Restify. Kompleksowy zestaw strategii obsługuje uwierzytelnianie przy użyciu m.in. nazwy użytkownika i hasła lub kont w serwisach Facebook i Twitter. Ten moduł zostanie zainstalowany na potrzeby usługi Azure Active Directory (Azure AD), po czym dodana zostanie wtyczka `passport-azure-ad` usługi Azure AD.

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności za pomocą usługi Passport:

* Logowanie użytkownika do aplikacji za pomocą usługi Azure AD.
* Wyświetlanie informacji o użytkowniku.
* Wylogowywanie użytkownika z aplikacji.

Aby utworzyć gotową, działającą aplikację, musisz:

1. Zarejestrować aplikację.
2. Skonfigurować aplikację do korzystania ze strategii `passport-azure-ad`.
3. Użyć programu Passport do wysyłania do usługi Azure AD żądań zalogowania się i wylogowania.
4. Wyświetlić dane o użytkowniku.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, należy spełnić poniższe następujące wstępne:

* [Pobrać szkielet aplikacji w pliku .zip](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip)
  
    * Sklonować szkielet:

        ```git clone --branch skeleton https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

    Kod używany w tym przewodniku Szybki start jest przechowywany w serwisie [GitHub](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS). Gotowa aplikacja znajduje się również na końcu tego przewodnika Szybki start.

* Dzierżawa usługi Azure AD, w której można utworzyć użytkowników i zarejestrować aplikację. Jeśli nie masz jeszcze dzierżawy, [dowiedz się, jak ją uzyskać](quickstart-create-new-tenant.md).

## <a name="step-1-register-an-app"></a>Krok 1. Rejestrowanie aplikacji

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W menu w górnej części strony wybierz swoje konto. Z listy **Katalog** wybierz dzierżawę usługi Active Directory, w której chcesz zarejestrować aplikację.
1. Wybierz pozycję **Wszystkie usługi** w menu po lewej stronie ekranu, a następnie wybierz pozycję **Azure Active Directory**.
1. Wybierz pozycję **Rejestracje aplikacji**, a następnie wybierz pozycję **Dodaj**.
1. Postępuj zgodnie z monitami, aby utworzyć **aplikację internetową** i/lub **internetowy interfejs API**.

    * **Nazwa** aplikacji opisuje Twoją aplikację innym użytkownikom.
    * **Adres URL logowania** to podstawowy adres URL aplikacji. Wartość domyślnie dla szkieletu to `http://localhost:3000/auth/openid/return`.

1. Po rejestracji usługa Azure AD przypisze aplikacji unikatowy identyfikator. Ta wartość będzie potrzebna w kolejnych sekcjach, więc skopiuj ją ze strony aplikacji.
1. Na stronie **Ustawienia > Właściwości** zaktualizuj identyfikator URI aplikacji. 
    
    **Identyfikator URI aplikacji** to unikatowy identyfikator aplikacji. W ramach konwencji nazewnictwa używany jest format `https://<tenant-domain>/<app-name>`, na przykład: `https://contoso.onmicrosoft.com/my-first-aad-app`.

1. Na stronie **Ustawienia > Adresy URL odpowiedzi** dla Twojej aplikacji dodaj adres URL dodany polu Adres URL logowania z kroku 5, a następnie wybierz pozycję **Zapisz**.
1. Aby utworzyć klucz tajny, przejdź do kroku 4 w sekcji [Dodawanie poświadczeń lub uprawnień na potrzeby uzyskiwania dostępu do internetowych interfejsów API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#to-add-application-credentials-or-permissions-to-access-web-apis).

   > [!IMPORTANT]
   > Skopiuj wartość klucza aplikacji. Jest to wartość parametru `clientSecret`, która będzie potrzebna w ramach **kroku 3** poniżej. 

## <a name="step-2-add-prerequisites-to-your-directory"></a>Krok 2. Dodawanie wymagań wstępnych do katalogu

1. Z poziomu wiersza polecenia zmień katalogi na folder główny, jeśli jeszcze tak się nie stało, a następnie uruchom następujące polecenia:

    * `npm install express`
    * `npm install ejs`
    * `npm install ejs-locals`
    * `npm install restify`
    * `npm install mongoose`
    * `npm install bunyan`
    * `npm install assert-plus`
    * `npm install passport`

1. Wymagany jest również moduł `passport-azure-ad`, więc uruchom następujące polecenie:

    * `npm install passport-azure-ad`

Spowoduje to zainstalowanie bibliotek, od których zależy moduł `passport-azure-ad`.

## <a name="step-3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>Krok 3. Konfigurowanie aplikacji do korzystania ze strategii passport-node-js

Teraz skonfigurujesz moduł Express do używania protokołu uwierzytelniania OpenID Connect. Usługa Passport umożliwia wykonywanie różnych działań, w tym wysyłanie żądań logowania i wylogowywania, zarządzanie sesjami użytkownika i uzyskiwanie informacji o użytkowniku.

1. Otwórz plik `config.js` w katalogu głównym projektu, a następnie wprowadź wartości konfiguracyjne aplikacji w sekcji `exports.creds`.

    * Parametr `clientID` to **identyfikator aplikacji** przypisany do aplikacji w portalu rejestracji.
    * Parametr `returnURL` to **adres URL odpowiedzi** wprowadzony w portalu.
    * Parametr `clientSecret` to klucz tajny wygenerowany w portalu.

1. Otwórz plik `app.js` w folderze głównym projektu. Dodaj następujące wywołanie, aby wywołać strategię `OIDCStrategy` dołączoną do modułu `passport-azure-ad`.

    ```JavaScript
    var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

    // add a logger

    var log = bunyan.createLogger({
        name: 'Microsoft OIDC Example Web Application'
    });
    ```

1. Następnie użyj strategii umieszczonej właśnie w odwołaniu do obsługi żądań logowania.

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
   Usługa Passport używa podobnego wzorca dla wszystkich swoich strategii (usługi Twitter, Facebook itd.), który stosują wszyscy twórcy strategii. Po wyświetleniu strategii można zobaczyć, że przekazywana jest do niej funkcja zawierająca token, metodę done oraz parametry. Strategia zostanie zwrócona po wykonaniu działania. Następnie chcemy przechować dane użytkownika i tokenu, aby nie trzeba było żądać go ponownie.

   > [!IMPORTANT]
   > Powyższy kod obejmuje wszystkich użytkowników, którzy są uwierzytelniani na serwerze. Ta czynność nazywana jest rejestracją automatyczną. Zaleca się, aby nie zezwalać nikomu na uwierzytelnianie na serwerze produkcyjnym bez konieczności wcześniejszej rejestracji przy użyciu wybranego przez Ciebie procesu. Zwykle stosowany jest wzorzec występujący w aplikacjach konsumenckich, który zezwala na rejestrację przy użyciu konta w serwisie Facebook, po czym wyświetlony zostaje monit o podanie dodatkowych informacji. Gdyby ta aplikacja nie była aplikacją przykładową, można by było wyodrębnić adres e-mail użytkownika ze zwracanego obiektu tokenu, a następnie poprosić użytkownika o podanie dodatkowych informacji. Ze względu na to, że jest to serwer testowy, informacje zostają dodane do bazy danych w pamięci.

1. Należy dodać metody, które umożliwiają śledzenie zalogowanych użytkowników zgodnie z wymogami usługi Passport. Te metody obejmują serializację i deserializację informacji o użytkowniku.

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

1. Dodaj kod, aby załadować aparat Express. W tym miejscu użyjemy domyślnych wzorców /views i /routes udostępnianych przez usługę Express.

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

1. Na końcu należy dodać trasy, które przekazują rzeczywiste żądania rejestrowania do aparatu `passport-azure-ad`:

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

## <a name="step-4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>Krok 4. Korzystanie z usługi Passport do wysyłania żądań zalogowania się i wylogowania do usługi Azure AD

Aplikacja jest teraz prawidłowo skonfigurowana do komunikowania się z punktem końcowym za pomocą protokołu uwierzytelniania OpenID Connect. Aparat `passport-azure-ad` obsługuje wszystkie kwestie związane z tworzeniem komunikatów uwierzytelniania, sprawdzaniem poprawności tokenów z usługi Azure AD i podtrzymywaniem sesji użytkowników. Pozostaje tylko zapewnić użytkownikom możliwość logowania się i wylogowywania oraz zebrać dodatkowe informacje o zalogowanych użytkownikach.

1. Do pliku `app.js` dodaj metody dotyczące następujących tras: domyślnej, konta, logowania i wylogowywania:

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

1. Zapoznaj się z ich szczegółowym omówieniem:

    * Trasa `/` przekierowuje do widoku index.ejs i przekazuje użytkownika w żądaniu (jeśli istnieje).
    * Trasa `/account` najpierw *zapewnia, że użytkownik jest uwierzytelniony* (zostanie to zaimplementowane w poniższym przykładzie), a następnie przekazuje użytkownika w żądaniu, dzięki czemu możemy uzyskać o nim dodatkowe informacje.
    * Trasa `/login` wywołuje wystawcę uwierzytelnienia azuread-openidconnect z modułu `passport-azuread`. Jeśli to się nie powiedzie, użytkownik jest przekierowywany z powrotem do trasy /login.
    * Trasa `/logout` po prostu wywołuje widok logout.ejs (i trasę), która powoduje wyczyszczenie plików cookie, a następnie przekierowuje użytkownika z powrotem do widoku index.ejs.

1. Na końcu pliku `app.js` dodaj metodę **EnsureAuthenticated**, która jest używana w trasie `/account`, co pokazano wcześniej.

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

1. Na koniec utwórz sam serwer w pliku `app.js`:

    ```JavaScript
    app.listen(3000);
    ```

## <a name="step-5-to-display-our-user-in-the-website-create-the-views-and-routes-in-express"></a>Krok 5. Wyświetlanie użytkowników w witrynie internetowej oraz tworzenie widoków i tras w module Express

Plik `app.js` jest gotowy. Teraz wystarczy dodać trasy i widoki, które wyświetlają informacje przekazywane do użytkownika, a także obsługują trasy `/logout` i `/login`, które utworzyliśmy.

1. Utwórz trasę `/routes/index.js` w obszarze katalogu głównego.

    ```JavaScript
    /*
     * GET home page.
     */

    exports.index = function(req, res){
      res.render('index', { title: 'Express' });
    };
    ```

1. Utwórz trasę `/routes/user.js` w obszarze katalogu głównego.

    ```JavaScript
    /*
     * GET users listing.
     */

    exports.list = function(req, res){
      res.send("respond with a resource");
    };
    ```

    Służą one do przekazywania żądania do naszych widoków, w tym użytkownika, jeśli istnieje.

1. Utwórz widok `/views/index.ejs` w katalogu głównym. Jest to prosta strona, która wywołuje nasze metody logowania i wylogowania oraz umożliwia pobieranie informacji o koncie. Zwróć uwagę, że możemy użyć warunkowej metody `if (!user)`, ponieważ użytkownik przekazywany w żądaniu to na pewno użytkownik zalogowany.

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

1. Utwórz widok `/views/account.ejs` w katalogu głównym, aby wyświetlić dodatkowe informacje umieszczone przez moduł `passport-azure-ad` w żądaniu użytkownika.

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
    <p>Full Claimes</p>
    <%- JSON.stringify(user) %>
    <p></p>
    <a href="/logout">Log Out</a>
    <% } %>
    ```

1. Dodaj układ, aby poprawić wygląd strony. Utwórz widok `/views/layout.ejs` w katalogu głównym.

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

## <a name="step-6-build-and-run-your-app"></a>Krok 6. Tworzenie i uruchamianie aplikacji

1. Uruchom plik `node app.js`, a następnie przejdź pod adres `http://localhost:3000`.
1. Zaloguj się, używając osobistego lub służbowego konta Microsoft.

    Zwróć uwagę, w jaki sposób tożsamość użytkownika jest odzwierciedlana na liście /account. Twoja aplikacja internetowa jest teraz zabezpieczona za pomocą standardowych protokołów branżowych, które umożliwiają uwierzytelnianie użytkowników przy użyciu kont osobistych i służbowych.

    Gotowa próbka (bez wartości konfiguracji) [jest dostępna w pliku .zip](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/master.zip). Alternatywnie możesz go sklonować z repozytorium GitHub:

    ```git clone --branch master https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

## <a name="next-steps"></a>Następne kroki

Możesz teraz przejść do wypróbowania innych scenariuszy:

> [!div class="nextstepaction"]
> [Zabezpieczanie internetowego interfejsu API za pomocą usługi Azure AD](quickstart-v1-nodejs-webapi.md)