---
title: Rozpoczynanie pracy z usługą uwierzytelniania dla aplikacji mobilnych w aplikacji platformy Xamarin Forms | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać funkcji Mobile Apps, uwierzytelniać użytkowników aplikacji platformy Xamarin Forms przy użyciu różnych dostawców tożsamości, obejmującej usługi AAD, Google, Facebook, Twitter i Microsoft.
services: app-service\mobile
documentationcenter: xamarin
author: panarasi
manager: crdun
editor: ''
ms.assetid: 9c55e192-c761-4ff2-8d88-72260e9f6179
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 09/24/2018
ms.author: panarasi
ms.openlocfilehash: d97c4689b40dd0bcf2ab083c688c547014a4de0b
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60011666"
---
# <a name="add-authentication-to-your-xamarin-forms-app"></a>Dodawanie uwierzytelniania do aplikacji platformy Xamarin Forms
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Omówienie
W tym temacie dowiesz się, jak uwierzytelniać użytkowników aplikacji usługi App Service Mobile z aplikacji klienckiej. W tym samouczku dodasz uwierzytelnianie do projektu quickstart narzędzia Xamarin Forms przy użyciu dostawcy tożsamości, która jest obsługiwana przez usługę App Service. Po są pomyślnie uwierzytelniony i autoryzowany przez aplikację Mobile, jest wyświetlana wartość Identyfikatora użytkownika i będzie można uzyskiwać dostęp do danych tabel z ograniczeniami.

## <a name="prerequisites"></a>Wymagania wstępne
Najlepszych wyników za pomocą tego samouczka, zaleca się wykonanie [tworzenie aplikacji platformy Xamarin Forms] [ 1] samouczka. Po ukończeniu tego samouczka trzeba będzie projektu Xamarin Forms, który jest aplikację listy zadań dla wielu platform.

Jeśli nie używasz projektu serwera pobranego — szybki start, należy dodać pakiet rozszerzenia uwierzytelnianie do projektu. Aby uzyskać więcej informacji na temat pakietów rozszerzeń serwera, zobacz [pracy z zestawem SDK serwera zaplecza platformy .NET dla usługi Azure Mobile Apps][2].

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Rejestrowanie aplikacji do uwierzytelniania i konfigurowanie usług aplikacji
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Dodawanie aplikacji do adresów URL dozwolonych zewnętrznego przekierowania

Bezpieczne uwierzytelnianie wymaga, zdefiniuj nowy schemat adresu URL dla aplikacji. Dzięki temu system uwierzytelniania przekierować z powrotem do aplikacji po zakończeniu procesu uwierzytelniania. W tym samouczku używamy schemat adresu URL _appname_ w całym. Można jednak użyć dowolnego wybranego schematu URL. Powinien on być unikatowy dla twojej aplikacji mobilnej. Aby włączyć przekierowywanie po stronie serwera:

1. W [witryny Azure portal][8], wybierz swoją usługę aplikacji.

2. Kliknij przycisk **uwierzytelniania / autoryzacji** opcji menu.

3. W **dozwolone zewnętrzne adresy URL przekierowania**, wprowadź `url_scheme_of_your_app://easyauth.callback`.  **Url_scheme_of_your_app** w tym ciągu jest schemat adresu URL aplikacji mobilnej.  Powinien on być zgodny z normalnej Specyfikacja adresu URL dla protokołu (litery użytkowania i tylko cyfry oraz rozpoczynać się literą).  Należy zapamiętać, ciąg, który wybierzesz, ponieważ trzeba będzie dostosować kod aplikacji mobilnych za pomocą schemat adresu URL w kilku miejscach.

4. Kliknij przycisk **OK**.

5. Kliknij pozycję **Zapisz**.

## <a name="restrict-permissions-to-authenticated-users"></a>Ogranicz uprawnienia do uwierzytelnionych użytkowników
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-to-the-portable-class-library"></a>Dodawanie uwierzytelniania do biblioteki klas przenośnych
Używa funkcji Mobile Apps [LoginAsync] [ 3] metody rozszerzenia [MobileServiceClient] [ 4] zalogowania użytkownika przy użyciu usługi App Service uwierzytelnianie. Ta próbka używa przepływu uwierzytelniania serwer zarządzany, który wyświetla interfejsie dostawcy logowania w aplikacji. Aby uzyskać więcej informacji, zobacz [serwer zarządzany uwierzytelniania][5]. Aby zapewnić lepsze środowisko użytkownika w swojej aplikacji w środowisku produkcyjnym, należy wziąć pod uwagę zamiast przy użyciu [zarządzanych przez klienta uwierzytelniania][6].

