---
title: Gorąca chłodna i archiwum warstwy dostępu dla obiektów blob — usługi Azure Storage
description: Gorąca chłodna i archiwum warstwy dostępu dla konta usługi Azure storage.
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: mhopkins
ms.reviewer: clausjor
ms.subservice: blobs
ms.openlocfilehash: 8e9e27f14c4ccd6f5822033baa21aaafcf96c428
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65148510"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>Usługa Azure Blob storage: gorąca, chłodna i archiwalna dostępu

Usługa Azure storage oferuje dostęp do różnych warstw, które umożliwiają przechowywanie danych obiektów blob w sposób najbardziej efektywny. Warstwy dostępu obejmują:

- **Gorąca** — zoptymalizowana pod kątem przechowywania danych, z których korzysta się często.
- **Chłodna** — zoptymalizowana pod kątem magazynowania danych, które rzadko uzyskuje dostęp i przechowywane przez co najmniej 30 dni.
- **Archiwum** — zoptymalizowana pod kątem magazynowania danych, które rzadko uzyskuje dostęp i przechowywane przez co najmniej 180 dni elastyczne wymagania dotyczące opóźnień (rzędu kilku godzin).

Do dostępu do różnych warstw obowiązują następujące zastrzeżenia:

- Warstwa dostępu archiwalna jest dostępna tylko na poziomie obiektów blob, a nie na poziomie konta magazynu.
- Dane w warstwie dostępu chłodna może tolerować nieco niższą dostępność, ale nadal wymagane wysoka trwałość oraz podobny czas dostępu i parametry przepływności jak gorących danych. Chłodnych danych, nieco niższą dostępności umowy poziomu usług (SLA) i wyższych dostępu koszty w porównaniu z gorącymi danymi to dopuszczalne wady, biorąc pod uwagę niższe koszty magazynowania.
- Magazyn Archiwum działa w trybie offline i ma najniższe koszty magazynowania, ale także najwyższe koszty dostępu.
- Można ustawić tylko warstwy dostępu gorąca i chłodna na poziomie konta.
- Gorąca, chłodna i archiwalna można ustawić na poziomie obiektu.

Dane przechowywane w chmurze rośnie w tempie wykładniczym. Aby zarządzać kosztami zwiększających się potrzeb dotyczących magazynowania, warto zorganizować dane na podstawie atrybutów, takich jak częstotliwość dostępu i planowany okres przechowywania. Pozwoli to na optymalizację kosztów. Dane przechowywane w chmurze mogą być różne pod względem jak został wygenerowany, przetwarzane i dostępne przez cały okres ich istnienia. Do niektórych danych często uzyskuje się dostęp. Są one również często modyfikowane w trakcie całego okresu istnienia. Do niektórych danych często uzyskuje się dostęp na początkowym etapie istnienia, a z czasem już zdecydowanie rzadziej. Niektóre dane pozostają nieużywane w chmurze i rzadko, jeśli, uzyskuje się dostęp po jego zapisaniu.

Każda z tych scenariuszy dostępu do danych istnieją korzyści płynące z warstwy dostępu do innego, zoptymalizowana pod kątem określonego wzorca dostępu. Za pomocą hot adresy magazynu chłodna i archiwalna dostępu do obiektów Blob platformy Azure to potrzebę warstw zróżnicowany dostęp z oddzielnymi modelami cenowymi.

## <a name="storage-accounts-that-support-tiering"></a>Konta magazynu z obsługą warstw

Może być tylko warstwy dane magazynu obiektów do warstw gorąca, chłodna lub archiwum w usłudze Blob storage i ogólnego przeznaczenia w wersji 2 (GPv2) konta. Konta ogólnego przeznaczenia w wersji 1 (GPv1) nie obsługują warstw. Można jednak łatwo przekształcać istniejące konta GPv1 lub usługi Blob storage do kont GPv2 w procesie jednym kliknięciem w witrynie Azure portal. Konto GPv2 oferuje nową strukturę cen obiektów blob, plików, kolejek i dostęp do różnych innych nowych funkcji magazynu. Idąc dalej, niektóre nowe funkcje i rabaty cenowe będą oferowane tylko na kontach GPv2. W związku z tym należy zastanowić się nad korzystaniem kont GPv2, ale tylko ich używać po zapoznaniu się z cenami wszystkich usług. Niektóre obciążenia mogą być droższe na kontach GPv2 niż na kontach GPv1. Aby uzyskać więcej informacji, zobacz [Omówienie konta magazynu platformy Azure](../common/storage-account-overview.md).

