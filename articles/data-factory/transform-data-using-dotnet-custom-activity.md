---
title: Korzystanie z działań niestandardowych w potoku usługi Azure Data Factory
description: Dowiedz się, jak tworzyć niestandardowe działania i używać ich w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/26/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 8076ae81b111aa6b524b7e286ed15ca0661d748b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67062114"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Korzystanie z działań niestandardowych w potoku usługi Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz wersję usługi Data Factory, którego używasz:"]
> * [Wersja 1](v1/data-factory-use-custom-activities.md)
> * [Bieżąca wersja](transform-data-using-dotnet-custom-activity.md)

Istnieją dwa typy działań, które można używać w potoku usługi Azure Data Factory.

- [Działania przenoszenia danych](copy-activity-overview.md) do przenoszenia danych między [obsługiwane magazyny danych źródła i ujścia](copy-activity-overview.md#supported-data-stores-and-formats).
- [Działania przekształcania danych](transform-data.md) do przekształcania danych za pomocą usług obliczeniowych, takich jak Azure HDInsight, Azure Batch i Azure Machine Learning.

Aby przenieść dane do/z danych przechowywania, nie obsługuje usługi Data Factory lub celu przekształcenie/przetworzenie danych w sposób, który nie jest obsługiwany przez usługę Data Factory, można utworzyć **niestandardowe działanie** przy użyciu własnych przenoszenia danych lub logiki przekształcania i użycia działanie w potoku. Niestandardowe działanie uruchamia logikę dostosowany kod w **usługi Azure Batch** puli maszyn wirtualnych.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zobacz następujące artykuły, jeśli jesteś nowym użytkownikiem usługi Azure Batch:

* [Podstawy usługi Azure Batch](../batch/batch-technical-overview.md) z omówieniem usługi Azure Batch.
* [Nowe AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) polecenia cmdlet, aby utworzyć konto usługi Azure Batch (lub) [witryny Azure portal](../batch/batch-account-create-portal.md) do utworzenia konta usługi Azure Batch przy użyciu witryny Azure portal. Zobacz [przy użyciu programu PowerShell do zarządzania kontem usługi Batch Azure](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) artykuł, aby uzyskać szczegółowe instrukcje na temat korzystania z polecenia cmdlet.
* [Nowe AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) polecenie cmdlet do tworzenia puli usługi Azure Batch.

## <a name="azure-batch-linked-service"></a>Usługa Azure Batch połączone

Następujący kod JSON definiuje przykładowej usługi Azure Batch połączone. Aby uzyskać więcej informacji, zobacz [obliczenia środowisk obsługiwanych przez usługę Azure Data Factory](compute-linked-services.md)

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "batchaccount",
            "accessKey": {
                "type": "SecureString",
                "value": "access key"
            },
            "batchUri": "https://batchaccount.region.batch.azure.com",
            "poolName": "poolname",
            "linkedServiceName": {
                "referenceName": "StorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
}
```

 Aby dowiedzieć się więcej na temat usługi Azure Batch połączone, zobacz [usługi połączone usługi Compute](compute-linked-services.md) artykułu.

## <a name="custom-activity"></a>Działanie niestandardowe

Poniższy fragment kodu JSON definiuje potok za pomocą prostego działania niestandardowe. Definicja aktywności zawiera odwołanie do usługi Azure Batch połączone.

```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "helloworld.exe",
        "folderPath": "customactv2/helloworld",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        }
      }
    }]
  }
}
```

W tym przykładzie helloworld.exe jest przechowywany w folderze customactv2/helloworld konta usługi Azure Storage używanego w resourceLinkedService aplikacji niestandardowej. Niestandardowe działanie przesyła ten niestandardowych aplikacji do wykonania w usłudze Azure Batch. Możesz zastąpić polecenie, aby dowolnej preferowanych aplikacji, która może być wykonywane w element docelowy System operacyjny węzłów w puli usługi Azure Batch.

W poniższej tabeli opisano nazwy i opisy właściwości, które są specyficzne dla tego działania.

| Właściwość              | Opis                              | Wymagane |
| :-------------------- | :--------------------------------------- | :------- |
| name                  | Nazwa działania w potoku     | Tak      |
| description           | Tekst opisujący, co działanie robi.  | Nie       |
| type                  | Działania niestandardowe, typ działania jest **niestandardowe**. | Yes      |
| linkedServiceName     | Połączoną usługę służącą do usługi Azure Batch. Aby dowiedzieć się więcej na temat tej połączonej usługi, zobacz [usługi połączone usługi Compute](compute-linked-services.md) artykułu.  | Yes      |
| Polecenie               | Polecenie niestandardowych aplikacji do wykonania. Jeśli aplikacja jest już dostępne w węźle puli Azure Batch, można pominąć resourceLinkedService i folderPath. Na przykład można określić polecenie, aby być `cmd /c dir`, które są natywnie obsługiwane przez węzeł Windows puli usługi Batch. | Tak      |
| resourceLinkedService | Usługa Azure Storage połączoną usługę służącą do konta magazynu, w którym przechowywany jest niestandardowy aplikacji | Brak&#42;       |
| folderPath            | Ścieżka do folderu niestandardowych aplikacji i wszystkich jego zależności<br/><br/>Jeśli występują zależności przechowywane w podfolderach — czyli w hierarchicznej struktury folderów w obszarze *folderPath* — struktura folderów jest spłaszczany obecnie, gdy pliki są kopiowane do usługi Azure Batch. Oznacza to, że wszystkie pliki są kopiowane do pojedynczego folderu z bez podfolderów. Aby obejść ten problem, należy wziąć pod uwagę kompresowanie plików, Kopiowanie skompresowanego pliku i rozpakowywania go przy użyciu niestandardowego kodu w dowolnym miejscu. | Brak&#42;       |
| referenceObjects      | Tablica istniejących połączonych usług i zestawów danych. Odwołania usługi połączone i zestawy danych są przekazywane do aplikacji niestandardowej w formacie JSON, dzięki czemu niestandardowy kod może odwoływać się do zasobów usługi Data Factory | Nie       |
| extendedProperties    | Właściwości zdefiniowane przez użytkownika, które mogą być przekazywane do aplikacji niestandardowej w formacie JSON, dzięki czemu niestandardowy kod może odwoływać się do dodatkowych właściwości | Nie       |
| retentionTimeInDays | Czas przechowywania plików przesłanych dla działań niestandardowych. Wartość domyślna to 30 dni. | Nie |

&#42;Właściwości `resourceLinkedService` i `folderPath` muszą być jednocześnie określone lub oba, można pominąć.

> [!NOTE]
> Jeśli przekazujesz połączone usługi jako referenceObjects w działaniu niestandardowym, jest dobrym sposobem do przekazania do usługi Azure Key Vault włączony połączonej usługi (ponieważ nie zawiera żadnych bezpiecznego ciągów) i pobierania poświadczeń przy użyciu nazwy klucza tajnego bezpośrednio z klucza Magazyn z kodu. Przykład można znaleźć [tutaj](https://github.com/nabhishek/customactivity_sample/tree/linkedservice) odwołania AKV włączenia połączona usługa pobiera poświadczenia z usługi Key Vault, a następnie uzyskuje dostęp do magazynu w kodzie.

## <a name="custom-activity-permissions"></a>Działanie niestandardowe uprawnienia

Niestandardowe działanie Ustawia konto usługi Azure Batch automatycznie użytkownika *dostępu inni niż administratorzy z zakresem zadań* (domyślna Specyfikacja użytkownika automatycznie). Nie można zmienić poziom uprawnień konta użytkownika automatycznie. Aby uzyskać więcej informacji, zobacz [uruchamianie zadań w ramach kont użytkowników w usłudze Batch | Konta użytkowników automatycznie](../batch/batch-user-accounts.md#auto-user-accounts).

## <a name="executing-commands"></a>Wykonywanie poleceń

Może bezpośrednio wykonywać polecenia przy użyciu działania niestandardowe. Poniższy przykład wykonuje polecenie "echo hello world" w docelowych węzłach puli usługi Azure Batch i wyświetla dane wyjściowe do strumienia wyjściowego stdout.

```json
{
  "name": "MyCustomActivity",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "cmd /c echo hello world"
      }
    }]
  }
}
```

## <a name="passing-objects-and-properties"></a>Przekazywanie obiektów i właściwości

Niniejszy przykład pokazuje, jak można użyć referenceObjects i extendedProperties do przekazania obiektów fabryki danych i właściwości zdefiniowane przez użytkownika do aplikacji niestandardowej.

```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "SampleApp.exe",
        "folderPath": "customactv2/SampleApp",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        },
        "referenceObjects": {
          "linkedServices": [{
            "referenceName": "AzureBatchLinkedService",
            "type": "LinkedServiceReference"
          }]
        },
        "extendedProperties": {
          "connectionString": {
            "type": "SecureString",
            "value": "aSampleSecureString"
          },
          "PropertyBagPropertyName1": "PropertyBagValue1",
          "propertyBagPropertyName2": "PropertyBagValue2",
          "dateTime1": "2015-04-12T12:13:14Z"
        }
      }
    }]
  }
}
```

Po wykonaniu działania referenceObjects i właściwości rozszerzone są przechowywane w następujących plików, które są wdrażane na tym samym folderze wykonywania SampleApp.exe:

- `activity.json`

  Przechowuje extendedProperties i właściwości działania niestandardowego.

- `linkedServices.json`

  Magazyny tablicy połączonych usług zdefiniowane we właściwości referenceObjects.

- `datasets.json`

  Magazyny tablicę zestawów danych zdefiniowana we właściwości referenceObjects.

Następujący przykładowy kod pokazują, jak SampleApp.exe dostęp do wymaganych informacji z plików JSON:

```csharp
using Newtonsoft.Json;
using System;
using System.IO;

