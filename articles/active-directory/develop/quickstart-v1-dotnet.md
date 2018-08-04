---
title: Usługa Azure AD .NET klasycznych (WPF) wprowadzenie | Dokumentacja firmy Microsoft
description: Jak utworzyć aplikację .NET Windows dla komputerów stacjonarnych, integruje się z usługą Azure AD, logowania, która wywołuje usługę Azure AD chronione interfejsów API przy użyciu protokołu OAuth.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: ed33574f-6fa3-402c-b030-fae76fba84e1
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/30/2017
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e7a662afd78ff053c60aeb5de3af9f0070a7c458
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39506528"
---
# <a name="azure-ad-net-desktop-wpf-getting-started"></a>Usługa Azure AD .NET klasycznych (WPF) wprowadzenie
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Jeżeli projektujesz aplikację usługi Azure AD ułatwia prostym służących do uwierzytelniania użytkowników za pomocą kont usługi Active Directory. Umożliwia również aplikacji bezpiecznie używać dowolnej internetowego interfejsu API chronionego przez usługę Azure AD, takich jak interfejsy API usługi Office 365 lub interfejsu API platformy Azure.

Dla platformy .NET native klientów, którzy potrzebują dostępu do chronionych zasobów Usługa Azure AD zapewnia biblioteki uwierzytelniania usługi Active Directory lub biblioteki ADAL. Biblioteki ADAL firmy wyłącznie w życiu ma na celu ułatwić uzyskiwanie tokenów dostępu aplikacji. Aby zademonstrować, jak łatwo jest, w tym miejscu utworzymy aplikację listy zadań do wykonania WPF .NET który:

