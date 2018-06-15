---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — komunikacja równorzędna dwóch sieci wirtualnych | Microsoft Docs
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — komunikacja równorzędna dwóch sieci wirtualnych.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: feab9f518076938ed20396319ceb1d5badb9eb8f
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
ms.locfileid: "30841388"
---
# <a name="peer-two-virtual-networks-script-sample"></a>Przykładowy skrypt tworzący komunikację równorzędną dwóch sieci wirtualnych

Ten przykładowy skrypt tworzy i łączy dwie sieci wirtualne w tym samym regionie za pośrednictwem sieci platformy Azure. Po uruchomieniu skryptu powstanie komunikacja równorzędna między dwiema sieciami wirtualnymi.

Skrypt można wykonać z poziomu usługi Azure [Cloud Shell](https://shell.azure.com/bash) lub lokalnej instalacji wiersza polecenia platformy Azure. Jeśli używasz interfejsu wiersza polecenia lokalnie, ten skrypt wymaga uruchomienia wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja została zainstalowana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia lokalnie, musisz też uruchomić polecenie `az login`, aby utworzyć połączenie z platformą Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.sh "Peer two networks")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby:

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do tworzenia grupy zasobów, maszyny wirtualnej i wszystkich powiązanych zasobów. Każde polecenie w poniższej tabeli stanowi link do dokumentacji polecenia:

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Tworzy sieć wirtualną i podsieć platformy Azure. |
| [az network vnet peering create](/cli/azure/network/vnet/peering#az_network_vnet_peering_create) | Tworzy komunikację równorzędną między dwiema sieciami wirtualnymi.  |
| [az group delete](/cli/azure/vm/extension#az_vm_extension_set) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla sieci wirtualnej można znaleźć w temacie [Virtual network CLI samples (Przykładowe skrypty interfejsu wiersza polecenia dla sieci wirtualnej)](../cli-samples.md).
