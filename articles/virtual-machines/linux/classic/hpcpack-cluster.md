---
title: Linux obliczeń maszyny wirtualne w klastrze HPC Pack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć i używać klastra HPC Pack Linux komputerowych o wysokiej wydajności (HPC) obciążeń na platformie Azure
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
ms.openlocfilehash: 57ad5d5d2e7e068f47d51408527f1f7553917279
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="get-started-with-linux-compute-nodes-in-an-hpc-pack-cluster-in-azure"></a>Rozpoczynanie pracy z węzłami obliczeniowymi systemu Linux w klastrze pakietu HPC Pack na platformie Azure
Konfigurowanie [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029.aspx) klastra na platformie Azure, zawierającą węzła głównego z systemem Windows Server i kilka obliczeniowe węzłów z systemem obsługiwanych dystrybucji systemu Linux. Poznaj opcje przenoszenia danych między węzły systemu Linux i Windows węzłem głównym klastra. Dowiedz się, jak można przesłać zadań HPC systemu Linux do klastra.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

Na wysokim poziomie na poniższym diagramie przedstawiono klastra HPC Pack, tworzenie i Praca z.

![Klaster HPC Pack z węzłami systemu Linux][scenario]

Inne opcje do uruchamiania obciążeń HPC systemu Linux na platformie Azure, zobacz [zasobów technicznych partii i wysoką wydajność pracy](../../../batch/big-compute-resources.md).

## <a name="deploy-an-hpc-pack-cluster-with-linux-compute-nodes"></a>Wdrożenie klastra HPC Pack z węzłami obliczeniowymi systemu Linux
W tym artykule przedstawiono się, że możesz dwie opcje do wdrożenia klastra HPC Pack na platformie Azure z systemem Linux węzły obliczeniowe. Obie metody za pomocą obrazu systemu Windows Server z witryny Marketplace HPC Pack można utworzyć węzła głównego. 

* **Szablonu usługi Azure Resource Manager** -Użyj szablonu z portalu Azure Marketplace, lub w szablonie Szybki Start od społeczności, można zautomatyzować tworzenie klastra w modelu wdrażania usługi Resource Manager. Na przykład [klastra HPC Pack dla systemu Linux obciążeń](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) szablonu w portalu Azure Marketplace tworzy pełną infrastruktura klastra HPC Pack dla systemu Linux HPC obciążeń.
* **Skrypt programu PowerShell** -użyj [skrypt wdrożenia Microsoft HPC Pack IaaS](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) (**HpcIaaSCluster.ps1 nowy**) można zautomatyzować wdrożenie całego klastra w klasycznym modelu wdrażania. Ten skrypt programu PowerShell usługi Azure używa obrazu maszyny Wirtualnej pakietu HPC w portalu Azure Marketplace do szybkiego wdrożenia i zapewnia rozbudowany zestaw parametrów konfiguracji, aby wdrożyć węzły obliczeniowe systemu Linux.

