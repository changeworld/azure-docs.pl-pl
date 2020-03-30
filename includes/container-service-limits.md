---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6b4678b381e769993b01bbedd1cb4c0aeefc0cc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334629"
---
| Zasób | Limit |
| --- | :--- |
| Maksymalna liczba klastrów na subskrypcję | 100 |
| Maksymalna liczba węzłów na klaster z zestawami dostępności maszyn wirtualnych i podstawową jednostką SKU modułu równoważenia obciążenia  | 100 |
| Maksymalna liczba węzłów na klaster z zestawami skalowania maszyny wirtualnej i [standardową jednostką SKU modułu równoważenia obciążenia][standard-load-balancer] | 1000 (100 węzłów na [pulę węzłów)][node-pool] |
| Maksymalna liczba zasobników na węzeł: [Podstawowa sieć][basic-networking] z Kubenet | 110 |
| Maksymalna liczba zasobników na węzeł: [zaawansowana sieć][advanced-networking] z interfejsem azure container networking | Wdrożenie za pomocą interfejsu wiersza polecenia platformy Azure: 30<sup>1</sup><br />Szablon usługi Azure Resource Manager: 30<sup>1</sup><br />Wdrożenie za pomocą portalu: 30 |

<sup>1.</sup> Po wdrożeniu klastra usługi Azure Kubernetes (AKS) z interfejsu wiersza polecenia platformy Azure lub szablonem Menedżera zasobów ta wartość można skonfigurować do 250 zasobników na węzeł. Nie można skonfigurować maksymalną liczbę zasobników na węzeł po już wdrożonym klastrze AKS lub w przypadku wdrożenia klastra przy użyciu witryny Azure portal.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-standard-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
