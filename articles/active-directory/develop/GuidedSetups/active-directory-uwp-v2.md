---
title: Azure AD w wersji 2 platformy uniwersalnej systemu Windows wprowadzenie | Dokumentacja firmy Microsoft
description: Jak aplikacji platformy uniwersalnej systemu Windows (UWP) można wywołać interfejs API, który wymaga tokenów dostępu przez punkt końcowy usługi Azure Active Directory w wersji 2
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/20/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: c2d5681e30651aac7a09a8ead923015e9a892d42
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34796618"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Wywołanie interfejsu API programu Microsoft Graph z aplikacji platformy uniwersalnej systemu Windows (XAML)

W tym przewodniku objaśniono sposób natywnych aplikacji systemu Windows platformy Uniwersalnej można żądania tokenu dostępu, a następnie wywołać interfejs API programu Microsoft Graph. Przewodnik ma również zastosowanie do innych interfejsów API, które wymagają tokenów dostępu z punktu końcowego usługi Azure Active Directory w wersji 2.

Na końcu tego przewodnika aplikacji wywołuje interfejs API chronionych przy użyciu kont osobistych. Przykłady są outlook.com, live.com i inne. Aplikacja wymaga również kont służbowych z firmy lub organizacji, która ma usługę Azure Active Directory.

>[!NOTE]
> W tym przewodniku wymaga programu Visual Studio 2017 rozwoju platformy uniwersalnej systemu Windows zainstalowane. Zobacz [uzyskać Konfigurowanie](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) instrukcje dotyczące pobierania i konfigurowanie programu Visual Studio umożliwiające tworzenie aplikacji platformy uniwersalnej systemu Windows.

### <a name="how-this-guide-works"></a>Jak działa w tym przewodniku

![Działania w tym przewodniku wykresu](media/active-directory-mobileanddesktopapp-windowsuniversalplatform-introduction/uwp-intro.png)

Ten przewodnik utworzy przykładowej aplikacji platformy uniwersalnej systemu Windows, który odpytuje interfejsu API programu Microsoft Graph lub interfejsu API sieci Web, który akceptuje tokeny od punktu końcowego usługi Azure Active Directory w wersji 2. W tym scenariuszu token zostanie dodany do żądań HTTP za pośrednictwem nagłówek autoryzacji. Biblioteka uwierzytelniania firmy Microsoft (MSAL) obsługuje nabycia tokenu i odnawiania.

### <a name="nuget-packages"></a>Pakiety NuGet

W tym przewodniku używa następujących pakietów NuGet:

|Biblioteka|Opis|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Biblioteka uwierzytelniania firmy Microsoft|


## <a name="set-up-your-project"></a>Konfigurowanie projektu

Ta sekcja zawiera instrukcje krok po kroku do integracji aplikacji .NET pulpitu systemu Windows (XAML) z *logowania z firmą Microsoft*. Następnie może wysyłać zapytania interfejsów API sieci Web, które wymagają tokenu, takich jak Microsoft interfejsu API programu Graph.

W tym przewodniku tworzy aplikację, która zawiera przycisk tego interfejsu API programu Graph zapytań, przycisk wylogowania i pola tekstowe zawierające wyniki wywołania.

