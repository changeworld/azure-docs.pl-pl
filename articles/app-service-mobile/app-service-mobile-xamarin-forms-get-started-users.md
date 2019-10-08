---
title: Wprowadzenie do uwierzytelniania Mobile Apps w aplikacji interfejsu Xamarin Forms | Microsoft Docs
description: Dowiedz się, jak używać Mobile Apps do uwierzytelniania użytkowników aplikacji interfejsu Xamarin Forms za pomocą różnych dostawców tożsamości, takich jak AAD, Google, Facebook, Twitter i Microsoft.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 9c55e192-c761-4ff2-8d88-72260e9f6179
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 643539fb569cdefba8e04d1ac08e73055624d3ae
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025059"
---
# <a name="add-authentication-to-your-xamarin-forms-app"></a>Dodawanie uwierzytelniania do aplikacji interfejsu Xamarin Forms
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center obsługuje kompleksowe i zintegrowane usługi centralne do tworzenia aplikacji mobilnych. Deweloperzy mogą używać usług **kompilowania**, **testowania** i **dystrybucji** , aby skonfigurować ciągłą integrację i potok dostarczania. Po wdrożeniu aplikacji deweloperzy mogą monitorować stan i użycie swojej aplikacji przy użyciu usług **analizy** i **diagnostyki** oraz angażować się z użytkownikami za pomocą usługi **wypychania** . Deweloperzy mogą również korzystać z **uwierzytelniania** w celu uwierzytelniania użytkowników i usługi **danych** w celu utrwalania i synchronizowania danych aplikacji w chmurze.
> Jeśli chcesz zintegrować usługi w chmurze w swojej aplikacji mobilnej, zarejestruj się w usłudze App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) już dziś.

## <a name="overview"></a>Przegląd
W tym temacie pokazano, jak uwierzytelniać użytkowników aplikacji mobilnej App Service z poziomu aplikacji klienckiej. W tym samouczku dodasz uwierzytelnianie do projektu narzędzia Xamarin Forms Start przy użyciu dostawcy tożsamości obsługiwanego przez App Service. Po pomyślnym uwierzytelnieniu i autoryzacji aplikacji mobilnej zostanie wyświetlona wartość identyfikatora użytkownika i będzie można uzyskać dostęp do ograniczonych danych tabeli.

## <a name="prerequisites"></a>Wymagania wstępne
W celu uzyskania najlepszego wyniku w tym samouczku zalecamy zakończenie pracy z samouczkiem [Tworzenie aplikacji Xamarin Forms][1] . Po ukończeniu tego samouczka będziesz mieć projekt Xamarin Forms, który jest wieloplatformową aplikacją TodoList.

Jeśli nie używasz pobranego projektu szybkiego startu serwera, musisz dodać pakiet rozszerzenia uwierzytelniania do projektu. Aby uzyskać więcej informacji o pakietach rozszerzeń serwera, zobacz [Work with the .NET zaplecz Server SDK for Azure Mobile Apps][2].

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Zarejestruj aplikację pod kątem uwierzytelniania i skonfiguruj App Services
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Dodaj aplikację do dozwolonych zewnętrznych adresów URL przekierowania

Bezpieczne uwierzytelnianie wymaga zdefiniowania nowego schematu adresu URL dla aplikacji. Dzięki temu system uwierzytelniania może przekierować z powrotem do aplikacji po zakończeniu procesu uwierzytelniania. W tym samouczku używamy _w systemie adresu_ URL. Można jednak użyć dowolnego wybranego schematu adresów URL. Powinna być unikatowa dla aplikacji mobilnej. Aby włączyć przekierowanie po stronie serwera:

1. W [Azure Portal][8]wybierz App Service.

2. Kliknij opcję menu **uwierzytelnianie/autoryzacja** .

3. W polu **dozwolone adresy URL zewnętrznych przekierowań**wprowadź wartość `url_scheme_of_your_app://easyauth.callback`.  **Url_scheme_of_your_app** w tym ciągu to schemat adresu URL aplikacji mobilnej.  Powinna być zgodna ze specyfikacją normalnych adresów URL dla protokołu (używaj tylko liter i cyfr i zaczynać się od litery).  Należy zanotować ciąg, który wybierzesz, ponieważ trzeba będzie dostosować kod aplikacji mobilnej przy użyciu schematu adresu URL w kilku miejscach.

