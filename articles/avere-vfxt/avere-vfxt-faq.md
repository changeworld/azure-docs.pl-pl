---
title: Często zadawane pytania — vFXT Avere dla platformy Azure
description: Często zadawane pytania dotyczące Avere vFXT dla platformy Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 334b4c912c40949cbecab2173425927d46350d07
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634191"
---
# <a name="avere-vfxt-for-azure-faq"></a>VFXT Avere dla platformy Azure — często zadawane pytania

Ten artykuł zawiera odpowiedzi na pytania, które mogą pomóc w podjęciu decyzji, czy rozwiązanie vFXT Avere jest odpowiednia dla Twoich potrzeb. Ona zapewnia podstawowe informacje dotyczące możliwości Avere vFXT i wyjaśniono, jak działa z innymi składnikami platformy Azure i produkty z zewnętrznych dostawców. 

## <a name="general"></a>Ogólne 

### <a name="what-is-avere-vfxt-for-azure"></a>Co to jest vFXT Avere dla platformy Azure?

VFXT Avere dla platformy Azure jest systemem plików o wysokiej wydajności, który buforuje aktywne dane w obliczenia Azure w celu wydajnego przetwarzania obciążeń o znaczeniu krytycznym.

### <a name="is-the-avere-vfxt-a-storage-solution"></a>To Avere vFXT rozwiązanie do magazynowania?

Nie. Avere vFXT jest system plików **pamięci podręcznej** które dołącza do środowiska pamięci masowej, takich jak usługi EMC lub NetApp NAS lub kontenera obiektów Blob. VFXT ujednolica dane żądania od klientów i buforuje dane, które służy do zwiększenia wydajności na dużą skalę i wraz z upływem czasu. VFXT, sam nie przechowuje danych. Go nie ma informacji o ilości przechowywanych związanych z nim danych.

### <a name="is-the-avere-vfxt-a-tiering-solution"></a>To Avere vFXT warstw rozwiązanie?

Avere vFXT nie automatycznie warstwy danych między warstwami gorąca i chłodna.  

### <a name="how-do-i-know-if-an-environment-is-right-for-the-avere-vfxt"></a>Jak sprawdzić, czy środowisko jest odpowiednia dla Avere vFXT?

Najlepszym sposobem zastanów się, to pytanie jest zadać, "Jest podlega buforowaniu, obciążenie?" Oznacza to, że obciążenie ma wysokie odczytu do zapisu współczynnik — na przykład 80/20 lub 70/30 operacji odczytu/zapisu.

Należy wziąć pod uwagę Avere vFXT dla platformy Azure, jeśli masz opartych na plikach potoku analitycznego, który jest uruchamiany w wielu maszyn wirtualnych platformy Azure i spełnia co najmniej jeden z następujących warunków:

* Ogólna działa wolno lub niespójna z powodu długie czasy dostępu (dziesiątki milisekund lub sekund w zależności od wymagań). Ten czas oczekiwania jest nieodpowiednia dla klienta końcowego.

* Dane potrzebne do przetwarzania znajduje się na końcu koniec środowiska sieci WAN, a następnie trwale przenoszenie danych jest niepraktyczne. Dane mogą być w innym regionie platformy Azure lub w centrum danych klienta.

* Znaczna liczba klientów żądania danych — na przykład w klastrze o wysokiej wydajności obliczeń (HPC). Dużą liczbą jednoczesnych żądań może zwiększyć opóźnienie.

* Użytkownik chce przeprowadzić ich bieżącego potoku "jako — jest" w usłudze Azure virtual machines i potrzeb na podstawie POSIX udostępnionego magazynu (lub buforowanie) rozwiązanie w przypadku skalowalności. Za pomocą Avere vFXT dla platformy Azure, nie trzeba Przekształcanie potoku pracy do macierzystych wywołań do usługi Azure Blob storage.

* Aplikacji HPC opiera się na klientach NFSv3. (W niektórych sytuacjach klienci SMB 2.1 można użyć, ale wydajność jest ograniczona).

   Na poniższym rysunku próbuje uprościć odpowiedź na to pytanie. Im bliżej przepływu pracy jest prawym górnym rogu, tym bardziej prawdopodobne jest, że Avere buforowania rozwiązanie jest odpowiednie dla danego środowiska.

   ![Diagram przedstawiający, że odczycie zadań ładowania z tysięcy klientów są lepsze dopasowane do Avere vFXT](media/avere-vfxt-fit-assessment.png)

