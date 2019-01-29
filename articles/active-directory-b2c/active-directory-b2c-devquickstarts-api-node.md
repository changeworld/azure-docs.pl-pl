---
title: Zabezpieczanie interfejsu API sieci web przy użyciu środowiska Node.js w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Jak utworzyć środowiska Node.js internetowego interfejsu API, który akceptuje tokeny od dzierżawcy usługi B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/07/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 84fd2356099ec11db011f20310938e3fc68f3bae
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55165970"
---
# <a name="secure-a-web-api-by-using-nodejs-in-azure-active-directory-b2c"></a>Zabezpieczanie interfejsu API sieci web przy użyciu środowiska Node.js w usłudze Azure Active Directory B2C

Usługa Azure Active Directory (Azure AD) B2C umożliwia zabezpieczanie interfejsu API sieci Web za pomocą tokenów dostępu protokołu OAuth 2.0. Tokeny te służą aplikacjom klienckim używającym usługi Azure AD B2C do uwierzytelniania w obrębie interfejsu API. W tym artykule pokazano, jak utworzyć interfejs API „lista zadań do wykonania”, umożliwiający użytkownikom dodawanie zadań i tworzenie listy zadań. Interfejs API sieci Web jest zabezpieczony za pomocą usługi Azure AD B2C i zezwala na zarządzanie listami zadań do wykonania tylko uwierzytelnionym użytkownikom.

> [!NOTE]
> Ta próbka została zapisana w taki sposób, aby podłączanie do niej odbywało się przy użyciu [przykładowej aplikacji B2C systemu iOS](active-directory-b2c-devquickstarts-ios.md). Należy najpierw zapoznać się z bieżącym przewodnikiem, a następnie kontynuować pracę z próbką.
>
>

**Passport** to uwierzytelniające oprogramowanie pośredniczące dla środowiska Node.js. Jest to elastyczne i modułowe oprogramowanie, które można dyskretnie zainstalować w dowolnej aplikacji internetowej opartej na module Express lub Restify. Kompleksowy zestaw strategii obsługuje uwierzytelnianie przy użyciu m.in. nazwy użytkownika i hasła lub kont w serwisach Facebook i Twitter. Opracowaliśmy strategię dla usługi Azure Active Directory (Azure AD). Polega ona na zainstalowaniu tego modułu, a następnie dodaniu wtyczki `passport-azure-ad` usługi Azure AD.

Aby pracować z tą próbką, należy:

1. Zarejestrować aplikację w usłudze Azure AD.
2. Skonfigurować aplikację do korzystania z wtyczki `passport-azure-ad` oprogramowania Passport.
3. Skonfigurować aplikację klienta do wywołania interfejsu API sieci Web listy zadań do wykonania.

## <a name="get-an-azure-ad-b2c-directory"></a>Tworzenie katalogu usługi Azure AD B2C
Przed rozpoczęciem korzystania z usługi Azure AD B2C należy utworzyć katalog lub dzierżawę.  Katalog jest kontenerem dla wszystkich użytkowników, aplikacji, grup i innych elementów.  Jeśli jeszcze go nie masz, [utwórz katalog usługi B2C](tutorial-create-tenant.md), zanim przejdziesz dalej.

## <a name="create-an-application"></a>Tworzenie aplikacji
Następnie należy utworzyć aplikację w katalogu B2C, która dostarcza do usługi Azure AD pewne informacje, które są potrzebne w celu bezpiecznego komunikowania się z aplikacją. W takim przypadku zarówno aplikacja klienta, jak i interfejs API sieci Web, są reprezentowane przez jeden **Identyfikator aplikacji**, ponieważ stanowią jedną aplikację logiczną. Aby utworzyć aplikację, postępuj zgodnie z [tymi instrukcjami](active-directory-b2c-app-registration.md). Należy pamiętać o wykonaniu następujących czynności:

