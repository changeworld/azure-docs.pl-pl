---
title: Diagnozowanie porzuconymi powiadomieniami w usłudze Azure Notification Hubs
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
ms.date: 04/04/2019
ms.author: jowargo
ms.openlocfilehash: eebf9ef63a8622c4cc431322b786fdf30f6352fe
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925822"
---
# <a name="diagnose-dropped-notifications-in-azure-notification-hubs"></a>Diagnozowanie porzuconymi powiadomieniami w usłudze Azure Notification Hubs

Często zadawane pytania dotyczące usługi Azure Notification Hubs jest jak rozwiązywać problemy podczas powiadomienia z aplikacji nie są wyświetlane na urządzeniach klienckich. Klienci chcą wiedzieć, gdzie i dlaczego powiadomienia zostały usunięte, a także sposób rozwiązać ten problem. W tym artykule identyfikuje Dlaczego powiadomienia porzucane może lub nie można odebrać przez urządzenia. Wyjaśniono również sposób ustalania głównej przyczyny.

Koniecznie najpierw zrozumieć, jak usługa Notification Hubs wypycha powiadomienia do urządzenia.

![Architektura centra powiadomień][0]

W ramach przepływu powiadomień typowe Wyślij komunikat jest wysyłany z *zapleczu aplikacji* usługę Notification hubs. Usługa Notification Hubs przetwarza wszystkie rejestracje. Uwzględnia ona skonfigurowanych znaczników i wyrażeń tagów, aby ustalić cele. Obiekty docelowe są rejestracji, które należy do odbierania powiadomień wypychanych. Tyto registrace może obejmować jedną z naszych obsługiwanych platform: Android, Baidu (urządzenia z systemem Android w Chinach), systemu operacyjnego ogień (Amazon) dla systemu iOS, Windows i Windows Phone.

Za pomocą ustanowionych celów usługi Notification Hubs wypycha powiadomienia do *push notification service* dla platformy urządzenia. Przykłady obejmują Apple Push Notification service (APNs) firmy Apple i usługi Firebase Cloud Messaging (FCM) dla usług Google. Notification Hubs wypchnięć powiadomienia podzielone między wiele instancji rejestracji. Uwierzytelnia się za pomocą usługi powiadomień wypychanych odpowiednich, na podstawie poświadczeń ustawiona w witrynie Azure portal w obszarze **Konfigurowanie Centrum powiadomień**. Powiadomienie wypychane powiadomienia dotyczące usług następnie będzie przekazywać do odpowiednich *urządzeń klienckich*.

Ostatnim etapie mechanizm dostarczania powiadomień to od usługi powiadomień wypychanych platformy i urządzenia. Mechanizm dostarczania powiadomień o może zakończyć się niepowodzeniem w dowolnym z czterech etapów w procesie powiadomień wypychanych (klient, zaplecza aplikacji, usługi Notification Hubs i usługi powiadomień wypychanych platformy). Aby uzyskać więcej informacji na temat architektury usługi Notification Hubs, zobacz [Notification Hubs — omówienie].

Błąd podczas dostarczania powiadomień mogą wystąpić podczas początkowego testu i przemieszczania fazy. Porzucone powiadomienia na tym etapie może wskazywać problem z konfiguracją. Jeśli wystąpi awaria dostarczać powiadomienia w środowisku produkcyjnym, niektóre lub wszystkie powiadomienia mogą być opuszczane. W tym przypadku jest wskazywany bardziej aplikacji lub messaging wzorzec problem.

Następna sekcja patrzy na scenariusze, w których powiadomienia mogą być opuszczane, od wspólnego rzadkie.

## <a name="notification-hubs-misconfiguration"></a>Notification Hubs z błędną konfiguracją ##

Do wysyłania powiadomień do usługi powiadomień wypychanych odpowiednich, Notification Hubs musi uwierzytelniać w kontekście aplikacji. Konto dewelopera należy utworzyć przy użyciu usługi powiadomień platformy docelowej (firmy Microsoft, firmy Apple, Google itp.). Następnie należy zarejestrować aplikację w usłudze systemu operacyjnego, gdzie można uzyskać tokenu lub klucza, których używasz do pracy z systemem powiadomień platformy docelowej.