### <a name="at-what-scale-of-clients-does-the-avere-vfxt-solution-make-the-most-sense"></a>W jakich skalowania klientów jest Avere rozwiązania vFXT sensu najbardziej?

Rozwiązanie pamięci podręcznej vFXT został opracowany pod kątem obsługi setek, tysięcy lub dziesiątki tysięcy rdzeni obliczeniowych. Jeśli masz kilka komputerów z systemem pracy światła, Avere vFXT nie jest odpowiednim rozwiązaniem.

Typowe klientów vFXT Avere uruchamiania wymagających obciążeń, zaczynając od około 1000 rdzeni procesora CPU. Te środowiska mogą być wynosi 50 000 rdzeni lub więcej. Ponieważ vFXT jest skalowalna, można dodać węzły do obsługi tych obciążeń, wraz z ich potrzebami wymagać więcej przepływności lub więcej operacji We/Wy.

### <a name="how-much-data-can-an-avere-vfxt-environment-store"></a>Jak dużo danych można przechowywać w środowisku vFXT Avere

Avere vFXT jest pamięć podręczna, go nie są w szczególności przechowywania danych. Używa kombinacji pamięci RAM i dysków SSD do przechowywania buforowanych danych. Dane są trwale przechowywane w systemie magazynu zaplecza (na przykład system NetApp NAS lub kontenera obiektów Blob). Avere vFXT system nie ma informacji na temat ilości danych przechowywanych za zaporą vFXT buforuje tylko podzbiór danych żądaną przez klientów.  

### <a name="what-regions-are-supported"></a>Jakie regiony są obsługiwane?

Od 1 listopada 2018 r. vFXT Avere dla platformy Azure jest obsługiwane we wszystkich regionach z wyjątkiem regionów suwerenne (China, Niemcy) i regiony dla instytucji rządowych. Upewnij się, że region, którego chcesz użyć może obsługiwać dużą ilość rdzeni obliczeniowych, jak również potrzebne do utworzenia klastra vFXT Avere wystąpień maszyn wirtualnych. Niezależne regiony i chmury dla instytucji rządowych nie są jeszcze obsługiwane.

### <a name="how-do-i-get-help-with-the-avere-vfxt"></a>Jak uzyskać pomoc dotyczącą Avere vFXT?

Grupy wsparcia wyspecjalizowane oferuje pomoc dotyczącą Avere vFXT dla platformy Azure. Postępuj zgodnie z instrukcjami w [Uzyskaj pomoc dotyczącą systemu](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) otworzyć bilet pomocy technicznej w witrynie Azure portal. 

### <a name="is-the-avere-vfxt-highly-available"></a>Avere vFXT ma wysoką dostępność?

Tak, Avere vFXT działa wyłącznie jako rozwiązania wysokiej dostępności.

### <a name="does-avere-vfxt-for-azure-also-support-other-cloud-services"></a>VFXT Avere dla platformy Azure również obsługuje inne usługi w chmurze?

Tak, klienci mogą używać więcej niż jednego dostawcę chmury z klastrem vFXT Avere. Obsługuje ona, przedziałów standardowa AWS S3 i standardowe przedziały Google Cloud Services, a także kontenery obiektów Blob platformy Azure. 

> [!NOTE] 
> Opłata oprogramowania dotyczą vFXT Avere w usługach AWS i Google Cloud, ale nie z platformą Azure.

## <a name="technical---compute"></a>Zagadnienia techniczne — obliczeniowe

### <a name="can-you-describe-what-an-avere-vfxt-environment-looks-like"></a>Może wystarczy opisać jakiego środowiska vFXT Avere "wygląda"?

Avere vFXT to urządzenie klastrowanego z wielu maszyn wirtualnych platformy Azure. Biblioteka języka Python obsługuje tworzenie klastra, usuwanie i modyfikowanie. Odczyt [co to jest vFXT Avere dla platformy Azure?](avere-vfxt-overview.md) Aby dowiedzieć się więcej. 

