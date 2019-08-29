---
title: vCPU przydziały dla platformy Azure | Microsoft Docs
description: Dowiedz się więcej na temat przydziałów vCPU dla platformy Azure.
keywords: ''
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: add0170c016b1a8226424ccf9b25cfde3a4c196f
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70091439"
---
# <a name="virtual-machine-vcpu-quotas"></a>Limity przydziału procesorów wirtualnych maszyny wirtualnej

Przydziały vCPU dla maszyn wirtualnych i zestawów skalowania maszyn wirtualnych są ułożone w dwóch warstwach dla każdej subskrypcji w każdym regionie. Pierwsza warstwa jest całkowitą procesorów wirtualnych vCPU regionalną, a druga warstwy to różne rdzenie rodzinne rozmiaru maszyny wirtualnej, takie jak seria D procesorów wirtualnych vCPU. Za każdym razem, gdy zostanie wdrożona nowa maszyna wirtualna, procesorów wirtualnych vCPU dla maszyny wirtualnej nie może przekraczać limitu przydziału vCPU dla rodziny rozmiaru maszyny wirtualnej ani łącznego limitu regionalnego vCPU. Jeśli jeden z tych przydziałów zostanie przekroczony, wdrożenie maszyny wirtualnej nie będzie dozwolone. Istnieje również limit przydziału ogólnej liczby maszyn wirtualnych w regionie. Szczegółowe informacje na temat każdego z tych przydziałów można znaleźć w sekcji **użycie i** przydziały strony **subskrypcji** w [Azure Portal](https://portal.azure.com)lub można wykonać zapytanie dotyczące wartości przy użyciu interfejsu wiersza polecenia platformy Azure.


## <a name="check-usage"></a>Sprawdzanie użycia

Użycie limitu przydziału można sprawdzić za pomocą polecenia [AZ VM list-Usage](/cli/azure/vm).

```azurecli-interactive
az vm list-usage --location "East US" -o table
```

Dane wyjściowe powinny wyglądać następująco:


```
Name                                CurrentValue    Limit
--------------------------------  --------------  -------
Availability Sets                              0     2000
Total Regional vCPUs                          29      100
Virtual Machines                               7    10000
Virtual Machine Scale Sets                     0     2000
Standard DSv3 Family vCPUs                     8      100
Standard DSv2 Family vCPUs                     3      100
Standard Dv3 Family vCPUs                      2      100
Standard D Family vCPUs                        8      100
Standard Dv2 Family vCPUs                      8      100
Basic A Family vCPUs                           0      100
Standard A0-A7 Family vCPUs                    0      100
Standard A8-A11 Family vCPUs                   0      100
Standard DS Family vCPUs                       0      100
Standard G Family vCPUs                        0      100
Standard GS Family vCPUs                       0      100
Standard F Family vCPUs                        0      100
Standard FS Family vCPUs                       0      100
Standard Storage Managed Disks                 5    10000
Premium Storage Managed Disks                  5    10000
```

## <a name="reserved-vm-instances"></a>Zarezerwowane wystąpienia maszyn wirtualnych
Wystąpienia zarezerwowane maszyn wirtualnych, które są objęte zakresem pojedynczej subskrypcji bez elastyczności rozmiaru maszyny wirtualnej, spowodują dodanie nowego aspektu do przydziałów vCPU. Te wartości opisują liczbę wystąpień określonego rozmiaru, które muszą być wdrożone w subskrypcji. Działają jako symbol zastępczy w systemie przydziału, aby zapewnić, że przydział jest zarezerwowany w celu zapewnienia wdrożenia rezerwacji platformy Azure w ramach subskrypcji. Na przykład jeśli określona subskrypcja ma 10 Standard_D1, limit użycia dla rezerwacji Standard_D1 wynosi 10. Spowoduje to, że platforma Azure będzie mieć pewność, że w łącznej liczbie miejscowej przydziału procesorów wirtualnych vCPU jest dostępna co najmniej 10 procesorów wirtualnych vCPU, a w przypadku wystąpień Standard_D1 dostępnych jest co najmniej 10 procesorów wirtualnych vCPU.

Jeśli zwiększenie limitu przydziału jest wymagane do zakupu pojedynczej subskrypcji RI, można zażądać [zwiększenia limitu przydziału](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) w ramach subskrypcji.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rozliczeń i przydziałów, zobacz [limity subskrypcji i usług platformy Azure, limity przydziału i ograniczenia](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
