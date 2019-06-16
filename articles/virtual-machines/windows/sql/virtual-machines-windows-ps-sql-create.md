---
title: Przewodnik dotyczący inicjowania obsługi administracyjnej maszyny wirtualne SQL Server przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Udostępnia kroków i poleceń programu PowerShell do tworzenia maszyny Wirtualnej platformy Azure za pomocą obrazów w galerii maszyn wirtualnych programu SQL Server.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: edf5f2b681123243f55b1c2bf19a500e68171c0e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66165729"
---
# <a name="how-to-provision-sql-server-virtual-machines-with-azure-powershell"></a>Jak wykonać aprowizację maszyn wirtualnych programu SQL Server przy użyciu programu Azure PowerShell

W tym przewodniku objaśniono sposoby tworzenia maszyn wirtualnych Windows SQL Server przy użyciu programu Azure PowerShell. Uproszczony przykład programu Azure PowerShell z wartościami domyślnymi więcej, zobacz [szybkiego startu programu PowerShell platformy Azure dla maszyny Wirtualnej SQL](quickstart-sql-vm-create-powershell.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-your-subscription"></a>Konfigurowanie subskrypcji

1. Otwórz program PowerShell i nawiąż połączenie z kontem platformy Azure, uruchamiając polecenie **Connect-AzAccount**.

   ```powershell
   Connect-AzAccount
   ```

1. Powinien zostać wyświetlony ekran z monitem o podanie poświadczeń. Użyj tego samego adresu e-mail i hasła, którego używasz do logowania w witrynie Azure Portal.

## <a name="define-image-variables"></a>Zdefiniuj zmienne do obrazu
Aby ponownie użyć wartości i uproszczenie tworzenia skryptów, Rozpocznij od zdefiniowania liczbę zmiennych. Zmiany wartości parametrów, ale należy pamiętać o długości nazwy i znaki specjalne dotyczą podczas modyfikowania podanych wartości ograniczenia nazewnictwa.

### <a name="location-and-resource-group"></a>Lokalizacji i grupie zasobów
Zdefiniuj region danych i grupy zasobów, do którego możesz utworzyć inne zasoby maszyny Wirtualnej.

Zmodyfikuj, ile chcesz, a następnie uruchom te polecenia cmdlet do inicjowania tych zmiennych.

```powershell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>Właściwości magazynu
Zdefiniuj konto magazynu i typ magazynu, który będzie używany przez maszynę wirtualną.

Zmodyfikuj, ile chcesz, a następnie uruchom następujące polecenie cmdlet do inicjowania tych zmiennych. Firma Microsoft zaleca używanie [premium SSD](../disks-types.md#premium-ssd) dla obciążeń produkcyjnych.

```powershell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Właściwości sieci
Zdefiniuj właściwości, które ma być używany przez sieć maszyny wirtualnej. 

- Interfejs sieciowy
- Metoda alokacji TCP/IP
- Nazwa sieci wirtualnej
- Nazwa podsieci wirtualne
- Zakres adresów IP dla sieci wirtualnej
- Zakres adresów IP w podsieci
- Etykieta nazwy domeny publicznego

Zmodyfikuj, ile chcesz, a następnie uruchom to polecenie cmdlet do inicjowania tych zmiennych.

```powershell
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$TCPIPAllocationMethod = "Dynamic"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$DomainName = $ResourceGroupName
```

### <a name="virtual-machine-properties"></a>Właściwości maszyny wirtualnej
Zdefiniuj nazwę maszyny wirtualnej, nazwę komputera, rozmiar maszyny wirtualnej i nazwa dysku systemu operacyjnego dla maszyny wirtualnej.

Zmodyfikuj, ile chcesz, a następnie uruchom to polecenie cmdlet do inicjowania tych zmiennych.

```powershell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>Wybierz obraz programu SQL Server

Aby zdefiniować obrazu programu SQL Server do użycia dla maszyny wirtualnej, należy użyć następujących zmiennych. 

1. Najpierw Wyświetl listę wszystkich ofert dotyczących obrazów programu SQL Server przy użyciu `Get-AzVMImageOffer` polecenia. To polecenie wyświetla listę bieżących obrazów, które są dostępne w witrynie Azure Portal, a także starsze obrazy, które mogą być instalowane tylko przy użyciu programu PowerShell:

   ```powershell
   Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. Na potrzeby tego samouczka należy użyć następujących zmiennych do określania programu SQL Server 2017 w systemie Windows Server 2016.

   ```powershell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. Następnie Wyświetl dostępne wersje oferty.

   ```powershell
   Get-AzVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. W tym samouczku, należy użyć wersji programu SQL Server 2017 Developer edition (**SQLDEV**). Developer edition jest bezpłatnie licencjonowane na potrzeby projektowania i testowania, a płacisz tylko kosztów obsługi maszyny Wirtualnej.

   ```powershell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Przy użyciu modelu wdrażania usługi Resource Manager pierwszy obiekt, który tworzysz jest grupa zasobów. Użyj [New AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) polecenia cmdlet, aby utworzyć grupę zasobów platformy Azure i jej zasobów. Określ zmienne, które poprzednio inicjowany dla nazwy grupy zasobów i lokalizacji.

Uruchom to polecenie cmdlet do tworzenia nowej grupy zasobów.

```powershell
New-AzResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu
Maszyna wirtualna wymaga zasobów magazynu dla dysku systemu operacyjnego i plików danych i dziennika programu SQL Server. Dla uproszczenia utworzysz jednego dysku dla obu. Możesz dołączyć dodatkowe dyski później za pomocą [dysk platformy Azure Dodaj](https://docs.microsoft.com/powershell/module/servicemanagement/azure/add-azuredisk) polecenia cmdlet, aby umieścić swoje dane programu SQL Server i dzienniku plików na dedykowanych dysków. Użyj [New AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) polecenia cmdlet, aby utworzyć konto magazynu w warstwie standardowa w nowej grupy zasobów. Określ zmienne, które poprzednio inicjowany dla nazwy konta magazynu, nazwa jednostki Sku magazynu i lokalizacji.

Uruchom to polecenie cmdlet do tworzenia nowego konta magazynu.

```powershell
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName `
   -Name $StorageName -SkuName $StorageSku `
   -Kind "Storage" -Location $Location
```

> [!TIP]
> Tworzenie konta magazynu może potrwać kilka minut.

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych
Maszyna wirtualna wymaga wiele zasobów, sieci, łączności sieciowej.

* Każda maszyna wirtualna wymaga sieci wirtualnej.
* Sieć wirtualna musi mieć co najmniej jedną podsieć zdefiniowane.
* Interfejs sieciowy musi być zdefiniowany publiczny lub prywatny adres IP.

### <a name="create-a-virtual-network-subnet-configuration"></a>Utwórz konfigurację podsieci sieci wirtualnej
Rozpocznij od utworzenia konfiguracji podsieci dla sieci wirtualnej. Na potrzeby tego samouczka, Utwórz podsieć domyślna za pomocą [New AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) polecenia cmdlet. Określ zmienne, które poprzednio inicjowany dla prefiksu nazwy i adresu podsieci.

> [!NOTE]
> Można zdefiniować dodatkowe właściwości konfiguracji podsieci sieci wirtualnej za pomocą tego polecenia cmdlet, ale która wykracza poza zakres tego samouczka.

Uruchom to polecenie cmdlet umożliwia utworzenie konfiguracji podsieci wirtualnej.

```powershell
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
Następnie utwórz sieć wirtualną w nowych zasobów grupy przy użyciu [New AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) polecenia cmdlet. Określ zmienne, które poprzednio inicjowany dla nazwy, lokalizacji i prefiksu adresu. Użyj konfiguracji podsieci zdefiniowanej w poprzednim kroku.

Uruchom to polecenie cmdlet do tworzenia sieci wirtualnej.

```powershell
$VNet = New-AzVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>Tworzenie publicznego adresu IP
Skoro zdefiniowano sieci wirtualnej, należy skonfigurować adres IP dla połączenia z maszyną wirtualną. W tym samouczku należy utworzyć publiczny adres IP za pomocą dynamicznego adresu IP, odnoszący się do obsługi łączności z Internetem. Użyj [New AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) polecenia cmdlet, aby utworzyć publiczny adres IP w tej nowej grupy zasobów. Określ zmienne, które poprzednio inicjowany dla nazwy, lokalizacji, metodę alokacji i etykietę nazwy domeny DNS.

> [!NOTE]
> Można zdefiniować dodatkowe właściwości publicznego adresu IP za pomocą tego polecenia cmdlet, ale która wykracza poza zakres tego samouczka początkowej. Istnieje również możliwość utworzenia prywatnego adresu lub adresem ze statycznym adresem, ale jest to poza zakres tego samouczka.

Uruchom to polecenie cmdlet do tworzenia publicznego adresu IP.

```powershell
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>Utwórz sieciową grupę zabezpieczeń
Aby zabezpieczyć ruch maszyny Wirtualnej i programu SQL Server, Utwórz sieciową grupę zabezpieczeń.

1. Najpierw utwórz reguły sieciowej grupy zabezpieczeń dla protokołu RDP zezwolić na połączenia pulpitu zdalnego.

   ```powershell
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. Skonfiguruj reguły sieciowej grupy zabezpieczeń zezwalającą na ruch na porcie TCP 1433. Ten krok umożliwia połączenia z programem SQL Server za pośrednictwem Internetu.

   ```powershell
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. Utwórz sieciową grupę zabezpieczeń.

   ```powershell
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>Utwórz interfejs sieciowy
Teraz możesz przystąpić do tworzenia interfejsu sieciowego dla maszyny wirtualnej. Użyj [New AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) polecenie cmdlet do tworzenia interfejsu sieciowego w nowej grupie zasobów. Określ nazwę, lokalizację, podsieci i publicznego adresu IP, które wcześniej zdefiniowany.

Uruchom to polecenie cmdlet do tworzenia interfejsu sieciowego.

```powershell
$Interface = New-AzNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>Konfigurowanie obiektu maszyny Wirtualnej
Teraz, gdy są zdefiniowane zasoby magazynu i sieci, możesz zdefiniować zasoby obliczeniowe dla maszyny wirtualnej.

- Określ rozmiar maszyny wirtualnej i różne właściwości systemu operacyjnego.
- Określ interfejs sieciowy, który został wcześniej utworzony.
- Zdefiniuj magazynu obiektów blob.
- Określ dysk systemu operacyjnego.

### <a name="create-the-vm-object"></a>Tworzenie obiektu maszyny Wirtualnej
Rozpocznij od określenia rozmiaru maszyny wirtualnej. W tym samouczku należy określić DS13. Użyj [New AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) polecenia cmdlet, aby utworzyć obiekt można skonfigurować maszyny wirtualnej. Określ zmienne, które poprzednio inicjowany dla nazwy i rozmiaru.

Uruchom to polecenie cmdlet do tworzenia obiektu maszyny wirtualnej.

```powershell
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Utwórz obiekt poświadczeń, aby pomieścić nazwę i hasło dla poświadczeń administratora lokalnego
Zanim będzie można skonfigurować właściwości systemu operacyjnego dla maszyny wirtualnej, musisz podać poświadczenia dla konta administratora lokalnego jako bezpieczny ciąg. Aby to zrobić, użyj [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) polecenia cmdlet.

Uruchom następujące polecenie cmdlet, a następnie w oknie żądania poświadczenie programu PowerShell, wpisz nazwę i hasło dla konta administratora lokalnego na maszynie wirtualnej.

```powershell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Ustaw właściwości systemu operacyjnego dla maszyny wirtualnej
Teraz możesz przystąpić do ustawiania właściwości systemu operacyjnego maszyny wirtualnej za pomocą [AzVMOperatingSystem zestaw](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) polecenia cmdlet.

- Ustaw typ systemu operacyjnego jako Windows.
- Wymagaj [agenta maszyny wirtualnej](../../extensions/agent-windows.md) do zainstalowania.
- Określ, czy polecenie cmdlet umożliwia automatyczną aktualizację.
- Określ zmienne, które poprzednio inicjowany nazwę maszyny wirtualnej, nazwę komputera i poświadczeń.

Uruchom to polecenie cmdlet, aby ustawić właściwości systemu operacyjnego dla maszyny wirtualnej.

```powershell
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Dodawanie interfejsu sieciowego do maszyny wirtualnej
Następnie użyj [AzVMNetworkInterface Dodaj](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) polecenia cmdlet, aby dodać interfejs sieciowy przy użyciu zmiennej określonej wcześniej.

Uruchom to polecenie cmdlet, aby ustawić interfejsu sieciowego dla maszyny wirtualnej.

```powershell
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Ustaw lokalizację obiektu blob magazynu dla dysku, który ma być używany przez maszynę wirtualną
Następnym etapem jest skonfigurowanie dysku maszyny Wirtualnej przy użyciu zmiennych, które wcześniej zdefiniowaną lokalizacja magazynu obiektów blob.

Uruchom to polecenie cmdlet, aby ustawić lokalizacji magazynu obiektów blob.

```powershell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Ustaw właściwości dysku dla maszyny wirtualnej systemu operacyjnego
Następnym etapem jest skonfigurowanie systemu operacyjnego właściwości dysku dla maszyny wirtualnej za pomocą [AzVMOSDisk zestaw](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) polecenia cmdlet. 

- Określ, czy system operacyjny dla maszyny wirtualnej będą pochodzić z obrazu.
- Ustaw buforowanie można odczytać tylko (ponieważ program SQL Server jest instalowany na tym samym dysku).
- Określ zmienne, które poprzednio inicjowany dla nazwy maszyny Wirtualnej i dysku systemu operacyjnego.

Uruchom to polecenie cmdlet, aby ustawić właściwości dysku dla maszyny wirtualnej systemu operacyjnego.

```powershell
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Określ obraz platformy dla maszyny wirtualnej
Ostatnim krokiem konfiguracji jest do określenia obrazu platformy dla swojej maszyny wirtualnej. W tym samouczku użyj najnowszego obrazu programu SQL Server 2016 CTP. Użyj [AzVMSourceImage zestaw](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) polecenia cmdlet, aby użyć tego obrazu za pomocą zmiennych, które zostały wcześniej zdefiniowane.

Uruchom to polecenie cmdlet, aby określić obrazu platformy dla swojej maszyny wirtualnej.

```powershell
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Tworzenie maszyny wirtualnej z programem SQL
Teraz, po zakończeniu kroków konfiguracji możesz przystąpić do tworzenia maszyny wirtualnej. Użyj [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) polecenia cmdlet, aby utworzyć maszynę wirtualną przy użyciu zmiennych, które zostały zdefiniowane.

> [!TIP]
> Tworzenie maszyny Wirtualnej może potrwać kilka minut.

Uruchom to polecenie cmdlet, aby utworzyć maszynę wirtualną.

```powershell
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

Maszyna wirtualna jest tworzona.

> [!NOTE]
> Jeśli wystąpi błąd dotyczący diagnostyki rozruchu, możesz go zignorować. Standardowe konto magazynu jest utworzyć dla diagnostyki rozruchu, ponieważ określone konto magazynu dla dysku maszyny wirtualnej jest konto magazynu premium storage.

## <a name="install-the-sql-iaas-agent"></a>Instalacja agenta SQL IaaS
Maszyny wirtualne programu SQL Server obsługują funkcje automatyczne zarządzanie za pomocą [rozszerzenie agenta IaaS programu SQL Server](virtual-machines-windows-sql-server-agent-extension.md). Aby zainstalować agenta na nowej maszyny Wirtualnej, uruchom następujące polecenie, po jego utworzeniu.


   ```powershell
   Set-AzVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="stop-or-remove-a-vm"></a>Zatrzymywanie lub usuwanie maszyny Wirtualnej

Jeśli maszyna wirtualna nie musi działać w sposób ciągły, możesz uniknąć niepotrzebnych opłat, zatrzymując ją, gdy jest nieużywana. Następujące polecenie zatrzyma maszynę wirtualną, ale pozostawi ją dostępną do użycia w przyszłości.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Możesz również trwale usunąć wszystkie zasoby skojarzone z maszyną wirtualną, korzystając z polecenia **Remove-AzResourceGroup**. Spowoduje to również trwałe usunięcie maszyny wirtualnej, dlatego tego polecenia należy używać z rozwagą.

## <a name="example-script"></a>Przykładowy skrypt
Poniższy skrypt zawiera kompletny skrypt programu PowerShell na potrzeby tego samouczka. Przyjęto założenie, że już skonfigurowaniu subskrypcji platformy Azure za pomocą **Connect AzAccount** i **AzSubscription wybierz** poleceń.

```powershell
# Variables

## Global
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"

## Storage
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"

## Network
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$TCPIPAllocationMethod = "Dynamic"
$DomainName = $ResourceGroupName

##Compute
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"

##Image
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2017-WS2016"
$Sku = "SQLDEV"
$Version = "latest"

# Resource Group
New-AzResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
$NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
$Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $Location -Name $NsgName -SecurityRules $NsgRuleRDP,$NsgRuleSQL
$Interface = New-AzNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id -NetworkSecurityGroupId $Nsg.Id

# Compute
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

# Create the VM in Azure
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

# Add the SQL IaaS Extension
Set-AzVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
```

## <a name="next-steps"></a>Kolejne kroki
Po utworzeniu maszyny wirtualnej, możesz wykonywać następujące czynności:

- Łączenie z maszyną wirtualną przy użyciu protokołu RDP
- Konfigurowanie ustawień programu SQL Server w portalu dla maszyny Wirtualnej, w tym:
   - [Ustawienia magazynu](virtual-machines-windows-sql-server-storage-configuration.md) 
   - [Zautomatyzowanych zadań zarządzania](virtual-machines-windows-sql-server-agent-extension.md)
- [Konfigurowanie połączeń](virtual-machines-windows-sql-connect.md)
- Łączenie klientów i aplikacji do nowego wystąpienia programu SQL Server

