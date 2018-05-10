---
title: Centra powiadomień Azure porzucony diagnostyki powiadomień
description: Dowiedz się, jak zdiagnozować typowe problemy z porzuconych powiadomienia w usług Azure Notification Hubs.
services: notification-hubs
documentationcenter: Mobile
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: bc9ef70560f0485da81c1f54aa955cee76d280ab
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="diagnose-dropped-notifications-in-notification-hubs"></a>Diagnozowanie porzuconych powiadomienia w centra powiadomień

Jednym z najbardziej typowe pytania klientów usługi Azure Notification Hubs jest jak rozwiązywać problemy podczas powiadomienia, które są wysyłane z aplikacji nie są wyświetlane na urządzeniach klienckich. Chce wiedzieć, gdzie i dlaczego powiadomienia zostały usunięte i jak rozwiązać problem. W tym artykule identyfikuje Dlaczego powiadomień może pobrać usunięty lub nie można odebrać przez urządzenia. Dowiedz się, jak analizować i ustalenia głównej przyczyny. 

Warto najpierw zrozumieć, jak usługa Notification Hubs wypychanie powiadomień do urządzenia.

![Architektura centra powiadomień][0]

Do typowych wysyłania powiadomień przepływu, komunikat jest wysyłany z *zaplecza aplikacji* do usługi Notification Hubs. Centra powiadomień jest część przetwarzania na wszystkich rejestracji. Przetwarzanie uwzględnia skonfigurowanych znaczników i wyrażeń tagów w celu ustalenia "elementów docelowych." Obiekty docelowe są rejestracje, które muszą odbierać powiadomienia wypychane. Te mogą obejmować jedną lub naszych obsługiwanych platform: iOS, Google, Windows, Windows Phone, urządzeń Kindle i Baidu dla Chin systemu Android.

Z ustanowionych celów usługi Notification Hubs wypychanie powiadomień do *push notification service* dla platformy urządzeń. Przykładami Apple Push Notification service (APNs) firmy Apple i Firebase Cloud Messaging (FCM) dla usług Google. Powiadomienia wypchnięć centra powiadomień podzielić na wiele instancji rejestracji. Centra powiadomień jest uwierzytelniany przy użyciu usługi powiadomień wypychanych odpowiednich na podstawie podanych poświadczeń, które ustawione w portalu Azure w obszarze **Konfigurowanie Centrum powiadomień**. Powiadomienie wypychane następnie usługi przodu powiadomienia do odpowiednich *urządzeń klienckich*. 

Do końcowej gałęzi dostarczania powiadomień o zapytaniach odbywa się między usługi powiadomień wypychanych platformy i urządzenia. Jedną z czterech głównych elementów w procesie powiadomień wypychanych (klienta, zaplecza aplikacji usługi Notification Hubs i usługi powiadomień wypychanych platformy) może spowodować powiadomienia ma być przerwane. Aby uzyskać więcej informacji o architekturze usługi Notification Hubs, zobacz [usługi Notification Hubs — omówienie].

Nie można dostarczać powiadomienia mogą wystąpić podczas wstępnej fazy badania/przemieszczania. Porzucone powiadomienia na tym etapie może wskazywać na problem z konfiguracją. W przypadku niepowodzenia dostarczać powiadomienia w środowisku produkcyjnym, niektóre lub wszystkie powiadomienia mogą być opuszczane. W takim przypadku wskazane jest bardziej aplikacji lub problem wzorzec do obsługi komunikatów. 

Następna sekcja analizuje scenariusze, w których powiadomienia mogą być opuszczane, od typowych więcej rzadko.

## <a name="notification-hubs-misconfiguration"></a>Błąd konfiguracji centra powiadomień
Aby pomyślnie wysyłać powiadomienia do usługi odpowiednich push notification service, usługi Notification Hubs musi do samodzielnego uwierzytelnienia w kontekście dewelopera aplikacji. W tym celu włączone dewelopera tworzy konto dewelopera z odpowiednich platform (Google, Apple systemu Windows i tak dalej). Następnie Deweloper rejestruje ich aplikacji z platformą, w którym znaleźć poświadczeń. 

