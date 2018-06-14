---
title: Magazyn w warstwie Premium Azure za pomocą programu SQL Server | Dokumentacja firmy Microsoft
description: W tym artykule używa zasobów utworzone za pomocą klasycznym modelu wdrażania i zawiera wskazówki dotyczące przy użyciu usługi Azure Premium Storage z programem SQL Server uruchomiony na maszynach wirtualnych platformy Azure.
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
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
ms.author: jroth
ms.openlocfilehash: 3d3fdd8865a293c5e2f0df6a97910ac8e2a07d4c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29400617"
---
# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Korzystanie z usługi Azure Premium Storage z programem SQL Server na maszynach wirtualnych
## <a name="overview"></a>Przegląd
[Usługa Azure Premium Storage](../premium-storage.md) to nowa generacja magazynów, który zapewnia małe opóźnienia i wysokiej wydajności we/wy. Najlepsza dla klucza obciążeń intensywnie wykorzystujących we/wy, takich jak program SQL Server na IaaS [maszyn wirtualnych](https://azure.microsoft.com/services/virtual-machines/).

> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../azure-resource-manager/resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.

Ten artykuł zawiera wskazówki dotyczące migrowania maszyny wirtualnej z uruchomionym programem SQL Server do używania magazyn w warstwie Premium i planowania. W tym infrastruktury platformy Azure (sieci, magazynu) i kroki maszyny Wirtualnej systemu Windows gościa. Przykład [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) zawiera kompleksowe pełnej migracji kompleksowe jak przenieść większych maszyn wirtualnych przeprowadzać ulepszone lokalnego magazynu dysków SSD przy użyciu programu PowerShell.

Należy zrozumieć proces end-to-end pomocą usługi Azure Premium Storage z programem SQL Server na maszynach wirtualnych IAAS. Obejmuje to:

* Identyfikacja wymagań wstępnych, aby użyć magazyn w warstwie Premium.
* Przykłady wdrażania serwera SQL na IaaS magazyn w warstwie Premium dla nowych wdrożeń.
* Przykłady Migrowanie istniejących wdrożeń zarówno serwerów autonomicznych, jak i wdrożeń przy użyciu SQL zawsze włączonych grup dostępności.
* Podejścia możliwej migracji.
* Pełne end-to-end przykład kroki Azure, Windows i program SQL Server do migracji z istniejącej implementacji zawsze włączony.

Aby uzyskać więcej informacji o tła w programie SQL Server w maszynach wirtualnych platformy Azure, zobacz [programu SQL Server w usłudze Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

**Autor:** Sol Danielowi **recenzenci techniczni:** Śledź Vargas Artur Luis, Sanjay Mishra, Pravin Mital, blogu Thomasa Juergen, Gonzalo Ruiz.

## <a name="prerequisites-for-premium-storage"></a>Wymagania wstępne dotyczące magazyn w warstwie Premium
Istnieje kilka wymagań wstępnych dotyczących używania magazyn w warstwie Premium.

### <a name="machine-size"></a>Rozmiar maszyny
Dla magazynu Premium, musisz użyć serii DS maszyn wirtualnych (VM). Jeśli nie używasz maszyny serii DS w usługi w chmurze przed, należy usunąć istniejącą maszynę Wirtualną, Zachowaj dołączonych dysków i następnie utwórz nową usługę w chmurze przed ponownym utworzeniem jako DS * rozmiaru roli maszyny Wirtualnej. Aby uzyskać więcej informacji na temat rozmiarów maszyny wirtualnej, zobacz [maszyny wirtualnej i rozmiary usługi chmury Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="cloud-services"></a>Usługi w chmurze
Po utworzeniu nowej usługi w chmurze tylko można DS * maszyn wirtualnych z magazyn w warstwie Premium. Jeśli używasz programu SQL Server zawsze na platformie Azure, zawsze na odbiornika odwołuje się do adres Azure wewnętrznego lub zewnętrznego adresu IP usługi równoważenia obciążenia, który jest skojarzony z usługą w chmurze. W tym artykule przedstawiono sposób migracji przy zachowaniu dostępności w tym scenariuszu.

> [!NOTE]
> Serii DS * musi być pierwszym maszynę Wirtualną, która została wdrożona do nowej usługi w chmurze.
>
>

### <a name="regional-vnets"></a>Regionalnych sieci wirtualnych
Dla maszyn wirtualnych DS * należy skonfigurować wirtualne sieci (VNET) obsługującym maszyny wirtualne jako regionalne. To "rozszerzenie" sieci Wirtualnej jest umożliwienie większych maszyn wirtualnych do obsługi administracyjnej w innych klastrach i zezwolić na komunikację między nimi. Na poniższym zrzucie ekranu wyróżnioną lokalizację pokazuje regionalnych sieci wirtualnych, pierwszego wyniku pokazuje "wąskie" sieci Wirtualnej.

![RegionalVNET][1]

Można zwiększyć biletu pomocy technicznej firmy Microsoft, aby przeprowadzić migrację do regionalnej sieci Wirtualnej. Firma Microsoft udostępnia następnie zmiany. Aby ukończyć migrację do regionalnej sieci wirtualnych, zmień wartość właściwości AffinityGroup w konfiguracji sieci. Najpierw wyeksportować konfigurację sieci w programie PowerShell, a następnie zastąp **AffinityGroup** właściwości w **VirtualNetworkSite** element z **lokalizacji** właściwości. Określ `Location = XXXX` gdzie `XXXX` jest region platformy Azure. Następnie można zaimportować nową konfigurację.

Na przykład biorąc pod uwagę następujące konfigurację sieci Wirtualnej:

    <VirtualNetworkSite name="danAzureSQLnet" AffinityGroup="AzureSQLNetwork">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

Aby przenieść to regionalnej sieci Wirtualnej w Europa Zachodnia, należy zmienić konfigurację do następującego:

    <VirtualNetworkSite name="danAzureSQLnet" Location="West Europe">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

### <a name="storage-accounts"></a>Konta magazynu
Musisz utworzyć nowe konto magazynu, który jest skonfigurowany dla usługi Premium Storage. Należy zauważyć, że użycie magazyn w warstwie Premium jest dla konta magazynu, nie dla poszczególnych dysków VHD, jednak podczas korzystania z maszyny Wirtualnej serii DS * możesz dołączyć pliki VHD z konta Premium i standardowa magazynu. Może to rozważyć, jeśli nie chcesz umieścić wirtualnego dysku twardego systemu operacyjnego do konta Premium Storage.

Następujące **AzureStorageAccountPowerShell nowy** z "Premium_LRS" **typu** tworzy konto magazynu Premium:

    $newstorageaccountname = "danpremstor"
    New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   

### <a name="vhds-cache-settings"></a>Ustawienia pamięci podręcznej wirtualne dyski twarde
Główną różnicą między tworzenie dysków, które są częścią konta Premium Storage jest ustawienie dyskowej pamięci podręcznej. Dla danych programu SQL Server woluminu dyski go zaleca się użycie "**buforowania odczytu**". Woluminy dziennika transakcji, ustawienie dyskowej pamięci podręcznej, należy wybrać opcję "**Brak**". Różni się to od zaleceń dotyczących kont magazynu w warstwie standardowa.

Po dołączeniu wirtualne dyski twarde nie można zmienić ustawienia pamięci podręcznej. Należy odłączyć i ponownie dołączyć wirtualny dysk twardy z ustawieniem zaktualizowano pamięci podręcznej.

### <a name="windows-storage-spaces"></a>Miejsca do magazynowania systemu Windows
Można użyć [miejsca do magazynowania systemu Windows](https://technet.microsoft.com/library/hh831739.aspx) tak samo jak z poprzedniego magazynu w warstwie standardowa, dzięki temu można migrować maszynę Wirtualną, która jest już przy użyciu funkcji miejsca do magazynowania. Przykład [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) kod programu Powershell, aby wyodrębnić i zaimportować maszynę Wirtualną za pomocą wielu wirtualnych dysków twardych dołączonych pokazuje (krok 9 i dalej).

Pule magazynu były używane z kontem magazynu platformy Azure standardowe zwiększyć przepustowość i zmniejsza opóźnienia. Wartość można znaleźć w testowanie pod kątem nowych wdrożeń pul magazynu z atrybutem magazyn w warstwie Premium, ale co zwiększa stopnia złożoności z instalacji magazynu.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>Jak znaleźć które mapy dyski wirtualne Azure do pul magazynu
Jako pamięci podręcznej różne ustawienia zalecenia dotyczące dołączonych dysków VHD, można zdecydować skopiować pliki VHD do konta Premium Storage. Jednak w przypadku podłącz je do nowej serii DS maszyny Wirtualnej, może być konieczne zmiany ustawień pamięci podręcznej. Jest łatwiejsze do zastosowania zalecanych ustawień pamięci podręcznej, jeśli masz VHD oddzielne pliki danych SQL i plików dziennika (zamiast dla pojedynczego wirtualnego dysku twardego, który zawiera zarówno) Magazyn w warstwie Premium.

> [!NOTE]
> Jeśli masz pliki danych i dziennika programu SQL Server na tym samym woluminie, wybrana opcja buforowania zależy od wzorce dostępu we/wy dla obciążeń bazy danych. Tylko testowania wykaże buforowania, która opcja jest najlepsza w przypadku tego scenariusza.
>
>

Jeśli używasz miejsca do magazynowania systemu Windows, które składają się z wielu dysków VHD, należy przyjrzeć się oryginalnego skryptów do identyfikowania, która dołączona wirtualne dyski twarde są jednak w określonej puli, więc można zdefiniować ustawienia pamięci podręcznej odpowiednio dla każdego dysku.

Jeśli nie masz oryginalny skrypt dostępne do wyświetlenia, które mapują wirtualnych dysków twardych do puli magazynu, można użyć następujące kroki do określenia mapowania puli magazynowania dysku.

Dla każdego dysku wykonaj następujące kroki:

1. Pobranie listy dysków dołączonych do maszyny Wirtualnej z **Get-AzureVM** polecenia:

    Get-AzureVM -ServiceName <servicename> -Name <vmname> | Get-AzureDataDisk
2. Zanotuj Diskname i jednostki LUN.

    ![DisknameAndLUN][2]
3. Pulpit zdalny do maszyny Wirtualnej. Następnie przejdź do **Zarządzanie komputerem** | **Menedżera urządzeń** | **dysków**. Sprawdź właściwości każdej "Dysków wirtualnych firmy Microsoft"

    ![VirtualDiskProperties][3]
4. Numer jednostki LUN, w tym miejscu jest odwołaniem do numer jednostki LUN, do którego należy określić, gdy dołączanie wirtualnego dysku twardego do maszyny Wirtualnej.
5. Dla "Microsoft Virtual Disk" Przejdź do **szczegóły** kartę, a następnie w **właściwości** listy, przejdź do **klucz sterownika**. W **wartość**, Uwaga **przesunięcia**, która jest 0002 na poniższym zrzucie ekranu. 0002 oznacza Dysk_fizyczny_2, który odwołuje się do puli magazynu.

    ![VirtualDiskPropertyDetails][4]
6. Dla każdej puli magazynów zrzutu limit skojarzone dyski:

    Get-StoragePool - FriendlyName AMS1pooldata | Get-PhysicalDisk

    ![GetStoragePool][5]

Teraz można używać tych informacji, aby skojarzyć dołączony wirtualne dyski twarde do dysków fizycznych w pule magazynu.

Wirtualne dyski twarde zmapowaniu do dysków fizycznych w pule magazynu można odłączyć i skopiuj je za pośrednictwem konta Premium Storage następnie dołącz je z ustawieniem poprawne pamięci podręcznej. Zapoznaj się z przykładem w [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage), kroki od 8 do 12. Poniższe kroki przedstawiają sposób Wyodrębnij konfiguracji dysku VHD maszyny Wirtualnej, dołączyć do pliku CSV, skopiować pliki VHD, zmiany ustawień pamięci podręcznej konfiguracji dysku, a na koniec należy ponownie wdrożyć maszynę Wirtualną jako serii DS maszyny Wirtualnej z wszystkich dołączonych dysków.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>Maszyna wirtualna magazynu przepustowość i przepływność magazynu wirtualnego dysku twardego
Wydajność magazynu zależy od wybranego rozmiaru maszyny Wirtualnej DS * i rozmiary dysku VHD. Maszyny wirtualne mają różnych dodatków dla wielu dysków VHD, które można dołączać i maksymalnej przepustowości, które obsługują (MB/s). Numery określonych przepustowości dla [maszyny wirtualnej i rozmiary usługi chmury Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Zwiększona IOPS są osiągane o dużym rozmiarze dysku. Należy rozważyć to Zastanawiając się ścieżki migracji. Aby uzyskać szczegółowe informacje [zobacz tabelę IOPS i typów dysków](../premium-storage.md#scalability-and-performance-targets).

Na koniec należy rozważyć, czy maszyny wirtualne mają inny dysk maksymalnej przepustowości, obsługujących dla wszystkich dysków dołączonych. Mocno obciążony może zużyć całą dysku maksymalną przepustowość dla tego rozmiaru roli maszyny Wirtualnej. Na przykład Standard_DS14 obsługuje maksymalnie 512 MB/s; w związku z tym z trzech dysków P30 można nasycenia przepustowości dysków maszyny wirtualnej. Jednak w tym przykładzie limit przepustowości może zostać przekroczona w zależności od różnych odczytu i zapisu z systemem IOs.

## <a name="new-deployments"></a>Nowych wdrożeń
W dwóch następnych sekcjach pokazują, jak można wdrożyć maszyn wirtualnych programu SQL Server magazyn w warstwie Premium. Jak wspomniano wcześniej, niekoniecznie zbędna, nie można umieścić dysk systemu operacyjnego na magazyn w warstwie Premium. Można to zrobić, jeśli mają zamiar Umieść wszystkie intensywnych obciążeń we/wy na wirtualny dysk twardy systemu operacyjnego.

W pierwszym przykładzie pokazano, przy użyciu istniejących obrazów w galerii Azure. Drugi przykład przedstawia sposób użycia niestandardowego obrazu maszyny Wirtualnej, czy masz istniejące konta magazynu w warstwie standardowa.

> [!NOTE]
> Tych przykładów założono, że utworzono już regionalnej sieci Wirtualnej.
>
>

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>Utwórz nową maszynę Wirtualną z magazynu Premium z galerii
W poniższym przykładzie pokazano, jak umieścić wirtualnego dysku twardego systemu operacyjnego na magazyn w warstwie premium i Dołącz wirtualne dyski twarde Premium magazynu. Można jednak również umieścić dysk systemu operacyjnego w ramach konta Standard Storage, a następnie dołącz wirtualne dyski twarde, które znajdują się na koncie magazynu Premium. Przedstawiono oba scenariusze.

    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Set up subscription
    Set-AzureSubscription -SubscriptionName $mysubscription
    Select-AzureSubscription -SubscriptionName $mysubscription -Current  

#### <a name="step-1-create-a-premium-storage-account"></a>Krok 1: Tworzenie konta magazynu w warstwie Premium
    #Create Premium Storage account, note Type
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  


#### <a name="step-2-create-a-new-cloud-service"></a>Krok 2: Tworzenie nowej usługi w chmurze
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>Krok 3: Reserve VIP usługi chmury (opcjonalnie)
    #check exisitng reserved VIP
    Get-AzureReservedIP

    $reservedVIPName = “sqlcloudVIP”
    New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location

#### <a name="step-4-create-a-vm-container"></a>Krok 4: Tworzenie kontenera maszyny Wirtualnej
    #Generate storage keys for later
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    ##Generate storage acc contexts
    $xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

    #Create container
    $containerName = 'vhds'
    New-AzureStorageContainer -Name $containerName -Context $xioContext

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>Krok 5: Wprowadzenie do wirtualnego dysku twardego systemu operacyjnego na Standard lub Premium Storage
    #NOTE: Set up subscription and default storage account which is used to place the OS VHD in

    #If you want to place the OS VHD Premium Storage Account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  

    #If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
    $standardstorageaccountname = "danstdams"

    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname

#### <a name="step-6-create-vm"></a>Krok 6: Tworzenie maszyny Wirtualnej
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

    #create new Avaiability Set
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


### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>Utwórz nową maszynę Wirtualną do użycia z niestandardowego obrazu magazyn w warstwie Premium
W tym scenariuszu pokazano, gdzie masz istniejących dostosowanych obrazów, które znajdują się na koncie magazynu w warstwie standardowa. Jak wspomniano, jeśli chcesz umieścić wirtualnego dysku twardego systemu operacyjnego na magazyn w warstwie Premium należy skopiować obrazu, który istnieje w ramach konta Standard Storage i przeniesienie ich do magazyn w warstwie Premium, zanim będzie można go używać. Jeśli masz obrazu lokalnej, możesz również użyć tej metody do skopiowania który bezpośrednio do konta Premium Storage.

#### <a name="step-1-create-storage-account"></a>Krok 1: Tworzenie konta magazynu
    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

    #Standard Storage account
    $origstorageaccountname = "danstdams"

#### <a name="step-2-create-cloud-service"></a>Krok 2 Tworzenie usługi w chmurze
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-use-existing-image"></a>Krok 3: Użyj istniejącego obrazu
Można użyć istniejącego obrazu. Można też [zająć obraz istniejącej maszyny](../classic/capture-image-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Należy pamiętać, komputera, do którego należy obrazu musi być DS * maszyny. Po utworzeniu obrazu, poniższe kroki pokazują, jak skopiować go do konta Premium Storage z **Start AzureStorageBlobCopy** polecenia programu PowerShell.

    #Get storage account keys:
    #Standard Storage account
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    #Premium Storage account
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    #Set up contexts for the storage accounts:
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $destContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

#### <a name="step-4-copy-blob-between-storage-accounts"></a>Krok 4: Kopię Blob między kontami magazynu
    #Get Image VHD
    $myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
    $containerName = 'vhds'

    #Copy Blob between accounts
    $blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
    -DestContainer vhds -Destblob "prem-$myImageVHD" `
    -Context $origContext -DestContext $destContext  

#### <a name="step-5-regularly-check-copy-status"></a>Krok 5: Regularne sprawdzanie stanu kopiowania:
    $blob | Get-AzureStorageBlobCopyState

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>Krok 6: Dodawanie obrazu dysku na dysk Azure repozytorium w subskrypcji
    $imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
    $newimageName = "prem"+"dansoldonorsql2k14"

    Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation

> [!NOTE]
> Może się okazać, że mimo że zgłasza stan jako Powodzenie, może nadal otrzymywać, błąd dzierżawy dysku. W takim przypadku poczekaj około 10 minut.
>
>

#### <a name="step-7--build-the-vm"></a>Krok 7: Tworzenie maszyny Wirtualnej
W tym miejscu tworzysz maszynę Wirtualną z obrazu i dołączanie dwóch dysków VHD magazynu Premium:

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

    #create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS3"

    #Machine User Credentials
    $userName = "myadmin"
    $pass = "theM)stC0mplexP@ssw0rd!”


    #Create VM Config
    $vmConfigsl2 = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $newimageName  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-Datadisk-1.vhd"
    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "LogDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-logdisk-1.vhd"



    $vmConfigsl2 | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Istniejące wdrożenia, które nie należy używać zawsze włączonych grup dostępności
> [!NOTE]
> Istniejące wdrożenia, najpierw zobacz [wymagania wstępne](#prerequisites-for-premium-storage) sekcji tego artykułu.
>
>

Istnieją różne zagadnienia dotyczące wdrożenia programu SQL Server, których nie należy używać zawsze włączonych grup dostępności i tych, które wykonują. Jeśli nie jest używana zawsze włączone i mieć istniejących autonomicznego serwera SQL, można uaktualnić do magazyn w warstwie Premium, przy użyciu nowego konta magazyn i usługa w chmurze. należy wziąć pod uwagę następujące opcje:

* **Tworzenie nowej maszyny Wirtualnej serwera SQL**. Można utworzyć nowego programu SQL Server maszynę Wirtualną, która używa konta Premium Storage, zgodnie z opisem w nowych wdrożeń. Następnie wykonania kopii zapasowej i przywracanie baz danych konfiguracji i użytkownika programu SQL Server. Aplikacja musi zostać zaktualizowany do odwołania nowy serwer SQL, jeśli jest uzyskiwany wewnętrznie i zewnętrznie. Należy skopiować wszystkie obiekty "z bazy danych", jaką wykonujesz migrację obok siebie (SxS) programu SQL Server. Obejmuje to obiekty, takie jak nazwy logowania, certyfikaty i połączone serwery.
* **Migrowanie istniejących maszyn wirtualnych serwera SQL**. Wymaga to przełączania w tryb offline maszyna wirtualna programu SQL Server, a następnie przenoszenia go do nowej usługi w chmurze, która obejmuje kopiowanie wszystkich jego dołączonych dysków VHD na konta Premium Storage. Po przejściu do trybu online maszyny Wirtualnej, aplikacja odwołuje się nazwa hosta serwera jako przed. Należy pamiętać, że istniejący dysk ma wpływ na charakterystyki wydajności. Na przykład dysk 400 GB pobiera zaokrąglona w górę do P20. Jeśli znasz nie wymagają tego wydajności dysku, można ponownie utworzyć maszynę Wirtualną jako maszynę Wirtualną serii DS i Dołącz Premium magazynu wirtualnych dysków twardych specyfikacji wydajności rozmiar wymagane. Następnie można odłączyć i ponownie dołączyć plików bazy danych SQL.

> [!NOTE]
> Kopiowanie dysków VHD, należy pamiętać o rozmiarze, zależnie od wielkości oznacza, że typ dysku magazynu Premium dzielą się na, określa specyfikacji wydajności dysku. Azure zaokrąglenie do najbliższej rozmiaru dysku, więc jeśli dysk 400 GB, to jest zaokrąglona w górę do P20. W zależności od wymagań we/wy istniejącego wirtualnego dysku twardego systemu operacyjnego może nie trzeba przeprowadzać migracji to konto magazynu Premium.
>
>

Jeśli program SQL Server jest dostępny zewnętrznie, ulegnie zmianie adresu VIP usługi chmury. Masz również aktualizacji punktów końcowych, listy ACL i w systemie DNS ustawienia.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Istniejące wdrożenia, które używają zawsze włączone grupy dostępności
> [!NOTE]
> Istniejące wdrożenia, najpierw zobacz [wymagania wstępne](#prerequisites-for-premium-storage) sekcji tego artykułu.
>
>

Początkowo w tej sekcji opisano, zawsze na współdziałanie z sieci platformy Azure. Firma Microsoft następnie rozbić migracje w przypadku dwóch scenariuszy: migracje, w którym mogą następować Przestój i migracje, w którym należy osiągnąć minimalnym czasem przestojów.

Lokalnego programu SQL Server zawsze włączonych grup dostępności Użyj odbiornika lokalnych, które rejestruje wirtualnego nazwę DNS oraz adres IP, który jest współużytkowana przez jeden lub więcej serwerów SQL. Jeżeli klienci łączą się routingu IP odbiornika do podstawowego serwera SQL. Jest to serwer, który jest właścicielem zasobu zawsze na IP, w tym czasie.

![DeploymentsUseAlways na][6]

W systemie Microsoft Azure może mieć tylko jeden adres IP przypisane do karty Sieciowej na maszynie Wirtualnej tak aby osiągnąć tę samą warstwę abstrakcji jako lokalne, Azure korzysta z adres IP, który jest przypisany do usługi równoważenia obciążenia wewnętrznego/zewnętrzne (ILB/ELB). Zasób adresu IP, który jest współużytkowana przez serwery ustawiono tego samego adresu IP jako ILB/ELB. To jest opublikowany w systemie DNS, a ruch klientów jest przekazywana do podstawowego serwera SQL repliki ILB/ELB. ILB/ELB wie, która programu SQL Server jest podstawowego, ponieważ używa sond badanie zasobów zawsze na IP. W poprzednim przykładzie go sondy w każdym węźle, który ma punkt końcowy odwołuje się ELB/ILB, zależnie od odpowiada jest podstawowym serwerem SQL.

> [!NOTE]
> ILB i ELB są oba przypisane do konkretnego Azure usługa w chmurze, w związku z tym wszystkie migracja do chmury w Azure najprawdopodobniej oznacza, że zmiany adresu IP usługi równoważenia obciążenia.
>
>

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>Migrowanie zawsze na wdrożenia, które umożliwiają Przestój
Istnieją dwa strategii migracji wdrożeń zawsze włączone, która pozwala na pewien przestój:

1. **Dodaj więcej replikach pomocniczych w istniejącym zawsze na klastrze**
2. **Migracja do nowego zawsze na klastra**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. Dodaj więcej replikach pomocniczych w istniejącym zawsze na klastrze
Jedną ze strategii jest można dodać więcej pomocnicze bazy danych do zawsze włączonej grupy dostępności. Należy dodać do nowej usługi w chmurze i zaktualizuj odbiornika za pomocą nowego adresu IP usługi równoważenia obciążenia.

##### <a name="points-of-downtime"></a>Punkty przestój:
* Sprawdzanie poprawności klastra.
* Test trybu failover zawsze na nowe pomocnicze bazy danych.

Jeśli używane są pule magazynu systemu Windows w ramach maszyny Wirtualnej dla wyższej przepustowości we/wy, następnie te są przełączony w tryb offline podczas pełnej weryfikacji klastra. Test weryfikacji jest wymagana podczas dodawania węzłów do klastra. Czas wymagany do uruchomienia testu może się różnić, to należy przetestować w środowisku testowym reprezentatywny, aby uzyskać jak długo trwa to przybliżony czas.

Należy udostępnić czasu, w którym można wykonywać ręcznego przełączania trybu failover i testowania chaos na nowo dodanych węzłach, aby zapewnić funkcji zawsze na wysoką dostępność, zgodnie z oczekiwaniami.

![DeploymentUseAlways On2][7]

> [!NOTE]
> Należy zatrzymać wszystkie wystąpienia programu SQL Server, w której używane są pule magazynu przed uruchomieniem sprawdzania poprawności.
>
> ##### <a name="high-level-steps"></a>Ogólne kroki
>

1. Utwórz dwóch nowych serwerów SQL w nową usługę w chmurze z dołączonym magazynem Premium.
2. Skopiuj przez pełne kopie zapasowe i przywracanie z **NORECOVERY**.
3. Skopiuj za pośrednictwem "poza użytkownik bazy danych" obiekty zależne, takie jak nazwy logowania itp.
4. Utworzenie nowego wewnętrznego obciążenia równoważenia (ILB) lub Użyj zewnętrznego modułu równoważenia obciążenia (ELB), a następnie skonfigurować końcowych równoważenia obciążenia w obu węzłach nowego.

   > [!NOTE]
   > Sprawdź, czy wszystkie węzły mają prawidłowej konfiguracji punktu końcowego, aby kontynuować
   >
   >
5. Zatrzymaj użytkownika/aplikacji dostęp do programu SQL Server (Jeśli używane są pule magazynu).
6. Zatrzymaj usługi aparatu programu SQL Server na wszystkich węzłach (Jeśli używane są pule magazynu).
7. Dodaj nowe węzły do klastra, a pełna weryfikacja została uruchomiona.
8. Po sprawdzania poprawności zakończy się pomyślnie, należy uruchomić wszystkie usługi serwera SQL.
9. Dzienniki transakcji i przywracania kopii zapasowych baz danych użytkowników.
10. Dodaj nowe węzły w zawsze włączonej grupy dostępności i umieścić replikacji do **synchroniczne**.
11. Dodaj zasób adresu IP z nowego chmury usługi ILB/ELB za pomocą programu PowerShell dla zawsze na podstawie na przykład obejmujący wiele lokacji w [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage). W klastrowania systemu Windows, należy ustawić **możliwych właścicieli** z **adres IP** zasobów do nowych węzłów stary. Zobacz sekcję "Dodawanie zasobu adresu IP w tej samej podsieci" [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
12. Tryb failover na jedną z nowych węzłów.
13. Wprowadź nowe węzły automatycznej pracy awaryjnej partnerów i testu pracy w trybie Failover.
14. Usuń węzły oryginalnego z grupy dostępności.

##### <a name="advantages"></a>Zalety
* Nowe serwery SQL mogą być testowane (SQL Server i aplikacji), zanim zostaną one dodane do zawsze włączony.
* Można zmienić rozmiar maszyny Wirtualnej i dostosować magazyn, aby dokładnie wymagań. Jednakże może być korzystne zachować wszystkie ścieżki pliku SQL bez zmian.
* Można kontrolować przy uruchamianiu transfer kopii zapasowych bazy danych na replikach pomocniczych. Ta różni się od przy użyciu usługi Azure **Start AzureStorageBlobCopy** polecenia, aby skopiować wirtualne dyski twarde, ponieważ jest to kopia asynchronicznego.

##### <a name="disadvantages"></a>Wady
* Gdy używane są pule magazynu systemu Windows, brak przestoju klastra podczas pełnej weryfikacji klastra dla nowych dodatkowych węzłów.
* W zależności od wersji programu SQL Server oraz liczba istniejących replik pomocniczych nie można dodać więcej replikach pomocniczych bez usuwania istniejących replik pomocniczych.
* Może być długi czas transferu danych SQL podczas konfigurowania pomocniczych baz danych.
* Brak dodatkowych kosztów podczas migracji, gdy zostały uruchomione równolegle nowej maszyny.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. Migracja do nowego zawsze na klastra
Kolejną strategią jest utworzenie nowego zawsze na klastrze z całkiem nowe węzły w nową usługę w chmurze i przekierowanie klientów z niego korzystać.

##### <a name="points-of-downtime"></a>Punkty przestoju
Brak przestoju podczas przenoszenia aplikacji i użytkowników do nowego odbiornika zawsze włączone. Przestój jest zależna od:

* Czas potrzebny do przywrócenia kopii zapasowej dziennika transakcji końcowej do baz danych na nowe serwery.
* Czas potrzebny na aktualizowanie aplikacji klienckich do użycia nowego odbiornika zawsze włączone.

##### <a name="advantages"></a>Zalety
* Można przetestować środowiska rzeczywistej produkcji, SQL Server, a zmiany kompilacji systemu operacyjnego.
* Istnieje możliwość dostosowywania magazynu i zmniejszenie rozmiaru maszyny wirtualnej. Może to spowodować zmniejszenie kosztów.
* W trakcie tego procesu można aktualizacji kompilacji programu SQL Server lub wersji. Możesz również uaktualnić System operacyjny.
* Poprzednie zawsze na klastrze może działać jako docelowy stałe wycofywania.

##### <a name="disadvantages"></a>Wady
* Należy zmienić nazwę DNS odbiornika obu zawsze na klastrach uruchomione jednocześnie. Dodaje administracji obciążenie podczas migracji, ponieważ ciągi aplikacji klienta musi odzwierciedlać nową nazwę odbiornika.
* Musisz zaimplementować mechanizm synchronizacji między dwoma środowiskami, aby były jak można zminimalizować wymagania wykonana końcowa synchronizacja przed migracją.
* Zostanie dodany koszt podczas migracji ma nowego środowiska uruchamiania.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>Migrowanie zawsze na wdrożeń minimalizująca przestoje
Minimalizująca przestoje istnieją dwa strategii migracji wdrożeń zawsze włączone:

1. **Korzystanie z istniejących pomocniczy: pojedynczej lokacji**
2. **Korzystanie z istniejących dodatkowej Replica(s): obejmujący wiele lokacji**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. Korzystanie z istniejących pomocniczy: pojedynczej lokacji
Minimalizująca przestoje jedną ze strategii jest podjąć istniejącej chmurze dodatkowej i usunąć go z bieżącej usługi w chmurze. Następnie skopiuj wirtualne dyski twarde do nowego konta magazynu Premium i tworzenie maszyny Wirtualnej w nowej usługi w chmurze. Następnie zaktualizuj odbiornika w klaster i pracy awaryjnej.

##### <a name="points-of-downtime"></a>Punkty przestoju
* Brak przestoju podczas aktualizowania ostatni węzeł z punktem końcowym o zrównoważonym obciążeniu.
* Ponowne połączenie z klienta może być opóźnione w zależności od konfiguracji klienta/serwera DNS.
* Jeśli użytkownik chce wykonać grupy zawsze na klastra w tryb offline, aby wymienić adresów IP jest dodatkowy Przestój. Można tego uniknąć, przy użyciu zależności lub i możliwych właścicieli zasobu dodany adres IP. Zobacz sekcję "Dodawanie zasobu adresu IP w tej samej podsieci" [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).

> [!NOTE]
> Dodany węzeł, aby partake w jako zawsze na partnerski trybu Failover, należy należy dodać punktu końcowego platformy Azure z odwołaniem do obciążenia zrównoważonym zestawu. Po uruchomieniu **AzureEndpoint Dodaj** polecenia w tym celu, otwórz bieżących połączeń pozostają, ale nowe połączenia odbiornika nie są w stanie ustalić, dopóki usługa równoważenia obciążenia został zaktualizowany. Podczas testowania to było widoczne do 90 ostatnich-120seconds, powinny być testowane.
>
>

##### <a name="advantages"></a>Zalety
* Bez dodatkowych kosztów ponoszonych podczas migracji.
* Jeden do jednego migracji.
* Złożoności.
* Pozwala na zwiększenie IOPS przez jednostki SKU magazynu Premium. Gdy dyski są odłączony od maszyny Wirtualnej i kopiowane do nowej usługi w chmurze firmy 3rd narzędzia można zwiększyć rozmiaru wirtualnego dysku twardego, który zawiera wyższej przepustowości. Aby zwiększyć rozmiar wirtualnego dysku twardego, zobacz [dyskusjach prowadzonych na forum](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows).

##### <a name="disadvantages"></a>Wady
* Podczas migracji jest do tymczasowej utraty wysokiej dostępności i odzyskiwania po awarii.
* Jak jest to migracji 1:1, należy użyć minimalny rozmiar maszyny Wirtualnej, który obsługuje liczby dysków VHD, więc może nie mieć możliwość downsize maszyn wirtualnych.
* W tym scenariuszu użyje Azure **Start AzureStorageBlobCopy** polecenia, które jest asynchroniczne. Na zakończenie kopiowania nie ma żadnych umowy SLA. Czas kopie różni się podczas zależy to od oczekiwania w kolejce, który również jest zależna od ilości danych do przesłania. Podczas kopiowania zwiększa, jeśli mają inną centrum danych Azure, który obsługuje magazyn w warstwie Premium w innym regionie transferu. Jeśli masz tylko węzły 2, należy wziąć pod uwagę możliwe środki zaradcze w przypadku kopiowania trwa dłużej niż w trakcie testów. Może to być poniższe pomysłów.
  * Dodać tymczasowego 3 węzła programu SQL Server dla wysokiej dostępności, przed migracją uzgodnionych przestojów.
  * Uruchom migrację poza Azure zaplanowanej konserwacji.
  * Upewnij się, że zostały prawidłowo skonfigurowane z kworum klastra.  

##### <a name="high-level-steps"></a>Ogólne kroki
Ten dokument nie wykazują pełny przykład pełnego, ale [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) zawiera szczegółowe informacje, których można użyć, które należy wykonać.

![MinimalDowntime][8]

* Zbierz informacje konfiguracji dysku, a następnie usuń węzeł (nie należy usuwać dołączonych dysków VHD).
* Utwórz konto magazynu Premium i skopiuj wirtualne dyski twarde z konta magazynu w warstwie standardowa
* Utwórz nową usługę w chmurze i wdrożenie SQL2 maszyny Wirtualnej w tej usłudze w chmurze. Tworzenie maszyny Wirtualnej za pomocą skopiowanego dysku VHD oryginalnego systemu operacyjnego i dołączania skopiowane wirtualne dyski twarde.
* Skonfiguruj ILB / ELB i dodać punkty końcowe.
* Aktualizacja odbiornika przez:
  * Przełączania w tryb offline zawsze w grupie i aktualizowanie zawsze na odbiornika za pomocą nowego ILB / adresu ELB IP.
  * Lub dodawanie zasobu adresu IP z nowego chmury usługi ILB/ELB za pomocą programu PowerShell do klastra systemu Windows. Następnie ustawić możliwych właścicieli zasobu adresu IP do migrowanych węzła SQL2 i Ustaw jako zależność lub w nazwie sieci. Zobacz sekcję "Dodawanie zasobu adresu IP w tej samej podsieci" [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
* Sprawdzanie konfiguracji DNS/propagowania do klientów.
* Migrowanie maszyny Wirtualnej SQL1 i kroków 2 – 4.
* Jeśli przy użyciu kroków 5ii, Dodaj komputera SQL1 jako możliwego właściciela zasobu dodany adres IP
* Testy trybu failover.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. Korzystanie z istniejących dodatkowej replica(s): obejmujący wiele lokacji
Jeśli niektóre węzły w więcej niż jednego centrum danych Azure (DC) lub w przypadku środowiska hybrydowego, następnie służy konfiguracji zawsze włączone w tym środowisku w celu zminimalizowania przestojów.

Podejście jest Zmień zawsze włączony synchronizacja synchroniczne dla lokalnego lub kontrolera pomocniczego Azure, a następnie w tryb failover przez program SQL Server. Następnie skopiuj wirtualne dyski twarde do konta Premium Storage, a następnie wdrożyć ponownie maszyny do nowej usługi w chmurze. Zaktualizuj odbiornika, a następnie wykonaj powrót po awarii.

##### <a name="points-of-downtime"></a>Punkty przestoju
Czas przestoju składa się z czas do trybu failover do alternatywnego kontrolera domeny i z powrotem. Zależy także od konfiguracji klienta/serwera DNS i ponowne połączenie z klienta mogą być opóźnione.
Rozważmy następujący przykład hybrydowego zawsze w konfiguracji:

![MultiSite1][9]

##### <a name="advantages"></a>Zalety
* Można korzystać z istniejącej infrastruktury.
* Masz możliwość przed uaktualnieniem najpierw magazynu Azure na kontrolerze domeny Azure odzyskiwania po awarii.
* Można tak skonfigurować Magazyn DR Azure kontrolera domeny.
* Brak co najmniej dwóch przechodzenia w tryb failover podczas migracji, wyłączając testowy tryb failover.
* Nie należy do przenoszenia danych serwera SQL z kopii zapasowej i przywracania.

##### <a name="disadvantages"></a>Wady
* W zależności od klienta dostępu do programu SQL Server może być większe opóźnienia serwer SQL jest uruchomiony w alternatywnej kontrolera domeny do aplikacji.
* Podczas kopiowania dysków VHD na magazyn w warstwie Premium może trwać długo. Może to mieć wpływ na decyzję od tego, czy zachować węzeł w grupie dostępności. Należy wziąć pod uwagę to kiedy dziennika pracy intensywnych obciążeń uruchomionych podczas migracji jest wymagane, ponieważ węzeł podstawowy ma Niezreplikowane transakcje są przechowywane w jego dzienniku transakcji. W związku z tym to można powiększać znacznie.
* W tym scenariuszu użyje Azure **Start AzureStorageBlobCopy** polecenia, które jest asynchroniczne. Na zakończenie nie ma żadnych umowy dotyczącej poziomu usług. Czas kopie różni się podczas zależy to od oczekiwania w kolejce, również są zależne od ilości danych do przesłania. W związku z tym istnieje tylko jeden węzeł 2 centrum danych, należy podjąć kroki zaradcze w przypadku kopiowania trwa dłużej niż w trakcie testów. Te kroki zaradcze obejmują poniższe pomysłów:
  * Dodaj węzeł tymczasowy SQL 2 dla HA przed migracją uzgodnionych przestojów.
  * Uruchom migrację poza Azure zaplanowanej konserwacji.
  * Upewnij się, że zostały prawidłowo skonfigurowane z kworum klastra.

W tym scenariuszu założono, że udokumentowanych instalacji i wiedzieć odwzorowania magazynu w celu zmiany ustawień pamięci podręcznej dysku optymalne.

##### <a name="high-level-steps"></a>Ogólne kroki
![Multisite2][10]

* Upewnij lokalną / alternatywny Azure DC podstawowego serwera SQL oraz jej innych automatycznej pracy awaryjnej partnera (AFP).
* Zbierz informacje o konfiguracji dysków z SQL2, a następnie usuń węzeł (nie należy usuwać dołączonych dysków VHD).
* Utwórz konta Premium Storage i skopiuj wirtualne dyski twarde z konta magazynu w warstwie standardowa.
* Utwórz nową usługę w chmurze a SQL2 maszyny Wirtualnej z jej dołączone dyski magazynu dodatków.
* Skonfiguruj ILB / ELB i dodać punkty końcowe.
* Zaktualizuj zawsze na odbiornika za pomocą nowego ILB / ELB IP adres i testowania trybu failover.
* Sprawdź konfigurację systemu DNS.
* Zmień AFP SQL2, migracja SQL1 i przejść przez kroki od 2 do 5.
* Testy trybu failover.
* Przełącza AFP z powrotem do komputera SQL1 i SQL2

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>Dodatek: Migrowanie wdrożenia w wielu lokacjach zawsze w klastrze do magazyn w warstwie Premium
W dalszej części tego artykułu udostępnia szczegółowy przykład konwersji obejmujący wiele lokacji zawsze na klaster magazyn w warstwie Premium. Ponadto konwertuje odbiornika z za pomocą zewnętrznej usługi równoważenia obciążenia (ELB) do wewnętrznego modułu równoważenia obciążenia (ILB).

### <a name="environment"></a>Środowisko
* Windows 2k12 / SQL 2k12
* Pliki 1 DB na SP
* 2 x pule magazynu w każdym węźle

![Appendix1][11]

### <a name="vm"></a>VM:
W tym przykładzie zamierzamy pokazują przenoszenie z ELB do ILB. ELB nie była dostępna przed ILB, dlatego oznacza to, jak przejść do ILB podczas migracji.

![Appendix2][12]

### <a name="pre-steps-connect-to-subscription"></a>Kroki przed: Łączenie się z subskrypcją
    Add-AzureAccount

    #Set up subscription
    Get-AzureSubscription

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>Krok 1: Utwórz nowe konto magazynu i usługa w chmurze
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

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>Krok 2: Zwiększ dozwolonych błędów dla zasobów <Optional>
W niektórych zasobów, które należą do sieci zawsze włączonej grupy dostępności, istnieją ograniczenia na liczbę błędów, które mogą wystąpić w okresie, w którym usługa klastrowania spróbuje ponownie uruchomić grupy zasobów. Zaleca się, że możesz zwiększyć to o ile można są Instruktaż tej procedury, ponieważ w przeciwnym razie ręcznie trybu failover i wyzwalacza przechodzenia w tryb failover przez zamknięcie maszyny, które można uzyskać w pobliżu tego limitu.

Byłoby rozsądne podwojenia dodatku awarii, w tym celu w Menedżerze klastra trybu Failover, przejdź do właściwości grupy zasobów zawsze włączone:

![Appendix3][13]

Maksymalna liczba awarii należy zmienić wartość 6.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>Krok 3: Dodawanie adresu IP zasobu dla grupy klastra <Optional>
Jeśli masz tylko jeden adres IP dla grupy klastra i to jest wyrównany do podsieci chmury, należy pamiętać, jeśli przypadkowo w tryb offline wszystkich węzłów klastra w chmurze w tej sieci, a następnie zasobu adres IP klastra i nazwa sieciowa klastra są nie można przejść w tryb online. W takiej sytuacji nie dopuszcza aktualizacji do innych zasobów klastra.

#### <a name="step-4-dns-configuration"></a>Krok 4: Konfiguracja DNS
Implementowanie przejścia zależy od tego, jak DNS są wykorzystywane i zaktualizować.
Zawsze włączone jest zainstalowany, tworzy grupy zasobów klastra z systemem Windows, po otwarciu Menedżera klastra trybu Failover, zobaczysz, że co najmniej go trzy zasoby, które dokument odwołuje się do są:

* Nazwy sieci wirtualnej (VNN) — nazwa DNS klienci łączą się, gdy chcą nawiązać połączenia z serwerami programu SQL Server za pośrednictwem zawsze włączone.
* Zasób adres IP — adres IP którą skojarzona VNN, może mieć więcej niż jeden i w wielu lokacjach konfiguracji ma adres IP według podsieci/lokacji.

Podczas łączenia z programem SQL Server, SQL Server Client sterownik pobiera skojarzonych z odbiornika rekordów DNS i próbuje podłączyć się do każdego zawsze włączone skojarzony adres IP. Następnie omówiono pewne czynniki, które wpływają na to.

Liczba równoczesnych rekordy DNS, które są skojarzone z nazwą odbiornika zależy nie tylko w liczbę adresów IP skojarzonych, ale "RegisterAllIpProviders'setting w klastrze trybu Failover zawsze ON VNN zasobu.

Podczas wdrażania zawsze na platformie Azure istnieją inne czynności, aby utworzyć odbiornik i adresy IP, należy ręcznie skonfigurować RegisterAllIpProviders 1, ta zmienna różni do wdrożenia lokalnego zawsze na którym jest już ustawiona na 1.

Jeśli "RegisterAllIpProviders" ma wartość 0, a następnie zostanie wyświetlony tylko jeden rekord DNS w systemie DNS skojarzone z odbiornika:

![Appendix4][14]

Jeśli 'RegisterAllIpProviders' to 1:

![Appendix5][15]

Poniższy kod zrzuty limit ustawienia VNN i ustawia go dla Ciebie. Aby zmiana zaczęła obowiązywać należy VNN Przełącz do trybu offline i włączyć ją ponownie do trybu online. Trwa to odbiornika w trybie offline powoduje zakłócenia łączności klienta.

    ##Always On Listener Name
    $ListenerName = "Mylistener"
    ##Get AlwaysOn Network Name Settings
    Get-ClusterResource $ListenerName| Get-ClusterParameter
    ##Set RegisterAllProvidersIP
    Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1

W kolejnym kroku migracji należy zaktualizować odbiornika zawsze włączonej z aktualizacja adresu IP, który odwołuje się do modułu równoważenia obciążenia, obejmuje to usunięcie zasobu adresu IP i dodawania. Po zakończeniu aktualizowania adresu IP należy się upewnić, nowy adres IP został zaktualizowany w strefie DNS i klientów aktualizowania ich lokalnej pamięci podręcznej DNS.

Jeśli klienci znajdują się w segmencie inną sieć i referencyjne z innego serwera DNS, należy wziąć pod uwagę wpływ o Transfer strefy DNS podczas migracji, jak ponownie połączyć aplikację czas jest ograniczone przez czas transferu strefy dowolnego nowego adresu IP adresy dla odbiornika. Podlegają ograniczenia czasu, w tym miejscu, należy omówienia i przetestować wymuszania przyrostowy transfer strefy z zespołów systemu Windows, a również wprowadzić rekord hosta DNS do dolnej czasu wygaśnięcia (TTL), więc klienci aktualizacji. Aby uzyskać więcej informacji, zobacz [przyrostowych transferów stref](https://technet.microsoft.com/library/cc958973.aspx) i [Start DnsServerZoneTransfer](https://technet.microsoft.com/library/jj649917.aspx).

Domyślny czas TTL rekord DNS, który jest skojarzony z odbiornika zawsze na platformie Azure to 1200 sekund. Możesz zmniejszyć, to jeśli podlegają czas ograniczenia podczas migracji, aby zapewnić klientom zaktualizować ich DNS przy użyciu zaktualizowanych adresu IP dla odbiornika. Możesz wyświetlić i zmodyfikować konfigurację przez zrzucanie limit konfiguracji VNN:

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"
    #Look at HostRecordTTL
    Get-ClusterResource $ListenerName| Get-ClusterParameter

    #Set HostRecordTTL Examples
    Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120

> [!NOTE]
> Im niższa "HostRecordTTL" wyższy stopień ruch DNS występuje.

##### <a name="client-application-settings"></a>Ustawienia aplikacji klienta
Jeśli aplikacja kliencka SQL obsługuje programu .net 4.5 SQLClient, możesz użyć "MULTISUBNETFAILOVER = TRUE" — słowo kluczowe. Powinny być stosowane to słowo kluczowe, ponieważ umożliwia szybsze połączenia SQL zawsze włączonej grupy dostępności podczas pracy awaryjnej. Wylicza wszystkie adresy IP skojarzone z odbiornika zawsze włączonej równolegle i wykonuje skuteczniejsze szybkość ponów próbę połączenia TCP w trybie failover.

Aby uzyskać więcej informacji na temat poprzednich ustawień, zobacz [MultiSubnetFailover — słowo kluczowe i skojarzonych z funkcji](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover). Zobacz też [SqlClient obsługę wysokiej dostępności, odzyskiwania po awarii](https://msdn.microsoft.com/library/hh205662\(v=vs.110\).aspx).

#### <a name="step-5-cluster-quorum-settings"></a>Krok 5: Ustawienia kworum klastra
Jak mają być pobieranie co najmniej jeden serwer SQL nie działa w czasie, należy zmodyfikować konfigurację kworum klastra, jeśli używa monitora udziału plików (FSW) z dwoma węzłami, należy ustawić kworum, aby umożliwić Większość węzłów i korzystać z dynamicznych głosu , co pozwala na jednym węźle pozostaje stałego.

    Set-ClusterQuorum -NodeMajority  

Aby uzyskać więcej informacji dotyczących zarządzania oraz konfigurowania kworum klastra, zobacz [Konfigurowanie i zarządzanie kworum w klastrze pracy awaryjnej systemu Windows Server 2012](https://technet.microsoft.com/library/jj612870.aspx).

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>Krok 6: Wyodrębnianie istniejące punkty końcowe i listy kontroli dostępu
    #GET Endpoint info
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
    #GET ACL Rules for Each EP, this example is for the Always On Endpoint
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  

Zapisz ten tekst do pliku.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>Krok 7: Zmień partnerskich trybu Failover i tryby replikacji
Jeśli masz więcej niż dwa serwery SQL, należy zmienić pracy awaryjnej, innym pomocniczej w innym kontrolera domeny lub lokalnego "Synchroniczny" i stał się automatyczne partnera pracy awaryjnej (AFP), jest więc zachowują HA podczas wprowadzania zmian. Można to zrobić przy użyciu języka TSQL z jednak modyfikować SSMS:

![Appendix6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>Krok 8: Usuń dodatkowej maszyny Wirtualnej z usługi w chmurze
Należy się przygotować najpierw migracji węzła pomocniczego chmury. Jeśli ten węzeł jest obecnie podstawową, należy zainicjować ręcznego przełączania trybu failover.

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

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>Krok 9: Zmień ustawienia w pliku CSV pamięci podręcznej dysku i Zapisz
Woluminy danych tych powinna być równa tylko do odczytu.

Woluminy TLOG te należy wybrać opcję Brak.

![Appendix7][17]

#### <a name="step-10-copy-vhds"></a>Krok 10: Kopiowanie dysków VHD
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



Można sprawdzić stan kopiowania dysków VHD do konta Premium Storage:

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

![Appendix8][18]

Poczekaj, aż wszystkie one są rejestrowane jako Powodzenie.

Aby uzyskać informacje dotyczące poszczególnych obiektów blob:

    Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext

#### <a name="step-11-register-os-disk"></a>Krok 11: Dysk systemu operacyjnego rejestru
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

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>Krok 12: Zaimportuj dodatkowej do nowej usługi w chmurze
Poniższy kod również używa opcji dodane w tym miejscu można zaimportować maszyny i używać retainable adresu VIP.

    #Build VM Config
    $ipaddr = "192.168.0.5"
    #Remember to change to XIO
    $newInstanceSize = "Standard_DS13"
    $subnet = "SQL"

    #Create new Avaiability Set
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

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>Krok 13: Tworzenie ILB na nowe usługi chmury, Dodaj obciążenia zrównoważonym punktów końcowych i listy kontroli dostępu
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

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

    ####WAIT FOR FULL AlwaysOn RESYNCRONISATION!!!!!!!!!#####

#### <a name="step-14-update-always-on"></a>Krok 14: Zawsze aktualizacji
    #Code to be executed on a Cluster Node
    $ClusterNetworkNameAmsterdam = "Cluster Network 2" # the azure cluster subnet network name
    $newCloudServiceIPAmsterdam = "192.168.0.25" # IP address of your cloud service

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"


    Add-ClusterResource "IP Address $newCloudServiceIPAmsterdam" -ResourceType "IP Address" -Group $AGName -ErrorAction Stop |  Set-ClusterParameter -Multiple @{"Address"="$newCloudServiceIPAmsterdam";"ProbePort"="59999";SubnetMask="255.255.255.255";"Network"=$ClusterNetworkNameAmsterdam;"OverrideAddressMatch"=1;"EnableDhcp"=0} -ErrorAction Stop

    #set dependancy and NETBIOS, then remove old IP address

    #set NETBIOS, then remove old IP address
    Get-ClusterGroup $AGName | Get-ClusterResource -Name "IP Address $newCloudServiceIPAmsterdam" | Set-ClusterParameter -Name EnableNetBIOS -Value 0

    #set dependency to Listener (OR Dependency) and delete previous IP Address resource that references:

    #Make sure no static records in DNS

![Appendix9][19]

Teraz Usuń stare usługi w chmurze adresu IP.

![Appendix10][20]

#### <a name="step-15-dns-update-check"></a>Krok 15: Sprawdzanie aktualizacji DNS
Teraz należy sprawdzić serwery DNS w sieci klienta programu SQL Server i upewnij się, że klaster został dodany rekord hosta dodatkowe dodany adres IP. Jeśli te serwery DNS nie zostały zaktualizowane, należy wziąć pod uwagę wymuszania transferu strefy DNS i upewnij się, że klienci w podsieci są w stanie rozpoznać zawsze na adresy IP, jest więc nie trzeba czekać na automatyczne replikacja DNS.

#### <a name="step-16-reconfigure-always-on"></a>Krok 16: Skonfiguruj ponownie zawsze na
W tym momencie poczekać pomocniczej tego węzła, które zostały poddane migracji do pełni ponownego zsynchronizowania jej z węzła lokalnego i przejdź do węzła synchronicznej replikacji i stał się AFP.  

#### <a name="step-17-migrate-second-node"></a>Kroku 17: Drugi węzeł migracji
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

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>Krok 18: Zmień ustawienia w pliku CSV pamięci podręcznej dysku i Zapisz
Woluminy danych ustawienia pamięci podręcznej powinna być równa tylko do odczytu.

W przypadku woluminów TLOG ustawień pamięci podręcznej należy wybrać opcję Brak.

![Appendix11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>Krok 19: Utwórz nowe konto magazynu niezależnie od przypadku węzła pomocniczego
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

#### <a name="step-20-copy-vhds"></a>Krok 20: Kopiowanie dysków VHD
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

    #check induvidual blob status
    Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContext


Można sprawdzić stan kopiowania wirtualnego dysku twardego dla wszystkich dysków VHD: ForEach ($disk w $diskobjects) {$lun = $disk. Jednostka LUN $vhdname = $disk.vhdname $cacheoption = $disk. HostCaching $disklabel = $disk. Etykieta_dysku $diskName = $disk. DiskName

       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContextnode2
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
       }

![Appendix12][22]

Poczekaj, aż wszystkie one są rejestrowane jako Powodzenie.

Aby uzyskać informacje dotyczące poszczególnych obiektów blob:

    #Check induvidual blob status
    Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContextnode2

#### <a name="step-21-register-os-disk"></a>Krok 21: Dysk rejestru systemu operacyjnego
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

    #Join to existing Avaiability Set

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

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>Krok 22: Dodawanie obciążenia zrównoważonym punktów końcowych i listy kontroli dostępu
    #Endpoints
    $epname="sqlIntEP"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM


    #STOP!!! CHECK in the Azure portal or Machine Endpoints through PowerShell that these Endpoints are created!

    #SET ACLs or Azure Network Security Groups & Windows FWs

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

#### <a name="step-23-test-failover"></a>Krok 23: Test trybu failover
Poczekaj na węźle zmigrowane do synchronizacji z lokalnymi zawsze na węzeł. Umieszczenie go w trybie synchronicznym replikacji i poczekaj, aż został zsynchronizowany. Następnie w tryb failover z lokalnego w pierwszym węźle migracji, która jest AFP. Po którym pracuje, zmienić ostatni węzeł zmigrowane do AFP.

Należy testy trybu failover pomiędzy wszystkimi węzłami i uruchamiania, ale oczekiwano chaos testy, aby zapewnić działanie trybu failover jako i w odpowiednim manor.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>Krok 24: Odłożyć ustawienia kworum klastra / czas wygaśnięcia DNS / Pntrs pracy awaryjnej / ustawienia synchronizacji
##### <a name="adding-ip-address-resource-on-same-subnet"></a>Dodawanie zasobu adresu IP na tej samej podsieci
Jeśli istnieją tylko dwa serwery SQL i chcesz przeprowadzić ich migrację do nowej usługi w chmurze, ale chce zachować je w tej samej podsieci, można uniknąć, biorąc odbiornika w trybie offline, aby usunąć oryginalny zawsze na adres IP i dodać nowy adres IP. W przypadku migracji maszyn wirtualnych do innej podsieci, nie należy to zrobić, ponieważ ma sieci dodatkowe klastra, który odwołuje się do tej podsieci.

Po włączane migrowanych pomocniczej i dodać nowy adres IP zasób dla nowej usługi w chmurze przed trybu failover istniejącą główną należy wykonać następujące czynności w Menedżerze klastra trybu Failover:

Aby dodać w polu adres IP, zobacz [dodatku](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage), krok 14.

1. Dla bieżącego zasobu adresu IP Zmień możliwych właścicieli "Istniejące podstawowy serwer SQL", w tym przykładzie "dansqlams4":

    ![Appendix13][23]
2. Dla nowego zasobu adresu IP Zmień możliwych właścicieli do"zmigrowane pomocniczym programu SQL Server", w tym przykładzie "dansqlams5":

    ![Appendix14][24]
3. Po ustawieniu to można trybu failover, a podczas migracji jest ostatni węzeł możliwych właścicieli można edytować, że węzeł zostanie dodany jako możliwego właściciela:

    ![Appendix15][25]

## <a name="additional-resources"></a>Zasoby dodatkowe
* [Azure Premium Storage](../premium-storage.md)
* [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/)
* [SQL Server na maszynach wirtualnych Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

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