Aby uzyskać więcej informacji dotyczących opcji wdrażania klastrów HPC Pack na platformie Azure, zobacz [opcje tworzenia i zarządzania nimi, przetwarzanie o wysokiej wydajności (HPC) klastra na platformie Azure z pakietem Microsoft HPC](../hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="prerequisites"></a>Wymagania wstępne
* **Subskrypcja platformy Azure** — można użyć subskrypcji w usłudze Azure globalne lub chińskiej wersji platformy Azure. Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.
* **Limit przydziału rdzeni** — należy zwiększyć limit przydziału rdzeni, zwłaszcza, jeśli użytkownik chce wdrożyć kilka węzłów klastra z wielordzeniowych rozmiarów maszyn wirtualnych. Aby zwiększyć limit przydziału, otwórz żądanie pomocy technicznej online klienta bez dodatkowych opłat.
* **Dystrybucje systemu Linux** — obecnie HPC Pack obsługuje następujące dystrybucje systemu Linux dla węzłów obliczeniowych. Użyj wersji Marketplace tych dystrybucji, jeśli jest on dostępny lub Podaj własny.
  
  * **Na podstawie centOS**: 6.5, 6.6, 6.7, 7.0, 7.1, 7.2, 6.5 HPC, 7.1 HPC
  * **Red Hat Enterprise Linux**: 6.7, 6.8, 7.2
  * **SUSE Linux Enterprise Server**: SLES 12 SLES 12 (Premium), SLES 12 z dodatkiem SP1, SLES 12 z dodatkiem SP1 (Premium), SLES 12 dla HPC, SLES 12 dla HPC (Premium)
  * **Ubuntu Server**: 14.04 LTS, 16.04 LTS
    
    > [!TIP]
    > Aby użyć sieciowych Azure RDMA z jednym rozmiary maszyny Wirtualnej z funkcją RDMA, podaj SUSE Linux Enterprise Server 12 HPC lub na podstawie CentOS HPC obrazu z portalu Azure Marketplace. Aby uzyskać więcej informacji, zobacz [wysokiej wydajności obliczeniowe rozmiarów maszyn wirtualnych](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    > 

Dodatkowe wymagania wstępne dotyczące wdrażania klastra za pomocą skryptu wdrażania HPC Pack IaaS:

* **Komputer kliencki** — należy komputer kliencki z systemem Windows uruchom skrypt wdrażania klastra.
* **Program Azure PowerShell** - [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview) (wersja 0.8.10 lub nowszego) na komputerze klienckim.
* **Skrypt wdrożenia HPC Pack IaaS** — Pobierz i Rozpakuj najnowszej wersji skryptu z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). Wersja skryptu można sprawdzić, uruchamiając `.\New-HPCIaaSCluster.ps1 –Version`. Ten artykuł jest oparty na wersji 4.4.1 lub nowszej skryptu.

### <a name="deployment-option-1-use-a-resource-manager-template"></a>Opcja wdrażania 1. Użyj szablonu usługi Resource Manager
1. Przejdź do [klastra HPC Pack dla systemu Linux obciążeń](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) szablonu w portalu Azure Marketplace, a następnie kliknij przycisk **Wdróż**.
2. W portalu Azure, przejrzyj informacje, a następnie kliknij przycisk **Utwórz**.
   
    ![Tworzenie portalu][portal]
3. Na **podstawy** bloku, wprowadź nazwę klastra, który także nazwy węzła głównego maszyny Wirtualnej. Można wybrać istniejącą grupę zasobów lub Utwórz grupę wdrożenia w lokalizacji, która jest dostępna. Lokalizacja ma wpływ na dostępność niektórych rozmiarów maszyn wirtualnych i innymi usługami Azure (zobacz [produkty, które są dostępne w regionie](https://azure.microsoft.com/regions/services/)).
4. Na **przejdź do węzła ustawienia** bloku, w przypadku pierwszego wdrożenia zazwyczaj można zaakceptować ustawienia domyślne. 
   
   > [!NOTE]
   > **Adresu URL skryptu pokonfiguracyjnego** jest ustawienie opcjonalne, aby określić publicznie dostępnych skrypt programu Windows PowerShell, który ma zostać uruchomione w węźle głównym maszyny Wirtualnej po jej uruchomieniu. 
   > 
   > 
5. Na **obliczeniowe węzła ustawienia** bloku, wybierz wzorzec nazewnictwa dla węzłów, liczby i rozmiaru węzły i dystrybucja systemu Linux do wdrożenia.
6. Na **ustawienia infrastruktury** bloku nazwy sieci wirtualnej i usługi Active Directory wprowadź domenę, domeny i poświadczenia administratora maszyny Wirtualnej i wzorzec nazewnictwa dla kont magazynu.
   
   > [!NOTE]
   > Do uwierzytelniania użytkowników klastra HPC Pack używane do domeny usługi Active Directory. 
   > 
   > 
7. Po uruchomieniu testów sprawdzania poprawności i zapoznaj się z warunkami użytkowania, kliknij przycisk **zakupu**.

### <a name="deployment-option-2-use-the-iaas-deployment-script"></a>Opcja wdrażania 2. Użyj skryptu wdrożenia IaaS
Poniżej przedstawiono dodatkowe wymagania wstępne dotyczące wdrażania klastra za pomocą skryptu wdrażania HPC Pack IaaS:

* **Komputer kliencki** — należy komputer kliencki z systemem Windows uruchom skrypt wdrażania klastra.
* **Program Azure PowerShell** - [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview) (wersja 0.8.10 lub nowszego) na komputerze klienckim.
* **Skrypt wdrożenia HPC Pack IaaS** — Pobierz i Rozpakuj najnowszej wersji skryptu z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). Wersja skryptu można sprawdzić, uruchamiając `.\New-HPCIaaSCluster.ps1 –Version`. Ten artykuł jest oparty na wersji 4.4.1 lub nowszej skryptu.

**Plik konfiguracji XML**

Skrypt wdrażania HPC Pack IaaS używa pliku konfiguracji XML jako dane wejściowe do opisywania klastra HPC. Przykładowy plik konfiguracyjny określa mały składające się z węzłem głównym HPC Pack i dwa węzły obliczeniowe A7 CentOS 7.0 Linux rozmiar klastra. 

Zmodyfikuj odpowiednio dla swojego środowiska i konfiguracji klastra, a następnie zapisz plik z nazwą, takich jak HPCDemoConfig.xml. Na przykład należy podać nazwę subskrypcji i nazwy konta magazynu unikatowy i nazwa usługi w chmurze. Ponadto można wybrać inny obraz systemu Linux obsługiwane dla węzłów obliczeniowych. Aby uzyskać więcej informacji na temat elementów w pliku konfiguracji, zobacz plik Manual.rtf w folderze skryptów i [utworzyć klaster HPC z skrypt wdrożenia HPC Pack IaaS](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

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

**Aby uruchomić skrypt wdrażania HPC Pack IaaS**

1. Jako administrator Otwórz program Windows PowerShell na komputerze klienckim.
2. Zmień katalog na folder, w którym skrypt jest zainstalowany (E:\IaaSClusterScript w tym przykładzie).
   
    ```powershell
    cd E:\IaaSClusterScript
    ```
3. Uruchom następujące polecenie, aby wdrożyć klaster HPC Pack. W tym przykładzie przyjęto założenie, że plik konfiguracji znajduje się w E:\HPCDemoConfig.xml
   
    ```powershell
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```
   
    a. Ponieważ **AdminPassword** nie została określona w poprzednim poleceniu, zostanie wyświetlony monit o wprowadzenie hasła dla użytkownika *MyAdminName*.
   
    b. Skrypt uruchamia następnie można sprawdzić poprawności pliku konfiguracji. Może potrwać kilka minut w zależności od połączenia sieciowego.
   
    ![Walidacja][validate]
   
    c. Po operacji sprawdzania poprawności przekazać, skrypt zawiera listę zasobów klastra do utworzenia. Wprowadź *Y* aby kontynuować.
   
    ![Zasoby][resources]
   
    d. Skrypt rozpocznie wdrażanie klastra HPC Pack i kończy działanie konfiguracji bez dalszego wymagane ręczne wykonanie czynności. Skrypt może trwać kilka minut.
   
    ![Wdrażanie][deploy]
   
   > [!NOTE]
   > W tym przykładzie skrypt generuje plik dziennika automatycznie ponieważ **- LogFile** parametr nie jest określony. Dzienniki nie są zapisywane w czasie rzeczywistym, ale są zbierane na końcu sprawdzania poprawności i wdrażania. Jeśli proces programu PowerShell zostanie zatrzymana podczas wykonywania skryptu, niektóre dzienniki zostaną utracone.
   > 
   > 

## <a name="connect-to-the-head-node"></a>Połączenie z węzłem głównym
Po wdrożeniu klastra HPC Pack na platformie Azure, [połączenia przez pulpit zdalny](../../windows/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) z węzłem głównym maszyny Wirtualnej przy użyciu domeny poświadczenia zostanie podane podczas wdrażania klastra (na przykład *hpc\\clusteradmin*). Klaster zarządzania za pomocą węzła głównego.

W węźle głównym Uruchom Menedżera klastra HPC, aby sprawdzić stan klastra HPC Pack. Można zarządzać i węzły obliczeniowe monitor węzły obliczeniowe systemu Linux, ten sam sposób pracy z systemem Windows. Na przykład, zobacz węzłów systemu Linux na liście **zarządzanie zasobami** (te węzły zostały wdrożone za pomocą **LinuxNode** szablonu).

![Zarządzanie węzła][management]

Zobacz też w węzłach Linux **Mapa cieplna** widoku.

![Mapa cieplna][heatmap]

## <a name="how-to-move-data-in-a-cluster-with-linux-nodes"></a>Sposób przenoszenia danych w klastrze z węzłami systemu Linux
Można wybrać kilka opcji do przenoszenia danych między węzłami Linux i Windows węzłem głównym klastra. Poniżej przedstawiono trzy typowe metody opisane bardziej szczegółowo w następujących sekcjach:

* **Plików na platformę Azure** — przedstawia zarządzany udział plików SMB do przechowywania plików danych w magazynie Azure. Węzłów z systemem Windows i Linux węzłów mogą zainstalować udział plików Azure jako dysku lub folderu w tym samym czasie, nawet jeśli są one wdrożone w różnych sieciach wirtualnych.
* **Udział SMB węzła głównego** — instaluje standardowe folder udostępniony systemu Windows węzła głównego w węzłach systemu Linux.
* **Serwer systemu plików NFS węzła HEAD** — zapewnia rozwiązanie do udostępniania plików w środowisku mieszanym Windows i Linux.

### <a name="azure-file-storage"></a>Magazyn plików Azure
[Plików Azure](https://azure.microsoft.com/services/storage/files/) udziałami plików przy użyciu standardowego protokołu SMB 2.1 udostępnia usługi. Maszyny wirtualne platformy Azure i usługi w chmurze mogą udostępniać dane między składnikami aplikacji za pośrednictwem zainstalowanych udziałów i lokalnych aplikacji mają dostęp do danych plików w udziale za pośrednictwem interfejsu API magazynu plików. 

Aby uzyskać szczegółowy opis kroków utworzyć udział plików Azure i zainstalować go w węźle głównym, zobacz [Rozpoczynanie pracy z magazynem plików Azure w systemie Windows](../../../storage/files/storage-how-to-use-files-windows.md). Aby zainstalować udział plików Azure w węzłach Linux, zobacz [jak używać magazynu plików Azure z systemem Linux](../../../storage/files/storage-how-to-use-files-linux.md). Aby skonfigurować utrwalanie połączeń, zobacz [połączeń Persisting pliki programu Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx).

W poniższym przykładzie należy utworzyć udział plików Azure na koncie magazynu. Aby zainstalować udział w węźle głównym, otwórz wiersz polecenia i wpisz następujące polecenia:

```command
cmdkey /add:allvhdsje.file.core.windows.net /user:allvhdsje /pass:<storageaccountkey>

net use Z: \\allvhdje.file.core.windows.net\rdma /persistent:yes
```

W tym przykładzie allvhdsje to nazwa konta magazynu, storageaccountkey jest klucz konta magazynu, a funkcja rdma jest nazwa udziału plików platformy Azure. Udziału plików platformy Azure jest zainstalowany jako Z: węzła głównego.

Aby zainstalować udział plików Azure w węzłach Linux, uruchom **clusrun** na węzła głównego. **[Clusrun](https://technet.microsoft.com/library/cc947685.aspx)**  stanowi przydatne narzędzie HPC Pack do wykonywania zadań administracyjnych na wielu węzłach. (Zobacz też [Clusrun dla systemu Linux węzłów](#Clusrun-for-Linux-nodes) w tym artykule.)

Otwórz okno programu Windows PowerShell i wpisz następujące polecenia:

```powershell
clusrun /nodegroup:LinuxNodes mkdir -p /rdma

clusrun /nodegroup:LinuxNodes mount -t cifs //allvhdsje.file.core.windows.net/rdma /rdma -o vers=2.1`,username=allvhdsje`,password=<storageaccountkey>'`,dir_mode=0777`,file_mode=0777
```

Pierwsze polecenie tworzy folder o nazwie /rdma we wszystkich węzłach grupy LinuxNodes. Drugie polecenie instaluje allvhdsjw.file.core.windows.net/rdma udziału plików platformy Azure na folder /rdma z katalogu i pliku bitów trybu ustawioną 777. W drugim poleceniu allvhdsje jest nazwa konta magazynu i storageaccountkey jest klucz konta magazynu.

> [!NOTE]
> "\`" Symbol w drugie polecenie jest symbolem ucieczki dla środowiska PowerShell. "\`," oznacza, że "," (przecinek znak) jest częścią polecenia.
> 
> 

### <a name="head-node-share"></a>Udział węzła głównego
Można również zainstalować z węzłem głównym folderu udostępnionego na węzłów systemu Linux. Udział zapewnia Najprostszym sposobem udostępniania plików, ale węzła głównego i wszystkie węzły Linux należy wdrożyć w tej samej sieci wirtualnej. Poniżej przedstawiono kroki.

1. Utwórz folder w węźle głównym i udostępnić go dla wszystkich osób mających uprawnienia odczytu/zapisu. Na przykład udostępnić D:\OpenFOAM w węźle głównym jako \\CentOS7RDMA HN\OpenFOAM. W tym miejscu CentOS7RDMA HN jest nazwą hosta węzła głównego.
   
    ![Uprawnienia do udziału plików][fileshareperms]
   
    ![Udostępnianie plików][filesharing]
2. Otwórz okno programu Windows PowerShell i uruchom następujące polecenia:
   
    ```powershell
    clusrun /nodegroup:LinuxNodes mkdir -p /openfoam
   
    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS7RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

Pierwsze polecenie tworzy folder o nazwie /openfoam we wszystkich węzłach grupy LinuxNodes. Drugie polecenie instaluje //CentOS7RDMA-HN/OpenFOAM folderu udostępnionego na folder z katalogu i pliku bitów trybu ustawioną 777. Nazwa użytkownika i hasło w poleceniu powinna być nazwa użytkownika i hasło użytkownika klastra w węźle głównym. (Zobacz [Dodaj lub usuń użytkowników klastra](https://technet.microsoft.com/library/ff919330.aspx).)

> [!NOTE]
> "\`" Symbol w drugie polecenie jest symbolem ucieczki dla środowiska PowerShell. "\`," oznacza, że "," (przecinek znak) jest częścią polecenia.
> 
> 

### <a name="nfs-server"></a>Serwer systemu plików NFS
Usługa systemu plików NFS umożliwia udostępnianie i migrować pliki komputerach działa system operacyjny Windows Server 2012 przy użyciu protokołu SMB i opartych na systemie Linux przy użyciu protokołu NFS. Serwer systemu plików NFS i inne węzły ma zostać wdrożony w tej samej sieci wirtualnej. Zapewnia lepszą zgodność z węzłami systemu Linux w porównaniu z udziału SMB. Na przykład obsługuje łącza do plików.

1. Aby zainstalować i skonfigurować serwer NFS, postępuj zgodnie z instrukcjami [serwera dla systemu pierwszego udziału sieciowego na całej trasie](http://blogs.technet.com/b/filecab/archive/2012/10/08/server-for-network-file-system-first-share-end-to-end.aspx).
   
    Na przykład można utworzyć udziału NFS o nazwie nfs z następującymi właściwościami:
   
    ![Autoryzacja systemu plików NFS][nfsauth]
   
    ![Uprawnienia udziału NFS][nfsshare]
   
    ![Uprawnienia NTFS systemu plików NFS][nfsperm]
   
    ![Właściwości zarządzania systemu plików NFS][nfsmanage]
2. Otwórz okno programu Windows PowerShell i uruchom następujące polecenia:
   
    ```powershell
    clusrun /nodegroup:LinuxNodes mkdir -p /nfsshare
   
    clusrun /nodegroup:LinuxNodes mount CentOS7RDMA-HN:/nfs /nfsshared
    ```
   
   Pierwsze polecenie tworzy folder o nazwie /nfsshared we wszystkich węzłach grupy LinuxNodes. Drugie polecenie instaluje udziału NFS CentOS7RDMA HN: / systemu plików nfs na folder. W tym miejscu CentOS7RDMA HN: / systemu plików nfs to ścieżka do zdalnego udziału NFS.

## <a name="how-to-submit-jobs"></a>Temat dotyczący przesyłania zadań
Istnieje kilka sposobów umożliwiają przesyłanie zadań do klastra HPC Pack:

* Menedżer klastrów HPC lub Menedżer zadań klastra HPC graficznego interfejsu użytkownika
* Portalu internetowego HPC
* Interfejs API REST

Przesyłanie zadań do klastra na platformie Azure za pomocą narzędzia HPC Pack graficznego interfejsu użytkownika i portalu internetowego HPC są takie same jak w przypadku systemu Windows węzły obliczeniowe. Zobacz [Menedżer zadań klastra HPC Pack](https://technet.microsoft.com/library/ff919691.aspx) i [sposobu przesyłania zadań z komputera klienckiego lokalnymi](../../windows/hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Aby przesłać zadania za pomocą interfejsu API REST, należy zapoznać się [tworzenie i przesyłanie zadań przy użyciu interfejsu API REST w Microsoft HPC Pack](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx). Do przesyłania zadań w kliencie systemu Linux, również dotyczyć próbki Python w [HPC Pack SDK](https://www.microsoft.com/download/details.aspx?id=47756).

## <a name="clusrun-for-linux-nodes"></a>Clusrun dla węzłów systemu Linux
HPC Pack [clusrun](https://technet.microsoft.com/library/cc947685.aspx) narzędzie może służyć do wykonywania poleceń w węzłach systemu Linux przy użyciu wiersza polecenia lub Menedżer klastra HPC. Poniżej przedstawiono niektóre podstawowe przykłady.

* Pokaż bieżącej nazwy użytkownika na wszystkich węzłach w klastrze.
  
    ```command
    clusrun whoami
    ```
* Zainstaluj **gdb** narzędzie debugera z **yum** na wszystkich węzłów w linuxnodes grupy, a następnie uruchom ponownie węzeł po 10 minutach.
  
    ```command
    clusrun /nodegroup:linuxnodes yum install gdb –y; shutdown –r 10
    ```
* Utwórz skrypt powłoki wyświetlania każdej liczby od 1 do 10 sekundy jeden w każdym węźle systemu Linux w klastrze, uruchom go i natychmiast wyświetlane dane wyjściowe z węzłów.
  
    ```command
    clusrun /interleaved /nodegroup:linuxnodes echo \"for i in {1..10}; do echo \\\"\$i\\\"; sleep 1; done\" ^> script.sh; chmod +x script.sh; ./script.sh
    ```

> [!NOTE]
> Konieczne może być użycia pewnych znaków ucieczki w **clusrun** poleceń. Jak pokazano w tym przykładzie, użyj ^ w wierszu polecenia, aby wprowadzić ">" symbolu.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
* Spróbuj skalowaniu większą liczbę węzłów klastra lub działających obciążeń systemu Linux w klastrze. Na przykład zobacz [Uruchom NAMD z pakietem Microsoft HPC w systemie Linux obliczeniowe węzłów na platformie Azure](hpcpack-cluster-namd.md).
* Spróbuj klaster z [maszyn wirtualnych z funkcją RDMA, obliczeniowych](../../windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) uruchamiać obciążenia na MPI. Na przykład zobacz [klastra uruchom OpenFOAM z pakietem Microsoft HPC na RDMA systemu Linux na platformie Azure](hpcpack-cluster-openfoam.md).
* Jeśli interesuje Cię w pracy z Linux węzłów w klastrze HPC Pack lokalnych, zobacz [wskazówki TechNet](https://technet.microsoft.com/library/mt595803.aspx).

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
