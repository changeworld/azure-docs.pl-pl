---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/05/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: aa7ddb75017a532b436b9a5cfc71d1a7c2832cb6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77179130"
---
W tym artykule wyjaśniono, jak zaplanować tworzenie kopii zapasowych i odzyskiwania po awarii (DR) maszyn wirtualnych IaaS i dysków na platformie Azure. Ten dokument obejmuje zarówno dyski zarządzane, jak i niezarządzane.

Po pierwsze, omówimy wbudowane funkcje odporności na uszkodzenia na platformie Azure, która pomaga chronić przed lokalnymi awariami. Następnie omówimy scenariusze awarii nie w pełni objęte wbudowanymi możliwościami. Pokazujemy również kilka przykładów scenariuszy obciążenia, w których można zastosować różne zagadnienia dotyczące tworzenia kopii zapasowych i odzyskiwania po awarii. Następnie przeszukujemy możliwe rozwiązania dla odzyskiwania po awarii dysków IaaS.

## <a name="introduction"></a>Wprowadzenie

Platforma Azure używa różnych metod nadmiarowości i odporności na uszkodzenia, aby chronić klientów przed zlokalizowanych awarii sprzętu. Lokalne błędy mogą obejmować problemy z komputerem serwera usługi Azure Storage, który przechowuje część danych dla dysku wirtualnego lub awarie dysku SSD lub HDD na tym serwerze. Takie awarie izolowanych składników sprzętowych mogą się zdarzyć podczas normalnych operacji.

Platforma Azure została zaprojektowana tak, aby była odporna na te błędy. Poważne awarie mogą spowodować awarie lub niedostępność wielu serwerów magazynu, a nawet całego centrum danych. Mimo że maszyny wirtualne i dyski są zwykle chronione przed zlokalizowanych błędów, dodatkowe kroki są niezbędne do ochrony obciążenia z całego regionu katastrofalne awarie, takie jak poważna awaria, które mogą mieć wpływ na maszyny wirtualne i dyski.

Oprócz możliwości awarii platformy mogą wystąpić problemy z aplikacją klienta lub danymi. Na przykład nowa wersja aplikacji może przypadkowo wprowadzić zmiany w danych, które powoduje, że do przerwania. W takim przypadku można przywrócić aplikację i dane do poprzedniej wersji, która zawiera ostatni znany dobry stan. Wymaga to utrzymywania regularnych kopii zapasowych.

W celu odzyskiwania po awarii regionalnej należy wygenerować kopii zapasowej dysków maszyny Wirtualnej IaaS w innym regionie.

Zanim przyjrzymy się opcji kopii zapasowej i odzyskiwania po awarii, podsumujmy kilka metod dostępnych do obsługi zlokalizowanych błędów.

### <a name="azure-iaas-resiliency"></a>Odporność usługi Azure IaaS

*Odporność* odnosi się do tolerancji dla normalnych awarii występujących w składnikach sprzętu. Odporność jest możliwość odzyskania od awarii i nadal działać. Nie dotyczy to unikania awarii, ale odpowiadania na awarie w taki sposób, który pozwala uniknąć przestoju lub utraty danych. Celem odporności jest przywrócenie aplikacji do stanu pełnej funkcjonalności po wystąpieniu awarii. Maszyny i dyski wirtualne platformy Azure są zaprojektowane tak, aby były odporne na typowe błędy sprzętowe. Przyjrzyjmy się, jak platforma Azure IaaS zapewnia tę odporność.

Maszyna wirtualna składa się głównie z dwóch części: serwera obliczeniowego i dysków trwałych. Oba wpływają na odporność na uszkodzenia maszyny wirtualnej.

Jeśli serwer hosta obliczeniowego platformy Azure, w którym znajduje się maszyna wirtualna, występuje awaria sprzętu, co jest rzadkością, platforma Azure jest przeznaczona do automatycznego przywracania maszyny wirtualnej na innym serwerze. Jeśli w tym scenariuszu komputer zostanie uruchomiony ponownie, a maszyna wirtualna powróci po pewnym czasie. Platforma Azure automatycznie wykrywa takie awarie sprzętu i wykonuje odzyskiwanie, aby zapewnić dostępność maszyny Wirtualnej klienta tak szybko, jak to możliwe.

Jeśli chodzi o dyski IaaS, trwałość danych ma kluczowe znaczenie dla trwałej platformy pamięci masowej. Klienci platformy Azure mają ważne aplikacje biznesowe działające na usługach IaaS i zależą od trwałości danych. Platforma Azure projektuje ochronę dla tych dysków IaaS z trzema nadmiarowymi kopiami danych przechowywanych lokalnie. Te kopie zapewniają wysoką trwałość przed lokalnymi awariami. Jeśli jeden ze składników sprzętowych, który przechowuje dysk nie powiedzie się, maszyna wirtualna nie ma wpływu, ponieważ istnieją dwie dodatkowe kopie do obsługi żądań dysku. Działa dobrze, nawet jeśli dwa różne składniki sprzętowe, które obsługują dysk, nie działają w tym samym czasie (co jest rzadkością). 

