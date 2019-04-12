---
title: Konfigurowanie programu IBM Db2 HADR na maszynach wirtualnych Azure (maszyny wirtualne) | Dokumentacja firmy Microsoft
description: Ustanów wysoka dostępność programu IBM Db2 LUW na maszynach wirtualnych Azure (maszyny wirtualne).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/10/2019
ms.author: juergent
ms.openlocfilehash: d47c64fb8b148e39eab09a33c3241ac7dd3ea537
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502131"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105

[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm
[sles-pacemaker]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker
[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[nfs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>Wysoka dostępność programu IBM Db2 LUW na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server za pomocą program Pacemaker

IBM Db2 LUW (Linux, Unix lub Windows) w [konfiguracji HADR](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) składają się z jednego węzła, który uruchamia wystąpienie podstawowej bazy danych i co najmniej jeden węzeł, który uruchamia wystąpienie pomocniczej bazy danych. Do wystąpienia podstawowej bazy danych wprowadzenie Replikacja zmian na wystąpienie pomocniczej bazy danych synchronicznie lub asynchronicznie, zależne od konfiguracji. 

W tym artykule opisano sposób wdrażania i konfigurowania maszyn wirtualnych, zainstalować framework klaster i zainstalować i skonfigurować IBM Db2 LUW HADR konfiguracji. Artykuł nie wyjaśniono, jak zainstalować i skonfigurować program IBM Db2 LUW HADR lub SAP instalacji oprogramowania. Aby osiągnąć te zadania znajdują się odwołania do instrukcje instalacji SAP i IBM. Koncentruje się na części, które są specyficzne dla środowiska platformy Azure. 

Obsługiwane wersje programu IBM Db2 są 10,5 lub nowszy, zgodnie z opisem w Uwaga SAP #[1928533].

Przeczytaj następujące uwagi SAP i dokumentacji przed zbliża się do instalacji:

| SAP Note | Opis |
| --- | --- |
| [1928533] | Aplikacje środowiska SAP na platformie Azure: Obsługiwane produkty i typy maszyn wirtualnych platformy Azure |
| [2015553] | SAP na platformie Microsoft Azure: Wymagania wstępne dotyczące obsługi |
| [2178632] | Klucz metryki monitorowania dla rozwiązania SAP na platformie Microsoft Azure |
| [2191498] | SAP w systemie Linux przy użyciu platformy Azure: Rozszerzone monitorowanie |
| [2243692] | Systemu Linux na platformie Microsoft Azure (IaaS) maszyny Wirtualnej: Problemy dotyczące licencji SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: Uwagi dotyczące instalacji |
| [1999351] |Rozwiązywanie problemów z rozszerzonego monitorowania platformy Azure dla rozwiązania SAP |
| [2233094] |DB6: Aplikacje środowiska SAP na platformie Azure przy użyciu programu IBM Db2 dla systemów Linux, UNIX i Windows — informacje dodatkowe |
| [1612105] |DB6: Często zadawane pytania dotyczące odzyskiwania po awarii wysoką dostępność bazy danych Db2 (HADR) |


| Dokumentacja | 
| --- |
| [WIKI społeczności SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) ma wszystkie wymagane informacje o SAP dla systemu Linux |
| [Azure maszyny wirtualne, planowania i implementacji dla rozwiązania SAP w systemie Linux] [ planning-guide] przewodnik |
| [Wdrażania maszyn wirtualnych platformy Azure dla rozwiązania SAP w systemie Linux] [ deployment-guide] (w tym artykule) |
| [Wdrażania systemu DBMS na maszynach wirtualnych platformy Azure dla rozwiązania SAP w systemie Linux] [ dbms-guide] przewodnik |
| [Obciążeń SAP na Azure Lista kontrolna dotycząca planowania i wdrażania][azr-sap-plancheck] |
| [SUSE Linux Enterprise Server dla SAP aplikacji 12 z dodatkiem SP3 najlepsze praktyki prowadnic][sles-for-sap-bp] |
| [SUSE Linux Enterprise wysoką dostępność rozszerzenia 12 z dodatkiem SP3][sles-ha-guide] |
| [Wdrażania systemu DBMS na IBM Db2 Azure maszyn wirtualnych w przypadku obciążeń SAP][dbms-db2] |
| [Odzyskiwanie po awarii w usłudze IBM Db2 wysokiej dostępności 11.1][db2-hadr-11.1] |
| [Odzyskiwanie po awarii w usłudze IBM Db2 wysokiej dostępności R 10.5][db2-hadr-10.5] |

## <a name="overview"></a>Omówienie
Aby osiągnąć wysoką dostępność, IBM Db2 LUW z HADR jest zainstalowany na co najmniej dwóch maszyn wirtualnych platformy Azure, które są wdrażane w [zestawu dostępności platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) lub wielu [strefy dostępności platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones). Grafika poniżej przedstawiono konfigurację serwera bazy danych dwóch maszyn wirtualnych platformy Azure. Zarówno serwer bazy danych maszyn wirtualnych platformy Azure ma swoje własne magazynie dołączonym i są gotowe do działania. W HADR jedno wystąpienie bazy danych w jednej z maszyn wirtualnych platformy Azure, ma rolę podstawowego wystąpienia. Wszyscy klienci są połączone z tym wystąpieniem podstawowego. Wszystkie zmiany w transakcji bazy danych są zachowywane lokalnie w dzienniku transakcji bazy danych Db2. Jak rekordy dziennika transakcji są zachowywane lokalnie, rekordy są przesyłane za pośrednictwem protokołu TCP/IP z wystąpieniem bazy danych na drugi serwer bazy danych, rezerwowy serwer lub wystąpienie w trybie gotowości. Rezerwowe wystąpienie aktualizuje lokalnej bazy danych przez stopniowe do przodu przeniesionych transakcji rekordów dziennika. Więc rezerwowy serwer jest zsynchronizowany z serwerem podstawowym.

HADR jest tylko funkcje replikacji. W nim nie wykrywanie awarii i nie automatycznego urządzeń przejęcia lub pracy awaryjnej. Przejęcie lub przeniesienie do stanu wstrzymania musi być inicjowana ręcznie przez administratora bazy danych. Aby osiągnąć automatycznego przejęcia i wykrywanie awarii, można użyć program Linux Pacemaker funkcji klaster. Program pacemaker monitoruje serwery/wystąpień dwie bazy danych. Jeśli podstawowe wystąpienie serwera bazy danych/ulegnie awarii, inicjuje program Pacemaker **automatyczne** HADR przejęcia przez rezerwowy serwer i gwarantuje również, że wirtualny adres IP jest przypisywany z nowym serwerem podstawowym.

![Omówienie IBM Db2 wysokiej dostępności](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

SAP serwerów aplikacji, aby połączyć się z podstawowej bazy danych należy na potrzeby wirtualnego hosta i wirtualnego adresu IP. W przypadku przejścia w tryb failover serwery aplikacji SAP połączy się nowe wystąpienie podstawowej bazy danych. W środowisku platformy Azure [usługi Azure Load Balancer](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) jest wymagane do używania wirtualnego adresu IP w taki sposób, wymaganych do HADR IBM Db2. 

Aby w pełni zrozumieć, jak IBM Db2 LUW HADR i program Pacemaker dopasowuje się do wysokiej dostępności konfiguracji systemu SAP poniższy obraz przedstawia omówienie konfiguracji charakteryzującej systemu SAP, na podstawie bazy danych programu IBM Db2. Ten artykuł dotyczy tylko programu IBM Db2 i odwołuje się do innych artykułów, jak skonfigurować inne składniki systemu SAP.

![IBM DB2 HA pełne omówienie środowiska](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-steps-needed"></a>Ogólne omówienie czynnościach, które
Aby wdrożyć konfiguracji programu IBM Db2, te kroki należy need:

  + Planowanie środowiska
  + Wdrażanie maszyn wirtualnych
  + Aktualizowanie systemu SUSE Linux i konfigurowanie systemów plików
  + Zainstalowanie i skonfigurowanie program Pacemaker
  + Zainstaluj [NFS o wysokiej dostępności][nfs-ha]
  + Zainstaluj [ASCS/Wywołujących w osobnym klastrze][ascs-ha] 
  + Zainstaluj bazę danych programu IBM Db2 z opcją rozproszonych/wysokiej dostępności (SWPM)
  + Instalacja/tworzenie pomocniczej bazy danych node i wystąpienia i konfigurowanie HADR
  + Upewnij się, że działa HADR
  + Zastosuj konfigurację program Pacemaker do kontroli programu IBM Db2
  + Konfigurowanie modułu Azure Load Balancer 
  + Zainstaluj podstawowy i okno dialogowe serwerów aplikacji
  + Sprawdź/dostosowanie konfigurację dla serwerów aplikacji SAP
  + W tryb failover / przejęcia testów



## <a name="planning-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Planowanie infrastruktury platformy Azure do hostowania IBM Db2 LUW z HADR

Wykonaj strugania przed wykonaniem wdrożenia. Czy tworzysz podstawę do wdrażania konfiguracji bazy danych Db2 z HADR na platformie Azure. Kluczowe elementy, które muszą być częścią planowanie IMB bazy danych Db2 LUW (część bazy danych środowiska SAP).

| Temat | Krótki opis |
| --- | --- |
| Definiowanie grup zasobów platformy Azure | Grupy zasobów, gdy wdrażanie maszyn wirtualnych, sieci wirtualnej, usługa Azure Load Balancer i innych zasobów. Może być istniejącym lub nowym |
| Sieć wirtualna / podsieć definicji | Gdy maszyny wirtualne programu IBM Db2 i usługi Azure Load Balancer pojawiają się do wdrożenia. Może być istniejących i nowo utworzony |
| Maszyny wirtualne hostujące rozwiązania IBM Db2 LUW | Rozmiar maszyny Wirtualnej, Magazyn, sieci, adres IP |
| Nazwa hosta wirtualnego i wirtualnego adresu IP dla bazy danych programu IBM Db2| Wirtualnego adresu IP/nazwę hosta, która jest używana do połączenia serwerów aplikacji SAP. **DB-virt-hostname**, **virt-db-ip** |
| Preferowane platformy Azure | Preferowane platformy Azure lub preferowane interwencja (zdecydowanie zalecane). Nie będzie mógł metodę, aby uniknąć sytuacji mózg podziału |
| INTERWENCJA MASZYNY WIRTUALNEJ | Rozmiar pamięci maszyny wirtualnej interwencja, Magazyn, sieć |
| Azure Load Balancer | Użycie Basic lub Standard (zalecane), port dla bazy danych Db2 (zalecamy 62500) sondy **port sondy** |
| Rozpoznawanie nazw| Jak rozpoznawanie nazw działa w środowisku. Zdecydowanie zaleca się usługi DNS. Można użyć pliku hostów lokalnych |
    
Szczegółowe informacje na temat użycia program Pacemaker systemu Linux, na platformie Azure można znaleźć w następujących artykułach:

- [Konfigurowanie program Pacemaker w systemie SUSE Linux Enterprise Server na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)



## <a name="deployment-on-suse-linux"></a>Wdrażanie w systemie SUSE Linux

Agent zasobów dla programu IBM Db2 LUW znajduje się w systemie SUSE Linux Enterprise Server dla aplikacji SAP. Dla ustawienia opisane w niniejszym dokumencie jest to konieczne, użyj SUSE Linux Server dla aplikacji SAP. W portalu Azure Marketplace zawiera obraz dla SUSE Enterprise Server 12 aplikacje SAP, który służy do wdrażania nowych maszyn wirtualnych platformy Azure. Należy pamiętać o modele różnych technicznej oferowanymi przez SUSE galerii platformy Azure podczas wybierania obrazu maszyny Wirtualnej w galerii maszyn wirtualnych platformy Azure. 

### <a name="hosts---dns-updates"></a>Hosty — aktualizacje rekordów DNS
Sporządź listę wszystkich nazw hostów, w tym nazwy hostów wirtualnych i zaktualizuj serwery DNS, aby umożliwić właściwy adres IP do rozpoznawania nazwy hosta. W przypadku serwer DNS nie istnieje lub nie jest możliwe zaktualizować i utworzenie wpisów DNS, należy korzystać z plików hostów lokalnych w poszczególnych maszyn wirtualnych, które uczestniczą w tym scenariuszu. W przypadku za pomocą pozycji plików hosta, należy upewnić się, że wpisy są stosowane do wszystkich maszyn wirtualnych w środowisku systemu SAP. Jednak zaleca się używać Twój rekord DNS, który najlepiej jest rozszerzony na platformę Azure


### <a name="manual-deployment"></a>Ręczne wdrażanie

Upewnij się, że wybranego systemu operacyjnego jest obsługiwana przez oprogramowanie firmy IBM/SAP dla IBM Db2 LUW. Lista obsługiwanych wersji systemu operacyjnego dla maszyn wirtualnych platformy Azure i bazy danych Db2 wersjami jest dostępna w Uwaga SAP [1928533]. Lista wersji systemu operacyjnego przez poszczególne wersje bazy danych Db2 jest dostępna w macierzy dostępności produktów SAP. Zdecydowanie zaleca się co najmniej w systemie SLES 12 z dodatkiem SP3 ze względu na ulepszenia wydajności dotyczące platformy Azure w tej lub nowszej wersje systemu SUSE Linux.

1. Utwórz/wybrać grupę zasobów
2. Utwórz/wybrać sieci wirtualnej i podsieci
3. Tworzenie zestawu dostępności platformy Azure lub wdrożyć w strefie dostępności
    + Dostępność zestawu — konfigurowanie domeny max aktualizacji do dwóch
4. Tworzenie maszyny wirtualnej 1.
    + Na użytek SLES SAP obraz w galerii systemu Azure
    + Wybierz platformy Azure zestaw dostępności w utworzonej w kroku 3 lub wybierz strefę dostępności
5.  Tworzenie maszyny wirtualnej 2.
    + Na użytek SLES SAP obraz w galerii systemu Azure
    + Wybierz platformy Azure zestaw dostępności w utworzonej w kroku 3. lub wybierz strefę dostępności — w tej samej strefie tak jak w kroku 3.
6. Dodawanie dysków danych do maszyn wirtualnych — Sprawdź plik zalecenie dotyczące instalacji systemu plików w artykule [wdrażania systemu DBMS na IBM Db2 Azure maszyn wirtualnych w przypadku obciążeń SAP][dbms-db2]

## <a name="create-the-pacemaker-cluster"></a>Tworzenie klastra program Pacemaker
    
Postępuj zgodnie z instrukcjami w [konfigurowania program Pacemaker w systemie SUSE Linux Enterprise Server na platformie Azure][sles-pacemaker] do tworzenia klastra program Pacemaker podstawowe dla tego serwera programu IBM Db2. 

## <a name="install-ibm-db2-luw-and-sap-environment"></a>Zainstaluj środowisko SAP i IBM Db2 LUW

Przed rozpoczęciem instalacji środowiska SAP, IBM Db2 LUW — na podstawie przeglądu (linków dostępnych na początku artykułu):

+ Dokumentacja platformy Azure
+ Dokumentacja usługi SAP
+ Dokumentację firmy IBM

Sprawdź podręczniku instalacji programu SAP na temat sposobu instalowania aplikacji NetWeaver oparte na IBM Db2 LUW.

Możesz znaleźć przewodniki na SAP pomocy portalu przy użyciu [wyszukiwania Przewodnik po instalacji SAP][sap-instfind]

Możesz zastosować filtr wyszukiwania w celu zmniejszenia liczby przewodniki udostępniono filtry:

+ Chcę: "Zainstaluj nowy system"
+ Moja baza danych: "IBM Db2 dla systemów Linux, Unix i Windows"
+ Dodatkowe filtry dla wersji oprogramowania SAP Netweaver, konfiguracja stosu lub systemu operacyjnego.

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Instalacja wskazówki dotyczące konfigurowania IBM Db2 LUW z HADR

Skonfiguruj podstawowy wystąpienie bazy danych IBM Db2 LUW:

- Użyć opcji rozproszonej lub o wysokiej dostępności
- Zainstaluj wystąpienie SAP ASCS/Wywołujących i bazy danych
- Utwórz kopię zapasową bazy danych nowo zainstalowany


> [!IMPORTANT] 
> Zanotuj "port komunikacyjny bazy danych" ustawiane podczas instalacji. Musi być tego samego numeru portu dla obu wystąpień bazy danych

Do konfigurowania serwera bazy danych w stanie wstrzymania, przy użyciu procedury kopiowania jednorodny system SAP, wykonaj następujące kroki:

  - Użyj opcji kopię systemu — docelowe systemy - rozproszonych — wystąpienie bazy danych.
  - Jako metodę kopiowania wybierz jednorodnych kopia systemu tak, aby kopia zapasowa służy do przywrócenia kopii zapasowej w wystąpieniu/serwera rezerwy dynamicznej
  - Po przejściu do kroku wyjścia, aby przywrócić bazę danych związanym z kopiowaniem jednorodny system Zamknij Instalatora. Przywróć bazę danych z kopii zapasowej hostem głównym. Wszystkie fazy kolejnej instalacji zostały już wykonane na serwerze podstawowej bazy danych
- Konfigurowanie HADR dla programu IBM Db2

> [!NOTE]
> Instalacja/konfiguracja specyficzna dla platformy Azure i program Pacemaker. W trakcie procedury instalacji za pośrednictwem Menedżera inicjowania obsługi oprogramowania SAP jest jawne pytanie o wysokiej dostępności dla programu IBM Db2 LUW:
>+ Nie należy wybierać pureScale IBM Db2
>+ Nie należy wybierać "Zainstaluj IBM Tivoli systemu automatyzacji dla Multiplatforms
>+ Nie należy wybierać "Generowanie plików konfiguracji klastra"

> [!NOTE]
>W przypadku używania urządzenia interwencja dla program Pacemaker w systemie Linux, należy ustawić parametry HADR bazy danych Db2
>+ Czas trwania okna elementu równorzędnego HADR (w sekundach) (HADR_PEER_WINDOW) = 300  
>+ Wartość limitu czasu HADR (HADR_TIMEOUT) = 60

> [!NOTE]
>Przy użyciu usługi Azure program Pacemaker preferowane agenta:
>+ Czas trwania okna elementu równorzędnego HADR (w sekundach) (HADR_PEER_WINDOW) = 900  
>+ Wartość limitu czasu HADR (HADR_TIMEOUT) = 60

Parametry są zalecane, na podstawie w początkowej fazie testowania trybu failover/przejęcie. Jest to konieczne, aby przetestować funkcje właściwe przejęcia i trybu failover przy użyciu tych ustawień parametrów. Ponieważ może się różnić w poszczególnych ustawieniach, te parametry mogą wymagać dostosowania. 

> [!IMPORTANT]
> Przeznaczone dla programu IBM Db2 w konfiguracji HADR przy użyciu normalnego uruchamiania - wystąpienie/w gotowości pomocniczej bazy danych musi być uruchomiona, zanim będzie można uruchomić wystąpienia podstawowej bazy danych.

Dla celów demonstracyjnych i procedury opisane w niniejszym dokumencie, baza danych jest identyfikator SID **PTR**.

##### <a name="ibm-db2-hadr-check"></a>Sprawdzanie HADR programu IBM Db2
Po skonfigurowaniu HADR i stanie elementów RÓWNORZĘDNYCH i POŁĄCZONO w węzłach podstawowego i będącymi w gotowości.

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
# Database Member 0 -- Database PTR -- Active -- Up 1 days 01:51:38 -- Date 2019-02-06-15.35.28.505451
# 
#                             <b>HADR_ROLE = PRIMARY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 1
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.170561 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6137
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 13
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000025
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223713
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 374400
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#        STANDBY_RECV_REPLAY_GAP(bytes) = 0
#                      PRIMARY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#                      STANDBY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:40:25.000000 (1549467625)
#              READS_ON_STANDBY_ENABLED = N

#Secondary output:
# Database Member 0 -- Database PTR -- Standby -- Up 1 days 01:46:43 -- Date 2019-02-06-15.38.25.644168
# 
#                             <b>HADR_ROLE = STANDBY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 0
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.205067 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6186
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 5
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000023
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223725
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 372480
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#        STANDBY_RECV_REPLAY_GAP(bytes) = 155
#                      PRIMARY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#                      STANDBY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:43:19.000000 (1549467799)
#              READS_ON_STANDBY_ENABLED = N
</code></pre>



## <a name="db2-pacemaker-configuration"></a>Program Pacemaker bazy danych Db2 konfiguracji

Gdy używasz program Pacemaker do automatycznej pracy awaryjnej w przypadku awarii węzła, należy odpowiednio skonfigurować wystąpienia bazy danych Db2 i program Pacemaker. W tej sekcji przedstawiono ten typ konfiguracji.

Następujące elementy mają prefiks albo:

- **[A]**  — mające zastosowanie do wszystkich węzłów
- **[1]**  — dotyczy to tylko węzeł 1 
- **[2]**  — dotyczy to tylko węzeł 2.

<!-- Yast is a fixed term in Linux and not a spelling error -->

**[A]**  Wymagania wstępne dotyczące konfiguracji program Pacemaker:
1. Zamknij oba serwer bazy danych z bazy danych db2 użytkownika\<sid > z db2stop
2. Zmień środowisko powłoki dla bazy danych db2\<sid > użytkownikowi "/ bin/ksh" - zaleca się korzystanie z narzędzia Yast 


### <a name="pacemaker-configuration"></a>Program pacemaker konfiguracji:

**[1]**  IBM Db2 HADR konkretnej konfiguracji program Pacemaker
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]**  Zasobów Utwórz IBM Db2
<pre><code># Replace **bold strings** with your instance name db2sid, database SID and virtual IP address/Azure Load Balancer

sudo crm configure primitive rsc_Db2_db2ptr_<b>PTR</b> db2 \
        params instance="<b>db2ptr</b>" dblist="<b>PTR</b>" \
        op start interval="0" timeout="130" \
        op stop interval="0" timeout="120" \
        op promote interval="0" timeout="120" \
        op demote interval="0" timeout="120" \
        op monitor interval="30" timeout="60" \
        op monitor interval="31" role="Master" timeout="60"

# Configure virutal IP - same as Azure Load Balancer IP
sudo crm configure primitive rsc_ip_db2ptr_<b>PTR</b> IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="<b>10.100.0.10</b>"

# Configure probe port for Azure load Balancer
sudo crm configure primitive rsc_nc_db2ptr_<b>PTR</b> anything \
        params binfile="/usr/bin/nc" cmdline_options="-l -k <b>62500</b>" \
        op monitor timeout="20s" interval="10" depth="0"

sudo crm configure group g_ip_db2ptr_<b>PTR</b> rsc_ip_db2ptr_<b>PTR</b> rsc_nc_db2ptr_<b>PTR</b>

sudo crm configure ms msl_Db2_db2ptr_<b>PTR</b> rsc_Db2_db2ptr_<b>PTR</b> \
        meta target-role="Started" notify="true"

sudo crm configure colocation col_db2_db2ptr_<b>PTR</b> inf: g_ip_db2ptr_<b>PTR</b>:Started msl_Db2_db2ptr_<b>PTR</b>:Master

sudo crm configure order ord_db2_ip_db2ptr_<b>PTR</b> inf: msl_Db2_db2ptr_<b>PTR</b>:promote g_ip_db2ptr_<b>PTR</b>:start

sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

**[1]**  Zasoby start IBM Db2 — umieść program Pacemaker w trybie konserwacji
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]**  Upewnij się, że kondycja klastra jest ok i wszystkie zasoby są uruchamiane. Nie jest to ważne w węźle, które zasoby są uruchomione.
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>2 skonfigurowanych węzłów
# <a name="5-resources-configured"></a>5 zasobów skonfigurowana

# <a name="online--azibmdb01-azibmdb02-"></a>Online: [azibmdb01 azibmdb02]

# <a name="full-list-of-resources"></a>Pełną listę zasobów:

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>interwencja pomocą metody stonith (stonith:external / interwencja): Rozpoczęto azibmdb02
#  <a name="resource-group-gipdb2ptrptr"></a>Grupa zasobów: g_ip_db2ptr_PTR
#      <a name="rscipdb2ptrptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR (ocf::heartbeat:IPaddr2):       Rozpoczęto azibmdb02
#      <a name="rscncdb2ptrptr--ocfheartbeatanything------started-azibmdb02"></a>rsc_nc_db2ptr_PTR (ocf::heartbeat: wszystko):      Rozpoczęto azibmdb02
#  <a name="masterslave-set-msldb2db2ptrptr-rscdb2db2ptrptr"></a>Ustaw główny/podrzędny: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>Wzorce: [azibmdb02]
#      <a name="slaves--azibmdb01-"></a>Elementy podrzędne: [azibmdb01]
</pre>

> [!IMPORTANT]
> Zarządzanie program Pacemaker klastrowanego wystąpienia bazy danych Db2 musi odbywać się przy użyciu narzędzi program Pacemaker. Za pomocą polecenia bazy danych db2 (na przykład db2stop) zostanie wykryty przez program Pacemaker jako awarii zasobu. W przypadku obsługi można umieścić węzłów lub zasobów, które znajdują się w tryb konserwacji program Pacemaker zostanie zawieszone monitorowania zasobów i db2 normalne administracji polecenia mogą być używane.


### <a name="configure-azure-load-balancer"></a>Konfigurowanie modułu Azure Load Balancer
Zalecane jest użycie [standardowy SKU modułu równoważenia obciążenia Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

1. Konfigurowanie usługi Azure Load Balancer (za pośrednictwem witryny Azure portal). Najpierw Utwórz pulę adresów IP frontonu:

   1. W witrynie Azure portal Otwórz usługę równoważenia obciążenia platformy Azure, wybierz **puli adresów IP frontonu**i wybierz **Dodaj**.
   2. Wprowadź nazwę nowej puli adresów IP frontonu (na przykład **połączenia bazy danych Db2**).
   3. Ustaw **przypisania** do **statyczne** i wpisz adres IP **IP wirtualnych** zdefiniowane na początku.
   4. Kliknij przycisk **OK**.
   5. Po utworzeniu nowej puli adresów IP frontonu, należy pamiętać, puli adresów IP.

2. Następnym krokiem jest, aby utworzyć pulę zaplecza:

   1. W witrynie Azure portal Otwórz usługę równoważenia obciążenia platformy Azure, wybierz **pule zaplecza**i wybierz **Dodaj**.
   2. Wprowadź nazwę nowej puli zaplecza (na przykład **zaplecze bazy danych Db2**).
   3. Wybierz **Dodaj maszynę wirtualną**.
   4. Wybierz Dostępność zestawu/maszyny wirtualne hostujące bazę danych programu IBM Db2 utworzonego w kroku 3.
   5. Wybierz maszyny wirtualne klastra programu IBM Db2.
   6. Kliknij przycisk **OK**.

3. Trzecim krokiem jest Utwórz sondę kondycji:

   1. W witrynie Azure portal Otwórz usługę równoważenia obciążenia platformy Azure, wybierz **sond kondycji**i wybierz **Dodaj**.
   2. Wprowadź nazwę nowego sondy kondycji (na przykład **hp bazy danych Db2**).
   3. Wybierz **TCP** jako protokół i port **62500**. Zachowaj **interwał** wartość 5, a **próg złej kondycji** wartość równa 2.
   4. Kliknij przycisk **OK**.

4. Tworzenie reguł równoważenia obciążenia:

   1. W witrynie Azure portal, należy otworzyć usługi Azure Load Balancer, wybierz **reguły równoważenia obciążenia**i wybierz **Dodaj**.
   2. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład **SID bazy danych Db2**).
   3. Wybierz adres IP frontonu, puli zaplecza i sondy kondycji, który został utworzony wcześniej (na przykład **frontonu bazy danych Db2**).
   4. Zachowaj **protokołu** równa **TCP**i wprowadź numer portu *port komunikacyjny bazy danych*.
   5. Zwiększ **limitu czasu bezczynności** do 30 minut.
   6. Upewnij się, że **włączyć pływającego adresu IP**.
   7. Kliknij przycisk **OK**.


### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Wprowadzanie zmian do profilów SAP wirtualnego adresu IP na potrzeby połączenia
Warstwa aplikacji SAP musi używać wirtualnego adresu IP, które są zdefiniowane i skonfigurowany dla usługi Azure Load Balancer połączyć się z podstawowego wystąpienia konfiguracji HADR. Wymagane są następujące zmiany.

/sapmnt/\<SID >/profile/domyślne. PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>Instalowanie podstawowego i okno dialogowe aplikacji serwerów

Podczas instalowania podstawowym i serwerów aplikacji okna dialogowego konfiguracji bazy danych Db2 HADR, należy używać wirtualnego nazwy hosta, możesz pobrać konfiguracji. 

W przypadku, gdy wykonywana jest instalacji przed utworzeniem konfiguracji bazy danych Db2 HADR, musisz wprowadzić zmiany, zgodnie z opisem w poprzednim akapicie i następujący stosy języka Java SAP.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>Sprawdź systemy stosu ABAP + Java lub Java JDBC adresu URL

Aby sprawdzić lub zaktualizować adres URL JDBC, należy użyć narzędzia konfiguracji J2EE. Narzędzie konfiguracji J2EE to narzędzie graficzne, dlatego należy **X server** zainstalowane:
 
1. Zaloguj się na głównym serwerze aplikacji J2EE wystąpienia i wykonywania:
    <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>
2. W ramce po lewej stronie wybierz magazyn zabezpieczeń.
2. W prawej ramce wybierz kluczajdbc/pulę/ <SAPSID> /URL.
2. Zmień nazwę hosta w adresie URL JDBC nazwy hostów wirtualnych <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>
5. Wybierz pozycję Dodaj.
5. Aby zapisać zmiany, kliknij ikonę dysku, w lewym górnym rogu.
5. Zamknij narzędzie konfiguracji.
5. Ponownie uruchom wystąpienie języka Java.

## <a name="configuration-of-log-archiving-for-hadr-setup"></a>Konfiguracja dziennika archiwizowanie na potrzeby instalacji HADR
Można skonfigurować dziennika bazy danych Db2, archiwizowanie na potrzeby instalacji HADR, zaleca się skonfigurowanie podstawowego i wstrzymania bazy danych do mają możliwość pobierania automatycznego logowania z wszystkich lokalizacji archiwum dziennika. Zarówno podstawowego, jak i wstrzymania bazy danych musi mieć możliwość pobierania plików archiwum dziennika z wszystkie lokalizacje archiwum dziennika bazy danych albo jedną wystąpień może archiwizuj plików dzienników. 

Archiwizowanie dziennika jest realizowane wyłącznie przez podstawową bazę danych. Jeśli zmienisz HADR ról serwerów bazy danych lub jeśli wystąpi błąd, nowej podstawowej bazy danych jest odpowiedzialny za Archiwizowanie dziennika. Jeśli w innym dzienniku archiwum lokalizacje, dzienniki mogą być archiwizowane dwa razy, a w przypadku lokalnych lub zdalnych — wyrównywanie, trzeba ręcznie skopiować dzienników zarchiwizowanych ze starego serwera podstawowego do lokalizacji aktywnego dziennika z nowym serwerem podstawowym.

Firma Microsoft zaleca skonfigurowanie wspólnej udziału NFS, w których dzienniki są zapisywane z obu węzłów. Systemu plików NFS musi być o wysokiej dostępności. 

Możesz użyć istniejącego NFS o wysokiej dostępności, używany do transportu, katalogu profilu. Odczyt:

- [Wysoka dostępność systemu NFS na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server][nfs-ha] 
- [Wysoka dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server za pomocą usługi Azure Files NetApp dla aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) dotyczące sposobu używania [usługi Azure Files NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) do tworzenia udziałów NFS


## <a name="test-the-cluster-setup"></a>Testuj ustawienia klastra

W tej sekcji opisano, jak można przetestować konfigurację HADR bazy danych Db2. **Każdego testu przyjęto założenie, że zalogowano Cię jako użytkownik główny** i IBM Db2 podstawowy działa na **azibmdb01** maszyny wirtualnej.

Początkowy stan dla wszystkich przypadków testowych zostało wyjaśnione w tym miejscu: (crm_mon - r lub crm status)

- **Stan CRM** jest stanu program Pacemaker migawki w czasie wykonywania 
- **crm_mon - r** ciągłe dane wyjściowe stanu program Pacemaker

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   Promoting azibmdb01
     Slaves: [ azibmdb02 ]
</code></pre>

Oryginalny stan w systemie SAP jest udokumentowany w transakcji DBACOCKPIT--> konfiguracji--> Przegląd, takich jak:

![DBACockpit — sprzed migracji](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>Przejęcie test programu IBM Db2.


> [!IMPORTANT] 
> Przed rozpoczęciem testu, upewnij się, czy program Pacemaker nie ma żadnych akcji nie powiodło się (stan crm), istnieją nie ograniczenia lokalizacji (resztek testu migracji) i synchronizacji IBM Db2 HADR działa. Skontaktuj się z za pomocą użytkownika bazy danych db2\<sid > <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Migrowanie węzła, w którym podstawowej bazy danych Db2, wykonując następujące polecenie:
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

Po zakończeniu migracji, dane wyjściowe stanu crm wygląda następująco:
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]
</code></pre>

Oryginalny stan w systemie SAP jest udokumentowany w transakcji DBACOCKPIT--> konfiguracji--> Przegląd, takich jak: ![DBACockpit — po migracji](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

Proces migracji zasobów za pomocą "migrate zasobów crm" tworzy ograniczenia lokalizacji. Ograniczenia lokalizacji powinny być usuwane. Jeśli ograniczenia lokalizacji nie są usuwane, zasób nie może wykonać powrotu po awarii, lub może wystąpić niepożądane przejęcia. 

Migrowanie zasobów do **azibmdb01** i wyczyść ograniczenia lokalizacji
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>


- zasób CRM migracji < res_name > <host> — tworzy ograniczenia lokalizacji i przejęcia mogą powodować problemy
- Wyczyść zasobów CRM < res_name > - usuwa ograniczenia lokalizacji
- czyszczenie zasobów CRM < res_name > - czyści wszystkie błędy zasobu


### <a name="test-the-fencing-agent"></a>Test agent ogrodzenia

W tym przypadku przetestujemy interwencja preferowane, co jest zalecane do użycia z systemem SUSE Linux.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

Węzeł klastra **azibmdb01** powinien zostać przeprowadzony ponowny rozruch. Podstawową rolą HADR IBM Db2 zamierza można przenieść do **azibmdb02**. Gdy **azibmdb01** jest do trybu online, Db2, wystąpienie przechodzi do przenoszenia roli wystąpienia pomocniczej bazy danych. 

W przypadku usługi program Pacemaker nie uruchamia się automatycznie na podstawowym poprzedniego został uruchomiony ponownie upewnij się uruchomić je ręcznie przy użyciu:

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>Testowanie ręczne przejęcie

Możesz przetestować ręczne przejęcia przez zatrzymanie usługi program Pacemaker w **azibmdb01** węzła:
<pre><code>service pacemaker stop</code></pre>

Stan **azibmdb02**
<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]
</code></pre>

Po włączeniu trybu failover, możesz uruchomić usługę ponownie na **azibmdb01**.
<pre><code>service pacemaker start</code></pre>


### <a name="kill-db2-process-on-the-node-running-the-hadr-primary-database"></a>Kasowanie procesu bazy danych Db2 w węźle systemem HADR podstawowej bazy danych

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

Wystąpienie bazy danych Db2 zamierza się nie powieść i program Pacemaker informację o stanie po:

<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Slaves: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms


</code></pre>

Program pacemaker będzie ponowne uruchomienie wystąpienia podstawowej bazy danych Db2 na tym samym węźle lub tryb failover w węźle systemem pomocniczej bazy danych, wystąpienia i zwróci błąd.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms
</code></pre>


### <a name="kill-db2-process-on-node-that-runs-the-secondary-database-instance"></a>Kasowanie procesu bazy danych Db2 na węźle, który uruchamia wystąpienie pomocniczej bazy danych

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

Węzeł pobiera into nie powiodła się podane i zgłoszony błąd
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb02
     Masters: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>

Pobierz wystąpienia bazy danych Db2 ponownego uruchomienia dodatkowej rolę, którą miała ona przypisana przed

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>



### <a name="stop-db-via-db2stop-force-on-node-running-the-hadr-primary-database-instance"></a>Zatrzymaj bazy danych za pośrednictwem db2stop życie w węźle, na którym uruchomiono wystąpienie HADR podstawowej bazy danych

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Jako użytkownika bazy danych db2\<sid > wykonaj polecenie db2stop życie:
<pre><code>azibmdb01:~ # su - db2ptr
azibmdb01:db2ptr> db2stop force</code></pre>

Wykryto błąd
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb01
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=201, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:25 2019', queued=1ms, exec=150ms</code></pre>

Wystąpienie pomocniczej bazy danych Db2 HADR stało się promowane do roli podstawowej
<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_start_0 on azibmdb01 'unknown error' (1): call=205, stat
us=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:27 2019', queued=0ms, exec=865ms</pre></code>


### <a name="crash-vm-with-restart-on-node-running-the-hadr-primary-database-instance"></a>Awaria maszyny Wirtualnej z ponownym uruchomieniem komputera w węźle, na którym uruchomiono wystąpienie HADR podstawowej bazy danych

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Program pacemaker będzie promować pomocniczy wystąpienia do roli podstawowego wystąpienia. Stary podstawowe wystąpienie przejdzie w pełni rolę pomocniczej po maszyny Wirtualnej i wszystkich usług w pełni zostaną przywrócone po ponowny rozruch maszyny Wirtualnej:

<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>



### <a name="crash-the-vm-running-the-hadr-primary-database-instance-with-halt"></a>Awarii maszyny Wirtualnej, na którym uruchomiono wystąpienie HADR podstawowej bazy danych za pomocą "zatrzymania"

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

W takim przypadku program Pacemaker wykryje, że węzeł, na którym uruchomiono wystąpienie podstawowej bazy danych nie odpowiada.

<pre><code>2 nodes configured
5 resources configured

Node azibmdb01: UNCLEAN (online)
Online: [ azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

następnym krokiem jest sprawdzenie **Podziel mózg** sytuacji. Gdy działającego węzła jest się upewnić, że węzła, który ostatnio uruchomiono wystąpienie podstawowej bazy danych, nie działa, przejściu w tryb failover zasobów przechodzi do wykonania
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ] </code></pre>


W przypadku "Zatrzymywanie" węzła, węzeł nie powiodło się musi zostać uruchomiona ponownie za pomocą narzędzia do zarządzania platformy Azure (Portal, przepływach, codziennych,...). Nie powiodło się węzeł będzie można uruchomić wystąpienia bazy danych Db2 w pełni rolę pomocniczej, po powrocie do trybu online.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]</code></pre>

## <a name="next-steps"></a>Kolejne kroki
Zapoznaj się z dokumentacją tego:

- [Architektura wysokiej dostępności i scenariusze SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Konfigurowanie program Pacemaker w systemie SUSE Linux należy wprowadzić
- pri
- (SE serwera na platformie Azure]https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)

     

