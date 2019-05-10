---
title: Magazyn danych i transferu danych przychodzących w wersji zapoznawczej Azure czas serii szczegółowych informacji | Dokumentacja firmy Microsoft
description: Omówienie przechowywania danych i transferu danych przychodzących w wersji zapoznawczej Azure czas serii szczegółowych informacji.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: 35d9e953ade337672fd57149e325b507f6ce115f
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65405717"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Magazyn danych i transferu danych przychodzących w wersji zapoznawczej Azure czas serii szczegółowych informacji

W tym artykule opisano zmiany w magazynowaniu danych i transferu danych przychodzących z Azure czas Series Insights w wersji zapoznawczej. Poruszono w nim struktury magazynu, format pliku i właściwość Identyfikatora serii czasu. Omówiono również bazowego procesu transferu danych przychodzących, przepływności i ograniczeń.

## <a name="data-storage"></a>Magazyn danych

Podczas tworzenia środowiska jednostki SKU z płatnością za rzeczywiste użycie czas Series Insights w wersji zapoznawczej, tworzysz dwa zasoby:

* Środowisko usługi Time Series Insights.
* Konto usługi Azure Storage ogólnego przeznaczenia w wersji 1 przechowywania danych.

Czas Series Insights w wersji zapoznawczej korzysta z magazynu obiektów Blob platformy Azure z typem pliku Parquet. Usługa Time Series Insights zarządza wszystkie operacje danych, takich jak tworzenie obiektów blob, indeksowania i partycjonowanie danych na koncie magazynu platformy Azure. Możesz utworzyć te obiekty BLOB za pomocą konta usługi Azure storage.

Podobnie jak inne obiekty BLOB usługi Azure Storage usługi Time Series Insights, utworzone obiekty BLOB umożliwiają odczyt i zapis do obsługi różnych scenariuszy integracji.

> [!TIP]
> Wydajność Series Insights może mieć negatywny wpływ odczytu lub zapisu do obiektów blob, zbyt często.

Omówienie usługi Azure Blob storage, zobacz [magazynu obiektów blob wprowadzenie](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).

