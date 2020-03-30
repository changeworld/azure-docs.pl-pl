---
title: Typowe pytania dotyczące odzyskiwania po awarii maszyny Wirtualnej platformy Azure za pomocą usługi Azure Site Recovery
description: Ten artykuł zawiera odpowiedzi na typowe pytania dotyczące odzyskiwania po awarii maszyny Wirtualnej platformy Azure podczas korzystania z usługi Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: 3d71301534d56ef8eca68951c8c9f9a1570b3a6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282260"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Typowe pytania: odzyskiwanie po awarii na platformie Azure do platformy Azure

Ten artykuł zawiera odpowiedzi na typowe pytania dotyczące odzyskiwania po awarii maszyn wirtualnych platformy Azure do innego regionu platformy Azure podczas korzystania z [usługi Azure Site Recovery.](site-recovery-overview.md)

## <a name="general"></a>Ogólne

### <a name="how-is-site-recovery-priced"></a>Jak wyceniono odzyskiwanie witryny?

Przejrzyj [ceny usługi Azure Site Recovery dla maszyn wirtualnych](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).

### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Jak działa bezpłatna warstwa usługi Azure Site Recovery?

Każde wystąpienie, które jest chronione za pomocą usługi Azure Site Recovery jest bezpłatne przez pierwsze 31 dni ochrony. Po tym okresie ochrona dla każdego wystąpienia jest według stawek w [cennikach usługi Azure Site Recovery dla maszyn wirtualnych platformy Azure.](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Czy w ciągu pierwszych 31 dni poniesiem inne opłaty za korzystanie z platformy Azure?

Tak. Mimo że usługa Azure Site Recovery jest bezpłatna w ciągu pierwszych 31 dni chronionego wystąpienia, możesz naliczenie opłat za usługę Azure Storage, transakcje magazynu i transfery danych. Odzyskana maszyna wirtualna może również ponieść opłaty obliczeniowe platformy Azure. Uzyskaj szczegółowe informacje na temat cen w [cenniku usługi Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery).

### <a name="what-are-the-best-practices-for-azure-virtual-machines-disaster-recovery"></a>Jakie są najlepsze rozwiązania dotyczące odzyskiwania po awarii maszyn wirtualnych platformy Azure?

1. [Opis architektury platformy Azure do platformy Azure](azure-to-azure-architecture.md)
1. [Przeglądanie obsługiwanych i nie obsługiwanych konfiguracji](azure-to-azure-support-matrix.md)
1. [Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych platformy Azure](azure-to-azure-how-to-enable-replication.md)
1. [Wykonywanie testu przejścia w tryb failover](azure-to-azure-tutorial-dr-drill.md)
1. [Przełajenie awaryjne i powrót po awarii do regionu podstawowego](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-ensured-in-the-target-region"></a>W jaki sposób zapewnia się przepustowość w regionie docelowym?

Zespół odzyskiwania lokacji i zespół zarządzania zdolnością produkcyjną platformy Azure planują wystarczającą przepustowość infrastruktury. Po uruchomieniu pracy awaryjnej zespoły pomagają również zapewnić wystąpienia maszyn wirtualnych, które są chronione przez odzysk lokacji zostanie wdrożony w regionie docelowym.

## <a name="replication"></a>Replikacja

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>Czy można replikować maszyny wirtualne włączone za pomocą szyfrowania dysku platformy Azure?

Tak. Usługa Site Recovery obsługuje odzyskiwanie po awarii maszyn wirtualnych z włączoną obsługą szyfrowania dysków azure. Po włączeniu replikacji platforma Azure kopiuje wszystkie wymagane klucze szyfrowania dysku i wpisy tajne z regionu źródłowego do regionu docelowego w kontekście użytkownika. Jeśli nie masz odpowiednich uprawnień, administrator zabezpieczeń może użyć skryptu do skopiowania kluczy i wpisów tajnych.

- Usługa Site Recovery obsługuje szyfrowanie dysków platformy Azure dla maszyn wirtualnych platformy Azure z systemem Windows.
- Usługa Site Recovery obsługuje szyfrowanie dysków platformy Azure w wersji 0.1, która ma schemat, który wymaga usługi Azure Active Directory (Azure AD). Usługa Site Recovery obsługuje również wersję 1.1, która nie wymaga usługi Azure AD. [Dowiedz się więcej o schemacie rozszerzenia szyfrowania dysków platformy Azure](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema).
  - W przypadku szyfrowania dysków azure w wersji 1.1 należy używać maszyn wirtualnych systemu Windows z dyskami zarządzanymi.
  - [Dowiedz się więcej](azure-to-azure-how-to-enable-replication-ade-vms.md) o włączaniu replikacji zaszyfrowanych maszyn wirtualnych.

### <a name="can-i-replicate-vms-to-another-subscription"></a>Czy można replikować maszyny wirtualne do innej subskrypcji?

Tak, można replikować maszyny wirtualne platformy Azure do innej subskrypcji w ramach tej samej dzierżawy usługi Azure AD.

Skonfiguruj odzyskiwanie [po awarii w różnych subskrypcjach,](https://azure.microsoft.com/blog/cross-subscription-dr) wybierając inną subskrypcję w czasie replikacji.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Czy można replikować maszyny wirtualne platformy Azure przypięte do strefy do innego regionu?

Tak, można [replikować maszyny wirtualne przypięte do strefy](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) do innego regionu.

### <a name="can-i-exclude-disks"></a>Czy mogę wykluczyć dyski?

Tak, dyski można wykluczyć w czasie ochrony przy użyciu programu PowerShell. Aby uzyskać więcej informacji, zobacz [jak wykluczyć dyski z replikacji](azure-to-azure-exclude-disks.md).

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>Czy można dodawać nowe dyski do zreplikowanych maszyn wirtualnych i włączyć dla nich replikację?

Tak, dodawanie nowych dysków do replikowanych maszyn wirtualnych i włączanie replikacji dla nich jest obsługiwane dla maszyn wirtualnych platformy Azure z dyskami zarządzanymi. Po dodaniu nowego dysku do maszyny Wirtualnej platformy Azure, która jest włączona dla replikacji, kondycja replikacji maszyny Wirtualnej wyświetla ostrzeżenie. To ostrzeżenie stwierdza, że jeden lub więcej dysków na maszynie wirtualnej są dostępne do ochrony. Można włączyć replikację dla dodanych dysków.

- Jeśli włączysz ochronę dodanych dysków, ostrzeżenie zniknie po replikacji początkowej.
- Jeśli replikacja dysku nie zostanie włączona, możesz odrzucić to ostrzeżenie.
- Jeśli maszyna wirtualna jest włączona włączona w wyniku pracy awaryjnej, istnieją punkty replikacji. Punkty replikacji pokażą dyski, które są dostępne do odzyskania.

Załóżmy na przykład, że maszyna wirtualna ma jeden dysk i dodać nowy. Może istnieć punkt replikacji, który został utworzony przed dodaniem dysku. Ten punkt replikacji pokaże, że składa się z "1 z 2 dysków".

Usługa Site Recovery nie obsługuje "hot remove" dysku z replikowanej maszyny Wirtualnej. Jeśli usuniesz dysk maszyny Wirtualnej, musisz wyłączyć, a następnie ponownie włączyć replikację maszyny Wirtualnej.

### <a name="how-often-can-i-replicate-to-azure"></a>Jak często można replikować na platformie Azure?

Replikacja jest ciągła podczas replikowania maszyn wirtualnych platformy Azure do innego regionu platformy Azure. Aby uzyskać więcej informacji, zobacz [architekturę replikacji platformy Azure do platformy Azure.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process)

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-functionality-to-migrate-vms"></a>Czy można replikować maszyny wirtualne w regionie? Potrzebuję tej funkcji do migracji maszyn wirtualnych.

Nie można użyć rozwiązania do odzyskiwania dysków platformy Azure do replikacji maszyn wirtualnych w regionie.

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>Czy można replikować wystąpienia maszyn wirtualnych do dowolnego regionu platformy Azure?

Za pomocą usługi Site Recovery można replikować i odzyskiwać maszyny wirtualne między dowolnymi dwoma regionami w ramach tego samego klastra geograficznego. Klastry geograficzne są definiowane z myślą o opóźnieniu danych i suwerenności. Aby uzyskać więcej informacji, zobacz [macierz pomocy technicznej regionu](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)odzyskiwania witryny .

### <a name="does-site-recovery-require-internet-connectivity"></a>Czy usługa Site Recovery wymaga połączenia z Internetem?

Nie, usługa Site Recovery nie wymaga połączenia z Internetem. Ale wymaga dostępu do adresów URL i zakresów adresów IP odzyskiwania witryny, jak wspomniano w [sieci w usłudze Azure VM odzyskiwania po awarii.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)

### <a name="can-i-replicate-an-application-that-has-a-separate-resource-group-for-separate-tiers"></a>Czy można replikować aplikację, która ma oddzielną grupę zasobów dla oddzielnych warstw?

Tak, można replikować aplikację i zachować konfigurację odzyskiwania po awarii w oddzielnej grupie zasobów.

Na przykład jeśli aplikacja ma aplikacji każdej warstwy, bazy danych i sieci web w oddzielnej grupie zasobów, następnie należy wybrać [kreatora replikacji](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication#enable-replication) trzy razy, aby chronić wszystkie warstwy. Usługa Site Recovery będzie replikować te trzy warstwy na trzy różne grupy zasobów.

## <a name="replication-policy"></a>Zasady replikacji

### <a name="what-is-a-replication-policy"></a>Co to jest zasada replikacji?

Zasady replikacji definiuje ustawienia historii przechowywania punktów odzyskiwania. Zasady definiuje również częstotliwość migawek spójnych z aplikacjami. Domyślnie usługa Azure Site Recovery tworzy nową zasadę replikacji z domyślnymi ustawieniami:

- 24 godziny na historię retencji punktów odzyskiwania.
- 60 minut dla częstotliwości migawek spójnych z aplikacją.

[Dowiedz się więcej o ustawieniach replikacji](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#configure-replication-settings).

### <a name="what-is-a-crash-consistent-recovery-point"></a>Co to jest punkt odzyskiwania zgodny z awariami?

Punkt odzyskiwania zgodny z awariami ma dane na dysku, tak jakby pociągnął za pomocą kabla zasilającego z serwera podczas migawki. Punkt odzyskiwania spójne z awarią nie zawiera niczego, co było w pamięci, gdy migawka została podjęta.

Obecnie większość aplikacji można odzyskać również z migawek spójnych z awariami. Punkt odzyskiwania zgodny ze crash-consistent jest zwykle wystarczający dla systemów operacyjnych bez bazy danych i aplikacji, takich jak serwery plików, serwery DHCP i serwery wydruku.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Jaka jest częstotliwość generowania punktów odzyskiwania spójnych z awariami?

Odzyskiwanie witryny tworzy punkt odzyskiwania spójny z awarią co 5 minut.

### <a name="what-is-an-application-consistent-recovery-point"></a>Co to jest punkt odzyskiwania spójny z aplikacją?

Punkty odzyskiwania spójne z aplikacjami są tworzone na podstawie migawek spójnych z aplikacjami. Punkty odzyskiwania spójne z aplikacjami przechwytują te same dane co migawki zgodne ze zmianą awarii, jednocześnie przechwytując dane w pamięci i wszystkie transakcje w procesie.

Ze względu na ich dodatkową zawartość, migawki spójne z aplikacjami są najbardziej zaangażowane i trwają najdłużej. Zaleca się punkty odzyskiwania spójne z aplikacjami dla systemów operacyjnych bazy danych i aplikacji, takich jak SQL Server.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Jaki jest wpływ punktów odzyskiwania spójnych z aplikacjami na wydajność aplikacji?

Punkty odzyskiwania spójne z aplikacjami przechwytują wszystkie dane w pamięci i w procesie. Ponieważ punkty odzyskiwania przechwytują te dane, wymagają one struktury, takiej jak usługa kopiowania woluminów w tle w systemie Windows, aby schedy aplikacji. Jeśli proces przechwytywania jest częste, może mieć wpływ na wydajność, gdy obciążenie jest już zajęty. Nie zaleca się używania niskiej częstotliwości dla punktów odzyskiwania spójnych z aplikacjami dla obciążeń innych niż bazy danych. Nawet w przypadku obciążenia bazy danych wystarczy 1 godzina.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Jaka jest minimalna częstotliwość generowania punktów odzyskiwania spójnych z aplikacją?

Usługa Site Recovery może utworzyć punkt odzyskiwania spójny z aplikacją z minimalną częstotliwością 1 godziny.

### <a name="how-are-recovery-points-generated-and-saved"></a>W jaki sposób generowane i zapisywane są punkty odzyskiwania?

Aby zrozumieć, jak usługa Site Recovery generuje punkty odzyskiwania, zobaczmy przykład zasad replikacji. Ta zasada replikacji ma punkt odzyskiwania z 24-godzinnym okresem przechowywania i migawką częstotliwości spójną z aplikacją wynoszącą 1 godzinę.

Odzyskiwanie witryny tworzy punkt odzyskiwania spójny z awarią co 5 minut. Nie można zmienić tej częstotliwości. W ciągu ostatniej godziny możesz wybrać jeden z 12 punktów zgodnych z awariami i 1 punkt spójny z aplikacją. W miarę upływu czasu odzyskiwanie witryny przycina wszystkie punkty odzyskiwania poza ostatnią godzinę i zapisuje tylko 1 punkt odzyskiwania na godzinę.

Poniższy zrzut ekranu ilustruje przykład. Na zrzucie ekranu:

- W ciągu ostatniej godziny, istnieją punkty odzyskiwania z częstotliwością 5 minut.
- Poza minioną godziną odzyskiwanie witryny utrzymuje tylko 1 punkt odzyskiwania.

   ![Lista wygenerowanych punktów odzyskiwania](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>Jak daleko wstecz można odzyskać?

Najstarszy punkt odzyskiwania, którego można użyć, to 72 godziny.

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>Mam zasady replikacji 24 godzin. Co się stanie, jeśli problem uniemożliwi programowi Site Recovery generowanie punktów odzyskiwania przez ponad 24 godziny? Czy moje poprzednie punkty odzyskiwania zostaną utracone?

Nie, odzyskiwanie witryny zachowa wszystkie poprzednie punkty odzyskiwania. W zależności od okna przechowywania punktów odzyskiwania odzyskiwania lokacji zastępuje najstarszy punkt tylko wtedy, gdy generuje nowe punkty. Z powodu problemu funkcja Site Recovery nie może wygenerować żadnych nowych punktów odzyskiwania. Dopóki nie pojawią się nowe punkty odzyskiwania, wszystkie stare punkty pozostaną po dotarciu do okna przechowywania.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Jak zmienić zasady replikacji po włączeniu replikacji na maszynie wirtualnej?

Przejdź do zasad replikacji infrastruktury odzyskiwania lokacji **usługi Site Recovery ,,** > przejdź do zasad replikacji infrastruktury**odzyskiwania witryny** > magazynu**lokacji**. Wybierz zasady, które chcesz edytować, i zapisz zmiany. Wszelkie zmiany będą miały zastosowanie do wszystkich istniejących replikacji zbyt.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Czy wszystkie punkty odzyskiwania są pełną kopią maszyny Wirtualnej lub różnicą?

Pierwszy punkt odzyskiwania, który jest generowany ma pełną kopię. Wszystkie kolejne punkty odzyskiwania mają zmiany delta.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>Czy zwiększenie okresu przechowywania punktów odzyskiwania zwiększa koszt magazynowania?

Tak, jeśli wydłużysz okres przechowywania z 24 godzin do 72 godzin, odzyskiwanie witryny pozwoli zaoszczędzić punkty odzyskiwania na dodatkowe 48 godzin. Dodatkowy czas wiąże się z opłatami za przechowywanie. Na przykład pojedynczy punkt odzyskiwania może mieć zmiany różnicowe 10 GB z kosztem za GB $0.16 miesięcznie. Dodatkowe opłaty będą $1.60 × 48 miesięcznie.

## <a name="multi-vm-consistency"></a>Spójność wielu maszyn wirtualnych

### <a name="what-is-multi-vm-consistency"></a>Co to jest spójność wielu maszyn wirtualnych?

Spójność wielu maszyn wirtualnych zapewnia, że punkt odzyskiwania jest spójny we wszystkich zreplikowanych maszynach wirtualnych.

Usługa Site Recovery udostępnia opcję **spójności wielu maszyn wirtualnych,** która tworzy grupę replikacji wszystkich komputerów.

Po przełączeniu maszyn wirtualnych w tryb fail over maszyny wirtualne będą miały punkty odzyskiwania zgodne z awariami i spójne z aplikacjami.

Przejdź przez samouczek, aby [włączyć spójność wielu maszyn wirtualnych](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#enable-replication-for-a-vm).

### <a name="can-i-fail-over-a-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>Czy można przejmować po awarii pojedynczą maszynę wirtualną w grupie replikacji spójności wielu maszyn wirtualnych?

Po wybraniu opcji **spójność wielu maszyn wirtualnych,** należy stwierdzić, że aplikacja ma zależność od wszystkich maszyn wirtualnych w grupie. Praca awaryjna pojedynczej maszyny wirtualnej jest niedozwolona.

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>Ile maszyn wirtualnych można replikować jako część grupy replikacji spójności wielu maszyn wirtualnych?

W grupie replikacji można replikować 16 maszyn wirtualnych.

### <a name="when-should-i-enable-multi-vm-consistency"></a>Kiedy należy włączyć spójność wielu maszyn wirtualnych?

Ponieważ spójność wielu maszyn wirtualnych jest intensywnie obciążająca procesor, włączenie jej może mieć wpływ na wydajność obciążenia. Spójność wielu maszyn wirtualnych należy używać tylko wtedy, gdy na maszynach jest uruchomione to samo obciążenie i wymagana jest spójność na wielu komputerach. Na przykład jeśli masz dwa wystąpienia programu SQL Server i dwa serwery sieci web w aplikacji, powinieneś mieć spójność wielu maszyn wirtualnych tylko dla wystąpień programu SQL Server.

### <a name="can-you-add-an-already-replicating-vm-to-a-replication-group"></a>Czy można dodać już replikującą maszynę wirtualną do grupy replikacji?

Maszynę Wirtualną można dodać do nowej grupy replikacji podczas włączania replikacji. Maszynę Wirtualną można również dodać do istniejącej grupy replikacji podczas włączania replikacji. Nie można jednak dodać już replikującej maszyny Wirtualnej do nowej grupy replikacji lub istniejącej grupy replikacji.

## <a name="failover"></a>Tryb failover

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>W jaki sposób zapewnia się pojemność w regionie docelowym maszyn wirtualnych platformy Azure?

Zespół odzyskiwania lokacji i zespół zarządzania zdolnością produkcyjną platformy Azure planują wystarczającą przepustowość infrastruktury. Po uruchomieniu pracy awaryjnej zespoły pomagają również zapewnić wystąpienia maszyn wirtualnych, które są chronione przez odzysk lokacji zostanie wdrożony w regionie docelowym.

### <a name="is-failover-automatic"></a>Czy tryb failover jest automatyczny?

Tryb failover nie jest automatyczny. Pracę awaryjną można uruchomić za pomocą jednego kliknięcia w portalu lub użyć programu [PowerShell](azure-to-azure-powershell.md) do wyzwolenia pracy awaryjnej.

### <a name="can-i-keep-a-public-ip-address-after-a-failover"></a>Czy mogę zachować publiczny adres IP po przejściu awaryjnym?

Nie można zachować publiczny adres IP aplikacji produkcyjnej po pracy awaryjnej.

Po wywołaniu obciążenia w ramach procesu pracy awaryjnej, należy przypisać publiczny zasób IP platformy Azure do obciążenia. Publiczny zasób IP platformy Azure musi być dostępny w regionie docelowym. Publiczny zasób IP platformy Azure można przypisać ręcznie lub zautomatyzować go za pomocą planu odzyskiwania. Dowiedz się, jak [skonfigurować publiczne adresy IP po przemijaniu awaryjnym](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan).

### <a name="can-i-keep-a-private-ip-address-during-a-failover"></a>Czy mogę zachować prywatny adres IP podczas pracy awaryjnej?

Tak, możesz zachować prywatny adres IP. Domyślnie po włączeniu odzyskiwania po awarii dla maszyn wirtualnych platformy Azure usługa Site Recovery tworzy zasoby docelowe na podstawie ustawień zasobów źródłowych. W przypadku maszyn wirtualnych platformy Azure skonfigurowanych ze statycznymi adresami IP usługa Site Recovery próbuje aprowizować ten sam adres IP dla docelowej maszyny Wirtualnej, jeśli nie jest używana.
Dowiedz się więcej o [utrzymywaniu adresów IP podczas pracy awaryjnej](site-recovery-retain-ip-azure-vm-failover.md).

### <a name="after-a-failover-why-is-the-server-assigned-a-new-ip-address"></a>Dlaczego po przejściu awaryjnym serwerowi jest przypisywany nowy adres IP?

Usługa Site Recovery próbuje podać adres IP w czasie pracy awaryjnej. Jeśli inna maszyna wirtualna przyjmuje ten adres, usługa Site Recovery ustawia następny dostępny adres IP jako miejsce docelowe.

Dowiedz się więcej o [konfigurowaniu mapowania sieci i adresowania IP dla sieci wirtualnych](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>Jakie są punkty odzyskiwania **najnowszych (najniższy RPO)?**

Opcja **Ostatni (najniższy RPO)** najpierw przetwarza wszystkie dane, które zostały wysłane do usługi Site Recovery. Po usługa przetwarza dane, tworzy punkt odzyskiwania dla każdej maszyny Wirtualnej przed wdrowek wdrówkę do maszyny Wirtualnej. Ta opcja zapewnia najniższy cel punktu odzyskiwania (RPO). Maszyna wirtualna utworzona po przełączeniu w błąd ma wszystkie dane replikowane do odzyskiwania witryny od momentu wyzwolenia pracy awaryjnej.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>Czy punkty odzyskiwania **najnowszych (najniższy rpo)** mają wpływ na czasochłowania pracy awaryjnej?

Tak. Usługa Site Recovery przetwarza wszystkie oczekujące dane przed przepełnieniem, więc ta opcja ma wyższy cel czasu odzyskiwania (RTO) w porównaniu z innymi opcjami.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>Co oznacza opcja **Najnowsze przetworzone** w punktach odzyskiwania?

Opcja **Najnowsze przetworzone** kończy się niepowodzeniem dla wszystkich maszyn wirtualnych w planie do najnowszego punktu odzyskiwania, który został przetworzony przez odzyskiwanie witryny. Aby wyświetlić najnowszy punkt odzyskiwania dla określonej maszyny Wirtualnej, sprawdź **najnowsze punkty odzyskiwania** w ustawieniach maszyny Wirtualnej. Ta opcja zapewnia niski cel rto, ponieważ nie spędza się czasu na przetwarzaniu nieprzetwochanych danych.

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>Co się stanie, jeśli w moim regionie podstawowym wystąpi nieoczekiwana awaria?

Po awarii można wyzwolić przełączę w stan failas. Odzyskiwanie lokacji nie wymaga łączności z regionu podstawowego, aby wykonać przełączenie w stan failover.

### <a name="what-is-an-rto-of-a-vm-failover"></a>Co to jest rto maszyny wirtualnej w wersji awaryjnej?

Site Recovery ma [umowę SLA RTO 2 godziny.](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) Jednak przez większość czasu odzyskiwanie witryny kończy się niepowodzeniem przez maszyny wirtualne w ciągu kilku minut. Można obliczyć rto, przechodząc do zadań pracy trybu failover, które pokazują czas, który zajęło przywołanie maszyny Wirtualnej. Aby uzyskać informacje O celu odzyskiwania planu odzyskiwania, zapoznaj się z następną sekcją.

## <a name="recovery-plans"></a>Plany odzyskiwania

### <a name="what-is-a-recovery-plan"></a>Co to jest plan naprawczy?

Plan odzyskiwania w układzie odzyskiwania lokacji organizuje odzyskiwanie pracy awaryjnej maszyn wirtualnych. Pomaga to w spójnym, powtarzalnym i zautomatyzowanym odzyskiwaniu. Plan odzyskiwania odpowiada na następujące potrzeby:

- Definiowanie grupy maszyn wirtualnych, które łączą się w tryb fail over razem
- Definiowanie zależności między maszynami wirtualnymi, tak aby aplikacja pojawiała się dokładnie
- Automatyzacja odzyskiwania wraz z niestandardowymi akcjami ręcznymi w celu osiągnięcia zadań innych niż praca awaryjna maszyn wirtualnych

Dowiedz się więcej [o tworzeniu planów odzyskiwania](site-recovery-create-recovery-plans.md).

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>W jaki sposób sekwencjonowanie jest osiągane w planie naprawczym?

W planie odzyskiwania można utworzyć wiele grup w celu osiągnięcia sekwencjonowania. Każda grupa kończy się niepowodzeniem w tym czasie. Maszyny wirtualne, które są częścią tej samej grupy po awarii razem, a następnie innej grupy. Aby dowiedzieć się, jak modelować aplikację przy użyciu planu odzyskiwania, zobacz [Informacje o planach odzyskiwania](recovery-plan-overview.md#model-apps).

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Jak znaleźć RTO planu naprawczego?

Aby sprawdzić rto planu odzyskiwania, wykonaj test pracy awaryjnej dla planu odzyskiwania i przejdź do **zadań odzyskiwania witryny**.
W poniższym przykładzie zobacz zadanie **SAPTestRecoveryPlan**. Zadanie trwało 8 minut i 59 sekund, aby wykonać awaryjną pracę na wszystkich maszynach wirtualnych i wykonać określone akcje.

![Lista zadań odzyskiwania witryny](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>Czy można dodać elementy runbook automatyzacji do planu odzyskiwania?

Tak, można zintegrować elementy runbook usługi Azure Automation z planem odzyskiwania. Dowiedz się więcej o [dodawaniu śmięty usługi Azure Automation](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Reprotection i powrót awaryjny

### <a name="i-failed-over-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>Po awarii z regionu podstawowego do regionu odzyskiwania po awarii. Czy maszyny wirtualne w regionie odzyskiwania po awarii są chronione automatycznie?

Nie. Po [przełączeniu maszyn](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) wirtualnych platformy Azure w stan niechroniony z jednego regionu do drugiego maszyny wirtualne uruchamiają się w regionie odzyskiwania po awarii w stanie niechronionym. Aby przywrócić maszyny wirtualne po awarii do regionu podstawowego, należy ponownie przejść na [nowe maszyny](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect) wirtualne w regionie pomocniczym.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>Czy w momencie ponownego odtworzenia usługa Site Recovery replikuje pełne dane z regionu pomocniczego do regionu podstawowego?

To zależy od sytuacji. Jeśli istnieje maszyna wirtualna regionu źródłowego, synchronizowane są tylko zmiany między dyskiem źródłowym a dyskiem docelowym. Usługa Site Recovery oblicza różnice, porównując dyski, a następnie przesyła dane. Ten proces trwa zwykle kilka godzin. Aby uzyskać więcej informacji o tym, co dzieje się podczas ponownego odwrotowania, zobacz [Reprotect nie powiodło się w przypadku wystąpień maszyn wirtualnych platformy Azure w regionie podstawowym.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection)

### <a name="how-much-time-does-it-take-to-fail-back"></a>Ile czasu zajmuje powrót po awarii?

Po ponownej ochrony po awarii, po awarii trwa o tej samej ilości czasu, co zajmuje do pracy awaryjnej z regionu podstawowego do regionu pomocniczego.

## <a name="capacity"></a><a name="capacity"></a>Pojemność

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>W jaki sposób zapewnia się pojemność w regionie docelowym maszyn wirtualnych platformy Azure?

Zespół odzyskiwania lokacji i zespół zarządzania zdolnością produkcyjną platformy Azure planują wystarczającą przepustowość infrastruktury. Po uruchomieniu pracy awaryjnej zespoły pomagają również zapewnić wystąpienia maszyn wirtualnych, które są chronione przez odzysk lokacji zostanie wdrożony w regionie docelowym.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Czy odzysk witryny działa z wystąpieniami zarezerwowanymi?

Tak, można kupić [zarezerwowane maszyny wirtualne platformy Azure](https://azure.microsoft.com/pricing/reserved-vm-instances/) w regionie odzyskiwania po awarii, a operacje pracy awaryjnej odzyskiwania witryny będą z nich korzystać. Nie jest wymagana żadna dodatkowa konfiguracja.

## <a name="security"></a>Zabezpieczenia

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Czy dane replikacji są wysyłane do usługi Site Recovery?

Nie, usługa Site Recovery nie przechwytuje replikowanych danych i nie ma żadnych informacji o tym, co jest uruchomione na maszynach wirtualnych. Do usługi Site Recovery są wysyłane jedynie metadane wymagane do organizowania replikacji i trybu failover.

Odzysk witryny posiada certyfikat ISO 27001:2013, 27018, HIPAA i DPA. Usługa jest w trakcie oceny SOC2 i FedRAMP JAB.

### <a name="does-site-recovery-encrypt-replication"></a>Czy usługa Site Recovery szyfruje replikację?

Tak, obsługiwane są zarówno szyfrowanie podczas przesyłania, jak i [szyfrowanie w spoczynku na platformie Azure.](https://docs.microsoft.com/azure/storage/storage-service-encryption)

## <a name="next-steps"></a>Następne kroki

- [Przejrzyj wymagania dotyczące pomocy technicznej związanej z platformą Azure na platformie Azure.](azure-to-azure-support-matrix.md)
- [Konfigurowanie replikacji platformy Azure na platformie Azure](azure-to-azure-tutorial-enable-replication.md).
- Jeśli masz pytania po przeczytaniu tego artykułu, opublikuj je na [forum usługi Azure Recovery Services](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).
