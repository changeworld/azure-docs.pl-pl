---
title: Usługa Azure AD w środowisku Node.js interfejsu API sieci web wprowadzenie | Dokumentacja firmy Microsoft
description: Jak utworzyć Node.js REST internetowego interfejsu API, która integruje się z usługą Azure AD do uwierzytelniania.
services: active-directory
documentationcenter: nodejs
author: CelesteDG
manager: mtillman
ms.assetid: 7654ab4c-4489-4ea5-aba9-d7cdc256e42a
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 11/30/2017
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 3b203e5be82c01e7d586c90bae454aca23ebd630
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39581456"
---
# <a name="azure-ad-nodejs-web-api-getting-started"></a>Usługa Azure AD w środowisku Node.js interfejsu API sieci web wprowadzenie

W tym artykule pokazano, jak zabezpieczyć [Restify](http://restify.com/) punkt końcowy interfejsu API za pomocą [Passport](http://passportjs.org/) przy użyciu [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad) modułu do obsługi komunikacji z usługą Azure Active Directory (AAD). 

Zakres tego samouczka opisano problemy dotyczące zabezpieczanie punktów końcowych interfejsu API. Problemy z logowaniem i przechowywanie tokenów uwierzytelniania nie są zaimplementowane w tym miejscu i są odpowiedzialne za aplikacji klienckiej. Aby uzyskać szczegółowe informacje otaczającego implementacji klienta, zapoznaj się [aplikacji sieci web Node.js logowania i wylogowania z usługą Azure AD](quickstart-v1-openid-connect-code.md).

Przykład pełny kod skojarzony z tym artykule jest dostępny w [GitHub](https://github.com/Azure-Samples/active-directory-node-webapi-basic).

## <a name="create-the-sample-project"></a>Utwórz przykładowy projekt
Ta aplikacja serwera wymaga kilku zależności pakietów do obsługi modułu Restify i strategii Passport, a także konta informacje, które są przekazywane do usługi AAD.

Aby rozpocząć, Dodaj następujący kod do pliku o nazwie `package.json`:

```Shell
{
  "name": "node-aad-demo",
  "version": "0.0.1",
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {
    "passport": "0.4.0",
    "passport-azure-ad": "3.0.8",
    "restify": "6.0.1",
    "restify-plugins": "1.6.0"
  }
}
```

Gdy `package.json` zostanie utworzony, uruchom `npm install` w wierszu polecenia do zainstalowania zależności pakietów. 

### <a name="configure-the-project-to-use-active-directory"></a>Skonfiguruj projekt do korzystania z usługi Active Directory

Aby rozpocząć konfigurowanie aplikacji, istnieje kilka wartości specyficznych dla konta, którego można uzyskać z wiersza polecenia platformy Azure. Najprostszym sposobem rozpoczęcia pracy przy użyciu interfejsu wiersza polecenia jest korzystać z usługi Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wprowadź następujące polecenie w usłudze cloud shell: 

```azurecli-interactive
az ad app create --display-name node-aad-demo --homepage http://localhost --identifier-uris http://node-aad-demo
```

[Argumenty](/cli/azure/ad/app?view=azure-cli-latest#az-ad-app-create) dla `create` polecenia obejmują:

| Argument  | Opis |
|---------|---------|
|`display-name` | Przyjazna nazwa rejestracji |
|`homepage` | Adres URL, których użytkownicy mogą zarejestrować się w i korzystania z aplikacji |
|`identifier-uris` | Unikatowe identyfikatory URI, używanego przez usługi Azure AD dla tej aplikacji spacjami. |

Zanim będzie można połączyć z usługą Azure Active Directory, potrzebne są następujące informacje:

| Name (Nazwa)  | Opis | Nazwa zmiennej w pliku konfiguracji |
| ------------- | ------------- | ------------- |
| Nazwa dzierżawy  | [Nazwa dzierżawy](quickstart-create-new-tenant.md) ma być używany do uwierzytelniania | `tenantName`  |
| Identyfikator klienta  | Identyfikator klienta to określenie protokołu OAuth dla usługi AAD _identyfikator aplikacji_. |  `clientID`  |

Skopiuj z odpowiedzi rejestracji w usłudze Azure Cloud Shell, `appId` wartość i utworzyć nowy plik o nazwie `config.js`. Następnie dodaj poniższy kod i Zastąp wartości w nawiasach kwadratowych tokenów:

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
Aby uzyskać więcej informacji na temat ustawień konfiguracji poszczególnych Przejrzyj [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage) dokumentacji modułu.

## <a name="implement-the-server"></a>Implementowanie serwera
[Passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage) moduł funkcji pomocy dwóch strategii uwierzytelniania: [OIDC](https://github.com/AzureAD/passport-azure-ad#51-oidcstrategy) i [elementu nośnego](https://github.com/AzureAD/passport-azure-ad#52-bearerstrategy) strategii. Zaimplementowane w tym artykule są używane strategii elementu nośnego do bezpiecznego punktu końcowego interfejsu API.

### <a name="step-1-import-dependencies"></a>Krok 1: Import zależności
Utwórz nowy plik o nazwie `app.js` i wklej następujący tekst:

```JavaScript
const
      restify = require('restify')
    , restifyPlugins = require('restify-plugins')
    , passport = require('passport')
    , BearerStrategy = require('passport-azure-ad').BearerStrategy
    , config = require('./config')
    , authenticatedUserTokens = []
    , serverPort = process.env.PORT || config.serverPort
;
```

W tej sekcji kodu:

- `restify` i `restify-plugins` moduły są wywoływane w celu skonfigurowania serwera Restify.

- `passport` i `passport-azure-ad` moduły są zobowiązani do komunikacji przy użyciu usługi AAD.

- `config` Zmienna jest inicjowana z wartościami z `config.js` pliku utworzonego w poprzednim kroku.

- Tablica jest tworzona dla `authenticatedUserTokens` do przechowywania tokenów użytkownika, ponieważ są one przekazywane do bezpiecznego punktów końcowych.

- `serverPort` Albo zdefiniowano z portu środowiska procesu lub z pliku konfiguracji.

### <a name="step-2-instantiate-an-authentication-strategy"></a>Krok 2: Utwórz wystąpienie strategii uwierzytelniania
Jako punkt końcowy należy zabezpieczyć, należy podać strategii jest odpowiedzialny za sprawdzenie, czy bieżące żądanie pochodzi z uwierzytelnionego użytkownika. W tym miejscu `authenticatonStrategy` zmienna jest wystąpieniem `passport-azure-ad` `BearerStrategy` klasy. Dodaj następujący kod po `require` instrukcji.

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
Ta implementacja używa rejestracji automatycznej, dodając tokenów uwierzytelniania do `authenticatedUserTokens` tablicy, jeśli jeszcze nie istnieje.

Po utworzeniu nowego wystąpienia strategii możesz przekazać go w usłudze Passport za pośrednictwem `use` metody. Dodaj następujący kod do `app.js` do użycia strategii w usłudze Passport.

```JavaScript
passport.use(authenticationStrategy);
```

### <a name="step-3-server-configuration"></a>Krok 3: Konfiguracja serwera
Za pomocą strategii uwierzytelniania zdefiniowane możesz teraz skonfigurować serwer Restify z niektórymi ustawieniami podstawowe i ustawić na potrzeby usługi Passport zabezpieczeń.

```JavaScript
const server = restify.createServer({ name: 'Azure Active Directroy with Node.js Demo' });
server.use(restifyPlugins.authorizationParser());
server.use(passport.initialize());
server.use(passport.session());
```
Ten serwer zostanie zainicjowana i skonfigurowana do analizowania nagłówków autoryzacji i następnie skonfigurowany do używania usługi Passport.


### <a name="step-4-define-routes"></a>Krok 4: Definiowanie tras
Można teraz trasy są definiowane i określić, które można zabezpieczyć przy użyciu usługi AAD. Ten projekt zawiera dwie trasy, gdzie jest otwarty do katalogu głównego i `/api` trasy jest równa wymagają uwierzytelniania.

W `app.js` Dodaj następujący kod dla danej trasy do poziomu głównego:

```JavaScript
server.get('/', (req, res, next) => {
    res.send(200, 'Try: curl -isS -X GET http://127.0.0.1:3000/api');
    next();
});
```

Główny trasy zezwala na wszystkie żądania za pośrednictwem trasy i zwraca komunikat, który zawiera polecenie, aby przetestować `/api` trasy. Z drugiej strony `/api` trasy jest zablokowana przy użyciu [ `passport.authenticate` ](http://passportjs.org/docs/authenticate). Dodaj następujący kod po główny trasy.

```JavaScript
server.get('/api', passport.authenticate('oauth-bearer', { session: false }), (req, res, next) => {
    res.json({ message: 'response from API endpoint' });
    return next();
});
```

Ta konfiguracja może składać się uwierzytelnionego żądania, które oferują dostęp tokenu elementu nośnego, do `/api`. Opcja `session: false` służy do wyłączania sesje wymagają, że token jest przekazywany do interfejsu API z każdym żądaniem.

Ponadto serwer jest ustawiony do nasłuchiwania na porcie skonfigurowanym przez wywołanie metody `listen` metody.

```JavaScript
server.listen(serverPort);
```

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Teraz, gdy serwer jest implementowana, możesz uruchomić serwer, otwierając wiersz polecenia i wprowadź:

```Shell
npm start
```

Z systemu można przesłać żądania do serwera, do wyników testu. Aby zademonstrować odpowiedzi z główny trasy, Otwórz powłokę bash i wprowadź następujący kod:

```Shell 
curl -isS -X GET http://127.0.0.1:3000/
```

Jeśli serwer został skonfigurowany poprawnie, odpowiedź powinna wyglądać podobnie do:

```Shell
HTTP/1.1 200 OK
Server: Azure Active Directroy with Node.js Demo
Content-Type: application/json
Content-Length: 49
Date: Tue, 10 Oct 2017 18:35:13 GMT
Connection: keep-alive

Try: curl -isS -X GET http://127.0.0.1:3000/api
```

Następnie można przetestować trasy, który wymaga uwierzytelniania, wprowadzając następujące polecenie do powłoki bash:

```Shell 
curl -isS -X GET http://127.0.0.1:3000/api
```

Jeśli serwer został skonfigurowany poprawnie, a następnie serwer powinien odpowiadać ze stanem `Unauthorized`.

```Shell
HTTP/1.1 401 Unauthorized
Server: Azure Active Directroy with Node.js Demo
WWW-Authenticate: token is not found
Date: Tue, 10 Oct 2017 16:22:03 GMT
Connection: keep-alive
Content-Length: 12

Unauthorized
```
Teraz, po utworzeniu bezpiecznego interfejsu API, można zaimplementować klienta, który umożliwia przekazywanie tokenów uwierzytelniania do interfejsu API.

## <a name="next-steps"></a>Kolejne kroki
Jak wspomniano we wprowadzeniu, należy zaimplementować odpowiednika klienta, aby połączyć się z serwerem, obsługujący logowanie i wylogowywanie oraz zarządzania nimi tokenów. Przykłady oparte na kodzie, mogą odwoływać się do aplikacji klienckich w [iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios) i [Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android). Samouczek krok po kroku można znaleźć w następującym artykule:

> [!div class="nextstepaction"]
> [Aplikację internetową środowiska node.js, logowania i wylogowania z usługą Azure AD](quickstart-v1-openid-connect-code.md)