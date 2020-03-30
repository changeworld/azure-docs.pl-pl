---
title: Tworzenie zestawów danych w fabryce danych platformy Azure
description: Dowiedz się, jak tworzyć zestawy danych w usłudze Azure Data Factory, korzystając z przykładów korzystających z właściwości, takich jak przesunięcie i anchorDateTime.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 18a5e11d2341fb020fc442d2f9ce7c1d44de9d0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260438"
---
# <a name="datasets-in-azure-data-factory"></a>Zestawy danych w usłudze Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](data-factory-create-datasets.md)
> * [Wersja 2 (bieżąca wersja)](../concepts-datasets-linked-services.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Zestawy danych w wersji 2](../concepts-datasets-linked-services.md).

W tym artykule opisano, jakie są zestawy danych, jak są definiowane w formacie JSON i jak są używane w potokach usługi Azure Data Factory. Zawiera szczegółowe informacje o każdej sekcji (na przykład struktury, dostępności i zasad) w definicji JSON zestawu danych. W tym artykule podano również przykłady użycia **właściwości przesunięcia,** **anchorDateTime**i **stylu** w definicji JSON zestawu danych.

> [!NOTE]
> Jeśli jesteś nowy w fabryce danych, zobacz [Wprowadzenie do usługi Azure Data Factory](data-factory-introduction.md) dla przeglądu. Jeśli nie masz praktycznego doświadczenia z tworzeniem fabryk danych, możesz lepiej zrozumieć, czytając [samouczek transformacji danych](data-factory-build-your-first-pipeline.md) i [samouczek przenoszenia danych](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="overview"></a>Omówienie
Fabryka danych może obejmować jeden lub wiele potoków. **Potok** jest logiczne grupowanie **działań,** które razem wykonać zadanie. Działania w potoku definiują akcje do wykonania na danych. Na przykład można użyć działania kopiowania do kopiowania danych z lokalnego programu SQL Server do magazynu obiektów Blob platformy Azure. Następnie można użyć działania gałęzi, który uruchamia skrypt hive w klastrze usługi Azure HDInsight do przetwarzania danych z magazynu obiektów Blob do tworzenia danych wyjściowych. Na koniec można użyć drugiego działania kopiowania, aby skopiować dane wyjściowe do usługi Azure SQL Data Warehouse, na którym są tworzone rozwiązania raportowania analizy biznesowej (BI). Aby uzyskać więcej informacji na temat potoków i działań, zobacz [Potoki i działania w usłudze Azure Data Factory](data-factory-create-pipelines.md).

Działanie może trwać zero lub więcej wejściowych **zestawów danych**i tworzyć jeden lub więcej wyjściowych zestawów danych. Wejściowy zestaw danych reprezentuje dane wejściowe dla działania w potoku, a wyjściowy zestaw danych reprezentuje dane wyjściowe dla działania. Zestawy danych identyfikują dane w różnych magazynach danych, takich jak tabele, pliki, foldery i dokumenty. Na przykład zestaw danych obiektów Blob platformy Azure określa kontener obiektu blob i folder w magazynie obiektów Blob, z którego potok powinien odczytywać dane.

Przed utworzeniem zestawu danych utwórz **połączony serwis,** aby połączyć magazyn danych z fabryką danych. Połączone usługi działają podobnie do parametrów połączenia, umożliwiając definiowanie informacji wymaganych przez usługę Data Factory do nawiązywania połączeń z zasobami zewnętrznymi. Zestawy danych identyfikują dane w połączonych magazynach danych, takie jak tabele SQL, pliki, foldery i dokumenty. Na przykład usługa połączona usługi Azure Storage łączy konto magazynu z fabryką danych. Zestaw danych obiektów blob platformy Azure reprezentuje kontener obiektów blob i folder, który zawiera wejściowe obiekty blob do przetworzenia.

Oto przykładowy scenariusz. Aby skopiować dane z magazynu obiektów Blob do bazy danych SQL, należy utworzyć dwie połączone usługi: Usługi Azure Storage i Azure SQL Database. Następnie utwórz dwa zestawy danych: zestaw danych obiektów Blob platformy Azure (który odwołuje się do usługi połączonej usługi Azure Storage) i zestaw danych tabeli SQL platformy Azure (który odwołuje się do połączonej usługi Azure SQL Database). Usługi połączone w usłudze Azure Storage i Azure SQL Database zawierają parametry połączenia używane przez usługę Data Factory w czasie wykonywania do łączenia się odpowiednio z usługą Azure Storage i azure SQL Database. Zestaw danych obiektów Blob platformy Azure określa kontener obiektów blob i folder obiektów blob, który zawiera wejściowe obiekty blob w magazynie obiektów Blob. Zestaw danych tabeli SQL platformy Azure określa tabelę SQL w bazie danych SQL, do której mają być kopiowane dane.

Na poniższym diagramie przedstawiono relacje między potokiem, aktywnością, zestawem danych i usługą połączony w usłudze Data Factory:

![Relacja między rurociągiem, aktywnością, zestawem danych, połączonymi usługami](media/data-factory-create-datasets/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>Zestaw danych JSON
Zestaw danych w układzie danych jest zdefiniowany w formacie JSON w następujący sposób:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": "<boolean flag to indicate external data. only for input datasets>",
        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        },
        "availability": {
            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
        },
        "policy":
        {
        }
    }
}
```

W poniższej tabeli opisano właściwości w powyższym JSON:

| Właściwość | Opis | Wymagany | Domyślne |
| --- | --- | --- | --- |
| name |Nazwa zestawu danych. Zobacz [Usługi Azure Data Factory — reguły nazewnictwa](data-factory-naming-rules.md) dla reguł nazewnictwa. |Tak |Nie dotyczy |
| type |Typ zestawu danych. Określ jeden z typów obsługiwanych przez usługę Data Factory (na przykład: AzureBlob, AzureSqlTable). <br/><br/>Aby uzyskać szczegółowe informacje, zobacz [Typ zestawu danych](#Type). |Tak |Nie dotyczy |
| — struktura |Schemat zestawu danych.<br/><br/>Aby uzyskać szczegółowe informacje, zobacz [Struktura zestawu danych](#Structure). |Nie |Nie dotyczy |
| typeProperties | Właściwości typu są różne dla każdego typu (na przykład: Azure Blob, tabela SQL platformy Azure). Aby uzyskać szczegółowe informacje na temat obsługiwanych typów i ich właściwości, zobacz [Typ zestawu danych](#Type). |Tak |Nie dotyczy |
| external | Flaga logiczna, aby określić, czy zestaw danych jest jawnie produkowany przez potok fabryki danych, czy nie. Jeśli wejściowy zestaw danych dla działania nie jest produkowany przez bieżący potok, ustaw tę flagę na true. Ustaw tę flagę na true dla wejściowego zestawu danych pierwszego działania w potoku.  |Nie |false |
| availability | Definiuje okno przetwarzania (na przykład co godzinę lub codziennie) lub model krojenia dla produkcji zestawu danych. Każda jednostka danych zużytych i wyprodukowanych przez przebieg działania jest nazywana plasterkiem danych. Jeśli dostępność wyjściowego zestawu danych jest ustawiona na dzienną (częstotliwość - dzień, interwał - 1), plasterek jest produkowany codziennie. <br/><br/>Aby uzyskać szczegółowe informacje, zobacz Dostępność zestawu danych. <br/><br/>Aby uzyskać szczegółowe informacje na temat modelu krojenia zestawu danych, zobacz [harmonogram i wykonanie](data-factory-scheduling-and-execution.md) artykułu. |Tak |Nie dotyczy |
| policy |Definiuje kryteria lub warunek, który muszą spełniać wycinki zestawu danych. <br/><br/>Aby uzyskać szczegółowe informacje, zobacz sekcję [zasad zestawu danych.](#Policy) |Nie |Nie dotyczy |

## <a name="dataset-example"></a>Przykład zestawu danych
W poniższym przykładzie zestaw danych reprezentuje tabelę o nazwie **MyTable** w bazie danych SQL.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties":
        {
            "tableName": "MyTable"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Pamiętaj o następujących kwestiach:

* **jest** ustawiona na AzureSqlTable.
* **właściwość typu tableName** (specyficzne dla typu AzureSqlTable) jest ustawiona na MyTable.
* **linkedServiceName** odnosi się do połączonej usługi typu AzureSqlDatabase, która jest zdefiniowana w następnym urywek JSON.
* **częstotliwość dostępności** jest ustawiona na Dzień, a **interwał** jest ustawiony na 1. Oznacza to, że plasterek zestawu danych jest produkowany codziennie.

**Usługa AzureSqlLinkedService** jest zdefiniowana w następujący sposób:

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
        }
    }
}
```

