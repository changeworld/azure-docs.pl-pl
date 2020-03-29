---
title: Tworzenie klasycznej maszyny Wirtualnej programu SQL Server (PowerShell)
description: Zawiera kroki i skrypty programu PowerShell do tworzenia maszyny wirtualnej platformy Azure z obrazami galerii maszyn wirtualnych programu SQL Server. W tym temacie użyto klasycznego trybu wdrażania.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: b73be387-9323-4e08-be53-6e5928e3786e
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/07/2017
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 5bfdcfab37091a5f581ce147c0a6af5ccd8147a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914793"
---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>Aprowizuj maszynę wirtualną programu SQL Server przy użyciu programu Azure PowerShell (classic)

[!INCLUDE [classic-vm-deprecation](../../../../includes/classic-vm-deprecation.md)]

Ten artykuł zawiera kroki dotyczące tworzenia maszyny wirtualnej programu SQL Server na platformie Azure przy użyciu poleceń cmdlet programu PowerShell.

> [!NOTE] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Menedżer zasobów i Klasyczny](../../../azure-resource-manager/management/deployment-models.md). W tym artykule opisano przy użyciu modelu wdrażania klasycznego. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.

W przypadku wersji Menedżera zasobów w tym temacie zobacz [Aprowizowanie maszyny wirtualnej programu SQL Server przy użyciu programu Azure PowerShell Resource Manager](../sql/virtual-machines-windows-ps-sql-create.md).

