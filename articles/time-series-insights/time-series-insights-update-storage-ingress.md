---
title: Magazyn danych i ruch przychodzący w wersji zapoznawczej Azure Time Series Insights | Microsoft Docs
description: Informacje na temat przechowywania i transferów danych w wersji zapoznawczej Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/23/2019
ms.custom: seodec18
ms.openlocfilehash: 0b61e194bdea5fd8272ffc0fc9e16a2d80d3cf60
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989709"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Magazyn danych i ruch przychodzący w wersji zapoznawczej Azure Time Series Insights

W tym artykule opisano aktualizacje usługi Data Storage i transfer danych przychodzących na potrzeby wersji zapoznawczej Azure Time Series Insights. Obejmuje ona podstawową strukturę magazynu, format pliku i Właściwość identyfikatora szeregów czasowych. Omówiono w nim również podstawowe procesy związane z transferem danych przychodzących, przepływność i ograniczenia.

## <a name="data-ingress"></a>Dane wejściowe

W programie Time Series Insights w wersji zapoznawczej zasady danych przychodzących określają, z których danych mogą pochodzić i jakie dane mają być dostępne w formacie.

[Omówienie modelu szeregów czasowych![](media/v2-update-storage-ingress/tsi-data-ingress.png)](media/v2-update-storage-ingress/tsi-data-ingress.png#lightbox)

### <a name="ingress-policies"></a>Zasady dotyczące transferu danych przychodzących

Wersja zapoznawcza Time Series Insights obsługuje te same źródła zdarzeń, które Time Series Insights obecnie obsługiwane:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Wersja zapoznawcza Time Series Insights obsługuje maksymalnie dwa źródła zdarzeń na wystąpienie.
  
Azure Time Series Insights obsługuje dane JSON przesłane za pomocą usługi Azure IoT Hub lub Azure Event Hubs. Aby zoptymalizować dane JSON IoT, Dowiedz się, [jak kształtować kod JSON](./time-series-insights-send-events.md#supported-json-shapes).

### <a name="data-storage"></a>Magazyn danych

W przypadku tworzenia Time Series Insights w wersji zapoznawczej dla środowiska SKU z opcją płatność zgodnie z rzeczywistym użyciem można utworzyć dwa zasoby platformy Azure:

* Środowisko Time Series Insights w wersji zapoznawczej, które opcjonalnie może obejmować możliwości magazynu ciepłego.
* Konto usługi Azure Storage ogólnego przeznaczenia w wersji 1 dla magazynu zimnych danych.

Dane w magazynie ciepłym są dostępne tylko za pośrednictwem [zapytania szeregów czasowych](./time-series-insights-update-tsq.md) i [Eksploratora Azure Time Series Insights Preview](./time-series-insights-update-explorer.md). 

Time Series Insights w wersji zapoznawczej zapisuje dane w chłodnym sklepie w usłudze Azure Blob Storage w [formacie pliku Parquet](#parquet-file-format-and-folder-structure). Time Series Insights wersja zapoznawcza zarządza wyłącznie tym zimnym magazynem danych, ale jest dostępny do odczytu bezpośrednio jako standardowe pliki Parquet.

> [!WARNING]
> Jako właściciel konta usługi Azure Blob Storage, na którym znajdują się dane w chłodnym sklepie, masz pełny dostęp do wszystkich danych na koncie. Ten dostęp obejmuje uprawnienia do zapisu i usuwania. Nie Edytuj ani nie usuwaj danych, które Time Series Insights zapisywania w wersji zapoznawczej, ponieważ może to spowodować utratę danych.

### <a name="data-availability"></a>Dostępność danych
Time Series Insights Podgląd partycji i indeksowanie danych w celu uzyskania optymalnej wydajności zapytań. Dane będą dostępne do zbadania po jego indeksowaniu. Ilość danych, które są pozyskiwane, może mieć wpływ na tę dostępność.

> [!IMPORTANT]
> Wydanie ogólnej dostępności (GA) Time Series Insights udostępni dane w ciągu 60 sekund od momentu odczytania ze źródła zdarzeń. W trakcie korzystania z wersji zapoznawczej może wystąpić dłuższy okres, po upływie którego dane staną się dostępne. Jeśli wystąpi znaczący czas oczekiwania przekraczający 60 sekund, skontaktuj się z nami.

### <a name="scale"></a>Skalowanie

Domyślnie Time Series Insights wersja zapoznawcza obsługuje początkową skalę ruchu przychodzącego do 1 megabajtów na sekundę (MB/s) na środowisko. W razie potrzeby jest dostępna przepływność o rozmiarze do 16 MB/s. Jeśli potrzebujesz rozszerzonej pomocy technicznej skalowania, skontaktuj się z nami.

Możesz uzyskać dodatkowe możliwości związane z ruchem przychodzącym i skalowaniem dla źródła zdarzeń:

* [IoT Hub](../iot-hub/iot-hub-scaling.md)
* [Event Hubs](../event-hubs/event-hubs-scalability.md)

## <a name="azure-storage"></a>Azure Storage

W tej sekcji opisano szczegóły usługi Azure Storage istotne dla Azure Time Series Insights wersji zapoznawczej.

Aby uzyskać dokładny opis magazynu obiektów blob platformy Azure, Przeczytaj [wprowadzenie do magazynu obiektów BLOB](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>Twoje konto magazynu

Po utworzeniu usługi Time Series Insights w wersji zapoznawczej (płatność zgodnie z rzeczywistym użyciem) konto usługi Azure Storage ogólnego przeznaczenia w wersji 1 zostanie utworzone jako długoterminowy chłodny magazyn.  

Wersja zapoznawcza Time Series Insights publikuje do dwóch kopii każdego zdarzenia na koncie usługi Azure Storage. Kopia początkowa ma zdarzenia uporządkowane według czasu pozyskiwania i jest zawsze zachowywana, dzięki czemu można uzyskać do niej dostęp za pomocą innych usług. Do przetwarzania nieprzetworzonych plików Parquet można używać platformy Spark, usługi Hadoop i innych znanych narzędzi. 

Time Series Insights wersja zapoznawcza ponownie dzieli na partycje pliki Parquet, które mają zostać zoptymalizowane dla Time Series Insights kwerendy. Ta podzielona na partycje kopia danych również jest zapisywana.

W publicznej wersji zapoznawczej dane są przechowywane na koncie usługi Azure Storage na czas nieokreślony.

### <a name="writing-and-editing-time-series-insights-blobs"></a>Pisanie i edytowanie Time Series Insights obiektów BLOB

Aby zapewnić wydajność zapytań i dostępność danych, nie należy edytować ani usuwać obiektów blob, które są tworzone Time Series Insights Podgląd.

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Uzyskiwanie dostępu do danych i eksportowanie ich z wersji zapoznawczej Time Series Insights

Możesz chcieć uzyskać dostęp do danych wyświetlanych w Eksploratorze Time Series Insights w wersji zapoznawczej, aby użyć ich w połączeniu z innymi usługami. Na przykład możesz użyć danych do tworzenia raportu w Power BI lub do uczenia modelu uczenia maszynowego przy użyciu Azure Machine Learning Studio. Możesz też użyć danych do przekształcenia, wizualizowania i modelowania w notesach Jupyter.

Dostęp do danych można uzyskać na trzy sposoby:

* W Eksploratorze Time Series Insights w wersji zapoznawczej. Dane można eksportować jako plik CSV z Eksploratora. Aby uzyskać więcej informacji, zobacz [Time Series Insights Explorer w wersji zapoznawczej](./time-series-insights-update-explorer.md).
* Z poziomu interfejsu API usługi Time Series Insights Preview. Punkt końcowy interfejsu API można uzyskać w `/getRecorded`. Aby dowiedzieć się więcej na temat tego interfejsu API, zobacz [zapytanie szeregów czasowych](./time-series-insights-update-tsq.md).
* Bezpośrednio z konta usługi Azure Storage. Musisz mieć dostęp do odczytu do dowolnego konta, którego używasz, aby uzyskać dostęp do danych w wersji zapoznawczej Time Series Insights. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem do zasobów konta magazynu](../storage/blobs/storage-manage-access-to-resources.md).

### <a name="data-deletion"></a>Usuwanie danych

Nie usuwaj plików w wersji zapoznawczej Time Series Insights. Pokrewnymi danymi należy zarządzać wyłącznie w programie Time Series Insights Preview.

## <a name="parquet-file-format-and-folder-structure"></a>Parquet format pliku i struktura folderów

Parquet to format pliku kolumnowego Open Source, który został zaprojektowany w celu zapewnienia wydajnego magazynu i wydajności. Z tych powodów Time Series Insights w wersji zapoznawczej program używa Parquet. Umożliwia ona Partycjonowanie danych według identyfikatora szeregów czasowych na potrzeby wykonywania zapytań na dużą skalę.  

Więcej informacji o typie pliku Parquet można znaleźć w [dokumentacji Parquet](https://parquet.apache.org/documentation/latest/).

Time Series Insights w wersji zapoznawczej przechowuje kopie danych w następujący sposób:

* Pierwsza kopia początkowa jest podzielona na partycje według czasu pozyskiwania i przechowuje dane w sposób przybliżony w kolejności przybycia. Dane znajdują się w folderze `PT=Time`:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* Druga kopia z podziałem na partycje jest partycjonowana według grupowania identyfikatorów szeregów czasowych i znajduje się w folderze `PT=TsId`:

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

W obu przypadkach wartości czasu odpowiadają czasowi utworzenia obiektu BLOB. Dane w folderze `PT=Time` są zachowywane. Dane w folderze `PT=TsId` zostaną zoptymalizowane pod kątem zapytania w czasie i nie będą statyczne.

> [!NOTE]
> * `<YYYY>` mapuje do czwartej reprezentacji roku.
> * `<MM>` mapuje na dwucyfrowy reprezentację miesiąca.
> * `<YYYYMMDDHHMMSSfff>` mapuje do sygnatury czasowej z czterocyfrowym rokiem (`YYYY`), dwucyfrowym miesiącem (`MM`), dwucyfrowym i dwucyfrowym (`DD`), godziną dwucyfrową (`HH`), dwucyfrową minutą (`MM`), cyfrą sekundową (`SS`) i 3-cyfrową milisekundą (`fff`).

Zdarzenia w wersji zapoznawczej Time Series Insights są mapowane do zawartości pliku Parquet w następujący sposób:

* Każde zdarzenie jest mapowane na jeden wiersz.
* Każdy wiersz zawiera kolumnę **timestamp** z sygnaturą czasową zdarzenia. Właściwość sygnatury czasowej nigdy nie ma wartości null. Jeśli właściwość sygnatury czasowej nie została określona w źródle zdarzeń, domyślnie przyjmowana jest wartość domyślna **czasu zdarzenia** . Sygnatura czasowa jest zawsze w formacie UTC.
* Każdy wiersz zawiera kolumnę identyfikatora szeregów czasowych, która została zdefiniowana podczas tworzenia środowiska Time Series Insights. Nazwa właściwości zawiera sufiks `_string`.
* Wszystkie inne właściwości wysyłane jako dane telemetryczne są mapowane na nazwy kolumn kończące się na `_string` (ciąg), `_bool` (wartość logiczna), `_datetime` (DateTime) lub `_double` (Double), w zależności od typu właściwości.
* Ten schemat mapowania dotyczy pierwszej wersji formatu pliku, do którego odwołuje się wartość **V = 1**. W miarę rozwoju tej funkcji można zwiększyć nazwę.

## <a name="next-steps"></a>Następne kroki

- Przeczytaj [Azure Time Series Insights w wersji zapoznawczej magazynu i transferu danych](./time-series-insights-update-storage-ingress.md)przychodzących.

- Przeczytaj o nowym [modelowaniu danych](./time-series-insights-update-tsm.md).