Aby uzyskać więcej informacji na temat typu pliku Parquet, zobacz [obsługiwane typy plików w usłudze Azure Storage](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs#Parquet-format).

## <a name="parquet-file-format"></a>Format pliku parquet

Parquet jest zorientowana na kolumny danych formacie, który został zaprojektowany do:

* Współdziałanie
* Wydajności miejsca
* Wydajność zapytań

Usługi Time Series Insights wybrała Parquet, ponieważ zawiera ono kompresji danych wydajne i kodowanie systemów, za pomocą rozszerzonych wydajności, która może obsłużyć danych złożonych, zbiorczo.

Aby lepiej zrozumieć formatu pliku Parquet, zobacz [Parquet dokumentacji](https://parquet.apache.org/documentation/latest/).

### <a name="event-structure-in-parquet"></a>Struktura zdarzeń w Parquet

Usługa Time Series Insights tworzy i przechowuje kopie obiektów blob w dwóch następujących formatów:

1. Pierwszy początkowa kopia jest podzielona na partycje według czasu odbioru:

    * `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * Godzina utworzenia obiektu blob dla obiektów blob na partycje według czasu odbioru.

1. Dynamiczna grupa identyfikator serii czasu kopiowania drugi, ponownie Partycjonować jest podzielona na partycje:

    * `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * Sygnatura czasowa zdarzenia minimalne w obiekcie blob dla obiektów blob dzielone według identyfikatora serii czasu

> [!NOTE]
> * `<YYYY>` mapuje do reprezentacji roku 4-cyfrowego.
> * `<MM>` mapuje do reprezentacji 2-cyfrowy miesiąca.
> * `<YYYYMMDDHHMMSSfff>` mapuje na reprezentację w postaci sygnatury czasowej z roku 4-cyfrowego (`YYYY`), miesiąc 2-cyfrowy (`MM`), dnia 2-cyfrowy (`DD`), godzinę 2-cyfrowy (`HH`), 2-cyfrowy minuty (`MM`), drugi 2-cyfrowy (`SS`) i 3-cyfrowy milisekundy (`fff`).

Zdarzenia usługi Insights serie czasu są mapowane do zawartości pliku Parquet:

* Każde zdarzenie jest mapowany na pojedynczy wiersz.
* Wbudowane **sygnatura czasowa** kolumny z sygnaturą czasową zdarzenia. Właściwość sygnatury czasowej nigdy nie ma wartość null. Jego wartość domyślna to **czas umieszczonych w kolejce źródła zdarzenia** Jeśli właściwość sygnatury czasowej nie jest określony w zdarzeniu źródła. Jest to sygnatura czasowa w formacie UTC. 
* Wszystkie pozostałe właściwości, które są mapowane na kolumny kończyć się znakiem `_string` (ciąg) `_bool` (Boolean) `_datetime` (Data/godzina) i `_double` (podwójny), w zależności od typu właściwości.
* Jest to schemat mapowania dla pierwszej wersji formatu pliku, który nazywamy **V = 1**. Zgodnie z rozwojem tej funkcji, nazwa zostanie zwiększony do **V = 2**, **V = 3**i tak dalej.

## <a name="partitions"></a>Partycje

Każde środowisko czasu Series Insights w wersji zapoznawczej musi mieć **identyfikator serii czasu** właściwości i **sygnatura czasowa** właściwość, która jej jednoznaczną identyfikację. Twój identyfikator serii czasu działa jako partycji logicznej dla swoich danych i zapewnia środowisko czasu Series Insights w wersji zapoznawczej naturalnych granic rozproszenie danych na partycje fizyczne. Zarządzanie fizyczną partycję jest zarządzana przez czas Series Insights w wersji zapoznawczej w koncie magazynu platformy Azure.

Usługi Time Series Insights używa dynamiczne partycjonowanie, aby zoptymalizować magazyn i zwiększa wydajność zapytań przez porzucenie i ponowne utworzenie partycji. Czas Series Insights w wersji zapoznawczej algorytm dynamiczny partycjonowania próbuje uniknąć sytuacji, w jednej partycji fizycznych będzie miał danych dla wielu partycji odrębne i logicznych. Innymi słowy partycjonowania algorytm przechowuje wszystkie dane określone do jednej godziny Identyfikatora serii wyłącznie obecne w plikach Parquet bez trwa przeplatać z innych identyfikatorów w serii czasu. Algorytm dynamiczny dla partycjonowania również próbuje zachować pierwotną kolejność zdarzeń w ramach jednej serii czasu, identyfikator.

Początkowo podczas transferu danych przychodzących, dane są partycjonowane przez sygnaturę czasową, aby pojedynczy, logicznej partycji w danym okresie, mogły być rozkładane na wiele partycji fizycznych. Jedna partycja fizycznych mogą również zawierać wiele lub wszystkie logiczne partycji. Ze względu na ograniczenia rozmiaru obiektów blob, nawet w przypadku partycjonowania optymalne jednej partycji logicznej mogą zajmować wiele partycji fizycznych.

> [!NOTE]
> Domyślnie wartość znacznika czasu jest komunikat *czasu umieszczonych w kolejce* w źródle skonfigurowanego zdarzenia.

W przypadku przekazywania danych historycznych lub komunikaty przetwarzania wsadowego, należy przypisać wartości, które mają być przechowywane z danymi w celu właściwość sygnatury czasowej, który jest mapowany do odpowiedniego sygnatury czasowej. Właściwość sygnatury czasowej jest uwzględniana wielkość liter. Aby uzyskać więcej informacji, zobacz [modelu szeregów czasowych](./time-series-insights-update-tsm.md).

### <a name="physical-partitions"></a>Partycje fizyczne

Fizyczną partycję jest blokowy obiekt blob, który jest przechowywany na koncie magazynu. Rzeczywisty rozmiar obiektów blob może się różnić, ponieważ rozmiar zależy od szybkości wypychania. Jednak oczekujemy, że obiekty BLOB to około 20 MB do 50 MB rozmiar. Ta oczekiwania prowadzone od zespołu usługi Time Series Insights wybierz rozmiar Aby zoptymalizować wydajność zapytań o rozmiarze 20 MB. Ten rozmiar można zmieniać wraz z upływem czasu, w zależności od rozmiaru pliku oraz szybkość transferu danych przychodzących.

> [!NOTE]
> * Obiekty BLOB mają rozmiar o rozmiarze 20 MB.
> * Obiekty BLOB platformy Azure są od czasu do czasu ponownie Partycjonować uzyskać lepszą wydajność dzięki porzucona i utworzona ponownie.
> * Ponadto te same dane usługi Time Series Insights mogą być obecne w dwóch lub więcej obiektów blob.

### <a name="logical-partitions"></a>Partycje logiczne

Partycja logiczna jest partycji w obrębie fizyczną partycję, która przechowuje wszystkie dane, które są skojarzone z jedną wartością klucza partycji. Czas Series Insights w wersji zapoznawczej logicznie dzieli każdy obiekt blob, w oparciu o dwie właściwości:

* **Czas serii identyfikator**: Klucz partycji dla wszystkich danych usługi Time Series Insights w strumieniu zdarzeń i modelu.
* **Sygnatura czasowa**: Czas, oparte na początkowy ruch przychodzący.

Czas Series Insights w wersji zapoznawczej oferuje zapytania wydajna, które opierają się na te dwie właściwości. Te dwie właściwości udostępniają metodę najbardziej efektywne, umożliwiające szybkie dostarczanie danych usługi Time Series Insights.

Należy wybrać odpowiedni identyfikator serii czasu, ponieważ jest właściwością niezmienne. Aby uzyskać więcej informacji, zobacz [wybierz identyfikatory serii czasu](./time-series-insights-update-how-to-id.md).

## <a name="azure-storage"></a>Magazyn Azure

### <a name="your-storage-account"></a>Konta magazynu

Kiedy należy utworzyć środowisko zgodnie z rzeczywistym użyciem usługi Time Series Insights, tworzysz dwa zasoby: środowisko usługi Time Series Insights i Azure Storage ogólnego przeznaczenia w wersji 1 konto przechowywania danych. Wybraliśmy uczynić usługi Azure Storage ogólnego przeznaczenia w wersji 1 zasobu domyślnego z powodu jej współdziałanie, cen i wydajności. 

Usługa Time Series Insights publikuje maksymalnie dwie kopie każdego zdarzenia na Twoim koncie usługi Azure storage. Zawsze są zachowywane kopii początkowej, dzięki czemu mogą szybko przeszukiwać je za pomocą innych usług. Łatwo służy platformy Spark, Hadoop i inne dobrze znanych narzędzi w identyfikatorach serii czasu nieprzetworzone pliki Parquet, ponieważ te aparaty obsługuje filtrowanie podstawowe nazwę pliku. Grupowanie obiektów blob według roku i miesiąca jest wygodnym sposobem na wyświetlanie listy obiektów blob w określonym zakresie czasu dla niestandardowego zadania. 

Ponadto usługa Time Series Insights repartitions pliki Parquet zoptymalizowane pod kątem API Insights serii czasu. Najbardziej niedawno repartitioned plik również jest zapisywany.

W publicznej wersji zapoznawczej dane są przechowywane przez czas nieokreślony na Twoim koncie usługi Azure storage.

### <a name="writing-and-editing-time-series-insights-blobs"></a>Zapisywanie i edytowanie obiektów blob usługi Time Series Insights

Aby zapewnić wydajność zapytań i dostępność danych, nie Edytuj lub Usuń wszystkie obiekty BLOB, które są tworzone przez usługi Time Series Insights.

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Uzyskiwanie dostępu do i eksportowanie danych z godziny Series Insights w wersji zapoznawczej

Możesz chcieć uzyskać dostęp do danych przechowywanych w Eksploratorze czasu Series Insights w wersji zapoznawczej, aby użyć w połączeniu z innymi usługami. Na przykład możesz chcieć korzystanie z Twoich danych do raportu w usłudze Power BI, do przeprowadzenia uczenia maszynowego przy użyciu usługi Azure Machine Learning Studio, lub do użycia w aplikacji z notesami Jupyter notebook.

Może uzyskiwać dostęp do danych na trzy sposoby ogólne:

* Z poziomu Eksploratora czasu Series Insights w wersji zapoznawczej: dane można wyeksportować jako plik CSV z poziomu Eksploratora czasu Series Insights w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [czasu Series Insights w wersji zapoznawczej Eksplorator](./time-series-insights-update-explorer.md).
* Z szeregu czasowego interfejsów API w wersji zapoznawczej szczegółowych informacji: punkt końcowy interfejsu API można skontaktować, `/getRecorded`. Aby dowiedzieć się więcej na temat tego interfejsu API, zobacz [zapytania serii czasu](./time-series-insights-update-tsq.md).
* Bezpośrednio z konta usługi Azure storage (poniżej).

#### <a name="from-an-azure-storage-account"></a>Z konta usługi Azure storage

* Konieczne jest dostęp do odczytu do dowolnego konta, za pomocą uzyskiwać dostęp do danych usługi Time Series Insights. Aby uzyskać więcej informacji, zobacz [zarządzanie dostępem do zasobów kont magazynu](https://docs.microsoft.com/azure/storage/blobs/storage-manage-access-to-resources).
* Aby uzyskać więcej informacji na temat sposobów bezpośredniego można odczytać danych z usługi Azure Blob storage, zobacz [przenoszenie danych do i z konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
* Aby wyeksportować dane z konta usługi Azure storage:
    * Najpierw upewnij się, że Twoje konto spełnia wymagania niezbędne do eksportowania danych. Aby uzyskać więcej informacji, zobacz [magazynu importowanie i eksportowanie wymagania](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements).
    * Aby poznać inne sposoby eksportowania danych z konta usługi Azure storage, zobacz temat [importowanie i eksportowanie danych z obiektów blob](https://docs.microsoft.com/azure/storage/common/storage-import-export-data-from-blobs).

### <a name="data-deletion"></a>Usuwanie danych

Nie należy usuwać obiektów blob, ponieważ czas Series Insights w wersji zapoznawczej przechowuje metadane dotyczące obiektów blob w nim.

## <a name="time-series-insights-data-ingress"></a>Czas Series Insights dane przychodzące

### <a name="ingress-policies"></a>Zasady transferu danych przychodzących

Czas Series Insights w wersji zapoznawczej obsługuje te same źródła zdarzeń i typów plików, które obsługuje obecnie usługa Time Series Insights.

Źródła zdarzeń obsługiwanych obejmują:

- Azure IoT Hub
- Azure Event Hubs
  
  > [!NOTE]
  > Wystąpienia Centrum zdarzeń platformy Azure obsługuje platformy Kafka.

Obsługiwane typy plików obejmują:

* JSON: Aby uzyskać więcej informacji na temat obsługiwane kształty JSON, firma Microsoft może obsługiwać zobacz [jak kształt JSON](./time-series-insights-send-events.md#json).

### <a name="data-availability"></a>Dostępność danych

Czas Series Insights w wersji zapoznawczej indeksuje dane za pomocą strategii optymalizacji rozmiar obiektu blob. Dane będą dostępne do badania po są indeksowane, który jest zależnie od ilości danych i w jakich prędkości.

> [!IMPORTANT]
> * Wydania ogólnodostępnej (GA) usługa Time Series Insights będzie udostępniać dane w ciągu 60 sekund od osiągnięcia źródła zdarzenia. 
> * W trakcie okresu zapoznawczego należy się spodziewać dłuższy czas, przed udostępnieniem danych.
> * Jeśli wystąpią wszystkie istotne opóźnienia, pamiętaj, że skontaktuj się z nami.

### <a name="scale"></a>Skaluj

Czas Series Insights w wersji zapoznawczej obsługuje skali początkowy ruch przychodzący do 6 megabajty na sekundę (MB/s) na środowisko. Trwa rozszerzoną obsługę skalowania. Firma Microsoft planuje aktualizację naszej dokumentacji w celu odzwierciedlenia tych ulepszeń

## <a name="next-steps"></a>Kolejne kroki

- Odczyt [usługi Azure Time Series Insights w wersji zapoznawczej magazynu i transferu danych przychodzących](./time-series-insights-update-storage-ingress.md).

- Przeczytaj o nowym [modelowania danych](./time-series-insights-update-tsm.md).

<!-- Images -->
[1]: media/v2-update-storage-ingress/storage-architecture.png
[2]: media/v2-update-storage-ingress/parquet-files.png
[3]: media/v2-update-storage-ingress/blob-storage.png
