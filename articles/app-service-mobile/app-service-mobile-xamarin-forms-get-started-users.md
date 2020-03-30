---
title: Wprowadzenie do uwierzytelniania w aplikacji Formularze platformy Xamarin
description: Dowiedz się, jak używać aplikacji mobilnych do uwierzytelniania użytkowników aplikacji Xamarin Forms u dostawców tożsamości, takich jak AAD, Google, Facebook, Twitter i Microsoft.
ms.assetid: 9c55e192-c761-4ff2-8d88-72260e9f6179
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 4788aa50074016a34d906353f5b37dbba85ef104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458770"
---
# <a name="add-authentication-to-your-xamarin-forms-app"></a>Dodawanie uwierzytelniania do aplikacji Formularze platformy Xamarin
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Omówienie
W tym temacie pokazano, jak uwierzytelnić użytkowników aplikacji mobilnej usługi App Service z aplikacji klienckiej. W tym samouczku można dodać uwierzytelnianie do projektu szybki start formularzy platformy Xamarin przy użyciu dostawcy tożsamości, który jest obsługiwany przez usługę App Service. Po pomyślnym uwierzytelnieniu i autoryzowaniu przez aplikację mobilną wyświetlana jest wartość identyfikatora użytkownika, a dostęp do danych tabeli z ograniczeniami będzie można uzyskać.

## <a name="prerequisites"></a>Wymagania wstępne
Aby uzyskać najlepszy wynik w tym samouczku, zaleca się, aby najpierw ukończyć tworzenie aplikacji [formularzy xamarin][1] samouczek. Po wykonaniu tego samouczka będziesz miał projekt Xamarin Forms, który jest wieloplatformową aplikacją TodoList.

Jeśli nie używasz pobranego projektu serwera szybkiego startu, należy dodać pakiet rozszerzenia uwierzytelniania do projektu. Aby uzyskać więcej informacji na temat pakietów rozszerzeń serwera, zobacz [Praca z zestawem SDK serwera wewnętrznej bazy danych .NET dla aplikacji azure mobile apps][2].

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Rejestrowanie aplikacji w celu uwierzytelniania i konfigurowanie usług App Services
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a><a name="redirecturl"></a>Dodawanie aplikacji do adresów URL dozwolonego przekierowania zewnętrznego

Bezpieczne uwierzytelnianie wymaga zdefiniowania nowego schematu adresu URL dla aplikacji. Dzięki temu system uwierzytelniania przekierować z powrotem do aplikacji po zakończeniu procesu uwierzytelniania. W tym samouczku używamy _appname_ schematu adresu URL w całym. Możesz jednak użyć dowolnego schematu adresu URL, który wybierzesz. Powinien być unikalny dla twojej aplikacji mobilnej. Aby włączyć przekierowanie po stronie serwera:

1. W [witrynie Azure portal][8]wybierz usługę app service.

2. Kliknij opcję menu **Uwierzytelnianie / Autoryzacja.**

3. W **adresach URL dozwolonego przekierowania zewnętrznego**wprowadź . `url_scheme_of_your_app://easyauth.callback`  **url_scheme_of_your_app** w tym ciągu jest schemat adresu URL dla aplikacji mobilnej.  Powinien być zgodny ze specyfikacją normalnego adresu URL dla protokołu (użyj tylko liter i cyfr i zacznij od litery).  Należy zanotować ciąg, który wybierzesz, ponieważ trzeba będzie dostosować kod aplikacji mobilnej za pomocą schematu adresu URL w kilku miejscach.

4. Kliknij przycisk **OK**.

5. Kliknij przycisk **Zapisz**.

## <a name="restrict-permissions-to-authenticated-users"></a>Ograniczanie uprawnień do uwierzytelnionych użytkowników
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-to-the-portable-class-library"></a>Dodawanie uwierzytelniania do przenośnej biblioteki klas
Aplikacje mobilne używa [loginAsync][3] metody rozszerzenia na [MobileServiceClient][4] do logowania użytkownika za pomocą uwierzytelniania usługi App Service. W tym przykładzie użyto przepływu uwierzytelniania zarządzanego przez serwer, który wyświetla interfejs logowania dostawcy w aplikacji. Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie zarządzane przez serwer][5]. Aby zapewnić lepsze środowisko użytkownika w aplikacji produkcyjnej, należy rozważyć zamiast tego użycie [uwierzytelniania zarządzanego przez klienta.][6]

