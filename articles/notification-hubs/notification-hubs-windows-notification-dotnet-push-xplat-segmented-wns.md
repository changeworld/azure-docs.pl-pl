---
title: Wysyłanie powiadomień do konkretnych urządzeń (platforma uniwersalna systemu Windows) | Microsoft Docs
description: Za pomocą usługi Azure Notification Hubs z tagami w rejestracji wysyłaj ważne wiadomości do aplikacji platformy uniwersalnej systemu Windows.
services: notification-hubs
documentationcenter: windows
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 994d2eed-f62e-433c-bf65-4afebf1c0561
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/22/2019
ms.author: jowargo
ms.openlocfilehash: 9cfe5f490ef4063e02d9407f23130c1a216961ed
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402460"
---
# <a name="tutorial-push-notifications-to-specific-windows-devices-running-universal-windows-platform-applications"></a>Samouczek: wysyłanie powiadomień push do konkretnych urządzeń z systemem Windows z uruchomionymi aplikacjami platformy uniwersalnej systemu Windows

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Przegląd

Korzystając z tego samouczka, dowiesz się, jak rozgłaszać ważne wiadomości do aplikacji ze Sklepu Windows lub aplikacji dla systemu Windows Phone 8.1 (bez użycia platformy Silverlight) przy użyciu usługi Azure Notification Hubs. Jeśli aplikacja ma być przeznaczona dla systemu Windows Phone 8.1 z platformą Silverlight, zobacz wersję dla systemu [Windows Phone](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md).

Korzystając z tego samouczka, dowiesz się, jak wypychać powiadomienia do konkretnych urządzeń z systemem Windows z uruchomioną aplikacją platformy uniwersalnej systemu Windows przy użyciu usługi Azure Notification Hubs. Po zakończeniu samouczka możesz przeprowadzić rejestrację interesujących Cię kategorii ważnych wiadomości. Będziesz otrzymywać tylko powiadomienia push dla tych kategorii.

Scenariusze rozgłaszania są włączane przez uwzględnienie co najmniej jednego *tagu* podczas tworzenia rejestracji w centrum powiadomień. W przypadku wysłania powiadomień do tagu wszystkie urządzenia zarejestrowane dla tego tagu otrzymają powiadomienie. Aby uzyskać więcej informacji o tagach, zobacz [Tagi w rejestracjach](notification-hubs-tags-segment-push-message.md).

