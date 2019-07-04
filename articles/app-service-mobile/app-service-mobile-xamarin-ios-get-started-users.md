---
title: Rozpoczynanie pracy z usługą uwierzytelniania dla aplikacji mobilnych w systemie iOS program Xamarin
description: Dowiedz się, jak używać funkcji Mobile Apps, uwierzytelniać użytkowników aplikacji platformy Xamarin dla systemu iOS za pomocą różnych dostawców tożsamości, obejmującej usługi AAD, Google, Facebook, Twitter i Microsoft.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 180cc61b-19c5-48bf-a16c-7181aef3eacc
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: fa1f4bae314025a71568e1e04cbf950ebbe26dbe
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446244"
---
# <a name="add-authentication-to-your-xamarinios-app"></a>Dodawanie uwierzytelniania do aplikacji platformy Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center jest inwestujemy w nowe i zintegrowane usługi decydujące znaczenie dla aplikacji mobilnych. Deweloperzy mogą używać **kompilacji**, **testu** i **dystrybucji** usług do konfigurowania potoku ciągłej integracji i ciągłego dostarczania. Gdy aplikacja jest wdrażana, deweloperzy mogą monitorować stan i użycie ich przy użyciu aplikacji **Analytics** i **diagnostyki** usług i angażuj użytkowników za pomocą **wypychania** Usługa. Deweloperzy mogą również wykorzystać **uwierzytelniania** do uwierzytelniania użytkowników i **danych** usługę, aby utrwalić i synchronizowanie danych aplikacji w chmurze. Zapoznaj się z [platformy App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-ios-get-started-users) już dziś.
>

## <a name="overview"></a>Przegląd

W tym temacie dowiesz się, jak uwierzytelniać użytkowników aplikacji usługi App Service Mobile z aplikacji klienckiej. W tym samouczku dodasz uwierzytelnianie do projektu quickstart platformy Xamarin.iOS przy użyciu dostawcy tożsamości, która jest obsługiwana przez usługę App Service. Po pomyślnie jest uwierzytelniony i autoryzowany przez aplikację Mobile, jest wyświetlana wartość Identyfikatora użytkownika i będzie można uzyskiwać dostęp do danych tabel z ograniczeniami.

