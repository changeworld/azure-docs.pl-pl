---
title: Konfigurowanie wielościeżkowego wejścia/wyjścia na hoście z systemem StorSimple Linux | Dokumentacja firmy Microsoft
description: Konfigurowanie wielościeżkowego wejścia/wyjścia w połączeniu z hostem Linux uruchomiony CentOS 6.6 StorSimple
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: tysonn
ms.assetid: ca289eed-12b7-4e2e-9117-adf7e2034f2f
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: df9f53694cd72a700d593550bfde13a4f25180db
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37021757"
---
# <a name="configure-mpio-on-a-storsimple-host-running-centos"></a>Konfigurowanie wielościeżkowego wejścia/wyjścia na hoście StorSimple z systemem CentOS
W tym artykule opisano kroki wymagane do skonfigurowania Wielościeżkowe We/Wy (MPIO) na serwerze hosta Centos 6.6. Serwer hosta jest podłączony do urządzenia Microsoft Azure StorSimple wysokiej dostępności za pośrednictwem inicjatorów iSCSI. Opisuje szczegółowo automatycznego wykrywania urządzeń wielościeżkowego i ustawieniach określonych tylko dla woluminów StorSimple.

Ta procedura ma zastosowanie do wszystkich modeli urządzeń z serii StorSimple 8000.

> [!NOTE]
> Nie można użyć tej procedury dla urządzenia StorSimple chmury. Aby uzyskać więcej informacji zobacz Konfigurowanie serwerów hosta dla urządzenia chmury.


## <a name="about-multipathing"></a>O wiele ścieżek
Funkcja wielu ścieżek pozwala na skonfigurowanie wielu ścieżek wejścia/wyjścia między serwerem hosta a urządzeniem magazynującym. Te ścieżki We/Wy są fizycznych połączeń sieci SAN, które mogą zawierać osobne kable, przełączniki interfejsów sieciowych i kontrolerów. Wiele ścieżek agreguje ścieżek wejścia/wyjścia, aby skonfigurować nowe urządzenie, którego jest skojarzony z wszystkie zagregowane ścieżki.

Wiele ścieżek służy dwukrotne:

* **Wysoka dostępność**: zapewnia alternatywną ścieżkę, jeśli dowolny element ścieżkę We/Wy (takie jak kabel, przełącznika, interfejsu sieciowego lub kontrolera) zakończy się niepowodzeniem.
* **Równoważenie obciążenia**: w zależności od konfiguracji urządzenia magazynującego go poprawić wydajność, wykrywanie obciążenia w ścieżkach We/Wy i dynamicznie ponowne równoważenie obciążenia tych.

### <a name="about-multipathing-components"></a>O wiele ścieżek składników
Wiele ścieżek w systemie Linux zawiera składniki jądra i przestrzeń użytkownika jak przedstawione w poniższej tabeli.

* **Jądra**: jest to główny składnik *mapowania urządzenia* zmienia trasę We/Wy i obsługuje tryb failover dla ścieżki i grup ścieżki.

* **Przestrzeń użytkownika**: są to *narzędzia wielościeżkowego* który zarządzania urządzeniami multipathed przez poinstruowanie wielościeżkowe moduł mapowania urządzenia, co należy zrobić. Narzędzia obejmują:
   
   * **Wielościeżkowe**: zawiera listę i konfiguruje multipathed urządzenia.
   * **Multipathd**: demon, który wykonuje wielościeżkowego i monitoruje ścieżki.
   * **Nazwa Devmap**: zapewnia łatwy do rozpoznania nazwy urządzenia do demona udev devmaps.
   * **Kpartx**: mapuje liniowej devmaps partycji urządzenia, aby lepiej partitionable wielościeżkowe mapy.
   * **MultiPath.conf**: plik konfiguracji wielościeżkowe demona, używany do zastąpienia tabeli wbudowanych konfiguracji.

### <a name="about-the-multipathconf-configuration-file"></a>Plik konfiguracji multipath.conf — informacje
Plik konfiguracji `/etc/multipath.conf` udostępnia wiele funkcji wielu ścieżek konfigurowanych przez użytkownika. `multipath` Polecenia i demona jądra `multipathd` korzystać z informacji zamieszczonych w tym pliku. Plik konsultacji tylko podczas konfiguracji wielościeżkowe urządzeń. Upewnij się, że wszystkie zmiany zostały wprowadzone, przed uruchomieniem `multipath` polecenia. Jeśli później zmodyfikować plik należy zatrzymać i uruchomić multipathd ponownie, aby zmiany zaczęły obowiązywać.