Blob storage i GPv2 kont udostępniają **warstwy dostępu** atrybut na poziomie konta. Ten atrybut umożliwia określanie domyślnej warstwy dostępu gorąca lub chłodna dla dowolnego obiektu blob na koncie magazynu, który nie ma jawnie ustawionej warstwy na poziomie obiektu. W przypadku obiektów z warstwą ustawioną na poziomie obiektu warstwa konta nie będzie stosowana. Warstwę archiwum można można stosować tylko na poziomie obiektu. Możesz przełączać się między tymi warstwami dostępu w dowolnym momencie.

## <a name="premium-performance-block-blob-storage"></a>Magazyn Premium wydajności block blob storage

Magazyn Premium wydajności block blob storage udostępnia rzadziej używanych danych za pomocą sprzętu wysokiej wydajności. Dane w tej warstwie wydajności są przechowywane na dyskach półprzewodnikowych (SSD), które są zoptymalizowane pod kątem opóźnień niski i spójne. Dyski SSD zapewniają większe transakcyjne i przepustowość, w porównaniu do tradycyjnych dysków twardych.

Magazyn Premium wydajności block blob storage jest idealny dla obciążeń wymagających czasy reakcji szybkie i spójne. Najlepiej w przypadku obciążeń, które wykonują wiele małych transakcji, takich jak przechwytywanie danych telemetrycznych, wiadomości błyskawiczne i transformacji danych. Nadaje się również dane, które obejmuje użytkowników końcowych, takich jak interakcyjne edytowania materiałów wideo, zawartość statyczną sieci web i transakcji w trybie online.

Magazyn Premium wydajności block blob storage jest dostępna tylko za pośrednictwem typu konta magazynu blokowych obiektów blob, a nie obsługuje obecnie obsługą warstw pozwalająca na warstwę gorąca, chłodna lub archiwalna dostępu.

## <a name="hot-access-tier"></a>Warstwa dostępu Gorąca

Warstwa dostępu gorąca ma wyższe koszty magazynowania, niż w warstwach chłodna i archiwum, ale najniższe koszty dostępu. Przykładowe scenariusze użycia dotyczące warstwy dostępu gorąca obejmują:

- Dane, które jest aktywnie używany lub, że można uzyskać dostęp (Odczyt i zapis) często.
- Dane, które są przygotowywane do przetwarzania i ewentualnej migracji do warstwy dostępu chłodna.

## <a name="cool-access-tier"></a>Warstwa dostępu Chłodna

Warstwa dostępu chłodna ma niższe koszty magazynowania i wyższe koszty dostępu w porównaniu do magazynu gorącego. Ta warstwa jest przeznaczona dla danych, które pozostaną w warstwie Chłodna przez co najmniej 30 dni. Przykładowe scenariusze użycia dotyczące warstwy dostępu chłodna obejmują:

- Krótkoterminowe kopie zapasowe i zestawy danych odzyskiwania po awarii.
- Starszą zawartość nośników, która nie jest już często wyświetlana, ale oczekiwane jest, że będzie ona natychmiast dostępna, gdy będzie to wymagane.
- Duże zbiory danych, które muszą być przechowywane w sposób ekonomiczny, podczas gdy na potrzeby przyszłego przetwarzania zbierana jest większa ilość danych (*np.* długoterminowe magazynowanie danych naukowych lub nieprzetworzonych danych telemetrycznych z zakładu produkcyjnego).

## <a name="archive-access-tier"></a>Warstwa dostępu Archiwum

