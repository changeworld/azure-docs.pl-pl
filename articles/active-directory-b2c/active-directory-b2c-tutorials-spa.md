---
title: Samouczek — Włączanie uwierzytelniania aplikacji jednostronicowej — Azure Active Directory B2C
description: Dowiedz się, jak używać usługi Azure Active Directory B2C do udostępniania funkcji logowania użytkownika do aplikacji jednostronicowej (JavaScript).
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/08/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 496cf801a44638af61306b43791abce9466e2cb2
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835687"
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

Przed wykonaniem kroków w ramach tego samouczka potrzebne są następujące zasoby usługi Azure AD B2C w miejscu:

* [Dzierżawa usługi Azure AD B2C](tutorial-create-tenant.md)
* [Zarejestrowana aplikacja](tutorial-register-applications.md) w Twojej dzierżawie
* [Przepływy użytkownika utworzone](tutorial-create-user-flows.md) w Twojej dzierżawie

Ponadto użytkownik musi zawierać następujące elementy lokalne Środowisko deweloperskie:

* Na przykład kodu z edytora, [programu Visual Studio Code](https://code.visualstudio.com/) lub [Visual Studio 2019 r.](https://www.visualstudio.com/downloads/)
* [Zestaw SDK platformy .NET core 2.0.0](https://www.microsoft.com/net/core) lub nowszej
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Aktualizowanie aplikacji

W drugim samouczku ukończono jako część wymagań wstępnych zarejestrowano aplikacji sieci web w usłudze Azure AD B2C. Aby umożliwić komunikację z przykładową aplikacją w samouczku, musisz dodać do aplikacji w usłudze Azure AD B2C identyfikator URI przekierowania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Upewnij się, że używasz katalogu zawierającego Twoją dzierżawę usługi Azure AD B2C, klikając pozycję **Filtr katalogu i subskrypcji** w górnym menu i wybierając katalog zawierający Twoją dzierżawę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **Aplikacje**, a następnie wybierz aplikację *webapp1*.
1. W obszarze **Adres URL odpowiedzi** dodaj `http://localhost:6420`.
1. Wybierz pozycję **Zapisz**.
1. Na stronie właściwości rejestrowania **identyfikator aplikacji**. W kolejnym kroku będą użyć Identyfikatora aplikacji, po zaktualizowaniu kodu aplikacji jednej strony sieci web.

## <a name="get-the-sample-code"></a>Pobieranie przykładowego kodu

W tym samouczku skonfigurujesz przykładowy kod, który można pobrać z witryny GitHub. W przykładzie pokazano, jak aplikacja jednostronicowa używać usługi Azure AD B2C do rejestrowania i logowania, a następnie wywoływać chroniony internetowy interfejs API.

[Pobierz plik ZIP](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) lub sklonuj przykład z usługi GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>Aktualizacja przykładu

Teraz, gdy został uzyskany z przykładu, należy zaktualizować kod, za pomocą usługi Azure AD B2C nazwa dzierżawy oraz Identyfikatora aplikacji, zapisane w poprzednim kroku.

1. Otwórz `index.html` plik w folderze głównym katalog przykładu.
1. W `msalConfig` definicji, zmodyfikuj **clientId** wartość Identyfikator aplikacji jest rejestrowane w poprzednim kroku. Następnie zaktualizuj **urząd** wartość identyfikatora URI nazwą dzierżawy usługi Azure AD B2C. Również zaktualizować identyfikatora URI o nazwie utworzony w jednym z wymagań wstępnych przepływ konta-dokonywania/logowania użytkownika (na przykład *B2C_1_signupsignin1*).

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

    Nazwa przepływu użytkownika użyta w tym samouczku to **B2C_1_signupsignin1**. Jeśli używasz innej usługi flow nazwy użytkownika, określ ją w `authority` wartość.

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

    W oknie konsoli zostaną wyświetlone numer portu serwera Node.js uruchomionej lokalnie:

    ```
    Listening on port 6420...
    ```

1. Przejdź do `http://localhost:6420` w przeglądarce, aby wyświetlić aplikację.

Przykładowa aplikacja obsługuje rejestrację, logowanie, edytowanie profilu i resetowanie hasła. Ten samouczek przedstawia proces logowania użytkownika przy użyciu adresu e-mail.

### <a name="sign-up-using-an-email-address"></a>Rejestrowanie się przy użyciu adresu e-mail

1. Kliknij przycisk **Logowanie**, aby zarejestrować się jako użytkownik aplikacji. Ta metoda korzysta z **B2C_1_signupsignin1** przepływ użytkownika określonego w poprzednim kroku.
1. Usługa Azure AD B2C wyświetli stronę logowania z linkiem rejestracji. Ponieważ nie masz jeszcze konta, kliknij link **Sign up now** (Zarejestruj się teraz).
1. W przepływie pracy rejestracji jest wyświetlana strona do zbierania i weryfikowania tożsamości użytkownika przy użyciu adresu e-mail. Przepływ pracy rejestracji zbiera też hasło użytkownika i żądane atrybuty zdefiniowane w przepływie użytkownika.

    Użyj prawidłowego adresu e-mail i przeprowadź weryfikację przy użyciu kodu weryfikacyjnego. Ustaw hasło. Wprowadź wartości żądanych atrybutów.

    ![Strona rejestracji przedstawione przez przepływ logowania — w/rejestracją użytkownika](./media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.PNG)

1. Kliknij pozycję **Utwórz**, aby utworzyć konto lokalne w katalogu usługi Azure AD B2C.

Po kliknięciu **Utwórz**, zamyka logowania stronę w górę i pojawi się ponownie na stronę logowania.

Można teraz używać swój adres e-mail i hasło do logowania do aplikacji.

### <a name="error-insufficient-permissions"></a>Błąd: niewystarczające uprawnienia

Po zalogowaniu, aplikacja wyświetla komunikat o błędzie braku wystarczających uprawnień — jest to **Oczekiwano**:

`ServerError: AADB2C90205: This application does not have sufficient permissions against this web resource to perform the operation.`

Ten błąd jest wyświetlany, ponieważ próbujesz uzyskać dostęp do zasobu z katalogu wersji demonstracyjnej, ale token dostępu jest prawidłowy tylko w przypadku katalogu usługi Azure AD. Wywołanie interfejsu API nie ma związku z tym autoryzacji.

Przejdź do następnego samouczka w serii (zobacz [następne kroki](#next-steps)) aby utworzyć chroniony internetowy interfejs API dla katalogu.

## <a name="next-steps"></a>Następne kroki

W tym artykule zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Aktualizowanie aplikacji w usłudze Azure AD B2C
> * Konfigurowanie przykładu korzystania z aplikacji
> * Rejestrowanie przy użyciu przepływu użytkownika

Teraz przejść do następnego samouczka w serii, aby udzielić dostępu do chronionego internetowego interfejsu API z SPA:

> [!div class="nextstepaction"]
> [Samouczek: udzielanie dostępu do internetowego interfejsu API platformy ASP.NET Core z aplikacji jednostronicowej przy użyciu usługi Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md)
