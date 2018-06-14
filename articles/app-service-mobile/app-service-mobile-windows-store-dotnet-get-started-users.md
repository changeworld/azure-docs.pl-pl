---
title: Dodawanie uwierzytelniania do aplikacji systemu Windows platformy Uniwersalnej | Dokumentacja firmy Microsoft
description: 'Dowiedz się, jak używać usługi Azure App Service Mobile Apps uwierzytelniać użytkowników aplikacji systemu Windows platformy Uniwersalnej przy użyciu różnych dostawców tożsamości, obejmującej: usługi AAD, Google, Facebook, Twitter i firmy Microsoft.'
services: app-service\mobile
documentationcenter: windows
author: conceptdev
manager: panarasi
editor: ''
ms.assetid: 6cffd951-893e-4ce5-97ac-86e3f5ad9466
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 07/05/2017
ms.author: panarasi
ms.openlocfilehash: 4cc597f8aca13445034c8a1691b41018d4d9bc4b
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2018
ms.locfileid: "27592148"
---
# <a name="add-authentication-to-your-windows-app"></a>Dodawanie uwierzytelniania do aplikacji systemu Windows
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

W tym temacie przedstawiono sposób dodawania uwierzytelniania opartego na chmurze do aplikacji mobilnej. W tym samouczku dodanie authentication projektu Szybki Start systemu Windows platformy Uniwersalnej aplikacji mobilnej przy użyciu dostawcy tożsamości, która jest obsługiwana przez usługę Azure App Service. Po pomyślnym są uwierzytelnieni i upoważnieni przez zaplecza aplikacji mobilnej, wyświetlana jest wartość Identyfikatora użytkownika.

W tym samouczku opiera się na szybki start Mobile Apps. Najpierw musisz zakończyć samouczka [Rozpoczynanie pracy z usługą Mobile Apps](app-service-mobile-windows-store-dotnet-get-started.md).

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

Teraz można sprawdzić dostępu anonimowego z wewnętrzną bazą danych zostało wyłączone. W projekcie aplikacji platformy uniwersalnej systemu Windows Ustaw jako projekt rozruchu, wdrażanie i uruchamianie aplikacji; Upewnij się, że wystąpił nieobsługiwany wyjątek przy użyciu kodu stanu 401 (bez autoryzacji) jest wywoływane po uruchomieniu aplikacji. Dzieje się tak, ponieważ aplikacja próbuje uzyskać dostęp kodu aplikacji mobilnej jako nieuwierzytelniony użytkownik, ale *TodoItem* tabeli teraz wymaga uwierzytelniania.

Następnie spowoduje zaktualizowanie aplikacji do uwierzytelniania użytkowników przed wysłaniem żądania zasobów z usługi aplikacji.

## <a name="add-authentication"></a>Dodawanie uwierzytelniania do aplikacji
1. W przypadku platformy uniwersalnej systemu Windows aplikacji w pliku MainPage.xaml.cs projektu i Dodaj poniższy fragment kodu:
   
        // Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;
   
        // Define a method that performs the authentication process
        // using a Facebook sign-in. 
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
            try
            {
                // Change 'MobileService' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                message =
                    string.Format("You are now signed in - {0}", user.UserId);
   
                success = true;
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            return success;
        }
   
    Ten kod uwierzytelnia użytkownika za pomocą nazwy logowania usługi Facebook. Jeśli używasz dostawcy tożsamości innego niż usługi Facebook, zmień wartość **MobileServiceAuthenticationProvider** powyżej wartości dla dostawcy.
2. Zastąp **OnNavigatedTo()** metody w MainPage.xaml.cs. Następnie należy dodać **Zaloguj** przycisku aplikacja, która wyzwala uwierzytelniania.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            if (e.Parameter is Uri)
            {
                App.MobileService.ResumeWithURL(e.Parameter as Uri);
            }
        }

