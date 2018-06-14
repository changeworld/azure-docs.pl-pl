---
title: Wprowadzenie do uwierzytelniania dla aplikacji mobilnych w dla systemu Xamarin Android
description: Dowiedz się, jak korzystać z aplikacji mobilnej uwierzytelniać użytkowników aplikacji platformy Xamarin Android za pomocą różnych dostawców tożsamości, obejmującej AAD, Google, Facebook, Twitter i Microsoft.
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
ms.date: 07/05/2017
ms.author: panarasi
ms.openlocfilehash: 97207b722b65ccf98c57304cd559b0927aacd5a4
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2018
ms.locfileid: "27595299"
---
# <a name="add-authentication-to-your-xamarinandroid-app"></a>Dodawanie uwierzytelniania do aplikacji platformy Xamarin.Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

W tym temacie przedstawiono, jak uwierzytelniać użytkowników aplikacji mobilnych z aplikacji klienta. W tym samouczku należy dodać do projektu szybkiego startu przy użyciu dostawcy tożsamości, który jest obsługiwany przez usługi Azure Mobile Apps uwierzytelniania. Po pomyślnie trwa uwierzytelnieniu i autoryzacji w aplikacji mobilnej, jest wyświetlana wartość Identyfikatora użytkownika.

W tym samouczku jest oparta na aplikacja mobilna — Szybki Start. Należy również najpierw Ukończ samouczek [tworzenie aplikacji platformy Xamarin.Android]. Jeśli nie używasz szybki start pobrany Projekt serwera, należy dodać pakietu rozszerzenia uwierzytelniania do projektu. Aby uzyskać więcej informacji na temat pakietów rozszerzenia serwera, zobacz [pracować z serwera wewnętrznej bazy danych .NET SDK usługi Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register"></a>Zarejestrować aplikację do uwierzytelniania i konfigurowanie usługi aplikacji
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Dodaj aplikację do dozwolonych przekierowania zewnętrzne adresy URL

Bezpieczne uwierzytelnianie wymaga Zdefiniuj nowy schemat adresu URL dla aplikacji. Dzięki temu system uwierzytelniania przekierować z powrotem do aplikacji po zakończeniu procesu uwierzytelniania. W tym samouczku używamy schemat adresu URL _appname_ w ciągu. Można jednak użyć dowolnego wybranego schematu URL. Powinien być unikatowy w aplikacji mobilnej. Aby włączyć przekierowywanie po stronie serwera:

1. W [portalu Azure] wybierz usługę aplikacji.

2. Kliknij przycisk **uwierzytelniania / autoryzacji** opcji menu.

3. W **dozwolone zewnętrznych adresów URL przekierowań**, wprowadź `url_scheme_of_your_app://easyauth.callback`.  **Url_scheme_of_your_app** w tym ciągu jest schemat adresu URL dla aplikacji mobilnej.  Musi on być zgodny normalne Specyfikacja adresu URL dla protokołu (Użyj litery i tylko cyfry i rozpoczyna się od litery).  Należy zanotuj ciąg, który wybrany jako trzeba będzie dostosować kodu aplikacji mobilnej przy użyciu schemat adresu URL w kilku miejscach.

4. Kliknij przycisk **OK**.

5. Kliknij pozycję **Zapisz**.

## <a name="permissions"></a>Ogranicz uprawnienia dla uwierzytelnionych użytkowników
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

W programie Visual Studio lub Xamarin Studio uruchamianie projektu klienta na urządzenia lub emulatora. Upewnij się, że wystąpił nieobsługiwany wyjątek przy użyciu kodu stanu 401 (bez autoryzacji) jest wywoływane po uruchomieniu aplikacji. Dzieje się tak, ponieważ aplikacja próbuje uzyskać dostęp jako użytkownik nieuwierzytelniony zaplecza aplikacji mobilnej. *TodoItem* tabeli teraz wymaga uwierzytelniania.

Następnie możesz zaktualizuje aplikacji klienckiej żądania zasobów z zaplecza aplikacji mobilnej z uwierzytelnionego użytkownika.

## <a name="add-authentication"></a>Dodawanie uwierzytelniania do aplikacji
Gdy aplikacja jest zaktualizowana, aby wymagać od użytkowników wybierz **Zaloguj** przycisk i uwierzytelniania przed wyświetleniem danych.

1. Dodaj następujący kod do **TodoActivity** klasy:
   
        // Define a authenticated user.
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
   
    Spowoduje to utworzenie nowej metody do uwierzytelniania użytkownika i metody obsługi nowej **Zaloguj** przycisku. W powyższym przykładowym kodzie użytkownika jest uwierzytelniany przy użyciu identyfikatora logowania usługi Facebook. Okno dialogowe służy do wyświetlania raz uwierzytelnić użytkownika.
   
   > [!NOTE]
   > Jeśli używasz dostawcy tożsamości innego niż usługi Facebook, zmień wartość przekazana do **LoginAsync** powyżej do jednej z następujących czynności: *MicrosoftAccount*, *Twitter*, *Google*, lub *WindowsAzureActiveDirectory*.
   > 
   > 
2. W **OnCreate** metody, usunąć lub komentarz następującego kodu:
   
        OnRefreshItemsSelected ();
3. W pliku Activity_To_Do.axml, Dodaj następujący *odwoływały* przycisk definicji przed istniejące *AddItem* przycisk:
   
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

6. W programie Visual Studio lub Xamarin Studio uruchamianie projektu klienta na urządzeniu lub emulatorze i zaloguj się przy użyciu dostawcy tożsamości wybrany. Pomyślnie zalogowany, aplikacja wyświetli Identyfikatora logowania i Lista zadań do wykonania, i umożliwia aktualizacji danych.

<!-- URLs. -->
[tworzenie aplikacji platformy Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md