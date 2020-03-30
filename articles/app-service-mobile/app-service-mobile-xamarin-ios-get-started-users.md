---
title: Wprowadzenie do uwierzytelniania w układzie Xamarin iOS
description: Dowiedz się, jak używać aplikacji mobilnych do uwierzytelniania użytkowników aplikacji Xamarin na iOS u dostawców tożsamości, takich jak AAD, Google, Facebook, Twitter i Microsoft.
ms.assetid: 180cc61b-19c5-48bf-a16c-7181aef3eacc
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 05e936accbcd5c6fa2760c4f8682d907557f23b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461321"
---
# <a name="add-authentication-to-your-xamarinios-app"></a>Dodawanie uwierzytelniania do aplikacji Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Omówienie

W tym temacie pokazano, jak uwierzytelnić użytkowników aplikacji mobilnej usługi App Service z aplikacji klienckiej. W tym samouczku można dodać uwierzytelnianie do projektu szybki start platformy Xamarin.iOS przy użyciu dostawcy tożsamości, który jest obsługiwany przez usługę App Service. Po pomyślnym uwierzytelnieniu i autoryzowaniu przez aplikację mobilną wyświetlana jest wartość identyfikatora użytkownika, a dostęp do danych tabeli z ograniczeniami będzie można uzyskać.

