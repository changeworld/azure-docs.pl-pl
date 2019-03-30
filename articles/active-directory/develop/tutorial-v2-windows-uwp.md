---
title: Usługa Azure AD v2.0 platformy uniwersalnej systemu Windows wprowadzenie | Dokumentacja firmy Microsoft
description: Jak wywołać interfejs API, który wymaga punktu końcowego v2.0 usługi Azure Active Directory tokenów dostępu aplikacji uniwersalnych platformy Windows (UWP)
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
ms.date: 03/20/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5e05faa37baf3c25be70a9500f1131cc0ea9f66
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58629412"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Wywołanie interfejsu API Microsoft Graph z poziomu aplikacji platformy uniwersalnej Windows (XAML)

> [!div renderon="docs"]
> [!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

W tym przewodniku wyjaśniono, jak żądania tokenu dostępu i następnie wywoływania interfejsu API programu Microsoft Graph natywnych aplikacji uniwersalnych platformy Windows (UWP). Przewodnik ma zastosowanie także do innych interfejsów API, które wymagają tokenów dostępu z punktu końcowego v2.0 usługi Azure Active Directory.

Na końcu tego przewodnika Twoja aplikacja wywołuje interfejs API chroniony przy użyciu konta osobistego. Przykładami są usługi outlook.com, live.com i inne. Twoja aplikacja wywołuje również kont służbowych z firmy lub organizacji, która ma usługę Azure Active Directory.

>[!NOTE]
> Ten przewodnik wymaga programu Visual Studio 2017 z programowania na platformę uniwersalną Windows zainstalowany. Zobacz [skonfigurować](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) Aby uzyskać instrukcje pobrać i skonfigurować program Visual Studio do opracowywania aplikacji uniwersalnych platformy Windows.

## <a name="how-this-guide-works"></a>Jak działa ten przewodnik

![Pokazuje, jak działa przykładowej aplikacji wygenerowanych w ramach tego samouczka](./media/tutorial-v2-windows-uwp/uwp-intro-updated.png)

Ten przewodnik tworzy przykładowej aplikacji platformy uniwersalnej systemu Windows, który odpytuje interfejsu API Microsoft Graph i interfejsu API sieci Web, który akceptuje tokeny od punktu końcowego v2.0 usługi Azure Active Directory. W tym scenariuszu token jest dodawany do żądań HTTP za pomocą nagłówka autoryzacji. Microsoft Authentication Library (MSAL) obsługuje nabycia tokenu i odnowienia.

## <a name="nuget-packages"></a>Pakiety NuGet

W tym przewodniku używane są następujące pakiety NuGet:

|Biblioteka|Opis|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Biblioteka uwierzytelniania firmy Microsoft|

## <a name="set-up-your-project"></a>konfigurowanie projektu

Ta sekcja zawiera instrukcje krok po kroku, aby zintegrować aplikację Windows Desktop .NET (XAML) za pomocą *Zaloguj się przy użyciu Microsoft*. Następnie może wysyłać zapytania interfejsów API sieci Web, które wymagają tokenu, np. interfejsu API Microsoft Graph.

Ten przewodnik tworzy aplikację, która wyświetla przycisk tego zapytania interfejsu API programu Graph, przyciskiem Wyloguj i pola tekstowe, zawierające wyniki wywołania.

> [!NOTE]
> Czy chcesz zamiast tego Pobierz ten przykładowy projekt programu Visual Studio? [Pobieranie projektu](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/master.zip) , a następnie przejdź do [Rejestracja aplikacji](#register-your-application "kroku rejestracji aplikacji") krok do konfigurowania próbki kodu, zanim zostanie ona uruchomiona.


### <a name="create-your-application"></a>Tworzenie aplikacji

1. W programie Visual Studio wybierz pozycje **Plik** > **Nowy** > **Projekt**.
2. W obszarze **szablony**, wybierz opcję **Visual C#**.
3. Wybierz pozycję **Pusta aplikacja (platforma uniwersalna systemu Windows)**.
4. Określanie nazwy aplikacji, a następnie wybierz pozycję **OK**.
5. Po wyświetleniu monitu wybierz dowolnej wersji dla **docelowej** i **Minimum** wersji, a następnie wybierz **OK**.

    >![Wersji minimalnej i docelowej](./media/tutorial-v2-windows-uwp/vs-minimum-target.png)

## <a name="add-microsoft-authentication-library-to-your-project"></a>Dodaj Biblioteka Microsoft Authentication Library do projektu
1. W programie Visual Studio wybierz pozycję **Narzędzia** > **Menedżer pakietów NuGet** > **Konsola menedżera pakietów**.
2. Skopiuj i wklej następujące polecenie w **Konsola Menedżera pakietów** okna:

    ```powershell
    Install-Package Microsoft.Identity.Client
    ```

> [!NOTE]
> To polecenie powoduje zainstalowanie [Biblioteka Microsoft Authentication Library](https://aka.ms/msal-net). Biblioteka MSAL uzyskuje zapisuje w pamięci podręcznej i odświeża tokenów użytkownika, które dostęp do interfejsów API chroniony przez usługę Azure Active Directory w wersji 2.0.

## <a name="initialize-msal"></a>Inicjowanie biblioteki MSAL
Ten krok ułatwia utworzenie klasy do obsługi interakcji z biblioteki MSAL, takie jak Obsługa tokenów.

1. Otwórz **App.xaml.cs** pliku i Dodaj odwołanie do biblioteki MSAL do klasy:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Dodaj następujące dwa wiersze do klasy przez aplikację (wewnątrz <code>sealed partial class App : Application</code> bloku):

    ```csharp
    // Below is the clientId of your app registration. 
    // You have to replace the below with the Application Id for your app registration
    private static string ClientId = "your_client_id_here";
    
    public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);
    ```

## <a name="create-your-applications-ui"></a>Tworzenie aplikacji interfejsu użytkownika

A **MainPage.xaml** pliku jest tworzony automatycznie jako część szablonu projektu. Otwórz ten plik, a następnie postępuj zgodnie z instrukcjami:

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
    
## <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>Użycia biblioteki MSAL do pobrania tokenu dla interfejsu API Microsoft Graph

W tej sekcji przedstawiono sposób użycia biblioteki MSAL do pobrania tokenu dla interfejsu API Microsoft Graph.

1.  W **MainPage.xaml.cs**, Dodaj odwołanie do biblioteki MSAL do klasy:

    ```csharp
    using Microsoft.Identity.Client;
    ```
2. Zastąp kod metody swoje <code>MainPage</code> klasy z następującym kodem:

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
                var accounts = await App.PublicClientApp.GetAccountsAsync();
                authResult = await App.PublicClientApp.AcquireTokenSilentAsync(scopes, accounts.FirstOrDefault());
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

#### <a name="get-a-user-token-interactively"></a>Interaktywne pobieranie tokenu użytkownika

Wywołanie `AcquireTokenAsync` metoda wyniki w oknie, który monituje użytkowników, aby zalogować się. Aplikacje zwykle wymagają od użytkowników zalogować się interaktywnie potrzebują dostępu do chronionego zasobu po raz pierwszy. Może być muszą się zalogować, gdy dyskretnej operację, aby uzyskać token nie powiodło się. Na przykład sytuacja wygasło hasło użytkownika.

#### <a name="get-a-user-token-silently"></a>Dyskretne pobieranie tokenu użytkownika

`AcquireTokenSilentAsync` Obsługiwała nabycia tokenu i odnowienia bez żadnej interakcji użytkownika. Po `AcquireTokenAsync` jest wykonywany po raz pierwszy, a użytkownik zostanie poproszony o poświadczenia, `AcquireTokenSilentAsync` metoda powinna służyć do żądania tokenów dla kolejnych wywołań, ponieważ uzyskać tokeny dyskretnie. Biblioteka MSAL będzie obsługiwać pamięć podręczną tokenu i odnawiania.

Po pewnym czasie `AcquireTokenSilentAsync` metoda kończy się niepowodzeniem. Przyczyny niepowodzenia może być, że użytkownicy mają wylogowany lub zmienić swoje hasło na innym urządzeniu. Gdy biblioteki MSAL wykryje, że ten problem można rozwiązać, wymagając akcję interaktywne, jest on uruchamiany `MsalUiRequiredException` wyjątku. Aplikacja może obsłużyć ten wyjątek na dwa sposoby:

* Może sprawić, że wywołanie względem `AcquireTokenAsync` natychmiast. To wywołanie powoduje monitowanie użytkownika do logowania. Zazwyczaj ten wzorzec jest używany w aplikacjach online w przypadku, gdy brak zawartości w trybie offline dostępne dla użytkownika. Przykład wygenerowanych za pomocą tego Instalatora z przewodnikiem jest zgodny ze wzorcem. Zostanie wyświetlony w czasie działania pierwszego uruchomienia przykładu.
  * Ponieważ żaden użytkownik nie ma korzystali z aplikacji, `accounts.FirstOrDefault()` zawiera wartość null i `MsalUiRequiredException` wyjątku.
  * Następnie kod w przykładzie obsługuje wyjątek, wywołując `AcquireTokenAsync`. To wywołanie powoduje monitowanie użytkownika do logowania.

* Lub zamiast tego przedstawia oznaczenia wizualne dla użytkowników, że interakcyjnego logowania jest wymagana. Następnie można wybrać do logowania w odpowiednim czasie. Lub aplikacji można ponowić próbę `AcquireTokenSilentAsync` później. Często ten wzór jest używany, gdy użytkownicy mogą używać innych funkcji aplikacji bez przerw w działaniu. Na przykład sytuacja offline zawartość jest dostępna w aplikacji. W takim przypadku użytkownicy mogą wybrać, chcąc Zaloguj się do dostępu do chronionego zasobu lub Odśwież nieaktualne informacje. Lub inne aplikacji można zdecydować ponowić próbę `AcquireTokenSilentAsync` gdy sieć jest przywracany po znajdował się tymczasowo niedostępne.

## <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Wywołanie interfejsu API Microsoft Graph przy użyciu tokenu, który został uzyskany

* Dodaj następującą nową metodę do **MainPage.xaml.cs**. Ta metoda umożliwia `GET` żądania interfejsu API programu Graph przy użyciu `Authorization` nagłówka:

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

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Więcej informacji na temat wywołania chronionego interfejsu API REST

W tej przykładowej aplikacji `GetHttpContentWithToken` metoda umożliwia HTTP `GET` żądania i porównuje chronionego zasobu, który wymaga tokenu. Następnie metoda zwraca zawartości do obiektu wywołującego. Metoda ta umożliwia dodanie uzyskano token w **autoryzacji HTTP** nagłówka. W tym przykładzie zasób jest interfejsu API programu Microsoft Graph **mnie** punktu końcowego, który wyświetla informacje o profilu użytkownika.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Dodaj metodę, aby się wylogować użytkownika

* Aby się wylogować użytkownika, dodaj następującą metodę do **MainPage.xaml.cs**:

    ```csharp
    /// <summary>
    /// Sign out the current user
    /// </summary>
    private void SignOutButton_Click(object sender, RoutedEventArgs e)
    {
        var accounts = await App.PublicClientApp.GetAccountsAsync();
        if (accounts.Any())
        {
            try
            {
                App.PublicClientApp.RemoveAsync(accounts.FirstOrDefault());
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

`SignOutButton_Click` Metoda usuwa użytkownika z pamięci podręcznej biblioteki MSAL użytkownika. Ta metoda informuje skutecznie MSAL zapomnieć bieżącego użytkownika. Następnie przyszłe żądania, aby uzyskać token powiedzie się tylko wtedy, gdy ma on być interaktywna.
Aplikacja, w tym przykładzie obsługuje pojedynczego użytkownika. Ale biblioteki MSAL obsługuje scenariusze, w których więcej niż jedno konto może być zalogowany na tym samym czasie. Przykładem jest aplikacja poczty e-mail, gdy użytkownik ma kilka kont.

## <a name="display-basic-token-information"></a>Wyświetlanie podstawowych informacji o tokenie

* Dodaj następującą metodę do **MainPage.xaml.cs** Aby wyświetlić podstawowe informacje o tokenie:

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

Tokeny Identyfikatora pozyskane za pośrednictwem **OpenID Connect** również zawierać małego podzbioru informacji dotyczących użytkownika. `DisplayBasicTokenInfo` zawiera podstawowe informacje zawarte w tokenie. Przykładami są nazwę wyświetlaną tego użytkownika i identyfikator daty wygaśnięcia tokenu i ciąg, który reprezentuje token dostępu, sam. Jeśli wybierzesz **wywołania interfejsu API Microsoft Graph** przycisk kilka razy, zobaczysz, że ten sam token został ponownie dla kolejnych żądań. Widać również datę wygaśnięcia, w przypadku MSAL zdecyduje się, że nadszedł czas na odnowienie tokenu.

## <a name="register-your-application"></a>Rejestrowanie aplikacji

Teraz musisz zarejestrować aplikację w portalu rejestracji aplikacji firmy Microsoft:
1. Przejdź do [portalu rejestracji aplikacji firmy Microsoft](https://apps.dev.microsoft.com/portal/register-app), aby zarejestrować aplikację.
2. Wprowadź nazwę aplikacji.
3. Upewnij się, że opcja **instrukcje konfiguracji** jest *niewybranych*.
4. Wybierz **platform dodać**, wybierz opcję **aplikacji natywnej**, a następnie wybierz pozycję **Zapisz**.
5. Skopiuj identyfikator GUID w **identyfikator aplikacji**, wróć do programu Visual Studio, otwórz **App.xaml.cs**i Zastąp `your_client_id_here` identyfikatorem aplikacji, które właśnie zostało zarejestrowane:

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Włącz uwierzytelnianie zintegrowane w domenach federacyjnych (opcjonalnie)

Aby włączyć zintegrowane uwierzytelnianie Windows, gdy jest używany z domeny federacyjnej usługi Azure Active Directory, manifest aplikacji musi włączyć dodatkowe funkcje:

1. Kliknij dwukrotnie **Package.appxmanifest**.
2. Wybierz **możliwości** kartę i upewnij się, że są włączone następujące ustawienia:

    - Uwierzytelnianie w przedsiębiorstwie
    - Sieci prywatne (klient i serwer)
    - Udostępnione certyfikaty użytkowników

3. Otwórz **App.xaml.cs** i Dodaj następujący wiersz w Konstruktorze aplikacji:

    ```csharp
    App.PublicClientApp.UseCorporateNetwork = true;
    ```

> [!IMPORTANT]
> [Zintegrowane uwierzytelnianie Windows](https://aka.ms/msal-net-iwa) nie są konfigurowane domyślnie dla tego przykładu. Aplikacje, które żądają *uwierzytelnianie w przedsiębiorstwie* lub *udostępnione certyfikaty użytkownika* możliwości wymagać wyższego poziomu weryfikacji przez Windows Store. Ponadto nie wszystkie deweloperów chcesz wykonać wyższy poziom weryfikacji. To ustawienie jest włączone, tylko wtedy, gdy potrzebujesz zintegrowane uwierzytelnianie Windows za pomocą federacyjnego domeny usługi Azure Active Directory.

## <a name="test-your-code"></a>testowanie kodu

Aby przetestować aplikację, naciśnij klawisz F5, aby uruchomić projekt w programie Visual Studio. Zostanie wyświetlone okno główne:

![Interfejs użytkownika aplikacji](./media/tutorial-v2-windows-uwp/testapp-ui.png)

Gdy wszystko będzie gotowe do testowania, wybierz pozycję **wywołania interfejsu API Microsoft Graph**. Następnie użyj konta organizacyjnego usługi Microsoft Azure Active Directory lub konta Microsoft, takich jak live.com lub outlook.com, aby zalogować się. Jeśli po raz pierwszy, zostanie wyświetlony okno z pytaniem do użytkownika, zaloguj się:

![Strona logowania](./media/tutorial-v2-windows-uwp/sign-in-page.png)

### <a name="consent"></a>Wyraź zgodę
Podczas pierwszego logowania się do aplikacji, zobaczysz ekran wyrażania zgody, podobny do następującego. Wybierz **tak** jawnie zgody na dostęp:

![Ekran wyrażania zgody dostępu do zasobów](./media/tutorial-v2-windows-uwp/consentscreen.png)
### <a name="expected-results"></a>Oczekiwane wyniki
Zobacz informacje o profilu użytkownika zwrócona przez wywołanie interfejsu API Microsoft Graph **wyników wywołań interfejsu API** ekranu:

![Ekran wyników wywołań interfejsu API](./media/tutorial-v2-windows-uwp/uwp-results-screen.PNG)

Zobacz też podstawowe informacje o tokenie pozyskane za pośrednictwem `AcquireTokenAsync` lub `AcquireTokenSilentAsync` w **informacje o tokenie** pola:

|Właściwość  |Format  |Opis |
|---------|---------|---------|
|**Nazwa** |Imię i nazwisko użytkownika|Użytkownik na imię i nazwisko.|
|**Nazwa użytkownika** |<span>user@domain.com</span> |Nazwa użytkownika identyfikuje użytkownika.|
|**Token wygasa** |DateTime |Czas, po wygaśnięciu ważności tokenu. Biblioteka MSAL rozszerza datę wygaśnięcia, odnawianie tokenu zgodnie z potrzebami.|
|**Token dostępu** |String |Ciąg tokenu, który jest wysyłany do protokołu HTTP żądania, które wymagają *nagłówek autoryzacji*.|

#### <a name="see-whats-in-the-access-token-optional"></a>Zobacz, co w tokenie dostępu (opcjonalnie)
Opcjonalnie, skopiuj wartość w **Token dostępu** i wklej go w https://jwt.ms zdekodować i zapoznać się z listą oświadczeń.

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Więcej informacji o zakresach i delegowane uprawnienia

Interfejs API programu Microsoft Graph wymaga *user.read* zakresu na odczytywanie profilu użytkownika. Ten zakres jest automatycznie dodawany domyślnie każda aplikacja, która jest zarejestrowana w portalu rejestracji aplikacji. Inne interfejsy API dla programu Microsoft Graph i niestandardowych interfejsów API dla serwera zaplecza, mogą wymagać dodatkowe zakresy. Interfejs API programu Microsoft Graph wymaga *Calendars.Read* zakres, aby wyświetlić listę kalendarzy użytkownika.

Aby uzyskać dostęp do kalendarzy użytkownika w kontekście aplikacji, należy dodać *Calendars.Read* delegowane uprawnienia do rejestrowania informacji o aplikacji. Następnie dodaj *Calendars.Read* ograniczyć zakres do `acquireTokenSilent` wywołania.

> [!NOTE]
> Użytkownicy mogą być monitowani dla dodatkowych zgody, jak zwiększyć liczbę zakresów.

## <a name="known-issues"></a>Znane problemy

### <a name="issue-1"></a>Problem 1
Po zalogowaniu się w swojej aplikacji w domenie usługi Azure Active Directory federacyjnego wyświetlany jest jeden z następujących komunikatów o błędzie:
 - Nie znaleziono w żądaniu certyfikatu prawidłowego klienta.
 - Nie znaleziono w magazynie certyfikatów użytkownika prawidłowe certyfikaty.
 - Spróbuj ponownie, wybierając innej metody uwierzytelniania.

**Przyczyna:** Funkcje przedsiębiorstwa i certyfikat nie są włączone.

**Rozwiązanie:** Postępuj zgodnie z instrukcjami w [zintegrowanego uwierzytelniania w przypadku domen federacyjnych](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Problem 2
Możesz włączyć [zintegrowanego uwierzytelniania w przypadku domen federacyjnych](#enable-integrated-authentication-on-federated-domains-optional) , a następnie spróbuj się korzystanie z usługi Windows Hello na komputerze z systemem Windows 10 do logowania się w środowisku z uwierzytelnianiem wieloskładnikowym skonfigurowane. Jest wyświetlana lista certyfikatów. Jednak jeśli zdecydujesz się użyć numeru PIN, w oknie Kod PIN nigdy nie zobaczy.

**Przyczyna:** Ten problem jest znanym ograniczeniem broker uwierzytelniania w sieci web w aplikacjach platformy uniwersalnej systemu Windows, działających w systemie Windows 10 desktop. Współpracuje z systemu Windows 10 Mobile.

**Obejście:** Wybierz **Zaloguj się przy użyciu innych opcji**. Następnie wybierz pozycję **Zaloguj się przy użyciu nazwy użytkownika i hasła**. Wybierz **Podaj hasło**. Następnie przejdź przez proces uwierzytelniania phone.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
