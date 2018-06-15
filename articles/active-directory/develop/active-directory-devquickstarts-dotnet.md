---
title: Azure AD .NET pulpitu (WPF) wprowadzenie | Dokumentacja firmy Microsoft
description: Jak utworzyć aplikację .NET pulpitu systemu Windows, która integruje się z usługą Azure AD w celu logowania się i wymaga usługi Azure AD chronione interfejsów API w trybie OAuth.
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
ms.openlocfilehash: 1097d4b1f7d8c4feae500bf46b40e61029781d68
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34156266"
---
# <a name="azure-ad-net-desktop-wpf-getting-started"></a>Azure AD .NET pulpitu (WPF) wprowadzenie
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Jeśli projektujesz klasycznej aplikacji usługi Azure AD umożliwia proste i bezpośrednie umożliwia uwierzytelnianie użytkowników przy użyciu ich kont usługi Active Directory. Umożliwia również aplikacji bezpiecznego używać dowolnego składnika web API chronione przez usługę Azure AD, takie jak interfejsami API usługi Office 365 lub interfejsu API platformy Azure.

Dla platformy .NET native klientów, którzy potrzebują dostępu do chronionych zasobów Usługa Azure AD zapewnia biblioteki uwierzytelniania usługi Active Directory lub biblioteki ADAL. ADAL w wyłącznie życia ma na celu ułatwić aplikację, aby uzyskać dostęp do tokenów. Aby zademonstrować, jak łatwo jest, w tym miejscu będzie budujemy aplikacji listy zadań do wykonania WPF .NET który:

