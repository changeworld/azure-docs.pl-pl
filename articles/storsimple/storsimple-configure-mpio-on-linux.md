---
title: Konfigurowanie wielościeżkowego wejścia/wyjścia na hoście z systemem StorSimple Linux | Dokumentacja firmy Microsoft
description: Konfigurowanie wielościeżkowego wejścia/wyjścia na podłączone do systemu Linux hoście z systemem CentOS 6.6 StorSimple
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
ms.openlocfilehash: b968cc29a7139a4a6db5d2dea8dd6f8f4e1c7ccd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60630793"
---
# <a name="configure-mpio-on-a-storsimple-host-running-centos"></a>Konfigurowanie wielościeżkowego wejścia/wyjścia na hoście StorSimple z systemem CentOS
W tym artykule opisano kroki wymagane do skonfigurowania Wielościeżkowe We/Wy (MPIO) na serwerze hosta Centos 6.6. Serwer hosta jest podłączony do Twojego urządzenia Microsoft Azure StorSimple, wysokiej dostępności za pośrednictwem inicjatorów iSCSI. Opisano w nim szczegółowo automatyczne odnajdowanie urządzeń wielościeżkowego i dlatego konfiguracja tylko w przypadku woluminów StorSimple.

Ta procedura ma zastosowanie do wszystkich modeli urządzeń StorSimple serii 8000.

> [!NOTE]
> Nie można użyć tej procedury dla urządzenia StorSimple w chmurze. Aby uzyskać więcej informacji zobacz, jak skonfigurować serwery hosta dla urządzenia w chmurze.


## <a name="about-multipathing"></a>O wielu ścieżek
Funkcja wielu ścieżek umożliwia można skonfigurować wiele ścieżek wejścia/wyjścia między serwerem hosta a urządzeniem pamięci masowej. Te ścieżki We/Wy są fizycznego połączenia sieci SAN, zawierających osobne kable, przełączniki, interfejsów sieciowych i kontrolerów. Wielu ścieżek agreguje ścieżki we/wy, aby skonfigurować nowe urządzenie, który jest skojarzony z wszystkie zagregowane ścieżki.

Celem wielu ścieżek składa się z dwóch etapów:

* **Wysoka dostępność**: W przypadku niepowodzenia jakiegokolwiek elementu w ścieżce operacji We/Wy (na przykład kabla, przełącznika, interfejsu sieciowego lub kontroler), zapewnia alternatywną ścieżkę.
* **Równoważenie obciążenia**: W zależności od konfiguracji urządzenia magazynującego może zwiększyć wydajność, wykrywając obciążenia w ścieżkach operacji We/Wy i dynamicznie ponowne równoważenie tych obciążeń.

### <a name="about-multipathing-components"></a>O składnikach wielu ścieżek
Wielu ścieżek w systemie Linux składa się z jądra i składników przestrzeń użytkownika jak przedstawione w poniższej tabeli.

* **Jądra**: Głównym składnikiem jest *mapowania urządzenia* który zmienia trasę operacji We/Wy i obsługuje tryb failover dla ścieżek i grup ścieżki.

* **Przestrzeń użytkownika**: Są to *narzędzia wielościeżkowego* zarządzające urządzeniami multipathed przez poinstruowanie wielościeżkowe moduł mapowania urządzenia, co należy zrobić. Narzędzia obejmują:
   
   * **Wielościeżkowe**: Wyświetla listę i konfiguruje multipathed urządzenia.
   * **Multipathd**: demona, który wykonuje wielościeżkowego i monitoruje ścieżki.
   * **Nazwa Devmap**: zapewnia zrozumiałej nazwy urządzenia do usługi udev devmaps.
   * **Kpartx**: mapuje liniowej devmaps partycji urządzenia, aby lepiej wielościeżkowe mapy partitionable.
   * **MultiPath.conf**: plik konfiguracji dla wielu ścieżek demona, który jest używany do zastąpienia tabeli wbudowana konfiguracja.

### <a name="about-the-multipathconf-configuration-file"></a>Plik konfiguracyjny multipath.conf — informacje
Plik konfiguracyjny `/etc/multipath.conf` zapewnia wiele funkcji wielu ścieżek konfigurowanych przez użytkownika. `multipath` Polecenia i demona jądra `multipathd` Użyj informacji zawartych w tym pliku. Plik jest konsultacje tylko podczas konfigurowania wielu ścieżek urządzeń. Upewnij się, że wszystkie zmiany zostały wprowadzone przed rozpoczęciem `multipath` polecenia. Jeśli zmodyfikujesz go później, należy uruchamiać i zatrzymywać multipathd ponownie, aby zmiany zaczęły obowiązywać.