Multipath.conf zawiera pięć sekcje:

- **Poziomu domyślnych ustawień systemowych** *(wartość domyślna)*: można zastąpić poziomu ustawień domyślnych systemu.
- **Urządzenia na liście zabronionych numerów** *(lista zablokowanych)*: można określić listę urządzeń, które nie powinny być kontrolowane na podstawie mapowania urządzenia.
- **Wyeliminować wyjątki** *(blacklist_exceptions)*: może rozpoznać określonego urządzenia powinien być traktowany jako wielościeżkowe urządzeń, nawet jeśli na liście zabronionych.
- **Określone ustawienia kontrolera magazynu** *(urządzeń)*: można określić ustawienia konfiguracji, które zostaną zastosowane do urządzenia, które mają dostawcy i informacje o produkcie.
- **Określone ustawienia urządzenia** *(multipaths)*: Ta sekcja umożliwia dostosowywanie ustawień konfiguracji poszczególne jednostki LUN.

## <a name="configure-multipathing-on-storsimple-connected-to-linux-host"></a>Konfigurowanie wielu ścieżek na StorSimple połączony z hostem systemu Linux
Wysoka dostępność i równoważenie obciążenia sieciowego można skonfigurować urządzenia StorSimple podłączonego do hosta systemu Linux. Na przykład jeśli Linux host ma dwa interfejsy połączony z siecią SAN, a urządzenie ma dwa interfejsy połączony z siecią SAN w taki sposób, że te interfejsy są w tej samej podsieci, będą 4 ścieżki dostępne. Jednak jeśli każdy interfejs danych w interfejsie urządzenia i hosta w innej podsieci IP (i nie wzajemnie obsługują routing), to tylko 2 ścieżki będą dostępne. Istnieje możliwość skonfigurowania wielu ścieżek automatycznie odnajdywać dostępnych ścieżek, wybrać algorytm równoważenia obciążenia dla tych ścieżek, zastosowania określonych ustawień konfiguracyjnych dla woluminów tylko StorSimple, włączyć i sprawdź wielu ścieżek.

W poniższej procedurze opisano sposób konfigurowania wielu ścieżek, gdy urządzenie StorSimple z dwóch interfejsów sieciowych jest podłączony do hosta z dwoma interfejsami sieciowymi.

## <a name="prerequisites"></a>Wymagania wstępne
Ta sekcja zawiera szczegóły dotyczące wymagania wstępne dotyczące konfiguracji dla serwera CentOS i urządzenia StorSimple.

### <a name="on-centos-host"></a>Na hoście CentOS
1. Upewnij się, że na hoście CentOS ma włączone 2 interfejsów sieciowych. Wpisz:
   
    `ifconfig`
   
    W poniższym przykładzie przedstawiono dane wyjściowe, gdy dwa interfejsy sieciowe (`eth0` i `eth1`) znajdują się na hoście.
   
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
2. Zainstaluj *iSCSI inicjatora witryny* na serwerze CentOS. Wykonaj poniższe kroki, aby zainstalować *iSCSI inicjatora witryny*.
   
   1. Zaloguj się jako `root` do hosta CentOS.
   2. Zainstaluj *iSCSI inicjatora witryny*. Wpisz:
      
       `yum install iscsi-initiator-utils`
   3. Po *iSCSI inicjatora witryny* jest poprawnie zainstalowany, uruchomienie usługi iSCSI. Wpisz:
      
       `service iscsid start`
      
       W przypadkach `iscsid` faktycznie nie może uruchomić i `--force` opcji mogą być potrzebne.
   4. Aby zapewnić włączenie podczas rozruchu z inicjatora iSCSI, należy użyć `chkconfig` polecenie, aby włączyć usługę.
      
       `chkconfig iscsi on`
   5. Aby sprawdzić, czy zostało poprawnie skonfigurowane, uruchom polecenie:
      
       `chkconfig --list | grep iscsi`
      
       Poniżej pokazano przykładowe dane wyjściowe.
      
           iscsi   0:off   1:off   2:on3:on4:on5:on6:off
           iscsid  0:off   1:off   2:on3:on4:on5:on6:off
      
       W powyższym przykładzie widać, że środowisko iSCSI uruchomi w czasie rozruchu wykonywania poziomu 2, 3, 4 i 5.
