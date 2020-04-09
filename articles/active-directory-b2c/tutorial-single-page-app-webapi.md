---
title: 'Samouczek: Ochrona interfejsu API sieci Web node.js przy użyciu usługi Azure AD B2C i udzielanie dostępu do aplikacji jednostronicowej (SPA)'
titleSuffix: Azure AD B2C
description: W tym samouczku dowiesz się, jak chronić internetowy interfejs API usługi Active Directory B2C i wywoływać go z aplikacji jednostronicowej.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 50524159186987b7a30015c878fa3fac949afc79
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875695"
---
# <a name="tutorial-protect-and-grant-access-to-a-nodejs-web-api-from-a-single-page-application-with-azure-ad-b2c"></a>Samouczek: Ochrona i udzielanie dostępu do internetowego interfejsu API node.js za pomocą aplikacji jednostronicowej za pomocą usługi Azure AD B2C

W tym samouczku pokazano, jak wywołać interfejs API sieci Web Node.js chroniony przez usługę Azure Active Directory B2C (Azure AD B2C) z aplikacji jednostronicowej.

W tym samouczku drugi z serii dwuczęściowej:

> [!div class="checklist"]
> * Tworzenie rejestracji aplikacji interfejsu API sieci Web w dzierżawie usługi Azure AD B2C
> * Konfigurowanie zakresów dla internetowego interfejsu API
> * Udzielanie uprawnień do internetowego interfejsu API
> * Modyfikowanie przykładowego kodu interfejsu API sieci Web w celu pracy z dzierżawą