* Pobiera dostępu tokenów do wywoływania za pomocą interfejsu API usługi Azure AD Graph [protokół uwierzytelniania OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Przeszukuje katalog dla użytkowników z podanego aliasu.
* Znaki użytkowników.

Aby utworzyć pełną działającą aplikację, musisz:

1. Zarejestrować aplikację w usłudze Azure AD.
2. Instalowanie i konfigurowanie biblioteki ADAL.
3. Użyj biblioteki ADAL, aby uzyskać tokenów z usługi Azure AD.

Aby rozpocząć, [pobrać szkielet aplikacji](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) lub [Pobieranie ukończone próbki](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip). Należy również dzierżawa usługi Azure AD można tworzyć użytkowników i zarejestrowanie aplikacji. Jeśli nie masz już dzierżawę, [Dowiedz się, jak kupić](active-directory-howto-tenant.md).

## <a name="1-register-the-directorysearcher-application"></a>1. Zarejestrować aplikację DirectorySearcher
Aby włączyć swoją aplikację w celu uzyskania tokenów, musisz najpierw zarejestrować ją w dzierżawie usługi Azure AD i udzielić jej uprawnienia dostępu do interfejsu API programu Azure AD Graph:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Na górnym pasku, kliknij na Twoim koncie i w obszarze **katalogu** wybierz dzierżawy usługi Active Directory, w którym chcesz zarejestrować aplikację.
3. Polecenie **wszystkie usługi** w nawigacji po lewej stronie i wybierz polecenie **usługi Azure Active Directory**.
4. Polecenie **rejestracji aplikacji** i wybierz polecenie **Dodaj**.
5. Postępuj zgodnie z monitami i utworzyć nową **natywną aplikację kliencką**.
  * **Nazwa** aplikacji będzie opisywać aplikację dla użytkowników końcowych
  * **Identyfikator Uri przekierowania** jest kombinacją schemat i ciąg, używanego do zwracania odpowiedzi tokenu usługi Azure AD. Wprowadź wartości określonych aplikacji, np. `http://DirectorySearcher`.
6. Po zakończeniu rejestracji usługi AAD przypisze aplikacji Unikatowy identyfikator aplikacji. Ta wartość jest potrzebny w kolejnych sekcjach, dlatego skopiuj go ze strony aplikacji.
7. Z **ustawienia** wybierz pozycję **wymagane uprawnienia** i wybierz polecenie **Dodaj**. Wybierz **Microsoft Graph** jako interfejsu API i Dodaj **Czytaj dane katalogu** uprawnienie w obszarze **delegowane uprawnienia**. Umożliwi to aplikacja do zapytania interfejsu API programu Graph dla użytkowników.

## <a name="2-install--configure-adal"></a>2. Instalowanie i konfigurowanie biblioteki ADAL
Teraz, gdy masz aplikacji w usłudze Azure AD, można zainstalować biblioteki ADAL i wpisz swój kod dotyczące tożsamości. Biblioteki ADAL można było nawiązać połączenia z usługą Azure AD, należy dostarczyć informacji o rejestracji aplikacji.

* Rozpocznij, dodając biblioteki ADAL do projektu DirectorySearcher przy użyciu konsoli Menedżera pakietów.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

* W projekcie DirectorySearcher Otwórz `app.config`. Zastąp wartości elementów w `<appSettings>` sekcji, aby odzwierciedlić wartości wejściowych do portalu Azure. Kod będzie odwoływać tych wartości, przy każdym użyciu biblioteki ADAL.
  * `ida:Tenant` Jest domeny dzierżawy usługi Azure AD, np. contoso.onmicrosoft.com
  * `ida:ClientId` Jest clientId aplikacji został skopiowany z portalu.
  * `ida:RedirectUri` Przekierowania jest adres url jest zarejestrowany w portalu.

## <a name="3-use-adal-to-get-tokens-from-aad"></a>3. Aby uzyskać tokenów z usługi AAD, użyj biblioteki ADAL
Zasada podstawowa za ADAL jest, że zawsze, gdy Twoja aplikacja powinna tokenu dostępu, po prostu wywołuje `authContext.AcquireTokenAsync(...)`, a reszta biblioteki ADAL. 

* W `DirectorySearcher` otwarciu projektu `MainWindow.xaml.cs` i Znajdź `MainWindow()` metody. Pierwszym krokiem jest zainicjowanie aplikacji `AuthenticationContext` -ADAL obiektu klasy podstawowej. Jest to, gdy przekazujesz ADAL współrzędne wymaga do komunikacji z usługą Azure AD i poinformuj go, jak pamięci podręcznej tokenów.

```csharp
public MainWindow()
{
    InitializeComponent();

    authContext = new AuthenticationContext(authority, new FileCache());

    CheckForCachedToken();
}
```

* Znajdź teraz `Search(...)` metodę, która będzie wywoływana, gdy użytkownik kliknie przycisk "Wyszukiwanie" w Interfejsie użytkownika aplikacji. Ta metoda zgłasza żądanie GET interfejsu API Azure AD Graph zapytania dla użytkowników, których nazwy UPN rozpoczyna się od danego wyszukiwanego. Jednak aby można było wykonać kwerendę interfejsu API programu Graph, należy uwzględnić ' access_token ' w `Authorization` nagłówka żądania — jest to, gdzie ADAL jest dostarczany.

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
* Gdy aplikacja żąda token przez wywołanie metody `AcquireTokenAsync(...)`, ADAL podejmie próbę zwracają token bez monitowania użytkownika o poświadczenia. Jeśli ADAL Określa, czy użytkownik powinien logować się do pobrania tokenu, zostanie wyświetlane okno dialogowe logowania, zbieranie poświadczeń użytkownika i zwracają token, po pomyślnym uwierzytelnieniu. Jeśli nie można zwrócić token jakiegokolwiek powodu ADAL, zgłosi `AdalException`.
* Zwróć uwagę, że `AuthenticationResult` zawiera obiekt `UserInfo` obiektu, który może służyć do zbierania informacji o aplikacji może być konieczne. W DirectorySearcher `UserInfo` jest używany do dostosowania interfejsu użytkownika aplikacji z identyfikatorem użytkownika.
* Gdy użytkownik kliknie przycisk "Wyloguj", chcemy się upewnić się, że następne wywołanie `AcquireTokenAsync(...)` będzie monitować użytkownika do logowania. Przy użyciu biblioteki ADAL jest tak proste, jak czyszczenie pamięci podręcznej tokenu:

```csharp
private void SignOut(object sender = null, RoutedEventArgs args = null)
{
    // Clear the token cache
    authContext.TokenCache.Clear();

    ...
}
```

* Jednak jeśli użytkownik nie klikniesz przycisku "Wyloguj", można zachować sesję użytkownika dla przy następnym uruchomieniu DirectorySearcher. Po uruchomieniu aplikacji, należy sprawdzić ADAL w pamięci podręcznej tokenu dla istniejącego tokenu, a następnie odpowiednio zaktualizować interfejsu użytkownika. W `CheckForCachedToken()` metody innego wywoływania `AcquireTokenAsync(...)`, przekazując teraz `PromptBehavior.Never` parametru. `PromptBehavior.Never` informuje ADAL, że użytkownik nie powinien być monitowany w celu logowania się i ADAL zamiast tego powinien zgłosić wyjątek, jeśli nie można zwrócić token.

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

Gratulacje! Możesz teraz działa aplikacja .NET WPF, która ma możliwość uwierzytelniania użytkowników, bezpiecznie wywoływać interfejsy API sieci Web przy użyciu protokołu OAuth 2.0 i uzyskać podstawowe informacje o użytkowniku. Jeśli nie jest jeszcze nadszedł czas do wypełnienia dzierżawy z niektórych użytkowników. Uruchom aplikację DirectorySearcher i zaloguj się przy użyciu jednej z tych użytkowników. Wyszukiwać innych użytkowników, w oparciu o ich nazwy UPN. Zamknij aplikację i uruchom je ponownie. Zwróć uwagę, jak sesji użytkownika pozostanie niezmieniona. Wyloguj się i zaloguj się ponownie jako inny użytkownik.

Biblioteka ADAL można łatwo zastosować wszystkie te typowe funkcje tożsamości w aplikacji. Zapewnia obsługę pracy dirty dla Ciebie — Zarządzanie pamięci podręcznej, obsługa protokołu OAuth, przedstawiający użytkownika za pomocą interfejsu użytkownika, odświeżanie wygaśnięcia tokenów i inne nazwy logowania. Tak naprawdę trzeba wiedzieć jednego wywołania interfejsu API, jest `authContext.AcquireTokenAsync(...)`.

Odwołania, jest dostępne ukończone próbka (bez wartości konfiguracji) [tutaj](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip). Możesz teraz przejść do dodatkowe scenariusze. Można spróbować:

[Zabezpieczanie interfejsu API za pomocą usługi Azure AD sieci Web .NET >>](active-directory-devquickstarts-webapi-dotnet.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

