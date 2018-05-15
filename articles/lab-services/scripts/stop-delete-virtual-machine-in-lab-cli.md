---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — zatrzymywanie i usuwanie maszyny wirtualnej w laboratorium niestandardowym | Microsoft Docs
description: Ten skrypt interfejsu wiersza polecenia platformy Azure umożliwia zatrzymanie i usunięcie maszyny wirtualnej w laboratorium niestandardowym.
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
ms.openlocfilehash: 0da99bb5e029f4d1c6be74d09ad11c6017121a7c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="use-azure-cli-to-stop-and-delete-a-virtual-machine-in-a-custom-lab"></a>Zatrzymywanie i usuwanie maszyny wirtualnej w laboratorium niestandardowym za pomocą interfejsu wiersza polecenia platformy Azure

Ten skrypt interfejsu wiersza polecenia platformy Azure umożliwia zatrzymanie i usunięcie maszyny wirtualnej w laboratorium niestandardowym. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/stop-delete-virtual-machine-in-lab/stop-delete-virtual-machine-in-lab.sh "Stop and delete a VM in a custom lab")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń:

| Polecenie | Uwagi |
|---|---|
| [az lab vm stop](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-stop) | Zatrzymuje maszynę wirtualną w laboratorium niestandardowym. Wykonanie tej operacji może chwilę potrwać. |
| [az lab vm delete](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-delete) | Usuwa maszynę wirtualną w laboratorium niestandardowym. Wykonanie tej operacji może chwilę potrwać. |


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Dodatkowe przykłady skryptów programu PowerShell usługi Azure Lab Services można znaleźć w artykule [Przykłady interfejsu wiersza polecenia usługi Azure Lab Services](../samples-cli.md).