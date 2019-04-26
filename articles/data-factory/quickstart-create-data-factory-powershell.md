---
title: Kopiowanie danych w usłudze Blob Storage przy użyciu usługi Azure Data Factory | Microsoft Docs
description: Tworzenie fabryki danych platformy Azure w celu skopiowania danych między lokalizacjami w usłudze Azure Blob Storage.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: quickstart
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: 07b5b039e0069702b613619c54eb7eda46bf3051
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60313207"
---
# <a name="quickstart-create-an-azure-data-factory-using-powershell"></a>Szybki start: Tworzenie fabryki danych platformy Azure przy użyciu programu PowerShell

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Bieżąca wersja](quickstart-create-data-factory-powershell.md)

Ten samouczek szybki start opisuje sposób używania programu PowerShell w celu utworzenia usługi Azure Data Factory. Potok tworzony w tej fabryce danych **kopiuje** dane z jednego folderu do innego folderu w usłudze Azure Blob Storage. Aby zapoznać się z samouczkiem dotyczącym **przekształcania** danych przy użyciu usługi Azure Data Factory, zobacz [Tutorial: Transform data using Spark](transform-data-using-spark.md) (Samouczek: przekształcanie danych przy użyciu platformy Spark).

> [!NOTE]
> Ten artykuł nie zawiera szczegółowego wprowadzenia do usługi Data Factory. Aby zapoznać się z wprowadzeniem do usługi Azure Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)]

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zainstaluj najnowsze moduły programu Azure PowerShell, wykonując instrukcje podane w temacie [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-Az-ps).

#### <a name="log-in-to-powershell"></a>Logowanie do programu PowerShell

1. Uruchom program **PowerShell** na maszynie. Nie zamykaj programu PowerShell aż do końca tego samouczka Szybki start. Jeśli go zamkniesz i otworzysz ponownie, musisz uruchomić te polecenia jeszcze raz.

2. Uruchom poniższe polecenie i wprowadź tę samą nazwę użytkownika platformy Azure oraz hasło, których używasz do logowania się w witrynie Azure Portal:

    ```powershell
    Connect-AzAccount
    ```

3. Uruchom poniższe polecenie, aby wyświetlić wszystkie subskrypcje dla tego konta:

    ```powershell
    Get-AzSubscription
    ```

4. Jeśli z kontem jest skojarzonych wiele subskrypcji, uruchom poniższe polecenie, aby wybrać subskrypcję, z którą chcesz pracować. Zastąp parametr **SubscriptionId** identyfikatorem Twojej subskrypcji platformy Azure:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"
    ```

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Zdefiniuj zmienną nazwy grupy zasobów, której użyjesz później w poleceniach programu PowerShell. Skopiuj poniższy tekst polecenia do programu PowerShell, podaj nazwę [grupy zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) w podwójnych cudzysłowach, a następnie uruchom polecenie. Na przykład: `"ADFQuickStartRG"`.

     ```powershell
    $resourceGroupName = "ADFQuickStartRG";
    ```

    Jeśli grupa zasobów już istnieje, możesz zrezygnować z jej zastąpienia. Przypisz inną wartość do zmiennej `$ResourceGroupName` i ponownie uruchom polecenie.

2. Aby utworzyć grupę zasobów platformy Azure, uruchom następujące polecenie:

    ```powershell
    $ResGrp = New-AzResourceGroup $resourceGroupName -location 'East US'
    ```

    Jeśli grupa zasobów już istnieje, możesz zrezygnować z jej zastąpienia. Przypisz inną wartość do zmiennej `$ResourceGroupName` i ponownie uruchom polecenie.

3. Zdefiniuj zmienną nazwy fabryki danych. 

    > [!IMPORTANT]
    >  Zaktualizuj nazwę fabryki danych, aby była unikatowa w skali globalnej, na przykład ADFTutorialFactorySP1127.

    ```powershell
    $dataFactoryName = "ADFQuickStartFactory";
    ```

4. Aby utworzyć fabrykę danych, uruchom następujące polecenie **AzDataFactoryV2 zestaw** polecenia cmdlet, używając właściwości Location i ResourceGroupName ze zmiennej $ResGrp:

    ```powershell
    $DataFactory = Set-AzDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName `
        -Location $ResGrp.Location -Name $dataFactoryName
    ```

