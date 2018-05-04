---
title: Azure Notification Hubs
description: Dowiedz się, jak dodać funkcje powiadomień wypychanych przy użyciu usługi Azure Notification Hubs.
author: ysxu
manager: erikre
editor: ''
services: notification-hubs
documentationcenter: ''
ms.assetid: fcfb0ce8-0e19-4fa8-b777-6b9f9cdda178
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 1/17/2017
ms.author: yuaxu
ms.openlocfilehash: ea8d569a8d51afee352860ab60a1fe311496b83c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="azure-notification-hubs"></a>Azure Notification Hubs
## <a name="overview"></a>Przegląd
Centra powiadomień Azure zapewniają aparat wypychania łatwy w użyciu, obejmującego wiele platform, skalowalnych w poziomie. Przy użyciu wywołania interfejsu API jednym i platform umożliwia łatwe wysyłanie powiadomień wypychanych docelowych i spersonalizowaną na dowolną platformę mobilną z poziomu dowolnego zaplecza w chmurze lub lokalnie.

Centra powiadomień działa Wielkiej dla firm i konsumentów scenariuszy. Oto kilka przykładów, których klienci używają usługi Notification Hubs dla:

* Wyślij powiadomienia o najważniejszych wiadomościach do milionów osób z niskim opóźnieniem.
* Wysyłaj kupony oparte na lokalizacji do segmentów użytkowników zainteresowanych.
* Wyślij powiadomienia związane ze zdarzeniami do użytkowników lub grup dla aplikacji media/sportowych/finance/gier.
* Wypchnij promocyjna zawartości do udziału i obrotu dla klientów do aplikacji.
* Powiadom użytkowników o zdarzeniach dotyczących przedsiębiorstwa takie jak nowe wiadomości i elementów roboczych.
* Wyślij kody uwierzytelniania wieloskładnikowego.

## <a name="what-are-push-notifications"></a>Co to są powiadomienia wypychane?
Powiadomień wypychanych jest formę komunikacji aplikacji do użytkownika, gdy powiadamiania użytkowników aplikacji mobilnych niektórych informacji żądaną, zazwyczaj w oknie podręcznym lub okno dialogowe. Użytkownicy zazwyczaj można wybrać do wyświetlania lub odrzucić komunikat, a wybór pierwszej spowoduje otwarcie aplikacji mobilnej, które były przekazywane powiadomienia.

Wysyłanie powiadomień wypychanych jest zwiększenie zaangażowania i użycia aplikacji w aplikacjach dla użytkowników indywidualnych i aplikacje przedsiębiorstwa w komunikacji biznesowej aktualne informacje. Jest on najlepiej komunikacji aplikacji do użytkownika, ponieważ jest energii dla urządzeń przenośnych, elastyczne dla nadawców powiadomienia i dostępne podczas odpowiedniej aplikacji nie są aktywne.

