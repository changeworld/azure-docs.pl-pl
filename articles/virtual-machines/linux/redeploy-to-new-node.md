---
title: Ponowne wdrażanie maszyn wirtualnych systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Jak ponownie wdrożyć maszyny wirtualne systemu Linux na platformie Azure, aby uniknąć problemów z połączeniem SSH.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2017
ms.author: cynthn
ms.openlocfilehash: d6a98e49df96f2427d37c1d401e8c5207bc2268e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993820"
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Ponowne wdrażanie maszyny wirtualnej systemu Linux w nowym węźle platformy Azure
Jeśli napotkasz problemy, rozwiązywanie problemów z protokołu SSH lub dostęp do aplikacji na maszynę wirtualną systemu Linux (VM) na platformie Azure, ponowne wdrożenie maszyny Wirtualnej może pomóc. Podczas ponownego wdrażania maszyny Wirtualnej powoduje przeniesienie maszyny Wirtualnej w nowym węźle w ramach infrastruktury platformy Azure i obsługuje go ponownie. Opcje konfiguracji i powiązane zasoby są zachowywane. W tym artykule pokazano, jak przeprowadzić ponowne wdrożenie maszyny Wirtualnej przy użyciu wiersza polecenia platformy Azure lub w portalu Azure.

> [!NOTE]
> Po przeprowadzić ponowne wdrożenie maszyny Wirtualnej, dysk tymczasowy to dysk utracone i dynamiczne adresy IP skojarzoną z interfejsem sieci wirtualnej zostały zaktualizowane. 

Można wdrożyć ponownie Maszynę wirtualną przy użyciu jednej z następujących opcji. Należy wybrać jedną opcję, aby przeprowadzić ponowne wdrożenie maszyny Wirtualnej:

- [Interfejs wiersza polecenia platformy Azure](#azure-cli-20)
- [Klasyczny interfejs wiersza polecenia Azure](#azure-cli-10)
- [Azure Portal](#using-azure-portal)

## <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure
Zainstaluj najnowszą wersję [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) i zaloguj się do subskrypcji platformy Azure konta przy użyciu [az login](/cli/azure/reference-index#az_login).

Ponowne wdrażanie maszyny Wirtualnej za pomocą [ponowne wdrażanie maszyny wirtualnej az](/cli/azure/vm#az_vm_redeploy). Poniższy przykład ponownie wdraża maszynę Wirtualną o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup*:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-classic-cli"></a>Użyj klasycznego wiersza polecenia platformy Azure
Zainstaluj [najnowsze klasycznego wiersza polecenia platformy Azure](../../cli-install-nodejs.md) i zaloguj się do konta platformy Azure. Upewnij się, że jesteś w trybie usługi Resource Manager (`azure config mode arm`).

Poniższy przykład ponownie wdraża maszynę Wirtualną o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup*:

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Kolejne kroki
Jeśli występują problemy z nawiązywaniem połączenia z maszyną wirtualną, możesz znaleźć pomoc na [Rozwiązywanie problemów z połączeniami SSH](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [szczegółowe kroki rozwiązywania problemów SSH](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Jeśli nie masz dostępu aplikacji uruchomionej na maszynie Wirtualnej, można również przeczytać [aplikacji Rozwiązywanie problemów z](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

