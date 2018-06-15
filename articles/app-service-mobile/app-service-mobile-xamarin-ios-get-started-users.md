---
title: Wprowadzenie do uwierzytelniania dla aplikacji mobilnych w systemu Xamarin iOS
description: Dowiedz się, jak używać Mobile Apps do uwierzytelniania użytkowników aplikacji platformy Xamarin iOS za pomocą różnych dostawców tożsamości, obejmującej AAD, Google, Facebook, Twitter i Microsoft.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 180cc61b-19c5-48bf-a16c-7181aef3eacc
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 07/05/2017
ms.author: crdun
ms.openlocfilehash: c2b77d7c9caab1c7ac13af5ccd50e992bcce1caf
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31787662"
---
# <a name="add-authentication-to-your-xamarinios-app"></a>Dodawanie uwierzytelniania do aplikacji platformy Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

W tym temacie przedstawiono, jak uwierzytelniać użytkowników aplikacji usługi Mobile aplikacji z poziomu aplikacji klienta. W tym samouczku Dodawanie uwierzytelniania do projektu szybkiego startu platformy Xamarin.iOS przy użyciu dostawcy tożsamości, która jest obsługiwana przez usługę App Service. Po pomyślnie trwa uwierzytelnieniu i autoryzacji w aplikacji mobilnej, jest wyświetlana wartość Identyfikatora użytkownika i będzie można uzyskać dostęp do danych tabeli ograniczone.

