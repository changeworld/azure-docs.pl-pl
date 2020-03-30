---
title: Równoważenie obciążenia w wielu konfiguracjach IP przy użyciu interfejsu wiersza polecenia platformy Azure
titleSuffix: Azure Load Balancer
description: Dowiedz się, jak przypisać wiele adresów IP do maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: allensu
ms.openlocfilehash: 69d324647af014a5122c404929c104a9077d5f13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225304"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-azure-cli"></a>Równoważenie obciążenia w wielu konfiguracjach IP przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule opisano sposób używania modułu równoważenia obciążenia platformy Azure z wieloma adresami IP w pomocniczym interfejsie sieciowym (NIC). W tym scenariuszu mamy dwie maszyny wirtualne z systemem Windows, z których każda ma podstawową i pomocniczą kartę sieciową. Każda z pomocniczych kart sieciowych ma dwie konfiguracje adresów IP. Każda maszyna wirtualna obsługuje obie witryny sieci Web contoso.com i fabrikam.com. Każda witryna sieci Web jest powiązana z jedną z konfiguracji adresów IP na pomocniczej karcie sieciowej. Używamy usługi Azure Load Balancer do udostępnienia dwóch adresów IP frontonu, po jednym dla każdej witryny sieci Web, w celu dystrybucji ruchu do odpowiedniej konfiguracji ip dla witryny sieci Web. W tym scenariuszu używa tego samego numeru portu w obu frontendach, a także adresach IP puli wewnętrznej bazy danych.

![Obraz scenariusza LB](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Kroki równoważenia obciążenia w wielu konfiguracjach IP

Aby osiągnąć scenariusz opisany w tym artykule, wykonaj następujące kroki:

1. [Zainstaluj i skonfiguruj interfejsu wiersza polecenia platformy Azure,](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) wykonując kroki opisane w połączonym artykule i zaloguj się do konta platformy Azure.
2. [Utwórz grupę zasobów](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-group) o nazwie *contosofabrikam* w następujący sposób:

    ```azurecli
    az group create contosofabrikam westcentralus
    ```

3. [Utwórz dostępność ustawioną](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-an-availability-set) na dwie maszyny wirtualne. W tym scenariuszu należy użyć następującego polecenia:

    ```azurecli
    az vm availability-set create --resource-group contosofabrikam --location westcentralus --name myAvailabilitySet
    ```

4. [Utwórz sieć wirtualną](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet) o nazwie *myVNet* i podsieć o nazwie *mySubnet*:

    ```azurecli
    az network vnet create --resource-group contosofabrikam --name myVnet --address-prefixes 10.0.0.0/16  --location westcentralus --subnet-name MySubnet --subnet-prefix 10.0.0.0/24

    ```

5. [Utwórz moduł równoważenia obciążenia](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o nazwie *mylb*:

    ```azurecli
    az network lb create --resource-group contosofabrikam --location westcentralus --name mylb
    ```

6. Utwórz dwa dynamiczne publiczne adresy IP dla konfiguracji IP frontu modułu równoważenia obciążenia:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp1 --domain-name-label contoso --allocation-method Dynamic

    az network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp2 --domain-name-label fabrikam --allocation-method Dynamic
    ```

7. Utwórz dwie konfiguracje IP frontonu, *contosofe* i *fabrikamfe* odpowiednio:

    ```azurecli
    az network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp1 --name contosofe
    az network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp2 --name fabrkamfe
    ```

8. Tworzenie pul adresów wewnętrznej bazy danych —*HTTP* *contosopool* i *fabrikampool*, http [sondy](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) - oraz reguł równoważenia obciążenia — *HTTPc* i *HTTPf*:

    ```azurecli
    az network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name contosopool
    azure network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name fabrikampool

    az network lb probe create --resource-group contosofabrikam --lb-name mylb --name HTTP --protocol "http" --interval 15 --count 2 --path index.html

    az network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPc --protocol tcp --probe-name http--frontend-port 5000 --backend-port 5000 --frontend-ip-name contosofe --backend-address-pool-name contosopool
    az network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPf --protocol tcp --probe-name http --frontend-port 5000 --backend-port 5000 --frontend-ip-name fabrkamfe --backend-address-pool-name fabrikampool
    ```

9. Sprawdź dane wyjściowe, aby [sprawdzić, czy moduł równoważenia obciążenia](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) został utworzony poprawnie, uruchamiając następujące polecenie:

    ```azurecli
    az network lb show --resource-group contosofabrikam --name mylb
    ```

10. [Utwórz publiczny adres IP,](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-public-ip-address) *myPublicIp*i [konto magazynu](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json), *mystorageaccont1* dla pierwszej maszyny wirtualnej VM1 w następujący sposób:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP --domain-name-label mypublicdns345 --allocation-method Dynamic

    az storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount1
    ```

11. [Utwórz interfejsy sieciowe](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-nic) dla maszyny Wirtualnej1 i dodaj drugą konfigurację IP, *VM1-ipconfig2,* i [utwórz maszynę wirtualną w](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-vm) następujący sposób:

    ```azurecli
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM1Nic1 --ip-config-name NIC1-ipconfig1
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM1Nic2 --ip-config-name VM1-ipconfig1 --public-ip-name myPublicIP --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    az network nic ip-config create --resource-group contosofabrikam --nic-name VM1Nic2 --name VM1-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    az vm create --resource-group contosofabrikam --name VM1 --location westcentralus --os-type linux --nic-names VM1Nic1,VM1Nic2  --vnet-name VNet1 --vnet-subnet-name Subnet1 --availability-set myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount1 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

12. Powtórz kroki 10-11 dla drugiej maszyny Wirtualnej:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP2 --domain-name-label mypublicdns785 --allocation-method Dynamic
    az storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount2
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM2Nic1
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM2Nic2 --ip-config-name VM2-ipconfig1 --public-ip-name myPublicIP2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    az network nic ip-config create --resource-group contosofabrikam --nic-name VM2Nic2 --name VM2-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    az vm create --resource-group contosofabrikam --name VM2 --location westcentralus --os-type linux --nic-names VM2Nic1,VM2Nic2 --vnet-name VNet1 --vnet-subnet-name Subnet1 --availability-set myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount2 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

13. Na koniec należy skonfigurować rekordy zasobów DNS tak, aby wskazywały odpowiedni adres IP frontendu modułu Równoważenia obciążenia. Możesz hostować swoje domeny w usłudze Azure DNS. Aby uzyskać więcej informacji na temat korzystania z usługi Azure DNS z modułem równoważenia obciążenia, zobacz [Korzystanie z usługi Azure DNS z innymi usługami platformy Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o łączeniu usług równoważenia obciążenia na platformie Azure w [obszarze Korzystanie z usług równoważenia obciążenia na platformie Azure.](../traffic-manager/traffic-manager-load-balancing-azure.md)
- Dowiedz się, jak używać różnych typów dzienników na platformie Azure do zarządzania i rozwiązywania problemów z modułem równoważenia obciążenia w [obszarze Analiza dzienników dla modułu Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).
