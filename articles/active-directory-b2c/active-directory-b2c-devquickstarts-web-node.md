---
title: Dodaj logowanie do aplikacji sieci web Node.js w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Jak utworzyć aplikację sieci web Node.js, który loguje się użytkowników z usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 03/10/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: b9c589f8bf8189961f88a6ae1d66f1fd9e17e9bc
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711114"
---
# <a name="azure-ad-b2c-add-sign-in-to-a-nodejs-web-app"></a>Azure AD B2C: dodawanie funkcji logowania się do aplikacji sieci Web w środowisku Node.js

**Passport** to uwierzytelniające oprogramowanie pośredniczące dla środowiska Node.js. Jest to elastyczne i modułowe oprogramowanie, które można dyskretnie zainstalować w dowolnej aplikacji sieci Web opartej na module Express lub Restify. Kompleksowy zestaw strategii obsługuje uwierzytelnianie przy użyciu m.in. nazwy użytkownika i hasła lub kont w serwisach Facebook i Twitter.

Usługi Azure Active Directory (Azure AD), można zainstalować ten moduł i następnie dodać usługi Azure AD `passport-azure-ad` wtyczki.

Należy:

1. Zarejestrować aplikację w usłudze Azure AD.
2. Skonfigurować aplikację do korzystania z wtyczki `passport-azure-ad`.
3. Użyć programu Passport do wysyłania do usługi Azure AD żądań zalogowania się i wylogowania.
4. Wydrukować dane użytkownika.

Kod używany w tym samouczku [jest przechowywany w serwisie GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS). Aby z niego skorzystać, można [pobrać szkielet aplikacji w pliku .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/master.zip). Można również sklonować szkielet:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS.git```

Ukończona aplikacja znajduje się na końcu tego samouczka.

## <a name="get-an-azure-ad-b2c-directory"></a>Tworzenie katalogu usługi Azure AD B2C

Przed rozpoczęciem korzystania z usługi Azure AD B2C należy utworzyć katalog lub dzierżawę.  Katalog jest kontenerem dla wszystkich użytkowników, aplikacji, grup i innych elementów. Jeśli jeszcze go nie masz, [utwórz katalog usługi B2C](active-directory-b2c-get-started.md), zanim przejdziesz dalej.

## <a name="create-an-application"></a>Tworzenie aplikacji

Następnie musisz utworzyć aplikację w katalogu usługi B2C. Dzięki temu do usługi Azure AD będą przekazywane informacje wymagane do bezpiecznego komunikowania się z aplikacją. Zarówno aplikacja klienta, jak i interfejs API sieci Web będą reprezentowane przez jeden **Identyfikator aplikacji**, ponieważ stanowią jedną aplikację logiczną. Aby utworzyć aplikację, postępuj zgodnie z [tymi instrukcjami](active-directory-b2c-app-registration.md). Należy pamiętać o wykonaniu następujących czynności:

- Dołącz do aplikacji **aplikację sieci Web**/**interfejs API sieci Web**.
- Wprowadź `http://localhost:3000/auth/openid/return` w polu **Adres URL odpowiedzi**. Jest to domyślny adres URL dla tej próbki kodu.
- Utwórz **klucz tajny aplikacji** i skopiuj go. Będzie potrzebny później. Pamiętaj, że aby go użyć, jego wartość musi być [ujęta w kodzie XML w znaki ucieczki](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape).
- Skopiuj **Identyfikator aplikacji** przypisany do aplikacji. On również będzie później potrzebny.

## <a name="create-your-policies"></a>Tworzenie zasad

