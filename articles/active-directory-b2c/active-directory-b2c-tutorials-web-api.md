---
title: Samouczek — udzielanie dostępu do internetowego interfejsu API platformy ASP.NET z aplikacji internetowej przy użyciu usługi Azure Active Directory B2C | Microsoft Docs
description: Samouczek dotyczący używania usługi Active Directory B2C do chronienia internetowego interfejsu API platformy ASP.NET i wywoływania go z aplikacji internetowej platformy ASP.NET.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 11/30/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: 4051c0438cfb7c33f36cf49542e422e54e723cfc
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856064"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-from-a-web-app-using-azure-active-directory-b2c"></a>Samouczek: udzielanie dostępu do internetowego interfejsu API platformy ASP.NET z aplikacji internetowej przy użyciu usługi Azure Active Directory B2C

W tym samouczku pokazano, jak wywoływać zasób internetowego interfejsu API chronionego przez usługę Azure Active Directory (Azure AD) B2C z aplikacji internetowej platformy ASP.NET.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Rejestrowanie internetowego interfejsu API w dzierżawie usługi Azure AD B2C
> * Definiowanie i konfigurowanie zakresów dla internetowego interfejsu API
> * Udzielanie uprawnień aplikacji do internetowego interfejsu API
> * Aktualizowanie przykładowego kodu w celu chronienia internetowego interfejsu API za pomocą usługi Azure AD B2C

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Ukończ [samouczek dotyczący uwierzytelniania użytkowników za pomocą usługi Azure Active Directory B2C w aplikacji internetowej platformy ASP.NET](active-directory-b2c-tutorials-web-app.md).
* Zainstaluj program [Visual Studio 2017](https://www.visualstudio.com/downloads/) z pakietem roboczym **Tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych**.

## <a name="register-web-api"></a>Rejestrowanie internetowego interfejsu API

Należy zarejestrować zasoby internetowego interfejsu API w dzierżawie, zanim będzie on mógł akceptować i odpowiadać na [żądania chronionych zasobów](../active-directory/develop/developer-glossary.md#resource-server) wysyłane przez [aplikacje klienckie](../active-directory/develop/developer-glossary.md#client-application) przedstawiające [token dostępu](../active-directory/develop/developer-glossary.md#access-token) z usługi Azure Active Directory. Rejestracja powoduje ustanowienie [obiektu aplikacji i jednostki usługi](../active-directory/develop/developer-glossary.md#application-object) w dzierżawie. 

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**. Teraz powinna być używana dzierżawa utworzona w poprzednim samouczku.

2. Wybierz pozycję **Aplikacje**, a następnie wybierz polecenie **Dodaj**.

    Aby zarejestrować przykładowy internetowy interfejs API w dzierżawie, użyj następujących ustawień.
    
    ![Dodawanie nowego interfejsu API](./media/active-directory-b2c-tutorials-web-api/web-api-registration.png)
    
    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nazwa** | Mój przykładowy internetowy interfejs API | Wprowadź wartość **Nazwa** opisującą internetowy interfejs API dla deweloperów. |
    | **Uwzględnij aplikację internetową/internetowy interfejs API** | Yes | Wybierz pozycję **Tak** dla internetowego interfejsu API. |
    | **Zezwalaj na niejawny przepływ** | Yes | Wybierz pozycję **Tak**, ponieważ interfejs API używa [logowania OpenID Connect](active-directory-b2c-reference-oidc.md). |
    | **Adres URL odpowiedzi** | `https://localhost:44332` | Adresy URL odpowiedzi to punkty końcowe, do których usługa Azure AD B2C zwraca wszelkie tokeny żądane przez interfejs API. W tym samouczku przykładowy internetowy interfejs API jest uruchamiany lokalnie (localhost) i nasłuchuje na porcie 44332. |
    | **Identyfikator URI identyfikatora aplikacji** | myAPISample | Identyfikator URI unikatowo identyfikuje interfejs API w dzierżawie. Dzięki temu można zarejestrować wiele interfejsów API w dzierżawie. [Zakresy](../active-directory/develop/developer-glossary.md#scopes) umożliwiają zarządzanie dostępem do chronionego zasobu interfejsu API i są definiowane dla identyfikatora URI aplikacji. |
    | **Natywny klient** | Nie | Ponieważ jest to internetowy interfejs API, a nie klient natywny, wybierz pozycję Nie. |
    
3. Kliknij pozycję **Utwórz**, aby zarejestrować interfejs API.

Zarejestrowane interfejsy API są wyświetlane na liście aplikacji dla dzierżawy usługi Azure AD B2C. Wybierz swój internetowy interfejs API z listy. Zostanie wyświetlone okienko właściwości interfejsu API.

![Właściwości internetowego interfejsu API](./media/active-directory-b2c-tutorials-web-api/b2c-web-api-properties.png)

Zwróć uwagę na wartość **Identyfikator klienta aplikacji**. Ten identyfikator unikatowo identyfikuje interfejs API i jest potrzebny podczas późniejszego konfigurowania interfejsu API w samouczku.

Zarejestrowanie internetowego interfejsu API w usłudze Azure AD B2C definiuje relację zaufania. Interfejs API zarejestrowany w usłudze B2C może ufać tokenom dostępu B2C otrzymywanym z innych aplikacji.

## <a name="define-and-configure-scopes"></a>Definiowanie i konfigurowanie zakresów

[Zakresy](../active-directory/develop/developer-glossary.md#scopes) umożliwiają zarządzanie dostępem do chronionych zasobów. Zakresy są używane przez internetowy interfejs API w celu implementowania kontroli dostępu opartej na zakresach. Na przykład użytkownicy internetowego interfejsu API mogą mieć dostęp zarówno do odczytu, jak i zapisu lub tylko dostęp do odczytu. Korzystając z zakresów, w tym samouczku zdefiniujesz uprawnienia do odczytu i zapisu dla internetowego interfejsu API.

### <a name="define-scopes-for-the-web-api"></a>Definiowanie zakresów dla internetowego interfejsu API

Zarejestrowane interfejsy API są wyświetlane na liście aplikacji dla dzierżawy usługi Azure AD B2C. Wybierz swój internetowy interfejs API z listy. Zostanie wyświetlone okienko właściwości interfejsu API.

Kliknij pozycję **Opublikowane zakresy (wersja zapoznawcza)**.

Aby skonfigurować zakresy dla interfejsu API, dodaj następujące wpisy. 

![zakresy definiowane w internetowym interfejsie API](media/active-directory-b2c-tutorials-web-api/scopes-defined-in-web-api.png)

| Ustawienie      | Sugerowana wartość  | Opis                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Zakres** | Hello.Read | Dostęp do odczytu do folderu hello |
| **Zakres** | Hello.Write | Dostęp do zapisu do folderu hello |

Kliknij pozycję **Zapisz**.

Opublikowane zakresy umożliwiają udzielenie aplikacji klienckiej uprawnień do internetowego interfejsu API.

### <a name="grant-app-permissions-to-web-api"></a>Udzielanie uprawnień aplikacji do internetowego interfejsu API

Aby wywoływać chroniony internetowy interfejs API z aplikacji, należy udzielić aplikacji uprawnień do tego interfejsu. W tym samouczku użyj aplikacji utworzonej w [samouczku dotyczącym uwierzytelniania użytkowników za pomocą usługi Azure Active Directory B2C w aplikacji internetowej platformy ASP.NET](active-directory-b2c-tutorials-web-app.md). 

1. W witrynie Azure Portal wybierz pozycję **Azure AD B2C** na liście usług i kliknij pozycję **Aplikacje**, aby wyświetlić listę zarejestrowanych aplikacji.

2. Wybierz pozycję **Moja przykładowa aplikacja internetowa** na liście aplikacji i kliknij pozycję **Dostęp do interfejsu API (wersja zapoznawcza)**, a następnie pozycję **Dodaj**.

3. Na liście rozwijanej **Wybierz interfejs API** wybierz zarejestrowany internetowy interfejs API o nazwie **Mój przykładowy internetowy interfejs API**.

4. Na liście rozwijanej **Wybierz zakresy** wybierz zakresy zdefiniowane podczas rejestracji internetowego interfejsu API.

    ![wybieranie zakresów dla aplikacji](media/active-directory-b2c-tutorials-web-api/selecting-scopes-for-app.png)

5. Kliknij przycisk **OK**.

Aplikacja **Moja przykładowa aplikacja internetowa** zostanie zarejestrowana w celu wywoływania chronionego interfejsu **Mój przykładowy internetowy interfejs API**. Użytkownik [uwierzytelnia się](../active-directory/develop/developer-glossary.md#authentication) w usłudze Azure AD B2C, aby korzystać z aplikacji internetowej. Aplikacja internetowa uzyskuje [autoryzację](../active-directory/develop/developer-glossary.md#authorization-grant) z usługi Azure AD B2C w celu uzyskiwania dostępu do chronionego internetowego interfejsu API.

## <a name="update-code"></a>Aktualizowanie kodu

Po zarejestrowaniu internetowego interfejsu API i zdefiniowaniu zakresów należy skonfigurować kod internetowego interfejsu API w celu korzystania z dzierżawy usługi Azure AD B2C. W tym samouczku skonfigurujesz przykładowy internetowy interfejs API. 

Przykładowy internetowy interfejs API jest częścią projektu, który pobrano w samouczku wskazanym w wymaganiach wstępnych: [Samouczek dotyczący uwierzytelniania użytkowników za pomocą usługi Azure Active Directory B2C w aplikacji internetowej platformy ASP.NET](active-directory-b2c-tutorials-web-app.md) Przed kontynuowaniem ukończ samouczek określony w wymaganiach wstępnych, jeśli nie zostało to jeszcze zrobione.

Przykładowe rozwiązanie zawiera dwa projekty:

**Przykładowa aplikacja internetowa (TaskWebApp):** Aplikacja internetowa do tworzenia i edytowania listy zadań. Ta aplikacja internetowa używa przepływu użytkownika **rejestracja lub logowanie**, aby rejestrować lub logować użytkowników za pomocą adresu e-mail.

**Przykładowa aplikacja internetowego interfejsu API (TaskService):** Internetowy interfejs API, który obsługuje następujące funkcje listy zadań: tworzenie, odczytywanie, aktualizowanie i usuwanie. Internetowy interfejs API jest chroniony przez usługę Azure AD B2C i wywoływany przez aplikację internetową.

Przykładowa aplikacja internetowa i internetowy interfejs API definiują wartości konfiguracji jako ustawienia aplikacji w pliku Web.config w każdym projekcie.

Otwórz rozwiązanie **B2C-WebAPI-DotNet** w programie Visual Studio.

### <a name="configure-the-web-app"></a>Konfigurowanie aplikacji internetowej

1. Otwórz plik **Web.config** w projekcie **TaskWebApp**.

2. Aby uruchomić interfejs API lokalnie, użyj ustawienia „localhost” dla wartości **api:TaskServiceUrl**. Zmień plik Web.config w następujący sposób: 

    ```C#
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

3. Skonfiguruj identyfikator URI interfejsu API. Ten identyfikator URI jest używany przez aplikację internetową w celu wysyłania żądań do interfejsu API. Ponadto skonfiguruj żądane uprawnienia.

    ```C#
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/myAPISample/" />
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

### <a name="configure-the-web-api"></a>Konfigurowanie internetowego interfejsu API

1. Otwórz plik **Web.config** w projekcie **TaskService**.

2. Skonfiguruj interfejs API w celu korzystania z dzierżawy.

    ```C#
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

3. Jako identyfikator klienta ustaw zarejestrowany identyfikator aplikacji dla interfejsu API.

    ```C#
    <add key="ida:ClientId" value="<The Application ID for your web API obtained from the Azure portal>"/>
    ```

4. Zaktualizuj ustawienie przepływu użytkownika, używając nazwy wygenerowanej podczas tworzenia przepływu użytkownika rejestracji i logowania.

    ```C#
    <add key="ida:SignUpSignInUserFlowId" value="B2C_1_SiUpIn" />
    ```

5. Skonfiguruj ustawienie zakresów tak, aby było zgodne z ustawieniami utworzonymi w portalu.

    ```C#
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Należy uruchomić zarówno projekt **TaskWebApp**, jak i **TaskService**. 

1. W Eksploratorze rozwiązań kliknij rozwiązanie prawym przyciskiem myszy i wybierz pozycję **Ustaw projekty startowe...**. 
2. Wybierz przycisk radiowy **Wiele projektów startowych**.
3. Zmień ustawienie **Akcja** dla obu projektów na wartość **Uruchom**.
4. Kliknij pozycję OK, aby zapisać konfigurację.
5. Naciśnij klawisz **F5**, aby uruchomić obie aplikacje. Każda aplikacja zostanie otwarta na własnej karcie przeglądarki. `https://localhost:44316/` to aplikacja internetowa.
    `https://localhost:44332/` to internetowy interfejs API.

6. W aplikacji internetowej kliknij łącze Sign up / Sign in (Zarejestruj się / Zaloguj się) na banerze menu, aby zarejestrować się w aplikacji internetowej. Użyj konta utworzonego w [samouczku dotyczącym aplikacji internetowej](active-directory-b2c-tutorials-web-app.md). 
7. Po zalogowaniu kliknij link **To-do list** (Lista zadań do wykonania) i utwórz element listy zadań do wykonania.

Podczas tworzenia elementu listy zadań do wykonania aplikacja internetowa wysyła żądanie do internetowego interfejsu API, aby wygenerować element listy zadań do wykonania. Chroniona aplikacja internetowa wywołuje chroniony internetowy interfejs API w dzierżawie usługi Azure AD B2C.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz wypróbować inne samouczki usługi Azure AD B2C, możesz użyć swojej dzierżawy usługi Azure AD B2C. Możesz [usunąć dzierżawę usługi Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant), gdy nie będzie już potrzebna.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono chronienie internetowego interfejsu API platformy ASP.NET przez rejestrowanie i definiowanie zakresów w usłudze Azure AD B2C. Aby uzyskać więcej informacji o projektowaniu tego scenariusza, w tym przewodniki po kodzie, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Tworzenie aplikacji internetowej platformy ASP.NET z obsługą rejestracji, logowania, edytowania profilów i resetowania haseł w usłudze Azure Active Directory B2C](active-directory-b2c-devquickstarts-web-dotnet-susi.md)