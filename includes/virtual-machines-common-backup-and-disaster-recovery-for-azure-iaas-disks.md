---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: luywang
ms.service: storage
ms.topic: include
ms.date: 06/05/2018
ms.author: luywang
ms.custom: include file
ms.openlocfilehash: 03db1bf84e200d8b66f0395cbd96813e2248eefe
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34806370"
---
# <a name="backup-and-disaster-recovery-for-azure-iaas-disks"></a>Kopia zapasowa i odzyskiwanie po awarii dla dysków IaaS platformy Azure

W tym artykule opisano sposób planowania tworzenia kopii zapasowej i odzyskiwanie po awarii (DR) dysków na platformie Azure i maszyny wirtualne IaaS (VM). W tym dokumencie opisano dysków zarządzane i niezarządzane.

Po pierwsze firma Microsoft obejmuje możliwości tolerancji błędów wbudowanych w platformy Azure, która pomaga chronić przed awariami lokalnego. Następnie omówiono w scenariuszach po awarii nie są w pełni objęte wbudowanych możliwości. Możemy również Pokaż przykłady scenariuszy obciążenie, gdzie można zastosować innej kopii zapasowej i odzyskiwania po awarii zagadnienia. Następnie analizujemy możliwych rozwiązań do obsługi dysków DR IaaS. 

## <a name="introduction"></a>Wprowadzenie

Platforma Azure używa różne metody nadmiarowość i odporność na uszkodzenia w celu ochrony klientów przed awariami sprzętu zlokalizowanego. Błędy lokalne mogą obejmować problemów z maszyną serwera usługi Azure Storage, która przechowuje dane dla dysku wirtualnego lub w przypadku awarii dysków SSD i HDD na tym serwerze. Takie błędy składnika izolowanego sprzętu może nastąpić podczas wykonywania normalnych operacji. 

Platforma Azure została zaprojektowana jako odporność na awarie tych. Główne awarii może spowodować błędy lub inaccessibility wiele serwerów magazynu lub nawet całe centrum danych. Chociaż maszyn wirtualnych i dyski są zwykle chronione zlokalizowanych awarii, dodatkowe kroki są niezbędne do ochrony obciążenia poważnej awarii całej region takich jak poważnej awarii, które mogą wpłynąć na Twojej maszyny Wirtualnej i dysków.

Oprócz możliwości platformy błędów mogą wystąpić problemy z aplikacji klienta lub danych. Na przykład nowa wersja aplikacji może przypadkowo zmiany danych, powodujący przerwanie. W takim przypadku można przywrócić aplikacji i danych do poprzednich wersji, który zawiera ostatniego znanego dobrego stanu. Wymagane jest utrzymanie regularnych kopii zapasowych.

Regionalnej awarii należy wykonać kopię zapasową dysków maszyn wirtualnych IaaS w innym regionie. 

Zanim przyjrzymy tworzenia kopii zapasowych i odzyskiwania po awarii opcje Załóżmy recap kilku dostępnych metod obsługi zlokalizowanych awarii.

### <a name="azure-iaas-resiliency"></a>Odporność IaaS platformy Azure

*Odporność* odwołuje się do uszkodzenia normalne błędów występujących w składników sprzętowych. Odporność jest możliwość skutków błędów i kontynuować działanie. Nie jest o unikanie błędów, ale odpowiadać na awarie w taki sposób, który pozwala uniknąć przestoju lub utraty danych. Celem odporności jest przywrócenie aplikacji do stanu pełnej funkcjonalności po wystąpieniu awarii. Maszyny wirtualne platformy Azure i dyski mają na celu zapewnienie odporności na typowych błędów sprzętu. Oto jak platforma Azure IaaS zapewnia to elastyczność.

Maszyna wirtualna składa się głównie z dwóch części: serwera obliczeń i stałe dyski. Wpływ zarówno na odporność na uszkodzenia maszyny wirtualnej.

Jeśli serwer hosta obliczeń platformy Azure, która przechowuje maszyny Wirtualnej wystąpi awaria sprzętowa, która jest rzadko, Azure jest przeznaczona na automatyczne przywracanie maszyny Wirtualnej na innym serwerze. Jeśli w tym scenariuszu, Twoje ponowne uruchomienie komputera i maszyny Wirtualnej wróci do sprawności po pewnym czasie. Azure automatycznie wykrywa takie awarie sprzętu i wykonuje odzyskiwanie, aby pomóc, upewnij się, że odbiorcy maszyny Wirtualnej jest dostępna tak szybko, jak to możliwe.

Dotyczących dysków IaaS trwałości danych ma kluczowe znaczenie dla platformy magazynu trwałego. Azure klienci mają ważnymi aplikacji uruchomionych na IaaS i są one zależne od trwałość danych. Ochrona Azure projektów tych dysków IaaS z trzech nadmiarowe kopie danych, który jest przechowywany lokalnie. Te kopie zapewniają wysoką trwałością przed awariami lokalnego. W przypadku niepowodzenia jednym ze składników sprzętu, które przechowuje dysku maszyny Wirtualnej nie występuje, ponieważ istnieją dwie kopie dodatkowe do obsługi żądań dysku. Działa on prawidłowo, nawet w przypadku awarii dwóch komponenty sprzętowe, które obsługują dysku w tym samym czasie (która jest rzadko). 