W usłudze Azure AD B2C każde działanie użytkownika jest definiowane przy użyciu [zasad](active-directory-b2c-reference-policies.md). Ta aplikacja obejmuje trzy środowiska tożsamości: rejestracji, logowania i logowania przy użyciu konta w serwisie Facebook. Te zasady musisz utworzyć dla każdego typu zgodnie z opisem w [artykule dotyczącym struktury zasad](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Podczas tworzenia trzech zbiorów zasad należy koniecznie:

- Wybrać wartość **Nazwa wyświetlana** i inne atrybuty rejestracji w ramach zasad rejestracji.
- Wybrać oświadczenia aplikacji **Nazwa wyświetlana** oraz **Identyfikator obiektu** we wszystkich zasadach. Można również wybrać inne oświadczenia.
- Skopiować każdą utworzoną wartość **Nazwa** zasad. Powinny zawierać prefiks `b2c_1_`.  Te nazwy zasad będą potrzebne później.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Po utworzeniu trzech zbiorów zasad można rozpocząć tworzenie aplikacji.

Należy pamiętać, że w tym artykule nie opisano sposobu korzystania z nowo utworzonych zasad. Aby dowiedzieć się, jak działają zasady w usłudze Azure AD B2C, należy najpierw zapoznać się z [samouczkiem ułatwiającym rozpoczęcie pracy z aplikacją sieci Web platformy .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="add-prerequisites-to-your-directory"></a>Dodawanie wymagań wstępnych do katalogu

W wierszu polecenia przełącz katalogi na folder główny, jeśli jeszcze tak się nie stało. Uruchom następujące polecenia:

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`
- `npm install webfinger`
- `npm install body-parser`
- `npm install express-session`
- `npm install cookie-parser`

Dodatkowo w naszym podglądzie w przewodniku Szybki start użyliśmy elementu `passport-azure-ad`.

- `npm install passport-azure-ad`

Spowoduje to zainstalowanie bibliotek, od których zależy pakiet `passport-azure-ad`.

## <a name="set-up-your-app-to-use-the-passport-nodejs-strategy"></a>Konfigurowanie aplikacji do korzystania ze strategii Passport-Node.js
Konfigurowanie oprogramowania pośredniczącego Express do używania protokołu uwierzytelniania OpenID Connect. Program Passport będzie używany do wysyłania żądań zalogowania się i wylogowania, zarządzania sesjami użytkownika i uzyskiwania informacji o użytkownikach oraz do innych działań.

Otwórz plik `config.js` w katalogu głównym projektu i wprowadź wartości konfiguracji aplikacji w sekcji `exports.creds`.
- `clientID`: **Identyfikator aplikacji** przypisany do aplikacji w portalu rejestracji.
- `returnURL`: **Identyfikator URI przekierowania** wprowadzony w portalu.
- `tenantName`: Nazwa dzierżawy aplikacji, np. **contoso.onmicrosoft.com**.

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Otwórz plik `app.js` w folderze głównym projektu. Dodaj następujące wywołanie, aby wywołać strategię `OIDCStrategy` dołączoną do metody `passport-azure-ad`.


```javascript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// Add some logging
var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

Użyj strategii umieszczonej właśnie w odwołaniu do obsługi żądań logowania.

```javascript
// Use the OIDCStrategy in Passport (Section 2).
//
//   Strategies in Passport require a "validate" function that accepts
//   credentials (in this case, an OpenID identifier), and invokes a callback
//   by using a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    skipUserProfile: config.creds.skipUserProfile,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode,
    tenantName: config.creds.tenantName
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    log.info('Example: Email address we received was: ', profile.email);
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
Program Passport używa podobnego wzorca do wszystkich swoich strategii (w tym serwisów Facebook i Twitter). Ten wzorzec stosują wszyscy twórcy kodu strategii. Po wyświetleniu strategii można zobaczyć, że przekazujesz ją jako ciąg `function()`, w którym parametrami są token i element `done`. Strategia zostanie zwrócona po wykonaniu całego zadania. Przechowaj dane użytkownika i zapisz token, aby nie trzeba było zażądać go ponownie.

> [!IMPORTANT]
Poprzedni kod obejmuje wszystkich użytkowników, których uwierzytelnia serwer. Jest to autorejestracja. Na serwerach produkcyjnych dostęp powinni mieć tylko ci użytkownicy, którzy przeszli przygotowany dla nich proces rejestracji. Sposób ten jest często stosowany w aplikacjach dla użytkowników indywidualnych. Umożliwia on zarejestrowanie się za pomocą serwisu Facebook, ale potem użytkownik zostaje poproszony o dodatkowe informacje. Gdyby ta aplikacja nie była przykładem, można by było wyodrębnić adres e-mail ze zwracanego obiektu tokenu, a następnie poprosić użytkownika o podanie dodatkowych informacji. Ze względu na to, że jest to serwer testowy, użytkownicy zostają po prostu dodani do bazy danych w pamięci.

Dodaj metody, które pozwalają na śledzenie użytkowników, którzy zalogowali się, zgodnie z żądaniem programu Passport. Obejmuje to serializację i deserializację informacji o użytkowniku:

```javascript

// Passport session setup. (Section 2)

//   To support persistent sign-in sessions, Passport needs to be able to
//   serialize users into and deserialize users out of sessions. Typically,
//   this is as simple as storing the user ID when Passport serializes a user
//   and finding the user by ID when Passport deserializes that user.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// Array to hold users who have signed in
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

Dodaj kod, aby załadować aparat Express. Poniżej widać, że używana jest wartość domyślna `/views` i wzorzec `/routes` dostarczany przez aparat Express.

```javascript

// configure Express (Section 2)

var app = express();


app.configure(function() {
  app.set('views', __dirname + '/views');
  app.set('view engine', 'ejs');
  app.use(express.logger());
  app.use(express.methodOverride());
  app.use(cookieParser());
  app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
  app.use(bodyParser.urlencoded({ extended : true }));
  // Initialize Passport!  Also use passport.session() middleware to support
  // persistent sign-in sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

Dodaj trasy `POST`, które przekazują rzeczywiste żądania rejestrowania do aparatu `passport-azure-ad`:

```javascript

// Our Auth routes (Section 3)

// GET /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request. The first step in OpenID authentication involves redirecting
//   the user to an OpenID provider. After the user is authenticated,
//   the OpenID provider redirects the user back to this application at
//   /auth/openid/return

app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authentication was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request. If authentication fails, the user will be redirected back to the
//   sign-in page. Otherwise, the primary route function will be called.
//   In this example, it redirects the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request. If authentication fails, the user will be redirected back to the
//   sign-in page. Otherwise, the primary route function will be called.
//   In this example, it will redirect the user to the home page.

app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });
```

## <a name="use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>Korzystanie z programu Passport do wysyłania żądań zalogowania się i wylogowania do usługi Azure AD

Aplikacja jest teraz prawidłowo skonfigurowana do komunikowania się z punktem końcowym v2.0 za pomocą protokołu uwierzytelniania OpenID Connect. Aparat `passport-azure-ad` obsługuje tworzenie komunikatów uwierzytelniania, sprawdzanie poprawności tokenów z usługi Azure AD i podtrzymywanie sesji użytkowników. Pozostaje tylko zapewnić użytkownikom możliwość logowania się i wylogowywania oraz zebrać dodatkowe informacje o użytkownikach, którzy się zalogowali.

Najpierw dodaj metody domyślną, logowania, konta i wylogowania do pliku `app.js`:

```javascript

