---
title: Microsoft Identity platform platformy UWP — wprowadzenie | Azure
description: Jak aplikacje platforma uniwersalna systemu Windows (platformy UWP) mogą wywołać interfejs API, który wymaga tokenów dostępu przez punkt końcowy platformy tożsamości firmy Microsoft.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: dae3f9f2f4a9f6966d3453123773887c244cf4e2
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334057"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Wywoływanie interfejsu API Microsoft Graph z aplikacji platforma uniwersalna systemu Windows (XAML)

> [!div renderon="docs"]

W tym przewodniku wyjaśniono sposób, w jaki aplikacja natywna platforma uniwersalna systemu Windows (platformy UWP) może zażądać tokenu dostępu, a następnie wywołać Microsoft Graph API. Przewodnik dotyczy również innych interfejsów API, które wymagają tokenów dostępu z punktu końcowego platformy tożsamości firmy Microsoft.

Na końcu tego przewodnika aplikacja wywołuje chroniony interfejs API przy użyciu kont osobistych. Przykłady to outlook.com, live.com i inne. Aplikacja wywołuje również konta służbowe z dowolnej firmy lub organizacji, która ma Azure Active Directory (Azure AD).

>[!NOTE]
> Ten przewodnik wymaga programu Visual Studio 2017 z zainstalowanym programowaniem platforma uniwersalna systemu Windows. Zobacz sekcję [Pobierz konfigurację](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) , aby uzyskać instrukcje dotyczące pobierania i konfigurowania programu Visual Studio do opracowywania aplikacji platforma uniwersalna systemu Windows.

## <a name="how-this-guide-works"></a>Jak działa ten przewodnik

