---
title: Warstwy dostępu gorąca, chłodna i archiwalna dla obiektów Blob — Azure Storage
description: Warstwy dostępu gorąca, chłodna i archiwalna dla kont usługi Azure Storage.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/23/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: d370a8ac93de20596b2c15f52427d09f11c2b1bd
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68722054"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>Azure Blob Storage: warstwy dostępu gorąca, chłodna i archiwalna

Usługa Azure Storage oferuje różne warstwy dostępu, dzięki którym można przechowywać dane obiektów BLOB w najbardziej opłacalny sposób. Dostępne warstwy dostępu obejmują:

- Zoptymalizowany pod kątem przechowywania danych, które są często dostępne.
- **Chłodnie** zoptymalizowane pod kątem przechowywania danych, które są rzadko używane i są przechowywane przez co najmniej 30 dni.
- Optymalizacja zoptymalizowana pod kątem przechowywania danych, które są rzadko używane i przechowywane przez co najmniej 180 dni z elastycznymi wymaganiami opóźnienia (w kolejności godzin).

Poniższe uwagi dotyczą różnych warstw dostępu:

- Warstwa dostępu archiwalnego jest dostępna tylko na poziomie obiektu BLOB, a nie na poziomie konta magazynu.
- Dane w warstwie dostępu chłodnego mogą być nieznacznie niższej dostępności, ale nadal wymagają wysokiej trwałości i podobnych cech czasu do dostępu i przepływności jako danych gorąca. W przypadku danych chłodnych nieznacznie niższa umowa dotycząca poziomu usług dostępności i wyższe koszty dostępu w porównaniu z gorącą usługą są akceptowalnymi kosztami niższych kosztów magazynowania.
- Magazyn Archiwum działa w trybie offline i ma najniższe koszty magazynowania, ale także najwyższe koszty dostępu.
- Na poziomie konta można ustawić tylko warstwy dostępu gorąca i chłodna.
- Warstwy gorąca, chłodna i archiwalna można ustawić na poziomie obiektu.

Dane przechowywane w chmurze zwiększają się w tempie wykładniczym. Aby zarządzać kosztami zwiększających się potrzeb dotyczących magazynowania, warto zorganizować dane na podstawie atrybutów, takich jak częstotliwość dostępu i planowany okres przechowywania. Pozwoli to na optymalizację kosztów. Dane przechowywane w chmurze mogą być różne w zależności od tego, jak są generowane, przetwarzane i dostępne w okresie istnienia. Do niektórych danych często uzyskuje się dostęp. Są one również często modyfikowane w trakcie całego okresu istnienia. Do niektórych danych często uzyskuje się dostęp na początkowym etapie istnienia, a z czasem już zdecydowanie rzadziej. Niektóre dane pozostają w stanie bezczynności w chmurze i są rzadko używane, jeśli kiedykolwiek są do niego dostępne.

Każdy z tych scenariuszy dostępu do danych korzysta z innej warstwy dostępu, która jest zoptymalizowana pod kątem określonego wzorca dostępu. Dzięki warstwom dostępu gorąca, chłodna i archiwalna usługa Azure Blob Storage jest wymagana w przypadku zróżnicowanych warstw dostępu z oddzielnymi modelami cenowymi.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Konta magazynu z obsługą warstw

Dane magazynu obiektów można przyciągać tylko do warstwy gorąca, chłodna lub archiwalna na kontach BLOB Storage i Ogólnego przeznaczenia v2 (GPv2). Konta ogólnego przeznaczenia w wersji 1 (GPv1) nie obsługują warstw. Można jednak łatwo skonwertować istniejące konta GPv1 lub BLOB Storage na konta GPv2 w ramach procesu jednego kliknięcia w Azure Portal. GPv2 zapewnia nową strukturę cenową dla obiektów blob, plików i kolejek oraz dostęp do różnych nowych funkcji magazynu. W przód nowe funkcje i ceny są oferowane tylko na kontach GPv2. W związku z tym należy oceniać przy użyciu kont usługi GPv2, ale używać ich tylko po przejrzeniu cen wszystkich usług. Niektóre obciążenia mogą być droższe w GPv2 niż GPv1. Aby uzyskać więcej informacji, zobacz [Omówienie konta magazynu platformy Azure](../common/storage-account-overview.md).