### <a name="what-kind-of-azure-virtual-machines-does-the-avere-vfxt-run-on"></a>Jakiego rodzaju maszyn wirtualnych platformy Azure jest Avere vFXT systemem?  

VFXT Avere klastra Azure używa programu Microsoft Azure E32s_v3 lub D16s_v3 maszyn wirtualnych. 

### <a name="can-i-mix-and-match-virtual-machine-types-for-my-cluster"></a>Czy mogę mieszać i dopasowywać typów maszyn wirtualnych dla mojego klastra?

Nie, musisz wybrać jeden typ maszyn wirtualnych lub innych.
    
### <a name="can-i-move-between-virtual-machine-types"></a>Można przenosić między typami maszyny wirtualnej?

Tak, jest ścieżka migracji, aby przenieść jeden typ maszyn wirtualnych. [Otwórz bilet pomocy technicznej](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) Aby dowiedzieć się, jak.

### <a name="does-the-avere-vfxt-environment-scale"></a>Środowisko vFXT Avere skalowany?

Klaster vFXT Avere może być tak małej, jak trzy węzły maszyny wirtualnej lub wynosi 24 węzłów. Aby uzyskać pomoc w planowaniu, jeśli uważasz, że potrzebujesz więcej niż dziewięciu węzłów klastra, skontaktuj się z działem pomocy technicznej platformy Azure. Większą liczbę węzłów wymaga większej architekturze wdrożenia.

### <a name="does-the-avere-vfxt-environment-autoscale"></a>Czy w środowisku vFXT Avere "automatycznego skalowania"?

Nie. Skalowanie rozmiaru klastra w górę i w dół, ale dodawania lub usuwania węzłów klastra jest to krok wykonywany ręcznie.

### <a name="can-i-run-the-vfxt-cluster-as-a-virtual-machine-scale-set"></a>Można uruchomić klaster vFXT jako zestaw skalowania maszyn wirtualnych?

Avere vFXT nie obsługuje wdrażania zestawu (VMSS) skalowania maszyny wirtualnej. Kilka dostępność wbudowanej obsługi mechanizmów są przeznaczone tylko dla niepodzielnych maszyn wirtualnych z uczestnictwa w klastrze.  

### <a name="can-i-run-the-vfxt-cluster-on-low-priority-vms"></a>Klaster vFXT można uruchomić na maszynach wirtualnych o niskim priorytecie?

Nie, system wymaga podstawowej stabilne zestawy maszyn wirtualnych.

### <a name="can-i-run-the-vfxt-cluster-in-containers"></a>Można uruchomić klaster vFXT w kontenerach?

Nie, Avere vFXT musi zostać wdrożony jako aplikacja niezależne.

### <a name="do-the-avere-vfxt-vms-count-against-my-compute-quota"></a>Czy vFXT Avere liczby maszyn wirtualnych przed Moje przydział usługi compute?

Tak. Upewnij się, że masz wystarczającego limitu przydziału w regionie w celu obsługi klastra.  

### <a name="can-i-run-the-avere-vfxt-cluster-machines-in-different-availability-zones"></a>Można uruchomić Avere vFXT maszyn klastra w różnych strefach dostępności?

Nie. Model o wysokiej dostępności, używane w Avere vFXT aktualnie nie obsługuje vFXT poszczególnych elementów członkowskich klastra znajduje się w różnych strefach dostępności.

### <a name="can-i-clone-avere-vfxt-virtual-machines"></a>Czy można sklonować maszyny wirtualne vFXT Avere?

Nie, musisz podać obsługiwanych skrypt w języku Python do dodawania lub usuwania węzłów w klastrze vFXT Avere. Aby uzyskać więcej informacji, przeczytaj [Zarządzanie klastrem vFXT Avere](avere-vfxt-manage-cluster.md).  

### <a name="is-there-a-vm-version-of-the-software-i-can-run-in-my-own-local-environment"></a>Czy istnieje "Maszyny Wirtualnej" w wersji oprogramowania, które można uruchomić w własnego środowiska lokalnego?

