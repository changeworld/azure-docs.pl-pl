---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie maszyny wirtualnej z systemem Windows | Microsoft Docs
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie maszyny wirtualnej z systemem Windows
services: virtual-machines-Windows
documentationcenter: virtual-machines
author: rickstercdn
manager: jeconnoc
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-machines-Windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-Windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
ms.openlocfilehash: e4e098373120e8dc889ffd0319c049f2a83160ad
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2019
ms.locfileid: "54844555"
---
# <a name="create-a-virtual-machine-with-the-azure-cli"></a>Tworzenie maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure

Ten skrypt tworzy maszynę wirtualną platformy Azure z systemem Windows Server 2016. Po uruchomieniu skryptu można uzyskać dostęp do maszyny wirtualnej za pomocą połączenia pulpitu zdalnego.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-detailed/create-windows-vm-detailed.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do tworzenia grupy zasobów, maszyny wirtualnej i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#az_network_vnet_create) | Tworzy sieć wirtualną i podsieć platformy Azure. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#az_network_public_ip_create) | Tworzy publiczny adres IP przy użyciu statycznego adresu IP i skojarzonej nazwy DNS. |
| [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg#az_network_nsg_create) | Tworzy sieciową grupę zabezpieczeń, czyli granicę zabezpieczeń między Internetem i maszyną wirtualną. |
| [az network nic create](https://docs.microsoft.com/cli/azure/network/nic) | Tworzy wirtualną kartę sieciową i dołącza ją do sieci wirtualnej, podsieci i sieciowej grupy zabezpieczeń. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Tworzy maszynę wirtualną i łączy ją z kartą sieciową, siecią wirtualną, podsiecią i sieciową grupą zabezpieczeń. To polecenie określa również obraz maszyny wirtualnej do użycia oraz poświadczenia administracyjne.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Windows](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
