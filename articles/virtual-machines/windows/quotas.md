---
title: limity przydziału procesorów wirtualnych na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o limity przydziału procesorów wirtualnych na platformie Azure.
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
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 8912387583c24945de22bcb029d40e4d4766b1fb
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67719865"
---
# <a name="virtual-machine-vcpu-quotas"></a>Limity przydziału procesorów wirtualnych maszyny wirtualnej

Limity przydziału procesorów wirtualnych dla maszyn wirtualnych i zestawów skalowania maszyn wirtualnych są rozmieszczone w dwóch warstwach dla każdej subskrypcji, w każdym regionie. Pierwsza warstwa jest łączna liczba procesorów wirtualnych Vcpu, a druga warstwa jest różnych rdzeni maszyn wirtualnych rozmiar rodziny takich jak seria D procesorów wirtualnych Vcpu. Zawsze, gdy nowa maszyna wirtualna jest wdrożona procesorów wirtualnych dla maszyny Wirtualnej nie może przekraczać limit przydziału procesorów wirtualnych dla rodziny rozmiaru maszyny Wirtualnej lub limitu przydziału łączna liczba procesorów wirtualnych w regionalnych. Jeśli jedno z tych limitów przydziału zostaną przekroczone, wdrażanie maszyny Wirtualnej będzie niemożliwe. Istnieje również limit przydziału dla całkowitej liczby maszyn wirtualnych w regionie. Informacji dotyczących każdej z tych limitów przydziału można zobaczyć w **użycie i przydziały** części **subskrypcji** strony w [witryny Azure portal](https://portal.azure.com), lub można wyszukać wartości za pomocą Program PowerShell.

 [!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)] 
 
## <a name="check-usage"></a>Sprawdzanie użycia

Możesz użyć [Get AzVMUsage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmusage) polecenia cmdlet, aby sprawdzić użycie przydziału.

```azurepowershell-interactive
Get-AzVMUsage -Location "East US"
```

Dane wyjściowe będą wyglądać następująco:

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
Zarezerwowane wystąpienia maszyn wirtualnych, które są ograniczone do pojedynczej subskrypcji bez elastyczność rozmiaru maszyny Wirtualnej, zostaną dodane nowe aspekt do limity przydziału procesorów wirtualnych. Wartości te opisują liczbę wystąpień podane rozmiar, który musi być możliwy do wdrożenia w ramach subskrypcji. Działają jako symbol zastępczy w systemie limitu przydziału, aby upewnić się, że ten limit przydziału jest zarezerwowana do upewnij się, że wystąpienia zarezerwowane maszyn wirtualnych możliwych do wdrożenia w ramach subskrypcji. Na przykład jeśli 10 maszyna wirtualna Standard_D1 wystąpienia zarezerwowane maszyn wirtualnych limit użycia dla określonej subskrypcji maszyna wirtualna Standard_D1 zarezerwowane wystąpienia maszyn wirtualnych będzie równa 10. Spowoduje to platformy Azure w celu zapewnienia, że zawsze są dostępne w całkowitej regionalnego przydziału procesorów wirtualnych do użycia dla wystąpień maszyna wirtualna Standard_D1 co najmniej 10 procesorów wirtualnych Vcpu i dostępne do limitu przydziału procesorów wirtualnych rodzina Standard D ma być używany dla wystąpień maszyna wirtualna Standard_D1 są co najmniej 10 procesorów wirtualnych.

Jeśli zwiększenia limitu przydziału jest wymagany do zakupu wystąpień Zarezerwowanych na pojedynczej subskrypcji, możesz to zrobić [zażądać zwiększenia limitu przydziału](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) w ramach Twojej subskrypcji.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rozliczeń i przydziałów, zobacz [subskrypcji platformy Azure i limity, przydziały i ograniczenia](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
