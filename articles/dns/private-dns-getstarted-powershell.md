---
title: Samouczek — tworzenie strefy prywatnej usługi Azure DNS przy użyciu programu Azure PowerShell
description: W tym samouczku utworzysz i przetestujesz strefę prywatną i rekord DNS w usłudze Azure DNS. W tym szczegółowym przewodniku pokazano, jak po raz pierwszy utworzyć strefę prywatną i rekord DNS przy użyciu programu Azure PowerShell oraz zarządzać nimi.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: 2b88454f06d2e2d42298e52feeaa26ae9d1a4902
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66730261"
---
# <a name="tutorial-create-an-azure-dns-private-zone-using-azure-powershell"></a>Samouczek: Tworzenie strefy prywatnej usługi Azure DNS przy użyciu programu Azure PowerShell

W tym samouczku przedstawiono kroki umożliwiające utworzenie po raz pierwszy strefy prywatnej i rekordu DNS przy użyciu programu Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Strefa DNS jest używana do hostowania rekordów DNS dla określonej domeny. Aby rozpocząć hostowanie domeny w usłudze Azure DNS, musisz utworzyć strefę DNS dla tej nazwy domeny. Każdy rekord DNS domeny zostanie utworzony w tej strefie DNS. Aby opublikować prywatną strefę DNS w sieci wirtualnej, musisz określić listę sieci wirtualnych, które mogą rozpoznawać rekordy w strefie.  Są to tak zwane *sieci wirtualne rozpoznawania*. Możesz również określić sieć wirtualną, dla której usługa Azure DNS utrzymuje rekordy nazw hosta zawsze, gdy maszyna wirtualna jest tworzona, zmienia protokół internetowy lub jest usuwana.  Jest to tak zwana *sieć wirtualna rejestracji*.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie strefy prywatnej DNS
> * Tworzenie testowych maszyn wirtualnych
> * Tworzenie dodatkowego rekordu DNS
> * Testowanie strefy prywatnej

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Jeśli chcesz, możesz wykonać ten samouczek przy użyciu [interfejsu wiersza polecenia platformy Azure](private-dns-getstarted-cli.md).

<!--- ## Get the Preview PowerShell modules
These instructions assume you have already installed and signed in to Azure PowerShell, including ensuring you have the required modules for the Private Zone feature. -->

<!---[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)] -->

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-group"></a>Tworzenie grupy zasobów

Najpierw utwórz grupę zasobów, która będzie zawierać strefę DNS: 

```azurepowershell
New-AzResourceGroup -name MyAzureResourceGroup -location "eastus"
```

## <a name="create-a-dns-private-zone"></a>Tworzenie strefy prywatnej DNS

Strefa DNS jest tworzona przy użyciu polecenia cmdlet `New-AzDnsZone` i wartości *Private* parametru **ZoneType**. Poniższy przykład tworzy strefę DNS o nazwie **private.contoso.com** w grupie zasobów o nazwie **MyAzureResourceGroup** i udostępnia strefę DNS w sieci wirtualnej o nazwie  **MyAzureVnet**.

W przypadku pominięcia parametru **ZoneType** strefa zostanie utworzona jako publiczna, dlatego jest on wymagany do utworzenia strefy prywatnej. 

```azurepowershell
$backendSubnet = New-AzVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix "10.2.0.0/24"
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName MyAzureResourceGroup `
  -Location eastus `
  -Name myAzureVNet `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $backendSubnet

New-AzDnsZone -Name private.contoso.com -ResourceGroupName MyAzureResourceGroup `
   -ZoneType Private `
   -RegistrationVirtualNetworkId @($vnet.Id)
