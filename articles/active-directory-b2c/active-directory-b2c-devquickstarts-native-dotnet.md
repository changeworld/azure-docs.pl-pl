---
title: Uwierzytelnianie, zarejestruj się, Edycja profilu w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Jak utworzyć aplikację pulpitu Windows, która obejmuje logowania, rejestracji, i zarządzania profilami za pomocą usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/07/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 43da5b32fe3ad8891f89544d0f9bdbd1d4d127d0
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/19/2018
ms.locfileid: "53606186"
---
# <a name="azure-ad-b2c-build-a-windows-desktop-app"></a>Usługa Azure AD B2C: Tworzenie aplikacji klasycznej dla systemu Windows
Za pomocą usługi Azure Active Directory (Azure AD) B2C, można dodać zaawansowane Samoobsługowe funkcje obsługi tożsamości zarządzania do aplikacji komputerowej w kilku krótkich krokach. W tym artykule pokazano sposób tworzenia aplikacji "Lista zadań do wykonania".NET Windows Presentation Foundation (WPF), która obejmuje rejestracji i logowania użytkowników i zarządzania profilami. Aplikacja będzie zawierać obsługę rejestracji i logowania za pomocą nazwy użytkownika lub adres e-mail. Zawiera również obsługę rejestracji i logowania za pomocą kont społecznościowych, takich jak Facebook i Google.

## <a name="get-an-azure-ad-b2c-directory"></a>Tworzenie katalogu usługi Azure AD B2C
Przed rozpoczęciem korzystania z usługi Azure AD B2C należy utworzyć katalog lub dzierżawę. Katalog jest kontenerem dla wszystkich użytkowników, aplikacji, grup i innych elementów. Jeśli jeszcze go nie masz, [utwórz katalog usługi B2C](active-directory-b2c-get-started.md), zanim przejdziesz dalej.

## <a name="create-an-application"></a>Tworzenie aplikacji
Następnie musisz utworzyć aplikację w katalogu usługi B2C. Dzięki temu informacje wymagane do bezpiecznego komunikowania się z aplikacją będą przekazywane do usługi Azure AD. Aby utworzyć aplikację, postępuj zgodnie z [tymi instrukcjami](active-directory-b2c-app-registration.md). Należy pamiętać o wykonaniu następujących czynności:

* Obejmują **klienta natywnego** w aplikacji.
* Kopiuj **identyfikator URI przekierowania** `urn:ietf:wg:oauth:2.0:oob`. Jest to domyślny adres URL dla tej próbki kodu.
* Skopiuj **Identyfikator aplikacji** przypisany do aplikacji. Będzie potrzebny później.

