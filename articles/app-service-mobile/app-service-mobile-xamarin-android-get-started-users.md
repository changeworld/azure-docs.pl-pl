---
title: Wprowadzenie do uwierzytelniania w systemie Xamarin Android
description: Dowiedz się, jak używać aplikacji mobilnych do uwierzytelniania użytkowników aplikacji Xamarin na Androida za pomocą dostawców tożsamości, takich jak AAD, Google, Facebook, Twitter i Microsoft.
ms.assetid: 570fc12b-46a9-4722-b2e0-0d1c45fb2152
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: fa70b7419e1877ab2daba49ad154cdfd5a8d2cba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458957"
---
# <a name="add-authentication-to-your-xamarinandroid-app"></a>Dodawanie uwierzytelniania do aplikacji Xamarin.Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Omówienie
W tym temacie pokazano, jak uwierzytelnić użytkowników aplikacji mobilnej z aplikacji klienckiej. W tym samouczku można dodać uwierzytelnianie do projektu szybkiego startu przy użyciu dostawcy tożsamości, który jest obsługiwany przez usługi Azure Mobile Apps. Po pomyślnym uwierzytelnieniu i autoryzacji w aplikacji mobilnej wyświetlana jest wartość identyfikatora użytkownika.

Ten samouczek jest oparty na przewodniku szybki start aplikacji mobilnej. Należy również najpierw ukończyć samouczek [Tworzenie aplikacji Xamarin.Android]. Jeśli nie używasz pobranego projektu serwera szybkiego startu, należy dodać pakiet rozszerzenia uwierzytelniania do projektu. Aby uzyskać więcej informacji na temat pakietów rozszerzeń serwera, zobacz [Praca z zestawem SDK serwera wewnętrznej bazy danych .NET dla aplikacji azure mobile apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register-your-app-for-authentication-and-configure-app-services"></a><a name="register"></a>Rejestrowanie aplikacji w celu uwierzytelniania i konfigurowanie usług App Services
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a><a name="redirecturl"></a>Dodawanie aplikacji do adresów URL dozwolonego przekierowania zewnętrznego

Bezpieczne uwierzytelnianie wymaga zdefiniowania nowego schematu adresu URL dla aplikacji. Dzięki temu system uwierzytelniania przekierować z powrotem do aplikacji po zakończeniu procesu uwierzytelniania. W tym samouczku używamy _appname_ schematu adresu URL w całym. Możesz jednak użyć dowolnego schematu adresu URL, który wybierzesz. Powinien być unikalny dla twojej aplikacji mobilnej. Aby włączyć przekierowanie po stronie serwera:

1. W [Witrynie Azure portal] wybierz usługę app service.

2. Kliknij opcję menu **Uwierzytelnianie / Autoryzacja.**

3. W **adresach URL dozwolonego przekierowania zewnętrznego**wprowadź . `url_scheme_of_your_app://easyauth.callback`  **url_scheme_of_your_app** w tym ciągu jest schemat adresu URL dla aplikacji mobilnej.  Powinien być zgodny ze specyfikacją normalnego adresu URL dla protokołu (użyj tylko liter i cyfr i zacznij od litery).  Należy zanotować ciąg, który wybierzesz, ponieważ trzeba będzie dostosować kod aplikacji mobilnej za pomocą schematu adresu URL w kilku miejscach.

4. Kliknij przycisk **OK**.

5. Kliknij przycisk **Zapisz**.

## <a name="restrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Ograniczanie uprawnień do uwierzytelnionych użytkowników
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

W programie Visual Studio lub Xamarin Studio uruchom projekt klienta na urządzeniu lub emulatorze. Sprawdź, czy nieobsługiowany wyjątek o kodzie stanu 401 (Nieautoryzowane) jest wywoływany po uruchomieniu aplikacji. Dzieje się tak, ponieważ aplikacja próbuje uzyskać dostęp do wewnętrznej bazy danych aplikacji mobilnej jako nieuwierzyfowany użytkownik. Tabela *TodoItem* wymaga teraz uwierzytelnienia.

Następnie zaktualizujesz aplikację kliencką, aby zażądać zasobów z zaplecza aplikacji mobilnej z uwierzytelnionym użytkownikiem.

## <a name="add-authentication-to-the-app"></a><a name="add-authentication"></a>Dodawanie uwierzytelniania do aplikacji
Aplikacja jest aktualizowana, aby wymagać od użytkowników naciśnięcia przycisku **Zaloguj się** i uwierzytelnienia przed wyświetleniem danych.

1. Dodaj następujący kod do klasy **TodoActivity:**
   
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
   
    Spowoduje to utworzenie nowej metody uwierzytelniania użytkownika i obsługi metody dla nowego przycisku **Logowania.** Użytkownik w powyższym przykładzie kodu jest uwierzytelniony przy użyciu logowania na Facebooku. Okno dialogowe służy do wyświetlania identyfikatora użytkownika po uwierzytelnieniu.
   
   > [!NOTE]
   > Jeśli korzystasz z dostawcy tożsamości innego niż Facebook, zmień wartość przekazywana do **LoginAsync** powyżej na jedną z następujących czynności: *MicrosoftAccount*, *Twitter*, *Google*lub *WindowsAzureActiveDirectory*.
   > 
   > 
2. W **OnCreate** metody, usuń lub komentarz-out następujący wiersz kodu:
   
        OnRefreshItemsSelected ();
3. W pliku Activity_To_Do.axml dodaj następującą definicję przycisku *LoginUser* przed istniejącym *przyciskiem AddItem:*
   
          <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />
4. Dodaj następujący element do pliku zasobów Strings.xml:
   
        <string name="login_button_text">Sign in</string>
5. Otwórz plik AndroidManifest.xml, dodaj następujący `<application>` kod wewnątrz elementu XML:

        <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
          <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
          </intent-filter>
        </activity>

6. W programie Visual Studio lub Xamarin Studio uruchom projekt klienta na urządzeniu lub emulatorze i zaloguj się przy wybranym dostawcy tożsamości. Po pomyślnym zalogowaniu aplikacja wyświetli twój identyfikator logowania i listę elementów zadań do wykonania, a także będzie można dokonywać aktualizacji danych.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

**Aplikacja uległa awarii z`Java.Lang.NoSuchMethodError: No static method startActivity`**

W niektórych przypadkach konflikty w pakietach pomocy technicznej wyświetlane jako tylko ostrzeżenie w programie Visual Studio, ale aplikacja ulega awarii z tym wyjątkiem w czasie wykonywania. W takim przypadku należy upewnić się, że wszystkie pakiety pomocy technicznej, do których odwołuje się w projekcie mają tę samą wersję. [Pakiet NuGet usługi Azure Mobile Apps](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) ma zależność `Xamarin.Android.Support.CustomTabs` dla platformy Android, więc jeśli projekt używa nowszych pakietów dla pomocy technicznej, musisz bezpośrednio zainstalować ten pakiet w wymaganej wersji w celu uniknięcia konfliktów.

<!-- URLs. -->
[Tworzenie aplikacji platformy Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
