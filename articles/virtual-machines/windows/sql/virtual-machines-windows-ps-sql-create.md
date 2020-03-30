---
title: Przewodnik po inicjowaniu obsługi administracyjnej maszyn wirtualnych z programu SQL Server za pomocą programu Azure PowerShell | Dokumenty firmy Microsoft
description: Zawiera kroki i polecenia programu PowerShell do tworzenia maszyny wirtualnej platformy Azure z obrazami galerii maszyn wirtualnych programu SQL Server.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b1578547fbca4caaecb209021569f0fbb2f1ae24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790631"
---
# <a name="how-to-provision-sql-server-virtual-machines-with-azure-powershell"></a>Jak aprowizować maszyny wirtualne programu SQL Server za pomocą programu Azure PowerShell

W tym przewodniku opisano opcje tworzenia maszyn wirtualnych z systemem Windows SQL Server za pomocą programu Azure PowerShell. Aby uzyskać usprawniony przykład programu Azure PowerShell z większą liczrą wartości domyślnych, zobacz [szybki start programu SQL VM Azure PowerShell](quickstart-sql-vm-create-powershell.md).

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-your-subscription"></a>Konfigurowanie subskrypcji

1. Otwórz program PowerShell i nawiąż połączenie z kontem platformy Azure, uruchamiając polecenie **Connect-AzAccount**.

   ```powershell
   Connect-AzAccount
   ```

1. Powinien zostać wyświetlony ekran z monitem o podanie poświadczeń. Użyj tego samego adresu e-mail i hasła, którego używasz do logowania w witrynie Azure Portal.

## <a name="define-image-variables"></a>Definiowanie zmiennych obrazu
Aby ponownie użyć wartości i uprościć tworzenie skryptów, należy rozpocząć od zdefiniowania wielu zmiennych. Zmień wartości parametrów, jak chcesz, ale należy pamiętać o ograniczenia nazewnictwa związane z długością nazw i znaków specjalnych podczas modyfikowania podanych wartości.

### <a name="location-and-resource-group"></a>Lokalizacja i grupa zasobów
Zdefiniuj region danych i grupę zasobów, do której tworzysz inne zasoby maszyny Wirtualnej.

Modyfikuj zgodnie z oczekiwaniami, a następnie uruchom te polecenia cmdlet, aby zainicjować te zmienne.

```powershell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>Właściwości magazynu
Zdefiniuj konto magazynu i typ magazynu, który ma być używany przez maszynę wirtualną.

Modyfikuj zgodnie z oczekiwaniami, a następnie uruchom następujące polecenie cmdlet, aby zainicjować te zmienne. Zalecamy używanie [najwyższej jakości ssd](../disks-types.md#premium-ssd) dla obciążeń produkcyjnych.

```powershell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Właściwości sieci
Zdefiniuj właściwości, które mają być używane przez sieć na maszynie wirtualnej. 

- Interfejs sieciowy
- Metoda alokacji TCP/IP
- Nazwa sieci wirtualnej
- Nazwa podsieci wirtualnej
- Zakres adresów IP dla sieci wirtualnej
- Zakres adresów IP podsieci
- Etykieta nazwy domeny publicznej

Modyfikuj zgodnie z oczekiwaniami, a następnie uruchom to polecenie cmdlet, aby zainicjować te zmienne.

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
Zdefiniuj nazwę maszyny wirtualnej, nazwę komputera, rozmiar maszyny wirtualnej i nazwę dysku systemu operacyjnego dla maszyny wirtualnej.

Modyfikuj zgodnie z oczekiwaniami, a następnie uruchom to polecenie cmdlet, aby zainicjować te zmienne.

