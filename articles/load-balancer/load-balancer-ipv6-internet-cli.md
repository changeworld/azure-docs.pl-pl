---
title: Tworzenie publicznego modułu równoważenia obciążenia przy użyciu protokołu IPv6 — interfejs wiersza polecenia platformy Azure
titlesuffix: Azure Load Balancer
description: Dowiedz się, jak utworzyć publiczny moduł równoważenia obciążenia przy użyciu protokołu IPv6 przy użyciu wiersza polecenia platformy Azure.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: Protokół IPv6, usługa azure load balancer, podwójnego stosu, publiczny adres ip, natywnego protokołu ipv6, aplikację mobilną, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: allensu
ms.openlocfilehash: 0ee85a92753845e0e67fff22da894a048acb1b14
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274952"
---
# <a name="create-a-public-load-balancer-with-ipv6-using-azure-cli"></a>Tworzenie publicznego modułu równoważenia obciążenia przy użyciu protokołu IPv6 przy użyciu wiersza polecenia platformy Azure


Usługa Azure Load Balancer to moduł równoważenia obciążenia w warstwie 4 (TCP, UDP). Moduły równoważenia obciążenia zapewniają wysoką dostępność, rozkładając ruch przychodzący między wystąpieniami usług w dobrej kondycji w usługach w chmurze i maszyn wirtualnych w zestawie modułu równoważenia obciążenia. Moduły równoważenia obciążenia może także prezentować te usługi w wielu portów lub wiele adresów IP lub obu.

## <a name="example-deployment-scenario"></a>Przykładowy scenariusz wdrażania

Na poniższym diagramie przedstawiono rozwiązanie, które zostało wdrożone przy użyciu przykładowy szablon opisanych w tym artykule do równoważenia obciążenia.

