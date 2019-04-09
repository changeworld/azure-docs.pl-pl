---
title: Często zadawane pytania — vFXT Avere dla platformy Azure
description: Często zadawane pytania dotyczące Avere vFXT dla platformy Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: v-erkell
ms.openlocfilehash: 69921300163bd9a326f3baedd3182da887ad02c4
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59057153"
---
# <a name="avere-vfxt-for-azure-faq"></a>Avere vFXT for Azure — często zadawane pytania

Ten artykuł zawiera odpowiedzi na pytania, które mogą pomóc w podjęciu decyzji, czy vFXT Avere dla platformy Azure jest odpowiedni dla Twoich potrzeb. Ona zapewnia podstawowe informacje o Avere vFXT i wyjaśniono, jak działa z innymi składnikami platformy Azure i produkty z zewnętrznych dostawców. 

## <a name="general"></a>Ogólne 

### <a name="what-is-avere-vfxt-for-azure"></a>Co to jest system Avere vFXT for Azure?

VFXT Avere dla platformy Azure jest systemem plików o wysokiej wydajności, który buforuje dane usługi active w zasoby obliczeniowe platformy Azure na potrzeby wydajnego przetwarzania obciążeń o znaczeniu krytycznym.

### <a name="is-avere-vfxt-a-storage-solution"></a>To Avere vFXT rozwiązanie do magazynowania?

Nie. Avere vFXT to system plików *pamięci podręcznej* które dołącza do środowiska pamięci masowej, takich jak usługi EMC lub NetApp NAS lub kontenera obiektów blob platformy Azure. Avere vFXT ujednolica dane żądania od klientów, a jego buforuje dane, które służy do zwiększenia wydajności na dużą skalę i wraz z upływem czasu. VFXT Avere, sam nie przechowuje danych. Go nie ma informacji o ilości przechowywanych związanych z nim danych.

### <a name="is-avere-vfxt-a-tiering-solution"></a>To Avere vFXT warstw rozwiązanie?

Avere vFXT nie automatycznie warstwy danych między warstwami gorąca i chłodna.  

### <a name="how-do-i-know-if-an-environment-is-right-for-avere-vfxt"></a>Jak sprawdzić, czy środowisko jest odpowiednia dla Avere vFXT?

Najlepszym sposobem zastanów się, to pytanie jest zadać, "Jest podlega buforowaniu, obciążenie?" Oznacza to, że obciążenie ma wysoki współczynnik odczyt zapis Przykładem jest 80/20 lub 70/30 operacji odczytu/zapisu.

Należy wziąć pod uwagę Avere vFXT dla platformy Azure, jeśli masz opartych na plikach potoku analitycznego, który jest uruchamiany w wielu maszyn wirtualnych platformy Azure i spełnia co najmniej jeden z następujących warunków:

* Ogólna działa wolno lub niespójna z powodu długie czasy dostępu (dziesiątki milisekund lub sekund w zależności od wymagań). Ten czas oczekiwania jest nieodpowiednia dla klienta.

* Dane potrzebne do przetwarzania znajduje się na końcu koniec środowiska sieci WAN, a następnie trwale przenoszenie danych jest niepraktyczne. Dane mogą być w innym regionie platformy Azure lub w centrum danych klienta.

* Znaczna liczba klientów żądania danych — na przykład w klastrze o wysokiej wydajności obliczeń (HPC). Dużą liczbą jednoczesnych żądań może zwiększyć opóźnienie.

* Użytkownik chce przeprowadzić ich bieżącego potoku ", w jakim są" w usłudze Azure virtual machines i wymaga na podstawie POSIX udostępnionego magazynu (lub buforowanie) rozwiązanie w przypadku skalowalności. Za pomocą Avere vFXT dla platformy Azure, nie trzeba Przekształcanie potoku pracy do macierzystych wywołań do usługi Azure Blob storage.

* Aplikacji HPC opiera się na klientach NFSv3. (W niektórych sytuacjach może korzystać klienci SMB 2.1, ale wydajność jest ograniczona).

Poniższy diagram ułatwia odpowiedź na to pytanie. Im bliżej przepływu pracy jest prawym górnym rogu, tym bardziej prawdopodobne jest, że Avere buforowania rozwiązanie jest odpowiednie dla danego środowiska.

