---
title: Monitoruj wydajność, kondycję i użycie usługi Azure Eksplorator danych przy użyciu metryk
description: Dowiedz się, jak używać metryk Eksplorator danych platformy Azure do monitorowania wydajności, kondycji i użycia klastra.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: 82aa7f782dbb1842a29d55eef8983edd4afce8eb
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277417"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Monitoruj wydajność, kondycję i użycie usługi Azure Eksplorator danych przy użyciu metryk

Usługa Azure Data Explorer to szybka, w pełni zarządzana usługa do analizy danych, która pozwala w czasie rzeczywistym analizować duże woluminy danych przesyłanych strumieniowo z aplikacji, witryn internetowych, urządzeń IoT i nie tylko. Aby używać usługi Azure Data Explorer, najpierw utwórz klaster, a następnie utwórz w tym klastrze co najmniej jedną bazę danych. Następnie pozyskaj (załaduj) dane do bazy danych, aby uruchamiać w niej zapytania. Metryki usługi Azure Eksplorator danych zapewniają kluczowe wskaźniki dotyczące kondycji i wydajności zasobów klastra. Aby monitorować kondycję i wydajność klastra platformy Azure Eksplorator danych w konkretnym scenariuszu jako metryki autonomiczne, należy użyć metryk, które zostały szczegółowo opisane w tym artykule. Możesz również użyć metryk jako podstawy dla operacyjnych [pulpitów nawigacyjnych platformy Azure](/azure/azure-portal/azure-portal-dashboards) i [alertów platformy Azure](/azure/azure-monitor/platform/alerts-metric-overview).

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

