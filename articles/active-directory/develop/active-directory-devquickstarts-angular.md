---
title: Usługa Azure AD AngularJS, wprowadzenie | Dokumentacja firmy Microsoft
description: Jak utworzyć aplikacja jednostronicowa AngularJS, która integruje się z usługą Azure AD, logowania i wywołuje interfejsy API chroniony przez usługi AD Azure za pomocą protokołu OAuth.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: f2991054-8146-4718-a5f7-59b892230ad7
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 11/30/2017
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 0c7f6a0e447e3b48cdd1df684dc105ece1e98f66
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39502053"
---
# <a name="azure-ad-angularjs-getting-started"></a>Usługa Azure AD AngularJS, rozpoczęcie

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Usługa Azure Active Directory (Azure AD) sprawia, że prostym umożliwiające dodawanie logowania, wylogowywania i bezpiecznego uwierzytelniania OAuth interfejsu API wywołuje do aplikacji jednostronicowej. Umożliwia aplikacji do uwierzytelniania użytkowników za pomocą swoich kont usługi Active Directory systemu Windows Server i korzystania z żadnych internetowy interfejs API usługi Azure AD pomaga chronić, takich jak interfejsy API usługi Office 365 lub interfejsu API platformy Azure.

Dla aplikacji JavaScript w przeglądarce usługa Azure AD zapewnia Active Directory Authentication Library (ADAL), czyli adal.js. Wyłącznie do celów adal.js ma ułatwić uzyskiwanie tokenów dostępu aplikacji. Aby zademonstrować, jak łatwo jest, w tym miejscu utworzymy aplikację AngularJS listy zadań do wykonania który:

* Loguje użytkownika do aplikacji przy użyciu usługi Azure AD jako dostawcy tożsamości.

* Wyświetla informacje dotyczące użytkownika.
* Bezpiecznie wywołuje aplikacji w usłudze wykonaj listę API za pomocą tokenów elementu nośnego z usługi Azure AD.
* Loguje użytkownika z aplikacji.

Aby utworzyć pełną, działającą aplikację, należy:

1. Rejestrowanie aplikacji w usłudze Azure AD.
2. Zainstaluj biblioteki ADAL i skonfiguruj aplikację jednostronicową.
3. Używa biblioteki ADAL, ułatwiające bezpieczne stron w aplikacji jednostronicowej.

