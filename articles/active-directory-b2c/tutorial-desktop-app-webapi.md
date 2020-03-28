---
title: 'Samouczek: Udzielanie dostępu do internetowego interfejsu API node.js z aplikacji klasycznej'
description: Samouczek dotyczący używania usługi Active Directory B2C do ochrony internetowego interfejsu API node.js i wywoływania go z aplikacji klasycznej .NET.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 59670cda68f54e4c0b20b361f0688e6766acba61
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78183390"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Samouczek — udzielanie dostępu do internetowego interfejsu API platformy Node.js z aplikacji klasycznej przy użyciu usługi Azure Active Directory B2C

W tym samouczku pokazano, jak wywołać interfejs API sieci Web Node.js chroniony przez usługę Azure Active Directory B2C (Azure AD B2C) z aplikacji klasycznej Windows Presentation Foundation (WPF), również chronionej przez usługę Azure AD B2C.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie aplikacji internetowego interfejsu API
> * Konfigurowanie zakresów dla internetowego interfejsu API
> * Udzielanie uprawnień do internetowego interfejsu API
> * Aktualizacja przykładu korzystania z aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki i wymagania wstępne w [samouczku: Uwierzytelnianie użytkowników w natywnym kliencie pulpitu](tutorial-desktop-app.md).

## <a name="add-a-web-api-application"></a>Dodawanie aplikacji internetowego interfejsu API

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Konfigurowanie zakresów

Zakresy umożliwiają zarządzanie dostępem do chronionych zasobów. Zakresy są używane przez internetowy interfejs API w celu implementowania kontroli dostępu opartej na zakresach. Na przykład niektórzy użytkownicy mogą mieć dostęp do odczytu i zapisu, a inni użytkownicy mogą mieć uprawnienia tylko do odczytu. W tym samouczku zdefiniujesz uprawnienia do odczytu i zapisu dla internetowego interfejsu API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Zapisz wartość w obszarze `demo.read` **ZAKRESY** dla zakresu do użycia w późniejszym kroku podczas konfigurowania aplikacji klasycznej. Pełna wartość zakresu jest `https://contosob2c.onmicrosoft.com/api/demo.read`podobna do .

## <a name="grant-permissions"></a>Udzielenie uprawnień

Aby wywołać chroniony internetowy interfejs API z natywnej aplikacji klienckiej, należy udzielić uprawnień zarejestrowanej natywnej aplikacji klienckiej do internetowego interfejsu API zarejestrowanego w usłudze Azure AD B2C.

W samouczku wymagania wstępne zarejestrowano natywną aplikację kliencką o nazwie *nativeapp1*. Poniższe kroki konfigurują rejestrację aplikacji natywnej z zakresami interfejsu API udostępnianych dla *webapi1* w poprzedniej sekcji. Dzięki temu aplikacja klasyczna może uzyskać token dostępu z usługi Azure AD B2C, który może używać internetowego interfejsu API do weryfikacji i zapewnienia ograniczonego dostępu do swoich zasobów. Konfigurowanie i uruchamianie zarówno aplikacji klasycznej, jak i przykładów kodu interfejsu API sieci Web w dalszej części samouczka.

#### <a name="applications"></a>[Aplikacje](#tab/applications/)

1. Wybierz pozycję **Aplikacje**, a następnie wybierz pozycję *nativeapp1*.
1. Wybierz pozycję **Dostęp do interfejsu API**, a następnie wybierz polecenie **Dodaj**.
1. Na liście rozwijanej **Wybierz interfejs API** wybierz pozycję *webapi1*.
1. Z listy rozwijanej **Wybierz zakresy** wybierz zakresy zdefiniowane wcześniej. Na przykład *demo.read* i *demo.write*.
1. Kliknij przycisk **OK**.

#### <a name="app-registrations-preview"></a>[Rejestracje aplikacji (wersja zapoznawcza)](#tab/app-reg-preview/)

1. Wybierz **pozycję Rejestracje aplikacji (Wersja zapoznawcza),** a następnie wybierz natywną aplikację kliencką, która powinna mieć dostęp do interfejsu API. Na przykład *nativeapp1*.
1. W obszarze **Zarządzanie**wybierz pozycję **Uprawnienia interfejsu API**.
1. W obszarze **Skonfigurowane uprawnienia**wybierz pozycję **Dodaj uprawnienie**.
1. Wybierz kartę **Moje interfejsy API.**
1. Wybierz interfejs API, do którego natywna aplikacja kliencka powinna mieć dostęp. Na przykład *webapi1*.
1. W obszarze **Uprawnienie**rozwiń **demo**, a następnie wybierz zakresy zdefiniowane wcześniej. Na przykład *demo.read* i *demo.write*.
1. Wybierz pozycję **Dodaj uprawnienia**. Zgodnie z zaleceniami, odczekaj kilka minut, zanim przejdziesz do następnego kroku.
1. Wybierz **pozycję Przyznaj zgodę administratora dla (nazwa dzierżawy)**.
1. Wybierz aktualnie zalogowane konto administratora lub zaloguj się za pomocą konta w dzierżawie usługi Azure AD B2C, do których przypisano co najmniej rolę administratora aplikacji w *chmurze.*
1. Wybierz pozycję **Zaakceptuj**.
1. Wybierz **pozycję Odśwież**, a następnie sprawdź, czy "Przyznane dla ..." pojawia się w obszarze **Stan** dla obu zakresów. Może upłynąć kilka minut, aby uprawnienia do propagacji.

* * *

