---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: networking
author: jimdial
ms.service: networking
ms.topic: include
ms.date: 02/07/2019
ms.author: jdial
ms.custom: include file
ms.openlocfilehash: fe1993a914ea4d3bd8ab9116748198cbb0c1c43c
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2019
ms.locfileid: "55905180"
---
<a name="virtual-networking-limits-classic"></a>Poniższe limity mają zastosowanie tylko w przypadku zasobów sieciowych zarządzanych przy użyciu klasycznego modelu wdrażania, które przypadają na jedną subskrypcję. Dowiedz się, jak [wyświetlić bieżące użycie zasobów względem limitów subskrypcji](../articles/networking/check-usage-against-limits.md).

| Zasób | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Sieci wirtualne |50 |100 |
| Lokalne lokacje sieciowe |20 |kontakt z pomocą techniczną |
| Serwery DNS na sieć wirtualną |20 |20 |
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

> [!NOTE]
> Firma Microsoft niedawno zwiększono wszystkie domyślne limity do ich maksymalnych limitów. Jeśli ma nie **maksymalny Limit** kolumny, a następnie zasób nie ma limitów zmienianych. Jeśli mieli te limity wzrosła o pomocy technicznej w przeszłości i nie ma limitów zaktualizowane zgodnie z poniższymi instrukcjami, możesz to zrobić [Otwórz żądanie obsługi klienta online bez dodatkowych opłat](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| Zasób | Limit domyślny | 
| --- | --- |
| Sieci wirtualne |1000 |
| Podsieci na sieć wirtualną |3000 |
| Komunikacja równorzędna sieci wirtualnych na sieć wirtualną |100 |
| Serwery DNS na sieć wirtualną |20 |
| Prywatne adresy IP na sieć wirtualną |65536 |
| Prywatne adresy IP na interfejs sieciowy |256 |
| Prywatne adresy IP na maszynie wirtualnej |256 |
| Współbieżne TCP lub UDP przepływy dla karty Sieciowej maszyny wirtualnej lub wystąpienia roli |500 000 |
| Interfejsy sieciowe (karta sieciowa) |65536 |
| Sieciowe grupy zabezpieczeń (NSG) |5000 |
| Reguły sieciowej grupy zabezpieczeń na sieciową grupę zabezpieczeń |1000 |
| Adresy IP i zakresów określonych dla źródła lub miejsca docelowego w grupie zabezpieczeń |4000 |
| Grupy zabezpieczeń aplikacji |3000 |
| Grupy zabezpieczeń aplikacji na konfiguracji adresu IP dla karty Sieciowej |20 |
| Konfiguracje adresów IP na grupy zabezpieczeń aplikacji |4000 |
| Grupy zabezpieczeń aplikacji, które można określić w ramach wszystkich reguł zabezpieczeń grupy zabezpieczeń sieci |100 |
| Zdefiniowane przez użytkownika tabele tras |200 |
| Zdefiniowane przez użytkownika trasy na tabelę tras |400 |
| Certyfikaty główne typu punkt-lokacja na bramę VPN Gateway |20 |
| Dotknie sieci wirtualnej |100 |
| Konfiguracji wzorca TAP interfejsu sieciowego na PODSŁUCHU sieci wirtualnej |100 |

#### <a name="publicip-address"></a>Publiczne ograniczeń adresów IP
| Zasób | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Publiczne adresy IP — dynamiczny |1000 (podstawowe) |kontakt z pomocą techniczną |
| Publiczne adresy IP — statyczny |(Basic) 200 |kontakt z pomocą techniczną |
| Publiczne adresy IP — statyczny |(Standard) 200 |kontakt z pomocą techniczną |
| Rozmiar publicznego prefiksu adresu IP (wersja zapoznawcza) | /28 | /28 |

#### <a name="load-balancer"></a>Limity usługi równoważenia obciążenia
Następujące limity mają zastosowanie tylko w przypadku zasobów sieciowych zarządzanych przy użyciu usługi Azure Resource Manager, które przypadają na region na subskrypcję. Dowiedz się, jak [wyświetlić bieżące użycie zasobów względem limitów subskrypcji](../articles/networking/check-usage-against-limits.md)

| Zasób | Limit domyślny |
| --- | --- |
| Moduły równoważenia obciążenia | 1000 | 
| Zasady dla każdego zasobu, Basic | 250 |
| Zasady dla każdego zasobu i Standard | 1500 | 
| Zasady na konfigurację adresu IP | 299 |
| Reguły dla karty Sieciowej | 500 |
| Konfiguracja adresu IP frontonu, Basic | 200 |
| Konfiguracja adresu IP frontonu i Standard | 600 |
| Pula zaplecza, Basic | 100, pojedynczy zestaw dostępności |
| Pula zaplecza i Standard | 1000, pojedynczy sieci wirtualnej |
| Zasoby zaplecza na moduł równoważenia obciążenia, Standard * | 150 |
| Zaświadczanie o kondycji portów, standardowe | 1 na wewnętrznych frontonu |

** Maksymalnie 150 zasobów, w dowolnej kombinacji zasobów maszyny wirtualnej autonomicznych, zestawy dostępności zasobów i zasobów zestawu skalowania maszyn wirtualnych.

