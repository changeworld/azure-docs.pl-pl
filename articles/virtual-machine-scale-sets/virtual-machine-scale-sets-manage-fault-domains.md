---
title: Zarządzanie domenami błędów w zestawach skalowania maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wybrać odpowiednią liczbę błędów, gdy tworzenie zestawu skalowania maszyn wirtualnych.
services: virtual-machine-scale-sets
documentationcenter: ''
author: rajsqr
manager: drewm
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2018
ms.author: rajraj
ms.openlocfilehash: bab264769576b6e5478236c452d7de920d887c1a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60617983"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>Wybieranie odpowiedniej liczby domen błędów dla zestawu skalowania maszyn wirtualnych
Zestawy skalowania maszyn wirtualnych są tworzone z pięcioma domenami błędów domyślnie w regionach bez stref. Dla regionów, które obsługują strefowych wdrażania zestawów skalowania maszyn wirtualnych wartość domyślna liczba domen błędów jest 1 dla każdej ze stref. FD = 1 w takim przypadku oznacza, że wystąpień maszyn wirtualnych należących do zestawu skalowania będzie rozkłada wieloma stojakami na optymalne rozwiązanie.

Możesz też rozważyć wyrównywanie liczba domen błędów zestawu skalowania przy użyciu liczba domen błędów dysków zarządzanych. Wyrównanie takie może zapobiec utrata kworum jeśli ulegnie awarii całej domeny błędów dysków zarządzanych. Liczba FD można ustawić na wartość mniejszą niż lub równa liczbie dysków Managed Disks domen błędów jest dostępny we wszystkich regionach. Zapoznaj się z tym [dokumentu](../virtual-machines/windows/manage-availability.md) Aby dowiedzieć się więcej o liczba domen błędów dysków zarządzanych, uporządkowane według regionów.

## <a name="rest-api"></a>Interfejs API REST
Można ustawić właściwości `properties.platformFaultDomainCount` do 1, 2 lub 3 (domyślnie 5, jeśli nie określono). Zapoznaj się z dokumentacją interfejsu API REST [tutaj](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate).

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Można ustawić parametru `--platform-fault-domain-count` do 1, 2 lub 3 (domyślnie 5, jeśli nie określono). Zapoznaj się z dokumentacją wiersza polecenia platformy Azure [tutaj](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create).

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --platform-fault-domain-count 3\
  --generate-ssh-keys
```

Utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania i maszyn wirtualnych trwa kilka minut.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [funkcji dostępności i nadmiarowości](../virtual-machines/windows/regions-and-availability.md) środowisk platformy Azure.
