---
title: Co to jest usługa Azure Notification Hubs?
description: Dowiedz się, jak dodawać możliwości powiadomień push przy użyciu usługi Azure Notification Hubs.
author: jwargo
manager: patniko
editor: spelluru
services: notification-hubs
documentationcenter: ''
ms.assetid: fcfb0ce8-0e19-4fa8-b777-6b9f9cdda178
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 04/30/2019
ms.author: jowargo
ms.openlocfilehash: 03d4c269f76a89c43dec253367d07f3bf71a06d8
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141209"
---
# <a name="what-is-azure-notification-hubs"></a>Co to jest usługa Azure Notification Hubs?

Usługa Azure Notification Hubs oferuje łatwy w użyciu, skalowany w poziomie mechanizm, który umożliwia wysyłanie powiadomień do dowolnej platformy (iOS, Android, Windows, Kindle, Baidu, itp.) z poziomu dowolnego zaplecza (w chmurze lub w środowisku lokalnym). Usługa Notification Hubs działa świetnie w scenariuszach przeznaczonych dla firm i użytkowników. Poniżej przedstawiono kilka przykładowych scenariuszy:

- Wysyłanie powiadomień o najważniejszych wiadomościach do milionów osób z małym opóźnieniem.
- Wysyłanie kuponów opartych na lokalizacji do segmentów zainteresowanych użytkowników.
- Wysyłanie powiadomień o zdarzeniach do użytkowników lub grup dla aplikacji związanych z mediami, sportem i finansami oraz gier.
- Wypychanie promocyjnej zawartości do aplikacji w celu zaangażowania klientów i wprowadzania aplikacji na rynek.
- Powiadamianie użytkowników o zdarzeniach dotyczących przedsiębiorstwa, takich jak nowe wiadomości lub elementy robocze.
- Wysyłanie kodów uwierzytelniania wieloskładnikowego.

## <a name="what-are-push-notifications"></a>Co to są powiadomienia push?

Powiadomienia wypychane jest formą komunikacja użytkownika aplikacji, gdzie użytkownicy aplikacji mobilnych są powiadamiani, niektórych informacji żądanego, zazwyczaj w oknie podręcznym lub w oknach dialogowych na urządzeniu przenośnym. Użytkownicy zazwyczaj wybrać do wyświetlania lub Odrzuć komunikat; Po wybraniu opcji poprzedniej wersji portalu zostanie otwarty aplikacjami mobilnymi, które przekazują powiadomienia. Niektóre powiadomienia są silent - dostarczane w tle dla aplikacji do przetwarzania w tle i podejmowania decyzji.

Powiadomienia push są istotne w przypadku aplikacji użytkowników, ponieważ powodują zwiększenie zaangażowania i użycia aplikacji, a w przypadku aplikacji dla przedsiębiorstw, ponieważ są używane podczas codziennej komunikacji obejmującej informacje biznesowe. Jest najlepiej komunikacja użytkownika aplikacji, ponieważ jest energii dla urządzeń przenośnych, elastyczność i nadawcy powiadomień i dostępne, gdy odpowiednie aplikacje nie są aktywne.

Aby uzyskać więcej informacji o powiadomieniach push dla kilku popularnych platform, zobacz następujące tematy:

- [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
- [iOS](https://developer.apple.com/notifications/)
- [Windows](https://msdn.microsoft.com/library/windows/apps/hh779725.aspx)

## <a name="how-push-notifications-work"></a>Jak działają powiadomienia push?

Powiadomienia push są dostarczane przy użyciu infrastruktur poszczególnych platform nazywanych *systemami powiadomień platformy* (PNS, Platform Notification System). Oferują one podstawowe funkcje wypychania, które służą do dostarczania komunikatu do urządzenia przy użyciu podanego dojścia i nie mają wspólnego interfejsu. Aby wysłać powiadomienie do wszystkich klientów dla systemów Android, iOS i Windows wersje aplikacji, deweloper muszą współpracować z Apple Push Notification Service(APNS), Firebase Cloud Messaging(FCM) i Service(WNS) powiadomień Windows osobno.

Poniżej przedstawiono sposób działania na wysokim poziomie:

1. Aplikacja zdecyduje się, że chce otrzymać powiadomienie, dzięki czemu kontaktowania się z systemu powiadomień platformy dla platformy docelowej, gdzie aplikacja jest uruchomiona i żąda uchwyt wypychania unikatowy i tymczasowe. Typ dojścia zależy od systemu (na przykład usługi WNS używa identyfikatorów URI podczas APNS używa tokenów).
2. Aplikacja kliencka przechowuje to dojście w zapleczu aplikacji lub dostawcą.
3. Aby wysłać powiadomienie wypychane, zaplecza aplikacji kontaktuje się z systemu powiadomień platformy przy użyciu dojścia do obiektu docelowego aplikacji określonego klienta.
4. System powiadomień platformy przekazuje następnie powiadomienie do urządzenia określonego przez dojście.

![Przepływ pracy powiadomienia push](./media/notification-hubs-overview/registration-diagram.png)

## <a name="the-challenges-of-push-notifications"></a>Wyzwania związane z powiadomieniami push

Systemy powiadomień platformy to zaawansowane rozwiązania. Deweloper aplikacji musi jednak wykonać dużo pracy, aby zaimplementować nawet typowe scenariusze dotyczące powiadomień push, takie jak emitowanie powiadomień push do segmentów użytkowników.

Wypychanie powiadomień wymaga złożonej infrastruktury niezwiązanej z główną logiką biznesową aplikacji. Niektóre wyzwania dotyczące infrastruktury to:

- **Zależności dotyczące platformy**
  - Zaplecze wymaga złożone i twardych Obsługa logikę zależny od platformy do wysyłania powiadomień do urządzeń na różnych platformach, ponieważ PNSes nie są jednolite.
- **Skalowanie**
  - Zgodnie z zaleceniami dotyczącymi systemu powiadomień platformy tokeny urządzeń muszą być odświeżane przy każdym uruchomieniu aplikacji. Wewnętrznej bazy danych zajmuje się dużą ilość ruchu sieciowego i bazy danych programu access tylko w celu zapewnienia aktualności tokenów. Gdy wzrośnie liczba urządzeń do setek, tysięcy lub milionów, koszt tworzenia i obsługi tej infrastruktury jest ogromnych.
  - Większość systemów powiadomień platformy nie obsługuje emisji do wielu urządzeń. Prosta emisja do milionów urządzeń powoduje miliony wywołań systemów powiadomień platformy. Skalowanie tej ilości ruchu sieciowego z minimalnym opóźnieniem to poważne zadanie.
- **Routing**
  - Mimo że systemy powiadomień platformy umożliwiają wysyłanie komunikatów do urządzeń, większości powiadomień aplikacji jest przeznaczona dla użytkowników lub grup zainteresowań. Zaplecze musi obsługiwać rejestr, aby kojarzyć urządzenia z grupami zainteresowań, użytkownikami, właściwościami itd. Ten narzut zwiększa czas wprowadzania aplikacji na rynek oraz koszty jej obsługi.

## <a name="why-use-azure-notification-hubs"></a>Dlaczego warto korzystać z usługi Azure Notification Hubs?

Usługa Notification Hubs eliminuje złożoność wszystkie skojarzone z wypychanie powiadomień na własne przepływy od aplikacji zaplecza. Jej międzyplatformowa, skalowana w poziomie infrastruktura powiadomień push zmniejsza ilość kodowania i upraszcza zaplecze. Dzięki usłudze Notification Hubs urządzenia są odpowiedzialne jedynie za rejestrację swoich dojść systemu powiadomień platformy w centrum, a zaplecze wysyła komunikaty do użytkowników lub grup zainteresowań, jak przedstawiono na poniższym rysunku:

![Diagram centrum powiadomień](./media/notification-hubs-overview/notification-hub-diagram.png)

Centra powiadomień to gotowy do użycia aparat wypychania, który oferuje następujące korzyści:

- **Obsługa wielu platform**
  - Obsługa wszystkich głównych platform wypychania, w tym systemów iOS, Android, Windows, Kindle i Baidu.
  - Wspólny interfejs wypychania do wszystkich platform w formatach specyficznych dla platformy lub niezależnych od platformy bez zadań specyficznych dla platformy.
  - Zarządzanie dojściami urządzeń w jednym miejscu.
- **Obsługa wielu zapleczy**
  - Chmura lub środowisko lokalne
  - .NET, Node.js, Java itd.
- **Rozbudowany zestaw wzorców dostarczania**
  - Emisja do jednej lub wielu platform: możliwość natychmiastowej emisji do milionów urządzeń na wielu platformach przy użyciu pojedynczego wywołania interfejsu API.
  - Wypychanie do urządzenia: można kierować powiadomienia do poszczególnych urządzeń.
  - Wypychanie do użytkownika: funkcja tagów i szablonów ułatwia komunikowanie się ze wszystkimi urządzeniami użytkownika na wielu platformach.
  - Wypychanie do segmentu przy użyciu tagów dynamicznych: funkcja tagów ułatwia dzielenie urządzeń na segmenty i wypychanie do nich zgodnie z potrzebami, bez względu na to, czy jest to wysyłanie do jednego segmentu, czy też do wyrażenia złożonego z segmentów (na przykład: aktywny I mieszka w Warszawie NIE nowy użytkownik). Zamiast ograniczać się do publikowania/subskrybowania, można aktualizować tagi urządzeń w dowolnym miejscu i czasie.
  - Wypychanie zlokalizowane: funkcja szablonów ułatwia osiągnąć lokalizację bez wpływu na kod zaplecza.
  - Wypychanie dyskretne: można włączyć wzorzec wypychania i ściągania, wysyłając dyskretne powiadomienia do urządzeń i wyzwalając je w celu zakończenia wybranych operacji ściągnięcia lub akcji.
  - Wypychanie zaplanowane: można zaplanować wysyłanie powiadomień w dowolnym czasie.
  - Wypychanie bezpośrednie: można pominąć rejestrowanie urządzeń w usłudze Notification Hubs i bezpośrednio wypchnąć partię do listy dojść urządzeń.
  - Wypychanie spersonalizowane: zmienne wypychania urządzenia ułatwiają wysyłanie specyficznych dla urządzenia, spersonalizowanych powiadomień push przy użyciu dostosowanych par klucz-wartość.
- **Rozbudowane dane telemetryczne**
  - Ogólne dane telemetryczne dotyczące wypychania, urządzeń, błędów i operacji są dostępne programowo oraz w witrynie Azure Portal.
  - Funkcja danych telemetrycznych dla poszczególnych komunikatów śledzi każde wypchnięcie z początkowego wywołania żądania do usługi Notification Hubs, które pozwala na pomyślne utworzenie partii operacji wypychania.
  - Funkcja informacji zwrotnych z systemu powiadomień platformy przekazuje wszystkie informacje zwrotne z systemów powiadomień platformy, aby pomóc w debugowaniu.
- **Skalowalność**
  - Wysyłanie szybkich komunikatów do milionów urządzeń bez konieczności ponownego tworzenia architektury lub dzielenia na fragmenty dla urządzeń.
- **Bezpieczeństwo**
  - Uwierzytelnianie przy użyciu wpisu tajnego dostępu współdzielonego (SAS, Shared Access Secret) lub uwierzytelnianie federacyjne.

## <a name="next-steps"></a>Kolejne kroki

Rozpocznij tworzenie centrum powiadomień i korzystanie z niego, wykonując kroki opisane w temacie [Samouczek: wysyłanie powiadomień push do aplikacji mobilnych](notification-hubs-android-push-notification-google-fcm-get-started.md).

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
