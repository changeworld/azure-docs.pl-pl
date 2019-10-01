---
title: Samouczek — udzielanie dostępu do internetowego interfejsu API platformy Node.js z aplikacji klasycznej — Azure Active Directory B2C | Microsoft Docs
description: Samouczek dotyczący używania usługi Active Directory B2C do chronienia internetowego interfejsu API platformy Node.js i wywoływania go z aplikacji klasycznej platformy .NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 8c4b2d818549da07faf9ecd28f61b4ed5315f745
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679321"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Samouczek — udzielanie dostępu do internetowego interfejsu API platformy Node.js z aplikacji klasycznej przy użyciu usługi Azure Active Directory B2C

W tym samouczku pokazano, jak wywoływać zasób internetowego interfejsu API środowiska Node. js Azure Active Directory B2C (Azure AD B2C) z aplikacji klasycznej środowiska Windows Presentation Foundation (WPF).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie aplikacji internetowego interfejsu API
> * Konfigurowanie zakresów dla internetowego interfejsu API
> * Udzielanie uprawnień do internetowego interfejsu API
> * Aktualizacja przykładu korzystania z aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki i wymagania wstępne w [samouczku: Włączanie uwierzytelniania aplikacji klasycznych za pomocą kont przy użyciu Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app.md).

## <a name="add-a-web-api-application"></a>Dodawanie aplikacji internetowego interfejsu API

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Konfigurowanie zakresów

Zakresy umożliwiają zarządzanie dostępem do chronionych zasobów. Zakresy są używane przez internetowy interfejs API w celu implementowania kontroli dostępu opartej na zakresach. Na przykład niektórzy użytkownicy mogą mieć dostęp do odczytu i zapisu, a inni użytkownicy mogą mieć uprawnienia tylko do odczytu. W tym samouczku zdefiniujesz uprawnienia do odczytu dla internetowego interfejsu API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Udzielenie uprawnień

Aby wywoływać chroniony internetowy interfejs API z aplikacji, należy udzielić aplikacji uprawnień do tego interfejsu. W ramach samouczka wymagań wstępnych w usłudze Azure AD B2C została utworzona aplikacja internetowa o nazwie *app1*. Tej aplikacji użyjesz do wywołania internetowego interfejsu API.

1. Wybierz pozycję **Aplikacje**, a następnie wybierz pozycję *nativeapp1*.
1. Wybierz pozycję **Dostęp do interfejsu API**, a następnie wybierz polecenie **Dodaj**.
1. Na liście rozwijanej **Wybierz interfejs API** wybierz pozycję *webapi1*.
1. Z listy rozwijanej **Wybierz zakresy** wybierz zdefiniowane wcześniej zakresy. Na przykład *Demonstracja. odczyt* i *Demonstracja. Write*.
1. Kliknij przycisk **OK**.

Użytkownik uwierzytelnia się w usłudze Azure AD B2C, aby korzystać z klasycznej aplikacji WPF. Aplikacja klasyczna uzyskuje autoryzację z usługi Azure AD B2C w celu uzyskiwania dostępu do chronionego internetowego interfejsu API.

## <a name="configure-the-sample"></a>Konfigurowanie przykładu

Po zarejestrowaniu internetowego interfejsu API i zdefiniowaniu zakresów konfigurujesz kod internetowego interfejsu API w celu skorzystania ze swojej dzierżawy usługi Azure AD B2C. W tym samouczku skonfigurujesz przykładową aplikację internetową platformy Node.js, którą można pobrać z witryny GitHub.

[Pobierz plik zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) lub sklonuj przykładową aplikację internetową z usługi GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

Przykładowy internetowy interfejs API platformy Node.js korzysta z biblioteki Passport.js, aby umożliwić usłudze Azure AD B2C ochronę wywołań do interfejsu API.

1. Otwórz plik `index.js`.
2. Skonfiguruj przykład obejmujący informacje na temat rejestracji dzierżawy usługi Azure AD B2C. Zmień następujące wiersze kodu:

    ```nodejs
    var tenantID = "<your-tenant-name>.onmicrosoft.com";
    var clientID = "<application-ID>";
    var policyName = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

1. Uruchom wiersz polecenia platformy Node.js.
2. Przejdź do katalogu zawierającego przykład platformy Node.js. Na przykład `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Uruchom następujące polecenia:
    ```
    npm install && npm update
    ```
    ```
    node index.js
    ```

### <a name="run-the-desktop-application"></a>Uruchamianie aplikacji klasycznej

1. Otwórz rozwiązanie **active-directory-b2c-wpf** w programie Visual Studio.
2. Naciśnij klawisz **F5**, aby uruchomić aplikację klasyczną.
3. Zaloguj się przy użyciu adresu e-mail i hasła użytego w [samouczku dotyczącym uwierzytelniania użytkowników za pomocą usługi Azure Active Directory B2C w aplikacji klasycznej](active-directory-b2c-tutorials-desktop-app.md).
4. Kliknij przycisk **Wywołaj interfejs API**.

Aplikacja klasyczna zgłasza żądanie do internetowego interfejsu API i uzyskuje odpowiedź z nazwą wyświetlaną zalogowanego użytkownika. Chroniona aplikacja klasyczna wywołuje chroniony internetowy interfejs API w dzierżawie usługi Azure AD B2C.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie aplikacji internetowego interfejsu API
> * Konfigurowanie zakresów dla internetowego interfejsu API
> * Udzielanie uprawnień do internetowego interfejsu API
> * Aktualizacja przykładu korzystania z aplikacji

> [!div class="nextstepaction"]
> [Samouczek: Dodawanie dostawców tożsamości do aplikacji w Azure Active Directory B2C](tutorial-add-identity-providers.md)