Najpierw musisz zakończyć samouczka [tworzenie aplikacji platformy Xamarin.iOS]. Jeśli nie używasz projektu serwera pobranego — szybki start, należy dodać pakiet rozszerzenia uwierzytelnianie do projektu. Aby uzyskać więcej informacji na temat pakietów rozszerzeń serwera, zobacz [pracy z zestawem SDK serwera zaplecza platformy .NET dla usługi Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Rejestrowanie aplikacji do uwierzytelniania i konfigurowanie usług aplikacji
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a>Dodawanie aplikacji do adresów URL dozwolonych zewnętrznego przekierowania

Bezpieczne uwierzytelnianie wymaga, zdefiniuj nowy schemat adresu URL dla aplikacji. Dzięki temu system uwierzytelniania przekierować z powrotem do aplikacji po zakończeniu procesu uwierzytelniania. W tym samouczku używamy schemat adresu URL _appname_ w całym. Można jednak użyć dowolnego wybranego schematu URL. Powinien on być unikatowy dla twojej aplikacji mobilnej. Aby włączyć przekierowywanie po stronie serwera:

1. W [witryny Azure portal](https://portal.azure.com/), wybierz swoją usługę aplikacji.

2. Kliknij przycisk **uwierzytelniania / autoryzacji** opcji menu.

3. W **dozwolone zewnętrzne adresy URL przekierowania**, wprowadź `url_scheme_of_your_app://easyauth.callback`.  **Url_scheme_of_your_app** w tym ciągu jest schemat adresu URL aplikacji mobilnej.  Powinien on być zgodny z normalnej Specyfikacja adresu URL dla protokołu (litery użytkowania i tylko cyfry oraz rozpoczynać się literą).  Należy zapamiętać, ciąg, który wybierzesz, ponieważ trzeba będzie dostosować kod aplikacji mobilnych za pomocą schemat adresu URL w kilku miejscach.

4. Kliknij przycisk **OK**.

5. Kliknij pozycję **Zapisz**.

## <a name="restrict-permissions-to-authenticated-users"></a>Ogranicz uprawnienia do uwierzytelnionych użytkowników
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* W programie Visual Studio lub Xamarin Studio należy uruchomić projekt klienta na urządzenia lub emulatora. Upewnij się, że wystąpił nieobsługiwany wyjątek, kod stanu 401 (bez autoryzacji) jest wywoływane po uruchomieniu aplikacji. Błąd jest rejestrowane w konsoli debugera. Dlatego w programie Visual Studio, powinien zostać wyświetlony błąd w oknie danych wyjściowych.

    Tego błędu nieautoryzowanego odbywa się, ponieważ aplikacja próbuje uzyskać dostęp zaplecza aplikacji mobilnej jako nieuwierzytelniony użytkownik zgłasza do. *TodoItem* tabeli teraz wymaga uwierzytelnienia.

Następnie zmodyfikujemy aplikację kliencką do zasobów na żądanie z zaplecza aplikacji mobilnej z uwierzytelnionego użytkownika.

## <a name="add-authentication-to-the-app"></a>Dodawanie uwierzytelniania do aplikacji
W tej sekcji zmodyfikujesz aplikację, aby wyświetlić ekran logowania przed wyświetleniem danych. Po uruchomieniu aplikacji, nie będzie nawiązać połączenia z usługi App Service i nie będą wyświetlane wszystkie dane. Po raz pierwszy użytkownik wykonuje gestu odświeżania, zostanie wyświetlony ekran logowania; Po pomyślnym zalogowaniu zostanie wyświetlona lista zadań do wykonania.

1. W projekcie klienta, otwórz plik **QSTodoService.cs** i dodaj następującą instrukcję using i `MobileServiceUser` za pomocą metody dostępu do klasy QSTodoService:

    ```csharp
    using UIKit;

    // Logged in user
    private MobileServiceUser user;
    public MobileServiceUser User { get { return user; } }
    ```

2. Dodaj nową metodę o nazwie **Uwierzytelnij** do **QSTodoService** przy użyciu następujących definicji:

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
    > Jeśli używasz dostawcy tożsamości innych niż usługi Facebook, zmień wartość przekazana do **LoginAsync** powyżej do jednej z następujących czynności: _MicrosoftAccount_, _Twitter_, _Google_, lub _WindowsAzureActiveDirectory_.

3. Otwórz **QSTodoListViewController.cs**. Modyfikowanie definicji metody **ViewDidLoad** usuwanie wywołanie **RefreshAsync()** w pobliżu końca:

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

4. Zmodyfikuj metodę **RefreshAsync** uwierzytelniać **użytkownika** właściwość ma wartość null. Dodaj następujący kod w górnej części definicji metody:

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

6. Otwórz **Info.plist** pliku, przejdź do folderu **typy adresów URL** w **zaawansowane** sekcji. Teraz skonfigurować **identyfikator** i **Schematy adresów URL** typ adresu URL i kliknij przycisk **Dodaj typ adresu URL**. **Schematy adresów URL** powinna być taka sama, jak Twoje {url_scheme_of_your_app}.
7. W programie Visual Studio, nawiązanie połączenia z hostem Mac lub Visual Studio dla komputerów Mac należy uruchomić projekt klienta, przeznaczonych dla urządzenia lub emulatora. Sprawdź, czy aplikacja nie wyświetla żadnych danych.

    Wykonaj gest odświeżania, przenosząc je w dół na liście elementów, które spowoduje wyświetlenie ekranu logowania. Po pomyślnym wprowadzeniu prawidłowych poświadczeń, aplikacja wyświetli listę zadań do wykonania i wprowadzasz aktualizacje danych.

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Tworzenie aplikacji platformy Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md