Poświadczenia platformy należy dodać do portalu Azure. Jeśli żadne powiadomienia się połączyć urządzenie, pierwszym krokiem powinno być upewnij się, że poprawne poświadczenia są skonfigurowane w usłudze Notification Hubs. Poświadczenia muszą odpowiadać aplikację, która jest tworzona przy użyciu konta dewelopera specyficzne dla platformy. 

Aby uzyskać instrukcje ukończyć ten proces, zobacz [wprowadzenie do usługi Azure Notification Hubs].

Poniżej przedstawiono kilka typowych błędów konfiguracji, aby wyszukać:

* **Ogólne**
   
    * Upewnij się, że nazwą Centrum powiadomień (bez błędów pisowni) jest taka sama w każdym z tych lokalizacji:

        * Gdy zarejestrujesz od klienta.
        * Gdzie wysyłania powiadomień z zaplecza.
        * Gdzie można skonfigurować poświadczenia usługi wypychania powiadomień.
    
    * Upewnij się, że używasz ciągi konfiguracji podpisu dostępu współdzielonego poprawne na kliencie i wewnętrznych aplikacji. Ogólnie rzecz biorąc, należy użyć **DefaultListenSharedAccessSignature** na kliencie i **DefaultFullSharedAccessSignature** dla aplikacji zaplecza (przyznaje uprawnienia do wysyłania powiadomień do Centra powiadomień).

* **Konfiguracja usługi APNs**
   
    Dwa elementy Hub różnych muszą zachować: koncentratorze w środowisku produkcyjnym, a inny Centrum testowania. Oznacza to, że musisz przekazać certyfikatu, który jest używany w środowisku piaskownicy do osobnego koncentratora niż certyfikat i Centrum, które mają być używane w środowisku produkcyjnym. Nie należy próbować przekazać różne typy certyfikatów do tego samego Centrum. Może to powodować błędy powiadomień. 
    
    Jeśli przypadkowo przekazać różne typy certyfikatów samego Centrum, zaleca się usunąć koncentratora i rozpocząć od nowa pracę z nowym Centrum. Jeśli z jakiegoś powodu nie można usunąć Centrum, co najmniej należy usunąć wszystkie istniejące rejestracje z koncentratora. 

* **Konfiguracja FCM** 
   
    1. Upewnij się, że *klucz serwera* uzyskany Firebase pasuje do klucza serwera, który został zarejestrowany w portalu Azure.
   
    ![Klucz serwera firebase][3]
   
    2. Upewnij się, że skonfigurowano **identyfikator projektu** na kliencie. Można uzyskać wartość **identyfikator projektu** z Firebase pulpitu nawigacyjnego.
   
    ![Identyfikator projektu firebase][1]

## <a name="application-issues"></a>Problemy z aplikacji
* **Znaczniki i wyrażeń tagów**

    Jeśli używasz znaczników lub wyrażeń tagów do segmentowania odbiorców, jest to możliwe, że podczas wysyłania powiadomienia docelowy nie znajduje się na podstawie znaczników lub tagu wyrażeń, które określisz w wywołania wysyłania. 
    
    Zapoznaj się z rejestracji, aby upewnić się, czy zgodnymi tagami podczas wysyłania powiadomień. Następnie zweryfikuj odbieranie powiadomień tylko od klientów, którzy mają tych rejestracji. 
    
    Na przykład jeśli wprowadzono wszystkich rejestracji w usłudze Notification Hubs za pomocą znacznika "Polityka" i wysyłania powiadomień z tagiem "Sport", powiadomienie nie zostanie wysłany do dowolnego urządzenia. Złożonych przypadkach mogą dotyczyć wyrażeń tagów, w których został zarejestrowany za pomocą "Tag A" lub "Tag B", ale podczas wysyłania powiadomień, target "Tag A & & Tag B." W sekcji porady samokontroli w dalszej części tego artykułu możemy opisano, jak Przejrzyj rejestracji i ich tagów. 

