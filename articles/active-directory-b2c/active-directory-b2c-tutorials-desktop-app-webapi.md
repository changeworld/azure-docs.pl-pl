---
title: Samouczek — udzielanie dostępu do internetowego interfejsu API platformy Node.js z aplikacji klasycznej przy użyciu usługi Azure Active Directory B2C | Microsoft Docs
description: Samouczek dotyczący używania usługi Active Directory B2C do chronienia internetowego interfejsu API platformy Node.js i wywoływania go z aplikacji klasycznej platformy .NET.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.author: davidmu
ms.date: 3/01/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: 1ce74afd8ee2266439a591730686b4f5277e1b03
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2018
ms.locfileid: "53322009"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Samouczek: Udzielanie dostępu do internetowego interfejsu API platformy Node.js z aplikacji klasycznej przy użyciu usługi Azure Active Directory B2C

W tym samouczku pokazano, jak wywoływać zasób internetowego interfejsu API środowiska Node.js chroniony przez usługę Azure Active Directory (Azure AD) B2C z aplikacji klasycznej systemu Windows Presentation Foundation (WPF).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Rejestrowanie internetowego interfejsu API w dzierżawie usługi Azure AD B2C
> * Definiowanie i konfigurowanie zakresów dla internetowego interfejsu API
> * Udzielanie uprawnień aplikacji do internetowego interfejsu API
> * Aktualizowanie przykładowego kodu w celu chronienia internetowego interfejsu API za pomocą usługi Azure AD B2C

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Wykonaj czynności opisane w [samouczku dotyczącym uwierzytelniania użytkowników za pomocą usługi Azure Active Directory B2C w aplikacji klasycznej](active-directory-b2c-tutorials-desktop-app.md).
* Zainstaluj program [Visual Studio 2017](https://www.visualstudio.com/downloads/) z obciążeniami **Programowanie aplikacji klasycznych dla platformy .NET** oraz **Tworzenie aplikacji na platformie ASP.NET i tworzenie aplikacji internetowych**.
* Zainstalować środowisko [Node.js](https://nodejs.org/en/download/).

## <a name="register-web-api"></a>Rejestrowanie internetowego interfejsu API

Należy zarejestrować zasoby internetowego interfejsu API w dzierżawie, zanim będzie on mógł akceptować i odpowiadać na [żądania chronionych zasobów](../active-directory/develop/developer-glossary.md#resource-server) wysyłane przez [aplikacje klienckie](../active-directory/develop/developer-glossary.md#client-application) przedstawiające [token dostępu](../active-directory/develop/developer-glossary.md#access-token) z usługi Azure Active Directory. Rejestracja powoduje ustanowienie [obiektu aplikacji i jednostki usługi](../active-directory/develop/developer-glossary.md#application-object) w dzierżawie. 

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Wybierz usługę **Azure AD B2C** z listy usług w witrynie Azure Portal.

2. W ustawieniach usługi B2C kliknij pozycję **Aplikacje**, a następnie kliknij pozycję **Dodaj**.

    Aby zarejestrować przykładowy internetowy interfejs API w dzierżawie, użyj następujących ustawień.
    
    ![Dodawanie nowego interfejsu API](media/active-directory-b2c-tutorials-desktop-app-webapi/web-api-registration.png)
    
    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nazwa** | Mój przykładowy internetowy interfejs API platformy Node.js | Wprowadź wartość **Nazwa** opisującą internetowy interfejs API dla deweloperów. |
    | **Uwzględnij aplikację internetową/internetowy interfejs API** | Yes | Wybierz pozycję **Tak** dla internetowego interfejsu API. |
    | **Zezwalaj na niejawny przepływ** | Yes | Wybierz pozycję **Tak**, ponieważ interfejs API używa [logowania OpenID Connect](active-directory-b2c-reference-oidc.md). |
    | **Adres URL odpowiedzi** | `http://localhost:5000` | Adresy URL odpowiedzi to punkty końcowe, do których usługa Azure AD B2C zwraca wszelkie tokeny żądane przez interfejs API. W tym samouczku przykładowy internetowy interfejs API jest uruchamiany lokalnie (localhost) i nasłuchuje na porcie 5000. |
    | **Identyfikator URI identyfikatora aplikacji** | demoapi | Identyfikator URI unikatowo identyfikuje interfejs API w dzierżawie. Dzięki temu można zarejestrować wiele interfejsów API w dzierżawie. [Zakresy](../active-directory/develop/developer-glossary.md#scopes) umożliwiają zarządzanie dostępem do chronionego zasobu interfejsu API i są definiowane dla identyfikatora URI aplikacji. |
    | **Natywny klient** | Nie | Ponieważ jest to internetowy interfejs API, a nie klient natywny, wybierz pozycję Nie. |
    
3. Kliknij pozycję **Utwórz**, aby zarejestrować interfejs API.

Zarejestrowane interfejsy API są wyświetlane na liście aplikacji dla dzierżawy usługi Azure AD B2C. Wybierz swój internetowy interfejs API z listy. Zostanie wyświetlone okienko właściwości interfejsu API.

![Właściwości internetowego interfejsu API](./media/active-directory-b2c-tutorials-web-api/b2c-web-api-properties.png)

Zwróć uwagę na wartość **Identyfikator klienta aplikacji**. Ten identyfikator unikatowo identyfikuje interfejs API i jest potrzebny podczas późniejszego konfigurowania interfejsu API w samouczku.

Zarejestrowanie internetowego interfejsu API w usłudze Azure AD B2C definiuje relację zaufania. Interfejs API zarejestrowany w usłudze B2C może ufać tokenom dostępu B2C otrzymywanym z innych aplikacji.

## <a name="define-and-configure-scopes"></a>Definiowanie i konfigurowanie zakresów

[Zakresy](../active-directory/develop/developer-glossary.md#scopes) umożliwiają zarządzanie dostępem do chronionych zasobów. Zakresy są używane przez internetowy interfejs API w celu implementowania kontroli dostępu opartej na zakresach. Na przykład niektórzy użytkownicy mogą mieć dostęp do odczytu i zapisu, a inni użytkownicy mogą mieć uprawnienia tylko do odczytu. W tym samouczku zdefiniujesz uprawnienia do odczytu i zapisu dla internetowego interfejsu API.

### <a name="define-scopes-for-the-web-api"></a>Definiowanie zakresów dla internetowego interfejsu API

Zarejestrowane interfejsy API są wyświetlane na liście aplikacji dla dzierżawy usługi Azure AD B2C. Wybierz swój internetowy interfejs API z listy. Zostanie wyświetlone okienko właściwości interfejsu API.

Kliknij pozycję **Opublikowane zakresy (wersja zapoznawcza)**.

Aby skonfigurować zakresy dla interfejsu API, dodaj następujące wpisy. 

![zakresy definiowane w internetowym interfejsie API](media/active-directory-b2c-tutorials-web-api/scopes-defined-in-web-api.png)

| Ustawienie      | Sugerowana wartość  | Opis                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Zakres** | demo.read | Dostęp tylko do odczytu do pokazowego interfejsu API|

Kliknij pozycję **Zapisz**.

Opublikowane zakresy umożliwiają udzielenie aplikacji klienckiej uprawnień do internetowego interfejsu API.

### <a name="grant-app-permissions-to-web-api"></a>Udzielanie uprawnień aplikacji do internetowego interfejsu API

Aby wywoływać chroniony internetowy interfejs API z aplikacji, należy udzielić aplikacji uprawnień do tego interfejsu. W tym samouczku użyj aplikacji klasycznej utworzonej w [samouczku dotyczącym uwierzytelniania użytkowników za pomocą usługi Azure Active Directory B2C w aplikacji klasycznej](active-directory-b2c-tutorials-desktop-app.md).

1. W witrynie Azure Portal wybierz pozycję **Azure AD B2C** na liście usług i kliknij pozycję **Aplikacje**, aby wyświetlić listę zarejestrowanych aplikacji.

2. Wybierz pozycję **Moja przykładowa aplikacja WPF** na liście aplikacji i kliknij pozycję **Dostęp do interfejsu API (wersja zapoznawcza)**, a następnie pozycję **Dodaj**.

3. Na liście rozwijanej **Wybierz interfejs API** wybierz zarejestrowany internetowy interfejs API o nazwie **Mój przykładowy internetowy interfejs API platformy Node.js**.

4. Na liście rozwijanej **Wybierz zakresy** wybierz zakresy zdefiniowane podczas rejestracji internetowego interfejsu API.

    ![wybieranie zakresów dla aplikacji](media/active-directory-b2c-tutorials-web-api/selecting-scopes-for-app.png)

5. Kliknij przycisk **OK**.

Aplikacja **Moja przykładowa aplikacja WPF** zostanie zarejestrowana w celu wywoływania chronionego interfejsu **Mój przykładowy internetowy interfejs API platformy Node.js**. Użytkownik [uwierzytelnia się](../active-directory/develop/developer-glossary.md#authentication) w usłudze Azure AD B2C, aby korzystać z klasycznej aplikacji WPF. Aplikacja klasyczna uzyskuje [autoryzację](../active-directory/develop/developer-glossary.md#authorization-grant) z usługi Azure AD B2C w celu uzyskiwania dostępu do chronionego internetowego interfejsu API.

## <a name="update-web-api-code"></a>Aktualizowanie kodu internetowego interfejsu API

Po zarejestrowaniu internetowego interfejsu API i zdefiniowaniu zakresów należy skonfigurować kod internetowego interfejsu API w celu korzystania z dzierżawy usługi Azure AD B2C. W tym samouczku skonfigurujesz przykładową aplikację internetową platformy Node.js, którą można pobrać z witryny GitHub. 

[Pobierz plik zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) lub sklonuj przykładową aplikację internetową z usługi GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```
Przykładowy internetowy interfejs API platformy Node.js korzysta z biblioteki Passport.js, aby umożliwić usłudze Azure AD B2C ochronę wywołań do interfejsu API. 

### <a name="configure-the-web-api"></a>Konfigurowanie internetowego interfejsu API

1. Otwórz plik `index.js` w przykładowym internetowym interfejsie API platformy Node.js.
2. Skonfiguruj przykład obejmujący informacje na temat rejestracji dzierżawy usługi Azure AD B2C. Zmień następujące wiersze kodu:

```nodejs
var tenantID = "<your-tenant-name>.onmicrosoft.com";
var clientID = "<Application ID for your Node.js Web API>";
var policyName = "B2C_1_SiUpIn";  // Sign-in / sign-up policy name
```

### <a name="configure-the-desktop-app"></a>Konfigurowanie aplikacji klasycznej

1. Otwórz rozwiązanie `active-directory-b2c-wpf` z [samouczka dotyczącego uwierzytelniania użytkowników za pomocą usługi Azure Active Directory B2C w aplikacji klasycznej](active-directory-b2c-tutorials-desktop-app.md) w programie Visual Studio.

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Uruchom internetowy interfejs API platformy Node.js:

1. Uruchom wiersz polecenia platformy Node.js.
2. Przejdź do katalogu zawierającego przykład platformy Node.js. Na przykład `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Uruchom następujące polecenia:
    ```
    npm install && npm update
    ```
    ```
    node index.js
    ```
Uruchom aplikację klasyczną:

1. Naciśnij klawisz **F5**, aby uruchomić aplikację klasyczną.
2. Zaloguj się przy użyciu adresu e-mail i hasła użytego w [samouczku dotyczącym uwierzytelniania użytkowników za pomocą usługi Azure Active Directory B2C w aplikacji klasycznej](active-directory-b2c-tutorials-desktop-app.md).
3. Kliknij przycisk **Wywołaj interfejs API**. 

Aplikacja klasyczna zgłasza żądanie do internetowego interfejsu API i uzyskuje odpowiedź z nazwą wyświetlaną zalogowanego użytkownika. Chroniona aplikacja klasyczna wywołuje chroniony internetowy interfejs API w dzierżawie usługi Azure AD B2C.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz wypróbować inne samouczki usługi Azure AD B2C, możesz użyć swojej dzierżawy usługi Azure AD B2C. Możesz [usunąć dzierżawę usługi Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant), gdy nie będzie już potrzebna.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono chronienie internetowego interfejsu API platformy ASP.NET przez rejestrowanie i definiowanie zakresów w usłudze Azure AD B2C. Dowiedz się więcej, zapoznając się z dostępnymi przykładami kodu usługi Azure AD B2C.

> [!div class="nextstepaction"]
> [Przykłady kodu usługi Azure AD B2C](https://azure.microsoft.com/resources/samples/?service=active-directory-b2c&sort=0)
