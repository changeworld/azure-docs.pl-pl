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
ms.openlocfilehash: c1b7785e5078fdd3fdc2fc3d407b8890f76cd558
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766242"
---
# <a name="backup-and-disaster-recovery-for-azure-iaas-disks"></a>Kopia zapasowa i odzyskiwanie po awarii dla dysków IaaS platformy Azure

W tym artykule opisano sposób planowania kopii zapasowych i odzyskiwanie po awarii (DR) maszyn wirtualnych IaaS (VM) i dysków na platformie Azure. W tym dokumencie opisano dysków zarządzanych i niezarządzanych.

Po pierwsze zostaną przedstawione możliwości wbudowaną odporność na uszkodzenia na platformie Azure, która pomaga je przed nieprzewidzianymi awariami lokalnego. Następnie omówimy scenariuszy awarii, nie są w pełni objęte wbudowanych możliwości. Ponadto pokazano kilka przykładów scenariuszy obciążenie, gdzie można zastosować różne tworzenia kopii zapasowych i odzyskiwania po awarii, zagadnienia. Następnie omówimy możliwe rozwiązania odzyskiwania po awarii IaaS dysków.

## <a name="introduction"></a>Wprowadzenie

Platforma Azure korzysta różne metody nadmiarowość i odporność na uszkodzenia, aby chronić użytkowników przed awariami sprzętu zlokalizowanego. Błędy lokalne może zawierać problemy z maszyną serwera usługi Azure Storage, która przechowuje dane dla dysku wirtualnego lub awarii dysku SSD lub HDD na tym serwerze. Takie awarii składników sprzętowych izolowane sprzętu może się zdarzyć podczas normalnego działania.

Platforma Azure została zaprojektowana jako odporne na te błędy. Główne awarii może powodować awarie lub inaccessibility wiele serwerów magazynu lub nawet całe centrum danych. Mimo, że maszyny wirtualne i dyski są zwykle chronione przed zlokalizowanymi awariami, dodatkowe kroki są niezbędne do ochrony obciążenia z katastrofalnych awarii całego regionu, takich jak awarii, które mogą mieć wpływ na maszyny Wirtualnej i dyski.

Oprócz możliwości błędów platformy, mogą wystąpić problemy z klienta aplikacji lub danych. Na przykład nowa wersja aplikacji może przypadkowo wprowadzić zmianę w danych, który powoduje, że można przerwać. W takim przypadku można przywrócić aplikację i dane do poprzedniej wersji, który zawiera ostatniego znanego dobrego stanu. Wymagane jest utrzymanie regularnie Twórz kopie zapasowe.

Do odzyskiwania po awarii regionalnej należy wykonać kopię zapasową dysków maszyn wirtualnych IaaS w innym regionie.

Zanim przyjrzymy się opcje odzyskiwania po awarii i kopii zapasowych, przypomnijmy kilku dostępnych metod do obsługi zlokalizowanymi awariami.

### <a name="azure-iaas-resiliency"></a>Odporność platformy Azure IaaS

*Odporność* odwołuje się do tolerancji błędów normalne, występujące w składników sprzętowych. Odporność to zdolność do odzyskiwania po awarii i kontynuowania działania. Nie jest o unikanie błędów, ale odpowiadanie na awarie w taki sposób, aby uniknąć przestoju lub utraty danych. Celem odporności jest przywrócenie aplikacji do stanu pełnej funkcjonalności po wystąpieniu awarii. Usługa Azure virtual machines i dyski są przeznaczone do być odporny na awarie sprzętu typowych. Przyjrzyjmy się jak platformie iaas Azure zapewnia to elastyczność.

Maszyna wirtualna składa się głównie z dwóch części: serwera obliczeń i dysków trwałych. Zarówno wpływa na uszkodzenia maszyny wirtualnej.

Jeśli awaria sprzętu, który jest rzadkie, serwer hosta zasobów obliczeniowych Azure, który przechowuje maszyny Wirtualnej platformy Azure została zaprojektowana do automatycznie przywrócić maszynę Wirtualną na innym serwerze. Jeśli w tym scenariuszu, ponowne uruchomienie Twojego komputera i maszyna wirtualna wróci do sprawności po pewnym czasie. Azure automatycznie wykrywa takie awariami sprzętu i wykonuje odzyskiwanie, aby pomóc upewnić się, że klient maszyny Wirtualnej jest dostępna tak szybko, jak to możliwe.

Dotyczące dysków IaaS trwałość danych jest krytyczny dla platformy z magazynu trwałego. Klienci platformy Azure mają ważnych aplikacji biznesowych działającą w usłudze IaaS i są one zależne od stanu trwałego danych. Ochrona projekty platformy Azure dla tych dysków IaaS przy użyciu trzech nadmiarowych kopii danych, które są przechowywane lokalnie. Te kopie zapewniają wysoką trwałością na awarie lokalne. W przypadku niepowodzenia jednej składniki sprzętowe, które posiada dysku maszyny Wirtualnej nie występuje, ponieważ istnieją dwie kopie dodatkowe do obsługi żądań dysku. Działa prawidłowo, nawet w przypadku awarii dwóch składniki sprzętowe, które obsługują dysku, w tym samym czasie (jest to rzadkie). 

