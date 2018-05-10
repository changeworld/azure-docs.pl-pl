---
title: Zrozumienie punkty końcowe Centrum IoT Azure | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — informacje na temat Centrum IoT punkty końcowe skierowane do urządzenia i połączonej usługi.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 57ba52ae-19c6-43e4-bc6c-d8a5c2476e95
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: d9378a084d8ff421539a445986a5a18d073f3ce6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="reference---iot-hub-endpoints"></a>Odwołanie — punkty końcowe Centrum IoT

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>Nazwy Centrum IoT

Nazwy hostów punktów końcowych w portalu w Centrum IoT można znaleźć **omówienie** bloku. Domyślnie nazwa DNS Centrum IoT wygląda następująco: `{your iot hub name}.azure-devices.net`.

Usługi Azure DNS umożliwia utworzenie niestandardowej nazwy DNS dla Centrum IoT. Aby uzyskać więcej informacji, zobacz [użycia usługi Azure DNS, aby określić ustawienia domeny niestandardowej dla usługi Azure](../dns/dns-custom-domain.md).

## <a name="list-of-built-in-iot-hub-endpoints"></a>Lista wbudowanych punkty końcowe Centrum IoT

Centrum IoT Azure to usługa wielodostępne, która udostępnia jego funkcje do różnych złośliwych użytkowników. Na poniższym diagramie przedstawiono różne punkty końcowe, że przedstawia Centrum IoT.

![Punkty końcowe centrum IoT Hub][img-endpoints]

Poniżej przedstawiono listę punktów końcowych:

* **Dostawca zasobów**. Opisuje dostawcę zasobów Centrum IoT [usługi Azure Resource Manager] [ lnk-arm] interfejsu. Ten interfejs umożliwia właściciele subskrypcji platformy Azure do tworzenia i usuwania centra IoT i aktualizowanie właściwości Centrum IoT. Centrum IoT właściwości regulują [zasad zabezpieczeń na poziomie koncentratora][lnk-accesscontrol], w przeciwieństwie do kontroli dostępu na poziomie urządzenia i funkcjonalności opcji chmury do urządzenia i urządzenia do chmury do obsługi komunikatów. Dostawca zasobów Centrum IoT umożliwia także [wyeksportować tożsamości urządzenia][lnk-importexport].
* **Zarządzanie tożsamościami urządzenia**. Każdy Centrum IoT udostępnia zestaw punktów końcowych HTTPS REST do zarządzania tożsamościami urządzenia (Tworzenie, pobieranie, aktualizowanie i usuwanie). [Tożsamości urządzenia] [ lnk-device-identities] służą do urządzenia uwierzytelniania i kontroli dostępu.
* **Zarządzanie urządzeniami dwie**. Każdy Centrum IoT udostępnia zestaw punkt końcowy HTTPS REST połączonej usługi do zapytania i aktualizacji [twins urządzenia] [ lnk-twins] (aktualizacja znaczniki i właściwości).
* **Zadania zarządzania**. Każdy Centrum IoT udostępnia zestaw połączonej usługi REST protokołu HTTPS z punktu końcowego do wykonywania zapytań i zarządzanie [zadania][lnk-jobs].
* **Punkty końcowe urządzenia**. Dla każdego urządzenia w rejestrze tożsamości Centrum IoT udostępnia zestaw punktów końcowych:

  * *Wysyłanie komunikatów urządzenia do chmury*. Urządzenie korzysta z tego punktu końcowego do [wysyłać urządzenia do chmury][lnk-d2c].
  * *Komunikaty chmury do urządzenia*. Urządzenie korzysta z tego punktu końcowego do odbierania docelowe [wiadomości chmury do urządzenia][lnk-c2d].
  * *Zainicjuj przekazywania plików*. Urządzenie korzysta z tego punktu końcowego do odbierania identyfikatora URI połączenia SAS magazynu Azure z Centrum IoT na [przekazać plik][lnk-upload].
  * *Pobierania i aktualizowania urządzenia dwie właściwości*. Urządzenie korzysta z tego punktu końcowego dostęp do jego [dwie urządzenia][lnk-twins]jego właściwości.
  * *Odbierania żądań metoda bezpośrednia*. Urządzenie korzysta z tego punktu końcowego do nasłuchiwania [metoda bezpośrednia][lnk-methods]tego żądania.

    Te punkty końcowe są udostępniane za pomocą [MQTT v3.1.1][lnk-mqtt], HTTPS 1.1 i [protokołu AMQP 1.0] [ lnk-amqp] protokołów. Jest również dostępny za pośrednictwem protokołu AMQP [Websocket] [ lnk-websockets] na porcie 443.