//Routes (Section 4)

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

Przyjrzyjmy się tym metodom dokładniej:
- Trasa `/` przekierowuje do widoku `index.ejs` przez przekazanie użytkownika w żądaniu (jeśli istnieje).
- Trasa `/account` sprawdza najpierw, czy użytkownik został uwierzytelniony (to wdrożenie zostało przedstawione poniżej). Następnie przekazuje użytkownika w żądaniu, co umożliwia uzyskanie dodatkowych informacji o użytkowniku.
- Trasa `/login` wywołuje wywołania uwierzytelniania `azuread-openidconnect` z `passport-azure-ad`. Jeśli to się nie powiedzie, trasa przekierowuje użytkownika z powrotem do `/login`.
- Trasa `/logout` po prostu wywołuje widok `logout.ejs` (i jego trasę). Powoduje wyczyszczenie plików cookie, a następnie przekierowuje użytkownika z powrotem do `index.ejs`.


W przypadku ostatniej części `app.js` dodaj metodę `EnsureAuthenticated` używaną w trasie `/account`.

```javascript

// Simple route middleware to ensure that the user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected. If
//   the request is authenticated (typically via a persistent sign-in session),
//   then the request will proceed. Otherwise, the user will be redirected to the
//   sign-in page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}

```

Na koniec utwórz sam serwer w `app.js`.