Multipath.conf składa się z pięciu sekcji:

- **Poziom domyślnych ustawień systemowych** *(wartość domyślna)*: Można zastąpić domyślne ustawienia poziomu systemu.
- **Na czarnej liście urządzeń** *(czarna lista)*: Można określić listę urządzeń, które nie powinny być kontrolowane przez urządzenie mapowania.
- **Utworzyć listę niedozwolonych wyjątki** *(blacklist_exceptions)*: Można zidentyfikować konkretnych urządzeń powinien być traktowany jako wielościeżkowe urządzeń, nawet jeżeli listy zabronionych na liście.
- **Ustawienia określonego kontrolera magazynu** *(urządzenia)*: Można określić ustawienia konfiguracji, które będą stosowane do urządzeń, dostawcy i informacje o produkcie.
- **Określone ustawienia urządzenia** *(multipaths)*: W tej sekcji można użyć, aby dostroić ustawienia konfiguracji dla poszczególnych jednostek LUN.

## <a name="configure-multipathing-on-storsimple-connected-to-linux-host"></a>Konfigurowanie wielu ścieżek na StorSimple połączony z hostem systemu Linux
Można skonfigurować urządzenia StorSimple podłączonego do hosta systemu Linux o wysokiej dostępności i równoważenia obciążenia. Na przykład jeśli na hoście z systemem Linux ma dwa interfejsy połączony z siecią SAN, a urządzenie ma dwa interfejsy połączony z siecią SAN w taki sposób, że te interfejsy są w tej samej podsieci, następnie rozdamy 4 ścieżki. Jednak jeśli każdy interfejs danych w interfejsie urządzenia i host znajdują się w innej podsieci IP (a nie Routing), to tylko 2 ścieżki będą dostępne. Można skonfigurować wielościeżkowe automatycznie odkryć wszystkie dostępne ścieżki, wybieranie algorytmu równoważenia obciążenia dla tych ścieżek, zastosowanie określonych ustawień konfiguracji w przypadku woluminów StorSimple — tylko do włączenia i sprawdź wielu ścieżek.

Poniższa procedura opisuje sposób konfigurowania wielu ścieżek, po podłączeniu urządzenia StorSimple z dwoma interfejsami sieciowymi na hoście z dwoma interfejsami sieciowymi.

## <a name="prerequisites"></a>Wymagania wstępne
W tej sekcji przedstawiono wymagania wstępne dotyczące konfiguracji dla serwera CentOS i urządzeniu StorSimple.

### <a name="on-centos-host"></a>Na hoście CentOS
1. Upewnij się, że host z systemem CentOS ma 2 interfejsach sieciowych włączone. Wpisz:
   
    `ifconfig`
   
    Poniższy przykład przedstawia dane wyjściowe, gdy dwa interfejsy sieciowe (`eth0` i `eth1`) znajdują się na hoście.
   
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
1. Zainstaluj *iSCSI inicjatora utils* na serwerze CentOS. Wykonaj poniższe kroki, aby zainstalować *iSCSI inicjatora utils*.
   
   1. Zaloguj się jako `root` na hoście z systemem CentOS.
   1. Zainstaluj *iSCSI inicjatora utils*. Wpisz:
      
       `yum install iscsi-initiator-utils`
   1. Po *iSCSI inicjatora utils* został pomyślnie zainstalowany, uruchomienie usługi iSCSI. Wpisz:
      
       `service iscsid start`
      
       W przypadkach `iscsid` faktycznie nie można uruchomić i `--force` opcji mogą być potrzebne.
   1. Aby upewnić się, czy w czasie rozruchu włączono usługi inicjatora iSCSI, należy użyć `chkconfig` polecenie, aby włączyć usługę.
      
       `chkconfig iscsi on`
   1. Aby sprawdzić, czy zostało poprawnie skonfigurowane, uruchom polecenie:
      
       `chkconfig --list | grep iscsi`
      
       Poniżej pokazano przykładowe dane wyjściowe.
      
           iscsi   0:off   1:off   2:on3:on4:on5:on6:off
           iscsid  0:off   1:off   2:on3:on4:on5:on6:off
      
       W powyższym przykładzie widać, że środowisko iSCSI uruchomi w czasie rozruchu poziomy uruchamiania, 2, 3, 4 i 5.