Aby uwierzytelnić się za pomocą projektu formularzy platformy Xamarin, zdefiniuj interfejs **IAuthenticate** w bibliotece klas przenośnych dla aplikacji. Następnie dodaj przycisk **logowania** do interfejsu użytkownika zdefiniowanego w bibliotece klas przenośnych, który zostanie kliknąć, aby rozpocząć uwierzytelnianie. Dane są ładowane z zaplecza aplikacji mobilnej po pomyślnym uwierzytelnieniu.

Zaimplementuj interfejs **IAuthenticate** dla każdej platformy obsługiwanej przez aplikację.

1. W programie Visual Studio lub Xamarin Studio otwórz App.cs z projektu z **Portable** w nazwie, która `using` jest portable class library projektu, a następnie dodać następującą instrukcję:

        using System.Threading.Tasks;
2. W App.cs dodaj następującą `IAuthenticate` definicję interfejsu `App` bezpośrednio przed definicją klasy.

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }
3. Aby zainicjować interfejs z implementacji specyficzne dla platformy, należy dodać następujące statyczne elementy członkowskie do **klasy aplikacji.**

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }
4. Otwórz plik TodoList.xaml z projektu Biblioteka klas przenośnych, dodaj następujący element **Button** w *elemencie układu buttonsPanel,* po istniejącym przycisku:

          <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30"
            Clicked="loginButton_Clicked"/>

    Ten przycisk powoduje wyzwalanie uwierzytelniania zarządzanego przez serwer za pomocą zaplecza aplikacji mobilnej.
5. Otwórz TodoList.xaml.cs z projektu Biblioteka klas przenośnych, a `TodoList` następnie dodaj do klasy następujące pole:

        // Track whether the user has authenticated.
        bool authenticated = false;
6. Wymień metodę **OnAppearing** na następujący kod:

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

    Ten kod zapewnia, że dane są odświeżane tylko z usługi po uwierzytelnieniu.
7. Dodaj następujący program obsługi dla **clicked** zdarzenia do **TodoList** klasy:

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }
8. Zapisz zmiany i odbuduj projekt biblioteki klas przenośnych, sprawdzając brak błędów.

## <a name="add-authentication-to-the-android-app"></a>Dodawanie uwierzytelniania do aplikacji na Androida
W tej sekcji pokazano, jak zaimplementować interfejs **IAuthenticate** w projekcie aplikacji systemu Android. Pomiń tę sekcję, jeśli nie obsługujesz urządzeń z Androidem.

1. W programie Visual Studio lub Xamarin Studio kliknij prawym przyciskiem myszy projekt **droid,** a następnie **ustaw jako projekt startowy**.
2. Naciśnij klawisz F5, aby uruchomić projekt w debugerze, a następnie sprawdź, czy po uruchomieniu aplikacji jest wywoływany nieobsługiowany wyjątek o kodzie stanu 401 (Nieautoryzowane). Kod 401 jest produkowany, ponieważ dostęp do wewnętrznej bazy danych jest ograniczony tylko do autoryzowanych użytkowników.
3. Otwórz MainActivity.cs w projekcie systemu Android i `using` dodaj następujące instrukcje:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Zaktualizuj **mainactivity** klasy zaimplementować interfejs **IAuthenticate,** w następujący sposób:

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate
5. Zaktualizuj **mainactivity** klasy przez dodanie **MobileServiceUser** pole i **Authenticate** metody, która jest wymagana przez interfejs **IAuthenticate,** w następujący sposób:

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

    Jeśli używasz dostawcy tożsamości innego niż Facebook, wybierz inną wartość dla [MobileServiceAuthenticationProvider][7].

6. Zaktualizuj plik **AndroidManifest.xml,** dodając `<application>` następujący kod XML wewnątrz elementu:

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
    Zamień `{url_scheme_of_your_app}` schemat adresów URL.
