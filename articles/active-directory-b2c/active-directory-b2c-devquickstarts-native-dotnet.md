---
title: Uwierzytelnianie konta, Edytuj profil w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Jak utworzyć aplikację pulpitu systemu Windows, która obejmuje logowania, rejestracji, i zarządzania profilami za pomocą usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 01/07/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 980d554d96796a673db13bb369337d90088e8a75
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711068"
---
# <a name="azure-ad-b2c-build-a-windows-desktop-app"></a>Usługa Azure AD B2C: Tworzenie aplikacji klasycznej systemu Windows
Za pomocą usługi Azure Active Directory (Azure AD) B2C, można dodać tożsamości samoobsługi zaawansowanych funkcji zarządzania do aplikacji pulpitu w kilku krótkich krokach. W tym artykule opisano sposób tworzenia aplikacji "Lista zadań do wykonania".NET systemu Windows Presentation Foundation (WPF), która zawiera użytkowników rejestrację, logowanie i zarządzanie profilami. Aplikacja będzie zawierać obsługę rejestracji i logowania przy użyciu nazwy użytkownika lub adres e-mail. Zawiera również obsługę rejestracji i logowania przy użyciu kont społecznościowych, takich jak Facebook i Google.

## <a name="get-an-azure-ad-b2c-directory"></a>Tworzenie katalogu usługi Azure AD B2C
Przed rozpoczęciem korzystania z usługi Azure AD B2C należy utworzyć katalog lub dzierżawę.  Katalog jest kontenerem dla wszystkich użytkowników, aplikacji, grup i innych elementów. Jeśli jeszcze go nie masz, [utwórz katalog usługi B2C](active-directory-b2c-get-started.md), zanim przejdziesz dalej.

## <a name="create-an-application"></a>Tworzenie aplikacji
Następnie musisz utworzyć aplikację w katalogu usługi B2C. Dzięki temu informacje wymagane do bezpiecznego komunikowania się z aplikacją będą przekazywane do usługi Azure AD. Aby utworzyć aplikację, postępuj zgodnie z [tymi instrukcjami](active-directory-b2c-app-registration.md).  Należy pamiętać o wykonaniu następujących czynności:

* Obejmują **klientami** w aplikacji.
* Kopiuj **identyfikator URI przekierowania** `urn:ietf:wg:oauth:2.0:oob`. Jest to domyślny adres URL dla tej próbki kodu.
* Skopiuj **Identyfikator aplikacji** przypisany do aplikacji. Będzie potrzebny później.

