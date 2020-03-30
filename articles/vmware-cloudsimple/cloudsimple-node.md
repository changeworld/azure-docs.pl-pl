---
title: Rozwiązanie Azure VMware by CloudSimple — omówienie węzłów
description: Dowiedz się więcej o węzłach i pojęciach CloudSimple.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 65afe26a98a53b00b72a1ea2b49799db2049b727
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024929"
---
# <a name="cloudsimple-nodes-overview"></a>Omówienie węzłów CloudSimple

Węzły są blokami konstrukcyjnymi chmury prywatnej. Węzeł to:

* Dedykowany host obliczeniowy bez systemu metalowego, w którym zainstalowany jest hipernadzorca VMware ESXi  
* Jednostka obliczeniowa, którą można udostępnić lub zarezerwować w celu utworzenia chmur prywatnych
* Dostępne do udostępniania lub rezerwowania w regionie, w którym dostępna jest usługa CloudSimple

Tworzenie chmury prywatnej z aprowizowanych węzłów. Aby utworzyć chmurę prywatną, potrzebujesz co najmniej trzech węzłów tej samej jednostki SKU. Aby rozwinąć chmurę prywatną, dodaj dodatkowe węzły.  Można dodać węzły do istniejącego klastra lub utworzyć nowy klaster, inicjując obsługę administracyjną węzłów w witrynie Azure portal i kojarząc je z usługą CloudSimple.  Wszystkie aprowizowane węzły są widoczne w usłudze CloudSimple.  

## <a name="provisioned-nodes"></a>Aprowerowuj węzły

Zaaprowizowane węzły zapewniają pojemność płatności zgodnie z rzeczywistymu poła. Inicjowanie obsługi administracyjnej węzłów ułatwia szybkie skalowanie klastra VMware na żądanie. W razie potrzeby można dodać węzły lub usunąć aprowizowanym węzeł, aby skalować w dół klaster VMware. Zaaprowizowane węzły są rozliczane co miesiąc i obciążane subskrypcją, w której są aprowizacji.

* Jeśli płacisz za subskrypcję platformy Azure kartą kredytową, karta zostanie natychmiast naliczona.
* Jeśli faktura zostanie naliczona, opłaty pojawią się na następnej fakturze.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>Rozwiązanie VMware według jednostki SKU węzłów CloudSimple

Następujące typy węzłów są dostępne do inicjowania obsługi administracyjnej lub rezerwacji.

| SKU           | CS28 - Węzeł                 | CS36 - Węzeł                 | CS36m - Węzeł                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| Region        | Wschodnie stany USA, zachodnie stany USA            | Wschodnie stany USA, zachodnie stany USA            | Europa Zachodnia                 |
| Procesor CPU           | 2x2.2 GHz, 28 rdzeni (56 HT) | 2x2.3 GHz, 36 rdzeni (72 HT) | 2x2.3 GHz, 36 rdzeni (72 HT) |
| Pamięć RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| Dysk pamięci podręcznej    | 1,6 TB NVMe                 | 3,2 TB NVMe                 | 3,2 TB NVMe                 |
| Dysk pojemności | 5,625 TB surowca                | 11,25 TB surowca                | 15,36 TB surowca                |
| Typ magazynu  | Wszystkie Flash                   | Wszystkie Flash                   | Wszystkie Flash                   |

## <a name="limits"></a>Limity

Następujące limity węzłów dotyczą chmur prywatnych.

| Zasób | Limit |
|----------|-------|
| Minimalna liczba węzłów do utworzenia chmury prywatnej | 3 |
| Maksymalna liczba węzłów w klastrze w chmurze prywatnej | 16 |
| Maksymalna liczba węzłów w chmurze prywatnej | 64 |
| Minimalna liczba węzłów w nowym klastrze | 3 |

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [aprowizować węzły](create-nodes.md)
* Dowiedz się więcej o [chmurach prywatnych](cloudsimple-private-cloud.md)