Aby rozpocząć pracę, [pobrać szkielet aplikacji](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) lub [Pobierz przykładowe ukończone](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip). Należy również dzierżawę usługi Azure AD może tworzyć użytkowników i zarejestrowanie aplikacji. Jeśli nie masz jeszcze dzierżawy, [Dowiedz się, jak je](quickstart-create-new-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>Krok 1: Rejestrowanie aplikacji DirectorySearcher
Aby umożliwić aplikacji w celu uwierzytelniania użytkowników i uzyskiwanie tokenów, należy najpierw zarejestrować ją w dzierżawie usługi Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Jeśli użytkownik jest zalogowany do wielu katalogów, może być konieczne upewnić się, czy przeglądasz prawidłowy katalog. Aby to zrobić, na górnym pasku kliknij swoje konto. W obszarze **katalogu** wybierz dzierżawę usługi Azure AD, w którym chcesz zarejestrować aplikację.
3. Kliknij przycisk **wszystkich usług** w okienku po lewej stronie, a następnie wybierz **usługi Azure Active Directory**.
4. Kliknij przycisk **rejestracje aplikacji**, a następnie wybierz pozycję **Dodaj**.
5. Postępuj zgodnie z monitami i Utwórz nową aplikację sieci web i/lub interfejs API sieci web:
  * **Nazwa** opisywać aplikację użytkownikom.
  * **Adres URL logowania** to lokalizacja, do którego usługa Azure AD będzie zwracać tokeny. Domyślna lokalizacja dla tego przykładu to `https://localhost:44326/`.
6. Po zakończeniu rejestracji usługi Azure AD przypisuje unikatowy identyfikator aplikacji. Ta wartość będzie potrzebna w kolejnych sekcjach, więc skopiuj ją na karcie aplikacji.
7. Adal.js używa niejawny przepływ OAuth do komunikowania się z usługą Azure AD. Należy włączyć niejawny przepływ aplikacji:
  1. Kliknij aplikację, a następnie wybierz pozycję **manifestu** aby otworzyć Edytor manifestu w tekście.
  2. Znajdź `oauth2AllowImplicitFlow` właściwości. Ustaw dla niego wartość `true`.
  3. Kliknij przycisk **Zapisz** można zapisać manifestu.
8. Przyznaj uprawnienia w dzierżawie dla swojej aplikacji. Przejdź do **ustawienia** > **wymagane uprawnienia**i kliknij przycisk **Udziel uprawnień** przycisku na górnym pasku. Kliknij przycisk **Tak**, aby potwierdzić.

## <a name="step-2-install-adal-and-configure-the-single-page-app"></a>Krok 2: Instalacja biblioteki ADAL i konfigurowanie aplikacji jednostronicowej
Teraz, gdy masz już aplikację w usłudze Azure AD, możesz zainstalować adal.js i pisanie kodu związanych z tożsamościami.

### <a name="configure-the-javascript-client"></a>Konfigurowanie klienta JavaScript
Rozpocznij, dodając do projektu TodoSPA adal.js przy użyciu konsoli Menedżera pakietów:
  1. Pobierz [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) i dodać go do `App/Scripts/` katalogu projektu.
  2. Pobierz [adal angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) i dodać go do `App/Scripts/` katalogu projektu.
  3. Załaduj każdy skrypt przed końcem `</body>` w `index.html`:

    ```js
    ...
    <script src="App/Scripts/adal.js"></script>
    <script src="App/Scripts/adal-angular.js"></script>
    ...
    ```

### <a name="configure-the-back-end-server"></a>Konfigurowanie serwera zaplecza
Aby aplikacja jednostronicowa do czy listy interfejsu API zaplecza mogą zaakceptować tokeny z przeglądarki zaplecza musi informacje o konfiguracji dotyczące rejestracji aplikacji. W projekcie TodoSPA Otwórz `web.config`. Zastąp wartości elementów w `<appSettings>` sekcji, aby odzwierciedlał odpowiednie wartości, których użyto w witrynie Azure portal. Twój kod będzie odwoływać się te wartości w każdym przypadku, gdy używa biblioteki ADAL.
  * `ida:Tenant` jest to domena dzierżawy usługi Azure AD — na przykład contoso.onmicrosoft.com.
  * `ida:Audience` jest to identyfikator klienta Twojej aplikacji, który został skopiowany z portalu.

## <a name="step-3-use-adal-to-help-secure-pages-in-the-single-page-app"></a>Krok 3: Użyj biblioteki ADAL w celu bezpiecznego strony w aplikacji jednostronicowej pomocy
Adal.js integruje się z tras AngularJS i dostawcy protokołu HTTP, więc bezpiecznego pojedyncze widoki mogą pomóc w aplikacji jednostronicowej.

1. W `App/Scripts/app.js`, przesuń w adal.js module:

    ```js
    angular.module('todoApp', ['ngRoute','AdalAngular'])
    .config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
     function ($routeProvider, $httpProvider, adalProvider) {
    ...
    ```
2. Inicjowanie `adalProvider` przy użyciu wartości konfiguracji Twojej rejestracji aplikacji również w `App/Scripts/app.js`:

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
3. Zabezpieczanie `TodoList` widoku w aplikacji przy użyciu tylko jeden wiersz kodu: `requireADLogin`.

    ```js
    ...
    }).when("/TodoList", {
            controller: "todoListCtrl",
            templateUrl: "/App/Views/TodoList.html",
            requireADLogin: true,
    ...
    ```

## <a name="summary"></a>Podsumowanie
Masz teraz bezpiecznej aplikacji jednej strony, który logowania użytkowników i wysyłania żądań chronionego tokenu elementu nośnego do jego interfejsu API zaplecza. Kiedy użytkownik kliknie **TodoList** linku adal.js spowoduje automatyczne przekierowanie do usługi Azure AD podczas logowania w razie potrzeby. Ponadto adal.js będzie automatycznie dołączać token dostępu do wszystkich żądań Ajax, które są wysyłane do zaplecza aplikacji. 

Te czynności są bez minimalnych niezbędnych do tworzenia aplikacji jednostronicowej przy użyciu adal.js. Jednak kilka innych funkcji są przydatne w aplikacji jednostronicowej:

* Aby jawnie wysyłania żądań logowania i wylogowania, można zdefiniować funkcje w kontrolerach, które wywołują adal.js. W `App/Scripts/homeCtrl.js`:

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
* Możesz chcieć prezentowanie informacji o użytkowniku w Interfejsie użytkownika aplikacji. Usługi ADAL został już dodany do `userDataCtrl` kontrolera, aby mogli uzyskiwać dostęp `userInfo` obiekt skojarzony widok `App/Views/UserData.html`:

    ```js
    <p>{{userInfo.userName}}</p>
    <p>aud:{{userInfo.profile.aud}}</p>
    <p>iss:{{userInfo.profile.iss}}</p>
    ...
    ```

* Istnieje wiele scenariuszy, w których warto wiedzieć, jeśli użytkownik jest zalogowany. Można również użyć `userInfo` obiektu do zebrania tych informacji. Na przykład w `index.html`, można wyświetlić **logowania** lub **wylogowania** przycisku na podstawie uwierzytelniania stanu:

    ```js
    <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
    <li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    ```

Jednostronicowej aplikacji platformy Azure zintegrowanych z usługą AD może uwierzytelniać użytkowników, bezpiecznie wywoływać swojej wewnętrznej przy użyciu protokołu OAuth 2.0 i Uzyskaj podstawowe informacje o użytkowniku. Jeśli nie jest jeszcze teraz jest czasu na wypełnienie dzierżawy niektórych użytkowników. Uruchamianie aplikacji jednostronicowej listy zadań do wykonania, a następnie zaloguj się przy użyciu jednego z tych użytkowników. Dodawanie podzadań do listy zadań do wykonania użytkownika, wyloguj się i zaloguj się ponownie.

Adal.js można łatwo zintegrować popularne funkcje tożsamości aplikacji. Ta odpowiada za całą pracę dirty dla Ciebie: Zarządzanie pamięcią podręczną, obsługa protokołu OAuth, prezentowanie użytkownika przy użyciu interfejsu logowania użytkownika, odświeżanie wygasłych tokenów i nie tylko.

Odwołanie, jest dostępna w ukończonej próbka (bez wartości konfiguracji) [GitHub](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).

## <a name="next-steps"></a>Kolejne kroki
Możesz teraz przejść do dodatkowych scenariuszy. Być może chcesz wypróbować: [wywoływanie mechanizmu CORS interfejsu API sieci web z aplikacji jednostronicowej](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
