---
title: Magazyn danych i ruch przychodzący w wersji zapoznawczej Azure Time Series Insights | Microsoft Docs
description: Informacje na temat przechowywania i transferów danych w wersji zapoznawczej Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/20/2019
ms.custom: seodec18
ms.openlocfilehash: a37021d11de86fc0958f330f4f594e25e3aa00bd
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68618198"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Magazyn danych i ruch przychodzący w wersji zapoznawczej Azure Time Series Insights

W tym artykule opisano zmiany w usłudze Data Storage i transfer danych przychodzących z wersji zapoznawczej Azure Time Series Insights. Obejmuje ona podstawową strukturę magazynu, format pliku i Właściwość identyfikatora szeregów czasowych. W tym artykule omówiono również podstawowe procesy związane z transferem danych przychodzących, przepływność i ograniczenia.

## <a name="data-ingress"></a>Dane wejściowe

Azure Time Series Insights zasady transferu danych przychodzących określają, skąd mają być pochodzące dane źródłowe i w jakim formacie.

[![Omówienie modelu szeregów czasowych](media/v2-update-storage-ingress/tsi-data-ingress.png)](media/v2-update-storage-ingress/tsi-data-ingress.png#lightbox)

### <a name="ingress-policies"></a>Zasady dotyczące transferu danych przychodzących

Wersja zapoznawcza Time Series Insights obsługuje te same źródła zdarzeń i typy plików, które Time Series Insights obecnie obsługiwane:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)
  
