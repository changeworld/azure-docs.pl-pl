---
title: przydziały vCPU dla platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o vCPU przydziały dla platformy Azure.
keywords: ''
services: virtual-machines-linux
documentationcenter: ''
author: Drewm3
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: drewm
ms.openlocfilehash: a4e0bbe1c6d9b121dfb422934cdd67ff19f80482
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="virtual-machine-vcpu-quotas"></a>Przydziały vCPU maszyny wirtualnej

Przydziały vCPU dla maszyn wirtualnych i zestawy skalowania maszyny wirtualnej są rozmieszczone w dwóch warstw dla każdej subskrypcji, w każdym regionie. Pierwsza warstwa jest całkowita regionalnych Vcpu, a druga warstwa jest różnych maszyny Wirtualnej rozmiar rodziny rdzeni takich jak Vcpu standardowe rodziny D. Zawsze, gdy jest nowej maszyny Wirtualnej wdrożone Vcpu nowo wdrożonej maszyny Wirtualnej nie może przekraczać przydziału vCPU dla określonej rodziny rozmiar maszyny Wirtualnej lub przydziału całkowita vCPU regionalnych. W przypadku przekroczenia jednej z tych przydziałów wdrożenia maszyny Wirtualnej nie jest dozwolone. Istnieje również limit przydziału dla ogólnej liczby maszyn wirtualnych w tym regionie. Szczegółowe informacje o każdej z tych przydziałów są widoczne w **użycia + przydziały** sekcji **subskrypcji** strony [portalu Azure](https://portal.azure.com), lub można zbadać wartości przy użyciu wiersza polecenia platformy Azure .


## <a name="check-usage"></a>Sprawdź użycie

Można sprawdzić, używając przydział użycia [użycie listy az maszyny wirtualnej](/cli/azure/vm#az_vm_list_usage).

```azurecli-interactive
az vm list-usage --location "East US"
[
  …
  {
    "currentValue": 4,
    "limit": 260,
    "name": {
      "localizedValue": "Total Regional vCPUs",
      "value": "cores"
    }
  },
  {
    "currentValue": 4,
    "limit": 10000,
    "name": {
      "localizedValue": "Virtual Machines",
      "value": "virtualMachines"
    }
  },
  {
    "currentValue": 1,
    "limit": 2000,
    "name": {
      "localizedValue": "Virtual Machine Scale Sets",
      "value": "virtualMachineScaleSets"
    }
  },
  {
    "currentValue": 1,
    "limit": 10,
    "name": {
      "localizedValue": "Standard B Family vCPUs",
      "value": "standardBFamily"
    }
  },
```
## <a name="reserved-vm-instances"></a>Wystąpienia zarezerwowane maszyn wirtualnych
Ograniczone do jednego subskrypcji, wystąpień maszyny Wirtualnej zarezerwowana doda aspekt nowe do przydziały vCPU. Te wartości opisano liczbę wystąpień podane rozmiar, który musi być możliwy do wdrożenia w ramach subskrypcji. Funkcje te działają jako element zastępczy w systemie przydziału, aby upewnić się, że ten przydział jest zarezerwowana do upewnij się, że można wdrożyć w ramach subskrypcji są zastrzeżone wystąpień. Na przykład jeśli określonej subskrypcji ma 10 Standard_D1 zastrzeżone wystąpień ograniczenie użycia dla wystąpień zastrzeżone Standard_D1 będzie 10. Spowoduje to Azure upewnić się, że zawsze są dostępne w całkowita regionalnych Vcpu przydział do zastosowania w przypadku wystąpienia Standard_D1 Vcpu co najmniej 10 i są dostępne w standardowe rodziny D vCPU przydział do zastosowania w przypadku wystąpienia Standard_D1 Vcpu co najmniej 10.

Jeśli zwiększenia limitu przydziału jest wymagana, aby zakupić pojedynczego RI subskrypcji, możesz [zażądać zwiększenia limitu przydziału](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) w ramach subskrypcji.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji dotyczących rozliczeń i przydziały, zobacz [subskrypcji platformy Azure i usługi limity, przydziały i ograniczenia](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
