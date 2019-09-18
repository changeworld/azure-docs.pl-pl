---
title: Samouczek — udzielanie dostępu do internetowego interfejsu API platformy ASP.NET — Azure Active Directory B2C | Microsoft Docs
description: Samouczek dotyczący używania usługi Active Directory B2C do chronienia internetowego interfejsu API platformy ASP.NET i wywoływania go z aplikacji internetowej platformy ASP.NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: ec6b667dfc554c037d9b0a56e52bc8f212812812
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064725"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-using-azure-active-directory-b2c"></a>Samouczek: udzielanie dostępu do internetowego interfejsu API platformy ASP.NET przy użyciu usługi Azure Active Directory B2C

W tym samouczku przedstawiono sposób wywoływania chronionego zasobu internetowego interfejsu API w programie Azure Active Directory B2C (Azure AD B2C) z aplikacji sieci Web ASP.NET.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie aplikacji internetowego interfejsu API
> * Konfigurowanie zakresów dla internetowego interfejsu API
> * Udzielanie uprawnień do internetowego interfejsu API
> * Konfigurowanie przykładu korzystania z aplikacji

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki i spełnij wymagania wstępne w [Samouczku: włączanie uwierzytelniania w aplikacji internetowej przy użyciu usługi Azure Active Directory B2C](active-directory-b2c-tutorials-web-app.md).

## <a name="add-a-web-api-application"></a>Dodawanie aplikacji internetowego interfejsu API

Należy zarejestrować zasoby internetowego interfejsu API w dzierżawie, zanim będzie on mógł akceptować i odpowiadać na żądania chronionych zasobów wysyłane przez aplikacje klienckie przedstawiające token dostępu.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz pozycję **Aplikacje**, a następnie wybierz polecenie **Dodaj**.
5. Wprowadź nazwę aplikacji. Na przykład *webapi1*.
6. Dla pozycji **Uwzględnij aplikację internetową/internetowy interfejs API** i **Zezwalaj na niejawny przepływ** wybierz wartość **Tak**.
7. Dla pozycji **Adres URL odpowiedzi** wprowadź punkt końcowy, w którym usługa Azure AD B2C powinna zwracać wszelkie tokeny żądane przez Twoją aplikację. W tym samouczku przykładowa aplikacja jest uruchamiana lokalnie i nasłuchuje na `https://localhost:44332`.
8. Dla pozycji **Identyfikator URI identyfikatora aplikacji** wprowadź identyfikator używany na potrzeby internetowego interfejsu API. Zostanie wygenerowany pełny identyfikator URI łącznie z domeną. Na przykład `https://contosotenant.onmicrosoft.com/api`.
9. Kliknij przycisk **Utwórz**.
10. Na stronie właściwości zapisz identyfikator aplikacji, który będzie używany podczas konfigurowania aplikacji internetowej.

## <a name="configure-scopes"></a>Konfigurowanie zakresów

Zakresy umożliwiają zarządzanie dostępem do chronionych zasobów. Zakresy są używane przez internetowy interfejs API w celu implementowania kontroli dostępu opartej na zakresach. Na przykład użytkownicy internetowego interfejsu API mogą mieć dostęp zarówno do odczytu, jak i zapisu lub tylko dostęp do odczytu. Korzystając z zakresów, w tym samouczku zdefiniujesz uprawnienia do odczytu i zapisu dla internetowego interfejsu API.

1. Wybierz pozycję **Aplikacje**, a następnie wybierz pozycję *webapi1*.
2. Wybierz pozycję **Opublikowane zakresy**.
3. Dla pozycji **zakres** wprowadź `Hello.Read`, a dla opisu wprowadź `Read access to hello`.
4. Dla pozycji **zakres** wprowadź `Hello.Write`, a dla opisu wprowadź `Write access to hello`.
5. Kliknij polecenie **Zapisz**.

Opublikowane zakresy umożliwiają udzielenie aplikacji klienckiej uprawnień do internetowego interfejsu API.

## <a name="grant-permissions"></a>Udzielenie uprawnień

Aby wywoływać chroniony internetowy interfejs API z aplikacji, należy udzielić aplikacji uprawnień do tego interfejsu. W ramach samouczka wymagań wstępnych w usłudze Azure AD B2C została utworzona aplikacja internetowa o nazwie *webapp1*. Tej aplikacji użyjesz do wywołania internetowego interfejsu API.

1. Wybierz pozycję **Aplikacje**, a następnie wybierz pozycję *webapp1*.
2. Wybierz pozycję **Dostęp do interfejsu API**, a następnie wybierz polecenie **Dodaj**.
3. Na liście rozwijanej **Wybierz interfejs API** wybierz pozycję *webapi1*.
4. Na liście rozwijanej **Wybierz zakresy** wybierz zakresy **Hello.Read** i **Hello.Write**, które zostały wcześniej zdefiniowane.
5. Kliknij przycisk **OK**.