W [pierwszym samouczku](tutorial-single-page-app.md) z tej serii pobrano przykład kodu i zmodyfikowano go, aby zalogować się do użytkowników przy przepływie użytkownika w dzierżawie usługi Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Wykonaj kroki i wymagania wstępne w [samouczku: Włącz uwierzytelnianie w aplikacji jednostronicowej za pomocą usługi Azure AD B2C](tutorial-single-page-app.md)
* [Visual Studio Code](https://code.visualstudio.com/) lub inny edytor kodu
* [Node.js](https://nodejs.org/en/download/)

## <a name="add-a-web-api-application"></a>Dodawanie aplikacji internetowego interfejsu API

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Konfigurowanie zakresów

Zakresy umożliwiają zarządzanie dostępem do chronionych zasobów. Zakresy są używane przez internetowy interfejs API w celu implementowania kontroli dostępu opartej na zakresach. Na przykład niektórzy użytkownicy mogą mieć dostęp do odczytu i zapisu, a inni użytkownicy mogą mieć uprawnienia tylko do odczytu. W tym samouczku można zdefiniować uprawnienia do odczytu i zapisu dla internetowego interfejsu API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Rejestrowanie wartości w obszarze `demo.read` **Zakresy** dla zakresu do użycia w późniejszym kroku podczas konfigurowania aplikacji jednostronicowej. Pełna wartość zakresu jest `https://contosob2c.onmicrosoft.com/api/demo.read`podobna do .

## <a name="grant-permissions"></a>Udzielenie uprawnień

Aby wywołać chroniony internetowy interfejs API z innej aplikacji, należy udzielić tej aplikacji uprawnień do internetowego interfejsu API.

W samouczku warunek wstępny utworzono aplikację sieci web o nazwie *webapp1*. W tym samouczku skonfigurujesz tę aplikację tak, aby wywoływała internetowy interfejs API utworzony w poprzedniej sekcji *webapi1*.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Aplikacja sieci web jednostronicowej uzyskała uprawnienia do chronionego interfejsu API sieci web dla określonych zakresów. Użytkownik uwierzytelnia się za pomocą usługi Azure AD B2C do korzystania z aplikacji jednostronicowej. Aplikacja jednostronicowa używa przepływu udzielania autoryzacji, aby uzyskać dostęp do chronionego interfejsu API sieci web za pomocą tokenu dostępu zwróconego przez usługę Azure AD B2C.

## <a name="configure-the-sample"></a>Konfigurowanie przykładu

Teraz, gdy internetowy interfejs API jest zarejestrowany i zdefiniowano zakresy, skonfiguruj kod interfejsu API sieci web do pracy z dzierżawą usługi Azure AD B2C. W tym samouczku można skonfigurować przykładowy interfejs API sieci Web Node.js pobrany z usługi GitHub.

[Pobierz \*archiwum zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) lub sklonuj przykładowy projekt interfejsu API sieci web z usługi GitHub. Można również przejść bezpośrednio do [projektu Azure-Samples/active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) w usłudze GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="configure-the-web-api"></a>Konfigurowanie internetowego interfejsu API

1. Otwórz plik *config.js* w edytorze kodu.
1. Zmodyfikuj wartości zmiennych, aby odzwierciedlić wartości rejestracji aplikacji utworzonej wcześniej. Zaktualizuj `policyName` również przepływ użytkownika utworzony jako część wymagań wstępnych. Na przykład *B2C_1_signupsignin1*.

    ```javascript
    const clientID = "<your-webapi-application-ID>"; // Application (client) ID
    const b2cDomainHost = "<your-tenant-name>.b2clogin.com";
    const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (a GUID)
    const policyName = "B2C_1_signupsignin1";
    ```

#### <a name="enable-cors"></a>Włączanie mechanizmu CORS

Aby zezwolić aplikacji jednostronicowej na wywołanie internetowego interfejsu API Node.js, należy włączyć [usługę CORS](https://expressjs.com/en/resources/middleware/cors.html) w internetowym interfejsie API. W aplikacji produkcyjnej należy uważać, która domena jest złożenie żądania, ale w tym samouczku, zezwalaj na żądania z dowolnej domeny.

Aby włączyć cors, należy użyć następującego oprogramowania pośredniczącego. W przykładzie kodu interfejsu API sieci Web Node.js w tym samouczku został on już dodany do pliku *index.js.*

```javascript
app.use((req, res, next) => {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "Authorization, Origin, X-Requested-With, Content-Type, Accept");
    next();
});
```

### <a name="configure-the-single-page-application"></a>Konfigurowanie aplikacji jednostronicowej

Aplikacja jednostronicowa (SPA) z [poprzedniego samouczka](tutorial-single-page-app.md) z tej serii używa usługi Azure AD B2C do rejestracji i logowania użytkownika i domyślnie wywołuje internetowy interfejs API Node.js chroniony przez dzierżawę demonstracyjną *fabrikamb2c.*

W tej sekcji zaktualizujesz jednostronicową aplikację sieci web, aby wywołać internetowy interfejs API node.js chroniony *przez* dzierżawę usługi Azure AD B2C (uruchamianą na komputerze lokalnym).

Aby zmienić ustawienia w SPA:

1. W projekcie [active-directory-b2c-javascript-msal-singlepageapp][github-js-spa] pobranym lub sklonowanym w poprzednim samouczku otwórz plik *apiConfig.js* w folderze *JavaScriptSPA.*
1. Skonfiguruj przykład z identyfikatorem URI dla zakresu *demo.read* utworzonego wcześniej i adresu URL internetowego interfejsu API.
    1. W `apiConfig` definicji zastąp `b2cScopes` wartość pełnym identyfikatorem URI dla zakresu *demo.read* (wartość **zakresu,** którą zarejestrowano wcześniej).
    1. Zmień domenę `webApi` w wartości na identyfikator URI przekierowania dodany podczas rejestracji aplikacji interfejsu API sieci web we wcześniejszym kroku.

    Ponieważ interfejs API jest `/hello` dostępny w punkcie końcowym, pozostaw */hello* w identyfikatorze URI.

    Definicja `apiConfig` powinna wyglądać podobnie do następującego bloku kodu, ale z nazwą `<your-tenant-name>`dzierżawy B2C w miejscu:

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/hello" // '/hello' should remain in the URI
    };
    ```

## <a name="run-the-spa-and-web-api"></a>Uruchamianie interfejsu API SPA i sieci Web

Teraz możesz przystąpić do testowania ograniczonego dostępu aplikacji jednostronicowej do interfejsu API. Uruchom zarówno internetowy interfejs API node.js, jak i przykładową aplikację jednostronicową JavaScript na komputerze lokalnym. Następnie zaloguj się do aplikacji jednostronicowej i wybierz przycisk **Interfejsu API połączeń,** aby zainicjować żądanie do chronionego interfejsu API.

Mimo że obie aplikacje są uruchomione lokalnie po wykonaniu tego samouczka, skonfigurowano je do używania usługi Azure AD B2C do bezpiecznego rejestracji/logowania i udzielenia dostępu do chronionego interfejsu API sieci web.

### <a name="run-the-nodejs-web-api"></a>Uruchamianie internetowego interfejsu API node.js

1. Otwórz okno konsoli i zmień katalog zawierający przykład internetowego interfejsu API node.js. Przykład:

    ```console
    cd active-directory-b2c-javascript-nodejs-webapi
    ```

1. Uruchom następujące polecenia:

    ```console
    npm install && npm update
    node index.js
    ```

    W oknie konsoli wyświetlany jest numer portu, w którym znajduje się aplikacja.

    ```console
    Listening on port 5000...
    ```

### <a name="run-the-single-page-app"></a>Uruchamianie aplikacji jednostronicowej

1. Otwórz inne okno konsoli i zmień katalog zawierający przykład spa JavaScript. Przykład:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```

1. Uruchom następujące polecenia:

    ```console
    npm install && npm update
    npm start
    ```

    W oknie konsoli jest wyświetlany numer portu, w którym jest hostowana aplikacja.

    ```console
    Listening on port 6420...
    ```

1. Przejdź `http://localhost:6420` do przeglądarki, aby wyświetlić aplikację.

    ![Przykładowa aplikacja jednostronicowa wyświetlana w przeglądarce](./media/tutorial-single-page-app-webapi/tutorial-01-sample-app-browser.png)

1. Zaloguj się przy użyciu adresu e-mail i hasła użytego w [poprzednim samouczku](tutorial-single-page-app.md). Po pomyślnym zalogowaniu `User 'Your Username' logged-in` powinien zostać wyświetlony komunikat.
1. Wybierz przycisk **Interfejs API połączeń.** Spa uzyskuje udzielić autoryzacji z usługi Azure AD B2C, a następnie uzyskuje dostęp do chronionego interfejsu API sieci web, aby wyświetlić nazwę zalogowanego użytkownika:

    ![Jednostronicowa aplikacja w przeglądarce z wynikiem JSON użytkownika zwróconym przez API](./media/tutorial-single-page-app-webapi/tutorial-02-call-api.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Utworzono rejestrację aplikacji interfejsu API sieci Web w dzierżawie usługi Azure AD B2C
> * Skonfigurowane zakresy dla internetowego interfejsu API
> * Uprawnienia do internetowego interfejsu API
> * Zmodyfikowano przykład kodu interfejsu API sieci Web w celu pracy z dzierżawą

Teraz, gdy widzisz SPA żądania zasobu z chronionego interfejsu API sieci web, uzyskać głębsze zrozumienie, jak te typy aplikacji współdziałają ze sobą i z usługi Azure AD B2C.

> [!div class="nextstepaction"]
> [Typy aplikacji, które mogą być używane w usłudze Active Directory B2C >](application-types.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