* Dołącz do aplikacji **aplikację internetową/internetowy interfejs API**.
* Wprowadź `http://localhost/TodoListService` w polu **Adres URL odpowiedzi**. Jest to domyślny adres URL dla tej próbki kodu.
* Utwórz **klucz tajny aplikacji** i skopiuj go. Te dane będą potrzebne później. Pamiętaj, że aby go użyć, jego wartość musi być [ujęta w kodzie XML w znaki ucieczki](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape).
* Skopiuj **Identyfikator aplikacji** przypisany do aplikacji. Te dane będą potrzebne później.

## <a name="create-your-policies"></a>Tworzenie zasad
W usłudze Azure AD B2C każde działanie użytkownika jest definiowane przy użyciu [zasad](active-directory-b2c-reference-policies.md). Ta aplikacja zawiera dwa elementy dotyczące tożsamości: Zarejestruj się i zaloguj się. Musisz utworzyć jeden zbiór każdego typu zasad.  Po utworzeniu zasad należy koniecznie:

* Wybrać wartość **Nazwa wyświetlana** i inne atrybuty rejestracji w ramach zasad rejestracji.
* Wybrać oświadczenia aplikacji **Nazwa wyświetlana** oraz **Identyfikator obiektu** we wszystkich zasadach.  Można również wybrać inne oświadczenia.
* Skopiować wartość **Nazwa** poszczególnych zasad po ich utworzeniu. Powinny zawierać prefiks `b2c_1_`.  Te nazwy zasad będą potrzebne później.

Po utworzeniu zasad będzie można rozpocząć tworzenie aplikacji.

## <a name="download-the-code"></a>Pobieranie kodu
Kod używany w tym samouczku [jest przechowywany w serwisie GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS). Aby samodzielnie tworzyć przykładowy kod w trakcie pracy, możesz [pobrać plik ZIP ze szkieletem projektu](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/skeleton.zip). Można również sklonować szkielet:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS.git
```

Ukończona aplikacja jest również [dostępna jako plik ZIP](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/complete.zip) lub w gałęzi `complete` tego samego repozytorium.

## <a name="download-nodejs-for-your-platform"></a>Pobieranie środowiska Node.js dla platformy
Aby pomyślnie korzystać z tej próbki, potrzebna jest działająca instalacja środowiska Node.js.

Zainstaluj środowisko Node.js z witryny [nodejs.org](https://nodejs.org).

## <a name="install-mongodb-for-your-platform"></a>Instalowanie bazy danych MongoDB dla platformy
Aby pomyślnie korzystać z tej próbki, potrzebna jest działająca instalacja bazy danych MongoDB. Baza danych MongoDB zostanie użyta w celu uzyskania trwałego interfejsu API REST w wystąpieniach serwera.

Zainstaluj bazę danych MongoDB z witryny [mongodb.org](https://www.mongodb.org).

> [!NOTE]
> W tym przewodniku założono, że używasz domyślnej instalacji i domyślnych punktów końcowych serwera dla bazy danych MongoDB, które aktualnie mają wartość `mongodb://localhost`.
>
>

## <a name="install-the-restify-modules-in-your-web-api"></a>Instalowanie modułów Restify w interfejsie API sieci Web
Moduł Restify służy do tworzenia interfejsu API REST. Restify jest minimalną i elastyczną strukturą aplikacji w środowisku Node.js, która pochodzi z modułu Express. Oferuje rozbudowany zestaw funkcji do tworzenia interfejsów API REST na bazie usługi Connect.

### <a name="install-restify"></a>Instalacja modułu Restify
W wierszu polecenia zmień katalog na `azuread`. Jeśli katalog `azuread` nie istnieje, utwórz go.

`cd azuread` lub `mkdir azuread;`

Wprowadź następujące polecenie:

`npm install restify`

To polecenie powoduje zainstalowanie modułu Restify.

