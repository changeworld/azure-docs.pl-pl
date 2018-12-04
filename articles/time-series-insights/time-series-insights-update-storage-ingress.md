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
ms.date: 11/27/2018
ms.openlocfilehash: 6635558d1b7cf664084c103e3b3b37b44c022fa6
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856548"
---
# <a name="data-storage-and-ingress-in-the-azure-time-series-insights-preview"></a>Magazyn danych i transferu danych przychodzących w usłudze Azure Time Series Insights (wersja zapoznawcza)

W tym artykule opisano zmiany w magazynowaniu danych i transferu danych przychodzących z usługi Azure Time Series Insights (wersja zapoznawcza). Poruszono w nim wewnętrzna struktura magazynu format pliku i **identyfikator serii czasu** właściwości. Omówiono w nim również bazowego procesu transferu danych przychodzących, przepływności i ograniczeń.

## <a name="data-storage"></a>Magazyn danych

Podczas tworzenia aktualizacji usługi Time Series Insights (**jednostki Sku PAYG**) środowiska, tworzysz dwa zasoby:

1. Środowisko Azure TSI.
1. Usługa Azure storage ogólnego przeznaczenia w wersji 1 konta, gdzie będą przechowywane dane.

Usługa TSI Przesyła (wersja zapoznawcza) używa usługi Azure Blob Storage z typem pliku Parquet. Azure TSI zarządza wszystkie operacje danych, takich jak tworzenie obiektów blob, indeksowania i partycjonowanie danych w ramach konta usługi Azure Storage. Te obiekty BLOB są tworzone przy użyciu konta usługi Azure Storage. Aby upewnić się, że wszystkie zdarzenia mogą być wyszukiwane w sposób wydajny. Aktualizacja usługi TSI będzie obsługiwać usługi Azure Storage ogólnego przeznaczenia w wersji 1 i 2 "gorącymi" Opcje konfiguracji.  

Podobnie jak inne blob usługi Azure Storage może odczytywać i zapisywać obiektów blob Azure TSI, utworzony na potrzeby scenariuszy integracji różnych.

> [!TIP]
> Należy pamiętać, że usługa TSI Przesyła mogą mieć niekorzystny wpływ na wydajność przez odczyt lub zapis do obiektów blob, zbyt często.

Omówienie działania usługi Azure Blob Storage, przeczytaj [magazynu obiektów blob wprowadzenie](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).

