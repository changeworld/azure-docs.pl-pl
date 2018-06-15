---
title: Tworzenie maszyny wirtualnej programu SQL Server w programie Azure PowerShell (klasyczne) | Dokumentacja firmy Microsoft
description: Zawiera kroki i skryptów programu PowerShell do tworzenia maszyny Wirtualnej platformy Azure z obrazów Galeria maszyny wirtualnej programu SQL Server. W tym temacie używa trybu klasycznego wdrożenia.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
tags: azure-service-management
ms.assetid: b73be387-9323-4e08-be53-6e5928e3786e
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/07/2017
ms.author: jroth
ms.openlocfilehash: 66f44e27562f33373e0b67fe6e0ebf9c6bf99e03
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29399665"
---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>Aprowizowanie maszyny wirtualnej programu SQL Server przy użyciu programu Azure PowerShell (klasyczne)

W tym artykule przedstawiono kroki umożliwiające tworzenie maszyny wirtualnej programu SQL Server na platformie Azure przy użyciu poleceń cmdlet programu PowerShell.

> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../azure-resource-manager/resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.

Wersja usługi Resource Manager w tym temacie, dla [Aprowizowanie maszyny wirtualnej programu SQL Server za pomocą Menedżera zasobów Azure PowerShell](../sql/virtual-machines-windows-ps-sql-create.md).

