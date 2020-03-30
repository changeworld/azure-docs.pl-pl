---
title: Opis adresu IP wystąpienia usługi obsługi urządzeń IoT (DPS) | Dokumenty firmy Microsoft
description: Dowiedz się, jak zbadać adres usługi obsługi administracyjnej urządzeń IoT (DPS) i jego właściwości. Adres IP wystąpienia DPS można zmienić w niektórych scenariuszach, takich jak odzyskiwanie po awarii lub regionalnej pracy awaryjnej.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: f6afd5c4cc5aa0215f943979ae91389b39d449f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284930"
---
# <a name="iot-hub-dps-ip-addresses"></a>Adresy IP DPS centrum IoT Hub

Prefiksy adresów IP dla publicznych punktów końcowych usługi inicjowania obsługi administracyjnej urządzeń usługi IoT Hub (DPS) są okresowo publikowane pod [tagiem usługi](../virtual-network/service-tags-overview.md) _AzureIoTHub_ . Te prefiksy adresów IP można używać do kontrolowania łączności między wystąpieniem DPS IoT a urządzeniami lub zasobami sieciowymi w celu zaimplementowania różnych celów izolacji sieci:

| Cel | Podejście |
|------|----------|
| Upewnij się, że twoje urządzenia i usługi komunikują się tylko z punktami końcowymi DPS usługi IoT Hub | Użyj tagu usługi _AzureIoTHub,_ aby odnajdować wystąpienia DPS usługi IoT Hub. Skonfiguruj reguły ZEZWALANIA na ustawieniach zapory urządzeń i usług dla tych prefiksów adresów IP. Skonfiguruj reguły, aby upuścić ruch do innych docelowych adresów IP, z którymi nie chcesz, aby urządzenia lub usługi komunikowały się z nim. |
| Upewnij się, że punkt końcowy DPS usługi IoT Hub odbiera połączenia tylko z urządzeń i zasobów sieciowych | Funkcja [filtru IP](iot-dps-ip-filtering.md) usługi IoT DPS umożliwia tworzenie reguł filtrowania dla interfejsów API usługi urządzenia i dps. Te reguły filtrowania mogą służyć do zezwalania na połączenia tylko z urządzeń i adresów IP zasobów sieciowych (patrz sekcja [ograniczenia).](#limitations-and-workarounds) | 




## <a name="best-practices"></a>Najlepsze rozwiązania

* Podczas dodawania reguł ALLOW w konfiguracji zapory urządzeń najlepiej podać określone [porty używane przez odpowiednie protokoły.](../iot-hub/iot-hub-devguide-protocols.md#port-numbers)

* Prefiksy adresów IP wystąpień DPS IoT mogą ulec zmianie. Zmiany te są okresowo publikowane za pośrednictwem tagów usługi przed wejściem w życie. Dlatego ważne jest, aby opracować procesy do regularnego pobierania i używania najnowszych tagów usługi. Ten proces można zautomatyzować za pośrednictwem [interfejsu API odnajdywania tagów usługi.](../virtual-network/service-tags-overview.md#service-tags-on-premises) Interfejs API odnajdowania tagów usługi jest nadal w wersji zapoznawczej i w niektórych przypadkach może nie tworzyć pełną listę tagów i adresów IP. Dopóki interfejs API odnajdywania nie będzie ogólnie dostępny, należy rozważyć użycie [tagów usługi w formacie JSON do pobrania.](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) 

* Użyj *usługi AzureIoTHub.[ nazwa regionu]* znacznik do identyfikowania prefiksów IP używanych przez punkty końcowe DPS w określonym regionie. Aby uwzględnić odzyskiwanie po awarii centrum danych lub [regionalną funkcję failover,](../iot-hub/iot-hub-ha-dr.md)upewnij się, że łączność z prefiksami ADRESÓW IP regionu pary geograficznej wystąpienia DPS jest również włączona.

* Konfigurowanie reguł zapory dla wystąpienia DPS może blokować łączność potrzebną do uruchamiania poleceń interfejsu wiersza polecenia platformy Azure i programu PowerShell przeciwko niemu. Aby uniknąć tych problemów z łącznością, można dodać reguły ZEZWALAJ dla prefiksów adresów IP klientów, aby ponownie włączyć klientów interfejsu wiersza polecenia lub programu PowerShell do komunikowania się z wystąpieniem DPS.  


## <a name="limitations-and-workarounds"></a>Ograniczenia i obejścia

* Funkcja filtru IP DPS ma limit 100 reguł. Ten limit i mogą być wywoływane za pośrednictwem żądań za pośrednictwem obsługi klienta platformy Azure. 

* Skonfigurowane [reguły filtrowania adresów IP](iot-dps-ip-filtering.md) są stosowane tylko w punktach końcowych DPS, a nie w połączonych punktach końcowych usługi IoT Hub. Filtrowanie adresów IP dla połączonych centrów IoT musi być skonfigurowane oddzielnie. Aby uzyskać więcej informacji, zobacz [zasady filtrowania adresów IP w centrum IoT](../iot-hub/iot-hub-ip-filtering.md).

## <a name="support-for-ipv6"></a>Obsługa IPv6 

IPv6 nie jest obecnie obsługiwany w Uodochy IoT hub lub DPS.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o konfiguracjach adresów IP za pomocą dps, zobacz:

* [Konfigurowanie filtrowania adresów IP](iot-dps-ip-filtering.md)
