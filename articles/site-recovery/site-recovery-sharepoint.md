---
title: Odzyskiwanie po awarii dla wielowarstwowej aplikacji programu SharePoint przy użyciu usługi Azure Site Recovery
description: W tym artykule opisano sposób konfigurowania odzyskiwania po awarii dla wielowarstwowej aplikacji programu SharePoint przy użyciu funkcji usługi Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 6/27/2019
ms.author: sutalasi
ms.openlocfilehash: d74e28ce470c23bbc8ee2081532a198c260ccea5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74706359"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sharepoint-application-for-disaster-recovery-using-azure-site-recovery"></a>Konfigurowanie odzyskiwania po awarii dla wielowarstwowej aplikacji programu SharePoint do odzyskiwania po awarii przy użyciu usługi Azure Site Recovery

W tym artykule opisano szczegółowo, jak chronić aplikację programu SharePoint przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md).


## <a name="overview"></a>Omówienie

Microsoft SharePoint to zaawansowana aplikacja, która może pomóc grupie lub działowi organizować, współpracować i udostępniać informacje. Program SharePoint może udostępniać portale intranetowe, zarządzanie dokumentami i plikami, współpracę, sieci społecznościowe, ekstranety, witryny sieci Web, wyszukiwanie w przedsiębiorstwie i analizy biznesowe. Posiada również możliwości integracji systemu, integracji procesów i automatyzacji przepływu pracy. Zazwyczaj organizacje uważają go za aplikację warstwy 1 wrażliwe na przestoje i utraty danych.

Obecnie program Microsoft SharePoint nie udostępnia żadnych gotowych funkcji odzyskiwania po awarii. Niezależnie od typu i skali awarii odzyskiwanie obejmuje użycie centrum danych w trybie gotowości, do którego można odzyskać farmę. Centra danych w trybie gotowości są wymagane w scenariuszach, w których lokalne nadmiarowe systemy i kopie zapasowe nie mogą odzyskać danych po awarii w głównym centrum danych.

Dobre rozwiązanie odzyskiwania po awarii powinno umożliwić modelowanie planów odzyskiwania wokół złożonych architektur aplikacji, takich jak SharePoint. Powinien również mieć możliwość dodawania niestandardowych kroków do obsługi mapowania aplikacji między różnymi warstwami, a tym samym zapewnienie pracy awaryjnej z jednym kliknięciem z niższym celem odzyskiwania w przypadku awarii.

W tym artykule opisano szczegółowo, jak chronić aplikację programu SharePoint przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md). W tym artykule opisano najlepsze rozwiązania dotyczące replikowania aplikacji programu SharePoint z trzema warstwami na platformie Azure, jak można wykonać ćwiczenie odzyskiwania po awarii i jak można wykonać tryb failover aplikacji na platformie Azure.

Możesz obejrzeć poniższy film o odzyskiwaniu aplikacji wielowarstwowej na platformie Azure.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/Disaster-Recovery-of-load-balanced-multi-tier-applications-using-Azure-Site-Recovery/player]


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że rozumiesz następujące kwestie:

1. [Replikowanie maszyny wirtualnej na platformę Azure](site-recovery-vmware-to-azure.md)
2. Jak [zaprojektować sieć odzyskiwania](site-recovery-network-design.md)
3. [Wykonywanie testowego trybu failover na platformie Azure](site-recovery-test-failover-to-azure.md)
4. [Wykonywanie pracy awaryjnej na platformie Azure](site-recovery-failover.md)
5. Jak [replikować kontroler domeny](site-recovery-active-directory.md)
6. Jak [replikować program SQL Server](site-recovery-sql.md)

## <a name="sharepoint-architecture"></a>Architektura programu SharePoint

