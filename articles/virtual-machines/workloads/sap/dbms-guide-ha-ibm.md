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
ms.openlocfilehash: a74dd1a932cac41081786f76938a5b35de62d878
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64689710"
---
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2233094]: https://launchpad.support.sap.com/#/notes/2233094
[1612105]: https://launchpad.support.sap.com/#/notes/1612105

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

IBM Db2 dla systemów Linux, UNIX i Windows (LUW) w [wysoką dostępność i odzyskiwanie po awarii konfiguracji odzyskiwania (HADR)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) składa się z jednego węzła, który uruchamia wystąpienie podstawowej bazy danych i co najmniej jeden węzeł, który uruchamia wystąpienie pomocniczej bazy danych. Zmiany do wystąpienia podstawowej bazy danych są replikowane do dodatkowej bazy danych dla wystąpienia synchronicznie lub asynchronicznie, w zależności od konfiguracji. 

W tym artykule opisano sposób wdrażania i konfigurowania maszyn wirtualnych (VM), zainstalować w ramach klastra i zainstalować IBM Db2 LUW HADR konfiguracji. 

Artykuł nie obejmuje sposobu instalowania i konfigurowania programu IBM Db2 LUW HADR lub SAP instalacji oprogramowania. Aby ułatwić wykonywanie tych zadań, firma Microsoft zapewnia odwołania do instrukcje instalacji SAP i IBM. Ten artykuł koncentruje się na części, które są specyficzne dla środowiska platformy Azure. 

Obsługiwane wersje programu IBM Db2 to 10,5 lub nowszy, zgodnie z opisem w Uwaga SAP [1928533].

Przed rozpoczęciem instalacji, zobacz następujące uwagi SAP i dokumentacji:

| Uwaga SAP | Opis |
| --- | --- |
| [1928533] | Aplikacje środowiska SAP na platformie Azure: Obsługiwane produkty i typy maszyn wirtualnych platformy Azure |
| [2015553] | SAP na platformie Azure: Wymagania wstępne dotyczące obsługi |
| [2178632] | Klucz metryki monitorowania dla rozwiązania SAP na platformie Azure |
| [2191498] | SAP w systemie Linux przy użyciu platformy Azure: Rozszerzone monitorowanie |
| [2243692] | Linux na platformie Azure maszyn wirtualnych (IaaS): Problemy dotyczące licencji SAP |
| [1984787] | SUSE LINUX Enterprise Server 12: Uwagi dotyczące instalacji |
| [1999351] | Rozwiązywanie problemów z rozszerzonego monitorowania dla rozwiązania SAP platformy Azure |
| [2233094] | DB6: Aplikacje SAP na platformie Azure, które używają programu IBM Db2 dla systemów Linux, UNIX i Windows — informacje dodatkowe |
| [1612105] | DB6: Często zadawane pytania dotyczące bazy danych Db2 z HADR |