W poprzednim urywek JSON:

* **jest** ustawiona na AzureSqlDatabase.
* **Właściwość typu connectionString** określa informacje, które mają być łączonych z bazą danych SQL.

Jak widać, usługa połączona definiuje sposób łączenia się z bazą danych SQL. Zestaw danych definiuje, jaka tabela jest używana jako dane wejściowe i wyjściowe dla działania w potoku.

> [!IMPORTANT]
> Jeśli zestaw danych nie jest produkowany przez potok, powinien być oznaczony jako **zewnętrzny**. To ustawienie zazwyczaj ma zastosowanie do danych wejściowych pierwszego działania w potoku.

## <a name="dataset-type"></a><a name="Type"></a>Typ zestawu danych
Typ zestawu danych zależy od używanego magazynu danych. Zobacz poniższą tabelę, aby uzyskać listę magazynów danych obsługiwanych przez fabrykę danych. Kliknij magazyn danych, aby dowiedzieć się, jak utworzyć połączony serwis i zestaw danych dla tego magazynu danych.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Magazyny danych z * może być lokalnie lub na infrastrukturze platformy Azure jako usługi (IaaS). Te magazyny danych wymagają [zainstalowania bramy zarządzania danymi](data-factory-data-management-gateway.md).

W przykładzie w poprzedniej sekcji typ zestawu danych jest ustawiony na **AzureSqlTable**. Podobnie dla zestawu danych obiektów blob platformy Azure typ zestawu danych jest ustawiony na **AzureBlob**, jak pokazano w następującym JSON:

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

