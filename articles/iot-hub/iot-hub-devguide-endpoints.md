---
title: Omówienie punktów końcowych usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — informacje na temat usługi IoT Hub punktów końcowych przeznaczonych dla urządzeń i przeznaczonych dla usługi.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: bf23046b8a80b02bc1667f647cb1d475503a8feb
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125780"
---
# <a name="reference---iot-hub-endpoints"></a>Odwołanie - punktów końcowych usługi IoT Hub

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>Nazwy Centrum IoT Hub

Można znaleźć nazwy hosta usługi IoT hub, obsługujący punktów końcowych w portalu na Twoje Centrum **Przegląd** strony. Domyślnie, nazwa DNS usługi IoT hub wygląda następująco: `{your iot hub name}.azure-devices.net`.

System DNS Azure umożliwia tworzenie niestandardowej nazwy DNS dla usługi IoT hub. Aby uzyskać więcej informacji, zobacz [Use Azure DNS to provide custom domain settings for an Azure service](../dns/dns-custom-domain.md) (Korzystanie z usługi Azure DNS w celu udostępnienia niestandardowych ustawień domeny dla usługi platformy Azure).

## <a name="list-of-built-in-iot-hub-endpoints"></a>Lista wbudowanej punktów końcowych usługi IoT Hub

Usługa Azure IoT Hub jest usługą wielodostępną, która udostępnia funkcję do poszczególnych podmiotów. Na poniższym diagramie przedstawiono różne punkty końcowe IoT Hub udostępnia.

![Punkty końcowe centrum IoT Hub][img-endpoints]

Poniższa lista zawiera punkty końcowe:

* **Dostawca zasobów**. Dostawca zasobów usługi IoT Hub udostępnia [usługi Azure Resource Manager] [ lnk-arm] interfejsu. Ten interfejs umożliwia właściciele subskrypcji platformy Azure do tworzenia i usuwania centra IoT i aktualizowanie właściwości Centrum IoT. Właściwości usługi IoT Hub określają [zasady zabezpieczeń na poziomie koncentratora][lnk-accesscontrol], w przeciwieństwie do kontroli dostępu na poziomie urządzenia i funkcjonalności opcje do obsługi komunikatów chmura urządzenie i urządzenia do chmury. Dostawca zasobów usługi IoT Hub umożliwia także [wyeksportować tożsamości urządzeń][lnk-importexport].
* **Zarządzanie tożsamościami urządzeń**. Każde Centrum IoT hub ujawnia zestaw punktów końcowych HTTPS REST do zarządzania tożsamościami urządzeń (Tworzenie, pobieranie, aktualizowanie i usuwanie). [Tożsamości urządzeń] [ lnk-device-identities] służą do urządzenia uwierzytelniania i kontroli dostępu.
* **Zarządzanie bliźniaczych reprezentacji urządzeń**. Każde Centrum IoT hub ujawnia zestaw przeznaczonych dla usługi REST protokół HTTPS punktu końcowego zapytania i aktualizację [bliźniaczych reprezentacji urządzeń] [ lnk-twins] (aktualizacja tagów i właściwości).
* **Zadania zarządzania**. Każde Centrum IoT hub ujawnia zestaw przeznaczonych dla usługi REST protokołu HTTPS z punktu końcowego do wykonywania zapytań i zarządzanie [zadania][lnk-jobs].
* **Punkty końcowe urządzenia**. Dla każdego urządzenia w rejestrze tożsamości Centrum IoT Hub ujawnia zestaw punktów końcowych:

  * *Wysyłanie komunikatów z urządzenia do chmury*. Urządzenie korzysta z tego punktu końcowego do [wysyłać komunikaty urządzenie chmura][lnk-d2c].
  * *Odbieranie komunikatów z chmury do urządzeń*. Urządzenie korzysta z tego punktu końcowego do odbierania docelowych [komunikatów z chmury do urządzeń][lnk-c2d].
  * *Zainicjuj przekazywania plików*. Urządzenie korzysta z tego punktu końcowego do odbierania identyfikatora URI połączenia SAS magazynu platformy Azure z Centrum IoT Hub, aby [przekazać plik][lnk-upload].
  * *Pobierania i aktualizowania z właściwości bliźniaczych reprezentacji urządzeń*. Urządzenie korzysta z tego punktu końcowego uzyskać dostęp do jego [bliźniaczej reprezentacji urządzenia][lnk-twins]jego właściwości.
  * *Odbierania żądań metody bezpośredniej*. Urządzenie korzysta z tego punktu końcowego do nasłuchiwania pod kątem [metoda bezpośrednia][lnk-methods]tego żądania.

    Te punkty końcowe są uwidaczniane za pomocą [MQTT 3.1.1][lnk-mqtt], HTTPS 1.1 i [protokołu AMQP 1.0] [ lnk-amqp] protokołów. Jest również dostępna za pośrednictwem protokołu AMQP [WebSockets] [ lnk-websockets] na porcie 443.

