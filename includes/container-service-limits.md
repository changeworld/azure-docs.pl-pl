---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 08/31/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 71294824bd3dd5215c388cfcd44382c7eee123ad
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2018
ms.locfileid: "48874001"
---
| Zasób | Limit domyślny |
| --- | :--- |
| Maksymalna liczba klastrów w subskrypcji | 100 |
| Maksymalna liczba węzłów w klastrze | 100 |
| Maksymalna liczba zasobników w węźle: [sieć podstawowa][basic-networking] z rozwiązaniem Kubenet | 110 |
| Maksymalna liczba zasobników w węźle: [sieć zaawansowana][advanced-networking] z rozwiązaniem Azure CNI | Wdrożenie za pomocą interfejsu wiersza polecenia platformy Azure: 30<sup>1</sup><br />Szablon usługi Resource Manager: 30<sup>1</sup><br />Wdrożenie za pomocą portalu: 30 |

<sup>1</sup> Tę wartość można skonfigurować podczas wdrażania klastra, jeśli klaster usługi AKS jest wdrażany przy użyciu interfejsu wiersza polecenia platformy Azure lub szablonu usługi Resource Manager.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/networking-overview.md#basic-networking
[advanced-networking]: ../articles/aks/networking-overview.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
