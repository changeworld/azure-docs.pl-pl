---
title: Azure Active Directory v2.0 logowanie aplikacji sieci web Node.js | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia aplikacji sieci web Node.js, który loguje się użytkownik, używając osobistego konta Microsoft i konta firmowego lub szkolnego.
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 1b889e72-f5c3-464a-af57-79abf5e2e147
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 04/20/2018
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: eb16502ca255bf99c3780ff3975b6ca7f5a79993
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="add-sign-in-to-a-nodejs-web-app"></a>Dodaj logowanie do aplikacji sieci web Node.js

> [!NOTE]
> Nie wszystkie usługi Azure Active Directory scenariusze i funkcje działają z punktem końcowym v2.0. Aby ustalić, czy należy używać punktu końcowego v2.0 lub punkt końcowy w wersji 1.0, przeczytaj o [ograniczenia v2.0](active-directory-v2-limitations.md).
>

W tym samouczku używamy [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad) do wykonywania następujących zadań:

* W aplikacji sieci web logowania użytkownika, przy użyciu usługi Azure Active Directory (Azure AD) i punktu końcowego v2.0.
* Wyświetl informacje o użytkowniku.
* Zaloguj użytkownika poza aplikacją.

[Usługa Passport](http://passportjs.org/) to uwierzytelniające oprogramowanie pośredniczące dla środowiska Node.js. Elastyczne i moduły, usługa Passport można dyskretnie usunięty w każdym ekspresowe lub restify aplikacji sieci web. W usłudze Passport kompleksowy zestaw strategii obsługuje uwierzytelnianie przy użyciu nazwy użytkownika i hasła, Facebook, Twitter lub innych opcji. Opracowaliśmy strategię dla usługi Azure AD. W tym artykule, firma Microsoft opisano, jak zainstalować moduł, a następnie dodaj usługi Azure AD **passport-azure-ad** wtyczki.

## <a name="download"></a>Do pobrania
Kod używany w tym samouczku jest przechowywany [ w serwisie GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs). Aby użyć tego samouczka, możesz [pobrać szkielet aplikacji w pliku .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/skeleton.zip) lub sklonować szkielet:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

Możesz również uzyskać ukończona aplikacja na końcu tego samouczka.

## <a name="1-register-an-app"></a>1: Rejestrowanie aplikacji
Utwórz nową aplikację na [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), lub wykonaj [te szczegółowe kroki](active-directory-v2-app-registration.md) zarejestrować aplikację. Upewnij się, że:

* Kopiuj **identyfikator aplikacji** przypisany do aplikacji. Należy go w tym samouczku.
* Dodaj **Web** platformy aplikacji.
* Dodaj następujący adres URL skonfigurowany w próbce `http://localhost:3000/auth/openid/return` jako **identyfikator URI przekierowania**.

## <a name="2-prerequisities-to-run-the-sample"></a>2: wymagania wstępne, aby uruchomić przykładowy

Konieczne będzie zainstalowana tak, aby skompilować i uruchomić ten przykład środowiska node.js. Można zainstalować środowisko Node.js z http://nodejs.org/.

W wierszu polecenia przejdź do przejdź do folderu głównego, jeśli nie masz już istnieje.
Uruchom następujące polecenie, aby zainstalować moduły wymagań wstępnych węzła na liście `package.json` pliku:

`npm install`

Spowoduje również zainstalowanie biblioteki który `passport-azure-ad` używa.

## <a name="3-set-up-your-app-to-use-passport-azure-ad"></a>3: Konfigurowanie aplikacji przy użyciu usługi passport-azure-usługi ad
Konfigurowanie oprogramowania pośredniczącego Express do używania protokołu uwierzytelniania OpenID Connect. Możesz użyć programu Passport do wysyłania żądań zalogowania się i wylogowania, zarządzania sesji użytkownika i uzyskać informacje o użytkowniku, między innymi.

1.  W katalogu głównym projektu otwórz plik config.js. W `exports.creds` wprowadź wartości konfiguracji aplikacji.

  * `clientID`: **Identyfikator aplikacji** przypisany do aplikacji w portalu.
  * `returnURL`: **Identyfikator URI przekierowania** wprowadzony w portalu.
  * `clientSecret`Klucz tajny, który wygenerował w portalu.

2.  W katalogu głównym projektu otwórz plik app.js. Aby wywołać OIDCStrategy, który jest dostarczany z `passport-azure-ad`, Dodaj następujące wywołanie:

  ```JavaScript
  var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

  // Add some logging
  var log = bunyan.createLogger({
      name: 'Microsoft OIDC Example Web Application'
  });
  ```

3.  Do obsługi żądań logowania, użyj strategii, które właśnie w odwołaniu:

  ```JavaScript
  // Use the OIDCStrategy within Passport (section 2)
  //
  //   Strategies in Passport require a `validate` function. The function accepts
  //   credentials (in this case, an OpenID identifier), and invokes a callback
  //   with a user object.
  passport.use( new OIDCStrategy({
      callbackURL: config.creds.returnURL,
      realm: config.creds.realm,
      clientID: config.creds.clientID,
      clientSecret: config.creds.clientSecret,
      oidcIssuer: config.creds.issuer,
      identityMetadata: config.creds.identityMetadata,
      responseType: config.creds.responseType,
      responseMode: config.creds.responseMode,
      skipUserProfile: config.creds.skipUserProfile
      scope: config.creds.scope
    },
    function(iss, sub, profile, accessToken, refreshToken, done) {
      log.info('Example: Email address we received was: ', profile.email);
      // Asynchronous verification, for effect...
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

Usługa Passport używa podobnego wzorca do wszystkich swoich strategii (Twitter, Facebook i tak dalej). Wszyscy twórcy kodu strategii jest zgodna z wzorcem. Przekaż strategii `function()` używającą tokenu i `done` jako parametry. Strategia jest zwracane, gdy istnieje swojej pracy. Przechowaj dane użytkownika i Zapisz token, więc nie trzeba było zażądać go ponownie.

  > [!IMPORTANT]
  > Poprzedni kod obejmuje każdy użytkownik, który może uwierzytelniać się z serwerem. Jest to nazywane rejestracji automatycznej. Na serwerze produkcyjnym nie mają mieć możliwość każda osoba, która bez konieczności ich przejść przez proces rejestracji, który można wybrać. Jest to zwykle wzorzec, który pojawi się w aplikacjach dla użytkowników indywidualnych. Aplikacja może pozwala zarejestrować się w serwisie Facebook, ale następnie prosi o podanie dodatkowych informacji. Jeśli nie korzystasz z wiersza polecenia programu w tym samouczku, można wyodrębnić wiadomości e-mail z obiektu tokena, który jest zwracany. Następnie może poprosić użytkownika o podanie dodatkowych informacji. Ponieważ jest to serwer testowy, możesz dodać użytkownika bezpośrednio do bazy danych w pamięci.
  >
  >

4.  Dodaj metody, które służą do śledzenia użytkowników, którzy są podpisane, zgodnie z żądaniem usługi Passport. Obejmuje to serializację i deserializację informacji użytkownika:

  ```JavaScript

  // Passport session setup (section 2)

  //   To support persistent login sessions, Passport needs to be able to
  //   serialize users into, and deserialize users out of, the session. Typically,
  //   this is as simple as storing the user ID when serializing, and finding
  //   the user by ID when deserializing.
  passport.serializeUser(function(user, done) {
    done(null, user.email);
  });

  passport.deserializeUser(function(id, done) {
    findByEmail(id, function (err, user) {
      done(err, user);
    });
  });

  // Array to hold signed-in users
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

5.  Dodaj kod, który ładuje aparat Express. Użyj /views domyślne i zawiera wzorzec /routes Express:

  ```JavaScript

  // Set up Express (section 2)

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

6.  Dodaj WPIS kieruje tego Dostarcz rzeczywiste żądania rejestrowania do `passport-azure-ad` aparatu:

  ```JavaScript

  // Auth routes (section 3)

  // GET /auth/openid
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. The first step in OpenID authentication involves redirecting
  //   the user to the user's OpenID provider. After authenticating, the OpenID
  //   provider redirects the user back to this application at
  //   /auth/openid/return.

  app.get('/auth/openid',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {
      log.info('Authentication was called in the sample');
      res.redirect('/');
    });

  // GET /auth/openid/return
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. If authentication fails, the user is redirected back to the
  //   sign-in page. Otherwise, the primary route function is called.
  //   In this example, it redirects the user to the home page.
  app.get('/auth/openid/return',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {

      res.redirect('/');
    });

  // POST /auth/openid/return
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. If authentication fails, the user is redirected back to the
  //   sign-in page. Otherwise, the primary route function is called.
  //   In this example, it redirects the user to the home page.

  app.post('/auth/openid/return',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {

      res.redirect('/');
    });
  ```

## <a name="4-add-sign-in-and-sign-out-requests-to-azure-ad"></a>4: Dodaj żądań logowania się i wylogowania do usługi Azure AD
Aplikacja jest teraz skonfigurowane do komunikacji z punktem końcowym v2.0 za pomocą protokołu uwierzytelniania OpenID Connect. `passport-azure-ad` Strategii zajmuje się wszystkie szczegóły obsługuje tworzenie komunikatów uwierzytelniania, sprawdzanie poprawności tokenów z usługi Azure AD i podtrzymywanie sesji użytkowników. Pozostało celu ma zapewnić użytkownikom taki sposób, aby zalogować się i wylogowywania oraz zebrać więcej informacji na temat użytkownika, który jest zalogowany.

1.  Dodaj **domyślne**, **logowania**, **konta**, i **wylogowania** metod do pliku app.js:

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
      log.info('Login was called in the sample');
      res.redirect('/');
  });

  app.get('/logout', function(req, res){
    req.logout();
    res.redirect('/');
  });

  ```

  Oto szczegóły:

    * `/` Trasa przekierowuje do widoku index.ejs. Przekazuje użytkownika w żądaniu (jeśli istnieje).
    * `/account` Trasy najpierw *gwarantuje, że użytkownik jest uwierzytelniony* (zaimplementowaniem który w poniższym kodzie). Następnie przekazuje użytkownika w żądaniu. Jest to, aby uzyskać więcej informacji o użytkowniku.
    * `/login` Trasy wywołania z `azuread-openidconnect` uwierzytelniania z `passport-azuread`. Jeśli które się nie powiedzie, przekierowuje użytkownika do `/login`.
    * `/logout` Trasy wywołuje logout.ejs widoku (i trasę). Powoduje wyczyszczenie plików cookie, a następnie wróci użytkownika index.ejs.

2.  Dodaj **EnsureAuthenticated** metody użytej wcześniej wcześniej w `/account`:

  ```JavaScript

  // Route middleware to ensure the user is authenticated (section 4)

  //   Use this route middleware on any resource that needs to be protected. If
  //   the request is authenticated (typically via a persistent login session),
  //   the request proceeds. Otherwise, the user is redirected to the
  //   sign-in page.
  function ensureAuthenticated(req, res, next) {
    if (req.isAuthenticated()) { return next(); }
    res.redirect('/login')
  }

  ```

3.  W app.js należy utworzyć serwer:

  ```JavaScript

  app.listen(3000);

  ```


## <a name="5-create-the-views-and-routes-in-express"></a>5: tworzenie widoków i tras w Express
Dodaj trasy i widoki, które zawierają informacje dla użytkownika. Trasy i widoki również obsługiwać `/logout` i `/login` tras, które zostały utworzone.

1. W katalogu głównym, należy utworzyć `/routes/index.js` trasy.

  ```JavaScript

  /*
  * GET home page.
  */

  exports.index = function(req, res){
    res.render('index', { title: 'Express' });
  };
  ```

2.  W katalogu głównym, należy utworzyć `/routes/user.js` trasy.

  ```JavaScript

  /*
  * GET users listing.
  */

  exports.list = function(req, res){
    res.send("respond with a resource");
  };
  ```

  `/routes/index.js` i `/routes/user.js` są proste trasy przekazują żądania do widoków, w tym użytkownika, jeśli jest obecny.

3.  W katalogu głównym, należy utworzyć `/views/index.ejs` widoku. Ta strona wymaga programu **logowania** i **wylogowania** metody. Można również użyć `/views/index.ejs` widok, aby przechwycić informacje o koncie. Możesz użyć spójnika `if (!user)` jako użytkownik, są przekazywane w żądaniu. Jest dowód, że zalogowany użytkownik.

  ```JavaScript
  <% if (!user) { %>
      <h2>Welcome! Please sign in.</h2>
      <a href="/login">Sign in</a>
  <% } else { %>
      <h2>Hello, <%= user.displayName %>.</h2>
      <a href="/account">Account info</a></br>
      <a href="/logout">Sign out</a>
  <% } %>
  ```

4.  W katalogu głównym, należy utworzyć `/views/account.ejs` widoku. `/views/account.ejs` Widoku umożliwia wyświetlanie dodatkowych informacji który `passport-azuread` umieszcza w żądaniu użytkownika.

  ```Javascript
  <% if (!user) { %>
      <h2>Welcome! Please sign in.</h2>
      <a href="/login">Sign in</a>
  <% } else { %>
  <p>displayName: <%= user.displayName %></p>
  <p>givenName: <%= user.name.givenName %></p>
  <p>familyName: <%= user.name.familyName %></p>
  <p>UPN: <%= user._json.upn %></p>
  <p>Profile ID: <%= user.id %></p>
  <p>Full Claimes</p>
  <%- JSON.stringify(user) %>
  <p></p>
  <a href="/logout">Sign out</a>
  <% } %>
  ```

5.  Dodawanie układu. W katalogu głównym, należy utworzyć `/views/layout.ejs` widoku.

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
              <a href="/login">Sign in</a>
              </p>
          <% } else { %>
              <p>
              <a href="/">Home</a> |
              <a href="/account">Account</a> |
              <a href="/logout">Sign out</a>
              </p>
          <% } %>
          <%- body %>
      </body>
  </html>
  ```


  ## <a name="6-build-and-run-your-app"></a>6: tworzenie i uruchamianie aplikacji
  1. Aby skompilować i uruchomić aplikację, uruchom następujące polecenie z katalogu głównego aplikacji.

  `node app.js`

   Następnie przejdź do `http://localhost:3000` w przeglądarce.

  2. Zaloguj się za pomocą osobistego konta Microsoft lub konta służbowego. Należy pamiętać, że tożsamość użytkownika jest widoczny w `/account` trasy.

