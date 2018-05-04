---
title: Replikowanie wielowarstwowej aplikacji programu SharePoint przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób replikowania wielowarstwowej aplikacji programu SharePoint przy użyciu możliwości usługi Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: sujayt
manager: rochakm
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.author: sutalasi
ms.openlocfilehash: 48a8f6d35c3ea1266880f05d12ebf4efd855df41
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="replicate-a-multi-tier-sharepoint-application-for-disaster-recovery-using-azure-site-recovery"></a>Replikowanie wielowarstwowej aplikacji programu SharePoint przy użyciu usługi Azure Site Recovery odzyskiwania po awarii

W tym artykule opisano szczegółowo sposób chronić aplikacji programu SharePoint za pomocą [usługi Azure Site Recovery](site-recovery-overview.md).


## <a name="overview"></a>Przegląd

Microsoft SharePoint jest zaawansowanych aplikacji, które mogą ułatwić grupy lub działu organizowanie, współpracę i udostępnianie informacji. SharePoint zapewniają portali sieci intranet, Zarządzanie dokumentami i plikami, współpracy, sieci społecznościowych, sieci ekstranet, witryn sieci Web, wyszukiwania enterprise i business intelligence. Ma również integracji systemu, integracji procesów i możliwości automatyzacji przepływu pracy. Zazwyczaj organizacje uważają, że jako aplikacja warstwy 1 wrażliwe na czas przestoju i utraty danych.

Już dziś Microsoft SharePoint nie zawiera żadnych funkcji odzyskiwania po awarii poza pole. Niezależnie od typu i skali awarii odzyskiwanie obejmuje korzystanie z centrum danych wstrzymania, które można odzyskać do farmy. Centra danych wstrzymania są wymagane dla scenariuszy, w którym lokalnego nadmiarowych systemów i kopii zapasowych nie można odzyskać z przestoju w centrum danych podstawowych.

Rozwiązanie odzyskiwania po awarii dobrej powinien umożliwić modelowania planów odzyskiwania wokół architekturach aplikacji złożonych, takich jak SharePoint. Powinien on także możliwość dodawania niestandardowych kroki w celu obsługi aplikacji mapowań między różnych warstw i dlatego udostępnianie trybu failover z jednym kliknięciem w dolnym RTO w przypadku awarii.

W tym artykule opisano szczegółowo sposób chronić aplikacji programu SharePoint za pomocą [usługi Azure Site Recovery](site-recovery-overview.md). W tym artykule opisano najlepsze rozwiązania dotyczące replikacji trzy warstwy aplikacji programu SharePoint na platformie Azure, jak wyszczególniania odzyskiwania po awarii oraz sposób pracy awaryjnej aplikacji na platformie Azure.

Możesz obserwować poniżej wideo o odzyskiwaniu multi warstwy aplikacji na platformie Azure.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/Disaster-Recovery-of-load-balanced-multi-tier-applications-using-Azure-Site-Recovery/player]


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że należy zrozumieć następujące kwestie:

1. [Replikacja maszyny wirtualnej na platformie Azure](site-recovery-vmware-to-azure.md)
2. Jak [projektowania sieci odzyskiwania](site-recovery-network-design.md)
3. [Ten test trybu failover na platformie Azure](site-recovery-test-failover-to-azure.md)
4. [Podczas pracy w trybie failover na platformie Azure](site-recovery-failover.md)
5. Jak [replikacji kontrolera domeny](site-recovery-active-directory.md)
6. Jak [replikacji programu SQL Server](site-recovery-sql.md)

## <a name="sharepoint-architecture"></a>Architektura programu SharePoint

