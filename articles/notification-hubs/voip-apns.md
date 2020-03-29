---
title: Wysyłanie powiadomień USŁUGI APNS VOIP za pomocą usługi Azure Notification Hubs
description: Dowiedz się, jak wysyłać powiadomienia USŁUGI APNS VOIP za pośrednictwem usługi Azure Notification Hubs (oficjalnie nieobjętej).
author: sethmanheim
ms.author: sethm
ms.date: 3/23/2020
ms.topic: how-to
ms.service: notification-hubs
ms.openlocfilehash: c99af881b8f93b75633741c2352dc5df17dd2963
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80146890"
---
# <a name="use-apns-voip-through-notification-hubs-not-officially-supported"></a>Użyj apns VOIP za pośrednictwem Centrum powiadomień (nie jest oficjalnie obsługiwane)

Istnieje możliwość korzystania z powiadomień VOIP usługi APNS za pośrednictwem usługi Azure Notification Hubs; nie ma jednak oficjalnego poparcia dla tego scenariusza.

## <a name="considerations"></a>Zagadnienia do rozważenia

Jeśli nadal zdecydujesz się wysyłać powiadomienia USŁUGI APNS VOIP za pośrednictwem Centrów powiadomień, pamiętaj o następujących ograniczeniach:

- Wysłanie powiadomienia VOIP `apns-topic` wymaga, aby nagłówek został ustawiony `.voip` na identyfikator pakietu aplikacji + sufiks. Na przykład dla przykładowej aplikacji o `com.microsoft.nhubsample`identyfikatorze pakietu `apns-topic` nagłówek powinien być ustawiony na`com.microsoft.nhubsample.voip.`

   Ta metoda nie działa dobrze z usługi Azure Notification Hubs, ponieważ identyfikator pakietu aplikacji musi być skonfigurowany jako część poświadczeń usługi APNS centrum i nie można zmienić wartości. Ponadto centra powiadomień nie zezwalają `apns-topic` na zastąpienie wartości nagłówka w czasie wykonywania.

   Aby wysyłać powiadomienia VOIP, należy skonfigurować oddzielne `.voip` centrum powiadomień o identyfikatorze pakietu aplikacji.

- Wysłanie powiadomienia VOIP `apns-push-type` wymaga ustawienia nagłówka `voip`na wartość .

   Aby pomóc klientom w przejściu do systemu iOS 13, Centra `apns-push-type` powiadomień próbują wywnioskować poprawną wartość nagłówka. Logika wnioskowania jest celowo proste, w celu uniknięcia łamania standardowych powiadomień. Niestety ta metoda powoduje problemy z powiadomieniami VOIP, ponieważ Firma Apple traktuje powiadomienia VOIP jako szczególny przypadek, który nie jest zgodny z tymi samymi regułami co standardowe powiadomienia.

   Aby wysyłać powiadomienia VOIP, należy określić `apns-push-type` jawną wartość nagłówka.

- Centra powiadomień ograniczają ładunki usługi APNS do 4 KB, co udokumentował apple. W przypadku powiadomień VOIP firma Apple zezwala na ładowanie ładunków o masie do 5 KB. Centra powiadomień nie rozróżniają powiadomień standardowych i voip; w związku z tym wszystkie powiadomienia są ograniczone do 4 KB.

   Aby wysyłać powiadomienia VOIP, nie należy przekraczać limitu rozmiaru ładunku 4 KB.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, skorzystaj z następujących linków:

- [Dokumentacja `apns-topic` `apns-push-type` nagłówków i wartości, w tym specjalne przypadki powiadomień VOIP](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns).

- [Dokumentacja limitu rozmiaru ładunku](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification).

- [Aktualizacje centrów powiadomień dla systemu iOS 13](push-notification-updates-ios-13.md#apns-push-type).