Warstwa dostępu archiwalna ma najniższy koszt magazynowania i wyższe koszty pobierania danych w porównaniu do warstwy gorąca i chłodna. Ta warstwa jest przeznaczona dla danych, które można pobierać z opóźnieniem kilku godzin i które pozostaną w warstwie Archiwum przez co najmniej 180 dni.

Gdy obiekt blob znajduje się w magazynie archiwalnym, danych obiektów blob jest w trybie offline i nie można odczytać skopiowany, zastąpione lub zmodyfikowane. Nie można wykonać migawki obiektu blob w magazynie archiwalnym. Jednak metadane obiektu blob pozostaje online i dostępne, co umożliwia wyświetlanie listy obiektów blob i jego właściwości. Dla obiektów blob w warstwie archiwum jedynymi prawidłowymi operacjami są GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier oraz DeleteBlob.

Przykładowe scenariusze użycia dotyczące warstwy dostępu do archiwum obejmują:

- Długoterminowe kopie zapasowe, dodatkowe kopie zapasowej i archiwalne zestawy danych
- Oryginalne (nieprzetworzone) dane, które muszą zostać zachowane, nawet po przetworzeniu ich do ostatecznej użytecznej postaci (*np.* nieprzetworzone pliki multimedialne po transkodowaniu do innych formatów).
- Dane zgodności i dane archiwalne, które muszą być przechowywane przez długi czas, i do których bardzo rzadko uzyskuje się dostęp (*Na przykład*, monitorujących, stare rentgenowskie-kamer/MRIs dla organizacji opieki zdrowotnej, nagrania audio i zapisy klient wywołuje dla usług finansowych)

### <a name="blob-rehydration"></a>Ponowne wypełnianie obiektów blob

Aby odczytać dane w magazynie archiwalnym, należy najpierw zmienić warstwę obiektu blob na gorącą lub chłodną. Proces ten, określany jako „ponowne wypełnianie” (ang. rehydration), może potrwać do 15 godzin. Dla uzyskania optymalnej wydajności zaleca się dużych rozmiarów obiektów blob. Ponowne wypełnianie kilku małych obiektów blob równocześnie może spowodować dalsze przedłużenie czasu.

Podczas ponownego wypełniania można sprawdzać właściwość obiektu blob **Stan archiwum**, aby upewnić się, że warstwa została zmieniona. Właściwość ta ma wartość „rehydrate-pending-to-hot” (ponowne wypełnianie w celu przejścia do warstwy gorącej) lub „rehydrate-pending-to-cool” (ponowne wypełnianie w celu przejścia do warstwy chłodnej) w zależności od warstwy docelowej. Po zakończeniu tego procesu właściwość obiektu blob „stan archiwum” jest usuwana, a wartość właściwości **Warstwa dostępu** odpowiada nowej warstwie Gorąca lub Chłodna.  

## <a name="blob-level-tiering"></a>Obsługa warstw na poziomie obiektów blob

Funkcja obsługi warstw na poziomie obiektów blob umożliwia zmianę warstwy danych na poziomie obiektu przy użyciu jednej operacji o nazwie [Ustawianie warstwy obiektu blob](/rest/api/storageservices/set-blob-tier). W odpowiedzi na zmiany wzorców użycia można łatwo zmieniać warstwy dostępu do obiektu blob (wybierając warstwę gorącą, chłodną lub archiwalną) bez przenoszenia danych między kontami. Wszystkie zmiany warstw natychmiast. Jednak ponownego wypełniania obiektu blob z archiwum może potrwać kilka godzin.

Czas ostatniej zmiany warstwy obiektu blob jest uwidaczniany za pomocą właściwości obiektu blob **Czas zmiany warstwy dostępu**. Jeśli obiekt blob znajduje się w warstwie archiwum, go nie można zastąpić, więc przekazanie tego samego obiektu blob nie jest dozwolona w tym scenariuszu. Może spowodować zastąpienie obiektu blob w gorącej lub chłodnej warstwy, w których przypadku nowy obiekt blob dziedziczy warstwę obiektu blob, który został zastąpiony.

