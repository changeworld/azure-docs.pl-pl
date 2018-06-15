---
title: Inicjowanie obsługi administracyjnej przewodnik dla maszyn wirtualnych serwera SQL z programem Azure PowerShell | Dokumentacja firmy Microsoft
description: Zapewnia kroków i poleceń programu PowerShell do tworzenia maszyny Wirtualnej platformy Azure z obrazów Galeria maszyny wirtualnej programu SQL Server.
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
ms.openlocfilehash: 2f0d9c42e32f2dd1181eac8d74c324b5ff2b0c53
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "33944522"
---
# <a name="how-to-provision-sql-server-virtual-machines-with-azure-powershell"></a>Udostępnianie maszyn wirtualnych programu SQL Server przy użyciu programu Azure PowerShell

W tym przewodniku opisano opcje do tworzenia maszyn wirtualnych systemu Windows programu SQL Server przy użyciu programu Azure PowerShell. Aby prostsze przykład programu Azure PowerShell z wartościami domyślnymi więcej, zobacz [programu Azure PowerShell maszyny Wirtualnej SQL — Szybki Start](quickstart-sql-vm-create-powershell.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

W tym artykule wymaga programu Azure PowerShell w wersji modułu 3,6 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="configure-your-subscription"></a>Skonfiguruj subskrypcję

1. Otwórz program PowerShell i nawiąż połączenie z kontem platformy Azure, uruchamiając polecenie **Connect-AzureRmAccount**.

   ```PowerShell
   Connect-AzureRmAccount
   ```

1. Powinien zostać wyświetlony ekran logowania z monitem o podanie poświadczeń. Użyj tego samego adresu e-mail i hasła, którego używasz do logowania w witrynie Azure Portal.

## <a name="define-image-variables"></a>Zdefiniuj zmienne obrazu
Aby uprościć tworzenie skryptów i ponowne użycie, Rozpocznij od zdefiniowania liczba zmiennych. Zmiany wartości parametrów, ale Uważaj nazewnictwa ograniczenia związane z długości nazwy i znaków specjalnych, modyfikując podanych wartości.

### <a name="location-and-resource-group"></a>Lokalizacji i grupy zasobów
Dwie zmienne umożliwiają definiowanie obszaru danych i grupy zasobów, do którego tworzenia innych zasobów dla maszyny wirtualnej.

Zmodyfikuj stosownie do potrzeb, a następnie wykonaj następujące polecenia cmdlet można zainicjować zmienne.

```PowerShell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>Magazyn właściwości
Użyj poniższych zmiennych, aby określić konto magazynu i typu magazynu do użycia przez maszynę wirtualną.

Zmodyfikuj zgodnie z potrzebami, a następnie wykonaj następujące polecenie cmdlet, aby zainicjować zmienne. Należy pamiętać, że w tym przykładzie używamy [magazyn w warstwie Premium](../premium-storage.md), który jest zalecane w przypadku obciążeń produkcyjnych.

```PowerShell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Właściwości sieci
Użyj poniższych zmiennych do definiowania interfejsu sieciowego, Metoda alokacji TCP/IP, nazwa sieci wirtualnej, nazwę podsieci wirtualnej, zakres adresów IP dla sieci wirtualnej, zakres adresów IP podsieci i etykieta nazwy domeny publicznego do użytku przez sieć maszyny wirtualnej.

Zmodyfikuj zgodnie z potrzebami, a następnie wykonaj następujące polecenie cmdlet, aby zainicjować zmienne.

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
Użyj poniższych zmiennych, aby zdefiniować nazwę maszyny wirtualnej, nazwę komputera, rozmiar maszyny wirtualnej i nazwa dysku systemu operacyjnego dla maszyny wirtualnej.

Zmodyfikuj zgodnie z potrzebami, a następnie wykonaj następujące polecenie cmdlet, aby zainicjować zmienne.

```PowerShell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>Wybierz obraz programu SQL Server
Użyj poniższych zmiennych do definiowania obrazu programu SQL Server do użycia dla maszyny wirtualnej.

1. Najpierw listy całe ofert obrazu programu SQL Server z **Get-AzureRmVMImageOffer** polecenia:

   ```PowerShell
   Get-AzureRmVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. W tym samouczku Użyj poniższych zmiennych do określania 2017 serwera SQL na systemie Windows Server 2016.

   ```PowerShell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. Następnie listy się dostępne wersje dla danej oferty.

   ```PowerShell
   Get-AzureRmVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. W tym samouczku, użyj programu SQL Server 2017 Developer edition (**SQLDEV**). Developer edition za darmo jest licencjonowany do badania i rozwój i ponoszenie kosztów uruchamiania maszyny Wirtualnej.

   ```PowerShell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Z modelu wdrażania usługi Resource Manager pierwszy obiekt tworzona jest grupa zasobów. Użyj [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) , aby utworzyć grupy zasobów platformy Azure i jej zasobach z nazwy grupy zasobów i lokalizacji zdefiniowanej przez zmiennych, które zostały wcześniej zainicjowane.

Wykonaj następujące polecenie cmdlet można utworzyć nowej grupy zasobów.

```PowerShell
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu
Maszyna wirtualna wymaga zasobów magazynu dla dysku systemu operacyjnego i plików danych i dziennika programu SQL Server. Dla uproszczenia możemy utworzyć jeden dysk dla obu. Możesz dołączyć dodatkowe dyski później za pomocą [dysku Azure Dodaj](/powershell/module/azure/add-azuredisk) polecenia cmdlet, aby umieścić danych programu SQL Server i dziennik pliki na dedykowanych dysków. Użyj [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) polecenia cmdlet, aby utworzyć konto magazynu w warstwie standardowa w tej nowej grupy zasobów oraz nazwy konta magazynu, nazwa jednostki Sku magazynu i lokalizacja zdefiniowane przy użyciu zmiennych, które zostały wcześniej zainicjowane .

Wykonaj następujące polecenie cmdlet, aby utworzyć nowe konto magazynu.

```PowerShell
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName `
   -Name $StorageName -SkuName $StorageSku `
   -Kind "Storage" -Location $Location
```

> [!TIP]
> Tworzenie konta magazynu może potrwać kilka minut.

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych
Maszyna wirtualna wymaga liczba zasobów sieciowych do obsługi łączności sieciowej.

* Każda maszyna wirtualna wymaga sieci wirtualnej.
* Sieć wirtualna musi mieć co najmniej jedną podsieć zdefiniowane.
* Interfejs sieciowy musi być zdefiniowany publiczny lub prywatny adres IP.

### <a name="create-a-virtual-network-subnet-configuration"></a>Tworzenie konfiguracji podsieci sieci wirtualnej
Rozpocznij od utworzenia konfiguracji podsieci dla naszych sieci wirtualnej. W naszym samouczku utworzymy podsieci domyślne przy użyciu [AzureRmVirtualNetworkSubnetConfig nowy](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) polecenia cmdlet. Utworzymy naszych konfiguracji podsieci sieci wirtualnej z prefiksem nazwy i adresu podsieci zdefiniowane przy użyciu zmiennych, które zostały wcześniej zainicjowane.

> [!NOTE]
> Można zdefiniować dodatkowe właściwości konfiguracji podsieci sieci wirtualnej za pomocą tego polecenia cmdlet, ale która wykracza poza zakres tego samouczka.

Wykonaj następujące polecenie cmdlet, aby utworzyć konfigurację podsieci wirtualnej.

```PowerShell
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
Następnie należy utworzyć przy użyciu sieci wirtualnej [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) polecenia cmdlet. Tworzenie sieci wirtualnej w nowej grupy zasobów, z nazwy, lokalizacji i adres prefiks zdefiniowany za pomocą zmiennych, które zostały wcześniej zainicjowane, a za pomocą konfiguracji podsieci, który został zdefiniowany w poprzednim kroku.

Wykonaj następujące polecenie cmdlet, aby utworzyć sieć wirtualną.

```PowerShell
$VNet = New-AzureRmVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>Utwórz publiczny adres IP
Teraz, gdy mamy naszych sieci wirtualnej zdefiniowane należy skonfigurować adres IP dla połączenia z maszyną wirtualną. W tym samouczku utworzymy publiczny adres IP za pomocą dynamicznego adresu IP, adresy obsługuje łączność z Internetem. Użyj [AzureRmPublicIpAddress nowy](/powershell/module/azurerm.network/new-azurermpublicipaddress) , aby utworzyć publiczny adres IP adres w grupie zasobów utworzone wcześniej oraz nazwy, lokalizacji, Metoda alokacji i etykieta nazwy domeny DNS zdefiniowane przy użyciu zmiennych tego wcześniej zainicjowana.

> [!NOTE]
> Można zdefiniować dodatkowe właściwości publiczny adres IP za pomocą tego polecenia cmdlet, ale która wykracza poza zakres tego samouczka początkowej. Można również utworzyć za pomocą statycznego adresu prywatny adres lub adres, ale jest to poza zakres tego samouczka.

Wykonaj następujące polecenie cmdlet, aby utworzyć publicznego adresu IP.

```PowerShell
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>Utwórz grupę zabezpieczeń sieci
Aby zabezpieczyć ruch maszyny Wirtualnej i SQL Server, Utwórz grupę zabezpieczeń sieci.

1. Najpierw utwórz reguły grupy zabezpieczeń sieci dla protokołu RDP umożliwić połączenia pulpitu zdalnego.

   ```PowerShell
   $NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. Skonfiguruj reguły grupy zabezpieczeń sieci, która zezwala na ruch na porcie TCP 1433. Dzięki temu połączenia z programem SQL Server za pośrednictwem Internetu.

   ```PowerShell
   $NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. Następnie utwórz grupy zabezpieczeń sieci.

   ```PowerShell
   $Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>Tworzenie interfejsu sieciowego
Firma Microsoft są teraz gotowe do tworzenia interfejsu sieciowego, który będzie używany przez naszych maszyny wirtualnej. Nazywamy [AzureRmNetworkInterface nowy](/powershell/module/azurerm.network/new-azurermnetworkinterface) wcześniej zdefiniowane, aby utworzyć naszych interfejsu sieciowego w wcześniej utworzoną grupę zasobów i nazwy, lokalizacji, podsieci i publicznego adresu IP.

Wykonaj następujące polecenie cmdlet, aby utworzyć interfejsu sieciowego.

```PowerShell
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>Konfigurowanie obiektu maszyny Wirtualnej
Teraz, gdy mamy magazynu i zasobów sieciowych zdefiniowanych możemy przystąpić do definiowania zasoby obliczeniowe dla maszyny wirtualnej. W naszym samouczku będziemy Określ rozmiar maszyny wirtualnej i różne właściwości systemu operacyjnego, określ interfejsu sieciowego, który utworzono wcześniej, zdefiniuj magazynu obiektów blob, a następnie określ dysk systemu operacyjnego.

### <a name="create-the-vm-object"></a>Tworzenie obiektu maszyny Wirtualnej
Rozpocznij od określania rozmiaru maszyny wirtualnej. W tym samouczku będziemy określania DS13. Nazywamy [AzureRmVMConfig nowy](/powershell/module/azurerm.compute/new-azurermvmconfig) , aby utworzyć obiekt można skonfigurować maszyny wirtualnej o nazwie i rozmiarze zdefiniowane przy użyciu zmiennych, które zostały wcześniej zainicjowane.

Wykonaj następujące polecenie cmdlet, aby utworzyć obiekt maszyny wirtualnej.

```PowerShell
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Utwórz obiekt poświadczeń, aby pomieścić nazwę i hasło dla poświadczeń administratora lokalnego
Zanim firma Microsoft można ustawić właściwości systemu operacyjnego dla maszyny wirtualnej, musimy Podaj poświadczenia dla konta administratora lokalnego jako bezpieczny ciąg. Aby to zrobić, użyj [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) polecenia cmdlet.

Wykonaj następujące polecenie cmdlet i, w oknie żądania poświadczenie programu PowerShell, wpisz nazwę i hasło dla konta administratora lokalnego na maszynie wirtualnej.

```PowerShell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Ustaw właściwości systemu operacyjnego dla maszyny wirtualnej
Teraz możemy przystąpić do ustawiania właściwości systemu operacyjnego maszyny wirtualnej z [AzureRmVMOperatingSystem zestaw](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) polecenia cmdlet, aby ustawić typ systemu operacyjnego jako systemu Windows, wymagają [agenta maszyny wirtualnej](../../extensions/agent-windows.md) do zainstalowania, określ, czy polecenie cmdlet umożliwia automatyczne aktualizacje, a następnie ustaw nazwę maszyny wirtualnej, nazwę komputera i poświadczenia, korzystając ze zmiennych, które zostały wcześniej zainicjowane.

Wykonaj następujące polecenie cmdlet można ustawić właściwości systemu operacyjnego dla maszyny wirtualnej.

```PowerShell
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Dodaj interfejs sieciowy do maszyny wirtualnej
Następnie dodamy interfejsu sieciowego, który utworzono wcześniej do maszyny wirtualnej. Wywołanie [AzureRmVMNetworkInterface Dodaj](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) polecenia cmdlet, aby dodać interfejs sieci przy użyciu zdefiniowanego wcześniej zmiennej interfejs sieci.

Wykonaj następujące polecenie cmdlet, aby ustawić interfejsu sieciowego dla maszyny wirtualnej.

```PowerShell
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Ustaw lokalizację magazynu obiektów blob dysku używanego przez maszynę wirtualną
Następnie ustaw lokalizację magazynu obiektów blob dysku używanego przez maszynę wirtualną, korzystając ze zmiennych, które wcześniej zdefiniowane.

Wykonaj następujące polecenie cmdlet, aby ustawić lokalizacji magazynu obiektów blob.

```PowerShell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Ustaw właściwości dysku dla maszyny wirtualnej systemu operacyjnego
Następnie właściwości systemu operacyjnego dysku dla maszyny wirtualnej. Użyj [AzureRmVMOSDisk zestaw](/powershell/module/azurerm.compute/set-azurermvmosdisk) polecenia cmdlet, aby określić, że system operacyjny dla maszyny wirtualnej będą działać z obrazu do określania buforowanie, aby odczytać tylko (ponieważ program SQL Server jest instalowany na tym samym dysku), definiowania maszyny wirtualnej Nazwa i zdefiniowane przy użyciu zmiennych, które wcześniej zdefiniowanego dysku systemu operacyjnego.

Wykonaj następujące polecenie cmdlet można ustawić właściwości dysku dla maszyny wirtualnej systemu operacyjnego.

```PowerShell
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Określ obraz platformy dla maszyny wirtualnej
Nasze ostatni krok konfiguracji jest Określ obrazu platformy naszych maszyny wirtualnej. W naszym samouczku używamy najnowsze obrazu programu SQL Server 2016 CTP. Użyj [AzureRmVMSourceImage zestaw](/powershell/module/azurerm.compute/set-azurermvmsourceimage) polecenia cmdlet, aby użyć tego obrazu, zgodnie z definicją w zmiennych, które wcześniej zdefiniowany.

Wykonaj następujące polecenie cmdlet, aby określić obrazu platformy dla maszyny wirtualnej.

```PowerShell
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Tworzenie maszyny wirtualnej z programem SQL
Po zakończeniu kroków konfiguracji, można przystąpić do tworzenia maszyny wirtualnej. Użyj [AzureRmVM nowy](/powershell/module/azurerm.compute/new-azurermvm) polecenia cmdlet, aby utworzyć maszynę wirtualną, korzystając ze zmiennych, które możemy zdefiniowane.

Wykonaj następujące polecenie cmdlet, aby utworzyć maszynę wirtualną.

```PowerShell
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

Tworzenie maszyny wirtualnej.

> [!NOTE]
> Możesz zignorować błąd dotyczący diagnostyki rozruchu. Utworzeniu konta magazynu w warstwie standardowa dla diagnostyki rozruchu, ponieważ podanego konta magazynu dla dysku maszyny wirtualnej to konto magazynu premium.

## <a name="install-the-sql-iaas-agent"></a>Instalacja agenta SQL IaaS
Maszyny wirtualne programu SQL Server obsługują z funkcji automatycznego zarządzania [rozszerzenia agenta programu SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md). Aby zainstalować agenta na nowej maszynie wirtualnej, uruchom następujące polecenie po jego utworzeniu.

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
Poniższy skrypt zawiera pełną skrypt programu PowerShell na potrzeby tego samouczka. Przyjęto założenie, że masz już ustawienia subskrypcji platformy Azure do użycia z **Connect-AzureRmAccount** i **Select-AzureRmSubscription** poleceń.

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
Po utworzeniu maszyny wirtualnej, można:

- Połączenie z maszyną wirtualną przy użyciu pulpitu zdalnego (RDP).
- Skonfiguruj ustawienia programu SQL Server w portalu dla maszyny Wirtualnej, w tym:
   - [Ustawienia magazynu](virtual-machines-windows-sql-server-storage-configuration.md) 
   - [Automatyczne zarządzanie zadania](virtual-machines-windows-sql-server-agent-extension.md)
- [Konfiguracja połączenia](virtual-machines-windows-sql-connect.md).
- Połączenia klientów i aplikacji do nowego wystąpienia programu SQL Server.

