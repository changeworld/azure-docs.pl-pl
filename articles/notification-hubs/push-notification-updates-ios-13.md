---
title: Usługi Azure Notification Hubs aktualizacje systemu iOS 13 | Dokumenty firmy Microsoft
description: Dowiedz się więcej o przełomowych zmianach w usłudze Azure Notification Hubs dla systemu iOS 13
author: sethmanheim
ms.author: sethm
ms.date: 10/16/2019
ms.topic: article
ms.service: notification-hubs
ms.reviewer: jowargo
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 697e8ba9c9f27e8d5644e3a78950ff006290efe7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74228140"
---
# <a name="azure-notification-hubs-updates-for-ios-13"></a>Aktualizacje usługi Azure Notification Hubs dla systemu iOS 13

Apple niedawno dokonało pewnych zmian w swojej publicznej usłudze push; zmiany w większości wyrównane z wersjami iOS 13 i Xcode. W tym artykule opisano wpływ tych zmian na usługi Azure Notification Hubs.

## <a name="apns-push-payload-changes"></a>Zmiany ładunku wypychanego APNS

### <a name="apns-push-type"></a>Typ wypychania APNS

Firma Apple wymaga teraz, aby deweloperzy identyfikowali `apns-push-type` powiadomienia jako powiadomienia alertów lub powiadomienia w tle za pośrednictwem nowego nagłówka w interfejsie API usługi APNS. Zgodnie z [dokumentacją Apple](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns): "Wartość tego nagłówka musi dokładnie odzwierciedlać zawartość ładunku powiadomienia. Jeśli występuje niezgodność lub jeśli w wymaganych systemach brakuje nagłówka, nazwy APN mogą zwrócić błąd, opóźnić dostarczenie powiadomienia lub całkowicie go usunąć."

Deweloperzy muszą teraz ustawić ten nagłówek w aplikacjach, które wysyłają powiadomienia za pośrednictwem usługi Azure Notification Hubs. Ze względu na ograniczenia techniczne klienci muszą używać uwierzytelniania opartego na tokenie dla poświadczeń apns z żądaniami, które zawierają ten atrybut. Jeśli używasz uwierzytelniania opartego na certyfikatach dla poświadczeń apns, należy przełączyć się do korzystania z uwierzytelniania opartego na tokenie.

Poniższe przykłady kodu pokazują, jak ustawić ten atrybut nagłówka w żądaniach powiadomień wysyłanych za pośrednictwem usługi Azure Notification Hubs.

#### <a name="template-notifications---net-sdk"></a>Powiadomienia o szablonie — SDK .NET

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var tempprop = new Dictionary<string, string> {{"message", "value"}};
var notification = new TemplateNotification(tempprop);
notification.Headers = headers;
await hub.SendNotificationAsync(notification);
```

#### <a name="native-notifications---net-sdk"></a>Powiadomienia natywne — SDK .NET

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

#### <a name="direct-rest-calls"></a>Bezpośrednie połączenia REST

```csharp
var request = new HttpRequestMessage(method, $"<resourceUri>?api-version=2017-04");
request.Headers.Add("Authorization", createToken(resourceUri, KEY_NAME, KEY_VALUE));
request.Headers.Add("ServiceBusNotification-Format", "apple");
request.Headers.Add("apns-push-type", "alert");
```

Aby ułatwić podczas tego przejścia, gdy usługa Azure Notification Hubs `apns-push-type` wykryje powiadomienie, które nie ma zestawu, usługa wywnioskuje typ wypychania z żądania powiadomienia i automatycznie ustawia wartość. Należy pamiętać, że należy skonfigurować usługi Azure Notification Hubs, aby używać uwierzytelniania opartego na tokenie, aby ustawić wymagany nagłówek; Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie oparte na tokenach (HTTP/2) dla usługi APNS](notification-hubs-push-notification-http2-token-authentification.md).

## <a name="apns-priority"></a>Priorytet APNS

Inną drobną zmianą, ale która wymaga zmiany w aplikacji wewnętrznej bazy danych, która `apns-priority` wysyła powiadomienia, jest wymóg, że dla powiadomień w tle nagłówek musi być teraz ustawiony na 5. Wiele aplikacji `apns-priority` ustawić nagłówek na 10 (wskazując natychmiastowe dostarczanie), lub nie ustawić go i uzyskać wartość domyślną (która jest również 10).

Ustawienie tej wartości na 10 nie jest już dozwolone dla powiadomień w tle i należy ustawić wartość dla każdego żądania. Firma Apple nie będzie dostarczać powiadomień w tle, jeśli brakuje tej wartości. Przykład:

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "background"}, { "apns-priority", "5" }};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

## <a name="sdk-changes"></a>Zmiany w SDK

Przez lata deweloperzy systemu `description` iOS `deviceToken` używali atrybutu danych wysyłanych do delegata tokenu wypychanego, aby wyodrębnić token wypychania używany przez aplikację zaplecza do wysyłania powiadomień do urządzenia. W xcode 11 `description` ten atrybut został zmieniony na inny format. Istniejący kod, który deweloperzy używane dla tego atrybutu jest teraz uszkodzony. Zaktualizowaliśmy zestaw SDK usługi Azure Notification Hubs w celu uwzględnienia tej zmiany, dlatego zaktualizuj zestaw SDK używany przez aplikacje do wersji 2.0.4 lub [nowszej w witrynie Azure Notification Hubs iOS SDK](https://github.com/Azure/azure-notificationhubs-ios).