namespace SampleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            //From Extend Properties
            dynamic activity = JsonConvert.DeserializeObject(File.ReadAllText("activity.json"));
            Console.WriteLine(activity.typeProperties.extendedProperties.connectionString.value);

            // From LinkedServices
            dynamic linkedServices = JsonConvert.DeserializeObject(File.ReadAllText("linkedServices.json"));
            Console.WriteLine(linkedServices[0].properties.typeProperties.accountName);
        }
    }
}
```

## <a name="retrieve-execution-outputs"></a>Pobieranie wyników wykonywania

Można zacząć przebiegu potoku przy użyciu następującego polecenia programu PowerShell:

```powershell
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
```

Potok jest uruchomiona, można sprawdzić dane wyjściowe wykonania za pomocą następujących poleceń:

```powershell
while ($True) {
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

    if(!$result) {
        Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
    }
    elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
        Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
    }
    else {
        Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
        $result
        break
    }
    ($result | Format-List | Out-String)
    Start-Sleep -Seconds 15
}

Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
$result.Output -join "`r`n"

Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
$result.Error -join "`r`n"
```

**Stdout** i **stderr** z Twoją niestandardową aplikacją są zapisywane w **adfjobs** kontenera w połączoną usługę Azure Storage określone podczas tworzenia usługi Azure Batch połączone Usługa z identyfikatorem GUID zadania. Szczegółowa ścieżka można uzyskać z uruchamiania działania danych wyjściowych, jak pokazano w poniższym fragmencie kodu:

```
Pipeline ' MyCustomActivity' run finished. Result:

ResourceGroupName : resourcegroupname
DataFactoryName   : datafactoryname
ActivityName      : MyCustomActivity
PipelineRunId     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
PipelineName      : MyCustomActivity
Input             : {command}
Output            : {exitcode, outputs, effectiveIntegrationRuntime}
LinkedServiceName :
ActivityRunStart  : 10/5/2017 3:33:06 PM
ActivityRunEnd    : 10/5/2017 3:33:28 PM
DurationInMs      : 21203
Status            : Succeeded
Error             : {errorCode, message, failureType, target}

Activity Output section:
"exitcode": 0
"outputs": [
  "https://<container>.blob.core.windows.net/adfjobs/<GUID>/output/stdout.txt",
  "https://<container>.blob.core.windows.net/adfjobs/<GUID>/output/stderr.txt"
]
"effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
Activity Error section:
"errorCode": ""
"message": ""
"failureType": ""
"target": "MyCustomActivity"
```

Jeśli chcesz korzystać z zawartości stdout.txt działania podrzędnego, możesz ją uzyskać ścieżkę do pliku stdout.txt w wyrażeniu "\@activity('MyCustomActivity').output.outputs [0]".

> [!IMPORTANT]
> - Activity.json linkedServices.json i datasets.json są przechowywane w folderze czasu wykonywania zadania wsadowego. W tym przykładzie activity.json linkedServices.json i datasets.json są przechowywane w `"https://adfv2storage.blob.core.windows.net/adfjobs/\<GUID>/runtime/"` ścieżki. Jeśli to konieczne, należy wyczyścić oddzielnie.
> - W przypadku połączonych usług, które korzystają z produktem Integration Runtime poufne informacje, takie jak klucze lub hasła są zaszyfrowane za środowiskiem Integration Runtime, aby upewnić się, pozostaje poświadczeń klientów definiowane prywatnym środowisku sieciowym. Niektóre pola poufnych może być brak w odwołuje się kod aplikacji niestandardowej w ten sposób. W extendedProperties zamiast odwołanie do połączonej usługi, jeśli to konieczne, należy użyć ciągu SecureString.

## <a name="pass-outputs-to-another-activity"></a>Przekaż dane wyjściowe do kolejnego działania

Możesz wysłać wartości niestandardowe w kodzie w działań niestandardowych z powrotem do usługi Azure Data Factory. Możesz to zrobić przez napisanie je do `outputs.json` z aplikacji. Data Factory kopiuje zawartość `outputs.json` i dołącza je do dane wyjściowe działania jako wartość `customOutput` właściwości. (Limit rozmiaru wynosi 2MB). Jeśli chcesz korzystać z zawartości z `outputs.json` działania podrzędne mogą uzyskać wartość przy użyciu wyrażenia `@activity('<MyCustomActivity>').output.customOutput`.

## <a name="retrieve-securestring-outputs"></a>Pobieranie danych wyjściowych SecureString

Wartości właściwości poufnych, wyznaczony jako typ *SecureString*, jak pokazano na niektóre przykłady w niniejszym artykule są maskowane się na karcie monitorowanie w interfejsie użytkownika usługi Data Factory.  Podczas wykonywania potoku rzeczywiste, jednak *SecureString* właściwość jest serializowana jako plik JSON w ramach `activity.json` pliku jako zwykły tekst. Na przykład:

```json
"extendedProperties": {
  "connectionString": {
    "type": "SecureString",
    "value": "aSampleSecureString"
  }
}
```

Tej serializacji nie jest naprawdę bezpieczny i nie jest przeznaczony do zabezpieczenia. Celem jest wskazówkę Data Factory, aby wartość na karcie monitorowanie maski.

Dostęp do właściwości typu *SecureString* z niestandardowych działań odczytywać `activity.json` pliku, który jest umieszczony w tym samym folderze co Twoje. Plik EXE, deserializacji za pomocą pliku JSON, a następnie uzyskać dostęp do właściwości JSON (extendedProperties = > [propertyName] = > wartość).

## <a name="compare-v2-v1"></a> Porównaj działaniu niestandardowym w wersji 2 i w wersji 1 (niestandardowy) działania DotNet

W usłudze Azure Data Factory w wersji 1 wykonania działania DotNet (niestandardowy), tworząc projekt biblioteki klas platformy .NET przy użyciu klasy, która implementuje `Execute` metody `IDotNetActivity` interfejsu. Usługi połączone, zestawy danych i właściwości rozszerzone w ładunku JSON działania DotNet (niestandardowy) są przekazywane do metody wykonywania jako silnie typizowanych obiektów. Aby uzyskać szczegółowe informacje o zachowaniu w wersji 1, zobacz [DotNet (niestandardowy) w wersji 1](v1/data-factory-use-custom-activities.md). Z powodu tej implementacji w wersji 1 Kod działania DotNet ma pod kątem platformy .NET Framework 4.5.2. Wersja 1 działania DotNet również musi być wykonywane w węzłach puli usługi Batch opartych na Windows Azure.

W przypadku działania niestandardowego Azure danych fabryki V2 nie należy implementować interfejsu .NET. Można teraz bezpośrednio uruchomić polecenia, skrypty i własny kod niestandardowy skompilowany jako wykonywalny. Aby skonfigurować tę implementację, należy określić `Command` właściwości wraz z `folderPath` właściwości. Niestandardowe działanie przekazuje plik wykonywalny i jego zależności, aby `folderpath` i wykonuje polecenie dla Ciebie.

Połączone usługi, zestawów danych (zdefiniowanymi w referenceObjects) i rozszerzone właściwości zdefiniowane w ładunku JSON usługi Data Factory w wersji 2 niestandardowe działanie może być dostępna przez plik wykonywalny jako pliki w formacie JSON. Wymagane właściwości, za pomocą serializator JSON, jak pokazano w poprzednim przykładzie kodu SampleApp.exe możesz uzyskać dostęp.

Zmiany wprowadzone w Data Factory V2 niestandardowe działanie możesz napisać logikę niestandardowego kodu w Twoim preferowanym języku i uruchomić go na Windows i systemy operacyjne Linux obsługiwane przez usługę Azure Batch.

W poniższej tabeli opisano różnice między działań niestandardowych w wersji 2 fabryki danych i Data Factory w wersji 1 (niestandardowy) działania DotNet:

|Różnice      | Działanie niestandardowe      | Wersja 1 (niestandardowy) działania DotNet      |
| ---- | ---- | ---- |
|Jak jest zdefiniowany w niestandardowej logiki      |Dostarczając plik wykonywalny      |Wdrażając biblioteki DLL platformy .NET      |
|Środowisko wykonywania logiki niestandardowej      |Windows lub Linux      |Windows (.NET Framework 4.5.2)      |
|Wykonywanie skryptów      |Obsługuje wykonywanie skryptów bezpośrednio (na przykład "cmd /c echo hello world" na maszynie Wirtualnej Windows)      |Wymaga wdrożenia w bibliotece DLL platformy .NET      |
|Zestaw danych jest wymagana      |Optional (Opcjonalność)      |Wymagane do działania połączyć w łańcuch informacjami i przekazują      |
|Przekazywanie informacji z działania do logiki niestandardowej      |Za pomocą ReferenceObjects (LinkedServices i zestawów danych) i ExtendedProperties (właściwości niestandardowych)      |Przy użyciu właściwości rozszerzone (właściwości niestandardowych), dane wejściowe i wyjściowe zestawy danych      |
|Pobieranie informacji w niestandardowej logiki      |Analizuje activity.json linkedServices.json i datasets.json przechowywane w tym samym folderze plik wykonywalny      |Za pomocą zestawu .NET SDK (ramki .NET 4.5.2)      |
|Rejestrowanie      |Zapisuje dane bezpośrednio na STDOUT      |Implementowanie rejestrowania biblioteki dll platformy .NET      |

Jeśli masz istniejący kod .NET napisany dla wersji 1 działania DotNet (niestandardowy), należy zmodyfikować swój kod pod kątem go do pracy z bieżącą wersją działania niestandardowe. Wykonując te wytyczne wysokiego poziomu, należy zaktualizować kod:

  - Zmień projekt z biblioteki klas platformy .NET do aplikacji konsoli.
  - Uruchom aplikację za pomocą `Main` metody. `Execute` Metody `IDotNetActivity` interfejsu nie jest już wymagane.
  - Przeczytaj i analizowanie usługi połączone, zestawy danych i działania, za pomocą serializator JSON, a nie jako silnie typizowanych obiektów. Przekaż wartości właściwości wymaganych do logiki głównego kodu niestandardowego. Skorzystaj z poprzedniego kodu SampleApp.exe jako przykład.
  - Obiekt rejestratora nie jest już obsługiwana. Dane wyjściowe z plik wykonywalny może zostać zrealizowane w konsoli i jest zapisywany na stdout.txt.
  - Pakiet Microsoft.Azure.Management.DataFactories NuGet nie jest już wymagane.
  - Kompiluj swój kod, przekazywanie pliku wykonywalnego oraz jego zależności do usługi Azure Storage i zdefiniować ścieżkę w `folderPath` właściwości.

Pełny przykład opisu przykładowy potok i biblioteki DLL end-to-end w usługi Data Factory w wersji 1 artykułu [używanie niestandardowych działań w potoku usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities) można inaczej jako działania niestandardowego fabryki danych, zobacz [ Przykładowe działanie niestandardowe fabryki danych](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFv2CustomActivitySample).

## <a name="auto-scaling-of-azure-batch"></a>Automatyczne skalowanie usługi Azure Batch

Możesz również utworzyć puli usługi Azure Batch przy użyciu **skalowania automatycznego** funkcji. Na przykład można utworzyć puli usługi azure batch przy użyciu 0 dedykowanych maszyn wirtualnych i formułę skalowania automatycznego na podstawie liczby oczekujących zadań.

W tym miejscu wartość przykładowa formuła realizuje następujące zachowanie: Podczas tworzenia puli, rozpoczynają się one od 1 maszyna wirtualna. Metryki $PendingTasks definiuje liczbę zadań uruchamiania + aktywny (kolejki) stanu. Formuła wyszukuje średnią liczbę zadań oczekujących w ostatnich 180 sekund i ustawia odpowiednio TargetDedicated. Zapewnia, że TargetDedicated nigdy nie trafiają ponad 25 maszyn wirtualnych. Tak, ponieważ nowe zadania są przesyłane, puli automatycznie rozszerza się w i jako zadania, maszyn wirtualnych stają się bezpłatne pojedynczo, i automatyczne skalowanie zmniejsza tych maszyn wirtualnych. startingNumberOfVMs i maxNumberofVMs mogą być dostosowane do potrzeb.

Formułę skalowania automatycznego:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Zobacz [automatyczne skalowanie węzłów obliczeniowych w puli usługi Azure Batch](../batch/batch-automatic-scaling.md) Aby uzyskać szczegółowe informacje.

Jeśli pula używa domyślnie [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), usługa Batch może potrwać 15 – 30 minut, aby przygotować maszynę Wirtualną przed uruchomieniem działania niestandardowego. Jeśli pula używa różnych autoScaleEvaluationInterval, usługa Batch może potrwać autoScaleEvaluationInterval + 10 minut.

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły, które wyjaśniają, jak przekształcać dane w inny sposób:

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Działanie technologii hive](transform-data-using-hadoop-hive.md)
* [Działania technologii pig](transform-data-using-hadoop-pig.md)
* [Działania technologii MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Działania przesyłania strumieniowego usługi Hadoop](transform-data-using-hadoop-streaming.md)
* [Działania platformy Spark](transform-data-using-spark.md)
* [Machine Learning Batch Execution działania](transform-data-using-machine-learning.md)
* [Działania procedur składowanych](transform-data-using-stored-procedure.md)
