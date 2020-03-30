---
title: Odzyskiwanie po awarii i przewijanie awaryjne konta magazynu (wersja zapoznawcza)
titleSuffix: Azure Storage
description: Usługa Azure Storage obsługuje pracę awaryjną konta (w wersji zapoznawczej) dla kont magazynu geograficznie nadmiarowego. Dzięki pracy awaryjnej konta można zainicjować proces pracy awaryjnej dla konta magazynu, jeśli podstawowy punkt końcowy stanie się niedostępny.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: f7a8f6d0d3ab3b456c41128da9b689f6b7eda0f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365371"
---
# <a name="disaster-recovery-and-account-failover-preview"></a>Odzyskiwanie po awarii i przewijanie awaryjne konta (wersja zapoznawcza)

Firma Microsoft dokłada wszelkich starań, aby usługi platformy Azure były zawsze dostępne. Mogą jednak wystąpić nieplanowane przerwy w dostawie usług. Jeśli aplikacja wymaga odporności, firma Microsoft zaleca użycie magazynu geograficznie nadmiarowego, aby dane były kopiowane do drugiego regionu. Ponadto klienci powinni mieć plan odzyskiwania po awarii w miejscu do obsługi awarii usługi regionalnej. Ważną częścią planu odzyskiwania po awarii przygotowuje się do awaryjnego do pomocniczego punktu końcowego w przypadku, gdy podstawowy punkt końcowy staje się niedostępny.

Usługa Azure Storage obsługuje pracę awaryjną konta (w wersji zapoznawczej) dla kont magazynu geograficznie nadmiarowego. Dzięki pracy awaryjnej konta można zainicjować proces pracy awaryjnej dla konta magazynu, jeśli podstawowy punkt końcowy stanie się niedostępny. Przewijalnia awaryjna aktualizuje pomocniczy punkt końcowy, aby stał się podstawowym punktem końcowym dla konta magazynu. Po zakończeniu pracy awaryjnej klienci mogą rozpocząć zapisywanie w nowym podstawowym punkcie końcowym.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

