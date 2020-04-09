---
title: 'Samouczek: Włączanie uwierzytelniania w aplikacji jednostronicowej'
titleSuffix: Azure AD B2C
description: W tym samouczku dowiesz się, jak używać usługi Azure Active Directory B2C do udostępniania logowania użytkownika dla aplikacji jednostronicowej opartej na języku JavaScript (SPA).
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc, seo-javascript-september2019
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: d7cd437f597fc34fe83904715fc2e459dfe4550f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875575"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-with-azure-ad-b2c"></a>Samouczek: Włączanie uwierzytelniania w aplikacji jednostronicowej za pomocą usługi Azure AD B2C

W tym samouczku pokazano, jak używać usługi Azure Active Directory B2C (Azure AD B2C) do rejestrowania się i logowania użytkowników w aplikacji jednostronicowej (SPA).

W tym samouczku, pierwszy z serii dwuczęściowej:

> [!div class="checklist"]
> * Dodawanie adresu URL odpowiedzi do aplikacji zarejestrowanej w dzierżawie usługi Azure AD B2C
> * Pobierz przykładowy kod z usługi GitHub
> * Modyfikowanie kodu przykładowej aplikacji w celu pracy z dzierżawą
> * Zarejestruj się przy użyciu przepływu użytkownika rejestracji/logowania

[Następny samouczek](tutorial-single-page-app-webapi.md) z serii umożliwia część interfejsu API sieci web przykładu kodu.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed kontynuowaniem czynności w tym samouczku potrzebne są następujące zasoby usługi Azure AD B2C:

* [Dzierżawa usługi Azure AD B2C](tutorial-create-tenant.md)
* [Aplikacja zarejestrowana](tutorial-register-applications.md) w dzierżawie
* [Przepływy użytkownika utworzone](tutorial-create-user-flows.md) w dzierżawie

Ponadto w lokalnym środowisku programistycznym potrzebne są następujące elementy:

* [Visual Studio Code](https://code.visualstudio.com/) lub inny edytor kodu
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Aktualizowanie aplikacji

W drugim samouczku, który został ukończony w ramach wymagań wstępnych, zarejestrowano aplikację sieci web w usłudze Azure AD B2C. Aby włączyć komunikację z przykładowym kodem w tym samouczku, dodaj adres URL odpowiedzi (nazywany również identyfikatorem URI przekierowania) do rejestracji aplikacji.

Aby zaktualizować aplikację, można użyć bieżącego środowiska **aplikacji** lub naszego nowego środowiska rejestracji aplikacji (Wersja zapoznawcza) w wersji 100 000 000 000 000 000 000 000 000 000 **000** 000 [Dowiedz się więcej na temat nowego środowiska](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplikacje](#tab/applications/)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C, wybierając filtr **subskrypcja katalog +** w górnym menu i wybierając katalog zawierający dzierżawę.
1. Wybierz **pozycję Wszystkie usługi** w lewym górnym rogu witryny Azure portal, a następnie wyszukaj i wybierz pozycję Azure AD **B2C**.
1. Wybierz pozycję **Aplikacje**, a następnie wybierz aplikację *webapp1*.
1. W obszarze **Adres URL odpowiedzi** dodaj `http://localhost:6420`.
1. Wybierz **pozycję Zapisz**.
1. Na stronie właściwości zarejestruj **identyfikator aplikacji**. Identyfikator aplikacji jest używany w późniejszym kroku podczas aktualizowania kodu w aplikacji sieci web jednostronicowej.

#### <a name="app-registrations-preview"></a>[Rejestracje aplikacji (wersja zapoznawcza)](#tab/app-reg-preview/)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz filtr **subskrypcja katalog +** w górnym menu, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Możesz też wybrać **wszystkie usługi** i wyszukać i wybrać pozycję Azure **AD B2C**.
1. Wybierz **pozycję Rejestracje aplikacji (Wersja zapoznawcza),** wybierz kartę **Posiadane aplikacje,** a następnie wybierz aplikację *webapp1.*
1. Wybierz **pozycję Uwierzytelnianie**, a następnie wybierz pozycję **Wypróbuj nowe środowisko** (jeśli jest to wyświetlane).
1. W **obszarze Sieć Web**zaznacz łącze Dodaj identyfikator **URI,** wprowadź , `http://localhost:6420`a następnie wybierz pozycję **Zapisz**.
1. Wybierz pozycję **Przegląd**.
1. Zarejestruj **identyfikator aplikacji (klienta)** do użycia w późniejszym kroku podczas aktualizowania kodu w aplikacji sieci web jednostronicowej.

* * *

## <a name="get-the-sample-code"></a>Pobieranie przykładowego kodu

W tym samouczku skonfigurujesz przykładowy kod pobrany z usługi GitHub do pracy z dzierżawą B2C. W przykładzie pokazano, jak aplikacja jednostronicowa może używać usługi Azure AD B2C do rejestracji i logowania użytkownika oraz do wywoływania chronionego interfejsu API sieci web (włączyć internetowy interfejs API w następnym samouczku z serii).

[Pobierz plik ZIP](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) lub sklonuj przykład z usługi GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>Aktualizowanie próbki

Teraz, po uzyskaniu próbki, zaktualizuj kod o nazwę dzierżawy usługi Azure AD B2C i identyfikator aplikacji, który został zarejestrowany we wcześniejszym kroku.

1. Otwórz plik *authConfig.js* w folderze *JavaScriptSPA.*
1. W `msalConfig` obiekcie zaktualizuj:
    * `clientId`z wartością z **identyfikatorem aplikacji (klienta)** zarejestrowanym we wcześniejszym kroku
    * `authority`Identyfikator URI z nazwą dzierżawy usługi Azure AD B2C i nazwą przepływu użytkownika rejestracji/logowania utworzonym w ramach wymagań wstępnych (na przykład *B2C_1_signupsignin1)*

    ```javascript
    const msalConfig = {
        auth: {
            clientId: "00000000-0000-0000-0000-000000000000", // Replace this value with your Application (client) ID
            authority: "https://your-b2c-tenant.b2clogin.com/your-b2c-tenant.onmicrosoft.com/B2C_1_signupsignin1", // Update with your tenant and user flow names
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

1. Otwórz okno konsoli i zmień katalog zawierający przykład. Przykład:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. Uruchom następujące polecenia:

    ```console
    npm install && npm update
    npm start
    ```

    W oknie konsoli wyświetlany jest numer portu lokalnie uruchomionego serwera Node.js:

    ```console
    Listening on port 6420...
    ```
1. Przejdź do `http://localhost:6420` celu, aby wyświetlić aplikację sieci web działającą na komputerze lokalnym.

    :::image type="content" source="media/tutorial-single-page-app/web-app-spa-01-not-logged-in.png" alt-text="Przeglądarka internetowa z aplikacją jednostronicową działającą lokalnie":::

### <a name="sign-up-using-an-email-address"></a>Rejestrowanie się przy użyciu adresu e-mail

Ta przykładowa aplikacja obsługuje rejestrację, logowanie i resetowanie hasła. W tym samouczku zarejestruj się przy użyciu adresu e-mail.

1. Wybierz **opcję Zaloguj się,** aby zainicjować *B2C_1_signupsignin1* przepływ użytkownika określony we wcześniejszym kroku.
1. Usługa Azure AD B2C przedstawia stronę logowania zawierającą łącze do rejestracji. Ponieważ nie masz jeszcze konta, wybierz link **Zarejestruj się teraz.**
1. Przepływ pracy rejestracji przedstawia stronę do zbierania i weryfikowania tożsamości użytkownika przy użyciu adresu e-mail. Przepływ pracy rejestracji zbiera również hasło użytkownika i żądane atrybuty zdefiniowane w przepływie użytkownika.

    Użyj prawidłowego adresu e-mail i przeprowadź weryfikację przy użyciu kodu weryfikacyjnego. Ustaw hasło. Wprowadź wartości żądanych atrybutów.

    :::image type="content" source="media/tutorial-single-page-app/user-flow-sign-up-workflow-01.png" alt-text="Strona rejestracji wyświetlana przez przepływ użytkownika usługi Azure AD B2C":::

1. Wybierz **pozycję Utwórz,** aby utworzyć konto lokalne w katalogu usługi Azure AD B2C.

Po wybraniu opcji **Utwórz**aplikacja wyświetla nazwę zalogowanego użytkownika.

:::image type="content" source="media/tutorial-single-page-app/web-app-spa-02-logged-in.png" alt-text="Przeglądarka internetowa z jednostronicową aplikacją z zalogowanym użytkownikiem":::

Jeśli chcesz przetestować logowanie, wybierz przycisk **Wyloguj,** a następnie wybierz pozycję **Zaloguj się** i zaloguj się przy tym przy tym przy tym przy tym przywiązanym do ciebie adresie e-mail i haśle, który został wprowadzony podczas rejestracji.

### <a name="what-about-calling-the-api"></a>Co z wywoływaniem interfejsu API?

Jeśli po zalogowaniu się wybierzesz przycisk **Interfejsu API połączeń,** zamiast wyników wywołania interfejsu API zostanie wyświetlona strona przepływu użytkownika rejestracji/logowania. Jest to oczekiwane, ponieważ nie skonfigurowano jeszcze część interfejsu API aplikacji do komunikowania się z aplikacją interfejsu API sieci web zarejestrowaną w *dzierżawie* usługi Azure AD B2C.

W tym momencie aplikacja nadal próbuje komunikować się z interfejsem API zarejestrowanym w dzierżawie demonstra (fabrikamb2c.onmicrosoft.com), a ponieważ nie są uwierzytelnione z tej dzierżawy, strona rejestracji/logowania jest wyświetlana.

Przejdź do następnego samouczka z serii, aby włączyć chroniony interfejs API (zobacz sekcję [Następne kroki).](#next-steps)

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano aplikację jednostronicową do pracy z przepływem użytkownika w dzierżawie usługi Azure AD B2C w celu zapewnienia możliwości rejestracji i logowania. Wykonane są następujące kroki:

> [!div class="checklist"]
> * Dodano adres URL odpowiedzi do aplikacji zarejestrowanej w dzierżawie usługi Azure AD B2C
> * Pobrano przykład kodu z usługi GitHub
> * Zmodyfikowano kod przykładowej aplikacji do pracy z dzierżawą
> * Rejestracja przy użyciu przepływu użytkownika rejestracji/logowania

Teraz przejdź do następnego samouczka z serii, aby udzielić dostępu do chronionego interfejsu API sieci web ze SPA:

> [!div class="nextstepaction"]
> [Samouczek: Ochrona i udzielanie dostępu do internetowego interfejsu API za pomocą aplikacji jednostronicowej >](tutorial-single-page-app-webapi.md)