Poświadczenia platformy należy dodać do witryny Azure portal. Jeśli żadne powiadomienia się połączyć urządzenie, pierwszym krokiem jest upewnij się, że poprawne poświadczenia są konfigurowane w usłudze Notification Hubs. Poświadczenia muszą odpowiadać aplikacji, która jest tworzona przy użyciu konta dewelopera specyficzne dla platformy.

Instrukcje krok po kroku ukończyć ten proces, zobacz [Rozpoczynanie pracy z usługą Azure Notification Hubs].

Poniżej przedstawiono kilka typowych błędów konfiguracji, aby wyszukać:

### <a name="notification-hub-name-location"></a>Lokalizacja nazwy Centrum powiadomień

Upewnij się, że nazwę Centrum powiadomień (bez błędów) jest taka sama w każdym z następujących lokalizacji:
   * Gdy zarejestrujesz od klienta
   * Gdzie wysyłanie powiadomień z zaplecza
   * Którego skonfigurowano poświadczenia usługi powiadomień wypychanych

Upewnij się, użyj ciągi konfiguracji sygnatury dostępu współdzielonego poprawne na komputerze klienckim i kończyć aplikację ponownie. Ogólnie rzecz biorąc, należy użyć **DefaultListenSharedAccessSignature** na komputerze klienckim i **DefaultFullSharedAccessSignature** aplikacji zaplecza. Spowoduje to przydzielenie uprawnień do wysyłania powiadomień do usługi Notification Hubs.

### <a name="apn-configuration"></a>Konfiguracja elementu APN ###

Należy zachować dwa różne centra: jeden dla produkcji i inny wpis dla testowania. Musisz przekazać certyfikat, którego używasz w środowisku piaskownicy w osobnym Centrum niż certyfikatu/koncentratora, które będą używane w środowisku produkcyjnym. Nie należy próbować przekazać różne typy certyfikatów do tego samego Centrum. Spowoduje błędy powiadomień.

Jeśli załadujesz przypadkowo różne typy certyfikatów do tego samego Centrum, należy usunąć koncentratora i zacznij od zera za pomocą nowego Centrum. Jeśli z jakiegoś powodu nie można usunąć koncentratora, co najmniej należy usunąć wszystkie istniejące rejestracje z koncentratora.

### <a name="fcm-configuration"></a>Konfiguracja usługi FCM ###

1. Upewnij się, że *klucz serwera* uzyskany od dopasowania Firebase klucz serwera zarejestrowany w witrynie Azure portal.

   ![Klucz serwera usługi firebase][3]

2. Upewnij się, że skonfigurowano **identyfikator projektu** na komputerze klienckim. Można uzyskać wartość **identyfikator projektu** na pulpicie nawigacyjnym usługi Firebase.

   ![Identyfikator projektu firebase][1]

## <a name="application-issues"></a>Problemy z aplikacją ##

### <a name="tags-and-tag-expressions"></a>Znaczniki i wyrażenia tagu ###

Jeśli używasz tagów lub wyrażenia tagu do segmentowania odbiorców, jest możliwe, podczas wysyłania powiadomienia, żadne miejsce docelowe nie zostanie znaleziony. Ten błąd jest oparty na określonymi tagami lub wyrażenia tagu w wywołania wysyłania.

Przejrzyj rejestracje w taki sposób, aby upewnić się, że znaczniki odpowiadają podczas wysyłania powiadomienia. Następnie sprawdź odbieranie powiadomień od klientów, które mają te rejestracji.

Na przykład załóżmy, że wszystkie rejestracje z usługą Notification Hubs używany jest tag "Polityka". Jeśli następnie należy wysłać powiadomienia za pomocą znacznika "Sport", powiadomienia nie zostaną wysłane na dowolnym urządzeniu. Złożonych przypadkach może obejmować wyrażenia tagu, na którym zarejestrowany przez tagu "A" *lub* "Tag B", ale docelowe "Tag & & Tag B." Sekcji wskazówki dotyczące Autodiagnostyki w dalszej części tego artykułu dowiesz się, jak przeglądać rejestracje i ich tagów.

