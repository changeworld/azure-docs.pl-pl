---
title: Konfigurowanie odzyskiwania po awarii dla wielowarstwowej aplikacji programu SharePoint przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania odzyskiwania po awarii dla wielowarstwowej aplikacji programu SharePoint przy użyciu możliwości usługi Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: sutalasi
ms.openlocfilehash: 5f477cf20b817d7a6c8be856636bf1e3755b5424
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61472115"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sharepoint-application-for-disaster-recovery-using-azure-site-recovery"></a>Konfigurowanie odzyskiwania po awarii dla wielowarstwowej aplikacji programu SharePoint do odzyskiwania po awarii przy użyciu usługi Azure Site Recovery

W tym artykule opisano szczegółowo sposobu ochrony aplikacji programu SharePoint przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md).


## <a name="overview"></a>Omówienie

Microsoft SharePoint to zaawansowany aplikacja, która może pomóc grupy lub działu organizowania, współpracy i udostępniania informacji. Program SharePoint może zapewnić portali sieci intranet, dokumentów i plików zarządzania, współpracy, sieci społecznościowych, sieci ekstranet, witryn sieci Web, wyszukiwaniu korporacyjnemu i analizy biznesowej. Ma również integracji systemów, procesów i możliwości automatyzacji przepływu pracy. Zazwyczaj organizacji należy wziąć pod uwagę ją jako aplikację warstwy 1 wrażliwe na czas przestoju i utraty danych.

Już dziś Microsoft SharePoint nie zapewnia wszystkie funkcje odzyskiwania po awarii poza pole. Niezależnie od typu i skalowania awarii odzyskiwanie obejmuje korzystanie z centrum danych wstrzymania, które można odzyskać do farmy. Centra danych wstrzymania są wymagane dla scenariuszy, w którym lokalnych systemów nadmiarowy i kopie zapasowe nie można odzyskać sprawności po awarii w podstawowym centrum danych.

Rozwiązanie odzyskiwania po awarii w dobrej powinna zezwalać na modelowanie planów odzyskiwania dotyczące architektury aplikacji złożonych, takich jak program SharePoint. Powinien również mieć możliwość dodawania niestandardowych kroków do obsługi aplikacji mapowania między różnych warstw i dlatego dostarczanie przejścia w tryb failover jednym kliknięciem niższa wartość celu punktu odzyskiwania w przypadku awarii.

W tym artykule opisano szczegółowo sposobu ochrony aplikacji programu SharePoint przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md). W tym artykule opisano najlepsze rozwiązania dotyczące replikowania trzy warstwy aplikacji programu SharePoint do platformy Azure, jak odzyskiwanie po awarii i jak możesz przełączyć działanie aplikacji na platformie Azure.

Możesz obejrzeć poniższy film o odzyskiwaniu wielowarstwową aplikację na platformie Azure.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/Disaster-Recovery-of-load-balanced-multi-tier-applications-using-Azure-Site-Recovery/player]


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że należy zrozumieć następujące kwestie:

1. [Replikowanie maszyny wirtualnej na platformie Azure](site-recovery-vmware-to-azure.md)
2. Jak [projektować sieć odzyskiwania](site-recovery-network-design.md)
3. [Podczas testowania trybu failover na platformie Azure](site-recovery-test-failover-to-azure.md)
4. [Przełączeniem w tryb failover na platformie Azure](site-recovery-failover.md)
5. Jak [replikacji kontrolera domeny](site-recovery-active-directory.md)
6. Jak [replikowanie programu SQL Server](site-recovery-sql.md)

## <a name="sharepoint-architecture"></a>Architektura programu SharePoint

