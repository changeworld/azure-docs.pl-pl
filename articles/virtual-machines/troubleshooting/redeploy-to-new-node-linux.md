---
title: Ponowne wdrożenie maszyn wirtualnych systemu Linux na platformie Azure | Dokumenty firmy Microsoft
description: Jak ponownie wdrożyć maszyny wirtualne systemu Linux na platformie Azure, aby ograniczyć problemy z połączeniem SSH.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: genlin
manager: dcscontentpm
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 18e96f9463176b0fce04252492eea6dbede416c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531111"
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Ponowne wdrażanie maszyny wirtualnej z systemem Linux w nowym węźle platformy Azure
Jeśli napotkasz trudności z rozwiązywaniem problemów z dostępem SSH lub aplikacji do maszyny wirtualnej systemu Linux (VM) na platformie Azure, ponowne wdrożenie maszyny wirtualnej może pomóc. Po ponowne wdrożenie maszyny Wirtualnej, przenosi maszynę wirtualną do nowego węzła w infrastrukturze platformy Azure, a następnie zasila ją ponownie. Wszystkie opcje konfiguracji i skojarzone zasoby są zachowywane. W tym artykule pokazano, jak ponownie wdrożyć maszynę wirtualną przy użyciu interfejsu wiersza polecenia platformy Azure lub witryny Azure portal.

> [!NOTE]
> Po ponownej wydechu maszyny Wirtualnej dysk tymczasowy zostanie utracony i zostaną zaktualizowane dynamiczne adresy IP skojarzone z interfejsem sieci wirtualnej. 


## <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure
Zainstaluj najnowszą [platformę Cli platformy Azure](/cli/azure/install-az-cli2) i zaloguj się do swojego konta platformy Azure przy użyciu [logowania az.](/cli/azure/reference-index)

Ponownie wdrożyć maszynę [wirtualną z az vm redeploy](/cli/azure/vm). W poniższym przykładzie ponownie wcieli się maszyna wirtualna o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup:*

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-classic-cli"></a>Korzystanie z klasycznego interfejsu wiersza polecenia platformy Azure

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


Zainstaluj [najnowszą klasyczną interfejs wiersza polecenia platformy Azure](../../cli-install-nodejs.md) i zaloguj się do swojego konta platformy Azure. Upewnij się, że jesteś w`azure config mode arm`trybie Menedżera zasobów ( ).

W poniższym przykładzie ponownie wcieli się maszyna wirtualna o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup:*

```console
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Następne kroki
Jeśli masz problemy z połączeniem się z maszyną wirtualną, możesz znaleźć konkretną pomoc dotyczącą [rozwiązywania problemów z połączeniami SSH](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [szczegółowe kroki rozwiązywania problemów z SSH](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Jeśli nie można uzyskać dostępu do aplikacji uruchomionej na maszynie wirtualnej, można również przeczytać [problemy z rozwiązywaniem problemów z aplikacjami](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


