---
title: Należy ponownie wdrożyć maszyn wirtualnych systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Jak ponownie wdrożyć maszyn wirtualnych systemu Linux na platformie Azure, aby ograniczyć problemy z połączeniem SSH.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2017
ms.author: iainfou
ms.openlocfilehash: 484e23f7f5800faf4a1d83c2386e0c766c3a1f2d
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
ms.locfileid: "29849311"
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Wdrożenie maszyny wirtualnej systemu Linux do nowego węzła Azure
Jeśli pokonywaniu trudności Rozwiązywanie problemów z SSH lub aplikacji dostęp do maszyny wirtualnej systemu Linux (VM) na platformie Azure, ponownego wdrażania maszyny Wirtualnej może pomóc. Podczas ponownego wdrażania maszyny Wirtualnej maszyny Wirtualnej są przenoszone do nowego węzła w ramach infrastruktury platformy Azure i uprawnień go ponownie. Opcje konfiguracji i skojarzonych zasobów są zachowywane. W tym artykule przedstawiono sposób ponownego wdrażania maszyny Wirtualnej przy użyciu wiersza polecenia platformy Azure lub w portalu Azure.

> [!NOTE]
> Po ponownego wdrażania maszyny Wirtualnej, tymczasowego dysku zostaną utracone i dynamicznych adresów IP skojarzonych z interfejsu sieci wirtualnej zostały zaktualizowane. 

Można wdrożyć ponownie Maszynę wirtualną przy użyciu jednej z następujących opcji. Należy wybrać jedną opcję, aby wdrożyć ponownie maszyny Wirtualnej:

- [Interfejs wiersza polecenia platformy Azure 2.0](#azure-cli-20)
- [Interfejs wiersza polecenia platformy Azure 1.0](#azure-cli-10)
- [Azure Portal](#using-azure-portal)

## <a name="use-the-azure-cli-20"></a>Użyj Azure CLI 2.0
Zainstaluj najnowszą [Azure CLI 2.0](/cli/azure/install-az-cli2) i zaloguj się do platformy Azure konta przy użyciu [logowania az](/cli/azure/reference-index#az_login).

Wdrożenie maszyny Wirtualnej z [ponownego wdrażania maszyny wirtualnej az](/cli/azure/vm#az_vm_redeploy). Poniższy przykład wdraża ponownie maszyny Wirtualnej o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup*:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-cli-10"></a>Użyj Azure CLI 1.0
Zainstaluj [najnowsze Azure CLI 1.0](../../cli-install-nodejs.md) i zaloguj się do konta platformy Azure. Upewnij się, że jesteś w trybie Menedżera zasobów (`azure config mode arm`).

Poniższy przykład wdraża ponownie maszyny Wirtualnej o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup*:

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Kolejne kroki
Jeśli występują problemy dotyczące nawiązywania połączenia z maszyną Wirtualną, możesz znaleźć pomoc w [Rozwiązywanie problemów z połączeniami SSH](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [szczegółowe kroki rozwiązywania problemów SSH](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Jeśli nie można uzyskać dostęp do aplikacji działających na maszynie Wirtualnej, możesz przeczytać [aplikacji Rozwiązywanie problemów z](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

