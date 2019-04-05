---
title: Omówienie punktów końcowych usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — informacje na temat usługi IoT Hub punktów końcowych przeznaczonych dla urządzeń i przeznaczonych dla usługi.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.openlocfilehash: 5015068f9b165190bef3b0cb97ddb194e173303e
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59045921"
---
# <a name="reference---iot-hub-endpoints"></a>Odwołanie - punktów końcowych usługi IoT Hub

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>Nazwy Centrum IoT Hub

Można znaleźć nazwy hosta usługi IoT hub, obsługujący punktów końcowych w portalu na Twoje Centrum **Przegląd** strony. Domyślnie, nazwa DNS usługi IoT hub wygląda następująco: `{your iot hub name}.azure-devices.net`.

## <a name="list-of-built-in-iot-hub-endpoints"></a>Lista wbudowanej punktów końcowych usługi IoT Hub

Usługa Azure IoT Hub jest usługą wielodostępną, która udostępnia funkcję do poszczególnych podmiotów. Na poniższym diagramie przedstawiono różne punkty końcowe IoT Hub udostępnia.

![Punkty końcowe centrum IoT Hub](./media/iot-hub-devguide-endpoints/endpoints.png)

Poniższa lista zawiera punkty końcowe:

* **Dostawca zasobów**. Dostawca zasobów usługi IoT Hub udostępnia [usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) interfejsu. Ten interfejs umożliwia właściciele subskrypcji platformy Azure do tworzenia i usuwania centra IoT i aktualizowanie właściwości Centrum IoT. Właściwości usługi IoT Hub określają [zasady zabezpieczeń na poziomie koncentratora](iot-hub-devguide-security.md#access-control-and-permissions), w przeciwieństwie do kontroli dostępu na poziomie urządzenia i funkcjonalności opcje do obsługi komunikatów chmura urządzenie i urządzenia do chmury. Dostawca zasobów usługi IoT Hub umożliwia także [wyeksportować tożsamości urządzeń](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).

* **Zarządzanie tożsamościami urządzeń**. Każde Centrum IoT hub ujawnia zestaw punktów końcowych HTTPS REST do zarządzania tożsamościami urządzeń (Tworzenie, pobieranie, aktualizowanie i usuwanie). [Tożsamości urządzeń](iot-hub-devguide-identity-registry.md) służą do urządzenia uwierzytelniania i kontroli dostępu.

* **Zarządzanie bliźniaczych reprezentacji urządzeń**. Każde Centrum IoT hub ujawnia zestaw przeznaczonych dla usługi REST protokół HTTPS punktu końcowego zapytania i aktualizację [bliźniaczych reprezentacji urządzeń](iot-hub-devguide-device-twins.md) (aktualizacja tagów i właściwości).

* **Zadania zarządzania**. Każde Centrum IoT hub ujawnia zestaw przeznaczonych dla usługi REST protokołu HTTPS z punktu końcowego do wykonywania zapytań i zarządzanie [zadań](iot-hub-devguide-jobs.md).

* **Punkty końcowe urządzenia**. Dla każdego urządzenia w rejestrze tożsamości Centrum IoT Hub ujawnia zestaw punktów końcowych:

  * *Wysyłanie komunikatów z urządzenia do chmury*. Urządzenie korzysta z tego punktu końcowego do [wysyłać komunikaty urządzenie chmura](iot-hub-devguide-messages-d2c.md).

  * *Odbieranie komunikatów z chmury do urządzeń*. Urządzenie korzysta z tego punktu końcowego do odbierania docelowych [komunikatów z chmury do urządzeń](iot-hub-devguide-messages-c2d.md).

  * *Zainicjuj przekazywania plików*. Urządzenie korzysta z tego punktu końcowego do odbierania identyfikatora URI połączenia SAS magazynu platformy Azure z Centrum IoT Hub, aby [przekazać plik](iot-hub-devguide-file-upload.md).

  * *Pobierania i aktualizowania z właściwości bliźniaczych reprezentacji urządzeń*. Urządzenie korzysta z tego punktu końcowego uzyskać dostęp do jego [bliźniaczej reprezentacji urządzenia](iot-hub-devguide-device-twins.md)jego właściwości.

  * *Odbierania żądań metody bezpośredniej*. Urządzenie korzysta z tego punktu końcowego do nasłuchiwania pod kątem [metoda bezpośrednia](iot-hub-devguide-direct-methods.md)tego żądania.

    Te punkty końcowe są uwidaczniane za pomocą [MQTT 3.1.1](https://mqtt.org/), HTTPS 1.1 i [protokołu AMQP 1.0](https://www.amqp.org/) protokołów. Jest również dostępna za pośrednictwem protokołu AMQP [WebSockets](https://tools.ietf.org/html/rfc6455) na porcie 443.

* **Punkty końcowe usługi**. Każde Centrum IoT hub ujawnia zestaw punktów końcowych w zapleczu rozwiązania do komunikowania się z urządzeniami. Z jednym wyjątkiem te punkty końcowe są dostępne tylko przy użyciu [AMQP](https://www.amqp.org/) protokołu. Punkt końcowy z wywołania metody jest uwidaczniany za pośrednictwem protokołu HTTPS.
  
  * *Odbieranie komunikatów z urządzenia do chmury*. Ten punkt końcowy jest zgodny z [usługi Azure Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/). Usługa zaplecza służy do odczytywania [komunikatów z urządzenia do chmury](iot-hub-devguide-messages-d2c.md) wysyłane przez urządzenie. Niestandardowe punkty końcowe można utworzyć w usłudze IoT hub, oprócz tego wbudowany punkt końcowy.
  
  * *Wysyłanie komunikatów z chmury do urządzeń i otrzymywać potwierdzeń dostarczenia*. Tych punktów końcowych, Włącz w zapleczu rozwiązania do wysyłania niezawodne [komunikatów z chmury do urządzeń](iot-hub-devguide-messages-c2d.md)oraz dostarczania odpowiednich lub potwierdzenia wygaśnięcia.
  
  * *Otrzymuj powiadomienia pliku*. Tego obsługi komunikatów punktu końcowego zezwala otrzymywać powiadomienia, gdy urządzenie pomyślnie przekazać plik. 
  
  * *Bezpośrednie wywołania metody*. Ten punkt końcowy umożliwia usłudze zaplecza w celu wywołania [metoda bezpośrednia](iot-hub-devguide-direct-methods.md) na urządzeniu.
  
  * *Monitorowanie zdarzeń operacji odbioru*. Ten punkt końcowy umożliwia monitorowanie zdarzeń, jeśli Centrum IoT hub została skonfigurowana do wysyłania ich operacji odbioru. Aby uzyskać więcej informacji, zobacz [monitorowanie operacji usługi IoT Hub](iot-hub-operations-monitoring.md).

[Azure IoT SDKs](iot-hub-devguide-sdks.md) artykule opisano różne sposoby dostępu do tych punktów końcowych.

Użyj wszystkich punktów końcowych usługi IoT Hub [TLS](https://tools.ietf.org/html/rfc5246) protokołu i żaden punkt końcowy nigdy nie jest uwidaczniany za pośrednictwem niezaszyfrowanej niezabezpieczone kanałów.

## <a name="custom-endpoints"></a>Niestandardowe punkty końcowe

Możesz połączyć istniejące usługi platformy Azure w ramach subskrypcji, do usługi IoT hub jako punkty końcowe do rozsyłania wiadomości. Te punkty końcowe działają jako punkty końcowe usługi i są używane jako ujścia dla trasy wiadomości. Urządzenia nie można zapisać bezpośrednio dodatkowe punkty końcowe. Dowiedz się więcej o [routing komunikatów](../iot-hub/iot-hub-devguide-messages-d2c.md).

Usługa IoT Hub obsługuje obecnie następujących usług platformy Azure jako dodatkowe punkty końcowe:

* Kontenery usługi Azure Storage
* Event Hubs
* Kolejki usługi Service Bus
* Tematy dotyczące usługi Service Bus

Limity liczby punktów końcowych, możesz dodać, zobacz [przydziałów i dławienia](iot-hub-devguide-quotas-throttling.md).

Można użyć interfejsu API REST [kondycji punktu końcowego uzyskać](https://docs.microsoft.com/de-de/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) można pobrać stanu kondycji punktów końcowych. Firma Microsoft zaleca używanie [metryki usługi IoT Hub](iot-hub-metrics.md) związane z routingu opóźnienie wiadomości, aby zidentyfikować i debugować błędy, gdy punkt końcowy kondycji jest nieużywany lub w złej kondycji.

|Kondycja|Opis|
|---|---|
|w dobrej kondycji|Punkt końcowy akceptuje komunikaty, zgodnie z oczekiwaniami.|
|Złej kondycji|Punkt końcowy nie akceptuje komunikaty, zgodnie z oczekiwaniami i ponawia próbę wysyłania danych do tego punktu końcowego usługi IoT Hub. Będzie można zaktualizować stan złej kondycji punktu końcowego do dobrej kondycji, gdy usługa IoT Hub ustanowiła ostatecznie spójny stan kondycji.|
|nieznane|Usługa IoT Hub nie ma nawiązać połączenie z punktem końcowym. Brak komunikatów zostały dostarczone do lub odrzucone z tego punktu końcowego.|
|Obsługa utraconych|Punkt końcowy nie akceptuje komunikaty, po usługi IoT Hub ponowiona wysyłanie wiadomości w okresie retrial.|

## <a name="field-gateways"></a>Bram działających w terenie

Rozwiązania IoT *bramy w terenie* znajduje się między urządzeniami a punktami końcowymi usługi IoT Hub. Jest to zwykle znajduje się w pobliżu urządzeń. Urządzenia komunikują się bezpośrednio z bramy w terenie przy użyciu protokołu obsługiwanego przez urządzenia. Łączy się z punktem końcowym usługi IoT Hub przy użyciu protokołu, który jest obsługiwany przez usługę IoT Hub z bramy w terenie. Bramy w terenie może być dedykowanym urządzeniem sprzętowym lub komputer słabym zasilaniu z oprogramowaniem bram.

Możesz użyć [usługi Azure IoT Edge](/azure/iot-edge/) do wdrożenia bramy w terenie. Usługi IoT Edge oferuje funkcje, takie jak multipleksowania komunikacji przy użyciu wielu urządzeń do tego samego połączenia usługi IoT Hub.

## <a name="next-steps"></a>Kolejne kroki

Inne tematy referencyjne w tym przewodniku dla deweloperów usługi IoT Hub obejmują:

* [Język zapytań usługi IoT Hub dla bliźniaczych reprezentacji urządzeń, zadań i routingu wiadomości](iot-hub-devguide-query-language.md)
* [Limity przydziału i ograniczanie wydajności](iot-hub-devguide-quotas-throttling.md)
* [Obsługa protokołu MQTT Centrum IoT](iot-hub-mqtt-support.md)