7. Dodaj następujący kod do **OnCreate** metody **MainActivity** klasy `LoadApplication()`przed wywołaniem:

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    Ten kod zapewnia, że wystawca uwierzytelnień jest inicjowany przed załadowaniem aplikacji.
8. Odbuduj aplikację, uruchom ją, a następnie zaloguj się u wybranego dostawcy uwierzytelniania i sprawdź, czy masz dostęp do danych jako uwierzytelniony użytkownik.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

**Aplikacja uległa awarii z`Java.Lang.NoSuchMethodError: No static method startActivity`**

W niektórych przypadkach konflikty w pakietach pomocy technicznej wyświetlane jako tylko ostrzeżenie w programie Visual Studio, ale aplikacja ulega awarii z tym wyjątkiem w czasie wykonywania. W takim przypadku należy upewnić się, że wszystkie pakiety pomocy technicznej, do których odwołuje się w projekcie mają tę samą wersję. [Pakiet NuGet usługi Azure Mobile Apps](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) ma zależność `Xamarin.Android.Support.CustomTabs` dla platformy Android, więc jeśli projekt używa nowszych pakietów dla pomocy technicznej, musisz bezpośrednio zainstalować ten pakiet w wymaganej wersji w celu uniknięcia konfliktów.

## <a name="add-authentication-to-the-ios-app"></a>Dodawanie uwierzytelniania do aplikacji na iOS
W tej sekcji pokazano, jak zaimplementować interfejs **IAuthenticate** w projekcie aplikacji systemu iOS. Pomiń tę sekcję, jeśli nie obsługujesz urządzeń z systemem iOS.

1. W programie Visual Studio lub Xamarin Studio kliknij prawym przyciskiem myszy projekt **systemu iOS,** a następnie **ustaw jako projekt startowy**.
2. Naciśnij klawisz F5, aby uruchomić projekt w debugerze, a następnie sprawdź, czy po uruchomieniu aplikacji jest wywoływany nieobsługiowany wyjątek o kodzie stanu 401 (Nieautoryzowane). Odpowiedź 401 jest produkowany, ponieważ dostęp na wewnętrznej bazy danych jest ograniczony tylko do autoryzowanych użytkowników.
3. Otwórz AppDelegate.cs w projekcie systemu iOS i `using` dodaj następujące instrukcje:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Zaktualizuj **appdelegate** klasy zaimplementować interfejs **IAuthenticate,** w następujący sposób:

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate
5. Zaktualizuj **AppDelegate** klasy przez dodanie **MobileServiceUser** pole i **Authenticate** metody, która jest wymagana przez interfejs **IAuthenticate,** w następujący sposób:

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
            UIAlertController avAlert = UIAlertController.Create("Sign-in result", message, UIAlertControllerStyle.Alert);
            avAlert.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
            UIApplication.SharedApplication.KeyWindow.RootViewController.PresentViewController(avAlert, true, null);

            return success;
        }

    Jeśli używasz dostawcy tożsamości innego niż Facebook, wybierz inną wartość dla [MobileServiceAuthenticationProvider].
    
6. Zaktualizuj **klasę AppDelegate,** dodając przeciążenie metody **OpenUrl** w następujący sposób:

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(url);
        }
   
7. Dodaj następujący wiersz kodu do **FinishedLaunching** metody `LoadApplication()`przed wywołaniem:

        App.Init(this);

    Ten kod zapewnia, że wystawca uwierzytelnień jest inicjowany przed załadowaniem aplikacji.

8. Otwórz Info.plist i dodaj **typ adresu URL**. Ustaw **identyfikator** na nazwę wybranej przez siebie, **schematy adresów URL** schematu adresu URL aplikacji i **rolę** na brak.

9. Odbuduj aplikację, uruchom ją, a następnie zaloguj się u wybranego dostawcy uwierzytelniania i sprawdź, czy masz dostęp do danych jako uwierzytelniony użytkownik.

