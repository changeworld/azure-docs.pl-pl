---
title: Przykład interfejsu wiersza polecenia — równoważenie obciążenia ruchu do maszyn wirtualnych na potrzeby wysokiej dostępności — Azure | Microsoft Docs
description: Ten przykładowy skrypt wiersza polecenia platformy Azure przedstawia sposób równoważenia obciążenia ruchu do maszyn wirtualnych na potrzeby wysokiej dostępności
services: load-balancer
documentationcenter: load-balancer
author: KumudD
manager: jeconnoc
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: kumud
ms.openlocfilehash: 218308f1b025c8f43b9d19c4b7fe5d0330d3c7f4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32181205"
---
# <a name="azure-cli-script-example-load-balance-traffic-to-vms-for-high-availability"></a>Przykładowy skrypt wiersza polecenia platformy Azure: równoważenie obciążenia ruchu do maszyn wirtualnych na potrzeby wysokiej dostępności

Ten przykładowy skrypt interfejsu wiersza polecenia platformy Azure umożliwia utworzenie wszystkich elementów potrzebnych do uruchomienia kilku maszyn wirtualnych z systemem Ubuntu skonfigurowanych w ramach konfiguracji o wysokiej dostępności i zrównoważonym obciążeniu. Po uruchomieniu skryptu będziesz mieć trzy maszyny wirtualne dołączone do zestawu dostępności platformy Azure i dostępne za pośrednictwem usługi Azure Load Balancer. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do tworzenia grupy zasobów, maszyny wirtualnej, zestawu dostępności, modułu równoważenia obciążenia i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#az_network_vnet_create) | Tworzy sieć wirtualną i podsieć platformy Azure. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#az_network_public_ip_create) | Tworzy publiczny adres IP przy użyciu statycznego adresu IP i skojarzonej nazwy DNS. |
| [az network lb create](https://docs.microsoft.com/cli/azure/network/lb#az_network_lb_create) | Tworzy moduł równoważenia obciążenia platformy Azure. |
| [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe#az_network_lb_probe_create) | Tworzy sondę modułu równoważenia obciążenia. Sonda modułu równoważenia obciążenia służy do monitorowania każdej maszyny wirtualnej w zestawie modułu równoważenia obciążenia. Jeśli maszyna wirtualna stanie się niedostępna, ruch nie będzie do niej kierowany. |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#az_network_lb_rule_create) | Tworzy regułę modułu równoważenia obciążenia. W tym przykładzie tworzona jest reguła dla portu 80. Ruch HTTP docierający do modułu równoważenia obciążenia jest kierowany do portu 80 jednej z maszyn wirtualnych w zestawie modułu równoważenia obciążenia. |
| [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule#az_network_lb_inbound_nat_rule_create) | Tworzy regułę translatora adresów sieciowych modułu równoważenia obciążenia.  Reguły translatora adresów sieciowych mapują port modułu równoważenia obciążenia na port na maszynie wirtualnej. W tym przykładzie tworzona jest reguła translatora adresów sieciowych dla ruchu SSH do każdej maszyny wirtualnej w zestawie modułu równoważenia obciążenia.  |
| [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg#az_network_nsg_create) | Tworzy sieciową grupę zabezpieczeń, czyli granicę zabezpieczeń między Internetem i maszyną wirtualną. |
| [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule#az_network_nsg_rule_create) | Tworzy regułę sieciowej grupy zabezpieczeń zezwalającą na ruch przychodzący. W tym przykładzie port 22 jest otwierany dla ruchu protokołu SSH. |
| [az network nic create](https://docs.microsoft.com/cli/azure/network/nic#az_network_nic_create) | Tworzy wirtualną kartę sieciową i dołącza ją do sieci wirtualnej, podsieci i sieciowej grupy zabezpieczeń. |
| [az vm availability-set create](https://docs.microsoft.com/cli/azure/network/lb/rule#az_network_lb_rule_create) | Tworzy zestaw dostępności. Zestawy dostępności zapewniają działanie aplikacji bez przestojów dzięki rozmieszczeniu maszyn wirtualnych w ramach zasobów fizycznych tak, aby niepowodzenie nie wpływało na cały zestaw. |
| [az vm create](/cli/azure/vm#az_vm_create) | Tworzy maszynę wirtualną i łączy ją z kartą sieciową, siecią wirtualną, podsiecią i sieciową grupą zabezpieczeń. To polecenie określa również obraz maszyny wirtualnej do użycia oraz poświadczenia administracyjne.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia platformy Azure dla sieci można znaleźć w [dokumentacji sieci platformy Azure](../cli-samples.md).