## <a name="dataset-structure"></a><a name="Structure"></a>Struktura zestawu danych
Sekcja **konstrukcji** jest opcjonalna. Definiuje schemat zestawu danych, zawierając kolekcję nazw i typów danych kolumn. Sekcja struktury służy do dostarczania informacji o typie, który jest używany do konwertowania typów i mapowania kolumn ze źródła do miejsca docelowego. W poniższym przykładzie zestaw danych ma `slicetimestamp` `projectname`trzy `pageviews`kolumny: , , i . Są one typu String, String i Decimal, odpowiednio.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

Każda kolumna w strukturze zawiera następujące właściwości:

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| name |Nazwa kolumny. |Tak |
| type |Typ danych kolumny.  |Nie |
| kultura |. Kultura oparta na sieci, która ma być `Datetime` używana, gdy typem jest typ .NET: lub `Datetimeoffset`. Wartość domyślna to `en-us`. |Nie |
| format |Ciąg formatu, który ma być używany, `Datetime` `Datetimeoffset`gdy typem jest typ .NET: lub . |Nie |

Poniższe wskazówki pomagają określić, kiedy należy uwzględnić informacje o strukturze i co należy uwzględnić w sekcji **struktury.**

* **W przypadku ustrukturyzowanych źródeł danych**należy określić sekcję struktury tylko wtedy, gdy kolumny źródłowe mapy mają przejaswojać kolumny, a ich nazwy nie są takie same. Ten rodzaj ustrukturyzowego źródła danych przechowuje schemat danych i wpisywać informacje wraz z samymi danymi. Przykłady ustrukturyzowanych źródeł danych obejmują SQL Server, Oracle i tabeli Platformy Azure.
  
    Ponieważ informacje o typie są już dostępne dla ustrukturyzowanych źródeł danych, nie należy dołączać informacji o typie podczas dołączania sekcji struktury.
* **W przypadku schematu w źródłach odczytu danych (w szczególności magazynu obiektów Blob)** można zapisać dane bez przechowywania żadnych schematów lub wpisywać informacje z danymi. Dla tych typów źródeł danych należy uwzględnić strukturę, gdy chcesz mapować kolumny źródłowe do kolumn ujścia. Należy również uwzględnić strukturę, gdy zestaw danych jest dane wejściowe dla działania kopiowania i typy danych źródłowego zestawu danych powinny być konwertowane na typy macierzyste dla ujścia.
    
    Fabryka danych obsługuje następujące wartości dostarczania informacji o typie w strukturze: **Int16, Int32, Int64, Single, Double, Decimal, Byte[], Boolean, String, Guid, Datetime, Datetimeoffset i Timespan**. Wartości te są zgodne ze specyfikacją języka wspólnego (CLS), . Wartości typu oparte na sieci.

