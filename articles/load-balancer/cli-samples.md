---
title: Przykłady interfejsu wiersza polecenia platformy Azure dla modułu równoważenia obciążenia
titleSuffix: Azure Load Balancer
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
ms.openlocfilehash: f35007bc3c47aeec9bcdd8a418983b95f6f20ad6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225416"
---
# <a name="azure-cli-samples-for-load-balancer"></a>Przykłady interfejsu wiersza polecenia platformy Azure dla modułu równoważenia obciążenia

Poniższa tabela zawiera linki do skryptów bash utworzonych za pomocą interfejsu wiersza polecenia platformy Azure.

| | |
|-|-|
| [Równoważenie obciążenia ruchem maszyn wirtualnych w celu uzyskania wysokiej dostępności](./scripts/load-balancer-linux-cli-sample-nlb.md) | Tworzy kilka maszyn wirtualnych w konfiguracji o wysokiej dostępności i z równoważenia obciążenia. |
| [Równoważenie obciążenia maszyn wirtualnych w różnych strefach dostępności](./scripts/load-balancer-linux-cli-sample-zone-redundant-frontend.md) | Tworzy trzy maszyny wirtualne w różnych strefach dostępności w regionie i standardowy moduł równoważenia obciążenia z nadmiarowym adresem IP frontonia strefy. Ta konfiguracja modułu równoważenia obciążenia pomaga chronić aplikacje i dane przed mało prawdopodobną awarią lub utratą całego centrum danych. |
|[Równoważenie obciążenia maszyn wirtualnych w określonej strefie dostępności](./scripts/load-balancer-linux-cli-sample-zonal-frontend.md)|Tworzy trzy maszyny wirtualne, standardowy moduł równoważenia obciążenia z strefowym adresem IP frontonu, który pomaga wyrównać ścieżkę danych i zasoby w jednej strefie dla danego regionu.|
| [Równoważenie obciążenia wielu witryn sieci Web na maszynach wirtualnych](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md) | Tworzy dwie maszyny wirtualne z wieloma konfiguracjami IP, przyłączone do zestawu dostępności platformy Azure, dostępne za pośrednictwem modułu równoważenia obciążenia platformy Azure. |
| | |

