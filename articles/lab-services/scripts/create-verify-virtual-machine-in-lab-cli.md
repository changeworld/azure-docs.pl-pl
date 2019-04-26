---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure— tworzenie i weryfikowanie maszyny wirtualnej w laboratorium | Microsoft Docs
description: Ten skrypt interfejsu wiersza polecenia platformy Azure umożliwia utworzenie maszyny wirtualnej w laboratorium i zweryfikowanie jej dostępności.
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
ms.openlocfilehash: 225e62cedfea79875d56a50b4b04bdf0ad4c7892
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60427309"
---
# <a name="use-azure-cli-to-create-and-verify-availability-of-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Tworzenie maszyny wirtualnej i weryfikowanie jej dostępności w laboratorium za pomocą interfejsu wiersza polecenia platformy Azure w usłudze Azure DevTest Labs

Ten skrypt interfejsu wiersza polecenia platformy Azure umożliwia utworzenie maszyny wirtualnej w laboratorium. Maszyna wirtualna utworzona na podstawie obrazu z platformy handlowej z uwierzytelnianiem za pomocą protokołu SSH. Następnie skrypt weryfikuje, czy maszyna wirtualna jest dostępna do użycia. 

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
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az lab vm create](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-create) | Tworzy maszynę wirtualną w laboratorium. |
| [az lab vm show](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-show) | Wyświetla stan maszyny wirtualnej w laboratorium. |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia usługi Azure Lab Services można znaleźć w artykule [Przykłady interfejsu wiersza polecenia usługi Azure Lab Services](../samples-cli.md).
