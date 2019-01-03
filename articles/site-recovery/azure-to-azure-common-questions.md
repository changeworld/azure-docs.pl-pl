---
title: Typowe pytania — odzyskiwanie po awarii Azure – Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera podsumowanie często zadawane pytania, podczas konfigurowania odzyskiwania po awarii maszyn wirtualnych platformy Azure do aonther region platformy Azure przy użyciu usługi Azure Site Recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.date: 12/12/2018
ms.topic: conceptual
ms.author: asgang
ms.openlocfilehash: 6fe7152e43640a809ab9f4de39b1c6b599975a20
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/02/2019
ms.locfileid: "53969951"
---
# <a name="common-questions---azure-to-azure-replication"></a>Często zadawane pytania — replikacji Azure – Azure

Ten artykuł zawiera odpowiedzi na typowe pytania, które widać w przypadku wdrażania odzyskiwania po awarii maszyn wirtualnych platformy Azure do innego regionu platformy Azure. Jeśli masz pytania dotyczące po przeczytaniu tego artykułu, opublikuj je na [Forum usług odzyskiwania Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Ogólne
### <a name="how-is-site-recovery-priced"></a>Jak jest rozliczana Usługa Site Recovery?
Przegląd [cennika usługi Azure Site Recovery](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) szczegółowe informacje.

### <a name="how-to-configure-site-recovery-on-azure-vms-what-are-the-best-practices"></a>Jak skonfigurować Site Recovery na maszynach wirtualnych platformy Azure. Jakie są najlepsze rozwiązania?
1. [Informacje na temat architektury Azure – Azure](azure-to-azure-architecture.md)
2. [Przejrzyj konfiguracje obsługiwane i nieobsługiwane](azure-to-azure-support-matrix.md)
3. [Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych platformy Azure](azure-to-azure-how-to-enable-replication.md)
4. [Wykonywanie testu przejścia w tryb failover](azure-to-azure-tutorial-dr-drill.md)
5. [Tryb failover i powrót po awarii do regionu podstawowego](azure-to-azure-tutorial-failover-failback.md)

## <a name="replication"></a>Replikacja

### <a name="can-i-replicate-azure-disk-encryption-enabled-vms"></a>Azure może replikować maszyny wirtualne z włączoną funkcją szyfrowania dysków?
Tak, można replikować je. Zapoznaj się [artykułu](azure-to-azure-how-to-enable-replication-ade-vms.md) umożliwiające maszyn wirtualnych z włączoną replikacją z usługa Azure disk encryption (ADE). Należy pamiętać, że tylko maszyny wirtualne platformy Azure z systemem operacyjnym Windows OS i włączeniu obsługi szyfrowania za pomocą usługi Azure AD aplikacji są obecnie obsługiwane przez usługę Azure Site Recovery.

### <a name="can-i-replicate-vms-to-another-subscription"></a>Maszyny wirtualne można replikować do innej subskrypcji?
Tak, można replikować maszyny wirtualne platformy Azure do innej subskrypcji przy użyciu tej samej dzierżawy usługi Azure Active Directory.
Konfigurowanie odzyskiwania po awarii [w subskrypcjach](https://azure.microsoft.com/blog/cross-subscription-dr) jest proste. Możesz wybrać inną subskrypcję w czasie replikacji.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Czy można replikować maszyny wirtualne platformy Azure przypięte w strefie, do innego regionu.
Tak, można [replikowanie maszyn wirtualnych przypięte w strefie](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) do innego regionu.

### <a name="can-i-exclude-disks"></a>Czy można wykluczyć dysków?

Tak, można wykluczyć dyski w czasie ochrony za pomocą powłoki power shell. Zapoznaj się [wskazówki dotyczące programu powershell](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine) , aby wykluczyć dysk

###<a name="how-often-can-i-replicate-to-azure"></a>Jak często można replikować na platformę Azure?
Replikacja jest ciągłe podczas replikowania maszyn wirtualnych platformy Azure do innego regionu platformy Azure. Sprawdź [Azure – Azure](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process) architektura replikacji, aby poznać szczegóły.

### <a name="can-i-replicate-virtual-machines-within-a-same-region-i-need-this-to-migrate-vms"></a>Czy można replikować maszyny wirtualne w tym samym regionie? Należy to przeprowadzić migrację maszyn wirtualnych?
Za pomocą rozwiązania odzyskiwania po awarii Azure – Azure nie można replikować maszyny wirtualne w tym samym regionie.

### <a name="can-i-replicate-vms-to-any-azure-region"></a>Maszyny wirtualne można replikować do dowolnego regionu systemu Azure?
Usługa Site Recovery może replikowanie i odzyskiwanie maszyn wirtualnych między wszystkie dwóch regionach w obrębie tego samego klastra geograficznego. Klastry geograficzne są zdefiniowane, pamiętając o opóźnieniu przesyłania danych i niezależność danych. Aby uzyskać więcej informacji, zobacz Site Recovery [macierz obsługi region](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support).

### <a name="does-site-recovery-require-internet-connectivity"></a>Usługa Site Recovery wymaga łączności z Internetem?

Nie, Usługa Site Recovery nie wymaga łączności z Internetem, ale dostęp do adresów URL Site Recovery i zakresami IP zgodnie z opisem w tym [artykułu](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)

## <a name="replication-policy"></a>Zasady replikacji

### <a name="what-is-a-replication-policy"></a>Co to są zasady replikacji?
Definiuje ustawienia odzyskiwania punktu przechowywania historii i aplikacji częstotliwość wykonywania migawek. Domyślnie usługa Azure Site Recovery tworzy nowe zasady replikacji z ustawieniami domyślnymi 24 godzin czas przechowywania punktu odzyskiwania i "60 minut częstotliwość migawek spójnych na poziomie aplikacji.

### <a name="what-is-crash-consistent-recovery-point"></a>Co to jest punkt odzyskiwania spójnego na poziomie awarii?
Punktu odzyskiwania spójnego na poziomie awarii reprezentuje dane na dysku, tak, jakby maszyny Wirtualnej uległ awarii lub przewód zasilający została ściągnięta z serwera w momencie utworzenia migawki. Nie obejmuje wszystko, co zostało w pamięci, gdy migawka została utworzona. Obecnie większość aplikacji można odzyskać od awarii migawki spójne z aplikacjami. Punktu odzyskiwania spójnego na poziomie awarii jest wystarczająco zwykle nie bazy danych, systemów operacyjnych i aplikacji, takich jak serwery plików, serwery DHCP, serwerów wydruku i tak dalej.

### <a name="what-is-application-consistent-recovery-point"></a>Co to jest punkt odzyskiwania zapewniających spójność aplikacji? 
Punkty odzyskiwania spójne na poziomie aplikacji są tworzone na podstawie migawek spójności aplikacji, które przechwytują te same dane jako migawki spójne na poziomie awarii, dodając wszystkie dane w pamięci i wszystkie trwające transakcje. Ze względu na ich zawartość dodatkowa migawki spójne z aplikacjami są najbardziej zaangażowani i trwało najdłużej wykonywanie. Punkty odzyskiwania spójne z aplikacjami są zalecane dla systemów operacyjnych bazy danych i aplikacji, takich jak SQL.

### <a name="how-are-recovery-points-generated-and-saved"></a>Jak punkty odzyskiwania wygenerowana i zapisana?
Aby zrozumieć, jak Usługa Site Recovery generuje punkty odzyskiwania umożliwia przykładzie zasad replikacji, która ma zakres czasu 24-godzinnym czasem przechowywania i aplikacji migawki częstotliwość godzinę punktu odzyskiwania.
Usługa Site Recovery tworzy punktu spójnego na poziomie awarii, co 5 minut, a użytkownik nie ma dowolną kontrolkę, aby zmienić tę częstotliwość. W związku z tym w dla ostatniej godziny użytkownik będzie miał 12 punktów spójnego na poziomie awarii i 1 punkt spójny aplikacji do wyboru. W miarę postępów czasu śliwek Site Recovery, wszystkie punkty odzyskiwania poza Ostatnia godzina i zapisać tylko jeden punkt na godzinę.
Do celów ilustracyjnych na poniższym zrzucie ekranu:


1. Po raz ostatni mniej niż 1 godzina istnieją punkty odzyskiwania z częstotliwością wynoszącą 5 minut.
2. Czas poza Ostatnia godzina widać, że będzie przechowywany tylko jeden punkt odzyskiwania na godzinę.

  ![Generowanie punktów odzyskiwania](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)


### <a name="how-far-back-can-i-recover"></a>Jak daleko można odzyskać?
Najstarszy punkt odzyskiwania, których można użyć to 72 godziny.

### <a name="what-will-happen-if-i-have-a-replication-policy-of-24-hours-and-there-is-no-recovery-points-generation-due-to-some-issue-for-more-than-24-hours-does-my-previous-recovery-points-will-be-pruned"></a>Co się stanie, jeśli mam zasad replikacji w 24-godzinnym czasem i nie ma żadnych punktów Generowanie odzyskiwania z powodu problemu z niektórych dłużej niż 24 godziny. Czy Moje poprzednie odzyskiwania, które będą usuwane punkty?
Nie, Usługa Site Recovery zostanie zachowana, wszystkie poprzednie punkty odzyskiwania w tym przypadku. 

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Po włączeniu replikacji na maszynie Wirtualnej, jak zmienić zasady replikacji? 
Przejdź do magazynu usługi Site Recovery > infrastruktura usługi Site Recovery > zasady replikacji. Wybierz zasady, którą chcesz edytować, a następnie zapisz zmiany. Wszelkie zmiany będą dotyczyć wszystkich istniejących replikacje zbyt. 

### <a name="are-all-the-recovery-points-complete-copy-of-the-vm-or-differential"></a>Czy wszystkie punkty odzyskiwania kompletna kopia maszyny Wirtualnej lub różnicowej?
W przypadku początkowej replikacji pierwszego punktu odzyskiwania, który pobiera wygenerowany będzie miał pełnej kopii, a wszystkie punkty odzyskiwania kolejnych będzie zawierał replikowanie zmian różnicowych.

### <a name="does-increasing-recovery-points-retention-windows-increases-the-storage-cost"></a>Zwiększenie okna przechowywania punktów odzyskiwania powoduje zwiększenie kosztów magazynu?
Tak, jeśli zwiększysz okres przechowywania z 24 godzin do 72 godzin, a następnie Usługa Site Recovery zapisze punktów odzyskiwania do dodania 48 godzin, które będą naliczane z zastosowaniem możesz opłaty za magazyn. Na przykład jeśli punkt odzyskiwania jednego zmiany różnicowe wynosi 10 GB i każdy gigabajt jest $0.16 na miesiąc wyniósłby $1.6 * 48 dodatkowe opłaty za miesiąc.

## <a name="failover"></a>Tryb failover

### <a name="is-failover-automatic"></a>Czy tryb failover jest automatyczny?

Tryb failover nie jest automatyczny. Zainicjuj tryb failover z jednym kliknięciem w portalu lub możesz użyć usługi Site Recovery [PowerShell](azure-to-azure-powershell.md) do wyzwolenia przejścia w tryb failover. 

### <a name="can-i-retain-public-ip-address-after-failover"></a>Po włączeniu trybu failover można zachować publiczny adres IP?

Publiczny adres IP aplikacji produkcyjnej **nie mogą być przechowywane w trybie failover**. Obciążenia wznowione, jako część procesu pracy awaryjnej musi być przypisany zasób publicznego adresu IP usługi Azure dostępne w regionie docelowym. Ten krok można wykonać albo ręcznie lub jest automatycznie przy użyciu planów odzyskiwania. Zapoznaj się [artykułu](https://docs.microsoft.com/azure/site-recovery/concepts-public-ip-address-with-site-recovery#public-ip-address-assignment-using-recovery-plan) można przypisać publiczny adres IP przy użyciu planu odzyskiwania.  

### <a name="can-i-retain-private-ip-address-during-failover"></a>Podczas pracy awaryjnej można zachować prywatny adres IP?
Tak, można zachować prywatny adres IP. Domyślnie po włączeniu funkcji odzyskiwania po awarii dla maszyn wirtualnych platformy Azure, Usługa Site Recovery tworzy zasobów docelowych na podstawie ustawień zasobu źródła. Skonfigurowano statyczne adresy IP maszyn wirtualnych platformy Azure Usługa Site Recovery próbuje udostępniania tego samego adresu IP dla docelowej maszyny Wirtualnej, jeśli nie jest używany. Zapoznaj się [artykułu](site-recovery-retain-ip-azure-vm-failover.md) zachować prywatny adres IP w różnych warunkach.

### <a name="after-failover-the-server-does-not-have-the-same-ip-address-as-the-source-vm-why-is-it-assigned-with-a-new-ip-address"></a>Po przejściu w tryb failover serwera nie ma ten sam adres IP co źródłowa maszyna wirtualna. Dlaczego jest ona przypisana z nowego adresu IP?

Usługa Site Recovery próbuje adres IP na optymalne rozwiązanie w momencie przejścia w tryb failover. W przypadku, gdy jest wykonywana przez inną maszynę wirtualną, następny dostępny adres IP jest ustawiony jako element docelowy. Aby uzyskać pełne wyjaśnienie, jak Usługa Site Recovery obsługuje adresowania [zapoznaj się z tym artykułem.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-network-mapping#set-up-ip-addressing-for-target-vms)

### <a name="what-does-latestlowest-rpo-recovery-points-means"></a>Działanie odzyskiwania najnowsze (najniższy RPO) wskazuje oznacza?
Ta opcja najpierw przetwarza wszystkie dane, które została wysłana do usługi Site Recovery, aby utworzyć punkt odzyskiwania dla każdej maszyny Wirtualnej przed przechodzenie w tryb failover do niego. Ta opcja zapewnia najniższy cel punktu odzyskiwania (cel punktu odzyskiwania), ponieważ maszyna wirtualna utworzona po pracy awaryjnej, będzie miał wszystkie dane, które są replikowane do usługi Site Recovery podczas pracy w trybie failover zostało wyzwolone.

### <a name="does-latest-lowest-rpo-recovery-points-have-impact-on-failover-rto"></a>(Najniższy cel punktu odzyskiwania) najnowsze punkty odzyskiwania mają wpływ na czas RTO trybu Failover
Tak, jak Usługa Site Recovery będzie przetwarzać wszystkie oczekujące dane przed przechodzenie w tryb failover, ta opcja ma wyższy wskaźnik RTO, w porównaniu do innych użytkowników.

### <a name="what-does-latest-processed-option-in-recovery-points-mean"></a>Co to jest najnowsza wersja przetwarzania opcji w oznaczają punkty odzyskiwania?
Ta opcja nie powiedzie się za pośrednictwem wszystkich maszyn wirtualnych w planie do najnowszego punktu odzyskiwania przetworzonego przez usługę Site Recovery. Aby wyświetlić najnowszy punkt dla określonej maszyny Wirtualnej odzyskiwania, należy sprawdzić najnowsze punkty odzyskiwania w ustawieniach maszyny Wirtualnej. Ta opcja zapewnia niską wartość celu czasu odzyskiwania, ponieważ nie wymaga przetwarzania nieprzetworzonych danych.

### <a name="if-im-replicating-between-two-azure-regions-what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>Jeśli Przeprowadzam replikację między dwoma regionami platformy Azure co się stanie, jeśli wystąpi nieoczekiwana awaria mojego regionu podstawowego?
Możesz wyzwolić tryb failover, po awarii. Usługa Site Recovery nie wymaga łączności z regionu podstawowego do wykonania pracy w trybie failover.

## <a name="recovery-plan"></a>Plan odzyskiwania

### <a name="what-is-a-recovery-plan-"></a>Co to jest planu odzyskiwania?
Plany odzyskiwania w usłudze Site Recovery możesz odpowiednio organizować odzyskiwanie maszyn wirtualnych w trybie failover. Jego ułatwia odzyskiwanie spójnie dokładne, powtarzalnych i zautomatyzowanych. Plan odzyskiwania rozwiązuje następujące wymagania dla użytkownika:

- Zdefiniowanie grupy wirtualnej maszyny tryb failover ze sobą.
- Definiowanie zależności między maszynami wirtualnymi, aby dokładnie funkcjonuje aplikacji.
- Automatyzowanie odzyskiwania oraz akcje ręczne niestandardowe, aby robić również zadań innych niż tryb failover maszyn wirtualnych.

[Dowiedz się więcej](site-recovery-create-recovery-plans.md) informacje o planach odzyskiwania.

### <a name="how-does-sequencing-is-achieved-in-a-recovery-plan"></a>Jak robi sekwencjonowania odbywa się w planie odzyskiwania?

W planie odzyskiwania można utworzyć wielu grup w celu osiągnięcia sekwencjonowania. Każdej grupy trybu failover, w czasie, co oznacza maszyn wirtualnych, które są częścią tej samej grupy przejdą w tryb failover ze sobą następuje innej grupy. Sprawdź, jak [aplikacji modelu przy użyciu planu odzyskiwania.](https://review.docs.microsoft.com/azure/site-recovery/recovery-plan-overview?branch=pr-en-us-61681#model-apps) 

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Jak znaleźć RTO planu odzyskiwania?
Aby sprawdzić czas RTO planu odzyskiwania, przetestować tryb failover planu odzyskiwania, a następnie przejdź do zadania usługi Site Recovery.
Na przykład jak pokazano poniżej, planu odzyskiwania testu SAP trwało 8 sekund 59 minut w tryb failover wszystkich maszyn wirtualnych i wykonywać akcje określone.

  ![Błąd modelu COM](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>Czy można dodać elementów runbook usługi automation, do planu odzyskiwania?
Tak, możesz zintegrować elementów runbook usługi Azure automation do planu odzyskiwania. [Dowiedz się więcej](site-recovery-runbook-automation.md)

## <a name="reprotect-and-failback"></a>Ponowne włączanie ochrony i powrotu po awarii 

### <a name="after-doing-a-failover-from-primary-region-to-disaster-recovery-region-does-vms-in-a-dr-region-gets-protected-automatically"></a>Po przełączeniem w tryb failover z regionu podstawowego do regionu odzyskiwania po awarii jest maszyny wirtualne w regionie odzyskiwania po awarii jest chroniony automatycznie?
Nie, gdy użytkownik [trybu failover](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) maszyn wirtualnych platformy Azure z jednego regionu do innego, maszyny wirtualne rozruchu w regionie odzyskiwania po awarii w stanie niechronionym. Aby wykonać powrotu po awarii maszyn wirtualnych do regionu podstawowego, należy [ponownie włączyć ochronę](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect) maszyny wirtualne w regionie pomocniczym.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-secondary-region-to-primary-region"></a>W momencie ponownego włączania ochrony jest usługa Site Recovery replikowany kompletne dane z regionu pomocniczego do regionu podstawowego?
To zależy od sytuacji, na przykład jeśli region źródłowej maszyny Wirtualnej istnieje, a następnie synchronizowane będą tylko zmiany od dysku źródłowego i docelowego dysku. Różnice są obliczane przez porównanie obu dyskach tak i następnie przeniesiona. To zwykle trwać kilka godzin. Zapoznaj się [artykułu]( https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection) na naukę szczegółów dotyczących co się dzieje podczas ponownego włączania ochrony.

### <a name="how-much-time-does-it-take-to-failback"></a>Ile czasu zajmuje wykonać powrót po awarii?
Po zakończeniu ponownego włączania ochrony zwykle jest ilość czasu, podobny do trybu failover z regionu podstawowego do regionu pomocniczego. 

## <a name="security"></a>Bezpieczeństwo
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Czy dane replikacji są wysyłane do usługi Site Recovery?
Nie, Usługa Site Recovery nie przechwytuje replikowanych danych, nie ma żadnych informacji na temat co działa na maszynach wirtualnych. Do usługi Site Recovery są wysyłane jedynie metadane wymagane do organizowania replikacji i trybu failover.  
Usługa Site Recovery jest ISO 27001: 2013, 27018, HIPAA, DPA certyfikowane i jest w trakcie SOC2 i FedRAMP JAB.

### <a name="does-site-recovery-encrypt-replication"></a>Czy usługa Site Recovery szyfruje replikację?
Tak, zarówno szyfrowanie przesyłanych w i [szyfrowania na platformie Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) są obsługiwane.

## <a name="next-steps"></a>Kolejne kroki
* [Przegląd](azure-to-azure-support-matrix.md) wymagań.
* [Konfigurowanie](azure-to-azure-tutorial-enable-replication.md) replikacji Azure – Azure.