Nie, system jest oferowany jako klastrowane urządzenia i testowane na typach określonej maszyny wirtualnej. To ograniczenie pomaga klientom, o których należy unikać tworzenia systemu, który nie spełnia wymagań o wysokiej wydajności typowego przepływu pracy vFXT Avere. 

## <a name="technical---disks"></a>Zagadnienia techniczne — dyski

### <a name="what-type-of-disks-are-supported-for-the-azure-vms"></a>Jakiego rodzaju dysków są obsługiwane w przypadku maszyn wirtualnych platformy Azure?

VFXT Avere dla platformy Azure można używać konfiguracji dysków SSD premium 1 TB lub 4 TB. Konfiguracja dysków SSD premium może być wdrożony jako wielu dysków zarządzanych.

### <a name="does-the-cluster-support-unmanaged-disks"></a>Klaster obsługuje dyski niezarządzane?

Nie, klaster wymaga dysków zarządzanych.

### <a name="does-the-system-support-local-attached-ssds"></a>Czy system obsługuje lokalny SSD (dołączone)?

VFXT Avere dla platformy Azure nie obsługuje obecnie lokalne dyski SSD. Dyskach używanych na potrzeby Avere vFXT musi być w stanie zamknięcia i ponownego uruchomienia, ale można zakończyć tylko lokalne dołączone dyski SSD w tej konfiguracji.

### <a name="does-the-system-support-ultra-ssds"></a>Czy system obsługuje ultra SSD?

Nie, system obsługuje tylko konfiguracje dysków SSD — wersja premium.

### <a name="can-i-detach-my-premium-ssds-and-reattach-them-later-to-preserve-cache-contents-between-use"></a>Można odłączyć Moje dyski SSD w warstwie premium i ponownie podłącz je później, aby zachować zawartość pamięci podręcznej między użycia?

Odłączenie i ponowne dołączenie dysków SSD jest obsługiwany. Zawartość metadanych lub pliku źródłowego, być może zmieniono między używa, co może spowodować problemy z integralnością danych.

### <a name="does-the-system-encrypt-the-cache"></a>Czy system szyfruje pamięci podręcznej?