![Diagram przedstawiający, że odczycie zadań ładowania z tysięcy klientów są lepsze dopasowane do Avere vFXT](media/avere-vfxt-fit-assessment.png)

### <a name="at-what-scale-of-clients-does-the-avere-vfxt-solution-make-the-most-sense"></a>W jakich skalowania klientów jest Avere rozwiązania vFXT sensu najbardziej?

Rozwiązanie pamięci podręcznej vFXT Avere został opracowany pod kątem obsługi setek, tysięcy lub dziesiątki tysięcy rdzeni obliczeniowych. Jeśli masz kilka komputerów z systemem pracy światła, Avere vFXT nie jest odpowiednim rozwiązaniem.

Typowe klientów vFXT Avere uruchamiania wymagających obciążeń, zaczynając od około 1000 rdzeni procesora CPU. Te środowiska mogą być wynosi 50 000 rdzeni lub więcej. Ponieważ Avere vFXT jest skalowalna, można dodać węzły do obsługi tych obciążeń, wraz z ich potrzebami wymagać więcej przepływności lub więcej operacji We/Wy.

### <a name="how-much-data-can-an-avere-vfxt-environment-store"></a>Jak dużo danych można przechowywać w środowisku vFXT Avere

Avere vFXT jest pamięć podręczna. W szczególności nie to przechowywania danych. Używa kombinacji pamięci RAM i dysków SSD do przechowywania buforowanych danych. Dane są trwale przechowywane w systemie magazynu zaplecza (na przykład system NetApp NAS lub kontenera obiektów blob). Avere vFXT system nie ma informacji o ilości przechowywanych związanych z nim danych. Avere vFXT buforuje tylko podzbiór danych żądaną przez klientów.  

### <a name="what-regions-are-supported"></a>Jakie regiony są obsługiwane?

VFXT Avere dla platformy Azure jest obsługiwane we wszystkich regionach z wyjątkiem regionów suwerenne (China, Niemcy). Upewnij się, że region, którego chcesz użyć może obsługiwać dużą liczbę rdzeni obliczeniowych oraz wystąpień maszyn wirtualnych, potrzebne do utworzenia klastra vFXT Avere.

### <a name="how-do-i-get-help-with-avere-vfxt"></a>Jak uzyskać pomoc dotyczącą Avere vFXT?

Grupy wsparcia wyspecjalizowane oferuje pomoc dotyczącą Avere vFXT dla platformy Azure. Postępuj zgodnie z instrukcjami w [Uzyskaj pomoc dotyczącą systemu](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) otworzyć bilet pomocy technicznej w witrynie Azure portal. 

### <a name="is-avere-vfxt-highly-available"></a>Avere vFXT ma wysoką dostępność?

Tak, Avere vFXT działa wyłącznie jako rozwiązania wysokiej dostępności.

### <a name="does-avere-vfxt-for-azure-also-support-other-cloud-services"></a>VFXT Avere dla platformy Azure również obsługuje inne usługi w chmurze?

Tak, klienci mogą używać więcej niż jednego dostawcę chmury z klastrem vFXT Avere. Obsługuje standardowe przedziały AWS S3, standardowa zasobników Google Cloud Services i kontenerów obiektów blob platformy Azure. 

> [!NOTE] 
> Opłata oprogramowania dotyczą vFXT Avere w usługach AWS i Google Cloud, ale nie z platformą Azure.

## <a name="technical-compute"></a>Technical Preview: Wystąpienia obliczeniowe

### <a name="can-you-describe-what-an-avere-vfxt-environment-looks-like"></a>Może wystarczy opisać jakiego środowiska vFXT Avere "wygląda"?

Avere vFXT to urządzenie klastrowanego z wielu maszyn wirtualnych platformy Azure. Biblioteka języka Python obsługuje tworzenie klastra, usuwanie i modyfikowanie. Odczyt [co to jest vFXT Avere dla platformy Azure?](avere-vfxt-overview.md) Aby dowiedzieć się więcej. 

### <a name="what-kind-of-azure-virtual-machines-does-avere-vfxt-run-on"></a>Jakiego rodzaju maszyn wirtualnych platformy Azure czy Avere vFXT jest uruchomiony?  

VFXT Avere klastra Azure używa maszyn wirtualnych Microsoft Azure E32s_v3. 

