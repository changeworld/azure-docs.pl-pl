---
title: Planowanie i wykonywanie w usłudze Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, aspekty planowania i wykonywania modelu aplikacji usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 088a83df-4d1b-4ac1-afb3-0787a9bd1ca5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 2d7fc45faf1fb77c7d9181e5a2419096dd1ad0f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61258955"
---
# <a name="data-factory-scheduling-and-execution"></a>Planowanie fabryki danych i wykonywanie
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [wyzwalacze i wykonywanie potoku](../concepts-pipeline-execution-triggers.md) artykułu.

W tym artykule wyjaśniono aspekty planowania i wykonywania modelu aplikacji usługi Azure Data Factory. W tym artykule założono, że rozumiesz podstawowe pojęcia modelu aplikacji usługi Data Factory, włącznie z działania, potoki, połączonych usług i zestawów danych. Podstawowe pojęcia usługi Azure Data Factory na ten temat można znaleźć w następujących artykułach:

* [Wprowadzenie do usługi Data Factory](data-factory-introduction.md)
* [Potoki](data-factory-create-pipelines.md)
* [Zestawy danych](data-factory-create-datasets.md) 

## <a name="start-and-end-times-of-pipeline"></a>Czas rozpoczęcia i zakończenia potoku
Potok jest aktywny tylko między jego **start** czasu i **zakończenia** czasu. Nie jest wykonywany przed godziną rozpoczęcia lub po godzinie zakończenia. Jeśli potok jest wstrzymana, nie jest wykonywany niezależnie od czasu rozpoczęcia i zakończenia. Dla potoku do uruchamiania go powinna nie można wstrzymać. Te ustawienia (uruchamianie i kończenie, wstrzymane) można znaleźć w definicji potoku: 

```json
"start": "2017-04-01T08:00:00Z",
"end": "2017-04-01T11:00:00Z"
"isPaused": false
```

Aby uzyskać więcej informacji zobacz te właściwości [tworzenie potoków](data-factory-create-pipelines.md) artykułu. 


## <a name="specify-schedule-for-an-activity"></a>Określ harmonogram działania
Nie jest potok, który jest wykonywany. To działania w potoku, które są wykonywane w kontekście ogólnego potoku. Należy określić Harmonogram cykliczny dla działania, za pomocą **harmonogramu** sekcji aktywność JSON. Na przykład można zaplanować działania uruchamiane co godzinę w następujący sposób:  

```json
"scheduler": {
    "frequency": "Hour",
    "interval": 1
},
```

Jak pokazano na poniższym diagramie, określając harmonogram działania tworzy serię wirowania systemu windows za pomocą w menu start potoku i godzinę zakończenia. Okna wirowania to o stałym rozmiarze ciągłe, nienakładające interwałami. Te okna wirowania logicznych dla działania są nazywane **okien działania**.

![Przykład harmonogramu działania](media/data-factory-scheduling-and-execution/scheduler-example.png)

**Harmonogramu** właściwość dla działania jest opcjonalna. Jeśli określisz tę właściwość, muszą być zgodne tempo, określonych w definicji wyjściowego zestawu danych działania. Obecnie harmonogram jest prowadzony przy użyciu wyjściowego zestawu danych. Dlatego należy utworzyć wyjściowy zestaw danych, nawet jeśli działanie nie generuje żadnych danych wyjściowych. 

## <a name="specify-schedule-for-a-dataset"></a>Określ harmonogram dla zestawu danych
Działania w potoku usługi fabryka danych może zająć zero lub więcej danych wejściowych **zestawów danych** i tworzące co najmniej jeden wyjściowe zestawy danych. W przypadku działania, można określić tempo, w którym dane wejściowe są dostępne lub danych wyjściowych jest generowany przy użyciu **dostępności** sekcji w definicji zestawu danych. 

**Częstotliwość** w **dostępności** sekcja określa jednostkę czasu. Dozwolone wartości częstotliwości to: Minuty, godziny, dnia, tygodnia i miesiąca. **Interwał** właściwości w sekcji dostępność Określa mnożnik częstotliwości. Na przykład: Jeśli częstotliwość jest ustawione na dzień, a interwał wynosi 1 dla wyjściowego zestawu danych, dane wyjściowe są generowane raz dziennie. Jeśli określisz częstotliwość co minutę, zaleca się ustawić interwał wynoszący nie mniej niż 15. 

W poniższym przykładzie danych wejściowych jest dostępny co godzinę i dane wyjściowe są generowane co godzinę (`"frequency": "Hour", "interval": 1`). 

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

Obecnie **wyjściowy zestaw danych kieruje harmonogramem**. Innymi słowy harmonogramu określony dla wyjściowego zestawu danych służy do uruchomienia działania w czasie wykonywania. Dlatego należy utworzyć wyjściowy zestaw danych, nawet jeśli działanie nie generuje żadnych danych wyjściowych. Jeśli w działaniu nie są używane żadne dane wejściowe, możesz pominąć tworzenie zestawu danych wejściowych. 

W poniższej definicji potoku **harmonogramu** właściwość jest używana do określenia harmonogramu działania. Ta właściwość jest opcjonalna. Obecnie harmonogramu działania muszą być zgodne z harmonogramem określonym dla wyjściowego zestawu danych.
 
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

W tym przykładzie działanie jest uruchamiane co godzinę od godziny rozpoczęcia i zakończenia potoku. Dane wyjściowe są generowane co godzinę dla windows trzech godzin (8: 00 - 9 AM, 9: 00 - 10: 00 i 10: 00 - 11: 00). 

Każda jednostka danych używane lub tworzone przez uruchomienie działania jest nazywany **wycinka danych**. Na poniższym diagramie przedstawiono przykład działania o jeden wejściowy zestaw danych i jeden wyjściowy zestaw danych: 

