---
title: Dodawanie uwierzytelniania do aplikacji platformy uniwersalnej Windows (UWP) | Dokumentacja firmy Microsoft
description: 'Dowiedz się, jak uwierzytelniać użytkowników aplikacji platformy uniwersalnej Windows (UWP) przy użyciu wielu dostawców tożsamości, w tym za pomocą usługi Azure App Service Mobile Apps: Usługi AAD, Google, Facebook, Twitter i Microsoft.'
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
ms.openlocfilehash: 7caaa1ca4cdaf7290b7ce05d17c07e565e7b51d1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62128002"
---
# <a name="add-authentication-to-your-windows-app"></a>Dodawanie uwierzytelniania do aplikacji Windows
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

W tym temacie dowiesz się, jak dodać uwierzytelnianie oparte na chmurze do aplikacji mobilnej. W tym samouczku dodasz uwierzytelnianie do projektu quickstart uniwersalnej platformy Windows (UWP) dla aplikacji mobilnych za pomocą dostawcy tożsamości, która jest obsługiwana przez usługę Azure App Service. Po pomyślnym jest uwierzytelniony i autoryzowany przez zaplecze aplikacji mobilnej, jest wyświetlana wartość Identyfikatora użytkownika.

Ten samouczek opiera się na szybki start Mobile Apps. Najpierw musisz zakończyć samouczka [Rozpoczynanie pracy z usługą Mobile Apps](app-service-mobile-windows-store-dotnet-get-started.md).

## <a name="register"></a>Rejestrowanie aplikacji do uwierzytelniania i konfigurowanie usługi App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Dodawanie aplikacji do adresów URL dozwolonych zewnętrznego przekierowania

Bezpieczne uwierzytelnianie wymaga, zdefiniuj nowy schemat adresu URL dla aplikacji. Dzięki temu system uwierzytelniania przekierować z powrotem do aplikacji po zakończeniu procesu uwierzytelniania. W tym samouczku używamy schemat adresu URL _appname_ w całym. Można jednak użyć dowolnego wybranego schematu URL. Powinien on być unikatowy dla twojej aplikacji mobilnej. Aby włączyć przekierowywanie po stronie serwera:

1. W [witryny Azure portal](https://ms.portal.azure.com), wybierz swoją usługę aplikacji.

2. Kliknij przycisk **uwierzytelniania / autoryzacji** opcji menu.

3. W **dozwolone zewnętrzne adresy URL przekierowania**, wprowadź `url_scheme_of_your_app://easyauth.callback`.  **Url_scheme_of_your_app** w tym ciągu jest schemat adresu URL aplikacji mobilnej.  Powinien on być zgodny z normalnej Specyfikacja adresu URL dla protokołu (litery użytkowania i tylko cyfry oraz rozpoczynać się literą).  Należy zapamiętać, ciąg, który wybierzesz, ponieważ trzeba będzie dostosować kod aplikacji mobilnych za pomocą schemat adresu URL w kilku miejscach.

4. Kliknij pozycję **Zapisz**.

## <a name="permissions"></a>Ogranicz uprawnienia do uwierzytelnionych użytkowników
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Można teraz, upewnij się, że anonimowy dostęp do swojej wewnętrznej bazy danych zostały wyłączone. Za pomocą projektu aplikacji platformy uniwersalnej systemu Windows Ustaw jako projekt startowy, wdrażanie i uruchamianie aplikacji; Upewnij się, że wystąpił nieobsługiwany wyjątek, kod stanu 401 (bez autoryzacji) jest wywoływane po uruchomieniu aplikacji. Dzieje się tak, ponieważ aplikacja podejmie próbę dostępu do kodu aplikacji mobilnej jako nieuwierzytelniony użytkownik zgłasza, ale *TodoItem* tabeli teraz wymaga uwierzytelnienia.

Następnie należy zaktualizować aplikację do uwierzytelniania kont użytkowników przed zażądaniem zasobów z usługi App Service.

## <a name="add-authentication"></a>Dodawanie uwierzytelniania do aplikacji
1. Na platformie UWP aplikacji w pliku MainPage.xaml.cs projektu i Dodaj poniższy fragment kodu:
   
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
   
    Ten kod uwierzytelnia użytkownika przy użyciu logowania do usługi Facebook. Jeśli używasz dostawcy tożsamości innych niż usługi Facebook, zmień wartość **MobileServiceAuthenticationProvider** powyżej wartości dla dostawcy.
2. Zastąp **OnNavigatedTo()** metody w MainPage.xaml.cs. Następnie dodasz **Zaloguj** przycisku do aplikacji, która wyzwala uwierzytelnianie.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            if (e.Parameter is Uri)
            {
                App.MobileService.ResumeWithURL(e.Parameter as Uri);
            }
        }

