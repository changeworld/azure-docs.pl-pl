---
title: Często zadawane pytania — Avere vFXT for Azure
description: Często zadawane pytania dotyczące usługi Avere vFXT for Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 186b2c048a9de42318e4af287393d731a4eb16f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153466"
---
# <a name="avere-vfxt-for-azure-faq"></a>Avere vFXT for Azure — często zadawane pytania

W tym artykule znajdziesz odpowiedzi na pytania, które mogą pomóc w podjęciu decyzji, czy Avere vFXT for Azure jest odpowiedni dla Twoich potrzeb. Zawiera podstawowe informacje o Avere vFXT i wyjaśnia, jak działa z innymi składnikami platformy Azure i z produktami od dostawców zewnętrznych.

## <a name="general"></a>Ogólne

### <a name="what-is-avere-vfxt-for-azure"></a>Co to jest system Avere vFXT for Azure?

Avere vFXT for Azure to wysokowydajny system plików, który buforuje aktywne dane w obliczeniach platformy Azure w celu wydajnego przetwarzania krytycznych obciążeń.

### <a name="is-avere-vfxt-a-storage-solution"></a>Czy Avere vFXT jest rozwiązaniem pamięci masowej?

Nie. Avere vFXT for Azure to *pamięć podręczna* systemu plików, która jest dołączana do środowisk magazynu, takich jak EMC lub NetApp NAS lub kontener obiektów blob platformy Azure. Avere vFXT usprawnia żądania danych od klientów i buforuje dane, które służy do zwiększenia wydajności na dużą skalę i w czasie. Avere vFXT sam nie przechowuje danych. Nie ma informacji o ilości danych przechowywanych za nim.

### <a name="is-avere-vfxt-a-tiering-solution"></a>Czy Avere vFXT jest rozwiązaniem warstwowym?

Avere vFXT for Azure nie warstwy danych między warstwami hot i cool.  

### <a name="how-do-i-know-if-an-environment-is-right-for-avere-vfxt"></a>Skąd mam wiedzieć, czy środowisko jest odpowiednie dla Avere vFXT?

Najlepszym sposobem, aby myśleć o tym pytaniu jest pytanie, "Czy obciążenie jest buforowane?" Oznacza to, że obciążenie ma wysoki współczynnik odczytu do zapisu? Przykładem jest 80/20 lub 70/30 odczytów/zapisów.

Należy wziąć pod uwagę Avere vFXT dla platformy Azure, jeśli masz potok analityczny oparty na plikach, który działa na dużej liczbie maszyn wirtualnych platformy Azure i spełnia co najmniej jeden z następujących warunków:

* Ogólna wydajność jest niska lub niespójna ze względu na długi czas dostępu do pliku (dziesiątki milisekund lub sekundy, w zależności od wymagań). To opóźnienie jest nie do przyjęcia dla klienta.

* Dane wymagane do przetworzenia znajdują się na końcu środowiska WAN, a przeniesienie tych danych na stałe jest niepraktyczne. Dane mogą znajdować się w innym regionie platformy Azure lub w centrum danych klienta.

* Znaczna liczba klientów żąda danych — na przykład w klastrze obliczeniowym o wysokiej wydajności (HPC). Duża liczba równoczesnych żądań może zwiększyć opóźnienie.

* Klient chce uruchomić ich bieżącego potoku "tak jak jest" na maszynach wirtualnych platformy Azure i potrzebuje rozwiązania udostępnionego magazynu (lub buforowania) opartego na posix do skalowalności. Korzystając z Avere vFXT for Azure, nie trzeba ponownie przeprojektować potoku pracy, aby natywnych wywołań usługi Azure Blob storage.

* Aplikacja HPC jest oparta na klientach NFSv3. (W niektórych okolicznościach można używać klientów SMB 2.1, ale wydajność jest ograniczona.)

Poniższy diagram może pomóc odpowiedzieć na to pytanie. Im bliżej przepływu pracy znajduje się w prawym górnym rogu, tym bardziej prawdopodobne jest, że rozwiązanie do buforowania Avere vFXT for Azure jest odpowiednie dla twojego środowiska.

![Wykres przedstawiający, że obciążenia odczytu z tysiącami klientów są lepiej dostosowane do Avere vFXT](media/avere-vfxt-fit-assessment.png)

### <a name="at-what-scale-of-clients-does-the-avere-vfxt-solution-make-the-most-sense"></a>Na jaką skalę klientów ma największy sens rozwiązanie Avere vFXT?