* Pobiera dostępu tokenów do wywoływania interfejsu API programu Graph usługi Azure AD, przy użyciu [protokołu uwierzytelniania OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Przeszukuje katalog dla użytkowników przy użyciu podanego aliasu.
* Objawy użytkowników.

Aby utworzyć pełną działającą aplikację, musisz:

1. Zarejestrować aplikację w usłudze Azure AD.
2. Zainstalowanie i skonfigurowanie biblioteki ADAL.
3. Używa biblioteki ADAL, uzyskiwanie tokenów z usługi Azure AD.

Aby rozpocząć pracę, [pobrać szkielet aplikacji](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) lub [Pobierz przykładowe ukończone](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip). Należy także dzierżawę usługi Azure AD może tworzyć użytkowników i zarejestrowanie aplikacji. Jeśli nie masz jeszcze dzierżawy, [Dowiedz się, jak je](quickstart-create-new-tenant.md).

## <a name="1-register-the-directorysearcher-application"></a>1. Rejestrowanie aplikacji DirectorySearcher
Aby umożliwić zarządzanie aplikacją uzyskiwanie tokenów, musisz najpierw zarejestrować ją w dzierżawie usługi Azure AD i przyznać jej uprawnienia dostępu do interfejsu API programu Graph usługi Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Na górnym pasku kliknij na Twoim koncie, a w obszarze **katalogu** wybierz dzierżawę usługi Active Directory, w którym chcesz zarejestrować aplikację.
3. Kliknij pozycję **wszystkich usług** w okienku nawigacji po lewej stronie ekranu i wybierz polecenie **usługi Azure Active Directory**.
4. Kliknij pozycję **rejestracje aplikacji** i wybierz polecenie **Dodaj**.
5. Postępuj zgodnie z monitami i Utwórz nowy **natywną aplikację kliencką**.
  * **Nazwa** aplikacji będzie opisywać aplikację użytkownikom końcowym
  * **Identyfikator Uri przekierowania** jest połączeniem schemat i parametry, które usługi Azure AD będzie używać do zwracania odpowiedzi tokenu. Wprowadź wartość specyficzną dla twojej aplikacji, np. `http://DirectorySearcher`.
6. Po zakończeniu rejestracji usługi AAD przypisze aplikacji Unikatowy identyfikator aplikacji. Ta wartość będzie potrzebna w kolejnych sekcjach, więc skopiuj ją ze strony aplikacji.
7. Z **ustawienia** wybierz **wymagane uprawnienia** i wybierz polecenie **Dodaj**. Wybierz **programu Microsoft Graph** jako interfejs API i Dodaj **Czytaj dane katalogu** uprawnień w ramach **delegowane uprawnienia**. Spowoduje to włączenie aplikacji do wykonywania zapytań interfejsu API programu Graph dla użytkowników.

## <a name="2-install--configure-adal"></a>2. Zainstalowanie i skonfigurowanie biblioteki ADAL
Teraz, gdy masz już aplikację w usłudze Azure AD, możesz zainstalować biblioteki ADAL i pisanie kodu związanych z tożsamościami. Biblioteki ADAL można było nawiązać połączenia z usługą Azure AD, należy udostępnić informacje dotyczące rejestracji aplikacji.

* Rozpocznij, dodając biblioteki ADAL do projektu DirectorySearcher przy użyciu konsoli Menedżera pakietów.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

* W projekcie DirectorySearcher Otwórz `app.config`. Zastąp wartości elementów w `<appSettings>` sekcji, aby odzwierciedlał odpowiednie wartości wejściowe w witrynie Azure Portal. Twój kod będzie odwoływać się te wartości w każdym przypadku, gdy używa biblioteki ADAL.
  * `ida:Tenant` Jest domena dzierżawy usługi Azure AD, np. contoso.onmicrosoft.com
  * `ida:ClientId` Jest clientId aplikacji skopiowane z portalu.
  * `ida:RedirectUri` Przekierowania, który jest adres url jest zarejestrowany w portalu.

## <a name="3-use-adal-to-get-tokens-from-aad"></a>3. Użyj biblioteki ADAL w celu uzyskania tokenów z usługi AAD
Podstawową zasadą za ADAL jest, że zawsze wtedy, gdy Twoja aplikacja wymaga tokenu dostępu, po prostu wywołuje on `authContext.AcquireTokenAsync(...)`, a ADAL zajmie się resztą. 

* W `DirectorySearcher` otwarty projekt `MainWindow.xaml.cs` i Znajdź `MainWindow()` metody. Pierwszym krokiem jest Zainicjuj aplikację usługi `AuthenticationContext` -biblioteki ADAL w klasie podstawowej. Jest to, gdzie należy przekazać biblioteki ADAL współrzędne musi komunikować się z usługą Azure AD i poinformuj go, jak tokeny pamięci podręcznej.

```csharp
public MainWindow()
{
    InitializeComponent();

    authContext = new AuthenticationContext(authority, new FileCache());

    CheckForCachedToken();
}
```

* Znajdź teraz `Search(...)` metody, która zostanie wywołany, gdy użytkownik kliknie przycisk "Wyszukaj" w Interfejsie użytkownika aplikacji. Ta metoda zgłasza żądanie GET interfejsu API programu Graph usługi Azure AD do zapytania dla użytkowników, których nazwy UPN zaczyna się od danego wyszukiwany termin. Jednak aby można było zbadać interfejsu API programu Graph, trzeba uwzględnić ' access_token ' w `Authorization` nagłówka żądania — jest to, gdzie jest dostępna w bibliotece ADAL.

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
* Gdy aplikacja żąda tokenu, wywołując `AcquireTokenAsync(...)`, biblioteka ADAL będzie podejmować próby zwracania tokenu bez monitowania użytkownika o poświadczenia. Jeśli biblioteki ADAL okaże się, że użytkownik musi zarejestrować się w celu pobrania tokenu, wówczas wyświetlić okna dialogowego logowania, zbieranie poświadczeń użytkownika i zwracania tokenu po pomyślnym uwierzytelnieniu. W przypadku nie można zwrócić token z jakiegokolwiek powodu ADAL zgłosi `AdalException`.
* Należy zauważyć, że `AuthenticationResult` obiekt zawiera `UserInfo` obiektu, który może służyć do zbierania informacji może być konieczne w swojej aplikacji. W DirectorySearcher `UserInfo` jest używany do dostosowywania w Interfejsie użytkownika aplikacji z identyfikatorem użytkownika.
* Gdy użytkownik kliknie przycisk "Wyloguj", chcemy, aby upewnić się, że następne wywołanie `AcquireTokenAsync(...)` będzie monitować użytkownika do logowania. Za pomocą biblioteki ADAL jest równie proste jak wyczyścić pamięć podręczną tokenu:

```csharp
private void SignOut(object sender = null, RoutedEventArgs args = null)
{
    // Clear the token cache
    authContext.TokenCache.Clear();

    ...
}
```

* Jednak jeśli użytkownik nie klikać przycisku "Wyloguj", należy zachować sesję użytkownika użytku następnym razem, gdy uruchamiają DirectorySearcher. Po uruchomieniu aplikacji, należy sprawdzić pamięć podręczną tokenu biblioteki ADAL firmy do istniejącego tokenu, a następnie odpowiednio zaktualizować interfejs użytkownika. W `CheckForCachedToken()` metody innego wywoływania `AcquireTokenAsync(...)`, tym razem, przekazując `PromptBehavior.Never` parametru. `PromptBehavior.Never` poinformuje biblioteki ADAL, że użytkownik nie powinien monit logowania i biblioteki ADAL zamiast tego powinien zgłosić wyjątek, jeśli nie można zwrócić tokenu.

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

Gratulacje! Teraz możesz mieć działającą aplikację .NET WPF, która ma możliwość uwierzytelniania użytkowników, bezpiecznie wywoływać interfejsy API sieci Web przy użyciu protokołu OAuth 2.0 i uzyskać podstawowe informacje o użytkowniku. Jeśli nie jest jeszcze teraz jest czasu na wypełnienie dzierżawy niektórych użytkowników. Uruchom aplikację DirectorySearcher i zaloguj się przy użyciu jednego z tych użytkowników. Wyszukiwać innych użytkowników, w oparciu o ich nazwy UPN. Zamknij aplikację i uruchom je ponownie. Zwróć uwagę, jak sesji użytkownika pozostaną niezmienione. Wyloguj się i zaloguj się ponownie jako inny użytkownik.

Biblioteka ADAL ułatwia uwzględnienie wszystkich tych typowe funkcje obsługi tożsamości w aplikacji. Zajmuje się całej pracy dirty dla Ciebie — zarządzanie pamięcią podręczną, obsługa protokołu OAuth, prezentowanie użytkownika z logowaniem użytkownika odświeżanie wygasłych tokenów i nie tylko. Tak naprawdę musisz wiedzieć, wystarczy jedno wywołanie interfejsu API, `authContext.AcquireTokenAsync(...)`.

Odwołanie, znajduje się przykład ukończone (bez wartości konfiguracji) [tutaj](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip). Możesz teraz przejść do dodatkowych scenariuszy. Można spróbować:

[Zabezpieczanie sieci Web platformy .NET interfejsu API za pomocą usługi Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

