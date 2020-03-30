---
title: Diagnozowanie porzuconych powiadomień w Centrum powiadomień platformy Azure
description: Dowiedz się, jak zdiagnozować typowe problemy z porzuconych powiadomień w usłudze Azure Notification Hubs.
services: notification-hubs
documentationcenter: Mobile
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 02/25/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/04/2019
ms.openlocfilehash: 1f3c16e6fe1855cf7882d83e620c70d15ce3cb92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657587"
---
# <a name="diagnose-dropped-notifications-in-azure-notification-hubs"></a>Diagnozowanie porzuconych powiadomień w Centrum powiadomień platformy Azure

Typowe pytanie dotyczące usługi Azure Notification Hubs jest jak rozwiązywać problemy, gdy powiadomienia z aplikacji nie są wyświetlane na urządzeniach klienckich. Klienci chcą wiedzieć, gdzie i dlaczego powiadomienia zostały usunięte i jak rozwiązać problem. W tym artykule określono, dlaczego powiadomienia mogą zostać usunięte lub nie odebrane przez urządzenia. Wyjaśniono również, jak ustalić przyczynę.

Ważne jest, aby najpierw zrozumieć, w jaki sposób Centra powiadomień wypychają powiadomienia do urządzenia.

![Architektura centrów powiadomień][0]

W typowym przepływie powiadomień wysyłania wiadomość jest wysyłana z *zaplecza aplikacji* do centrum powiadomień. Centra powiadomień przetwarzają wszystkie rejestracje. Uwzględnia skonfigurowane tagi i wyrażenia znaczników w celu określenia obiektów docelowych. Obiekty docelowe to rejestracje, które muszą otrzymać powiadomienie wypychaniem. Te rejestracje mogą obejmować dowolną z naszych obsługiwanych platform: Android, Baidu (urządzenia z systemem Android w Chinach), Fire OS (Amazon) iOS, Windows i Windows Phone.

Po ustanowieniu obiektów docelowych centra powiadomień wypychają powiadomienia do *usługi powiadomień wypychanych* dla platformy urządzenia. Przykłady obejmują usługę powiadomień push (APN) firmy Apple dla systemów iOS i macOS oraz firebase cloud messaging (FCM) dla urządzeń z systemem Android. Centra powiadomień wypycha powiadomienia podzielone na wiele partii rejestracji. Uwierzytelnia się za pomocą odpowiedniej usługi powiadomień wypychanych, na podstawie poświadczeń ustawionych w witrynie Azure portal, w obszarze **Konfiguruj Centrum powiadomień**. Następnie usługa powiadomień wypychanych przekazuje powiadomienia do odpowiednich *urządzeń klienckich*.

Ostatni etap dostarczania powiadomień znajduje się między usługą powiadomień push platformy a urządzeniem. Dostarczanie powiadomień może zakończyć się niepowodzeniem na dowolnym z czterech etapów procesu powiadamiania wypychanego (klient, zaplecze aplikacji, centra powiadomień i usługa powiadomień wypychanych platformy). Aby uzyskać więcej informacji na temat architektury Centra powiadomień, zobacz [Omówienie centrów powiadomień].

Nie dostarczenie powiadomień może wystąpić podczas początkowej fazy testu/przemieszczania. Porzucone powiadomienia na tym etapie może wskazywać na problem z konfiguracją. Jeśli w produkcji wystąpi niepowodzenie dostarczania powiadomień, niektóre lub wszystkie powiadomienia mogą zostać usunięte. W tym przypadku jest wskazany problem z głębszą aplikacją lub wzorcem obsługi wiadomości.

W następnej sekcji omówiono scenariusze, w których powiadomienia mogą zostać usunięte, od wspólnych do rzadkich.

## <a name="notification-hubs-misconfiguration"></a>Nieprawidłowa konfiguracja centrów powiadomień

Aby wysyłać powiadomienia do odpowiedniej usługi powiadomień wypychanych, Centra powiadomień muszą uwierzytelniać się w kontekście aplikacji. Musisz utworzyć konto dewelopera w usłudze powiadamiania platformy docelowej (Microsoft, Apple, Google itp.). Następnie należy zarejestrować aplikację w system operacyjny, gdzie można uzyskać token lub klucz, który służy do pracy z docelowym pns.