<!-- ### Can I mix and match virtual machine types for my cluster?

No, you must choose one virtual machine type or the other.
    
### Can I move between virtual machine types?

Yes, there is a migration path to move from one VM type to the other. [Open a support ticket](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) to learn how.

-->

### <a name="does-the-avere-vfxt-environment-scale"></a>Środowisko vFXT Avere skalowany?

Klaster vFXT Avere może być tak małej, jak trzy węzły maszyny wirtualnej lub wynosi 24 węzłów. Aby uzyskać pomoc dotyczącą planowania, jeśli uważasz, że potrzebujesz więcej niż dziewięciu węzłów klastra, skontaktuj się z działem pomocy technicznej platformy Azure. Większą liczbę węzłów wymaga większej architekturze wdrożenia.

### <a name="does-the-avere-vfxt-environment-autoscale"></a>Czy w środowisku vFXT Avere "automatycznego skalowania"?

Nie. Skalowanie rozmiaru klastra w górę i w dół, ale dodawania lub usuwania węzłów klastra jest to krok wykonywany ręcznie.

### <a name="can-i-run-the-avere-vfxt-cluster-as-a-virtual-machine-scale-set"></a>Można uruchomić klaster vFXT Avere jako zestaw skalowania maszyn wirtualnych?

Avere vFXT nie obsługuje wdrażania zestawu skalowania maszyn wirtualnych. Kilka dostępność wbudowanej obsługi mechanizmów są przeznaczone tylko dla niepodzielnych maszyn wirtualnych z uczestnictwa w klastrze.  

### <a name="can-i-run-the-avere-vfxt-cluster-on-low-priority-vms"></a>Klaster vFXT Avere można uruchomić na maszynach wirtualnych o niskim priorytecie?

Nie, system wymaga podstawowej stabilne zestawy maszyn wirtualnych.

### <a name="can-i-run-the-avere-vfxt-cluster-in-containers"></a>Można uruchomić klaster vFXT Avere w kontenerach?

Nie, Avere vFXT musi zostać wdrożony jako aplikacja niezależne.

### <a name="do-the-avere-vfxt-vms-count-against-my-compute-quota"></a>Czy vFXT Avere liczby maszyn wirtualnych przed Moje przydział usługi compute?

Tak. Upewnij się, że masz wystarczającego limitu przydziału w regionie w celu obsługi klastra.  

### <a name="can-i-run-the-avere-vfxt-cluster-machines-in-different-availability-zones"></a>Można uruchomić Avere vFXT maszyn klastra w różnych strefach dostępności?

Nie. Model o wysokiej dostępności w vFXT Avere aktualnie nie obsługuje poszczególnych Avere vFXT elementów członkowskich klastra znajduje się w różnych strefach dostępności.

### <a name="can-i-clone-avere-vfxt-virtual-machines"></a>Czy można sklonować maszyny wirtualne vFXT Avere?

Nie, musisz podać obsługiwanych skrypt w języku Python do dodawania lub usuwania węzłów w klastrze vFXT Avere. Aby uzyskać więcej informacji, przeczytaj [Zarządzanie klastrem vFXT Avere](avere-vfxt-manage-cluster.md).  

### <a name="is-there-a-vm-version-of-the-software-i-can-run-in-my-own-local-environment"></a>Czy istnieje "Maszyny Wirtualnej" w wersji oprogramowania, które można uruchomić w własnego środowiska lokalnego?

Nie, system jest oferowany jako klastrowane urządzenia i testowane na typach określonej maszyny wirtualnej. To ograniczenie pomaga klientom, o których należy unikać tworzenia systemu, który nie spełnia wymagań o wysokiej wydajności typowego przepływu pracy vFXT Avere. 

## <a name="technical-disks"></a>Technical Preview: Dyski

### <a name="what-types-of-disks-are-supported-for-the-azure-vms"></a>Jakich typów dysków są obsługiwane dla maszyn wirtualnych platformy Azure?

VFXT Avere dla platformy Azure można używać konfiguracji dysków SSD premium 1 TB lub 4 TB. Konfiguracja dysków SSD premium może być wdrożony jako wielu dysków zarządzanych.

### <a name="does-the-cluster-support-unmanaged-disks"></a>Klaster obsługuje dyski niezarządzane?

