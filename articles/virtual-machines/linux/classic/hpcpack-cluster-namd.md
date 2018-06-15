---
title: NAMD z pakietem Microsoft HPC na maszynach wirtualnych systemu Linux | Dokumentacja firmy Microsoft
description: Wdrażanie klastra Microsoft HPC Pack na platformie Azure i uruchom symulacji NAMD z charmrun na wielu węzłach obliczeniowych systemu Linux
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: 76072c6b-ac35-4729-ba67-0d16f9443bd7
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 10/13/2016
ms.author: danlep
ms.openlocfilehash: 61dd49d4bd3183b6b9a78036d6d7d01798e4dc89
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
ms.locfileid: "30842017"
---
# <a name="run-namd-with-microsoft-hpc-pack-on-linux-compute-nodes-in-azure"></a>Uruchamianie oprogramowania NAMD przy użyciu pakietu Microsoft HPC w węzłach obliczeniowych systemu Linux na platformie Azure
Ten artykuł przedstawia sposób do uruchamiania na maszynach wirtualnych Azure Linux obciążenia wysokiej wydajności (HPC). W tym miejscu można skonfigurować [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) klaster na platformie Azure z węzłami obliczeniowymi systemu Linux i uruchamianie [NAMD](http://www.ks.uiuc.edu/Research/namd/) symulację do obliczenia i wizualizację struktury systemu biomolekularnych duże.  

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

* **NAMD** (dla programu Dynamics molekularnej Nanoscale) to pakiet równoległych dynamics molekularnej przeznaczone do symulacji wysokiej wydajności systemów dużych biomolekularnych zawierających miliony atomami. Przykładami tych systemów wirusy, komórki struktury i białek duże. NAMD skaluje setki rdzeniami dla typowych symulacje ponad 500 000 rdzeni dla największy symulacji.
* **Microsoft HPC Pack** udostępnia funkcje do uruchomienia HPC na dużą skalę i aplikacje równoległe w klastrach komputerów lokalnych lub maszyn wirtualnych platformy Azure. Pierwotnie opracowany jako rozwiązaniem w przypadku obciążeń HPC systemu Windows HPC Pack obsługuje teraz aplikacje HPC systemu Linux w systemie Linux obliczeniowe węzła maszyn wirtualnych wdrożonych w klastrze HPC Pack. Zobacz [wprowadzenie węzły obliczeniowe systemu Linux w klastrze HPC Pack na platformie Azure](hpcpack-cluster.md) wprowadzenie.

## <a name="prerequisites"></a>Wymagania wstępne
* **Węzły obliczeniowe HPC Pack klastra z systemem Linux** — wdrożenie klastra HPC Pack z węzłami obliczeniowymi systemu Linux na platformie Azure przy użyciu [szablonu usługi Azure Resource Manager](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) lub [skrypt programu PowerShell Azure](hpcpack-cluster-powershell-script.md). Zobacz [wprowadzenie węzły obliczeniowe systemu Linux w klastrze HPC Pack na platformie Azure](hpcpack-cluster.md) wymagania wstępne i kroki dla każdej opcji. Jeśli wybierzesz opcję wdrożenia skryptu programu PowerShell, zobacz przykładowy plik konfiguracji w przykładowych plików na końcu tego artykułu. Ten plik służy do konfigurowania klastra bazujących na platformie Azure HPC Pack składające się z węzłem głównym systemu Windows Server 2012 R2 i cztery węzły obliczeniowe 6.6 CentOS duży rozmiar. Ten plik należy dostosować zgodnie z wymaganiami środowiska.
* **Pliki oprogramowania i samouczek NAMD** — Pobierz NAMD oprogramowania dla systemu Linux z [NAMD](http://www.ks.uiuc.edu/Research/namd/) lokacji (rejestracja wymagane). W tym artykule jest oparta na NAMD wersji 2.10 i używa [Linux-x86_64 (64-bit Intel/AMD z Ethernet)](http://www.ks.uiuc.edu/Development/Download/download.cgi?UserID=&AccessCode=&ArchiveID=1310) archiwum. Również pobrać [plików samouczka NAMD](http://www.ks.uiuc.edu/Training/Tutorials/#namd). Pliki do pobrania są pliki tar i trzeba narzędzie systemu Windows, aby wyodrębnić pliki na głównym węzłem klastra. Aby wyodrębnić pliki, postępuj zgodnie z instrukcjami w dalszej części tego artykułu. 
* **VMD** (opcjonalnie) — aby wyświetlić wyniki zadania NAMD, Pobierz i zainstaluj program molekularnej wizualizacji [VMD](http://www.ks.uiuc.edu/Research/vmd/) na wybranym komputerze. Bieżąca wersja to 1.9.2. Zobacz VMD lokacji, aby rozpocząć pobieranie.  

## <a name="set-up-mutual-trust-between-compute-nodes"></a>Konfigurowanie wzajemnego zaufania między węzły obliczeń
Uruchomienie zadania między węzłami na wielu węzłach Linux wymaga węzłów ufać sobie wzajemnie (przez **rsh** lub **ssh**). Po utworzeniu klastra HPC Pack przy użyciu skryptu wdrażania Microsoft HPC Pack IaaS, skrypt automatycznie konfiguruje stałe wzajemnego zaufania dla konta administratora, które określisz. Użytkownicy niebędący administratorami, utworzonych w domenie klastra należy skonfigurować tymczasowego wzajemnego zaufania między węzłami, gdy zadanie jest przydzielony do nich. Następnie należy zniszczyć relacji po zakończeniu zadania. Aby to zrobić dla każdego użytkownika, należy podać parę kluczy RSA do klastra HPC Pack używanym do ustanawiania relacji zaufania. Postępuj zgodnie z instrukcjami.

### <a name="generate-an-rsa-key-pair"></a>Generowanie pary kluczy RSA
Łatwo można wygenerować pary kluczy RSA, który zawiera klucz publiczny i klucz prywatny, uruchamiając systemu Linux **ssh-keygen** polecenia.

1. Zaloguj się do komputera z systemem Linux.
2. Uruchom następujące polecenie:
   
   ```bash
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
1. [Połącz przez pulpit zdalny](../../windows/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) z węzłem głównym maszyny Wirtualnej przy użyciu domeny poświadczenia zostanie podane podczas wdrażania klastra (na przykład hpc\clusteradmin). Klaster zarządzania za pomocą węzła głównego.
2. Standardowe procedury systemu Windows Server umożliwia tworzenie konta użytkownika domeny w domenie usługi Active Directory klastra. Na przykład można użyć narzędzia i komputery usługi Active Directory użytkownika w węźle głównym. Przykłady w tym artykule przyjęto założenie, że utworzenie użytkownika domeny o nazwie hpcuser w domenie hpclab (hpclab\hpcuser).
3. Dodaj użytkownika domeny do klastra HPC Pack jako użytkownik klastra. Aby uzyskać instrukcje, zobacz [Dodaj lub usuń użytkowników klastra](https://technet.microsoft.com/library/ff919330.aspx).
4. Utwórz plik o nazwie C:\cred.xml i skopiuj do niego dane klucza RSA. Przykład można znaleźć w przykładowych plików na końcu tego artykułu.
   
   ```
   <ExtendedData>
     <PrivateKey>Copy the contents of private key here</PrivateKey>
     <PublicKey>Copy the contents of public key here</PublicKey>
   </ExtendedData>
   ```
5. Otwórz wiersz polecenia, a następnie wprowadź następujące polecenie, aby ustawić danych poświadczeń dla konta hpclab\hpcuser. Możesz użyć **extendeddata** parametr do przekazania nazwę pliku C:\cred.xml utworzony dla kluczy danych.
   
   ```command
   hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
   ```
   
   To polecenie zakończy się pomyślnie bez danych wyjściowych. Po ustawieniu poświadczenia dla konta użytkowników, które należy wykonać zadania, cred.xml plik jest przechowywany w bezpiecznym miejscu, lub usuń go.
6. Jeśli parę kluczy RSA jest generowany na jednym z węzłów systemu Linux, pamiętaj, aby usunąć klucze po zakończeniu korzystania z nich. HPC Pack nie konfiguruje wzajemnego zaufania w przypadku odnalezienia istniejącego pliku id_rsa lub id_rsa.pub pliku.

> [!IMPORTANT]
> Nie zaleca się uruchamiania zadania Linux jako administrator klastra w klastrze udostępnionych, ponieważ zadanie przesłane przez administratora jest wykonywana w ramach konta głównego na węzłach Linux. Zadania przesłane przez użytkownika z systemem innym niż administrator uruchamia przy użyciu lokalnego konta użytkownika systemu Linux o nazwie identycznej z nazwą użytkownika zadania. W takim przypadku HPC Pack konfiguruje wzajemnego zaufania dla tego użytkownika w systemie Linux we wszystkich węzłach przydzielony do zadania. Przed uruchomieniem zadania można skonfigurować ręcznie w węzłach Linux użytkownika systemu Linux lub HPC Pack użytkownika automatycznie tworzy podczas przesyłania zadania. Jeśli użytkownik tworzy HPC Pack HPC Pack usuwa je po zakończeniu zadania. Aby zmniejszyć zagrożenie, klucze są usuwane po zakończeniu zadania w węzłach.
> 
> 

## <a name="set-up-a-file-share-for-linux-nodes"></a>Konfigurowanie udziału plików dla węzłów systemu Linux
Teraz skonfigurować udział plików SMB i zainstalować folder udostępniony na wszystkich węzłach Linux umożliwia węzłów Linux dostęp do plików NAMD z wspólnej ścieżki. Poniżej przedstawiono kroki, aby zainstalować w węźle głównym folderu udostępnionego. Udział jest zalecane dla dystrybucji takie jak CentOS 6.6, które aktualnie nie obsługują usługę Azure plików. Jeśli węzły Linux obsługują udziału plików platformy Azure, zobacz [jak używać magazynu plików Azure z systemem Linux](../../../storage/files/storage-how-to-use-files-linux.md). W przypadku pliku dodatkowe opcje udostępniania pakietem HPC, zobacz [wprowadzenie węzły obliczeniowe systemu Linux w klastrze HPC Pack na platformie Azure](hpcpack-cluster.md).

1. Utwórz folder w węźle głównym i udostępnić go dla wszystkich użytkowników, ustawiając uprawnienia odczytu/zapisu. W tym przykładzie \\ \\CentOS66HN\Namd jest nazwą folderu, gdzie CentOS66HN jest nazwą hosta węzła głównego.
2. Utwórz podfolder o nazwie namd2 w folderze udostępnionym. W namd2 należy utworzyć inny podfolder o nazwie namdsample.
3. Wyodrębnij pliki NAMD w folderze przy użyciu wersji systemu Windows z **tar** lub innego narzędzia systemu Windows, który operuje na archiwa tar. 
   
   * Wyodrębnij z archiwum tar NAMD do \\ \\CentOS66HN\Namd\namd2.
   * Wyodrębnij pliki z samouczkiem, w obszarze \\ \\CentOS66HN\Namd\namd2\namdsample.
4. Otwórz okno programu Windows PowerShell i uruchom następujące polecenia, aby zainstalować w folderze udostępnionym na węzłach systemu Linux.
   
    ```command
    clusrun /nodegroup:LinuxNodes mkdir -p /namd2
   
    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS66HN/Namd/namd2 /namd2 -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

Pierwsze polecenie tworzy folder o nazwie /namd2 we wszystkich węzłach grupy LinuxNodes. Drugie polecenie instaluje //CentOS66HN/Namd/namd2 folderu udostępnionego na folder z dir_mode i file_mode bits ustawioną 777. *Username* i *hasło* polecenia powinien być poświadczenia użytkownika w węźle głównym.

> [!NOTE]
> "\`" Symbol w drugie polecenie jest symbolem ucieczki dla środowiska PowerShell. "\`," oznacza "," (przecinek znak) jest częścią polecenia.
> 
> 

## <a name="create-a-bash-script-to-run-a-namd-job"></a>Utwórz skrypt Bash, aby uruchomić zadanie NAMD
Zadanie NAMD musi *wstawienia* plik **charmrun** można określić liczbę węzłów do użycia podczas uruchamiania procesów NAMD. Możesz użyć skryptu Bash, który generuje plik wstawienia i uruchamia **charmrun** z tym plikiem wstawienia. Następnie można przesłać zadania NAMD w Menedżer klastra HPC, który wywołuje ten skrypt.

Za pomocą dowolnego edytora tekstu, utworzyć skrypt Bash w folderze /namd2 zawierającym pliki programu NAMD i nadaj mu nazwę hpccharmrun.sh. W przypadku szybkiego Weryfikacja koncepcji, skopiuj przykładowy skrypt hpccharmrun.sh podana na końcu tego artykułu, przejdź do [przesłać zadania NAMD](#submit-a-namd-job).

> [!TIP]
> Zapisz skrypt jako plik tekstowy z systemem Linux zakończenia (tylko LF, nie CR LF) wierszy. Dzięki temu, że działa prawidłowo w węzłach systemu Linux.
> 
> 

Poniżej przedstawiono szczegółowe informacje o czego ten skrypt bash. 

1. Zdefiniuj niektóre zmienne.
   
   ```bash
   #!/bin/bash
   
   # The path of this script
   SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
   # Charmrun command
   CHARMRUN=${SCRIPT_PATH}/charmrun
   # Argument of ++nodelist
   NODELIST_OPT="++nodelist"
   # Argument of ++p
   NUMPROCESS="+p"
   ```
2. Pobierz informacje węzła ze zmiennych środowiskowych. $NODESCORES przechowuje listę słów podziału z $CCP_NODES_CORES. $COUNT jest rozmiarem $NODESCORES.
   
   ```
   # Get node information from the environment variables
   NODESCORES=(${CCP_NODES_CORES})
   COUNT=${#NODESCORES[@]}
   ```    
   
   Format zmiennej CCP_NODES_CORES $ wygląda następująco:
   
   ```
   <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>…
   ```
   
   Ta zmienna Wyświetla całkowitą liczbę węzłów, nazwy węzła i liczba rdzeni w każdym węźle, które są przydzielone do zadania. Na przykład jeśli zadanie wymaga 10 rdzeni do uruchomienia, wartość $CCP_NODES_CORES jest podobny do:
   
   ```
   3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 2
   ```
3. Jeśli nie ustawiono zmienną CCP_NODES_CORES $, uruchom **charmrun** bezpośrednio. (Ten może występować tylko po uruchomieniu tego skryptu bezpośrednio na węzły systemu Linux.)
   
   ```
   if [ ${COUNT} -eq 0 ]
   then
     # CCP_NODES is_CORES is not found or is empty, so just run charmrun without nodelist arg.
     #echo ${CHARMRUN} $*
     ${CHARMRUN} $*
   ```
4. Lub Utwórz plik wstawienia **charmrun**.
   
   ```
   else
     # Create the nodelist file
     NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$
   
     # Write the head line
     echo "group main" > ${NODELIST_PATH}
   
     # Get every node name and number of cores and write into the nodelist file
     I=1
     while [ ${I} -lt ${COUNT} ]
     do
         echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
         let "I=${I}+2"
     done
   ```
5. Uruchom **charmrun** z plikiem wstawienia pobrać stanu powrotu, a następnie usuń plik wstawienia na końcu.
   
   ${CCP_NUMCPUS} jest ustawiony przez węzłem głównym HPC Pack innej zmiennej środowiskowej. Liczba całkowita liczba rdzeni przydzielone do tego zadania jest przechowywana. Ten element służy do określ liczbę procesów dla charmrun.
   
   ```
   # Run charmrun with nodelist arg
   #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
   ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
   
   RTNSTS=$?
   rm -f ${NODELIST_PATH}
   fi
   
   ```
6. Zakończenie pracy **charmrun** stan powrotu.
   
   ```
   exit ${RTNSTS}
   ```

Poniżej znajduje się w pliku wstawienia skrypt generuje informacje:

```
group main
host <Name of node1> ++cpus <Cores of node1>
host <Name of node2> ++cpus <Cores of node2>
…
```

Na przykład:

```
group main
host CENTOS66LN-00 ++cpus 4
host CENTOS66LN-01 ++cpus 4
host CENTOS66LN-03 ++cpus 2
```

## <a name="submit-a-namd-job"></a>Prześlij zadanie NAMD
Teraz można przystąpić do przesyłania zadania NAMD w Menedżerze klastra HPC.

1. Nawiązać połączenia z głównym węzłem klastra, a następnie uruchom Menedżera klastra HPC.
2. W **zarządzanie zasobami**, upewnij się, że węzły obliczeniowe systemu Linux w **Online** stanu. Jeśli nie, zaznacz je i kliknij przycisk **przejdź do trybu Online**.
3. W **zadania zarządzania**, kliknij przycisk **nowe zadanie**.
4. Wprowadź nazwę dla zadania, takie jak *hpccharmrun*.
   
   ![Nowe zadanie HPC][namd_job]
5. Na **szczegóły zadania** w obszarze **zasobów zadania**, wybierz typ zasobu jako **węzła** i ustaw **Minimum** 3. , możemy uruchomić zadania na trzy węzły systemu Linux i każdy węzeł ma cztery rdzenie.
   
   ![Zasoby zadania][job_resources]
6. Kliknij przycisk **Edycja zadań** nawigacji po lewej stronie, a następnie kliknij polecenie **Dodaj** można dodać zadania do zadania.    
7. Na **szczegółów zadań i Przekierowanie We/Wy** ustaw następujące wartości:
   
   * **Wiersz polecenia** -
     `/namd2/hpccharmrun.sh ++remote-shell ssh /namd2/namd2 /namd2/namdsample/1-2-sphere/ubq_ws_eq.conf > /namd2/namd2_hpccharmrun.log`
     
     > [!TIP]
     > Poprzedni wiersz polecenia jest jednego polecenia bez podziałów wierszy. Zawija się na kilka wierszy w obszarze **wiersza polecenia**.
     > 
     > 
   * **Katalog roboczy** -/namd2
   * **Co najmniej** — 3
     
     ![Szczegóły zadania][task_details]
     
     > [!NOTE]
     > W tym miejscu ustawić katalogu roboczego ponieważ **charmrun** próbuje przejdź do tego samego katalogu roboczego w każdym węźle. Jeśli katalog roboczy nie jest ustawiona, HPC Pack uruchamia polecenia w losowo wybranej nazwie folder utworzony w jednym z węzłów systemu Linux. Powoduje to następujący błąd w innych węzłach: `/bin/bash: line 37: cd: /tmp/nodemanager_task_94_0.mFlQSN: No such file or directory.` Aby uniknąć tego problemu, określ ścieżkę folderu, które są dostępne przez wszystkie węzły katalogu roboczego.
     > 
     > 
8. Kliknij przycisk **OK** , a następnie kliknij przycisk **przesyłania** do uruchomienia tego zadania.
   
   Domyślnie HPC Pack przesyła zadanie jako konto bieżącego zalogowanego użytkownika. Okno dialogowe może wyświetlić monit o wprowadzenie nazwy użytkownika i hasła, po kliknięciu **przesyłania**.
   
   ![Poświadczenia zadania][creds]
   
   W niektórych warunkach HPC Pack są przechowywane informacje o użytkowniku wejściowych przed i nie pokazuj tego okna dialogowego. Aby ponownie wyświetlić pakietu HPC, wprowadź następujące polecenie w wierszu polecenia, a następnie przesłać zadanie.
   
   ```command
   hpccred delcreds
   ```
9. Zadanie może zająć kilka minut, aby zakończyć.
10. Znajdź dziennik zadań na \\ <headnodeName>\Namd\namd2\namd2_hpccharmrun.log i pliki wyjściowe w \\ <headnodeName>\Namd\namd2\namdsample\1-2-sphere\.
11. Opcjonalnie można uruchomić VMD, aby wyświetlić wyniki zadania. Kroki do wizualizacji NAMD wyjściowe plików (w tym przypadku ubiquitin białka związku w dziedzinie limitu górnego) wykraczają poza zakres tego artykułu. Zobacz [samouczek NAMD](http://www.life.illinois.edu/emad/biop590c/namd-tutorial-unix-590C.pdf) szczegółowe informacje.
    
    ![Wyniki zadania][vmd_view]

## <a name="sample-files"></a>Przykładowe pliki
### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>Przykładowy plik konfiguracyjny XML dla wdrożenia klastra za pomocą skryptu programu PowerShell
```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
      <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpclab.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS66HN</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS66LN-%00%</VMNamePattern>
    <ServiceName>MyLnxCNService</ServiceName>
     <VMSize>Large</VMSize>
     <NodeCount>4</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-66-20150325</ImageName>
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

### <a name="sample-hpccharmrunsh-script"></a>Przykładowy skrypt hpccharmrun.sh
```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
# Charmrun command
CHARMRUN=${SCRIPT_PATH}/charmrun
# Argument of ++nodelist
NODELIST_OPT="++nodelist"
# Argument of ++p
NUMPROCESS="+p"

# Get node information from ENVs
# CCP_NODES_CORES=3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 4
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # If CCP_NODES_CORES is not found or is empty, just run the charmrun without nodelist arg.
    #echo ${CHARMRUN} $*
    ${CHARMRUN} $*
else
    # Create the nodelist file
    NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

    # Write the head line
    echo "group main" > ${NODELIST_PATH}

    # Get every node name & cores and write into the nodelist file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the charmrun with nodelist arg
    #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
    ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}
```

 

<!--Image references-->
[keygen]:media/hpcpack-cluster-namd/keygen.png
[keys]:media/hpcpack-cluster-namd/keys.png
[namd_job]:media/hpcpack-cluster-namd/namd_job.png
[job_resources]:media/hpcpack-cluster-namd/job_resources.png
[creds]:media/hpcpack-cluster-namd/creds.png
[task_details]:media/hpcpack-cluster-namd/task_details.png
[vmd_view]:media/hpcpack-cluster-namd/vmd_view.png
