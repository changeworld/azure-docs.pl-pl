---
title: Konfigurowanie systemu IBM Db2 HADR na maszynach wirtualnych platformy Azure | Dokumenty firmy Microsoft
description: Omiń wysoką dostępność środowiska IBM Db2 LUW na maszynach wirtualnych platformy Azure.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/06/2020
ms.author: juergent
ms.openlocfilehash: 614ac8b651224a3b6ec605a6bffd520449a1d793
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78926742"
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
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP4/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>Wysoka dostępność środowiska IBM Db2 LUW na maszynach wirtualnych platformy Azure na suse linux enterprise server z rozrusznikiem serca

IBM Db2 dla systemów Linux, UNIX i Windows (LUW) w [konfiguracji o wysokiej dostępności i odzyskiwania po awarii (HADR)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) składa się z jednego węzła, który uruchamia wystąpienie podstawowej bazy danych i co najmniej jednego węzła, w który działa pomocnicze wystąpienie bazy danych. Zmiany w wystąpieniu podstawowej bazy danych są replikowane do pomocniczego wystąpienia bazy danych synchronicznie lub asynchronicznie, w zależności od konfiguracji. 

W tym artykule opisano sposób wdrażania i konfigurowania maszyn wirtualnych platformy Azure (maszyn wirtualnych), instalowania struktury klastra i instalowania środowiska IBM Db2 LUW z konfiguracją HADR. 

W tym artykule nie opisano sposobu instalowania i konfigurowania urządzenia IBM Db2 LUW z instalacją oprogramowania HADR lub SAP. Aby ułatwić wykonanie tych zadań, dostarczamy odniesienia do podręczników instalacji SAP i IBM. W tym artykule koncentruje się na części, które są specyficzne dla środowiska platformy Azure. 

Obsługiwane wersje IBM Db2 to 10.5 i nowsze, zgodnie z dokumentem w notatce SAP [1928533].

Przed rozpoczęciem instalacji zapoznaj się z następującymi uwagami i dokumentacją sap:

| Uwaga SAP | Opis |
| --- | --- |
| [1928533] | Aplikacje SAP na platformie Azure: obsługiwane produkty i typy maszyn wirtualnych platformy Azure |
| [2015553] | SAP na platformie Azure: wymagania wstępne dotyczące pomocy technicznej |
| [2178632] | Kluczowe metryki monitorowania sap na platformie Azure |
| [2191498] | SAP na Linuksie z platformą Azure: Ulepszone monitorowanie |
| [2243692] | Maszyna wirtualna systemu Linux on Azure (IaaS): problemy z licencją SAP |
| [1984787] | SUSE LINUX Enterprise Server 12: Uwagi dotyczące instalacji |
| [1999351] | Rozwiązywanie problemów z rozszerzonym monitorowaniem platformy Azure dla systemu SAP |
| [2233094] | DB6: Aplikacje SAP na platformie Azure korzystające z ibm db2 dla systemów Linux, UNIX i Windows — dodatkowe informacje |
| [1612105] | DB6: CZĘSTO ZADAWANE PYTANIA na Db2 z HADR |


