---
title: Wprowadzenie do uwierzytelniania Mobile Apps w programie Xamarin iOS
description: Dowiedz się, jak za pomocą usługi Mobile Apps uwierzytelniać użytkowników aplikacji platformy Xamarin dla systemu iOS za pomocą różnych dostawców tożsamości, takich jak AAD, Google, Facebook, Twitter i Microsoft.
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
ms.openlocfilehash: 1af488d4f170508bbf586621d00e9a92657983ca
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72024819"
---
# <a name="add-authentication-to-your-xamarinios-app"></a>Dodawanie uwierzytelniania do aplikacji platformy Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center obsługuje kompleksowe i zintegrowane usługi centralne do tworzenia aplikacji mobilnych. Deweloperzy mogą używać usług **kompilowania**, **testowania** i **dystrybucji** , aby skonfigurować ciągłą integrację i potok dostarczania. Po wdrożeniu aplikacji deweloperzy mogą monitorować stan i użycie swojej aplikacji przy użyciu usług **analizy** i **diagnostyki** oraz angażować się z użytkownikami za pomocą usługi **wypychania** . Deweloperzy mogą również korzystać z **uwierzytelniania** w celu uwierzytelniania użytkowników i usługi **danych** w celu utrwalania i synchronizowania danych aplikacji w chmurze.
> Jeśli chcesz zintegrować usługi w chmurze w swojej aplikacji mobilnej, zarejestruj się w usłudze App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) już dziś.

## <a name="overview"></a>Przegląd

W tym temacie pokazano, jak uwierzytelniać użytkowników aplikacji mobilnej App Service z poziomu aplikacji klienckiej. W tym samouczku opisano Dodawanie uwierzytelniania do projektu szybkiego startu platformy Xamarin. iOS przy użyciu dostawcy tożsamości obsługiwanego przez App Service. Po pomyślnym uwierzytelnieniu i autoryzacji aplikacji mobilnej zostanie wyświetlona wartość identyfikator użytkownika i będzie można uzyskać dostęp do ograniczonych danych tabeli.

