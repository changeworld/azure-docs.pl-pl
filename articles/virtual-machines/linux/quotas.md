---
title: przydziały vCPU dla platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o vCPU przydziały dla platformy Azure.
keywords: ''
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: a880ee18bb13b2cd8471cc58157469555397b872
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34716520"
---
# <a name="virtual-machine-vcpu-quotas"></a>Przydziały vCPU maszyny wirtualnej

Przydziały vCPU dla maszyn wirtualnych i zestawy skalowania maszyny wirtualnej są rozmieszczone w dwóch warstw dla każdej subskrypcji, w każdym regionie. Pierwsza warstwa jest całkowita regionalnych Vcpu, a druga warstwa jest różnych wirtualna rozmiar rodziny rdzeni takich jak Vcpu D-series. Zawsze, gdy nowa maszyna wirtualna jest wdrożona Vcpu dla maszyny Wirtualnej nie może przekraczać limit przydziału vCPU rodziny rozmiar maszyny Wirtualnej lub przydziału całkowita regionalnych vCPU. W przypadku przekroczenia jednej z tych przydziałów wdrożenia maszyny Wirtualnej będzie niemożliwe. Istnieje również limit przydziału dla ogólnej liczby maszyn wirtualnych w tym regionie. Szczegółowe informacje o każdej z tych przydziałów są widoczne w **użycia + przydziały** sekcji **subskrypcji** strony [portalu Azure](https://portal.azure.com), lub można zbadać wartości przy użyciu platformy Azure INTERFEJS WIERSZA POLECENIA.


## <a name="check-usage"></a>Sprawdź użycie

Można sprawdzić, używając przydział użycia [użycie listy az maszyny wirtualnej](/cli/azure/vm#az_vm_list_usage).

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

## <a name="reserved-vm-instances"></a>Wystąpienia zarezerwowane maszyn wirtualnych
Ograniczone do jednego subskrypcji, wystąpień maszyny Wirtualnej zarezerwowana doda aspekt nowe do przydziały vCPU. Te wartości opisano liczbę wystąpień podane rozmiar, który musi być możliwy do wdrożenia w ramach subskrypcji. Funkcje te działają jako element zastępczy w systemie przydziału, aby upewnić się, że ten przydział jest zarezerwowana do upewnij się, że można wdrożyć w ramach subskrypcji są zastrzeżone wystąpień. Na przykład jeśli określonej subskrypcji ma 10 Standard_D1 zastrzeżone wystąpień ograniczenie użycia dla wystąpień zastrzeżone Standard_D1 będzie 10. Spowoduje to Azure upewnić się, że zawsze są dostępne w całkowita regionalnych Vcpu przydział do zastosowania w przypadku wystąpienia Standard_D1 Vcpu co najmniej 10 i są dostępne w standardowe rodziny D vCPU przydział do zastosowania w przypadku wystąpienia Standard_D1 Vcpu co najmniej 10.

Jeśli zwiększenia limitu przydziału jest wymagana, aby zakupić pojedynczego RI subskrypcji, możesz [zażądać zwiększenia limitu przydziału](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) w ramach subskrypcji.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji dotyczących rozliczeń i przydziały, zobacz [subskrypcji platformy Azure i usługi limity, przydziały i ograniczenia](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
