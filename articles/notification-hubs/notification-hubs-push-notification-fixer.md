---
title: Usługa Azure Notification Hubs — diagnozowanie porzucone powiadomienia
description: Dowiedz się, jak diagnozować typowych problemów z porzuconymi powiadomieniami w usłudze Azure Notification Hubs.
services: notification-hubs
documentationcenter: Mobile
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 4419b68a0005ff6e0b5e46f22fba918699f3ba4f
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2019
ms.locfileid: "56672206"
---
# <a name="azure-notification-hubs---diagnose-dropped-notifications"></a>Usługa Azure Notification Hubs — diagnozowanie porzucone powiadomienia

Jednym z najczęściej zadawane pytania klientów usługi Azure Notification Hubs jest jak rozwiązywać problemy podczas powiadomienia, które są wysyłane z aplikacji nie są wyświetlane na urządzeniach klienckich. Firma chce wiedzieć, gdzie i dlaczego powiadomienia zostały usunięte, a także sposób rozwiązać ten problem. W tym artykule identyfikuje Dlaczego powiadomienia porzucane może lub nie można odebrać przez urządzenia. Dowiedz się, jak analizować i określenia głównej przyczyny.

Koniecznie najpierw zrozumieć, jak usługa Notification Hubs wypycha powiadomienia do urządzenia.

![Architektura centra powiadomień][0]

W ramach przepływu powiadomień typowe Wyślij komunikat jest wysyłany z *zapleczu aplikacji* usługę Notification hubs. Usługa Notification Hubs przetwarza je na wszystkich rejestracji. Przetwarzanie uwzględnia skonfigurowanych znaczników i wyrażeń tagów, aby określić "obiektów docelowych." Obiekty docelowe są wszystkie rejestracje potrzebnych do odbierania powiadomień wypychanych. Tyto registrace mogą znajdować się na dowolne lub naszych obsługiwanych platform: iOS, Google, Windows, Windows Phone, Kindle i Baidu dla systemu Android (Chiny).

Za pomocą ustanowionych celów usługi Notification Hubs wypycha powiadomienia do *push notification service* dla platformy urządzenia. Przykłady obejmują Apple Push Notification service (APNs) firmy Apple i usługi Firebase Cloud Messaging (FCM) dla usług Google. Notification Hubs wypchnięć powiadomienia podzielone między wiele instancji rejestracji. Usługa Notification Hubs uwierzytelnia się za pomocą usługi powiadomień wypychanych odpowiednich, które są oparte na poświadczenia, które można ustawić w witrynie Azure portal w obszarze **Konfigurowanie Centrum powiadomień**. Powiadomienie wypychane powiadomienia dotyczące usług następnie będzie przekazywać do odpowiednich *urządzeń klienckich*.

Mechanizm dostarczania powiadomień o ostatnim etapie odbywa się między usługą powiadomień wypychanych platformy i urządzenia. Jedną z czterech głównych składników w procesie powiadomień wypychanych (klient, zaplecza aplikacji, usługi Notification Hubs i usługą powiadomień wypychanych platformy) może spowodować powiadomień można go porzucić. Aby uzyskać więcej informacji na temat architektury usługi Notification Hubs, zobacz [Notification Hubs — omówienie].

Niedostarczenie powiadomień mogą wystąpić podczas początkowego testu i przemieszczania fazy. Porzucone powiadomienia na tym etapie może wskazywać problem z konfiguracją. Jeśli wystąpi błąd w celu dostarczania powiadomień w środowisku produkcyjnym, niektóre lub wszystkie powiadomienia mogą być opuszczane. W tym przypadku jest wskazywany bardziej aplikacji lub messaging wzorzec problem.

Następna sekcja patrzy na scenariusze, w których powiadomienia mogą być opuszczane, od wspólnego bardziej rzadkie.

## <a name="notification-hubs-misconfiguration"></a>Notification Hubs z błędną konfiguracją

Pomyślnie wysyłać powiadomienia usługi powiadomień wypychanych odpowiednich, usługi Notification Hubs musi uwierzytelniać w kontekście dewelopera aplikacji. W tym celu deweloper tworzy konto dewelopera z odpowiednich platform (Google, firmy Apple, Windows i tak dalej). Następnie Deweloper rejestruje swoich aplikacji dzięki platformie uzyskują poświadczeń.

