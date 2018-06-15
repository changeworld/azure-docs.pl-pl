---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie maszyny wirtualnej z systemem Windows Server 2016 z usługami IIS przy użyciu konfiguracji DSC | Microsoft Docs
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie maszyny wirtualnej z systemem Windows Server 2016 z usługami IIS przy użyciu konfiguracji DSC
services: virtual-machines-windows
documentationcenter: virtual-machines
author: rickstercdn
manager: jeconnoc
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-machines-Windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
ms.custom: mvc
ms.openlocfilehash: bb3f10c44ddf8ded35f4d80b599cda7d44226652
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654661"
---
# <a name="create-a-vm-with-iis-using-dsc"></a>Tworzenie maszyny wirtualnej z usługami IIS przy użyciu konfiguracji DSC

Ten skrypt tworzy maszynę wirtualną, a następnie używa rozszerzenia niestandardowego skryptu konfiguracji DSC maszyny wirtualnej platformy Azure do zainstalowania i skonfigurowania usług IIS. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-windows-iis-using-dsc/create-windows-iis-using-dsc.sh "Quick Create VM")]

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
| [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Tworzy maszynę wirtualną i łączy ją z kartą sieciową, siecią wirtualną, podsiecią i sieciową grupą zabezpieczeń. To polecenie określa również obraz maszyny wirtualnej do użycia oraz poświadczenia administracyjne.  |
| [az vm extension set](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Dodaje do maszyny wirtualnej rozszerzenie niestandardowego skryptu, które wywołuje skrypt instalacji usług IIS. |
| [az vm open-port](https://docs.microsoft.com/cli/azure/vm#az_vm_open_port) | Tworzy regułę sieciowej grupy zabezpieczeń, aby zezwalać na ruch przychodzący. W tym przykładzie port 80 jest otwarty dla ruchu HTTP. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Windows](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
