---
title: Co to jest usługa Azure Notification Hubs?
description: Dowiedz się, jak dodawać możliwości powiadomień push przy użyciu usługi Azure Notification Hubs.
author: sethmanheim
manager: femila
editor: tjsomasundaram
services: notification-hubs
documentationcenter: ''
ms.assetid: fcfb0ce8-0e19-4fa8-b777-6b9f9cdda178
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 03/23/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 04/30/2019
ms.openlocfilehash: f5b59d1669d89c73b93199d1f833da149003d399
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80159340"
---
# <a name="what-is-azure-notification-hubs"></a>Co to jest usługa Azure Notification Hubs?

Usługi Azure Notification Hubs zapewniają łatwy w użyciu i skalowane w poziomie aparat wypychania, który umożliwia wysyłanie powiadomień do dowolnej platformy (iOS, Android, Windows, itp.) z dowolnego zaplecza (chmury lub lokalnie). Usługa Notification Hubs działa świetnie w scenariuszach przeznaczonych dla firm i użytkowników. Poniżej przedstawiono kilka przykładowych scenariuszy:

- Wysyłanie powiadomień o najważniejszych wiadomościach do milionów osób z małym opóźnieniem.
- Wysyłanie kuponów opartych na lokalizacji do segmentów zainteresowanych użytkowników.
- Wysyłanie powiadomień o zdarzeniach do użytkowników lub grup dla aplikacji związanych z mediami, sportem i finansami oraz gier.
- Wypychanie promocyjnej zawartości do aplikacji w celu zaangażowania klientów i wprowadzania aplikacji na rynek.
- Powiadamiaj użytkowników o zdarzeniach przedsiębiorstwa, takich jak nowe wiadomości i elementy robocze.
- Wysyłanie kodów uwierzytelniania wieloskładnikowego.

## <a name="what-are-push-notifications"></a>Co to są powiadomienia push?

Powiadomienia wypychane są formą komunikacji między aplikacjami, w której użytkownicy aplikacji mobilnych są powiadamiani o pewnych żądanych informacjach, zwykle w wyskakującym okienku lub oknie dialogowym na urządzeniu przenośnym. Użytkownicy zazwyczaj wybrać, aby wyświetlić lub odrzucić wiadomość; wybór pierwszego otwiera aplikację mobilną, która przekazała powiadomienie. Niektóre powiadomienia są ciche - dostarczane za kulisami, aby aplikacja przetwarzała i decydowania, co robić.

Powiadomienia push są istotne w przypadku aplikacji użytkowników, ponieważ powodują zwiększenie zaangażowania i użycia aplikacji, a w przypadku aplikacji dla przedsiębiorstw, ponieważ są używane podczas codziennej komunikacji obejmującej informacje biznesowe. Jest to najlepsza komunikacja między aplikacjami, ponieważ jest energooszczędna dla urządzeń mobilnych, elastyczna dla nadawców powiadomień i dostępna, gdy odpowiednie aplikacje nie są aktywne.

> [!NOTE]
> Usługa Azure Notification Hubs oficjalnie nie obsługuje powiadomień wypychanych Voice Over Internet Protocol (VOIP); Jednak [w tym artykule opisano, jak można używać powiadomień VOIP usługi APNS](voip-apns.md) za pośrednictwem usługi Azure Notification Hubs.

Aby uzyskać więcej informacji o powiadomieniach push dla kilku popularnych platform, zobacz następujące tematy:

- [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
- [iOS](https://developer.apple.com/notifications/)
- [Windows](https://msdn.microsoft.com/library/windows/apps/hh779725.aspx)

## <a name="how-do-push-notifications-work"></a>Jak działają powiadomienia push?

Powiadomienia push są dostarczane przy użyciu infrastruktur poszczególnych platform nazywanych *systemami powiadomień platformy* (PNS, Platform Notification System). Oferują one podstawowe funkcje wypychania, aby dostarczyć wiadomość do urządzenia z dostarczonym uchwytem i nie mają wspólnego interfejsu. Aby wysłać powiadomienie do wszystkich klientów w wersjach aplikacji dla systemu Android, iOS i Windows, deweloper musi pracować oddzielnie z usługą apple push notification service (APNS), Firebase Cloud Messaging (FCM) i Usługą powiadomień systemu Windows (WNS).

Poniżej przedstawiono sposób działania na wysokim poziomie:

1. Aplikacja chce otrzymać powiadomienie, więc kontaktuje się z usługą PNS dla platformy docelowej, na której aplikacja jest uruchomiona i żąda unikatowego i tymczasowego uchwytu wypychanego. Typ dojścia zależy od systemu (na przykład WNS używa identyfikatorów URI, podczas gdy apns używa tokenów).
2. Aplikacja kliencka przechowuje ten dojście w wewnętrznej bazy danych lub dostawcy aplikacji.
3. Aby wysłać powiadomienie wypychanego, wewnętrznej bazy danych aplikacji kontaktuje się z usługi PNS przy użyciu dojścia do docelowej aplikacji klienckiej.
4. System powiadomień platformy przekazuje następnie powiadomienie do urządzenia określonego przez dojście.

![Przepływ pracy powiadomienia push](./media/notification-hubs-overview/registration-diagram.png)

## <a name="the-challenges-of-push-notifications"></a>Wyzwania związane z powiadomieniami push

Systemy powiadomień platformy to zaawansowane rozwiązania. Deweloper aplikacji musi jednak wykonać dużo pracy, aby zaimplementować nawet typowe scenariusze dotyczące powiadomień push, takie jak emitowanie powiadomień push do segmentów użytkowników.

Wysyłanie powiadomień wypychanych wymaga złożonej infrastruktury, która nie jest związana z główną logiką biznesową aplikacji. Niektóre z wyzwań infrastrukturalnych to:

- **Zależności dotyczące platformy**
  - Wewnętrznej bazy danych wymaga złożonych i trudnych do utrzymania logiki zależne od platformy do wysyłania powiadomień do urządzeń na różnych platformach, jak PNSes nie są ujednolicone.
- **Skali**
  - Zgodnie z wytycznymi systemu PNS tokeny urządzeń muszą być odświeżane przy każdym uruchomieniu aplikacji. Wewnętrznej bazy danych zajmuje się dużą ilość ruchu i dostępu do bazy danych tylko po to, aby zachować tokeny aktualne. Gdy liczba urządzeń wzrasta do setek, tysięcy lub milionów, koszt utworzenia i utrzymania tej infrastruktury jest ogromny.
  - Większość systemów powiadomień platformy nie obsługuje emisji do wielu urządzeń. Prosta emisja do milionów urządzeń powoduje miliony wywołań systemów powiadomień platformy. Skalowanie tej ilości ruchu sieciowego z minimalnym opóźnieniem to poważne zadanie.
- **Routing**
  - Chociaż PNSes umożliwiają wysyłanie wiadomości do urządzeń, większość powiadomień aplikacji jest kierowana do użytkowników lub grup interesu. Wewnętrznej bazy danych musi obsługiwać rejestru do skojarzenia urządzeń z grupami interesu, użytkowników, właściwości, itp. To obciążenie zwiększa czas na rynku i koszty konserwacji aplikacji.

## <a name="why-use-azure-notification-hubs"></a>Dlaczego warto korzystać z usługi Azure Notification Hubs?

Centra powiadomień eliminują wszystkie zawiłości związane z samodzielnym wysyłaniem powiadomień wypychanych z wewnętrznej bazy danych aplikacji. Jej międzyplatformowa, skalowana w poziomie infrastruktura powiadomień push zmniejsza ilość kodowania i upraszcza zaplecze. Dzięki usłudze Notification Hubs urządzenia są odpowiedzialne jedynie za rejestrację swoich dojść systemu powiadomień platformy w centrum, a zaplecze wysyła komunikaty do użytkowników lub grup zainteresowań, jak przedstawiono na poniższym rysunku:

![Diagram centrum powiadomień](./media/notification-hubs-overview/notification-hub-diagram.png)

Centra powiadomień to gotowy do użycia silnik wypychania z następującymi zaletami:

- **Obsługa wielu platform**
  - Obsługa wszystkich głównych platform push.
  - Wspólny interfejs wypychania do wszystkich platform w formatach specyficznych dla platformy lub niezależnych od platformy bez zadań specyficznych dla platformy.
  - Zarządzanie dojściami urządzeń w jednym miejscu.
- **Obsługa wielu zapleczy**
  - Chmura lub lokalnie.
  - .NET, Node.js, Java, Python itp.
- **Rozbudowany zestaw wzorców dostarczania**
  - Emitowane na co najmniej jednej platformie: możesz natychmiast emitować do milionów urządzeń na różnych platformach za pomocą jednego wywołania interfejsu API.
  - Wypychanie do urządzenia: można kierować powiadomienia do poszczególnych urządzeń.
  - Wypychanie do użytkownika: tagi i szablony ułatwiają dostęp do wszystkich urządzeń między platformami dla użytkownika.
  - Push do segmentu za pomocą tagów dynamicznych: Funkcja tagów pomaga segmentować urządzenia i wypychać do nich zgodnie z potrzebami, niezależnie od tego, czy wysyłasz do jednego segmentu, czy do wyrażenia segmentów (na przykład aktywny i mieszka w Seattle NIE nowy użytkownik). Zamiast ograniczać się do publikowania i subskrybowania, możesz aktualizować tagi urządzeń w dowolnym miejscu i czasie.
  - Zlokalizowane wypychanie: funkcja szablonów pomaga osiągnąć lokalizację bez wpływu na kod wewnętrznej bazy danych.
  - Wypychanie dyskretne: można włączyć wzorzec wypychania i ściągania, wysyłając dyskretne powiadomienia do urządzeń i wyzwalając je w celu zakończenia wybranych operacji ściągnięcia lub akcji.
  - Zaplanowane wypychanie: możesz zaplanować wysłanie powiadomień w dowolnym momencie.
  - Wypychania bezpośrednie: można pominąć rejestrowanie urządzeń w usłudze Notification Hubs i bezpośrednio wypchnąć partię do listy dojść urządzeń.
  - Spersonalizowane wypychanie: zmienne wypychania urządzeń pomagają wysyłać spersonalizowane powiadomienia push specyficzne dla urządzenia z dostosowanymi parami klucz-wartość.
- **Rozbudowane dane telemetryczne**
  - Ogólne dane telemetryczne wypychania, urządzenia, błędu i operacji są dostępne zarówno w witrynie Azure portal, jak i programowo.
  - Telemetria na wiadomość śledzi każde wypychanie z wywołania początkowego żądania do usługi Centra powiadomień pomyślnie wysyłając wypychanie.
  - Informacje zwrotne systemu powiadomień platformy przekazuje wszystkie opinie z PNSes, aby pomóc w debugowaniu.
- **Skalowalność**
  - Wysyłaj szybkie wiadomości do milionów urządzeń bez ponownego egoiskowania lub dzielenia na fragmenty urządzeń.
- **Zabezpieczenia**
  - Uwierzytelnianie przy użyciu wpisu tajnego dostępu współdzielonego (SAS, Shared Access Secret) lub uwierzytelnianie federacyjne.

## <a name="next-steps"></a>Następne kroki

Rozpocznij tworzenie centrum powiadomień i korzystanie z niego, wykonując kroki opisane w temacie [Samouczek: wypychanie powiadomień do aplikacji mobilnych](notification-hubs-android-push-notification-google-fcm-get-started.md).

[0]: ./media/notification-hubs-overview/registration-diagram.png
[1]: ./media/notification-hubs-overview/notification-hub-diagram.png

[How customers are using Notification Hubs]: https://azure.microsoft.com/services/notification-hubs
[Notification Hubs tutorials and guides]: https://azure.microsoft.com/documentation/services/notification-hubs
[iOS]: https://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
[Android]: https://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
[Windows Universal]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
[Windows Phone]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
[Kindle]: https://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
[Xamarin.iOS]: https://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
[Xamarin.Android]: https://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
[Microsoft.WindowsAzure.Messaging.NotificationHub]: https://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
[Microsoft.ServiceBus.Notifications]: https://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
[App Service Mobile Apps]: https://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop/
[templates]: notification-hubs-templates-cross-platform-push-messages.md
[Azure portal]: https://portal.azure.com
[tags]: (https://msdn.microsoft.com/library/azure/dn530749.aspx)