![Harmonogram dostępności](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

Na diagramie przedstawiono godzinowe wycinki danych wejściowych i wyjściowych zestawu danych. Na diagramie przedstawiono trzy wycinki danych wejściowych, które są gotowe do przetworzenia. Działanie AM 10-11 jest w toku, tworzenie wycinek danych wyjściowych AM 10-11. 

Aby uzyskać dostęp przedział czasu skojarzony z bieżącym wycinek w zestawie danych JSON przy użyciu zmiennych: [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) i [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables). Podobnie można uzyskać dostęp do skojarzony z oknem działania przy użyciu WindowStart i WindowEnd przedział czasu. Harmonogram działania muszą być zgodne harmonogram wyjściowy zestaw danych działania. W związku z tym, SliceStart i SliceEnd wartości są takie same jak wartości WindowStart i WindowEnd odpowiednio. Aby uzyskać więcej informacji na temat tych zmiennych, zobacz [funkcji usługi fabryka danych i zmiennych systemowych](data-factory-functions-variables.md#data-factory-system-variables) artykułów.  

W kodzie JSON działania, można użyć tych zmiennych do różnych celów. Na przykład, można je zaznaczyć dane wejściowe i wyjściowe zestawy danych reprezentujące dane szeregów czasowych (na przykład: 8 JESTEM 9 AM). W tym przykładzie również użyto **WindowStart** i **WindowEnd** do wybrania odpowiednich danych dla działania uruchamiania i skopiuj go do obiektu blob z odpowiednią **folderPath**. **FolderPath** jest sparametryzowane mieć osobnego folderu dla każdej godziny.  

W powyższym przykładzie harmonogram określona dla wejściowej i wyjściowe zestawy danych jest ten sam (co godzinę). Jeśli wejściowy zestaw danych dla działania jest dostępna pod innymi wartościami frequency Załóżmy, że co 15 minut, działanie, które generuje ten wyjściowy zestaw danych nadal działa co godzinę jako wyjściowy zestaw danych jest elementem wpływającym na harmonogram działania. Aby uzyskać więcej informacji, zobacz [modelować zestawy danych z różnych częstotliwości](#model-datasets-with-different-frequencies).

## <a name="dataset-availability-and-policies"></a>Zasad i dostępności zestawu danych
Jak już wspomniano użycie częstotliwość i interwał właściwości w sekcji dostępność definicji zestawu danych. Istnieje kilka innych właściwości, które mają wpływ na planowanie i wykonywanie działania. 

### <a name="dataset-availability"></a>Dostępności zestawu danych 
W poniższej tabeli opisano właściwości można używać w **dostępności** sekcji:

| Właściwość | Opis | Wymagane | Domyślne |
| --- | --- | --- | --- |
| frequency |Określa jednostkę czasu dla trybu produkcyjnego wycinek zestawu danych.<br/><br/><b>Obsługiwana częstotliwość</b>: Minuty, godziny, dnia, tygodnia, miesiąca |Yes |Nie dotyczy |
| interval |Określa mnożnik częstotliwości<br/><br/>"Interwał częstotliwości x" Określa, jak często wycinek jest generowany.<br/><br/>Zestaw danych można podzielić w systemie godzinowym, należy ustawić <b>częstotliwość</b> do <b>godzinę</b>, i <b>interwał</b> do <b>1</b>.<br/><br/><b>Uwaga</b>: Jeśli określisz częstotliwość co minutę, zaleca się ustawić interwał wynoszący nie mniej niż 15 |Yes |Nie dotyczy |
| Styl |Określa, czy wycinek powinny być tworzone na początku/końca zakresu.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Jeśli parametr Frequency ma wartość miesiąca i styl jest równa EndOfInterval, wycinek jest generowany na ostatni dzień miesiąca. Jeśli styl StartOfInterval wycinek jest generowany pierwszego dnia miesiąca.<br/><br/>Jeśli parametr Frequency ma wartość dnia i styl jest równa EndOfInterval, wycinek jest generowany w ciągu ostatniej godziny, dnia.<br/><br/>Jeśli ustawiono styl EndOfInterval Frequency ma wartość Hour, wycinek jest generowany na koniec godziny. Na przykład dla wycinka okres 13: 00 – 2 PM, wycinek jest generowany w 14: 00. |Nie |EndOfInterval |
| anchorDateTime |Definiuje położenie bezwzględne w czasie używanych przez harmonogram do obliczenia granice wycinek zestawu danych. <br/><br/><b>Uwaga</b>: Jeśli AnchorDateTime części daty, które są bardziej szczegółowe niż częstotliwość bardziej szczegółowego części są ignorowane. <br/><br/>Na przykład jeśli <b>interwał</b> jest <b>co godzinę</b> (frequency: hour, interval: (1) i <b>AnchorDateTime</b> zawiera <b>minuty i sekundy</b>, a następnie <b>minuty i sekundy</b> części AnchorDateTime są ignorowane. |Nie |01/01/0001 |
| offset |Zakres czasu za pomocą którego przesunięte początek i koniec okresu wszystkich wycinków zestawu danych. <br/><br/><b>Uwaga</b>: Jeśli określono zarówno anchorDateTime, jak i przesunięcie, wynik jest połączone shift. |Nie |Nie dotyczy |

### <a name="offset-example"></a>przykład przesunięcia
Domyślnie codziennie (`"frequency": "Day", "interval": 1`) wycinki rozpoczynają się od 12: 00 czasu UTC (północ). Czas rozpoczęcia to 6: 00 czasu UTC, zamiast tego należy ustawić przesunięcie, jak pokazano w poniższym fragmencie kodu: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>przykład anchorDateTime
W poniższym przykładzie zestaw danych jest generowany co 23 godzin. Pierwszy wycinek, który rozpoczyna się o godzinie określonej przez anchorDateTime, która jest ustawiona na `2017-04-19T08:00:00` (czas UTC).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>Przesunięcie/styl przykład
Poniższy zestaw danych jest miesięcznych danych i jest generowany na 3 każdego miesiąca o 8:00 (`3.08:00:00`):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

### <a name="dataset-policy"></a>Zestaw danych zasad
Zestaw danych może mieć zdefiniowane zasady walidacji określający, jak dane generowane przez wykonanie wycinek mogą być sprawdzone zanim będzie gotowa do użycia. W takich przypadkach po zakończeniu wykonywania, wycinek danych wyjściowych stan wycinka jest zmieniana na **oczekiwania** z podstanu z **weryfikacji**. Po zweryfikowaniu wycinki stan wycinka zmienia się na **gotowe**. Jeśli wycinek danych została opracowana, ale nie przeszedł pomyślnie weryfikacji, uruchomienia działania podrzędne wycinki, które są zależne od tego wycinka nie są przetwarzane. [Monitorowanie potoków i zarządzanie nimi](data-factory-monitor-manage-pipelines.md) obejmuje różne stany wycinki danych w usłudze Data Factory.

**Zasad** sekcji w definicji zestawu danych definiuje kryteria lub warunek, który należy spełnić wycinków zestawu danych. W poniższej tabeli opisano właściwości można używać w **zasad** sekcji:

| Policy Name (Nazwa zasad) | Opis | Dotyczy | Wymagane | Domyślne |
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

Aby uzyskać więcej informacji na temat tych właściwości i przykłady, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md) artykułu. 

## <a name="activity-policies"></a>Zasady dotyczące działań
Zasady wpływają na zachowania w czasie wykonywania działania, w szczególności, po przetworzeniu wycinka tabeli. Poniższa tabela zawiera szczegółowe informacje.

| Właściwość | Dozwolone wartości | Wartość domyślna | Opis |
| --- | --- | --- | --- |
| Współbieżność |Liczba całkowita <br/><br/>Wartość maksymalna: 10 |1 |Liczba współbieżnych wykonań działania.<br/><br/>Określa liczbę wykonań działania równoległego, które mogą być uruchomione na różnych wycinki. Na przykład jeśli działanie musi przechodzić przez duży zestaw dostępnych danych, o wartości większej współbieżności przyspiesza przetwarzanie danych. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Określa kolejność wycinki danych, które są przetwarzane.<br/><br/>Na przykład jeśli masz 2 dzieli (jeden występuje o 16: 00 i inną o 17: 00), a oba są oczekiwanie na wykonanie. Jeśli ustawisz executionPriorityOrder jako NewestFirst, jest przetwarzana najpierw wycinek o 17: 00. Podobnie jeśli ustawisz executionPriorityORder jako OldestFIrst, następnie wycinka u 16: 00 jest przetwarzany. |
| retry |Liczba całkowita<br/><br/>Maksymalna wartość może wynosić 10 |0 |Liczba ponownych prób zanim przetwarzania danych dla wycinka jest oznaczony jako niepowodzenie. Wykonania działania dla wycinka danych zostanie ponowiony do określonej liczby ponownych prób. Ponowienie próby jest wykonywane tak szybko, jak to możliwe po niepowodzeniu. |
| timeout |TimeSpan |00:00:00 |Limit czasu działania. Przykład: 00:10:00 (oznacza limit czasu 10 minut)<br/><br/>Jeśli wartość nie została określona lub ma wartość 0, limit czasu jest nieskończona.<br/><br/>Jeśli czas przetwarzania danych na wycinek przekracza wartość limitu czasu, zostanie anulowane, a system podejmuje próbę przetwarzania. Liczba ponownych prób, zależy od właściwości ponownych prób. W przypadku przekroczenia limitu czasu stan jest ustawiony na przekroczenie limitu czasu. |
| delay |TimeSpan |00:00:00 |Określ opóźnienie przed rozpoczęciem przetwarzania danych startów wycinka.<br/><br/>Wykonywanie działania dla wycinka danych została uruchomiona po oczekiwanym czasie wykonywania opóźnienie.<br/><br/>Przykład: 00:10:00 (implikuje użycie opóźnieniem 10 minut) |
| longRetry |Liczba całkowita<br/><br/>Wartość maksymalna: 10 |1 |Liczba prób długa — ponowienie próby, zanim wycinek wykonanie nie powiodło się.<br/><br/>są rozciągane w prób longRetry, longRetryInterval. Więc jeśli potrzebujesz określić czas między ponownymi próbami, należy użyć longRetry. Jeśli określono zarówno longRetry, jak i ponów próbę kolejnymi próbami longRetry zawiera ponownymi próbami i maksymalną liczbę prób ponawiania * longRetry.<br/><br/>Na przykład, mamy następujące ustawienia zasad dotyczących działań:<br/>Ponów próbę: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Przyjęto założenie, istnieje tylko jeden wycinek do wykonania (oczekiwanie stanu) i wykonania działania każdym razem, gdy kończy się niepowodzeniem. Początkowo będzie można 3 próby wykonania kolejnych. Po każdej próbie stan wycinka byłoby ponownych prób. Po pierwsze 3 prób przez, stan wycinka byłoby LongRetry.<br/><br/>Po upływie godziny (czyli wartość longRetryInteval firmy) będzie inny zbiór 3 próby wykonania kolejnych. Po tym stan wycinka, czy nie, a wszystkie próby może nastąpić. Dlatego całkowity podejmowano próby 6.<br/><br/>Jeśli wykonanie dowolnego zakończy się powodzeniem, stan wycinka będzie gotowy, a wszystkie próby są próby.<br/><br/>longRetry mogą być używane w sytuacji, w którym dane zależne dociera niedeterministyczne razy lub niestabilnym całego środowiska, w ramach której przetwarzania danych. W takich przypadkach to ponownych prób po kolei może nie pozwalających i sposób po upływie czasu skutkuje żądaną produktu wyjściowego.<br/><br/>Word Przestroga: nie należy ustawiać wysokiej wartości longRetry lub longRetryInterval. Zazwyczaj wyższe wartości oznaczają innych kwestii systemowych. |
| longRetryInterval |TimeSpan |00:00:00 |Opóźnienie między próbami długa — ponowienie próby |

Aby uzyskać więcej informacji, zobacz [potoki](data-factory-create-pipelines.md) artykułu. 

## <a name="parallel-processing-of-data-slices"></a>Równoległe przetwarzanie wycinki danych
W przeszłości, można ustawić datę rozpoczęcia dla potoku. Jeśli tak zrobisz, Data Factory automatycznie oblicza wszystkie wycinki danych (wypełnienia tyłu) w przeszłości i rozpoczyna przetwarzanie ich. Na przykład: Jeśli utworzysz potok z datą rozpoczęcia 2017-04-01, a bieżąca data to 2017-04-10. Jeśli tempo wyjściowy zestaw danych jest codziennie, a następnie uruchamia fabryki danych, przetwarzanie wszystkich wycinków od 2017-04-01 do 2017-04-09 natychmiast, ponieważ data rozpoczęcia przypada w przeszłości. Wycinek od 2017-04-10 nie jest przetwarzany jeszcze ponieważ wartość właściwości stylu w sekcji dostępność EndOfInterval domyślnie. Najstarszy wycinek jest przetwarzany najpierw jako domyślną wartość executionPriorityOrder jest OldestFirst. Aby uzyskać opis właściwości stylu, zobacz [dostępności zestawu danych](#dataset-availability) sekcji. Opis sekcji executionPriorityOrder, zobacz [zasady dotyczące działań](#activity-policies) sekcji. 

Można skonfigurować wycinki wypełnione kopii danych, które ma być przetwarzana równolegle przez ustawienie **współbieżności** właściwość **zasad** sekcji aktywność JSON. Ta właściwość określa liczbę wykonań działania równoległego, które mogą być uruchomione na różnych wycinki. Wartość domyślna właściwości współbieżności to 1. W związku z tym jeden wycinek jest przetwarzany w danym momencie domyślnie. Wartość maksymalna to 10. Gdy potoku musi przechodzić przez duży zestaw dostępnych danych, o wartości większej współbieżności przyspiesza przetwarzanie danych. 

## <a name="rerun-a-failed-data-slice"></a>Uruchom ponownie wycinka danych nie powiodło się
Gdy wystąpi błąd podczas przetwarzania wycinka danych, można znaleźć niepowodzenia przetwarzania wycinka przy użyciu bloków w witrynie Azure portal lub aplikacji monitorowanie i zarządzanie. Zobacz [monitorowanie i zarządzanie potoków przy użyciu bloków w witrynie Azure portal](data-factory-monitor-manage-pipelines.md) lub [aplikacja do monitorowania i zarządzania](data-factory-monitor-manage-app.md) Aby uzyskać szczegółowe informacje.

Poniższy przykład przedstawia dwa działania należy wziąć pod uwagę. Działania Activity1 i działania 2. Działania Activity1 wykorzystuje wycinek Dataset1 i generuje wycinek Dataset2, który jest używany jako dane wejściowe przez Activity2 do tworzenia wycinka końcowy zestaw danych.

![Wycinek nie powiodło się](./media/data-factory-scheduling-and-execution/failed-slice.png)

Diagram pokazuje, że poza trzy ostatnie wycinki, wystąpił błąd podczas tworzenia wycinka 9 – 10 AM dla Dataset2. Data Factory automatycznie śledzi zależności dla zestawu danych serii czasu. W rezultacie nie uruchamia przebiegu działania dla wycinka podrzędnego 9-10: 00.

Narzędzia usługi fabryka danych monitorowania i zarządzania pozwalają umożliwiających dogłębne analizowanie dziennika diagnostycznego wycinek nie powiodło się, aby łatwo znaleźć główną przyczynę problemu i rozwiązać ten problem. Po rozwiązaniu problemu, możesz łatwo rozpocząć działanie uruchomiona, aby utworzyć wycinek nie powiodło się. Aby uzyskać więcej informacji na temat sposobu ponowne uruchomienie i zrozumieć stanami wycinki danych, zobacz [monitorowanie i zarządzanie potoków przy użyciu bloków w witrynie Azure portal](data-factory-monitor-manage-pipelines.md) lub [aplikacja do monitorowania i zarządzania](data-factory-monitor-manage-app.md).

Po ponownym uruchomieniu 9 – 10 AM wycinek dotyczący **Dataset2**, Data Factory uruchamia uruchomienia dla wycinka zależne 9 – 10 AM na ostateczny zestaw danych.

![Uruchom ponownie wycinka nie powiodło się](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="multiple-activities-in-a-pipeline"></a>Wiele działań w potoku
Potok może obejmować więcej niż jedno działanie. Jeśli masz wiele działań w potoku, a dane wyjściowe działania nie jest dane wejściowe kolejnego działania, działania mogą być wykonywane równolegle, jeśli wycinki danych wejściowych dla działania jest gotowe.

Dwa działania można połączyć w łańcuch (uruchomić jedno działanie po drugim), ustawiając wyjściowy zestaw danych jednego działania jako zestaw wejściowy drugiego. Działania można w potoku tej samej lub różnych potoków. Drugie działanie wykonuje, tylko gdy pierwsza z nich zakończy się pomyślnie.

Na przykład rozważmy następujący przypadek, gdzie potok zawiera dwa działania:

1. A1 działania, który wymaga zewnętrzny zestaw danych wejściowych D1 i tworzy wyjściowy zestaw danych D2.
2. Działanie A2, wymaga wprowadzenia danych z zestawu danych D2, która tworzy wyjściowy zestaw danych D3.

W tym scenariuszu działania A1 i A2 znajdują się w tej samej potoku. Działanie A1 jest uruchamiany, gdy dane zewnętrzne są dostępne i częstotliwość zaplanowana dostępność zostanie osiągnięty. Działanie A2 jest uruchamiany, gdy zaplanowane wycinki D2 stają się dostępne i częstotliwość zaplanowana dostępność zostanie osiągnięty. W przypadku błędu w jednym z wycinków w zestawie danych D2 A2 nie działa dla tego wycinka, dopóki nie stanie się dostępny.

Widok diagramu z obu działań w potoku w tym samym powinien wyglądać podobnie jak na poniższym diagramie:

![Tworzenie łańcuchów działań w tym samym potoku](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

Jak wspomniano wcześniej, działania może być w różnych potoków. W takiej sytuacji w widoku diagramu będzie wyglądać na poniższym diagramie:

![Tworzenie łańcuchów działań w dwa potoki](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

Zobacz kopiowania sekwencyjnie w dodatku, na przykład sekcji.

## <a name="model-datasets-with-different-frequencies"></a>Zestawy modelu danych z różnych częstotliwości
W przykładach częstotliwości dla danych wejściowych i wyjściowych zestawów danych i okno harmonogramu działania są takie same. Niektóre scenariusze wymagają możliwość generowania danych wyjściowych z częstotliwością różni się od częstotliwości co najmniej jedne dane wejściowe. Usługa Data Factory obsługuje modelowanie tych scenariuszy.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>Przykład 1: Wygenerowanie raportu dotyczącego codziennych danych wyjściowych dla danych wejściowych, który jest dostępny, co godzinę
Rozważmy scenariusz, w którym wprowadzone pomiaru danych z czujników w usłudze Azure Blob storage na godzinę. Ma zostać wyprodukowany codzienne raportu zbiorczego przy użyciu statystyki, takie jak średnia, maksymalna i minimalna na dzień przy [działanie hive usługi Data Factory](data-factory-hive-activity.md).

Poniżej przedstawiono, jak można modelować tego scenariusza usługa Data Factory:

**Wejściowy zestaw danych**

Co godzinę pliki wejściowe są porzucane w folderze dla danego dnia. Dostępność dla danych wejściowych jest ustawiony na **godzinę** (frequency: Godzina, interwał: 1).

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

Jeden plik wyjściowy jest tworzony co dzień w folderze codziennych. Dostępność danych wyjściowych jest ustawiony na **dzień** (frequency: Day i interval: 1).

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

**Działanie: działanie programu hive w potoku**

Skrypt programu hive otrzyma odpowiednią *daty/godziny* informacji jako parametry, które używają **WindowStart** zmiennej, jak pokazano w poniższym fragmencie kodu. Skrypt programu hive używa tej zmiennej do załadowania danych z odpowiednim folderze, w ciągu dnia i uruchamiania agregacji w celu wygenerowania danych wyjściowych.

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

Wycinek danych wyjściowych dla każdego dnia, zależy od 24 wycinki godzinowe wejściowego zestawu danych. Data Factory automatycznie oblicza te zależności z nazwami wycinków, które mieszczą się w tym samym okresie czasu jako wycinek danych wyjściowych, generowanie danych wejściowych. Jeśli dowolny 24 wycinki danych wejściowych nie jest dostępna, Data Factory czeka wycinek danych wejściowych, które będzie gotowa, przed rozpoczęciem codziennego uruchamiania działania.

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>Przykład 2: Określ zależności przy użyciu wyrażeń i funkcji usługi fabryka danych
Rozważmy inny scenariusz. Załóżmy, że działanie programu hive, która przetwarza dwa zestawy danych wejściowych. Jeden z nich ma nowe dane raz dziennie, ale jeden z nich pobiera nowe dane co tydzień. Załóżmy, że chcesz to zrobić sprzężenia między dwa obiekty wejściowe i generuje dane wyjściowe każdego dnia.

Proste podejście, w których Data Factory automatycznie rysunki się po prawej stronie wprowadź liczbę wycinków do przetworzenia przez dopasowanie do danych wyjściowych, wycinek danych czasu okresu nie działają.

Należy określić, że dla każdego uruchomienia działania, Data Factory powinna używać wycinka danych w ostatnim tygodniu dla wejściowego zestawu danych co tydzień. Przy użyciu funkcji usługi Azure Data Factory jak pokazano w poniższym fragmencie kodu to zachowanie.

**Wejście1: Obiekt blob platformy Azure**

Pierwszy element danych wejściowych jest obiektów blob platformy Azure są aktualizowane codziennie.

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

**Wejście2: Obiekt blob platformy Azure**

Wejście2 jest obiektów blob platformy Azure jest aktualizowana co tydzień.

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

**Dane wyjściowe: Obiekt blob platformy Azure**

Jeden plik wyjściowy jest tworzony w folderze każdego dnia w ciągu dnia. Dostępność danych wyjściowych jest ustawiona na **dzień** (frequency: Dzień, interwał: 1).

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

**Działanie: działanie programu hive w potoku**

Działanie programu hive przyjmuje dwa obiekty wejściowe i generuje wycinek danych wyjściowych każdego dnia. Można określić każdego dnia wycinek danych wyjściowych, aby była zależna od poprzedniego tygodnia wycinek danych wejściowych dla danych wejściowych co tydzień w następujący sposób.

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

Zobacz [funkcji usługi fabryka danych i zmiennych systemowych](data-factory-functions-variables.md) lista funkcji i zmiennych systemowych, obsługiwanych przez usługi Data Factory.

## <a name="appendix"></a>Dodatek

### <a name="example-copy-sequentially"></a>Przykład: Skopiuj sekwencyjnie
Istnieje możliwość uruchomić wiele operacji kopiowania, jedna po drugiej w sposób sekwencyjny/uporządkowane. Na przykład Niewykluczone, że dwa działania kopiowania w potoku (CopyActivity1 i CopyActivity2) za pomocą następujących wyjściowe zestawy danych dane wejściowe:   

CopyActivity1

Dane wejściowe: Zestaw danych. Dane wyjściowe: Dataset2.

CopyActivity2

Dane wejściowe: Dataset2.  Dane wyjściowe: Dataset3.

CopyActivity2 może działać tylko wtedy, gdy CopyActivity1 zostało uruchomione pomyślnie Dataset2 jest dostępna.

Poniżej przedstawiono przykładowy potok JSON:

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

Należy zauważyć, że w tym przykładzie wyjściowego zestawu danych pierwszego działania kopiowania (Dataset2) został określony jako danych wejściowych dla drugiego działania. W związku z tym drugie działanie działa tylko w przypadku, gdy wyjściowy zestaw danych z pierwszego działania będzie gotowy.  

W tym przykładzie CopyActivity2 mogą mieć różne dane wejściowe, takie jak Dataset3, ale Dataset2 można określić jako dane wejściowe do CopyActivity2, więc działanie nie jest uruchamiane, dopóki nie zakończy się CopyActivity1. Na przykład:

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

Należy zauważyć, że w tym przykładzie dwa zestawy danych wejściowych są określone dla drugiego działania kopiowania. Jeśli określono wiele danych wejściowych, tylko pierwszy wejściowy zestaw danych służy do kopiowania danych, ale inne zestawy danych są używane jako zależności. CopyActivity2 zaczyna się tylko wtedy, gdy są spełnione następujące warunki:

* Dostępna jest Dataset2 i CopyActivity1 została ukończona pomyślnie. Ten zestaw danych nie jest używana podczas kopiowania danych do Dataset4. Tylko działa jako zależność planowania dla CopyActivity2.   
* Dataset3 jest dostępna. Ten zestaw danych reprezentuje dane, które są kopiowane do lokalizacji docelowej. 
