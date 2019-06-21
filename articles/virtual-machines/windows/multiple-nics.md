---
title: Tworzenie i zarządzanie nimi Windows maszyn wirtualnych na platformie Azure korzystające z wieloma kartami sieciowymi | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć i zarządzać nimi Windows maszyny Wirtualnej, która ma wiele kart sieciowych dołączonych do niej przy użyciu szablonów programu Azure PowerShell lub Menedżera zasobów.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: 9bff5b6d-79ac-476b-a68f-6f8754768413
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: cynthn
ms.openlocfilehash: d6387fc0f2f7342eb7a1fda25fcf1272889d13f7
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "64698144"
---
# <a name="create-and-manage-a-windows-virtual-machine-that-has-multiple-nics"></a>Tworzenie i zarządzanie nimi maszynę wirtualną Windows, który ma wiele kart sieciowych
Maszyny wirtualne (VM) na platformie Azure może mieć wiele wirtualnych kart sieciowych (NIC) dołączonych do nich. Typowym scenariuszem jest zapewnienie różnych podsieci frontonu i zaplecza łączności. Można skojarzyć wiele kart sieciowych na maszynie Wirtualnej z wieloma podsieciami, ale te podsieci wszystkie znajdować się w tej samej sieci wirtualnej (vNet). Ten artykuł szczegółowo opisuje sposób tworzenia maszyny Wirtualnej, która ma wiele kart sieciowych dołączonych do niego. Poznasz również sposób dodawania lub usuwania karty sieciowe z istniejącej maszyny Wirtualnej. Różne [rozmiarów maszyn wirtualnych](sizes.md) obsługi różną liczbę kart sieciowych, więc odpowiednio rozmiaru maszyny Wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne

W poniższych przykładach należy zastąpić własnymi wartościami przykładowe nazwy parametru. Przykładowe nazwy parametru zawierają *myResourceGroup*, *myVnet*, i *myVM*.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="create-a-vm-with-multiple-nics"></a>Tworzenie maszyny wirtualnej z wieloma kartami sieciowymi
Najpierw utwórz grupę zasobów. Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w *EastUs* lokalizacji:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

### <a name="create-virtual-network-and-subnets"></a>Tworzenie sieci wirtualnej i podsieci
Jest to typowy scenariusz dla sieci wirtualnej dwa lub więcej podsieci. Może być jedną podsieć frontonu ruchu, a drugą dla ruchu zaplecza. Aby połączyć obie podsieci, następnie użyć wielu kart sieciowych na maszynie Wirtualnej.

1. Zdefiniuj dwie podsieci sieci wirtualnej za pomocą [New AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig). W poniższym przykładzie zdefiniowano podsieci dla *mySubnetFrontEnd* i *mySubnetBackEnd*:

    ```powershell
    $mySubnetFrontEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
        -AddressPrefix "192.168.1.0/24"
    $mySubnetBackEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
        -AddressPrefix "192.168.2.0/24"
    ```

2. Tworzenie sieci wirtualnej i podsieci z [New AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork). Poniższy przykład tworzy sieć wirtualną o nazwie *myVnet*:

    ```powershell
    $myVnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "EastUs" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
    ```


### <a name="create-multiple-nics"></a>Utwórz wiele kart sieciowych
Utwórz dwie karty sieciowe z [New AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface). Dołącz jedną kartę Sieciową do podsieci frontonu i jedną kartę Sieciową do podsieci zaplecza. Poniższy przykład obejmuje tworzenie karty sieciowe o nazwie *myNic1* i *myNic2*:

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic1" `
    -Location "EastUs" `
    -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic2" `
    -Location "EastUs" `
    -SubnetId $backEnd.Id
```

Zazwyczaj można również utworzyć [sieciowej grupy zabezpieczeń](../../virtual-network/security-overview.md) do filtrowania ruchu sieciowego do maszyny Wirtualnej i [moduł równoważenia obciążenia](../../load-balancer/load-balancer-overview.md) do rozdzielenia ruchu między wieloma maszynami wirtualnymi.

### <a name="create-the-virtual-machine"></a>Tworzenie maszyny wirtualnej
Teraz rozpocząć tworzenie konfiguracji maszyny Wirtualnej. Rozmiar każdej maszyny Wirtualnej jest objęta limitem całkowitą liczbę kart sieciowych, które można dodać do maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [rozmiarów maszyn wirtualnych Windows](sizes.md).