### <a name="install-and-configure-powershell"></a>Zainstaluj i skonfiguruj program PowerShell:
1. Jeśli nie masz konta platformy Azure, odwiedź stronę [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
2. [Pobierz i zainstaluj najnowsze polecenia programu Azure PowerShell](/powershell/azure/overview).
3. Uruchom program Windows PowerShell i połącz go z subskrypcją platformy Azure za pomocą polecenia **Add-AzureAccount.**

   ```powershell
   Add-AzureAccount
   ```

## <a name="determine-your-target-azure-region"></a>Określanie docelowego regionu platformy Azure

Maszyna wirtualna programu SQL Server będzie hostowana w usłudze w chmurze, która znajduje się w określonym regionie platformy Azure. Poniższe kroki pomagają określić region, konto magazynu i usługę w chmurze, które będą używane w pozostałej części samouczka.

1. Określ centrum danych, którego chcesz używać do hostowania maszyny Wirtualnej programu SQL Server. Następujące polecenie programu PowerShell wyświetla listę dostępnych nazw regionów.

   ```powershell
   (Get-AzureLocation).Name
   ```

2. Po zidentyfikowaniu preferowanej lokalizacji ustaw zmienną o nazwie **$dcLocation** do tego regionu. Na przykład następujące polecenie ustawia region na "Wschodnie stany USA":

   ```powershell
   $dcLocation = "East US"
   ```

## <a name="set-your-subscription-and-storage-account"></a>Ustawianie konta subskrypcji i magazynu

1. Określ subskrypcję platformy Azure, która będzie używana dla nowej maszyny wirtualnej.

   ```powershell
   (Get-AzureSubscription).SubscriptionName
   ```

2. Przypisz docelową subskrypcję platformy Azure do zmiennej **$subscr.** Następnie ustaw to jako bieżącą subskrypcję platformy Azure.

   ```powershell
   $subscr="<subscription name>"
   Select-AzureSubscription -SubscriptionName $subscr –Current
   ```

3. Następnie sprawdź, czy nie ma istniejących kont magazynu. W poniższym skrypcie są wyświetlane wszystkie konta magazynu, które istnieją w wybranym regionie:

   ```powershell
   (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName
   ```

   > [!NOTE]
   > Jeśli potrzebujesz nowego konta magazynu, najpierw utwórz nazwę konta magazynu o wszystkich mniejszych literach za pomocą polecenia New-AzureStorageAccount, jak w poniższym przykładzie:`New-AzureStorageAccount -StorageAccountName "<storage account name>" -Location $dcLocation`

4. Przypisz nazwę docelowego konta magazynu do **$staccount**. Następnie użyj **Set-AzureSubscription,** aby ustawić konto subskrypcji i bieżącego magazynu.

   ```powershell
   $staccount="<storage account name>"
   Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount
   ```

## <a name="select-a-sql-server-virtual-machine-image"></a>Wybieranie obrazu maszyny wirtualnej programu SQL Server

1. Znajdź listę obrazów dostępnych maszyn wirtualnych programu SQL Server z galerii. Wszystkie te obrazy mają **ImageFamily** właściwość, która zaczyna się od "SQL". W poniższej kwerendzie jest wyświetlana rodzina obrazów, które mają preinstalowany program SQL Server.

   ```powershell
   Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily
   ```

2. Po znalezieniu rodziny obrazów maszyny wirtualnej w tej rodzinie może znajdować się wiele opublikowanych obrazów. Użyj następującego skryptu, aby znaleźć najnowszą opublikowaną nazwę obrazu maszyny wirtualnej dla wybranej rodziny obrazów (na przykład **SQL Server 2016 RTM Enterprise w systemie Windows Server 2012 R2):**

   ```powershell
   $family="<ImageFamily value>"
   $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

   echo "Selected SQL Server image name:"
   echo "   $image"
   ```

## <a name="create-the-virtual-machine"></a>Tworzenie maszyny wirtualnej

Na koniec utwórz maszynę wirtualną za pomocą programu PowerShell:

1. Utwórz usługę w chmurze do obsługi nowej maszyny Wirtualnej. Należy zauważyć, że jest również możliwe użycie istniejącej usługi w chmurze zamiast tego. Utwórz nową zmienną **$svcname** o krótkiej nazwie usługi w chmurze.

   ```powershell
   $svcname = "<cloud service name>"
   New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation
   ```

2. Określ nazwę maszyny wirtualnej i rozmiar. Aby uzyskać więcej informacji na temat rozmiarów maszyn wirtualnych, zobacz [Rozmiary maszyn wirtualnych dla platformy Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

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
> Aby uzyskać dodatkowe opcje wyjaśnień i konfiguracji, zobacz sekcję **Tworzenie zestawu poleceń** w [programie Azure PowerShell do tworzenia i konfigurowania maszyn wirtualnych opartych na systemie Windows.](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="example-powershell-script"></a>Przykładowy skrypt programu PowerShell

Poniższy skrypt zawiera przykład pełnego skryptu, który tworzy maszynę wirtualną **RTM Enterprise programu SQL Server 2016 na komputerze wirtualnym systemu Windows Server 2012 R2.** Jeśli używasz tego skryptu, należy dostosować zmienne początkowe na podstawie poprzednich kroków w tym temacie.

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

## <a name="connect-with-remote-desktop"></a>Łączenie się z pulpitem zdalnym

1. Utwórz pliki RDP w folderze dokumentu bieżącego użytkownika, aby uruchomić te maszyny wirtualne w celu ukończenia instalacji:

   ```powershell
   $documentspath = [environment]::getfolderpath("mydocuments")
   Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"
   ```

2. W katalogu dokumentów uruchom plik RDP. Połącz się z nazwą użytkownika administratora i hasłem podanym wcześniej (na przykład, jeśli nazwa użytkownika to VMAdmin, określ "\VMAdmin" jako użytkownika i podaj hasło).

   ```powershell
   cd $documentspath
   .\vm1.rdp
   ```

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>Ukończ konfigurację komputera SQL Server dla dostępu zdalnego

Po zalogowaniu się do komputera za pomocą pulpitu zdalnego skonfiguruj program SQL Server na podstawie instrukcji opisanych w [instrukcjach dotyczących konfigurowania łączności programu SQL Server na maszynie Wirtualnej platformy Azure.](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm)

## <a name="next-steps"></a>Następne kroki

Dodatkowe instrukcje dotyczące inicjowania obsługi administracyjnej maszyn wirtualnych za pomocą programu PowerShell można znaleźć w [dokumentacji maszyn wirtualnych.](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

W wielu przypadkach następnym krokiem jest migracja baz danych do tej nowej maszyny Wirtualnej programu SQL Server. Aby uzyskać wskazówki dotyczące migracji bazy danych, zobacz [Migrowanie bazy danych do programu SQL Server na maszynie Wirtualnej platformy Azure](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Jeśli chcesz również utworzyć maszyny wirtualne SQL za pomocą portalu Azure, zobacz [Inicjowanie obsługi administracyjnej maszyny wirtualnej programu SQL Server na platformie Azure.](../sql/virtual-machines-windows-portal-sql-server-provision.md) Należy zauważyć, że samouczek, który przeprowadzi Cię przez portal tworzy maszyny wirtualne przy użyciu zalecanego modelu Menedżera zasobów, a nie model klasyczny używany w tym temacie programu PowerShell.

Oprócz tych zasobów zaleca się [przejrzenie innych tematów związanych z uruchamianiem programu SQL Server w programie Azure Virtual Machines.](../sql/virtual-machines-windows-sql-server-iaas-overview.md)