* Utwórz [klaster i bazę danych](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [Portalu Azure](https://portal.azure.com/).

## <a name="using-metrics"></a>Korzystanie z metryk

W klastrze usługi Azure Eksplorator danych wybierz pozycję **metryki** , aby otworzyć okienko metryki i rozpocząć analizowanie w klastrze.

![Wybierz metryki](media/using-metrics/select-metrics.png)

W okienku metryki:

![Okienko metryk](media/using-metrics/metrics-pane.png)

1. Aby utworzyć wykres pomiarowy, wybierz pozycję Nazwa **metryki** i odpowiednia **agregacja** na metrykę. Selektory **przestrzeni nazw** **zasobów** i metryk są wstępnie wybrane dla klastra Eksplorator danych platformy Azure. Aby uzyskać więcej informacji na temat różnych metryk, zobacz [obsługiwane metryki usługi Azure Eksplorator danych](#supported-azure-data-explorer-metrics).
1. Wybierz przycisk **Dodaj metrykę** , aby zobaczyć wiele metryk wykreślonych na tym samym wykresie.
1. Wybierz przycisk **+ Nowy wykres** , aby wyświetlić wiele wykresów w jednym widoku.
1. Użyj selektora czas, aby zmienić zakres czasu (domyślnie: ostatnie 24 godziny).
1. Użyj [ **Dodaj filtr** i **Zastosuj podział** ](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) dla metryk, które mają wymiary.
1. Wybierz pozycję **Przypnij do pulpitu nawigacyjnego** , aby dodać konfigurację wykresu do pulpitów nawigacyjnych, aby można było wyświetlić ją ponownie.
1. Ustaw **nową regułę alertu** , aby wizualizować metryki przy użyciu ustawionych kryteriów. Nowa reguła alertów będzie obejmować zasób docelowy, metrykę, podział i wymiary filtru z wykresu. Zmodyfikuj te ustawienia w [okienku tworzenia reguły alertów](/azure/azure-monitor/platform/metrics-charts#create-alert-rules).

Dodatkowe informacje na temat korzystania z [Eksplorator metryk](/azure/azure-monitor/platform/metrics-getting-started).

## <a name="supported-azure-data-explorer-metrics"></a>Obsługiwane metryki usługi Azure Eksplorator danych

Obsługiwane metryki usługi Azure Eksplorator danych są rozdzielone na różne kategorie zgodnie z użyciem. 

### <a name="cluster-health-metrics"></a>Metryki kondycji klastra

Metryki kondycji klastra śledzą ogólną kondycję klastra. Obejmuje to wykorzystanie zasobów i pozyskiwanie oraz czas odpowiedzi.

**Metryka** | **Unit** | **Agregacja** | **Opis metryki**
|---|---|---|---|
| Użycie pamięci podręcznej | Procent | Średnia, maks., minimum | Procent przyznanych zasobów pamięci podręcznej, które są obecnie używane przez klaster. Pamięć podręczna to rozmiar dysku SSD przydzieloną dla aktywności użytkownika zgodnie ze zdefiniowanymi zasadami pamięci podręcznej. Średnie użycie pamięci podręcznej wynoszącej 80% lub mniej jest trwałym stanem klastra. Jeśli średnie wykorzystanie pamięci podręcznej jest powyżej 80%, klaster należy [skalować w górę](manage-cluster-vertical-scaling.md) do warstwy cenowej zoptymalizowanej pod kątem magazynu lub [skalować](manage-cluster-horizontal-scaling.md) do większej liczby wystąpień. Alternatywnie możesz dostosować zasady pamięci podręcznej (mniej dni w pamięci podręcznej). Jeśli użycie pamięci podręcznej wynosi ponad 100%, rozmiar danych, które mają być buforowane, zgodnie z zasadami buforowania, jest większy niż całkowity rozmiar pamięci podręcznej w klastrze. |
| Procesor CPU | Procent | Średnia, maks., minimum | Procent przyznanych zasobów obliczeniowych, które są obecnie używane przez maszyny w klastrze. Średni procesor CPU wynoszący 80% lub mniej jest trwały dla klastra. Maksymalna wartość procesora CPU to 100%, co oznacza, że nie ma dodatkowych zasobów obliczeniowych do przetwarzania danych. Gdy klaster nie działa prawidłowo, sprawdź maksymalną wartość procesora CPU, aby określić, czy istnieją konkretne procesora CPU, które są blokowane. |
| Wykorzystanie pozyskiwania | Procent | Średnia, maks., minimum | Procent rzeczywistych zasobów używanych do pozyskiwania danych z łącznej liczby przyznanych zasobów w ramach zasad pojemności, aby przeprowadzić pozyskiwanie. Domyślne zasady pojemności nie przekraczają 512 współbieżnych operacji pozyskiwania lub 75% zasobów klastra zainwestowanych w pozyskiwanie. Średnie wykorzystanie pozyskiwania 80% lub mniej jest trwałym stanem klastra. Maksymalna wartość wykorzystania pozyskiwania to 100%, co oznacza, że wszystkie możliwości pozyskiwania klastra są używane, a kolejka pozyskiwania może wynikać z kolejki pozyskiwania. |
| Utrzymywanie aktywności | Liczba | Śr. | Śledzi czas odpowiedzi klastra. W pełni reagujący klaster zwraca wartość 1, a zablokowany lub odłączony klaster zwraca 0. |
| Łączna liczba poleceń z ograniczeniami | Liczba | AVG, Max, min, sum | Liczba poleceń z ograniczeniami (odrzuconych) w klastrze, ponieważ osiągnięto maksymalną dozwoloną liczbę poleceń współbieżnych (równoległych). |
| Łączna Liczba zakresów | Liczba | AVG, Max, min, sum | Łączna Liczba zakresów danych w klastrze. Zmiany w tej metryce mogą oznaczać ogromne zmiany struktury danych i duże obciążenie klastra, ponieważ scalanie zakresów danych jest działaniem intensywnie obciążającym Procesor. |
| | | | |

### <a name="export-health-and-performance-metrics"></a>Eksportowanie metryk kondycji i wydajności

Eksportowanie metryk kondycji i wydajności śledzą ogólną kondycję i wydajność operacji eksportu, takich jak późność, wyniki, liczba rekordów i użycie.

**Metryka** | **Unit** | **Agregacja** | **Opis metryki**
|---|---|---|---|
Ciągły eksport liczby eksportowanych rekordów    | Liczba | Suma | Łączna liczba rekordów wyeksportowanych z klastra. |
Maksymalna liczba minut opóźnienia eksportu |    Liczba   | Maks.   | Maksymalna wartość w minutach wyeksportowanych rekordów.|
Liczba oczekujących eksportu ciągłego | Liczba | Maks.   | Maksymalna wartość oczekujących operacji eksportowania.
Wynik eksportu ciągłego    | Liczba |   Liczba   | Łączna liczba operacji eksportu ciągłego przez wynik. Metryka obejmuje nazwę eksportu ciągłego i bazę danych. 
Użycie eksportu |    Procent | Maks.   | Użycie zdefiniowanego gniazda dla operacji eksportowania.
| | | | |

### <a name="ingestion-health-and-performance-metrics"></a>Metryki kondycji i wydajności pozyskiwania

Metryki kondycji i wydajności pozyskiwania śledzą ogólną kondycję i wydajność operacji pozyskiwania, takich jak opóźnienia, wyniki i wolumin.

**Metryka** | **Unit** | **Agregacja** | **Opis metryki**
|---|---|---|---|
| Zdarzenia przetwarzane (dla centrów zdarzeń/IoT) | Liczba | Max, min, sum | Łączna liczba zdarzeń odczytanych z centrów zdarzeń i przetworzonych przez klaster. Zdarzenia są podzielone na zdarzenia odrzucone i zdarzenia akceptowane przez aparat klastra. |
| Opóźnienie pozyskiwania | Sekundy | Średnia, maks., minimum | Opóźnienie pozyskiwania danych od momentu odebrania danych w klastrze do momentu, aż będzie gotowe do zapytania. Okres opóźnienia pozyskiwania zależy od scenariusza pozyskiwania. |
| Wynik pozyskiwania | Liczba | Liczba | Łączna liczba operacji pozyskiwania zakończonych niepowodzeniem i zakończonych pomyślnie. Użyj **dzielenia** , aby utworzyć zasobniki sukcesów i niepowodzeń oraz analizować Wymiary (**wartość** > **status**).|
| Wolumin pozyskiwania (w MB) | Liczba | Max, sum | Łączny rozmiar danych pozyskanych w klastrze (w MB) przed kompresją. |
| | | | |  

### <a name="query-performance"></a>Wydajność zapytań

Zapytanie metryk wydajności zapytania Śledź czas trwania zapytania i łączną liczbę zapytań współbieżnych lub ograniczających.

**Metryka** | **Unit** | **Agregacja** | **Opis metryki**
|---|---|---|---|
| Czas trwania zapytania | MS | Średnia, minimalna, maksymalna, suma | Łączny czas do odebrania wyników zapytania (nie obejmuje opóźnienia sieci). |
| Łączna liczba współbieżnych zapytań | Liczba | AVG, Max, min, sum | Liczba zapytań wykonywanych równolegle w klastrze. Ta Metryka jest dobrym sposobem oszacowania obciążenia w klastrze. |
| Łączna liczba zapytań z ograniczeniami | Liczba | AVG, Max, min, sum | Liczba zapytań z ograniczeniami (odrzuconych) w klastrze. Maksymalna dozwolona liczba współbieżnych (równoległych) zapytań jest definiowana w zasadzie współbieżnych zapytań. |
| | | | |

### <a name="streaming-ingest-metrics"></a>Metryki pozyskiwania strumieniowego

Metryki pozyskiwania przesyłania strumieniowego śledzą dane pozyskiwania przesyłania strumieniowego oraz liczbę żądań, czas trwania i wyniki.

**Metryka** | **Unit** | **Agregacja** | **Opis metryki**
|---|---|---|---|
Szybkość danych pozyskiwania przesyłania strumieniowego |    Liczba   | RateRequestsPerSecond | Całkowita ilość danych pozyskanych w klastrze. |
Czas trwania pozyskiwania strumieniowego   | MS  | Średnia, maks., minimum | Łączny czas trwania wszystkich żądań pozyskiwania przesyłania strumieniowego. |
Szybkość żądania pozyskiwania strumieniowego   | Liczba | Liczba, średnia, Max, min, suma | Całkowita liczba żądań pozyskiwania przesyłania strumieniowego. |
Wynik pozyskiwania strumieniowego | Liczba | Śr.   | Całkowita liczba żądań pozyskiwania strumieniowego według typu wyniku. |
| | | | |

Dodatkowe informacje na temat [obsługiwanych metryk klastra Eksplorator danych platformy Azure](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)


## <a name="next-steps"></a>Następne kroki

* [Samouczek: pozyskiwanie i wykonywanie zapytań dotyczących danych monitorowania na platformie Azure Eksplorator danych](/azure/data-explorer/ingest-data-no-code)
* [Monitorowanie operacji pozyskiwania Eksplorator danych platformy Azure przy użyciu dzienników diagnostycznych](/azure/data-explorer/using-diagnostic-logs)
* [Szybki start: wykonywanie zapytań o dane w usłudze Azure Data Explorer](web-query-data.md)
