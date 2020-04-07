---
title: Przechowywanie i przychyłów danych w wersji zapoznawczej — usługa Azure Time Series Insights | Dokumenty firmy Microsoft
description: Dowiedz się więcej o przechowywaniu danych i transferze danych w usłudze Azure Time Series Insights Preview.
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/10/2020
ms.custom: seodec18
ms.openlocfilehash: 95a579cacc339360295f5f25fa6415ab29cd68ff
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673896"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Przechowywanie danych i ruch przychodzący w usłudze Azure Time Series Insights Preview

W tym artykule opisano aktualizacje magazynu danych i transferu danych przychodzących dla usługi Azure Time Series Insights Preview. Opisano w nim podstawową strukturę magazynu, format pliku i właściwość identyfikator szeregów czasowych. Opisano również podstawowy proces transferu danych przychodzących, najlepsze rozwiązania i bieżące ograniczenia wersji zapoznawczej.

## <a name="data-ingress"></a>Transfer danych

Środowisko usługi Azure Time Series Insights zawiera *aparat pozyskiwania* do zbierania, przetwarzania i przechowywania danych szeregów czasowych. 

Istnieją pewne kwestie, o których należy pamiętać, aby upewnić się, że wszystkie przychodzące dane są przetwarzane, aby osiągnąć skalę wysokiego transferu danych przychodzących i zminimalizować *opóźnienie pozyskiwania* (czas pochłonięty przez time series insights do odczytu i przetwarzania danych ze źródła zdarzeń) podczas [planowania środowiska.](time-series-insights-update-plan.md)

Zasady transferu danych danych usługi Time Series Insights w wersji zapoznawczej określają, skąd dane mogą pochodzić i jaki format powinny mieć dane.

### <a name="ingress-policies"></a>Zasady transferu przychodzącego

*Transfer danych* obejmuje sposób wysyłania danych do środowiska usługi Azure Time Series Insights Preview. 

Poniżej podsumowano konfigurację kluczy, formatowanie i najważniejsze wskazówki.

#### <a name="event-sources"></a>Źródła zdarzeń

Usługa Azure Time Series Insights Preview obsługuje następujące źródła zdarzeń:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Usługa Azure Time Series Insights Preview obsługuje maksymalnie dwa źródła zdarzeń na wystąpienie. Po podłączeniu źródła zdarzeń środowisko TSI odczyta wszystkie zdarzenia aktualnie przechowywane w centrum Iot lub Event Hub, począwszy od najstarszego zdarzenia. 

> [!IMPORTANT] 
> * Podczas dołączania źródła zdarzeń do środowiska w wersji zapoznawczej może wystąpić duże opóźnienie początkowe. 
> Opóźnienie źródła zdarzeń zależy od liczby zdarzeń aktualnie w centrum IoT hub lub centrum zdarzeń.
> * Duże opóźnienie ustąpi po pierwszym spożyciu danych źródła zdarzeń. Prześlij zgłoszenie pomocy technicznej za pośrednictwem witryny Azure portal, jeśli wystąpi ciągłe duże opóźnienia.

#### <a name="supported-data-format-and-types"></a>Obsługiwany format i typy danych

Usługa Azure Time Series Insights obsługuje kodowany json zgodnie z utf-8 wysyłany z usługi Azure IoT Hub lub usługi Azure Event Hubs. 

Obsługiwane typy danych to:

| Typ danych | Opis |
|---|---|
| **bool** | Typ danych o jednym z `true` `false`dwóch stanów: lub . |
| **Datetime** | Reprezentuje chwilę w czasie, zwykle wyrażoną jako data i godzina dnia. Wyrażona w formacie [ISO 8601.](https://www.iso.org/iso-8601-date-and-time-format.html) |
| **double** | Podwójna precyzja 64-bitowy [IEEE 754](https://ieeexplore.ieee.org/document/8766229) zmiennoprzecinkowy. |
| **Ciąg** | Wartości tekstowe, składające się ze znaków Unicode.          |

#### <a name="objects-and-arrays"></a>Obiekty i tablice

Możesz wysyłać złożone typy, takie jak obiekty i tablice jako część ładunku zdarzeń, ale dane zostaną poddane procesowi spłaszczania podczas przechowywania. 

Szczegółowe informacje opisujące sposób kształtowania zdarzeń JSON, wysyłania typów złożonych i spłaszczania obiektów zagnieżdżonych są dostępne w [obszarze Jak kształtować JSON dla ruchu przychodzącego i kwerendy,](./time-series-insights-update-how-to-shape-events.md) aby pomóc w planowaniu i optymalizacji.

### <a name="ingress-best-practices"></a>Najważniejsze wskazówki dotyczące transferu przychodzącego

Zaleca się stosowanie następujących sprawdzonych rozwiązań:

* Skonfiguruj usługę Azure Time Series Insights i dowolną usługę IoT Hub lub Centrum zdarzeń w tym samym regionie, aby zmniejszyć potencjalne opóźnienia.

* [Zaplanuj swoje potrzeby skalowania,](time-series-insights-update-plan.md) obliczając przewidywany wskaźnik spożycia i sprawdzając, czy mieści się on w obsługiwanym tempie podanym poniżej.

* Dowiedz się, jak zoptymalizować i kształtować dane JSON, a także bieżące ograniczenia w wersji zapoznawczej, czytając sposób [kształtowania JSON dla ruchu przychodzącego i zapytania](./time-series-insights-update-how-to-shape-events.md).

### <a name="ingress-scale-and-preview-limitations"></a>Ograniczenia skali transferu wnika i podglądu 

Ograniczenia transferu danych przychodzących usługi Azure Time Series Insights w wersji zapoznawczej są opisane poniżej.

> [!TIP]
> Przeczytaj [artykuł Planowanie środowiska w wersji zapoznawczej,](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits) aby uzyskać pełną listę wszystkich limitów wersji zapoznawczej.

#### <a name="per-environment-limitations"></a>Na ograniczenia środowiskowe

Ogólnie rzecz biorąc, współczynniki transferu ruchu przychodzącego są wyświetlane jako współczynnik liczby urządzeń w organizacji, częstotliwość emisji zdarzeń i rozmiar każdego zdarzenia:

*  **Liczba urządzeń** × **Częstotliwość emisji zdarzeń** × Rozmiar każdego **zdarzenia**.

Domyślnie usługa Time Series Insights umożliwia pozyskiwania przychodzących danych z szybkością **do 1 megabajta na sekundę (MB/s) w środowisku usługi Time Series Insights**. Istnieją dodatkowe ograniczenia [na partycję koncentratora](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#hub-partitions-and-per-partition-limits).

> [!TIP] 
> * Na życzenie można zapewnić obsługę środowiska dla pozyskiwania prędkości do 16 MB/s.
> * Skontaktuj się z nami, jeśli potrzebujesz wyższej przepływności, przesyłając zgłoszenie pomocy technicznej za pośrednictwem witryny Azure portal.
 
* **Przykład 1:**

    Contoso Shipping ma 100 000 urządzeń, które emitują zdarzenie trzy razy na minutę. Rozmiar zdarzenia wynosi 200 bajtów. Używają centrum Iot z czterema partycjami jako źródłem zdarzeń usługi Time Series Insights.

    * Szybkość pozyskiwania dla ich środowiska Time Series Insights wynosi: **100 000 urządzeń * 200 bajtów/zdarzenie * (zdarzenie 3/60/s) = 1 MB/s**.
    * Szybkość pozyskiwania na partycję wynosi 0,25 MB/s.
    * Szybkość pozyskiwania usługi Contoso Shipping mieściłaby się w ograniczeniu skali wersji zapoznawczej.

* **Przykład 2:**

    Contoso Fleet Analytics ma 60 000 urządzeń, które emitują zdarzenie co sekundę. Używają Centrum zdarzeń z liczbą partycji 4 jako źródło zdarzeń usługi Time Series Insights. Rozmiar zdarzenia wynosi 200 bajtów.

    * Szybkość pozyskiwania środowiska wynosi: **60 000 urządzeń * 200 bajtów/zdarzenie * 1 zdarzenie/s = 12 MB/s**.
    * Stawka za partycję wynosi 3 MB/s.
    * Szybkość pozyskiwania przez firmę Contoso Fleet Analytics jest prześcigzona w granicach środowiska i partycji. Mogą przesłać żądanie do usługi Time Series Insights za pośrednictwem witryny Azure portal, aby zwiększyć szybkość pozyskiwania dla swojego środowiska i utworzyć Centrum zdarzeń z większą liczoną partycją, która ma mieszczeć się w granicach wersji zapoznawczej.

#### <a name="hub-partitions-and-per-partition-limits"></a>Partycje koncentratora i limity partycji

Planując środowisko usługi Time Series Insights, należy wziąć pod uwagę konfigurację źródeł zdarzeń, które będą łączyć się z usługą Time Series Insights. Usługa Azure IoT Hub i usługi Event Hubs wykorzystują partycje, aby włączyć skalę poziomą do przetwarzania zdarzeń. 

*Partycja* jest uporządkowaną sekwencją zdarzeń przechowywanych w centrum. Liczba partycji jest ustawiana podczas fazy tworzenia koncentratora i nie można jej zmienić. 

W przypadku centrów zdarzeń partycjonowania najlepszych rozwiązań, sprawdź ile [partycji muszę?](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)

> [!NOTE]
> Większość centrów IoT używanych z usługą Azure Time Series Insights potrzebuje tylko czterech partycji.

Niezależnie od tego, czy tworzysz nowe centrum dla środowiska usługi Time Series Insights, czy też używasz istniejącego, musisz obliczyć szybkość pozyskiwania na partycje, aby określić, czy mieści się on w granicach podglądu. 

Usługa Azure Time Series Insights Preview ma obecnie ogólny **limit na partycję 0,5 MB/s.**

#### <a name="iot-hub-specific-considerations"></a>Zagadnienia specyficzne dla usługi IoT Hub

Gdy urządzenie jest tworzone w Centrum IoT, jest trwale przypisane do partycji. W ten sposób Usługa IoT Hub jest w stanie zagwarantować kolejność zdarzeń (ponieważ przypisanie nigdy się nie zmienia).

Przypisanie partycji stałej ma również wpływ na wystąpienia usługi Time Series Insights, które pochłoną dane wysyłane z usługi IoT Hub. Gdy wiadomości z wielu urządzeń są przekazywane do koncentratora przy użyciu tego samego identyfikatora urządzenia bramy, mogą one pojawić się w tej samej partycji w tym samym czasie potencjalnie przekraczające limity skali partycji. 

**Wpływ**:

* Jeśli pojedyncza partycja wystąpi trwałe tempo pozyskiwania w ramach limitu wersji zapoznawczej, jest możliwe, że usługa Time Series Insights nie zsynchronizuje wszystkich danych telemetrycznych urządzenia przed przekroczeniem okresu przechowywania danych usługi IoT Hub. W rezultacie wysłane dane mogą zostać utracone, jeśli limity pozyskiwania są stale przekraczane.

Aby złagodzić tę okoliczność, zaleca się następujące najlepsze rozwiązania:

* Oblicz szybkość pozyskiwania danych na środowisko i partycję przed wdrożeniem rozwiązania.
* Upewnij się, że urządzenia Usługi IoT Hub są równoważące obciążenie w najszerszym możliwym zakresie.

> [!IMPORTANT]
> W przypadku środowisk używających usługi IoT Hub jako źródła zdarzeń oblicz szybkość pozyskiwania przy użyciu liczby używanych urządzeń koncentratorów, aby upewnić się, że szybkość spada poniżej ograniczenia 0,5 MB/s na partycję w wersji zapoznawczej.
> * Nawet jeśli kilka zdarzeń pojawi się jednocześnie, limit podglądu nie zostanie przekroczony.

  ![Diagram partycji centrum IoT](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Zapoznaj się z następującymi zasobami, aby dowiedzieć się więcej na temat optymalizacji przepływności i partycji koncentratora:

* [Skala piasty IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Skala Centrum zdarzeń](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Partycje Centrum zdarzeń](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>Magazyn danych

Podczas tworzenia środowiska SKU usługi *PayG (PayG)* w usłudze Time Series Insights Preview (payg) tworzysz dwa zasoby platformy Azure:

* Środowisko usługi Azure Time Series Insights Preview, które można skonfigurować pod kątem ciepłego magazynu danych.
* Konto obiektów blob ogólnego przeznaczenia usługi Azure Storage dla magazynu danych w chłodni.

Dane w ciepłym magazynie są dostępne tylko za pośrednictwem [zapytania szeregów czasowych](./time-series-insights-update-tsq.md) i [eksploratora usługi Azure Time Series Insights Preview.](./time-series-insights-update-explorer.md) Ciepły magazyn będzie zawierał najnowsze dane w [okresie przechowywania](./time-series-insights-update-plan.md#the-preview-environment) wybranym podczas tworzenia środowiska Usługi Time Series Insights.

Usługa Time Series Insights Preview zapisuje dane chłodni w magazynie obiektów Blob platformy Azure w [formacie pliku Parkietu.](#parquet-file-format-and-folder-structure) Usługa Time Series Insights Preview zarządza wyłącznie tymi danymi w chłodni, ale jest dostępna do odczytu bezpośrednio jako standardowe pliki parkietu.

> [!WARNING]
> Jako właściciel konta magazynu obiektów Blob platformy Azure, na którym znajdują się dane chłodni, masz pełny dostęp do wszystkich danych na koncie. Ten dostęp obejmuje uprawnienia do zapisu i usuwania. Nie edytuj ani nie usuwaj danych, które zapisuje usługa Time Series Insights Preview, ponieważ może to spowodować utratę danych.

### <a name="data-availability"></a>Dostępność danych

Usługa Azure Time Series Insights — partycje i indeksuje dane w celu uzyskania optymalnej wydajności zapytań. Dane stają się dostępne do kwerendy zarówno z ciepłych (jeśli jest włączona) i chłodni po jego indeksacji. Ilość danych, które są pozyskiwania może mieć wpływ na tę dostępność.

> [!IMPORTANT]
> Podczas podglądu może wystąpić okres do 60 sekund, zanim dane staną się dostępne. Jeśli wystąpi znaczne opóźnienie powyżej 60 sekund, prześlij zgłoszenie pomocy technicznej za pośrednictwem witryny Azure portal.

## <a name="azure-storage"></a>Azure Storage

W tej sekcji opisano szczegóły usługi Azure Storage dotyczące usługi Azure Time Series Insights Preview.

Aby uzyskać dokładny opis magazynu obiektów Blob platformy Azure, przeczytaj [wprowadzenie obiektów blob magazynu.](../storage/blobs/storage-blobs-introduction.md)

### <a name="your-storage-account"></a>Twoje konto pamięci masowej

Podczas tworzenia środowiska PAYG usługi Azure Time Series Insights Preview konto obiektów blob ogólnego przeznaczenia usługi Azure Storage jest tworzony jako długoterminowa chłodnię.  

Usługa Azure Time Series Insights Preview zachowuje do dwóch kopii każdego zdarzenia na koncie usługi Azure Storage. Jedna kopia przechowuje zdarzenia uporządkowane przez czas pozyskiwania, zawsze umożliwiając dostęp do zdarzeń w kolejności czasu. Z biegiem czasu usługa Time Series Insights Preview tworzy również partycjonowane kopie danych w celu optymalizacji pod kątem wydajnej kwerendy usługi Time Series Insights. 

Podczas publicznej wersji zapoznawczej dane są przechowywane przez czas nieokreślony na koncie usługi Azure Storage.

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Zapisywanie i edytowanie obiektów blob usługi Time Series Insights

Aby zapewnić wydajność kwerendy i dostępność danych, nie edytuj ani nie usuwaj żadnych obiektów blob, które tworzy usługa Time Series Insights Preview.

#### <a name="accessing-time-series-insights-preview-cold-store-data"></a>Uzyskiwanie dostępu do danych chłodni usługi Time Series Insights Preview 

Oprócz uzyskiwania dostępu do danych z [Eksploratora podglądu szeregów czasowych](./time-series-insights-update-explorer.md) i [zapytania szeregów czasowych](./time-series-insights-update-tsq.md)można również uzyskać dostęp do danych bezpośrednio z plików parkietu przechowywanych w chłodni. Na przykład można odczytać, przekształcić i oczyścić dane w notesie Jupyter, a następnie użyć go do uczenia modelu usługi Azure Machine Learning w tym samym obiegu pracy platformy Spark.

Aby uzyskać dostęp do danych bezpośrednio z konta usługi Azure Storage, potrzebujesz dostępu do odczytu do konta używanego do przechowywania danych usługi Time Series Insights Preview. Następnie można odczytać wybrane dane na podstawie czasu utworzenia `PT=Time` pliku Parkietu znajdującego się w folderze opisanym poniżej w sekcji [Format pliku Parkietu.](#parquet-file-format-and-folder-structure)  Aby uzyskać więcej informacji na temat włączania dostępu do odczytu konta magazynu, zobacz [Zarządzanie dostępem do zasobów konta magazynu](../storage/blobs/storage-manage-access-to-resources.md).

#### <a name="data-deletion"></a>Usuwanie danych

Nie usuwaj plików usługi Time Series Insights Preview. Zarządzanie powiązanymi danymi tylko z poziomu usługi Time Series Insights Preview.

### <a name="parquet-file-format-and-folder-structure"></a>Format pliku parkietu i struktura folderów

Parkiet to format pliku kolumnowego typu open source zaprojektowany z myślą o wydajnej pamięci masowej i wydajności. Aplikacja Time Series Insights Preview używa parkietu, aby włączyć wydajność zapytań opartych na identyfikatorze szeregów czasowych na dużą skalę.  

Aby uzyskać więcej informacji na temat typu pliku Parkiet, przeczytaj [dokumentację Parkietu](https://parquet.apache.org/documentation/latest/).

Usługa Time Series Insights Preview przechowuje kopie danych w następujący sposób:

* Pierwsza, początkowa kopia jest podzielona na partycje przez czas pozyskiwania i przechowuje dane w przybliżeniu w kolejności przybycia. Te dane znajdują się `PT=Time` w folderze:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* Druga kopia partycjonowana jest pogrupowana według identyfikatorów szeregów czasowych i znajduje się w folderze: `PT=TsId`

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

W obu przypadkach właściwość czasu pliku Parkiet odpowiada czas utworzenia obiektu blob. Dane w `PT=Time` folderze są zachowywane bez zmian po zapisaniu ich w pliku. Dane w `PT=TsId` folderze będą zoptymalizowane pod kątem zapytania w czasie i nie są statyczne.

> [!NOTE]
> * `<YYYY>`map do czterocyfrowej reprezentacji roku.
> * `<MM>`mapuje się na dwucyfrową reprezentację miesiąca.
> * `<YYYYMMDDHHMMSSfff>`mapuje na reprezentację sygnatury`YYYY`czasowej z`MM`czterocyfrowym rokiem ( ),`HH`dwucyfrowym miesiącem`MM`( ), dwucyfrowym`SS`dniem (`DD`), dwucyfrową`fff`godziną ( ), dwucyfrową minutą ( ), dwucyfrową sekundą ( ) i trzycyfrową milisekundą ( ).

Zdarzenia podglądu usługi Time Series Insights są mapowane na zawartość pliku Parkietu w następujący sposób:

* Każde zdarzenie jest mapowane do jednego wiersza.
* Każdy wiersz zawiera kolumnę **sygnatury czasowej** z sygnaturą czasową zdarzenia. Właściwość sygnatury czasowej nigdy nie jest null. Domyślnie zdarzenie jest w **kolejce,** jeśli właściwość sygnatury czasowej nie jest określona w źródle zdarzenia. Przechowywana sygnatura czasowa jest zawsze w czasie UTC.
* Każdy wiersz zawiera kolumny identyfikatora serii czasowych (TSID) zdefiniowane podczas tworzenia środowiska usługi Time Series Insights. Nazwa właściwości TSID `_string` zawiera sufiks.
* Wszystkie inne właściwości wysyłane jako dane telemetryczne są `_string` mapowane `_bool` na nazwy `_datetime` kolumn, które `_double` kończą się (ciąg), (boolean), (datetime) lub (double), w zależności od typu właściwości.
* Ten schemat mapowania ma zastosowanie do pierwszej wersji formatu pliku, określanej jako **V=1** i przechowywanej w folderze podstawowym o tej samej nazwie. W miarę rozwoju tej funkcji ten schemat mapowania może ulec zmianie, a nazwa odwołania została przyrostowana.

## <a name="next-steps"></a>Następne kroki

- Przeczytaj, [jak kształtować JSON dla ruchu przychodzącego i kwerendy](./time-series-insights-update-how-to-shape-events.md).

- Przeczytaj o nowym [modelowaniu danych](./time-series-insights-update-tsm.md).