![Scenariusz modułu równoważenia obciążenia](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

W tym scenariuszu utworzysz następujących zasobów platformy Azure:

* Dwie maszyny wirtualne (VM)
* Interfejs sieci wirtualnej, dla każdej maszyny Wirtualnej przy użyciu adresów IPv4 i IPv6 przypisany
* Publiczny moduł równoważenia obciążenia przy użyciu adresu IPv4 i IPv6 publiczny adres IP
* Zestawu dostępności zawierającego dwie maszyny wirtualne
* Dwa obciążenia równoważenia reguł mapowania publicznych adresów VIP do prywatnych punktów końcowych

## <a name="deploy-the-solution-by-using-azure-cli"></a>Wdrażanie rozwiązania przy użyciu wiersza polecenia platformy Azure

Poniższe kroki pokazują jak utworzyć publiczny moduł równoważenia obciążenia przy użyciu wiersza polecenia platformy Azure. Przy użyciu interfejsu wiersza polecenia, należy utworzyć, jak i skonfigurować każdy obiekt indywidualnie, a następnie łączyć je ze sobą, aby utworzyć zasób.

Aby wdrożyć moduł równoważenia obciążenia, należy utworzyć i skonfigurować następujące obiekty:

* **Konfiguracja adresu IP frontonu**: Zawiera publiczne adresy IP dla przychodzącego ruchu sieciowego.
* **Pula adresów zaplecza**: Zawiera interfejsy sieciowe (nic), dla których maszyny wirtualne mają odbierać ruch sieciowy z modułu równoważenia obciążenia.
* **Reguły równoważenia obciążenia**: Zawiera reguły, które mapują port publiczny modułu równoważenia obciążenia na port w puli adresów zaplecza.
* **Reguły NAT dla ruchu przychodzącego**: Zawiera reguły translacji adresów sieciowych (NAT), które mapują port publiczny modułu równoważenia obciążenia na port określonej maszyny wirtualnej w puli adresów zaplecza.
* **Sondy**: Zawiera sondy kondycji, które są używane do sprawdzania dostępności wystąpień maszyn wirtualnych w puli adresów zaplecza.

## <a name="set-up-azure-cli"></a>Konfigurowanie interfejsu wiersza polecenia platformy Azure

W tym przykładzie uruchomieniu narzędzia wiersza polecenia platformy Azure w okno poleceń programu PowerShell. Aby zwiększyć czytelność i ponownego użycia, należy użyć możliwości obsługi skryptów programu PowerShell, nie poleceń cmdlet programu Azure PowerShell.

1. [Instalowanie i Konfigurowanie interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) wykonując kroki opisane w artykule połączone i zaloguj się do konta platformy Azure.

2. Skonfiguruj zmienne programu PowerShell do użycia z poleceniami wiersza polecenia platformy Azure:

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

## <a name="create-a-resource-group-a-load-balancer-a-virtual-network-and-subnets"></a>Utwórz grupę zasobów, moduł równoważenia obciążenia, sieci wirtualnej i podsieci

1. Utwórz grupę zasobów:

    ```azurecli
    az group create --name $rgName --location $location
    ```

2. Utwórz moduł równoważenia obciążenia:

    ```azurecli
    $lb = az network lb create --resource-group $rgname --location $location --name $lbName
    ```

3. Utwórz sieć wirtualną:

    ```azurecli
    $vnet = az network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix
    ```

4. W tej sieci wirtualnej Utwórz dwie podsieci:

    ```azurecli
    $subnet1 = az network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
    $subnet2 = az network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName
    ```

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Utwórz publicznych adresów IP dla puli frontonu

1. Skonfiguruj zmienne programu PowerShell:

    ```powershell
    $publicIpv4Name = "myIPv4Vip"
    $publicIpv6Name = "myIPv6Vip"
    ```

2. Utwórz publiczny adres IP dla puli adresów IP frontonu:

    ```azurecli
    $publicipV4 = az network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --version IPv4 --allocation-method Dynamic --dns-name $dnsLabel
    $publicipV6 = az network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --version IPv6 --allocation-method Dynamic --dns-name $dnsLabel
    ```

    > [!IMPORTANT]
    > Moduł równoważenia obciążenia używa etykiety domeny publicznego adresu IP jako jego w pełni kwalifikowaną nazwę domeny (FQDN). Ta zmiana z wdrożenia klasycznego, które wykorzystuje usługę w chmurze nazwa jako nazwę FQDN modułu równoważenia obciążenia.
    >
    > W tym przykładzie FQDN to *contoso09152016.southcentralus.cloudapp.azure.com*.

## <a name="create-front-end-and-back-end-pools"></a>Tworzenie puli frontonu i zaplecza

W tej sekcji utworzysz następujące pule adresów IP:
* Puli adresów IP frontonu, który odbiera przychodzący ruch sieciowy w module równoważenia obciążenia.
* Pula adresów IP zaplecza, gdzie pula frontonu wysyła ruch sieciowy ze zrównoważonym obciążeniem.

1. Skonfiguruj zmienne programu PowerShell:

    ```powershell
    $frontendV4Name = "FrontendVipIPv4"
    $frontendV6Name = "FrontendVipIPv6"
    $backendAddressPoolV4Name = "BackendPoolIPv4"
    $backendAddressPoolV6Name = "BackendPoolIPv6"
    ```

2. Utwórz pulę adresów IP frontonu i skojarz ją z publicznym adresem IP, który został utworzony w poprzednim kroku oraz moduł równoważenia obciążenia.

    ```azurecli
    $frontendV4 = az network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-address $publicIpv4Name --lb-name $lbName
    $frontendV6 = az network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-address $publicIpv6Name --lb-name $lbName
    $backendAddressPoolV4 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
    $backendAddressPoolV6 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName
    ```

## <a name="create-the-probe-nat-rules-and-load-balancer-rules"></a>Tworzenie sondy, reguły NAT i reguły modułu równoważenia obciążenia

W tym przykładzie opisano tworzenie następujących elementów:

* Reguła sondy do sprawdzania łączności na porcie 80 protokołu TCP.
* Reguła NAT do translacji całego ruchu przychodzącego na porcie 3389 na port 3389 dla protokołu RDP.\*
* Reguła NAT do translacji całego ruchu przychodzącego na porcie 3391 do portu 3389 dla protokołu remote desktop protocol (RDP).\*
* Reguła modułu równoważenia obciążenia do równoważenia całego ruchu przychodzącego na porcie 80 na port 80 adresów w puli zaplecza.

\* Reguły NAT są powiązane z konkretnym wystąpieniem maszyny wirtualnej za modułem równoważenia obciążenia. Ruch sieciowy przychodzący do portu 3389 są wysyłane do określonej maszyny wirtualnej i port, który jest skojarzony z regułą translatora adresów Sieciowych. Musisz określić protokół (UDP lub TCP) dla reguły NAT. Nie można przypisać obu protokołów do tego samego portu.

1. Skonfiguruj zmienne programu PowerShell:

    ```powershell
    $probeV4V6Name = "ProbeForIPv4AndIPv6"
    $natRule1V4Name = "NatRule-For-Rdp-VM1"
    $natRule2V4Name = "NatRule-For-Rdp-VM2"
    $lbRule1V4Name = "LBRuleForIPv4-Port80"
    $lbRule1V6Name = "LBRuleForIPv6-Port80"
    ```

2. Utwórz sondę.

    Poniższy przykład tworzy sondę TCP, który umożliwia sprawdzenie łączności z portem TCP 80 zaplecza co 15 sekund. Po dwóch kolejnych niepowodzeń oznacza zasobów zaplecza jako niedostępny.

    ```azurecli
    $probeV4V6 = az network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --threshold 2 --lb-name $lbName
    ```

3. Tworzenie reguł ruchu przychodzącego translatora adresów Sieciowych, zezwalających na połączenia RDP z zasobami zaplecza:

    ```azurecli
    $inboundNatRuleRdp1 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
    $inboundNatRuleRdp2 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
    ```

4. Tworzenie reguły modułu równoważenia obciążenia, które przesyłać dane do innych portów zaplecza, w zależności od frontonu, który odebrał żądanie.

    ```azurecli
    $lbruleIPv4 = az network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
    $lbruleIPv6 = az network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName
    ```

5. Sprawdź ustawienia:

    ```azurecli
    az network lb show --resource-group $rgName --name $lbName
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

Utwórz karty sieciowe i skojarzyć je z sond, reguł usługi load balancer i reguł translatora adresów Sieciowych.

1. Skonfiguruj zmienne programu PowerShell:

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

2. Utwórz kartę Sieciową dla każdego zaplecza, a następnie dodaj konfiguracji protokołu IPv6:

    ```azurecli
    $nic1 = az network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet1Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule1V4Id
    $nic1IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic1Name

    $nic2 = az network nic create --name $nic2Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet2Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule2V4Id
    $nic2IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic2Name
    ```

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>Tworzenie zasobów maszyny Wirtualnej zaplecza, a następnie Dołącz każdą kartę Sieciową

Aby utworzyć maszyny wirtualne, musi mieć konto magazynu. W przypadku równoważenia obciążenia maszyn wirtualnych muszą być elementów członkowskich zestawu dostępności. Aby uzyskać więcej informacji na temat tworzenia maszyn wirtualnych, zobacz [Utwórz Maszynę wirtualną platformy Azure przy użyciu programu PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

1. Skonfiguruj zmienne programu PowerShell:

    ```powershell
    $availabilitySetName = "myIPv4IPv6AvailabilitySet"
    $vm1Name = "myIPv4IPv6VM1"
    $vm2Name = "myIPv4IPv6VM2"
    $nic1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic1Name"
    $nic2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic2Name"
    $imageurn = "MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest"
    $vmUserName = "vmUser"
    $mySecurePassword = "PlainTextPassword*1"
    ```

    > [!WARNING]
    > W tym przykładzie używa nazwy użytkownika i hasła dla maszyn wirtualnych w formie zwykłego tekstu. Należy zwrócić uwagę odpowiednie, korzystając z tych poświadczeń w formie zwykłego tekstu. Aby uzyskać bardziej bezpieczną metodą obsługi poświadczenia w programie PowerShell, zobacz [ `Get-Credential` ](https://technet.microsoft.com/library/hh849815.aspx) polecenia cmdlet.

2. Tworzenie zestawu dostępności:

    ```azurecli
    $availabilitySet = az vm availability-set create --name $availabilitySetName --resource-group $rgName --location $location
    ```

3. Tworzenie maszyn wirtualnych za pomocą skojarzonego kart sieciowych:

    ```azurecli
    az vm create --resource-group $rgname --name $vm1Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic1Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 

    az vm create --resource-group $rgname --name $vm2Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic2Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 
    ```

## <a name="next-steps"></a>Kolejne kroki

[Get started configuring an internal load balancer](load-balancer-get-started-ilb-arm-cli.md) (Wprowadzenie do konfigurowania wewnętrznego modułu równoważenia obciążenia)  
[Configure a load balancer distribution mode](load-balancer-distribution-mode.md) (Konfigurowanie trybu dystrybucji modułu równoważenia obciążenia)  
[Configure idle TCP timeout settings for your load balancer](load-balancer-tcp-idle-timeout.md) (Konfigurowanie ustawień limitu czasu bezczynności protokołu TCP dla modułu równoważenia obciążenia)