Twoja aplikacja została zarejestrowana do wywoływania chronionego internetowego interfejsu API. Użytkownik uwierzytelnia się w usłudze Azure AD B2C, aby korzystać z aplikacji. Aplikacja uzyskuje autoryzację z usługi Azure AD B2C w celu uzyskiwania dostępu do chronionego internetowego interfejsu API.

## <a name="configure-the-sample"></a>Konfigurowanie przykładu

Po zarejestrowaniu internetowego interfejsu API i zdefiniowaniu zakresów konfigurujesz internetowy interfejs API w celu skorzystania ze swojej dzierżawy usługi Azure AD B2C. W tym samouczku skonfigurujesz przykładowy internetowy interfejs API. Przykładowy internetowy interfejs API jest częścią projektu, który pobrano w samouczku wskazanym w wymaganiach wstępnych.

Przykładowe rozwiązanie zawiera dwa projekty:

Przykładowe rozwiązanie zawiera następujące dwa projekty:

- **TaskWebApp** — tworzenie i edytowanie listy zadań. W przykładowej aplikacji jest używany przepływ użytkownika dotyczący **tworzenia konta lub logowania** na potrzeby tworzenia kont lub logowania użytkowników.
- **TaskService** — obsługuje następujące funkcje listy zadań: tworzenie, odczytywanie, aktualizowanie i usuwanie. Interfejs API jest chroniony przez usługę Azure AD B2C i wywoływany przez aplikację TaskWebApp.

### <a name="configure-the-web-application"></a>Konfigurowanie aplikacji internetowej

1. Otwórz rozwiązanie **B2C-WebAPI-DotNet** w programie Visual Studio.
2. Otwórz plik **Web.config** w projekcie **TaskWebApp**.
3. Aby uruchomić interfejs API lokalnie, użyj ustawienia „localhost” dla wartości **api:TaskServiceUrl**. Zmień plik Web.config w następujący sposób:

    ```csharp
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

3. Skonfiguruj identyfikator URI interfejsu API. Ten identyfikator URI jest używany przez aplikację internetową w celu wysyłania żądań do interfejsu API. Ponadto skonfiguruj żądane uprawnienia.

    ```csharp
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/api/" />
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

### <a name="configure-the-web-api"></a>Konfigurowanie internetowego interfejsu API

1. Otwórz plik **Web.config** w projekcie **TaskService**.
2. Skonfiguruj interfejs API w celu korzystania z dzierżawy.

    ```csharp
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

3. Jako identyfikator klienta ustaw zarejestrowany identyfikator aplikacji dla interfejsu API.

    ```csharp
    <add key="ida:ClientId" value="<application-ID>"/>
    ```

4. Zaktualizuj ustawienie przepływu użytkownika, używając nazwy przepływu użytkownika rejestracji i logowania.

    ```csharp
    <add key="ida:SignUpSignInUserFlowId" value="B2C_1_signupsignin1" />
    ```

5. Skonfiguruj ustawienie zakresów tak, aby było zgodne z ustawieniami utworzonymi w portalu.

    ```csharp
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Należy uruchomić zarówno projekt **TaskWebApp**, jak i **TaskService**.

1. W Eksploratorze rozwiązań kliknij rozwiązanie prawym przyciskiem myszy i wybierz pozycję **Ustaw projekty startowe...** .
2. Wybierz pozycję **Ustawianie wielu projektów startowych**.
3. Zmień ustawienie **Akcja** dla obu projektów na wartość **Uruchom**.
4. Kliknij pozycję **OK**, aby zapisać konfigurację.
5. Naciśnij klawisz **F5**, aby uruchomić obie aplikacje. Każda aplikacja zostanie otwarta na własnej karcie przeglądarki. `https://localhost:44316/` to aplikacja internetowa.
    `https://localhost:44332/` to internetowy interfejs API.

6. W aplikacji internetowej kliknij pozycję **rejestracja/logowanie**, aby zalogować się do aplikacji internetowej. Użyj konta, które zostało wcześniej utworzone.
7. Po zalogowaniu kliknij pozycję **Lista zadań do wykonania** i utwórz element listy zadań do wykonania.

Podczas tworzenia elementu listy zadań do wykonania aplikacja internetowa wysyła żądanie do internetowego interfejsu API, aby wygenerować element listy zadań do wykonania. Chroniona aplikacja internetowa wywołuje chroniony internetowy interfejs API w dzierżawie usługi Azure AD B2C.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie aplikacji internetowego interfejsu API
> * Konfigurowanie zakresów dla internetowego interfejsu API
> * Udzielanie uprawnień do internetowego interfejsu API
> * Konfigurowanie przykładu korzystania z aplikacji

> [!div class="nextstepaction"]
> [Samouczek: Dodawanie dostawcy tożsamości do aplikacji w usłudze Azure Active Directory B2C](tutorial-add-identity-providers.md)
