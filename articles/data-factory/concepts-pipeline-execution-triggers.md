---
title: Wyzwalacze i wykonywanie potoku w usłudze Azure Data Factory | Microsoft Docs
description: Ten artykuł zawiera informacje na temat wykonywania potoku w usłudze Azure Data Factory na żądanie lub przy użyciu wyzwalacza.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/05/2018
ms.author: shlo
ms.openlocfilehash: 21e66f962d1cc0bbbe8d780a702216d40abe2836
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66155215"
---
# <a name="pipeline-execution-and-triggers-in-azure-data-factory"></a>Wyzwalacze i wykonywanie potoku w usłudze Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of the Data Factory service that you're using:"]
> * [Wersja 1](v1/data-factory-scheduling-and-execution.md)
> * [Bieżąca wersja](concepts-pipeline-execution-triggers.md)

_Uruchomienie potoku_ w bieżącej wersji usługi Azure Data Factory definiuje wystąpienie wykonania potoku. Załóżmy na przykład istnienie potoku, który jest wykonywany o godzinie 8:00, 9:00 i 10:00. W takim przypadku występują trzy osobne uruchomienia potoku. Każde uruchomienie potoku ma unikatowy identyfikator uruchomienia potoku. Identyfikator uruchomienia jest identyfikatorem GUID, który w sposób unikatowy definiuje to konkretne uruchomienie potoku.

Uruchomienia potoku są tworzone zazwyczaj przez przekazanie argumentów do parametrów, które definiujesz w potoku. Potok możesz wykonywać ręcznie albo za pomocą _wyzwalacza_. Ten artykuł zawiera szczegółowe informacje na temat obu metod wykonywania potoku.

## <a name="manual-execution-on-demand"></a>Wykonywanie ręczne (na żądanie)
Ręczne wykonywanie potoku nazywane jest również wykonywaniem _na żądanie_.

Na przykład załóżmy, że masz podstawowy potok o nazwie **copyPipeline**, który chcesz wykonać. Ten potok ma jedno działanie polegające na kopiowaniu elementów z folderu źródłowego w magazynie Azure Blob Storage do folderu docelowego w tym samym magazynie. W następującej definicji JSON przedstawiono ten przykładowy potok:

```json
{
    "name": "copyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "name": "CopyBlobtoBlob",
                "inputs": [
                    {
                        "referenceName": "sourceBlobDataset",
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "sinkBlobDataset",
                        "type": "DatasetReference"
                    }
                ]
            }
        ],
        "parameters": {
            "sourceBlobContainer": {
                "type": "String"
            },
            "sinkBlobContainer": {
                "type": "String"
            }
        }
    }
}
```

W definicji JSON potok przyjmuje dwa parametry: **sourceBlobContainer** i **sinkBlobContainer**. Wartości tych parametrów są przekazywane w czasie uruchomienia.

Możesz uruchomić potok ręcznie, używając jednej z następujących metod:
- Zestaw SDK .NET
- Moduł programu Azure PowerShell
- Interfejs API REST
- Zestaw SDK dla języka Python

### <a name="rest-api"></a>Interfejs API REST
Następujące przykładowe polecenie ilustruje, jak ręcznie uruchomić potok przy użyciu interfejsu API REST:

```
POST
https://management.azure.com/subscriptions/mySubId/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory/pipelines/copyPipeline/createRun?api-version=2017-03-01-preview
```

Aby uzyskać pełny przykład, zobacz [Szybki Start: Tworzenie fabryki danych przy użyciu interfejsu API REST](quickstart-create-data-factory-rest-api.md).

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Następujące przykładowe polecenie ilustruje, jak ręcznie uruchomić potok przy użyciu programu Azure PowerShell:

```powershell
Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
```

Parametry są przekazywane w treści ładunku żądania. W zestawie SDK platformy .NET, programie Azure PowerShell i zestawie SDK dla języka Python wartości przekazuje się w słowniku przekazywanym jako argument wywołania:

```json
{
  "sourceBlobContainer": "MySourceFolder",
  "sinkBlobContainer": "MySinkFolder"
}
```

Ładunek odpowiedzi to unikatowy identyfikator uruchomienia potoku:

```json
{
  "runId": "0448d45a-a0bd-23f3-90a5-bfeea9264aed"
}
```

Aby uzyskać pełny przykład, zobacz [Szybki Start: Tworzenie fabryki danych przy użyciu programu Azure PowerShell](quickstart-create-data-factory-powershell.md).

### <a name="net-sdk"></a>Zestaw SDK .NET
Następujące przykładowe wywołanie ilustruje, jak ręcznie uruchomić potok przy użyciu zestawu SDK platformy .NET:

```csharp
client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters)
```

Aby uzyskać pełny przykład, zobacz [Szybki Start: Tworzenie fabryki danych przy użyciu zestawu .NET SDK](quickstart-create-data-factory-dot-net.md).

> [!NOTE]
> Zestaw SDK platformy .NET umożliwia wywoływanie potoków usługi Data Factory z usługi Azure Functions, z własnych usług internetowych itp.

<h2 id="triggers">Wykonywanie wyzwalacza</h2>
Wyzwalacze to inny sposób wykonywania uruchomienia potoku. Wyzwalacze reprezentują jednostki przetwarzania, które określają, kiedy należy rozpocząć wykonanie potoku. Obecnie usługa Data Factory obsługuje 3 typy wyzwalaczy:

- Wyzwalacz harmonogramu: Wyzwalacz, który wywołuje potok zgodnie z harmonogramem zegarowym.

- Wyzwalacz okna wirowania: Wyzwalacz, który działa w okresowych interwałach przy zachowaniu stanu.

- Oparte na zdarzeniach wyzwalacza: Wyzwalacz, który odpowiada na zdarzenie.

Między potokami i wyzwalaczami występuje relacja wiele-do-wielu. Wiele wyzwalaczy może uruchamiać jeden potok, a jeden wyzwalacz może uruchamiać wiele potoków. W poniższej definicji wyzwalacza właściwość **pipelines** odnosi się do listy potoków wyzwalanych przez określony wyzwalacz. Definicja właściwości zawiera wartości dla parametrów potoku.

### <a name="basic-trigger-definition"></a>Podstawowa definicja wyzwalacza

```json
{
    "properties": {
        "name": "MyTrigger",
        "type": "<type of trigger>",
        "typeProperties": {...},
        "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>": "<parameter 2 Value>"
                }
            }
        ]
    }
}
```

## <a name="schedule-trigger"></a>Wyzwalacz harmonogramu
Wyzwalacz harmonogramu uruchamia potoki zgodnie z harmonogramem zegarowym. Ten wyzwalacz obsługuje opcje okresowe i zaawansowane kalendarza. Obsługuje na przykład interwały takie jak „co tydzień” czy „w poniedziałek o godzinie 17:00 i czwartek o godzinie 21: 00”. Wyzwalacz harmonogramu jest elastyczny, ponieważ wzorzec zestawu jest niezależny, a wyzwalacz nie rozróżnia pomiędzy danymi serii czasowych a innymi.

Aby uzyskać więcej informacji o wyzwalaczach harmonogramu i przykładach, zobacz [Tworzenie wyzwalacza harmonogramu](how-to-create-schedule-trigger.md).

## <a name="schedule-trigger-definition"></a>Definicja wyzwalacza harmonogramu
Tworząc wyzwalacz harmonogramu, należy określić planowanie i powtarzanie przy użyciu definicji JSON.