3. Dodaj poniższy fragment kodu do pliku MainPage.xaml.cs:
   
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
4. Otwórz plik projektu w pliku MainPage.xaml, zlokalizuj element, który definiuje **Zapisz** przycisk i zastąp go następującym kodem:
   
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
5. Dodaj poniższy fragment kodu do pliku App.xaml.cs:

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
6. Otwórz plik manifestu Package.appx, przejdź do **deklaracje**w **dostępne deklaracje** listy rozwijanej wybierz **protokołu** i kliknij przycisk **Dodaj** przycisku. Teraz skonfigurować **właściwości** z **protokołu** deklaracji. W **nazwę wyświetlaną**, Dodaj nazwę, które mają zostać wyświetlone dla użytkowników aplikacji. W **nazwa**, Dodaj swoje {url_scheme_of_your_app}.
7. Naciśnij klawisz F5, aby uruchomić aplikację, kliknij przycisk **Zaloguj** przycisk, a następnie zaloguj się do aplikacji przy użyciu dostawcy tożsamości wybrany. Po logowanie zakończy się pomyślnie, aplikacja działa bez błędów i masz możliwość wykonywania zapytań wewnętrzną bazą danych i aktualizowanie danych.

## <a name="tokens"></a>Store token uwierzytelniania na komputerze klienckim
W poprzednim przykładzie pokazano standardowy logowania, która wymaga od klienta do kontaktowania się z dostawcy tożsamości i usługi App Service każdorazowym uruchomieniu aplikacji. Nie tylko jest nieefektywne, można uruchomić tej metody do użycia — odnosi się problemów należy spróbować wielu klientów uruchomić aplikację w tym samym czasie. Lepszym rozwiązaniem jest token autoryzacji, zwrócony przez usługi App Service w pamięci podręcznej, a następnie spróbuj użyć to najpierw przed rozpoczęciem korzystania z opartą na dostawcy logowania.

> [!NOTE]
> Może buforować token wystawiony przez usługi aplikacji, niezależnie od tego, czy używasz zarządzanych przez klienta lub zarządzane przez usługę uwierzytelniania. W tym samouczku korzysta z zarządzanego przez usługę uwierzytelniania.
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy została zakończona w tym samouczku uwierzytelnianie podstawowe, należy wziąć pod uwagę przejściem do jednej z następujących samouczków:

* [Dodawanie powiadomień wypychanych do aplikacji](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Dowiedz się, jak dodać obsługę powiadomień wypychanych do aplikacji i skonfigurować zaplecze Aplikacji mobilnej na potrzeby wysyłania powiadomień wypychanych przy użyciu usługi Azure Notification Hubs.
* [Włączanie synchronizacji w trybie offline dla aplikacji](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Dowiedz się, jak dodać obsługę aplikacji w trybie offline przy użyciu zaplecza Aplikacji mobilnej. Synchronizacja w trybie offline umożliwia użytkownikom końcowym interakcję z aplikacją mobilną &mdash; przeglądanie, dodawanie lub modyfikowanie danych &mdash; nawet w przypadku braku połączenia sieciowego.

<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
