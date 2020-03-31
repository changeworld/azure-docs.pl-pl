---
title: Korzystanie z usługi Azure Premium Storage z programem SQL Server | Dokumenty firmy Microsoft
description: W tym artykule użyto zasobów utworzonych za pomocą klasycznego modelu wdrażania i zawiera wskazówki dotyczące korzystania z usługi Azure Premium Storage z programem SQL Server uruchomionym na maszynach wirtualnych platformy Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 7ccf99d7-7cce-4e3d-bbab-21b751ab0e88
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/01/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 479f9abc667e20a136da5f6231e78a1e4052f087
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75965669"
---
# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Korzystanie z usługi Azure Premium Storage z programem SQL Server na maszynach wirtualnych

## <a name="overview"></a>Omówienie

[Dyski SSD w wersji Azure w wersji premium](../disks-types.md) to następna generacja magazynu, która zapewnia małe opóźnienia i wysoką przepływność we/wy. Najlepiej sprawdza się w przypadku kluczowych obciążeń intensywnie korzystających z we/wy, takich jak SQL Server na [maszynach wirtualnych IaaS.](https://azure.microsoft.com/services/virtual-machines/)

> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Menedżer zasobów i Klasyczny](../../../azure-resource-manager/management/deployment-models.md). W tym artykule opisano przy użyciu modelu wdrażania klasycznego. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.

Ten artykuł zawiera planowanie i wskazówki dotyczące migracji maszyny wirtualnej z systemem SQL Server w celu użycia magazynu w wersji Premium. Obejmuje to infrastruktury platformy Azure (sieci, magazynu) i gościa maszyny Wirtualnej systemu Windows kroki. W przykładzie w [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) przedstawiono pełną kompleksową migrację end to end, jak przenieść większe maszyny wirtualne, aby skorzystać z ulepszonego lokalnego magazynu dysków SSD za pomocą programu PowerShell.

Ważne jest, aby zrozumieć kompleksowy proces korzystania z usługi Azure Premium Storage z programem SQL Server na maszynach wirtualnych IAAS. Obejmuje to:

* Identyfikacja wymagań wstępnych do korzystania z magazynu w wersji Premium.
* Przykłady wdrażania programu SQL Server w systemie IaaS w magazynie w wersji Premium dla nowych wdrożeń.
* Przykłady migracji istniejących wdrożeń, zarówno serwerów autonomicznych, jak i wdrożeń przy użyciu grup dostępności SQL zawsze włączone.
* Możliwe podejścia migracyjne.
* Pełny przykład end-to-end pokazujący kroki platformy Azure, Windows i SQL Server dla migracji istniejącej implementacji Zawsze włączone.

