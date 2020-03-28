---
title: 'Samouczek: Włączanie uwierzytelniania w aplikacji jednostronicowej'
titleSuffix: Azure AD B2C
description: W tym samouczku dowiesz się, jak używać usługi Azure Active Directory B2C do udostępniania logowania użytkownika dla aplikacji jednostronicowej opartej na języku JavaScript (SPA).
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/14/2019
ms.custom: mvc, seo-javascript-september2019
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 435800d9c6bfd9131d50681a9808f9836104fac0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78183350"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-using-azure-active-directory-b2c-azure-ad-b2c"></a>Samouczek: Włączanie uwierzytelniania w aplikacji jednostronicowej przy użyciu usługi Azure Active Directory B2C (Azure AD B2C)

W tym samouczku pokazano, jak używać usługi Azure Active Directory B2C (Azure AD B2C) do logowania się i rejestrowania użytkowników w aplikacji jednostronicowej (SPA). Usługa Azure AD B2C umożliwia aplikacjom uwierzytelnianie się na kontach społecznościowych, kontach przedsiębiorstw i kontach usługi Azure Active Directory za pomocą otwartych standardowych protokołów.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Aktualizowanie aplikacji w usłudze Azure AD B2C
> * Konfigurowanie przykładu korzystania z aplikacji
> * Rejestrowanie przy użyciu przepływu użytkownika

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed kontynuowaniem czynności w tym samouczku potrzebne są następujące zasoby usługi Azure AD B2C:

* [Dzierżawa usługi Azure AD B2C](tutorial-create-tenant.md)
* [Aplikacja zarejestrowana](tutorial-register-applications.md) w dzierżawie
* [Przepływy użytkownika utworzone](tutorial-create-user-flows.md) w dzierżawie

Ponadto w lokalnym środowisku programistycznym potrzebne są następujące elementy:

* Edytor kodu, na przykład [Visual Studio Code](https://code.visualstudio.com/) lub Visual Studio [2019](https://www.visualstudio.com/downloads/)
* [.NET Core SDK 2.2 lub nowsza](https://dotnet.microsoft.com/download)
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Aktualizowanie aplikacji

W drugim samouczku, który został ukończony w ramach wymagań wstępnych, zarejestrowano aplikację sieci web w usłudze Azure AD B2C. Aby umożliwić komunikację z przykładową aplikacją w samouczku, musisz dodać do aplikacji w usłudze Azure AD B2C identyfikator URI przekierowania.

Aby zaktualizować aplikację, można użyć bieżącego środowiska **aplikacji** lub naszego nowego środowiska rejestracji aplikacji (Wersja zapoznawcza) w wersji 100 000 000 000 000 000 000 000 000 000 **000** 000 [Dowiedz się więcej na temat nowego środowiska](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplikacje](#tab/applications/)

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C, wybierając filtr **subskrypcja katalog +** w górnym menu i wybierając katalog zawierający dzierżawę.
1. Wybierz **pozycję Wszystkie usługi** w lewym górnym rogu witryny Azure portal, a następnie wyszukaj i wybierz pozycję Azure AD **B2C**.
1. Wybierz pozycję **Aplikacje**, a następnie wybierz aplikację *webapp1*.
1. W obszarze **Adres URL odpowiedzi** dodaj `http://localhost:6420`.
1. Wybierz **pozycję Zapisz**.
1. Na stronie właściwości zarejestruj **identyfikator aplikacji**. Identyfikator aplikacji jest używany w późniejszym kroku podczas aktualizowania kodu w aplikacji sieci web jednostronicowej.

#### <a name="app-registrations-preview"></a>[Rejestracje aplikacji (wersja zapoznawcza)](#tab/app-reg-preview/)

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wybierz filtr **subskrypcja katalog +** w górnym menu, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Możesz też wybrać **wszystkie usługi** i wyszukać i wybrać pozycję Azure **AD B2C**.
1. Wybierz **pozycję Rejestracje aplikacji (Wersja zapoznawcza),** wybierz kartę **Posiadane aplikacje,** a następnie wybierz aplikację *webapp1.*
1. Wybierz **pozycję Uwierzytelnianie**, a następnie wybierz pozycję **Wypróbuj nowe środowisko** (jeśli jest to wyświetlane).
1. W **obszarze Sieć Web**zaznacz łącze Dodaj identyfikator **URI,** wprowadź , `http://localhost:6420`a następnie wybierz pozycję **Zapisz**.
1. Wybierz pozycję **Przegląd**.
1. Zarejestruj **identyfikator aplikacji (klienta)** do użycia w późniejszym kroku podczas aktualizowania kodu w aplikacji sieci web jednostronicowej.

* * *

## <a name="get-the-sample-code"></a>Pobieranie przykładowego kodu

W tym samouczku skonfigurujesz przykładowy kod pobrany z usługi GitHub. W przykładzie pokazano, jak aplikacja jednostronicowa może używać usługi Azure AD B2C do rejestracji i logowania użytkownika oraz do wywoływania chronionego interfejsu API sieci Web.

[Pobierz plik ZIP](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) lub sklonuj przykład z usługi GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>Aktualizowanie próbki

Teraz, po uzyskaniu próbki, zaktualizuj kod o nazwę dzierżawy usługi Azure AD B2C i identyfikator aplikacji, który został zarejestrowany we wcześniejszym kroku.

1. Otwórz `index.html` plik w katalogu głównym przykładowego katalogu.
1. W `msalConfig` definicji zmodyfikuj wartość **clientId** za pomocą identyfikatora aplikacji zarejestrowanego we wcześniejszym kroku. Następnie zaktualizuj wartość **identyfikatora** URI urzędu o nazwę dzierżawy usługi Azure AD B2C. Zaktualizuj również identyfikator URI o nazwę przepływu użytkownika rejestracji/logowania utworzonego w jednym z wymagań wstępnych (na przykład *B2C_1_signupsignin1*).

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "00000000-0000-0000-0000-000000000000", //This is your client ID
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi", //This is your tenant info
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    Nazwa przepływu użytkownika użyta w tym samouczku to **B2C_1_signupsignin1**. Jeśli używasz innej nazwy przepływu użytkownika, określ `authority` tę nazwę w wartości.

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

1. Otwórz okno konsoli i zmień katalog zawierający przykład. Przykład:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. Uruchom następujące polecenia:

    ```
    npm install && npm update
    node server.js
    ```

    W oknie konsoli wyświetlany jest numer portu lokalnie uruchomionego serwera Node.js:

    ```
    Listening on port 6420...
    ```

1. Przejdź `http://localhost:6420` do przeglądarki, aby wyświetlić aplikację.

Przykładowa aplikacja obsługuje rejestrację, logowanie, edytowanie profilu i resetowanie hasła. Ten samouczek przedstawia proces logowania użytkownika przy użyciu adresu e-mail.

### <a name="sign-up-using-an-email-address"></a>Rejestrowanie się przy użyciu adresu e-mail

> [!WARNING]
> Po zarejestrowaniu się lub zalogowaniu może pojawić się [błąd niewystarczających uprawnień.](#error-insufficient-permissions) Ze względu na bieżącą implementację próbki kodu ten błąd jest oczekiwany. Ten problem zostanie rozwiązany w przyszłej wersji przykładowego kodu, w którym to ostrzeżenie zostanie usunięte.

1. Wybierz **pozycję Zaloguj,** aby zainicjować *B2C_1_signupsignin1* przepływ użytkownika określony we wcześniejszym kroku.
1. Usługa Azure AD B2C wyświetli stronę logowania z linkiem rejestracji. Ponieważ nie masz jeszcze konta, wybierz link **Zarejestruj się teraz.**
1. W przepływie pracy rejestracji jest wyświetlana strona do zbierania i weryfikowania tożsamości użytkownika przy użyciu adresu e-mail. Przepływ pracy rejestracji zbiera też hasło użytkownika i żądane atrybuty zdefiniowane w przepływie użytkownika.

    Użyj prawidłowego adresu e-mail i przeprowadź weryfikację przy użyciu kodu weryfikacyjnego. Ustaw hasło. Wprowadź wartości żądanych atrybutów.

    ![Strona rejestracji przedstawiona przez przepływ użytkownika logowania/rejestracji](./media/tutorial-single-page-app/azure-ad-b2c-sign-up-workflow.png)

1. Wybierz **pozycję Utwórz,** aby utworzyć konto lokalne w katalogu usługi Azure AD B2C.

Po wybraniu opcji **Utwórz**strona rejestracji zostanie zamknięta, a strona logowania pojawi się ponownie.

Teraz możesz użyć swojego adresu e-mail i hasła, aby zalogować się do aplikacji.

### <a name="error-insufficient-permissions"></a>Błąd: niewystarczające uprawnienia

Po zalogowaniu się aplikacja może zwrócić błąd niewystarczających uprawnień:

```Output
ServerError: AADB2C90205: This application does not have sufficient permissions against this web resource to perform the operation.
Correlation ID: ce15bbcc-0000-0000-0000-494a52e95cd7
Timestamp: 2019-07-20 22:17:27Z
```

Ten błąd jest odbierany, ponieważ aplikacja internetowa próbuje uzyskać dostęp do internetowego interfejsu API chronionego przez katalog demonstracyjny, *fabrikamb2c*. Ponieważ token dostępu jest prawidłowy tylko dla katalogu usługi Azure AD, wywołanie interfejsu API jest nieautoryzowane.

Aby naprawić ten błąd, przejdź do następnego samouczka z serii (zobacz [Następne kroki),](#next-steps)aby utworzyć chroniony internetowy interfejs API dla katalogu.

## <a name="next-steps"></a>Następne kroki

W tym artykule zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Aktualizowanie aplikacji w usłudze Azure AD B2C
> * Konfigurowanie przykładu korzystania z aplikacji
> * Rejestrowanie przy użyciu przepływu użytkownika

Teraz przejdź do następnego samouczka z serii, aby udzielić dostępu do chronionego interfejsu API sieci web ze SPA:

> [!div class="nextstepaction"]
> [Samouczek: Udzielanie dostępu do ASP.NET core internetowego interfejsu API z SPA przy użyciu usługi Azure AD B2C >](tutorial-single-page-app-webapi.md)