> [!NOTE]
> Wersje projektu dla Sklepu Windows i systemu Windows Phone w wersji 8.1 i wcześniejszych nie są obsługiwane w programie Visual Studio 2017. Aby uzyskać więcej informacji, zobacz [Obsługiwane platformy i zgodność programu Visual Studio 2017](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

W tym samouczku wykonasz następujące kroki:

> [!div class="checklist"]
> * Dodawanie wyboru kategorii do aplikacji mobilnej
> * Rejestrowanie na potrzeby powiadomień
> * Wysyłanie otagowanego powiadomienia
> * Uruchamianie aplikacji i generowanie powiadomień

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki z artykułu [Samouczek: wysyłanie powiadomień do aplikacji platformy uniwersalnej systemu Windows przy użyciu usługi Azure Notification Hubs][get-started] przed rozpoczęciem tego samouczka.  

## <a name="add-category-selection-to-the-app"></a>Dodawanie wyboru kategorii do aplikacji

Pierwszym krokiem jest dodanie elementów interfejsu użytkownika do istniejącej strony głównej, co umożliwi użytkownikom wybieranie kategorii do zarejestrowania. Wybrane kategorie są przechowywane na urządzeniu. Po uruchomieniu aplikacji w centrum powiadomień zostanie utworzona rejestracja urządzenia z wybranymi kategoriami w formie tagów.

1. Otwórz plik projektu MainPage.xaml, a następnie skopiuj i wklej następujący kod w elemencie `Grid`:

    ```xml
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition/>
            <ColumnDefinition/>
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1" HorizontalAlignment="Center"/>
        <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click"/>
    </Grid>
    ```

2. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt i dodaj nową klasę: **Notifications**. Dodaj modyfikator **public** do definicji klasy, a następnie dodaj następujące instrukcje `using` do nowego pliku kodu:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.Storage;
    using System.Threading.Tasks;
    ```

3. Skopiuj następujący kod do nowej klasy `Notifications`:

    ```csharp
    private NotificationHub hub;

    public Notifications(string hubName, string listenConnectionString)
    {
        hub = new NotificationHub(hubName, listenConnectionString);
    }

    public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
    {
        ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
        return await SubscribeToCategories(categories);
    }

    public IEnumerable<string> RetrieveCategories()
    {
        var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
        return categories != null ? categories.Split(','): new string[0];
    }

    public async Task<Registration> SubscribeToCategories(IEnumerable<string> categories = null)
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        if (categories == null)
        {
            categories = RetrieveCategories();
        }

        // Using a template registration to support notifications across platforms.
        // Any template notifications that contain messageParam and a corresponding tag expression
        // will be delivered for this registration.

        const string templateBodyWNS = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

        return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "simpleWNSTemplateExample",
                categories);
    }
    ```

    Ta klasa używa magazynu lokalnego do przechowywania kategorii wiadomości, które mają być odbierane przez to urządzenie. Zamiast wywoływać metodę `RegisterNativeAsync`, wywołaj metodę `RegisterTemplateAsync`, aby przeprowadzić rejestrację na potrzeby kategorii przy użyciu rejestracji szablonu.

    Jeśli chcesz zarejestrować więcej niż jeden szablon (np. jeden szablon dla powiadomień wyskakujących i jeden dla kafelków), podaj nazwę szablonu (np. „simpleWNSTemplateExample”). Nadaj nazwy szablonom, aby można było je aktualizować lub usuwać.

    >[!NOTE]
    >Jeśli urządzenie zarejestruje wiele szablonów z tym samym tagiem, nadejście wiadomości z tym tagiem jako docelowym spowoduje dostarczenie wielu powiadomień do urządzenia (jedno dla każdego szablonu). To zachowanie jest przydatne, jeśli jedna wiadomość logiczna ma powodować wiele powiadomień wizualnych (np. wyświetlać wskaźnik i powiadomienie wyskakujące w aplikacji ze Sklepu Windows).

    Aby uzyskać więcej informacji, zobacz [Szablony](notification-hubs-templates-cross-platform-push-messages.md).

4. W pliku projektu App.xaml.cs dodaj następującą właściwość do klasy `App`:

    ```csharp
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```

    Ta właściwość służy do tworzenia wystąpienia klasy `Notifications` i uzyskiwania do niego dostępu.

    W kodzie zamień symbole zastępcze `<hub name>` i `<connection string with listen access>` na uzyskane wcześniej wartości: nazwę centrum powiadomień i parametry połączenia *DefaultListenSharedAccessSignature*.

   > [!NOTE]
   > Ponieważ poświadczenia dystrybuowane przy użyciu aplikacji klienckiej nie są zazwyczaj zabezpieczone, przy użyciu aplikacji klienckiej dystrybuuj wyłącznie klucz dostępu do *nasłuchiwania*. Dostęp do nasłuchiwania umożliwia aplikacji rejestrowanie powiadomień, ale nie może ona modyfikować istniejących rejestracji ani wysyłać powiadomień. Klucz pełnego dostępu jest używany w zabezpieczonej usłudze zaplecza do wysyłania powiadomień oraz zmiany istniejących rejestracji.

5. W pliku `MainPage.xaml.cs` dodaj następujący wiersz:

    ```csharp
    using Windows.UI.Popups;
    ```

6. W pliku `MainPage.xaml.cs` dodaj następującą metodę:

    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var categories = new HashSet<string>();
        if (WorldToggle.IsOn) categories.Add("World");
        if (PoliticsToggle.IsOn) categories.Add("Politics");
        if (BusinessToggle.IsOn) categories.Add("Business");
        if (TechnologyToggle.IsOn) categories.Add("Technology");
        if (ScienceToggle.IsOn) categories.Add("Science");
        if (SportsToggle.IsOn) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

        var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    ```

    Ta metoda tworzy listę kategorii i używa klasy `Notifications` do przechowywania listy w magazynie lokalnym. Ponadto rejestruje ona odpowiednie tagi w centrum powiadomień. Jeśli kategorie zostaną zmienione, rejestracja zostanie ponownie utworzona przy użyciu nowych kategorii.

Aplikacja może teraz przechowywać zestaw kategorii w magazynie lokalnym na urządzeniu. Aplikacja rejestruje się w centrum powiadomień za każdym razem, gdy użytkownik zmieni wybór kategorii.

## <a name="register-for-notifications"></a>Rejestrowanie na potrzeby powiadomień

W tej sekcji rejestrujesz się w centrum powiadomień przy uruchamianiu przy użyciu kategorii przechowywanych w magazynie lokalnym.

