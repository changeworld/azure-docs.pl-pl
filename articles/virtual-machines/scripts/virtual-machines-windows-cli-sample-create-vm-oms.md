---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie maszyny wirtualnej z systemem Windows Server 2016 z funkcją monitorowania pakietu OMS | Microsoft Docs
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie maszyny wirtualnej z systemem Windows Server 2016 z funkcją monitorowania pakietu OMS
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
ms.custom: mvc
ms.openlocfilehash: 05ecaa856cf2c513b48f0ba0f170dfb3bbf31e75
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658295"
---
# <a name="monitor-a-vm-with-operations-management-suite"></a>Monitorowanie maszyny wirtualnej przy użyciu pakietu Operations Management Suite

Ten skrypt tworzy maszynę wirtualną platformy Azure, instaluje agenta pakietu Operations Management Suite (OMS) i rejestruje system przy użyciu obszaru roboczego pakietu OMS. Po uruchomieniu skryptu maszyna wirtualna będzie widoczna w konsoli OMS.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-monitor-oms/create-windows-vm-monitor-oms.sh "Quick Create VM")]

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
| [azure vm extension set](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Uruchamia rozszerzenie maszyny wirtualnej względem maszyny wirtualnej. W takim przypadku rozszerzenie agenta pakietu Operations Management Suite jest używane do instalowania agenta pakietu OMS i rejestrowania maszyny wirtualnej w obszarze roboczym pakietu OMS. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Windows](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
