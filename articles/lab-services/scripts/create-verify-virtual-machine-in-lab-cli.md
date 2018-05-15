---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure— tworzenie i weryfikowanie maszyny wirtualnej w laboratorium niestandardowym | Microsoft Docs
description: Ten skrypt interfejsu wiersza polecenia platformy Azure umożliwia utworzenie maszyny wirtualnej w laboratorium niestandardowym i zweryfikowanie jej dostępności.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2018
ms.author: spelluru
ms.custom: mvc
ms.openlocfilehash: bd564cda7b4d5c2158b8499b48b8faa68309b461
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="use-azure-cli-to-create-and-verify-availability-of-a-virtual-machine-in-a-custom-lab"></a>Tworzenie maszyny wirtualnej i weryfikowanie jej dostępności w laboratorium niestandardowym za pomocą interfejsu wiersza polecenia platformy Azure

Ten skrypt interfejsu wiersza polecenia platformy Azure umożliwia utworzenie maszyny wirtualnej w laboratorium niestandardowym. Maszyna wirtualna utworzona na podstawie obrazu z platformy handlowej z uwierzytelnianiem za pomocą protokołu SSH. Następnie skrypt weryfikuje, czy maszyna wirtualna jest dostępna do użycia. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/create-verify-virtual-machine-in-lab/create-verify-virtual-machine-in-lab.sh "Create and verify availability of a VM")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń:

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az lab vm create ](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-create) | Tworzy maszynę wirtualną w laboratorium niestandardowym. |
| [az lab vm show](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-show) | Wyświetla stan maszyny wirtualnej w laboratorium niestandardowym. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Dodatkowe przykłady skryptów programu PowerShell usługi Azure Lab Services można znaleźć w artykule [Przykłady interfejsu wiersza polecenia usługi Azure Lab Services](../samples-cli.md).