### <a name="template-issues"></a>Problemy dotyczące szablonu ###

Jeśli korzystasz z szablonów, upewnij się, postępuj zgodnie z wytycznymi opisany w [szablony].

### <a name="invalid-registrations"></a>Nieprawidłowy rejestracji ###

Jeśli Centrum powiadomień zostały poprawnie skonfigurowane i znaczników lub wyrażenia tagu zostały prawidłowo wykorzystywane, znajdują się elementy docelowe prawidłowe. Powiadomienia mają być wysyłane do następujących elementów docelowych. Usługa Notification Hubs, a następnie uruchamiany off partie przetwarzania równoległego. Każda partia wysyła komunikaty do zestawu rejestracji.

> [!NOTE]
> Ponieważ usługa Notification Hubs przetwarza partie w sposób równoległy, nie jest gwarantowana kolejność, w której powiadomienia są dostarczane.

Usługa Notification Hubs jest zoptymalizowana pod kątem modelu dostarczania wiadomości "co większość jednokrotne". Staramy się deduplikacji, dzięki czemu żadne powiadomienia są dostarczane więcej niż jeden raz na urządzeniu. Rejestracje są sprawdzane w celu upewnij się, że tylko jeden komunikat jest wysyłany do jednego identyfikatora urządzenia przed wysłaniem ich do usługi powiadomień wypychanych.

Partie są wysyłane do usługi powiadomień wypychanych, która z kolei akceptuje i sprawdza poprawność rejestracji. W trakcie tego procesu jest możliwe, że usługi powiadomień wypychanych wykryje błąd związany z co najmniej jeden rejestracji w zadaniu wsadowym. Usługa powiadomień wypychanych następnie zwróci błąd usługi Notification Hubs i zatrzymuje proces. Usługa powiadomień wypychanych porzuca tej partii całkowicie. Jest to szczególnie istotne, za pomocą usługi APNs, który używa protokołu strumienia TCP.

W tym przypadku błędną rejestracji jest usuwany z bazy danych. Spróbuj możemy mechanizm dostarczania powiadomień dla pozostałych urządzeń w danej partii.