Obiekty BLOB we wszystkich warstwach dostępu trzy mogą współistnieć w obrębie tego samego konta. Obiekt blob bez jawnie przypisanej warstwy korzysta z warstwy zgodnie z ustawieniem warstwy dostępu konta. Jeśli warstwa dostępu jest wnioskowana z konta, możesz zobaczyć **wywnioskowana Warstwa dostępu** obiektu blob właściwość ustawioną na wartość "true", a obiekt blob **warstwy dostępu** właściwość obiektu blob jest zgodna z warstwą konta. W witrynie Azure portal wywnioskowana Warstwa dostępu, właściwości jest wyświetlana z warstwą dostępu obiektu blob (na przykład **gorąca (wywnioskowana)** lub **chłodna (wywnioskowana)** ).

> [!NOTE]
> Magazyn Archiwum i funkcja obsługi warstw na poziomie obiektów blob obsługują tylko blokowe obiekty blob. Nie można także zmienić warstwy blokowego obiektu blob, który ma migawki.

> [!NOTE]
> Dane przechowywane na koncie magazynu blokowych obiektów blob nie może obecnie należeć do warstwy gorąca, chłodna lub archiwum, przy użyciu [Ustawianie warstwy obiektu Blob](/rest/api/storageservices/set-blob-tier) lub za pomocą zarządzania cyklem życia usługi Azure Blob Storage.
> W celu przeniesienia danych musisz synchronicznie skopiować obiekty BLOB z konta usługi block blob storage Warstwa dostępu gorąca przy użyciu innego konta [umieścić blok z adresu URL interfejsu API](/rest/api/storageservices/put-block-from-url) lub wersję narzędzia AzCopy, która obsługuje ten interfejs API.
> *Umieścić blok z adresu URL* API synchronicznie kopiuje dane na serwerze, co oznacza ukończenia wywołania tylko raz wszystkie dane są przenoszone z oryginalnej lokalizacji serwera do lokalizacji docelowej.

### <a name="blob-lifecycle-management"></a>Zarządzanie cyklem życia obiektów blob

Zarządzanie cyklem życia magazynu obiektów blob oferuje zaawansowane, oparte na regułach zasadę, która umożliwia przeniesienie danych do najlepszych warstwy dostępu i wygasanie danych na końcu jej cyklu projektowania. Zobacz [Zarządzanie cyklem życia magazynu obiektów Blob platformy Azure](storage-lifecycle-management-concepts.md) Aby dowiedzieć się więcej.  

### <a name="blob-level-tiering-billing"></a>Rozliczanie obsługi warstw na poziomie obiektów blob

Gdy obiekt blob jest przenoszony do chłodniejszej warstwy (gorąca -> chłodna, gorąca -> archiwum lub chłodna -> archiwum), operacja jest rozliczana jako operacja zapisu do warstwy docelowej, gdzie operacje zapisu (za 10 000 operacji) i opłaty za zapisu (za GB) danych w warstwie docelowej mają zastosowanie.

Gdy obiekt blob jest przenoszony do cieplejszej warstwy (archiwum -> chłodna, archiwum -> gorąca lub chłodna -> gorąca), operacja jest rozliczana jako odczyt z warstwy źródłowej, w których obowiązują operacje odczytu (za 10 000 operacji) i opłaty za pobieranie (za GB) danych z warstwy źródłowej. W poniższej tabeli przedstawiono, jak są rozliczane zmiany warstwy.

| | **Zapis opłaty (Operacja + dostępu)** | **Opłaty odczytu (Operacja + dostępu)**
| ---- | ----- | ----- |
| **SetBlobTier Direction** | gorąca -> chłodna, gorąca -> archiwum, chłodna -> archiwum | archiwum -> chłodna, archiwum -> gorąca, chłodna -> gorąca

W przypadku przełączania warstwy konta z Gorąca na Chłodna opłaty zostaną naliczone za operacje zapisu (za 10 000 operacji) dla wszystkich obiektów blob bez ustawionej warstwy tylko na kontach GPv2. Nie ma opłat dla tej zmiany w kontach magazynu obiektów Blob. Zostanie naliczona opłata za operacje odczytu (za 10 000 operacji) i pobieranie danych (za GB), jeśli przełączasz konta magazynu usługi Blob Storage lub GPv2 z warstwy Chłodna na Gorąca. Mogą również obowiązywać opłaty za wczesne usunięcie dla dowolnego obiektu przeniesionego z warstwy Chłodna lub Archiwum.

