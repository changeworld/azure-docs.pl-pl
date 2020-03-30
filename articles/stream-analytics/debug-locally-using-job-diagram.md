---
title: Debugowanie zapytań usługi Azure Stream Analytics lokalnie przy użyciu diagramu zadań w programie Visual Studio
description: W tym artykule opisano sposób debugowania kwerend lokalnie przy użyciu diagramu zadań w narzędziach Azure Stream Analytics Tools for Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 106b1f0b765700803d2cd55b5e049fae5be3dfad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76847201"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio"></a>Debugowanie zapytań usługi Azure Stream Analytics lokalnie przy użyciu diagramu zadań w programie Visual Studio

Zadania, które nie wyprowadzają żadnych wyników lub nieoczekiwanych wyników, są typowymi scenariuszami rozwiązywania problemów dla zapytań przesyłanych strumieniowo. Diagram zadań można użyć podczas testowania kwerendy lokalnie w programie Visual Studio, aby sprawdzić zestaw wyników pośrednich i metryki dla każdego kroku. Diagramy zadań mogą pomóc szybko wyizolować źródło problemu podczas rozwiązywania problemów.

## <a name="debug-a-query-using-job-diagram"></a>Debugowanie kwerendy przy użyciu diagramu zadań

Skrypt usługi Azure Stream Analytics służy do przekształcania danych wejściowych do danych wyjściowych. Diagram zadań pokazuje, jak przepływy danych ze źródeł wejściowych (Centrum zdarzeń, Centrum IoT itp.) za pośrednictwem wielu kroków kwerendy i na koniec do pochłaniaczy danych wyjściowych. Każdy krok kwerendy jest mapowany na tymczasowy zestaw `WITH` wyników zdefiniowany w skrypcie przy użyciu instrukcji. Można wyświetlić dane, a także metryki każdego kroku kwerendy w każdym zestawie wyników pośrednich, aby znaleźć źródło problemu.

> [!NOTE]
> Ten diagram zadań zawiera tylko dane i metryki do testowania lokalnego w jednym węźle. Nie należy go używać do dostrajania wydajności i rozwiązywania problemów.

### <a name="start-local-testing"></a>Rozpocznij testy lokalne