Masz teraz aplikacji sieci web, który jest zabezpieczony za pomocą standardowych protokołach branżowych. Użytkownicy mogą uwierzytelniać w aplikacji za pomocą ich kont osobistych i służbowych.

## <a name="next-steps"></a>Kolejne kroki
Odwołania, ukończonych próbka (bez wartości konfiguracji) jest dostępna jako [plik zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/complete.zip). Użytkownik może ją także sklonować z serwisu GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

Następnie możesz przejść do bardziej zaawansowanych tematów. Można spróbować:

[Zabezpieczanie interfejsu API sieci web Node.js za pomocą punktu końcowego v2.0](active-directory-v2-devquickstarts-node-api.md)

Poniżej przedstawiono dodatkowe zasoby:

* [Przewodnik dewelopera v2.0 usługi Azure AD](active-directory-appmodel-v2-overview.md)
* [Tag "azure-active-directory" przepełnienie stosu](http://stackoverflow.com/questions/tagged/azure-active-directory)

### <a name="get-security-updates-for-our-products"></a>Pobierz aktualizacje zabezpieczeń naszych produktów
Firma Microsoft zachęca do Zarejestruj się, aby otrzymać powiadomienie, gdy występujących incydentach zabezpieczeń. Na [powiadomień o zabezpieczeniach technicznych Microsoft](https://technet.microsoft.com/security/dd252948) strony, należy subskrybować alerty klasyfikatory zabezpieczeń.
