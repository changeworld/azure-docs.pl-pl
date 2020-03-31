---
title: Użyj filtru pakietów FreeBSD, aby utworzyć zaporę na platformie Azure
description: Dowiedz się, jak wdrożyć zaporę NAT przy użyciu pf freebsd na platformie Azure.
author: KylieLiang
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/20/2017
ms.author: kyliel
ms.openlocfilehash: 9b78c0d93b57a3e3f4963088d0b93f121f57483c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945103"
---
# <a name="how-to-use-freebsds-packet-filter-to-create-a-secure-firewall-in-azure"></a>Jak utworzyć bezpieczną zaporę na platformie Azure za pomocą filtru pakietów FreeBSD
W tym artykule przedstawiono sposób wdrażania zapory NAT przy użyciu filtru pakowania FreeBSD za pośrednictwem szablonu usługi Azure Resource Manager dla wspólnego scenariusza serwera sieci web.

## <a name="what-is-pf"></a>Co to jest PF?
PF (Packet Filter, również napisany pf) to licencjonowany przez BSD stanowy filtr pakietów, centralny element oprogramowania do zapory. PF od tego czasu ewoluował szybko i teraz ma kilka zalet w stosunku do innych dostępnych zapór. Translacja adresów sieciowych (NAT) jest w PF od pierwszego dnia, a następnie harmonogram pakietów i aktywne zarządzanie kolejkami zostały zintegrowane z PF, integrując ALTQ i sprawiając, że można go skonfigurować za pomocą konfiguracji PF. Funkcje, takie jak pfsync i CARP do pracy awaryjnej i nadmiarowości, authpf do uwierzytelniania sesji i ftp-proxy w celu ułatwienia zapory trudnego protokołu FTP, również rozszerzyły PF. Krótko mówiąc, PF to potężna i bogata w funkcje zapora ogniowa. 

## <a name="get-started"></a>Wprowadzenie
Jeśli chcesz skonfigurować bezpieczną zaporę w chmurze dla serwerów sieci web, zacznijmy. Skrypty używane w tym szablonie usługi Azure Resource Manager można również zastosować do skonfigurowania topologii sieci.
Szablon usługi Azure Resource Manager skonfigurował maszynę wirtualną FreeBSD, która wykonuje nat /przekierowanie przy użyciu pf i dwóch maszyn wirtualnych FreeBSD z zainstalowanym i skonfigurowanym serwerem sieci Web Nginx. Oprócz wykonywania translatora adresów sieciowych dla ruchu wychodzącego dwóch serwerów sieci web maszyna wirtualna NAT/przekierowanie przechwytuje żądania HTTP i przekierowuje je na dwa serwery sieci web w sposób okrężny. Sieć wirtualna używa prywatnej nierutowalnej przestrzeni adresowej IP 10.0.0.2/24 i można zmodyfikować parametry szablonu. Szablon usługi Azure Resource Manager definiuje również tabelę tras dla całej sieci wirtualnej, która jest kolekcją poszczególnych tras używanych do zastępowania domyślnych tras platformy Azure na podstawie docelowego adresu IP. 

![pf_topology](./media/freebsd-pf-nat/pf_topology.jpg)
    
### <a name="deploy-through-azure-cli"></a>Wdrażanie za pomocą interfejsu wiersza polecenia platformy Azure
Potrzebujesz najnowszego interfejsu [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) zainstalowanego i zalogowanego do konta platformy Azure przy użyciu [logowania az.](/cli/azure/reference-index) Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład tworzy nazwę `myResourceGroup` grupy `West US` zasobów w lokalizacji.

```azurecli
az group create --name myResourceGroup --location westus
```

Następnie wdrożyć szablon [pf-freebsd-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup) z [az group deployment create](/cli/azure/group/deployment). Pobierz [azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/pf-freebsd-setup/azuredeploy.parameters.json) w tej samej ścieżce i `adminPassword` `networkPrefix`zdefiniuj własne wartości zasobów, takie jak , i `domainNamePrefix`. 

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeploymentName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/pf-freebsd-setup/azuredeploy.json \
    --parameters '@azuredeploy.parameters.json' --verbose
```

Po około pięciu minutach otrzymasz `"provisioningState": "Succeeded"`informacje o . Następnie można ssh do frontendu VM (NAT) lub dostęp do serwera sieci Web Nginx w przeglądarce przy użyciu publicznego adresu IP lub FQDN frontendu VM (NAT). Poniższy przykład zawiera listę FQDN i publiczny adres IP, który został `myResourceGroup` przypisany do frontendu VM (NAT) w grupie zasobów. 

```azurecli
az network public-ip list --resource-group myResourceGroup
```
    
## <a name="next-steps"></a>Następne kroki
Czy chcesz skonfigurować własne NAT na platformie Azure? Open Source, wolny, ale potężny? Wtedy PF jest dobrym wyborem. Korzystając z [szablonu pf-freebsd-setup,](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup)wystarczy tylko pięć minut, aby skonfigurować zaporę NAT z równoważeniem obciążenia okrężnego przy użyciu pfd FreeBSD na platformie Azure dla wspólnego scenariusza serwera sieci web. 

Jeśli chcesz poznać ofertę FreeBSD na platformie Azure, zapoznaj się [z wprowadzeniem do FreeBSD na platformie Azure.](freebsd-intro-on-azure.md)

Jeśli chcesz dowiedzieć się więcej o PF, zapoznaj się z [podręcznikiem FreeBSD](https://www.freebsd.org/doc/handbook/firewalls-pf.html) lub [przewodnikiem PF-User's Guide.](https://www.freebsd.org/doc/handbook/firewalls-pf.html)