Rozwiązanie pamięci podręcznej Avere vFXT jest zbudowane z myślą o obsłudze setek, tysięcy lub dziesiątków tysięcy rdzeni obliczeniowych. Jeśli masz kilka maszyn pracy światła, Avere vFXT nie jest właściwym rozwiązaniem.

Typowi klienci Avere vFXT uruchamiają wymagające obciążenia, zaczynając od około 1000 rdzeni procesora. Te środowiska mogą mieć rozmiar nawet 50 000 rdzeni lub więcej. Ponieważ Avere vFXT jest skalowalny, można dodać węzły do obsługi tych obciążeń, ponieważ rosną one wymagać większej przepływności lub więcej We/Wy.

### <a name="how-much-data-can-an-avere-vfxt-environment-store"></a>Ile danych może przechowywać środowisko VFXT Avere?

Avere vFXT for Azure to pamięć podręczna. Nie przechowuje danych. Używa kombinacji pamięci RAM i SSD do przechowywania danych w pamięci podręcznej. Dane są trwale przechowywane w systemie pamięci masowej zaplecza (na przykład w systemie NetApp NAS lub kontenerze obiektów blob). System Avere vFXT nie zawiera informacji o ilości przechowywanych za nim danych. Avere vFXT buforuje tylko podzbiór tych danych, których żądają klienci.  

### <a name="what-regions-are-supported"></a>Jakie regiony są obsługiwane?

Avere vFXT for Azure jest obsługiwany we wszystkich regionach z wyjątkiem regionów suwerennych (Chiny, Niemcy). Upewnij się, że region, którego chcesz użyć, może obsługiwać dużą liczbę rdzeni obliczeniowych i wystąpienia maszyn wirtualnych potrzebne do utworzenia klastra VFXT Avere.

### <a name="how-do-i-get-help-with-avere-vfxt"></a>Jak uzyskać pomoc dotyczącą avere vFXT?

Wyspecjalizowana grupa pracowników pomocy technicznej oferuje pomoc dotyczącą usługi Avere vFXT for Azure. Postępuj zgodnie z instrukcjami w [Pobierz pomoc z systemem,](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) aby otworzyć bilet pomocy technicznej z witryny Azure portal.

### <a name="is-avere-vfxt-highly-available"></a>Czy Avere vFXT jest wysoce dostępne?

Tak, Avere vFXT działa wyłącznie jako rozwiązanie wysokiej sieci.

### <a name="does-avere-vfxt-for-azure-also-support-other-cloud-services"></a>Czy Avere vFXT for Azure obsługuje również inne usługi w chmurze?

Tak, klienci mogą używać więcej niż jednego dostawcy chmury z klastrem Avere vFXT. Obsługuje standardowe zasobniki AWS S3, standardowe zasobniki usług Google Cloud Services i kontenery obiektów blob platformy Azure.

> [!NOTE]
> Opłata za oprogramowanie dotyczy korzystania z Avere vFXT z AWS lub Google Cloud storage. Nie ma żadnych dodatkowych opłat za oprogramowanie za korzystanie z magazynu obiektów blob platformy Azure.

## <a name="technical-compute"></a>Techniczne: Obliczeń

### <a name="can-you-describe-what-an-avere-vfxt-environment-looks-like"></a>Czy możesz opisać, jak "wygląda środowisko VFXT Avere"?

Avere vFXT to urządzenie klastrowane wykonane z wielu maszyn wirtualnych platformy Azure. Biblioteka języka Python obsługuje tworzenie, usuwanie i modyfikowanie klastra. Przeczytaj [Co to jest Avere vFXT for Azure?](avere-vfxt-overview.md)

### <a name="what-kind-of-azure-virtual-machines-does-avere-vfxt-run-on"></a>Na jakich maszynach wirtualnych platformy Azure działa usługa Avere vFXT?  

Klaster Avere vFXT for Azure używa platformy Microsoft Azure E32s_v3 maszyn wirtualnych.

<!-- ### Can I mix and match virtual machine types for my cluster?

No, you must choose one virtual machine type or the other.
    
### Can I move between virtual machine types?

Yes, there is a migration path to move from one VM type to the other. [Open a support ticket](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) to learn how.
-->

### <a name="does-the-avere-vfxt-environment-scale"></a>Czy środowisko Avere vFXT jest skalowane?