#### <a name="did-you-get-an-error"></a>Czy został wyświetlony komunikat o błędzie?
W niektórych systemach operacyjnych użycie ciągu `npm` może spowodować wystąpienie błędu `Error: EPERM, chmod '/usr/local/bin/..'` oraz pojawienie się żądania uruchomienia konta z uprawnieniami administratora. W takim przypadku należy użyć polecenia `sudo`, aby uruchomić polecenie `npm` na wyższym poziomie uprawnień.

#### <a name="did-you-get-a-dtrace-error"></a>Czy został wyświetlony błąd narzędzia DTrace?
Po zainstalowaniu modułu Restify może zostać wyświetlony następujący tekst:

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: 2
gyp ERR! stack     at ChildProcess.onExit (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:267:23)
gyp ERR! stack     at ChildProcess.EventEmitter.emit (events.js:98:17)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (child_process.js:789:12)
gyp ERR! System Darwin 13.1.0
gyp ERR! command "node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /Volumes/Development HD/azuread/node_modules/restify/node_modules/dtrace-provider
gyp ERR! node -v v0.10.11
gyp ERR! node-gyp -v v0.10.0
gyp ERR! not ok
npm WARN optional dep failed, continuing dtrace-provider@0.2.8
```

Moduł Restify zapewnia zaawansowany mechanizm śledzenia wywołań REST przy użyciu narzędzia DTrace. Jednak w wielu systemach operacyjnych narzędzie DTrace jest niedostępne. Można te błędy bezpiecznie zignorować.

Dane wyjściowe polecenia powinny wyglądać podobnie jak poniżej:

    restify@2.6.1 node_modules/restify
    ├── assert-plus@0.1.4
    ├── once@1.3.0
    ├── deep-equal@0.0.0
    ├── escape-regexp-component@1.0.2
    ├── qs@0.6.5
    ├── tunnel-agent@0.3.0
    ├── keep-alive-agent@0.0.1
    ├── lru-cache@2.3.1
    ├── node-uuid@1.4.0
    ├── negotiator@0.3.0
    ├── mime@1.2.11
    ├── semver@2.2.1
    ├── spdy@1.14.12
    ├── backoff@2.3.0
    ├── formidable@1.0.14
    ├── verror@1.3.6 (extsprintf@1.0.2)
    ├── csv@0.3.6
    ├── http-signature@0.10.0 (assert-plus@0.1.2, asn1@0.1.11, ctype@0.5.2)
    └── bunyan@0.22.0 (mv@0.0.5)

## <a name="install-passport-in-your-web-api"></a>Instalacja oprogramowania Passport w interfejsie API sieci Web
W wierszu polecenia zmień katalog na `azuread`, jeśli nie jest jeszcze określony.

Zainstaluj oprogramowanie Passport, używając następującego polecenia:

`npm install passport`

Dane wyjściowe polecenia powinny wyglądać podobnie jak poniższy tekst:

    passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3

## <a name="add-passport-azuread-to-your-web-api"></a>Dodawanie biblioteki passport-azuread do interfejsu API interfejsu sieci Web
Dodaj strategię OAuth przy użyciu biblioteki `passport-azuread` — zestawu strategii łączących usługę Azure AD z oprogramowaniem Passport. Użyj tej strategii w odniesieniu do tokenów elementów nośnych w próbce interfejsu API REST.

> [!NOTE]
> Chociaż protokół OAuth2 zapewnia platformę umożliwiającą wystawienie dowolnego znanego typu tokenu, tylko niektóre typy tokenów weszły do powszechnego użycia. Tokeny zabezpieczające punkty końcowe są tokenami elementów nośnych. Jest to najczęściej wystawiany typ tokenu protokołu OAuth2. W wielu wdrożeniach zakłada się, że tokeny elementów nośnych są jedynym typem wystawianych tokenów.
>
>

W wierszu polecenia zmień katalog na `azuread`, jeśli nie jest jeszcze określony.

Zainstaluj moduł `passport-azure-ad` oprogramowania Passport, używając następującego polecenia:

`npm install passport-azure-ad`

Dane wyjściowe polecenia powinny wyglądać podobnie jak poniższy tekst:

``
passport-azure-ad@1.0.0 node_modules/passport-azure-ad
├── xtend@4.0.0
├── xmldom@0.1.19
├── passport-http-bearer@1.0.1 (passport-strategy@1.0.0)
├── underscore@1.8.3
├── async@1.3.0
├── jsonwebtoken@5.0.2
├── xml-crypto@0.5.27 (xpath.js@1.0.6)
├── ursa@0.8.5 (bindings@1.2.1, nan@1.8.4)
├── jws@3.0.0 (jwa@1.0.1, base64url@1.0.4)
├── request@2.58.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, tunnel-agent@0.4.1, oauth-sign@0.8.0, isstream@0.1.2, extend@2.0.1, json-stringify-safe@5.0.1, node-uuid@1.4.3, qs@3.1.0, combined-stream@1.0.5, mime-types@2.0.14, form-data@1.0.0-rc1, http-signature@0.11.0, bl@0.9.4, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
└── xml2js@0.4.9 (sax@0.6.1, xmlbuilder@2.6.4)
``

## <a name="add-mongodb-modules-to-your-web-api"></a>Dodawanie modułów MongoDB do interfejsu API sieci Web
W tej próbce moduł MongoDB będzie używany jako magazyn danych. W tym celu zainstaluj oprogramowanie Mongoose — powszechnie używaną wtyczkę do zarządzania modelami i schematami.

* `npm install mongoose`

## <a name="install-additional-modules"></a>Instalowanie dodatkowych modułów
Następnie zainstaluj pozostałe wymagane moduły.

W wierszu polecenia zmień katalog na `azuread`, jeśli nie jest jeszcze określony:

`cd azuread`

Odpowiednie moduły zainstaluj w katalogu `node_modules`:

* `npm install assert-plus`
* `npm install ejs`
* `npm install ejs-locals`
* `npm install express`
* `npm install bunyan`

## <a name="create-a-serverjs-file-with-your-dependencies"></a>Tworzenie pliku server.js z własnymi zależnościami
Plik `server.js` zapewnia obsługę większości funkcji serwera interfejsu API sieci Web.

W wierszu polecenia zmień katalog na `azuread`, jeśli nie jest jeszcze określony:

`cd azuread`

Utwórz plik `server.js` w edytorze. Dodaj następujące informacje:

```Javascript
'use strict';
/**
* Module dependencies.
*/
var fs = require('fs');
var path = require('path');
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').BearerStrategy;
```

Zapisz plik. Powrócisz do niego później.

## <a name="create-a-configjs-file-to-store-your-azure-ad-settings"></a>Tworzenie pliku config.js do przechowywania ustawień usługi Azure AD
Ten plik kodu przekazuje parametry konfiguracji z portalu usługi Azure AD do pliku `Passport.js`. Te wartości konfiguracji zostały utworzone po dodaniu interfejsu API sieci Web do portalu w pierwszej części przewodnika. Wyjaśnijmy, co należy umieścić w wartościach tych parametrów, po skopiowaniu kodu.

W wierszu polecenia zmień katalog na `azuread`, jeśli nie jest jeszcze określony:

`cd azuread`

Utwórz plik `config.js` w edytorze. Dodaj następujące informacje:

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
clientID: <your client ID for this Web API you created in the portal>
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
audience: '<your audience URI>', // the Client ID of the application that is calling your API, usually a web API or native client
identityMetadata: 'https://login.microsoftonline.com/<tenant name>/.well-known/openid-configuration', // Make sure you add the B2C tenant name in the <tenant name> area
tenantName:'<tenant name>',
policyName:'b2c_1_<sign in policy name>' // This is the policy you'll want to validate against in B2C. Usually this is your Sign-in policy (as users sign in to this API)
passReqToCallback: false // This is a node.js construct that lets you pass the req all the way back to any upstream caller. We turn this off as there is no upstream caller.
};

```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### <a name="required-values"></a>Wymagane wartości
`clientID`: Identyfikator klienta aplikacji interfejsu API sieci Web.

