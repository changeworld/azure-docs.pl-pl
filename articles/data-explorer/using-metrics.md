---
title: Monitoruj wydajność, kondycję i użycie usługi Azure Eksplorator danych przy użyciu metryk
description: Dowiedz się, jak używać metryk Eksplorator danych platformy Azure do monitorowania wydajności, kondycji i użycia klastra.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: f8078d8bae00ac4789a679be4d7a1944c749cce6
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77423848"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Monitoruj wydajność, kondycję i użycie usługi Azure Eksplorator danych przy użyciu metryk

Usługa Azure Data Explorer to szybka, w pełni zarządzana usługa do analizy danych, która pozwala w czasie rzeczywistym analizować duże woluminy danych przesyłanych strumieniowo z aplikacji, witryn internetowych, urządzeń IoT i nie tylko. Aby używać usługi Azure Data Explorer, najpierw utwórz klaster, a następnie utwórz w tym klastrze co najmniej jedną bazę danych. Następnie pozyskaj (załaduj) dane do bazy danych, aby uruchamiać w niej zapytania. Metryki usługi Azure Eksplorator danych zapewniają kluczowe wskaźniki dotyczące kondycji i wydajności zasobów klastra. Aby monitorować kondycję i wydajność klastra platformy Azure Eksplorator danych w konkretnym scenariuszu jako metryki autonomiczne, należy użyć metryk, które zostały szczegółowo opisane w tym artykule. Możesz również użyć metryk jako podstawy dla operacyjnych [pulpitów nawigacyjnych platformy Azure](/azure/azure-portal/azure-portal-dashboards) i [alertów platformy Azure](/azure/azure-monitor/platform/alerts-metric-overview).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
* [Klaster i baza danych](create-cluster-database-portal.md).

## <a name="using-metrics"></a>Korzystanie z metryk