## <a name="create-your-policies"></a>Tworzenie zasad
W usłudze Azure AD B2C każde działanie użytkownika jest definiowane przy użyciu [zasad](active-directory-b2c-reference-policies.md). Ten przykładowy kod obejmuje trzy środowiska tożsamości: Zarejestruj się, zaloguj się i edytowanie profilu. Należy utworzyć zasady dla każdego typu zgodnie z opisem w [artykule dotyczącym struktury zasad](active-directory-b2c-reference-policies.md#create-a-sign-up-user-flow). Podczas tworzenia trzech zbiorów zasad należy koniecznie:

* W bloku dostawców tożsamości wybrać opcję **tworzenia konta przy użyciu identyfikatora użytkownika** lub **tworzenia konta przy użyciu adresu e-mail**.
* Wybrać wartość **Nazwa wyświetlana** i inne atrybuty tworzenia konta w zasadach tworzenia konta.
* Wybrać oświadczenia **Nazwa wyświetlana** i **Identyfikator obiektu** jako oświadczenia aplikacji dla wszystkich zasad. Można również wybrać inne oświadczenia.
* Skopiować każdą utworzoną wartość **Nazwa** zasad. Powinny zawierać prefiks `b2c_1_`. Te nazwy zasad będą potrzebne później.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Po pomyślnym utworzeniu trzech zasad możesz rozpocząć tworzenie aplikacji.

## <a name="download-the-code"></a>Pobieranie kodu
Kod używany w tym samouczku [jest przechowywany w serwisie GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet). Aby samodzielnie tworzyć przykładowy kod w trakcie pracy, możesz [pobrać plik ZIP ze szkieletem projektu](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip). Można również sklonować szkielet:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

Ukończona aplikacja jest również [dostępna jako plik ZIP](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip) lub w gałęzi `complete` tego samego repozytorium.

Po pobraniu przykładu kodu otwórz plik SLN programu Visual Studio, aby rozpocząć. `TaskClient` Projekt jest, użytkownik wchodzi w interakcję z aplikacji klasycznej WPF. Do celów tego samouczka wywołuje zadań zaplecza internetowego interfejsu API, które są hostowane na platformie Azure, który przechowuje listy zadań do wykonania poszczególnych użytkowników. Nie trzeba tworzyć internetowy interfejs API, mamy już będzie działać dla Ciebie.

Aby dowiedzieć się, jak internetowy interfejs API bezpiecznie przeprowadza uwierzytelnianie żądań przy użyciu usługi Azure AD B2C, zapoznaj się z [wprowadzenie interfejsu API sieci web do artykułu](active-directory-b2c-devquickstarts-api-dotnet.md).

## <a name="execute-policies"></a>Zasady wykonywania
Aplikacja komunikuje się za pomocą usługi Azure AD B2C, wysyłając komunikatów uwierzytelniania, które określają zasady, które mają być wykonywane jako część żądania HTTP. Dla aplikacji klasycznych .NET Microsoft Authentication Library (MSAL) w wersji zapoznawczej umożliwiają wysyłanie komunikatów uwierzytelniania OAuth 2.0, należy wykonać zasad i uzyskiwanie tokenów, które wywołują interfejsów API sieci web.

### <a name="install-msal"></a>Zainstaluj biblioteki MSAL
Dodawanie biblioteki MSAL do `TaskClient` projektu przy użyciu konsoli Menedżera pakietów Visual Studio.

```
PM> Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="enter-your-b2c-details"></a>Wprowadzanie szczegółów B2C
Otwórz plik `Globals.cs` i zastąpić wartości właściwości swoją własną. Ta klasa jest używana w całej `TaskClient` odwołania najczęściej używanych wartości.

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
Podstawową klasą Biblioteka MSAL jest `PublicClientApplication`. Ta klasa reprezentuje aplikację w systemie usługi Azure AD B2C. Gdy initalizes aplikacji, Utwórz wystąpienie obiektu `PublicClientApplication` w `MainWindow.xaml.cs`. Może to służyć w całym okna.

```csharp
protected async override void OnInitialized(EventArgs e)
{
    base.OnInitialized(e);

    pca = new PublicClientApplication(Globals.clientId)
    {
        // MSAL implements an in-memory cache by default. Since we want tokens to persist when the user closes the app,
        // we've extended the MSAL TokenCache and created a simple FileCache in this app.
        UserTokenCache = new FileCache(),
    };

    ...
```

### <a name="initiate-a-sign-up-flow"></a>Inicjowanie przepływu rejestracji
Gdy użytkownik zażąda ich podpisuje się, chcesz zainicjować rejestracji przepływ, który używa zasady tworzenia konta, który został utworzony. Za pomocą biblioteki MSAL, możesz po prostu Wywołaj `pca.AcquireTokenAsync(...)`. Parametry są przekazywane do `AcquireTokenAsync(...)` określić, której token zostanie wyświetlony, zasady stosowane w żądaniu uwierzytelnienia i nie tylko.

```csharp
private async void SignUp(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        // Use the app's clientId here as the scope parameter, indicating that
        // you want a token to the your app's backend web API (represented by
        // the cloud hosted task API). Use the UiOptions.ForceLogin flag to
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

### <a name="initiate-a-sign-in-flow"></a>Zainicjować przepływ logowania
Możesz zainicjować przepływ logowania w taki sam sposób, że zainicjować przepływu rejestracji. Gdy użytkownik loguje się, należy to samo wywołanie do biblioteki MSAL, tym razem przy użyciu zasad logowania:

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
Ponownie zasad profilu edycji można wykonywać w taki sam sposób:

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

We wszystkich tych przypadkach MSAL albo zwraca token w `AuthenticationResult` ani nie zgłasza wyjątku. Każdorazowo pobierał token z biblioteki MSAL, można użyć `AuthenticationResult.User` obiektu do zaktualizowania danych użytkownika w aplikacji, takich jak interfejs użytkownika. Biblioteka ADAL buforuje token do użytku w innych częściach aplikacji.

### <a name="check-for-tokens-on-app-start"></a>Sprawdź, czy tokeny w aplikacji w menu start
Można również użycia biblioteki MSAL do śledzenia stanu logowania użytkownika. W tej aplikacji chcemy, aby użytkownik pozostaje zalogowany, nawet po zamknąć aplikację, a następnie otwórz go ponownie. Zwrotne wewnątrz `OnInitialized` zastąpienia, użycia biblioteki MSAL firmy `AcquireTokenSilent` metoda pod kątem pamięci podręcznej tokenów:

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
        // There are no tokens in the cache. Proceed without calling the To Do list service.
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
Biblioteka MSAL mają teraz używane do wykonywania zasad i uzyskiwanie tokenów. Jeśli chcesz użyć jednego tokeny te służą do wywołania interfejsu API zadań, należy ponownie użyć biblioteki MSAL firmy `AcquireTokenSilent` metoda pod kątem pamięci podręcznej tokenów:

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

Po wywołaniu `AcquireTokenSilentAsync(...)` zakończy się pomyślnie i token znajduje się w pamięci podręcznej, można dodać token do `Authorization` nagłówka żądania HTTP. Zadanie interfejsu API sieci web użyje tego pliku nagłówkowego w celu uwierzytelnienia żądania, które można odczytać listy zadań do wykonania użytkownika:

```csharp
    ...
    // Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

    // Call the To Do list service.
    HttpResponseMessage response = await httpClient.GetAsync(Globals.taskServiceUrl + "/api/tasks");
    ...
```

## <a name="sign-the-user-out"></a>Wylogować użytkownika
Na koniec użycia biblioteki MSAL, aby zakończyć sesji użytkownika z aplikacją, gdy użytkownik wybierze **Wyloguj**. Korzystając z biblioteki MSAL, jest to realizowane przez wyczyszczenie wszystkich tokenów z pamięci podręcznej tokenu:

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
Na koniec Skompiluj i uruchom aplikację przykładową. Zarejestruj się w aplikacji przy użyciu nazwy użytkownika lub adres e-mail. Wyloguj się i zaloguj się ponownie jako tego samego użytkownika. Edytuj profil użytkownika. Wyloguj się i zaloguj przy użyciu innego użytkownika.

## <a name="add-social-idps"></a>Dodaj dostawców tożsamości społecznościowych
Obecnie aplikacja obsługuje tylko użytkownika rejestracji i logowania korzystające z **kont lokalnych**. Oto konta przechowywane w katalogu usługi B2C, używające nazwy użytkownika i hasła. Za pomocą usługi Azure AD B2C, można dodać obsługę innych dostawców tożsamości (IDPs) bez konieczności zmieniania żadnego kodu.

Aby dodać społecznościowych dostawców tożsamości do aplikacji, należy rozpocząć zgodnie z instrukcjami szczegółowe w następujących artykułach. Dla każdego dostawcy tożsamości, które mają być obsługiwane należy zarejestrować aplikację w tym systemie i uzyskać identyfikator klienta.

* [Konfigurowanie usługi Facebook jako dostawcy tożsamości](active-directory-b2c-setup-fb-app.md)
* [Konfigurowanie Google jako dostawcy tożsamości](active-directory-b2c-setup-goog-app.md)
* [Konfigurowanie Amazon jako dostawcy tożsamości](active-directory-b2c-setup-amzn-app.md)
* [Konfigurowanie usługi LinkedIn jako dostawcy tożsamości](active-directory-b2c-setup-li-app.md)

Po dodaniu dostawcy tożsamości do katalogu usługi B2C należy edytować każdy z trzech zbiorów zasad w celu uwzględnienia nowych dostawców tożsamości, zgodnie z opisem w [artykule dotyczącym struktury zasad](active-directory-b2c-reference-policies.md). Po zapisaniu zasad, należy ponownie uruchomić tę aplikację. Powinien zostać wyświetlony nowy dostawców tożsamości, dodany jako logowania i środowisk opcji rejestracji w każdym z Twoją tożsamość.

Możesz eksperymentować z zasadami dotyczącymi zasobów i obserwować wpływ na przykładowej aplikacji. Dodaj lub usuń dostawców tożsamości, manipulowania oświadczeń aplikacji lub zmień atrybuty tworzenia konta. Wypróbuj, aż zobaczysz, jak zasady, żądania uwierzytelniania i Biblioteka MSAL powiązać ze sobą.

Próbka ukończone [znajduje się w pliku .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip). Można ją także sklonować z serwisu GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```