Programu SharePoint można wdrożyć na jeden lub więcej serwerów przy użyciu topologii warstwowych i ról serwera do implementowania projektu farmy, spełniającej konkretne cele i cele. Typowy dużych, wysokiej jakości farmy programu SharePoint server obsługującego dużej liczby równoczesnych użytkowników i dużą liczbę elementów zawartości Użyj grupowania usługi w ramach strategii ich skalowalności. Ta metoda polega na uruchamianie usług na dedykowanych serwerach, grupowanie tych usług, a następnie skalowanie w poziomie serwery w grupie. Następujące topologii zilustrowano usługi i grupowanie trzy warstwy farmy serwerów programu SharePoint server. Zobacz dokumentację programu SharePoint i architektury linii produktu szczegółowe wskazówki dotyczące różnych topologiach programu SharePoint. Można znaleźć więcej szczegółów na temat wdrażania programu SharePoint 2013 w [tego dokumentu](https://technet.microsoft.com/library/cc303422.aspx).



![Wzorzec wdrożenia 1](./media/site-recovery-sharepoint/sharepointarch.png)


## <a name="site-recovery-support"></a>Obsługa odzyskiwania lokacji

Do tworzenia w tym artykule, użyto maszyn wirtualnych VMware z systemem Windows Server 2012 R2 Enterprise. Użyto programu SharePoint 2013 Enterprise edition i programu SQL server 2014 Enterprise edition. Zgodnie z replikacji usługi Site Recovery jest niezależny od aplikacji, do przechowywania następujących scenariuszach również powinny zalecenia zawarte w tym miejscu.

### <a name="source-and-target"></a>Źródłowa i docelowa

**Scenariusz** | **Do lokacji dodatkowej** | **Platforma Azure**
--- | --- | ---
**Funkcja Hyper-V** | Yes | Yes
**VMware** | Yes | Yes
**Serwer fizyczny** | Yes | Yes
**Azure** | Nie dotyczy | Yes

### <a name="sharepoint-versions"></a>Wersje programu SharePoint
Obsługiwane są następujące wersje serwera programu SharePoint.

* Program SharePoint server 2013 standardowe
* SharePoint server 2013 Enterprise
* Serwer programu SharePoint 2016 standardowe
* Program SharePoint server 2016 Enterprise

### <a name="things-to-keep-in-mind"></a>Warto pamiętać o

Jeśli używasz udostępniony klaster opartej na dysku jako wszystkie warstwy aplikacji następnie nie będzie można używać usługi Site Recovery replikacji replikacja tych maszyn wirtualnych. Można przy użyciu replikacji natywnego udostępniany przez aplikację, a następnie użyć [planu odzyskiwania](site-recovery-create-recovery-plans.md) warstwy z informacjami na tryb failover wszystkich.

## <a name="replicating-virtual-machines"></a>Replikowanie maszyn wirtualnych

Postępuj zgodnie z [w tych wskazówkach](site-recovery-vmware-to-azure.md) do rozpoczęcia replikacji maszyny wirtualnej na platformie Azure.

* Po zakończeniu replikacji, upewnij się, przejdź do każdej maszyny wirtualnej dla każdej warstwy i wybierz zestaw tej samej dostępności "elementu zreplikowane > Ustawienia > Właściwości > obliczenia i sieć". Na przykład jeśli warstwę web 3 maszyn wirtualnych, upewnij się, że wszystkie 3 maszyny wirtualne są skonfigurowane jako część tego samego zestawem dostępności w usłudze Azure.

    ![Set-Availability-Set](./media/site-recovery-sharepoint/select-av-set.png)

* Aby uzyskać wskazówki dotyczące ochrony usługi Active Directory i DNS, zapoznaj się [ochrony usługi Active Directory i DNS](site-recovery-active-directory.md) dokumentu.

* Aby uzyskać wskazówki dotyczące ochrony warstwy bazy danych uruchomiony na serwerze SQL server, zapoznaj się [ochrony programu SQL Server](site-recovery-active-directory.md) dokumentu.

## <a name="networking-configuration"></a>Konfiguracja sieci

### <a name="network-properties"></a>Właściwości sieci

* Dla aplikacji i maszyny wirtualne warstwy sieci Web należy skonfigurować ustawienia sieciowe w portalu Azure, aby maszyn wirtualnych uzyskać dołączona do prawej sieci DR po pracy awaryjnej.

    ![Wybierz sieć](./media/site-recovery-sharepoint/select-network.png)


* Jeśli używasz statycznego adresu IP, określ adres IP, który ma maszyny wirtualnej w **docelowy adres IP** pola

    ![Ustawianie statycznego adresu IP](./media/site-recovery-sharepoint/set-static-ip.png)

### <a name="dns-and-traffic-routing"></a>DNS i routingu ruchu

Dla witryn, internetowy [tworzenia profilu usługi Traffic Manager typu 'Priority'](../traffic-manager/traffic-manager-create-profile.md) w subskrypcji platformy Azure. A następnie skonfiguruj profil DNS i usługi Traffic Manager w następujący sposób.


| **Where** | **Źródło** | **docelowy**|
| --- | --- | --- |
| Publicznym systemie DNS | Publicznym systemie DNS dla witryny programu SharePoint <br/><br/> Przykład: sharepoint.contoso.com | Traffic Manager <br/><br/> contososharepoint.trafficmanager.net |
| DNS lokalnego | sharepointonprem.contoso.com | Publiczny adres IP w farmie lokalnej |


W profilu usługi Traffic Manager [utworzyć punkty końcowe podstawowymi i odzyskiwania](../traffic-manager/traffic-manager-configure-priority-routing-method.md). Użyj zewnętrznego punktu końcowego dla lokalnego punktu końcowego i publicznego adresu IP dla punktu końcowego platformy Azure. Upewnij się, że priorytet ma wartość wyższej do lokalnego punktu końcowego.

Host stronę testową na określonym porcie (np. 800) w warstwie sieci web programu SharePoint dla Menedżera ruchu można automatycznie wykryć dostępności post w tryb failover. W przypadku, gdy nie można włączyć uwierzytelnianie anonimowe dla poszczególnych witryn programu SharePoint jest obejście tego problemu.

[Skonfiguruj profil Menedżera ruchu](../traffic-manager/traffic-manager-configure-priority-routing-method.md) z poniższych ustawień.

* Metoda routingu - 'Priority'
* Czas wygaśnięcia (TTL) - 30 sekund DNS
* Ustawienia monitora punktu końcowego — Jeśli zostanie włączone uwierzytelnianie anonimowe można nadać punktu końcowego określonej witryny sieci Web. Lub za pomocą strony testu na określonym porcie (np. 800).

## <a name="creating-a-recovery-plan"></a>Tworzenie planu odzyskiwania

Plan odzyskiwania umożliwia sekwencjonowania pracę awaryjną różnych warstw w wielowarstwowej aplikacji, w związku z tym zachowaniu spójności aplikacji. Wykonaj następujące czynności, podczas tworzenia planu odzyskiwania dla aplikacji sieci web w wielowarstwowych. [Dowiedz się więcej o tworzeniu planu odzyskiwania](site-recovery-runbook-automation.md#customize-the-recovery-plan).

### <a name="adding-virtual-machines-to-failover-groups"></a>Dodawanie maszyn wirtualnych do trybu failover grupy

1. Tworzenie planu odzyskiwania przez dodanie aplikacji i maszyny wirtualne warstwy sieci Web.
2. Kliknij pozycję Dostosuj, aby zgrupować maszyn wirtualnych. Domyślnie wszystkie maszyny wirtualne są częścią "Grupa 1".

    ![Dostosowywanie planu odzyskiwania](./media/site-recovery-sharepoint/rp-groups.png)

3. Utworzyć inną grupę (Grupa 2) i przenieść warstwa sieci Web, maszyn wirtualnych do nowej grupy. Warstwę aplikacji maszyn wirtualnych powinny być częścią "Grupa 1" i warstwa sieci Web maszyn wirtualnych powinny być częścią "Grupa 2". To jest zapewnienie, że rozruch maszyny wirtualne warstwy aplikacji się najpierw następuje warstwa sieci Web maszyn wirtualnych.


### <a name="adding-scripts-to-the-recovery-plan"></a>Dodawanie skryptów do planu odzyskiwania

Najczęściej używane skrypty usługi Azure Site Recovery można wdrożyć na koncie automatyzacji, klikając przycisk "Wdrażanie na platformie Azure" poniżej. Korzystając z dowolnego skryptu opublikowane, upewnij się, że możesz postępuj zgodnie ze wskazówkami w skrypcie.

[![Wdrażanie na platformie Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Skrypt przed akcji "Grupa 1" do trybu failover grupy dostępności funkcji SQL. Użyj skryptu "ASR-SQL-FailoverAG" opublikowany w przykładowe skrypty. Upewnij się, postępuj zgodnie ze wskazówkami w skrypcie i odpowiednio wprowadzić wymagane zmiany w skrypcie.

    ![Dodaj AG-skryptu krok-1](./media/site-recovery-sharepoint/add-ag-script-step1.png)

    ![Dodaj AG-skryptu krok-2](./media/site-recovery-sharepoint/add-ag-script-step2.png)

2. Dodawanie skryptu akcji post do podłączenia modułu równoważenia obciążenia na nieudane przez maszyny wirtualne warstwy sieci Web (Grupa 2). Użyj skryptu "ASR AddSingleLoadBalancer" opublikowany w przykładowe skrypty. Upewnij się, postępuj zgodnie ze wskazówkami w skrypcie i odpowiednio wprowadzić wymagane zmiany w skrypcie.

    ![Add-LB-Script-Step-1](./media/site-recovery-sharepoint/add-lb-script-step1.png)

    ![Add-LB-Script-Step-2](./media/site-recovery-sharepoint/add-lb-script-step2.png)

3. Dodaj krok wykonywany ręcznie do aktualizowania rekordów DNS, aby wskazywał nową farmę na platformie Azure.

    * Dla witryn internetowy nie DNS ma aktualizacji wymagane po pracy awaryjnej. Wykonaj kroki opisane w sekcji "Wskazówki sieci", aby skonfigurować Menedżera ruchu. Jeśli profil usługi Traffic Manager został skonfigurowany zgodnie z opisem w poprzedniej sekcji, należy dodać skrypt, aby otworzyć port zastępczego (800 w przykładzie) na maszynie Wirtualnej Azure.

    * Dla wewnętrznych witryn Dodaj krok wykonywany ręcznie, aby zaktualizować rekord DNS, aby wskazywał IP usługi równoważenia obciążenia nowych sieci Web warstwy maszyny Wirtualnej.

4. Dodaj krok wykonywany ręcznie, aby przywrócić z kopii zapasowej aplikacji wyszukiwania lub uruchomić nową usługę wyszukiwania.

5. Przywracanie aplikacji usługi wyszukiwania z kopii zapasowej, wykonaj następujące czynności.

    * Ta metoda przyjęto założenie, że przed zdarzeniem krytycznego wykonano kopię zapasową aplikacji usługi wyszukiwania i kopia zapasowa jest dostępna w lokacji odzyskiwania po awarii.
    * Łatwo można to osiągnąć przez planowanie tworzenia kopii zapasowej (na przykład raz dziennie) i umieść kopię zapasową w lokacji odzyskiwania po awarii za pomocą procedury kopiowania. Procedury kopiowania mogą obejmować skryptową programów, takich jak AzCopy (kopiowania na platformę Azure) lub konfigurowania DFSR (Distributed pliku usługi replikacji).
    * Po farmy programu SharePoint jest uruchomiona, przejdź administracji centralnej "Kopia zapasowa i przywracanie" i wybierz przywracania. Przywracanie interrogates określona lokalizacja kopii zapasowej (może być konieczne zaktualizowanie wartości). Wybierz kopię zapasową aplikacji usługi wyszukiwania, który chcesz przywrócić.
    * Po przywróceniu wyszukiwania. Należy pamiętać, która oczekuje przywracania można znaleźć taki sam topologii (tej samej liczby serwerów) i te same dysk twardy litery przypisane do tych serwerów. Aby uzyskać więcej informacji, zobacz ["Aplikacji usługi przywrócić wyszukiwania w SharePoint 2013"](https://technet.microsoft.com/library/ee748654.aspx) dokumentu.


6. Począwszy od nowej aplikacji usługi wyszukiwania, wykonaj następujące czynności.

    * Ta metoda zakłada, że kopia zapasowa bazy danych "Administracja wyszukiwania" dostępne w lokacji odzyskiwania po awarii.
    * Ponieważ innych baz danych aplikacji usługi wyszukiwania nie są replikowane, muszą zostać ponownie utworzone. Aby to zrobić, przejdź do witryny Administracja centralna i Usuń aplikację usługi wyszukiwania. Na wszystkich serwerach, które host indeksu wyszukiwania Usuń pliki indeksu.
    * Ponownie utwórz aplikację usługi wyszukiwania i tworzy to ponownie baz danych. Zaleca się, aby przygotować skrypt, który tworzy ponownie tej aplikacji usługi, ponieważ nie można wykonywać wszystkie akcje, za pomocą graficznego interfejsu użytkownika. Na przykład lokalizacji dysku indeksu i Konfigurowanie topologii wyszukiwania są możliwe tylko przy użyciu poleceń cmdlet programu PowerShell programu SharePoint. Użyj polecenia cmdlet programu Windows PowerShell Restore SPEnterpriseSearchServiceApplication i określ dziennikiem i replikowane Administracja wyszukiwania w bazie danych, Search_Service__DB. To polecenie cmdlet umożliwia wyszukiwanie konfiguracji, schematu, właściwości zarządzanych, reguł i źródeł oraz tworzy domyślny zestaw składników.
    * Po aplikacji usługi wyszukiwania ma zostać utworzony ponownie, należy uruchomić pełne przeszukiwanie dla każdego źródła zawartości przywrócić usługi wyszukiwania. Niektóre informacje analytics z farmy lokalnych, takich jak zalecenia wyszukiwania zostaną utracone.

7. Po wykonaniu wszystkich kroków, zapisać planu odzyskiwania i planu odzyskiwania końcowego będą wyglądać jak poniżej.

    ![Zapisane planu odzyskiwania](./media/site-recovery-sharepoint/saved-rp.png)

## <a name="doing-a-test-failover"></a>Ten test trybu failover
Postępuj zgodnie z [w tych wskazówkach](site-recovery-test-failover-to-azure.md) przeprowadzić test trybu failover.

1.  Przejdź do portalu Azure i wybierz magazyn usług odzyskiwania.
2.  Polecenie planu odzyskiwania utworzone dla aplikacji programu SharePoint.
3.  Kliknij na "Test trybu Failover".
4.  Wybierz punkt odzyskiwania i sieć wirtualna platformy Azure, aby rozpocząć proces test trybu failover.
5.  Po skonfigurowaniu dodatkowej środowiska można wykonywać z operacji sprawdzania poprawności.
6.  Po zakończeniu operacji sprawdzania poprawności, kliknij przycisk "Oczyszczania testowy tryb failover" w planie odzyskiwania i testowe środowisko trybu failover jest czyszczona.

Wskazówki na ten test trybu failover dla usługi AD i DNS, zapoznaj się z [Test pracy awaryjnej zagadnienia dotyczące AD i DNS](site-recovery-active-directory.md#test-failover-considerations) dokumentu.

Aby uzyskać wskazówki na ten test trybu failover dla SQL zawsze dotyczących grup dostępności, zobacz [podczas testowego trybu failover dla programu SQL Server AlwaysOn](site-recovery-sql.md#steps-to-do-a-test-failover) dokumentu.

## <a name="doing-a-failover"></a>Podczas pracy w trybie failover
Postępuj zgodnie z [w tych wskazówkach](site-recovery-failover.md) do wykonywania pracy awaryjnej.

1.  Przejdź do portalu Azure i wybierz magazyn usług odzyskiwania.
2.  Polecenie planu odzyskiwania utworzone dla aplikacji programu SharePoint.
3.  Kliknij pozycję "Failover".
4.  Wybierz punkt odzyskiwania, aby rozpocząć proces trybu failover.

## <a name="next-steps"></a>Kolejne kroki
Użytkownik może dowiedzieć się więcej o [replikowanie inne aplikacje](site-recovery-workload.md) przy użyciu usługi Site Recovery.
