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
ms.openlocfilehash: 4251f379c517d5ccfd0430987e3d5280208590ff
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49400442"
---
| Zasób | Limit domyślny |
| --- | :--- |
| Maksymalna liczba klastrów w subskrypcji | 100 |
| Maksymalna liczba węzłów w klastrze | 100 |
| Maksymalna liczba zasobników w węźle: [sieć podstawowa][basic-networking] z rozwiązaniem Kubenet | 110 |
| Maksymalna liczba zasobników w węźle: [sieć zaawansowana][advanced-networking] z rozwiązaniem Azure CNI | Wdrożenie za pomocą interfejsu wiersza polecenia platformy Azure: 30<sup>1</sup><br />Szablon usługi Resource Manager: 30<sup>1</sup><br />Wdrożenie za pomocą portalu: 30 |

<sup>1</sup> Tę wartość można skonfigurować do **110 zasobników na węzeł**, jeśli klaster usługi AKS jest wdrażany przy użyciu interfejsu wiersza polecenia platformy Azure lub szablonu usługi Resource Manager. Nie można skonfigurować maksymalnej liczby zasobników na węzeł, jeśli już wdrożono klaster usługi AKS lub w przypadku wdrażania klastra przy użyciu witryny Azure Portal.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
