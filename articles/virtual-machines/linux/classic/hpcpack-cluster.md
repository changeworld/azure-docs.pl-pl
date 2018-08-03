---
title: Maszyny wirtualne wystąpienie obliczeniowe systemu Linux w klastrze pakietu HPC Pack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzenie i używanie klastra pakietu HPC Pack na platformie Azure dla systemu Linux o wysokiej wydajności (HPC) obciążeń obliczeniowych
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: 4d080fdd-5ffe-4f54-a78d-4c818f6eb3fb
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 10/12/2016
ms.author: danlep
ms.openlocfilehash: 2d4091d8ad6a778405ee6bb916c399e0b144f21d
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39441531"
---
# <a name="get-started-with-linux-compute-nodes-in-an-hpc-pack-cluster-in-azure"></a>Rozpoczynanie pracy z węzłami obliczeniowymi systemu Linux w klastrze pakietu HPC Pack na platformie Azure
Konfigurowanie [pakietu Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029.aspx) klastra na platformie Azure, zawierający głównego węzła z systemem Windows Server i kilka obliczeń węzłów z systemem obsługiwanych dystrybucji systemu Linux. Poznaj opcje przenoszenia danych między węzłami systemu Linux i Windows węzłem klastra. Dowiedz się, jak przesyłanie zadań HPC systemu Linux w klastrze.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

Na wysokim poziomie na poniższym diagramie przedstawiono klastra pakietu HPC Pack, tworzenie i Praca z.

![Klastra pakietu HPC Pack w przypadku węzłów systemu Linux][scenario]

Aby uzyskać inne opcje uruchamiania obciążeń HPC w systemie Linux na platformie Azure, zobacz [zasoby techniczne usługi batch i obliczeń o wysokiej wydajności](../../../batch/big-compute-resources.md).

## <a name="deploy-an-hpc-pack-cluster-with-linux-compute-nodes"></a>Wdrażanie klastra pakietu HPC Pack za pomocą węzłów obliczeniowych systemu Linux
W tym artykule przedstawiono dwie opcje do wdrożenia klastra pakietu HPC Pack na platformie Azure z systemem Linux możesz węzłów obliczeniowych. Obie metody umożliwia obrazu z witryny Marketplace systemu Windows Server za pomocą pakietu HPC Pack Tworzenie węzła głównego. 

* **Szablon usługi Azure Resource Manager** — Użyj szablonu z portalu Azure Marketplace lub szablonu szybkiego startu przez społeczność do zautomatyzowania tworzenia klastra w modelu wdrażania usługi Resource Manager. Na przykład [klastra pakietu HPC Pack dla obciążeń systemu Linux](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) szablon w witrynie Azure Marketplace umożliwia utworzenie całej infrastruktury klastra HPC Pack dla systemu Linux HPC obciążeń.
* **Skrypt programu PowerShell** — użyj [skryptem wdrażania IaaS pakietu Microsoft HPC Pack](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) (**New HpcIaaSCluster.ps1**) do automatycznego wdrożenia całego klastra, w klasycznym modelu wdrażania. Ten skrypt programu Azure PowerShell używa obrazu maszyny Wirtualnej pakietu HPC Pack w witrynie Azure Marketplace, do szybkiego wdrożenia i zapewnia kompleksowy zestaw parametrów konfiguracji do wdrożenia węzłów obliczeniowych systemu Linux.