* **Punkty końcowe usługi**. Każdy Centrum IoT udostępnia zestaw punktów końcowych dla sieci zaplecza rozwiązania do komunikowania się z urządzeniami. Z jednym wyjątkiem te punkty końcowe są dostępne tylko za pomocą [AMQP] [ lnk-amqp] protokołu. Punkt końcowy wywołania metody jest udostępniany za pośrednictwem protokołu HTTPS.
  
  * *Komunikaty urządzenia do chmury*. Ten punkt końcowy jest zgodny z [Azure Event Hubs][lnk-event-hubs]. Usługi zaplecza służy do odczytu [wiadomości urządzenia do chmury] [ lnk-d2c] wysyłanych przez urządzenia. Można tworzyć niestandardowe punkty końcowe na oprócz tego wbudowanych punktu końcowego Centrum IoT.
  * *Chmury do urządzenia wysyłać i odbierać potwierdzeń dostarczenia*. Te punkty końcowe Włącz z zaplecza rozwiązania do wysyłania niezawodnej [wiadomości chmury do urządzenia][lnk-c2d]i otrzymywanie dostarczania odpowiednich lub wygaśnięcia potwierdzenia.
  * *Odbieranie powiadomień pliku*. Tego obsługi komunikatów punktu końcowego pozwala na otrzymywanie powiadomień o urządzeniach udało się przekazać pliku. 
  * *Bezpośrednie wywołania metody*. Ten punkt końcowy pozwala wywoływać usługi zaplecza [metoda bezpośrednia] [ lnk-methods] na urządzeniu.
  * *Monitorowanie zdarzeń operacji odbioru*. Ten punkt końcowy pozwala na monitorowanie zdarzeń, jeśli Centrum IoT został skonfigurowany do wysyłania ich operacji odbioru. Aby uzyskać więcej informacji, zobacz [operacji centrum IoT monitorowania][lnk-operations-mon].

[Azure IoT SDK] [ lnk-sdks] artykule opisano różne sposoby dostęp do tych punktów końcowych.

Użyj wszystkie punkty końcowe Centrum IoT [TLS] [ lnk-tls] protokołu i żaden punkt końcowy kiedykolwiek jest uwidaczniana w postaci niezaszyfrowanej niezabezpieczone kanałach.

## <a name="custom-endpoints"></a>Niestandardowe punkty końcowe

Możesz połączyć istniejące usługi platformy Azure w ramach subskrypcji, z Centrum IoT na działanie jako punktów końcowych do rozsyłania wiadomości. Te punkty końcowe działać jako punkty końcowe usługi i są używane jako wychwytywanie tras wiadomości. Urządzenia nie można zapisać bezpośrednio dodatkowe punkty końcowe. Aby dowiedzieć się więcej na temat tras wiadomości, zobacz wpis przewodnik dewelopera na [wysyłanie i odbieranie komunikatów z Centrum IoT][lnk-devguide-messaging].

Centrum IoT obsługuje obecnie następujących usług platformy Azure jako dodatkowe punkty końcowe:

* Kontenery magazynu Azure
* Event Hubs
* Kolejki usługi Service Bus
* Tematy dotyczące usługi Service Bus

