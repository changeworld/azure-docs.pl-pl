---
title: Otwórz porty do maszyny Wirtualnej systemu Linux z interfejsu wiersza polecenia platformy Azure w wersji 1.0 | Dokumentacja firmy Microsoft
description: Dowiedz się, jak otworzyć port / utworzyć punktu końcowego maszyny Wirtualnej systemu Linux przy użyciu modelu wdrażania Menedżera zasobów platformy Azure i interfejsu wiersza polecenia platformy Azure w wersji 1.0
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: 21b408f4347bf59154407ac005a449f797c67e94
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="opening-ports-and-endpoints-to-a-linux-vm-in-azure-using-the-azure-cli-10"></a>Otwieranie portów i punktów końcowych do maszyny Wirtualnej systemu Linux na platformie Azure przy użyciu 1.0 interfejsu wiersza polecenia platformy Azure
Otwarcie portu lub utworzyć punkt końcowy z maszyną wirtualną (VM) na platformie Azure, tworząc filtr sieci w podsieci lub interfejsu sieciowego maszyny Wirtualnej. Te filtry, które kontrolują ruchu przychodzącego i wychodzącego, można umieścić na grupę zabezpieczeń sieci dołączony do tego zasobu, który odbiera ruch. Użyjmy typowym przykładem ruchu w sieci web na porcie 80. W tym artykule przedstawiono sposób otwierania portu z maszyną wirtualną za pomocą 1.0 interfejsu wiersza polecenia platformy Azure.


## <a name="cli-versions-to-complete-the-task"></a>Wersje interfejsu wiersza polecenia umożliwiające wykonanie zadania
Zadanie można wykonać przy użyciu jednej z następujących wersji interfejsu wiersza polecenia:

- [Azure CLI 1.0](#quick-commands) — nasze interfejsu wiersza polecenia dla klasycznego i zasobów zarządzania wdrażania modeli (w tym artykule)
- [Interfejs wiersza polecenia platformy Azure w wersji 2.0](nsg-quickstart.md) — nasz interfejs wiersza polecenia nowej generacji dla modelu wdrażania na potrzeby zarządzania zasobami


## <a name="quick-commands"></a>Szybkie polecenia
Aby utworzyć grupę zabezpieczeń sieci i potrzebnych reguł [1.0 interfejsu wiersza polecenia Azure](../../cli-install-nodejs.md) zainstalowanych i użycie tryb usługi Resource Manager:

```azurecli
azure config mode arm
```

W poniższych przykładach Zastąp przykładowe nazwy parametrów własne wartości. Przykład nazwy parametrów uwzględnione *myResourceGroup*, *myNetworkSecurityGroup*, i *myVnet*.

Utwórz swojej grupy zabezpieczeń sieci, odpowiednio wprowadzania własne nazwy i lokalizacji. Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie *myNetworkSecurityGroup* w *eastus* lokalizacji:

```azurecli
azure network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Dodaj regułę, aby zezwolić na ruch HTTP, aby na serwerze sieci Web (lub dostosować własne scenariusz, takich jak SSH łączności dostępu lub bazy danych). Poniższy przykład tworzy reguły o nazwie *myNetworkSecurityGroupRule* zezwalająca na ruch TCP na porcie 80:

```azurecli
azure network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --direction inbound \
    --priority 1000 \
    --destination-port-range 80 \
    --access allow
```

Skojarzenia sieciowej grupy zabezpieczeń z maszyny Wirtualnej interfejsu sieciowego (NIC). Poniższy przykład powoduje skojarzenie istniejącej karty NIC o nazwie *myNic* z sieciową grupą zabezpieczeń o nazwie *myNetworkSecurityGroup*:

```azurecli
azure network nic set \
    --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup \
    --name myNic
```

Alternatywnie można skojarzyć swojej grupy zabezpieczeń sieci z podsiecią sieci wirtualnej, a nie tylko do interfejsu sieciowego na jednej maszynie Wirtualnej. Poniższy przykład powoduje skojarzenie istniejącą podsieć o nazwie *mySubnet* w *myVnet* sieci wirtualnej z sieciową grupą zabezpieczeń o nazwie *myNetworkSecurityGroup*:

```azurecli
azure network vnet subnet set \
    --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup \
    --vnet-name myVnet --name mySubnet
```

## <a name="more-information-on-network-security-groups"></a>Więcej informacji na temat grup zabezpieczeń sieci
Szybkie polecenia tutaj pozwala rozpocząć pracę z ruchem przepływać do maszyny Wirtualnej. Grupy zabezpieczeń sieci zapewniają wiele funkcje i poziom szczegółowości kontrolowania dostępu do zasobów. Możesz przeczytać dodatkowe informacje [Tworzenie grupy zabezpieczeń sieci i listy ACL zasady tutaj](../../virtual-network/tutorial-filter-network-traffic-cli.md).

Grupy zabezpieczeń sieci i reguły list kontroli dostępu można zdefiniować jako część szablonów usługi Azure Resource Manager. Przeczytaj więcej na temat [tworzenie grup zabezpieczeń sieci z szablonami](../../virtual-network/template-samples.md).

Jeśli musisz użyć przekierowania portów do mapowania unikatowy portu zewnętrznego wewnętrznego portu na maszynie Wirtualnej, należy użyć modułu równoważenia obciążenia i reguły translatora adresów sieciowych (NAT). Na przykład można ujawnić zewnętrznie TCP port 8080 i ruch kierowany aby port TCP 80 na maszynie Wirtualnej. Informacje na temat [tworzenia modułu równoważenia obciążenia internetowy](../../load-balancer/load-balancer-get-started-internet-arm-cli.md).

## <a name="next-steps"></a>Kolejne kroki
W tym przykładzie utworzono prosta Reguła zezwalająca na ruch HTTP. Można znaleźć informacje dotyczące tworzenia środowisk bardziej szczegółowe zawierają następujące artykuły:

* [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
* [Co to jest sieciowa grupa zabezpieczeń?](../../virtual-network/security-overview.md)
* [Omówienie usługi Azure Resource Manager dla usługi równoważenia obciążenia](../../load-balancer/load-balancer-arm.md)

