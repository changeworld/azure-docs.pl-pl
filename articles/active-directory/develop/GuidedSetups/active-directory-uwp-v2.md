---
title: Usługi Azure AD w wersji 2 platformy uniwersalnej systemu Windows wprowadzenie | Dokumentacja firmy Microsoft
description: Jak aplikacji uniwersalnych platformy systemu Windows (XAML) można wywołać interfejsu API, które wymagają tokenów dostępu przez punkt końcowy w wersji 2 usługi Azure Active Directory
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
ms.openlocfilehash: 4db14bc250bf9d6740380f3c4376f43d6f315b01
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Wywołanie interfejsu API programu Microsoft Graph z aplikacji uniwersalnych platformy systemu Windows (UWP)

W tym przewodniku pokazano, jak natywnych aplikacji uniwersalnych platformy systemu Windows (XAML) można uzyskać token dostępu, a następnie użyć tej toke dostępu do wywołania interfejsu API programu Graph firmy Microsoft lub innych interfejsów API, które wymagają tokenów dostępu z punktu końcowego w wersji 2 usługi Azure Active Directory.

Na końcu tego przewodnika aplikacja będzie mogła wywołać interfejs API chronionych przy użyciu konta osobiste (w tym outlook.com, live.com i inne) oraz pracy i służbowych z firmy lub organizacji, która ma usługę Azure Active Directory.  