![Pokazuje sposób działania przykładowej aplikacji wygenerowanej przez ten samouczek](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

W tym przewodniku przedstawiono Tworzenie przykładowej aplikacji platformy UWP, która wysyła zapytania do Microsoft Graph API lub interfejsu API sieci Web, który akceptuje tokeny z punktu końcowego platformy tożsamości firmy Microsoft. W tym scenariuszu token jest dodawany do żądań HTTP za pośrednictwem nagłówka autoryzacji. Biblioteka Microsoft Authentication Library (MSAL) obsługuje pozyskiwanie i odnawianie tokenów.

## <a name="nuget-packages"></a>Pakiety NuGet

W tym przewodniku są stosowane następujące pakiety NuGet:

|Biblioteka|Opis|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Biblioteka uwierzytelniania firmy Microsoft|

## <a name="set-up-your-project"></a>konfigurowanie projektu

Ta sekcja zawiera instrukcje krok po kroku dotyczące integrowania aplikacji .NET Desktop (XAML) z logowaniem się *z firmą Microsoft*. Następnie może wykonywać zapytania dotyczące internetowych interfejsów API, które wymagają tokenu, takiego jak Microsoft Graph API.

Ten przewodnik tworzy aplikację, która wyświetla przycisk, który wykonuje zapytania interfejs API programu Graph, przycisk Wyloguj i pola tekstowe, które wyświetlają wyniki wywołań.

> [!NOTE]
> Czy chcesz pobrać projekt tego przykładowego projektu programu Visual Studio? [Pobierz projekt](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip) i przejdź do kroku(#register-your-application "rejestracji aplikacji") do [rejestracji]aplikacji, aby skonfigurować przykładowy kod przed jego uruchomieniem.

### <a name="create-your-application"></a>Tworzenie aplikacji

1. W programie Visual Studio, wybierz **pliku** > **New** > **projektu**.
2. W obszarze **Szablony**wybierz **pozycję C#Wizualizacja** .
3. Wybierz pozycję **Pusta aplikacja (platforma uniwersalna systemu Windows)** .
4. Nadaj nazwę aplikacji, a następnie wybierz **przycisk OK**.
5. Jeśli zostanie wyświetlony monit, wybierz dowolną wersję dla wersji **docelowej** i **minimalnej** , a następnie wybierz **przycisk OK**.

    >![Wersje minimalne i docelowe](./media/tutorial-v2-windows-uwp/vs-minimum-target.png)

## <a name="add-microsoft-authentication-library-to-your-project"></a>Dodawanie biblioteki uwierzytelniania firmy Microsoft do projektu
1. W programie Visual Studio wybierz pozycję **Narzędzia** > **Menedżer pakietów NuGet** > **Konsola menedżera pakietów**.
2. Skopiuj i wklej następujące polecenie w oknie **konsola Menedżera pakietów** :

    ```powershell
    Install-Package Microsoft.Identity.Client -IncludePrerelease
    ```

> [!NOTE]
> To polecenie powoduje zainstalowanie [biblioteki uwierzytelniania firmy Microsoft](https://aka.ms/msal-net). MSAL uzyskuje, buforuje i odświeża tokeny użytkowników, które uzyskują dostęp do interfejsów API chronionych przez platformę tożsamości firmy Microsoft.

## <a name="create-your-applications-ui"></a>Tworzenie interfejsu użytkownika aplikacji

Plik **MainPage. XAML** jest tworzony automatycznie jako część szablonu projektu. Otwórz ten plik, a następnie postępuj zgodnie z instrukcjami:

* Zastąp węzeł **siatki** aplikacji następującym kodem:

    ```xml
    <Grid>
        <StackPanel Background="Azure">
            <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
                <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
                <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
            </StackPanel>
            <TextBlock Text="API Call Results" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
            <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
            <TextBlock Text="Token Info" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
            <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
        </StackPanel>
    </Grid>
    ```
    
## <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>Użyj MSAL, aby uzyskać token dla Microsoft Graph interfejsu API

W tej sekcji pokazano, jak uzyskać token Microsoft Graph API przy użyciu programu MSAL.

1.  W **MainPage.XAML.cs**, Dodaj odwołanie do MSAL do klasy:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Zastąp kod <code>MainPage</code> klasy następującym kodem:

    ```csharp
    public sealed partial class MainPage : Page
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };

        // Below are the clientId (Application Id) of your app registration and the tenant information. 
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - Te content of Tenant by the information about the accounts allowed to sign-in in your application:
        //   - For Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use organizations
        //   - for any Work or School accounts, or Microsoft personal account, use common
        //   - for Microsoft Personal account, use consumers
        private const string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";        

        public IPublicClientApplication PublicClientApp { get; } 

        public MainPage()
        {
          this.InitializeComponent();

          PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AadAuthorityAudience.AzureAdAndPersonalMicrosoftAccount)
                .WithLogging((level, message, containsPii) =>
                {
                    Debug.WriteLine($"MSAL: {level} {message} ");
                }, LogLevel.Warning, enablePiiLogging:false,enableDefaultPlatformLogging:true)
                .WithUseCorporateNetwork(true)
                .Build();
        }

        /// <summary>
        /// Call AcquireTokenInteractive - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
         AuthenticationResult authResult = null;
         ResultText.Text = string.Empty;
         TokenInfoText.Text = string.Empty;

         // It's good practice to not do work on the UI thread, so use ConfigureAwait(false) whenever possible.            
         IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false); 
         IAccount firstAccount = accounts.FirstOrDefault();

         try
         {
          authResult = await PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                                  .ExecuteAsync();
         }
         catch (MsalUiRequiredException ex)
         {
          // A MsalUiRequiredException happened on AcquireTokenSilent.
          // This indicates you need to call AcquireTokenInteractive to acquire a token
          System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

          try
          {
           authResult = await PublicClientApp.AcquireTokenInteractive(scopes)
                                                      .ExecuteAsync()
                                                      .ConfigureAwait(false);
           }
           catch (MsalException msalex)
           {
            await DisplayMessageAsync($"Error Acquiring Token:{System.Environment.NewLine}{msalex}");
           }
          }
          catch (Exception ex)
          {
           await DisplayMessageAsync($"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}");
           return;
          }

          if (authResult != null)
          {
           var content = await GetHttpContentWithToken(graphAPIEndpoint,
                                                       authResult.AccessToken).ConfigureAwait(false);

           // Go back to the UI thread to make changes to the UI
           await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
           {
            ResultText.Text = content;
            DisplayBasicTokenInfo(authResult);
            this.SignOutButton.Visibility = Visibility.Visible;
           });
          }
        }
    ```

### <a name="more-information"></a>Więcej informacji

#### <a name="get-a-user-token-interactively"></a>Interaktywne pobieranie tokenu użytkownika

Wywołanie `AcquireTokenInteractive` metody powoduje pojawienie się okna z monitowaniem użytkowników o zalogowanie się. Aplikacje zwykle wymagają od użytkowników logowania się interaktywnie przy pierwszym dostępie do chronionego zasobu. Może być również konieczne zalogowanie się, gdy operacja dyskretna uzyskiwania tokenu nie powiedzie się. Przykładem jest wygaśnięcie hasła użytkownika.

#### <a name="get-a-user-token-silently"></a>Dyskretne pobieranie tokenu użytkownika

`AcquireTokenSilent` Metoda obsługuje pozyskiwanie i odnawianie tokenów bez żadnej interakcji z użytkownikiem. Gdy `AcquireTokenInteractive` jest wykonywane po raz pierwszy, a użytkownik zostanie poproszony o podanie poświadczeń, `AcquireTokenSilent` Metoda ta powinna być używana do żądania tokenów dla kolejnych wywołań, ponieważ uzyskuje tokeny dyskretnie. MSAL będzie obsługiwać pamięć podręczną i odnawianie tokenów.

`AcquireTokenSilent` Ostatecznie Metoda kończy się niepowodzeniem. Przyczyną niepowodzenia może być wylogowanie lub zmiana hasła przez użytkowników na innym urządzeniu. Gdy MSAL wykryje, że problem może zostać rozwiązany przez wymaganie interaktywnej akcji, `MsalUiRequiredException` wyzwala wyjątek. Aplikacja może obsłużyć ten wyjątek na dwa sposoby:

* Może to spowodować `AcquireTokenInteractive` natychmiastowe wywołanie. To wywołanie powoduje wyświetlenie monitu o zalogowanie użytkownika. Zwykle ten wzorzec jest używany w aplikacjach online, w przypadku których użytkownik nie ma dostępnej zawartości w trybie offline. Przykład wygenerowany przez tę konfigurację z przewodnikiem jest zgodny ze wzorcem. Zobaczysz ją w działaniu przy pierwszym uruchomieniu przykładu.
  * Ponieważ żaden użytkownik nie użył aplikacji, `accounts.FirstOrDefault()` zawiera wartość null `MsalUiRequiredException` i zgłaszany jest wyjątek.
  * Kod w przykładzie przechwytuje wyjątek przez wywołanie `AcquireTokenInteractive`. To wywołanie powoduje wyświetlenie monitu o zalogowanie użytkownika.

* Zamiast tego przedstawia wskazanie wizualizacji użytkownikom, że wymagane jest logowanie interakcyjne. Następnie mogą wybrać odpowiedni czas na zalogowanie się. Lub aplikacja może ponowić próbę `AcquireTokenSilent` później. Często ten wzorzec jest używany, gdy użytkownicy mogą korzystać z innych funkcji aplikacji bez zakłóceń. Przykładem jest to, że zawartość w trybie offline jest dostępna w aplikacji. W takim przypadku użytkownicy mogą zdecydować się na zalogowanie się w celu uzyskania dostępu do chronionego zasobu lub odświeżenia nieaktualnych informacji. Lub w przeciwnym razie aplikacja może zdecydować się `AcquireTokenSilent` na ponowienie próby, gdy sieć zostanie przywrócona, gdy jest ona tymczasowo niedostępna.

## <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Wywoływanie interfejsu API Microsoft Graph przy użyciu właśnie uzyskanego tokenu

* Dodaj następującą nową metodę do **MainPage.XAML.cs**. Ta metoda jest używana do `GET` żądania odinterfejs API programu Graph przy `Authorization` użyciu nagłówka:

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
            // Add the token in Authorization header
            request.Headers.Authorization = 
              new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
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

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Więcej informacji na temat tworzenia wywołania REST w ramach chronionego interfejsu API

W tej przykładowej aplikacji `GetHttpContentWithToken` Metoda jest używana do wykonania żądania HTTP `GET` względem chronionego zasobu, który wymaga tokenu. Następnie metoda zwraca zawartość do obiektu wywołującego. Ta metoda dodaje token uzyskany w nagłówku **autoryzacji http** . Dla tego przykładu zasób jest punktem końcowym usługi  Microsoft Graph API, który wyświetla informacje o profilu użytkownika.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Dodawanie metody w celu wylogowania użytkownika

* Aby wylogować użytkownika, Dodaj następującą metodę do **MainPage.XAML.cs**:

    ```csharp
    /// <summary>
    /// Sign out the current user
    /// </summary>
    private async void SignOutButton_Click(object sender, RoutedEventArgs e)
    {
        IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync
                                                              .ConfigureAwait(false);
        IAccount firstAccount = accounts.FirstOrDefault();

        try
        {
            await PublicClientApp.RemoveAsync(firstAccount).ConfigureAwait(false);
            await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
            {
                ResultText.Text = "User has signed-out";
                this.CallGraphButton.Visibility = Visibility.Visible;
                    this.SignOutButton.Visibility = Visibility.Collapsed;
                });
            }
            catch (MsalException ex)
            {
                ResultText.Text = $"Error signing-out user: {ex.Message}";
            }
        }
    ```

> [!NOTE]
> MSAL.NET korzysta z metod asynchronicznych w celu uzyskania tokenów lub manipulowania kontami, w związku z czym należy zadbać o wykonywanie akcji interfejsu użytkownika w wątku `Dispatcher.RunAsync`interfejsu użytkownika, a tym samym, i środków zaradczych do wywołania`ConfigureAwait(false)`

### <a name="more-information-on-sign-out"></a>Więcej informacji na temat wylogowywania

`SignOutButton_Click` Metoda usuwa użytkownika z pamięci podręcznej użytkownika MSAL. Ta metoda skutecznie informuje MSAL o zapomnieniu bieżącego użytkownika. Następnie przyszłe żądanie pozyskania tokenu powiedzie się tylko wtedy, gdy zostanie ono wykonane interaktywnie.
Aplikacja w tym przykładzie obsługuje jednego użytkownika. Ale MSAL obsługuje scenariusze, w których może być zalogowanych więcej niż jedno konto w tym samym czasie. Przykładem jest aplikacja poczty e-mail, w której użytkownik ma kilka kont.

## <a name="display-basic-token-information"></a>Wyświetl podstawowe informacje o tokenie

* Dodaj następującą metodę do **MainPage.XAML.cs** , aby wyświetlić podstawowe informacje o tokenie:

    ```csharp
    /// <summary>
    /// Display basic information contained in the token. Needs to be called from the UI thead.
    /// </summary>
    private void DisplayBasicTokenInfo(AuthenticationResult authResult)
    {
        TokenInfoText.Text = "";
        if (authResult != null)
        {
            TokenInfoText.Text += $"User Name: {authResult.Account.Username}" + Environment.NewLine;
            TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
        }
    }
    ```

### <a name="more-information"></a>Więcej informacji

Tokeny identyfikatorów nabyte za pośrednictwem programu **OpenID Connect Connect** zawierają również niewielki podzbiór informacji przydatnych dla użytkownika. `DisplayBasicTokenInfo`Wyświetla podstawowe informacje zawarte w tokenie. Przykłady to nazwa wyświetlana i identyfikator użytkownika, Data wygaśnięcia tokenu oraz ciąg, który reprezentuje token dostępu. W przypadku wybrania przycisku **Wywołaj Microsoft Graph interfejsu API** kilka razy zobaczysz, że ten sam token został ponownie użyty dla kolejnych żądań. Można także zobaczyć datę wygaśnięcia rozszerzoną, gdy MSAL zdecyduje, że czas odnowienia tokenu.

## <a name="register-your-application"></a>Rejestrowanie aplikacji

Teraz musisz zarejestrować aplikację w portalu rejestracji aplikacji firmy Microsoft:

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. Jeśli Twoje konto znajduje się w więcej niż jednej dzierżawie usługi Azure AD `Directory + Subscription` , wybierz pozycję w prawym górnym rogu menu u góry strony i przejdź do odpowiedniej dzierżawy usługi Azure AD.
1. Przejdź do strony Microsoft Identity Platform for Developers [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) .
1. Wybierz pozycję **Nowa rejestracja**.
   - W sekcji **Nazwa** podaj znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład `UWP-App-calling-MSGraph`.
   - W sekcji **obsługiwane typy kont** wybierz pozycję **konta w dowolnym katalogu organizacyjnym i osobiste konta Microsoft (np. Skype, Xbox, Outlook.com)** .
   - Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.
1. Na stronie **Przegląd** aplikacji Znajdź wartość **Identyfikator aplikacji (klienta)** i Zapisz ją jako nowszą. Wróć do programu Visual Studio, Otwórz **MainPage.XAML.cs**i Zastąp wartość ClientId identyfikatorem aplikacji, którą właśnie zarejestrowano:
1. Na liście stron dla aplikacji wybierz pozycję **Uwierzytelnianie**.
   1. W sekcji **identyfikatory URI przekierowania** na liście identyfikatorów URI przekierowania:
   1. W kolumnie **Typ** wybierz pozycję **Klient publiczny (Mobile & Desktop)** .
   1. Wprowadź `urn:ietf:wg:oauth:2.0:oob` wartość w kolumnie **Identyfikator URI przekierowania** .
1. Wybierz pozycję **Zapisz**.
1. Na liście stron dla aplikacji wybierz pozycję **uprawnienia interfejsu API**
   - Kliknij przycisk **Dodaj uprawnienia** , a następnie
   - Upewnij się, że wybrano kartę **Microsoft API**
   - W sekcji *najczęściej używane interfejsy API firmy Microsoft* kliknij pozycję **Microsoft Graph**
   - W sekcji **uprawnienia delegowane** upewnij się, że są zaznaczone odpowiednie uprawnienia: **User. Read**. W razie potrzeby użyj pola wyszukiwania.
   - Wybierz przycisk **Dodaj uprawnienia**

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Włącz uwierzytelnianie zintegrowane w domenach federacyjnych (opcjonalnie)

Aby włączyć uwierzytelnianie zintegrowane systemu Windows, gdy jest ono używane z federacyjną domeną usługi Azure AD, manifest aplikacji musi włączyć dodatkowe możliwości:

1. Kliknij dwukrotnie pozycję **Package. appxmanifest**.
2. Wybierz kartę **możliwości** i upewnij się, że są włączone następujące ustawienia:

    - Uwierzytelnianie w przedsiębiorstwie
    - Sieci prywatne (serwer & klienta)
    - Udostępnione certyfikaty użytkowników

> [!IMPORTANT]
> [Zintegrowane uwierzytelnianie systemu Windows](https://aka.ms/msal-net-iwa) nie jest domyślnie skonfigurowane dla tego przykładu. Aplikacje, które żądają funkcji *uwierzytelnianie przedsiębiorstwa* lub *udostępnione certyfikaty użytkownika* , wymagają wyższego poziomu weryfikacji w Sklepie Windows. Ponadto nie wszyscy deweloperzy chcą przeprowadzić wyższy poziom weryfikacji. Włącz to ustawienie tylko wtedy, gdy wymagane jest zintegrowane uwierzytelnianie systemu Windows z federacyjną domeną usługi Azure AD.

## <a name="test-your-code"></a>testowanie kodu

Aby przetestować aplikację, wybierz F5, aby uruchomić projekt w programie Visual Studio. Zostanie wyświetlone okno główne:

![Interfejs użytkownika aplikacji](./media/tutorial-v2-windows-uwp/testapp-ui.png)

Gdy wszystko będzie gotowe do przetestowania, wybierz pozycję **Wywołaj Microsoft Graph interfejs API**. Aby się zalogować, użyj konta organizacyjnego usługi Azure AD lub konto Microsoft, takiego jak live.com lub outlook.com. Jeśli po raz pierwszy zobaczysz okno z prośbą do użytkownika o zalogowanie:

![Strona logowania](./media/tutorial-v2-windows-uwp/sign-in-page.png)

### <a name="consent"></a>Posiadacz

Po pierwszym zalogowaniu się do aplikacji jest wyświetlany ekran zgody podobny do poniższego. Wybierz pozycję **tak** , aby jawnie wyrazić zgodę na dostęp:

![Ekran zgody na dostęp](./media/tutorial-v2-windows-uwp/consentscreen.png)

### <a name="expected-results"></a>Oczekiwane wyniki

Informacje o profilu użytkownika zwracane przez wywołanie interfejsu API Microsoft Graph na ekranie **wyniki wywołania interfejsu API** :

![Ekran wyników wywołań interfejsu API](./media/tutorial-v2-windows-uwp/uwp-results-screen.PNG)

Widoczne są również podstawowe informacje o tokenie uzyskanym `AcquireTokenInteractive` za `AcquireTokenSilent` pośrednictwem lub w polu **Informacje o tokenie** :

|Właściwość  |Format  |Opis |
|---------|---------|---------|
|**Nazwa użytkownika** |<span>user@domain.com</span> |Nazwa użytkownika.|
|**Token wygasa** |Datetime |Czas wygaśnięcia tokenu. MSAL rozszerza datę wygaśnięcia przez odnowienie tokenu w razie potrzeby.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Więcej informacji na temat zakresów i uprawnień delegowanych

Interfejs API Microsoft Graph wymaga, aby *użytkownik. Read* miał zakres do odczytu profilu użytkownika. Ten zakres jest domyślnie dodawany automatycznie w każdej aplikacji, która jest zarejestrowana w portalu rejestracji aplikacji. Inne interfejsy API dla Microsoft Graph i niestandardowe interfejsy API dla serwera zaplecza mogą wymagać dodatkowych zakresów. Microsoft Graph API wymaga zakresu *Calendars. Read* , aby wyświetlić kalendarze użytkownika.

Aby uzyskać dostęp do kalendarzy użytkownika w kontekście aplikacji, Dodaj *kalendarze. Odczytaj* delegowane uprawnienia do informacji rejestracyjnych aplikacji. Następnie Dodaj do `acquireTokenSilent` wywołania zakres *Calendars. Read* .

> [!NOTE]
> Po zwiększeniu liczby zakresów użytkownicy mogą otrzymywać monity o dodatkowe przesłanie.

## <a name="known-issues"></a>Znane problemy

### <a name="issue-1"></a>Problem 1

Po zalogowaniu się do aplikacji w federacyjnej domenie usługi Azure AD zostanie wyświetlony jeden z następujących komunikatów o błędach:

* Nie znaleziono prawidłowego certyfikatu klienta w żądaniu.
* Nie znaleziono prawidłowych certyfikatów w magazynie certyfikatów użytkownika.
* Spróbuj ponownie wybrać inną metodę uwierzytelniania.

**Może** Możliwości przedsiębiorstwa i certyfikatów nie są włączone.

**Narzędzie** Postępuj zgodnie z instrukcjami w temacie [uwierzytelnianie zintegrowane w domenach federacyjnych](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Problem 2

[Uwierzytelnianie zintegrowane można włączyć w domenach federacyjnych](#enable-integrated-authentication-on-federated-domains-optional) i próbować użyć usługi Windows Hello na komputerze z systemem Windows 10 w celu zalogowania się w środowisku z skonfigurowanym uwierzytelnianiem wieloskładnikowym. Zostanie wyświetlona lista certyfikatów. Jeśli jednak zdecydujesz się użyć numeru PIN, okno numeru PIN nigdy nie zostanie wyświetlone.

**Może** Ten problem to znane ograniczenie brokera uwierzytelniania w sieci Web w aplikacjach platformy UWP, które działają w systemie Windows 10 Desktop. Działa prawidłowo w systemie Windows 10 Mobile.

**Poprawkę** Wybierz pozycję **Zaloguj się z innymi opcjami**. Następnie wybierz pozycję **Zaloguj się przy użyciu nazwy użytkownika i hasła**. Wybierz pozycję **Podaj hasło**. Następnie przejdź przez proces uwierzytelniania telefonu.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Pomóż nam ulepszyć platformę tożsamości firmy Microsoft. Powiedz nam, co myślisz, wykonując krótką ankietę z dwoma pytaniami.

> [!div class="nextstepaction"]
> [Microsoft Identity platform — ankieta](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
