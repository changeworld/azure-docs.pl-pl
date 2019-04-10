---
title: Zrozumieć poziom zgodności dla zadań usługi Azure Stream Analytics
description: Dowiedz się, jak ustawić poziom zgodności dla zadania usługi Azure Stream Analytics i istotne zmiany w najnowszej poziom zgodności
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: 6fb93152263d253de983b17d25f02f4c68a172fd
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361390"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Poziom zgodności dla zadań usługi Azure Stream Analytics
 
Poziom zgodności odnosi się do zachowania specyficzne dla wersji usługi Azure Stream Analytics. Usługa Azure Stream Analytics to zarządzana usługa, za pomocą funkcji regularnych aktualizacji i ulepszenia wydajności. Zazwyczaj aktualizacje są automatycznie udostępniane użytkownikom końcowym. Jednak niektóre nowe funkcje mogą wprowadzać znaczące zmiany takich jak zmiany w zachowaniu istniejącego zadania, zmiany w procesach korzystanie z danych z tych zadań itd. Poziom zgodności jest używana do reprezentowania istotne zmiany wprowadzone w usłudze Stream Analytics. Zawsze wprowadzono znaczące zmiany dzięki nowemu poziomowi zgodności. 

Poziom zgodności zapewnia, że istniejące zadania są uruchamiane bez żadnych błędów. Podczas tworzenia nowego zadania usługi Stream Analytics jest najlepszym rozwiązaniem, aby je utworzyć za pomocą najnowszych poziom zgodności. 
 
## <a name="set-a-compatibility-level"></a>Ustaw poziom zgodności 

Poziom zgodności steruje zachowaniem czasu wykonywania zadania usługi stream analytics. Można ustawić poziom zgodności dla zadania usługi Stream Analytics, za pomocą portalu lub przy użyciu [wywołanie interfejsu API REST zadania tworzenia](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). Usługa Azure Stream Analytics obsługuje obecnie dwie zgodności poziomów — "1.0" i "1.1". Domyślnie poziom zgodności jest równa "1.0", który został wprowadzony w ogólnie dostępnej wersji usługi Azure Stream Analytics. Aby zaktualizować wartość domyślną, przejdź do istniejącego zadania usługi Stream Analytics > Wybierz **poziom zgodności** opcji **Konfiguruj** sekcji, a następnie zmień wartość. 

Upewnij się, Zatrzymaj zadanie przed zaktualizowaniem poziom zgodności. Nie można zaktualizować poziom zgodności, jeśli zadanie jest w stanie uruchomienia. 

![Stream Analytics poziom zgodności w witrynie Azure portal](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

 
Po zaktualizowaniu poziom zgodności kompilatora języka T-SQL sprawdza poprawność zadania za pomocą składni, który odpowiada poziomowi zgodności wybranego. 

## <a name="major-changes-in-the-latest-compatibility-level-12"></a>Istotne zmiany w najnowszej poziom zgodności (1.2)

Następujące istotne zmiany są wprowadzane przy poziomie zgodności 1.2:

### <a name="geospatial-functions"></a>Funkcje geoprzestrzenne 

**Poprzednie wersje:** Usługa Azure Stream Analytics używane obliczeń lokalizacji geograficznej.

**Bieżąca wersja:** Usługa Azure Stream Analytics umożliwia współrzędnych geograficznych przewidywane geometryczne obliczeń. Nie ma zmian w sygnaturze funkcji geoprzestrzennych. Jednak ich semantyka jest nieco inny, umożliwiając dokładniejszą obliczeń niż przed.

Usługa Azure Stream Analytics obsługuje indeksowanie danych odwołania geoprzestrzenne. Dane referencyjne zawierający elementy dane geograficzne mogą być indeksowane szybciej obliczeń sprzężenia.

Funkcje geoprzestrzenne zaktualizowane Przenieś wyrazistość pełny format danych geoprzestrzennych popularnym tekstu (WELL-KNOWN). Można określić inne składniki geoprzestrzennych, które nie były wcześniej obsługiwane GeoJson.

Aby uzyskać więcej informacji, zobacz [aktualizacje funkcji geoprzestrzennych w usłudze Azure Stream Analytics — chmury i usługi IoT Edge](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/).

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Równoległego wykonywania zapytań do źródeł danych wejściowych z różnych partycji 

**Poprzednie wersje:** Zapytania usługi Azure Stream Analytics wymaga użycia klauzuli PARTITION BY równoległe przetwarzanie przetwarzanie zapytań między partycjami źródła danych wejściowych.

**Bieżąca wersja:** Jeśli logiki zapytania może odbywać się równolegle w partycjach źródła danych wejściowych, Azure Stream Analytics tworzy wystąpienia oddzielnego zapytania i uruchamia obliczenia równoległe.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>Natywna integracja interfejsu API zbiorcze z danymi wyjściowymi bazy danych cosmos DB

**Poprzednie wersje:** Zachowanie upsert został *wstawiania lub scalić*.

**Bieżąca wersja:** Natywna integracja interfejsu API zbiorcze z danymi wyjściowymi bazy danych cosmos DB pozwala zwiększyć przepływność i skutecznie obsługuje ograniczanie żądań.

Zachowanie upsert jest *wstawić lub Zastąp*.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset podczas zapisywania danych wyjściowych SQL

**Poprzednie wersje:** [DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) typy zostały dostosowane na czas UTC.

**Bieżąca wersja:** DateTimeOffset już nie jest dostosowywany.

### <a name="strict-validation-of-prefix-of-functions"></a>Ścisła Weryfikacja prefiks funkcji

**Poprzednie wersje:** Wystąpił nie ścisła weryfikacja funkcji prefiksy.

**Bieżąca wersja:** Usługa Azure Stream Analytics jest ścisła weryfikacja funkcji prefiksów. Dodawanie prefiksu do wbudowanej funkcji spowoduje wystąpienie błędu. Na przykład`myprefix.ABS(…)` nie jest obsługiwane.

Dodawanie prefiksu do agregacje wbudowane również powoduje błąd. Na przykład `myprefix.SUM(…)` nie jest obsługiwane.

Za pomocą prefiks "system" dla dowolnej funkcji zdefiniowanych przez użytkownika spowoduje błąd.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Nie zezwalaj na tablicy i obiektów jako właściwości klucza karty danych wyjściowych Cosmos DB

**Poprzednie wersje:** Typy tablicy i obiektów były obsługiwane jako właściwość klucza.

**Bieżąca wersja:** Typy tablicy i obiektów nie są już obsługiwane jako właściwość klucza.


## <a name="next-steps"></a>Kolejne kroki
* [Rozwiązywanie problemów z danych wejściowych usługi Azure Stream Analytics](stream-analytics-troubleshoot-input.md)
* [Stream Analytics Resource health](stream-analytics-resource-health.md)