Aby uwierzytelniać się w projekcie Xamarin Forms, zdefiniuj **IAuthenticate** interfejsu in Portable Class Library dla aplikacji. Następnie dodaj **logowania** przycisku do interfejsu użytkownika zdefiniowane w Portable Class Library, którego kliknięcie pozwala uruchomić uwierzytelniania. Dane są ładowane z zaplecza aplikacji mobilnej po pomyślnym uwierzytelnieniu.

Implementowanie **IAuthenticate** interfejs dla każdej platformy obsługiwane przez aplikację.

1. W programie Visual Studio lub Xamarin Studio, otwórz App.cs z projektu za pomocą **przenośne** nazwę, która jest projekt przenośnej biblioteki klas, a następnie dodaj następujący kod `using` instrukcji:

        using System.Threading.Tasks;
2. W App.cs, Dodaj następujący kod `IAuthenticate` definicji interfejsu bezpośrednio przed `App` definicji klasy.

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }
3. Aby zainicjować interfejsu z implementacją specyficzne dla platformy, Dodaj następujące składowe statyczne do **aplikacji** klasy.

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }
4. Otwórz TodoList.xaml z projektu Portable Class Library, Dodaj następujący kod **przycisk** element *buttonsPanel* elementu układu, po istniejącego przycisku:

          <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30"
            Clicked="loginButton_Clicked"/>

    Ten przycisk uruchamia serwer zarządzany uwierzytelniania przy użyciu zaplecza aplikacji mobilnej.
5. Otwórz TodoList.xaml.cs z projektu Portable Class Library, a następnie dodaj następujące pola `TodoList` klasy:

        // Track whether the user has authenticated.
        bool authenticated = false;
6. Zastąp **OnAppearing** metoda następującym kodem:

        protected override async void OnAppearing()
        {
            base.OnAppearing();

            // Refresh items only when authenticated.
            if (authenticated == true)
            {
                // Set syncItems to true in order to synchronize the data
                // on startup when running in offline mode.
                await RefreshItems(true, syncItems: false);

                // Hide the Sign-in button.
                this.loginButton.IsVisible = false;
            }
        }

    Ten kod sprawdza, czy dane są odświeżane z usługi po użytkownik został uwierzytelniony.
7. Dodaj następujący program obsługi dla **kliknięciu** zdarzenia **TodoList** klasy:

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }
8. Zapisz zmiany i skompiluj ponownie projekt biblioteki klas przenośnych weryfikowanie bez błędów.

## <a name="add-authentication-to-the-android-app"></a>Dodawanie uwierzytelniania do aplikacji dla systemu Android
W tej sekcji pokazano, jak zaimplementować **IAuthenticate** interfejsu w projekcie aplikacji dla systemu Android. Pomiń tę sekcję, jeśli nie są obsługiwane urządzenia z systemem Android.

1. W programie Visual Studio lub Xamarin Studio kliknij prawym przyciskiem myszy **droid** projekt następnie **Ustaw jako projekt startowy**.
2. Naciśnij klawisz F5, aby uruchomić projekt w debugerze, a następnie sprawdź, czy wystąpił nieobsługiwany wyjątek, kod stanu 401 (bez autoryzacji) jest wywoływane po uruchomieniu aplikacji. 401 kod jest generowany, ponieważ dostęp do wewnętrznej bazy danych jest ograniczony tylko autoryzowanym użytkownikom.
3. Otwórz MainActivity.cs w projekcie dla systemu Android i dodaj następującą `using` instrukcji:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Aktualizacja **MainActivity** klasy do zaimplementowania **IAuthenticate** interfejsu, w następujący sposób:

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate
5. Aktualizacja **MainActivity** klasy, dodając **MobileServiceUser** pola i **Uwierzytelnij** metody, która jest wymagana przez **IAuthenticate** interfejsu, w następujący sposób:

        // Define an authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this, 
                    MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                if (user != null)
                {
                    message = string.Format("you are now signed-in as {0}.",
                        user.UserId);
                    success = true;
                }
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }

            // Display the success or failure message.
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(message);
            builder.SetTitle("Sign-in result");
            builder.Create().Show();

            return success;
        }

    Jeśli używasz dostawcy tożsamości innych niż usługi Facebook, wybierz inną wartość dla [MobileServiceAuthenticationProvider][7].

6. Aktualizacja **AndroidManifest.xml** pliku, dodając następujący kod XML wewnątrz `<application>` elementu:

    ```xml
    <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
      <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
      </intent-filter>
    </activity>
    ```
    Zastąp `{url_scheme_of_your_app}` z schemat adresu URL.
