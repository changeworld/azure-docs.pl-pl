---
title: Tworzenie wyzwalaczy okno wirowania w fabryce danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć wyzwalacza w fabryce danych Azure, uruchamianego potoku na okno wirowania.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: shlo
ms.openlocfilehash: 02f84047d0e1d3e73fac991250da814176f3995d
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049928"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Utwórz wyzwalacz, który działa na okno wirowania potoku
Ten artykuł zawiera kroki, aby utworzyć, uruchomić i monitorować wyzwalacz okno wirowania. Aby uzyskać ogólne informacje o wyzwalaczy i obsługiwane typy, zobacz [potoku wykonywania i wyzwalaczy](concepts-pipeline-execution-triggers.md).

Wyzwalacze okna wirowania to rodzaj wyzwalaczy uruchamianych w określonych odstępach czasu od wskazanego czasu rozpoczęcia przy zachowaniu stanu. Okna wirowania to ciągłe, nienakładające się na siebie serie odstępów czasu o stałych rozmiarach. Wyzwalacz okno wirowania ma relacje jeden do jednego z potokiem i może się odwoływać tylko do pojedynczej potoku.

## <a name="tumbling-window-trigger-type-properties"></a>Właściwości typu wyzwalacza okno wirowania
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
                "count":  <<int - optional, default: 0>>,
                “intervalInSeconds”: <<int>>,
            }
        },
        "pipeline":
            {
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

Poniższa tabela zawiera omówienie głównych elementów JSON, które są powiązane z cyklu i planowania wyzwalacza okno wirowania:

| Element JSON | Opis | Typ | Dozwolone wartości | Wymagane |
|:--- |:--- |:--- |:--- |:--- |
| **type** | Typ wyzwalacza. Typ jest wartością stałą "TumblingWindowTrigger." | Ciąg | "TumblingWindowTrigger" | Yes |
| **runtimeState** | Bieżący stan czasu wykonywania wyzwalacza.<br/>**Uwaga**: ten element jest \<tylko do odczytu >. | Ciąg | "Uruchomiona", "zatrzymana," "Wyłączone" | Yes |
| **frequency** | Ciąg, który reprezentuje jednostkę częstotliwość (w minutach lub godzinach) powtarzania wyzwalacza. Jeśli **startTime** wartości daty są bardziej szczegółowego niż **częstotliwość** wartość **startTime** daty są traktowane jako, gdy są obliczane granice okna. Na przykład jeśli **częstotliwość** wartość to co godzinę i **startTime** wartość jest 2016-04-01T10:10:10Z, pierwsze okno jest (2017 — 09 — 01T10:10:10Z, 2017-09 — 01T11:10:10Z). | Ciąg | "minute", "Godzina"  | Yes |
| **interval** | Dodatnia liczba całkowita oznaczająca interwał wartości właściwości **frequency**, która określa częstotliwość uruchamiania wyzwalacza. Na przykład jeśli **interwał** 3 i **częstotliwość** to "Godzina", wyzwalacz wystąpi co 3 godziny. | Liczba całkowita | Dodatnia liczba całkowita. | Yes |
| **startTime**| Pierwsze wystąpienie, które mogą znajdować się w przeszłości. Pierwszy interwał wyzwalacza (**startTime**, **startTime** + **interwał**). | DateTime | Wartość daty i godziny. | Yes |
| **endTime**| Ostatnie wystąpienie, które mogą znajdować się w przeszłości. | DateTime | Wartość daty i godziny. | Yes |
| **delay** | Ilość czasu opóźnienie przetwarzania danych w oknie. Uruchom potoku została uruchomiona po oczekiwany czas wykonywania oraz ilość **opóźnienie**. **Opóźnienie** określa czas oczekiwania wyzwalacza późniejsza niż godzina ukończenia, aby mogło nastąpić wyzwolenie nowy przebieg. **Opóźnienie** nie powoduje zmian okna **startTime**. Na przykład **opóźnienie** wartość 00:10:00 oznacza 10 minutach. | Zakres czasu  | Wartość czasu, gdy wartość domyślna to 00:00:00. | Nie |
| **maxConcurrency** | Liczba uruchomień jednoczesnych wyzwalacza, które są uruchamiane dla systemu windows, które są gotowe. Na przykład kopii wypełnienia co godzinę trwający wczoraj powoduje 24 systemu windows. Jeśli **maxConcurrency** = 10, wyzwalacz zdarzenia są generowane tylko dla systemu windows 10 pierwszych (00:00-01:00 - 09:00-10:00). Po zakończeniu 10 pierwszych uruchamia wyzwalanych potoku uruchamia wyzwalacz są uruchamiane dla kolejnych 10 systemu windows (10:00-11:00-19:00-20:00). Kontynuowanie z tym przykładem **maxConcurrency** = 10, jeśli istnieją 10 systemu windows, które są gotowe, istnieją 10 uruchamia całkowita potoku. Jeśli istnieje tylko 1 okno gotowy, jest tylko 1 wykonywania potoku. | Liczba całkowita | Liczba całkowita od 1 do 50. | Yes |
| **retryPolicy: liczba** | Liczba ponownych prób przed uruchomieniem potoku jest oznaczony jako "Nieudane".  | Liczba całkowita | Liczba całkowita, gdzie wartość domyślna to 0 (brak ponownych prób). | Nie |
| **retryPolicy: element intervalInSeconds** | Opóźnienie między ponownymi próbami określony w sekundach. | Liczba całkowita | Liczba sekund, gdzie wartość domyślna to 30. | Nie |

### <a name="windowstart-and-windowend-system-variables"></a>Zmienne systemu WindowStart i WindowEnd

Można użyć **WindowStart** i **WindowEnd** zmienne systemowe wyzwalacza okno wirowania w Twojej **potoku** definicji (oznacza to, że części zapytania). Przekaż zmienne systemowe jako parametry do potoku sieci w **wyzwalacza** definicji. Poniższy przykład przedstawia sposób przekazywania tych zmiennych jako parametry:

```  
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
            ...
        "pipeline":
            {
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

Aby użyć **WindowStart** i **WindowEnd** odpowiednio parametry "MyWindowStart" i "MyWindowEnd", użyj systemu wartości zmiennych w definicji potoku.

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>Kolejność wykonywania systemu windows w scenariuszu uzupełniania
Istnieje wiele systemu windows do wykonywania (szczególnie w scenariuszu uzupełniania), kolejność wykonywania dla systemu windows jest deterministyczna, od najstarszych do najnowszych odstępach czasu. Obecnie nie można modyfikować tego zachowania.

### <a name="existing-triggerresource-elements"></a>Istniejące elementy TriggerResource
Mają zastosowanie następujące kwestie istniejących **TriggerResource** elementy:

* Jeśli wartość **częstotliwość** elementu (lub rozmiar okna) zmian wyzwalacza, stan systemu windows, które już są przetwarzane jest *nie* resetowania. Wyzwalacz w dalszym ciągu wyzwalać dla systemu windows z oknie ostatniego wykonania przy użyciu nowego rozmiaru okna.
* Jeśli wartość **endTime** jest element wyzwalacza zmiany (dodano lub zaktualizowano), stan systemu windows, które już są przetwarzane *nie* resetowania. Wyzwalacz honoruje nowe **endTime** wartość. Jeśli nowy **endTime** wartość jest przed okna, które są już wykonane zatrzymuje wyzwalacza. W przeciwnym razie wyzwalacza zatrzymuje, gdy nowy **endTime** napotkano wartość.

## <a name="sample-for-azure-powershell"></a>Przykład dla programu Azure PowerShell
W tej sekcji przedstawiono sposób tworzenia, uruchomić i monitorować wyzwalacz przy użyciu programu Azure PowerShell.

1. Utwórz plik JSON o nazwie **MyTrigger.json** w folderze C:\ADFv2QuickStartPSH\ o następującej treści:

   > [!IMPORTANT]
   > Przed zapisaniem pliku JSON, ustaw wartość **startTime** element bieżący czas UTC. Ustaw wartość **endTime** elementu godzinę poza bieżący czas UTC.

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

2. Utwórz wyzwalacz za pomocą **AzureRmDataFactoryV2Trigger zestaw** polecenia cmdlet:

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
    
3. Upewnij się, że jest w stanie wyzwalacza **zatrzymane** za pomocą **Get-AzureRmDataFactoryV2Trigger** polecenia cmdlet:

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Uruchomić wyzwalacz za pomocą **Start AzureRmDataFactoryV2Trigger** polecenia cmdlet:

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Upewnij się, że jest w stanie wyzwalacza **uruchomiono** za pomocą **Get-AzureRmDataFactoryV2Trigger** polecenia cmdlet:

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. GET, wyzwalacz uruchamia w programie Azure PowerShell przy użyciu **Get-AzureRmDataFactoryV2TriggerRun** polecenia cmdlet. Aby uzyskać informacje o uruchomieniu wyzwalacza, uruchom następujące polecenie okresowo. Aktualizacja **TriggerRunStartedAfter** i **TriggerRunStartedBefore** wartości są zgodne z wartościami w definicji wyzwalacz:

    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
Do monitorowania wyzwalacz uruchamia i potoku jest uruchamiana w portalu Azure, zobacz [uruchamia Monitor potoku](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać szczegółowe informacje dotyczące wyzwalaczy, zobacz [potoku wykonywania i wyzwalaczy](concepts-pipeline-execution-triggers.md#triggers).