Poświadczenia platformy należy dodać do witryny Azure portal. Jeśli żadne powiadomienia się połączyć urządzenie, pierwszym krokiem powinno być upewnij się, że poprawne poświadczenia są konfigurowane w usłudze Notification Hubs. Poświadczenia muszą odpowiadać aplikacji, która jest tworzona przy użyciu konta dewelopera specyficzne dla platformy.

Instrukcje krok po kroku ukończyć ten proces, zobacz [Rozpoczynanie pracy z usługą Azure Notification Hubs].

Poniżej przedstawiono kilka typowych błędów konfiguracji, aby wyszukać:

**Ogólne:**

    * Upewnij się, że nazwę Centrum powiadomień (bez błędów) jest taka sama w każdym z następujących lokalizacji:
        * Gdzie należy zarejestrować od klienta.
        * Gdzie wysyłanie powiadomień z zaplecza.
        * Którego skonfigurowano poświadczenia usługi powiadomień wypychanych.
    * Upewnij się, że używasz ciągi konfiguracji sygnatury dostępu współdzielonego poprawne na kliencie i zapleczu aplikacji. Ogólnie rzecz biorąc, należy użyć **DefaultListenSharedAccessSignature** na komputerze klienckim i **DefaultFullSharedAccessSignature** aplikacji zaplecza (przyznaje uprawnienia do wysyłania powiadomień do Usługa Notification Hubs).

**Konfiguracja usługi APNs:**

    You must maintain two different hubs: one hub for production, and another hub for testing. This means that you must upload the certificate that you use in a sandbox environment to a separate hub than the certificate and hub that you are going to use in production. Don't try to upload different types of certificates to the same hub. This might cause notification failures.

    If you inadvertently upload different types of certificates to the same hub, we recommend that you delete the hub and start fresh with a new hub. If for some reason you can't delete the hub, at a minimum, you must delete all the existing registrations from the hub.

**Konfiguracja usługi FCM:**

    1. Upewnij się, że *klucz serwera* uzyskany od Firebase pasuje do klucza serwera, który został zarejestrowany w witrynie Azure portal.

    ![Klucz serwera usługi firebase][3]

    2. Upewnij się, że skonfigurowano **identyfikator projektu** na komputerze klienckim. Można uzyskać wartość **identyfikator projektu** na pulpicie nawigacyjnym usługi Firebase.

    ![Identyfikator projektu firebase][1]

## <a name="application-issues"></a>Problemy z aplikacją

**Znaczniki i wyrażenia tagu:**

    If you use tags or tag expressions to segment your audience, it's possible that when you send the notification, no target is found based on the tags or tag expressions that you specify in your send call.

    Review your registrations to ensure that there are matching tags when you send a notification. Then, verify the notification receipt only from the clients that have those registrations.

    As an example, if all your registrations with Notification Hubs were made by using the tag "Politics" and you send a notification with the tag "Sports," the notification isn't sent to any device. A complex case might involve tag expressions in which you registered by using "Tag A" OR "Tag B," but while sending notifications, you target "Tag A && Tag B." In the self-diagnosis tips section later in the article, we show you how to review your registrations and their tags.

** Problemy dotyczące szablonu: **

    If you use templates, ensure that you follow the guidelines described in [Templates].

