---
title: Użyj filtru pakietów FreeBSD na utworzenie zapory na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć przy użyciu jego FreeBSD zapory NAT. PF na platformie Azure.
services: virtual-machines-linux
documentationcenter: ''
author: KylieLiang
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/20/2017
ms.author: kyliel
ms.openlocfilehash: 42312522381529075f544a6c277f66a4ef1982d2
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30904257"
---
# <a name="how-to-use-freebsds-packet-filter-to-create-a-secure-firewall-in-azure"></a>Jak używać filtr pakietów FreeBSD na utworzenie bezpiecznego zapory na platformie Azure
W tym artykule przedstawiono sposób wdrażania zapory NAT. przy użyciu filtru pakujący FreeBSD firmy przy użyciu szablonu usługi Azure Resource Manager dla typowego scenariusza serwera sieci web.

## <a name="what-is-pf"></a>Co to jest PF?
PF (filtr pakietów równocześnie zapisywane pf) jest filtr licencjonowane pakietów BSD, centralnej części oprogramowanie zapory. PF ponieważ powstał szybko, a teraz ma kilka zalet w porównaniu z innych dostępnych zapór. Translatora adresów sieciowych (NAT) jest PF od pierwszego dnia, a następnie Harmonogram pakietów i zarządzania active kolejki są integrowane PF, integrowanie ALTQ i co można skonfigurować za pomocą PF w konfiguracji. Funkcje, takie jak pfsync i protokołu CARP dla trybu failover i nadmiarowość, authpf dla sesji uwierzytelniania i proxy ftp, aby ułatwić zapory trudne protokołu FTP, również rozszerzony PF. Krótko mówiąc PF jest wydajny i bogate zapory. 

## <a name="get-started"></a>Rozpoczęcie pracy
Jeśli planuje się skonfigurowanie bezpiecznego zapory w chmurze dla serwerów sieci web, Zacznijmy od początku. Można także zastosować dla skryptów używana w tym szablonie usługi Azure Resource Manager, do konfigurowania topologii sieci.
Szablon usługi Azure Resource Manager skonfigurować FreeBSD maszyny wirtualnej, która wykonuje /redirection translatora adresów Sieciowych przy użyciu PF i dwie maszyny wirtualne FreeBSD na serwerze sieci web Nginx zainstalowana i skonfigurowana. Oprócz przeprowadzania NAT dla ruchu wyjście serwerów dwie sieci web, maszyny wirtualnej NAT/przekierowanie przechwytuje żądania HTTP i Przekierowanie do serwerów sieci web dwóch w okrężne. Sieć wirtualna używa prywatnego 10.0.0.2/24 miejsca bez obsługi routingu adresów IP i można zmodyfikować parametry szablonu. Szablon usługi Azure Resource Manager definiuje również tabelę tras dla całej sieci wirtualnej, który jest kolekcją indywidualnych tras, służy do zastępowania trasy domyślne Azure na podstawie docelowego adresu IP. 

![pf_topology](./media/freebsd-pf-nat/pf_topology.jpg)
    
### <a name="deploy-through-azure-cli"></a>Wdrażanie za pośrednictwem interfejsu wiersza polecenia platformy Azure
Należy najnowszej [Azure CLI 2.0](/cli/azure/install-az-cli2) zainstalowane i zalogowany do konta platformy Azure przy użyciu [logowania az](/cli/azure/reference-index#az_login). Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Poniższy przykład tworzy nazwę grupy zasobów `myResourceGroup` w `West US` lokalizacji.

```azurecli
az group create --name myResourceGroup --location westus
```

Następnie należy wdrożyć szablon [pf freebsd Konfiguracja](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup) z [Utwórz wdrożenie grupy az](/cli/azure/group/deployment#az_group_deployment_create). Pobierz [azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/pf-freebsd-setup/azuredeploy.parameters.json) w tej samej ścieżce i definiować własne wartości zasobów, takich jak `adminPassword`, `networkPrefix`, i `domainNamePrefix`. 

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeploymentName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/pf-freebsd-setup/azuredeploy.json \
    --parameters '@azuredeploy.parameters.json' --verbose
```

Po około pięciu minut, uzyskasz informacje `"provisioningState": "Succeeded"`. Następnie możesz ssh na frontonie maszyny Wirtualnej (NAT) lub dostęp Nginx serwera sieci web w przeglądarce, za pomocą publicznego adresu IP lub nazwa FQDN maszyny Wirtualnej (NAT) serwera sieci Web. Poniższy przykład zawiera nazwy FQDN i publiczny adres IP, przypisana do maszyny Wirtualnej (NAT) serwera sieci Web w `myResourceGroup` grupy zasobów. 

```azurecli
az network public-ip list --resource-group myResourceGroup
```
    
## <a name="next-steps"></a>Kolejne kroki
Czy chcesz skonfigurować własny translatora adresów Sieciowych na platformie Azure? Open Source, bezpłatne, ale wydajne? Następnie PF jest dobrym rozwiązaniem. Przy użyciu szablonu [pf freebsd Konfiguracja](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup), wystarczy pięć minut Aby skonfigurować zaporę translacji adresów Sieciowych z obciążenia okrężnego równoważenia przy użyciu FreeBSD na PF na platformie Azure dla typowego scenariusza serwera sieci web. 

Jeśli chcesz dowiedzieć się ofercie FreeBSD na platformie Azure, zobacz [wprowadzenie do FreeBSD na platformie Azure](freebsd-intro-on-azure.md).

Jeśli chcesz dowiedzieć się więcej o PF, zapoznaj się [Podręcznik FreeBSD](https://www.freebsd.org/doc/handbook/firewalls-pf.html) lub [Podręcznik PF użytkownika](https://www.freebsd.org/doc/handbook/firewalls-pf.html).
