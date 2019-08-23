---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 06/25/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: e55b191e937b6c9f8183b3878b264eb50401b3f1
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69903900"
---
<a name="azure-resource-manager-virtual-networking-limits"></a>Limity sieci — Azure Resource Manager następujące limity mają zastosowanie tylko w przypadku zasobów sieciowych zarządzanych za pomocą **Azure Resource Manager** na region na subskrypcję. Dowiedz się, jak [wyświetlić bieżące użycie zasobów względem limitów subskrypcji](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Ostatnio zwiększono wszystkie domyślne limity do ich maksymalnych limitów. Jeśli nie ma żadnej kolumny maksymalnego limitu, zasób nie ma dopuszczalnych limitów. Jeśli te limity zostały zwiększone przez pomoc techniczną w przeszłości i nie widzisz zaktualizowanych limitów w poniższych tabelach, [Otwórz bezpłatnie żądanie pomocy technicznej w trybie online](../articles/azure-resource-manager/resource-manager-quota-errors.md) .

| Resource | Domyślny/maksymalny limit | 
| --- | --- |
| Sieci wirtualne |1000 |
| Podsieci na sieć wirtualną |3000 |
| Wirtualne sieci równorzędne dla sieci wirtualnej |500 |
| Serwery DNS na sieć wirtualną |20 |
| Prywatne adresy IP na sieć wirtualną |65 536 |
| Prywatne adresy IP na interfejs sieciowy |256 |
| Prywatne adresy IP na maszynę wirtualną |256 |
| Publiczne adresy IP na interfejs sieciowy |256 |
| Publiczne adresy IP na maszynę wirtualną |256 |
| Współbieżne przepływy TCP lub UDP na kartę sieciową maszyny wirtualnej lub wystąpienia roli |500,000 |
| Karty sieciowe |65 536 |
| Grupy zabezpieczeń sieci |5,000 |
| Reguły sieciowej grupy zabezpieczeń na sieciową grupę zabezpieczeń |1000 |
| Adresy IP i zakresy określone dla źródła lub miejsca docelowego w grupie zabezpieczeń |4,000 |
| Grupy zabezpieczeń aplikacji |3000 |
| Grupy zabezpieczeń aplikacji na konfigurację IP, na kartę sieciową |20 |
| Konfiguracje protokołu IP na grupę zabezpieczeń aplikacji |4,000 |
| Grupy zabezpieczeń aplikacji, które można określić w ramach wszystkich reguł zabezpieczeń sieciowej grupy zabezpieczeń |100 |
| Tabele tras zdefiniowane przez użytkownika |200 |
| Trasy zdefiniowane przez użytkownika na tabelę tras |400 |
| Certyfikaty główne punkt-lokacja na platformie Azure VPN Gateway |20 |
| Kurki sieci wirtualnej |100 |
| Naciśnij KOLEJno pozycje konfiguracje interfejsu sieciowego dla sieci wirtualnej |100 |

#### <a name="publicip-address"></a>Limity publicznych adresów IP
| Resource | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Publiczne adresy IP — dynamiczny | 1 000 dla warstwy Podstawowa. |Skontaktuj się z pomocą techniczną. |
| Publiczne adresy IP — statyczne | 1 000 dla warstwy Podstawowa. |Skontaktuj się z pomocą techniczną. |
| Publiczne adresy IP — statyczne | 200 dla warstwy Standardowa.|Skontaktuj się z pomocą techniczną. |
| Długość prefiksu publicznego adresu IP | /28 | Skontaktuj się z pomocą techniczną. |

#### <a name="load-balancer"></a>Limity usługi równoważenia obciążenia
Następujące limity mają zastosowanie tylko w przypadku zasobów sieciowych zarządzanych przy użyciu usługi Azure Resource Manager, które przypadają na region na subskrypcję. Dowiedz się, jak [wyświetlić bieżące użycie zasobów względem limitów subskrypcji](../articles/networking/check-usage-against-limits.md).

| Resource | Domyślny/maksymalny limit |
| --- | --- |
| Moduły równoważenia obciążenia | 1000 | 
| Reguły na zasób, podstawowe | 250 |
| Reguły na zasób, standard | 1,500 | 
| Reguły na konfigurację protokołu IP | 299 |
| Reguły na kartę sieciową | 300 |
| Konfiguracje adresów IP frontonu, podstawowe | 200 |
| Konfiguracje adresów IP frontonu, standard | 600 |
| Pula zaplecza, podstawowa | 100, zestaw o pojedynczej dostępności |
| Pula zaplecza, standard | 1 000, pojedyncza Sieć wirtualna |
| Zasoby zaplecza na moduł równoważenia obciążenia, wersja Standard<sup>1</sup> | 150 |
| Porty wysokiej dostępności, standard | 1 na fronton wewnętrzny |

<sup>1</sup> Limit wynosi do 150 zasobów w dowolnej kombinacji autonomicznych zasobów maszyny wirtualnej, zasobów zestawu dostępności i zasobów zestawu skalowania maszyn wirtualnych.

#### <a name="virtual-networking-limits-classic"></a>Poniższe limity mają zastosowanie tylko w przypadku zasobów sieciowych zarządzanych przy użyciu klasycznego modelu wdrażania na subskrypcję. Dowiedz się, jak [wyświetlić bieżące użycie zasobów względem limitów subskrypcji](../articles/networking/check-usage-against-limits.md).

| Resource | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Sieci wirtualne |100 |100 |
| Lokalne lokacje sieciowe |20 |50 |
| Serwery DNS na sieć wirtualną |20 |20 |
| Prywatne adresy IP na sieć wirtualną |4 096 |4 096 |
| Współbieżne przepływy TCP lub UDP na kartę sieciową maszyny wirtualnej lub wystąpienia roli |500 000, do 1 000 000 w przypadku co najmniej dwóch kart sieciowych. |500 000, do 1 000 000 w przypadku co najmniej dwóch kart sieciowych. |
| Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) |200 |200 |
| Reguły sieciowej grupy zabezpieczeń na sieciową grupę zabezpieczeń |1000 |1000 |
| Tabele tras zdefiniowane przez użytkownika |200 |200 |
| Trasy zdefiniowane przez użytkownika na tabelę tras |400 |400 |
| Publiczne adresy IP (dynamiczne) |500 |500 |
| Zastrzeżone publiczne adresy IP |500 |500 |
| Publiczne adresy VIP na wdrożenie |5 |Skontaktuj się z pomocą techniczną |
| Prywatne wirtualne adresy IP (wewnętrzne równoważenie obciążenia) na wdrożenie |1 |1 |
| Listy kontroli dostępu (ACL) punktu końcowego |50 |50 |