Należy dodać poświadczenia platformy do witryny Azure portal. Jeśli żadne powiadomienia nie docierają do urządzenia, pierwszym krokiem jest upewnienie się, że poprawne poświadczenia są skonfigurowane w centrach powiadomień. Poświadczenia muszą być zgodne z aplikacją, która jest tworzona w ramach konta dewelopera specyficzne dla platformy.

Aby uzyskać instrukcje krok po kroku, aby zakończyć ten proces, zobacz [Wprowadzenie do usługi Azure Notification Hubs].

Oto kilka typowych błędów konfiguracji, aby sprawdzić:

### <a name="notification-hub-name-location"></a>Lokalizacja nazwy centrum powiadomień

Upewnij się, że nazwa centrum powiadomień (bez literówek) jest taka sama w każdej z tych lokalizacji:

* Gdzie rejestrujesz się od klienta
* Gdzie wysyłasz powiadomienia z zaplecza
* Gdzie skonfigurowano poświadczenia usługi powiadomień wypychanych

Upewnij się, że używasz poprawne ciągi konfiguracji podpisu dostępu udostępnionego na kliencie i zapleczu aplikacji. Ogólnie rzecz biorąc należy użyć **DefaultListenSharedAccessSignature** na kliencie i **DefaultFullSharedAccessSignature** na zapleczu aplikacji. To udziela uprawnień do wysyłania powiadomień do Centrów powiadomień.

### <a name="apn-configuration"></a>Konfiguracja APN

Należy zachować dwa różne koncentratory: jeden dla produkcji i inny do testowania. Certyfikat używany w środowisku piaskownicy należy przekazać do oddzielnego koncentratora niż certyfikat/koncentrator, który będzie używany w środowisku produkcyjnym. Nie próbuj przekazywać różnych typów certyfikatów do tego samego centrum. Spowoduje to błędy powiadomień.

Jeśli przypadkowo przekażesz różne typy certyfikatów do tego samego koncentratora, należy usunąć centrum i zacząć od nowa z nowym koncentratorem. Jeśli z jakiegoś powodu nie można usunąć koncentratora, należy przynajmniej usunąć wszystkie istniejące rejestracje z centrum.

### <a name="fcm-configuration"></a>Konfiguracja FCM

1. Upewnij się, że *klucz serwera* uzyskany z firebase jest zgodny z kluczem serwera zarejestrowanym w witrynie Azure portal.

   ![Klucz serwera Firebase][3]

2. Upewnij się, że skonfigurowano **identyfikator projektu** na kliencie. Wartość **identyfikatora projektu** można uzyskać z pulpitu nawigacyjnego Firebase.

   ![Identyfikator projektu Firebase][1]

## <a name="application-issues"></a>Problemy z aplikacją

### <a name="tags-and-tag-expressions"></a>Znaczniki i wyrażenia znaczników

Jeśli używasz tagów lub wyrażeń tagów do segmentacji odbiorców, możliwe, że podczas wysyłania powiadomienia nie zostanie znaleziony żaden cel. Ten błąd jest oparty na określonych tagach lub wyrażeniach tagów w wywołaniu wysyłania.

Przejrzyj swoje rejestracje, aby upewnić się, że tagi są zgodne podczas wysyłania powiadomień. Następnie sprawdź potwierdzenie powiadomienia tylko od klientów, którzy mają te rejestracje.

Załóżmy na przykład, że wszystkie rejestracje w Centrach powiadomień używają tagu "Polityka". Jeśli wyślesz powiadomienie z tagiem "Sport", powiadomienie nie zostanie wysłane na żadne urządzenie. Złożona sprawa może obejmować wyrażenia znaczników, w których został zarejestrowany przy użyciu "Tag A" *lub* "Tag B", ale kierowane "Tag A && Tag B." W dalszej części artykułu porady dotyczące autodiagnostyki pokazują, jak przeglądać rejestracje i ich tagi.

### <a name="template-issues"></a>Problemy z szablonami

Jeśli używasz szablonów, upewnij się, że postępuj zgodnie z wytycznymi opisanymi w [szablonach].

