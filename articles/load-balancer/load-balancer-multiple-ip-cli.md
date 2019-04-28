---
title: Równoważenie obciążenia na wielu konfiguracji adresu IP przy użyciu wiersza polecenia platformy Azure
titlesuffix: Azure Load Balancer
description: Dowiedz się, jak przypisać wiele adresów IP do maszyny wirtualnej przy użyciu wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: kumud
ms.openlocfilehash: 1e8911847a555e3b6326f15d15a09344a4472f2c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60860559"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-azure-cli"></a>Równoważenie obciążenia na wielu konfiguracji adresu IP przy użyciu wiersza polecenia platformy Azure

W tym artykule opisano sposób użycia usługi Azure Load Balancer z wieloma adresami IP na pomocniczego interfejsu sieciowego (NIC). W tym scenariuszu mamy dwóch maszyn wirtualnych z systemem Windows, każdy z podstawowej i pomocniczej karty sieciowej Każda z dodatkową kartą sieciową ma dwie konfiguracje adresów IP. Każda maszyna wirtualna jest hostem witryny sieci Web contoso.com i fabrikam.com. Każda witryna sieci Web jest powiązana z jedną konfiguracją IP pomocniczej karty sieciowej Usługa Azure Load Balancer są używane do udostępnienia dwóch adresów IP frontonu, po jednym dla każdej witryny sieci Web, aby dystrybuować ruch do odpowiednich konfiguracji IP witryny sieci Web. W tym scenariuszu użyto tego samego numeru portu dla zarówno frontonów, jak i adresy IP puli zaplecza.

![Obraz scenariusz modułu równoważenia obciążenia](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Kroki, aby zrównoważyć obciążenie na wiele konfiguracji adresów IP

Aby osiągnąć scenariusz opisany w tym artykule, wykonaj następujące czynności:

1. [Instalowanie i Konfigurowanie interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) wykonując czynności opisane w artykule połączone i dziennika na koncie platformy Azure.
2. [Utwórz grupę zasobów](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-group) o nazwie *contosofabrikam* w następujący sposób:

    ```azurecli
    az group create contosofabrikam westcentralus
    ```

3. [Tworzenie zestawu dostępności](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-an-availability-set) do dla dwóch maszyn wirtualnych. W tym scenariuszu należy użyć następującego polecenia:

    ```azurecli
    az vm availability-set create --resource-group contosofabrikam --location westcentralus --name myAvailabilitySet
    ```

4. [Tworzenie sieci wirtualnej](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet) o nazwie *myVNet* i podsieć o nazwie *mySubnet*:

    ```azurecli
    az network vnet create --resource-group contosofabrikam --name myVnet --address-prefixes 10.0.0.0/16  --location westcentralus --subnet-name MySubnet --subnet-prefix 10.0.0.0/24

    ```

5. [Tworzenie modułu równoważenia obciążenia](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o nazwie *mylb*:

    ```azurecli
    az network lb create --resource-group contosofabrikam --location westcentralus --name mylb
    ```

6. Utwórz dwa dynamiczne publiczne adresy IP, dla konfiguracji adresów IP frontonu modułu równoważenia obciążenia:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp1 --domain-name-label contoso --allocation-method Dynamic

    az network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp2 --domain-name-label fabrikam --allocation-method Dynamic
    ```

7. Utwórz dwie konfiguracje adresu IP frontonu *contosofe* i *fabrikamfe* odpowiednio:

    ```azurecli
    az network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp1 --name contosofe
    az network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp2 --name fabrkamfe
    ```

8. Utwórz wewnętrzną bazą danych, pule adresów - *contosopool* i *fabrikampool*, [sondy](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) - *HTTP*i obciążenia reguły - równoważenia *HTTPc* i *HTTPf*:

    ```azurecli
    az network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name contosopool
    azure network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name fabrikampool

    az network lb probe create --resource-group contosofabrikam --lb-name mylb --name HTTP --protocol "http" --interval 15 --count 2 --path index.html

    az network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPc --protocol tcp --probe-name http--frontend-port 5000 --backend-port 5000 --frontend-ip-name contosofe --backend-address-pool-name contosopool
    az network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPf --protocol tcp --probe-name http --frontend-port 5000 --backend-port 5000 --frontend-ip-name fabrkamfe --backend-address-pool-name fabrikampool
    ```

9. Sprawdź dane wyjściowe do [Sprawdź modułu równoważenia obciążenia](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) została poprawnie utworzona, uruchamiając następujące polecenie:

    ```azurecli
    az network lb show --resource-group contosofabrikam --name mylb
    ```

10. [Tworzenie publicznego adresu IP](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-public-ip-address), *myPublicIp*, i [konta magazynu](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json), *mystorageaccont1* dla pierwszej maszyny wirtualnej maszyny VM1 w następujący sposób:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP --domain-name-label mypublicdns345 --allocation-method Dynamic

    az storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount1
    ```

11. [Tworzenie interfejsów sieciowych](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-nic) maszyny vm1 i Dodaj druga Konfiguracja protokołu IP *VM1 ipconfig2*, i [utworzyć maszynę Wirtualną](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-vm) w następujący sposób:

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

13. Na koniec należy skonfigurować rekordy zasobów DNS, aby wskazywał adresu IP odpowiedniego frontonu modułu równoważenia obciążenia. Może być hostowanie domen w usłudze Azure DNS. Aby uzyskać więcej informacji o korzystaniu z usługi Azure DNS przy użyciu modułu równoważenia obciążenia, zobacz [przy użyciu usługi Azure DNS z innymi usługami Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o sposobie łączenia usług na platformie Azure w równoważenia obciążenia [przy użyciu usługi równoważenia obciążenia na platformie Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Dowiedz się, jak używać różnych typów dzienników na platformie Azure do zarządzania i rozwiązywanie problemów z modułu równoważenia obciążenia w [Analiza dzienników na potrzeby usługi Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).
