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
ms.date: 09/12/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a5d4319f47530a91bcceb9b2dba94c6aa8e4c388
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78183894"
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>Szybki start: konfigurowanie logowania dla aplikacji jednostronicowej przy użyciu usługi Azure Active Directory B2C

Usługa Azure Active Directory B2C (Azure AD B2C) zapewnia zarządzanie tożsamościami w chmurze w celu ochrony aplikacji, firm i klientów. Usługa Azure AD B2C umożliwia aplikacjom uwierzytelnianie względem kont społecznościowych i firmowych za pomocą protokołów zgodnych z otwartymi standardami. W tym przewodniku Szybki start aplikacja jednostronicowa jest używana do logowania za pomocą dostawcy tożsamości społecznościowych i wywoływania chronionego internetowego interfejsu API usługi Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) z **obciążeniem ASP.NET i tworzenia stron internetowych**
- [Node.js](https://nodejs.org/en/download/)
- Konto społecznościowe facebooka, Google lub Microsoftu
- Przykładowy kod z gitHub: [active-directory-b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)

    Możesz [pobrać archiwum zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) lub sklonować repozytorium:

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
    ```

## <a name="run-the-application"></a>Uruchamianie aplikacji

1. Uruchom serwer za pomocą następującego polecenia w wierszu polecenia środowiska Node.js:

    ```
    cd active-directory-b2c-javascript-msal-singlepageapp
    npm install && npm update
    node server.js
    ```

    Skrypt Server.js generuje numer portu, którego nasłuchuje na hoście lokalnym.

    ```
    Listening on port 6420...
    ```

2. Przejdź do adresu URL aplikacji. Na przykład `http://localhost:6420`.

## <a name="sign-in-using-your-account"></a>Logowanie się przy użyciu swojego konta

1. Kliknij przycisk **Login** (Zaloguj), aby uruchomić przepływ pracy.

    ![Przykładowa aplikacja jednostronicowa wyświetlana w przeglądarce](./media/quickstart-single-page-app/sample-app-spa.png)

    Przykład obsługuje kilka opcji rejestracji, w tym przy użyciu dostawcy tożsamości dla sieci społecznościowej, lub tworzenia konta lokalnego przy użyciu adresu e-mail. W przypadku tego przewodnika Szybki start użyj konta dostawcy tożsamości społecznościowej z Facebooka, Google lub Firmy Microsoft.

2. Usługa Azure AD B2C przedstawia stronę logowania dla fikcyjnej firmy o nazwie Fabrikam dla przykładowej aplikacji sieci web. Aby zarejestrować się przy użyciu dostawcy tożsamości dla sieci społecznościowej, kliknij przycisk dostawcy tożsamości, którego chcesz użyć.

    ![Strona logowania lub rejestracji z przyciskami dostawcy tożsamości](./media/quickstart-single-page-app/sign-in-or-sign-up-spa.png)

    Uwierzytelniasz się (zaloguj się) przy użyciu poświadczeń konta społecznościowego i autoryzujesz aplikację do odczytu informacji z konta społecznościowego. Po udzieleniu dostępu aplikacji może ona pobrać informacje z profilu na koncie w sieci społecznościowej, takie jak Twoje nazwisko i miasto.

3. Zakończ proces logowania dla dostawcy tożsamości.

## <a name="access-a-protected-api-resource"></a>Uzyskiwanie dostępu do chronionego zasobu interfejsu API

Kliknij przycisk **Call Web API** (Wywołaj internetowy interfejs API), aby uzyskać swoją nazwę wyświetlaną zwróconą jako obiekt JSON z wywołania internetowego interfejsu API.

![Przykładowa aplikacja w przeglądarce z odpowiedzią na internetowy interfejs API](./media/quickstart-single-page-app/call-api-spa.png)

Przykładowa aplikacja jednostronicowa dołącza token dostępu do żądania wysłanego do chronionego zasobu internetowego interfejsu API.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz wypróbować inne przewodniki Szybki start lub samouczki usługi Azure AD B2C, możesz użyć swojej dzierżawy usługi Azure AD B2C. Możesz [usunąć dzierżawę usługi Azure AD B2C](faq.md#how-do-i-delete-my-azure-ad-b2c-tenant), gdy nie będzie już potrzebna.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start użyto przykładowej aplikacji jednostronicowej do:

* Logowanie się przy za pomocą niestandardowej strony logowania
* Logowanie się za pomocą dostawcy tożsamości społecznościowej
* Tworzenie konta usługi Azure AD B2C
* Wywoływanie internetowego interfejsu API chronionego przez usługę Azure AD B2C

Wprowadzenie do tworzenia własnej dzierżawy usługi Azure AD B2C.

> [!div class="nextstepaction"]
> [Tworzenie dzierżawy usługi Azure Active Directory B2C w witrynie Azure Portal](tutorial-create-tenant.md)
