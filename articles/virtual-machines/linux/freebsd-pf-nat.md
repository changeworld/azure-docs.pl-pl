---
title: Używanie filtru pakietów systemu FreeBSD, aby utworzyć zaporę na platformie Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie wdrażania zapory NAT za pomocą FreeBSD PF w systemie Azure.
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
ms.openlocfilehash: 8cfa1696a18925e9e9e8b96299f1255875e85aa8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60542991"
---
# <a name="how-to-use-freebsds-packet-filter-to-create-a-secure-firewall-in-azure"></a>Jak utworzyć zaporę na bezpiecznej platformie Azure za pomocą filtru pakietów systemu FreeBSD
W tym artykule przedstawiono sposób wdrażania zapory NAT za pomocą filtru usługi Packer FreeBSD przy użyciu szablonu usługi Azure Resource Manager dla typowego scenariusza serwera sieci web.

## <a name="what-is-pf"></a>Co to jest PF?
PF (filtr pakietów również zapisywane pf) jest filtr pakietów licencjonowane BSD, Centralna część oprogramowanie zapory. PF, ponieważ powstała szybko i ma kilka zalet w stosunku do innych zapór dostępne. Translacji adresów sieciowych (NAT) znajduje się w pliku PF od pierwszego dnia, a następnie Harmonogram pakietów i kolejki active management zostały zintegrowane z programem PF, przez integrowanie ALTQ i co można skonfigurować za pomocą pliku PF w konfiguracji. Funkcje, takie jak pfsync i protokołu CARP dla trybu failover i nadmiarowość, authpf dla sesji uwierzytelniania i serwer proxy ftp do jej obsługi ułatwiają realizację zapory trudne protokołu FTP, również rozszerzono PF. Krótko mówiąc PF to zaawansowane i bogate Zapora. 

## <a name="get-started"></a>Rozpoczęcie pracy
Jeśli interesują Cię konfigurowania zapory bezpieczne w chmurze na potrzeby serwerów sieci web, zaczynajmy. Można także zastosować skrypty użytych w tym szablonie usługi Azure Resource Manager, aby skonfigurować topologii sieci.
Szablon usługi Azure Resource Manager, skonfiguruj FreeBSD maszyny wirtualnej, który wykonuje /redirection translatora adresów Sieciowych przy użyciu pliku PF i dwie maszyny wirtualne FreeBSD z serwera internetowego Nginx, zainstalowany i skonfigurowany. Oprócz wykonywania translatora adresów Sieciowych za ruch wychodzący web dwa serwery, maszyny wirtualnej translatora adresów Sieciowych/przekierowanie przechwytuje żądania HTTP i przekierowywać je do serwerów internetowych dwa w okrężne. Sieci wirtualnej używa 10.0.0.2/24 prywatnych przestrzeni adresów bez obsługi routingu IP, a następnie można zmodyfikować parametry szablonu. Szablon usługi Azure Resource Manager definiuje również tabelę tras dla całej sieci wirtualnej, który jest kolekcją indywidualnych tras, które umożliwiają zastępują trasy domyślne systemu Azure, w oparciu o docelowy adres IP. 

![pf_topology](./media/freebsd-pf-nat/pf_topology.jpg)
    
### <a name="deploy-through-azure-cli"></a>Wdrażanie przy użyciu wiersza polecenia platformy Azure
Potrzebujesz najnowszej [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) zainstalowane i zalogować się do konta platformy Azure przy użyciu [az login](/cli/azure/reference-index). Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład tworzy nazwę grupy zasobów `myResourceGroup` w `West US` lokalizacji.

```azurecli
az group create --name myResourceGroup --location westus
```

Następnie Wdróż szablon [pf freebsd skonfigurowania](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup) z [Utwórz wdrożenie grupy az](/cli/azure/group/deployment). Pobierz [azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/pf-freebsd-setup/azuredeploy.parameters.json) w tej samej ścieżce i zdefiniować własne wartości zasobów, takich jak `adminPassword`, `networkPrefix`, i `domainNamePrefix`. 

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeploymentName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/pf-freebsd-setup/azuredeploy.json \
    --parameters '@azuredeploy.parameters.json' --verbose
```

Po około pięciu minut, otrzymasz informacje o `"provisioningState": "Succeeded"`. Następnie możesz ssh do frontonu maszyn wirtualnych (NAT) lub dostęp do serwera internetowego Nginx w przeglądarce, za pomocą publicznego adresu IP lub nazwa FQDN maszyny Wirtualnej (NAT) serwera sieci Web. Poniższy przykład wyświetla nazwy FQDN i publiczny adres IP przypisany do maszyny Wirtualnej (NAT) serwera sieci Web w `myResourceGroup` grupy zasobów. 

```azurecli
az network public-ip list --resource-group myResourceGroup
```
    
## <a name="next-steps"></a>Kolejne kroki
Czy chcesz skonfigurować własny Translator adresów Sieciowych na platformie Azure? Oprogramowanie typu Open Source, bezpłatne, ale zaawansowane? Następnie PF jest dobrym rozwiązaniem. Przy użyciu szablonu [pf freebsd skonfigurowania](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup), wystarczy pięć minut skonfigurować zapory NAT za pomocą działania okrężnego równoważenia przy użyciu FreeBSD użytkownika PF na platformie Azure dla typowego scenariusza serwera sieci web. 

Aby dowiedzieć się, oferty FreeBSD na platformie Azure, zapoznaj się [wprowadzenie do systemu FreeBSD na platformie Azure](freebsd-intro-on-azure.md).

Aby dowiedzieć się więcej na temat PF, zapoznaj się [poradnik FreeBSD](https://www.freebsd.org/doc/handbook/firewalls-pf.html) lub [PF User Guide](https://www.freebsd.org/doc/handbook/firewalls-pf.html).
