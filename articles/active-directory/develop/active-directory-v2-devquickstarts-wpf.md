---
title: "Natywnych aplikacji .NET v2.0 usługi Azure Active Directory | Dokumentacja firmy Microsoft"
description: "Sposób tworzenia natywnych aplikacji .NET i logowania użytkowników przy użyciu obu Account Microsoft i konta służbowego."
services: active-directory
documentationcenter: 
author: jmprieur
manager: mtillman
editor: 
ms.assetid: 46d81e09-bad0-44ce-9026-881805976e72
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/30/2016
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 88679e7dd71011f767cbe4de295c284516375d20
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2018
---
# <a name="add-sign-in-to-a-windows-desktop-app"></a>Dodaj logowanie do aplikacji Windows Desktop
Z punktu końcowego v2.0, można szybko dodać uwierzytelniania aplikacji klasycznych z obsługą oba osobistego konta Microsoft i konta firmowego lub szkolnego.  Umożliwia również aplikacji do bezpiecznego komunikowania się z wewnętrznej bazy danych sieci web interfejsu api, a także [Microsoft Graph](https://graph.microsoft.io) i kilka [Office 365 Unified API](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).

> [!NOTE]
> Nie wszystkie scenariusze Azure Active Directory (AD) i funkcji obsługiwanych przez punktu końcowego v2.0.  Aby ustalić, czy należy używać punktu końcowego v2.0, przeczytaj o [ograniczenia v2.0](active-directory-v2-limitations.md).
> 
> 

Aby uzyskać [natywnych aplikacji .NET, na urządzeniu z systemem](active-directory-v2-flows.md#mobile-and-native-apps), usługa Azure AD zapewnia biblioteki uwierzytelniania tożsamości firmy Microsoft lub MSAL.  Jedynym celem MSAL jego życia jest ułatwiają aplikację, aby uzyskać tokenów dla wywołania usługi sieci web.  Aby zademonstrować, jak łatwo jest, w tym miejscu będzie budujemy aplikację listy zadań do wykonania WPF .NET który:

* Pobiera dostęp do tokenów przy użyciu & loguje się użytkownik [protokół uwierzytelniania OAuth 2.0](active-directory-v2-protocols.md).
* Bezpieczne wywołania wewnętrznej bazy danych usługi sieci web listy zadań do wykonania, która jest również chroniony przez OAuth 2.0.
* Wylogowaniu użytkownika.

## <a name="download-sample-code"></a>Pobierz przykładowy kod
Kod używany w tym samouczku jest przechowywany [ w serwisie GitHub](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet).  Aby z niego skorzystać, można [pobrać szkielet aplikacji w formie .zip](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/skeleton.zip) lub sklonować szkielet:

    git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git

Ukończonej aplikacji znajduje się na końcu również w tym samouczku.

## <a name="register-an-app"></a>Rejestracja aplikacji
Utwórz nową aplikację na [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), lub wykonaj następujące [szczegółowe kroki](active-directory-v2-app-registration.md).  Upewnij się, że:

* Skopiuj **identyfikator aplikacji** przypisany do aplikacji, będzie on potrzebny wkrótce.
* Dodaj **Mobile** platformy aplikacji.

## <a name="install--configure-msal"></a>Instalowanie i konfigurowanie MSAL
Teraz, gdy masz aplikację w zarejestrowany z firmą Microsoft, można zainstalować MSAL i wpisz swój kod dotyczące tożsamości.  MSAL mieć możliwość komunikacji z punktem końcowym v2.0, należy dostarczyć informacji o rejestracji aplikacji.

* Rozpocznij, dodając MSAL do projektu TodoListClient przy użyciu konsoli Menedżera pakietów.

```
PM> Install-Package Microsoft.Identity.Client -ProjectName TodoListClient -IncludePrerelease
```

* W projekcie TodoListClient Otwórz `app.config`.  Zastąp wartości elementów w `<appSettings>` sekcji, aby odzwierciedlić wartości wejściowych do portalu rejestracji aplikacji.  Kod będzie odwoływać tych wartości, przy każdym użyciu MSAL.
  
  * `ida:ClientId` Jest **identyfikator aplikacji** aplikacji został skopiowany z portalu.
* W projekcie usługi TodoList Otwórz `web.config` w katalogu głównym projektu.  
  
  * Zastąp `ida:Audience` wartości o takim samym **identyfikator aplikacji** z portalu.

## <a name="use-msal-to-get-tokens"></a>Aby uzyskać tokeny, użyj MSAL
Podstawową zasadą za MSAL jest, że zawsze, gdy Twoja aplikacja powinna tokenu dostępu, należy po prostu wywołać `app.AcquireToken(...)`, i MSAL zajmie się resztą.  

* W `TodoListClient` otwarciu projektu `MainWindow.xaml.cs` i Znajdź `OnInitialized(...)` metody.  Pierwszym krokiem jest zainicjowanie aplikacji `PublicClientApplication` -MSAL dla klasy podstawowej reprezentujący natywnych aplikacji.  Jest to, gdy przekazujesz MSAL współrzędne potrzebne do komunikowania się z usługą Azure AD i poinformuj go, jak pamięci podręcznej tokenów.

```csharp
protected override async void OnInitialized(EventArgs e)
{
        base.OnInitialized(e);

        app = new PublicClientApplication(new FileCache());
        AuthenticationResult result = null;
        ...
}
```

* Podczas uruchamiania aplikacji, chcemy do sprawdzenia, czy użytkownik jest już zalogowany do aplikacji.  Jednak nie chcemy jeszcze wywołania interfejsu użytkownika logowania — wybierzemy użytkownika, kliknij przycisk "Zarejestruj" Aby to zrobić.  Również w `OnInitialized(...)` metody:

```csharp
// As the app starts, we want to check to see if the user is already signed in.
// You can do so by trying to get a token from MSAL, using the method
// AcquireTokenSilent.  This forces MSAL to throw an exception if it cannot
// get a token for the user without showing a UI.
try
{
    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
    // If we got here, a valid token is in the cache - or MSAL was able to get a new oen via refresh token.
    // Proceed to fetch the user's tasks from the TodoListService via the GetTodoList() method.

    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "user_interaction_required")
    {
        // If user interaction is required, the app should take no action,
        // and simply show the user the sign in button.
    }
    else
    {
        // Here, we catch all other MsalExceptions
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }
}

```

* Jeśli użytkownik nie jest zalogowany i kliknięciu przycisku "Zaloguj", chcemy wywołania interfejsu użytkownika logowania i użytkownika, wprowadź swoje poświadczenia.  Implementowanie obsługi przycisk Zaloguj:

```csharp
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // TODO: Sign the user out if they clicked the "Clear Cache" button

// If the user clicked the 'Sign-In' button, force
// MSAL to prompt the user for credentials by using
// AcquireTokenAsync, a method that is guaranteed to show a prompt to the user.
// MSAL will get a token for the TodoListService and cache it for you.

AuthenticationResult result = null;
try
{
    result = await app.AcquireTokenAsync(new string[] { clientId });
    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    // If MSAL cannot get a token, it will throw an exception.
    // If the user canceled the login, it will result in the
    // error code 'authentication_canceled'.

    if (ex.ErrorCode == "authentication_canceled")
    {
        MessageBox.Show("Sign in was canceled by the user");
    }
    else
    {
        // An unexpected error occurred.
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }

        MessageBox.Show(message);
    }

    return;
}


}
```

* Jeśli użytkownik pomyślnie logowania, MSAL będą odbierać i pamięci podręcznej tokenu i można przejść do wywołania `GetTodoList()` metody bez obaw.  Pozostało można pobrać zadań użytkownika jest zaimplementowanie `GetTodoList()` metody.

```csharp
private async void GetTodoList()
{

AuthenticationResult result = null;
try
{
    // Here, we try to get an access token to call the TodoListService
    // without invoking any UI prompt.  AcquireTokenSilentAsync forces
    // MSAL to throw an exception if it cannot get a token silently.


    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
}
catch (MsalException ex)
{
    // MSAL couldn't get a token silently, so show the user a message
    // and let them click the Sign-In button.

    if (ex.ErrorCode == "user_interaction_required")
    {
        MessageBox.Show("Please sign in first");
        SignInButton.Content = "Sign In";
    }
    else
    {
        // In any other case, an unexpected error occurred.

        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }

    return;
}

// Once the token has been returned by MSAL,
// add it to the http authorization header,
// before making the call to access the To Do list service.

httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);


        ...
...


- When the user is done managing their To-Do List, they may finally sign out of the app by clicking the "Clear Cache" button.

```csharp
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // If the user clicked the 'clear cache' button,
        // clear the MSAL token cache and show the user as signed out.
        // It's also necessary to clear the cookies from the browser
        // control so the next user has a chance to sign in.

        if (SignInButton.Content.ToString() == "Clear Cache")
        {
                TodoList.ItemsSource = string.Empty;
                app.UserTokenCache.Clear(app.ClientId);
                ClearCookies();
                SignInButton.Content = "Sign In";
                return;
        }

        ...
```

## <a name="run"></a>Uruchom polecenie
Gratulacje! Masz teraz aplikacji .NET WPF pracy, który ma możliwość uwierzytelniania użytkowników i bezpiecznie wywoływać interfejsy API sieci Web przy użyciu protokołu OAuth 2.0.  Uruchom oba projekty i zaloguj się za pomocą osobistego konta Microsoft lub konta służbowego.  Dodawanie zadań do listy zadań do wykonania tego użytkownika.  Wyloguj się i zaloguj się ponownie jako inny użytkownik, aby wyświetlić ich listy zadań do wykonania.  Zamknij aplikację i uruchom je ponownie.  Zwróć uwagę, jak sesji użytkownika pozostanie niezmieniona — to aplikacja przechowuje w pamięci podręcznej tokenów w pliku lokalnym.

MSAL można łatwo zastosować wspólne funkcje tożsamości w aplikacji, za pomocą konta osobiste oraz firmowe.  Zapewnia obsługę pracy dirty dla Ciebie — Zarządzanie pamięci podręcznej, obsługa protokołu OAuth, przedstawiający użytkownika za pomocą interfejsu użytkownika, odświeżanie wygaśnięcia tokenów i inne nazwy logowania.  Tak naprawdę trzeba wiedzieć jednego wywołania interfejsu API, jest `app.AcquireTokenAsync(...)`.

Próbka ukończone (bez wartości konfiguracji) [jest dostarczane jako zip w tym miejscu](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip), lub można ją sklonować z serwisu GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

## <a name="next-steps"></a>Kolejne kroki
Możesz teraz przejść do bardziej zaawansowanych tematów.  Można spróbować:

* [Zabezpieczanie interfejsu API sieci Web TodoListService z punktem końcowym v2.0](active-directory-v2-devquickstarts-dotnet-api.md)

Aby uzyskać dodatkowe zasoby Zobacz:  

* [Przewodnik dewelopera v2.0 >>](active-directory-appmodel-v2-overview.md)
* [Tag "msal" StackOverflow >>](http://stackoverflow.com/questions/tagged/msal)

## <a name="get-security-updates-for-our-products"></a>Pobierz aktualizacje zabezpieczeń naszych produktów
Firma Microsoft zachęca do przekazywania powiadomień o występujących incydentach zabezpieczeń poprzez wizytę na [tej stronie](https://technet.microsoft.com/security/dd252948) i subskrybowanie Doradczych alertów zabezpieczeń.

