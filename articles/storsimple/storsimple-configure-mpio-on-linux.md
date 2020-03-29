---
title: Konfigurowanie mpio na hoście StorSimple Linux
description: Konfigurowanie mpio na StorSimple podłączony do hosta Systemu Linux z systemem CentOS 6.6
author: alkohli
ms.assetid: ca289eed-12b7-4e2e-9117-adf7e2034f2f
ms.service: storsimple
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: alkohli
ms.openlocfilehash: 5dadd231335e93839e947077168f32dbfe96eb45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278357"
---
# <a name="configure-mpio-on-a-storsimple-host-running-centos"></a>Konfigurowanie mpio na hoście StorSimple z systemem CentOS
W tym artykule opisano kroki wymagane do skonfigurowania wielościeżkowego wejścia we/wy (MPIO) na serwerze hosta Centos 6.6. Serwer hosta jest połączony z urządzeniem Microsoft Azure StorSimple w celu uzyskania wysokiej dostępności za pośrednictwem inicjatorów iSCSI. Opisano w nim szczegółowo automatyczne odnajdowanie urządzeń wielościeżkowych i określoną konfigurację tylko dla woluminów StorSimple.

Ta procedura ma zastosowanie do wszystkich modeli urządzeń storsimple serii 8000.

> [!NOTE]
> Tej procedury nie można używać dla urządzenia StorSimple Cloud Appliance. Aby uzyskać więcej informacji, zobacz jak skonfigurować serwery hosta dla urządzenia w chmurze.


## <a name="about-multipathing"></a>Informacje o wielopatycznym
Funkcja multipathing umożliwia skonfigurowanie wielu ścieżek we/wy między serwerem hosta a urządzeniem magazynującym. Te ścieżki we/wy są fizycznymi połączeniami sieci SAN, które mogą obejmować oddzielne kable, przełączniki, interfejsy sieciowe i kontrolery. Wielościeżkowe agreguje ścieżki we/wy, aby skonfigurować nowe urządzenie, które jest skojarzone ze wszystkimi ścieżkami zagregowanymi.

Celem multipathing jest dwojaki:

* **Wysoka dostępność:** Zapewnia alternatywną ścieżkę, jeśli dowolny element ścieżki we/wy (taki jak kabel, przełącznik, interfejs sieciowy lub kontroler) ulegnie awarii.
* **Równoważenie obciążenia:** W zależności od konfiguracji urządzenia pamięci masowej może poprawić wydajność, wykrywając obciążenia na ścieżkach we/wy i dynamicznie równoważąc te obciążenia.

### <a name="about-multipathing-components"></a>Informacje o składnikach wielościeżkowych
Multipathing w systemie Linux składa się ze składników jądra i składników przestrzeni użytkownika, jak tabelaryczne poniżej.

* **Jądro:** Głównym składnikiem jest *maper urządzenia,* który przekierowywa we/wy i obsługuje tryb failover dla ścieżek i grup ścieżek.

* **Przestrzeń użytkownika:** Są to *narzędzia wielościeżowe,* które zarządzają urządzeniami wielościeżkowymi, instruując moduł wielościeżkowy mapera urządzenia, co należy zrobić. Narzędzia składają się z:
   
   * **Multipath**: wyświetla listę i konfiguruje urządzenia wielościeżkowe.
   * **Wielościeżkowy**: demon, który wykonuje wielościeżki i monitoruje ścieżki.
   * **Devmap-name**: zapewnia znaczącą nazwę urządzenia do udev dla devmaps.
   * **Kpartx**: mapuje linearne mapy devmapy na partycje urządzenia, aby mapy wielościeżkowe można było podzielić na partycje.
   * **Multipath.conf**: plik konfiguracyjny dla demona wielościeżkowego, który służy do zastępowania wbudowanej tabeli konfiguracji.

### <a name="about-the-multipathconf-configuration-file"></a>Informacje o pliku konfiguracyjnym multipath.conf
Plik konfiguracyjny `/etc/multipath.conf` sprawia, że wiele funkcji multipathing konfigurowalne przez użytkownika. Polecenie `multipath` i demon jądra `multipathd` używają informacji znalezionych w tym pliku. Plik jest konsultowany tylko podczas konfiguracji urządzeń wielościeżkowych. Upewnij się, że wszystkie zmiany `multipath` są wprowadzane przed uruchomieniem polecenia. Jeśli zmodyfikujesz plik później, należy zatrzymać i uruchomić wielościeżkowe ponownie, aby zmiany zaczęły obowiązywać.

