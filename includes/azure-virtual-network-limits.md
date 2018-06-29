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
ms.openlocfilehash: 326da32f91b263bbd09a4c6f521c9ec72094820c
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37066071"
---
<a name="virtual-networking-limits-classic"></a>Poniższe limity mają zastosowanie tylko w przypadku zasobów sieciowych zarządzanych przy użyciu klasycznego modelu wdrażania, które przypadają na jedną subskrypcję. Dowiedz się, jak [wyświetlać z bieżącego wykorzystania zasobów względem swoje limity subskrypcji](../articles/networking/check-usage-against-limits.md).

| Zasób | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Sieci wirtualne |50 |100 |
| Lokalne lokacje sieciowe |20 |kontakt z pomocą techniczną |
| Serwery DNS na sieć wirtualną |20 |100 |
| Prywatne adresy IP na sieć wirtualną |4096 |4096 |
| Współbieżne TCP lub UDP przepływów na kartę Sieciową maszyny wirtualnej lub wystąpienia roli |500 000 |500 000 |
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
Następujące limity mają zastosowanie tylko w przypadku zasobów sieciowych zarządzanych przy użyciu usługi Azure Resource Manager, które przypadają na region na subskrypcję. Dowiedz się, jak [wyświetlać z bieżącego wykorzystania zasobów względem swoje limity subskrypcji](../articles/networking/check-usage-against-limits.md).

| Zasób | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Sieci wirtualne |50 |1000 |
| Podsieci na sieć wirtualną |1000 |10 000 |
| Komunikacji równorzędnych sieci wirtualnych dla sieci wirtualnej |10 |50 |
| Serwery DNS na sieć wirtualną |9 |25 |
| Prywatne adresy IP na sieć wirtualną |16384 ** |16384 |
| Prywatne adresy IP na interfejs sieciowy |256 |256 |
| Współbieżne TCP lub UDP przepływów na kartę Sieciową maszyny wirtualnej lub wystąpienia roli |500 000 |500 000 |
| Interfejsy sieciowe (karta sieciowa) |24000 ** |24000 |
| Sieciowe grupy zabezpieczeń (NSG) |100 |5000 |
| Reguły sieciowej grupy zabezpieczeń na sieciową grupę zabezpieczeń |1000 ** |1000 |
| Adresy IP i zakresów określony dla źródła lub miejsca docelowego w grupie zabezpieczeń |2000 |4000 |
| Grupy zabezpieczeń aplikacji |500 |3000 |
| Stosowanie grup zabezpieczeń dla konfiguracji IP dla karty Sieciowej |10 |20 |
| Konfiguracje adresów IP na grupy zabezpieczeń aplikacji |1000 |4000 |
| Grupy zabezpieczeń aplikacji, które można określić w ramach wszystkich reguł zabezpieczeń grupy zabezpieczeń sieci |50 |100 |
| Zdefiniowane przez użytkownika tabele tras |100 |200 |
| Zdefiniowane przez użytkownika trasy na tabelę tras |100 |400 |
| Publiczne adresy IP — dynamiczne |60 (podstawowe) |kontakt z pomocą techniczną |
| Publiczne adresy IP — statyczne |(Basic) 20 |kontakt z pomocą techniczną |
| Publiczne adresy IP — statyczne |20 (standardowy) |kontakt z pomocą techniczną |
| Certyfikaty główne typu punkt-lokacja na bramę VPN Gateway |20 |20 |

** Te domyślne limity dotyczą subskrypcje, które wcześniej nie było te limity zwiększona za pomocą techniczną

#### <a name="load-balancer"></a>Limity usługi równoważenia obciążenia
Następujące limity mają zastosowanie tylko w przypadku zasobów sieciowych zarządzanych przy użyciu usługi Azure Resource Manager, które przypadają na region na subskrypcję. Dowiedz się, jak [wyświetlać z bieżącego wykorzystania zasobów względem swoje limity subskrypcji](../articles/networking/check-usage-against-limits.md)

| Zasób | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Moduły równoważenia obciążenia | 100 | 1000 |
| Zasady dla zasobów, Basic | 150 | 250 |
| Reguły w poszczególnych zasobów i Standard | 1250 | 1500 |
| Zasady na konfigurację adresu IP | 299 |299 |
| Konfiguracje adresów IP frontonu, Basic | 10 | 200 |
| Konfiguracje adresów IP frontonu, Standard | 10 | 600 |
| Puli zaplecza, Basic | 100, pojedynczy zestaw dostępności | 100, pojedynczy zestaw dostępności |
| Puli zaplecza, Standard | 1000, pojedynczy sieci wirtualnej | 1000, pojedynczy sieci wirtualnej |
| Zasoby wewnętrznej bazy danych dla modułu równoważenia obciążenia, Standard &ast; | 50 | 150 |
| HA portów, Standard | 1 na frontonie wewnętrzny | 1 na frontonie wewnętrzny |

&ast; Zasoby do 150 dowolną kombinację autonomicznych maszyn wirtualnych, zestawów dostępności i zestawy skalowania maszyny wirtualnej.

Jeśli będzie konieczne zwiększenie limitów domyślnych, [skontaktuj się z pomocą techniczną](../articles/azure-supportability/resource-manager-core-quotas-request.md ).

