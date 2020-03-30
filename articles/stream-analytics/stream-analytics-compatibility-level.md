---
title: Poziomy zgodności usługi Azure Stream Analytics
description: Dowiedz się, jak ustawić poziom zgodności zadania usługi Azure Stream Analytics i główne zmiany na najnowszym poziomie zgodności
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 8f22b1ff97826dc318794aca58973b1276e74209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087855"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Poziom zgodności dla zadań usługi Azure Stream Analytics

W tym artykule opisano opcję poziomu zgodności w usłudze Azure Stream Analytics. Stream Analytics to usługa zarządzana, z regularnymi aktualizacjami funkcji i ulepszeniami wydajności. Większość aktualizacji środowiska uruchomieniowego usługi są automatycznie udostępniane użytkownikom końcowym. 

Jednak niektóre nowe funkcje w usłudze może wprowadzić istotne zmiany, takie jak zmiana w zachowaniu istniejącego zadania lub zmiany w sposobie zużywania danych w uruchomionych zadaniach. Istniejące zadania usługi Stream Analytics mogą być uruchomione bez większych zmian, pozostawiając obniżone ustawienie poziomu zgodności. Gdy będziesz gotowy na najnowsze zachowania środowiska uruchomieniowego, możesz wyrazić zgodę, podnosząc poziom zgodności. 

## <a name="choose-a-compatibility-level"></a>Wybieranie poziomu zgodności

Poziom zgodności kontroluje zachowanie środowiska uruchomieniowego zadania analizy strumienia. 

Usługa Azure Stream Analytics obsługuje obecnie trzy poziomy zgodności:

* 1.0 — oryginalny poziom zgodności, wprowadzony podczas ogólnej dostępności usługi Azure Stream Analytics kilka lat temu.
* 1.1 - Poprzednie zachowanie
* 1.2 - Najnowsze zachowanie z najnowszymi ulepszeniami

Podczas tworzenia nowego zadania usługi Stream Analytics, jest najlepszym rozwiązaniem, aby utworzyć go przy użyciu najnowszego poziomu zgodności. Rozpocznij projekt zadania, opierając się na najnowszych zachowaniach, aby uniknąć dodatkowych zmian i złożoności później.

## <a name="set-the-compatibility-level"></a>Ustawianie poziomu zgodności

Można ustawić poziom zgodności zadania usługi Stream Analytics w witrynie Azure portal lub przy użyciu [wywołania interfejsu API REST zadania.](/rest/api/streamanalytics/stream-analytics-job)

Aby zaktualizować poziom zgodności zadania w witrynie Azure portal:

1. Użyj [witryny Azure Portal,](https://portal.azure.com) aby znaleźć zadanie usługi Stream Analytics.
2. **Zatrzymaj** zadanie przed zaktualizowaniem poziomu zgodności. Nie można zaktualizować poziomu zgodności, jeśli zadanie jest w stanie uruchomionym.
3. W nagłówku **Konfiguruj** wybierz **pozycję Poziom zgodności**.
4. Wybierz odpowiednią wartość poziomu zgodności.
5. Wybierz **pozycję Zapisz** u dołu strony.

![Poziom zgodności usługi Stream Analytics w witrynie Azure portal](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

Podczas aktualizowania poziomu zgodności kompilator T sprawdza poprawność zadania za pomocą składni odpowiadającej wybranemu poziomowi zgodności.

## <a name="compatibility-level-12"></a>Poziom zgodności 1.2

Następujące istotne zmiany są wprowadzane w poziomie zgodności 1.2:

###  <a name="amqp-messaging-protocol"></a>Protokół komunikacji AMQP

**Poziom 1.2:** Usługa Azure Stream Analytics używa protokołu obsługi protokołu komunikacji [protokołu AMQP (Advanced Message Queueing Protocol)](../service-bus-messaging/service-bus-amqp-overview.md) do zapisywania w kolejkach i tematach usługi Service Bus. Protokół AMQP umożliwia tworzenie wieloplatformowych, hybrydowych aplikacji przy użyciu otwartego standardowego protokołu.

### <a name="geospatial-functions"></a>Funkcje geoprzestrzenne

**Poprzednie poziomy:** Usługa Azure Stream Analytics korzystała z obliczeń geografii.

**1.2 poziom:** Usługa Azure Stream Analytics umożliwia obliczenie współrzędnych geograficznych rzutowania geograficznego geometrycznego. Nie ma zmian w podpisie funkcji geoprzestrzennych. Jednak ich semantyka jest nieco inna, co pozwala na bardziej precyzyjne obliczenia niż wcześniej.

Usługa Azure Stream Analytics obsługuje geoprzestrzenne indeksowanie danych referencyjnych. Dane referencyjne zawierające elementy geoprzestrzenne mogą być indeksowane w celu szybszego obliczania sprzężenia.

Zaktualizowane funkcje geoprzestrzenne przynoszą pełną ekspresję formatu geoprzestrzennego dobrze znanego tekstu (WKT). Można określić inne składniki geoprzestrzenne, które nie były wcześniej obsługiwane przez geojson.

Aby uzyskać więcej informacji, zobacz [Aktualizacje funkcji geoprzestrzennych w usłudze Azure Stream Analytics — Chmura i usługa IoT Edge.](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/)

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Równoległe wykonywanie kwerend dla źródeł wejściowych z wieloma partycjami

**Poprzednie poziomy:** Kwerendy usługi Azure Stream Analytics wymagane użycie klauzuli PARTITION BY do równoległości przetwarzania zapytań między wejściowymi partycjami źródłowymi.

**1.2 poziom:** Jeśli logika kwerendy może być równoległa między partycjami źródłowymi wejściowymi, usługa Azure Stream Analytics tworzy oddzielne wystąpienia zapytań i uruchamia obliczenia równolegle.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>Natywna zbiorcza integracja interfejsu API z danymi wyjściowymi usługi CosmosDB

**Poprzednie poziomy:** Zachowanie upsert było *wstawić lub scalić*.

**1.2 poziom:** Natywna zbiorcza integracja interfejsu API z wyjściem usługi CosmosDB maksymalizuje przepływność i wydajnie obsługuje żądania ograniczania przepustowości. Aby uzyskać więcej informacji, zobacz [dane wyjściowe usługi Azure Stream Analytics na stronie usługi Azure Cosmos DB.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-documentdb-output#improved-throughput-with-compatibility-level-12)

Zachowanie upsert to *wstaw lub zastąp*.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset podczas zapisywania do danych wyjściowych SQL

**Poprzednie poziomy:** [Typy DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) zostały dostosowane do czasu UTC.

**1.2 poziom:** DateTimeOffset nie jest już korygowany.

### <a name="long-when-writing-to-sql-output"></a>Długie zapisywanie na wyjściu SQL

**Poprzednie poziomy:** Wartości zostały obcięty na podstawie typu docelowego.

**1.2 poziom:** Wartości, które nie pasują do typu docelowego są obsługiwane zgodnie z zasadami błędu wyjściowego.

### <a name="record-and-array-serialization-when-writing-to-sql-output"></a>Serializacja rekordów i tablic podczas zapisywania na wyjściu SQL

**Poprzednie poziomy:** Rekordy zostały napisane jako "Record", a tablice zostały napisane jako "Array".

**1.2 poziom:** Rekordy i tablice są serializowane w formacie JSON.

### <a name="strict-validation-of-prefix-of-functions"></a>Ścisła walidacja prefiksu funkcji

**Poprzednie poziomy:** Nie było ścisłego sprawdzania poprawności prefiksów funkcji.

**1.2 poziom:** Usługa Azure Stream Analytics ma ścisłe sprawdzanie poprawności prefiksów funkcji. Dodanie prefiksu do wbudowanej funkcji powoduje błąd. Na przykład`myprefix.ABS(…)` nie jest obsługiwany.

Dodanie prefiksu do wbudowanych agregatów powoduje również błąd. Na przykład `myprefix.SUM(…)` nie jest obsługiwany.

Użycie prefiksu "system" dla wszystkich funkcji zdefiniowanych przez użytkownika powoduje błąd.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Nie zezwalaj na tablicę i obiekt jako kluczowe właściwości karty wyjściowej usługi Cosmos DB

**Poprzednie poziomy:** Typy tablic i obiektów były obsługiwane jako właściwość klucza.

**1.2 poziom:** Typy tablic i obiektów nie są już obsługiwane jako właściwość klucza.

## <a name="compatibility-level-11"></a>Poziom zgodności 1.1

Następujące istotne zmiany są wprowadzane w poziomie zgodności 1.1:

### <a name="service-bus-xml-format"></a>Format XML usługi Service Bus

**1,0 poziom:** Usługa Azure Stream Analytics używane DataContractSerializer, więc zawartość wiadomości zawarte tagi XML. Przykład:

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**1.1 poziom:** Zawartość wiadomości zawiera strumień bezpośrednio bez dodatkowych tagów. Na przykład: `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>Utrzymująca się wielkość liter dla nazw pól

**1,0 poziom:** Nazwy pól zostały zmienione na małe litery podczas przetwarzania przez aparat usługi Azure Stream Analytics.

**Poziom 1.1:** wielkość liter jest zachowywana dla nazw pól, gdy są przetwarzane przez aparat usługi Azure Stream Analytics.

> [!NOTE]
> Utrzymująca się czułość wielkości liter nie jest jeszcze dostępna dla zadań analitycznych strumienia obsługiwanych przy użyciu środowiska Edge. W rezultacie wszystkie nazwy pól są konwertowane na małe litery, jeśli zadanie jest hostowane na krawędzi.

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationWydaj

**1,0 poziom:** Polecenie UTWÓRZ TABELĘ nie filtruje zdarzeń za pomocą nan (Not-a-Number). Na przykład Infinity, -Infinity) w typie kolumny FLOAT, ponieważ są one poza udokumentowanym zakresem dla tych liczb.

**1.1 poziom:** CREATE TABLE umożliwia określenie silnego schematu. Aparat usługi Stream Analytics sprawdza, czy dane są zgodne z tym schematem. W tym modelu polecenie może filtrować zdarzenia za pomocą wartości NaN.

### <a name="disable-automatic-upcast-for-datetime-strings-in-json"></a>Wyłącz automatyczne emisje upcast dla ciągów datetime w JSON

**1,0 poziom:** Analizator JSON automatycznie przekształca wartości ciągu z informacjami o dacie/czasie/strefie na typ DateTime, a następnie konwertuje je na wartość UTC. To zachowanie spowodowało utratę informacji o strefie czasowej.

**1.1 poziom:** Nie ma już automatycznie upcast wartości ciągu z datą /czas / strefa informacji do typu DateTime. W rezultacie informacje o strefie czasowej są przechowywane.

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z danymi wejściowymi usługi Azure Stream Analytics](stream-analytics-troubleshoot-input.md)
* [Kondycja zasobów usługi Analizy strumieniowej](stream-analytics-resource-health.md)