7. Dodaj następujący kod do **OnCreate** metody **MainActivity** klasy przed wywołaniem do `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    Ten kod zapewnia, że wystawcy uwierzytelnienia jest inicjowana przed ładowania aplikacji.
8. Ponownie skompiluj aplikację, uruchom go, a następnie zaloguj się przy użyciu dostawcy uwierzytelniania została wybrana i upewnij się, że jesteś w stanie uzyskać dostęp do danych jako użytkownik uwierzytelniony.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

**Aplikacja uległa awarii, za pomocą `Java.Lang.NoSuchMethodError: No static method startActivity`**

W niektórych przypadkach powoduje konflikt w pakietach pomocy technicznej wyświetlany jako tylko ostrzeżeniem w Visual studio, ale awarii aplikacji za pomocą tego wyjątku w czasie wykonywania. W takim przypadku należy się upewnić, że wszystkie pakiety obsługi, określane w projekcie mają tę samą wersję. [Pakiet NuGet usługi Azure Mobile Apps](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) ma zależność `Xamarin.Android.Support.CustomTabs` dla platformy Android, więc jeśli projekt używa nowszych pakietów dla pomocy technicznej, musisz bezpośrednio zainstalować ten pakiet w wymaganej wersji w celu uniknięcia konfliktów.

## <a name="add-authentication-to-the-ios-app"></a>Dodawanie uwierzytelniania do aplikacji dla systemu iOS
W tej sekcji pokazano, jak zaimplementować **IAuthenticate** interfejsu w projekcie aplikacji dla systemu iOS. Pomiń tę sekcję, jeśli nie są obsługiwane urządzenia z systemem iOS.

1. W programie Visual Studio lub Xamarin Studio kliknij prawym przyciskiem myszy **iOS** projekt następnie **Ustaw jako projekt startowy**.
2. Naciśnij klawisz F5, aby uruchomić projekt w debugerze, a następnie sprawdź, czy wystąpił nieobsługiwany wyjątek, kod stanu 401 (bez autoryzacji) jest wywoływane po uruchomieniu aplikacji. Odpowiedzi 401 jest generowany, ponieważ dostęp do wewnętrznej bazy danych jest ograniczony tylko autoryzowanym użytkownikom.
3. Otwórz AppDelegate.cs w projekcie dla systemu iOS i dodaj następującą `using` instrukcji:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Aktualizacja **elemencie AppDelegate** klasy do zaimplementowania **IAuthenticate** interfejsu, w następujący sposób:

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate
5. Aktualizacja **elemencie AppDelegate** klasy, dodając **MobileServiceUser** pola i **Uwierzytelnij** metody, która jest wymagana przez **IAuthenticate** interfejsu, w następujący sposób:

        // Define an authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    if (user != null)
                    {
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                        success = true;
                    }
                }
            }
            catch (Exception ex)
            {
               message = ex.Message;
            }

            // Display the success or failure message.
            UIAlertView avAlert = new UIAlertView("Sign-in result", message, null, "OK", null);
            avAlert.Show();

            return success;
        }

    Jeśli używasz dostawcy tożsamości innych niż usługi Facebook, wybierz inną wartość dla [MobileServiceAuthenticationProvider].
    
6. Aktualizacja **elemencie AppDelegate** klasy, dodając **OpenUrl** przeciążenia metody, w następujący sposób:

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(url);
        }
   
7. Dodaj następujący wiersz kodu w celu **FinishedLaunching** metoda przed wywołaniem do `LoadApplication()`:

        App.Init(this);

    Ten kod zapewnia, że wystawcy uwierzytelnienia jest zainicjowany przed załadowaniu aplikacji.

8. Otwórz plik Info.plist i Dodaj **typ adresu URL**. Ustaw **identyfikator** nazwę wybranej, **Schematy adresów URL** na schemat adresu URL dla swojej aplikacji i **roli** None.

9. Ponownie skompiluj aplikację, uruchom go, a następnie zaloguj się przy użyciu dostawcy uwierzytelniania została wybrana i upewnij się, że jesteś w stanie uzyskać dostęp do danych jako użytkownik uwierzytelniony.

## <a name="add-authentication-to-windows-10-including-phone-app-projects"></a>Dodawanie uwierzytelniania do projektów aplikacji systemu Windows 10 (w tym telefon)
W tej sekcji pokazano, jak zaimplementować **IAuthenticate** interfejsu w projektach aplikacji systemu Windows 10. Te same kroki zastosowania dla projektów uniwersalnych platformy Windows (UWP), ale przy użyciu **platformy uniwersalnej systemu Windows** projektu (za pomocą dostrzeżone zmian). Jeśli nie są obsługiwane urządzenia Windows, należy pominąć tę sekcję.

1. W programie Visual Studio, kliknij prawym przyciskiem myszy **platformy uniwersalnej systemu Windows** projekt następnie **Ustaw jako projekt startowy**.
2. Naciśnij klawisz F5, aby uruchomić projekt w debugerze, a następnie sprawdź, czy wystąpił nieobsługiwany wyjątek, kod stanu 401 (bez autoryzacji) jest wywoływane po uruchomieniu aplikacji. Odpowiedzi 401 odbywa się, ponieważ dostęp do wewnętrznej bazy danych jest ograniczony tylko autoryzowanym użytkownikom.
3. Otwórz MainPage.xaml.cs dla projektu aplikacji Windows i Dodaj następujący kod `using` instrukcji:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    Zastąp `<your_Portable_Class_Library_namespace>` z przestrzenią nazw dla biblioteki klas przenośnych.
4. Aktualizacja **MainPage** klasy do zaimplementowania **IAuthenticate** interfejsu, w następujący sposób:

        public sealed partial class MainPage : IAuthenticate
5. Aktualizacja **MainPage** klasy, dodając **MobileServiceUser** pola i **Uwierzytelnij** metody, która jest wymagana przez **IAuthenticate**interfejsu, w następujący sposób:

        // Define an authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            string message = string.Empty;
            var success = false;

            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    if (user != null)
                    {
                        success = true;
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                    }
                }

            }
            catch (Exception ex)
            {
                message = string.Format("Authentication Failed: {0}", ex.Message);
            }

            // Display the success or failure message.
            await new MessageDialog(message, "Sign-in result").ShowAsync();

            return success;
        }

    Jeśli używasz dostawcy tożsamości innych niż usługi Facebook, wybierz inną wartość dla [MobileServiceAuthenticationProvider][7].

1. Dodaj następujący wiersz kodu w Konstruktorze **MainPage** klasy przed wywołaniem do `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);

    Zastąp `<your_Portable_Class_Library_namespace>` z przestrzenią nazw dla biblioteki klas przenośnych.