Usługa Data Factory automatycznie przeprowadza konwersje typów podczas przenoszenia danych ze źródłowego magazynu danych do magazynu danych ujścia.

## <a name="dataset-availability"></a>Dostępność zestawu danych
Sekcja **dostępności** w zestawie danych definiuje okno przetwarzania (na przykład co godzinę, codziennie lub co tydzień) dla zestawu danych. Aby uzyskać więcej informacji na temat okien aktywności, zobacz [Planowanie i wykonywanie](data-factory-scheduling-and-execution.md).

Poniższa sekcja dostępności określa, że wyjściowy zestaw danych jest produkowany co godzinę lub wejściowy zestaw danych jest dostępny co godzinę:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

Jeśli potok ma następujące godziny rozpoczęcia i zakończenia:

```json
    "start": "2016-08-25T00:00:00Z",
    "end": "2016-08-25T05:00:00Z",
```

Wyjściowy zestaw danych jest produkowany co godzinę w godzinach rozpoczęcia i zakończenia potoku. W związku z tym istnieje pięć wycinków zestawu danych produkowanych przez ten potok, po jednym dla każdego okna działania (12 AM - 1 AM, 1 AM - 2 AM, 2 AM - 3 AM, 3 AM - 4 AM, 4 AM - 5 AM).

W poniższej tabeli opisano właściwości, których można użyć w sekcji dostępności:

| Właściwość | Opis | Wymagany | Domyślne |
| --- | --- | --- | --- |
| frequency |Określa jednostkę czasu produkcji plasterków zestawu danych.<br/><br/><b>Obsługiwana częstotliwość:</b>Minuta, Godzina, Dzień, Tydzień, Miesiąc |Tak |Nie dotyczy |
| interval |Określa mnożnik częstotliwości.<br/><br/>"Interwał częstotliwości x" określa, jak często powstaje plasterek. Na przykład, jeśli chcesz, aby zestaw danych był krojony co godzinę, należy ustawić <b>częstotliwość</b> <b>na Godzina</b>i <b>interwał</b> na <b>1</b>.<br/><br/>Należy zauważyć, że jeśli określisz **częstotliwość** jako **Minuta,** należy ustawić interwał na nie mniej niż 15. |Tak |Nie dotyczy |
|  — styl |Określa, czy plasterek powinien być produkowany na początku czy na końcu interwału.<ul><li>StartOfInterval (Początekinterwał)</li><li>KoniecInterwał</li></ul>Jeśli **częstotliwość** jest ustawiona na **Miesiąc,** a **styl** jest ustawiony na **EndOfInterval**, plasterek jest produkowany w ostatnim dniu miesiąca. Jeśli **styl** jest ustawiony na **StartOfInterval**, plasterek jest produkowany w pierwszym dniu miesiąca.<br/><br/>Jeśli **częstotliwość** jest ustawiona na **Dzień,** a **styl** jest ustawiony na **EndOfInterval**, plasterek jest produkowany w ostatniej godzinie dnia.<br/><br/>Jeśli **częstotliwość** jest ustawiona na **Godzina**, a **styl** jest ustawiony na **EndOfInterval**, plasterek jest produkowany na koniec godziny. Na przykład dla plasterka dla okresu 1 PM - 14:00, plasterek jest produkowany o godzinie 14:00. |Nie |KoniecInterwał |
| anchorDateTime |Definiuje położenie bezwzględne w czasie używane przez harmonogram do obliczania granic plasterka zestawu danych. <br/><br/>Należy zauważyć, że jeśli ta właściwość ma części daty, które są bardziej szczegółowe niż określona częstotliwość, bardziej szczegółowe części są ignorowane. Na przykład, jeśli **interwał** jest **co godzinę** (częstotliwość: godzina i interwał: 1), a **anchorDateTime** zawiera **minuty i sekundy**, a następnie minut i sekund części **anchorDateTime** są ignorowane. |Nie |01/01/0001 |
| przesunięcie |Czas, o który są przesuwane początkowe i końcowe wszystkich wycinków zestawu danych. <br/><br/>Należy zauważyć, że jeśli określono zarówno **anchorDateTime,** jak i **offset,** wynikiem jest połączone przesunięcie. |Nie |Nie dotyczy |

