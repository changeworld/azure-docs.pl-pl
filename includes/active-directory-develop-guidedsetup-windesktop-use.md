---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 13497c8be578990b58cd6d6524eb0e945f8619c2
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59498430"
---
## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Użycia biblioteki MSAL do pobrania tokenu dla interfejsu API programu Microsoft Graph

W tej sekcji użycia biblioteki MSAL można pobrać tokenu dla interfejsu API programu Microsoft Graph.

1. W *MainWindow.xaml.cs* plików, Dodaj odwołanie do biblioteki MSAL do klasy:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Zastąp `MainWindow` klasy kod następującym kodem:

    ```csharp
    public partial class MainWindow : Window
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };


        public MainWindow()
        {
            InitializeComponent();
        }

      /// <summary>
        /// Call AcquireToken - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;
            var app = App.PublicClientApp;
            ResultText.Text = string.Empty;
            TokenInfoText.Text = string.Empty;

            var accounts = await app.GetAccountsAsync();
            var firstAccount = accounts.FirstOrDefault();

            try
            {
                authResult = await app.AcquireTokenSilent(scopes, firstAccount)
                    .ExecuteAsync();
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilent.
                // This indicates you need to call AcquireTokenInteractive to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                try
                {
                    authResult = await app.AcquireTokenInteractive(scopes, this)
                        .WithAccount(accounts.FirstOrDefault())
                        .WithPrompt(Prompt.SelectAccount)
                        .ExecuteAsync();
                }
                catch (MsalException msalex)
                {
                    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
                }
            }
            catch (Exception ex)
            {
                ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
                return;
            }

            if (authResult != null)
            {
                ResultText.Text = await GetHttpContentWithToken(graphAPIEndpoint, authResult.AccessToken);
                DisplayBasicTokenInfo(authResult);
                this.SignOutButton.Visibility = Visibility.Visible;
            }
        }
    ```

<!--start-collapse-->
### <a name="more-information"></a>Więcej informacji

#### <a name="get-a-user-token-interactively"></a>Interaktywne pobieranie tokenu użytkownika

Wywoływanie `AcquireTokenInteractive` metoda wyniki w oknie, który monituje użytkowników, aby zalogować się. Aplikacje zwykle wymagają od użytkowników zalogować się interaktywnie potrzebują dostępu do chronionego zasobu po raz pierwszy. Może być muszą się zalogować, gdy dyskretnej operację, aby uzyskać token nie powiodło się (na przykład, po wygaśnięciu hasła użytkownika).

#### <a name="get-a-user-token-silently"></a>Dyskretne pobieranie tokenu użytkownika

`AcquireTokenSilent` Obsługiwała nabycia tokenu i odnowienia bez żadnej interakcji użytkownika. Po `AcquireTokenInteractive` jest wykonywany po raz pierwszy `AcquireTokenSilent` jest zwykle metody służące do uzyskiwania tokenów, uzyskujących dostęp do chronionych zasobów dla kolejnych wywołań, ponieważ do żądania lub odnowienia tokenów wywołań dyskretnie.

Po pewnym czasie `AcquireTokenSilent` metoda zakończy się niepowodzeniem. Przyczyny niepowodzenia może być, że użytkownik jest wylogowany lub zmienić swoje hasło na innym urządzeniu. Gdy biblioteki MSAL wykryje, że ten problem można rozwiązać, wymagając akcję interaktywne, jest on uruchamiany `MsalUiRequiredException` wyjątku. Aplikacja może obsłużyć ten wyjątek na dwa sposoby:

* Może sprawić, że wywołanie względem `AcquireTokenInteractive` natychmiast. To wywołanie powoduje monitowanie użytkownika do logowania. Ten wzorzec jest zwykle używany w aplikacjach w trybie online w przypadku, gdy brak zawartości w trybie offline dostępne dla użytkownika. Przykład wygenerowanych za pomocą tego Instalatora z przewodnikiem następuje tego wzorca, który można wyświetlić w czasie działania pierwszego wykonania próbki. 

* Ponieważ żaden użytkownik nie ma korzystali z aplikacji, `PublicClientApp.Users.FirstOrDefault()` zawiera wartość null i `MsalUiRequiredException` wyjątku. 

* Następnie kod w przykładzie obsługuje wyjątek, wywołując `AcquireTokenInteractive`, które powoduje monitowanie użytkownika do logowania.

