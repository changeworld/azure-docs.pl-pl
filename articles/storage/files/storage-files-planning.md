---
title: Planowanie wdrożenia Azure Files | Microsoft Docs
description: Dowiedz się, co należy wziąć pod uwagę podczas planowania wdrożenia Azure Files.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 163c3cbaeed1526f08d047c3bf357232ccb534a3
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802393"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planowanie wdrażania usługi Pliki Azure

[Usługa Azure Files](storage-files-introduction.md) oferuje w pełni zarządzane udziały plików w chmurze, które są dostępne za pośrednictwem standardowego protokołu SMB. Ponieważ Azure Files jest w pełni zarządzany, wdrażanie go w scenariuszach produkcyjnych jest znacznie łatwiejsze niż Wdrażanie serwera plików lub urządzenia NAS oraz zarządzanie nim. Ten artykuł dotyczy tematów, które należy wziąć pod uwagę podczas wdrażania udziału plików platformy Azure do użytku produkcyjnego w organizacji.

## <a name="management-concepts"></a>Pojęcia związane z zarządzaniem

 Na poniższym diagramie przedstawiono konstrukcje zarządzania Azure Files:

![Struktura plików](./media/storage-files-introduction/files-concepts.png)

* **Konto magazynu**: Cały dostęp do usługi Azure Storage odbywa się za pomocą konta magazynu. Aby uzyskać szczegółowe informacje na temat pojemności konta magazynu, zobacz [Cele dotyczące skalowalności i wydajności](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* **Udział**: Udział File Storage to udział plików SMB na platformie Azure. Wszystkie pliki i katalogi muszą być tworzone w udziale nadrzędnym. Konto może zawierać nieograniczoną liczbę udziałów, a udział może przechowywać nieograniczoną liczbę plików, do całkowitej pojemności udziału plików. W przypadku standardowych udziałów plików całkowita pojemność jest równa 5 TiB (GA) lub 100 TiB (wersja zapoznawcza) dla udziałów plików w warstwie Premium, łączna pojemność to 100 TiB.

* **Katalog**: Opcjonalna hierarchia katalogów.

* **Plik**: Plik w udziale. Plik może mieć maksymalnie 1 TiB rozmiar.

* **Format adresu URL**: W przypadku żądań skierowanych do udziału plików platformy Azure przy użyciu protokołu REST plików można używać adresów przy użyciu następującego formatu adresu URL:

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/<file>
    ```

## <a name="data-access-method"></a>Metoda dostępu do danych

Azure Files oferuje dwie wbudowane, wygodne metody dostępu do danych, których można używać oddzielnie lub w połączeniu ze sobą, aby uzyskać dostęp do danych:

1. **Bezpośredni dostęp do chmury**: Każdy udział plików platformy Azure można zainstalować w [systemie Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md)i/lub [Linux](storage-how-to-use-files-linux.md) przy użyciu standardowego protokołu bloku komunikatów serwera (SMB) lub za pośrednictwem interfejsu API REST plików. W przypadku protokołu SMB operacje odczytu i zapisu do plików w udziale są wprowadzane bezpośrednio w udziale plików na platformie Azure. Aby można było zainstalować program przy użyciu maszyny wirtualnej na platformie Azure, klient SMB w systemie operacyjnym musi obsługiwać co najmniej protokół SMB 2,1. Aby zainstalować lokalnie, na przykład na stacji roboczej użytkownika, klient SMB obsługiwany przez stację roboczą musi obsługiwać co najmniej protokół SMB 3,0 (z szyfrowaniem). Oprócz protokołu SMB nowe aplikacje lub usługi mogą bezpośrednio uzyskiwać dostęp do udziału plików za pośrednictwem usługi File REST, co zapewnia łatwy i skalowalny interfejs programowania aplikacji na potrzeby tworzenia oprogramowania.
2. **Azure File Sync**: Dzięki Azure File Sync udziały mogą być replikowane do serwerów z systemem Windows lokalnie lub na platformie Azure. Użytkownicy będą mieli dostęp do udziału plików za pomocą systemu Windows Server, na przykład za pomocą udziału SMB lub NFS. Jest to przydatne w scenariuszach, w których dane będą dostępne i modyfikowane daleko od centrum danych platformy Azure, na przykład w scenariuszu oddziału. Dane mogą być replikowane między wieloma punktami końcowymi systemu Windows Server, takimi jak między wieloma biurami oddziałów. Na koniec dane mogą być warstwami Azure Files, w taki sposób, że wszystkie dane są nadal dostępne za pośrednictwem serwera, ale serwer nie ma pełnej kopii danych. Zamiast tego dane są bezproblemowo wywoływane po otwarciu przez użytkownika.

W poniższej tabeli przedstawiono sposób, w jaki użytkownicy i aplikacje mogą uzyskiwać dostęp do udziału plików platformy Azure:

| | Bezpośredni dostęp do chmury | Azure File Sync |
|------------------------|------------|-----------------|
| Jakich protokołów należy używać? | Azure Files obsługuje protokół SMB 2,1, protokół SMB 3,0 i interfejs API REST plików. | Uzyskaj dostęp do udziału plików platformy Azure za pomocą dowolnego obsługiwanego protokołu w systemie Windows Server (SMB, NFS, FTPS itp.) |  
| Gdzie działa Twoje obciążenie? | **Na platformie Azure**: Azure Files oferuje bezpośredni dostęp do danych. | **W środowisku lokalnym z powolnej sieci**: Klienci z systemami Windows, Linux i macOS mogą instalować lokalny udział plików systemu Windows jako szybką pamięć podręczną udziału plików platformy Azure. |
| Jakiego poziomu listy kontroli dostępu potrzebujesz? | Poziom udziału i pliku. | Udostępnianie, plik i poziom użytkownika. |

## <a name="data-security"></a>Bezpieczeństwo danych

Azure Files zawiera kilka wbudowanych opcji zapewniających bezpieczeństwo danych:

* Obsługa szyfrowania w ramach protokołów over-Wire: Szyfrowanie SMB 3,0 i plik REST za pośrednictwem protokołu HTTPS. Domyślnie: 
    * Klienci, którzy obsługują szyfrowanie SMB 3,0, wysyłają i odbierają dane za pośrednictwem zaszyfrowanego kanału.
    * Klienci nieobsługujący protokołu SMB 3,0 z szyfrowaniem mogą komunikować się wewnątrz centrum danych za pośrednictwem protokołu SMB 2,1 lub SMB 3,0 bez szyfrowania. Klienci protokołu SMB nie mogą komunikować się między centrami danych za pośrednictwem protokołu SMB 2,1 ani protokołu SMB 3,0 bez szyfrowania.
    * Klienci mogą komunikować się za pośrednictwem pliku HTTP lub HTTPS.
* Szyfrowanie w spoczynku ([Azure szyfrowanie usługi Storage](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)): Szyfrowanie usługi Storage (SSE) jest włączona dla wszystkich kont magazynu. Dane przechowywane w usłudze REST są szyfrowane za pomocą w pełni zarządzanych kluczy. Szyfrowanie w czasie spoczynku nie zwiększa kosztów magazynu ani nie zmniejsza wydajności. 
* Opcjonalne wymaganie szyfrowanych danych podczas przesyłania: w przypadku wybrania Azure Files odrzuca dostęp do danych za pośrednictwem nieszyfrowanych kanałów. Dozwolone są tylko protokołu HTTPS i SMB 3,0 z połączeniami szyfrowania.

    > [!Important]  
    > Wymaganie bezpiecznego transferu danych spowoduje, że starsze klientów SMB nie będą mogły komunikować się z protokołem SMB 3,0 z szyfrowaniem, aby nie powiodło się. Aby uzyskać więcej informacji, zobacz [Instalowanie systemu Windows](storage-how-to-use-files-windows.md), [Instalowanie w systemie Linux](storage-how-to-use-files-linux.md)i [Instalowanie w systemie macOS](storage-how-to-use-files-mac.md).

W celu zapewnienia maksymalnego bezpieczeństwa zdecydowanie zalecamy włączenie szyfrowania i włączenie szyfrowania danych w czasie, gdy korzystasz z nowoczesnych klientów do uzyskiwania dostępu do danych. Na przykład jeśli trzeba zainstalować udział na maszynie wirtualnej z systemem Windows Server 2008 R2, która obsługuje tylko protokół SMB 2,1, należy zezwolić na nieszyfrowany ruch do konta magazynu, ponieważ protokół SMB 2,1 nie obsługuje szyfrowania.

Jeśli używasz Azure File Sync, aby uzyskać dostęp do udziału plików platformy Azure, zawsze będziemy używać protokołów HTTPS i SMB 3,0 z szyfrowaniem w celu synchronizowania danych z serwerami z systemem Windows, bez względu na to, czy wymagane jest szyfrowanie danych.

## <a name="file-share-performance-tiers"></a>Warstwy wydajności udziału plików

Azure Files oferuje dwie warstwy wydajności: standardowa i Premium.

### <a name="standard-file-shares"></a>Standardowe udziały plików

Standardowe udziały plików są obsługiwane przez dyski twarde (HDD). Standardowe udziały plików zapewniają niezawodną wydajność dla obciążeń we/wy, które są mniej wrażliwe na zmienności wydajności, takie jak udziały plików ogólnego przeznaczenia i środowiska deweloperskie/testowe. Standardowe udziały plików są dostępne tylko w modelu rozliczania z płatnością zgodnie z rzeczywistym użyciem.

Standardowe udziały plików o rozmiarze do 5 TiB są dostępne jako oferta GA. W przypadku większych udziałów plików, które są udziałami większymi niż 5 TiB, do maksymalnie 100 TiB, są obecnie dostępne jako oferta w wersji zapoznawczej.

> [!IMPORTANT]
> Zapoznaj się z sekcją dołączanie [do większej liczby udziałów plików (warstwa standardowa)](#onboard-to-larger-file-shares-standard-tier) , aby zapoznać się z krokami do dołączenia, a także zakresem i ograniczeniami w wersji zapoznawczej.

### <a name="premium-file-shares"></a>Udziały plików w warstwie Premium

Udziały plików w warstwie Premium są obsługiwane przez dyski półprzewodnikowe (dysków SSD). Udziały plików w warstwie Premium zapewniają spójną wysoką wydajność i małe opóźnienia w obrębie jednocyfrowych milisekund dla większości operacji we/wy dla obciążeń intensywnie korzystających z operacji we/wy. Dzięki temu są one odpowiednie dla różnorodnych obciążeń, takich jak bazy danych, hosting witryn sieci Web i środowiska deweloperskie. Udziały plików w warstwie Premium są dostępne tylko w modelu rozliczania z obsługą administracyjną. Udziały plików w warstwie Premium korzystają z modelu wdrażania niezależnego od standardowych udziałów plików.

Azure Backup jest dostępny dla udziałów plików w warstwie Premium, a usługa Azure Kubernetes obsługuje udziały plików w warstwie Premium w wersji 1,13 i nowszych.

Jeśli chcesz dowiedzieć się, jak utworzyć udział plików w warstwie Premium, zobacz nasz artykuł w temacie: [Jak utworzyć konto magazynu plików platformy Azure w warstwie Premium](storage-how-to-create-premium-fileshare.md).

Obecnie nie można bezpośrednio konwertować między standardowym udziałem plików a udziałem plików w warstwie Premium. Jeśli chcesz przełączyć się do jednej z warstw, musisz utworzyć nowy udział plików w tej warstwie i ręcznie skopiować dane z oryginalnego udziału do utworzonego nowego udziału. Można to zrobić przy użyciu dowolnego Azure Files obsługiwanych narzędzi do kopiowania, takich jak Robocopy lub AzCopy.

> [!IMPORTANT]
> Udziały plików w warstwie Premium są dostępne tylko w programie LRS i są dostępne w większości regionów, które oferują konta magazynu. Aby dowiedzieć się, czy w Twoim regionie są obecnie dostępne udziały plików w warstwie Premium, zobacz stronę [dostępne według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=storage) na platformie Azure.

#### <a name="provisioned-shares"></a>Udostępnione udziały

Udziały plików w warstwie Premium są udostępniane na podstawie stałego współczynnika GiB/IOPS/przepływności. Dla każdego zainicjowanej GiB udział zostanie wystawiony przez wiele operacji we/wy i 0,1 MiB/s do maksymalnej liczby limitów na udział. Minimalną dozwoloną alokacją jest 100 GiB z minimalnymi operacjami IOPS/przepływności.

Na podstawie najlepszego wysiłku wszystkie udziały mogą wykonać maksymalnie trzy liczby operacji we/wy na sekundę GiB magazynu, 60 w zależności od rozmiaru udziału. Nowe udziały zaczynają pełne środki na korzystanie z systemu na podstawie zainicjowanej pojemności.

Udziały muszą być udostępniane w 1 przyrostach GiB. Minimalny rozmiar to 100 GiB, następny rozmiar to 101 GiB i tak dalej.

> [!TIP]
> Liczba operacji we/wy dla linii bazowej = 1 * zainicjowana GiB. (Maksymalnie 100 000 operacji we/wy).
>
> Limit serii = 3 * Liczba operacji wejścia/wyjścia dla linii bazowej. (Maksymalnie 100 000 operacji we/wy).
>
> szybkość ruchu wychodzącego = 60 MiB/s + 0,06 * zainicjowana GiB
>
> transfer danych przychodzących = 40 MiB/s + 0,04 * aprowizacji GiB

Udostępniony rozmiar udziału jest określany przez przydział udziału. Przydział udziału można zwiększyć w dowolnym momencie, ale może zostać zmniejszony dopiero po 24 godzinach od momentu ostatniego wzrostu. Po odczekaniu przez 24 godziny bez zwiększenia limitu przydziału można zmniejszyć przydział udziału dowolną liczbę razy, aż do momentu jego zwiększenia. Zmiany skali operacji we/wy na sekundę będą obowiązywać w ciągu kilku minut od zmiany rozmiaru.

Istnieje możliwość zmniejszenia rozmiaru udziału przystosowanego poniżej użytych GiB. Jeśli to zrobisz, nie utracisz danych, ale nadal będą naliczane opłaty za używany rozmiar i zostanie uzyskana wydajność (liczba operacji wejścia/wyjścia na sekundę, przepływność i liczba IOPS dla operacji wejścia/wyjścia na sekundę) udziału, który nie jest używany.

W poniższej tabeli przedstawiono kilka przykładów tych wzorów dla rozmiarów udostępnianych udziałów:

|Pojemność (GiB) | Liczba operacji we/wy na sekundę punktu odniesienia | Operacje we/wy na sekundę | Ruch wychodzący (MiB/s) | Ruch przychodzący (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | Do 300     | 66   | 44   |
|500         | 500     | Do 1 500   | 90   | 60   |
|1,024       | 1,024   | Do 3 072   | 122   | 81   |
|5 120       | 5 120   | Do 15 360  | 368   | 245   |
|10 240      | 10 240  | Do 30 720  | 675 | 450   |
|33 792      | 33 792  | Do 100 000 | 2 088 | 1 392   |
|51 200      | 51 200  | Do 100 000 | 3,132 | 2 088   |
|102 400     | 100,000 | Do 100 000 | 6 204 | 4 136   |

> [!NOTE]
> Wydajność udziałów plików zależy od ograniczeń sieci maszynowych, dostępnej przepustowości sieci, rozmiarów we/wy, równoległości, między wieloma innymi czynnikami. Aby osiągnąć maksymalną skalę wydajności, należy rozłożyć obciążenie na wiele maszyn wirtualnych. Zapoznaj się [z przewodnikiem rozwiązywania problemów](storage-troubleshooting-files-performance.md) w przypadku niektórych typowych problemów z wydajnością i obejść.

#### <a name="bursting"></a>Rozszerzanie możliwości

Udziały plików w warstwie Premium mogą zwiększać liczbę operacji we/wy na sekundę. Rozliczanie jest zautomatyzowane i działa na podstawie systemu kredytowego. Przeszukiwanie odbywa się na podstawie najlepszego nakładu pracy, a limit graniczny nie jest gwarancją, udziały plików mogą przekroczyć limit.

Kredyty są gromadzone w zasobniku, gdy ruch dla udziału plików jest poniżej liczby operacji wejścia/wyjścia na sekundę. Na przykład udział GiB 100 ma 100 liczby operacji wejścia/wyjścia na sekundę. Jeśli rzeczywisty ruch w udziale był 40 operacji we/wy dla określonego interwału 1 sekund, wówczas liczba nieużywanych operacji 60 we/wy na sekundę jest księgowana w zasobniku serii. Te kredyty zostaną następnie użyte później, gdy operacje przekroczą liczbę IOPs linii bazowej.

> [!TIP]
> Rozmiar zasobnika szeregowego = liczba IOPS linii bazowej * 2 * 3600.

Za każdym razem, gdy udział przekracza liczbę operacji we/wy na sekundę i ma kredyty w zasobniku szeregowym, spowoduje to utworzenie serii. Udziały mogą w dalszym ciągu przekroczyć czas trwania obciążeń, chociaż udziały mniejsze niż 50 TiB będą przechowywane tylko przy maksymalnej szybkości przez maksymalnie godzinę. Udziały większe niż 50 TiB mogą technicznie przekroczyć ten limit godzin, do dwóch godzin, ale jest to oparte na liczbie naliczanych kredytów na korzystanie z serii. Poszczególne operacje we/wy poza linią bazową zużywają jedno środki, a po wykorzystaniu wszystkich środków udział zwróci wartość do operacji wejścia/wyjścia na sekundę.

Kredyty na udostępnianie mają trzy stany:

- Naliczanie, gdy udział plików używa mniejszej niż liczba operacji we/wy na sekundę.
- Odrzucanie, gdy udział plików jest rozruchowy.
- Pozostała stała, gdy nie ma żadnych kredytów lub operacji wejścia/wyjścia linii bazowej.

Nowe udziały plików zaczynają się od pełnej liczby kredytów w swoim zasobniku. Środki na korzystanie z serii nie zostaną naliczone, jeśli liczba operacji we/wy udziałów spadnie poniżej liczby operacji wejścia/wyjścia na sekundę z powodu ograniczenia przez serwer.

## <a name="file-share-redundancy"></a>Nadmiarowość udziałów plików

Azure Files udziały standardowe obsługuje trzy opcje nadmiarowości danych: Magazyn lokalnie nadmiarowy (LRS), magazyn strefowo nadmiarowy (ZRS), magazyn Geograficznie nadmiarowy (GRS) i magazyn Geograficznie nadmiarowy (GZRS) (wersja zapoznawcza).

Azure Files udziały w warstwie Premium obsługują tylko Magazyn lokalnie nadmiarowy (LRS).

W poniższych sekcjach opisano różnice między różnymi opcjami nadmiarowości:

### <a name="locally-redundant-storage"></a>Magazyn lokalnie nadmiarowy

[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="zone-redundant-storage"></a>Magazyn strefowo nadmiarowy

[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="geo-redundant-storage"></a>Magazyn geograficznie nadmiarowy

> [!Warning]  
> Jeśli używasz udziału plików platformy Azure jako punktu końcowego w chmurze na koncie magazynu GRS, nie należy inicjować trybu failover dla konta magazynu. Wykonanie tej operacji spowoduje, że synchronizacja przestanie działać, a także może spowodować nieoczekiwaną utratę danych w przypadku nowych plików warstwowych. W przypadku utraty regionu platformy Azure firma Microsoft będzie wyzwalać tryb failover na koncie magazynu w sposób zgodny z Azure File Sync.

Magazyn Geograficznie nadmiarowy (GRS) został zaprojektowany z założenia, że co najmniej 99.99999999999999% (16 9) trwałości obiektów w danym roku przez replikowanie danych do regionu pomocniczego, który znajduje się w setki kilometrów od regionu podstawowego. Jeśli konto magazynu ma włączone GRS, dane są trwałe nawet w przypadku pełnej awarii regionalnej lub awarii, w której region podstawowy nie jest możliwy do odzyskania.

W przypadku wybrania opcji magazyn Geograficznie nadmiarowy do odczytu (RA-GRS) należy wiedzieć, że usługa Azure File nie obsługuje magazynu geograficznie nadmiarowego dostępnego do odczytu (RA-GRS) w dowolnym regionie. Udziały plików na koncie magazynu RA-GRS działają tak samo jak w przypadku kont GRS i są obciążane cenami GRS.

GRS replikuje dane do innego centrum danych w regionie pomocniczym, ale dane te są dostępne tylko do odczytu, jeśli firma Microsoft zainicjuje tryb failover z regionu podstawowego do pomocniczego.

W przypadku konta magazynu z włączonym GRS wszystkie dane są najpierw replikowane przy użyciu magazynu lokalnie nadmiarowego (LRS). Aktualizacja jest najpierw zatwierdzana do lokalizacji podstawowej i replikowana przy użyciu LRS. Aktualizacja jest następnie replikowana asynchronicznie do regionu pomocniczego za pomocą GRS. Gdy dane są zapisywane w lokalizacji dodatkowej, są również replikowane w tej lokalizacji przy użyciu LRS.

Zarówno region podstawowy, jak i pomocniczy zarządzają replikami w osobnych domenach błędów i uaktualniania domen w ramach jednostki skalowania magazynu. Jednostka skali magazynu jest podstawową jednostką replikacji w centrum danych. Replikacja na tym poziomie jest zapewniana przez LRS; Aby uzyskać więcej informacji, [zobacz Magazyn lokalnie nadmiarowy (LRS): Low-cost data redundancy for Azure Storage](../common/storage-redundancy-lrs.md) (Magazyn lokalnie nadmiarowy: nadmiarowość danych o niskich kosztach dla usługi Azure Storage).

Przed wybraniem opcji replikacji należy pamiętać o następujących kwestiach:

* Magazyn strefy Geograficznie nadmiarowy (GZRS) (wersja zapoznawcza) zapewnia wysoką dostępność wraz z maksymalną trwałością przez replikację danych synchronicznie w trzech strefach dostępności platformy Azure, a następnie replikowanie danych asynchronicznie do regionu pomocniczego. Możesz również włączyć dostęp do odczytu do regionu pomocniczego. GZRS zaprojektowano w celu udostępnienia co najmniej 99.99999999999999% (16 9) trwałości obiektów w danym roku. Aby uzyskać więcej informacji na temat GZRS, zobacz [Geograficznie nadmiarowy magazyn w celu zapewnienia wysokiej dostępności i maksymalnej trwałości (wersja zapoznawcza)](../common/storage-redundancy-gzrs.md).
* Magazyn strefowo nadmiarowy (ZRS) zapewnia wysoką dostępność z replikacją synchroniczną i może być lepszym wyborem w przypadku niektórych scenariuszy niż GRS. Aby uzyskać więcej informacji na temat ZRS, zobacz [ZRS](../common/storage-redundancy-zrs.md).
* Replikacja asynchroniczna polega na opóźnieniu od momentu zapisania danych w regionie podstawowym, gdy zostanie ono zreplikowane do regionu pomocniczego. W przypadku awarii regionalnej zmiany, które nie zostały jeszcze zreplikowane do regionu pomocniczego, mogą zostać utracone, jeśli nie można odzyskać tych danych z regionu podstawowego.
* W programie GRS replika nie jest dostępna do odczytu lub zapisu, chyba że firma Microsoft zainicjuje przejście w tryb failover do regionu pomocniczego. W przypadku przejścia w tryb failover będziesz mieć dostęp do odczytu i zapisu do tych danych po zakończeniu pracy w trybie failover. Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące odzyskiwania po awarii](../common/storage-disaster-recovery-guidance.md).

## <a name="onboard-to-larger-file-shares-standard-tier"></a>Dołączanie do większych udziałów plików (warstwa standardowa)

Ta sekcja ma zastosowanie tylko do standardowych udziałów plików. Wszystkie udziały plików w warstwie Premium są dostępne z 100 TiB jako oferty w całości.

### <a name="restrictions"></a>Ograniczenia

- [Warunki](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczej platformy Azure dotyczą dużych udziałów plików w wersji zapoznawczej, takich jak użycie z wdrożeniami Azure File Sync.
- Wymaga utworzenia nowego konta magazynu ogólnego zastosowania (nie można rozwinąć istniejących kont magazynu).
- Konwersja konta LRS/ZRS na GRS/GZRS nie będzie możliwa na żadnym nowym koncie magazynu utworzonym po zaakceptowaniu subskrypcji do większej wersji zapoznawczej udziałów plików.


### <a name="regional-availability"></a>Dostępność regionalna

Standardowe udziały plików są dostępne we wszystkich regionach do 5 TiB. W niektórych regionach jest dostępny z limitem 100 TiB, te regiony są wymienione w poniższej tabeli:

|Region |Obsługiwana nadmiarowość |Obsługuje istniejące konta magazynu |Obsługa portalu * |
|-------|---------|---------|---------|
|Australia Wschodnia |LRS     |Nie    |Tak|
|Australia Południowo-Wschodnia|LRS     |Nie    |Jeszcze nie|
|Indie Środkowe  |LRS     |Nie    |Jeszcze nie|
|Francja Środkowa  |LRS, ZRS|Nie    |LRS — tak, ZRS — jeszcze nie|
|Indie Południowe    |LRS     |Nie    |Jeszcze nie|
|Azja Południowo-Wschodnia |LRS, ZRS|Nie    |Tak|
|Środkowo-zachodnie stany USA|LRS     |Nie    |Jeszcze nie|
|Europa Zachodnia    |LRS, ZRS|Nie    |Tak|
|Zachodnie stany USA 2      |LRS, ZRS|Nie    |Tak|

\* W przypadku regionów bez obsługi portalu można nadal używać programu PowerShell lub interfejsu wiersza polecenia platformy Azure do tworzenia większych udziałów TiB. Alternatywnie możesz utworzyć nowy udział za pośrednictwem portalu bez określania limitu przydziału. Spowoduje to utworzenie udziału o domyślnym rozmiarze 100 TiB, który można później zaktualizować za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

Aby pomóc nam określić priorytety nowych regionów i funkcji, Wypełnij tę [ankietę](https://aka.ms/azurefilesatscalesurvey).

### <a name="steps-to-onboard"></a>Kroki do dołączenia

Aby zarejestrować subskrypcję w większej wersji zapoznawczej udziałów plików, musisz użyć Azure PowerShell. Możesz użyć [Azure Cloud Shell](https://shell.azure.com/) lub zainstalować [moduł Azure PowerShell lokalnie](https://docs.microsoft.com/powershell/azure/install-Az-ps?view=azps-2.4.0) , aby uruchomić następujące polecenia programu PowerShell:

Najpierw upewnij się, że wybrano subskrypcję, która ma zostać zarejestrowana w wersji zapoznawczej:

```powershell
$context = Get-AzSubscription -SubscriptionId ...
Set-AzContext $context
```

Następnie zarejestruj się w wersji zapoznawczej przy użyciu następujących poleceń:

```powershell
Register-AzProviderFeature -FeatureName AllowLargeFileShares -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```
Twoja subskrypcja jest automatycznie zatwierdzana po uruchomieniu obu poleceń.

Aby sprawdzić stan rejestracji, możesz uruchomić następujące polecenie:

```powershell
Get-AzProviderFeature -FeatureName AllowLargeFileShares -ProviderNamespace Microsoft.Storage
```

Zaktualizowanie stanu do **zarejestrowanego**może potrwać do 15 minut. Po **zarejestrowaniu**stanu powinno być możliwe korzystanie z tej funkcji.

### <a name="use-larger-file-shares"></a>Użyj większych udziałów plików

Aby rozpocząć korzystanie z większych udziałów plików, Utwórz nowe konto magazynu ogólnego przeznaczenia w wersji 2 i nowy udział plików.

## <a name="data-growth-pattern"></a>Wzorzec wzrostu danych

Obecnie maksymalny rozmiar udziału plików platformy Azure to 5 TiB (100 TiB w wersji zapoznawczej). Ze względu na bieżące ograniczenie należy wziąć pod uwagę oczekiwany wzrost ilości danych podczas wdrażania udziału plików platformy Azure.

Istnieje możliwość synchronizacji wielu udziałów plików platformy Azure z jednym serwerem plików systemu Windows z Azure File Sync. Pozwala to zagwarantować, że starsze, duże udziały plików, które mogą być lokalnie dostępne, mogą zostać wprowadzone do Azure File Sync. Aby uzyskać więcej informacji, zobacz [Planowanie wdrożenia Azure File Sync](storage-files-planning.md).

## <a name="data-transfer-method"></a>Metoda transferu danych

Istnieje wiele łatwych opcji przesyłania zbiorczego danych z istniejącego udziału plików, takiego jak lokalny udział plików, do Azure Files. Oto kilka popularnych (niewyczerpująca lista):

* **Azure File Sync**: W ramach pierwszej synchronizacji między udziałem plików platformy Azure ("punktem końcowym w chmurze") a przestrzenią nazw katalogu systemu Windows ("punkt końcowy serwera") Azure File Sync będzie replikowana wszystkie dane z istniejącego udziału plików do Azure Files.
* **[Importowanie/Eksportowanie na platformie Azure](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** : Usługa Azure Import/Export umożliwia bezpieczne przesyłanie dużych ilości danych do udziału plików platformy Azure przez wysyłanie dysków twardych do centrum danych platformy Azure. 
* **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)** : Robocopy to dobrze znane narzędzie do kopiowania, które jest dostarczane z systemami Windows i Windows Server. Robocopy może służyć do transferowania danych do Azure Files przez zainstalowanie udziału plików lokalnie, a następnie użycie zainstalowanej lokalizacji jako miejsca docelowego w poleceniu Robocopy.
* **[AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** : AzCopy to narzędzie wiersza polecenia przeznaczone do kopiowania danych do i z Azure Files, a także do usługi Azure Blob Storage przy użyciu prostych poleceń z optymalną wydajnością.

## <a name="next-steps"></a>Następne kroki
* [Planowanie wdrożenia Azure File Sync](storage-sync-files-planning.md)
* [Wdrażanie Azure Files](storage-files-deployment-guide.md)
* [Wdrażanie Azure File Sync](storage-sync-files-deployment-guide.md)
