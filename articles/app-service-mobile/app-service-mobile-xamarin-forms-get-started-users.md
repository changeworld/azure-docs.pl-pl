---
title: Wprowadzenie do uwierzytelniania dla aplikacji mobilnej w aplikacji platformy Xamarin Forms | Dokumentacja firmy Microsoft
description: Dowiedz się, jak korzystać z aplikacji mobilnej uwierzytelniać użytkowników aplikacji platformy Xamarin Forms za pomocą różnych dostawców tożsamości, obejmującej AAD, Google, Facebook, Twitter i Microsoft.
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
ms.date: 08/07/2017
ms.author: panarasi
ms.openlocfilehash: e3e8c843437558c6d5d3a3c39bed1e647f852b18
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2018
ms.locfileid: "27593402"
---
# <a name="add-authentication-to-your-xamarin-forms-app"></a>Dodawanie uwierzytelniania do aplikacji platformy Xamarin Forms
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Przegląd
W tym temacie przedstawiono, jak uwierzytelniać użytkowników aplikacji usługi Mobile aplikacji z poziomu aplikacji klienta. W tym samouczku Dodawanie uwierzytelniania do projektu szybkiego startu Xamarin Forms przy użyciu dostawcy tożsamości, która jest obsługiwana przez usługę App Service. Po pomyślnie trwa uwierzytelnieniu i autoryzacji w aplikacji mobilnej, jest wyświetlana wartość Identyfikatora użytkownika i będzie można uzyskać dostęp do danych tabeli ograniczone.

## <a name="prerequisites"></a>Wymagania wstępne
Aby uzyskać najlepsze wyniki z tego samouczka, zaleca się wykonanie [tworzenie aplikacji platformy Xamarin Forms] [ 1] samouczka. Po ukończeniu tego samouczka, konieczne będzie projektu Xamarin Forms aplikacji TodoList wielu platform.

Jeśli nie używasz szybki start pobrany Projekt serwera, należy dodać pakietu rozszerzenia uwierzytelniania do projektu. Aby uzyskać więcej informacji na temat pakietów rozszerzenia serwera, zobacz [pracować z serwera wewnętrznej bazy danych .NET SDK usługi Azure Mobile Apps][2].

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Zarejestrować aplikację do uwierzytelniania i konfigurowanie usługi aplikacji
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Dodaj aplikację do dozwolonych przekierowania zewnętrzne adresy URL

Bezpieczne uwierzytelnianie wymaga Zdefiniuj nowy schemat adresu URL dla aplikacji. Dzięki temu system uwierzytelniania przekierować z powrotem do aplikacji po zakończeniu procesu uwierzytelniania. W tym samouczku używamy schemat adresu URL _appname_ w ciągu. Można jednak użyć dowolnego wybranego schematu URL. Powinien być unikatowy w aplikacji mobilnej. Aby włączyć przekierowywanie po stronie serwera:

1. W [portalu Azure][8], wybierz usługę aplikacji.

2. Kliknij przycisk **uwierzytelniania / autoryzacji** opcji menu.

3. W **dozwolone zewnętrznych adresów URL przekierowań**, wprowadź `url_scheme_of_your_app://easyauth.callback`.  **Url_scheme_of_your_app** w tym ciągu jest schemat adresu URL dla aplikacji mobilnej.  Musi on być zgodny normalne Specyfikacja adresu URL dla protokołu (Użyj litery i tylko cyfry i rozpoczyna się od litery).  Należy zanotuj ciąg, który wybrany jako trzeba będzie dostosować kodu aplikacji mobilnej przy użyciu schemat adresu URL w kilku miejscach.

4. Kliknij przycisk **OK**.

5. Kliknij pozycję **Zapisz**.

