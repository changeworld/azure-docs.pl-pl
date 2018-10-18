---
title: Premium, gorąca, chłodna i archiwalna magazynu obiektów blob — usługi Azure Storage
description: — Wersja Premium, gorąca, chłodna i archiwalny magazyn dla kont usługi Azure storage.
services: storage
author: kuhussai
ms.service: storage
ms.topic: article
ms.date: 09/11/2018
ms.author: kuhussai
ms.component: blobs
ms.openlocfilehash: 922e7ed5d55f50b2069dad71ead73d9ef4475ed0
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49389908"
---
# <a name="azure-blob-storage-premium-preview-hot-cool-and-archive-storage-tiers"></a>Usługa Azure Blob storage: Premium (wersja zapoznawcza), gorąca, chłodna i archiwalna magazynu

## <a name="overview"></a>Przegląd

Usługa Azure storage oferuje warstw innego magazynu, które umożliwiają przechowywanie danych obiektów Blob w sposób najbardziej efektywny. Dostępne warstwy obejmują:

- **Usługa Premium storage (wersja zapoznawcza)** zapewnia wysoką wydajność sprzętu dla danych, z których korzysta się często.
 
- **Gorąco magazynu**: jest zoptymalizowana pod kątem magazynowania danych, z których korzysta się często. 

- **Chłodna magazynu** została zoptymalizowana pod kątem magazynowania danych, które rzadko uzyskuje dostęp i przechowywane przez co najmniej 30 dni.
 
- **Magazyn archiwum** została zoptymalizowana pod kątem magazynowania danych, które rzadko uzyskuje dostęp i przechowywane przez co najmniej 180 dni elastyczne wymagania dotyczące opóźnień (rzędu kilku godzin).

Następujące zagadnienia dotyczące towarzyszyć warstw innego magazynu:

- Warstwa magazynowania Archiwum jest dostępna tylko na poziomie obiektu blob. Nie jest ona dostępna na poziomie konta magazynu.
 
- Dla danych w warstwie magazynu chłodnego nie ma znaczenia nieco niższa dostępność, ale nadal są wymagane wysoka trwałość oraz podobny czas dostępu i parametry przepływności jak w przypadku gorących danych. W przypadku chłodnych danych umowa SLA zapewniająca nieco niższą dostępność i wyższe koszty dostępu w porównaniu z gorącymi danymi to dopuszczalny kompromis w celu uzyskania niższych kosztów magazynowania.

- Magazyn Archiwum działa w trybie offline i ma najniższe koszty magazynowania, ale także najwyższe koszty dostępu.
 
- Na poziomie konta można ustawić tylko warstwy magazynu Gorąca i Chłodna (nie Archiwum).
 
- Wszystkie warstwy można ustawić na poziomie obiektu.

Dane przechowywane w chmurze rośnie w tempie wykładniczym. Aby zarządzać kosztami zwiększających się potrzeb dotyczących magazynowania, warto zorganizować dane na podstawie atrybutów, takich jak częstotliwość dostępu i planowany okres przechowywania. Pozwoli to na optymalizację kosztów. Dane przechowywane w chmurze mogą być różne pod względem sposobu ich generowania i przetwarzania oraz uzyskiwania do nich dostępu przez cały okres ich istnienia. Do niektórych danych często uzyskuje się dostęp. Są one również często modyfikowane w trakcie całego okresu istnienia. Do niektórych danych często uzyskuje się dostęp na początkowym etapie istnienia, a z czasem już zdecydowanie rzadziej. Niektóre dane pozostają nieużywane w chmurze i dostęp do nich uzyskuje się rzadko (lub w ogóle) po umieszczeniu ich w magazynie.

W przypadku każdego z tych scenariuszy dostępu do danych istnieją korzyści płynące z różnych warstw magazynowania, zoptymalizowanych pod kątem określonego wzorca dostępu. Dzięki warstwom magazynowania Gorąca, Chłodna i Archiwum usługa Azure Blob Storage zaspokaja potrzebę korzystania ze zróżnicowanych warstw magazynowania z oddzielnymi modelami cenowymi.

## <a name="storage-accounts-that-support-tiering"></a>Konta magazynu z obsługą warstw