Aby uzyskać więcej informacji o błędzie o próbie dostarczania nie powiodło się dla rejestracji, możesz użyć interfejsów API REST dla centrów powiadomień [na Telemetrii komunikat: Pobierz dane telemetryczne komunikatu powiadomienia](https://msdn.microsoft.com/library/azure/mt608135.aspx) i [PNS, opinii](https://msdn.microsoft.com/library/azure/mt705560.aspx). Przykładowy kod, zobacz [przykład Wyślij REST](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/SendRestExample/).

## <a name="push-notification-service-issues"></a>Problemy z usługą powiadomień wypychanych

Gdy Usługa powiadomień wypychanych otrzyma powiadomienie, zapewnia powiadomienie do urządzenia. W tym momencie usługa Notification Hubs nie ma kontroli nad dostarczania powiadomień na urządzeniu.

Ponieważ usług powiadomień platformy są niezawodne, powiadomienia zwykle udostępnianiem urządzeniom w ciągu kilku sekund. Jeśli ograniczania usługi powiadomień wypychanych usługi Notification Hubs ma zastosowanie strategii wykładniczego wycofywania. Usługa powiadomień wypychanych pozostaje nieosiągalny przez 30 minut, czy zasady mające na celu zakończenie okresu ważności i trwale usunąć komunikaty.

Jeśli usługi powiadomień wypychanych, próby dostarczenia powiadomienia, ale urządzenie jest w trybie offline, powiadomienia są przechowywane w usłudze powiadomień wypychanych. Jest on przechowywany przez ograniczony okres czasu. Powiadomienia są dostarczane do urządzenia, gdy urządzenie stanie się dostępna.

Każda aplikacja przechowuje tylko jedno powiadomienie ostatnie. Jeśli wiele powiadomień są wysyłane, gdy urządzenie jest w trybie offline, każde nowe powiadomienie powoduje, że ostatni z nich do usunięcia. Przechowywanie tylko najnowsze powiadomień jest wywoływana *łączenie* w APNs i *zwijanie* w usługi FCM. (FCM używa klucza zwijanie). Gdy urządzenie będzie w trybie offline przez długi czas, powiadomienia, które były przechowywane na urządzeniu zostaną odrzucone. Aby uzyskać więcej informacji, zobacz [Omówienie usługi APNs] i [Temat wiadomości FCM].

Usługa Notification Hubs można przekazać łączącego klucz za pośrednictwem nagłówek HTTP za pomocą ogólnego interfejsu API SendNotification. Na przykład dla zestawu .NET SDK, możesz użyć `SendNotificationAsync`. Interfejs API SendNotification ma również nagłówki HTTP, które są przekazywane jako usługi powiadomień wypychanych odpowiednich.

## <a name="self-diagnosis-tips"></a>Wskazówki dotyczące Autodiagnostyki

Poniżej przedstawiono ścieżki, aby zdiagnozować jej główną przyczynę porzucone powiadomienia usługi Notification Hubs.

### <a name="verify-credentials"></a>Weryfikowanie poświadczeń ###

#### <a name="push-notification-service-developer-portal"></a>Portal dla deweloperów usługi powiadomień wypychanych ####

Sprawdź poświadczenia w portalu dla deweloperów programu odpowiednich wypychania powiadomień service (APNs, FCM, Windows Notification Service i tak dalej). Aby uzyskać więcej informacji, zobacz [Samouczek: wysyłanie powiadomień do aplikacji platformy uniwersalnej systemu Windows przy użyciu usługi Azure Notification Hubs](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification).

#### <a name="azure-portal"></a>Azure Portal ####

Aby przejrzeć i pasować do poświadczeń z identyfikatorami uzyskanymi z portalu dla deweloperów usługi powiadomień wypychanych, przejdź do **zasady dostępu** kartę w witrynie Azure portal.

![Zasady dostępu do witryny Azure portal][4]

### <a name="verify-registrations"></a>Weryfikowanie rejestracji

#### <a name="visual-studio"></a>Visual Studio ####

W programie Visual Studio możesz się połączyć na platformie Azure za pomocą Eksploratora serwera do przeglądania i zarządzania wielu usług platformy Azure, w tym usługi Notification Hubs. Ten skrót jest szczególnie przydatne w przypadku środowiska projektowania i testowania.

![Visual Studio Server Explorer][9]

Można wyświetlać i zarządzać wszystkich rejestracji w Centrum. Rejestracje mogą zostać podzielone przez platformę, rejestracja natywnym, czy szablon, tag, identyfikator usługi powiadomień wypychanych, identyfikator rejestracji i datę wygaśnięcia. Można również edytować rejestracji na tej stronie. Jest to szczególnie przydatne w przypadku edytowania tagów.

Kliknij prawym przyciskiem myszy Centrum powiadomień w **Eksploratora serwera**i wybierz **diagnozowanie**. 

![Visual Studio Server Explorer: Diagnozowanie menu](./media/notification-hubs-diagnosing/diagnose-menu.png)

Zostanie wyświetlona następująca strona:

![Visual Studio: Diagnozowanie strony](./media/notification-hubs-diagnosing/diagnose-page.png)

Przełącz się do **rejestracje urządzeń** strony:

![Visual Studio: Rejestracje urządzeń](./media/notification-hubs-diagnosing/VSRegistrations.png)

Możesz użyć **wysyłanie testowe** strony, aby wysłać komunikatu z powiadomieniem testowym:

![Visual Studio: Wysyłanie testowe](./media/notification-hubs-diagnosing/test-send-vs.png)

> [!NOTE]
> Edytowanie rejestracji tylko podczas tworzenia/testowania i z ograniczoną liczbą rejestracji przy użyciu programu Visual Studio. Jeśli musisz zmodyfikować rejestracje w trybie zbiorczym, rozważ użycie opcji eksportowania i zaimportuj rejestracji funkcji opisanych w [How to: Eksportowanie i modyfikowanie rejestracji zbiorczej](https://msdn.microsoft.com/library/dn790624.aspx).

#### <a name="service-bus-explorer"></a>Eksplorator usługi Service Bus ####

Wielu klientów używa [Eksploratora usługi Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer) do przeglądania i zarządzania ich usługi notification hubs. Eksplorator usługi Service Bus to projekt typu open source. 

### <a name="verify-message-notifications"></a>Sprawdź powiadomienia

#### <a name="azure-portal"></a>Azure Portal ####

Aby wysłać powiadomienie testowe do klientów bez konieczności usługi zaplecza działanie, w obszarze **pomoc techniczna i rozwiązywanie problemów**, wybierz opcję **wysyłanie testowe**.

![Testowanie funkcji wysyłania na platformie Azure][7]

#### <a name="visual-studio"></a>Visual Studio ####

Można również wysłać powiadomień dotyczących testów z programu Visual Studio.

![Testowanie funkcji wysyłania w programie Visual Studio][10]

Aby uzyskać więcej informacji na temat używania usługi Notification Hubs za pomocą Eksploratora serwera w usłudze Visual Studio zobacz następujące artykuły:

* [Jak wyświetlić rejestracje urządzeń do usługi notification hubs](https://docs.microsoft.com/previous-versions/windows/apps/dn792122(v=win.10))
* [Szczegółowe informacje: Visual Studio 2013 Update 2 RC i Azure SDK 2.3]
* [Ogłaszamy wydanie programu Visual Studio 2013 Update 3 i Azure SDK 2.4]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Powiadomienia dotyczące niepowodzenia debugowania i przejrzyj wynik powiadomienia

#### <a name="enabletestsend-property"></a>Atrybut EnableTestSend właściwości ####

Podczas wysyłania powiadomienia za pośrednictwem usługi Notification Hubs, powiadomienia początkowo znajduje się w kolejce. Usługa Notification Hubs określa prawidłowe elementy docelowe, a następnie wysyła powiadomienie push notification Service. Jeśli używasz interfejsu API REST, ani żadnego z zestawów SDK klienta zwracany wywołania wysyłania oznacza, że tylko czy komunikat jest umieszczane w kolejce przy użyciu usługi Notification Hubs. Go nie zapewniają wgląd w co się dzieje po usługę Notification Hubs po pewnym czasie wysyłane powiadomienie push notification Service.

Jeśli powiadomienia nie dotrze na urządzeniu klienckim, może wystąpić błąd podczas próby dostarczenia go do usługi powiadomień wypychanych usługi Notification Hubs. Na przykład rozmiar ładunku może przekracza maksymalną dozwoloną przez usługi powiadomień wypychanych lub poświadczenia skonfigurowane w usłudze Notification Hubs może być nieprawidłowy.

Aby uzyskać wgląd w wypychanych błędy usługi powiadomień, możesz użyć [EnableTestSend] właściwości. Ta właściwość jest automatycznie włączone, gdy wysyłanie wiadomości testowych z portalu lub klienta programu Visual Studio. Ta właściwość umożliwia Zobacz szczegółowe informacje o debugowaniu i również za pośrednictwem interfejsów API. Obecnie można go używać w zestawie SDK platformy .NET. Jego zostaną dodane do wszystkich zestawów SDK klienta po pewnym czasie.

Do użycia `EnableTestSend` właściwości za pomocą wywołania REST, Dołącz parametr ciągu zapytania o nazwie *test* w celu wywołania wysyłania. Na przykład:

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

#### <a name="net-sdk-example"></a>Przykładowy zestaw SDK platformy .NET ####

Oto przykład przy użyciu zestawu .NET SDK, można wysłać powiadomienia wyskakującego natywnego (wyskakujące):

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

Po zakończeniu wykonywania `result.State` po prostu stany `Enqueued`. Wynikami niepodania wgląd co się stało z usługi powiadomień wypychanych.

Następnie możesz użyć `EnableTestSend` właściwość typu Boolean. Użyj `EnableTestSend` właściwości podczas inicjowania `NotificationHubClient` Aby uzyskać szczegółowy stan wypychania błędów usługi powiadomień, które występują, gdy powiadomienie jest wysyłane. Wywołanie wysyłania zajmuje dodatkowy czas zwrotu, ponieważ najpierw musi usługi Notification Hubs w celu dostarczenia powiadomienia push notification Service.

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

#### <a name="sample-output"></a>Przykładowe dane wyjściowe ####

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

Ten komunikat oznacza, że skonfigurowane w usłudze Notification Hubs poświadczenia są nieprawidłowe albo że występuje problem z rejestracji w Centrum. Odstranit tuto registraci i pozwól klienta, ponownie utwórz rejestracji przed wysłaniem wiadomości.

> [!NOTE]
> Korzystanie z `EnableTestSend` właściwość jest mocno ograniczona. Użyj tej opcji tylko w środowisku tworzenia/testowania i z ograniczonym zestawem rejestracji. Debugowanie powiadomienia są wysyłane do urządzenia tylko 10. Ma to również limit przetwarzania wysyła debugowania, od 10 na minutę.

### <a name="review-telemetry"></a>Przejrzyj dane telemetryczne ###

#### <a name="azure-portal"></a>Azure Portal ####

W portalu możesz uzyskać szybki przegląd wszystkich działań w Centrum powiadomień.

1. Na **Przegląd** kartę, zobaczysz zagregowany widok rejestracji, powiadomienia i błędy przez platformę.

   ![Pulpit nawigacyjny przeglądu centra powiadomień][5]

2. Na **Monitor** karcie, można dodać wiele innych metryk specyficzne dla platformy, aby dowiedzieć się więcej. Możesz obejrzeć specjalnie błędów, które są zwracane, gdy próbuje wysłać powiadomienie do usługi powiadomień wypychanych usługi Notification Hubs.

   ![Dziennik aktywności platformy Azure portal][6]

3. Rozpocznij od przejrzenia **komunikaty przychodzące**, **operacje rejestracji**, i **powiadomienia zakończone powodzeniem**. Następnie przejdź do karty na platformie, aby przejrzeć błędy, które są specyficzne dla usługi powiadomień wypychanych.

4. Jeżeli ustawienia uwierzytelniania dla Centrum powiadomień są nieprawidłowe, komunikat **błąd uwierzytelniania systemu powiadomień platformy** pojawia się. Jest dobrym wskaźnikiem w celu sprawdzenia poświadczeń usługi push notification.

#### <a name="programmatic-access"></a>Dostęp programowy ####

Aby uzyskać więcej informacji na temat dostęp programowy zobacz [dostęp programowy](https://docs.microsoft.com/previous-versions/azure/azure-services/dn458823(v=azure.100)).

> [!NOTE]
> Kilka funkcji związane z telemetrią, takich jak eksportowanie i importowanie rejestracji i dane telemetryczne dostęp za pośrednictwem interfejsów API, są dostępne tylko w warstwie usług standardowa na. Próby użycia tych funkcji, bezpłatna lub podstawowa z warstwy usług, zapewnisz sobie komunikat o wyjątku używania zestawu SDK. Jeśli korzystasz z funkcji bezpośrednio za pośrednictwem interfejsów API REST, zostanie wyświetlony błąd HTTP 403 (zabronione).
>
> Korzystanie z funkcji powiązanych danych telemetrycznych, najpierw upewnij się, w witrynie Azure portal używasz warstwie usługi standardowa.  

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
[Szablony]: https://msdn.microsoft.com/library/dn530748.aspx
[Omówienie usługi APNs]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[Temat wiadomości FCM]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: https://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[View device registrations for notification hubs]: https://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[Szczegółowe informacje: Visual Studio 2013 Update 2 RC i Azure SDK 2.3]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[Ogłaszamy wydanie programu Visual Studio 2013 Update 3 i Azure SDK 2.4]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Programmatic telemetry access]: https://msdn.microsoft.com/library/azure/dn458823.aspx