1. Ustaw poświadczenia maszyny Wirtualnej `$cred` zmiennej w następujący sposób:

    ```powershell
    $cred = Get-Credential
    ```

2. Definiowanie przy użyciu [nowe AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig). W poniższym przykładzie zdefiniowano Maszynę wirtualną o nazwie *myVM* i używa rozmiaru maszyny Wirtualnej, która obsługuje więcej niż dwie karty sieciowe (*standardowa_ds3_v2*):

    ```powershell
    $vmConfig = New-AzVMConfig -VMName "myVM" -VMSize "Standard_DS3_v2"
    ```

3. Utworzyć pozostałą część konfiguracji maszyny Wirtualnej za pomocą [AzVMOperatingSystem zestaw](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) i [AzVMSourceImage zestaw](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage). Poniższy przykład tworzy maszynę Wirtualną z systemem Windows Server 2016:

    ```powershell
    $vmConfig = Set-AzVMOperatingSystem -VM $vmConfig `
        -Windows `
        -ComputerName "myVM" `
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
    $vmConfig = Set-AzVMSourceImage -VM $vmConfig `
        -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" `
        -Skus "2016-Datacenter" `
        -Version "latest"
   ```

4. Dołącz dwie karty sieciowe, które wcześniej utworzone za pomocą [AzVMNetworkInterface Dodaj](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface):

    ```powershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
    ```

5. Utwórz maszynę Wirtualną za pomocą [nowe AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm):

    ```powershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "EastUs"
    ```