## <a name="add-authentication-to-windows-10-including-phone-app-projects"></a>Dodawanie uwierzytelniania do projektów aplikacji systemu Windows 10 (w tym telefonu)
W tej sekcji pokazano, jak zaimplementować interfejs **IAuthenticate** w projektach aplikacji systemu Windows 10. Te same kroki dotyczą projektów platformy uniwersalnej systemu Windows (UWP), ale przy użyciu projektu **platformy uniwersalnej systemu Windows** (ze odnotowanymi zmianami). Pomiń tę sekcję, jeśli nie obsługujesz urządzeń z systemem Windows.

1. W programie Visual Studio kliknij prawym przyciskiem myszy projekt **platformy uniwersalnej systemuśrodku,** a następnie **ustaw jako projekt startowy**.
2. Naciśnij klawisz F5, aby uruchomić projekt w debugerze, a następnie sprawdź, czy po uruchomieniu aplikacji jest wywoływany nieobsługiowany wyjątek o kodzie stanu 401 (Nieautoryzowane). Odpowiedź 401 dzieje się, ponieważ dostęp na wewnętrznej bazy danych jest ograniczona tylko do autoryzowanych użytkowników.
3. Otwórz MainPage.xaml.cs dla projektu aplikacji systemu Windows `using` i dodaj następujące instrukcje:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    Zamień `<your_Portable_Class_Library_namespace>` obszar nazw przenośnej biblioteki klas.
4. Zaktualizuj **mainpage** klasy do zaimplementowania interfejsu **IAuthenticate,** w następujący sposób:

        public sealed partial class MainPage : IAuthenticate
5. Zaktualizuj **mainpage** klasy przez dodanie **MobileServiceUser** pole i **Authenticate** metody, która jest wymagana przez interfejs **IAuthenticate,** w następujący sposób:

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

    Jeśli używasz dostawcy tożsamości innego niż Facebook, wybierz inną wartość dla [MobileServiceAuthenticationProvider][7].

1. Dodaj następujący wiersz kodu w konstruktorze dla **MainPage** klasy przed `LoadApplication()`wywołaniem:

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);

    Zamień `<your_Portable_Class_Library_namespace>` obszar nazw przenośnej biblioteki klas.

3. Jeśli używasz platformy **uniwersalnej systemu i platformy uniwersalnej systemu,dodaj**następujące zastępowanie metody **OnActivated** do klasy **aplikacji:**

       protected override void OnActivated(IActivatedEventArgs args)
       {
           base.OnActivated(args);

            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                MobileServiceClientExtensions.ResumeWithURL(TodoItemManager.DefaultManager.CurrentClient,protocolArgs.Uri);
            }
       }

3. Otwórz Package.appxmanifest i dodaj deklarację **protokołu.** Ustaw **nazwę wyświetlaną** na nazwę wyboru, a **nazwę** na schemat adresu URL dla twojej aplikacji.

4. Odbuduj aplikację, uruchom ją, a następnie zaloguj się u wybranego dostawcy uwierzytelniania i sprawdź, czy masz dostęp do danych jako uwierzytelniony użytkownik.

## <a name="next-steps"></a>Następne kroki
Teraz, po wykonaniu tego samouczka uwierzytelniania podstawowego, należy rozważyć kontynuowanie jednego z następujących samouczków:

* [Dodawanie powiadomień wypychanych do aplikacji](app-service-mobile-xamarin-forms-get-started-push.md)

  Dowiedz się, jak dodać obsługę powiadomień wypychanych do aplikacji i skonfigurować zaplecze Aplikacji mobilnej na potrzeby wysyłania powiadomień wypychanych przy użyciu usługi Azure Notification Hubs.
* [Włączanie synchronizacji w trybie offline dla aplikacji](app-service-mobile-xamarin-forms-get-started-offline-data.md)

  Dowiedz się, jak dodać obsługę aplikacji w trybie offline przy użyciu zaplecza aplikacji mobilnej. Synchronizacja w trybie offline umożliwia użytkownikom końcowym interakcję z aplikacją mobilną — wyświetlanie, dodawanie lub modyfikowanie danych — nawet wtedy, gdy nie ma połączenia sieciowego.

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