Najpierw musisz zakończyć samouczka [tworzenie aplikacji platformy Xamarin.iOS]. Jeśli nie używasz szybki start pobrany Projekt serwera, należy dodać pakietu rozszerzenia uwierzytelniania do projektu. Aby uzyskać więcej informacji na temat pakietów rozszerzenia serwera, zobacz [pracować z serwera wewnętrznej bazy danych .NET SDK usługi Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Zarejestrować aplikację do uwierzytelniania i konfigurowanie usługi aplikacji
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a>Dodaj aplikację do dozwolonych przekierowania zewnętrzne adresy URL

Bezpieczne uwierzytelnianie wymaga Zdefiniuj nowy schemat adresu URL dla aplikacji. Dzięki temu system uwierzytelniania przekierować z powrotem do aplikacji po zakończeniu procesu uwierzytelniania. W tym samouczku używamy schemat adresu URL _appname_ w ciągu. Można jednak użyć dowolnego wybranego schematu URL. Powinien być unikatowy w aplikacji mobilnej. Aby włączyć przekierowywanie po stronie serwera:

1. W [portalu Azure] wybierz usługę aplikacji.

2. Kliknij przycisk **uwierzytelniania / autoryzacji** opcji menu.

3. W **dozwolone zewnętrznych adresów URL przekierowań**, wprowadź `url_scheme_of_your_app://easyauth.callback`.  **Url_scheme_of_your_app** w tym ciągu jest schemat adresu URL dla aplikacji mobilnej.  Musi on być zgodny normalne Specyfikacja adresu URL dla protokołu (Użyj litery i tylko cyfry i rozpoczyna się od litery).  Należy zanotuj ciąg, który wybrany jako trzeba będzie dostosować kodu aplikacji mobilnej przy użyciu schemat adresu URL w kilku miejscach.

4. Kliknij przycisk **OK**.

5. Kliknij pozycję **Zapisz**.

## <a name="restrict-permissions-to-authenticated-users"></a>Ogranicz uprawnienia dla uwierzytelnionych użytkowników
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;4. W programie Visual Studio lub Xamarin Studio uruchamianie projektu klienta na urządzenia lub emulatora. Upewnij się, że wystąpił nieobsługiwany wyjątek przy użyciu kodu stanu 401 (bez autoryzacji) jest wywoływane po uruchomieniu aplikacji. Niepowodzenie jest rejestrowane w konsoli debugera. Dlatego w programie Visual Studio powinna zostać wyświetlona awarii w oknie danych wyjściowych.

&nbsp;&nbsp;Tego błędu nieautoryzowanego odbywa się, ponieważ aplikacja próbuje uzyskać dostęp jako użytkownik nieuwierzytelniony zaplecza aplikacji mobilnej. *TodoItem* tabeli teraz wymaga uwierzytelniania.

Następnie możesz zaktualizuje aplikacji klienckiej żądania zasobów z zaplecza aplikacji mobilnej z uwierzytelnionego użytkownika.

## <a name="add-authentication-to-the-app"></a>Dodawanie uwierzytelniania do aplikacji
W tej sekcji należy zmodyfikować aplikację, aby wyświetlić ekran logowania przed wyświetleniem danych. Po uruchomieniu aplikacji, nie nie łączy się z usługą aplikacji i nie zawiera żadnych danych. Po raz pierwszy użytkownik wykonuje gestu odświeżania, pojawi się ekran logowania. Po pomyślnym logowaniu pojawi się lista zadań do wykonania.

1. W projekcie klienta, otwórz plik **QSTodoService.cs** i dodaj następującą instrukcję using i `MobileServiceUser` z metody dostępu do klasy QSTodoService:
 
        using UIKit;
       
        // Logged in user
        private MobileServiceUser user;
        public MobileServiceUser User { get { return user; } }
2. Dodaj nową metodę o nazwie **Uwierzytelnij** do **QSTodoService** z następującej definicji:

        public async Task Authenticate(UIViewController view)
        {
            try
            {
                AppDelegate.ResumeWithURL = url => url.Scheme == "zumoe2etestapp" && client.ResumeWithURL(url);
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    >[AZURE.NOTE] Jeśli używasz dostawcy tożsamości innych niż Facebook, zmień wartość przekazana do **LoginAsync** powyżej do jednej z następujących czynności: _MicrosoftAccount_, _Twitter_, _Google_, lub _WindowsAzureActiveDirectory_.

3. Otwórz **QSTodoListViewController.cs**. Zmodyfikować definicję metody **ViewDidLoad** usuwanie wywołanie **RefreshAsync()** w pobliżu elementu end:
   
        public override async void ViewDidLoad ()
        {
            base.ViewDidLoad ();
   
            todoService = QSTodoService.DefaultService;
            await todoService.InitializeStoreAsync();
   
            RefreshControl.ValueChanged += async (sender, e) => {
                await RefreshAsync();
            }
   
            // Comment out the call to RefreshAsync
            // await RefreshAsync();
        }
4. Zmodyfikuj metodę **RefreshAsync** uwierzytelniać **użytkownika** właściwość ma wartość null. Dodaj następujący kod w górnej części definicja metody:
   
        // start of RefreshAsync method
        if (todoService.User == null) {
            await QSTodoService.DefaultService.Authenticate(this);
            if (todoService.User == null) {
                Console.WriteLine("couldn't login!!");
                return;
            }
        }
        // rest of RefreshAsync method
5. Otwórz **AppDelegate.cs**, dodaj następującą metodę:

        public static Func<NSUrl, bool> ResumeWithURL;

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return ResumeWithURL != null && ResumeWithURL(url);
        }
6. Otwórz **Info.plist** pliku, przejdź do **typy adresu URL** w **zaawansowane** sekcji. Teraz skonfigurować **identyfikator** i **Schematy adresów URL** typ adresu URL i kliknij przycisk **Dodawanie adresu URL typu**. **Schematy adresów URL** powinna być taka sama jak Twoje {url_scheme_of_your_app}.
7. W programie Visual Studio, podłączone do hostów Mac lub Visual Studio dla komputerów Mac uruchamianie projektu klienta przeznaczonych dla określonego urządzenia lub emulatora. Sprawdź, czy aplikacja nie wyświetla żadnych danych.
   
    Wykonuje gestu odświeżania ściąganie w dół listę elementów, co spowoduje wyświetlenie ekranu logowania. Po pomyślnie zostały wprowadzone prawidłowe poświadczenia, aplikacja będzie wyświetlana lista zadań do wykonania i mogą wysyłać aktualizacje do danych.

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[tworzenie aplikacji platformy Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md