Najpierw musisz ukończyć samouczek [Tworzenie aplikacji Xamarin.iOS]. Jeśli nie używasz pobranego projektu serwera szybkiego startu, należy dodać pakiet rozszerzenia uwierzytelniania do projektu. Aby uzyskać więcej informacji na temat pakietów rozszerzeń serwera, zobacz [Praca z zestawem SDK serwera wewnętrznej bazy danych .NET dla aplikacji azure mobile apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Rejestrowanie aplikacji w celu uwierzytelniania i konfigurowanie usług App Services
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a>Dodawanie aplikacji do adresów URL dozwolonego przekierowania zewnętrznego

Bezpieczne uwierzytelnianie wymaga zdefiniowania nowego schematu adresu URL dla aplikacji. Dzięki temu system uwierzytelniania przekierować z powrotem do aplikacji po zakończeniu procesu uwierzytelniania. W tym samouczku używamy _appname_ schematu adresu URL w całym. Możesz jednak użyć dowolnego schematu adresu URL, który wybierzesz. Powinien być unikalny dla twojej aplikacji mobilnej. Aby włączyć przekierowanie po stronie serwera:

1. W [witrynie Azure portal](https://portal.azure.com/)wybierz usługę app service.

2. Kliknij opcję menu **Uwierzytelnianie / Autoryzacja.**

3. W **adresach URL dozwolonego przekierowania zewnętrznego**wprowadź . `url_scheme_of_your_app://easyauth.callback`  **url_scheme_of_your_app** w tym ciągu jest schemat adresu URL dla aplikacji mobilnej.  Powinien być zgodny ze specyfikacją normalnego adresu URL dla protokołu (użyj tylko liter i cyfr i zacznij od litery).  Należy zanotować ciąg, który wybierzesz, ponieważ trzeba będzie dostosować kod aplikacji mobilnej za pomocą schematu adresu URL w kilku miejscach.

4. Kliknij przycisk **OK**.

5. Kliknij przycisk **Zapisz**.

## <a name="restrict-permissions-to-authenticated-users"></a>Ograniczanie uprawnień do uwierzytelnionych użytkowników
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* W programie Visual Studio lub Xamarin Studio uruchom projekt klienta na urządzeniu lub emulatorze. Sprawdź, czy nieobsługiowany wyjątek o kodzie stanu 401 (Nieautoryzowane) jest wywoływany po uruchomieniu aplikacji. Błąd jest rejestrowany w konsoli debugera. Tak więc w programie Visual Studio powinien zostać wyświetlony błąd w oknie danych wyjściowych.

    Ten nieautoryzowany błąd występuje, ponieważ aplikacja próbuje uzyskać dostęp do wewnętrznej bazy danych aplikacji mobilnej jako nieuwierzyfowanego użytkownika. Tabela *TodoItem* wymaga teraz uwierzytelnienia.

Następnie zaktualizujesz aplikację kliencką, aby zażądać zasobów z zaplecza aplikacji mobilnej z uwierzytelnionym użytkownikiem.

## <a name="add-authentication-to-the-app"></a>Dodawanie uwierzytelniania do aplikacji
W tej sekcji zmodyfikujesz aplikację, aby wyświetlić ekran logowania przed wyświetleniem danych. Po uruchomieniu aplikacja nie połączy się z usługą App Service i nie będzie wyświetlać żadnych danych. Po pierwszym uruchomieniu gestu odświeżania pojawi się ekran logowania; po pomyślnym zalogowaniu zostanie wyświetlona lista przedmiotów do wykonania.

1. W projekcie klienta otwórz **plik QSTodoService.cs** i dodaj następujące elementy `MobileServiceUser` za pomocą instrukcji i akcesora do klasy QSTodoService:

    ```csharp
    using UIKit;

    // Logged in user
    private MobileServiceUser user;
    public MobileServiceUser User { get { return user; } }
    ```

2. Dodaj nową metodę o nazwie **Uwierzytelnij** do **usługi QSTodoService** z następującą definicją:

    ```csharp
    public async Task Authenticate(UIViewController view)
    {
        try
        {
            AppDelegate.ResumeWithURL = url => url.Scheme == "{url_scheme_of_your_app}" && client.ResumeWithURL(url);
            user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
        }
        catch (Exception ex)
        {
            Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
        }
    }
    ```

    > [!NOTE]
    > Jeśli korzystasz z dostawcy tożsamości innego niż Facebook, zmień wartość przekazywana do **LoginAsync** powyżej na jedną z następujących czynności: _MicrosoftAccount_, _Twitter_, _Google_lub _WindowsAzureActiveDirectory_.

3. Otwórz **QSTodoListViewController.cs**. Zmodyfikuj definicję metody **ViewDidLoad,** usuwając wywołanie **RefreshAsync()** na końcu:

    ```csharp
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
    ```

4. Zmodyfikuj metodę **RefreshAsync,** aby uwierzytelnić, jeśli **właściwość User** ma wartość null. Dodaj następujący kod u góry definicji metody:

    ```csharp
    // start of RefreshAsync method
    if (todoService.User == null) {
        await QSTodoService.DefaultService.Authenticate(this);
        if (todoService.User == null) {
            Console.WriteLine("couldn't login!!");
            return;
        }
    }
    // rest of RefreshAsync method
    ```

5. Otwórz **AppDelegate.cs**, dodaj następującą metodę:

    ```csharp
    public static Func<NSUrl, bool> ResumeWithURL;

    public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
    {
        return ResumeWithURL != null && ResumeWithURL(url);
    }
    ```

6. Otwórz plik **Info.plist,** przejdź do **typów adresów URL** w sekcji **Zaawansowane.** Teraz skonfiguruj **identyfikator** i **schematy adresów URL** typu adresu URL i kliknij pozycję **Dodaj typ adresu URL**. **Schematy adresów URL** powinny być takie same jak {url_scheme_of_your_app}.
7. W programie Visual Studio połączony z hostem komputera Mac lub programem Visual Studio dla komputerów Mac uruchom projekt klienta przeznaczony dla urządzenia lub emulatora. Sprawdź, czy aplikacja nie wyświetla żadnych danych.

    Wykonaj gest odświeżania, ściągając listę elementów, co spowoduje wyświetlenie ekranu logowania. Po pomyślnym wprowadzeniu prawidłowych poświadczeń aplikacja wyświetli listę elementów zadań do wykonania i będzie można wprowadzać aktualizacje danych.

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Tworzenie aplikacji platformy Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md