Aby uzyskać więcej informacji o opcjach wdrażania klastra pakietu HPC Pack na platformie Azure, zobacz [funkcje umożliwiające tworzenie i zarządzanie nimi obliczeń o wysokiej wydajności (HPC) klastra na platformie Azure przy użyciu pakietu Microsoft HPC Pack](../hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="prerequisites"></a>Wymagania wstępne
* **Subskrypcja platformy Azure** — można użyć subskrypcji w usłudze Azure Global lub chińska wersja platformy Azure. Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.
* **Limit przydziału rdzeni** — może być konieczne zwiększenie limitu przydziału rdzeni, zwłaszcza, jeśli zdecydujesz się wdrożyć kilka węzłów klastra z wyspecjalizowanymi rozmiarów maszyn wirtualnych. Aby zwiększyć limit przydziału, otwórz żądanie obsługi klienta online bez dodatkowych opłat.
* **Dystrybucje systemu Linux** — obecnie pakietu HPC Pack obsługuje poniższe dystrybucje systemu Linux dla węzłów obliczeniowych. Możesz korzystać z tych dystrybucji przy użyciu wersji portalu Marketplace, gdzie są dostępne lub podać własne.
  
  * **Opartych na systemie centOS**: 6.5, 6.6, 6.7, 7.0, 7.1, 7.2, 6.5 HPC, 7.1 HPC
  * **Red Hat Enterprise Linux**: 6.7, 6.8, 7.2
  * **SUSE Linux Enterprise Server**: SLES 12, SLES 12 (Premium), SLES 12 z dodatkiem SP1, SLES 12 z dodatkiem SP1 (Premium), z systemem SLES 12 for HPC, SLES 12 dla HPC (Premium)
  * **Ubuntu Server**: 14.04 LTS, 16.04 LTS
    
    > [!TIP]
    > Aby użyć sieci RDMA na platformie Azure przy użyciu jednego z obsługą dostępu RDMA rozmiarach, należy określić obraz z systemem SUSE Linux Enterprise Server 12 HPC lub opartych na systemie CentOS HPC w portalu Azure Marketplace. Aby uzyskać więcej informacji, zobacz [o wysokiej wydajności obliczeń rozmiarów maszyn wirtualnych](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    > 

Dodatkowe wymagania wstępne dotyczące wdrażania klastra za pomocą skryptu wdrażania IaaS pakietu HPC Pack:

* **Komputer kliencki** — należy komputera klienckiego z systemem Windows do uruchamiania skryptu wdrażania klastra.
* **Program Azure PowerShell** - [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview) (wersja 0.8.10 lub nowszej) na komputerze klienckim.
* **Skryptem wdrażania IaaS pakietu HPC Pack** — pobieranie i rozpakowywanie najnowszej wersji skryptu [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). Wersja skryptu można sprawdzić, uruchamiając `.\New-HPCIaaSCluster.ps1 –Version`. Ten artykuł jest oparty na wersji 4.4.1 lub nowszej skryptu.

### <a name="deployment-option-1-use-a-resource-manager-template"></a>Opcja wdrażania 1. Korzystanie z szablonu usługi Resource Manager
1. Przejdź do [klastra pakietu HPC Pack dla obciążeń systemu Linux](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) szablonu w portalu Azure Marketplace, a następnie kliknij przycisk **Wdróż**.
1. W witrynie Azure portal, zapoznaj się z informacjami, a następnie kliknij przycisk **Utwórz**.
   
    ![Tworzenie portalu][portal]
1. Na **podstawy** bloku, wprowadź nazwę klastra, który także nazwy węzła głównego maszyny Wirtualnej. Można wybrać istniejącą grupę zasobów lub Utwórz grupę wdrożenia w lokalizacji, która jest dostępna dla Ciebie. Lokalizacja ma wpływ na dostępność określonych rozmiarów maszyn wirtualnych i innych usług platformy Azure (zobacz [dostępność produktów według regionów](https://azure.microsoft.com/regions/services/)).
1. Na **głównymi ustawienia węzła** bloku pierwszego wdrożenia, zazwyczaj można zaakceptować ustawienia domyślne. 
   
   > [!NOTE]
   > **Adres URL skryptu pokonfiguracyjnego** jest ustawienie opcjonalne, aby określić publicznie dostępny skrypt programu Windows PowerShell, który chcesz uruchomić w węźle głównym maszyny Wirtualnej po jej uruchomieniu. 
   > 
   > 
1. Na **ustawienia węzła obliczeniowego** bloku wybierz wzorca nazewnictwa dla węzłów, liczbę i rozmiar węzłów i dystrybucji systemu Linux do wdrożenia.
1. Na **ustawień infrastruktury** bloku, wprowadź nazwy sieci wirtualnej i usługi Active Directory domeny, domeny i poświadczenia administratora maszyny Wirtualnej i wzorca nazewnictwa dla kont magazynu.
   
   > [!NOTE]
   > Pakiet HPC Pack używa domeny usługi Active Directory do uwierzytelniania użytkowników klastra. 
   > 
   > 
1. Po uruchomieniu testów sprawdzania poprawności i przejrzyj warunki użytkowania, kliknij przycisk **zakupu**.

### <a name="deployment-option-2-use-the-iaas-deployment-script"></a>Opcja wdrażania 2. Użyj skryptem wdrażania IaaS pakietu
Poniżej przedstawiono dodatkowe wymagania wstępne dotyczące wdrażania klastra za pomocą skryptu wdrażania IaaS pakietu HPC Pack:

* **Komputer kliencki** — należy komputera klienckiego z systemem Windows do uruchamiania skryptu wdrażania klastra.
* **Program Azure PowerShell** - [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview) (wersja 0.8.10 lub nowszej) na komputerze klienckim.
* **Skryptem wdrażania IaaS pakietu HPC Pack** — pobieranie i rozpakowywanie najnowszej wersji skryptu [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). Wersja skryptu można sprawdzić, uruchamiając `.\New-HPCIaaSCluster.ps1 –Version`. Ten artykuł jest oparty na wersji 4.4.1 lub nowszej skryptu.

**Plik konfiguracyjny XML**

Skryptem wdrażania IaaS pakietu HPC Pack używa plik konfiguracyjny XML jako dane wejściowe do opisu klastra HPC. Przykładowy plik konfiguracji określa małego składające się z węzłem głównym pakietu HPC Pack i dwóch węzłów obliczeniowych systemu Linux 7.0 CentOS A7 rozmiaru klastra. 

Zmodyfikuj plik, zgodnie z potrzebami dla danego środowiska i konfiguracji klastra żądaną i zapisz go przy użyciu nazwy, takie jak HPCDemoConfig.xml. Na przykład należy podać nazwę subskrypcji i unikatowej nazwy konta magazynu i nazwa usługi w chmurze. Ponadto można wybrać inny obraz systemu Linux obsługiwanych dla węzłów obliczeniowych. Aby uzyskać więcej informacji na temat elementów w pliku konfiguracji, zobacz plik Manual.rtf w folderze skryptu i [Utwórz klaster HPC ze skryptem wdrażania IaaS pakietu HPC Pack](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>allvhdsje</StorageAccount>
  </Subscription>
  <Location>Japan East</Location>  
  <VNet>
    <VNetName>centos7rdmavnetje</VNetName>
    <SubnetName>CentOS7RDMACluster</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS7RDMA-HN</VMName>
    <ServiceName>centos7rdma-je</ServiceName>
  <VMSize>ExtraLarge</VMSize>
  <EnableRESTAPI />
  <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS7RDMA-LN%1%</VMNamePattern>
    <ServiceName>centos7rdma-je</ServiceName>
    <VMSize>A7</VMSize>
    <NodeCount>2</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```

**Aby uruchomić skrypt wdrażania IaaS pakietu HPC Pack**

1. Otwórz program Windows PowerShell na komputerze klienckim jako administrator.
1. Zmień katalog na folder, w którym skrypt jest zainstalowany (E:\IaaSClusterScript w tym przykładzie).
   
    ```powershell
    cd E:\IaaSClusterScript
    ```
1. Uruchom następujące polecenie, aby wdrożyć w klastrze pakietu HPC Pack. W tym przykładzie założono, że plik konfiguracji znajduje się w E:\HPCDemoConfig.xml
   
    ```powershell
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```
   
    a. Ponieważ **AdminPassword** nie została określona w poprzednim poleceniu, zostanie wyświetlony monit o wprowadzenie hasła dla użytkownika *MyAdminName*.
   
    b. Skrypt następnie rozpocznie się sprawdzanie poprawności pliku konfiguracji. Może potrwać kilka minut w zależności od połączenia sieciowego.
   
    ![Walidacja][validate]
   
    c. Po operacji sprawdzania poprawności przekazać, skrypt zawiera listę zasobów klastra, aby utworzyć. Wprowadź *Y* aby kontynuować.
   
    ![Zasoby][resources]
   
    d. Skrypt uruchamia wdrożenie klastra pakietu HPC Pack i zakończeniu konfiguracji bez dalszego wymagane ręczne wykonanie czynności. Skrypt można uruchomić w kilka minut.
   
    ![Wdrażanie][deploy]
   
   > [!NOTE]
   > W tym przykładzie skrypt generuje plik dziennika automatycznie od **- LogFile** parametr nie jest określony. Dzienniki nie są zapisywane w czasie rzeczywistym, ale są zbierane na końcu Weryfikacja i wdrażanie. Jeśli proces programu PowerShell zostanie zatrzymana podczas wykonywania skryptu, wybrane dzienniki zostaną utracone.
   > 
   > 

## <a name="connect-to-the-head-node"></a>Łączenie z węzłem głównym
Po wdrożeniu klastra pakietu HPC Pack na platformie Azure, [połączenia przez pulpit zdalny](../../windows/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) z węzłem głównym maszyny Wirtualnej przy użyciu domeny poświadczeń podana podczas wdrażania klastra (na przykład *hpc\\clusteradmin*). Możesz zarządzać klastra z węzła głównego.

W węźle głównym Uruchom Menedżera klastra HPC, aby sprawdzić stan klastra pakietu HPC Pack. Można zarządzać, i monitor węzłów obliczeniowych systemu Linux, ten sam sposób pracy z Windows węzłów obliczeniowych. Na przykład zobacz węzłów systemu Linux, na liście **zarządzania zasobami** (te węzły są wdrażane przy użyciu **LinuxNode** szablonu).

![Węzeł zarządzania][management]

Zobacz też węzłów systemu Linux w **Mapa cieplna** widoku.

![Mapa cieplna][heatmap]

## <a name="how-to-move-data-in-a-cluster-with-linux-nodes"></a>Jak przenieść dane w klastrze z węzłami systemu Linux
Masz kilka opcji, aby przenosić dane między węzłami systemu Linux i Windows węzłem klastra. Poniżej przedstawiono trzy typowe metody opisane bardziej szczegółowo w poniższych sekcjach:

* **Plików platformy Azure** — udostępnia zarządzanego udziału plików SMB do przechowywania plików danych w usłudze Azure storage. Windows węzłów i węzłów systemu Linux można zainstalować udział plików platformy Azure jako dysku lub folder, w tym samym czasie, nawet jeśli zostały wdrożone w różnych sieciach wirtualnych.
* **Udostępnianie węzłem SMB** — instaluje Standardowy Windows folderu udostępnionego z węzła głównego na węzłów systemu Linux.
* **Serwer systemu plików NFS węzeł główny** — zapewnia rozwiązanie do udostępniania plików w środowisku mieszanym Windows i Linux.

### <a name="azure-file-storage"></a>Usługa Azure File storage
[Plików platformy Azure](https://azure.microsoft.com/services/storage/files/) service udostępnia udziały plików, przy użyciu standardowego protokołu SMB 2.1. Maszyny wirtualne platformy Azure i usług w chmurze mogą udostępniać dane między składnikami aplikacji za pośrednictwem zainstalowanych udziałów i lokalnych aplikacje mają dostęp do danych plików w udziale za pośrednictwem interfejsu API usługi File storage. 

Aby uzyskać szczegółowy opis kroków utworzyć udział plików platformy Azure i zainstalować go w węźle głównym, zobacz [Rozpoczynanie pracy z usługą Azure File storage w Windows](../../../storage/files/storage-how-to-use-files-windows.md). Aby zainstalować udział plików platformy Azure w węzłach systemu Linux, zobacz [jak używać usługi Azure File storage z systemem Linux](../../../storage/files/storage-how-to-use-files-linux.md). Aby skonfigurować utrwalanie połączeń, zobacz [Persisting połączenia do plików pakietu Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx).

W poniższym przykładzie należy utworzyć udział plików platformy Azure na koncie magazynu. Aby zainstalować udział w węźle głównym, otwórz wiersz polecenia i wprowadź następujące polecenia:

```command
cmdkey /add:allvhdsje.file.core.windows.net /user:allvhdsje /pass:<storageaccountkey>

net use Z: \\allvhdje.file.core.windows.net\rdma /persistent:yes
```

W tym przykładzie allvhdsje to nazwa konta magazynu, storageaccountkey jest klucz konta magazynu, a funkcja rdma jest nazwa udziału plików platformy Azure. Udział plików platformy Azure jest zainstalowany jako określanej przez użytkownika w węźle głównym.

Aby zainstalować udział plików platformy Azure w węzłach systemu Linux, uruchom **clusrun** polecenia w węźle głównym. **[Clusrun](https://technet.microsoft.com/library/cc947685.aspx)**  jest użytecznym narzędziem pakietu HPC Pack do wykonywania zadań administracyjnych w wielu węzłach. (Zobacz też [Clusrun w przypadku węzłów systemu Linux](#Clusrun-for-Linux-nodes) w tym artykule.)

Otwórz okno programu Windows PowerShell, a następnie wprowadź następujące polecenia:

```powershell
clusrun /nodegroup:LinuxNodes mkdir -p /rdma

clusrun /nodegroup:LinuxNodes mount -t cifs //allvhdsje.file.core.windows.net/rdma /rdma -o vers=2.1`,username=allvhdsje`,password=<storageaccountkey>'`,dir_mode=0777`,file_mode=0777
```

Pierwsze polecenie tworzy folder o nazwie /rdma we wszystkich węzłach grupy LinuxNodes. Drugie polecenie instaluje allvhdsjw.file.core.windows.net/rdma udziału plików platformy Azure na folder /rdma z dir i plików trybu bitów o wartości 777. Drugie polecenie allvhdsje to nazwa konta magazynu i storageaccountkey jest klucz konta magazynu.

> [!NOTE]
> "\`" Symbol, drugie polecenie jest symbolem ucieczki dla programu PowerShell. "\`," oznacza, że "," (znak przecinka) jest częścią polecenia.
> 
> 

### <a name="head-node-share"></a>Węzeł główny udziału
Ewentualnie Zainstaluj folderu udostępnionego z węzła głównego w węzłach systemu Linux. Udział udostępnia najprostszy sposób na potrzeby udostępniania plików, ale węzła głównego i wszystkich węzłów systemu Linux, musi zostać wdrożony w tej samej sieci wirtualnej. Poniżej przedstawiono kroki.

1. Utwórz folder w węźle głównym i udostępnić go dla wszystkich użytkowników z uprawnieniami do odczytu/zapisu. Na przykład udostępnić D:\OpenFOAM w węźle głównym jako \\CentOS7RDMA HN\OpenFOAM. Tutaj: CentOS7RDMA HN jest nazwą hosta węzła głównego.
   
    ![Uprawnienia do udziału plików][fileshareperms]
   
    ![Udostępnianie plików][filesharing]
1. Otwórz okno programu Windows PowerShell i uruchom następujące polecenia:
   
    ```powershell
    clusrun /nodegroup:LinuxNodes mkdir -p /openfoam
   
    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS7RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

Pierwsze polecenie tworzy folder o nazwie /openfoam we wszystkich węzłach grupy LinuxNodes. Drugie polecenie instaluje //CentOS7RDMA-HN/OpenFOAM folderu udostępnionego na folder z dir i plików trybu bitów o wartości 777. Nazwa użytkownika i hasło w poleceniu powinna być nazwa użytkownika i hasło użytkownika klastra w węźle głównym. (Zobacz [apletu Dodaj lub usuń użytkowników klastra](https://technet.microsoft.com/library/ff919330.aspx).)

> [!NOTE]
> "\`" Symbol, drugie polecenie jest symbolem ucieczki dla programu PowerShell. "\`," oznacza, że "," (znak przecinka) jest częścią polecenia.
> 
> 

### <a name="nfs-server"></a>Serwer systemu plików NFS
Usługi systemu plików NFS pozwala udostępniać i migrować plików na komputerach działa system operacyjny Windows Server 2012 przy użyciu protokołu SMB i opartych na systemie Linux przy użyciu protokołu NFS. Serwer systemu plików NFS i wszystkich innych węzłów muszą zostać wdrożony w tej samej sieci wirtualnej. Zapewnia lepszą zgodność w przypadku węzłów systemu Linux w porównaniu z udziału SMB. Na przykład obsługiwane linki do plików.

1. Aby zainstalować i skonfigurować serwer NFS, wykonaj kroki opisane w [serwera do sieciowego udziału pierwszy System plików End-to-End](http://blogs.technet.com/b/filecab/archive/2012/10/08/server-for-network-file-system-first-share-end-to-end.aspx).
   
    Na przykład można utworzyć udziału NFS o nazwie systemu plików nfs z następującymi właściwościami:
   
    ![Autoryzacja systemu plików NFS][nfsauth]
   
    ![Uprawnienia udziału NFS][nfsshare]
   
    ![Uprawnienia NTFS systemu plików NFS][nfsperm]
   
    ![Właściwości zarządzania systemu plików NFS][nfsmanage]
1. Otwórz okno programu Windows PowerShell i uruchom następujące polecenia:
   
    ```powershell
    clusrun /nodegroup:LinuxNodes mkdir -p /nfsshare
   
    clusrun /nodegroup:LinuxNodes mount CentOS7RDMA-HN:/nfs /nfsshared
    ```
   
   Pierwsze polecenie tworzy folder o nazwie /nfsshared we wszystkich węzłach grupy LinuxNodes. Drugie polecenie instaluje udziału NFS CentOS7RDMA HN: / systemu plików nfs na folder. W tym miejscu CentOS7RDMA HN: / systemu plików nfs jest zdalny ścieżką udziału NFS.

## <a name="how-to-submit-jobs"></a>Jak przesłać zadania
Istnieje kilka sposobów, aby przesłać zadania do klastra pakietu HPC Pack:

* Menedżer klastra HPC lub Menedżer zadań HPC graficznego interfejsu użytkownika
* Portalu internetowego HPC
* Interfejs API REST

Przesyłanie zadań w klastrze na platformie Azure za pomocą narzędzia z graficznym interfejsem użytkownika HPC Pack oraz portalu internetowego HPC są takie same jak dla Windows węzłów obliczeniowych. Zobacz [Menedżer zadania pakietu HPC Pack](https://technet.microsoft.com/library/ff919691.aspx) i [jak przesyłanie zadań z komputera klienckiego w środowisku lokalnym](../../windows/hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Aby przesyłać zadania za pomocą interfejsu API REST, zobacz [tworzenie i przesyłanie zadań za pomocą interfejsu API REST w Microsoft HPC Pack](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx). Przesyłanie zadań z klientów systemu Linux, również znaleźć przykład języka Python w [zestawu SDK pakietu HPC Pack](https://www.microsoft.com/download/details.aspx?id=47756).

## <a name="clusrun-for-linux-nodes"></a>Clusrun w przypadku węzłów systemu Linux
Pakiet HPC Pack [clusrun](https://technet.microsoft.com/library/cc947685.aspx) narzędzie może służyć do wykonywania poleceń w węzłach systemu Linux przy użyciu wiersza polecenia lub Menedżer klastra HPC. Poniżej przedstawiono niektóre podstawowe przykłady.

* Pokaż aktualne nazwy użytkowników we wszystkich węzłach w klastrze.
  
    ```command
    clusrun whoami
    ```
* Zainstaluj **gdb** narzędzie debugger z **yum** na wszystkich węzłach w linuxnodes grupy i następnie uruchom ponownie węzły po upływie 10 minut.
  
    ```command
    clusrun /nodegroup:linuxnodes yum install gdb –y; shutdown –r 10
    ```
* Utwórz skrypt powłoki, wyświetlanie poszczególnych liczb od 1 do 10 dla jednej sekundy w każdym węźle systemu Linux w klastrze, uruchom go i natychmiast wyświetlić dane wyjściowe z węzłów.
  
    ```command
    clusrun /interleaved /nodegroup:linuxnodes echo \"for i in {1..10}; do echo \\\"\$i\\\"; sleep 1; done\" ^> script.sh; chmod +x script.sh; ./script.sh
    ```

> [!NOTE]
> Może być konieczne użycie niektórych znaków ucieczki w **clusrun** poleceń. Jak pokazano w poniższym przykładzie, użyj ^ w wierszu polecenia, aby anulować ">" symboli.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
* Spróbuj skalowania w górę do większej liczby węzłów klastra lub obciążenie systemu Linux w klastrze. Aby uzyskać przykład, zobacz [uruchamianie programu NAMD przy użyciu pakietu Microsoft HPC Pack w systemie Linux węzłów obliczeniowych w usłudze Azure](hpcpack-cluster-namd.md).
* Spróbuj klastra z [maszyn wirtualnych z funkcją RDMA, mocy obliczeniowej](../../windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) do uruchamiania obciążeń MPI. Aby uzyskać przykład, zobacz [uruchamianie programu OpenFOAM przy użyciu pakietu Microsoft HPC Pack na RDMA systemu Linux klastra na platformie Azure](hpcpack-cluster-openfoam.md).
* Jeśli interesuje Cię w pracy z węzłów systemu Linux w klastrze pakietu HPC Pack w środowisku lokalnym, zobacz [wskazówki TechNet](https://technet.microsoft.com/library/mt595803.aspx).

<!--Image references-->
[scenario]:media/hpcpack-cluster/scenario.png
[portal]:media/hpcpack-cluster/portal.png
[validate]:media/hpcpack-cluster/validate.png
[resources]:media/hpcpack-cluster/resources.png
[deploy]:media/hpcpack-cluster/deploy.png
[management]:media/hpcpack-cluster/management.png
[heatmap]:media/hpcpack-cluster/heatmap.png
[fileshareperms]:media/hpcpack-cluster/fileshare1.png
[filesharing]:media/hpcpack-cluster/fileshare2.png
[nfsauth]:media/hpcpack-cluster/nfsauth.png
[nfsshare]:media/hpcpack-cluster/nfsshare.png
[nfsperm]:media/hpcpack-cluster/nfsperm.png
[nfsmanage]:media/hpcpack-cluster/nfsmanage.png