Pamiętaj o następujących kwestiach:

* Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli zostanie wyświetlony następujący błąd, zmień nazwę i spróbuj ponownie.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Aby utworzyć wystąpienia usługi Data Factory, konto użytkownika używane do logowania się na platformie Azure musi być członkiem roli **współautora** lub **właściciela** albo **administratorem** subskrypcji platformy Azure.

* Aby uzyskać listę regionów platformy Azure, w których obecnie jest dostępna usługa Data Factory, wybierz dane regiony na poniższej stronie, a następnie rozwiń węzeł **Analiza**, aby zlokalizować pozycję **Data Factory**: [Dostępność produktów według regionów](https://azure.microsoft.com/global-infrastructure/services/). Magazyny danych (Azure Storage, Azure SQL Database itp.) i jednostki obliczeniowe (HDInsight itp.) używane przez fabrykę danych mogą mieścić się w innych regionach.

## <a name="create-a-linked-service"></a>Tworzenie usługi połączonej

Utwórz połączone usługi w fabryce danych w celu połączenia swoich magazynów danych i usług obliczeniowych z fabryką danych. W tym samouczku Szybki start utworzysz połączoną usługę Azure Storage, która będzie używana zarówno jako źródło, jak i ujście magazynu. Połączona usługa ma informacje o połączeniu, których usługa Data Factory używa w środowisku uruchomieniowym do nawiązywania z nią połączenia.

1. W folderze **C:\ADFv2QuickStartPSH** utwórz plik JSON o nazwie **AzureStorageLinkedService.json** z następującą zawartością: (Utwórz folder ADFv2QuickStartPSH, jeśli jeszcze nie istnieje).

    > [!IMPORTANT]
    > Przed zapisaniem pliku zastąp wartości &lt;accountName&gt; i &lt;accountKey&gt; nazwą i kluczem konta usługi Azure Storage.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net",
                    "type": "SecureString"
                }
            }
        }
    }
    ```

    Jeśli używasz programu Notatnik, wybierz pozycję **Wszystkie pliki** na liście **Zapisz jako typ** w oknie dialogowym **Zapisywanie jako**. W przeciwnym razie do pliku może zostać dodane rozszerzenie `.txt`. Na przykład `AzureStorageLinkedService.json.txt`. W przypadku utworzenia pliku w Eksploratorze plików przed jego otwarciem w programie Notatnik rozszerzenie `.txt` może nie być widoczne, ponieważ opcja **Ukryj rozszerzenia znanych typów plików** jest domyślnie ustawiona. Przed przejściem do następnego kroku usuń rozszerzenie `.txt`.

2. W programie **PowerShell** przejdź do folderu **ADFv2QuickStartPSH**.

    ```powershell
    Set-Location 'C:\ADFv2QuickStartPSH'
    ```

3. Uruchom **AzDataFactoryV2LinkedService zestaw** polecenia cmdlet, aby utworzyć połączoną usługę: **AzureStorageLinkedService**.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureStorageLinkedService" `
        -DefinitionFile ".\AzureStorageLinkedService.json"
    ```

    Oto przykładowe dane wyjściowe:

    ```console
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

## <a name="create-a-dataset"></a>Tworzenie zestawu danych

W tym kroku zdefiniujesz zestaw danych, który reprezentuje dane do skopiowania ze źródła do ujścia. Zestaw danych jest typu **AzureBlob**. Odwołuje się on do **połączonej usługi Azure Storage** utworzonej w poprzednim kroku. Pobiera on parametr w celu skonstruowania właściwości **folderPath**. Dla wejściowego zestawu danych działanie kopiowania w potoku przekazuje ścieżkę wejściową jako wartość tego parametru. Analogicznie dla wyjściowego zestawu danych działanie kopiowania przekazuje ścieżkę wyjściową jako wartość tego parametru. 