Aby uzyskać więcej informacji na powiadomienia wypychane dla kilku popularnych platform:
* [iOS](https://developer.apple.com/notifications/)
* [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
* [Windows](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx)

## <a name="how-push-notifications-work"></a>Sposób działania powiadomień wypychanych
Powiadomienia wypychane są dostarczane przy użyciu infrastruktur dla poszczególnych platform o nazwie *systemów powiadomień platformy* (PNSes). Oferują one barebone wypychanych do dostarczania wiadomości na urządzeniu z podanych obsługi i nie ma wspólnego interfejsu. Aby wysłać powiadomienie do wszystkich klientów w systemie iOS, Android i Windows wersje aplikacji, deweloper należy skontaktować się z APNS (Apple Push Notification Service), FCM (Firebase Cloud Messaging) i usługi WNS (Windows Notification Service), podczas przetwarzania wsadowego wysyła.

Na wysokim poziomie poniżej przedstawiono, jak działa wypychania:

1. Aplikacja kliencka decyduje, chce otrzymywać wypchnięć dlatego kontaktuje się odpowiednie systemu powiadomień platformy można pobrać uchwytu wypychania unikatowy i tymczasowych. Typ dojścia zależy od systemu (np. usługi WNS ma identyfikatory URI, podczas gdy APNS ma tokenów).
2. Aplikacja kliencka przechowuje to dojście w zaplecze aplikacji lub dostawcy.
3. Aby wysyłać powiadomienia wypychane, zaplecza aplikacji kontaktuje się z systemu powiadomień platformy przy użyciu dojścia pod kątem aplikacji określonego klienta.
4. System powiadomień platformy przekazuje następnie powiadomienie do urządzenia określonego przez dojście.

![][0]

## <a name="the-challenges-of-push-notifications"></a>Wyzwania związane z powiadomieniami wypychanymi
PNSes są wydajne, ich wykonać dużo pracy Deweloper aplikacji, aby zaimplementować nawet typowe scenariusze dotyczące powiadomień wypychanych, takie jak emitowanie lub wysyłanie powiadomień wypychanych do segmentowanych użytkowników.

Wypychania jest jednym z najbardziej pożądanych funkcji przenośnych usług w chmurze, ponieważ jego funkcjonowania wymaga infrastruktury złożonych, niezwiązane aplikacji główną logiką biznesową. Niektóre wyzwania infrastrukturalne są:

* **Zależności dotyczące platformy**: 

  * Wewnętrznej bazy danych musi mieć złożone i twardych Obsługa logiki zależny od platformy do wysyłania powiadomień do urządzeń na różnych platformach, ponieważ nie są unified PNSes.
* **Skala**:

  * Zgodnie z zaleceniami tokeny urządzeń muszą zostać odświeżone przy każdym uruchomieniu aplikacji. Oznacza to, że wewnętrznej bazy danych zajmuje się dużą ilość ruchu i bazy danych programu access tylko w celu zapewnienia aktualności tokenów. Liczba urządzeń rozwojem setki i tysiące miliony koszt tworzenia i obsługi tej infrastruktury po masowych.
  * Większość PNSes nie obsługuje emisji do wielu urządzeń. Oznacza to proste emisji do milionów urządzeń powoduje milion wywołania PNSes. Skalowanie tej ilość ruchu sieciowego z minimalnym opóźnieniem jest proste.
* **Routing**:
  
  * Chociaż PNSes umożliwiają wysyłanie komunikatów do urządzeń, większości aplikacji Powiadomienia są przeznaczone do użytkowników lub grup zainteresowań. Oznacza to, że wewnętrznej bazy danych musi zapewniać rejestr Aby skojarzyć urządzenia z grupy zainteresowań, użytkowników, właściwości, itd. Ten narzut zwiększa czas na rynek oraz koszty obsługi aplikacji.

## <a name="why-use-notification-hubs"></a>Dlaczego warto używać usługi Notification Hubs?
Notification Hubs eliminuje wszystkich skomplikowanych elementów, skojarzonego z włączeniem push własnych. Jego infrastruktury powiadomień wypychanych obejmującego wiele platform, skalowalnych w poziomie zmniejsza wypychania związane z kodami i upraszcza z wewnętrzną bazą danych. Z usługą Notification Hubs urządzenia są jedynie odpowiedzialny za rejestrację ich dojść systemu powiadomień platformy przy użyciu koncentratora, gdy wewnętrznej bazy danych wysyła wiadomości do użytkowników lub grup zainteresowań, jak pokazano na poniższej ilustracji:

![][1]

Centra powiadomień jest aparat gotowe do użycia wypychanych oferującą następujące korzyści:

* **Dla wielu platform**

  * Obsługę wszystkich platform głównych wypychania z systemami iOS, Android, Windows i urządzeń Kindle i Baidu.
  * Wspólny interfejs do wszystkich platform w formatach specyficzne dla platformy lub niezależne od platformy z żadne czynności specyficzne dla platformy.
  * Urządzenie obsłudze w jednym miejscu.
* **Krzyżowe zapleczy**
  
  * Chmurze lub lokalnie
  * .NET, Node.js, Java,... itd.
* **Bogaty zestaw wzorców dostarczania**:

  * *Emisji do jednego lub wielu platform*: można natychmiast emisji do milionów urządzeń na platformach przy użyciu jednego wywołania interfejsu API.
  * *Wypychanie urządzenie*: można kierować powiadomienia do poszczególnych urządzeń.
  * *Wypychanie do użytkownika*: tagi i szablony funkcji pomóc Ci zrealizować wszystkich urządzeń i platform użytkownika.
  * *Powiadomień wypychanych do segmentu tagami dynamiczne*: tagi funkcja pomaga segmentu urządzenia i naciśnij, aby je zgodnie z potrzebami, czy jest wysyłany do jednego segmentu, czy wyrażenie segmentów (np. active i życie w Seattle nie nowego użytkownika). Zamiast jest ograniczona do pub-sub, możesz zaktualizować dowolnym tagi urządzenia i w czasie.
  * *Zlokalizowane wypychania*: szablony pomaga osiągnąć lokalizacji bez wpływu na kod zaplecza.
  * *Dyskretnej wypychania*: wzorzec do wypychania można umożliwia wysyłanie powiadomień dyskretnej na urządzeniach i wyzwalania ich zakończenie niektórych ściąga lub akcji.
  * *Zaplanowane wypychania*: można zaplanować w dowolnym czasie wysyłania powiadomień.
  * *Bezpośrednie wypychania*: można pominąć rejestrowania urządzeń z obsługą i bezpośrednio partii naciśnij, aby uzyskać listę dojść urządzeń.
  * *Spersonalizowanych wypychania*: zmienne wypychania urządzenia spersonalizowanych pomaga wysyłania urządzenia wypychanie powiadomień z dostosowanych pary klucz wartość.
* **Rozbudowane informacje telemetryczne**
  
  * Ogólne dane telemetryczne wypychania, urządzenia, błąd i operacji jest dostępna w portalu Azure i programowo.
  * Na komunikat Telemetrii śledzi każdego wypychania od wywołania żądania początkowego do naszej usługi pomyślnie przetwarzanie wsadowe wypchnięć wychodzących.
  * Opinie systemu powiadomień platformy komunikuje się wszystkich informacji zwrotnych z systemów powiadomień Platfom pomocnych w debugowaniu.
* **Skalowalność** 
  
  * Wysyłanie wiadomości szybkiego do milionów urządzeń bez konieczności ponownego projektowania lub urządzenie dzielenia na fragmenty.
* **Bezpieczeństwo**

  * Udostępniony klucz tajny dostępu (SAS) lub uwierzytelnianie federacyjne.

## <a name="integration-with-app-service-mobile-apps"></a>Integracja z usługą App Service Mobile Apps
W celu umożliwienia bezproblemowej i jednorodnej obsługi we wszystkich usługach Azure funkcja [App Service Mobile Apps] ma wbudowaną obsługę powiadomień wypychanych przy użyciu usługi Notification Hubs. Funkcja [App Service Mobile Apps] oferuje wysoce skalowalną, globalnie dostępną platformę tworzenia aplikacji mobilnych dla deweloperów w przedsiębiorstwach i integratorów systemów. Platforma ta oferuje bogaty zestaw funkcji dla deweloperów aplikacji mobilnych.

Deweloperzy aplikacji mobilnych mogą korzystać z usługi Notification Hubs przy użyciu następującego przepływu pracy:

1. Pobranie dojścia do urządzenia w systemie powiadomień platformy
2. Rejestrowanie urządzenia z usługą Notification Hubs za pomocą wygodny interfejs API rejestru zestawu SDK klienta usługi Mobile Apps
   * Zauważ, że usługa Mobile Apps usuwa wszystkie tagi rejestracji ze względów bezpieczeństwa. Pracuj z usługą Notification Hubs bezpośrednio z zaplecza, aby skojarzyć tagi z urządzeniami.
3. Wysyłanie powiadomień z zaplecza aplikacji przy użyciu usługi Notification Hubs

Oto niektóre udogodnienia dla deweloperów wynikające z tej integracji:

* **Zestawy SDK klientów aplikacji mobilnej**: te wieloplatformowe zestawy SDK zapewniają proste interfejsy API do rejestracji i komunikują się z Centrum powiadomień, skojarzone z aplikacją mobilną automatycznie. Deweloperzy nie muszą odnajdywać poświadczeń usługi Notification Hubs i pracować przy użyciu dodatkowych usług.

  * *Wypychanie do użytkownika*: zestawy SDK automatycznie dodają tagi danego urządzenia w usłudze Mobile Apps uwierzytelniony identyfikator użytkownika, aby włączyć scenariusza wypychania do użytkownika.
  * *Wypychanie urządzenie*: zestawy SDK automatycznie używają Identyfikatora instalacji Mobile Apps jako identyfikatora GUID do rejestracji w usłudze Notification Hubs, oszczędzając deweloperom pracy związanej z obsługą identyfikatorów GUID wielu usług.
* **Model instalacji**: Mobile Apps współpracuje z najnowszym modelem wypychania usługi Notification Hubs do reprezentowania wszystkich właściwości wypychania skojarzonych z urządzeniem w instalacji JSON, jest wyrównywany z usługami powiadomień wypychanych, który jest łatwy w użyciu.
* **Elastyczność**: deweloperzy zawsze można wybrać do pracy z usługą Notification Hubs bezpośrednio nawet w przypadku integracji w miejscu.
* **Zintegrowane środowisko w [portalu Azure]**: wypychanie jako możliwość ma wizualną reprezentację w Mobile Apps i deweloperzy mogą z łatwością pracować przy użyciu skojarzonego Centrum powiadomień za pośrednictwem aplikacji mobilnej.

## <a name="next-steps"></a>Następne kroki
Więcej informacji na temat usługi Notification Hubs można znaleźć w następujących tematach:

* **[W jaki sposób klienci używają usługi Notification Hubs]**
* **[Przewodniki i samouczki dotyczące usługi Notification Hubs]**
* **Samouczki wprowadzenie centra powiadomień**: [iOS], [Android], [uniwersalnych systemu Windows], [Windows Phone], [ Kindle], [Xamarin.iOS], [platformy Xamarin.Android]

[0]: ./media/notification-hubs-overview/registration-diagram.png
[1]: ./media/notification-hubs-overview/notification-hub-diagram.png
[W jaki sposób klienci używają usługi Notification Hubs]: http://azure.microsoft.com/services/notification-hubs
[Przewodniki i samouczki dotyczące usługi Notification Hubs]: http://azure.microsoft.com/documentation/services/notification-hubs
[iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
[Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
[uniwersalnych systemu Windows]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
[Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
[ Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
[Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
[platformy Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
[Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
[Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
[App Service Mobile Apps]: https://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop/
[templates]: notification-hubs-templates-cross-platform-push-messages.md
[portalu Azure]: https://portal.azure.com
[tags]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)