3. Zainstaluj *urządzenia mapowania wielościeżkowego*. Wpisz:
   
    `yum install device-mapper-multipath`
   
    Instalacja rozpocznie się. Typ **Y** kontynuowanie po wyświetleniu monitu o potwierdzenie.

### <a name="on-storsimple-device"></a>Na urządzeniu StorSimple
Urządzenia StorSimple powinny mieć:

* Co najmniej dwa interfejsy włączone dla interfejsu iSCSI. Aby sprawdzić, czy dwa interfejsy są włączone iSCSI na urządzeniu StorSimple, wykonaj następujące kroki w klasycznym portalu Azure dla urządzenia StorSimple:
  
  1. Zaloguj się do klasycznego portalu dla urządzenia StorSimple.
  2. Wybierz usługę Menedżer StorSimple, kliknij przycisk **urządzeń** i wybierz urządzenie StorSimple. Kliknij przycisk **Konfiguruj** i sprawdź ustawienia interfejsu sieciowego. Poniżej przedstawiono zrzut ekranu z dwa interfejsy sieci iSCSI. Tutaj dane 2 i dane 3, zarówno 10 GbE interfejsy są włączone dla interfejsu iSCSI.
     
      ![Konfiguracja MPIO StorsSimple dane 2](./media/storsimple-configure-mpio-on-linux/IC761347.png)
     
      ![Wielościeżkowe wejście/wyjście StorSimple dane 3 konfiguracji](./media/storsimple-configure-mpio-on-linux/IC761348.png)
     
      W **Konfiguruj** strony
     
     1. Upewnij się, że oba interfejsy sieciowe są włączone iSCSI. **ISCSI włączone** pola powinna być równa **tak**.
     2. Upewnij się, że interfejsy sieciowe mają taką samą szybkość, powinny być 1 GbE lub 10 GbE.
     3. Należy pamiętać, adresy IPv4 interfejsów włączono interfejs iSCSI, a następnie zapisz do późniejszego użytku na hoście.
* Interfejsów iSCSI na urządzeniu StorSimple powinien być dostępny z serwera, CentOS.
      Aby to sprawdzić, należy podać adresy IP interfejsów sieci iSCSI StorSimple na serwerze hosta. Polecenia używane i odpowiednie dane wyjściowe z dane2 (10.126.162.25) i DATA3 (10.126.162.26) są wyświetlane poniżej:
  
        [root@centosSS ~]# iscsiadm -m discovery -t sendtargets -p 10.126.162.25:3260
        10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
        10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target

### <a name="hardware-configuration"></a>Konfiguracja sprzętu
Zaleca się połączyć z dwa interfejsy sieci iSCSI na oddzielnych ścieżek nadmiarowości. Na poniższym rysunku przedstawiono zalecana konfiguracja sprzętu wysokiej dostępności i równoważenia obciążenia wielu ścieżek dla urządzenia StorSimple i CentOS serwera.

![Konfiguracja sprzętowa wielościeżkowego wejścia/wyjścia dla urządzenia StorSimple na hoście z systemem Linux](./media/storsimple-configure-mpio-on-linux/MPIOHardwareConfigurationStorSimpleToLinuxHost2M.png)

Jak pokazano na powyższej ilustracji:

* Urządzenia StorSimple znajduje się w konfiguracji aktywny / pasywny z dwóch kontrolerów.
* Dwa przełączniki sieci SAN są połączone z kontrolerami urządzenia.
* Dwa inicjatorów iSCSI są włączone na urządzeniu StorSimple.
* Dwa interfejsy sieciowe są włączone na hoście CentOS.

Powyższej konfiguracji umożliwia uzyskanie 4 osobnych ścieżek między urządzeniem i hosta, jeśli interfejsy danych hosta i są wzajemnie obsługiwać Routing.