Najpierw należy ukończyć samouczek [Tworzenie aplikacji platformy Xamarin. iOS]. Jeśli nie używasz pobranego projektu szybkiego startu serwera, musisz dodać pakiet rozszerzenia uwierzytelniania do projektu. Aby uzyskać więcej informacji o pakietach rozszerzeń serwera, zobacz [Work with the .NET zaplecz Server SDK for Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Zarejestruj aplikację pod kątem uwierzytelniania i skonfiguruj App Services
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a>Dodaj aplikację do dozwolonych zewnętrznych adresów URL przekierowania

Bezpieczne uwierzytelnianie wymaga zdefiniowania nowego schematu adresu URL dla aplikacji. Dzięki temu system uwierzytelniania może przekierować z powrotem do aplikacji po zakończeniu procesu uwierzytelniania. W tym samouczku używamy _w systemie adresu_ URL. Można jednak użyć dowolnego wybranego schematu adresów URL. Powinna być unikatowa dla aplikacji mobilnej. Aby włączyć przekierowanie po stronie serwera:

1. W [Azure Portal](https://portal.azure.com/)wybierz App Service.

2. Kliknij opcję menu **uwierzytelnianie/autoryzacja** .

3. W polu **dozwolone adresy URL zewnętrznych przekierowań**wprowadź wartość `url_scheme_of_your_app://easyauth.callback`.  **Url_scheme_of_your_app** w tym ciągu to schemat adresu URL aplikacji mobilnej.  Powinna być zgodna ze specyfikacją normalnych adresów URL dla protokołu (używaj tylko liter i cyfr i zaczynać się od litery).  Należy zanotować ciąg, który wybierzesz, ponieważ trzeba będzie dostosować kod aplikacji mobilnej przy użyciu schematu adresu URL w kilku miejscach.

4. Kliknij przycisk **OK**.

5. Kliknij przycisk **Save** (Zapisz).

## <a name="restrict-permissions-to-authenticated-users"></a>Ograniczanie uprawnień do uwierzytelnionych użytkowników
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* W programie Visual Studio lub Xamarin Studio Uruchom projekt klienta na urządzeniu lub w emulatorze. Przed uruchomieniem aplikacji należy sprawdzić, czy zgłoszono nieobsługiwany wyjątek z kodem stanu 401 (bez autoryzacji). Błąd jest rejestrowany w konsoli debugera. W związku z tym w programie Visual Studio w oknie danych wyjściowych powinien zostać wyświetlony błąd.

    Nieautoryzowany błąd występuje, ponieważ aplikacja próbuje uzyskać dostęp do zaplecza aplikacji mobilnej jako nieuwierzytelnionego użytkownika. Tabela *TodoItem* wymaga teraz uwierzytelniania.

Następnie zaktualizujesz aplikację kliencką w celu żądania zasobów z zaplecza aplikacji mobilnej za pomocą uwierzytelnionego użytkownika.

## <a name="add-authentication-to-the-app"></a>Dodawanie uwierzytelniania do aplikacji
W tej sekcji zmodyfikujesz aplikację w celu wyświetlenia ekranu logowania przed wyświetleniem danych. Gdy aplikacja zostanie uruchomiona, nie nawiąże połączenia z App Service i nie będzie wyświetlała żadnych danych. Gdy użytkownik po raz pierwszy wykona gest odświeżenia, zostanie wyświetlony ekran logowania. Po pomyślnym zalogowaniu zostanie wyświetlona lista elementów do wykonania.

1. W projekcie klienta Otwórz plik **QSTodoService.cs** i Dodaj następującą instrukcję using i `MobileServiceUser` z akcesorem do klasy QSTodoService:

    ```csharp
    using UIKit;

    // Logged in user
    private MobileServiceUser user;
    public MobileServiceUser User { get { return user; } }
    ```

2. Dodaj nową metodę o nazwie **uwierzytelnianie** do **QSTodoService** przy użyciu następującej definicji:

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
    > Jeśli używasz dostawcy tożsamości innego niż Facebook, Zmień wartość przekazaną do **LoginAsync** powyżej na jedną z następujących wartości: _MicrosoftAccount_, _Twitter_, _Google_lub _WindowsAzureActiveDirectory_.

3. Otwórz **QSTodoListViewController.cs**. Zmodyfikuj definicję metody **ViewDidLoad** , usuwając wywołanie do **RefreshAsync ()** blisko końca:

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

4. Zmodyfikuj metodę **RefreshAsync** w celu uwierzytelnienia, jeśli właściwość **użytkownika** ma wartość null. Dodaj następujący kod w górnej części definicji metody:

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

5. Otwórz **AppDelegate.cs**, Dodaj następującą metodę:

    ```csharp
    public static Func<NSUrl, bool> ResumeWithURL;

    public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
    {
        return ResumeWithURL != null && ResumeWithURL(url);
    }
    ```

6. Otwórz plik **info. plist** , przejdź do strony **typy adresów URL** w sekcji **Zaawansowane** . Teraz Skonfiguruj **Identyfikator** i **schematy adresów** URL, a następnie kliknij przycisk **Dodaj typ adresu**URL. **Schematy adresów URL** powinny być takie same jak Twoje {url_scheme_of_your_app}.
7. W programie Visual Studio, podłączonym do hosta Mac lub Visual Studio dla komputerów Mac, Uruchom projekt klienta przeznaczony dla urządzenia lub emulatora. Sprawdź, czy aplikacja nie wyświetla żadnych danych.

    Wykonaj gest odświeżenia, pobierając listę elementów, co spowoduje wyświetlenie ekranu logowania. Po pomyślnym wprowadzeniu prawidłowych poświadczeń aplikacja wyświetli listę elementów do wykonania i będzie można wprowadzać aktualizacje danych.

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Tworzenie aplikacji platformy Xamarin. iOS]: app-service-mobile-xamarin-ios-get-started.md