Multipath.conf ma pięć sekcji:

- **Ustawienia domyślne na poziomie systemu** *(wartości domyślne):* Można zastąpić ustawienia domyślne poziomu systemu.
- **Urządzenia z** czarnej listy *(czarna lista)*: Można określić listę urządzeń, które nie powinny być kontrolowane przez maper urządzenia.
- **Wyjątki z czarnej listy** *(blacklist_exceptions)*: Można zidentyfikować określone urządzenia, które mają być traktowane jako urządzenia wielościeżowe, nawet jeśli są wymienione na czarnej liście.
- Ustawienia *(urządzenia)* **kontrolera magazynu:** Można określić ustawienia konfiguracji, które będą stosowane do urządzeń, które mają informacje o dostawcy i produkcie.
- **Ustawienia specyficzne dla urządzenia** *(wielościeżki)*: Za pomocą tej sekcji można dostosować ustawienia konfiguracji poszczególnych jednostek LUN.

## <a name="configure-multipathing-on-storsimple-connected-to-linux-host"></a>Konfigurowanie multipathingu na storsimple podłączony do hosta systemu Linux
Urządzenie StorSimple podłączone do hosta systemu Linux można skonfigurować pod kątem wysokiej dostępności i równoważenia obciążenia. Na przykład jeśli host systemu Linux ma dwa interfejsy podłączone do sieci SAN, a urządzenie ma dwa interfejsy podłączone do sieci SAN, tak aby te interfejsy znajdują się w tej samej podsieci, dostępne będą 4 ścieżki. Jeśli jednak każdy interfejs DANYCH na urządzeniu i interfejsie hosta znajduje się w innej podsieci IP (i nie można ich routingu), dostępne będą tylko 2 ścieżki. Można skonfigurować multipathing, aby automatycznie odnajdywać wszystkie dostępne ścieżki, wybrać algorytm równoważenia obciążenia dla tych ścieżek, zastosować określone ustawienia konfiguracji dla woluminów tylko StorSimple, a następnie włączyć i zweryfikować multipathing.

W poniższej procedurze opisano sposób konfigurowania multipathing, gdy urządzenie StorSimple z dwoma interfejsami sieciowymi jest połączone z hostem z dwoma interfejsami sieciowymi.

## <a name="prerequisites"></a>Wymagania wstępne
W tej sekcji opisano wymagania wstępne dotyczące konfiguracji serwera CentOS i urządzenia StorSimple.

### <a name="on-centos-host"></a>Na hoście CentOS
1. Upewnij się, że host CentOS ma włączone 2 interfejsy sieciowe. Wpisz:
   
    `ifconfig`
   
    Poniższy przykład pokazuje dane wyjściowe,`eth0` `eth1`gdy dwa interfejsy sieciowe ( i ) są obecne na hoście.
   
        [root@centosSS ~]# ifconfig
        eth0  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:41  
          inet addr:10.126.162.65  Bcast:10.126.163.255  Mask:255.255.252.0
          inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3341/64 Scope:Global
          inet6 addr: fe80::215:5dff:fea2:3341/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
         RX packets:36536 errors:0 dropped:0 overruns:0 frame:0
          TX packets:6312 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:13994127 (13.3 MiB)  TX bytes:645654 (630.5 KiB)
   
        eth1  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:42  
          inet addr:10.126.162.66  Bcast:10.126.163.255  Mask:255.255.252.0
          inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3342/64 Scope:Global
          inet6 addr: fe80::215:5dff:fea2:3342/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:25962 errors:0 dropped:0 overruns:0 frame:0
          TX packets:11 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:2597350 (2.4 MiB)  TX bytes:754 (754.0 b)
   
        loLink encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:12 errors:0 dropped:0 overruns:0 frame:0
          TX packets:12 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:720 (720.0 b)  TX bytes:720 (720.0 b)
