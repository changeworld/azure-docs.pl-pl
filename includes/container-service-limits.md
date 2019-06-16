---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 10/11/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: a2729af6a689daa551fc01f585324d53a8770a9b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66142997"
---
| Resource | Limit domyślny |
| --- | :--- |
| Maksymalna klastrów na subskrypcję | 100 |
| Maksymalna liczba węzłów w klastrze | 100 |
| Maksymalna zasobników na każdym węźle: [sieć podstawowa][basic-networking] z rozwiązaniem Kubenet | 110 |
| Maksymalna zasobników na każdym węźle: [Zaawansowany siecią] [ advanced-networking] przy użyciu interfejsu sieciowego kontenera platformy Azure | Wdrożenie za pomocą interfejsu wiersza polecenia platformy Azure: 30<sup>1</sup><br />Szablon usługi Azure Resource Manager: 30<sup>1</sup><br />Wdrożenie za pomocą portalu: 30 |

<sup>1</sup>podczas wdrażania klastra usługi Azure Kubernetes Service (AKS), przy użyciu wiersza polecenia platformy Azure lub w szablonie usługi Resource Manager, ta wartość jest można skonfigurować maksymalnie 250 zasobniki w każdym węźle. Nie można skonfigurować maksymalną zasobników w każdym węźle, po został już wdrożony klaster AKS, czy wdrożyć klaster przy użyciu witryny Azure portal.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