4. Kliknij przycisk **OK**.

5. Kliknij przycisk **Save** (Zapisz).

## <a name="restrict-permissions-to-authenticated-users"></a>Ograniczanie uprawnień do uwierzytelnionych użytkowników
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-to-the-portable-class-library"></a>Dodawanie uwierzytelniania do przenośnej biblioteki klas
Mobile Apps używa metody rozszerzenia [LoginAsync][3] w [MobileServiceClient][4] do logowania użytkownika z uwierzytelnianiem App Service. Ten przykład używa przepływu uwierzytelniania zarządzanego przez serwer, który wyświetla interfejs logowania dostawcy w aplikacji. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie zarządzane przez serwer][5]. Aby zapewnić lepsze środowisko użytkownika w aplikacji produkcyjnej, należy rozważyć użycie [uwierzytelniania zarządzanego przez klienta][6].

Aby uwierzytelnić się za pomocą projektu Xamarin Forms, zdefiniuj Interfejs **IAuthenticate** w przenośnej bibliotece klas dla aplikacji. Następnie Dodaj przycisk **logowania** do interfejsu użytkownika zdefiniowanego w bibliotece klas przenośnych, którą klikniesz, aby uruchomić uwierzytelnianie. Dane są ładowane z zaplecza aplikacji mobilnej po pomyślnym uwierzytelnieniu.

Zaimplementuj Interfejs **IAuthenticate** dla każdej platformy obsługiwanej przez aplikację.

1. W programie Visual Studio lub Xamarin Studio Otwórz App.cs z projektu z **przenośną** nazwą, która jest projektem biblioteki klas przenośnych, a następnie Dodaj następującą instrukcję `using`:

        using System.Threading.Tasks;
2. W App.cs, Dodaj następującą definicję interfejsu `IAuthenticate` bezpośrednio przed definicją klasy `App`.

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }
3. Aby zainicjować interfejs z implementacją specyficzną dla platformy, Dodaj następujące statyczne elementy członkowskie do klasy **App** .

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }
4. Otwórz TodoList. XAML z projektu biblioteki klas przenośnych, Dodaj następujący element **Button** w elemencie układu *buttonsPanel* , po istniejącym przycisku:

          <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30"
            Clicked="loginButton_Clicked"/>

    Ten przycisk wyzwala uwierzytelnianie zarządzane przez serwer przy użyciu zaplecza aplikacji mobilnej.
5. Otwórz TodoList.xaml.cs z projektu biblioteki klas przenośnych, a następnie Dodaj następujące pole do klasy `TodoList`:

        // Track whether the user has authenticated.
        bool authenticated = false;
6. Zastąp metodę **Onwyświetlaną** następującym kodem:

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

    Ten kod gwarantuje, że dane są odświeżane tylko z usługi po uwierzytelnieniu.
7. Dodaj następującą procedurę obsługi dla **klikniętego** zdarzenia do klasy **todolist** :

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }
8. Zapisz zmiany i ponownie skompiluj projekt biblioteki klas przenośnych, sprawdzając, czy nie występują żadne błędy.

## <a name="add-authentication-to-the-android-app"></a>Dodawanie uwierzytelniania do aplikacji systemu Android
W tej sekcji pokazano, jak zaimplementować interfejs **IAuthenticate** w projekcie aplikacji systemu Android. Pomiń tę sekcję, jeśli nie obsługujesz urządzeń z systemem Android.

