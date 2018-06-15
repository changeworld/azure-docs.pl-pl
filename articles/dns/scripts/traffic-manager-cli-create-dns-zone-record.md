---
title: Przykład interfejsu wiersza polecenia — tworzenie strefy DNS i rekordu dla nazwy domeny — Azure | Microsoft Docs
description: Ten przykładowy skrypt wiersza polecenia platformy Azure przedstawia sposób tworzenia strefy DNS i rekordu dla nazwy domeny
services: load-balancer
documentationcenter: traffic-manager
author: KumudD
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
ms.author: kumud
ms.openlocfilehash: 3f9f272f6ae79ad39ba9d9ad09775f3082774a0c
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
ms.locfileid: "32773163"
---
# <a name="azure-cli-script-example-create-a-dns-zone-and-record"></a>Przykładowy skrypt interfejsu wiersza polecenia platformy Azure: tworzenie rekordu i strefy DNS

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
| [az group create](/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az network dns zone create](/cli/azure/network/dns/zone#az-network-dns-zone-create) | Tworzy strefę DNS na platformie Azure. |
| [az network dns record-set a add-record](/cli/azure/network/dns/record-set#az-network-dns-record-set-a-add-record) | Dodaje rekord *A* do strefy DNS. |
| [az network dns record-set list](/cli/azure/network/dns/record-set#az-network-dns-record-set-a-list) | Zwraca listę wszystkich zestawów rekordów *A* w strefie DNS. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

