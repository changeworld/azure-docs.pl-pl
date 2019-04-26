---
title: Samouczek — udzielanie dostępu do internetowego interfejsu API platformy Node.js z aplikacji klasycznej — Azure Active Directory B2C | Microsoft Docs
description: Samouczek dotyczący używania usługi Active Directory B2C do chronienia internetowego interfejsu API platformy Node.js i wywoływania go z aplikacji klasycznej platformy .NET.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 90a6a88ff0dc5aab1163e471b24cd1d00e548a1b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60315936"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Samouczek: Udzielanie dostępu do internetowego interfejsu API platformy Node.js z aplikacji klasycznej przy użyciu usługi Azure Active Directory B2C

W tym samouczku pokazano, jak wywoływać zasób internetowego interfejsu API środowiska Node.js chroniony przez usługę Azure Active Directory (Azure AD) B2C z aplikacji klasycznej systemu Windows Presentation Foundation (WPF).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie aplikacji internetowego interfejsu API
> * Konfigurowanie zakresów dla internetowego interfejsu API
> * Udzielanie uprawnień do internetowego interfejsu API
> * Aktualizacja przykładu korzystania z aplikacji

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki i spełnij wymagania wstępne w [Samouczku: włączanie uwierzytelniania aplikacji klasycznej przy użyciu kont w usłudze Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app.md).

## <a name="add-a-web-api-application"></a>Dodawanie aplikacji internetowego interfejsu API

Należy zarejestrować zasoby internetowego interfejsu API w dzierżawie, zanim będzie on mógł akceptować i odpowiadać na żądania chronionych zasobów wysyłane przez aplikacje klienckie przedstawiające token dostępu. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Upewnij się, że używasz katalogu zawierającego Twoją dzierżawę usługi Azure AD B2C, klikając pozycję **Filtr katalogu i subskrypcji** w górnym menu i wybierając katalog zawierający Twoją dzierżawę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz pozycję **Aplikacje**, a następnie wybierz polecenie **Dodaj**.
5. Wprowadź nazwę aplikacji. Na przykład *webapi1*.
6. Dla pozycji **Uwzględnij aplikację internetową/internetowy interfejs API** i **Zezwalaj na niejawny przepływ** wybierz wartość **Tak**.
7. Dla pozycji **Adres URL odpowiedzi** wprowadź punkt końcowy, w którym usługa Azure AD B2C powinna zwracać wszelkie tokeny żądane przez Twoją aplikację. W tym samouczku przykładowa aplikacja jest uruchamiana lokalnie i nasłuchuje na `https://localhost:5000`.
8. Dla pozycji **Identyfikator URI identyfikatora aplikacji** wprowadź identyfikator używany na potrzeby internetowego interfejsu API. Zostanie wygenerowany pełny identyfikator URI łącznie z domeną. Na przykład `https://contosotenant.onmicrosoft.com/api`.
9. Kliknij pozycję **Utwórz**.
10. Na stronie właściwości zapisz identyfikator aplikacji, który będzie używany podczas konfigurowania aplikacji internetowej.

## <a name="configure-scopes"></a>Konfigurowanie zakresów

Zakresy umożliwiają zarządzanie dostępem do chronionych zasobów. Zakresy są używane przez internetowy interfejs API w celu implementowania kontroli dostępu opartej na zakresach. Na przykład niektórzy użytkownicy mogą mieć dostęp do odczytu i zapisu, a inni użytkownicy mogą mieć uprawnienia tylko do odczytu. W tym samouczku zdefiniujesz uprawnienia do odczytu dla internetowego interfejsu API.

1. Wybierz pozycję **Aplikacje**, a następnie wybierz pozycję *webapi1*.
2. Wybierz pozycję **Opublikowane zakresy**.
3. Dla pozycji **zakres** wprowadź `Hello.Read`, a dla opisu wprowadź `Read access to hello`.
4. Dla pozycji **zakres** wprowadź `Hello.Write`, a dla opisu wprowadź `Write access to hello`.
5. Kliknij pozycję **Zapisz**.

Opublikowane zakresy umożliwiają udzielenie aplikacji klienckiej uprawnień do internetowego interfejsu API.

## <a name="grant-permissions"></a>Udzielenie uprawnień

Aby wywoływać chroniony internetowy interfejs API z aplikacji, należy udzielić aplikacji uprawnień do tego interfejsu. W ramach samouczka wymagań wstępnych w usłudze Azure AD B2C została utworzona aplikacja internetowa o nazwie *app1*. Tej aplikacji użyjesz do wywołania internetowego interfejsu API.

1. Wybierz pozycję **Aplikacje**, a następnie wybierz pozycję *nativeapp1*.
2. Wybierz pozycję **Dostęp do interfejsu API**, a następnie wybierz polecenie **Dodaj**.
3. Na liście rozwijanej **Wybierz interfejs API** wybierz pozycję *webapi1*.
4. Na liście rozwijanej **Wybierz zakresy** wybierz zakresy **Hello.Read** i **Hello.Write**, które zostały wcześniej zdefiniowane.
5. Kliknij przycisk **OK**.

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

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie aplikacji internetowego interfejsu API
> * Konfigurowanie zakresów dla internetowego interfejsu API
> * Udzielanie uprawnień do internetowego interfejsu API
> * Aktualizacja przykładu korzystania z aplikacji

> [!div class="nextstepaction"]
> [Samouczek: Dodawanie dostawcy tożsamości do aplikacji w usłudze Azure Active Directory B2C](tutorial-add-identity-providers.md)