Klaster Avere vFXT może mieć rozmiary zaledwie trzech węzłów maszyny wirtualnej lub 24 węzłów. Skontaktuj się z pomocą techniczną platformy Azure, aby uzyskać pomoc dotyczącą planowania, jeśli uważasz, że potrzebujesz klastra z więcej niż dziewięcioma węzłami. Większa liczba węzłów wymaga większej architektury wdrażania.

### <a name="does-the-avere-vfxt-environment-autoscale"></a>Czy środowisko Avere vFXT jest "skalowanie automatyczne"?

Nie. Rozmiar klastra można skalować w górę i w dół, ale dodawanie lub usuwanie węzłów klastra jest krokiem ręcznym.

### <a name="can-i-run-the-avere-vfxt-cluster-as-a-virtual-machine-scale-set"></a>Czy mogę uruchomić klaster VFXT Avere jako zestaw skalowania maszyny wirtualnej?

Avere vFXT nie obsługuje wdrażania zestawu skalowania maszyny wirtualnej. Kilka wbudowanych mechanizmów obsługi dostępności są przeznaczone tylko dla atomowych maszyn wirtualnych uczestniczących w klastrze.  

### <a name="can-i-run-the-avere-vfxt-cluster-on-low-priority-vms"></a>Czy mogę uruchomić klaster Avere vFXT na maszynach wirtualnych o niskim priorytecie?

Nie, system wymaga podstawowego stabilnego zestawu maszyn wirtualnych.

### <a name="can-i-run-the-avere-vfxt-cluster-in-containers"></a>Czy mogę uruchomić klaster Avere vFXT w kontenerach?

Nie, Avere vFXT musi być wdrożony jako niezależna aplikacja.

### <a name="do-the-avere-vfxt-vms-count-against-my-compute-quota"></a>Czy maszyny wirtualne Avere vFXT są wliczane do mojego przydziału obliczeniowego?

Tak. Upewnij się, że masz wystarczającą ilość przydziału w regionie do obsługi klastra.  

### <a name="can-i-run-the-avere-vfxt-cluster-machines-in-different-availability-zones"></a>Czy można uruchomić maszyny klastrowe Avere vFXT w różnych strefach dostępności?

Nie. Model wysokiej dostępności w Avere vFXT obecnie nie obsługuje poszczególnych członków klastra Avere vFXT znajdujących się w różnych strefach dostępności.

### <a name="can-i-clone-avere-vfxt-virtual-machines"></a>Czy mogę sklonować maszyny wirtualne Avere vFXT?

Nie, aby dodać lub usunąć węzły w klastrze Avere vFXT, należy użyć obsługiwanego skryptu języka Python. Aby uzyskać więcej informacji, zobacz [Zarządzanie klastrem VFXT Avere](avere-vfxt-manage-cluster.md).

### <a name="is-there-a-vm-version-of-the-software-i-can-run-in-my-own-local-environment"></a>Czy istnieje wersja "VM" oprogramowania, które mogę uruchomić we własnym środowisku lokalnym?

Nie, system jest oferowany jako urządzenie klastrowane i testowany na określonych typach maszyn wirtualnych. To ograniczenie pomaga klientom uniknąć tworzenia systemu, który nie może obsługiwać wymagań o wysokiej wydajności typowego przepływu pracy Avere vFXT.

## <a name="technical-disks"></a>Techniczne: Dyski

### <a name="what-types-of-disks-are-supported-for-the-azure-vms"></a>Jakie typy dysków są obsługiwane dla maszyn wirtualnych platformy Azure?

Avere vFXT for Azure może używać konfiguracji dysków SSD w wersji premium o pojemności 1 TB lub 4 TB. Konfigurację dysków SSD w wersji premium można wdrożyć jako wiele dysków zarządzanych.

### <a name="does-the-cluster-support-unmanaged-disks"></a>Czy klaster obsługuje dyski niezarządzane?

Nie, klaster wymaga dysków zarządzanych.

### <a name="does-the-system-support-local-attached-ssds"></a>Czy system obsługuje lokalne (podłączone) ssdy?

Avere vFXT for Azure obecnie nie obsługuje lokalnych identyfikatorów SSD. Dyski używane dla Avere vFXT muszą być w stanie zamknąć i ponownie uruchomić, ale lokalne dołączone dyski SSD w tej konfiguracji można zakończyć tylko.