* **Punkty końcowe usługi**. Każde Centrum IoT hub ujawnia zestaw punktów końcowych w zapleczu rozwiązania do komunikowania się z urządzeniami. Z jednym wyjątkiem te punkty końcowe są dostępne tylko przy użyciu [AMQP] [ lnk-amqp] protokołu. Punkt końcowy z wywołania metody jest uwidaczniany za pośrednictwem protokołu HTTPS.
  
  * *Odbieranie komunikatów z urządzenia do chmury*. Ten punkt końcowy jest zgodny z [usługi Azure Event Hubs][lnk-event-hubs]. Usługa zaplecza służy do odczytywania [komunikatów z urządzenia do chmury] [ lnk-d2c] wysyłane przez urządzenie. Niestandardowe punkty końcowe można utworzyć w usłudze IoT hub, oprócz tego wbudowany punkt końcowy.
  * *Wysyłanie komunikatów z chmury do urządzeń i otrzymywać potwierdzeń dostarczenia*. Tych punktów końcowych, Włącz w zapleczu rozwiązania do wysyłania niezawodne [komunikatów z chmury do urządzeń][lnk-c2d]oraz dostarczania odpowiednich lub potwierdzenia wygaśnięcia.
  * *Otrzymuj powiadomienia pliku*. Tego obsługi komunikatów punktu końcowego zezwala otrzymywać powiadomienia, gdy urządzenie pomyślnie przekazać plik. 
  * *Bezpośrednie wywołania metody*. Ten punkt końcowy umożliwia usłudze zaplecza w celu wywołania [metoda bezpośrednia] [ lnk-methods] na urządzeniu.
  * *Monitorowanie zdarzeń operacji odbioru*. Ten punkt końcowy umożliwia monitorowanie zdarzeń, jeśli Centrum IoT hub została skonfigurowana do wysyłania ich operacji odbioru. Aby uzyskać więcej informacji, zobacz [monitorowanie operacji usługi IoT Hub][lnk-operations-mon].

[Azure IoT SDKs] [ lnk-sdks] artykule opisano różne sposoby dostępu do tych punktów końcowych.

Użyj wszystkich punktów końcowych usługi IoT Hub [TLS] [ lnk-tls] protokołu i żaden punkt końcowy nigdy nie jest uwidaczniany za pośrednictwem niezaszyfrowanej niezabezpieczone kanałów.

## <a name="custom-endpoints"></a>Niestandardowe punkty końcowe

Możesz połączyć istniejące usługi platformy Azure w ramach subskrypcji, do usługi IoT hub jako punkty końcowe do rozsyłania wiadomości. Te punkty końcowe działają jako punkty końcowe usługi i są używane jako ujścia dla trasy wiadomości. Urządzenia nie można zapisać bezpośrednio dodatkowe punkty końcowe. Aby dowiedzieć się więcej na temat trasy wiadomości, zobacz wpis przewodnik dla deweloperów na [wysyłanie i odbieranie komunikatów za pomocą usługi IoT hub][lnk-devguide-messaging].

Usługa IoT Hub obsługuje obecnie następujących usług platformy Azure jako dodatkowe punkty końcowe:

* Kontenery usługi Azure Storage
* Event Hubs
* Kolejki usługi Service Bus
* Tematy dotyczące usługi Service Bus

Usługa IoT Hub wymaga dostępu do tych punktów końcowych usługi zapisu do rozsyłania wiadomości do pracy. Jeśli skonfigurujesz punktów końcowych za pośrednictwem witryny Azure portal, niezbędne uprawnienia są dodawane. Upewnij się, że konfigurowanie usługi do obsługi oczekiwanej przepływności. Podczas pierwszej konfiguracji rozwiązania IoT, może być konieczne monitorowanie dodatkowych punktów końcowych i wprowadź wymagane zmiany dotyczące rzeczywistego obciążenia.

Jeśli komunikat zgodne kieruje wiele wskazujące do tego samego punktu końcowego, IoT Hub dostarczy komunikatu do określonego punktu końcowego tylko raz. W związku z tym nie ma potrzeby konfigurowania deduplikacji tematu lub kolejki usługi Service Bus. Partycjonowane kolejki koligacji partycji gwarantuje, kolejność komunikatów.

Limity liczby punktów końcowych, możesz dodać, zobacz [przydziałów i dławienia][lnk-devguide-quotas].

### <a name="when-using-azure-storage-containers"></a>Podczas korzystania z kontenerów usługi Azure Storage

Usługa IoT Hub obsługuje tylko, zapisywanie danych w kontenerach usługi Azure Storage jako obiekty BLOB w [Apache Avro](http://avro.apache.org/) formatu. Usługa IoT Hub partii komunikatów i zapisuje dane do obiektu blob zawsze wtedy, gdy:

* Zadanie wsadowe osiągnie określony rozmiar.
* Lub upłynął określoną ilość czasu.

Usługi IoT Hub będzie zapisywać do pustego obiektu blob, jeśli nie ma żadnych danych do zapisu.

Usługa IoT Hub wartością domyślną jest następująca Konwencja nazewnictwa pliku:

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Możesz użyć dowolnych nazw plików Konwencji, którą chcesz, jednak należy użyć listy wszystkich tokenów.

### <a name="when-using-service-bus-queues-and-topics"></a>Podczas korzystania z tematów i kolejek usługi Service Bus

Kolejki usługi Service Bus i tematy, używane jako punktów końcowych usługi IoT Hub nie może mieć **sesje** lub **wykrywania duplikatów** włączone. Jeśli jedno z tych opcji są włączone, punkt końcowy jest wyświetlany jako **informujący** w witrynie Azure portal.

## <a name="field-gateways"></a>Bram działających w terenie

Rozwiązania IoT *bramy w terenie* znajduje się między urządzeniami a punktami końcowymi usługi IoT Hub. Jest to zwykle znajduje się w pobliżu urządzeń. Urządzenia komunikują się bezpośrednio z bramy w terenie przy użyciu protokołu obsługiwanego przez urządzenia. Łączy się z punktem końcowym usługi IoT Hub przy użyciu protokołu, który jest obsługiwany przez usługę IoT Hub z bramy w terenie. Bramy w terenie może być dedykowanym urządzeniem sprzętowym lub komputer słabym zasilaniu z oprogramowaniem bram.

Możesz użyć [usługi Azure IoT Edge] [ lnk-iot-edge] do wdrożenia bramy w terenie. Usługi IoT Edge oferuje funkcje, takie jak multipleksowania komunikacji przy użyciu wielu urządzeń do tego samego połączenia usługi IoT Hub.

## <a name="next-steps"></a>Kolejne kroki

Inne tematy referencyjne w tym przewodniku dla deweloperów usługi IoT Hub obejmują:

* [Język zapytań usługi IoT Hub dla bliźniaczych reprezentacji urządzeń, zadań i routingu wiadomości][lnk-devguide-query]
* [Limity przydziału i ograniczenia przepustowości][lnk-devguide-quotas]
* [Obsługa protokołu MQTT Centrum IoT][lnk-devguide-mqtt]

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