1. Zainstaluj *urządzenia — mapowania wielościeżkowego*. Wpisz:
   
    `yum install device-mapper-multipath`
   
    Instalacja rozpocznie się. Typ **Y** nakazujący kontynuowanie po wyświetleniu monitu o potwierdzenie.

### <a name="on-storsimple-device"></a>Na urządzeniu StorSimple
Urządzenie StorSimple powinny mieć:

* Co najmniej dwa interfejsy włączone dla interfejsu iSCSI. Aby sprawdzić, czy dwa interfejsy są włączone iSCSI na urządzeniu StorSimple, wykonaj następujące kroki w klasycznej witrynie Azure portal dla urządzenia StorSimple:
  
  1. Zaloguj się do klasycznego portalu dla urządzenia StorSimple.
  1. Wybierz usługi Menedżer StorSimple, kliknij przycisk **urządzeń** i wybierz urządzenie StorSimple. Kliknij przycisk **Konfiguruj** i sprawdź ustawienia interfejsu sieciowego. Poniżej przedstawiono zrzut ekranu z dwoma interfejsami sieciowymi włączono interfejs iSCSI. Tutaj dane 2 i DATA 3 zarówno 10 GbE interfejsy są włączone dla interfejsu iSCSI.
     
      ![Konfiguracja wielościeżkowego wejścia/wyjścia StorsSimple dane 2](./media/storsimple-configure-mpio-on-linux/IC761347.png)
     
      ![Wielościeżkowe wejście/wyjście usługi StorSimple DATA 3 konfiguracji](./media/storsimple-configure-mpio-on-linux/IC761348.png)
     
      W **Konfiguruj** strony
     
     1. Upewnij się, że oba interfejsy sieciowe z obsługą interfejsu iSCSI. **Włączoną obsługę iSCSI** pola powinna być równa **tak**.
     1. Upewnij się, że interfejsy sieciowe mają taką samą szybkość, obie powinny być 1 GbE lub 10 GbE.
     1. Należy pamiętać adresów IPv4 interfejsów włączono interfejs iSCSI, a następnie zapisz do późniejszego użycia na hoście.
* Interfejsów iSCSI na urządzeniu StorSimple powinien być dostępny z serwera CentOS.
      Aby to sprawdzić, należy podać adresy IP interfejsów sieci iSCSI sieci usługi StorSimple na serwerze hosta. Polecenia używane i odpowiednie dane wyjściowe z dane2 (10.126.162.25) i DATA3 (10.126.162.26) znajdują się poniżej:
  
        [root@centosSS ~]# iscsiadm -m discovery -t sendtargets -p 10.126.162.25:3260
        10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
        10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target

### <a name="hardware-configuration"></a>Konfiguracja sprzętu
Zaleca się, że łączysz dwa interfejsy sieci iSCSI na osobnym ścieżkom w celu zapewnienia nadmiarowości. Na poniższym rysunku przedstawiono zalecana konfiguracja sprzętu wysokiej dostępności i równoważenia obciążenia wielu ścieżek serwera CentOS i urządzeniu StorSimple.

![Konfiguracja sprzętowa wielościeżkowego wejścia/wyjścia dla urządzenia StorSimple na hoście z systemem Linux](./media/storsimple-configure-mpio-on-linux/MPIOHardwareConfigurationStorSimpleToLinuxHost2M.png)

Jak pokazano na poprzednim rysunku:

* Urządzenie StorSimple znajduje się w konfiguracji aktywny / pasywny z dwóch kontrolerów.
* Dwa przełączniki sieci SAN są podłączone do sieci kontrolery urządzeń.
* Dwa inicjatorów iSCSI są włączone na urządzeniu StorSimple.
* Dwa interfejsy sieciowe są włączone na hoście z systemem CentOS.

Powyższa konfiguracja umożliwia uzyskanie 4 osobnym ścieżkom między urządzeniem i hosta, w przypadku interfejsów danych hosta i routing.