> [!IMPORTANT]
> * Zaleca się, że niemieszanie 1 GbE i 10 GbE interfejsów sieciowych do obsługi wielu ścieżek. Korzystając z dwóch interfejsów sieciowych, oba interfejsy powinna być identyczne typu.
> * Na urządzeniu StorSimple DATA0, dane1, DATA4 i DATA5 są 1 GbE interfejsy dane2 i DATA3 są 10 GbE interfejsów. |
> 
> 

## <a name="configuration-steps"></a>Kroki konfiguracji
Kroki konfiguracji wielu ścieżek obejmują konfigurowanie dostępnych ścieżek automatycznego wykrywania, określając algorytm równoważenia obciążenia w celu użycia, włączanie wielu ścieżek i na koniec Weryfikowanie konfiguracji. Każdy z tych kroków została omówiona szczegółowo w poniższych sekcjach.

### <a name="step-1-configure-multipathing-for-automatic-discovery"></a>Krok 1: Konfigurowanie wielu ścieżek automatycznego wykrywania
Automatycznie odnalezione urządzenia obsługiwane wielościeżkowe i skonfigurowane.

1. Inicjowanie `/etc/multipath.conf` pliku. Wpisz:
   
     `mpathconf --enable`
   
    Powyższe polecenie spowoduje utworzenie `sample/etc/multipath.conf` pliku.
2. Uruchom usługę wielu ścieżek. Wpisz:
   
    `service multipathd start`
   
    Zostaną wyświetlone następujące informacje:
   
    `Starting multipathd daemon:`
3. Włącz automatyczne odnajdowanie multipaths. Wpisz:
   
    `mpathconf --find_multipaths y`
   
    Spowoduje to zmodyfikowanie wartości domyślne sekcji z `multipath.conf` w sposób przedstawiony poniżej:
   
        defaults {
        find_multipaths yes
        user_friendly_names yes
        path_grouping_policy multibus
        }

### <a name="step-2-configure-multipathing-for-storsimple-volumes"></a>Krok 2: Konfigurowanie wielu ścieżek dla woluminów StorSimple
Domyślnie wszystkie urządzenia są czarny wymienione w pliku multipath.conf i będą pomijane. Musisz utworzyć listę zablokowanych wyjątków, aby umożliwić wielu ścieżek dla woluminów z urządzenia StorSimple.

1. Edytuj `/etc/mulitpath.conf` pliku. Wpisz:
   
    `vi /etc/multipath.conf`
2. Znajdź sekcję blacklist_exceptions w pliku multipath.conf. Urządzenia StorSimple musi być wymieniona jako wyjątek do czarnej listy w tej sekcji. Można Usuń komentarz odpowiednich wierszy w tym pliku można go zmodyfikować, jak pokazano poniżej (Użyj tylko określonego modelu urządzenia, które są używane):
   
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

### <a name="step-3-configure-round-robin-multipathing"></a>Krok 3: Konfigurowanie wielu ścieżek okrężnego
Ten algorytm równoważenia obciążenia używa wszystkich dostępnych multipaths z aktywnym kontrolerem w sposób zrównoważony, okrężnego.

1. Edytuj `/etc/multipath.conf` pliku. Wpisz:
   
    `vi /etc/multipath.conf`
2. W obszarze `defaults` sekcji, ustaw `path_grouping_policy` do `multibus`. `path_grouping_policy` Określa ścieżkę domyślną grupowanie zasady do zastosowania dla multipaths nieokreślony. Wartości domyślne sekcji będzie wyglądać jak pokazano poniżej.
   
        defaults {
                user_friendly_names yes
                path_grouping_policy multibus
        }

> [!NOTE]
> Najbardziej typowe wartości `path_grouping_policy` obejmują:
> 
> * tryb failover = 1 ścieżka według priorytetu grupy
> * multibus = wszystkie prawidłowe ścieżki w grupie o priorytecie 1
> 
> 

### <a name="step-4-enable-multipathing"></a>Krok 4: Włącz wielościeżkowe
1. Uruchom ponownie `multipathd` demon. Wpisz:
   
    `service multipathd restart`
2. Dane wyjściowe będą, jak pokazano poniżej:
   
        [root@centosSS ~]# service multipathd start
        Starting multipathd daemon:  [OK]

