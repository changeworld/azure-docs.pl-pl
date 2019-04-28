---
title: Jak oznaczyć maszynę wirtualną z systemem Linux platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o znakowaniu maszynę wirtualną z systemem Linux platformy Azure utworzone na platformie Azure przy użyciu modelu wdrażania usługi Resource Manager.
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
ms.openlocfilehash: 297d3bc201b4bc9d9db0b0bed7a364769fa72859
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62115978"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Jak oznaczyć maszynę wirtualną z systemem Linux na platformie Azure
W tym artykule opisano różne sposoby, aby oznaczyć maszynę wirtualną systemu Linux na platformie Azure za pomocą modelu wdrażania usługi Resource Manager. Tagi to pary klucz/wartość zdefiniowanych przez użytkownika, które mogą być umieszczone bezpośrednio na zasób lub grupa zasobów. Platforma Azure obsługuje obecnie maksymalnie 15 tagów na zasób i grupy zasobów. Znaczniki może być umieszczone na zasób w czasie tworzenia lub dodawane do istniejącego zasobu. Należy pamiętać, znaczniki są obsługiwane w przypadku zasobów utworzonych za pomocą tylko model wdrażania usługi Resource Manager.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Tagowanie z wiersza polecenia platformy Azure

Aby rozpocząć, potrzebujesz najnowszej [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) zainstalowane i zalogować się do konta platformy Azure przy użyciu [az login](/cli/azure/reference-index#az-login).

Możesz wyświetlić wszystkie właściwości, dla danej maszyny wirtualnej, włączając tagi, za pomocą tego polecenia:

```azurecli
az vm show --resource-group MyResourceGroup --name MyTestVM
```

Aby dodać nowy tag maszyny Wirtualnej za pomocą wiersza polecenia platformy Azure, można użyć `azure vm update` polecenia wraz z parametrem tag **— ustaw**:

```azurecli
az vm update \
    --resource-group MyResourceGroup \
    --name MyTestVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Aby usunąć tagi, można użyć **— Usuń** parametru w `azure vm update` polecenia.

```azurecli
az vm update --resource-group MyResourceGroup --name MyTestVM --remove tags.myNewTagName1
```

Teraz, gdy znaczniki zostały zastosowane na nasze zaawansowane zasoby wiersza polecenia platformy Azure i portalem, Spójrzmy na szczegóły użycia, aby wyświetlić tagi w portalu rozliczeń.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Kolejne kroki
* Aby dowiedzieć się więcej na temat tagowania zasobów platformy Azure, zobacz [Omówienie usługi Azure Resource Manager] [ Azure Resource Manager Overview] i [za pomocą tagów do organizowania zasobów platformy Azure] [ Using Tags to organize your Azure Resources].
* Aby dowiedzieć się, jak tagi zarządzanie może ułatwić korzystanie z zasobów platformy Azure, zobacz [informacje o rachunku Azure] [ Understanding your Azure Bill] i [wgląd w użycie zasobów usługi Microsoft Azure] [Gain insights into your Microsoft Azure resource consumption].

[Azure CLI environment]: ../../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
