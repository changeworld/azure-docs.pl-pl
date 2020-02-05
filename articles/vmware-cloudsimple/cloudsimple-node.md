---
title: Azure VMware Solutions (Automatyczna synchronizacja) — Omówienie węzłów
description: Dowiedz się więcej o węzłach i pojęciach dotyczących automatycznej synchronizacji.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 08d8fc3e6f1f2f83cf3c4fee3fdafb0bd07e336c
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024929"
---
# <a name="avs-nodes-overview"></a>Przegląd automatycznej synchronizacji węzłów

Węzły są blokami konstrukcyjnymi chmury prywatnej. Węzeł:

* Dedykowany Host obliczeniowy bez systemu operacyjnego, na którym zainstalowano VMware ESXi funkcji hypervisor  
* Jednostka obliczeniowa, którą można zakupić lub zarezerwować do tworzenia chmur prywatnych
* Dostępne do zakupu lub rezerwowania w regionie, w którym jest dostępna usługa automatycznej synchronizacji

Chmurę prywatną można utworzyć z poziomu zakupionych węzłów. Aby utworzyć chmurę prywatną do automatycznej synchronizacji, potrzebne są co najmniej trzy węzły tej samej jednostki SKU. Aby rozwinąć chmurę prywatną do automatycznej synchronizacji, Dodaj dodatkowe węzły. Można dodać węzły do istniejącego klastra lub utworzyć nowy klaster przez wykupienie węzłów w Azure Portal i skojarzenie ich z usługą automatycznej synchronizacji. Wszystkie zakupione węzły są widoczne w ramach usługi automatycznej synchronizacji. 

## <a name="provisioned-nodes"></a>Węzły inicjowane

Węzły z zainicjowaną obsługą zapewniają pojemność płatność zgodnie z rzeczywistym użyciem. Węzły aprowizacji ułatwiają szybkie skalowanie klastra VMware na żądanie. Możesz dodać węzły w razie potrzeby lub usunąć węzeł aprowizacji, aby przeskalować swój klaster VMware. Na węzłach inicjowanych jest rozliczana co miesiąc i naliczana jest opłata za subskrypcję, w której są one inicjowane.

* Jeśli płacisz za subskrypcję platformy Azure za pomocą karty kredytowej, karta jest rozliczana od razu.
* Jeśli opłata jest naliczana według faktury, opłaty są naliczane na następnej fakturze.

## <a name="vmware-solution-by-avs-nodes-sku"></a>Rozwiązanie VMware wg automatycznej synchronizacji węzłów SKU

Następujące typy węzłów są dostępne do aprowizacji lub rezerwacji.

| JSZ           | CS28 — węzeł                 | CS36 — węzeł                 | CS36m — węzeł                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| Region        | Zachodnie stany USA, Wschodnie stany USA            | Zachodnie stany USA, Wschodnie stany USA            | Europa Zachodnia                 |
| Procesor CPU           | 2\.2,2 GHz, 28 rdzeni (56 HT) | 2 rdzenie 2,3 GHz, 36 (72 HT) | 2 rdzenie 2,3 GHz, 36 (72 HT) |
| Pamięć RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| Dysk pamięci podręcznej    | 1,6 – TB interfejsu NVMe                 | 3,2 – TB interfejsu NVMe                 | 3,2 – TB interfejsu NVMe                 |
| Dysk pojemności | 5,625 TB nieprzetworzonych                | 11,25 TB nieprzetworzonych                | 15,36 TB nieprzetworzonych                |
| Typ magazynu  | Wszystkie lampy błyskowe                   | Wszystkie lampy błyskowe                   | Wszystkie lampy błyskowe                   |

## <a name="limits"></a>Limity

Poniższe limity węzłów mają zastosowanie do automatycznej synchronizacji chmur prywatnych.

| Zasób | Limit |
|----------|-------|
| Minimalna liczba węzłów do utworzenia chmury prywatnej do automatycznej synchronizacji | 3 |
| Maksymalna liczba węzłów w klastrze w chmurze prywatnej automatycznej synchronizacji | 16 |
| Maksymalna liczba węzłów w chmurze prywatnej automatycznej synchronizacji | 64 |
| Minimalna liczba węzłów w nowym klastrze | 3 |

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [kupić węzły](create-nodes.md)
* Informacje o [automatycznej synchronizacji chmur prywatnych](cloudsimple-private-cloud.md)
* Dowiedz się, jak [inicjować obsługę węzłów](create-nodes.md)
* Informacje o [chmurach prywatnych](cloudsimple-private-cloud.md)