## <a name="restrict-permissions-to-authenticated-users"></a>Ogranicz uprawnienia dla uwierzytelnionych użytkowników
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-to-the-portable-class-library"></a>Dodawanie uwierzytelniania do biblioteki klas przenośnych
Aplikacje mobilne używa [LoginAsync] [ 3] — metoda rozszerzenia na [MobileServiceClient] [ 4] do logowania użytkownika z usługi aplikacji uwierzytelniania. W przykładzie użyto przepływ uwierzytelniania serwer zarządzany, który wyświetla dostawcy w interfejsie logowania w aplikacji. Aby uzyskać więcej informacji, zobacz [serwer zarządzany uwierzytelniania][5]. Aby zapewnić lepsze środowisko użytkownika w aplikacji produkcyjnej, należy rozważyć zamiast za pomocą [zarządzanych przez klienta uwierzytelniania][6].

Do uwierzytelniania za pomocą projektu Xamarin Forms, zdefiniuj **IAuthenticate** interfejsu przenośnej biblioteki klas dla aplikacji. Następnie dodaj **logowania** przycisk interfejsu użytkownika zdefiniowane w przenośną bibliotekę klas kliknij, aby uruchomić uwierzytelniania. Dane są ładowane z zaplecza aplikacji mobilnej po pomyślnym uwierzytelnieniu.

Implementowanie **IAuthenticate** interfejs dla każdej platformy obsługiwane przez aplikację.

1. W programie Visual Studio lub Xamarin Studio Otwórz App.cs z projektu o **przenośne** w nazwie, czyli projektu biblioteki przenośnej klasy, a następnie dodaj następujące `using` instrukcji:

        using System.Threading.Tasks;
2. W App.cs, Dodaj następujący `IAuthenticate` definicji interfejsu bezpośrednio przed `App` definicji klasy.

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }
3. Można zainicjować interfejsu z implementacją specyficzne dla platformy, należy dodać następujące statyczne elementy członkowskie do **aplikacji** klasy.

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }
4. Otwórz TodoList.xaml z projektu biblioteki klas przenośnych, należy dodać następujące **przycisk** element *buttonsPanel* elementu układu po istniejącego przycisku:

          <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30"
            Clicked="loginButton_Clicked"/>

    Ten przycisk wyzwala serwer zarządzany uwierzytelniania za pomocą zaplecza aplikacji mobilnej.
5. Otwórz TodoList.xaml.cs z projektu biblioteki klas przenośnych, a następnie dodaj poniższe pole, aby `TodoList` klasy:

        // Track whether the user has authenticated.
        bool authenticated = false;
6. Zastąp **OnAppearing** metodę z następującym kodem:

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

    Ten kod upewnia się, że tylko odświeżania danych z usługi po Cię uwierzytelniono.
7. Dodaj następujące obsługę **kliknięto element** zdarzenia **TodoList** klasy:

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }
8. Zapisz zmiany i ponownie skompilować projekt przenośnej biblioteki klas weryfikowanie żadne błędy.

## <a name="add-authentication-to-the-android-app"></a>Dodawanie uwierzytelniania do aplikacji systemu Android
W tej sekcji przedstawiono sposób wykonania **IAuthenticate** interfejs w projekcie aplikacji systemu Android. Pomiń tę sekcję, jeśli urządzenia z systemem Android nie są obsługiwane.

1. W programie Visual Studio lub Xamarin Studio kliknij prawym przyciskiem myszy **droid** projektu, następnie **Ustaw jako projekt startowy**.
2. Naciśnij klawisz F5, aby uruchomić projekt w debugerze, a następnie sprawdź, czy wystąpił nieobsługiwany wyjątek przy użyciu kodu stanu 401 (bez autoryzacji) jest wywoływane po uruchomieniu aplikacji. 401 kod jest generowany, ponieważ dostęp do wewnętrznej bazy danych jest ograniczony tylko autoryzowanym użytkownikom.
3. Otwórz MainActivity.cs w projekcie systemu Android i dodaj następujące `using` instrukcji:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Aktualizacja **MainActivity** klasy do zaimplementowania **IAuthenticate** interfejsu, w następujący sposób:

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate
5. Aktualizacja **MainActivity** klasy, dodając **MobileServiceUser** pola i **Uwierzytelnij** metodę, która jest wymagana przez **IAuthenticate** interfejsu, w następujący sposób:

        // Define a authenticated user.
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

    Jeśli używasz dostawcy tożsamości innego niż usługi Facebook, wybierz inną wartość dla [MobileServiceAuthenticationProvider][7].

