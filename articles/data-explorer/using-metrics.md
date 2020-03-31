---
title: Monitorowanie wydajności, użycia & kondycji usługi Azure Data Explorer za pomocą metryk
description: Dowiedz się, jak używać metryk usługi Azure Data Explorer do monitorowania wydajności, kondycji i użycia klastra.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: 1319b8cd6ac8a0eb83381c24bcde9996458e47a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560308"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Monitorowanie wydajności, kondycji i użycia usługi Azure Data Explorer za pomocą metryk

Usługa Azure Data Explorer to szybka, w pełni zarządzana usługa do analizy danych, która pozwala w czasie rzeczywistym analizować duże woluminy danych przesyłanych strumieniowo z aplikacji, witryn internetowych, urządzeń IoT i nie tylko. Aby używać usługi Azure Data Explorer, najpierw utwórz klaster, a następnie utwórz w tym klastrze co najmniej jedną bazę danych. Następnie pozyskaj (załaduj) dane do bazy danych, aby uruchamiać w niej zapytania. Metryki usługi Azure Data Explorer zawierają kluczowe wskaźniki dotyczące kondycji i wydajności zasobów klastra. Użyj metryki, które są szczegółowo opisane w tym artykule do monitorowania kondycji i wydajności klastra usługi Azure Data Explorer w określonym scenariuszu jako metryki autonomiczne. Metryki można również używać jako podstawy operacyjnych [pulpitów nawigacyjnych platformy Azure](/azure/azure-portal/azure-portal-dashboards) i [alertów platformy Azure.](/azure/azure-monitor/platform/alerts-metric-overview)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto platformy Azure.](https://azure.microsoft.com/free/)
* [Klaster i baza danych](create-cluster-database-portal.md).

## <a name="using-metrics"></a>Korzystanie z danych

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
1. W klastrze usługi Azure Data Explorer wybierz **metryki,** aby otworzyć okienko metryk i rozpocząć analizę w klastrze.
    ![Wybierz Metryki](media/using-metrics/select-metrics.png).
1. W okienku Metryki: ![Metryki](media/using-metrics/metrics-pane.png)
    1. Aby utworzyć wykres metryczny, wybierz nazwę **metryki** i odpowiednią **agregację** na metrykę. Selektory **zasobów** i **metryki obszaru nazw** są wstępnie wybrane dla klastra usługi Azure Data Explorer. Aby uzyskać więcej informacji na temat różnych metryk, zobacz [obsługiwane metryki usługi Azure Data Explorer](#supported-azure-data-explorer-metrics).
    1. Wybierz **dodaj metrykę,** aby wyświetlić wiele metryk wykreślonych na tym samym wykresie.
    1. Wybierz **+ Nowy wykres,** aby wyświetlić wiele wykresów w jednym widoku.
    1. Użyj selektora czasu, aby zmienić zakres czasu (domyślnie: ostatnie 24 godziny).
    1. Użyj [ **dodaj filtru** i **Zastosuj dzielenie** ](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) dla danych, które mają wymiary.
    1. Wybierz **pozycję Przypnij do pulpitu nawigacyjnego,** aby dodać konfigurację wykresu do pulpitów nawigacyjnych, aby można było ją wyświetlić ponownie.
    1. Ustaw **nową regułę alertu,** aby wizualizować metryki przy użyciu ustawionych kryteriów. Nowa reguła alertów będzie zawierać zasób docelowy, metrykę, dzielenie i filtrowanie wymiarów z wykresu. Zmodyfikuj te ustawienia w [okienku tworzenia reguły alertu](/azure/azure-monitor/platform/metrics-charts#create-alert-rules).

Dodatkowe informacje na temat korzystania z [Eksploratora metryk](/azure/azure-monitor/platform/metrics-getting-started).

## <a name="supported-azure-data-explorer-metrics"></a>Obsługiwane metryki usługi Azure Data Explorer

Obsługiwane metryki usługi Azure Data Explorer są podzielone na różne kategorie zgodnie z użyciem. 

### <a name="cluster-health-metrics"></a>Metryki kondycji klastra

Metryki kondycji klastra śledzą ogólny stan kondycji klastra. Obejmuje to wykorzystanie zasobów i pozyskiwania oraz czas reakcji.

**Metryka** | **Jednostki** | **Agregacja** | **Opis metryki** | **Wymiary** |
|---|---|---|---|---|
| Wykorzystanie pamięci podręcznej | Wartość procentowa | Średnia, Max, Min | Procent przydzielonych zasobów pamięci podręcznej aktualnie używanych przez klaster. Pamięć podręczna to rozmiar ssd przydzielony do aktywności użytkownika zgodnie ze zdefiniowanymi zasadami pamięci podręcznej. Średnie wykorzystanie pamięci podręcznej 80% lub mniej jest stanem zrównoważonego dla klastra. Jeśli średnie wykorzystanie pamięci podręcznej jest powyżej 80%, klaster powinien być [skalowany](manage-cluster-vertical-scaling.md) do warstwy cenowej zoptymalizowanej pod kątem magazynu lub [skalowany w poziomie](manage-cluster-horizontal-scaling.md) do większej liczby wystąpień. Alternatywnie dostosować zasady pamięci podręcznej (mniej dni w pamięci podręcznej). Jeśli wykorzystanie pamięci podręcznej jest ponad 100%, rozmiar danych, które mają być buforowane, zgodnie z zasadami buforowania, jest większy niż całkowity rozmiar pamięci podręcznej w klastrze. | Brak |
| Procesor CPU | Wartość procentowa | Średnia, Max, Min | Procent przydzielonych zasobów obliczeniowych aktualnie używanych przez maszyny w klastrze. Średni procesor CPU 80% lub mniej jest zrównoważony dla klastra. Maksymalna wartość procesora CPU wynosi 100%, co oznacza, że nie ma żadnych dodatkowych zasobów obliczeniowych do przetwarzania danych. Gdy klaster nie działa dobrze, sprawdź maksymalną wartość procesora CPU, aby ustalić, czy istnieją określone procesory, które są zablokowane. | Brak |
| Wykorzystanie spożycia | Wartość procentowa | Średnia, Max, Min | Procent rzeczywistych zasobów używanych do pozyskiwania danych z całkowitych zasobów przydzielonych w zasadach zdolności produkcyjnych do wykonania pozyskiwania. Domyślna zasada zdolności produkcyjnych to nie więcej niż 512 równoczesnych operacji pozyskiwania lub 75% zasobów klastra zainwestowanych w pozyskiwania. Średnie wykorzystanie spożycia 80% lub mniej jest stanem zrównoważonego dla klastra. Maksymalna wartość wykorzystania pozyskiwania wynosi 100%, co oznacza, że używana jest cała zdolność pozyskiwania klastra i może spowodować kolejkę pozyskiwania. | Brak |
| Utrzymać przy życiu | Liczba | Średnia | Śledzi czas reakcji klastra. Klaster w pełni responsywny zwraca wartość 1, a zablokowany lub rozłączony klaster zwraca wartość 0. |
| Całkowita liczba poleceń z ograniczeniem | Liczba | Średnia, Max, Min, Sum | Liczba ograniczonych (odrzuconych) poleceń w klastrze, ponieważ osiągnięto maksymalną dozwoloną liczbę poleceń równoczesnych (równoległych). | Brak |
| Łączna liczba zakresów | Liczba | Średnia, Max, Min, Sum | Całkowita liczba zakresów danych w klastrze. Zmiany w tej metryki może oznaczać ogromne zmiany struktury danych i duże obciążenie klastra, ponieważ scalanie zakresów danych jest działaniem cpu-heavy. | Brak |
| | | | |

### <a name="export-health-and-performance-metrics"></a>Eksportowanie danych o kondycji i wydajności

Metryki kondycji i wydajności eksportu śledzą ogólny stan zdrowia i wydajność operacji eksportu, takich jak opóźnienie, wyniki, liczba rekordów i wykorzystanie.

**Metryka** | **Jednostki** | **Agregacja** | **Opis metryki** | **Wymiary** |
|---|---|---|---|---|
Ciągła liczba eksportowanych rekordów    | Liczba | Suma | Liczba eksportowanych rekordów we wszystkich zadaniach ciągłego eksportowania. | Brak |
Ciągły eksport max spóźnienie |    Liczba   | Maks.   | Opóźnienie (w minutach) zgłaszane przez zadania ciągłego eksportu w klastrze. | Brak |
Ciągła liczba oczekujących na eksport | Liczba | Maks.   | Liczba oczekujących zadań ciągłego eksportu. Te zadania są gotowe do uruchomienia, ale czeka w kolejce, prawdopodobnie z powodu niewystarczającej pojemności). 
Ciągły wynik eksportu    | Liczba |   Liczba   | Wynik niepowodzenia/sukcesu każdego ciągłego uruchamiania eksportu. | Ciągła nazwa ekspportu |
Wykorzystanie eksportu |    Wartość procentowa | Maks.   | Wykorzystana zdolność eksportowa, z całkowitej zdolności eksportowej w klastrze (od 0 do 100). | Brak |
| | | | |

### <a name="ingestion-health-and-performance-metrics"></a>Wskaźniki kondycji i wydajności pozyskiwania

Metryki kondycji i wydajności pozyskiwania śledzą ogólny stan zdrowia i wydajność operacji pozyskiwania, takich jak opóźnienie, wyniki i objętość.

**Metryka** | **Jednostki** | **Agregacja** | **Opis metryki** | **Wymiary** |
|---|---|---|---|---|
| Przetwarzane zdarzenia (dla centrów zdarzeń/IoT) | Liczba | Max, Min, Suma | Całkowita liczba zdarzeń odczytywanych z centrów zdarzeń i przetwarzanych przez klaster. Zdarzenia są podzielone na zdarzenia odrzucone i zdarzenia akceptowane przez aparat klastra. | EventStatus |
| Opóźnienie połknięcia | Sekundy | Średnia, Max, Min | Opóźnienie pozyskanych danych, od momentu odebraniem danych w klastrze, aż do momentu, gdy będą gotowe do kwerendy. Okres opóźnienia pozyskiwania zależy od scenariusza pozyskiwania. | Brak |
| Wynik spożycia | Liczba | Liczba | Całkowita liczba operacji pozyskiwania, które zakończyły się niepowodzeniem i powiodły się. Użyj **podziału zastosuj,** aby utworzyć zasobniki wyników sukcesu i niepowodzenia oraz analizować wymiary (**Stan****wartości** > ).| PołknięciaWyskłości szczegółowe |
| Głośność pozyskiwania (w MB) | Liczba | Maks., Suma | Całkowity rozmiar danych przyciągniętych do klastra (w MB) przed kompresją. | baza danych |
| | | | |  

### <a name="query-performance"></a>Wydajność zapytań

Metryki wydajności kwerendy śledzą czas trwania kwerendy i całkowitą liczbę zapytań równoczesnych lub ograniczonych.

**Metryka** | **Jednostki** | **Agregacja** | **Opis metryki** | **Wymiary** |
|---|---|---|---|---|
| Czas trwania kwerendy | Milisekund | Średnia, Min, Max, Sum | Całkowity czas do odebraniu wyników kwerendy (nie obejmuje opóźnienia sieci). | Querystatus |
| Całkowita liczba równoczesnych zapytań | Liczba | Średnia, Max, Min, Sum | Liczba zapytań uruchamianych równolegle w klastrze. Ta metryka jest dobrym sposobem, aby oszacować obciążenie klastra. | Brak |
| Całkowita liczba kwerend z ograniczeniami | Liczba | Średnia, Max, Min, Sum | Liczba ograniczonych (odrzuconych) zapytań w klastrze. Maksymalna dozwolona liczba równoczesnych (równoległych) zapytań jest zdefiniowana w zasadach równoczesnych zapytań. | Brak |
| | | | |

### <a name="streaming-ingest-metrics"></a>Dane dotyczące pozyskiwania przesyłania strumieniowego

Metryki pozyskiwania przesyłania strumieniowego śledzą dane pozyskiwania przesyłania strumieniowego i szybkość żądania, czas trwania i wyniki.

**Metryka** | **Jednostki** | **Agregacja** | **Opis metryki** | **Wymiary** |
|---|---|---|---|---|
Szybkość pozyskiwania danych przesyłania strumieniowego |    Liczba   | RateRequestsPerSecond | Całkowita ilość danych przyciągniętych do klastra. | Brak |
Czas pozyskiwania przesyłania strumieniowego   | Milisekund  | Średnia, Max, Min | Całkowity czas trwania wszystkich żądań pozyskiwania przesyłania strumieniowego. | Brak |
Szybkość pozyskiwania przesyłania strumieniowego   | Liczba | Liczba, Średnia, Max, Min, Sum | Całkowita liczba żądań pozyskiwania przesyłania strumieniowego. | Brak |
Streaming Ingest Wynik | Liczba | Średnia   | Całkowita liczba żądań pozyskiwania przesyłania strumieniowego według typu wyniku. | Wynik |
| | | | |

Dodatkowe informacje na temat [obsługiwanych metryk klastra usługi Azure Data Explorer](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters).


## <a name="next-steps"></a>Następne kroki

* [Samouczek: Pozyskiwania i wykonywania zapytań danych monitorowania danych w Eksploratorze danych platformy Azure](/azure/data-explorer/ingest-data-no-code)
* [Monitorowanie operacji pozyskiwania usługi Azure Data Explorer przy użyciu dzienników diagnostycznych](/azure/data-explorer/using-diagnostic-logs)
* [Szybki start: wykonywanie zapytań o dane w usłudze Azure Data Explorer](web-query-data.md)
