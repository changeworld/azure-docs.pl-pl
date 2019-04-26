---
title: Zabezpieczanie internetowego interfejsu API za pomocą usługi Azure AD | Microsoft Docs
description: Dowiedz się, jak utworzyć internetowy interfejs API REST platformy Node.js, który integruje się z usługą Azure AD w celu uwierzytelniania.
services: active-directory
documentationcenter: nodejs
author: CelesteDG
manager: mtillman
ms.assetid: 7654ab4c-4489-4ea5-aba9-d7cdc256e42a
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f72cbd719cea585144be3757f0791a74bde452ab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60299196"
---
# <a name="quickstart-secure-a-web-api-with-azure-active-directory"></a>Szybki start: zabezpieczanie internetowego interfejsu API za pomocą usługi Azure Active Directory

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

W tym przewodniku Szybki start dowiesz się, jak zabezpieczyć punkt końcowy interfejsu API modułu [Restify](http://restify.com/) za pomocą usługi [Passport](http://passportjs.org/) przy użyciu modułu [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad) w celu obsługi komunikacji z usługą Azure Active Directory (Azure AD).

Zakres tego przewodnika Szybki start obejmuje kwestie dotyczące zabezpieczania punktów końcowych interfejsu API. Kwestie dotyczące logowania się i przechowywania tokenów uwierzytelniania nie są opisywane w tym module i odpowiada za nie aplikacji kliencka. Aby uzyskać szczegółowe informacje dotyczące implementacji klienta, zobacz[Node.js web app sign-in and sign-out with Azure AD (Logowanie się do aplikacji internetowych Node.js i wylogowywanie się z nich za pomocą usługi Azure AD)](quickstart-v1-openid-connect-code.md).

Pełny przykład kodu skojarzony z tym artykułem jest dostępny w repozytorium [GitHub](https://github.com/Azure-Samples/active-directory-node-webapi-basic).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, należy spełnić poniższe wymagania wstępne.

### <a name="create-the-sample-project"></a>Tworzenie przykładowego projektu

Aplikacja serwerowa wymaga kilku zależności pakietów do obsługi modułu Restify i usługi Passport, a także informacji o koncie przekazywanych do usługi Azure AD.

Aby rozpocząć, dodaj następujący kod do pliku o nazwie `package.json`:

```Shell
{
  "name": "active-directory-webapi-nodejs",
  "version": "0.0.1",
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {
    "passport": "0.4.0",
    "passport-azure-ad": "4.0.0",
    "restify": "7.7.0"
  }
}
```

Po utworzeniu pliku `package.json` w wierszu polecenia uruchom polecenie `npm install`, aby zainstalować zależności pakietów.

#### <a name="configure-the-project-to-use-active-directory"></a>Konfigurowanie projektu pod kątem korzystania z usługi Active Directory

Aby rozpocząć konfigurowanie aplikacji, należy pobrać kilka wartości specyficznych dla konta, które można uzyskać z poziomu interfejsu wiersza polecenia platformy Azure. Najprostszym sposobem rozpoczęcia pracy z interfejsem wiersza polecenia jest użycie usługi Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wprowadź następujące polecenie w usłudze Cloud Shell:

```azurecli-interactive
az ad app create --display-name node-aad-demo --homepage http://localhost --identifier-uris http://node-aad-demo
```

[Argumenty](/cli/azure/ad/app?view=azure-cli-latest#az-ad-app-create) dla polecenia `create` obejmują:

| Argument  | Opis |
|---------|---------|
|`display-name` | Przyjazna nazwa rejestracji |
|`homepage` | Adres URL, którego użytkownicy mogą użyć, aby zalogować się do aplikacji i korzystać z niej |
|`identifier-uris` | Oddzielane spacjami unikatowe identyfikatory URI, których usługa Azure AD może używać na potrzeby tej aplikacji |

Do nawiązania połączenia z usługą Azure Active Directory potrzebne są następujące informacje:

| Name (Nazwa)  | Opis | Nazwa zmiennej w pliku konfiguracji |
| ------------- | ------------- | ------------- |
| Nazwa dzierżawy  | [Nazwa dzierżawy](quickstart-create-new-tenant.md), która ma być używana do uwierzytelniania | `tenantName`  |
| Identyfikator klienta  | Identyfikator klienta to termin dotyczący protokołu OAuth na potrzeby _identyfikatora aplikacji_ usługi AAD. |  `clientID`  |

Z odpowiedzi rejestracji w usłudze Azure Cloud Shell skopiuj wartość `appId` i utwórz nowy plik o nazwie `config.js`. Następnie dodaj poniższy kod i zastąp swoje wartości tokenami w nawiasach:

```JavaScript
const tenantName    = //<YOUR_TENANT_NAME>;
const clientID      = //<YOUR_APP_ID_FROM_CLOUD_SHELL>;
const serverPort    = 3000;

module.exports.serverPort = serverPort;

module.exports.credentials = {
  identityMetadata: `https://login.microsoftonline.com/${tenantName}.onmicrosoft.com/.well-known/openid-configuration`, 
  clientID: clientID
};
```

Aby uzyskać więcej informacji na temat poszczególnych ustawień konfiguracyjnych, zapoznaj się z dokumentacją modułu [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage).

### <a name="implement-the-server"></a>Implementowanie serwera

Moduł [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage) obsługuje dwie strategie uwierzytelniania: strategię [OIDC](https://github.com/AzureAD/passport-azure-ad#51-oidcstrategy) i strategię [elementu nośnego](https://github.com/AzureAD/passport-azure-ad#52-bearerstrategy). Serwer zaimplementowany w tym artykule korzysta ze strategii elementu nośnego do zabezpieczenia punktu końcowego interfejsu API.

### <a name="step-1-import-dependencies"></a>Krok 1: Importowanie zależności

Utwórz nowy plik o nazwie `app.js` i wklej następujący tekst:

```JavaScript
const
      restify = require('restify')
    , restifyPlugins = require ('restify').plugins
    , passport = require('passport')
    , BearerStrategy = require('passport-azure-ad').BearerStrategy
    , config = require('./config')
    , authenticatedUserTokens = []
    , serverPort = process.env.PORT || config.serverPort
;
```

W powyższej sekcji kodu:

- Odwołania do modułu `restify` i modułów wtyczek są tworzone w celu skonfigurowania serwera Restify.
- Moduły `passport` i `passport-azure-ad` są odpowiedzialne za komunikację z usługą Azure AD.
- Zmienna `config` jest inicjowana z wartościami z pliku `config.js` utworzonego w poprzednim kroku.
- Tablica jest tworzona dla elementu `authenticatedUserTokens` w celu przechowywania tokenów użytkowników, gdy są one przekazywane do zabezpieczonych punktów końcowych.
- Element `serverPort` jest definiowany na podstawie portu środowiska procesu lub pliku konfiguracji.

### <a name="step-2-instantiate-an-authentication-strategy"></a>Krok 2: Tworzenie wystąpienia strategii uwierzytelniania

Po zabezpieczeniu punktu końcowego należy podać strategię odpowiedzialną za określenie, czy bieżące żądanie pochodzi od uwierzytelnionego użytkownika. W tym przypadku zmienna `authenticatonStrategy` jest wystąpieniem klasy `BearerStrategy` modułu `passport-azure-ad`. Dodaj następujący kod po instrukcjach `require`.

```JavaScript
const authenticationStrategy = new BearerStrategy(config.credentials, (token, done) => {
    let currentUser = null;

    let userToken = authenticatedUserTokens.find((user) => {
        currentUser = user;
        user.sub === token.sub;
    });

    if(!userToken) {
        authenticatedUserTokens.push(token);
    }

    return done(null, currentUser, token);
});
```

Ta implementacja korzysta z rejestracji automatycznej, dodając tokeny uwierzytelniania do tablicy `authenticatedUserTokens`, jeśli jeszcze nie istnieją.

Po utworzeniu nowego wystąpienia strategii należy przekazać je do usługi Passport za pośrednictwem metody `use`. Dodaj następujący kod do pliku `app.js` w celu użycia strategii w usłudze Passport.

```JavaScript
passport.use(authenticationStrategy);
```

### <a name="step-3-server-configuration"></a>Krok 3: Konfiguracja serwera

Po zdefiniowaniu strategii uwierzytelniania możesz teraz skonfigurować serwer Restify za pomocą pewnych ustawień podstawowych i użyć usługi Passport na potrzeby zabezpieczeń.

```JavaScript
const server = restify.createServer({ name: 'Azure Active Directory with Node.js Demo' });
server.use(restifyPlugins.authorizationParser());
server.use(passport.initialize());
server.use(passport.session());
```
Ten serwer jest inicjowany i konfigurowany do analizowania nagłówków autoryzacji, a następnie ustawiany do używania usługi Passport.

### <a name="step-4-define-routes"></a>Krok 4: Definiowanie tras

Możesz teraz zdefiniować trasy i określić, które z nich mają zostać zabezpieczone za pomocą usługi Azure AD. Ten projekt zawiera dwie trasy, których poziom główny jest otwarty, a trasa `/api` jest ustawiona w celu wymagania uwierzytelniania.

W pliku `app.js` dodaj następujący kod dla trasy poziomu głównego:

```JavaScript
server.get('/', (req, res, next) => {
    res.send(200, 'Try: curl -isS -X GET http://127.0.0.1:3000/api');
    next();
});
```

Trasa główna zezwala na wszystkie żądania i zwraca komunikat, który zawiera polecenie służące do przetestowania trasy `/api`. Z kolei trasa `/api` jest zablokowana przy użyciu metody [`passport.authenticate`](http://passportjs.org/docs/authenticate). Dodaj następujący kod poniżej trasy głównej.

```JavaScript
server.get('/api', passport.authenticate('oauth-bearer', { session: false }), (req, res, next) => {
    res.json({ message: 'response from API endpoint' });
    return next();
});
```

Ta konfiguracja zezwala jedynie na uwierzytelnione żądania, które obejmują dostęp tokenu elementu nośnego do trasy `/api`. Dzięki użyciu opcji `session: false` w ramach sesji nie jest wymagane przekazywanie tokenu do interfejsu API wraz każdym żądaniem.

Na końcu serwer jest ustawiany do nasłuchiwania na skonfigurowanym porcie przez wywołanie metody `listen`.

```JavaScript
server.listen(serverPort);
```

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Teraz, gdy serwer jest zaimplementowany, możesz go uruchomić, otwierając wiersz polecenia i wprowadzając następujące polecenie:

```shell
npm start
```

Gdy serwer jest uruchomiony, możesz przesłać do niego żądanie w celu przetestowania wyników. Aby zademonstrować odpowiedź z trasy głównej, otwórz powłokę bash i wprowadź następujący kod:

```shell
curl -isS -X GET http://127.0.0.1:3000/
```

Jeśli serwer został poprawnie skonfigurowany, odpowiedź powinna wyglądać podobnie do poniższej:

```shell
HTTP/1.1 200 OK
Server: Azure Active Directory with Node.js Demo
Content-Type: application/json
Content-Length: 49
Date: Tue, 10 Oct 2017 18:35:13 GMT
Connection: keep-alive

Try: curl -isS -X GET http://127.0.0.1:3000/api
```

Następnie możesz przetestować trasy, które wymagają uwierzytelniania, wprowadzając następujące polecenie w powłoce bash:

```shell
curl -isS -X GET http://127.0.0.1:3000/api
```

Jeśli serwer został poprawnie skonfigurowany, powinien on odpowiedzieć za pomocą stanu `Unauthorized`.

```shell
HTTP/1.1 401 Unauthorized
Server: Azure Active Directory with Node.js Demo
WWW-Authenticate: token is not found
Date: Tue, 10 Oct 2017 16:22:03 GMT
Connection: keep-alive
Content-Length: 12

Unauthorized
```

Po utworzeniu bezpiecznego interfejsu API możesz zaimplementować klienta, za pomocą którego możliwe jest przekazywanie tokenów uwierzytelniania do interfejsu API.

## <a name="next-steps"></a>Kolejne kroki

* Należy zaimplementować odpowiednik klienta w celu nawiązania połączenia z serwerem, który obsługuje logowanie, wylogowywanie i zarządzanie tokenami. Przykłady oparte na kodzie możesz znaleźć w aplikacjach klienckich w systemie [iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios) i [Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android).
* Aby uzyskać samouczek krok po kroku, zobacz [Node.js web app sign-in and sign-out with Azure AD (Logowanie się do aplikacji internetowych Node.js i wylogowywanie się z nich za pomocą usługi Azure AD)](quickstart-v1-openid-connect-code.md).
