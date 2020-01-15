---
title: Często zadawane pytania dotyczące odzyskiwania po awarii maszyny wirtualnej platformy Azure za pomocą Azure Site Recovery
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące odzyskiwania po awarii maszyny wirtualnej platformy Azure przy użyciu Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: 8ed5df15b8ae8e9836c5b8ac8e7d6ad0111f63e1
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941892"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Często zadawane pytania: odzyskiwanie po awarii z platformy Azure do platformy Azure

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące odzyskiwania po awarii maszyn wirtualnych platformy Azure do innego regionu platformy Azure przy użyciu [Site Recovery](site-recovery-overview.md). 


## <a name="general"></a>Ogólne

### <a name="how-is-site-recovery-priced"></a>W jaki sposób Site Recovery cena?
Przejrzyj szczegóły [cennika Azure Site Recovery](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) .
### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Jak działa bezpłatna warstwa usługi Azure Site Recovery?
Każde wystąpienie chronione przez usługę Azure Site Recovery jest bezpłatne przez pierwsze 31 dni tej ochrony. Od 32. dnia opłaty za ochronę wystąpienia są naliczane według powyższych stawek.
### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Czy podczas pierwszych 31 dni będą naliczane inne opłaty usługi Azure?
Tak, nawet jeśli usługa Azure Site Recovery będzie bezpłatna przez pierwsze 31 dni dla chronionego wystąpienia, mogą zostać naliczone opłaty za usługę Azure Storage, transakcje magazynowe i transfer danych. Opłaty za zasoby obliczeniowe platformy Azure mogą zostać również naliczone dla odzyskanej maszyny wirtualnej. Uzyskaj pełne szczegóły [dotyczące cen](https://azure.microsoft.com/pricing/details/site-recovery)

### <a name="where-can-i-find-best-practices-for-azure-vm-disaster-recovery"></a>Gdzie mogę znaleźć najlepsze rozwiązania dotyczące odzyskiwania po awarii maszyny wirtualnej platformy Azure? 
1. [Informacje o architekturze platformy Azure do platformy Azure](azure-to-azure-architecture.md)
2. [Przejrzyj obsługiwane i nieobsługiwane konfiguracje](azure-to-azure-support-matrix.md)
3. [Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych platformy Azure](azure-to-azure-how-to-enable-replication.md)
4. [Wykonywanie testu przejścia w tryb failover](azure-to-azure-tutorial-dr-drill.md)
5. [Przełączenie w tryb failover i powrót po awarii do regionu podstawowego](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-guaranteed-in-the-target-region"></a>Jak jest gwarantowane zapotrzebowanie w regionie docelowym?
Zespół Site Recovery współpracuje z zespołem zarządzania pojemnością platformy Azure w celu zaplanowania wystarczającej pojemności infrastruktury i zapewnienia, że maszyny wirtualne chronione przez Site Recovery dla programu zostaną pomyślnie wdrożone w przypadku zainicjowania trybu failover.

## <a name="replication"></a>Replikacja

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>Czy można replikować maszyny wirtualne z włączoną funkcją szyfrowania dysków Azure?

Tak, Site Recovery obsługuje odzyskiwanie po awarii maszyn wirtualnych z włączonym usługą Azure Disk Encryption (ADE). Po włączeniu replikacji wszystkie wymagane klucze szyfrowania dysków i wpisy tajne są kopiowane z regionu źródłowego do regionu docelowego w kontekście użytkownika. Jeśli nie masz odpowiednich uprawnień, gotowy do użycia skrypt może być przekazywany do administratora zabezpieczeń, aby skopiować klucze i wpisy tajne.

- Site Recovery obsługuje ADE dla maszyn wirtualnych platformy Azure z systemem Windows.
- Site Recovery obsługuje ADE w wersji 0,1 ze schematem przy użyciu usługi Azure Active Directory (AAD) i wersji 1,1 bez usługi AAD. [Dowiedz się więcej](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schemata).
- W wersji 1,1, maszyny wirtualne z systemem Windows muszą używać dysków zarządzanych.
- [Dowiedz się więcej](azure-to-azure-how-to-enable-replication-ade-vms.md) na temat włączania replikacji szyfrowanych maszyn wirtualnych.



### <a name="can-i-replicate-vms-to-another-subscription"></a>Czy można replikować maszyny wirtualne do innej subskrypcji?
Tak. maszyny wirtualne platformy Azure można replikować do innej subskrypcji w ramach tej samej dzierżawy usługi Azure AD.
Konfigurowanie odzyskiwania po awarii [w ramach subskrypcji](https://azure.microsoft.com/blog/cross-subscription-dr) jest proste. W momencie replikacji można wybrać kolejną subskrypcję.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Czy można replikować przypięte do strefy maszyny wirtualne platformy Azure do innego regionu?
Tak, można [replikować przypięte strefy maszyny wirtualne](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) do innego regionu.

### <a name="can-i-exclude-disks"></a>Czy można wykluczać dyski?

Tak, możesz wykluczyć dyski w czasie ochrony przy użyciu programu PowerShell. Aby uzyskać więcej informacji, zobacz [artykuł](azure-to-azure-exclude-disks.md)

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>Czy mogę dodać nowe dyski do replikowanych maszyn wirtualnych i włączyć dla nich replikację?

Tak, jest to obsługiwane w przypadku maszyn wirtualnych platformy Azure z dyskami zarządzanymi. Po dodaniu nowego dysku do maszyny wirtualnej platformy Azure, na której włączono replikację, kondycja replikacji dla maszyny wirtualnej zawiera ostrzeżenie z adnotacją określającą, że co najmniej jeden dysk w maszynie wirtualnej jest dostępny do ochrony. Można włączyć replikację dla dodanych dysków.
- Jeśli włączysz ochronę dla dodanych dysków, ostrzeżenie zniknie po replikacji początkowej.
- Jeśli nie zdecydujesz się na włączenie replikacji dla dysku, możesz wybrać opcję odrzucania ostrzeżenia.
- Po przełączeniu w tryb failover maszyny wirtualnej, do której został dodany dysk i włączeniu dla niej replikacji, punkty replikacji będą zawierać dyski dostępne do odzyskania. Na przykład jeśli maszyna wirtualna ma jeden dysk i dodasz nowy, punkty replikacji, które zostały utworzone przed dodaniem dysku, będą wskazywać, że punkt replikacji składa się z "1 z 2 dysków".

Site Recovery nie obsługuje "gorąca usuwanie" dysku z zreplikowanej maszyny wirtualnej. W przypadku usunięcia dysku maszyny wirtualnej należy wyłączyć i ponownie włączyć replikację maszyny wirtualnej.


### <a name="how-often-can-i-replicate-to-azure"></a>Jak często mogę przeprowadzić replikację do platformy Azure?
Replikacja jest ciągła w przypadku replikowania maszyn wirtualnych platformy Azure do innego regionu platformy Azure. Aby uzyskać więcej informacji, zobacz [Architektura replikacji z platformy Azure do platformy Azure](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process).

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-to-migrate-vms"></a>Czy można replikować maszyny wirtualne w obrębie regionu? Muszę przeprowadzić migrację maszyn wirtualnych.
Nie można użyć rozwiązania Azure-to-Azure DR do replikowania maszyn wirtualnych w danym regionie.

### <a name="can-i-replicate-vms-to-any-azure-region"></a>Czy można replikować maszyny wirtualne do dowolnego regionu platformy Azure?
Za pomocą Site Recovery można replikować i odzyskiwać maszyny wirtualne między dowolnymi dwoma regionami w tym samym klastrze geograficznym. Klastry geograficzne są definiowane z uwzględnieniem opóźnień danych i suwerenności. Aby uzyskać więcej informacji, zobacz [Macierz obsługi Site Recovery regionie](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support).

### <a name="does-site-recovery-require-internet-connectivity"></a>Czy Site Recovery wymaga połączenia z Internetem?

Nie, Site Recovery nie wymaga łączności z Internetem. Jednak wymaga dostępu do Site Recovery adresów URL i zakresów adresów IP, jak wspomniano w [tym artykule](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges).

### <a name="can-i-replicate-the-application-having-separate-resource-group-for-separate-tiers"></a>Czy można replikować aplikację mającą osobną grupę zasobów dla różnych warstw?
Tak, można replikować aplikację i zachować konfigurację odzyskiwania po awarii w osobnej grupie zasobów.
Na przykład jeśli masz aplikację z każdą aplikacją warstwy, bazą danych i siecią Web w oddzielnej grupie zasobów, musisz kliknąć [Kreatora replikacji](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication#enable-replication) trzy razy dziennie, aby chronić wszystkie warstwy. Site Recovery będą replikować te trzy warstwy w trzech różnych grupach zasobów.

## <a name="replication-policy"></a>Zasady replikacji

### <a name="what-is-a-replication-policy"></a>Co to są zasady replikacji?
Definiuje on ustawienia historii przechowywania punktów odzyskiwania oraz częstotliwość migawek spójnych z aplikacjami. Domyślnie Azure Site Recovery tworzy nowe zasady replikacji z ustawieniami domyślnymi:

* 24 godziny dla historii przechowywania punktów odzyskiwania.
* 60 minut dla częstotliwości migawek spójnych z aplikacjami.

[Dowiedz się więcej](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#configure-replication-settings).

### <a name="what-is-a-crash-consistent-recovery-point"></a>Co to jest punkt odzyskiwania spójny na poziomie awarii?
Punkt odzyskiwania spójny na poziomie awarii reprezentuje dane na dysku, tak jak gdyby maszyna wirtualna uległa awarii lub przewód zasilający został pobrany z serwera w czasie trwania migawki. Nie obejmuje to niczego, co było w pamięci podczas tworzenia migawki.

Obecnie większość aplikacji może odzyskać z migawek spójnych na poziomie awarii. Punkt odzyskiwania spójny na poziomie awarii jest zwykle wystarczający dla systemów operacyjnych i aplikacji, takich jak serwery plików, serwery DHCP i serwery wydruku.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Jaka jest częstotliwość generowania punktów odzyskiwania spójnych na poziomie awarii?
Site Recovery tworzy punkt odzyskiwania spójny na poziomie awarii co 5 minut.

### <a name="what-is-an-application-consistent-recovery-point"></a>Co to jest punkt odzyskiwania spójny na poziomie aplikacji?
Punkty odzyskiwania spójne z aplikacją są tworzone na podstawie migawek spójnych z aplikacjami. Punkty odzyskiwania spójne z aplikacją przechwytują te same dane co migawki spójne z awarią, dodając wszystkie dane w pamięci i wszystkie transakcje w procesie.
Ze względu na dodatkową zawartość migawki spójne z aplikacjami są najbardziej wykorzystywane i przejęcie najdłużej. Zalecamy używanie punktów odzyskiwania spójnych na poziomie aplikacji dla systemów operacyjnych i aplikacji baz danych, takich jak SQL Server.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Jaki jest wpływ punktów odzyskiwania spójnych na poziomie aplikacji na wydajność aplikacji?
Biorąc pod uwagę, że punkty odzyskiwania spójne z aplikacją przechwytuje wszystkie dane w pamięci i w procesie, wymagają one środowiska, takiego jak VSS w systemie Windows, aby przełączać aplikację w stan spoczynku. W przypadku bardzo częstego działania mogą mieć wpływ na wydajność, jeśli obciążenie jest już bardzo zajęte. W przypadku punktów odzyskiwania spójnych z aplikacjami nie należy używać niskiej częstotliwości dla obciążeń niezwiązanych z bazą danych, a nawet w przypadku obciążenia bazy danych 1 godzina wystarcza.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Jaka jest minimalna częstotliwość generowania punktów odzyskiwania spójnych z aplikacjami?
Site Recovery może utworzyć punkt odzyskiwania spójny na poziomie aplikacji z minimalną częstotliwością wynoszącą 1 godzinę.

### <a name="how-are-recovery-points-generated-and-saved"></a>Jak są generowane i zapisywane punkty odzyskiwania?
Aby zrozumieć, jak Site Recovery generuje punkty odzyskiwania, należymy przykładem zasad replikacji, które mają okno przechowywania punktu odzyskiwania przez 24 godziny, a migawka częstotliwości spójnej na poziomie aplikacji wynosząca 1 godzinę.

Site Recovery tworzy punkt odzyskiwania spójny na poziomie awarii co 5 minut. Użytkownik nie może zmienić tej częstotliwości. W ciągu ostatniej godziny użytkownik będzie miał 12 punktów spójnych pod względem awarii i 1 punkt spójny z aplikacją do wyboru. W miarę upływu czasu program Site Recovery czyści wszystkie punkty odzyskiwania poza ostatnią 1 godziną i zapisuje tylko 1 punkt odzyskiwania na godzinę.

Poniższy zrzut ekranu ilustruje przykład. Zrzut ekranu:

1. W przypadku okresu krótszego niż Ostatnia godzina istnieją punkty odzyskiwania z częstotliwością wynoszącą 5 minut.
2. W przypadku przekroczenia czasu w ciągu ostatnich 1 godziny Site Recovery zachowuje tylko 1 punkt odzyskiwania.

   ![Lista wygenerowanych punktów odzyskiwania](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)


### <a name="how-far-back-can-i-recover"></a>Jak daleko z powrotem mogę odzyskać?
Najstarszym punktem odzyskiwania, którego można użyć, jest 72 godzin.

### <a name="what-will-happen-if-i-have-a-replication-policy-of-24-hours-and-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>Co się stanie, jeśli będę mieć zasady replikacji przez 24 godziny, a problem uniemożliwia Site Recovery wygenerowanie punktów odzyskiwania przez ponad 24 godziny? Czy moje poprzednie punkty odzyskiwania zostaną utracone?
Nie, Site Recovery będzie przechowywać wszystkie poprzednie punkty odzyskiwania. W zależności od okna przechowywania punktów odzyskiwania 24 godziny w tym przypadku Site Recovery zastępuje najstarszego punktu tylko wtedy, gdy istnieje generacja nowych punktów. W takim przypadku, ponieważ nie będzie żadnych nowych punktów odzyskiwania wygenerowanych z powodu niektórych problemów, wszystkie stare punkty pozostaną nienaruszone po osiągnięciu okna przechowywania.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Jak zmienić zasady replikacji po włączeniu replikacji na maszynie wirtualnej?
Przejdź do **magazynu Site Recovery** > **Site Recovery infrastruktury** > **zasady replikacji**. Wybierz zasady, które chcesz edytować, i Zapisz zmiany. Każda zmiana zostanie również zastosowana do wszystkich istniejących replikacji.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Czy wszystkie punkty odzyskiwania są kompletną kopią maszyny wirtualnej czy różnicą?
Pierwszy wygenerowany punkt odzyskiwania ma kompletną kopię. Wszystkie kolejne punkty odzyskiwania mają zmiany różnicowe.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>Czy zwiększenie okresu przechowywania punktów odzyskiwania zwiększa koszt magazynu?
Tak. Jeśli okres przechowywania zostanie zwiększony z 24 godzin do 72 godzin, Site Recovery będzie zapisywać punkty odzyskiwania dla dodatkowych 48 godzin. Dodatkowy czas będzie powodować naliczanie opłat za magazyn. Na przykład jeśli pojedynczy punkt odzyskiwania ma zmiany różnicowe o wartości 10 GB, a koszt za GB wynosi $0,16 miesięcznie, dodatkowe opłaty będą wynosić $1,6 * 48 miesięcznie.

## <a name="multi-vm-consistency"></a>Spójność między MASZYNami wirtualnymi

### <a name="what-is-multi-vm-consistency"></a>Co to jest spójność między MASZYNami wirtualnymi?
Oznacza to, że punkt odzyskiwania jest spójny dla wszystkich zreplikowanych maszyn wirtualnych.
Site Recovery zapewnia opcję "spójność z obsługą wiele maszyn wirtualnych", co oznacza, że po jej wybraniu program tworzy grupę replikacji w celu replikowania wszystkich maszyn, które są częścią grupy.
Po przełączeniu w tryb failover wszystkie maszyny wirtualne będą miały udostępnione punkty odzyskiwania spójne z awarią i aplikacjami.
Zapoznaj się z samouczkiem, aby [włączyć spójność między maszynami](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#enable-replication-for-a-vm)wirtualnymi.

### <a name="can-i-failover-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>Czy można przełączyć do trybu failover pojedynczą maszynę wirtualną w ramach grupy replikacji spójności z obsługą kilku maszyn wirtualnych?
Wybierając opcję "spójność z obsługą wiele maszyn wirtualnych", oznacza to, że aplikacja ma zależność od wszystkich maszyn wirtualnych w grupie. W związku z tym pojedynczy tryb failover maszyny wirtualnej jest niedozwolony.

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>Ile maszyn wirtualnych można replikować jako część grupy replikacji spójności z wieloma MASZYNami wirtualnymi?
Można replikować 16 maszyn wirtualnych razem w grupie replikacji.

### <a name="when-should-i-enable-multi-vm-consistency-"></a>Kiedy należy włączyć spójność wiele maszyn wirtualnych?
Ponieważ jest to intensywnie obciążające procesor CPU, włączenie spójności wielu maszyn wirtualnych może wpłynąć na wydajność obciążeń. Powinna być używana tylko wtedy, gdy na maszynach działa to samo obciążenie i wymagana jest spójność na wielu komputerach. Na przykład jeśli masz dwa SQL Server wystąpienia i dwa serwery sieci Web w aplikacji, należy mieć tylko spójność dla SQL Server wystąpień.


## <a name="failover"></a>Tryb failover

### <a name="how-is-capacity-assured-in-target-region-for-azure-vms"></a>Jak jest zapewniana pojemność w regionie docelowym maszyn wirtualnych platformy Azure?
Zespół Site Recovery współpracuje z zespołem zarządzania pojemnością platformy Azure w celu zaplanowania wystarczającej pojemności infrastruktury, aby zapewnić, że maszyny wirtualne z obsługą odzyskiwania po awarii zostaną pomyślnie wdrożone w regionie docelowym po zainicjowaniu trybu failover.

### <a name="is-failover-automatic"></a>Czy tryb failover jest automatyczny?

Tryb failover nie jest automatyczny. Uruchamiasz tryb failover za pomocą jednego kliknięcia w portalu lub możesz użyć [programu PowerShell](azure-to-azure-powershell.md) do wyzwolenia trybu failover.

### <a name="can-i-retain-a-public-ip-address-after-failover"></a>Czy można zachować publiczny adres IP po przejściu do trybu failover?

Nie można zachować publicznego adresu IP aplikacji produkcyjnej po przejściu do trybu failover.
- Obciążenia, które są tworzone w ramach procesu przełączania do trybu failover, muszą mieć przypisany zasób publicznego adresu IP platformy Azure, który jest dostępny w regionie docelowym.
- Można to zrobić ręcznie lub zautomatyzować przy użyciu planu odzyskiwania.
- Dowiedz się, jak [skonfigurować publiczne adresy IP po](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan)przejściu do trybu failover.  

### <a name="can-i-retain-a-private-ip-address-during-failover"></a>Czy można zachować prywatny adres IP podczas pracy w trybie failover?
Tak, możesz zachować prywatny adres IP. Domyślnie po włączeniu odzyskiwania po awarii dla maszyn wirtualnych platformy Azure Site Recovery tworzy zasoby docelowe na podstawie ustawień zasobów źródłowych. — W przypadku maszyn wirtualnych platformy Azure skonfigurowanych ze statycznymi adresami IP Site Recovery próbuje zainicjować ten sam adres IP dla docelowej maszyny wirtualnej, jeśli nie jest używany.
Dowiedz się więcej na temat [zachowywania adresów IP podczas pracy w trybie failover](site-recovery-retain-ip-azure-vm-failover.md).

### <a name="after-failover-why-is-the-server-assigned-a-new-ip-address"></a>Dlaczego po przejściu do trybu failover serwer przypisał nowy adres IP?

Site Recovery próbuje podać adres IP w momencie przejścia w tryb failover. Jeśli dla tego adresu jest przydana inna maszyna wirtualna, Site Recovery ustawia następny dostępny adres IP jako element docelowy.
Dowiedz się więcej o [konfigurowaniu mapowania sieci i adresowania IP dla sieci wirtualnych](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>Co to są najnowsze punkty odzyskiwania **(najniższy cel RPO)** ?
**Najnowsza opcja (najniższy cel punktu odzyskiwania)** najpierw przetwarza wszystkie dane, które zostały wysłane do usługi Site Recovery, aby utworzyć punkt odzyskiwania dla każdej maszyny wirtualnej przed przejściem do trybu failover. Ta opcja zapewnia najniższy cel punktu odzyskiwania (RPO), ponieważ maszyna wirtualna utworzona po przejściu w tryb failover ma wszystkie dane zreplikowane do Site Recovery podczas wyzwolenia trybu failover.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>Czy **najnowsze punkty odzyskiwania (najniższych RPO)** mają wpływ na RTO pracy awaryjnej?
Tak. Site Recovery przetwarza wszystkie oczekujące dane przed przełączeniem w tryb failover, dlatego ta opcja ma wyższy cel czasu odzyskiwania (RTO) w porównaniu z innymi opcjami.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>Co oznacza **Najnowsza przetworzona** opcja w punktach odzyskiwania?
**Ostatnia przetworzona** opcja przejdzie w tryb failover wszystkich maszyn wirtualnych w planie do najnowszego punktu odzyskiwania, który Site Recovery przetworzony. Aby wyświetlić najnowszy punkt odzyskiwania dla określonej maszyny wirtualnej, sprawdź **najnowsze punkty odzyskiwania** w ustawieniach maszyny wirtualnej. Ta opcja zapewnia niską RTO, ponieważ nie ma czasu poświęcanego na przetwarzanie nieprzetworzonych danych.

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>Co się stanie, jeśli w regionie podstawowym wystąpił nieoczekiwany przestój?
Po awarii można wyzwolić przejście w tryb failover. Site Recovery nie wymaga łączności z regionu podstawowego w celu przeprowadzenia przejścia w tryb failover.

### <a name="what-is-a-rto-of-a-vm-failover-"></a>Co to jest RTO maszyny wirtualnej w trybie failover?
Site Recovery ma umowę [SLA RTO o wartości 2 godz](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). Jednak w ciągu kilku minut Site Recovery w trybie failover maszyn wirtualnych. Możesz obliczyć RTO, przechodząc do zadań trybu failover, które przedstawiają czas potrzebny na przełączenie maszyny wirtualnej. W przypadku planu odzyskiwania RTO zapoznaj się z sekcją poniżej.

## <a name="recovery-plans"></a>Plany odzyskiwania

### <a name="what-is-a-recovery-plan"></a>Co to jest plan odzyskiwania?
Plan odzyskiwania w Site Recovery organizuje odzyskiwanie maszyn wirtualnych w trybie failover. Ułatwia to odzyskiwanie spójnie, powtarzalne i zautomatyzowane. Plan odzyskiwania dotyczy następujących potrzeb użytkownika:

- Definiowanie grupy maszyn wirtualnych, które są łączone w tryb failover
- Definiowanie zależności między maszynami wirtualnymi w celu dokładnego przetworzenia aplikacji
- Automatyzowanie odzyskiwania wraz z niestandardowymi akcjami ręcznymi w celu uzyskania zadań innych niż tryb failover maszyn wirtualnych

[Dowiedz się więcej](site-recovery-create-recovery-plans.md) o planach odzyskiwania.

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>Jak odbywa się sekwencjonowanie w planie odzyskiwania?

W planie odzyskiwania można utworzyć wiele grup, aby osiągnąć sekwencjonowanie. Każda grupa działa w trybie failover jednocześnie. Maszyny wirtualne, które są częścią tej samej grupy, są przełączane do trybu failover razem z inną grupą. Aby dowiedzieć się, jak modelować aplikację przy użyciu planu odzyskiwania, zobacz [Informacje o planach odzyskiwania](recovery-plan-overview.md#model-apps).

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Jak mogę znaleźć RTO planu odzyskiwania?
Aby sprawdzić RTO planu odzyskiwania, wykonaj test pracy w trybie failover dla planu odzyskiwania i przejdź do **Site Recovery zadań**.
W poniższym przykładzie zadanie o nazwie SAPTestRecoveryPlan zajęło 8 minut i 59 sekund na przełączenie wszystkich maszyn wirtualnych do trybu failover i wykonanie określonych akcji.

![Lista zadań Site Recovery](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>Czy mogę dodać elementy Runbook usługi Automation do planu odzyskiwania?
Tak, możesz zintegrować Azure Automation elementów Runbook z planem odzyskiwania. [Dowiedz się więcej](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Ochrona i powrót po awarii

### <a name="after-a-failover-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>Czy po przejściu w tryb failover z regionu podstawowego do regionu odzyskiwania po awarii czy maszyny wirtualne w regionie DR są chronione automatycznie?
Nie. Po przełączeniu maszyn wirtualnych platformy Azure w [tryb failover](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) z jednego regionu do innego, maszyny wirtualne są uruchamiane w regionie odzyskiwania w stanie niechronionym. Aby zakończyć przywracanie maszyn wirtualnych do regionu podstawowego, należy ponownie [włączyć ochronę](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect) maszyn wirtualnych w regionie pomocniczym.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>Czy w czasie ponownej ochrony program Site Recovery replikowania pełnych danych z regionu pomocniczego do regionu podstawowego?
Zależy to od sytuacji. Na przykład jeśli istnieje maszyna wirtualna regionu źródłowego, synchronizowane są tylko zmiany między dyskiem źródłowym a dyskiem docelowym. Site Recovery oblicza różnic, porównując dyski, a następnie przesyła dane. Ten proces zazwyczaj trwa kilka godzin. Aby uzyskać więcej informacji na temat tego, co się dzieje podczas ponownej ochrony, zobacz Ponowne [Włączanie ochrony maszyn wirtualnych platformy Azure w trybie failover w regionie podstawowym]( https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection).

### <a name="how-much-time-does-it-take-to-fail-back"></a>Ile czasu trwa powrót po awarii?
Po włączeniu ochrony czas powrotu po awarii jest zwykle podobny do czasu, który był wymagany do przejścia w tryb failover z regionu podstawowego do regionu pomocniczego.

## <a name="capacity"></a>Pojemności

### <a name="how-is-capacity-assured-in-target-region-for-azure-vms"></a>Jak jest zapewniana pojemność w regionie docelowym maszyn wirtualnych platformy Azure?
Zespół Site Recovery współpracuje z zespołem zarządzania pojemnością platformy Azure w celu zaplanowania wystarczającej pojemności infrastruktury, aby zapewnić, że maszyny wirtualne z włączonym odzyskiwaniem po awarii zostaną pomyślnie wdrożone w regionie docelowym po zainicjowaniu trybu failover.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Czy Site Recovery współpracuje z wystąpieniami zarezerwowanymi?
Tak. w regionie odzyskiwania po awarii można zakupić [wystąpienia rezerwowe](https://azure.microsoft.com/pricing/reserved-vm-instances/) , a Site Recovery będą z nich korzystać operacje trybu failover. </br> Dodatkowa konfiguracja nie jest potrzebna.


## <a name="security"></a>Zabezpieczenia

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Czy dane replikacji są wysyłane do usługi Site Recovery?
Nie, Site Recovery nie przechwytuje replikowanych danych i nie zawiera żadnych informacji o działaniach na maszynach wirtualnych. Do usługi Site Recovery są wysyłane jedynie metadane wymagane do organizowania replikacji i trybu failover.  
Site Recovery to ISO 27001:2013, 27018, HIPAA, DPA certyfikowany i jest w trakcie oceny SOC2 i FedRAMP JAB.

### <a name="does-site-recovery-encrypt-replication"></a>Czy usługa Site Recovery szyfruje replikację?
Tak, obsługiwane są zarówno szyfrowanie podczas przesyłania, jak i [szyfrowanie na platformie Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) .

## <a name="next-steps"></a>Następne kroki
* [Przejrzyj](azure-to-azure-support-matrix.md) wymagania dotyczące pomocy technicznej.
* [Konfiguracja](azure-to-azure-tutorial-enable-replication.md) Replikacja z platformy Azure do platformy Azure.
- Jeśli masz pytania po przeczytaniu tego artykułu, Opublikuj je na [Forum usługi Azure Recovery Services](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).
