---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 01/14/2020
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 8752585e731f905636f57d31741e2be67f7140b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335040"
---
### <a name="networking-limits---azure-resource-manager"></a><a name="azure-resource-manager-virtual-networking-limits"></a>Limity sieci — Usługa Azure Resource Manager
Następujące limity dotyczą tylko zasobów sieciowych zarządzanych za pośrednictwem **usługi Azure Resource Manager** na region na subskrypcję. Dowiedz się, jak [wyświetlić bieżące użycie zasobów względem limitów subskrypcji](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Niedawno zwiększyliśmy wszystkie domyślne limity do ich maksymalnych limitów. Jeśli nie ma kolumny maksymalnego limitu, zasób nie ma regulowanych limitów. Jeśli w przeszłości te limity były zwiększane dzięki obsłudze technicznej i nie widzisz zaktualizowanych limitów w poniższych tabelach, [otwórz żądanie obsługi klienta online bez żadnych opłat](../articles/azure-resource-manager/templates/error-resource-quota.md)

| Zasób | Limit | 
| --- | --- |
| Sieci wirtualne |1000 |
| Podsieci na sieć wirtualną |3000 |
| Komunikacje równorzędne sieci wirtualnych na sieć wirtualną |500 |
| [Bramy sieci wirtualnej (bramy sieci VPN) na sieć wirtualną](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |1 |
| [Bramy sieci wirtualnej (bramy usługi ExpressRoute) na sieć wirtualną](../articles/expressroute/expressroute-about-virtual-network-gateways.md#gwsku) |1 |
| Serwery DNS na sieć wirtualną |20 |
| Prywatne adresy IP na sieć wirtualną |65 536 |
| Prywatne adresy IP na interfejs sieciowy |256 |
| Prywatne adresy IP na maszynę wirtualną |256 |
| Publiczne adresy IP na interfejs sieciowy |256 |
| Publiczne adresy IP na maszynę wirtualną |256 |
| [Równoczesne przepływy TCP lub UDP na kartę sieciową maszyny wirtualnej lub wystąpienia roli](../articles/virtual-network/virtual-machine-network-throughput.md#flow-limits-and-recommendations) |500 000 |
| Karty interfejsu sieciowego |65 536 |
| Grupy zabezpieczeń sieci |5000 |
| Reguły sieciowej grupy zabezpieczeń na sieciową grupę zabezpieczeń |1000 |
| Adresy IP i zakresy określone dla źródła lub miejsca docelowego w grupie zabezpieczeń |4000 |
| Grupy zabezpieczeń aplikacji |3000 |
| Grupy zabezpieczeń aplikacji na konfigurację IP, na kartę sieciową |20 |
| Konfiguracje adresów IP dla grupy zabezpieczeń aplikacji |4000 |
| Grupy zabezpieczeń aplikacji, które można określić we wszystkich regułach zabezpieczeń sieciowej grupy zabezpieczeń |100 |
| Tabele tras zdefiniowane przez użytkownika |200 |
| Trasy zdefiniowane przez użytkownika na tabelę tras |400 |
| Certyfikaty główne typu punkt-lokacja dla bramy sieci VPN platformy Azure |20 |
| Taps sieci wirtualnej |100 |
| Konfiguracje tap interfejsu sieciowego na sieć wirtualną TAP |100 |

#### <a name="public-ip-address-limits"></a><a name="publicip-address"></a>Limity publicznych adresów IP
| Zasób | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Publiczne adresy IP<sup>1</sup> | 10 dla basic. | Skontaktuj się z pomocą techniczną. |
| Statyczne publiczne adresy IP<sup>1</sup> | 10 dla basic. | Skontaktuj się z pomocą techniczną. |
| Standardowe publiczne adresy IP<sup>1</sup> | 10 | Skontaktuj się z pomocą techniczną. |
| Prefiksy publicznego adresu IP | ograniczona przez liczbę standardowych publicznych ip w ramach subskrypcji | Skontaktuj się z pomocą techniczną. |
| Długość prefiksu publicznego adresu IP | /28 | Skontaktuj się z pomocą techniczną. |

<sup>1.</sup> Domyślne limity dla publicznych adresów IP różnią się w zależności od typu kategorii oferty, takich jak bezpłatna wersja próbna, płatność zgodnie z rzeczywistymi elementami, dostawcy csp. Na przykład wartość domyślna dla subskrypcji umowy Enterprise Agreement to 1000.

#### <a name="load-balancer-limits"></a><a name="load-balancer"></a>Limity równoważenia obciążenia
Następujące limity mają zastosowanie tylko w przypadku zasobów sieciowych zarządzanych przy użyciu usługi Azure Resource Manager, które przypadają na region na subskrypcję. Dowiedz się, jak [wyświetlić bieżące użycie zasobów względem limitów subskrypcji](../articles/networking/check-usage-against-limits.md).

**Standardowy moduł równoważenia obciążenia**

| Zasób                                | Limit         |
|-----------------------------------------|-------------------------------|
| Moduły równoważenia obciążenia                          | 1000                         |
| Reguły dla zasobu                      | 1500                         |
| Reguły na kartę sieciową (we wszystkich ip na karcie sieciowej) | 300                           |
| Konfiguracje adresów IP frontu              | 600                           |
| Rozmiar puli zaplecza                       | 1000 konfiguracji IP, pojedyncza sieć wirtualna |
| Porty o wysokiej dostępności                 | 1 na wewnętrzną przód       |
| Reguły ruchu wychodzącego na moduł równoważenia obciążenia        | 20                            |
| [Limit czasu bezczynnego TCP](https://docs.microsoft.com/azure/load-balancer/load-balancer-tcp-idle-timeout#tcp-idle-timeout) | 4 minuty/30 minut          |


**Podstawowy moduł równoważenia obciążenia**

| Zasób                                | Limit        |
|-----------------------------------------|------------------------------|
| Moduły równoważenia obciążenia                          | 1000                        |
| Reguły dla zasobu                      | 250                          |
| Reguły na kartę sieciową (we wszystkich ip na karcie sieciowej) | 300                          |
| Konfiguracje adresów IP frontu              | 200                          |
| Rozmiar puli zaplecza                       | 300 konfiguracji IP, pojedynczy zestaw dostępności |
| Zestawy dostępności na moduł równoważenia obciążenia     | 150                          |

<a name="virtual-networking-limits-classic"></a>Następujące limity dotyczą tylko zasobów sieciowych zarządzanych za pośrednictwem **klasycznego** modelu wdrażania na subskrypcję. Dowiedz się, jak [wyświetlić bieżące użycie zasobów względem limitów subskrypcji](../articles/networking/check-usage-against-limits.md).

| Zasób | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Sieci wirtualne |100 |100 |
| Lokalne lokacje sieciowe |20 |50 |
| Serwery DNS na sieć wirtualną |20 |20 |
| Prywatne adresy IP na sieć wirtualną |4,096 |4,096 |
| Równoczesne przepływy TCP lub UDP na kartę sieciową maszyny wirtualnej lub wystąpienia roli |500 000, do 1 000 000 dla dwóch lub więcej kart sieciowych. |500 000, do 1 000 000 dla dwóch lub więcej kart sieciowych. |
| Sieciowe grupy zabezpieczeń |200 |200 |
| Reguły sieciowej grupy zabezpieczeń na sieciową grupę zabezpieczeń |1000 |1000 |
| Tabele tras zdefiniowane przez użytkownika |200 |200 |
| Trasy zdefiniowane przez użytkownika na tabelę tras |400 |400 |
| Publiczne adresy IP (dynamiczne) |500 |500 |
| Zastrzeżone publiczne adresy IP |500 |500 |
| Publiczne adresy VIP na wdrożenie |5 |Kontakt z pomocą techniczną |
| Prywatny adres VIP (wewnętrzne równoważenie obciążenia) na wdrożenie |1 |1 |
| Listy kontroli dostępu do punktu końcowego (Listy kontroli dostępu) |50 |50 |