### <a name="cool-and-archive-early-deletion"></a>Opłaty za wcześniejsze usunięcie w warstwach Chłodna i Archiwum

Oprócz opłaty miesięcznej za GB, każdy obiekt blob przenoszony do warstwy Chłodna (tylko konta GPv2) jest objęty okresem wcześniejszego usunięcia z warstwy Chłodna przez 30 dni, a każdy obiekt przenoszony do warstwy Archiwum jest objęty okresem wcześniejszego usunięcia z warstwy Chłodna przez 180 dni. Ta opłata jest naliczana proporcjonalnie. Jeśli na przykład obiekt blob zostanie przeniesiony do warstwy Archiwum, a następnie usunięty lub przeniesiony do warstwy Gorąca po 45 dniach, zostanie naliczona opłata za wczesne usunięcie odpowiadająca 135 (180 minus 45) dniom przechowywania tego obiektu blob w archiwum.

Wcześniejsze usunięcie może obliczyć przy użyciu właściwość obiektu blob **czas utworzenia**, jeśli wystąpił brak dostępu do zmiany warstwy. W przeciwnym razie można skorzystać w przypadku warstwy dostępu do ostatniej modyfikacji się do warstwy chłodna lub archiwalna, wyświetlając właściwość obiektu blob: **Zmień czas, dostęp do warstwy w-** . Aby uzyskać więcej informacji na temat właściwości obiektu blob, zobacz [uzyskać właściwości obiektu Blob](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Porównanie opcji magazynu obiektów blob bloku

Poniższej tabeli przedstawiono porównanie magazynu premium wydajności block blob storage i gorąca, chłodna i archiwum warstwy dostępu.

|                                           | **Wydajność warstwy Premium** | **Warstwy gorąca i używanie** | **Warstwa chłodna** | **Warstwę archiwum**
| ----------------------------------------- | ---------------- | ------------ | ----- | ----- |
| **Dostępność**                          | 99,9%            | 99,9%        | 99% | ND |
| **Dostępność** <br> **(odczyty RA-GRS)**  | ND              | 99,99%       | 99,9% | ND |
| **Opłaty za użycie**                         | Wyższe koszty magazynowania, niższe dostępu i koszt transakcji | Wyższe koszty magazynowania, niższe, koszty dostępu i transakcji | Niższe koszty magazynowania, wyższe, koszty dostępu i transakcji | Najniższe koszty magazynowania, najwyższy, koszty dostępu i transakcji |
| **Minimalny rozmiar obiektu**                   | ND | ND | ND | ND |
| **Minimalny czas magazynowania**              | ND | ND | 30 dni (tylko GPv2) | 180 dni
| **Opóźnienie** <br> **(czas do pierwszego bajtu)** | Oznaczona jedną cyfrą MS | milisekundy | milisekundy | mniej niż 15 godz.

> [!NOTE]
> Aby uzyskać cele dotyczące skalowalności i wydajności, zobacz [cele dotyczące skalowalności i wydajności usługi Azure Storage dla kont magazynu](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="quickstart-scenarios"></a>Scenariusze typu Szybki start

W tej sekcji przedstawiono następujące scenariusze obejmujące użycie witryny Azure Portal:

- Jak zmienić domyślną warstwę dostępu konta GPv2 lub usługi Blob Storage.
- Jak zmienić warstwę obiektu blob na koncie GPv2 lub usługi Blob Storage.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Zmienianie domyślnej warstwy dostępu konta GPv2 lub usługi Blob Storage

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Aby przejść do konta magazynu, zaznacz pozycję Wszystkie zasoby, a następnie wybierz konto magazynu.

1. W bloku Ustawienia kliknij pozycję **Konfiguracja**, aby wyświetlić i/lub zmienić konfigurację konta.

1. Wybierz dla Twoich potrzeb warstwę dostępu: Ustaw **warstwy dostępu** do jednej **chłodna** lub **gorąca**.

1. Kliknij pozycję **Zapisz** w górnej części bloku.

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Dokonywanie zmiany warstwy obiektu blob na koncie GPv2 lub usługi Blob storage

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Aby przejść do obiektu blob na koncie magazynu, wybierz pozycję **wszystkie zasoby**, wybierz konto magazynu, wybierz kontener, a następnie wybierz obiekt blob.

1. W **właściwości obiektu Blob** bloku wybierz **warstwy dostępu** menu rozwijane, aby wybrać **gorąca**, **chłodna**, lub **archiwum**  warstwy dostępu.

1. Kliknij pozycję **Zapisz** w górnej części bloku.

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Wszystkie konta magazynu używają modelu cenowego dla magazynu obiektów Blob opartego na warstwie każdego obiektu blob. Mieć na uwadze następujące zagadnienia dotyczące rozliczeń:

- **Koszty magazynowania**: Oprócz ilości przechowywanych danych koszt przechowywania danych różni się w zależności od warstwy dostępu. Koszt za gigabajt zmniejsza się w miarę, jak warstwa staje się chłodniejsza.
- **Koszty dostępu do danych**: Dostęp do danych opłat wzrostu, jak warstwa staje się chłodniejsza. W przypadku danych w warstwie dostępu chłodna i archiwum jest naliczana opłata dostępu do danych za każdy gigabajt dla operacji odczytu.
- **Koszty transakcji**: Brak opłat każdej transakcji dla wszystkich warstw, która zwiększa się, jak warstwa staje się chłodniejsza.
- **Koszty transferu danych replikacji geograficznej**: Ta opłata dotyczy tylko kont ze skonfigurowaną replikacją geograficzną, w tym GRS i RA-GRS. Transfer danych w ramach replikacji geograficznej powoduje naliczanie opłaty za każdy gigabajt.
- **Koszty transferu danych wychodzących**: Wychodzące transfery danych (dane przesyłane poza region platformy Azure) Naliczanie opłat za zużycie przepustowości za każdy gigabajt, co jest spójne z kontami magazynu ogólnego przeznaczenia.
- **Zmiana warstwy dostępu**: Zmiana warstwy dostępu konta z chłodna na gorąca spowoduje naliczenie opłaty równej odczytanie wszystkich danych na koncie magazynu. Jednak zmiana warstwy dostępu konta z gorąca na chłodna spowoduje naliczenie opłaty równej zapisanie wszystkich danych w warstwie chłodna (tylko konta GPv2).

> [!NOTE]
> Aby uzyskać więcej informacji na temat cen kont usługi Blob storage, zobacz [cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/) strony. Więcej informacji dotyczących opłat za transfer danych wychodzących można znaleźć na stronie [Szczegóły cennika transferów danych](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="faq"></a>Często zadawane pytania

**Czy używać konta usługi Blob Storage, czy GPv2, jeśli chcę obsługiwać warstwy danych?**

Zalecamy korzystanie z kont GPv2 zamiast kont usługi Blob Storage na potrzeby obsługi warstw. Konta GPv2 obsługują wszystkie funkcje, które obsługują konta usługi Blob Storage, i wiele innych. Ceny kont usługi Blob Storage i GPv2 są niemal identyczne, ale niektóre nowe funkcje i rabaty cenowe będą dostępne tylko na kontach GPv2. Konta GPv1 nie obsługują warstw.

Struktury cen kont GPv1 i GPv2 są różne, a klienci powinni dokładnie je sprawdzić przed podjęciem decyzji o użyciu kont GPv2. Istniejące konto usługi Blob Storage lub GPv1 można łatwo przekonwertować na konto GPv2 w ramach prostego procesu uruchamianego jednym kliknięciem. Aby uzyskać więcej informacji, zobacz [Omówienie konta magazynu platformy Azure](../common/storage-account-overview.md).

**Czy mogę przechowywać obiekty we wszystkich trzech (gorąca, chłodna i archiwum) dostęp do warstwy na tym samym koncie?**

Tak. Atrybut **Warstwa dostępu** ustawiany na poziomie konta to domyślna warstwa mająca zastosowanie do wszystkich obiektów na tym koncie bez jawnie ustawionej warstwy. Jednak obsługa warstw na poziomie obiektów blob umożliwia ustawienie warstwy dostępu na poziomie obiektu niezależnie od ustawienia warstwy dostępu konta. Obiekty BLOB we wszystkich warstwach trzy dostępu (gorąca, chłodna lub archiwum) mogą istnieć w ramach tego samego konta.

**Czy można zmienić domyślną warstwę dostępu konta magazynu blob Storage lub GPv2?**

Tak, możesz zmienić domyślną warstwę dostępu przez ustawienie **warstwy dostępu** atrybutu na koncie magazynu. Zmiana warstwy dostępu ma zastosowanie do wszystkich obiektów przechowywanych na koncie, które nie mają jawnie ustawionej warstwy. Zmiana warstwy dostępu z gorąca na chłodna spowoduje naliczenie operacje zapisu (za 10 000 operacji) dla wszystkich obiektów blob bez ustawionej warstwy tylko na kontach GPv2 i zmiana warstwy z chłodna na gorąca spowoduje naliczenie operacje odczytu (za 10 000 operacji) i pobieranie danych (za GB), opłaty za wszystkie obiekty BLOB w magazynie obiektów Blob i gpv2.

**Czy mogę ustawić domyślną warstwę dostępu do konta na Archiwum?**

Nie. Tylko warstwy dostępu gorąca i chłodna można ustawić jako domyślną warstwę dostępu konta. Archiwum można ustawić tylko na poziomie obiektu.

**W którym regionach są gorąca, chłodna i archiwum dostępne warstwy dostępu?**

Warstwy dostępu gorąca i chłodna oraz obsługa warstw na poziomie obiektów blob są dostępne we wszystkich regionach. Magazyn w warstwie Archiwum będzie początkowo dostępny tylko w wybranych regionach. Pełną listę można znaleźć w temacie [Dostępność produktów platformy Azure według regionów](https://azure.microsoft.com/regions/services/).

**Czy obiekty BLOB w warstwie dostępu chłodna będą działały inaczej niż w warstwie dostępu gorąca?**

Obiekty BLOB w warstwie dostępu gorąca mają takie samo opóźnienie jak obiekty BLOB na kontach magazynu GPv1, GPv2 i Blob. Obiekty BLOB w warstwie dostępu chłodna mają podobne opóźnienie (w milisekundach) jak obiekty BLOB na kontach magazynu GPv1, GPv2 i Blob. Obiekty BLOB w warstwie dostępu do archiwum mają kilka godzin opóźnienia na kontach magazynu GPv1, GPv2 i Blob.

Obiekty BLOB w warstwie dostępu chłodna miały nieco niższy poziom dostępności usług (SLA) niż obiekty BLOB przechowywane w ramach warstwy dostępu gorąca. Aby uzyskać więcej informacji, zobacz [Magazyn — umowa SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**Czy operacje w warstwach Gorąca, Chłodna i Archiwum są takie same?**

Tak. Wszystkie operacje w warstwach Gorąca i Chłodna są w 100% spójne. Wszystkie prawidłowe operacje w warstwie Archiwum, w tym usuwanie, tworzenie listy, pobieranie właściwości/metadanych obiektów blob i ustawianie warstwy obiektu blob, są w 100% spójne z warstwami Gorąca i Chłodna. Obiektu blob znajdującego się w warstwie Archiwum nie można odczytywać ani modyfikować.

**Kiedy podczas ponownego wypełniania obiektu blob z warstwy Archiwum do warstwy Gorąca lub Chłodna będę wiedzieć, że ten proces został ukończony?**

Podczas ponownego wypełniania można używać operacji pobierania właściwości obiektu blob, aby sondować atrybut **Stan archiwum** w celu potwierdzenia ukończenia zmiany warstwy. Właściwość ta ma wartość „rehydrate-pending-to-hot” (ponowne wypełnianie w celu przejścia do warstwy gorącej) lub „rehydrate-pending-to-cool” (ponowne wypełnianie w celu przejścia do warstwy chłodnej) w zależności od warstwy docelowej. Po zakończeniu tego procesu właściwość obiektu blob „stan archiwum” jest usuwana, a wartość właściwości **Warstwa dostępu** odpowiada nowej warstwie Gorąca lub Chłodna.  

**Kiedy po ustawieniu warstwy obiektu blob rozpocznie się naliczanie opłat przy użyciu odpowiedniej stawki?**

Każdy obiekt blob jest zawsze rozliczane zgodnie z warstwą określoną przez obiektu blob **warstwy dostępu** właściwości. Podczas ustawiania nowej warstwy obiektu blob, **warstwy dostępu** właściwość odzwierciedla natychmiast nową warstwę dla wszystkich przejść. Jednak ponowne wypełnienie obiektu blob z warstwy Archiwum w warstwie Gorąca lub Chłodna może potrwać kilka godzin. W takich przypadkach stosowana jest stawka przy użyciu stawek warstwy archiwum do czasu ukończenia, w tym momencie ponownego wypełniania **warstwy dostępu** właściwość odzwierciedla nową warstwę. W tym momencie są rozliczane dla tego obiektu blob zgodnie ze stawką gorąca lub chłodna.

**Jak mogę ustalić, czy będę płacić za wczesne usunięcie w przypadku usunięcia lub przeniesienia obiektu blob z warstwy Chłodna lub Archiwum?**

Proporcjonalne opłaty za wczesne usunięcie będą dotyczyć wszystkich obiektów blob usuniętych lub przeniesionych z warstwy Chłodna (tylko konta GPv2) lub Archiwum przed upływem odpowiednio 30 lub 180 dni. Można określić, jak długo obiekt blob został w warstwie chłodna lub archiwum, sprawdzając **czas zmiany warstwy dostępu** właściwość, która udostępnia znacznik ostatniej zmiany warstwy obiektu blob. Aby uzyskać więcej informacji, zobacz [wcześniejsze usunięcie w warstwach chłodna i archiwum](#cool-and-archive-early-deletion).

**Które narzędzia i zestawy SDK platformy Azure obsługują warstwy na poziomie obiektów blob i magazyn w warstwie Archiwum?**

Następujące rozwiązania: witryna Azure Portal, program PowerShell, narzędzia interfejsu wiersza polecenia oraz biblioteki klienta środowisk .NET, Java, Python i Node.js obsługują warstwy na poziomie obiektów blob i magazyn w warstwie Archiwum.  

**Ile danych można przechowywać w warstwach Gorąca, Chłodna i Archiwum?**

Magazyn danych, wraz z innych ograniczeń są ustawiane na poziomie konta, a nie na warstwę dostępu. W związku z tym można korzystać ze wszystkich limitu w jednej warstwy lub we wszystkich trzech warstwach. Aby uzyskać więcej informacji, zobacz [cele dotyczące skalowalności i wydajności usługi Azure Storage](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Kolejne kroki

### <a name="evaluate-hot-cool-and-archive-in-gpv2-blob-storage-accounts"></a>Ocena warstw Gorąca, Chłodna i Archiwum na kontach GPv2 i usługi Blob Storage

[Sprawdzanie dostępności warstw Gorąca, Chłodna i Archiwum według regionu](https://azure.microsoft.com/regions/#services)

[Zarządzanie cyklem życia magazynu obiektów Blob platformy Azure](storage-lifecycle-management-concepts.md)

[Ocena użycia bieżących kont magazynu przez włączenie metryk usługi Azure Storage](../common/storage-enable-and-view-metrics.md)

[Sprawdzanie cen warstw Gorąca, Chłodna i Archiwum na kontach usługi Blob Storage i GPv2 według regionu](https://azure.microsoft.com/pricing/details/storage/)

[Sprawdzanie ceny transferu danych](https://azure.microsoft.com/pricing/details/data-transfers/)