| Dokumentacja | 
| --- |
| [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Czy wszystkie wymagane notatki SAP dla Linuksa |
| [Przewodnik planowania i wdrażania maszyn wirtualnych platformy Azure dla systemu SAP w systemie Linux][planning-guide] |
| [Wdrożenie maszyn wirtualnych platformy Azure dla systemu SAP w systemie Linux][deployment-guide] (w tym artykule) |
| [Wdrożenie systemu zarządzania bazami danych (DBMS) platformy Azure dla systemu SAP w][dbms-guide] systemie Linux — przewodnik |
| [Obciążenie SAP na liście kontrolnej planowania i wdrażania platformy Azure][azr-sap-plancheck] |
| [SUSE Linux Enterprise Server for SAP Applications 12 SP4 — najważniejsze wskazówki — przewodniki po najlepszych praktykach][sles-for-sap-bp] |
| [Rozszerzenie SUSE Linux Enterprise o wysokiej dostępności 12 SP4][sles-ha-guide] |
| [Wdrażanie systemu DBMS usługi Azure Virtual Machines programu IBM Db2 dla obciążenia SAP][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR R 10.5][db2-hadr-10.5] |

## <a name="overview"></a>Omówienie
Aby osiągnąć wysoką dostępność, moduł LUW IBM Db2 z usługą HADR jest instalowany na co najmniej dwóch maszynach wirtualnych platformy Azure, które są wdrażane w [zestawie dostępności platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) lub w [strefach dostępności platformy Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones) 

Na poniższej grafice jest wyświetlana konfiguracja dwóch maszyn wirtualnych platformy Azure na serwerze bazy danych. Oba serwery bazy danych Platformy Azure maszyny wirtualne mają własny magazyn podłączony i są uruchomione. W hadr jedno wystąpienie bazy danych w jednym z maszyn wirtualnych platformy Azure ma rolę wystąpienia podstawowego. Wszyscy klienci są połączeni z tym wystąpieniem podstawowym. Wszystkie zmiany w transakcjach bazy danych są utrwalone lokalnie w dzienniku transakcji db2. Ponieważ rekordy dziennika transakcji są zachowywane lokalnie, rekordy są przesyłane za pośrednictwem protokołu TCP/IP do wystąpienia bazy danych na drugim serwerze bazy danych, na serwerze rezerwowym lub wystąpieniu rezerwowym. Wystąpienie rezerwowe aktualizuje lokalną bazę danych, przewijając rekordy dziennika przeniesionych transakcji. W ten sposób serwer rezerwowy jest synchronizowany z serwerem podstawowym.

HADR jest tylko funkcją replikacji. Nie ma wykrywania awarii i nie ma automatycznego przejęcia lub pracy awaryjnej. Przejęcie lub przeniesienie na serwer rezerwowy musi być zainicjowane ręcznie przez administratora bazy danych. Aby uzyskać automatyczne wykrywanie przejęcia i awarii, można użyć funkcji klastrowania szesnastego linuksa. Rozrusznik serca monitoruje dwa wystąpienia serwera bazy danych. Gdy wystąpienie serwera podstawowej bazy danych ulega awarii, program Pacemaker inicjuje *automatyczne* przejęcie hadr przez serwer rezerwowy. Rozrusznik zapewnia również, że wirtualny adres IP jest przypisany do nowego serwera podstawowego.

![Omówienie wysokiej dostępności programu IBM Db2](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

Aby serwery aplikacji SAP łączyły się z podstawową bazą danych, potrzebna jest nazwa hosta wirtualnego i wirtualny adres IP. W przypadku pracy awaryjnej serwery aplikacji SAP będą łączyć się z nowym wystąpieniem podstawowej bazy danych. W środowisku platformy Azure [moduł równoważenia obciążenia platformy Azure](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) jest wymagany do używania wirtualnego adresu IP w sposób wymagany dla usługi HADR ibm db2. 

Aby pomóc Ci w pełni zrozumieć, w jaki sposób ibm Db2 LUW z HADR i pacemaker pasuje do konfiguracji systemu SAP o wysokiej dostępności, poniższy obraz przedstawia przegląd wysoce dostępnej konfiguracji systemu SAP opartego na bazie danych IBM Db2. Ten artykuł dotyczy tylko ibm Db2, ale zawiera odniesienia do innych artykułów na temat konfigurowania innych składników systemu SAP.

![Ibm DB2 omówienie pełnego środowiska o wysokiej dostępności](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-the-required-steps"></a>Przegląd wysokiego poziomu wymaganych kroków
Aby wdrożyć konfigurację IBM Db2, należy wykonać następujące kroki:

  + Zaplanuj swoje środowisko.
  + Wdrażanie maszyn wirtualnych.
  + Aktualizuj SUSE Linux i skonfiguruj systemy plików.
  + Zainstaluj i skonfiguruj rozrusznik serca.
  + Zainstaluj [wysoce dostępne pliki NFS][nfs-ha].
  + Zainstaluj [ASCS/ERS w oddzielnym klastrze][ascs-ha].
  + Zainstaluj bazę danych IBM Db2 z opcją Rozproszona/Wysoka dostępność (SWPM).
  + Zainstaluj i utwórz pomocniczy węzeł bazy danych i wystąpienie oraz skonfiguruj usługę HADR.
  + Potwierdź, że HADR działa.
  + Zastosuj konfigurację rozrusznika serca, aby sterować ibm db2.
  + Konfigurowanie modułu równoważenia obciążenia platformy Azure.
  + Instalowanie serwerów aplikacji podstawowych i dialogowych.
  + Sprawdź i dostosuj konfigurację serwerów aplikacji SAP.
  + Wykonywanie testów pracy awaryjnej i przejęcia.



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Planowanie infrastruktury platformy Azure do obsługi infrastruktury IBM Db2 LUW z hadr

Przed wykonaniem wdrożenia należy ukończyć proces planowania. Planowanie tworzy podstawę do wdrażania konfiguracji Db2 z HADR na platformie Azure. Kluczowe elementy, które muszą być częścią planowania identyfikatora IMB Db2 LUW (część bazy danych środowiska SAP) są wymienione w poniższej tabeli:

| Temat | Krótki opis |
| --- | --- |
| Definiowanie grup zasobów platformy Azure | Grupy zasobów, w których można wdrożyć maszynę wirtualną, wirtualną, moduł równoważenia obciążenia platformy Azure i inne zasoby. Może istnieć lub być nowy. |
| Definicja sieci wirtualnej / podsieci | Gdzie są wdrażane maszyny wirtualne dla ibm db2 i Azure Load Balancer. Może istnieć lub zostać utworzony. |
| Maszyny wirtualne obsługujące urządzenia IBM Db2 LUW | Rozmiar maszyny Wirtualnej, magazyn, sieć, adres IP. |
| Nazwa wirtualnego hosta i wirtualny adres IP bazy danych IBM Db2| Wirtualna nazwa ip lub hosta używana do połączenia serwerów aplikacji SAP. **db-virt-hostname**, **db-virt-ip**. |
| Ogrodzenie azure | Ogrodzenia azure lub ogrodzenia SBD (wysoce zalecane). Metoda, aby uniknąć sytuacji podzielonego mózgu. |
| Maszyna wirtualna SBD | Rozmiar maszyny wirtualnej SBD, magazyn, sieć. |
| Azure Load Balancer | Użycie standardowego lub standardowego (zalecanego), portu sondy dla bazy danych Db2 (nasza rekomendacja 62500) **probe-port**. |
| Rozpoznawanie nazw| Jak działa rozpoznawanie nazw w środowisku. Usługa DNS jest wysoce zalecana. Można użyć pliku hostów lokalnych. |
    
Aby uzyskać więcej informacji na temat rozrusznika systemu Linux na platformie Azure, zobacz [Konfigurowanie rozrusznika serca na suse Linux Enterprise Server na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker).

## <a name="deployment-on-suse-linux"></a>Wdrożenie na SUSE Linux

Agent zasobów dla środowiska IBM Db2 LUW znajduje się w aplikacji SUSE Linux Enterprise Server for SAP Applications. W przypadku konfiguracji opisanej w tym dokumencie należy użyć programu SUSE Linux Server for SAP Applications. Portal Azure Marketplace zawiera obraz dla aplikacji SUSE Enterprise Server for SAP Applications 12, którego można użyć do wdrożenia nowych maszyn wirtualnych platformy Azure. Należy pamiętać o różnych modeli pomocy technicznej lub usług, które są oferowane przez SUSE za pośrednictwem portalu Azure Marketplace po wybraniu obrazu maszyny Wirtualnej w portalu Azure VM Marketplace. 

### <a name="hosts-dns-updates"></a>Hosty: aktualizacje DNS
Należy tworzyć listę wszystkich nazw hostów, w tym nazwy hostów wirtualnych, i aktualizować serwery DNS, aby umożliwić prawidłowe rozpoznawanie nazw adresów IP. Jeśli serwer DNS nie istnieje lub nie można zaktualizować i utworzyć wpisów DNS, należy użyć lokalnych plików hosta poszczególnych maszyn wirtualnych, które uczestniczą w tym scenariuszu. Jeśli używasz wpisów plików hosta, upewnij się, że wpisy są stosowane do wszystkich maszyn wirtualnych w środowisku systemowym SAP. Zaleca się jednak użycie systemu DNS, który najlepiej rozszerzy się na platformę Azure


### <a name="manual-deployment"></a>Wdrożenie ręczne

Upewnij się, że wybrany system operacyjny jest obsługiwany przez IBM/SAP dla środowiska IBM Db2 LUW. Lista obsługiwanych wersji systemu operacyjnego dla maszyn wirtualnych platformy Azure i wersji Db2 jest dostępna w komunikacie SAP [Note 1928533]. Lista wydań systemu operacyjnego według poszczególnych wersji Db2 jest dostępna w macierzy dostępności produktów SAP. Firma Microsoft zdecydowanie zaleca co najmniej SLES 12 SP4 ze względu na ulepszenia wydajności związane z platformą Azure w tej lub nowszej wersji systemu SUSE Linux.

1. Tworzenie lub wybieranie grupy zasobów.
1. Utwórz lub wybierz sieć wirtualną i podsieć.
1. Utwórz zestaw dostępności platformy Azure lub wdrożyć strefę dostępności.
    + Dla zestawu dostępności ustaw maksymalną liczbę domen aktualizacji na 2.
1. Utwórz maszynę wirtualną 1.
    + Użyj SLES dla obrazu SAP w portalu Azure Marketplace.
    + Wybierz zestaw dostępności platformy Azure utworzony w kroku 3 lub wybierz strefę dostępności.
1.  Utwórz maszynę wirtualną 2.
    + Użyj SLES dla obrazu SAP w portalu Azure Marketplace.
    + Wybierz ustawioną dostępność platformy Azure w utworzonym w kroku 3 lub wybierz strefę dostępności (nie tę samą strefę, co w kroku 3).
1. Dodaj dyski danych do maszyn wirtualnych, a następnie sprawdź zalecenie konfiguracji systemu plików w artykule [Wdrożenie DBMS maszyn wirtualnych IBM Db2 Azure dla obciążenia SAP][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>Tworzenie klastra rozrusznika serca
    
Aby utworzyć podstawowy klaster rozrusznika dla tego serwera IBM Db2, zobacz [Konfigurowanie rozrusznika serca na serwerze SUSE Linux Enterprise Server na platformie Azure][sles-pacemaker]. 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>Instalowanie środowiska IBM Db2 LUW i SAP

Przed rozpoczęciem instalacji środowiska SAP opartego na urządzeniach LUW IBM Db2 należy zapoznać się z następującą dokumentacją:

+ Dokumentacja platformy Azure
+ Dokumentacja SAP
+ Dokumentacja IBM

Łącza do tej dokumentacji znajdują się w sekcji wprowadzającej tego artykułu.

Zapoznaj się z podręcznikami instalacji SAP dotyczącymi instalowania aplikacji opartych na programie NetWeaver w systemie IBM Db2 LUW.

Przewodniki można znaleźć w portalu pomocy SAP za pomocą programu [SAP Installation Guide Finder][sap-instfind].

Można zmniejszyć liczbę prowadnic wyświetlanych w portalu, ustawiając następujące filtry:

- Chcę: "Zainstaluj nowy system"
- Moja baza danych: "IBM Db2 dla Systemów Linux, Unix i Windows"
- Dodatkowe filtry dla wersji SAP NetWeaver, konfiguracji stosu lub systemu operacyjnego

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Wskazówki dotyczące instalacji do konfigurowania urządzenia IBM Db2 LUW z hadr

Aby skonfigurować podstawowe wystąpienie bazy danych IBM Db2 LUW:

- Użyj opcji wysokiej dostępności lub rozproszonej.
- Zainstaluj wystąpienie SAP ASCS/ERS i Database.
- Wykonaj kopię zapasową nowo zainstalowanej bazy danych.


> [!IMPORTANT] 
> Zapisz "Port komunikacji bazy danych", który jest ustawiony podczas instalacji. Musi to być ten sam numer portu dla obu wystąpień bazy danych

Aby skonfigurować serwer bazy danych w trybie gotowości przy użyciu jednorodnej procedury kopiowania systemu SAP, wykonaj następujące kroki:

1. Wybierz opcję **Kopiowanie systemu** > **wystąpienie systemy** > **docelowe Rozproszona** > **baza danych**.
1. Jako metodę kopiowania wybierz **jednorodny system,** aby można było użyć kopii zapasowej do przywrócenia kopii zapasowej w wystąpieniu serwera rezerwowego.
1. Po osiągnięciu kroku zakończenia, aby przywrócić bazę danych dla jednorodnej kopii systemu, zamknij instalatora. Przywracanie bazy danych z kopii zapasowej hosta podstawowego. Wszystkie kolejne fazy instalacji zostały już wykonane na serwerze podstawowej bazy danych.
1. Konfigurowanie usługi HADR dla programu IBM Db2.

   > [!NOTE]
   > W przypadku instalacji i konfiguracji specyficznej dla platformy Azure i rozrusznika serca: podczas procedury instalacji za pośrednictwem menedżera aprowizacji oprogramowania SAP pojawia się wyraźne pytanie dotyczące wysokiej dostępności środowiska IBM Db2 LUW:
   >+ Nie należy wybierać **ibm Db2 pureScale**.
   >+ Nie należy **wybierać opcji Zainstaluj ibm Tivoli System Automation for Multiplatforms**.
   >+ Nie należy wybierać opcji **Generuj pliki konfiguracyjne klastra**.

   W przypadku korzystania z urządzenia SBD dla producenta serca systemu Linux należy ustawić następujące parametry Db2 HADR:
   + Czas trwania okna równorzędnego HADR (w sekundach) (HADR_PEER_WINDOW) = 300  
   + Wartość limitu czasu HADR (HADR_TIMEOUT) = 60

   Korzystając z agenta szermierki azure pacemaker, należy ustawić następujące parametry:
   + Czas trwania okna równorzędnego HADR (w sekundach) (HADR_PEER_WINDOW) = 900  
   + Wartość limitu czasu HADR (HADR_TIMEOUT) = 60

Zaleca się poprzednie parametry na podstawie początkowych testów pracy awaryjnej/przejęcia. Jest to obowiązkowe, aby przetestować dla prawidłowej funkcjonalności pracy awaryjnej i przejęcia z tych ustawień parametrów. Ponieważ poszczególne konfiguracje mogą się różnić, parametry mogą wymagać dostosowania. 

> [!IMPORTANT]
> Specyficzne dla ibm Db2 z konfiguracją HADR przy normalnym starcie: Wystąpienie pomocniczej lub rezerwowej bazy danych musi być uruchomione przed uruchomieniem wystąpienia podstawowej bazy danych.

W celach demonstracyjnych i procedur opisanych w tym artykule, identyfikator SID bazy danych to **PTR**.

#### <a name="ibm-db2-hadr-check"></a>Ibm Db2 HADR sprawdzić
Po skonfigurowaniu programu HADR i stanie: PEER i CONNECTED w węzłach podstawowych i wstrzymanych wykonaj następujące czynności:

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



## <a name="db2-pacemaker-configuration"></a>Konfiguracja rozrusznika Db2

Korzystając z programu Pacemaker do automatycznego pracy awaryjnej w przypadku awarii węzła, należy odpowiednio skonfigurować wystąpienia Db2 i rozrusznik serca. W tej sekcji opisano ten typ konfiguracji.

Następujące elementy są poprzedzone albo:

- **[A]**: Dotyczy wszystkich węzłów
- **[1]**: Dotyczy tylko węzła 1 
- **[2]**: Dotyczy tylko węzła 2

**[A]** Wymagania wstępne dotyczące konfiguracji rozrusznika serca:
1. Zamknij oba serwery bazy danych\<z użytkownikiem db2 sid> z db2stop.
1. Zmień środowisko powłoki dla\<użytkownika db2 sid> na */bin/ksh*. Zalecamy użycie narzędzia Yast. 


### <a name="pacemaker-configuration"></a>Konfiguracja rozrusznika serca

> [!IMPORTANT]
> Ostatnie testy ujawniły sytuacje, w których netcat przestaje odpowiadać na żądania z powodu zaległości i ograniczenia obsługi tylko jednego połączenia. Zasób netcat przestaje nasłuchiwać żądań modułu równoważenia obciążenia platformy Azure, a zmienny adres IP staje się niedostępny.  
> W przypadku istniejących klastrów rozruszników zalecamy w przeszłości zastąpienie netcata socatem. Obecnie zalecamy użycie agenta zasobów azure-lb, który jest częścią agentów zasobów pakietu, z następującymi wymaganiami dotyczącymi wersji pakietu:
> - W przypadku SLES 12 SP4/SP5 wersja musi być co najmniej resource-agents-4.3.018.a7fb5035-3.30.1.  
> - Dla SLES 15/15 SP1 wersja musi być co najmniej resource-agents-4.3.0184.6ee15eb2-4.13.1.  
>
> Należy pamiętać, że zmiana będzie wymagać krótkiego przestoju.  
> W przypadku istniejących klastrów stymulatora, jeśli konfiguracja została już zmieniona, aby używać socat zgodnie z opisem w [umacnianiu wykrywania równoważenia obciążenia azure,](https://www.suse.com/support/kb/doc/?id=7024128)nie ma wymogu natychmiastowego przejścia na agenta zasobów azure-lb.

**[1]** Konfiguracja rozrusznika serca ibm Db2 HADR:
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]** Tworzenie zasobów IBM Db2:
<pre><code># Replace **bold strings** with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.

sudo crm configure primitive rsc_Db2_db2ptr_<b>PTR</b> db2 \
        params instance="<b>db2ptr</b>" dblist="<b>PTR</b>" \
        op start interval="0" timeout="130" \
        op stop interval="0" timeout="120" \
        op promote interval="0" timeout="120" \
        op demote interval="0" timeout="120" \
        op monitor interval="30" timeout="60" \
        op monitor interval="31" role="Master" timeout="60"

# Configure virtual IP - same as Azure Load Balancer IP
sudo crm configure primitive rsc_ip_db2ptr_<b>PTR</b> IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="<b>10.100.0.10</b>"

# Configure probe port for Azure load Balancer
sudo crm configure primitive rsc_nc_db2ptr_<b>PTR</b> azure-lb port=<b>62500</b>

sudo crm configure group g_ip_db2ptr_<b>PTR</b> rsc_ip_db2ptr_<b>PTR</b> rsc_nc_db2ptr_<b>PTR</b>

sudo crm configure ms msl_Db2_db2ptr_<b>PTR</b> rsc_Db2_db2ptr_<b>PTR</b> \
        meta target-role="Started" notify="true"

sudo crm configure colocation col_db2_db2ptr_<b>PTR</b> inf: g_ip_db2ptr_<b>PTR</b>:Started msl_Db2_db2ptr_<b>PTR</b>:Master

sudo crm configure order ord_db2_ip_db2ptr_<b>PTR</b> inf: msl_Db2_db2ptr_<b>PTR</b>:promote g_ip_db2ptr_<b>PTR</b>:start

sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

**[1]** Uruchamianie zasobów IBM Db2:
* Wyjmij rozrusznik serca z trybu konserwacji.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]** Upewnij się, że stan klastra jest OK i że wszystkie zasoby są uruchamiane. Nie jest ważne, który węzeł zasoby są uruchomione na.
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>2 skonfigurowane węzły
# <a name="5-resources-configured"></a>5 skonfigurowanych zasobów

# <a name="online--azibmdb01-azibmdb02-"></a>Online: [ azibmdb01 azibmdb02 ]

# <a name="full-list-of-resources"></a>Pełna lista zasobów:

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>stonith-sbd (stonith:external/sbd): Rozpoczęto azibmdb002
#  <a name="resource-group-g_ip_db2ptr_ptr"></a>Grupa zasobów: g_ip_db2ptr_PTR
#      <a name="rsc_ip_db2ptr_ptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR (ocf::heartbeat:IPaddr2): Rozpoczęto azibmdb02
#      <a name="rsc_nc_db2ptr_ptr--ocfheartbeatazure-lb------started-azibmdb02"></a>rsc_nc_db2ptr_PTR (ocf::heartbeat:azure-lb): Rozpoczęto azibmdb00
#  <a name="masterslave-set-msl_db2_db2ptr_ptr-rsc_db2_db2ptr_ptr"></a>Zestaw master/slave: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>Mistrzowie: [ azibmdb02 ]
#      <a name="slaves--azibmdb01-"></a>Niewolnicy: [ azibmdb01 ]
</pre>

> [!IMPORTANT]
> Za pomocą narzędzi Rozrusznika należy zarządzać wystąpieniem klastrowanego programu Db2 z klastrowanym programem Pacemaker. Jeśli używasz db2 poleceń, takich jak db2stop, Pacemaker wykrywa akcję jako błąd zasobu. Jeśli wykonujesz konserwację, możesz umieścić węzły lub zasoby w trybie konserwacji. Rozrusznik serca zawiesza monitorowanie zasobów, a następnie można użyć normalnych poleceń administracyjnych db2.


### <a name="configure-azure-load-balancer"></a>Konfigurowanie modułu Azure Load Balancer
Aby skonfigurować moduł równoważenia obciążenia platformy Azure, zaleca się użycie jednostki [SKU równoważenia obciążenia standardowego platformy Azure,](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) a następnie wykonanie następujących czynności;

> [!NOTE]
> Jednostka SKU standardowego modułu równoważenia obciążenia ma ograniczenia dostępu do publicznych adresów IP z węzłów pod modułem równoważenia obciążenia. Artykuł [Łączność publicznych punktów końcowych dla maszyn wirtualnych przy użyciu standardowego modułu równoważenia obciążenia platformy Azure w scenariuszach wysokiej dostępności SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections) opisuje sposoby włączania tych węzłów w dostęp do publicznych adresów IP

1. Tworzenie puli adresów IP front-end:

   a. W witrynie Azure portal otwórz moduł równoważenia obciążenia platformy Azure, wybierz **pulę adresów IP frontu,** a następnie wybierz pozycję **Dodaj**.

   b. Wprowadź nazwę nowej puli adresów IP front-endu (na przykład **Db2-connection**).

   d. Ustaw **przypisanie** na **Statyczne**i wprowadź adres IP **Virtual-IP** zdefiniowany na początku.

   d. Kliknij przycisk **OK**.

   e. Po utworzeniu nowej puli adresów IP front-end należy zwrócić uwagę na adres IP puli.

1. Tworzenie puli zaplecza:

   a. W witrynie Azure portal otwórz moduł równoważenia obciążenia platformy Azure, wybierz **pule zaplecza,** a następnie wybierz pozycję **Dodaj**.

   b. Wprowadź nazwę nowej puli zaplecza (na przykład **Db2-backend).**

   d. Wybierz **pozycję Dodaj maszynę wirtualną**.

   d. Wybierz zestaw dostępności lub maszyny wirtualne obsługujące bazę danych IBM Db2 utworzone w poprzednim kroku.

   e. Wybierz maszyny wirtualne klastra IBM Db2.

   f. Kliknij przycisk **OK**.

1. Utwórz sondę kondycji:

   a. W witrynie Azure portal otwórz moduł równoważenia obciążenia platformy Azure, wybierz **sondy kondycji**i wybierz pozycję **Dodaj**.

   b. Wprowadź nazwę nowej sondy kondycji (na przykład **Db2-HP**).

   d. Wybierz **TCP** jako protokół i port **62500**. Zachowaj wartość **interwału** ustawioną na **5**i zachowaj wartość **progową złej** kondycji ustawioną na **2**.

   d. Kliknij przycisk **OK**.

1. Tworzenie reguł równoważenia obciążenia:

   a. W witrynie Azure portal otwórz moduł równoważenia obciążenia platformy Azure, wybierz pozycję **Reguły równoważenia obciążenia,** a następnie wybierz pozycję **Dodaj**.

   b. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład **Db2-SID**).

   d. Wybierz adres IP front-endu, pulę zaplecza i sondę kondycji utworzoną wcześniej (na przykład **Db2-frontend).**

   d. Zachowaj **protokół** ustawiony na **TCP**i wprowadź *port Database Communication port*.

   e. Wydłuż **limit czasu bezczynnego** do 30 minut.

   f. Upewnij się, że **włączysz pływający adres IP**.

   g. Kliknij przycisk **OK**.


### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Wprowadzanie zmian w profilach SAP w celu używania wirtualnego adresu IP do połączenia
Aby połączyć się z podstawowym wystąpieniem konfiguracji HADR, warstwa aplikacji SAP musi używać wirtualnego adresu IP zdefiniowanego i skonfigurowanego dla modułu Azure Load Balancer. Wymagane są następujące zmiany:

/sapmnt/\<SID>/profile/DEFAULT. Pfl
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>Instalowanie serwerów aplikacji podstawowych i dialogowych

Podczas instalowania serwerów aplikacji podstawowych i dialogowych względem konfiguracji Db2 HADR należy użyć nazwy hosta wirtualnego wybranej dla tej konfiguracji. 

Jeśli instalacja została wykonana przed utworzeniem konfiguracji Db2 HADR, należy wprowadzić zmiany zgodnie z opisem w poprzedniej sekcji i w następujący sposób dla stosów SAP Java.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>ABAP + Java lub Java stos systemów JDBC url sprawdzić

Użyj narzędzia J2EE Config, aby sprawdzić lub zaktualizować adres URL JDBC. Ponieważ narzędzie J2EE Config jest narzędziem graficznym, musisz mieć zainstalowany serwer X:
 
1. Zaloguj się do podstawowego serwera aplikacji wystąpienia J2EE i wykonaj:`sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh`
1. W lewej ramce wybierz **pozycję Magazyn zabezpieczeń**.
1. W prawej ramce wybierz klucz jdbc/pool/\<SAPSID>/url.
1. Zmień nazwę hosta w adresie URL JDBC na nazwę hosta wirtualnego.
     `jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0`
1. Wybierz pozycję **Dodaj**.
1. Aby zapisać zmiany, wybierz ikonę dysku w lewym górnym rogu.
1. Zamknij narzędzie konfiguracyjne.
1. Uruchom ponownie wystąpienie języka Java.

## <a name="configure-log-archiving-for-hadr-setup"></a>Konfigurowanie archiwizacji dzienników dla konfiguracji hadra
Aby skonfigurować archiwizację dziennika db2 dla konfiguracji HADR, zaleca się skonfigurowanie zarówno podstawowej, jak i rezerwowej bazy danych tak, aby miała możliwość automatycznego pobierania dziennika ze wszystkich lokalizacji archiwum dzienników. Zarówno podstawowa, jak i rezerwowana baza danych musi mieć możliwość pobierania plików archiwum dziennika ze wszystkich lokalizacji archiwum dziennika, do których jedno z wystąpień bazy danych może archiwizować pliki dziennika. 

Archiwizacja dziennika jest wykonywana tylko przez podstawową bazę danych. Jeśli zmienisz role HADR serwerów bazy danych lub wystąpi błąd, nowa podstawowa baza danych jest odpowiedzialna za archiwizację dziennika. Jeśli masz skonfigurowane wiele lokalizacji archiwum dzienników, dzienniki mogą być archiwizowane dwukrotnie. W przypadku lokalnego lub zdalnego nadrabiania zaległości może być również konieczności ręcznego skopiowania zarchiwizowanych dzienników ze starego serwera podstawowego do aktywnej lokalizacji dziennika nowego serwera podstawowego.

Zaleca się skonfigurowanie wspólnego udziału NFS, w którym dzienniki są zapisywane z obu węzłów. Udział NFS musi być wysoce dostępny. 

Można użyć istniejących udziałów nfs o wysokiej dostępności dla transportów lub katalogu profilu. Aby uzyskać więcej informacji, zobacz:

- [Wysoka dostępność systemu plików NFS na maszynach wirtualnych platformy Azure na serwerze SUSE Linux Enterprise Server][nfs-ha] 
- [Wysoka dostępność sap NetWeaver na maszynach wirtualnych platformy Azure na serwerze SUSE Linux Enterprise Server z plikami NetApp azure dla aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
- [Usługi Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) (do tworzenia udziałów NFS)


## <a name="test-the-cluster-setup"></a>Testowanie konfiguracji klastra

W tej sekcji opisano, jak można przetestować konfigurację Db2 HADR. *Każdy test zakłada, że użytkownik jest zalogowany jako katalog główny użytkownika,* a podstawowy system IBM Db2 jest uruchomiony na maszynie wirtualnej *azibmdb01.*

Stan początkowy dla wszystkich przypadków testowych jest wyjaśniony tutaj: (crm_mon -r lub crm)

- **status crm** jest migawką stanu rozrusznika w czasie wykonywania 
- **crm_mon -r** jest ciągłą produkcją stanu rozrusznika serca

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   Promoting azibmdb01
     Slaves: [ azibmdb02 ]
</code></pre>

Oryginalny stan w systemie SAP jest udokumentowany w przeglądzie > konfiguracji > transaction DBACOCKPIT, jak pokazano na poniższej ilustracji:

![DBACockpit - Przed migracją](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>Testowe przejęcie IBM Db2


> [!IMPORTANT] 
> Przed rozpoczęciem testu upewnij się, że:
> * Rozrusznik serca nie ma żadnych nieudanych akcji (stan crm).
> * Brak ograniczeń lokalizacji (resztki testu migracji)
> * Synchronizacja IBM Db2 HADR działa. Skontaktuj się z\<użytkownikiem db2 sid> <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Migrowanie węzła z podstawową bazą danych Db2 przez wykonanie następującego polecenia:
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

Po zakończeniu migracji dane wyjściowe stanu crm wyglądają następująco:
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]
</code></pre>

Oryginalny stan w systemie SAP jest udokumentowany w przeglądzie > konfiguracji > transaction DBACOCKPIT, jak pokazano na poniższej ilustracji:

![DBACockpit - Po migracji](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

Migracja zasobów z "migracją zasobów crm" tworzy ograniczenia lokalizacji. Ograniczenia lokalizacji powinny zostać usunięte. Jeśli ograniczenia lokalizacji nie zostaną usunięte, zasób nie może wrócić awaryjnie lub mogą wystąpić niechciane przejęcia. 

Migrowanie zasobu z powrotem do *azibmdb01* i wyczyszczenie ograniczeń lokalizacji
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>

- **migracja \<zasobów crm res_name \<>> hosta:** Tworzy ograniczenia lokalizacji i może powodować problemy z przejęciem
- **crm resource \<clear>res_name **: Czyści ograniczenia lokalizacji
- **oczyszczanie zasobów \<crm res_name>**: Usuwa wszystkie błędy zasobu

### <a name="test-the-fencing-agent"></a>Przetestuj środek ogrodzeniowy

W takim przypadku testujemy ogrodzenia SBD, które zalecamy, aby zrobić, gdy używasz SUSE Linux.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

Węzeł *klastra azibmdb01* powinien zostać ponownie uruchomiony. IBM Db2 podstawowej roli HADR zostanie przeniesiony do *azibmdb02*. Gdy *azibmdb01* jest z powrotem w trybie online, Db2 wystąpienie będzie przenieść w roli wystąpienia pomocniczej bazy danych. 

Jeśli usługa Rozrusznik nie uruchamia się automatycznie po ponownym uruchomieniu dawnej podstawowej, należy uruchomić ją ręcznie z:

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>Przetestuj ręczne przejęcie

Można przetestować ręczne przejęcie, zatrzymując usługę rozrusznika w węźle *azibmdb01:*
<pre><code>service pacemaker stop</code></pre>

stan na *azibmdb02*
<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]
</code></pre>

Po pracy awaryjnej można ponownie uruchomić usługę na *azibmdb01*.
<pre><code>service pacemaker start</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Zabij proces Db2 w węźle, w który działa podstawowa baza danych HADR

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

Wystąpienie Db2 zakończy się niepowodzeniem, a rozrusznik serca zgłosi następujący stan:

<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Slaves: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms

</code></pre>

Rozrusznik serca uruchomi ponownie wystąpienie podstawowej bazy danych Db2 w tym samym węźle lub przejdzie w stan fail over do węzła, w który jest uruchomiony pomocnicze wystąpienie bazy danych i zgłaszany jest błąd.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms
</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Zabij proces Db2 w węźle, który uruchamia wystąpienie pomocniczej bazy danych

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

Węzeł dostaje się do nie powiodło się i zgłoszono błąd
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb02
     Masters: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>

Wystąpienie Db2 zostanie ponownie uruchomione w roli pomocniczej, którą wcześniej przypisała.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>



### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Zatrzymaj bazę danych za pomocą db2stop force w węźle, który uruchamia wystąpienie podstawowej bazy danych HADR

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Jako użytkownik db2\<sid> wykonać polecenie db2stop force:
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
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb01
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=201, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:25 2019', queued=1ms, exec=150ms</code></pre>

Pomocnicza baza danych Db2 HADR została awansowana do roli podstawowej
<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_start_0 on azibmdb01 'unknown error' (1): call=205, stat
us=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:27 2019', queued=0ms, exec=865ms</pre></code>


### <a name="crash-vm-with-restart-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Awaria maszyny Wirtualnej z ponownym uruchomieniem w węźle, w który uruchamia wystąpienie podstawowej bazy danych HADR

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Rozrusznik będzie promować wystąpienie pomocnicze do roli wystąpienia podstawowego. Stare wystąpienie podstawowe zostanie przeniesione do roli pomocniczej po maszynie wirtualnej i wszystkie usługi są w pełni przywrócone po ponownym uruchomieniu maszyny Wirtualnej:

<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>



### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Awaria maszyny Wirtualnej, która uruchamia wystąpienie podstawowej bazy danych HADR z "halt"

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

W takim przypadku program Pacemaker wykryje, że węzeł, w który jest uruchomiony wystąpienie podstawowej bazy danych nie odpowiada.

<pre><code>2 nodes configured
5 resources configured

Node azibmdb01: UNCLEAN (online)
Online: [ azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Następnym krokiem jest sprawdzenie sytuacji *w mózgu Split.* Po węźle zachowane ustali, że węzeł, który ostatnio uruchomiono wystąpienie podstawowej bazy danych jest w dół, pracy awaryjnej zasobów jest wykonywana.
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ] </code></pre>


W przypadku "zatrzymania" węzła węzeł nie powiodło się węzeł musi zostać ponownie uruchomiony za pomocą narzędzi usługi Azure Management (w witrynie Azure portal, PowerShell lub interfejsu wiersza polecenia platformy Azure). Po awarii węzła jest z powrotem w trybie online, uruchamia wystąpienie Db2 do roli pomocniczej.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]</code></pre>

## <a name="next-steps"></a>Następne kroki
- [Architektura i scenariusze wysokiej dostępności dla SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Konfigurowanie rozrusznika serca na serwerze SUSE Linux Enterprise Server na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)

