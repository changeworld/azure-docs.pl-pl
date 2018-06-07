---
title: Tworzenie zestawów danych w fabryce danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć zestawy danych w fabryce danych Azure, wraz z przykładami, które używają właściwości, takie jak przesunięcie i anchorDateTime.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 0614cd24-2ff0-49d3-9301-06052fd4f92a
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 6a3401f620f7dfe8b42bad9ed1a3981325b2ce1e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620483"
---
# <a name="datasets-in-azure-data-factory"></a>Zestawy danych w fabryce danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](data-factory-create-datasets.md)
> * [Wersja 2 — wersja zapoznawcza](../concepts-datasets-linked-services.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [zestawów danych w wersji 2](../concepts-datasets-linked-services.md).

W tym artykule opisano, jakie zestawy danych są, jak są definiowane w formacie JSON i w jaki sposób są one używane w fabryce danych Azure potoków. W definicji zestawu danych JSON on szczegółowe informacje na temat każdej sekcji (na przykład struktury, dostępności i zasad). Artykuł zawiera również przykłady dotyczące korzystania z **przesunięcie**, **anchorDateTime**, i **styl** właściwości w definicji zestawu danych JSON.

> [!NOTE]
> Jeśli jesteś nowym użytkownikiem usługi fabryka danych, zobacz [wprowadzenie do fabryki danych Azure](data-factory-introduction.md) omówienie. Jeśli nie masz praktyczne doświadczenie w tworzeniu fabryki danych, można uzyskać lepsze zrozumienie odczytując [samouczek przekształcania danych](data-factory-build-your-first-pipeline.md) i [samouczek przepływu danych](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

## <a name="overview"></a>Przegląd
Fabryka danych może obejmować jeden lub wiele potoków. A **potoku** to logiczne grupowanie **działania** który razem wykonania zadania. Działania w potoku definiują akcje do wykonania na danych. Na przykład można użyć działania kopiowania można skopiować danych z lokalnego serwera SQL do magazynu obiektów Blob Azure. Następnie należy użyć działania Hive, które uruchamia skrypt Hive w klastrze usługi HDInsight Azure do przetwarzania danych z magazynu obiektów Blob wygenerowało danych wyjściowych. Ponadto można użyć drugiej działanie kopiowania można skopiować danych wyjściowych do usługi Azure SQL Data Warehouse, na które raportowania są wbudowane rozwiązania analizy biznesowej (BI). Aby uzyskać więcej informacji na temat potoków i działania, zobacz [potoki i działań w fabryce danych Azure](data-factory-create-pipelines.md).

Działanie może zająć zero lub więcej danych wejściowych **zestawów danych**i utworzyć co najmniej jeden wyjściowy zestaw danych. Zestaw danych wejściowych reprezentuje dane wejściowe dla działania w potoku i wyjściowy zestaw danych reprezentuje dane wyjściowe działania. Zestawy danych identyfikują dane w różnych magazynach danych, takich jak tabele, pliki, foldery i dokumenty. Na przykład zestaw danych obiektów Blob platformy Azure określa folder i kontener obiektów blob w magazynie obiektów Blob, z którego potoku odczytywane dane. 

Przed utworzeniem zestawu danych, należy utworzyć **połączona usługa** do łączenia z magazynu danych z fabryką danych. Połączone usługi działają podobnie do parametrów połączenia, umożliwiając definiowanie informacji wymaganych przez usługę Data Factory do nawiązywania połączeń z zasobami zewnętrznymi. Zestawy danych zidentyfikować dane w połączonych magazynów, takich jak tabele SQL, pliki, foldery i dokumenty. Na przykład usługi Azure Storage połączone usługi łączy konta magazynu z fabryką danych. Zestaw danych obiektów Blob platformy Azure reprezentuje kontener obiektów blob i folderu, który zawiera obiekty BLOB wejściowych do przetwarzania. 

Oto przykładowy scenariusz. Aby skopiować dane z magazynu obiektów Blob do bazy danych SQL, należy utworzyć dwa połączone usługi: Magazyn Azure i bazy danych SQL Azure. Następnie utwórz dwa zestawy danych: zestaw danych obiektów Blob platformy Azure, (czyli połączoną usługą magazynu Azure), a tabeli SQL Azure zestawu danych (odwołuje się do usługi baza danych SQL Azure połączone). Usługi magazynu Azure i połączone bazy danych SQL Azure zawiera parametry połączeń, które korzysta z fabryki danych w czasie wykonywania do nawiązania połączenia z usługi Azure Storage i Azure SQL Database, odpowiednio. Zestaw danych obiektów Blob platformy Azure określa folder obiektów blob, który zawiera wejściowych obiektów blob w magazynie obiektów Blob i kontener obiektów blob. Zestaw danych tabeli SQL Azure określa tabeli SQL w bazie danych SQL, do której ma zostać skopiowany danych.

Na poniższym diagramie przedstawiono relacje między potoku, działania, zestawu danych i połączonej usługi z fabryki danych: 

![Relacja między potoku, działania, zestaw danych, połączone usługi](media/data-factory-create-datasets/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>JSON dla zestawu danych
Zestaw danych z fabryki danych jest zdefiniowany w formacie JSON w następujący sposób:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": <boolean flag to indicate external data. only for input datasets>,
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

| Właściwość | Opis | Wymagane | Domyślne |
| --- | --- | --- | --- |
| name |Nazwa zestawu danych. Zobacz [fabryki danych Azure - reguły nazewnictwa](data-factory-naming-rules.md) dla reguły nazewnictwa. |Yes |Nie dotyczy |
| type |Typ zestawu danych. Określ jeden z typów obsługiwanych przez fabrykę danych (na przykład: AzureBlob, AzureSqlTable). <br/><br/>Aby uzyskać więcej informacji, zobacz [typ zestawu](#Type). |Yes |Nie dotyczy |
| Struktura |Schemat zestawu danych.<br/><br/>Aby uzyskać więcej informacji, zobacz [struktury zestawu danych](#Structure). |Nie |Nie dotyczy |
| typeProperties | Właściwości typu są różne dla każdego typu (na przykład: obiektów Blob platformy Azure, tabeli Azure SQL). Aby uzyskać szczegółowe informacje o obsługiwanych typów i ich właściwości, zobacz [typ zestawu](#Type). |Yes |Nie dotyczy |
| external | Flaga wartości logicznej, aby określić, czy element dataset jawnie jest generowany przez potok fabryki danych, czy nie. Jeśli wejściowy zestaw danych działania nie jest generowany przez bieżący potoku, ustawienia tej flagi na wartość true. Ustawienia tej flagi na wartość true dla zestawu danych wejściowych pierwszy działania w potoku.  |Nie |false |
| availability | Definiuje okna przetwarzania (na przykład godzinowo lub dziennie) lub skalowania modelu do produkcji zestawu danych. Każda jednostka danych używane i produkowane przez uruchomienia działania nosi nazwę wycinka danych. Jeśli dostępności wyjściowy zestaw danych jest ustawiony na codziennie (częstotliwość - Day, interwał - 1), wycinek jest tworzony codziennie. <br/><br/>Aby uzyskać więcej informacji, zobacz [dostępności zestawu danych](#Availability). <br/><br/>Szczegółowe informacje dotyczące tworzenia wycinków modelu zestawu danych, zobacz [planowania i wykonywania](data-factory-scheduling-and-execution.md) artykułu. |Yes |Nie dotyczy |
| policy |Definiuje kryteria i warunków, które należy spełnić wycinków zestaw danych. <br/><br/>Aby uzyskać więcej informacji, zobacz [zestawie danych zasad](#Policy) sekcji. |Nie |Nie dotyczy |

## <a name="dataset-example"></a>Przykład zestawu danych
W poniższym przykładzie zestawu danych reprezentuje tabela o nazwie **MyTable** w bazie danych SQL.

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

* **Typ** ma ustawioną wartość AzureSqlTable.
* **tableName** (specyficzne dla typu AzureSqlTable) właściwość type ma ustawioną MyTable.
* **linkedServiceName** odwołuje się do połączonej usługi typu AzureSqlDatabase, który został zdefiniowany w następnym fragment kodu JSON. 
* **częstotliwość dostępności** ma ustawioną wartość dnia, a **interwał** jest ustawiona na 1. Oznacza to, że wycinek zestawu danych jest tworzony codziennie.  

**AzureSqlLinkedService** jest zdefiniowane w następujący sposób:

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

W poprzednim fragment kodu JSON:

* **Typ** ma ustawioną wartość AzureSqlDatabase.
* **connectionString** właściwość type określa informacje do połączenia z bazą danych SQL.  

Jak widać, połączonej usługi definiuje sposób nawiązywania połączenia z bazą danych SQL. Zestaw danych definiuje, jakie tabeli jest używane jako dane wejściowe i wyjściowe działania w potoku.   

> [!IMPORTANT]
> Jeśli zestaw danych jest tworzonym przez potok, powinien być oznaczony jako **zewnętrznych**. To ustawienie dotyczy wejść pierwsze działanie w potoku.   


## <a name="Type"></a> Typ zestawu danych
Typ zestawu danych zależy od źródła danych, którego używasz. Zobacz poniższą tabelę listę magazynów danych obsługiwane przez fabryki danych. Kliknij magazyn danych, aby dowiedzieć się, jak utworzyć połączonej usługi i zestawu danych dla tego magazynu danych.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Przechowuje dane z * można lokalnie lub na Azure infrastruktura jako usługa (IaaS). Te magazyny danych wymagają zainstalowania [brama zarządzania danymi](data-factory-data-management-gateway.md).

W tym przykładzie w poprzedniej sekcji ustawiono typ zestawu danych **AzureSqlTable**. Podobnie dla zestawu danych obiektów Blob platformy Azure, typ zestawu danych ma ustawioną wartość **AzureBlob**, jak pokazano w poniższych JSON:

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
**Struktury** sekcja jest opcjonalna. Definiuje schemat zestawu danych przez zawierający kolekcję nazwy i typy danych kolumn. Sekcja struktury umożliwia Podaj informacje o typie, który służy do konwersji typów i mapowanie kolumn ze źródła do miejsca docelowego. W poniższym przykładzie element dataset zawiera trzy kolumny: `slicetimestamp`, `projectname`, i `pageviews`. Są one typu String, typ String i dziesiętnych, odpowiednio.

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
| Kultury |. Kulturę opartą na sieci do użycia, gdy typem jest typ architektury .NET: `Datetime` lub `Datetimeoffset`. Wartość domyślna to `en-us`. |Nie |
| Format |Ciąg do użycia, gdy typem jest typ architektury .NET formatu: `Datetime` lub `Datetimeoffset`. |Nie |

Poniższe wskazówki pomocne w określeniu, kiedy obejmują informacje o strukturze i elementów do uwzględnienia w **struktury** sekcji.

* **Dla źródeł danych strukturalnych**, określ sekcji struktury tylko wtedy, gdy ma mapować kolumny źródłowe do zbiornika, kolumn i ich nazwy nie są takie same. Tego rodzaju źródła danych strukturalnych przechowuje informacje o schematu i typu danych oraz samych danych. Przykładami źródeł danych strukturalnych programu SQL Server, Oracle i tabeli platformy Azure. 
  
    Ponieważ informacje o typie jest już dostępne dla źródeł danych strukturalnych, nie może zawierać informacje o typie zawierają sekcji struktury.
* **Do schematu w źródłach danych odczytu (w szczególności magazynu obiektów Blob)**, można wybrać do przechowywania danych bez przechowywania żadnych informacji schematu lub typ z danymi. W przypadku tych typów źródeł danych mają strukturę, aby mapować kolumny źródłowe do zbiornika kolumn. Gdy zestaw danych jest wartością wejściową dla działania kopiowania i typy danych zestawu źródła danych powinny być konwertowane na typy natywne dla obiekt sink także struktury. 
    
    Fabryka danych obsługuje następujące wartości udostępnienie informacji o typie w strukturze: **Int16, Int32, Int64, pojedynczego, Double, Decimal bajtów [], wartość logiczna, ciąg, Guid, Datetime, Datetimeoffset i Timespan**. Te wartości są specyfikacja języka wspólnego (CLS)-zgodne,. Wartości typu opartej na sieci.

Fabryka danych automatycznie wykonuje konwersje typów podczas przenoszenia danych z magazynu danych źródła do ujścia magazynu danych. 
  

## <a name="dataset-availability"></a>Dostępność zestawu danych
**Dostępności** sekcja w zestawie danych definiuje okna przetwarzania (na przykład co godzinę, codziennie lub co tydzień) dla zestawu danych. Aby uzyskać więcej informacji na temat działania systemu windows, zobacz [planowania i wykonywania](data-factory-scheduling-and-execution.md).

W poniższej sekcji dostępności Określa, że wyjściowy zestaw danych albo tworzone co godzinę lub co godzinę wejściowy zestaw danych jest dostępne:

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 1    
}
```

Jeśli potoku ma następujące rozpoczęcia i zakończenia:  

```json
    "start": "2016-08-25T00:00:00Z",
    "end": "2016-08-25T05:00:00Z",
```

Wyjściowy zestaw danych jest tworzony co godzinę w potoku godziny rozpoczęcia i zakończenia. W związku z tym ma pięć wycinków zestaw danych utworzonych w ramach tego potoku, po jednej dla każdego działania okna (00: 00 - 1 AM, 1: 00 - 2 AM, 2 AM - 3 AM, 3 AM — 4 AM, 4: 00 - 5: 00). 

W poniższej tabeli opisano właściwości, które można użyć w sekcji dostępności:

| Właściwość | Opis | Wymagane | Domyślne |
| --- | --- | --- | --- |
| frequency |Określa jednostkę czasu dla trybu produkcyjnego wycinek zestawu danych.<br/><br/><b>Obsługiwana częstotliwość</b>: minuty, godziny, dnia, tygodnia, miesiąca |Yes |Nie dotyczy |
| interval |Określa mnożnik częstotliwości.<br/><br/>"Interwał częstotliwości x" Określa, jak często jest tworzony wycinek. Na przykład, jeśli potrzebujesz zestawu danych, aby zostać podzielona na godzinę, należy ustawić <b>częstotliwość</b> do <b>godzina</b>, i <b>interwał</b> do <b>1</b>.<br/><br/>Należy pamiętać, że jeśli określisz **częstotliwość** jako **minutę**, należy ustawić interwał nie mniej niż 15. |Yes |Nie dotyczy |
| Styl |Określa, czy wycinek będą tworzone na początku lub końca zakresu.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul>Jeśli **częstotliwość** ma ustawioną wartość **miesiąca**, i **styl** ustawiono **EndOfInterval**, wycinek jest generowany na ostatni dzień miesiąca. Jeśli **styl** ustawiono **StartOfInterval**, wycinek jest generowany na pierwszy dzień miesiąca.<br/><br/>Jeśli **częstotliwość** ma ustawioną wartość **dzień**, i **styl** ustawiono **EndOfInterval**, wycinek jest generowany w ciągu ostatniej godziny, dnia.<br/><br/>Jeśli **częstotliwość** ustawiono **godzina**, i **styl** ma ustawioną wartość **EndOfInterval**, wycinek jest generowany na koniec godziny. Na przykład dla wycinka okres 13: 00 - 14: 00, wycinek jest generowany na 14: 00. |Nie |EndOfInterval |
| anchorDateTime |Definiuje położenie bezwzględne w czasie używanych przez harmonogram do obliczenia granice wycinek zestawu danych. <br/><br/>Należy zwrócić uwagę, jeśli ta propoerty części daty, które są bardziej szczegółowego niż określona częstotliwość, bardziej szczegółowego części są ignorowane. Na przykład jeśli **interwał** jest **co godzinę** (częstotliwość: godzinę i interwał: 1) i **anchorDateTime** zawiera **minut i sekund**, a następnie części minut i sekund **anchorDateTime** są ignorowane. |Nie |01/01/0001 |
| Przesunięcie |Zakres czasu za pomocą której zostaną przesunięte początku i końcu wszystkie fragmenty zestawu danych. <br/><br/>Należy pamiętać, że jeśli obie **anchorDateTime** i **przesunięcie** są określone, wynikiem jest połączonych shift. |Nie |Nie dotyczy |

### <a name="offset-example"></a>przykład przesunięcia
Domyślnie codziennie (`"frequency": "Day", "interval": 1`) wycinków Rozpocznij od 00: 00 (północ) uniwersalny czas koordynowany (UTC). Czas rozpoczęcia jako czas UTC 6: 00, zamiast tego należy ustawić przesunięcie pokazane na następujący fragment kodu: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>przykład anchorDateTime
W poniższym przykładzie zestawu danych jest tworzony co 23 godz. Pierwszy wycinek rozpoczyna się w czasie określonym przez **anchorDateTime**, która ma wartość `2017-04-19T08:00:00` (UTC).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>przykład przesunięcie i stylu
Następujący zestaw danych jest co miesiąc i jest generowany na 3rd każdego miesiąca o godzinie 8:00 AM (`3.08:00:00`):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

## <a name="Policy"></a>Zestaw danych zasad
**Zasad** sekcja w definicji zestawu danych definiuje kryteria i warunków, które należy spełnić wycinków zestaw danych.

### <a name="validation-policies"></a>Zasady sprawdzania poprawności
| Nazwa zasad | Opis | Dotyczy | Wymagane | Domyślne |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Sprawdza, czy dane w **magazynu obiektów Blob Azure** spełnia wymagania minimalny rozmiar (w megabajtach). |Azure Blob Storage |Nie |Nie dotyczy |
| minimumRows |Sprawdza, czy dane w **bazy danych Azure SQL** lub **tabeli platformy Azure** zawiera minimalną liczbę wierszy. |<ul><li>Baza danych SQL Azure</li><li>Tabela platformy Azure</li></ul> |Nie |Nie dotyczy |

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
Zewnętrznych zestawów danych są tymi, które nie są tworzone przez uruchomione potok w fabryce danych. Jeśli zestaw danych jest oznaczona jako **zewnętrznych**, **ExternalData** zasad może być zdefiniowana do wywierania wpływu na zachowanie dostępności wycinek zestawu danych.

Jeśli zestaw danych jest tworzonym przez fabrykę danych, powinien być oznaczony jako **zewnętrznych**. To ustawienie dotyczy wejść pierwsze działanie w potoku, chyba że działania lub łańcucha potoku jest używany.

| Name (Nazwa) | Opis | Wymagane | Wartość domyślna |
| --- | --- | --- | --- |
| dataDelay |Czas opóźnienia sprawdzanie dostępności danych zewnętrznych dla danego wycinka. Na przykład za pomocą tego ustawienia można opóźnić wyboru co godzinę.<br/><br/>Ustawienie ma zastosowanie tylko do chwili obecnej.  Na przykład jeśli 1:00 PM od razu i ta wartość to 10 minut, sprawdzanie poprawności rozpoczyna się od 1:22:00.<br/><br/>Należy pamiętać, że to ustawienie nie dotyczy wycinków w przeszłości. Wycinków z **czas zakończenia wycinek** + **dataDelay** < **teraz** są przetwarzane bez opóźnień.<br/><br/>Godzinach większą niż 23:59 godziny należy określić przy użyciu `day.hours:minutes:seconds` format. Na przykład aby określić 24 godziny, nie używaj 24:00:00. Zamiast tego należy użyć 1.00:00:00. Jeśli używasz 24:00:00, będzie traktowane jako 24 dni (24.00:00:00). 1 dzień i 4 godziny Określ 1:04:00:00. |Nie |0 |
| retryInterval |Czas oczekiwania między awarii i ponowieniem próby. To ustawienie ma zastosowanie do chwili obecnej. Jeśli poprzedni ponów zakończone niepowodzeniem, po następnej próbie **retryInterval** okresu. <br/><br/>Jeśli jest 1:00 PM od razu, możemy rozpocząć pierwszej próby. W przypadku czasu oczekiwania na zakończenie pierwszej sprawdzanie poprawności 1 minutę i operacja nie powiodła się, następna ponowna próba wynosi 1:00 1 min (czas trwania) + 1min (interwału ponawiania prób) = 13:02:00. <br/><br/>Wycinki w przeszłości nie ma żadnego opóźnienia. Ponów próbę odbywa się natychmiast. |Nie |00:01:00 (1 minuta) |
| retryTimeout |Limit czasu dla każdego ponowienia próby.<br/><br/>Jeśli ta właściwość jest ustawiona na 10 minut, należy wykonać sprawdzanie poprawności w ciągu 10 minut. Jeśli trwa dłużej niż 10 minut, aby wykonać sprawdzanie poprawności, limit czasu próby.<br/><br/>Jeśli wszystkie próby dla limitu czasu sprawdzania poprawności, wycinka, jest oznaczone jako **upłynął limit czasu**. |Nie |00:10:00 (10 minut) |
| maximumRetry |Liczba razy, aby sprawdzić dostępność danych zewnętrznych. Maksymalna dozwolona wartość to 10. |Nie |3 |


## <a name="create-datasets"></a>Tworzenie zestawów danych
Zestawy danych można utworzyć za pomocą jednej z tych narzędzi i zestawy SDK: 

- Kreator kopiowania 
- Azure Portal
- Visual Studio
- PowerShell
- Szablon usługi Azure Resource Manager
- Interfejs API REST
- Interfejs API .NET

Zobacz następujące samouczki krok po kroku tworzenia potoki i zestawów danych przy użyciu jednej z tych narzędzi i zestawy SDK:
 
- [Tworzenie potoku z działaniem przekształcania danych](data-factory-build-your-first-pipeline.md)
- [Tworzenie potoku z działaniem przepływu danych](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Po utworzeniu i wdrożeniu potoku można zarządzać i monitorować Twoje potoki za pomocą Azure bloki portalu lub aplikacji, monitorowania i zarządzania. Zobacz poniższe tematy zawierają instrukcje krok po kroku: 

- [Monitorowanie i zarządzanie nimi potoki przy użyciu bloków portalu Azure](data-factory-monitor-manage-pipelines.md)
- [Monitorowanie i zarządzanie nimi potoki przy użyciu aplikacji monitorowanie i zarządzanie](data-factory-monitor-manage-app.md)


## <a name="scoped-datasets"></a>Zestawy danych w zakresie
Możesz utworzyć zestawy danych, które należą do potoku zakresu przy użyciu **zestawów danych** właściwości. Te zestawy danych można używać tylko przez działania w ramach tego potoku, a nie przez działania w innych potoków. W poniższym przykładzie zdefiniowano potoku z dwóch zestawów danych (InputDataset rdc i kompresji rdc OutputDataset) do użycia w potoku.  

> [!IMPORTANT]
> Zestawy danych w zakresie są obsługiwane tylko w przypadku jednorazowego potoki (gdzie **pipelineMode** ustawiono **OneTime**). Zobacz [potoku Onetime](data-factory-create-pipelines.md#onetime-pipeline) szczegółowe informacje.
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
- Aby uzyskać więcej informacji o sposobie planowania i wykonywania potoków, zobacz [planowania i wykonywania w fabryce danych Azure](data-factory-scheduling-and-execution.md). 