```

Jeśli chcesz utworzyć strefę tylko do rozpoznawania nazw (bez automatycznego tworzenia nazwy hosta), możesz użyć parametru *ResolutionVirtualNetworkId* zamiast parametru *RegistrationVirtualNetworkId*.

> [!NOTE]
> Nie będzie można zobaczyć rekordów automatycznie tworzonych nazw hosta. Jednak później przeprowadzisz testowanie, aby sprawdzić, czy istnieją.

### <a name="list-dns-private-zones"></a>Wyświetlanie listy stref prywatnych DNS

Pomijając nazwę strefy w elemencie `Get-AzDnsZone`, można wyliczyć wszystkie strefy w grupie zasobów. Ta operacja zwraca tablicę obiektów stref.

```azurepowershell
Get-AzDnsZone -ResourceGroupName MyAzureResourceGroup
```

Pomijając zarówno nazwę strefy, jak i nazwę grupy zasobów w elemencie `Get-AzDnsZone`, można wyliczyć wszystkie strefy w subskrypcji platformy Azure.

```azurepowershell
Get-AzDnsZone
```

## <a name="create-the-test-virtual-machines"></a>Tworzenie testowych maszyn wirtualnych

Teraz utworzysz dwie maszyny wirtualne, aby umożliwić przetestowanie strefy prywatnej DNS:

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

Ukończenie tej operacji potrwa kilka minut.

## <a name="create-an-additional-dns-record"></a>Tworzenie dodatkowego rekordu DNS

Zestawy rekordów są tworzone za pomocą polecenia cmdlet `New-AzDnsRecordSet`. Poniższy przykład tworzy rekord o nazwie względnej **db** w strefie DNS **private.contoso.com**, w grupie zasobów **MyAzureResourceGroup**. W pełni kwalifikowaną nazwą zestawu rekordów jest **db.private.contoso.com**. Typ rekordu to „A” z adresem IP „10.2.0.4”, a czas wygaśnięcia wynosi 3600 sekund.

```azurepowershell
New-AzDnsRecordSet -Name db -RecordType A -ZoneName private.contoso.com `
   -ResourceGroupName MyAzureResourceGroup -Ttl 3600 `
   -DnsRecords (New-AzDnsRecordConfig -IPv4Address "10.2.0.4")
```

### <a name="view-dns-records"></a>Wyświetlanie rekordów DNS

Aby wyświetlić listę rekordów DNS w strefie, uruchom polecenie:

```azurepowershell
Get-AzDnsRecordSet -ZoneName private.contoso.com -ResourceGroupName MyAzureResourceGroup
```
Pamiętaj, że automatycznie tworzone rekordy A dla dwóch testowych maszyn wirtualnych nie będą widoczne.

## <a name="test-the-private-zone"></a>Testowanie strefy prywatnej

Teraz możesz sprawdzić rozpoznawanie nazw dla swojej **private.contoso.com** stref prywatnych.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Konfigurowanie maszyn wirtualnych w celu zezwolenia na ruch przychodzący protokołu ICMP

Do przetestowania rozpoznawania nazw możesz użyć polecenia ping. W tym celu skonfiguruj zaporę na obydwu maszynach wirtualnych, aby zezwolić na przychodzące pakiety protokołu ICMP.

1. Połącz się z maszyną wirtualną myVM01 i otwórz okno programu Windows PowerShell, używając uprawnień administratora.
2. Uruchom następujące polecenie:

   ```powershell
   New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
   ```

Powtórz dla maszyny wirtualnej myVM02.

### <a name="ping-the-vms-by-name"></a>Wysyłanie polecenia ping do maszyn wirtualnych według nazwy

1. W wierszu polecenia programu Windows PowerShell maszyny wirtualnej myVM02 wyślij polecenie ping do maszyny wirtualnej myVM01 przy użyciu automatycznie zarejestrowanej nazwy hosta:
   ```
   ping myVM01.private.contoso.com
   ```
   Powinny pojawić się dane wyjściowe podobne do następujących:
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
2. Teraz wyślij polecenie ping do utworzonej wcześniej nazwy **db**:
   ```
   ping db.private.contoso.com
   ```
   Powinny pojawić się dane wyjściowe podobne do następujących:
   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>Usuwanie wszystkich zasobów

Jeśli zasoby utworzone w tym samouczku nie są już potrzebne, możesz je usunąć, usuwając grupę zasobów **MyAzureResourceGroup**.

```azurepowershell
Remove-AzResourceGroup -Name MyAzureResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku wdrożono strefę prywatną DNS, utworzono rekord DNS oraz przetestowano strefę.
Następnie możesz dowiedzieć się więcej na temat stref prywatnych DNS.

> [!div class="nextstepaction"]
> [Używanie usługi Azure DNS na potrzeby domen prywatnych](private-dns-overview.md)




