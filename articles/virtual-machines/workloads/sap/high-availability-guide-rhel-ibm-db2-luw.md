---
title: Konfigurowanie systemu IBM Db2 HADR na maszynach wirtualnych platformy Azure na platformie RHEL | Dokumenty firmy Microsoft
description: Ustal wysoką dostępność funkcji IBM Db2 LUW na maszynach wirtualnych platformy Azure (VMs) RHEL.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/13/2020
ms.author: juergent
ms.openlocfilehash: c6a230f6abeab45c56aab2db40b8b1defcc06d90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598701"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105
[2694118]:https://launchpad.support.sap.com/#/notes/2694118


[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm

[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[rhel-ha-addon]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index
[rhel-ha-admin]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index
[rhel-ha-ref]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index
[rhel-azr-supp]:https://access.redhat.com/articles/3131341
[rhel-azr-inst]:https://access.redhat.com/articles/3252491
[rhel-db2-supp]:https://access.redhat.com/articles/3144221
[ascs-ha-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel
[glusterfs]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs
[rhel-pcs-azr]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker
[anf-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-red-hat-enterprise-linux-server"></a>Wysoka dostępność programu IBM Db2 LUW na maszynach wirtualnych platformy Azure w systemie Red Hat Enterprise Linux Server

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
| [2002167] | Red Hat Enterprise Linux 7.x: Instalacja i aktualizacja |
| [2694118] | Dodatek Red Hat Enterprise Linux HA na platformie Azure |
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
| [Omówienie dodatku o wysokiej dostępności dla systemu Red Hat Enterprise Linux 7][rhel-ha-addon] |
| [Administracja dodatkami o wysokiej dostępności][rhel-ha-admin] |
| [Dokumentacja dodatku o wysokiej dostępności][rhel-ha-ref] |
| [Zasady pomocy technicznej dotyczące klastrów wysokiej dostępności RHEL — maszyny wirtualne platformy Microsoft Azure jako elementy członkowskie klastra][rhel-azr-supp]
| [Instalowanie i konfigurowanie klastra o wysokiej dostępności w systemie Microsoft Azure dla systemu Red Hat Enterprise 7.4 (lub nowszych)][rhel-azr-inst]
| [Wdrażanie systemu DBMS usługi Azure Virtual Machines programu IBM Db2 dla obciążenia SAP][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR 10.5][db2-hadr-10.5] |
| [Zasady pomocy technicznej dotyczące klastrów O wysokiej dostępności RHEL — zarządzanie programem IBM Db2 dla systemów Linux, Unix i Windows w klastrze][rhel-db2-supp]



## <a name="overview"></a>Omówienie
Aby osiągnąć wysoką dostępność, moduł LUW IBM Db2 z usługą HADR jest instalowany na co najmniej dwóch maszynach wirtualnych platformy Azure, które są wdrażane w [zestawie dostępności platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) lub w [strefach dostępności platformy Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones) 

Na poniższej grafice jest wyświetlana konfiguracja dwóch maszyn wirtualnych platformy Azure na serwerze bazy danych. Oba serwery bazy danych Platformy Azure maszyny wirtualne mają własny magazyn podłączony i są uruchomione. W hadr jedno wystąpienie bazy danych w jednym z maszyn wirtualnych platformy Azure ma rolę wystąpienia podstawowego. Wszyscy klienci są połączeni z wystąpieniem podstawowym. Wszystkie zmiany w transakcjach bazy danych są utrwalone lokalnie w dzienniku transakcji db2. Ponieważ rekordy dziennika transakcji są zachowywane lokalnie, rekordy są przesyłane za pośrednictwem protokołu TCP/IP do wystąpienia bazy danych na drugim serwerze bazy danych, na serwerze rezerwowym lub wystąpieniu rezerwowym. Wystąpienie rezerwowe aktualizuje lokalną bazę danych, przewijając rekordy dziennika przeniesionych transakcji. W ten sposób serwer rezerwowy jest synchronizowany z serwerem podstawowym.

HADR jest tylko funkcją replikacji. Nie ma wykrywania awarii i nie ma automatycznego przejęcia lub pracy awaryjnej. Przejęcie lub przeniesienie na serwer rezerwowy musi być zainicjowane ręcznie przez administratora bazy danych. Aby uzyskać automatyczne wykrywanie przejęcia i awarii, można użyć funkcji klastrowania szesnastego linuksa. Rozrusznik serca monitoruje dwa wystąpienia serwera bazy danych. Gdy wystąpienie serwera podstawowej bazy danych ulega awarii, program Pacemaker inicjuje *automatyczne* przejęcie hadr przez serwer rezerwowy. Rozrusznik zapewnia również, że wirtualny adres IP jest przypisany do nowego serwera podstawowego.

![Omówienie wysokiej dostępności programu IBM Db2](./media/high-availability-guide-rhel-ibm-db2-luw/ha-db2-hadr-lb-rhel.png)

Aby serwery aplikacji SAP łączyły się z podstawową bazą danych, potrzebna jest nazwa hosta wirtualnego i wirtualny adres IP. W przypadku pracy awaryjnej serwery aplikacji SAP będą łączyć się z nowym wystąpieniem podstawowej bazy danych. W środowisku platformy Azure [moduł równoważenia obciążenia platformy Azure](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) jest wymagany do używania wirtualnego adresu IP w sposób wymagany dla usługi HADR ibm db2. 

Aby pomóc Ci w pełni zrozumieć, w jaki sposób ibm Db2 LUW z HADR i pacemaker pasuje do konfiguracji systemu SAP o wysokiej dostępności, poniższy obraz przedstawia przegląd wysoce dostępnej konfiguracji systemu SAP opartego na bazie danych IBM Db2. Ten artykuł dotyczy tylko ibm Db2, ale zawiera odniesienia do innych artykułów na temat konfigurowania innych składników systemu SAP.

![Ibm DB2 omówienie pełnego środowiska o wysokiej dostępności](./media/high-availability-guide-rhel-ibm-db2-luw/end-2-end-ha-rhel.png)


### <a name="high-level-overview-of-the-required-steps"></a>Przegląd wysokiego poziomu wymaganych kroków
Aby wdrożyć konfigurację IBM Db2, należy wykonać następujące kroki:

  + Zaplanuj swoje środowisko.
  + Wdrażanie maszyn wirtualnych.
  + Zaktualizuj RHEL Linux i skonfiguruj systemy plików.
  + Zainstaluj i skonfiguruj rozrusznik serca.
  + Konfigurowanie [klastra glusterfs][glusterfs] lub [plików NetApp platformy Azure][anf-rhel]
  + Zainstaluj [ASCS/ERS w oddzielnym klastrze][ascs-ha-rhel].
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
| Ogrodzenie azure | Metoda, aby uniknąć sytuacji podzielonego mózgu jest zapobiec. |
| Azure Load Balancer | Użycie standardowego lub standardowego (zalecanego), portu sondy dla bazy danych Db2 (nasza rekomendacja 62500) **probe-port**. |
| Rozpoznawanie nazw| Jak działa rozpoznawanie nazw w środowisku. Usługa DNS jest wysoce zalecana. Można użyć pliku hostów lokalnych. |
    
Aby uzyskać więcej informacji na temat rozrusznika serca systemu Linux na platformie Azure, zobacz [Konfigurowanie rozrusznika serca w systemie Red Hat Enterprise Linux na platformie Azure][rhel-pcs-azr].

## <a name="deployment-on-red-hat-enterprise-linux"></a>Wdrożenie na systemie Red Hat Enterprise Linux

Agent zasobów dla IBM Db2 LUW znajduje się w dodatku Red Hat Enterprise Linux Server HA Addon. W przypadku konfiguracji opisanej w tym dokumencie należy użyć systemu Red Hat Enterprise Linux dla systemu SAP. Portal Azure Marketplace zawiera obraz systemu Red Hat Enterprise z systemem Linux 7.4 dla systemu SAP lub nowszego, którego można użyć do wdrożenia nowych maszyn wirtualnych platformy Azure. Należy pamiętać o różnych modeli pomocy technicznej lub usługi, które są oferowane przez Red Hat za pośrednictwem portalu Azure Marketplace po wybraniu obrazu maszyny Wirtualnej w portalu Azure VM Marketplace.

### <a name="hosts-dns-updates"></a>Hosty: aktualizacje DNS
Należy tworzyć listę wszystkich nazw hostów, w tym nazwy hostów wirtualnych, i aktualizować serwery DNS, aby umożliwić prawidłowe rozpoznawanie nazw adresów IP. Jeśli serwer DNS nie istnieje lub nie można zaktualizować i utworzyć wpisów DNS, należy użyć lokalnych plików hosta poszczególnych maszyn wirtualnych, które uczestniczą w tym scenariuszu. Jeśli używasz wpisów plików hosta, upewnij się, że wpisy są stosowane do wszystkich maszyn wirtualnych w środowisku systemowym SAP. Zaleca się jednak użycie systemu DNS, który najlepiej rozszerzy się na platformę Azure


### <a name="manual-deployment"></a>Wdrożenie ręczne

Upewnij się, że wybrany system operacyjny jest obsługiwany przez IBM/SAP dla środowiska IBM Db2 LUW. Lista obsługiwanych wersji systemu operacyjnego dla maszyn wirtualnych platformy Azure i wersji Db2 jest dostępna w komunikacie SAP [Note 1928533]. Lista wydań systemu operacyjnego według poszczególnych wersji Db2 jest dostępna w macierzy dostępności produktów SAP. Firma Microsoft zdecydowanie zaleca co najmniej Red Hat Enterprise Linux 7.4 dla SAP ze względu na ulepszenia wydajności związane z platformą Azure w tej lub nowszej wersji systemu Red Hat Enterprise Linux.

1. Tworzenie lub wybieranie grupy zasobów.
1. Utwórz lub wybierz sieć wirtualną i podsieć.
1. Utwórz zestaw dostępności platformy Azure lub wdrożyć strefę dostępności.
    + Dla zestawu dostępności ustaw maksymalną liczbę domen aktualizacji na 2.
1. Utwórz maszynę wirtualną 1.
    + Użyj red hat enterprise linux dla obrazu SAP w portalu Azure Marketplace.
    + Wybierz zestaw dostępności platformy Azure utworzony w kroku 3 lub wybierz strefę dostępności.
1.  Utwórz maszynę wirtualną 2.
    + Użyj red hat enterprise linux dla obrazu SAP w portalu Azure Marketplace.
    + Wybierz ustawioną dostępność platformy Azure w utworzonym w kroku 3 lub wybierz strefę dostępności (nie tę samą strefę, co w kroku 3).
1. Dodaj dyski danych do maszyn wirtualnych, a następnie sprawdź zalecenie konfiguracji systemu plików w artykule [Wdrożenie DBMS maszyn wirtualnych IBM Db2 Azure dla obciążenia SAP][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>Tworzenie klastra rozrusznika serca
    
Aby utworzyć podstawowy klaster rozrusznika dla tego serwera IBM Db2, zobacz [Konfigurowanie rozrusznika serca na systemie Red Hat Enterprise Linux na platformie Azure][rhel-pcs-azr]. 

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

#### <a name="red-hat-firewall-rules"></a>Reguły zapory Red Hat
Red Hat Enterprise Linux ma domyślnie włączoną zaporę. 

<pre><code>#Allow access to SWPM tool. Rule is not permanent.
sudo firewall-cmd --add-port=4237/tcp</code></pre>

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Wskazówki dotyczące instalacji do konfigurowania urządzenia IBM Db2 LUW z hadr

Aby skonfigurować podstawowe wystąpienie bazy danych IBM Db2 LUW:

- Użyj opcji wysokiej dostępności lub rozproszonej.
- Zainstaluj wystąpienie SAP ASCS/ERS i Database.
- Wykonaj kopię zapasową nowo zainstalowanej bazy danych.

> [!IMPORTANT] 
> Zapisz "Port komunikacji bazy danych", który jest ustawiony podczas instalacji. Musi to być ten sam numer portu dla obu wystąpień bazy danych.
>![Definicja portu SAP SWPM](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-swpm-db2-port.png)

### <a name="ibm-db2-hadr-settings-for-azure"></a>Ustawienia IBM Db2 HADR dla platformy Azure

   Korzystając z agenta szermierki azure pacemaker, należy ustawić następujące parametry:

   - Czas trwania okna równorzędnego HADR (w sekundach) (HADR_PEER_WINDOW) = 240  
   - Wartość limitu czasu HADR (HADR_TIMEOUT) = 45

Zaleca się poprzednie parametry na podstawie początkowych testów pracy awaryjnej/przejęcia. Jest to obowiązkowe, aby przetestować dla prawidłowej funkcjonalności pracy awaryjnej i przejęcia z tych ustawień parametrów. Ponieważ poszczególne konfiguracje mogą się różnić, parametry mogą wymagać dostosowania. 

> [!NOTE]
> Specyficzne dla ibm Db2 z konfiguracją HADR przy normalnym starcie: Wystąpienie pomocniczej lub rezerwowej bazy danych musi być uruchomione przed uruchomieniem wystąpienia podstawowej bazy danych.

   
> [!NOTE]
> W przypadku instalacji i konfiguracji specyficznej dla platformy Azure i rozrusznika serca: podczas procedury instalacji za pośrednictwem menedżera aprowizacji oprogramowania SAP pojawia się wyraźne pytanie dotyczące wysokiej dostępności środowiska IBM Db2 LUW:
>+ Nie należy wybierać **ibm Db2 pureScale**.
>+ Nie należy **wybierać opcji Zainstaluj ibm Tivoli System Automation for Multiplatforms**.
>+ Nie należy wybierać opcji **Generuj pliki konfiguracyjne klastra**.
>![SAP SWPM - Opcje DB2 HA](./media/high-availability-guide-rhel-ibm-db2-luw/swpm-db2ha-opt.png)


Aby skonfigurować serwer bazy danych w trybie gotowości przy użyciu jednorodnej procedury kopiowania systemu SAP, wykonaj następujące kroki:

1. Wybierz opcję **Kopiowanie systemu** > **wystąpienie systemy** > **docelowe Rozproszona** > **baza danych**.
1. Jako metodę kopiowania wybierz **jednorodny system,** aby można było użyć kopii zapasowej do przywrócenia kopii zapasowej w wystąpieniu serwera rezerwowego.
1. Po osiągnięciu kroku zakończenia, aby przywrócić bazę danych dla jednorodnej kopii systemu, zamknij instalatora. Przywracanie bazy danych z kopii zapasowej hosta podstawowego. Wszystkie kolejne fazy instalacji zostały już wykonane na serwerze podstawowej bazy danych.

#### <a name="red-hat-firewall-rules-for-db2-hadr"></a>Reguły zapory Red Hat dla DB2 HADR
Dodaj reguły zapory, aby umożliwić ruch do bazy danych DB2 i między DB2 dla hadr do pracy:
+ Port komunikacji bazy danych. Jeśli używasz partycji, dodaj te porty.
+ Port HADR (wartość parametru DB2 HADR_LOCAL_SVC)
+ Port sondy platformy Azure
<pre><code>sudo firewall-cmd --add-port=&lt;port&gt;/tcp --permanent
sudo firewall-cmd --reload</code></pre>

#### <a name="ibm-db2-hadr-check"></a>Ibm Db2 HADR sprawdzić
W celach demonstracyjnych i procedur opisanych w tym artykule identyfikator SID bazy danych to **ID2**.

Po skonfigurowaniu programu HADR i stanie: PEER i CONNECTED w węzłach podstawowych i wstrzymanych wykonaj następujące czynności:

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
Database Member 0 -- Database ID2 -- Active -- Up 1 days 15:45:23 -- Date 2019-06-25-10.55.25.349375

                            <b>HADR_ROLE = PRIMARY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 1
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.076494 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 5
                   HEARTBEAT_EXPECTED = 52
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 5
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 369280
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 132242668
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 300
                      PEER_WINDOW_END = 06/25/2019 11:12:03.000000 (1561461123)
             READS_ON_STANDBY_ENABLED = N



#Secondary output:
Database Member 0 -- Database ID2 -- Standby -- Up 1 days 15:45:18 -- Date 2019-06-25-10.56.19.820474

                            <b>HADR_ROLE = STANDBY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 0
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.078116 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 0
                   HEARTBEAT_EXPECTED = 10
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 1
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 367360
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 0
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 1000
                      PEER_WINDOW_END = 06/25/2019 11:12:59.000000 (1561461179)
             READS_ON_STANDBY_ENABLED = N

</code></pre>



## <a name="db2-pacemaker-configuration"></a>Konfiguracja rozrusznika Db2

Korzystając z programu Pacemaker do automatycznego pracy awaryjnej w przypadku awarii węzła, należy odpowiednio skonfigurować wystąpienia Db2 i rozrusznik serca. W tej sekcji opisano ten typ konfiguracji.

Następujące elementy są poprzedzone albo:

- **[A]**: Dotyczy wszystkich węzłów
- **[1]**: Dotyczy tylko węzła 1 
- **[2]**: Dotyczy tylko węzła 2

**[A]** Warunek wstępny konfiguracji rozrusznika serca:
1. Zamknij oba serwery bazy danych\<z użytkownikiem db2 sid> z db2stop.
1. Zmień środowisko powłoki dla\<> użytkownika db2 sid na */bin/ksh*:
<pre><code># Install korn shell:
sudo yum install ksh
# Change users shell:
sudo usermod -s /bin/ksh db2&lt;sid&gt;</code></pre>
   

### <a name="pacemaker-configuration"></a>Konfiguracja rozrusznika serca

**[1]** Konfiguracja rozrusznika serca ibm Db2 HADR:
<pre><code># Put Pacemaker into maintenance mode
sudo pcs property set maintenance-mode=true 
</code></pre>

**[1]** Tworzenie zasobów IBM Db2:
<pre><code># Replace <b>bold strings</b> with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.
sudo pcs resource create Db2_HADR_<b>ID2</b> db2 instance='<b>db2id2</b>' dblist='<b>ID2</b>' master meta notify=true resource-stickiness=5000

#Configure resource stickiness and correct cluster notifications for master resoruce
sudo pcs resource update Db2_HADR_<b>ID2</b>-master meta notify=true resource-stickiness=5000

# Configure virtual IP - same as Azure Load Balancer IP
sudo pcs resource create vip_<b>db2id2</b>_<b>ID2</b> IPaddr2 ip='<b>10.100.0.40</b>'

# Configure probe port for Azure load Balancer
sudo pcs resource create nc_<b>db2id2</b>_<b>ID2</b> azure-lb port=<b>62500</b>

#Create a group for ip and Azure loadbalancer probe port
sudo pcs resource group add g_ipnc_<b>db2id2</b>_<b>ID2</b> vip_<b>db2id2</b>_<b>ID2</b> nc_<b>db2id2</b>_<b>ID2</b>

#Create colocation constrain - keep Db2 HADR Master and Group on same node
sudo pcs constraint colocation add g_ipnc_<b>db2id2</b>_<b>ID2</b> with master Db2_HADR_<b>ID2</b>-master

#Create start order constrain
sudo pcs constraint order promote Db2_HADR_<b>ID2</b>-master then g_ipnc_<b>db2id2</b>_<b>ID2</b>
</code></pre>

**[1]** Uruchamianie zasobów IBM Db2:
* Wyjmij rozrusznik serca z trybu konserwacji.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo pcs property set maintenance-mode=false</pre></code>

**[1]** Upewnij się, że stan klastra jest OK i że wszystkie zasoby są uruchamiane. Nie jest ważne, który węzeł zasoby są uruchomione na.
<pre><code>sudo pcs status</code>
2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Pełna lista zasobów:

 rsc_st_azure (stonith:fence_azure_arm): Rozpoczęto az-idb01 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2] Masters: [ az-idb01 ] Slaves: [ az-idb02 ] Grupa zasobów: g_ipnc_db2id2_ID2 vip_db2id2_ID2 (ocf::heartbeat:IPaddr2): Rozpoczęto az-idb01 nc_db2id2_ID2 (ocf::heartbeat:azure-lb): Uruchomiono az-idb01

Stan demona: corosync: aktywny/wyłączony rozrusznik serca: aktywny/wyłączony pcsd: aktywny/włączony
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

**[A]** Dodaj regułę zapory dla portu sondy:
<pre><code>sudo firewall-cmd --add-port=<b><probe-port></b>/tcp --permanent
sudo firewall-cmd --reload</code></pre>

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
 
1. Zaloguj się do podstawowego serwera aplikacji wystąpienia J2EE i wykonaj:
    
    <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>  
    
1. W lewej ramce wybierz **pozycję Magazyn zabezpieczeń**.
1. W prawej ramce wybierz `jdbc/pool/\<SAPSID>/url`klawisz .
1. Zmień nazwę hosta w adresie URL JDBC na nazwę hosta wirtualnego.
    
    <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>  
    
1. Wybierz pozycję **Dodaj**.
1. Aby zapisać zmiany, wybierz ikonę dysku w lewym górnym rogu.
1. Zamknij narzędzie konfiguracyjne.
1. Uruchom ponownie wystąpienie języka Java.

## <a name="configure-log-archiving-for-hadr-setup"></a>Konfigurowanie archiwizacji dzienników dla konfiguracji hadra
Aby skonfigurować archiwizację dziennika db2 dla konfiguracji HADR, zaleca się skonfigurowanie zarówno podstawowej, jak i rezerwowej bazy danych tak, aby miała możliwość automatycznego pobierania dziennika ze wszystkich lokalizacji archiwum dzienników. Zarówno podstawowa, jak i rezerwowana baza danych musi mieć możliwość pobierania plików archiwum dziennika ze wszystkich lokalizacji archiwum dziennika, do których jedno z wystąpień bazy danych może archiwizować pliki dziennika. 

Archiwizacja dziennika jest wykonywana tylko przez podstawową bazę danych. Jeśli zmienisz role HADR serwerów bazy danych lub wystąpi błąd, nowa podstawowa baza danych jest odpowiedzialna za archiwizację dziennika. Jeśli masz skonfigurowane wiele lokalizacji archiwum dzienników, dzienniki mogą być archiwizowane dwukrotnie. W przypadku lokalnego lub zdalnego nadrabiania zaległości może być również konieczności ręcznego skopiowania zarchiwizowanych dzienników ze starego serwera podstawowego do aktywnej lokalizacji dziennika nowego serwera podstawowego.

Zaleca się skonfigurowanie wspólnego udziału NFS lub GlusterFS, gdzie dzienniki są zapisywane z obu węzłów. Udział NFS lub GlusterFS musi być wysoce dostępny. 

Można użyć istniejących udziałów nfs o wysokiej dostępności lub GlusterFS dla transportów lub katalogu profilu. Aby uzyskać więcej informacji, zobacz:

- [System GlusterFS na maszynach wirtualnych platformy Azure z systemem Red Hat Enterprise Linux dla oprogramowania SAP NetWeaver][glusterfs] 
- [Wysoka dostępność dla sap NetWeaver na maszynach wirtualnych platformy Azure w systemie Red Hat Enterprise Linux z plikami NetApp azure dla aplikacji SAP][anf-rhel]
- [Usługi Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) (do tworzenia udziałów NFS)

## <a name="test-the-cluster-setup"></a>Testowanie konfiguracji klastra

W tej sekcji opisano, jak można przetestować konfigurację Db2 HADR. Każdy test zakłada, że ibm Db2 podstawowy jest uruchomiony na maszynie wirtualnej *az-idb01.* Użytkownik z uprawnieniami sudo lub root (nie zalecane) musi być używany.

Stan początkowy dla wszystkich przypadków testowych jest wyjaśniony tutaj: (crm_mon -r lub pcs)

- **pcs status** jest migawką stanu rozrusznika w czasie wykonywania 
- **crm_mon -r** jest ciągłą produkcją stanu rozrusznika serca

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
</code></pre>

Oryginalny stan w systemie SAP jest udokumentowany w przeglądzie > konfiguracji > transaction DBACOCKPIT, jak pokazano na poniższej ilustracji:

![DBACockpit - Przed migracją](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-org-rhel.png)




### <a name="test-takeover-of-ibm-db2"></a>Testowe przejęcie IBM Db2


> [!IMPORTANT] 
> Przed rozpoczęciem testu upewnij się, że:
> * Rozrusznik serca nie ma żadnych nieudanych akcji (stan pcs).
> * Brak ograniczeń lokalizacji (resztki testu migracji)
> * Synchronizacja IBM Db2 HADR działa. Skontaktuj się z\<użytkownikiem db2 sid> <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Migrowanie węzła z podstawową bazą danych Db2 przez wykonanie następującego polecenia:
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master</code></pre>

Po zakończeniu migracji dane wyjściowe stanu crm wyglądają następująco:
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

</code></pre>

Oryginalny stan w systemie SAP jest udokumentowany w przeglądzie > konfiguracji > transaction DBACOCKPIT, jak pokazano na poniższej ilustracji:

![DBACockpit - Po migracji](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-post-rhel.png)

Migracja zasobów z "przenoszeniem zasobów pcs" tworzy ograniczenia lokalizacji. Ograniczenia lokalizacji w tym przypadku uniemożliwiają uruchomienie wystąpienia IBM Db2 na az-idb01. Jeśli ograniczenia lokalizacji nie zostaną usunięte, zasób nie może wrócić awaryjnie.

Usuń ograniczenie lokalizacji i węzeł gotowości zostanie uruchomiony na az-idb01.
<pre><code>sudo pcs resource clear Db2_HADR_<b>ID2</b>-master</code></pre>
A stan klastra zmienia się na:
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

![DBACockpit - Usunięto ograniczenie lokalizacji](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-clear-rhel.png)


Migrowanie zasobu z powrotem do *az-idb01* i czyszczenie ograniczeń lokalizacji
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master az-idb01
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
</code></pre>

- **res_name> \<przenoszenia <host>zasobów szt:** Tworzy ograniczenia lokalizacji i może powodować problemy z przejęciem
- **pcs resource \<clear res_name>**: Czyści ograniczenia lokalizacji
- **oczyszczanie zasobów \<pcs res_name>**: Usuwa wszystkie błędy zasobu

### <a name="test-a-manual-takeover"></a>Przetestuj ręczne przejęcie

Można przetestować ręczne przejęcie, zatrzymując usługę rozrusznika w węźle *az-idb01:*
<pre><code>systemctl stop pacemaker</code></pre>

status na *az-ibdb02*
<pre><code>2 nodes configured
5 resources configured

Node az-idb01: pending
Online: [ az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled</code></pre>

Po przejściu w stan failover można ponownie uruchomić usługę na *az-idb01*.
<pre><code>systemctl start  pacemaker</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Zabij proces Db2 w węźle, w który działa podstawowa baza danych HADR

<pre><code>#Kill main db2 process - db2sysc
[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 34598
</code></pre>

Wystąpienie Db2 zakończy się niepowodzeniem, a rozrusznik serca przeniesie węzeł główny i raport o następującym stanie:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=49, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 09:57:35 2019', queued=0ms, exec=362ms</code></pre>

Rozrusznik serca uruchomi ponownie wystąpienie podstawowej bazy danych Db2 w tym samym węźle lub przejdzie w stan fail over do węzła, w który jest uruchomiony pomocnicze wystąpienie bazy danych i zgłaszany jest błąd.

### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Zabij proces Db2 w węźle, który uruchamia wystąpienie pomocniczej bazy danych

<pre><code>[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2id2    23144  23142  2 09:53 ?        00:00:13 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 23144
</code></pre>

Węzeł dostaje się do nie powiodło się i zgłoszono błąd
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Failed Actions:
* Db2_HADR_ID2_monitor_20000 on az-idb02 'not running' (7): call=144, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 10:02:09 2019', queued=0ms, exec=0ms</code></pre>

Wystąpienie Db2 zostanie ponownie uruchomione w roli pomocniczej, którą wcześniej przypisała.

### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Zatrzymaj bazę danych za pomocą db2stop force w węźle, który uruchamia wystąpienie podstawowej bazy danych HADR

Jako użytkownik db2\<sid> wykonać polecenie db2stop force:
<pre><code>az-idb01:db2ptr> db2stop force</code></pre>

Wykryto błąd:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Slaves: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Stopped
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Stopped

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</code></pre>

Db2 HADR pomocniczej bazy danych wystąpienie został awansowany do roli podstawowej.
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</pre></code>


### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Awaria maszyny Wirtualnej, która uruchamia wystąpienie podstawowej bazy danych HADR z "halt"

<pre><code>#Linux kernel panic. 
sudo echo b > /proc/sysrq-trigger</code></pre>

W takim przypadku program Pacemaker wykryje, że węzeł, w który jest uruchomiony wystąpienie podstawowej bazy danych nie odpowiada.

<pre><code>2 nodes configured
5 resources configured

Node az-idb01: UNCLEAN (online)
Online: [ az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01</code></pre>

Następnym krokiem jest sprawdzenie sytuacji *w mózgu Split.* Po węźle zachowane ustali, że węzeł, który ostatnio uruchomiono wystąpienie podstawowej bazy danych jest w dół, pracy awaryjnej zasobów jest wykonywana.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb02 ]
OFFLINE: [ az-idb01 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02 </code></pre>


W przypadku paniki jądra, nieudany węzeł zostanie zatrzymany przez agenta szermierki. Po powrocie węzła, który uległ awarii, należy uruchomić klaster rozrusznika serca
<pre><code>sudo pcs cluster start</code></pre> uruchamia wystąpienie Db2 do roli pomocniczej.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

## <a name="next-steps"></a>Następne kroki
- [Architektura i scenariusze wysokiej dostępności dla SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Konfigurowanie rozrusznika serca w systemie Red Hat Enterprise Linux na platformie Azure][rhel-pcs-azr]