3. Jeśli używasz **platformy uniwersalnej systemu Windows**, Dodaj następujący kod **OnActivated** zastąpienie metody **aplikacji** klasy:

       protected override void OnActivated(IActivatedEventArgs args)
       {
           base.OnActivated(args);

            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                MobileServiceClientExtensions.ResumeWithURL(TodoItemManager.DefaultManager.CurrentClient,protocolArgs.Uri);
            }
       }

3. Otwórz Package.appxmanifest i Dodaj **protokołu** deklaracji. Ustaw **nazwę wyświetlaną** nazwę wybranej i **nazwa** na schemat adresu URL dla aplikacji.

4. Ponownie skompiluj aplikację, uruchom go, a następnie zaloguj się przy użyciu dostawcy uwierzytelniania została wybrana i upewnij się, że jesteś w stanie uzyskać dostęp do danych jako użytkownik uwierzytelniony.

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy została zakończona w tym samouczku uwierzytelnianie podstawowe, należy wziąć pod uwagę przejściem do jednej z następujących samouczków:

* [Dodawanie powiadomień wypychanych do aplikacji](app-service-mobile-xamarin-forms-get-started-push.md)

  Dowiedz się, jak dodać obsługę powiadomień wypychanych do aplikacji i skonfigurować zaplecze Aplikacji mobilnej na potrzeby wysyłania powiadomień wypychanych przy użyciu usługi Azure Notification Hubs.
* [Włączanie synchronizacji w trybie offline dla aplikacji](app-service-mobile-xamarin-forms-get-started-offline-data.md)

  Dowiedz się, jak dodać obsługę aplikacji w trybie offline przy użyciu zaplecza aplikacji mobilnej. Synchronizacja w trybie offline umożliwia użytkownikom końcowym interakcję z aplikacją mobilną — wyświetlanie, dodawanie lub modyfikowanie danych — nawet w przypadku braku połączenia sieciowego.

<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-xamarin-forms-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: https://msdn.microsoft.com/library/azure/dn268341(v=azure.10).aspx
[4]: https://msdn.microsoft.com/library/azure/JJ553674(v=azure.10).aspx
[5]: app-service-mobile-dotnet-how-to-use-client-library.md#serverflow
[6]: app-service-mobile-dotnet-how-to-use-client-library.md#clientflow
[7]: https://msdn.microsoft.com/library/azure/jj730936(v=azure.10).aspx
[8]: https://portal.azure.com