Konta magazynu obiektów blob i GPv2 uwidaczniają atrybut **Warstwa dostępu** na poziomie konta. Ten atrybut umożliwia określenie domyślnej warstwy dostępu jako gorąca lub chłodna dla dowolnego obiektu BLOB na koncie magazynu, w którym nie ustawiono warstwy jawnej na poziomie obiektu. W przypadku obiektów z warstwą ustawioną na poziomie obiektu warstwa konta nie będzie stosowana. Warstwę archiwum można stosować tylko na poziomie obiektu. W każdej chwili można przełączać się między tymi warstwami dostępu.

## <a name="premium-performance-block-blob-storage"></a>Magazyn obiektów BLOB w warstwie Premium

Magazyn blokowych obiektów BLOB w warstwie Premium udostępnia często używane dane za pośrednictwem sprzętu o wysokiej wydajności. Dane z tej warstwy wydajności są przechowywane na dyskach półprzewodnikowych (dysków SSD), które są zoptymalizowane pod kątem niskich i spójnych opóźnień. Dysków SSD zapewniają wyższe stawki transakcyjne i przepływność w porównaniu z tradycyjnymi dyskami twardymi.

Magazyn obiektów BLOB w warstwie Premium jest idealnym rozwiązaniem w przypadku obciążeń wymagających szybkiego i spójnego czasu odpowiedzi. Najlepiej jest w przypadku obciążeń, które wykonują wiele małych transakcji, takich jak przechwytywanie danych telemetrycznych, komunikatów i transformacji danych. Dane, które obejmują użytkowników końcowych, takie jak interaktywne Edytowanie wideo, statyczna zawartość sieci Web i transakcje online, są również dobrymi kandydatami.

Magazyn obiektów BLOB w warstwie Premium jest dostępny tylko za pośrednictwem typu konta magazynu blokowych obiektów blob, a obecnie nie obsługuje warstw dostępu gorąca, chłodna lub archiwalna.

## <a name="hot-access-tier"></a>Warstwa dostępu Gorąca

Warstwa dostępu gorąca ma wyższe koszty magazynowania niż warstwy chłodna i archiwum, ale najtańsze koszty dostępu. Przykładowe scenariusze użycia dla warstwy dostępu gorąca obejmują:

- Dane, które są używane w aktywnym lub oczekiwanie, są często dostępne (odczyt i zapis).
- Dane przygotowane do przetworzenia i ostatecznej migracji do warstwy dostępu chłodna.

## <a name="cool-access-tier"></a>Warstwa dostępu Chłodna

Warstwa dostępu chłodna ma niższe koszty magazynowania i wyższe koszty dostępu w porównaniu z magazynem gorąca. Ta warstwa jest przeznaczona dla danych, które pozostaną w warstwie Chłodna przez co najmniej 30 dni. Przykładowe scenariusze użycia dla warstwy dostępu chłodnego obejmują:

- Krótkoterminowe kopie zapasowe i zestawy danych odzyskiwania po awarii.
- Starszą zawartość nośników, która nie jest już często wyświetlana, ale oczekiwane jest, że będzie ona natychmiast dostępna, gdy będzie to wymagane.
- Duże zbiory danych, które muszą być przechowywane w sposób ekonomiczny, podczas gdy na potrzeby przyszłego przetwarzania zbierana jest większa ilość danych (*np.* długoterminowe magazynowanie danych naukowych lub nieprzetworzonych danych telemetrycznych z zakładu produkcyjnego).

## <a name="archive-access-tier"></a>Warstwa dostępu Archiwum

Warstwa dostępu archiwalnego ma najniższy koszt magazynowania i wyższe koszty pobierania danych w porównaniu z warstwami gorąca i chłodna. Ta warstwa jest przeznaczona dla danych, które można pobierać z opóźnieniem kilku godzin i które pozostaną w warstwie Archiwum przez co najmniej 180 dni.

