---
title: Logowania użytkowników i wywoływanie interfejsu API programu Microsoft Graph z aplikacji klasycznych .NET (WPF) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację .NET Windows dla komputerów stacjonarnych, która integruje się z usługą Azure AD, logowania i wywołania usługi Azure AD chronione interfejsów API przy użyciu protokołu OAuth 2.0.
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
ms.topic: article
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 968afcba8b0a6ab9d46c5582eecbb4901975257c
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2019
ms.locfileid: "55101153"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-a-net-desktop-wpf-app"></a>Szybki start: Logowania użytkowników i wywoływanie interfejsu API programu Microsoft Graph z aplikacji klasycznych .NET (WPF)

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Dla platformy .NET native klientów, którzy potrzebują dostępu do chronionych zasobów usługi Azure Active Directory (Azure AD) zapewnia Active Directory Authentication Library (ADAL). Biblioteka ADAL ułatwia uzyskiwanie tokenów dostępu aplikacji. 

W tym przewodniku Szybki Start dowiesz się, jak utworzyć aplikację listy zadań do wykonania WPF .NET który:

* Pobiera uzyskiwać dostęp do tokenów dla wywołania interfejsu API programu Graph usługi Azure AD przy użyciu protokołu uwierzytelniania OAuth 2.0.
* Przeszukuje katalog dla użytkowników przy użyciu podanego aliasu.
* Objawy użytkowników.

Aby utworzyć gotową, działającą aplikację, musisz:

1. zarejestrować aplikację w usłudze Azure AD,
2. zainstalować i skonfigurować bibliotekę ADAL,
3. uzyskać tokeny z usługi Azure AD przy użyciu biblioteki ADAL.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, należy spełnić poniższe następujące wstępne:

