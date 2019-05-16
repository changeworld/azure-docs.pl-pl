---
title: Tworzenie aplikacji jednostronicowej AngularJS służącej do logowania w usłudze Azure Active Directory i wylogowywania się z niej | Microsoft Docs
description: Dowiedz się, jak utworzyć aplikację jednostronicową AngularJS, która integruje się z usługą Azure AD w celu logowania oraz wywołuje interfejsy API chronione przez usługę Azure AD przy użyciu protokołu OAuth.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: f2991054-8146-4718-a5f7-59b892230ad7
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f526ea3d1a53ef2ae80f36c863e7a19797e9142
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546000"
---
# <a name="quickstart-build-an-angularjs-single-page-app-for-sign-in-and-sign-out-with-azure-active-directory"></a>Szybki start: Tworzenie aplikacji jednostronicowej AngularJS służącej do logowania w usłudze Azure Active Directory i wylogowywania się z niej

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Dzięki usłudze Azure Active Directory (Azure AD) możesz łatwo dodawać funkcje logowania, wylogowywania i bezpieczne wywołania interfejsu API protokołu OAuth do aplikacji jednostronicowych. Dzięki temu aplikacje mogą uwierzytelniać użytkowników za pomocą kont Windows Server Active Directory i używać dowolnego internetowego interfejsu API chronionego przez usługę Azure AD, na przykład interfejsu API usługi Office 365 lub platformy Azure.

W przypadku aplikacji w języku JavaScript uruchomionych w przeglądarce usługa Azure AD zapewnia bibliotekę ADAL (Active Directory Authentication Library) lub adal.js. Służy ona wyłącznie do tego, aby ułatwić aplikacji uzyskanie tokenów dostępu.

W tym przewodniu Szybki start dowiesz się, w jaki sposób utworzyć aplikację listy zadań AngularJS, która:

* Loguje użytkownika do aplikacji przy użyciu usługi Azure AD jako dostawcy tożsamości.
* Wyświetla informacje dotyczące użytkownika.
* Bezpiecznie wywołuje interfejs API listy zadań za pomocą tokenów elementu nośnego z usługi Azure AD.
* Wylogowuje użytkownika z aplikacji.

Aby utworzyć gotową, działającą aplikację, musisz:

1. Zarejestrować aplikację w usłudze Azure AD.
2. Zainstalować bibliotekę ADAL i skonfigurować aplikację jednostronicową.
3. Użyć biblioteki ADAL do zabezpieczenia stron w aplikacji jednostronicowej.

> [!NOTE]
> Jeśli musisz włączyć logowania dla osobistych kont oprócz oraz do kont służbowych, możesz użyć  *[punktu końcowego platformy tożsamości firmy Microsoft](azure-ad-endpoint-comparison.md)*. Aby uzyskać więcej informacji, zobacz [w tym samouczku JavaScript SPA](tutorial-v2-javascript-spa.md) także [w tym artykule](active-directory-v2-limitations.md) wyjaśniających *punktu końcowego platformy tożsamości firmy Microsoft*. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, należy spełnić poniższe następujące wstępne:

