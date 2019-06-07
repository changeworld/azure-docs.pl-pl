---
title: Często zadawane pytania dotyczące odzyskiwania po awarii Azure – Azure za pomocą usługi Azure Site Recovery
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące odzyskiwania po awarii maszyn wirtualnych platformy Azure do innego regionu platformy Azure przy użyciu usługi Azure Site Recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.date: 04/29/2019
ms.topic: conceptual
ms.author: asgan
ms.openlocfilehash: 271e3c31c3e08d170add84ca4995f4876d4d3a33
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2019
ms.locfileid: "66753775"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Często zadawane pytania: odzyskiwanie po awarii z platformy Azure do platformy Azure

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące odzyskiwania po awarii maszyn wirtualnych platformy Azure do innego regionu platformy Azure przy użyciu [Site Recovery](site-recovery-overview.md). 


## <a name="general"></a>Ogólne

### <a name="how-is-site-recovery-priced"></a>Jak jest rozliczana Usługa Site Recovery?
Przegląd [cennika usługi Azure Site Recovery](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) szczegółowe informacje.
### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Jak działa bezpłatna warstwa usługi Azure Site Recovery?
Każde wystąpienie chronione przez usługę Azure Site Recovery jest bezpłatne przez pierwsze 31 dni tej ochrony. Od 32. dnia opłaty za ochronę wystąpienia są naliczane według powyższych stawek.
### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Czy podczas pierwszych 31 dni będą naliczane inne opłaty usługi Azure?
Tak, nawet jeśli usługa Azure Site Recovery będzie bezpłatna przez pierwsze 31 dni dla chronionego wystąpienia, mogą zostać naliczone opłaty za usługę Azure Storage, transakcje magazynowe i transfer danych. Opłaty za zasoby obliczeniowe platformy Azure mogą zostać również naliczone dla odzyskanej maszyny wirtualnej. Uzyskaj szczegółowe informacje na temat cen [tutaj](https://azure.microsoft.com/pricing/details/site-recovery)

### <a name="where-can-i-find-best-practices-for-azure-vm-disaster-recovery"></a>Gdzie znaleźć najlepsze rozwiązania do odzyskiwania po awarii maszyny Wirtualnej platformy Azure? 
1. [Informacje na temat architektury Azure – Azure](azure-to-azure-architecture.md)
2. [Przejrzyj konfiguracje obsługiwane i nieobsługiwane](azure-to-azure-support-matrix.md)
3. [Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych platformy Azure](azure-to-azure-how-to-enable-replication.md)
4. [Wykonywanie testu przejścia w tryb failover](azure-to-azure-tutorial-dr-drill.md)
5. [Tryb failover i powrót po awarii do regionu podstawowego](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-guaranteed-in-the-target-region"></a>Jak jest wydajność gwarantowana w regionie docelowym
Zespół usługi Site Recovery współpracuje z zespołem zarządzania pojemność na platformie Azure, aby zaplanować pojemność infrastruktury jest wystarczająca i ułatwia, upewnij się, że maszyny wirtualne chronione przez usługę Site Recovery dla pomyślnie będzie region docelowy wdrożone po zainicjowaniu trybu failover.

## <a name="replication"></a>Replikacja

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>Czy można replikować maszyny wirtualne, włączana za pomocą szyfrowania dysków Azure?
Tak, można replikować je. Zapoznaj się z artykułem [maszyny wirtualne w innym regionie platformy Azure obsługujące replikacja usługi Azure disk encryption](azure-to-azure-how-to-enable-replication-ade-vms.md). Obecnie usługi Azure Site Recovery obsługuje tylko maszyny wirtualne platformy Azure są z systemem operacyjnym Windows i włączonego szyfrowania za pomocą aplikacji usługi Azure Active Directory (Azure AD).

### <a name="can-i-replicate-vms-to-another-subscription"></a>Maszyny wirtualne można replikować do innej subskrypcji?
Tak, można replikować maszyny wirtualne platformy Azure do innej subskrypcji w ramach tej samej dzierżawie usługi Azure AD.
Konfigurowanie odzyskiwania po awarii [w subskrypcjach](https://azure.microsoft.com/blog/cross-subscription-dr) jest proste. Możesz wybrać inną subskrypcję w czasie replikacji.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Czy można replikować przypięte w strefie maszyn wirtualnych platformy Azure, do innego regionu?
Tak, można [replikowanie maszyn wirtualnych z przypięte w strefie](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) do innego regionu.

### <a name="can-i-exclude-disks"></a>Czy można wykluczyć dysków?

Tak, można wykluczyć dyski w czasie ochrony przy użyciu programu PowerShell. Aby uzyskać więcej informacji, zobacz [artykułu](azure-to-azure-exclude-disks.md)

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>Czy mogę dodać nowych dysków do replikowane maszyny wirtualne i włączyć ich replikację?

Tak, jest to obsługiwane dla maszyn wirtualnych platformy Azure z dyskami zarządzanymi. Po dodaniu nowego dysku do maszyny Wirtualnej platformy Azure, w którym włączono obsługę replikacji, kondycja replikacji maszyny wirtualnej zawiera ostrzeżenia, za pomocą Uwaga określenie, że co najmniej jeden dysk na maszynie Wirtualnej są dostępne do ochrony. Można włączyć replikację dla dodanych dysków.
- Jeśli włączysz ochronę dodane dyski, ostrzeżenie znikną po replikacji początkowej.
- Jeśli wybierzesz nie włączyć replikację dla dysku, możesz wybrać, aby zignorować to ostrzeżenie.
- Po przełączeniu w tryb failover maszyny Wirtualnej, do którego dodawany jest dysk i włączyć dla niego replikację replikacji punkty zostaną wyświetlone dyski, które są dostępne do odzyskania. Na przykład jeśli maszyna wirtualna ma jeden dysk, a następnie dodaj nową, punkty replikacji, które zostały utworzone przed dodaniem dysku pokaże, że punktu replikacji składa się z "1 2 dyski".

Usługa Site Recovery nie obsługuje "hot Usuń" dysku ze zreplikowanej maszyny Wirtualnej. Jeśli usuniesz dysku maszyny Wirtualnej, musisz wyłączyć i ponownie włączyć replikację dla maszyny Wirtualnej.


### <a name="how-often-can-i-replicate-to-azure"></a>Jak często można replikować na platformę Azure?
Replikacja jest ciągłego, Jeśli replikujesz maszyny wirtualne platformy Azure do innego regionu platformy Azure. Aby uzyskać więcej informacji, zobacz [architektura replikacji Azure – Azure](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process).

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-to-migrate-vms"></a>Czy można replikować maszyny wirtualne w danym regionie? Czy mogę niezbędne do przeprowadzenia migracji maszyn wirtualnych.
Rozwiązanie do odzyskiwania po awarii Azure – Azure nie można użyć do replikowania maszyn wirtualnych w obrębie regionu.

### <a name="can-i-replicate-vms-to-any-azure-region"></a>Maszyny wirtualne można replikować do dowolnego regionu systemu Azure?
Usługa Site Recovery może replikowanie i odzyskiwanie maszyn wirtualnych między wszystkie dwóch regionach w obrębie tego samego klastra geograficznego. Klastry geograficzne są definiowane przy opóźnieniu przesyłania danych i niezależność danych należy pamiętać. Aby uzyskać więcej informacji, zobacz Site Recovery [macierz obsługi region](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support).

### <a name="does-site-recovery-require-internet-connectivity"></a>Usługa Site Recovery wymaga łączności z Internetem?

Nie, Usługa Site Recovery nie wymaga łączności z Internetem. Jednak wymaga dostępu do adresów URL Site Recovery i zakresami IP, jak wspomniano w [w tym artykule](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges).

### <a name="can-i-replicate-the-application-having-separate-resource-group-for-separate-tiers"></a>Czy można replikować aplikacji mających oddzielnej grupy zasobów dla oddzielnych warstw?
Tak, możesz zreplikować aplikacji i zbyt zachować konfiguracji odzyskiwania po awarii w oddzielnej grupie zasobów.
Na przykład, jeśli będziesz mieć aplikację z każdej warstwy aplikacji, bazy danych i sieci web w oddzielnej grupie zasobów, a następnie być konieczne kliknięcie pozycji [kreatora replikacji](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication#enable-replication) trzy razy, aby chronić wszystkie warstwy. Usługa Site Recovery będzie replikować tych trzech warstw w trzech różnych grupach zasobów.

## <a name="replication-policy"></a>Zasady replikacji

### <a name="what-is-a-replication-policy"></a>Co to są zasady replikacji?
Definiuje ustawienia dla historii przechowywania punktów odzyskiwania i częstotliwość migawek spójności aplikacji. Domyślnie usługa Azure Site Recovery tworzy nowe zasady replikacji przy użyciu ustawień domyślnych:

* 24 godziny dla historii przechowywania punktów odzyskiwania.
* 60 minut w celu częstotliwość migawek spójności aplikacji.

[Dowiedz się więcej](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#configure-replication-settings).

### <a name="what-is-a-crash-consistent-recovery-point"></a>Co to jest punkt odzyskiwania spójnego na poziomie awarii?
Punktu odzyskiwania spójnego na poziomie awarii reprezentuje dane na dysku, tak, jakby maszyny Wirtualnej uległ awarii lub przewód zasilający została ściągnięta z serwera w momencie utworzenia migawki. Nie obejmuje wszystko, co zostało w pamięci, gdy migawka została utworzona.

Obecnie większość aplikacji można odzyskać dobrze z migawek spójnych awaryjnie. Punktu odzyskiwania spójnego na poziomie awarii jest wystarczająco zwykle dla bazy danych nie systemów operacyjnych i aplikacji, takich jak serwery plików, serwery DHCP i serwery wydruku.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Co to jest częstotliwość generowania punktu odzyskiwania spójnego na poziomie awarii?
Usługa Site Recovery tworzy punkt odzyskiwania spójnego na poziomie awarii, co 5 minut.

### <a name="what-is-an-application-consistent-recovery-point"></a>Co to jest punkt odzyskiwania zapewniających spójność aplikacji?
Punkty odzyskiwania spójne na poziomie aplikacji są tworzone na podstawie migawki spójne z aplikacjami. Punktów odzyskiwania zapewniających spójność aplikacji przechwytywać te same dane jako migawki spójne na poziomie awarii, dodając wszystkie dane w pamięci i wszystkie trwające transakcje.
Ze względu na ich zawartość dodatkowa migawki spójne z aplikacjami są najbardziej zaangażowani i trwało najdłużej wykonywanie. Firma Microsoft zaleca punktów odzyskiwania zapewniających spójność aplikacji dla systemów operacyjnych bazy danych i aplikacji, takich jak SQL Server.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Jaki jest wpływ punktów odzyskiwania zapewniających spójność aplikacji na wydajność aplikacji?
Biorąc pod uwagę punktów odzyskiwania zapewniających spójność aplikacji przechwytuje wszystkie dane w pamięci i w procesie wymaga środowiska, takiego jak usługi VSS w systemie windows, aby przełączyć w stan spoczynku aplikacji. Jeśli będą wykonywane bardzo często może to mieć wpływ na wydajność, jeśli obciążenie jest już bardzo zajęty. Zazwyczaj zalecane jest nie należy używać niskiej częstotliwości dla punktów odzyskiwania spójnego na poziomie aplikacji dla obciążeń innych baz danych, a nawet w przypadku obciążeń bazy danych 1 godzina jest wystarczająca.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Jaka jest minimalna częstotliwość generowania punktów odzyskiwania zapewniających spójność aplikacji?
Usługa Site Recovery może tworzy punkt odzyskiwania zapewniających spójność aplikacji z minimalna częstotliwość w ciągu 1 godziny.

### <a name="how-are-recovery-points-generated-and-saved"></a>Jak punkty odzyskiwania wygenerowana i zapisana?
Aby dowiedzieć się, jak Usługa Site Recovery generuje punktów odzyskiwania, Spójrzmy na przykład zasady replikacji, która ma odzyskiwania punktu okna przechowywania 24 godzin i migawki spójny na poziomie aplikacji częstotliwości równej 1 godz.

Usługa Site Recovery tworzy punkt odzyskiwania spójnego na poziomie awarii, co 5 minut. Użytkownik nie może zmienić tę częstotliwość. Tak w ciągu ostatniej 1 godziny, użytkownik będzie miał 12 spójnych awaryjnie punkty i 1 spójny na poziomie aplikacji punktu do wyboru. W miarę postępów czasu śliwek Site Recovery wszystkie odzyskiwania wskazuje poza ostatniej godzinie i zapisuje odzyskiwania tylko 1 wskazują na godzinę.

Poniższy zrzut ekranu przedstawia przykład. Na zrzucie ekranu:

1. Czas mniejsza od ostatniej godzinie istnieją punkty odzyskiwania przy częstotliwości równej 5 minut.
2. Czas poza ostatniej 1 godziny Usługa Site Recovery przechowuje punkt odzyskiwania tylko 1.

   ![Lista punktów odzyskiwania wygenerowany](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)


### <a name="how-far-back-can-i-recover"></a>Jak daleko można odzyskać?
Najstarszy punkt odzyskiwania, którego można użyć to 72 godziny.

### <a name="what-will-happen-if-i-have-a-replication-policy-of-24-hours-and-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>Co się stanie, jeśli zasady replikacji, 24 godzin i problem uniemożliwia Site Recovery z generowania punktów odzyskiwania dla ponad 24 godzin? Moje poprzednie punkty odzyskiwania zostaną utracone?
Nie, Usługa Site Recovery zostanie zachowana, wszystkie poprzednie punkty odzyskiwania. W zależności od czasu przechowywania punktów odzyskiwania, 24 godzin w tym przypadku Usługa Site Recovery zastępuje najstarszy punkt tylko wtedy, gdy generowanie nowych punktów. W tym przypadku, ponieważ nie będzie żadnych nowy punkt odzyskiwania, które są generowane ze względu na problem, stare punkty pozostaje niezmieniony po osiągniemy okna przechowywania.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Po włączeniu replikacji na maszynie Wirtualnej, jak zmienić zasady replikacji?
Przejdź do **magazyn usługi Site Recovery** > **infrastruktura usługi Site Recovery** > **zasady replikacji**. Wybierz zasady, które chcesz edytować, a następnie zapisz zmiany. Wszelkie zmiany będą dotyczyć wszystkich istniejących replikacje zbyt.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Czy wszystkie punkty odzyskiwania to kompletna kopia maszyny Wirtualnej lub różnicowej?
Pierwszy punkt odzyskiwania, który jest generowany jest kompletna kopia. Wszystkie punkty odzyskiwania kolejnych mają replikowanie zmian różnicowych.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>Zwiększenie okres przechowywania punktów odzyskiwania wzrostu kosztów magazynu?
Tak. Jeśli zwiększysz okres przechowywania z 24 godzin do 72 godzin, Usługa Site Recovery zapisze punktów odzyskiwania dodatkowe 48 godzin. Dodano czasu będą naliczane opłaty za magazyn. Na przykład jeśli wprowadzono zmiany różnicowe wynosi 10 GB, punkt odzyskiwania jednego, a koszt za każdy Gigabajt jest $0.16 miesięcznie, dodatkowe opłaty będzie $1.6 * 48 miesięcznie.

## <a name="multi-vm-consistency"></a>Spójność wielu maszyn wirtualnych

### <a name="what-is-multi-vm-consistency"></a>Co to jest spójność wielu maszyn wirtualnych?
Oznacza to, upewniając się, że punkt odzyskiwania są spójne dla wszystkich replikowanych maszyn wirtualnych.
Usługa Site Recovery udostępnia opcję "Spójności wielu maszyn wirtualnych,", który po wybraniu, tworzy grupę replikacji replikacji ze sobą wszystkich komputerów, które są częścią grupy.
Wszystkie maszyny wirtualne będą udostępniane punkty odzyskiwania spójne na poziomie awarii i spójny na poziomie aplikacji, gdy masz przełączone w tryb failover.
Zapoznaj się z artykułem samouczka, aby [włączyć spójność wielu maszyn wirtualnych](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#enable-replication-for-a-vm).

### <a name="can-i-failover-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>Czy mogę trybu failover jednej maszyny wirtualnej w grupie replikacji spójności wielu maszyn wirtualnych?
Wybranie opcji "-spójność wielu maszyn wirtualnych" są informujący, że aplikacja ma zależności na wszystkich maszynach wirtualnych w grupie. W związku z tym tryb failover jednej maszyny wirtualnej jest niedozwolona.

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>Ile maszyn wirtualnych można replikować jako część grupy replikacji spójności wielu maszyn wirtualnych?
Teraz można replikować 16 maszyny wirtualne razem w grupie replikacji.

### <a name="when-should-i-enable-multi-vm-consistency-"></a>Kiedy należy włączyć spójność wielu maszyn wirtualnych?
Ponieważ jest intensywnie korzystających z procesora CPU, włączenie spójności wielu maszyn wirtualnych może wpłynąć na wydajność obciążenia. Należy używać tylko wtedy, gdy maszynach są uruchomione to samo obciążenie i jest wymagana spójność między wieloma maszynami. Na przykład jeśli masz dwa wystąpienia programu SQL Server i dwóch serwerów sieci web w aplikacji, powinny mieć spójności wielu maszyn wirtualnych tylko wystąpień programu SQL Server.


## <a name="failover"></a>Tryb failover

### <a name="how-is-capacity-assured-in-target-region-for-azure-vms"></a>Jak pojemność zapewniona jest w regionie docelowym dla maszyn wirtualnych platformy Azure?
Zespół usługi Site Recovery współpracuje z zespołem zarządzania pojemność na platformie Azure, aby zaplanować pojemność infrastruktury jest wystarczająca, aby mieć pewność, że włączony na potrzeby odzyskiwania po awarii maszyn wirtualnych zostanie wdrożony pomyślnie w regionie docelowym, gdy tryb failover jest inicjowane.

### <a name="is-failover-automatic"></a>Czy tryb failover jest automatyczny?

Tryb failover nie jest automatyczny. Uruchom tryb failover jednym kliknięciem w portalu lub użyć [PowerShell](azure-to-azure-powershell.md) do wyzwolenia przejścia w tryb failover.

### <a name="can-i-retain-a-public-ip-address-after-failover"></a>Po włączeniu trybu failover można zachować publiczny adres IP?

Publiczny adres IP aplikacji produkcyjnej nie mogą być przechowywane po włączeniu trybu failover.
- Obciążenia wznowione, jako część procesu pracy awaryjnej musi być przypisany do zasobu platformy Azure publicznego adresu IP, która jest dostępna w regionie docelowym.
- Można to zrobić ręcznie lub zautomatyzowania tego procesu z planu odzyskiwania.
- Dowiedz się, jak [konfigurowanie publicznych adresów IP, po włączeniu trybu failover](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan).  

### <a name="can-i-retain-a-private-ip-address-during-failover"></a>Podczas pracy awaryjnej można zachować prywatny adres IP?
Tak, można zachować prywatny adres IP. Domyślnie po włączeniu funkcji odzyskiwania po awarii dla maszyn wirtualnych platformy Azure, Usługa Site Recovery tworzy zasobów docelowych na podstawie ustawień zasobu źródła. – W przypadku maszyn wirtualnych platformy Azure skonfigurowano przy użyciu statycznych adresów IP usługa Site Recovery próbuje udostępniania tego samego adresu IP dla docelowej maszyny Wirtualnej, jeśli nie jest używany.
Dowiedz się więcej o [zachowaniu adresów IP podczas pracy awaryjnej](site-recovery-retain-ip-azure-vm-failover.md).

### <a name="after-failover-why-is-the-server-assigned-a-new-ip-address"></a>Po włączeniu trybu failover, dlaczego serwer przypisano adres IP?

Usługa Site Recovery próbuje Podaj adres IP w momencie przejścia w tryb failover. Jeśli innej maszyny wirtualnej trwa ten adres, Usługa Site Recovery ustawia następny dostępny adres IP jako element docelowy.
Dowiedz się więcej o [konfigurowania mapowania sieci i adresowania IP dla sieci wirtualnych](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>Co to są **najnowsze (najniższy cel punktu odzyskiwania)** punktów odzyskiwania?
**Najnowsze (najniższy cel punktu odzyskiwania)** opcja najpierw przetwarza wszystkie dane, które została wysłana do usługi Site Recovery, aby utworzyć punkt odzyskiwania dla każdej maszyny Wirtualnej przed przechodzenie w tryb failover do niego. Ta opcja zapewnia najniższy cel punktu odzyskiwania (RPO), ponieważ maszyna wirtualna utworzona po trybu failover zawiera wszystkie dane, które są replikowane do usługi Site Recovery podczas pracy w trybie failover zostało wyzwolone.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>Czy **najnowsze (najniższy cel punktu odzyskiwania)** punkty odzyskiwania mają wpływ na czas RTO trybu failover?
Tak. Usługa Site Recovery przetwarza wszystkie oczekujące dane przed przechodzenia w tryb failover, dzięki czemu ta opcja ma wyższe cel czasu odzyskiwania (RTO) w porównaniu do innych opcji.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>Jak działa **najnowszy przetworzony** opcji odzyskiwania wskazuje średniej?
**Ostatniego przetwarzania** opcji przejdzie w tryb failover wszystkich maszyn wirtualnych w planie odzyskiwania najnowsze punktu tego usługa Site Recovery przetworzone. Aby zobaczyć najnowszy punkt dla określonej maszyny Wirtualnej odzyskiwania, sprawdź **najnowsze punkty odzyskiwania** w ustawieniach maszyny Wirtualnej. Ta opcja zapewnia niską wartość, ponieważ nie jest zużywany czas przetwarzania nieprzetworzonych danych.

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>Co się stanie, jeśli wystąpi nieoczekiwana awaria mojego regionu podstawowego?
Możesz wyzwolić tryb failover, po awarii. Usługa Site Recovery nie wymaga łączności z regionu podstawowego do wykonania pracy w trybie failover.

### <a name="what-is-a-rto-of-a-vm-failover-"></a>Co to jest RTO przejścia w tryb failover maszyny Wirtualnej?
Usługa Site Recovery ma [SLA RTO 2 godziny](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). Jednak w większości przypadków, Usługa Site Recovery pracy awaryjnej maszyn wirtualnych w ciągu kilku minut. Można obliczyć czas RTO, przechodząc do pracy w trybie failover zadań, która przedstawia czas, jaki zajęło Aby przenieść maszynę wirtualną. Do odzyskiwania należy zaplanować czas RTO, można znaleźć poniżej.

## <a name="recovery-plans"></a>Plany odzyskiwania

### <a name="what-is-a-recovery-plan"></a>Co to jest plan odzyskiwania?
Plan odzyskiwania w usłudze Site Recovery organizuje odzyskiwanie maszyn wirtualnych w trybie failover. Jego ułatwia odzyskiwanie spójnie dokładne, powtarzalnych i zautomatyzowanych. Plan odzyskiwania rozwiązuje następujące wymagania dla użytkownika:

- Zdefiniowanie grupy maszyn wirtualnych, które razem przechodzą
- Definiowanie zależności między maszynami wirtualnymi, aby dokładnie funkcjonuje aplikacji
- Automatyzowanie odzyskiwania oraz niestandardowe działania ręczne do osiągnięcia zadań innych niż tryb failover maszyn wirtualnych

[Dowiedz się więcej](site-recovery-create-recovery-plans.md) informacje o planach odzyskiwania.

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>Jak odbywa się sekwencjonowania w ramach planu odzyskiwania?

W planie odzyskiwania można utworzyć wielu grup w celu osiągnięcia sekwencjonowania. Każda grupa awaryjnie w tym samym czasie. W przypadku maszyn wirtualnych, które są częścią tego samego trybu grupy za pośrednictwem razem następuje innej grupy. Aby uzyskać informacje o modelu aplikacji przy użyciu planu odzyskiwania, zobacz [informacje o planach odzyskiwania](recovery-plan-overview.md#model-apps).

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Jak znaleźć RTO planu odzyskiwania?
Aby sprawdzić czas RTO planu odzyskiwania, należy przetestować tryb failover planu odzyskiwania, a następnie przejdź do **zadania usługi Site Recovery**.
W poniższym przykładzie zadanie o nazwie SAPTestRecoveryPlan trwało 8 minut i 59 sekund do trybu failover wszystkich maszyn wirtualnych i wykonywania określonej akcji.

![Lista zadania usługi Site Recovery](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>Elementy runbook usługi automation można dodać do planu odzyskiwania?
Tak, możesz zintegrować elementów runbook usługi Azure Automation do planu odzyskiwania. [Dowiedz się więcej](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Ponownego włączania ochrony i powrotu po awarii

### <a name="after-a-failover-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>Po przejściu w tryb failover z regionu podstawowego do regionu odzyskiwania po awarii czy maszyny wirtualne w regionie odzyskiwania po awarii, chroniona automatycznie?
Nie. Gdy użytkownik [w trybie Failover](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) maszyn wirtualnych platformy Azure z jednego regionu do innego, maszyny wirtualne, rozruch w regionie odzyskiwania po awarii w stanie niechronionym. Aby wykonać powrotu po awarii maszyn wirtualnych do regionu podstawowego, należy [ponownie włączyć ochronę](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect) maszyny wirtualne w regionie pomocniczym.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>Podczas ponownego włączania ochrony jest usługa Site Recovery replikowany kompletne dane z regionu pomocniczego do regionu podstawowego?
To zależy od sytuacji. Na przykład: Jeśli region źródłowej maszyny Wirtualnej istnieje, synchronizowane będą tylko zmiany od dysku źródłowego i docelowego dysku. Usługa Site Recovery oblicza różnice przez porównanie dysków i następnie przesyłania danych. Ten proces zwykle zajmuje kilka godzin. Aby uzyskać więcej informacji na temat zachodzących podczas ponownego włączania ochrony, zobacz [ponowne objęcie ochroną przełączone w tryb failover maszyn wirtualnych platformy Azure w regionie podstawowym]( https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection).

### <a name="how-much-time-does-it-take-to-fail-back"></a>Ile czasu zajmuje take do powrotu po awarii?
Po ponownego włączania ochrony ilość czasu na potrzeby powrotu po awarii jest zazwyczaj podobne do czasu, który był konieczny do trybu failover z regionu podstawowego do regionu pomocniczego.

## <a name="capacity"></a>Pojemność

### <a name="how-is-capacity-assured-in-target-region-for-azure-vms"></a>Jak pojemność zapewniona jest w regionie docelowym dla maszyn wirtualnych platformy Azure?
Zespół usługi Site Recovery współpracuje z zespołem zarządzania pojemność na platformie Azure, aby zaplanować pojemność infrastruktury jest wystarczająca, aby mieć pewność, że włączony na potrzeby odzyskiwania po awarii maszyn wirtualnych zostanie pomyślnie wdrożony w regionie docelowym, gdy tryb failover jest inicjowane.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Czy usługa Site Recovery współpracuje z wystąpień zarezerwowanych?
Tak, możesz kupić [zarezerwować wystąpień](https://azure.microsoft.com/pricing/reserved-vm-instances/) w regionie odzyskiwania po awarii, a usługa Site Recovery operacji trybu failover używają ich. </br> Dodatkowa konfiguracja nie jest potrzebna.


## <a name="security"></a>Bezpieczeństwo

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Czy dane replikacji są wysyłane do usługi Site Recovery?
Nie, Usługa Site Recovery nie przechwytuje replikowanych danych, nie ma żadnych informacji na temat co działa na maszynach wirtualnych. Do usługi Site Recovery są wysyłane jedynie metadane wymagane do organizowania replikacji i trybu failover.  
Usługa Site Recovery jest ISO 27001: 2013, 27018, HIPAA, DPA certyfikowane i jest w trakcie SOC2 i FedRAMP JAB.

### <a name="does-site-recovery-encrypt-replication"></a>Czy usługa Site Recovery szyfruje replikację?
Tak, zarówno szyfrowanie przesyłanych w i [szyfrowania podczas spoczynku w usłudze Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) są obsługiwane.

## <a name="next-steps"></a>Kolejne kroki
* [Przegląd](azure-to-azure-support-matrix.md) wymagań.
* [Konfigurowanie](azure-to-azure-tutorial-enable-replication.md) replikacji Azure – Azure.
- Jeśli masz pytania dotyczące po przeczytaniu tego artykułu, opublikuj je na [forum usług Azure Recovery Services](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).