### <a name="offset-example"></a>przykład przesunięcia
Domyślnie plasterki`"frequency": "Day", "interval": 1`dzienne ( ) rozpoczynają się o godzinie 12:00 (północ) Skoordynowany czas uniwersalny (UTC). Jeśli chcesz, aby czas rozpoczęcia był 6 AM CZASU UTC zamiast, należy ustawić przesunięcie, jak pokazano w następującym urywek:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>przykład anchorDateTime
W poniższym przykładzie zestaw danych jest produkowany raz na 23 godziny. Pierwszy plasterek rozpoczyna się o godzinie określonej przez **anchorDateTime**, który jest ustawiony na `2017-04-19T08:00:00` (UTC).

```json
"availability":
{
    "frequency": "Hour",
    "interval": 23,
    "anchorDateTime":"2017-04-19T08:00:00"
}
```

### <a name="offsetstyle-example"></a>przykład odsunięcia/stylu
Następujący zestaw danych jest co miesiąc i jest produkowany 3 dnia każdego miesiąca`3.08:00:00`o godzinie 8:00 ( ):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

## <a name="dataset-policy"></a><a name="Policy"></a>Zasady zestawu danych
Sekcja **zasad** w definicji zestawu danych definiuje kryteria lub warunek, który muszą spełniać wycinki zestawu danych.

### <a name="validation-policies"></a>Zasady sprawdzania poprawności
| Nazwa zasady | Opis | Stosowane do | Wymagany | Domyślne |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Sprawdza, czy dane w **magazynie obiektów Blob platformy Azure** spełniają minimalne wymagania dotyczące rozmiaru (w megabajtach). |Azure Blob Storage |Nie |Nie dotyczy |
| minimumRows |Sprawdza, czy dane w **bazie danych SQL platformy Azure** lub **tabeli platformy Azure** zawiera minimalną liczbę wierszy. |<ul><li>Baza danych Azure SQL Database</li><li>Tabela platformy Azure</li></ul> |Nie |Nie dotyczy |

#### <a name="examples"></a>Przykłady
**minimumSizeMB:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**minimumRows:**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

### <a name="external-datasets"></a>Zewnętrzne zestawy danych
Zewnętrzne zestawy danych są te, które nie są produkowane przez uruchomiony potok w fabryce danych. Jeśli zestaw danych jest oznaczony jako **zewnętrzny,** zasady **ExternalData** mogą być zdefiniowane w celu wywrzenia wpływu na zachowanie dostępności plasterka zestawu danych.

Jeśli zestaw danych nie jest produkowany przez fabrykę danych, powinien być oznaczony jako **zewnętrzny**. To ustawienie zazwyczaj ma zastosowanie do danych wejściowych pierwszego działania w potoku, chyba że używane jest tworzenie łańcucha działania lub potoku.

| Nazwa | Opis | Wymagany | Wartość domyślna |
| --- | --- | --- | --- |
| dataDelay |Czas opóźnienia sprawdzania dostępności danych zewnętrznych dla danego plasterka. Na przykład można opóźnić sprawdzanie godzinowe przy użyciu tego ustawienia.<br/><br/>Ustawienie dotyczy tylko bieżącej. Na przykład jeśli jest 13:00 pm teraz i ta wartość jest 10 minut, sprawdzanie poprawności rozpoczyna się o 13:10 PM.<br/><br/>Należy zauważyć, że to ustawienie nie ma wpływu na plasterki w przeszłości. Plasterki z danymi czas +  **zakończenia plasterkówDelay****dataDelay** < **Teraz** są przetwarzane bez żadnych opóźnień.<br/><br/>Czasy większe niż 23:59 godziny `day.hours:minutes:seconds` powinny być określone przy użyciu formatu. Na przykład, aby określić 24 godziny, nie należy używać 24:00:00. Zamiast tego użyj 1.00:00:00. 24:00:00. Przez 1 dzień i 4 godziny określ 1:04:00:00. |Nie |0 |
| retryInterval |Czas oczekiwania między niepowodzeniem a następną próbą. To ustawienie ma zastosowanie do bieżącej chwili. Jeśli poprzednia próba nie powiodła się, następna próba jest po **retryInterval** okres. <br/><br/>Jeśli jest 13:00 pm teraz, zaczynamy pierwszą próbę. Jeśli czas trwania pierwszego sprawdzania poprawności wynosi 1 minutę, a operacja nie powiodła się, następna ponowna próba jest o 1:00 + 1min (czas trwania) + 1min (interwał ponawiania) = 1:02 PM. <br/><br/>W przypadku plasterków w przeszłości nie ma opóźnienia. Ponowna próba odbywa się natychmiast. |Nie |00:01:00 (1 minuta) |
| ponów czas próby |Limit czasu dla każdej próby ponowienia próby.<br/><br/>Jeśli ta właściwość jest ustawiona na 10 minut, sprawdzanie poprawności należy zakończyć w ciągu 10 minut. Jeśli trwa dłużej niż 10 minut, aby wykonać sprawdzanie poprawności, ponawiania czasu.<br/><br/>Jeśli upłynie limit czasu sprawdzania poprawności, plasterek jest oznaczony jako **TimedOut**. |Nie |00:10:00 (10 minut) |
| maksymalnaRetry |Liczba razy, aby sprawdzić dostępność danych zewnętrznych. Maksymalna dozwolona wartość wynosi 10. |Nie |3 |