| Dokumentacja | 
| --- |
| [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Zawiera wszystkie wymagane informacje o SAP dla systemu Linux |
| [Azure maszyny wirtualne, planowania i implementacji dla rozwiązania SAP w systemie Linux] [ planning-guide] przewodnik |
| [Wdrażania maszyn wirtualnych platformy Azure dla rozwiązania SAP w systemie Linux] [ deployment-guide] (w tym artykule) |
| [Usługa Azure Virtual Machines wdrożenie bazy danych zarządzania system(DBMS) dla rozwiązania SAP w systemie Linux] [ dbms-guide] przewodnik |
| [Obciążeń SAP na Azure Lista kontrolna dotycząca planowania i wdrażania][azr-sap-plancheck] |
| [SUSE Linux Enterprise Server dla SAP aplikacji 12 z dodatkiem SP3 najlepsze praktyki prowadnic][sles-for-sap-bp] |
| [SUSE Linux Enterprise wysoką dostępność rozszerzenia 12 z dodatkiem SP3][sles-ha-guide] |
| [Wdrażania systemu DBMS na IBM Db2 Azure maszyn wirtualnych w przypadku obciążeń SAP][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR R 10.5][db2-hadr-10.5] |

## <a name="overview"></a>Omówienie
Aby osiągnąć wysoką dostępność, IBM Db2 LUW z HADR jest zainstalowany na co najmniej dwóch maszyn wirtualnych platformy Azure, które są wdrażane w [zestawu dostępności platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) lub wielu [strefy dostępności platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones). 

Następujące grafiki wyświetlić instalacji serwera bazy danych w dwóch maszyn wirtualnych platformy Azure. Zarówno serwer bazy danych maszyn wirtualnych platformy Azure ma swoje własne magazynie dołączonym i są gotowe do działania. W HADR jedno wystąpienie bazy danych w jednej z maszyn wirtualnych platformy Azure, ma rolę podstawowego wystąpienia. Wszyscy klienci są połączone z tym wystąpieniem podstawowego. Wszystkie zmiany w transakcji bazy danych są zachowywane lokalnie w dzienniku transakcji bazy danych Db2. Jak rekordy dziennika transakcji są zachowywane lokalnie, rekordy są przesyłane za pośrednictwem protokołu TCP/IP z wystąpieniem bazy danych na drugi serwer bazy danych, rezerwowy serwer lub wystąpienie w trybie gotowości. Rezerwowe wystąpienie aktualizuje lokalnej bazy danych przez stopniowe do przodu przeniesionych transakcji rekordów dziennika. W ten sposób rezerwowy serwer jest zsynchronizowany z serwerem podstawowym.

HADR jest tylko funkcje replikacji. W nim nie wykrywanie awarii i nie automatycznego urządzeń przejęcia lub pracy awaryjnej. Przejęcie lub przeniesienie do rezerwowy serwer musi być inicjowana ręcznie przez administratora bazy danych. Aby osiągnąć automatycznego przejęcia i wykrywanie awarii, można użyć funkcję klastrowania program Pacemaker w systemie Linux. Program pacemaker monitorowanie wystąpienia serwera dwie bazy danych. Jeśli wystąpienie serwera podstawowej bazy danych ulegnie awarii, inicjuje program Pacemaker *automatyczne* HADR przejęcia przez serwer rezerwy dynamicznej. Program pacemaker gwarantuje również, że wirtualny adres IP jest przypisywany z nowym serwerem podstawowym.

![Omówienie wysokiej dostępności programu IBM Db2](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

Zapewnienie SAP aplikacji serwerów połączyć się z podstawowej bazy danych, należy nazwę hosta wirtualnego i wirtualnego adresu IP. Zdarzenia na pracę awaryjną serwery aplikacji SAP połączy się nowe wystąpienie podstawowej bazy danych. W środowisku platformy Azure [usługa Azure load balancer](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) jest wymagane do używania wirtualnego adresu IP w taki sposób, który jest wymagany dla HADR IBM Db2. 

Aby pomóc Ci lepiej zrozumieć, jak IBM Db2 LUW HADR i program Pacemaker dopasowuje się do wysokiej dostępności konfiguracji systemu SAP, Poniższa ilustracja przedstawia omówienie konfiguracji charakteryzującej systemu SAP, na podstawie bazy danych programu IBM Db2. Ten artykuł dotyczy tylko programu IBM Db2, ale udostępnia odwołania do innych artykułów na temat sposobu konfigurowania innych składników systemu SAP.

![Omówienie pełnego środowiska wysokiej dostępności programu IBM DB2](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-the-required-steps"></a>Ogólne omówienie wymaganych czynności
Można wdrożyć konfiguracji programu IBM Db2, należy wykonać następujące kroki:

  + Planowanie środowiska.
  + Wdrażanie maszyn wirtualnych.
  + Zaktualizuj SUSE Linux i konfigurowanie systemów plików.
  + Zainstaluj i skonfiguruj program Pacemaker.
  + Zainstaluj [NFS o wysokiej dostępności][nfs-ha].
  + Zainstaluj [ASCS/Wywołujących w osobnym klastrze][ascs-ha].
  + Zainstaluj bazę danych programu IBM Db2 z opcją rozproszonych/wysokiej dostępności (SWPM).
  + Instalowanie i tworzenie pomocniczej bazy danych node i wystąpienia i konfigurowanie HADR.
  + Upewnij się, że działa HADR.
  + Zastosuj konfigurację program Pacemaker do sterowania IBM Db2.
  + Konfigurowanie modułu równoważenia obciążenia platformy Azure.
  + Instalowanie podstawowego i serwerów aplikacji w oknie dialogowym.
  + Sprawdź i dostosowanie konfigurację serwerów aplikacji SAP.
  + Wykonywanie pracy awaryjnej i przejęcia testów.



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Planowanie infrastruktury platformy Azure do hostowania IBM Db2 LUW z HADR

Zakończenie procesu planowania, przed wykonaniem wdrożenia. Planowanie kompilacji podstawę do wdrażania konfiguracji bazy danych Db2 z HADR na platformie Azure. W poniższej tabeli przedstawiono kluczowe elementy, które muszą być częścią planowanie IMB bazy danych Db2 LUW (część bazy danych środowiska SAP):

| Temat | Krótki opis |
| --- | --- |
| Definiowanie grup zasobów platformy Azure | Grupy zasobów, gdy wdrażanie maszyn wirtualnych, sieci wirtualnej, usługa Azure Load Balancer i innych zasobów. Może być istniejącym lub nowym. |
| Sieć wirtualna / podsieć definicji | Gdy maszyny wirtualne programu IBM Db2 i modułu równoważenia obciążenia platformy Azure są wdrażane. Może być istniejących i nowo utworzony. |
| Maszyny wirtualne hostujące rozwiązania IBM Db2 LUW | Rozmiar maszyny Wirtualnej, Magazyn, sieci, adres IP. |
| Nazwa hosta wirtualnego i wirtualnego adresu IP dla bazy danych programu IBM Db2| Wirtualny adres IP lub hosta nazwę używaną dla połączenia serwerów aplikacji SAP. **DB-virt-hostname**, **virt-db-ip**. |
| Preferowane platformy Azure | Preferowane platformy Azure lub preferowane interwencja (zdecydowanie zalecane). Nie będzie mógł metodę, aby uniknąć sytuacji mózg podziału. |
| INTERWENCJA MASZYNY WIRTUALNEJ | Rozmiar maszyny wirtualnej interwencja, magazynu, użycia sieci. |
| Azure Load Balancer | Użycie Basic lub Standard (zalecane), port dla bazy danych Db2 (zalecamy 62500) sondy **port sondy**. |
| Rozpoznawanie nazw| Jak rozpoznawanie nazw działa w środowisku. Zdecydowanie zaleca się usługi DNS. Można użyć pliku hostów lokalnych. |
    
Aby uzyskać więcej informacji na temat program Pacemaker systemu Linux, na platformie Azure, zobacz [skonfigurować program Pacemaker w systemie SUSE Linux Enterprise Server na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker).

## <a name="deployment-on-suse-linux"></a>Wdrażanie w systemie SUSE Linux

Agent zasobów dla programu IBM Db2 LUW znajduje się w systemie SUSE Linux Enterprise Server dla aplikacji SAP. Dla ustawienia opisane w niniejszym dokumencie należy użyć systemu SUSE Linux Server do aplikacji SAP. W portalu Azure Marketplace zawiera obraz dla SUSE Enterprise Server 12 aplikacje SAP, który służy do wdrażania nowych maszyn wirtualnych platformy Azure. Należy pamiętać o różnych modeli pomocy technicznej lub usługi, które są oferowane przez SUSE w portalu Azure Marketplace, w przypadku wybrania obrazu maszyny Wirtualnej w witrynie Azure Marketplace do maszyny Wirtualnej. 

### <a name="hosts-dns-updates"></a>Hosts: Aktualizacje rekordów DNS
Sporządź listę wszystkich nazw hostów, w tym nazwy hostów wirtualnych i zaktualizuj serwery DNS, aby umożliwić właściwy adres IP do rozpoznawania nazwy hosta. Jeśli serwer DNS nie istnieje lub nie można zaktualizować i utworzenie wpisów DNS, należy użyć plików hosta lokalnego, poszczególnych maszyn wirtualnych, które uczestniczą w tym scenariuszu. Jeśli używasz wpisy plików hosta, upewnij się, że wpisy są stosowane do wszystkich maszyn wirtualnych w środowisku systemu SAP. Jednak zaleca się korzystania z usługi serwera DNS, który najlepiej, jeśli rozciąga się na platformie Azure


### <a name="manual-deployment"></a>Ręczne wdrażanie

Upewnij się, że wybranego systemu operacyjnego jest obsługiwana przez oprogramowanie firmy IBM/SAP dla IBM Db2 LUW. Lista obsługiwanych wersji systemu operacyjnego dla maszyn wirtualnych platformy Azure i bazy danych Db2 wersjami jest dostępna w Uwaga SAP [1928533]. Lista wersji systemu operacyjnego w przypadku poszczególnych wersji bazy danych Db2 jest dostępna w macierzy dostępności produktów SAP. Zdecydowanie zaleca się co najmniej w systemie SLES 12 z dodatkiem SP3 ze względu na ulepszenia wydajności związane z platformy Azure, w tym lub nowszej wersje systemu SUSE Linux.

1. Utwórz lub wybierz grupę zasobów.
1. Utwórz lub wybierz sieć wirtualną i podsieć.
1. Tworzenie zestawu dostępności platformy Azure można też wdrażać strefie dostępności.
    + Dla zestawu dostępności ustawione domenach aktualizacji maksymalną na 2.
1. Tworzenie maszyny wirtualnej 1.
    + SLES na użytek SAP obrazów w portalu Azure Marketplace.
    + Wybierz zestaw dostępności platformy Azure, który został utworzony w kroku 3, lub wybierz strefę dostępności.
1.  Tworzenie maszyny wirtualnej 2.
    + SLES na użytek SAP obrazów w portalu Azure Marketplace.
    + Wybierz zestaw dostępności platformy Azure w utworzonego w kroku 3, lub wybierz strefę dostępności (nie tej samej strefie tak jak w kroku 3).
1. Dodawanie dysków danych do maszyn wirtualnych, a następnie sprawdź zalecenia konfiguracji systemu plików, w artykule [wdrażania systemu DBMS na IBM Db2 Azure maszyn wirtualnych w przypadku obciążeń SAP][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>Tworzenie klastra program Pacemaker
    
Aby utworzyć podstawowy klaster program Pacemaker dla tego serwera programu IBM Db2, zobacz [skonfigurować program Pacemaker w systemie SUSE Linux Enterprise Server na platformie Azure][sles-pacemaker]. 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>Zainstaluj środowisko LUW bazy danych Db2 IBM i SAP

Przed rozpoczęciem instalacji środowiska SAP, IBM Db2 LUW w oparciu można znaleźć w poniższej dokumentacji:

+ Dokumentacja platformy Azure
+ Dokumentacja usługi SAP
+ Dokumentację firmy IBM

Łącza do tej dokumentacji znajdują się w wprowadzające sekcji tego artykułu.

Sprawdź instrukcje instalacji SAP o instalowaniu aplikacji NetWeaver w IBM Db2 LUW.

Przewodniki w portalu pomocy SAP można znaleźć za pomocą [wyszukiwania Przewodnik po instalacji SAP][sap-instfind].

Można zmniejszyć liczbę przewodniki wyświetlana w portalu przez ustawienie następujących filtrów:

- Chcę: "Zainstaluj nowy system"
- Moja baza danych: "IBM Db2 dla systemów Linux, Unix i Windows"
- Dodatkowe filtry dla wersji oprogramowania SAP NetWeaver, konfiguracja stosu lub systemu operacyjnego

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Instalacja wskazówki dotyczące konfigurowania IBM Db2 LUW z HADR

Aby skonfigurować podstawowe wystąpienie bazy danych IBM Db2 LUW:

- Korzystając z wysokiej dostępności lub rozproszonej opcji.
- Zainstaluj wystąpienie SAP ASCS/Wywołujących i bazy danych.
- Tworzenie kopii zapasowej bazy danych nowo zainstalowany.


> [!IMPORTANT] 
> Zanotuj "bazy danych portowi komunikacyjnemu" ustawioną podczas instalacji. Musi być tego samego numeru portu dla obu wystąpień bazy danych

Aby skonfigurować serwer bazy danych w stanie wstrzymania, za pomocą procedury kopiowania jednorodny system SAP, wykonaj następujące kroki:

1. Wybierz **kopia systemu** opcja > **systemach** > **rozproszonych** > **wystąpienie bazy danych**.
1. Jako metodę kopiowania, wybierz **jednorodny System** tak, aby kopia zapasowa służy do przywrócenia kopii zapasowej w wystąpieniu serwera rezerwy dynamicznej.
1. Po przejściu do kroku wyjścia, aby przywrócić bazę danych związanym z kopiowaniem jednorodny system Zamknij Instalatora. Przywróć bazę danych z kopii zapasowej hostem głównym. Wszystkie fazy kolejnej instalacji zostały już wykonane na serwerze podstawowej bazy danych.
1. Skonfiguruj HADR dla programu IBM Db2.

   > [!NOTE]
   > W przypadku instalacji i konfiguracji, które są specyficzne dla platformy Azure i program Pacemaker: W trakcie procedury instalacji za pośrednictwem Menedżera inicjowania obsługi oprogramowania SAP jest jawne pytanie o wysokiej dostępności dla programu IBM Db2 LUW:
   >+ Nie należy wybierać **pureScale IBM Db2**.
   >+ Nie należy wybierać **zainstalować IBM Tivoli systemu automatyzacji Multiplatforms**.
   >+ Nie należy wybierać **Generowanie plików konfiguracji klastra**.

   Kiedy używasz urządzenia interwencja dla program Pacemaker w systemie Linux, należy ustawić następujące parametry HADR bazy danych Db2:
   + Czas trwania okna elementu równorzędnego HADR (w sekundach) (HADR_PEER_WINDOW) = 300  
   + Wartość limitu czasu HADR (HADR_TIMEOUT) = 60

   Gdy używasz agenta preferowane program Pacemaker platformy Azure, należy ustawić następujące parametry:
   + Czas trwania okna elementu równorzędnego HADR (w sekundach) (HADR_PEER_WINDOW) = 900  
   + Wartość limitu czasu HADR (HADR_TIMEOUT) = 60

Firma Microsoft zaleca poprzedniego parametrów, na podstawie w początkowej fazie testowania trybu failover/przejęcie. Jest to konieczne, test do prawidłowego działania funkcji trybu failover i przejęcia przy użyciu tych ustawień parametrów. Ponieważ poszczególne konfiguracje mogą się różnić, parametry mogą wymagać dostosowania. 

> [!IMPORTANT]
> Specyficzne dla programu IBM Db2 z konfiguracją HADR przy użyciu normalnego uruchamiania: Wystąpienie bazy danych w dodatkowej lub wstrzymania musi być uruchomiona, przed rozpoczęciem wystąpienia podstawowej bazy danych.

Dla celów demonstracyjnych i procedur opisanych w tym artykule, baza danych jest identyfikator SID **PTR**.

#### <a name="ibm-db2-hadr-check"></a>IBM Db2 HADR wyboru
Po skonfigurowaniu HADR i stanu elementów RÓWNORZĘDNYCH i POŁĄCZONO w węzłach podstawowego i będącymi w gotowości, sprawdzić następujące:

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

Gdy używasz program Pacemaker do automatycznej pracy awaryjnej w przypadku awarii węzła, należy odpowiednio skonfigurować wystąpienia bazy danych Db2 i program Pacemaker. W tej sekcji opisano tego typu konfiguracją.

Następujące elementy mają prefiks albo:

- **[A]** : Ma zastosowanie do wszystkich węzłów
- **[1]** : Dotyczy tylko węzeł 1 
- **[2]** : Dotyczy tylko węzeł 2

**[A]**  Wymagania wstępne dotyczące konfiguracji program Pacemaker:
1. Zamknij oba serwery bazy danych z bazy danych db2 użytkownika\<sid > z db2stop.
1. Zmień środowisko powłoki dla bazy danych db2\<sid > użytkownikowi */bin/ksh*. Firma Microsoft zaleca użycie narzędzia Yast. 


### <a name="pacemaker-configuration"></a>Program pacemaker konfiguracji

**[1]**  Program IBM Db2 specyficzne dla HADR Pacemaker konfiguracji:
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]**  Tworzenie IBM Db2 zasobów:
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

**[1]**  Start IBM Db2 zasobów:
* Umieść program Pacemaker w trybie konserwacji.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]**  Upewnij się, że kondycja klastra jest OK i wszystkie zasoby są uruchamiane. Nie jest ważna, który węzeł zasoby są uruchomione na.
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
> Musisz zarządzać program Pacemaker klastrowanego wystąpienia bazy danych Db2 za pomocą narzędzi program Pacemaker. Jeśli używasz bazy danych db2 poleceń, takich jak db2stop, program Pacemaker wykrywa akcji jako błąd zasobu. Jeśli przeprowadzasz konserwacji można umieścić węzłów lub zasobów w trybie konserwacji. Program pacemaker wstrzymuje działanie monitorowania zasobów, a następnie można użyć poleceń administracyjnych normalne bazy danych db2.


### <a name="configure-azure-load-balancer"></a>Konfigurowanie modułu Azure Load Balancer
Aby skonfigurować moduł równoważenia obciążenia platformy Azure, firma Microsoft zaleca, możesz użyć [standardowy SKU modułu równoważenia obciążenia Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) , a następnie wykonaj następujące czynności:

1. Utwórz pulę adresów IP frontonu:

   a. W witrynie Azure portal Otwórz usługę równoważenia obciążenia platformy Azure, wybierz opcję **puli adresów IP frontonu**, a następnie wybierz pozycję **Dodaj**.

   b. Wprowadź nazwę nowej puli adresów IP frontonu (na przykład **połączenia bazy danych Db2**).

   c. Ustaw **przypisania** do **statyczne**, a następnie wprowadź adres IP **adresu IP wirtualnej** zdefiniowane na początku.

   d. Kliknij przycisk **OK**.

   e. Po utworzeniu nowej puli adresów IP frontonu, należy pamiętać, puli adresów IP.

1. Tworzenie puli zaplecza:

   a. W witrynie Azure portal Otwórz usługę równoważenia obciążenia platformy Azure, wybierz opcję **pule zaplecza**, a następnie wybierz pozycję **Dodaj**.

   b. Wprowadź nazwę nowej puli zaplecza (na przykład **zaplecze bazy danych Db2**).

   c. Wybierz **Dodaj maszynę wirtualną**.

   d. Wybierz zestaw dostępności lub maszyny wirtualne hostujące rozwiązania IBM Db2 bazy danych utworzonej w poprzednim kroku.

   e. Wybierz maszyny wirtualne klastra programu IBM Db2.

   f. Kliknij przycisk **OK**.

1. Utwórz sondę kondycji:

   a. W witrynie Azure portal Otwórz usługę równoważenia obciążenia platformy Azure, wybierz opcję **sond kondycji**i wybierz **Dodaj**.

   b. Wprowadź nazwę nowego sondy kondycji (na przykład **hp bazy danych Db2**).

   c. Wybierz **TCP** jako protokół i port **62500**. Zachowaj **interwał** wartość **5**i Zachowaj **próg złej kondycji** wartość **2**.

   d. Kliknij przycisk **OK**.

1. Tworzenie reguł równoważenia obciążenia:

   a. W witrynie Azure portal Otwórz usługę równoważenia obciążenia platformy Azure, wybierz opcję **reguły równoważenia obciążenia**, a następnie wybierz pozycję **Dodaj**.

   b. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład **SID bazy danych Db2**).

   c. Wybierz adres IP frontonu, puli zaplecza i sondy kondycji, który został utworzony wcześniej (na przykład **frontonu bazy danych Db2**).

   d. Zachowaj **protokołu** równa **TCP**i wprowadź numer portu *port komunikacyjny bazy danych*.

   e. Zwiększ **limitu czasu bezczynności** do 30 minut.

   f. Upewnij się, że **włączyć pływającego adresu IP**.

   g. Kliknij przycisk **OK**.


### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Wprowadzanie zmian do profilów SAP wirtualnego adresu IP na potrzeby połączenia
Połączyć się z podstawowego wystąpienia konfiguracji HADR SAP warstwy aplikacji musi używać wirtualnego adresu IP, zdefiniowane przez użytkownika, która skonfigurowane dla usługi Azure Load Balancer. Wymagane są następujące zmiany:

/sapmnt/\<SID >/profile/domyślne. PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>Instalowanie podstawowego i serwerów aplikacji w oknie dialogowym

Podczas instalacji podstawowej i serwerów aplikacji okna dialogowego konfiguracji bazy danych Db2 HADR, użyj host wirtualny nazwę, która jest pobierana w konfiguracji. 

Jeśli przeprowadzono instalacji przed utworzoną konfigurację bazy danych Db2 HADR, należy wprowadzić zmiany, zgodnie z opisem w poprzedniej sekcji i w następujący sposób stosy języka Java SAP.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>Sprawdź systemy stosu ABAP + Java lub Java JDBC adresu URL

Aby sprawdzić lub zaktualizować adres URL JDBC, należy użyć narzędzia konfiguracji J2EE. Narzędzie konfiguracji J2EE jest narzędzie graficzne, dlatego musisz mieć X zainstalowany serwer:
 
1. Zaloguj się na głównym serwerze aplikacji wystąpienia J2EE i wykonać:
     <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>
1. W ramach po lewej stronie wybierz **store bezpieczeństwo**.
1. W prawej ramce wybierz klucza jdbc/pulę / \ <SAPSID> /URL.
1. Zmień nazwę hosta w adresie URL JDBC na nazwę hosta wirtualnego.
     <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>
1. Wybierz **Dodaj**.
1. Aby zapisać zmiany, wybierz ikonę dysku, w lewym górnym rogu.
1. Zamknij narzędzie konfiguracji.
1. Ponownie uruchom wystąpienie języka Java.

## <a name="configure-log-archiving-for-hadr-setup"></a>Konfigurowanie dzienników archiwizowanie na potrzeby instalacji HADR
Można skonfigurować dziennika bazy danych Db2, archiwizowanie na potrzeby instalacji HADR, zaleca się skonfigurowanie podstawowego i wstrzymania bazy danych do mają możliwość pobierania automatycznego logowania z wszystkich lokalizacji archiwum dziennika. Podstawowy i zapasowy database musi mieć możliwość pobierania plików archiwum dziennika z wszystkie lokalizacje archiwum dziennika bazy danych albo jedną wystąpień może archiwizuj plików dzienników. 

Archiwizowanie dziennika jest wykonywane tylko przez podstawową bazę danych. Jeśli zmienisz HADR ról serwerów bazy danych lub jeśli wystąpi błąd, nowej podstawowej bazy danych jest odpowiedzialny za Archiwizowanie dziennika. Jeśli skonfigurowano wiele lokalizacji archiwum dziennika dzienników może archiwizowany dwa razy. W przypadku lokalnych lub zdalnych — wyrównywanie może również należy ręcznie skopiować dzienników zarchiwizowanych ze starego serwera podstawowego do lokalizacji aktywnego dziennika z nowym serwerem podstawowym.

Firma Microsoft zaleca skonfigurowanie wspólnej udziału NFS, w których dzienniki są zapisywane z obu węzłów. Udziału NFS musi być o wysokiej dostępności. 

Można użyć istniejących udziałów NFS o wysokiej dostępności dla transportów lub katalogu profilu. Aby uzyskać więcej informacji, zobacz:

- [Wysoka dostępność systemu NFS na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server][nfs-ha] 
- [Wysoka dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server za pomocą usługi Azure Files NetApp dla aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
- [Usługa Azure Files NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) (do tworzenia udziałów NFS)


## <a name="test-the-cluster-setup"></a>Testuj ustawienia klastra

W tej sekcji opisano, jak można przetestować konfigurację HADR bazy danych Db2. *Każdego testu przyjęto założenie, że zalogowano Cię jako użytkownik główny* i podstawowego programu IBM Db2 działa na *azibmdb01* maszyny wirtualnej.

Początkowy stan dla wszystkich przypadków testowych zostało wyjaśnione w tym miejscu: (crm_mon - r lub crm status)

- **Stan CRM** jest migawką stanu program Pacemaker w czasie wykonywania 
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

Oryginalny stan w systemie SAP jest udokumentowany w transakcji DBACOCKPIT > Konfiguracja > Przegląd, jak pokazano na poniższej ilustracji:

![DBACockpit — sprzed migracji](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>Przejęcie test programu IBM Db2


> [!IMPORTANT] 
> Przed rozpoczęciem testu, upewnij się, że:
> * Program pacemaker nie ma żadnych akcji nie powiodło się (stan crm).
> * Istnieją nie ograniczenia lokalizacji (resztek testu migracji)
> * Synchronizacja programu IBM Db2 HADR pracuje. Skontaktuj się z użytkownika bazy danych db2\<sid > <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Migrowanie węzła, który jest uruchomiony podstawowej bazy danych Db2, wykonując następujące polecenie:
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

Oryginalny stan w systemie SAP jest udokumentowany w transakcji DBACOCKPIT > Konfiguracja > Przegląd, jak pokazano na poniższej ilustracji:

![DBACockpit — po migracji](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

Proces migracji zasobów za pomocą "migrate zasobów crm" tworzy ograniczenia lokalizacji. Ograniczenia lokalizacji powinny być usuwane. Ograniczenia lokalizacji nie są usuwane, zasób nie może wykonać powrotu po awarii, lub może wystąpić niepożądane przejęcia. 

Migrowanie zasobów do *azibmdb01* i wyczyść ograniczenia lokalizacji
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>

- **Migrowanie zasobów CRM \<res_name > <host>:** Tworzy ograniczenia lokalizacji i przejęcia mogą powodować problemy
- **Wyczyść zasobów CRM \<res_name >** : Usuwa ograniczenia lokalizacji
- **czyszczenie zasobów CRM \<res_name >** : Czyści wszystkie błędy zasobu

### <a name="test-the-fencing-agent"></a>Test agent ogrodzenia

W tym przypadku przetestujemy interwencja preferowane, w którym firma Microsoft zaleca wykonanie korzystając z systemu SUSE Linux.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

Węzeł klastra *azibmdb01* powinien zostać przeprowadzony ponowny rozruch. Podstawową rolą HADR IBM Db2 zamierza można przenieść do *azibmdb02*. Gdy *azibmdb01* jest do trybu online, Db2, wystąpienie przechodzi do przenoszenia roli wystąpienia pomocniczej bazy danych. 

Usługa program Pacemaker nie rozpoczyna się automatycznie w głównej pierwszej został uruchomiony ponownie, należy uruchomić je ręcznie przy użyciu:

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>Testowanie ręczne przejęcie

Możesz przetestować ręczne przejęcia przez zatrzymanie usługi program Pacemaker w *azibmdb01* węzła:
<pre><code>service pacemaker stop</code></pre>

Stan *azibmdb02*
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

Po włączeniu trybu failover, możesz uruchomić usługę ponownie na *azibmdb01*.
<pre><code>service pacemaker start</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Kasowanie procesu bazy danych Db2 w węźle, który uruchamia HADR podstawowej bazy danych

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

Wystąpienie bazy danych Db2 zamierza się niepowodzeniem, a program Pacemaker informację o stanie po:

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

Program pacemaker spowoduje ponowne uruchomienie wystąpienia podstawowej bazy danych Db2 w tym samym węźle lub za pośrednictwem zakończy się niepowodzeniem na węzeł, na którym jest uruchomione wystąpienie pomocniczej bazy danych i zwróci błąd.

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


### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Kasowanie procesu bazy danych Db2 w węźle, który uruchamia wystąpienie pomocniczej bazy danych

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

Wystąpienie bazy danych Db2 pobiera ponownie uruchomiony w roli dodatkowej, który miał on przypisany przed.

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



### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Zatrzymaj bazy danych za pośrednictwem db2stop życie w węźle, który uruchamia wystąpienie HADR podstawowej bazy danych

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


### <a name="crash-vm-with-restart-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Awarii maszyny Wirtualnej z ponownym uruchomieniem komputera na węźle, który uruchamia wystąpienie HADR podstawowej bazy danych

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



### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Awarii maszyny Wirtualnej, która uruchamia wystąpienie HADR podstawowej bazy danych za pomocą "zatrzymania"

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

W takim przypadku program Pacemaker wykryje, że węzeł, na którym jest uruchomione wystąpienie podstawowej bazy danych nie odpowiada.

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

następnym krokiem jest sprawdzenie *Podziel mózg* sytuacji. Po działającego węzła stwierdził, że węzeł, który ostatnio uruchomiono wystąpienie podstawowej bazy danych nie działa, pracy w trybie failover zasobów jest wykonywana.
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


W przypadku "powstrzymanie zmniejszania" węzła, węzeł nie powiodło się musi zostać uruchomiona ponownie za pomocą narzędzia do zarządzania platformy Azure (w witrynie Azure portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure). Po powrocie do trybu online węzła nie powiodło się uruchamia wystąpienie bazy danych Db2 w pełni rolę pomocniczej.

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
- [Architektura wysokiej dostępności i scenariusze SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Skonfiguruj program Pacemaker w systemie SUSE Linux Enterprise Server na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)

     