* **Problemy dotyczące szablonu**

    Jeśli używasz szablony, upewnij się, postępuj zgodnie z wytycznymi, w opisanych w [szablony]. 

* **Nieprawidłowy rejestracji**

    Jeśli Centrum powiadomień został poprawnie skonfigurowany i znaczniki lub wyrażeń tagów użyto poprawnie, prawidłowe elementy docelowe są dostępne. Mają być wysyłane powiadomienia do następujących elementów docelowych. Usługa centra powiadomień, a następnie uruchamiany poza partie przetwarzania równoległego. Każdej z partii wysyła komunikaty do zestawu rejestracji. 

    > [!NOTE]
    > Ponieważ przetwarzanie jest wykonywane równolegle, kolejność, w której powiadomienia są dostarczane nie jest gwarantowana. 

    Centra powiadomień jest zoptymalizowana pod kątem model dostarczania komunikatu "raz w większości". Firma Microsoft podejmować deduplikacji, dzięki czemu powiadomienia nie są dostępne w ramach więcej niż raz na urządzeniu. Aby to zapewnić, możemy sprawdzić rejestracji i upewnij się, że tylko jeden komunikat jest wysyłany na identyfikator urządzenia, przed wysłaniem wiadomości do usługi powiadomień wypychanych. 

    W każdej partii są wysyłane do usługi powiadomień wypychanych, która z kolei akceptuje i sprawdzanie poprawności rejestracje, jest to możliwe, że usługi powiadomień wypychanych wykryje błąd co najmniej jednym rejestracje w partii. W takim przypadku usługi powiadomień wypychanych zwróci błąd usługi Notification Hubs, a proces jest przerywany. Usługa powiadomień wypychanych powoduje całkowite zerwanie tej partii. Jest to szczególnie ważne przy użyciu usługi APNS, który używa protokołu strumienia TCP. 

    Firma Microsoft są zoptymalizowane pod kątem w większości raz dostarczania. Jednak w takim przypadku źle działający rejestracji zostanie usunięty z bazy danych. Spróbuj możemy dostarczania powiadomień o zapytaniach w pozostałej części urządzeń w danej partii.

    Aby uzyskać więcej informacji o o przy próbie dostarczenia nie powiodło się przed rejestracji, można użyć interfejsów API REST centra powiadomień [na Telemetrii wiadomości: pobrać Telemetrii komunikat powiadomienia](https://msdn.microsoft.com/library/azure/mt608135.aspx) i [opinii systemu powiadomień platformy](https://msdn.microsoft.com/library/azure/mt705560.aspx). Przykładowy kod, zobacz [przykład wysłać REST](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample).

## <a name="push-notification-service-issues"></a>Problemy z usługi powiadomień wypychanych
Po otrzymaniu komunikatu powiadomienia przez usługi powiadomień wypychanych platformy jest odpowiedzialny za usługi powiadomień wypychanych do dostarczenia powiadomienia do urządzenia. W tym momencie usługi Notification Hubs jest poza obrazu, a nie ma kontroli nad gdy lub jeśli powiadomienie jest dostarczany do urządzenia. 

Ponieważ usługi powiadomień platformy są niezawodne, powiadomienia zwykle do zarządzanego urządzenia z usługi powiadomień wypychanych w ciągu kilku sekund. Jeśli usługi powiadomień wypychanych jest ograniczanie, centra powiadomień stosuje wykładniczej strategii wycofania. Jeśli usługi powiadomień wypychanych pozostaje nieosiągalny przez 30 minut, mamy zasady w celu wygaśnie i trwale usunąć te wiadomości. 

Jeśli usługi powiadomień wypychanych prób dostarczenia powiadomienia, ale urządzenie jest w trybie offline, powiadomienie jest przechowywany przez usługi powiadomień wypychanych w ograniczonym okresie czasu. Powiadomienia są dostarczane do urządzenia, gdy urządzenie stanie się dostępny. 

Dla każdej aplikacji znajduje się tylko jedno powiadomienie ostatnie. Jeśli wiele powiadomienia są wysyłane, gdy urządzenie jest w trybie offline, każde nowe powiadomienie powoduje, że wcześniejsze powiadomienie do usunięcia. Utrzymywanie tylko najnowsze powiadomień jest określany jako *powiadomienia łączącego* w APN, i *zwijanie* w FCM (który używa klucza się). Jeśli urządzenie pozostaje w trybie offline przez dłuższy czas, powiadomienia są przechowywane na urządzeniu zostaną odrzucone. Aby uzyskać więcej informacji, zobacz [omówienie APNs] i [wiadomości o FCM].

Z usługą Azure Notification Hubs można przekazać klucza łączącego za pośrednictwem nagłówka HTTP przy użyciu ogólnego interfejsu API SendNotification. Na przykład dla zestawu SDK .NET, należy użyć **SendNotificationAsync**. Interfejs API SendNotification również ma nagłówki HTTP, które są przekazywane jako-ma usługi powiadomień wypychanych odpowiednich. 

## <a name="self-diagnosis-tips"></a>Samokontroli porady
Poniżej przedstawiono ścieżki do diagnozowanie głównej przyczyny porzuconych powiadomienia w centra powiadomień:

### <a name="verify-credentials"></a>Weryfikowanie poświadczeń
* **Portalu dla deweloperów usługi powiadomień wypychanych**
   
    Sprawdź poświadczenia w odpowiednich wypychania powiadomień usługi portalu dla deweloperów (APN, FCM usługi powiadomień systemu Windows i tak dalej). Aby uzyskać więcej informacji, zobacz [wprowadzenie do usługi Azure Notification Hubs].

* **Azure Portal**
   
    Aby przejrzeć i być zgodne z poświadczeniami z tymi, które uzyskane z portalu wypychania powiadomień usługi deweloperów w portalu Azure, przejdź do **zasady dostępu** kartę. 
   
    ![Zasady dostępu w portalu Azure][4]

### <a name="verify-registrations"></a>Weryfikowanie rejestracji
* **Program Visual Studio**
   
    Jeśli używasz programu Visual Studio do tworzenia aplikacji, można nawiązać za pomocą Eksploratora serwera do wyświetlania i zarządzania nimi wiele usług platformy Azure, w tym usługi Notification Hubs. Jest to szczególnie przydatne w przypadku środowiska i testowania. 
   
    ![W Eksploratorze serwera programu Visual Studio][9]
   
    Można wyświetlać i zarządzać wszystkich rejestracji w Centrum, skategoryzowany przez platformę, native lub rejestracji szablonu, wszystkie tagi identyfikator usługi powiadomień wypychanych, identyfikator rejestracji i datę wygaśnięcia. Można również edytować rejestracji na tej stronie. Jest to szczególnie przydatne do edycji tagów. 
   
    ![Rejestracji urządzenie programu Visual Studio][8]
   
   > [!NOTE]
   > Edytowanie rejestracji tylko podczas tworzenia/testowania, a także z ograniczoną liczbę rejestracji za pomocą programu Visual Studio. Rejestracji zbiorczo edytować należy wziąć pod uwagę przy użyciu eksportu i zaimportuj rejestracji funkcji opisanych w [eksportować i modyfikować rejestracje zbiorczo](https://msdn.microsoft.com/library/dn790624.aspx).
   > 
   > 
* **Eksplorator usługi Service Bus**
   
    Wielu klientów używa [Service Bus Explorer] do wyświetlania i zarządzania ich Centrum powiadomień. Eksploratora magistrali usług to projekt typu open source. Aby uzyskać przykłady, zobacz [kod Service Bus Explorer].

### <a name="verify-message-notifications"></a>Sprawdź komunikat powiadomienia
* **Azure Portal**
   
    Aby wysłać powiadomienie testowe do klientów bez konieczności usługi zaplecza do pracy, w obszarze **pomocy technicznej i rozwiązywania problemów**, wybierz pozycję **wysyłanie testowe**. 
   
    ![Funkcja wysyłania testu na platformie Azure][7]
* **Program Visual Studio**
   
    Można również wysłać powiadomienia testowe w programie Visual Studio.
   
    ![Testowanie funkcji wysyłania w programie Visual Studio][10]
   
    Aby uzyskać więcej informacji o korzystaniu z usługi Notification Hubs z Eksploratora serwera w usłudze Visual Studio zobacz następujące artykuły: 
   
   * [Wyświetlanie rejestracji urządzeń dla usługi notification hubs]
   * [Nowości: Visual Studio 2013 Update 2 RC i 2.3 zestawu SDK platformy Azure]
   * [Informuje o wersji programu Visual Studio 2013 Update 3 i 2.4 zestawu SDK platformy Azure]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Debugowanie nie powiodło się, powiadomień i przejrzyj wyniku powiadomienia
**Właściwość EnableTestSend**

W przypadku początkowo wysyłania powiadomienia za pośrednictwem usługi Notification Hubs, powiadomienia przebywa w kolejce do przetworzenia w usłudze Notification Hubs. Centra powiadomień określa elementy docelowe prawidłowe, a następnie wysyła powiadomienie do usługi powiadomień wypychanych. Jeśli używasz interfejsu API REST lub dowolny z zestawów SDK klienta zwracany pomyślnego połączenia wysyłania tylko oznacza że wiadomość została pomyślnie umieszczona w kolejce z usługą Notification Hubs. Nie masz żadnych analizę co się stało, gdy usługi Notification Hubs ostatecznie wiadomość wysłana usługi powiadomień wypychanych. 

Jeśli powiadomienia nie został odebrany w urządzeniu klienckim, jest to możliwe, że wystąpił błąd podczas próby dostarczenie wiadomości do usługi powiadomień wypychanych usługi Notification Hubs. Na przykład rozmiar ładunku może przekracza maksymalną dozwoloną przez usługi powiadomień wypychanych lub poświadczenia skonfigurowane w usłudze Notification Hubs może być nieprawidłowy. 

Aby uzyskać wgląd w wypychanych błędy usługi powiadomień, można użyć [EnableTestSend] właściwości. Ta właściwość jest włączane automatycznie, podczas wysyłania wiadomości testowych z portalu lub klienta programu Visual Studio. Ta właściwość służy do Zobacz szczegółowe informacje o debugowaniu. Można również użyć właściwości za pośrednictwem interfejsów API. Obecnie można go używać w zestawu .NET SDK. Po pewnym czasie zostaną dodane do wszystkich zestawów SDK klienta. 

Umożliwia **EnableTestSend** właściwość wywołania REST, Dołącz parametr ciągu zapytania o nazwie *test* w celu połączenia wysyłania. Na przykład: 

    https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test

**Przykład (.NET SDK)**

Oto przykład przy użyciu zestawu .NET SDK można wysłać powiadomienia wyskakującego natywnego (wyskakujące):

```csharp
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
    var result = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(result.State);
```

Po zakończeniu wykonywania **wynik. Stan** po prostu stany **umieszczonych w kolejce**. Wyniki nie udostępniają analizę co się stało z powiadomienia wypychane. 

Następnie można użyć **EnableTestSend** właściwości typu Boolean. Użyj **EnableTestSend** właściwości podczas inicjowania **NotificationHubClient** Aby uzyskać szczegółowy stan o wypychania powiadomień usługi o błędach podczas wysyłania powiadomienia. Wywołanie wysyłania zajmuje dodatkowy czas zwrotu, ponieważ jest zwracany tylko wtedy, gdy usługi Notification Hubs został dostarczony powiadomienia do usługi powiadomień wypychanych, aby określić wyniki. 

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

**Przykładowe dane wyjściowe**

    DetailedStateAvailable
    windows
    7619785862101227384-7840974832647865618-3
    The Token obtained from the Token Provider is wrong

Ten komunikat oznacza, że nieprawidłowe poświadczenia są skonfigurowane w usłudze Notification Hubs, albo ma problem z rejestracji w Centrum. Zaleca się usunięcie tej rejestracji i umożliwić klientowi ponownie utworzyć rejestracji przed wysłaniem wiadomości. 

> [!NOTE]
> Użycie **EnableTestSend** właściwości silnie jest ograniczany. Użyj tej opcji tylko w środowisku i testowania oraz z ograniczonym zestawem rejestracji. Wyślemy powiadomienia debugowania na urządzeniach tylko 10. Istnieje również limit przetwarzania debugowania wysyła do 10 na minutę. 
> 
> 

### <a name="review-telemetry"></a>Przejrzyj telemetrii
* **Korzystanie z witryny Azure Portal**
   
    W portalu możesz uzyskać szybki przegląd wszystkich działań w Centrum powiadomień. 
   
    1. Na **omówienie** karcie widać zagregowany widok rejestracji, powiadomienia i błędy przez platformę. 
   
        ![Pulpitu nawigacyjnego przeglądu centra powiadomień][5]
   
    2. Na **Monitor** kartę, można dodać wiele innych metryk specyficzne dla platformy znaleźć więcej danych. Można przyjrzeć się w szczególności błędy związane z usługi powiadomień wypychanych zwracane, gdy próbuje wysłać powiadomienia usługi powiadomień wypychanych usługi Notification Hubs. 
   
        ![Dziennik aktywności portalu Azure][6]
   
    3. Rozpocznij od przejrzenia **wiadomości przychodzących**, **operacji rejestracji**, i **powiadomienia o pomyślnym**. Następnie przejdź do karty na platformy, aby przejrzeć błędy, które są specyficzne dla usługi powiadomień wypychanych. 
   
    4. Jeśli ustawienia uwierzytelniania dla Centrum powiadomień jest niepoprawna, komunikat **błąd uwierzytelniania systemu powiadomień platformy** pojawi się. To jest wskazuje, aby sprawdzić poświadczenia usługi wypychania powiadomień. 

* **Programowy dostęp**

    Aby uzyskać więcej informacji na temat dostęp programistyczny zobacz następujące artykuły: 

    * [Dostęp programistyczny telemetrii]  
    * [Dane telemetryczne dostęp za pośrednictwem interfejsów API próbki] 

    > [!NOTE]
    > Kilka funkcji związanych z telemetrii, takie jak eksportowanie i importowanie rejestracji i dane telemetryczne dostęp za pośrednictwem interfejsów API, są dostępne tylko w warstwie usług standardowa. Jeśli próba użycia warstwę usługi, te funkcje, bezpłatna lub podstawowa, zostanie wyświetlony komunikat wyjątku, jeśli używasz zestawu SDK i błąd HTTP 403 (Dostęp zabroniony) Jeśli używasz funkcji bezpośrednio z interfejsów API REST. 
    >
    >Aby używać funkcji związanych z telemetrii, najpierw upewnij się, w portalu Azure używanym w warstwie usług standardowa.  
> 
> 

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
[usługi Notification Hubs — omówienie]: notification-hubs-push-notification-overview.md
[wprowadzenie do usługi Azure Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[szablony]: https://msdn.microsoft.com/library/dn530748.aspx 
[omówienie APNs]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[wiadomości o FCM]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: http://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer]: https://msdn.microsoft.com/library/dn530751.aspx#sb_explorer
[kod Service Bus Explorer]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[Wyświetlanie rejestracji urządzeń dla usługi notification hubs]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx 
[Nowości: Visual Studio 2013 Update 2 RC i 2.3 zestawu SDK platformy Azure]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs 
[Informuje o wersji programu Visual Studio 2013 Update 3 i 2.4 zestawu SDK platformy Azure]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/ 
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Dostęp programistyczny telemetrii]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[Dane telemetryczne dostęp za pośrednictwem interfejsów API próbki]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel

