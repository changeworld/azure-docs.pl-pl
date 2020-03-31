---
title: Planowanie i wykonywanie z fabryką danych
description: Poznaj aspekty planowania i wykonywania modelu aplikacji usługi Azure Data Factory.
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
ms.openlocfilehash: 15a2d6ae5d8b80468ffcdd00d60b1f36843ed677
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281069"
---
# <a name="data-factory-scheduling-and-execution"></a>Planowanie i wykonywanie fabryki danych
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [wykonanie potoku i wyzwalaczy](../concepts-pipeline-execution-triggers.md) artykułu.

W tym artykule wyjaśniono aspekty planowania i wykonywania modelu aplikacji usługi Azure Data Factory. W tym artykule przyjęto założenie, że rozumiesz podstawy koncepcji modelu aplikacji usługi Data Factory, w tym działania, potoki, połączone usługi i zestawy danych. Aby uzyskać podstawowe pojęcia usługi Azure Data Factory, zobacz następujące artykuły:

* [Wprowadzenie do usługi Data Factory](data-factory-introduction.md)
* [Potoki](data-factory-create-pipelines.md)
* [Zestawy danych](data-factory-create-datasets.md) 

## <a name="start-and-end-times-of-pipeline"></a>Godziny rozpoczęcia i zakończenia rurociągu
Potok jest aktywny tylko między jego czas **rozpoczęcia** i **zakończenia.** Nie jest wykonywany przed godziną rozpoczęcia lub po godzinie zakończenia. Jeśli potok jest wstrzymany, nie jest wykonywany niezależnie od jego czasu rozpoczęcia i zakończenia. Aby potok uruchomić, nie należy wstrzymać. Te ustawienia (start, end, wstrzymane) można znaleźć w definicji potoku: 

```json
"start": "2017-04-01T08:00:00Z",
"end": "2017-04-01T11:00:00Z"
"isPaused": false
```

Aby uzyskać więcej informacji te właściwości, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. 


## <a name="specify-schedule-for-an-activity"></a>Określanie harmonogramu działania
Nie jest to potok, który jest wykonywany. Jest to działania w potoku, które są wykonywane w ogólnym kontekście potoku. Harmonogram cykliczny dla działania można określić za pomocą sekcji **harmonogram** działania JSON. Na przykład można zaplanować działanie do uruchamiania co godzinę w następujący sposób:  

```json
"scheduler": {
    "frequency": "Hour",
    "interval": 1
},
```

Jak pokazano na poniższym diagramie, określenie harmonogramu dla działania tworzy serię okien upadek z w potoku godzin rozpoczęcia i zakończenia. Okna tumbling to seria nienakładających się, ciągłych interwałów czasowych o stałym rozmiarze. Te logiczne okna tumbling dla działania są nazywane **okna działania**.

![Przykład harmonogramu działań](media/data-factory-scheduling-and-execution/scheduler-example.png)

Właściwość **harmonogramu** dla działania jest opcjonalna. Jeśli określisz tę właściwość, musi ona odpowiadać rytmowi określonemu w definicji wyjściowego zestawu danych dla działania. Obecnie harmonogram jest prowadzony przy użyciu wyjściowego zestawu danych. W związku z tym należy utworzyć wyjściowy zestaw danych, nawet jeśli działanie nie generuje żadnych danych wyjściowych. 

## <a name="specify-schedule-for-a-dataset"></a>Określanie harmonogramu dla zestawu danych
Działanie w potoku fabryki danych może przyjmować zero lub więcej wejściowych **zestawów danych** i tworzyć co najmniej jeden wyjściowy zestaw danych. Dla działania można określić rytm, w którym dane wejściowe są dostępne lub dane wyjściowe są tworzone przy użyciu sekcji **dostępności** w definicjach zestawu danych. 

**Częstotliwość** w sekcji **dostępności** określa jednostkę czasu. Dozwolone wartości częstotliwości to: Minuta, Godzina, Dzień, Tydzień i Miesiąc. Właściwość **interwału** w sekcji dostępności określa mnożnik częstotliwości. Na przykład: jeśli częstotliwość jest ustawiona na Dzień i interwał jest ustawiona na 1 dla wyjściowego zestawu danych, dane wyjściowe są tworzone codziennie. Jeśli określisz częstotliwość jako minutę, zaleca się ustawienie interwału na nie mniej niż 15. 

W poniższym przykładzie dane wejściowe są dostępne co godzinę,`"frequency": "Hour", "interval": 1`a dane wyjściowe są tworzone co godzinę ( ). 

**Wejściowy zestaw danych:** 

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```


**Wyjściowy zestaw danych**

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mypath/{Year}/{Month}/{Day}/{Hour}",
            "format": {
                "type": "TextFormat"
            },
            "partitionedBy": [
                { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
                { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" }}
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Obecnie **wyjściowy zestaw danych napędza harmonogram**. Innymi słowy harmonogram określony dla wyjściowego zestawu danych jest używany do uruchamiania działania w czasie wykonywania. W związku z tym należy utworzyć wyjściowy zestaw danych, nawet jeśli działanie nie generuje żadnych danych wyjściowych. Jeśli w działaniu nie są używane żadne dane wejściowe, możesz pominąć tworzenie zestawu danych wejściowych. 

W poniższej definicji potoku właściwość **harmonogramu** jest używana do określania harmonogramu działania. Ta właściwość jest opcjonalna. Obecnie harmonogram działania musi być zgodny z harmonogramem określonym dla wyjściowego zestawu danych.
 
```json
{
    "name": "SamplePipeline",
    "properties": {
        "description": "copy activity",
        "activities": [
            {
                "type": "Copy",
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 100000,
                        "writeBatchTimeout": "00:05:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureSQLInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
        ],
        "start": "2017-04-01T08:00:00Z",
        "end": "2017-04-01T11:00:00Z"
    }
}
```

W tym przykładzie działanie jest uruchamiane co godzinę między godzinami rozpoczęcia i zakończenia potoku. Dane wyjściowe są tworzone co godzinę dla trzygodzinnych okien (8:00 - 9:00, 9:00 - 10:00 i 10:00 - 11:00). 

Każda jednostka danych zużytych lub wyprodukowanych przez przebieg działania jest nazywana **plasterkiem danych**. Na poniższym diagramie przedstawiono przykład działania z jednym wejściowym zestawem danych i jednym wyjściowym zestawem danych: 

![Harmonogram dostępności](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

Diagram przedstawia godzinowe wycinki danych dla zestawu danych wejściowych i wyjściowych. Diagram przedstawia trzy wycinki wejściowe, które są gotowe do przetworzenia. Działanie 10-11 AM jest w toku, produkujące plasterek wyjściowy 10-11 AM. 

Dostęp do interwału czasu skojarzonego z bieżącym plasterkiem w zestawie danych JSON można uzyskać przy użyciu zmiennych: [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) i [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables). Podobnie można uzyskać dostęp do przedziału czasu skojarzonego z oknem działania przy użyciu WindowStart i WindowEnd. Harmonogram działania musi być zgodny z harmonogramem zestawu danych wyjściowych dla działania. W związku z tym SliceStart i SliceEnd wartości są takie same jak WindowStart i WindowEnd wartości odpowiednio. Aby uzyskać więcej informacji na temat tych zmiennych, zobacz [funkcje fabryki danych i artykuły zmiennych systemowych.](data-factory-functions-variables.md#data-factory-system-variables)  

Te zmienne można używać do różnych celów w działania JSON. Na przykład można ich używać do wybierania danych z wejściowych i wyjściowych zestawów danych reprezentujących dane szeregów czasowych (na przykład: 8:00 do 9:00). W tym przykładzie użyto również **WindowStart** i **WindowEnd,** aby wybrać odpowiednie dane dla działania uruchomić i skopiować go do obiektu blob z odpowiednim **folderPath**. Ścieżka **folderu** jest parametryzowana, aby mieć oddzielny folder dla każdej godziny.  

W poprzednim przykładzie harmonogram określony dla wejściowych i wyjściowych zestawów danych jest taki sam (co godzinę). Jeśli zestaw danych wejściowych dla działania jest dostępny z inną częstotliwością, powiedzmy co 15 minut, działanie, które tworzy ten zestaw danych wyjściowych nadal działa raz na godzinę, ponieważ zestaw danych wyjściowych jest tym, co napędza harmonogram działań. Aby uzyskać więcej informacji, zobacz [Modelowanie zestawów danych o różnych częstotliwościach](#model-datasets-with-different-frequencies).

## <a name="dataset-availability-and-policies"></a>Dostępność i zasady zestawu danych
W sekcji dostępności definicji zestawu danych zaobserwowano użycie właściwości częstotliwości i interwałów. Istnieje kilka innych właściwości, które wpływają na planowanie i wykonywanie działania. 

### <a name="dataset-availability"></a>Dostępność zestawu danych 
W poniższej tabeli opisano właściwości, których można użyć w sekcji **dostępności:**

| Właściwość | Opis | Wymagany | Domyślne |
| --- | --- | --- | --- |
| frequency |Określa jednostkę czasu produkcji plasterków zestawu danych.<br/><br/><b>Obsługiwana częstotliwość:</b>Minuta, Godzina, Dzień, Tydzień, Miesiąc |Tak |Nie dotyczy |
| interval |Określa mnożnik częstotliwości<br/><br/>"Interwał częstotliwości x" określa, jak często powstaje plasterek.<br/><br/>Jeśli potrzebny jest, aby zestaw danych był krojony co godzinę, należy ustawić <b>częstotliwość</b> na <b>godzinę</b>i <b>interwał</b> na <b>1</b>.<br/><br/><b>Uwaga:</b>Jeśli określisz częstotliwość jako minutę, zalecamy ustawienie interwału na nie mniej niż 15 |Tak |Nie dotyczy |
|  — styl |Określa, czy plasterek powinien być produkowany na początku/na końcu interwału.<ul><li>StartOfInterval (Początekinterwał)</li><li>KoniecInterwał</li></ul><br/><br/>Jeśli częstotliwość jest ustawiona na Miesiąc i styl jest ustawiona na EndOfInterval, plasterek jest produkowany w ostatnim dniu miesiąca. Jeśli styl jest ustawiony na StartOfInterval, plasterek jest produkowany w pierwszym dniu miesiąca.<br/><br/>Jeśli częstotliwość jest ustawiona na Dzień i styl jest ustawiona na EndOfInterval, plasterek jest produkowany w ostatniej godzinie dnia.<br/><br/>Jeśli częstotliwość jest ustawiona na Godzina i styl jest ustawiona na EndOfInterval, plasterek jest produkowany na koniec godziny. Na przykład dla plasterka dla okresu 1 PM - 14:00, plasterek jest produkowany o godzinie 14:00. |Nie |KoniecInterwał |
| anchorDateTime |Definiuje położenie bezwzględne w czasie używane przez harmonogram do obliczania granic plasterków zestawu danych. <br/><br/><b>Uwaga:</b>Jeśli AnchorDateTime ma części daty, które są bardziej szczegółowe niż częstotliwość, to bardziej szczegółowe części są ignorowane. <br/><br/>Na przykład, jeśli <b>interwał</b> jest <b>co godzinę</b> (częstotliwość: godzina i interwał: 1) i <b>AnchorDateTime</b> zawiera <b>minuty i sekundy</b>, a następnie <b>minut i sekund</b> części AnchorDateTime są ignorowane. |Nie |01/01/0001 |
| przesunięcie |Czas, o który są przesuwane początkowe i końcowe wszystkich wycinków zestawu danych. <br/><br/><b>Uwaga:</b>Jeśli określono zarówno anchorDateTime, jak i offset, wynikiem jest połączone przesunięcie. |Nie |Nie dotyczy |

### <a name="offset-example"></a>przykład przesunięcia
Domyślnie plasterki`"frequency": "Day", "interval": 1`dzienne ( ) rozpoczynają się o godzinie 12:00 czasu UTC (północ). Jeśli chcesz, aby czas rozpoczęcia był 6 AM CZASU UTC zamiast, należy ustawić przesunięcie, jak pokazano w następującym urywek: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>przykład anchorDateTime
W poniższym przykładzie zestaw danych jest produkowany raz na 23 godziny. Pierwszy plasterek rozpoczyna się w czasie określonym przez anchorDateTime, który jest ustawiony na `2017-04-19T08:00:00` (czas UTC).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>Przykład odsunięcia/stylu
Poniższy zestaw danych jest miesięcznym zbiorem danych i jest sporządzany 3 dnia`3.08:00:00`każdego miesiąca o godzinie 8:00 ( ):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

### <a name="dataset-policy"></a>Zasady zestawu danych
Zestaw danych może mieć zdefiniowane zasady sprawdzania poprawności, która określa, w jaki sposób dane generowane przez wykonanie plasterka mogą być sprawdzane, zanim będzie gotowy do użycia. W takich przypadkach po zakończeniu wykonywania plasterka stan plasterka jest zmieniany **na Oczekiwanie** z podstanem **sprawdzania poprawności**. Po sprawdzeniu poprawności plasterków stan plasterka zmienia się na **Gotowe**. Jeśli wycinek danych został wyprodukowany, ale nie przeszedł sprawdzania poprawności, działanie jest uruchamiane dla wycinków podrzędnych, które zależą od tego plasterka, nie są przetwarzane. [Monitorowanie potoków i zarządzanie nimi](data-factory-monitor-manage-pipelines.md) obejmuje różne stany wycinków danych w fabryce danych.

Sekcja **zasad** w definicji zestawu danych definiuje kryteria lub warunek, który muszą spełniać wycinki zestawu danych. W poniższej tabeli opisano właściwości, których można użyć w sekcji **zasad:**

| Policy Name (Nazwa zasad) | Opis | Stosowane do | Wymagany | Domyślne |
| --- | --- | --- | --- | --- |
| minimumSizeMB | Sprawdza, czy dane w **obiekcie blob platformy Azure** spełnia minimalne wymagania dotyczące rozmiaru (w megabajtach). |Obiekt bob Azure |Nie |Nie dotyczy |
| minimumRows | Sprawdza, czy dane w **bazie danych SQL platformy Azure** lub **tabeli platformy Azure** zawiera minimalną liczbę wierszy. |<ul><li>Azure SQL Database</li><li>Tabela platformy Azure</li></ul> |Nie |Nie dotyczy |

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

**minimumRows**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

Aby uzyskać więcej informacji na temat tych właściwości i przykładów, zobacz [Tworzenie zestawów danych](data-factory-create-datasets.md) artykułu. 

## <a name="activity-policies"></a>Zasady dotyczące działań
Zasady mają wpływ na zachowanie w czasie wykonywania działania, w szczególności podczas przetwarzania wycinka tabeli. Poniższa tabela zawiera szczegółowe informacje.

| Właściwość | Dozwolone wartości | Wartość domyślna | Opis |
| --- | --- | --- | --- |
| współbieżność |Liczba całkowita <br/><br/>Maksymalna wartość: 10 |1 |Liczba równoczesnych wykonań działania.<br/><br/>Określa liczbę równoległych wykonań działań, które mogą się zdarzyć na różnych plasterkach. Na przykład jeśli działanie musi przejść przez duży zestaw dostępnych danych, o większą wartość współbieżności przyspiesza przetwarzanie danych. |
| wykonaniePriorder |NajnowszeFirst<br/><br/>NajstarszyPierwszość |NajstarszyPierwszość |Określa kolejność wycinków danych, które są przetwarzane.<br/><br/>Na przykład jeśli masz 2 wycinki (jeden dzieje się o 16:00, a drugi o 5pm), a oba są oczekujące wykonanie. Jeśli ustawisz executionPriorityOrder na NewestFirst, plasterek o godzinie 17:00 zostanie przetworzony jako pierwszy. Podobnie jeśli ustawisz executionPriorityORder być OldestFIrst, a następnie plasterek na 4 PM jest przetwarzany. |
| retry |Liczba całkowita<br/><br/>Maksymalna wartość może wynosić 10 |0 |Liczba ponownych prób przed przetwarzaniem danych dla plasterka jest oznaczona jako Błąd. Wykonanie działania dla plasterka danych jest ponowione do określonej liczby ponownych prób. Ponowna próby odbywa się tak szybko, jak to możliwe po awarii. |
| timeout |przedział_czasu |00:00:00 |Limit czasu dla działania. Przykład: 00:10:00 (oznacza przesuń czas 10 minut)<br/><br/>Jeśli wartość nie jest określony lub jest 0, limit czasu jest nieskończony.<br/><br/>Jeśli czas przetwarzania danych w plasterku przekracza wartość limitu czasu, jest anulowany, a system próbuje ponowić próbę przetworzenia. Liczba ponownych prób zależy od właściwości ponawiania próby. Po wystąpieniu limitu czasu stan jest ustawiony na TimedOut. |
| Opóźnienie |przedział_czasu |00:00:00 |Określ opóźnienie przed rozpoczęciem przetwarzania danych plasterka.<br/><br/>Wykonywanie działania dla plasterka danych jest uruchamiany po Delay jest przeszłości oczekiwany czas wykonywania.<br/><br/>Przykład: 00:10:00 (oznacza opóźnienie 10 min) |
| longRetry (longRetry) |Liczba całkowita<br/><br/>Maksymalna wartość: 10 |1 |Liczba długich ponownych prób, zanim wykonanie plasterka nie powiedzie się.<br/><br/>longRetry próby są rozmieszczone przez longRetryInterval. Więc jeśli trzeba określić czas między próbami ponawiania, należy użyć longRetry. Jeśli określono zarówno ponów próbę, jak i longRetry, każda próba longRetry zawiera próby ponawiania, a maksymalna liczba prób to Ponów próbę * longRetry.<br/><br/>Na przykład, jeśli w zasadach działania mamy następujące ustawienia:<br/>Ponów próbę: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Załóżmy, że istnieje tylko jeden plasterek do wykonania (stan jest oczekiwanie) i wykonanie działania kończy się niepowodzeniem za każdym razem. Początkowo będą 3 kolejne próby wykonania. Po każdej próbie stan plasterka będzie ponów próbę. Po pierwszych 3 próby są skończą, stan plasterka będzie LongRetry.<br/><br/>Po godzinie (czyli longRetryInteval wartość), nie będzie inny zestaw 3 kolejnych prób wykonania. Po tym stan plasterka będzie niepowodzeniem i nie więcej ponownych prób będzie podejmowana. W związku z tym w sumie podjęto 6 prób.<br/><br/>Jeśli wykonanie zakończy się pomyślnie, stan plasterka będzie gotowy i nie więcej ponownych prób są podejmowane.<br/><br/>longRetry mogą być używane w sytuacjach, gdy dane zależne docierają w czasie niedeterministycznym lub ogólne środowisko jest łuszczące się, w którym następuje przetwarzanie danych. W takich przypadkach robi ponownych prób jeden po drugim może nie pomóc i robi to po interwał czasu powoduje żądane dane wyjściowe.<br/><br/>Słowo ostrzeżenia: nie należy ustawiać wysokie wartości longRetry lub longRetryInterval. Zazwyczaj wyższe wartości oznaczają inne problemy systemowe. |
| longRetryInterval |przedział_czasu |00:00:00 |Opóźnienie między długimi próbami ponowiania prób |

Aby uzyskać więcej informacji, zobacz [pipelines](data-factory-create-pipelines.md) artykułu. 

## <a name="parallel-processing-of-data-slices"></a>Równoległe przetwarzanie wycinków danych
Można ustawić datę rozpoczęcia potoku w przeszłości. Po wykonaniu tej te usługi usługa Data Factory automatycznie oblicza (wypełnia wstecz) wszystkie wycinki danych w przeszłości i rozpoczyna ich przetwarzanie. Na przykład: jeśli utworzysz potok z datą rozpoczęcia 2017-04-01, a bieżącą datą jest 2017-04-10. Jeśli rytm wyjściowego zestawu danych jest codziennie, usługa Data Factory rozpoczyna przetwarzanie wszystkich wycinków z 2017-04-01 do 2017-04-09 natychmiast, ponieważ data rozpoczęcia jest w przeszłości. Plasterek z 2017-04-10 nie jest jeszcze przetwarzany, ponieważ wartość właściwości style w sekcji dostępności jest EndOfInterval domyślnie. Najstarszy plasterek jest przetwarzany jako domyślna wartość executionPriorityOrder jest OldestFirst. Aby uzyskać opis właściwości style, zobacz [sekcję dostępności zestawu danych.](#dataset-availability) Aby uzyskać opis executionPriorityOrder sekcji, zobacz sekcję [zasad działania.](#activity-policies) 

Można skonfigurować wycinki danych wypełnione z tyłu do przetwarzania równolegle, ustawiając właściwość **współbieżności** w sekcji **zasad** działania JSON. Ta właściwość określa liczbę równoległych wykonań działań, które mogą się zdarzyć na różnych plasterkach. Wartość domyślna właściwości współbieżności wynosi 1. W związku z tym jeden plasterek jest przetwarzany w czasie domyślnie. Maksymalna wartość wynosi 10. Gdy potok musi przejść przez duży zestaw dostępnych danych, o większą wartość współbieżności przyspiesza przetwarzanie danych. 

## <a name="rerun-a-failed-data-slice"></a>Ponowne przygodnie fragmentu danych, który nie powiódł się,
Gdy wystąpi błąd podczas przetwarzania plasterka danych, można dowiedzieć się, dlaczego przetwarzanie plasterka nie powiodło się przy użyciu bloków portalu Azure lub monitoruj i zarządzaj aplikacją. Zobacz [monitorowanie i zarządzanie potokami przy użyciu bloków witryny Azure portal lub](data-factory-monitor-manage-pipelines.md) aplikacji Monitorowania i [zarządzania,](data-factory-monitor-manage-app.md) aby uzyskać szczegółowe informacje.

Rozważmy poniższy przykład, który pokazuje dwa działania. Aktywność1 i Działanie 2. Działanie1 zużywa wycinek zestawu danych1 i tworzy wycinek zestawu danych2, który jest zużywany jako dane wejściowe przez Activity2 do tworzenia wycinka zestawu danych końcowych.

![Plasterek nie powiódł się](./media/data-factory-scheduling-and-execution/failed-slice.png)

Diagram pokazuje, że z trzech ostatnich wycinków wystąpił błąd produkcji 9-10 AM plasterek dla zestawu danych2. Usługa Data Factory automatycznie śledzi zależność dla zestawu danych szeregów czasowych. W rezultacie nie uruchamia się działania dla 9-10 AM wycinek niższego rzędu.

Narzędzia do monitorowania i zarządzania fabryki danych umożliwiają przechodzenie do szczegółów dzienników diagnostycznych dla nieudanego plasterka, aby łatwo znaleźć główną przyczynę problemu i rozwiązać go. Po naprawieniu problemu można łatwo uruchomić działanie, aby utworzyć nieudany plasterek. Aby uzyskać więcej informacji na temat ponownego uruchomienia i zrozumienia przejść stanu dla plasterków danych, zobacz [Monitorowanie i zarządzanie rurociągami przy użyciu bloków portalu Azure](data-factory-monitor-manage-pipelines.md) lub aplikacji Monitorowania i [zarządzania](data-factory-monitor-manage-app.md).

Po ponownym uruchomieniu plasterka 9-10 AM dla **zestawu danych2,** fabryka danych rozpoczyna uruchamianie dla wycinka zależnego od 9-10 AM od końcowego zestawu danych.

![Ponowne uruchomienie fragmentu nie powiodło się](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="multiple-activities-in-a-pipeline"></a>Wiele działań w potoku
Potok może obejmować więcej niż jedno działanie. Jeśli masz wiele działań w potoku i dane wyjściowe działania nie jest dane wejściowe innego działania, działania mogą działać równolegle, jeśli wycinki danych wejściowych dla działań są gotowe.

Dwa działania można połączyć w łańcuch (uruchomić jedno działanie po drugim), ustawiając wyjściowy zestaw danych jednego działania jako zestaw wejściowy drugiego. Działania mogą być w tym samym potoku lub w różnych potoków. Drugie działanie jest wykonywane tylko wtedy, gdy pierwsze zakończy się pomyślnie.

Rozważmy na przykład następujący przypadek, w którym potok ma dwa działania:

1. Działanie A1, które wymaga zewnętrznego wejściowego zestawu danych D1 i tworzy wyjściowy zestaw danych D2.
2. Działanie A2, które wymaga danych wejściowych z zestawu danych D2 i tworzy wyjściowy zestaw danych D3.

W tym scenariuszu działania A1 i A2 są w tym samym potoku. Działanie A1 jest uruchamiane, gdy dane zewnętrzne są dostępne i zostanie osiągnięta zaplanowana częstotliwość dostępności. Działanie A2 jest uruchamiane po udostępnieniu zaplanowanych wycinków z D2 i osiągnięciu zaplanowanej częstotliwości dostępności. Jeśli w jednym z wycinków w zestawie danych D2 występuje błąd, A2 nie jest uruchamiany dla tego plasterka, dopóki nie stanie się dostępny.

Widok diagramu z obu działań w tym samym potoku będzie wyglądać następująco:

![Tworzenie łańcucha działań w tym samym potoku](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

Jak wspomniano wcześniej, działania mogą być w różnych rurociągów. W takim scenariuszu widok diagramu będzie wyglądać następująco:

![Łączenie działań w dwóch rurociągach](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

Zobacz kopię sekwencyjnie sekcji w dodatku na przykład.

## <a name="model-datasets-with-different-frequencies"></a>Modele zestawów danych o różnych częstotliwościach
W przykładach częstotliwości dla wejściowych i wyjściowych zestawów danych oraz okna harmonogramu działań były takie same. Niektóre scenariusze wymagają możliwości wytwarzania danych wyjściowych z częstotliwością inną niż częstotliwości jednego lub więcej wejść. Usługa Data Factory obsługuje modelowanie tych scenariuszy.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>Przykład 1: Tworzenie dziennego raportu wyjściowego dla danych wejściowych, które są dostępne co godzinę
Należy wziąć pod uwagę scenariusz, w którym masz dane pomiarowe wejściowe z czujników dostępnych co godzinę w magazynie obiektów Blob platformy Azure. Chcesz utworzyć dzienny raport zbiorczy ze statystykami, takimi jak średnia, maksymalna i minimalna dla dnia z [aktywnością ule fabryki danych](data-factory-hive-activity.md).

Oto jak można modelować ten scenariusz za pomocą usługi Data Factory:

**Wejściowy zestaw danych**

Pliki wejściowe godzinowe są usuwane w folderze dla danego dnia. Dostępność wejścia jest **ustawiona** na godzinę (częstotliwość: godzina, interwał: 1).

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Wyjściowy zestaw danych**

Jeden plik wyjściowy jest tworzony codziennie w folderze dnia. Dostępność danych wyjściowych jest ustawiana w **dzień** (częstotliwość: Dzień i interwał: 1).

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Działanie: aktywność gałęzi w potoku**

Skrypt gałęzi odbiera odpowiednie informacje *DateTime* jako parametry, które używają zmiennej **WindowStart,** jak pokazano w poniższym urywek. Skrypt gałęzi używa tej zmiennej, aby załadować dane z właściwego folderu dla dnia i uruchomić agregację w celu wygenerowania danych wyjściowych.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
        {
            "name": "SampleHiveActivity",
            "inputs": [
                {
                    "name": "AzureBlobInput"
                }
            ],
            "outputs": [
                {
                    "name": "AzureBlobOutput"
                }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "adftutorial\\hivequery.hql",
                "scriptLinkedService": "StorageLinkedService",
                "defines": {
                    "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
                    "Month": "$$Text.Format('{0:MM}',WindowStart)",
                    "Day": "$$Text.Format('{0:dd}',WindowStart)"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },            
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 2,
                "timeout": "01:00:00"
            }
         }
     ]
   }
}
```

Na poniższym diagramie przedstawiono scenariusz z punktu widzenia zależności danych.

![Zależność danych](./media/data-factory-scheduling-and-execution/data-dependency.png)

Plasterek wyjściowy na każdy dzień zależy od 24-godzinnych plasterków z wejściowego zestawu danych. Data Factory oblicza te zależności automatycznie, ustalając wycinki danych wejściowych, które spadają w tym samym okresie co plasterek danych wyjściowych, które mają zostać wyprodukowane. Jeśli którykolwiek z 24 wycinków wejściowych nie jest dostępny, usługa Data Factory czeka na gotowość wycinka wejściowego przed rozpoczęciem codziennego uruchamiania aktywności.

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>Przykład 2: Określanie zależności za pomocą wyrażeń i funkcji fabryka danych
Rozważmy inny scenariusz. Załóżmy, że masz działanie gałęzi, który przetwarza dwa wejściowe zestawy danych. Jeden z nich ma nowe dane codziennie, ale jeden z nich otrzymuje nowe dane co tydzień. Załóżmy, że chcesz wykonać sprzężenie przez dwa wejścia i produkować dane wyjściowe codziennie.

Proste podejście, w którym usługa Data Factory automatycznie wylicza odpowiednie wycinki wejściowe do przetworzenia, dostosowując się do okresu czasu plasterka danych wyjściowych, nie działa.

Należy określić, że dla każdego uruchomienia działania fabryka danych powinna używać wycinka danych z zeszłego tygodnia dla tygodniowego zestawu danych wejściowych. Aby zaimplementować to zachowanie, należy użyć funkcji usługi Azure Data Factory, jak pokazano w poniższym urywek.

**Dane wejściowe1: obiekt blob platformy Azure**

Pierwsze dane wejściowe to obiekt blob platformy Azure aktualizowany codziennie.

```json
{
  "name": "AzureBlobInputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Input2: Obiekt blob platformy Azure**

Input2 jest obiektem blob platformy Azure aktualizowanym co tydzień.

```json
{
  "name": "AzureBlobInputWeekly",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 7
    }
  }
}
```

**Dane wyjściowe: obiekt blob platformy Azure**

Jeden plik wyjściowy jest tworzony codziennie w folderze dla dnia. Dostępność danych wyjściowych jest ustawiona na **dzień** (częstotliwość: Dzień, interwał: 1).

```json
{
  "name": "AzureBlobOutputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Działanie: aktywność gałęzi w potoku**

Działanie gałęzi przyjmuje dwa dane wejściowe i produkuje wycinek danych wyjściowych każdego dnia. Można określić każdy wycinek wyjściowy każdego dnia, aby zależeć od wycinka wejściowego z poprzedniego tygodnia dla danych wejściowych tygodniowych w następujący sposób.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
      {
        "name": "SampleHiveActivity",
        "inputs": [
          {
            "name": "AzureBlobInputDaily"
          },
          {
            "name": "AzureBlobInputWeekly",
            "startTime": "Date.AddDays(SliceStart, - Date.DayOfWeek(SliceStart))",
            "endTime": "Date.AddDays(SliceEnd,  -Date.DayOfWeek(SliceEnd))"  
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutputDaily"
          }
        ],
        "linkedServiceName": "HDInsightLinkedService",
        "type": "HDInsightHive",
        "typeProperties": {
          "scriptPath": "adftutorial\\hivequery.hql",
          "scriptLinkedService": "StorageLinkedService",
          "defines": {
            "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
            "Month": "$$Text.Format('{0:MM}',WindowStart)",
            "Day": "$$Text.Format('{0:dd}',WindowStart)"
          }
        },
        "scheduler": {
          "frequency": "Day",
          "interval": 1
        },            
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 2,  
          "timeout": "01:00:00"
        }
       }
     ]
   }
}
```

Zobacz [Funkcje fabryki danych i zmienne systemowe,](data-factory-functions-variables.md) aby uzyskać listę funkcji i zmiennych systemowych, które obsługuje usługa Data Factory.

## <a name="appendix"></a>Dodatek

### <a name="example-copy-sequentially"></a>Przykład: kopiowanie sekwencyjnie
Istnieje możliwość uruchamiania wielu operacji kopiowania jeden po drugim w sposób sekwencyjny/uporządkowany. Na przykład może mieć dwa działania kopiowania w potoku (CopyActivity1 i CopyActivity2) z następujących danych wejściowych wyjściowych zestawów danych:   

CopyActivity1

Dane wejściowe: zestaw danych. Dane wyjściowe: Zestaw danych2.

CopyActivity2

Dane wejściowe: Zestaw danych2.  Dane wyjściowe: Zestaw danych3.

CopyActivity2 będzie działać tylko wtedy, gdy CopyActivity1 został uruchomiony pomyślnie i Dataset2 jest dostępny.

Oto przykładowy potok JSON:

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob1ToBlob2",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset3"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob2ToBlob3",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2016-08-25T01:00:00Z",
        "end": "2016-08-25T01:00:00Z",
        "isPaused": false
    }
}
```

Należy zauważyć, że w przykładzie wyjściowy zestaw danych pierwszego działania kopiowania (Dataset2) jest określony jako dane wejściowe dla drugiego działania. W związku z tym drugie działanie jest uruchamiane tylko wtedy, gdy wyjściowy zestaw danych z pierwszego działania jest gotowy.  

W przykładzie CopyActivity2 może mieć inne dane wejściowe, takie jak Dataset3, ale określisz Dataset2 jako dane wejściowe do CopyActivity2, więc działanie nie zostanie uruchomione, dopóki copyactivity1 nie zostanie wykońcą. Przykład:

CopyActivity1

Dane wejściowe: Zestaw danych1. Dane wyjściowe: Zestaw danych2.

CopyActivity2

Dane wejściowe: Dataset3, Dataset2. Dane wyjściowe: Zestaw danych4.

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlobToBlob",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset3"
                    },
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset4"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob3ToBlob4",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2017-04-25T01:00:00Z",
        "end": "2017-04-25T01:00:00Z",
        "isPaused": false
    }
}
```

Należy zauważyć, że w przykładzie dwa zestawy danych wejściowych są określone dla drugiego działania kopiowania. Po określeniu wielu danych wejściowych do kopiowania danych jest używany tylko pierwszy zestaw danych wejściowych, ale inne zestawy danych są używane jako zależności. CopyActivity2 rozpocznie się dopiero po spełnieniu następujących warunków:

* CopyActivity1 została pomyślnie ukończona i dataset2 jest dostępny. Ten zestaw danych nie jest używany podczas kopiowania danych do zestawu danych4. Działa tylko jako zależność planowania copyactivity2.   
* Dostępny jest zestaw danych3. Ten zestaw danych reprezentuje dane, które są kopiowane do miejsca docelowego. 