```javascript

app.listen(3000);

```


## <a name="create-the-views-and-routes-in-express-to-call-your-policies"></a>Tworzenie widoków i tras w aparacie Express w celu wywołania zasad

Element `app.js` jest teraz gotowy. Wystarczy dodać trasy i widoki, które umożliwią wywoływanie zasad logowania i rejestrowania. Obsługują one również utworzone trasy `/logout` i `/login`.

Utwórz trasę `/routes/index.js` w obszarze katalogu głównego.

```javascript

/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

Utwórz trasę `/routes/user.js` w obszarze katalogu głównego.

```javascript

/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

Te proste trasy przekazują żądania do widoków. Obejmują one użytkownika (o ile on istnieje).

Utwórz widok `/views/index.ejs` w katalogu głównym. Jest to prosta strona, która wywołuje zasady dotyczące logowania się i wylogowywania. Można jej również użyć do pobrania informacji o koncie. Pamiętaj, że możesz użyć spójnika `if (!user)`, ponieważ dane użytkownika są przekazywane w żądaniu, aby udowodnić, że użytkownik jest zalogowany.

```javascript
<% if (!user) { %>
    <h2>Welcome! Please sign in.</h2>
    <a href="/login/?p=your facebook policy">Sign in with Facebook</a>
    <a href="/login/?p=your email sign-in policy">Sign in with email</a>
    <a href="/login/?p=your email sign-up policy">Sign up with email</a>
<% } else { %>
    <h2>Hello, <%= user.displayName %>.</h2>
    <a href="/account">Account info</a></br>
    <a href="/logout">Log out</a>
<% } %>
```

Utwórz widok `/views/account.ejs` w katalogu głównym, aby wyświetlić dodatkowe informacje, które element `passport-azure-ad` umieścił w żądaniu użytkownika.

```javascript
<% if (!user) { %>
    <h2>Welcome! Please sign in.</h2>
    <a href="/login">Sign in</a>
<% } else { %>
<p>displayName: <%= user.displayName %></p>
<p>givenName: <%= user.name.givenName %></p>
<p>familyName: <%= user.name.familyName %></p>
<p>UPN: <%= user._json.upn %></p>
<p>Profile ID: <%= user.id %></p>
<p>Full Claims</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>
```

Teraz możesz skompilować i uruchomić aplikację.

Uruchom plik `node app.js` i przejdź do adresu `http://localhost:3000`


Zarejestruj się lub zaloguj w aplikacji przy użyciu adresu e-mail lub konta w serwisie Facebook. Wyloguj się i zaloguj ponownie jako inny użytkownik.

## <a name="next-steps"></a>Kolejne kroki

Gotowa próbka (bez wartości konfiguracji) [jest dostępna w pliku .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/complete.zip). Można ją także sklonować z serwisu GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-nodejs.git```

Możesz teraz przejść do bardziej zaawansowanych tematów. Zobacz te tematy:

[Zabezpieczanie interfejsu API sieci Web za pomocą modelu usługi B2C w środowisku Node.js](active-directory-b2c-devquickstarts-api-node.md)

<!--

For additional resources, check out:
You can now move on to more advanced B2C topics. You might try:

[Call a Node.js web API from a Node.js web app]()

[Customizing the your B2C App's UX >>]()

-->