1. Utwórz plik JSON o nazwie **BlobDataset.json** w folderze **C:\ADFv2QuickStartPSH** o następującej zawartości:

    ```json
    {
        "name": "BlobDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "@{dataset().path}"
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            },
            "parameters": {
                "path": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. Aby utworzyć zestaw danych: **BlobDataset**Uruchom **AzDataFactoryV2Dataset zestaw** polecenia cmdlet.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName -Name "BlobDataset" `
        -DefinitionFile ".\BlobDataset.json"
    ```

    Oto przykładowe dane wyjściowe:

    ```console
    DatasetName       : BlobDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-a-pipeline"></a>Tworzenie potoku

W niniejszym przewodniku Szybki start tworzysz potok, który zawiera jedno działanie i pobiera dwa parametry — ścieżkę wejściowego obiektu blob i ścieżkę wyjściowego obiektu blob. Wartości tych parametrów są ustawiane w chwili wyzwolenia/uruchomienia potoku. Działanie kopiowania używa tego samego zestawu danych obiektu blob, który został utworzony w poprzednim kroku jako wejście i wyjście. W przypadku użycia zestawu danych jako zestawu danych wejściowych określana jest ścieżka wejściowa. Natomiast w przypadku użycia zestawu danych jako zestawu danych wyjściowych określana jest ścieżka wyjściowa.

1. Utwórz plik JSON o nazwie **Adfv2QuickStartPipeline.json** w folderze **C:\Adfv2QuickStartPSH** o następującej zawartości:

    ```json
    {
        "name": "Adfv2QuickStartPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToBlob",
                    "type": "Copy",
                    "inputs": [
                        {
                            "referenceName": "BlobDataset",
                            "parameters": {
                                "path": "@pipeline().parameters.inputPath"
                            },
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "BlobDataset",
                            "parameters": {
                                "path": "@pipeline().parameters.outputPath"
                            },
                            "type": "DatasetReference"
                        }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    }
                }
            ],
            "parameters": {
                "inputPath": {
                    "type": "String"
                },
                "outputPath": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. Aby utworzyć potok: **Adfv2QuickStartPipeline**Uruchom **AzDataFactoryV2Pipeline zestaw** polecenia cmdlet.

    ```powershell
    $DFPipeLine = Set-AzDataFactoryV2Pipeline `
        -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName `
        -Name "Adfv2QuickStartPipeline" `
        -DefinitionFile ".\Adfv2QuickStartPipeline.json"
    ```

## <a name="create-a-pipeline-run"></a>Tworzenie uruchomienia potoku

W tym kroku ustawisz wartości parametrów potoku: **inputPath** i **outputPath** przy użyciu rzeczywistych wartości ścieżki źródła i ujścia obiektu blob. Następnie utworzysz uruchomienie potoku za pomocą tych argumentów.

1. Utwórz plik JSON o nazwie **PipelineParameters.json** w folderze **C:\ADFv2QuickStartPSH** o następującej zawartości:

    ```json
    {
        "inputPath": "adftutorial/input",
        "outputPath": "adftutorial/output"
    }
    ```
2. Uruchom **Invoke AzDataFactoryV2Pipeline** polecenia cmdlet, aby utworzyć potok Uruchom i przekaż w wartości parametrów. Polecenie cmdlet zwraca identyfikator uruchomienia potoku w celu monitorowania w przyszłości.

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline `
        -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName `
        -PipelineName $DFPipeLine.Name `
        -ParameterFile .\PipelineParameters.json
    ```

## <a name="monitor-the-pipeline-run"></a>Monitorowanie działania potoku