```powershell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>Wybieranie obrazu programu SQL Server

Użyj następujących zmiennych, aby zdefiniować obraz programu SQL Server do użycia dla maszyny wirtualnej. 

1. Najpierw za pomocą `Get-AzVMImageOffer` polecenia wyekwzę wszystkie oferty obrazów programu SQL Server. To polecenie zawiera listę bieżących obrazów dostępnych w witrynie Azure Portal, a także starszych obrazów, które można zainstalować tylko w programie PowerShell:

   ```powershell
   Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. W tym samouczku użyj następujących zmiennych, aby określić program SQL Server 2017 w systemie Windows Server 2016.

   ```powershell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. Następnie wymień dostępne wersje oferty.

   ```powershell
   Get-AzVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. W tym samouczku użyj programu SQL Server 2017 Developer edition (**SQLDEV**). Wersja developer jest swobodnie licencjonowane do testowania i tworzenia, a płacisz tylko za koszty uruchomienia maszyny Wirtualnej.

   ```powershell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
W modelu wdrażania Menedżera zasobów pierwszym utworzonym obiektem jest grupa zasobów. Użyj polecenia cmdlet [New-AzResourceGroup,](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) aby utworzyć grupę zasobów platformy Azure i jej zasoby. Określ zmienne, które zostały wcześniej zainicjowane dla nazwy i lokalizacji grupy zasobów.

Uruchom to polecenie cmdlet, aby utworzyć nową grupę zasobów.

```powershell
New-AzResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu
Maszyna wirtualna wymaga zasobów magazynu dla dysku systemu operacyjnego oraz dla plików danych i dzienników programu SQL Server. Dla uproszczenia utworzysz jeden dysk dla obu. Dodatkowe dyski można dołączyć później za pomocą polecenia cmdlet [Add-Azure Disk,](https://docs.microsoft.com/powershell/module/servicemanagement/azure/add-azuredisk) aby umieścić dane programu SQL Server i pliki dziennika na dyskach dedykowanych. Polecenie cmdlet [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) służy do tworzenia standardowego konta magazynu w nowej grupie zasobów. Określ zmienne, które zostały wcześniej zainicjowane dla nazwy konta magazynu, nazwy Sku magazynu i lokalizacji.

Uruchom to polecenie cmdlet, aby utworzyć nowe konto magazynu.

```powershell
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName `
   -Name $StorageName -SkuName $StorageSku `
   -Kind "Storage" -Location $Location
```

> [!TIP]
> Utworzenie konta magazynu może potrwać kilka minut.

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych
Maszyna wirtualna wymaga wielu zasobów sieciowych dla łączności sieciowej.

* Każda maszyna wirtualna wymaga sieci wirtualnej.
* Sieć wirtualna musi mieć zdefiniowaną co najmniej jedną podsieć.
* Interfejs sieciowy musi być zdefiniowany za pomocą publicznego lub prywatnego adresu IP.

### <a name="create-a-virtual-network-subnet-configuration"></a>Tworzenie konfiguracji podsieci sieci wirtualnej
Rozpocznij od utworzenia konfiguracji podsieci dla sieci wirtualnej. W tym samouczku utwórz domyślną podsieć przy użyciu polecenia cmdlet [New-AzVirtualNetworkSubnetConfig.](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) Określ zmienne, które zostały wcześniej zainicjowane dla nazwy podsieci i prefiksu adresu.

> [!NOTE]
> Można zdefiniować dodatkowe właściwości konfiguracji podsieci sieci wirtualnej przy użyciu tego polecenia cmdlet, ale wykracza poza zakres tego samouczka.

Uruchom to polecenie cmdlet, aby utworzyć konfigurację podsieci wirtualnej.

```powershell
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
Następnie utwórz sieć wirtualną w nowej grupie zasobów za pomocą polecenia cmdlet [New-AzVirtualNetwork.](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) Określ zmienne, które zostały wcześniej zainicjowane dla prefiksu nazwy, lokalizacji i adresu. Użyj konfiguracji podsieci zdefiniowanej w poprzednim kroku.

Uruchom to polecenie cmdlet, aby utworzyć sieć wirtualną.

```powershell
$VNet = New-AzVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>Tworzenie publicznego adresu IP
Teraz, gdy sieć wirtualna jest zdefiniowana, należy skonfigurować adres IP dla łączności z maszyną wirtualną. W tym samouczku utwórz publiczny adres IP przy użyciu dynamicznego adresowania IP do obsługi łączności z Internetem. Polecenie cmdlet [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) służy do tworzenia publicznego adresu IP w nowej grupie zasobów. Określ zmienne, które zostały wcześniej zainicjowane dla nazwy, lokalizacji, metody alokacji i etykiety nazwy domeny DNS.

> [!NOTE]
> Można zdefiniować dodatkowe właściwości publicznego adresu IP przy użyciu tego polecenia cmdlet, ale to wykracza poza zakres tego początkowego samouczka. Można również utworzyć adres prywatny lub adres z adresem statycznym, ale to również wykracza poza zakres tego samouczka.

Uruchom to polecenie cmdlet, aby utworzyć publiczny adres IP.

```powershell
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń
Aby zabezpieczyć ruch maszyn wirtualnych i SQL Server, utwórz sieciową grupę zabezpieczeń.

1. Najpierw utwórz regułę sieciowej grupy zabezpieczeń dla protokołu RDP, aby zezwolić na połączenia pulpitu zdalnego.

   ```powershell
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. Skonfiguruj regułę sieciowej grupy zabezpieczeń, która zezwala na ruch na porcie TCP 1433. W ten sposób umożliwia połączenia z programem SQL Server przez Internet.

   ```powershell
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. Utwórz grupę zabezpieczeń sieci.

   ```powershell
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>Tworzenie interfejsu sieciowego
Teraz możesz przystąpić do tworzenia interfejsu sieciowego dla maszyny wirtualnej. Polecenie cmdlet [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) służy do tworzenia interfejsu sieciowego w nowej grupie zasobów. Określ wcześniej zdefiniowaną nazwę, lokalizację, podsieć i publiczny adres IP.

Uruchom to polecenie cmdlet, aby utworzyć interfejs sieciowy.

```powershell
$Interface = New-AzNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>Konfigurowanie obiektu maszyny Wirtualnej
Teraz, gdy zasoby magazynu i sieci są zdefiniowane, można przystąpić do definiowania zasobów obliczeniowych dla maszyny wirtualnej.

- Określ rozmiar maszyny wirtualnej i różne właściwości systemu operacyjnego.
- Określ interfejs sieciowy, który został wcześniej utworzony.
- Zdefiniuj magazyn obiektów blob.
- Określ dysk systemu operacyjnego.

### <a name="create-the-vm-object"></a>Tworzenie obiektu maszyny Wirtualnej
Zacznij od określenia rozmiaru maszyny wirtualnej. W tym samouczku określ DS13. Polecenie cmdlet [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) służy do tworzenia konfigurowalnego obiektu maszyny wirtualnej. Określ zmienne, które zostały wcześniej zainicjowane dla nazwy i rozmiaru.

Uruchom to polecenie cmdlet, aby utworzyć obiekt maszyny wirtualnej.

```powershell
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Tworzenie obiektu poświadczeń w celu przechowywania nazwy i hasła dla poświadczeń administratora lokalnego
Przed ustawieniem właściwości systemu operacyjnego dla maszyny wirtualnej należy podać poświadczenia dla konta administratora lokalnego jako bezpieczny ciąg. Aby to osiągnąć, należy użyć polecenia cmdlet [Get-Credential.](https://technet.microsoft.com/library/hh849815.aspx)

Uruchom następujące polecenie cmdlet i w oknie żądania poświadczeń programu PowerShell wpisz nazwę i hasło do użycia dla lokalnego konta administratora na maszynie wirtualnej.

```powershell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Ustawianie właściwości systemu operacyjnego dla maszyny wirtualnej
Teraz możesz przystąpić do ustawiania właściwości systemu operacyjnego maszyny wirtualnej za pomocą polecenia cmdlet [Set-AzVMOperatingSystem.](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem)

- Ustaw typ systemu operacyjnego jako Windows.
- Wymagaj zainstalowania [agenta maszyny wirtualnej.](../../extensions/agent-windows.md)
- Określ, że polecenie cmdlet umożliwia automatyczną aktualizację.
- Określ zmienne, które zostały wcześniej zainicjowane dla nazwy maszyny wirtualnej, nazwy komputera i poświadczeń.

Uruchom to polecenie cmdlet, aby ustawić właściwości systemu operacyjnego dla maszyny wirtualnej.

```powershell
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Dodawanie interfejsu sieciowego do maszyny wirtualnej
Następnie użyj polecenia cmdlet [Add-AzVMNetworkInterface,](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) aby dodać interfejs sieciowy przy użyciu zmiennej zdefiniowanej wcześniej.

Uruchom to polecenie cmdlet, aby ustawić interfejs sieciowy dla maszyny wirtualnej.

```powershell
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Ustawianie lokalizacji magazynu obiektów blob dla dysku, który ma być używany przez maszynę wirtualną
Następnie ustaw lokalizację magazynu obiektów blob dla dysku maszyny Wirtualnej przy użyciu zmiennych zdefiniowanych wcześniej.

Uruchom to polecenie cmdlet, aby ustawić lokalizację magazynu obiektów blob.

```powershell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Ustawianie właściwości dysku systemu operacyjnego dla maszyny wirtualnej
Następnie ustaw właściwości dysku systemu operacyjnego dla maszyny wirtualnej za pomocą polecenia cmdlet [Set-AzVMOSDisk.](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) 

- Określ, że system operacyjny dla maszyny wirtualnej będzie pochodzić z obrazu.
- Ustaw buforowanie tylko do odczytu (ponieważ sql server jest instalowany na tym samym dysku).
- Określ zmienne, które zostały wcześniej zainicjowane dla nazwy maszyny Wirtualnej i dysku systemu operacyjnego.

Uruchom to polecenie cmdlet, aby ustawić właściwości dysku systemu operacyjnego dla maszyny wirtualnej.

```powershell
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Określ obraz platformy dla maszyny wirtualnej
Ostatnim krokiem konfiguracji jest określenie obrazu platformy dla maszyny wirtualnej. W tym samouczku użyj najnowszego obrazu CTP programu SQL Server 2016. Użyj polecenia cmdlet [Set-AzVMSourceImage,](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) aby użyć tego obrazu ze zmiennymi zdefiniowanymi wcześniej.

Uruchom to polecenie cmdlet, aby określić obraz platformy dla maszyny wirtualnej.

```powershell
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Tworzenie maszyny wirtualnej z programem SQL
Po zakończeniu czynności konfiguracyjnych można przystąpić do tworzenia maszyny wirtualnej. Polecenie cmdlet [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) służy do tworzenia maszyny wirtualnej przy użyciu zdefiniowanych zmiennych.

> [!TIP]
> Tworzenie maszyny Wirtualnej może potrwać kilka minut.

Uruchom to polecenie cmdlet, aby utworzyć maszynę wirtualną.

```powershell
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

Maszyna wirtualna jest tworzona.

> [!NOTE]
> Jeśli zostanie wyświetlony błąd dotyczący diagnostyki rozruchu, możesz go zignorować. Standardowe konto magazynu jest tworzone do diagnostyki rozruchu, ponieważ określone konto magazynu dla dysku maszyny wirtualnej jest kontem magazynu w wersji premium.

## <a name="install-the-sql-iaas-agent"></a>Instalacja agenta SQL IaaS
Maszyny wirtualne programu SQL Server obsługują funkcje zautomatyzowanego zarządzania z [rozszerzeniem agenta IaaS programu SQL Server](virtual-machines-windows-sql-server-agent-extension.md). Aby zainstalować agenta na nowej maszynie wirtualnej i zarejestrować go u dostawcy zasobów, uruchom polecenie [New-AzSqlVM](/powershell/module/az.sqlvirtualmachine/new-azsqlvm) po utworzeniu maszyny wirtualnej. Określ typ licencji dla maszyny Wirtualnej programu SQL Server, wybierając między płatnością zgodnie z rzeczywistym i wyjazdem lub przynoś własną licencję za pośrednictwem [usługi Azure Hybrid Benefit.](https://azure.microsoft.com/pricing/hybrid-benefit/) Aby uzyskać więcej informacji na temat licencjonowania, zobacz [model licencjonowania](virtual-machines-windows-sql-ahb.md). 


   ```powershell
   New-AzSqlVM -ResourceGroupName $ResourceGroupName -Name $VMName -Location $Location -LicenseType <PAYG/AHUB> 
   ```


## <a name="stop-or-remove-a-vm"></a>Zatrzymywać lub usuwać maszynę wirtualną

Jeśli maszyna wirtualna nie musi działać w sposób ciągły, możesz uniknąć niepotrzebnych opłat, zatrzymując ją, gdy jest nieużywana. Następujące polecenie zatrzyma maszynę wirtualną, ale pozostawi ją dostępną do użycia w przyszłości.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Możesz również trwale usunąć wszystkie zasoby skojarzone z maszyną wirtualną, korzystając z polecenia **Remove-AzResourceGroup**. Spowoduje to również trwałe usunięcie maszyny wirtualnej, dlatego tego polecenia należy używać z rozwagą.

## <a name="example-script"></a>Przykładowy skrypt
Poniższy skrypt zawiera pełny skrypt programu PowerShell dla tego samouczka. Przyjęto założenie, że subskrypcja platformy Azure została już skonfigurowana do użycia za pomocą poleceń **Connect-AzAccount** i **Select-AzSubscription.**

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

# Add the SQL IaaS Extension, and choose the license type
New-AzSqlVM -ResourceGroupName $ResourceGroupName -Name $VMName -Location $Location -LicenseType <PAYG/AHUB> 
```

## <a name="next-steps"></a>Następne kroki
Po utworzeniu maszyny wirtualnej można:

- Łączenie się z maszyną wirtualną przy użyciu protokołu RDP
- Konfigurowanie ustawień programu SQL Server w portalu dla maszyny Wirtualnej, w tym:
   - [Ustawienia pamięci masowej](virtual-machines-windows-sql-server-storage-configuration.md) 
   - [Zautomatyzowane zadania zarządzania](virtual-machines-windows-sql-server-agent-extension.md)
- [Konfigurowanie łączności](virtual-machines-windows-sql-connect.md)
- Łączenie klientów i aplikacji z nowym wystąpieniem programu SQL Server