Aby uzyskać więcej informacji na temat programu SQL Server w maszynach wirtualnych platformy Azure, zobacz [SQL Server w usłudze Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

**Autor:** **Rekonstrukcyjni** Daniel Sol: Luis Carlos Vargas Herring, Sanjay Mishra, Pravin Mital, Juergen Thomas, Gonzalo Ruiz.

## <a name="prerequisites-for-premium-storage"></a>Wymagania wstępne dotyczące pamięci masowej w wersji premium

Istnieje kilka wymagań wstępnych dotyczących korzystania z magazynu w wersji Premium.

### <a name="machine-size"></a>Rozmiar maszyny

Do korzystania z magazynu w wersji Premium należy użyć maszyn wirtualnych serii DS (VM). Jeśli maszyny z serii DS nie były wcześniej używane w usłudze w chmurze, należy usunąć istniejącą maszynę wirtualną, zachować dołączone dyski, a następnie utworzyć nową usługę w chmurze przed ponownym utworzeniem maszyny Wirtualnej jako rozmiar roli DS*. Aby uzyskać więcej informacji na temat rozmiarów maszyn wirtualnych, zobacz [Rozmiary maszyn wirtualnych i usług w chmurze dla platformy Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="cloud-services"></a>Usługi w chmurze

Maszyn wirtualnych DS* z magazynem w wersji Premium można używać tylko wtedy, gdy są one tworzone w nowej usłudze w chmurze. Jeśli używasz programu SQL Server Always On na platformie Azure, odbiornik zawsze włączone odwołuje się do adresu IP wewnętrznego lub zewnętrznego modułu równoważenia obciążenia platformy Azure, który jest skojarzony z usługą w chmurze. W tym artykule koncentruje się na jak przeprowadzić migrację przy zachowaniu dostępności w tym scenariuszu.

> [!NOTE]
> Seria DS* musi być pierwszą maszyną wirtualną wdrożoną w nowej usłudze w chmurze.
>
>

### <a name="regional-vnets"></a>Regionalne sieci wirtualne

W przypadku maszyn wirtualnych DS* należy skonfigurować sieć wirtualną (VNET) hostującą maszyny wirtualne jako regionalne. To "rozszerza" sieci wirtualnej jest umożliwienie większych maszyn wirtualnych, które mają być aprowizacji w innych klastrach i umożliwić komunikację między nimi. Na poniższym zrzucie ekranu wyróżniona lokalizacja pokazuje regionalne wirtualne sieci wirtualne, podczas gdy pierwszy wynik pokazuje "wąską"net wirtualną.

![RegionalVNET (w sieci regionalnej)][1]

Można podnieść bilet pomocy technicznej firmy Microsoft, aby przeprowadzić migrację do regionalnej sieci wirtualnej. Firma Microsoft wprowadza zmiany. Aby zakończyć migrację do regionalnych sieci wirtualnych, zmień właściwość AffinityGroup w konfiguracji sieci. Najpierw wyeksportuj konfigurację sieci w programie PowerShell, a następnie zastąp właściwość **AffinityGroup** w elemencie **VirtualNetworkSite** **właściwością Lokalizacja.** Określ, `Location = XXXX` gdzie `XXXX` jest region platformy Azure. Następnie zaimportuj nową konfigurację.

Na przykład biorąc pod uwagę następującą konfigurację sieci wirtualnej:

```xml
<VirtualNetworkSite name="danAzureSQLnet" AffinityGroup="AzureSQLNetwork">
<AddressSpace>
  <AddressPrefix>10.0.0.0/8</AddressPrefix>
  <AddressPrefix>172.16.0.0/12</AddressPrefix>
</AddressSpace>
<Subnets>
...
</VirtualNetworkSite>
```

Aby przenieść tę tę do regionalnej sieci wirtualnej w Europie Zachodniej, zmień konfigurację na następującą:
```xml
<VirtualNetworkSite name="danAzureSQLnet" Location="West Europe">
<AddressSpace>
  <AddressPrefix>10.0.0.0/8</AddressPrefix>
  <AddressPrefix>172.16.0.0/12</AddressPrefix>
</AddressSpace>
<Subnets>
...
</VirtualNetworkSite>
```

### <a name="storage-accounts"></a>Konta magazynu

Należy utworzyć nowe konto magazynu, które jest skonfigurowane dla magazynu w wersji Premium. Należy zauważyć, że użycie magazynu w wersji Premium jest ustawione na koncie magazynu, a nie na poszczególnych dyskach wirtualnych, jednak podczas korzystania z maszyny wirtualnej z serii DS* można dołączyć dyski wirtualne z kont Premium i Magazynu standardowego. Możesz to rozważyć, jeśli nie chcesz umieszczać dysku VHD systemu operacyjnego na koncie magazynu w wersji Premium.

Następujące **polecenie New-AzureStorageAccountPowerShell** z **typem** "Premium_LRS" tworzy konto magazynu w wersji Premium:

```powershell
$newstorageaccountname = "danpremstor"
New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   
```

### <a name="vhds-cache-settings"></a>Ustawienia pamięci podręcznej VHD

Główną różnicą między tworzeniem dysków, które są częścią konta magazynu w warstwie Premium, jest ustawienie pamięci podręcznej dysku. W przypadku dysków woluminów SQL Server Data zaleca się użycie funkcji **"Buforowanie odczytu".** W przypadku woluminów dziennika transakcji ustawienie pamięci podręcznej dysku powinno być ustawione na "**Brak**". To różni się od zaleceń dla kont magazynu standardowego.

Po podłączeniu veds, ustawienie pamięci podręcznej nie można zmienić. Należy odłączyć i ponownie dołączyć VHD z zaktualizowanym ustawieniem pamięci podręcznej.

### <a name="windows-storage-spaces"></a>Miejsca do magazynowania systemu Windows

Można użyć [miejsca do magazynowania systemu Windows,](https://technet.microsoft.com/library/hh831739.aspx) tak jak w przypadku poprzedniego magazynu standardowego, co umożliwia migrację maszyny Wirtualnej, która już korzysta z miejsc do magazynowania. Przykład w [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) (krok 9 i do przodu) pokazuje kod programu Powershell wyodrębnić i zaimportować maszynę wirtualną z wieloma dołączonymi VHD.

Pule magazynu były używane ze standardowym kontem magazynu platformy Azure w celu zwiększenia przepływności i zmniejszenia opóźnień. Wartość może okazać się wartość w testowaniu pul magazynu z magazynu w wersji Premium dla nowych wdrożeń, ale dodać dodatkową złożoność z konfiguracją magazynu.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>Jak znaleźć dyski wirtualne platformy Azure mapowane do pul magazynu

Ponieważ istnieją różne zalecenia dotyczące ustawiania pamięci podręcznej dla dołączonych dysków VHD, możesz zdecydować się skopiować dyski VHD na konto magazynu w warstwie Premium. Jednak po ponownym dołączeniu ich do nowej maszyny Wirtualnej z serii DS może być konieczne zmodyfikowanie ustawień pamięci podręcznej. Łatwiej jest zastosować ustawienia pamięci podręcznej w warstwie Premium Storage zalecane, gdy masz oddzielne dyski VHD dla plików danych SQL i plików dziennika (a nie jeden dysk WIRTUALNY, który zawiera oba).

> [!NOTE]
> Jeśli masz dane i pliki dziennika programu SQL Server na tym samym woluminie, wybrana opcja buforowania zależy od wzorców dostępu we/wy dla obciążeń bazy danych. Tylko testowanie może wykazać, która opcja buforowania jest najlepsza dla tego scenariusza.
>
>

Jeśli jednak używasz miejsca do magazynowania systemu Windows, które są wykonane z wielu dysków VHD, należy spojrzeć na oryginalne skrypty, aby zidentyfikować, które dołączone dyski VHD znajdują się w określonej puli, dzięki czemu można następnie ustawić ustawienia pamięci podręcznej odpowiednio dla każdego dysku.

Jeśli nie masz oryginalnego skryptu dostępne, aby pokazać, które dyski VHD map do puli magazynu, można użyć następujących kroków, aby określić mapowanie puli dysku/magazynu.

Dla każdego dysku należy wykonać następujące czynności:

1. Pobierz listę dysków dołączonych do maszyny Wirtualnej za pomocą polecenia **Get-AzureVM:**

```powershell
Get-AzureVM -ServiceName <servicename> -Name <vmname> | Get-AzureDataDisk
```

1. Zanotuj DiskName i LUN.

    ![Nazwa dyskuAndLUN][2]
1. Pulpit zdalny do maszyny Wirtualnej. Następnie przejdź do **programu Zarządzanie komputerami** | **Dyski Menedżera** | **urządzeń**. Spójrz na właściwości każdego z "Microsoft Virtual Disks"

    ![VirtualDiskProperties (Właściwości wirtualne dyskietki)][3]
1. Numer jednostki LUN w tym miejscu jest odwołaniem do numeru jednostki LUN określonego podczas dołączania dysku VHD do maszyny Wirtualnej.
1. Aby uzyskać dostęp do "Microsoft Virtual Disk", przejdź do karty **Szczegóły,** a następnie na liście **Właściwości** przejdź do **klucza sterownika**. W **value**, należy zwrócić uwagę **na przesunięcie**, który jest 0002 na poniższym zrzucie ekranu. 0002 oznacza PhysicalDisk2, do którego odwołuje się pula magazynu.

    ![VirtualDiskPropertyDetails][4]
1. Dla każdej puli magazynu zrzuć skojarzone dyski:

```powershell
Get-StoragePool -FriendlyName AMS1pooldata | Get-PhysicalDisk
```

![GetStoragePool (Basen GetStoragePool)][5]

Teraz można użyć tych informacji do skojarzenia dołączonych dysków WIRTUALNYCH z dyskami fizycznymi w pulach magazynu.

Po zamapowaniu dysków VHD na dyski fizyczne w pulach magazynu można następnie odłączyć i skopiować je na konto magazynu w warstwie Premium, a następnie dołączyć je z prawidłowym ustawieniem pamięci podręcznej. Zobacz przykład w [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage), kroki od 8 do 12. W tych krokach pokazano, jak wyodrębnić konfigurację dysku VHD podłączoną do maszyny Wirtualnej do pliku CSV, skopiować dyski VHD, zmienić ustawienia pamięci podręcznej konfiguracji dysku i wreszcie ponownie wdrożyć maszynę wirtualną jako maszynę wirtualną z serii DS ze wszystkimi podłączonymi dyskami.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>Przepustowość pamięci masowej maszyn wirtualnych i przepustowość pamięci masowej VHD

Wydajność pamięci masowej zależy od określonego rozmiaru maszyny Wirtualnej DS* i rozmiarów dysków VHD. Maszyny wirtualne mają różne uprawnienia dla liczby wirtualnych, które mogą być dołączone i maksymalną przepustowość, którą obsługują (MB/s). Aby uzyskać określone numery przepustowości, zobacz [Rozmiary maszyn wirtualnych i usług w chmurze dla platformy Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Zwiększone we/wy są osiągane przy większych rozmiarach dysków. Należy wziąć to pod uwagę, gdy myślisz o ścieżce migracji. Aby uzyskać szczegółowe informacje, [zobacz tabelę dla we/wy i typy dysków](../disks-types.md#premium-ssd).

Na koniec należy wziąć pod uwagę, że maszyny wirtualne mają różne maksymalne przepustowość dysku, które obsługują dla wszystkich dysków podłączonych. Przy dużym obciążeniu można nasycić maksymalną przepustowość dysku dostępną dla tego rozmiaru roli maszyny Wirtualnej. Na przykład Standard_DS14 obsługuje do 512 MB/s; w związku z tym z trzech dysków P30 można nasycić przepustowość dysku maszyny Wirtualnej. Ale w tym przykładzie limit przepływności może zostać przekroczony w zależności od kombinacji operacji we/wy odczytu i zapisu.

## <a name="new-deployments"></a>Nowe wdrożenia

W następnych dwóch sekcjach pokazano, jak można wdrożyć maszyny wirtualne programu SQL Server w magazynie w wersji Premium. Jak wspomniano wcześniej, nie musi być konieczne umieszczenie dysku systemu operacyjnego w magazynie w wersji Premium. Możesz to zrobić, jeśli zamierzasz umieścić żadnych intensywnych obciążeń we/wy na dysku VHD systemu operacyjnego.

W pierwszym przykładzie pokazano przy użyciu istniejących obrazów galerii Azure. W drugim przykładzie pokazano, jak używać niestandardowego obrazu maszyny Wirtualnej, który masz na istniejącym koncie magazynu standardowego.

> [!NOTE]
> Te przykłady zakładają, że utworzono już regionalną siecię wirtualną.

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>Tworzenie nowej maszyny Wirtualnej z magazynem w wersji Premium z obrazem galerii

W poniższym przykładzie pokazano, jak umieścić dysk VHD systemu operacyjnego na pamięci masowej w wersji premium i dołączyć dyski VHD magazynu w wersji premium. Można jednak również umieścić dysk systemu operacyjnego na koncie magazynu standardowego, a następnie dołączyć dyski VHD, które znajdują się na koncie magazynu w wersji Premium. Oba scenariusze są zademonstrowane.

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Set up subscription
Set-AzureSubscription -SubscriptionName $mysubscription
Select-AzureSubscription -SubscriptionName $mysubscription -Current  
```

#### <a name="step-1-create-a-premium-storage-account"></a>Krok 1: Tworzenie konta magazynu w klasie premium

```powershell
#Create Premium Storage account, note Type
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  
```

#### <a name="step-2-create-a-new-cloud-service"></a>Krok 2: Tworzenie nowej usługi w chmurze

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>Krok 3: Zarezerwuj usługę w chmurze VIP (opcjonalnie)

```powershell
#check exisitng reserved VIP
Get-AzureReservedIP

$reservedVIPName = "sqlcloudVIP"
New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location
```

#### <a name="step-4-create-a-vm-container"></a>Krok 4: Tworzenie kontenera maszyny Wirtualnej

```powershell
#Generate storage keys for later
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

##Generate storage acc contexts
$xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

#Create container
$containerName = 'vhds'
New-AzureStorageContainer -Name $containerName -Context $xioContext
```

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>Krok 5: Umieszczenie OS VHD w pamięci standardowej lub premium

```powershell
#NOTE: Set up subscription and default storage account which is used to place the OS VHD in

#If you want to place the OS VHD Premium Storage Account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  

#If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
$standardstorageaccountname = "danstdams"

Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname
```

#### <a name="step-6-create-vm"></a>Krok 6: Tworzenie maszyny wirtualnej

```powershell
#Get list of available SQL Server Images from the Azure Image Gallery.
$galleryImage = Get-AzureVMImage | where-object {$_.ImageName -like "*SQL*2014*Enterprise*"}
$image = $galleryImage.ImageName

#Set up Machine Specific Information
$vmName = "dsDan1"
$vnet = "dansvnetwesteur"
$subnet = "SQL"
$ipaddr = "192.168.0.8"

#Remember to change to DS series VM
$newInstanceSize = "Standard_DS1"

#create new Availability Set
$availabilitySet = "cloudmigAVAMS"

#Machine User Credentials
$userName = "myadmin"
$pass = "mycomplexpwd4*"

#Create VM Config
$vmConfigsl = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $image  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

#Add Data and Log Disks to VM Config
#Note the size specified ‘-DiskSizeInGB 1023’, this attaches 2 x P30 Premium Storage Disk Type
#Utilising the Premium Storage enabled Storage account

$vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-data1.vhd"
$vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "logDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-log1.vhd"

#Create VM
$vmConfigsl  | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)  

#Add RDP Endpoint
$EndpointNameRDPInt = "3389"
Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Add-AzureEndpoint -Name "EndpointNameRDP" -Protocol "TCP" -PublicPort "53385" -LocalPort $EndpointNameRDPInt  | Update-AzureVM

#Check VHD storage account, these should be in $newxiostorageaccountname
Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Get-AzureDataDisk
Get-AzureVM -ServiceName $destcloudsvc -Name $vmName |Get-AzureOSDisk
```

### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>Tworzenie nowej maszyny Wirtualnej w celu używania magazynu w wersji Premium z obrazem niestandardowym

W tym scenariuszu pokazano, gdzie masz istniejących obrazów niestandardowych, które znajdują się na koncie magazynu standardowego. Jak wspomniano, jeśli chcesz umieścić OS VHD w magazynie w wersji Premium, musisz skopiować obraz, który istnieje na koncie magazynu standardowego i przenieść je do magazynu w wersji Premium, zanim będzie można go użyć. Jeśli masz obraz w środowisku lokalnym, można również użyć tej metody, aby skopiować go bezpośrednio do konta magazynu w wersji Premium.

#### <a name="step-1-create-storage-account"></a>Krok 1: Tworzenie konta magazynu

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Create Premium Storage account
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

#Standard Storage account
$origstorageaccountname = "danstdams"
```

#### <a name="step-2-create-cloud-service"></a>Krok 2 Tworzenie usługi w chmurze

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-use-existing-image"></a>Krok 3: Użyj istniejącego obrazu

Można użyć istniejącego obrazu. Możesz też [zrobić zdjęcie istniejącej maszyny](../classic/capture-image-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Należy pamiętać, że obraz nie musi być maszyną DS*. Po wyświetleniu obrazu poniższe kroki pokazują, jak skopiować go do konta magazynu w programie Premium za pomocą polecenia **Programu PowerShell Start-AzureStorageBlobCopy.**

```powershell
#Get storage account keys:
#Standard Storage account
$originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
#Premium Storage account
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

#Set up contexts for the storage accounts:
$origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
$destContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  
```

#### <a name="step-4-copy-blob-between-storage-accounts"></a>Krok 4: Kopiowanie obiektu blob między kontami magazynu

```powershell
#Get Image VHD
$myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
$containerName = 'vhds'

#Copy Blob between accounts
$blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
-DestContainer vhds -Destblob "prem-$myImageVHD" `
-Context $origContext -DestContext $destContext  
```

#### <a name="step-5-regularly-check-copy-status"></a>Krok 5: Regularnie sprawdzaj status kopii:

```powershell
$blob | Get-AzureStorageBlobCopyState
```

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>Krok 6: Dodawanie dysku obrazu do repozytorium dysków platformy Azure w ramach subskrypcji

```powershell
$imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
$newimageName = "prem"+"dansoldonorsql2k14"

Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation
```

> [!NOTE]
> Może się okazać, że nawet jeśli raporty stanu jako sukces, nadal można uzyskać błąd dzierżawy dysku. W takim przypadku odczekaj około 10 minut.

#### <a name="step-7--build-the-vm"></a>Krok 7: Tworzenie maszyny Wirtualnej

W tym miejscu budujesz maszynę wirtualną z obrazu i dołączasz dwie dyski wirtualne magazynu premium:

```powershell
$newimageName = "prem"+"dansoldonorsql2k14"
#Set up Machine Specific Information
$vmName = "dansolchild"
$vnet = "westeur"
$subnet = "Clients"
$ipaddr = "192.168.0.41"

#This needs to be a new cloud service
$destcloudsvc = "danregsvcamsxio2"

#Use to DS Series VM
$newInstanceSize = "Standard_DS1"

#create new Availability Set
$availabilitySet = "cloudmigAVAMS3"

#Machine User Credentials
$userName = "myadmin"
$pass = "theM)stC0mplexP@ssw0rd!"


#Create VM Config
$vmConfigsl2 = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $newimageName  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

$vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-Datadisk-1.vhd"
$vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "LogDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-logdisk-1.vhd"



$vmConfigsl2 | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet
```

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Istniejące wdrożenia, które nie korzystają z grup dostępności zawsze włączonych

> [!NOTE]
> W przypadku istniejących wdrożeń najpierw zobacz sekcję [Wymagania wstępne](#prerequisites-for-premium-storage) tego artykułu.

Istnieją różne zagadnienia dotyczące wdrożeń programu SQL Server, które nie używają grup zawsze na dostępność i tych, które to robią. Jeśli nie używasz always on i masz istniejący autonomiczny program SQL Server, możesz uaktualnić do magazynu w wersji Premium przy użyciu nowej usługi w chmurze i konta magazynu. Rozważmy następujące opcje:

* **Utwórz nową maszynę wirtualną programu SQL Server**. Można utworzyć nową maszynę wirtualną programu SQL Server, która używa konta magazynu w wersji Premium, zgodnie z dokumentami w nowych wdrożeniach. Następnie powróć kopię zapasową i przywróć konfigurację programu SQL Server i bazy danych użytkowników. Aplikacja musi zostać zaktualizowana w celu odwołania się do nowego programu SQL Server, jeśli jest dostępny wewnętrznie lub zewnętrznie. Należy skopiować wszystkie obiekty "out of db", tak jakby przeprowadzano migrację programu SQL Server side by side (SxS). Obejmuje to obiekty, takie jak logowania, certyfikaty i połączone serwery.
* **Migrowanie istniejącej maszyny Wirtualnej programu SQL Server**. Wymaga to przełączenia maszyny Wirtualnej programu SQL Server do trybu offline, a następnie przeniesienia jej do nowej usługi w chmurze, która obejmuje kopiowanie wszystkich dołączonych dysków VHD do konta magazynu w ramach programu Premium. Gdy maszyna wirtualna jest w trybie online, aplikacja odwołuje się do nazwy hosta serwera, jak poprzednio. Należy pamiętać, że rozmiar istniejącego dysku wpływa na charakterystykę wydajności. Na przykład dysk 400 GB jest zaokrąglany w górę do P20. Jeśli wiesz, że nie wymaga tej wydajności dysku, można odtworzyć maszynę wirtualną z serii DS i dołączyć dyski wirtualne magazynu premium o wymaganej specyfikacji rozmiaru/wydajności. Następnie można odłączyć i ponownie dołączyć pliki BAZY DANYCH SQL.

> [!NOTE]
> Podczas kopiowania dysków VHD należy pamiętać o rozmiarze, w zależności od rozmiaru oznacza, jaki typ dysku magazynu w wersji Premium należą do, określa specyfikację wydajności dysku. Platforma Azure zaokrągla do najbliższego rozmiaru dysku, więc jeśli masz dysk 400GB, jest zaokrąglana w górę do P20. W zależności od istniejących wymagań we/wy vHD systemu operacyjnego może nie być konieczne migrowanie tego do konta magazynu w wersji Premium.

Jeśli program SQL Server jest dostępny zewnętrznie, zmieni się program VIP usługi w chmurze. Należy również zaktualizować punkty końcowe, listy ACL i ustawienia DNS.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Istniejące wdrożenia korzystające z grup dostępności zawsze włączone

> [!NOTE]
> W przypadku istniejących wdrożeń najpierw zobacz sekcję [Wymagania wstępne](#prerequisites-for-premium-storage) tego artykułu.

Początkowo w tej sekcji przyjrzymy się, jak zawsze na interakcję z siecią Azure. Następnie rozłamujemy migracje na dwa scenariusze: migracje, w których niektóre przestoje mogą być tolerowane, oraz migracje, w których należy osiągnąć minimalny czas przestoju.

Lokalne grupy dostępności programu SQL Server Always On Availability używają odbiornika lokalnego, który rejestruje wirtualną nazwę DNS wraz z adresem IP współużytkowany przez jeden lub więcej serwerów SQL. Gdy klienci łączą się, są kierowani za pośrednictwem adresu IP odbiornika do podstawowego programu SQL Server. Jest to serwer, który jest właścicielem zawsze na zasobie IP w tym czasie.

![WdrożeniaZarządzazazachomożknięć włączone][6]

Na platformie Microsoft Azure można mieć tylko jeden adres IP przypisany do karty sieciowej na maszynie wirtualnej, więc w celu osiągnięcia tej samej warstwy abstrakcji jako lokalnie, platforma Azure wykorzystuje adres IP, który jest przypisany do wewnętrznych/zewnętrznych modułów równoważenia obciążenia (ILB/ELB). Zasób IP współużytkowane przez serwery jest ustawiony na ten sam adres IP co ILB/ELB. Jest to publikowane w systemie DNS, a ruch klienta jest przekazywany przez klucz równoważenia obciążenia/elb do repliki programu SQL Server. Moduł ILB/ELB wie, który program SQL Server jest podstawowy, ponieważ używa sond do sondowania zasobu Zawsze na dysku IP. W poprzednim przykładzie sonduje każdy węzeł, który ma punkt końcowy, do którego odwołuje się ELB/ILB, w zależności od odpowiedzi jest podstawowy program SQL Server.

> [!NOTE]
> ModułY równoważenia obciążenia sieciowego i elb są przypisane do określonej usługi w chmurze platformy Azure, dlatego każda migracja w chmurze na platformie Azure najprawdopodobniej oznacza, że zmienia się adres IP modułu równoważenia obciążenia.
>
>

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>Migracja wdrożeń Always On, które mogą umożliwić pewne przestoje

Istnieją dwie strategie migracji zawsze włączone wdrożenia, które pozwalają na pewne przestoje:

1. **Dodawanie kolejnych replik pomocniczych do istniejącego klastra zawsze włączone**
2. **Migrowanie do nowego klastra zawsze włączone**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. Dodawanie większej liczby replik pomocniczych do istniejącego klastra zawsze włączone

Jedną ze strategii jest dodanie kolejnych pomocniczych do grupy zawsze na dostępności. Należy dodać je do nowej usługi w chmurze i zaktualizować odbiornika za pomocą nowego adresu IP modułu równoważenia obciążenia.

##### <a name="points-of-downtime"></a>Punkty przestoju:

* Sprawdzanie poprawności klastra.
* Testowanie zawsze na pracy awaryjnej dla nowych pomocniczych.

Jeśli używasz pule magazynu systemu Windows w ramach maszyny Wirtualnej dla wyższej przepływności we/wy, są one przełączane do trybu offline podczas sprawdzania poprawności pełnego klastra. Test sprawdzania poprawności jest wymagany podczas dodawania węzłów do klastra. Czas potrzebny do uruchomienia testu może się różnić, więc należy przetestować to w reprezentatywnym środowisku testowym, aby uzyskać przybliżony czas, jak długo to trwa.

Należy aprowizować czas, w którym można wykonać ręczne testowanie pracy awaryjnej i chaosu na nowo dodanych węzłach, aby zapewnić funkcje zawsze na wysokiej dostępności zgodnie z oczekiwaniami.

![WdrażanieZachodza w 2][7]

> [!NOTE]
> Należy zatrzymać wszystkie wystąpienia programu SQL Server, gdzie pule magazynu są używane przed uruchomieniu sprawdzania poprawności.
>
> ##### <a name="high-level-steps"></a>Kroki ogólne
>

1. Utwórz dwa nowe serwery SQL w nowej usłudze w chmurze z dołączonym magazynem premium.
2. Kopiuj za pomocą pełnych kopii zapasowych i przywracaj za pomocą **norecovery**.
3. Kopiuj obiekty zależne "z bazy danych użytkownika", takie jak loginy itp.
4. Utwórz nowy wewnętrzny moduł równoważenia obciążenia (ILB) lub użyj zewnętrznego modułu równoważenia obciążenia (ELB), a następnie skonfiguruj punkty końcowe z równoważenia obciążenia w obu nowych węzłach.

   > [!NOTE]
   > Sprawdź, czy wszystkie węzły mają prawidłową konfigurację punktu końcowego przed kontynuowaniem
   >
   >
5. Zatrzymaj dostęp użytkownika/aplikacji do programu SQL Server (w przypadku korzystania z puli magazynu).
6. Zatrzymaj usługi SQL Server Engine Services we wszystkich węzłach (w przypadku korzystania z pul magazynu).
7. Dodaj nowe węzły do klastra i uruchom pełną weryfikację.
8. Po pomyślnym zakończeniu sprawdzania poprawności uruchom wszystkie usługi SQL Server Services.
9. Tworzenie kopii zapasowych dzienników transakcji i przywracanie baz danych użytkowników.
10. Dodaj nowe węzły do grupy Zawsze dostępności i umieść replikację w **synchroniczności**.
11. Dodaj zasób adresu IP nowego przyjemnego/ELB usługi w chmurze za pośrednictwem programu PowerShell dla zawsze włączone na podstawie przykładu wielu lokacji w [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage). W klastrze systemu Windows ustaw **możliwych właścicieli** zasobu **Adres IP** na nowe stare węzły. Zobacz sekcję "Dodawanie zasobu adresu IP w tej samej podsieci" [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
12. Przewija się w stan failover do jednego z nowych węzłów.
13. Wykonuj nowe węzły Automatycznie partnerów trybu failover i testowania pracy awaryjnej.
14. Usuń oryginalne węzły z grupy dostępności.

##### <a name="advantages"></a>Zalety

* Nowe serwery SQL mogą być testowane (SQL Server i aplikacja) przed ich dodaniem do Always On.
* Można zmienić rozmiar maszyny Wirtualnej i dostosować magazyn do dokładnych wymagań. Jednak byłoby korzystne, aby zachować wszystkie ścieżki pliku SQL takie same.
* Można kontrolować, kiedy zostanie rozpoczęty transfer kopii zapasowych bazy danych do replik pomocniczych. To różni się od używania polecenia Azure **Start-AzureStorageBlobCopy** do kopiowania vhds, ponieważ jest to kopia asynchroniczną.

##### <a name="disadvantages"></a>Wady

* Podczas korzystania z pul magazynu systemu Windows występuje przestój klastra podczas sprawdzania poprawności pełnego klastra dla nowych dodatkowych węzłów.
* W zależności od wersji programu SQL Server i istniejącej liczby replik pomocniczych dodawanie większej liczby replik pomocniczych może być nie możliwe bez usuwania istniejących pomocniczych plików.
* Może to być długi czas transferu danych SQL podczas konfigurowania pomocniczych.
* Podczas migracji wiąże się dodatkowe koszty, gdy nowe maszyny działają równolegle.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. Migracja do nowego klastra zawsze włączone

Inną strategią jest utworzenie zupełnie nowego klastra zawsze włączone z zupełnie nowymi węzłami w nowej usłudze w chmurze, a następnie przekierowanie klientów do korzystania z niej.

##### <a name="points-of-downtime"></a>Punkty przestoju

Podczas przesyłania aplikacji i użytkowników do nowego odbiornika Zawsze włączone występują przestoje. Czas przestoju zależy od:

* Czas, który został przesuń, aby przywrócić końcowe kopie zapasowe dziennika transakcji do baz danych na nowych serwerach.
* Czas pracy, aby zaktualizować aplikacje klienckie do korzystania z nowego odbiornika zawsze włączone.

##### <a name="advantages"></a>Zalety

* Można przetestować rzeczywiste zmiany kompilacji środowiska produkcyjnego, programu SQL Server i systemu operacyjnego.
* Istnieje możliwość dostosowania magazynu i potencjalnie zmniejszyć rozmiar maszyny Wirtualnej. Może to doprowadzić do obniżenia kosztów.
* Podczas tego procesu można zaktualizować kompilację lub wersję programu SQL Server. Można również uaktualnić system operacyjny.
* Poprzedni zawsze na klastrze może działać jako stały obiekt docelowy wycofywania.

##### <a name="disadvantages"></a>Wady

* Należy zmienić nazwę DNS odbiornika, jeśli chcesz, aby oba klastry zawsze włączone były uruchomione jednocześnie. Spowoduje to dodanie kosztów administracyjnych podczas migracji, ponieważ ciągi aplikacji klienckiej muszą odzwierciedlać nową nazwę odbiornika.
* Należy zaimplementować mechanizm synchronizacji między dwoma środowiskami, aby zachować je tak blisko, jak to możliwe, aby zminimalizować wymagania synchronizacji końcowej przed migracją.
* Podczas migracji jest dodatkowy koszt, gdy masz uruchomione nowe środowisko.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>Migracja zawsze włączonych wdrożeń przy minimalnych przestojach

Istnieją dwie strategie migracji wdrożeń Always On dla minimalnych przestojów:

1. **Korzystanie z istniejącego dodatkowego: jednoustrojowego**
2. **Wykorzystanie istniejących replik pomocniczych: wiele lokacji**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. Wykorzystanie istniejącego dodatkowego: single-site

Jedną ze strategii minimalnego przestoju jest podjęcie istniejącej chmury pomocniczej i usunięcie go z bieżącej usługi w chmurze. Następnie skopiuj dyski VHD do nowego konta magazynu w wersji Premium i utwórz maszynę wirtualną w nowej usłudze w chmurze. Następnie zaktualizuj odbiornik w klastrowaniu i pracy awaryjnej.

##### <a name="points-of-downtime"></a>Punkty przestoju

* Jest przestoju podczas aktualizowania węzła końcowego z punktu końcowego z równoważenia obciążenia.
* Ponowne połączenie klienta może być opóźnione w zależności od konfiguracji klienta/DNS.
* Istnieje dodatkowy przestój, jeśli zdecydujesz się przetraktować grupę Zawsze na klastrze w trybie offline, aby zamienić adresy IP. Można tego uniknąć przy użyciu zależności OR i możliwych właścicieli dla dodanego zasobu adresu IP. Zobacz sekcję "Dodawanie zasobu adresu IP w tej samej podsieci" [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).

> [!NOTE]
> Jeśli chcesz, aby dodany węzeł był uczestniczyć w jako zawsze włączony partner trybu failover, musisz dodać punkt końcowy platformy Azure z odwołaniem do zestawu z równoważeniem obciążenia. Po uruchomieniu polecenia **Add-AzureEndpoint** w tym celu bieżące połączenia pozostają otwarte, ale nowe połączenia z odbiornikiem nie mogą być ustanawiane, dopóki moduł równoważenia obciążenia nie zostanie zaktualizowany. W testach zaobserwowano, że trwa to 90-120sekund, to powinno być testowane.

##### <a name="advantages"></a>Zalety

* Podczas migracji nie poniesiono żadnych dodatkowych kosztów.
* Migracja jeden do jednego.
* Zmniejszona złożoność.
* Umożliwia zwiększenie we/wy we/wy z jednostek SKU magazynu w jakości Premium. Gdy dyski są odłączone od maszyny Wirtualnej i kopiowane do nowej usługi w chmurze, narzędzie innej firmy może służyć do zwiększenia rozmiaru dysku VHD, co zapewnia wyższą przepustowość. Aby zwiększyć rozmiary VHD, zobacz tę [dyskusję na forum](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows).

##### <a name="disadvantages"></a>Wady

* Podczas migracji występuje tymczasowa utrata wysokiej pozna i odzyskiwania po awarii.
* Ponieważ jest to migracja 1:1, należy użyć minimalnego rozmiaru maszyny Wirtualnej, który obsługuje liczbę wirtualnych, więc może nie być w stanie zmniejszyć rozmiaru maszyn wirtualnych.
* W tym scenariuszu można użyć polecenia Azure **Start-AzureStorageBlobCopy,** który jest asynchroniczne. Nie ma umowy SLA przy wypełnianie kopii. Czas kopii jest różny, podczas gdy zależy to od oczekiwania w kolejce zależy również od ilości danych do przesłania. Czas kopiowania zwiększa się, jeśli transfer przechodzi do innego centrum danych platformy Azure, które obsługuje magazyn w usłudze Premium w innym regionie. Jeśli masz tylko 2 węzły, należy wziąć pod uwagę możliwe środki zaradcze w przypadku, gdy kopia trwa dłużej niż w testach. Może to obejmować następujące pomysły.
  * Dodaj tymczasowy węzeł programu SQL Server dla wysokiej klasy przed migracją z uzgodnionym przestojem.
  * Uruchom migrację poza zaplanowaną konserwacją platformy Azure.
  * Upewnij się, że kworum klastra zostało poprawnie skonfigurowane.  

##### <a name="high-level-steps"></a>Kroki ogólne

Ten dokument nie wykazuje pełnego przykładu końca do końca, jednak [dodatek](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) zawiera szczegółowe informacje, które można wykorzystać do wykonania tego.

![Minimalny czas przestoju][8]

* Zbierz konfigurację dysku i usuń węzeł (nie usuwaj dołączonych dysków VHD).
* Tworzenie konta magazynu w wersji Premium i kopiowanie dysków VHD z konta Magazynu standardowego
* Utwórz nową usługę w chmurze i ponownie wdrożyć SQL2 VM w tej usłudze w chmurze. Utwórz maszynę wirtualną przy użyciu skopiowanego oryginalnego dysku twardego systemu operacyjnego I VHD i dołączając skopiowane dyski VHD.
* Skonfiguruj ILB / ELB i dodaj punkty końcowe.
* Aktualizuj odbiornika przez:
  * Przesuń grupę Zawsze w trybie offline i aktualizuje odbiornik zawsze włączony z nowym adresem IP ILB / ELB.
  * Lub dodanie zasobu adresu IP nowych WEWNĘTRZNEGO USŁUGI W chmurze/ELB za pośrednictwem programu PowerShell do klastrowania systemu Windows. Następnie ustaw możliwe właścicieli zasobu adres IP do zmigrowanego węzła SQL2 i ustaw to jako zależność OR w nazwie sieciowej. Zobacz sekcję "Dodawanie zasobu adresu IP w tej samej podsieci" [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
* Sprawdź konfigurację/propagację dns dla klientów.
* Migrowanie maszyny wirtualnej SQL1 i przechodzenie przez kroki 2–4.
* Jeśli używasz kroków 5ii, dodaj SQL1 jako możliwego właściciela dla dodanego zasobu adresu IP
* Testowanie pracy awaryjnej.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. Wykorzystanie istniejących replik pomocniczych: Wielozadaniowe

Jeśli masz węzły w więcej niż jednym centrum danych platformy Azure (DC) lub jeśli masz środowisko hybrydowe, możesz użyć konfiguracji Zawsze włączone w tym środowisku, aby zminimalizować przestoje.

Podejście polega na zmianie synchronizacji zawsze włączone na synchroniczne dla lokalnego lub pomocniczego kontrolera domeny platformy Azure, a następnie pracy awaryjnej do tego programu SQL Server. Następnie skopiuj dyski VHD na konto magazynu w wersji Premium i ponownie wdrożyć komputer do nowej usługi w chmurze. Zaktualizuj odbiornik, a następnie wróć po awarii.

##### <a name="points-of-downtime"></a>Punkty przestoju

Czas przestoju składa się z czasu do pracy awaryjnej do alternatywnego kontrolera domeny i z powrotem. Zależy to również od konfiguracji klienta/DNS, a ponowne połączenie klienta może być opóźnione.
Rozważmy następujący przykład konfiguracji hybrydowej Zawsze włączone:

![Wielolokalowe1][9]

##### <a name="advantages"></a>Zalety

* Można korzystać z istniejącej infrastruktury.
* Masz możliwość wstępnego uaktualnienia magazynu platformy Azure na dr azure dc pierwszy.
* Magazyn kontrolera domeny platformy DR Azure można ponownie skonfigurować.
* Istnieje co najmniej dwa zadania failover podczas migracji, z wyłączeniem testów pracy awaryjnej.
* Nie trzeba przenosić danych programu SQL Server z kopią zapasową i przywracania.

##### <a name="disadvantages"></a>Wady

* W zależności od dostępu klienta do programu SQL Server może wystąpić zwiększone opóźnienie, gdy program SQL Server jest uruchomiony w alternatywnym kontrolerze domeny dla aplikacji.
* Czas kopiowania dysków VHD do magazynu w wersji Premium może być długi. Może to mieć wpływ na decyzję o utrzymaniu węzła w grupie dostępności. Należy wziąć to pod uwagę, gdy obciążenia pracy intensywnej pracy dziennika są uruchomione podczas migracji jest wymagane, ponieważ węzeł podstawowy musi zachować niezreliktowane transakcje w dzienniku transakcji. W związku z tym może to znacznie wzrosnąć.
* W tym scenariuszu można użyć polecenia Azure **Start-AzureStorageBlobCopy,** który jest asynchroniczne. Po zakończeniu nie ma umowy SLA. Czas kopii jest różny, podczas gdy zależy to od oczekiwania w kolejce, zależy również od ilości danych do przesłania. W związku z tym masz tylko jeden węzeł w drugim centrum danych, należy podjąć kroki ograniczające zagrożenie w przypadku, gdy kopia trwa dłużej niż w testach. Te kroki ograniczające zagrożenie obejmują następujące pomysły:
  * Dodaj tymczasowy drugi węzeł SQL dla wysokiej klasy przed migracją z uzgodnionym przestojem.
  * Uruchom migrację poza zaplanowaną konserwacją platformy Azure.
  * Upewnij się, że kworum klastra zostało poprawnie skonfigurowane.

W tym scenariuszu przyjęto założenie, że instalacja została udokumentowana i wiesz, jak magazyn jest mapowany w celu wprowadzania zmian w celu zapewnienia optymalnych ustawień pamięci podręcznej dysku.

##### <a name="high-level-steps"></a>Kroki ogólne

![Wielozadaniowe2][10]

* Upewnij się, że lokalnie / alternatywne azure DC SQL Server Primary i uczynić go innym partnerem automatycznego przełączania awaryjnego (AFP).
* Zbierz informacje o konfiguracji dysku z sql2 i usuń węzeł (nie usuwaj dołączonych dysków VHD).
* Utwórz konto magazynu w wersji Premium i skopiuj dyski VHD z konta magazynu standardowego.
* Utwórz nową usługę w chmurze i utwórz maszynę wirtualną SQL2 z dołączonymi dyskami magazynu premium.
* Skonfiguruj ILB / ELB i dodaj punkty końcowe.
* Zaktualizuj odbiornik zawsze włączony, podają nowy adres IP ILB / ELB i przetestuj ją w pracy awaryjnej.
* Sprawdź konfigurację DNS.
* Zmień AFP na SQL2, a następnie migruj SQL1 i przejdź przez kroki 2– 5.
* Testowanie pracy awaryjnej.
* Przełączanie AFP z powrotem do SQL1 i SQL2

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>Dodatek: Migrowanie klastra zawsze w klastrze do magazynu w jakości Premium

Poniżej dalsza część artykułu Zawiera szczegółowy przykład konwersji klastra z wieloma lokacjami zawsze na magazyn w wersji Premium. Konwertuje również odbiornika z przy użyciu zewnętrznego modułu równoważenia obciążenia (ELB) do wewnętrznego modułu równoważenia obciążenia (ILB).

### <a name="environment"></a>Środowisko

* Windows 2k12 / SQL 2k12
* 1 Pliki DB na SP
* 2 x Pule magazynów na węzeł

![Dodatek 1][11]

### <a name="vm"></a>Vm:

W tym przykładzie mamy zamiar zademonstrować przejście z ELB do ILB. Elb był dostępny przed ILB, więc pokazuje, jak przełączyć się na ILB podczas migracji.

![Appendix2][12]

### <a name="pre-steps-connect-to-subscription"></a>Kroki wstępne: Łączenie się z subskrypcją

```powershell
Add-AzureAccount

#Set up subscription
Get-AzureSubscription
```

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>Krok 1: Tworzenie nowego konta magazynu i usługi w chmurze

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Storage accounts
#current storage account where the vm to migrate resides
$origstorageaccountname = "danstdams"

#Create Premium Storage account
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

#Generate storage keys for later
$originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

#Generate storage acc contexts
$origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
$xioContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

#Set up subscription and default storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $origstorageaccountname
Select-AzureSubscription -SubscriptionName $mysubscription -Current

#CREATE NEW CLOUD SVC
$vnet = "dansvnetwesteur"

##Existing cloud service
$sourceSvc="dansolSrcAms"

##Create new cloud service
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>Krok 2: Zwiększ dozwolone błędy \<zasobów Opcjonalne>

W przypadku niektórych zasobów należących do grupy zawsze dostępnej dostępności istnieją ograniczenia dotyczące liczby błędów, które mogą wystąpić w okresie, w którym usługa klastrowania próbuje ponownie uruchomić grupę zasobów. Zaleca się zwiększenie tej wartości podczas przechodzenia przez tę procedurę, ponieważ jeśli nie ręcznie przejdziesz w tryb failover i nie uruchomisz awaryjnie, wyłączając maszyny, możesz zbliżyć się do tego limitu.

Byłoby rozsądne, aby podwoić limit awarii, aby to zrobić w Menedżerze klastra trybu failover, przejdź do właściwości zawsze na grupy zasobów:

![Dodatek3][13]

Zmień maksymalną liczbę błędów na 6.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>Krok 3: Dodawanie zasobu \<adresu IP dla grupy klastrów opcjonalne>

Jeśli masz tylko jeden adres IP dla grupy klastrów i jest on wyrównany do podsieci w chmurze, uważaj, jeśli przypadkowo przejedziesz do trybu offline wszystkie węzły klastra w chmurze w tej sieci, zasób IP klastra i nazwa sieci klastra nie będą mogły przejść do trybu online. W tej sytuacji zapobiega aktualizacji do innych zasobów klastra.

#### <a name="step-4-dns-configuration"></a>Krok 4: Konfiguracja DNS

Implementowanie płynnego przejścia zależy od sposobu wykorzystania i aktualizacji systemu DNS.
Po zainstalowaniu funkcji Zawsze włączone tworzy grupę zasobów klastra systemu Windows, po otwarciu Menedżera klastrów trybu failover zobaczysz, że ma on co najmniej trzy zasoby, do których odwołuje się dokument:

* Virtual Network Name (VNN) — nazwa DNS, z którą klienci łączą się, gdy chcą połączyć się z serwerami SQL za pośrednictwem funkcji Zawsze włączone.
* Zasób adresu IP — adres IP skojarzony z siecią VNN, można mieć więcej niż jeden, a w konfiguracji wielolokacjowej masz adres IP na lokację/podsieć.

Podczas nawiązywania połączenia z programem SQL Server sterownik klienta programu SQL Server pobiera rekordy DNS skojarzone z odbiornikiem i próbuje połączyć się z każdym skojarzonym adresem IP zawsze włączonym. Następnie omawiamy pewne czynniki, które mogą mieć na to wpływ.

Liczba równoczesnych rekordów DNS skojarzonych z nazwą odbiornika zależy nie tylko od liczby skojarzonych adresów IP, ale od ustawienia "RegisterAllIpProviders" w klastrze trybu failover dla zasobu Always ON VNN.

Podczas wdrażania zawsze włączone na platformie Azure istnieją różne kroki, aby utworzyć odbiornika i adresy IP, trzeba ręcznie skonfigurować "RegisterAllIpProviders" do 1, to różni się od lokalnego zawsze włączone wdrożenia, gdzie jest już ustawiona na 1.

Jeśli "RegisterAllIpProviders" jest 0, a następnie widzisz tylko jeden rekord DNS w systemie DNS skojarzone z odbiornikiem:

![Dodatek 4][14]

Jeśli "RegisterAllIpProviders" jest 1:

![Dodatek5][15]

Poniższy kod zrzuca ustawienia VNN i ustawia go dla Ciebie. Aby zmiana została wejdą w życie, musisz wyłączyć VNN i przywrócić ją do trybu online. Spowoduje to przełączenie odbiornika w tryb offline, powodując zakłócenia łączności klienta.

```powershell
##Always On Listener Name
$ListenerName = "Mylistener"
##Get AlwaysOn Network Name Settings
Get-ClusterResource $ListenerName| Get-ClusterParameter
##Set RegisterAllProvidersIP
Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1
```

W późniejszym kroku migracji należy zaktualizować odbiornik zawsze włączone o zaktualizowany adres IP, który odwołuje się do modułu równoważenia obciążenia, co wiąże się z usuwaniem i dodawaniem zasobów adresu IP. Po aktualizacji adresu IP należy upewnić się, że nowy adres IP został zaktualizowany w strefie DNS i że klienci aktualizują swoją lokalną pamięć podręczną DNS.

Jeśli klienci znajdują się w innym segmencie sieci i odwołują się do innego serwera DNS, należy wziąć pod uwagę, co dzieje się z transferem strefy DNS podczas migracji, ponieważ czas ponownego połączenia aplikacji jest ograniczony co najmniej przez czas transferu strefy dowolnego nowego adresu IP adresów dla odbiornika. Jeśli jesteś w tym miejscu w granicach czasu, należy omówić i przetestować wymuszanie przyrostowego transferu strefy z zespołami systemu Windows, a także umieścić rekord hosta DNS na niższym czasie wygaśnięcia (TTL), więc klienci są aktualizowani. Aby uzyskać więcej informacji, zobacz [Przyrostowe transfery stref](https://technet.microsoft.com/library/cc958973.aspx) i [Start-DnsServerZoneTransfer](https://docs.microsoft.com/powershell/module/dnsserver/start-dnsserverzonetransfer).

Domyślnie czas wygaśnięcia rekordu DNS skojarzony z odbiornikiem w trybie Zawsze włączony na platformie Azure wynosi 1200 sekund. Można to zmniejszyć, jeśli podczas migracji występują ograniczenia czasowe, aby upewnić się, że klienci aktualizują swój system DNS za pomocą zaktualizowanego adresu IP odbiornika. Można zobaczyć i zmodyfikować konfigurację przez wyrzucenie konfiguracji VNN:

```powershell
$AGName = "myProductionAG"
$ListenerName = "Mylistener"
#Look at HostRecordTTL
Get-ClusterResource $ListenerName| Get-ClusterParameter

#Set HostRecordTTL Examples
Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120
```

> [!NOTE]
> Im niższy "HostRecordTTL", tym większa ilość ruchu DNS.

##### <a name="client-application-settings"></a>Ustawienia aplikacji klienckiej

Jeśli aplikacja kliencka SQL obsługuje .NET 4.5 SQLClient, możesz użyć słowa kluczowego "MULTISUBNETFAILOVER=TRUE". To słowo kluczowe powinno być stosowane, ponieważ umożliwia szybsze połączenie z grupą dostępności zawsze w programie SQL podczas pracy awaryjnej. Wylicza za pośrednictwem wszystkich adresów IP skojarzonych z odbiornikiem Zawsze włączone równolegle i wykonuje bardziej agresywne połączenie TCP ponawianie próby podczas pracy awaryjnej.

Aby uzyskać więcej informacji na temat poprzednich ustawień, zobacz [Słowo kluczowe MultiSubnetFailover i Skojarzone funkcje](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover). Zobacz też [Pomoc techniczna SqlClient dla wysokiej dostępności, odzyskiwania po awarii](https://msdn.microsoft.com/library/hh205662\(v=vs.110\).aspx).

#### <a name="step-5-cluster-quorum-settings"></a>Krok 5: Ustawienia kworum klastra

Ponieważ zamierzasz wyjmować co najmniej jeden program SQL Server w dół naraz, należy zmodyfikować ustawienie kworum klastra, jeśli używasz monitora udziału plików (FSW) z dwoma węzłami, należy ustawić kworum, aby zezwolić na większość węzłów i korzystać z dynamicznego głosowania , co pozwala na pozostanie jednego węzła na stojąco.

```powershell
Set-ClusterQuorum -NodeMajority  
```

Aby uzyskać więcej informacji na temat zarządzania kworum klastra i konfigurowania go, zobacz [Konfigurowanie kworum i zarządzanie nim w klastrze trybu failover systemu Windows Server 2012](https://technet.microsoft.com/library/jj612870.aspx).

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>Krok 6: Wyodrębnianie istniejących punktów końcowych i list ACL

```powershell
#GET Endpoint info
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
#GET ACL Rules for Each EP, this example is for the Always On Endpoint
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  
```

Zapisz ten tekst w pliku.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>Krok 7: Zmiana partnerów trybu pracy awaryjnej i trybów replikacji

Jeśli masz więcej niż dwa serwery SQL, należy zmienić tryb failover innego pomocniczego w innym kontrolerze domeny lub lokalnie na "Synchroniczne" i uczynić go automatycznym partnerem trybu failover (AFP), jest to tak, aby zachować ha podczas wprowadzania zmian. Można to zrobić za pośrednictwem TSQL modyfikacji chociaż SSMS:

![Dodatek6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>Krok 8: Usuwanie dodatkowej maszyny Wirtualnej z usługi w chmurze

Należy zaplanować migrację węzła pomocniczego chmury pierwszy. Jeśli ten węzeł jest obecnie podstawowy, należy zainicjować ręczne tryb failover.

```powershell
$vmNameToMigrate="dansqlams2"

#Check machine status
Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#Shutdown secondary VM
Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM


#Extract disk configuration

##Building Existing Data Disk Configuration
$file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
$datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
Add-Content $file "lun, vhdname, hostcaching, disklabel, diskName"
foreach ($disk in $datadisks)
{
    $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
    $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
}

#Get OS Disk
$osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
$osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
$osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
$addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
$addosdisk | add-content -path $file

#Import disk config
$diskobjects  = Import-CSV $file

#Check disk config, make sure below returns the disks associated with the VM
$diskobjects

#Identify OS Disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osdiskforbuild = $osdiskimport.diskName

#Check machibe is off
Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

#Drop machine and rebuild to new cls
Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate
```

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>Krok 9: Zmiana ustawień buforowania dysku w pliku CSV i zapisywanie

W przypadku woluminów danych należy je ustawić tylko na odczyt.

W przypadku woluminów TLOG należy je ustawić na NONE.

![Dodatek7][17]

#### <a name="step-10-copy-vhds"></a>Krok 10: Kopiowanie VHDS

```powershell
#Ensure you have created the container for these:
$containerName = 'vhds'

#Create container
New-AzureStorageContainer -Name $containerName -Context $xioContext

####DISK COPYING####
#Get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
ForEach ($disk in $diskobjects)
{
   $lun = $disk.Lun
   $vhdname = $disk.vhdname
   $cacheoption = $disk.HostCaching
   $disklabel = $disk.DiskLabel
   $diskName = $disk.DiskName
   Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

   #Start async copy
   Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContext
}
```


Możesz sprawdzić stan kopii VHD na konto magazynu w wersji Premium:

```powershell
ForEach ($disk in $diskobjects)
{
   $lun = $disk.Lun
   $vhdname = $disk.vhdname
   $cacheoption = $disk.HostCaching
   $disklabel = $disk.DiskLabel
   $diskName = $disk.DiskName

   $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContext
   Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
}
```

![Dodatek 8][18]

Poczekaj, aż wszystkie te zostaną zarejestrowane jako sukces.

Aby uzyskać informacje dotyczące poszczególnych obiektów blob:

```powershell
Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext
```

#### <a name="step-11-register-os-disk"></a>Krok 11: Zarejestruj dysk systemu operacyjnego

```powershell
#Change storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountname
Select-AzureSubscription -SubscriptionName $mysubscription -Current

#Register OS disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osvhd = $osdiskimport.vhdname
$osdiskforbuild = $osdiskimport.diskName

#Registering OS disk, but as XIO disk
$xioDiskName = $osdiskforbuild + "xio"
Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"
```

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>Krok 12: Importowanie dodatku wtórnego do nowej usługi w chmurze

Poniższy kod używa również dodaną opcję tutaj można zaimportować maszynę i użyć zachowawki VIP.

```powershell
#Build VM Config
$ipaddr = "192.168.0.5"
#Remember to change to XIO
$newInstanceSize = "Standard_DS13"
$subnet = "SQL"

#Create new Availability Set
$availabilitySet = "cloudmigAVAMS"

#build machine config into object
$vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

#Reload disk config
$diskobjects  = Import-CSV $file
$datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

ForEach ( $attachdatadisk in $datadiskimport)
{
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###Attaching disks to a VM during a deploy to a new cloud service and new storage account is different from just attaching VHDs to just a redeploy in a new cloud service
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

}

#Create VM
$vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)
```

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>Krok 13: Tworzenie równoważenia obciążenia przy nowym svcie w chmurze, dodawanie punktów końcowych i list ACL z równoważenia obciążenia

```powershell
#Check for existing ILB
GET-AzureInternalLoadBalancer -ServiceName $destcloudsvc

$ilb="sqlIntIlbDest"
$subnet = "SQL"
$IP="192.168.0.25"
Add-AzureInternalLoadBalancer -ServiceName $destcloudsvc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP

#Endpoints
$epname="sqlIntEP"
$prot="tcp"
$locport=1433
$pubport=1433
Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM

#SET Azure ACLs or Network Security Groups & Windows FWs

#https://msdn.microsoft.com/library/azure/dn495192.aspx

####WAIT FOR FULL AlwaysOn RESYNCRONISATION!!!!!!!!!#####
```

#### <a name="step-14-update-always-on"></a>Krok 14: Aktualizacja zawsze wł.

```powershell
#Code to be executed on a Cluster Node
$ClusterNetworkNameAmsterdam = "Cluster Network 2" # the azure cluster subnet network name
$newCloudServiceIPAmsterdam = "192.168.0.25" # IP address of your cloud service

$AGName = "myProductionAG"
$ListenerName = "Mylistener"


Add-ClusterResource "IP Address $newCloudServiceIPAmsterdam" -ResourceType "IP Address" -Group $AGName -ErrorAction Stop |  Set-ClusterParameter -Multiple @{"Address"="$newCloudServiceIPAmsterdam";"ProbePort"="59999";SubnetMask="255.255.255.255";"Network"=$ClusterNetworkNameAmsterdam;"OverrideAddressMatch"=1;"EnableDhcp"=0} -ErrorAction Stop

#set dependency and NETBIOS, then remove old IP address

#set NETBIOS, then remove old IP address
Get-ClusterGroup $AGName | Get-ClusterResource -Name "IP Address $newCloudServiceIPAmsterdam" | Set-ClusterParameter -Name EnableNetBIOS -Value 0

#set dependency to Listener (OR Dependency) and delete previous IP Address resource that references:

#Make sure no static records in DNS
```

![Dodatek 9][19]

Teraz usuń stary adres IP usługi w chmurze.

![Dodatek 10][20]

#### <a name="step-15-dns-update-check"></a>Krok 15: Sprawdzanie aktualizacji DNS

Teraz należy sprawdzić serwery DNS w sieciach klienckich programu SQL Server i upewnić się, że klastrowanie dodało dodatkowy rekord hosta dla dodanego adresu IP. Jeśli te serwery DNS nie zostały zaktualizowane, należy rozważyć wymuszenie transferu strefy DNS i upewnić się, że klienci tam podsieci są w stanie rozpoznać zarówno zawsze na adresach IP, jest to, dzięki czemu nie trzeba czekać na automatyczną replikację DNS.

#### <a name="step-16-reconfigure-always-on"></a>Krok 16: Rekonfiguruj zawsze włączone

W tym momencie poczekaj na pomocniczy tego węzła, który został zmigrowany, aby w pełni ponownie zsynchronizować z węzłem lokalnym i przełączyć się do węzła replikacji synchroniiowej i uczynić go AFP.  

#### <a name="step-17-migrate-second-node"></a>Krok 17: Migrowanie drugiego węzła

```powershell
$vmNameToMigrate="dansqlams1"

Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#Get endpoint information
$endpoint = Get-AzureVM -ServiceName $sourceSvc  -Name $vmNameToMigrate | Get-AzureEndpoint

#Shutdown VM
Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM

#Get disk config

#Building Existing Data Disk Configuration
$file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
$datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
Add-Content $file "lun, vhdname, hostcaching, disklabel, diskName"
foreach ($disk in $datadisks)
{
    $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
    $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
}

#Get OS Disk
$osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
$osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
$osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
$addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
$addosdisk | add-content -path $file

#Import disk config
$diskobjects  = Import-CSV $file

#Check disk configuration
$diskobjects

#Identify OS Disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osdiskforbuild = $osdiskimport.diskName

#Check machine is off
Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

#Drop machine and rebuild to new cls
Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate
```

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>Krok 18: Zmiana ustawień buforowania dysku w pliku CSV i zapisywanie

W przypadku woluminów danych ustawienia pamięci podręcznej powinny być ustawione na READONLY.

W przypadku woluminów TLOG ustawienia pamięci podręcznej powinny być ustawione na BRAK.

![Dodatek 11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>Krok 19: Tworzenie nowego niezależnego konta magazynu dla węzła pomocniczego

```powershell
$newxiostorageaccountnamenode2 = "danspremsams2"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountnamenode2 -Location $location -Type "Premium_LRS"  

#Reset the storage account src if node 1 in a different storage account
$origstorageaccountname2nd = "danstdams2"

#Generate storage keys for later
$xiostoragenode2 = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountnamenode2

#Generate storage acc contexts
$xioContextnode2 = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountnamenode2 -StorageAccountKey $xiostoragenode2.Primary  

#Set up subscription and default storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
Select-AzureSubscription -SubscriptionName $mysubscription -Current
```

#### <a name="step-20-copy-vhds"></a>Krok 20: Kopiowanie VHDS

```powershell
#Ensure you have created the container for these:
$containerName = 'vhds'

#Create container
New-AzureStorageContainer -Name $containerName -Context $xioContextnode2  

####DISK COPYING####
##get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
ForEach ($disk in $diskobjects)
{
   $lun = $disk.Lun
   $vhdname = $disk.vhdname
   $cacheoption = $disk.HostCaching
   $disklabel = $disk.DiskLabel
   $diskName = $disk.DiskName
   Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

   #Start async copy
   Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname2nd.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContextnode2
}

#Check for copy progress

#check individual blob status
Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContext
```

Stan kopii VHD można sprawdzić dla wszystkich dysków VHD:

```powershell
ForEach ($disk in $diskobjects)
{
    $lun = $disk.Lun
    $vhdname = $disk.vhdname
    $cacheoption = $disk.HostCaching
    $disklabel = $disk.DiskLabel
    $diskName = $disk.DiskName

    $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContextnode2
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
}
```

![Dodatek 12][22]

Poczekaj, aż wszystkie te zostaną zarejestrowane jako sukces.

Aby uzyskać informacje dotyczące poszczególnych obiektów blob:

```powershell
#Check individual blob status
Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContextnode2
```

#### <a name="step-21-register-os-disk"></a>Krok 21: Zarejestruj dysk systemu operacyjnego

```powershell
#change storage account to the new XIO storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
Select-AzureSubscription -SubscriptionName $mysubscription -Current

#Register OS disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osvhd = $osdiskimport.vhdname
$osdiskforbuild = $osdiskimport.diskName

#Registering OS disk, but as XIO disk
$xioDiskName = $osdiskforbuild + "xio"
Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

#Build VM Config
$ipaddr = "192.168.0.4"
$newInstanceSize = "Standard_DS13"

#Join to existing Availability Set

#Build machine config into object
$vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

#Reload disk config
$diskobjects  = Import-CSV $file
$datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

ForEach ( $attachdatadisk in $datadiskimport)
{
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###This is different to just a straight cloud service change
    #note if you do not have a disk label the command below will fail, populate as required.
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

}

#Create VM
$vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet -Verbose
```

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>Krok 22: Dodawanie punktów końcowych i list ACL z równoważenia obciążenia

```powershell
#Endpoints
$epname="sqlIntEP"
$prot="tcp"
$locport=1433
$pubport=1433
Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM


#STOP!!! CHECK in the Azure portal or Machine Endpoints through PowerShell that these Endpoints are created!

#SET ACLs or Azure Network Security Groups & Windows FWs

#https://msdn.microsoft.com/library/azure/dn495192.aspx
```

#### <a name="step-23-test-failover"></a>Krok 23: Test pracy awaryjnej

Poczekaj na zmiętowane węzła do synchronizacji z lokalnym węzłem zawsze włączone. Umieść go w trybie replikacji synchroniczowej i poczekaj, aż zostanie zsynchronizowany. Następnie pracy awaryjnej z lokalnego do pierwszego węzła migrowane, który jest AFP. Gdy to zadziała, zmień ostatni migrowany węzeł do AFP.

Należy przetestować pracy awaryjnej między wszystkimi węzłami i uruchomić mimo testów chaosu, aby upewnić się, że praca w przypadku pracy awaryjnej zgodnie z oczekiwaniami i w czasie manor.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>Krok 24: Odłóż ustawienia kworum klastra / DNS TTL / Failover Pntrs / Ustawienia synchronizacji

##### <a name="adding-ip-address-resource-on-same-subnet"></a>Dodawanie zasobu adresu IP w tej samej podsieci

Jeśli masz tylko dwa serwery SQL i chcesz przeprowadzić migrację do nowej usługi w chmurze, ale chcesz zachować je w tej samej podsieci, możesz uniknąć przesuń odbiornika w tryb offline, aby usunąć oryginalny adres IP zawsze włączony i dodać nowy adres IP. W przypadku migracji maszyn wirtualnych do innej podsieci nie trzeba tego robić, ponieważ istnieje dodatkowa sieć klastra, która odwołuje się do tej podsieci.

Po utworzeniu zmigrowanego pomocniczego i dodaniu do nowego zasobu adresu IP dla nowej usługi w chmurze przed przełączeniem w życie istniejącej usługi podstawowej należy wykonać następujące kroki w Menedżerze trybu failover klastra:

Aby dodać adres IP, zobacz dodatek, krok 14.

1. W przypadku bieżącego zasobu adresu IP zmień możliwego właściciela na "Istniejący podstawowy program SQL Server", w przykładzie "dansqlams4":

    ![Dodatek 13][23]
2. W przypadku nowego zasobu adresu IP zmień możliwego właściciela na "Migrowany pomocniczy program SQL Server", w przykładzie "dansqlams5":

    ![Dodatek 14][24]
3. Po ustawieniu można przejść w ramach trybu failover, a podczas migracji ostatniego węzła możliwe właściciele powinni być edytowani, aby węzeł był dodawany jako możliwy właściciel:

    ![Dodatek 15][25]

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Usługa Azure w magazynie w wersji premium](../disks-types.md)
* [Maszyny wirtualne](https://azure.microsoft.com/services/virtual-machines/)
* [Program SQL Server w maszynach wirtualnych platformy Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

<!-- IMAGES -->
[1]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/1_VNET_Portal.png
[2]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/2_Diskname_Lun.png
[3]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/3_Virtual_Disk_Properties.png
[4]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/4_Virtual_Disk_Properties_Details.png
[5]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/5_Get_Storage_Pool.png
[6]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/6_Deployments_Always_On.png
[7]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/7_Add_More_Secondaries.png
[8]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/8_Use_Existing_Secondary_Single_Site.png
[9]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site.png
[10]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site_b.png
[11]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_01.png
[12]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_02.png
[13]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_03.png
[14]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_04.png
[15]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_05.png
[16]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_06.png
[17]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_07.png
[18]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_08.png
[19]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_09.png
[20]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_10.png
[21]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_11.png
[22]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_12.png
[23]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_13.png
[24]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_14.png
[25]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_15.png