3. Dodaj poniższy fragment kodu w pliku MainPage.xaml.cs:
   
        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Switch the buttons and load items from the mobile app.
                ButtonLogin.Visibility = Visibility.Collapsed;
                ButtonSave.Visibility = Visibility.Visible;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
4. Otwórz plik projektu MainPage.xaml, zlokalizuj element, który definiuje **zapisać** przycisk i zastąp go następującym kodem:
   
        <Button Name="ButtonSave" Visibility="Collapsed" Margin="0,8,8,0" 
                Click="ButtonSave_Click">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Add"/>
                <TextBlock Margin="5">Save</TextBlock>
            </StackPanel>
        </Button>
        <Button Name="ButtonLogin" Visibility="Visible" Margin="0,8,8,0" 
                Click="ButtonLogin_Click" TabIndex="0">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Permissions"/>
                <TextBlock Margin="5">Sign in</TextBlock> 
            </StackPanel>
        </Button>
5. Dodaj poniższy fragment kodu w pliku App.xaml.cs:

        protected override void OnActivated(IActivatedEventArgs args)
        {
            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                Frame content = Window.Current.Content as Frame;
                if (content.Content.GetType() == typeof(MainPage))
                {
                    content.Navigate(typeof(MainPage), protocolArgs.Uri);
                }
            }
            Window.Current.Activate();
            base.OnActivated(args);
        }
6. Otwórz plik Package.appxmanifest, przejdź do **deklaracje**w **dostępne deklaracje** listy rozwijanej wybierz **protokołu** i kliknij przycisk **Dodaj** przycisku. Teraz skonfigurować **właściwości** z **protokołu** deklaracji. W **Nazwa wyświetlana**, Dodaj nazwę, które mają zostać wyświetlone dla użytkowników aplikacji. W **nazwa**, Dodaj użytkownika {url_scheme_of_your_app}.
7. Naciśnij klawisz F5, aby uruchomić aplikację, kliknij przycisk **Zaloguj** przycisk i logowania do aplikacji za pomocą dostawcy tożsamości wybrany. Po logowanie zakończy się pomyślnie, aplikacja jest uruchamiana bez błędów, i są w stanie zapytania z wewnętrzną bazą danych i aktualizowanie danych.

## <a name="tokens"></a>Magazyn token uwierzytelniania na kliencie
W poprzednim przykładzie pokazano standardowego logowania, która wymaga od klienta do kontaktowania się z zarówno dostawcy tożsamości i usługi App Service każdym uruchomieniu aplikacji. Nie tylko jest nieefektywne, można uruchomić tej metody do użycia — odnosi się problemów należy wielu klientów próbuje uruchomić aplikację w tym samym czasie. Lepszym rozwiązaniem jest buforowanie token autoryzacji zwrócony przez usługę aplikacji, spróbuj użyć pierwsze przed użyciem opartej na dostawcy logowania.

> [!NOTE]
> Może buforować token wystawiony przez usługi aplikacji niezależnie od tego, czy używasz zarządzanych przez klienta lub zarządzane przez usługę uwierzytelniania. W tym samouczku korzysta z zarządzanymi przez usługę uwierzytelniania.
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Kolejne kroki
Teraz, aby ukończyć w tym samouczku uwierzytelnianie podstawowe, należy wziąć pod uwagę kontynuowanie na jedno z następujących samouczków:

* [Dodawanie powiadomień wypychanych do aplikacji](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Dowiedz się, jak dodać obsługę powiadomień wypychanych do aplikacji i skonfigurować zaplecze Aplikacji mobilnej na potrzeby wysyłania powiadomień wypychanych przy użyciu usługi Azure Notification Hubs.
* [Włączanie synchronizacji w trybie offline dla aplikacji](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Dowiedz się, jak dodać obsługę aplikacji w trybie offline przy użyciu zaplecza Aplikacji mobilnej. Synchronizacja w trybie offline umożliwia użytkownikom końcowym interakcję z aplikacją mobilną &mdash; przeglądanie, dodawanie lub modyfikowanie danych &mdash; nawet w przypadku braku połączenia sieciowego.

<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
