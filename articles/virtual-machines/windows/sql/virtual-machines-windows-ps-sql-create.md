---
title: Przewodnik dotyczący inicjowania obsługi administracyjnej maszyny wirtualne SQL Server przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Udostępnia kroków i poleceń programu PowerShell do tworzenia maszyny Wirtualnej platformy Azure za pomocą obrazów w galerii maszyn wirtualnych programu SQL Server.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/15/2018
ms.author: jroth
ms.openlocfilehash: bb7a0b8c2d0511088282e180a108f8d925f0e4e8
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2018
ms.locfileid: "42054755"
---
# <a name="how-to-provision-sql-server-virtual-machines-with-azure-powershell"></a>Jak wykonać aprowizację maszyn wirtualnych programu SQL Server przy użyciu programu Azure PowerShell

W tym przewodniku objaśniono sposoby tworzenia maszyn wirtualnych Windows SQL Server przy użyciu programu Azure PowerShell. Uproszczony przykład programu Azure PowerShell z wartościami domyślnymi więcej, zobacz [szybkiego startu programu PowerShell platformy Azure dla maszyny Wirtualnej SQL](quickstart-sql-vm-create-powershell.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Ten artykuł wymaga programu Azure PowerShell module w wersji 3.6 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="configure-your-subscription"></a>Konfigurowanie subskrypcji

1. Otwórz program PowerShell i nawiąż połączenie z kontem platformy Azure, uruchamiając polecenie **Connect-AzureRmAccount**.

   ```PowerShell
   Connect-AzureRmAccount
   ```

1. Powinien zostać wyświetlony ekran logowania z monitem o podanie poświadczeń. Użyj tego samego adresu e-mail i hasła, którego używasz do logowania w witrynie Azure Portal.

## <a name="define-image-variables"></a>Zdefiniuj zmienne do obrazu
Aby uprościć tworzenie i ponowne użycie skryptów, Rozpocznij od zdefiniowania liczbę zmiennych. Zmiany wartości parametrów, ale należy ograniczenia związane z długości nazwy i znaki specjalne w przypadku modyfikowania podanych wartości nazewnictwa.

### <a name="location-and-resource-group"></a>Lokalizacji i grupie zasobów
Aby zdefiniować obszar danych i grupy zasobów, do którego możesz utworzyć inne zasoby dla maszyny wirtualnej, należy użyć dwie zmienne.

Modyfikowanie zgodnie z potrzebami, a następnie wykonaj następujące polecenia cmdlet do inicjowania tych zmiennych.

```PowerShell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>Właściwości magazynu
Aby zdefiniować konto magazynu i typ magazynu, który będzie używany przez maszynę wirtualną, należy użyć następujących zmiennych.

Modyfikowanie zgodnie z potrzebami, a następnie wykonaj następujące polecenie cmdlet do inicjowania tych zmiennych. Należy pamiętać, że w tym przykładzie używamy [usługi Premium Storage](../premium-storage.md), który jest zalecane w przypadku obciążeń produkcyjnych.

```PowerShell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Właściwości sieci
Użyj poniższych zmiennych, aby zdefiniować interfejs sieciowy, metodę alokacji TCP/IP, nazwa sieci wirtualnej, nazwę podsieci wirtualnej, zakres adresów IP dla sieci wirtualnej, zakres adresów IP podsieci i etykieta nazwy domeny publicznego ma być używany przez sieć maszyny wirtualnej.

Modyfikowanie zgodnie z potrzebami, a następnie wykonaj następujące polecenie cmdlet do inicjowania tych zmiennych.

```PowerShell
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
Aby zdefiniować nazwę maszyny wirtualnej, nazwę komputera, rozmiar maszyny wirtualnej i nazwa dysku systemu operacyjnego dla maszyny wirtualnej, należy użyć następujących zmiennych.

Modyfikowanie zgodnie z potrzebami, a następnie wykonaj następujące polecenie cmdlet do inicjowania tych zmiennych.

```PowerShell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>Wybierz obraz programu SQL Server
Aby zdefiniować obrazu programu SQL Server do użycia dla maszyny wirtualnej, należy użyć następujących zmiennych.

1. Najpierw Wyświetl listę wszystkich ofert dotyczących obrazów programu SQL Server za pomocą **Get-AzureRmVMImageOffer** polecenia:

   ```PowerShell
   Get-AzureRmVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. Na potrzeby tego samouczka należy użyć następujących zmiennych do określania programu SQL Server 2017 w systemie Windows Server 2016.

   ```PowerShell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. Następnie wyświetlić listę dostępnych wersji oferty.

   ```PowerShell
   Get-AzureRmVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. W tym samouczku, należy użyć wersji programu SQL Server 2017 Developer edition (**SQLDEV**). Developer edition jest bezpłatnie licencjonowane na potrzeby projektowania i testowania, a płacisz tylko kosztów obsługi maszyny Wirtualnej.

   ```PowerShell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Przy użyciu modelu wdrażania usługi Resource Manager pierwszy obiekt, który tworzysz jest grupa zasobów. Użyj [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) polecenia cmdlet, aby utworzyć grupę zasobów platformy Azure i jej zasobów z nazwy grupy zasobów i lokalizacji zdefiniowanej przez zmienne, które wcześniej zainicjowane.

Wykonaj następujące polecenie cmdlet do tworzenia nowej grupy zasobów.

```PowerShell
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu
Maszyna wirtualna wymaga zasobów magazynu dla dysku systemu operacyjnego i plików danych i dziennika programu SQL Server. Dla uproszczenia utworzymy jednego dysku dla obu. Możesz dołączyć dodatkowe dyski później za pomocą [dysk platformy Azure Dodaj](/powershell/module/servicemanagement/azure/add-azuredisk) polecenia cmdlet w celu umieszczenia danych programu SQL Server i dzienniku plików na dedykowanych dysków. Użyj [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) polecenia cmdlet, aby utworzyć konto magazynu w warstwie standardowa w nowej grupie zasobów i nazwy konta magazynu, nazwa jednostki Sku magazynu i lokalizacji zdefiniowanej przy użyciu zmiennych, które poprzednio inicjowany .

Wykonaj następujące polecenie cmdlet, aby utworzyć nowe konto magazynu.

```PowerShell
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName `
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
Rozpocznij od utworzenia konfiguracji podsieci dla naszej sieci wirtualnej. W naszym samouczku utworzymy podsieci domyślne przy użyciu [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) polecenia cmdlet. Tworzymy nasze konfiguracji podsieci sieci wirtualnej z prefiksem nazwy i adresu podsieci zdefiniowane przy użyciu zmiennych, które poprzednio inicjowany.

> [!NOTE]
> Można zdefiniować dodatkowe właściwości konfiguracji podsieci sieci wirtualnej za pomocą tego polecenia cmdlet, ale która wykracza poza zakres tego samouczka.

Wykonaj następujące polecenie cmdlet umożliwia utworzenie konfiguracji podsieci wirtualnej.

```PowerShell
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
Następnie należy utworzyć swoją sieć wirtualną przy użyciu [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) polecenia cmdlet. Tworzenie sieci wirtualnej w nowej grupie zasobów, przy użyciu nazwy, lokalizacji i adres prefiks zdefiniowany zmiennych, które poprzednio inicjowany, a następnie używając konfigurację podsieci, które są zdefiniowane w poprzednim kroku.

Wykonaj następujące polecenie cmdlet, aby utworzyć sieć wirtualną.

```PowerShell
$VNet = New-AzureRmVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>Tworzenie publicznego adresu IP
Teraz, gdy mamy już naszej sieci wirtualnej, które zostały zdefiniowane, należy skonfigurować adres IP dla połączenia z maszyną wirtualną. W tym samouczku utworzymy publiczny adres IP za pomocą dynamicznego adresu IP, odnoszący się do obsługi łączności z Internetem. Użyj [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) polecenia cmdlet, aby utworzyć publiczny adres IP adres w grupie zasobów utworzonej wcześniej i przy użyciu nazwy, lokalizacji, metodę alokacji i zdefiniowane przy użyciu zmiennych etykietę nazwy domeny DNS wcześniej zainicjowana.

> [!NOTE]
> Można zdefiniować dodatkowe właściwości publicznego adresu IP za pomocą tego polecenia cmdlet, ale która wykracza poza zakres tego samouczka początkowej. Istnieje również możliwość utworzenia prywatnego adresu lub adresem ze statycznym adresem, ale jest to poza zakres tego samouczka.

Wykonaj następujące polecenie cmdlet do tworzenia publicznego adresu IP.

```PowerShell
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>Utwórz sieciową grupę zabezpieczeń
Aby zabezpieczyć ruch maszyny Wirtualnej i programu SQL Server, Utwórz sieciową grupę zabezpieczeń.

1. Najpierw utwórz reguły sieciowej grupy zabezpieczeń dla protokołu RDP zezwolić na połączenia pulpitu zdalnego.

   ```PowerShell
   $NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. Skonfiguruj reguły sieciowej grupy zabezpieczeń zezwalającą na ruch na porcie TCP 1433. Dzięki temu połączenia z programem SQL Server za pośrednictwem Internetu.

   ```PowerShell
   $NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. Następnie utwórz grupę zabezpieczeń sieci.

   ```PowerShell
   $Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>Utwórz interfejs sieciowy
Teraz możemy przystąpić do tworzenia interfejsu sieciowego, który będzie używać naszych maszyn wirtualnych. Nazywamy [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) uprzednio zdefiniowany polecenie cmdlet do tworzenia interfejsu sieciowego, w grupie zasobów utworzonej wcześniej i przy użyciu nazwy, lokalizacji, podsieci i publicznego adresu IP.

Wykonaj następujące polecenie cmdlet do tworzenia interfejsu sieciowego.

```PowerShell
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>Konfigurowanie obiektu maszyny Wirtualnej
Teraz, gdy zasoby magazynu i sieci, które są zdefiniowane, jesteśmy gotowi zdefiniować zasoby obliczeniowe dla maszyny wirtualnej. W naszym samouczku możemy określić rozmiar maszyny wirtualnej i różne właściwości systemu operacyjnego, określ interfejs sieciowy utworzony wcześniej, zdefiniuj magazynu obiektów blob, a następnie określ dysk systemu operacyjnego.

### <a name="create-the-vm-object"></a>Tworzenie obiektu maszyny Wirtualnej
Rozpocznij od określenia rozmiaru maszyny wirtualnej. W tym samouczku określamy DS13. Nazywamy [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) polecenia cmdlet, aby utworzyć obiekt można skonfigurować maszynę wirtualną o nazwie i rozmiarze zdefiniowane przy użyciu zmiennych, które poprzednio inicjowany.

Wykonaj następujące polecenie cmdlet do tworzenia obiektu maszyny wirtualnej.

```PowerShell
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Utwórz obiekt poświadczeń, aby pomieścić nazwę i hasło dla poświadczeń administratora lokalnego
Zanim firma Microsoft można ustawić właściwości systemu operacyjnego dla maszyny wirtualnej, należy podać poświadczenia dla konta administratora lokalnego jako bezpieczny ciąg. Aby to zrobić, użyj [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) polecenia cmdlet.

Wykonaj następujące polecenie cmdlet, a następnie w oknie żądania poświadczenie programu PowerShell, wpisz nazwę i hasło dla konta administratora lokalnego na maszynie wirtualnej.

```PowerShell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Ustaw właściwości systemu operacyjnego dla maszyny wirtualnej
Teraz jesteśmy gotowi ustawić właściwości systemu operacyjnego maszyny wirtualnej za pomocą [AzureRmVMOperatingSystem zestaw](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) polecenia cmdlet, aby ustawić typ systemu operacyjnego jako Windows, wymagają [agenta maszyny wirtualnej](../../extensions/agent-windows.md) do zainstalowania, określ, czy polecenie cmdlet umożliwia automatyczną aktualizację, a następnie ustaw nazwę maszyny wirtualnej, nazwę komputera i poświadczeń, korzystając ze zmiennych, które wcześniej zainicjowane.

Wykonaj następujące polecenie cmdlet, aby ustawić właściwości systemu operacyjnego dla maszyny wirtualnej.

```PowerShell
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Dodawanie interfejsu sieciowego do maszyny wirtualnej
Następnie dodamy interfejs sieciowy utworzony wcześniej do maszyny wirtualnej. Wywołaj [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) polecenia cmdlet, aby dodać interfejs sieciowy przy użyciu zmiennej interfejsu sieciowego, która wcześniej zdefiniowaną.

Wykonaj następujące polecenie cmdlet, aby ustawić interfejsu sieciowego dla maszyny wirtualnej.

```PowerShell
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Ustaw lokalizację obiektu blob magazynu dla dysku, który ma być używany przez maszynę wirtualną
Następnym etapem jest skonfigurowanie dysku, który ma być używany przez maszynę wirtualną, korzystając ze zmiennych, które wcześniej zdefiniowaną lokalizacja magazynu obiektów blob.

Wykonaj następujące polecenie cmdlet do ustawiania lokalizacji magazynu obiektów blob.

```PowerShell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Ustaw właściwości dysku dla maszyny wirtualnej systemu operacyjnego
Następnym etapem jest skonfigurowanie systemu operacyjnego właściwości dysku dla maszyny wirtualnej. Użyj [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) polecenia cmdlet, aby określić, że system operacyjny dla maszyny wirtualnej będą pochodzić z obrazu, buforowanie można odczytać tylko (ponieważ program SQL Server jest instalowany na tym samym dysku) i definicja maszyny wirtualnej Nazwa i zdefiniowane przy użyciu zmiennych, które wcześniej zdefiniowaną dysku systemu operacyjnego.

Wykonaj następujące polecenie cmdlet, aby ustawić właściwości dysku dla maszyny wirtualnej system operacyjny.

```PowerShell
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Określ obraz platformy dla maszyny wirtualnej
Nasze ostatnim krokiem konfiguracji jest do określenia obrazu platformy dla naszej maszyny wirtualnej. W naszym samouczku używamy najnowszych obrazów programu SQL Server 2016 CTP. Użyj [AzureRmVMSourceImage zestaw](/powershell/module/azurerm.compute/set-azurermvmsourceimage) polecenia cmdlet, aby użyć tego obrazu, zgodnie z definicją w zmiennych, które zostały wcześniej zdefiniowane.

Wykonaj następujące polecenie cmdlet do określania obrazu platformy dla swojej maszyny wirtualnej.

```PowerShell
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Tworzenie maszyny wirtualnej z programem SQL
Po zakończeniu kroków konfiguracji, można przystąpić do utworzenia maszyny wirtualnej. Użyj [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) polecenia cmdlet, aby utworzyć maszynę wirtualną przy użyciu zmiennych, które mają zdefiniowaliśmy.

Wykonaj następujące polecenie cmdlet, aby utworzyć maszynę wirtualną.

```PowerShell
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

Maszyna wirtualna jest tworzona.

> [!NOTE]
> Można zignorować błąd dotyczący diagnostyki rozruchu. Standardowe konto magazynu jest utworzyć dla diagnostyki rozruchu, ponieważ określone konto magazynu dla dysku maszyny wirtualnej jest konto magazynu premium storage.

## <a name="install-the-sql-iaas-agent"></a>Instalacja agenta SQL IaaS
Maszyny wirtualne programu SQL Server obsługują funkcje automatyczne zarządzanie za pomocą [rozszerzenie agenta IaaS programu SQL Server](virtual-machines-windows-sql-server-agent-extension.md). Aby zainstalować agenta na nowej maszynie wirtualnej, uruchom następujące polecenie po jego utworzeniu.

   ```PowerShell
   Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="remove-a-test-vm"></a>Usunąć testowej maszyny Wirtualnej

Jeśli maszyna wirtualna nie musi działać w sposób ciągły, możesz uniknąć niepotrzebnych opłat, zatrzymując ją, gdy jest nieużywana. Następujące polecenie zatrzyma maszynę wirtualną, ale pozostawi ją dostępną do użycia w przyszłości.

```PowerShell
Stop-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Możesz również trwale usunąć wszystkie zasoby skojarzone z maszyną wirtualną, korzystając z polecenia **Remove-AzureRmResourceGroup**. Spowoduje to również trwałe usunięcie maszyny wirtualnej, dlatego tego polecenia należy używać z rozwagą.

## <a name="example-script"></a>Przykładowy skrypt
Poniższy skrypt zawiera kompletny skrypt programu PowerShell na potrzeby tego samouczka. Przyjęto założenie, że masz już instalacji subskrypcji platformy Azure za pomocą **Connect-AzureRmAccount** i **Select-AzureRmSubscription** poleceń.

```PowerShell
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
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
$NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id -NetworkSecurityGroupId $Nsg.Id

# Compute
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

# Create the VM in Azure
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

# Add the SQL IaaS Extension
Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
```

## <a name="next-steps"></a>Kolejne kroki
Po utworzeniu maszyny wirtualnej, możesz wykonywać następujące czynności:

- Łączenie z maszyną wirtualną przy użyciu pulpitu zdalnego (RDP).
- Konfigurowanie ustawień programu SQL Server w portalu dla maszyny Wirtualnej, w tym:
   - [Ustawienia magazynu](virtual-machines-windows-sql-server-storage-configuration.md) 
   - [Zautomatyzowanych zadań zarządzania](virtual-machines-windows-sql-server-agent-extension.md)
- [Konfigurowanie połączeń](virtual-machines-windows-sql-connect.md).
- Połącz z klientów i aplikacji do nowego wystąpienia programu SQL Server.