> [!NOTE]
> Ponieważ identyfikator URI kanału przypisany przez usługę powiadomień systemu Windows (WNS) może ulec zmianie w dowolnym momencie, należy często rejestrować się w celu otrzymywania powiadomień, aby uniknąć niepowodzeń ich dostarczania. Poniższy przykład przeprowadza rejestrację w celu otrzymywania powiadomień za każdym razem, gdy aplikacja jest uruchamiana. W przypadku często uruchamianych aplikacji (więcej niż raz dziennie) prawdopodobnie możesz pominąć rejestrację, aby zachować przepustowość, jeśli od poprzedniej rejestracji upłynął czas krótszy niż jeden dzień.

1. Aby użyć klasy `notifications` do subskrybowania w oparciu o kategorie, otwórz plik App.xaml.cs, a następnie zaktualizuj metodę `InitNotificationsAsync`.

    ```csharp
    // *** Remove or comment out these lines ***
    //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    //var hub = new NotificationHub("your hub name", "your listen connection string");
    //var result = await hub.RegisterNativeAsync(channel.Uri);

    var result = await notifications.SubscribeToCategories();
    ```

    Ten proces gwarantuje, że podczas uruchamiania aplikacja pobiera kategorie z magazynu lokalnego oraz żąda rejestracji tych kategorii. Metodę `InitNotificationsAsync` utworzono w ramach samouczka [Rozpoczynanie pracy z usługą Notification Hubs][get-started].
2. W pliku projektu `MainPage.xaml.cs` dodaj następujący kod do metody `OnNavigatedTo`:

    ```csharp
    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        var categories = ((App)Application.Current).notifications.RetrieveCategories();

        if (categories.Contains("World")) WorldToggle.IsOn = true;
        if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
        if (categories.Contains("Business")) BusinessToggle.IsOn = true;
        if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
        if (categories.Contains("Science")) ScienceToggle.IsOn = true;
        if (categories.Contains("Sports")) SportsToggle.IsOn = true;
    }
    ```

    Ten kod aktualizuje stronę główną w oparciu o stan poprzednio zapisanych kategorii.

Aplikacja jest teraz ukończona. Może przechowywać zestaw kategorii w magazynie lokalnym na urządzeniu, który jest używany do rejestrowania w centrum powiadomień, gdy użytkownicy dokonają zmian w wyborach kategorii. W następnej sekcji zdefiniujesz zaplecze, które może wysyłać do tej aplikacji powiadomienia w odpowiednich kategoriach.

## <a name="run-the-uwp-app"></a>Uruchamianie aplikacji platformy uniwersalnej systemu Windows 
1. W programie Visual Studio naciśnij klawisz **F5**, aby skompilować i uruchomić aplikację. Interfejs użytkownika aplikacji udostępnia zestaw przełączników pozwalających wybrać kategorie do zasubskrybowania.

    ![Aplikacja Breaking News](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breakingnews-win1.png)

2. Włącz co najmniej jeden przełącznik kategorii, a następnie kliknij przycisk **Subscribe** (Subskrybuj).

    Aplikacja konwertuje wybrane kategorie na tagi i żąda nowej rejestracji urządzenia dla wybranych tagów z centrum powiadomień. Zarejestrowane kategorie są zwracane i wyświetlane w oknie dialogowym.

    ![Przełączniki kategorii i przycisk Subscribe (Subskrybuj)](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast-2.png)

## <a name="create-a-console-app-to-send-tagged-notifications"></a>Tworzenie aplikacji konsolowej do wysyłania powiadomień oznakowane

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-console-app-to-send-tagged-notifications"></a>Uruchom aplikację konsoli, aby wysyłać powiadomienia oznakowane

1. Uruchom aplikację utworzoną w poprzedniej sekcji.
2. Powiadomienia dla wybranych kategorii będą wyświetlane jako powiadomienia wyskakujące. Jeśli wybierzesz opcję powiadomienia, zobaczysz pierwszym okna aplikacji platformy uniwersalnej systemu Windows. 

     ![Powiadomienia wyskakujące](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-reg-2.png)


## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób rozgłaszania ważnych wiadomości według kategorii. Aplikacja zaplecza wypycha otagowane powiadomienia do urządzeń, które zarejestrowały się do odbierania powiadomień dla danego tagu. Aby dowiedzieć się, jak wypychać powiadomienia do konkretnych użytkowników, niezależnie od używanego urządzenia, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
> [Wypychanie powiadomień zlokalizowanych](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- URLs.-->
[get-started]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: https://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[wns object]: https://go.microsoft.com/fwlink/p/?LinkId=260591
