---
title: Zabezpiecz interfejs API web v2.0 usługi Azure Active Directory przy użyciu środowiska Node.js | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia sieci web Node.js interfejsu API, który akceptuje tokeny zarówno z osobistego konta Microsoft, jak i w pracy lub szkołą kont.
services: active-directory
documentationcenter: nodejs
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 0b572fc1-2aaf-4cb6-82de-63010fb1941d
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 05/13/2017
ms.author: celested
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 19ad25c7b08ff073097cacf3be359772ca0a327f
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34158374"
---
# <a name="secure-a-web-api-by-using-nodejs"></a>Zabezpieczanie interfejsu API sieci web przy użyciu środowiska Node.js
> [!NOTE]
> Nie wszystkie usługi Azure Active Directory scenariusze i funkcje działają z punktem końcowym v2.0. Aby ustalić, czy należy używać punktu końcowego v2.0 lub punkt końcowy w wersji 1.0, przeczytaj o [ograniczenia v2.0](active-directory-v2-limitations.md).
> 
> 

Jeśli używasz punktu końcowego v2.0 usługi Azure Active Directory (Azure AD), możesz użyć [OAuth 2.0](active-directory-v2-protocols.md) dostęp do tokenów do ochrony interfejsu API sieci web. Za pomocą protokołu OAuth 2.0 tokeny dostępu, użytkownicy, którzy mają zarówno osobistego konta Microsoft i pracy lub kont służbowych można bezpiecznego dostępu do interfejsu API sieci web.

*Passport* to uwierzytelniające oprogramowanie pośredniczące dla środowiska Node.js. Elastyczne i moduły, usługa Passport można dyskretnie usunięty w każdym ekspresowe lub restify aplikacji sieci web. W usłudze Passport kompleksowy zestaw strategii obsługuje uwierzytelnianie przy użyciu nazwy użytkownika i hasła, Facebook, Twitter lub innych opcji. Opracowaliśmy strategię dla usługi Azure AD. W tym artykule, firma Microsoft opisano, jak zainstalować moduł, a następnie dodaj usługi Azure AD `passport-azure-ad` wtyczki.

## <a name="download"></a>Do pobrania
Kod używany w tym samouczku jest przechowywany [ w serwisie GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs). Aby użyć tego samouczka, możesz [pobrać szkielet aplikacji w pliku .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/skeleton.zip), lub sklonować szkielet:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

Możesz również uzyskać ukończona aplikacja na końcu tego samouczka.

## <a name="1-register-an-app"></a>1: Rejestrowanie aplikacji
Utwórz nową aplikację na [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), lub wykonaj [te szczegółowe kroki](active-directory-v2-app-registration.md) zarejestrować aplikację. Upewnij się, że:

* Kopiuj **identyfikator aplikacji** przypisany do aplikacji. Będzie on potrzebny w tym samouczku.
* Dodaj **Mobile** platformy aplikacji.
* Kopiuj **identyfikator URI przekierowania** z portalu. Należy używać domyślnej wartości identyfikatora URI z `urn:ietf:wg:oauth:2.0:oob`.

