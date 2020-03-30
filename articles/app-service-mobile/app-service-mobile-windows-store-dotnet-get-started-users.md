---
title: Dodawanie uwierzytelniania do aplikacji platformy uniwersalnej systemuśpiłnie
description: Dowiedz się, jak używać aplikacji mobilnych usługi Azure App Service do uwierzytelniania użytkowników aplikacji platformy uniwersalnej systemu Windows (UWP) u dostawców tożsamości, takich jak AAD, Google, Facebook, Twitter i Microsoft.
ms.assetid: 6cffd951-893e-4ce5-97ac-86e3f5ad9466
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 32d4313b345964a2db13d68e83f81756a4acf0d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458940"
---
# <a name="add-authentication-to-your-windows-app"></a>Dodawanie uwierzytelniania do aplikacji systemu Windows
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Omówienie
W tym temacie pokazano, jak dodać uwierzytelnianie oparte na chmurze do aplikacji mobilnej. W tym samouczku dodasz uwierzytelnianie do projektu szybkiego startu platformy systemu Windows (PLATFORMY UNIWERSALNEJ Systemu Windows) dla aplikacji mobilnych przy użyciu dostawcy tożsamości obsługiwanego przez usługę Azure App Service. Po pomyślnym uwierzytelnieniu i autoryzowaniu przez zaplecze aplikacji mobilnej wyświetlana jest wartość identyfikatora użytkownika.

Ten samouczek jest oparty na przewodniku Szybki start aplikacji mobilnych. Najpierw musisz ukończyć samouczek [Wprowadzenie do aplikacji mobilnych](app-service-mobile-windows-store-dotnet-get-started.md).

## <a name="register-your-app-for-authentication-and-configure-the-app-service"></a><a name="register"></a>Zarejestruj aplikację do uwierzytelniania i skonfiguruj usługę App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a><a name="redirecturl"></a>Dodawanie aplikacji do adresów URL dozwolonego przekierowania zewnętrznego

Bezpieczne uwierzytelnianie wymaga zdefiniowania nowego schematu adresu URL dla aplikacji. Dzięki temu system uwierzytelniania przekierować z powrotem do aplikacji po zakończeniu procesu uwierzytelniania. W tym samouczku używamy _appname_ schematu adresu URL w całym. Możesz jednak użyć dowolnego schematu adresu URL, który wybierzesz. Powinien być unikalny dla twojej aplikacji mobilnej. Aby włączyć przekierowanie po stronie serwera:

1. W [witrynie Azure portal](https://ms.portal.azure.com)wybierz usługę app service.

2. Kliknij opcję menu **Uwierzytelnianie / Autoryzacja.**

3. W **adresach URL dozwolonego przekierowania zewnętrznego**wprowadź . `url_scheme_of_your_app://easyauth.callback`  **url_scheme_of_your_app** w tym ciągu jest schemat adresu URL dla aplikacji mobilnej.  Powinien być zgodny ze specyfikacją normalnego adresu URL dla protokołu (użyj tylko liter i cyfr i zacznij od litery).  Należy zanotować ciąg, który wybierzesz, ponieważ trzeba będzie dostosować kod aplikacji mobilnej za pomocą schematu adresu URL w kilku miejscach.

4. Kliknij przycisk **Zapisz**.

## <a name="restrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Ograniczanie uprawnień do uwierzytelnionych użytkowników
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Teraz możesz sprawdzić, czy anonimowy dostęp do wewnętrznej bazy danych został wyłączony. Z projektu aplikacji platformy uniwersalnej systemu uniwersalnego ustawiony jako projekt uruchamiania, wdrożyć i uruchomić aplikację; sprawdź, czy nieobsługiwał się wyjątek o kodzie stanu 401 (Nieautoryzowane) jest wywoływany po uruchomieniu aplikacji. Dzieje się tak, ponieważ aplikacja próbuje uzyskać dostęp do kodu aplikacji mobilnej jako nieuwierzytywy użytkownik, ale tabela *TodoItem* wymaga teraz uwierzytelniania.

Następnie zaktualizujesz aplikację, aby uwierzytelnić użytkowników przed żądaniem zasobów z usługi App Service.

## <a name="add-authentication-to-the-app"></a><a name="add-authentication"></a>Dodawanie uwierzytelniania do aplikacji
1. W pliku projektu aplikacji platformy uniwersalnej systemu zużyciu platformy uniwersalnej MainPage.xaml.cs i dodaj fragment kodu:
   
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
   
    Ten kod uwierzytelnia użytkownika za pomocą loginu na Facebooku. Jeśli używasz dostawcy tożsamości innego niż Facebook, zmień wartość **MobileServiceAuthenticationProvider** powyżej na wartość dla dostawcy.
2. Wymień **metodę OnNavigatedTo()** w MainPage.xaml.cs. Następnie dodasz przycisk **Zaloguj** do aplikacji, która wyzwala uwierzytelnianie.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            if (e.Parameter is Uri)
            {
                App.MobileService.ResumeWithURL(e.Parameter as Uri);
            }
        }