Aby wyzwalacz harmonogramu uruchamiał potok, należy dołączyć odwołanie do konkretnego potoku do definicji wyzwalacza. Między potokami i wyzwalaczami występuje relacja wiele-do-wielu. Wiele wyzwalaczy może uruchomić jeden potok. Jeden wyzwalacz może uruchamiać wiele potoków.

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Year>>,
        "interval": <<int>>, // How often to fire
        "startTime": <<datetime>>,
        "endTime": <<datetime>>,
        "timeZone": "UTC",
        "schedule": { // Optional (advanced scheduling specifics)
          "hours": [<<0-24>>],
          "weekDays": [<<Monday-Sunday>>],
          "minutes": [<<0-60>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurrences": [
            {
              "day": <<Monday-Sunday>>,
              "occurrence": <<1-5>>
            }
          ]
        }
      }
    },
  "pipelines": [
    {
      "pipelineReference": {
        "type": "PipelineReference",
        "referenceName": "<Name of your pipeline>"
      },
      "parameters": {
        "<parameter 1 Name>": {
          "type": "Expression",
          "value": "<parameter 1 Value>"
        },
        "<parameter 2 Name>": "<parameter 2 Value>"
      }
    }
  ]}
}
```

> [!IMPORTANT]
> Właściwość **parameters** jest obowiązkową wartością elementu właściwości **pipelines**. Jeśli potok nie przyjmuje żadnych parametrów, należy dodać pustą definicję JSON dla właściwości **parameters**.

### <a name="schema-overview"></a>Przegląd schematu
Poniższa tabela zawiera ogólne omówienie głównych elementów schematu odnoszących się do powtarzania i planowania wyzwalacza:

| Właściwość JSON | Opis |
|:--- |:--- |
| **startTime** | Wartość daty i godziny. W przypadku podstawowych harmonogramów wartość właściwości **startTime** dotyczy pierwszego wystąpienia. W przypadku harmonogramów złożonych wyzwalacz nie jest uruchamiany wcześniej niż określona wartość właściwości **startTime**. |
| **endTime** | Data i godzina zakończenia wyzwalacza. Wyzwalacz nie jest wykonywany po określonej dacie i godzinie zakończenia. Wartość właściwości nie może odnosić się do przeszłości. <!-- This property is optional. --> |
| **timeZone** | Strefa czasowa. Obecnie jest obsługiwana tylko strefa czasowa UTC. |
| **recurrence** | Obiekt cyklu określający reguły powtarzania wyzwalacza. Obiekt cyklu obsługuje elementy właściwości **frequency**, **interval**, **endTime**, **count** i **schedule**. Po zdefiniowaniu obiektu cyklu wymagany jest element właściwości **frequency**. Inne elementy obiektu cyklu są opcjonalne. |
| **frequency** | Jednostka częstotliwości powtarzania wyzwalacza. Obsługiwane wartości to „minute” („minuta”), „hour” („godzina”), „day” („dzień”), „week” („tydzień”) i „month” („miesiąc”). |
| **interval** | Dodatnia liczba całkowita, która określa odstęp czasu dla wartości **frequency**. Wartość **frequency** określa, jak często jest uruchamiany wyzwalacz. Jeśli na przykład właściwość **interval** ma wartość 3, a właściwość **frequency** ma wartość „week” („tydzień”), wyzwalacz jest powtarzany co trzy tygodnie. |
| **schedule** | Harmonogram cyklu wyzwalacza. Wyzwalacz z określoną wartością właściwości **frequency** zmienia swój cykl na podstawie harmonogramu cyklu. Właściwość **schedule** zawiera modyfikacje cyklu oparte na minutach, godzinach, dniach tygodnia, dniach miesiąca i numerze tygodnia.

### <a name="schedule-trigger-example"></a>Przykładowy wyzwalacz harmonogramu

```json
{
    "properties": {
        "name": "MyTrigger",
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Hour",
                "interval": 1,
                "startTime": "2017-11-01T09:00:00-08:00",
                "endTime": "2017-11-02T22:00:00-08:00"
            }
        },
        "pipelines": [{
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "SQLServerToBlobPipeline"
                },
                "parameters": {}
            },
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "SQLServerToAzureSQLPipeline"
                },
                "parameters": {}
            }
        ]
    }
}
```

### <a name="schema-defaults-limits-and-examples"></a>Wartości domyślne, limity i przykłady harmonogramów

| Właściwość JSON | Type | Wymagane | Wartość domyślna | Prawidłowe wartości | Przykład |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** | string | Tak | Brak | Daty i godziny ISO 8601 | `"startTime" : "2013-01-09T09:30:00-08:00"` |
| **recurrence** | obiekt | Tak | Brak | Obiekt cyklu | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **interval** | numer | Nie | 1 | Od 1 do 1000 | `"interval":10` |
| **endTime** | string | Tak | Brak | Wartość daty i godziny reprezentująca godzinę w przyszłości | `"endTime" : "2013-02-09T09:30:00-08:00"` |
| **schedule** | obiekt | Nie | Brak | Obiekt harmonogramu | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="starttime-property"></a>Właściwość startTime
W poniższej tabeli przedstawiono, w jaki sposób właściwość **startTime** kontroluje uruchamianie wyzwalacza:

| Wartość startTime | Cykl bez harmonogramu | Cykl z harmonogramem |
|:--- |:--- |:--- |
| **Czas rozpoczęcia jest w przeszłości** | Oblicza pierwszy przyszły czas wykonania po czasie rozpoczęcia i uruchamia wyzwalacz w tym czasie.<br /><br />Uruchamia kolejne wykonania obliczone na podstawie ostatniego czasu wykonania.<br /><br />Zobacz przykład znajdujący się pod tabelą. | Wyzwalacz jest uruchamiany _nie wcześniej niż_ w określonym czasie rozpoczęcia. Pierwsze wystąpienie opiera się na harmonogramie obliczonym na podstawie czasu rozpoczęcia.<br /><br />Kolejne wykonania opierają na harmonogramie cyklu. |
| **Czas rozpoczęcia jest w przyszłości lub jest to bieżący czas** | Uruchamiany raz o określonym czasie rozpoczęcia.<br /><br />Uruchamia kolejne wykonania obliczone na podstawie ostatniego czasu wykonania. | Wyzwalacz jest uruchamiany _nie wcześniej niż_ w określonym czasie rozpoczęcia. Pierwsze wystąpienie opiera się na harmonogramie obliczonym na podstawie czasu rozpoczęcia.<br /><br />Kolejne wykonania opierają na harmonogramie cyklu. |

Rozpatrzmy przykład zdarzeń w przypadku, gdy czas rozpoczęcia przypada w przeszłości, z cyklem, ale bez harmonogramu. Załóżmy, że obecna data i godzina to 13:00 08-04-2017, czas rozpoczęcia to 14:00 07-04-2017, a cykl jest określony na 2 dni. (Wartość właściwości **recurrence** definiuje się, ustawiając właściwość **frequency** na wartość „day” („dzień”) i właściwość **interval** na wartość 2). Zwróć uwagę, że wartość właściwości **startTime** dotyczy przeszłej daty i następuje przed aktualną godziną.

W tych warunkach pierwsze wykonanie następuje 2017-04-09 o 14:00. Aparat harmonogramu oblicza wystąpienia wykonania od czasu rozpoczęcia. Wszystkie wystąpienia w przeszłości są odrzucane. Aparat wykorzystuje następne wystąpienie, które ma miejsce w przyszłości. W tym scenariuszu godziną rozpoczęcia jest 14:00 w dniu 07-04-2017. Następne wystąpienie zostanie uruchomione za dwa dni od tego czasu, czyli o 14:00 w dniu 09-04-2017.

Czas pierwszego wykonania jest identyczny, nawet jeśli właściwość **startTime** ma wartość 14:00 05-04-2017 lub 14:00 01-04-2017. Po pierwszym wykonaniu kolejne wykonania są obliczane przy użyciu harmonogramu. W związku z tym kolejne wykonania zostaną uruchomione o 14:00 w dniu 11-04-2017, a następnie o 14:00 w dniu 13-04-2017, o 14:00 w dniu 15-04-2017 i tak dalej.

Gdy z kolei w harmonogramie wyzwalacza nie ustawiono godzin lub minut, wartością domyślną będą godziny lub minuty pierwszego wykonania.

### <a name="schedule-property"></a>Wartość schedule
Właściwości **schedule** można użyć do *ograniczenia* liczby wykonań wyzwalacza. Jeśli na przykład uruchomienie wyzwalacza z częstotliwością miesięczną zaplanowano na 31. dzień, wyzwalacz będzie uruchamiany tylko w miesiącach, które mają trzydzieści jeden dni.

Właściwości **schedule** możesz też użyć do *zwiększenia* liczby wykonań wyzwalacza. Na przykład wyzwalacz z częstotliwością miesięczną, którego uruchomienie zaplanowano na 1. i 2. dzień miesiąca, będzie uruchamiany pierwszego i drugiego dnia miesiąca, a nie raz na miesiąc.

W przypadku określenia wielu elementów właściwości **schedule** ocena następuje od największego do najmniejszego ustawienia harmonogramu: numeru tygodnia, dnia miesiąca, dnia tygodnia, godziny i minuty.

W poniższej tabeli opisano szczegółowo elementy właściwości **schedule**:

| Element JSON | Opis | Prawidłowe wartości |
|:--- |:--- |:--- |
| **minutes** | Minuty godziny, o których uruchamiany jest wyzwalacz. |- Liczba całkowita<br />- Tablica liczb całkowitych|
| **hours** | Godziny dnia, o których uruchamiany jest wyzwalacz. |- Liczba całkowita<br />- Tablica liczb całkowitych|
| **weekDays** | Dni tygodnia, w które uruchamiany jest wyzwalacz. Wartość można określić tylko z częstotliwością tygodniową.|<br />- Poniedziałek<br />- Wtorek<br />- Środa<br />- Czwartek<br />- Piątek<br />- Sobota<br />- Niedziela<br />- Tablica wartości dni (maksymalny rozmiar tablicy to 7)<br /><br />W wartościach dni nie są uwzględniane wielkości liter|
| **monthlyOccurrences** | Dni miesiąca, w których uruchamiany jest wyzwalacz. Wartość można określić tylko z częstotliwością miesięczną. |-Tablica **monthlyOccurrence** obiektów: `{ "day": day, "occurrence": occurrence }`<br />- Atrybut **day** jest dniem tygodnia, w którym uruchamiany jest wyzwalacz. Na przykład właściwość **monthlyOccurrences** o wartości **day** wynoszącej `{Sunday}` oznacza każdą niedzielę miesiąca. Atrybut **day** jest wymagany.<br />- Atrybut **occurence** jest wystąpieniem określonej wartości **day** w miesiącu. Na przykład właściwość **monthlyOccurrences** o wartościach **day** i **occurence** wynoszących `{Sunday, -1}` oznacza ostatnią niedzielę miesiąca. Atrybut **occurence** jest opcjonalny.|
| **monthDays** | Dzień miesiąca, w którym uruchamiany jest wyzwalacz. Wartość można określić tylko z częstotliwością miesięczną. |- Dowolna wartość <= -1 i >= -31<br />- Dowolna wartość >= 1 i <= 31<br />- Tablica wartości|

## <a name="tumbling-window-trigger"></a>Wyzwalacz okna wirowania
Wyzwalacze okna wirowania to rodzaj wyzwalaczy uruchamianych w określonych odstępach czasu od wskazanego czasu rozpoczęcia przy zachowaniu stanu. Okna wirowania to ciągłe, nienakładające się na siebie serie odstępów czasu o stałych rozmiarach.

Aby uzyskać więcej informacji o wyzwalaczach okna wirowania i przykładach, zobacz [Tworzenie wyzwalacza okna wirowania](how-to-create-tumbling-window-trigger.md).

## <a name="event-based-trigger"></a>Wyzwalacz oparty na zdarzeniach

Wyzwalacz usługi oparte na zdarzeniach uruchamia potoki w odpowiedzi na zdarzenia, takiego jak otrzymanie pliku lub usunięcie pliku, w usłudze Azure Blob Storage.

Aby uzyskać więcej informacji dotyczących wyzwalaczy opartych na zdarzeniach, zobacz [Create a trigger that runs a pipeline in response to an event](how-to-create-event-trigger.md) (Tworzenie wyzwalacza uruchamiającego potok w odpowiedzi na zdarzenie).

## <a name="examples-of-trigger-recurrence-schedules"></a>Przykłady harmonogramów cyklu wyzwalaczy
Ta sekcja zawiera przykłady harmonogramów cyklu. Koncentruje się ona na obiekcie **schedule** i jego elementach.

W przykładach założono, ze wartość właściwości **interval** wynosi 1 oraz że wartość właściwości **frequency** jest prawidłowa i zgodna z definicją harmonogramu. Na przykład nie można mieć wartości **frequency** równej „day” i jednocześnie mieć modyfikacji **monthDays** w obiekcie **schedule**. Tego rodzaju ograniczenia są opisane w tabeli w poprzedniej sekcji.

| Przykład | Opis |
|:--- |:--- |
| `{"hours":[5]}` | Uruchamiany o godz. 5:00 każdego dnia. |
| `{"minutes":[15], "hours":[5]}` | Uruchamiany o godz. 5:15 każdego dnia. |
| `{"minutes":[15], "hours":[5,17]}` | Uruchamiany o godz. 5:15 i 17:15 każdego dnia. |
| `{"minutes":[15,45], "hours":[5,17]}` | Uruchamiany o godz. 5:15, 5:45, 17:15 i 17:45 każdego dnia. |
| `{"minutes":[0,15,30,45]}` | Uruchamiany co 15 minut. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Uruchamiany co godzinę.<br /><br />Ten wyzwalacz jest uruchamiany co godzinę. Minuty są kontrolowane przez wartość właściwości **startTime**, gdy wartość jest określona. Jeśli wartość nie jest określona, minuty są kontrolowane przez czas utworzenia. Jeśli na przykład czas rozpoczęcia lub czas utworzenia (zależnie od sytuacji) to 12:25, wyzwalacz jest uruchamiany o godzinie 0:25, 1:25, 2:25, ..., 23:25.<br /><br />Ten harmonogram jest odpowiednikiem wyzwalacza z właściwością **frequency** o wartości „hour”, właściwością **interval** o wartości 1 i bez właściwości **schedule**. Tego harmonogramu można także używać z innymi wartościami właściwości **frequency** i **interval** do tworzenia innych wyzwalaczy. Gdy na przykład właściwość **frequency** ma wartość „month”, harmonogram jest uruchamiany tylko raz w miesiącu, a nie każdego dnia, gdy właściwość **frequency** ma wartość „day”. |
| `{"minutes":[0]}` | Uruchamiany co godzinę o pełnej godzinie.<br /><br />Ten wyzwalacz jest uruchamiany co godzinę o pełnej godzinie począwszy od godz. 0:00, a następnie o 1:00, 2:00 itd.<br /><br />Ten harmonogram jest odpowiednikiem wyzwalacza z właściwością **frequency** o wartości „hour” i właściwością **startTime** o wartości 0 minut lub bez właściwości **schedule**, ale z właściwością **frequency** o wartości „day”. Jeśli wartość właściwości **frequency** to „week” lub „month”, harmonogram jest wykonywany odpowiednio tylko raz w tygodniu lub raz w miesiącu. |
| `{"minutes":[15]}` | Uruchamiany 15 minut po pełnej godzinie.<br /><br />Ten wyzwalacz jest uruchamiany 15 minut po pełnej godzinie począwszy od godz. 0:15, a następnie o 1:15, 2:15 itd. aż do 23.15. |
| `{"hours":[17], "weekDays":["saturday"]}` | Uruchamiany o godz. 17:00 w każdą sobotę. |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Uruchamiany o godz. 17:00 w każdy poniedziałek, środę i piątek. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Uruchamiany o godz. 17:15 i 17:45 w każdy poniedziałek, środę i piątek. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Uruchamiany co 15 minut w dni robocze. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Uruchamiany co 15 minut w dni robocze, między godz. 9:00 a 16:45. |
| `{"weekDays":["tuesday", "thursday"]}` | Uruchamiany we wtorki i czwartki o określonej godzinie. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | Uruchamiany o godz. 6:00 dwudziestego ósmego dnia każdego miesiąca (zakładając, że właściwość **frequency** ma wartość „month”). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Uruchamiany o godz. 6:00 ostatniego dnia miesiąca.<br /><br />Aby uruchomić wyzwalacz ostatniego dnia miesiąca, użyj wartości -1 zamiast 28, 29, 30 lub 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Uruchamiany o godz. 6:00 pierwszego i ostatniego dnia każdego miesiąca. |
| `{monthDays":[1,14]}` | Uruchamiany pierwszego i czternastego dnia każdego miesiąca o określonej godzinie. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Uruchamiany w pierwszy piątek każdego miesiąca o godz. 5:00. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Uruchamiany w pierwszy piątek każdego miesiąca o określonej godzinie. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | Uruchamiany w trzeci piątek od końca każdego miesiąca o określonej godzinie. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Uruchamiany w pierwszy i ostatni piątek każdego miesiąca o godz. 5:00. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Uruchamiany w pierwszy i ostatni piątek każdego miesiąca o określonej godzinie. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Uruchamiany w piąty piątek każdego miesiąca o określonej godzinie.<br /><br />Gdy w miesiącu nie ma piątego piątku, potok nie zostanie uruchomiony. Aby uruchomić wyzwalacz w ostatni piątek miesiąca, rozważ użycie wartości -1 zamiast 5 dla właściwości **occurrence**. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Uruchamiany co 15 minut w ostatni piątek miesiąca. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Uruchamiany o godz. 5:15, 5:45, 17:15 i 17:45 w trzecią środę każdego miesiąca. |

## <a name="trigger-type-comparison"></a>Porównanie typów wyzwalaczy
Zarówno wyzwalacz okna wirowania, jak i wyzwalacz harmonogramu pracują w oparciu o pulsy czasu. Czym się od siebie różnią?

Poniższa tabela zawiera porównanie wyzwalacza okna wirowania i wyzwalacza harmonogramu:

|  | Wyzwalacz okna wirowania | Wyzwalacz harmonogramu |
|:--- |:--- |:--- |
| **Scenariusze wypełniania** | Obsługiwane. Uruchomienia potoków można planować dla okien w przeszłości. | Nieobsługiwane. Uruchomienia potoków można wykonywać tylko w teraźniejszych i przyszłych okresach czasu. |
| **Niezawodność** | 100-procentowa niezawodność. Uruchomienia potoków można zaplanować dla wszystkich okien od określonej daty rozpoczęcia bez przerw. | Mniej niezawodne. |
| **Możliwość ponowienia próby** | Obsługiwane. Uruchomienia potoków zakończone niepowodzeniem mają domyślne zasady ponawiania próby ustawione na wartość 0 lub zasady określone przez użytkownika w definicji wyzwalacza. Automatycznie ponawia próbę, gdy uruchomienia potoku zakończą się niepowodzeniem z powodu limitów współbieżności/serwerów/ograniczania (czyli kody stanu 400: Błąd użytkownika, 429: Zbyt wiele żądań i 500: Wewnętrzny błąd serwera). | Nieobsługiwane. |
| **Współbieżność** | Obsługiwane. Użytkownicy mogą jawnie ustawić limity współbieżności dla wyzwalacza. Umożliwia od 1 do 50 jednocześnie wyzwolonych uruchomień potoków. | Nieobsługiwane. |
| **Zmienne systemu** | Obsługuje używanie zmiennych systemowych **WindowStart** i **WindowEnd**. Użytkownicy mogą uzyskiwać dostęp do zmiennych `triggerOutputs().windowStartTime` i `triggerOutputs().windowEndTime` jako zmiennych systemu wyzwalacza w definicji wyzwalacza. Wartości są używane odpowiednio jako czas rozpoczęcia okna i czas zakończenia okna. Na przykład w przypadku wyzwalacza okna wirowania uruchamianego co godzinę dla okna od godz. 1:00 do 2:00 definicją jest zmienna `triggerOutputs().WindowStartTime = 2017-09-01T01:00:00Z` i `triggerOutputs().WindowEndTime = 2017-09-01T02:00:00Z`. | Nieobsługiwane. |
| **Relacja potoku do wyzwalacza** | Obsługuje relację „jeden do jednego”. Można wyzwolić tylko jeden potok. | Obsługuje relacje „wiele do wielu”. Wiele wyzwalaczy może uruchomić jeden potok. Jeden wyzwalacz może uruchamiać wiele potoków. |

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące samouczki:

- [Szybki start: Tworzenie fabryki danych przy użyciu zestawu .NET SDK](quickstart-create-data-factory-dot-net.md)
- [Tworzenie wyzwalacza harmonogramu](how-to-create-schedule-trigger.md)
- [Tworzenie wyzwalacza okna wirowania](how-to-create-tumbling-window-trigger.md)
