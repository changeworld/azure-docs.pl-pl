---
title: Utwórz wyzwalacze okna wirowania w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć wyzwalacz w usłudze Azure Data Factory uruchamia potok na okno wirowania.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: shlo
ms.openlocfilehash: 6fbdee71ab1123c258a5191a78e38f51eb41cbab
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433233"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Utwórz wyzwalacz, który uruchamia potok okna wirowania
Ten artykuł zawiera kroki umożliwiające tworzenie, uruchamianie i monitorowanie wyzwalacza okna wirowania. Aby uzyskać ogólne informacje na temat wyzwalaczy i obsługiwanych typów, zobacz [wyzwalacze i wykonywanie potoku](concepts-pipeline-execution-triggers.md).

Wyzwalacze okna wirowania to rodzaj wyzwalaczy uruchamianych w określonych odstępach czasu od wskazanego czasu rozpoczęcia przy zachowaniu stanu. Okna wirowania to ciągłe, nienakładające się na siebie serie odstępów czasu o stałych rozmiarach. Wyzwalacz okna wirowania ma relację jeden do jednego z potokiem i mogą odwoływać się tylko do pojedynczej potoku.

## <a name="data-factory-ui"></a>Interfejs użytkownika usługi Data Factory

Aby utworzyć wyzwalacz okna wirowania w witrynie Azure portal, wybierz **wyzwalacza > okno wirowania > Dalej**, a następnie skonfiguruj właściwości, które definiują okno wirowania.

![Tworzenie wyzwalacza okna wirowania w witrynie Azure portal](media/how-to-create-tumbling-window-trigger/create-tumbling-window-trigger.png)

## <a name="tumbling-window-trigger-type-properties"></a>Właściwości typu wyzwalacza okna wirowania
Okno wirowania ma następujące właściwości typu wyzwalacza:

```
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": "<<Minute/Hour>>",
            "interval": <<int>>,
            "startTime": "<<datetime>>",
            "endTime: "<<datetime – optional>>"",
            "delay": "<<timespan – optional>>",
            “maxConcurrency”: <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                “intervalInSeconds”: <<int>>,
            }
        },
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipelineName"
            },
            "parameters": {
                "parameter1": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "parameter2": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "parameter3": "https://mydemo.azurewebsites.net/api/demoapi"
            }
        }
    }
}
```

Poniższa tabela zawiera ogólne omówienie głównych elementów JSON, które są powiązane do powtarzania i planowania wyzwalacza okna wirowania:

| Element JSON | Opis | Type | Dozwolone wartości | Wymagane |
|:--- |:--- |:--- |:--- |:--- |
| **type** | Typ wyzwalacza. Typ jest wartością stałą "TumblingWindowTrigger." | String | "TumblingWindowTrigger" | Yes |
| **runtimeState** | Bieżący stan wyzwalacza czasu wykonywania.<br/>**Uwaga**: Ten element jest \<tylko do odczytu >. | String | "Started", "zatrzymana," "Wyłączone" | Yes |
| **frequency** | Ciąg, który reprezentuje jednostkę częstotliwość (minuty lub godziny) powtarzania wyzwalacza. Jeśli **startTime** wartości daty są bardziej szczegółowe niż **częstotliwość** wartość **startTime** daty są traktowane jako, gdy są obliczane w granicach okna. Na przykład jeśli **częstotliwość** wartość to co godzinę i **startTime** wartość 2017 — 09 — 01T10:10:10Z, pierwsze okno jest (2017-09 — 01T10:10:10Z, 2017-09 — 01T11:10:10Z). | String | "minute", "hour"  | Yes |
| **interval** | Dodatnia liczba całkowita oznaczająca interwał wartości właściwości **frequency**, która określa częstotliwość uruchamiania wyzwalacza. Na przykład jeśli **interwał** 3 i **częstotliwość** jest "hour" wyzwalacz jest powtarzany co 3 godziny. | Liczba całkowita | Dodatnia liczba całkowita. | Yes |
| **startTime**| Pierwsze wystąpienie, które mogą znajdować się w przeszłości. Pierwszy interwał wyzwalacza (**startTime**, **startTime** + **interwał**). | DateTime | Wartość daty/godziny. | Yes |
| **endTime**| Ostatnie wystąpienie, które mogą znajdować się w przeszłości. | DateTime | Wartość daty/godziny. | Yes |
| **delay** | Ilość czasu, opóźnienie przetwarzania danych dla okna. Uruchomienia potoku została uruchomiona po oczekiwanym czasie wykonywania oraz ilość **opóźnienie**. **Opóźnienie** definiuje, jak długo czekać wyzwalacz późniejsza niż godzina ukończenia przed wyzwoleniem uruchomienie nowego przebiegu. **Opóźnienie** nie powoduje zmian okna **startTime**. Na przykład **opóźnienie** wartość 00:10:00 oznacza opóźnieniem 10 minut. | Zakres czasu<br/>(: mm: ss)  | Wartość przedziału czasu, w którym wartość domyślna to 00:00:00. | Nie |
| **maxConcurrency** | Liczba uruchomień wyzwalacza jednoczesnych, które są uruchamiane dla systemu windows, które są gotowe. Na przykład aby wypełnienie tyłu co godzinę jest uruchamiane dla wyników wczoraj w 24 w systemie windows. Jeśli **maxConcurrency** = 10, wyzwalacz zdarzenia są uruchamiane tylko w przypadku systemu windows 10 pierwszych (00:00-01:00 - 09:00-10:00). Po zakończeniu 10 pierwszych wyzwolonych uruchomień potoków uruchomienia wyzwalacza są uruchamiane dla systemu windows 10 (10:00-11:00 – 19:00-20:00). Kontynuując ten przykład **maxConcurrency** = 10, w przypadku windows 10, wszystko będzie gotowe, istnieją 10 uruchomienia potoku całkowitej. Jeśli jest dostępny tylko 1 oknie gotowy, jest dostępny tylko 1 uruchomienie potoku. | Liczba całkowita | Liczba całkowita od 1 do 50. | Yes |
| **retryPolicy: Count** | Liczba ponownych prób przed uruchomieniem potoku jest oznaczony jako "Nieudane".  | Liczba całkowita | Liczba całkowita, gdzie wartość domyślna to 0 (bez ponawiania). | Nie |
| **retryPolicy: intervalInSeconds** | Opóźnienie między ponownymi próbami określony w ciągu kilku sekund. | Liczba całkowita | Liczba sekund, gdzie wartość domyślna to 30. | Nie |