1. Uruchom następujący skrypt programu PowerShell, aby stale sprawdzać stan uruchomienia potoku do momentu zakończenia kopiowania danych. Skopiuj/wklej poniższy skrypt w oknie programu PowerShell i naciśnij klawisz ENTER.

    ```powershell
    while ($True) {
        $Run = Get-AzDataFactoryV2PipelineRun `
            -ResourceGroupName $ResGrp.ResourceGroupName `
            -DataFactoryName $DataFactory.DataFactoryName `
            -PipelineRunId $RunId

        if ($Run) {
            if ($run.Status -ne 'InProgress') {
                Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
                $Run
                break
            }
            Write-Output "Pipeline is running...status: InProgress"
        }

        Start-Sleep -Seconds 10
    }
    ```

    Oto przykładowe dane wyjściowe uruchomienia potoku:

    ```console
    Pipeline is running...status: InProgress
    Pipeline run finished. The status is:  Succeeded
    
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    RunId             : 0000000000-0000-0000-0000-0000000000000
    PipelineName      : Adfv2QuickStartPipeline
    LastUpdated       : 9/28/2017 8:28:38 PM
    Parameters        : {[inputPath, adftutorial/input], [outputPath, adftutorial/output]}
    RunStart          : 9/28/2017 8:28:14 PM
    RunEnd            : 9/28/2017 8:28:38 PM
    DurationInMs      : 24151
    Status            : Succeeded
    Message           :
    ```

    Może zostać wyświetlony następujący błąd:

    ```console
    Activity CopyFromBlobToBlob failed: Failed to detect region of linked service 'AzureStorage' : 'AzureStorageLinkedService' with error '[Region Resolver] Azure Storage failed to get address for DNS. Warning: System.Net.Sockets.SocketException (0x80004005): No such host is known
    ```

    Jeśli zostanie wyświetlony błąd, wykonaj następujące kroki:

    1. W pliku AzureStorageLinkedService.json upewnij się, że nazwa i klucz konta usługi Azure Storage są poprawne.
    2. Sprawdź, czy format parametrów połączenia jest poprawny. Właściwości, na przykład AccountName i AccountKey, są oddzielone średnikami (`;`).
    3. Jeśli nazwę konta i klucz konta otaczają nawiasy ostrokątne, usuń te nawiasy.
    4. Oto przykładowe parametry połączenia:

        ```json
        "connectionString": {
            "value": "DefaultEndpointsProtocol=https;AccountName=mystorageaccountname;AccountKey=mystorageaccountkey;EndpointSuffix=core.windows.net",
            "type": "SecureString"
        }
        ```

    5. Ponownie utwórz połączoną usługę, wykonując kroki opisane w sekcji [Tworzenie usługi połączonej](#create-a-linked-service).
    6. Następnie ponownie uruchom potok, wykonując kroki opisane w sekcji [Tworzenie uruchomienia potoku](#create-a-pipeline-run).
    7. Uruchom ponownie polecenie bieżącego monitorowania, aby monitorować nowe uruchomienie potoku.

2. Uruchom następujący skrypt, aby pobrać szczegóły uruchomienia działania kopiowania, na przykład rozmiar odczytanych/zapisanych danych.

    ```powershell
    Write-Output "Activity run details:"
    $Result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineRunId $RunId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $Result

    Write-Output "Activity 'Output' section:"
    $Result.Output -join "`r`n"

    Write-Output "Activity 'Error' section:"
    $Result.Error -join "`r`n"
    ```
3. Upewnij się, że wyświetlone dane wyjściowe są podobne do następujących przykładowych danych wyjściowych uruchomienia działania:

    ```console
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    ActivityName      : CopyFromBlobToBlob
    PipelineRunId     : 00000000000-0000-0000-0000-000000000000
    PipelineName      : Adfv2QuickStartPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, copyDuration, throughput...}
    LinkedServiceName :
    ActivityRunStart  : 9/28/2017 8:28:18 PM
    ActivityRunEnd    : 9/28/2017 8:28:36 PM
    DurationInMs      : 18095
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity 'Output' section:
    "dataRead": 38
    "dataWritten": 38
    "copyDuration": 7
    "throughput": 0.01
    "errors": []
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)"
    "usedDataIntegrationUnits": 2
    "billedDuration": 14
    ```

[!INCLUDE [data-factory-quickstart-verify-output-cleanup.md](../../includes/data-factory-quickstart-verify-output-cleanup.md)]

## <a name="next-steps"></a>Kolejne kroki

Potok w tym przykładzie kopiuje dane z jednej lokalizacji do innej lokalizacji w usłudze Azure Blob Storage. Zapoznaj się z [samouczkami](tutorial-copy-data-dot-net.md), aby dowiedzieć się więcej o korzystaniu z usługi Data Factory w dalszych scenariuszach.
