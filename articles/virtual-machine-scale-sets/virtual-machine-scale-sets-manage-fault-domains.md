---
title: Zarządzanie domenami błędów w zestawach skalowania maszyny wirtualnej platformy Azure
description: Dowiedz się, jak wybrać odpowiednią liczbę dysków FD podczas tworzenia zestawu skalowania maszyny wirtualnej.
author: rajsqr
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: drewm
ms.openlocfilehash: 297837354cea4bb5ccdcc03261810dcffd144243
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275726"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>Choosing the right number of fault domains for virtual machine scale set (Wybieranie odpowiedniej liczby domen błędów dla zestawu skalowania maszyn wirtualnych)
Zestawy skalowania maszyny wirtualnej są tworzone domyślnie z pięcioma domenami błędów w regionach platformy Azure bez stref. Dla regionów, które obsługują strefowe wdrażanie zestawów skalowania maszyny wirtualnej i ta opcja jest zaznaczona, domyślna wartość liczby domen błędów wynosi 1 dla każdej ze stref. FD = 1 w tym przypadku oznacza, że wystąpienia maszyn wirtualnych należących do zestawu skalowania będą rozłożone na wielu stojakach na podstawie najlepszego wysiłku.

Można również rozważyć wyrównanie liczby domen błędów zestawu skalowania do liczby domen błędów dysków zarządzanych. To wyrównanie może pomóc zapobiec utracie kworum, jeśli cała domena błędów dysków zarządzanych ulegnie awarii. Liczba FD można ustawić na mniejszą lub równą liczbie domen błędów dysków zarządzanych dostępnych w każdym z regionów. Zapoznaj się z tym [dokumentem,](../virtual-machines/windows/manage-availability.md) aby dowiedzieć się więcej o liczbie domen błędów dysków zarządzanych według regionów.

## <a name="rest-api"></a>Interfejs API REST
Można ustawić właściwość `properties.platformFaultDomainCount` na 1, 2 lub 3 (domyślnie 5, jeśli nie określono). Zapoznaj się z dokumentacją interfejsu REST API [tutaj](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate).

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Można ustawić parametr `--platform-fault-domain-count` na 1, 2 lub 3 (domyślnie 5, jeśli nie określono). Zapoznaj się z dokumentacją interfejsu [wiersza](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create)polecenia platformy Azure tutaj .

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

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [funkcjach dostępności i nadmiarowości](../virtual-machines/windows/availability.md) w środowiskach platformy Azure.
