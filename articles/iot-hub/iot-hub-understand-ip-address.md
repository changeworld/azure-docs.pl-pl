---
title: Informacje o adresie IP Centrum IoT Hub | Microsoft Docs
description: Zapoznaj się z badaniem adresu IP Centrum IoT i jego właściwości. Adres IP Centrum IoT Hub może ulec zmianie w niektórych scenariuszach, takich jak odzyskiwanie po awarii lub regionalne przełączanie do trybu failover.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: f05be2725ef766bb1e5fd7f2624e754a2e21698a
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563177"
---
# <a name="iot-hub-ip-addresses"></a>IoT Hub adresy IP

Prefiksy adresów IP IoT Hub publicznych punktów końcowych są publikowane okresowo w [tagu usługi](../virtual-network/service-tags-overview.md)AzureIoTHub. Te prefiksy adresów IP mogą być używane do kontrolowania łączności między IoT Hub i urządzeniami i zasobami sieciowymi w celu zaimplementowania różnych celów izolacji sieci:

| Cel | Odpowiednie scenariusze | Podejście |
|------|-----------|----------|
| Upewnij się, że urządzenia i usługi komunikują się tylko z IoT Hub punktami końcowymi | Przesyłanie komunikatów [z urządzeń do chmury](./iot-hub-devguide-messaging.md)i [komunikacji między urządzeniami,](./iot-hub-devguide-messages-c2d.md) [metodami bezpośrednimi](./iot-hub-devguide-direct-methods.md), [bliźniaczych reprezentacji urządzeń i modułów](./iot-hub-devguide-device-twins.md) oraz [strumienie urządzeń](./iot-hub-device-streams-overview.md) | Użyj tagów usługi _AzureIoTHub_ i _EventHub_ , aby odnaleźć prefiksy adresów IP IoT Hub i centrum zdarzeń, a następnie skonfiguruj reguły zezwalania dla ustawień zapory urządzenia i usług odpowiednio dla tych prefiksów adresów IP. upuść ruch na inne docelowe adresy IP, z którymi nie chcesz komunikować się z urządzeniami ani usługami. |
| Upewnij się, że punkt końcowy urządzenia IoT Hub odbiera połączenia tylko z urządzeń i zasobów sieciowych | Przesyłanie komunikatów [z urządzeń do chmury](./iot-hub-devguide-messaging.md)i [komunikacji między urządzeniami,](./iot-hub-devguide-messages-c2d.md) [metodami bezpośrednimi](./iot-hub-devguide-direct-methods.md), [bliźniaczych reprezentacji urządzeń i modułów](./iot-hub-devguide-device-twins.md) oraz [strumienie urządzeń](./iot-hub-device-streams-overview.md) | Użyj [funkcji IoT Hub filtr IP](iot-hub-ip-filtering.md) , aby zezwolić na połączenia z urządzeń i adresów IP zasobów sieciowych (patrz sekcja [ograniczenia](#limitations-and-workarounds) ). | 
| Upewnij się, że niestandardowe zasoby punktu końcowego (konta magazynu, Magistrala usług i centrów zdarzeń) są dostępne tylko dla zasobów sieci | [Routing komunikatów](./iot-hub-devguide-messages-d2c.md) | Postępuj zgodnie ze wskazówkami dotyczącymi zasobów, aby ograniczyć łączność (na przykład za pośrednictwem [reguł zapory](../storage/common/storage-network-security.md), [linków prywatnych](../private-link/private-endpoint-overview.md)lub [punktów końcowych usługi](../virtual-network/virtual-network-service-endpoints-overview.md)); Użyj tagów usługi _AzureIoTHub_ , aby odnaleźć prefiksy adresów IP IoT Hub i dodać reguły zezwalania dla tych prefiksów IP w konfiguracji zapory zasobu (patrz sekcja [ograniczenia](#limitations-and-workarounds) ). |



## <a name="best-practices"></a>Najlepsze rozwiązania

* W przypadku dodawania reguł ZEZWALAnia w konfiguracji zapory urządzeń najlepiej jest podać określone [porty używane przez odpowiednie protokoły](./iot-hub-devguide-protocols.md#port-numbers).

* Prefiksy adresów IP w usłudze IoT Hub mogą ulec zmianie. Te zmiany są okresowo publikowane za pośrednictwem tagów usługi przed rozpoczęciem działania. W związku z tym ważne jest, aby opracować procesy do regularnego pobierania i używania najnowszych tagów usługi. Ten proces można zautomatyzować za pomocą [interfejsu API odnajdywania tagów usług](../virtual-network/service-tags-overview.md#service-tags-on-premises).

* Użyj *AzureIoTHub. [ Nazwa regionu]* tag do identyfikowania prefiksów IP używanych przez punkty końcowe Centrum IoT Hub w określonym regionie. W przypadku odzyskiwania po awarii centrum danych lub [regionalnej pracy w trybie failover](iot-hub-ha-dr.md) upewnij się również, że połączenie z PREFIKSAMI adresów IP w regionie pary geograficznej IoT Hub jest włączone.


## <a name="limitations-and-workarounds"></a>Ograniczenia i obejścia

* Funkcja filtru IP IoT Hub ma limit 10 reguł. Ten limit może być zgłaszany przez żądania za pośrednictwem obsługi klienta platformy Azure. 

* Skonfigurowane [reguły filtrowania adresów IP](iot-hub-ip-filtering.md) są stosowane tylko w przypadku punktów końcowych IP IoT Hub, a nie do wbudowanego punktu końcowego centrum zdarzeń centrum IoT Hub. Jeśli trzeba również zastosować filtrowanie adresów IP w centrum zdarzeń, w którym są przechowywane wiadomości, można to zrobić przy użyciu własnego zasobu centrum zdarzeń, w którym można bezpośrednio skonfigurować żądane reguły filtrowania adresów IP. W tym celu należy udostępnić własny zasób centrum zdarzeń i skonfigurować [Routing komunikatów](./iot-hub-devguide-messages-d2c.md) w celu wysyłania komunikatów do tego zasobu zamiast wbudowanego centrum zdarzeń IoT Hub. Na koniec, zgodnie z opisem w powyższej tabeli, aby włączyć funkcję routingu wiadomości, należy również zezwolić na połączenie z prefiksami adresów IP IoT Hub z zainicjowanym zasobem centrum zdarzeń.

* W przypadku routingu do konta magazynu Zezwalanie na ruch z prefiksów adresów IP IoT Hub jest możliwe tylko wtedy, gdy konto magazynu znajduje się w innym regionie niż IoT Hub.

## <a name="support-for-ipv6"></a>Obsługa protokołu IPv6 

Protokół IPv6 nie jest obecnie obsługiwany w IoT Hub.