* [Pobranie szkieletu aplikacji](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) lub [pobranie pełnego przykładu](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).
* Dzierżawa usługi Azure AD, w której można utworzyć użytkowników i zarejestrować aplikację. Jeśli nie masz jeszcze dzierżawy, [dowiedz się, jak ją uzyskać](quickstart-create-new-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>Krok 1: Rejestrowanie aplikacji DirectorySearcher

Aby umożliwić aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów, należy najpierw zarejestrować ją w dzierżawie usługi Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Jeśli zalogowano się w wielu katalogach, należy upewnić się, że wyświetlany jest prawidłowy katalog. Aby to sprawdzić, kliknij swoje konto na pasku u góry strony. Na liście **Katalog** wybierz dzierżawę usługi Azure AD, w której chcesz zarejestrować aplikację.
1. Kliknij pozycję **Wszystkie usługi** w okienku po lewej stronie, a następnie wybierz pozycję **Azure Active Directory**.
1. Kliknij przycisk **Rejestracje aplikacji**, a następnie wybierz pozycję **Dodaj**.
1. Postępuj zgodnie z monitami, aby utworzyć nową aplikację internetową i/lub internetowy interfejs API:

    * **Nazwa** opisuje aplikację użytkownikom.
    * **Adres URL logowania** to lokalizacja, do której zwracane są tokeny przez usługę Azure AD. W tym przykładzie domyślna lokalizacja to `https://localhost:44326/`.

1. Po zakończeniu rejestracji usługa Azure AD przypisuje aplikacji unikatowy identyfikator. Ta wartość będzie potrzebna w kolejnych sekcjach, a więc należy skopiować ją z karty aplikacji.
1. Biblioteka adal.js wykorzystuje niejawny przepływ protokołu OAuth, aby nawiązać połączenie z usługą Azure AD. Należy włączyć niejawny przepływ dla aplikacji:

    1. Kliknij aplikację, a następnie wybierz pozycję **Manifest**, aby otworzyć edytor manifestu w tekście.
    1. Znajdź właściwość `oauth2AllowImplicitFlow`. Ustaw dla niej wartość `true`.
    1. Kliknij przycisk **Zapisz**, aby zapisać manifest.

1. Udziel uprawnień w całej dzierżawie dla swojej aplikacji. Przejdź kolejno do pozycji **Ustawienia > Wymagane uprawnienia** i wybierz przycisk **Udziel uprawnień** na pasku u góry strony.
1. Wybierz pozycję **Tak**, aby potwierdzić.

## <a name="step-2-install-adal-and-configure-the-single-page-app"></a>Krok 2: Instalowanie biblioteki ADAL i konfigurowanie aplikacji jednostronicowej

Gdy masz już aplikację w usłudze Azure AD, możesz zainstalować bibliotekę adal.js i napisać kod dotyczący tożsamości.

### <a name="configure-the-javascript-client"></a>Konfigurowanie klienta języka JavaScript

Zacznij od dodania biblioteki adal.js do projektu TodoSPA, korzystając z konsoli menedżera pakietów:

1. Pobierz bibliotekę [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) i dodaj ją do katalogu projektu `App/Scripts/`.
2. Pobierz plik [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) i dodaj go do katalogu projektu`App/Scripts/`.
3. Załaduj poszczególne skrypty przed końcem sekcji `</body>` w pliku `index.html`:

    ```js
    ...
    <script src="App/Scripts/adal.js"></script>
    <script src="App/Scripts/adal-angular.js"></script>
    ...
    ```

### <a name="configure-the-back-end-server"></a>Konfigurowanie serwera zaplecza

Aby interfejs API listy zadań zaplecza aplikacji jednostronicowej akceptował tokeny z przeglądarki, należy przekazać do zaplecza informacje konfiguracyjne dotyczące rejestracji aplikacji. W projekcie TodoSPA otwórz plik `web.config`. Zastąp wartości elementów w sekcji `<appSettings>`, aby odzwierciedlały wartości użyte w witrynie Azure Portal. Twój kod będzie odwoływał się do tych wartości podczas każdego użycia biblioteki ADAL.

   * `ida:Tenant` to domena dzierżawy usługi Azure AD — na przykład contoso.onmicrosoft.com.
   * `ida:Audience` to identyfikator klienta Twojej aplikacji skopiowany z portalu.

## <a name="step-3-use-adal-to-help-secure-pages-in-the-single-page-app"></a>Krok 3: Używanie biblioteki ADAL do zabezpieczenia stron w aplikacji jednostronicowej

Biblioteka adal.js integruje się z trasą AngularJS i dostawcami protokołu HTTP, co umożliwia zabezpieczenie indywidualnych widoków w aplikacji jednostronicowej.

1. W obszarze `App/Scripts/app.js` użyj modułu adal.js:

    ```js
    angular.module('todoApp', ['ngRoute','AdalAngular'])
    .config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
     function ($routeProvider, $httpProvider, adalProvider) {
    ...
    ```
2. Zainicjuj polecenie `adalProvider`, korzystając z wartości konfiguracyjnych rejestracji aplikacji, również w lokalizacji `App/Scripts/app.js`:

    ```js
    adalProvider.init(
      {
          instance: 'https://login.microsoftonline.com/',
          tenant: 'Enter your tenant name here e.g. contoso.onmicrosoft.com',
          clientId: 'Enter your client ID here e.g. e9a5a8b6-8af7-4719-9821-0deef255f68e',
          extraQueryParameter: 'nux=1',
          //cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
      },
      $httpProvider
    );
    ```
3. Zabezpiecz widok `TodoList` w aplikacji, używając jednego wiersza kodu: `requireADLogin`.

    ```js
    ...
    }).when("/TodoList", {
            controller: "todoListCtrl",
            templateUrl: "/App/Views/TodoList.html",
            requireADLogin: true,
    ...
    ```

## <a name="summary"></a>Podsumowanie

Masz teraz bezpieczną aplikację jednostronicową, która umożliwia logowanie użytkowników i wysyłanie żądań chronionych przez tokeny elementu nośnego w interfejsie API zaplecza. Gdy użytkownik kliknie link **TodoList**, biblioteka adal.js automatyczne przekieruje go do usługi Azure AD w celu zalogowania, jeśli będzie to konieczne. Co więcej biblioteka adal.js automatycznie dołączy token dostępu do wszystkich żądań Ajax wysyłanych do zaplecza aplikacji.

Opisane czynności to niezbędne minimum konieczne do utworzenia aplikacji jednostronicowej przy użyciu biblioteki adal.js. Jest jednak kilka innych funkcji przydatnych w aplikacji jednostronicowej:

* Aby jawnie wysyłać żądania logowania i wylogowania, możesz określić funkcje w kontrolerach, które wywołują bibliotekę adal.js. W pliku `App/Scripts/homeCtrl.js`:

    ```js
    ...
    $scope.login = function () {
        adalService.login();
    };
    $scope.logout = function () {
        adalService.logOut();
    };
    ...
    ```
* Rozważ zaprezentowanie informacji o użytkowniku w interfejsie użytkownika aplikacji. Usługa ADAL została już dodana do kontrolera `userDataCtrl`, a więc możesz uzyskać dostęp do obiektu `userInfo` w skojarzonym widoku `App/Views/UserData.html`:

    ```js
    <p>{{userInfo.userName}}</p>
    <p>aud:{{userInfo.profile.aud}}</p>
    <p>iss:{{userInfo.profile.iss}}</p>
    ...
    ```

* Istnieje wiele scenariuszy, w których warto wiedzieć, czy użytkownik jest zalogowany. Możesz również użyć obiektu `userInfo` do zebrania tych informacji. Na przykład w pliku `index.html` może być wyświetlany przycisk **Zaloguj** lub **Wyloguj** w zależności od stanu uwierzytelniania:

    ```js
    <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
    <li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    ```

Aplikacja jednostronicowa zintegrowana z usługą Azure AD może uwierzytelniać użytkowników, bezpiecznie wywołać zaplecze, korzystając z protokołu OAuth 2.0, i uzyskiwać podstawowe informacje dotyczące użytkownika. Jeśli jeszcze tego nie zrobiono, warto teraz wypełnić dzierżawę użytkownikami. Uruchom aplikację jednostronicową listy zadań, a następnie zaloguj się przy użyciu jednego z tych użytkowników. Dodaj zadania do listy zadań do wykonania użytkownika, wyloguj się i zaloguj się ponownie.

Biblioteka adal.js ułatwia dodanie typowych funkcji tożsamości do aplikacji. Zajmuje się wszystkimi żmudnymi zadaniami: zarządza pamięcią podręczną, obsługuje protokół OAuth, prezentuje użytkownikom interfejs użytkownika logowania, odświeża wygasłe tokeny i nie tylko.

Gotowy przykład (bez wartości konfiguracji) jest dostępny w repozytorium [GitHub](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).

## <a name="next-steps"></a>Kolejne kroki

Możesz teraz przejść do dodatkowych scenariuszy.

> [!div class="nextstepaction"]
> [Call a CORS web API from a single-page app (Wywoływanie mechanizmu CORS internetowego interfejsu API z poziomu aplikacji jednostronicowej)](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet).
