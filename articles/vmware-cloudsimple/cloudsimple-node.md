---
title: Omówienie węzłów dla oprogramowania VMware Solution by CloudSimple - Azure
description: Informacje o węzłach CloudSimple i pojęć.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fb82e31d58d9955efc3b147eccf2b82b8768aeee
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165804"
---
# <a name="cloudsimple-nodes-overview"></a>Omówienie węzłów CloudSimple

Węzeł to:

* Dedykowany zera obliczenia hosta z zainstalowanym funkcji hypervisor VMware ESXi  
* Jednostka przetwarzania danych, które można udostępnić lub rezerw tworzenie chmur prywatnych  
* Dostępne do aprowizowania lub zarezerwować w regionie, w których usługa CloudSimple jest dostępna

Węzły są bloków konstrukcyjnych w chmurze prywatnej.  Aby utworzyć chmurę prywatną, musisz mieć co najmniej trzy węzły tej samej jednostki SKU.  Aby rozwinąć chmury prywatnej, należy dodać więcej węzłów.  Możesz dodać węzły do istniejącego klastra. Alternatywnie można utworzyć nowego klastra, inicjowanie obsługi administracyjnej węzłów w witrynie Azure portal i skojarzenie ich z usługą CloudSimple.  Wszystkie węzły aprowizacji są widoczne w ramach usługi CloudSimple.  Można utworzyć chmurę prywatną z aprowizowaną węzłów w portalu CloudSimple.

## <a name="provisioned-nodes"></a>Elastycznie węzłów

Węzły elastycznie zapewniają pojemność zgodnie z rzeczywistym użyciem. Inicjowanie obsługi administracyjnej węzłów pomoże Ci szybko przeskalować klaster VMware na żądanie. Dodawanie węzłów stosownie do potrzeb lub usunąć węzeł elastycznie skalować klaster VMware. elastycznie węzły są rozliczane co miesiąc, a obciążany opłatą za subskrypcję, w których jest aprowizowane:

* Płacisz za subskrypcję platformy Azure przy użyciu karty kredytowej, karty jest rozliczana natychmiast.
* Jeśli są naliczane za pomocą faktury, opłaty są wyświetlane na następnej fakturze.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>VMware Solution by węzłów CloudSimple jednostki SKU

Następujące węzły typy są dostępne do aprowizowania ani rezerwacji.

| SKU | CS28 - Node | CS36 - Node |
|-----|-------------|-------------|
| Procesor CPU | 2x2.2 GHz, 28 rdzeni (56 zasobników tablicy skrótów) | 2x2.3 GHz, 36 rdzeni (72 zasobników tablicy skrótów) |
| Pamięć RAM | 256 GB | 512 GB |
| Dysk pamięci podręcznej |  NVMe TB w wersji 1.6 | NVMe 3.2 TB |
| Pojemność dysku | 5.625 TB nieprzetworzone | 11,25 TB nieprzetworzone |
| Typ magazynu | Wszystkie Flash | Wszystkie Flash |

## <a name="limits"></a>Limits

Poniższe limity węzła mają zastosowanie do chmur prywatnych.

| Resource | Limit |
|----------|-------|
| Minimalna liczba węzłów w celu utworzenia chmury prywatnej | 3 |
| Maksymalna liczba węzłów w klastrze w chmurze prywatnej | 16 |
| Maksymalna liczba węzłów w chmurze prywatnej | 64 |
| Minimalna liczba węzłów w nowym klastrze | 3 |

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [aprowizacji węzłów](create-nodes.md)
* Dowiedz się więcej o [Chmura prywatna](cloudsimple-private-cloud.md)