Dane są rozkładane na dyskach, ale nie są szyfrowane. Jednak same dyski mogą być szyfrowane. Więcej informacji można znaleźć [tutaj](https://docs.microsoft.com/azure/virtual-machines/linux/security-policy#encryption).

## <a name="technical---networking"></a>Technical Preview — sieci

### <a name="what-network-is-recommended"></a>Zaleca się, jakie sieci?

Jeśli za pomocą magazynu lokalnego przy użyciu Avere vFXT, powinny mieć 1 GB/s lub lepszą połączenia sieciowego. Jeśli masz niewielką ilość danych i chcesz skopiować dane do chmury przed uruchomieniem zadania, połączenie sieci VPN może być wystarczające. 

> [!TIP] 
> Jest wolniejsze połączenia sieciowego, tym wolniej początkowej odczyty zimnych. Odczyty powolne zwiększyć opóźnienie procesu roboczego. 

### <a name="can-i-run-the-avere-vfxt-in-a-different-virtual-network-than-my-compute-cluster"></a>Można uruchomić Avere vFXT w innej sieci wirtualnej niż Mój klaster obliczeniowy?

Tak, możesz utworzyć systemu vFXT Avere w innej sieci wirtualnej. Odczyt [Planowanie systemu vFXT Avere](avere-vfxt-deploy-plan.md) Aby uzyskać szczegółowe informacje.

### <a name="does-the-avere-vfxt-require-its-own-subnet"></a>Avere vFXT wymaga własnej podsieci?

Tak. Avere vFXT działa wyłącznie jako klastra o wysokiej dostępności i wymaga wielu adresów IP do działania. Jeśli klaster znajduje się we własnej podsieci, można uniknąć ryzyka konflikty adresów IP, co może powodować problemy instalację i normalnych operacji. W podsieci klastra mogą być zawarte w istniejącej sieci wirtualnej, tak długo, jak brak adresu IP adresów nakładają się na siebie.

### <a name="can-i-run-the-avere-vfxt-on-infiniband"></a>Czy można uruchamiać Avere vFXT Infiniband?

Nie, Avere vFXT używa Ethernet/adresu IP tylko.

### <a name="how-do-i-access-my-on-premises-nas-environment-from-the-avere-vfxt"></a>Jak dostęp do mojego lokalnego środowiska NAS z Avere vFXT?

Środowisko vFXT Avere jest nie różni się od wszelkich innych maszyn wirtualnych platformy Azure, w tym, że wymaga on trasowane dostęp za pośrednictwem bramy sieci lub sieci VPN do klientów centrum danych (i z powrotem). Należy rozważyć użycie połączenia usługi Azure ExpressRoute, jeśli jest dostępna w danym środowisku.

### <a name="what-are-the-bandwidth-requirements-for-the-avere-vfxt"></a>Jakie są wymagania dotyczące przepustowości dla Avere vFXT?

Ogólną przepustowość wymagana jest zależna od dwa czynniki: 

* Ilość żądana ze źródła danych 
* System klienta tolerancja opóźnienia podczas początkowego ładowania danych  

Wrażliwy na opóźnienia w środowiskach należy używać rozwiązania fiber z szybkość łącza minimalna 1 GB/s. Usługa ExpressRoute, jeśli jest ona dostępna.  

### <a name="can-i-run-the-vfxt-with-public-ip-addresses"></a>Można uruchomić vFXT z publicznych adresów IP?

Nie, vFXT ma działać w środowisku sieciowym, zabezpieczone przy użyciu najlepszych rozwiązań.  

## <a name="technical---backend-storage-core-filers"></a>Zagadnienia techniczne — magazynu zaplecza (filtrach core)

### <a name="how-many-core-filers-does-a-single-avere-vfxt-environment-support"></a>Ile filtrach core obsługuje się w jednym środowisku vFXT Avere?

Klaster vFXT Avere obsługuje do 20 filtrach core. 

### <a name="how-does-the-avere-environment-store-data"></a>Jak w środowisku Avere są przechowywane dane?

Avere vFXT nie jest magazynem. Jest pamięci podręcznej, która odczytuje i zapisuje dane z wielu celów magazynu o nazwie podstawowej filtrach. Dane przechowywane na dyski SSD w warstwie premium Avere vFXT jest przejściowy i ostatecznie jest opróżniany do magazynu filtr rdzeni wewnętrznej bazy danych.

### <a name="which-core-filers-does-avere-vfxt-support"></a>Które filtrach core obsługuje Avere vFXT?

Ogólnie rzecz biorąc vFXT Avere dla platformy Azure obsługuje następujące systemy jak wygląda podstawowe: 

* Firmy Dell EMC Isilon (OneFS 7.1, 7.2, 8.0 i 8.1) 
* NetApp ONTAP (klastra trybu 9.4, 9.3, do 9.2, 9.1P1, 8.0 8.3) i (tryb 7 7.* 8.0 8.3) 
* Kontenery obiektów Blob platformy Azure (tylko LRS) 
* Zasobniki AWS S3 
* Usługa Google Cloud przedziałów

### <a name="why-doesnt-the-avere-vfxt-support-all-nfs-filers"></a>Dlaczego Avere vFXT nie obsługuje wszystkich filtrach systemu plików NFS

Mimo, że wszystkie platformy systemu plików NFS spełniać te same standardy IETF, w praktyce każda implementacja ma swój własny Osobliwości. Te informacje mają wpływ na sposób interakcji Avere vFXT w systemie magazynu. Obsługiwane systemy są najczęściej używanych platform w witrynie marketplace.

### <a name="does-avere-vfxt-support-private-object-storage-such-as-swiftstack"></a>Avere vFXT obsługuje obiektu prywatnego magazynu (na przykład Swiftstack)?

Avere vFXT nie obsługuje obiektu prywatnego magazynu.

### <a name="how-can-i-get-a-specific-storage-product-under-support"></a>Jak uzyskać produkt określonego magazynu w ramach pomocy technicznej?

Obsługa opiera się na ilości żądanie, w tym polu. W przypadku wystarczającej ilości żądania na podstawie przychód w celu obsługi danego rozwiązania NAS jest uważane za. Wysyłanie żądań za pośrednictwem pomocy technicznej platformy Azure.

### <a name="can-i-use-azure-blob-storage-as-a-core-filer"></a>Jako filtr core można używać usługi Azure Blob storage?

Tak, vFXT Avere dla platformy Azure można użyć bloku kontenera obiektów Blob jako filtr core chmury.  

### <a name="what-are-the-storage-account-requirements-for-a-blob-core-filer"></a>Jakie są wymagania dotyczące konta magazynu dla filtr podstawowych obiektów Blob?

Konta magazynu musi być ogólnego przeznaczenia w wersji 2 (GPv2) konto i skonfigurować dla magazynu lokalnie nadmiarowego (LRS) tylko. Magazyn ZRS i GRS są nieobsługiwane.

### <a name="can-i-use-archive-blob-storage"></a>Czy można używać Archive Blob storage?

Nie. Magazyn archiwum — umowa SLA nie jest zgodny z katalogu w czasie rzeczywistym i uzyskiwania dostępu do plików wymaga systemu vFXT. 

### <a name="can-i-use-cool-blob-storage"></a>Czy można używać chłodnego magazynu obiektów Blob?

Możesz korzystać z warstwy chłodna, ale należy pamiętać, że szybkość przeprowadzania operacji będzie znacznie wyższa. 

### <a name="how-do-i-encrypt-the-blob-container"></a>Jak zaszyfrować kontenera obiektów Blob?

Można skonfigurować szyfrowanie obiektów Blob na platformie Azure (preferowany) lub na poziomie vFXT core filtr.  

### <a name="can-i-use-my-own-encryption-key-for-a-blob-core-filer"></a>Do filtr podstawowych obiektów Blob można używać własnego klucza szyfrowania?

Domyślnie dane są szyfrowane za pomocą kluczy zarządzanych firmy Microsoft dla obiektów blob platformy Azure, tabel, plików i kolejek. Można użyć własnego klucza szyfrowania dla plików i obiektów blob platformy Azure. Jeśli zdecydujesz się używać szyfrowania vFXT, należy użyć klucz generowany Avere i zapisz go lokalnie. 

## <a name="purchasing"></a>Zakup

### <a name="how-do-i-get-avere-vfxt-for-azure-licensing"></a>Jak uzyskać Avere vFXT licencjonowania platformy Azure?

Pobieranie vFXT Avere licencja na usługę Azure jest łatwe w portalu Azure Marketplace. Załóż konto platformy Azure, a następnie postępuj zgodnie z instrukcjami wyświetlanymi w [wdrożenie klastra vFXT](avere-vfxt-deploy.md) do utworzenia klastra vFXT Avere. 

### <a name="how-much-does-the-avere-vfxt-cost"></a>Ile kosztuje Avere vFXT?

Na platformie Azure ma bez dodatkowych opłat licencyjnych do korzystania z klastrów vFXT Avere. Klienci są zobowiązani do przechowywania i inne opłaty za użycie platformy Azure.

### <a name="can-avere-vfxt-vms-be-run-as-low-priority"></a>Można uruchomić maszyny wirtualne vFXT Avere jako — niski priorytet?

Nie, "zawsze włączona" wymagają Avere vFXT klastrów usługi. Klastry można wyłączyć, gdy nie jest potrzebny. 

## <a name="next-steps"></a>Kolejne kroki

Aby rozpocząć pracę z Avere vFXT dla platformy Azure, przeczytaj poniższych linków, aby dowiedzieć się, jak planować i wdrażać własny system:

* [Planowanie systemu vFXT Avere](avere-vfxt-deploy-plan.md)
* [Omówienie wdrażania](avere-vfxt-deploy-overview.md)
* [Przygotowanie do tworzenia Avere vFXT](avere-vfxt-prereqs.md)
* [Wdrażanie klastra vFXT](avere-vfxt-deploy.md)

Aby dowiedzieć się więcej o możliwościach Avere vFXT i przypadki użycia, odwiedź stronę [vFXT Avere dla platformy Azure (wersja zapoznawcza)](https://azure.microsoft.com/services/storage/avere-vfxt/).
