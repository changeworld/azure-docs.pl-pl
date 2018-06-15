---
title: Uruchomienie GWIAZDĘ — CCM + pakietem HPC na maszynach wirtualnych systemu Linux | Dokumentacja firmy Microsoft
description: Wdrażanie klastra Microsoft HPC Pack na platformie Azure i uruchomienie GWIAZDĘ — CCM + zadania w systemie Linux w wielu węzłach obliczeniowych przez sieć RDMA.
services: virtual-machines-linux
documentationcenter: ''
author: xpillons
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: 75523406-d268-4623-ac3e-811c7b74de4b
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 09/13/2016
ms.author: xpillons
ms.openlocfilehash: 8689d7abfd5ab45277df3b5672a1f6e7e874d88e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
ms.locfileid: "30841491"
---
# <a name="run-star-ccm-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>Uruchomienie GWIAZDĘ — CCM + z pakietem Microsoft HPC na Linux RDMA klaster na platformie Azure
W tym artykule przedstawiono sposób wdrażania klastra Microsoft HPC Pack na platformie Azure i uruchom [GWIAZDKĘ CD adapco-CCM +](http://www.cd-adapco.com/products/star-ccm%C2%AE) zadania na wielu węzłach obliczeniowych Linux, które są wzajemnie InfiniBand.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

Microsoft HPC Pack udostępnia funkcje, aby obsługiwał różne HPC na dużą skalę i równoległych aplikacji, w tym aplikacji MPI w klastrach maszyny wirtualne Microsoft Azure. HPC Pack również umożliwia uruchamianie aplikacji HPC systemu Linux na węźle obliczeń maszyn wirtualnych systemu Linux, które zostały wdrożone w klastrze HPC Pack. Aby obejrzeć wprowadzenie do pomocą Linux węzły obliczeniowe HPC Pack zobacz [wprowadzenie węzły obliczeniowe systemu Linux w klastrze HPC Pack na platformie Azure](hpcpack-cluster.md).

## <a name="set-up-an-hpc-pack-cluster"></a>Konfigurowanie klastra HPC Pack
Pobierz skrypty wdrażania HPC Pack IaaS z [Centrum pobierania](https://www.microsoft.com/en-us/download/details.aspx?id=44949) i wyodrębnić je lokalnie.

Program Azure PowerShell jest wymagane. Jeśli nie skonfigurowano programu PowerShell na komputerze lokalnym, przeczytaj artykuł na temat [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

W momencie pisania tego dokumentu obrazy systemu Linux z portalu Azure Marketplace, (zawierający sterowniki InfiniBand dla platformy Azure) są SLES 12, CentOS 6.5 i CentOS 7.1. W tym artykule jest na podstawie użycia SLES 12. Aby pobrać nazwy wszystkich obrazów systemu Linux, które obsługują HPC w witrynie Marketplace, można uruchomić następujące polecenie programu PowerShell:

```
    get-azurevmimage | ?{$_.ImageName.Contains("hpc") -and $_.OS -eq "Linux" }
```

Wyświetla dane wyjściowe, lokalizacji, w których te obrazy są dostępne i nazwa obrazu (**Nazwa_obrazu**) do użycia w szablonie wdrożenia później.

Przed wdrożeniem klastra, masz do utworzenia pliku szablonu wdrożenia HPC Pack. Ponieważ firma Microsoft docelowych małych klastra, węzłem głównym zostanie kontrolera domeny i hosta lokalnego bazy danych SQL.

Następujący szablon będzie wdrażanie węzła głównego, Utwórz plik XML o nazwie **MyCluster.xml**i Zastąp wartości **SubscriptionId**, **StorageAccount**,  **Lokalizacja**, **VMName**, i **ServiceName** z Twoimi zmianami.

    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionId>99999999-9999-9999-9999-999999999999</SubscriptionId>
        <StorageAccount>mystorageaccount</StorageAccount>
      </Subscription>
      <Location>North Europe</Location>
      <VNet>
        <VNetName>hpcvnetne</VNetName>
        <SubnetName>subnet-hpc</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpc.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>myhpchn</VMName>
        <ServiceName>myhpchn</ServiceName>
        <VMSize>Standard_D4</VMSize>
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>lnxcn-%0001%</VMNamePattern>
        <ServiceNamePattern>mylnxcn%01%</ServiceNamePattern>
        <MaxNodeCountPerService>20</MaxNodeCountPerService>
        <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
        <VMSize>A9</VMSize>
        <NodeCount>0</NodeCount>
        <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>

Rozpocznij tworzenie węzłem head za pomocą polecenia programu PowerShell w wiersza polecenia o podniesionych uprawnieniach:

```
    .\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml
```

Po upływie 20-30 minut zapewne zechcesz węzła głównego. Można połączyć się go w portalu Azure, klikając **Connect** ikona maszyny wirtualnej.

Po pewnym czasie może być konieczne napraw usługę przesyłania dalej DNS. Aby to zrobić, uruchom Menedżera DNS.

1. Kliknij prawym przyciskiem myszy nazwę serwera w Menedżerze DNS, wybierz **właściwości**, a następnie kliknij przycisk **usług przesyłania dalej** kartę.
2. Kliknij przycisk **Edytuj** przycisk, aby usunąć wszystkie usługi przesyłania dalej, a następnie kliknij przycisk **OK**.
3. Upewnij się, że **Użyj wskazówek dotyczących serwerów głównych, jeśli są dostępne nie usług przesyłania dalej** pole wyboru jest zaznaczone, a następnie kliknij przycisk **OK**.

## <a name="set-up-linux-compute-nodes"></a>Konfigurowanie węzłów obliczeniowych systemu Linux
Możesz wdrożyć węzły obliczeniowe systemu Linux przy użyciu tego samego szablonu wdrażania, która pozwala utworzyć węzła głównego.

Skopiuj plik **MyCluster.xml** z komputera lokalnego do węzła głównego i aktualizacja **NodeCount** tag o liczbie węzłów, które mają zostać wdrożone (< = 20). Należy zachować ostrożność ma za mało dostępnych rdzeni limitu Azure, ponieważ każde wystąpienie A9 będą korzystać z 16 rdzeni w ramach subskrypcji. Jeśli chcesz użyć więcej maszyn wirtualnych w tej samej budżetu, można użyć wystąpienia A8 (8 rdzeni) zamiast A9.

W węźle głównym skopiuj HPC Pack IaaS skryptów wdrażania.

Uruchom następujące polecenia programu PowerShell systemu Azure w wiersza polecenia o podniesionych uprawnieniach:

1. Uruchom **Add-AzureAccount** nawiązać połączenia z subskrypcją platformy Azure.
2. Jeśli masz wiele subskrypcji, uruchom **Get AzureSubscription** wyświetlić je.
3. Wartość domyślna subskrypcja, uruchamiając **xxxx wybierz AzureSubscription — Nazwa subskrypcji — domyślna** polecenia.
4. Uruchom **.\New-HPCIaaSCluster.ps1 - ConfigFile MyCluster.xml** rozpocząć wdrażanie węzły obliczeniowe systemu Linux.
   
   ![Wdrażanie węzła HEAD w akcji][hndeploy]

Otwórz narzędzie Menedżer klastra HPC Pack. Po kilku minutach węzły obliczeniowe Linux regularnie pojawi się lista węzły klastra obliczeniowego. W trybie klasycznym wdrażania maszyn wirtualnych IaaS są tworzone po kolei. Dlatego jeśli ważne jest liczba węzłów, pobieranie wszystkie wdrożone może zająć znaczną ilość czasu.

![Linux węzłów w Menedżerze klastra HPC Pack][clustermanager]

Teraz wszystkie węzły są gotowe do działania w klastrze, czy ustawienia dodatkowej infrastruktury, aby się upewnić.

## <a name="set-up-an-azure-file-share-for-windows-and-linux-nodes"></a>Konfigurowanie udziału plików platformy Azure dla systemu Windows i Linux węzłów
Usługa Azure plików służy do przechowywania plików danych, pakiety aplikacji i skryptów. Plików na platformę Azure zapewnia możliwości CIFS u góry magazynu obiektów Blob platformy Azure jako magazynu trwałego. Należy pamiętać, że nie jest to najbardziej skalowalne rozwiązanie, ale jest to najprostsza i nie wymaga dedykowanych maszyn wirtualnych.

Tworzenie udziału plików platformy Azure zgodnie z instrukcjami w artykule [Rozpoczynanie pracy z magazynem plików Azure w systemie Windows](../../../storage/files/storage-dotnet-how-to-use-files.md).

Zachowaj nazwę konta magazynu jako **saname**, nazwa udziału plików jako **sharename**i klucz konta magazynu jako **sakey**.

### <a name="mount-the-azure-file-share-on-the-head-node"></a>Instalowanie udziału plików Azure w węźle głównym
Otwórz wiersz polecenia z podwyższonym poziomem uprawnień i uruchom następujące polecenie, aby zapisać poświadczenia w magazynie komputera lokalnego:

```
    cmdkey /add:<saname>.file.core.windows.net /user:<saname> /pass:<sakey>
```

Następnie Aby zainstalować udział plików Azure, uruchom polecenie:

```
    net use Z: \\<saname>.file.core.windows.net\<sharename> /persistent:yes
```

### <a name="mount-the-azure-file-share-on-linux-compute-nodes"></a>Instalowanie udziału plików Azure w węzłach obliczeniowych systemu Linux
Jeden przydatne narzędzie dołączoną HPC Pack to narzędzie clusrun. To narzędzie wiersza polecenia umożliwia jednoczesną tego samego polecenia na zestaw węzłów obliczeniowych. W tym przypadku służy do udziałów plików Azure i utrwala go przetrwać ponowne uruchomienia.
W podniesionego wiersza poleceń w węźle głównym uruchom następujące polecenia.

Aby utworzyć katalog instalacji:

```
    clusrun /nodegroup:LinuxNodes mkdir -p /hpcdata
```

Aby zainstalować udział plików Azure:

```
    clusrun /nodegroup:LinuxNodes mount -t cifs //<saname>.file.core.windows.net/<sharename> /hpcdata -o vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777
```

Aby zachować udziału instalacji:

```
    clusrun /nodegroup:LinuxNodes "echo //<saname>.file.core.windows.net/<sharename> /hpcdata cifs vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777 >> /etc/fstab"
```

## <a name="install-star-ccm"></a>Zainstaluj GWIAZDKĘ — CCM +
Azure wystąpień maszyna wirtualna A8 i A9 świadczenie obsługi InfiniBand i funkcji RDMA. Sterowniki jądra, które włączają te funkcje są dostępne dla systemu Windows Server 2012 R2, SUSE 12 CentOS 6.5 i CentOS 7.1 obrazów w portalu Azure Marketplace. Microsoft MPI i MPI firmy Intel (wersji 5.x) są dwie biblioteki MPI, które obsługują te sterowniki w usłudze Azure.

GWIAZDKĘ CD adapco — CCM + wersji 11.x i później jest powiązane z wersją Intel MPI 5.x, więc InfiniBand obsługę Azure jest dołączony.

Pobierz Linux64 GWIAZDKĘ — CCM + pakietu z [CD adapco portal](https://steve.cd-adapco.com). W naszym przykładzie użyliśmy wersji 11.02.010 w mieszanych dokładności.

W węźle głównym w **/hpcdata** plików Azure udostępnić, utworzyć skrypt powłoki o nazwie **setupstarccm.sh** o następującej zawartości. Ten skrypt będzie uruchamiany w każdym węźle obliczeń, aby skonfigurować GWIAZDKĘ — CCM + lokalnie.

#### <a name="sample-setupstarcmsh-script"></a>Przykładowy skrypt setupstarcm.sh
```
    #!/bin/bash
    # setupstarcm.sh to set up STAR-CCM+ locally

    # Create the CD-adapco main directory
    mkdir -p /opt/CD-adapco

    # Copy the STAR-CCM package from the file share to the local directory
    cp /hpcdata/StarCCM/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz /opt/CD-adapco/

    # Extract the package
    tar -xzf /opt/CD-adapco/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz -C /opt/CD-adapco/

    # Start a silent installation of STAR-CCM without the FLEXlm component
    /opt/CD-adapco/starccm+_11.02.010/STAR-CCM+11.02.010_01_linux-x86_64-2.5_gnu4.8.bin -i silent -DCOMPUTE_NODE=true -DNODOC=true -DINSTALLFLEX=false

    # Update memory limits
    echo "*               hard    memlock         unlimited" >> /etc/security/limits.conf
    echo "*               soft    memlock         unlimited" >> /etc/security/limits.conf
```
Teraz, aby skonfigurować GWIAZDKĘ — CCM + na wszystkich systemu Linux węzły obliczeniowe, otwórz wiersz polecenia z podwyższonym poziomem uprawnień i uruchom następujące polecenie:

```
    clusrun /nodegroup:LinuxNodes bash /hpcdata/setupstarccm.sh
```

Po uruchomieniu polecenia można monitorować użycie procesora CPU za pomocą Mapa cieplna z Menedżera klastra. Po kilku minutach wszystkie węzły powinny zostać poprawnie skonfigurowane.

## <a name="run-star-ccm-jobs"></a>Uruchomienie GWIAZDĘ — CCM + zadania
HPC Pack służy do jego możliwości harmonogram zadania uruchomienie GWIAZDĘ — CCM + zadania. Aby to zrobić, potrzebujemy obsługę kilku skryptów, które są używane do uruchamiania zadania i uruchomienie GWIAZDĘ — CCM +. Dane wejściowe pozostaje w udziale plików Azure pierwszy dla uproszczenia.

Poniższy skrypt programu PowerShell jest używany do kolejki GWIAZDY — CCM + zadania. Trwa trzech argumentów:

* Nazwa modelu
* Liczba węzłów do użycia
* Liczba rdzeni w każdym węźle ma być używany

Ponieważ GWIAZDKĘ — CCM + wpisać przepustowości pamięci jest lepiej jest użyć mniejszej liczby rdzeni w jednym węzłów obliczeniowych i dodać nowe węzły. Dokładna liczba rdzeni w jednym węźle zależy od rodziny procesorów i szybkość połączenia.

Węzły są przydzielane wyłącznie dla zadania i nie może być współużytkowana z innymi zadaniami. Zadanie nie jest uruchomiona jako zadanie MPI bezpośrednio. **Runstarccm.sh** skrypt powłoki rozpocznie uruchamiania MPI.

Wejściowy modelu i **runstarccm.sh** skryptu są przechowywane w **/hpcdata** udziału, który został wcześniej zainstalowany.

Pliki dziennika są nazywane z Identyfikatorem zadania i są przechowywane w **udziału /hpcdata**, wraz z GWIAZDY — CCM + plików wyjściowych.

#### <a name="sample-submitstarccmjobps1-script"></a>Przykładowy skrypt SubmitStarccmJob.ps1
```
    Add-PSSnapin Microsoft.HPC -ErrorAction silentlycontinue
    $scheduler="headnodename"
    $modelName=$args[0]
    $nbCoresPerNode=$args[2]
    $nbNodes=$args[1]

    #---------------------------------------------------------------------------------------------------------
    # Create a new job; this will give us the job ID that's used to identify the name of the uploaded package in Azure
    #
    $job = New-HpcJob -Name "$modelName $nbNodes $nbCoresPerNode" -Scheduler $scheduler -NumNodes $nbNodes -NodeGroups "LinuxNodes" -FailOnTaskFailure $true -Exclusive $true
    $jobId = [String]$job.Id

    #---------------------------------------------------------------------------------------------------------
    # Submit the job     
    $workdir =  "/hpcdata"
    $execName = "$nbCoresPerNode runner.java $modelName.sim"

    $job | Add-HpcTask -Scheduler $scheduler -Name "Compute" -stdout "$jobId.log" -stderr "$jobId.err" -Rerunnable $false -NumNodes $nbNodes -Command "runstarccm.sh $execName" -WorkDir "$workdir"


    Submit-HpcJob -Job $job -Scheduler $scheduler
```
Zastąp **runner.java** z Twojej preferowanych GWIAZDKĄ — CCM + Java modelu uruchamiania i rejestrowanie kodu.

#### <a name="sample-runstarccmsh-script"></a>Przykładowy skrypt runstarccm.sh
```
    #!/bin/bash
    echo "start"
    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    echo ${SCRIPT_PATH}
    # Set the mpirun runtime environment
    export CDLMD_LICENSE_FILE=1999@flex.cd-adapco.com

    # mpirun command
    STARCCM=/opt/CD-adapco/STAR-CCM+11.02.010/star/bin/starccm+

    # Get node information from ENVs
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    NBCORESPERNODE=$1

    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$
    echo ${NODELIST_PATH}

    # Get every node name and write into the hostfile file
    I=1
    NBNODES=0
    while [ ${I} -lt ${COUNT} ]
    do
        echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
        let "NBNODES=${NBNODES}+1"
    done
    let "NBCORES=${NBNODES}*${NBCORESPERNODE}"

    # Run STAR-CCM with the hostfile argument
    #  
    ${STARCCM} -np ${NBCORES} -machinefile ${NODELIST_PATH} \
        -power -podkey "<yourkey>" -rsh ssh \
        -mpi intel -fabric UDAPL -cpubind bandwidth,v \
        -mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0" \
        -batch $2 $3
    RTNSTS=$?
    rm -f ${NODELIST_PATH}

    exit ${RTNSTS}
```

W naszym teście użyliśmy token licencji zasilania na żądanie. Token, należy ustawić **$CDLMD_LICENSE_FILE** zmienną środowiskową **1999@flex.cd-adapco.com** i klucz w **- podkey** opcji wiersza polecenia.

Po zainicjowaniu niektórych skrypt wyodrębnia — od **$CCP_NODES_CORES** zmienne środowiskowe tego pakietu HPC ustawiane — Lista węzłów do tworzenia hostfile, używającej uruchamiania MPI. Ta hostfile będzie zawierać listę nazw węzła obliczeń, które są używane do zadania, nazwa jeden na wiersz.

Format **$CCP_NODES_CORES** następuje tego wzorca:

```
<Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
```

Gdzie:

* `<Number of nodes>` jest to liczba węzłów przydzielone do tego zadania.
* `<Name of node_n_...>` to nazwa każdego węzła przydzielone do tego zadania.
* `<Cores of node_n_...>` jest to liczba rdzeni w węźle przydzielone do tego zadania.

Liczba rdzeni (**$NBCORES**) jest obliczana na podstawie liczby węzłów (**$NBNODES**) i liczba rdzeni przypadająca na węzeł (podać jako parametr **$NBCORESPERNODE**).

Dla opcji MPI te, które są używane z Intel MPI na platformie Azure są:

* `-mpi intel` Aby określić Intel MPI.
* `-fabric UDAPL` do korzystania z poleceń Azure InfiniBand.
* `-cpubind bandwidth,v` w celu zoptymalizowania przepustowości dla MPI z GWIAZDKĄ — CCM +.
* `-mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0"` Aby pracować z Azure InfiniBand MPI Intel i ustawić wymaganej liczby rdzeni w jednym węźle.
* `-batch` Aby uruchomić GWIAZDKĘ — CCM + w trybie wsadowym z bez interfejsu użytkownika.

Na koniec można uruchomić zadania, upewnij się, że węzły są gotowe do działania i są w trybie online w Menedżerze klastra. Następnie w wierszu polecenia programu PowerShell, uruchom to:

```
    .\ SubmitStarccmJob.ps1 <model> <nbNodes> <nbCoresPerNode>
```

## <a name="stop-nodes"></a>Zatrzymaj węzłów
Później po wykonaniu tych testów, służy następujące polecenia środowiska PowerShell klastra HPC Pack do zatrzymywania i uruchamiania węzłów:

```
    Stop-HPCIaaSNode.ps1 -Name <prefix>-00*
    Start-HPCIaaSNode.ps1 -Name <prefix>-00*
```

## <a name="next-steps"></a>Kolejne kroki
Spróbuj uruchomić inne obciążenia systemu Linux. Na przykład zobacz:

* [Uruchamianie NAMD z pakietem Microsoft HPC w węzłach obliczeń systemu Linux na platformie Azure](hpcpack-cluster-namd.md)
* [Uruchom OpenFOAM z pakietem Microsoft HPC w klastrze RDMA systemu Linux na platformie Azure](hpcpack-cluster-openfoam.md)

<!--Image references-->
[hndeploy]:media/hpcpack-cluster-starccm/hndeploy.png
[clustermanager]:media/hpcpack-cluster-starccm/ClusterManager.png
