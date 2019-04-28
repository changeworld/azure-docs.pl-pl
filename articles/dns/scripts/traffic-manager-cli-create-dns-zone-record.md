---
title: Przykład interfejsu wiersza polecenia — tworzenie strefy DNS i rekordu dla nazwy domeny — Azure | Microsoft Docs
description: Ten przykładowy skrypt wiersza polecenia platformy Azure przedstawia sposób tworzenia strefy DNS i rekordu dla nazwy domeny
services: load-balancer
documentationcenter: traffic-manager
author: vhorne
manager: jeconnoc
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/30/2018
ms.author: victorh
ms.openlocfilehash: 59ec8d4f93b18469818c9ead2e965679e41360ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61059488"
---
# <a name="azure-cli-script-example-create-a-dns-zone-and-record"></a>Przykład skryptu interfejsu wiersza polecenia platformy Azure: Tworzenie rekordu i strefy DNS

Ten przykładowy skrypt wiersza polecenia platformy Azure umożliwia utworzenie strefy DNS i rekordu dla nazwy domeny. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

```azurecli-interactive

# Create a resource group.
az group create \
  -n myResourceGroup \
  -l eastus

# Create a DNS zone. Substitute zone name "contoso.com" with the values for your own.

az network dns zone create \
  -g MyResourceGroup \
  -n contoso.com

# Create a DNS record. Substitute zone name "contoso.com" and IP address "1.2.3.4* with the values for your own.

az network dns record-set a add-record \
  -g MyResourceGroup \
  -z contoso.com \
  -n www \
  -a 1.2.3.4

# Get a list the DNS records in your zone
az network dns record-set list \
  -g MyResourceGroup \ 
  -z contoso.com
```

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, strefę DNS i wszystkie powiązane zasoby.

```azurecli
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do tworzenia grupy zasobów, maszyny wirtualnej, zestawu dostępności, modułu równoważenia obciążenia i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az network dns zone create](/cli/azure/network/dns/zone#az-network-dns-zone-create) | Tworzy strefę DNS na platformie Azure. |
| [az network dns record-set a add-record](/cli/azure/network/dns/record-set) | Dodaje rekord *A* do strefy DNS. |
| [az network dns record-set list](/cli/azure/network/dns/record-set) | Zwraca listę wszystkich zestawów rekordów *A* w strefie DNS. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

