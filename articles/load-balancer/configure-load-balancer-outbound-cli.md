---
title: Configure load balancing and outbound rules using Azure CLI
titleSuffix: Azure Load Balancer
description: This article shows how to configure load balancing and outbound rules in a Standard Load Balancer using the Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: allensu
ms.openlocfilehash: 7230b0c2b80137b068bbeacf43ab2133491a69b0
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225467"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-cli"></a>Konfigurowanie równoważenia obciążenia i reguł ruchu wychodzącego w usłudze Load Balancer w warstwie Standardowa przy użyciu interfejsu wiersza polecenia platformy Azure

This quickstart shows you how to configure outbound rules in Standard Load Balancer using Azure CLI.  

When you are done, the Load Balancer resource contains two frontends and rules associated with them: one for inbound and another for outbound.  Each frontend has a reference to a public IP address and this scenario uses a different public IP address for inbound versus outbound.   The load balancing rule provides only inbound load balancing and the outbound rule controls the outbound NAT provided for the VM.  This quickstart uses two separate backend pools, one for inbound and one for outbound, to illustrate capability and allow for flexibility for this scenario.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](https://docs.microsoft.com/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

The following example creates a resource group named *myresourcegroupoutbound* in the *eastus2* location:

```azurecli-interactive
  az group create \
    --name myresourcegroupoutbound \
    --location eastus2
```
## <a name="create-virtual-network"></a>Tworzenie sieci wirtualnej
Create a virtual network named *myvnetoutbound* with a subnet named *mysubnetoutbound* in the *myresourcegroupoutbound* using [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet).

```azurecli-interactive
  az network vnet create \
    --resource-group myresourcegroupoutbound \
    --name myvnetoutbound \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mysubnetoutbound \
    --subnet-prefix 192.168.0.0/24
```

## <a name="create-inbound-public-ip-address"></a>Create inbound Public IP address 

Aby uzyskać dostęp do aplikacji internetowej za pośrednictwem Internetu, potrzebujesz publicznego adresu IP modułu równoważenia obciążenia. Usługa Load Balancer w warstwie Standardowa obsługuje tylko publiczne adresy IP w warstwie Standardowa. Use [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) to create a Standard Public IP address named *mypublicipinbound* in *myresourcegroupoutbound*.

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipinbound --sku standard
```

## <a name="create-outbound-public-ip-address"></a>Create outbound public IP address 

Create a Standard IP address for Load Balancer's frontend outbound configuration using [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip).

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipoutbound --sku standard
```

## <a name="create-azure-load-balancer"></a>Create Azure Load Balancer

W tej sekcji opisano szczegółowo procedurę tworzenia i konfigurowania następujących składników modułu równoważenia obciążenia:
  - A frontend IP that receives the incoming network traffic on the load balancer.
  - A backend pool where the frontend IP sends the load balanced network traffic.
  - A backend pool for outbound connectivity. 
  - A health probe that determines health of the backend VM instances.
  - A load balancer inbound rule that defines how traffic is distributed to the VMs.
  - A load balancer outbound rule that defines how traffic is distributed from the VMs.

### <a name="create-load-balancer"></a>Create Load Balancer

Create a Load Balancer with the inbound IP address using [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) named *lb* that includes an inbound frontend IP configuration and a backend pool *bepoolinbound* that is associated with the public IP address *mypublicipinbound* that you created in the preceding step.

```azurecli-interactive
  az network lb create \
    --resource-group myresourcegroupoutbound \
    --name lb \
    --sku standard \
    --backend-pool-name bepoolinbound \
    --frontend-ip-name myfrontendinbound \
    --location eastus2 \
    --public-ip-address mypublicipinbound   
  ```

### <a name="create-outbound-pool"></a>Create outbound pool

Create an additional backend address pool to define outbound connectivity for a pool of VMs with [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) with the name *bepooloutbound*.  Creating a separate outbound pool provides maximum flexibility, but you can omit this step and only use the inbound *bepoolinbound* as well.

```azurecli-interactive
  az network lb address-pool create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name bepooloutbound
```

### <a name="create-outbound-frontend-ip"></a>Create outbound frontend IP
Create the outbound frontend IP configuration for the Load Balancer with [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) that includes and outbound frontend IP configuration named *myfrontendoutbound* that is associated to the public IP address *mypublicipoutbound*

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myresourcegroupoutbound \
    --name myfrontendoutbound \
    --lb-name lb \
    --public-ip-address mypublicipoutbound 
  ```

### <a name="create-health-probe"></a>Create health probe

Sonda kondycji sprawdza wszystkie wystąpienia maszyny wirtualnej, aby upewnić się, że mogą wysyłać ruch sieciowy. Wystąpienie maszyny wirtualnej, w przypadku którego sprawdzanie kondycji za pomocą sondy nie powiodło się, jest usuwane z modułu równoważenia obciążenia do momentu ponownego przejścia do trybu online i pomyślnego sprawdzenia kondycji. Utwórz sondę kondycji za pomocą polecenia [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) w celu monitorowania kondycji maszyn wirtualnych. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name http \
    --protocol http \
    --port 80 \
    --path /  
```

### <a name="create-load-balancing-rule"></a>Create load balancing rule

A load balancer rule defines the frontend IP configuration for the incoming traffic and the backend pool to receive the traffic, along with the required source and destination port. Create a load balancer rule *myinboundlbrule* with [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) for listening to port 80 in the frontend pool *myfrontendinbound* and sending load-balanced network traffic to the backend address pool *bepool* also using port 80. 

>[!NOTE]
>This load balancing rule disables automatic outbound (S)NAT as a result of this rule with the --disable-outbound-snat parameter. Outbound NAT is only provided by the outbound rule.

```azurecli-interactive
az network lb rule create \
--resource-group myresourcegroupoutbound \
--lb-name lb \
--name inboundlbrule \
--protocol tcp \
--frontend-port 80 \
--backend-port 80 \
--probe http \
--frontend-ip-name myfrontendinbound \
--backend-pool-name bepoolinbound \
--disable-outbound-snat
```

### <a name="create-outbound-rule"></a>Create outbound rule

An outbound rule defines the frontend public IP, represented by the frontend *myfrontendoutbound*, which will be used for all outbound NAT traffic as well as the backend pool to which this rule applies.  Create an outbound rule *myoutboundrule* for outbound network translation of all virtual machines (NIC IP configurations) in *bepool* backend pool.  The command below also changes the outbound idle timeout from 4 to 15 minutes and allocates 10000 SNAT ports instead of 1024.  Review [outbound rules](https://aka.ms/lboutboundrules) for more details.

```azurecli-interactive
az network lb outbound-rule create \
 --resource-group myresourcegroupoutbound \
 --lb-name lb \
 --name outboundrule \
 --frontend-ip-configs myfrontendoutbound \
 --protocol All \
 --idle-timeout 15 \
 --outbound-ports 10000 \
 --address-pool bepooloutbound
```

If you do not want to use a separate outbound pool, you can change the address pool argument in the preceding command to specify *bepoolinbound* instead.  We recommend to use separate pools for flexibility and readability of the resulting configuration.

At this point, you can proceed with adding your VM's to the backend pool *bepoolinbound* __and__ *bepooloutbound* by updating the IP configuration of the respective NIC resources using [az network nic ip-config address-pool add](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów nie będzie już potrzebna, możesz użyć polecenia [az group delete](/cli/azure/group#az-group-delete), aby usunąć grupę zasobów, moduł równoważenia obciążenia oraz wszystkie pokrewne zasoby.

```azurecli-interactive 
  az group delete --name myresourcegroupoutbound
```

## <a name="next-steps"></a>Następne kroki
In this article, you created Standard Load Balancer, configured both inbound load balancer traffic rules, configured and health probe for the VMs in the backend pool. Aby dowiedzieć się więcej na temat usługi Azure Load Balancer, przejdź do samouczków dotyczących usługi Azure Load Balancer.

> [!div class="nextstepaction"]
> [Samouczki usługi Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
