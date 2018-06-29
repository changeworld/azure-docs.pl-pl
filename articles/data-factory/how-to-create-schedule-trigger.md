---
title: Tworzenie harmonogramu wyzwalaczy w fabryce danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć wyzwalacza w fabryce danych Azure z systemem potoku zgodnie z harmonogramem.
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
ms.date: 01/23/2018
ms.author: shlo
ms.openlocfilehash: eee68481f4396f8a09241b664d4c3d7d4a4f6567
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054356"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>Utwórz wyzwalacz uruchamia potoku zgodnie z harmonogramem
Ten artykuł zawiera informacje o wyzwalacza harmonogramu oraz instrukcje umożliwiające tworzenie, uruchomić i monitorować wyzwalacz harmonogramu. Dla innych typów wyzwalaczy, zobacz [potoku wykonywania i wyzwalaczy](concepts-pipeline-execution-triggers.md).

Podczas tworzenia harmonogramu wyzwalacza należy określić harmonogram (rozpoczęcie cyklu, końcowa data itp.) dla wyzwalacza i skojarz z potokiem. Między potokami i wyzwalaczami występuje relacja wiele-do-wielu. Wiele wyzwalaczy może uruchomić jeden potok. Jeden wyzwalacz może uruchamiać wiele potoków.

Poniższe sekcje zawierają kroki, aby utworzyć harmonogram wyzwalacza w inny sposób. 

## <a name="data-factory-ui"></a>Interfejs użytkownika usługi Data Factory
Można utworzyć **wyzwalacza harmonogram** można zaplanować potoku do uruchamiania okresowo (co godzinę, codziennie, itp.). 

> [!NOTE]
> Aby uzyskać pełny Przewodnik tworzenia potoku i wyzwalacz harmonogram kojarzenie wyzwalacza potoku i uruchamiania i monitorowania potoku, zobacz [Szybki Start: tworzenie fabryki danych za pomocą interfejsu użytkownika z fabryki danych](quickstart-create-data-factory-portal.md).

1. Przejdź do karty **Edycja**. 

    ![Przechodzenie do karty Edycja](./media/how-to-create-schedule-trigger/switch-edit-tab.png)
1. Kliknij pozycję **Wyzwalacz** w menu, a następnie kliknij pozycję **Nowy/Edytuj**. 

    ![Menu Nowy wyzwalacz](./media/how-to-create-schedule-trigger/new-trigger-menu.png)
2. Na stronie **Dodawanie wyzwalaczy** kliknij pozycję **Wybierz wyzwalacz...** , a następnie kliknij przycisk **Nowy**. 

    ![Dodawanie wyzwalaczy — nowy wyzwalacz](./media/how-to-create-schedule-trigger/add-trigger-new-button.png)
3. W **nowego wyzwalacza** wykonaj następujące czynności: 

    1. Upewnij się, że **harmonogram** został wybrany do **typu**. 
    2. Określ datę i godzinę rozpoczęcia wyzwalacza dla **Start Data (UTC)**. Domyślnie jest ustawiona do bieżącej daty/godziny. 
    3. Określ **cyklu** wyzwalacza. Wybierz jedną z wartości z listy rozwijanej (co minutę, co godzinę, codziennie, co tydzień i miesięczne). Mnożnik należy wprowadzić w polu tekstowym. Na przykład, jeśli chcesz trigger, aby uruchomić jeden raz dla co 15 minut, wybraniu **co minutę**, a następnie wprowadź **15** w polu tekstowym. 
    4. Dla **zakończenia** pola, jeśli nie chcesz określać daty/godziny zakończenia dla wyzwalacza, wybierz opcję **End nie**. Aby określić Data i godzina zakończenia, zaznacz **na daty**i określ daty/godziny zakończenia i kliknij **Zastosuj**. Z każdym uruchomieniem potoku wiąże się koszt. Podczas testowania, może zajść potrzeba upewnij się, że potok zostanie wywołany tylko kilka razy. Upewnij się również, że okres między czasem publikowania i czasem zakończenia będzie wystarczający do uruchomienia potoku. Wyzwalacz zaczyna obowiązywać dopiero po opublikowaniu rozwiązania w fabryce Data Factory, a nie po zapisaniu go w interfejsie użytkownika.

        ![Ustawienia wyzwalacza](./media/how-to-create-schedule-trigger/trigger-settings.png)