### <a name="invalid-registrations"></a>Nieprawidłowe rejestracje

Jeśli centrum powiadomień zostało poprawnie skonfigurowane i tagi lub wyrażenia tagów zostały użyte poprawnie, zostaną znalezione prawidłowe obiekty docelowe. Powiadomienia należy przesyłać do tych celów. Centra powiadomień następnie uruchamia kilka partii przetwarzania równolegle. Każda partia wysyła wiadomości do zestawu rejestracji.

> [!NOTE]
> Ponieważ Centra powiadomień przetwarza partie równolegle, kolejność, w której powiadomienia są dostarczane nie jest gwarantowana.

Centra powiadomień jest zoptymalizowany pod kątem modelu dostarczania komunikatów "at-most-once". Podejmujemy próbę deduplikacji, aby żadne powiadomienia nie były dostarczane więcej niż jeden raz do urządzenia. Rejestracje są sprawdzane, aby upewnić się, że tylko jedna wiadomość jest wysyłana na identyfikator urządzenia przed wysłaniem do usługi powiadomień wypychanych.

Każda partia jest wysyłana do usługi powiadomień wypychanych, która z kolei akceptuje i sprawdza poprawność rejestracji. Podczas tego procesu jest możliwe, że usługa powiadomień wypychanych wykryje błąd z co najmniej jedną rejestracją w partii. Usługa powiadomień wypychanych następnie zwraca błąd do Centrum powiadomień, a proces zatrzymuje się. Usługa powiadomień wypychanego całkowicie porzuca tę partię. Jest to szczególnie ważne w przypadku sieci APN, która używa protokołu strumienia TCP.

W takim przypadku rejestracja błędów jest usuwany z bazy danych. Następnie ponowimy próbę dostarczenia powiadomień dla pozostałych urządzeń w tej partii.

