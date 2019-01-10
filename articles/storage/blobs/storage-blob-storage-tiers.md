---
title: — Wersja Premium, gorąca, chłodna i archiwalna magazynu obiektów blob — usługi Azure Storage
description: — Wersja Premium, gorąca, chłodna i archiwalna magazynu dla konta usługi Azure storage.
services: storage
author: kuhussai
ms.service: storage
ms.topic: article
ms.date: 01/09/2018
ms.author: kuhussai
ms.component: blobs
ms.openlocfilehash: 21e442c7a0cdd0edcce77c862b11ae368d4a3abc
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2019
ms.locfileid: "54191670"
---
# <a name="azure-blob-storage-premium-preview-hot-cool-and-archive-storage-tiers"></a>Usługa Azure Blob storage: — Wersja Premium (wersja zapoznawcza), warstw magazynowania gorąca, chłodna i archiwum

## <a name="overview"></a>Przegląd

Usługa Azure storage oferuje warstw innego magazynu, które umożliwiają przechowywanie danych obiektów Blob w sposób najbardziej efektywny. Dostępne warstwy obejmują:

- **Usługa Premium storage (wersja zapoznawcza)** zapewnia wysoką wydajność sprzętu dla danych, z których korzysta się często.
 
- **Gorąco magazynu**: jest zoptymalizowana pod kątem magazynowania danych, z których korzysta się często. 

- **Chłodna magazynu** została zoptymalizowana pod kątem magazynowania danych, które rzadko uzyskuje dostęp i przechowywane przez co najmniej 30 dni.
 
- **Magazyn archiwum** została zoptymalizowana pod kątem magazynowania danych, które rzadko uzyskuje dostęp i przechowywane przez co najmniej 180 dni elastyczne wymagania dotyczące opóźnień (rzędu kilku godzin).

Następujące zagadnienia dotyczące towarzyszyć warstw innego magazynu:

- Warstwa magazynowania archiwum jest dostępna tylko na poziomie obiektów blob, a nie na poziomie konta magazynu.
 
- Dane w warstwie magazynu chłodnego mogą tolerować nieco niższą dostępność, ale nadal wymagane wysoka trwałość oraz podobny czas dostępu i parametry przepływności jak gorących danych. Dla chłodnych danych umowa SLA dotycząca nieco niższej dostępności i wyższe dostępu koszty w porównaniu z gorącymi danymi to dopuszczalne wady, biorąc pod uwagę niższe koszty magazynowania.

- Magazyn Archiwum działa w trybie offline i ma najniższe koszty magazynowania, ale także najwyższe koszty dostępu.
 
- Można ustawić tylko warstwy magazynu gorąca i chłodna na poziomie konta. Obecnie w warstwie archiwum nie można ustawić na poziomie konta.
 
- Gorąca, chłodna, i warstwy archiwum można ustawić na poziomie obiektu.

Dane przechowywane w chmurze rośnie w tempie wykładniczym. Aby zarządzać kosztami zwiększających się potrzeb dotyczących magazynowania, warto zorganizować dane na podstawie atrybutów, takich jak częstotliwość dostępu i planowany okres przechowywania. Pozwoli to na optymalizację kosztów. Dane przechowywane w chmurze mogą być różne pod względem sposobu ich generowania i przetwarzania oraz uzyskiwania do nich dostępu przez cały okres ich istnienia. Do niektórych danych często uzyskuje się dostęp. Są one również często modyfikowane w trakcie całego okresu istnienia. Do niektórych danych często uzyskuje się dostęp na początkowym etapie istnienia, a z czasem już zdecydowanie rzadziej. Niektóre dane pozostają nieużywane w chmurze i dostęp do nich uzyskuje się rzadko (lub w ogóle) po umieszczeniu ich w magazynie.

W przypadku każdego z tych scenariuszy dostępu do danych istnieją korzyści płynące z różnych warstw magazynowania, zoptymalizowanych pod kątem określonego wzorca dostępu. Z warstwami magazynowania gorąca, chłodna i archiwum usługi Azure Blob storage zaspokaja potrzebę korzystania ze zróżnicowanych warstw magazynowania z oddzielnymi modelami cenowymi.