**Nieprawidłowy rejestracji:**

    If the notification hub was configured correctly, and if any tags or tag expressions were used correctly, valid targets are found. Notifications should be sent to these targets. The Notification Hubs service then fires off several processing batches in parallel. Each batch sends messages to a set of registrations.

    > [!NOTE]
    > Because processing is performed in parallel, the order in which the notifications are delivered is not guaranteed.

    Notification Hubs is optimized for an "at-most once" message delivery model. We attempt deduplication, so that no notifications are delivered more than once to a device. To ensure this, we check registrations and ensure that only one message is sent per device identifier before the message is sent to the push notification service.

    As each batch is sent to the push notification service, which in turn is accepting and validating the registrations, it's possible that the push notification service will detect an error with one or more of the registrations in a batch. In this case, the push notification service returns an error to Notification Hubs, and the process stops. The push notification service drops that batch completely. This is especially true with APNS, which uses a TCP stream protocol.

    We are optimized for at-most once delivery. But in this case, the faulting registration is removed from the database. Then, we retry notification delivery for the rest of the devices in that batch.

    To get more error information about the failed delivery attempt against a registration, you can use the Notification Hubs REST APIs [Per Message Telemetry: Get Notification Message Telemetry](https://msdn.microsoft.com/library/azure/mt608135.aspx) and [PNS feedback](https://msdn.microsoft.com/library/azure/mt705560.aspx). For sample code, see the [Send REST example](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample).

## <a name="push-notification-service-issues"></a>Problemy z usługą powiadomień wypychanych

Po otrzymaniu komunikatu powiadomienia przez usługi powiadomień wypychanych platformy jest odpowiedzialność usługi powiadomień wypychanych w celu dostarczenia powiadomienia do urządzenia. W tym momencie usługa Notification Hubs jest poza obraz, a nie ma kontroli nad kiedy lub jeśli powiadomienia są dostarczane do urządzenia.

Ponieważ usług powiadomień platformy są niezawodne, powiadomienia zwykle do zarządzanego urządzenia z usługi powiadomień wypychanych w ciągu kilku sekund. Jeśli ograniczania usługi powiadomień wypychanych usługi Notification Hubs ma zastosowanie strategii wykładniczego wycofywania. Jeśli Usługa powiadomień wypychanych pozostaje nieosiągalny przez 30 minut, mamy zasady mające na celu zakończenie okresu ważności i trwale usunąć te komunikaty.

Jeśli Usługa powiadomień wypychanych, próby dostarczenia powiadomienia, ale urządzenie jest w trybie offline, powiadomienia są przechowywane przez usługi powiadomień wypychanych na pewien czas. Powiadomienia są dostarczane do urządzenia, gdy urządzenie stanie się dostępna.

Dla każdej aplikacji znajduje się tylko jedno powiadomienie ostatnie. Jeśli wiele powiadomień są wysyłane, gdy urządzenie jest w trybie offline, każde nowe powiadomienie powoduje, że wcześniejsze powiadomienie, aby zostać odrzucone. Przechowywanie tylko najnowsze powiadomień jest określany jako *łączącego powiadomienia* w usługi APNs i *zwijanie* w FCM (który jest używany klucz zwijanie). Jeśli urządzenie będzie w trybie offline przez długi czas, wszelkie powiadomienia, które były przechowywane na urządzeniu zostaną odrzucone. Aby uzyskać więcej informacji, zobacz [Omówienie usługi APNs] i [Temat wiadomości FCM].

Usługa Azure Notification Hubs można przekazać łączącego klucza przy użyciu nagłówka HTTP za pomocą ogólnego interfejsu API SendNotification. Na przykład dla zestawu .NET SDK, możesz użyć `SendNotificationAsync`. Interfejs API SendNotification pobiera również nagłówki HTTP, które są przekazywane jako — jest odpowiednich push notification Service.

## <a name="self-diagnosis-tips"></a>Wskazówki dotyczące Autodiagnostyki

Poniżej przedstawiono ścieżki do diagnozowania przyczyny porzucone powiadomienia usługi Notification Hubs:

### <a name="verify-credentials"></a>Weryfikowanie poświadczeń

**Portal dla deweloperów usługi powiadomień wypychanych:**

Sprawdź poświadczenia w portalu dla deweloperów programu odpowiednich wypychania powiadomień service (APNs, FCM, Windows Notification Service i tak dalej). Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z usługą Azure Notification Hubs].

**Witryna Azure portal:**

Aby przejrzeć i pasować do poświadczeń z tymi, które są uzyskiwane z portalu wypychania powiadomień usługi dla deweloperów w witrynie Azure portal przejdź do **zasady dostępu** kartę.

![Zasady dostępu do witryny Azure portal][4]

### <a name="verify-registrations"></a>Weryfikowanie rejestracji

**Visual Studio:**

Jeśli używasz programu Visual Studio do tworzenia aplikacji możesz połączyć się na platformie Azure za pomocą Eksploratora serwera do przeglądania i zarządzania wielu usług platformy Azure, w tym usługi Notification Hubs. Jest to szczególnie przydatne w środowisku tworzenia i testowania.

![Visual Studio Server Explorer][9]

Można wyświetlać i zarządzać wszystkich rejestracji w Centrum, pogrupowane według platformy natywnej lub szablon rejestracji, wszystkie tagi, identyfikator usługi powiadomień wypychanych, identyfikator rejestracji i datę wygaśnięcia. Można również edytować rejestracji na tej stronie. Jest to szczególnie przydatne do edycji, tagi.

![Rejestracje urządzeń w programie Visual Studio][8]