### <a name="step-5-verify-multipathing"></a>Krok 5: Sprawdzanie wielu ścieżek
1. Najpierw upewnij się, że iSCSI jest nawiązywane połączenie z urządzenia StorSimple w następujący sposób:
   
   a. Odnajdywanie urządzenia StorSimple. Wpisz:
      
    ```
    iscsiadm -m discovery -t sendtargets -p  <IP address of network interface on the device>:<iSCSI port on StorSimple device>
    ```
    
    Dane wyjściowe, gdy adres IP dla DATA0 jest 10.126.162.25 i jest otwarty port 3260, na urządzeniu StorSimple dla ruchu wychodzącego iSCSI jest w sposób przedstawiony poniżej:
    
    ```
    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    ```

    Skopiuj IQN urządzenia StorSimple `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`, z powyższych danych wyjściowych.

   b. Nawiąż połączenie z urządzeniem przy użyciu docelowego IQN. Urządzenie StorSimple, jest obiektem docelowym iSCSI. Wpisz:

    ```
    iscsiadm -m node --login -T <IQN of iSCSI target>
    ```

    W poniższym przykładzie przedstawiono dane wyjściowe z elementem docelowym IQN z `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`. Dane wyjściowe wskazują, że pomyślnie nawiązano połączenie dwa interfejsy sieci iSCSI na urządzeniu.

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

    Jeśli zostanie wyświetlony interfejs tylko jednego hosta i dwóch ścieżek, należy włączyć interfejsy na hoście dla interfejsu iSCSI. Możesz wykonać [szczegółowe instrukcje w dokumentacji systemu Linux](https://access.redhat.com/documentation/Red_Hat_Enterprise_Linux/5/html/Online_Storage_Reconfiguration_Guide/iscsioffloadmain.html).

2. Wolumin jest narażony na serwerze CentOS z urządzenia StorSimple. Aby uzyskać więcej informacji, zobacz [krok 6: Tworzenie woluminu](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) za pośrednictwem portalu Azure w urządzeniu StorSimple.

3. Sprawdź dostępne ścieżki. Wpisz:

      ```
      multipath –l
      ```

      Poniższy przykład przedstawia dane wyjściowe dla dwa interfejsy sieci na urządzenia StorSimple podłączonego do karty sieciowej hosta z dwóch ścieżek dostępne.

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

## <a name="troubleshoot-multipathing"></a>Rozwiązywanie problemów z wielu ścieżek
Ta sekcja zawiera wskazówki przydatne, jeśli wystąpiły problemy podczas konfigurowania wielu ścieżek.

Q. Nie ma zmiany w `multipath.conf` pliku wpływają.

A. Jeśli zostały wprowadzone żadne zmiany, aby `multipath.conf` pliku, należy ponownie uruchomić usługę wielu ścieżek. Wpisz następujące polecenie:

    service multipathd restart

Q. Dwa interfejsy sieci na urządzeniu StorSimple i dwa interfejsy sieciowe na hoście został włączony. Podczas wyświetlania dostępnych ścieżek, są widoczne tylko dwie ścieżki. Oczekiwanej cztery ścieżki dostępne w temacie.

A. Upewnij się, że dwie ścieżki są w tej samej podsieci i wzajemnie obsługują routing. Jeśli interfejsów sieciowych znajdują się na różnych sieci VLAN, a nie obsługuje routingu, wyświetlona zostanie tylko dwie ścieżki. Jest jednym ze sposobów to sprawdzić, aby upewnić się, że może nawiązać połączenie interfejsy hosta z karty sieciowej na urządzeniu StorSimple. Konieczne będzie [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) jako weryfikacji jest możliwe tylko za pośrednictwem sesji pomocy technicznej.

Q. Podczas wyświetlania dostępnych ścieżek, nie ma żadnych danych wyjściowych.

A. Zazwyczaj nie ma żadnych ścieżek multipathed sugeruje problem z demona wielu ścieżek, i jest to najprawdopodobniej wszelkie problemy w tym miejscu znajduje się `multipath.conf` pliku.

Byłoby warto sprawdzanie, czy rzeczywiście widać niektóre dyski po nawiązaniu połączenia z docelowym jako odpowiedzi z listy wielościeżkowe również może oznaczać, że nie masz żadnych dysków.

* Aby ponownie przeskanować magistrali SCSI, użyj następującego polecenia:
  
    `$ rescan-scsi-bus.sh `(część pakietu sg3_utils)
* Wpisz następujące polecenia:
  
    `$ dmesg | grep sd*`
     
     Lub
  
    `$ fdisk –l`
  
    Te będą zwracane szczegóły ostatnio dodane dyski.
* Aby ustalić, czy jest to dysk StorSimple, użyj następujących poleceń:
  
    `cat /sys/block/<DISK>/device/model`
  
    Zwraca ciąg, który określa, czy jest to dysk StorSimple.

A mniej prawdopodobne, ale możliwa przyczyna może być również starych iscsid identyfikator PID procesu. Użyj następującego polecenia, aby wylogować się z sesji iSCSI:

    iscsiadm -m node --logout -p <Target_IP>

Powtórz to polecenie dla wszystkich interfejsów sieciowych podłączonych dla obiektu docelowego iSCSI, czyli urządzenia StorSimple. Po użytkownik ma wylogowany ze wszystkich sesji iSCSI, użyj obiektu docelowego iSCSI IQN Aby ponownie ustanowić sesji iSCSI. Wpisz następujące polecenie:

    iscsiadm -m node --login -T <TARGET_IQN>


Q. Nie mam pewności, czy urządzenie jest białej.

A. Aby sprawdzić, czy urządzenie jest białej, użyj następującego polecenia interaktywnego rozwiązywania problemów:

    multipathd –k
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


Aby uzyskać więcej informacji, przejdź do [Użyj Rozwiązywanie problemów z interaktywnego polecenia wielu ścieżek](http://www.centos.org/docs/5/html/5.1/DM_Multipath/multipath_config_confirm.html).

## <a name="list-of-useful-commands"></a>Lista poleceń przydatne
| Typ | Polecenie | Opis |
| --- | --- | --- |
| **iSCSI** |`service iscsid start` |Uruchomienie usługi iSCSI |
| &nbsp; |`service iscsid stop` |Zatrzymaj usługę iSCSI |
| &nbsp; |`service iscsid restart` |Ponowne uruchomienie usługi iSCSI |
| &nbsp; |`iscsiadm -m discovery -t sendtargets -p <TARGET_IP>` |Odnajdywanie dostępnych elementów docelowych na określony adres |
| &nbsp; |`iscsiadm -m node --login -T <TARGET_IQN>` |Zaloguj się do obiektu docelowego iSCSI |
| &nbsp; |`iscsiadm -m node --logout -p <Target_IP>` |Wyloguj się z obiektem docelowym iSCSI |
| &nbsp; |`cat /etc/iscsi/initiatorname.iscsi` |Nazwa inicjatora iSCSI drukowania |
| &nbsp; |`iscsiadm –m session –s <sessionid> -P 3` |Sprawdź stan sesji iSCSI i wolumin odnalezionych na hoście |
| &nbsp; |`iscsi –m session` |Przedstawia wszystkie sesje iSCSI między hostem a urządzenia StorSimple |
|  | | |
| **Wiele ścieżek** |`service multipathd start` |Demon wielościeżkowe Start |
| &nbsp; |`service multipathd stop` |Zatrzymaj demon wielościeżkowych |
| &nbsp; |`service multipathd restart` |Uruchom ponownie demon wielościeżkowych |
| &nbsp; |`chkconfig multipathd on` </br> LUB </br> `mpathconf –with_chkconfig y` |Włącz wielościeżkowe demon można uruchomić w czasie rozruchu |
| &nbsp; |`multipathd –k` |Uruchom konsolę interakcyjne do rozwiązywania problemów |
| &nbsp; |`multipath –l` |Lista połączeń wielościeżkowe i urządzeń |
| &nbsp; |`mpathconf --enable` |Utwórz plik przykładowy mulitpath.conf w `/etc/mulitpath.conf` |
|  | | |

## <a name="next-steps"></a>Kolejne kroki
Jak skonfigurować wielościeżkowego We/Wy na hoście z systemem Linux, również może być konieczne można znaleźć w następujących dokumentach CentoS 6.6:

* [Konfigurowanie wielościeżkowego wejścia/wyjścia na CentOS](http://www.centos.org/docs/5/html/5.1/DM_Multipath/setup_procedure.html)
* [Przewodnik szkolenia systemu Linux](http://linux-training.be/files/books/LinuxAdm.pdf)