> W tym przewodniku wymaga programu Visual Studio 2017 rozwoju platformy uniwersalnej systemu Windows zainstalowane. Sprawdź [artykułu](https://docs.microsoft.com/windows/uwp/get-started/get-set-up "instalacji programu Visual Studio dla platformy uniwersalnej systemu Windows") instrukcje dotyczące sposobu pobierania i konfigurowanie programu Visual Studio do opracowywania uniwersalnych aplikacji platformy systemu Windows.

### <a name="how-this-guide-works"></a>Jak działa w tym przewodniku

![Jak działa w tym przewodniku](media/active-directory-mobileanddesktopapp-windowsuniversalplatform-introduction/uwp-intro.png)

Przykładowa aplikacja utworzone przez ten przewodnik umożliwia aplikacji platformy uniwersalnej systemu Windows, wykonać zapytania interfejsu API programu Microsoft Graph i interfejsu API sieci Web, który akceptuje tokeny od punktu końcowego w wersji 2 usługi Azure Active Directory. W tym scenariuszu token zostanie dodany do żądań HTTP za pośrednictwem nagłówek autoryzacji. Token nabycia i odnawiania są obsługiwane przez biblioteki uwierzytelniania firmy Microsoft (MSAL).

### <a name="nuget-packages"></a>Pakiety NuGet

W tym przewodniku używa następujących pakietów NuGet:

|Biblioteka|Opis|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Biblioteka uwierzytelniania firmy Microsoft (MSAL)|


## <a name="set-up-your-project"></a>Konfigurowanie projektu

Ta sekcja zawiera instrukcje krok po kroku dotyczące sposobu tworzenia nowego projektu aby zademonstrować sposób integracji aplikacji .NET pulpitu systemu Windows (XAML) z *logowania z firmą Microsoft* aby mogła zbadać interfejsów API sieci Web, które wymagają tokenu.

Aplikacji utworzonych przez ten przewodnik przedstawia przycisk Wykres i wyświetlić wyniki na ekranie i przycisk wylogowania.

> Preferowane jest zamiast tego Pobierz ten przykładowy projekt programu Visual Studio? [Pobieranie projektu](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/master.zip) i przejść [Rejestracja aplikacji](#register-your-application "kroku rejestracji aplikacji") krok, aby skonfigurować przykładowy kod przed wykonaniem.


### <a name="create-your-application"></a>Tworzenie aplikacji
1. W programie Visual Studio: **pliku** > **nowe** > **projektu**<br/>
2. W obszarze *szablony*, wybierz pozycję **Visual C#**
3. Wybierz **pusta aplikacja (uniwersalna systemu Windows)**
4. Nadaj mu nazwę, a następnie kliknij przycisk "Ok".
5. Jeśli zostanie wyświetlony monit, spadła wybierz dowolną dowolnej wersji dla *docelowej* i *Minimum* wersji i kliknij przycisk 'Ok':<br/><br/>![Minimalna i docelowa wersji](media/active-directory-uwp-v2.md/vs-minimum-target.png)

## <a name="add-the-microsoft-authentication-library-msal-to-your-project"></a>Dodaj do projektu biblioteki uwierzytelniania firmy Microsoft (MSAL)
1. W programie Visual Studio: **narzędzia** > **Menedżera pakietów Nuget** > **Konsola Menedżera pakietów**
2. Skopiuj/wklej poniższe polecenie w oknie Konsola Menedżera pakietów:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

> [!NOTE]
> Pakiet powyżej instaluje [biblioteki uwierzytelniania firmy Microsoft (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet). MSAL obsługuje pobieranie, buforowanie i odświeżanie tokenów użytkownika, które umożliwiają dostęp do interfejsów API chronione przez usługi Azure Active Directory w wersji 2.

## <a name="initialize-msal"></a>Inicjowanie MSAL
Ten krok ułatwia utworzenie klasy do obsługi interakcji z biblioteką MSAL, takie jak Obsługa tokenów.

1. Otwórz **App.xaml.cs** plik i dodać odwołanie do biblioteki MSAL do klasy:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Dodaj następujące dwa wiersze do klasy aplikację (wewnątrz <code>sealed partial class App : Application</code> blok):

    ```csharp
    //Below is the clientId of your app registration. 
    //You have to replace the below with the Application Id for your app registration
    private static string ClientId = "your_client_id_here";
    
    public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);
    ```

## <a name="create-your-applications-ui"></a>Tworzenie aplikacji interfejsu użytkownika

A **MainPage.xaml** automatycznie można utworzyć pliku w ramach szablonu projektu. Otwórz ten plik, a następnie postępuj zgodnie z instrukcjami:

1.  Zastąp aplikacji **<Grid>** węzeł z:

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
    
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Użyj biblioteki uwierzytelniania firmy Microsoft (MSAL), aby uzyskać token dla interfejsu API programu Microsoft Graph

W tej sekcji przedstawia sposób użycia MSAL uzyskać token dla interfejsu API programu Microsoft Graph.

1.  W **MainPage.xaml.cs**, Dodaj odwołanie do biblioteki MSAL do klasy:

    ```csharp
    using Microsoft.Identity.Client;
    ```
2. Zastąp kod użytkownika <code>MainPage</code> klasy następującym kodem:

    ```csharp
    public sealed partial class MainPage : Page
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";
    
        //Set the scope for API call to user.read
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
Wywoływanie `AcquireTokenAsync` metoda powoduje okna, które monituje użytkowników do logowania. Aplikacje zazwyczaj wymagają użytkownikom na logowanie interakcyjne po raz pierwszy potrzebnych do uzyskania dostępu do chronionego zasobu. Może być muszą się zalogować, gdy dyskretnej operacji można uzyskać tokenu nie powiodło się (na przykład po wygaśnięciu hasła użytkownika).

#### <a name="get-a-user-token-silently"></a>Pobierz token użytkownika dyskretnej
`AcquireTokenSilentAsync` Metoda obsługuje nabycia tokenu i odnowienia bez interakcji użytkownika. Po `AcquireTokenAsync` jest wykonywana po raz pierwszy `AcquireTokenSilentAsync` jest zwykle metodę można uzyskać tokeny, które uzyskują dostęp do chronionych zasobów w kolejnych wywołaniach, ponieważ wywołania żądania lub odnowić tokeny zostały wprowadzone w trybie dyskretnym.

Po pewnym czasie `AcquireTokenSilentAsync` metoda zakończy się niepowodzeniem. Przyczyny niepowodzenia może być, czy użytkownik został wylogowany lub zmienić swoje hasło na innym urządzeniu. Gdy MSAL wykryje, że problem można rozwiązać, gdyż akcję interaktywne, jego generowane `MsalUiRequiredException` wyjątku. Aplikacja może obsłużyć tego wyjątku na dwa sposoby:

* Można wykonać połączenie przed `AcquireTokenAsync` natychmiast. To wywołanie powoduje monitowanie użytkownika do logowania. Ten wzorzec jest zazwyczaj używany w aplikacji online w przypadku, gdy brak dostępnej zawartości w trybie offline dla użytkownika. Przykładowe wygenerowane za pomocą tego Instalatora z przewodnikiem następuje tego wzorca, który można wyświetlić w czasie działania pierwszy wykonać próbki. 
    * Ponieważ żaden użytkownik nie ma korzystali z aplikacji `PublicClientApp.Users.FirstOrDefault()` zawiera wartość null i `MsalUiRequiredException` wyjątku. 
    * Następnie kod w próbce obsługuje wyjątek przez wywołanie metody `AcquireTokenAsync`, która powoduje monitowanie użytkownika do logowania.

* Ona zamiast tego prezentować oznaczenia wizualne dla użytkowników, którzy interakcyjne logowanie jest wymagane, dzięki czemu użytkownik może wybrać pozycję odpowiednich momentach do logowania. Lub aplikacji można ponowić próbę `AcquireTokenSilentAsync` później. Ten wzorzec jest często używany, gdy użytkownicy mogą używać innych funkcji aplikacji bez przerw w działaniu — na przykład, jeśli w trybie offline zawartość jest dostępna w aplikacji. W takim przypadku użytkownicy mogą wybrać, jeśli chcą, aby zalogować się do dostępu do chronionego zasobu albo Odśwież nieaktualne informacje. Alternatywnie aplikacji można zdecydować ponowić próbę `AcquireTokenSilentAsync` przywrócenie sieci po jest tymczasowo niedostępny.

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Wywołanie interfejsu API programu Microsoft Graph przy użyciu tokenu, który został uzyskany

1. Dodaj następującą metodę nowe do Twojej **MainPage.xaml.cs**. Metoda jest umożliwia `GET` żądania interfejsu API programu Graph przy użyciu nagłówka autoryzacji:

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

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Więcej informacji na temat nawiązywania połączenia przed chronionego interfejsu API REST

W tej przykładowej aplikacji `GetHttpContentWithToken` używa metody w celu HTTP `GET` żądania względem chronionego zasobu, który wymaga tokenu, a następnie wróć zawartości do obiektu wywołującego. Ta metoda dodaje token nabyte w *nagłówek autoryzacji HTTP*. Dla tego przykładu zasób jest interfejsu API programu Microsoft Graph *mnie* punktu końcowego — Wyświetla informacje o profilu użytkownika.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Dodaj metodę, aby się wylogować użytkownika

1. Aby wylogować użytkownika, dodaj następującą metodę do **MainPage.xaml.cs**:

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

### <a name="more-info-on-sign-out"></a>Więcej informacji dotyczących wylogowania

Metoda `SignOutButton_Click` Usuwa użytkownika z pamięci podręcznej użytkownika MSAL — informuje MSAL zapomnieć bieżącego użytkownika, dlatego przyszłych żądań można uzyskać tokenu można tylko się powieść, jeśli zawiera ona interaktywne.
Mimo że w tym przykładzie aplikacja obsługuje pojedynczego użytkownika, MSAL obsługuje scenariusze, w której wiele kont może być zalogowany na tym samym czasie — przykładem jest aplikacja poczty e-mail, w których użytkownik ma wiele kont.

## <a name="display-basic-token-information"></a>Wyświetlanie informacji o tokenie podstawowe

1. Dodaj następującą metodę do Twojej **MainPage.xaml.cs** do wyświetlania podstawowych informacji o tokenie:

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

Tokeny identyfikatora nabyte za pośrednictwem *OpenID Connect* również zawierać małego podzbioru informacji dotyczących użytkownika. `DisplayBasicTokenInfo` zawiera podstawowe informacje zawarte w tokenie: na przykład nazwa wyświetlana użytkownika i identyfikatorze, oraz datę wygaśnięcia tokenu i ciąg reprezentujący dostępu token samej siebie. Te informacje są wyświetlane dla można zobaczyć. Naciśnij klawisz **wywołać Microsoft interfejsu API programu Graph** przycisk wielokrotnie i zobacz, że ten sam token został ponownie dla kolejnych żądań. Można również sprawdzić Data wygaśnięcia jest rozszerzana podczas MSAL decyduje o jego czas odnowienia tokenu.

## <a name="register-your-application"></a>Rejestrowanie aplikacji

Teraz musisz zarejestrować aplikację w *portalu rejestracji aplikacji Microsoft*:
1. Przejdź do [portalu rejestracji aplikacji Microsoft](https://apps.dev.microsoft.com/portal/register-app) do rejestrowania aplikacji
2. Wprowadź nazwę aplikacji 
3. Upewnij się, że jest zaznaczona opcja instrukcje konfiguracji
4. Kliknij przycisk **dodać platform**, a następnie wybierz pozycję **aplikacji natywnej** i kliknij przycisk Zapisz
5. Skopiuj identyfikator GUID w identyfikator aplikacji, wróć do programu Visual Studio, otwórz **App.xaml.cs** i Zastąp `your_client_id_here` z został zarejestrowany identyfikator aplikacji:

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Włącz zintegrowane uwierzytelnianie na domen federacyjnych (opcjonalnie)

Aby włączyć uwierzytelnianie zintegrowane systemu Windows, gdy jest używany z federacyjnego domeny usługi Azure Active Directory, manifest aplikacji musi włączyć dodatkowe funkcje:

1. Kliknij dwukrotnie **Package.appxmanifest**
2. Wybierz **możliwości** karcie i upewnij się, że są włączone następujące ustawienia:

    - Enterprise Authentication
    - Sieci prywatne (klient i serwer)
    - Udostępnione certyfikaty użytkowników 

3. Następnie otwórz **App.xaml.cs**i Dodaj następujący kod w Konstruktorze aplikacji:

    ```csharp
    App.PublicClientApp.UseCorporateNetwork = true;
    ```

> [!IMPORTANT]
> Domyślnie dla tego przykładu nie skonfigurowano zintegrowanego uwierzytelniania systemu Windows, ponieważ aplikacje żąda *Enterprise Authentication* lub *udostępnione certyfikaty użytkowników* wymagają możliwości wyższy poziom verificationby Sklepu Windows i nie wszystkich deweloperów chcesz wyższy poziom weryfikacji. Włącz to ustawienie tylko wtedy, gdy potrzebujesz zintegrowanego uwierzytelniania systemu Windows z domeną federacyjnych usługi Azure Active Directory.


## <a name="test-your-code"></a>Testowanie kodu

Aby przetestować aplikację, naciśnij klawisz `F5` do uruchomienia projektu w programie Visual Studio. Powinna zostać wyświetlona okno główne:

![Interfejs użytkownika aplikacji](media/active-directory-uwp-v2.md/testapp-ui.png)

Gdy wszystko jest gotowe do testowania, kliknij przycisk *wywołać Microsoft interfejsu API programu Graph* i używane do logowania Microsoft Azure Active Directory (konto organizacyjne) lub konta Account Microsoft (live.com, outlook.com). Jeśli po raz pierwszy, zostanie wyświetlone okno z zapytaniem logowanie użytkownika:

![Strona logowania](media/active-directory-uwp-v2.md/sign-in-page.png)

### <a name="consent"></a>Zgody
Podczas pierwszego logowania się do aplikacji, jest wyświetlana zgody ekran podobny do następującego, gdzie należy jawnie akceptować:

![Zgoda ekranu](media/active-directory-uwp-v2.md/consentscreen.png)
### <a name="expected-results"></a>Oczekiwanych rezultatów
Powinny być widoczne informacje o profilu użytkownika zwrócony przez wywołanie interfejsu API programu Microsoft Graph na ekranie wyniki wywołania interfejsu API:

![Wyniki ekranu](media/active-directory-uwp-v2.md/uwp-results-screen.PNG)

Podstawowe informacje o tokenie nabyte za pośrednictwem powinno również zostać wyświetlone `AcquireTokenAsync` lub `AcquireTokenSilentAsync` w polu informacji o tokenie:

|Właściwość  |Format  |Opis |
|---------|---------|---------|
|**Nazwa** |Imię i nazwisko użytkownika |Imię i nazwisko użytkownika.|
|**Nazwa użytkownika** |<span>user@domain.com</span> |Nazwa użytkownika, która jest używana do identyfikacji użytkownika.|
|**Wygaśnięcia tokenu** |DateTime |Czas, w którym token jest ważny. MSAL rozszerza datę wygaśnięcia, odnowienie tokenu w razie potrzeby.|
|**Token dostępu** |Ciąg |Tokenu ciąg, który jest wysyłany do HTTP żądań, które wymagają *nagłówek autoryzacji*.|

#### <a name="see-what-is-in-the-access-token-optional"></a>Zobacz, co w tokenie dostępu (opcjonalnie)
Opcjonalnie można skopiować wartości w "Token dostępu" i wklej go w https://jwt.ms zdekodować i zapoznać się z listą oświadczeń.

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Więcej informacji o zakresach i uprawnień delegowanych

Wymaga interfejsu API programu Microsoft Graph *user.read* zakresu odczytać profilu użytkownika. Ten zakres jest automatycznie dodawany domyślnie w każdej aplikacji, która jest zarejestrowana w portalu rejestracji aplikacji. Inne interfejsy API dla programu Microsoft Graph, a także niestandardowych interfejsów API dla serwera zaplecza może wymagać dodatkowych zakresów. Wymaga interfejsu API programu Microsoft Graph *Calendars.Read* zakres, aby wyświetlić listę kalendarzy użytkownika.

Aby uzyskać dostęp do kalendarzy użytkownika w kontekście aplikacji, należy dodać *Calendars.Read* delegowane uprawnienia do informacji o rejestracji aplikacji. Następnie należy dodać *Calendars.Read* zakres do `acquireTokenSilent` wywołania. 

> [!NOTE]
> Użytkownik może zostać poproszony dla dodatkowych zgody, jak zwiększyć liczbę zakresów.

## <a name="known-issues"></a>Znane problemy

### <a name="issue-1"></a>Problem 1:
Może pojawić się jeden z następujących błędów podczas logowania dla aplikacji na federacyjnych Azure domeny usługi Active Directory:
 - "Znaleziono nie ważnego certyfikatu klienta w żądaniu.
 - Nie znaleziono w magazynie certyfikatów użytkownika prawidłowe certyfikaty.
 - Spróbuj ponownie, wybierając innej metody uwierzytelniania. "

**Przyczyna:** możliwości przedsiębiorstwa i certyfikaty nie są włączone.

**Rozwiązanie:** postępuj zgodnie z instrukcjami [zintegrowane uwierzytelnianie na Sfederowanych domen](#enable-integrated-authentication-on-federated-domains-optional)

### <a name="issue-2"></a>Problem 2:
Możesz włączyć ater [zintegrowane uwierzytelnianie na Sfederowanych domen](#enable-integrated-authentication-on-federated-domains-optional) i spróbuj użyć usługi Windows Hello na komputerze z systemem Windows 10 do logowania środowisko z kilku-factor uwierzytelniania skonfigurowane, jest wyświetlana lista certyfikatów , jednak jeśli wybierzesz używasz numeru PIN, okna Kod PIN nigdy nie zobaczy.

**Przyczyna:** jest to znane ograniczenie o brokerze uwierzytelniania sieci Web w aplikacjach platformy uniwersalnej systemu Windows z systemem Windows 10 desktop (działa poprawnie w systemie Windows 10 Mobile)

**Obejście problemu:** jako obejście, użytkownicy muszą wybrać Zaloguj się przy użyciu innych opcji, a następnie wybierz pozycję *Zaloguj się przy użyciu nazwy użytkownika i hasła* , wybierz, podaj hasło, a następnie przejdź za pośrednictwem telefonu uwierzytelniania.