> [!NOTE]
> Edytowanie rejestracji tylko podczas tworzenia i testowania, a także z ograniczoną liczbą rejestracji przy użyciu programu Visual Studio. Jeśli musisz zmodyfikować rejestracje w trybie zbiorczym, rozważ użycie opcji eksportowania i zaimportuj rejestracji funkcji opisanych w [eksportowanie i modyfikowanie rejestracji zbiorczej](https://msdn.microsoft.com/library/dn790624.aspx).

**Eksplorator usługi Service Bus:**

Wielu klientów używa [Eksplorator usługi Service Bus] do przeglądania i zarządzania ich Centrum powiadomień. Eksplorator usługi Service Bus to projekt typu open source. Aby uzyskać przykłady, zobacz [Kod Eksploratora usługi Service Bus].

### <a name="verify-message-notifications"></a>Sprawdź powiadomienia

 **Witryna Azure portal:**

Aby wysłać powiadomienie testowe do klientów bez konieczności usługi zaplecza działanie, w obszarze **pomoc techniczna i rozwiązywanie problemów**, wybierz opcję **wysyłanie testowe**.

![Testowanie funkcji wysyłania na platformie Azure][7]

**Visual Studio:**

Można również wysłać powiadomień dotyczących testów z programu Visual Studio.

![Testowanie funkcji wysyłania w programie Visual Studio][10]

Aby uzyskać więcej informacji na temat używania usługi Notification Hubs za pomocą Eksploratora serwera w usłudze Visual Studio zobacz następujące artykuły:

* [Wyświetlanie rejestracji urządzeń do usługi notification hubs]
* [Szczegółowe informacje: Visual Studio 2013 Update 2 RC i Azure SDK 2.3]
* [Ogłaszamy wydanie programu Visual Studio 2013 Update 3 i Azure SDK 2.4]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Powiadomienia dotyczące niepowodzenia debugowania i przejrzyj wynik powiadomienia

**`EnableTestSend` Właściwość:**

Po wysłaniu początkowo powiadomienie za pośrednictwem usługi Notification Hubs, powiadomienia znajduje się w kolejce do przetwarzania w usłudze Notification Hubs. Usługa Notification Hubs określa prawidłowe elementy docelowe, a następnie wysyła powiadomienie push notification Service. Jeśli używasz interfejsu API REST, ani żadnego z zestawów SDK klienta zwracany pomyślnego wywołania wysyłania oznacza, że tylko czy wiadomość została pomyślnie umieszczona w kolejce przy użyciu usługi Notification Hubs. Nie masz wgląd co się dzieje po usługę Notification Hubs ostatecznie wysłała komunikat do usługi powiadomień wypychanych.

Jeśli powiadomienia nie dotrze na urządzeniu klienckim, jest to możliwe, że wystąpił błąd podczas próby dostarczenia komunikatu do usługi powiadomień wypychanych usługi Notification Hubs. Na przykład rozmiar ładunku może przekracza maksymalną dozwoloną przez usługi powiadomień wypychanych lub poświadczenia skonfigurowane w usłudze Notification Hubs może być nieprawidłowy.

Aby uzyskać wgląd w wypychanych błędy usługi powiadomień, możesz użyć [EnableTestSend] właściwości. Ta właściwość jest automatycznie włączone, gdy wysyłanie wiadomości testowych z portalu lub klienta programu Visual Studio. Korzystać z tej właściwości, aby wyświetlić szczegółowe informacje o debugowaniu. Można również użyć właściwości, za pośrednictwem interfejsów API. Obecnie można go używać w zestawie SDK platformy .NET. Po pewnym czasie zostaną dodane do wszystkich zestawów SDK klienta.

Do użycia `EnableTestSend` właściwości za pomocą wywołania REST, Dołącz parametr ciągu zapytania o nazwie *test* w celu wywołania wysyłania. Na przykład:

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

**Przykład (.NET SDK):**

Oto przykład przy użyciu zestawu .NET SDK, można wysłać powiadomienia wyskakującego natywnego (wyskakujące):

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

Po zakończeniu wykonywania `result.State` po prostu stany `Enqueued`. Wynikami niepodania wgląd co się stało z usługi powiadomień wypychanych.

Następnie możesz użyć `EnableTestSend` właściwość typu Boolean. Użyj `EnableTestSend` właściwości podczas inicjowania `NotificationHubClient` Aby uzyskać szczegółowy stan wypychania błędów usługi powiadomień, które występują, gdy powiadomienie jest wysyłane. Wywołanie wysyłania zajmuje dodatkowy czas zwrotu, ponieważ jest on zwracany tylko wtedy, gdy usługa Notification Hubs dostarczał powiadomienia usługi powiadomień wypychanych, aby określić wyniki.

```csharp
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }
```

**Przykładowe dane wyjściowe:**

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

Ten komunikat oznacza, że nieprawidłowe poświadczenia są konfigurowane w usłudze Notification Hubs albo występuje problem z rejestracji w Centrum. Firma Microsoft zaleca odstranit tuto registraci i umożliwić klientowi odtworzenie rejestracji przed wysłaniem wiadomości.

> [!NOTE]
> Korzystanie z `EnableTestSend` właściwość jest mocno ograniczona. Użyj tej opcji tylko w środowisku tworzenia i testowania, a także z ograniczonym zestawem rejestracji. Wysyłamy powiadomienia debugowania tylko 10 urządzeń. Mamy także limit przetwarzania wysyła debugowania do 10 na minutę.

### <a name="review-telemetry"></a>Przejrzyj dane telemetryczne

**Użyj witryny Azure portal:**

W portalu możesz uzyskać szybki przegląd wszystkich działań w Centrum powiadomień.

1. Na **Przegląd** kartę, zobaczysz zagregowany widok rejestracji, powiadomienia i błędy przez platformę.

    ![Pulpit nawigacyjny przeglądu centra powiadomień][5]

2. Na **Monitor** karcie, można dodać wiele innych metryk specyficzne dla platformy, aby dowiedzieć się więcej. Możesz obejrzeć specjalnie wszelkie błędy związane z usługą powiadomień wypychanych, które są zwracane, gdy próbuje wysłać powiadomienie do usługi powiadomień wypychanych usługi Notification Hubs.

    ![Dziennik aktywności platformy Azure portal][6]

3. Rozpocznij od przejrzenia **komunikaty przychodzące**, **operacje rejestracji**, i **powiadomienia zakończone powodzeniem**. Następnie przejdź do karty na platformie, aby przejrzeć błędy, które są specyficzne dla usługi powiadomień wypychanych.

4. Jeżeli ustawienia uwierzytelniania dla Centrum powiadomień są nieprawidłowe, komunikat **błąd uwierzytelniania systemu powiadomień platformy** pojawia się. To jest dobrym wskaźnikiem w celu sprawdzenia poświadczeń usługi push notification.

* **Dostęp programowy**

Aby uzyskać więcej informacji na temat dostęp programowy zobacz [dostęp programowy telemetrii]

> [!NOTE]
> Kilka funkcji związane z telemetrią, takich jak eksportowanie i importowanie rejestracji i dane telemetryczne dostęp za pośrednictwem interfejsów API, są dostępne tylko w warstwie usług standardowa na. Jeśli spróbujesz użyć tych funkcji, bezpłatna lub podstawowa warstwę usługi, zostanie wyświetlony komunikat wyjątku, jeśli używasz zestawu SDK i wystąpienia błędu HTTP 403 (zabronione) Jeśli korzystasz z funkcji bezpośrednio za pośrednictwem interfejsów API REST.
>
> Korzystanie z funkcji powiązanych danych telemetrycznych, najpierw upewnij się, w witrynie Azure portal używają warstwie usługi standardowa.  

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/FCMConfigure.png
[3]: ./media/notification-hubs-diagnosing/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png

<!-- LINKS -->
[Notification Hubs — omówienie]: notification-hubs-push-notification-overview.md
[Rozpoczynanie pracy z usługą Azure Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Templates]: https://msdn.microsoft.com/library/dn530748.aspx
[Omówienie usługi APNs]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[Temat wiadomości FCM]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: http://msdn.microsoft.com/library/dn790624.aspx
[Eksplorator usługi Service Bus]: https://msdn.microsoft.com/library/dn530751.aspx#sb_explorer
[Kod Eksploratora usługi Service Bus]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[Wyświetlanie rejestracji urządzeń do usługi notification hubs]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[Szczegółowe informacje: Visual Studio 2013 Update 2 RC i Azure SDK 2.3]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[Ogłaszamy wydanie programu Visual Studio 2013 Update 3 i Azure SDK 2.4]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Dostęp programowy telemetrii]: http://msdn.microsoft.com/library/azure/dn458823.aspx