Skorzystaj z tego [przewodnika Szybki start,](stream-analytics-quick-create-vs.md) aby dowiedzieć się, jak utworzyć zadanie usługi Stream Analytics za pomocą programu Visual Studio lub [wyeksportować istniejące zadanie do projektu lokalnego.](stream-analytics-vs-tools.md#export-jobs-to-a-project) Jeśli chcesz przetestować kwerendę przy obliczu lokalnych danych wejściowych, postępuj zgodnie z tymi [instrukcjami](stream-analytics-live-data-local-testing.md). Jeśli chcesz przetestować dane wejściowe na żywo, przejdź do następnego kroku.

> [!NOTE]
> Jeśli eksportujesz zadanie do projektu lokalnego i chcesz przetestować strumień wejściowy na żywo, należy ponownie określić poświadczenia dla wszystkich danych wejściowych.  

Wybierz źródło danych wejściowych i wyjściowych z edytora skryptów i wybierz pozycję **Uruchom lokalnie**. Schemat zadań pojawi się po prawej stronie.

### <a name="view-the-intermediate-result-set"></a>Wyświetlanie zestawu wyników pośrednich  

1. Wybierz krok kwerendy, aby przejść do skryptu. Zostaniesz automatycznie przekierowany do odpowiedniego skryptu w edytorze po lewej stronie.

   ![Skrypt nawigacji diagramu zadań](./media/debug-locally-using-job-diagram/navigate-script.png)

2. Zaznacz krok kwerendy i wybierz **pozycję Podgląd** w wyświetlonym oknie dialogowym. Zestaw wyników jest wyświetlany na karcie w dolnym oknie wyników.

   ![Wynik podglądu diagramu zadania](./media/debug-locally-using-job-diagram/preview-result.png)

### <a name="view-step-metrics"></a>Wyświetlanie metryk kroku

W tej sekcji można eksplorować metryki dostępne dla każdej części diagramu.

#### <a name="input-sources-live-stream"></a>Źródła wejściowe (transmisja na żywo)

![Źródła wejściowe na żywo diagramu zadań](./media/debug-locally-using-job-diagram/live-input.png)

|Metryka|Opis|
|-|-|
|**TaxiRide (Kołysze)**| Nazwa danych wejściowych.|
|**Centrum zdarzeń** | Typ źródła wejściowego.|
|**Zdarzenia**|Liczba odczytanych zdarzeń.|
|**Zaległe źródła zdarzeń**|Ile jeszcze wiadomości należy odczytać dla centrów zdarzeń i danych wejściowych Usługi IoT Hub.|
|**Zdarzenia w bajtach**|Liczba odczytanych bajtów.|
| **Zdarzenia zdegradowane**|Liczba zdarzeń, które miały problem inny niż z deserializacji.|
|**Wczesne wydarzenia**| Liczba zdarzeń, które mają sygnaturę czasową aplikacji przed wysokim znakiem wodnym.|
|**Późne wydarzenia**| Liczba zdarzeń, które mają sygnaturę czasową aplikacji po wysokim znaku wodnym.|
|**Źródła zdarzeń**| Liczba odczytanych jednostek danych. Na przykład liczba obiektów blob.|

#### <a name="input-sources-local-input"></a>Źródła wejściowe (dane wejściowe lokalne)

![Lokalne źródła danych wejściowe diagramu zadań](./media/debug-locally-using-job-diagram/local-input.png)

|Metryka|Opis|
|-|-|
|**TaxiRide (Kołysze)**| Nazwa danych wejściowych.|
|**Liczba wierszy**| Liczba wierszy wygenerowanych z kroku.|
|**Rozmiar danych**| Rozmiar danych generowanych z tego kroku.|
|**Dane wejściowe lokalne**| Użyj danych lokalnych jako danych wejściowych.|

#### <a name="query-steps"></a>Kroki zapytania

![Krok kwerendy diagramu zadań](./media/debug-locally-using-job-diagram/query-step.png)

|Metryka|Opis|
|-|-|
|**Dane tripdata**|Nazwa zestawu wyników tymczasowych.|
|**Liczba wierszy**| Liczba wierszy wygenerowanych z kroku.|
|**Rozmiar danych**| Rozmiar danych generowanych z tego kroku.|
  
#### <a name="output-sinks-live-output"></a>Umywalki wyjściowe (wyjście na żywo)

![Diagram zadań lokalnych zlewów wyjściowych](./media/debug-locally-using-job-diagram/live-output.png)

|Metryka|Opis|
|-|-|
|**regionaggEH**|Nazwa danych wyjściowych.|
|**Zdarzenia**|Liczba zdarzeń danych wyjściowych do pochłaniania.|

#### <a name="output-sinks-local-output"></a>Pochłaniacze wyjściowe (wyjście lokalne)

![Diagram zadań lokalnych zlewów wyjściowych](./media/debug-locally-using-job-diagram/local-output.png)

|Metryka|Opis|
|-|-|
|**regionaggEH**|Nazwa danych wyjściowych.|
|**Wyjście lokalne**| Wynik wyjściowy do pliku lokalnego.|
|**Liczba wierszy**| Liczba wierszy danych wyjściowych do pliku lokalnego.|
|**Rozmiar danych**| Rozmiar danych danych wyjściowych do pliku lokalnego.|

### <a name="close-job-diagram"></a>Zamknij diagram zadań

Jeśli diagram zadań nie jest już potrzebny, wybierz **pozycję Zamknij** w prawym górnym rogu. Po zamknięciu okna diagramu, należy ponownie uruchomić lokalne testowanie, aby go zobaczyć.

### <a name="view-job-level-metrics-and-stop-running"></a>Wyświetlanie metryk poziomu zadania i zatrzymywania działania

Inne dane poziomu zadania są wyświetlane w konsoli podręcznej. Jeśli chcesz zatrzymać zadanie, naciśnij **klawisze Ctrl+C** w konsoli.

![Zadanie zatrzymania diagramu zadań](./media/debug-locally-using-job-diagram/stop-job.png)

## <a name="limitations"></a>Ograniczenia

* Ujarzmienia wyjściowe usługi Power BI i usługi Azure Data Lake Gen1 nie są obsługiwane z powodu ograniczeń modelu uwierzytelniania.

* Tylko opcje wprowadzania w chmurze mają obsługę [zasad czasu,](stream-analytics-out-of-order-and-late-events.md) podczas gdy lokalne opcje wprowadzania nie.

## <a name="next-steps"></a>Następne kroki

* [Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu programu Visual Studio](stream-analytics-quick-create-vs.md)
* [Use Visual Studio to view Azure Stream Analytics jobs (Wyświetlanie zadań usługi Azure Stream Analytics za pomocą programu Visual Studio)](stream-analytics-vs-tools.md)
* [Testowanie danych na żywo lokalnie przy użyciu narzędzi usługi Azure Stream Analytics dla programu Visual Studio (Wersja zapoznawcza)](stream-analytics-live-data-local-testing.md)