6. Aktualizacja **AndroidManifest.xml** plików, dodając następujące XML wewnątrz `<application>` elementu:

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
    Zastąp `{url_scheme_of_your_app}` ze schematem adresu URL.
7. Dodaj następujący kod do **OnCreate** metody **MainActivity** klasy przed wywołaniem do `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    Ten kod gwarantuje, że wystawca uwierzytelnienia jest inicjowana przed obciążeń aplikacji.
8. Skompiluj ponownie aplikację, uruchom go, a następnie zaloguj się przy użyciu dostawcy uwierzytelniania wybraną i sprawdź, czy można uzyskać dostępu do danych jako użytkownik uwierzytelniony.

## <a name="add-authentication-to-the-ios-app"></a>Dodawanie uwierzytelniania do aplikacji systemu iOS
W tej sekcji przedstawiono sposób wykonania **IAuthenticate** interfejs w projekcie aplikacji systemu iOS. Pomiń tę sekcję, jeśli urządzenia z systemem iOS nie są obsługiwane.

1. W programie Visual Studio lub Xamarin Studio kliknij prawym przyciskiem myszy **iOS** projektu, następnie **Ustaw jako projekt startowy**.
2. Naciśnij klawisz F5, aby uruchomić projekt w debugerze, a następnie sprawdź, czy wystąpił nieobsługiwany wyjątek przy użyciu kodu stanu 401 (bez autoryzacji) jest wywoływane po uruchomieniu aplikacji. Odpowiedzi 401 jest generowany, ponieważ dostęp do wewnętrznej bazy danych jest ograniczony tylko autoryzowanym użytkownikom.
3. Otwórz AppDelegate.cs w projekcie systemu iOS i dodaj następujące `using` instrukcji:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Aktualizacja **AppDelegate** klasy do zaimplementowania **IAuthenticate** interfejsu, w następujący sposób:

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate
5. Aktualizacja **AppDelegate** klasy, dodając **MobileServiceUser** pola i **Uwierzytelnij** metodę, która jest wymagana przez **IAuthenticate** interfejsu, w następujący sposób:

        // Define a authenticated user.
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

    Jeśli używasz dostawcy tożsamości innego niż usługi Facebook, wybierz inną wartość dla [MobileServiceAuthenticationProvider].
    
6. Aktualizacja **AppDelegate** klasy, dodając **OpenUrl** przeciążenia metody, w następujący sposób:

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(url);
        }
   
7. Dodaj następujący wiersz kodu w celu **FinishedLaunching** metody przed wywołanie `LoadApplication()`:

        App.Init(this);

    Ten kod gwarantuje, że wystawca uwierzytelnienia jest zainicjowany przed załadowaniem aplikacji.

8. Otwórz Info.plist i Dodaj **typ URL**. Ustaw **identyfikator** do wybierania, nazwę **Schematy adresów URL** do schemat adresu URL dla aplikacji oraz **roli** None.

9. Skompiluj ponownie aplikację, uruchom go, a następnie zaloguj się przy użyciu dostawcy uwierzytelniania wybraną i sprawdź, czy można uzyskać dostępu do danych jako użytkownik uwierzytelniony.

## <a name="add-authentication-to-windows-10-including-phone-app-projects"></a>Dodawanie uwierzytelniania do projektów aplikacji systemu Windows 10 (w tym telefon)
W tej sekcji przedstawiono sposób wykonania **IAuthenticate** interfejsu w projektach aplikacji systemu Windows 10. Zastosuj te same kroki dla projektów uniwersalnych platformy systemu Windows (UWP), ale przy użyciu **UWP** projektu (ze zmianami dostrzeżone). Pomiń tę sekcję, jeśli urządzenia z systemem Windows nie są obsługiwane.

1. W programie Visual Studio, kliknij prawym przyciskiem myszy **UWP** projektu, następnie **Ustaw jako projekt startowy**.
2. Naciśnij klawisz F5, aby uruchomić projekt w debugerze, a następnie sprawdź, czy wystąpił nieobsługiwany wyjątek przy użyciu kodu stanu 401 (bez autoryzacji) jest wywoływane po uruchomieniu aplikacji. Odpowiedzi 401 odbywa się, ponieważ dostęp do wewnętrznej bazy danych jest ograniczony tylko autoryzowanym użytkownikom.
3. Otwórz MainPage.xaml.cs dla projektu aplikacji systemu Windows i dodaj następujące `using` instrukcji:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    Zastąp `<your_Portable_Class_Library_namespace>` z przestrzenią nazw biblioteki klas przenośnych.
4. Aktualizacja **MainPage** klasy do zaimplementowania **IAuthenticate** interfejsu, w następujący sposób:

        public sealed partial class MainPage : IAuthenticate
5. Aktualizacja **MainPage** klasy, dodając **MobileServiceUser** pola i **Uwierzytelnij** metodę, która jest wymagana przez **IAuthenticate**interfejsu, w następujący sposób:

        // Define a authenticated user.
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

    Jeśli używasz dostawcy tożsamości innego niż usługi Facebook, wybierz inną wartość dla [MobileServiceAuthenticationProvider][7].

1. Dodaj następujący wiersz kodu w Konstruktorze **MainPage** klasy przed wywołaniem do `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);

    Zastąp `<your_Portable_Class_Library_namespace>` z przestrzenią nazw biblioteki klas przenośnych.