`IdentityMetadata`: Jest to miejsce `passport-azure-ad` szuka danych konfiguracji dla dostawcy tożsamości. Wyszukuje również klucze do weryfikacji tokenów sieci Web JSON.

`audience`: Jednolity identyfikator zasobów (URI) z portalu, który identyfikuje aplikację wywołującą.

`tenantName`: Nazwa dzierżawy (na przykład **contoso.onmicrosoft.com**).

`policyName`: Zasady, które mają być używane do weryfikacji tokenów przychodzących na serwerze. Powinny to być te same zasady, które są używane do logowania w aplikacji klienta.

> [!NOTE]
> Na tę chwilę należy używać tych samych zasad w konfiguracji klienta i serwera. Jeśli zasady zostały już utworzone w ramach tego przewodnika, nie trzeba tego robić ponownie. Ponieważ zadania w tym przewodniku zostały wykonane, nie ma potrzeby konfigurowania nowych zasad w ramach przewodników klienta w witrynie.
>
>

## <a name="add-configuration-to-your-serverjs-file"></a>Dodawanie konfiguracji do pliku server.js
Aby odczytać wartości z utworzonego pliku `config.js`, dodaj plik `.config` jako wymagany zasób w aplikacji, a następnie ustaw zmienne globalne na zmienne z dokumentu `config.js`.