Program SharePoint można wdrażać na co najmniej jeden serwer przy użyciu warstwowego topologii i ról serwera do implementacji projektu farmy, który spełnia określone cele i zamierzenia. Typowe dużych, ważnej farmy serwerów programu SharePoint obsługującej dużą liczbę równoczesnych użytkowników i dużą liczbę elementów zawartości użyć grupowania usługi jako część strategii ich skalowalność. Ta strategia polega na uruchamianie usług na dedykowanych serwerach grupowanie tych usług, a następnie skalowanie w poziomie serwerów jako grupa. Następująca topologia przedstawia usługi i grupowanie trójwarstwową farmy serwerów programu SharePoint server. Zajrzyj do dokumentacji programu SharePoint i architektury linii produktów szczegółowe wskazówki dotyczące różnych topologii programu SharePoint. Można znaleźć więcej szczegółów na temat wdrażania programu SharePoint 2013 w [w tym dokumencie](https://technet.microsoft.com/library/cc303422.aspx).



![Wzorzec wdrożenia 1](./media/site-recovery-sharepoint/sharepointarch.png)


## <a name="site-recovery-support"></a>Obsługa usługi Site Recovery

Do tworzenia w tym artykule, maszyn wirtualnych VMware z systemem Windows Server 2012 R2 Enterprise zostały użyte. Użyto programu SharePoint 2013 Enterprise edition od wersji programu SQL server 2014 Enterprise edition. Jak replikacji usługi Site Recovery jest niezależny od aplikacji, do przechowywania następujących scenariuszach również powinny zaleceń przedstawionych w tym miejscu.

### <a name="source-and-target"></a>Źródłowe i docelowe

**Scenariusz** | **Lokacja dodatkowa** | **Platforma Azure**
--- | --- | ---
**Funkcja Hyper-V** | Yes | Yes
**VMware** | Yes | Yes
**Serwer fizyczny** | Yes | Yes
**Azure** | Nie dotyczy | Yes

### <a name="sharepoint-versions"></a>Wersje programu SharePoint
Obsługiwane są następujące wersje serwera programu SharePoint.

* Program SharePoint server 2013 standardowe
* SharePoint server 2013 Enterprise
* Program SharePoint server 2016 Standard
* SharePoint server 2016 Enterprise

### <a name="things-to-keep-in-mind"></a>Warto pamiętać o

Korzystając z udostępnionego klastra na dysku jako dowolną warstwę aplikacji następnie nie będziesz mógł używać replikacji usługi Site Recovery do replikowania tych maszyn wirtualnych. Możesz korzystać z natywnych replikacji udostępniany przez aplikację, a następnie użyć [planu odzyskiwania](site-recovery-create-recovery-plans.md) w tryb failover wszystkich warstwach.

## <a name="replicating-virtual-machines"></a>Replikowanie maszyn wirtualnych

Postępuj zgodnie z [Niniejsze wskazówki](site-recovery-vmware-to-azure.md) do uruchamiania replikacji maszyny wirtualnej na platformie Azure.

* Po zakończeniu replikacji, upewnij się, przejdź do każdej maszyny wirtualnej dla każdej warstwy i wybierz ten sam zestaw dostępności w "zreplikowany element > Ustawienia > Właściwości > obliczenia i sieć". Na przykład jeśli 3 maszyn wirtualnych warstwy internetowej, upewnij się, że wszystkie 3 maszyny wirtualne są skonfigurowane jako część ten sam zestaw dostępności w usłudze Azure.

    ![Set-Availability-Set](./media/site-recovery-sharepoint/select-av-set.png)

* Aby uzyskać wskazówki na temat ochrony usługi Active Directory i DNS, zobacz [chronić Active Directory i DNS](site-recovery-active-directory.md) dokumentu.

* Aby uzyskać wskazówki na temat ochrony warstwy bazy danych uruchomione w programie SQL server, zapoznaj się [ochrona programu SQL Server](site-recovery-active-directory.md) dokumentu.

## <a name="networking-configuration"></a>Konfiguracja sieci

### <a name="network-properties"></a>Właściwości sieci

* Dla aplikacji i maszyn wirtualnych warstwy internetowej należy skonfigurować ustawienia sieciowe w witrynie Azure portal, aby maszyny wirtualne Pobierz podłączone z odpowiednią siecią odzyskiwania po awarii po włączeniu trybu failover.

    ![Wybierz sieć](./media/site-recovery-sharepoint/select-network.png)


* Jeśli używasz statycznych adresów IP, a następnie określ adres IP, który chcesz, aby maszyna wirtualna w **docelowy adres IP** pola

    ![Ustawianie statycznego adresu IP](./media/site-recovery-sharepoint/set-static-ip.png)

### <a name="dns-and-traffic-routing"></a>Usługi DNS i Routing ruchu

Dla lokacji, z Internetem [Tworzenie profilu usługi Traffic Manager typu "Priority"](../traffic-manager/traffic-manager-create-profile.md) w subskrypcji platformy Azure. A następnie skonfiguruj DNS i usługi Traffic Manager profil w następujący sposób.


| **Where** | **Element źródłowy** | **Docelowy**|
| --- | --- | --- |
| Publicznym systemie DNS | Publiczna usługa DNS dla witryny programu SharePoint <br/><br/> Przykład: sharepoint.contoso.com | Traffic Manager <br/><br/> contososharepoint.trafficmanager.net |
| DNS w środowisku lokalnym | sharepointonprem.contoso.com | Publiczny adres IP w farmie w środowisku lokalnym |


W profilu usługi Traffic Manager [tworzenie punktów końcowych podstawowymi i odzyskiwania](../traffic-manager/traffic-manager-configure-priority-routing-method.md). Użyj zewnętrzny punkt końcowy dla punktu końcowego w lokalnych i publiczny adres IP dla punktu końcowego platformy Azure. Upewnij się, że priorytet jest wyższa do punktu końcowego w środowisku lokalnym.

Hostowanie strony testowej na określonym porcie (np. 800) w warstwie sieci web programu SharePoint, w kolejności dla usługi Traffic Manager próbę automatycznego wykrycia dostępności wpis w tryb failover. To obejście tego problemu w przypadku, gdy nie można włączyć anonimowego uwierzytelniania na żadnym z witryn programu SharePoint.

[Konfigurowanie profilu usługi Traffic Manager](../traffic-manager/traffic-manager-configure-priority-routing-method.md) za pomocą poniższych ustawień.

* Metody routingu — "Priority"
* Czas wygaśnięcia (TTL) — 30 sekund DNS
* Ustawienia monitora punktu końcowego — po włączeniu uwierzytelniania anonimowego, można udostępnić punkt końcowy określonej witryny sieci Web. Alternatywnie można użyć strony testowej na określonym porcie (np. 800).

## <a name="creating-a-recovery-plan"></a>Trwa tworzenie planu odzyskiwania

Plan odzyskiwania umożliwia sekwencjonowania pracy awaryjnej w różnych warstwach aplikacji wielowarstwowej, dlatego utrzymania spójności aplikacji. Wykonaj poniższe kroki, podczas tworzenia planu odzyskiwania w przypadku aplikacji sieci web z wieloma warstwami. [Dowiedz się więcej na temat tworzenia planu odzyskiwania](site-recovery-runbook-automation.md#customize-the-recovery-plan).

### <a name="adding-virtual-machines-to-failover-groups"></a>Dodawanie maszyn wirtualnych do grupy trybu failover

1. Przez dodanie aplikacji i maszyn wirtualnych warstwy internetowej, należy utworzyć plan odzyskiwania.
2. Kliknij przycisk "Dostosuj", aby pogrupować maszyny wirtualne. Domyślnie wszystkie maszyny wirtualne są częścią "Grupa 1".

    ![Dostosuj jednostki Uzależnionej](./media/site-recovery-sharepoint/rp-groups.png)

3. Utworzyć inną grupę (Grupa 2) i przenoszenie maszyn wirtualnych warstwy internetowej do nowej grupy. Maszyn wirtualnych do warstwy aplikacji powinna być częścią "Grupa 1" i maszyn wirtualnych warstwy internetowej powinna być częścią "Grupa 2". Ma to upewnij się, że rozruch maszyny wirtualne warstwy aplikacji się najpierw następuje maszyn wirtualnych warstwy internetowej.


### <a name="adding-scripts-to-the-recovery-plan"></a>Dodawanie skryptów do planu odzyskiwania

Najczęściej używane skryptów usługi Azure Site Recovery można wdrożyć na koncie usługi Automation, klikając przycisk "Wdróż na platformie Azure" poniżej. Korzystając z dowolnego skryptu opublikowane, upewnij się, że należy postępować zgodnie ze wskazówkami w skrypcie.

[![Wdrażanie na platformie Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Dodać skrypt akcję wstępną "Grupa 1", aby przełączyć awaryjnie grupy dostępności programu SQL. Użyj skryptu "ASR-SQL-FailoverAG" opublikowany w przykładowych skryptach. Upewnij się, zgodnie z wytycznymi w skrypcie i wprowadź wymagane zmiany w skrypcie, odpowiednio.

    ![Add-AG-Script-Step-1](./media/site-recovery-sharepoint/add-ag-script-step1.png)

    ![Add-AG-Script-Step-2](./media/site-recovery-sharepoint/add-ag-script-step2.png)

2. Dodaj skrypt akcji post w celu dołączenia modułu równoważenia obciążenia w trybie za pośrednictwem maszyn wirtualnych warstwy internetowej (Grupa 2). Użyj skryptu "ASR-AddSingleLoadBalancer" opublikowany w przykładowych skryptach. Upewnij się, zgodnie z wytycznymi w skrypcie i wprowadź wymagane zmiany w skrypcie, odpowiednio.

    ![Add-LB-Script-Step-1](./media/site-recovery-sharepoint/add-lb-script-step1.png)

    ![Add-LB-Script-Step-2](./media/site-recovery-sharepoint/add-lb-script-step2.png)

3. Dodaj to krok wykonywany ręcznie do aktualizowania rekordów DNS, aby wskazywał nową farmę na platformie Azure.

    * W przypadku witryn Internetem żadnych aktualizacji DNS są wymagane po pracy awaryjnej. Wykonaj kroki opisane w sekcji "Wskazówki dotyczące sieci", aby skonfigurować usługi Traffic Manager. Jeśli profil usługi Traffic Manager została skonfigurowana zgodnie z opisem w poprzedniej sekcji, należy dodać skrypt, aby otworzyć port fikcyjnego (800 w przykładzie) na maszynie Wirtualnej platformy Azure.

    * W przypadku wewnętrznych witryn firmowych umożliwiający dostęp do Internetu należy dodać krok wykonywany ręcznie, aby zaktualizować rekord DNS, aby wskazywał nowy sieci Web warstwy maszyny Wirtualnej adresu IP modułu równoważenia obciążenia.

4. Dodaj to krok wykonywany ręcznie, aby przywrócić aplikację wyszukiwania z kopii zapasowej lub uruchomić nową usługę wyszukiwania.

5. Przywracanie aplikacji usługi wyszukiwania z kopii zapasowej, wykonaj poniższe kroki.

    * Ta metoda zakłada się, czy przed zdarzeniem krytycznego wykonano kopię zapasową aplikacji usługi wyszukiwania i kopii zapasowej jest dostępna w lokacji odzyskiwania po awarii.
    * Łatwo można to osiągnąć przez planowanie tworzenia kopii zapasowej (na przykład raz dziennie) i umieszczenie kopii zapasowej lokacji odzyskiwania po awarii za pomocą procedury kopiowania. Kopiuj procedury mogą obejmować inicjowanych przez skrypty programów, takich jak narzędzia AzCopy (kopiowania na platformę Azure) lub konfigurowania DFSR (pliku usługi replikacji rozproszonego).
    * Po farmy programu SharePoint jest uruchomiona, przejdź administracji centralnej, "Kopia zapasowa i przywracanie" i wybierz opcję przywracania. Przywracanie interrogates określona lokalizacja kopii zapasowej (może być konieczne zaktualizowanie wartości). Wybierz kopię zapasową aplikacji usługi wyszukiwania, który chcesz przywrócić.
    * Wyszukiwanie zostanie przywrócony. Należy pamiętać, że przywracanie spodziewa się znaleźć takie same topologii (tej samej liczby serwerów) oraz takie same twarde litery dysków przypisane do tych serwerów. Aby uzyskać więcej informacji, zobacz ["Aplikacja usługi wyszukiwania przywracania w programie SharePoint 2013"](https://technet.microsoft.com/library/ee748654.aspx) dokumentu.


6. Począwszy od nowej aplikacji usługi wyszukiwania, wykonaj poniższe kroki.

    * Ta metoda zakłada, że kopię zapasową bazy danych "Administracja wyszukiwania" jest dostępna, w lokacji odzyskiwania po awarii.
    * Ponieważ innych baz danych aplikacji usługi wyszukiwania nie są replikowane, muszą zostać ponownie utworzone. Aby to zrobić, przejdź do witryny Administracja centralna i usuwania aplikacji usługi wyszukiwania. Na wszystkich serwerach, które hostują indeksu wyszukiwania Usuń pliki indeksu.
    * Ponownie utwórz aplikację usługi wyszukiwania i ponownie powstaje baz danych. Zaleca się mieć przygotowany skrypt, który tworzy ponownie tej aplikacji usługi, ponieważ nie jest możliwe wykonanie wszystkich akcji za pomocą graficznego interfejsu użytkownika. Na przykład lokalizacji dysku indeksu i konfigurowania topologię wyszukiwania są możliwe tylko przy użyciu poleceń cmdlet programu PowerShell programu SharePoint. Użyj polecenia cmdlet programu Windows PowerShell SPEnterpriseSearchServiceApplication przywracania i określ dziennikiem i replikowane Administracja wyszukiwania w bazie danych, Search_Service__DB. To polecenie cmdlet zapewnia konfiguracji wyszukiwania, schemat, właściwości zarządzane, reguł i źródeł i tworzy domyślny zestaw innych składników.
    * Gdy aplikacja usługi wyszukiwania ma zostać utworzona ponownie, należy uruchomić pełne przeszukiwanie dla każdego źródła zawartości przywrócić usługę wyszukiwania. Niektóre informacje analizy z farmy serwerów lokalnych, takich jak zalecenia dotyczące wyszukiwania zostaną utracone.

7. Po wykonaniu wszystkich kroków, Zapisz plan odzyskiwania i planu odzyskiwania końcowy będzie wyglądał jak poniżej.

    ![Zapisane RP](./media/site-recovery-sharepoint/saved-rp.png)

## <a name="doing-a-test-failover"></a>Podczas testowania trybu failover
Postępuj zgodnie z [Niniejsze wskazówki](site-recovery-test-failover-to-azure.md) celu testowy tryb failover.

1.  Przejdź do witryny Azure portal i wybierz swój magazyn usług odzyskiwania.
2.  Kliknij w planie odzyskiwania, utworzony dla aplikacji programu SharePoint.
3.  Kliknij pozycję "Testowanie trybu Failover".
4.  Wybierz punkt odzyskiwania i siecią wirtualną platformy Azure, aby rozpocząć proces testu trybu failover.
5.  Po skonfigurowaniu dodatkowej środowiska można wykonywać swoje operacji sprawdzania poprawności.
6.  Po zakończeniu operacji sprawdzania poprawności, kliknij pozycję "Wyczyść test pracy awaryjnej" w planie odzyskiwania i testowe środowisko trybu failover jest czyszczona.

Wskazówki dotyczące przeprowadzasz test pracy awaryjnej dla usługi AD DNS, można znaleźć [testowanie trybu failover zagadnienia dotyczące usługi AD DNS i](site-recovery-active-directory.md#test-failover-considerations) dokumentu.

Aby uzyskać wskazówki dotyczące przeprowadzasz test trybu failover dla SQL zawsze włączone grupy dostępności, zapoznaj się [podczas testowania trybu failover dla programu SQL Server Always On](site-recovery-sql.md#steps-to-do-a-test-failover) dokumentu.

## <a name="doing-a-failover"></a>Przełączeniem w tryb failover
Postępuj zgodnie z [Niniejsze wskazówki](site-recovery-failover.md) dla przełączeniem w tryb failover.

1.  Przejdź do witryny Azure portal i wybierz swój magazyn usługi Recovery Services.
2.  Kliknij w planie odzyskiwania, utworzony dla aplikacji programu SharePoint.
3.  Kliknij pozycję "Failover".
4.  Wybierz punkt odzyskiwania, aby rozpocząć proces trybu failover.

## <a name="next-steps"></a>Kolejne kroki
Możesz dowiedzieć się więcej [replikacji z innych aplikacji](site-recovery-workload.md) przy użyciu Site Recovery.