## <a name="create-your-policies"></a>Tworzenie zasad
W usłudze Azure AD B2C każde działanie użytkownika jest definiowane przy użyciu [zasad](active-directory-b2c-reference-policies.md). Ten przykładowy kod obejmuje trzy środowiska tożsamości: Zarejestruj się, zaloguj się i edytowanie profilu. Musisz utworzyć zasady dla każdego typu zgodnie z opisem w [artykule o zasadach](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Podczas tworzenia trzech zbiorów zasad należy koniecznie:

* W bloku dostawców tożsamości wybrać opcję **tworzenia konta przy użyciu identyfikatora użytkownika** lub **tworzenia konta przy użyciu adresu e-mail**.
* Wybrać wartość **Nazwa wyświetlana** i inne atrybuty tworzenia konta w zasadach tworzenia konta.
* Wybrać oświadczenia **Nazwa wyświetlana** i **Identyfikator obiektu** jako oświadczenia aplikacji dla wszystkich zasad. Można również wybrać inne oświadczenia.
* Skopiować każdą utworzoną wartość **Nazwa** zasad. Powinny zawierać prefiks `b2c_1_`.  Te nazwy zasad będą potrzebne później.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Po pomyślnym utworzeniu trzech zasad możesz rozpocząć tworzenie aplikacji.

## <a name="download-the-code"></a>Pobieranie kodu
Kod używany w tym samouczku [jest przechowywany w serwisie GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet). Aby samodzielnie tworzyć przykładowy kod w trakcie pracy, możesz [pobrać plik ZIP ze szkieletem projektu](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip). Można również sklonować szkielet:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

Ukończona aplikacja jest również [dostępna jako plik ZIP](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip) lub w gałęzi `complete` tego samego repozytorium.

Po pobraniu przykładu kodu otwórz plik SLN programu Visual Studio, aby rozpocząć. `TaskClient` Projekt jest aplikacji klasycznych WPF, którą użytkownik wchodzi w interakcję z. Do celów tego samouczka wywołuje zadań zaplecza interfejsu API sieci web, hostowana na platformie Azure, która przechowuje listy zadań do wykonania poszczególnych użytkowników.  Nie jest potrzebne do tworzenia interfejsu API sieci web, mamy już będzie działać dla Ciebie.

Aby dowiedzieć się, jak interfejsu API sieci web bezpiecznie uwierzytelnia żądania przy użyciu usługi Azure AD B2C, zapoznaj się [interfejsu API sieci web wprowadzenie artykułu](active-directory-b2c-devquickstarts-api-dotnet.md).

## <a name="execute-policies"></a>Zasady wykonywania
Aplikacja komunikuje się z usługi Azure AD B2C przez wysyłanie komunikatów uwierzytelniania, które określają zasady, które mają być wykonywane jako część żądania HTTP. Dla aplikacji .NET, korzystając z podglądu biblioteki uwierzytelniania firmy Microsoft (MSAL) do wysyłania wiadomości uwierzytelniania OAuth 2.0, należy wykonać zasad i uzyskiwać tokeny, które mogą wywoływać interfejsy API sieci web.

### <a name="install-msal"></a>Zainstaluj MSAL
Dodaj MSAL do `TaskClient` projektu przy użyciu konsoli Menedżera pakietów programu Visual Studio.

```
PM> Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="enter-your-b2c-details"></a>Wprowadzanie szczegółów B2C
Otwórz plik `Globals.cs` i Zastąp wartości właściwości własne. Ta klasa jest używana w całym `TaskClient` wartości odwołania często używane.

```csharp
public static class Globals
{
    ...

    // TODO: Replace these five default with your own configuration values
    public static string tenant = "fabrikamb2c.onmicrosoft.com";
    public static string clientId = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
    public static string signInPolicy = "b2c_1_sign_in";
    public static string signUpPolicy = "b2c_1_sign_up";
    public static string editProfilePolicy = "b2c_1_edit_profile";

    ...
}
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### <a name="create-the-publicclientapplication"></a>Utwórz PublicClientApplication
Podstawowy klasa MSAL jest `PublicClientApplication`. Ta klasa reprezentuje aplikacji w systemie Azure AD B2C. Gdy initalizes aplikacji, Utwórz wystąpienie `PublicClientApplication` w `MainWindow.xaml.cs`. Może być używany w oknie.

```csharp
protected async override void OnInitialized(EventArgs e)
{
    base.OnInitialized(e);

    pca = new PublicClientApplication(Globals.clientId)
    {
        // MSAL implements an in-memory cache by default.  Since we want tokens to persist when the user closes the app,
        // we've extended the MSAL TokenCache and created a simple FileCache in this app.
        UserTokenCache = new FileCache(),
    };

    ...
```

### <a name="initiate-a-sign-up-flow"></a>Inicjowanie przepływu rejestracji
Jeśli użytkownik zdecyduje się loguje się, chcesz zainicjować przepływu rejestracji, który korzysta z utworzonymi zasadami tworzenia konta. Za pomocą MSAL, możesz po prostu Wywołaj `pca.AcquireTokenAsync(...)`. Parametry są przekazywane do `AcquireTokenAsync(...)` określić token, który zostanie wyświetlony, używane w żądanie uwierzytelnienia i inne zasady.

```csharp
private async void SignUp(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        // Use the app's clientId here as the scope parameter, indicating that
        // you want a token to the your app's backend web API (represented by
        // the cloud hosted task API).  Use the UiOptions.ForceLogin flag to
        // indicate to MSAL that it should show a sign-up UI no matter what.
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                Globals.signUpPolicy);

        // Upon success, indicate in the app that the user is signed in.
        SignInButton.Visibility = Visibility.Collapsed;
        SignUpButton.Visibility = Visibility.Collapsed;
        EditProfileButton.Visibility = Visibility.Visible;
        SignOutButton.Visibility = Visibility.Visible;

        // When the request completes successfully, you can get user
        // information from the AuthenticationResult
        UsernameLabel.Content = result.User.Name;

        // After the sign up successfully completes, display the user's To-Do List
        GetTodoList();
    }

    // Handle any exeptions that occurred during execution of the policy.
    catch (MsalException ex)
    {
        if (ex.ErrorCode != "authentication_canceled")
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

### <a name="initiate-a-sign-in-flow"></a>Inicjowanie przepływu logowania
W ten sam sposób, aby zainicjować tworzenia konta przepływu można zainicjować przepływu logowania. Kiedy użytkownik się zaloguje, należy to samo wywołanie do MSAL, teraz za pomocą zasad logowania:

```csharp
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.signInPolicy);
        ...
