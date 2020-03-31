---
title: Formaty danych obsługiwane przez Usługę Azure Data Explorer do pozyskiwania.
description: Dowiedz się więcej o różnych formatach danych i kompresji obsługiwanych przez Usługę Azure Data Explorer do pozyskiwania.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 16ecdf7ac3f0062637e4bbea86d26e2560f38222
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235302"
---
# <a name="data-formats-supported-by-azure-data-explorer-for-ingestion"></a>Formaty danych obsługiwane przez Eksploratora danych platformy Azure do pozyskiwania

Pozyskiwania danych jest procesem, w którym dane są dodawane do tabeli i jest udostępniany do kwerendy w Eksploratorze danych platformy Azure. Dla wszystkich metod pozyskiwania, inne niż pozyskiwania z kwerendy, dane muszą być w jednym z obsługiwanych formatów. W poniższej tabeli wymieniono i opisano formaty, które usługa Azure Data Explorer obsługuje w celu pozyskiwania danych.

|Format   |Wewnętrzny   |Opis|
|---------|------------|-----------|
|Avro     |`.avro`     |[Plik kontenera Avro](https://avro.apache.org/docs/current/). Obsługiwane są następujące kody: `null`, `deflate` (`snappy` obecnie nie jest obsługiwany).|
|CSV      |`.csv`      |Plik tekstowy z wartościami oddzielonymi przecinkami (`,`). Zobacz [RFC 4180: _Wspólny format i typ MIME dla plików csv (CSV) wartości oddzielonych przecinkami ._](https://www.ietf.org/rfc/rfc4180.txt)|
|JSON     |`.json`     |Plik tekstowy z obiektami JSON rozdzielanych przez `\n` lub `\r\n`. Zobacz [JSON Lines (JSONL)](http://jsonlines.org/).|
|MultiJSON ( MultiJSON )|`.multijson`|Plik tekstowy z tablicą JSON worków właściwości (każdy reprezentujący rekord) lub dowolną `\n` liczbę `\r\n`worków właściwości rozdzielonych przez odstępy lub . Każdy bagaż właściwości może być rozłożony na wielu liniach. Ten format jest `JSON`preferowany niż , chyba że dane są workami niebędącymi własnością.|
|Orc      |`.orc`      |[Plik Orka](https://en.wikipedia.org/wiki/Apache_ORC).|
|Parkiet  |`.parquet`  |[Plik parkietu](https://en.wikipedia.org/wiki/Apache_Parquet).|
|Psv      |`.psv`      |Plik tekstowy z wartościami<code>&#124;</code>oddzielonymi potokami ( ).|
|Raw      |`.raw`      |Plik tekstowy, którego cała zawartość jest wartością pojedynczego ciągu.|
|SCsv (wask)     |`.scsv`     |Plik tekstowy z wartościami oddzielonymi`;`średnikami ( ).|
|SOHsv ( SOHsv )    |`.sohsv`    |Plik tekstowy z wartościami oddzielonymi SOH. (SOH jest ascii codepoint 1; ten format jest używany przez Hive na HDInsight.)|
|Tsv      |`.tsv`      |Plik tekstowy z wartościami`\t`oddzielonymi tabulatorami ( ).|
|TSVE (właso)     |`.tsv`      |Plik tekstowy z wartościami`\t`oddzielonymi tabulatorami ( ). Do ucieczki służy`\`znak ukośnika odwrotnego ( ).|
|TXT      |`.txt`      |Plik tekstowy z liniami `\n`rozdzielanych przez plik . Puste wiersze są pomijane.|

## <a name="supported-data-compression-formats"></a>Obsługiwane formaty kompresji danych

Obiekty BLOB i pliki można skompresować za pomocą dowolnego z następujących algorytmów kompresji:

|Kompresja|Wewnętrzny|
|-----------|---------|
|Gzip       |.gz      |
|Zip        |zip     |

Wskaż kompresję, dołączając rozszerzenie do nazwy obiektu blob lub pliku.

Przykład:
* `MyData.csv.zip`wskazuje obiekt blob lub plik sformatowany jako CSV, skompresowany za pomocą zipu (archiwum lub pojedynczy plik)
* `MyData.csv.gz`wskazuje obiekt blob lub plik sformatowany jako CSV, skompresowany za pomocą GZip

Obsługiwane są również nazwy obiektów blob lub plików, które nie zawierają rozszerzeń formatu, ale tylko kompresja (na przykład) . W takim przypadku format pliku musi być określony jako właściwość pozyskiwania, ponieważ nie można wywnioskować.

> [!NOTE]
> Niektóre formaty kompresji śledzą oryginalne rozszerzenie pliku jako część skompresowanego strumienia. To rozszerzenie jest zazwyczaj ignorowane do określania formatu pliku. Jeśli formatu pliku nie można określić na podstawie (skompresowanego) obiektu `format` blob lub nazwy pliku, należy go określić za pomocą właściwości pozyskiwania.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [pozyskiwania danych](/azure/data-explorer/ingest-data-overview)
* Dowiedz się więcej o [właściwościach pozyskiwania danych usługi Azure Data Explorer](ingestion-properties.md)