W wierszu polecenia zmień katalog na `azuread`, jeśli nie jest jeszcze określony:

`cd azuread`

Otwórz plik `server.js` w edytorze. Dodaj następujące informacje:

```Javascript
var config = require('./config');
```
Dodaj do pliku `server.js` nową sekcję zawierającą następujący kod:

```Javascript
// We pass these options in to the ODICBearerStrategy.

var options = {
    // The URL of the metadata document for your app. We put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    tenantName: config.creds.tenantName,
    policyName: config.creds.policyName,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience,
    passReqToCallback: config.creds.passReqToCallback

};
```

Następnie dodajmy kilka symboli zastępczych dla użytkowników, których otrzymamy z naszej aplikacji wywołującej.

```Javascript
// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;
```

Utwórzmy także naszego rejestratora.

```Javascript
// Our logger
var log = bunyan.createLogger({
    name: 'Microsoft Azure Active Directory Sample'
});
```

## <a name="add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>Dodawanie informacji o modelu i schemacie modułu MongoDB przy użyciu wtyczki Mongoose
Wcześniejsze przygotowania są przydatne podczas łączenia tych trzech plików w usłudze interfejsu API REST.

Na potrzeby tego przewodnika do przechowywania zadań użyj bazy danych MongoDB, zgodnie z wcześniejszym opisem.

W pliku `config.js` została wywołana **lista zadań** bazy danych. Ta nazwa została również umieszczona na końcu adresu URL połączenia `mongoose_auth_local`. Nie trzeba tworzyć wcześniej tej bazy danych w module MongoDB. Jest ona tworzona przy pierwszym uruchomieniu aplikacji serwera.

Po przekazaniu do serwera informacji, która baza danych MongoDB ma być używana, należy napisać dodatkowy kod w celu utworzenia modelu i schematu dla zadań serwera.

### <a name="expand-the-model"></a>Rozszerzanie modelu
Ten model schematu jest prosty. Można go rozszerzyć w zależności od potrzeb.

`owner`: Kto jest przydzielony do zadania. Ten obiekt to **ciąg**.  

`Text`: Samo zadanie. Ten obiekt to **ciąg**.

`date`: Data nadchodzi data ukończenia zadania. Ten obiekt to **data i godzina**.

`completed`: Jeśli zadanie zostało ukończone. Ten obiekt to **dane logiczne**.

### <a name="create-the-schema-in-the-code"></a>Tworzenie schematu w kodzie
W wierszu polecenia zmień katalog na `azuread`, jeśli nie jest jeszcze określony:

`cd azuread`

Otwórz plik `server.js` w edytorze. Dodaj następujące informacje poniżej pozycji konfiguracji:

```Javascript
// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 3000; // Note we are hosting our API on port 3000
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;

// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');

// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
    owner: String,
    Text: String,
    completed: Boolean,
    date: Date
});

// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
Najpierw tworzony jest schemat, a następnie obiekt modelu używany do przechowywania danych w całym kodzie podczas definiowania **tras**.

## <a name="add-routes-for-your-rest-api-task-server"></a>Dodawanie tras do serwera zadań interfejsu API REST
Dysponując modelem bazy danych, możesz dodać trasy, które są używane na potrzeby serwera interfejsu API REST.

### <a name="about-routes-in-restify"></a>Informacje o trasach w module Restify
Trasy działają w module Restify w taki sam sposób, jak podczas korzystania ze stosu modułu Express. Trasy są definiowane za pomocą identyfikatora URI, który ma być wywoływany przez aplikacje klienta.

Oto typowy wzorzec trasy modułu Restify:

```Javascript
function createObject(req, res, next) {
// do work on Object
_object.name = req.params.object; // passed value is in req.params under object
///...
return next(); // keep the server going
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```

Moduły Restify i Express mogą zapewniać o wiele bardziej złożone funkcje, takie jak definiowanie typów aplikacji i wykonywanie złożonego routingu przez różne punkty końcowe. W tym samouczku używamy prostych tras.

#### <a name="add-default-routes-to-your-server"></a>Dodawanie tras domyślnych do serwera
Teraz dodaj podstawowe trasy operacji CRUD dla operacji **tworzenia** i **umieszczania na liście** dla naszego interfejsu API REST. Inne trasy znajdują się w gałęzi `complete` próbki.

W wierszu polecenia zmień katalog na `azuread`, jeśli nie jest jeszcze określony:

`cd azuread`

Otwórz plik `server.js` w edytorze. Poniżej pozycji bazy danych utworzonych powyżej dodaj poniższe informacje:

```Javascript
/**
 *
 * APIs for our REST Task server
 */

// Create a task

function createTask(req, res, next) {

    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it up and save it to Mongodb
    var _task = new Task();

    if (!req.params.Text) {
        req.log.warn({
            params: req.params
        }, 'createTodo: missing task');
        next(new MissingTaskError());
        return;
    }

    _task.owner = owner;
    _task.Text = req.params.Text;
    _task.date = new Date();

    _task.save(function(err) {
        if (err) {
            req.log.warn(err, 'createTask: unable to save');
            next(err);
        } else {
            res.send(201, _task);

        }
    });

    return next();

}
```

```Javascript
/// Simple returns the list of TODOs that were loaded.

function listTasks(req, res, next) {
    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    log.info("listTasks was called for: ", owner);

    Task.find({
        owner: owner
    }).limit(20).sort('date').exec(function(err, data) {

        if (err)
            return next(err);

        if (data.length > 0) {
            log.info(data);
        }

        if (!data.length) {
            log.warn(err, "There is no tasks in the database. Add one!");
        }

        if (!owner) {
            log.warn(err, "You did not pass an owner when listing tasks.");
        } else {

            res.json(data);

        }
    });

    return next();
}
```


#### <a name="add-error-handling-for-the-routes"></a>Dodawanie obsługi błędów dla tras
Dodaj obsługę błędów, aby można było przekazać informacje o napotkanych błędach z powrotem do klienta w zrozumiały dla niego sposób.

Dodaj następujący kod:

```Javascript
///--- Errors for communicating something interesting back to the client
function MissingTaskError() {
restify.RestError.call(this, {
statusCode: 409,
restCode: 'MissingTask',
message: '"task" is a required parameter',
constructorOpt: MissingTaskError
});
this.name = 'MissingTaskError';
}
util.inherits(MissingTaskError, restify.RestError);
function TaskExistsError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 409,
restCode: 'TaskExists',
message: owner + ' already exists',
constructorOpt: TaskExistsError
});
this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);
function TaskNotFoundError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 404,
restCode: 'TaskNotFound',
message: owner + ' was not found',
constructorOpt: TaskNotFoundError
});
this.name = 'TaskNotFoundError';
}
util.inherits(TaskNotFoundError, restify.RestError);
```


## <a name="create-your-server"></a>Tworzenie serwera
Baza danych została zdefiniowana, a trasy — określone. Ostatnim zadaniem, które należy wykonać, jest dodanie wystąpienia serwera, które zarządza wywołaniami.

Moduły Restify i Express oferują możliwość głębokiego dostosowania do serwera interfejsu API REST, ale w tym przypadku stosujemy najbardziej podstawową konfigurację.

```Javascript

