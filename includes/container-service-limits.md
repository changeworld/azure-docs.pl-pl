---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: container-service
author: mmacy
ms.service: container-service
ms.topic: include
ms.date: 08/31/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 1e8913d31677f3da9b6eb9d2216d8d9ec8b186b4
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666904"
---
| Zasób | Limit domyślny |
| --- | :--- |
| Maksymalna liczba klastrów w subskrypcji | 100 |
| Maksymalna liczba węzłów w klastrze | 100 |
| Maksymalna liczba zasobników w węźle: [sieć podstawowa][basic-networking] z rozwiązaniem Kubenet | 110 |
| Maksymalna liczba zasobników w węźle: [sieć zaawansowana][advanced-networking] z rozwiązaniem Azure CNI | Wdrożenie za pomocą interfejsu wiersza polecenia platformy Azure: 110<sup>1</sup><br />Szablon usługi Resource Manager: 110<sup>1</sup><br />Wdrożenie za pomocą portalu: 30 |

<sup>1</sup> Tę wartość można skonfigurować podczas wdrażania klastra, jeśli klaster usługi AKS jest wdrażany przy użyciu interfejsu wiersza polecenia platformy Azure lub szablonu usługi Resource Manager.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/networking-overview.md#basic-networking
[advanced-networking]: ../articles/aks/networking-overview.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
