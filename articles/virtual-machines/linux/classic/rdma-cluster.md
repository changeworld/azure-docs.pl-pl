---
title: Konfigurowanie klastra RDMA systemu Linux do uruchamiania aplikacji MPI | Dokumentacja firmy Microsoft
description: Tworzenie klastra systemu Linux o rozmiarze H16r, H16mr, A8 i A9 maszyn wirtualnych, aby użyć sieci RDMA platformy Azure do uruchamiania aplikacji MPI
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
ms.date: 07/11/2018
ms.author: danlep
ms.openlocfilehash: 471fd4095fe45e76f94df8c61a07eeb9bbc1c120
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990731"
---
# <a name="set-up-a-linux-rdma-cluster-to-run-mpi-applications"></a>Konfigurowanie klastra RDMA systemu Linux na potrzeby uruchamiania aplikacji MPI

Informacje o sposobie konfigurowania klastra RDMA systemu Linux na platformie Azure przy użyciu [o wysokiej wydajności obliczeń rozmiarów maszyn wirtualnych](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) do uruchamiania aplikacji równoległych interfejsu przekazywania komunikatów (MPI). W tym artykule przedstawiono kroki, aby przygotować obraz HPC systemu Linux, aby uruchomić Intel MPI w klastrze. Po przygotowaniu wdrażania klastra maszyn wirtualnych przy użyciu tego obrazu, a jeden z [rozmiary maszyn wirtualnych Azure obsługą dostępu RDMA](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#rdma-capable-instances). Jest używany klaster do uruchamiania aplikacji MPI, które efektywnie komunikacji za pośrednictwem sieci o małych opóźnieniach i dużej przepływności oparciu o technologię zdalnego bezpośredniego pamięci (RDMA) dostępu.

> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [usługi Azure Resource Manager](../../../azure-resource-manager/resource-manager-deployment-model.md) i model klasyczny. Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.

## <a name="cluster-deployment-options"></a>Opcje wdrażania klastra
Poniżej przedstawiono różne metody, można użyć do utworzenia klastra RDMA systemu Linux z użyciem lub bez harmonogramu zadań.

* **Skryptami interfejsu wiersza polecenia platformy Azure**: jak pokazano w dalszej części tego artykułu, należy użyć [interfejsu wiersza polecenia platformy Azure](../../../cli-install-nodejs.md) (CLI), aby utworzyć skrypt wdrażania klastra maszyn wirtualnych z funkcją RDMA. Interfejs wiersza polecenia w trybie zarządzania usługami szeregowo tworzy węzłów klastra w klasycznym modelu wdrażania, więc wdrażania wielu węzłów obliczeniowych może potrwać kilka minut. Aby włączyć połączenia sieciowego RDMA, korzystając z klasycznego modelu wdrażania, należy wdrożyć maszyny wirtualne w tej samej usłudze w chmurze.
* **Szablony usługi Azure Resource Manager**: umożliwia także modelu wdrażania usługi Resource Manager do wdrażania klastra maszyn wirtualnych z funkcją RDMA, który nawiązuje połączenie z sieci RDMA. Możesz [utworzyć własny szablon](../../../resource-group-authoring-templates.md), lub sprawdź [szablony szybkiego startu platformy](https://azure.microsoft.com/documentation/templates/) dla szablonów zamieszczone przez firmę Microsoft lub społeczność, aby wdrożyć rozwiązanie, które ma. Szablony usługi Resource Manager zapewnia to szybki i niezawodny sposób wdrażania klastra systemu Linux. Aby włączyć połączenia sieciowego RDMA, korzystając z modelu wdrażania usługi Resource Manager, należy wdrożyć maszyny wirtualne w tym samym zestawie dostępności lub zestawie skalowania maszyn wirtualnych.
* **Pakiet HPC Pack**: Tworzenie klastra Microsoft HPC Pack na platformie Azure i dodać obsługą RDMA węzłów obliczeniowych z systemem obsługiwanych dystrybucji systemu Linux, uzyskanie dostępu do sieci RDMA. Aby uzyskać więcej informacji, zobacz [wprowadzenie do węzłów obliczeniowych systemu Linux w klastrze pakietu HPC Pack na platformie Azure](hpcpack-cluster.md).

## <a name="sample-deployment-steps-in-the-classic-model"></a>Przykładowe wdrożenie kroki w klasycznym modelu
Poniższe kroki pokazują, jak wdrożyć maszynę Wirtualną HPC SUSE Linux Enterprise Server (SLES) 12 w portalu Azure Marketplace, dostosować go i tworzenie niestandardowego obrazu maszyny Wirtualnej za pomocą wiersza polecenia platformy Azure. Następnie można użyć obrazu do skryptu wdrażania klastra maszyn wirtualnych z funkcją RDMA.

> [!TIP]
> Należy użyć podobnych kroków, aby wdrożyć klaster z funkcją RDMA maszyn wirtualnych opartych na obrazach opartych na systemie CentOS HPC w witrynie Azure Marketplace. Niektóre kroki różnić się nieznacznie, zgodnie z informacjami.
>

### <a name="prerequisites"></a>Wymagania wstępne
* **Komputer kliencki**: wymagany jest komputer klienta Mac, Linux lub Windows do komunikowania się z platformą Azure. Te czynności zakładają, że używasz klienta systemu Linux.
* **Subskrypcja platformy Azure**: Jeśli nie masz subskrypcji, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free/) w zaledwie kilka minut. W przypadku większych klastrów należy wziąć pod uwagę subskrypcji zgodnie z rzeczywistym użyciem lub inne opcje zakupu.
* **Dostępność rozmiar maszyny Wirtualnej**: Sprawdź [dostępność produktów według regionów](https://azure.microsoft.com/regions/services/) dostępność serii H lub inne rozmiary maszyn wirtualnych funkcją RDMA, w regionach platformy Azure.
* **Limit przydziału rdzeni**: może być konieczne zwiększyć limit przydziału rdzeni, aby wdrożyć klaster maszyn wirtualnych intensywnie korzystających z obliczeń. Na przykład potrzebujesz co najmniej 128 rdzeni, aby wdrożyć 8 A9 maszyn wirtualnych, jak pokazano w tym artykule. Twoja subskrypcja może także ograniczać liczbę rdzeni, które można wdrożyć w maszynie Wirtualnej rodzinach o określonym rozmiarze, łącznie z serii h. Aby zażądać zwiększenia limitu przydziału [Otwórz żądanie obsługi klienta online](../../../azure-supportability/how-to-create-azure-support-request.md) bez dodatkowych opłat.
* **Interfejs wiersza polecenia Azure**: [zainstalować](../../../cli-install-nodejs.md) wiersza polecenia platformy Azure i [nawiązać połączenie z subskrypcją platformy Azure](/cli/azure/authenticate-azure-cli) z komputera klienckiego.

Sprawdź również kwestie dotyczące wdrażania [rozmiary maszyn wirtualnych Azure obsługą dostępu RDMA](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#rdma-capable-instances).

### <a name="provision-an-sles-12-hpc-vm"></a>Aprowizowanie maszyny Wirtualnej systemu SLES 12 HPC
Po zalogowaniu się do platformy Azure przy użyciu wiersza polecenia platformy Azure, uruchom `azure config list` aby upewnić się, że dane wyjściowe pokazują trybie zarządzania usługami. Jeśli nie, należy ustawić tryb, uruchamiając następujące polecenie:

    azure config mode asm


Wpisz następujące polecenie, aby wyświetlić listę wszystkich subskrypcji, które są autoryzowane do używania:

    azure account list

Bieżący aktywną subskrypcję jest identyfikowany za pomocą `Current` równa `true`. Jeśli ta subskrypcja nie jest ten, którego chcesz użyć do utworzenia klastra, należy ustawić identyfikator odpowiednią subskrypcję, jako aktywną subskrypcję:

    azure account set <subscription-Id>

Aby wyświetlić publicznie dostępnych obrazów SLES 12 HPC na platformie Azure, uruchom polecenie podobne do następujących, zakładając, że Twoje obsługuje środowisko powłoki **grep**:

    azure vm image list | grep "suse.*hpc"

Zapewnij obsługą dostępu RDMA maszyn wirtualnych z obrazów SLES 12 z dodatkiem SP3 HPC, uruchamiając polecenie podobne do następującego:

    azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp3-hpc-v20170913

Gdzie:

* Rozmiar (A9 w tym przykładzie) jest jednym z obsługą dostępu RDMA rozmiarach.
* Zewnętrzny numer portu SSH (22, w tym przykładzie, co jest ustawieniem domyślnym SSH) jest dowolny prawidłowy numer portu. Wewnętrzny numer portu SSH jest równa 22.
* Nową usługę w chmurze jest utworzona w regionie platformy Azure, określone przez lokalizację. Określ lokalizację, w którym jest dostępne, takich jak "Zachodnie stany USA" rozmiar maszyny Wirtualnej, którą wybierzesz.
* SUSE priorytetowa pomoc techniczna (co spowoduje naliczenie dodatkowych opłat), aby uzyskać nazwę obrazu systemu SLES 12. obecnie może być jedną z opcji, które ma `priority` nazwę, takich jak: 

 `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp3-hpc-priority-v20170913`

### <a name="customize-the-vm"></a>Dostosowywanie maszyny Wirtualnej
Po maszyny Wirtualnej zakończy się inicjowanie obsługi administracyjnej SSH z maszyną Wirtualną za pomocą zewnętrznego adresu IP maszyny Wirtualnej (lub nazwę DNS) i porcie zewnętrznym skonfigurowany numer, a następnie ją Dostosuj. Aby uzyskać informacje dotyczące połączenia, zobacz [jak zalogować się do maszyny wirtualnej z systemem Linux](../mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Wykonywanie poleceń jako użytkownik, które zostały skonfigurowane na maszynie Wirtualnej, chyba że dostęp na poziomie administratora jest wymagana do ukończenia kroku.

> [!IMPORTANT]
> Microsoft Azure nie zapewnia dostęp na poziomie administratora do maszyn wirtualnych systemu Linux. Aby uzyskać administracyjny dostęp podczas połączenia jako użytkownik z maszyną wirtualną, Uruchom polecenia przy użyciu `sudo`.
>
>

* **Aktualizacje**: Instalowanie aktualizacji za pomocą zypper. Można także zainstalować narzędzia systemu plików NFS.

  > [!IMPORTANT]
  > W przypadku systemu SLES 12 HPC maszyny Wirtualnej zaleca się, że nie mają zastosowania aktualizacji jądra, które mogą powodować problemy RDMA systemu Linux sterowniki.
  >
  >
* **Intel MPI**: ukończenie instalacji Intel MPI na maszynie Wirtualnej, uruchamiając następujące polecenie:

    ```bash
    sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
    ```

* **Zablokować pamięci**: kodów dla MPI zablokować pamięci dostępnej dla funkcji RDMA, dodać lub zmienić następujące ustawienia w pliku /etc/security/limits.conf. Potrzebny jest dostęp głównego, aby edytować ten plik.

    ```bash
    <User or group name> hard    memlock <memory required for your application in KB>

    <User or group name> soft    memlock <memory required for your application in KB>
    ```

  > [!NOTE]
  > Do celów testowych można również ustawić memlock na nieograniczoną. Na przykład: `* hard memlock unlimited`. Aby uzyskać więcej informacji, zobacz [znanych najlepszych metod dla ustawienia zablokowane pamięci o rozmiarze](https://software.intel.com/en-us/blogs/2014/12/16/best-known-methods-for-setting-locked-memory-size).
  >
  >
* **Klucze SSH dla maszyn wirtualnych z systemem SLES**: klucze Generowanie SSH do ustanawiania relacji zaufania dla konta użytkownika między węzły obliczeniowe w systemie SLES klastra podczas uruchamiania zadań MPI. Jeśli wdrożono Maszynę wirtualną HPC opartych na systemie CentOS, nie wykonaj ten krok. Zobacz instrukcje w dalszej części tego artykułu, aby skonfigurować bez hasła SSH relacji zaufania między węzłami klastra, po przechwyceniu obrazu i wdrożenie klastra.

  Aby utworzyć klucze SSH, uruchom `ssh-keygen` polecenia. Po wyświetleniu monitu o wprowadzenie danych, wybierz **Enter** do generowania kluczy w lokalizacji domyślnej, bez konieczności ustawiania hasła.

  Dołącz klucz publiczny do pliku authorized_keys znanych kluczy publicznych.

  ```bash
  cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
  ```

  W katalogu ~/.ssh, edytować lub utworzyć `config` pliku. Podaj zakres adresów IP w sieci prywatnej, którego chcesz użyć na platformie Azure (10.32.0.0/16 w tym przykładzie):

  ```bash
  host 10.32.0.*
  StrictHostKeyChecking no
  ```

  Alternatywnie listy adresu IP w sieci prywatnej każdej maszyny wirtualnej w klastrze w następujący sposób:

  ```bash
  host 10.32.0.1
  StrictHostKeyChecking no
  host 10.32.0.2
  StrictHostKeyChecking no
  host 10.32.0.3
  StrictHostKeyChecking no
  ...
  ```

  > [!NOTE]
  > Konfigurowanie `StrictHostKeyChecking no` można utworzyć potencjalne zagrożenie bezpieczeństwa, jeśli nie określono określonego adresu IP lub zakresu.
  >
  >
* **Aplikacje**: Zainstaluj wszystkie aplikacje muszą lub wykonywania innych dostosowań przed przechwyceniem obrazu.

### <a name="capture-the-image"></a>Przechwycenie obrazu
Aby przechwycić obraz, należy najpierw uruchomić następujące polecenie na maszynie Wirtualnej systemu Linux. To polecenie deprovisions maszyny Wirtualnej, ale obsługuje kont użytkowników i klucze SSH, które zostały zdefiniowane.

```bash
sudo waagent -deprovision
```

Na komputerze klienckim uruchom następujące polecenia wiersza polecenia platformy Azure w celu przechwycenia obrazu. Aby uzyskać więcej informacji, zobacz [Przechwytywanie klasycznej maszyny wirtualnej systemu Linux jako obraz](capture-image-classic.md).  

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

Po uruchomieniu tych poleceń obrazu maszyny Wirtualnej są przechwytywane do użycia, a maszyna wirtualna zostanie usunięty. Masz teraz gotowe do wdrożenia klastra obraz niestandardowy.

### <a name="deploy-a-cluster-with-the-image"></a>Wdrażanie klastra przy użyciu obrazu
Modyfikowanie poniższego skryptu powłoki systemowej odpowiednimi wartościami dla danego środowiska, a następnie uruchom go z komputera klienckiego. Ponieważ platforma Azure wdroży maszyny wirtualne pojedynczo w klasycznym modelu wdrażania, zajmuje kilka minut, aby wdrożyć osiem maszyn wirtualnych A9 sugerowane w tym skrypcie.

```bash
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a classic VNet in Azure
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
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i -w <network-name> -b Subnet-1 <image-name>;
done

# Save this script with a name like makecluster.sh and run it in your shell environment to provision your cluster
```

## <a name="considerations-for-a-centos-hpc-cluster"></a>Zagadnienia dotyczące klastra CentOS HPC
Jeśli chcesz skonfigurować klaster oparty na jeden z obrazów opartych na systemie CentOS HPC w witrynie Azure Marketplace, zamiast systemu SLES 12 dla HPC, wykonaj ogólne kroki opisane w poprzedniej sekcji. Podczas aprowizacji i konfigurowania maszyny Wirtualnej, zwróć uwagę na poniższe różnice:

- Intel MPI jest już zainstalowana na maszynie Wirtualnej z obrazu systemu CentOS HPC.
- Zablokuj ustawienia pamięci zostały już dodane do pliku /etc/security/limits.conf maszyny Wirtualnej.
- Generuje klucze SSH na maszynie Wirtualnej, możesz aprowizować do przechwycenia. Zamiast tego zaleca się skonfigurowanie uwierzytelniania użytkowników po wdrożeniu klastra. Aby uzyskać więcej informacji zobacz następującą sekcję.  

### <a name="set-up-passwordless-ssh-trust-on-the-cluster"></a>Bez hasła SSH relacji zaufania w klastrze
W klastrze HPC opartych na systemie CentOS, istnieją dwie metody do ustanawiania relacji zaufania między węzłami obliczeniowymi: uwierzytelnianie oparte na hoście i uwierzytelnianie oparte na użytkownikach. Uwierzytelnianie oparte na hoście znajduje się poza zakres tego artykułu i ogólnie musi odbywać się przy użyciu rozszerzenia skryptów podczas wdrażania. Uwierzytelnianie oparte na użytkowniku jest wygodne w przypadku ustanawiania relacji zaufania, po wdrożeniu i wymaga generowania i udostępniania kluczy SSH wśród węzłów obliczeniowych w klastrze. Ta metoda jest często nazywana logowania bez hasła SSH i jest wymagany w przypadku uruchamiania zadań MPI.

Przykładowy skrypt `user_authentication.sh` Aby włączyć uwierzytelnianie oparte na użytkownikach na HPC z systemem CentOS następuje klastra:

```bash
#!/bin/bash
# For CentOS user must first install epel-release, sshpass, and nmap (sshpass and nmap are available from epel-release for CentOS)

# usage ./user_authentication.sh [username] [password] [internalIP prefix]
# ./user_authentication.sh azureuser Azure@123 10.32.0
USER=$1
PASS=$2
IPPRE=$3
HEADNODE=`hostname`

mkdir -p .ssh
echo -e  'y\n' | ssh-keygen -f .ssh/id_rsa -t rsa -N ''

echo 'Host *' >> .ssh/config
echo 'StrictHostKeyChecking no' >> .ssh/config
chmod 400 .ssh/config
chown azureuser:azureuser /home/azureuser/.ssh/config

nmap -sn $IPPRE.* | grep $IPPRE. | awk '{print $5}' > nodeips.txt
for NAME in `cat nodeips.txt`; do sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME 'hostname' >> nodenames.txt;done

NAMES=`cat nodenames.txt` #names from names.txt file
for NAME in $NAMES; do
    sshpass -p $PASS scp -o "StrictHostKeyChecking no" -o ConnectTimeout=2 /home/$USER/nodenames.txt $USER@$NAME:/home/$USER/
    sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME "mkdir .ssh && chmod 700 .ssh"
    sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME "echo -e  'y\n' | ssh-keygen -f .ssh/id_rsa -t rsa -N ''"
    sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME 'touch /home/'$USER'/.ssh/config'
    sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME 'echo "Host *" >  /home/'$USER'/.ssh/config'
    sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME 'echo StrictHostKeyChecking no >> /home/'$USER'/.ssh/config'
    sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME 'chmod 400 /home/'$USER'/.ssh/config'
    cat .ssh/id_rsa.pub | sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME 'cat >> .ssh/authorized_keys'
    sshpass -p $PASS scp -o "StrictHostKeyChecking no" -o ConnectTimeout=2 $USER@$NAME:/home/$USER/.ssh/id_rsa.pub .ssh/sub_node.pub

    for SUBNODE in `cat nodeips.txt`; do
         sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$SUBNODE 'mkdir -p .ssh'
         cat .ssh/sub_node.pub | sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$SUBNODE 'cat >> .ssh/authorized_keys'
    done
    sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME 'chmod 700 .ssh/'
    sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME 'chmod 640 .ssh/authorized_keys'
done
```

Uruchom ten skrypt za pomocą poniższej procedury. Możesz również zmodyfikować ten skrypt lub użyć innej metody do ustanowienia bez hasła uwierzytelniania SSH między węzłami klastra obliczeniowego.

Aby uruchomić skrypt, musisz wiedzieć prefiks dla swoich adresów IP podsieci. Uzyskaj prefiksu, uruchamiając następujące polecenie w jednym z węzłów klastra. Dane wyjściowe powinny wyglądać 10.1.3.5 i prefiks jest 10.1.3 części.

```bash
ifconfig eth0 | grep -w inet | awk '{print $2}'
```

Teraz uruchom skrypt z użyciem trzy parametry: Nazwa pospolita użytkownika w węzłach obliczeniowych, wspólnego hasła dla tego użytkownika w węzłach obliczeniowych i prefiks podsieci, który został zwrócony z poprzedniego polecenia.

```bash
./user_authentication.sh <myusername> <mypassword> 10.1.3
```

Ten skrypt wykonuje następujące czynności:

* Tworzy katalog, w węźle hosta o nazwie SSH, co jest wymagane do logowania bez hasła.
* Tworzy plik konfiguracji w katalogu SSH, który powoduje, że logowanie bez hasła, umożliwia logowanie z dowolnego węzła w klastrze.
* Tworzy pliki zawierające nazwy węzła, węzeł adresów IP dla wszystkich węzłów w klastrze. Te pliki są pozostawiane po uruchomieniu skryptu do późniejszego wykorzystania.
* Tworzy parę prywatnych i publicznych kluczy dla każdego węzła klastra (w tym węzła hosta) oraz wpisy w pliku authorized_keys.

> [!WARNING]
> Uruchomienie tego skryptu można utworzyć potencjalne zagrożenie bezpieczeństwa. Upewnij się, że informacje o kluczu publicznym w ~/.ssh nie będzie on rozpowszechniany.
>

## <a name="run-mpi-on-a-basic-two-node-cluster"></a>Uruchamianie MPI na podstawowe dwa węzły klastra
Jeśli nie zostało to jeszcze zrobione, najpierw skonfigurować środowisko dla Intel MPI.

```bash
# For a SLES 12 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh
```

### <a name="run-an-mpi-command"></a>Uruchom polecenie MPI
Uruchom polecenie MPI na jednym z węzłów obliczeniowych, aby pokazać, że MPI jest poprawnie zainstalowany i może komunikować się między co najmniej że dwóch węzłów obliczeniowych. Następujące **mpirun** polecenia **hostname** polecenie z uwzględnieniem dwóch węzłów. Aby uzyskać `-hosts` parametr przekazywania adresów IP z uwzględnieniem dwóch węzłów w sieci wirtualnej platformy Azure (na przykład 10.32.0.4,10.32.0.5).

```bash
mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
Dane wyjściowe powinny wyświetlić listę nazw wszystkie węzły, które są przekazywane jako dane wejściowe dla `-hosts`. Na przykład **mpirun** polecenie z dwoma węzłami zwraca dane wyjściowe podobne do następujących:

```
cluster11
cluster12
```

### <a name="run-an-mpi-benchmark"></a>Uruchamianie testów porównawczych MPI
Następujące polecenie Intel MPI uruchamia porównawczych pingpong, aby sprawdzić konfigurację klastra i połączenia z siecią RDMA.

```bash
mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 IMB-MPI1 pingpong
```

W klastrze pracy z dwoma węzłami powinny być widoczne dane wyjściowe podobne do następujących. W sieci Azure RDMA spodziewać się, że opóźnienie lub poniżej 3 mikrosekund komunikatu o rozmiarach maksymalnie 512 bajtów.

```
#------------------------------------------------------------
#    Intel (R) MPI Benchmarks 4.0 Update 1, MPI-1 part
#------------------------------------------------------------
# Date                  : Fri Jul 6 17:16:46 2018
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
### <a name="sample-mpi-run-script"></a>Przykładowe MPI, uruchom skrypt
Poniżej przedstawiono przykładowy skrypt powłoki Bash, aby skonfigurować zmienne wymagane do uruchamiania aplikacji MPI. Zmień ścieżkę, tak aby `mpivars.sh` zgodnie z potrzebami dla Twojej instalacji Intel MPI.

```bash
#!/bin/bash -x

# For a SLES 12 HPC cluster

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

# Command line to run the MPI job. Substitute with values appropriate for your application.

mpirun -n <number-of-cores> -ppn <core-per-node> -hostfile <hostfilename>  /path <path to the application exe> <arguments specific to the application>

#end
```

Format pliku hosta jest w następujący sposób. Dodaj jeden wiersz dla każdego węzła w klastrze. Określ prywatnych adresów IP z sieci wirtualnej nie zostały zdefiniowane wcześniej, nie nazw DNS. Na przykład dla dwa hosty z adresami IP 10.32.0.4 i 10.32.0.4, plik zawiera następujące czynności:

```bash
10.32.0.4:16
10.32.0.5:16
```


## <a name="next-steps"></a>Kolejne kroki
* Wdrażanie i uruchamianie aplikacji MPI Twojego systemu Linux w klastrze systemu Linux.
* Zobacz [dokumentację Intel MPI biblioteki](https://software.intel.com/en-us/articles/intel-mpi-library-documentation/) wskazówki dotyczące Intel MPI.
* Spróbuj [szablon szybkiego startu](https://github.com/Azure/azure-quickstart-templates/tree/master/intel-lustre-clients-on-centos) do utworzenia klastra Intel Lustre przy użyciu obrazu systemu CentOS HPC. 