### <a name="windowstart-and-windowend-system-variables"></a>WindowStart i WindowEnd zmienne systemowe

Możesz użyć **WindowStart** i **WindowEnd** zmiennych systemu wyzwalacza okna wirowania w swojej **potoku** definicji (oznacza to, dla części zapytania). Przekazać zmienne systemowe jako parametry do potoku w **wyzwalacza** definicji. Poniższy przykład pokazuje, jak przekazać te zmienne jako parametry:

```
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
            ...
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipelineName"
            },
            "parameters": {
                "MyWindowStart": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "MyWindowEnd": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                }
            }
        }
    }
}
```

Aby użyć **WindowStart** i **WindowEnd** odpowiednio parametry "MyWindowStart" i "MyWindowEnd", użyj wartości zmiennych systemowych w definicji potoku.

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>Kolejność wykonywania systemu windows w scenariuszu wypełniania
Istnieje wiele okien na potrzeby wykonywania (szczególnie w przypadku scenariusza wypełniania), kolejność wykonywania dla systemu windows jest deterministyczna, od najstarszych do najnowszych odstępach czasu. Obecnie nie można zmodyfikować tego zachowania.

### <a name="existing-triggerresource-elements"></a>Istniejące elementy TriggerResource
Obowiązują następujące reguły do istniejącej **TriggerResource** elementy:

* Jeśli wartość **częstotliwość** elementu (lub rozmiar okna) zmian wyzwalacza, stan systemu windows, które już przetwarzane jest *nie* resetowania. Wyzwalacz w dalszym ciągu uruchamiane w przypadku systemu windows z poziomu okna ostatniego wykonywanego przy użyciu nowego rozmiaru okna.
* Jeśli wartość **endTime** elementu wyzwalacza zmiany (dodania lub zaktualizowania), stan systemu windows, które już są przetwarzane *nie* resetowania. Wyzwalacz honoruje nowy **endTime** wartość. Jeśli nowy **endTime** wartość przed systemu windows, które są już wykonane zatrzymuje wyzwalacza. W przeciwnym razie wyzwalacza, zatrzymuje się, gdy nowy **endTime** napotkano wartość.

## <a name="sample-for-azure-powershell"></a>Przykład dla programu Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tej sekcji dowiesz się, jak tworzenie, uruchamianie i monitorowanie wyzwalacz za pomocą programu Azure PowerShell.

1. Utwórz plik JSON o nazwie **MyTrigger.json** w folderze C:\ADFv2QuickStartPSH\ o następującej zawartości:

    > [!IMPORTANT]
    > Przed zapisaniem pliku JSON, ustaw wartość **startTime** element do bieżącego czasu UTC. Ustaw wartość **endTime** element na jedną godzinę w przeszłości bieżący czas UTC.

    ```json
    {
      "name": "PerfTWTrigger",
      "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
          "frequency": "Minute",
          "interval": "15",
          "startTime": "2017-09-08T05:30:00Z",
          "delay": "00:00:01",
          "retryPolicy": {
            "count": 2,
            "intervalInSeconds": 30
          },
          "maxConcurrency": 50
        },
        "pipeline": {
          "pipelineReference": {
            "type": "PipelineReference",
            "referenceName": "DynamicsToBlobPerfPipeline"
          },
          "parameters": {
            "windowStart": "@trigger().outputs.windowStartTime",
            "windowEnd": "@trigger().outputs.windowEndTime"
          }
        },
        "runtimeState": "Started"
      }
    }
    ```

2. Tworzenie wyzwalacza za pomocą **AzDataFactoryV2Trigger zestaw** polecenia cmdlet:

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
    
3. Upewnij się, że jest w stanie wyzwalacz **zatrzymane** przy użyciu **Get AzDataFactoryV2Trigger** polecenia cmdlet:

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Uruchom wyzwalacz za pomocą **Start AzDataFactoryV2Trigger** polecenia cmdlet:

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Upewnij się, że jest w stanie wyzwalacz **uruchomiono** przy użyciu **Get AzDataFactoryV2Trigger** polecenia cmdlet:

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. GET, o których uruchamiany jest wyzwalacz w programie Azure PowerShell przy użyciu **Get AzDataFactoryV2TriggerRun** polecenia cmdlet. Aby uzyskać informacje na temat uruchomienia wyzwalacza, wykonaj następujące polecenie okresowo. Aktualizacja **TriggerRunStartedAfter** i **TriggerRunStartedBefore** wartości odpowiadają wartościom w definicji wyzwalacza:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
Do monitorowania potoku i uruchomienia wyzwalacza jest uruchamiany w witrynie Azure portal, zobacz [monitorowanie uruchomień potoku](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać szczegółowe informacje na temat wyzwalaczy, zobacz [wyzwalacze i wykonywanie potoku](concepts-pipeline-execution-triggers.md#triggers).
