---
title: Przykłady wiersza polecenia platformy Azure dla usługi równoważenia obciążenia
titlesuffix: Azure Load Balancer
description: Przykłady interfejsu wiersza polecenia platformy Azure
services: load-balancer
documentationcenter: load-balancer
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/14/2018
ms.author: allensu
ms.openlocfilehash: 9567dc1425ea74a9f46912532c5b8e4e4afc2fdb
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275693"
---
# <a name="azure-cli-samples-for-load-balancer"></a>Przykłady wiersza polecenia platformy Azure dla usługi równoważenia obciążenia

Poniższa tabela zawiera linki do skryptów bash utworzonych za pomocą interfejsu wiersza polecenia platformy Azure.

| | |
|-|-|
| [Równoważenie obciążenia ruchem do maszyn wirtualnych wysokiej dostępności](./scripts/load-balancer-linux-cli-sample-nlb.md) | Tworzy kilka maszyn wirtualnych na platformie o wysokiej dostępności i konfiguracji ze zrównoważonym obciążeniem. |
| [Równoważenie obciążenia maszyn wirtualnych w różnych strefach dostępności](./scripts/load-balancer-linux-cli-sample-zone-redundant-frontend.md) | Tworzy trzy maszyny wirtualne w różnych strefach dostępności w obrębie regionu i standardowego modułu równoważenia obciążenia przy użyciu adresu IP frontonu strefowo nadmiarowe. Ta konfiguracja usługi równoważenia obciążenia pomaga chronić aplikacje i dane z prawdopodobieństwo wystąpienia awarii lub utraty całego centrum danych. |
|[Równoważenie obciążenia maszyn wirtualnych w określonej strefie dostępności](./scripts/load-balancer-linux-cli-sample-zonal-frontend.md)|Tworzy trzy maszyny wirtualne, standardowy moduł równoważenia obciążenia za pomocą adresu IP frontonu strefowych, który ułatwia wyrównywanie ścieżki danych i zasobów w jednej strefie dla danego regionu.|
| [Równoważ obciążenie powodowane przez wielu witryn sieci Web na maszynach wirtualnych](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md) | Tworzy dwie maszyny wirtualne z wieloma konfiguracjami IP, dołączone do zestawu dostępności platformy Azure, za pośrednictwem usługi Azure Load Balancer. |
| | |