1. Zainstaluj *iSCSI-initiator-utils* na serwerze CentOS. Wykonaj następujące kroki, aby zainstalować *iSCSI-initiator-utils*.
   
   1. Zaloguj się `root` jako do hosta CentOS.
   1. Zainstaluj *iSCSI-initiator-utils*. Wpisz:
      
       `yum install iscsi-initiator-utils`
   1. Po pomyślnym zainstalowaniu *usługi iSCSI-Initiator-utils* uruchom usługę iSCSI. Wpisz:
      
       `service iscsid start`
      
       Czasami może `iscsid` nie rozpocząć, a `--force` opcja może być potrzebna
   1. Aby upewnić się, że inicjator iSCSI jest `chkconfig` włączony w czasie rozruchu, użyj polecenia, aby włączyć usługę.
      
       `chkconfig iscsi on`
   1. Aby sprawdzić, czy została poprawnie skonfigurowana, uruchom polecenie:
      
       `chkconfig --list | grep iscsi`
      
       Poniżej pokazano przykładowe dane wyjściowe.
      
           iscsi   0:off   1:off   2:on3:on4:on5:on6:off
           iscsid  0:off   1:off   2:on3:on4:on5:on6:off
      
       Na powyższym przykładzie widać, że środowisko iSCSI będzie działać w czasie rozruchu na poziomach uruchamiania 2, 3, 4 i 5.
1. Zainstaluj *device-mapper-multipath*. Wpisz:
   
    `yum install device-mapper-multipath`
   
    Rozpocznie się instalacja. Wpisz **Y,** aby kontynuować po wyświetleniu monitu o potwierdzenie.

### <a name="on-storsimple-device"></a>Na urządzeniu StorSimple
Urządzenie StorSimple powinno mieć:

* Co najmniej dwa interfejsy włączone dla iSCSI. Aby sprawdzić, czy dwa interfejsy są włączone iSCSI na urządzeniu StorSimple, wykonaj następujące kroki w klasycznej witrynie azure portal dla urządzenia StorSimple:
  
  1. Zaloguj się do klasycznego portalu dla urządzenia StorSimple.
  1. Wybierz usługę StorSimple Manager, kliknij pozycję **Urządzenia** i wybierz konkretne urządzenie StorSimple. Kliknij **pozycję Konfiguruj** i sprawdź ustawienia interfejsu sieciowego. Poniżej przedstawiono zrzut ekranu z dwoma interfejsami sieciowymi obsługującymi interfejs iSCSI. Tutaj DANE 2 i DATA 3, oba interfejsy 10 GbE są włączone dla iSCSI.
     
      ![MPIO StorsProste dane 2 config](./media/storsimple-configure-mpio-on-linux/IC761347.png)
     
      ![MPIO StorSimple DANE 3 Config](./media/storsimple-configure-mpio-on-linux/IC761348.png)
     
      Na stronie **Konfigurowanie**
     
     1. Upewnij się, że oba interfejsy sieciowe są włączone iSCSI. Pole **włączone iSCSI** powinno być ustawione na **Tak**.
     1. Upewnij się, że interfejsy sieciowe mają taką samą szybkość, oba powinny być 1 GbE lub 10 GbE.
     1. Zanotuj adresy IPv4 interfejsów obsługujących interfejsy iSCSI i zapisz do późniejszego użycia na hoście.
* Interfejsy iSCSI na urządzeniu StorSimple powinny być dostępne z serwera CentOS.
      Aby to sprawdzić, należy podać adresy IP interfejsów sieciowych obsługujących system iSCSI storasci na serwerze hosta. Poniżej przedstawiono użyte polecenia i odpowiadające im dane wyjściowe z data2 (10.126.162.25) i DATA3 (10.126.162.26):
  
        [root@centosSS ~]# iscsiadm -m discovery -t sendtargets -p 10.126.162.25:3260
        10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
        10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target

### <a name="hardware-configuration"></a>Konfiguracja sprzętu
Zaleca się połączenie dwóch interfejsów sieciowych iSCSI na oddzielnych ścieżkach w celu zapewnienia nadmiarowości. Na poniższym rysunku przedstawiono zalecaną konfigurację sprzętową dla wysokiej dostępności i równoważenia obciążenia multipathing dla serwera CentOS i urządzenia StorSimple.

