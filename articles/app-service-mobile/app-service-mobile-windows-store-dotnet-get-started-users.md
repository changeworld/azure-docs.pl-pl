---
title: Dodawanie uwierzytelniania do aplikacji platformy UWP
description: Dowiedz się, jak używać Mobile Apps Azure App Service do uwierzytelniania użytkowników aplikacji platforma uniwersalna systemu Windows (platformy UWP) z dostawcami tożsamości, takimi jak AAD, Google, Facebook, Twitter i Microsoft.
ms.assetid: 6cffd951-893e-4ce5-97ac-86e3f5ad9466
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 32d4313b345964a2db13d68e83f81756a4acf0d9
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77458940"
---
# <a name="add-authentication-to-your-windows-app"></a>Dodawanie uwierzytelniania do aplikacji systemu Windows
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Omówienie
W tym temacie opisano sposób dodawania uwierzytelniania opartego na chmurze do aplikacji mobilnej. W tym samouczku dodasz uwierzytelnianie do projektu szybkiego startu platforma uniwersalna systemu Windows (platformy UWP) dla Mobile Apps przy użyciu dostawcy tożsamości obsługiwanego przez Azure App Service. Po pomyślnym uwierzytelnieniu i autoryzacji przez zaplecze aplikacji mobilnej zostanie wyświetlona wartość identyfikatora użytkownika.

Ten samouczek jest oparty na Mobile Apps Szybki Start. Musisz najpierw ukończyć samouczek [wprowadzenie do Mobile Apps](app-service-mobile-windows-store-dotnet-get-started.md).

## <a name="register"></a>Zarejestruj aplikację pod kątem uwierzytelniania i skonfiguruj App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Dodaj aplikację do dozwolonych zewnętrznych adresów URL przekierowania

Bezpieczne uwierzytelnianie wymaga zdefiniowania nowego schematu adresu URL dla aplikacji. Dzięki temu system uwierzytelniania może przekierować z powrotem do aplikacji po zakończeniu procesu uwierzytelniania. W tym samouczku używamy _w systemie adresu_ URL. Można jednak użyć dowolnego wybranego schematu adresów URL. Powinna być unikatowa dla aplikacji mobilnej. Aby włączyć przekierowanie po stronie serwera:

1. W [Azure Portal](https://ms.portal.azure.com)wybierz App Service.

2. Kliknij opcję menu **uwierzytelnianie/autoryzacja** .

3. W polu **dozwolone adresy URL zewnętrznych przekierowań**wprowadź `url_scheme_of_your_app://easyauth.callback`.  **Url_scheme_of_your_app** w tym ciągu jest schematem adresu URL aplikacji mobilnej.  Powinna być zgodna ze specyfikacją normalnych adresów URL dla protokołu (używaj tylko liter i cyfr i zaczynać się od litery).  Należy zanotować ciąg, który wybierzesz, ponieważ trzeba będzie dostosować kod aplikacji mobilnej przy użyciu schematu adresu URL w kilku miejscach.

4. Kliknij przycisk **Save** (Zapisz).

## <a name="permissions"></a>Ograniczanie uprawnień do uwierzytelnionych użytkowników
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Teraz możesz sprawdzić, czy dostęp anonimowy do zaplecza został wyłączony. Gdy projekt aplikacji platformy UWP jest ustawiony jako projekt startowy, wdróż i uruchom aplikację. przed uruchomieniem aplikacji należy sprawdzić, czy zgłoszono nieobsługiwany wyjątek z kodem stanu 401 (bez autoryzacji). Dzieje się tak, ponieważ aplikacja próbuje uzyskać dostęp do kodu aplikacji mobilnej jako nieuwierzytelnionego użytkownika, ale tabela *TodoItem* wymaga teraz uwierzytelniania.

Następnie zaktualizujesz aplikację w celu uwierzytelniania użytkowników przed zażądaniem zasobów z App Service.

## <a name="add-authentication"></a>Dodawanie uwierzytelniania do aplikacji
1. W pliku projektu aplikacji platformy UWP MainPage.xaml.cs i Dodaj następujący fragment kodu:
   
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
   
    Ten kod uwierzytelnia użytkownika za pomocą logowania w serwisie Facebook. Jeśli używasz dostawcy tożsamości innego niż Facebook, Zmień wartość **MobileServiceAuthenticationProvider** powyżej na wartość dla dostawcy.
2. Zastąp metodę **OnNavigatedTo ()** w MainPage.XAML.cs. Następnie dodasz przycisk **Zaloguj** do aplikacji, która wyzwala uwierzytelnianie.

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
4. Otwórz plik projektu MainPage. XAML, Znajdź element, który definiuje przycisk **Zapisz** , i zastąp go następującym kodem:
   
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
6. Otwórz plik Package. appxmanifest, przejdź do **deklaracji**, na liście rozwijanej **dostępne deklaracje** wybierz pozycję **Protokół** , a następnie kliknij przycisk **Dodaj** . Teraz Skonfiguruj **Właściwości** deklaracji **protokołu** . W polu **Nazwa wyświetlana**Dodaj nazwę, która ma być wyświetlana użytkownikom aplikacji. W polu **Nazwa**Dodaj swój {url_scheme_of_your_app}.
7. Naciśnij klawisz F5, aby uruchomić aplikację, kliknij przycisk **Zaloguj się** , a następnie zaloguj się do aplikacji przy użyciu wybranego dostawcy tożsamości. Po pomyślnym zalogowaniu aplikacja zostanie uruchomiona bez błędów i będzie można wykonywać zapytania dotyczące zaplecza i wprowadzać aktualizacje danych.

## <a name="tokens"></a>Przechowywanie tokenu uwierzytelniania na kliencie
W poprzednim przykładzie pokazano logowanie standardowe, które wymaga, aby klient skontaktował się zarówno z dostawcą tożsamości, jak i App Service przy każdym uruchomieniu aplikacji. Ta metoda nie tylko jest nieefektywna, dlatego można korzystać z problemów związanych z użyciem, dlatego wielu klientów próbuje uruchomić aplikację w tym samym czasie. Lepszym rozwiązaniem jest buforowanie tokenu autoryzacji zwróconego przez App Service i próba skorzystania z niego w pierwszej kolejności przed użyciem logowania opartego na dostawcy.

> [!NOTE]
> Token wystawiony przez App Services można buforować bez względu na to, czy jest używane uwierzytelnianie zarządzane przez klienta, czy za pomocą usługi. W tym samouczku jest stosowane uwierzytelnianie zarządzane przez usługę.
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Następne kroki
Po ukończeniu tego samouczka dotyczącego uwierzytelniania podstawowego Rozważ przejście do jednego z następujących samouczków:

* [Dodawanie powiadomień wypychanych do aplikacji](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Dowiedz się, jak dodać obsługę powiadomień wypychanych do aplikacji i skonfigurować zaplecze Aplikacji mobilnej na potrzeby wysyłania powiadomień wypychanych przy użyciu usługi Azure Notification Hubs.
* [Włączanie synchronizacji w trybie offline dla aplikacji](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Dowiedz się, jak dodać obsługę aplikacji w trybie offline przy użyciu zaplecza Aplikacji mobilnej. Synchronizacja w trybie offline umożliwia użytkownikom końcowym interakcję z aplikacją mobilną &mdash; przeglądanie, dodawanie lub modyfikowanie danych &mdash; nawet w przypadku braku połączenia sieciowego.

<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