Gdy obiekt BLOB znajduje się w magazynie archiwalnym, dane obiektów BLOB są w trybie offline i nie można ich odczytywać, kopiować, zastępować ani modyfikować. Nie można wykonać migawek obiektu BLOB w magazynie archiwum. Jednak metadane obiektu BLOB pozostają w trybie online i są dostępne, umożliwiając wyświetlenie listy obiektów blob i jego właściwości. W przypadku obiektów BLOB w archiwum jedynymi prawidłowymi operacjami są GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier i DeleteBlob.

Przykładowe scenariusze użycia dla warstwy dostępu archiwalnego obejmują:

- Długoterminowe kopie zapasowe, dodatkowe kopie zapasowej i archiwalne zestawy danych
- Oryginalne (nieprzetworzone) dane, które muszą zostać zachowane, nawet po przetworzeniu ich do ostatecznej użytecznej postaci (*np.* nieprzetworzone pliki multimedialne po transkodowaniu do innych formatów).
- Dane zgodności i dane archiwalne, które muszą być przechowywane przez długi czas, i do których bardzo rzadko uzyskuje się dostęp (*Na przykład*: film z bezpieczeństwem, stare X-promienie/rezonansu magnetycznego dla organizacji opieki zdrowotnej, nagrania audio i transkrypcje wezwań klienta na usługi finansowe)

### <a name="blob-rehydration"></a>Ponowne wypełnianie obiektów blob

Aby odczytać dane w magazynie archiwalnym, należy najpierw zmienić warstwę obiektu blob na gorącą lub chłodną. Proces ten, określany jako „ponowne wypełnianie” (ang. rehydration), może potrwać do 15 godzin. Duże rozmiary obiektów BLOB są zalecane w celu uzyskania optymalnej wydajności. Ponowne wypełnianie kilku małych obiektów blob równocześnie może spowodować dalsze przedłużenie czasu.

Podczas ponownego wypełniania można sprawdzać właściwość obiektu blob **Stan archiwum**, aby upewnić się, że warstwa została zmieniona. Właściwość ta ma wartość „rehydrate-pending-to-hot” (ponowne wypełnianie w celu przejścia do warstwy gorącej) lub „rehydrate-pending-to-cool” (ponowne wypełnianie w celu przejścia do warstwy chłodnej) w zależności od warstwy docelowej. Po zakończeniu tego procesu właściwość obiektu blob „stan archiwum” jest usuwana, a wartość właściwości **Warstwa dostępu** odpowiada nowej warstwie Gorąca lub Chłodna.  

## <a name="blob-level-tiering"></a>Obsługa warstw na poziomie obiektów blob

Funkcja obsługi warstw na poziomie obiektów blob umożliwia zmianę warstwy danych na poziomie obiektu przy użyciu jednej operacji o nazwie [Ustawianie warstwy obiektu blob](/rest/api/storageservices/set-blob-tier). W odpowiedzi na zmiany wzorców użycia można łatwo zmieniać warstwy dostępu do obiektu blob (wybierając warstwę gorącą, chłodną lub archiwalną) bez przenoszenia danych między kontami. Wszystkie zmiany warstwy są wykonywane natychmiast. Ponownego wypełniania obiekt BLOB z archiwum może jednak trwać kilka godzin.

Czas ostatniej zmiany warstwy obiektu blob jest uwidaczniany za pomocą właściwości obiektu blob **Czas zmiany warstwy dostępu**. Jeśli obiekt BLOB znajduje się w warstwie archiwum, nie można go zastąpić, więc przekazywanie tego samego obiektu BLOB nie jest dozwolone w tym scenariuszu. Można zastąpić obiekt BLOB w warstwie gorąca lub chłodna, w tym przypadku nowy obiekt BLOB dziedziczy warstwę obiektu BLOB, który został zastąpiony.