Aby zapewnić, że zawsze Obsługa trzy repliki, Magazyn Azure automatycznie spowoduje utworzenie nowej kopii w tle dane, jeśli jeden z trzech kopiuje staje się niedostępna. W związku z tym nie należy używać RAID z dyskami Azure odporności na uszkodzenia. Proste RAID 0 konfiguracji powinny być wystarczające stosowanie dysków, jeśli to konieczne utworzyć większych woluminów.

Z powodu tej architektury Azure spójnie wydał korporacyjnej trwałości dla IaaS z branży dysków zera [współczynnik awaryjności firmie](https://en.wikipedia.org/wiki/Annualized_failure_rate).

Usterki sprzętu zlokalizowanych na mocy obliczeniowej hosta lub w platformie magazynu mogą czasami wynik w tymczasowej niedostępności maszyny wirtualnej, który pasuje do żadnego [umowy SLA platformy Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) dostępności maszyny Wirtualnej. Platforma Azure udostępnia również branży umowa SLA dla pojedynczego wystąpień maszyn wirtualnych, które używają dysków Azure Premium SSD.

Aby chronić obciążeń aplikacji z przestoju z powodu tymczasowej niedostępności dysku lub maszyny Wirtualnej, klienci mogą używać [zestawów dostępności](../articles/virtual-machines/windows/manage-availability.md). Co najmniej dwie maszyny wirtualne w zestawie dostępności zapewnić nadmiarowość aplikacji. Azure utworzy tych maszyn wirtualnych i dyski w domenach awarii oddzielne z różnych składników zasilania, sieci i serwera. 

Z powodu tych domen błędów oddzielne awarie sprzętowe zlokalizowanego zwykle nie wpływają na wiele maszyn wirtualnych w zestawie w tym samym czasie. Posiadanie domen błędów oddzielne zapewnia wysoką dostępność aplikacji. Uwzględniono dobrym rozwiązaniem jest korzystanie z zestawów dostępności, gdy wymagana jest wysoka dostępność. Następna sekcja obejmuje aspekt odzyskiwania po awarii.

### <a name="backup-and-disaster-recovery"></a>Kopia zapasowa i odzyskiwanie po awarii

Odzyskiwanie po awarii jest możliwość odzyskania z rzadko, ale głównych, zdarzenia. Zdarzenia te obejmują błędy z systemem innym niż przejściowy, całej skali, takie jak zakłócenia, która wpływa na całą regionu. Odzyskiwanie po awarii obejmuje kopii zapasowej danych i archiwizowania i może zawierać ręcznej interwencji, takich jak przywracanie z kopii zapasowej bazy danych.

Platformy Azure wbudowaną ochronę przed awariami zlokalizowanych nie może całkowicie ochrona maszyn wirtualnych i/lub dysków, jeśli poważnej awarii spowoduje przestojami na dużą skalę. Te awarii na dużą skalę obejmują krytycznego zdarzenia, takie jak Jeśli centrum danych przez huragan, trzęsienia ziemi, fire lub w przypadku awarii sprzętu na dużą skalę jednostki. Ponadto mogą wystąpić błędy z powodu problemy dotyczące danych lub aplikacji.

Aby lepiej chronić obciążeń IaaS z awarie, należy zaplanować nadmiarowości i mieć kopie zapasowe, aby umożliwić odzyskiwanie. Podczas odzyskiwania po awarii należy wykonać kopię zapasową w innej lokalizacji geograficznej od lokacji głównej. Takie podejście ułatwia, upewnij się, że kopia zapasowa nie ma wpływu na tego samego zdarzenia, który pierwotnie wpływ na maszynie Wirtualnej lub dysków. Aby uzyskać więcej informacji, zobacz [odzyskiwania po awarii dla aplikacji Azure](/azure/architecture/resiliency/disaster-recovery-azure-applications).

Uwagi dotyczące programu DR może obejmować następujące aspekty:

- Wysoka dostępność: możliwości aplikacji mogą nadal działać w dobrej kondycji, bez przestojów znaczących. Przez *dobrej kondycji*, ten stan oznacza, że aplikacja jest elastyczny i użytkownicy mogą połączyć się z aplikacją i korzystać z niego. Niektórych kluczowych aplikacji i baz danych może być konieczne być zawsze dostępny, nawet jeśli błędy występują w platformie. Dla tych zadań konieczne może być planowania nadmiarowości na potrzeby aplikacji, a także dane.

- Trwałość danych: W niektórych przypadkach głównym zagadnieniem jest zapewnienie, że dane zostaną zachowane w przypadku awarii. W związku z tym może być konieczne kopii zapasowej danych w innej lokacji. W przypadku takich obciążeń może nie być konieczny pełne nadmiarowości na potrzeby aplikacji, ale tylko zwykłych kopii zapasowych dysków.

## <a name="backup-and-dr-scenarios"></a>Scenariusze tworzenia kopii zapasowych i odzyskiwania po awarii

Oto kilka przykładów typowych scenariuszy obciążenia aplikacji i zagadnienia dotyczące planowania dla odzyskiwania po awarii.

### <a name="scenario-1-major-database-solutions"></a>Scenariusz 1: Rozwiązań głównej bazy danych

Należy wziąć pod uwagę produkcji serwer bazy danych, takich jak SQL Server lub Oracle, który może obsługiwać wysokiej dostępności. Krytyczne produkcji aplikacji i użytkowników są zależne od tej bazy danych. Plan odzyskiwania po awarii dla tego systemu może być konieczne obsługuje następujące wymagania:

- Dane muszą być chronione i możliwe do odzyskania.
- Serwer musi być dostępny do użycia.

Plan odzyskiwania po awarii może wymagać utrzymania repliki bazy danych w innym regionie jako kopii zapasowej. W zależności od wymagań dotyczących dostępności serwera i odzyskiwanie danych rozwiązanie może należeć do zakresu od lokacji repliki aktywny aktywny lub aktywny / pasywny do okresowe tworzenie kopii zapasowych danych. Relacyjnych baz danych, takich jak SQL Server i Oracle, podaj różne opcje replikacji. Dla programu SQL Server, użyj [grup dostępności AlwaysOn programu SQL Server](https://msdn.microsoft.com/library/hh510230.aspx) wysokiej dostępności.

Bazy danych NoSQL, takie jak bazy danych MongoDB, obsługują również [replik](https://docs.mongodb.com/manual/replication/) nadmiarowości. Używane są repliki wysokiej dostępności.

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>Scenariusz 2: Klastra nadmiarowe maszyny wirtualne

Należy wziąć pod uwagę obciążenia obsługiwane przez klaster maszyn wirtualnych, które zapewniają nadmiarowość i równoważenie obciążenia. Przykładem jest klastrem Cassandra wdrożone w regionie. Ten typ architektury już zapewnia wysoki poziom nadmiarowości w tym regionie. Jednak aby chronić obciążenia w przypadku awarii na poziomie regionalnym, należy rozważyć rozpraszania klastra w dwóch regionach lub wprowadzanie okresowe kopie zapasowe w innym regionie.

### <a name="scenario-3-iaas-application-workload"></a>Scenariusz 3: IaaS aplikacji obciążenia

Przyjrzyjmy się IaaS obciążenia aplikacji. Na przykład ta aplikacja może być produkcji pracą maszyny Wirtualnej platformy Azure. Może być serwerem sieci web lub serwera plików zawartości i innych zasobów w lokacji. Można ją również aplikacji biznesowej niestandardowej uruchomione na maszynie Wirtualnej, które przechowywane jego dane, zasobów i stan aplikacji dysków maszyny Wirtualnej. W takim przypadku koniecznie upewnij się, że możesz korzystać z kopii zapasowych na bieżąco. Częstotliwość wykonywania kopii zapasowych powinny być oparte na rodzaju obciążenia maszyny Wirtualnej. Na przykład jeśli aplikacja jest uruchamiana codziennie i modyfikuje danych, następnie kopii zapasowej należy podjąć co godzinę.

Innym przykładem jest serwer raportowania, który pobiera dane z innych źródeł i generuje raporty zagregowanych. Utrata tej maszyny Wirtualnej lub dysków mogą spowodować utratę raportów. Jednak może być możliwe ponownie uruchomić proces raportowania i ponownie wygenerować danych wyjściowych. W takim przypadku naprawdę masz utrata danych, nawet jeśli serwer raportowania o awarii. W związku z tym może być wyższy poziom tolerancji utraty części danych na serwerze raportowania. W takim przypadku mniej częste wykonywanie kopii zapasowych są opcję, aby zmniejszyć koszty.

### <a name="scenario-4-iaas-application-data-issues"></a>Scenariusz 4: Problemy z danych aplikacji IaaS

Problemy z danych aplikacji IaaS są inną możliwością. Należy wziąć pod uwagę aplikacji, która oblicza, przechowuje i służy krytyczne komercyjnych danych, takie jak informacje o cenach. Nowa wersja aplikacji ma usterkę oprogramowania, która niepoprawnie obliczana, ceny i uszkodzone dane commerce obsługiwane przez platformę. W tym miejscu najlepszy plan działania jest przywrócenie poprzedniej wersji aplikacji i danych. Aby włączyć tę opcję, należy wykonać okresowe kopie zapasowe systemu.

## <a name="disaster-recovery-solution-azure-backup"></a>Rozwiązanie odzyskiwania po awarii: kopia zapasowa Azure 

[Kopia zapasowa Azure](https://azure.microsoft.com/services/backup/) służy do tworzenia kopii zapasowych i odzyskiwania po awarii i działa z [dyskach zarządzanych](../articles/virtual-machines/windows/managed-disks-overview.md) oraz [niezarządzanych dysków](../articles/virtual-machines/windows/about-disks-and-vhds.md#unmanaged-disks). Zadanie tworzenia kopii zapasowej można utworzyć na podstawie czasu tworzenia kopii zapasowych, łatwe przywrócenie maszyny Wirtualnej i zasady przechowywania kopii zapasowych. 

Jeśli używasz [dysków Premium SSD](../articles/virtual-machines/windows/premium-storage.md), [dyskach zarządzanych](../articles/virtual-machines/windows/managed-disks-overview.md), lub innych typów dysku z [magazyn lokalnie nadmiarowy](../articles/storage/common/storage-redundancy-lrs.md) opcji jest szczególnie ważne tworzyć kopie zapasowe okresowe odzyskiwania po awarii. Kopia zapasowa Azure przechowuje dane w magazynie usług odzyskiwania w celu przechowywania długoterminowego. Wybierz [magazynu geograficznie nadmiarowego](../articles/storage/common/storage-redundancy-grs.md) opcja w przypadku odzyskiwania kopii zapasowej usług magazynu. Ta opcja zapewnia, że kopie zapasowe są replikowane w innym regionie Azure, zabezpieczenia z regionalnej awarii.

Dla [niezarządzanych dysków](../articles/virtual-machines/windows/about-disks-and-vhds.md#unmanaged-disks), można użyć typu Magazyn lokalnie nadmiarowy dysków IaaS, ale upewnij się, że kopia zapasowa Azure jest włączone z opcją magazynu geograficznie nadmiarowego magazynu usług odzyskiwania.

> [!NOTE]
> Jeśli używasz [magazynu geograficznie nadmiarowego](../articles/storage/common/storage-redundancy-grs.md) lub [dostęp do odczytu magazynu geograficznie nadmiarowego](../articles/storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) opcji dla niezarządzanego dysków, można nadal konieczne migawki spójne z kopii zapasowej i odzyskiwania po awarii. Użyj jednej [kopia zapasowa Azure](https://azure.microsoft.com/services/backup/) lub [migawki spójne z](#alternative-solution-consistent-snapshots).

 Poniższa tabela znajduje się podsumowanie rozwiązania, które są dostępne do odzyskiwania po awarii.

| Scenariusz | Automatyczne replikacji | Rozwiązanie odzyskiwania po awarii |
| --- | --- | --- |
| Dyski SSD w warstwie Premium | Lokalne ([magazyn lokalnie nadmiarowy](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Dyski zarządzane | Lokalne ([magazyn lokalnie nadmiarowy](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Niezarządzane dysków magazyn lokalnie nadmiarowy | Lokalne ([magazyn lokalnie nadmiarowy](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Dyski niezarządzane magazynu geograficznie nadmiarowego | Krzyżowe region ([magazynu geograficznie nadmiarowego](../articles/storage/common/storage-redundancy-grs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Migawki spójne z](#alternative-solution-consistent-snapshots) |
| Dyski magazynu geograficznie nadmiarowego niezarządzanego dostęp do odczytu | Krzyżowe region ([dostęp do odczytu magazynu geograficznie nadmiarowego](../articles/storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Migawki spójne z](#alternative-solution-consistent-snapshots) |

Wysoka dostępność najlepiej można spełnić przez użycie dysków zarządzanych w zestawie wraz z usługą kopia zapasowa Azure dostępności. Użycie dysków niezarządzane, można nadal używać kopia zapasowa Azure do odzyskiwania po awarii. Jeśli nie można użyć usługi Kopia zapasowa Azure, następnie biorąc [migawek spójnych](#alternative-solution-consistent-snapshots), zgodnie z opisem w dalszej części artykułu alternatywne rozwiązanie do tworzenia kopii zapasowej i odzyskiwania po awarii.

Opcje wysokiej dostępności, tworzenia kopii zapasowej i odzyskiwania po awarii na poziomie aplikacji lub infrastruktury może być reprezentowany w następujący sposób:

| Poziom |   Wysoka dostępność   | Tworzenia kopii zapasowej lub odzyskiwania po awarii |
| --- | --- | --- |
| Aplikacja | SQL Server AlwaysOn | Azure Backup |
| Infrastruktura    | Zestaw dostępności  | Magazyn geograficznie nadmiarowy z migawki spójne z |

### <a name="using-azure-backup"></a>Przy użyciu kopii zapasowej systemu Azure 

[Kopia zapasowa Azure](../articles/backup/backup-azure-vms-introduction.md) może tworzyć kopie zapasowe maszyn wirtualnych systemu Windows lub Linux do odzyskiwania systemu Azure usługi magazynu. Tworzenie kopii zapasowej i przywracanie danych biznesowych o znaczeniu krytycznym skomplikowane jest fakt, że strategicznych danych biznesowych kopie zapasowe uruchomionej aplikacji, które tworzą dane. 

Aby rozwiązać ten problem, kopia zapasowa Azure zapewnia spójne z aplikacją kopii zapasowych obciążeń firmy Microsoft. Aby upewnić się, poprawnie zapisywania danych do magazynu używa usługi woluminów w tle. Dla maszyn wirtualnych systemu Linux tylko plik spójne kopie zapasowe są możliwe, ponieważ systemu Linux nie ma funkcji odpowiednikiem usługi woluminów w tle.

![Przepływ kopia zapasowa Azure][1]

Jeśli kopia zapasowa Azure inicjuje zadania tworzenia kopii zapasowej w zaplanowanym terminie, wyzwala kopii zapasowej rozszerzenia zainstalowane na maszynie wirtualnej, aby utworzyć migawkę punktu w czasie. Migawki w połączeniu z usługą woluminów w tle można pobrać migawki spójne z dysków w maszynie wirtualnej bez konieczności go zamknąć. Zapasowy numer wewnętrzny w maszynie Wirtualnej opróżnienia wszystkich zapisy przed wykonaniem migawki spójne wszystkie dyski. Po wykonaniu migawki, dane są przesyłane przez kopię zapasową Azure do magazynu kopii zapasowych. Aby proces tworzenia kopii zapasowej bardziej wydajne, usługa identyfikuje i transferuje tylko bloki danych, które zostały zmienione po utworzeniu ostatniej kopii zapasowej.

Aby przywrócić, można wyświetlić dostępnych kopii zapasowych za pomocą usługi Kopia zapasowa Azure i inicjuje operację przywracania. Możesz utworzyć i przywracania kopii zapasowych Azure za pośrednictwem [portalu Azure](https://portal.azure.com/), przez [przy użyciu programu PowerShell](../articles/backup/backup-azure-vms-automation.md), lub za pomocą [interfejsu wiersza polecenia Azure](/cli/azure/). 

### <a name="steps-to-enable-a-backup"></a>Kroki umożliwiające włączenie kopii zapasowej

Wykonaj następujące kroki, aby włączyć kopie zapasowe maszyn wirtualnych za pomocą [portalu Azure](https://portal.azure.com/). Różnice w zależności od danego scenariusza dokładne nie istnieje. Zapoznaj się [kopia zapasowa Azure](../articles/backup/backup-azure-vms-introduction.md) dokumentacji, aby uzyskać szczegółowe informacje. Azure Backup również [obsługuje maszyny wirtualne z dyskami zarządzanych](https://azure.microsoft.com/blog/azure-managed-disk-backup/).

1.  Tworzenie magazynu usług odzyskiwania dla maszyny Wirtualnej:

    a. W [portalu Azure](https://portal.azure.com/), Przeglądaj **wszystkie zasoby** i Znajdź **Magazyny usług odzyskiwania**.

    b. Na **Magazyny usług odzyskiwania** menu, kliknij przycisk **Dodaj** i wykonaj kroki, aby utworzyć nowy magazyn, w tym samym regionie co maszyna wirtualna. Na przykład jeśli maszyna wirtualna znajduje się w regionie zachodnie stany USA, wybierz zachodnie stany USA magazynu.

2.  Sprawdź replikacji magazynu dla nowo utworzonego magazynu. Uzyskać dostęp do magazynu w obszarze **Magazyny usług odzyskiwania** i przejdź do **ustawienia** > **konfiguracji kopii zapasowej**. Upewnij się, **magazynu geograficznie nadmiarowego** opcja jest domyślnie zaznaczona. Ta opcja zapewnia, że magazyn jest automatycznie replikowane do dodatkowego centrum danych. Na przykład magazynu w zachodnie stany USA, są automatycznie replikowane do wschodnie stany USA.

3.  Konfigurowanie zasad tworzenia kopii zapasowej, a następnie wybierz maszynę Wirtualną z tego samego interfejsu użytkownika.

4.  Upewnij się, że zainstalowano agenta kopii zapasowej na maszynie Wirtualnej. Jeśli maszyna wirtualna została utworzona za pomocą galerii Azure, Agent kopii zapasowej już jest zainstalowany. W przeciwnym razie (Jeśli używasz niestandardowego obrazu), postępuj zgodnie z instrukcjami, aby [Zainstaluj agenta maszyny Wirtualnej na maszynie wirtualnej](../articles/backup/backup-azure-arm-vms-prepare.md#install-the-vm-agent-on-the-virtual-machine).

5.  Upewnij się, że maszyna wirtualna zezwala na połączenie sieciowe dla usługi tworzenia kopii zapasowej do funkcji. Postępuj zgodnie z instrukcjami dotyczącymi [połączeń sieciowych](../articles/backup/backup-azure-arm-vms-prepare.md#establish-network-connectivity).

6.  Po wykonaniu poprzednich kroków tworzenia kopii zapasowej w regularnych odstępach czasu określonych w zasadach tworzenia kopii zapasowej. W razie potrzeby możesz wyzwolić pierwszej kopii zapasowej ręcznie z pulpitem nawigacyjnym magazynu w portalu Azure.

Automatyzacja kopia zapasowa Azure za pomocą skryptów, można znaleźć w temacie [poleceń cmdlet programu PowerShell dla kopii zapasowej maszyny Wirtualnej](../articles/backup/backup-azure-vms-automation.md).

### <a name="steps-for-recovery"></a>Procedura odzyskiwania

Jeśli musisz naprawić lub ponownie skompiluj maszyny Wirtualnej, można przywrócić maszyny Wirtualnej z żadnym z punktów odzyskiwania kopii zapasowej w magazynie. Istnieje kilka różnych opcji do wykonania odzyskiwania:

-   Można utworzyć nowej maszyny Wirtualnej jako punktu w czasie reprezentacji kopii zapasowej maszyny wirtualnej.

-   Można przywrócić dyski, a następnie dostosować i skompiluj ponownie przywróconą maszyną Wirtualną przy użyciu szablonu maszyny wirtualnej. 

Aby uzyskać więcej informacji, zobacz instrukcje dotyczące [przywracanie maszyn wirtualnych za pomocą portalu Azure](../articles/backup/backup-azure-arm-restore-vms.md). Tym dokumencie opisano również wykonania określonych kroków przywracania kopii zapasowej maszyn wirtualnych do sparowanego centrum danych przy użyciu geograficznie nadmiarowego magazynu kopii zapasowej, jeśli istnieje awarii podstawowego centrum danych. W takim przypadku kopia zapasowa Azure korzysta z usługi obliczeniowe z regionu pomocniczego do tworzenia przywróconej maszyny wirtualnej.

Można również użyć programu PowerShell dla [przywracanie maszyny Wirtualnej](../articles/backup/backup-azure-arm-restore-vms.md#restore-a-vm-during-an-azure-datacenter-disaster) lub [tworzenia nowej maszyny Wirtualnej z przywrócić dysków](../articles/backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="alternative-solution-consistent-snapshots"></a>Alternatywne rozwiązanie: migawki spójne z

Jeśli nie można użyć usługi Kopia zapasowa Azure, można wdrożyć własny mechanizm tworzenia kopii zapasowej przy użyciu migawek. Tworzenie migawki spójne z dla wszystkich dysków używanych przez Maszynę wirtualną, a następnie replikację te migawki do innego regionu jest skomplikowane. Z tego powodu Azure traktuje jako lepszym rozwiązaniem niż Tworzenie niestandardowego rozwiązania przy użyciu usługi Kopia zapasowa. 

Użycie magazynu geograficznie nadmiarowego magazynu/geograficznie nadmiarowego dostęp do odczytu dla dysków migawki są automatycznie replikowane do dodatkowego centrum danych. Użycie dysków magazyn lokalnie nadmiarowy, musisz samodzielnie replikowane dane. Aby uzyskać więcej informacji, zobacz [kopii zapasowych dysków niezarządzanych Azure maszyny Wirtualnej z migawkami przyrostowe](../articles/virtual-machines/windows/incremental-snapshots.md).

Migawka jest reprezentację obiektu w określonym punkcie w czasie. Migawki wiąże się z rozliczeń dla przyrostowych rozmiar danych go blokad. Aby uzyskać więcej informacji, zobacz [utworzenia migawki obiektu blob](../articles/storage/blobs/storage-blob-snapshots.md).

### <a name="create-snapshots-while-the-vm-is-running"></a>Tworzenie migawek uruchomionej maszyny Wirtualnej

Mimo że można utworzyć migawkę w dowolnym momencie, jeśli maszyna wirtualna jest uruchomiona, jest nadal danych przesyłane strumieniowo z dyskami. Migawki może zawierać częściowe operacje, które zostały w locie. Ponadto jeśli są zaangażowane kilka dysków, migawek różnych dysków może wystąpić w różnym czasie. Te scenariusze może spowodować ich migawek można nieskoordynowane. Ten brak koordynacji jest szczególnie kłopotliwe w sytuacji dla woluminów rozłożonych może być uszkodzony których pliki, jeśli trwa zmian podczas tworzenia kopii zapasowej.

Aby tego uniknąć, proces tworzenia kopii zapasowej musi implementować następujące czynności:

1.  Zablokuj wszystkie dyski.

2.  Usuń wszystkie oczekujące operacje zapisu.

3.  [Tworzenie migawki obiektu blob](../articles/storage/blobs/storage-blob-snapshots.md) dla wszystkich dysków.

Niektóre aplikacje systemu Windows, takich jak SQL Server, podaj mechanizm tworzenia kopii zapasowych skoordynowanego za pośrednictwem usługi woluminów w tle do tworzenia kopii zapasowych spójnych z aplikacją. W systemie Linux, można użyć narzędzia, takiego jak *fsfreeze* koordynacji dyski. To narzędzie zapewnia spójne z plikami kopii zapasowych, ale nie migawki spójne z aplikacjami. Ten proces jest złożony, więc należy rozważyć użycie [kopia zapasowa Azure](../articles/backup/backup-azure-vms-introduction.md) lub kopii zapasowej rozwiązania innych firm, które już implementuje tę procedurę.

Wyniki poprzedniego procesu w kolekcji skoordynowanego migawek dla wszystkich dysków maszyny Wirtualnej, reprezentujący widok w momencie określonej maszyny wirtualnej. To jest punkt przywracania kopii zapasowej dla maszyny Wirtualnej. Proces można powtarzać w zaplanowanych odstępach czasu, aby utworzyć okresowe kopie zapasowe. Zobacz [skopiuj kopie zapasowe do innego regionu](#copy-the-snapshots-to-another-region) kroki skopiować migawki do innego regionu do odzyskiwania po awarii.

### <a name="create-snapshots-while-the-vm-is-offline"></a>Tworzenie migawek, podczas gdy maszyna wirtualna jest w trybie offline

Inną opcją można utworzyć spójne tworzenie kopii zapasowych jest zamykania maszyny Wirtualnej i wykonać migawki obiektu blob każdego dysku. Tworzenie migawek obiektu blob jest łatwiejsze niż koordynowanie migawki uruchomionej maszyny wirtualnej, ale wymaga to kilka minut przestoju.

1. Zamknij maszynę Wirtualną.

2. Tworzenie migawki obiektu blob każdego wirtualnego dysku twardego, który zajmuje tylko kilka sekund.

    Aby utworzyć migawkę, można użyć [środowiska PowerShell](../articles/storage/common/storage-powershell-guide-full.md), [interfejsu API REST magazynu Azure](https://msdn.microsoft.com/library/azure/ee691971.aspx), [interfejsu wiersza polecenia Azure](/cli/azure/), lub jednej z bibliotek klienta usługi Azure Storage, takich jak [ Biblioteka klienta usługi Storage dla platformy .NET](https://msdn.microsoft.com/library/azure/hh488361.aspx).

3. Uruchom maszynę Wirtualną, która kończy przestoju. Cały proces zakończy się zazwyczaj w ciągu kilku minut.

Ten proces zapewnia zbiór migawki spójne z dla wszystkich dysków, zapewniając punktu przywracania dla maszyny Wirtualnej.

### <a name="copy-the-snapshots-to-another-region"></a>Kopiowanie migawek do innego regionu

Tworzenie migawek wyłącznie nie może być wystarczający do odzyskiwania po awarii. Kopie zapasowe migawek również muszą być replikowane do innego regionu.

Jeśli używasz magazynu geograficznie nadmiarowego lub magazynu geograficznie nadmiarowego dostęp do odczytu dla dysków, a następnie migawki są automatycznie replikowane w regionie pomocniczym. Może istnieć kilka minut opóźnienie przed replikacji. Jeśli podstawowe centrum danych ulegnie awarii, przed migawki Zakończ replikacji, nie można uzyskać dostępu migawki z dodatkowego centrum danych. Prawdopodobieństwo to jest mała.

> [!NOTE] 
> Tylko o dyski magazynu geograficznie nadmiarowego lub dostęp do odczytu z magazynu geograficznie nadmiarowego konta magazynu nie chroni maszyny Wirtualnej po awarii. Należy również tworzenie migawek skoordynowanego albo użyć kopia zapasowa Azure. Jest to wymagane, aby odzyskać Maszynę wirtualną do stanu spójności.

Jeśli używasz magazyn lokalnie nadmiarowy, należy skopiować migawki do innego konta magazynu, natychmiast po utworzeniu migawki. Element docelowy kopiowania może być konto magazyn lokalnie nadmiarowy w innym regionie, co powoduje kopiowania znajdujące się w regionie zdalnego. Można także skopiować migawki na konto magazynu geograficznie nadmiarowego dostęp do odczytu w tym samym regionie. W takim przypadku migawki opóźnieniem są replikowane do zdalnego regionie pomocniczym. Kopia zapasowa jest chronione przed awarii w lokacji głównej po skopiowaniu i ukończeniu replikacji.

Aby skopiować wydajnie Twojej przyrostowe migawki do odzyskiwania po awarii, zapoznaj się z instrukcjami wyświetlanymi w [kopia zapasowa Azure niezarządzane dysków maszyny Wirtualnej z migawkami przyrostowe](../articles/virtual-machines/windows/incremental-snapshots.md).

![Tworzenie kopii zapasowej Azure niezarządzane dysków maszyny Wirtualnej z migawkami przyrostowe][2]

### <a name="recovery-from-snapshots"></a>Odzyskiwanie z migawki

Aby pobrać migawki, skopiuj go do utworzyć nowego obiektu blob. Jeśli kopiujesz migawki z kontem podstawowym, można skopiować migawki za pośrednictwem do podstawowego obiektu blob migawki. Ten proces przekształcenia na dysku w celu tworzenia migawki. Ten proces jest nazywany podwyższania poziomu migawki. Jeśli kopiujesz migawki kopii zapasowej z pomocniczego konta, w przypadku konta magazynu geograficznie nadmiarowego dostęp do odczytu, należy skopiować go do konta podstawowego. Możesz skopiować migawki przez [przy użyciu programu PowerShell](../articles/storage/common/storage-powershell-guide-full.md) lub za pomocą narzędzia AzCopy. Aby uzyskać więcej informacji, zobacz [Transfer danych za pomocą wiersza polecenia azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy).

Dla maszyn wirtualnych z wieloma dyskami należy skopiować wszystkie migawki, które są częścią tego samego punktu przywracania w skoordynowany sposób. Po skopiowaniu migawki zapisywalny obiekty BLOB dysków VHD, można użyć obiektów blob można odtworzyć maszyny Wirtualnej przy użyciu szablonu maszyny wirtualnej.

## <a name="other-options"></a>Inne opcje

### <a name="sql-server"></a>Oprogramowanie SQL Server

Program SQL Server uruchomiony na maszynie wirtualnej ma własną wbudowanych możliwości, Utwórz kopię zapasową bazy danych SQL Server do magazynu obiektów Blob platformy Azure lub pliku udostępniania. W przypadku konta magazynu geograficznie nadmiarowego magazynu lub magazynu geograficznie nadmiarowego dostęp do odczytu, te kopie zapasowe w dodatkowego centrum danych na koncie magazynu są dostępne w przypadku awarii, z ograniczeniami wspomnianej wcześniej. Aby uzyskać więcej informacji, zobacz [kopii zapasowej i przywracania dla programu SQL Server na maszynach wirtualnych Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md). Oprócz kopii zapasowej i przywracania [grup dostępności AlwaysOn programu SQL Server](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md) można zachować replikach pomocniczych baz danych. Tę możliwość znacznie zmniejsza czas odzyskiwania po awarii.

## <a name="other-considerations"></a>Inne zagadnienia

W tym artykule został omówiony sposób wykonywania kopii zapasowej lub wykonać migawki maszyn wirtualnych i ich dyski, która obsługuje odzyskiwanie po awarii i sposobu korzystania z tych kopii zapasowych lub migawki, aby odzyskać dane. Model usługi Azure Resource Manager wiele osób za pomocą szablonów do tworzenia innych infrastruktury i ich maszyny wirtualne na platformie Azure. Szablon służy do tworzenia maszyny Wirtualnej, który ma taką samą konfigurację zawsze. Jeśli używasz niestandardowych obrazów do tworzenia maszyn wirtualnych, należy również upewnić się, że obrazy są chronione przy użyciu konta magazynu geograficznie nadmiarowego dostęp do odczytu do przechowywania ich.

W rezultacie procesu tworzenia kopii zapasowej może być kombinacją dwie czynności:

- Utwórz kopię zapasową danych (dyski).
- Utwórz kopię zapasową konfiguracji (szablony i niestandardowych obrazów).

W zależności od wybranej opcji tworzenia kopii zapasowej może być do obsługi kopii zapasowych danych i konfiguracji lub usługi tworzenia kopii zapasowej może obsłużyć to wszystko dla Ciebie.

## <a name="appendix-understanding-the-impact-of-data-redundancy"></a>Dodatek: Zrozumienie wpływu nadmiarowość danych

Dla konta magazynu na platformie Azure, istnieją trzy typy nadmiarowość danych, które należy wziąć pod uwagę dotyczące odzyskiwania po awarii: lokalnie nadmiarowego, geograficznie nadmiarowego lub geograficznie nadmiarowego z dostępem do odczytu. 

Magazyn lokalnie nadmiarowy przechowuje trzy kopie danych w tym samym centrum danych. Gdy maszyna wirtualna zapisuje dane, wszystkie trzy kopie są aktualizowane przed zwróceniem Powodzenie do wywołującego, dlatego wiadomo, że są one identyczne. Dysk jest zabezpieczony błędy lokalne, ponieważ jest mało prawdopodobne, że wszystkie trzy kopie uwzględnianych w tym samym czasie. W przypadku magazyn lokalnie nadmiarowy nie nie nadmiarowość geograficzna, więc dysk nie jest chroniony poważnej awarii, które mogą wpłynąć na całą jednostkę w centrum danych lub magazynu.

Z magazynu geograficznie nadmiarowego i dostęp do odczytu magazynu geograficznie nadmiarowego trzy kopie danych zostaną zachowane w regionie podstawowym wybrany przez użytkownika. Więcej trzy kopie danych są zachowywane w regionie dodatkowym odpowiedniego ustawionej przez platformę Azure. Na przykład jeśli dane są przechowywane w zachodnie stany USA, dane są replikowane do wschodnie stany USA. Przechowywania kopii jest wykonywane asynchronicznie, a istnieje małe opóźnienia między aktualizacji do lokacji głównych i dodatkowych. Repliki dyski w lokacji dodatkowej są spójne na podstawie na dysku (z opóźnieniem), ale replik wielu aktywnych dysków może nie być zsynchronizowany ze sobą. Aby były spójne replik na wielu dyskach, potrzebne są migawki spójne.

Główna różnica magazynu geograficznie nadmiarowego magazynu geograficznie nadmiarowego dostęp do odczytu jest, że z magazynu geograficznie nadmiarowego dostęp do odczytu, możesz przeczytać pomocniczej kopii w dowolnym momencie. Jeśli występuje problem, który renderuje dane w regionie podstawowym niedostępny, zespół Azure sprawia, że wszelkich starań, aby przywrócić dostęp. Gdy podstawowej nie działa, jeśli masz dostęp do odczytu magazynu geograficznie nadmiarowego włączone, można uzyskać dostęp do danych dodatkowego centrum danych. W związku z tym jeśli planujesz do odczytu z repliki, gdy podstawowy jest niedostępny, następnie dostęp do odczytu z magazynu geograficznie nadmiarowego magazynu należy traktować jako.

Jeśli okaże się być istotne awarii zespół Azure może wyzwolić geograficznie trybu failover i zmień głównej wpisy DNS, aby wskazywał pomocniczego magazynu. W tym momencie w Jeśli masz dostęp do odczytu magazynu geograficznie nadmiarowego włączone albo magazynu geograficznie nadmiarowego, ma dostęp do danych w regionie, które było pomocniczej. Innymi słowy Twoje konto magazynu jest magazyn geograficznie nadmiarowy, jeśli ma problem, są dostępne magazynu pomocniczego tylko wtedy, gdy geograficznie trybu failover.

Aby uzyskać więcej informacji, zobacz [co robić w przypadku wystąpienia awarii usługi Azure Storage](../articles/storage/common/storage-disaster-recovery-guidance.md). 

>[!NOTE] 
>Microsoft kontroluje, czy do pracy awaryjnej. Tryb failover nie są kontrolowane na konto magazynu, więc nie zostanie podjęta decyzja przez poszczególnych klientów. Aby zaimplementować odzyskiwania po awarii dla określonego magazynu kont lub dysków maszyny wirtualnej, należy użyć metod opisanych wcześniej w tym artykule.



[1]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png