W tym artykule opisano pojęcia i proces związany z pracą awaryjną konta i omówiono sposób przygotowania konta magazynu do odzyskiwania z najmniejszą ilością wpływu na klienta. Aby dowiedzieć się, jak zainicjować tryb failover konta w witrynie Azure portal lub programie PowerShell, zobacz [Inicjowanie pracy awaryjnej konta (wersja zapoznawcza)](storage-initiate-account-failover.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="choose-the-right-redundancy-option"></a>Wybierz odpowiednią opcję nadmiarowości

Usługa Azure Storage przechowuje wiele kopii konta magazynu, aby zapewnić trwałość i wysoką dostępność. Opcja nadmiarowości wybrana dla konta zależy od stopnia odporności, którego potrzebujesz. Aby chronić przed regionalnymi awariami, wybierz magazyn geograficznie nadmiarowy, z opcją dostępu do odczytu lub bez opcji z regionu pomocniczego:  

**Magazyn geograficznie nadmiarowy (GRS) lub magazyn geograficzny nadmiarowy (GZRS) (wersja zapoznawcza)** kopiuje dane asynchronicznie w dwóch regionach geograficznych oddalonych od siebie o co najmniej setki mil. Jeśli region podstawowy cierpi na awarię, region pomocniczy służy jako nadmiarowe źródło danych. Można zainicjować pracy awaryjnej, aby przekształcić pomocniczy punkt końcowy do podstawowego punktu końcowego.

**Magazyn geograficzny dostępu do odczytu (RA-GRS) lub magazyn geograficzny dostępu do odczytu (RA-GZRS) (wersja zapoznawcza)** zapewnia magazyn geograficznie nadmiarowy z dodatkową zaletą dostępu do odczytu do pomocniczego punktu końcowego. Jeśli wystąpi awaria w podstawowym punkcie końcowym, aplikacje skonfigurowane dla RA-GRS i zaprojektowane z myślą o wysokiej dostępności mogą nadal odczytywać z pomocniczego punktu końcowego. Firma Microsoft zaleca RA-GRS dla maksymalnej odporności aplikacji.

Aby uzyskać więcej informacji na temat nadmiarowości w usłudze Azure Storage, zobacz [Nadmiarowość usługi Azure Storage](storage-redundancy.md).

> [!WARNING]
> Magazyn geograficznie nadmiarowy niesie ze sobą ryzyko utraty danych. Dane są kopiowane do pomocniczego regionu asynchronicznie, co oznacza, że występuje opóźnienie między kiedy dane zapisywane w regionie podstawowym jest zapisywany w regionie pomocniczym. W przypadku awarii operacje zapisu do podstawowego punktu końcowego, które nie zostały jeszcze skopiowane do pomocniczego punktu końcowego zostaną utracone.

## <a name="design-for-high-availability"></a>Projektowanie na potrzeby wysokiej dostępności

Ważne jest, aby zaprojektować aplikację pod kątem wysokiej dostępności od samego początku. Zapoznaj się z tymi zasobami platformy Azure, aby uzyskać wskazówki dotyczące projektowania aplikacji i planowania odzyskiwania po awarii:

- [Projektowanie aplikacji odpornych na platformę Azure:](/azure/architecture/checklist/resiliency-per-service)omówienie kluczowych koncepcji projektowania aplikacji o wysokiej dostępności na platformie Azure.
- [Lista kontrolna dostępności:](/azure/architecture/checklist/resiliency-per-service)lista kontrolna do sprawdzania, czy aplikacja implementuje najlepsze rozwiązania w zakresie projektowania dotyczące wysokiej dostępności.
- [Projektowanie aplikacji o wysokiej dostępności przy użyciu RA-GRS](storage-designing-ha-apps-with-ragrs.md): Wskazówki projektowe dla aplikacji budowlanych, aby skorzystać z RA-GRS.
- [Samouczek: Tworzenie aplikacji o wysokiej dostępności z magazynu obiektów Blob:](../blobs/storage-create-geo-redundant-storage.md)Samouczek, który pokazuje, jak utworzyć aplikację o wysokiej dostępności, która automatycznie przełącza się między punktami końcowymi, gdy symulowane są błędy i odzyskiwanie. 

Ponadto należy pamiętać o tych najlepszych praktyk dotyczących utrzymania wysokiej dostępności danych usługi Azure Storage:

- **Dyski:** Użyj [usługi Azure Backup,](https://azure.microsoft.com/services/backup/) aby wykonać kopię zapasową dysków maszyn wirtualnych używanych przez maszyny wirtualne platformy Azure. Należy również rozważyć użycie [usługi Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) do ochrony maszyn wirtualnych w przypadku awarii regionalnej.
- **Blokowe obiekty blob:** Włącz [usuwanie nietrwałe,](../blobs/storage-blob-soft-delete.md) aby chronić przed usunięciami i zastąpień na poziomie obiektu, lub skopiuj blokowe obiekty blob do innego konta magazynu w innym regionie przy użyciu [programu AzCopy,](storage-use-azcopy.md) [azure powershell](storage-powershell-guide-full.md)lub [biblioteki przenoszenia danych platformy Azure.](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
- **Pliki:** Użyj [AzCopy](storage-use-azcopy.md) lub [Azure PowerShell,](storage-powershell-guide-full.md) aby skopiować pliki do innego konta magazynu w innym regionie.
- **Tabele:** użyj [AzCopy](storage-use-azcopy.md) do eksportowania danych tabeli do innego konta magazynu w innym regionie.

## <a name="track-outages"></a>Śledzenie przestojów

Klienci mogą subskrybować [pulpit nawigacyjny kondycji usługi Azure,](https://azure.microsoft.com/status/) aby śledzić kondycję i stan usługi Azure Storage i innych usług platformy Azure.

Firma Microsoft zaleca również zaprojektowanie aplikacji w celu przygotowania się na błędy zapisu. Aplikacja powinna uwidaczniać błędy zapisu w sposób, który ostrzega o możliwości awarii w regionie podstawowym.

## <a name="understand-the-account-failover-process"></a>Opis procesu pracy awaryjnej konta

Funkcja pracy awaryjnej konta zarządzanego przez klienta (wersja zapoznawcza) umożliwia niepowodzenie całego konta magazynu w regionie pomocniczym, jeśli podstawowy stanie się niedostępny z jakiegokolwiek powodu. Po wymuszenie pracy awaryjnej do regionu pomocniczego, klienci mogą rozpocząć zapisywanie danych do pomocniczego punktu końcowego po zakończeniu pracy awaryjnej. Przewija się w błąd zwykle trwa około godziny.

### <a name="how-an-account-failover-works"></a>Na czym polega przełączanie konta w tryb failover

W normalnych warunkach klient zapisuje dane na koncie usługi Azure Storage w regionie podstawowym, a te dane są kopiowane asynchronicznie do regionu pomocniczego. Na poniższej ilustracji przedstawiono scenariusz, w którym region podstawowy jest dostępny:

![Klienci zapisują dane na koncie magazynu w regionie podstawowym](media/storage-disaster-recovery-guidance/primary-available.png)

Jeśli podstawowy punkt końcowy staje się niedostępny z jakiegokolwiek powodu, klient nie jest już w stanie zapisać na koncie magazynu. Na poniższej ilustracji przedstawiono scenariusz, w którym podstawowy stał się niedostępny, ale nie doszło jeszcze do odzyskania:

![Podstawowy jest niedostępny, więc klienci nie mogą zapisywać danych](media/storage-disaster-recovery-guidance/primary-unavailable-before-failover.png)

Klient inicjuje od pracy awaryjnej konta do pomocniczego punktu końcowego. Proces pracy awaryjnej aktualizuje wpis DNS dostarczony przez usługę Azure Storage, dzięki czemu pomocniczy punkt końcowy staje się nowym podstawowym punktem końcowym dla konta magazynu, jak pokazano na poniższej ilustracji:

![Klient inicjuje przekonywowanie konta do pomocniczego punktu końcowego](media/storage-disaster-recovery-guidance/failover-to-secondary.png)

Dostęp do zapisu jest przywracany dla kont GRS i RA-GRS po zaktualizowaniu wpisu DNS i przekierowaniu żądań do nowego podstawowego punktu końcowego. Istniejące punkty końcowe usługi magazynowania dla obiektów blob, tabel, kolejek i plików pozostają takie same po przemijaniu awaryjnym.

> [!IMPORTANT]
> Po zakończeniu pracy awaryjnej konto magazynu jest skonfigurowane jako lokalnie nadmiarowe w nowym podstawowym punkcie końcowym. Aby wznowić replikację do nowej pomocniczej, skonfiguruj konto tak, aby ponownie używało magazynu geograficznie nadmiarowego (RA-GRS lub GRS).
>
> Należy pamiętać, że konwersja konta LRS na RA-GRS lub GRS wiąże się z kosztem. Ten koszt ma zastosowanie do aktualizowania konta magazynu w nowym regionie podstawowym, aby używać RA-GRS lub GRS po przemijeniu awaryjnym.  

### <a name="anticipate-data-loss"></a>Przewidywanie utraty danych

> [!CAUTION]
> Przewijanie awaryjne konta zwykle wiąże się z utratą danych. Ważne jest, aby zrozumieć konsekwencje inicjowania pracy awaryjnej konta.  

Ponieważ dane są zapisywane asynchronicznie z regionu podstawowego do regionu pomocniczego, zawsze występuje opóźnienie przed zapisem w regionie podstawowym jest kopiowany do regionu pomocniczego. Jeśli region podstawowy stanie się niedostępny, najnowsze zapisy mogą nie zostać jeszcze skopiowane do regionu pomocniczego.

Po wymuszenie pracy awaryjnej, wszystkie dane w regionie podstawowym zostaną utracone, ponieważ region pomocniczy staje się nowym regionem podstawowym, a konto magazynu jest skonfigurowane jako lokalnie zbędne. Wszystkie dane już skopiowane do pomocniczego jest zachowywany, gdy nastąpi przewijenie awaryjne. Jednak wszystkie dane zapisane do podstawowego, który nie został również skopiowany do pomocniczego jest tracona trwale.

**Właściwość Czas ostatniej synchronizacji** wskazuje ostatni czas, w której dane z regionu podstawowego są gwarantowane, że zostały zapisane w regionie pomocniczym. Wszystkie dane zapisane przed ostatnim czasem synchronizacji są dostępne w drugorzędnym, podczas gdy dane zapisane po ostatnim czasie synchronizacji mogą nie zostać zapisane w pomocniczym i mogą zostać utracone. Użyj tej właściwości w przypadku awarii, aby oszacować kwotę utraty danych, które mogą wystąpić w wyniku zainicjowania pracy awaryjnej konta.

Najlepszym rozwiązaniem jest zaprojektowanie aplikacji, aby można było użyć czasu ostatniej synchronizacji do oceny oczekiwanej utraty danych. Na przykład jeśli rejestrujesz wszystkie operacje zapisu, następnie można porównać czas ostatnich operacji zapisu do ostatniego czasu synchronizacji, aby ustalić, które zapisy nie zostały zsynchronizowane z pomocniczym.

### <a name="use-caution-when-failing-back-to-the-original-primary"></a>Należy zachować ostrożność w przypadku powrotu do pierwotnego

Po przejściu w stan fail over z regionu podstawowego do pomocniczego, konto magazynu jest skonfigurowany jako lokalnie nadmiarowe w nowym regionie podstawowym. Konto można ponownie skonfigurować pod kątem nadmiarowości geograficznej, aktualizując je w celu użycia grs lub RA-GRS. Gdy konto jest ponownie skonfigurowane do nadmiarowości geograficznej po przełączeniu w błąd, nowy region podstawowy natychmiast rozpoczyna kopiowanie danych do nowego regionu pomocniczego, który był podstawowym przed pierwotnym przejściem w stan failover. Jednak może upłynąć trochę czasu, zanim istniejące dane w podstawowym jest w pełni skopiowany do nowego pomocniczego.

Po ponownym skonfigurowaniu konta magazynu pod kątem nadmiarowości geograficznej możliwe jest zainicjowanie innej pracy awaryjnej z nowego podstawowego z powrotem do nowej pomocniczej. W takim przypadku oryginalny region podstawowy przed przewijaniem awaryjnym staje się ponownie regionem podstawowym i jest skonfigurowany jako lokalnie nadmiarowy. Wszystkie dane w regionie podstawowym po przegubie awaryjnym (oryginalny pomocniczy) są następnie tracone. Jeśli większość danych na koncie magazynu nie została skopiowana do nowej pomocniczej przed powrotem po awarii, może dopłynąć poważna utrata danych.

Aby uniknąć utraty danych głównych, sprawdź wartość **last sync time** właściwości przed niepowodzeniem z powrotem. Porównaj czas ostatniej synchronizacji z ostatnimi czasami, w których dane zostały zapisane w nowej podstawowej, aby ocenić oczekiwaną utratę danych. 

## <a name="initiate-an-account-failover"></a>Inicjowanie trybu failover konta

Możesz zainicjować przebłaję konta z witryny Azure portal, PowerShell, Interfejsu wiersza polecenia platformy Azure lub interfejsu API dostawcy zasobów usługi Azure Storage. Aby uzyskać więcej informacji na temat inicjowania pracy awaryjnej, zobacz [Inicjowanie pracy awaryjnej konta (wersja zapoznawcza)](storage-initiate-account-failover.md).

## <a name="about-the-preview"></a>Informacje o podglądzie

Funkcja pracy awaryjnej konta jest dostępna w wersji zapoznawczej dla wszystkich klientów korzystających z grs lub RA-GRS z wdrożeniami usługi Azure Resource Manager. Obsługiwane są typy kont magazynu ogólnego przeznaczenia w wersji 1, ogólnego przeznaczenia w wersji 2 i magazynu obiektów Blob. Przewijenie awaryjne konta jest obecnie dostępne we wszystkich regionach publicznych. Opcja pracy awaryjnej konta nie jest obecnie dostępna w chmurach suwerennych/krajowych.

Wersja zapoznawcza jest przeznaczona tylko do użytku nieprodukcyjnego. Umowy o poziomie usług produkcyjnych (SLA) nie są obecnie dostępne.

### <a name="additional-considerations"></a>Dodatkowe zagadnienia

Przejrzyj dodatkowe zagadnienia opisane w tej sekcji, aby dowiedzieć się, jak mogą mieć wpływ na aplikacje i usługi po wymuszenie pracy awaryjnej w okresie podglądu.

#### <a name="storage-account-containing-archived-blobs"></a>Konto magazynu zawierające zarchiwizowane obiekty blob

Konta magazynu zawierające zarchiwizowane obiekty blob obsługują konto w trybie failover. Po zakończeniu pracy awaryjnej, aby przekonwertować konto z powrotem do GRS lub RA-GRS wszystkie zarchiwizowane obiekty blob muszą być najpierw nawodnione do warstwy online.

#### <a name="storage-resource-provider"></a>Dostawca zasobów magazynu

Po zakończeniu pracy awaryjnej klienci mogą ponownie odczytywać i zapisywać dane usługi Azure Storage w nowym regionie podstawowym. Jednak dostawca zasobów usługi Azure Storage nie działa w miejscu fail over, więc operacje zarządzania zasobami nadal muszą odbywać się w regionie podstawowym. Jeśli region podstawowy jest niedostępny, nie będzie można wykonywać operacji zarządzania na koncie magazynu.

Ponieważ dostawca zasobów usługi Azure Storage nie działa w miejscu failover, [Location](/dotnet/api/microsoft.azure.management.storage.models.trackedresource.location) właściwość zwróci oryginalną lokalizację podstawową po zakończeniu pracy awaryjnej.

#### <a name="azure-virtual-machines"></a>Maszyny wirtualne platformy Azure

Maszyny wirtualne platformy Azure (maszyny wirtualne) nie działają w trybie failover w ramach pracy awaryjnej konta. Jeśli region podstawowy staje się niedostępny, a po awarii do regionu pomocniczego, należy ponownie utworzyć wszystkie maszyny wirtualne po przełączeniu wznawienie w przypadku pracy awaryjnej. Ponadto istnieje potencjalna utrata danych skojarzona z przewijanym w błąd kontem. Firma Microsoft zaleca następujące [wskazówki](../../virtual-machines/windows/manage-availability.md) dotyczące wysokiej dostępności i [odzyskiwania po awarii](../../virtual-machines/virtual-machines-disaster-recovery-guidance.md) specyficzne dla maszyn wirtualnych na platformie Azure.

#### <a name="azure-unmanaged-disks"></a>Dyski niezarządzane platformy Azure

Najlepszym rozwiązaniem jest zalecana konwersja dysków niezarządzanych na dyski zarządzane. Jednak jeśli trzeba wykonać w stan failover konto, które zawiera dyski niezarządzane dołączone do maszyn wirtualnych platformy Azure, należy zamknąć maszynę wirtualną przed zainicjowaniem pracy awaryjnej.

Dyski niezarządzane są przechowywane jako obiekty blob stron w usłudze Azure Storage. Gdy maszyna wirtualna jest uruchomiona na platformie Azure, wszystkie dyski niezarządzane dołączone do maszyny Wirtualnej są dzierżawione. Nie można kontynuować pracy awaryjnej konta, gdy istnieje dzierżawa obiektu blob. Aby wykonać przemieścić w pełnienia pracy awaryjnej, wykonaj następujące kroki:

1. Przed rozpoczęciem należy zwrócić uwagę na nazwy dysków niezarządzanych, ich numery jednostek logicznych (LUN) i maszynę wirtualną, do której są dołączone. Dzięki temu ułatwi ponowne podłączenie dysków po przekroczeniu trybu failover.
2. Zamknij maszynę wirtualną.
3. Usuń maszynę wirtualną, ale zachowaj pliki VHD dla dysków niezarządzanych. Zanotuj czas, w którym usunięto maszynę wirtualną.
4. Poczekaj, aż **czas ostatniej synchronizacji** został zaktualizowany i jest późniejszy niż czas, w którym usunięto maszynę wirtualną. Ten krok jest ważne, ponieważ jeśli pomocniczy punkt końcowy nie został w pełni zaktualizowany z plików VHD, gdy nastąpi przepełnienie awaryjne, maszyna wirtualna może nie działać poprawnie w nowym regionie podstawowym.
5. Inicjowanie pracy awaryjnej konta.
6. Poczekaj, aż przewijanie awaryjne konta zostanie ukończone, a region pomocniczy stanie się nowym regionem podstawowym.
7. Utwórz maszynę wirtualną w nowym regionie podstawowym i ponownie dołącz veds.
8. Uruchom nową maszynę wirtualną.

Należy pamiętać, że wszystkie dane przechowywane na dysku tymczasowym zostaną utracone po zamknięciu maszyny Wirtualnej.

### <a name="unsupported-features-and-services"></a>Nieobsługiwały funkcje i usługi

Następujące funkcje i usługi nie są obsługiwane w przypadku pracy awaryjnej konta w wersji zapoznawczej:

- Usługa Azure File Sync nie obsługuje pracy awaryjnej konta magazynu. Kont magazynu zawierających udziały plików platformy Azure używane jako punkty końcowe w chmurze w usłudze Azure File Sync nie należy przełączać w tryb failover. Wykonanie tej operacji spowoduje, że synchronizacja przestanie działać, a także może spowodować nieoczekiwaną utratę danych w przypadku nowych plików warstwowych.
- Konta magazynu ADLS Gen2 (konta z włączoną hierarchiczną przestrzenią nazw) nie są obecnie obsługiwane.
- Nie można pozycyjnie konta magazynu zawierającego obiekty BLOB bloku premium. Konta magazynu obsługujące obiekty blob bloku premium nie obsługują obecnie nadmiarowości geograficznej.
- Nie można zakończyć niepowodzeniem konta magazynu zawierającego wszystkie kontenery obsługujące [zasady niezmienności WORM.](../blobs/storage-blob-immutable-storage.md) Odblokowane/zablokowane zasady przechowywania w oparciu o czas lub zasady blokady prawnej zapobiegają pracy awaryjnej w celu zachowania zgodności.
- Po zakończeniu pracy awaryjnej następujące funkcje mogą przestać działać, jeśli pierwotnie włączono: [subskrypcje zdarzeń,](../blobs/storage-blob-event-overview.md) [zmień kanał informacyjny,](../blobs/storage-blob-change-feed.md) [zasady cyklu życia](../blobs/storage-lifecycle-management-concepts.md)i [rejestrowanie analizy magazynu.](storage-analytics-logging.md)

## <a name="copying-data-as-an-alternative-to-failover"></a>Kopiowanie danych jako alternatywy dla pracy awaryjnej

Jeśli konto magazynu jest skonfigurowany dla RA-GRS, a następnie masz dostęp do odczytu do danych przy użyciu pomocniczego punktu końcowego. Jeśli wolisz nie awaryjnie w przypadku awarii w regionie podstawowym, można użyć narzędzi, takich jak [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md)lub [biblioteki przenoszenia danych platformy Azure,](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) aby skopiować dane z konta magazynu w regionie pomocniczym do innego konta magazynu w regionie, którego nie zmieniono. Następnie można skierować aplikacje do tego konta magazynu dla dostępności odczytu i zapisu.

> [!CAUTION]
> Praca awaryjna konta nie powinna być używana jako część strategii migracji danych.


## <a name="microsoft-managed-failover"></a>Przewija się w pełni w stanie failover zarządzanym przez firmę Microsoft

W ekstremalnych okolicznościach, gdy region jest tracony z powodu poważnej awarii, firma Microsoft może zainicjować regionalną usługę failover. W takim przypadku nie jest wymagane żadne działanie z Twojej strony. Dopóki usługa trybu failover zarządzanego przez firmę Microsoft nie zostanie ukończona, nie będziesz mieć dostępu do zapisu do konta magazynu. Aplikacje można odczytać z regionu pomocniczego, jeśli konto magazynu jest skonfigurowany dla RA-GRS. 

## <a name="see-also"></a>Zobacz też

- [Inicjowanie pracy awaryjnej konta (wersja zapoznawcza)](storage-initiate-account-failover.md)
- [Projektowanie wysoko dostępnych aplikacji przy użyciu magazynu RA-GRS](storage-designing-ha-apps-with-ragrs.md)
- [Samouczek: Tworzenie aplikacji o wysokiej dostępności z pamięcią blob](../blobs/storage-create-geo-redundant-storage.md) 