Nie, klaster wymaga dysków zarządzanych.

### <a name="does-the-system-support-local-attached-ssds"></a>Czy system obsługuje lokalny SSD (dołączone)?

VFXT Avere dla platformy Azure nie obsługuje obecnie lokalne dyski SSD. Dyskach używanych na potrzeby Avere vFXT musi być w stanie zamknięcia i ponownego uruchomienia, ale można zakończyć tylko lokalne dołączone dyski SSD w tej konfiguracji.

### <a name="does-the-system-support-ultra-ssds"></a>Czy system obsługuje ultra SSD?

Nie, system obsługuje tylko konfiguracje dysków SSD — wersja premium.

### <a name="can-i-detach-my-premium-ssds-and-reattach-them-later-to-preserve-cache-contents-between-use"></a>Można odłączyć Moje dyski SSD w warstwie premium i ponownie podłącz je później, aby zachować zawartość pamięci podręcznej między użycia?

Odłączenie i ponowne dołączenie dysków SSD jest obsługiwany. Zawartość metadanych lub pliku źródłowego, być może zmieniono między zastosowań, które mogą spowodować problemy z integralnością danych.

### <a name="does-the-system-encrypt-the-cache"></a>Czy system szyfruje pamięci podręcznej?

Dane są rozkładane na dyskach, ale nie są szyfrowane. Jednak same dyski mogą być szyfrowane. Aby uzyskać więcej informacji, zobacz [bezpiecznego i użyj zasad na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/security-policy#encryption).

## <a name="technical-networking"></a>Technical Preview: Networking

### <a name="what-network-is-recommended"></a>Zaleca się, jakie sieci?

Jeśli używasz magazynu lokalnego przy użyciu Avere vFXT, powinny mieć 1 GB/s lub lepszą połączenia sieciowego. Jeśli masz niewielką ilość danych i chcesz skopiować dane do chmury przed uruchomieniem zadania, połączenie sieci VPN może być wystarczająca. 

> [!TIP] 
> Jest wolniejsze połączenia sieciowego, tym wolniej początkowej odczyty zimnych. Odczyty powolne zwiększyć opóźnienie procesu roboczego. 

### <a name="can-i-run-avere-vfxt-in-a-different-virtual-network-than-my-compute-cluster"></a>Można uruchomić Avere vFXT w innej sieci wirtualnej niż Mój klaster obliczeniowy?

Tak, możesz utworzyć systemu vFXT Avere w innej sieci wirtualnej. Odczyt [Planowanie systemu vFXT Avere](avere-vfxt-deploy-plan.md) Aby uzyskać szczegółowe informacje.

### <a name="does-avere-vfxt-require-its-own-subnet"></a>Avere vFXT wymaga własnej podsieci?

Tak. Avere vFXT działa wyłącznie jako klaster o wysokiej dostępności (HA) i wymaga wielu adresów IP do działania. Jeśli klaster znajduje się we własnej podsieci, można uniknąć ryzyka konflikty adresów IP, co może powodować problemy instalację i normalnych operacji. W podsieci klastra mogą być zawarte w istniejącej sieci wirtualnej, tak długo, jak brak adresu IP adresów nakładają się na siebie.

### <a name="can-i-run-avere-vfxt-on-infiniband"></a>Czy można uruchamiać Avere vFXT InfiniBand?

Nie, Avere vFXT używa Ethernet/adresu IP tylko.

### <a name="how-do-i-access-my-on-premises-nas-environment-from-avere-vfxt"></a>Jak dostęp do mojego lokalnego środowiska NAS z Avere vFXT?

Środowisko vFXT Avere jest jak każdej innej maszyny Wirtualnej platformy Azure, w tym, że wymaga on trasowane dostęp za pośrednictwem bramy sieci lub sieci VPN do klientów centrum danych (i z powrotem). Należy rozważyć użycie połączenia usługi Azure ExpressRoute, jeśli jest dostępna w danym środowisku.

### <a name="what-are-the-bandwidth-requirements-for-avere-vfxt"></a>Jakie są wymagania dotyczące przepustowości dla Avere vFXT?

Ogólną przepustowość wymagana jest zależna od dwa czynniki: 

* Ilość żądana ze źródła danych 
* System klienta tolerancja opóźnienia podczas początkowego ładowania danych  

Wrażliwy na opóźnienia w środowiskach należy używać rozwiązania fiber z szybkość łącza minimalna 1 GB/s. Usługa ExpressRoute, jeśli jest ona dostępna.  

### <a name="can-i-run-avere-vfxt-with-public-ip-addresses"></a>Można uruchomić Avere vFXT z publicznych adresów IP?

Nie, Avere vFXT ma działać w środowisku sieciowym zabezpieczony za pomocą najlepszych rozwiązań.  

### <a name="can-i-restrict-internet-access-from-my-clusters-virtual-network"></a>Czy można ograniczyć dostęp do Internetu, z sieci wirtualnej dla mojego klastra? 

Ogólnie rzecz biorąc można skonfigurować dodatkowe zabezpieczenia w Twojej sieci wirtualnej, zgodnie z potrzebami, ale pewne ograniczenia może zakłócać działanie klastra.

Na przykład ograniczając ruch wychodzący do Internetu z sieci wirtualnej powoduje występowanie problemów dla klastra, chyba że dodasz reguł, które jawnie zezwolić na dostęp do AzureConnectors i AzureCloud. Ta sytuacja jest opisana w [uzupełniające dokumentacji w serwisie GitHub](https://github.com/Azure/Avere/tree/master/src/vfxt/internet_access.md).

Aby uzyskać pomoc dotyczącą zabezpieczeń dostosowane, skontaktuj się z działem pomocy technicznej zgodnie z opisem w [Uzyskaj pomoc dotyczącą systemu](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt).

## <a name="technical-back-end-storage-core-filers"></a>Technical Preview: Magazynu zaplecza (filtrach core)

### <a name="how-many-core-filers-does-a-single-avere-vfxt-environment-support"></a>Ile filtrach core obsługuje się w jednym środowisku vFXT Avere?

Klaster vFXT Avere obsługuje do 20 filtrach core. 

### <a name="how-does-the-avere-vfxt-environment-store-data"></a>Jak w środowisku vFXT Avere są przechowywane dane?

Avere vFXT nie jest magazynem. Jest pamięci podręcznej, która odczytuje i zapisuje dane z wielu celów magazynu o nazwie podstawowej filtrach. Dane przechowywane na dyski SSD w warstwie premium w Avere vFXT jest przejściowy i ostatecznie jest opróżniany do magazynu filtr core zaplecza.

### <a name="which-core-filers-does-avere-vfxt-support"></a>Które filtrach core obsługuje Avere vFXT?

Ogólnie rzecz biorąc vFXT Avere dla platformy Azure obsługuje następujące systemy jak wygląda podstawowe: 

* Firmy Dell EMC Isilon (OneFS 7.1, 7.2, 8.0 i 8.1) 
* NetApp ONTAP (klastra trybu 9.4, 9.3, do 9.2, 9.1P1, 8.0 8.3) i (tryb 7 7.* 8.0 8.3) 

  > [!NOTE] 
  > Usługa Azure Files NetApp aktualnie nie jest obsługiwana. 

* Kontenery obiektów blob platformy Azure (tylko magazyn lokalnie nadmiarowy) 
* Zasobniki AWS S3 
* Usługa Google Cloud przedziałów

### <a name="why-doesnt-avere-vfxt-support-all-nfs-filers"></a>Dlaczego Avere vFXT nie obsługuje wszystkich filtrach systemu plików NFS

Mimo, że wszystkie platformy systemu plików NFS spełniać te same standardy IETF, w praktyce każda implementacja ma swój własny Osobliwości. Te informacje mają wpływ na sposób interakcji Avere vFXT w systemie magazynu. Obsługiwane systemy są najczęściej używanych platform w witrynie marketplace.

### <a name="does-avere-vfxt-support-private-object-storage-such-as-swiftstack"></a>Avere vFXT obsługuje obiektu prywatnego magazynu (na przykład SwiftStack)?

Avere vFXT nie obsługuje obiektu prywatnego magazynu.

### <a name="how-can-i-get-a-specific-storage-product-under-support"></a>Jak uzyskać produkt określonego magazynu w ramach pomocy technicznej?

Obsługa opiera się na ilości żądanie, w tym polu. W przypadku wystarczającej liczby żądań na podstawie przychód pozwalające obsługiwać rozwiązanie NAS teraz przyjrzymy się go. Wysyłanie żądań za pośrednictwem pomocy technicznej platformy Azure.

### <a name="can-i-use-azure-blob-storage-as-a-core-filer"></a>Jako filtr core można używać usługi Azure Blob storage?

Tak, vFXT Avere dla platformy Azure można użyć bloku kontenera obiektów blob jako filtr core chmury.  

### <a name="what-are-the-storage-account-requirements-for-a-blob-core-filer"></a>Jakie są wymagania dotyczące konta magazynu dla filtr podstawowych obiektów blob?

Twoje konto magazynu musi być ogólnego przeznaczenia w wersji 2 (GPv2) konta i skonfigurowana dla tylko magazyn lokalnie nadmiarowy. Magazyn geograficznie nadmiarowy i Magazyn strefowo nadmiarowy nie są obsługiwane.

### <a name="can-i-use-archive-blob-storage"></a>Można użyć usługa archive blob storage?

Nie. Umowy poziomu usług (SLA) w przypadku usługi archive storage nie jest zgodny z katalogu w czasie rzeczywistym i potrzeby dostępu do pliku systemu vFXT Avere. 

### <a name="can-i-use-cool-blob-storage"></a>Czy można używać chłodnego magazynu obiektów blob?

Możesz korzystać z warstwy chłodna, ale należy pamiętać, że szybkość przeprowadzania operacji będzie znacznie wyższa. 

### <a name="how-do-i-encrypt-the-blob-container"></a>Jak zaszyfrować kontenera obiektów blob?

Możesz skonfigurować szyfrowanie obiektów blob na platformie Azure (preferowany) lub na poziomie Avere vFXT core filtr.  

### <a name="can-i-use-my-own-encryption-key-for-a-blob-core-filer"></a>Do filtr podstawowych obiektów blob można używać własnego klucza szyfrowania?

Domyślnie dane są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft dla usługi Azure Blob, tabel i kolejek magazynu, a także usługi Azure Files. Możesz użyć własnego klucza szyfrowania dla magazynu obiektów Blob i plików platformy Azure. Jeśli zdecydujesz się używać Avere vFXT szyfrowania, należy użyć klucz generowany Avere i zapisz go lokalnie. 

## <a name="purchasing"></a>Zakupy

### <a name="how-do-i-get-avere-vfxt-for-azure-licensing"></a>Jak uzyskać Avere vFXT licencjonowania platformy Azure?

Pobieranie vFXT Avere licencja na usługę Azure jest łatwe w portalu Azure Marketplace. Załóż konto platformy Azure, a następnie postępuj zgodnie z instrukcjami wyświetlanymi w [wdrożenie klastra vFXT Avere](avere-vfxt-deploy.md) do utworzenia klastra vFXT Avere. 

### <a name="how-much-does-avere-vfxt-cost"></a>Jaki jest koszt vFXT Avere?

Na platformie Azure ma bez dodatkowych opłat licencyjnych do korzystania z klastrów vFXT Avere. Klienci są zobowiązani do przechowywania i inne opłaty za użycie platformy Azure.

### <a name="can-avere-vfxt-vms-be-run-as-low-priority"></a>Można uruchomić maszyny wirtualne vFXT Avere jako — niski priorytet?

Nie, "zawsze włączona" wymagają Avere vFXT klastrów usługi. Klastry można wyłączyć, gdy nie jest potrzebny. 

## <a name="next-steps"></a>Kolejne kroki

Aby rozpocząć pracę z Avere vFXT dla platformy Azure, przeczytaj następujące artykuły, aby dowiedzieć się, jak planować i wdrażać własny system:

* [Planowanie systemu Avere vFXT](avere-vfxt-deploy-plan.md)
* [Omówienie wdrażania](avere-vfxt-deploy-overview.md)
* [Przygotowywanie do utworzenia klastra vFXT Avere](avere-vfxt-prereqs.md)
* [Wdrażanie klastra vFXT Avere](avere-vfxt-deploy.md)

Aby dowiedzieć się więcej o możliwościach i zastosowań Avere vFXT, odwiedź stronę [vFXT Avere dla platformy Azure](https://azure.microsoft.com/services/storage/avere-vfxt/).