3. Dodaj następujący fragment kodu do MainPage.xaml.cs:
   
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
4. Otwórz plik projektu MainPage.xaml, znajdź element definiujący przycisk **Zapisz** i zastąp go następującym kodem:
   
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
5. Dodaj następujący fragment kodu do App.xaml.cs:

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
6. Otwórz plik Package.appxmanifest, przejdź do **pozycji Deklaracje**, na liście rozwijanej **Dostępne deklaracje** wybierz **protokół** i kliknij przycisk **Dodaj.** Teraz skonfiguruj **właściwości** deklaracji **protokołu.** W **części Nazwa wyświetlana**dodaj nazwę, którą chcesz wyświetlić użytkownikom aplikacji. W **pozycji Nazwa**dodaj {url_scheme_of_your_app}.
7. Naciśnij klawisz F5, aby uruchomić aplikację, kliknij przycisk **Zaloguj się** i zaloguj się do aplikacji u wybranego dostawcy tożsamości. Po pomyślnym zalogowaniu aplikacja działa bez błędów i można zbadać wewnętrznej bazy danych i dokonać aktualizacji danych.

## <a name="store-the-authentication-token-on-the-client"></a><a name="tokens"></a>Przechowywanie tokenu uwierzytelniania na kliencie
W poprzednim przykładzie pokazano standardowe logowanie, które wymaga od klienta, aby skontaktować się zarówno z dostawcą tożsamości i usługi app service przy każdym uruchomieniu aplikacji. Nie tylko jest to metoda nieefektywne, można napotkać na użycie dotyczy problemów, jeśli wielu klientów próbuje uruchomić aplikację w tym samym czasie. Lepszym rozwiązaniem jest buforowanie tokenu autoryzacji zwróconego przez usługę App Service i spróbuj użyć tego najpierw przed użyciem logowania opartego na dostawcy.

> [!NOTE]
> Token wystawiony przez usługi App Services można buforować niezależnie od tego, czy używasz uwierzytelniania zarządzanego przez klienta, czy z usługą. W tym samouczku użyto uwierzytelniania zarządzanego przez usługę.
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Następne kroki
Teraz, po wykonaniu tego samouczka uwierzytelniania podstawowego, należy rozważyć kontynuowanie jednego z następujących samouczków:

* [Dodawanie powiadomień wypychanych do aplikacji](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Dowiedz się, jak dodać obsługę powiadomień wypychanych do aplikacji i skonfigurować zaplecze Aplikacji mobilnej na potrzeby wysyłania powiadomień wypychanych przy użyciu usługi Azure Notification Hubs.
* [Włączanie synchronizacji w trybie offline dla aplikacji](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Dowiedz się, jak dodać obsługę aplikacji w trybie offline przy użyciu zaplecza Aplikacji mobilnej. Synchronizacja w trybie offline umożliwia użytkownikom końcowym interakcję z aplikacją mobilną &mdash; przeglądanie, dodawanie lub modyfikowanie danych &mdash; nawet w przypadku braku połączenia sieciowego.

<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