/**
 * Our Server
 */


var server = restify.createServer({
    name: "Microsoft Azure Active Directory TODO Server",
    version: "2.0.1"
});

// Ensure we don't drop data on uploads
server.pre(restify.pre.pause());

// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());

// Handles annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());

// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());

// Allow 5 requests/second by IP, and burst to 10
server.use(restify.throttle({
    burst: 10,
    rate: 5,
    ip: true,
}));

// Use the common stuff you probably want
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
    mapParams: true
})); // Allows for JSON mapping to REST
server.use(restify.authorizationParser()); // Looks for authorization headers

// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support


```
## <a name="add-the-routes-to-the-server-without-authentication"></a>Dodawanie tras do serwera (bez uwierzytelniania)
```Javascript
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.head('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.post('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.post('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.del('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeAll, function respond(req, res, next) {
    res.send(204);
    next();
});


// Register a default '/' handler

server.get('/', function root(req, res, next) {
    var routes = [
        'GET     /',
        'POST    /api/tasks/:owner/:task',
        'POST    /api/tasks (for JSON body)',
        'GET     /api/tasks',
        'PUT     /api/tasks/:owner',
        'GET     /api/tasks/:owner',
        'DELETE  /api/tasks/:owner/:task'
    ];
    res.send(200, routes);
    next();
});
```

```Javascript

server.listen(serverPort, function() {

    var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
    consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
    consoleMessage += '\n %s server is listening at %s';
    consoleMessage += '\n Open your browser to %s/api/tasks\n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
    consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';

    //log.info(consoleMessage, server.name, server.url, server.url, server.url);

});

```

## <a name="add-authentication-to-your-rest-api-server"></a>Dodawanie uwierzytelniania do serwera interfejsu API REST
Działający serwer interfejsu API REST można wykorzystać do celów usługi Azure AD.

W wierszu polecenia zmień katalog na `azuread`, jeśli nie jest jeszcze określony:

`cd azuread`

### <a name="use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>Korzystanie ze strategii OIDCBearerStrategy dołączonej do modułu passport-azure-ad
> [!TIP]
> Podczas pisania interfejsów API należy zawsze połączyć dane z unikatowym elementem tokenu, pod który użytkownik nie może się podszyć. Jeśli serwer przechowuje elementy listy zadań do wykonania, odbywa się to w oparciu o **identyfikator obiektu** użytkownika w tokenie (wywoływanym za pośrednictwem token.oid), który znajduje się w polu „właściciela”. Ta wartość zapewnia, że tylko ten użytkownik może uzyskać dostęp do własnych zadań do wykonania. W interfejsie API nie ma ekspozycji „właściciela” umożliwiającej użytkownikowi zewnętrznemu zażądanie zadań do wykonania innych osób, nawet po dokonaniu uwierzytelnienia.
>
>

Następnie użyj strategii elementu nośnego dołączonej do modułu `passport-azure-ad`.

```Javascript
var findById = function(id, fn) {
    for (var i = 0, len = users.length; i < len; i++) {
        var user = users[i];
        if (user.oid === id) {
            log.info('Found user: ', user);
            return fn(null, user);
        }
    }
    return fn(null, null);
};


var oidcStrategy = new OIDCBearerStrategy(options,
    function(token, done) {
        log.info('verifying the user');
        log.info(token, 'was the token retrieved');
        findById(token.sub, function(err, user) {
            if (err) {
                return done(err);
            }
            if (!user) {
                // "Auto-registration"
                log.info('User was added automatically as they were new. Their sub is: ', token.oid);
                users.push(token);
                owner = token.oid;
                return done(null, token);
            }
            owner = token.sub;
            return done(null, user, token);
        });
    }
);

passport.use(oidcStrategy);
```

Oprogramowanie Passport używa tego samego wzorca dla wszystkich swoich strategii. Zawiera on polecenie `function()` z parametrami `token` i `done`. Strategia zostanie zwrócona po wykonaniu całego zadania. Następnie należy przechować dane użytkownika i zapisać token, aby nie trzeba było żądać go ponownie.

> [!IMPORTANT]
> Powyższy kod obejmuje wszystkich użytkowników, którzy są uwierzytelniani na serwerze. Ten proces jest nazywany autorejestracją. W przypadku serwerów produkcyjnych nie zezwalaj żadnemu użytkownikowi na dostęp do interfejsu API, jeśli nie przejdzie on najpierw przez proces rejestracji. Ten proces to zazwyczaj wzorzec występujący w aplikacjach komercyjnych, który zezwala na rejestrację przy użyciu konta w serwisie Facebook, po czym wyświetlony zostaje monit o podanie dodatkowych informacji. Gdyby ten program nie był programem wiersza polecenia, moglibyśmy wyodrębnić wiadomość e-mail z obiektu tokena, który został zwrócony, a następnie poprosić użytkowników o podanie dodatkowych informacji. Ze względu na to, że to jest próbka, dodamy ich do bazy danych w pamięci.
>
>

## <a name="run-your-server-application-to-verify-that-it-rejects-you"></a>Uruchamianie aplikacji serwera w celu sprawdzenia, czy użytkownik jest odrzucany
Możesz użyć polecenia `curl`, aby sprawdzić, czy ochrona punktów końcowych przy użyciu protokołu OAuth2 jest aktualnie włączona. Zwrócone nagłówki powinny wystarczyć do ustalenia, czy działanie zostało wykonane prawidłowo.

Upewnij się, że wystąpienie bazy danych MongoDB zostało uruchomione:

    $sudo mongodb

Przejdź do katalogu i uruchom serwer:

    $ cd azuread
    $ node server.js

W nowym oknie terminala uruchom polecenie `curl`

Wypróbuj podstawową procedurę testową POST:

`$ curl -isS -X POST http://127.0.0.1:3000/api/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

Oczekiwaną odpowiedzią jest błąd 401. Oznacza on, że warstwa oprogramowania Passport podejmuje próbę przekierowania do punktu końcowego autoryzacji.

## <a name="you-now-have-a-rest-api-service-that-uses-oauth2"></a>Efektem jest usługa interfejsu API REST korzystająca z protokołu OAuth2
Udało Ci się wdrożyć interfejs API REST przy użyciu modułu Restify i strategii OAuth! Masz teraz kod wystarczający do tego, aby kontynuować opracowywanie usługi i tworzyć ją na bazie tego przykładu. W przypadku tego serwera nie można wykonać żadnych dalszych czynności bez użycia klienta zgodnego z protokołem OAuth2. Na potrzeby następnego kroku użyj dodatkowego przewodnika, na przykład naszego przewodnika [Podłączanie do interfejsu API sieci Web przy użyciu systemu iOS z usługą B2C](active-directory-b2c-devquickstarts-ios.md).

## <a name="next-steps"></a>Kolejne kroki
Możesz teraz przejść do bardziej zaawansowanych tematów, takich jak:

[Podłączanie do interfejsu API sieci Web przy użyciu systemu iOS z usługą B2C](active-directory-b2c-devquickstarts-ios.md)
