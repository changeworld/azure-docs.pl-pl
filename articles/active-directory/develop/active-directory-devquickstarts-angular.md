---
title: Azure AD AngularJS, wprowadzenie | Dokumentacja firmy Microsoft
description: Sposób tworzenia aplikacji jednostronicowej AngularJS, która integruje się z usługą Azure AD, logowania i wywołuje Azure interfejsów API, które są chronione przez usługi AD za pomocą uwierzytelniania OAuth.
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
ms.openlocfilehash: 5b99ce605d9ecea6c7d67ab9a2ea679d531787d7
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34156966"
---
# <a name="azure-ad-angularjs-getting-started"></a>Azure AD AngularJS, wprowadzenie

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Azure Active Directory (Azure AD) powoduje, że proste i bezpośrednie dodanie logowania, wylogowywania i bezpieczny OAuth interfejs API wywołuje do aplikacji jednej strony. Umożliwia aplikacji do uwierzytelniania użytkowników przy użyciu ich kont usługi Active Directory systemu Windows Server i korzystać z dowolnej sieci web interfejsu API usługi Azure AD pozwala to chronić, takich jak interfejsami API usługi Office 365 lub interfejsu API platformy Azure.

W przypadku aplikacji JavaScript działającego w przeglądarce usługa Azure AD zapewnia Active Directory Authentication Library (ADAL) lub adal.js. Jest wyłącznie w celu adal.js umożliwia łatwe uzyskanie tokenów dostępu do aplikacji. Aby zademonstrować, jak łatwo jest, w tym miejscu będzie budujemy aplikacji AngularJS listy zadań do wykonania który:

* Loguje użytkownika do aplikacji przy użyciu usługi Azure AD jako dostawcy tożsamości.

* Przedstawia niektóre informacje o użytkowniku.
* Bezpieczne wywołania aplikacji do czy listy interfejsu API za pomocą tokenów elementu nośnego z usługi Azure AD.
* Loguje użytkownika poza aplikacją.

Aby utworzyć pełną, działającą aplikację, musisz:

1. Zarejestrować aplikację w usłudze Azure AD.
2. Instalowanie ADAL i konfigurowanie aplikacji jednej strony.
3. Umożliwia bezpieczne strony w aplikacji jednej strony pomocy biblioteki ADAL.