6. Dodawanie tras dla pomocniczych kartach sieciowych do systemu operacyjnego, wykonując kroki opisane w [konfiguracji systemu operacyjnego dla wielu kart sieciowych](#configure-guest-os-for-multiple-nics).

## <a name="add-a-nic-to-an-existing-vm"></a>Dodaj kartę Sieciową do istniejącej maszyny Wirtualnej
Aby dodać wirtualną kartę Sieciową do istniejącej maszyny Wirtualnej, cofany jest przydział maszyny Wirtualnej, dodać wirtualnej karty Sieciowej, a następnie uruchom maszynę Wirtualną. Różne [rozmiarów maszyn wirtualnych](sizes.md) obsługi różną liczbę kart sieciowych, więc odpowiednio rozmiaru maszyny Wirtualnej. Jeśli to konieczne, możesz [zmienić rozmiar maszyny Wirtualnej](resize-vm.md).

1. Cofnij Przydział maszyny Wirtualnej przy użyciu [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm). Poniższy przykład powoduje cofnięcie przydziału maszyny Wirtualnej o nazwie *myVM* w *myResourceGroup*:

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Pobieranie istniejącej konfiguracji maszyny Wirtualnej przy użyciu [Get-AzVm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm). Poniższy przykład pobiera informacje dotyczące maszyny Wirtualnej o nazwie *myVM* w *myResourceGroup*:

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. Poniższy przykład tworzy wirtualną kartę Sieciową z [New AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) o nazwie *myNic3* dołączona do *mySubnetBackEnd*. Wirtualna karta sieciowa jest następnie dołączony do maszyny Wirtualnej o nazwie *myVM* w *myResourceGroup* z [AzVMNetworkInterface Dodaj](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface):

    ```powershell
    # Get info for the back end subnet
    $myVnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
    $backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}

    # Create a virtual NIC
    $myNic3 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic3" `
        -Location "EastUs" `
        -SubnetId $backEnd.Id

    # Get the ID of the new virtual NIC and add to VM
    $nicId = (Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" -Name "MyNic3").Id
    Add-AzVMNetworkInterface -VM $vm -Id $nicId | Update-AzVm -ResourceGroupName "myResourceGroup"
    ```

    ### <a name="primary-virtual-nics"></a>Podstawowe wirtualne karty sieciowe
    Jedną z kart sieciowych na maszynie Wirtualnej z wieloma kartami musi być podstawowy. Jeśli jeden z istniejących wirtualnych kart sieciowych na maszynie Wirtualnej jest już ustawiony jako podstawowy, możesz pominąć ten krok. W poniższym przykładzie założono, że dwie wirtualne karty sieciowe są się teraz na maszynie Wirtualnej i chcesz dodać pierwszej karty Sieciowej (`[0]`) jako podstawowy:
        
    ```powershell
    # List existing NICs on the VM and find which one is primary
    $vm.NetworkProfile.NetworkInterfaces
    
    # Set NIC 0 to be primary
    $vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
    $vm.NetworkProfile.NetworkInterfaces[1].Primary = $false
    
    # Update the VM state in Azure
    Update-AzVM -VM $vm -ResourceGroupName "myResourceGroup"
    ```

4. Uruchom maszynę Wirtualną za pomocą [Start-AzVm](https://docs.microsoft.com/powershell/module/az.compute/start-azvm):

    ```powershell
    Start-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

5. Dodawanie tras dla pomocniczych kartach sieciowych do systemu operacyjnego, wykonując kroki opisane w [konfiguracji systemu operacyjnego dla wielu kart sieciowych](#configure-guest-os-for-multiple-nics).

## <a name="remove-a-nic-from-an-existing-vm"></a>Usuń kartę Sieciową z istniejącej maszyny Wirtualnej
Aby usunąć wirtualną kartę Sieciową z istniejącej maszyny Wirtualnej, możesz cofnąć przydział maszyny Wirtualnej, usunąć wirtualną kartę Sieciową, a następnie uruchom maszynę Wirtualną.

1. Cofnij Przydział maszyny Wirtualnej przy użyciu [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm). Poniższy przykład powoduje cofnięcie przydziału maszyny Wirtualnej o nazwie *myVM* w *myResourceGroup*:

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Pobieranie istniejącej konfiguracji maszyny Wirtualnej przy użyciu [Get-AzVm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm). Poniższy przykład pobiera informacje dotyczące maszyny Wirtualnej o nazwie *myVM* w *myResourceGroup*:

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. Uzyskać informacje na temat usuwania karty Sieciowej z [Get AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface). Poniższy przykład pobiera informacje *myNic3*:

    ```powershell
    # List existing NICs on the VM if you need to determine NIC name
    $vm.NetworkProfile.NetworkInterfaces

    $nicId = (Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" -Name "myNic3").Id   
    ```

4. Usuń z kartą Sieciową o [AzVMNetworkInterface Usuń](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmnetworkinterface) , a następnie zaktualizuj maszynę Wirtualną za pomocą [Update-AzVm](https://docs.microsoft.com/powershell/module/az.compute/update-azvm). Poniższy przykład usuwa *myNic3* uzyskanych przez `$nicId` w poprzednim kroku:

    ```powershell
    Remove-AzVMNetworkInterface -VM $vm -NetworkInterfaceIDs $nicId | `
        Update-AzVm -ResourceGroupName "myResourceGroup"
    ```   

5. Uruchom maszynę Wirtualną za pomocą [Start-AzVm](https://docs.microsoft.com/powershell/module/az.compute/start-azvm):

    ```powershell
    Start-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```   

## <a name="create-multiple-nics-with-templates"></a>Tworzenie wielu kart sieciowych za pomocą szablonów
Szablony usługi Azure Resource Manager umożliwiają tworzenie wielu wystąpień zasobu podczas wdrażania, takie jak tworzenie wiele kart sieciowych. Szablony usługi Resource Manager umożliwiają deklaratywne pliki JSON zdefiniować środowiska. Aby uzyskać więcej informacji, zobacz [przegląd możliwości usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Możesz użyć *kopiowania* Aby określić liczbę wystąpień, aby utworzyć:

```json
"copy": {
    "name": "multiplenics",
    "count": "[parameters('count')]"
}
```

Aby uzyskać więcej informacji, zobacz [tworzenie wielu wystąpień przy użyciu *kopiowania*](../../resource-group-create-multiple.md). 

Można również użyć `copyIndex()` celu dołączenia liczby do nazwy zasobu. Następnie możesz utworzyć *myNic1*, *MyNic2* i tak dalej. Poniższy kod przedstawia przykład dodając wartość indeksu:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Pełny przykład można znaleźć [tworzenia wielu kart sieciowych przy użyciu szablonów usługi Resource Manager](../../virtual-network/template-samples.md).

Dodawanie tras dla pomocniczych kartach sieciowych do systemu operacyjnego, wykonując kroki opisane w [konfiguracji systemu operacyjnego dla wielu kart sieciowych](#configure-guest-os-for-multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Konfigurowanie systemu operacyjnego gościa dla wielu kart sieciowych

Platforma Azure przypisuje domyślnej bramy do pierwszego interfejsu sieciowego (podstawowy) dołączonych do maszyny wirtualnej. Platforma Azure domyślnie nie przypisuje domyślnej bramy do dodatkowych interfejsów sieciowych dołączonych do maszyny wirtualnej. Dlatego domyślnie nie można komunikować się z zasobami poza podsiecią, w której znajduje się dodatkowy interfejs sieciowy. Dodatkowymi interfejsami sieciowymi mogą jednak komunikować się z zasobami poza ich podsieciami, chociaż kroki włączania komunikacji są różne dla różnych systemów operacyjnych.

1. W wierszu polecenia Windows uruchom `route print` polecenia, które zwraca dane wyjściowe podobne do następujących danych wyjściowych dla maszyny wirtualnej z dwiema podłączonych interfejsów sieciowych:

    ```
    ===========================================================================
    Interface List
    3...00 0d 3a 10 92 ce ......Microsoft Hyper-V Network Adapter #3
    7...00 0d 3a 10 9b 2a ......Microsoft Hyper-V Network Adapter #4
    ===========================================================================
    ```
 
    W tym przykładzie **4 karty sieci funkcji Hyper-V Microsoft** (interfejs 7) jest dodatkowy interfejs sieciowy, nie ma domyślnej bramy do niej przypisany.

2. W wierszu polecenia Uruchom polecenie `ipconfig` polecenie, aby zobaczyć, których adres IP jest przypisywany z pomocniczym interfejsem sieciowym. W tym przykładzie 192.168.2.4 jest przypisany do interfejsu 7. Dodatkowy interfejs sieciowy jest zwracane nie adres bramy domyślnej.

3. Aby kierować cały ruch skierowany do adresów poza podsiecią dodatkowy interfejs sieciowy do bramy dla podsieci, uruchom następujące polecenie:

    ```
    route add -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5015 IF 7
    ```

    Adres bramy podsieci jest pierwszy adres IP (kończy się rozszerzeniem.1) w zakresie adresów zdefiniowanych dla tej podsieci. Jeśli nie chcesz kierować cały ruch poza podsiecią, można zamiast tego dodać indywidualnych tras do określonych miejsc docelowych. Na przykład, jeśli chcesz skierować ruch z dodatkowy interfejs sieciowy do 192.168.3.0 sieci, wpisz polecenie:

      ```
      route add -p 192.168.3.0 MASK 255.255.255.0 192.168.2.1 METRIC 5015 IF 7
      ```
  
4. Aby potwierdzić łączność z zasobami na 192.168.3.0 sieci, na przykład, wprowadź następujące polecenie, aby wykonać polecenie ping 192.168.3.4 przy użyciu interfejsu 7 (192.168.2.4):

    ```
    ping 192.168.3.4 -S 192.168.2.4
    ```

    Może być konieczne otwarcie protokołu ICMP przez zaporę Windows urządzenia, które one wysyłać polecenia ping przy użyciu następującego polecenia:
  
      ```
      netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
      ```
  
5. Aby upewnić się, dodano trasę znajduje się w tabeli tras, wprowadź `route print` polecenia, które zwraca dane wyjściowe podobne do następującego tekstu:

    ```
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4     15
              0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.4   5015
    ```

    Trasy z *192.168.1.1* w obszarze **bramy**, trasy, który jest domyślnie dla podstawowego interfejsu sieciowego. Trasa o *192.168.2.1* w obszarze **bramy**, trasa został dodany.

## <a name="next-steps"></a>Kolejne kroki
Przegląd [rozmiarów maszyn wirtualnych Windows](sizes.md) Kiedy próbujesz utworzyć maszynę Wirtualną, która ma wiele kart sieciowych. Należy zwrócić uwagę na maksymalną liczbę kart sieciowych obsługiwanych przez każdego rozmiaru maszyny Wirtualnej. 