Dane magazynu obiektów można dzielić między warstwy Gorąca, Chłodna lub Archiwum tylko na kontach usługi Blob Storage lub ogólnego przeznaczenia w wersji 2 (GPv2). Konta ogólnego przeznaczenia w wersji 1 (GPv1) nie obsługują warstw. Klienci mogą jednak łatwo przekształcać istniejące konta GPv1 lub konta usługi Blob Storage w konta GPv2 w ramach prostego procesu uruchamianego jednym kliknięciem w witrynie Azure Portal. Konto GPv2 oferuje nową strukturę cen obiektów blob, plików i kolejek oraz dostęp do różnych nowych funkcji magazynu. Co więcej, w przyszłości niektóre nowe funkcje i rabaty cenowe będą oferowane tylko w przypadku kont GPv2. W związku z tym klienci powinni zastanowić się nad korzystaniem z konta GPv2, ale podjąć taką decyzję mogą tylko po zapoznaniu się z cenami wszystkich usług, ponieważ niektóre obciążenia mogą być droższe na kontach GPv2 niż na kontach GPv1. Aby uzyskać więcej informacji, zobacz [Omówienie konta magazynu platformy Azure](../common/storage-account-overview.md).

Konta usługi Blob Storage i GPv2 ujawniają atrybut **Warstwa dostępu** na poziomie konta, co pozwala na określanie domyślnej warstwy magazynowania jako Gorąca lub Chłodna dla dowolnego obiektu blob na koncie magazynu, które nie ma warstwy ustawionej na poziomie obiektu. W przypadku obiektów z warstwą ustawioną na poziomie obiektu warstwa konta nie będzie stosowana. Warstwę Archiwum można stosować tylko na poziomie obiektu. W dowolnym momencie można przełączać się między tymi warstwami magazynowania.

## <a name="premium-access-tier"></a>Warstwa dostępu — wersja Premium

Dostępna w wersji zapoznawczej jest warstwa dostępu — wersja Premium, co sprawia, że często używane dane dostępne za pośrednictwem sprzętu wysokiej wydajności. Dane przechowywane w tej warstwie znajduje się na dyskach SSD, które są zoptymalizowane pod kątem mniejsze opóźnienie większe transakcyjnych w porównaniu do tradycyjnych dysków twardych. Warstwa dostępu — wersja Premium jest dostępna za pośrednictwem typu konta magazynu blokowych obiektów Blob tylko.

Ta warstwa jest idealny dla obciążeń wymagających czasy reakcji szybkie i spójne. Dane, które obejmuje użytkowników końcowych, takich jak interaktywne wideo edycji statyczne zawartość sieci web, transakcji w trybie online i jak dobrze nadają się dla warstwy dostępu do wersji Premium. Ta warstwa jest przeznaczony dla obciążeń, które wykonują wiele małych transakcji, takich jak przechwytywanie danych telemetrycznych, wiadomości błyskawiczne i transformacji danych.