## <a name="2-install-nodejs"></a>2: zainstaluj środowisko Node.js
Aby użyć przykładowego dla tego samouczka, należy najpierw [instalowania programu Node.js](http://nodejs.org).

## <a name="3-install-mongodb"></a>3: Zainstaluj bazę danych MongoDB
Aby pomyślnie korzystać z tej próbki, należy [zainstalować bazy danych MongoDB](http://www.mongodb.org). W tym przykładzie używasz bazy danych MongoDB ustawienia interfejsu API REST trwałego w wystąpieniach serwera.

> [!NOTE]
> W tym artykule przyjęto założenie, Użyj domyślnych punktów instalacji i serwera, końcowych bazy danych mongodb: mongodb://localhost.
> 
> 

## <a name="4-install-the-restify-modules-in-your-web-api"></a>4: instalowanie modułów restify w interfejsie API sieci web
Używamy Resitfy do kompilacji w interfejsie API REST. Restify jest minimalną i elastyczną Node.js aplikacji platforma, która jest pochodną Express. Moduły restify oferuje rozbudowany zestaw funkcji, które służą do tworzenia interfejsów API REST na bazie usługi Connect.

### <a name="install-restify"></a>Instalacja modułu restify
1.  W wierszu polecenia Zmień katalog na **azuread**:

    `cd azuread`

    Jeśli **azuread** katalog nie istnieje, utwórz go:

    `mkdir azuread`

2.  Instalacja modułu restify:

    `npm install restify`

    Dane wyjściowe tego polecenia powinien wyglądać następująco:

    ```
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
    └── bunyan@0.22.0(mv@0.0.5)
    ```

#### <a name="did-you-get-an-error"></a>Czy został wyświetlony komunikat o błędzie?
W niektórych systemach operacyjnych, korzystając z `npm` polecenia, można napotkać tego komunikatu: `Error: EPERM, chmod '/usr/local/bin/..'`. Błąd następuje żądanie spróbuj konto uruchamiania jako administrator. W takim przypadku należy użyć polecenia `sudo` do uruchomienia `npm` na wyższym poziomie uprawnień.

#### <a name="did-you-get-an-error-related-to-dtrace"></a>Czy został wyświetlony komunikat o błędzie dotyczący do narzędzia DTrace?
Po zainstalowaniu restify, można napotkać ten komunikat:

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: two
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

Moduły restify ma zaawansowany mechanizm śledzenia wywołań REST przy użyciu narzędzia DTrace. Jednak narzędzia DTrace nie jest dostępne w wielu systemach operacyjnych. Można bezpiecznie zignorować ten komunikat o błędzie.


## <a name="5-install-passportjs-in-your-web-api"></a>5: Instalacja Passport.js w interfejsie API sieci web
1.  W wierszu polecenia Zmień katalog na **azuread**.

2.  Zainstaluj Passport.js:

    `npm install passport`

    Dane wyjściowe polecenia powinny wyglądać następująco:

    ```
     passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3
    ```

## <a name="6-add-passport-azure-ad-to-your-web-api"></a>6: Dodaj passport-azure-ad do interfejsu API sieci web
Następnie dodaj strategię OAuth przy użyciu biblioteki passport-azuread. `passport-azuread` jest zestawu strategii łączących usługę Azure AD z usługą Passport. Używamy tej strategii do tokenów elementów nośnych w próbce tego interfejsu API REST.

> [!NOTE]
> Chociaż framework, w którym mogą być wystawiane dowolnego znanego typu tokenu OAuth 2.0, niektóre typy tokenów są często używane. Tokenów elementu nośnego są często używane do ochrony punktów końcowych. Tokeny elementów nośnych są najczęściej wystawiany typ tokenu OAuth 2.0. Wiele implementacji protokołu OAuth 2.0 założono, że tokeny elementów nośnych są jedynym typem wystawianych tokenów.
> 
> 

1.  W wierszu polecenia Zmień katalog na **azuread**.

    `cd azuread`

2.  Zainstaluj Passport.js `passport-azure-ad` modułu:

    `npm install passport-azure-ad`

    Dane wyjściowe polecenia powinny wyglądać następująco:

    ```
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
    ```

## <a name="7-add-mongodb-modules-to-your-web-api"></a>7: Dodawanie modułów MongoDB do interfejsu API sieci web
W tym przykładzie używamy bazy danych MongoDB naszych przechowywania danych. 

1.  Instalowanie wtyczki Mongoose powszechnie używaną wtyczkę do zarządzania zabezpieczeniami: 

    `npm install mongoose`

2.  Zainstaluj sterownik bazy danych dla bazy danych MongoDB, który jest również noszący nazwę MongoDB:

    `npm install mongodb`

## <a name="8-install-additional-modules"></a>8: Instalowanie dodatkowych modułów
Zainstaluj pozostałe wymagane moduły.

1.  W wierszu polecenia Zmień katalog na **azuread**:

    `cd azuread`

2.  Wprowadź następujące polecenia. Polecenia należy zainstalować w katalogu node_modules następujących modułów:

    *   `npm install crypto`
    *   `npm install assert-plus`
    *   `npm install posix-getopt`
    *   `npm install util`
    *   `npm install path`
    *   `npm install connect`
    *   `npm install xml-crypto`
    *   `npm install xml2js`
    *   `npm install xmldom`
    *   `npm install async`
    *   `npm install request`
    *   `npm install underscore`
    *   `npm install grunt-contrib-jshint@0.1.1`
    *   `npm install grunt-contrib-nodeunit@0.1.2`
    *   `npm install grunt-contrib-watch@0.2.0`
    *   `npm install grunt@0.4.1`
    *   `npm install xtend@2.0.3`
    *   `npm install bunyan`
    *   `npm update`

## <a name="9-create-a-serverjs-file-for-your-dependencies"></a>9: Tworzenie pliku Server.js dla zależności
Plik Server.js zawiera większość funkcjonalności serwera interfejsu API sieci web. Większość kod należy dodać do tego pliku. Do celów produkcyjnych należy refaktoryzować funkcje na mniejsze pliki, takich jak odrębne trasy i kontrolery. W tym artykule w tym celu możemy użyć Server.js.

1.  W wierszu polecenia Zmień katalog na **azuread**:

    `cd azuread`

2.  Za pomocą dowolnego edytora, Utwórz plik Server.js. Dodaj następujące informacje do pliku:

    ```Javascript
    'use strict';
    /**
    * Module dependencies.
    */
    var util = require('util');
    var assert = require('assert-plus');
    var mongoose = require('mongoose/');
    var bunyan = require('bunyan');
    var restify = require('restify');
    var config = require('./config');
    var passport = require('passport');
    var OIDCBearerStrategy = require('passport-azure-ad').OIDCStrategy;
    ```

3.  Zapisz plik. Nastąpi powrót do niego później.

## <a name="10-create-a-config-file-to-store-your-azure-ad-settings"></a>10: Utwórz plik konfiguracji do przechowywania ustawień usługi Azure AD
Ten plik kodu przekazuje parametry konfiguracji w portalu usługi Azure AD do Passport.js. Te wartości konfiguracji są tworzone po dodaniu interfejsu API sieci web do portalu na początku tego artykułu. Po skopiowaniu kodu wyjaśniamy co należy umieścić w wartościach tych parametrów.

1.  W wierszu polecenia Zmień katalog na **azuread**:

    `cd azuread`

2.  W edytorze Tworzenie pliku Config.js. Dodaj następujące informacje:

    ```Javascript
    // Don't commit this file to your public repos. This config is for first-run.
    exports.creds = {
    mongoose_auth_local: 'mongodb://localhost/tasklist', // Your Mongo auth URI goes here.
    issuer: 'https://sts.windows.net/**<your application id>**/',
    audience: '<your redirect URI>',
    identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration' // For Microsoft, you should never need to change this.
    };

    ```



### <a name="required-values"></a>Wymagane wartości

*   **IdentityMetadata**: jest to, gdy `passport-azure-ad` sprawdza dane konfiguracyjne dla dostawcy tożsamości (IDP) i klucze do sprawdzania poprawności tokenów sieci Web JSON (Jwt). Jeśli używasz usługi Azure AD, prawdopodobnie nie chcesz zmienić.

*   **grupy odbiorców**: przekierowania URI z portalu.

> [!NOTE]
> Przywróć klucze w częstych odstępach czasu. Upewnij się, że należy zawsze ściąganie danych z adresu URL "openid_keys" i że aplikacja może uzyskać dostęp do Internetu.
> 
> 

## <a name="11-add-the-configuration-to-your-serverjs-file"></a>11: Dodaj konfigurację do pliku Server.js
Aplikacja wymaga można odczytać wartości z pliku konfiguracji, który został utworzony. Dodaj plik .config jako wymagany zasób w aplikacji. Ustaw zmienne globalne do tych, które znajdują się w Config.js.

1.  W wierszu polecenia Zmień katalog na **azuread**:

    `cd azuread`

2.  W edytorze Otwórz Server.js. Dodaj następujące informacje:

    ```Javascript
    var config = require('./config');
    ```

3.  Dodaj nową sekcję do Server.js:

    ```Javascript
    // Pass these options in to the ODICBearerStrategy.
    var options = {
    // The URL of the metadata document for your app. Put the keys for token validation from the URL found in the jwks_uri tag in the metadata.
    identityMetadata: config.creds.identityMetadata,
    issuer: config.creds.issuer,
    audience: config.creds.audience
    };
    // Array to hold signed-in users and the current signed-in user (owner).
    var users = [];
    var owner = null;
    // Your logger
    var log = bunyan.createLogger({
    name: 'Microsoft Azure Active Directory Sample'
    });
    ```

## <a name="12-add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>12: Dodaj informacje o modelu i schematu bazy danych MongoDB przy użyciu wtyczki Mongoose
Następnie należy połączyć z tych trzech plików w usłudze interfejsu API REST.

W tym artykule używamy bazy danych MongoDB do przechowywania nasze zadania. Omówiono w *krok 4*.

W pliku Config.js został utworzony w kroku 11, nosi nazwę bazy danych *tasklist*. Która jest umieszczona na końcu mongoose_auth_local adres URL połączenia. Nie trzeba tworzyć wcześniej tej bazy danych w module MongoDB. Baza danych została utworzona przy pierwszym uruchomieniu aplikacji serwera (przy założeniu, że bazy danych już nie istnieje).

Serwer został informację której bazy danych MongoDB bazy danych. Następnie należy napisać dodatkowy kod w celu utworzenia modelu i schematu dla zadań serwera.

### <a name="the-model"></a>Model
Model schematu jest bardzo proste. Aby można ją rozszerzyć. 

Model schematu ma następujące wartości:

*   **NAZWA**. Osoba przypisana do zadania. Jest to **ciąg** wartość.
*   **ZADANIE**. Nazwa zadania. Jest to **ciąg** wartość.
*   **DATA**. Data przypada zadania. Jest to **datetime** wartość.
*   **UKOŃCZONO**. Określa, czy zadanie zostało ukończone. Jest to **logiczna** wartość.

### <a name="create-the-schema-in-the-code"></a>Tworzenie schematu w kodzie
1.  W wierszu polecenia Zmień katalog na **azuread**:

    `cd azuread`

2.  W edytorze Otwórz Server.js. Poniżej pozycji konfiguracji należy dodać następujące informacje:

    ```Javascript
    // MongoDB setup.
    // Set up some configuration.
    var serverPort = process.env.PORT || 8080;
    var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
    // Connect to MongoDB.
    global.db = mongoose.connect(serverURI);
    var Schema = mongoose.Schema;
    log.info('MongoDB Schema loaded');
    ```

Ten kod łączy się z serwerem bazy danych MongoDB. Zwraca obiekt schematu.

#### <a name="using-the-schema-create-your-model-in-the-code"></a>Za pomocą schematu, tworzenie modelu w kodzie
Poniżej poprzednim kodzie Dodaj następujący kod:

```Javascript
// Create a basic schema to store your tasks and users.
var TaskSchema = new Schema({
owner: String,
task: String,
completed: Boolean,
date: Date
});
// Use the schema to register a model.
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```

Jak stwierdzić, z kodu, najpierw należy utworzyć schemat. Następnie można utworzyć obiektu modelu. Obiekt modelu używany do przechowywania danych w całym kodzie podczas definiowania Twojej **tras**.

## <a name="13-add-your-routes-for-your-task-rest-api-server"></a>13: Dodaj trasy dla serwera zadań interfejsu API REST
Teraz, gdy masz model bazy danych do pracy z dodać trasy, które będą używane dla serwera interfejsu API REST.

### <a name="about-routes-in-restify"></a>Informacje o trasach w restify
Praca restify trasy w taki sam sposób jak korzystania ze stosu Express. Trasy są definiowane za pomocą identyfikatora URI, który ma być wywoływany przez aplikacje klienta. Zazwyczaj należy zdefiniować trasy w oddzielnym pliku. W tym samouczku testujemy naszych trasy w Server.js. Do użytku produkcyjnego zaleca się współczynnika trasy do ich własnych pliku.

Typowy wzorzec trasy modułu restify jest:

```Javascript
function createObject(req, res, next) {
// Do work on object.
_object.name = req.params.object; // Passed value is in req.params under object.
///...
return next(); // Keep the server going.
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```


Jest to wzorzec na najbardziej podstawowym poziomie. Moduły restify (i Express) podaj znacznie bardziej złożone funkcje, takie jak możliwość definiowania typy aplikacji i złożonego routingu przez różne punkty końcowe.

#### <a name="add-default-routes-to-your-server"></a>Dodawanie tras domyślnych do serwera
Dodać podstawowe trasy CRUD: **utworzyć**, **pobrać**, **aktualizacji**, i **usunąć**.

1.  W wierszu polecenia Zmień katalog na **azuread**:

    `cd azuread`

2.  W edytorze Otwórz Server.js. Poniżej pozycji bazy danych, wprowadzone wcześniej, Dodaj następujące informacje:

    ```Javascript
    /**
    *
    * APIs for your REST task server
    */
    // Create a task.
    function createTask(req, res, next) {
    // Resitify currently has a bug that doesn't allow you to set default headers.
    // These headers comply with CORS, and allow you to use MongoDB Server as your response to any origin.
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");
    // Create a new task model, fill it, and save it to MongoDB.
    var _task = new Task();
    if (!req.params.task) {
    req.log.warn({
    params: p
    }, 'createTodo: missing task');
    next(new MissingTaskError());
    return;
    }
    _task.owner = owner;
    _task.task = req.params.task;
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
    // Delete a task by name.
    function removeTask(req, res, next) {
    Task.remove({
    task: req.params.task,
    owner: owner
    }, function(err) {
    if (err) {
    req.log.warn(err,
    'removeTask: unable to delete %s',
    req.params.task);
    next(err);
    } else {
    log.info('Deleted task:', req.params.task);
    res.send(204);
    next();
    }
    });
    }
    // Delete all tasks.
    function removeAll(req, res, next) {
    Task.remove();
    res.send(204);
    return next();
    }
    // Get a specific task based on name.
    function getTask(req, res, next) {
    log.info('getTask was called for: ', owner);
    Task.find({
    owner: owner
    }, function(err, data) {
    if (err) {
    req.log.warn(err, 'get: unable to read %s', owner);
    next(err);
    return;
    }
    res.json(data);
    });
    return next();
    }
    /// Returns the list of TODOs that were loaded.
    function listTasks(req, res, next) {
    // Resitify currently has a bug that doesn't allow you to set default headers.
    // These headers comply with CORS, and allow us to use MongoDB Server as our response to any origin.
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
    log.warn(err, "There are no tasks in the database. Add one!");
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

### <a name="add-error-handling-for-the-routes"></a>Dodawanie obsługi błędów dla tras
Dodaj obsługę błędów, może komunikować się do klienta o napotkany problem.

Dodaj następujący kod poniżej kodu, który został już zapisany:

```Javascript
///--- Errors for communicating something more information back to the client.
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


## <a name="14-create-your-server"></a>14: Tworzenie serwera
Ostatnim etapem jest można dodać wystąpienia serwera. Wystąpienie serwera zarządza wywołania.

Moduły restify (i Express) ma możliwość głębokiego dostosowania można używać z serwera interfejsu API REST. W tym samouczku używamy najbardziej podstawowa konfiguracja.

```Javascript
/**
* Your server
*/
var server = restify.createServer({
name: "Microsoft Azure Active Directory TODO Server",
version: "2.0.1"
});
// Ensure that you don't drop data on uploads.
server.pre(restify.pre.pause());
// Clean up imprecise paths like //todo//////1//.
server.pre(restify.pre.sanitizePath());
// Handle annoying user agents (curl).
server.pre(restify.pre.userAgentConnection());
// Set a per-request Bunyan logger (with requestid filled in).
server.use(restify.requestLogger());
// Allow 5 requests/second by IP address, and burst to 10.
server.use(restify.throttle({
burst: 10,
rate: 5,
ip: true,
}));
// Use common commands, such as:
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
mapParams: true
}));
```
## <a name="15-add-the-routes-without-authentication-for-now"></a>15: dodać trasy (bez uwierzytelniania jest obecnie)
```Javascript
/// Use CRUD to add the real handlers.
/**
/*
/* Each of these handlers is protected by your Open ID Connect Bearer strategy. Invoke 'oidc-bearer'
/* in the pasport.authenticate() method. Because REST is stateless, set 'session: false'. You 
/* don't need to maintain session state. You can experiment with removing API protection.
/* To do this, remove the passport.authenticate() method:
/*
/* server.get('/tasks', listTasks);
/*
**/
server.get('/tasks', listTasks);
server.get('/tasks', listTasks);
server.get('/tasks/:owner', getTask);
server.head('/tasks/:owner', getTask);
server.post('/tasks/:owner/:task', createTask);
server.post('/tasks', createTask);
server.del('/tasks/:owner/:task', removeTask);
server.del('/tasks/:owner', removeTask);
server.del('/tasks', removeTask);
server.del('/tasks', removeAll, function respond(req, res, next) {
res.send(204);
next();
});
// Register a default '/' handler
server.get('/', function root(req, res, next) {
var routes = [
'GET /',
'POST /tasks/:owner/:task',
'POST /tasks (for JSON body)',
'GET /tasks',
'PUT /tasks/:owner',
'GET /tasks/:owner',
'DELETE /tasks/:owner/:task'
];
res.send(200, routes);
next();
});
server.listen(serverPort, function() {
var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
consoleMessage += '\n %s server is listening at %s';
consoleMessage += '\n Open your browser to %s/tasks\n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';
});
```
## <a name="16-run-the-server"></a>16: uruchamianie serwera
Jest warto przetestować serwer przed dodaniem uwierzytelniania.

Najprostszym sposobem przetestować serwer jest przy użyciu programu curl w wierszu polecenia. Aby to zrobić, należy proste narzędzie, które służy do analizy danych wyjściowych w formacie JSON. 

1.  Zainstaluj narzędzie JSON, który używamy w poniższych przykładach:

    `$npm install -g jsontool`

    Narzędzie JSON zostanie zainstalowane globalnie.

2.  Upewnij się, że wystąpienie bazy danych MongoDB zostało uruchomione:

    `$sudo mongod`

3.  Zmień katalog na **azuread**, a następnie uruchom narzędzie curl:

    `$ cd azuread`
    `$ node server.js`

    `$ curl -isS http://127.0.0.1:8080 | json`

    ```Shell
    HTTP/1.1 2.0OK
    Connection: close
    Content-Type: application/json
    Content-Length: 171
    Date: Tue, 14 Jul 2015 05:43:38 GMT
    [
    "GET /",
    "POST /tasks/:owner/:task",
    "POST /tasks (for JSON body)",
    "GET /tasks",
    "PUT /tasks/:owner",
    "GET /tasks/:owner",
    "DELETE /tasks/:owner/:task"
    ]
    ```

4.  Aby dodać zadania:

    `$ curl -isS -X POST http://127.0.0.1:8888/tasks/brandon/Hello`

    Odpowiedź powinna wyglądać w następujący sposób:

    ```Shell
    HTTP/1.1 201 Created
    Connection: close
    Access-Control-Allow-Origin: *
    Access-Control-Allow-Headers: X-Requested-With
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 5
    Date: Tue, 04 Feb 2014 01:02:26 GMT
    Hello
    ```

5.  Listy zadań dla Brandon:

    `$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Jeśli wszystkie te polecenia są uruchamiane bez błędów, wszystko jest gotowe do dodania OAuth do serwera interfejsu API REST.

*Masz teraz serwerem interfejsu API REST z bazą danych MongoDB.*

## <a name="17-add-authentication-to-your-rest-api-server"></a>17: Dodawanie uwierzytelniania do serwera interfejsu API REST
Teraz, gdy masz uruchomiony interfejs API REST, skonfigurować go do używania go z usługą Azure AD.

W wierszu polecenia Zmień katalog na **azuread**:

`cd azuread`

### <a name="use-the-oidcbearerstrategy-thats-included-with-passport-azure-ad"></a>Użyj oidcbearerstrategy, który wchodzi w skład passport-azure-ad
Do tej pory zostały skompilowane typowy serwer REST TODO bez żadnej autoryzacji. Teraz Dodaj uwierzytelniania.

Najpierw należy wskazać chcesz korzystać z usługi Passport. To prawo należy umieścić po wcześniejszej konfiguracji serwera:

```Javascript
// Start using Passport.js.

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support
```

> [!TIP]
> Podczas pisania interfejsów API jest dobrym pomysłem jest zawsze połączyć dane z unikatowym z tokenu, który użytkownik nie może się podszyć. Gdy ten serwer przechowuje elementy przechowuje je na podstawie Identyfikatora subskrypcji użytkownika w tokenie (wywoływanym za pośrednictwem token.sub). Token.sub należy umieścić w polu "właściciela". Daje to pewność, że tylko ten użytkownik może uzyskać dostęp TODOs użytkownika. Inni użytkownicy mogą uzyskiwać dostęp do TODOs, które zostały wprowadzone. Brak ma ekspozycji w interfejsie API dla "właściciela". Użytkownikowi zewnętrznemu zażądanie TODOs innych użytkowników, nawet po dokonaniu uwierzytelnienia.
> 
> 

Następnie użyj strategii Otwórz elementu Identyfikatora połączenia nośnego dołączonej `passport-azure-ad`. Umieścić po wcześniejszym wkleić:

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users.
/*
/* Because of the Passport pattern, you need to manage users and info tokens
/* with a FindorCreate() method. The method must be provided by the implementor.
/* In the following code, you autoregister any user and implement a FindById().
/* It's a good idea to do something more advanced.
**/
var findById = function(id, fn) {
for (var i = 0, len = users.length; i < len; i++) {
var user = users[i];
if (user.sub === id) {
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
log.info('User was added automatically, because they were new. Their sub is: ', token.sub);
users.push(token);
owner = token.sub;
return done(null, token);
}
owner = token.sub;
return done(null, user, token);
});
}
);
passport.use(oidcStrategy);
```

Usługa Passport używa podobnego wzorca do wszystkich swoich strategii (Twitter, Facebook i tak dalej). Wszyscy twórcy kodu strategii jest zgodna z wzorcem. Przekaż strategii `function()` używającą tokenu i `done` jako parametry. Strategia jest zwracane, gdy istnieje swojej pracy. Przechowaj dane użytkownika i Zapisz token, więc nie trzeba było zażądać go ponownie.

> [!IMPORTANT]
> Poprzedni kod obejmuje każdy użytkownik, który może uwierzytelniać się z serwerem. Jest to nazywane rejestracji automatycznej. Na serwerze produkcyjnym nie mają mieć możliwość każda osoba, która bez konieczności ich przejść przez proces rejestracji, który można wybrać. Jest to zwykle wzorzec występujący w aplikacjach dla użytkowników indywidualnych. Aplikacja może pozwala zarejestrować się w serwisie Facebook, ale następnie prosi o podanie dodatkowych informacji. Jeśli nie korzystasz z wiersza polecenia programu w tym samouczku, można wyodrębnić wiadomości e-mail z obiektu tokena, który jest zwracany. Następnie może poprosić użytkownika o podanie dodatkowych informacji. Ponieważ jest to serwer testowy, możesz dodać użytkownika bezpośrednio do bazy danych w pamięci.
> 
> 

### <a name="protect-endpoints"></a>Ochrona punktów końcowych
Ochrona punktów końcowych, określając **passport.authenticate()** wywołania z protokołem, którego chcesz używać.

Dostęp można edytować w kodzie serwera dla bardziej zaawansowanych opcji:

```Javascript
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## <a name="18-run-your-server-application-again"></a>18: Uruchom ponownie aplikację serwera
Użyj ponownie curl, aby zobaczyć, jeśli masz OAuth 2.0 ochrony punktów końcowych przy użyciu. Wykonaj tę operację przed uruchomieniem żadnego z zestawów SDK klienta względem tego punktu końcowego. Zwrócone nagłówki powinny stwierdzić, czy uwierzytelnianie działa poprawnie.

1.  Upewnij się, czy działa isntance Twojej bazy danych MongoDB:

    `$sudo mongod`

2.  Zmień na **azuread** katalogu, a następnie użyj curl:

    `$ cd azuread`

    `$ node server.js`

3.  Wypróbuj podstawową procedurę testową POST:

    `$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

    ```Shell
    HTTP/1.1 401 Unauthorized
    Connection: close
    WWW-Authenticate: Bearer realm="Users"
    Date: Tue, 14 Jul 2015 05:45:03 GMT
    Transfer-Encoding: chunked
    ```

Uzyskanie odpowiedzi 401 wskazuje, że warstwa oprogramowania Passport podejmuje próbę przekierowania do punktu końcowego autoryzacji, który ma dokładnie.

*Masz teraz usługi interfejsu API REST, który korzysta z protokołu OAuth 2.0.*

Został usunięty w zakresie, w jakim można bez użycia klienta OAuth 2.0 zgodnego z tym serwerem. W tym należy przejrzeć dodatkowe samouczka.

## <a name="next-steps"></a>Kolejne kroki
Odwołania, ukończonych próbka (bez wartości konfiguracji) jest dostępna jako [plik zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/complete.zip). Użytkownik może ją także sklonować z serwisu GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

Teraz możesz przejść do bardziej zaawansowanych tematów. Możesz spróbować [zabezpieczenia aplikacji sieci web Node.js za pomocą punktu końcowego v2.0](active-directory-v2-devquickstarts-node-web.md).

Poniżej przedstawiono dodatkowe zasoby:

* [Przewodnik dewelopera v2.0 usługi Azure AD](active-directory-appmodel-v2-overview.md)
* [Tag "azure-active-directory" przepełnienie stosu](http://stackoverflow.com/questions/tagged/azure-active-directory)

### <a name="get-security-updates-for-our-products"></a>Pobierz aktualizacje zabezpieczeń naszych produktów
Firma Microsoft zachęca do Zarejestruj się, aby otrzymać powiadomienie, gdy występujących incydentach zabezpieczeń. Na [powiadomień o zabezpieczeniach technicznych Microsoft](https://technet.microsoft.com/security/dd252948) strony, należy subskrybować alerty klasyfikatory zabezpieczeń.