4. W **nowego wyzwalacza** okno wyboru **Activated** opcji, a następnie kliknij przycisk **dalej**. To pole wyboru służy do dezaktywowania wyzwalacza później. 

    ![Ustawienia wyzwalacza — przycisk Dalej](./media/how-to-create-schedule-trigger/trigger-settings-next.png)
5. Na stronie **Nowy wyzwalacz** zapoznaj się z komunikatem ostrzegawczym, a następnie kliknij przycisk **Zakończ**.

    ![Ustawienia wyzwalacza — przycisk Zakończ](./media/how-to-create-schedule-trigger/new-trigger-finish.png)
6. Kliknij pozycję **Opublikuj**, aby opublikować zmiany w fabryce Data Factory. Dopóki Opublikuj zmiany z fabryką danych, wyzwalacz nie można uruchomić wyzwalania uruchamia potoku. 

    ![Przycisk Opublikuj](./media/how-to-create-schedule-trigger/publish-2.png)
8. Przejdź do karty **Monitorowanie** po lewej stronie. Kliknij pozycję **Odśwież**, aby odświeżyć listę. Zobaczysz wyzwalanych przez zaplanowane wyzwalacz uruchamia potoku. Zwróć uwagę na wartości w kolumnie **Wyzwolone przez**. Jeśli używasz **wyzwalacza teraz** opcji, zobacz wyzwalacza ręcznego uruchamiania na liście. 

    ![Monitorowanie wyzwolonych uruchomień](./media/how-to-create-schedule-trigger/monitor-triggered-runs.png)
9. Kliknij strzałkę w dół obok pozycji **Uruchomienia potoków**, aby przejść do widoku **Uruchomienia wyzwalacza**. 

    ![Monitorowanie uruchomień wyzwalacza](./media/how-to-create-schedule-trigger/monitor-trigger-runs.png)

## <a name="azure-powershell"></a>Azure PowerShell
W tej sekcji przedstawiono sposób tworzenia, uruchomić i monitorować wyzwalacz harmonogramu przy użyciu programu Azure PowerShell. Aby wyświetlić pracy tej próbki, najpierw przejść przez [Szybki Start: tworzenie fabryki danych przy użyciu programu Azure PowerShell](quickstart-create-data-factory-powershell.md). Następnie dodaj następujący kod do metody main, która tworzy i uruchamia wyzwalacz harmonogram, który jest uruchamiane co 15 minut. Wyzwalacz jest skojarzony z potokiem o nazwie **Adfv2QuickStartPipeline** utworzonego w ramach szybkiego startu.

1. Utwórz plik JSON o nazwie **MyTrigger.json** w folderze C:\ADFv2QuickStartPSH\ o następującej treści:

    > [!IMPORTANT]
    > Przed zapisaniem pliku JSON, ustaw wartość **startTime** element bieżący czas UTC. Ustaw wartość **endTime** elementu godzinę poza bieżący czas UTC.

    ```json   
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Minute",
                    "interval": 15,
                    "startTime": "2017-12-08T00:00:00",
                    "endTime": "2017-12-08T01:00:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "Adfv2QuickStartPipeline"
                    },
                    "parameters": {
                        "inputPath": "adftutorial/input",
                        "outputPath": "adftutorial/output"
                    }
                }
            ]
        }
    }
    ```

    We fragmencie JSON:
    - **Typu** element wyzwalacza jest ustawiony na wartość "ScheduleTrigger."
    - **Częstotliwość** element jest ustawiony na wartość "Min" i **interwał** element jest ustawiony na wartość 15. W związku z tym wyzwalacz uruchamia potoku co 15 minut od godziny rozpoczęcia i zakończenia.
    - **EndTime** element jest jednej godziny po wartości **startTime** elementu. W związku z tym wyzwalacz uruchamia potoku 15 minut, 30 minut, a 45 minut po czasie rozpoczęcia. Należy pamiętać zaktualizować czas rozpoczęcia bieżącego czasu UTC, a czas zakończenia godzinę późniejsza niż godzina rozpoczęcia. 
    - Wyzwalacz skojarzony z **Adfv2QuickStartPipeline** potoku. Aby skojarzyć wiele potoki z wyzwalaczem, Dodaj więcej **pipelineReference** sekcje.
    - Potok w szybkiego startu przyjmuje dwa **parametry** wartości: **inputPath** i **outputPath**. W związku z tym należy przekazać wartości tych parametrów z wyzwalacza.

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