Aby korzystać z tej warstwy aprowizować nowe konto magazynu blokowych obiektów Blob i rozpocząć tworzenie kontenerów i obiektów blob za pomocą [interfejsu API REST usługi Blob](/rest/api/storageservices/blob-service-rest-api), [AzCopy](/azure/storage/common/storage-use-azcopy), lub [Eksploratora usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

W trakcie okresu zapoznawczego warstwy dostępu do usługi Premium:

- Jest dostępna jako magazyn lokalnie nadmiarowy (LRS)
- Jest dostępna tylko w następujących regionach: wschodnie stany USA 2, środkowe stany USA i zachodnie stany USA
- Nie obsługuje automatycznego warstw i zarządzanie cyklem życia danych

Aby dowiedzieć się, jak zarejestrować się w wersji zapoznawczej z warstwy Premium dostępu, zobacz [wprowadzenie do magazynu obiektów Blob platformy Azure — wersja Premium](http://aka.ms/premiumblob).

## <a name="hot-access-tier"></a>Warstwa dostępu Gorąca

Koszty magazynowania w warstwie Gorąca są wyższe niż w warstwach Chłodna i Archiwum, ale ma ona najniższe koszty dostępu. Przykładowe scenariusze użycia dotyczące warstwy magazynu gorącego obejmują:

* Dane, które są często używane lub przewiduje się do nich częsty dostęp (odczyt i zapis danych).
* Dane, które są przygotowywane do przetwarzania i ewentualnej migracji do warstwy magazynu chłodnego.

## <a name="cool-access-tier"></a>Warstwa dostępu Chłodna

Warstwa magazynowania Chłodna ma niższe koszty magazynowania i wyższe koszty dostępu w porównaniu do warstwy Gorąca. Ta warstwa jest przeznaczona dla danych, które pozostaną w warstwie Chłodna przez co najmniej 30 dni. Przykładowe scenariusze użycia dotyczące warstwy magazynu chłodnego obejmują:

* Krótkoterminowe kopie zapasowe i zestawy danych odzyskiwania po awarii.
* Starszą zawartość nośników, która nie jest już często wyświetlana, ale oczekiwane jest, że będzie ona natychmiast dostępna, gdy będzie to wymagane.
* Duże zbiory danych, które muszą być przechowywane w sposób ekonomiczny, podczas gdy na potrzeby przyszłego przetwarzania zbierana jest większa ilość danych (*np.* długoterminowe magazynowanie danych naukowych lub nieprzetworzonych danych telemetrycznych z zakładu produkcyjnego).

## <a name="archive-access-tier"></a>Warstwa dostępu Archiwum

W warstwie Archiwum koszt przechowywania jest najniższy, a koszt pobierania danych jest wyższy niż w przypadku magazynu w warstwie Gorąca i Chłodna. Ta warstwa jest przeznaczona dla danych, które można pobierać z opóźnieniem kilku godzin i które pozostaną w warstwie Archiwum przez co najmniej 180 dni.

Gdy obiekt blob znajduje się w magazynie w warstwie Archiwum, jest on w trybie offline i nie można go odczytać (z wyjątkiem metadanych, które są w trybie online i dostępne), skopiować, zastąpić ani zmodyfikować. Nie można również tworzyć migawek takiego obiektu blob. Można jednak używać istniejących operacji do usuwania lub wyświetlania listy obiektów blob, pobierania ich właściwości/metadanych albo zmiany warstwy obiektu blob.

Przykładowe scenariusze użycia archiwalnej warstwy magazynowania obejmują:

* Długoterminowe kopie zapasowe, dodatkowe kopie zapasowej i archiwalne zestawy danych
* Oryginalne (nieprzetworzone) dane, które muszą zostać zachowane, nawet po przetworzeniu ich do ostatecznej użytecznej postaci (*np.* nieprzetworzone pliki multimedialne po transkodowaniu do innych formatów).
* Dane zgodności i dane archiwalne, które muszą być przechowywane przez długi czas, i do których bardzo rzadko uzyskuje się dostęp (*np.* zapisy z kamer monitorujących, stare zdjęcia rentgenowskie lub zdjęcia z rezonansu magnetycznego dla organizacji opieki zdrowotnej, nagrania audio i zapisy rozmów telefonicznych z klientami dla firm z branży usług finansowych).

### <a name="blob-rehydration"></a>Ponowne wypełnianie obiektów blob
Aby odczytać dane w magazynie archiwalnym, należy najpierw zmienić warstwę obiektu blob na gorącą lub chłodną. Proces ten, określany jako „ponowne wypełnianie” (ang. rehydration), może potrwać do 15 godzin. Dla uzyskania optymalnej wydajności zaleca się dużych rozmiarów obiektów blob. Ponowne wypełnianie kilku małych obiektów blob równocześnie może spowodować dalsze przedłużenie czasu.

Podczas ponownego wypełniania można sprawdzać właściwość obiektu blob **Stan archiwum**, aby upewnić się, że warstwa została zmieniona. Właściwość ta ma wartość „rehydrate-pending-to-hot” (ponowne wypełnianie w celu przejścia do warstwy gorącej) lub „rehydrate-pending-to-cool” (ponowne wypełnianie w celu przejścia do warstwy chłodnej) w zależności od warstwy docelowej. Po zakończeniu tego procesu właściwość obiektu blob „stan archiwum” jest usuwana, a wartość właściwości **Warstwa dostępu** odpowiada nowej warstwie Gorąca lub Chłodna.  

## <a name="blob-level-tiering"></a>Obsługa warstw na poziomie obiektów blob

Funkcja obsługi warstw na poziomie obiektów blob umożliwia zmianę warstwy danych na poziomie obiektu przy użyciu jednej operacji o nazwie [Ustawianie warstwy obiektu blob](/rest/api/storageservices/set-blob-tier). W odpowiedzi na zmiany wzorców użycia można łatwo zmieniać warstwy dostępu do obiektu blob (wybierając warstwę gorącą, chłodną lub archiwalną) bez przenoszenia danych między kontami. Wszystkie zmiany warstw natychmiast. Jednak ponownego wypełniania obiektu blob z archiwum może potrwać kilka godzin. 

Czas ostatniej zmiany warstwy obiektu blob jest uwidaczniany za pomocą właściwości obiektu blob **Czas zmiany warstwy dostępu**. Jeśli obiekt blob znajduje się w warstwie archiwum, go nie można zastąpić, więc przekazanie tego samego obiektu blob nie jest dozwolona w tym scenariuszu. Może spowodować zastąpienie obiektu blob w gorącej lub chłodnej warstwy, w których przypadku nowy obiekt blob dziedziczy warstwę obiektu blob, który został zastąpiony.

W obrębie jednego konta jest możliwe współistnienie obiektów blob należących do wszystkich trzech warstw magazynowania. Obiekt blob bez jawnie przypisanej warstwy korzysta z warstwy zgodnie z ustawieniem warstwy dostępu konta. Jeśli warstwa dostępu jest wnioskowana z konta, możesz zobaczyć **wywnioskowana Warstwa dostępu** obiektu blob właściwość ustawioną na wartość "true", a obiekt blob **warstwy dostępu** właściwość obiektu blob jest zgodna z warstwą konta. W witrynie Azure Portal właściwość Wywnioskowana warstwa dostępu jest wyświetlana z warstwą dostępu obiektu blob, np. Gorąca (wywnioskowana) lub Chłodna (wywnioskowana).

> [!NOTE]
> Magazyn Archiwum i funkcja obsługi warstw na poziomie obiektów blob obsługują tylko blokowe obiekty blob. Nie można także zmienić warstwy blokowego obiektu blob, który ma migawki.

Dane przechowywane w warstwie dostępu do wersji Premium nie warstwowe gorąca, chłodna lub archiwum, przy użyciu [Ustawianie warstwy obiektu Blob](/rest/api/storageservices/set-blob-tier) lub za pomocą zarządzania cyklem życia usługi Azure Blob Storage. W celu przeniesienia danych należy synchronicznie skopiować obiekty BLOB z dostępu Premium do korzystania z gorącej [umieścić blok z adresu URL interfejsu API](/rest/api/storageservices/put-block-from-url) lub wersję narzędzia AzCopy, która obsługuje ten interfejs API. *Umieścić blok z adresu URL* API synchronicznie kopiuje dane na serwerze, co oznacza ukończenia wywołania tylko raz wszystkie dane są przenoszone z oryginalnej lokalizacji serwera do lokalizacji docelowej.

### <a name="blob-lifecycle-management"></a>Zarządzanie cyklem życia obiektów blob
Zarządzanie cyklem życia magazynu obiektów blob (wersja zapoznawcza) oferuje zaawansowane, oparte na regułach zasadę, która umożliwia przeniesienie danych do najlepszych warstwy dostępu i wygasanie danych na końcu jej cyklu projektowania. Zobacz [Zarządzanie cyklem życia magazynu obiektów Blob platformy Azure](https://docs.microsoft.com/azure/storage/common/storage-lifecycle-managment-concepts) Aby dowiedzieć się więcej.  

### <a name="blob-level-tiering-billing"></a>Rozliczanie obsługi warstw na poziomie obiektów blob

Gdy obiekt blob jest przenoszony do chłodniejszej warstwy (gorąca -> chłodna, gorąca -> archiwum lub chłodna -> archiwum), operacja jest rozliczana jako operacja zapisu do warstwy docelowej, gdzie operacje zapisu (za 10 000 operacji) i opłaty za zapisu (za GB) danych w warstwie docelowej mają zastosowanie. Gdy obiekt blob jest przenoszony do cieplejszej warstwy (Archiwum -> Chłodna, Archiwum -> Gorąca lub Chłodna -> Gorąca), operacja jest rozliczana jako odczyt z warstwy źródłowej i naliczane są opłaty za operacje odczytu (za 10 000 operacji) i za pobieranie danych (za GB) zgodnie z cennikiem warstwy źródłowej.

W przypadku przełączania warstwy konta z Gorąca na Chłodna opłaty zostaną naliczone za operacje zapisu (za 10 000 operacji) dla wszystkich obiektów blob bez ustawionej warstwy tylko na kontach GPv2. Nie ma opłat dla tej zmiany w kontach magazynu obiektów Blob. Zostanie naliczona opłata za operacje odczytu (za 10 000 operacji) i pobieranie danych (za GB), jeśli przełączasz konta magazynu usługi Blob Storage lub GPv2 z warstwy Chłodna na Gorąca. Mogą również obowiązywać opłaty za wczesne usunięcie dla dowolnego obiektu przeniesionego z warstwy Chłodna lub Archiwum.

### <a name="cool-and-archive-early-deletion"></a>Opłaty za wcześniejsze usunięcie w warstwach Chłodna i Archiwum

Oprócz opłaty miesięcznej za GB, każdy obiekt blob przenoszony do warstwy Chłodna (tylko konta GPv2) jest objęty okresem wcześniejszego usunięcia z warstwy Chłodna przez 30 dni, a każdy obiekt przenoszony do warstwy Archiwum jest objęty okresem wcześniejszego usunięcia z warstwy Chłodna przez 180 dni. Ta opłata jest naliczana proporcjonalnie. Jeśli na przykład obiekt blob zostanie przeniesiony do warstwy Archiwum, a następnie usunięty lub przeniesiony do warstwy Gorąca po 45 dniach, zostanie naliczona opłata za wczesne usunięcie odpowiadająca 135 (180 minus 45) dniom przechowywania tego obiektu blob w archiwum.

## <a name="comparison-of-the-storage-tiers"></a>Porównanie warstw magazynowania

W poniższej tabeli przedstawiono porównanie warstw magazynowania Gorąca, Chłodna i Archiwum.

| | **Warstwa magazynu gorącego** | **Warstwa magazynu chłodnego** | **Warstwa magazynowania Archiwum**
| ---- | ----- | ----- | ----- |
| **Dostępność** | 99,9% | 99% | ND |
| **Dostępność** <br> **(odczyty RA-GRS)**| 99,99% | 99,9% | ND |
| **Opłaty za użycie** | Wyższe koszty magazynowania, niższe koszty dostępu i transakcji | Niższe koszty magazynowania, wyższe koszty dostępu i transakcji | Najniższe koszty magazynowania, najwyższe koszty dostępu i transakcji |
| **Minimalny rozmiar obiektu** | ND | ND | ND |
| **Minimalny czas magazynowania** | ND | 30 dni (tylko GPv2) | 180 dni
| **Opóźnienie** <br> **(czas do pierwszego bajtu)** | milisekundy | milisekundy | mniej niż 15 godz.
| **Cele dotyczące skalowalności i wydajności** | Takie same jak w przypadku kont magazynu ogólnego przeznaczenia | Takie same jak w przypadku kont magazynu ogólnego przeznaczenia | Takie same jak w przypadku kont magazynu ogólnego przeznaczenia |

> [!NOTE]
> Konta Magazynu obiektów blob obsługują te same cele wydajności i skalowalności co konta magazynu ogólnego przeznaczenia. Aby uzyskać więcej informacji, zobacz [Azure Storage Scalability and Performance Targets](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Cele dotyczące skalowalności i wydajności usługi Magazyn Azure).

## <a name="quickstart-scenarios"></a>Scenariusze typu Szybki start

W tej sekcji przedstawiono następujące scenariusze obejmujące użycie witryny Azure Portal:

* Jak zmienić domyślną warstwę dostępu konta GPv2 lub usługi Blob Storage.
* Jak zmienić warstwę obiektu blob na koncie GPv2 lub usługi Blob Storage.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Zmienianie domyślnej warstwy dostępu konta GPv2 lub usługi Blob Storage

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Aby przejść do konta magazynu, zaznacz pozycję Wszystkie zasoby, a następnie wybierz konto magazynu.

3. W bloku Ustawienia kliknij pozycję **Konfiguracja**, aby wyświetlić i/lub zmienić konfigurację konta.

4. Wybierz odpowiednią dla Twoich potrzeb warstwę magazynu: ustaw pozycję **Warstwa dostępu** na wartość **Chłodna** lub **Gorąca**.

5. Kliknij pozycję Zapisz w górnej części bloku.

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Zmień warstwę obiektu blob na koncie GPv2 lub usługi Blob Storage.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Aby przejść do obiektu blob na koncie magazynu, wybierz pozycję Wszystkie zasoby, wybierz konto magazynu, wybierz kontener, a następnie wybierz obiekt blob.

3. W bloku Właściwości obiektu blob kliknij menu rozwijane **Warstwa dostępu**, aby wybrać warstwę magazynowania **Gorąca**, **Chłodna** lub **Archiwum**.

5. Kliknij pozycję Zapisz w górnej części bloku.

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Wszystkie konta magazynu używają modelu cenowego dla magazynu obiektów Blob opartego na warstwie każdego obiektu blob. Mieć na uwadze następujące zagadnienia dotyczące rozliczeń:

* **Koszty usługi Storage**: oprócz ilości przechowywanych danych koszt przechowywania danych różni się w zależności od warstwy magazynowania. Koszt za gigabajt zmniejsza się w miarę, jak warstwa staje się chłodniejsza.
* **Koszty dostępu do danych**: opłaty za dostęp do danych wzrastają w miarę, jak warstwa staje się chłodniejsza. W przypadku danych w warstwie magazynowania Chłodna i Archiwum naliczana jest opłata za dostęp do danych za każdy gigabajt dla operacji odczytu.
* **Koszty transakcji**: w przypadku wszystkich warstw naliczana jest opłata za transakcję, która wzrasta w miarę, jak warstwa staje się chłodniejsza.
* **Koszty transferu danych replikacji geograficznej**: ta opłata dotyczy tylko kont ze skonfigurowaną replikacją geograficzną, w tym GRS i RA-GRS. Transfer danych w ramach replikacji geograficznej powoduje naliczanie opłaty za każdy gigabajt.
* **Koszty transferu danych wychodzących**: transfery danych wychodzących (dane przesyłane poza region platformy Azure) powodują naliczanie opłat za zużycie przepustowości za każdy gigabajt, co jest spójne z kontami magazynu ogólnego przeznaczenia.
* **Zmiana warstwy magazynowania**: zmiana warstwy magazynowania z Chłodna na Gorąca spowoduje naliczenie opłaty równej opłacie za odczytanie wszystkich danych istniejących w ramach konta magazynu. Natomiast zmiana warstwy magazynowania konta z Gorąca na Chłodna spowoduje naliczenie opłaty równej opłacie za zapisanie wszystkich danych w warstwie Chłodna (tylko konta GPv2).

> [!NOTE]
> Aby uzyskać więcej informacji na temat cen kont usługi Blob storage, zobacz [cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/) strony. Więcej informacji dotyczących opłat za transfer danych wychodzących można znaleźć na stronie [Szczegóły cennika transferów danych](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="faq"></a>Często zadawane pytania

**Czy używać konta usługi Blob Storage, czy GPv2, jeśli chcę obsługiwać warstwy danych?**

Zalecamy korzystanie z kont GPv2 zamiast kont usługi Blob Storage na potrzeby obsługi warstw. Konta GPv2 obsługują wszystkie funkcje, które obsługują konta usługi Blob Storage, i wiele innych. Ceny kont usługi Blob Storage i GPv2 są niemal identyczne, ale niektóre nowe funkcje i rabaty cenowe będą dostępne tylko na kontach GPv2. Konta GPv1 nie obsługują warstw.

Struktury cen kont GPv1 i GPv2 są różne, a klienci powinni dokładnie je sprawdzić przed podjęciem decyzji o użyciu kont GPv2. Istniejące konto usługi Blob Storage lub GPv1 można łatwo przekonwertować na konto GPv2 w ramach prostego procesu uruchamianego jednym kliknięciem. Aby uzyskać więcej informacji, zobacz [Omówienie konta magazynu platformy Azure](../common/storage-account-overview.md).

**Czy mogę przechowywać obiekty we wszystkich trzech warstwach (Gorąca, Chłodna i Archiwum) na tym samym koncie?**

Tak. Atrybut **Warstwa dostępu** ustawiany na poziomie konta to domyślna warstwa mająca zastosowanie do wszystkich obiektów na tym koncie bez jawnie ustawionej warstwy. Jednak obsługa warstw na poziomie obiektów blob umożliwia ustawienie warstwy dostępu na poziomie obiektu niezależnie od ustawienia warstwy dostępu konta. Obiekty blob mogą istnieć w ramach tego samego konta w jednej z trzech warstw magazynowania (Gorąca, Chłodna lub Archiwum).

**Czy mogę zmienić domyślną warstwę magazynowania na moim koncie usługi Blob Storage lub koncie magazynu GPv2?**

Tak, zmiana domyślnej warstwy magazynowania jest możliwa przez ustawienie atrybutu **Warstwa dostępu** dla konta magazynu. Zmiana warstwy magazynowania będzie miała zastosowanie do wszystkich obiektów przechowywanych na koncie, które nie mają jawnie ustawionej warstwy. Zmiana warstwy magazynowania z Gorąca na Chłodna spowoduje naliczenie opłat za operacje zapisu (za 10 000 operacji) dla wszystkich obiektów blob bez ustawionej warstwy tylko na kontach GPv2. Natomiast zmiana warstwy z Chłodna na Gorąca spowoduje naliczenie opłat za operacje odczytu (za 10 000 operacji) i pobieranie danych (za GB) dla wszystkich obiektów blob na kontach usługi Blob Storage i GPv2.

**Czy mogę ustawić domyślną warstwę dostępu do konta na Archiwum?**

Nie. Tylko warstwę magazynowania Gorąca lub Chłodna można ustawić jako domyślną warstwę dostępu do konta. Archiwum można ustawić tylko na poziomie obiektu.

**W którym regionach są dostępne warstwy magazynowania Gorąca, Chłodna i Archiwum?**

Warstwy magazynowania Gorąca i Chłodna oraz obsługa warstw na poziomie obiektu blob są dostępne we wszystkich regionach. Magazyn w warstwie Archiwum będzie początkowo dostępny tylko w wybranych regionach. Pełną listę można znaleźć w temacie [Dostępność produktów platformy Azure według regionów](https://azure.microsoft.com/regions/services/).

**Czy obiekty blob w chłodnej warstwie magazynowania działają inaczej niż obiekty blob w gorącej warstwie magazynowania?**

Obiekty blob w warstwie magazynowania Gorąca mają takie samo opóźnienie jak obiekty blob na kontach GPv1, GPv2 i usługi Blob Storage. Obiekty blob w warstwie magazynowania Chłodna mają podobne opóźnienie (w milisekundach) jak obiekty blob na kontach GPv1, GPv2 i usługi Blob Storage. Obiekty blob w warstwie magazynowania Archiwum mają kilka godzin opóźnienia na kontach GPv1, GPv2 i usługi Blob Storage.

Obiekty blob w chłodnej warstwie magazynowania będą miały nieco niższy poziom dostępności usług (umowa SLA) niż obiekty blob przechowywane w gorącej warstwie magazynowania. Aby uzyskać więcej informacji, zobacz [Magazyn — umowa SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**Czy operacje w warstwach Gorąca, Chłodna i Archiwum są takie same?**

Tak. Wszystkie operacje w warstwach Gorąca i Chłodna są w 100% spójne. Wszystkie prawidłowe operacje w warstwie Archiwum, w tym usuwanie, tworzenie listy, pobieranie właściwości/metadanych obiektów blob i ustawianie warstwy obiektu blob, są w 100% spójne z warstwami Gorąca i Chłodna. Obiektu blob znajdującego się w warstwie Archiwum nie można odczytywać ani modyfikować.

**Kiedy podczas ponownego wypełniania obiektu blob z warstwy Archiwum do warstwy Gorąca lub Chłodna będę wiedzieć, że ten proces został ukończony?**

Podczas ponownego wypełniania można używać operacji pobierania właściwości obiektu blob, aby sondować atrybut **Stan archiwum** w celu potwierdzenia ukończenia zmiany warstwy. Właściwość ta ma wartość „rehydrate-pending-to-hot” (ponowne wypełnianie w celu przejścia do warstwy gorącej) lub „rehydrate-pending-to-cool” (ponowne wypełnianie w celu przejścia do warstwy chłodnej) w zależności od warstwy docelowej. Po zakończeniu tego procesu właściwość obiektu blob „stan archiwum” jest usuwana, a wartość właściwości **Warstwa dostępu** odpowiada nowej warstwie Gorąca lub Chłodna.  

**Kiedy po ustawieniu warstwy obiektu blob rozpocznie się naliczanie opłat przy użyciu odpowiedniej stawki?**

Każdy obiekt blob jest zawsze rozliczane zgodnie z warstwą określoną przez obiektu blob **warstwy dostępu** właściwości. Podczas ustawiania nowej warstwy obiektu blob, **warstwy dostępu** właściwość odzwierciedla natychmiast nową warstwę dla wszystkich przejść. Jednak ponownego wypełniania obiektu blob z warstwy archiwum do warstwy gorąca lub chłodna może potrwać kilka godzin. W takich przypadkach stosowana jest stawka przy użyciu stawek warstwy archiwum do czasu ukończenia, w tym momencie ponownego wypełniania **warstwy dostępu** właściwość odzwierciedla nową warstwę. W tym momencie są rozliczane dla tego obiektu blob zgodnie ze stawką gorąca lub chłodna.

**Jak mogę ustalić, czy będę płacić za wczesne usunięcie w przypadku usunięcia lub przeniesienia obiektu blob z warstwy Chłodna lub Archiwum?**

Proporcjonalne opłaty za wczesne usunięcie będą dotyczyć wszystkich obiektów blob usuniętych lub przeniesionych z warstwy Chłodna (tylko konta GPv2) lub Archiwum przed upływem odpowiednio 30 lub 180 dni. Można określić, jak długo obiekt blob został w warstwie chłodna lub archiwum, sprawdzając **czas zmiany warstwy dostępu** właściwość, która udostępnia znacznik ostatniej zmiany warstwy obiektu blob. Więcej szczegółów można znaleźć w sekcji dotyczącej [wczesnego usunięcia z warstwy Chłodna i Archiwum](#cool-and-archive-early-deletion).

**Które narzędzia i zestawy SDK platformy Azure obsługują warstwy na poziomie obiektów blob i magazyn w warstwie Archiwum?**

Następujące rozwiązania: witryna Azure Portal, program PowerShell, narzędzia interfejsu wiersza polecenia oraz biblioteki klienta środowisk .NET, Java, Python i Node.js obsługują warstwy na poziomie obiektów blob i magazyn w warstwie Archiwum.  

**Ile danych można przechowywać w warstwach Gorąca, Chłodna i Archiwum?**

Limity magazynu danych i inne są ustawiane na poziomie konta, a nie na poziomie warstwy magazynowania. W związku z tym można korzystać ze wszystkich limitu w jednej warstwy lub we wszystkich trzech warstwach. Aby uzyskać więcej informacji, zobacz [usługi Azure Storage dotyczące skalowalności i cele wydajności](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Kolejne kroki

### <a name="evaluate-hot-cool-and-archive-in-gpv2-blob-storage-accounts"></a>Ocena warstw Gorąca, Chłodna i Archiwum na kontach GPv2 i usługi Blob Storage

[Sprawdzanie dostępności warstw Gorąca, Chłodna i Archiwum według regionu](https://azure.microsoft.com/regions/#services)

[Zarządzanie cyklem życia magazynu obiektów Blob platformy Azure](https://docs.microsoft.com/azure/storage/common/storage-lifecycle-managment-concepts)

[Ocena użycia bieżących kont magazynu przez włączenie metryk usługi Azure Storage](../common/storage-enable-and-view-metrics.md)

[Sprawdzanie cen warstw Gorąca, Chłodna i Archiwum na kontach usługi Blob Storage i GPv2 według regionu](https://azure.microsoft.com/pricing/details/storage/)

[Sprawdzanie ceny transferu danych](https://azure.microsoft.com/pricing/details/data-transfers/)
