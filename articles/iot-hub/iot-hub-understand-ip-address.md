---
title: Opis adresu IP centrum IoT | Dokumenty firmy Microsoft
description: Dowiedz się, jak zbadać adres IP centrum IoT i jego właściwości. Adres IP centrum IoT hub można zmienić w niektórych scenariuszach, takich jak odzyskiwanie po awarii lub regionalnej pracy awaryjnej.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: c609f2a3843481442e97061739a806de60a680b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367571"
---
# <a name="iot-hub-ip-addresses"></a>Adresy IP usługi IoT Hub

Prefiksy adresów IP publicznych punktów końcowych usługi IoT Hub są okresowo publikowane pod [tagiem usługi](../virtual-network/service-tags-overview.md) _AzureIoTHub_ .

> [!NOTE]
> W przypadku urządzeń wdrożonych wewnątrz sieci lokalnych usługa Azure IoT Hub obsługuje integrację łączności sieci wirtualnej z prywatnymi punktami końcowymi. Aby uzyskać więcej informacji, zobacz [obsługę usługi IoT Hub dla sieci wirtualnej.](./virtual-network-support.md#ingress-connectivity-to-iot-hub-using-private-endpoints)


Te prefiksy adresów IP można używać do kontrolowania łączności między Centrum IoT a urządzeniami lub zasobami sieciowymi w celu zaimplementowania różnych celów izolacji sieci:

| Cel | Odpowiednie scenariusze | Podejście |
|------|-----------|----------|
| Upewnij się, że urządzenia i usługi komunikują się tylko z punktami końcowymi usługi IoT Hub | [Wiadomości między urządzeniami](./iot-hub-devguide-messaging.md)a chmurą i z chmury do [urządzenia,](./iot-hub-devguide-messages-c2d.md) [metody bezpośrednie,](./iot-hub-devguide-direct-methods.md) [bliźniacze urządzenia i moduły](./iot-hub-devguide-device-twins.md) oraz [strumienie urządzeń](./iot-hub-device-streams-overview.md) | Użyj _azureiothub_ i _EventHub_ tagów usługi, aby odnajdować IoT Hub i prefiksy adresów IP Centrum zdarzeń i skonfigurować reguły ALLOW na urządzeniach i ustawienia zapory usług odpowiednio dla tych prefiksów adresów IP; spadek ruchu do innych docelowych adresów IP, z których urządzenia lub usługi nie mają się komunikować. |
| Upewnij się, że punkt końcowy urządzenia Usługi IoT Hub odbiera połączenia tylko z urządzeń i zasobów sieciowych | [Wiadomości między urządzeniami](./iot-hub-devguide-messaging.md)a chmurą i z chmury do [urządzenia,](./iot-hub-devguide-messages-c2d.md) [metody bezpośrednie,](./iot-hub-devguide-direct-methods.md) [bliźniacze urządzenia i moduły](./iot-hub-devguide-device-twins.md) oraz [strumienie urządzeń](./iot-hub-device-streams-overview.md) | Użyj [funkcji filtru IP](iot-hub-ip-filtering.md) usługi IoT Hub, aby zezwolić na połączenia z urządzeń i adresów IP zasobów sieciowych (zobacz [sekcję ograniczenia).](#limitations-and-workarounds) | 
| Upewnij się, że zasoby niestandardowych punktów końcowych tras (konta magazynu, magistrala usług i centra zdarzeń) są dostępne tylko z zasobów sieciowych | [Routing komunikatów](./iot-hub-devguide-messages-d2c.md) | Postępuj zgodnie ze wskazówkami dotyczącymi ograniczania łączności (na przykład za pomocą [reguł zapory,](../storage/common/storage-network-security.md) [łączy prywatnych](../private-link/private-endpoint-overview.md)lub [punktów końcowych usługi);](../virtual-network/virtual-network-service-endpoints-overview.md) użyj tagów usługi _AzureIoTHub,_ aby odnajdować prefiksy adresów IP usługi IoT Hub i dodać reguły ZEZWALAJ DLA tych prefiksów IP w konfiguracji zapory zasobu (zobacz [sekcję ograniczenia).](#limitations-and-workarounds) |



## <a name="best-practices"></a>Najlepsze rozwiązania

* Podczas dodawania reguł ALLOW w konfiguracji zapory urządzeń najlepiej podać określone [porty używane przez odpowiednie protokoły.](./iot-hub-devguide-protocols.md#port-numbers)

* Prefiksy adresów IP centrum IoT hub mogą ulec zmianie. Zmiany te są okresowo publikowane za pośrednictwem tagów usługi przed wejściem w życie. Dlatego ważne jest, aby opracować procesy do regularnego pobierania i używania najnowszych tagów usługi. Ten proces można zautomatyzować za pośrednictwem [interfejsu API odnajdywania tagów usługi.](../virtual-network/service-tags-overview.md#service-tags-on-premises) Należy zauważyć, że interfejs API odnajdywania tagów usługi jest nadal w wersji zapoznawczej i w niektórych przypadkach może nie tworzyć pełną listę tagów i adresów IP. Dopóki interfejs API odnajdywania nie będzie ogólnie dostępny, należy rozważyć użycie [tagów usługi w formacie JSON do pobrania.](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) 

* Użyj *usługi AzureIoTHub.[ nazwa regionu]* tag do identyfikowania prefiksów IP używanych przez punkty końcowe centrum IoT w określonym regionie. Aby uwzględnić odzyskiwanie po awarii centrum danych lub [regionalnej pracy awaryjnej](iot-hub-ha-dr.md) upewnij się, że łączność z prefiksami IP regionu pary geograficznej usługi IoT Hub jest również włączona.

* Konfigurowanie reguł zapory w centrum IoT Hub może blokować łączność wymaganą do uruchamiania poleceń interfejsu wiersza polecenia platformy Azure i programu PowerShell względem usługi IoT Hub. Aby tego uniknąć, można dodać reguły ZEZWALAJ dla prefiksów adresów IP klientów, aby ponownie włączyć klientów interfejsu wiersza polecenia lub programu PowerShell do komunikowania się z centrum IoT Hub.  


## <a name="limitations-and-workarounds"></a>Ograniczenia i obejścia

* Funkcja filtru IP usługi IoT Hub ma limit 10 reguł. Ten limit i mogą być wywoływane za pośrednictwem żądań za pośrednictwem obsługi klienta platformy Azure. 

* Skonfigurowane [reguły filtrowania adresów IP](iot-hub-ip-filtering.md) są stosowane tylko w punktach końcowych IP usługi IoT Hub, a nie w wbudowanym punkcie końcowym centrum zdarzeń usługi IoT Hub. Jeśli filtrowanie ip wymaga również zastosowania w Centrum zdarzeń, w którym są przechowywane wiadomości, możesz to zrobić, wprowadzając własny zasób Centrum zdarzeń, w którym można bezpośrednio skonfigurować żądane reguły filtrowania adresów IP. Aby to zrobić, należy aprowizować własny zasób Centrum zdarzeń i skonfigurować [routing wiadomości,](./iot-hub-devguide-messages-d2c.md) aby wysyłać wiadomości do tego zasobu zamiast wbudowanego Centrum zdarzeń usługi IoT Hub. Na koniec, jak omówiono w powyższej tabeli, aby włączyć funkcje routingu wiadomości należy również zezwolić na łączność z prefiksów adresów IP usługi IoT Hub do aprowizowanego zasobu Centrum zdarzeń.

* Podczas routingu do konta magazynu zezwalanie na ruch z prefiksów adresów IP usługi IoT Hub jest możliwe tylko wtedy, gdy konto magazynu znajduje się w innym regionie, co Centrum IoT Hub.

## <a name="support-for-ipv6"></a>Obsługa IPv6 

IPv6 nie jest obecnie obsługiwany w UIO.