Centrum IoT wymaga dostępu do tych punktów końcowych usługi zapisu do rozsyłania wiadomości do pracy. Jeśli konfigurujesz punktami końcowymi za pośrednictwem portalu Azure, niezbędne uprawnienia są dodawane dla Ciebie. Upewnij się, że konfigurowania usługi do obsługi oczekiwanych przepływności. Podczas pierwszej konfiguracji rozwiązania IoT, konieczne może być monitorowanie dodatkowych punktów końcowych i wprowadź wymagane zmiany do rzeczywistego obciążenia.

Jeśli komunikat dopasowania wielu kieruje wszystkie wskaż tego samego punktu końcowego, Centrum IoT dostarcza wiadomość do tego punktu końcowego tylko raz. W związku z tym jest konieczne konfigurowanie deduplikacji na kolejki usługi Service Bus lub tematu. W kolejkach podzielonym na partycje koligacji partycji gwarantuje porządkowania wiadomości.

Limitów liczby można dodać punktów końcowych, zobacz [przydziały i ograniczenia przepustowości][lnk-devguide-quotas].

### <a name="when-using-azure-storage-containers"></a>Korzystając z kontenerami usługi Azure Storage

Centrum IoT obsługuje tylko zapisywania danych do usługi Azure Storage kontenerów jako obiekty BLOB w [Apache Avro](http://avro.apache.org/) format. Centrum IoT partii wiadomości i zapisuje dane do obiektu blob zawsze, gdy:

* Partii osiągnie określony rozmiar.
* Lub upływie pewnego czasu.

Centrum IoT będą zapisywane pusty obiekt typu blob, jeśli nie ma żadnych danych do zapisania.

Centrum IoT domyślnie następującej konwencji nazewnictwa plików:

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Można użyć niezależnie od nazw Konwencji, które mają plików, jednak należy użyć wszystkie wyświetlone tokenów.

### <a name="when-using-service-bus-queues-and-topics"></a>Korzystając z tematów i kolejek usługi Service Bus

Tematy dotyczące używany jako punkty końcowe Centrum IoT nie może mieć i kolejek usługi Service Bus **sesji** lub **wykrywania duplikatów** włączone. Jeśli dowolna z tych opcji są włączone, punkt końcowy jest wyświetlany jako **informujący** w portalu Azure.

## <a name="field-gateways"></a>Pole bram

Rozwiązania IoT *bramy pola* znajduje się między urządzeniami a punktami końcowymi Centrum IoT. Jest to zazwyczaj znajduje się w pobliżu urządzenia. Urządzenia komunikują się bezpośrednio z bramą pola przy użyciu protokołu obsługiwanego przez urządzenia. Brama pola łączy do punktu końcowego Centrum IoT przy użyciu protokołu, który jest obsługiwany przez Centrum IoT. Brama pole może być dedykowanym urządzeniem sprzętowym lub niskiego poboru energii komputer z oprogramowaniem bram.

Można użyć [Azure IoT krawędzi] [ lnk-iot-edge] do zaimplementowania bramy pola. Krawędź IoT oferuje funkcje, takie jak multipleksowania komunikacji z różnych urządzeń do tego samego połączenia Centrum IoT.

## <a name="next-steps"></a>Kolejne kroki

Inne tematy dokumentacji, w tym przewodniku deweloperów Centrum IoT obejmują:

* [Język zapytań Centrum IoT urządzenia twins, zadań i rozsyłania wiadomości][lnk-devguide-query]
* [Przydziały i ograniczenia przepustowości][lnk-devguide-quotas]
* [Obsługa MQTT Centrum IoT][lnk-devguide-mqtt]

[lnk-iot-edge]: https://github.com/Azure/iot-edge

[img-endpoints]: ./media/iot-hub-devguide-endpoints/endpoints.png
[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../azure-resource-manager/resource-group-overview.md
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/

[lnk-tls]: https://tools.ietf.org/html/rfc5246


[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-accesscontrol]: iot-hub-devguide-security.md#access-control-and-permissions
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-device-identities]: iot-hub-devguide-identity-registry.md
[lnk-upload]: iot-hub-devguide-file-upload.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md

[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[lnk-operations-mon]: iot-hub-operations-monitoring.md
