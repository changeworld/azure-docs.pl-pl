---
title: Tworzenie maszyny wirtualnej programu SQL Server w programie Azure PowerShell (klasyczny) | Dokumentacja firmy Microsoft
description: Zawiera kroki i skryptów programu PowerShell do tworzenia maszyny Wirtualnej platformy Azure za pomocą obrazów w galerii maszyn wirtualnych programu SQL Server. Ten temat korzysta z trybu klasycznego wdrożenia.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: b73be387-9323-4e08-be53-6e5928e3786e
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/07/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ad8b59a9290c533a3687b5ff8956d8682fb6d9e9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60607834"
---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>Aprowizowanie maszyny wirtualnej programu SQL Server przy użyciu programu Azure PowerShell (wersja klasyczna)

Ten artykuł zawiera instrukcje dotyczące sposobu tworzenia maszyny wirtualnej programu SQL Server na platformie Azure przy użyciu poleceń cmdlet programu PowerShell.

> [!IMPORTANT] 
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [Usługi Resource Manager i Model Klasyczny](../../../azure-resource-manager/resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.

Wersja usługi Resource Manager w tym temacie, zobacz [udostępnianie maszyny wirtualnej programu SQL Server przy użyciu programu Azure PowerShell Resource Manager](../sql/virtual-machines-windows-ps-sql-create.md).

### <a name="install-and-configure-powershell"></a>Instalowanie i konfigurowanie programu PowerShell:
1. Jeśli nie masz konta platformy Azure, odwiedź stronę [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
2. [Pobierz i zainstaluj najnowsze polecenia programu Azure PowerShell](/powershell/azure/overview).
3. Uruchom program Windows PowerShell i połącz go z subskrypcji platformy Azure za pomocą **Add-AzureAccount** polecenia.

   ```powershell
   Add-AzureAccount
   ```

## <a name="determine-your-target-azure-region"></a>Określić Twoim docelowym regionem świadczenia usługi Azure

Z maszyną wirtualną programu SQL Server będzie hostowana w usłudze w chmurze, która znajduje się w określonym regionie platformy Azure. Poniższe kroki pomocne podczas określania Twojego regionu, konto magazynu i w chmurze usługa, która będzie używana w pozostałej części tego samouczka.

1. Określ centrum danych, które chcesz użyć do hostowania maszyny Wirtualnej programu SQL Server. Następujące polecenie programu PowerShell Wyświetla listę nazw dostępnych regionów.

   ```powershell
   (Get-AzureLocation).Name
   ```

2. Po zidentyfikowaniu preferowanych lokalizacji, ustaw zmienną o nazwie **$dcLocation** obsługiwany w tym regionie. Na przykład następujące polecenie ustawia obszar do "Wschodnie stany USA":

   ```powershell
   $dcLocation = "East US"
   ```

## <a name="set-your-subscription-and-storage-account"></a>Ustawianie swojej subskrypcji i konto magazynu

1. Określ subskrypcję platformy Azure, które będą używane dla nowej maszyny wirtualnej.

   ```powershell
   (Get-AzureSubscription).SubscriptionName
   ```

2. Przypisz docelowej subskrypcji platformy Azure do **$subscr** zmiennej. Następnie ustaw jako Twojej bieżącej subskrypcji platformy Azure.

   ```powershell
   $subscr="<subscription name>"
   Select-AzureSubscription -SubscriptionName $subscr –Current
   ```

3. Następnie sprawdź, czy istniejące konta magazynu. Poniższy skrypt wyświetla wszystkie konta magazynu, które istnieją w wybranym regionie:

   ```powershell
   (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName
   ```

   > [!NOTE]
   > Jeśli potrzebujesz nowego konta magazynu, należy najpierw utworzyć nazwę konta magazynu małą wszystkie za pomocą polecenia New-AzureStorageAccount, jak w poniższym przykładzie: `New-AzureStorageAccount -StorageAccountName "<storage account name>" -Location $dcLocation`

4. Nazwa docelowego konta magazynu można przypisać **$staccount**. Następnie użyj **Set-AzureSubscription** można ustawić subskrypcję oraz bieżące konto magazynu.

   ```powershell
   $staccount="<storage account name>"
   Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount
   ```

## <a name="select-a-sql-server-virtual-machine-image"></a>Wybieranie obrazu maszyny wirtualnej programu SQL Server

1. Dowiedz się, lista dostępnych obrazów maszyn wirtualnych programu SQL Server z galerii. Obrazy te wszystkie mają **ImageFamily** właściwość, która rozpoczyna się od "SQL". Następujące zapytanie Wyświetla rodziny obrazów dostępnych dla użytkownika, który serwer SQL Server jest preinstalowany.

   ```powershell
   Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily
   ```

2. Po znalezieniu rodziny obrazu maszyny wirtualnej, może być wiele obrazów opublikowanych w tej rodzinie. Użyj następującego skryptu, aby znaleźć nazwę obrazu usługi najnowszej opublikowanej maszyny wirtualnej, dla rodziny wybranego obrazu (takie jak **programu SQL Server 2016 RTM Enterprise w systemie Windows Server 2012 R2**):

   ```powershell
   $family="<ImageFamily value>"
   $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

   echo "Selected SQL Server image name:"
   echo "   $image"
   ```

## <a name="create-the-virtual-machine"></a>Tworzenie maszyny wirtualnej

Na koniec utwórz maszynę wirtualną przy użyciu programu PowerShell:

1. Utwórz usługę w chmurze do obsługi nowej maszyny Wirtualnej. Należy pamiętać, że użytkownik może również użyć istniejącej usługi w chmurze. Tworzenie nowej zmiennej **$svcname** z krótką nazwę usługi w chmurze.

   ```powershell
   $svcname = "<cloud service name>"
   New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation
   ```

2. Określ nazwę maszyny wirtualnej i rozmiar. Aby uzyskać więcej informacji na temat rozmiarów maszyny wirtualnej, zobacz [rozmiarów maszyn wirtualnych na platformie Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

   ```powershell
   $vmname="<machine name>"
   $vmsize="<Specify one: Large, ExtraLarge, A5, A6, A7, A8, A9, or see the link to the other VM sizes>"
   $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image
   ```

3. Określ konto administratora lokalnego i hasło.

   ```powershell
   $cred=Get-Credential -Message "Type the name and password of the local administrator account."
   $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
   ```

4. Uruchom następujący skrypt, aby utworzyć maszynę wirtualną.

   ```powershell
   New-AzureVM –ServiceName $svcname -VMs $vm1
   ```

> [!NOTE]
> Dla opcji konfiguracji i dodatkowego objaśnienia, zobacz **Utwórz zestaw poleceń** sekcji [użyciu programu Azure PowerShell tworzenie i wstępne konfigurowanie maszyn wirtualnych z systemem Windows](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="example-powershell-script"></a>Przykładowy skrypt programu PowerShell

Poniższy skrypt zawiera przykład kompletny skrypt, który tworzy **programu SQL Server 2016 RTM Enterprise w systemie Windows Server 2012 R2** maszyny wirtualnej. Jeśli używasz tego skryptu, należy dostosować początkową zmiennych, na podstawie poprzednich kroków w tym temacie.

```powershell
# Customize these variables based on your settings and requirements:
$dcLocation = "East US"
$subscr="mysubscription"
$staccount="mystorageaccount"
$family="SQL Server 2016 RTM Enterprise on Windows Server 2012 R2"
$svcname = "mycloudservice"
$vmname="myvirtualmachine"
$vmsize="A5"

# Set the current subscription and storage account
# Comment out the New-AzureStorageAccount line if the account already exists
Select-AzureSubscription -SubscriptionName $subscr –Current
New-AzureStorageAccount -StorageAccountName $staccount -Location $dcLocation
Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

# Select the most recent VM image in this image family:
$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

# Create the new cloud service; comment out this line if cloud service exists already:
New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

# Create the VM config:
$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

# Set administrator credentials:
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

# Create the SQL Server VM:
New-AzureVM –ServiceName $svcname -VMs $vm1
```

## <a name="connect-with-remote-desktop"></a>Połącz przy użyciu pulpitu zdalnego

1. Tworzenie plików RDP w folderze dokumentów bieżącego użytkownika do uruchomienia tych maszyn wirtualnych, aby ukończyć konfigurację:

   ```powershell
   $documentspath = [environment]::getfolderpath("mydocuments")
   Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"
   ```

2. W katalogu dokumenty Uruchom plik RDP. Połącz się z administratorem, nazwę użytkownika i hasło podane wcześniej (na przykład, jeśli nazwa użytkownika był VMAdmin, określ "\VMAdmin" jako użytkownika i podaj hasło).

   ```powershell
   cd $documentspath
   .\vm1.rdp
   ```

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>Zakończ konfigurację komputera serwera SQL dla dostępu zdalnego

Po zalogowaniu się do komputera przy użyciu pulpitu zdalnego, skonfiguruj program SQL Server zgodnie z instrukcjami w [czynności służących do konfigurowania połączenia programu SQL Server w Maszynie wirtualnej platformy Azure](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

## <a name="next-steps"></a>Kolejne kroki

Można znaleźć dodatkowe instrukcje dotyczące inicjowania obsługi administracyjnej maszyn wirtualnych przy użyciu programu PowerShell w programie [dokumentacja dotycząca maszyn wirtualnych](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

W wielu przypadkach następnym krokiem jest migrować swoje bazy danych do tej nowej maszyny Wirtualnej serwera SQL. Aby uzyskać wskazówki dotyczące migracji bazy danych, zobacz [migracji bazy danych do programu SQL Server na Maszynie wirtualnej platformy Azure](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Jeśli interesuje Cię również przy użyciu witryny Azure portal do tworzenia maszyn wirtualnych SQL, zobacz [inicjowania obsługi administracyjnej maszyny wirtualnej programu SQL Server na platformie Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md). Należy pamiętać, że samouczek, który przeprowadzi Cię przez portal tworzy maszyny wirtualne przy użyciu modelu usługi Resource Manager zalecaną zamiast opartych na modelu klasycznym używaną w tym temacie programu PowerShell.

Oprócz tych zasobów, firma Microsoft zaleca, aby przejrzeć [innych zagadnień związanych z programem SQL Server w usłudze Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).
