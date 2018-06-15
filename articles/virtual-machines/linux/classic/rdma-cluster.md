---
title: Konfigurowanie klastra Linux RDMA na uruchamianie aplikacji MPI | Dokumentacja firmy Microsoft
description: Tworzenie klastra Linux rozmiaru H16r H16mr, A8 i A9 maszyn wirtualnych do używania w sieci Azure RDMA do uruchamiania aplikacji MPI
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 01834bad-c8e6-48a3-b066-7f1719047dd2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: danlep
ms.openlocfilehash: d53305aae3b12c0de983dced85a9626cf98c6309
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34210379"
---
# <a name="set-up-a-linux-rdma-cluster-to-run-mpi-applications"></a>Konfigurowanie klastra RDMA systemu Linux na potrzeby uruchamiania aplikacji MPI
Dowiedz się, jak skonfigurować klaster RDMA systemu Linux na platformie Azure z [wysokiej wydajności obliczeniowe rozmiarów maszyn wirtualnych](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) na uruchamianie równoległe aplikacji komunikat interfejsu (Passing Interface). Ten artykuł zawiera kroki, aby przygotować obraz HPC systemu Linux, aby uruchomić Intel MPI w klastrze. Po przygotowaniu można wdrożyć klaster maszyn wirtualnych przy użyciu tego obrazu i jeden rozmiary maszyny Wirtualnej platformy Azure z funkcją RDMA (obecnie H16r H16mr, A8 i A9). Klaster umożliwia uruchamianie aplikacji MPI wydajne komunikowanie się przez sieć małym opóźnieniu i dużej przepustowości oparte na technologii dostępu (do pamięci RDMA) zdalnego pamięci.

> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [usługi Azure Resource Manager](../../../resource-manager-deployment-model.md) i klasycznym. Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.

## <a name="cluster-deployment-options"></a>Opcje wdrażania klastra
Poniżej przedstawiono metody można użyć do utworzenia klastra Linux RDMA z lub bez harmonogramu zadań.

