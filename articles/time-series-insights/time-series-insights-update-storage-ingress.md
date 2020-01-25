---
title: Magazyn danych i ruch przychodzący w wersji zapoznawczej — Azure Time Series Insights | Microsoft Docs
description: Dowiedz się więcej na temat przechowywania i transferów danych w wersji zapoznawczej Azure Time Series Insights.
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/31/2019
ms.custom: seodec18
ms.openlocfilehash: f00529d00312fd6acb045de698590047f991bec7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76714290"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Magazyn danych i ruch przychodzący w wersji zapoznawczej Azure Time Series Insights

W tym artykule opisano aktualizacje usługi Data Storage i transfer danych przychodzących na potrzeby wersji zapoznawczej Azure Time Series Insights. Obejmuje ona podstawową strukturę magazynu, format pliku i Właściwość identyfikatora szeregów czasowych. Omówiono w nim również podstawowe procesy związane z transferem danych przychodzących, najlepsze rozwiązania i bieżące ograniczenia wersji zapoznawczej.

## <a name="data-ingress"></a>Dane wejściowe

Środowisko Azure Time Series Insights zawiera aparat pozyskiwania do zbierania, przetwarzania i przechowywania danych szeregów czasowych. Podczas planowania środowiska należy wziąć pod uwagę pewne zagadnienia, aby zapewnić, że wszystkie dane przychodzące są przetwarzane i aby osiągnąć wysoką skalę transferu danych przychodzących i zminimalizować opóźnienia pozyskiwania (czas potrzebny przez TSI do odczytu i przetworzenia z zdarzenia Źródło). 

W programie Time Series Insights w wersji zapoznawczej zasady danych przychodzących określają, z których danych mogą pochodzić i jakie dane mają być dostępne w formacie.

### <a name="ingress-policies"></a>Zasady dotyczące transferu danych przychodzących

#### <a name="event-sources"></a>Źródła zdarzeń

Wersja zapoznawcza Time Series Insights obsługuje następujące źródła zdarzeń:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Wersja zapoznawcza Time Series Insights obsługuje maksymalnie dwa źródła zdarzeń na wystąpienie.

> [!WARNING] 
> * W przypadku dołączania źródła zdarzeń do środowiska w wersji zapoznawczej mogą wystąpić wysokie początkowe opóźnienia. 
> Opóźnienie źródła zdarzenia zależy od liczby zdarzeń znajdujących się obecnie w IoT Hub lub centrum zdarzeń.
> * Duże opóźnienie zostanie umieszczone po pierwszym pozyskaniu danych źródła zdarzenia. Skontaktuj się z nami, przesyłając bilet pomocy technicznej przez Azure Portal, jeśli będziesz mieć ciągłe duże opóźnienia.

#### <a name="supported-data-format-and-types"></a>Obsługiwane formaty i typy danych

Azure Time Series Insights obsługuje kod JSON zakodowany w formacie UTF8 za pomocą usługi Azure IoT Hub lub Azure Event Hubs. 

Poniżej znajduje się lista obsługiwanych typów danych.

| Typ danych | Opis |
|-----------|------------------|-------------|
| bool      |   Typ danych, który ma jeden z dwóch stanów: true lub false.       |
| Data i godzina    |   Reprezentuje chwilę w czasie, zwykle wyrażoną jako datę i godzinę dnia. Daty i godziny powinny mieć format ISO 8601.      |
| double    |   Podwójna precyzja 64-bitowa IEEE 754 zmiennoprzecinkowa
| string    |   Wartości tekstowe składające się z znaków Unicode.          |

#### <a name="objects-and-arrays"></a>Obiekty i tablice

Można wysyłać złożone typy, takie jak obiekty i tablice, w ramach ładunku zdarzenia, ale dane zostaną przetworzone przez proces spłaszczania w przypadku przechowywania. Aby uzyskać więcej informacji na temat sposobu tworzenia kształtów zdarzeń w formacie JSON, a także szczegółów dotyczących typu złożonego i spłaszczonych obiektów zagnieżdżonych, zobacz stronę dotyczącą [sposobu tworzenia kształtów JSON dla](./time-series-insights-update-how-to-shape-events.md)ruchu przychodzącego i zapytań.


### <a name="ingress-best-practices"></a>Najlepsze rozwiązania związane z transferem danych przychodzących

Zalecamy stosowanie następujących najlepszych rozwiązań:

* Skonfiguruj Time Series Insights i IoT Hub lub centrum zdarzeń w tym samym regionie, aby zmniejszyć opóźnienie pozyskiwania sieci.
* Zaplanuj potrzeby skalowania, obliczając przewidywany wskaźnik pozyskiwania i sprawdzając, czy znajduje się on w ramach obsługiwanej stawki wymienionej poniżej
* Dowiedz się, jak optymalizować i kształtować dane JSON, a także bieżące ograniczenia w wersji zapoznawczej, odczytując [Informacje o sposobie tworzenia kształtu JSON dla](./time-series-insights-update-how-to-shape-events.md)ruchu przychodzącego i zapytań.

### <a name="ingress-scale-and-limitations-in-preview"></a>Skala ruchu przychodzącego i ograniczenia w wersji zapoznawczej

#### <a name="per-environment-limitations"></a>Ograniczenia dotyczące środowiska

Ogólnie rzecz biorąc, stawki za transfer danych przychodzących są wyświetlane jako współczynnik liczby urządzeń w organizacji, częstotliwości emisji zdarzeń i rozmiaru każdego zdarzenia:

*  **Liczba urządzeń** × **częstotliwość emisji zdarzeń** × **rozmiar każdego zdarzenia**.

Domyślnie Time Series Insights w wersji zapoznawczej może przyjmować dane przychodzące z szybkością do 1 MB na sekundę (MB/s) **na środowisko TSI**. Skontaktuj się z nami, jeśli to nie spełnia wymagań, możemy obsłużyć do 16 MB/s dla środowiska przez przesłanie biletu pomocy technicznej w Azure Portal.
 
Przykład 1: wysyłka contoso obejmuje 100 000 urządzeń, które emitują wydarzenie trzy razy na minutę. Rozmiar zdarzenia to 200 bajtów. Korzystają one z centrum zdarzeń z 4 partycjami jako źródłem zdarzeń TSI.
Szybkość pozyskiwania dla środowiska TSI: 100 000 urządzeń * 200 bajtów/zdarzenia * (3/60 Event/s) = 1 MB/s.
Szybkość pozyskiwania na partycję 0,25 MB/s.
Stawka pozyskiwania kosztów wysyłki firmy Contoso będzie w ramach ograniczenia skalowania w wersji zapoznawczej.
 
Przykład 2: Analiza floty Contoso ma 60 000 urządzeń, które emitują zdarzenia co sekundę. Używają one IoT Hub 24 liczba partycji 4 jako źródło zdarzeń TSI. Rozmiar zdarzenia to 200 bajtów.
Szybkość pozyskiwania środowiska: 20 000 urządzeń * 200 bajtów/zdarzenie * 1 zdarzenie/s = 4 MB/s.
Stawka za partycje wynosi 1 MB/s.
Analiza floty firmy Contoso musi przesłać żądanie do TSI za pośrednictwem Azure Portal dla dedykowanego środowiska w celu osiągnięcia tej skali.

#### <a name="hub-partitions-and-per-partition-limits"></a>Partycje centrum i limity na partycję

Podczas planowania środowiska TSI należy rozważyć konfigurację źródeł zdarzeń, które będą nawiązywane połączenie z TSI. Zarówno IoT Hub platformy Azure, jak i Event Hubs używają partycji, aby włączyć skalowanie w poziomie na potrzeby przetwarzania zdarzeń.  Partycja to uporządkowana sekwencja zdarzeń, które są przechowywane w centrum. Liczba partycji jest ustawiana w fazie tworzenia IoT lub Event Hubs "i nie można jej zmienić. Aby uzyskać więcej informacji na temat określania liczby partycji, zobacz Event Hubs "często zadawane pytania dotyczące liczby potrzebnych partycji? W przypadku środowisk z TSI przy użyciu IoT Hub większość centrów IoT potrzebuje tylko 4 partycji. Bez względu na to, czy tworzysz nowe centrum dla środowiska TSI, czy też korzystasz z istniejącego, musisz obliczyć współczynnik pozyskiwania partycji, aby określić, czy jest on w ramach limitów wersji zapoznawczej. Wersja zapoznawcza w wersji zapoznawczej ma obecnie limit **partycji** wynoszący 0,5 MB/s. Skorzystaj z przykładów poniżej jako odwołania, a jeśli jesteś użytkownikiem IoT Hub, zwróć uwagę na następujące informacje IoT Hub.

#### <a name="iot-hub-specific-considerations"></a>Zagadnienia dotyczące IoT Hub