6.  GET, wyzwalacz uruchamia w programie Azure PowerShell przy użyciu **Get-AzureRmDataFactoryV2TriggerRun** polecenia cmdlet. Aby uzyskać informacje na temat uruchamia wyzwalacz, uruchom następujące polecenie okresowo. Aktualizacja **TriggerRunStartedAfter** i **TriggerRunStartedBefore** wartości są zgodne z wartościami w definicji wyzwalacz:

    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
    Aby monitorować wyzwalacz uruchamia i potoku jest uruchamiana w portalu Azure, zobacz [uruchamia Monitor potoku](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).


## <a name="net-sdk"></a>Zestaw SDK .NET
W tej sekcji przedstawiono sposób użycia zestawu .NET SDK do tworzenia, uruchomić i monitorować wyzwalacz. Aby wyświetlić pracy tej próbki, najpierw przejść przez [Szybki Start: tworzenie fabryki danych przy użyciu zestawu .NET SDK](quickstart-create-data-factory-dot-net.md). Następnie dodaj następujący kod do metody main, która tworzy i uruchamia wyzwalacz harmonogram, który jest uruchamiane co 15 minut. Wyzwalacz jest skojarzony z potokiem o nazwie **Adfv2QuickStartPipeline** utworzonego w ramach szybkiego startu.

Aby utworzyć i uruchomić wyzwalacz harmonogram, który jest uruchamiane co 15 minut, Dodaj następujący kod do metody głównej:

```csharp
            // Create the trigger
            Console.WriteLine("Creating the trigger");

            // Set the start time to the current UTC time
            DateTime startTime = DateTime.UtcNow;

            // Specify values for the inputPath and outputPath parameters
            Dictionary<string, object> pipelineParameters = new Dictionary<string, object>();
            pipelineParameters.Add("inputPath", "adftutorial/input");
            pipelineParameters.Add("outputPath", "adftutorial/output");

            // Create a schedule trigger
            string triggerName = "MyTrigger";
            ScheduleTrigger myTrigger = new ScheduleTrigger()
            {
                Pipelines = new List<TriggerPipelineReference>()
                {
                    // Associate the Adfv2QuickStartPipeline pipeline with the trigger
                    new TriggerPipelineReference()
                    {
                        PipelineReference = new PipelineReference(pipelineName),
                        Parameters = pipelineParameters,
                    }
                },
                Recurrence = new ScheduleTriggerRecurrence()
                {
                    // Set the start time to the current UTC time and the end time to one hour after the start time
                    StartTime = startTime,
                    TimeZone = "UTC",
                    EndTime = startTime.AddHours(1),
                    Frequency = RecurrenceFrequency.Minute,
                    Interval = 15,
                }
            };

            // Now, create the trigger by invoking the CreateOrUpdate method
            TriggerResource triggerResource = new TriggerResource()
            {
                Properties = myTrigger
            };
            client.Triggers.CreateOrUpdate(resourceGroup, dataFactoryName, triggerName, triggerResource);

            // Start the trigger
            Console.WriteLine("Starting the trigger");
            client.Triggers.Start(resourceGroup, dataFactoryName, triggerName);
```

Aby monitorować wyzwalacz uruchomienia, Dodaj następujący kod przed ostatniego `Console.WriteLine` instrukcji w przykładzie:

```csharp
            // Check that the trigger runs every 15 minutes
            Console.WriteLine("Trigger runs. You see the output every 15 minutes");

            for (int i = 0; i < 3; i++)
            {
                System.Threading.Thread.Sleep(TimeSpan.FromMinutes(15));
                List<TriggerRun> triggerRuns = client.Triggers.ListRuns(resourceGroup, dataFactoryName, triggerName, DateTime.UtcNow.AddMinutes(-15 * (i + 1)), DateTime.UtcNow.AddMinutes(2)).ToList();
                Console.WriteLine("{0} trigger runs found", triggerRuns.Count);

                foreach (TriggerRun run in triggerRuns)
                {
                    foreach (KeyValuePair<string, string> triggeredPipeline in run.TriggeredPipelines)
                    {
                        PipelineRun triggeredPipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, triggeredPipeline.Value);
                        Console.WriteLine("Pipeline run ID: {0}, Status: {1}", triggeredPipelineRun.RunId, triggeredPipelineRun.Status);
                        List<ActivityRun> runs = client.ActivityRuns.ListByPipelineRun(resourceGroup, dataFactoryName, triggeredPipelineRun.RunId, run.TriggerRunTimestamp.Value, run.TriggerRunTimestamp.Value.AddMinutes(20)).ToList();
                    }
                }
            }
```

Aby monitorować wyzwalacz uruchamia i potoku jest uruchamiana w portalu Azure, zobacz [uruchamia Monitor potoku](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).


## <a name="python-sdk"></a>Zestaw SDK dla języka Python
W tej sekcji przedstawiono sposób użycia zestawu SDK Python do tworzenia, uruchomić i monitorować wyzwalacz. Aby wyświetlić pracy tej próbki, najpierw przejść przez [Szybki Start: tworzenie fabryki danych przy użyciu zestawu SDK Python](quickstart-create-data-factory-python.md). Następnie należy dodać następujący blok kodu po bloku kodu "Monitoruj potoku uruchamiania" w skrypcie języka Python. Ten kod tworzy wyzwalacz harmonogram uruchamiany co 15 minut od określonego czasu rozpoczęcia i zakończenia. Aktualizacja **godzina_rozpoczęcia** zmiennej bieżący czas UTC i **end_time** zmienną godzinę poza bieżący czas UTC.

```python
    # Create a trigger
    tr_name = 'mytrigger'
    scheduler_recurrence = ScheduleTriggerRecurrence(frequency='Minute', interval='15',start_time='2017-12-12T04:00:00', end_time='2017-12-12T05:00:00', time_zone='UTC')
    pipeline_parameters = {'inputPath':'adftutorial/input', 'outputPath':'adftutorial/output'}
    pipelines_to_run = []
    pipeline_reference = PipelineReference('copyPipeline')
    pipelines_to_run.append(TriggerPipelineReference(pipeline_reference, pipeline_parameters))
    tr_properties = ScheduleTrigger(description='My scheduler trigger', pipelines = pipelines_to_run, recurrence=scheduler_recurrence)    
    adf_client.triggers.create_or_update(rg_name, df_name, tr_name, tr_properties)

    # Start the trigger
    adf_client.triggers.start(rg_name, df_name, tr_name)
```

