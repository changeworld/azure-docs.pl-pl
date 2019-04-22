---
title: Zrozumieć poziom zgodności dla zadań usługi Azure Stream Analytics
description: Dowiedz się, jak ustawić poziom zgodności dla zadania usługi Azure Stream Analytics i istotne zmiany w najnowszej poziom zgodności
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: b5c833798f8533e7c6fbe3595a726ac6ce56e2d2
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682818"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Poziom zgodności dla zadań usługi Azure Stream Analytics

W tym artykule opisano opcje poziomu zgodności w usłudze Azure Stream Analytics. Stream Analytics to zarządzana usługa, za pomocą funkcji regularnych aktualizacji i ulepszenia wydajności. Większość aktualizacji środowiska uruchomieniowe service są automatycznie udostępniane użytkownikom końcowym. 

Jednak niektóre nowe funkcje w usłudze może stanowić istotne zmiany, takie jak zmiany w zachowaniu istniejącego zadania, lub zmiana danych sposób jest używany w uruchomionych zadań. Możesz zachować swoje istniejące zadania usługi Stream Analytics pracę bez najważniejszych zmian, pozostawiając ustawienie obniżony poziom zgodności. Gdy wszystko jest gotowe, aby uzyskać najnowsze zachowanie w czasie wykonywania, użytkownik może zoptymalizowany pod kątem w poprzez podnoszenie poziomu zgodności. 

## <a name="choose-a-compatibility-level"></a>Wybierz poziom zgodności

Poziom zgodności steruje zachowaniem czasu wykonywania zadania usługi stream analytics. 

Usługa Azure Stream Analytics obsługuje obecnie trzy poziomy zgodności:

* 1.0 — poziom domyślny
* 1.1 — bieżące zachowanie wersji
* 1.2 (wersja zapoznawcza) — najnowsza zachowania przy użyciu najnowszych ulepszeń w wersji ewaluacyjnej

Oryginalny 1.0 poziom zgodności wprowadzono ogólnie dostępnej wersji usługi Azure Stream Analytics kilka lat temu.

Podczas tworzenia nowego zadania usługi Stream Analytics jest najlepszym rozwiązaniem, aby je utworzyć za pomocą najnowszych poziom zgodności. Uruchom projekt zadania opierająca się na najnowszej zachowania, tak, aby uniknąć zmiany dodane i później.

## <a name="set-the-compatibility-level"></a>Ustaw poziom zgodności

Można ustawić poziom zgodności dla zadania usługi Stream Analytics w witrynie Azure portal lub za pomocą [wywołanie interfejsu API REST zadania tworzenia](/rest/api/streamanalytics/stream-analytics-job).

Aby zaktualizować poziom zgodności zadania w witrynie Azure portal:

1. Użyj [witryny Azure portal](https://portal.azure.com) zlokalizować do zadania usługi Stream Analytics.
2. **Zatrzymaj** zadania przed zaktualizowaniem poziom zgodności. Nie można zaktualizować poziom zgodności, jeśli zadanie jest w stanie uruchomienia.
3. W obszarze **Konfiguruj** nagłówka, wybierz **poziom zgodności**.
4. Wybierz żądaną wartość poziomu zgodności.
5. Wybierz **Zapisz** w dolnej części strony.

![Stream Analytics poziom zgodności w witrynie Azure portal](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

Po zaktualizowaniu poziom zgodności kompilatora języka T-SQL sprawdza poprawność zadania za pomocą składni, który odpowiada poziomowi zgodności wybranego.

## <a name="compatibility-level-12"></a>Poziom zgodności 1.2

Następujące istotne zmiany są wprowadzane przy poziomie zgodności 1.2:

### <a name="geospatial-functions"></a>Funkcje geoprzestrzenne

**Poprzednie poziomy:** Usługa Azure Stream Analytics używane obliczeń lokalizacji geograficznej.

**1,2 poziom:** Usługa Azure Stream Analytics umożliwia współrzędnych geograficznych przewidywane geometryczne obliczeń. Nie ma zmian w sygnaturze funkcji geoprzestrzennych. Jednak ich semantyka jest nieco inny, umożliwiając dokładniejszą obliczeń niż przed.

Usługa Azure Stream Analytics obsługuje indeksowanie danych odwołania geoprzestrzenne. Dane referencyjne zawierający elementy dane geograficzne mogą być indeksowane szybciej obliczeń sprzężenia.

Funkcje geoprzestrzenne zaktualizowane Przenieś wyrazistość pełny format danych geoprzestrzennych popularnym tekstu (WELL-KNOWN). Można określić inne składniki geoprzestrzennych, które nie były wcześniej obsługiwane GeoJson.

Aby uzyskać więcej informacji, zobacz [aktualizacje funkcji geoprzestrzennych w usłudze Azure Stream Analytics — chmury i usługi IoT Edge](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/).

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Równoległego wykonywania zapytań do źródeł danych wejściowych z różnych partycji

**Poprzednie poziomy:** Zapytania usługi Azure Stream Analytics wymaga użycia klauzuli PARTITION BY równoległe przetwarzanie przetwarzanie zapytań między partycjami źródła danych wejściowych.

**1,2 poziom:** Jeśli logiki zapytania może odbywać się równolegle w partycjach źródła danych wejściowych, Azure Stream Analytics tworzy wystąpienia oddzielnego zapytania i uruchamia obliczenia równoległe.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>Natywna integracja interfejsu API zbiorcze z danymi wyjściowymi bazy danych cosmos DB

**Poprzednie poziomy:** Zachowanie upsert został *wstawiania lub scalić*.

**1,2 poziom:** Natywna integracja interfejsu API zbiorcze z danymi wyjściowymi bazy danych cosmos DB pozwala zwiększyć przepływność i skutecznie obsługuje ograniczanie żądań.

Zachowanie upsert jest *wstawić lub Zastąp*.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset podczas zapisywania danych wyjściowych SQL

**Poprzednie poziomy:** [DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) typy zostały dostosowane na czas UTC.

**1,2 poziom:** DateTimeOffset już nie jest dostosowywany.

### <a name="strict-validation-of-prefix-of-functions"></a>Ścisła Weryfikacja prefiks funkcji

**Poprzednie poziomy:** Wystąpił nie ścisła weryfikacja funkcji prefiksy.

**1,2 poziom:** Usługa Azure Stream Analytics jest ścisła weryfikacja funkcji prefiksów. Dodawanie prefiksu do wbudowanej funkcji spowoduje wystąpienie błędu. Na przykład`myprefix.ABS(…)` nie jest obsługiwane.

Dodawanie prefiksu do agregacje wbudowane również powoduje błąd. Na przykład `myprefix.SUM(…)` nie jest obsługiwane.

Za pomocą prefiks "system" dla dowolnej funkcji zdefiniowanych przez użytkownika spowoduje błąd.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Nie zezwalaj na tablicy i obiektów jako właściwości klucza karty danych wyjściowych Cosmos DB

**Poprzednie poziomy:** Typy tablicy i obiektów były obsługiwane jako właściwość klucza.

**1,2 poziom:** Typy tablicy i obiektów nie są już obsługiwane jako właściwość klucza.

## <a name="compatibility-level-11"></a>Poziom zgodności 1.1

Następujące istotne zmiany są wprowadzane przy poziomie zgodności 1.1:

### <a name="service-bus-xml-format"></a>Format XML magistrali usług

**poziom 1.0:** Usługa Azure Stream Analytics używane DataContractSerializer, dlatego zawartość komunikatu tagów XML. Na przykład:

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**poziom 1.1:** Treść wiadomości zawiera strumienia bezpośrednio z nie dodatkowe tagi. Na przykład: `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>Utrwalanie uwzględnianie wielkości liter dla nazw pól

**poziom 1.0:** Nazwy pól zostały zamienione na małe litery, gdy jest przetwarzane przez aparat usługi Azure Stream Analytics.

**poziom 1.1:** uwzględnianie wielkości liter są utrwalane dotyczące nazw pól, gdy są przetwarzane przez aparat usługi Azure Stream Analytics.

> [!NOTE]
> Utrwalanie uwzględnianie wielkości liter nie jest jeszcze dostępna dla zadania Stream Analytics hostowany przy użyciu środowiska krawędzi. W rezultacie wszystkie nazwy pól są konwertowane na małe litery, gdy zadanie jest obsługiwana na urządzeniach brzegowych.

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**poziom 1.0:** Polecenia CREATE TABLE nie filtrowanie zdarzeń z NaN (Not a Number. Na przykład w nieskończoność, - nieskończoność) w kolumnie FLOAT typu, ponieważ są one poza zakresem udokumentowane w przypadku tych liczb.

**poziom 1.1:** CREATE TABLE umożliwia określenie silne schematu. Aparat usługi Stream Analytics weryfikuje, że danych odpowiada tym schemacie. W tym modelu polecenia można filtrować zdarzenia przy użyciu wartości NaN.

### <a name="disable-automatic-upcast-for-datetime-strings-in-json"></a>Wyłącz automatyczne rozszerzające dla ciągów daty i godziny w formacie JSON

**poziom 1.0:** Analizator składni JSON będzie automatycznie upcast — ciągi informacjami Data/Godzina/strefa na typ DateTime, a następnie przekonwertować go na czas UTC. To zachowanie spowodowało utraty informacji o strefie czasowej.

**poziom 1.1:** Nie ma już automatycznie upcast — wartości ciągu daty/godziny/strefy informacje umożliwiające typu DateTime. W rezultacie są przechowywane informacje dotyczące strefy czasowej.

## <a name="next-steps"></a>Kolejne kroki

* [Rozwiązywanie problemów z danych wejściowych usługi Azure Stream Analytics](stream-analytics-troubleshoot-input.md)
* [Stream Analytics Resource health](stream-analytics-resource-health.md)
