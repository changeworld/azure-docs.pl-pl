---
title: Rozwiązanie VMware firmy Azure według CloudSimple — Omówienie węzłów
description: Dowiedz się więcej o węzłach i pojęciach CloudSimple.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 729b832c068dcd401fa6e9d2f4af9193d3859a83
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71845545"
---
# <a name="cloudsimple-nodes-overview"></a>Przegląd węzłów CloudSimple

Węzły są blokami konstrukcyjnymi chmury prywatnej. Węzeł:

* Dedykowany Host obliczeniowy bez systemu operacyjnego, na którym zainstalowano VMware ESXi funkcji hypervisor  
* Jednostka obliczeniowa, którą można zakupić lub zarezerwować w celu utworzenia chmur prywatnych
* Dostępne do zakupu lub rezerwowania w regionie, w którym jest dostępna usługa CloudSimple

Chmurę prywatną można utworzyć na podstawie zakupionych węzłów. Aby utworzyć chmurę prywatną, potrzebne są co najmniej trzy węzły tej samej jednostki SKU. Aby rozwinąć chmurę prywatną, Dodaj dodatkowe węzły.  Można dodać węzły do istniejącego klastra lub utworzyć nowy klaster przez wykupienie węzłów w Azure Portal i skojarzenie ich z usługą CloudSimple.  Wszystkie zakupione węzły są widoczne w ramach usługi CloudSimple.  

## <a name="purchased-nodes"></a>Zakupione węzły

Zakupione węzły zapewniają pojemność płatną zgodnie z rzeczywistym użyciem. Węzły zakupów ułatwiają szybkie skalowanie klastra VMware na żądanie. Możesz dodać węzły w razie potrzeby lub usunąć zakupionego węzła, aby przeskalować swój klaster VMware. Zakupione węzły są rozliczane co miesiąc i są naliczane za subskrypcję, w której zostały zakupione.

* Jeśli płacisz za subskrypcję platformy Azure za pomocą karty kredytowej, karta jest rozliczana od razu.
* Jeśli opłata jest naliczana według faktury, opłaty są naliczane na następnej fakturze.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>Rozwiązanie VMware według jednostki SKU węzłów CloudSimple

Następujące typy węzłów są dostępne do zakupu lub rezerwacji.

| Magazyn           | CS28 — węzeł                 | CS36 — węzeł                 | CS36m — węzeł                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| okolicy        | Wschodnie stany USA, zachodnie stany USA            | Wschodnie stany USA, zachodnie stany USA            | Europa Zachodnia                 |
| CPU           | 2\.2,2 GHz, 28 rdzeni (56 HT) | 2 rdzenie 2,3 GHz, 36 (72 HT) | 2 rdzenie 2,3 GHz, 36 (72 HT) |
| Pamięć RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| Dysk pamięci podręcznej    | 1,6 – TB interfejsu NVMe                 | 3,2 – TB interfejsu NVMe                 | 3,2 – TB interfejsu NVMe                 |
| Dysk pojemności | 5,625 TB nieprzetworzonych                | 11,25 TB nieprzetworzonych                | 15,36 TB nieprzetworzonych                |
| Typ magazynu  | Wszystkie lampy błyskowe                   | Wszystkie lampy błyskowe                   | Wszystkie lampy błyskowe                   |

## <a name="limits"></a>Ograniczeń

Poniższe limity węzłów mają zastosowanie do chmur prywatnych.

| Zasób | Granice |
|----------|-------|
| Minimalna liczba węzłów do utworzenia chmury prywatnej | 3 |
| Maksymalna liczba węzłów w klastrze w chmurze prywatnej | 16 |
| Maksymalna liczba węzłów w chmurze prywatnej | 64 |
| Minimalna liczba węzłów w nowym klastrze | 3 |

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [kupić węzły](create-nodes.md)
* Informacje o [chmurach prywatnych](cloudsimple-private-cloud.md)