![Konfiguracja sprzętowa MPIO dla hosta StorSimple na Linux](./media/storsimple-configure-mpio-on-linux/MPIOHardwareConfigurationStorSimpleToLinuxHost2M.png)

Jak pokazano na powyższym rysunku:

* Urządzenie StorSimple jest w konfiguracji aktywnej i pasywnej z dwoma kontrolerami.
* Do kontrolerów urządzenia są podłączone dwa przełączniki SAN.
* Dwa inicjatory iSCSI są włączone na urządzeniu StorSimple.
* Dwa interfejsy sieciowe są włączone na hoście CentOS.

Powyższa konfiguracja przyniesie 4 oddzielne ścieżki między urządzeniem a hostem, jeśli interfejsy hosta i danych są rutowalne.

> [!IMPORTANT]
> * Zaleca się, aby nie mieszać interfejsów sieciowych 1 GbE i 10 GbE dla multipathing. Podczas korzystania z dwóch interfejsów sieciowych oba interfejsy powinny być identycznym typem.
> * Na urządzeniu StorSimple, DATA0, DATA1, DATA4 i DATA5 są interfejsami 1 GbE, podczas gdy DATA2 i DATA3 są interfejsami sieciowymi 10 GbE.|
> 
> 

## <a name="configuration-steps"></a>Kroki konfiguracji
Kroki konfiguracji dla multipathing obejmują konfigurowanie dostępnych ścieżek do automatycznego odnajdowania, określanie algorytmu równoważenia obciążenia do użycia, włączanie multipathing i wreszcie weryfikacji konfiguracji. Każdy z tych kroków jest szczegółowo omówiony w poniższych sekcjach.

### <a name="step-1-configure-multipathing-for-automatic-discovery"></a>Krok 1: Konfigurowanie wielościeżkowego do automatycznego odnajdowania
Urządzenia obsługiwane przez multipath mogą być automatycznie odnajdowane i konfigurowane.

1. Inicjowanie `/etc/multipath.conf` pliku. Wpisz:
   
     `mpathconf --enable`
   
    Powyższe polecenie utworzy `sample/etc/multipath.conf` plik.
1. Uruchom usługę wielościeżkową. Wpisz:
   
    `service multipathd start`
   
    Zostaną wyświetlone następujące dane wyjściowe:
   
    `Starting multipathd daemon:`
1. Włącz automatyczne odnajdowanie wielościeżkowych ścieżek. Wpisz:
   
    `mpathconf --find_multipaths y`
   
    Spowoduje to zmodyfikowanie domyślnej `multipath.conf` sekcji, jak pokazano poniżej:
   
        defaults {
        find_multipaths yes
        user_friendly_names yes
        path_grouping_policy multibus
        }

### <a name="step-2-configure-multipathing-for-storsimple-volumes"></a>Krok 2: Konfigurowanie multipathing dla woluminów StorSimple
Domyślnie wszystkie urządzenia są wymienione na czarno w pliku multipath.conf i zostaną pominięte. Należy utworzyć wyjątki czarnej listy, aby zezwolić na multipathing dla woluminów z urządzeń StorSimple.

1. Edytuj `/etc/mulitpath.conf` plik. Wpisz:
   
    `vi /etc/multipath.conf`
1. Znajdź sekcję blacklist_exceptions w pliku multipath.conf. Urządzenie StorSimple musi być wymienione jako wyjątek czarnej listy w tej sekcji. Możesz odkomentować odpowiednie wiersze w tym pliku, aby zmodyfikować go w sposób pokazany poniżej (użyj tylko konkretnego modelu używanego urządzenia):
   
        blacklist_exceptions {
            device {
                       vendor  "MSFT"
                       product "STORSIMPLE 8100*"
            }
            device {
                       vendor  "MSFT"
                       product "STORSIMPLE 8600*"
            }
           }

### <a name="step-3-configure-round-robin-multipathing"></a>Krok 3: Konfigurowanie wielościeżkowego wielościeżkowego okrężnego
Ten algorytm równoważenia obciążenia używa wszystkich dostępnych ścieżek wielościeżkowych do aktywnego kontrolera w sposób zrównoważony, okrężny.

1. Edytuj `/etc/multipath.conf` plik. Wpisz:
   
    `vi /etc/multipath.conf`
