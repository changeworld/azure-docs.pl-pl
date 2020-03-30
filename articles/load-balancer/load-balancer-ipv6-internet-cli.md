---
title: Tworzenie publicznego modułu równoważenia obciążenia za pomocą usługi IPv6 — interfejsu wiersza polecenia platformy Azure
titleSuffix: Azure Load Balancer
description: Dzięki tej ścieżce szkoleniowej rozpocznij tworzenie publicznego modułu równoważenia obciążenia za pomocą iPv6 przy użyciu interfejsu wiersza polecenia platformy Azure.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: ipv6, lazurowy moduł równoważenia obciążenia, podwójny stos, publiczny adres IP, natywny ipv6, mobilny, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: allensu
ms.openlocfilehash: bff6a7ca6eb1a6859ec25d488f564c66946a780b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045414"
---
# <a name="create-a-public-load-balancer-with-ipv6-using-azure-cli"></a>Tworzenie publicznego modułu równoważenia obciążenia za pomocą funkcji IPv6 przy użyciu interfejsu wiersza polecenia platformy Azure

>[!NOTE] 
>W tym artykule opisano funkcję wprowadzającą IPv6, która umożliwia podstawowe moduły równoważenia obciążenia w celu zapewnienia łączności IPv4 i IPv6. Kompleksowa łączność IPv6 jest teraz dostępna z [technologiami wirtualnymi IPv6 dla platformy Azure,](../virtual-network/ipv6-overview.md) które integrują łączność IPv6 z sieciami wirtualnymi i zawierają najważniejsze funkcje, takie jak reguły sieciowej grupy zabezpieczeń IPv6, routing zdefiniowany przez użytkownika IPv6, podstawowe i standardowe równoważenie obciążenia IPv6 i inne.  IPv6 dla platformy Azure VNETs jest zalecanym standardem dla aplikacji IPv6 na platformie Azure. Zobacz [IPv6 dla wdrażania sieci wirtualnej sieci wirtualnej platformy Azure](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) 

Usługa Azure Load Balancer to moduł równoważenia obciążenia w warstwie 4 (TCP, UDP). Moduły równoważenia obciążenia zapewniają wysoką dostępność, rozdzielając ruch przychodzący między wystąpienia usług w dobrej kondycji w usługach w chmurze lub maszynach wirtualnych w zestawie modułu równoważenia obciążenia. Moduły równoważenia obciążenia mogą również prezentować te usługi na wielu portach lub wielu adresach IP lub obu.

## <a name="example-deployment-scenario"></a>Przykładowy scenariusz wdrażania

Na poniższym diagramie przedstawiono rozwiązanie równoważenia obciążenia, które jest wdrażane przy użyciu przykładowego szablonu opisanego w tym artykule.