> [!IMPORTANT]
> * Zaleca się, że użytkownik nie należy mieszać 1 GbE i interfejsów sieciowych 10 GbE wielu ścieżek. Korzystając z dwóch interfejsów sieciowych, oba interfejsy powinna być taka sama typu.
> * Na urządzeniu StorSimple DATA0, dane1, DATA4 i DATA5 są interfejsów 1 GbE dane2 i DATA3 są interfejsów sieciowych 10 GbE. |
> 
> 

## <a name="configuration-steps"></a>Kroki konfiguracji
Kroki konfiguracji wielu ścieżek obejmują konfigurowanie dostępnych ścieżek do automatycznego odnajdowania, określając algorytm równoważenia obciążenia, aby użyć, włączania wielu ścieżek i na koniec Weryfikowanie konfiguracji. Każdy z tych kroków jest omówiona szczegółowo w poniższych sekcjach.

### <a name="step-1-configure-multipathing-for-automatic-discovery"></a>Krok 1: Konfigurowanie wielu ścieżek do automatycznego odnajdowania
Automatycznie odnalezione urządzenia obsługiwane wielościeżkowego i skonfigurowane.

1. Inicjowanie `/etc/multipath.conf` pliku. Wpisz:
   
     `mpathconf --enable`
   
    Powyższe polecenie spowoduje utworzenie `sample/etc/multipath.conf` pliku.
1. Uruchom usługę wielu ścieżek. Wpisz:
   
    `service multipathd start`
   
    Zobaczysz następujące dane wyjściowe:
   
    `Starting multipathd daemon:`
1. Włącz automatyczne odnajdowanie multipaths. Wpisz:
   
    `mpathconf --find_multipaths y`
   
    W rezultacie zostanie zmodyfikowany w sekcji ustawień domyślnych usługi `multipath.conf` jak pokazano poniżej:
   
        defaults {
        find_multipaths yes
        user_friendly_names yes
        path_grouping_policy multibus
        }

### <a name="step-2-configure-multipathing-for-storsimple-volumes"></a>Krok 2: Konfigurowanie wielu ścieżek dla woluminów StorSimple
Domyślnie wszystkie urządzenia są czarne wymienione w pliku multipath.conf i będą pomijane. Musisz utworzyć listę zablokowanych wyjątków, aby umożliwić wielu ścieżek dla woluminów z urządzenia StorSimple.

1. Edytuj `/etc/mulitpath.conf` pliku. Wpisz:
   
    `vi /etc/multipath.conf`
1. Zlokalizuj sekcję blacklist_exceptions w pliku multipath.conf. Urządzenie StorSimple musi pojawić się jako listy zabronionych wyjątek w tej sekcji. Można komentarz odpowiednie wiersze, w tym pliku, aby go zmodyfikować, jak pokazano poniżej (Użyj konkretny model urządzenia, którego używasz):
   
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

### <a name="step-3-configure-round-robin-multipathing"></a>Krok 3: Konfigurowanie wielu ścieżek działania okrężnego
Ten algorytm równoważenia obciążenia używa wszystkich dostępnych multipaths z aktywnym kontrolerem w sposób o zrównoważonym obciążeniu, działanie okrężne.

1. Edytuj `/etc/multipath.conf` pliku. Wpisz:
   
    `vi /etc/multipath.conf`
1. W obszarze `defaults` sekcji, ustaw `path_grouping_policy` do `multibus`. `path_grouping_policy` Określa domyślną ścieżkę grupowanie zasady mają dotyczyć multipaths nieokreślony. W sekcji Ustawienia domyślne będzie wyglądać jak poniżej.
   
        defaults {
                user_friendly_names yes
                path_grouping_policy multibus
        }

> [!NOTE]
> Najbardziej typowe wartości `path_grouping_policy` obejmują:
> 
> * tryb failover = 1 ścieżkę dla każdej grupy priorytet
> * multibus = wszystkie prawidłowe ścieżki w grupie o priorytecie 1
> 
> 

### <a name="step-4-enable-multipathing"></a>Krok 4: Włączanie wielu ścieżek
1. Uruchom ponownie `multipathd` demon. Wpisz:
   
    `service multipathd restart`
1. Dane wyjściowe będą się, jak pokazano poniżej:
   
        [root@centosSS ~]# service multipathd start
        Starting multipathd daemon:  [OK]

