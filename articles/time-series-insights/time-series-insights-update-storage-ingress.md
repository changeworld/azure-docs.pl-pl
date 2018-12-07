---
title: Magazyn danych i transferu danych przychodzących w usłudze Azure Time Series Insights (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Omówienie przechowywania danych i transferu danych przychodzących w usłudze Azure Time Series Insights (wersja zapoznawcza)
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/05/2018
ms.openlocfilehash: e2440f6aa32710730e8b015bef1e7c583f7063e2
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "53001859"
---
# <a name="data-storage-and-ingress-in-the-azure-time-series-insights-preview"></a>Magazyn danych i transferu danych przychodzących w usłudze Azure Time Series Insights (wersja zapoznawcza)

W tym artykule opisano zmiany w magazynowaniu danych i transferu danych przychodzących z wersji zapoznawczej usługi Azure Time Series Insights (TSI). Poruszono w nim wewnętrzna struktura magazynu format pliku i **identyfikator serii czasu** właściwości. Omówiono w nim również bazowego procesu transferu danych przychodzących, przepływności i ograniczeń.

## <a name="data-storage"></a>Magazyn danych

Podczas tworzenia usługi TSI Azure w wersji zapoznawczej (**jednostki SKU PAYG**) środowiska, tworzysz dwa zasoby:

* Środowisko Azure TSI.
* Konto usługi Azure Storage ogólnego przeznaczenia w wersji 1 przechowywania danych.

Usługa TSI Przesyła (wersja zapoznawcza) używa usługi Azure Blob Storage z typem pliku Parquet. Azure TSI zarządza wszystkie operacje danych, takich jak tworzenie obiektów blob, indeksowania i partycjonowanie danych w ramach konta usługi Azure Storage. Te obiekty BLOB są tworzone przy użyciu konta usługi Azure Storage.

Podobnie jak inne blob usługi Azure Storage może odczytywać i zapisywać obiektów blob Azure TSI, utworzony na potrzeby scenariuszy integracji różnych.

> [!TIP]
> Usługa TSI Przesyła mogą mieć niekorzystny wpływ na wydajność przez odczyt lub zapis do obiektów blob, zbyt często.

Omówienie usługi Azure Blob Storage, przeczytaj [magazynu obiektów blob wprowadzenie](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).

Aby uzyskać więcej informacji na temat typu pliku Parquet, zobacz [obsługiwane typy plików w usłudze Azure Storage](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs#Parquet-format).

## <a name="parquet-file-format"></a>Format pliku parquet

Parquet jest zorientowana na kolumny danych, format pliku, który był przeznaczony dla:

* Współdziałanie
* Wydajności miejsca
* Wydajność zapytań

Azure TSI wybrała Parquet, ponieważ zapewnia kompresji danych wydajne i schematy kodowania za pomocą rozszerzonych wydajności do obsługi danych złożonych, zbiorczo.

Aby lepiej zrozumieć Parquet format pliku to wszystko na temat, przeczytaj [oficjalnej dokumentacji Parquet](https://parquet.apache.org/documentation/latest/).

## <a name="event-structure-in-parquet"></a>Struktura zdarzeń w Parquet

Dwie kopie obiektów blob utworzonych przez Azure TSI będą przechowywane w następujących formatach:

1. Pierwszy kopii początkowej będą można podzielić na partycje według czasu odbioru:

    * `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * Godzina utworzenia obiektu blob dla obiektów blob na partycje według czasu odbioru.

1. Dynamiczna grupa będą można podzielić na partycje drugi kopię repartitioned **identyfikator serii czasu**:

    * `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * Sygnatura czasowa zdarzenia minimalne w obiekcie blob dla obiektów blob, podzielone między **identyfikator serii czasu**.

> [!NOTE]
> * `<YYYY>` mapuje do reprezentacji roku 4-cyfrowego.
> * `<MM>` mapuje do reprezentacji 2-cyfrowy miesiąca.
> * `<YYYYMMDDHHMMSSfff>` mapuje na reprezentację w postaci sygnatury czasowej z roku 4-cyfrowego (`YYYY`), miesiąc 2-cyfrowy (`MM`), dnia 2-cyfrowy (`DD`), godzinę 2-cyfrowy (`HH`), 2-cyfrowy minuty (`MM`), drugi 2-cyfrowy (`SS`) i 3-cyfrowy milisekundy (`fff`).

Azure — wydarzenia TSI są mapowane do zawartości pliku Parquet:

* Każde zdarzenie jest mapowany na pojedynczy wiersz.
* Wbudowane **sygnatura czasowa** kolumny z sygnaturą czasową zdarzenia. **Sygnatura czasowa** właściwość nigdy nie ma wartość null.  Jego wartość domyślna to **czas umieszczonych w kolejce źródła zdarzenia** Jeśli **sygnatura czasowa** właściwość nie jest określona w źródle zdarzenia.  **Sygnatura czasowa** jest w formacie UTC.  
* Wszystkie inne mapy właściwości do kolumn będzie kończyć się znakiem `_string` (ciąg) `_bool` (boolean) `_datetime` (Data/godzina) i `_double` (podwójny) w zależności od typu właściwości.
* To pierwsza wersja formatu pliku, a nazywamy go jako **V = 1**.  Jeśli ta funkcja zmienia nazwę jest zwiększany odpowiednio do **V = 2**, **V = 3**i tak dalej.

## <a name="partitions"></a>Partycje

Każde środowisko Azure TSI (wersja zapoznawcza) musi mieć **identyfikator serii czasu** właściwości i **sygnatura czasowa** właściwość, która jej jednoznaczną identyfikację. Twoje **identyfikator serii czasu** działa jako partycji logicznej dla swoich danych i oferuje środowisko Azure TSI (wersja zapoznawcza) przy użyciu naturalnych granic rozproszenie danych na partycje fizyczne. Zarządzanie fizyczną partycję zarządza Azure TSI (wersja zapoznawcza) na koncie usługi Azure Storage.

Azure TSI używa dynamiczne partycjonowanie, aby zoptymalizować magazyn wykorzystanie i zwiększa wydajność zapytań przez usunięcie i ponowne utworzenie partycji. Azure TSI (wersja zapoznawcza) algorytm dynamiczny partycjonowania próbuje uniknąć jednej partycji fizycznych, zawierającej dane dla wielu partycji odrębne i logicznych. Innymi słowy, partycjonowania algorytm zachowuje wszystkie dane określone do pojedynczego **identyfikator serii czasu** wyłącznie obecne w plikach Parquet bez trwa z przeplotem z innymi **identyfikatory serii czasu**. Algorytm dynamiczny dla partycjonowania również próbuje zachować pierwotną kolejność zdarzeń w ramach pojedynczej **identyfikator serii czasu**.

Początkowo w czasie transferu danych przychodzących danych jest podzielona na partycje według **sygnatura czasowa** dzięki pojedynczej, logicznej partycji w danym okresie mogą znajdować się na wielu partycjach fizycznych. Jedna partycja fizycznych mogą również zawierać wiele lub wszystkie logiczne partycji.  Ze względu na ograniczenia rozmiaru obiektów blob nawet w przypadku optymalne, Partycjonowanie jednej partycji logicznej może zajmować wiele partycji fizycznych.

> [!NOTE]
> **Sygnatura czasowa** wartość jest komunikat **czasu umieszczonych w kolejce** w źródle zdarzenia skonfigurowane domyślnie.  

Podczas przekazywania danych historycznych lub komunikaty przetwarzania wsadowego, należy wyznaczyć **sygnatura czasowa** właściwość w Twoich danych, który jest mapowany do odpowiedniego **sygnatura czasowa** wartości, które mają być przechowywane z danymi.  **Sygnatura czasowa** właściwości jest rozróżniana wielkość liter. Aby uzyskać więcej informacji, przeczytaj [artykułu modelu szeregów czasowych](./time-series-insights-update-tsm.md).

## <a name="physical-partitions"></a>Partycje fizyczne

Fizyczną partycję jest blokowy obiekt blob w usłudze Azure Storage. Rzeczywisty rozmiar obiektów blob różnią się, ponieważ zależy od szybkości wypychania, jednak oczekujemy, że obiekty BLOB jako rozmiar około 20 – 50 MB. W związku z tym oczekiwania zespołu TSI wybrany o rozmiarze 20 MB na rozmiar w celu zoptymalizowania wydajności zapytania. Można to zmienić wraz z upływem czasu, w oparciu o rozmiar pliku i szybkość transferu danych przychodzących.

> [!NOTE]
> * Obiekty BLOB mają rozmiar o rozmiarze 20MB.
> * Obiekty BLOB platformy Azure są od czasu do czasu ponownie Partycjonować uzyskać lepszą wydajność dzięki porzucona i utworzona ponownie.
> * Należy również zauważyć, że te same dane TSI mogą być obecne w wielu obiektów blob.

## <a name="logical-partitions"></a>Partycje logiczne

Partycja logiczna jest partycji w obrębie fizyczną partycję, która przechowuje wszystkie dane, które są skojarzone z jedną wartością klucza partycji. Usługa TSI Przesyła (wersja zapoznawcza) zostanie logicznie partycji każdy obiekt blob, w oparciu o dwie właściwości:

1. **Czas serii identyfikator** — jest kluczem partycji dla wszystkich danych TSI w strumieniu zdarzeń i modelu.
1. **Sygnatura czasowa** — zależne od początkowego transferu danych przychodzących.

Azure TSI (wersja zapoznawcza) oferuje zapytania wydajna, które opierają się na te dwie właściwości. Te dwie właściwości zapewniają także najbardziej efektywną metodę szybkie dostarczanie danych w usłudze TSI.

Ważne jest, aby wybrać odpowiednią **identyfikator serii czasu**, ponieważ jest właściwością niezmienne.  Zobacz [wybierając identyfikatory serii czasu](./time-series-insights-update-how-to-id.md) Aby uzyskać więcej informacji.

## <a name="your-azure-storage-account"></a>Konta usługi Azure Storage

### <a name="storage"></a>Magazyn

Po utworzeniu TSI **PAYG** środowiska, Utwórz dwa zasoby — środowisko usługi TSI i konto magazynu platformy Azure ogólnego przeznaczenia w wersji 1 przechowywania danych. Wybraliśmy się domyślnie z powodu jej współdziałanie, cen i wydajności usługi Azure Storage ogólnego przeznaczenia w wersji 1.  

Azure TSI opublikuje maksymalnie dwie kopie każdego zdarzenia na Twoim koncie usługi Azure Storage. Kopii początkowej są zawsze zachowywane w celu zapewnienia, że można zbadać performantly za pomocą innych usług. W związku z tym, Spark, Hadoop i inne dobrze znanych narzędzi można łatwo stosować w przypadku **identyfikatory serii czasu** za pośrednictwem pierwotne Parquet pliku filtrowanie nazw plików, ponieważ te aparaty obsługuje podstawowe. Grupowanie obiektów blob według roku i miesiąca jest przydatny do zbierania listy obiektów blob w określonym zakresie czasu dla zadania niestandardowego.  

Ponadto usługa TSI Przesyła zostanie ponownie podzielić na partycje pliki Parquet zoptymalizowane pod kątem interfejsów API usługi Azure TSI i najbardziej niedawno repartitioned plik zostanie też zapisane.

W publicznej wersji zapoznawczej dane będą przechowywane przez czas nieokreślony na Twoim koncie usługi Azure Storage.

### <a name="writing-and-editing-time-series-insights-blobs"></a>Zapisywanie i edytowanie obiektów blob usługi Time Series Insights

Aby zapewnić wydajność zapytań i dostępność danych, nie edytować ani nie usuwaj żadnych obiektów blob utworzonych przez TSI.

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Uzyskiwanie dostępu do i eksportować dane z usługi Time Series Insights (wersja zapoznawcza)

Możesz uzyskać dostęp do danych przechowywanych w Eksploratorze TSI Azure (wersja zapoznawcza), aby użyć w połączeniu z innymi usługami. Na przykład można użyć danych raportowania w usłudze Power BI do przeprowadzenia uczenia maszynowego przy użyciu usługi Azure Machine Learning Studio lub w aplikacji notesu Jupyter Notebooks itp.

Istnieją trzy ogólne ścieżki dostępu do danych:

* Eksplorator usługi Azure TSI (wersja zapoznawcza).
* Interfejsy API usługi Azure TSI (wersja zapoznawcza).
* Bezpośrednio z konta usługi Azure Storage.

### <a name="from-the-time-series-insights-preview-explorer"></a>Z poziomu Eksploratora usługi Time Series Insights (wersja zapoznawcza)

Możesz wyeksportować dane do pliku CSV, w Eksploratorze TSI (wersja zapoznawcza). Przeczytaj więcej na temat [Eksplorator usługi TSI (wersja zapoznawcza)](./time-series-insights-update-explorer.md).

### <a name="from-the-time-series-insights-preview-apis"></a>Za pomocą interfejsów API usługi Insights (wersja zapoznawcza) dla serii czasu

Punkt końcowy interfejsu API można skontaktować, `/getRecorded`. Aby dowiedzieć się więcej na temat tego interfejsu API, przeczytaj temat [zapytania serii czasu](./time-series-insights-update-tsq.md).

### <a name="from-an-azure-storage-account"></a>Z konta usługi Azure Storage

1. Musisz mieć dostęp do odczytu przyznanych w do dowolnego konta będzie używany dostęp do danych usługi TSI. Aby dowiedzieć się więcej na temat udzielania dostępu do odczytu do magazynu obiektów Blob platformy Azure, przeczytaj [zarządzanie dostępem do zasobów magazynu](https://docs.microsoft.com/azure/storage/blobs/storage-manage-access-to-resources).

1. Aby uzyskać więcej informacji na temat sposobów bezpośredniego można odczytać danych z usługi Azure Blob Storage, przeczytaj [przenoszenie danych do i z usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

1. Eksportowanie danych z konta usługi Azure Storage:

    * Najpierw upewnij się, że Twoje konto ma niezbędne wymagania zostały spełnione, aby wyeksportować dane. Odczyt [magazynu importowanie i eksportowanie wymagania](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements) Aby uzyskać więcej informacji.
    * Dowiedz się więcej o innych sposobach eksportowania danych z konta usługi Azure Storage, odwiedzając [magazynu importowanie i eksportowanie danych z obiektów blob](https://docs.microsoft.com/azure/storage/common/storage-import-export-data-from-blobs)

### <a name="data-deletion"></a>Usuwanie danych

Nie należy usuwać obiekty BLOB, ponieważ Azure TSI (wersja zapoznawcza) przechowuje metadane dotyczące obiektów blob w usłudze TSI aktualizacji.

## <a name="ingress"></a>Ruch przychodzący

### <a name="azure-time-series-insights-ingress-policies"></a>Zasady transferu danych przychodzących w usłudze Azure Time Series Insights

Azure TSI (wersja zapoznawcza) obsługuje te same źródła zdarzeń i typów plików, które obecnie.

Źródła zdarzeń obsługiwanych obejmują:

- Azure IoT Hub
- Azure Event Hubs
  
  > [!NOTE]
  > Wystąpienia Centrum zdarzeń platformy Azure obsługuje platformy Kafka.

Obsługiwane typy plików obejmują:

* JSON: Bardziej na obsługiwane kształty JSON firma Microsoft może obsłużyć, zobacz [jak kształt JSON](./time-series-insights-send-events.md#json) dokumentacji.

### <a name="data-availability"></a>Dostępność danych

W publicznej wersji zapoznawczej Azure TSI (wersja zapoznawcza) indeksuje dane za pomocą strategii optymalizacji rozmiar obiektu blob. Oznacza to, że dane będą dostępne dla zapytań, gdy są indeksowane (która jest zależnie od ilości danych i w jakich prędkość).

> [!IMPORTANT]
> * Usługa TSI Przesyła GA będzie udostępniać danych w ciągu 60 sekund jego naciśnięcie źródła zdarzenia.  
> * W trakcie okresu zapoznawczego oczekujemy wyświetlić na dłuższy czas, przed udostępnieniem danych.  
>   * Jeśli wystąpią wszystkie istotne opóźnienia, skontaktuj się z nami.

### <a name="scale"></a>Skalowanie

Azure TSI (wersja zapoznawcza) obsługuje skalę początkowy ruch przychodzący do 6 MB/s na środowisko. Trwa rozszerzoną obsługę skalowania. Dokumentacja zostanie zaktualizowany w celu odzwierciedlenia tych ulepszeń.

## <a name="next-steps"></a>Kolejne kroki

Odczyt [magazynu Azure TSI (wersja zapoznawcza) i ruch przychodzący](./time-series-insights-update-storage-ingress.md).

Przeczytaj o nowym [modelowania danych](./time-series-insights-update-tsm.md).

<!-- Images -->
[1]: media/v2-update-storage-ingress/storage-architecture.png
[2]: media/v2-update-storage-ingress/parquet-files.png
[3]: media/v2-update-storage-ingress/blob-storage.png