### <a name="install-and-configure-powershell"></a>Instalowanie i konfigurowanie programu PowerShell:
1. Jeśli nie masz konta platformy Azure, odwiedź stronę [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
2. [Pobierz i zainstaluj najnowsze poleceń programu PowerShell Azure](/powershell/azure/overview).
3. Uruchom program Windows PowerShell i połącz go z subskrypcją platformy Azure z **Add-AzureAccount** polecenia.

   ```powershell
   Add-AzureAccount
   ```

## <a name="determine-your-target-azure-region"></a>Określić urządzenie docelowe region platformy Azure

Maszyny wirtualnej programu SQL Server będzie obsługiwana w systemie usługi chmury, która znajduje się w określonym regionie Azure. Poniższe kroki ułatwiają ustalenie z regionu, konta magazynu i w chmurze usługi, który będzie używany w pozostałej części samouczka.

1. Określ centrum danych, który ma być używany do hostowania maszyną Wirtualną programu SQL Server. Następujące polecenia programu PowerShell Wyświetla listę nazw dostępnych regionów.

   ```powershell
   (Get-AzureLocation).Name
   ```

2. Po wyłaniają preferowanych lokalizacji, ustaw zmienną o nazwie **$dcLocation** do tego regionu. Na przykład następujące polecenie ustawia region "Wschodnie nam":

   ```powershell
   $dcLocation = "East US"
   ```

## <a name="set-your-subscription-and-storage-account"></a>Ustaw konto subskrypcji i magazynu

1. Określ subskrypcję platformy Azure, które będą używane dla nowej maszyny wirtualnej.

   ```powershell
   (Get-AzureSubscription).SubscriptionName
   ```

2. Przypisz urządzenie docelowe subskrypcji platformy Azure do **$subscr** zmiennej. Następnie ustaw jako Twojej bieżącej subskrypcji platformy Azure.

   ```powershell
   $subscr="<subscription name>"
   Select-AzureSubscription -SubscriptionName $subscr –Current
   ```

3. Następnie sprawdź, czy istniejących kont magazynu. Poniższy skrypt wyświetla wszystkie konta magazynu, które istnieją w wybranym regionie:

   ```powershell
   (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName
   ```

   > [!NOTE]
   > Jeśli potrzebujesz nowe konto magazynu, należy najpierw utworzyć nazwę konta magazynu liter wszystkich przy użyciu polecenia New-AzureStorageAccount, jak w poniższym przykładzie: `New-AzureStorageAccount -StorageAccountName "<storage account name>" -Location $dcLocation`

4. Nazwa konta magazynu docelowego, aby przypisać **$staccount**. Następnie użyj **AzureSubscription zestaw** subskrypcji i bieżącego konta magazynu.

   ```powershell
   $staccount="<storage account name>"
   Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount
   ```

## <a name="select-a-sql-server-virtual-machine-image"></a>Wybierz obraz maszyny wirtualnej programu SQL Server

1. Dowiedz się na liście dostępnych obrazów maszyn wirtualnych serwera SQL z galerii. Tych obrazów, wszystkie mają **ImageFamily** właściwość, która rozpoczyna się od "SQL". Następujące zapytanie Wyświetla dostępne rodziny obrazu do użytkownika, które mają preinstalowany programu SQL Server.

   ```powershell
   Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily
   ```

2. Po znalezieniu rodziny obrazu maszyny wirtualnej w tej rodzinie może istnieć wiele obrazów opublikowanych. Użyj następującego skryptu można znaleźć najnowsze nazwy obrazów opublikowanych maszyny wirtualnej dla rodziny wybranego obrazu (takich jak **programu SQL Server 2016 RTM przedsiębiorstwa w systemie Windows Server 2012 R2**):

   ```powershell
   $family="<ImageFamily value>"
   $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

   echo "Selected SQL Server image name:"
   echo "   $image"
   ```

## <a name="create-the-virtual-machine"></a>Tworzenie maszyny wirtualnej

Na koniec utwórz maszynę wirtualną przy użyciu programu PowerShell:

1. Tworzenie usługi w chmurze do obsługi nowej maszyny Wirtualnej. Należy pamiętać, że istnieje również możliwość zamiast tego użyj istniejącej usługi w chmurze. Utwórz nową zmienną **$svcname** z krótką nazwę usługi w chmurze.

   ```powershell
   $svcname = "<cloud service name>"
   New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation
   ```

2. Określ nazwę maszyny wirtualnej i rozmiarze. Aby uzyskać więcej informacji na temat rozmiarów maszyn wirtualnych, zobacz [rozmiarów maszyn wirtualnych na platformie Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

   ```powershell
   $vmname="<machine name>"
   $vmsize="<Specify one: Large, ExtraLarge, A5, A6, A7, A8, A9, or see the link to the other VM sizes>"
   $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image
   ```

3. Podaj konto administratora lokalnego i hasło.

   ```powershell
   $cred=Get-Credential -Message "Type the name and password of the local administrator account."
   $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
   ```

4. Uruchom następujący skrypt, aby utworzyć maszynę wirtualną.

   ```powershell
   New-AzureVM –ServiceName $svcname -VMs $vm1
   ```

> [!NOTE]
> Dla opcji konfiguracji i dodatkowe informacje, zobacz **Utwórz zestaw poleceń** sekcji [użycia programu Azure PowerShell do tworzenia i wstępnie skonfigurować maszyn wirtualnych z systemem Windows](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="example-powershell-script"></a>Przykładowy skrypt programu PowerShell

Poniższy skrypt zawiera przykład pełną skrypt, który tworzy **programu SQL Server 2016 RTM przedsiębiorstwa w systemie Windows Server 2012 R2** maszyny wirtualnej. Jeśli używasz tego skryptu, należy dostosować początkowej zmienne, w oparciu o poprzednie kroki w tym temacie.

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

## <a name="connect-with-remote-desktop"></a>Uzyskuj dostęp do usług pulpitu zdalnego

1. Tworzenie plików protokołu RDP w folderze dokument bieżącego użytkownika do uruchamiania tych maszyn wirtualnych do ukończenia instalacji:

   ```powershell
   $documentspath = [environment]::getfolderpath("mydocuments")
   Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"
   ```

2. W katalogu dokumenty Uruchom plik RDP. Łączenie się z nazwą użytkownika administratora i hasło podane wcześniej (na przykład, jeśli nazwa użytkownika została VMAdmin, określ "\VMAdmin" jako użytkownik i podaj hasło).

   ```powershell
   cd $documentspath
   .\vm1.rdp
   ```

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>Zakończ konfigurację komputera serwera SQL dla dostępu zdalnego

Po zalogowaniu się do komputera przy użyciu pulpitu zdalnego, należy skonfigurować serwer SQL, na podstawie instrukcji w [kroki związane z konfigurowaniem łączności z serwerem SQL w maszynie Wirtualnej platformy Azure](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

## <a name="next-steps"></a>Kolejne kroki

Można znaleźć dodatkowe instrukcje dotyczące inicjowania obsługi administracyjnej maszyn wirtualnych przy użyciu programu PowerShell w [dokumentacji maszyn wirtualnych](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

W wielu przypadkach następnym krokiem jest migracja bazy danych do tej nowej maszyny Wirtualnej serwera SQL. Aby uzyskać wskazówki dotyczące migracji bazy danych, zobacz [Migrowanie bazy danych do programu SQL Server na maszynie Wirtualnej platformy Azure](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Jeśli interesuje Cię również przy użyciu portalu Azure do utworzenia maszyny wirtualnej SQL, zobacz [inicjowania obsługi maszyny wirtualnej programu SQL Server na platformie Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md). Należy pamiętać, że samouczek, który przeprowadzi Cię przez portal tworzy maszyny wirtualne przy użyciu modelu klasycznego, używane w tym temacie programu PowerShell, a nie zalecanym modelu Resource Manager.

Oprócz tych zasobów, firma Microsoft zaleca przejrzenie [innych zagadnień związanych z programem SQL Server w usłudze Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).
