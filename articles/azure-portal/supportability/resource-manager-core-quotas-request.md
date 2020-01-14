---
title: Azure Resource Manager żądania wzrostu przydziału vCPU | Microsoft Docs
description: Azure Resource Manager żądania zwiększenia limitu przydziału vCPU
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: c3248b10b4ad343e8776056d42ec153130f0061f
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897106"
---
# <a name="quota-increase-requests"></a>Żądania zwiększenia limitu przydziału

Menedżer zasobów przydziały vCPU dla maszyn wirtualnych i zestawów skalowania maszyn wirtualnych są wymuszane w dwóch warstwach dla każdej subskrypcji w każdym regionie. 

Pierwsza warstwa to łączny limit procesorów wirtualnych vCPU regionalnych (dla wszystkich serii maszyn wirtualnych), a druga warstwa to limit procesorów wirtualnych vCPU dla serii maszyn wirtualnych (na przykład procesorów wirtualnych vCPU serii D). Za każdym razem, gdy zostanie wdrożona nowa maszyna wirtualna, suma nowych i istniejących procesorów wirtualnych vCPU użycie dla tej serii maszyn wirtualnych nie może przekroczyć limitu przydziału vCPU zatwierdzonego dla danej serii maszyn wirtualnych. Dodatkowo całkowita i istniejąca liczba vCPU wdrożona na wszystkich seriach maszyn wirtualnych nie powinna przekraczać całkowitego przydziału regionalnego procesorów wirtualnych vCPU zatwierdzonego dla subskrypcji. Jeśli jeden z tych przydziałów zostanie przekroczony, wdrożenie maszyny wirtualnej nie będzie dozwolone.
Można zażądać zwiększenia limitu przydziału procesorów wirtualnych vCPU dla serii maszyn wirtualnych z Azure Portal. Zwiększenie limitu przydziału serii maszyn wirtualnych powoduje automatyczne zwiększenie łącznej granicy procesorów wirtualnych vCPU w regionie o tej samej wartości. 

Po utworzeniu nowej subskrypcji domyślna łączna liczba procesorów wirtualnych vCPU regionalnych może nie być równa sumie domyślnych przydziałów vCPU dla wszystkich poszczególnych serii maszyn wirtualnych. Może to skutkować subskrypcją z wystarczającym limitem przydziału dla każdej pojedynczej serii maszyn wirtualnych, która ma zostać wdrożona, ale nie jest wystarczająca do całkowitego procesorów wirtualnych vCPU regionalnych dla wszystkich wdrożeń. W takim przypadku musisz przesłać żądanie, aby w sposób jawny zwiększyć łączny limit procesorów wirtualnych vCPU regionalny. Łączny limit procesorów wirtualnych vCPU regionalnych nie może przekroczyć sumy zatwierdzonego przydziału dla całej serii maszyn wirtualnych dla regionu.

Dowiedz się więcej o przydziałach na [stronie limity przydziału vCPU maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) oraz na stronie [limitów subskrypcji i usług platformy Azure](https://aka.ms/quotalimits) . 

