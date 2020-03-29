---
title: Żądania zwiększenia przydziału programu Azure Resource Manager w ramach programu VCPU
description: Żądania zwiększenia przydziału programu Azure Resource Manager w ramach programu VCPU
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: cdbf7364a275eb246615f398044456645a96d1a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843671"
---
# <a name="quota-increase-requests"></a>Żądania zwiększenia limitu przydziału

Przydziały vCPU Menedżera zasobów dla maszyn wirtualnych i zestawów skalowania maszyn wirtualnych są wymuszane w dwóch warstwach dla każdej subskrypcji w każdym regionie.

Pierwsza warstwa to limit całkowitych regionalnych procesorów wirtualnych we wszystkich seriach maszyn wirtualnych. Druga warstwa to limit procesorów wirtualnych serii maszyn wirtualnych na, takich jak procesory wirtualne z serii D. Za każdym razem, gdy ma zostać wdrożona nowa maszyna wirtualna, suma użycia nowych i istniejących procesorów wirtualnych dla tej serii maszyn wirtualnych nie może przekraczać przydziału wirtualnego zatwierdzonego dla danej serii maszyn wirtualnych. Ponadto całkowita liczba nowych i istniejących procesorów wirtualnych wdrożonych we wszystkich seriach maszyn wirtualnych nie powinna przekraczać przydziału całkowita regionalna procesory wirtualne zatwierdzone dla subskrypcji. Jeśli którykolwiek z tych przydziałów zostaną przekroczone, wdrożenie maszyny Wirtualnej nie będzie dozwolone.
Można zażądać zwiększenia limitu przydziału procesorów wirtualnych dla serii maszyn wirtualnych z witryny Azure portal. Zwiększenie przydziału serii maszyn wirtualnych automatycznie zwiększa limit regionalnych procesorów wirtualnych o tę samą kwotę.

Podczas tworzenia nowej subskrypcji domyślne regionalne procesory wirtualne suma nie może być równa sumie domyślnych przydziałów procesorów wirtualnych dla wszystkich poszczególnych serii maszyn wirtualnych. Ten fakt może spowodować subskrypcji z wystarczającą ilością przydziału dla każdej serii maszyn wirtualnych, które chcesz wdrożyć. Może brakować wystarczającego przydziału dla regionalnych procesorów wirtualnych totalnych dla wszystkich wdrożeń. W takim przypadku należy przesłać żądanie, aby jawnie zwiększyć limit regionalnych procesorów wirtualnych. Całkowity regionalny limit procesorów wirtualnych nie może przekroczyć sumy zatwierdzonego przydziału we wszystkich seriach maszyn wirtualnych dla regionu.

Aby uzyskać więcej informacji na temat przydziałów, zobacz [Przydziały vCPU maszyn wirtualnych maszyny wirtualnej](../../virtual-machines/windows/quotas.md) oraz [limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../../azure-resource-manager/management/azure-subscription-service-limits.md).

