---
title: Rozpoczynanie pracy z usługą uwierzytelniania dla aplikacji mobilnych w Xamarin dla systemu Android
description: Dowiedz się, jak używać funkcji Mobile Apps, uwierzytelniać użytkowników aplikacji platformy Xamarin Android przy użyciu różnych dostawców tożsamości, obejmującej usługi AAD, Google, Facebook, Twitter i Microsoft.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: panarasi
editor: ''
ms.assetid: 570fc12b-46a9-4722-b2e0-0d1c45fb2152
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 09/24/2018
ms.author: panarasi
ms.openlocfilehash: 0a2d964d60d13f0e71de5776112a4edbe3cdcc45
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62127917"
---
# <a name="add-authentication-to-your-xamarinandroid-app"></a>Dodawanie uwierzytelniania do aplikacji platformy Xamarin.Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

W tym temacie dowiesz się, jak uwierzytelniać użytkowników aplikacji mobilnych z aplikacji klienckiej. W tym samouczku można dodać do projektu quickstart za pomocą dostawcy tożsamości, która jest obsługiwana przez usługę Azure Mobile Apps uwierzytelniania. Po trwa pomyślnym uwierzytelnieniu i autoryzacji w aplikacji mobilnej jest wyświetlana wartość Identyfikatora użytkownika.

Ten samouczek opiera się na szybki start aplikacji mobilnej. Samouczek również najpierw musisz zakończyć [tworzenie aplikacji platformy Xamarin.Android]. Jeśli nie używasz projektu serwera pobranego — szybki start, należy dodać pakiet rozszerzenia uwierzytelnianie do projektu. Aby uzyskać więcej informacji na temat pakietów rozszerzeń serwera, zobacz [pracy z zestawem SDK serwera zaplecza platformy .NET dla usługi Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register"></a>Rejestrowanie aplikacji do uwierzytelniania i konfigurowanie usług aplikacji
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Dodawanie aplikacji do adresów URL dozwolonych zewnętrznego przekierowania

Bezpieczne uwierzytelnianie wymaga, zdefiniuj nowy schemat adresu URL dla aplikacji. Dzięki temu system uwierzytelniania przekierować z powrotem do aplikacji po zakończeniu procesu uwierzytelniania. W tym samouczku używamy schemat adresu URL _appname_ w całym. Można jednak użyć dowolnego wybranego schematu URL. Powinien on być unikatowy dla twojej aplikacji mobilnej. Aby włączyć przekierowywanie po stronie serwera:

1. W [witrynie Azure portal] wybierz usługi App Service.

2. Kliknij przycisk **uwierzytelniania / autoryzacji** opcji menu.

3. W **dozwolone zewnętrzne adresy URL przekierowania**, wprowadź `url_scheme_of_your_app://easyauth.callback`.  **Url_scheme_of_your_app** w tym ciągu jest schemat adresu URL aplikacji mobilnej.  Powinien on być zgodny z normalnej Specyfikacja adresu URL dla protokołu (litery użytkowania i tylko cyfry oraz rozpoczynać się literą).  Należy zapamiętać, ciąg, który wybierzesz, ponieważ trzeba będzie dostosować kod aplikacji mobilnych za pomocą schemat adresu URL w kilku miejscach.

4. Kliknij przycisk **OK**.

5. Kliknij pozycję **Zapisz**.

## <a name="permissions"></a>Ogranicz uprawnienia do uwierzytelnionych użytkowników
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

W programie Visual Studio lub Xamarin Studio należy uruchomić projekt klienta na urządzenia lub emulatora. Upewnij się, że wystąpił nieobsługiwany wyjątek, kod stanu 401 (bez autoryzacji) jest wywoływane po uruchomieniu aplikacji. Dzieje się tak, ponieważ aplikacja próbuje dostęp do zaplecza aplikacji mobilnej jako nieuwierzytelniony użytkownik. *TodoItem* tabeli teraz wymaga uwierzytelnienia.

Następnie zmodyfikujemy aplikację kliencką do zasobów na żądanie z zaplecza aplikacji mobilnej z uwierzytelnionego użytkownika.

## <a name="add-authentication"></a>Dodawanie uwierzytelniania do aplikacji
Gdy aplikacja jest zaktualizowana, aby wymagać od użytkowników mogą wybrać **Zaloguj** przycisk i Uwierzytelnij się przed wyświetleniem danych.

1. Dodaj następujący kod do **TodoActivity** klasy:
   
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
   
    Spowoduje to utworzenie nowej metody na potrzeby uwierzytelniania użytkownika i obsługę metody nowej **Zaloguj** przycisku. Użytkownik w powyższym przykładowym kodzie jest uwierzytelniane za pomocą logowania do usługi Facebook. Okno dialogowe służy do wyświetlenia Identyfikatora użytkownika, po uwierzytelnieniu.
   
   > [!NOTE]
   > Jeśli używasz dostawcy tożsamości innych niż usługi Facebook, zmień wartość przekazana do **LoginAsync** powyżej do jednej z następujących czynności: *MicrosoftAccount*, *Twitter*, *Google*, lub *WindowsAzureActiveDirectory*.
   > 
   > 
2. W **OnCreate** metody, usunąć lub komentarz następujący wiersz kodu:
   
        OnRefreshItemsSelected ();
3. W pliku Activity_To_Do.axml, Dodaj następujący kod *odwoływały* przycisk definicji przed istniejące *AddItem* przycisku:
   
          <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />
4. Dodaj następujący element do pliku zasobów Strings.xml:
   
        <string name="login_button_text">Sign in</string>
5. Otwórz plik AndroidManifest.xml, Dodaj następujący kod wewnątrz `<application>` — element XML:

        <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
          <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
          </intent-filter>
        </activity>

6. W programie Visual Studio lub Xamarin Studio uruchomić projekt klienta na urządzeniu lub w emulatorze, a następnie zaloguj się przy użyciu dostawcy tożsamości wybrany. Gdy jesteś zalogowanym pomyślnie swój identyfikator logowania i listy zadań do wykonania w aplikacji zostaną wyświetlone, a wprowadzasz aktualizacje danych.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

**Aplikacja uległa awarii, za pomocą `Java.Lang.NoSuchMethodError: No static method startActivity`**

W niektórych przypadkach powoduje konflikt w pakietach pomocy technicznej wyświetlany jako tylko ostrzeżeniem w Visual studio, ale awarii aplikacji za pomocą tego wyjątku w czasie wykonywania. W takim przypadku należy się upewnić, że wszystkie pakiety obsługi, określane w projekcie mają tę samą wersję. [Pakiet NuGet usługi Azure Mobile Apps](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) ma zależność `Xamarin.Android.Support.CustomTabs` dla platformy Android, więc jeśli projekt używa nowszych pakietów dla pomocy technicznej, musisz bezpośrednio zainstalować ten pakiet w wymaganej wersji w celu uniknięcia konfliktów.

<!-- URLs. -->
[Tworzenie aplikacji platformy Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
