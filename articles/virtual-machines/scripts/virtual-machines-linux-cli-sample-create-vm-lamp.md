---
title: Przykładowy skrypt wiersza polecenia platformy Azure — wdrażanie stosu LAMP w zestawie skalowania maszyn wirtualnych o zrównoważonym obciążeniu | Microsoft Docs
description: Użyj rozszerzenia skryptu niestandardowego w celu wdrożenia stosu LAMP w zestawie skalowania maszyn wirtualnych ze zrównoważonym obciążeniem na platformie Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/05/2017
ms.author: allclark
ms.custom: mvc
ms.openlocfilehash: cb8815db5e16603ca4578f6d3d943cbef62e49af
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54461149"
---
# <a name="deploy-the-lamp-stack-in-a-load-balanced-virtual-machine-scale-set"></a>Wdrażanie stosu LAMP w zestawie skalowania maszyn wirtualnych ze zrównoważonym obciążeniem

Ten przykład tworzy zestaw skalowania maszyn wirtualnych i stosuje rozszerzenie, które uruchamia niestandardowy skrypt w celu wdrożenia stosu LAMP na każdej maszynie wirtualnej w zestawie skalowania.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/build-stack.sh "Create virtual machine scale set with LAMP stack")]

## <a name="connect"></a>Połączenie

Ten kod umożliwia sprawdzenie sposobu połączenia maszyn wirtualnych i zestawu skalowania.

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/how-to-access.sh "Access the virtual machine scale set")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, zestaw skalowania i maszyny wirtualne oraz wszystkie powiązane zasoby.

```azurecli-interactive 
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do tworzenia grupy zasobów, maszyny wirtualnej, zestawu dostępności, modułu równoważenia obciążenia i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az vmss create](https://docs.microsoft.com/cli/azure/vmss#az_vmss_create) | Tworzy zestaw skalowania maszyn wirtualnych. |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#az_network_lb_rule_create) | Dodaje zestaw punktów końcowych ze zrównoważonym obciążeniem. |
| [az vmss extension set](https://docs.microsoft.com/cli/azure/vmss/extension#az_vmss_extension_set) | Tworzy rozszerzenie, które uruchamia skrypt niestandardowy w obrębie wdrożenia maszyny wirtualnej. |
| [az vmss update-instances](https://docs.microsoft.com/cli/azure/vmss#az_vmss_update_instances) | Uruchamia skrypt niestandardowy w obrębie wdrożonych wystąpień maszyny wirtualnej przed zastosowaniem rozszerzenia w zestawie skalowania. |
| [az vmss scale](https://docs.microsoft.com/cli/azure/vmss#az_vmss_scale) | Skaluje zestaw skalowania w górę, dodając kolejne wystąpienia maszyn wirtualnych. Podczas ich wdrażania w ich obrębie jest uruchamiany skrypt niestandardowy. |
| [az network public-ip list](https://docs.microsoft.com/cli/azure/network/public-ip) | Pobiera adresy IP maszyn wirtualnych utworzonych w ramach przykładu. |
| [az network lb show](https://docs.microsoft.com/cli/azure/network/lb#az_network_lb_show) | Pobiera porty frontonu i zaplecza używane przez moduł równoważenia obciążenia. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Linux](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
