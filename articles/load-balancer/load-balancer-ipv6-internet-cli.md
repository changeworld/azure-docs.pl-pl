---
title: Utwórz publiczny moduł równoważenia obciążenia w przypadku adresu IPv6 - wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć publiczny moduł równoważenia obciążenia w przypadku adresu IPv6 w usłudze Azure Resource Manager przy użyciu wiersza polecenia platformy Azure.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
keywords: Protokół IPv6, usługi równoważenia obciążenia azure, podwójnego stosu, publiczny adres ip, natywnego protokołu ipv6, mobile, iot
ms.assetid: a1957c9c-9c1d-423e-9d5c-d71449bc1f37
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 62f22ccadfabd2f3d6906beb3c241703d4e6383f
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="create-a-public-load-balancer-with-ipv6-in-azure-resource-manager-by-using-azure-cli"></a>Utwórz publiczny moduł równoważenia obciążenia w przypadku adresu IPv6 w usłudze Azure Resource Manager przy użyciu wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> * [Program PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Interfejs wiersza polecenia platformy Azure](load-balancer-ipv6-internet-cli.md)
> * [Szablon](load-balancer-ipv6-internet-template.md)


Usługa Azure Load Balancer to moduł równoważenia obciążenia w warstwie 4 (TCP, UDP). Moduły równoważenia obciążenia zapewnia wysoką dostępność, przekazując przychodzący ruch między wystąpienie usługi działa prawidłowo usług w chmurze lub maszyn wirtualnych w zestawie usługi równoważenia obciążenia. Moduły równoważenia obciążenia można także przedstawić tych usług w wielu portów lub wiele adresów IP lub oba.

## <a name="example-deployment-scenario"></a>Przykładowy scenariusz wdrażania

Na poniższym diagramie przedstawiono rozwiązania, które zostało wdrożone przy użyciu szablonu przykład opisane w tym artykule do równoważenia obciążenia.