1. W programie Visual Studio lub Xamarin Studio kliknij prawym przyciskiem myszy projekt **Droid** , a następnie **Ustaw jako projekt startowy**.
2. Naciśnij klawisz F5, aby uruchomić projekt w debugerze, a następnie sprawdź, czy zgłoszono nieobsługiwany wyjątek z kodem stanu 401 (nieautoryzowany) po uruchomieniu aplikacji. Tworzony jest kod 401, ponieważ dostęp do zaplecza jest ograniczony tylko do autoryzowanych użytkowników.
3. Otwórz MainActivity.cs w projekcie systemu Android i Dodaj następujące instrukcje `using`:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Zaktualizuj klasę **MAINS** , aby zaimplementować interfejs **IAuthenticate** w następujący sposób:

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate
5. Zaktualizuj klasę **MAINS** , dodając pole **MobileServiceUser** i metodę **uwierzytelniania** , która jest wymagana przez interfejs **IAuthenticate** , w następujący sposób:

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

6. Zaktualizuj plik **pliku AndroidManifest. XML** , dodając następujący kod XML wewnątrz elementu `<application>`:

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
    Zamień `{url_scheme_of_your_app}` na schemat adresu URL.
7. Dodaj następujący kod do metody **OnCreate** klasy **MAINS** przed wywołaniem do `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    Ten kod gwarantuje, że wystawca zostanie zainicjowany przed załadowaniem aplikacji.
8. Skompiluj ponownie aplikację, uruchom ją, a następnie zaloguj się przy użyciu wybranego dostawcy uwierzytelniania i sprawdź, czy masz dostęp do danych jako uwierzytelniony użytkownik.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

**Aplikacja uległa awarii z `Java.Lang.NoSuchMethodError: No static method startActivity`**

W niektórych przypadkach konflikty w pakietach pomocy technicznej są wyświetlane jako ostrzeżenie w programie Visual Studio, ale aplikacja uległa awarii z powodu tego wyjątku w czasie wykonywania. W takim przypadku należy upewnić się, że wszystkie pakiety obsługi, do których odwołuje się w projekcie, mają tę samą wersję. [Pakiet NuGet usługi Azure Mobile Apps](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) ma zależność `Xamarin.Android.Support.CustomTabs` dla platformy Android, więc jeśli projekt używa nowszych pakietów dla pomocy technicznej, musisz bezpośrednio zainstalować ten pakiet w wymaganej wersji w celu uniknięcia konfliktów.

## <a name="add-authentication-to-the-ios-app"></a>Dodawanie uwierzytelniania do aplikacji systemu iOS
W tej sekcji pokazano, jak zaimplementować interfejs **IAuthenticate** w projekcie aplikacji systemu iOS. Pomiń tę sekcję, jeśli nie obsługujesz urządzeń z systemem iOS.

1. W programie Visual Studio lub Xamarin Studio kliknij prawym przyciskiem myszy projekt **iOS** , a następnie **Ustaw jako projekt startowy**.
2. Naciśnij klawisz F5, aby uruchomić projekt w debugerze, a następnie sprawdź, czy zgłoszono nieobsługiwany wyjątek z kodem stanu 401 (nieautoryzowany) po uruchomieniu aplikacji. Odpowiedź 401 jest generowana, ponieważ dostęp do zaplecza jest ograniczony tylko do autoryzowanych użytkowników.
3. Otwórz AppDelegate.cs w projekcie systemu iOS i Dodaj następujące instrukcje `using`:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Zaktualizuj klasę **AppDelegate** , aby zaimplementować interfejs **IAuthenticate** w następujący sposób:

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate
5. Zaktualizuj klasę **AppDelegate** , dodając pole **MobileServiceUser** i metodę **uwierzytelniania** , która jest wymagana przez interfejs **IAuthenticate** , w następujący sposób:

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
    
6. Zaktualizuj klasę **AppDelegate** , dodając Przeciążenie metody **OpenURL** w następujący sposób:

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(url);
        }
   
7. Dodaj następujący wiersz kodu do metody **FinishedLaunching** przed wywołaniem do `LoadApplication()`:

        App.Init(this);

    Ten kod gwarantuje, że wystawca zostanie zainicjowany przed załadowaniem aplikacji.

8. Otwórz info. plist i Dodaj **Typ adresu URL**. Ustaw **Identyfikator** na wybraną nazwę, **schematy adresów URL** w schemacie URL dla aplikacji i **rolę** do none.

9. Skompiluj ponownie aplikację, uruchom ją, a następnie zaloguj się przy użyciu wybranego dostawcy uwierzytelniania i sprawdź, czy masz dostęp do danych jako uwierzytelniony użytkownik.

## <a name="add-authentication-to-windows-10-including-phone-app-projects"></a>Dodawanie uwierzytelniania do systemu Windows 10 (w tym telefonów)
W tej sekcji pokazano, jak zaimplementować interfejs **IAuthenticate** w projektach aplikacji systemu Windows 10. Te same kroki dotyczą projektów platforma uniwersalna systemu Windows (platformy UWP), ale przy użyciu projektu **platformy UWP** (z zanotowanymi zmianami). Pomiń tę sekcję, jeśli nie obsługujesz urządzeń z systemem Windows.

1. W programie Visual Studio kliknij prawym przyciskiem myszy projekt **platformy UWP** , a następnie **Ustaw jako projekt startowy**.
2. Naciśnij klawisz F5, aby uruchomić projekt w debugerze, a następnie sprawdź, czy zgłoszono nieobsługiwany wyjątek z kodem stanu 401 (nieautoryzowany) po uruchomieniu aplikacji. Odpowiedź 401 dzieje się tak, ponieważ dostęp do zaplecza jest ograniczony tylko do autoryzowanych użytkowników.
3. Otwórz MainPage.xaml.cs dla projektu aplikacji systemu Windows i Dodaj następujące instrukcje `using`:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    Zastąp `<your_Portable_Class_Library_namespace>` z przestrzenią nazw dla przenośnej biblioteki klas.
4. Zaktualizuj klasę **MainPage** , aby zaimplementować interfejs **IAuthenticate** w następujący sposób:

        public sealed partial class MainPage : IAuthenticate
5. Zaktualizuj klasę **MainPage** , dodając pole **MobileServiceUser** i metodę **uwierzytelniania** , która jest wymagana przez interfejs **IAuthenticate** , w następujący sposób:

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

1. Dodaj następujący wiersz kodu w konstruktorze klasy **MainPage** przed wywołaniem do `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);

    Zastąp `<your_Portable_Class_Library_namespace>` z przestrzenią nazw dla przenośnej biblioteki klas.