Gdy urządzenie zostanie utworzone w IoT Hub jest przypisane do partycji, a przypisanie partycji nie zostanie zmienione. Dzięki temu IoT Hub jest w stanie zagwarantować porządkowanie zdarzeń. Jednak ma to wpływ na TSI jako czytnik podrzędny w niektórych scenariuszach. Gdy komunikaty z wielu urządzeń są przekazywane do centrum przy użyciu tego samego identyfikatora urządzenia bramy, będą docierane do tej samej partycji, co może przekroczyć ograniczenie skalowania na partycję. 

**Wpływ**: Jeśli jedna partycja ma stałą częstotliwość pozyskiwania w ramach ograniczenia wersji zapoznawczej, istnieje możliwość, że czytnik TSI nie zostanie przestawiony przed przekroczeniem okresu przechowywania danych IoT Hub. Mogłoby to spowodować utratę danych.

Zalecamy następujące czynności: 

* Przed wdrożeniem rozwiązania Oblicz liczbę dla poszczególnych środowisk i przypadaj na partycje.
* Upewnij się, że urządzenia IoT Hub (i w ten sposób partycje) są zrównoważone względem najdalej możliwego zwiększenia

> [!WARNING]
> W przypadku środowisk, w których IoT Hub jako źródło zdarzeń, Oblicz wskaźnik pozyskiwania przy użyciu liczby używanych urządzeń Hub, aby upewnić się, że stawka spadnie poniżej limitu 0,5 MB/s na partycję w wersji zapoznawczej.

  ![Diagram partycji IoT Hub](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Aby uzyskać więcej informacji na temat jednostek przepływności i partycji, zobacz następujące linki:

* [Skalowanie IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Skala centrum zdarzeń](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Partycje centrum zdarzeń](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

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
> W trakcie korzystania z wersji zapoznawczej może wystąpić okres do 60 sekund, po upływie którego dane staną się dostępne. Jeśli wystąpi znaczący czas oczekiwania przekraczający 60 sekund, Prześlij bilet pomocy technicznej za pomocą Azure Portal.

## <a name="azure-storage"></a>Azure Storage

W tej sekcji opisano szczegóły usługi Azure Storage istotne dla Azure Time Series Insights wersji zapoznawczej.

Aby uzyskać dokładny opis magazynu obiektów blob platformy Azure, Przeczytaj [wprowadzenie do magazynu obiektów BLOB](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>Twoje konto magazynu

Po utworzeniu usługi Time Series Insights w wersji zapoznawczej (płatność zgodnie z rzeczywistym użyciem) konto usługi Azure Storage ogólnego przeznaczenia w wersji 1 zostanie utworzone jako długoterminowy chłodny magazyn.  

Wersja zapoznawcza Time Series Insights publikuje do dwóch kopii każdego zdarzenia na koncie usługi Azure Storage. Kopia początkowa ma zdarzenia uporządkowane według czasu pozyskiwania i jest zawsze zachowywana, dzięki czemu można uzyskać do niej dostęp za pomocą innych usług. Do przetwarzania nieprzetworzonych plików Parquet można używać platformy Spark, usługi Hadoop i innych znanych narzędzi. 

Time Series Insights wersja zapoznawcza ponownie dzieli na partycje pliki Parquet, które mają zostać zoptymalizowane dla Time Series Insights kwerendy. Ta podzielona na partycje kopia danych również jest zapisywana.

W publicznej wersji zapoznawczej dane są przechowywane na koncie usługi Azure Storage na czas nieokreślony.

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Pisanie i edytowanie Time Series Insights obiektów BLOB

Aby zapewnić wydajność zapytań i dostępność danych, nie należy edytować ani usuwać obiektów blob, które są tworzone Time Series Insights Podgląd.

#### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Uzyskiwanie dostępu do danych i eksportowanie ich z wersji zapoznawczej Time Series Insights

Możesz chcieć uzyskać dostęp do danych wyświetlanych w Eksploratorze Time Series Insights w wersji zapoznawczej, aby użyć ich w połączeniu z innymi usługami. Na przykład możesz użyć danych do tworzenia raportu w Power BI lub do uczenia modelu uczenia maszynowego przy użyciu Azure Machine Learning Studio. Możesz też użyć danych do przekształcenia, wizualizowania i modelowania w notesach Jupyter.

Dostęp do danych można uzyskać na trzy sposoby:

* W Eksploratorze Time Series Insights w wersji zapoznawczej. Dane można eksportować jako plik CSV z Eksploratora. Aby uzyskać więcej informacji, Przeczytaj [Time Series Insights Explorer w wersji zapoznawczej](./time-series-insights-update-explorer.md).
* Z poziomu interfejsu API usługi Time Series Insights Preview przy użyciu zapytania Get Events. Aby dowiedzieć się więcej na temat tego interfejsu API, Przeczytaj [zapytanie dotyczące szeregów czasowych](./time-series-insights-update-tsq.md).
* Bezpośrednio z konta usługi Azure Storage. Musisz mieć dostęp do odczytu do dowolnego konta, którego używasz, aby uzyskać dostęp do danych w wersji zapoznawczej Time Series Insights. Aby uzyskać więcej informacji, zapoznaj się z artykułem [Zarządzanie dostępem do zasobów konta magazynu](../storage/blobs/storage-manage-access-to-resources.md).

#### <a name="data-deletion"></a>Usuwanie danych

Nie usuwaj plików w wersji zapoznawczej Time Series Insights. Zarządzaj powiązanymi danymi wyłącznie w programie Time Series Insights Preview.

### <a name="parquet-file-format-and-folder-structure"></a>Parquet format pliku i struktura folderów

Parquet to format pliku kolumnowego Open Source, który został zaprojektowany w celu zapewnienia wydajnego magazynu i wydajności. Z tych powodów Time Series Insights w wersji zapoznawczej program używa Parquet. Umożliwia ona Partycjonowanie danych według identyfikatora szeregów czasowych na potrzeby wykonywania zapytań na dużą skalę.  

Aby uzyskać więcej informacji na temat typu pliku Parquet, Przeczytaj [dokumentację dotyczącą Parquet](https://parquet.apache.org/documentation/latest/).

Time Series Insights w wersji zapoznawczej przechowuje kopie danych w następujący sposób:

* Pierwsza kopia początkowa jest podzielona na partycje według czasu pozyskiwania i przechowuje dane w sposób przybliżony w kolejności przybycia. Dane znajdują się w folderze `PT=Time`:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* Druga kopia z podziałem na partycje jest partycjonowana według grupowania identyfikatorów szeregów czasowych i znajduje się w folderze `PT=TsId`:

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

W obu przypadkach wartości czasu odpowiadają czasowi utworzenia obiektu BLOB. Dane w folderze `PT=Time` są zachowywane. Dane w folderze `PT=TsId` zostaną zoptymalizowane pod kątem zapytania w czasie i nie będą statyczne.

> [!NOTE]
> * `<YYYY>` mapuje do czwartej reprezentacji roku.
> * `<MM>` mapuje na dwucyfrowy reprezentację miesiąca.
> * `<YYYYMMDDHHMMSSfff>` mapuje do sygnatury czasowej przy użyciu czterocyfrowego roku (`YYYY`), dwucyfrowego miesiąca (`MM`), dwucyfrowego dnia (`DD`), godziny dwucyfrowej (`HH`), dwucyfrowej minuty (`MM`), dwucyfrowej sekundy (`SS`) i 3-cyfrowy milisekundy (`fff`).

Zdarzenia w wersji zapoznawczej Time Series Insights są mapowane do zawartości pliku Parquet w następujący sposób:

* Każde zdarzenie jest mapowane na jeden wiersz.
* Każdy wiersz zawiera kolumnę **timestamp** z sygnaturą czasową zdarzenia. Właściwość sygnatury czasowej nigdy nie ma wartości null. Jeśli właściwość sygnatury czasowej nie została określona w źródle zdarzeń, domyślnie przyjmowana jest wartość domyślna **czasu zdarzenia** . Sygnatura czasowa jest zawsze w formacie UTC.
* Każdy wiersz zawiera kolumny identyfikatorów szeregów czasowych zdefiniowane podczas tworzenia środowiska Time Series Insights. Nazwa właściwości zawiera sufiks `_string`.
* Wszystkie inne właściwości wysyłane jako dane telemetryczne są mapowane na nazwy kolumn kończące się na `_string` (ciąg), `_bool` (wartość logiczna), `_datetime` (DateTime) lub `_double` (Double), w zależności od typu właściwości.
* Ten schemat mapowania dotyczy pierwszej wersji formatu pliku, do którego odwołuje się wartość **V = 1**. W miarę rozwoju tej funkcji można zwiększyć nazwę.

## <a name="next-steps"></a>Następne kroki

- Przeczytaj [, jak Shape JSON dla](./time-series-insights-update-how-to-shape-events.md)ruchu przychodzącego i zapytań.

- Przeczytaj o nowym [modelowaniu danych](./time-series-insights-update-tsm.md).