Aby uzyskać więcej informacji na temat typu pliku Parquet, przejrzyj [obsługiwane typy plików w usłudze Azure Storage](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs#Parquet-format).

## <a name="parquet-file-format"></a>Format pliku parquet

Parquet to format pliku zorientowana na kolumny danych, który został zaprojektowany do:

* Współdziałanie
* Wydajności miejsca
* Wydajność zapytań

Azure TSI wybrała Parquet, ponieważ zapewnia kompresji danych wydajne i schematy kodowania za pomocą rozszerzonych wydajności do obsługi danych złożonych, zbiorczo.

Lepiej zrozumieć, jakie pliku Parquet format wszystkim chodzi, przejdź do [oficjalne strony Parquet](https://parquet.apache.org/documentation/latest/).

## <a name="event-structure-in-parquet"></a>Struktura zdarzeń w Parquet

Dwie kopie obiektów blob utworzonych przez Azure TSI będą przechowywane w następujących formatach:

1. Pierwszy kopii początkowej będą można podzielić na partycje według czasu odbioru:

    * `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI internal suffix>.parquet`
    * Godzina utworzenia obiektu blob dla obiektów blob na partycje według czasu odbioru.

1. Drugi kopię repartitioned będą można podzielić na partycje przez grupowanie dynamiczne identyfikatora serii czasu:

    • `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_< TSI internal suffix >.parquet`
    * Sygnatura czasowa zdarzenia min w obiekcie blob dla obiektów blob jest podzielona na partycje szeregów czasowych identyfikatora.

> [!NOTE]
> * `<YYYY>` mapowania na rok.
> * `<MM>` mapowania na miesiąc.
> * `<YYYYMMDDHHMMSSfff>` mapuje do pełnego sygnatura czasowa w milisekundach.

Azure — wydarzenia TSI są mapowane do zawartości pliku Parquet:

* Każde zdarzenie jest mapowany na pojedynczy wiersz.
* Wbudowane **sygnatura czasowa** kolumny z sygnaturą czasową zdarzenia. **Sygnatura czasowa** właściwość nigdy nie ma wartość null.  Jego wartość domyślna to **czas umieszczonych w kolejce źródła zdarzenia** Jeśli **sygnatura czasowa** właściwość nie jest określona w źródle zdarzenia.  **Sygnatura czasowa** jest w formacie UTC.  
* Wszystkie inne mapy właściwości do kolumn będzie kończyć się znakiem `_string` (ciąg) `_bool` (boolean) `_datetime` (Data/godzina) i `_double` (podwójny) w zależności od typu właściwości.
* Jest to pierwsza wersja formatu pliku, a nazywamy go jako ***V = 1**.  Jeśli ta funkcja zmienia nazwę jest zwiększany odpowiednio do **V = 2**, **V = 3**i tak dalej.

## <a name="how-to-partition"></a>Jak partycji

Każde środowisko Azure TSI (wersja zapoznawcza) musi mieć **identyfikator serii czasu** właściwości i **sygnatura czasowa** właściwość, która jej jednoznaczną identyfikację. Twoje **identyfikator serii czasu** działa jako partycji logicznej dla swoich danych i oferuje środowisko Azure TSI (wersja zapoznawcza) przy użyciu naturalnych granic rozproszenie danych na partycje fizyczne. Zarządzanie fizyczną partycję zarządza Azure TSI (wersja zapoznawcza) na koncie usługi Azure Storage.

Azure TSI używa dynamiczne partycjonowanie, aby zoptymalizować magazyn wykorzystanie i zwiększa wydajność zapytań przez usunięcie i ponowne utworzenie partycji. Algorytm dynamiczny partycjonowania (wersja zapoznawcza) w usłudze TSI dokłada wszelkich starań uniknąć jednej partycji fizycznych, zawierającej dane dla wielu różnych partycji logicznej. Lub innymi słowy celem algorytm partycjonowania jest zachować wszystkie dane związane z jedną **identyfikator serii czasu** występować wyłącznie w pliki Parquet bez trwa z przeplotem z innymi **identyfikatory serii czasu**. Algorytm dynamiczny dla partycjonowania także dokłada starań, aby zachować pierwotną kolejność zdarzeń w ramach pojedynczej **identyfikator serii czasu**.

Początkowo w czasie transferu danych przychodzących danych jest podzielona na partycje według **sygnatura czasowa** dzięki pojedynczej, logicznej partycji w danym okresie mogą znajdować się na wielu partycjach fizycznych. Jedna partycja fizycznych mogą również zawierać wiele lub wszystkie logiczne partycji.  Ze względu na ograniczenia rozmiaru obiektów blob nawet w przypadku optymalne, Partycjonowanie jednej partycji logicznej może zajmować wiele partycji fizycznych.

> [!NOTE]
> **Sygnatura czasowa** wartość jest komunikat **czasu umieszczonych w kolejce** w źródle zdarzenia skonfigurowane domyślnie.  

Podczas przekazywania danych historycznych lub komunikaty przetwarzania wsadowego, należy wyznaczyć **sygnatura czasowa** właściwość w Twoich danych, który jest mapowany do odpowiedniego **sygnatura czasowa** wartości, które mają być przechowywane z danymi.  **Sygnatura czasowa** właściwości jest rozróżniana wielkość liter. Aby uzyskać więcej informacji, przeczytaj [artykułu modelu szeregów czasowych](./time-series-insights-update-tsm.md).

## <a name="physical-partition"></a>Partycję fizyczną

Fizyczną partycję jest blokowy obiekt blob w usłudze Azure Storage. Rzeczywisty rozmiar obiektów blob różnią się, ponieważ zależy od szybkości wypychania, jednak oczekujemy, że obiekty BLOB jako rozmiar około 20 – 50 MB. W związku z tym oczekiwania zespołu TSI wybrany o rozmiarze 20 MB na rozmiar w celu zoptymalizowania wydajności zapytania. Można to zmienić wraz z upływem czasu, w oparciu o rozmiar pliku i szybkość transferu danych przychodzących.

> [!NOTE]
> * Obiekty BLOB mają rozmiar o rozmiarze 20MB.
> * Obiekty BLOB platformy Azure są od czasu do czasu ponownie Partycjonować uzyskać lepszą wydajność dzięki porzucona i utworzona ponownie.
> * Należy również zauważyć, że te same dane TSI mogą być obecne w wielu obiektów blob.

## <a name="logical-partition"></a>Partycja logiczna

Partycja logiczna jest partycji w obrębie fizyczną partycję, która przechowuje wszystkie dane, które są skojarzone z jedną wartością klucza partycji. Usługa TSI Przesyła (wersja zapoznawcza) zostanie logicznie partycji każdy obiekt blob, w oparciu o dwie właściwości:

1. **Czas serii identyfikator** — jest kluczem partycji dla wszystkich danych TSI w strumieniu zdarzeń i modelu.
1. **Sygnatura czasowa** — zależne od początkowego transferu danych przychodzących.

Azure TSI (wersja zapoznawcza) oferuje zapytania wydajna, które opierają się na te dwie właściwości. Te dwie właściwości zapewniają także najbardziej efektywną metodę szybkie dostarczanie danych w usłudze TSI.

Ważne jest, aby wybrać odpowiednią **identyfikatory serii czasu**, ponieważ jest właściwością niezmienne.  Zobacz [wybierając identyfikatory serii czasu](./time-series-insights-update-how-to-id.md) Aby uzyskać więcej informacji.

## <a name="your-azure-storage-account"></a>Konta usługi Azure Storage

### <a name="storage"></a>Magazyn

Podczas tworzenia środowiska TSI PAYG utworzysz dwa zasoby — w środowisku usługi TSI i ogólnego przeznaczenia usługi Azure storage konta V1, w którym będą przechowywane dane. W przyszłości kont ogólnego przeznaczenia w wersji 1 usługi Azure Storage będzie ograniczona do istniejących klientów platformy Azure, w związku z tym wszyscy nowi klienci Azure aprowizacji środowiska (wersja zapoznawcza) w usłudze TSI domyślnie utworzy konto "Hot" usługi Azure Storage ogólnego przeznaczenia w wersji 2.  Wybraliśmy się usługi Azure Storage ogólnego przeznaczenia w wersji 1 domyślnej ze względu na jej współdziałanie, cen i wydajności.  

Azure TSI opublikuje maksymalnie dwie kopie każdego zdarzenia na Twoim koncie usługi Azure Storage. Kopii początkowej są zawsze zachowywane w celu zapewnienia, że można zbadać performantly za pomocą innych usług. W związku z tym, Spark, Hadoop i inne dobrze znanych narzędzi można łatwo stosować w przypadku **identyfikatory serii czasu** za pośrednictwem pierwotne Parquet pliku filtrowanie nazw plików, ponieważ te aparaty obsługuje podstawowe. Grupowanie obiektów blob według roku i miesiąca jest przydatny do zbierania listy obiektów blob w określonym zakresie czasu dla zadania niestandardowego.  

Ponadto usługa TSI Przesyła zostanie ponownie podzielić na partycje pliki Parquet zoptymalizowane pod kątem interfejsów API usługi Azure TSI i najbardziej niedawno repartitioned plik zostanie też zapisane.

W publicznej wersji zapoznawczej dane będą przechowywane przez czas nieokreślony na Twoim koncie usługi Azure Storage. Możliwość usunięcia danych zostaną dodane w przyszłości, dzięki czemu lepszej kontroli nad zarządzaniem stare dane.

### <a name="writing-and-editing-time-series-insights-blobs"></a>Zapisywanie i edytowanie obiektów blob usługi Time Series Insights

Aby zapewnić wydajność zapytań i dostępność danych, nie edytować ani nie usuwaj żadnych obiektów blob utworzonych przez TSI.

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Uzyskiwanie dostępu do i eksportować dane z usługi Time Series Insights (wersja zapoznawcza)

Możesz uzyskać dostęp do danych przechowywanych w usłudze Azure TSI (wersja zapoznawcza) Explorer do użytku w połączeniu z innymi usługami. Na przykład można użyć danych raportowania w usłudze Power BI do przeprowadzenia uczenia maszynowego przy użyciu usługi Azure Machine Learning Studio lub w aplikacji notesu Jupyter Notebooks itp.

Istnieją trzy ogólne ścieżki dostępu do danych:

* Eksplorator usługi Azure TSI (wersja zapoznawcza).
* Interfejsy API usługi Azure TSI (wersja zapoznawcza).
* Bezpośrednio z konta usługi Azure Storage.

### <a name="from-the-time-series-insights-preview-explorer"></a>Z poziomu Eksploratora usługi Time Series Insights (wersja zapoznawcza)

Możesz wyeksportować dane do pliku CSV z usługi TSI Explorer (wersja zapoznawcza). Przeczytaj więcej na temat [Explorer (wersja zapoznawcza) w usłudze TSI](./time-series-insights-update-explorer.md).

### <a name="from-the-time-series-insights-preview-apis"></a>Za pomocą interfejsów API usługi Time Series Insights (wersja zapoznawcza)

Punkt końcowy interfejsu API można skontaktować, `/getRecorded`. Aby dowiedzieć się więcej na temat tego interfejsu API, przeczytaj temat [zapytania serii czasu](./time-series-insights-update-tsq.md).

### <a name="from-an-azure-storage-account"></a>Z konta usługi Azure Storage

1. Musisz mieć dostęp do odczytu przyznanych w do dowolnego konta będzie używany dostęp do danych usługi TSI. Aby dowiedzieć się więcej na temat udzielania dostępu do odczytu do magazynu obiektów Blob platformy Azure, przeczytaj [zarządzanie dostępem do zasobów magazynu](https://docs.microsoft.com/azure/storage/blobs/storage-manage-access-to-resources).

1. Aby uzyskać więcej informacji na temat sposobów bezpośredniego można odczytać danych z usługi Azure Blob Storage, przeczytaj [przenoszenie danych do i z usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

1. Eksportowanie danych z konta usługi Azure Storage:

    * Najpierw upewnij się, że Twoje konto ma niezbędne wymagania zostały spełnione, aby wyeksportować dane. Odczyt [magazynu importowanie i eksportowanie wymagania](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements) Aby uzyskać więcej informacji.
    * Dowiedz się więcej o innych sposobach eksportowania danych z konta usługi Azure Storage, odwiedzając [magazynu importowanie i eksportowanie danych z obiektów blob](https://docs.microsoft.com/azure/storage/common/storage-import-export-data-from-blobs)

### <a name="blob-storage-considerations"></a>Zagadnienia dotyczące magazynu obiektów blob

* Usługa Azure storage ma Odczyt i zapis w zależności od jak duże jest użycie usługi TSI (wersja zapoznawcza).  
* Azure TSI prywatne w wersji zapoznawczej nie dostarcza jeszcze dowolnego rodzaju Parquet magazynu obiektów blob meta-do obsługi systemów zewnętrznych przetwarzania danych. Jednak firma Microsoft to badania i może dodać pomocy technicznej w przyszłości.  
* Klienci będą musieli odczytu obiektów blob platformy Azure na partycje według czasu, aby można było do przetwarzania danych.
* Azure TSI prywatne w wersji zapoznawczej wykonuje dynamiczne ponowny podział danych obiektów blob w celu zapewnienia lepszej wydajności. Jest to realizowane przez usunięcie i ponowne utworzenie obiektów blob. Większość usług będzie najlepiej obsługiwane przy użyciu oryginalnych plików.  
* Mogą być zduplikowane dane (wersja zapoznawcza) w usłudze TSI przez obiekty BLOB.

### <a name="data-deletion"></a>Usuwanie danych

Azure TSI prywatne w wersji zapoznawczej aktualnie nie obsługuje usuwania danych, ale będzie w przyszłości. Oczekujemy, że do jego obsługi za wersję ogólnie Dostępną, ale potencjalnie wcześniej. Gdy firma Microsoft obsługuje usuwania danych otrzymasz powiadomienie użytkowników.

Nie należy usuwać obiekty BLOB, ponieważ usługa Time Series Insights (wersja zapoznawcza) przechowuje metadane dotyczące obiektów blob w usłudze TSI aktualizacji.

## <a name="ingress"></a>Ruch przychodzący

### <a name="azure-time-series-insights-ingress-policies"></a>Zasady transferu danych przychodzących w usłudze Azure Time Series Insights

Azure TSI prywatne w wersji zapoznawczej obsługuje te same źródła zdarzeń i typów plików, które obecnie.

Źródła zdarzeń obsługiwanych obejmują:

* Azure IoT Hub
* Azure Event Hubs
  * Uwaga: Wystąpienia usługi Azure Event Hub obsługuje platformy Kafka.

Obsługiwane typy plików obejmują:

* JSON
  * Aby uzyskać bardziej na obsługiwane kształty JSON firma Microsoft może obsłużyć, zobacz [zapytania serii czasu](./time-series-insights-update-tsq.md) dokumentacji.

### <a name="data-availability"></a>Dostępność danych

Azure TSI prywatne w wersji zapoznawczej indeksuje dane za pomocą strategii optymalizacji rozmiar obiektu blob. Oznacza to, czy dane będą dostępne dla zapytań, gdy są indeksowane (która jest zależnie od ilości danych i w jakich prędkość). Jak podamy publicznej wersji zapoznawczej, logiki zostaną dodane do wyszukania nowych zdarzeń co kilka sekund (co spowoduje, że dane dostępne dla zapytań, szybszy i bardziej niezawodny).

> [!IMPORTANT]
> * Publicznej wersji zapoznawczej usługi TSI będzie udostępniać dane w ciągu 60 sekund jego naciśnięcie źródła zdarzenia.  
> * Podczas prywatnej wersji zapoznawczej firma Microsoft powinny być widoczne na dłuższy czas, zanim dane staje się dostępny.  
>   * Jeśli wystąpią wszystkie istotne opóźnienia, skontaktuj się z nami.

### <a name="scale"></a>Skalowanie

Oczekujemy, że Azure TSI prywatnej wersji zapoznawczej, aby obsłużyć maksymalnie 6Mbps na środowisko. Ulepszona obsługa skalowania jest planowana w przyszłych wersjach.

[!INCLUDE [tsi-update-docs](../../includes/time-series-insights-update-documents.md)]

## <a name="next-steps"></a>Kolejne kroki

Odczyt [Azure TSI (wersja zapoznawcza), magazynu i transferu danych przychodzących](./time-series-insights-update-storage-ingress.md).

Przeczytaj o nowym [modelu szeregów czasowych](./time-series-insights-update-tsm.md).

<!-- Images -->
[1]: media/v2-update-storage-ingress/storage-architecture.png
[2]: media/v2-update-storage-ingress/parquet-files.png
[3]: media/v2-update-storage-ingress/blob-storage.png