1. W `defaults` sekcji ustaw `path_grouping_policy` na `multibus`. Określa `path_grouping_policy` domyślną zasadę grupowania ścieżek, która ma być stosowana do nieokreślonych ścieżek wielościeżkowych. Sekcja domyślna będzie wyglądać tak, jak pokazano poniżej.
   
        defaults {
                user_friendly_names yes
                path_grouping_policy multibus
        }

> [!NOTE]
> Najczęstsze `path_grouping_policy` wartości to:
> 
> * praca awaryjna = 1 ścieżka na grupę priorytetów
> * multibus = wszystkie prawidłowe ścieżki w 1 grupie priorytetów
> 
> 

### <a name="step-4-enable-multipathing"></a>Krok 4: Włącz multipathing
1. `multipathd` Uruchom ponownie demona. Wpisz:
   
    `service multipathd restart`
1. Dane wyjściowe będą jak pokazano poniżej:
   
        [root@centosSS ~]# service multipathd start
        Starting multipathd daemon:  [OK]

### <a name="step-5-verify-multipathing"></a>Krok 5: Sprawdź multipathing
1. Najpierw upewnij się, że połączenie iSCSI jest nawiązane za pomocą urządzenia StorSimple w następujący sposób:
   
   a. Odkryj urządzenie StorSimple. Wpisz:
      
    ```
    iscsiadm -m discovery -t sendtargets -p  <IP address of network interface on the device>:<iSCSI port on StorSimple device>
    ```
    
    Dane wyjściowe, gdy adres IP dla danych DATA0 to 10.126.162.25, a port 3260 jest otwierany na urządzeniu StorSimple dla wychodzącego ruchu iSCSI, jak pokazano poniżej:
    
    ```
    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    ```

    Skopiuj IQN urządzenia `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`StorSimple z poprzedniego wyjścia.

   b. Połącz się z urządzeniem za pomocą docelowego IQN. Urządzenie StorSimple jest tutaj celem iSCSI. Wpisz:

    ```
    iscsiadm -m node --login -T <IQN of iSCSI target>
    ```

    W poniższym przykładzie przedstawiono dane `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`wyjściowe z docelowym IQN . Dane wyjściowe wskazują, że pomyślnie nawiązano połączenie z dwoma interfejsami sieciowymi obsługującymi interfejs iSCSI na urządzeniu.

    ```
    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
    Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
    Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
    Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
    Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
    Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
    Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
    ```

    Jeśli widzisz tylko jeden interfejs hosta i dwie ścieżki w tym miejscu, musisz włączyć oba interfejsy na hoście dla iSCSI. Możesz postępować zgodnie ze [szczegółowymi instrukcjami w dokumentacji systemu Linux](https://access.redhat.com/documentation/Red_Hat_Enterprise_Linux/5/html/Online_Storage_Reconfiguration_Guide/iscsioffloadmain.html).

1. Wolumin jest narażony na serwer CentOS z urządzenia StorSimple. Aby uzyskać więcej informacji, zobacz [krok 6: Tworzenie woluminu](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) za pośrednictwem witryny Azure portal na urządzeniu StorSimple.

1. Sprawdź dostępne ścieżki. Wpisz:

      ```
      multipath -l
      ```

      Poniższy przykład przedstawia dane wyjściowe dla dwóch interfejsów sieciowych na urządzeniu StorSimple podłączonym do jednego interfejsu sieciowego hosta z dwiema dostępnymi ścieżkami.

        ```
        mpathb (36486fd20cc081f8dcd3fccb992d45a68) dm-3 MSFT,STORSIMPLE 8100
        size=100G features='0' hwhandler='0' wp=rw
        `-+- policy='round-robin 0' prio=0 status=active
        |- 7:0:0:1 sdc 8:32 active undef running
        `- 6:0:0:1 sdd 8:48 active undef running
        ```

        The following example shows the output for two network interfaces on a StorSimple device connected to two host network interfaces with four available paths.

        ```
        mpathb (36486fd27a23feba1b096226f11420f6b) dm-2 MSFT,STORSIMPLE 8100
        size=100G features='0' hwhandler='0' wp=rw
        `-+- policy='round-robin 0' prio=0 status=active
        |- 17:0:0:0 sdb 8:16 active undef running
        |- 15:0:0:0 sdd 8:48 active undef running
        |- 14:0:0:0 sdc 8:32 active undef running
        `- 16:0:0:0 sde 8:64 active undef running
        ```

        After the paths are configured, refer to the specific instructions on your host operating system (Centos 6.6) to mount and format this volume.

## <a name="troubleshoot-multipathing"></a>Rozwiązywanie problemów z multipathing
W tej sekcji przedstawiono kilka przydatnych wskazówek, jeśli napotkasz jakiekolwiek problemy podczas konfiguracji wielościeżkowej.

PYTANIE: Nie widzę zmian `multipath.conf` w pliku, które wchodzą w życie.

A. Jeśli wprowadzono jakiekolwiek zmiany `multipath.conf` w pliku, należy ponownie uruchomić usługę wielościeżkową. Wpisz następujące polecenie:

    service multipathd restart

PYTANIE: Mam włączone dwa interfejsy sieciowe na urządzeniu StorSimple i dwa interfejsy sieciowe na hoście. Gdy wymienię dostępne ścieżki, widzę tylko dwie ścieżki. Spodziewałem się zobaczyć cztery dostępne ścieżki.

A. Upewnij się, że dwie ścieżki znajdują się w tej samej podsieci i rutowalne. Jeśli interfejsy sieciowe znajdują się na różnych sieciach VLAN i nie są rutowalne, zobaczysz tylko dwie ścieżki. Jednym ze sposobów, aby to sprawdzić, jest, aby upewnić się, że można dotrzeć do obu interfejsów hosta z interfejsu sieciowego na urządzeniu StorSimple. Należy [skontaktować się z pomocą techniczną firmy Microsoft,](storsimple-8000-contact-microsoft-support.md) ponieważ weryfikacja ta może być przeprowadzona tylko za pośrednictwem sesji pomocy technicznej.

PYTANIE: Kiedy wymieniam dostępne ścieżki, nie widzę żadnych danych wyjściowych.

A. Zazwyczaj nie widząc żadnych ścieżek wielościeżkowych sugeruje problem z demonem wielopaśkowym i jest `multipath.conf` najbardziej prawdopodobne, że każdy problem tutaj leży w pliku.

Warto również sprawdzić, czy rzeczywiście można zobaczyć niektóre dyski po podłączeniu do obiektu docelowego, ponieważ brak odpowiedzi z aukcji wielościeżkowych może również oznaczać, że nie masz żadnych dysków.

* Użyj następującego polecenia, aby ponownie przeskanować magistraulę SCSI:
  
    `$ rescan-scsi-bus.sh`(część pakietu sg3_utils)
* Wpisz następujące polecenia:
  
    `$ dmesg | grep sd*`
     
     Lub
  
    `$ fdisk -l`
  
    Będą one zwracać szczegóły ostatnio dodanych dysków.
* Aby ustalić, czy jest to dysk StorSimple, należy użyć następujących poleceń:
  
    `cat /sys/block/<DISK>/device/model`
  
    Spowoduje to zwrócenie ciągu, który określi, czy jest to dysk StorSimple.

Mniej prawdopodobną, ale możliwą przyczyną może być również przestarzały iscsid pid. Użyj następującego polecenia, aby wylogować się z sesji iSCSI:

    iscsiadm -m node --logout -p <Target_IP>

Powtórz to polecenie dla wszystkich podłączonych interfejsów sieciowych w docelowym interfejsie iSCSI, które jest urządzeniem StorSimple. Po wylogowaniu się ze wszystkich sesji iSCSI użyj docelowego IQN iSCSI, aby ponownie nakreślić sesję iSCSI. Wpisz następujące polecenie:

    iscsiadm -m node --login -T <TARGET_IQN>


PYTANIE: Nie jestem pewien, czy moje urządzenie znajduje się na białej liście.

A. Aby sprawdzić, czy urządzenie znajduje się na białej liście, użyj następującego interaktywnego polecenia rozwiązywania problemów:

    multipathd -k
    multipathd> show devices
    available block devices:
    ram0 devnode blacklisted, unmonitored
    ram1 devnode blacklisted, unmonitored
    ram2 devnode blacklisted, unmonitored
    ram3 devnode blacklisted, unmonitored
    ram4 devnode blacklisted, unmonitored
    ram5 devnode blacklisted, unmonitored
    ram6 devnode blacklisted, unmonitored
    ram7 devnode blacklisted, unmonitored
    ram8 devnode blacklisted, unmonitored
    ram9 devnode blacklisted, unmonitored
    ram10 devnode blacklisted, unmonitored
    ram11 devnode blacklisted, unmonitored
    ram12 devnode blacklisted, unmonitored
    ram13 devnode blacklisted, unmonitored
    ram14 devnode blacklisted, unmonitored
    ram15 devnode blacklisted, unmonitored
    loop0 devnode blacklisted, unmonitored
    loop1 devnode blacklisted, unmonitored
    loop2 devnode blacklisted, unmonitored
    loop3 devnode blacklisted, unmonitored
    loop4 devnode blacklisted, unmonitored
    loop5 devnode blacklisted, unmonitored
    loop6 devnode blacklisted, unmonitored
    loop7 devnode blacklisted, unmonitored
    sr0 devnode blacklisted, unmonitored
    sda devnode whitelisted, monitored
    dm-0 devnode blacklisted, unmonitored
    dm-1 devnode blacklisted, unmonitored
    dm-2 devnode blacklisted, unmonitored
    sdb devnode whitelisted, monitored
    sdc devnode whitelisted, monitored
    dm-3 devnode blacklisted, unmonitored


Aby uzyskać więcej informacji, przejdź do [rozwiązywania problemów z multipathing](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/dm_multipath/mpio_admin-troubleshoot).

## <a name="list-of-useful-commands"></a>Lista przydatnych poleceń
| Typ | Polecenie | Opis |
| --- | --- | --- |
| **iSCSI** |`service iscsid start` |Uruchamianie usługi iSCSI |
| &nbsp; |`service iscsid stop` |Zatrzymaj usługę iSCSI |
| &nbsp; |`service iscsid restart` |Ponowne uruchamianie usługi iSCSI |
| &nbsp; |`iscsiadm -m discovery -t sendtargets -p <TARGET_IP>` |Odnajduj dostępne obiekty docelowe pod określonym adresem |
| &nbsp; |`iscsiadm -m node --login -T <TARGET_IQN>` |Zaloguj się do obiektu docelowego iSCSI |
| &nbsp; |`iscsiadm -m node --logout -p <Target_IP>` |Wyloguj się z celu iSCSI |
| &nbsp; |`cat /etc/iscsi/initiatorname.iscsi` |Drukuj nazwę inicjatora iSCSI |
| &nbsp; |`iscsiadm -m session -s <sessionid> -P 3` |Sprawdź stan sesji iSCSI i wolumin odnaleziony na hoście |
| &nbsp; |`iscsi -m session` |Pokazuje wszystkie sesje iSCSI ustanowione między hostem a urządzeniem StorSimple |
|  | | |
| **Wiele ścieżek** |`service multipathd start` |Uruchamianie demona wielościeżkowego |
| &nbsp; |`service multipathd stop` |Zatrzymaj demona wielościeżkowego |
| &nbsp; |`service multipathd restart` |Uruchom ponownie demona wielościeżkowego |
| &nbsp; |`chkconfig multipathd on` </br> LUB </br> `mpathconf -with_chkconfig y` |Włącz uruchamianie demona wielościeżkowego w czasie rozruchu |
| &nbsp; |`multipathd -k` |Uruchamianie interaktywnej konsoli w celu rozwiązywania problemów |
| &nbsp; |`multipath -l` |Wyświetlanie listy połączeń wielościeżkowych i urządzeń |
| &nbsp; |`mpathconf --enable` |Tworzenie przykładowego pliku mulitpath.conf w`/etc/mulitpath.conf` |
|  | | |

## <a name="next-steps"></a>Następne kroki
Podczas konfigurowania mpio na hoście Linuksa może być konieczne zapoznanie się z następującymi dokumentami CentoS 6.6:

* [Konfigurowanie mpio w centos](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/dm_multipath/index)
* [Przewodnik szkoleniowy dla systemu Linux](http://linux-training.be/linuxsys.pdf)

