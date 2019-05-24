---
title: Tworzenie zestawów danych w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć zestawy danych w usłudze Azure Data Factory przy użyciu przykładów, które używają właściwości, takie jak przesunięcie i anchorDateTime.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 0614cd24-2ff0-49d3-9301-06052fd4f92a
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 6b16b6c4de8c8d2d7a821dd476f07c8ab1135408
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60487271"
---
# <a name="datasets-in-azure-data-factory"></a>Zestawy danych w usłudze Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](data-factory-create-datasets.md)
> * [Wersja 2 (bieżąca wersja)](../concepts-datasets-linked-services.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [zestawów danych w wersji 2](../concepts-datasets-linked-services.md).

W tym artykule opisano, jakie zestawy danych są, jak są one definiowane w formacie JSON i w jaki sposób są one używane w potoki usługi Azure Data Factory. Zapewnia szczegółowe informacje na temat każdej sekcji (na przykład, struktury, dostępność i zasady) w definicji JSON zestawu danych. Artykuł zawiera również przykłady dotyczące używania **przesunięcie**, **anchorDateTime**, i **styl** właściwości w definicji JSON zestawu danych.

> [!NOTE]
> Jeśli jesteś nowym użytkownikiem usługi Data Factory, zobacz [wprowadzenie do usługi Azure Data Factory](data-factory-introduction.md) omówienie. Jeśli nie masz zdobycie praktycznego doświadczenia z tworzenia fabryk danych, można uzyskać lepsze zrozumienie, zapoznając się [samouczkiem dotyczącym przekształcania danych](data-factory-build-your-first-pipeline.md) i [samouczek przenoszenia danych](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="overview"></a>Omówienie
Fabryka danych może obejmować jeden lub wiele potoków. A **potoku** jest logicznym grupowaniem **działania** wspólnie wykonują zadanie. Działania w potoku definiują akcje do wykonania na danych. Może na przykład użyć działania kopiowania, aby skopiować dane z lokalnego programu SQL Server do usługi Azure Blob storage. Następnie należy użyć działania programu Hive, które uruchamia skrypt Hive w klastrze usługi HDInsight platformy Azure do przetwarzania danych z magazynu obiektów Blob w celu wygenerowania danych wyjściowych. Może na koniec użyj drugiego działania kopiowania, aby skopiować dane wyjściowe do usługi Azure SQL Data Warehouse, na podstawie której raportowania dotyczącego rozwiązania analizy biznesowej (BI). Aby uzyskać więcej informacji na temat potoków i działań, zobacz [potokami i działaniami w usłudze Azure Data Factory](data-factory-create-pipelines.md).

Działanie może zająć zero lub więcej danych wejściowych **zestawów danych**i tworzące co najmniej jeden wyjściowe zestawy danych. Wejściowy zestaw danych reprezentuje dane wejściowe dla działania w potoku i wyjściowy zestaw danych reprezentuje dane wyjściowe dla działania. Zestawy danych identyfikują dane w różnych magazynach danych, takich jak tabele, pliki, foldery i dokumenty. Na przykład zestaw danych usługi Azure Blob Określa kontener obiektów blob i folder w usłudze Blob storage, z których potok ma odczytywać dane.

Przed utworzeniem zestawu danych, Utwórz **połączoną usługę** połączyć usługi magazynu danych w usłudze data factory. Połączone usługi działają podobnie do parametrów połączenia, umożliwiając definiowanie informacji wymaganych przez usługę Data Factory do nawiązywania połączeń z zasobami zewnętrznymi. Zestawy danych identyfikują dane w połączonych magazynach danych, takich jak SQL tabel, plików, folderów i dokumentów. Na przykład Azure Storage połączona usługa łączy konto usługi storage z fabryką danych. Zestaw danych obiektów Blob platformy Azure reprezentuje kontener obiektów blob oraz folder, który zawiera wejściowe obiekty BLOB mają być przetwarzane.

Poniżej przedstawiono przykładowy scenariusz. Aby skopiować dane z magazynu obiektów Blob do usługi SQL database, utworzysz dwie połączone usługi: Azure Storage i Azure SQL Database. Następnie należy utworzyć dwa zestawy danych: Azure zestaw danych obiektów Blob (która odwołuje się do połączonej usługi Azure Storage) i zestaw danych tabeli SQL Azure, (która odwołuje się do usługi Azure SQL Database, połączone). Usługi Azure Storage i Azure SQL Database, połączone usługi zawiera parametry połączenia, które usługi Data Factory używa w środowisku uruchomieniowym połączyć się z usługi Azure Storage i Azure SQL Database, odpowiednio. Zestaw danych obiektów Blob platformy Azure Określa kontener obiektów blob i folder obiektów blob, który zawiera wejściowe obiekty BLOB w usłudze Blob storage. Zestaw danych tabeli SQL Azure Określa tabelę SQL w usłudze SQL database, do której ma zostać skopiowane dane.

Na poniższym diagramie przedstawiono relacje między potoku, działania, zestaw danych i połączonej usługi w usłudze Data Factory:

![Relacja potoku, działania, zestaw danych, połączonych usług](media/data-factory-create-datasets/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>Dataset JSON
Zestaw danych w usłudze Data Factory jest zdefiniowany w formacie JSON:

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

W poniższej tabeli opisano właściwości w powyższy kod JSON:

| Właściwość | Opis | Wymagane | Domyślne |
| --- | --- | --- | --- |
| name |Nazwa zestawu danych. Zobacz [usługi Azure Data Factory — reguły nazewnictwa](data-factory-naming-rules.md) reguły nazewnictwa. |Yes |Nie dotyczy |
| type |Typ zestawu danych. Określ jeden z typów obsługiwanych przez usługę Data Factory (na przykład: AzureBlob, AzureSqlTable). <br/><br/>Aby uzyskać więcej informacji, zobacz [typ zestawu danych](#Type). |Yes |Nie dotyczy |
| structure |Schemat zestawu danych.<br/><br/>Aby uzyskać więcej informacji, zobacz [struktury zestawu danych](#Structure). |Nie |Nie dotyczy |
| typeProperties | Właściwości typu są różne dla każdego typu (na przykład: Usługa Azure Blob, tabela Azure SQL). Szczegółowe informacje na temat obsługiwanych typów i ich właściwości, [typ zestawu danych](#Type). |Yes |Nie dotyczy |
| external | Flagę logiczną, aby określić, czy zestaw danych jest jawnie generowany przez potok usługi data factory, czy nie. Jeśli wejściowy zestaw danych działania nie jest generowany przez bieżącego potoku, należy ustawić tę flagę na wartość true. Tej flagi należy ustawić na wartość true dla wejściowego zestawu danych pierwszego działania w potoku.  |Nie |false |
| availability | Definiuje okno przetwarzania (na przykład co godzinę lub codziennie) lub model tworzenia wycinków w środowisku produkcyjnym zestaw danych. Każda jednostka danych używane i generowane przez uruchomienia działania jest nazywany wycinka danych. Jeśli dostępnością wyjściowego zestawu danych jest ustawiony na codziennie (częstotliwość — dzień, interval — 1), wycinek jest generowany codziennie. <br/><br/>Aby uzyskać szczegółowe informacje Zobacz sekcję dotyczącą dostępności zestawu danych. <br/><br/>Szczegółowe informacje na temat zestawu danych model tworzenia wycinków, [planowanie i wykonywanie](data-factory-scheduling-and-execution.md) artykułu. |Yes |Nie dotyczy |
| policy |Definiuje kryteria lub warunek, który należy spełnić wycinków zestawu danych. <br/><br/>Aby uzyskać więcej informacji, zobacz [zestawie danych zasad](#Policy) sekcji. |Nie |Nie dotyczy |

## <a name="dataset-example"></a>Przykładowy zestaw danych
W poniższym przykładzie zestaw danych reprezentuje tabelę o nazwie **MyTable** w usłudze SQL database.

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

* **Typ** jest ustawiona na AzureSqlTable.
* **Właściwość tableName** MyTable ustawiono właściwość type (specyficzne AzureSqlTable typu).
* **linkedServiceName** odwołuje się do połączonej usługi typu AzureSqlDatabase, która jest zdefiniowana w następnym fragmencie kodu JSON.
* **częstotliwość dostępności** jest ustawiona na dzień, a **interwał** jest ustawiona na 1. Oznacza to, że wycinek zestaw danych jest generowany codziennie.

**AzureSqlLinkedService** jest zdefiniowana w następujący sposób:

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

W poprzednim fragmencie kodu JSON:

* **Typ** jest ustawiona na AzureSqlDatabase.
* **element connectionString** właściwość type określa informacje do łączenia z bazą danych SQL.

Jak widać, połączona usługa definiuje sposób nawiązywania połączeń z bazą danych SQL. Zestaw danych określa, jakie tabela jest używany jako dane wejściowe i wyjściowe dla działania w potoku.

> [!IMPORTANT]
> Chyba, że zestaw danych jest generowany przez potok, powinien być oznaczony jako **zewnętrznych**. To ustawienie dotyczy dane wejściowe pierwszego działania w potoku.

## <a name="Type"></a> Typ zestawu danych
Typ zestawu danych, zależy od magazynu danych, którego używasz. Zobacz poniższą tabelę, aby uzyskać listę magazynów danych obsługiwanych przez usługę Data Factory. Kliknij magazyn danych, aby dowiedzieć się, jak utworzyć połączoną usługę i zestaw danych dla tego magazynu danych.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Magazyny danych za pomocą * mogą być konfigurowane lokalnie lub w infrastrukturze platformy Azure jako usługa (IaaS). Te magazyny danych wymagają zainstalowania [bramy zarządzania danymi](data-factory-data-management-gateway.md).

W przykładzie w poprzedniej sekcji, typ zestawu danych jest równa **AzureSqlTable**. Podobnie dla zestawu danych obiektów Blob platformy Azure, typ zestawu danych jest równa **AzureBlob**, jak pokazano w poniższym formacie JSON:

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

## <a name="Structure"></a>Struktura zestawu danych
**Struktury** sekcja jest opcjonalna. Definiuje schemat zestawu danych, zawierający kolekcję nazwy i typy danych kolumn. Sekcja struktury umożliwia dostarczyć informacji o typie, który służy do konwersji typów oraz mapowanie kolumn ze źródła do miejsca docelowego. W poniższym przykładzie zestaw danych zawiera trzy kolumny: `slicetimestamp`, `projectname`, i `pageviews`. Są one typu String, String i dziesiętne, odpowiednio.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

Każda kolumna w strukturze zawiera następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| name |Nazwa kolumny. |Yes |
| type |Typ danych kolumny.  |Nie |
| culture |. Kulturę opartą na sieci ma być używany, gdy typem jest typ architektury .NET: `Datetime` lub `Datetimeoffset`. Wartość domyślna to `en-us`. |Nie |
| format |Format ciągu ma być używany, gdy typem jest typ architektury .NET: `Datetime` lub `Datetimeoffset`. |Nie |

Poniższe wskazówki pomocne w określeniu, kiedy należy uwzględnić informacje o strukturze i co należy uwzględnić w **struktury** sekcji.

* **W przypadku źródeł danych ze strukturą**, określ sekcji struktury, tylko wtedy, gdy chcesz, aby zamapować kolumny źródła do ujścia kolumn i ich nazwy nie są takie same. Tego rodzaju źródła danych ze strukturą są przechowywane informacje schematu i typu danych oraz samych danych. Przykładami źródeł danych ze strukturą programu SQL Server, Oracle i tabela platformy Azure.
  
    Jak informacje o typie jest już dostępna dla źródeł danych ze strukturą, nie może zawierać informacje o typie, gdy zawiera sekcję struktury.
* **Dla schematu do źródeł danych odczytu (w szczególności usługi Blob storage)**, istnieje możliwość przechowywania danych bez przechowywania żadnych informacji schematu lub typu z danymi. Dla tych typów źródeł danych mają strukturę, gdy chcesz mapować kolumny źródła i ujścia kolumn. Również mają strukturę, gdy zestaw danych jest wartością wejściową dla działania kopiowania, a typy danych źródłowy zestaw danych powinny być konwertowane na typach natywnych ujścia.
    
    Usługa Data Factory obsługuje następujące wartości do dostarczania informacji o typie w strukturze: **Int16, Int32, Int64, pojedynczego, Double, Decimal, bajt [], atrybut typu wartość logiczna, ciąg, identyfikator Guid, daty/godziny, Datetimeoffset i Timespan**. Te wartości są Common Language Specification (CLS)-zgodne. Wartości typu opartego na sieci.

Podczas przenoszenia danych z magazynu danych źródłowych do magazynu danych ujścia usługi Data Factory automatycznie wykonuje konwersje typów.

## <a name="dataset-availability"></a>Dostępności zestawu danych
**Dostępności** sekcji w zestawie danych definiuje okien przetwarzania (na przykład co godzinę, codziennie lub co tydzień) dla zestawu danych. Aby uzyskać więcej informacji na temat aktywności systemu windows zobacz [planowanie i wykonywanie](data-factory-scheduling-and-execution.md).

W poniższej sekcji dostępność Określa, że wyjściowy zestaw danych albo są generowane co godzinę lub wejściowy zestaw danych jest dostępny co godzinę:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

Jeśli potok zawiera następujące czasu rozpoczęcia i zakończenia:

```json
    "start": "2016-08-25T00:00:00Z",
    "end": "2016-08-25T05:00:00Z",
```

Wyjściowy zestaw danych jest generowany co godzinę w ramach potoku godziny rozpoczęcia i zakończenia. Dlatego jest pięć wycinków zestaw danych utworzony przez ten potok, jeden dla każdego okna działania (00: 00 - 1 AM, 1: 00 - 2 AM, 2: 00 - 3 AM, 3: 00 - 4 AM, 4: 00 - 5: 00).

W poniższej tabeli opisano właściwości, które można użyć w sekcji dostępności:

| Właściwość | Opis | Wymagane | Domyślne |
| --- | --- | --- | --- |
| frequency |Określa jednostkę czasu dla trybu produkcyjnego wycinek zestawu danych.<br/><br/><b>Obsługiwana częstotliwość</b>: Minuty, godziny, dnia, tygodnia, miesiąca |Yes |Nie dotyczy |
| interval |Określa mnożnik częstotliwości.<br/><br/>"Interwał częstotliwości x" Określa, jak często wycinek jest generowany. Na przykład, jeśli potrzebujesz zestawu danych można podzielić w systemie godzinowym, należy ustawić <b>częstotliwość</b> do <b>godzinę</b>, i <b>interwał</b> do <b>1</b>.<br/><br/>Należy pamiętać, że jeśli określisz **częstotliwość** jako **minutę**, należy ustawić interwał nie może być mniej niż 15. |Yes |Nie dotyczy |
| style |Określa, czy wycinek powinny być tworzone na początku lub końcu interwału.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul>Jeśli **częstotliwość** ustawiono **miesiąca**, i **styl** jest ustawiona na **EndOfInterval**, wycinek jest generowany na ostatni dzień miesiąca. Jeśli **styl** ustawiono **StartOfInterval**, wycinek jest generowany pierwszego dnia miesiąca.<br/><br/>Jeśli **częstotliwość** jest ustawiona na **dzień**, i **styl** ustawiono **EndOfInterval**, wycinek jest generowany w ciągu ostatniej godziny dnia.<br/><br/>Jeśli **częstotliwość** ustawiono **godzinę**, i **styl** jest ustawiona na **EndOfInterval**, wycinek jest generowany na koniec godziny. Na przykład dla wycinka okres 13: 00 - 14: 00, wycinek jest generowany w 14: 00. |Nie |EndOfInterval |
| anchorDateTime |Definiuje położenie bezwzględne w czasie używanych przez harmonogram do obliczenia granice wycinek zestawu danych. <br/><br/>Należy pamiętać, że jeśli ta właściwość ma części daty, które są bardziej szczegółowe niż określoną częstotliwością, części bardziej szczegółowe są ignorowane. Na przykład jeśli **interwał** jest **co godzinę** (frequency: hour, interval: 1), a **anchorDateTime** zawiera **minuty i sekundy**, a następnie minuty i sekundy części **anchorDateTime** są ignorowane. |Nie |01/01/0001 |
| offset |Zakres czasu za pomocą którego przesunięte początek i koniec okresu wszystkich wycinków zestawu danych. <br/><br/>Należy pamiętać, że jeśli oba **anchorDateTime** i **przesunięcie** są określone, wynik jest połączone shift. |Nie |Nie dotyczy |

### <a name="offset-example"></a>przykład przesunięcia
Domyślnie codziennie (`"frequency": "Day", "interval": 1`) wycinki rozpoczynają się od 12: 00 (północ) uniwersalny czas koordynowany (UTC). Czas rozpoczęcia to 6: 00 czasu UTC, zamiast tego należy ustawić przesunięcie, jak pokazano w poniższym fragmencie kodu:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>przykład anchorDateTime
W poniższym przykładzie zestaw danych jest generowany co 23 godzin. Pierwszy wycinek, który rozpoczyna się o godzinie określonej przez **anchorDateTime**, która jest równa `2017-04-19T08:00:00` (UTC).

```json
"availability":
{
    "frequency": "Hour",
    "interval": 23,
    "anchorDateTime":"2017-04-19T08:00:00"
}
```

### <a name="offsetstyle-example"></a>przykład przesunięcie/styl
Poniższy zestaw danych jest co miesiąc i jest generowany na 3 każdego miesiąca o 8:00 (`3.08:00:00`):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

## <a name="Policy"></a>Zestaw danych zasad
**Zasad** sekcji w definicji zestawu danych definiuje kryteria lub warunek, który należy spełnić wycinków zestawu danych.

### <a name="validation-policies"></a>Sprawdzanie poprawności zasad
| Nazwa zasad | Opis | Dotyczy | Wymagane | Domyślne |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Sprawdza, czy dane w **usługi Azure Blob storage** spełnia wymagania minimalny rozmiar (w megabajtach). |Azure Blob Storage |Nie |Nie dotyczy |
| minimumRows |Sprawdza, czy dane w **bazy danych Azure SQL** lub **tabeli platformy Azure** zawiera minimalną liczbę wierszy. |<ul><li>Baza danych Azure SQL Database</li><li>Tabela platformy Azure</li></ul> |Nie |Nie dotyczy |

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

### <a name="external-datasets"></a>Zewnętrznych zestawów danych
Zewnętrznych zestawów danych są tymi, które nie są produkowane przez uruchamianie potoku w fabryce danych. Jeśli zestaw danych jest oznaczona jako **zewnętrznych**, **ExternalData** zasad może być zdefiniowana w celu wywierania wpływu na zachowanie dostępności wycinek zestawu danych.

Chyba, że zestaw danych jest generowany przez usługę Data Factory, powinien być oznaczony jako **zewnętrznych**. To ustawienie dotyczy dane wejściowe pierwszego działania w potoku, chyba że działania lub łańcucha potoku jest używany.

| Name (Nazwa) | Opis | Wymagane | Wartość domyślna |
| --- | --- | --- | --- |
| dataDelay |Czas opóźnienia wyboru na dostępność danych zewnętrznych dla danego wycinka. Na przykład można opóźnić wyboru godzinowe za pomocą tego ustawienia.<br/><br/>To ustawienie dotyczy tylko do chwili obecnej. Na przykład jeśli jest 1:00 PM teraz, a wartość ta wynosi 10 minut, sprawdzanie poprawności rozpoczyna się od 1:10 PM.<br/><br/>Należy pamiętać, że to ustawienie nie wpływa na wycinki w przeszłości. Dzieli z **czas zakończenia wycinka** + **dataDelay** < **teraz** są przetwarzane bez żadnego opóźnienia.<br/><br/>Razy większa od 23:59 godzin, można określić za pomocą `day.hours:minutes:seconds` formatu. Na przykład aby określić 24 godziny, nie używaj 24:00:00. Zamiast tego należy użyć 1.00:00:00. Jeśli używasz 24:00:00, jest ona traktowana jako 24 dni (24.00:00:00). W przypadku 1 dzień i 4 godziny należy określić 1:04:00:00. |Nie |0 |
| retryInterval |Czas oczekiwania między awarii, a następnie spróbuj. To ustawienie ma zastosowanie do chwili obecnej. Jeśli poprzedni nie powiodło się, spróbuj dalej jest po **retryInterval** okres. <br/><br/>Jeśli jest to 1:00 PM teraz, możemy rozpocząć pierwszej próby. W przypadku 1 minutę i operacja nie powiodła się czas do ukończenia pierwsze sprawdzenie poprawności następne ponowienie próby wynosi 1:00 + 1 min (czas trwania) + 1min (interwał ponawiania) = 13:02:00. <br/><br/>Wycinki w przeszłości nie ma żadnego opóźnienia. Ponowienie próby odbywa się natychmiast. |Nie |00:01:00 (1 minuta) |
| retryTimeout |Limit czasu dla każdego ponowienia próby.<br/><br/>Jeśli ta właściwość jest ustawiona na 10 minut, należy wykonać sprawdzanie poprawności w ciągu 10 minut. Jeśli trwa dłużej niż 10 minut, aby wykonać sprawdzanie poprawności, ponowienie próby upłynie limit czasu.<br/><br/>Jeśli wszystkie próby limit czasu sprawdzania poprawności, wycinek, zostanie oznaczona jako **przekroczenie limitu czasu**. |Nie |00:10:00 (10 minut) |
| maximumRetry |Liczba razy, aby sprawdzić dostępność danych zewnętrznych. Maksymalna dozwolona wartość to 10. |Nie |3 |


## <a name="create-datasets"></a>Tworzenie zestawów danych
Zestawy danych można utworzyć przy użyciu jednej z następujących narzędzi lub zestawów SDK:

- Kreator kopiowania
- Azure Portal
- Visual Studio
- PowerShell
- Szablon usługi Azure Resource Manager
- Interfejs API REST
- Interfejs API .NET

Zobacz następujące samouczki krok po kroku dotyczące tworzenia potoków i zestawów danych przy użyciu jednej z następujących narzędzi lub zestawów SDK:

- [Tworzenie potoku z działaniem przekształcania danych](data-factory-build-your-first-pipeline.md)
- [Tworzenie potoku za pomocą działania przenoszenia danych](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Po utworzeniu i wdrożeniu potoku można zarządzać i monitorowania potoków przy użyciu bloków witryny Azure portal lub aplikacji monitorowanie i zarządzanie. Zobacz następujące tematy, aby uzyskać instrukcje krok po kroku:

- [Monitorowanie potoków i zarządzanie nimi przy użyciu bloków w witrynie Azure portal](data-factory-monitor-manage-pipelines.md)
- [Monitorowanie potoków i zarządzanie nimi przy użyciu aplikacji monitorowanie i zarządzanie](data-factory-monitor-manage-app.md)

## <a name="scoped-datasets"></a>Zestawy danych o określonym zakresie
Możesz utworzyć zestawy danych, które są ograniczone do potoku przy użyciu **zestawów danych** właściwości. Te zestawy danych można używać tylko przez działania w ramach tego potoku, a nie przez działania w innych potokach. W poniższym przykładzie zdefiniowano potoku za pomocą dwóch zestawów danych (kompresji rdc InputDataset i OutputDataset rdc) do użycia w ramach potoku.

> [!IMPORTANT]
> O określonym zakresie zestawy danych są obsługiwane tylko w przypadku jednorazowego potoków (gdzie **pipelineMode** ustawiono **OneTime**). Zobacz [potoku Onetime](data-factory-create-pipelines.md#onetime-pipeline) Aby uzyskać szczegółowe informacje.
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

## <a name="next-steps"></a>Kolejne kroki
- Aby uzyskać więcej informacji na temat potoków, zobacz [tworzenie potoków](data-factory-create-pipelines.md).
- Aby uzyskać więcej informacji na temat sposobu planowania i wykonywania potoków, zobacz [planowanie i wykonywanie w usłudze Azure Data Factory](data-factory-scheduling-and-execution.md).
