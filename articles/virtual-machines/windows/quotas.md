---
title: Przydziały procesora wirtualnego dla platformy Azure
description: Dowiedz się więcej o przydziałach procesora wirtualnego dla platformy Azure.
keywords: ''
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 20017f42031e126bbdae40f66bf4c36efd12f17c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75893468"
---
# <a name="virtual-machine-vcpu-quotas"></a>Limity przydziału procesorów wirtualnych maszyny wirtualnej

Przydziały procesora wirtualnego dla maszyn wirtualnych i zestawów skalowania maszyn wirtualnych są rozmieszczone w dwóch warstwach dla każdej subskrypcji w każdym regionie. Pierwsza warstwa to całkowita regionalna maszyna wirtualna, a druga warstwa to różne rdzenie rodziny maszyn wirtualnych, takie jak procesory wirtualne z serii D. Za każdym razem, gdy nowa maszyna wirtualna jest wdrażana, procesory wirtualne dla maszyny wirtualnej nie mogą przekraczać przydziału vCPU dla rodziny rozmiarów maszyn wirtualnych ani całkowitego regionalnego przydziału vCPU. Jeśli którykolwiek z tych przydziałów zostaną przekroczone, wdrożenie maszyny Wirtualnej nie będzie dozwolone. Istnieje również przydział dla ogólnej liczby maszyn wirtualnych w regionie. Szczegóły dotyczące każdego z tych przydziałów można zobaczyć w **użycia + przydziały** sekcji **subskrypcji** strony w [witrynie Azure portal](https://portal.azure.com)lub można zbadać wartości przy użyciu programu PowerShell.

   
 
## <a name="check-usage"></a>Sprawdzanie użycia

Polecenia cmdlet [Get-AzVMUsage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmusage) można użyć do sprawdzenia użycia przydziału.

```azurepowershell-interactive
Get-AzVMUsage -Location "East US"
```

Dane wyjściowe będą wyglądać podobnie do tego:

```
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional vCPUs                         4   260 Count
Virtual Machines                             4 10000 Count
Virtual Machine Scale Sets                   1  2000 Count
Standard B Family vCPUs                      1    10 Count
Standard DSv2 Family vCPUs                   1   100 Count
Standard Dv2 Family vCPUs                    2   100 Count
Basic A Family vCPUs                         0   100 Count
Standard A0-A7 Family vCPUs                  0   250 Count
Standard A8-A11 Family vCPUs                 0   100 Count
Standard D Family vCPUs                      0   100 Count
Standard G Family vCPUs                      0   100 Count
Standard DS Family vCPUs                     0   100 Count
Standard GS Family vCPUs                     0   100 Count
Standard F Family vCPUs                      0   100 Count
Standard FS Family vCPUs                     0   100 Count
Standard NV Family vCPUs                     0    24 Count
Standard NC Family vCPUs                     0    48 Count
Standard H Family vCPUs                      0     8 Count
Standard Av2 Family vCPUs                    0   100 Count
Standard LS Family vCPUs                     0   100 Count
Standard Dv2 Promo Family vCPUs              0   100 Count
Standard DSv2 Promo Family vCPUs             0   100 Count
Standard MS Family vCPUs                     0     0 Count
Standard Dv3 Family vCPUs                    0   100 Count
Standard DSv3 Family vCPUs                   0   100 Count
Standard Ev3 Family vCPUs                    0   100 Count
Standard ESv3 Family vCPUs                   0   100 Count
Standard FSv2 Family vCPUs                   0   100 Count
Standard ND Family vCPUs                     0     0 Count
Standard NCv2 Family vCPUs                   0     0 Count
Standard NCv3 Family vCPUs                   0     0 Count
Standard LSv2 Family vCPUs                   0     0 Count
Standard Storage Managed Disks               2 10000 Count
Premium Storage Managed Disks                1 10000 Count
```


## <a name="reserved-vm-instances"></a>Wystąpienia zarezerwowane maszyn wirtualnych
Zarezerwowane wystąpienia maszyn wirtualnych, które są ograniczone do pojedynczej subskrypcji bez elastyczności rozmiaru maszyny Wirtualnej, doda nowy aspekt do przydziałów vCPU. Te wartości opisują liczbę wystąpień określonego rozmiaru, które muszą być wdrażalne w subskrypcji. Działają one jako symbol zastępczy w systemie przydziałów, aby upewnić się, że przydział jest zarezerwowany, aby zapewnić, że zarezerwowane wystąpienia maszyn wirtualnych są wdrażane w ramach subskrypcji. Na przykład jeśli określona subskrypcja ma 10 Standard_D1 zarezerwowanych wystąpień maszyn wirtualnych limit użycia dla Standard_D1 zarezerwowanych wystąpień maszyn wirtualnych będzie 10. Spowoduje to, że platforma Azure zapewni, że w przydziałzie totalialnych procesorów wirtualnych będzie używanych co najmniej 10 procesorów wirtualnych w przydziału regionalnych procesorów wirtualnych, które mają być używane dla Standard_D1 wystąpień, a w przydziałzie wirtualnego procesora typu D dostępne jest co najmniej 10 procesorów wirtualnych, które mają być używane dla Standard_D1 wystąpień.

Jeśli zwiększenie przydziału jest wymagane do zakupu identyfikatora RI pojedynczej subskrypcji, można [zażądać zwiększenia przydziału](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) w ramach subskrypcji.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rozliczeń i przydziałów, zobacz [Limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