### <a name="does-the-system-support-ultra-ssds"></a>Czy system obsługuje ultra SSD?

Nie, system obsługuje tylko konfiguracje najwyższej jakości SSD.

### <a name="can-i-detach-my-premium-ssds-and-reattach-them-later-to-preserve-cache-contents-between-use"></a>Czy mogę odłączyć moje najwyższej jakości ssd i ponownie dołączyć je później, aby zachować zawartość pamięci podręcznej między użyciem?

Odłączanie i ponowne dołączanie ssd jest nieobsługiwanie. Metadane lub zawartość pliku w źródle mogły ulec zmianie między zastosowaniami, co może powodować problemy z integralnością danych.

### <a name="does-the-system-encrypt-the-cache"></a>Czy system szyfruje pamięć podręczną?

Dane są rozłożone na dyski, ale nie są szyfrowane. Jednak same dyski mogą być szyfrowane. Aby uzyskać więcej informacji, zobacz [Bezpieczne i używaj zasad na maszynach wirtualnych na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/security-policy#encryption).

## <a name="technical-networking"></a>Techniczne: Tworzenie sieci kontaktów

### <a name="what-network-is-recommended"></a>Jaka sieć jest zalecana?

Jeśli używasz magazynu lokalnego z Avere vFXT, powinieneś mieć 1 Gb/s lub lepsze połączenie sieciowe między magazynem a klastrem. Jeśli masz niewielką ilość danych i chcesz skopiować dane do chmury przed uruchomieniem zadań, łączność sieci VPN może być wystarczająca.

> [!TIP]
> Im wolniejsze jest łącze sieciowe, tym wolniej będzie początkowa "zimna" odczyty. Powolne odczyty zwiększają opóźnienie potoku pracy.

### <a name="can-i-run-avere-vfxt-in-a-different-virtual-network-than-my-compute-cluster"></a>Czy mogę uruchomić avere vFXT w innej sieci wirtualnej niż mój klaster obliczeniowy?

Tak, system VFXT Avere można utworzyć w innej sieci wirtualnej. Przeczytaj plan [systemu Avere vFXT,](avere-vfxt-deploy-plan.md) aby uzyskać szczegółowe informacje.

### <a name="does-avere-vfxt-require-its-own-subnet"></a>Czy Avere vFXT wymaga własnej podsieci?

Tak. Avere vFXT działa ściśle jako klaster wysokiej dostępności (HA) i wymaga wielu adresów IP do działania. Jeśli klaster znajduje się we własnej podsieci, można uniknąć ryzyka konfliktów adresów IP, które mogą powodować problemy z instalacją i normalną obsługą. Podsieć klastra może znajdować się w sieci wirtualnej używanej przez inne zasoby, o ile nie nakładają się żadne adresy IP.

### <a name="can-i-run-avere-vfxt-on-infiniband"></a>Czy mogę uruchomić Avere vFXT na InfiniBand?

Nie, Avere vFXT używa tylko Ethernet/IP.

### <a name="how-do-i-access-my-on-premises-nas-environment-from-avere-vfxt"></a>Jak uzyskać dostęp do lokalnego środowiska NAS firmy Avere vFXT?

Środowisko Avere vFXT jest jak każda inna maszyna wirtualna platformy Azure, ponieważ wymaga przekierowanego dostępu za pośrednictwem bramy sieciowej lub sieci VPN do centrum danych klienta (i z powrotem). Należy rozważyć użycie usługi Azure ExpressRoute łączności, jeśli jest ona dostępna w twoim środowisku.

### <a name="what-are-the-bandwidth-requirements-for-avere-vfxt"></a>Jakie są wymagania dotyczące przepustowości dla Avere vFXT?

Ogólne zapotrzebowanie na przepustowość zależy od dwóch czynników:

* Ilość danych wymaganych od źródła
* Tolerancja opóźnienia systemu klienckiego podczas początkowego ładowania danych  

W środowiskach wrażliwych na opóźnienia należy użyć rozwiązania światłowodowego o minimalnej prędkości łącza 1 Gb/s. Użyj usługi ExpressRoute, jeśli jest dostępna.  

### <a name="can-i-run-avere-vfxt-with-public-ip-addresses"></a>Czy mogę uruchomić avere vFXT z publicznymi adresami IP?

Nie, Avere vFXT ma być obsługiwany w środowisku sieciowym zabezpieczonym za pomocą najlepszych rozwiązań.

### <a name="can-i-restrict-internet-access-from-my-clusters-virtual-network"></a>Czy mogę ograniczyć dostęp do Internetu z sieci wirtualnej mojego klastra?

Ogólnie rzecz biorąc można skonfigurować dodatkowe zabezpieczenia w sieci wirtualnej w razie potrzeby, ale niektóre ograniczenia mogą zakłócać działanie klastra.

Na przykład ograniczenie wychodzącego dostępu do Internetu z sieci wirtualnej powoduje problemy dla klastra, chyba że można również dodać regułę, która jawnie zezwala na dostęp do usługi AzureCloud. Sytuacja ta jest opisana w [uzupełniającej dokumentacji na GitHub](https://github.com/Azure/Avere/tree/master/src/vfxt/internet_access.md).

Aby uzyskać pomoc dotyczącą niestandardowych zabezpieczeń, skontaktuj się z pomocą techniczną zgodnie z opisem w [temacie Uzyskaj pomoc dotyczącą systemu](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt).

## <a name="technical-back-end-storage-core-filers"></a>Techniczne: Pamięć masowa zaplecza (podstawowe filers)

### <a name="how-many-core-filers-does-a-single-avere-vfxt-environment-support"></a>Ile podstawowych filerów obsługuje jedno środowisko VFXT Avere?

Klaster VFXT Avere obsługuje do 20 podstawowych filerów.

### <a name="how-does-the-avere-vfxt-environment-store-data"></a>W jaki sposób środowisko VFXT Avere przechowuje dane?

Avere vFXT nie jest magazynem. Jest to pamięć podręczna, która odczytuje i zapisuje dane z wielu obiektów docelowych magazynu o nazwie core filers. Dane przechowywane na dyskach SSD premium w Avere vFXT są przejściowe i ostatecznie są opróżniane do magazynu back-end core filer.

### <a name="which-core-filers-does-avere-vfxt-support"></a>Które podstawowe filers obsługuje Avere vFXT?

Ogólnie rzecz biorąc Avere vFXT for Azure obsługuje następujące systemy jako podstawowe filers:

* Firma Dell EMC Isilon (OneFS 7.1, 7.2, 8.0 i 8.1) 
* NetApp ONTAP (tryb klastrowany 9.4, 9.3, 9.2, 9.1P1, 8.0-8.3) i (7-Mode 7.*, 8.0-8.3)

* Kontenery obiektów blob platformy Azure (tylko magazyn dostępny z nadmiarem lokalnie)
* Łyżki AWS S3
* Wiadra Google Cloud

### <a name="why-doesnt-avere-vfxt-support-all-nfs-filers"></a>Dlaczego Avere vFXT nie obsługuje wszystkich filerów NFS?

Chociaż wszystkie platformy NFS spełniają te same standardy IETF, w praktyce każda implementacja ma swoje własne dziwactwa. Te szczegóły wpływają na sposób interakcji avere vFXT z systemem pamięci masowej. Obsługiwane systemy są najczęściej używanymi platformami na rynku.

### <a name="does-avere-vfxt-support-private-object-storage-such-as-swiftstack"></a>Czy Avere vFXT obsługuje magazyn obiektów prywatnych (na przykład SwiftStack)?

Avere vFXT nie obsługuje magazynu obiektów prywatnych.

### <a name="how-can-i-get-a-specific-storage-product-under-support"></a>Jak mogę uzyskać konkretny produkt pamięci masowej pod pomocą pomocy technicznej?

Wsparcie jest oparte na wysokości zapotrzebowania w terenie. Jeśli istnieje wystarczająca liczba żądań opartych na przychodach do obsługi rozwiązania NAS, rozważymy to. Składaj żądania za pośrednictwem pomocy technicznej platformy Azure.

### <a name="can-i-use-azure-blob-storage-as-a-core-filer"></a>Czy mogę używać magazynu obiektów Blob platformy Azure jako głównego filera?

Tak, Avere vFXT for Azure może używać kontenera obiektów blob bloku jako filera rdzenia chmury.

### <a name="what-are-the-storage-account-requirements-for-a-blob-core-filer"></a>Jakie są wymagania dotyczące konta magazynu dla filera rdzenia obiektu blob?

Konto magazynu musi być kontem ogólnego przeznaczenia w wersji 2 (GPv2) i skonfigurowane tylko dla magazynu nadmiarowego lokalnie. Magazyn geograficznie nadmiarowy i magazyn strefowy nie są obsługiwane.

Przeczytaj [narzędzie Azure Blob Storage core filer,](avere-vfxt-add-storage.md#azure-blob-storage-cloud-core-filer) aby uzyskać więcej informacji na temat wymagań dotyczących konta magazynu.

### <a name="can-i-use-archive-blob-storage"></a>Czy mogę używać magazynu obiektów blob archiwum?

Nie. Umowa spółce SLA dotyczącej poziomu usług w zakresie przechowywania archiwów nie jest zgodna z potrzebami w zakresie dostępu do katalogów i plików w czasie rzeczywistym systemu Avere vFXT.

### <a name="can-i-use-cool-blob-storage"></a>Czy mogę używać fajnego magazynu obiektów blob?

Chłodny magazyn obiektów blob warstwy zwykle nie jest zalecane dla Avere vFXT dla narzędzia azure core filer. Warstwa chłodna oferuje niższe koszty magazynowania, ale wyższe koszty operacyjne. (Aby uzyskać więcej informacji, zobacz [Cennik obiektów blob](<https://azure.microsoft.com/pricing/details/storage/blobs/>) blokuj). Jeśli dane będą często dostępne, często modyfikowane lub usuwane, należy rozważyć użycie warstwy Hot.

[Warstwy dostępu](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers#cool-access-tier) zawiera więcej informacji o tym, kiedy może to mieć sens, aby użyć chłodnej warstwy magazynu jako pliku rdzenia vFXT.

### <a name="how-do-i-encrypt-the-blob-container"></a>Jak zaszyfrować kontener obiektów blob?

Szyfrowanie obiektów blob można skonfigurować na platformie Azure (preferowane) lub na poziomie core filer Avere vFXT.  

### <a name="can-i-use-my-own-encryption-key-for-a-blob-core-filer"></a>Czy mogę używać własnego klucza szyfrowania dla filera rdzenia obiektu blob?

Domyślnie dane są szyfrowane za pomocą kluczy zarządzanych przez firmę Microsoft dla usługi Azure Blob, Tabela i kolejka magazynu oraz usługi Azure Files. Możesz przynieść własny klucz do szyfrowania magazynu obiektów Blob i usługi Azure Files. Jeśli zdecydujesz się użyć szyfrowania Avere vFXT, musisz użyć klucza wygenerowanego przez Avere i przechowywać go lokalnie.

## <a name="purchasing"></a>Zakupy

### <a name="how-do-i-get-avere-vfxt-for-azure-licensing"></a>Jak uzyskać licencję Avere vFXT dla platformy Azure?

Uzyskanie licencji Avere vFXT dla platformy Azure jest łatwe za pośrednictwem portalu Azure Marketplace. Zarejestruj się w celu utworzenia konta platformy Azure, a następnie postępuj zgodnie z instrukcjami w [obszarze Wdrażanie klastra VFXT Avere,](avere-vfxt-deploy.md) aby utworzyć klaster VFXT Avere.

### <a name="how-much-does-avere-vfxt-cost"></a>Ile kosztuje Avere vFXT?

Na platformie Azure nie ma dodatkowej opłaty licencyjnej za korzystanie z klastrów VFXT Avere. Klienci są odpowiedzialni za opłaty za przechowywanie i inne opłaty za zużycie platformy Azure.

### <a name="can-avere-vfxt-vms-be-run-as-low-priority"></a>Czy maszyny wirtualne VFXT Avere mogą być uruchamiane jako o niskim priorytecie?

Nie, klastry VFXT Avere wymagają usługi "zawsze włączone". Klastry można wyłączyć, gdy nie są potrzebne.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć korzystanie z avere vFXT for Azure, przeczytaj te artykuły, aby dowiedzieć się, jak zaplanować i wdrożyć własny system:

* [Planowanie systemu Avere vFXT](avere-vfxt-deploy-plan.md)
* [Omówienie wdrażania](avere-vfxt-deploy-overview.md)
* [Przygotowanie do utworzenia klastra VFXT Avere](avere-vfxt-prereqs.md)
* [Wdrażanie klastra Avere vFXT](avere-vfxt-deploy.md)

Aby dowiedzieć się więcej o możliwościach i przypadkach użycia avere vFXT, odwiedź [Avere vFXT for Azure](https://azure.microsoft.com/services/storage/avere-vfxt/).