Aby uzyskać więcej informacji o błędzie dotyczących nieudanej próby dostarczenia względem rejestracji, można użyć interfejsów API REST centrum powiadomień [na telemetrii wiadomości: Pobierz dane telemetryczne komunikatu powiadomień](https://docs.microsoft.com/rest/api/notificationhubs/get-notification-message-telemetry) i [opinie pns](https://msdn.microsoft.com/library/azure/mt705560.aspx). Przykładowy kod można znaleźć w [przykładzie Wyślij REST](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/SendRestExample/).

## <a name="push-notification-service-issues"></a>Problemy z usługą powiadomień wypychaowych

Po odebraniu powiadomienia przez usługę powiadomień wypychanych, zostanie ona doręcza powiadomienie do urządzenia. W tym momencie Centra powiadomień nie ma kontroli nad dostarczaniem powiadomienia do urządzenia.

Ponieważ usługi powiadamiania platformy są niezawodne, powiadomienia mają tendencję do docierania do urządzeń w ciągu kilku sekund. Jeśli usługa powiadomień wypychanych jest ograniczanie, Notification Hubs stosuje wykładniczą strategii wycofywania. Jeśli usługa powiadomień wypychanych pozostaje nieosiągalna przez 30 minut, istnieje zasada wygaśnie i upuść wiadomości na stałe.

Jeśli usługa powiadomień wypychań próbuje dostarczyć powiadomienie, ale urządzenie jest w trybie offline, powiadomienie jest przechowywane przez usługę powiadomień wypychań. Jest przechowywany tylko przez ograniczony czas. Powiadomienie zostanie dostarczone do urządzenia, gdy urządzenie stanie się dostępne.

Każda aplikacja przechowuje tylko jedno ostatnie powiadomienie. Jeśli wiele powiadomień są wysyłane, gdy urządzenie jest w trybie offline, każde nowe powiadomienie powoduje, że ostatnie, które mają zostać odrzucone. Utrzymywanie tylko najnowsze powiadomienie jest nazywany *scalanie* w APN i *zwijanie* w FCM. (FCM używa zwijania klucza.) Gdy urządzenie pozostaje w trybie offline przez długi czas, powiadomienia, które zostały zapisane dla urządzenia są odrzucane. Aby uzyskać więcej informacji, zobacz [Omówienie sieci APN] i [komunikaty FCM .]

Za pomocą centrów powiadomień można przekazać klucz scalania za pośrednictwem nagłówka HTTP przy użyciu ogólnego interfejsu API sendnotification. Na przykład dla pliku .NET SDK `SendNotificationAsync`należy użyć pliku . Interfejs API sendnotification przyjmuje również nagłówki HTTP, które są przekazywane zgodnie z odpowiednią usługą powiadomień wypychanych.

## <a name="self-diagnosis-tips"></a>Wskazówki dotyczące autodiagnostyki

Poniżej przedstawiono ścieżki do diagnozowania głównej przyczyny porzuconych powiadomień w Centrach powiadomień.

### <a name="verify-credentials"></a>Weryfikowanie poświadczeń

#### <a name="push-notification-service-developer-portal"></a>Portal dewelopera usługi powiadomień wypychaowych

Sprawdź poświadczenia w odpowiednim portalu dewelopera usługi powiadomień wypychanych (APN, FCM, Usługa powiadamiania systemu Windows i tak dalej). Aby uzyskać więcej informacji, zobacz [Samouczek: Wysyłanie powiadomień do aplikacji platformy uniwersalnej systemu Windows przy użyciu usługi Azure Notification Hubs](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification).

#### <a name="azure-portal"></a>Portal Azure

Aby przejrzeć i dopasować poświadczenia do tych uzyskanych z portalu dewelopera usługi powiadomień wypychanych, przejdź do karty **Zasady dostępu** w witrynie Azure portal.

![Zasady dostępu usługi Azure portal][4]

### <a name="verify-registrations"></a>Weryfikowanie rejestracji

#### <a name="visual-studio"></a>Visual Studio

W programie Visual Studio można połączyć się z platformą Azure za pośrednictwem Eksploratora serwera, aby wyświetlić wiele usług platformy Azure i zarządzać nimi, w tym Centrum powiadomień. Ten skrót jest przede wszystkim przydatne dla środowiska rozwoju/testu.

![Eksplorator serwera programu Visual Studio][9]

![Eksplorator serwera](media/notification-hubs-push-notification-fixer/vsserverexplorer2.png)

Możesz wyświetlać i zarządzać wszystkimi rejestracjami w centrum. Rejestracje można podzielić na platformy, rejestrację natywną lub szablonową, tag, identyfikator usługi powiadomień wypychanych, identyfikator rejestracji i datę wygaśnięcia. Rejestrację można również edytować na tej stronie. Jest to szczególnie przydatne do edycji tagów.

Kliknij prawym przyciskiem myszy centrum powiadomień w **Eksploratorze serwera**i wybierz polecenie **Diagnozuj**. 

![Eksplorator serwera programu Visual Studio: menu Diagnozowanie](./media/notification-hubs-push-notification-fixer/diagnose-menu.png)

Zostanie wyświetlona następująca strona:

![Visual Studio: Diagnozowanie strony](./media/notification-hubs-push-notification-fixer/diagnose-page.png)

Przełącz się na stronę **Rejestracje urządzeń:**

![Visual Studio: Rejestracje urządzeń](./media/notification-hubs-push-notification-fixer/VSRegistrations.png)

Za pomocą strony **Wyślij test** można wysłać wiadomość z powiadomieniem testowym:

![Visual Studio: Wyślij test](./media/notification-hubs-push-notification-fixer/test-send-vs.png)

> [!NOTE]
> Program Visual Studio służy do edytowania rejestracji tylko podczas opracowywania/testowania i z ograniczoną liczbą rejestracji. Jeśli chcesz zbiorczo edytować rejestracje, rozważ użycie funkcji rejestracji eksportu i importu opisanych w [trybie jak: Eksportowanie i modyfikowanie rejestracji zbiorczo.](https://msdn.microsoft.com/library/dn790624.aspx)

#### <a name="service-bus-explorer"></a>Eksplorator usługi Service Bus

Wielu klientów korzysta z [Eksploratora usługi Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer) do wyświetlania centrów powiadomień i zarządzania nimi. Usługa Service Bus Explorer jest projektem typu open source. 

### <a name="verify-message-notifications"></a>Weryfikowanie powiadomień o wiadomościach

#### <a name="azure-portal"></a>Portal Azure

Aby wysłać powiadomienie testowe do klientów bez konieczności konieczności uruchamiania kopii zapasowej usługi, w obszarze **POMOC / ROZWIĄZYWANIE PROBLEMÓW**wybierz opcję **Testuj wyślij**.

![Testowanie funkcji wysyłania na platformie Azure][7]

#### <a name="visual-studio"></a>Visual Studio

Można również wysyłać powiadomienia testowe z programu Visual Studio.

![Testowanie funkcji wysyłania w programie Visual Studio][10]

Aby uzyskać więcej informacji na temat korzystania z Centrów powiadomień w Eksploratorze programu Visual Studio Server, zobacz następujące artykuły:

* [Jak wyświetlić rejestracje urządzeń dla centrów powiadomień](https://docs.microsoft.com/previous-versions/windows/apps/dn792122(v=win.10))
* [Głębokie nurkowanie: Visual Studio 2013 Update 2 RC i Azure SDK 2.3]
* [Zapowiedź wydania programu Visual Studio 2013 Update 3 i Azure SDK 2.4]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Debugowanie powiadomień nie powiodło się i przejrzyj wynik powiadomień

#### <a name="enabletestsend-property"></a>Właściwość EnableTestSend

Po wysłaniu powiadomienia za pośrednictwem centrum powiadomień powiadomienie jest początkowo umieszczane w kolejce. Centra powiadomień określa poprawne obiekty docelowe, a następnie wysyła powiadomienie do usługi powiadomień wypychanych. Jeśli używasz interfejsu API REST lub dowolnego z sdk klienta, powrót wywołania wysyłania oznacza tylko, że wiadomość jest w kolejce z centrum powiadomień. Nie zapewnia wglądu w to, co się stało, gdy centra powiadomień ostatecznie wysłał powiadomienie do usługi powiadomień wypychanych.

Jeśli powiadomienie nie dociera do urządzenia klienckiego, mógł wystąpić błąd, gdy centra powiadomień próbowały dostarczyć je do usługi powiadomień wypychanych. Na przykład rozmiar ładunku może przekroczyć maksymalną dozwoloną przez usługę powiadomień wypychanych lub poświadczenia skonfigurowane w Centrach powiadomień mogą być nieprawidłowe.

Aby uzyskać wgląd w błędy usługi powiadomień wypychanych, można użyć [EnableTestSend] właściwości. Ta właściwość jest automatycznie włączona podczas wysyłania wiadomości testowych z portalu lub klienta programu Visual Studio. Ta właściwość służy do wyświetlić szczegółowe informacje debugowania, a także za pośrednictwem interfejsów API. Obecnie można go używać w pliku .NET SDK. Zostanie on dodany do wszystkich sdk klienta po pewnym czasie.

Aby użyć `EnableTestSend` właściwości z wywołania REST, dołącz parametr ciągu zapytania o nazwie *test* na końcu wywołania wysyłania. Przykład:

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

#### <a name="net-sdk-example"></a>Przykład SDK platformy .NET

Oto przykład użycia sdk .NET do wysyłania natywnego powiadomienia podręcznego (wyskakującego):

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

Pod koniec egzekucji, `result.State` po `Enqueued`prostu stwierdza . Wyniki nie zapewniają żadnego wglądu w to, co się stało z powiadomieniem wypychanym.

Następnie można użyć `EnableTestSend` właściwości logicznej. Użyj `EnableTestSend` właściwości podczas inicjowania, `NotificationHubClient` aby uzyskać szczegółowy stan o błędach usługi powiadomień wypychanych, które występują podczas wysyłania powiadomienia. Wywołanie wysyłania zajmuje dodatkowy czas, aby powrócić, ponieważ najpierw potrzebuje Centrum powiadomień do dostarczenia powiadomienia do usługi powiadomień wypychanych.

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

#### <a name="sample-output"></a>Przykładowe dane wyjściowe

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

Ten komunikat wskazuje, że poświadczenia skonfigurowane w centrach powiadomień są nieprawidłowe lub że występuje problem z rejestracjami w centrum. Usuń tę rejestrację i pozwól klientowi ponownie utworzyć rejestrację przed wysłaniem wiadomości.

> [!NOTE]
> Korzystanie z `EnableTestSend` właściwości jest mocno ograniczona. Tej opcji należy używać tylko w środowisku deweloperskim/testowym i z ograniczonym zestawem rejestracji. Powiadomienia debugowania są wysyłane tylko do 10 urządzeń. Istnieje również limit na debugowanie przetwarzania wysyła, na 10 na minutę.

### <a name="review-telemetry"></a>Przeglądanie danych telemetrycznych

#### <a name="azure-portal"></a>Portal Azure

W portalu możesz uzyskać szybki przegląd wszystkich działań w Centrum powiadomień.

1. Na **karcie Przegląd** można zobaczyć zagregowany widok rejestracji, powiadomień i błędów według platformy.

   ![Pulpit nawigacyjny przeglądu centrów powiadomień][5]

2. Na karcie **Monitor** możesz dodać wiele innych metryk specyficznych dla platformy, aby uzyskać głębszy wygląd. Można sprawdzić w szczególności błędy, które są zwracane, gdy Centra powiadomień próbuje wysłać powiadomienie do usługi powiadomień wypychanych.

   ![Dziennik aktywności portalu Azure][6]

3. Zacznij od przejrzenia **wiadomości przychodzących,** **operacji rejestracji**i **pomyślnych powiadomień**. Następnie przejdź do karty na platformę, aby przejrzeć błędy, które są specyficzne dla usługi powiadomień wypychanych.

4. Jeśli ustawienia uwierzytelniania centrum powiadomień są nieprawidłowe, zostanie wyświetlony **komunikat Błąd uwierzytelniania PNS.** Jest to dobry wskaźnik, aby sprawdzić poświadczenia usługi powiadomień wypychanych.

#### <a name="programmatic-access"></a>Dostęp programowy

Aby uzyskać więcej informacji na temat dostępu programowego, zobacz [Dostęp programowy](https://docs.microsoft.com/previous-versions/azure/azure-services/dn458823(v=azure.100)).

> [!NOTE]
> Kilka funkcji związanych z telemetrią, takich jak eksportowanie i importowanie rejestracji i dostępu do danych telemetrycznych za pośrednictwem interfejsów API, jest dostępnych tylko w warstwie usługi Standardowa. Jeśli spróbujesz użyć tych funkcji z warstwy usługi Bezpłatna lub Podstawowa, zostanie wyświetlony komunikat o wyjątku, jeśli używasz sdk. Jeśli używasz funkcji bezpośrednio z interfejsów API REST, otrzymasz błąd HTTP 403 (zabronione).
>
> Aby korzystać z funkcji związanych z telemetrią, najpierw upewnij się w witrynie Azure portal, że używasz standardowej warstwy usługi.  

<!-- IMAGES -->
[0]: ./media/notification-hubs-push-notification-fixer/Architecture.png
[1]: ./media/notification-hubs-push-notification-fixer/FCMConfigure.png
[3]: ./media/notification-hubs-push-notification-fixer/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-push-notification-fixer/PortalDashboard.png
[6]: ./media/notification-hubs-push-notification-fixer/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-push-notification-fixer/VSRegistrations.png
[9]: ./media/notification-hubs-push-notification-fixer/vsserverexplorer.png
[10]: ./media/notification-hubs-push-notification-fixer/VSTestNotification.png

<!-- LINKS -->
[Omówienie Centrów powiadomień]: notification-hubs-push-notification-overview.md
[Wprowadzenie do usługi Azure Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Szablony]: https://msdn.microsoft.com/library/dn530748.aspx
[Omówienie sieci APN]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[Informacje o wiadomościach FCM]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: https://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[View device registrations for notification hubs]: https://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[Głębokie nurkowanie: Visual Studio 2013 Update 2 RC i Azure SDK 2.3]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[Zapowiedź wydania programu Visual Studio 2013 Update 3 i Azure SDK 2.4]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend (Włącz testowanie)]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Programmatic telemetry access]: https://msdn.microsoft.com/library/azure/dn458823.aspx