Aby upewnić się, że Ty masz zawsze trzech replik, usługi Azure Storage automatycznie spowoduje utworzenie nowej kopii danych w tle, jeśli jeden z trzech kopiuje staje się niedostępny. W związku z tym nie należy koniecznych do używania macierzy RAID za pomocą usługi Azure disks odporności na uszkodzenia. Proste RAID 0 konfiguracji powinny być wystarczające stosowanie dysków, jeśli to konieczne utworzyć większe woluminy.

Z powodu tej architektury Azure ciągle dostarczał przeznaczonych dla przedsiębiorstw dzięki wiodącej w branży trwałość IaaS dysków zera [współczynnik błędów wycena](https://en.wikipedia.org/wiki/Annualized_failure_rate).

Błędy sprzętu zlokalizowanego w usłudze obliczeniowej hosta lub na platformie magazynu mogą czasami wynik w tymczasową niedostępność maszyny Wirtualnej, który pasuje do żadnego [umowy SLA platformy Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) dostępności maszyny Wirtualnej. System Azure oferuje również wiodące w branży umowy SLA dla pojedynczych wystąpień maszyn wirtualnych, które używają usługi Azure premium SSD.

Aby chronić obciążenia aplikacji z przestoje związane z tymczasową niedostępność dysku lub maszyny Wirtualnej, klienci mogą używać [zestawy dostępności](../articles/virtual-machines/windows/manage-availability.md). Co najmniej dwóch maszyn wirtualnych w zestawie dostępności zapewnić nadmiarowość dla aplikacji. Platforma Azure tworzy następnie tych maszyn wirtualnych i dysków w oddzielnych domenach błędów z różnych składników zasilania, sieci i serwera.

Ze względu na te oddzielnych domenach błędów awariami sprzętu zlokalizowanego zwykle nie wpływają na wiele maszyn wirtualnych w zestawie w tym samym czasie. Posiadanie oddzielnych domenach błędów zapewnia wysoką dostępność dla aplikacji. Ma on uznawany za dobrą praktyką jest użycie zestawów dostępności, gdy wymagana jest wysoka dostępność. Następna sekcja obejmuje aspekt odzyskiwania po awarii.

### <a name="backup-and-disaster-recovery"></a>Kopia zapasowa i odzyskiwanie po awarii

Odzyskiwanie po awarii jest zdolność odzyskania po rzadkich, ale główne, zdarzenia. Zdarzenia te obejmują awarie innych niż przejściowe, szerokiej skali, takie jak zakłócenia, które ma wpływ na cały region. Odzyskiwanie po awarii obejmuje tworzenie kopii zapasowych danych i zarchiwizowanie mających i może obejmować ręczną interwencję, taką jak przywracanie bazy danych z kopii zapasowej.

Platforma Azure wbudowaną ochronę przed zlokalizowanymi awariami nie może w pełni chronić dyski/maszyn wirtualnych, jeśli awarii powoduje awarie na dużą skalę. Te awarie na dużą skalę obejmują katastrofami, takie jak czy centrum danych tych limitów zostanie osiągnięty huragan, trzęsienie ziemi, pożaru, czy wystąpi awaria sprzętu na dużą skalę jednostki. Ponadto mogą wystąpić błędy spowodowane aplikacji lub problemy z danych.

Aby chronić swoje obciążenia IaaS z awarii, należy planowanie w celu zapewnienia nadmiarowości i kopii zapasowych, aby włączyć odzyskiwanie. Do odzyskiwania po awarii należy wykonać kopię zapasową w innej lokalizacji geograficznej od lokacji głównej. Takie podejście pomaga upewnić się, że kopia zapasowa nie dotyczy tego samego zdarzenia, która pierwotnie dotyczy maszyny Wirtualnej lub dysków. Aby uzyskać więcej informacji, zobacz [odzyskiwania po awarii dla aplikacji platformy Azure](/azure/architecture/resiliency/disaster-recovery-azure-applications).

Zagadnienia dotyczące usługi odzyskiwania po awarii mogą obejmować następujące aspekty:

- Wysoka dostępność: Zdolność aplikacji do kontynuowania działania w dobrej kondycji bez znaczących przestojów. Przez *dobrej*, ten stan oznacza, że aplikacja reaguje i użytkownicy mogą połączyć się z aplikacją i wchodzić w interakcje z nią. Niektórych kluczowych aplikacji i baz danych może być konieczne będą zawsze dostępne, nawet wtedy, gdy występują błędy na platformie. W przypadku tych obciążeń może być konieczne planowanie nadmiarowość dla aplikacji, a także dane.

- Trwałość danych: W niektórych przypadkach głównego brany pod uwagę jest zapewnienie, że dane zostaną zachowane w przypadku awarii. W związku z tym może być konieczne kopię zapasową danych w innej lokacji. W przypadku takich obciążeń może potrzebujesz pełnej nadmiarowości aplikacji, ale tylko zwykłych kopii zapasowych dysków.

## <a name="backup-and-dr-scenarios"></a>Scenariusze tworzenia kopii zapasowych i odzyskiwania po awarii

Spójrzmy na kilka przykładów typowych scenariuszy obciążenia aplikacji oraz zagadnienia dotyczące planowania odzyskiwania po awarii.

### <a name="scenario-1-major-database-solutions"></a>Scenariusz 1: Rozwiązania głównej bazy danych

Należy wziąć pod uwagę produkcji serwer bazy danych, takich jak SQL Server lub Oracle, który może obsługiwać wysoką dostępność. Aplikacje produkcyjne krytyczne i użytkowników są zależne od tej bazy danych. Plan odzyskiwania po awarii dla tego systemu, może zajść potrzeba obsługuje następujące wymagania:

- Dane muszą być chronione i możliwe do odzyskania.
- Serwer musi być dostępny do użytku.

Plan odzyskiwania po awarii może wymagać utrzymania repliki bazy danych w różnych regionach do przechowywania kopii zapasowych. W zależności od wymagań dostępności serwera i odzyskiwanie danych rozwiązanie może należeć do zakresu z lokacją usługi active-active lub active-passive repliki do okresowe tworzenie kopii zapasowych danych. Relacyjne bazy danych, takich jak SQL Server i Oracle, zapewniają różne opcje replikacji. W przypadku programu SQL Server, użyj [grup dostępności AlwaysOn programu SQL Server](https://msdn.microsoft.com/library/hh510230.aspx) wysokiej dostępności.

Bazy danych NoSQL, takie jak MongoDB, również obsługiwać [replik](https://docs.mongodb.com/manual/replication/) nadmiarowości. Używane są repliki w celu zapewnienia wysokiej dostępności.

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>Scenariusz 2: Klaster nadmiarowe maszyny wirtualne

Należy wziąć pod uwagę obciążenie obsługiwane przez klaster maszyn wirtualnych, które zapewnia nadmiarowość i równoważenie obciążenia. Przykładem jest klastra oprogramowania Cassandra, wdrożonych w regionie. Tego rodzaju architekturę już zapewnia wysoki poziom nadmiarowości w tym regionie. Jednak aby chronić obciążenia w przypadku awarii na poziomie regionalnym, należy rozważyć rozmieszczenie klastra w dwóch regionach lub dokonywania okresowe kopie zapasowe do innego regionu.

### <a name="scenario-3-iaas-application-workload"></a>Scenariusz 3: Obciążenia aplikacji IaaS

Przyjrzyjmy się IaaS obciążenia aplikacji. Na przykład ta aplikacja może być produkcji typowych obciążeń uruchomionych na Maszynie wirtualnej platformy Azure. Może być serwera sieci web lub serwer plików zawartości i innych zasobów w lokacji. Może być również aplikacji biznesowych stworzonych specjalnie uruchomione na maszynie Wirtualnej, który przechowywane jego dane, zasoby i stan aplikacji na dyskach maszyny Wirtualnej. W tym przypadku należy się upewnić, że wykonujesz kopie zapasowe w regularnych odstępach czasu. Częstotliwość wykonywania kopii zapasowych powinna być oparta na rodzaj obciążenia maszyny Wirtualnej. Na przykład jeśli aplikacja jest uruchamiane codziennie i modyfikuje dane, następnie kopii zapasowej należy podjąć co godzinę.

Innym przykładem jest serwer raportowania, który pobiera dane z innych źródeł, a następnie generuje raporty zagregowane. Utrata tej maszyny Wirtualnej lub dysków może prowadzić do utraty raportów. Jednak może być możliwe ponowne uruchomienie procesu raportowania i ponownie je generować dane wyjściowe. W takim przypadku nie naprawdę masz utraty danych, nawet wtedy, gdy serwer raportowania tych limitów zostanie osiągnięty przy użyciu awarii. W rezultacie może mieć wyższy poziom tolerancji utraty części danych na serwerze raportowania. W takim przypadku mniej częste wykonywanie kopii zapasowych jest to możliwe, aby ograniczyć koszty.

### <a name="scenario-4-iaas-application-data-issues"></a>Scenariusz 4: Problemy z danych aplikacji IaaS

Problemy z danych aplikacji IaaS są inną możliwością. Rozważmy aplikację, która oblicza, przechowuje i służy krytycznych danych komercyjnych, takich jak informacje o cenach. Nową wersję aplikacji ma błąd oprogramowania, który niepoprawnie obliczane, ceny i uszkodzone istniejące dane handlu obsługiwanej przez platformę. W tym miejscu najlepszy plan działania jest Przywróć starszą wersję aplikacji i danych. Aby włączyć tę opcję, należy wykonać okresowe kopie zapasowe systemu.

## <a name="disaster-recovery-solution-azure-backup"></a>Rozwiązanie odzyskiwania po awarii: Azure Backup 

[Usługa Azure Backup](https://azure.microsoft.com/services/backup/) służy do tworzenia kopii zapasowych i odzyskiwania po awarii i współdziałają z [usługi managed disks](../articles/virtual-machines/windows/managed-disks-overview.md) oraz dysków niezarządzanych. Można utworzyć zadania tworzenia kopii zapasowej z kopii zapasowych opartych na czasie, łatwe przywracanie maszyny Wirtualnej i zasad przechowywania kopii zapasowych.

Jeśli używasz [premium SSD](../articles/virtual-machines/windows/disks-types.md), [usługi managed disks](../articles/virtual-machines/windows/managed-disks-overview.md), lub innych typów dysku przy użyciu [magazyn lokalnie nadmiarowy](../articles/storage/common/storage-redundancy-lrs.md) opcji, szczególnie ważne jest zapewnienie DR okresowe kopie zapasowe. Usługa Azure Backup przechowuje dane w magazynie usług odzyskiwania do długoterminowego przechowywania danych. Wybierz [magazyn geograficznie nadmiarowy](../articles/storage/common/storage-redundancy-grs.md) opcja w przypadku odzyskiwania kopii zapasowej usług magazynu. Ta opcja zapewnia, że kopie zapasowe są replikowane do innego regionu platformy Azure, stanowiąca klęskami żywiołowymi regionalne.

W przypadku dysków niezarządzanych można użyć typu magazynu lokalnie nadmiarowego dla dysków IaaS, ale upewnij się, czy kopia zapasowa Azure jest włączone z opcją dla magazynu geograficznie nadmiarowego magazynu usługi recovery services.

> [!NOTE]
> Jeśli używasz [magazyn geograficznie nadmiarowy](../articles/storage/common/storage-redundancy-grs.md) lub [magazyn geograficznie nadmiarowy geograficznie](../articles/storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) opcji dla niezarządzanych dysków, możesz nadal potrzebujesz migawki spójne z kopii zapasowej i odzyskiwanie po awarii. Użyj jednej [kopia zapasowa Azure](https://azure.microsoft.com/services/backup/) lub [migawki spójne z aplikacjami](#alternative-solution-consistent-snapshots).

 Poniższa tabela znajduje się podsumowanie rozwiązań, które są dostępne na potrzeby odzyskiwania po awarii.

| Scenariusz | Automatyczną replikację | Rozwiązanie odzyskiwania po awarii |
| --- | --- | --- |
| Dyski SSD w warstwie Premium | Lokalne ([magazyn lokalnie nadmiarowy](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Dyski zarządzane | Lokalne ([magazyn lokalnie nadmiarowy](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Dyski niezarządzane magazyn lokalnie nadmiarowy | Lokalne ([magazyn lokalnie nadmiarowy](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Dyski niezarządzane magazyn geograficznie nadmiarowy | Między regionami ([magazyn geograficznie nadmiarowy](../articles/storage/common/storage-redundancy-grs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Migawki spójne z aplikacjami](#alternative-solution-consistent-snapshots) |
| Dyski niezarządzane magazyn geograficznie nadmiarowy geograficznie nadmiarowy | Między regionami ([magazyn geograficznie nadmiarowy geograficznie](../articles/storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Migawki spójne z aplikacjami](#alternative-solution-consistent-snapshots) |

Wysoka dostępność najlepiej można spełnić przez użycie dysków zarządzanych w zestawie, wraz z usługi Azure Backup dostępności. Jeśli używasz dysków niezarządzanych, możesz nadal używać usługi Azure Backup do odzyskiwania po awarii. Jeśli nie można użyć usługi Azure Backup, następnie wykonywanie [migawki spójne z aplikacjami](#alternative-solution-consistent-snapshots), zgodnie z opisem w dalszej części tego tematu, jest alternatywnego rozwiązania do tworzenia kopii zapasowych i odzyskiwanie po awarii.

Opcje wysokiej dostępności, kopii zapasowych i odzyskiwania po awarii na poziomie aplikacji lub infrastruktury mogą być reprezentowane w następujący sposób:

| Poziom |   Wysoka dostępność   | Tworzenia kopii zapasowej lub odzyskiwania po awarii |
| --- | --- | --- |
| Aplikacja | SQL Server AlwaysOn | Azure Backup |
| Infrastruktura    | Zestaw dostępności  | Magazyn geograficznie nadmiarowy z migawki spójne z aplikacjami |

### <a name="using-azure-backup"></a>Za pomocą usługi Azure Backup 

[Usługa Azure Backup](../articles/backup/backup-azure-vms-introduction.md) można utworzyć kopię zapasową sieci maszyn wirtualnych z systemem Windows lub Linux do usługi Azure recovery services vault. Tworzenie kopii zapasowej i przywracanie danych krytyczne dla prowadzonej działalności jest skomplikowane fakt, że dane krytyczne dla prowadzonej działalności, należy wykonać kopię zapasową uruchomionej aplikacji, które generują dane. 

Aby rozwiązać ten problem, usługa Azure Backup zapewnia spójnych z aplikacją kopii zapasowych obciążeń firmy Microsoft. Aby upewnić się, że dane są zapisywane prawidłowo magazynu używa usługi woluminów w tle. W przypadku maszyn wirtualnych systemu Linux tylko spójna na poziomie plików kopie zapasowe są to tylko możliwe, ponieważ systemu Linux nie ma funkcji odpowiednikiem usługi woluminów w tle.

![Usługa Azure Backup przepływu][1]

Gdy usługa Azure Backup Inicjuje zadania tworzenia kopii zapasowej w zaplanowanym czasie, wyzwala rozszerzenie kopii zapasowej, zainstalowane na maszynie Wirtualnej, aby utworzyć migawkę punktu w czasie. Migawka zostanie utworzona w połączeniu z usługą woluminów w tle uzyskanie spójnej migawki dysków na maszynie wirtualnej bez konieczności zamknij go. Rozszerzenie kopii zapasowej na maszynie wirtualnej i czyści wszystkie zapisy przed przełączeniem migawkę spójną wszystkich dysków. Po wykonaniu migawki, dane są przesyłane przez usługę Azure Backup do magazynu kopii zapasowych. Aby proces tworzenia kopii zapasowej bardziej wydajne, usługa identyfikuje i przesyła jedynie te bloki danych, które uległy zmianie po utworzeniu ostatniej kopii zapasowej.

Aby przywrócić, można wyświetlić dostępne kopie zapasowe za pośrednictwem usługi Azure Backup, a następnie zainicjuj przywracania. Można tworzyć i przywracanie kopii zapasowych Azure za pośrednictwem [witryny Azure portal](https://portal.azure.com/), [przy użyciu programu PowerShell](../articles/backup/backup-azure-vms-automation.md), lub za pomocą [wiersza polecenia platformy Azure](/cli/azure/).

### <a name="steps-to-enable-a-backup"></a>Procedurę włączania kopii zapasowej

Wykonaj następujące kroki, aby włączyć kopie zapasowe maszyn wirtualnych przy użyciu [witryny Azure portal](https://portal.azure.com/). Brak niektórych odmiany w zależności od tego, do danego scenariusza. Zapoznaj się [kopia zapasowa Azure](../articles/backup/backup-azure-vms-introduction.md) dokumentacji, aby uzyskać szczegółowe informacje. Azure Backup również [obsługuje maszyny wirtualne z dyskami zarządzanymi](https://azure.microsoft.com/blog/azure-managed-disk-backup/).

1.  Utwórz magazyn usługi recovery services dla maszyny Wirtualnej:

    a. W [witryny Azure portal](https://portal.azure.com/), Przeglądaj **wszystkie zasoby** i Znajdź **Magazyny usługi Recovery Services**.

    b. Na **Magazyny usługi Recovery Services** menu, kliknij przycisk **Dodaj** i postępuj zgodnie z instrukcjami, aby utworzyć nowy magazyn, w tym samym regionie co maszyna wirtualna. Na przykład jeśli maszyna wirtualna znajduje się w regionie zachodnie stany USA, należy wybrać zachodnie stany USA magazynu.

1.  Sprawdź, czy replikacja magazynu dla nowo utworzonego magazynu Uzyskiwać dostęp do magazynu w ramach **Magazyny usługi Recovery Services** i przejdź do **ustawienia** > **konfiguracji kopii zapasowej**. Upewnij się, **magazyn geograficznie nadmiarowy** opcja jest zaznaczona domyślnie. Ta opcja zapewnia, że magazyn będą automatycznie replikowane do dodatkowego centrum danych. Na przykład magazynu w regionie zachodnie stany USA są automatycznie replikowane do wschodnie stany USA.

1.  Konfigurowanie zasad tworzenia kopii zapasowej, a następnie wybierz maszynę Wirtualną z tego samego interfejsu użytkownika.

1.  Upewnij się, że zainstalowano agenta kopii zapasowej na maszynie Wirtualnej. Jeśli maszyna wirtualna jest tworzona przy użyciu obrazu z galerii platformy Azure, agenta kopii zapasowej już jest zainstalowane. W przeciwnym razie (jeśli jest używany obraz niestandardowy), postępuj zgodnie z instrukcjami na [Zainstaluj agenta maszyny Wirtualnej na maszynie wirtualnej](../articles/backup/backup-azure-arm-vms-prepare.md#install-the-vm-agent).

1.  Upewnij się, że maszyna wirtualna umożliwia łącznością sieciową na potrzeby usługi kopii zapasowej do funkcji. Postępuj zgodnie z instrukcjami dotyczącymi [połączenia sieciowego](../articles/backup/backup-azure-arm-vms-prepare.md#establish-network-connectivity).

1.  Po wykonaniu poprzednich kroków, tworzenie kopii zapasowej jest uruchamiane w regularnych odstępach czasu określonych w zasadach kopii zapasowych. Jeśli to konieczne, możesz wyzwolić pierwsza kopia zapasowa ręcznie na pulpicie nawigacyjnym magazynu w witrynie Azure portal.

Automatyzacja usługi Azure Backup za pomocą skryptów, można znaleźć [poleceń cmdlet programu PowerShell do utworzenia kopii zapasowej maszyny Wirtualnej](../articles/backup/backup-azure-vms-automation.md).

### <a name="steps-for-recovery"></a>Procedura odzyskiwania

Jeśli musisz naprawić lub ponownie utworzyć Maszynę wirtualną można przywrócić maszynę Wirtualną z żadnym z punktów odzyskiwania kopii zapasowej w magazynie. Istnieje kilka różnych opcji do wykonania odzyskiwania:

-   Możesz utworzyć nową maszynę Wirtualną jako reprezentację w momencie kopii zapasowej maszyny Wirtualnej.

-   Możesz przywracać dyski, a następnie użyć szablonu maszyny wirtualnej można dostosować i ponownie utworzyć przywróconej maszyny Wirtualnej.

Aby uzyskać więcej informacji, zobacz instrukcje dotyczące [przywracanie maszyn wirtualnych za pomocą witryny Azure portal](../articles/backup/backup-azure-arm-restore-vms.md). W tym dokumencie wyjaśniono również określone kroki przywracania kopii zapasowych maszyn wirtualnych w sparowanym centrum danych przy użyciu magazynu geograficznie nadmiarowego magazynu kopii zapasowych w przypadku awarii w podstawowym centrum danych. W takiej sytuacji usługa Azure Backup używa usługi obliczeniowej z regionu pomocniczego, można utworzyć przywróconej maszyny wirtualnej.

Możesz również użyć programu PowerShell na potrzeby [tworzenia nowej maszyny Wirtualnej z przywrócić dyski](../articles/backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="alternative-solution-consistent-snapshots"></a>Alternatywnym rozwiązaniem: Migawki spójne z aplikacjami

Jeśli nie możesz używać usługi Azure Backup można wdrożyć własny mechanizm tworzenia kopii zapasowych, przy użyciu migawek. Tworzenie migawki spójne dla wszystkich dysków, które są używane przez Maszynę wirtualną, a następnie replikowanie tych migawek do innego regionu jest skomplikowane. Z tego powodu Azure uznaje za pomocą usługi Kopia zapasowa jako lepszym rozwiązaniem niż Tworzenie niestandardowego rozwiązania.

Jeśli używasz geograficznie nadmiarowego magazynu/magazyn geograficznie nadmiarowy dla dysków migawek są automatycznie replikowane do dodatkowego centrum danych. Jeśli używasz magazynu lokalnie nadmiarowego w przypadku dysków, wymaganą do replikowania danych, samodzielnie. Aby uzyskać więcej informacji, zobacz [wykonywanie kopii zapasowych dysków niezarządzanych Azure maszyny Wirtualnej przy użyciu migawek przyrostowych](../articles/virtual-machines/windows/incremental-snapshots.md).

Migawka jest reprezentację obiektu w określonym punkcie w czasie. Rozliczanie przyrostowe rozmiar danych go przechowuje migawki jest naliczana opłata. Aby uzyskać więcej informacji, zobacz [utworzyć migawki obiektu blob](../articles/storage/blobs/storage-blob-snapshots.md).

### <a name="create-snapshots-while-the-vm-is-running"></a>Tworzenie migawek, gdy maszyna wirtualna jest uruchomiona.

Chociaż można utworzyć migawkę w dowolnym momencie, jeśli maszyna wirtualna jest uruchomiona, jest nadal danych przesyłane strumieniowo do dysków. Migawki może zawierać częściowe operacje, które zostały w locie. Ponadto kilka dysków są zaangażowani, mogły mieć miejsce w różnym czasie migawki różnych dyskach. Te scenariusze może spowodować migawek jako nieskoordynowane. Ten brak koordynacji jest szczególnie problematyczny dla woluminów rozłożonych, w których pliki mogą zostać uszkodzone, jeśli zmiany zostało wykonane podczas tworzenia kopii zapasowej.

Aby tego uniknąć, proces tworzenia kopii zapasowej należy zaimplementować następujące czynności:

1.  Zablokuj wszystkie dyski.

1.  Opróżnij wszystkie oczekujące operacje zapisu.

1.  [Tworzenie migawki obiektu blob](../articles/storage/blobs/storage-blob-snapshots.md) dla wszystkich dysków.

Niektóre aplikacje Windows, takich jak SQL Server zapewniają mechanizm tworzenia kopii zapasowych koordynowane za pośrednictwem usługi w tle woluminów do tworzenia kopii zapasowych spójnych z aplikacją. W systemie Linux, można użyć narzędzia, takiego jak *fsfreeze* koordynacji dyski. To narzędzie umożliwia wykonywanie kopii zapasowych spójnych z pliku, ale nie migawki spójne z aplikacjami. Ten proces jest złożone, dlatego należy rozważyć użycie [kopia zapasowa Azure](../articles/backup/backup-azure-vms-introduction.md) lub rozwiązania kopii zapasowych innych firm, które już implementuje tej procedury.

Poprzednie wyniki procesu w kolekcji skoordynowanego migawek dla wszystkich dysków maszyny Wirtualnej, reprezentujący określonego punktu w czasie widoku maszyny wirtualnej. Jest to punkt przywracania kopii zapasowej dla maszyny Wirtualnej. Ten proces można powtarzać w zaplanowanych odstępach czasu, aby utworzyć okresowe kopie zapasowe. Zobacz [skopiuj kopie zapasowe do innego regionu](#copy-the-snapshots-to-another-region) kroków do kopiowania migawki do innego regionu do odzyskiwania po awarii.

### <a name="create-snapshots-while-the-vm-is-offline"></a>Tworzenie migawek, podczas gdy maszyna wirtualna jest w trybie offline

Inną opcję, aby utworzyć kopie zapasowe spójne jest zamykania maszyny Wirtualnej i wykonaj migawki obiektu blob każdego dysku. Wykonywanie migawki obiektu blob jest łatwiejsze niż koordynowanie migawek uruchomionej maszyny Wirtualnej, ale wymaga kilku minut przestoju.

1. Zamknij maszynę Wirtualną.

1. Tworzenie migawki obiektu blob każdy wirtualny dysk twardy, którego rejestracja trwa tylko kilka sekund.

    Aby utworzyć migawkę, można użyć [PowerShell](../articles/storage/common/storage-powershell-guide-full.md), [API REST usługi Azure Storage](https://msdn.microsoft.com/library/azure/ee691971.aspx), [wiersza polecenia platformy Azure](/cli/azure/), lub jednej z bibliotek klienta usługi Azure Storage, takie jak [ Biblioteka klienta usługi Storage dla platformy .NET](https://msdn.microsoft.com/library/azure/hh488361.aspx).

1. Uruchamianie maszyny Wirtualnej, którego nazwa kończy się przestojów. Cały proces zakończy się zazwyczaj w ciągu kilku minut.

Ten proces daje kolekcję migawki spójne dla wszystkich dysków, zapewniając punkt przywracania kopii zapasowej dla maszyny Wirtualnej.

### <a name="copy-the-snapshots-to-another-region"></a>Kopiowanie migawki do innego regionu

Tworzenie migawek samodzielnie może nie być wystarczające do odzyskiwania po awarii. Kopie zapasowe migawek muszą być również replikowane do innego regionu.

Jeśli używasz magazyn geograficznie nadmiarowy lub magazyn geograficznie nadmiarowy z dostępem do odczytu dla dysków, a następnie migawki są automatycznie replikowane do regionu pomocniczego. Może istnieć kilka minut opóźnienie przed włączeniem replikacji. Podstawowym centrum danych ulegnie awarii przed migawki zakończenie replikacji, nie można uzyskać dostęp do migawek z dodatkowego centrum danych. Prawdopodobieństwo ten jest mały.

> [!NOTE]
> Tylko o dyski na magazyn geograficznie nadmiarowy lub geograficznie nadmiarowy geograficznie nadmiarowy klęskami żywiołowymi konta magazynu nie chroni maszyny Wirtualnej. Należy również tworzenie migawek skoordynowanego albo użyć usługi Azure Backup. Jest to wymagane, aby odzyskać Maszynę wirtualną do stanu spójności.

Jeśli używasz magazynu lokalnie nadmiarowego, należy skopiować migawki do innego konta magazynu, natychmiast po utworzeniu migawki. Element docelowy kopii może być konto magazyn lokalnie nadmiarowy w innym regionie, wynikiem jest kopia odległym regionie. Ponadto można kopiować migawkę do konta magazynu geograficznie nadmiarowego do odczytu, w tym samym regionie. W takim przypadku migawki opóźnieniem są replikowane do zdalnego regionu pomocniczego. Kopia zapasowa jest chroniony przed awarii w lokacji głównej po skopiowaniu i ukończeniu replikacji.

Aby skopiować z migawek przyrostowych dla odzyskiwania po awarii wydajnie, zapoznaj się z zgodnie z instrukcjami w [tworzenie kopii zapasowej Azure niezarządzanych dysków maszyn wirtualnych przy użyciu migawek przyrostowych](../articles/virtual-machines/windows/incremental-snapshots.md).

![Tworzenie kopii zapasowej Azure niezarządzanych dysków maszyn wirtualnych przy użyciu migawek przyrostowych][2]

### <a name="recovery-from-snapshots"></a>Odzyskiwanie z migawki

Aby pobrać migawki, skopiuj go do utworzenia nowego obiektu blob. Migawki są kopiowane z kontem podstawowym, można skopiować migawki za pośrednictwem, podstawowej obiekt blob migawki. Ten proces przywraca dysku, który ma migawki. Ten proces jest nazywany podwyższanie poziomu migawki. Jeśli kopiujesz migawki kopii zapasowej z pomocniczego konta, w przypadku konta magazynu geograficznie nadmiarowego do odczytu, musi skopiuj go do konta podstawowego. Można skopiować migawki przez [przy użyciu programu PowerShell](../articles/storage/common/storage-powershell-guide-full.md) lub za pomocą narzędzia AzCopy. Aby uzyskać więcej informacji, zobacz [Transfer danych za pomocą narzędzia wiersza polecenia AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy).

W przypadku maszyn wirtualnych z wieloma dyskami należy skopiować wszystkie migawki, które są częścią tego samego punktu przywracania skoordynowany. Po skopiowaniu migawek do zapisywalnego obiekty BLOB dysków VHD, można użyć obiektów blob można odtworzyć maszyny Wirtualnej przy użyciu szablonu maszyny wirtualnej.

## <a name="other-options"></a>Inne opcje

### <a name="sql-server"></a>Oprogramowanie SQL Server

Program SQL Server działający na maszynie wirtualnej ma swój własny wbudowanych możliwości w celu tworzenie kopii zapasowej bazy danych SQL Server do usługi Azure Blob storage lub pliku udziałów. W przypadku magazyn geograficznie nadmiarowy lub magazyn geograficznie nadmiarowy do odczytu do konta magazynu jest dostępne kopie zapasowe w dodatkowym centrum danych konta magazynu awarii, z takim samym ograniczeniom jak wspomniano. Aby uzyskać więcej informacji, zobacz [kopii zapasowej i przywracania dla programu SQL Server na maszynach wirtualnych platformy Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md). Oprócz tworzenia kopii zapasowej i przywracanie [grup dostępności AlwaysOn programu SQL Server](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md) może zachować replik pomocniczych baz danych. Ta możliwość znacznie zmniejsza czas odzyskiwania po awarii.

## <a name="other-considerations"></a>Inne zagadnienia

Ten artykuł ma omówiono sposób wykonywania kopii zapasowej lub wykonać migawki maszyn wirtualnych i ich dyski, która obsługuje odzyskiwanie po awarii i sposobu używania te kopie zapasowe lub migawki, można odzyskać dane. Za pomocą modelu usługi Azure Resource Manager wiele osób Szablony umożliwiają tworzenie maszyn wirtualnych oraz inne elementy infrastruktury na platformie Azure. Aby utworzyć maszynę Wirtualną, która ma taką samą konfigurację, każdym razem, gdy, można użyć szablonu. Jeśli używasz niestandardowych obrazów do tworzenia maszyn wirtualnych, również należy się upewnić, że obrazy są chronione przy użyciu konta magazynu geograficznie nadmiarowego do odczytu do ich przechowywania.

W związku z tym proces tworzenia kopii zapasowej może być kombinacją dwie rzeczy:

- Utwórz kopię zapasową danych (dyski).
- Utwórz kopię zapasową konfiguracji (szablony i niestandardowe obrazy).

W zależności od wybranej opcji tworzenia kopii zapasowej może być do obsługi kopii zapasowych danych i konfiguracji lub usługi kopii zapasowej może obsługiwać wszystko to za Ciebie.

## <a name="appendix-understanding-the-impact-of-data-redundancy"></a>Dodatek: Opis wpływu nadmiarowość danych

Dla kont usługi storage na platformie Azure, istnieją trzy typy nadmiarowości danych, które należy wziąć pod uwagę dotyczące odzyskiwania po awarii: lokalnie nadmiarowy, geograficznie nadmiarowy lub geograficznie nadmiarowy z dostępem do odczytu. 

Magazyn lokalnie nadmiarowy przechowuje trzy kopie danych w jednym centrum danych. Gdy maszyna wirtualna zapisuje dane, wszystkie trzy kopie są aktualizowane, zanim powodzenia jest zwracany do obiektu wywołującego, aby było wiadomo, że są identyczne. Dysk jest chroniony po błędach lokalnych, ponieważ jest mało prawdopodobne, że wszystkie trzy kopie są zainfekowane w tym samym czasie. W przypadku magazynu lokalnie nadmiarowego jest nie nadmiarowość geograficzna, dlatego dysku nie jest chroniona przed poważnej awarii, które mogą mieć wpływ na całą jednostkę w centrum danych lub magazynu.

Magazyn geograficznie nadmiarowy i Magazyn geograficznie nadmiarowy z dostępem do odczytu trzy kopie danych są przechowywane w regionie głównym, który jest wybrany przez użytkownika. Trzy kopie więcej danych zostaną zachowane w odpowiedniego regionu pomocniczego, który jest ustawiony przez platformę Azure. Na przykład jeśli dane są przechowywane w regionie zachodnie stany USA, dane są replikowane do wschodnie stany USA. Przechowywanie kopii jest realizowana asynchronicznie, a występuje małe opóźnienie między aktualizacjami do lokacji głównych i dodatkowych. Repliki dyski w lokacji dodatkowej są zgodne na zasadzie na dysku (z opóźnieniem), ale replik wiele aktywnych dysków może nie być zsynchronizowany ze sobą. Zapewnienie spójnej repliki na wielu dyskach, potrzebne są migawki spójne z aplikacjami.

Główna różnica między magazyn geograficznie nadmiarowy i Magazyn geograficznie nadmiarowy z dostępem do odczytu jest, czy w przypadku magazyn geograficznie nadmiarowy z dostępem do odczytu, możesz mogą odczytywać kopię pomocniczą w dowolnym momencie. Jeśli występuje problem, który powoduje wyświetlenie danych w regionie głównym jest niedostępne, zespół platformy Azure dokłada wszelkich starań, aby przywrócić dostęp. Gdy podstawowej nie działa, jeśli masz magazyn geograficznie nadmiarowy z dostępem do odczytu włączone, można uzyskać dostęp do danych w dodatkowym centrum danych. W związku z tym jeśli planowane jest do odczytu z repliki, gdy podstawowy jest niedostępny, następnie magazyn geograficznie nadmiarowy z dostępem do odczytu należy rozważyć.

Jeśli okazuje się, aby dojść do zaniku połączenia znaczące, zespół platformy Azure może wyzwolić geograficznie tryb failover i zmienić podstawowy wpisy DNS, aby wskazywały do magazynu pomocniczego. W tym momencie w przypadku magazyn geograficznie nadmiarowy lub magazyn geograficznie nadmiarowy z dostępem do odczytu włączone mają dostęp do danych w regionie, które były pomocniczej. Innymi słowy Twoje konto magazynu jest magazyn geograficznie nadmiarowy, jeśli ma problemu, dostępne magazynu pomocniczego tylko wtedy, gdy geograficznie trybu failover.

Aby uzyskać więcej informacji, zobacz [co należy zrobić po wystąpieniu awarii usługi Azure Storage](../articles/storage/common/storage-disaster-recovery-guidance.md).

>[!NOTE] 
>Microsoft kontroluje, czy do pracy awaryjnej. Tryb failover nie jest kontrolowany na konto magazynu, więc nie zostanie podjęta decyzja przez poszczególnych klientów. Aby zaimplementować funkcje odzyskiwania po awarii dla określonego magazynu kont lub dyski maszyny wirtualnej, należy użyć metod opisanych wcześniej w tym artykule.

[1]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png