## <a name="storage-accounts-that-support-tiering"></a>Konta magazynu z obsługą warstw

Może być tylko warstwy dane magazynu obiektów na warstwę gorąca, chłodna i archiwum w usłudze Blob storage lub ogólnego przeznaczenia w wersji 2 (GPv2) konta. Konta ogólnego przeznaczenia w wersji 1 (GPv1) nie obsługują warstw. Klienci mogą jednak łatwo przekształcać istniejące konta GPv1 lub konta usługi Blob Storage w konta GPv2 w ramach prostego procesu uruchamianego jednym kliknięciem w witrynie Azure Portal. Konto GPv2 oferuje nową strukturę cen obiektów blob, plików i kolejek oraz dostęp do różnych nowych funkcji magazynu. Co więcej, w przyszłości niektóre nowe funkcje i rabaty cenowe będą oferowane tylko w przypadku kont GPv2. W związku z tym klienci powinni zastanowić się nad korzystaniem z konta GPv2, ale podjąć taką decyzję mogą tylko po zapoznaniu się z cenami wszystkich usług, ponieważ niektóre obciążenia mogą być droższe na kontach GPv2 niż na kontach GPv1. Aby uzyskać więcej informacji, zobacz [Omówienie konta magazynu platformy Azure](../common/storage-account-overview.md).

Blob storage i GPv2 kont udostępniają **warstwy dostępu** atrybut na poziomie konta, co pozwala na określanie domyślnej warstwy magazynowania jako gorąca lub chłodna dla dowolnego obiektu blob na koncie magazynu, który nie ma jawnie ustawionej warstwy na poziom obiektu. W przypadku obiektów z warstwą ustawioną na poziomie obiektu warstwa konta nie będzie stosowana. Warstwę archiwum można stosować tylko na poziomie obiektu. W dowolnym momencie można przełączać się między tymi warstwami magazynowania.

## <a name="premium-access-tier"></a>Warstwa dostępu — wersja Premium

Dostępna w wersji zapoznawczej jest warstwa dostępu — wersja Premium, co sprawia, że często używane dane dostępne za pośrednictwem sprzętu wysokiej wydajności. Dane przechowywane w tej warstwie znajduje się na dyskach SSD, które są zoptymalizowane pod kątem mniejsze opóźnienie większe transakcyjnych w porównaniu do tradycyjnych dysków twardych. Warstwa dostępu — wersja Premium jest dostępna za pośrednictwem typu konta magazynu blokowych obiektów Blob tylko.

Ta warstwa jest idealny dla obciążeń wymagających czasy reakcji szybkie i spójne. Dane, które obejmuje użytkowników końcowych, takich jak interaktywne wideo edycji statyczne zawartość sieci web, transakcji w trybie online i jak dobrze nadają się dla warstwy dostępu do wersji Premium. Ta warstwa jest przeznaczony dla obciążeń, które wykonują wiele małych transakcji, takich jak przechwytywanie danych telemetrycznych, wiadomości błyskawiczne i transformacji danych.

