---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie dwóch maszyn wirtualnych przy użyciu wewnętrznej i zewnętrznej sieciowej grupy zabezpieczeń | Microsoft Docs
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie dwóch maszyn wirtualnych przy użyciu wewnętrznej i zewnętrznej sieciowej grupy zabezpieczeń
services: virtual-machines-windows
documentationcenter: virtual-machines
author: rickstercdn
manager: jeconnoc
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
ms.custom: mvc
ms.openlocfilehash: 7a43bd6a7f64278553c3e1d83a7ef9d57831f998
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654321"
---
# <a name="secure-network-traffic-between-virtual-machines"></a>Bezpieczny ruch sieciowy między maszynami wirtualnymi

Ten skrypt tworzy dwie maszyny wirtualne i zabezpiecza ruch przychodzący do obydwu. Pierwsza maszyna wirtualna jest dostępna w Internecie i ma sieciową grupę zabezpieczeń skonfigurowaną tak, aby zezwolić na ruch w portach 3389 i 80. Pierwsza maszyna wirtualna nie jest dostępna w Internecie i ma sieciową grupę zabezpieczeń skonfigurowaną tak, aby zezwolić tylko na ruch z pierwszej maszyny wirtualnej. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nsg/create-windows-vm-nsg.sh "Create VM with NSG")]

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
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Tworzy podsieć. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Tworzy maszynę wirtualną i łączy ją z kartą sieciową, siecią wirtualną, podsiecią i sieciową grupą zabezpieczeń. To polecenie określa również obraz maszyny wirtualnej do użycia oraz poświadczenia administracyjne.  |
| [az network nsg rule update](https://docs.microsoft.com/cli/azure/network/nsg/rule#az_network_nsg_rule_update) | Aktualizuje regułę sieciowej grupy zabezpieczeń. W tym przykładzie reguła zaplecza jest aktualizowana w celu przekazywania ruchu tylko z podsieci frontonu. |
| [az network nsg rule list](https://docs.microsoft.com/cli/azure/network/nsg/rule#az_network_nsg_rule_list) | Zwraca informacje dotyczące reguły sieciowej grupy zabezpieczeń. W tym przykładzie nazwa reguły jest przechowywana w zmiennej na potrzeby późniejszego użycia w skrypcie. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Windows](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
