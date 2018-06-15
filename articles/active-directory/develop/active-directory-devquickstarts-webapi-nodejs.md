---
title: Azure AD Node.js interfejsu API sieci web wprowadzenie | Dokumentacja firmy Microsoft
description: Jak utworzyć sieci web Node.js REST API, która integruje się z usługą Azure AD do uwierzytelniania.
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
ms.openlocfilehash: 24591c46858970724fbd1fe36336f7f2b0b2fefd
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34155841"
---
# <a name="azure-ad-nodejs-web-api-getting-started"></a>Azure AD Node.js interfejsu API sieci web wprowadzenie

W tym artykule pokazano, jak zabezpieczyć [Restify](http://restify.com/) punkt końcowy interfejsu API z [Passport](http://passportjs.org/) przy użyciu [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad) modułu do obsługi komunikacji z usługą Azure Active Directory (AAD). 

Zakres tego samouczka obejmuje problemów dotyczących zabezpieczanie punkty końcowe interfejsu API. Dotyczy logowania i zachowując tokeny uwierzytelniania nie zostały zaimplementowane w tym miejscu i są odpowiedzialne za aplikacji klienckiej. Szczegóły otaczającego implementacja klienta, można przejrzeć [aplikacji sieci web Node.js logowania i wylogowywania z usługą Azure AD](active-directory-devquickstarts-openidconnect-nodejs.md).

Przykład pełny kod, skojarzone z tym artykule jest dostępny na [GitHub](https://github.com/Azure-Samples/active-directory-node-webapi-basic).

## <a name="create-the-sample-project"></a>Utworzyć przykładowy projekt
Ta aplikacja serwera wymaga kilku zależności pakietu do obsługi modułu Restify i Passport, a także konta informacje przekazywane do usługi AAD.

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

Raz `package.json` został utworzony, uruchom `npm install` w z wiersza polecenia, aby zainstalować zależności pakietów. 

### <a name="configure-the-project-to-use-active-directory"></a>Konfigurowanie projektu do użycia usługi Active Directory

Aby rozpocząć konfigurowanie aplikacji, istnieje kilka wartości specyficznych dla konta, które można uzyskać z wiersza polecenia platformy Azure. Najprostszym sposobem, aby rozpocząć korzystanie z interfejsu wiersza polecenia jest użyć powłoki chmury Azure.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wprowadź następujące polecenie w powłoce chmury: 

```azurecli-interactive
az ad app create --display-name node-aad-demo --homepage http://localhost --identifier-uris http://node-aad-demo
```

[Argumenty](/cli/azure/ad/app?view=azure-cli-latest#az_ad_app_create) dla `create` polecenia obejmują:

| Argument  | Opis |
|---------|---------|
|`display-name` | Przyjazna nazwa rejestracji |
|`homepage` | Adres URL, gdzie użytkownicy mogą zalogować i korzystać z aplikacji |
|`identifier-uris` | Spacjami unikatowych identyfikatorów URI usługi Azure AD można użyć dla tej aplikacji |

Przed podłączeniem do usługi Azure Active Directory, należy podać następujące informacje:

| Name (Nazwa)  | Opis | Nazwa zmiennej w pliku konfiguracji |
| ------------- | ------------- | ------------- |
| Nazwa dzierżawcy  | [Nazwa dzierżawcy](active-directory-howto-tenant.md) ma być używany do uwierzytelniania | `tenantName`  |
| Identyfikator klienta  | Identyfikator klienta to określenie OAuth dla usługi AAD _identyfikator aplikacji_. |  `clientID`  |

Odpowiedzi dotyczącej rejestracji, w powłoce chmury Azure, skopiuj `appId` wartości i Utwórz nowy plik o nazwie `config.js`. Następnie dodaj poniższy kod i Zastąp wartości w nawiasach kwadratowych tokenów:

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
[Passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage) modułu funkcje dwóch strategii uwierzytelniania: [OIDC](https://github.com/AzureAD/passport-azure-ad#51-oidcstrategy) i [elementu nośnego](https://github.com/AzureAD/passport-azure-ad#52-bearerstrategy) strategii. Serwer zaimplementowana w tym artykule używa strategii elementu nośnego, aby zabezpieczyć punkt końcowy interfejsu API.

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

- `config` Zmienna jest zainicjowana wartościami z `config.js` plik utworzony w poprzednim kroku.

- Tablica jest tworzona dla `authenticatedUserTokens` do przechowywania tokenów użytkownika, ponieważ są one przekazywane do zabezpieczonej punktów końcowych.

- `serverPort` Albo zdefiniowano z portu środowiska procesu lub z pliku konfiguracji.

### <a name="step-2-instantiate-an-authentication-strategy"></a>Krok 2: Utworzenie wystąpienia strategii uwierzytelniania
Jak zabezpieczyć punkt końcowy, musisz podać strategii odpowiedzialna za określenie, czy bieżące żądanie pochodzi z uwierzytelnionego użytkownika. W tym miejscu `authenticatonStrategy` zmiennej jest wystąpieniem `passport-azure-ad` `BearerStrategy` klasy. Dodaj następujący kod po `require` instrukcje.

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
Ta implementacja używa automatyczną rejestrację przez dodanie tokeny uwierzytelniania do `authenticatedUserTokens` tablica, jeśli jeszcze nie istnieje.

Po utworzeniu nowego wystąpienia strategii należy przekazać ją w usłudze Passport za pośrednictwem `use` metody. Dodaj następujący kod do `app.js` do użycia na strategię w usłudze Passport.

```JavaScript
passport.use(authenticationStrategy);
```

### <a name="step-3-server-configuration"></a>Krok 3: Konfiguracja serwera
Ze strategią uwierzytelniania zdefiniowane można skonfigurować serwer Restify z niektórych podstawowych ustawień i skonfigurowane do korzystania z usługi Passport zabezpieczeń.

```JavaScript
const server = restify.createServer({ name: 'Azure Active Directroy with Node.js Demo' });
server.use(restifyPlugins.authorizationParser());
server.use(passport.initialize());
server.use(passport.session());
```
Ten serwer jest zainicjowany i skonfigurowany tak, aby przeanalizować nagłówki autoryzacji i następnie skonfigurowane do korzystania z usługi Passport.


### <a name="step-4-define-routes"></a>Krok 4: Definiowanie tras
Można teraz trasy są definiowane i zdecydować, które do zabezpieczenia przy użyciu usługi AAD. Ten projekt zawiera dwie trasy, gdzie poziomu głównego jest otwarty i `/api` trasy ma ustawioną wartość Wymagaj uwierzytelniania.

W `app.js` Dodaj następujący kod dla trasy poziomu głównego:

```JavaScript
server.get('/', (req, res, next) => {
    res.send(200, 'Try: curl -isS -X GET http://127.0.0.1:3000/api');
    next();
});
```

Trasy głównego umożliwia wszystkich żądań za pośrednictwem trasy i zwraca komunikat, który zawiera polecenie, aby przetestować `/api` trasy. Z kolei `/api` trasy jest zablokowana przy użyciu [ `passport.authenticate` ](http://passportjs.org/docs/authenticate). Dodaj następujący kod po trasy głównego.

```JavaScript
server.get('/api', passport.authenticate('oauth-bearer', { session: false }), (req, res, next) => {
    res.json({ message: 'response from API endpoint' });
    return next();
});
```

Ta konfiguracja umożliwia tylko uwierzytelnionego żądania, które obejmują dostęp tokenu elementu nośnego, do `/api`. Opcja `session: false` jest używana do wyłączenia sesji, aby wymagać, że token jest przekazywany z każdym żądaniem do interfejsu API.

Na koniec ustawiono serwera do nasłuchiwania na porcie skonfigurowanym przez wywołanie metody `listen` metody.

```JavaScript
server.listen(serverPort);
```

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Teraz, gdy serwer jest zaimplementowana, można uruchomić serwera, otwierając wiersz polecenia i wprowadź:

```Shell
npm start
```

Z serwerem należy przesłać żądanie do serwera do wyników testu. Aby zademonstrować odpowiedzi na podstawie trasy głównego, otwórz powłoki bash i wprowadź poniższy kod:

```Shell 
curl -isS -X GET http://127.0.0.1:3000/
```

Jeśli serwer został skonfigurowany prawidłowo, powinien wyglądać podobnie do odpowiedzi:

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

Jeśli serwer został skonfigurowany prawidłowo, a następnie serwer powinien odpowiadać ze stanem `Unauthorized`.

```Shell
HTTP/1.1 401 Unauthorized
Server: Azure Active Directroy with Node.js Demo
WWW-Authenticate: token is not found
Date: Tue, 10 Oct 2017 16:22:03 GMT
Connection: keep-alive
Content-Length: 12

Unauthorized
```
Teraz, po utworzeniu bezpiecznego interfejsu API, można zaimplementować klienta, który jest w stanie przekazać tokeny uwierzytelniania do interfejsu API.

## <a name="next-steps"></a>Kolejne kroki
Zgodnie z wprowadzeniem, musisz zaimplementować odpowiednik klienta do łączenia się z serwerem obsługującym logowania, wylogowywania i zarządzania tokenów. Oparte na kodzie przykłady mogą odwoływać się do aplikacji klienckich w [iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios) i [Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android). Samouczek krok po kroku można znaleźć w następującym artykule:

> [!div class="nextstepaction"]
> [Aplikacja sieci web node.js logowania i wylogowywania z usługą Azure AD](active-directory-devquickstarts-openidconnect-nodejs.md)