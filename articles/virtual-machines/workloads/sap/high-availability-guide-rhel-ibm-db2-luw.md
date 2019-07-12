---
title: Konfigurowanie programu IBM Db2 HADR na maszynach wirtualnych Azure (VM) na RHEL | Dokumentacja firmy Microsoft
description: Ustanów wysoka dostępność programu IBM Db2 LUW na maszynach wirtualnych Azure (maszyny wirtualne) systemu RHEL.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/10/2019
ms.author: juergent
ms.openlocfilehash: b26a66eaee3a107c37d64541ec6b832331a3e2c9
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812131"
---
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2233094]: https://launchpad.support.sap.com/#/notes/2233094
[1612105]: https://launchpad.support.sap.com/#/notes/1612105
[2694118]: https://launchpad.support.sap.com/#/notes/2694118


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
[anf-rhel]:(https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-red-hat-enterprise-linux-server"></a>Wysoka dostępność programu IBM Db2 LUW na maszynach wirtualnych platformy Azure w systemie Red Hat Enterprise Linux Server

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
| [2002167] | Red Hat Enterprise Linux 7.x: Instalowanie i uaktualnianie |
| [2694118] | Dodatek wysokiej dostępności programu Red Hat Enterprise Linux na platformie Azure |
| [1999351] | Rozwiązywanie problemów z rozszerzonego monitorowania dla rozwiązania SAP platformy Azure |
| [2233094] | DB6: Aplikacje SAP na platformie Azure, które używają programu IBM Db2 dla systemów Linux, UNIX i Windows — informacje dodatkowe |
| [1612105] | DB6: Często zadawane pytania dotyczące bazy danych Db2 z HADR |


| Dokumentacja | 
| --- |
| [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Zawiera wszystkie wymagane informacje o SAP dla systemu Linux |
| [Azure maszyny wirtualne, planowania i implementacji dla rozwiązania SAP w systemie Linux][planning-guide] przewodnik |
| [Wdrażania maszyn wirtualnych platformy Azure dla rozwiązania SAP w systemie Linux][deployment-guide] (w tym artykule) |
| [Usługa Azure Virtual Machines wdrożenie bazy danych zarządzania system(DBMS) dla rozwiązania SAP w systemie Linux][dbms-guide] przewodnik |
| [Obciążeń SAP na Azure Lista kontrolna dotycząca planowania i wdrażania][azr-sap-plancheck] |
| [Omówienie dodatek wysokiej dostępności dla programu Red Hat Enterprise Linux 7][rhel-ha-addon] |
| [Administracja dodatek wysokiej dostępności][rhel-ha-admin] |
| [Dokumentacja dodatek wysokiej dostępności][rhel-ha-ref] |
| [Zasady pomocy technicznej w przypadku klastrów o wysokiej dostępności systemu RHEL — maszyny wirtualne platformy Microsoft Azure jako elementów członkowskich klastra][rhel-azr-supp]
| [Instalowanie i konfigurowanie Red Hat Enterprise Linux 7.4 (lub nowszy) o wysokiej dostępności klastra w systemie Microsoft Azure][rhel-azr-inst]
| [Wdrażania systemu DBMS na IBM Db2 Azure maszyn wirtualnych w przypadku obciążeń SAP][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR 10.5][db2-hadr-10.5] |
| [Zasady pomocy technicznej w przypadku klastrów o wysokiej dostępności systemu RHEL — Zarządzanie programu IBM Db2 dla systemów Linux, Unix i Windows w klastrze][rhel-db2-supp]



## <a name="overview"></a>Omówienie
Aby osiągnąć wysoką dostępność, IBM Db2 LUW z HADR jest zainstalowany na co najmniej dwóch maszyn wirtualnych platformy Azure, które są wdrażane w [zestawu dostępności platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) lub wielu [strefy dostępności platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones). 

Następujące grafiki wyświetlić instalacji serwera bazy danych w dwóch maszyn wirtualnych platformy Azure. Zarówno serwer bazy danych maszyn wirtualnych platformy Azure ma swoje własne magazynie dołączonym i są gotowe do działania. W HADR jedno wystąpienie bazy danych w jednej z maszyn wirtualnych platformy Azure, ma rolę podstawowego wystąpienia. Wszyscy klienci są podłączone do podstawowego wystąpienia. Wszystkie zmiany w transakcji bazy danych są zachowywane lokalnie w dzienniku transakcji bazy danych Db2. Jak rekordy dziennika transakcji są zachowywane lokalnie, rekordy są przesyłane za pośrednictwem protokołu TCP/IP z wystąpieniem bazy danych na drugi serwer bazy danych, rezerwowy serwer lub wystąpienie w trybie gotowości. Rezerwowe wystąpienie aktualizuje lokalnej bazy danych przez stopniowe do przodu przeniesionych transakcji rekordów dziennika. W ten sposób rezerwowy serwer jest zsynchronizowany z serwerem podstawowym.

HADR jest tylko funkcje replikacji. W nim nie wykrywanie awarii i nie automatycznego urządzeń przejęcia lub pracy awaryjnej. Przejęcie lub przeniesienie do rezerwowy serwer musi być inicjowana ręcznie przez administratora bazy danych. Aby osiągnąć automatycznego przejęcia i wykrywanie awarii, można użyć funkcję klastrowania program Pacemaker w systemie Linux. Program pacemaker monitorowanie wystąpienia serwera dwie bazy danych. Jeśli wystąpienie serwera podstawowej bazy danych ulegnie awarii, inicjuje program Pacemaker *automatyczne* HADR przejęcia przez serwer rezerwy dynamicznej. Program pacemaker gwarantuje również, że wirtualny adres IP jest przypisywany z nowym serwerem podstawowym.

![Omówienie wysokiej dostępności programu IBM Db2](./media/high-availability-guide-rhel-ibm-db2-luw/ha-db2-hadr-lb-rhel.png)

Zapewnienie SAP aplikacji serwerów połączyć się z podstawowej bazy danych, należy nazwę hosta wirtualnego i wirtualnego adresu IP. Zdarzenia na pracę awaryjną serwery aplikacji SAP połączy się nowe wystąpienie podstawowej bazy danych. W środowisku platformy Azure [usługa Azure load balancer](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) jest wymagane do używania wirtualnego adresu IP w taki sposób, który jest wymagany dla HADR IBM Db2. 

Aby pomóc Ci lepiej zrozumieć, jak IBM Db2 LUW HADR i program Pacemaker dopasowuje się do wysokiej dostępności konfiguracji systemu SAP, Poniższa ilustracja przedstawia omówienie konfiguracji charakteryzującej systemu SAP, na podstawie bazy danych programu IBM Db2. Ten artykuł dotyczy tylko programu IBM Db2, ale udostępnia odwołania do innych artykułów na temat sposobu konfigurowania innych składników systemu SAP.

![Omówienie pełnego środowiska wysokiej dostępności programu IBM DB2](./media/high-availability-guide-rhel-ibm-db2-luw/end-2-end-ha-rhel.png)


### <a name="high-level-overview-of-the-required-steps"></a>Ogólne omówienie wymaganych czynności
Można wdrożyć konfiguracji programu IBM Db2, należy wykonać następujące kroki:

  + Planowanie środowiska.
  + Wdrażanie maszyn wirtualnych.
  + Zaktualizuj RHEL Linux i konfigurowanie systemów plików.
  + Zainstaluj i skonfiguruj program Pacemaker.
  + Instalator [klastra glusterfs][glusterfs] or [Azure NetApp Files][anf-rhel]
  + Zainstaluj [ASCS/Wywołujących w osobnym klastrze][ascs-ha-rhel].
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
| Preferowane platformy Azure | Nie będzie mógł metodę, aby uniknąć sytuacji mózg podziału. |
| Azure Load Balancer | Użycie Basic lub Standard (zalecane), port dla bazy danych Db2 (zalecamy 62500) sondy **port sondy**. |
| Rozpoznawanie nazw| Jak rozpoznawanie nazw działa w środowisku. Zdecydowanie zaleca się usługi DNS. Można użyć pliku hostów lokalnych. |
    
Aby uzyskać więcej informacji na temat program Pacemaker systemu Linux, na platformie Azure, zobacz [konfigurowania program Pacemaker w systemie Red Hat Enterprise Linux na platformie Azure][rhel-pcs-azr].

## <a name="deployment-on-red-hat-enterprise-linux"></a>Wdrażanie w systemie Red Hat Enterprise Linux

Agent zasobów dla programu IBM Db2 LUW znajduje się w systemie Red Hat Enterprise Linux serwera zaświadczanie o kondycji dodatek. Dla ustawienia opisane w niniejszym dokumencie należy użyć Red Hat Enterprise Linux dla rozwiązania SAP. W portalu Azure Marketplace zawiera obraz Red Hat Enterprise Linux 7.4 SAP lub wyższej używanego do wdrażania nowych maszyn wirtualnych platformy Azure. Należy pamiętać o różnych modeli pomocy technicznej lub usługi, które są oferowane przez firmy Red Hat w portalu Azure Marketplace, w przypadku wybrania obrazu maszyny Wirtualnej w witrynie Azure Marketplace do maszyny Wirtualnej.

### <a name="hosts-dns-updates"></a>Hosts: Aktualizacje rekordów DNS
Sporządź listę wszystkich nazw hostów, w tym nazwy hostów wirtualnych i zaktualizuj serwery DNS, aby umożliwić właściwy adres IP do rozpoznawania nazwy hosta. Jeśli serwer DNS nie istnieje lub nie można zaktualizować i utworzenie wpisów DNS, należy użyć plików hosta lokalnego, poszczególnych maszyn wirtualnych, które uczestniczą w tym scenariuszu. Jeśli używasz wpisy plików hosta, upewnij się, że wpisy są stosowane do wszystkich maszyn wirtualnych w środowisku systemu SAP. Jednak zaleca się korzystania z usługi serwera DNS, który najlepiej, jeśli rozciąga się na platformie Azure


### <a name="manual-deployment"></a>Ręczne wdrażanie

Upewnij się, że wybranego systemu operacyjnego jest obsługiwana przez oprogramowanie firmy IBM/SAP dla IBM Db2 LUW. Lista obsługiwanych wersji systemu operacyjnego dla maszyn wirtualnych platformy Azure i bazy danych Db2 wersjami jest dostępna w Uwaga SAP [1928533]. Lista wersji systemu operacyjnego w przypadku poszczególnych wersji bazy danych Db2 jest dostępna w macierzy dostępności produktów SAP. Zdecydowanie zaleca się co najmniej Red Hat Enterprise Linux 7.4 dla rozwiązania SAP ze względu na ulepszenia wydajności związane z platformy Azure, w tym lub nowszej wersje Red Hat Enterprise Linux.

1. Utwórz lub wybierz grupę zasobów.
1. Utwórz lub wybierz sieć wirtualną i podsieć.
1. Tworzenie zestawu dostępności platformy Azure można też wdrażać strefie dostępności.
    + Dla zestawu dostępności ustawione domenach aktualizacji maksymalną na 2.
1. Tworzenie maszyny wirtualnej 1.
    + Red Hat Enterprise Linux na użytek SAP obrazów w portalu Azure Marketplace.
    + Wybierz zestaw dostępności platformy Azure, który został utworzony w kroku 3, lub wybierz strefę dostępności.
1.  Tworzenie maszyny wirtualnej 2.
    + Red Hat Enterprise Linux na użytek SAP obrazów w portalu Azure Marketplace.
    + Wybierz zestaw dostępności platformy Azure w utworzonego w kroku 3, lub wybierz strefę dostępności (nie tej samej strefie tak jak w kroku 3).
1. Dodawanie dysków danych do maszyn wirtualnych, a następnie sprawdź zalecenia konfiguracji systemu plików, w artykule [wdrażania systemu DBMS na IBM Db2 Azure maszyn wirtualnych w przypadku obciążeń SAP][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>Tworzenie klastra program Pacemaker
    
Aby utworzyć podstawowy klaster program Pacemaker dla tego serwera programu IBM Db2, zobacz [konfigurowania program Pacemaker w systemie Red Hat Enterprise Linux na platformie Azure][rhel-pcs-azr]. 

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

#### <a name="red-hat-firewall-rules"></a>Reguły zapory firmy Red Hat
Red Hat Enterprise Linux ma domyślnie włączoną zaporę. 

<pre><code>#Allow access to SWPM tool. Rule is not permanent.
sudo firewall-cmd --add-port=4237/tcp</code></pre>

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Instalacja wskazówki dotyczące konfigurowania IBM Db2 LUW z HADR

Aby skonfigurować podstawowe wystąpienie bazy danych IBM Db2 LUW:

- Korzystając z wysokiej dostępności lub rozproszonej opcji.
- Zainstaluj wystąpienie SAP ASCS/Wywołujących i bazy danych.
- Tworzenie kopii zapasowej bazy danych nowo zainstalowany.

> [!IMPORTANT] 
> Zanotuj "bazy danych portowi komunikacyjnemu" ustawioną podczas instalacji. Musi ona tego samego numeru portu dla obu wystąpień bazy danych.
>![Definicja portu SWPM SAP](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-swpm-db2-port.png)

### <a name="ibm-db2-hadr-settings-for-azure"></a>Ustawienia programu IBM Db2 HADR dla platformy Azure

   Gdy używasz agenta preferowane program Pacemaker platformy Azure, należy ustawić następujące parametry:

   - Czas trwania okna elementu równorzędnego HADR (w sekundach) (HADR_PEER_WINDOW) = 240  
   - Wartość limitu czasu HADR (HADR_TIMEOUT) = 45

Firma Microsoft zaleca poprzedniego parametrów, na podstawie w początkowej fazie testowania trybu failover/przejęcie. Jest to konieczne, test do prawidłowego działania funkcji trybu failover i przejęcia przy użyciu tych ustawień parametrów. Ponieważ poszczególne konfiguracje mogą się różnić, parametry mogą wymagać dostosowania. 

> [!NOTE]
> Specyficzne dla programu IBM Db2 z konfiguracją HADR przy użyciu normalnego uruchamiania: Wystąpienie bazy danych w dodatkowej lub wstrzymania musi być uruchomiona, przed rozpoczęciem wystąpienia podstawowej bazy danych.

   
> [!NOTE]
> W przypadku instalacji i konfiguracji, które są specyficzne dla platformy Azure i program Pacemaker: W trakcie procedury instalacji za pośrednictwem Menedżera inicjowania obsługi oprogramowania SAP jest jawne pytanie o wysokiej dostępności dla programu IBM Db2 LUW:
>+ Nie należy wybierać **pureScale IBM Db2**.
>+ Nie należy wybierać **zainstalować IBM Tivoli systemu automatyzacji Multiplatforms**.
>+ Nie należy wybierać **Generowanie plików konfiguracji klastra**.
>![SAP SWPM — opcje bazy danych DB2 zaświadczanie o kondycji](./media/high-availability-guide-rhel-ibm-db2-luw/swpm-db2ha-opt.png)


Aby skonfigurować serwer bazy danych w stanie wstrzymania, za pomocą procedury kopiowania jednorodny system SAP, wykonaj następujące kroki:

1. Wybierz **kopia systemu** opcja > **systemach** > **rozproszonych** > **wystąpienie bazy danych**.
1. Jako metodę kopiowania, wybierz **jednorodny System** tak, aby kopia zapasowa służy do przywrócenia kopii zapasowej w wystąpieniu serwera rezerwy dynamicznej.
1. Po przejściu do kroku wyjścia, aby przywrócić bazę danych związanym z kopiowaniem jednorodny system Zamknij Instalatora. Przywróć bazę danych z kopii zapasowej hostem głównym. Wszystkie fazy kolejnej instalacji zostały już wykonane na serwerze podstawowej bazy danych.

#### <a name="red-hat-firewall-rules-for-db2-hadr"></a>Red Hat reguł zapory dla bazy danych DB2 HADR
Dodawanie reguły zapory zezwalające na ruch do bazy danych DB2 i między bazy danych DB2 for HADR pracę:
+ Port komunikacyjny bazy danych. Jeśli używasz partycji, zbyt Dodaj te porty.
+ Port HADR (wartość parametru bazy danych DB2 HADR_LOCAL_SVC)
+ Port sondy platformy Azure
<pre><code>sudo firewall-cmd --add-port=&lt;port&gt;/tcp --permanent
sudo firewall-cmd --reload</code></pre>

#### <a name="ibm-db2-hadr-check"></a>IBM Db2 HADR wyboru
Dla celów demonstracyjnych i procedur opisanych w tym artykule, baza danych jest identyfikator SID **ID2**.

Po skonfigurowaniu HADR i stanu elementów RÓWNORZĘDNYCH i POŁĄCZONO w węzłach podstawowego i będącymi w gotowości, sprawdzić następujące:

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



## <a name="db2-pacemaker-configuration"></a>Program Pacemaker bazy danych Db2 konfiguracji

Gdy używasz program Pacemaker do automatycznej pracy awaryjnej w przypadku awarii węzła, należy odpowiednio skonfigurować wystąpienia bazy danych Db2 i program Pacemaker. W tej sekcji opisano tego typu konfiguracją.

Następujące elementy mają prefiks albo:

- **[A]** : Ma zastosowanie do wszystkich węzłów
- **[1]** : Dotyczy tylko węzeł 1 
- **[2]** : Dotyczy tylko węzeł 2

**[A]**  Wymagania wstępne dotyczące konfiguracji program Pacemaker:
1. Zamknij oba serwery bazy danych z bazy danych db2 użytkownika\<sid > z db2stop.
1. Zmień środowisko powłoki dla bazy danych db2\<sid > użytkownikowi */bin/ksh*:
<pre><code># Install korn shell:
sudo yum install ksh
# Change users shell:
sudo usermod -s /bin/ksh db2&lt;sid&gt;</code></pre>
   

### <a name="pacemaker-configuration"></a>Program pacemaker konfiguracji

**[1]**  Program IBM Db2 specyficzne dla HADR Pacemaker konfiguracji:
<pre><code># Put Pacemaker into maintenance mode
sudo pcs property set maintenance-mode=true 
</code></pre>

**[1]**  Tworzenie IBM Db2 zasobów:
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

**[1]**  Start IBM Db2 zasobów:
* Umieść program Pacemaker w trybie konserwacji.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo pcs property set maintenance-mode=false</pre></code>

**[1]**  Upewnij się, że kondycja klastra jest OK i wszystkie zasoby są uruchamiane. Nie jest ważna, który węzeł zasoby są uruchomione na.
<pre><code>sudo pcs status</code>
2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Pełną listę zasobów:

 rsc_st_azure (stonith:fence_azure_arm):      Rozpoczęto idb01 az Set główny/podrzędny: Wzorce Db2_HADR_ID2 — wzorzec [Db2_HADR_ID2]: [az-idb01] w elementy podrzędne: Grupa zasobów [az-idb02]: g_ipnc_db2id2_ID2 vip_db2id2_ID2 (ocf::heartbeat:IPaddr2):       Pracę nc_db2id2_ID2 az idb01 (ocf::heartbeat:azure-lb):      Rozpoczęto idb01 az

Stanu demona: corosync: aktywne/wyłączony program pacemaker: pcsd active/wyłączone: aktywne lub nie włączono
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

**[A]**  Dodaj regułę zapory na porcie sondowania:
<pre><code>sudo firewall-cmd --add-port=<b><probe-port></b>/tcp --permanent
sudo firewall-cmd --reload</code></pre>

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

Zalecamy Konfigurowanie typowych udziału NFS lub GlusterFS, w których dzienniki są zapisywane z obu węzłów. Udziału NFS lub GlusterFS musi być o wysokiej dostępności. 

Można użyć istniejących udziałów NFS o wysokiej dostępności lub GlusterFS dla transportów lub katalogu profilu. Aby uzyskać więcej informacji, zobacz:

- [System GlusterFS na maszynach wirtualnych platformy Azure z systemem Red Hat Enterprise Linux dla oprogramowania SAP NetWeaver][glusterfs] 
- [Wysoka dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure w systemie Red Hat Enterprise Linux przy użyciu usługi Azure Files NetApp dla aplikacji SAP][anf-rhel]
- [Usługa Azure Files NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) (do tworzenia udziałów NFS)

## <a name="test-the-cluster-setup"></a>Testuj ustawienia klastra

W tej sekcji opisano, jak można przetestować konfigurację HADR bazy danych Db2. Każde badanie przyjęto założenie, IBM Db2 podstawowy jest uruchomiona na *az idb01* maszyny wirtualnej. Użytkownik z uprawnieniami sudo lub katalogu głównego (niezalecane) może być używany.

Początkowy stan dla wszystkich przypadków testowych zostało wyjaśnione w tym miejscu: (crm_mon - r lub komputerów status)

- **Stan komputerów** jest migawką stanu program Pacemaker w czasie wykonywania 
- **crm_mon - r** ciągłe dane wyjściowe stanu program Pacemaker

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

Oryginalny stan w systemie SAP jest udokumentowany w transakcji DBACOCKPIT > Konfiguracja > Przegląd, jak pokazano na poniższej ilustracji:

![DBACockpit — sprzed migracji](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-org-rhel.png)




### <a name="test-takeover-of-ibm-db2"></a>Przejęcie test programu IBM Db2


> [!IMPORTANT] 
> Przed rozpoczęciem testu, upewnij się, że:
> * Program pacemaker nie ma żadnych akcji nie powiodło się (stan komputerów).
> * Istnieją nie ograniczenia lokalizacji (resztek testu migracji)
> * Synchronizacja programu IBM Db2 HADR pracuje. Skontaktuj się z użytkownika bazy danych db2\<sid > <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Migrowanie węzła, który jest uruchomiony podstawowej bazy danych Db2, wykonując następujące polecenie:
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master</code></pre>

Po zakończeniu migracji, dane wyjściowe stanu crm wygląda następująco:
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

Oryginalny stan w systemie SAP jest udokumentowany w transakcji DBACOCKPIT > Konfiguracja > Przegląd, jak pokazano na poniższej ilustracji:

![DBACockpit — po migracji](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-post-rhel.png)

Proces migracji zasobów z "przenoszenia zasobów komputerów" tworzy ograniczenia lokalizacji. Ograniczenia lokalizacji w tym przypadku nie pozwalają na uruchomione wystąpienie programu IBM Db2 w az idb01. Ograniczenia lokalizacji nie są usuwane, zasób nie może wykonać powrotu po awarii.

Usuń ograniczenie lokalizacji i węzeł wstrzymania zostanie uruchomiony na az idb01.
<pre><code>sudo pcs resource clear Db2_HADR_<b>ID2</b>-master</code></pre>
I stan klastra zmieni się na:
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

![Ograniczenie lokalizacji usuniętych DBACockpit-](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-clear-rhel.png)


Migrowanie zasobów do *az idb01* i wyczyść ograniczenia lokalizacji
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master az-idb01
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
</code></pre>

- **Przenoszenie zasobów komputerów \<res_name > <host>:** Tworzy ograniczenia lokalizacji i przejęcia mogą powodować problemy
- **Wyczyść zasobów komputerów \<res_name >** : Usuwa ograniczenia lokalizacji
- **czyszczenie zasobów komputerów \<res_name >** : Czyści wszystkie błędy zasobu

### <a name="test-a-manual-takeover"></a>Testowanie ręczne przejęcie

Możesz przetestować ręczne przejęcia przez zatrzymanie usługi program Pacemaker w *az idb01* węzła:
<pre><code>systemctl stop pacemaker</code></pre>

Stan *az ibdb02*
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

Po włączeniu trybu failover, możesz uruchomić usługę ponownie na *az idb01*.
<pre><code>systemctl start  pacemaker</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Kasowanie procesu bazy danych Db2 w węźle, który uruchamia HADR podstawowej bazy danych

<pre><code>#Kill main db2 process - db2sysc
[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 34598
</code></pre>

Wystąpienie bazy danych Db2 zamierza się nie powieść, a program Pacemaker spowoduje przeniesienie węzła głównego i raportu po stanu:

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

Program pacemaker spowoduje ponowne uruchomienie wystąpienia podstawowej bazy danych Db2 w tym samym węźle lub za pośrednictwem zakończy się niepowodzeniem na węzeł, na którym jest uruchomione wystąpienie pomocniczej bazy danych i zwróci błąd.

### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Kasowanie procesu bazy danych Db2 w węźle, który uruchamia wystąpienie pomocniczej bazy danych

<pre><code>[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2id2    23144  23142  2 09:53 ?        00:00:13 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 23144
</code></pre>

Węzeł pobiera into nie powiodła się podane i zgłoszony błąd
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

Wystąpienie bazy danych Db2 pobiera ponownie uruchomiony w roli dodatkowej, który miał on przypisany przed.

### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Zatrzymaj bazy danych za pośrednictwem db2stop życie w węźle, który uruchamia wystąpienie HADR podstawowej bazy danych

Jako użytkownika bazy danych db2\<sid > wykonaj polecenie db2stop życie:
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

Wystąpienia pomocniczej bazy danych Db2 HADR stało się podwyższyć do roli podstawowej.
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


### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Awarii maszyny Wirtualnej, która uruchamia wystąpienie HADR podstawowej bazy danych za pomocą "zatrzymania"

<pre><code>#Linux kernel panic. 
sudo echo b > /proc/sysrq-trigger</code></pre>

W takim przypadku program Pacemaker wykryje, że węzeł, na którym jest uruchomione wystąpienie podstawowej bazy danych nie odpowiada.

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

następnym krokiem jest sprawdzenie *Podziel mózg* sytuacji. Po działającego węzła stwierdził, że węzeł, który ostatnio uruchomiono wystąpienie podstawowej bazy danych nie działa, pracy w trybie failover zasobów jest wykonywana.

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


W przypadku jądra nie powiodło się węzeł będzie restared przez agenta preferowane. Po powrocie do trybu online węzła nie powiodło się, należy uruchomić program pacemaker klastra przez
<pre><code>sudo pcs cluster start</code></pre> uruchamia wystąpienie bazy danych Db2 w pełni rolę pomocniczej.

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
- [Architektura wysokiej dostępności i scenariusze SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Konfigurowanie program Pacemaker w systemie Red Hat Enterprise Linux na platformie Azure][rhel-pcs-azr]