![Scenariusz modułu równoważenia obciążenia](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

W tym scenariuszu utworzysz następujących zasobów platformy Azure:

* dwóch maszyn wirtualnych (VM)
* Interfejs sieci wirtualnej, dla każdej maszyny Wirtualnej przy użyciu adresów IPv4 i IPv6 przypisany
* Publiczny moduł równoważenia obciążenia z protokołami IPv4 i IPv6 publicznego adresu IP
* Zestaw dostępności, która zawiera dwie maszyny wirtualne
* dwie reguły równoważenia do mapowania publiczne adresy VIP prywatnej punkty końcowe obciążenia

## <a name="deploy-the-solution-by-using-azure-cli"></a>Wdrażanie rozwiązania przy użyciu wiersza polecenia platformy Azure

Poniższe kroki pokazują, jak utworzyć publiczny moduł równoważenia obciążenia przy użyciu usługi Azure Resource Manager z wiersza polecenia platformy Azure. Usługi Azure Resource Manager można tworzyć i konfigurować każdego obiektu oddzielnie, a następnie umieść je ze sobą w celu utworzenia zasobu.

Aby wdrożyć usługę równoważenia obciążenia, należy utworzyć i skonfigurować następujące obiekty:

* **Konfiguracja IP frontonu**: zawiera publiczne adresy IP dla przychodzącego ruchu sieciowego.
* **Pula adresów zaplecza**: zawiera interfejsów sieciowych (NIC) dla maszyn wirtualnych na odbieranie ruchu w sieci z usługi równoważenia obciążenia.
* **Reguły równoważenia obciążenia**: zawiera reguły, które mapują port publiczny modułu równoważenia obciążenia do portu w puli adresów zaplecza.
* **Reguły NAT dla ruchu przychodzącego**: zawiera reguły translacji adresów adres sieci, które mapują port publiczny modułu równoważenia obciążenia do portu dla określonej maszyny wirtualnej w puli adresów zaplecza.
* **Sondy**: zawiera sondy kondycji, które są używane do sprawdzania dostępności wystąpień maszyn wirtualnych w puli adresów zaplecza.

Aby uzyskać więcej informacji, zobacz artykuł [Azure Resource Manager support for Load Balancer](load-balancer-arm.md) (Obsługa usługi Azure Resource Manager dla modułu równoważenia obciążenia).

## <a name="set-up-your-azure-cli-environment-to-use-azure-resource-manager"></a>Konfigurowanie środowiska wiersza polecenia platformy Azure do użycia usługi Azure Resource Manager

W tym przykładzie uruchomieniu narzędzia wiersza polecenia platformy Azure w oknie poleceń programu PowerShell. Aby zwiększyć czytelność i ponowne użycie, będzie korzystać programu PowerShell dla funkcji obsługi skryptów, nie poleceń cmdlet programu Azure PowerShell.

1. Jeśli po raz pierwszy używasz interfejsu wiersza polecenia Azure, zobacz [Instalowanie i Konfigurowanie interfejsu wiersza polecenia Azure](../cli-install-nodejs.md) i postępuj zgodnie z instrukcjami aż do punktu, w którym można wybrać konto platformy Azure i subskrypcji.

2. Aby włączyć tryb Resource Manager, uruchom **trybie azure config** polecenia:

    ```azurecli
    azure config mode arm
    ```

    Oczekiwane dane wyjściowe:

        info:    New mode is arm

3. Logowanie do platformy Azure i uzyskać listę subskrypcji:

    ```azurecli
    azure login
    ```

4. W wierszu polecenia wprowadź swoje poświadczenia platformy Azure:

    ```azurecli
    azure account list
    ```

5. Wybierz subskrypcję, którego chcesz używać i Zanotuj identyfikator subskrypcji do użycia w następnym kroku.

6. Ustawianie zmiennych środowiska PowerShell do użycia przy użyciu poleceń wiersza polecenia platformy Azure:

    ```powershell
    $subscriptionid = "########-####-####-####-############"  # enter subscription id
    $location = "southcentralus"
    $rgName = "pscontosorg1southctrlus09152016"
    $vnetName = "contosoIPv4Vnet"
    $vnetPrefix = "10.0.0.0/16"
    $subnet1Name = "clicontosoIPv4Subnet1"
    $subnet1Prefix = "10.0.0.0/24"
    $subnet2Name = "clicontosoIPv4Subnet2"
    $subnet2Prefix = "10.0.1.0/24"
    $dnsLabel = "contoso09152016"
    $lbName = "myIPv4IPv6Lb"
    ```

## <a name="create-a-resource-group-a-load-balancer-a-virtual-network-and-subnets"></a>Utwórz grupę zasobów, usługi równoważenia obciążenia sieci wirtualnej i podsieci

1. Utwórz grupę zasobów:

    ```azurecli
    azure group create $rgName $location
    ```

2. Tworzenie modułu równoważenia obciążenia:

    ```azurecli
    $lb = azure network lb create --resource-group $rgname --location $location --name $lbName
    ```

3. Utwórz sieć wirtualną:

    ```azurecli
    $vnet = azure network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix
    ```

4. W tej sieci wirtualnej Utwórz dwie podsieci:

    ```azurecli
    $subnet1 = azure network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
    $subnet2 = azure network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName
    ```

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Tworzenie publicznych adresów IP dla puli frontonu

1. Skonfiguruj zmienne środowiska PowerShell:

    ```powershell
    $publicIpv4Name = "myIPv4Vip"
    $publicIpv6Name = "myIPv6Vip"
    ```

2. Utwórz publiczny adres IP dla puli adresów IP frontonu:

    ```azurecli
    $publicipV4 = azure network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --ip-version IPv4 --allocation-method Dynamic --domain-name-label $dnsLabel
    $publicipV6 = azure network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --ip-version IPv6 --allocation-method Dynamic --domain-name-label $dnsLabel
    ```

    > [!IMPORTANT]
    > Moduł równoważenia obciążenia używa etykieta domeny publicznego adresu IP, jako jego pełną nazwę domeny (FQDN). To zmiana z klasycznym wdrożenia, którego używa usługa w chmurze nazwę FQDN modułu równoważenia obciążenia.
    >
    > W tym przykładzie nazwa FQDN to *contoso09152016.southcentralus.cloudapp.azure.com*.

## <a name="create-front-end-and-back-end-pools"></a>Tworzenie puli frontonu i zaplecza

W tej sekcji utworzysz następujące pule adresów IP:
* Frontonu pula adresów IP, która odbiera przychodzącego ruchu sieciowego w ramach usługi równoważenia obciążenia.
* Puli IP zaplecza, gdzie puli frontonu wysyła ruch sieciowy z równoważeniem obciążenia.

1. Skonfiguruj zmienne środowiska PowerShell:

    ```powershell
    $frontendV4Name = "FrontendVipIPv4"
    $frontendV6Name = "FrontendVipIPv6"
    $backendAddressPoolV4Name = "BackendPoolIPv4"
    $backendAddressPoolV6Name = "BackendPoolIPv6"
    ```

2. Utwórz pulę IP frontonu i skojarzyć go z publicznego adresu IP, który został utworzony w poprzednim kroku i moduł równoważenia obciążenia.

    ```azurecli
    $frontendV4 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-name $publicIpv4Name --lb-name $lbName
    $frontendV6 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-name $publicIpv6Name --lb-name $lbName
    $backendAddressPoolV4 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
    $backendAddressPoolV6 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName
    ```

## <a name="create-the-probe-nat-rules-and-load-balancer-rules"></a>Utwórz sondowania, reguł NAT, a reguły modułu równoważenia obciążenia

W tym przykładzie opisano tworzenie następujących elementów:

* Zasada sondowania, aby sprawdzić połączenie z portem TCP 80 z.
* Regułę NAT do tłumaczenia cały ruch przychodzący na porcie 3389 do portu 3389 protokołu RDP.\*
* Regułę NAT do tłumaczenia cały ruch przychodzący na porcie 3391 do portu 3389 dla protokołu remote desktop protocol (RDP).\*
* reguły modułu równoważenia obciążenia do zrównoważenia cały ruch przychodzący na porcie 80 dla portu 80 adresy w puli zaplecza.

\* Reguły NAT są skojarzone z konkretnym wystąpieniem maszyny wirtualnej związanej z modułem równoważenia obciążenia. Ruch sieciowy przychodzący do portu 3389 są wysyłane do określonej maszyny wirtualnej i port, który jest skojarzony z regułą translatora adresów Sieciowych. Musisz określić protokół (UDP lub TCP) dla reguły NAT. Nie można przypisać obu tych protokołów do tego samego portu.

1. Skonfiguruj zmienne środowiska PowerShell:

    ```powershell
    $probeV4V6Name = "ProbeForIPv4AndIPv6"
    $natRule1V4Name = "NatRule-For-Rdp-VM1"
    $natRule2V4Name = "NatRule-For-Rdp-VM2"
    $lbRule1V4Name = "LBRuleForIPv4-Port80"
    $lbRule1V6Name = "LBRuleForIPv6-Port80"
    ```

2. Utwórz sondy.

    Poniższy przykład tworzy sondowaniem TCP, który umożliwia sprawdzenie połączenia z portem TCP 80 zaplecza co 15 s. Po dwóch kolejnych niepowodzeń oznacza zasób zaplecza jako niedostępny.

    ```azurecli
    $probeV4V6 = azure network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --count 2 --lb-name $lbName
    ```

3. Utwórz reguły NAT ruchu przychodzącego zezwalających na połączenia RDP do zasobów wewnętrznych:

    ```azurecli
    $inboundNatRuleRdp1 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
    $inboundNatRuleRdp2 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
    ```

4. Utwórz reguły modułu równoważenia obciążenia, które przesyłają dane do różnych portów zaplecza, w zależności od fronton, który odebrał żądanie.

    ```azurecli
    $lbruleIPv4 = azure network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-address-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
    $lbruleIPv6 = azure network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-address-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName
    ```

5. Sprawdź ustawienia:

    ```azurecli
    azure network lb show --resource-group $rgName --name $lbName
    ```

    Oczekiwane dane wyjściowe:

        info:    Executing command network lb show
        info:    Looking up the load balancer "myIPv4IPv6Lb"
        data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/pscontosorg1southctrlus09152016/providers/Microsoft.Network/loadBalancers/myIPv4IPv6Lb
        data:    Name                            : myIPv4IPv6Lb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : southcentralus
        data:    Provisioning state              : Succeeded
        data:
        data:    Frontend IP configurations:
        data:    Name             Provisioning state  Private IP allocation  Private IP   Subnet  Public IP
        data:    ---------------  ------------------  ---------------------  -----------  ------  ---------
        data:    FrontendVipIPv4  Succeeded           Dynamic                                     myIPv4Vip
        data:    FrontendVipIPv6  Succeeded           Dynamic                                     myIPv6Vip
        data:
        data:    Probes:
        data:    Name                 Provisioning state  Protocol  Port  Path  Interval  Count
        data:    -------------------  ------------------  --------  ----  ----  --------  -----
        data:    ProbeForIPv4AndIPv6  Succeeded           Tcp       80          15        2
        data:
        data:    Backend Address Pools:
        data:    Name             Provisioning state
        data:    ---------------  ------------------
        data:    BackendPoolIPv4  Succeeded
        data:    BackendPoolIPv6  Succeeded
        data:
        data:    Load Balancing Rules:
        data:    Name                  Provisioning state  Load distribution  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    --------------------  ------------------  -----------------  --------  -------------  ------------  ------------------  -----------------------
        data:    LBRuleForIPv4-Port80  Succeeded           Default            Tcp       80             80            false               4
        data:    LBRuleForIPv6-Port80  Succeeded           Default            Tcp       80             8080          false               4
        data:
        data:    Inbound NAT Rules:
        data:    Name                 Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    -------------------  ------------------  --------  -------------  ------------  ------------------  -----------------------
        data:    NatRule-For-Rdp-VM1  Succeeded           Tcp       3389           3389          false               4
        data:    NatRule-For-Rdp-VM2  Succeeded           Tcp       3391           3389          false               4
        info:    network lb show

## <a name="create-nics"></a>Tworzenie kart sieciowych

Tworzenie kart sieciowych i skojarzyć je z reguł NAT, reguły modułu równoważenia obciążenia i sondy.

1. Skonfiguruj zmienne środowiska PowerShell:

    ```powershell
    $nic1Name = "myIPv4IPv6Nic1"
    $nic2Name = "myIPv4IPv6Nic2"
    $subnet1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet1Name"
    $subnet2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet2Name"
    $backendAddressPoolV4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV4Name"
    $backendAddressPoolV6Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV6Name"
    $natRule1V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule1V4Name"
    $natRule2V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule2V4Name"
    ```

2. Utwórz kartę Sieciową dla każdej zaplecza, a następnie dodaj konfiguracji protokołu IPv6:

    ```azurecli
    $nic1 = azure network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-version "IPv4" --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule1V4Id
    $nic1IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic1Name

    $nic2 = azure network nic create --name $nic2Name --resource-group $rgname --location $location --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule2V4Id
    $nic2IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic2Name
    ```

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>Tworzenie zaplecza zasobów maszyny Wirtualnej, a następnie dołącz poszczególne karty Sieciowe

Aby utworzyć maszyny wirtualne, musi mieć konto magazynu. W przypadku równoważenia obciążenia maszyn wirtualnych muszą być członkami zestawu dostępności. Aby uzyskać więcej informacji na temat tworzenia maszyn wirtualnych, zobacz [tworzenia maszyny Wirtualnej platformy Azure przy użyciu programu PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

1. Skonfiguruj zmienne środowiska PowerShell:

    ```powershell
    $storageAccountName = "ps08092016v6sa0"
    $availabilitySetName = "myIPv4IPv6AvailabilitySet"
    $vm1Name = "myIPv4IPv6VM1"
    $vm2Name = "myIPv4IPv6VM2"
    $nic1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic1Name"
    $nic2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic2Name"
    $disk1Name = "WindowsVMosDisk1"
    $disk2Name = "WindowsVMosDisk2"
    $osDisk1Uri = "https://$storageAccountName.blob.core.windows.net/vhds/$disk1Name.vhd"
    $osDisk2Uri = "https://$storageAccountName.blob.core.windows.net/vhds/$disk2Name.vhd"
    $imageurn "MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest"
    $vmUserName = "vmUser"
    $mySecurePassword = "PlainTextPassword*1"
    ```

    > [!WARNING]
    > W tym przykładzie używa nazwy użytkownika i hasła dla maszyn wirtualnych w zwykłym tekstem. Zwróć uwagę na odpowiednie, korzystając z tych poświadczeń jako zwykły tekst. Aby uzyskać bardziej bezpieczne metody obsługi poświadczenia w programie PowerShell, zobacz [ `Get-Credential` ](https://technet.microsoft.com/library/hh849815.aspx) polecenia cmdlet.

2. Utwórz zestaw dostępności i konto magazynu.

    Podczas tworzenia maszyn wirtualnych, można użyć istniejącego konta magazynu. Możesz utworzyć nowe konto magazynu przy użyciu następującego polecenia:

    ```azurecli
    $storageAcc = azure storage account create $storageAccountName --resource-group $rgName --location $location --sku-name "LRS" --kind "Storage"
    ```

3. Utwórz zestaw dostępności:

    ```azurecli
    $availabilitySet = azure availset create --name $availabilitySetName --resource-group $rgName --location $location
    ```

4. Tworzenie maszyn wirtualnych z skojarzone kartami sieciowymi:

    ```azurecli
    $vm1 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm1Name --nic-id $nic1Id --os-disk-vhd $osDisk1Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn --storage-account-name $storageAccountName --disable-bginfo-extension

    $vm2 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm2Name --nic-id $nic2Id --os-disk-vhd $osDisk2Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn --storage-account-name $storageAccountName --disable-bginfo-extension
    ```

## <a name="next-steps"></a>Kolejne kroki

[Get started configuring an internal load balancer](load-balancer-get-started-ilb-arm-cli.md) (Wprowadzenie do konfigurowania wewnętrznego modułu równoważenia obciążenia)  
[Configure a load balancer distribution mode](load-balancer-distribution-mode.md) (Konfigurowanie trybu dystrybucji modułu równoważenia obciążenia)  
[Configure idle TCP timeout settings for your load balancer](load-balancer-tcp-idle-timeout.md) (Konfigurowanie ustawień limitu czasu bezczynności protokołu TCP dla modułu równoważenia obciążenia)
