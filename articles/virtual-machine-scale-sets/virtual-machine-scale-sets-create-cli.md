---
title: "Tworzenie zestawu skalowania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 2.0 | Microsoft Docs"
description: "Dowiedz się, jak szybko utworzyć skalę maszyny wirtualnej przy użyciu programu Azure PowerShell"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: get-started-article
ms.date: 12/19/2017
ms.author: iainfou
ms.openlocfilehash: 8794ea7d998293e7ea88ba780f67ef8a021f2298
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2018
---
# <a name="create-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Tworzenie zestawu skalowania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 2.0
Zestaw skalowania maszyn wirtualnych umożliwia wdrożenie zestawu identycznych, automatycznie skalowanych maszyn wirtualnych, oraz zarządzanie nimi. Maszyny wirtualne w zestawie skalowania można skalować ręcznie lub można zdefiniować reguły skalowania automatycznego na podstawie użycia zasobów, takich jak procesor CPU, zapotrzebowanie na pamięć lub ruch sieciowy. W tym artykule wprowadzającym opisano tworzenie zestawu skalowania maszyn wirtualnych za pomocą interfejsu wiersza polecenia platformy Azure w wersji 2.0. Zestaw skalowania można również utworzyć przy użyciu [programu Azure PowerShell](virtual-machine-scale-sets-create-powershell.md) lub [witryny Azure Portal](virtual-machine-scale-sets-create-portal.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.20 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli). 


## <a name="create-a-scale-set"></a>Tworzenie zestawu skalowania
Zanim będzie można utworzyć zestaw skalowania, utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teraz utwórz zestaw skalowania maszyn wirtualnych przy użyciu polecenia [az vmss create](/cli/azure/vmss#az_vmss_create). W poniższym przykładzie pokazano tworzenie zestawu skalowania o nazwie *myScaleSet* i generowanie kluczy SSH, jeśli nie istnieją:

```azurecli-interactive 
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys
```

Utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania i maszyn wirtualnych trwa kilka minut.


## <a name="install-nginx-webserver"></a>Instalowanie serwera internetowego NGINX
Aby przetestować zestaw skalowania, użyj niestandardowego rozszerzenia skryptu w celu pobrania i uruchomienia skryptu, który instaluje serwer NGINX na wystąpieniach maszyn wirtualnych. To rozszerzenie jest przydatne w przypadku konfiguracji po wdrożeniu, instalowania oprogramowania lub każdego innego zadania związanego z konfiguracją lub zarządzaniem. Aby uzyskać więcej informacji, zobacz [Omówienie niestandardowego rozszerzenia skryptu](../virtual-machines/linux/extensions-customscript.md).

Zastosuj niestandardowe rozszerzenie skryptu, które instaluje serwer NGINX, w następujący sposób:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],"commandToExecute":"./automate_nginx.sh"}'
```


## <a name="allow-web-traffic"></a>Zezwalanie na ruch internetowy
Aby zezwolić na ruch internetowy na serwerze, utwórz regułę modułu równoważenia obciążenia za pomocą polecenia [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create). W poniższym przykładzie pokazano tworzenie reguły o nazwie *myLoadBalancerRuleWeb*:

```azurecli-interactive 
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```


## <a name="test-your-web-server"></a>Testowanie serwera internetowego
Aby zobaczyć, jak działa serwer internetowy, uzyskaj publiczny adres IP modułu równoważenia obciążenia za pomocą polecenia [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). W poniższym przykładzie pokazano uzyskiwanie adresu IP dla modułu *myScaleSetLBPublicIP* utworzonego w ramach zestawu skalowania:

```azurecli-interactive 
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Wprowadź publiczny adres IP modułu równoważenia obciążenia w przeglądarce internetowej. Moduł równoważenia obciążenia kieruje ruch do jednego z wystąpień maszyn wirtualnych, jak pokazano w poniższym przykładzie:

![Domyślna strona internetowa na serwerze NGINX](media/virtual-machine-scale-sets-create-cli/running-nginx-site.png)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy grupa zasobów, zestaw skalowania i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [az group delete](/cli/azure/group#az_group_delete) w następujący sposób:

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Następne kroki
W tym artykule wprowadzającym utworzyliśmy podstawowy zestaw skalowania i użyliśmy niestandardowego rozszerzenia skryptu w celu zainstalowania podstawowego serwera internetowego NGINX na wystąpieniach maszyn wirtualnych. W celu osiągnięcia większej skalowalności i automatyzacji rozszerz swój zestaw skalowania, korzystając z następujących artykułów z instrukcjami:

- [Wdrażanie aplikacji na zestawach skalowania maszyn wirtualnych](virtual-machine-scale-sets-deploy-app.md)
- Skalowanie automatyczne za pomocą [interfejsu wiersza polecenia platformy Azure](virtual-machine-scale-sets-autoscale-cli.md), [programu Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md), lub [witryny Azure Portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Używanie automatycznych uaktualnień systemu operacyjnego dla wystąpień maszyn wirtualnych zestawu skalowania](virtual-machine-scale-sets-automatic-upgrade.md)