Aby upewnić się, że zawsze można zachować trzy repliki, usługa Azure Storage automatycznie tworzy nową kopię danych w tle, jeśli jedna z trzech kopii stanie się niedostępna. W związku z tym nie powinno być konieczne użycie raid z dyskami platformy Azure dla odporności na uszkodzenia. Prosta konfiguracja RAID 0 powinna być wystarczająca do rozkładania dysków, jeśli to konieczne, do tworzenia większych woluminów.

Dzięki tej architekturze platforma Azure konsekwentnie zapewnia trwałość dysków IaaS klasy korporacyjnej, z wiodącym w branży zerowym procentem [rocznego wskaźnika awaryjności.](https://en.wikipedia.org/wiki/Annualized_failure_rate)

Zlokalizowane błędy sprzętu na hoście obliczeniowym lub na platformie Magazynu mogą czasami spowodować tymczasową niedostępność maszyny Wirtualnej, która jest objęta usługą [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) dla dostępności maszyny Wirtualnej. Platforma Azure udostępnia również wiodącą w branży umowę SLA dla pojedynczych wystąpień maszyn wirtualnych korzystających z najwyższej jakości identyfikatorów SSD platformy Azure.

Aby chronić obciążenia aplikacji przed przestojami spowodowanymi tymczasową niedostępnością dysku lub maszyny wirtualnej, klienci mogą używać [zestawów dostępności.](../articles/virtual-machines/windows/manage-availability.md) Dwie lub więcej maszyn wirtualnych w zestawie dostępności zapewniają nadmiarowość dla aplikacji. Platforma Azure następnie tworzy te maszyny wirtualne i dyski w oddzielnych domenach błędów z różnymi składnikami zasilania, sieci i serwera.

Z powodu tych oddzielnych domen błędów zlokalizowane awarie sprzętu zazwyczaj nie mają wpływu na wiele maszyn wirtualnych w zestawie w tym samym czasie. Posiadanie oddzielnych domen błędów zapewnia wysoką dostępność dla aplikacji. Jest uważany za dobrą praktykę, aby używać zestawów dostępności, gdy wymagana jest wysoka dostępność. Następna sekcja obejmuje aspekt odzyskiwania po awarii.

### <a name="backup-and-disaster-recovery"></a>Odzyskiwanie kopii zapasowych i odzyskiwania po awarii

Odzyskiwanie po awarii to możliwość odzyskania danych po rzadkich, ale poważnych incydentach. Zdarzenia te obejmują nieprzejściowe awarie na szeroką skalę, takie jak przerwy w działaniu usługi, które dotyczą całego regionu. Odzyskiwanie po awarii obejmuje tworzenie kopii zapasowych i archiwizowanie danych i może obejmować ręczną interwencję, taką jak przywracanie bazy danych z kopii zapasowej.

Wbudowana ochrona platformy Azure przed zlokalizowanych błędów może nie w pełni chronić maszyny wirtualne/dyski, jeśli poważna awaria powoduje awarie na dużą skalę. Te awarie na dużą skalę obejmują katastrofalne zdarzenia, takie jak jeśli centrum danych zostanie dotknięte przez huragan, trzęsienie ziemi, pożar lub awarię jednostki sprzętowej na dużą skalę. Ponadto mogą wystąpić błędy z powodu problemów z aplikacją lub danymi.

Aby chronić obciążenia IaaS przed awariami, należy zaplanować nadmiarowość i wykonać kopie zapasowe, aby umożliwić odzyskiwanie. W przypadku odzyskiwania po awarii należy wygeneruj zapasy w innej lokalizacji geograficznej z dala od lokacji głównej. Takie podejście pomaga upewnić się, że kopia zapasowa nie ma wpływu na to samo zdarzenie, które pierwotnie miało wpływ na maszynę wirtualną lub dyski. Aby uzyskać więcej informacji, zobacz [Odzyskiwanie po awarii dla aplikacji platformy Azure](/azure/architecture/resiliency/disaster-recovery-azure-applications).

Zagadnienia dotyczące odzyskiwania po awarii mogą obejmować następujące aspekty:

- Wysoka dostępność: możliwość kontynuowania pracy aplikacji w dobrym stanie bez znacznych przestojów. Według *stanu dobrej kondycji,* ten stan oznacza, że aplikacja jest responsywna, a użytkownicy mogą łączyć się z aplikacją i wchodzić z nią w interakcje. Niektóre aplikacje o znaczeniu krytycznym i bazy danych mogą być zawsze dostępne, nawet jeśli występują błędy na platformie. W przypadku tych obciążeń może być konieczne zaplanowanie nadmiarowości dla aplikacji, a także danych.

- Trwałość danych: W niektórych przypadkach głównym czynnikiem jest zapewnienie, że dane są zachowywane w przypadku awarii. W związku z tym może być konieczne tworzenie kopii zapasowej danych w innej witrynie. W przypadku takich obciążeń może nie być wymagana pełna nadmiarowość dla aplikacji, ale tylko zwykła kopia zapasowa dysków.

## <a name="backup-and-dr-scenarios"></a>Scenariusze tworzenia kopii zapasowych i odzyskiwania po awarii

Przyjrzyjmy się kilka typowych przykładów scenariuszy obciążenia aplikacji i zagadnienia dotyczące planowania odzyskiwania po awarii.

### <a name="scenario-1-major-database-solutions"></a>Scenariusz 1: Główne rozwiązania bazy danych

Należy wziąć pod uwagę serwer produkcyjnej bazy danych, takich jak SQL Server lub Oracle, które mogą obsługiwać wysoką dostępność. Krytyczne aplikacje produkcyjne i użytkownicy zależą od tej bazy danych. Plan odzyskiwania po awarii dla tego systemu może wymagać obsługi następujących wymagań:

- Dane muszą być chronione i możliwe do odzyskania.
- Serwer musi być dostępny do użycia.

Plan odzyskiwania po awarii może wymagać utrzymania repliki bazy danych w innym regionie jako kopii zapasowej. W zależności od wymagań dotyczących dostępności serwera i odzyskiwania danych rozwiązanie może wahać się od lokacji repliki aktywnej aktywnej lub aktywnej i pasywnej do okresowych kopii zapasowych danych w trybie offline. Relacyjne bazy danych, takie jak SQL Server i Oracle, zapewniają różne opcje replikacji. W przypadku programu SQL Server użyj [grup dostępności programu SQL Server AlwaysOn, aby](https://msdn.microsoft.com/library/hh510230.aspx) uzyskać wysoką dostępność.

Bazy danych NoSQL, takie jak MongoDB, obsługują również [repliki](https://docs.mongodb.com/manual/replication/) nadmiarowości. Repliki dla wysokiej dostępności są używane.

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>Scenariusz 2: Klaster nadmiarowych maszyn wirtualnych

Należy wziąć pod uwagę obciążenie obsługiwane przez klaster maszyn wirtualnych, które zapewniają nadmiarowość i równoważenie obciążenia. Jednym z przykładów jest klaster Cassandra wdrożony w regionie. Ten typ architektury już zapewnia wysoki poziom nadmiarowości w tym regionie. Jednak aby chronić obciążenie przed awarią na poziomie regionalnym, należy rozważyć rozłożenie klastra na dwa regiony lub wykonywanie okresowych kopii zapasowych do innego regionu.

### <a name="scenario-3-iaas-application-workload"></a>Scenariusz 3: Obciążenie aplikacji IaaS

Przyjrzyjmy się obciążeniu aplikacji IaaS. Na przykład ta aplikacja może być typowe obciążenie produkcyjne uruchomione na maszynie Wirtualnej platformy Azure. Może to być serwer sieci web lub serwer plików zawierający zawartość i inne zasoby witryny. Może to być również niestandardowa aplikacja biznesowa działająca na maszynie wirtualnej, która przechowywała swoje dane, zasoby i stan aplikacji na dyskach maszyn wirtualnych. W takim przypadku ważne jest, aby regularnie dokonywać wykonywania kopii zapasowych. Częstotliwość tworzenia kopii zapasowej powinna być oparta na charakterze obciążenia maszyny Wirtualnej. Na przykład jeśli aplikacja działa codziennie i modyfikuje dane, kopia zapasowa powinna być podejmowana co godzinę.

Innym przykładem jest serwer raportowania, który pobiera dane z innych źródeł i generuje zagregowane raporty. Utrata tej maszyny Wirtualnej lub dysków może prowadzić do utraty raportów. Jednak może być możliwe ponowne uruchomienie procesu raportowania i ponowne wygenerowanie danych wyjściowych. W takim przypadku tak naprawdę nie masz utraty danych, nawet jeśli serwer raportowania zostanie dotknięty awarią. W rezultacie może mieć wyższy poziom tolerancji dla utraty części danych na serwerze raportowania. W takim przypadku rzadsze kopie zapasowe są opcją zmniejszenia kosztów.

### <a name="scenario-4-iaas-application-data-issues"></a>Scenariusz 4: Problemy z danymi aplikacji IaaS

Inną możliwością są problemy z danymi aplikacji IaaS. Należy wziąć pod uwagę aplikację, która oblicza, utrzymuje i obsługuje krytyczne dane handlowe, takie jak informacje o cenach. Nowa wersja aplikacji miała błąd oprogramowania, który nieprawidłowo obliczał ceny i uszkodził istniejące dane handlowe obsługiwane przez platformę. W tym miejscu najlepszym sposobem działania jest powrót do wcześniejszej wersji aplikacji i danych. Aby to włączyć, należy wykonać okresowe kopie zapasowe systemu.

## <a name="disaster-recovery-solution-azure-backup"></a>Rozwiązanie do odzyskiwania po awarii: kopia zapasowa platformy Azure 

[Usługa Azure Backup](https://azure.microsoft.com/services/backup/) jest używana do tworzenia kopii zapasowych i odzyskiwania po awarii i współpracuje z [dyskami zarządzanymi](../articles/virtual-machines/windows/managed-disks-overview.md) oraz dyskami niezarządzanymi. Zadanie tworzenia kopii zapasowej można utworzyć z tworzeniem kopii zapasowych opartych na czasie, łatwym przywracanie maszyn wirtualnych i zasadami przechowywania kopii zapasowych.

W przypadku korzystania z [dysków SSD premium,](../articles/virtual-machines/windows/disks-types.md) [dysków zarządzanych](../articles/virtual-machines/windows/managed-disks-overview.md)lub innych typów dysków z opcją [magazynu lokalnie nadmiarowego,](../articles/storage/common/storage-redundancy-lrs.md) szczególnie ważne jest wykonywanie okresowych kopii zapasowych odzyskiwania po awarii. Usługa Azure Backup przechowuje dane w magazynie usług odzyskiwania w celu długoterminowego przechowywania. Wybierz opcję [magazynu geograficznie nadmiarowego](../articles/storage/common/storage-redundancy-grs.md) dla magazynu usług odzyskiwania kopii zapasowych. Ta opcja zapewnia, że kopie zapasowe są replikowane do innego regionu platformy Azure w celu ochrony przed awariami regionalnymi.

W przypadku dysków niezarządzanych można użyć lokalnie nadmiarowego typu magazynu dla dysków IaaS, ale upewnij się, że usługa Azure Backup jest włączona z opcją magazynu geograficznie nadmiarowego dla magazynu usług odzyskiwania.

> [!NOTE]
> Jeśli używasz opcji [magazynu geograficznie nadmiarowego](../articles/storage/common/storage-redundancy-grs.md) lub [dostępu do odczytu geobegobłądowego](../articles/storage/common/storage-redundancy.md) dla dysków niezarządzanych, nadal potrzebujesz spójnych migawek do tworzenia kopii zapasowych i odzyskiwania po awarii. Użyj [kopii zapasowej platformy Azure](https://azure.microsoft.com/services/backup/) lub [spójnych migawek](#alternative-solution-consistent-snapshots).

 Poniższa tabela zawiera podsumowanie rozwiązań dostępnych dla odzyskiwania po awarii.

| Scenariusz | Automatyczna replikacja | Rozwiązanie DR |
| --- | --- | --- |
| Dyski SSD w warstwie Premium | Lokalna[(lokalnie nadmiarowa pamięć masowa)](../articles/storage/common/storage-redundancy-lrs.md) | [Kopia zapasowa platformy Azure](https://azure.microsoft.com/services/backup/) |
| Dyski zarządzane | Lokalna[(lokalnie nadmiarowa pamięć masowa)](../articles/storage/common/storage-redundancy-lrs.md) | [Kopia zapasowa platformy Azure](https://azure.microsoft.com/services/backup/) |
| Niezarządzane lokalnie nadmiarowe dyski magazynu | Lokalna[(lokalnie nadmiarowa pamięć masowa)](../articles/storage/common/storage-redundancy-lrs.md) | [Kopia zapasowa platformy Azure](https://azure.microsoft.com/services/backup/) |
| Niezarządzane geograficznie nadmiarowe dyski pamięci masowej | Region krzyżowy[(magazyn geograficznie nadmiarowy)](../articles/storage/common/storage-redundancy-grs.md) | [Kopia zapasowa platformy Azure](https://azure.microsoft.com/services/backup/)<br/>[Spójne migawki](#alternative-solution-consistent-snapshots) |
| Niezarządzane dyski magazynu geograficznego dostępu do odczytu | Międzyregion[(magazyn geograficzny dostępu do odczytu)](../articles/storage/common/storage-redundancy.md) | [Kopia zapasowa platformy Azure](https://azure.microsoft.com/services/backup/)<br/>[Spójne migawki](#alternative-solution-consistent-snapshots) |

Wysoka dostępność jest najlepiej spełniona przy użyciu dysków zarządzanych w zestawie dostępności wraz z usługą Azure Backup. Jeśli używasz dysków niezarządzanych, nadal można użyć usługi Azure Backup dla odzyskiwania po awarii. Jeśli nie możesz użyć usługi Azure Backup, a następnie wykonywanie [spójnych migawek](#alternative-solution-consistent-snapshots), zgodnie z opisem w dalszej sekcji, jest alternatywnym rozwiązaniem dla kopii zapasowej i odzyskiwania po awarii.

Opcje wysokiej dostępności, tworzenia kopii zapasowych i odzyskiwania po awarii na poziomie aplikacji lub infrastruktury mogą być reprezentowane w następujący sposób:

| Poziom |   Wysoka dostępność   | Kopia zapasowa lub funkcja odzyskiwania po awarii |
| --- | --- | --- |
| Aplikacja | SQL Server AlwaysOn | Azure Backup |
| Infrastruktura    | Zestaw dostępności  | Magazyn geograficznie nadmiarowy ze spójnymi migawkami |

### <a name="using-azure-backup"></a>Korzystanie z usługi Azure Backup 

[Usługa Azure Backup](../articles/backup/backup-azure-vms-introduction.md) może wykonać kopię zapasową maszyn wirtualnych z systemem Windows lub Linux w magazynie usług odzyskiwania platformy Azure. Tworzenie kopii zapasowych i przywracanie danych o znaczeniu krytycznym dla firmy jest skomplikowane przez fakt, że należy utworzyć kopię zapasową danych o znaczeniu krytycznym dla firmy, gdy aplikacje, które generują dane, są uruchomione. 

Aby rozwiązać ten problem, usługa Azure Backup udostępnia kopie zapasowe spójne z aplikacjami dla obciążeń firmy Microsoft. Używa usługi cień woluminu, aby upewnić się, że dane są poprawnie zapisywane do magazynu. W przypadku maszyn wirtualnych z systemem Linux domyślnym trybem spójności kopii zapasowych są kopie zapasowe spójne z plikami, ponieważ system Linux nie ma funkcji równoważnych usłudze cienia woluminów, jak w przypadku systemu Windows. W przypadku komputerów z systemem Linux zobacz [Tworzenie kopii zapasowych maszyn wirtualnych z systemem Azure z systemem Linux .](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent)

![Przepływ kopii zapasowej platformy Azure][1]

Gdy usługa Azure Backup inicjuje zadanie tworzenia kopii zapasowej w zaplanowanym czasie, wyzwala rozszerzenie kopii zapasowej zainstalowane na maszynie Wirtualnej w celu wykonania migawki w czasie. Migawka jest podejmowana w koordynacji z usługą cienia woluminu, aby uzyskać spójną migawkę dysków na maszynie wirtualnej bez konieczności zamykania jej. Rozszerzenie kopii zapasowej na maszynie wirtualnej opróżnia wszystkie zapisy przed podjęciem spójnej migawki wszystkich dysków. Po wykonaniu migawki dane są przesyłane przez usługę Azure Backup do magazynu kopii zapasowych. Aby proces tworzenia kopii zapasowej był bardziej wydajny, usługa identyfikuje i przesyła tylko bloki danych, które uległy zmianie po ostatniej kopii zapasowej.

Aby przywrócić, można wyświetlić dostępne kopie zapasowe za pośrednictwem usługi Azure Backup, a następnie zainicjować przywracanie. Tworzenie i przywracanie kopii zapasowych platformy Azure za pośrednictwem [portalu Azure](https://portal.azure.com/), [za pomocą programu PowerShell](../articles/backup/backup-azure-vms-automation.md)lub przy użyciu [interfejsu wiersza polecenia platformy Azure](/cli/azure/).

### <a name="steps-to-enable-a-backup"></a>Kroki umożliwiające włączenie kopii zapasowej

Wykonaj następujące kroki, aby włączyć kopie zapasowe maszyn wirtualnych przy użyciu [witryny Azure portal](https://portal.azure.com/). Istnieje pewne różnice w zależności od dokładnego scenariusza. Szczegółowe informacje można znaleźć w dokumentacji [usługi Azure Backup.](../articles/backup/backup-azure-vms-introduction.md) Usługa Azure Backup obsługuje również [maszyny wirtualne z dyskami zarządzanymi.](https://azure.microsoft.com/blog/azure-managed-disk-backup/)

1.  Tworzenie magazynu usług odzyskiwania dla maszyny Wirtualnej:

    a. W [witrynie Azure portal](https://portal.azure.com/)przeglądaj **wszystkie zasoby** i znajdź **magazyny usług odzyskiwania**.

    b. W menu **Magazyny usług odzyskiwania** kliknij polecenie **Dodaj** i postępuj zgodnie z instrukcjami, aby utworzyć nowy magazyn w tym samym regionie co maszyna wirtualna. Jeśli na przykład maszyna wirtualna znajduje się w regionie Zachodnie stany USA, wybierz zachodnie stany USA dla przechowalni.

1.  Sprawdź replikację magazynu dla nowo utworzonego magazynu. Uzyskaj dostęp do przechowalni w **obszarze Magazyny usług odzyskiwania** i przejdź do**aktualizacji**konfiguracji**kopii** > zapasowej **właściwości** > . Upewnij się, że opcja **magazynu geograficznie nadmiarowego** jest zaznaczona domyślnie. Ta opcja gwarantuje, że magazyn jest automatycznie replikowany do pomocniczego centrum danych. Na przykład przechowalnia w zachodnie stany USA jest automatycznie replikowana do wschodnich stanów USA.

1.  Skonfiguruj zasady tworzenia kopii zapasowych i wybierz maszynę wirtualną z tego samego interfejsu użytkownika.

1.  Upewnij się, że agent kopii zapasowej jest zainstalowany na maszynie wirtualnej. Jeśli maszyna wirtualna jest tworzona przy użyciu obrazu galerii platformy Azure, agent tworzenia kopii zapasowych jest już zainstalowany. W przeciwnym razie (oznacza to, że jeśli używasz obrazu niestandardowego), użyj instrukcji, aby [zainstalować agenta maszyny wirtualnej na maszynie wirtualnej](../articles/backup/backup-azure-arm-vms-prepare.md#install-the-vm-agent).

1.  Po wykonaniu poprzednich kroków kopia zapasowa jest uruchamiana w regularnych odstępach czasu określonych w zasadach tworzenia kopii zapasowych. W razie potrzeby można wyzwolić pierwszą kopię zapasową ręcznie z pulpitu nawigacyjnego magazynu w witrynie Azure portal.

Aby zautomatyzować usługę Azure Backup przy użyciu skryptów, zobacz [polecenia cmdlet programu PowerShell dla kopii zapasowej maszyny Wirtualnej.](../articles/backup/backup-azure-vms-automation.md)

### <a name="steps-for-recovery"></a>Kroki odzyskiwania

Jeśli trzeba naprawić lub odbudować maszynę wirtualną, można przywrócić maszynę wirtualną z dowolnego punktu odzyskiwania kopii zapasowej w magazynie. Istnieje kilka różnych opcji odzyskiwania:

-   Można utworzyć nową maszynę wirtualną jako reprezentację punktu w czasie kopii zapasowej maszyny Wirtualnej.

-   Można przywrócić dyski, a następnie użyć szablonu dla maszyny Wirtualnej, aby dostosować i odbudować przywróconą maszynę wirtualną.

Aby uzyskać więcej informacji, zobacz instrukcje [dotyczące przywracania maszyn wirtualnych za pomocą portalu Azure.](../articles/backup/backup-azure-arm-restore-vms.md) W tym dokumencie opisano również określone kroki przywracania kopii zapasowych maszyn wirtualnych do sparowanego centrum danych przy użyciu magazynu kopii zapasowych nadmiarowych geograficznie, jeśli występuje awaria w podstawowym centrum danych. W takim przypadku usługa Azure Backup używa usługi Compute z regionu pomocniczego do utworzenia przywróconej maszyny wirtualnej.

Program PowerShell służy również do [tworzenia nowej maszyny Wirtualnej z przywróconych dysków](../articles/backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="alternative-solution-consistent-snapshots"></a>Alternatywne rozwiązanie: spójne migawki

Jeśli nie możesz użyć usługi Azure Backup, możesz zaimplementować własny mechanizm tworzenia kopii zapasowych przy użyciu migawek. Tworzenie spójnych migawek dla wszystkich dysków używanych przez maszynę wirtualną, a następnie replikowanie tych migawek do innego regionu jest skomplikowane. Z tego powodu platforma Azure uważa użycie usługi kopia zapasowa za lepszą opcję niż tworzenie rozwiązania niestandardowego.

Jeśli używasz magazynu geograficznego nadmiarowego dostępu do odczytu/magazynu geograficznie nadmiarowego dla dysków, migawki są automatycznie replikowane do pomocniczego centrum danych. Jeśli używasz lokalnie nadmiarowego magazynu dla dysków, musisz samodzielnie zreplikować dane. Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowej dysków maszyn wirtualnych niezarządzanych przez platformę Azure z migawkami przyrostowymi.](../articles/virtual-machines/windows/incremental-snapshots.md)

Migawka jest reprezentacją obiektu w określonym punkcie w czasie. Migawka wiąże się z rozliczeniami za przyrostowy rozmiar danych, które przechowuje. Aby uzyskać więcej informacji, zobacz [Tworzenie migawki obiektu blob](../articles/storage/blobs/storage-blob-snapshots.md).

### <a name="create-snapshots-while-the-vm-is-running"></a>Tworzenie migawek, gdy maszyna wirtualna jest uruchomiona

Chociaż można zrobić migawkę w dowolnym momencie, jeśli maszyna wirtualna jest uruchomiona, nadal są przesyłane strumieniowo do dysków. Migawki mogą zawierać częściowe operacje, które były w locie. Ponadto jeśli istnieje kilka dysków zaangażowanych, migawki różnych dysków może mieć miejsce w różnym czasie. Te scenariusze mogą powodować migawki, które mają być nieskoordynowane. Ten brak koordynacji jest szczególnie problematyczny w przypadku woluminów rozłożonych, których pliki mogą być uszkodzone, jeśli podczas wykonywania kopii zapasowej wprowadzono zmiany.

Aby uniknąć tej sytuacji, proces tworzenia kopii zapasowej należy wykonać następujące kroki:

1.  Zamrozić wszystkie dyski.

1.  Opróżnij wszystkie oczekujące zapisy.

1.  [Utwórz migawkę obiektu blob](../articles/storage/blobs/storage-blob-snapshots.md) dla wszystkich dysków.

Niektóre aplikacje systemu Windows, takie jak SQL Server, zapewniają skoordynowany mechanizm tworzenia kopii zapasowych za pośrednictwem usługi w tle woluminów w celu tworzenia kopii zapasowych spójnych z aplikacjami. W systemie Linux można użyć narzędzia takiego jak *fsfreeze* do koordynowania dysków. To narzędzie udostępnia kopie zapasowe spójne z plikami, ale nie migawki spójne z aplikacjami. Ten proces jest złożony, dlatego należy rozważyć użycie [usługi Azure Backup](../articles/backup/backup-azure-vms-introduction.md) lub rozwiązania do tworzenia kopii zapasowych innej firmy, które już implementuje tę procedurę.

Poprzedni proces powoduje zbieranie skoordynowanych migawek dla wszystkich dysków maszyn wirtualnych, reprezentujący określony widok punktu w czasie maszyny Wirtualnej. Jest to punkt przywracania kopii zapasowej dla maszyny Wirtualnej. Proces można powtarzać w zaplanowanych odstępach czasu, aby utworzyć okresowe kopie zapasowe. Zobacz [Kopiowanie kopii zapasowych do innego regionu,](#copy-the-snapshots-to-another-region) aby uzyskać instrukcje kopiowania migawek do innego regionu odzyskiwania po awarii.

### <a name="create-snapshots-while-the-vm-is-offline"></a>Tworzenie migawek, gdy maszyna wirtualna jest w trybie offline

Inną opcją tworzenia spójnych kopii zapasowych jest zamknięcie maszyny Wirtualnej i wykonywanie migawek obiektów blob każdego dysku. Robienie migawek obiektów blob jest łatwiejsze niż koordynowanie migawek uruchomionej maszyny Wirtualnej, ale wymaga kilku minut przestoju.

1. Zamknij maszynę wirtualną.

1. Utwórz migawkę każdego obiektu blob wirtualnego dysku twardego, który zajmuje tylko kilka sekund.

    Aby utworzyć migawkę, można użyć programu [PowerShell](../articles/storage/common/storage-powershell-guide-full.md), [interfejsu API REST usługi Azure Storage,](https://msdn.microsoft.com/library/azure/ee691971.aspx)interfejsu [wiersza polecenia platformy Azure](/cli/azure/)lub jednej z bibliotek klienta usługi Azure Storage, takiej jak [biblioteka klienta magazynu dla platformy .NET](https://msdn.microsoft.com/library/azure/hh488361.aspx).

1. Uruchom maszynę wirtualną, która kończy przestój. Zazwyczaj cały proces kończy się w ciągu kilku minut.

Ten proces daje kolekcję spójnych migawek dla wszystkich dysków, zapewniając punkt przywracania kopii zapasowej dla maszyny Wirtualnej.

### <a name="copy-the-snapshots-to-another-region"></a>Kopiowanie migawek do innego regionu

Samo tworzenie migawek może nie być wystarczające dla odzyskiwania po awarii. Należy również replikować kopie zapasowe migawki do innego regionu.

Jeśli używasz magazynu geograficznie nadmiarowego lub dostępu do odczytu magazynu geograficznego dla dysków, migawki są automatycznie replikowane do regionu pomocniczego. Może być kilka minut opóźnienia przed replikacją. Jeśli podstawowe centrum danych ustępuje przed zakończeniem replikowania migawek, nie można uzyskać dostępu do migawek z pomocniczego centrum danych. Prawdopodobieństwo tego jest niewielkie.

> [!NOTE]
> Tylko dyski w magazynie geograficznie nadmiarowym lub dostęp do odczytu geobegob nadmiarowego konta magazynu nie chroni maszyny Wirtualnej przed awariami. Należy również utworzyć skoordynowane migawki lub użyć usługi Azure Backup. Jest to wymagane do odzyskania maszyny Wirtualnej do stanu spójnego.

Jeśli używasz magazynu lokalnie nadmiarowego, należy skopiować migawki do innego konta magazynu natychmiast po utworzeniu migawki. Miejsce docelowe kopiowania może być lokalnie nadmiarowym kontem magazynu w innym regionie, co spowoduje, że kopia jest w regionie zdalnym. Migawkę można również skopiować na konto magazynu geograficznego dostępu do odczytu w tym samym regionie. W takim przypadku migawka jest leniwie replikowane do zdalnego regionu pomocniczego. Kopia zapasowa jest chroniona przed awariami w lokacji głównej po zakończeniu kopiowania i replikacji.

Aby skutecznie skopiować przyrostowe migawki odzyskiwania po awarii, zapoznaj się z instrukcjami w [obszarze Tworzenie kopii zapasowej niezarządzanych dysków maszyn wirtualnych platformy Azure z migawkami przyrostowymi](../articles/virtual-machines/windows/incremental-snapshots.md).

![Tworzenie kopii zapasowych niezarządzanych dysków maszyn wirtualnych platformy Azure z migawkami przyrostowymi][2]

### <a name="recovery-from-snapshots"></a>Odzyskiwanie z migawek

Aby pobrać migawkę, skopiuj ją, aby nawiązać nowy obiekt blob. Jeśli kopiujesz migawkę z konta podstawowego, możesz skopiować migawkę do podstawowego obiektu blob migawki. Ten proces przywraca dysk do migawki. Ten proces jest znany jako promowanie migawki. W przypadku kopiowania kopii zapasowej migawki z konta pomocniczego w przypadku konta magazynu geograficznego dostępu do odczytu należy skopiować ją na konto podstawowe. Migawkę można skopiować [za pomocą programu PowerShell](../articles/storage/common/storage-powershell-guide-full.md) lub za pomocą narzędzia AzCopy. Aby uzyskać więcej informacji, zobacz [Przenoszenie danych za pomocą narzędzia wiersza polecenia AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy).

W przypadku maszyn wirtualnych z wieloma dyskami należy skopiować wszystkie migawki, które są częścią tego samego skoordynowanego punktu przywracania. Po skopiowaniu migawek do zapisywalnych obiektów blob vhd można użyć obiektów blob do ponownego tworzenia maszyny wirtualnej przy użyciu szablonu dla maszyny Wirtualnej.

## <a name="other-options"></a>Inne opcje

### <a name="sql-server"></a>SQL Server

Program SQL Server uruchomiony na maszynie wirtualnej ma własne wbudowane możliwości do utworzenia kopii zapasowej bazy danych programu SQL Server do magazynu obiektów Blob platformy Azure lub udziału plików. Jeśli konto magazynu jest magazynem geograficzno nadmiarowym lub magazynem geograficznym dostępu do odczytu, można uzyskać dostęp do tych kopii zapasowych w pomocniczym centrum danych konta magazynu w przypadku awarii, z tymi samymi ograniczeniami, które zostały wcześniej omówione. Aby uzyskać więcej informacji, zobacz [Kopię zapasową i przywracanie programu SQL Server na maszynach wirtualnych platformy Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md). Oprócz kopii zapasowej i [przywracania, grupy dostępności programu SQL Server AlwaysOn](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md) mogą obsługiwać pomocnicze repliki baz danych. Ta zdolność znacznie skraca czas odzyskiwania po awarii.

## <a name="other-considerations"></a>Inne zagadnienia

W tym artykule omówiono sposób tworzenia kopii zapasowych lub wykonywania migawek maszyn wirtualnych i ich dysków w celu obsługi odzyskiwania po awarii oraz sposobu używania tych kopii zapasowych lub migawek do odzyskiwania danych. W modelu usługi Azure Resource Manager wiele osób używa szablonów do tworzenia maszyn wirtualnych i innych infrastruktur na platformie Azure. Za pomocą szablonu można utworzyć maszynę wirtualną, która ma tę samą konfigurację za każdym razem. Jeśli używasz obrazów niestandardowych do tworzenia maszyn wirtualnych, należy również upewnić się, że obrazy są chronione przy użyciu konta magazynu geograficznego dostępu do odczytu do ich przechowywania.

W związku z tym proces tworzenia kopii zapasowej może być kombinacją dwóch rzeczy:

- Zapasowy danych (dysków).
- Konfigurowanie kopii zapasowej (szablony i obrazy niestandardowe).

W zależności od opcji tworzenia kopii zapasowej, które wybierzesz, może być do obsługi kopii zapasowej danych i konfiguracji lub usługa tworzenia kopii zapasowej może obsłużyć to wszystko dla Ciebie.

## <a name="appendix-understanding-the-impact-of-data-redundancy"></a>Dodatek: Opis wpływu nadmiarowości danych

W przypadku kont magazynu na platformie Azure istnieją trzy typy nadmiarowości danych, które należy wziąć pod uwagę w odniesieniu do odzyskiwania po awarii: lokalnie nadmiarowe, geograficznie nadmiarowe lub geograficznie nadmiarowe z dostępem do odczytu. 

Lokalnie nadmiarowy magazyn zachowuje trzy kopie danych w tym samym centrum danych. Gdy maszyna wirtualna zapisuje dane, wszystkie trzy kopie są aktualizowane przed powodzenie jest zwracany do wywołującego, więc wiesz, że są identyczne. Dysk jest chroniony przed lokalnymi awariami, ponieważ jest mało prawdopodobne, że wszystkie trzy kopie są zagrożone w tym samym czasie. W przypadku magazynu lokalnie nadmiarowego nie ma nadmiarowości geograficznej, więc dysk nie jest chroniony przed katastrofalnymi awariami, które mogą mieć wpływ na całe centrum danych lub jednostkę magazynu.

Dzięki magazynowi geograficzno nadmiarowego i geograficznemu dostępowi do odczytu trzy kopie danych są zachowywane w wybranym przez Ciebie regionie podstawowym. Trzy kolejne kopie danych są przechowywane w odpowiednim regionie pomocniczym, który jest ustawiany przez platformę Azure. Na przykład jeśli przechowujesz dane w zachodnie stany USA, dane są replikowane do wschodnich stanów USA. Przechowywanie kopii odbywa się asynchronicznie i występuje niewielkie opóźnienie między aktualizacjami lokacji podstawowych i dodatkowych. Repliki dysków w lokacji dodatkowej są spójne dla poszczególnych dysków (z opóźnieniem), ale repliki wielu aktywnych dysków mogą nie być ze sobą zsynchronizowane. Aby mieć spójne repliki na wielu dyskach, potrzebne są spójne migawki.

Główną różnicą między magazynem geograficznym a magazynem geograficznym dostępu do odczytu jest to, że w przypadku magazynu geograficznego dostępu do odczytu można odczytać kopię pomocniczą w dowolnym momencie. Jeśli występuje problem, który sprawia, że dane w regionie podstawowym są niedostępne, zespół platformy Azure dokłada wszelkich starań, aby przywrócić dostęp. Gdy podstawowy jest w dół, jeśli masz dostęp do odczytu geobegob nadmiarowego magazynu włączone, można uzyskać dostęp do danych w pomocniczym centrum danych. W związku z tym jeśli planujesz odczytać z repliki, gdy podstawowy jest niedostępny, a następnie dostęp do odczytu magazynu geograficznego nadmiarowego należy wziąć pod uwagę.

Jeśli okaże się, że jest to znacząca awaria, zespół platformy Azure może wyzwolić geo-failover i zmienić podstawowe wpisy DNS, aby wskazywały magazyn pomocniczy. W tym momencie, jeśli masz magazyn geograficznie nadmiarowy lub dostęp do odczytu magazynu geograficznego nadmiarowego włączone, można uzyskać dostęp do danych w regionie, który był kiedyś pomocniczy. Innymi słowy, jeśli konto magazynu jest magazynem geograficznie nadmiarowym i występuje problem, można uzyskać dostęp do magazynu pomocniczego tylko wtedy, gdy istnieje geo-failover.

Aby uzyskać więcej informacji, zobacz [Co zrobić po wystąpieniu awarii usługi Azure Storage](../articles/storage/common/storage-disaster-recovery-guidance.md).

>[!NOTE] 
>Firma Microsoft kontroluje, czy nastąpi przewijanie awaryjne. Przewijalnia awaryjna nie jest kontrolowana na konto magazynu, więc nie jest podejmowana przez klientów indywidualnych. Aby zaimplementować odzyskiwanie po awarii dla określonych kont magazynu lub dysków maszyn wirtualnych, należy użyć technik opisanych wcześniej w tym artykule.

[1]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png
