---
title: Jak tagu maszyny wirtualnej systemu Linux platformy Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat znakowanie Azure maszyny wirtualnej systemu Linux utworzone na platformie Azure przy użyciu modelu wdrażania Menedżera zasobów.
services: virtual-machines-linux
documentationcenter: ''
author: mmccrory
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ca0e17e5-d78e-42e6-9dad-c1e8f1c58027
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: memccror
ms.openlocfilehash: 19e8c11a0051f9d13ef4be3d77fe828a272c3c77
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Jak tagu maszyny wirtualnej systemu Linux na platformie Azure
W tym artykule opisano różne sposoby tagu maszyny wirtualnej systemu Linux na platformie Azure za pośrednictwem modelu wdrażania usługi Resource Manager. Tagi to pary klucz wartość zdefiniowana przez użytkownika, które mogą być umieszczone bezpośrednio na zasób lub grupa zasobów. Azure obecnie obsługuje maksymalnie 15 znaczników dla zasobu i grupy zasobów. Tagi mogą dotyczącymi zasobów w czasie tworzenia lub dodać do istniejącego zasobu. Należy pamiętać, tagi są obsługiwane dla zasobów utworzone za pośrednictwem tylko modelu wdrażania Menedżera zasobów.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Znakowanie za pomocą interfejsu wiersza polecenia platformy Azure
Aby rozpocząć, należy najnowszej [Azure CLI 2.0](/cli/azure/install-azure-cli) zainstalowane i zalogowany do konta platformy Azure przy użyciu [logowania az](/cli/azure/reference-index#az-login).

Czynności te można również wykonać przy użyciu [interfejsu wiersza polecenia platformy Azure w wersji 1.0](tag-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Wszystkie właściwości można wyświetlić dla danej maszyny wirtualnej, tym tagów, za pomocą tego polecenia:

```azurecli
az vm show --resource-group MyResourceGroup --name MyTestVM
```

Aby dodać nowy znacznik maszyny Wirtualnej za pomocą wiersza polecenia platformy Azure, można użyć `azure vm update` polecenia wraz z parametrem tag **— Ustawianie**:

```azurecli
az vm update \
    --resource-group MyResourceGroup \
    --name MyTestVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Aby usunąć tagów, można użyć **— Usuń** parametru w `azure vm update` polecenia.

```azurecli
az vm update --resource-group MyResourceGroup --name MyTestVM --remove tags.myNewTagName1
```

Teraz, gdy firma Microsoft zastosowano tagi naszych zasobów Azure CLI i portalu, Spójrzmy na szczegóły użycia, aby zobaczyć tagów w portalu rozliczeń.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Kolejne kroki
* Aby dowiedzieć się więcej na temat znakowanie zasobów platformy Azure, zobacz [Omówienie usługi Azure Resource Manager] [ Azure Resource Manager Overview] i [przy użyciu tagów do organizowania zasobów platformy Azure][Using Tags to organize your Azure Resources].
* Aby dowiedzieć się, jak tagów można zarządzać korzystanie z zasobów platformy Azure, zobacz [Opis rachunku Azure] [ Understanding your Azure Bill] i [uzyskać wgląd w Microsoft Azure użycia zasobów][Gain insights into your Microsoft Azure resource consumption].

[Azure CLI environment]: ../../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