Obiekty blob we wszystkich trzech warstwach dostępu mogą współistnieć w ramach tego samego konta. Obiekt blob bez jawnie przypisanej warstwy korzysta z warstwy zgodnie z ustawieniem warstwy dostępu konta. Jeśli warstwa dostępu jest wywnioskowana z konta, właściwość obiektu BLOB wywnioskowanej **warstwy dostępu** jest ustawiona na wartość "true", a właściwość obiektu BLOB **warstwy dostępu** do obiektów BLOB jest zgodna z warstwą konta. W Azure Portal Właściwość wywnioskowana Warstwa dostępu jest wyświetlana z użyciem warstwy dostępu do obiektów BLOB (na przykład gorąca **(wywnioskowana)** lub **chłodna (wywnioskowana)** .

> [!NOTE]
> Magazyn Archiwum i funkcja obsługi warstw na poziomie obiektów blob obsługują tylko blokowe obiekty blob. Nie można także zmienić warstwy blokowego obiektu blob, który ma migawki.

> [!NOTE]
> Dane przechowywane na koncie magazynu blokowych obiektów BLOB nie mogą być obecnie warstwowe dla gorąca, chłodna lub archiwalna przy użyciu [warstwy obiektu BLOB](/rest/api/storageservices/set-blob-tier) lub funkcji zarządzania cyklem życia BLOB Storage Azure.
> Aby przenieść dane, należy synchronicznie skopiować obiekty blob z konta blokowego usługi BLOB Storage do warstwy dostępu gorąca na innym koncie przy użyciu [funkcji Put blok z adresu URL](/rest/api/storageservices/put-block-from-url) lub wersji AzCopy, która obsługuje ten interfejs API.
> *Blok Put z adresu URL* synchronicznie kopiuje dane na serwerze, co oznacza, że wywołanie kończy się tylko wtedy, gdy wszystkie dane są przenoszone z oryginalnej lokalizacji serwera do lokalizacji docelowej.

### <a name="blob-lifecycle-management"></a>Zarządzanie cyklem życia obiektów BLOB

Blob Storage Zarządzanie cyklem życia obejmuje zaawansowane zasady oparte na regułach, których można użyć do przechodzenia danych do najlepszej warstwy dostępu oraz do wygasania danych na koniec cyklu życia. Aby dowiedzieć się więcej [, zobacz Zarządzanie cyklem życia usługi Azure Blob Storage](storage-lifecycle-management-concepts.md) .  

### <a name="blob-level-tiering-billing"></a>Rozliczanie obsługi warstw na poziomie obiektów blob

Gdy obiekt BLOB jest przenoszony do warstwy chłodnicy (gorąca > chłodna, archiwum gorąca > lub archiwum chłodnych >), operacja jest rozliczana jako operacja zapisu w warstwie docelowej, w przypadku której stosuje się opłaty za operacje zapisu (za 10 000) i zapis danych (za GB) dla warstwy docelowej.

Gdy obiekt BLOB jest przenoszony do warstwy gorąca (archiwum-> chłodna, > archiwalna lub chłodna > gorąca), operacja jest rozliczana jako odczyt z warstwy źródłowej, gdzie stosowane są opłaty za operacje odczytu (za 10 000) i pobieranie danych (za GB) dla warstwy źródłowej. Poniższa tabela zawiera podsumowanie sposobu rozliczania zmian warstwy.

| | **Opłaty za zapis (operacja i dostęp)** | **Opłaty za odczyt (operacja i dostęp)**
| ---- | ----- | ----- |
| **Kierunek SetBlobTier** | gorące > chłodna, Archiwum na gorąco >, archiwum > chłodnych | Archiwizuj > chłodna, archiwalna, > gorąca, chłodna > gorąca

W przypadku przełączania warstwy konta z Gorąca na Chłodna opłaty zostaną naliczone za operacje zapisu (za 10 000 operacji) dla wszystkich obiektów blob bez ustawionej warstwy tylko na kontach GPv2. Ta zmiana nie jest naliczana w ramach kont usługi BLOB Storage. Zostanie naliczona opłata za operacje odczytu (za 10 000 operacji) i pobieranie danych (za GB), jeśli przełączasz konta magazynu usługi Blob Storage lub GPv2 z warstwy Chłodna na Gorąca. Mogą również obowiązywać opłaty za wczesne usunięcie dla dowolnego obiektu przeniesionego z warstwy Chłodna lub Archiwum.

### <a name="cool-and-archive-early-deletion"></a>Opłaty za wcześniejsze usunięcie w warstwach Chłodna i Archiwum

Oprócz opłaty miesięcznej za GB, każdy obiekt blob przenoszony do warstwy Chłodna (tylko konta GPv2) jest objęty okresem wcześniejszego usunięcia z warstwy Chłodna przez 30 dni, a każdy obiekt przenoszony do warstwy Archiwum jest objęty okresem wcześniejszego usunięcia z warstwy Chłodna przez 180 dni. Ta opłata jest naliczana proporcjonalnie. Jeśli na przykład obiekt blob zostanie przeniesiony do warstwy Archiwum, a następnie usunięty lub przeniesiony do warstwy Gorąca po 45 dniach, zostanie naliczona opłata za wczesne usunięcie odpowiadająca 135 (180 minus 45) dniom przechowywania tego obiektu blob w archiwum.

Możesz obliczyć wczesne usunięcie przy użyciu właściwości obiektu BLOB, **czasu utworzenia**, jeśli nie ma żadnych zmian w warstwie dostępu. W przeciwnym razie można użyć, gdy warstwa dostępu została ostatnio zmodyfikowana do chłodna lub archiwalna, wyświetlając właściwość obiektu BLOB: **Warstwa dostępu-zmiana-czas**. Aby uzyskać więcej informacji na temat właściwości obiektów blob, zobacz [pobieranie właściwości obiektów BLOB](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Porównywanie opcji magazynu blokowych obiektów BLOB

W poniższej tabeli przedstawiono porównanie magazynu obiektów BLOB wydajności w warstwie Premium oraz warstw dostępu gorąca, chłodna i archiwalna.

|                                           | **Wydajność warstwy Premium** | **Warstwa gorąca** | **Warstwa chłodna** | **Warstwa Archiwum**
| ----------------------------------------- | ---------------- | ------------ | ----- | ----- |
| **Dostępność**                          | 99,9%            | 99,9%        | 99% | ND |
| **Dostępność** <br> **(odczyty RA-GRS)**  | ND              | 99,99%       | 99,9% | ND |
| **Opłaty za użycie**                         | Wyższe koszty magazynowania, niższy koszt dostępu i transakcji | Wyższe koszty magazynowania, niższy dostęp i koszty transakcji | Niższe koszty magazynowania, wyższego poziomu dostępu i kosztów transakcji | Najniższe koszty magazynowania, najwyższy poziom dostępu i koszty transakcji |
| **Minimalny rozmiar obiektu**                   | ND | ND | ND | ND |
| **Minimalny czas magazynowania**              | ND | ND | 30 dni (tylko GPv2) | 180 dni
| **Opóźnienie** <br> **(czas do pierwszego bajtu)** | Jednocyfrowe milisekundy | milisekundy | milisekundy | mniej niż 15 godz.

> [!NOTE]
> Aby uzyskać skalowalność i cele wydajności, zobacz [cele dotyczące skalowalności i wydajności usługi Azure Storage dla kont magazynu](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="quickstart-scenarios"></a>Scenariusze typu Szybki start

W tej sekcji przedstawiono następujące scenariusze obejmujące użycie witryny Azure Portal:

- Jak zmienić domyślną warstwę dostępu konta GPv2 lub usługi Blob Storage.
- Jak zmienić warstwę obiektu blob na koncie GPv2 lub usługi Blob Storage.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Zmienianie domyślnej warstwy dostępu konta GPv2 lub usługi Blob Storage

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Aby przejść do konta magazynu, zaznacz pozycję Wszystkie zasoby, a następnie wybierz konto magazynu.

1. W bloku Ustawienia kliknij pozycję **Konfiguracja**, aby wyświetlić i/lub zmienić konfigurację konta.

1. Wybierz odpowiednią warstwę dostępu dla potrzeb: Ustaw **warstwę dostępu** na wartość **chłodna** lub **gorąca**.

1. Kliknij pozycję **Zapisz** w górnej części bloku.

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Zmienianie warstwy obiektu BLOB na koncie GPv2 lub BLOB Storage

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Aby przejść do obiektu BLOB na koncie magazynu, wybierz pozycję **wszystkie zasoby**, wybierz konto magazynu, wybierz kontener, a następnie wybierz obiekt BLOB.

1. W bloku **właściwości obiektu BLOB** wybierz menu rozwijane **Warstwa dostępu** , aby wybrać warstwędostępu gorąca, **chłodna**lub **archiwalna** .

1. Kliknij pozycję **Zapisz** w górnej części bloku.

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Wszystkie konta magazynu używają modelu cenowego dla magazynu obiektów BLOB na podstawie warstwy każdego obiektu BLOB. Należy pamiętać o następujących kwestiach dotyczących rozliczeń:

- **Koszty magazynu**: Oprócz ilości przechowywanych danych koszt przechowywania danych różni się w zależności od warstwy dostępu. Koszt za gigabajt zmniejsza się w miarę, jak warstwa staje się chłodniejsza.
- **Koszty dostępu do danych**: Opłaty za dostęp do danych zwiększają się, ponieważ warstwa uzyskuje chłodnicę. W przypadku danych w warstwie dostępu chłodna i archiwalna naliczana jest opłata za dostęp do danych za każdy gigabajt dla operacji odczytu.
- **Koszty transakcji**: Istnieje opłata za transakcję dla wszystkich warstw, które zwiększają się, gdy warstwa uzyskuje chłodnicę.
- **Koszty transferu danych replikacji**geograficznej: Ta opłata dotyczy tylko kont ze skonfigurowaną replikacją geograficzną, w tym GRS i RA-GRS. Transfer danych w ramach replikacji geograficznej powoduje naliczanie opłaty za każdy gigabajt.
- **Koszty transferu danych wychodzących**: Wychodzące transfery danych (dane przesyłane z regionu platformy Azure) powodują naliczanie opłat za użycie przepustowości dla każdego gigabajta, spójne z kontami magazynu ogólnego przeznaczenia.
- **Zmiana warstwy dostępu**: Zmiana warstwy dostępu do konta z chłodna na gorąca spowoduje naliczenie opłaty równej odczytaniu wszystkich danych istniejących na koncie magazynu. Jednak zmiana warstwy dostępu do konta z gorąca na chłodna spowoduje naliczenie opłaty równej zapisywaniu wszystkich danych w warstwie chłodna (tylko konta GPv2).

> [!NOTE]
> Aby uzyskać więcej informacji na temat cen dla kont usługi BLOB Storage, zobacz stronę z [cennikiem usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/) . Więcej informacji dotyczących opłat za transfer danych wychodzących można znaleźć na stronie [Szczegóły cennika transferów danych](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="faq"></a>Często zadawane pytania

**Czy używać konta usługi Blob Storage, czy GPv2, jeśli chcę obsługiwać warstwy danych?**

Zalecamy korzystanie z kont GPv2 zamiast kont usługi Blob Storage na potrzeby obsługi warstw. Konta GPv2 obsługują wszystkie funkcje, które obsługują konta usługi Blob Storage, i wiele innych. Ceny kont usługi Blob Storage i GPv2 są niemal identyczne, ale niektóre nowe funkcje i rabaty cenowe będą dostępne tylko na kontach GPv2. Konta GPv1 nie obsługują warstw.

Struktury cen kont GPv1 i GPv2 są różne, a klienci powinni dokładnie je sprawdzić przed podjęciem decyzji o użyciu kont GPv2. Istniejące konto usługi Blob Storage lub GPv1 można łatwo przekonwertować na konto GPv2 w ramach prostego procesu uruchamianego jednym kliknięciem. Aby uzyskać więcej informacji, zobacz [Omówienie konta magazynu platformy Azure](../common/storage-account-overview.md).

**Czy mogę przechowywać obiekty we wszystkich trzech warstwach dostępu (gorąca, chłodna i archiwalna) na tym samym koncie?**

Tak. Atrybut **Warstwa dostępu** ustawiany na poziomie konta to domyślna warstwa mająca zastosowanie do wszystkich obiektów na tym koncie bez jawnie ustawionej warstwy. Jednak obsługa warstw na poziomie obiektów blob umożliwia ustawienie warstwy dostępu na poziomie obiektu niezależnie od ustawienia warstwy dostępu konta. Obiekty blob w jednej z trzech warstw dostępu (gorąca, chłodna lub archiwalna) mogą znajdować się w ramach tego samego konta.

**Czy mogę zmienić domyślną warstwę dostępu na moim koncie magazynu obiektów blob lub GPv2?**

Tak, możesz zmienić domyślną warstwę dostępu, ustawiając atrybut **Warstwa dostępu** na koncie magazynu. Zmiana warstwy dostępu dotyczy wszystkich obiektów przechowywanych na koncie, które nie mają jawnie ustawionej warstwy. Przełączenie warstwy dostępu z gorącej na chłodną operacji zapisu (na 10 000) dla wszystkich obiektów Blob bez zestawu warstwy na kontach GPv2 i przełączanie z chłodzenia do gorąca powoduje, że operacje odczytu (za 10 000) i pobierania danych (za GB) dla wszystkich obiektów BLOB w usłudze BLOB Storage i konta GPv2.

**Czy mogę ustawić domyślną warstwę dostępu do konta na Archiwum?**

Nie. Tylko warstwy dostępu gorąca i chłodna mogą być ustawione jako domyślna Warstwa dostępu do konta. Archiwum można ustawić tylko na poziomie obiektu.

**W jakich regionach są dostępne warstwy dostępu gorąca, chłodna i archiwalna?**

Warstwy dostępu gorąca i chłodna wraz z warstwami na poziomie obiektów BLOB są dostępne we wszystkich regionach. Magazyn w warstwie Archiwum będzie początkowo dostępny tylko w wybranych regionach. Pełną listę można znaleźć w temacie [Dostępność produktów platformy Azure według regionów](https://azure.microsoft.com/regions/services/).

**Czy obiekty blob w warstwie dostępu chłodnego działają inaczej niż te w warstwie dostępu gorąca?**

Obiekty blob w warstwie dostępu gorącą mają takie samo opóźnienie jak obiekty blob na kontach GPv1, GPv2 i BLOB Storage. Obiekty blob w warstwie dostępu chłodnego mają podobne opóźnienie (w milisekundach) jako obiekty blob na kontach GPv1, GPv2 i BLOB Storage. Obiekty blob w warstwie dostępu archiwizowania mają kilka godzin opóźnienia na kontach GPv1, GPv2 i BLOB Storage.

Obiekty blob w warstwie dostępu chłodnego mają nieco niższy poziom dostępności usług (SLA) niż obiekty blob przechowywane w warstwie dostępu gorąca. Aby uzyskać więcej informacji, zobacz [Magazyn — umowa SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**Czy operacje w warstwach Gorąca, Chłodna i Archiwum są takie same?**

Tak. Wszystkie operacje w warstwach Gorąca i Chłodna są w 100% spójne. Wszystkie prawidłowe operacje w warstwie Archiwum, w tym usuwanie, tworzenie listy, pobieranie właściwości/metadanych obiektów blob i ustawianie warstwy obiektu blob, są w 100% spójne z warstwami Gorąca i Chłodna. Obiektu blob znajdującego się w warstwie Archiwum nie można odczytywać ani modyfikować.

**Kiedy podczas ponownego wypełniania obiektu blob z warstwy Archiwum do warstwy Gorąca lub Chłodna będę wiedzieć, że ten proces został ukończony?**

Podczas ponownego wypełniania można używać operacji pobierania właściwości obiektu blob, aby sondować atrybut **Stan archiwum** w celu potwierdzenia ukończenia zmiany warstwy. Właściwość ta ma wartość „rehydrate-pending-to-hot” (ponowne wypełnianie w celu przejścia do warstwy gorącej) lub „rehydrate-pending-to-cool” (ponowne wypełnianie w celu przejścia do warstwy chłodnej) w zależności od warstwy docelowej. Po zakończeniu tego procesu właściwość obiektu blob „stan archiwum” jest usuwana, a wartość właściwości **Warstwa dostępu** odpowiada nowej warstwie Gorąca lub Chłodna.  

**Kiedy po ustawieniu warstwy obiektu blob rozpocznie się naliczanie opłat przy użyciu odpowiedniej stawki?**

Każdy obiekt BLOB jest zawsze rozliczany zgodnie z warstwą określoną przez właściwość **warstwy dostępu** obiektu BLOB. Po ustawieniu nowej warstwy dla obiektu BLOB Właściwość **Warstwa dostępu** natychmiast odzwierciedla nową warstwę dla wszystkich przejść. Jednak ponowne wypełnienie obiektu blob z warstwy Archiwum w warstwie Gorąca lub Chłodna może potrwać kilka godzin. W tym przypadku opłaty są naliczane według stawek za archiwizację do momentu ukończenia ponownego wypełniania. w tym momencie Właściwość **Warstwa dostępu** odzwierciedla nową warstwę. W tym momencie są naliczane opłaty za ten obiekt BLOB przy użyciu stawki gorąca lub chłodna.

**Jak mogę ustalić, czy będę płacić za wczesne usunięcie w przypadku usunięcia lub przeniesienia obiektu blob z warstwy Chłodna lub Archiwum?**

Proporcjonalne opłaty za wczesne usunięcie będą dotyczyć wszystkich obiektów blob usuniętych lub przeniesionych z warstwy Chłodna (tylko konta GPv2) lub Archiwum przed upływem odpowiednio 30 lub 180 dni. Możesz określić, jak długo obiekt BLOB znajduje się w warstwie chłodna lub archiwum, sprawdzając właściwość obiektu BLOB **czasu zmiany warstwy dostępu** , która zapewnia sygnaturę ostatniej zmiany warstwy. Aby uzyskać więcej informacji, zobacz [chłodna i archiwum wczesne usuwanie](#cool-and-archive-early-deletion).

**Które narzędzia i zestawy SDK platformy Azure obsługują warstwy na poziomie obiektów blob i magazyn w warstwie Archiwum?**

Następujące rozwiązania: witryna Azure Portal, program PowerShell, narzędzia interfejsu wiersza polecenia oraz biblioteki klienta środowisk .NET, Java, Python i Node.js obsługują warstwy na poziomie obiektów blob i magazyn w warstwie Archiwum.  

**Ile danych można przechowywać w warstwach Gorąca, Chłodna i Archiwum?**

Magazyn danych oraz inne limity są ustawiane na poziomie konta, a nie na warstwie dostępu. W związku z tym można wybrać opcję użycia wszystkich limitów w jednej warstwie lub we wszystkich trzech warstwach. Aby uzyskać więcej informacji, zobacz [cele dotyczące skalowalności i wydajności usługi Azure Storage](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Następne kroki

### <a name="evaluate-hot-cool-and-archive-in-gpv2-blob-storage-accounts"></a>Ocena warstw Gorąca, Chłodna i Archiwum na kontach GPv2 i usługi Blob Storage

[Sprawdzanie dostępności warstw Gorąca, Chłodna i Archiwum według regionu](https://azure.microsoft.com/regions/#services)

[Zarządzanie cyklem życia usługi Azure Blob Storage](storage-lifecycle-management-concepts.md)

[Ocena użycia bieżących kont magazynu przez włączenie metryk usługi Azure Storage](../common/storage-enable-and-view-metrics.md)

[Sprawdzanie cen warstw Gorąca, Chłodna i Archiwum na kontach usługi Blob Storage i GPv2 według regionu](https://azure.microsoft.com/pricing/details/storage/)

[Sprawdzanie ceny transferu danych](https://azure.microsoft.com/pricing/details/data-transfers/)