* [Pobrać szkielet aplikacji](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) lub [Pobierz przykładowe ukończone](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip)
* Mają dzierżawę usługi Azure AD, której tworzenie użytkowników i zarejestrowanie aplikacji. Jeśli nie masz jeszcze dzierżawy, [dowiedz się, jak ją uzyskać](quickstart-create-new-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>Krok 1: Rejestrowanie aplikacji DirectorySearcher

Aby umożliwić aplikacji w celu uzyskania tokenów, Zarejestruj swoją aplikację w dzierżawie usługi Azure AD i przyznać jej uprawnienia dostępu do interfejsu API programu Graph usługi Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Na górnym pasku wybierz swoje konto i w obszarze **katalogu** wybierz dzierżawę usługi Active Directory, w którym chcesz zarejestrować aplikację.
3. Wybierz **wszystkich usług** w okienku nawigacji po lewej stronie, a wybierz **usługi Azure Active Directory**.
4. Na **rejestracje aplikacji**, wybierz **Dodaj**.
5. Postępuj zgodnie z monitami i Utwórz nowy **natywnych** aplikacji klienckiej.
    * **Nazwa** aplikacji będzie opisywać aplikację użytkownikom końcowym
    * **Identyfikator Uri przekierowania** jest połączeniem schemat i parametry, które usługi Azure AD będzie używać do zwracania odpowiedzi tokenu. Wprowadź wartość specyficzną dla twojej aplikacji, na przykład `http://DirectorySearcher`.

6. Po zakończeniu rejestracji usługi AAD przypisze aplikacji Unikatowy identyfikator aplikacji. Ta wartość będzie potrzebna w kolejnych sekcjach, więc skopiuj ją ze strony aplikacji.
7. Z **ustawienia** wybierz **wymagane uprawnienia** i wybierz polecenie **Dodaj**. Wybierz **programu Microsoft Graph** jako interfejs API, a następnie w obszarze **delegowane uprawnienia** Dodaj **Odczyt danych katalogu** uprawnień. Ustawienie to uprawnienie umożliwia aplikacji do wykonywania zapytań interfejsu API programu Graph dla użytkowników.

## <a name="step-2-install-and-configure-adal"></a>Krok 2: Instalowanie i konfigurowanie biblioteki ADAL

Gdy masz już aplikację w usłudze Azure AD, możesz zainstalować bibliotekę ADAL i napisać kod dotyczący tożsamości. Aby biblioteka ADAL mogła komunikować się z usługą Azure AD, należy podać niektóre informacje dotyczące rejestracji aplikacji.

1. Rozpocznij, dodając biblioteki ADAL w celu `DirectorySearcher` projektu przy użyciu konsoli Menedżera pakietów.

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

1. W `DirectorySearcher` otwarty projekt `app.config`.
1. Zastąp wartości elementów w `<appSettings>` sekcji, aby odzwierciedlał odpowiednie wartości wejściowe w witrynie Azure portal. Twój kod będzie odwoływał się do tych wartości podczas każdego użycia biblioteki ADAL.
  * `ida:Tenant` Jest domena dzierżawy usługi Azure AD, np. contoso.onmicrosoft.com
  * `ida:ClientId` Jest identyfikator klienta aplikacji skopiowane z portalu.
  * `ida:RedirectUri` Jest adres URL przekierowania zarejestrowany w portalu.

## <a name="step-3-use-adal-to-get-tokens-from-azure-ad"></a>Krok 3: Użyj biblioteki ADAL w celu uzyskania tokenów z usługi Azure AD

Podstawową zasadą za ADAL jest, że zawsze wtedy, gdy Twoja aplikacja wymaga tokenu dostępu, aplikacji po prostu wywołuje `authContext.AcquireTokenAsync(...)`, a ADAL zajmie się resztą.

1. W `DirectorySearcher` otwarty projekt `MainWindow.xaml.cs`.
1. Znajdź `MainWindow()` metody. 
1. Zainicjuj aplikację usługi `AuthenticationContext` -biblioteki ADAL w klasie podstawowej. `AuthenticationContext` to, gdzie należy przekazać biblioteki ADAL współrzędne musi komunikować się z usługą Azure AD i poinformuj go, jak tokeny pamięci podręcznej.

    ```csharp
    public MainWindow()
    {
        InitializeComponent();

        authContext = new AuthenticationContext(authority, new FileCache());

        CheckForCachedToken();
    }
    ```

1. Znajdź `Search(...)` metody, która zostanie wywołana, gdy użytkownik wybierze **wyszukiwania** przycisku w Interfejsie użytkownika aplikacji. Ta metoda wysyła żądanie GET do interfejsu API programu Graph usługi Azure AD, aby wykonać zapytanie dotyczące użytkowników, których nazwa UPN zaczyna się od danego wyszukiwanego terminu.
1. Aby odpytać interfejsu API programu Graph, należy dołączyć ' access_token ' w `Authorization` nagłówka żądania, czyli, gdzie jest dostępna w bibliotece ADAL.

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

    Gdy aplikacja żąda tokenu, wywołując `AcquireTokenAsync(...)`, biblioteka ADAL będzie podejmować próby zwracania tokenu bez monitowania użytkownika o poświadczenia.
    * Jeśli biblioteki ADAL okaże się, że użytkownik musi zarejestrować się w celu pobrania tokenu, wówczas wyświetlić okna dialogowego logowania, zbieranie poświadczeń użytkownika i zwracania tokenu po pomyślnym uwierzytelnieniu. 
    * W przypadku nie można zwrócić token z jakiegokolwiek powodu ADAL zgłosi `AdalException`.

1. Należy zauważyć, że `AuthenticationResult` obiekt zawiera `UserInfo` obiektu, który może służyć do zbierania informacji może być konieczne w swojej aplikacji. W DirectorySearcher `UserInfo` jest używany do dostosowywania w Interfejsie użytkownika aplikacji za pomocą identyfikatora użytkownika.
1. Gdy użytkownik wybierze **Wyloguj** przycisku, upewnij się, że następne wywołanie `AcquireTokenAsync(...)` będzie monitować użytkownika do logowania. Możesz to łatwo zrobić za pomocą biblioteki ADAL przez wyczyszczenie pamięci podręcznej tokenu:

    ```csharp
    private void SignOut(object sender = null, RoutedEventArgs args = null)
    {
        // Clear the token cache
        authContext.TokenCache.Clear();

        ...
    }
    ```

    Jeśli użytkownik nie klikać **Wyloguj** przycisku trzeba utrzymywać w sesji użytkownika, przy następnym uruchomieniu DirectorySearcher. Po uruchomieniu aplikacji, należy sprawdzić pamięć podręczną tokenu biblioteki ADAL firmy do istniejącego tokenu, a następnie odpowiednio zaktualizować interfejs użytkownika.

1. W `CheckForCachedToken()` metody innego wywoływania `AcquireTokenAsync(...)`, tym razem, przekazując `PromptBehavior.Never` parametru. `PromptBehavior.Never` poinformuje biblioteki ADAL, że użytkownik nie powinien monit logowania i biblioteki ADAL zamiast tego powinien zgłosić wyjątek, jeśli nie można zwrócić tokenu.

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

            // If user interaction is required, proceed to main page without singing the user in.
            return;
        }

        // A valid token is in the cache
        SignOutButton.Visibility = Visibility.Visible;
        UserNameLabel.Content = result.UserInfo.DisplayableId;
    }
    ```

Gratulacje! Masz teraz działającą aplikację .NET WPF, która może uwierzytelniać użytkowników, bezpiecznie wywoływać interfejsy API sieci Web przy użyciu protokołu OAuth 2.0 i Uzyskaj podstawowe informacje o użytkowniku. Jeśli jeszcze tego nie zrobiono, warto teraz wypełnić dzierżawę użytkownikami. Uruchom aplikację DirectorySearcher i zaloguj się przy użyciu jednego z tych użytkowników. Wyszukaj innych użytkowników na podstawie ich nazw UPN. Zamknij aplikację i ponownie ją uruchomić. Zwróć uwagę, jak sesji użytkownika pozostaje niezmieniona. Wyloguj się i zaloguj się ponownie jako inny użytkownik.

Biblioteka ADAL ułatwia uwzględnienie tych typowe funkcje obsługi tożsamości w aplikacji. Zajmuje się pracy dirty dla Ciebie, uwzględniając zarządzanie pamięcią podręczną, obsługa protokołu OAuth, prezentowanie użytkownika z logowaniem użytkownika odświeżanie wygasłych tokenów i nie tylko. Musisz znać tylko jedno wywołanie interfejsu API, `authContext.AcquireTokenAsync(...)`.

Aby uzyskać odwołanie, zobacz przykład ukończone (bez wartości konfiguracji) [w serwisie GitHub](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak chronić internetowy interfejs API przy użyciu tokenów dostępu elementu nośnego OAuth 2.0.
> [!div class="nextstepaction"]
> [Zabezpieczanie sieci Web platformy .NET interfejsu API za pomocą usługi Azure AD >>](quickstart-v1-dotnet-webapi.md)