Program SharePoint można wdrożyć na jednym lub kilku serwerach przy użyciu warstwowych topologii i ról serwera w celu zaimplementowania projektu farmy, który spełnia określone cele i cele. Typowa duża farma serwerów programu SharePoint o wysokim popycie, która obsługuje dużą liczbę równoczesnych użytkowników i dużą liczbę elementów zawartości, używa grupowania usług w ramach strategii skalowalności. Takie podejście obejmuje uruchamianie usług na serwerach dedykowanych, grupowanie tych usług razem, a następnie skalowanie w poziomie serwerów jako grupy. Poniższa topologia ilustruje grupowanie usług i serwerów dla trzywarstwowej farmy serwerów programu SharePoint. Szczegółowe wskazówki dotyczące różnych topologii programu SharePoint można znaleźć w dokumentacji programu SharePoint i architekturach linii produktów. Więcej informacji na temat wdrażania programu SharePoint 2013 można znaleźć w [tym dokumencie](https://technet.microsoft.com/library/cc303422.aspx).



![Wzorzec wdrażania 1](./media/site-recovery-sharepoint/sharepointarch.png)


## <a name="site-recovery-support"></a>Obsługa usługi Site Recovery

Usługa Site Recovery jest niezależna od aplikacji i powinna współpracować z dowolną wersją programu SharePoint działającą na obsługiwanym komputerze. Do tworzenia tego artykułu użyto maszyn wirtualnych VMware z systemem Windows Server 2012 R2 Enterprise. Użyto programu SharePoint 2013 Enterprise edition i SQL Server 2014 Enterprise edition.

### <a name="source-and-target"></a>Źródło i cel

**Scenariusz** | **Do lokacji dodatkowej** | **Na platformę Azure**
--- | --- | ---
**Funkcja Hyper-V** | Tak | Tak
**Vmware** | Tak | Tak
**Serwer fizyczny** | Tak | Tak
**Azure** | Nie dotyczy | Tak


### <a name="things-to-keep-in-mind"></a>Rzeczy, o których należy pamiętać

Jeśli używasz udostępnionego klastra opartego na dysku jako dowolnej warstwy w aplikacji, nie będzie można użyć replikacji usługi Site Recovery do replikacji tych maszyn wirtualnych. Można użyć replikacji natywnej dostarczonej przez aplikację, a następnie użyć [planu odzyskiwania,](site-recovery-create-recovery-plans.md) aby przewinąć w stan failover wszystkie warstwy.

## <a name="replicating-virtual-machines"></a>Replikowanie maszyn wirtualnych

Postępuj zgodnie [z tymi wskazówkami,](site-recovery-vmware-to-azure.md) aby rozpocząć replikowanie maszyny wirtualnej na platformę Azure.

* Po zakończeniu replikacji upewnij się, że przejdziesz do każdej maszyny wirtualnej każdej warstwy i wybierz tę samą dostępność ustawioną w sekcji "Replikowane elementy > Ustawienia > właściwości > obliczeniowe i sieciowe". Na przykład jeśli warstwa sieci web ma 3 maszyny wirtualne, upewnij się, że wszystkie 3 maszyny wirtualne są skonfigurowane jako część tej samej dostępności ustawionej na platformie Azure.

    ![Zestaw zestaw-dostępność-zestaw](./media/site-recovery-sharepoint/select-av-set.png)

* Aby uzyskać wskazówki dotyczące ochrony usługi Active Directory i dns, zobacz [Ochrona usługi Active Directory i](site-recovery-active-directory.md) dokumentu DNS.

* Aby uzyskać wskazówki dotyczące ochrony warstwy bazy danych uruchomionej na serwerze SQL, zobacz [Chroń](site-recovery-sql.md) dokument programu SQL Server.

## <a name="networking-configuration"></a>Konfiguracja sieci

### <a name="network-properties"></a>Właściwości sieci

* W przypadku maszyn wirtualnych w warstwie App i sieci Web należy skonfigurować ustawienia sieciowe w witrynie Azure portal, aby maszyny wirtualne zostały podłączone do prawej sieci odzyskiwania po awarii po przełączeniu awaryjnym.

    ![Wybierz sieć](./media/site-recovery-sharepoint/select-network.png)


* Jeśli używasz statycznego adresu IP, określ adres IP, który ma zostać przyjmowany przez maszynę wirtualną w polu **Docelowy adres IP**

    ![Ustawianie statycznego adresu IP](./media/site-recovery-sharepoint/set-static-ip.png)

### <a name="dns-and-traffic-routing"></a>Routing DNS i traffic

W przypadku witryn internetowych [utwórz profil usługi Traffic Manager typu "Priorytet"](../traffic-manager/traffic-manager-create-profile.md) w subskrypcji platformy Azure. Następnie skonfiguruj profil DNS i Traffic Manager w następujący sposób.


| **Gdzie** | **Źródła** | **Docelowego**|
| --- | --- | --- |
| Publiczna usługa DNS | Publiczny system DNS dla witryn programu SharePoint <br/><br/> Przykład: sharepoint.contoso.com | Traffic Manager <br/><br/> contososharepoint.trafficmanager.net |
| Lokalny system DNS | sharepointonprem.contoso.com | Publiczny adres IP w gospodarstwie lokalnym |


W profilu Usługi Traffic Manager [utwórz podstawowe i odzyskiwania punktów końcowych](../traffic-manager/traffic-manager-configure-priority-routing-method.md). Użyj zewnętrznego punktu końcowego dla lokalnego punktu końcowego i publicznego adresu IP dla punktu końcowego platformy Azure. Upewnij się, że priorytet jest ustawiony wyżej na lokalny punkt końcowy.

Hostuj stronę testową na określonym porcie (na przykład 800) w warstwie sieci Web programu SharePoint, aby usługa Traffic Manager automatycznie wykrywała dostępność po przemijeniu w stan failover. Jest to obejście w przypadku, gdy nie można włączyć uwierzytelniania anonimowego w żadnej z witryn programu SharePoint.

[Skonfiguruj profil Usługi Traffic Manager](../traffic-manager/traffic-manager-configure-priority-routing-method.md) z poniższymi ustawieniami.

* Metoda routingu — "Priorytet"
* Czas dns do życia (TTL) - '30 sekund'
* Ustawienia monitora punktu końcowego — jeśli można włączyć uwierzytelnianie anonimowe, można podać określony punkt końcowy witryny sieci Web. Można też użyć strony testowej na określonym porcie (na przykład 800).

## <a name="creating-a-recovery-plan"></a>Tworzenie planu odzyskiwania

Plan odzyskiwania umożliwia sekwencjonowanie pracy awaryjnej różnych warstw w aplikacji wielowarstwowej, w związku z tym utrzymanie spójności aplikacji. Wykonaj poniższe kroki podczas tworzenia planu odzyskiwania dla wielowarstwowej aplikacji sieci web. [Dowiedz się więcej o tworzeniu planu odzyskiwania](site-recovery-runbook-automation.md#customize-the-recovery-plan).

### <a name="adding-virtual-machines-to-failover-groups"></a>Dodawanie maszyn wirtualnych do grup trybu failover

1. Utwórz plan odzyskiwania, dodając maszyny wirtualne warstwy aplikacji i sieci Web.
2. Kliknij na "Dostosuj", aby zgrupować maszyny wirtualne. Domyślnie wszystkie maszyny wirtualne są częścią "Grupy 1".

    ![Dostosowywanie RP](./media/site-recovery-sharepoint/rp-groups.png)

3. Utwórz inną grupę (grupa 2) i przenieś maszyny wirtualne warstwy sieci Web do nowej grupy. Maszyny wirtualne warstwy aplikacji powinny być częścią "Grupy 1", a maszyny wirtualne warstwy sieci Web powinny być częścią "Grupy 2". Ma to na celu zapewnienie, że maszyny wirtualne warstwy aplikacji są uruchamiane najpierw, a następnie maszyny wirtualne w warstwie Sieci Web.


### <a name="adding-scripts-to-the-recovery-plan"></a>Dodawanie skryptów do planu odzyskiwania

Najczęściej używane skrypty usługi Azure Site Recovery można wdrożyć na koncie automatyzacji, klikając przycisk "Wdrażanie na platformie Azure" poniżej. Podczas korzystania z dowolnego opublikowanego skryptu, upewnij się, że postępuj zgodnie ze wskazówkami w skrypcie.

[![Wdrażanie na platformie Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Dodaj skrypt przeddziałakiem do grupy "Grupa 1" do grupy dostępności SQL trybu failover. Użyj skryptu "ASR-SQL-FailoverAG" opublikowanego w przykładowych skryptach. Upewnij się, że postępuj zgodnie ze wskazówkami w skrypcie i odpowiednio wprowadzić wymagane zmiany w skrypcie.

    ![Add-AG-Script-Krok-1](./media/site-recovery-sharepoint/add-ag-script-step1.png)

    ![Add-AG-Script-Krok-2](./media/site-recovery-sharepoint/add-ag-script-step2.png)

2. Dodaj skrypt akcji księgowania, aby dołączyć moduł równoważenia obciążenia na maszynach wirtualnych w sieci Web po awarii (grupa 2). Użyj skryptu "ASR-AddSingleLoadBalancer" opublikowanego w przykładowych skryptach. Upewnij się, że postępuj zgodnie ze wskazówkami w skrypcie i odpowiednio wprowadzić wymagane zmiany w skrypcie.

    ![Add-LB-Script-Krok-1](./media/site-recovery-sharepoint/add-lb-script-step1.png)

    ![Add-LB-Script-Step-2](./media/site-recovery-sharepoint/add-lb-script-step2.png)

3. Dodaj ręczny krok, aby zaktualizować rekordy DNS, aby wskazać nową farmę na platformie Azure.

    * W przypadku witryn internetowych nie są wymagane żadne aktualizacje DNS po przełączeniu w stan failover. Wykonaj kroki opisane w sekcji "Wskazówki dotyczące sieci", aby skonfigurować Menedżera ruchu. Jeśli profil usługi Traffic Manager został skonfigurowany zgodnie z opisem w poprzedniej sekcji, dodaj skrypt, aby otworzyć port fikcyjny (800 w przykładzie) na maszynie Wirtualnej platformy Azure.

    * W przypadku witryn wychodzących wewnętrznych dodaj ręczny krok, aby zaktualizować rekord DNS, aby wskazać adres IP modułu równoważenia obciążenia nowej warstwy sieci Web.

4. Dodaj ręczny krok, aby przywrócić aplikację wyszukiwania z kopii zapasowej lub uruchomić nową usługę wyszukiwania.

5. Aby przywrócić aplikację usługi wyszukiwania z kopii zapasowej, wykonaj poniższe kroki.

    * Ta metoda zakłada, że kopia zapasowa aplikacji usługi wyszukiwania została wykonana przed katastrofalnym zdarzeniem i że kopia zapasowa jest dostępna w lokacji odzyskiwania po awarii.
    * Można to łatwo osiągnąć, planując kopię zapasową (na przykład raz dziennie) i korzystając z procedury kopiowania, aby umieścić kopię zapasową w lokacji odzyskiwania po awarii. Procedury kopiowania mogą obejmować programy skryptowe, takie jak AzCopy (Azure Copy) lub konfigurowanie usługi DFSR (Replikacja rozproszonych usług plików).
    * Teraz, gdy farma programu SharePoint jest uruchomiona, przejdź do administracji centralnej, "Kopia zapasowa i przywracanie" i wybierz pozycję Przywróć. Przywracanie przesłuchuje określoną lokalizację kopii zapasowej (może być konieczne zaktualizowanie wartości). Wybierz kopię zapasową aplikacji usługi wyszukiwania, którą chcesz przywrócić.
    * Wyszukiwanie zostanie przywrócone. Należy pamiętać, że przywracanie oczekuje, aby znaleźć tę samą topologię (taką samą liczbę serwerów) i te same litery dysków twardych przypisane do tych serwerów. Aby uzyskać więcej informacji, zobacz [dokument "Przywracanie aplikacji usługi wyszukiwania w programie SharePoint 2013".](https://technet.microsoft.com/library/ee748654.aspx)


6. Aby rozpocząć od nowej aplikacji usługi wyszukiwania, wykonaj poniższe kroki.

    * Ta metoda zakłada, że kopia zapasowa bazy danych "Administracja wyszukiwania" jest dostępna w lokacji odzyskiwania po awarii.
    * Ponieważ inne bazy danych aplikacji usługi wyszukiwania nie są replikowane, muszą zostać ponownie utworzone. Aby to zrobić, przejdź do administracji centralnej i usuń aplikację usługi wyszukiwania. Na wszystkich serwerach, na których znajduje się indeks wyszukiwania, usuń pliki indeksu.
    * Ponownie utwórz aplikację usługi wyszukiwania i to ponownie tworzy bazy danych. Zaleca się, aby mieć przygotowany skrypt, który ponownie tworzy tę aplikację usługi, ponieważ nie jest możliwe wykonywanie wszystkich akcji za pośrednictwem interfejsu użytkownika. Na przykład ustawienie lokalizacji dysku indeksu i skonfigurowanie topologii wyszukiwania jest możliwe tylko za pomocą poleceń cmdlet programu SharePoint PowerShell. Użyj polecenia cmdlet restore-SPEnterpriseSearchServiceApplication systemu Windows PowerShell i określ bazę danych administracji wyszukiwania dostarczoną z dziennikiem i replikowaną, Search_Service__DB. To polecenie cmdlet daje konfigurację wyszukiwania, schemat, właściwości zarządzane, reguły i źródła i tworzy domyślny zestaw innych składników.
    * Po ponownym utworzeniu aplikacji usługi wyszukiwania należy uruchomić pełne przeszukiwanie dla każdego źródła zawartości, aby przywrócić usługę wyszukiwania. Utracisz niektóre informacje analityczne z lokalnej farmy, takie jak zalecenia dotyczące wyszukiwania.

7. Po wykonaniu wszystkich kroków zapisz plan odzyskiwania, a ostateczny plan odzyskiwania będzie wyglądał następująco.

    ![Zapisany RP](./media/site-recovery-sharepoint/saved-rp.png)

## <a name="doing-a-test-failover"></a>Wykonywanie testu w pracy awaryjnej
Postępuj zgodnie [z tymi wskazówkami,](site-recovery-test-failover-to-azure.md) aby wykonać test pracy awaryjnej.

1.  Przejdź do witryny Azure portal i wybierz magazyn usługi odzyskiwania.
2.  Kliknij plan odzyskiwania utworzony dla aplikacji programu SharePoint.
3.  Kliknij na "Test failover".
4.  Wybierz punkt odzyskiwania i sieć wirtualną platformy Azure, aby rozpocząć proces testowania pracy awaryjnej.
5.  Po uruchomieniu środowiska pomocniczego można wykonać sprawdzanie poprawności.
6.  Po zakończeniu sprawdzania poprawności można kliknąć przycisk "Oczyszczanie testu pracy awaryjnej" w planie odzyskiwania i środowiska pracy awaryjnej testu jest czyszczone.

Aby uzyskać wskazówki dotyczące wykonywania pracy awaryjnej testu dla usługi AD i DNS, zapoznaj się z [testowaniem zagadnień trybu failover dla](site-recovery-active-directory.md#test-failover-considerations) usługi AD i dokumentu DNS.

Aby uzyskać wskazówki dotyczące wykonywania testowania pracy awaryjnej dla grup dostępności zawsze włączone sql, zobacz [wykonywanie odzyskiwania po awarii aplikacji za pomocą usługi Azure Site Recovery i robi test](site-recovery-sql.md#disaster-recovery-of-an-application) pracy awaryjnej dokumentu.

## <a name="doing-a-failover"></a>Wykonywanie pracy awaryjnej
Postępuj zgodnie [z tymi wskazówkami](site-recovery-failover.md) dotyczącymi pracy awaryjnej.

1.  Przejdź do witryny Azure portal i wybierz magazyn usług odzyskiwania.
2.  Kliknij plan odzyskiwania utworzony dla aplikacji programu SharePoint.
3.  Kliknij na "Failover".
4.  Wybierz punkt odzyskiwania, aby rozpocząć proces pracy awaryjnej.

## <a name="next-steps"></a>Następne kroki
Możesz dowiedzieć się więcej o [replikowaniu innych aplikacji](site-recovery-workload.md) przy użyciu funkcji Odzysk witryny.