### <a name="step-5-verify-multipathing"></a>Krok 5. Sprawdź wielu ścieżek
1. Najpierw upewnij się, że iSCSI jest nawiązywane połączenie z urządzeniem StorSimple w następujący sposób:
   
   a. Dowiedz się, urządzenia StorSimple. Wpisz:
      
    ```
    iscsiadm -m discovery -t sendtargets -p  <IP address of network interface on the device>:<iSCSI port on StorSimple device>
    ```
    
    Dane wyjściowe, gdy adres IP dla DATA0 jest 10.126.162.25 i otwarciu portu 3260 na urządzeniu StorSimple dla ruchu wychodzącego iSCSI jest pokazany poniżej:
    
    ```
    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    ```

    Skopiuj nazwę IQN urządzenia StorSimple, `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`, z poprzedniego danych wyjściowych.

   b. Nawiązać połączenia z urządzeniem przy użyciu nazwa IQN celu. Urządzenie StorSimple jest obiektem docelowym iSCSI. Wpisz:

    ```
    iscsiadm -m node --login -T <IQN of iSCSI target>
    ```

    Poniższy przykład przedstawia dane wyjściowe z obiektem docelowym IQN z `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`. Dane wyjściowe wskazują, że pomyślnie łączysz się dwa interfejsy sieci iSCSI na urządzeniu.

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

    Jeśli zostanie wyświetlony tylko jeden host interfejsu i w tym miejscu dwie ścieżki, następnie należy włączyć zarówno interfejsy na hoście dla interfejsu iSCSI. Możesz wykonać [szczegółowe instrukcje w dokumentacji systemu Linux](https://access.redhat.com/documentation/Red_Hat_Enterprise_Linux/5/html/Online_Storage_Reconfiguration_Guide/iscsioffloadmain.html).

1. Wolumin jest narażony na serwerze CentOS z urządzenia StorSimple. Aby uzyskać więcej informacji, zobacz [krok 6: Tworzenie woluminu](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) za pośrednictwem witryny Azure portal na urządzeniu StorSimple.

1. Sprawdź dostępne ścieżki. Wpisz:

      ```
      multipath –l
      ```

      Poniższy przykład przedstawia dane wyjściowe dwa interfejsy sieci na urządzenia StorSimple podłączonego do karty sieciowej jednego hosta z dwóch ścieżek dostępne.

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
Ta sekcja zawiera wskazówki przydatne, jeśli napotkasz problemy podczas konfiguracji wielu ścieżek.

PYTANIE: Nie ma zmian w `multipath.conf` plików z zastosowaniem.

A. Jeśli zostały wprowadzone jakiekolwiek zmiany, aby `multipath.conf` pliku, należy ponownie uruchomić usługę wielu ścieżek. Wpisz następujące polecenie:

    service multipathd restart

PYTANIE: Czy włączono dwa interfejsy sieciowe na urządzeniu StorSimple i dwa interfejsy sieciowe na hoście. Czy mogę wyświetlić listę dostępnych ścieżek, widać się tylko dwie ścieżki. Oczekiwana się cztery dostępne ścieżki.

A. Upewnij się, że dwie ścieżki są w tej samej podsieci i routing. Jeśli interfejsy sieciowe są na różnych sieci VLAN, a nie obsługuje routingu, widoczne będą tylko dwie ścieżki. Jest jednym ze sposobów, aby to sprawdzić, aby upewnić się, że może osiągnąć interfejsów hosta z interfejsem sieciowym na urządzeniu StorSimple. Konieczne będzie [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) jak ta weryfikacja jest możliwe wyłącznie za pośrednictwem sesji pomocy technicznej.

PYTANIE: Gdy mogę wyświetlić listę dostępnych ścieżek, nie widzę żadnych danych wyjściowych.

A. Zazwyczaj nie widzisz żadnych ścieżek multipathed sugeruje problem z demonem wielu ścieżek, i jest to najprawdopodobniej wszelkich problemów, w tym miejscu znajduje się w `multipath.conf` pliku.

Byłoby warte sprawdzanie, czy rzeczywiście widać niektóre dyski po nawiązaniu połączenia z docelowym, zgodnie z braku odpowiedzi z ofert wielościeżkowe również może oznaczać, że nie masz żadnych dysków.

* Aby ponownie przeskanować magistrali SCSI, użyj następującego polecenia:
  
    `$ rescan-scsi-bus.sh` (część pakietu sg3_utils)
* Wpisz następujące polecenia:
  
    `$ dmesg | grep sd*`
     
     Lub
  
    `$ fdisk –l`
  
    Zwróci te szczegółowe informacje o ostatnio dodanych dysków.
* Aby ustalić, czy jest on dyskiem StorSimple, użyj następujących poleceń:
  
    `cat /sys/block/<DISK>/device/model`
  
    Spowoduje to zwrócenie ciąg, który określa, czy jest on dyskiem StorSimple.

Element mniej prawdopodobne, ale możliwa przyczyna może być również starych iscsid pid. Użyj następującego polecenia, aby wylogować się z sesji iSCSI:

    iscsiadm -m node --logout -p <Target_IP>

Powtórz to polecenie dla wszystkich interfejsów sieciowych podłączonych na obiekt docelowy iSCSI, czyli urządzenia StorSimple. Po użytkownik zalogował się ze wszystkich sesji iSCSI, należy użyć obiektu docelowego iSCSI IQN Aby ponownie ustanowić sesję iSCSI. Wpisz następujące polecenie:

    iscsiadm -m node --login -T <TARGET_IQN>


PYTANIE: Nie mam pewności, czy urządzenie jest na liście dozwolonych.

A. Aby sprawdzić, czy urządzenie znajduje się na białej liście, użyj następującego polecenia interakcyjnego rozwiązywania problemów:

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


Aby uzyskać więcej informacji, przejdź do [Użyj Rozwiązywanie problemów z poleceń interaktywnych wielu ścieżek](http://www.centos.org/docs/5/html/5.1/DM_Multipath/multipath_config_confirm.html).

## <a name="list-of-useful-commands"></a>Lista przydatnych poleceń
| Type | Polecenie | Opis |
| --- | --- | --- |
| **iSCSI** |`service iscsid start` |Uruchomienie usługi iSCSI |
| &nbsp; |`service iscsid stop` |Zatrzymaj usługę iSCSI |
| &nbsp; |`service iscsid restart` |Uruchom ponownie usługę iSCSI |
| &nbsp; |`iscsiadm -m discovery -t sendtargets -p <TARGET_IP>` |Odnajdywanie dostępnych elementów docelowych pod określonym adresem |
| &nbsp; |`iscsiadm -m node --login -T <TARGET_IQN>` |Zaloguj się do obiektu docelowego iSCSI |
| &nbsp; |`iscsiadm -m node --logout -p <Target_IP>` |Wyloguj się z obiektem docelowym iSCSI |
| &nbsp; |`cat /etc/iscsi/initiatorname.iscsi` |Nazwa inicjatora iSCSI drukowania |
| &nbsp; |`iscsiadm –m session –s <sessionid> -P 3` |Sprawdź stan sesji iSCSI i wolumin, o których odnaleźć na hoście |
| &nbsp; |`iscsi –m session` |Przedstawia wszystkie sesje iSCSI między hostem a urządzenia StorSimple |
|  | | |
| **Wielu ścieżek** |`service multipathd start` |Demon wielościeżkowe Start |
| &nbsp; |`service multipathd stop` |Zatrzymaj wielościeżkowe demona |
| &nbsp; |`service multipathd restart` |Ponownie uruchom demona wielościeżkowych |
| &nbsp; |`chkconfig multipathd on` </br> LUB </br> `mpathconf –with_chkconfig y` |Włącz wielościeżkowe demona można uruchomić w czasie rozruchu |
| &nbsp; |`multipathd –k` |Uruchom konsolę interaktywne do rozwiązywania problemów |
| &nbsp; |`multipath –l` |Lista połączeń wielościeżkowe i urządzeń |
| &nbsp; |`mpathconf --enable` |Utwórz przykładowy plik mulitpath.conf w `/etc/mulitpath.conf` |
|  | | |

## <a name="next-steps"></a>Kolejne kroki
Jak skonfigurować wielościeżkowego wejścia/wyjścia na hoście z systemem Linux, konieczne może być można znaleźć w następujących dokumentach CentoS 6.6:

* [Konfigurowanie wielościeżkowego wejścia/wyjścia na CentOS](http://www.centos.org/docs/5/html/5.1/DM_Multipath/setup_procedure.html)
* [Przewodnik szkolenia systemu Linux](http://linux-training.be/linuxsys.pdf)

