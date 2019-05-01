---
title: Samouczek — włączanie uwierzytelniania w aplikacji jednostronicowej — Azure Active Directory B2C | Microsoft Docs
description: Samouczek dotyczący sposobu użycia usługi Azure Active Directory B2C w celu określenia nazwy logowania użytkownika na potrzeby aplikacji jednostronicowej (JavaScript).
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 9541d635ff69444459470cf1e486568a58af0a1e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64730160"
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

* [Tworzenie przepływów użytkownika](tutorial-create-user-flows.md), aby umieścić platformę użytkownika w Twojej aplikacji. 
* Zainstaluj program [Visual Studio 2017](https://www.visualstudio.com/downloads/) z pakietem roboczym **Tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych**.
* Zainstaluj [zestaw .NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) lub nowszy
* Zainstalować środowisko [Node.js](https://nodejs.org/en/download/).

## <a name="update-the-application"></a>Aktualizowanie aplikacji

W samouczku ukończonym jako część wymagań wstępnych została dodana aplikacja internetowa w usłudze Azure AD B2C. Aby umożliwić komunikację z przykładową aplikacją w samouczku, musisz dodać do aplikacji w usłudze Azure AD B2C identyfikator URI przekierowania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Upewnij się, że używasz katalogu zawierającego Twoją dzierżawę usługi Azure AD B2C, klikając pozycję **Filtr katalogu i subskrypcji** w górnym menu i wybierając katalog zawierający Twoją dzierżawę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz pozycję **Aplikacje**, a następnie wybierz aplikację *webapp1*.
5. W obszarze **Adres URL odpowiedzi** dodaj `http://localhost:6420`.
6. Wybierz pozycję **Zapisz**.
7. Na stronie właściwości zapisz identyfikator aplikacji, który będzie używany podczas konfigurowania aplikacji internetowej.
8. Wybierz pozycję **Klucze**, wybierz opcję **Wygeneruj klucz**, a następnie wybierz pozycję **Zapisz**. Zapisz klucz, którego będziesz używać podczas konfigurowania aplikacji internetowej.

## <a name="configure-the-sample"></a>Konfigurowanie przykładu

W tym samouczku skonfigurujesz przykładową aplikację, którą możesz pobrać z witryny GitHub. Przykładowa aplikacja pokazuje, jak aplikacja jednostronicowa może używać usługi Azure AD B2C na potrzeby tworzenia nowych kont i logowania użytkowników, a także wywoływania chronionego internetowego interfejsu API.

[Pobierz plik ZIP](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) lub sklonuj przykład z usługi GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

Aby zmienić ustawienia:

1. Otwórz plik `index.html` w przykładowej aplikacji.
2. Skonfiguruj przykładową aplikację za pomocą zapisanego wcześniej identyfikatora aplikacji i klucza. Zmień następujące wiersze kodu, zastępując wartości nazwami Twojego katalogu i interfejsów API:

    ```javascript
    // The current application coordinates were pre-registered in a B2C directory.
    var applicationConfig = {
        clientID: '<Application ID>',
        authority: "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://contoso.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://contosohello.azurewebsites.net/hello',
    };
    ```

    Nazwa przepływu użytkownika użyta w tym samouczku to **B2C_1_signupsignin1**. Jeśli używasz innej nazwy przepływu użytkownika, użyj jej w wartości `authority`.

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

1. Uruchom wiersz polecenia platformy Node.js.
2. Przejdź do katalogu zawierającego przykład platformy Node.js. Na przykład `cd c:\active-directory-b2c-javascript-msal-singlepageapp`
3. Uruchom następujące polecenia:

    ```
    npm install && npm update
    node server.js
    ```

    W oknie konsoli jest wyświetlany numer portu, w którym jest hostowana aplikacja.
    
    ```
    Listening on port 6420...
    ```

4. W przeglądarce przejdź do adresu `http://localhost:6420`, aby wyświetlić aplikację.

Przykładowa aplikacja obsługuje rejestrację, logowanie, edytowanie profilu i resetowanie hasła. Ten samouczek przedstawia proces logowania użytkownika przy użyciu adresu e-mail.

### <a name="sign-up-using-an-email-address"></a>Rejestrowanie się przy użyciu adresu e-mail

1. Kliknij przycisk **Logowanie**, aby zarejestrować się jako użytkownik aplikacji. W tym procesie jest używany przepływ użytkownika **B2C_1_signupsignin1** zdefiniowany w poprzednim kroku.
2. Usługa Azure AD B2C wyświetli stronę logowania z linkiem rejestracji. Ponieważ nie masz jeszcze konta, kliknij link **Sign up now** (Zarejestruj się teraz). 
3. W przepływie pracy rejestracji jest wyświetlana strona do zbierania i weryfikowania tożsamości użytkownika przy użyciu adresu e-mail. Przepływ pracy rejestracji zbiera też hasło użytkownika i żądane atrybuty zdefiniowane w przepływie użytkownika.

    Użyj prawidłowego adresu e-mail i przeprowadź weryfikację przy użyciu kodu weryfikacyjnego. Ustaw hasło. Wprowadź wartości żądanych atrybutów. 

    ![Przepływ pracy rejestracji](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. Kliknij pozycję **Utwórz**, aby utworzyć konto lokalne w katalogu usługi Azure AD B2C.

Teraz użytkownik może logować się i korzystać z aplikacji SPA, używając adresu e-mail.

> [!NOTE]
> Po zalogowaniu aplikacja wyświetla błąd „niewystarczające uprawnienia”. Ten błąd jest wyświetlany, ponieważ próbujesz uzyskać dostęp do zasobu z katalogu pokazowego. Ponieważ token dostępu jest prawidłowy tylko dla katalogu usługi Azure AD, wywołanie interfejsu API nie ma autoryzacji. Przejdź do następnego samouczka, aby utworzyć chroniony internetowy interfejs API dla Twojego katalogu.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Aktualizowanie aplikacji w usłudze Azure AD B2C
> * Konfigurowanie przykładu korzystania z aplikacji
> * Rejestrowanie przy użyciu przepływu użytkownika

> [!div class="nextstepaction"]
> [Samouczek: udzielanie dostępu do internetowego interfejsu API platformy ASP.NET Core z aplikacji jednostronicowej przy użyciu usługi Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md)