Użytkownik uwierzytelnia się w usłudze Azure AD B2C, aby korzystać z klasycznej aplikacji WPF. Aplikacja klasyczna uzyskuje autoryzację z usługi Azure AD B2C w celu uzyskiwania dostępu do chronionego internetowego interfejsu API.

## <a name="configure-the-samples"></a>Konfigurowanie przykładów

Teraz, gdy internetowy interfejs API jest zarejestrowany i masz skonfigurowane zakresy i uprawnienia, należy skonfigurować przykłady aplikacji klasycznej i interfejsu API sieci Web do korzystania z dzierżawy usługi Azure AD B2C.

### <a name="update-the-desktop-application"></a>Aktualizowanie aplikacji klasycznej

W warunek wstępny dla tego artykułu zmodyfikowano [aplikacji pulpitu WPF,](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) aby włączyć logowanie się z przepływem użytkownika w dzierżawie usługi Azure AD B2C. W tej sekcji zaktualizujesz tę samą aplikację, aby odwoływać się do internetowego interfejsu API zarejestrowanego wcześniej, *webapi1*.

1. Otwórz rozwiązanie **active-directory-b2c-wpf** (`active-directory-b2c-wpf.sln`) w programie Visual Studio.
1. W projekcie **active-directory-b2c-wpf** otwórz plik *App.xaml.cs* i znajdź następujące definicje zmiennych.
    1. Zastąp `ApiScopes` wartość zmiennej wartością zarejestrowaną wcześniej podczas definiowania zakresu **demo.read.**
    1. Zastąp `ApiEndpoint` wartość zmiennej identyfikatorem **URI przekierowania** zarejestrowanym wcześniej podczas rejestrowania internetowego interfejsu API (na przykład *webapi1)* w dzierżawie.

    Oto przykład:

    ```csharp
    public static string[] ApiScopes = { "https://contosob2c.onmicrosoft.com/api/demo.read" };
    public static string ApiEndpoint = "http://localhost:5000";
    ```

### <a name="get-and-update-the-nodejs-api-sample"></a>Pobieranie i aktualizowanie przykładu interfejsu API node.js

Następnie pobierz przykładowy kod interfejsu API sieci Web Node.js z usługi GitHub i skonfiguruj go tak, aby używał internetowego interfejsu API zarejestrowanego w dzierżawie usługi Azure AD B2C.

[Pobierz plik zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) lub sklonuj przykładową aplikację internetową z usługi GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

Przykładowy internetowy interfejs API platformy Node.js korzysta z biblioteki Passport.js, aby umożliwić usłudze Azure AD B2C ochronę wywołań do interfejsu API.

1. Otwórz plik `index.js`.
1. Zaktualizuj te definicje zmiennych o następujące wartości. Zmień `<web-API-application-ID>` identyfikator **aplikacji (klienta)** internetowego interfejsu API zarejestrowanego wcześniej (*webapi1*). Zmień `<your-b2c-tenant>` nazwę dzierżawy usługi Azure AD B2C.

    ```nodejs
    var clientID = "<web-API-application-ID>";
    var b2cDomainHost = "<your-b2c-tenant>.b2clogin.com";
    var tenantIdGuid = "<your-b2c-tenant>.onmicrosoft.com";
    var policyName = "B2C_1_signupsignin1";
    ```
1. Ponieważ interfejs API jest uruchamiany lokalnie, zaktualizuj `/` ścieżkę w trasie dla metody `/hello`GET, aby zamiast lokalizacji aplikacji demonstracyjnej:

    ```nodejs
    app.get("/",
    ```

## <a name="run-the-samples"></a>Uruchamianie przykładów

### <a name="run-the-nodejs-web-api"></a>Uruchamianie internetowego interfejsu API node.js

1. Uruchom wiersz polecenia platformy Node.js.
2. Przejdź do katalogu zawierającego przykład platformy Node.js. Na przykład `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Uruchom następujące polecenia:
    ```console
    npm install && npm update
    ```
    ```console
    node index.js
    ```

### <a name="run-the-desktop-application"></a>Uruchamianie aplikacji klasycznej

1. Otwórz rozwiązanie **active-directory-b2c-wpf** w programie Visual Studio.
2. Naciśnij klawisz **F5**, aby uruchomić aplikację klasyczną.
3. Zaloguj się przy użyciu adresu e-mail i hasła użytego w [samouczku dotyczącym uwierzytelniania użytkowników za pomocą usługi Azure Active Directory B2C w aplikacji klasycznej](tutorial-desktop-app.md).
4. Wybierz przycisk **Interfejs API połączeń.**

Aplikacja klasyczna sprawia, że żądanie do lokalnie uruchomionego interfejsu API sieci web i po weryfikacji prawidłowego tokenu dostępu, pokazuje nazwę wyświetlaną zalogowanego użytkownika.

![Nazwa wyświetlana wyświetlana w górnym okienku aplikacji klasycznej WPF](./media/tutorial-desktop-app-webapi/desktop-app-01-post-api-call.png)

Aplikacja klasyczna, chroniona przez usługę Azure AD B2C, wywołuje lokalnie uruchomiony internetowy interfejs API, który jest również chroniony przez usługę Azure AD B2C.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie aplikacji internetowego interfejsu API
> * Konfigurowanie zakresów dla internetowego interfejsu API
> * Udzielanie uprawnień do internetowego interfejsu API
> * Aktualizacja przykładu korzystania z aplikacji

> [!div class="nextstepaction"]
> [Samouczek: Dodawanie dostawców tożsamości do aplikacji w usłudze Azure Active Directory B2C](tutorial-add-identity-providers.md)