1. Zaloguj się do [Azure portal](https://portal.azure.com/).
1. W klastrze usługi Azure Eksplorator danych wybierz pozycję **metryki** , aby otworzyć okienko metryki i rozpocząć analizowanie w klastrze.
    ![wybierz metryki](media/using-metrics/select-metrics.png).
1. W okienku metryki: ![okienku metryk](media/using-metrics/metrics-pane.png)
    1. Aby utworzyć wykres pomiarowy, wybierz pozycję Nazwa **metryki** i odpowiednia **agregacja** na metrykę. Selektory **przestrzeni nazw** **zasobów** i metryk są wstępnie wybrane dla klastra Eksplorator danych platformy Azure. Aby uzyskać więcej informacji na temat różnych metryk, zobacz [obsługiwane metryki usługi Azure Eksplorator danych](#supported-azure-data-explorer-metrics).
    1. Wybierz pozycję **Dodaj metrykę** , aby zobaczyć wiele metryk wykreślonych na tym samym wykresie.
    1. Wybierz pozycję **+ Nowy wykres** , aby wyświetlić wiele wykresów w jednym widoku.
    1. Użyj selektora czas, aby zmienić zakres czasu (domyślnie: ostatnie 24 godziny).
    1. Użyj [ **Dodaj filtr** i **Zastosuj podział** ](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) dla metryk, które mają wymiary.
    1. Wybierz pozycję **Przypnij do pulpitu nawigacyjnego** , aby dodać konfigurację wykresu do pulpitów nawigacyjnych, aby można było wyświetlić ją ponownie.
    1. Ustaw **nową regułę alertu** , aby wizualizować metryki przy użyciu ustawionych kryteriów. Nowa reguła alertów będzie obejmować zasób docelowy, metrykę, podział i wymiary filtru z wykresu. Zmodyfikuj te ustawienia w [okienku tworzenia reguły alertów](/azure/azure-monitor/platform/metrics-charts#create-alert-rules).

Dodatkowe informacje na temat korzystania z [Eksplorator metryk](/azure/azure-monitor/platform/metrics-getting-started).

## <a name="supported-azure-data-explorer-metrics"></a>Obsługiwane metryki usługi Azure Eksplorator danych

Obsługiwane metryki usługi Azure Eksplorator danych są rozdzielone na różne kategorie zgodnie z użyciem. 

### <a name="cluster-health-metrics"></a>Metryki kondycji klastra

Metryki kondycji klastra śledzą ogólną kondycję klastra. Obejmuje to wykorzystanie zasobów i pozyskiwanie oraz czas odpowiedzi.

**Metryka** | **Jednostka** | **Agregacja** | **Opis metryki** | **Wymiary** |
|---|---|---|---|---|
| Użycie pamięci podręcznej | Procent | Średnia, maks., minimum | Procent przyznanych zasobów pamięci podręcznej, które są obecnie używane przez klaster. Pamięć podręczna to rozmiar dysku SSD przydzieloną dla aktywności użytkownika zgodnie ze zdefiniowanymi zasadami pamięci podręcznej. Średnie użycie pamięci podręcznej wynoszącej 80% lub mniej jest trwałym stanem klastra. Jeśli średnie wykorzystanie pamięci podręcznej jest powyżej 80%, klaster należy [skalować w górę](manage-cluster-vertical-scaling.md) do warstwy cenowej zoptymalizowanej pod kątem magazynu lub [skalować](manage-cluster-horizontal-scaling.md) do większej liczby wystąpień. Alternatywnie możesz dostosować zasady pamięci podręcznej (mniej dni w pamięci podręcznej). Jeśli użycie pamięci podręcznej wynosi ponad 100%, rozmiar danych, które mają być buforowane, zgodnie z zasadami buforowania, jest większy niż całkowity rozmiar pamięci podręcznej w klastrze. | None |
| Procesor CPU | Procent | Średnia, maks., minimum | Procent przyznanych zasobów obliczeniowych, które są obecnie używane przez maszyny w klastrze. Średni procesor CPU wynoszący 80% lub mniej jest trwały dla klastra. Maksymalna wartość procesora CPU to 100%, co oznacza, że nie ma dodatkowych zasobów obliczeniowych do przetwarzania danych. Gdy klaster nie działa prawidłowo, sprawdź maksymalną wartość procesora CPU, aby określić, czy istnieją konkretne procesora CPU, które są blokowane. | None |
| Wykorzystanie pozyskiwania | Procent | Średnia, maks., minimum | Procent rzeczywistych zasobów używanych do pozyskiwania danych z łącznej liczby przyznanych zasobów w ramach zasad pojemności, aby przeprowadzić pozyskiwanie. Domyślne zasady pojemności nie przekraczają 512 współbieżnych operacji pozyskiwania lub 75% zasobów klastra zainwestowanych w pozyskiwanie. Średnie wykorzystanie pozyskiwania 80% lub mniej jest trwałym stanem klastra. Maksymalna wartość wykorzystania pozyskiwania to 100%, co oznacza, że wszystkie możliwości pozyskiwania klastra są używane, a kolejka pozyskiwania może wynikać z kolejki pozyskiwania. | None |
| Utrzymywanie aktywności | Licznik | Śr | Śledzi czas odpowiedzi klastra. W pełni reagujący klaster zwraca wartość 1, a zablokowany lub odłączony klaster zwraca 0. |
| Łączna liczba poleceń z ograniczeniami | Licznik | AVG, Max, min, sum | Liczba poleceń z ograniczeniami (odrzuconych) w klastrze, ponieważ osiągnięto maksymalną dozwoloną liczbę poleceń współbieżnych (równoległych). | None |
| Łączna Liczba zakresów | Licznik | AVG, Max, min, sum | Łączna Liczba zakresów danych w klastrze. Zmiany w tej metryce mogą oznaczać ogromne zmiany struktury danych i duże obciążenie klastra, ponieważ scalanie zakresów danych jest działaniem intensywnie obciążającym Procesor. | None |
| | | | |

### <a name="export-health-and-performance-metrics"></a>Eksportowanie metryk kondycji i wydajności

Eksportowanie metryk kondycji i wydajności śledzą ogólną kondycję i wydajność operacji eksportu, takich jak późność, wyniki, liczba rekordów i użycie.

**Metryka** | **Jednostka** | **Agregacja** | **Opis metryki** | **Wymiary** |
|---|---|---|---|---|
Ciągły eksport liczby eksportowanych rekordów    | Licznik | Suma | Liczba wyeksportowanych rekordów we wszystkich zadaniach eksportu ciągłego. | None |
Maksymalna liczba opóźnień eksportu ciągłego |    Licznik   | Maks.   | Opóźnienie (w minutach) raportowane przez zadania eksportu ciągłego w klastrze. | None |
Liczba oczekujących eksportu ciągłego | Licznik | Maks.   | Liczba oczekujących zadań eksportu ciągłego. Te zadania są gotowe do uruchomienia, ale oczekują w kolejce, prawdopodobnie z powodu niewystarczającej pojemności. 
Wynik eksportu ciągłego    | Licznik |   Licznik   | Wynik błędu/sukcesu każdego przebiegu eksportu ciągłego. | ContinuousExportName |
Użycie eksportu |    Procent | Maks.   | Wykorzystano pojemność eksportu z całkowitej pojemności eksportu w klastrze (od 0 do 100). | None |
| | | | |

### <a name="ingestion-health-and-performance-metrics"></a>Metryki kondycji i wydajności pozyskiwania

Metryki kondycji i wydajności pozyskiwania śledzą ogólną kondycję i wydajność operacji pozyskiwania, takich jak opóźnienia, wyniki i wolumin.

**Metryka** | **Jednostka** | **Agregacja** | **Opis metryki** | **Wymiary** |
|---|---|---|---|---|
| Zdarzenia przetwarzane (dla centrów zdarzeń/IoT) | Licznik | Max, min, sum | Łączna liczba zdarzeń odczytanych z centrów zdarzeń i przetworzonych przez klaster. Zdarzenia są podzielone na zdarzenia odrzucone i zdarzenia akceptowane przez aparat klastra. | EventStatus |
| Opóźnienie pozyskiwania | S | Średnia, maks., minimum | Opóźnienie pozyskiwania danych od momentu odebrania danych w klastrze do momentu, aż będzie gotowe do zapytania. Okres opóźnienia pozyskiwania zależy od scenariusza pozyskiwania. | None |
| Wynik pozyskiwania | Licznik | Licznik | Łączna liczba operacji pozyskiwania zakończonych niepowodzeniem i zakończonych pomyślnie. Użyj **dzielenia** , aby utworzyć zasobniki sukcesów i niepowodzeń oraz analizować Wymiary (**wartość** > **status**).| IngestionResultDetails |
| Wolumin pozyskiwania (w MB) | Licznik | Max, sum | Łączny rozmiar danych pozyskanych w klastrze (w MB) przed kompresją. | Baza danych |
| | | | |  

### <a name="query-performance"></a>Wydajność zapytań

Zapytanie metryk wydajności zapytania Śledź czas trwania zapytania i łączną liczbę zapytań współbieżnych lub ograniczających.

**Metryka** | **Jednostka** | **Agregacja** | **Opis metryki** | **Wymiary** |
|---|---|---|---|---|
| Czas trwania zapytania | Milisekundy | Średnia, minimalna, maksymalna, suma | Łączny czas do odebrania wyników zapytania (nie obejmuje opóźnienia sieci). | QueryStatus |
| Łączna liczba współbieżnych zapytań | Licznik | AVG, Max, min, sum | Liczba zapytań wykonywanych równolegle w klastrze. Ta Metryka jest dobrym sposobem oszacowania obciążenia w klastrze. | None |
| Łączna liczba zapytań z ograniczeniami | Licznik | AVG, Max, min, sum | Liczba zapytań z ograniczeniami (odrzuconych) w klastrze. Maksymalna dozwolona liczba współbieżnych (równoległych) zapytań jest definiowana w zasadzie współbieżnych zapytań. | None |
| | | | |

### <a name="streaming-ingest-metrics"></a>Metryki pozyskiwania strumieniowego

Metryki pozyskiwania przesyłania strumieniowego śledzą dane pozyskiwania przesyłania strumieniowego oraz liczbę żądań, czas trwania i wyniki.

**Metryka** | **Jednostka** | **Agregacja** | **Opis metryki** | **Wymiary** |
|---|---|---|---|---|
Szybkość danych pozyskiwania przesyłania strumieniowego |    Licznik   | RateRequestsPerSecond | Całkowita ilość danych pozyskanych w klastrze. | None |
Czas trwania pozyskiwania strumieniowego   | Milisekundy  | Średnia, maks., minimum | Łączny czas trwania wszystkich żądań pozyskiwania przesyłania strumieniowego. | None |
Szybkość żądania pozyskiwania strumieniowego   | Licznik | Liczba, średnia, Max, min, suma | Całkowita liczba żądań pozyskiwania przesyłania strumieniowego. | None |
Wynik pozyskiwania strumieniowego | Licznik | Śr   | Całkowita liczba żądań pozyskiwania strumieniowego według typu wyniku. | Wynik |
| | | | |

Dodatkowe informacje na temat [obsługiwanych metryk klastra Eksplorator danych platformy Azure](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters).


## <a name="next-steps"></a>Następne kroki

* [Samouczek: pozyskiwanie i wykonywanie zapytań dotyczących danych monitorowania na platformie Azure Eksplorator danych](/azure/data-explorer/ingest-data-no-code)
* [Monitorowanie operacji pozyskiwania Eksplorator danych platformy Azure przy użyciu dzienników diagnostycznych](/azure/data-explorer/using-diagnostic-logs)
* [Szybki start: wykonywanie zapytań o dane w usłudze Azure Data Explorer](web-query-data.md)
