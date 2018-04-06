---
title: Uruchom OpenFOAM pakietem HPC na maszynach wirtualnych systemu Linux | Dokumentacja firmy Microsoft
description: Wdrażanie klastra Microsoft HPC Pack na platformie Azure, a następnie uruchom zadanie OpenFOAM na wielu węzłach obliczeniowych Linux przez sieć RDMA.
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
ms.openlocfilehash: f43790d3495e1c09730e90b5077ec840731a7d83
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="run-openfoam-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>Uruchamianie oprogramowania OpenFoam przy użyciu pakietu Microsoft HPC w węzłach RDMA systemu Linux na platformie Azure
Ten artykuł przedstawia sposób uruchamiania OpenFoam w maszynach wirtualnych platformy Azure. W tym miejscu, w przypadku wdrażania klastra Microsoft HPC Pack z węzłami obliczeniowymi systemu Linux na platformie Azure i uruchom [OpenFoam](http://openfoam.com/) zadania z Intel MPI. Tak, aby węzły obliczeniowe komunikują się za pośrednictwem sieci Azure RDMA, można użyć z funkcją RDMA maszynach wirtualnych platformy Azure dla węzłów obliczeniowych. Inne opcje do uruchomienia OpenFoam na platformie Azure obejmują dostępnych obrazów komercyjnych pełni skonfigurowany w witrynie Marketplace, takie jak jego UberCloud [2.3 OpenFoam na CentOS 6](https://azure.microsoft.com/marketplace/partners/ubercloud/openfoam-v2dot3-centos-v6/)i uruchamianych [partii zadań Azure](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/). 

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

OpenFOAM (dla operacji Otwórz pole i manipulowania) to pakiet oprogramowania open source obliczeniową płynu dynamics (CFD), który jest powszechnie używany w inżynieryjne i naukowe w organizacjach użytku komercyjnego i academic. Obejmuje narzędzia do meshing, szczególnie snappyHexMesh zrównoleglone mesher złożonych CAD mają geometrię oraz wstępne i przetwarzania końcowego. Prawie wszystkie procesy wykonywane równolegle, umożliwiające użytkownikom w pełni korzystać z komputera za pośrednictwem interfejsu LINQ.  

Microsoft HPC Pack udostępnia funkcje do uruchamiania HPC na dużą skalę i równoległych aplikacji, w tym aplikacji MPI w klastrach maszyny wirtualne Microsoft Azure. HPC Pack obsługuje również uruchomione HPC systemu Linux węzła, maszyn wirtualnych wdrożonych w klastrze HPC Pack obliczeniowego aplikacji w systemie Linux. Zobacz [wprowadzenie węzły obliczeniowe systemu Linux w klastrze HPC Pack na platformie Azure](hpcpack-cluster.md) wprowadzenie do korzystania z systemu Linux obliczeniowe węzłów o HPC Pack.

> [!NOTE]
> W tym artykule przedstawiono sposób uruchamiania obciążeń Linux MPI bez HPC Pack. Zakłada się, że masz pewną znajomość z Administracja systemu Linux i systemem obciążeń MPI klastry z systemem Linux. Jeśli używasz wersji MPI oraz OpenFOAM inne niż wyświetlane w tym artykule, może być konieczne zmodyfikować niektóre kroki instalacji i konfiguracji. 
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
* **Węzły obliczeniowe HPC Pack klastra z systemem Linux z funkcją RDMA** — wdrożenie klastra HPC Pack o rozmiarze A8, A9, H16r, lub H16rm Linux obliczeniowe węzłów za pomocą [szablonu usługi Azure Resource Manager](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) lub [Azure Skrypt programu PowerShell](hpcpack-cluster-powershell-script.md). Zobacz [wprowadzenie węzły obliczeniowe systemu Linux w klastrze HPC Pack na platformie Azure](hpcpack-cluster.md) wymagania wstępne i kroki dla każdej opcji. Jeśli wybierzesz opcję wdrożenia skryptu programu PowerShell, zobacz przykładowy plik konfiguracji w przykładowych plików na końcu tego artykułu. Ta konfiguracja umożliwia wdrożenie składający się z węzłem głównym A8 systemu Windows Server 2012 R2 rozmiar i 2 węzły obliczeniowe A8 SUSE Linux Enterprise Server 12 rozmiar klastra bazujących na platformie Azure HPC Pack. Zastąp wartości odpowiednie dla Twojej subskrypcji i usługi nazw. 
  
  **Dodatkowe czynności, aby dowiedzieć się**
  
  * Dla systemu Linux RDMA sieci wymagań wstępnych na platformie Azure, zobacz [wysokiej wydajności obliczeniowe rozmiarów maszyn wirtualnych](../../windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
  * Jeśli używasz opcji wdrażania skryptu programu Powershell, należy wdrożyć wszystkie węzły obliczeniowe systemu Linux w ramach jednej chmury usługi do używania połączenia sieciowego RDMA.
  * Po wdrożeniu węzłów Linux należy połączyć SSH, aby wykonać wszelkie dodatkowe zadania administracyjne. Znajdź szczegóły połączenia SSH dla każdej maszyny Wirtualnej systemu Linux w portalu Azure.  
* **Intel MPI** — umożliwiają uruchamianie OpenFOAM w węzłach obliczeniowych SLES 12 HPC na platformie Azure, należy zainstalować środowisko uruchomieniowe Intel MPI biblioteki 5 z [lokacji Intel.com](https://software.intel.com/en-us/intel-mpi-library/). (Intel MPI 5 jest preinstalowany na podstawie CentOS HPC obrazy).  W kolejnym kroku w razie potrzeby zainstaluj Intel MPI na systemie Linux węzłów obliczeniowych. Aby przygotować się do tego kroku, po zarejestrowaniu się Intel, wykonaj łącze w wiadomości e-mail do powiązanych strony sieci web. Następnie skopiuj łącze pobierania pliku .tgz odpowiednią wersję Intel MPI. W tym artykule jest oparta na Intel MPI wersji 5.0.3.048.
* **OpenFOAM źródła pakietu** — Pobierz oprogramowanie OpenFOAM źródła pakietu dla systemu Linux z [lokacji OpenFOAM Foundation](http://openfoam.org/download/2-3-1-source/). Ten artykuł jest oparty na wersja źródła pakietu 2.3.1 dostępny do pobrania jako OpenFOAM 2.3.1.tgz. Postępuj zgodnie z instrukcjami w dalszej części tego artykułu, aby rozpakować i skompilować OpenFOAM w węzłach obliczeniowych systemu Linux.
* **EnSight** (opcjonalnie) — aby wyświetlić wyniki symulacji OpenFOAM, Pobierz i zainstaluj [EnSight](https://www.ceisoftware.com/download/) program wizualizacji i analizy. Informacje licencyjne i pobierania znajdują się w lokacji EnSight.

## <a name="set-up-mutual-trust-between-compute-nodes"></a>Konfigurowanie wzajemnego zaufania między węzły obliczeń
Uruchomienie zadania między węzłami na wielu węzłach Linux wymaga węzłów ufać sobie wzajemnie (przez **rsh** lub **ssh**). Po utworzeniu klastra HPC Pack przy użyciu skryptu wdrażania Microsoft HPC Pack IaaS, skrypt automatycznie konfiguruje stałe wzajemnego zaufania dla konta administratora, które określisz. Dla użytkowników niebędących administratorami utworzonych w domenie klastra trzeba skonfigurować tymczasowego wzajemnego zaufania między węzłami, gdy zadanie jest przydzielony do nich, a zniszczyć relacji po zakończeniu zadania. Aby ustanowić zaufanie dla każdego użytkownika, podaj parę kluczy RSA do klastra używającego HPC Pack relacji zaufania.

### <a name="generate-an-rsa-key-pair"></a>Generowanie pary kluczy RSA
Łatwo można wygenerować pary kluczy RSA, który zawiera klucz publiczny i klucz prywatny, uruchamiając systemu Linux **ssh-keygen** polecenia.

1. Zaloguj się do komputera z systemem Linux.
2. Uruchom następujące polecenie:
   
   ```
   ssh-keygen -t rsa
   ```
   
   > [!NOTE]
   > Naciśnij klawisz **Enter** można użyć wartości domyślnych do czasu ukończenia polecenia. Nie należy wprowadzać hasło tutaj; Po wyświetleniu monitu o podanie hasła, wystarczy nacisnąć klawisz **Enter**.
   > 
   > 
   
   ![Generowanie pary kluczy RSA][keygen]
3. Zmień katalog na katalog ~/.ssh. Klucz prywatny jest przechowywany w id_rsa i klucz publiczny w id_rsa.pub.
   
   ![Klucze prywatne i publiczne][keys]

### <a name="add-the-key-pair-to-the-hpc-pack-cluster"></a>Dodawanie pary kluczy do klastra HPC Pack
1. Należy ustanowić połączenie pulpitu zdalnego z węzła głównego z konta administratora HPC Pack (konta administratora, które można skonfigurować po uruchomieniu skryptu wdrażania).
2. Standardowe procedury systemu Windows Server umożliwia tworzenie konta użytkownika domeny w domenie usługi Active Directory klastra. Na przykład można użyć narzędzia i komputery usługi Active Directory użytkownika w węźle głównym. Przykłady w tym artykule przyjęto założenie, że utworzenie użytkownika domeny o nazwie hpclab\hpcuser.
3. Utwórz plik o nazwie C:\cred.xml i skopiuj do niego dane klucza RSA. Przykładowy plik cred.xml znajduje się na końcu tego artykułu.
   
   ```
   <ExtendedData>
     <PrivateKey>Copy the contents of private key here</PrivateKey>
     <PublicKey>Copy the contents of public key here</PublicKey>
   </ExtendedData>
   ```
4. Otwórz wiersz polecenia, a następnie wprowadź następujące polecenie, aby ustawić danych poświadczeń dla konta hpclab\hpcuser. Możesz użyć **extendeddata** parametr do przekazania nazwę pliku C:\cred.xml utworzony dla kluczy danych.
   
   ```
   hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
   ```
   
   To polecenie zakończy się pomyślnie bez danych wyjściowych. Po ustawieniu poświadczenia dla konta użytkowników, które należy wykonać zadania, cred.xml plik jest przechowywany w bezpiecznym miejscu, lub usuń go.
5. Jeśli parę kluczy RSA jest generowany na jednym z węzłów systemu Linux, pamiętaj, aby usunąć klucze po zakończeniu korzystania z nich. Jeśli HPC Pack znajdzie istniejący plik id_rsa lub plik id_rsa.pub, nie ustawia relacji wzajemnego zaufania.

> [!IMPORTANT]
> Nie zaleca się uruchamiania zadania Linux jako administrator klastra w klastrze udostępnionych, ponieważ zadanie przesłane przez administratora jest wykonywana w ramach konta głównego na węzłach Linux. Jednak zadanie przesłane przez użytkownika z systemem innym niż administrator uruchamia się przy użyciu lokalnego konta użytkownika systemu Linux taką samą nazwę jak użytkownika zadania. W takim przypadku HPC Pack konfiguruje wzajemnego zaufania dla tego użytkownika w systemie Linux w węzłach przydzielony do zadania. Przed uruchomieniem zadania można skonfigurować ręcznie w węzłach Linux użytkownika systemu Linux lub HPC Pack użytkownika automatycznie tworzy podczas przesyłania zadania. Jeśli użytkownik tworzy HPC Pack HPC Pack usuwa je po zakończeniu zadania. Aby zmniejszyć zagrożenia bezpieczeństwa, HPC Pack usuwa kluczy po zakończeniu zadania.
> 
> 

## <a name="set-up-a-file-share-for-linux-nodes"></a>Konfigurowanie udziału plików dla węzłów systemu Linux
Teraz należy skonfigurować standardowy udział SMB dla folderu w węźle głównym. Aby umożliwić węzłów Linux dostęp do plików aplikacji za pomocą wspólnej ścieżki, zainstaluj folderu udostępnionego w węzłach Linux. Jeśli chcesz, możesz użyć innego pliku opcji, takich jak udział plików Azure — zalecane dla wielu scenariuszy - lub udziału NFS do udostępniania. Znajduje się w pliku udostępniania informacji i szczegółowy opis kroków w [wprowadzenie węzły obliczeniowe systemu Linux w klastrze HPC Pack na platformie Azure](hpcpack-cluster.md).

1. Utwórz folder w węźle głównym i udostępnić go dla wszystkich użytkowników, ustawiając uprawnienia odczytu/zapisu. Na przykład udostępnić C:\OpenFOAM w węźle głównym jako \\ \\SUSE12RDMA HN\OpenFOAM. W tym miejscu *SUSE12RDMA HN* jest nazwą hosta węzła głównego.
2. Otwórz okno programu Windows PowerShell i uruchom następujące polecenia:
   
   ```
   clusrun /nodegroup:LinuxNodes mkdir -p /openfoam
   
   clusrun /nodegroup:LinuxNodes mount -t cifs //SUSE12RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
   ```

Pierwsze polecenie tworzy folder o nazwie /openfoam we wszystkich węzłach grupy LinuxNodes. Drugie polecenie instaluje //SUSE12RDMA-HN/OpenFOAM folderu udostępnionego na węzłach Linux z dir_mode i file_mode bits ustawioną 777. *Username* i *hasło* polecenia powinien być poświadczenia użytkownika w węźle głównym.

> [!NOTE]
> "\`" Symbol w drugie polecenie jest symbolem ucieczki dla środowiska PowerShell. "\`," oznacza "," (przecinek znak) jest częścią polecenia.
> 
> 

## <a name="install-mpi-and-openfoam"></a>Zainstaluj MPI i OpenFOAM
Aby uruchomić OpenFOAM jako zadanie MPI sieciowych RDMA, należy skompilować OpenFOAM z bibliotekami Intel MPI. 

Najpierw uruchom kilka **clusrun** polecenia do zainstalowania bibliotek MPI firmy Intel (Jeśli nie jest jeszcze zainstalowany) i OpenFOAM na węzły systemu Linux. Użyj udziału węzła głównego został wcześniej skonfigurowany na potrzeby udostępniania plików instalacji między węzłami systemu Linux.

> [!IMPORTANT]
> Te instalacji i kroki kompilacji to przykłady. Należy pewna znajomość Administracja systemu Linux, aby upewnić się, że kompilatory zależne i biblioteki są poprawnie zainstalowane. Użytkownik może być konieczne zmodyfikowanie niektórych zmiennych środowiskowych lub inne ustawienia dla Twojej wersji Intel MPI i OpenFOAM. Aby uzyskać więcej informacji, zobacz [Intel MPI biblioteki dla systemu Linux Przewodnik instalacji](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html) i [OpenFOAM źródła pakietu instalacji](http://openfoam.org/download/2-3-1-source/) dla danego środowiska.
> 
> 

### <a name="install-intel-mpi"></a>Zainstaluj Intel MPI
Zapisz pobranego pakietu instalacyjnego dla MPI firmy Intel (l_mpi_p_5.0.3.048.tgz w tym przykładzie) w C:\OpenFoam w węźle głównym, aby węzły Linux można uzyskać dostęp do tego pliku z /openfoam. Następnie uruchom **clusrun** zainstalować biblioteki Intel MPI we wszystkich węzłach systemu Linux.

1. Poniższe polecenia Skopiuj pakiet instalacyjny i wyodrębnić je do /opt/intel w każdym węźle.
   
   ```
   clusrun /nodegroup:LinuxNodes mkdir -p /opt/intel
   
   clusrun /nodegroup:LinuxNodes cp /openfoam/l_mpi_p_5.0.3.048.tgz /opt/intel/
   
   clusrun /nodegroup:LinuxNodes tar -xzf /opt/intel/l_mpi_p_5.0.3.048.tgz -C /opt/intel/
   ```
2. Do przeprowadzenia instalacji dyskretnej biblioteki MPI firmy Intel, należy użyć pliku silent.cfg. Przykład można znaleźć w przykładowych plików na końcu tego artykułu. Umieść ten plik w /openfoam folderu udostępnionego. Aby uzyskać szczegółowe informacje o pliku silent.cfg, zobacz [Intel MPI biblioteki dla systemu Linux Przewodnik instalacji - instalacji dyskretnej](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html#silentinstall).
   
   > [!TIP]
   > Upewnij się, że możesz zapisać plik silent.cfg jako plik tekstowy z systemem Linux zakończenia (tylko LF, nie CR LF) wierszy. Ten krok zapewnia, że działa prawidłowo w węzłach systemu Linux.
   > 
   > 
3. Zainstaluj biblioteki MPI Intel w trybie dyskretnym.
   
   ```
   clusrun /nodegroup:LinuxNodes bash /opt/intel/l_mpi_p_5.0.3.048/install.sh --silent /openfoam/silent.cfg
   ```

### <a name="configure-mpi"></a>Skonfiguruj MPI
Do testowania, należy dodać następujące wiersze do /etc/security/limits.conf na każdym z węzłów Linux:

    clusrun /nodegroup:LinuxNodes echo "*               hard    memlock         unlimited" `>`> /etc/security/limits.conf
    clusrun /nodegroup:LinuxNodes echo "*               soft    memlock         unlimited" `>`> /etc/security/limits.conf


Po zaktualizowaniu pliku limits.conf ponownego uruchomienia węzłów systemu Linux. Na przykład, należy użyć następującego **clusrun** polecenia:

```
clusrun /nodegroup:LinuxNodes systemctl reboot
```

Po ponownym uruchomieniu, upewnij się, że folder udostępniony jest instalowany jako /openfoam.

### <a name="compile-and-install-openfoam"></a>Kompilowanie i zainstalować OpenFOAM
Zapisz pobranego pakietu instalacyjnego dla OpenFOAM źródła pakietu (w tym przykładzie OpenFOAM-2.3.1.tgz) do C:\OpenFoam w węźle głównym, aby węzły Linux można uzyskać dostęp do tego pliku z /openfoam. Następnie uruchom **clusrun** poleceń, aby skompilować OpenFOAM we wszystkich węzłach systemu Linux.

1. Tworzenie /opt/OpenFOAM folderu w każdym węźle Linux, skopiuj źródłowy pakiet do tego folderu i wyodrębnij go brak.
   
   ```
   clusrun /nodegroup:LinuxNodes mkdir -p /opt/OpenFOAM
   
   clusrun /nodegroup:LinuxNodes cp /openfoam/OpenFOAM-2.3.1.tgz /opt/OpenFOAM/
   
   clusrun /nodegroup:LinuxNodes tar -xzf /opt/OpenFOAM/OpenFOAM-2.3.1.tgz -C /opt/OpenFOAM/
   ```
2. Aby skompilować OpenFOAM z biblioteką MPI firmy Intel, należy najpierw skonfigurować niektóre zmienne środowiskowe Intel MPI i OpenFOAM. Użyj skryptu bash o nazwie settings.sh do ustawienia zmiennych. Przykład można znaleźć w przykładowych plików na końcu tego artykułu. Umieść ten plik (zapisane przy użyciu zakończenia wierszy w systemie Linux) w /openfoam folderu udostępnionego. Ten plik zawiera także ustawienia dla środowisk uruchomieniowych MPI i OpenFOAM, który zostanie później użyty do uruchomienia zadania OpenFOAM.
3. Instalowanie pakietów zależnych niezbędne do opracowania OpenFOAM. W zależności od dystrybucji systemu Linux należy najpierw dodać repozytorium. Uruchom **clusrun** podobny do następującego polecenia:
   
    ```
    clusrun /nodegroup:LinuxNodes zypper ar http://download.opensuse.org/distribution/13.2/repo/oss/suse/ opensuse
   
    clusrun /nodegroup:LinuxNodes zypper -n --gpg-auto-import-keys install --repo opensuse --force-resolution -t pattern devel_C_C++
    ```
   
    W razie potrzeby SSH do każdego węzła systemu Linux na potrzeby uruchamiania poleceń, aby upewnić się, że działają prawidłowo.
4. Uruchom następujące polecenie, aby skompilować OpenFOAM. Proces kompilacji zajmuje trochę czasu i generuje dużą ilość informacji dziennika do wyjścia standardowego, należy więc **/ przeplatana** opcję, aby wyświetlić dane wyjściowe przeplotu.
   
   ```
   clusrun /nodegroup:LinuxNodes /interleaved source /openfoam/settings.sh `&`& /opt/OpenFOAM/OpenFOAM-2.3.1/Allwmake
   ```
   
   > [!NOTE]
   > "\`" Symbol w poleceniu jest symbolem ucieczki dla środowiska PowerShell. "\`&" oznacza "&" jest częścią polecenia.
   > 
   > 

## <a name="prepare-to-run-an-openfoam-job"></a>Przygotowanie do uruchomienia zadania OpenFOAM
Pobierz teraz gotowy do uruchomienia zadań MPI wywołano sloshingTank3D, które jest jednym z przykładów OpenFoam, dwa węzły systemu Linux. 

### <a name="set-up-the-runtime-environment"></a>Konfigurowanie środowiska uruchomieniowego
Aby skonfigurować środowiska wykonawcze MPI i OpenFOAM w węzłach Linux, uruchom następujące polecenie w oknie programu Windows PowerShell w węźle głównym. (To polecenie jest prawidłowe w systemie SUSE Linux.)

```
clusrun /nodegroup:LinuxNodes cp /openfoam/settings.sh /etc/profile.d/
```

### <a name="prepare-sample-data"></a>Przygotowanie danych przykładowych
Użyj udziału węzła głównego, który został wcześniej skonfigurowany na potrzeby udostępniania plików między węzłami systemu Linux (zainstalowany jako /openfoam).

1. Węzły obliczeniowe SSH do jednego z systemem Linux.
2. Uruchom następujące polecenie, aby skonfigurować środowisko uruchomieniowe OpenFOAM, jeśli jeszcze nie zostało to zrobione.
   
   ```
   $ source /openfoam/settings.sh
   ```
3. Skopiuj przykładowy sloshingTank3D do folderu udostępnionego i przejdź do niego.
   
   ```
   $ cp -r $FOAM_TUTORIALS/multiphase/interDyMFoam/ras/sloshingTank3D /openfoam/
   
   $ cd /openfoam/sloshingTank3D
   ```
4. Gdy używane są parametry domyślne tego przykładu, może upłynąć dziesiątki minut do uruchomienia, należy zmodyfikować niektóre parametry, aby stał się szybsze. Jeden prosty wybór jest zmodyfikować deltaT zmienne kroku czasu i writeInterval w pliku system/controlDict. Ten plik przechowuje wszystkie dane wejściowe dotyczące kontroli czasu i odczytywania i zapisywania danych rozwiązania. Na przykład można zmienić wartość deltaT z 0,05 0,5 i wartość writeInterval z 0,05 0,5.
   
   ![Modyfikuj zmienne w kroku][step_variables]
5. Podaj odpowiednie wartości zmiennych w pliku system/decomposeParDict. W tym przykładzie użyto dwóch węzłów systemu Linux, każdy z 8 rdzeni, a więc Ustaw numberOfSubdomains 16 i n hierarchicalCoeffs do (1 1 16), co oznacza, że równolegle OpenFOAM z 16 procesów. Aby uzyskać więcej informacji, zobacz [Podręcznik użytkownika OpenFOAM: 3.4 aplikacji działa równolegle](http://cfd.direct/openfoam/user-guide/running-applications-parallel/#x12-820003.4).
   
   ![Rozłóż procesów][decompose]
6. Uruchom następujące polecenia w katalogu sloshingTank3D, aby przygotować przykładowych danych.
   
   ```
   $ . $WM_PROJECT_DIR/bin/tools/RunFunctions
   
   $ m4 constant/polyMesh/blockMeshDict.m4 > constant/polyMesh/blockMeshDict
   
   $ runApplication blockMesh
   
   $ cp 0/alpha.water.org 0/alpha.water
   
   $ runApplication setFields  
   ```
7. W węźle głównym powinien pojawić się dane przykładowe pliki są kopiowane do C:\OpenFoam\sloshingTank3D. (C:\OpenFoam jest udostępniony folder w węźle głównym).
   
   ![Pliki danych w węźle głównym][data_files]

### <a name="host-file-for-mpirun"></a>Plik hosta dla mpirun
W tym kroku możesz utworzyć plik hosta (listy węzłów obliczeniowych) który **mpirun** polecenia zastosowań.

1. W jednym z węzłów systemu Linux Utwórz plik o nazwie hostfile w obszarze /openfoam, więc ten plik jest osiągalny w /openfoam/hostfile we wszystkich węzłach systemu Linux.
2. Zapis nazwy węzła Linux do tego pliku. W tym przykładzie plik zawiera następujące nazwy:
   
   ```       
   SUSE12RDMA-LN1
   SUSE12RDMA-LN2
   ```
   
   > [!TIP]
   > Można także utworzyć ten plik na C:\OpenFoam\hostfile w węźle głównym. Jeśli wybierzesz tę opcję, zapisz go jako plik tekstowy z systemem Linux zakończenia wierszy (tylko LF, nie CR LF). Dzięki temu, że działa prawidłowo w węzłach systemu Linux.
   > 
   > 
   
   **Bash skryptu otoki**
   
   Jeśli masz wiele węzłów systemu Linux i chcesz zadania do uruchomienia na tylko niektóre z nich, nie jest dobrym pomysłem jest przy użyciu pliku stałym hosta, ponieważ nie wiadomo, węzły, które zostaną przydzielone do zadania. W takim przypadku zapisu otoka skryptu bash dla **mpirun** automatyczne tworzenie pliku hostów. Można znaleźć przykład otoki skryptu bash o nazwie hpcimpirun.sh na końcu tego artykułu i zapisz go jako /openfoam/hpcimpirun.sh. Ten przykładowy skrypt wykonuje następujące czynności:
   
   1. Konfiguruje zmiennych środowiskowych dla **mpirun**, a niektóre parametry polecenia Dodawanie, uruchamianie zadań MPI za pośrednictwem sieci RDMA. W takim przypadku ustawia następujące zmienne:
      
      * I_MPI_FABRICS=shm:dapl
      * I_MPI_DAPL_PROVIDER=ofa-v2-ib0
      * I_MPI_DYNAMIC_CONNECTION=0
   2. Tworzy plik hostów zgodnie ze środowiska zmiennej $CCP_NODES_CORES, który jest ustawiony przez węzła głównego klastra HPC, gdy zadanie jest aktywny.
      
      Format $CCP_NODES_CORES następujące tego wzorca:
      
      ```
      <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
      ```
      
      gdzie
      
      * `<Number of nodes>` -Liczba węzłów przydzielone do tego zadania.  
      * `<Name of node_n_...>` -Nazwa każdego węzła przydzielone do tego zadania.
      * `<Cores of node_n_...>` -Liczba rdzeni w węźle przydzielone do tego zadania.
      
      Na przykład jeśli zadanie wymaga dwóch węzłów do uruchomienia, $CCP_NODES_CORES jest podobny do
      
      ```
      2 SUSE12RDMA-LN1 8 SUSE12RDMA-LN2 8
      ```
   3. Wywołania **mpirun** polecenie i dołącza dwa parametry wiersza polecenia.
      
      * `--hostfile <hostfilepath>: <hostfilepath>` — Ścieżka pliku hostów skrypt tworzy
      * `-np ${CCP_NUMCPUS}: ${CCP_NUMCPUS}` -Zmienna środowiskowa ustawione przez węzłem głównym HPC Pack, którym przechowywana jest liczba całkowita liczba rdzeni przydzielone do tego zadania. W takim przypadku określa liczbę procesów dla **mpirun**.

## <a name="submit-an-openfoam-job"></a>Prześlij zadanie OpenFOAM
Teraz można przesłać zadania w Menedżerze klastra HPC. Należy przekazać hpcimpirun.sh skryptu w wierszu polecenia dla niektórych zadań zadania.

1. Nawiązać połączenia z głównym węzłem klastra, a następnie uruchom Menedżera klastra HPC.
2. **W przystawce Zarządzanie zasobów**, upewnij się, że węzły obliczeniowe systemu Linux w **Online** stanu. Jeśli nie, zaznacz je i kliknij przycisk **przejdź do trybu Online**.
3. W **zadania zarządzania**, kliknij przycisk **nowe zadanie**.
4. Wprowadź nazwę dla zadania, takie jak *sloshingTank3D*.
   
   ![Szczegóły zadania][job_details]
5. W **zadania zasoby**, wybierz typ zasobu jako "Węzła" i ustaw wartości minimalnej do 2. Ta konfiguracja uruchamia zadanie na dwa węzły Linux, z których każdy ma osiem rdzeni w tym przykładzie.
   
   ![Zasoby zadania][job_resources]
6. Kliknij przycisk **Edycja zadań** nawigacji po lewej stronie, a następnie kliknij polecenie **Dodaj** można dodać zadania do zadania. Dodaj cztery zadania do zadania o następujące wiersze poleceń i ustawienia.
   
   > [!NOTE]
   > Uruchomiona `source /openfoam/settings.sh` konfiguruje środowiska wykonawcze OpenFOAM i MPI, więc każdy z następujących zadań wywołuje przed poleceniem OpenFOAM.
   > 
   > 
   
   * **Zadanie 1**. Uruchom **decomposePar** na wygenerowanie plików danych do uruchomienia **interDyMFoam** równolegle.
     
     * Przypisać jeden węzeł do zadania
     * **Wiersz polecenia** - `source /openfoam/settings.sh && decomposePar -force > /openfoam/decomposePar${CCP_JOBID}.log`
     * **Katalog roboczy** -/ openfoam/sloshingTank3D
     
     Zobacz poniższą ilustrację. Podobnie można skonfigurować pozostałych zadań.
     
     ![Szczegóły zadania 1][task_details1]
   * **Zadanie 2**. Uruchom **interDyMFoam** równolegle obliczeniowe próbki.
     
     * Przypisz dwa węzły do zadania
     * **Wiersz polecenia** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh interDyMFoam -parallel > /openfoam/interDyMFoam${CCP_JOBID}.log`
     * **Katalog roboczy** -/ openfoam/sloshingTank3D
   * **Zadanie 3**. Uruchom **reconstructPar** do scalenia ustawia czas katalogów z każdego katalogu processor_N_ jednego zestawu.
     
     * Przypisać jeden węzeł do zadania
     * **Wiersz polecenia** - `source /openfoam/settings.sh && reconstructPar > /openfoam/reconstructPar${CCP_JOBID}.log`
     * **Katalog roboczy** -/ openfoam/sloshingTank3D
   * **Zadanie 4**. Uruchom **foamToEnsight** równolegle przekonwertować wynik OpenFOAM plików do EnSight sformatowanie i umieścić pliki EnSight w katalogu o nazwie Ensight w przypadku katalogu.
     
     * Przypisz dwa węzły do zadania
     * **Wiersz polecenia** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh foamToEnsight -parallel > /openfoam/foamToEnsight${CCP_JOBID}.log`
     * **Katalog roboczy** -/ openfoam/sloshingTank3D
7. Dodaj zależności do tych zadań w kolejności rosnącej kolejności zadań.
   
   ![Zależności zadań][task_dependencies]
8. Kliknij przycisk **przesyłania** do uruchomienia tego zadania.
   
   Domyślnie HPC Pack przesyła zadanie jako konto bieżącego zalogowanego użytkownika. Po kliknięciu **przesyłania**, może być wyświetlone okno dialogowe z prośbą o wprowadzenie nazwy użytkownika i hasła.
   
   ![Poświadczenia zadania][creds]
   
   W niektórych warunkach HPC Pack są przechowywane informacje o użytkowniku wejściowych przed i nie pokazuj tego okna dialogowego. Aby ponownie wyświetlić pakietu HPC, wprowadź następujące polecenie w wierszu polecenia, a następnie przesłać zadanie.
   
   ```
   hpccred delcreds
   ```
9. To zadanie pobiera dziesiątki minut do kilku godzin zgodnie z parametrami ustawione przykładowej. Mapa cieplna jest widoczny zadania uruchomione w węzłach systemu Linux. 
   
   ![Mapa cieplna][heat_map]
   
   W każdym węźle osiem procesy są uruchomione.
   
   ![Procesy systemu Linux][linux_processes]
10. Po zakończeniu zadania, Znajdź foldery w C:\OpenFoam\sloshingTank3D i pliki dziennika na C:\OpenFoam wyniki zadania.

## <a name="view-results-in-ensight"></a>Wyświetl wyniki w EnSight
Opcjonalnie użyj [EnSight](https://www.ceisoftware.com/) do wizualizowania i analizowania wyników zadania OpenFOAM. Więcej informacji o wizualizacji i animacji EnSight, zobacz [przewodnik wideo](http://www.ceisoftware.com/wp-content/uploads/screencasts/vof_visualization/vof_visualization.html).

1. Po zainstalowaniu EnSight w węźle głównym, należy ją uruchomić.
2. Open C:\OpenFoam\sloshingTank3D\EnSight\sloshingTank3D.case.
   
   Zostanie wyświetlony zbiornika w przeglądarce.
   
   ![Zbiornik w EnSight][tank]
3. Utwórz **Isosurface** z **internalMesh**, a następnie wybierz pozycję zmienna **alpha_water**.
   
   ![Utwórz isosurface][isosurface]
4. Ustawianie koloru **Isosurface_part** utworzony w poprzednim kroku. Na przykład ustawić go do limitu górnego niebieski.
   
   ![Edytuj isosurface kolorów][isosurface_color]
5. Utwórz **woluminu Iso** z **ściany** wybierając **ściany** w **części** panelu i kliknij **Isosurfaces** przycisku w pasku narzędzi.
6. W oknie dialogowym wybierz **typu** jako **Isovolume** i ustaw Min z **zakres Isovolume** 0,5. Aby utworzyć isovolume, kliknij przycisk **Utwórz z zaznaczonych części**.
7. Ustawianie koloru **Iso_volume_part** utworzony w poprzednim kroku. Na przykład ustawić go do dokładnego wody niebieski.
8. Ustawianie koloru **ściany**. Na przykład ustawić ją na przezroczysty kolor biały.
9. Teraz kliknij **odtwarzanie** wyników symulacji.
   
    ![Wynik zbiornika][tank_result]

## <a name="sample-files"></a>Przykładowe pliki
### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>Przykładowy plik konfiguracyjny XML dla wdrożenia klastra za pomocą skryptu programu PowerShell
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
### <a name="sample-silentcfg-file-to-install-mpi"></a>Przykładowy plik silent.cfg, aby zainstalować MPI
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