![Scenariusz modułu równoważenia obciążenia](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

W tym scenariuszu utworzysz następujące zasoby platformy Azure:

* Dwie maszyny wirtualne (maszyny wirtualne)
* Interfejs sieci wirtualnej dla każdej maszyny Wirtualnej z przypisanymi adresami IPv4 i IPv6
* Publiczny moduł równoważenia obciążenia z iPv4 i publicznym adresem IP IPv6
* Zestaw dostępności zawierający dwie maszyny wirtualne
* Dwie reguły równoważenia obciążenia w celu mapowania publicznych adresów VIP do prywatnych punktów końcowych

## <a name="deploy-the-solution-by-using-azure-cli"></a>Wdrażanie rozwiązania przy użyciu interfejsu wiersza polecenia platformy Azure

Poniższe kroki pokazują, jak utworzyć moduł równoważenia obciążenia publicznego przy użyciu interfejsu wiersza polecenia platformy Azure. Za pomocą interfejsu wiersza polecenia, należy utworzyć i skonfigurować każdy obiekt indywidualnie, a następnie umieścić je razem, aby utworzyć zasób.

Aby wdrożyć moduł równoważenia obciążenia, należy utworzyć i skonfigurować następujące obiekty:

* **Konfiguracja ip front-endu:** Zawiera publiczne adresy IP dla przychodzącego ruchu sieciowego.
* **Pula adresów zaplecza:** Zawiera interfejsy sieciowe (NIC) dla maszyn wirtualnych do odbierania ruchu sieciowego z modułu równoważenia obciążenia.
* **Reguły równoważenia obciążenia:** Zawiera reguły mapujące port publiczny na modułze równoważenia obciążenia do portu w puli adresów zaplecza.
* **Przychodzące reguły TRANSLATORA**: Zawiera reguły translacji adresów sieciowych (NAT), które mapują port publiczny modułu równoważenia obciążenia na port dla określonej maszyny wirtualnej w puli adresów zaplecza.
* **Sondy:** Zawiera sondy kondycji, które są używane do sprawdzania dostępności wystąpień maszyny wirtualnej w puli adresów zaplecza.

## <a name="set-up-azure-cli"></a>Konfigurowanie interfejsu wiersza polecenia platformy Azure

W tym przykładzie należy uruchomić narzędzia interfejsu wiersza polecenia platformy Azure w oknie polecenia programu PowerShell. Aby poprawić czytelność i ponowne użycie, należy użyć możliwości skryptów programu PowerShell, a nie poleceń cmdlet programu Azure PowerShell.

1. [Zainstaluj i skonfiguruj interfejsu wiersza polecenia platformy Azure,](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) wykonując kroki opisane w połączonym artykule i zaloguj się do konta platformy Azure.

2. Konfigurowanie zmiennych programu PowerShell do użycia z poleceniami interfejsu wiersza polecenia platformy Azure:

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

## <a name="create-a-resource-group-a-load-balancer-a-virtual-network-and-subnets"></a>Tworzenie grupy zasobów, modułu równoważenia obciążenia, sieci wirtualnej i podsieci

1. Utwórz grupę zasobów:

    ```azurecli
    az group create --name $rgName --location $location
    ```

2. Tworzenie modułu równoważenia obciążenia:

    ```azurecli
    $lb = az network lb create --resource-group $rgname --location $location --name $lbName
    ```

3. Utwórz sieć wirtualną:

    ```azurecli
    $vnet = az network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix
    ```

4. W tej sieci wirtualnej utwórz dwie podsieci:

    ```azurecli
    $subnet1 = az network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
    $subnet2 = az network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName
    ```

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Tworzenie publicznych adresów IP dla puli front-end

1. Konfigurowanie zmiennych programu PowerShell:

    ```powershell
    $publicIpv4Name = "myIPv4Vip"
    $publicIpv6Name = "myIPv6Vip"
    ```

2. Utwórz publiczny adres IP dla puli adresów IP front-end:

    ```azurecli
    $publicipV4 = az network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --version IPv4 --allocation-method Dynamic --dns-name $dnsLabel
    $publicipV6 = az network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --version IPv6 --allocation-method Dynamic --dns-name $dnsLabel
    ```

    > [!IMPORTANT]
    > Moduł równoważenia obciążenia używa etykiety domeny publicznego adresu IP jako w pełni kwalifikowanej nazwy domeny (FQDN). Ta zmiana w stosunku do wdrożenia klasycznego, który używa nazwy usługi w chmurze jako modułu FQDN modułu równoważenia obciążenia.
    >
    > W tym przykładzie FQDN jest *contoso09152016.southcentralus.cloudapp.azure.com*.

## <a name="create-front-end-and-back-end-pools"></a>Tworzenie pul front-end i back-end

W tej sekcji utworzysz następujące pule adresów IP:
* Pula adresów IP frontowaru, która odbiera przychodzący ruch sieciowy na moduł równoważenia obciążenia.
* Pulę adresów IP zaplecza, w której pula frontowa wysyła ruch sieciowy z równoważenia obciążenia.

1. Konfigurowanie zmiennych programu PowerShell:

    ```powershell
    $frontendV4Name = "FrontendVipIPv4"
    $frontendV6Name = "FrontendVipIPv6"
    $backendAddressPoolV4Name = "BackendPoolIPv4"
    $backendAddressPoolV6Name = "BackendPoolIPv6"
    ```

2. Utwórz pulę adresów IP frontu i skojarz ją z publicznym adresem IP utworzonym w poprzednim kroku i modułem równoważenia obciążenia.

    ```azurecli
    $frontendV4 = az network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-address $publicIpv4Name --lb-name $lbName
    $frontendV6 = az network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-address $publicIpv6Name --lb-name $lbName
    $backendAddressPoolV4 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
    $backendAddressPoolV6 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName
    ```

## <a name="create-the-probe-nat-rules-and-load-balancer-rules"></a>Tworzenie reguł sondy, translatora nat i modułu równoważenia obciążenia

W tym przykładzie opisano tworzenie następujących elementów:

* Reguła sondowania w celu sprawdzenia łączności z portem TCP 80.
* Reguła NAT, aby przetłumaczyć cały ruch przychodzący na porcie 3389 do portu 3389 dla protokołu RDP.\*
* Reguła TRANSLATORA, aby przetłumaczyć cały ruch przychodzący na porcie 3391 na port 3389 dla protokołu pulpitu zdalnego (RDP).\*
* Reguła modułu równoważenia całego ruchu przychodzącego na porcie 80 do portu 80 na adresach w puli zaplecza.

\*Reguły translatora adresów sieciowych są skojarzone z określonym wystąpieniem maszyny wirtualnej za modułem równoważenia obciążenia. Ruch sieciowy docierany do portu 3389 jest wysyłany do określonej maszyny wirtualnej i portu skojarzonego z regułą nat. Musisz określić protokół (UDP lub TCP) dla reguły NAT. Nie można przypisać obu protokołów do tego samego portu.

1. Konfigurowanie zmiennych programu PowerShell:

    ```powershell
    $probeV4V6Name = "ProbeForIPv4AndIPv6"
    $natRule1V4Name = "NatRule-For-Rdp-VM1"
    $natRule2V4Name = "NatRule-For-Rdp-VM2"
    $lbRule1V4Name = "LBRuleForIPv4-Port80"
    $lbRule1V6Name = "LBRuleForIPv6-Port80"
    ```

2. Utwórz sondę.

    Poniższy przykład tworzy sondę TCP, która sprawdza łączność z portem TCP zaplecza 80 co 15 sekund. Po dwóch kolejnych błędów oznacza zasób zaplecza jako niedostępny.

    ```azurecli
    $probeV4V6 = az network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --threshold 2 --lb-name $lbName
    ```

3. Tworzenie reguł przychodzącego translatora i sieci kontaktów, które zezwalają na połączenia protokołu RDP z zasobami zaplecza:

    ```azurecli
    $inboundNatRuleRdp1 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
    $inboundNatRuleRdp2 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
    ```

4. Utwórz reguły modułu równoważenia obciążenia, które wysyłają ruch do różnych portów zaplecza, w zależności od frontu, który odebrał żądanie.

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

Tworzenie kart sieciowych i kojarzenie ich z regułami nat, regułami równoważenia obciążenia i sondami.

1. Konfigurowanie zmiennych programu PowerShell:

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

2. Utwórz kartę sieciową dla każdego zaplecza i dodaj konfigurację IPv6:

    ```azurecli
    $nic1 = az network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet1Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule1V4Id
    $nic1IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic1Name

    $nic2 = az network nic create --name $nic2Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet2Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule2V4Id
    $nic2IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic2Name
    ```

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>Tworzenie zasobów zaplecza maszyny Wirtualnej i dołączanie każdej karty sieciowej

Aby utworzyć maszyny wirtualne, musisz mieć konto magazynu. W przypadku równoważenia obciążenia maszyny wirtualne muszą być członkami zestawu dostępności. Aby uzyskać więcej informacji na temat tworzenia maszyn wirtualnych, zobacz [Tworzenie maszyny wirtualnej platformy Azure przy użyciu programu PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

1. Konfigurowanie zmiennych programu PowerShell:

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
    > W tym przykładzie użyto nazwy użytkownika i hasła dla maszyn wirtualnych w cleartext. Należy pamiętać, korzystając z tych poświadczeń w cleartext. Aby uzyskać bezpieczniejszą metodę obsługi poświadczeń [`Get-Credential`](https://technet.microsoft.com/library/hh849815.aspx) w programie PowerShell, zobacz polecenie cmdlet.

2. Utwórz zestaw dostępności:

    ```azurecli
    $availabilitySet = az vm availability-set create --name $availabilitySetName --resource-group $rgName --location $location
    ```

3. Tworzenie maszyn wirtualnych przy tym powiązanych kart sieciowych:

    ```azurecli
    az vm create --resource-group $rgname --name $vm1Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic1Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 

    az vm create --resource-group $rgname --name $vm2Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic2Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 
    ```


