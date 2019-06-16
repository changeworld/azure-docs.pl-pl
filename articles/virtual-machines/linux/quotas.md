---
title: limity przydziału procesorów wirtualnych na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o limity przydziału procesorów wirtualnych na platformie Azure.
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
ms.openlocfilehash: b7ec3a6919a27633fc36ba7fb1551ead10e06ffe
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60542717"
---
# <a name="virtual-machine-vcpu-quotas"></a>Limity przydziału procesorów wirtualnych maszyny wirtualnej

Limity przydziału procesorów wirtualnych dla maszyn wirtualnych i zestawów skalowania maszyn wirtualnych są rozmieszczone w dwóch warstwach dla każdej subskrypcji, w każdym regionie. Pierwsza warstwa jest łączna liczba procesorów wirtualnych Vcpu, a druga warstwa jest różnych rdzeni maszyn wirtualnych rozmiar rodziny takich jak seria D procesorów wirtualnych Vcpu. Zawsze, gdy nowa maszyna wirtualna jest wdrożona procesorów wirtualnych dla maszyny Wirtualnej nie może przekraczać limit przydziału procesorów wirtualnych dla rodziny rozmiaru maszyny Wirtualnej lub limitu przydziału łączna liczba procesorów wirtualnych w regionalnych. Jeśli jedno z tych limitów przydziału zostaną przekroczone, wdrażanie maszyny Wirtualnej będzie niemożliwe. Istnieje również limit przydziału dla całkowitej liczby maszyn wirtualnych w regionie. Informacji dotyczących każdej z tych limitów przydziału można zobaczyć w **użycie i przydziały** części **subskrypcji** strony w [witryny Azure portal](https://portal.azure.com), lub można wyszukać wartości za pomocą platformy Azure INTERFEJS WIERSZA POLECENIA.


## <a name="check-usage"></a>Sprawdzanie użycia

Możesz sprawdzić, używając limit przydziału użycia [użycie listy az vm](/cli/azure/vm).

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
Zarezerwowane wystąpienia maszyn wirtualnych, które są ograniczone do pojedynczej subskrypcji bez elastyczność rozmiaru maszyny Wirtualnej, zostaną dodane nowe aspekt do limity przydziału procesorów wirtualnych. Wartości te opisują liczbę wystąpień podane rozmiar, który musi być możliwy do wdrożenia w ramach subskrypcji. Działają jako symbol zastępczy w systemie limitu przydziału, aby upewnić się, że ten limit przydziału jest zarezerwowana do upewnij się, że można wdrożyć w ramach subskrypcji platformy Azure rezerwacji. Na przykład jeśli w określonej subskrypcji 10 rezerwacje maszyna wirtualna Standard_D1 limitu użycia dla rezerwacji maszyna wirtualna Standard_D1 wyniosłaby 10. Spowoduje to platformy Azure w celu zapewnienia, że zawsze są dostępne w całkowitej regionalnego przydziału procesorów wirtualnych do użycia dla wystąpień maszyna wirtualna Standard_D1 co najmniej 10 procesorów wirtualnych Vcpu i dostępne do limitu przydziału procesorów wirtualnych rodzina Standard D ma być używany dla wystąpień maszyna wirtualna Standard_D1 są co najmniej 10 procesorów wirtualnych.

Jeśli zwiększenia limitu przydziału jest wymagana do albo zakupu wystąpień Zarezerwowanych na pojedynczej subskrypcji, możesz to zrobić [zażądać zwiększenia limitu przydziału](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) w ramach Twojej subskrypcji.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat rozliczeń i przydziałów, zobacz [subskrypcji platformy Azure i limity, przydziały i ograniczenia](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