Aby korzystać z tej warstwy aprowizować nowe konto magazynu blokowych obiektów Blob i rozpocząć tworzenie kontenerów i obiektów blob za pomocą [interfejsu API REST usługi Blob](/rest/api/storageservices/blob-service-rest-api), [AzCopy](/azure/storage/common/storage-use-azcopy), lub [Eksploratora usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

W trakcie okresu zapoznawczego warstwy dostępu do usługi Premium:

- Jest dostępna jako magazyn lokalnie nadmiarowy (LRS)
- Jest dostępna tylko w następujących regionach: Wschodnie stany USA 2, środkowe stany USA i zachodnie stany USA
- Nie obsługuje warstw na poziomie obiektu lub automatyczną obsługą warstw pozwalająca za pomocą funkcji zarządzania cyklem życia danych

Aby dowiedzieć się, jak zarejestrować się w wersji zapoznawczej z warstwy Premium dostępu, zobacz [wprowadzenie do magazynu obiektów Blob platformy Azure — wersja Premium](https://aka.ms/premiumblob).

## <a name="hot-access-tier"></a>Warstwa dostępu Gorąca

Magazynu gorącego ma wyższe koszty magazynowania, niż w warstwach chłodna i archiwum magazynu, ale najniższe koszty dostępu. Przykładowe scenariusze użycia dotyczące warstwy magazynu gorącego obejmują:

* Dane, które są często używane lub przewiduje się do nich częsty dostęp (odczyt i zapis danych).
* Dane, które są przygotowywane do przetwarzania i ewentualnej migracji do warstwy magazynu chłodnego.

## <a name="cool-access-tier"></a>Warstwa dostępu Chłodna

Warstwa magazynowania chłodna ma niższe koszty magazynowania i wyższe koszty dostępu w porównaniu do magazynu gorącego. Ta warstwa jest przeznaczona dla danych, które pozostaną w warstwie chłodna przez co najmniej 30 dni. Przykładowe scenariusze użycia dotyczące warstwy magazynu chłodnego obejmują:

* Krótkoterminowe kopie zapasowe i zestawy danych odzyskiwania po awarii.
* Starszą zawartość nośników, która nie jest już często wyświetlana, ale oczekiwane jest, że będzie ona natychmiast dostępna, gdy będzie to wymagane.
* Duże zbiory danych, które muszą być przechowywane w sposób ekonomiczny, podczas gdy na potrzeby przyszłego przetwarzania zbierana jest większa ilość danych (*np.* długoterminowe magazynowanie danych naukowych lub nieprzetworzonych danych telemetrycznych z zakładu produkcyjnego).

## <a name="archive-access-tier"></a>Warstwa dostępu Archiwum

Usługa Archive storage ma najniższy koszt magazynowania i wyższe koszty pobierania danych w porównaniu do warstwy gorąca i chłodna. Ta warstwa jest przeznaczona dla danych, które mogą tolerować kilka godzin opóźnienia w pobieraniu i pozostaną w warstwie archiwum przez co najmniej 180 dni.

Gdy obiekt blob znajduje się w magazynie archiwalnym, danych obiektów blob jest w trybie offline i nie można odczytać skopiowany, zastąpione lub zmodyfikowane. Nie można również tworzyć migawek obiektu blob w magazynie archiwalnym. Jednak metadane obiektu blob pozostaje online i dostępne, co umożliwia wyświetlanie listy obiektów blob i jego właściwości. W przypadku obiektów blob w warstwie Archiwum jedynymi prawidłowymi operacjami są: GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier oraz DeleteBlob. 


Przykładowe scenariusze użycia dotyczące warstwy magazynu archiwum obejmują:

* Długoterminowe kopie zapasowe, dodatkowe kopie zapasowej i archiwalne zestawy danych
* Oryginalne (nieprzetworzone) dane, które muszą zostać zachowane, nawet po przetworzeniu ich do ostatecznej użytecznej postaci (*np.* nieprzetworzone pliki multimedialne po transkodowaniu do innych formatów).
* Dane zgodności i dane archiwalne, które muszą być przechowywane przez długi czas, i do których bardzo rzadko uzyskuje się dostęp (*np.* zapisy z kamer monitorujących, stare zdjęcia rentgenowskie lub zdjęcia z rezonansu magnetycznego dla organizacji opieki zdrowotnej, nagrania audio i zapisy rozmów telefonicznych z klientami dla firm z branży usług finansowych).

### <a name="blob-rehydration"></a>Ponowne wypełnianie obiektów blob
Do odczytywania danych w magazynie archiwalnym, należy najpierw zmienić warstwę obiektu blob na gorącą lub chłodną. Proces ten, określany jako „ponowne wypełnianie” (ang. rehydration), może potrwać do 15 godzin. Dla uzyskania optymalnej wydajności zaleca się dużych rozmiarów obiektów blob. Ponowne wypełnianie kilku małych obiektów blob równocześnie może spowodować dalsze przedłużenie czasu.

Podczas ponownego wypełniania można sprawdzać właściwość obiektu blob **Stan archiwum**, aby upewnić się, że warstwa została zmieniona. Właściwość ta ma wartość „rehydrate-pending-to-hot” (ponowne wypełnianie w celu przejścia do warstwy gorącej) lub „rehydrate-pending-to-cool” (ponowne wypełnianie w celu przejścia do warstwy chłodnej) w zależności od warstwy docelowej. Po zakończeniu właściwość stan archiwum zostanie usunięty, a **warstwy dostępu** właściwość obiektu blob odzwierciedla nowej warstwy gorąca lub chłodna warstwa.  

## <a name="blob-level-tiering"></a>Obsługa warstw na poziomie obiektów blob

Funkcja obsługi warstw na poziomie obiektów blob umożliwia zmianę warstwy danych na poziomie obiektu przy użyciu jednej operacji o nazwie [Ustawianie warstwy obiektu blob](/rest/api/storageservices/set-blob-tier). Zmiany wzorców użycia, można łatwo zmienić warstwę dostępu obiektu blob między warstwami gorąca, chłodna lub archiwalna, bez konieczności przenoszenia danych między kontami. Wszystkie zmiany warstw natychmiast. Jednak ponownego wypełniania obiektu blob z archiwum może potrwać kilka godzin. 

Czas ostatniej zmiany warstwy obiektu blob jest uwidaczniany za pomocą właściwości obiektu blob **Czas zmiany warstwy dostępu**. Jeśli obiekt blob znajduje się w warstwie archiwum, go nie można zastąpić, więc przekazanie tego samego obiektu blob nie jest dozwolona w tym scenariuszu. Może spowodować zastąpienie obiektu blob w gorącej lub chłodnej warstwy, w których przypadku nowy obiekt blob dziedziczy warstwę obiektu blob, który został zastąpiony.

W obrębie jednego konta jest możliwe współistnienie obiektów blob należących do wszystkich trzech warstw magazynowania. Obiekt blob bez jawnie przypisanej warstwy korzysta z warstwy zgodnie z ustawieniem warstwy dostępu konta. Jeśli warstwa dostępu jest wnioskowana z konta, możesz zobaczyć **wywnioskowana Warstwa dostępu** obiektu blob właściwość ustawioną na wartość "true", a obiekt blob **warstwy dostępu** właściwość obiektu blob jest zgodna z warstwą konta. W witrynie Azure Portal właściwość Wywnioskowana warstwa dostępu jest wyświetlana z warstwą dostępu obiektu blob, np. Gorąca (wywnioskowana) lub Chłodna (wywnioskowana).

> [!NOTE]
> Magazyn Archiwum i funkcja obsługi warstw na poziomie obiektów blob obsługują tylko blokowe obiekty blob. Nie można także zmienić warstwy blokowego obiektu blob, który ma migawki.

> [!NOTE]
> Dane przechowywane w warstwie Premium i dostępu nie może obecnie należeć do warstwy gorąca, chłodna lub archiwum, przy użyciu [Ustawianie warstwy obiektu Blob](/rest/api/storageservices/set-blob-tier) lub za pomocą zarządzania cyklem życia usługi Azure Blob Storage. W celu przeniesienia danych należy synchronicznie skopiować obiekty BLOB z dostępu Premium do korzystania z gorącej [umieścić blok z adresu URL interfejsu API](/rest/api/storageservices/put-block-from-url) lub wersję narzędzia AzCopy, która obsługuje ten interfejs API. *Umieścić blok z adresu URL* API synchronicznie kopiuje dane na serwerze, co oznacza ukończenia wywołania tylko raz wszystkie dane są przenoszone z oryginalnej lokalizacji serwera do lokalizacji docelowej.

### <a name="blob-lifecycle-management"></a>Zarządzanie cyklem życia obiektów blob
Zarządzanie cyklem życia magazynu obiektów blob (wersja zapoznawcza) oferuje zaawansowane, oparte na regułach zasadę, która umożliwia przeniesienie danych do najlepszych warstwy dostępu i wygasanie danych na końcu jej cyklu projektowania. Zobacz [Zarządzanie cyklem życia magazynu obiektów Blob platformy Azure](storage-lifecycle-management-concepts.md) Aby dowiedzieć się więcej.  

### <a name="blob-level-tiering-billing"></a>Rozliczanie obsługi warstw na poziomie obiektów blob

Gdy obiekt blob jest przenoszony do chłodniejszej warstwy (gorąca -> chłodna, gorąca -> archiwum lub chłodna -> archiwum), operacja jest rozliczana jako operacja zapisu do warstwy docelowej, gdzie operacje zapisu (za 10 000 operacji) i opłaty za zapisu (za GB) danych w warstwie docelowej mają zastosowanie. Gdy obiekt blob jest przenoszony do cieplejszej warstwy (archiwum -> chłodna, archiwum -> gorąca lub chłodna -> gorąca), operacja jest rozliczana jako odczyt z warstwy źródłowej, w których obowiązują operacje odczytu (za 10 000 operacji) i opłaty za pobieranie (za GB) danych z warstwy źródłowej.

| | **Zapis opłaty** | **Opłata za odczyt** 
| ---- | ----- | ----- |
| **Kierunek SetBlobTier** | Gorąca -> chłodna, gorąca -> archiwum, chłodna -> archiwum | Archiwum -> chłodna, archiwum -> gorąca, chłodna -> gorąca

W przypadku przełączania warstwy konta z gorąca na chłodna, opłata wyniesie za operacje zapisu (za 10 000 operacji) dla wszystkich obiektów blob bez ustawionej warstwy tylko na kontach GPv2. Nie ma opłat dla tej zmiany w kontach magazynu obiektów Blob. Opłata wyniesie zarówno dla operacji odczytu (za 10 000 operacji) i pobieranie danych (za GB) w przypadku przełączania usługi Blob storage lub GPv2 Zmiana warstwy konta z chłodna na gorąca. Opłaty za wczesne usunięcie dla dowolnego obiektu blob przeniesionych z warstwy chłodna lub archiwum mogą także obowiązywać.

### <a name="cool-and-archive-early-deletion"></a>Opłaty za wcześniejsze usunięcie w warstwach Chłodna i Archiwum

Dodatkowo za GB danych, opłaty miesięcznej za każdy obiekt blob jest przenoszony do warstwy chłodna (tylko konta GPv2) podlega ciekawe wcześniejsze usunięcie w okresie 30 dni, a każdy obiekt blob jest przenoszony do warstwy archiwum podlega okresem wcześniejszego usunięcia 180 dni. Ta opłata jest naliczana proporcjonalnie. Na przykład, jeśli obiekt blob jest przenoszony do archiwum i następnie usunięty lub przeniesiony do warstwy gorąca po 45 dniach, możesz opłata będzie naliczana wczesne usunięcie odpowiadająca 135 (180 minus 45) dniom przechowywania tego obiektu blob w warstwie archiwum.

Wcześniejsze usunięcie może obliczyć przy użyciu właściwość obiektu blob **czas utworzenia**, jeśli wystąpił brak dostępu do zmiany warstwy. W przeciwnym razie można używać podczas ostatniej modyfikacji warstwy dostępu chłodna lub archiwum, wyświetlając właściwość obiektu blob: **Zmień czas, dostęp do warstwy w-**. Aby uzyskać więcej informacji na temat właściwości obiektu blob, zobacz [uzyskać właściwości obiektu Blob](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties).

## <a name="comparison-of-the-storage-tiers"></a>Porównanie warstw magazynowania

W poniższej tabeli przedstawiono porównanie gorącej, chłodna i archiwalna magazynu.

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
> Konta Magazynu obiektów blob obsługują te same cele wydajności i skalowalności co konta magazynu ogólnego przeznaczenia. Aby uzyskać więcej informacji, zobacz [usługi Azure Storage dotyczące skalowalności i cele wydajności](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). 

## <a name="quickstart-scenarios"></a>Scenariusze typu Szybki start

W tej sekcji przedstawiono następujące scenariusze obejmujące użycie witryny Azure Portal:

* Jak zmienić domyślną warstwę dostępu konta GPv2 lub usługi Blob Storage.
* Jak zmienić warstwę obiektu blob na koncie GPv2 lub usługi Blob Storage.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Zmienianie domyślnej warstwy dostępu konta GPv2 lub usługi Blob Storage

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Aby przejść do konta magazynu, zaznacz pozycję Wszystkie zasoby, a następnie wybierz konto magazynu.

3. W bloku Ustawienia kliknij pozycję **Konfiguracja**, aby wyświetlić i/lub zmienić konfigurację konta.

4. Wybierz odpowiednią warstwę magazynu do własnych potrzeb: Ustaw **warstwy dostępu** do jednej **chłodna** lub **gorąca**.

5. Kliknij pozycję Zapisz w górnej części bloku.

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Zmień warstwę obiektu blob na koncie GPv2 lub usługi Blob Storage.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Aby przejść do obiektu blob na koncie magazynu, wybierz pozycję Wszystkie zasoby, wybierz konto magazynu, wybierz kontener, a następnie wybierz obiekt blob.

3. W bloku Właściwości obiektu blob kliknij menu rozwijane **Warstwa dostępu**, aby wybrać warstwę magazynowania **Gorąca**, **Chłodna** lub **Archiwum**.

5. Kliknij pozycję Zapisz w górnej części bloku.

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Wszystkie konta magazynu używają modelu cenowego dla magazynu obiektów Blob opartego na warstwie każdego obiektu blob. Mieć na uwadze następujące zagadnienia dotyczące rozliczeń:

* **Koszty magazynowania**: Oprócz ilości przechowywanych danych koszt przechowywania danych różni się w zależności od warstwy magazynowania. Koszt za gigabajt zmniejsza się w miarę, jak warstwa staje się chłodniejsza.
* **Koszty dostępu do danych**: Dostęp do danych opłat wzrostu, jak warstwa staje się chłodniejsza. W przypadku danych w warstwie chłodna i warstwa magazynowania archiwum jest naliczana opłata dostępu do danych za każdy gigabajt dla operacji odczytu.
* **Koszty transakcji**: Brak opłat każdej transakcji dla wszystkich warstw, która zwiększa się, jak warstwa staje się chłodniejsza.
* **Koszty transferu danych replikacji geograficznej**: Ta opłata dotyczy tylko kont ze skonfigurowaną replikacją geograficzną, w tym GRS i RA-GRS. Transfer danych w ramach replikacji geograficznej powoduje naliczanie opłaty za każdy gigabajt.
* **Koszty transferu danych wychodzących**: Wychodzące transfery danych (dane przesyłane poza region platformy Azure) Naliczanie opłat za zużycie przepustowości za każdy gigabajt, co jest spójne z kontami magazynu ogólnego przeznaczenia.
* **Zmiana warstwy magazynowania**: Zmiana warstwy magazynowania konta z chłodna na gorąca spowoduje naliczenie opłaty równej odczytanie wszystkich danych na koncie magazynu. Jednak zmiana warstwy magazynowania konta z gorąca na chłodna spowoduje naliczenie opłaty równej zapisanie wszystkich danych w warstwie chłodna (tylko konta GPv2).

> [!NOTE]
> Aby uzyskać więcej informacji na temat cen kont usługi Blob storage, zobacz [cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/) strony. Więcej informacji dotyczących opłat za transfer danych wychodzących można znaleźć na stronie [Szczegóły cennika transferów danych](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="faq"></a>Często zadawane pytania

**Czy używać konta usługi Blob Storage, czy GPv2, jeśli chcę obsługiwać warstwy danych?**

Zalecamy korzystanie z kont GPv2 zamiast kont usługi Blob Storage na potrzeby obsługi warstw. Konta GPv2 obsługują wszystkie funkcje, które obsługują konta usługi Blob Storage, i wiele innych. Ceny kont usługi Blob Storage i GPv2 są niemal identyczne, ale niektóre nowe funkcje i rabaty cenowe będą dostępne tylko na kontach GPv2. Konta GPv1 nie obsługują warstw.

Struktury cen kont GPv1 i GPv2 są różne, a klienci powinni dokładnie je sprawdzić przed podjęciem decyzji o użyciu kont GPv2. Istniejące konto usługi Blob Storage lub GPv1 można łatwo przekonwertować na konto GPv2 w ramach prostego procesu uruchamianego jednym kliknięciem. Aby uzyskać więcej informacji, zobacz [Omówienie konta magazynu platformy Azure](../common/storage-account-overview.md).

**Czy mogę przechowywać obiekty w wszystkich trzech (gorąca, chłodna i archiwalna) warstw magazynowania na tym samym koncie?**

Tak. Atrybut **Warstwa dostępu** ustawiany na poziomie konta to domyślna warstwa mająca zastosowanie do wszystkich obiektów na tym koncie bez jawnie ustawionej warstwy. Jednak obsługa warstw na poziomie obiektów blob umożliwia ustawienie warstwy dostępu na poziomie obiektu niezależnie od ustawienia warstwy dostępu konta. Obiekty BLOB we wszystkich trzech warstw magazynowania (gorąca, chłodna lub archiwalna) mogą istnieć w ramach tego samego konta.

**Czy mogę zmienić domyślną warstwę magazynowania na moim koncie usługi Blob Storage lub koncie magazynu GPv2?**

Tak, zmiana domyślnej warstwy magazynowania jest możliwa przez ustawienie atrybutu **Warstwa dostępu** dla konta magazynu. Zmiana warstwy magazynowania będzie miała zastosowanie do wszystkich obiektów przechowywanych na koncie, które nie mają jawnie ustawionej warstwy. Zmiana warstwy magazynowania z gorąca na chłodna spowoduje naliczenie operacje zapisu (za 10 000 operacji) dla wszystkich obiektów blob bez ustawionej warstwy tylko na kontach GPv2 i zmiana warstwy z chłodna na gorąca spowoduje naliczenie operacje odczytu (za 10 000 operacji) i pobieranie danych (za GB), opłaty za wszystkie obiekty BLOB w magazynie obiektów Blob i gpv2.

**Mogę ustawić Moje domyślną warstwę dostępu konta do archiwum?**

Nie. Tylko warstwy magazynu gorąca i chłodna można ustawić jako domyślną warstwę dostępu konta. Archiwum można ustawić tylko na poziomie obiektu.

**W którym regionach warstw magazynowania gorąca, chłodna i archiwum w są dostępne?**

Warstwy magazynowania gorąca i chłodna oraz obsługa warstw na poziomie obiektów blob są dostępne we wszystkich regionach. Magazyn w warstwie Archiwum będzie początkowo dostępny tylko w wybranych regionach. Pełną listę można znaleźć w temacie [Dostępność produktów platformy Azure według regionów](https://azure.microsoft.com/regions/services/).

**Czy obiekty BLOB w chłodniej warstwie magazynowania będą działały inaczej niż w gorącej warstwie magazynowania?**

Obiekty BLOB w gorącej warstwie magazynowania mają takie samo opóźnienie jak obiekty BLOB na kontach magazynu GPv1, GPv2 i Blob. Obiekty BLOB w chłodniej warstwie magazynowania mają podobne opóźnienie (w milisekundach) jak obiekty BLOB na kontach magazynu GPv1, GPv2 i Blob. Obiekty BLOB w warstwie magazynowania archiwum mają kilka godzin opóźnienia na kontach magazynu GPv1, GPv2 i Blob.

Obiekty BLOB w chłodniej warstwie magazynowania mają nieco niższy poziom dostępności usług (SLA) niż obiekty BLOB przechowywane w gorącej warstwie magazynowania. Aby uzyskać więcej informacji, zobacz [Magazyn — umowa SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**Operacje między warstwy gorąca, chłodna i archiwum są takie same?**

Tak. Wszystkie operacje w warstwach gorąca i chłodna są w 100% spójne. Wszystkie prawidłowe operacje w warstwie archiwum tym usuwanie, list, get blob właściwości/metadanych i ustawianie warstwy obiektu blob są w 100% spójne z warstwy gorąca i chłodna. Obiekt blob nie może odczytać lub zmodyfikować znajduje się w warstwie archiwum.

**Kiedy podczas ponownego wypełniania obiektu blob z warstwy archiwum do gorąca lub chłodna warstwa, skąd mam wiedzieć, ten proces został ukończony?**

Podczas ponownego wypełniania można używać operacji pobierania właściwości obiektu blob, aby sondować atrybut **Stan archiwum** w celu potwierdzenia ukończenia zmiany warstwy. Właściwość ta ma wartość „rehydrate-pending-to-hot” (ponowne wypełnianie w celu przejścia do warstwy gorącej) lub „rehydrate-pending-to-cool” (ponowne wypełnianie w celu przejścia do warstwy chłodnej) w zależności od warstwy docelowej. Po zakończeniu właściwość stan archiwum zostanie usunięty, a **warstwy dostępu** właściwość obiektu blob odzwierciedla nowej warstwy gorąca lub chłodna warstwa.  

**Kiedy po ustawieniu warstwy obiektu blob rozpocznie się naliczanie opłat przy użyciu odpowiedniej stawki?**

Każdy obiekt blob jest zawsze rozliczane zgodnie z warstwą określoną przez obiektu blob **warstwy dostępu** właściwości. Podczas ustawiania nowej warstwy obiektu blob, **warstwy dostępu** właściwość odzwierciedla natychmiast nową warstwę dla wszystkich przejść. Jednak ponownego wypełniania obiektu blob z warstwy archiwum do gorąca lub chłodna warstwa może potrwać kilka godzin. W takich przypadkach stosowana jest stawka przy użyciu stawek warstwy archiwum do czasu ukończenia, w tym momencie ponownego wypełniania **warstwy dostępu** właściwość odzwierciedla nową warstwę. W tym momencie są rozliczane dla tego obiektu blob w gorącej lub chłodnej szybkość.

**Jak określić, jeśli będę płacić za wczesne usunięcie w przypadku usunięcia lub przeniesienia obiektu blob z warstwy chłodna lub archiwum warstwy?**

Każdy obiekt blob usuniętych lub przeniesionych z warstwy chłodna (tylko konta GPv2) lub warstwy archiwum przed 30 lub 180 dni odpowiednio zostaną naliczone proporcjonalnie wczesne usunięcie. Można określić, jak długo obiekt blob został w warstwie chłodna lub archiwum, sprawdzając **czas zmiany warstwy dostępu** właściwość, która udostępnia znacznik ostatniej zmiany warstwy obiektu blob. Zobacz [w warstwach chłodna i archiwum opłaty za wcześniejsze usunięcie](#cool-and-archive-early-deletion) sekcji, aby uzyskać więcej informacji.

**Zestawy SDK i narzędzi platformy Azure, która obsługuje obsługi warstw na poziomie obiektów blob i Magazyn w warstwie archiwum?**

Witryna Azure portal, programu PowerShell i interfejsu wiersza polecenia narzędzia i biblioteki klienta .NET, Java, Python i Node.js obsługują warstwy na poziomie obiektów blob i Magazyn w warstwie archiwum.  

**Jak dużo danych można przechowywać w warstwach gorąca, chłodna i archiwum?**

Limity magazynu danych i inne są ustawiane na poziomie konta, a nie na poziomie warstwy magazynowania. W związku z tym można korzystać ze wszystkich limitu w jednej warstwy lub we wszystkich trzech warstwach. Aby uzyskać więcej informacji, zobacz [usługi Azure Storage dotyczące skalowalności i cele wydajności](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Kolejne kroki

### <a name="evaluate-hot-cool-and-archive-in-gpv2-blob-storage-accounts"></a>Oceń gorąca, chłodna i archiwum na kontach magazynu obiektów Blob z konta GPv2

[Sprawdzanie dostępności gorąca, chłodna i archiwum według regionu](https://azure.microsoft.com/regions/#services)

[Zarządzanie cyklem życia magazynu obiektów Blob platformy Azure](storage-lifecycle-management-concepts.md)

[Ocena użycia bieżących kont magazynu przez włączenie metryk usługi Azure Storage](../common/storage-enable-and-view-metrics.md)

[Sprawdź gorąca, chłodna i archiwum ceny w usłudze Blob storage i GPv2 kont według regionu](https://azure.microsoft.com/pricing/details/storage/)

[Sprawdzanie ceny transferu danych](https://azure.microsoft.com/pricing/details/data-transfers/)
