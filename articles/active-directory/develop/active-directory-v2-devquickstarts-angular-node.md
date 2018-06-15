---
title: Azure AD 2.0 wprowadzenie aplikacji jednej strony NodeJS AngularJS | Dokumentacja firmy Microsoft
description: Sposób tworzenia aplikacji jednostronicowej kątowego JS logujący się użytkowników z obu osobistego konta Microsoft i konta firmowego lub szkolnego.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: d286aa33-8a94-452f-beb7-ddc6c6daa5c8
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/23/2017
ms.author: celested
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: c6f617c43cc5b4d471f3effb6f4a633604b8dde0
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157813"
---
# <a name="add-sign-in-to-an-angularjs-single-page-app---nodejs"></a>Dodawanie logowania do aplikacji jednej strony AngularJS - NodeJS
W tym artykule dodamy Zaloguj się przy użyciu konta Microsoft, jej do AngularJS aplikacji za pomocą punktu końcowego v2.0 usługi Azure Active Directory. punktu końcowego v2.0 umożliwiają wykonywanie jednego integracji w aplikacji i uwierzytelnianie użytkowników przy użyciu kont osobistych i pracy/służbowe.

Ten przykład jest prostej aplikacji jednej strony listy zadań do wykonania, która przechowuje zadania w wewnętrznej bazie danych interfejsu API REST, napisana w środowisku NodeJS i chronione za pomocą tokenów elementu nośnego OAuth z usługi Azure AD. AngularJS aplikacja będzie używać nasza Biblioteka uwierzytelniania JavaScript typu open source [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js) do obsługi całego logowania procesu i uzyskać tokeny wywołania interfejsu API REST. Do uwierzytelniania innych interfejsów API REST, tak samo, jak można zastosować tego samego wzorca [Microsoft Graph](https://graph.microsoft.com) lub interfejsów API usługi Azure Resource Manager.

> [!NOTE]
> Nie wszystkie usługi Azure Active Directory scenariuszy i funkcji obsługiwanych przez punktu końcowego v2.0. Aby ustalić, czy należy używać punktu końcowego v2.0, przeczytaj o [ograniczenia v2.0](active-directory-v2-limitations.md).
> 
> 

## <a name="download"></a>Do pobrania
Aby rozpocząć pracę, musisz pobrać i zainstalować [node.js](https://nodejs.org). Następnie można sklonować lub [Pobierz](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS/archive/skeleton.zip) szkielet aplikacji:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS.git
```

Szkielet aplikacji obejmuje całego kodu umożliwiającego prostej aplikacji AngularJS, ale brakuje wszystkie elementy związane z tożsamości. Jeśli nie chcesz z niego skorzystać, zamiast tego można sklonować lub [Pobierz](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS/archive/complete.zip) próbki ukończone.

```
git clone https://github.com/AzureADSamples/SinglePageApp-AngularJS-NodeJS.git
```

## <a name="register-an-app"></a>Rejestracja aplikacji
Najpierw utwórz aplikację w [portalu rejestracji aplikacji](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), lub wykonaj następujące [szczegółowe kroki](active-directory-v2-app-registration.md). Upewnij się, że:

* Dodaj **Web** platformy aplikacji.
* Wprowadź poprawny **identyfikator URI przekierowania**. Wartością domyślną w tym przykładzie jest `http://localhost:8080`.
* Pozostaw **Zezwalaj przepływu niejawnego** pole wyboru jest włączone. 

Skopiuj **identyfikator aplikacji** przypisany do aplikacji, będzie on potrzebny później. 

## <a name="install-adaljs"></a>Zainstaluj adal.js
Aby rozpocząć, przejdź do projektu, należy pobrać i zainstalować adal.js. Jeśli masz [bower](http://bower.io/) zainstalowany, możesz po prostu uruchomić to polecenie. W przypadku niezgodności wersji zależności wystarczy wybrać nowszej wersji.

```
bower install adal-angular#experimental
```

Alternatywnie możesz ręcznie pobrać [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal.min.js) i [adal angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal-angular.min.js). Dodaj oba pliki do `app/lib/adal-angular-experimental/dist` katalogu.

Teraz Otwórz projekt w w ulubionym edytorze tekstów i załadować adal.js na końcu treści strony:

```html
<!--index.html-->

...

<script src="App/bower_components/dist/adal.min.js"></script>
<script src="App/bower_components/dist/adal-angular.min.js"></script>

...
```

## <a name="set-up-the-rest-api"></a>Ustawianie interfejsu API REST
Gdy konfigurujemy rzeczy, umożliwia działało interfejsu API REST wewnętrznej bazy danych. W wierszu polecenia, należy zainstalować wszystkie niezbędne pakiety, uruchamiając (Upewnij się, możesz w katalogu najwyższego poziomu projektu):

```
npm install
```

Teraz otworzyć `config.js` i Zastąp `audience` wartość:

```js
exports.creds = {

     // TODO: Replace this value with the Application ID from the registration portal
     audience: '<Your-application-id>',

     ...
}
```

Ta wartość zostanie użyta do sprawdzania poprawności tokenów otrzymywanych z poziomu aplikacji kątową AJAX żądania interfejsu API REST. Należy pamiętać, że to prosty interfejs API REST przechowuje dane w pamięci — dzięki czasu należy zatrzymać serwer, utracisz wszystkie zadania utworzonego wcześniej.

Nadszedł czas, którą spróbujemy poświęcić dyskutować działanie interfejsu API REST. Możesz także umieszczania przy w kodzie, ale jeśli chcesz dowiedzieć się więcej na temat zabezpieczania sieci web API z usługi Azure AD, zapoznaj się [w tym artykule](active-directory-v2-devquickstarts-node-api.md). 

## <a name="sign-users-in"></a>Loguj użytkowników
Czas do pisania kodu tożsamości. Zwróć uwagę na to już tego adal.js zawiera dostawcy AngularJS, który dobrze odgrywa kątowego mechanizmów routingu. Rozpocznij od dodania modułu adal w aplikacji:

```js
// app/scripts/app.js

angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {

...
```

Można teraz zainicjować `adalProvider` za pomocą Identyfikatora aplikacji:

```js
// app/scripts/app.js

...

adalProvider.init({

        // Use this value for the public instance of Azure AD
        instance: 'https://login.microsoftonline.com/', 

        // The 'common' endpoint is used for multi-tenant applications like this one
        tenant: 'common',

        // Your application id from the registration portal
        clientId: '<Your-application-id>',

        // If you're using IE, uncommment this line - the default HTML5 sessionStorage does not work for localhost.
        //cacheLocation: 'localStorage',

    }, $httpProvider);
```

Duża, adal.js ma teraz wszystkie informacje potrzebne do zabezpieczania aplikacji i zaloguj się użytkownicy. Aby wymusić logowania dla określonej trasy w aplikacji, potrzebny jest jeden wiersz kodu:

```js
// app/scripts/app.js

...

}).when("/TodoList", {
    controller: "todoListCtrl",
    templateUrl: "/static/views/TodoList.html",
    requireADLogin: true, // Ensures that the user must be logged in to access the route
})

...
```

Teraz, gdy użytkownik kliknie `TodoList` łącza, adal.js spowoduje automatyczne przekierowanie do usługi Azure AD dla logowania w razie potrzeby. Można również jawnie wysyłać żądania logowania i wylogowywania, wywołując adal.js w kontrolerach:

```js
// app/scripts/homeCtrl.js

angular.module('todoApp')
// Load adal.js the same way for use in controllers and views   
.controller('homeCtrl', ['$scope', 'adalAuthenticationService','$location', function ($scope, adalService, $location) {
    $scope.login = function () {

        // Redirect the user to sign in
        adalService.login();

    };
    $scope.logout = function () {

        // Redirect the user to log out    
        adalService.logOut();

    };
...
```

## <a name="display-user-info"></a>Wyświetl informacje o użytkowniku
Teraz, gdy użytkownik jest zalogowany, prawdopodobnie musisz uzyskać dostęp do danych uwierzytelniania zalogowanego użytkownika w aplikacji. Adal.js przedstawia tych informacji w `userInfo` obiektu. Aby uzyskać dostęp do tego obiektu w widoku, należy najpierw dodać adal.js do zakresie głównym odpowiedniego kontrolera:

```js
// app/scripts/userDataCtrl.js

angular.module('todoApp')
// Load ADAL for use in view
.controller('userDataCtrl', ['$scope', 'adalAuthenticationService', function ($scope, adalService) {}]);
```

Następnie można bezpośrednio usunąć `userInfo` obiektu w tym widoku: 

```html
<!--app/views/UserData.html-->

...

    <!--Get the user's profile information from the ADAL userInfo object-->
    <tr ng-repeat="(key, value) in userInfo.profile">
        <td>{{key}}</td>
        <td>{{value}}</td>
    </tr>
...
```

Można również użyć `userInfo` obiektem, aby określić, czy użytkownik jest zalogowany lub nie.

```html
<!--index.html-->

...

    <!--Use the ADAL userInfo object to show the right login/logout button-->
    <ul class="nav navbar-nav navbar-right">
        <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
        <li><a class="btn btn-link" ng-hide="userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    </ul>
...
```

## <a name="call-the-rest-api"></a>Wywołanie interfejsu API REST
Na koniec nadszedł czas do uzyskania niektórych tokenów i wywołania interfejsu API REST do tworzenia, odczytu, aktualizacji i usuwania zadań. Dobrze wieści.  Musisz wykonać *przedmiotu*. Adal.js automatycznie zajmie się pobieranie, buforowanie i odświeżanie tokenów. On również zajmie się dołączanie tych tokenów do wychodzących żądania AJAX, które wysyłają do interfejsu API REST. 

Dokładnie jak to działa? To wszystko dzięki użyciu magic z [interceptory AngularJS](https://docs.angularjs.org/api/ng/service/$http), co pozwala adal.js do przekształcania wychodzące i przychodzące komunikaty http. Ponadto adal.js przyjęto założenie, że wszystkie żądania wysyłania do tej samej domeny jako okno należy używać tokeny przeznaczonych dla tego samego Identyfikatora aplikacji jako aplikacji AngularJS. Jest to, dlaczego użyliśmy ten sam identyfikator aplikacji w aplikacji kątowego i interfejsu API REST NodeJS. Oczywiście można zmienić to zachowanie i poinformuj adal.js uzyskać tokenów dla innych interfejsów API REST w razie potrzeby — ale dla tego prostego scenariusza wykona wartości domyślne.

Oto fragment kodu, który pokazuje, jak łatwo jest wysłać żądania za pomocą tokenów elementu nośnego z usługi Azure AD:

```js
// app/scripts/todoListSvc.js

...
return $http.get('/api/tasks');
...
```

Gratulacje!  Zintegrowane jednej strony aplikacji usługi Azure AD jest teraz ukończona. Teraz, podjąć łuku. Go może uwierzytelniać użytkowników, bezpiecznie wywołać jej zaplecza interfejsu API REST przy użyciu protokołu OpenID Connect i uzyskać podstawowe informacje o użytkowniku. Fabrycznej obsługuje on każdy użytkownik, osobiste Account Microsoft lub konta pracy/służbowych z usługi Azure AD. Wypróbuj aplikacji przez uruchomienie:

```
node server.js
```

W przeglądarce przejdź do `http://localhost:8080`. Zaloguj się przy użyciu konto robocze/służbowe lub osobiste konto Microsoft. Dodawanie zadań do listy zadań do wykonania użytkownika, a Wyloguj. Spróbuj zalogować się za innego typu konta. Jeśli potrzebujesz dzierżawa usługi Azure AD do tworzenia użytkowników pracy/służbowe [Dowiedz się, jak uzyskać, w tym miejscu](active-directory-howto-tenant.md) (jest bezpłatna).

Aby kontynuować zapoznawanie punktu końcowego v2.0, przejdź wstecz do naszej [przewodnik dewelopera v2.0](active-directory-appmodel-v2-overview.md). Aby uzyskać dodatkowe zasoby Zobacz:

* [Przykłady Azure z witryny GitHub >>](https://github.com/Azure-Samples)
* [Azure AD na przepełnienie stosu >>](http://stackoverflow.com/questions/tagged/azure-active-directory)
* Dokumentacja usługi Azure AD [witryny Azure.com >>](https://azure.microsoft.com/documentation/services/active-directory/)

## <a name="get-security-updates-for-our-products"></a>Pobierz aktualizacje zabezpieczeń naszych produktów
Firma Microsoft zachęca do przekazywania powiadomień o występujących incydentach zabezpieczeń poprzez wizytę na [tej stronie](https://technet.microsoft.com/security/dd252948) i subskrybowanie Doradczych alertów zabezpieczeń.