Aby rozpocząć, [pobrać szkielet aplikacji](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) lub [Pobieranie ukończone próbki](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip). Należy również dzierżawa usługi Azure AD można tworzyć użytkowników i zarejestrowanie aplikacji. Jeśli nie masz już dzierżawę, [Dowiedz się, jak kupić](active-directory-howto-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>Krok 1: Zarejestrować aplikację DirectorySearcher
Aby umożliwić aplikacji w celu uwierzytelniania użytkowników i uzyskać tokeny, należy najpierw zarejestrować ją w dzierżawie usługi Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Jeśli użytkownik jest zalogowany do wielu katalogów, konieczne może być upewnij się, że wyświetlany jest poprawnym katalogu. Aby to zrobić, na górnym pasku kliknij swoje konto. W obszarze **katalogu** wybierz dzierżawy usługi Azure AD, które chcesz zarejestrować aplikację.
3. Kliknij przycisk **wszystkie usługi** w okienku po lewej stronie, a następnie wybierz **usługi Azure Active Directory**.
4. Kliknij przycisk **rejestracji aplikacji**, a następnie wybierz **Dodaj**.
5. Postępuj zgodnie z monitami i utworzyć nową aplikację sieci web i/lub interfejs API sieci web:
  * **Nazwa** opisuje aplikacji dla użytkowników.
  * **Adres URL logowania na** to lokalizacja, do której usługi Azure AD zwróci tokenów. Domyślna lokalizacja dla tego przykładu `https://localhost:44326/`.
6. Po zakończeniu rejestracji usługi Azure AD przypisuje unikatowy identyfikator aplikacji do aplikacji. Ta wartość jest potrzebny w kolejnych sekcjach, dlatego skopiuj go na karcie aplikacji.
7. Adal.js używa niejawnego przepływu OAuth do komunikowania się z usługą Azure AD. Należy włączyć niejawnego przepływu aplikacji:
  1. Kliknij aplikację, a następnie wybierz **manifestu** aby otworzyć Edytor manifestu w tekście.
  2. Zlokalizuj `oauth2AllowImplicitFlow` właściwości. Ustaw dla niego wartość `true`.
  3. Kliknij przycisk **zapisać** można zapisać manifestu.
8. Przyznaj uprawnienia w dzierżawie dla aplikacji. Przejdź do **ustawienia** > **wymagane uprawnienia**i kliknij przycisk **udzielanie uprawnień** przycisk na górnym pasku. Kliknij przycisk **Tak**, aby potwierdzić.

## <a name="step-2-install-adal-and-configure-the-single-page-app"></a>Krok 2: Install ADAL i konfigurowanie aplikacji jednostronicowej
Teraz, gdy masz aplikacji w usłudze Azure AD, można zainstalować adal.js i wpisz swój kod dotyczące tożsamości.

### <a name="configure-the-javascript-client"></a>Konfigurowanie klienta JavaScript
Rozpocznij, dodając adal.js do projektu TodoSPA przy użyciu konsoli Menedżera pakietów:
  1. Pobierz [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) i dodaj go do `App/Scripts/` katalogu projektu.
  2. Pobierz [adal angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) i dodaj go do `App/Scripts/` katalogu projektu.
  3. Ładowanie każdego skryptu przed końcem `</body>` w `index.html`:

    ```js
    ...
    <script src="App/Scripts/adal.js"></script>
    <script src="App/Scripts/adal-angular.js"></script>
    ...
    ```

### <a name="configure-the-back-end-server"></a>Konfigurowanie serwera zaplecza
Dla aplikacji jednej strony zaplecza do czy listy interfejsu API zaakceptować tokeny z przeglądarki wewnętrznej musi informacji konfiguracyjnych dotyczących rejestracji aplikacji. W projekcie TodoSPA Otwórz `web.config`. Zastąp wartości elementów w `<appSettings>` sekcji do wartości, które były używane w portalu Azure. Kod będzie odwoływać tych wartości, przy każdym użyciu biblioteki ADAL.
  * `ida:Tenant` jest to domena dzierżawy usługi Azure AD — na przykład contoso.onmicrosoft.com.
  * `ida:Audience` jest to identyfikator klienta aplikacji, który został skopiowany z portalu.

## <a name="step-3-use-adal-to-help-secure-pages-in-the-single-page-app"></a>Krok 3: Użyj biblioteki ADAL w celu bezpiecznego stron w aplikacji jednej strony
Adal.js integruje się z AngularJS trasy i dostawców HTTP, więc bezpiecznego poszczególnych widoków może pomóc w aplikacji jednej strony.

1. W `App/Scripts/app.js`, umieść w adal.js module:

    ```js
    angular.module('todoApp', ['ngRoute','AdalAngular'])
    .config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
     function ($routeProvider, $httpProvider, adalProvider) {
    ...
    ```
2. Inicjowanie `adalProvider` przy użyciu wartości konfiguracji aplikacji rejestrację, również w `App/Scripts/app.js`:

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
Masz teraz bezpiecznego aplikacji jednej strony, który można zarejestrować użytkowników i wysyłania żądań chronionego tokenu elementu nośnego do jego interfejs API zaplecza. Gdy użytkownik kliknie **TodoList** łącza, adal.js spowoduje automatyczne przekierowanie do usługi Azure AD dla logowania w razie potrzeby. Ponadto adal.js automatycznie dołączą token dostępu do żadnych żądania Ajax, które są wysyłane do zaplecza aplikacji. 

Te czynności są systemu od zera minimalna niezbędnych do tworzenia aplikacji jednej strony przy użyciu adal.js. Jednak kilka innych funkcji są przydatne w aplikacji jednej strony:

* Aby jawnie wysyłania żądań zalogowania się i wylogowania, można zdefiniować funkcje w kontrolerach, które wywołują adal.js. W `App/Scripts/homeCtrl.js`:

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
* Można prezentować informacje o użytkowniku w Interfejsie użytkownika aplikacji. Usługa ADAL został już dodany do `userDataCtrl` kontrolera, więc można uzyskać dostęp do `userInfo` obiektu w widoku skojarzonego `App/Views/UserData.html`:

    ```js
    <p>{{userInfo.userName}}</p>
    <p>aud:{{userInfo.profile.aud}}</p>
    <p>iss:{{userInfo.profile.iss}}</p>
    ...
    ```

* Istnieje wiele scenariuszy, w których należy sprawdzić, czy użytkownik jest zalogowany. Można również użyć `userInfo` obiektu zebranie tych informacji. Na przykład w `index.html`, można wyświetlić **logowania** lub **wylogowania** przycisk na podstawie uwierzytelniania stanu:

    ```js
    <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
    <li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    ```

Azure aplikacji jednostronicowej zintegrowanej z usługą AD może uwierzytelniać użytkowników, bezpiecznie wywołać jej zaplecza przy użyciu protokołu OAuth 2.0 i uzyskać podstawowe informacje o użytkowniku. Jeśli nie jest jeszcze nadszedł czas do wypełnienia dzierżawy z niektórych użytkowników. Uruchamianie aplikacji jednostronicowej listy zadań do wykonania, a następnie zaloguj się przy użyciu jednej z tych użytkowników. Dodać zadania do listy zadań do wykonania użytkownika, wyloguj się i zaloguj się ponownie.

Adal.js można łatwo zastosować wspólne funkcje tożsamości w aplikacji. Zapewnia obsługę pracy dirty, można: Zarządzanie pamięci podręcznej, obsługa protokołu OAuth, przedstawiający użytkownika z logowaniem interfejsu użytkownika, odświeżanie wygaśnięcia tokenów i inne.

Odwołanie, ukończonych próbka (bez wartości konfiguracji) są dostępne w [GitHub](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).

## <a name="next-steps"></a>Kolejne kroki
Możesz teraz przejść do dodatkowe scenariusze. Możesz spróbować: [wywołać mechanizmu CORS interfejsu API sieci web z aplikacji jednej strony](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