Aby monitorować wyzwalacz uruchamia i potoku jest uruchamiana w portalu Azure, zobacz [uruchamia Monitor potoku](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager
Szablon usługi Azure Resource Manager służy do tworzenia wyzwalacza. Aby uzyskać instrukcje, zobacz [tworzenie fabryki danych Azure przy użyciu szablonu usługi Resource Manager](quickstart-create-data-factory-resource-manager-template.md).  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>Przekaż wyzwalacza godziny rozpoczęcia dla potoku
Fabryka danych Azure w wersji 1 obsługuje odczytu lub zapisu danych podzielonej na partycje przy użyciu zmiennych systemowych: **SliceStart**, **SliceEnd**, **WindowStart**i **WindowEnd**. W bieżącej wersji usługi fabryka danych Azure to zachowanie można osiągnąć za pomocą parametru potoku. Czas rozpoczęcia i zaplanowanym terminie wyzwalacza są ustawione jako wartość parametru potoku. W poniższym przykładzie zaplanowanego czasu dla wyzwalacza jest przekazywany jako wartość do potoku **scheduledRunTime** parametru:

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```    

Aby uzyskać więcej informacji, zobacz instrukcje w [jak do odczytu lub zapisu na partycje danych](how-to-read-write-partitioned-data.md).

## <a name="json-schema"></a>Schematu JSON
Poniższe definicji JSON przedstawia sposób tworzenia harmonogramu wyzwalacza z planowania i cyklu:

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Month>>,
        "interval": <<int>>,             // Optional, specifies how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime - optional>>,
        "timeZone": "UTC"
        "schedule": {                    // Optional (advanced scheduling specifics)
          "hours": [<<0-23>>],
          "weekDays": : [<<Monday-Sunday>>],
          "minutes": [<<0-59>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurences": [
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
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

> [!IMPORTANT]
>  Właściwość **parameters** jest obowiązkową wartością elementu właściwości **pipelines**. Jeśli potok nie przyjmuje żadnych parametrów, należy dodać pustą definicję JSON dla właściwości **parameters**.


### <a name="schema-overview"></a>Przegląd schematu
Poniższa tabela zawiera ogólne omówienie głównych elementów schematu odnoszących się do powtarzania i planowania wyzwalacza:

| Właściwość JSON | Opis |
|:--- |:--- |
| **startTime** | Wartość daty i godziny. W przypadku prostych harmonogramów wartość właściwości **startTime** dotyczy pierwszego wystąpienia. W przypadku harmonogramów złożonych wyzwalacz nie jest uruchamiany wcześniej niż określona wartość właściwości **startTime**. |
| **endTime** | Data i godzina zakończenia wyzwalacza. Wyzwalacz nie jest wykonywany po określonej dacie i godzinie zakończenia. Wartość właściwości nie może odnosić się do przeszłości. Ta właściwość jest opcjonalna. |
| **timeZone** | Strefa czasowa. Obecnie jest obsługiwana tylko strefa czasowa UTC. |
| **recurrence** | Obiekt cyklu określający reguły powtarzania wyzwalacza. Obiekt cyklu obsługuje **częstotliwość**, **interva**l, **endTime**, **liczba**, i **harmonogram**elementów. Po zdefiniowaniu obiektu cyklu wymagany jest element właściwości **frequency**. Inne elementy obiektu cyklu są opcjonalne. |
| **frequency** | Jednostka częstotliwości powtarzania wyzwalacza. Obsługiwane wartości to „minute” („minuta”), „hour” („godzina”), „day” („dzień”), „week” („tydzień”) i „month” („miesiąc”). |
| **interval** | Dodatnia liczba całkowita oznaczająca interwał wartości właściwości **frequency**, która określa częstotliwość uruchamiania wyzwalacza. Jeśli na przykład właściwość **interval** ma wartość 3, a właściwość **frequency** ma wartość „week” („tydzień”), wyzwalacz jest powtarzany co 3 tygodnie. |
| **schedule** | Harmonogram cyklu wyzwalacza. Wyzwalacz z określoną wartością właściwości **frequency** zmienia swój cykl na podstawie harmonogramu cyklu. Właściwość **schedule** zawiera modyfikacje cyklu oparte na minutach, godzinach, dniach tygodnia, dniach miesiąca i numerze tygodnia.


### <a name="schema-defaults-limits-and-examples"></a>Wartości domyślne, limity i przykłady harmonogramów

| Właściwość JSON | Typ | Wymagane | Wartość domyślna | Prawidłowe wartości | Przykład |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** | Ciąg | Yes | Brak | Daty i godziny ISO-8601 | `"startTime" : "2013-01-09T09:30:00-08:00"` |
| **recurrence** | Obiekt | Yes | Brak | Obiekt cyklu | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **interval** | Liczba | Nie | 1 | Od 1 do 1000 | `"interval":10` |
| **endTime** | Ciąg | Yes | Brak | Wartość daty i godziny reprezentująca godzinę w przyszłości. | `"endTime" : "2013-02-09T09:30:00-08:00"` |
| **schedule** | Obiekt | Nie | Brak | Obiekt harmonogramu | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="starttime-property"></a>Właściwość startTime
W poniższej tabeli przedstawiono, w jaki sposób właściwość **startTime** kontroluje uruchamianie wyzwalacza:

| Wartość startTime | Cykl bez harmonogramu | Cykl z harmonogramem |
|:--- |:--- |:--- |
| Godzina rozpoczęcia w przeszłości | Oblicza pierwszy przyszły czas wykonania po czasie rozpoczęcia i uruchamia wyzwalacz w tym czasie.<br/><br/>Uruchamia kolejne wykonania w oparciu o obliczenia wykonane na podstawie ostatniego czasu wykonania.<br/><br/>Zobacz przykład znajdujący się pod tabelą. | Wyzwalacz jest uruchamiany _nie wcześniej niż_ w określonym czasie rozpoczęcia. Pierwsze wystąpienie opiera się na harmonogramie obliczonym na podstawie czasu rozpoczęcia.<br/><br/>Kolejne wykonania opierają na harmonogramie cyklu. |
| Godzina rozpoczęcia w przyszłości lub obecna | Uruchamiany raz o określonym czasie rozpoczęcia.<br/><br/>Uruchamia kolejne wykonania w oparciu o obliczenia wykonane na podstawie ostatniego czasu wykonania. | Wyzwalacz jest uruchamiany _nie wcześniej niż_ w określonym czasie rozpoczęcia. Pierwsze wystąpienie opiera się na harmonogramie obliczonym na podstawie czasu rozpoczęcia.<br/><br/>Kolejne wykonania opierają na harmonogramie cyklu. |

Zobaczmy przykład zdarzeń w przypadku, gdy czas rozpoczęcia przypada w przeszłości, z cyklem, ale bez harmonogramu. Załóżmy, że obecna data i godzina to `2017-04-08 13:00`, czas rozpoczęcia to `2017-04-07 14:00`, a cykl jest określony na 2 dni. (Wartość właściwości **recurrence** definiuje się, ustawiając właściwość **frequency** na wartość „day” („dzień”) i właściwość **interval** na wartość 2). Zwróć uwagę, że wartość właściwości **startTime** dotyczy przeszłej daty i następuje przed aktualną godziną.

W tych warunkach pierwsze wykonanie następuje o `2017-04-09 at 14:00`. Aparat harmonogramu oblicza wystąpienia wykonania od czasu rozpoczęcia. Wszystkie wystąpienia w przeszłości są odrzucane. Aparat wykorzystuje następne wystąpienie, które ma miejsce w przyszłości. W tym przypadku czas rozpoczęcia to `2017-04-07 at 2:00pm`, w związku z czym kolejne wystąpienie ma miejsce 2 dni po tym czasie, czyli `2017-04-09 at 2:00pm`.

Czas pierwszego wykonania jest identyczny, nawet jeśli właściwość **startTime** ma wartość `2017-04-05 14:00` lub `2017-04-01 14:00`. Po pierwszym wykonaniu kolejne wykonania są obliczane przy użyciu harmonogramu. W związku z tym kolejne wykonania zostaną uruchomione `2017-04-11 at 2:00pm`, `2017-04-13 at 2:00pm`, `2017-04-15 at 2:00pm` itd.

Gdy z kolei w harmonogramie wyzwalacza nie ustawiono godzin lub minut, wartością domyślną będą godziny lub minuty pierwszego wykonania.

### <a name="schedule-property"></a>Wartość schedule
Użycie harmonogramu może ograniczyć liczbę wykonań wyzwalacza. Jeśli na przykład uruchomienie wyzwalacza z częstotliwością miesięczną zaplanowano na 31. dzień, wyzwalacz będzie uruchamiany tylko w miesiącach, której mają 31 dni.

Harmonogram może także zwiększyć liczbę wykonań wyzwalacza. Na przykład wyzwalacz z częstotliwością miesięczną, którego uruchomienie zaplanowano na 1. i 2. dzień miesiąca, będzie uruchamiany 1. i 2. dnia miesiąca, a nie raz na miesiąc.

W przypadku określenia wielu elementów właściwości **schedule** ocena następuje od największego do najmniejszego elementu. Ocena jest wykonywana w następującej kolejności: numer tygodnia, dzień miesiąca, dzień tygodnia, godzina i minuta.

W poniższej tabeli opisano szczegółowo elementy właściwości **schedule**:


| Element JSON | Opis | Prawidłowe wartości |
|:--- |:--- |:--- |
| **minutes** | Minuty godziny, o których uruchamiany jest wyzwalacz. | <ul><li>Liczba całkowita</li><li>Tablica liczb całkowitych</li></ul>
| **hours** | Godziny dnia, o których uruchamiany jest wyzwalacz. | <ul><li>Liczba całkowita</li><li>Tablica liczb całkowitych</li></ul> |
| **weekDays** | Dni tygodnia, w których uruchamiany jest wyzwalacz. Wartość można określić tylko z częstotliwością tygodniową. | <ul><li>Monday, Tuesday, Wednesday, Thursday, Friday, Saturday lub Sunday</li><li>Tablica wartości dni (maksymalny rozmiar tablicy to 7)</li><li>W wartościach dni nie są uwzględniane wielkości liter</li></ul> |
| **monthlyOccurrences** | Dni miesiąca, w których uruchamiany jest wyzwalacz. Wartość można określić tylko z częstotliwością miesięczną. | <ul><li>Tablica obiektów **monthlyOccurence**: `{ "day": day,  "occurrence": occurence }`.</li><li>Atrybut **day** jest dniem tygodnia, w którym uruchamiany jest wyzwalacz. Na przykład właściwość **monthlyOccurrences** o wartości **day** wynoszącej `{Sunday}` oznacza każdą niedzielę miesiąca. Atrybut **day** jest wymagany.</li><li>Atrybut **occurence** jest wystąpieniem określonej wartości **day** w miesiącu. Na przykład właściwość **monthlyOccurrences** o wartościach **day** i **occurence** wynoszących `{Sunday, -1}` oznacza ostatnią niedzielę miesiąca. Atrybut **occurence** jest opcjonalny.</li></ul> |
| **monthDays** | Dzień miesiąca, w którym uruchamiany jest wyzwalacz. Wartość można określić tylko z częstotliwością miesięczną. | <ul><li>Dowolna wartość <= -1 i >= -31</li><li>Dowolna wartość >= 1 i <= 31</li><li>Tablica wartości</li></ul> |


## <a name="examples-of-trigger-recurrence-schedules"></a>Przykłady harmonogramów cyklu wyzwalaczy
Ta sekcja zawiera przykłady harmonogramów cyklu i koncentruje się na obiekcie właściwości **schedule** oraz jego elementach.

W przykładach założono, ze wartość właściwości **interval** wynosi 1 oraz że wartość właściwości **frequency** jest prawidłowa i zgodna z definicją harmonogramu. Na przykład nie można mieć właściwości **frequency** o wartości „day” i jednocześnie modyfikacji „monthDays” w obiekcie **schedule**. Tego typu ograniczenia są wymienione w tabeli w poprzedniej sekcji.

| Przykład | Opis |
|:--- |:--- |
| `{"hours":[5]}` | Uruchamiany o godz. 5:00 każdego dnia. |
| `{"minutes":[15], "hours":[5]}` | Uruchamiany o godz. 5:15 każdego dnia. |
| `{"minutes":[15], "hours":[5,17]}` | Uruchamiany o godz. 5:15 i 17:15 każdego dnia. |
| `{"minutes":[15,45], "hours":[5,17]}` | Uruchamiany o godz. 5:15, 5:45, 17:15 i 17:45 każdego dnia. |
| `{"minutes":[0,15,30,45]}` | Uruchamiany co 15 minut. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Uruchamiany co godzinę. Ten wyzwalacz jest uruchamiany co godzinę. Minuty są kontrolowane przez wartość właściwości **startTime**, gdy wartość jest określona. Jeśli wartość nie jest określona, minuty są kontrolowane przez czas utworzenia. Jeśli na przykład czas rozpoczęcia lub czas utworzenia (zależnie od sytuacji) to 12:25, wyzwalacz jest uruchamiany o godzinie 0:25, 1:25, 2:25, ..., 23:25.<br/><br/>Ten harmonogram jest odpowiednikiem wyzwalacza z właściwością **frequency** o wartości „hour”, właściwością **interval** o wartości 1 i bez właściwości **schedule**.  Tego harmonogramu można także używać z innymi wartościami właściwości **frequency** i **interval** do tworzenia innych wyzwalaczy. Gdy na przykład właściwość **frequency** ma wartość „month”, harmonogram jest uruchamiany tylko raz w miesiącu, a nie każdego dnia, gdy właściwość **frequency** ma wartość „day”. |
| `{"minutes":[0]}` | Uruchamiany co godzinę o pełnej godzinie. Ten wyzwalacz jest uruchamiany co godzinę o pełnej godzinie począwszy od godz. 0:00, a następnie o 1:00, 2:00 itd.<br/><br/>Ten harmonogram jest odpowiednikiem wyzwalacza z właściwością **frequency** o wartości „hour” i właściwością **startTime** o wartości 0 minut lub bez właściwości **schedule**, ale z właściwością **frequency** o wartości „day”. Jeśli wartość właściwości **frequency** to „week” lub „month”, harmonogram jest wykonywany odpowiednio tylko raz w tygodniu lub raz w miesiącu. |
| `{"minutes":[15]}` | Uruchamiany 15 minut po pełnej godzinie. Ten wyzwalacz jest uruchamiany 15 minut po pełnej godzinie począwszy od godz. 0:15, a następnie o 1:15, 2:15 itd. aż do 23.15. |
| `{"hours":[17], "weekDays":["saturday"]}` | Uruchamiany o godz. 17:00 w każdą sobotę. |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Uruchamiany o godz. 17:00 w każdy poniedziałek, środę i piątek. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Uruchamiany o godz. 17:15 i 17:45 w każdy poniedziałek, środę i piątek. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Uruchamiany co 15 minut w dni robocze. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Uruchamiany co 15 minut w dni robocze, między godz. 9:00 a 16:45. |
| `{"weekDays":["tuesday", "thursday"]}` | Uruchamiany we wtorki i czwartki o określonej godzinie. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | Uruchamiany o godz. 6:00 28. dnia każdego miesiąca (zakładając, że właściwość **frequency** ma wartość „month”). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Uruchamiany o godz. 6:00 ostatniego dnia miesiąca. Aby uruchomić wyzwalacz ostatniego dnia miesiąca, użyj wartości -1 zamiast 28, 29, 30 lub 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Uruchamiany o godz. 6:00 pierwszego i ostatniego dnia każdego miesiąca. |
| `{monthDays":[1,14]}` | Uruchamiany 1. i 14. dnia każdego miesiąca o określonej godzinie. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Uruchamiany w pierwszy piątek każdego miesiąca o godz. 5:00. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Uruchamiany w pierwszy piątek każdego miesiąca o określonej godzinie. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | Uruchamiany w trzeci piątek od końca każdego miesiąca o określonej godzinie. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Uruchamiany w pierwszy i ostatni piątek każdego miesiąca o godz. 5:00. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Uruchamiany w pierwszy i ostatni piątek każdego miesiąca o określonej godzinie. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Uruchamiany w piąty piątek każdego miesiąca o określonej godzinie. Jeśli miesiąc nie ma piątego piątku, potok nie jest uruchamiany, ponieważ jego harmonogram określa uruchamianie tylko w piąty piątek. Aby uruchomić wyzwalacz w ostatni piątek miesiąca, rozważ użycie wartości -1 zamiast 5 dla właściwości **occurrence**. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Uruchamiany co 15 minut w ostatni piątek miesiąca. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Uruchamiany o godz. 5:15, 5:45, 17:15 i 17:45 w trzecią środę każdego miesiąca. |


## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać szczegółowe informacje dotyczące wyzwalaczy, zobacz [potoku wykonywania i wyzwalaczy](concepts-pipeline-execution-triggers.md#triggers).
