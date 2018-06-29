---
title: Planowanie i wykonywanie przy użyciu fabryki danych | Dokumentacja firmy Microsoft
description: Dowiedz się, planowania i wykonywania aspektów modelu aplikacji fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 088a83df-4d1b-4ac1-afb3-0787a9bd1ca5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: bd8b682e073e86bb824d31d6ebab20a80f807730
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054606"
---
# <a name="data-factory-scheduling-and-execution"></a>Planowanie fabryki danych i wykonywania
> [!NOTE]
> Ten artykuł dotyczy wersji 1 fabryki danych. Jeśli używasz bieżącą wersję usługi fabryka danych, zobacz [potoku wykonywania i wyzwalaczy](../concepts-pipeline-execution-triggers.md) artykułu.

W tym artykule wyjaśniono aspekty planowania i wykonywania modelu aplikacji usługi Azure Data Factory. W tym artykule przyjęto założenie, że rozumiesz podstawowe pojęcia modelu aplikacji fabryki danych, w tym działania, potoki połączonych usług i zestawów danych. Dla podstawowych pojęciach dotyczących usługi fabryka danych Azure zobacz następujące artykuły:

* [Wprowadzenie do fabryki danych](data-factory-introduction.md)
* [Potoki](data-factory-create-pipelines.md)
* [Zestawy danych](data-factory-create-datasets.md) 

## <a name="start-and-end-times-of-pipeline"></a>Czas rozpoczęcia i zakończenia potoku
Potok jest aktywna tylko między jego **start** czasu i **zakończenia** czasu. Nie jest wykonywany przed godziną rozpoczęcia lub późniejsza niż godzina zakończenia. Jeśli potoku jest wstrzymana, nie została wykonana niezależnie od jego czas rozpoczęcia i zakończenia. Dla potoku do uruchamiania jego powinien nie można wstrzymać. Te ustawienia (start, koniec wstrzymana) można znaleźć w definicji potoku: 

```json
"start": "2017-04-01T08:00:00Z",
"end": "2017-04-01T11:00:00Z"
"isPaused": false
```

Aby uzyskać więcej informacji zobacz te właściwości [tworzenie potoków](data-factory-create-pipelines.md) artykułu. 


## <a name="specify-schedule-for-an-activity"></a>Określ harmonogram dla działania
Nie jest potok, który jest wykonywany. Jest działania w potoku, które są wykonywane w ramach ogólnej potoku. Można określić harmonogramu cyklicznego dla działania za pomocą **harmonogramu** sekcji aktywność JSON. Na przykład można zaplanować działania, aby uruchamiać co godzinę w następujący sposób:  

```json
"scheduler": {
    "frequency": "Hour",
    "interval": 1
},
```

Jak pokazano na poniższym diagramie, określanie harmonogramu dla działania tworzy serię wirowania w potoku start systemu windows z godziny i zakończenia. Windows wirowania są szereg przedziały czasu nienakładający, ciągły stałym rozmiarze. Te okna wirowania logiczne dla działania są nazywane **okien działania**.

![Przykład harmonogram działania](media/data-factory-scheduling-and-execution/scheduler-example.png)

**Harmonogramu** właściwość dla działania jest opcjonalna. Jeśli określisz tej właściwości musi być zgodna okresach, które określisz w definicji zestawu danych wyjściowych dla działania. Obecnie harmonogram jest prowadzony przy użyciu wyjściowego zestawu danych. Dlatego należy utworzyć wyjściowy zestaw danych, nawet wtedy, gdy działanie nie generuje żadnego wyniku. 

## <a name="specify-schedule-for-a-dataset"></a>Określ harmonogram dla zestawu danych
Działania w potoku fabryki danych może zająć zero lub więcej danych wejściowych **zestawów danych** i tworzące co najmniej jeden wyjściowy zestaw danych. Dla działania można określić okresach, w którym dane wejściowe są dostępne lub danych wyjściowych jest generowany przy użyciu **dostępności** sekcji w definicji zestawu danych. 

**Częstotliwość** w **dostępności** sekcja określa jednostkę czasu. Dozwolone wartości częstotliwości: minuty, godziny, dnia, tygodnia i miesiąca. **Interwał** właściwości w sekcji dostępności Określa mnożnik częstotliwości. Na przykład: Jeśli ustawiono częstotliwość do dnia i interwał ma wartość 1 dla wyjściowego zestawu danych, danych wyjściowych jest tworzony codziennie. Jeśli określisz częstotliwość co minutę, zaleca się ustawić interwał wynoszący nie mniej niż 15. 

W poniższym przykładzie danych wejściowych jest dostępna co godzinę i danych wyjściowych jest tworzone co godzinę (`"frequency": "Hour", "interval": 1`). 

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

Obecnie **wyjściowy zestaw danych dyski harmonogram**. Innymi słowy harmonogram określony dla wyjściowego zestawu danych jest używany do uruchomienia działania w czasie wykonywania. Dlatego należy utworzyć wyjściowy zestaw danych, nawet wtedy, gdy działanie nie generuje żadnego wyniku. Jeśli w działaniu nie są używane żadne dane wejściowe, możesz pominąć tworzenie zestawu danych wejściowych. 

W następującej definicji potoku **harmonogramu** właściwość jest używana, aby określić harmonogram działania. Ta właściwość jest opcjonalna. Obecnie harmonogram działania muszą być zgodne z harmonogramem określonym dla wyjściowego zestawu danych.
 
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

W tym przykładzie działanie uruchamia się co godzinę między godziny rozpoczęcia i zakończenia potoku. Dla windows trzech godzin (8 AM - 9 AM, 9 AM - 10, a 10 AM - 11: 00) danych wyjściowych jest tworzony co godzinę. 

Nosi nazwę każdej jednostki danych używane lub generowane przez działanie Uruchom **wycinka danych**. Na poniższym diagramie przedstawiono przykład działania o jeden wejściowy zestaw danych i jeden wyjściowy zestaw danych: 

![Harmonogram dostępności](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

Na diagramie przedstawiono co godzinę wycinków danych dla zestawu danych wejściowych i wyjściowych. Na diagramie przedstawiono trzy wycinków wejściowych, które są gotowe do przetworzenia. Działanie AM 10-11 jest w toku, produkujących AM 10-11 wycinek danych wyjściowych. 

Dostęp można uzyskać przedział czasu skojarzone z bieżącym wycinka w zestawie danych JSON przy użyciu zmiennych: [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) i [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables). Podobnie można uzyskać dostępu do przedział czasu skojarzony z oknem działania przy użyciu WindowStart i WindowEnd. Harmonogram działania muszą być zgodne harmonogram wyjściowy zestaw danych działania. W związku z tym SliceStart i SliceEnd wartości są takie same jak wartości WindowStart i WindowEnd odpowiednio. Aby uzyskać więcej informacji o tych zmiennych, zobacz [funkcje fabryki danych i zmienne systemu](data-factory-functions-variables.md#data-factory-system-variables) artykułów.  

Te zmienne można użyć do różnych celów w Twoich działaniach w formacie JSON. Na przykład można ich wybierać danych wejściowych i wyjściowych zestawów danych reprezentujący dane serii czas (na przykład: 8 AM do 9 AM). W tym przykładzie również używane **WindowStart** i **WindowEnd** do wybrania odpowiednich danych dla działania uruchamiania i skopiować go do obiektu blob z odpowiednią **folderPath**. **FolderPath** jest sparametryzowana mieć osobny folder dla każdej godziny.  

W powyższym przykładzie harmonogram określony dla danych wejściowych i wyjściowe zestawy danych jest tym samym (co godzinę). Jeśli wejściowy zestaw danych działania jest dostępny w innej częstotliwości, powiedz co 15 minut, działania, który spowoduje utworzenie tego zestawu danych wyjściowych nadal działa godzinę jako wyjściowego zestawu danych jest podstawę harmonogram działania. Aby uzyskać więcej informacji, zobacz [modelu zestawów danych z różnych częstotliwości](#model-datasets-with-different-frequencies).

## <a name="dataset-availability-and-policies"></a>Dostępność zestawu danych i zasady
Jak już wspomniano użycie częstotliwość i interwał właściwości w sekcji dostępności definicji zestawu danych. Istnieje kilka właściwości, które mają wpływ na planowanie i wykonania działania. 

### <a name="dataset-availability"></a>Dostępność zestawu danych 
W poniższej tabeli opisano właściwości można używać w **dostępności** sekcji:

| Właściwość | Opis | Wymagane | Domyślne |
| --- | --- | --- | --- |
| frequency |Określa jednostkę czasu dla trybu produkcyjnego wycinek zestawu danych.<br/><br/><b>Obsługiwana częstotliwość</b>: minuty, godziny, dnia, tygodnia, miesiąca |Yes |Nie dotyczy |
| interval |Określa mnożnik częstotliwości<br/><br/>"Interwał częstotliwości x" Określa, jak często jest tworzony wycinek.<br/><br/>Zestaw danych, aby zostać podzielona na godzinę, należy ustawić <b>częstotliwość</b> do <b>godzina</b>, i <b>interwał</b> do <b>1</b>.<br/><br/><b>Uwaga</b>: Jeśli zostanie określona częstotliwość jako minutę, zaleca się ustawić interwał wynoszący nie mniej niż 15 |Yes |Nie dotyczy |
| Styl |Określa, czy wycinek będą tworzone na początku/końca zakresu.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Jeśli częstotliwość ma ustawioną wartość miesiąca i styl ma ustawioną wartość EndOfInterval, wycinek jest realizowane ostatniego dnia miesiąca. Jeżeli styl jest ustawiony na StartOfInterval, wycinek jest generowany na pierwszy dzień miesiąca.<br/><br/>Jeśli ustawiono częstotliwość do dnia i styl ma ustawioną wartość EndOfInterval, wycinka jest realizowane w ciągu ostatniej godziny dnia.<br/><br/>Jeśli częstotliwość wynosi godzinę i styl ma ustawioną wartość EndOfInterval, wycinek jest generowany na koniec godziny. Na przykład dla wycinka okres 13: 00 – 14: 00, wycinek jest generowany na 14: 00. |Nie |EndOfInterval |
| anchorDateTime |Definiuje położenie bezwzględne w czasie używanych przez harmonogram do obliczenia granice wycinek zestawu danych. <br/><br/><b>Uwaga</b>: Jeśli AnchorDateTime ma części daty, które są bardziej szczegółowego niż wartość częstotliwości, bardziej szczegółowego części są ignorowane. <br/><br/>Na przykład jeśli <b>interwał</b> jest <b>co godzinę</b> (częstotliwość: godzinę i interwał: 1) i <b>AnchorDateTime</b> zawiera <b>minut i sekund</b>, a następnie <b>minut i sekund</b> części AnchorDateTime są ignorowane. |Nie |01/01/0001 |
| Przesunięcie |Zakres czasu za pomocą której zostaną przesunięte początku i końcu wszystkie fragmenty zestawu danych. <br/><br/><b>Uwaga</b>: Jeśli jest określony zarówno anchorDateTime, jak i przesunięcie wynik jest połączonych shift. |Nie |Nie dotyczy |

### <a name="offset-example"></a>przykład przesunięcia
Domyślnie codziennie (`"frequency": "Day", "interval": 1`) wycinków uruchomione w czasie UTC 00: 00 (północ). Czas rozpoczęcia jako czas UTC 6: 00, zamiast tego należy ustawić przesunięcie pokazane na następujący fragment kodu: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>przykład anchorDateTime
W poniższym przykładzie zestawu danych jest tworzony co 23 godz. Pierwszy wycinek rozpoczyna się w czasie określonym przez anchorDateTime, która jest ustawiona na `2017-04-19T08:00:00` (czas UTC).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>Przesunięcie i stylu przykład
Następujący zestaw danych jest miesięczne zestawu danych i jest generowany na 3rd każdego miesiąca o godzinie 8:00 AM (`3.08:00:00`):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

### <a name="dataset-policy"></a>Zestaw danych zasad
Zestaw danych może mieć zdefiniowane zasady sprawdzania poprawności określający, jak dane generowane przez wykonanie wycinka można zweryfikować przed jest gotowy do użycia. W takich przypadkach po zakończeniu wykonywania, wycinka stanu wycinka danych wyjściowych jest zmieniana na **oczekiwania** z podstanu z **weryfikacji**. Po zweryfikowaniu wycinków wycinek stan zmienia się na **gotowe**. Jeśli wycinek danych został utworzony, ale nie przeszedł sprawdzania poprawności, nie są przetwarzane uruchomień działania podrzędne wycinki, które są zależne od tego wycinka. [Monitorowanie i zarządzanie nimi potoki](data-factory-monitor-manage-pipelines.md) obejmuje różne stany wycinków danych z fabryki danych.

**Zasad** sekcja w definicji zestawu danych definiuje kryteria i warunków, które należy spełnić wycinków zestaw danych. W poniższej tabeli opisano właściwości można używać w **zasad** sekcji:

| Nazwa zasad | Opis | Dotyczy | Wymagane | Domyślne |
| --- | --- | --- | --- | --- |
| minimumSizeMB | Sprawdza, czy dane w **obiektów blob platformy Azure** spełnia wymagania minimalny rozmiar (w megabajtach). |Obiekt bob Azure |Nie |Nie dotyczy |
| minimumRows | Sprawdza, czy dane w **bazy danych Azure SQL** lub **tabeli platformy Azure** zawiera minimalną liczbę wierszy. |<ul><li>Azure SQL Database</li><li>Tabela platformy Azure</li></ul> |Nie |Nie dotyczy |

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

Aby uzyskać więcej informacji na temat tych właściwości i przykłady, zobacz [utworzyć zestawy danych](data-factory-create-datasets.md) artykułu. 

## <a name="activity-policies"></a>Zasady dotyczące działań
Zasady wpływają na zachowanie czasu wykonania działania, w szczególności, podczas przetwarzania wycinka tabeli. Poniższa tabela zawiera szczegółowe informacje.

| Właściwość | Dozwolone wartości | Wartość domyślna | Opis |
| --- | --- | --- | --- |
| Współbieżność |Liczba całkowita <br/><br/>Wartość maksymalna: 10 |1 |Liczba równoczesnych wykonania działania.<br/><br/>Określa Liczba wykonań działania równoległego, które mogą wystąpić na różnych wycinków. Na przykład jeśli działanie musi przechodzić przez duży zbiór dostępnych danych o większej wartości współbieżności przyspiesza przetwarzania danych. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Określa kolejność wycinki danych, które są przetwarzane.<br/><br/>Na przykład jeśli masz wycinków 2 (w toku co 4 godziny, a innym godzinie 5), a oba oczekują na wykonanie. Jeśli ustawisz executionPriorityOrder jako NewestFirst wycinka godzinie 5 jest przetwarzana najpierw. Podobnie jeśli ustawisz executionPriorityORder jako OldestFIrst wycinka godzinie 4 jest przetwarzany. |
| retry |Liczba całkowita<br/><br/>Maksymalna wartość może być 10 |0 |Liczba ponownych prób przed przetwarzania danych dla wycinka jest oznaczony jako błąd. Do określonego ponownych próba zostanie ponowiona wykonania działania dla wycinka danych. Ponów próbę odbywa się jak najszybciej po awarii. |
| timeout |Zakres czasu |00:00:00 |Limit czasu działania. Przykład: 00:10:00 (oznacza limitu 10 minut)<br/><br/>Jeśli wartość nie została określona lub jest równa 0, limit czasu to nieskończoność.<br/><br/>Jeśli czas przetwarzania danych na wycinek przekracza wartość limitu czasu, anulowaniu, a system podejmuje próbę przetwarzania. Liczba ponownych prób zależy od właściwości ponów próbę. W przypadku przekroczenia limitu czasu stan jest ustawiony na upłynął limit czasu. |
| Opóźnienie |Zakres czasu |00:00:00 |Określ opóźnienie przetwarzania danych powoduje uruchomienie wycinka.<br/><br/>Wykonanie działań dotyczących wycinek danych jest uruchomiona po opóźnienie oczekiwany czas wykonywania.<br/><br/>Przykład: 00:10:00 (oznacza opóźnienia w ciągu 10 minut) |
| Parametr longRetry |Liczba całkowita<br/><br/>Wartość maksymalna: 10 |1 |Liczba długa ponownych prób przed wycinek wykonanie nie powiodło się.<br/><br/>Parametr longRetry prób uzyskają przez longRetryInterval. Dlatego jeśli trzeba określić czas między ponownymi próbami, użyj longRetry. Jeśli został określony zarówno longRetry, jak i ponów próbę, każdej próbie longRetry zawiera ponownych prób i maksymalną liczbę prób ponawiania * longRetry.<br/><br/>Na przykład, jeśli mamy poniższe ustawienia w zasadach działania:<br/>Spróbuj ponownie: 3<br/>Parametr longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Załóżmy istnieje tylko jeden wycinek do wykonania (oczekiwanie stanu) i wykonania działania zawsze kończy się niepowodzeniem. Początkowo może być 3 wykonywanie kolejnych prób. Po każdej próbie stanu wycinka byłoby ponów próbę. Po pierwsze 3 prób się za pośrednictwem stanu wycinka będą LongRetry.<br/><br/>Po upływie godziny (to znaczy wartość longRetryInteval w) będzie inny zestaw 3 wykonywanie kolejnych prób. Po wykonaniu tej nie będzie można stanu wycinka i będą podejmowane próby. Dlatego całkowity 6 podejmowano.<br/><br/>Jeśli wykonanie żadnych zakończy się powodzeniem, stan wycinek jest gotowy, a próby są próby.<br/><br/>Parametr longRetry mogą być używane w sytuacji, w których danych zależnych dociera deterministyczna razy lub ogólnej środowiska jest niestabilnym, w których przetwarzania danych. W takich przypadkach to ponownych prób po kolei może nie pomagają i w ten sposób po interwału czasu powoduje żądanego wyniku.<br/><br/>Word Przestroga: nie należy ustawiać wysokiej wartości longRetry lub longRetryInterval. Zazwyczaj wyższej wartości oznacza innych kwestii systemowych. |
| longRetryInterval |Zakres czasu |00:00:00 |Opóźnienie między próbami czas ponów |

Aby uzyskać więcej informacji, zobacz [potoki](data-factory-create-pipelines.md) artykułu. 

## <a name="parallel-processing-of-data-slices"></a>Równoległe przetwarzanie wycinków danych
Data rozpoczęcia dla potoku można ustawić w przeszłości. Po wykonaniu tej fabryki danych automatycznie oblicza wszystkie fragmenty danych (wypełnienia tyłu) w przeszłości i rozpoczyna przetwarzanie je. Na przykład: Jeśli utworzyć potok z datą rozpoczęcia 2017-04-01, a bieżąca data jest 2017-04-10. Jeśli okresach wyjściowy zestaw danych jest codziennie, a następnie uruchamia fabryki danych, przetwarzanie wszystkich wycinków z 2017-04-01 do 2017-04-09 natychmiast, ponieważ data rozpoczęcia przypada w przeszłości. Wycinek 2017-04-10 nie został przetworzony jeszcze ponieważ wartość właściwości stylu w sekcji dostępności jest EndOfInterval domyślnie. Najstarsze wycinek jest przetwarzana najpierw domyślnie wartość executionPriorityOrder jest OldestFirst. Opis właściwości stylu, zobacz [dostępności zestawu danych](#dataset-availability) sekcji. Opis sekcji executionPriorityOrder, zobacz [zasady dotyczące działań](#activity-policies) sekcji. 

Można skonfigurować wycinków danych wypełnione wstecz do przetwarzania równoległego przez ustawienie **współbieżności** właściwości w **zasad** sekcji aktywność JSON. Ta właściwość określa liczba wykonań działania równoległego, które mogą wystąpić na różnych wycinków. Wartość domyślna właściwości współbieżności to 1. W związku z tym jeden wycinek jest przetwarzany w czasie domyślnie. Wartość maksymalna wynosi 10. Gdy potoku musi przechodzić przez duży zbiór dostępnych danych o większej wartości współbieżności przyspiesza przetwarzania danych. 

## <a name="rerun-a-failed-data-slice"></a>Uruchom ponownie wycinek danych nie powiodło się
Gdy wystąpi błąd podczas przetwarzania wycinka danych, można ustalić przyczyny niepowodzenia przetwarzania wycinek przy użyciu bloków portalu Azure lub monitora i zarządzania aplikacji. Zobacz [monitorowania i zarządzania nimi przy użyciu bloków portalu Azure potoki](data-factory-monitor-manage-pipelines.md) lub [zarządzania i monitorowania aplikacji](data-factory-monitor-manage-app.md) szczegółowe informacje.

Rozważmy poniższy przykład przedstawia dwa działania. Działania Activity1 i działania 2. Działania Activity1 zużywa fragment Dataset1 i tworzy wycinek Dataset2, które jest używane jako dane wejściowe przez Activity2 wygenerowało wycinek ostatecznego zestawu danych.

![Wycinek nie powiodło się](./media/data-factory-scheduling-and-execution/failed-slice.png)

Diagram pokazuje, że poza trzech ostatnich wycinków, wystąpił błąd podczas produkowania wycinek 9 10 AM dla Dataset2. Fabryka danych automatycznie śledzi zależności zestawu czasu serii danych. W związku z tym nie rozpoczyna działania Uruchom dla wycinka podrzędne 9-10 AM.

Narzędzia zarządzania i monitorowania fabryki danych umożliwiają przejdź do dzienników diagnostycznych dla wycinka nie powiodło się łatwo dostępne główną przyczynę problemu i rozwiąż problem. Po rozwiązaniu problemu, można łatwo uruchomić działania Uruchom, aby utworzyć wycinek nie powiodło się. Aby uzyskać więcej informacji na temat sposobu ponownie i zrozumienie stanu przejścia wycinki danych, zobacz [monitorowania i zarządzania nimi przy użyciu bloków portalu Azure potoki](data-factory-monitor-manage-pipelines.md) lub [zarządzania i monitorowania aplikacji](data-factory-monitor-manage-app.md).

Po ponownie 9 10 AM wycinek dotyczący **Dataset2**, fabryki danych rozpoczyna się uruchomienie wycinek zależnych 9 10 AM ostatecznego zestawu danych.

![Uruchom ponownie wycinka nie powiodło się](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="multiple-activities-in-a-pipeline"></a>Wiele działań w potoku
Potok może obejmować więcej niż jedno działanie. Jeśli masz wiele działań w potoku dane wyjściowe działania nie jest wejściem innego działania, działania mogą działać równolegle Jeśli wycinki danych wejściowych dla działania jest gotowe.

Dwa działania można połączyć w łańcuch (uruchomić jedno działanie po drugim), ustawiając wyjściowy zestaw danych jednego działania jako zestaw wejściowy drugiego. Działania może być w potoku tej samej lub różnych potoków. Drugi działania wykonuje, tylko jeśli pierwsza z nich zakończy działanie pomyślnie.

Rozważmy na przykład następujący przypadek, w którym potoku ma dwa działania:

1. A1 działania, który wymaga zewnętrzny zestaw danych wejściowych D1 i tworzy wyjściowy zestaw danych D2.
2. A2 działania, która wymaga danych wejściowych z zestawu danych D2 i tworzy wyjściowy zestaw danych D3.

W tym scenariuszu działania A1 i A2 znajdują się w tej samej potoku. Działanie A1 zostanie uruchomione po udostępnieniu danych zewnętrznych i częstotliwość zaplanowana dostępność zostanie osiągnięty. Działanie A2 zostanie uruchomione po zaplanowanym wycinków z D2 staną się dostępne i częstotliwość zaplanowana dostępność zostanie osiągnięty. W przypadku błędu w jednym z wycinkami w zestawie danych D2, A2 nie działa dla tego wycinka, dopóki nie będzie dostępny.

Widok diagramu z obu działań w tym samym potoku będzie wyglądać podobnie jak na poniższym diagramie:

![Tworzenie łańcuchów działań w tym samym potoku](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

Jak wspomniano wcześniej, działania może być w różnych potoków. W takiej sytuacji widoku diagramu będzie wyglądać na poniższym diagramie:

![Tworzenie łańcuchów działań w dwóch potoki](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

Zobacz [skopiuj sekwencyjnie](#copy-sequentially) sekcji w dodatku, na przykład.

## <a name="model-datasets-with-different-frequencies"></a>Zestawy danych modelu z różnych częstotliwości
W przykładach częstotliwości wejściowych i wyjściowych zestawów danych i okno Harmonogram działania są takie same. Niektóre scenariusze wymagają możliwości generowania danych wyjściowych z częstotliwością od częstotliwości jeden lub więcej składników. Fabryka danych obsługuje modelowania tych scenariuszy.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>Przykład 1: Tworzy codzienne raportu wyjściowego dla danych wejściowych, który jest dostępny co godzinę
Rozważmy scenariusz, w którym możesz mieć pomiaru dane wejściowe z czujników dostępne co godzinę w magazynie obiektów Blob Azure. Aby utworzyć raport agregacji codziennie o statystyk, takich jak średnią, minimalną i maksymalną dla dnia z [działania hive fabryki danych](data-factory-hive-activity.md).

Oto, jak można model ten scenariusz przy użyciu fabryki danych:

**Wejściowy zestaw danych**

Co godzinę pliki wejściowe są usuwane w folderze danego dnia. Dostępność dla danych wejściowych jest ustawiona na **godzina** (częstotliwość: godziny, interwał: 1).

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

Jeden plik wyjściowy jest tworzony codziennie w folderze tego dnia. Dostępność danych wyjściowych jest ustawiona na **dzień** (częstotliwość: dzień i interwał: 1).

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

**Działania: działania w potoku hive**

Skrypt hive odbiera odpowiednie *DateTime* informacji jako parametry, które używają **WindowStart** zmiennej, jak pokazano w poniższy fragment kodu. Skrypt hive używa tej zmiennej do ładowania danych z odpowiednim folderze dzień, a następnie uruchom agregacji do generowania danych wyjściowych.

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

![Zależności danych](./media/data-factory-scheduling-and-execution/data-dependency.png)

Wycinek danych wyjściowych dla każdego dnia, zależy od 24 wycinków co godzinę z zestawem danych wejściowych. Fabryka danych automatycznie oblicza te zależności z nazwami wycinków mieszczące się w tym samym czasie jako wycinek danych wyjściowych do wyprodukowania danych wejściowych. Jeśli dowolne 24 wycinków wejściowy nie jest dostępna, fabryki danych oczekuje na wejściowych wycinek będzie gotowa, przed rozpoczęciem codziennego uruchamiania działania.

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>Przykład 2: Określ zależności z wyrażeń i funkcji usługi fabryka danych
Teraz Rozważmy scenariusz, w innym. Załóżmy, że masz działania hive, która przetwarza dwóch zestawów danych wejściowych. Jeden z nich ma codziennie nowe dane, ale jeden z nich pobiera nowe dane co tydzień. Załóżmy, że chcesz nie sprzężenia w dwóch danych wejściowych i utworzenie danych wyjściowych każdego dnia.

Proste podejście w fabryce danych, które automatycznie rysunki się po prawej wprowadź wycinków przetworzyć ustawiając z danymi wyjściowymi czasowym wycinka danych okresu nie działać.

Należy określić, że każde działanie Uruchom fabryki danych należy używać wycinek danych ostatniego tygodnia dla zestawu danych wejściowych co tydzień. Przy użyciu funkcji usługi fabryka danych Azure przedstawioną w poniższy fragment kodu to zachowanie.

**Input1: Obiektów blob platformy Azure**

Pierwsze dane wejściowe są obiektów blob platformy Azure są aktualizowane codziennie.

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

**Input2: Obiektów blob platformy Azure**

Input2 jest obiektów blob platformy Azure aktualizowana co tydzień.

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

**Dane wyjściowe: Obiektów blob platformy Azure**

Jeden plik wyjściowy jest tworzony codziennie w folderze na dzień. Dostępność danych wyjściowych ma ustawioną wartość **dzień** (częstotliwość: dzień, interwał: 1).

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

**Działania: działania w potoku hive**

Działanie hive przyjmuje dwóch parametrów wejściowych i tworzy wycinek danych wyjściowych każdego dnia. Można określić każdego dnia wycinka danych wyjściowych do zależą od poprzedniego tygodnia wycinek wejściowych reakcję co tydzień w następujący sposób.

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

Zobacz [funkcje fabryki danych i zmienne systemu](data-factory-functions-variables.md) listę funkcje i zmienne systemu, które obsługuje fabryki danych.

## <a name="appendix"></a>Dodatek

### <a name="example-copy-sequentially"></a>Przykład: kopiowanie sekwencyjnie
Istnieje możliwość uruchamiane po kolei wiele operacji kopiowania w sposób sekwencyjnych/uporządkowane. Na przykład może mieć dwa działania kopiowania w potoku (CopyActivity1 i CopyActivity2) z następujących zestawach danych wyjściowych danych wejściowych:   

CopyActivity1

Dane wejściowe: zestawu danych. Dane wyjściowe: Dataset2.

CopyActivity2

Dane wejściowe: Dataset2.  Dane wyjściowe: Dataset3.

CopyActivity2 może działać tylko wtedy, gdy CopyActivity1 zostało uruchomione pomyślnie i Dataset2 jest dostępny.

Oto kod JSON potoku próbki:

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

Zwróć uwagę, że w tym przykładzie wyjściowego zestawu danych pierwsze działanie kopiowania (Dataset2) został określony jako danych wejściowych dla drugiego działania. W związku z tym działanie drugi działa tylko wtedy, gdy wyjściowy zestaw danych z pierwsze działanie jest gotowy.  

W przykładzie CopyActivity2 może mieć inne dane, takie jak Dataset3, ale określ Dataset2 jako dane wejściowe CopyActivity2, dlatego działanie nie jest uruchamiane, dopóki nie zakończy się CopyActivity1. Na przykład:

CopyActivity1

Dane wejściowe: Dataset1. Dane wyjściowe: Dataset2.

CopyActivity2

Dane wejściowe: Dataset3, Dataset2. Dane wyjściowe: Dataset4.

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

Zwróć uwagę, że w tym przykładzie dwa zestawy danych wejściowych są określone dla działania kopiowania drugiego. Jeśli określono wielu danych wejściowych, tylko pierwszy wejściowy zestaw danych służy do kopiowania danych, ale inne zestawy danych są używane jako zależności. CopyActivity2 zaczyna się tylko wtedy, gdy są spełnione następujące warunki:

* Pomyślnie ukończono CopyActivity1 i Dataset2 jest dostępny. Ten zestaw danych nie jest używany podczas kopiowania danych do Dataset4. Tylko działa jako zależność planowania dla CopyActivity2.   
* Dataset3 jest dostępna. Ten zestaw danych reprezentuje dane, które są kopiowane do lokalizacji docelowej. 
