---
title: Równoważenie obciążenia Azure CLI przykładowym skrypcie — wiele witryn sieci Web z wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Równoważenie obciążenia Azure CLI przykładowym skrypcie — wiele witryn sieci Web do tej samej maszyny wirtualnej
services: load-balancer
documentationcenter: load-balancer
author: KumudD
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: kumud
ms.openlocfilehash: 72a780f1870d05a714aaeec879004b6f06d8bb7f
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
ms.locfileid: "29849141"
---
# <a name="load-balance-multiple-websites"></a>Równoważenie obciążenia wielu witryn sieci Web

Ten przykładowy skrypt tworzy sieć wirtualną z dwóch maszyn wirtualnych (VM), które są członkami zestawu dostępności. Moduł równoważenia obciążenia kieruje ruch dla dwóch osobnych adresów IP do dwóch maszyn wirtualnych. Po uruchomieniu skryptu, można wdrożyć oprogramowanie serwera sieci web do maszyn wirtualnych i hostów wiele witryn sieci web z własnego adresu IP.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt


[!code-azurecli-interactive[main](../../../cli_scripts/load-balancer/load-balance-multiple-web-sites-vm/load-balance-multiple-web-sites-vm.sh  "Load balance multiple web sites")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń do utworzenia grupy zasobów, sieci wirtualnej, usługi równoważenia obciążenia i wszystkie powiązane zasoby. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [Tworzenie sieci wirtualnej sieci az](https://docs.microsoft.com/cli/azure/network/vnet#az_network_vnet_create) | Tworzy sieć wirtualna platformy Azure i podsieć. |
| [Tworzenie sieci az publicznego ip](https://docs.microsoft.com/cli/azure/network/public-ip#az_network_public_ip_create) | Tworzy publiczny adres IP z statyczny adres IP i skojarzonej nazwy DNS. |
| [Utwórz równoważeniem obciążenia sieciowego az](https://docs.microsoft.com/cli/azure/network/lb#az_network_lb_create) | Tworzy moduł równoważenia obciążenia Azure. |
| [Utwórz sondowania równoważeniem obciążenia sieciowego az](https://docs.microsoft.com/cli/azure/network/lb/probe#az_network_lb_probe_create) | Tworzy sondę modułu równoważenia obciążenia. Sondę modułu równoważenia obciążenia jest używany do monitorowania każdej maszyny Wirtualnej w zestawie usługi równoważenia obciążenia. Jeśli żadnej maszyny Wirtualnej staje się niedostępny, ruch nie jest kierowany do maszyny Wirtualnej. |
| [Utwórz regułę równoważeniem obciążenia sieciowego az](https://docs.microsoft.com/cli/azure/network/lb/rule#az_network_lb_rule_create) | Tworzy regułę modułu równoważenia obciążenia. W tym przykładzie jest tworzona reguła dla portu 80. Jak ruchu HTTP dociera do usługi równoważenia obciążenia, jest kierowany do portu 80 jednej z maszyn wirtualnych w zestawie usługi równoważenia obciążenia. |
| [Utwórz az sieci lb-ip frontonu](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_create) | Utwórz adres IP frontonu dla usługi równoważenia obciążenia. |
| [AZ lb puli adresów sieciowych — tworzenie](https://docs.microsoft.com/cli/azure/network/lb/address-pool#az_network_lb_address_pool_create) | Tworzy puli adresów zaplecza. |
| [Utwórz az kart interfejsu sieciowego](https://docs.microsoft.com/cli/azure/network/nic#az_network_nic_create) | Tworzy karty sieci wirtualnej i dołącza go do sieci wirtualnej i podsieci. |
| [Tworzenie maszyny wirtualnej az zestawu dostępności](https://docs.microsoft.com/cli/azure/network/lb/rule#az_network_lb_rule_create) | Tworzy zestaw dostępności. Zestawy dostępności upewnij się, czas działania aplikacji poprzez rozłożenie zasobów fizycznych maszyny wirtualnej tak, aby w przypadku niepowodzenia cały zestaw nie jest wykonywane. |
| [Tworzenie kart sieciowych az ip-config](https://docs.microsoft.com/cli/azure/network/nic/ip-config#az_network_nic_ip_config_create) | Tworzy confiuration IP. Musi być funkcja Microsoft.Network/AllowMultipleIpConfigurationsPerNic włączona dla Twojej subskrypcji. Tylko jedną konfigurację mogą być oznaczone jako podstawową konfigurację protokołu IP dla karty Sieciowej, za pomocą flagi — podstawowy upewnij. |
| [Tworzenie maszyny wirtualnej az](https://docs.microsoft.com/cli/azure/vm/availability-set#az_vm_availability_set_create) | Tworzy maszynę wirtualną i podłączony do karty sieciowej, sieci wirtualnej, podsieci i NSG. To polecenie określa również obraz maszyny wirtualnej ma być używane i zapewnić poświadczenia administracyjne.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Dodatkowe przykłady skryptów sieci interfejsu wiersza polecenia można znaleźć w [Azure Przegląd dokumentacji](../cli-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