* **Skrypty interfejsu wiersza polecenia platformy Azure**: jak pokazano w dalszej części tego artykułu, użyj [interfejsu wiersza polecenia platformy Azure](../../../cli-install-nodejs.md) (CLI) do skryptu wdrażania klastra maszyn wirtualnych z funkcją RDMA. Interfejsu wiersza polecenia w trybie zarządzania usługami tworzy węzłów klastra szeregowo w klasycznym modelu wdrożenia, więc wdrażania wielu węzłów obliczeniowych może potrwać kilka minut. Aby włączyć połączenia sieciowego RDMA, korzystając z klasycznym modelu wdrożenia, należy wdrożyć maszyn wirtualnych w tej samej usłudze w chmurze.
* **Szablony usługi Azure Resource Manager**: umożliwia także modelu wdrażania usługi Resource Manager można wdrożyć klaster maszyn wirtualnych z funkcją RDMA, który łączy się z siecią RDMA. Możesz [utworzyć własny szablon](../../../resource-group-authoring-templates.md), lub sprawdź [szablonów Szybki Start Azure](https://azure.microsoft.com/documentation/templates/) zamieszczone przez firmę Microsoft lub społeczności, aby wdrożyć rozwiązanie ma szablonów. Szablony Menedżera zasobów zapewniają szybki i niezawodny sposób wdrożyć klaster systemu Linux. Aby włączyć połączenia sieciowego RDMA, korzystając z modelu wdrażania usługi Resource Manager, należy wdrożyć maszyn wirtualnych w tym samym zestawie dostępności.
* **HPC Pack**: Tworzenie klastra Microsoft HPC Pack na platformie Azure i Dodaj węzły obliczeniowe z funkcją RDMA, systemem obsługiwanych dystrybucji systemu Linux na dostęp do sieci RDMA. Aby uzyskać więcej informacji, zobacz [wprowadzenie węzły obliczeniowe systemu Linux w klastrze HPC Pack na platformie Azure](hpcpack-cluster.md).

## <a name="sample-deployment-steps-in-the-classic-model"></a>Przykładowe wdrożenie kroki w klasycznym modelu.
Poniższe kroki pokazują, jak wdrożyć maszynę Wirtualną HPC z dodatkiem SP1 SUSE Linux Enterprise Server (SLES) 12 z portalu Azure Marketplace, dostosować go i utworzyć niestandardowy obraz maszyny Wirtualnej za pomocą wiersza polecenia platformy Azure. Następnie można użyć obrazu do skryptu wdrażania klastra maszyn wirtualnych z funkcją RDMA.

> [!TIP]
> Podobne kroki umożliwiają wdrożenie klastra maszyn wirtualnych z funkcją RDMA, oparte na podstawie CentOS HPC obrazów w portalu Azure Marketplace. Niektóre kroki różnić się nieznacznie, jak podano. 
>
>

### <a name="prerequisites"></a>Wymagania wstępne
* **Komputer kliencki**: wymagany jest komputer klienta Mac, Linux lub Windows do komunikowania się z platformą Azure. Tych krokach przyjęto założenie, że używasz klienta systemu Linux.
* **Subskrypcja platformy Azure**: Jeśli nie masz subskrypcji, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free/) w zaledwie kilka minut. W przypadku dużych klastrów rozważ z subskrypcji lub inne opcje zakupu.
* **Dostępność rozmiar maszyny Wirtualnej**: następujące rozmiary wystąpienia są funkcją RDMA: H16r, H16mr A8 i A9. Sprawdź [produkty, które są dostępne w regionie](https://azure.microsoft.com/regions/services/) dostępności w regionach platformy Azure.
* **Limit przydziału rdzeni**: może być konieczne zwiększyć limit przydziału rdzeni do wdrożenia klastrów obliczeniowych maszyn wirtualnych. Na przykład należy co najmniej 128 rdzeni. Jeśli chcesz wdrożyć 8 A9 maszyn wirtualnych, jak pokazano w tym artykule. Subskrypcją również może ograniczyć liczbę rdzeni, którą można wdrożyć w niektórych rodzin rozmiar maszyny Wirtualnej, w tym serię H. Aby zażądać zwiększenia limitu przydziału [otwarcia żądania pomocy technicznej online klienta](../../../azure-supportability/how-to-create-azure-support-request.md) bez dodatkowych opłat.
* **Azure CLI**: [zainstalować](../../../cli-install-nodejs.md) wiersza polecenia platformy Azure i [nawiązać połączenia z subskrypcją platformy Azure](/cli/azure/authenticate-azure-cli) z komputera klienckiego.

### <a name="provision-an-sles-12-sp1-hpc-vm"></a>Zapewnij SLES 12 SP1 HPC maszyny Wirtualnej
Po zalogowaniu się na platformie Azure przy użyciu wiersza polecenia platformy Azure, uruchom `azure config list` aby upewnić się, że dane wyjściowe zawierają tryb usługi zarządzania. Jeśli nie, należy ustawić tryb uruchamiając poniższe polecenie:

    azure config mode asm


Wpisz następujące polecenie, aby wyświetlić wszystkie subskrypcje, które są autoryzowane do używania:

    azure account list

Jest identyfikowane bieżącej subskrypcji active `Current` ustawioną `true`. Jeśli ta subskrypcja nie jest ten, który ma być używany do tworzenia klastra, Ustaw identyfikator subskrypcji odpowiednie jako aktywną subskrypcją:

    azure account set <subscription-Id>

Aby wyświetlić publicznie dostępnych obrazów SLES 12 SP1 HPC na platformie Azure, uruchom polecenie podobne do poniższych, zakładając, że obsługuje środowisko z powłoki **grep**:

    azure vm image list | grep "suse.*hpc"

Zapewnij z funkcją RDMA maszyny Wirtualnej z obrazu systemu SLES 12 SP1 HPC, uruchamiając polecenie podobne do poniższych:

    azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824

Gdzie:

* Rozmiar (A9 w tym przykładzie) jest jednym z rozmiary maszyny Wirtualnej z funkcją RDMA.
* Zewnętrzny numer portu SSH (22 w tym przykładzie, która jest domyślnie SSH) jest dowolny prawidłowy numer portu. Wewnętrzny numer portu SSH wynosi 22.
* Nową usługę w chmurze jest tworzony w regionie Azure, określony przez lokalizację. Określ lokalizację, w której znajduje się wybrany rozmiar maszyny Wirtualnej.
* Obsługa SUSE priorytet (która wiąże się z dodatkowych opłat) Nazwa obrazu z dodatkiem SP1 SLES 12 aktualnie może być jedną z tych dwóch opcji: 

 `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824`

  `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-priority-v20160824`


### <a name="customize-the-vm"></a>Dostosowywanie maszyny Wirtualnej
Gdy maszyna wirtualna zakończy, udostępniania SSH z maszyną Wirtualną za pomocą zewnętrznego adresu IP maszyny Wirtualnej (lub nazwa DNS) i portu zewnętrznego numeru skonfigurowany, a następnie dostosować go. Aby uzyskać informacje dotyczące połączenia, zobacz [jak zalogować się do maszyny wirtualnej z systemem Linux](../mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Przeprowadź poleceń użytkownika, które zostały skonfigurowane na Maszynie wirtualnej, chyba że do ukończenia krok jest wymagany dostęp do konta root.

> [!IMPORTANT]
> Microsoft Azure nie zapewnia dostęp do konta root do maszyn wirtualnych systemu Linux. Aby uzyskać dostęp administracyjny podczas połączenia jako użytkownik z maszyną wirtualną, Uruchom polecenia przy użyciu `sudo`.
>
>

* **Aktualizacje**: Zainstaluj aktualizacje za pomocą zypper. Można także zainstalować narzędzia systemu plików NFS.

  > [!IMPORTANT]
  > SLES 12 SP1 HPC maszynie wirtualnej zalecamy nie stosowanie aktualizacji jądra, które mogą powodować problemy z protokołem RDMA Linux sterowniki.
  >
  >
* **Intel MPI**: zakończenie instalacji MPI Intel na maszynie Wirtualnej HPC SLES 12 z dodatkiem SP1, uruchamiając następujące polecenie:

        sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
* **Zablokować pamięci**: kodów dla MPI zablokować dostępnej pamięci dla funkcji RDMA, dodać lub zmienić poniższe ustawienia w pliku /etc/security/limits.conf. Należy głównego dostępu do edytowania tego pliku.

    ```
    <User or group name> hard    memlock <memory required for your application in KB>

    <User or group name> soft    memlock <memory required for your application in KB>
    ```

  > [!NOTE]
  > Do celów testowych można również ustawić memlock na nieograniczony. Na przykład: `<User or group name>    hard    memlock unlimited`. Aby uzyskać więcej informacji, zobacz [znaną metody ustawienie zablokowane rozmiar pamięci](https://software.intel.com/en-us/blogs/2014/12/16/best-known-methods-for-setting-locked-memory-size).
  >
  >
* **Klucze SSH dla maszyn wirtualnych SLES**: klucze Generowanie SSH do ustanawiania relacji zaufania dla konta użytkownika między węzłami obliczeniowymi w SLES klastra podczas uruchamiania zadań MPI. Jeśli wdrożono maszynę Wirtualną na podstawie CentOS HPC, nie należy wykonać ten krok. Zobacz instrukcje w dalszej części tego artykułu można ustawić passwordless SSH relacji zaufania między węzłami klastra po przechwyceniu obrazu i wdrożenie klastra.

    Do tworzenia kluczy SSH, uruchom następujące polecenie. Po wyświetleniu monitu o wprowadzenie danych, wybierz **Enter** do generowania kluczy w domyślnej lokalizacji bez ustawienia hasła.

        ssh-keygen

    Dołącz klucz publiczny do pliku authorized_keys znane kluczy publicznych.

        cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

    W katalogu ~/.ssh edycji lub tworzenia pliku konfiguracji. Podaj zakres adresów IP w sieci prywatnej, które będą używane na platformie Azure (10.32.0.0/16 w tym przykładzie):

        host 10.32.0.*
        StrictHostKeyChecking no

    Alternatywnie listy adres IP sieci prywatnej każdej maszyny Wirtualnej w klastrze w następujący sposób:

    ```
    host 10.32.0.1
     StrictHostKeyChecking no
    host 10.32.0.2
     StrictHostKeyChecking no
    host 10.32.0.3
     StrictHostKeyChecking no
    ```

  > [!NOTE]
  > Konfigurowanie `StrictHostKeyChecking no` można utworzyć potencjalne zagrożenie dla bezpieczeństwa, jeśli nie określono określonego adresu IP lub zakresu.
  >
  >
* **Aplikacje**: Zainstaluj wszystkie aplikacje muszą lub wykonywać inne dostosowania, przed przechwyceniem obrazu.

### <a name="capture-the-image"></a>Przechwycenie obrazu
Aby przechwycić obraz, uruchom następujące polecenie w maszyny Wirtualnej systemu Linux. To polecenie deprovisions maszyny Wirtualnej, ale przechowuje konta użytkowników i kluczy SSH, które zostały zdefiniowane.

```
sudo waagent -deprovision
```

Z komputera klienta uruchom następujące polecenia interfejsu wiersza polecenia Azure, aby przechwycić obraz. Aby uzyskać więcej informacji, zobacz [Przechwytywanie klasycznej maszyny wirtualnej systemu Linux jako obrazu](capture-image-classic.md).  

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

Po uruchomieniu tych poleceń obrazu maszyny Wirtualnej są przechwytywane do użycia, a maszyna wirtualna jest usuwana. Masz teraz gotowy do wdrożenia klastra obraz niestandardowy.

### <a name="deploy-a-cluster-with-the-image"></a>Wdrażanie klastra przy użyciu obrazu
Zmodyfikuj następujący skrypt Bash z odpowiednimi wartościami dla środowiska i uruchom go z komputera klienckiego. Ponieważ Azure wdraża szeregowo w klasycznym modelu wdrażania maszyn wirtualnych, zajmuje kilka minut, aby wdrożyć osiem maszyn wirtualnych A9 sugerowane w tym skrypcie.

```
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a VNet in Azure
# Create a custom private network in Azure
# Replace 10.32.0.0 with your virtual network address space
# Replace <network-name> with your network identifier
# Replace "West US" with an Azure region where the VM size is available
# See Azure Pricing pages for prices and availability of compute-intensive VMs

azure network vnet create -l "West US" -e 10.32.0.0 -i 16 <network-name>

# Create a cloud service. All the compute-intensive instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
# Note: The current maximum number of VMs in a cloud service is 50. If you need to provision more than 50 VMs in the same cloud service in your cluster, contact Azure Support.

azure service create <cloud-service-name> --location "West US" –s <subscription-ID>

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Define a prefix for external port numbers. If you want to turn off external ports and use only internal ports to communicate between compute nodes via port 22, don’t use this option. Since port numbers up to 10000 are reserved, use numbers after 10000. Leave external port on for rank 0 and head node.

portnumber=101

# In this cluster there will be 8 size A9 nodes, named cluster11 to cluster18. Specify your captured image in <image-name>. Specify the username and password you used when creating the SSH keys.

for (( i=11; i<19; i++ )); do
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i -w <network-name> -b Subnet-1 <image-name>
done

# Save this script with a name like makecluster.sh and run it in your shell environment to provision your cluster
```

## <a name="considerations-for-a-centos-hpc-cluster"></a>Zagadnienia dotyczące klastra CentOS HPC
Aby skonfigurować klaster oparty na jednym z obrazów na podstawie CentOS HPC w portalu Azure Marketplace zamiast SLES 12 dla HPC, wykonaj ogólne kroki opisane w poprzedniej sekcji. Podczas udostępniania i konfigurowania maszyny Wirtualnej, należy uwzględnić następujące różnice:

- Intel MPI jest już zainstalowana na maszynie Wirtualnej z obrazu na podstawie CentOS HPC.
- Ustawienia pamięci blokady są już dodana w pliku /etc/security/limits.conf maszyny Wirtualnej.
- Nie generują kluczy SSH na maszynie Wirtualnej, należy udostępnić do przechwycenia. Zamiast tego zaleca się skonfigurowanie uwierzytelniania użytkownika po wdrożeniu klastra. Aby uzyskać więcej informacji zobacz sekcję poniżej.  

### <a name="set-up-passwordless-ssh-trust-on-the-cluster"></a>Konfigurowanie passwordless zaufania SSH w klastrze
W klastrze HPC na podstawie CentOS, istnieją dwie metody do ustanawiania relacji zaufania między węzły obliczeniowe: uwierzytelnianie oparte na hoście i uwierzytelnianie oparte na użytkowniku. Uwierzytelnianie oparte na hoście, wykracza poza zakres tego artykułu i zazwyczaj muszą być wykonywane przez rozszerzenia skryptów podczas wdrażania. Uwierzytelnianie użytkownika jest wygodną metodą ustanawiania relacji zaufania po wdrożeniu i wymaga tworzenia i udostępniania kluczy SSH między węzłami obliczeniowymi w klastrze. Ta metoda jest często nazywana passwordless logowania SSH i jest wymagany w przypadku uruchamiania zadań MPI.

Przykładowy skrypt zamieszczone przez społeczność jest dostępna w [GitHub](https://github.com/tanewill/utils/blob/master/user_authentication.sh) do włączenia uwierzytelniania użytkownika łatwy w klastrze HPC na podstawie CentOS. Pobranie i użycie tego skryptu za pomocą poniższych kroków. Można również zmodyfikować tego skryptu lub użyć innej metody do ustanawiania passwordless uwierzytelniania SSH między węzłami klastra obliczeniowego.

    wget https://raw.githubusercontent.com/tanewill/utils/master/user_authentication.sh

Aby uruchomić skrypt, musisz znać prefiks podsieci adresów IP. Pobierz prefiks, uruchamiając następujące polecenie w jednym z węzłów klastra. Dane wyjściowe powinny wyglądać podobnie 10.1.3.5 i prefiks jest 10.1.3 części.

    ifconfig eth0 | grep -w inet | awk '{print $2}'

Teraz uruchom skrypt przy użyciu trzech parametrów: Nazwa pospolita użytkownika w węzłach obliczeniowych, wspólnego hasła dla tego użytkownika w węzłach obliczeniowych i prefiks podsieci zwrócony przez poprzednie polecenie.

    ./user_authentication.sh <myusername> <mypassword> 10.1.3

Ten skrypt wykonuje następujące czynności:

* Tworzy katalog o nazwie .ssh, co jest niezbędne do logowania passwordless węzła hosta.
* Tworzy plik konfiguracji w katalogu .ssh, która sprawia, że passwordless logowania, aby umożliwić logowanie z dowolnego węzła w klastrze.
* Tworzy pliki zawierające nazwy węzła i węzła adresów IP dla wszystkich węzłów w klastrze. Te pliki są pozostawiane po uruchomieniu skryptu do wykorzystania w późniejszym czasie.
* Tworzy prywatne i publiczne pary kluczy dla każdego węzła klastra (w tym węzła hosta) oraz wpisy w pliku authorized_keys.

> [!WARNING]
> Uruchomienie tego skryptu można utworzyć potencjalne zagrożenie dla bezpieczeństwa. Upewnij się, że informacje o kluczu publicznym w ~/.ssh nie będzie on rozpowszechniany.
>
>

## <a name="configure-intel-mpi"></a>Skonfiguruj Intel MPI
Aby uruchomić aplikacje MPI RDMA systemu Linux platformy Azure, należy skonfigurować niektóre zmienne środowiskowe specyficzne dla firmy Intel MPI. Poniżej przedstawiono przykładowy skrypt Bash, aby skonfigurować zmienne potrzebne do uruchomienia aplikacji. Zmień ścieżkę, aby mpivars.sh zgodnie z potrzebami dla tej instalacji Intel MPI.

```
#!/bin/bash -x

# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh

export I_MPI_FABRICS=shm:dapl

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB
# Setting the variable to shm:dapl gives best performance for some applications
# If your application doesn’t take advantage of shared memory and MPI together, then set only dapl

export I_MPI_DAPL_PROVIDER=ofa-v2-ib0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

export I_MPI_DYNAMIC_CONNECTION=0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

# Command line to run the job

mpirun -n <number-of-cores> -ppn <core-per-node> -hostfile <hostfilename>  /path <path to the application exe> <arguments specific to the application>

#end
```

Format pliku hosta ma następującą składnię. Dodaj jeden wiersz dla każdego węzła w klastrze. Określ prywatnych adresów IP z sieci wirtualnej nie zostały zdefiniowane wcześniej, a nie nazw DNS. Na przykład dla dwa hosty z adresami IP 10.32.0.1 i 10.32.0.2, plik zawiera następujące czynności:

```
10.32.0.1:16
10.32.0.2:16
```

## <a name="run-mpi-on-a-basic-two-node-cluster"></a>Uruchom MPI na podstawowe dwa węzły klastra
Jeśli jeszcze tego nie zrobiono, najpierw skonfigurować środowisko dla Intel MPI.

```
# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh
```

### <a name="run-an-mpi-command"></a>Uruchom polecenie MPI
Uruchom polecenie MPI na jednym z węzłów obliczeniowych do wskazują, że MPI jest poprawnie zainstalowany i może komunikować się między co najmniej się, że dwa węzły obliczeniowe. Następujące **mpirun** polecenia **hostname** na dwa węzły.

```
mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
Dane wyjściowe powinny zawierać nazw wszystkie węzły, które przekazany jako dane wejściowe dla `-hosts`. Na przykład **mpirun** polecenie z dwoma węzłami zwraca dane wyjściowe podobne do następujących:

```
cluster11
cluster12
```

### <a name="run-an-mpi-benchmark"></a>Uruchamianie testów porównawczych MPI
Następujące polecenie Intel MPI uruchamia testu pingpong, aby sprawdzić konfigurację klastra i połączenie z siecią RDMA.

```
mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 IMB-MPI1 pingpong
```

W klastrze pracy z dwoma węzłami powinny zostać wyświetlone dane wyjściowe podobne do następujących. W sieci Azure RDMA oczekuje się, że czas oczekiwania na poziomie lub poniżej 3 mikrosekundach wiadomości rozmiary maksymalnie 512 bajtów.

```
#------------------------------------------------------------
#    Intel (R) MPI Benchmarks 4.0 Update 1, MPI-1 part
#------------------------------------------------------------
# Date                  : Fri Jul 17 23:16:46 2015
# Machine               : x86_64
# System                : Linux
# Release               : 3.12.39-44-default
# Version               : #5 SMP Thu Jun 25 22:45:24 UTC 2015
# MPI Version           : 3.0
# MPI Thread Environment:
# New default behavior from Version 3.2 on:
# the number of iterations per message size is cut down
# dynamically when a certain run time (per message size sample)
# is expected to be exceeded. Time limit is defined by variable
# "SECS_PER_SAMPLE" (=> IMB_settings.h)
# or through the flag => -time

# Calling sequence was:
# /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
# Minimum message length in bytes:   0
# Maximum message length in bytes:   4194304
#
# MPI_Datatype                   :   MPI_BYTE
# MPI_Datatype for reductions    :   MPI_FLOAT
# MPI_Op                         :   MPI_SUM
#
#
# List of Benchmarks to run:
# PingPong
#---------------------------------------------------
# Benchmarking PingPong
# #processes = 2
#---------------------------------------------------
       #bytes #repetitions      t[usec]   Mbytes/sec
            0         1000         2.23         0.00
            1         1000         2.26         0.42
            2         1000         2.26         0.85
            4         1000         2.26         1.69
            8         1000         2.26         3.38
           16         1000         2.36         6.45
           32         1000         2.57        11.89
           64         1000         2.36        25.81
          128         1000         2.64        46.19
          256         1000         2.73        89.30
          512         1000         3.09       157.99
         1024         1000         3.60       271.53
         2048         1000         4.46       437.57
         4096         1000         6.11       639.23
         8192         1000         7.49      1043.47
        16384         1000         9.76      1600.76
        32768         1000        14.98      2085.77
        65536          640        25.99      2405.08
       131072          320        50.68      2466.64
       262144          160        80.62      3101.01
       524288           80       145.86      3427.91
      1048576           40       279.06      3583.42
      2097152           20       543.37      3680.71
      4194304           10      1082.94      3693.63

# All processes entering MPI_Finalize

```



## <a name="next-steps"></a>Kolejne kroki
* Wdrażanie i uruchamianie sieci MPI Linux aplikacji w klastrze systemu Linux.
* Zobacz [dokumentację biblioteki MPI Intel](https://software.intel.com/en-us/articles/intel-mpi-library-documentation/) wskazówki dotyczące Intel MPI.
* Spróbuj [szablon szybkiego startu](https://github.com/Azure/azure-quickstart-templates/tree/master/intel-lustre-clients-on-centos) do utworzenia klastra połysk Intel przy użyciu obrazu na podstawie CentOS HPC. Aby uzyskać więcej informacji, zobacz [wdrażanie Intel chmury Edition dla połysk w systemie Microsoft Azure](https://blogs.msdn.microsoft.com/arsen/2015/10/29/deploying-intel-cloud-edition-for-lustre-on-microsoft-azure/).