>[!NOTE]
> Czy chcesz zamiast tego Pobierz ten przykładowy projekt programu Visual Studio? [Pobieranie projektu](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/master.zip) i przejść [Rejestracja aplikacji](#register-your-application "kroku rejestracji aplikacji") krok, aby skonfigurować przykładowy kod przed uruchomieniem.


### <a name="create-your-application"></a>Tworzenie aplikacji
1. W programie Visual Studio, wybierz **pliku** > **nowy** > **projektu**.
2. W obszarze **szablony**, wybierz pozycję **Visual C#**.
3. Wybierz pozycję **Pusta aplikacja (platforma uniwersalna systemu Windows)**.
4. Nazwa aplikacji, a następnie wybierz **OK**.
5. Po wyświetleniu monitu wybierz dowolnej wersji dla **docelowej** i **Minimum** wersji, a następnie wybierz **OK**.

    >![Minimalna i docelowa wersji](media/active-directory-uwp-v2.md/vs-minimum-target.png)

## <a name="add-microsoft-authentication-library-to-your-project"></a>Dodaj bibliotekę uwierzytelniania firmy Microsoft do projektu
1. W programie Visual Studio, wybierz **narzędzia** > **Menedżera pakietów NuGet** > **Konsola Menedżera pakietów**.
2. Skopiuj i wklej poniższe polecenie w **Konsola Menedżera pakietów** okno:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

> [!NOTE]
> To polecenie powoduje zainstalowanie [biblioteki uwierzytelniania](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet). MSAL uzyskuje buforuje i odświeża tokenów użytkownika, które uzyskują dostęp do interfejsów API chronione przez usługi Azure Active Directory w wersji 2.

## <a name="initialize-msal"></a>Inicjowanie MSAL
Ten krok ułatwia utworzenie klasy do obsługi interakcji z MSAL, takie jak Obsługa tokenów.

1. Otwórz **App.xaml.cs** plik i dodać odwołanie do MSAL do klasy:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Dodaj następujące dwa wiersze do klasy aplikację (wewnątrz <code>sealed partial class App : Application</code> blok):

    ```csharp
    // Below is the clientId of your app registration. 
    // You have to replace the below with the Application Id for your app registration
    private static string ClientId = "your_client_id_here";
    
    public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);
    ```

## <a name="create-your-applications-ui"></a>Tworzenie aplikacji interfejsu użytkownika

A **MainPage.xaml** plik jest tworzony automatycznie w ramach szablonu projektu. Otwórz ten plik, a następnie postępuj zgodnie z instrukcjami:

* Zastąp aplikacji **siatki** węzła z następującym kodem:

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
    
## <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>Użyj MSAL, aby uzyskać token dla interfejsu API programu Microsoft Graph

W tej sekcji przedstawia sposób użycia MSAL uzyskać token dla interfejsu API programu Microsoft Graph.

1.  W **MainPage.xaml.cs**, Dodaj odwołanie do MSAL do klasy:

    ```csharp
    using Microsoft.Identity.Client;
    ```
2. Zastąp kod użytkownika <code>MainPage</code> klasy następującym kodem:

    ```csharp
    public sealed partial class MainPage : Page
    {
        // Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";
    
        // Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };
    
        public MainPage()
        {
            this.InitializeComponent();
        }
    
        /// <summary>
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;
            ResultText.Text = string.Empty;
            TokenInfoText.Text = string.Empty;
    
            try
            {
                authResult = await App.PublicClientApp.AcquireTokenSilentAsync(scopes, App.PublicClientApp.Users.FirstOrDefault());
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");
    
                try
                {
                    authResult = await App.PublicClientApp.AcquireTokenAsync(scopes);
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
    }
    ```

### <a name="more-information"></a>Więcej informacji
#### <a name="get-a-user-token-interactively"></a>Pobierz token użytkownika interakcyjnego
Wywołanie `AcquireTokenAsync` metoda powoduje okna, które monituje użytkowników do logowania. Aplikacje zazwyczaj wymagają użytkownikom na logowanie interakcyjne po raz pierwszy potrzebnych do uzyskania dostępu do chronionego zasobu. Może być muszą się zalogować, gdy dyskretnej operacji można uzyskać tokenu nie powiodło się. Przykładem jest po wygaśnięciu hasła użytkownika.

#### <a name="get-a-user-token-silently"></a>Pobierz token użytkownika dyskretnej
`AcquireTokenSilentAsync` Metoda obsługuje nabycia tokenu i odnowienia bez interakcji użytkownika. Po `AcquireTokenAsync` jest wykonywana po raz pierwszy, a użytkownik jest monitowany o poświadczenia, `AcquireTokenSilentAsync` metody powinien być używany do żądania tokenów w kolejnych wywołaniach, ponieważ uzyskać tokeny trybie dyskretnym. MSAL obsługi pamięci podręcznej tokenu i odnawiania.

Po pewnym czasie `AcquireTokenSilentAsync` metoda kończy się niepowodzeniem. Przyczyny niepowodzenia może być, czy użytkownicy mają wylogowany lub zmienić swoje hasło na innym urządzeniu. Gdy MSAL wykryje, że problem można rozwiązać, gdyż akcję interaktywne, jego generowane `MsalUiRequiredException` wyjątku. Aplikacja może obsłużyć tego wyjątku na dwa sposoby:

* Można wykonać połączenie przed `AcquireTokenAsync` natychmiast. To wywołanie powoduje monitowanie użytkownika do logowania. Zazwyczaj ten wzorzec jest używany w aplikacjach online w przypadku, gdy brak dostępnej zawartości w trybie offline dla użytkownika. Przykładowe wygenerowane za pomocą tego Instalatora z przewodnikiem zgodny ze wzorcem. Zostanie wyświetlony w akcji pierwszym uruchomieniu próbki. 
    * Ponieważ żaden użytkownik nie ma korzystali z aplikacji `PublicClientApp.Users.FirstOrDefault()` zawiera wartość null i `MsalUiRequiredException` wyjątku.
    * Następnie kod w próbce obsługuje wyjątek przez wywołanie metody `AcquireTokenAsync`. To wywołanie powoduje monitowanie użytkownika do logowania.

* Lub zamiast tego przedstawia oznaczenia wizualne dla użytkowników, że interakcyjnego logowania jest wymagana. Następnie można wybrać odpowiednich momentach do logowania. Lub aplikacji można ponowić próbę `AcquireTokenSilentAsync` później. Często ten wzorzec jest używany podczas użytkownicy mogą używać innych funkcji aplikacji bez zakłóceń. Przykładem jest podczas w trybie offline zawartość jest dostępna w aplikacji. W takim przypadku użytkownicy mogą wybrać, jeśli chcą, aby zalogować się do dostępu do chronionego zasobu albo Odśwież nieaktualne informacje. Lub #else aplikacji można zdecydować ponowić próbę `AcquireTokenSilentAsync` przywrócenie sieci po jego jest tymczasowo niedostępny.

## <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Wywołanie interfejsu API programu Graph firmy Microsoft przy użyciu tokenu, który został uzyskany

* Dodaj następującą metodę nowe do **MainPage.xaml.cs**. Ta metoda umożliwia `GET` żądania dotyczącego interfejsu API programu Graph przy użyciu nagłówka [Authorize]:

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

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Więcej informacji na temat nawiązywania połączenia przed chronionego interfejsu API REST

W tej przykładowej aplikacji `GetHttpContentWithToken` używa metody w celu HTTP `GET` żądania dotyczącego zasobu chronionego, która wymaga tokenu. Następnie metoda zwraca zawartości do obiektu wywołującego. Ta metoda dodaje token nabyte w **autoryzacji HTTP** nagłówka. Dla tego przykładu zasób jest interfejsu API programu Microsoft Graph **mnie** punktu końcowego, który wyświetla informacje o profilu użytkownika.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Dodaj metodę, aby się wylogować użytkownika

* Aby wylogować użytkownika, dodaj następującą metodę do **MainPage.xaml.cs**:

    ```csharp
    /// <summary>
    /// Sign out the current user
    /// </summary>
    private void SignOutButton_Click(object sender, RoutedEventArgs e)
    {
        if (App.PublicClientApp.Users.Any())
        {
            try
            {
                App.PublicClientApp.Remove(App.PublicClientApp.Users.FirstOrDefault());
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

### <a name="more-information-on-sign-out"></a>Więcej informacji na temat wylogowania

`SignOutButton_Click` Metoda usuwa użytkownika z pamięci podręcznej MSAL użytkownika. Ta metoda kod w praktyce określa MSAL zapomnieć bieżącego użytkownika. Następnie przyszłych żądań można uzyskać tokenu powiedzie się tylko wtedy, gdy ma ona interaktywne.
W tym przykładzie aplikacja obsługuje jednego użytkownika. Ale MSAL obsługuje scenariusze, w których może być podpisana więcej niż jedno konto w tym samym czasie. Przykładem jest aplikacja poczty e-mail, gdy użytkownik ma kilka kont.

## <a name="display-basic-token-information"></a>Wyświetlanie informacji o tokenie podstawowe

* Dodaj następującą metodę do **MainPage.xaml.cs** do wyświetlania podstawowych informacji o tokenie:

    ```csharp
    /// <summary>
    /// Display basic information contained in the token
    /// </summary>
    private void DisplayBasicTokenInfo(AuthenticationResult authResult)
    {
        TokenInfoText.Text = "";
        if (authResult != null)
        {
            TokenInfoText.Text += $"Name: {authResult.User.Name}" + Environment.NewLine;
            TokenInfoText.Text += $"Username: {authResult.User.DisplayableId}" + Environment.NewLine;
            TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
            TokenInfoText.Text += $"Access Token: {authResult.AccessToken}" + Environment.NewLine;
        }
    }
    ```

### <a name="more-information"></a>Więcej informacji

Tokeny Identyfikatora nabyte za pośrednictwem **OpenID Connect** również zawierać małego podzbioru informacji dotyczących użytkownika. `DisplayBasicTokenInfo` zawiera podstawowe informacje zawarte w tokenie. Przykładami są nazwa wyświetlana użytkownika i identyfikator, datę wygaśnięcia tokenu i ciąg, który reprezentuje token dostępu do samej siebie. W przypadku wybrania **wywołać Microsoft interfejsu API programu Graph** przycisk kilka razy, zobaczysz, że ten sam token został ponownie dla kolejnych żądań. Można również sprawdzić datę wygaśnięcia, w przypadku MSAL decyduje o tym, że czas na Odnów token.

## <a name="register-your-application"></a>Rejestrowanie aplikacji

Teraz musisz zarejestrować aplikację w portalu rejestracji aplikacji firmy Microsoft:
1. Przejdź do [portalu rejestracji aplikacji Microsoft](https://apps.dev.microsoft.com/portal/register-app) do rejestrowania aplikacji.
2. Wprowadź nazwę aplikacji.
3. Upewnij się, że opcja **instrukcje konfiguracji** jest *nie wybrano*.
4. Wybierz **dodać platformy**, wybierz pozycję **aplikacji natywnej**, a następnie wybierz **zapisać**.
5. Skopiuj identyfikator GUID w **identyfikator aplikacji**, wróć do programu Visual Studio, otwórz **App.xaml.cs**i Zastąp `your_client_id_here` z został zarejestrowany identyfikator aplikacji:

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Włącz zintegrowane uwierzytelnianie na domen federacyjnych (opcjonalnie)

Aby włączyć zintegrowane uwierzytelnianie systemu Windows, gdy jest używany z domeną federacyjnych usługi Azure Active Directory, manifest aplikacji musi włączyć dodatkowe funkcje:

1. Kliknij dwukrotnie **Package.appxmanifest**.
2. Wybierz **możliwości** karcie i upewnij się, że są włączone następujące ustawienia:

    - Enterprise Authentication
    - Sieci prywatne (klient i serwer)
    - Udostępnione certyfikaty użytkowników

3. Otwórz **App.xaml.cs** i Dodaj następujący wiersz w Konstruktorze aplikacji:

    ```csharp
    App.PublicClientApp.UseCorporateNetwork = true;
    ```

> [!IMPORTANT]
> Domyślnie dla tego przykładu nie skonfigurowano zintegrowanego uwierzytelniania systemu Windows. Aplikacje, które żądają *Enterprise Authentication* lub *udostępnione certyfikaty użytkowników* możliwości wymagać wyższego poziomu weryfikacji w Sklepie Windows. Ponadto nie wszystkie deweloperzy chcesz przeprowadzić wyższy poziom weryfikacji. To ustawienie jest włączone, tylko wtedy, gdy potrzebujesz zintegrowanego uwierzytelniania systemu Windows z domeną federacyjnych usługi Azure Active Directory.


## <a name="test-your-code"></a>Testowanie kodu

Aby przetestować aplikację, wybierz F5, aby uruchomić projekt w programie Visual Studio. Zostanie wyświetlone Twoje okno główne:

![Interfejs użytkownika aplikacji](media/active-directory-uwp-v2.md/testapp-ui.png)

Gdy wszystko jest gotowe do testowania, wybierz **wywołać Microsoft interfejsu API programu Graph**. Następnie używane do logowania konta organizacyjnego usługi Microsoft Azure Active Directory lub konta Microsoft, takich jak live.com lub outlook.com. Jeśli jest używany po raz pierwszy, zostanie wyświetlone okno z zapytaniem logowanie użytkownika:

![Strona logowania](media/active-directory-uwp-v2.md/sign-in-page.png)

### <a name="consent"></a>Wyraź zgodę
Podczas pierwszego logowania się do aplikacji, aby wyświetlić dostępne zgody ekran podobny do następującego. Wybierz **tak** o jawnie zgodę na dostęp:

![Ekran zgody dostępu do zasobów](media/active-directory-uwp-v2.md/consentscreen.png)
### <a name="expected-results"></a>Oczekiwanych rezultatów
Zobacz informacje o profilu użytkownika zwrócona przez wywołanie interfejsu API programu Microsoft Graph **wyniki wywołania interfejsu API** ekranu:

![Wyniki wywołania interfejsu API ekranu](media/active-directory-uwp-v2.md/uwp-results-screen.PNG)

Zobacz też podstawowe informacje o tokenie nabyte za pośrednictwem `AcquireTokenAsync` lub `AcquireTokenSilentAsync` w **informacji o tokenie** pola:

|Właściwość  |Format  |Opis |
|---------|---------|---------|
|**Nazwa** |Imię i nazwisko użytkownika|Imię i nazwisko użytkownika.|
|**Nazwa użytkownika** |<span>user@domain.com</span> |Nazwa użytkownika, która identyfikuje użytkownika.|
|**Wygaśnięcia tokenu** |DateTime |Czas wygaśnięcia tokenu. MSAL rozszerza datę wygaśnięcia, odnowienie tokenu w razie potrzeby.|
|**Token dostępu** |Ciąg |Tokenu ciąg, który jest wysyłany do HTTP żądań, które wymagają *nagłówek autoryzacji*.|

#### <a name="see-whats-in-the-access-token-optional"></a>Zobacz, co w tokenie dostępu (opcjonalnie)
Opcjonalnie można skopiować wartości w **Token dostępu** i wklej go w https://jwt.ms zdekodować i zapoznać się z listą oświadczeń.

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Więcej informacji o zakresach i uprawnień delegowanych

Wymaga programu Microsoft Graph API *user.read* zakresu odczytać profilu użytkownika. Ten zakres jest automatycznie dodawane domyślnie w każdej aplikacji, która jest zarejestrowana w portalu rejestracji aplikacji. Inne interfejsy API dla programu Microsoft Graph i niestandardowych interfejsów API dla serwera zaplecza może wymagać dodatkowych zakresów. Wymaga programu Microsoft Graph API *Calendars.Read* zakres, aby wyświetlić listę kalendarzy użytkownika.

Aby uzyskać dostęp do kalendarzy użytkownika w kontekście aplikacji, należy dodać *Calendars.Read* delegowane uprawnienia do informacji o rejestracji aplikacji. Następnie dodaj *Calendars.Read* zakres do `acquireTokenSilent` wywołania. 

> [!NOTE]
> Użytkownicy mogą monit dla dodatkowych zgody zwiększyć liczbę zakresów.

## <a name="known-issues"></a>Znane problemy

### <a name="issue-1"></a>Problem 1
Po zalogowaniu się na aplikacji federacyjnych domeny usługi Azure Active Directory wyświetlany jest jeden z następujących komunikatów o błędzie:
 - Nie znaleziono w żądaniu certyfikatu prawidłowego klienta.
 - Nie znaleziono w magazynie certyfikatów użytkownika prawidłowe certyfikaty.
 - Spróbuj ponownie, wybierając innej metody uwierzytelniania.

**Przyczyna:** możliwości przedsiębiorstwa i certyfikat nie są włączone.

**Rozwiązanie:** postępuj zgodnie z instrukcjami [zintegrowane uwierzytelnianie na Sfederowanych domen](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Wydanie 2
Możesz włączyć [zintegrowane uwierzytelnianie na Sfederowanych domen](#enable-integrated-authentication-on-federated-domains-optional) i spróbuj użyć usługi Windows Hello na komputerze z systemem Windows 10 do logowania w środowisku z uwierzytelnianiem wieloskładnikowym skonfigurowane. Jest wyświetlana lista certyfikatów. Jednak jeśli wybierzesz używasz numeru PIN, nigdy nie są prezentowane okna numeru PIN.

**Przyczyna:** tego problemu jest to znane ograniczenie brokera uwierzytelniania sieci web w aplikacji platformy uniwersalnej systemu Windows, które uruchamiane na pulpicie systemu Windows 10. Współpracuje z systemem Windows 10 Mobile.

**Obejście problemu:** wybierz **Zaloguj się przy użyciu innych opcji**. Następnie wybierz **Zaloguj się przy użyciu nazwy użytkownika i hasła**. Wybierz **, podaj hasło**. Następnie przejdź do procesu uwierzytelniania phone.
