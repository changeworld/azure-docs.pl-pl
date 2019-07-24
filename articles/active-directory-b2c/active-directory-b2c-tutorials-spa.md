---
title: Samouczek — Włączanie uwierzytelniania w aplikacji jednostronicowej — Azure Active Directory B2C
description: Dowiedz się, jak za pomocą Azure Active Directory B2C zapewnić Logowanie użytkownika dla aplikacji jednostronicowej (JavaScript).
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/24/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 6884cb7b10da3996977f2aea7693625bc45c3139
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/22/2019
ms.locfileid: "68369577"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-using-azure-active-directory-b2c"></a>Samouczek: włączanie uwierzytelniania w aplikacji jednostronicowej przy użyciu usługi Azure Active Directory B2C

W tym samouczku pokazano, jak używać usługi Azure Active Directory (Azure AD) B2C do tworzenia nowych kont i logowania użytkowników w aplikacji jednostronicowej (SPA). Usługa Azure AD B2C umożliwia aplikacjom uwierzytelnianie się na kontach społecznościowych, kontach przedsiębiorstw i kontach usługi Azure Active Directory za pomocą otwartych standardowych protokołów.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Aktualizowanie aplikacji w usłudze Azure AD B2C
> * Konfigurowanie przykładu korzystania z aplikacji
> * Rejestrowanie przy użyciu przepływu użytkownika

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem kroków opisanych w tym samouczku potrzebne są następujące Azure AD B2C zasoby:

* [Dzierżawa Azure AD B2C](tutorial-create-tenant.md)
* [Aplikacja zarejestrowana](tutorial-register-applications.md) w dzierżawie
* [Przepływy użytkowników utworzone](tutorial-create-user-flows.md) w dzierżawie

Ponadto w lokalnym środowisku programistycznym są potrzebne następujące elementy:

* Edytor kodu, na przykład [Visual Studio Code](https://code.visualstudio.com/) lub [Visual Studio 2019](https://www.visualstudio.com/downloads/)
* [Zestaw .NET Core SDK 2,2](https://dotnet.microsoft.com/download) lub nowszy
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Aktualizowanie aplikacji

W drugim samouczku, który został ukończony w ramach wymagań wstępnych, zarejestrowano aplikację sieci Web w Azure AD B2C. Aby umożliwić komunikację z przykładową aplikacją w samouczku, musisz dodać do aplikacji w usłudze Azure AD B2C identyfikator URI przekierowania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Upewnij się, że używasz katalogu zawierającego Twoją dzierżawę usługi Azure AD B2C, klikając pozycję **Filtr katalogu i subskrypcji** w górnym menu i wybierając katalog zawierający Twoją dzierżawę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **Aplikacje**, a następnie wybierz aplikację *webapp1*.
1. W obszarze **Adres URL odpowiedzi** dodaj `http://localhost:6420`.
1. Wybierz pozycję **Zapisz**.
1. Na stronie właściwości Zapisz **Identyfikator aplikacji**. IDENTYFIKATORA aplikacji można użyć w późniejszym kroku podczas aktualizowania kodu w jednostronicowej aplikacji sieci Web.

## <a name="get-the-sample-code"></a>Pobieranie przykładowego kodu

W tym samouczku zostanie skonfigurowany przykładowy kod pobrany z usługi GitHub. W przykładzie pokazano, jak aplikacja jednostronicowa może używać Azure AD B2C do tworzenia konta i logowania użytkowników oraz wywoływania chronionego internetowego interfejsu API.

[Pobierz plik ZIP](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) lub sklonuj przykład z usługi GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>Aktualizowanie przykładu

Teraz, gdy uzyskano przykład, zaktualizuj kod przy użyciu nazwy dzierżawy Azure AD B2C i identyfikatora aplikacji zapisanego w poprzednim kroku.

1. `index.html` Otwórz plik w katalogu głównym przykładowego katalogu.
1. W definicji zmodyfikuj wartość clientId z identyfikatorem aplikacji zapisanym we wcześniejszym kroku.  `msalConfig` Następnie zaktualizuj wartość identyfikatora URI **urzędu** przy użyciu nazwy dzierżawy Azure AD B2C. Zaktualizuj także identyfikator URI przy użyciu nazwy przepływu użytkownika rejestracji/logowania utworzonego w jednym z wymagań wstępnych (na przykład *B2C_1_signupsignin1*).

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

    Nazwa przepływu użytkownika użyta w tym samouczku to **B2C_1_signupsignin1**. Jeśli używasz innej nazwy przepływu użytkownika, podaj tę nazwę w polu `authority` wartość.

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

1. Otwórz okno konsoli i przejdź do katalogu zawierającego przykład. Na przykład:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. Uruchom następujące polecenia:

    ```
    npm install && npm update
    node server.js
    ```

    W oknie konsoli jest wyświetlany numer portu lokalnego uruchomionego serwera Node. js:

    ```
    Listening on port 6420...
    ```

1. Przejdź do `http://localhost:6420` przeglądarki, aby wyświetlić aplikację.

Przykładowa aplikacja obsługuje rejestrację, logowanie, edytowanie profilu i resetowanie hasła. Ten samouczek przedstawia proces logowania użytkownika przy użyciu adresu e-mail.

### <a name="sign-up-using-an-email-address"></a>Rejestrowanie się przy użyciu adresu e-mail

1. Kliknij pozycję **Zaloguj** , aby zainicjować przepływ użytkownika *B2C_1_signupsignin1* określony w poprzednim kroku.
1. Usługa Azure AD B2C wyświetli stronę logowania z linkiem rejestracji. Ponieważ jeszcze nie masz konta, kliknij link **zarejestruj się teraz** .
1. W przepływie pracy rejestracji jest wyświetlana strona do zbierania i weryfikowania tożsamości użytkownika przy użyciu adresu e-mail. Przepływ pracy rejestracji zbiera też hasło użytkownika i żądane atrybuty zdefiniowane w przepływie użytkownika.

    Użyj prawidłowego adresu e-mail i przeprowadź weryfikację przy użyciu kodu weryfikacyjnego. Ustaw hasło. Wprowadź wartości żądanych atrybutów.

    ![Strona rejestracji przedstawiona w przepływie użytkownika logowania/rejestrowania](./media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.PNG)

1. Kliknij pozycję **Utwórz**, aby utworzyć konto lokalne w katalogu usługi Azure AD B2C.

Po kliknięciu przycisku **Utwórz Strona tworzenia**konta zostanie ZAMKNIĘTA, a strona logowania zostanie wyświetlona ponownie.

Teraz możesz zalogować się do aplikacji przy użyciu swojego adresu e-mail i hasła.

### <a name="error-insufficient-permissions"></a>Błąd: niewystarczające uprawnienia

Po zalogowaniu aplikacja wyświetli błąd niewystarczających uprawnień — jest to **oczekiwane**:

```Output
ServerError: AADB2C90205: This application does not have sufficient permissions against this web resource to perform the operation.
Correlation ID: ce15bbcc-0000-0000-0000-494a52e95cd7
Timestamp: 2019-07-20 22:17:27Z
```

Ten błąd występuje, ponieważ aplikacja sieci Web próbuje uzyskać dostęp do internetowego interfejsu API chronionego przez katalog demonstracyjny, *fabrikamb2c*. Ponieważ token dostępu jest prawidłowy tylko dla katalogu usługi Azure AD, wywołanie interfejsu API jest dlatego nieautoryzowane.

Aby naprawić ten błąd, przejdź do następnego samouczka w serii (zobacz [następne kroki](#next-steps)), aby utworzyć chroniony internetowy interfejs API dla katalogu.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Aktualizowanie aplikacji w usłudze Azure AD B2C
> * Konfigurowanie przykładu korzystania z aplikacji
> * Rejestrowanie przy użyciu przepływu użytkownika

Teraz przejdź do następnego samouczka w serii, aby udzielić dostępu do chronionego internetowego interfejsu API przy użyciu SPA:

> [!div class="nextstepaction"]
> [Samouczek: Udzielanie dostępu do ASP.NET Core internetowego interfejsu API z SPA przy użyciu Azure AD B2C >](active-directory-b2c-tutorials-spa-webapi.md)