```

### <a name="initiate-an-edit-profile-flow"></a>Zainicjować przepływ edycji profilu
Ponownie możesz wykonać edycji profil zasady w taki sam sposób:

```csharp
private async void EditProfile(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.editProfilePolicy);
```

We wszystkich tych przypadkach MSAL albo zwraca token w `AuthenticationResult` lub zgłasza wyjątek. Zawsze uzyskać token z MSAL, można użyć `AuthenticationResult.User` obiekt, aby zaktualizować dane użytkownika w aplikacji, takich jak interfejsu użytkownika. Biblioteka ADAL buforuje token do użycia w innych częściach aplikacji.

### <a name="check-for-tokens-on-app-start"></a>Sprawdź, czy tokeny przy uruchamianiu aplikacji
Umożliwia także MSAL do śledzenia stanu logowania użytkownika.  W tej aplikacji chcemy użytkownik pozostaje zalogowany, nawet po Zamknij aplikację i otwórz go ponownie.  Ponownie wewnątrz `OnInitialized` zastąpienia, użyj tego MSAL `AcquireTokenSilent` metodę sprawdzania dla pamięci podręcznej tokenów:

```csharp
AuthenticationResult result = null;
try
{
    // If the user has has a token cached with any policy, we'll display them as signed-in.
    TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
    string existingPolicy = tci == null ? null : tci.Policy;
    result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    SignInButton.Visibility = Visibility.Collapsed;
    SignUpButton.Visibility = Visibility.Collapsed;
    EditProfileButton.Visibility = Visibility.Visible;
    SignOutButton.Visibility = Visibility.Visible;
    UsernameLabel.Content = result.User.Name;
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "failed_to_acquire_token_silently")
    {
        // There are no tokens in the cache.  Proceed without calling the To Do list service.
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
```

## <a name="call-the-task-api"></a>Wywołanie interfejsu API zadań
MSAL ma teraz używane do wykonywania zasad i uzyskiwać tokeny.  Za jego pomocą tokeny te wywołania interfejsu API zadań, należy ponownie można użyć w MSAL `AcquireTokenSilent` metodę sprawdzania dla pamięci podręcznej tokenów:

```csharp
private async void GetTodoList()
{
    AuthenticationResult result = null;
    try
    {
        // Here we want to check for a cached token, independent of whatever policy was used to acquire it.
        TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
        string existingPolicy = tci == null ? null : tci.Policy;

        // Use AcquireTokenSilent to indicate that MSAL should throw an exception if a token cannot be acquired
        result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    }
    // If a token could not be acquired silently, we'll catch the exception and show the user a message.
    catch (MsalException ex)
    {
        // There is no access token in the cache, so prompt the user to sign-in.
        if (ex.ErrorCode == "failed_to_acquire_token_silently")
        {
            MessageBox.Show("Please sign up or sign in first");
            SignInButton.Visibility = Visibility.Visible;
            SignUpButton.Visibility = Visibility.Visible;
            EditProfileButton.Visibility = Visibility.Collapsed;
            SignOutButton.Visibility = Visibility.Collapsed;
            UsernameLabel.Content = string.Empty;
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
    ...
```

Po wywołaniu `AcquireTokenSilentAsync(...)` zakończy się pomyślnie i token został znaleziony w pamięci podręcznej, można dodać token do `Authorization` nagłówka żądania HTTP. Interfejs API sieci web zadań będzie używają tego nagłówka do uwierzytelniania żądań odczytu listy zadań do wykonania użytkownika:

```csharp
    ...
    // Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

    // Call the To Do list service.
    HttpResponseMessage response = await httpClient.GetAsync(Globals.taskServiceUrl + "/api/tasks");
    ...
```

## <a name="sign-the-user-out"></a>Wylogowanie użytkownika
Ponadto umożliwia MSAL kończenie sesji użytkownika z aplikacją, gdy użytkownik wybierze **Wyloguj**.  Korzystając z MSAL, jest to osiągane przez wyczyszczenie wszystkich tokenów z pamięci podręcznej tokenu:

```csharp
private void SignOut(object sender, RoutedEventArgs e)
{
    // Clear any remnants of the user's session.
    pca.UserTokenCache.Clear(Globals.clientId);

    // This is a helper method that clears browser cookies in the browser control that MSAL uses, it is not part of MSAL.
    ClearCookies();

    // Update the UI to show the user as signed out.
    TaskList.ItemsSource = string.Empty;
    SignInButton.Visibility = Visibility.Visible;
    SignUpButton.Visibility = Visibility.Visible;
    EditProfileButton.Visibility = Visibility.Collapsed;
    SignOutButton.Visibility = Visibility.Collapsed;
    return;
}
```

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji
Na koniec Skompiluj i uruchom próbkę.  Utwórz aplikację za pomocą nazwa użytkownika lub adres e-mail. Wyloguj się i zaloguj się ponownie jako ten sam użytkownik. Edytuj profil użytkownika. Wyloguj się i zaloguj przy użyciu innego użytkownika.

## <a name="add-social-idps"></a>Dodaj IDPs społecznościowych
Obecnie aplikacji obsługuje wyłącznie użytkownika rejestracji i logowania używanego **kont lokalnych**. Oto konta przechowywane w katalogu usługi B2C, korzystających z nazwy użytkownika i hasła. Za pomocą usługi Azure AD B2C, można dodać obsługę innych dostawców tożsamości (IDPs) bez zmieniania żadnego kodu.

Aby dodać społecznościowych IDPs do aplikacji, Rozpocznij zgodnie z instrukcjami szczegółowe w tych artykułach. Dla każdego dostawców tożsamości, które mają być obsługiwane musisz zarejestrować aplikację w tym systemie i Uzyskaj identyfikator klienta.

* [Konfigurowanie usługi Facebook jako dostawca tożsamości](active-directory-b2c-setup-fb-app.md)
* [Konfigurowanie usługi Google jako dostawca tożsamości](active-directory-b2c-setup-goog-app.md)
* [Konfigurowanie usługi Amazon jako dostawca tożsamości](active-directory-b2c-setup-amzn-app.md)
* [Konfigurowanie LinkedIn jako dostawca tożsamości](active-directory-b2c-setup-li-app.md)

Po dodaniu dostawców tożsamości do katalogu usługi B2C należy edytować każdego z trzech zbiorów zasad uwzględnienie nowych IDPs, zgodnie z opisem w [artykule o zasadach](active-directory-b2c-reference-policies.md). Po zapisaniu zasad, uruchom ponownie aplikację. Powinien zostać wyświetlony nowy IDPs, dodawane jako logowania oraz wrażenia opcji zapisywania w każdym z Twoją tożsamość.

Możesz eksperymentować z zasadami i obserwować efekty w aplikacji przykładowej. Dodaj lub usuń IDPs, manipulować oświadczenia aplikacji lub zmień atrybuty rejestracji. Eksperymentu, aż zostanie wyświetlony, jak zasady, żądania uwierzytelniania i MSAL powiązać razem.

Próbka ukończone [jest dostępna jako plik .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip). Można ją także sklonować z serwisu GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```
