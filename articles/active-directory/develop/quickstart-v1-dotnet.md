---
title: Logowanie użytkowników i wywoływanie interfejsu API programu Microsoft Graph z poziomu aplikacji .NET Desktop (WPF) | Microsoft Docs
description: Dowiedz się, jak utworzyć aplikację .NET Desktop (WPF), która integruje się z usługą Azure AD w celu logowania oraz wywołuje interfejsy API chronione przez usługę Azure AD przy użyciu protokołu OAuth 2.0.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: ed33574f-6fa3-402c-b030-fae76fba84e1
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b55f7e615f2c2edb604d5b9433db6cc48d9f36f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60298982"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-a-net-desktop-wpf-app"></a>Szybki start: Logowanie użytkowników i wywoływanie interfejsu API programu Microsoft Graph z poziomu aplikacji .NET Desktop (WPF)

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

W przypadku natywnych klientów platformy .NET, którzy potrzebują dostępu do chronionych zasobów, usługa Azure Active Directory (Azure AD) udostępnia bibliotekę Active Directory Authentication Library (ADAL). Biblioteka ADAL ułatwia aplikacji uzyskiwanie tokenów dostępu. 

W tym przewodniu Szybki start dowiesz się, w jaki sposób utworzyć aplikację listy zadań .NET WPF, która:

* pobiera tokeny dostępu do wywoływania interfejsu API programu Graph usługi Azure AD przy użyciu protokołu uwierzytelniania OAuth 2.0,
* wyszukuje w katalogu użytkowników o określonym aliasie,
* wylogowuje użytkowników.

Aby utworzyć gotową, działającą aplikację, musisz:

1. zarejestrować aplikację w usłudze Azure AD,
2. zainstalować i skonfigurować bibliotekę ADAL,
3. uzyskać tokeny z usługi Azure AD przy użyciu biblioteki ADAL.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, należy spełnić poniższe następujące wstępne:

* [Pobranie szkieletu aplikacji](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) lub [pobranie pełnego przykładu](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).
* Dzierżawa usługi Azure AD, w której można utworzyć użytkowników i zarejestrować aplikację. Jeśli nie masz jeszcze dzierżawy, [dowiedz się, jak ją uzyskać](quickstart-create-new-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>Krok 1: Rejestrowanie aplikacji DirectorySearcher

Aby umożliwić aplikacji uzyskiwanie tokenów, zarejestruj aplikację w dzierżawie usługi Azure AD i przyznaj jej uprawnienia dostępu do interfejsu API programu Graph usługi Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Na górnym pasku wybierz swoje konto i z listy **Katalog** wybierz dzierżawę usługi Active Directory, w której chcesz zarejestrować aplikację.
3. Wybierz pozycję **Wszystkie usługi** w okienku nawigacji po lewej stronie, a następnie wybierz pozycję **Azure Active Directory**.
4. W obszarze **Rejestracje aplikacji** wybierz pozycję **Dodaj**.
5. Postępuj zgodnie z monitami i utwórz nową **natywną** aplikację kliencką.
    * **Nazwa** aplikacji będzie opisywać aplikację dla użytkowników końcowych
    * **Identyfikator URI przekierowania** jest połączeniem schematu i ciągu, przy użyciu którego usługa Azure AD zwróci odpowiedzi tokenów. Wprowadź wartość specyficzną dla Twojej aplikacji, na przykład `http://DirectorySearcher`.

6. Po zakończeniu rejestracji usługa AAD przypisze do aplikacji unikatowy identyfikator aplikacji. Ta wartość będzie potrzebna w kolejnych sekcjach, dlatego skopiuj ją ze strony aplikacji.
7. Na stronie **Ustawienia** wybierz pozycję **Wymagane uprawnienia**, a następnie wybierz pozycję **Dodaj**. Wybierz program **Microsoft Graph** jako interfejs API, a następnie w obszarze **Delegowane uprawnienia** dodaj uprawnienie **Odczytuj dane katalogu**. Ustawienie tego uprawnienia spowoduje skonfigurowanie aplikacji do wykonywania zapytań dotyczących użytkowników względem interfejsu API programu Graph.

## <a name="step-2-install-and-configure-adal"></a>Krok 2: Instalowanie i konfigurowanie biblioteki ADAL

Gdy masz już aplikację w usłudze Azure AD, możesz zainstalować bibliotekę ADAL i napisać kod dotyczący tożsamości. Aby biblioteka ADAL mogła komunikować się z usługą Azure AD, należy podać niektóre informacje dotyczące rejestracji aplikacji.

1. Zacznij od dodania biblioteki ADAL do projektu `DirectorySearcher`, korzystając z konsoli menedżera pakietów.

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

1. W projekcie `DirectorySearcher` otwórz plik `app.config`.
1. Zastąp wartości elementów w sekcji `<appSettings>`, aby odzwierciedlały wartości wprowadzone w witrynie Azure Portal. Twój kod będzie odwoływał się do tych wartości podczas każdego użycia biblioteki ADAL.
   * `ida:Tenant` to domena dzierżawy usługi Azure AD, na przykład contoso.onmicrosoft.com.
   * `ida:ClientId` to identyfikator klienta Twojej aplikacji skopiowany z portalu.
   * `ida:RedirectUri` to adres URL przekierowania zarejestrowany w portalu.

## <a name="step-3-use-adal-to-get-tokens-from-azure-ad"></a>Krok 3: Uzyskiwanie tokenów z usługi Azure AD przy użyciu biblioteki ADAL

Podstawowa zasada działania biblioteki ADAL polega na tym, że za każdym razem, gdy aplikacja potrzebuje tokenu dostępu, po prostu wywołuje ona element `authContext.AcquireTokenAsync(...)`, a biblioteka ADAL zajmuje się resztą.

1. W projekcie `DirectorySearcher` otwórz plik `MainWindow.xaml.cs`.
1. Znajdź metodę `MainWindow()`. 
1. Zainicjuj element `AuthenticationContext` aplikacji — podstawową klasę biblioteki ADAL. To właśnie w elemencie `AuthenticationContext` przekazuje się bibliotece ADAL współrzędne, aby umożliwić komunikację z usługą Azure AD i przekazać instrukcje dotyczące sposobu buforowania tokenów.

    ```csharp
    public MainWindow()
    {
        InitializeComponent();

        authContext = new AuthenticationContext(authority, new FileCache());

        CheckForCachedToken();
    }
    ```

1. Znajdź metodę `Search(...)`, która zostanie wywołana, gdy użytkownik wybierze przycisk **Wyszukaj** w interfejsie użytkownika aplikacji. Ta metoda wysyła żądanie GET do interfejsu API programu Graph usługi Azure AD, aby wykonać zapytanie dotyczące użytkowników, których nazwa UPN zaczyna się od danego wyszukiwanego terminu.
1. Aby wykonać zapytanie względem interfejsu API programu Graph, należy uwzględnić element access_token w nagłówku `Authorization` żądania, w którym rozpocznie się używanie biblioteki ADAL.

    ```csharp
    private async void Search(object sender, RoutedEventArgs e)
    {
        // Validate the Input String
        if (string.IsNullOrEmpty(SearchText.Text))
        {
            MessageBox.Show("Please enter a value for the To Do item name");
            return;
        }

        // Get an Access Token for the Graph API
        AuthenticationResult result = null;
        try
        {
            result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
            UserNameLabel.Content = result.UserInfo.DisplayableId;
            SignOutButton.Visibility = Visibility.Visible;
        }
        catch (AdalException ex)
        {
            // An unexpected error occurred, or user canceled the sign in.
            if (ex.ErrorCode != "access_denied")
                MessageBox.Show(ex.Message);

            return;
        }

        ...
    }
    ```

    Gdy aplikacja żąda tokenu przez wywołanie metody `AcquireTokenAsync(...)`, biblioteka ADAL próbuje zwrócić token bez monitowania użytkownika o poświadczenia.
    * Jeśli biblioteka ADAL ustali, że użytkownik musi się zalogować, aby uzyskać token, wyświetli okno dialogowe logowania, zbierze poświadczenia użytkownika i zwróci token po pomyślnym uwierzytelnieniu. 
    * Jeśli z jakiegokolwiek powodu biblioteka ADAL nie może zwrócić tokenu, zgłasza wyjątek `AdalException`.

1. Zauważ, że obiekt `AuthenticationResult` zawiera obiekt `UserInfo`, przy użyciu którego możesz zebrać informacje potrzebne w aplikacji. W obiekcie DirectorySearcher element `UserInfo` jest używany do dostosowywania interfejsu użytkownika aplikacji za pomocą identyfikatora użytkownika.
1. Gdy użytkownik wybierze przycisk **Wyloguj**, upewnij się, że następne wywołanie elementu `AcquireTokenAsync(...)` będzie monitować użytkownika o zalogowanie. Możesz to łatwo zrobić za pomocą biblioteki ADAL przez wyczyszczenie pamięci podręcznej tokenu:

    ```csharp
    private void SignOut(object sender = null, RoutedEventArgs args = null)
    {
        // Clear the token cache
        authContext.TokenCache.Clear();

        ...
    }
    ```

    Jeśli użytkownik nie kliknie przycisku **Wyloguj**, musisz utrzymać sesję użytkownika do następnego uruchomienia obiektu DirectorySearcher. Po uruchomieniu aplikacji możesz sprawdzić pamięć podręczną tokenu biblioteki ADAL dla istniejącego tokenu, a następnie odpowiednio zaktualizować interfejs użytkownika.

1. W metodzie `CheckForCachedToken()` wykonaj inne wywoływania do elementu `AcquireTokenAsync(...)`, tym razem przekazując parametr `PromptBehavior.Never`. Element `PromptBehavior.Never` poinformuje bibliotekę ADAL, że użytkownik nie powinien być monitowany o zalogowanie, a biblioteka ADAL powinna w zamian zgłosić wyjątek, jeśli nie może zwrócić tokenu.

    ```csharp
    public async void CheckForCachedToken() 
    {
        // As the application starts, try to get an access token without prompting the user. If one exists, show the user as signed in.
        AuthenticationResult result = null;
        try
        {
            result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never));
        }
        catch (AdalException ex)
        {
            if (ex.ErrorCode != "user_interaction_required")
            {
                // An unexpected error occurred.
                MessageBox.Show(ex.Message);
            }

            // If user interaction is required, proceed to main page without signing the user in.
            return;
        }

        // A valid token is in the cache
        SignOutButton.Visibility = Visibility.Visible;
        UserNameLabel.Content = result.UserInfo.DisplayableId;
    }
    ```

Gratulacje! Masz teraz działającą aplikację .NET WPF, która może uwierzytelniać użytkowników, bezpiecznie wywoływać internetowe interfejsy API przy użyciu protokołu OAuth 2.0 oraz uzyskiwać podstawowe informacje o użytkowniku. Jeśli jeszcze tego nie zrobiono, warto teraz wypełnić dzierżawę użytkownikami. Uruchom aplikację DirectorySearcher, a następnie zaloguj się przy użyciu jednego z użytkowników. Wyszukaj innych użytkowników na podstawie ich nazw UPN. Zamknij aplikację i uruchom ją ponownie. Zwróć uwagę, że sesja użytkownika pozostaje niezmieniona. Wyloguj się i zaloguj się jako inny użytkownik.

Biblioteka ADAL ułatwia dodanie tych typowych funkcji tożsamości do aplikacji. Zajmuje się ona żmudnymi zadaniami, w tym zarządza pamięcią podręczną, obsługuje protokół OAuth, prezentuje użytkownikom interfejs użytkownika logowania, odświeża wygasłe tokeny i nie tylko. Musisz znać tylko jedno wywołanie interfejsu API, `authContext.AcquireTokenAsync(...)`.

Gotowa próbka (bez wartości konfiguracji) jest dostępna w serwisie [GitHub](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak chronić internetowy interfejs API przy użyciu tokenów dostępu elementu nośnego protokołu OAuth 2.0.
> [!div class="nextstepaction"]
> [Zabezpiecz internetowy interfejs API za pomocą usługi Azure AD >>](quickstart-v1-dotnet-webapi.md)