3. Jeśli używasz **platformy uniwersalnej systemu Windows**, Dodaj następujący **OnActivated** zastąpienie metody **aplikacji** klasy:

       protected override void OnActivated(IActivatedEventArgs args)
       {
           base.OnActivated(args);

            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(protocolArgs.Uri);
            }
       }

3. Otwórz Package.appxmanifest i Dodaj **protokołu** deklaracji. Ustaw **Nazwa wyświetlana** do wybierania, nazwę i **nazwa** do schemat adresu URL dla aplikacji.

4. Skompiluj ponownie aplikację, uruchom go, a następnie zaloguj się przy użyciu dostawcy uwierzytelniania wybraną i sprawdź, czy można uzyskać dostępu do danych jako użytkownik uwierzytelniony.

## <a name="next-steps"></a>Kolejne kroki
Teraz, aby ukończyć w tym samouczku uwierzytelnianie podstawowe, należy wziąć pod uwagę kontynuowanie na jedno z następujących samouczków:

* [Dodawanie powiadomień wypychanych do aplikacji](app-service-mobile-xamarin-forms-get-started-push.md)

  Dowiedz się, jak dodać obsługę powiadomień wypychanych do aplikacji i skonfigurować zaplecze Aplikacji mobilnej na potrzeby wysyłania powiadomień wypychanych przy użyciu usługi Azure Notification Hubs.
* [Włączanie synchronizacji w trybie offline dla aplikacji](app-service-mobile-xamarin-forms-get-started-offline-data.md)

  Dowiedz się, jak dodać aplikację przy użyciu zaplecza aplikacji mobilnej obsługi w trybie offline. Synchronizacja w trybie offline umożliwia użytkownikom końcowym interakcję z aplikacji mobilnej — przeglądanie, dodawanie lub modyfikowanie danych — nawet wtedy, gdy istnieje połączenie sieciowe.

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
