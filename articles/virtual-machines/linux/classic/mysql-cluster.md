---
title: Clusterize MySQL z równoważeniem obciążenia zestawami | Dokumentacja firmy Microsoft
description: Konfigurowanie równoważenia obciążenia, wysoka dostępność, klaster programu Linux MySQL utworzone z klasycznym modelu wdrażania na platformie Azure
services: virtual-machines-linux
documentationcenter: ''
author: bureado
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 6c413a16-e9b5-4ffe-a8a3-ae67046bbdf3
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/14/2015
ms.author: jparrel
ms.openlocfilehash: e2671def47879e3d4eae000c9084cd458e29b933
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
ms.locfileid: "30841268"
---
# <a name="use-load-balanced-sets-to-clusterize-mysql-on-linux"></a>Umożliwia zestawów o zrównoważonym obciążeniu clusterize bazy danych MySQL na systemie Linux
> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [usługi Azure Resource Manager](../../../resource-manager-deployment-model.md) i klasycznym. Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. A [szablonu usługi Resource Manager](https://azure.microsoft.com/documentation/templates/mysql-replication/) jest dostępna, gdy należy wdrożyć klaster programu MySQL.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

W tym artykule Eksploruje i przedstawia różne podejścia można wdrożyć wysokiej dostępności usług opartych na systemie Linux w systemie Microsoft Azure, wysokiej dostępności serwera MySQL jako Elementarz eksploracji. Film pokazujący to rozwiązanie jest dostępne na [Channel 9](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL).

Firma Microsoft będzie konspektu nieudostępnianych jednostkach, dwa węzły, punktowe MySQL rozwiązanie zapewniające wysoką dostępność na podstawie DRBD, Corosync i rozrusznik. Tylko jeden węzeł uruchamia MySQL naraz. Odczytywanie i zapisywanie z zasobu DRBD również jest ograniczona do tylko jeden węzeł naraz.

Ponieważ w systemie Microsoft Azure użyjesz zestawów o zrównoważonym obciążeniu zapewnienie wykrywania funkcjonalność i punktu końcowego okrężnego, usuwania i skuteczne odzyskanie adres VIP nie istnieje potrzeba rozwiązania adresu VIP, takich jak LV. Adres VIP jest globalnie podlegającego routingowi adres IPv4 przypisany przez Microsoft Azure, podczas tworzenia usługi w chmurze.

Są dostępne jako Maszynę wirtualną na innych możliwych architektury MySQL, łącznie z klastra NBD, Percona Galera i kilka rozwiązań oprogramowania pośredniczącego, w tym co najmniej jeden [magazynu maszyny Wirtualnej](http://vmdepot.msopentech.com). Tak długo, jak te rozwiązania mogą replikować w emisji pojedynczej i multiemisji lub emisji i nie należy polegać na magazyn udostępniony lub wiele interfejsów sieciowych, scenariusze powinna być łatwa do wdrożenia w systemie Microsoft Azure.

Te klastrowanie architektury może zostać rozszerzony do innych produktów, takich jak PostgreSQL i OpenLDAP w podobny sposób. Na przykład procedury Równoważenie obciążenia bez udostępniania żadnego pomyślnie była testowana przy wielu wzorca OpenLDAP i można go oglądać w naszym blogu Channel 9.

## <a name="get-ready"></a>Przygotuj się
Niezbędne są następujące zasoby i możliwości:

  - Konto Microsoft Azure z prawidłową subskrypcją, można utworzyć co najmniej dwóch maszyn wirtualnych (XS został użyty w tym przykładzie)
  - Sieci i podsieci
  - Grupa koligacji
  - Zestaw dostępności
  - Możliwość tworzenia wirtualnych dysków twardych w tym samym regionie co usługa w chmurze i dołącz je do maszyn wirtualnych systemu Linux

### <a name="tested-environment"></a>Przetestowany środowiska
* Ubuntu 13.10
  * DRBD
  * Serwer MySQL
  * Corosync i rozrusznik

### <a name="affinity-group"></a>Grupa koligacji
Utworzyć grupę koligacji dla rozwiązania, logując się do portalu Azure, wybierając **ustawienia**oraz tworzenia grupy koligacji. Przydzielone zasoby utworzone później zostanie przypisany do tej grupy koligacji.

### <a name="networks"></a>Sieci
Utworzono nową sieć, i utworzeniu podsieci w sieci. W tym przykładzie używane sieci 10.10.10.0/24 z podsieci o prefiksie/24 tylko jeden wewnątrz.

### <a name="virtual-machines"></a>Maszyny wirtualne
Pierwsza maszyna wirtualna 13.10 Ubuntu jest tworzona przy użyciu obrazu Endorsed Ubuntu galerii i nosi nazwę `hadb01`. Nową usługę w chmurze jest tworzony w procesie o nazwie hadb. Ta nazwa przedstawiono charakter równoważeniem obciążenia, współdzielonych mające usługę po dodaniu więcej zasobów. Tworzenie `hadb01` ma procesu i jest wypełniane przy użyciu portalu. Punkt końcowy SSH jest tworzony automatycznie, a wybrano nowej sieci. Teraz możesz utworzyć zbiór dostępności dla maszyn wirtualnych.

Po utworzeniu pierwszej maszyny Wirtualnej (technicznie, podczas tworzenia usługi w chmurze), należy utworzyć maszynę Wirtualną z drugiej, `hadb02`. Dla drugiego maszyny Wirtualnej, użyj maszyny Wirtualnej systemu Ubuntu 13.10 z galerii przy użyciu portalu, ale użyj istniejącej usługi chmury, `hadb.cloudapp.net`, zamiast tworzyć nowy. Sieci i dostępność zestawu powinna być zaznaczona automatycznie. Punkt końcowy SSH zostanie utworzony, zbyt.

Po utworzeniu obie maszyny wirtualne, zanotuj numer portu SSH `hadb01` (TCP 22) i `hadb02` (automatycznie przypisywana przez platformę Azure).

### <a name="attached-storage"></a>Magazyn dołączony
Dołączanie nowego dysku do obie maszyny wirtualne i tworzenia dysków 5 GB w procesie. Dyski znajdują się w kontenerze wirtualnego dysku twardego na użytek dysków główne systemu operacyjnego. Po dyski są tworzone i dołączyć, jest niepotrzebna ponowne uruchomienie systemu Linux, ponieważ jądra będą widzieć nowego urządzenia. To urządzenie jest zwykle `/dev/sdc`. Sprawdź `dmesg` dla danych wyjściowych.

Na każdej maszynie Wirtualnej, Utwórz partycję za pomocą `cfdisk` (partycji podstawowej, Linux) i zapisu w nowej tabeli partycji. Nie należy tworzyć system plików na tej partycji.

## <a name="set-up-the-cluster"></a>Konfigurowanie klastra
Umożliwia instalowanie Corosync, rozrusznik i DRBD na obu maszynach wirtualnych Ubuntu APT. Aby to zrobić z `apt-get`, uruchom następujący kod:

    sudo apt-get install corosync pacemaker drbd8-utils.

W tej chwili nie należy instalować MySQL. Debian i Ubuntu skrypty instalacyjne zainicjować katalog danych MySQL na `/var/lib/mysql`, ale ponieważ katalog zostanie zastąpiony przez system plików DRBD, musisz zainstalować MySQL później.

Sprawdź (przy użyciu `/sbin/ifconfig`) czy obie maszyny wirtualne korzystają z adresów w podsieci 10.10.10.0/24 i że one może wysyłać polecenia ping wzajemnie według nazwy. Można również użyć `ssh-keygen` i `ssh-copy-id` się upewnić, że obie maszyny wirtualne mogą komunikować się za pośrednictwem SSH bez konieczności wprowadzania hasła.

### <a name="set-up-drbd"></a>Konfigurowanie DRBD
Utwórz zasób DRBD używającej odpowiadającego `/dev/sdc1` partycji w celu utworzenia `/dev/drbd1` zasobów, które mogą być sformatowane przy użyciu ext3 i używane w węzłach głównych i dodatkowych.

1. Otwórz `/etc/drbd.d/r0.res` i skopiuj następujące definicji zasobu na obu maszynach wirtualnych:

        resource r0 {
          on `hadb01` {
            device  /dev/drbd1;
            disk   /dev/sdc1;
            address  10.10.10.4:7789;
            meta-disk internal;
          }
          on `hadb02` {
            device  /dev/drbd1;
            disk   /dev/sdc1;
            address  10.10.10.5:7789;
            meta-disk internal;
          }
        }

2. Inicjowanie zasobu za pomocą `drbdadm` na obu maszynach wirtualnych:

        sudo drbdadm -c /etc/drbd.conf role r0
        sudo drbdadm up r0

3. W przypadku podstawowej maszyny Wirtualnej (`hadb01`), wymusić zasobów DRBD własność (podstawowe):

        sudo drbdadm primary --force r0

Jeśli należy zbadać zawartość/proc/drbd (`sudo cat /proc/drbd`) na obu maszynach wirtualnych, powinien zostać wyświetlony `Primary/Secondary` na `hadb01` i `Secondary/Primary` na `hadb02`, zgodnie z rozwiązaniem w tym momencie. Dysk 5 GB jest synchronizowany za pośrednictwem sieci 10.10.10.0/24 bez dodatkowych opłat, klientów.

Po zsynchronizowaniu dysku można utworzyć systemu plików na `hadb01`. Do celów testowych, użyliśmy ext2, ale następujący kod utworzy ext3 systemu plików:

    mkfs.ext3 /dev/drbd1

### <a name="mount-the-drbd-resource"></a>Instalowanie zasobów DRBD
Teraz możesz zainstalować na zasoby DRBD `hadb01`. Użyj debian i pochodne `/var/lib/mysql` jako katalog danych na MySQL. Ponieważ nie zainstalowano MySQL, utworzyć katalogu i instalacji DRBD zasobów. Aby wykonać tę opcję, uruchom następujący kod na `hadb01`:

    sudo mkdir /var/lib/mysql
    sudo mount /dev/drbd1 /var/lib/mysql

## <a name="set-up-mysql"></a>Konfigurowanie MySQL
Teraz wszystko jest gotowe do zainstalowania na MySQL `hadb01`:

    sudo apt-get install mysql-server

Aby uzyskać `hadb02`, są dostępne dwie opcje. Można zainstalować mysql serwer, który będzie utworzyć /var/lib/mysql, wypełnienie nowy katalog danych, a następnie usuń zawartość. Aby wykonać tę opcję, uruchom następujący kod na `hadb02`:

    sudo apt-get install mysql-server
    sudo service mysql stop
    sudo rm –rf /var/lib/mysql/*

Drugą opcją jest tryb failover `hadb02` , a następnie zainstaluj mysql serwer istnieje. Skrypty instalacyjne zauważyć istniejącej instalacji i nie będzie on touch.

Uruchom poniższy kod `hadb01`:

    sudo drbdadm secondary –force r0

Uruchom poniższy kod `hadb02`:

    sudo drbdadm primary –force r0
    sudo apt-get install mysql-server

Jeśli nie planujesz do trybu failover DRBD teraz, pierwsza opcja jest łatwiejsze, ale raczej mniej atrakcyjny. Po należy wybrać tę opcję, należy rozpocząć pracę nad bazy danych MySQL. Uruchom poniższy kod `hadb02` (lub jednego z tych serwerów jest aktywna, zgodnie z DRBD):

    mysql –u root –p
    CREATE DATABASE azureha;
    CREATE TABLE things ( id SERIAL, name VARCHAR(255) );
    INSERT INTO things VALUES (1, "Yet another entity");
    GRANT ALL ON things.\* TO root;

> [!WARNING]
> Tej ostatniej instrukcji wyłącza uwierzytelniania dla użytkownika głównego w tej tabeli. To powinna zostać zastąpiona przez klasy produkcji PRZYZNAĆ instrukcje i jest dołączany wyłącznie w celach ilustracyjnych.

Jeśli ma być zapytania z poza maszyn wirtualnych (co jest przeznaczenie tego przewodnika), należy włączyć sieci dla programu MySQL. Na obu maszynach wirtualnych, należy otworzyć `/etc/mysql/my.cnf` i przejdź do `bind-address`. Zmień adres z 127.0.0.1 0.0.0.0. Po zapisaniu pliku, należy wydać `sudo service mysql restart` na serwerze podstawowym bieżącej.

### <a name="create-the-mysql-load-balanced-set"></a>Utwórz zestaw o zrównoważonym obciążeniu — MySQL
Wróć do portalu, przejdź do `hadb01`i wybierz polecenie **punkty końcowe**. Aby utworzyć punkt końcowy, wybierz polecenie MySQL (TCP 3306) z listy rozwijanej, a następnie **zestaw o zrównoważonym obciążeniu nowe Utwórz**. Nazwa punktu końcowego równoważeniem obciążenia `lb-mysql`. Ustaw **czas** na 5 sekund, minimalna.

Po utworzeniu punktu końcowego, przejdź do `hadb02`, wybierz **punkty końcowe**i utworzyć punktu końcowego. Wybierz `lb-mysql`, a następnie wybierz z listy rozwijanej MySQL. Umożliwia także wiersza polecenia platformy Azure dla tego kroku.

Masz teraz wszystko, co jest potrzebne do ręcznego operacji klastra.

### <a name="test-the-load-balanced-set"></a>Testowanie zestawu z równoważeniem obciążenia
Za pomocą dowolnego klienta MySQL lub przy użyciu określonych aplikacji, takich jak phpMyAdmin uruchomione jako witryny sieci Web platformy Azure, można wykonać testy z poza maszyny. W tym przypadku użyto MySQL przez narzędzie wiersza polecenia w innym polu Linux:

    mysql azureha –u root –h hadb.cloudapp.net –e "select * from things;"

### <a name="manually-failing-over"></a>Ręcznie przechodzenie w tryb failover
Tryb failover można symulować zamykanie MySQL, przełączając DRBD w podstawowej i ponownie uruchomić MySQL.

Aby wykonać to zadanie, uruchom następujący kod na hadb01:

    service mysql stop && umount /var/lib/mysql ; drbdadm secondary r0

Następnie na hadb02:

    drbdadm primary r0 ; mount /dev/drbd1 /var/lib/mysql && service mysql start

Po przejścia w tryb failover ręcznie, można powtarzać zapytania zdalnego i powinien działać prawidłowo.

## <a name="set-up-corosync"></a>Konfigurowanie Corosync
Corosync jest podstawowy klaster infrastruktury wymaganej do rozrusznik do pracy. Pulsu (i innych metod, takich jak Ultramonkey) Corosync jest podziału funkcji CRM rozrusznik pozostaje więcej podobne do pulsu w działaniu.

Głównym ograniczeniem dla Corosync na platformie Azure jest Corosync preferuje multiemisji za pośrednictwem emisji komunikacji emisji pojedynczej, że sieci Microsoft Azure obsługuje tylko emisji pojedynczej.

Na szczęście Corosync ma pracy tryb emisji pojedynczej. Tylko rzeczywistych ograniczenie jest, że ponieważ wszystkie węzły nie komunikują się ze sobą, należy zdefiniować węzłów w plikach konfiguracji, w tym ich adresy IP. Możemy użyć plików przykład Corosync dla emisji pojedynczej i Zmień powiązanie adresu, węzeł listy i katalogi rejestrowania (używa Ubuntu `/var/log/corosync` podczas przykładzie pliki używają `/var/log/cluster`) i Włącz narzędzia kworum.

> [!NOTE]
> Należy użyć następującego `transport: udpu` dyrektywy i ręcznie zdefiniowanych adresów IP dla obu węzłów.

Uruchom poniższy kod `/etc/corosync/corosync.conf` dla obu węzłów:

    totem {
      version: 2
      crypto_cipher: none
      crypto_hash: none
      interface {
        ringnumber: 0
        bindnetaddr: 10.10.10.0
        mcastport: 5405
        ttl: 1
      }
      transport: udpu
    }

    logging {
      fileline: off
      to_logfile: yes
      to_syslog: yes
      logfile: /var/log/corosync/corosync.log
      debug: off
      timestamp: on
      logger_subsys {
        subsys: QUORUM
        debug: off
        }
      }

    nodelist {
      node {
        ring0_addr: 10.10.10.4
        nodeid: 1
      }

      node {
        ring0_addr: 10.10.10.5
        nodeid: 2
      }
    }

    quorum {
      provider: corosync_votequorum
    }

Skopiuj ten plik konfiguracji na obu maszynach wirtualnych i uruchomić Corosync w obu węzłów:

    sudo service start corosync

Wkrótce po uruchomieniu usługi klastra należy wprowadzić w bieżącym pierścień i powinien zostać utworzony kworum. Ta funkcja możemy można sprawdzić, przeglądając dzienniki lub uruchamiając następujący kod:

    sudo corosync-quorumtool –l

Zostaną wyświetlone dane wyjściowe podobne do następujących obrazów:

![corosync quorumtool - l przykładowe dane wyjściowe](./media/mysql-cluster/image001.png)

## <a name="set-up-pacemaker"></a>Konfigurowanie rozrusznik
Rozrusznik korzysta z klastra do monitorowania zasobów, zdefiniuj, gdy kolory podstawowe przestaną działać i przełączanie tych zasobów do pomocnicze bazy danych. Można zdefiniować zasoby z zbiór dostępnych skryptów lub skryptów (init jak) najmniej znaczący BAJT, między innymi opcjami.

Chcemy rozrusznik do "właścicielem" zasobu DRBD, punkt instalacji i usługi MySQL. Jeśli rozrusznik Włączanie i wyłączanie DRBD, instalacji i Odinstaluj go, a następnie uruchomić i zatrzymać MySQL w odpowiedniej kolejności zły najważniejsza z serwera podstawowego, instalacja została ukończona.

Po zainstalowaniu rozrusznik konfiguracji powinna być prosta, coś, takich jak:

    node $id="1" hadb01
      attributes standby="off"
    node $id="2" hadb02
      attributes standby="off"

1. Sprawdź konfigurację, uruchamiając `sudo crm configure show`.
2. Następnie utwórz plik (takie jak `/tmp/cluster.conf`) z następującymi zasobami:

        primitive drbd_mysql ocf:linbit:drbd \
              params drbd_resource="r0" \
              op monitor interval="29s" role="Master" \
              op monitor interval="31s" role="Slave"

        ms ms_drbd_mysql drbd_mysql \
              meta master-max="1" master-node-max="1" \
                clone-max="2" clone-node-max="1" \
                notify="true"

        primitive fs_mysql ocf:heartbeat:Filesystem \
              params device="/dev/drbd/by-res/r0" \
              directory="/var/lib/mysql" fstype="ext3"

        primitive mysqld lsb:mysql

        group mysql fs_mysql mysqld

        colocation mysql_on_drbd \
               inf: mysql ms_drbd_mysql:Master

        order mysql_after_drbd \
               inf: ms_drbd_mysql:promote mysql:start

        property stonith-enabled=false

        property no-quorum-policy=ignore

3. Załadowanie pliku do konfiguracji. Należy to zrobić w jeden węzeł.

        sudo crm configure
          load update /tmp/cluster.conf
          commit
          exit

4. Upewnij się, że rozrusznik uruchomienie przy rozruchu w obu węzłów:

        sudo update-rc.d pacemaker defaults

5. Za pomocą `sudo crm_mon –L`, sprawdź, czy jeden z węzłów stał się głównego klastra i działa wszystkich zasobów. Aby sprawdzić, czy zasoby działają, można użyć instalacji i ps.

Poniższy zrzut ekranu przedstawia `crm_mon` z jednym węzłem zatrzymane (Zamknij wybierając klawisze Ctrl + C):

![węzeł crm_mon zatrzymana](./media/mysql-cluster/image002.png)

Ten zrzut ekranu przedstawia węzłów, jeden z nich i jeden podrzędny:

![crm_mon operacyjne wzorzec/podrzędny](./media/mysql-cluster/image003.png)

## <a name="testing"></a>Testowanie
Wszystko jest gotowe do symulacji automatycznej pracy awaryjnej. Istnieją dwa sposoby, w tym celu: elastyczne i.

Elastyczne możliwości klastra funkcja zamknięcia: ``crm_standby -U `uname -n` -v on``. Jeśli możesz użyć tego wzorca podrzędnej ma. Pamiętaj, aby ponowne ustawienie to jest wyłączany. Jeśli nie zostanie crm_mon wyświetli jeden węzeł w stan wstrzymania.

Sposób twardym jest zamykana podstawowej maszyny Wirtualnej (hadb01) za pośrednictwem portalu lub zmieniając uruchamiania przełącznika/RL na Maszynie wirtualnej (to znaczy zatrzymania, zamknięcia). Pomaga to Corosync i rozrusznik przez sygnalizowania, który głównym elementu w dół. Można to sprawdzić (przydatne w przypadku okna obsługi), ale możesz też wymusić scenariusza zamrażanie maszyny Wirtualnej.

## <a name="stonith"></a>STONITH
Należy umożliwić wydania wyłączania maszyny Wirtualnej za pomocą wiersza polecenia platformy Azure zamiast STONITH skrypt, który kontroluje urządzenia fizycznego. Można użyć `/usr/lib/stonith/plugins/external/ssh` jako typu podstawowego i Włącz STONITH w konfiguracji klastra. Azure CLI powinien być zainstalowany globalnie i powinny być załadowane ustawienia publikowania i profilu dla użytkownika klastra.

Przykładowy kod dla zasobu jest dostępna w [GitHub](https://github.com/bureado/aztonith). Zmień konfigurację klastra, dodając następujące polecenie, aby `sudo crm configure`:

    primitive st-azure stonith:external/azure \
      params hostlist="hadb01 hadb02" \
      clone fencing st-azure \
      property stonith-enabled=true \
      commit

> [!NOTE]
> Skrypt nie działa kontroli w górę lub w dół. Oryginalny zasobów SSH były 15 kontroli ping, ale czas odzyskiwania dla maszyny Wirtualnej platformy Azure może być więcej zmiennej.

## <a name="limitations"></a>Ograniczenia
Mają zastosowanie następujące ograniczenia:

* Skrypt zasobów DRBD linbit, który zarządza DRBD jako zasób w używa rozrusznik `drbdadm down` zamknięcia węzła, nawet jeśli węzeł właśnie przechodzi w stan wstrzymania. To nie jest idealny ponieważ podrzędnej będzie nie można synchronizowanie zasobów DRBD podczas wzorca pobiera zapisów. Jeśli wzorzec nie powiedzie się graciously, podrzędnej może zająć ponad starsze stan systemu plików. Istnieją dwa sposoby potencjalnych rozwiązywania to:
  * Wymuszanie `drbdadm up r0` we wszystkich węzłach klastra za pośrednictwem lokalnego (nie clusterized) programu alarmowego
  * Edytowanie skryptu DRBD linbit, upewniając się, że `down` nie jest wywoływana `/usr/lib/ocf/resource.d/linbit/drbd`
* Moduł równoważenia obciążenia musi co najmniej pięciu sekund odpowiedzi, więc aplikacji powinien być typu cluster-aware i być bardziej odporne na limit czasu. Może również pomóc innych architektur, takich jak kolejki w aplikacji i middlewares zapytania.
* Dostrajanie MySQL jest niezbędne do zapewnienia, że zapisu odbywa się w zarządzaniu tempie i pamięci podręczne są opróżniane na dysku, jak często, jak to możliwe, aby zminimalizować ograniczenie pamięci.
* Zapis wydajności jest zależna w maszynie Wirtualnej łączyć przełącznika wirtualnego, ponieważ jest to mechanizm używany przez DRBD replikację na urządzeniu.
