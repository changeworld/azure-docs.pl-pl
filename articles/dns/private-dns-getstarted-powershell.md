---
title: Szybki Start — tworzenie prywatnej strefy DNS platformy Azure przy użyciu Azure PowerShell
description: W tym artykule opisano tworzenie i testowanie prywatnej strefy i rekordu DNS w Azure DNS. Jest to przewodnik krok po kroku dotyczący tworzenia pierwszej prywatnej strefy i rekordu DNS przy użyciu Azure PowerShell.
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 10/05/2019
ms.author: victorh
ms.openlocfilehash: fb39042e53795057a3404ba1e8cb5903188966f7
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71960459"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-azure-powershell"></a>Szybki Start: Tworzenie prywatnej strefy DNS platformy Azure przy użyciu Azure PowerShell

W tym artykule omówiono procedurę tworzenia pierwszej prywatnej strefy i rekordu DNS przy użyciu Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Strefa DNS jest używana do hostowania rekordów DNS dla określonej domeny. Aby rozpocząć Hostowanie domeny w Azure DNS, musisz utworzyć strefę DNS dla tej nazwy domeny. Każdy rekord DNS dla domeny zostanie utworzony w tej strefie DNS. Aby opublikować prywatną strefę DNS w sieci wirtualnej, należy określić listę sieci wirtualnych, które mogą rozpoznawać rekordy w strefie.  Są one nazywane *połączonymi* sieciami wirtualnymi. Gdy Autorejestracja jest włączona, Azure DNS aktualizuje także rekordy strefy za każdym razem, gdy maszyna wirtualna jest tworzona, zmienia jej adres IP lub został usunięty.

W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> * Tworzenie prywatnej strefy DNS
> * Tworzenie testowych maszyn wirtualnych
> * Tworzenie dodatkowego rekordu DNS
> * Testowanie strefy prywatnej

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

Jeśli wolisz, możesz ukończyć ten przewodnik Szybki Start przy użyciu [interfejsu wiersza polecenia platformy Azure](private-dns-getstarted-cli.md).

## <a name="create-the-resource-group"></a>Tworzenie grupy zasobów

Najpierw utwórz grupę zasobów zawierającą strefę DNS: 

```azurepowershell
New-AzResourceGroup -name MyAzureResourceGroup -location "eastus"
```

## <a name="create-a-private-dns-zone"></a>Tworzenie prywatnej strefy DNS

Strefa DNS jest tworzona za pomocą polecenia cmdlet `New-AzPrivateDnsZone`.

Poniższy przykład tworzy sieć wirtualną o nazwie **myAzureVNet**. Następnie tworzy strefę DNS o nazwie **Private.contoso.com** w grupie zasobów **MyAzureResourceGroup** , łączy strefę DNS z siecią wirtualną **MyAzureVnet** i włącza rejestrację automatyczną.

```azurepowershell
Install-Module -Name Az.PrivateDns -force

$backendSubnet = New-AzVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix "10.2.0.0/24"
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName MyAzureResourceGroup `
  -Location eastus `
  -Name myAzureVNet `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $backendSubnet

$zone = New-AzPrivateDnsZone -Name private.contoso.com -ResourceGroupName MyAzureResourceGroup

$link = New-AzPrivateDnsVirtualNetworkLink -ZoneName private.contoso.com `
  -ResourceGroupName MyAzureResourceGroup -Name "mylink" `
  -VirtualNetworkId $vnet.id -EnableRegistration
```

Jeśli chcesz utworzyć strefę tylko do rozpoznawania nazw (bez automatycznej rejestracji nazwy hosta), możesz pominąć parametr `-EnableRegistration`.

### <a name="list-dns-private-zones"></a>Wyświetlanie listy stref prywatnych DNS

Pomijając nazwę strefy z `Get-AzPrivateDnsZone`, można wyliczyć wszystkie strefy w grupie zasobów. Ta operacja zwraca tablicę obiektów strefy.

```azurepowershell
$zones = Get-AzPrivateDnsZone -ResourceGroupName MyAzureResourceGroup
$zones
```

Pomijając zarówno nazwę strefy, jak i nazwę grupy zasobów z `Get-AzPrivateDnsZone`, można wyliczyć wszystkie strefy w ramach subskrypcji platformy Azure.

```azurepowershell
$zones = Get-AzPrivateDnsZone
$zones
```

## <a name="create-the-test-virtual-machines"></a>Tworzenie testowych maszyn wirtualnych

Teraz Utwórz dwie maszyny wirtualne, aby można było przetestować prywatną strefę DNS:

```azurepowershell
New-AzVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM01" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389

New-AzVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM02" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389
```

Wykonanie tej czynności potrwa kilka minut.

## <a name="create-an-additional-dns-record"></a>Tworzenie dodatkowego rekordu DNS

Zestawy rekordów są tworzone za pomocą polecenia cmdlet `New-AzPrivateDnsRecordSet`. Poniższy przykład tworzy rekord z względną nazwą **bazy danych** w strefie DNS **Private.contoso.com**w grupie zasobów **MyAzureResourceGroup**. W pełni kwalifikowana nazwa zestawu rekordów to **DB.private.contoso.com**. Typ rekordu to "A" z adresem IP "10.2.0.4", a czas wygaśnięcia wynosi 3600 sekund.

```azurepowershell
New-AzPrivateDnsRecordSet -Name db -RecordType A -ZoneName private.contoso.com `
   -ResourceGroupName MyAzureResourceGroup -Ttl 3600 `
   -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address "10.2.0.4")
```

### <a name="view-dns-records"></a>Wyświetlanie rekordów DNS

Aby wyświetlić listę rekordów DNS w strefie, uruchom polecenie:

```azurepowershell
Get-AzPrivateDnsRecordSet -ZoneName private.contoso.com -ResourceGroupName MyAzureResourceGroup
```

## <a name="test-the-private-zone"></a>Testowanie strefy prywatnej

Teraz można testować rozpoznawanie nazw dla strefy prywatnej **Private.contoso.com** .

### <a name="configure-vms-to-allow-inbound-icmp"></a>Konfigurowanie maszyn wirtualnych do zezwalania na ruch przychodzący ICMP

Można użyć polecenia ping do testowania rozpoznawania nazw. Skonfiguruj zaporę na obu maszynach wirtualnych, aby zezwolić na przychodzące pakiety ICMP.

1. Połącz się z usługą myVM01 i Otwórz okno programu Windows PowerShell z uprawnieniami administratora.
2. Uruchom następujące polecenie:

   ```powershell
   New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
   ```

Powtórz dla myVM02.

### <a name="ping-the-vms-by-name"></a>Wyślij polecenie ping do maszyn wirtualnych według nazwy

1. W wierszu polecenia programu Windows PowerShell myVM02 polecenie ping myVM01 przy użyciu automatycznie zarejestrowanej nazwy hosta:

   ```
   ping myVM01.private.contoso.com
   ```

   Powinny pojawić się dane wyjściowe podobne do tego:

   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```

2. Teraz Wyślij polecenie ping do utworzonej wcześniej nazwy **bazy danych** :

   ```
   ping db.private.contoso.com
   ```

   Powinny pojawić się dane wyjściowe podobne do tego:

   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milliseconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>Usuń wszystkie zasoby

Gdy nie jest już potrzebne, Usuń grupę zasobów **MyAzureResourceGroup** , aby usunąć zasoby utworzone w tym artykule.

```azurepowershell
Remove-AzResourceGroup -Name MyAzureResourceGroup
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Scenariusze Azure DNS Private Zones](private-dns-scenarios.md)
