---
title: Przegląd węzłów dla rozwiązań VMware według CloudSimple — Azure
description: Dowiedz się więcej o węzłach i pojęciach CloudSimple.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 346bd046810ebae5142bc23400419857000d0c8e
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812578"
---
# <a name="cloudsimple-nodes-overview"></a>Przegląd węzłów CloudSimple

Węzeł:

* Dedykowany Host obliczeniowy bez systemu operacyjnego, na którym zainstalowano VMware ESXi funkcji hypervisor  
* Jednostka obliczeniowa, którą można zainicjować lub zarezerwować w celu utworzenia chmur prywatnych  
* Dostępne do aprowizacji lub rezerwowania w regionie, w którym jest dostępna usługa CloudSimple

Węzły tworzą bloki w chmurze prywatnej.  Aby utworzyć chmurę prywatną, potrzebne są co najmniej trzy węzły tej samej jednostki SKU.  Aby rozwinąć chmurę prywatną, Dodaj dodatkowe węzły.  Można dodać węzły do istniejącego klastra. Można też utworzyć nowy klaster za pomocą węzłów aprowizacji w Azure Portal i skojarzyć je z usługą CloudSimple.  Wszystkie węzły, które są obsługiwane, są widoczne w ramach usługi CloudSimple.  Chmurę prywatną można utworzyć na podstawie węzłów zainicjowanych w portalu CloudSimple.

## <a name="provisioned-nodes"></a>Węzły inicjowane

Węzły z zainicjowaną obsługą zapewniają pojemność płatność zgodnie z rzeczywistym użyciem. Węzły aprowizacji ułatwiają szybkie skalowanie klastra VMware na żądanie. Możesz dodać węzły w razie potrzeby lub usunąć węzeł aprowizacji, aby przeskalować swój klaster VMware. na węzłach inicjowanych jest rozliczana co miesiąc i naliczana jest opłata za subskrypcję, w której są one inicjowane:

* Jeśli płacisz za subskrypcję platformy Azure za pomocą karty kredytowej, karta jest rozliczana od razu.
* Jeśli opłata jest naliczana według faktury, opłaty są naliczane na następnej fakturze.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>Rozwiązanie VMware według jednostki SKU węzłów CloudSimple

Następujące węzły typów są dostępne do aprowizacji lub rezerwacji.

| SKU | CS28 — węzeł | CS36 — węzeł |
|-----|-------------|-------------|
| Procesor CPU | 2\.2,2 GHz, 28 rdzeni (56 HT) | 2 rdzenie 2,3 GHz, 36 (72 HT) |
| Pamięć RAM | 256 GB | 512 GB |
| Dysk pamięci podręcznej |  1,6 – TB interfejsu NVMe | 3,2 – TB interfejsu NVMe |
| Dysk pojemności | 5,625 TB nieprzetworzonych | 11,25 TB nieprzetworzonych |
| Typ magazynu | Wszystkie lampy błyskowe | Wszystkie lampy błyskowe |

## <a name="limits"></a>Limity

Poniższe limity węzłów mają zastosowanie do chmur prywatnych.

| Resource | Limit |
|----------|-------|
| Minimalna liczba węzłów do utworzenia chmury prywatnej | 3 |
| Maksymalna liczba węzłów w klastrze w chmurze prywatnej | 16 |
| Maksymalna liczba węzłów w chmurze prywatnej | 64 |
| Minimalna liczba węzłów w nowym klastrze | 3 |

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [inicjować obsługę węzłów](create-nodes.md)
* Informacje o [chmurze prywatnej](cloudsimple-private-cloud.md)