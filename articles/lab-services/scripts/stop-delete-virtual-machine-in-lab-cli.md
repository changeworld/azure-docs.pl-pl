---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — zatrzymywanie i usuwanie maszyny wirtualnej w laboratorium | Microsoft Docs
description: Ten skrypt interfejsu wiersza polecenia platformy Azure umożliwia zatrzymanie i usunięcie maszyny wirtualnej w laboratorium.
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
ms.openlocfilehash: c89a5c069186a1612161f660d985ff9144c68e64
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61084297"
---
# <a name="use-azure-cli-to-stop-and-delete-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Zatrzymywanie i usuwanie maszyny wirtualnej w laboratorium za pomocą interfejsu wiersza polecenia platformy Azure w usłudze Azure DevTest Labs

Ten skrypt interfejsu wiersza polecenia platformy Azure umożliwia zatrzymanie i usunięcie maszyny wirtualnej w laboratorium. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/stop-delete-virtual-machine-in-lab/stop-delete-virtual-machine-in-lab.sh "Stop and delete a VM in a lab")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń:

| Polecenie | Uwagi |
|---|---|
| [az lab vm stop](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-stop) | Zatrzymuje maszynę wirtualną w laboratorium. Wykonanie tej operacji może chwilę potrwać. |
| [az lab vm delete](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-delete) | Usuwa maszynę wirtualną w laboratorium. Wykonanie tej operacji może chwilę potrwać. |


## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia usługi Azure Lab Services można znaleźć w artykule [Przykłady interfejsu wiersza polecenia usługi Azure Lab Services](../samples-cli.md).
