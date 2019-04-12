---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 04/10/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: ff6e08929d5ede86ba0632e7cfb38d81d21ca7ec
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59503280"
---
<a name="virtual-networking-limits-classic"></a>Poniższe limity mają zastosowanie tylko w przypadku zasobów sieciowych zarządzanych przy użyciu klasycznego modelu wdrażania, które przypadają na jedną subskrypcję. Dowiedz się, jak [wyświetlić bieżące użycie zasobów względem limitów subskrypcji](../articles/networking/check-usage-against-limits.md).

| Zasób | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Sieci wirtualne |50 |100 |
| Lokalne lokacje sieciowe |20 |Skontaktuj się z pomocą techniczną. |
| Serwery DNS na sieć wirtualną |20 |20 |
| Prywatne adresy IP na sieć wirtualną |4,096 |4,096 |
| Współbieżne TCP lub UDP przepływy dla karty Sieciowej maszyny wirtualnej lub wystąpienia roli |500 000 maksymalnie 1 000 000 dla co najmniej dwóch kart interfejsu sieciowego. |500 000 maksymalnie 1 000 000 dla co najmniej dwóch kart interfejsu sieciowego. |
| Network Security Groups (NSGs) |100 |200 |
| Reguły sieciowej grupy zabezpieczeń na sieciową grupę zabezpieczeń |200 |1000 |
| Tabele tras zdefiniowanych przez użytkownika |100 |200 |
| Trasy zdefiniowane przez użytkownika na jedną tabelę tras |100 |400 |
| Publiczne adresy IP (dynamiczne) |5 |Skontaktuj się z pomocą techniczną. |
| Zastrzeżone publiczne adresy IP |20 |Skontaktuj się z pomocą techniczną. |
| Publiczne adresy VIP na wdrożenie |5 |Skontaktuj się z pomocą techniczną. |
| Prywatne adresy VIP (wewnętrzne Równoważenie obciążenia) na wdrożenie |1 |1 |
| Listy kontroli dostępu do punktu końcowego (ACL) |50 |50 |

#### <a name="azure-resource-manager-virtual-networking-limits"></a>Limity dotyczące sieci — usługi Azure Resource Manager
Następujące limity mają zastosowanie tylko w przypadku zasobów sieciowych zarządzanych przy użyciu usługi Azure Resource Manager, które przypadają na region na subskrypcję. Dowiedz się, jak [wyświetlić bieżące użycie zasobów względem limitów subskrypcji](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Firma Microsoft niedawno zwiększono wszystkie domyślne limity do ich maksymalnych limitów. W przypadku żadnej kolumny maksymalny limit zasób nie ma limitów dostosowywalnych. Jeśli ma limity wzrosła o pomocy technicznej w przeszłości, a nie widzisz limity zaktualizowane w poniższych tabelach [Otwórz żądanie obsługi klienta online bez dodatkowych opłat](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| Zasób | Limit domyślny | 
| --- | --- |
| Sieci wirtualne |1000 |
| Podsieci na sieć wirtualną |3,000 |
| Komunikacja równorzędna sieci wirtualnych na sieć wirtualną |500 |
| Serwery DNS na sieć wirtualną |20 |
| Prywatne adresy IP na sieć wirtualną |65,536 |
| Prywatne adresy IP na interfejs sieciowy |256 |
| Prywatne adresy IP na maszynę wirtualną |256 |
| Współbieżne TCP lub UDP przepływy dla karty Sieciowej maszyny wirtualnej lub wystąpienia roli |500,000 |
| Karty sieciowe |65,536 |
| Grupy zabezpieczeń sieci |5000 |
| Reguły sieciowej grupy zabezpieczeń na sieciową grupę zabezpieczeń |1000 |
| Adresy IP i zakresów określonych dla źródła lub miejsca docelowego w grupie zabezpieczeń |4,000 |
| Grupy zabezpieczeń aplikacji |3,000 |
| Grupy zabezpieczeń aplikacji na konfiguracji adresu IP dla karty Sieciowej |20 |
| Konfiguracje adresów IP na grupy zabezpieczeń aplikacji |4,000 |
| Grupy zabezpieczeń aplikacji, które można określić w ramach wszystkich reguł zabezpieczeń grupy zabezpieczeń sieci |100 |
| Tabele tras zdefiniowanych przez użytkownika |200 |
| Trasy zdefiniowane przez użytkownika na jedną tabelę tras |400 |
| Certyfikaty główne punkt lokacja na bramę Azure VPN Gateway |20 |
| Dotknie sieci wirtualnej |100 |
| Konfiguracji wzorca TAP interfejsu sieciowego na PODSŁUCHU sieci wirtualnej |100 |

#### <a name="publicip-address"></a>Publiczne ograniczeń adresów IP
| Zasób | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Publiczne adresy IP — dynamiczny | 1000 Basic. |Skontaktuj się z pomocą techniczną. |
| Publiczne adresy IP — statyczny | 1000 Basic. |Skontaktuj się z pomocą techniczną. |
| Publiczne adresy IP — statyczny | 200 dla warstwy standardowa.|Skontaktuj się z pomocą techniczną. |
| Rozmiar prefiksu publicznego adresu IP (wersja zapoznawcza) | /28 | /28 |

#### <a name="load-balancer"></a>Limity usługi równoważenia obciążenia
Następujące limity mają zastosowanie tylko w przypadku zasobów sieciowych zarządzanych przy użyciu usługi Azure Resource Manager, które przypadają na region na subskrypcję. Dowiedz się, jak [wyświetlić bieżące użycie zasobów względem limitów subskrypcji](../articles/networking/check-usage-against-limits.md).

| Zasób | Limit domyślny |
| --- | --- |
| Moduły równoważenia obciążenia | 1000 | 
| Zasady dla każdego zasobu, Basic | 250 |
| Zasady dla każdego zasobu i Standard | 1,500 | 
| Zasady na konfigurację adresu IP | 299 |
| Reguły dla karty Sieciowej | 500 |
| Konfiguracje adresów IP frontonu, Basic | 200 |
| Konfiguracje adresów IP frontonu, standardowy | 600 |
| Puli zaplecza, Basic | 100, pojedynczym zestawie dostępności |
| Puli zaplecza, standardowy | 1000, jednej sieci wirtualnej |
| Zasoby zaplecza na moduł równoważenia obciążenia, Standard<sup>1</sup> | 150 |
| Porty wysokiej dostępności, standardowy | 1 na wewnętrznych frontonu |

<sup>1</sup>wynoszący maksymalnie 150 zasobów, w dowolnej kombinacji zasobów maszyny wirtualnej, autonomiczna, zestaw dostępności zasobów i zasobów zestawu skalowania maszyn wirtualnych.

