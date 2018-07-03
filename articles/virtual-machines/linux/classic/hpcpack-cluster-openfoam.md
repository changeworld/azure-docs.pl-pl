---
title: Uruchamianie oprogramowania OpenFOAM przy użyciu pakietu HPC Pack na maszynach wirtualnych z systemem Linux | Dokumentacja firmy Microsoft
description: Wdrażanie klastra Microsoft HPC Pack na platformie Azure i uruchamiać zadanie OpenFOAM na wielu węzłach obliczeniowych systemu Linux w sieci RDMA.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: c0bb1637-bb19-48f1-adaa-491808d3441f
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 07/22/2016
ms.author: danlep
ms.openlocfilehash: 73ad78fc73a7605f8feaf114ebdfac5023cc91b6
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2018
ms.locfileid: "37342429"
---
# <a name="run-openfoam-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>Uruchamianie oprogramowania OpenFoam przy użyciu pakietu Microsoft HPC w węzłach RDMA systemu Linux na platformie Azure
W tym artykule przedstawiono sposób uruchamianie oprogramowania OpenFoam maszynach wirtualnych platformy Azure. W tym miejscu wdrożenia klastra Microsoft HPC Pack z węzłów obliczeniowych systemu Linux na platformie Azure i uruchom [OpenFoam](http://openfoam.com/) zadania za pomocą Intel MPI. Tak, aby węzły obliczeniowe komunikują się za pośrednictwem sieci platformy Azure RDMA, można użyć obsługą dostępu RDMA maszyn wirtualnych platformy Azure dla węzłów obliczeniowych. Inne opcje uruchamiania programu OpenFoam na platformie Azure obejmują w pełni skonfigurowany handlowej obrazów dostępnych w witrynie Marketplace, takie jak jego UberCloud [2.3 OpenFoam CentOS 6](https://azuremarketplace.microsoft.com/marketplace/apps/cfd-direct.cfd-direct-from-the-cloud)i uruchamiając [usługi Azure Batch](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/). 

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

OpenFOAM (dla operacji otwierania pola i manipulowania) to pakiet oprogramowania typu open-source analizach obliczeniowych dynamiki płynów (CFD), który jest powszechnie używany w inżynierii i analizy w organizacji komercyjnych i akademickich. Obejmuje narzędzia dla meshing przede wszystkim snappyHexMesh mesher równoległego, złożonych geometrii CAD oraz wstępnej i przetwarzania końcowego. Prawie wszystkie procesy są uruchamiane równolegle, umożliwiając użytkownikom umożliwiają pełne wykorzystywanie zalet sprzęt komputerowy dysponują.  

Pakiet Microsoft HPC Pack udostępnia funkcje do uruchamiania na dużą skalę HPC i działających równolegle aplikacji, w tym aplikacje MPI, w klastrach maszyn wirtualnych Microsoft Azure. Pakiet HPC Pack obsługuje również uruchomione HPC systemu Linux, aplikacji w systemie Linux obliczeniowych maszyn wirtualnych wdrożonych w klastrze pakietu HPC Pack węzła. Zobacz [wprowadzenie do węzłów obliczeniowych systemu Linux w klastrze pakietu HPC Pack na platformie Azure](hpcpack-cluster.md) węzłów za pomocą pakietu HPC Pack obliczeniowych — wprowadzenie do korzystania z systemu Linux.

> [!NOTE]
> W tym artykule przedstawiono sposób uruchamiania obciążenia MPI systemu Linux za pomocą pakietu HPC Pack. Założono, że masz kilka znajomość z Administracja systemu Linux i uruchamianie obciążeń MPI w klastrach systemu Linux. Jeśli używasz wersji MPI i OpenFOAM różni się od nich przedstawione w tym artykule, trzeba będzie zmodyfikować kilka kroków instalacji i konfiguracji. 
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
* **Węzły obliczeniowe w klastrze pakietu HPC Pack z obsługą dostępu RDMA systemu Linux** — wdrażanie klastra pakietu HPC Pack o rozmiarze A8, A9, H16r, lub H16rm Linux obliczenia węzłów za pomocą [szablonu usługi Azure Resource Manager](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) lub [platformy Azure Skrypt programu PowerShell](hpcpack-cluster-powershell-script.md). Zobacz [wprowadzenie do węzłów obliczeniowych systemu Linux w klastrze pakietu HPC Pack na platformie Azure](hpcpack-cluster.md) wymagania wstępne i kroki dla każdej opcji. Jeśli wybierzesz opcję wdrożenia skryptu programu PowerShell, zobacz przykładowy plik konfiguracji w plikach próbki na końcu tego artykułu. Wdrażanie klastra pakietu HPC Pack opartych na platformie Azure składający się z węzła głównego rozmiar A8 systemu Windows Server 2012 R2 i 2 węzły obliczeniowe A8 SUSE Linux Enterprise Server 12 rozmiar przy użyciu tej konfiguracji. Zastąp odpowiednie wartości dla nazwy subskrypcji i usług. 
  
  **Dodatkowe kwestie, które znasz**
  
  * Aby uzyskać wstępne sieci RDMA systemu Linux na platformie Azure, zobacz [o wysokiej wydajności obliczeń rozmiarów maszyn wirtualnych](../../windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
  * Jeśli używasz opcji wdrożenia skryptu programu Powershell, należy wdrożyć wszystkich węzłów obliczeniowych systemu Linux w jedną usługę w chmurze przy użyciu połączenia sieciowego RDMA.
  * Po wdrożeniu węzłów systemu Linux, łączenie za pośrednictwem protokołu SSH, aby wykonać wszelkie dodatkowe zadania administracyjne. Znajdź szczegóły połączenia SSH dla każdej maszyny Wirtualnej systemu Linux w witrynie Azure portal.  
* **Intel MPI** — uruchamianie programu OpenFOAM w węzłach obliczeniowych z systemem SLES 12 HPC na platformie Azure, musisz zainstalować środowisko uruchomieniowe Intel MPI biblioteki 5 z [witryny Intel.com](https://software.intel.com/en-us/intel-mpi-library/). (Intel MPI 5 jest preinstalowany na obrazy systemu CentOS HPC).  Na późniejszym etapie Jeśli to konieczne, zainstaluj Intel MPI na węzły obliczeniowe systemu Linux. Aby przygotować się do tego kroku, po zarejestrowaniu się firmy Intel, postępuj zgodnie z linku w wiadomości e-mail z potwierdzeniem do powiązanych strony sieci web. Następnie skopiuj link pobierania dla pliku .tgz dla odpowiedniej wersji Intel MPI. Ten artykuł jest oparty na Intel MPI wersji 5.0.3.048.
* **OpenFOAM źródła pakietu** — Pobierz oprogramowania OpenFOAM źródła dodatkiem Service Pack dla systemu Linux z [witryny OpenFOAM Foundation](http://openfoam.org/download/2-3-1-source/). W tym artykule opiera się na wersję źródła pakietu 2.3.1 dostępne do pobrania jako OpenFOAM 2.3.1.tgz. Postępuj zgodnie z instrukcjami w dalszej części tego artykułu, aby rozpakować i skompilować OpenFOAM w węzłach obliczeniowych systemu Linux.
* **EnSight** (opcjonalnie) — aby wyświetlić wyniki symulacji OpenFOAM Pobierz i zainstaluj [EnSight](https://www.ceisoftware.com/download/) program wizualizacji i analizy. Informacje o licencjonowaniu i pobierania znajdują się w witrynie EnSight.

## <a name="set-up-mutual-trust-between-compute-nodes"></a>Konfigurowanie wzajemnego zaufania między węzłami obliczeniowymi
Uruchamianie zadania między węzłami w wielu węzłach Linux wymaga węzłów sobie nawzajem ufają (przez **rsh** lub **ssh**). Po utworzeniu klastra pakietu HPC Pack ze skryptem wdrażania IaaS pakietu Microsoft HPC Pack skrypt automatycznie konfiguruje stałe wzajemnego zaufania dla konta administratora, które określisz. Dla użytkowników bez uprawnień administratora, utworzone w domenie klastra trzeba skonfigurować tymczasowe wzajemnego zaufania między węzłami, gdy zadanie jest przydzielany do nich i zniszcz relacji, po ukończeniu zadania. Aby ustanowić zaufanie dla każdego użytkownika, podaj pary kluczy RSA do klastra, który korzysta z pakietu HPC Pack dla relacji zaufania.

### <a name="generate-an-rsa-key-pair"></a>Generowanie pary kluczy RSA
Łatwo można wygenerować pary kluczy RSA, który zawiera klucz publiczny i klucz prywatny, uruchamiając Linux **ssh-keygen** polecenia.

1. Zaloguj się na komputerze z systemem Linux.
2. Uruchom następujące polecenie:
   
   ```
   ssh-keygen -t rsa
   ```
   
   > [!NOTE]
   > Naciśnij klawisz **Enter** można użyć ustawień domyślnych, aż do zakończenia polecenia. Nie należy wprowadzać hasło w tym miejscu Po wyświetleniu monitu o podanie hasła, wystarczy nacisnąć klawisz **Enter**.
   > 
   > 
   
   ![Generowanie pary kluczy RSA][keygen]
3. Zmień katalog na katalog ~/.ssh. Klucz prywatny jest przechowywany w id_rsa i klucz publiczny w id_rsa.pub.
   
   ![Klucze prywatne i publiczne][keys]

### <a name="add-the-key-pair-to-the-hpc-pack-cluster"></a>Dodaj parę kluczy do klastra pakietu HPC Pack
1. Nawiązywanie połączenia pulpitu zdalnego z węzła głównego przy użyciu konta administratora pakietu HPC Pack (konta administratora, które można skonfigurować po uruchomieniu skryptu wdrażania).
2. Użyj standardowych procedur w systemie Windows Server do utworzenia konta użytkownika domeny w domenie usługi Active Directory klastra. Na przykład narzędzie użytkowników usługi Active Directory i komputerów w węźle głównym. Przykłady w niniejszym artykule przyjęto założenie, że utworzenie użytkownika domeny o nazwie hpclab\hpcuser.
3. Utwórz plik o nazwie C:\cred.xml i skopiuj dane klucza RSA do niego. Przykładowy plik cred.xml znajduje się na końcu tego artykułu.
   
   ```
   <ExtendedData>
     <PrivateKey>Copy the contents of private key here</PrivateKey>
     <PublicKey>Copy the contents of public key here</PublicKey>
   </ExtendedData>
   ```
4. Otwórz wiersz polecenia i wprowadź następujące polecenie, aby ustawić danych poświadczenia dla konta hpclab\hpcuser. Możesz użyć **extendeddata** parametru, aby przekazać nazwę C:\cred.xml utworzony plik klucza danych.
   
   ```
   hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
   ```
   
   To polecenie zakończy się pomyślnie bez danych wyjściowych. Po ustawieniu poświadczeń dla kont użytkowników, które są potrzebne do uruchamiania zadań, plik cred.xml należy przechowywać w bezpiecznej lokalizacji lub usuń go.
5. Jeśli na jednym z węzłów z systemem Linux generowana jest para kluczy RSA, pamiętaj, aby usunąć klucze, po zakończeniu używania ich. Jeśli pakiet HPC Pack znajdzie istniejącego pliku klucza id_rsa lub plik id_rsa.pub, nieskonfigurowanym wypychaniem wzajemnego zaufania.

> [!IMPORTANT]
> Nie zaleca się uruchamiania zadania Linux jako administrator klastra w klastrze udostępnionym, ponieważ zadanie przesłane przez administratora jest uruchamiana w ramach konta głównego na węzłów systemu Linux. Jednak zadanie przesłane przez użytkownika bez uprawnień administratora uruchamia się w ramach lokalnego konta użytkownika systemu Linux taką samą nazwę jak użytkownika zadania. W tym przypadku pakietu HPC Pack konfiguruje wzajemnego zaufania dla tego użytkownika w systemie Linux w węzłach przydzielony do zadania. Przed uruchomieniem zadania można skonfigurować użytkownika ręcznie na węzłów systemu Linux w systemie Linux lub pakietu HPC Pack użytkownika automatycznie tworzy podczas przesyłania zadania. Jeśli pakiet HPC Pack tworzy użytkownik, pakietu HPC Pack spowoduje usunięcie go po ukończeniu zadania. Aby ograniczyć zagrożenia bezpieczeństwa, pakietu HPC Pack usuwa kluczy po zakończeniu zadania.
> 
> 

## <a name="set-up-a-file-share-for-linux-nodes"></a>Konfigurowanie udziału plików w przypadku węzłów systemu Linux
Teraz należy skonfigurować standardowy udział SMB dla folderu w węźle głównym. Aby umożliwić węzłów systemu Linux, dostęp do plików aplikacji przy użyciu wspólnej ścieżki, należy zainstalować folderu udostępnionego na węzłów systemu Linux. Jeśli chcesz, można użyć innego pliku, udostępnianie opcji, takich jak udział usługi Azure Files — zalecane w przypadku wielu scenariuszy — lub udziału NFS. Zapoznaj się z plikiem udostępniania informacji oraz szczegółowy opis kroków w [Rozpoczynanie pracy z węzłami obliczeniowymi systemu Linux w klastrze HPC Pack na platformie Azure](hpcpack-cluster.md).

1. Utwórz folder w węźle głównym i udostępnić go dla wszystkich użytkowników, ustawiając uprawnienia odczytu/zapisu. Na przykład udostępnić C:\OpenFOAM w węźle głównym jako \\ \\SUSE12RDMA HN\OpenFOAM. W tym miejscu *SUSE12RDMA HN* jest nazwa hosta węzła głównego.
2. Otwórz okno programu Windows PowerShell i uruchom następujące polecenia:
   
   ```
   clusrun /nodegroup:LinuxNodes mkdir -p /openfoam
   
   clusrun /nodegroup:LinuxNodes mount -t cifs //SUSE12RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
   ```

Pierwsze polecenie tworzy folder o nazwie /openfoam we wszystkich węzłach grupy LinuxNodes. Drugie polecenie instaluje //SUSE12RDMA-HN/OpenFOAM folderu udostępnionego na węzłów systemu Linux przy użyciu dir_mode i file_mode bitów o wartości 777. *Username* i *hasło* w poleceniu, powinny być to poświadczenia użytkownika w węźle głównym.

> [!NOTE]
> "\`" Symbol, drugie polecenie jest symbolem ucieczki dla programu PowerShell. "\`," oznacza "," (znak przecinka) jest częścią polecenia.
> 
> 

## <a name="install-mpi-and-openfoam"></a>Zainstaluj MPI i OpenFOAM
Uruchamianie programu OpenFOAM jako zadania MPI sieci RDMA, należy skompilować pakietu OpenFOAM z bibliotekami Intel MPI. 

Najpierw uruchom kilka **clusrun** polecenia do zainstalowania bibliotek Intel MPI (jeśli jeszcze nie zainstalowano) i OpenFOAM na węzłów z systemem Linux. Użyj udziału węzeł główny został wcześniej skonfigurowany na potrzeby udostępniania plików instalacji między węzłami systemu Linux.

> [!IMPORTANT]
> Te instalacji i kroki kompilacji są przykładami. Należy pewną wiedzę na temat administrowania systemem Linux, aby upewnić się, że biblioteki i Kompilatory języka zależne zostały zainstalowane poprawnie. Konieczne może się okazać zmodyfikowanie pewne zmienne środowiskowe lub inne ustawienia usługi i na procesorze Intel MPI OpenFOAM. Aby uzyskać więcej informacji, zobacz [Intel MPI biblioteki dla systemu Linux Przewodnik instalacji](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html) i [instalacji pakietu źródła pakietu OpenFOAM](http://openfoam.org/download/2-3-1-source/) dla danego środowiska.
> 
> 

### <a name="install-intel-mpi"></a>Zainstaluj Intel MPI
Zapisz pobranego pakietu instalacyjnego dla Intel MPI (l_mpi_p_5.0.3.048.tgz w tym przykładzie) w C:\OpenFoam w węźle głównym czemu węzłów systemu Linux można uzyskiwać dostęp do tego pliku z /openfoam. Następnie uruchom **clusrun** Aby zainstalować bibliotekę Intel MPI w węzłach systemu Linux.

1. Następujące polecenia, skopiuj pakiet instalacyjny i Wyodrębnij jego zawartość do /opt/intel w każdym węźle.
   
   ```
   clusrun /nodegroup:LinuxNodes mkdir -p /opt/intel
   
   clusrun /nodegroup:LinuxNodes cp /openfoam/l_mpi_p_5.0.3.048.tgz /opt/intel/
   
   clusrun /nodegroup:LinuxNodes tar -xzf /opt/intel/l_mpi_p_5.0.3.048.tgz -C /opt/intel/
   ```
2. Aby dyskretnie zainstalować Intel MPI biblioteki, należy użyć pliku silent.cfg. Przykład można znaleźć w plikach próbki na końcu tego artykułu. Umieść ten plik w /openfoam folderu udostępnionego. Aby uzyskać szczegółowe informacje o pliku silent.cfg, zobacz [Intel MPI biblioteki dla Przewodnik instalacji systemu Linux — instalacji dyskretnej](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html#silentinstall).
   
   > [!TIP]
   > Upewnij się, że zapiszesz plik silent.cfg jako plik tekstowy z systemem Linux zakończenia (tylko LF, nie CR LF) wierszy. Ten krok zapewnia, że działa prawidłowo w węzłach systemu Linux.
   > 
   > 
3. Zainstaluj Intel MPI biblioteki w trybie dyskretnym.
   
   ```
   clusrun /nodegroup:LinuxNodes bash /opt/intel/l_mpi_p_5.0.3.048/install.sh --silent /openfoam/silent.cfg
   ```

### <a name="configure-mpi"></a>Konfigurowanie MPI
W przypadku testowania należy dodać następujące wiersze do /etc/security/limits.conf na każdym z węzłów systemu Linux:

    clusrun /nodegroup:LinuxNodes echo "*               hard    memlock         unlimited" `>`> /etc/security/limits.conf
    clusrun /nodegroup:LinuxNodes echo "*               soft    memlock         unlimited" `>`> /etc/security/limits.conf


Po zaktualizowaniu pliku limits.conf, należy ponownie uruchomić węzłów systemu Linux. Na przykład Poniższa **clusrun** polecenia:

```
clusrun /nodegroup:LinuxNodes systemctl reboot
```

Po ponownym uruchomieniu komputera, upewnij się, że folder udostępniony został zainstalowany jako /openfoam.

### <a name="compile-and-install-openfoam"></a>Kompiluj i instalowanie pakietu OpenFOAM
Zapisz w węźle głównym pobranego pakietu instalacyjnego pakietu OpenFOAM źródła (w tym przykładzie OpenFOAM-2.3.1.tgz) do C:\OpenFoam, czemu węzłów systemu Linux można uzyskiwać dostęp do tego pliku z /openfoam. Następnie uruchom **clusrun** polecenia, aby skompilować OpenFOAM we wszystkich węzłach w systemie Linux.

1. Utwórz /opt/OpenFOAM folderu w każdym węźle systemu Linux, skopiuj pakiet "source" do tego folderu i Wyodrębnij jego zawartość.
   
   ```
   clusrun /nodegroup:LinuxNodes mkdir -p /opt/OpenFOAM
   
   clusrun /nodegroup:LinuxNodes cp /openfoam/OpenFOAM-2.3.1.tgz /opt/OpenFOAM/
   
   clusrun /nodegroup:LinuxNodes tar -xzf /opt/OpenFOAM/OpenFOAM-2.3.1.tgz -C /opt/OpenFOAM/
   ```
2. Aby skompilować pakietu OpenFOAM z biblioteką Intel MPI, najpierw skonfigurować niektóre zmienne środowiskowe zarówno Intel MPI, i OpenFOAM. Użyj skryptu powłoki systemowej, o nazwie settings.sh, aby ustawić zmienne. Przykład można znaleźć w plikach próbki na końcu tego artykułu. Umieść ten plik (zapisane przy użyciu systemu Linux końce wierszy) w /openfoam folderu udostępnionego. Ten plik zawiera także ustawienia środowiska uruchomieniowego MPI i OpenFOAM, którego później uruchomić zadanie OpenFOAM.
3. Zainstaluj zależne pakiety wymagane do kompilowania OpenFOAM. W zależności od dystrybucji systemu Linux może najpierw musisz dodać repozytorium. Uruchom **clusrun** polecenia podobnego do następującego:
   
    ```
    clusrun /nodegroup:LinuxNodes zypper ar http://download.opensuse.org/distribution/13.2/repo/oss/suse/ opensuse
   
    clusrun /nodegroup:LinuxNodes zypper -n --gpg-auto-import-keys install --repo opensuse --force-resolution -t pattern devel_C_C++
    ```
   
    Jeśli to konieczne, SSH z każdym z węzłów systemu Linux do uruchamiania poleceń, aby upewnić się, że działają prawidłowo.
4. Uruchom następujące polecenie, aby skompilować OpenFOAM. Proces kompilacji wymaga pewnego czasu, aby zakończyć i generuje dużą ilość informacji dziennika do wyjścia standardowego, a więc **/ z przeplotem** opcję, aby wyświetlić dane wyjściowe z przeplotem.
   
   ```
   clusrun /nodegroup:LinuxNodes /interleaved source /openfoam/settings.sh `&`& /opt/OpenFOAM/OpenFOAM-2.3.1/Allwmake
   ```
   
   > [!NOTE]
   > "\`" Symbol w poleceniu jest symbolem ucieczki dla programu PowerShell. "\`&" oznacza "&" jest częścią polecenia.
   > 
   > 

## <a name="prepare-to-run-an-openfoam-job"></a>Przygotowywanie do uruchomienia zadania programu OpenFOAM
Teraz Przygotuj się do uruchamiania zadań MPI w dwóch węzłów systemu Linux o nazwie sloshingTank3D, który jest jednym z przykładów programu OpenFoam. 

### <a name="set-up-the-runtime-environment"></a>Konfigurowanie środowiska wykonawczego
Aby skonfigurować środowiska uruchomieniowe MPI i OpenFOAM w węzłach systemu Linux, uruchom następujące polecenie w oknie programu Windows PowerShell w węźle głównym. (To polecenie jest prawidłowe dla systemu Linux SUSE.)

```
clusrun /nodegroup:LinuxNodes cp /openfoam/settings.sh /etc/profile.d/
```

### <a name="prepare-sample-data"></a>Przygotowywanie danych przykładowych
Użyj udział węzłem, który wcześniej skonfigurowane na potrzeby udostępniania plików między węzłami systemu Linux (zainstalowanego jako /openfoam).

1. SSH systemu Linux z węzłów obliczeniowych.
2. Uruchom następujące polecenie, aby skonfigurować środowisko uruchomieniowe programu OpenFOAM, jeśli jeszcze nie zostało to zrobione.
   
   ```
   $ source /openfoam/settings.sh
   ```
3. Skopiuj przykład sloshingTank3D do folderu udostępnionego, a następnie przejdź do niego.
   
   ```
   $ cp -r $FOAM_TUTORIALS/multiphase/interDyMFoam/ras/sloshingTank3D /openfoam/
   
   $ cd /openfoam/sloshingTank3D
   ```
4. Gdy używane są parametry domyślne tego przykładu, może upłynąć dziesiątki minut, dzięki czemu możesz chcieć zmodyfikować niektóre parametry, aby stał się działają szybciej. Jednego prostego to do modyfikowania deltaT zmienne krok czasu i writeInterval w pliku system/controlDict. Ten plik przechowuje wszystkie dane wejściowe, odnoszące się do kontroli czasu i odczytywania i zapisywania danych rozwiązania. Na przykład możesz zmienić wartość deltaT z 0,05 0,5 i wartość writeInterval z 0,05 0,5.
   
   ![Modyfikowanie zmiennych kroku][step_variables]
5. Podaj odpowiednie wartości dla zmiennych w pliku system/decomposeParDict. W tym przykładzie użyto dwóch węzłów systemu Linux, każdy z 8 rdzeni, więc Ustaw numberOfSubdomains 16 i n hierarchicalCoeffs do (1 1 16), co oznacza, że uruchamianie oprogramowania OpenFOAM równolegle z 16 procesów. Aby uzyskać więcej informacji, zobacz [Podręcznik użytkownika programu OpenFOAM: 3.4 aplikacje uruchomione w sposób równoległy](http://cfd.direct/openfoam/user-guide/running-applications-parallel/#x12-820003.4).
   
   ![Rozłóż procesów][decompose]
6. Uruchom następujące polecenia w katalogu sloshingTank3D, aby przygotować przykładowych danych.
   
   ```
   $ . $WM_PROJECT_DIR/bin/tools/RunFunctions
   
   $ m4 constant/polyMesh/blockMeshDict.m4 > constant/polyMesh/blockMeshDict
   
   $ runApplication blockMesh
   
   $ cp 0/alpha.water.org 0/alpha.water
   
   $ runApplication setFields  
   ```
7. W węźle głównym powinien pojawić się przykładowe pliki danych są kopiowane do C:\OpenFoam\sloshingTank3D. (C:\OpenFoam jest folder udostępniony w węźle głównym).
   
   ![Pliki danych na węzeł główny][data_files]

### <a name="host-file-for-mpirun"></a>Plik hosta dla mpirun
W tym kroku utworzysz pliku hosta (Lista węzłów obliczeniowych) który **mpirun** polecenie używa.

1. Na jednym z węzłów systemu Linux Utwórz plik o nazwie hostfile w obszarze /openfoam, więc ten plik można z Tobą skontaktować w /openfoam/hostfile we wszystkich węzłach w systemie Linux.
2. Do tego pliku, należy zapisać swoje nazwy węzłów systemu Linux. W tym przykładzie plik zawiera następujące nazwy:
   
   ```       
   SUSE12RDMA-LN1
   SUSE12RDMA-LN2
   ```
   
   > [!TIP]
   > Ten plik można także utworzyć w C:\OpenFoam\hostfile w węźle głównym. Jeśli ta opcja jest wybrana, zapisz go jako plik tekstowy z systemem Linux zakończenia wierszy (tylko LF, nie CR LF). Daje to gwarancję, że działa prawidłowo w węzłach systemu Linux.
   > 
   > 
   
   **Otoka skrypt powłoki bash**
   
   Jeśli masz wiele węzłów systemu Linux i chcesz, aby zadanie do uruchomienia tylko na niektórych z nich, nie jest dobry pomysł, aby przy użyciu pliku hosta stałą, ponieważ nie wiadomo, które węzły zostaną przydzielone do zadania. W tym przypadku zapisu otoką skrypt powłoki bash **mpirun** automatycznego tworzenia pliku hosta. Można znaleźć przykład bash skryptu wywołanej otoki hpcimpirun.sh na końcu tego artykułu, a następnie zapisz go jako /openfoam/hpcimpirun.sh. Ten przykładowy skrypt wykonuje następujące czynności:
   
   1. Ustawia zmienne środowiskowe dla **mpirun**, a niektóre parametry polecenia dodawania do uruchomienia zadania MPI za pośrednictwem sieci RDMA. W takim wypadku ustawia następujące zmienne:
      
      * I_MPI_FABRICS=shm:dapl
      * I_MPI_DAPL_PROVIDER=ofa-v2-ib0
      * I_MPI_DYNAMIC_CONNECTION=0
   2. Tworzy plik hosta zgodnie ze środowiska zmienną $CCP_NODES_CORES, która została ustawiona przez węzeł główny HPC, gdy zadanie jest aktywny.
      
      Format $CCP_NODES_CORES tym wzorcem:
      
      ```
      <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
      ```
      
      gdzie
      
      * `<Number of nodes>` -Liczba węzłów przydzielony do tego zadania.  
      * `<Name of node_n_...>` — Nazwa każdego węzła przydzielony do tego zadania.
      * `<Cores of node_n_...>` -Liczba rdzeni na węzeł, który został przydzielony do tego zadania.
      
      Na przykład jeśli zadanie wymaga dwóch węzłów do uruchomienia, $CCP_NODES_CORES przypomina
      
      ```
      2 SUSE12RDMA-LN1 8 SUSE12RDMA-LN2 8
      ```
   3. Wywołania **mpirun** polecenie i dołącza dwóch parametrów w wierszu polecenia.
      
      * `--hostfile <hostfilepath>: <hostfilepath>` — Ścieżka pliku hosta, skrypt tworzy
      * `-np ${CCP_NUMCPUS}: ${CCP_NUMCPUS}` — Zmienna środowiskowa ustawione przez węzeł główny HPC Pack, którym przechowywana jest liczba całkowita liczba rdzeni przydzielony do tego zadania. W tym przypadku określa liczbę procesów dla **mpirun**.

## <a name="submit-an-openfoam-job"></a>Prześlij zadanie programu OpenFOAM
Teraz można przesłać zadania w Menedżerze klastra HPC. Musisz przekazać hpcimpirun.sh skryptu w wierszach polecenia dla niektórych zadań.

1. Nawiązać połączenie z głównym węzłem klastra, a następnie uruchom Menedżer klastra HPC.
2. **W funkcji zarządzania zasobami**, upewnij się, że węzły obliczeniowe systemu Linux w **Online** stanu. Jeśli nie, zaznacz je i kliknij przycisk **przejdź do trybu Online**.
3. W **zadań zarządzania**, kliknij przycisk **nowe zadanie**.
4. Wprowadź nazwę zadania, taką jak *sloshingTank3D*.
   
   ![Szczegóły zadania][job_details]
5. W **zadania zasoby**, wybierz typ zasobu jako "Węzeł" oraz ustawiać Minimum na 2. Ta konfiguracja działa zadania na dwóch węzłów systemu Linux, z których każdy ma osiem rdzeni w tym przykładzie.
   
   ![Zasoby zadania][job_resources]
6. Kliknij przycisk **Edycja zadań** w nawigacji po lewej stronie, a następnie kliknij przycisk **Dodaj** Aby dodać zadanie do zadania. Dodaj cztery zadania podrzędne do zadania za pomocą następujących wierszy polecenia i ustawień.
   
   > [!NOTE]
   > Uruchamianie `source /openfoam/settings.sh` konfiguruje środowiska uruchomieniowe OpenFOAM i MPI, dzięki czemu każdy z poniższych zadań wywołuje przed poleceniem OpenFOAM.
   > 
   > 
   
   * **Zadanie 1**. Uruchom **decomposePar** ma generować pliki danych do uruchomienia **interDyMFoam** równolegle.
     
     * Przypisz jeden węzeł do zadania
     * **Wiersz polecenia** - `source /openfoam/settings.sh && decomposePar -force > /openfoam/decomposePar${CCP_JOBID}.log`
     * **Katalog roboczy** -/ openfoam/sloshingTank3D
     
     Zobacz poniższą ilustrację. Podobnie możesz skonfigurować pozostałych zadań.
     
     ![Zadanie 1 szczegółów][task_details1]
   * **Zadanie 2**. Uruchom **interDyMFoam** równolegle w celu obliczenia próbki.
     
     * Przypisywanie z uwzględnieniem dwóch węzłów do zadania
     * **Wiersz polecenia** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh interDyMFoam -parallel > /openfoam/interDyMFoam${CCP_JOBID}.log`
     * **Katalog roboczy** -/ openfoam/sloshingTank3D
   * **Zadanie 3**. Uruchom **reconstructPar** do scalenia ustawia czas katalogów z każdym katalogiem processor_N_ w jeden zestaw.
     
     * Przypisz jeden węzeł do zadania
     * **Wiersz polecenia** - `source /openfoam/settings.sh && reconstructPar > /openfoam/reconstructPar${CCP_JOBID}.log`
     * **Katalog roboczy** -/ openfoam/sloshingTank3D
   * **Zadanie 4**. Uruchom **foamToEnsight** równolegle, aby konwertować wynik OpenFOAM pliki do EnSight formatowania i umieść pliki EnSight w katalogu o nazwie Ensight w przypadku katalogu.
     
     * Przypisywanie z uwzględnieniem dwóch węzłów do zadania
     * **Wiersz polecenia** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh foamToEnsight -parallel > /openfoam/foamToEnsight${CCP_JOBID}.log`
     * **Katalog roboczy** -/ openfoam/sloshingTank3D
7. Dodaj zależności do tych zadań w kolejności rosnącej kolejności zadań.
   
   ![Zależności zadań][task_dependencies]
8. Kliknij przycisk **przesyłania** do uruchomienia tego zadania.
   
   Domyślnie pakiet HPC Pack przesyła zadanie jako konto bieżącego zalogowanego użytkownika. Po kliknięciu **przesyłania**, być może zobaczysz okno dialogowe z monitem o podanie nazwy użytkownika i hasła.
   
   ![Poświadczenia zadania][creds]
   
   W niektórych warunkach pakietu HPC Pack pamięta informacje o użytkowniku, dane wejściowe przed i nie pokazuj tego okna dialogowego. Aby pakietu HPC Pack wyświetlić ją ponownie, wpisz następujące polecenie w wierszu polecenia, a następnie przesłać zadanie.
   
   ```
   hpccred delcreds
   ```
9. Zadanie trwa od kilkunastu minut do kilku godzin zgodnie z parametrami ustawionych dla przykładu. Na mapie cieplnej zobaczysz zadania uruchomione w węzłach systemu Linux. 
   
   ![Mapa cieplna][heat_map]
   
   W każdym węźle osiem procesy są uruchomione.
   
   ![Procesy systemu Linux][linux_processes]
10. Po zakończeniu zadania, należy znaleźć wyniki zadania w folderach w ramach C:\OpenFoam\sloshingTank3D i pliki dziennika w C:\OpenFoam.

## <a name="view-results-in-ensight"></a>Wyświetlanie wyników w EnSight
Opcjonalnie użyć [EnSight](https://www.ceisoftware.com/) wizualizowanie i analizowanie wyników zadania OpenFOAM. Więcej informacji na temat wizualizacji i animacji w EnSight, zobacz [przewodnik wideo](http://www.ceisoftware.com/wp-content/uploads/screencasts/vof_visualization/vof_visualization.html).

1. Po zainstalowaniu EnSight w węźle głównym, należy ją uruchomić.
2. Open C:\OpenFoam\sloshingTank3D\EnSight\sloshingTank3D.case.
   
   Zostanie wyświetlony zbiornik w przeglądarce.
   
   ![Zbiornik w EnSight][tank]
3. Tworzenie **Isosurface** z **internalMesh**, a następnie wybierz zmienną **alpha_water**.
   
   ![Utwórz isosurface][isosurface]
4. Ustaw kolor **Isosurface_part** utworzony w poprzednim kroku. Na przykład ustaw ją do wody niebieski.
   
   ![Kolor isosurface edycji][isosurface_color]
5. Tworzenie **Iso-volume** z **ściany** , wybierając **ściany** w **części** panelu, a następnie kliknij przycisk **Isosurfaces** przycisku na pasku narzędzi.
6. W oknie dialogowym wybierz **typu** jako **Isovolume** i ustaw minimum z **zakres Isovolume** 0,5. Aby utworzyć isovolume, kliknij przycisk **Utwórz za pomocą zaznaczonych części**.
7. Ustaw kolor **Iso_volume_part** utworzony w poprzednim kroku. Na przykład ustaw ją do wody głębokiego niebieski.
8. Ustaw kolor **ściany**. Na przykład ustaw ją na biały przezroczysty.
9. Teraz klikaj polecenie **Odtwórz** aby zobaczyć wyniki symulacji.
   
    ![Wynik napełnienia zbiorników z ropą][tank_result]

## <a name="sample-files"></a>Przykładowe pliki
### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>Przykładowy plik konfiguracji XML wdrażania klastra za pomocą skryptu programu PowerShell
 ```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>allvhdsje</StorageAccount>
  </Subscription>
  <Location>Japan East</Location>  
  <VNet>
    <VNetName>suse12rdmavnet</VNetName>
    <SubnetName>SUSE12RDMACluster</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpclab.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>SUSE12RDMA-HN</VMName>
    <ServiceName>suse12rdma-je</ServiceName>
    <VMSize>A8</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>SUSE12RDMA-LN%1%</VMNamePattern>
    <ServiceName>suse12rdma-je</ServiceName>
    <VMSize>A8</VMSize>
    <NodeCount>2</NodeCount>
      <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```

### <a name="sample-credxml-file"></a>Przykładowy plik cred.xml
```
<ExtendedData>
  <PrivateKey>-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAxJKBABhnOsE9eneGHvsjdoXKooHUxpTHI1JVunAJkVmFy8JC
qFt1pV98QCtKEHTC6kQ7tj1UT2N6nx1EY9BBHpZacnXmknpKdX4Nu0cNlSphLpru
lscKPR3XVzkTwEF00OMiNJVknq8qXJF1T3lYx3rW5EnItn6C3nQm3gQPXP0ckYCF
Jdtu/6SSgzV9kaapctLGPNp1Vjf9KeDQMrJXsQNHxnQcfiICp21NiUCiXosDqJrR
AfzePdl0XwsNngouy8t0fPlNSngZvsx+kPGh/AKakKIYS0cO9W3FmdYNW8Xehzkc
VzrtJhU8x21hXGfSC7V0ZeD7dMeTL3tQCVxCmwIDAQABAoIBAQCve8Jh3Wc6koxZ
qh43xicwhdwSGyliZisoozYZDC/ebDb/Ydq0BYIPMiDwADVMX5AqJuPPmwyLGtm6
9hu5p46aycrQ5+QA299g6DlF+PZtNbowKuvX+rRvPxagrTmupkCswjglDUEYUHPW
05wQaNoSqtzwS9Y85M/b24FfLeyxK0n8zjKFErJaHdhVxI6cxw7RdVlSmM9UHmah
wTkW8HkblbOArilAHi6SlRTNZG4gTGeDzPb7fYZo3hzJyLbcaNfJscUuqnAJ+6pT
iY6NNp1E8PQgjvHe21yv3DRoVRM4egqQvNZgUbYAMUgr30T1UoxnUXwk2vqJMfg2
Nzw0ESGRAoGBAPkfXjjGfc4HryqPkdx0kjXs0bXC3js2g4IXItK9YUFeZzf+476y
OTMQg/8DUbqd5rLv7PITIAqpGs39pkfnyohPjOe2zZzeoyaXurYIPV98hhH880uH
ZUhOxJYnlqHGxGT7p2PmmnAlmY4TSJrp12VnuiQVVVsXWOGPqHx4S4f9AoGBAMn/
vuea7hsCgwIE25MJJ55FYCJodLkioQy6aGP4NgB89Azzg527WsQ6H5xhgVMKHWyu
Q1snp+q8LyzD0i1veEvWb8EYifsMyTIPXOUTwZgzaTTCeJNHdc4gw1U22vd7OBYy
nZCU7Tn8Pe6eIMNztnVduiv+2QHuiNPgN7M73/x3AoGBAOL0IcmFgy0EsR8MBq0Z
ge4gnniBXCYDptEINNBaeVStJUnNKzwab6PGwwm6w2VI3thbXbi3lbRAlMve7fKK
B2ghWNPsJOtppKbPCek2Hnt0HUwb7qX7Zlj2cX/99uvRAjChVsDbYA0VJAxcIwQG
TxXx5pFi4g0HexCa6LrkeKMdAoGAcvRIACX7OwPC6nM5QgQDt95jRzGKu5EpdcTf
g4TNtplliblLPYhRrzokoyoaHteyxxak3ktDFCLj9eW6xoCZRQ9Tqd/9JhGwrfxw
MS19DtCzHoNNewM/135tqyD8m7pTwM4tPQqDtmwGErWKj7BaNZARUlhFxwOoemsv
R6DbZyECgYEAhjL2N3Pc+WW+8x2bbIBN3rJcMjBBIivB62AwgYZnA2D5wk5o0DKD
eesGSKS5l22ZMXJNShgzPKmv3HpH22CSVpO0sNZ6R+iG8a3oq4QkU61MT1CfGoMI
a8lxTKnZCsRXU1HexqZs+DSc+30tz50bNqLdido/l5B4EJnQP03ciO0=
-----END RSA PRIVATE KEY-----</PrivateKey>
  <PublicKey>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDEkoEAGGc6wT16d4Ye+yN2hcqigdTGlMcjUlW6cAmRWYXLwkKoW3WlX3xAK0oQdMLqRDu2PVRPY3qfHURj0EEellpydeaSekp1fg27Rw2VKmEumu6Wxwo9HddXORPAQXTQ4yI0lWSerypckXVPeVjHetbkSci2foLedCbeBA9c/RyRgIUl227/pJKDNX2Rpqly0sY82nVWN/0p4NAyslexA0fGdBx+IgKnbU2JQKJeiwOomtEB/N492XRfCw2eCi7Ly3R8+U1KeBm+zH6Q8aH8ApqQohhLRw71bcWZ1g1bxd6HORxXOu0mFTzHbWFcZ9ILtXRl4Pt0x5Mve1AJXEKb username@servername;</PublicKey>
</ExtendedData>
```
### <a name="sample-silentcfg-file-to-install-mpi"></a>Przykładowy plik silent.cfg zainstalował MPI
```
# Patterns used to check silent configuration file
#
# anythingpat - any string
# filepat     - the file location pattern (/file/location/to/license.lic)
# lspat       - the license server address pattern (0123@hostname)
# snpat       - the serial number pattern (ABCD-01234567)

# accept EULA, valid values are: {accept, decline}
ACCEPT_EULA=accept

# optional error behavior, valid values are: {yes, no}
CONTINUE_WITH_OPTIONAL_ERROR=yes

# install location, valid values are: {/opt/intel, filepat}
PSET_INSTALL_DIR=/opt/intel

# continue with overwrite of existing installation directory, valid values are: {yes, no}
CONTINUE_WITH_INSTALLDIR_OVERWRITE=yes

# list of components to install, valid values are: {ALL, DEFAULTS, anythingpat}
COMPONENTS=DEFAULTS

# installation mode, valid values are: {install, modify, repair, uninstall}
PSET_MODE=install

# directory for non-RPM database, valid values are: {filepat}
#NONRPM_DB_DIR=filepat

# Serial number, valid values are: {snpat}
#ACTIVATION_SERIAL_NUMBER=snpat

# License file or license server, valid values are: {lspat, filepat}
#ACTIVATION_LICENSE_FILE=

# Activation type, valid values are: {exist_lic, license_server, license_file, trial_lic, serial_number}
ACTIVATION_TYPE=trial_lic

# Path to the cluster description file, valid values are: {filepat}
#CLUSTER_INSTALL_MACHINES_FILE=filepat

# Intel(R) Software Improvement Program opt-in, valid values are: {yes, no}
PHONEHOME_SEND_USAGE_DATA=no

# Perform validation of digital signatures of RPM files, valid values are: {yes, no}
SIGNING_ENABLED=yes

# Select yes to enable mpi-selector integration, valid values are: {yes, no}
ENVIRONMENT_REG_MPI_ENV=no

# Select yes to update ld.so.conf, valid values are: {yes, no}
ENVIRONMENT_LD_SO_CONF=no


```

### <a name="sample-settingssh-script"></a>Przykładowy skrypt settings.sh
```
#!/bin/bash

# impi
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# openfoam
export FOAM_INST_DIR=/opt/OpenFOAM
source /opt/OpenFOAM/OpenFOAM-2.3.1/etc/bashrc
export WM_MPLIB=INTELMPI
```


### <a name="sample-hpcimpirunsh-script"></a>Przykładowy skrypt hpcimpirun.sh
```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"

# Set mpirun runtime evironment
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# mpirun command
MPIRUN=mpirun
# Argument of "--hostfile"
NODELIST_OPT="--hostfile"
# Argument of "-np"
NUMPROCESS_OPT="-np"

# Get node information from ENVs
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # CCP_NODES_CORES is not found or is empty, just run the mpirun without hostfile arg.
    ${MPIRUN} $*
else
    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$

    # Get every node name and write into the hostfile file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the mpirun with hostfile arg
    ${MPIRUN} ${NUMPROCESS_OPT} ${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}

```





<!--Image references-->
[keygen]:media/hpcpack-cluster-openfoam/keygen.png
[keys]:media/hpcpack-cluster-openfoam/keys.png
[step_variables]:media/hpcpack-cluster-openfoam/step_variables.png
[data_files]:media/hpcpack-cluster-openfoam/data_files.png
[decompose]:media/hpcpack-cluster-openfoam/decompose.png
[job_details]:media/hpcpack-cluster-openfoam/job_details.png
[job_resources]:media/hpcpack-cluster-openfoam/job_resources.png
[task_details1]:media/hpcpack-cluster-openfoam/task_details1.png
[task_dependencies]:media/hpcpack-cluster-openfoam/task_dependencies.png
[creds]:media/hpcpack-cluster-openfoam/creds.png
[heat_map]:media/hpcpack-cluster-openfoam/heat_map.png
[tank]:media/hpcpack-cluster-openfoam/tank.png
[tank_result]:media/hpcpack-cluster-openfoam/tank_result.png
[isosurface]:media/hpcpack-cluster-openfoam/isosurface.png
[isosurface_color]:media/hpcpack-cluster-openfoam/isosurface_color.png
[linux_processes]:media/hpcpack-cluster-openfoam/linux_processes.png
