---
title: Wprowadzenie do uwierzytelniania w programie Xamarin Android
description: Dowiedz się, jak używać Mobile Apps do uwierzytelniania użytkowników aplikacji platformy Xamarin dla systemu Android za pomocą dostawców tożsamości, takich jak AAD, Google, Facebook, Twitter i Microsoft.
ms.assetid: 570fc12b-46a9-4722-b2e0-0d1c45fb2152
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: fa70b7419e1877ab2daba49ad154cdfd5a8d2cba
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77458957"
---
# <a name="add-authentication-to-your-xamarinandroid-app"></a>Dodawanie uwierzytelniania do aplikacji platformy Xamarin. Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Omówienie
W tym temacie pokazano, jak uwierzytelniać użytkowników aplikacji mobilnej z poziomu aplikacji klienckiej. W tym samouczku dodasz uwierzytelnianie do projektu szybkiego startu przy użyciu dostawcy tożsamości obsługiwanego przez usługę Azure Mobile Apps. Po pomyślnym uwierzytelnieniu i autoryzacji w aplikacji mobilnej zostanie wyświetlona wartość identyfikatora użytkownika.

Ten samouczek jest oparty na przewodniku szybki start aplikacji mobilnej. Należy również najpierw ukończyć samouczek [Tworzenie aplikacji platformy Xamarin. Android]. Jeśli nie używasz pobranego projektu szybkiego startu serwera, musisz dodać pakiet rozszerzenia uwierzytelniania do projektu. Aby uzyskać więcej informacji o pakietach rozszerzeń serwera, zobacz [Work with the .NET zaplecz Server SDK for Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register"></a>Zarejestruj aplikację pod kątem uwierzytelniania i skonfiguruj App Services
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Dodaj aplikację do dozwolonych zewnętrznych adresów URL przekierowania

Bezpieczne uwierzytelnianie wymaga zdefiniowania nowego schematu adresu URL dla aplikacji. Dzięki temu system uwierzytelniania może przekierować z powrotem do aplikacji po zakończeniu procesu uwierzytelniania. W tym samouczku używamy _w systemie adresu_ URL. Można jednak użyć dowolnego wybranego schematu adresów URL. Powinna być unikatowa dla aplikacji mobilnej. Aby włączyć przekierowanie po stronie serwera:

1. W [Azure Portal] Wybierz App Service.

2. Kliknij opcję menu **uwierzytelnianie/autoryzacja** .

3. W polu **dozwolone adresy URL zewnętrznych przekierowań**wprowadź `url_scheme_of_your_app://easyauth.callback`.  **Url_scheme_of_your_app** w tym ciągu jest schematem adresu URL aplikacji mobilnej.  Powinna być zgodna ze specyfikacją normalnych adresów URL dla protokołu (używaj tylko liter i cyfr i zaczynać się od litery).  Należy zanotować ciąg, który wybierzesz, ponieważ trzeba będzie dostosować kod aplikacji mobilnej przy użyciu schematu adresu URL w kilku miejscach.

4. Kliknij przycisk **OK**.

5. Kliknij przycisk **Save** (Zapisz).

## <a name="permissions"></a>Ograniczanie uprawnień do uwierzytelnionych użytkowników
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

W programie Visual Studio lub Xamarin Studio Uruchom projekt klienta na urządzeniu lub w emulatorze. Przed uruchomieniem aplikacji należy sprawdzić, czy zgłoszono nieobsługiwany wyjątek z kodem stanu 401 (bez autoryzacji). Dzieje się tak, ponieważ aplikacja próbuje uzyskać dostęp do zaplecza aplikacji mobilnej jako nieuwierzytelniony użytkownik. Tabela *TodoItem* wymaga teraz uwierzytelniania.

Następnie zaktualizujesz aplikację kliencką w celu żądania zasobów z zaplecza aplikacji mobilnej za pomocą uwierzytelnionego użytkownika.

## <a name="add-authentication"></a>Dodawanie uwierzytelniania do aplikacji
Aplikacja zostanie zaktualizowana, aby wymagać od użytkowników naciśnięcia przycisku **Zaloguj** i uwierzytelnienia przed wyświetleniem danych.

1. Dodaj następujący kod **do klasy zadania do wykonania** :
   
        // Define an authenticated user.
        private MobileServiceUser user;
        private async Task<bool> Authenticate()
        {
                var success = false;
                try
                {
                    // Sign in with Facebook login using a server-managed flow.
                    user = await client.LoginAsync(this,
                        MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    CreateAndShowDialog(string.Format("you are now logged in - {0}",
                        user.UserId), "Logged in!");
   
                    success = true;
                }
                catch (Exception ex)
                {
                    CreateAndShowDialog(ex, "Authentication failed");
                }
                return success;
        }
   
        [Java.Interop.Export()]
        public async void LoginUser(View view)
        {
            // Load data only after authentication succeeds.
            if (await Authenticate())
            {
                //Hide the button after authentication succeeds.
                FindViewById<Button>(Resource.Id.buttonLoginUser).Visibility = ViewStates.Gone;
   
                // Load the data.
                OnRefreshItemsSelected();
            }
        }
   
    Spowoduje to utworzenie nowej metody w celu uwierzytelnienia użytkownika i obsługi metody dla nowego przycisku **Zaloguj** . Użytkownik z powyższego przykładowego kodu jest uwierzytelniany przy użyciu logowania w serwisie Facebook. To okno dialogowe służy do wyświetlania identyfikatora użytkownika po uwierzytelnieniu.
   
   > [!NOTE]
   > Jeśli używasz dostawcy tożsamości innego niż Facebook, Zmień wartość przekazaną do **LoginAsync** powyżej na jedną z następujących wartości: *MicrosoftAccount*, *Twitter*, *Google*lub *WindowsAzureActiveDirectory*.
   > 
   > 
2. W metodzie **OnCreate** Usuń lub Skomentuj następujący wiersz kodu:
   
        OnRefreshItemsSelected ();
3. W pliku Activity_To_Do. axml Dodaj następującą definicję przycisku *LoginUser* przed istniejącym przyciskiem *AddItem* :
   
          <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />
4. Dodaj następujący element do pliku zasobów String. XML:
   
        <string name="login_button_text">Sign in</string>
5. Otwórz plik pliku AndroidManifest. XML, Dodaj następujący kod wewnątrz elementu `<application>` XML:

        <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
          <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
          </intent-filter>
        </activity>

6. W programie Visual Studio lub Xamarin Studio Uruchom projekt klienta na urządzeniu lub w emulatorze i zaloguj się przy użyciu wybranego dostawcy tożsamości. Po pomyślnym zalogowaniu aplikacja wyświetli identyfikator logowania i listę elementów do wykonania, a następnie będzie można wprowadzać aktualizacje danych.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

**Aplikacja uległa awarii z `Java.Lang.NoSuchMethodError: No static method startActivity`**

W niektórych przypadkach konflikty w pakietach pomocy technicznej są wyświetlane jako ostrzeżenie w programie Visual Studio, ale aplikacja uległa awarii z powodu tego wyjątku w czasie wykonywania. W takim przypadku należy upewnić się, że wszystkie pakiety obsługi, do których odwołuje się w projekcie, mają tę samą wersję. [Pakiet NuGet usługi Azure Mobile Apps](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) ma zależność `Xamarin.Android.Support.CustomTabs` dla platformy Android, więc jeśli projekt używa nowszych pakietów dla pomocy technicznej, musisz bezpośrednio zainstalować ten pakiet w wymaganej wersji w celu uniknięcia konfliktów.

<!-- URLs. -->
[Tworzenie aplikacji platformy Xamarin. Android]: app-service-mobile-xamarin-android-get-started.md
