---
title: 'Szybki start: konfigurowanie logowania do aplikacji jednostronicowej (SPA)'
titleSuffix: Azure AD B2C
description: W tym przewodniku Szybki start uruchom przykładową aplikację jednostronicową, która używa usługi Azure Active Directory B2C w celu zapewnienia logowania konta.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.date: 04/04/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0a754873aeafe8d4e7b48d49647469874ff40f7e
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875889"
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>Szybki start: konfigurowanie logowania dla aplikacji jednostronicowej przy użyciu usługi Azure Active Directory B2C

Usługa Azure Active Directory B2C (Azure AD B2C) zapewnia zarządzanie tożsamościami w chmurze w celu ochrony aplikacji, firm i klientów. Usługa Azure AD B2C umożliwia aplikacjom uwierzytelnianie względem kont społecznościowych i firmowych za pomocą protokołów zgodnych z otwartymi standardami. W tym przewodniku Szybki start aplikacja jednostronicowa jest używana do logowania za pomocą dostawcy tożsamości społecznościowych i wywoływania chronionego internetowego interfejsu API usługi Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- [Visual Studio Code](https://code.visualstudio.com/)
- [Node.js](https://nodejs.org/en/download/)
- Konto społecznościowe facebooka, Google lub Microsoftu
- Przykładowy kod z gitHub: [active-directory-b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)

    Możesz [pobrać archiwum zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) lub sklonować repozytorium:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
    ```

## <a name="run-the-application"></a>Uruchamianie aplikacji

1. Uruchom serwer za pomocą następującego polecenia w wierszu polecenia środowiska Node.js:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    npm install && npm update
    npm start
    ```

    Serwer uruchomiony przez *server.js* wyświetla port, na który nasłuchuje:

    ```console
    Listening on port 6420...
    ```

1. Przejdź do adresu URL aplikacji. Na przykład `http://localhost:6420`.

    ![Przykładowa aplikacja jednostronicowa wyświetlana w przeglądarce](./media/quickstart-single-page-app/sample-app-spa.png)

## <a name="sign-in-using-your-account"></a>Logowanie się przy użyciu swojego konta

1. Wybierz **pozycję Zaloguj** się, aby rozpocząć podróż użytkownika.
1. Usługa Azure AD B2C przedstawia stronę logowania dla fikcyjnej firmy o nazwie Fabrikam dla przykładowej aplikacji sieci web. Aby zarejestrować się przy użyciu dostawcy tożsamości społecznościowej, wybierz przycisk dostawcy tożsamości, którego chcesz użyć.

    ![Strona logowania lub rejestracji z przyciskami dostawcy tożsamości](./media/quickstart-single-page-app/sign-in-or-sign-up-spa.png)

    Uwierzytelniasz się (zaloguj się) przy użyciu poświadczeń konta społecznościowego i autoryzujesz aplikację do odczytu informacji z konta społecznościowego. Po udzieleniu dostępu aplikacji może ona pobrać informacje z profilu na koncie w sieci społecznościowej, takie jak Twoje nazwisko i miasto.

1. Zakończ proces logowania dla dostawcy tożsamości.

## <a name="access-a-protected-api-resource"></a>Uzyskiwanie dostępu do chronionego zasobu interfejsu API

Wybierz **polecenie Wywołaj interfejs API,** aby nazwa wyświetlana została zwrócona z internetowego interfejsu API jako obiektu JSON.

![Przykładowa aplikacja w przeglądarce z odpowiedzią na internetowy interfejs API](./media/quickstart-single-page-app/call-api-spa.png)

Przykładowa aplikacja jednostronicowa dołącza token dostępu do żądania wysłanego do chronionego zasobu internetowego interfejsu API.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start użyto przykładowej aplikacji jednostronicowej do:

- Logowanie się za pomocą dostawcy tożsamości społecznościowej
- Tworzenie konta użytkownika usługi Azure AD B2C (utworzonego automatycznie podczas logowania)
- Wywoływanie internetowego interfejsu API chronionego przez usługę Azure AD B2C

Wprowadzenie do tworzenia własnej dzierżawy usługi Azure AD B2C.

> [!div class="nextstepaction"]
> [Tworzenie dzierżawy usługi Azure Active Directory B2C w witrynie Azure Portal](tutorial-create-tenant.md)