Azure Time Series Insights obsługuje dane JSON przesłane za pomocą usługi Azure IoT Hub lub Azure Event Hubs. Aby zoptymalizować dane JSON IoT, Dowiedz się, [jak kształtować kod JSON](./time-series-insights-send-events.md#json).

### <a name="data-storage"></a>Magazyn danych

W przypadku tworzenia Time Series Insights w wersji zapoznawczej dla środowiska SKU z opcją płatność zgodnie z rzeczywistym użyciem należy utworzyć dwa zasoby:

* Środowisko Time Series Insights.
* Konto usługi Azure Storage ogólnego przeznaczenia, w którym będą przechowywane dane.

Wersja zapoznawcza Time Series Insights używa usługi Azure Blob Storage z typem pliku Parquet. Time Series Insights zarządza wszystkimi operacjami danych, w tym tworzeniem obiektów blob, indeksowania i partycjonowaniem danych na koncie usługi Azure Storage. Te obiekty blob tworzysz przy użyciu konta usługi Azure Storage.

Podobnie jak w przypadku innych obiektów BLOB usługi Azure Storage, tworzone obiekty blob Time Series Insights umożliwiają odczytywanie i zapisywanie ich w celu obsługi różnych scenariuszy integracji.

### <a name="data-availability"></a>Dostępność danych

Time Series Insights w wersji zapoznawczej indeksuje dane przy użyciu strategii optymalizacji rozmiaru obiektów BLOB. Dane staną się dostępne po indeksowaniu, które są oparte na tym, ile danych jest w czasie, i o tym, co szybkość.

> [!IMPORTANT]
> * Udostępnienie Time Series Insights ogólnej dostępności (GA) udostępni dane w ciągu 60 sekund od nadejścia do źródła zdarzeń.
> * W trakcie korzystania z wersji zapoznawczej oczekiwano dłuższego czasu, zanim dane zostaną udostępnione.
> * W przypadku wystąpienia jakichkolwiek znaczących opóźnień należy skontaktować się z nami.

### <a name="scale"></a>Skalowanie

Wersja zapoznawcza Time Series Insights obsługuje początkową skalę ruchu przychodzącego do 1 megabajtów na sekundę (MB/s) na środowisko. Trwa Ulepszona obsługa skalowania. Planujemy zaktualizować naszą dokumentację, aby odzwierciedlić te ulepszenia.

## <a name="parquet-file-format"></a>Format pliku Parquet

Parquet to format pliku danych zorientowany na kolumny, który został zaprojektowany dla:

* Współdziałanie
* Wydajność miejsca
* Wydajność zapytań

Time Series Insights wybrać Parquet, ponieważ zapewnia wydajne schematy kompresji i kodowania danych, z lepszą wydajnością, która może obsługiwać złożone dane zbiorczo.

Aby uzyskać więcej informacji na temat typu pliku Parquet, zapoznaj się z [dokumentacją Parquet](https://parquet.apache.org/documentation/latest/).

Aby uzyskać więcej informacji na temat formatu pliku Parquet na platformie Azure, zobacz [obsługiwane typy plików w usłudze Azure Storage](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs#parquet-format).

### <a name="event-structure-in-parquet"></a>Struktura zdarzeń w Parquet

Time Series Insights tworzy i przechowuje kopie obiektów BLOB w następujących dwóch formatach:

1. Pierwsza kopia początkowa jest partycjonowana według czasu przybycia:

    * `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * Godzina utworzenia obiektu BLOB dla obiektów BLOB partycjonowanych według czasu przybycia.

1. Druga kopia z podziałem na partycje jest partycjonowana przy użyciu dynamicznego grupowania identyfikatora szeregów czasowych:

    * `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * Minimalna sygnatura czasowa zdarzenia w obiekcie blob dla obiektów BLOB partycjonowanych według identyfikatora szeregów czasowych.

> [!NOTE]
> * `<YYYY>`mapuje do 4-cyfrowego reprezentacji.
> * `<MM>`mapuje na 2-cyfrowy reprezentację miesiąca.
> * `<YYYYMMDDHHMMSSfff>`mapuje na reprezentację sygnatury czasowej z`YYYY`4-cyfrowym rokiem ()`MM`, 2-cyfrowym miesiącem`DD`(), 2-cyfrowym`HH`dniem (), 2-cyfrową godziną (), 2`SS`-cyfrową minutą`MM`(), 2-cyfrową drugą () i 3-cyfrową milisekundy`fff`().

Zdarzenia Time Series Insights są mapowane do zawartości pliku Parquet w następujący sposób:

* Każde zdarzenie jest mapowane na jeden wiersz.
* Wbudowana kolumna **sygnatur czasowych** z sygnaturą czasową zdarzenia. Właściwość sygnatury czasowej nigdy nie ma wartości null. Wartość domyślna jest ustawiana na czas, w którym znajduje się **Źródło zdarzenia** , jeśli właściwość timestamp nie jest określona w źródle zdarzenia. Sygnatura czasowa jest w formacie UTC. 
* Wszystkie inne właściwości, które są mapowane na `_string` kolumny kończące się od (String), `_bool` ( `_datetime` wartość logiczna), ( `_double` DateTime) i (Double), w zależności od typu właściwości.
* Jest to schemat mapowania dla pierwszej wersji formatu pliku, do którego odwołuje się jako **V = 1**. W miarę rozwoju tej funkcji nazwa zostanie zwiększona do wartości **V = 2**, **v = 3**i tak dalej.

## <a name="azure-storage"></a>Azure Storage

W tej sekcji opisano szczegóły usługi Azure Storage istotne dla Azure Time Series Insights.

Aby uzyskać dokładny opis usługi Azure Blob Storage, przeczytaj temat [wprowadzenie do magazynu obiektów BLOB](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>Twoje konto magazynu

Po utworzeniu Time Series Insights środowiska z opcją płatność zgodnie z rzeczywistym użyciem należy utworzyć dwa zasoby: środowisko Time Series Insights i konto ogólnego przeznaczenia w wersji 1 do usługi Azure Storage, w którym będą przechowywane dane. Wybrano, aby usługa Azure Storage ogólnego przeznaczenia w wersji 1 była domyślnym zasobem ze względu na jego współdziałanie, cenę i wydajność.

Time Series Insights publikuje do dwóch kopii każdego zdarzenia na koncie usługi Azure Storage. Kopia początkowa jest zawsze zachowywana, dzięki czemu można szybko wykonać zapytania przy użyciu innych usług. Można łatwo korzystać z platformy Spark, Hadoop i innych znanych narzędzi w ramach identyfikatorów szeregów czasowych w przypadku nieprzetworzonych plików Parquet, ponieważ te aparaty obsługują podstawowe filtrowanie nazw plików. Grupowanie obiektów BLOB według roku i miesiąca to przydatny sposób wyświetlania obiektów BLOB w określonym zakresie czasu dla zadania niestandardowego.

Ponadto program Time Series Insights ponownie dzieli pliki Parquet na partycje w celu optymalizacji pod kątem Time Series Insights interfejsów API. Plik ostatnio ponownie podzielony na partycje jest również zapisywany.

W publicznej wersji zapoznawczej dane są przechowywane na koncie usługi Azure Storage na czas nieokreślony.

### <a name="writing-and-editing-time-series-insights-blobs"></a>Pisanie i edytowanie Time Series Insights obiektów BLOB

Aby zapewnić wydajność zapytań i dostępność danych, nie należy edytować ani usuwać obiektów blob, które są tworzone przez Time Series Insights.

> [!TIP]
> Niekorzystny wpływ na wydajność Time Series Insights może być w przypadku zbyt częstego odczytu lub zapisu w obiektach Blob.

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Uzyskiwanie dostępu do danych i eksportowanie ich z wersji zapoznawczej Time Series Insights

Możesz chcieć uzyskać dostęp do danych przechowywanych w Eksploratorze Time Series Insights w wersji zapoznawczej, aby użyć ich w połączeniu z innymi usługami. Na przykład możesz chcieć użyć danych do raportowania w Power BI, do wykonywania uczenia maszynowego przy użyciu Azure Machine Learning Studio lub do użycia w aplikacji notesu z notesami Jupyter.

Dostęp do danych można uzyskać na trzy sposoby:

* W Eksploratorze Time Series Insights w wersji zapoznawczej: dane można eksportować jako plik CSV z Eksploratora Time Series Insights Preview. Aby uzyskać więcej informacji, zobacz [Time Series Insights Explorer w wersji](./time-series-insights-update-explorer.md)zapoznawczej.
* Z poziomu interfejsów API programu Time Series Insights Preview: punkt końcowy interfejsu API można `/getRecorded`uzyskać pod adresem. Aby dowiedzieć się więcej na temat tego interfejsu API, zobacz [zapytanie szeregów czasowych](./time-series-insights-update-tsq.md).
* Bezpośrednio z konta usługi Azure Storage (poniżej).

#### <a name="from-an-azure-storage-account"></a>Z konta usługi Azure Storage

* Musisz mieć dostęp do odczytu do dowolnego konta, którego używasz, aby uzyskać dostęp do danych Time Series Insights. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem do zasobów konta magazynu](../storage/blobs/storage-manage-access-to-resources.md).
* Aby uzyskać więcej informacji na temat bezpośrednich sposobów odczytywania danych z usługi Azure Blob Storage, zobacz [Wybieranie rozwiązania platformy Azure na potrzeby transferu danych](../storage/common/storage-choose-data-transfer-solution.md).
* Aby wyeksportować dane z konta usługi Azure Storage:
    * Najpierw upewnij się, że Twoje konto spełnia wymagania wymagane do eksportowania danych. Aby uzyskać więcej informacji, zobacz [wymagania dotyczące importowania i eksportowania magazynu](../storage/common/storage-import-export-requirements.md).
    * Aby dowiedzieć się więcej na temat innych sposobów eksportowania danych z konta usługi Azure Storage, zobacz [Importowanie i eksportowanie danych z obiektów BLOB](../storage/common/storage-import-export-data-from-blobs.md).

### <a name="data-deletion"></a>Usuwanie danych

Nie usuwaj obiektów BLOB. Nie tylko są one przydatne do inspekcji i obsługi rekordu danych, a Time Series Insights w wersji zapoznawczej obsługuje metadane obiektów BLOB w ramach każdego obiektu BLOB.

## <a name="partitions"></a>Partycje

Każde środowisko Time Series Insights w wersji zapoznawczej musi mieć właściwość **identyfikatora szeregów czasowych** i Właściwość **sygnatury** czasowej, która jednoznacznie identyfikuje ją. Identyfikator szeregów czasowych działa jako partycja logiczna dla danych i zapewnia, Time Series Insights że środowisko wersji zapoznawczej jest naturalną granicą do dystrybucji danych między partycjami fizycznymi. Partycje fizyczne są zarządzane przez Time Series Insights w wersji zapoznawczej na koncie usługi Azure Storage.

Time Series Insights używa partycjonowania dynamicznego do optymalizowania wydajności magazynu i zapytań przez upuszczenie i ponowne tworzenie partycji. Algorytm partycjonowania dynamicznego Time Series Insights w wersji zapoznawczej próbuje uniemożliwić pojedynczej partycji fizycznej posiadanie danych dla wielu różnych partycji logicznych. Innymi słowy algorytm partycjonowania zachowuje wszystkie dane specyficzne dla pojedynczego identyfikatora szeregów czasowych w plikach Parquet, bez przeplotu przy użyciu innych identyfikatorów szeregów czasowych. Algorytm partycjonowania dynamicznego próbuje również zachować oryginalną kolejność zdarzeń w ramach jednego identyfikatora szeregów czasowych.

Początkowo w czasie, dane są partycjonowane przez sygnaturę czasową, dzięki czemu pojedynczej partycji logicznej w danym zakresie czasu można rozłożyć na wiele partycji fizycznych. Pojedyncza partycja fizyczna może również zawierać wiele lub wszystkie partycje logiczne. Ze względu na ograniczenia rozmiaru obiektów blob, nawet z optymalnym partycjonowaniem, pojedyncza partycja logiczna może zajmować wiele partycji fizycznych.

> [!NOTE]
> Domyślnie wartością sygnatury czasowej jest komunikat w *kolejce* w skonfigurowanym źródle zdarzenia.

W przypadku przekazywania danych historycznych lub komunikatów wsadowych należy przypisać wartość, która ma być przechowywana wraz z danymi, do właściwości sygnatury czasowej, która jest mapowana na odpowiednią sygnaturę czasową. Właściwość timestamp jest uwzględniana wielkość liter. Aby uzyskać więcej informacji, zobacz [model szeregów czasowych](./time-series-insights-update-tsm.md).

### <a name="physical-partitions"></a>Partycje fizyczne

Partycja fizyczna to blokowy obiekt BLOB, który jest przechowywany na koncie magazynu. Rzeczywisty rozmiar obiektów BLOB może się różnić, ponieważ rozmiar zależy od stawki wypychania. Jednak oczekujemy, że obiekty blob mają rozmiar około 20 MB do 50 MB. Oczekuje to, że zespół Time Series Insights wybrać 20 MB jako rozmiar, aby zoptymalizować wydajność zapytań. Ten rozmiar może ulec zmianie z upływem czasu, w zależności od rozmiaru pliku i szybkości transferu danych przychodzących.

> [!NOTE]
> * Rozmiar obiektów BLOB wynosi 20 MB.
> * Obiekty blob platformy Azure są czasami ponownie podzielona na partycje w celu uzyskania lepszej wydajności przez porzucenie i ponowne utworzenie.
> * Ponadto te same Time Series Insights dane mogą być obecne w co najmniej dwóch obiektach Blob.

### <a name="logical-partitions"></a>Partycje logiczne

Partycja logiczna to partycja w partycji fizycznej, w której przechowywane są wszystkie dane skojarzone z jedną wartością klucza partycji. Time Series Insights w wersji zapoznawczej logicznie dzieli każdy obiekt BLOB na podstawie dwóch właściwości:

* **Identyfikator szeregów czasowych**: Klucz partycji dla wszystkich danych Time Series Insights w strumieniu zdarzeń i modelu.
* **Sygnatura czasowa**: Czas oparty na początkowej operacji wejściowej.

Wersja zapoznawcza Time Series Insights zapewnia wykonywanie kwerend opartych na tych dwóch właściwościach. Te dwie właściwości również zapewniają najbardziej efektywną metodę dostarczania danych Time Series Insights szybko.

Ważne jest, aby wybrać odpowiedni identyfikator szeregów czasowych, ponieważ jest to niezmienna właściwość. Aby uzyskać więcej informacji, zobacz [Wybieranie identyfikatorów szeregów czasowych](./time-series-insights-update-how-to-id.md).

## <a name="next-steps"></a>Następne kroki

- Przeczytaj [Azure Time Series Insights w wersji zapoznawczej magazynu i transferu danych](./time-series-insights-update-storage-ingress.md)przychodzących.

- Przeczytaj o nowym [modelowaniu danych](./time-series-insights-update-tsm.md).
