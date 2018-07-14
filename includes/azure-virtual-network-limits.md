---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: networking
author: jimdial
ms.service: networking
ms.topic: include
ms.date: 06/20/2018
ms.author: jdial
ms.custom: include file
ms.openlocfilehash: 9ba9bc993832350f6b6ce1c642e2dc852731b6f0
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39029985"
---
<a name="virtual-networking-limits-classic"></a>Poniższe limity mają zastosowanie tylko w przypadku zasobów sieciowych zarządzanych przy użyciu klasycznego modelu wdrażania, które przypadają na jedną subskrypcję. Dowiedz się, jak [wyświetlić bieżące użycie zasobów względem limitów subskrypcji](../articles/networking/check-usage-against-limits.md).

| Zasób | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Sieci wirtualne |50 |100 |
| Lokalne lokacje sieciowe |20 |kontakt z pomocą techniczną |
| Serwery DNS na sieć wirtualną |20 |100 |
| Prywatne adresy IP na sieć wirtualną |4096 |4096 |
| Współbieżne TCP lub UDP przepływy dla karty Sieciowej maszyny wirtualnej lub wystąpienia roli |500 000 |500 000 |
| Sieciowe grupy zabezpieczeń (NSG) |100 |200 |
| Reguły sieciowej grupy zabezpieczeń na sieciową grupę zabezpieczeń |200 |1000 |
| Zdefiniowane przez użytkownika tabele tras |100 |200 |
| Zdefiniowane przez użytkownika trasy na tabelę tras |100 |400 |
| Publiczne adresy IP (dynamiczne) |5 |kontakt z pomocą techniczną |
| Zastrzeżone publiczne adresy IP |20 |kontakt z pomocą techniczną |
| Publiczne adresy VIP na wdrożenie |5 |kontakt z pomocą techniczną |
| Prywatne adresy VIP (wewnętrzny moduł równoważenia obciążenia) na wdrożenie |1 |1 |
| Listy kontroli dostępu do punktu końcowego (ACL) |50 |50 |

#### <a name="azure-resource-manager-virtual-networking-limits"></a>Limity dotyczące sieci — Azure Resource Manager
Następujące limity mają zastosowanie tylko w przypadku zasobów sieciowych zarządzanych przy użyciu usługi Azure Resource Manager, które przypadają na region na subskrypcję. Dowiedz się, jak [wyświetlić bieżące użycie zasobów względem limitów subskrypcji](../articles/networking/check-usage-against-limits.md).

| Zasób | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Sieci wirtualne |50 |1000 |
| Podsieci na sieć wirtualną |1000 |10 000 |
| Komunikacja równorzędna sieci wirtualnych na sieć wirtualną |50 ** |100 |
| Serwery DNS na sieć wirtualną |9 |25 |
| Prywatne adresy IP na sieć wirtualną |16384 ** |16384 |
| Prywatne adresy IP na interfejs sieciowy |256 |256 |
| Współbieżne TCP lub UDP przepływy dla karty Sieciowej maszyny wirtualnej lub wystąpienia roli |500 000 |500 000 |
| Interfejsy sieciowe (karta sieciowa) |24000 ** |24000 |
| Sieciowe grupy zabezpieczeń (NSG) |100 |5000 |
| Reguły sieciowej grupy zabezpieczeń na sieciową grupę zabezpieczeń |1000 ** |1000 |
| Adresy IP i zakresów określonych dla źródła lub miejsca docelowego w grupie zabezpieczeń |2000 |4000 |
| Grupy zabezpieczeń aplikacji |500 |3000 |
| Grupy zabezpieczeń aplikacji na konfiguracji adresu IP dla karty Sieciowej |10 |20 |
| Konfiguracje adresów IP na grupy zabezpieczeń aplikacji |1000 |4000 |
| Grupy zabezpieczeń aplikacji, które można określić w ramach wszystkich reguł zabezpieczeń grupy zabezpieczeń sieci |50 |100 |
| Zdefiniowane przez użytkownika tabele tras |100 |200 |
| Zdefiniowane przez użytkownika trasy na tabelę tras |400 ** |400 |
| Publiczne adresy IP — dynamiczny |60 (podstawowe) |kontakt z pomocą techniczną |
| Publiczne adresy IP — statyczny |(Basic) 20 |kontakt z pomocą techniczną |
| Publiczne adresy IP — statyczny |20 (standardowa) |kontakt z pomocą techniczną |
| Certyfikaty główne typu punkt-lokacja na bramę VPN Gateway |20 |20 |

** Domyślne limity mają zastosowanie do subskrypcji, dla których wcześniej nie było te limity wzrosła dzięki obsłudze są aktualizowane. Jeśli masz ma limity wzrosła o pomocy technicznej w przeszłości i chcesz je aktualizować do nowej wartości domyślnych, należy uzyskać [Otwórz żądanie obsługi klienta online bez dodatkowych opłat](../articles/azure-resource-manager/resource-manager-quota-errors.md)

#### <a name="load-balancer"></a>Limity usługi równoważenia obciążenia
Następujące limity mają zastosowanie tylko w przypadku zasobów sieciowych zarządzanych przy użyciu usługi Azure Resource Manager, które przypadają na region na subskrypcję. Dowiedz się, jak [wyświetlić bieżące użycie zasobów względem limitów subskrypcji](../articles/networking/check-usage-against-limits.md)

| Zasób | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Moduły równoważenia obciążenia | 100 | 1000 |
| Zasady dla każdego zasobu, Basic | 150 | 250 |
| Zasady dla każdego zasobu i Standard | 1250 | 1500 |
| Zasady na konfigurację adresu IP | 299 |299 |
| Konfiguracja adresu IP frontonu, Basic | 10 | 200 |
| Konfiguracja adresu IP frontonu i Standard | 10 | 600 |
| Pula zaplecza, Basic | 100, pojedynczy zestaw dostępności | 100, pojedynczy zestaw dostępności |
| Pula zaplecza i Standard | 1000, pojedynczy sieci wirtualnej | 1000, pojedynczy sieci wirtualnej |
| Zasoby zaplecza na moduł równoważenia obciążenia, standardowy &ast; | 50 | 150 |
| Zaświadczanie o kondycji portów, standardowe | 1 na wewnętrznych frontonu | 1 na wewnętrznych frontonu |

&ast; Zasoby do 150 dowolną kombinację autonomiczne maszyny wirtualne, zestawy dostępności i zestawy skalowania maszyn wirtualnych.

Jeśli będzie konieczne zwiększenie limitów domyślnych, [skontaktuj się z pomocą techniczną](../articles/azure-supportability/resource-manager-core-quotas-request.md ).