* Oznaczenia wizualne ona prezentować użytkownikom, którzy interakcyjnego logowania jest wymagana, zamiast tego, tak, aby użytkownik może wybrać chwila Aby zarejestrować się w. Lub aplikacji można ponowić próbę `AcquireTokenSilent` później. Ten wzorzec jest często używany, gdy użytkownicy mogą używać innych funkcji aplikacji bez przerw w działaniu — na przykład, jeśli zawartość w trybie offline jest dostępna w aplikacji. W takim przypadku użytkownicy mogą wybrać, chcąc Zaloguj się do dostępu do chronionego zasobu lub Odśwież nieaktualne informacje. Alternatywnie aplikacji zdecydować ponowić próbę `AcquireTokenSilent` po przywróceniu sieci po przejściu jest tymczasowo niedostępny.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Wywołanie interfejsu API programu Microsoft Graph przy użyciu tokenu, który został uzyskany

Dodaj następującą nową metodę do swojej `MainWindow.xaml.cs`. Metoda umożliwia `GET` żądania interfejsu API programu Graph przy użyciu nagłówka autoryzacji:

```csharp
/// <summary>
/// Perform an HTTP GET request to a URL using an HTTP Authorization header
/// </summary>
/// <param name="url">The URL</param>
/// <param name="token">The token</param>
/// <returns>String containing the results of the GET operation</returns>
public async Task<string> GetHttpContentWithToken(string url, string token)
{
    var httpClient = new System.Net.Http.HttpClient();
    System.Net.Http.HttpResponseMessage response;
    try
    {
        var request = new System.Net.Http.HttpRequestMessage(System.Net.Http.HttpMethod.Get, url);
        //Add the token in Authorization header
        request.Headers.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
        response = await httpClient.SendAsync(request);
        var content = await response.Content.ReadAsStringAsync();
        return content;
    }
    catch (Exception ex)
    {
        return ex.ToString();
    }
}
```

<!--start-collapse-->
### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Więcej informacji na temat wywołania chronionego interfejsu API REST

W tej przykładowej aplikacji, użyj `GetHttpContentWithToken` metody HTTP `GET` żądania względem chronionego zasobu, który wymaga tokenu, a następnie wróć zawartości do obiektu wywołującego. Metoda ta umożliwia dodanie uzyskano token w nagłówku autoryzacji HTTP. W tym przykładzie zasób jest interfejsu API programu Microsoft Graph *mnie* punktu końcowego, który wyświetla informacje o profilu użytkownika.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-a-user"></a>Dodaj metodę, aby się wylogować użytkownika

Aby się wylogować użytkownika, dodaj następującą metodę do swojej `MainWindow.xaml.cs` pliku:

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private async void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    var accounts = await App.PublicClientApp.GetAccountsAsync(); 

    if (accounts.Any())
    {
        try
        {
            await App.PublicClientApp.RemoveAsync(accounts.FirstOrDefault()); 
            this.ResultText.Text = "User has signed-out";
            this.CallGraphButton.Visibility = Visibility.Visible;
            this.SignOutButton.Visibility = Visibility.Collapsed;
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing-out user: {ex.Message}";
        }
    }
}
```

<!--start-collapse-->
### <a name="more-information-about-user-sign-out"></a>Więcej informacji na temat wylogowania użytkownika

`SignOutButton_Click` Metoda usuwa użytkowników z biblioteki MSAL użytkownika pamięci podręcznej, które skutecznie zawiera informacje dotyczące biblioteki MSAL zapomnieć bieżącego użytkownika, tak aby przyszłe żądania, aby uzyskać token powiedzie się tylko wtedy, gdy staje się interakcyjne.

Mimo że aplikacja, w tym przykładzie obsługuje pojedynczy użytkownicy, biblioteka MSAL obsługuje scenariusze, w którym może być podpisana wielu kont w tym samym czasie. Przykładem jest aplikacja poczty e-mail, gdy użytkownik ma wiele kont.
<!--end-collapse-->

## <a name="display-basic-token-information"></a>Wyświetlanie podstawowych informacji o tokenie

Aby wyświetlić podstawowe informacje o tokenie, dodaj następującą metodę do swojej *MainWindow.xaml.cs* pliku:

```csharp
/// <summary>
/// Display basic information contained in the token
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"Username: {authResult.Account.Username}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>Więcej informacji

Oprócz tokenu dostępu, który służy do wywoływania interfejsu API Microsoft Graph, po użytkownik loguje się biblioteka MSAL uzyskuje identyfikator tokenu. Ten token zawiera mały podzbiór informacje, które są odpowiednie dla użytkowników. `DisplayBasicTokenInfo` Metoda Wyświetla podstawowe informacje zawarte w tokenie. Na przykład wyświetla nazwę wyświetlaną tego użytkownika i identyfikator, tak jak Data wygaśnięcia tokenu i ciąg reprezentujący tokenu dostępu, sama. Możesz wybrać *wywołania interfejsu API Microsoft Graph* znajdujący się wiele razy i zobacz, że ten sam token został ponownie dla kolejnych żądań. Widać również zostanie przedłużony, gdy biblioteka MSAL postanawia go jest moment, aby odnowić token daty wygaśnięcia.
<!--end-collapse-->