3. Jeśli używasz **platformy UWP**, Dodaj następujące przesłonięcie metody **OnActivated** do klasy **App** :

       protected override void OnActivated(IActivatedEventArgs args)
       {
           base.OnActivated(args);

            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                MobileServiceClientExtensions.ResumeWithURL(TodoItemManager.DefaultManager.CurrentClient,protocolArgs.Uri);
            }
       }

3. Otwórz pakiet Package. appxmanifest i Dodaj deklarację **protokołu** . Ustaw **nazwę wyświetlaną** na wybraną nazwę i **nazwę** schematu adresu URL dla aplikacji.

4. Skompiluj ponownie aplikację, uruchom ją, a następnie zaloguj się przy użyciu wybranego dostawcy uwierzytelniania i sprawdź, czy masz dostęp do danych jako uwierzytelniony użytkownik.

## <a name="next-steps"></a>Następne kroki
Po ukończeniu tego samouczka dotyczącego uwierzytelniania podstawowego Rozważ przejście do jednego z następujących samouczków:

* [Dodawanie powiadomień wypychanych do aplikacji](app-service-mobile-xamarin-forms-get-started-push.md)

  Dowiedz się, jak dodać obsługę powiadomień wypychanych do aplikacji i skonfigurować zaplecze Aplikacji mobilnej na potrzeby wysyłania powiadomień wypychanych przy użyciu usługi Azure Notification Hubs.
* [Włączanie synchronizacji w trybie offline dla aplikacji](app-service-mobile-xamarin-forms-get-started-offline-data.md)

  Dowiedz się, jak dodać obsługę aplikacji w trybie offline przy użyciu zaplecza aplikacji mobilnej. Synchronizacja w trybie offline umożliwia użytkownikom końcowym współdziałanie z aplikacją mobilną — wyświetlanie, Dodawanie lub modyfikowanie danych — nawet w przypadku braku połączenia sieciowego.

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
