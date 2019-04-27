---
title: Korzystanie z usługi Azure Premium Storage z programem SQL Server | Dokumentacja firmy Microsoft
description: W tym artykule używa zasobów utworzonych za pomocą klasycznego modelu wdrażania i zapewnia wskazówki na temat korzystania z usługi Azure Premium Storage przy użyciu programu SQL Server uruchomionego na maszynach wirtualnych platformy Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 7ccf99d7-7cce-4e3d-bbab-21b751ab0e88
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/01/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 3b3bb206286629a68c14b6444f3f88ffa0af50dd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60583272"
---
# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Korzystanie z usługi Azure Premium Storage z programem SQL Server na maszynach wirtualnych

## <a name="overview"></a>Omówienie

[Usługa Azure premium SSD](../disks-types.md) to następna generacja magazynu, który zapewnia małe opóźnienia i wysoką przepływność operacji We/Wy. Działa najlepiej w kluczowych obciążeń intensywnie korzystających z operacji We/Wy, takich jak SQL Server w usłudze IaaS [maszyn wirtualnych](https://azure.microsoft.com/services/virtual-machines/).

> [!IMPORTANT]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [Usługi Resource Manager i Model Klasyczny](../../../azure-resource-manager/resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.

Ten artykuł zawiera wskazówki dotyczące migrowania maszyny wirtualnej z systemem SQL Server do korzystania z usługi Premium Storage i planowania. W tym infrastruktury platformy Azure (sieci, magazynu) i kroki Windows maszyny Wirtualnej gościa. W przykładzie w [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) przedstawia pełne kompleksowe migracji typu end to end, jak przenieść większych maszyn wirtualnych, aby korzystać z zalet ulepszonych lokalny magazyn SSD przy użyciu programu PowerShell.

Jest ważne zrozumieć ten proces end-to-end, stosowaniu usługi Azure Premium Storage z programem SQL Server na maszynach wirtualnych IAAS. Obejmuje to:

* Identyfikacja wymagań wstępnych, aby używać usługi Premium Storage.
* Przykłady wdrażania programu SQL Server w usłudze IaaS do usługi Premium Storage o nowych wdrożeniach.
* Przykłady Migrowanie istniejących wdrożeń zarówno serwerów autonomicznych, jak i wdrożeń przy użyciu programu SQL zawsze włączone grupy dostępności.
* Migracja możliwych podejść.
* Przykład pełnej end-to-end przedstawiający kroki platformy Azure, Windows i programu SQL Server do migracji istniejącej implementacji zawsze włączone.

Aby uzyskać więcej informacji o tła w programie SQL Server na maszynach wirtualnych platformy Azure, zobacz [programu SQL Server w usłudze Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

**Autor:** Daniel Sol **Technical Reviewers:** Śledź Vargas Luis Carlos Sanjay Mishra, Pravin Mital, Juergen Thomas, Gonzalo Ruiz.

## <a name="prerequisites-for-premium-storage"></a>Wymagania wstępne dotyczące magazynu w warstwie Premium

Istnieje kilka wymagań wstępnych dotyczące korzystania z usługi Premium Storage.

### <a name="machine-size"></a>Rozmiar maszyny

Dotyczące korzystania z usługi Premium Storage, musisz użyć serii DS maszyn wirtualnych (VM). Jeśli w usłudze w chmurze przed nie używano maszyn z serii DS, należy usunąć istniejącej maszyny Wirtualnej, zachować dołączonych dysków, a następnie utwórz nową usługę w chmurze przed ponownym utworzeniem maszyny Wirtualnej, jak rozmiar roli DS *. Aby uzyskać więcej informacji na temat rozmiarów maszyn wirtualnych, zobacz [maszyny wirtualnej i rozmiary usług w chmurze dla platformy Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="cloud-services"></a>Usługi w chmurze

Maszyny wirtualne DS * można używać dzięki usłudze Premium Storage tylko, gdy są one tworzone w nowej usługi w chmurze. Jeśli używasz programu SQL Server Always On na platformie Azure, zawsze na odbiornik odnosi się do adresu Azure wewnętrzny lub zewnętrzny adres IP modułu równoważenia obciążenia, który jest skojarzony z usługą w chmurze. Ten artykuł koncentruje się na temat migrowania przy zachowaniu dostępności w tym scenariuszu.

> [!NOTE]
> Seria DS * musi być pierwszą maszynę Wirtualną, który został wdrożony dla nowej usługi w chmurze.
>
>

### <a name="regional-vnets"></a>Regional VNETS

W przypadku maszyn wirtualnych DS * należy skonfigurować Virtual Network (VNET) hostingu maszyn wirtualnych, aby być regionalne. To "rozszerza się" sieci Wirtualnej jest umożliwienie większe maszyny wirtualne można aprowizować inne klastry do zezwalania na komunikację między nimi. Poniższy zrzut ekranu wyróżnione lokalizacje pokazuje regionalne sieci wirtualne, pierwszego wyniku pokazuje "narrow" sieci Wirtualnej.

![RegionalVNET][1]

Może zgłosić bilet pomocy technicznej firmy Microsoft, aby przeprowadzić migrację do regionalnej sieci Wirtualnej. Firma Microsoft udziela następnie zmiany. Aby przeprowadzić migrację do regionalnej sieci wirtualne, zmień wartość właściwości AffinityGroup w konfiguracji sieci. Najpierw wyeksportować konfigurację sieci w programie PowerShell, a następnie zastąp **AffinityGroup** właściwość **VirtualNetworkSite** element z **lokalizacji** właściwości. Określ `Location = XXXX` gdzie `XXXX` to region platformy Azure. Następnie można zaimportować nową konfigurację.

Na przykład biorąc pod uwagę następujące konfiguracja sieci Wirtualnej:

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

Aby przenieść to regionalne sieci Wirtualne w regionie Europa Zachodnia, należy zmienić konfigurację do następujących:
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

Musisz utworzyć nowe konto magazynu, który jest skonfigurowany dla usługi Premium Storage. Należy pamiętać, że korzystanie z usługi Premium Storage jest ustawiona na koncie magazynu, nie dla poszczególnych wirtualnych dysków twardych, jednak w przypadku korzystania z maszyny Wirtualnej serii DS * możesz dołączyć VHD z konta magazynu Premium i standardowa. To może rozważyć, jeśli nie chcesz umieścić wirtualny dysk twardy oparty na koncie magazynu w warstwie Premium.

Następujące **New AzureStorageAccountPowerShell** polecenia "Premium_LRS" **typu** tworzy konto magazynu Premium Storage:

```powershell
$newstorageaccountname = "danpremstor"
New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   
```

### <a name="vhds-cache-settings"></a>Ustawienia pamięci podręcznej wirtualne dyski twarde

Główna różnica między tworzenia dysków, które są dostępne w ramach konta usługi Premium Storage jest ustawienie pamięci podręcznej dysku. Dla woluminu danych programu SQL Server, dyski go jest zalecane, możesz użyć "**buforowania odczytu**". Aby uzyskać woluminy dziennika transakcji, ustawienie pamięci podręcznej dysku powinna być równa "**Brak**". To różni się od zaleceń dotyczących kont magazynu w warstwie standardowa.

Po dołączeniu wirtualne dyski twarde nie można zmienić ustawienie pamięci podręcznej. Będzie konieczne, odłączając i przyłączając ponownie wirtualny dysk twardy z ustawieniem zaktualizowano pamięci podręcznej.

### <a name="windows-storage-spaces"></a>Windows funkcji miejsca do magazynowania

Możesz użyć [magazynowania w systemie Windows](https://technet.microsoft.com/library/hh831739.aspx) tak jak w przypadku poprzedniego magazynu w warstwie standardowa, dzięki temu można migrować maszynę Wirtualną, która jest już przy użyciu funkcji miejsca do magazynowania. W przykładzie w [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) (krok 9 lub do przodu) pokazuje kod programu Powershell, aby wyodrębnić i zaimportować Maszynę wirtualną z wieloma dołączonymi dyskami VHD.

Pule magazynu zostały użyte przy użyciu konta usługi Standard Azure storage można zwiększyć wydajność i zmniejszyć opóźnienie. Wartość może się okazać podczas testowania pule magazynu dzięki usłudze Premium Storage o nowych wdrożeniach, ale dodają dodatkową złożoność z instalacji magazynu.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>Jak znaleźć mapie wirtualnych dysków platformy Azure, w której do pul magazynów

Jak istnieją różne pamięci podręcznej ustawienie zalecenia dotyczące dołączonymi dyskami VHD, można zdecydować skopiować wirtualne dyski twarde na koncie magazynu w warstwie Premium. Jednak w przypadku podłączysz je do nowej maszyny Wirtualnej serii DS może być konieczne zmiany ustawień pamięci podręcznej. Jest łatwiejsze do zastosowania usługi Premium Storage, zalecane ustawienia pamięci podręcznej, gdy masz osobne wirtualne dyski twarde dla plików danych SQL i plików dziennika (zamiast pojedynczego wirtualnego dysku twardego, który zawiera zarówno).

> [!NOTE]
> W przypadku plików danych i dziennika programu SQL Server na tym samym woluminie pamięci podręcznej wybranej opcji zależy od wzorców dostępu we/wy dla obciążeń bazy danych. Tylko testowanie wykaże, która opcja buforowania jest najlepsze dla tego scenariusza.
>
>

Jednak jeśli używasz miejsca do magazynowania systemu Windows, które składają się z wielu dysków VHD, należy przyjrzeć się oryginalnego skryptów do identyfikowania, w których dołączone dyski VHD są w określonej puli, więc można następnie ustawić ustawienia pamięci podręcznej odpowiednio dla każdego dysku.

Jeśli nie masz oryginalnego skryptu do przedstawienia, które wirtualne dyski twarde mapy do puli magazynów, można użyć następujące kroki do określenia mapowania dysku/magazynu w puli.

Dla każdego dysku wykonaj następujące czynności:

1. Pobierz listę dysków dołączonych do maszyny Wirtualnej przy użyciu **Get-AzureVM** polecenia:

```powershell
Get-AzureVM -ServiceName <servicename> -Name <vmname> | Get-AzureDataDisk
```

1. Zanotuj DiskName i jednostki LUN.

    ![DisknameAndLUN][2]
1. Pulpit zdalny do maszyny Wirtualnej. Następnie przejdź do **Zarządzanie komputerem** | **Menedżera urządzeń** | **dysków**. Sprawdź właściwości każdej "Dysków wirtualnych firmy Microsoft"

    ![VirtualDiskProperties][3]
1. W tym miejscu numeru LUN jest odwołanie do numeru LUN, gdy dołączanie wirtualnego dysku twardego do maszyny Wirtualnej.
1. Dla pozycji "Microsoft Virtual Disk" **szczegóły** kartę, a następnie w obszarze **właściwość** listy, przejdź do **klucz sterownika**. W **wartość**, Uwaga **przesunięcie**, czyli 0002 na następującym zrzucie ekranu. 0002 oznacza Dysk_fizyczny_2, który odwołuje się do puli magazynów.

    ![VirtualDiskPropertyDetails][4]
1. Dla każdej puli magazynów zrzutu się skojarzone dyski:

```powershell
Get-StoragePool -FriendlyName AMS1pooldata | Get-PhysicalDisk
```

![GetStoragePool][5]

Teraz możesz użyć tych informacji, aby skojarzyć dołączonych wirtualnych dysków twardych do dysków fizycznych w pule magazynu.

Po zamapowaniu wirtualnych dysków twardych do dysków fizycznych w pule magazynu można następnie odłącz i skopiować je za pośrednictwem konta usługi Premium Storage, a następnie dołącz je z ustawieniem odpowiedniej pamięci podręcznej. Zobacz przykład w [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage), kroki od 8 do 12. Poniższe kroki pokazują jak wyodrębnić konfiguracji dysku VHD dołączone do maszyny Wirtualnej do pliku CSV, skopiuj wirtualne dyski twarde, zmieniać ustawienia pamięci podręcznej konfiguracji dysków i na koniec ponowne wdrażanie maszyny Wirtualnej jako maszyny Wirtualnej serii DS przy użyciu wszystkich dołączonych dysków.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>Maszyny Wirtualnej przepustowości magazynu i przepływności dysku VHD

Wydajność magazynu zależy od wybranego rozmiaru maszyny Wirtualnej DS * oraz rozmiar wirtualnego dysku twardego. Maszyny wirtualne mają różne limity liczby wirtualnych dysków twardych, które mogą być dołączone i maksymalnej przepustowości, które obsługują (MB/s). W przypadku liczb określoną przepustowością, zobacz [maszyny wirtualnej i rozmiary usług w chmurze dla platformy Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Zwiększona operacje We/Wy są wykonywane za pomocą większego rozmiaru dysku. Należy rozważyć to Zastanawiając się ścieżki migracji. Aby uzyskać szczegółowe informacje [znajdują się w tabeli dla operacji We/Wy i typy dysków](../disks-types.md#premium-ssd).

Na koniec należy rozważyć, czy maszyny wirtualne mają inny dysk maksymalnej przepustowości, obsługujące dla wszystkich dysków dołączonych. Pod dużym obciążeniem można saturate dysku maksymalną przepustowość dostępna dla tego rozmiaru roli maszyny Wirtualnej. Na przykład Standard_DS14 obsługuje maksymalnie 512 MB/s w związku z trzy dyski P30, można saturate przepustowości dysku maszyny wirtualnej. Ale w tym przykładzie limit przepływności może zostać przekroczona w zależności od kombinacji odczytu i zapisu z systemem IOs.

## <a name="new-deployments"></a>Nowych wdrożeń

W dwóch następnych sekcji pokazują, jak wdrożyć maszyny wirtualne SQL Server do usługi Premium Storage. Jak wspomniano wcześniej, nie zawsze należy umieścić na dysku systemu operacyjnego na usługę Premium storage. Można to zrobić, jeśli zamierza umieścić wszelkie obciążeń z intensywnym wykorzystaniem we/wy do wirtualnego dysku twardego systemu operacyjnego.

Pierwszy przykład pokazuje, korzystanie z istniejących obrazów w galerii Azure. Drugi przykład pokazuje, jak używać niestandardowego obrazu maszyny Wirtualnej, które masz w istniejące konto magazynu w warstwie standardowa.

> [!NOTE]
> W poniższych przykładach założono, że utworzono już regionalnej sieci Wirtualnej.

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>Tworzenie nowej maszyny Wirtualnej przy użyciu usługi Premium Storage przy użyciu obrazu z galerii

W poniższym przykładzie pokazano, jak umieścić wirtualny dysk twardy oparty na magazyn w warstwie premium i Dołącz wirtualne dyski twarde Premium Storage. Jednak można także umieścić dysku systemu operacyjnego na koncie magazynu w warstwie standardowa, a następnie dołącz wirtualne dyski twarde, które znajdują się na koncie magazynu w warstwie Premium. Przedstawiono oba scenariusze.

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Set up subscription
Set-AzureSubscription -SubscriptionName $mysubscription
Select-AzureSubscription -SubscriptionName $mysubscription -Current  
```

#### <a name="step-1-create-a-premium-storage-account"></a>Krok 1: Tworzenie konta usługi Premium Storage

```powershell
#Create Premium Storage account, note Type
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  
```

#### <a name="step-2-create-a-new-cloud-service"></a>Krok 2: Utwórz nową usługę w chmurze

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>Krok 3: Zarezerwuj Cloud Service adres VIP (opcjonalnie)

```powershell
#check exisitng reserved VIP
Get-AzureReservedIP

$reservedVIPName = “sqlcloudVIP”
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

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>Krok 5. Wprowadzenie do wirtualnego dysku twardego systemu operacyjnego na standardowy lub Premium Storage

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

### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>Tworzenie nowej maszyny Wirtualnej usługi Premium Storage za pomocą obrazu niestandardowego

Ten scenariusz pokazuje, w którym masz istniejących dostosowanych obrazów, które znajdują się na koncie magazynu w warstwie standardowa. Jak wspomniano wcześniej, jeśli chcesz umieścić wirtualny dysk twardy Zawarty w magazynie Premium Storage należy skopiować obraz, który istnieje w ramach konta magazynu w warstwie standardowa i przenieść je do usługi Premium Storage, zanim będzie można jej używać. W przypadku obrazu w środowisku lokalnym, możesz również użyć tej metody do skopiowania, bezpośrednio na koncie usługi Premium Storage.

#### <a name="step-1-create-storage-account"></a>Krok 1: Utwórz konto magazynu

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Create Premium Storage account
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

#Standard Storage account
$origstorageaccountname = "danstdams"
```

#### <a name="step-2-create-cloud-service"></a>Krok 2 Utwórz usługę w chmurze

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-use-existing-image"></a>Krok 3: Użyj istniejącego obrazu

Można użyć istniejącego obrazu. Można też [zająć obraz istniejącej maszyny](../classic/capture-image-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Należy pamiętać, komputer, na którym możesz obraz musi być DS * maszyny. Po utworzeniu obrazu, poniższe kroki pokazują, jak skopiować go do konta magazynu w warstwie Premium z **Start AzureStorageBlobCopy** polecenia cmdlet programu PowerShell.

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

#### <a name="step-4-copy-blob-between-storage-accounts"></a>Krok 4: Kopiowanie obiektu Blob między kontami magazynu

```powershell
#Get Image VHD
$myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
$containerName = 'vhds'

#Copy Blob between accounts
$blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
-DestContainer vhds -Destblob "prem-$myImageVHD" `
-Context $origContext -DestContext $destContext  
```

#### <a name="step-5-regularly-check-copy-status"></a>Krok 5. Regularnie sprawdzać stan kopiowania:

```powershell
$blob | Get-AzureStorageBlobCopyState
```

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>Krok 6: Dodawanie obrazu dysku do dysku platformy Azure z repozytorium w ramach subskrypcji

```powershell
$imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
$newimageName = "prem"+"dansoldonorsql2k14"

Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation
```

> [!NOTE]
> Może się okazać, że mimo że zgłasza stan jako Powodzenie, będzie można nadal się pojawiać błąd dzierżawy dysku. W takim przypadku poczekaj około 10 minut.

#### <a name="step-7--build-the-vm"></a>Krok 7:  Tworzenie maszyny Wirtualnej

W tym miejscu tworzysz maszynę Wirtualną z obrazu i dołączanie dwa wirtualne dyski twarde Premium Storage:

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
$pass = "theM)stC0mplexP@ssw0rd!”


#Create VM Config
$vmConfigsl2 = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $newimageName  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

$vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-Datadisk-1.vhd"
$vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "LogDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-logdisk-1.vhd"



$vmConfigsl2 | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet
```

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Istniejące wdrożenia, które nie korzystają z zawsze włączonymi grupami dostępności

> [!NOTE]
> Dla istniejących wdrożeń, zobacz najpierw [wymagania wstępne](#prerequisites-for-premium-storage) dalszej części tego artykułu.

Istnieją różne zagadnienia dotyczące wdrożenia programu SQL Server, które nie korzystają z zawsze włączonych grup dostępności oraz tych, które wykonują. Jeśli nie używasz zawsze włączone i masz istniejących autonomiczne wystąpienie programu SQL Server, można uaktualnić do usługi Premium Storage przy użyciu nowego konta magazynu i usługi chmury. należy wziąć pod uwagę następujące opcje:

* **Utwórz nową maszynę Wirtualną programu SQL Server**. Można utworzyć nowej maszyny Wirtualnej serwera SQL, który korzysta z konta magazynu w warstwie Premium, zgodnie z opisem w nowych wdrożeniach. Następnie tworzy kopię zapasową i przywrócić bazy danych konfiguracji i użytkowników programu SQL Server. Aplikacja musi zostać zaktualizowany do odwołania nowy program SQL Server, jeśli element jest uzyskiwany wewnętrznie lub zewnętrznie. Należy skopiować wszystkie obiekty "z bazy danych", tak, jakby wykonywanych migracji programu SQL Server (SxS) obok siebie. Obejmuje to obiekty, takie jak nazwy logowania, certyfikaty i połączone serwery.
* **Migrowanie istniejącej maszyny Wirtualnej programu SQL Server**. Ta migracja wymaga przełączania w tryb offline maszyny Wirtualnej programu SQL Server, a następnie przesłanie ich do nowej usługi w chmurze, która obejmuje kopiowanie wszystkich jego dołączonymi dyskami VHD do konta usługi Premium Storage. Po przejściu maszyny Wirtualnej do trybu online, aplikacja odwołuje się nazwa hosta serwera jako przed. Należy pamiętać, że rozmiar istniejącego dysku ma wpływ na charakterystyki wydajności. Na przykład dysk 400 GB pobiera zaokrąglana w górę do P20. Jeśli wiesz, że nie wymagają tego wydajności dysku, a następnie można ponownie utworzyć maszynę Wirtualną jako Maszynę wirtualną serii DS i dołączanie dysków VHD z magazynu Premium specyfikacji rozmiaru i wydajności, które są wymagane. Następnie można odłączyć i ponownie dołączyć plików bazy danych SQL.

> [!NOTE]
> Podczas kopiowania dysków VHD, należy pamiętać o rozmiarze w zależności od rozmiaru oznacza, że typ dysku magazynu Premium dzielą się na, ta wartość określa specyfikacji wydajność dysku. Azure zostanie zaokrąglona do najbliższej rozmiaru dysku, więc jeśli masz dysk 400 GB, to jest zaokrąglana do P20. W zależności od wymagań we/wy istniejącego wirtualnego dysku twardego systemu operacyjnego nie może być konieczne to migrację do konta usługi Premium Storage.

Jeśli program SQL Server jest dostępny zewnętrznie, adres VIP usługi chmury zmiany. Masz również punkty końcowe aktualizacji list ACL oraz DNS ustawień.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Istniejące wdrożenia, które używają zawsze włączonych grup dostępności

> [!NOTE]
> Dla istniejących wdrożeń, zobacz najpierw [wymagania wstępne](#prerequisites-for-premium-storage) dalszej części tego artykułu.

Początkowo w tej sekcji przyjrzymy się zawsze na współdziałanie z sieci platformy Azure. Firma Microsoft następnie dzieląc migracji w dwóch scenariuszach: migracje, w którym może być tolerowana pewien Przestój i migracje, w którym musi osiągnąć minimalnym czasem przestojów.

Lokalne programu SQL Server zawsze włączonych grup dostępności, użyj odbiornika lokalnie, rejestruje wirtualnej nazwy DNS wraz z adresu IP, który jest udostępniany między co najmniej jeden serwer SQL. Gdy klienci łączą routingu IP odbiornika do podstawowego serwera SQL. Jest to serwer, który jest właścicielem zasobu zawsze na IP w danym momencie.

![DeploymentsUseAlways na][6]

W systemie Microsoft Azure może mieć tylko jeden adres IP przypisany do karty Sieciowej na maszynie Wirtualnej tak aby osiągnąć ten sam warstwę abstrakcji jako lokalne, Azure korzysta z adresu IP, która jest przypisana do wewnętrzne/zewnętrzne moduły równoważenia obciążenia (ILB/ELB). Zasób adresu IP, który jest udostępniany między serwerami jest ustawiony na tym samym adresem IP jako wewnętrznego modułu równoważenia obciążenia/ELB. To jest opublikowany w systemie DNS, a ruch sieciowy klienta jest przekazywany za pośrednictwem wewnętrznego modułu równoważenia obciążenia/ELB do repliki podstawowej programu SQL Server. Wewnętrznego modułu równoważenia obciążenia/ELB wie, której program SQL Server jest podstawowy, ponieważ używa sondy badanie zasób zawsze na adresu IP. W poprzednim przykładzie go sondy w każdym węźle, który ma punkt końcowy odwołuje się ELB/wewnętrznego modułu równoważenia obciążenia, odpowiada zależności jest podstawowym serwerem SQL.

> [!NOTE]
> Wewnętrznego modułu równoważenia obciążenia i ELB są zarówno przypisane do określonej platformy Azure usługa w chmurze, w związku z tym wszelkie migracji do chmury w usłudze Azure najprawdopodobniej oznacza, że zmiany IP modułu równoważenia obciążenia.
>
>

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>Migrowanie Always On wdrożenia, które można zezwolić na Przestój

Istnieją dwa strategii migracji wdrożenia zawsze włączonej, które pozwalają na pewien przestój:

1. **Dodaj więcej replik pomocniczych w istniejącym zawsze w klastrze**
2. **Migracja do nowego zawsze na klastra**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. Dodaj więcej replik pomocniczych w istniejącym zawsze w klastrze

Jedną ze strategii jest można dodać więcej pomocnicze bazy danych do zawsze włączonej grupy dostępności. Należy dodać je do nowej usługi w chmurze i zaktualizować odbiornik o nowy adres IP modułu równoważenia obciążenia.

##### <a name="points-of-downtime"></a>Punkty przestój:

* Sprawdzanie poprawności klastra.
* Testowanie trybu failover zawsze na nowe pomocnicze bazy danych.

Jeśli używasz pule magazynu systemu Windows na maszynie wirtualnej celu uzyskania wyższej przepustowości we/wy, a następnie je do trybu offline podczas pełnego sprawdzania poprawności klastra. Test weryfikacji jest wymagany podczas dodawania węzłów do klastra. Czas potrzebny do uruchomienia testu może się różnić, dlatego powinna to przetestować w środowisku testowym językiem, można pobrać przybliżony czas to czas.

Możesz zainicjować obsługę administracyjną czas, w którym ręcznej pracy awaryjnej i chaos testowania można wykonywać na nowo dodanych węzłach, aby zapewnić funkcji zawsze włączona wysoka dostępność, zgodnie z oczekiwaniami.

![DeploymentUseAlways On2][7]

> [!NOTE]
> Należy zatrzymać wszystkie wystąpienia programu SQL Server, w której pule magazynów są używane przed uruchomieniem walidacji.
>
> ##### <a name="high-level-steps"></a>Ogólne kroki
>

1. Tworzenie dwóch nowych serwerów SQL w nowej usługi w chmurze za pomocą dołączonej usługi Premium Storage.
2. Skopiuj pełnych kopii zapasowych i przywracania przy użyciu **NORECOVERY**.
3. Skopiuj "poza użytkownik bazy danych" obiekty zależne, takie jak logowania itp.
4. Utwórz nową nowy wewnętrzny obciążenia równoważenia (ILB) lub użyć zewnętrznego modułu równoważenia obciążenia (ELB), a następnie skonfiguruj punktów końcowych równoważenia obciążenia w obu węzłach nowego.

   > [!NOTE]
   > Sprawdź, czy wszystkie węzły mają prawidłowej konfiguracji punktu końcowego, aby kontynuować
   >
   >
5. Zatrzymaj użytkownika/aplikacji dostęp do programu SQL Server (Jeśli używane są pule magazynu).
6. Zatrzymaj usługi aparatu programu SQL Server na wszystkich węzłach (Jeśli używane są pule magazynu).
7. Dodaj nowych węzłów do klastra i pełna weryfikacja została uruchomiona.
8. Po pomyślnym zakończeniu sprawdzania poprawności należy uruchomić wszystkie usługi serwera SQL.
9. Dzienniki transakcji i przywracania kopii zapasowych baz danych użytkownika.
10. Dodawanie nowych węzłów do zawsze włączonej grupy dostępności i umieść replikacji do **synchroniczną**.
11. Dodaj zasób adresu IP z nowego Cloud Service wewnętrznego modułu równoważenia obciążenia/ELB za pomocą programu PowerShell, zawsze włączone na podstawie przykładu obejmujące wiele lokacji w [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage). W kontekście klastrowania Windows ustaw **możliwych właścicieli** z **adresu IP** zasobów do nowych węzłów stary. Zobacz sekcję "Dodawanie zasobu adresu IP w tej samej podsieci" [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
12. Tryb failover do jednej z nowych węzłów.
13. Wprowadź nowe węzły automatycznej pracy awaryjnej partnerów i testu pracy w trybie Failover.
14. Usuwanie oryginalnej węzłów z grupy dostępności.

##### <a name="advantages"></a>Zalety

* Nowe serwery SQL mogą być testowane (SQL Server i aplikacji), zanim zostaną one dodane do zawsze włączonych.
* Można zmienić rozmiar maszyny Wirtualnej i dostosować magazyn do wymagań dokładne. Jednakże może być korzystne zachować wszystkie ścieżki pliku SQL, takie same.
* Można kontrolować, po uruchomieniu transfer kopie zapasowe bazy danych na replikach pomocniczych. To różni się od korzystania z systemu Azure **Start AzureStorageBlobCopy** polecenia cmdlet, aby skopiować wirtualne dyski twarde, ponieważ jest to kopia asynchronicznego.

##### <a name="disadvantages"></a>Wady

* Gdy używane są pule magazynu systemu Windows, brak przestoju klastra podczas pełnego sprawdzania poprawności klastra do nowego dodatkowe węzły.
* W zależności od wersji programu SQL Server i liczba istniejących replik pomocniczych nie można dodać więcej replik pomocniczych, bez usuwania istniejącego pomocnicze bazy danych.
* Może być długi czas transferu danych SQL podczas konfigurowania pomocnicze bazy danych.
* Brak dodatkowych kosztów podczas migracji, gdy masz nowe maszyny uruchamianych równolegle.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. Migracja do nowego zawsze na klastra

Kolejną strategią jest utworzyć zupełnie nowy zawsze na klaster z zupełnie nowych węzłów w nowej usługi w chmurze i przekierowanie klientów z niego korzystać.

##### <a name="points-of-downtime"></a>Punkty przestojów

Brak przestojów w przypadku przeniesienia aplikacji i użytkowników do nowy odbiornik zawsze włączonych. Przestój jest zależny od:

* Czas do przywrócenia kopii zapasowej dziennika transakcji końcowego baz danych na nowe serwery.
* Czas poświęcony na aktualizowanie aplikacji klienckich do użycia nowy odbiornik zawsze włączonych.

##### <a name="advantages"></a>Zalety

* Możesz przetestować ze środowiskiem produkcyjnym rzeczywiste i programu SQL Server i zmiany kompilacji systemu operacyjnego.
* Istnieje możliwość dostosowywania magazynu i zmniejszenie rozmiaru maszyny Wirtualnej. Może to spowodować obniżenie kosztów.
* W trakcie tego procesu można zaktualizować wersja programu SQL Server lub wersji. Możesz również uaktualnić System operacyjny.
* Poprzednim zawsze w klastrze może działać jako obiekt docelowy solid wycofywania.

##### <a name="disadvantages"></a>Wady

* Należy zmienić nazwę DNS odbiornika, jeśli chcesz, aby obie zawsze włączonych klastrów z systemem jednocześnie. Spowoduje to dodanie administracji obciążenie podczas migracji, ponieważ ciągi aplikacji klienta muszą odzwierciedlać nową nazwę odbiornika.
* Musisz zaimplementować mechanizm synchronizacji między dwoma środowiskami, aby były jak najbliżej zminimalizować wymagania wykonana końcowa synchronizacja przed migracją.
* Jest dodatkowy koszt podczas migracji podczas, gdy masz nowe środowisko, które są uruchomione.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>Migrowanie zawsze na wdrożeń minimalizująca przestoje

Istnieją dwa strategii migracji wdrożenia zawsze włączonej minimalizująca przestoje:

1. **Korzystanie z istniejących pomocniczy: Pojedyncza witryna**
2. **Korzystanie z istniejących dodatkowej następującą: Połączenia obejmujące wiele lokacji**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. Korzystanie z istniejących pomocniczy: Pojedyncza witryna

Jedną ze strategii minimalizująca przestoje jest wykonać istniejącej chmurze dodatkowej i usunąć go z bieżącym usługi w chmurze. Następnie skopiuj wirtualne dyski twarde do nowego konta usługi Premium Storage i utworzyć maszynę Wirtualną w nowej usługi w chmurze. Następnie zaktualizuj odbiornik klastrowania i trybu failover.

##### <a name="points-of-downtime"></a>Punkty przestojów

* Brak przestojów po zaktualizowaniu ostatni węzeł z punktem końcowym ze zrównoważonym obciążeniem.
* Wartości swoje ponownego połączenia klienta mogą być opóźnione w zależności od konfiguracji klienta DNS.
* Jeśli użytkownik chce wykonać grupy zawsze na klastra w tryb offline, aby wymienić adresów IP jest dodatkowy Przestój. Można tego uniknąć, przy użyciu zależności OR i możliwych właścicieli zasobu dodano adres IP. Zobacz sekcję "Dodawanie zasobu adresu IP w tej samej podsieci" [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).

> [!NOTE]
> Dodano węzeł partake w jako zawsze na partnerski trybu Failover, należy musisz dodać punkt końcowy platformy Azure w odniesieniu do obciążenia zrównoważone zestawu. Po uruchomieniu **Add-AzureEndpoint** polecenia, aby to zrobić, otwórz bieżących połączeń, aby zachować, ale nowe połączenia z odbiornikiem nie będą mogli zostać ustanowione, dopóki nie został zaktualizowany moduł równoważenia obciążenia. Podczas testowania to występowało do ostatniego 120seconds 90, powinien zostać przetestowany.

##### <a name="advantages"></a>Zalety

* Bez dodatkowych kosztów poniesionych podczas migracji.
* Jeden do jednego migracji.
* Złożoności.
* Zezwala na zwiększone operacje We/Wy z jednostki SKU magazynu w warstwie Premium. Gdy dyski są odłączone od maszyny Wirtualnej i kopiowane do nowej usługi w chmurze innych firm 3 narzędzie może służyć do zwiększenia rozmiaru wirtualnego dysku twardego, który zapewnia wyższej przepustowości. Aby zwiększyć rozmiar wirtualnego dysku twardego, zobacz ten [dyskusjach prowadzonych na forum](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows).

##### <a name="disadvantages"></a>Wady

* Podczas migracji jest tymczasowej utraty wysokiej dostępności i odzyskiwania po awarii.
* Ponieważ migracja 1:1, musisz użyć minimalny rozmiar maszyny Wirtualnej, który obsługuje liczbę wirtualnych dysków twardych, więc może być możliwość downsize maszyn wirtualnych z systemem.
* W tym scenariuszu użyje Azure **Start AzureStorageBlobCopy** polecenia cmdlet jest asynchroniczna. Nie ma umowy SLA po zakończeniu kopiowania. Czas kopii różni się podczas zależy to od oczekiwania w kolejce, który również jest zależna od ilości danych do przesłania. Podczas kopiowania zwiększa się, gdy transfer przechodzi do innego centrum danych platformy Azure, która obsługuje usługę Premium Storage w innym regionie. Jeśli masz tylko 2 węzły, należy wziąć pod uwagę możliwe środki zaradcze w przypadku, gdy trwa dłużej niż podczas testowania, kopia. Może to obejmować następujące pomysły.
  * Dodaj tymczasowe 3 węzła programu SQL Server zapewnia wysoką dostępność, przed migracją uzgodnionych przestojów.
  * Uruchom migrację spoza platformy Azure zaplanowanej konserwacji.
  * Upewnij się, że Twoje kworum klastra zostały skonfigurowane prawidłowo.  

##### <a name="high-level-steps"></a>Ogólne kroki

W tym dokumencie nie przedstawiono tu kompletny przykład typu end to end, jednak [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) zawiera szczegółowe informacje, które można wykorzystać, które należy wykonać.

![MinimalDowntime][8]

* Zbierz informacje konfiguracji dysku i Usuń węzeł (nie należy usuwać dołączonymi dyskami VHD).
* Utwórz konto usługi Premium Storage i skopiuj wirtualne dyski twarde z konta magazynu w warstwie standardowa
* Utwórz nową usługę w chmurze i ponowne wdrożenie maszyny Wirtualnej SQL2 w tej usłudze w chmurze. Tworzenie maszyny Wirtualnej za pomocą skopiowanego oryginalny dysk VHD systemu operacyjnego i dołączanie skopiowane wirtualne dyski twarde.
* Konfigurowanie wewnętrznego modułu równoważenia obciążenia / ELB i dodać punkty końcowe.
* Zaktualizuj odbiornika przez:
  * Przełączania w tryb offline zawsze włączonej grupy i aktualizowanie nowego wewnętrznego modułu równoważenia obciążenia zawsze na odbiornik / adresu ELB IP.
  * Lub dodawanie zasobu adresu IP z nowego Cloud Service wewnętrznego modułu równoważenia obciążenia/ELB za pomocą programu PowerShell do klastrowania Windows. Następnie ustaw możliwych właścicieli zasobu adresu IP do migrowanych węzła SQL2 i ustawiać jako zależności OR w nazwie sieci. Zobacz sekcję "Dodawanie zasobu adresu IP w tej samej podsieci" [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
* Sprawdź konfigurację/Propagacja DNS do klientów.
* Migrowanie komputera sql1 do działania maszyny Wirtualnej, a następnie wykonaj kroki 2 – 4.
* Jeśli przy użyciu kroków 5ii, Dodaj komputera SQL1 jako możliwego właściciela dodano zasobu adresu IP
* Testy trybu failover.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. Korzystanie z istniejących dodatkowej następującą: Obejmujące wiele lokacji

Jeśli węzły w więcej niż jednym centrum danych platformy Azure (DC) lub jeśli w środowisku hybrydowym, następnie służy konfiguracji zawsze włączonej w tym środowisku w celu zminimalizowania przestojów.

Podejście to, aby zmienić synchroniczne synchronizacji Always On w środowisku lokalnym lub kontrolera pomocniczego platformy Azure, a następnie przejściu w tryb failover przez program SQL Server. Następnie skopiuj wirtualne dyski twarde na koncie magazynu w warstwie Premium i Wdróż ponownie maszynę do nowej usługi w chmurze. Zaktualizuj odbiornik, a następnie powrót po awarii.

##### <a name="points-of-downtime"></a>Punkty przestojów

Przestój składa się z czasu na pracę awaryjną do alternatywnego kontrolera domeny i z powrotem. Zależy on również konfigurację klienta DNS i ponowne połączenie Twojego klienta, które mogą być opóźnione.
Rozważmy następujący przykład hybrydowego Always On konfiguracji:

![MultiSite1][9]

##### <a name="advantages"></a>Zalety

* Korzystanie z istniejącej infrastruktury.
* Masz możliwość przed uaktualnieniem usługi Azure storage na kontrolerze domeny odzyskiwania po awarii Azure najpierw.
* Można tak skonfigurować magazyn usługi odzyskiwania po awarii Azure DC.
* Podczas migracji, z wyjątkiem testowych trybów Failover istnieje co najmniej dwóch przejścia w tryb failover.
* Nie musisz przenieść dane programu SQL Server przy użyciu kopii zapasowej i przywracania.

##### <a name="disadvantages"></a>Wady

* W zależności od klienta dostępu do programu SQL Server może występować większe opóźnienia, gdy jest uruchomiony program SQL Server w alternatywny kontroler domeny do aplikacji.
* Podczas kopiowania wirtualnych dysków twardych do usługi Premium storage może być długi. Może to mieć wpływ na Twoją decyzję od tego, czy zachować węzeł w grupie dostępności. Należy wziąć pod uwagę to podczas pracy o znacznym wykorzystaniu dziennika wykonywania ładowania podczas migracji jest to konieczne, ponieważ węzeł podstawowy musi zachować Niezreplikowane transakcje w jego dzienniku transakcji. W związku z tym ta może znacznie wzrośnie.
* W tym scenariuszu użyje Azure **Start AzureStorageBlobCopy** polecenia cmdlet jest asynchroniczna. Nie ma umowy SLA w momencie zakończenia. Czas kopii różni się podczas zależy to od oczekiwania w kolejce, również są zależne od ilości danych do przesłania. W związku z tym konieczne jest tylko jeden węzeł w centrum danych, 2, należy podjąć środki zaradcze w przypadku, gdy trwa dłużej niż podczas testowania, kopia. Te kroki zaradcze obejmują następujące pomysły:
  * Dodaj tymczasowe 2nd węzła programu SQL Server zapewnia wysoką dostępność, przed migracją uzgodnionych przestojów.
  * Uruchom migrację spoza platformy Azure zaplanowanej konserwacji.
  * Upewnij się, że Twoje kworum klastra zostały skonfigurowane prawidłowo.

W tym scenariuszu założono, że udokumentowanych instalacji i dowiedzieć się, jak magazyn jest mapowany w celu zmiany ustawień pamięci podręcznej dysku optymalne.

##### <a name="high-level-steps"></a>Ogólne kroki

![Multisite2][10]

* Upewnij lokalne / alternatywny podstawowego serwera SQL Azure DC oraz jej innych automatycznej pracy awaryjnej partnera (AFP).
* Zbieranie informacji o konfiguracji dysku z SQL2 i Usuń węzeł (nie należy usuwać dołączonymi dyskami VHD).
* Utwórz konto usługi Premium Storage i skopiuj wirtualne dyski twarde z konta magazynu w warstwie standardowa.
* Utwórz nową usługę w chmurze i tworzenie maszyny Wirtualnej SQL2 z jej dyski magazynu składki dołączone.
* Konfigurowanie wewnętrznego modułu równoważenia obciążenia / ELB i dodać punkty końcowe.
* Zaktualizuj zawsze na odbiornik za pomocą nowego wewnętrznego modułu równoważenia obciążenia / ELB IP adres i testowania trybu failover.
* Sprawdź konfigurację usług DNS.
* Zmień AFP SQL2 i migracji komputera sql1 do działania, a następnie wykonaj kroki od 2 do 5.
* Testy trybu failover.
* Przełącz się do komputera SQL1 i SQL2 AFP

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>Dodatek: Migracja wdrożenia w wielu lokacjach zawsze w klastrze do usługi Premium Storage

W dalszej części tego artykułu zawiera szczegółowy przykład konwertowanie klaster obejmujący wiele lokacji zawsze włączonych do usługi Premium storage. Konwertuje ono również odbiornik z przy użyciu zewnętrznym modułem równoważenia obciążenia (ELB) do wewnętrznego modułu równoważenia obciążenia (ILB).

### <a name="environment"></a>Środowisko

* Windows 2k 12 / SQL 2k 12
* Pliki bazy danych 1 SP
* 2 x pule magazynu w każdym węźle

![Appendix1][11]

### <a name="vm"></a>VM:

W tym przykładzie użyjemy do zademonstrowania przenoszony z ELB do wewnętrznego modułu równoważenia obciążenia. ELB była dostępna przed wewnętrznego modułu równoważenia obciążenia, więc to pokazuje, jak przełączyć się do wewnętrznego modułu równoważenia obciążenia podczas migracji.

![Appendix2][12]

### <a name="pre-steps-connect-to-subscription"></a>Czynności wstępne: Połącz z subskrypcją

```powershell
Add-AzureAccount

#Set up subscription
Get-AzureSubscription
```

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>Krok 1: Utwórz nowe konto magazynu i usługi w chmurze

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

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>Krok 2: Zwiększ dozwolonych błędów w zasobach \<Optional >

W niektórych zasobów, które należą do sieci zawsze włączonej grupy dostępności istnieją limity jak wiele błędów, które mogą wystąpić w okresie, w którym usługa klastrowania podejmie próbę ponownego uruchomienia grupy zasobów. Zalecane jest, że zwiększenie to o ile możesz są zalet tej procedury od w przeciwnym razie ręcznie trybu failover i wyzwalania trybu failover przez zamknięcie maszyny, na których można uzyskać blisko tego limitu.

Byłoby rozsądne dwukrotnie dopuszczalna liczba awarii, w tym celu w Menedżerze klastra trybu Failover, przejdź do właściwości grupy zasobów, zawsze włączone:

![Appendix3][13]

Zmień maksymalna liczba awarii na 6.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>Krok 3: Dodawanie adresu IP zasobu dla grupy klastra \<Optional >

Jeśli masz tylko jeden adres IP dla grupy klastra i to jest wyrównany do podsieci, w chmurze, należy pamiętać, jeśli przypadkowo w tryb offline wszystkich węzłów klastra w chmurze w tej sieci, a następnie zasób adresu IP klastra i nazwa sieciowa klastra są nie będą mogli trybu online. W takiej sytuacji uniemożliwia ona aktualizacje dla innych zasobów klastra.

#### <a name="step-4-dns-configuration"></a>Krok 4: Konfiguracja usługi DNS

Implementowanie płynne przejście zależy od tego, jak DNS jest wykorzystywany i zaktualizowane.
Po Always On zainstalowaniu obejmuje tworzenie grupy zasobów klastra Windows, jeśli otworzysz Menedżera klastra trybu Failover, zobaczysz, że co najmniej ma trzy zasoby, istnieją dwie, które dokument odwołuje się do:

* Nazwa sieci wirtualnej (VNN) — nazwa DNS, które klienci łączą się, jeśli chcą łączyć się z serwerami SQL przy użyciu zawsze włączonych.
* Zasób adresu IP — adres IP, z VNN skojarzona, może mieć więcej niż jeden, a w wielu lokacjach konfiguracji masz adres IP na witrynę/podsieci.

Podczas nawiązywania połączenia z programem SQL Server, SQL Server Client sterownik pobiera rekordy DNS skojarzone z odbiornikiem i próbuje nawiązać połączenie z każdego Always On skojarzony adres IP. Następnie omówimy pewne czynniki, które wpływają na to.

Liczba współbieżnych rekordów DNS, które są skojarzone z odbiornik o nazwie zależy nie tylko w liczbę adresów IP skojarzonych, ale "RegisterAllIpProviders'setting w klastrze trybu Failover dla zasobu zawsze VNN dalej.

Podczas wdrażania zawsze na platformie Azure są różne kroki, aby utworzyć odbiornik i adresy IP, trzeba ręcznie skonfigurować RegisterAllIpProviders 1, różni się to do lokalnego wdrożenia zawsze włączonej którym już jest ustawiona na 1.

Jeśli "RegisterAllIpProviders" ma wartość 0, a następnie zostanie wyświetlony tylko jeden rekord DNS w systemie DNS skojarzone z odbiornikiem:

![Appendix4][14]

Jeśli "RegisterAllIpProviders" ma wartość 1:

![Appendix5][15]

Poniższy kod wykonuje ustawienia VNN i ustawia go dla Ciebie. Aby zmiana zaczęła obowiązywać należy przełączyć w tryb offline VNN i włącz ją z powrotem do trybu online. Spowoduje to przejście odbiornik offline powodujące zakłócenia łączności klienta.

```powershell
##Always On Listener Name
$ListenerName = "Mylistener"
##Get AlwaysOn Network Name Settings
Get-ClusterResource $ListenerName| Get-ClusterParameter
##Set RegisterAllProvidersIP
Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1
```

Na późniejszym etapie migracji należy zaktualizować odbiornik zawsze włączonych za pomocą aktualizacja adresu IP, który odwołuje się moduł równoważenia obciążenia, obejmuje to usunięcie zasobu adresu IP i dodawanie. Po zaktualizowaniu adresów IP należy upewnić się, nowy adres IP został zaktualizowany w strefie DNS i klientów aktualizowania ich lokalnej pamięci podręcznej DNS.

Jeśli klienci znajdują się w segmencie inną sieć i odwoływać się do innego serwera DNS, należy wziąć pod uwagę, co się stanie, dotyczące transferu strefy DNS podczas migracji, jak ponownie połączyć aplikację czas jest ograniczone przez czas transferu strefy z dowolnego nowego adresu IP adresy dla odbiornika. Jeśli jesteś w obszarze ograniczenia czasu w tym miejscu, należy omówić i przetestować, wymuszając przyrostowy transfer strefy z zespołami Windows, a także umieścić rekordu hosta systemu DNS do niższych czas wygaśnięcia (TTL), więc klienci zaktualizować. Aby uzyskać więcej informacji, zobacz [przyrostowe transferów stref](https://technet.microsoft.com/library/cc958973.aspx) i [Start DnsServerZoneTransfer](https://docs.microsoft.com/powershell/module/dnsserver/start-dnsserverzonetransfer).

Domyślny czas wygaśnięcia rekordu DNS, który jest skojarzony z odbiornik zawsze na platformie Azure jest 1200 sekund. Możesz zmniejszyć to, jeśli znajdują się w czasie ograniczenia podczas migracji w taki sposób, aby zapewnić klientom zaktualizować ich DNS przy użyciu zaktualizowano adres IP dla odbiornika. Można wyświetlić i zmodyfikować konfigurację przez zrzucanie limit konfiguracji VNN:

```powershell
$AGName = "myProductionAG"
$ListenerName = "Mylistener"
#Look at HostRecordTTL
Get-ClusterResource $ListenerName| Get-ClusterParameter

#Set HostRecordTTL Examples
Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120
```

> [!NOTE]
> Im niższa "HostRecordTTL" większej liczby ruch DNS występuje.

##### <a name="client-application-settings"></a>Ustawienia aplikacji klienta

Jeśli Twoja aplikacja kliencka SQL obsługuje .NET 4.5 SQLClient, a następnie można użyć "MULTISUBNETFAILOVER = TRUE" — słowo kluczowe. This — słowo kluczowe powinny być stosowane, ponieważ umożliwia szybsze połączenia zawsze włączonej grupy dostępności SQL podczas pracy awaryjnej. Wylicza wszystkie adresy IP skojarzone z odbiornik zawsze włączonych równolegle i wykonuje wyższe szybkości ponawiania prób połączenia protokołu TCP podczas przejścia w tryb failover.

Aby uzyskać więcej informacji na temat poprzednich ustawień, zobacz [MultiSubnetFailover — słowo kluczowe i skojarzonych z funkcji](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover). Zobacz też [Obsługa SqlClient dla wysokiej dostępności, odzyskiwania po awarii](https://msdn.microsoft.com/library/hh205662\(v=vs.110\).aspx).

#### <a name="step-5-cluster-quorum-settings"></a>Krok 5. Ustawienia kworum klastra

Jak będą zajmuje się co najmniej jeden serwer SQL nie działa w czasie, należy zmodyfikować konfigurację kworum klastra, jeśli używa monitora udziału plików (FSW) z dwoma węzłami, należy skonfigurować kworum w celu zezwalania na Większość węzłów i korzystanie z dynamicznego do głosowania , pozwalając na jednym węźle pozostaje stały.

```powershell
Set-ClusterQuorum -NodeMajority  
```

Aby uzyskać więcej informacji na temat konfigurowania kworum klastra i zarządzanie nimi, zobacz [Konfigurowanie i zarządzanie kworum w klastrze trybu Failover systemu Windows Server 2012](https://technet.microsoft.com/library/jj612870.aspx).

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>Krok 6: Wyodrębnij istniejące punkty końcowe i listami kontroli dostępu

```powershell
#GET Endpoint info
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
#GET ACL Rules for Each EP, this example is for the Always On Endpoint
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  
```

Zapisz ten tekst do pliku.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>Krok 7: Zmień partnerskie trybu Failover i trybach replikacji

Jeśli masz więcej niż dwa serwery SQL, należy zmienić trybu failover, innym pomocniczej w innego kontrolera domeny lub lokalnego "Synchroniczny" i ułatwiają automatycznej pracy awaryjnej partnera AFP (), to obsługa wysokiej dostępności, podczas gdy w przypadku wprowadzania zmian. Można to zrobić przy użyciu języka TSQL programu jednak modyfikować SSMS:

![Appendix6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>Krok 8: Usuń pomocniczej maszyny Wirtualnej z usługą w chmurze

Należy zaplanować najpierw migracji węzła pomocniczego w chmurze. Jeśli ten węzeł jest obecnie głównej, należy zainicjować ręcznej pracy awaryjnej.

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
Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
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

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>Krok 9: Zmień ustawienia w pliku CSV buforowania dysku i Zapisz

Woluminy danych tych powinna być równa tylko do odczytu.

Woluminy TLOG te należy ustawić na wartość NONE.

![Appendix7][17]

#### <a name="step-10-copy-vhds"></a>Krok 10: Skopiuj wirtualne dyski TWARDE

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


Aby sprawdzić stan kopiowania dysków VHD na koncie usługi Premium Storage:

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

![Appendix8][18]

Poczekaj, aż wszystkie te są rejestrowane jako Powodzenie.

Aby uzyskać informacje dotyczące poszczególnych obiektów blob:

```powershell
Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext
```

#### <a name="step-11-register-os-disk"></a>Krok 11: Zarejestruj dysku systemu operacyjnego

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

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>Krok 12: Importowanie dodatkowych do nowej usługi w chmurze

Poniższy kod również używa opcji dodanych w tym miejscu możesz zaimportować maszyny i używać retainable adresów VIP.

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

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>Krok 13 Tworzenie wewnętrznego modułu równoważenia obciążenia w nowej usłudze chmury, Dodaj obciążenia zrównoważone punktów końcowych i listami kontroli dostępu

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

#### <a name="step-14-update-always-on"></a>Krok 14. Zawsze włączone aktualizacji

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

![Appendix9][19]

Teraz usunąć stare usługę w chmurze adresu IP.

![Appendix10][20]

#### <a name="step-15-dns-update-check"></a>Krok 15. Sprawdzanie aktualizacji DNS

Teraz należy sprawdzić serwery DNS w sieciach klienta programu SQL Server i upewnij się, że klaster został dodany rekord hosta dodatkowe dodano adres IP. Jeśli te serwery DNS nie zostały zaktualizowane, należy wziąć pod uwagę wymuszania transferu strefy DNS i upewnij się, że klienci w podsieci są w stanie rozpoznać zawsze na adresy IP, jest to, aby nie trzeba czekać na automatyczne replikacji DNS.

#### <a name="step-16-reconfigure-always-on"></a>Krok 16. Ponowna konfiguracja zawsze włączone

W tym momencie czekasz na pomocniczym tego węzła, który został zmigrowany pełni ponownego zsynchronizowania jej z węzła w środowisku lokalnym i przejdź do węzła replikacji synchronicznej i AFP.  

#### <a name="step-17-migrate-second-node"></a>W kroku 17: Migrowanie drugiego węzła

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
Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
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

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>Krok 18: Zmień ustawienia w pliku CSV buforowania dysku i Zapisz

Woluminy danych ustawienia pamięci podręcznej powinna być równa tylko do odczytu.

W przypadku woluminów TLOG ustawienia pamięci podręcznej powinna być równa NONE.

![Appendix11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>Krok 19: Utwórz nowe konto magazynu niezależne dla węzła pomocniczego

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

#### <a name="step-20-copy-vhds"></a>Krok 20: Skopiuj wirtualne dyski TWARDE

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

Możesz sprawdzić stan kopiowania wirtualnego dysku twardego dla wszystkich wirtualnych dysków twardych:

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

![Appendix12][22]

Poczekaj, aż wszystkie te są rejestrowane jako Powodzenie.

Aby uzyskać informacje dotyczące poszczególnych obiektów blob:

```powershell
#Check individual blob status
Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContextnode2
```

#### <a name="step-21-register-os-disk"></a>W kroku 21: Zarejestruj dysku systemu operacyjnego

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

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>Krok 22: Dodaj obciążenia zrównoważone punktów końcowych i listami kontroli dostępu

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

#### <a name="step-23-test-failover"></a>Krok 23: Testowanie pracy w trybie failover

Poczekaj, aż migrowanych węzeł, aby zsynchronizować z węzłem Always On w środowisku lokalnym. Umieść go w tryb replikacji synchronicznej i zaczekaj, aż jest zsynchronizowany. Następnie tryb failover z lokalnych pierwszy węzeł migracji, czyli AFP. Po pracował, zmień ostatniego węzła migrowanych AFP.

Należy testować tryb failover między wszystkie węzły, a następnie uruchomić, chociaż oczekiwano chaos testy, aby upewnić się działać przejścia w tryb failover, jak i w odpowiednim czasie manor.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>Krok 24: Odłożyć ustawienia kworum klastra / czasu wygaśnięcia DNS / Pntrs trybu Failover / ustawienia synchronizacji

##### <a name="adding-ip-address-resource-on-same-subnet"></a>Dodawanie zasobu adresu IP na tej samej podsieci

Jeśli istnieją tylko dwa serwery SQL i chcesz przeprowadzić ich migrację do nowej usługi w chmurze, ale chce zachować je w tej samej podsieci, można uniknąć, biorąc odbiornika w trybie offline, aby usunąć oryginalny zawsze na adres IP i dodać nowy adres IP. Maszyny wirtualne są migrowane do innej podsieci, nie trzeba to zrobić, ponieważ ma sieci dodatkowe klastra, który odwołuje się do tej podsieci.

Po wznowione migrowanych pomocniczej i dodany do nowego zasobu adres IP dla nowej usługi w chmurze przed włączeniem trybu failover istniejącą główną powinny wykonaj następujące czynności w Menedżerze klastra trybu Failover:

Aby dodać adres IP, zobacz dodatek, krok 14.

1. Zmień możliwych właścicieli, "Istniejącej głównej SQL Server", w tym przykładzie "dansqlams4" dla bieżącego zasobu adresu IP:

    ![Appendix13][23]
2. Zmień możliwych właścicieli do"zmigrowane pomocniczego programu SQL Server", w tym przykładzie "dansqlams5" dla nowego zasobu adres IP:

    ![Appendix14][24]
3. Gdy ta opcja jest ustawiona, możesz przełączyć działanie, a po zmigrowaniu ostatniego węzła możliwych właścicieli powinny być edytowane, więc ten węzeł zostanie dodany jako możliwego właściciela:

    ![Appendix15][25]

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Azure Premium Storage](../disks-types.md)
* [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/)
* [SQL Server na maszynach wirtualnych platformy Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

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