## <a name="create-datasets"></a>Tworzenie zestawów danych
Zestawy danych można tworzyć przy użyciu jednego z następujących narzędzi lub zestawów SDK:

- Kreator kopiowania
- Visual Studio
- PowerShell
- Szablon usługi Azure Resource Manager
- Interfejs API REST
- Interfejs API .NET

Zobacz następujące samouczki, aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoków i zestawów danych przy użyciu jednego z tych narzędzi lub zestawów SDK:

- [Tworzenie potoku z działaniem przekształcania danych](data-factory-build-your-first-pipeline.md)
- [Tworzenie potoku z działaniem przenoszenia danych](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Po utworzeniu i wdrożeniu potoku można zarządzać potokami i monitorować je przy użyciu bloków portalu Azure lub aplikacji Monitorowania i zarządzania. Instrukcje krok po kroku można znaleźć w następujących tematach:

- [Monitorowanie potoków i zarządzanie nimi przy użyciu bloków portalu Azure](data-factory-monitor-manage-pipelines.md)
- [Monitorowanie potoków i zarządzanie nimi za pomocą aplikacji Monitorowanie i zarządzanie](data-factory-monitor-manage-app.md)

## <a name="scoped-datasets"></a>Zestawy danych o określonym zakresie
Można utworzyć zestawy danych, które są ograniczone do potoku przy użyciu właściwości **zestawów danych.** Te zestawy danych mogą być używane tylko przez działania w ramach tego potoku, a nie przez działania w innych potokach. Poniższy przykład definiuje potok z dwóch zestawów danych (InputDataset-rdc i OutputDataset-rdc) do użycia w potoku.

> [!IMPORTANT]
> Zestawy danych o określonym zakresie są obsługiwane tylko z potokami jednorazowymi (gdzie **pipelineMode** jest ustawiony na **OneTime).** Zobacz [potok jednorazowy, aby](data-factory-create-pipelines.md#onetime-pipeline) uzyskać szczegółowe informacje.
>
>

```json
{
    "name": "CopyPipeline-rdc",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset-rdc"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-rdc"
                    }
                ],
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1,
                    "style": "StartOfInterval"
                },
                "name": "CopyActivity-0"
            }
        ],
        "start": "2016-02-28T00:00:00Z",
        "end": "2016-02-28T00:00:00Z",
        "isPaused": false,
        "pipelineMode": "OneTime",
        "expirationTime": "15.00:00:00",
        "datasets": [
            {
                "name": "InputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "InputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/input",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": true,
                    "policy": {}
                }
            },
            {
                "name": "OutputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "OutputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/output",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": false,
                    "policy": {}
                }
            }
        ]
    }
}
```

## <a name="next-steps"></a>Następne kroki
- Aby uzyskać więcej informacji na temat rurociągów, zobacz [Tworzenie potoków](data-factory-create-pipelines.md).
- Aby uzyskać więcej informacji na temat planowania i wykonywania potoków, zobacz [Planowanie i wykonywanie w usłudze Azure Data Factory.](data-factory-scheduling-and-execution.md)
