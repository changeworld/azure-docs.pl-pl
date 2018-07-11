---
title: Uruchamianie pakietów SSIS za pomocą wykonania SSIS pakietu działania — Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób uruchamiania pakietu usług SQL Server Integration Services (SSIS) w potoku usługi Azure Data Factory za pomocą działania wykonywanie pakietów usług SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/09/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: db5941528eedd10cf252607dbe2160bd498a70de
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37951971"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Uruchamianie pakietów SSIS za pomocą działania pakietu SSIS wykonania w usłudze Azure Data Factory
W tym artykule opisano sposób uruchamiania pakietu SSIS w potoku usługi Azure Data Factory za pomocą działania wykonywanie pakietu SSIS. 

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="azure-sql-database"></a>Azure SQL Database 
Instrukcje przedstawione w tym artykule używa usługi Azure SQL database, który hostuje katalog usług SSIS. Można również użyć wystąpienia zarządzanego Azure SQL (wersja zapoznawcza).

## <a name="create-an-azure-ssis-integration-runtime"></a>Tworzenie środowiska Azure SSIS Integration Runtime
Tworzenie środowiska Azure-SSIS integration runtime, jeśli nie masz, wykonując instrukcje krok po kroku instrukcji w [samouczek: pakiety usług SSIS wdrażanie](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="data-factory-ui-azure-portal"></a>Interfejs użytkownika usługi Data Factory (witryna Azure portal)
W tej sekcji użyjesz interfejs użytkownika usługi Data Factory do tworzenia potoku usługi Data Factory z działaniem wykonywanie pakietu SSIS uruchomionym pakietu SSIS.

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych
Pierwszym krokiem jest, aby utworzyć fabrykę danych za pomocą witryny Azure portal. 

1. Uruchom przeglądarkę internetową **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome.
2. Przejdź do witryny [Azure Portal](https://portal.azure.com). 
3. Kliknij przycisk **Nowy** w lewym menu, kliknij pozycję **Dane + analiza**, a następnie kliknij pozycję **Data Factory**. 
   
   ![Nowy-> Fabryka danych](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. Na stronie **Nowa fabryka danych** wprowadź wartość **ADFTutorialDataFactory** w polu **Nazwa**. 
      
     ![Strona Nowa fabryka danych](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   Nazwa fabryki danych platformy Azure musi być **globalnie unikatowa**. Jeśli dla pola nazwy wystąpi poniższy błąd, zmień nazwę fabryki danych (np. twojanazwaADFTutorialDataFactory). Artykuł [Data Factory — Naming Rules (Usługa Data Factory — reguły nazewnictwa)](naming-rules.md) zawiera reguły nazewnictwa artefaktów usługi Data Factory.
  
     ![Nazwa nie jest dostępna — błąd](./media/how-to-invoke-ssis-package-stored-procedure-activity/name-not-available-error.png)
3. Wybierz **subskrypcję** Azure, w której chcesz utworzyć fabrykę danych. 
4. Dla opcji **Grupa zasobów** wykonaj jedną z następujących czynności:
     
      - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej. 
      - Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów.   
         
    Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
4. Wybierz opcję **V2** w obszarze **Wersja**.
5. Na liście **lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko lokalizacje obsługiwane przez usługę Data Factory. Magazyny danych (Azure Storage, Azure SQL Database itp.) i jednostki obliczeniowe (HDInsight itp.) używane przez fabrykę danych mogą mieścić się w innych lokalizacjach.
6. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**.     
7. Kliknij przycisk **Utwórz**.
8. Na pulpicie nawigacyjnym jest widoczny następujący kafelek ze stanem: **Wdrażanie fabryki danych**. 

    ![kafelek Wdrażanie fabryki danych](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. Po zakończeniu tworzenia zostanie wyświetlona strona **Fabryka danych**, jak pokazano na poniższej ilustracji.
   
    ![Strona główna fabryki danych](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Kliknij kafelek **Tworzenie i monitorowanie**, aby w osobnej karcie uruchomić interfejs użytkownika usługi Azure Data Factory. 

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Tworzenie potoku z działaniem wykonywanie pakietu SSIS
Aby utworzyć potok, w tym kroku Użyj interfejs użytkownika usługi Data Factory. Możesz dodać działanie wykonanie pakietu SSIS do potoku i jest skonfigurowana do uruchamiania pakietów SSIS. 

1. Na stronie wprowadzenia kliknij **Utwórz potok**: 

    ![Strona Wprowadzenie](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. W **działania** przybornika, rozwiń węzeł **ogólne**i przeciągnij i upuść **wykonywanie pakietu SSIS** działania do powierzchni projektanta potoku. 

   ![Przeciągnij działanie usług SSIS na powierzchnię projektanta](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. Na **ogólne** kartę właściwości działania wykonywanie pakietu SSIS, podaj nazwę i opis działania. Ustaw opcjonalny limit czasu, a następnie spróbuj ponownie wartości.

    ![Ustaw właściwości na karcie Ogólne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. Na **ustawienia** kartę właściwości wykonywanie pakietu SSIS działania, wybierz środowisko Azure-SSIS Integration Runtime skojarzone z `SSISDB` bazy danych, gdy pakiet jest wdrażany. Podaj ścieżkę pakietu w `SSISDB` bazy danych w formacie `<folder name>/<project name>/<package name>.dtsx`. Opcjonalnie określ wykonawcze 32-bitowe i poziomu rejestrowania wstępnie zdefiniowaną lub niestandardową i podaj ścieżkę środowiska, w formacie `<folder name>/<environment name>`.

    ![Ustaw właściwości na karcie Ustawienia](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

5. Aby sprawdzić poprawność konfiguracji potoku, kliknij przycisk **weryfikacji** na pasku narzędzi. Aby zamknąć okno **Raport weryfikacji potoku**, kliknij pozycję **>>**.

6. Opublikuj potok w fabryce Data Factory, klikając **Opublikuj wszystkie** przycisku. 

### <a name="optionally-parameterize-the-activity"></a>Opcjonalnie można zdefiniować parametry działania

Opcjonalnie można przypisać wartości, wyrażenia lub funkcji, które mogą odwoływać się do zmiennych systemowych fabryki danych, parametry projektu lub pakietu w formacie JSON za pomocą **kodu źródłowego widoku** przycisk u dołu wykonaj SSIS Okno działania pakietu, lub **kodu** przycisk w prawym górnym rogu obszaru potoku. Na przykład można przypisać parametrów potoku usługi fabryka danych do projektu usług SSIS i pakiet parametrów, jak pokazano na poniższych zrzutach ekranu:

![Edytuj skrypt JSON umożliwiający wykonanie pakietu SSIS działania](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-parameters.png)

![Dodaj parametry do działania wykonywanie pakietu SSIS](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-parameters2.png)

### <a name="run-and-monitor-the-pipeline"></a>Uruchamianie i monitorowanie potoku
W tej sekcji możesz wyzwolić uruchomienie potoku, a następnie monitorować je. 

1. Aby wyzwolić uruchomienie potoku, kliknij pozycję **wyzwalacza** na pasku narzędzi, a następnie kliknij przycisk **Wyzwól teraz**. 

    ![Wyzwól teraz](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. W oknie **Uruchomienie potoku** wybierz pozycję **Zakończ**. 

3. Przejdź do karty **Monitorowanie** po lewej stronie. Widzisz uruchomienie potoku i jego stan, oraz inne informacje (na przykład uruchom Start czasu). Aby odświeżyć widok, kliknij przycisk **Odśwież**.

    ![Uruchomienia potoków](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

4. Kliknij link **Wyświetl uruchomienia działania** w kolumnie **Akcje**. Zobaczysz tylko jedno działanie, Uruchom jako potok ma tylko jedno działanie (działanie wykonanie pakietu SSIS).

    ![Uruchomienia działania](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

5. Możesz uruchomić następujące polecenia, **zapytania** względem bazy danych SSISDB bazy danych serwera Azure SQL, aby sprawdzić, czy pakiet, który został wykonany. 

    ```sql
    select * from catalog.executions
    ```

    ![Sprawdź pakiet wykonań](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

6. Można również uzyskać identyfikator wykonywania SSISDB z danych wyjściowych uruchomienia działania potoku i użyj Identyfikatora, aby sprawdzić dzienniki wykonywania bardziej kompleksowe i komunikaty o błędach w programie SSMS.

    ![Pobierz identyfikator wykonania.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

> [!NOTE]
> Można również utworzyć zaplanowane wyzwalanie dla potoku, tak aby potok jest uruchamiany zgodnie z harmonogramem (co godzinę, codziennie itp.). Aby uzyskać przykład, zobacz [tworzenie fabryki danych — interfejs użytkownika usługi Data Factory](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="azure-powershell"></a>Azure PowerShell
W tej sekcji użyjesz programu Azure PowerShell do utworzenia potoku usługi fabryka danych za pomocą działania SSIS, które uruchamia pakietu SSIS. 

Zainstaluj najnowsze moduły programu Azure PowerShell, wykonując instrukcje podane w temacie [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-azurerm-ps). 

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych
Możesz używaj tego samego fabryki danych, który ma Azure-SSIS IR lub Utwórz fabrykę danych. Poniższa procedura zawiera kroki, aby utworzyć fabrykę danych. Utworzysz potok z działaniem usług SSIS w tej fabryce danych. Działanie usług SSIS uruchamia pakietu SSIS. 

1. Zdefiniuj zmienną nazwy grupy zasobów, której użyjesz później w poleceniach programu PowerShell. Skopiuj poniższy tekst polecenia do programu PowerShell, podaj nazwę [grupy zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) w podwójnych cudzysłowach, a następnie uruchom polecenie. Na przykład: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Jeśli grupa zasobów już istnieje, możesz zrezygnować z jej zastąpienia. Przypisz inną wartość do zmiennej `$ResourceGroupName` i ponownie uruchom polecenie.
2. Aby utworzyć grupę zasobów platformy Azure, uruchom następujące polecenie: 

    ```powershell
    $ResGrp = New-AzureRmResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Jeśli grupa zasobów już istnieje, możesz zrezygnować z jej zastąpienia. Przypisz inną wartość do zmiennej `$ResourceGroupName` i ponownie uruchom polecenie. 
3. Zdefiniuj zmienną nazwy fabryki danych. 

    > [!IMPORTANT]
    >  Zaktualizuj nazwę fabryki danych, aby była unikatowa w skali globalnej, 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. Aby utworzyć fabrykę danych, uruchom następujące polecenie cmdlet **Set-AzureRmDataFactoryV2**, używając właściwości Location i ResourceGroupName ze zmiennej $ResGrp: 
    
    ```powershell       
    $DataFactory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName `
                                            -Location $ResGrp.Location `
                                            -Name $dataFactoryName 
    ```

Pamiętaj o następujących kwestiach:

* Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli zostanie wyświetlony następujący błąd, zmień nazwę i spróbuj ponownie.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Aby utworzyć wystąpienia usługi Data Factory, konto użytkownika używane do logowania się na platformie Azure musi być członkiem roli **współautora** lub **właściciela** albo **administratorem** subskrypcji platformy Azure.
* Aby uzyskać listę regionów świadczenia usługi Azure, w których Data Factory jest dostępna, wybierz regiony, które Cię interesują na następującej stronie, a następnie rozwiń **Analytics** zlokalizować **usługi Data Factory**: [ Dostępność produktów według regionów](https://azure.microsoft.com/global-infrastructure/services/). Magazyny danych (Azure Storage, Azure SQL Database itp.) i jednostki obliczeniowe (HDInsight itp.) używane przez fabrykę danych mogą mieścić się w innych regionach.

### <a name="create-a-pipeline-with-an-ssis-activity"></a>Tworzenie potoku za pomocą działania SSIS 
W tym kroku utworzysz potok z działaniem usług SSIS. Działanie zostanie uruchomione pakietu SSIS. 

1. Utwórz plik JSON o nazwie **RunSSISPackagePipeline.json** w **C:\ADF\RunSSISPackage** folderze o zawartości jest podobne do poniższego przykładu:

    > [!IMPORTANT]
    > Zastąp nazwy obiektów, opisy i ścieżek, właściwości i wartości parametrów, hasła i inne wartości zmiennej przed zapisaniem pliku. 

    ```json
    {
        "name": "RunSSISPackagePipeline",
        "properties": {
            "activities": [{
                "name": "mySSISActivity",
                "description": "My SSIS package/activity description",
                "type": "ExecuteSSISPackage",
                "typeProperties": {
                    "connectVia": {
                        "referenceName": "myAzureSSISIR",
                        "type": "IntegrationRuntimeReference"
                    },
                    "runtime": "x64",
                    "loggingLevel": "Basic",
                    "packageLocation": {
                        "packagePath": "FolderName/ProjectName/PackageName.dtsx"            
                    },
                    "environmentPath":   "FolderName/EnvironmentName",
                    "projectParameters": {
                        "project_param_1": {
                            "value": "123"
                        }
                    },
                    "packageParameters": {
                        "package_param_1": {
                            "value": "345"
                        }
                    },
                    "projectConnectionManagers": {
                        "MyAdonetCM": {
                            "userName": {
                                "value": "sa"
                            },
                            "passWord": {
                                "value": {
                                    "type": "SecureString",
                                    "value": "abc"
                                }
                            }
                        }
                    },
                    "packageConnectionManagers": {
                        "MyOledbCM": {
                            "userName": {
                                "value": "sa"
                            },
                            "passWord": {
                                "value": {
                                    "type": "SecureString",
                                    "value": "def"
                                }
                            }
                        }
                    },
                    "propertyOverrides": {
                        "\\PackageName.dtsx\\MaxConcurrentExecutables ": {
                            "value": 8,
                            "isSensitive": false
                        }
                    }
                },
                "policy": {
                    "timeout": "0.01:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30
                }
            }]
        }
    }
    ```

2.  W programie Azure PowerShell Przełącz się do `C:\ADF\RunSSISPackage` folderu.

3. Aby utworzyć potok **RunSSISPackagePipeline**Uruchom **Set-AzureRmDataFactoryV2Pipeline** polecenia cmdlet.

    ```powershell
    $DFPipeLine = Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                   -ResourceGroupName $ResGrp.ResourceGroupName `
                                                   -Name "RunSSISPackagePipeline"
                                                   -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

    Oto przykładowe dane wyjściowe:

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-a-pipeline-run"></a>Tworzenie uruchomienia potoku
Użyj **Invoke-AzureRmDataFactoryV2Pipeline** polecenia cmdlet, aby uruchomić potok. Polecenie cmdlet zwraca identyfikator uruchomienia potoku w celu monitorowania w przyszłości.

```powershell
$RunId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline-run"></a>Monitorowanie działania potoku

Uruchom następujący skrypt programu PowerShell, aby stale sprawdzać stan uruchomienia potoku do momentu zakończenia kopiowania danych. Skopiuj/wklej poniższy skrypt w oknie programu PowerShell i naciśnij klawisz ENTER. 

```powershell
while ($True) {
    $Run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

Można również monitorować potok przy użyciu witryny Azure portal. Aby uzyskać instrukcje krok po kroku, zobacz [monitorowanie potoku](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="create-a-trigger"></a>Tworzenie wyzwalacza
W poprzednim kroku uruchomiono potoku na żądanie. Można również utworzyć wyzwalacz harmonogramu, aby uruchomić potok zgodnie z harmonogramem (co godzinę, codziennie itp.).

1. Utwórz plik JSON o nazwie **MyTrigger.json** w **C:\ADF\RunSSISPackage** folderu o następującej zawartości: 

    ```json
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Hour",
                    "interval": 1,
                    "startTime": "2017-12-07T00:00:00-08:00",
                    "endTime": "2017-12-08T00:00:00-08:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "RunSSISPackagePipeline"
                    },
                    "parameters": {}
                }
            ]
        }
    }    
    ```
2. W **programu Azure PowerShell**, przełącz się do **C:\ADF\RunSSISPackage** folderu.
3. Uruchom **Set-AzureRmDataFactoryV2Trigger** polecenia cmdlet, który tworzy wyzwalacz. 

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                    -DataFactoryName $DataFactory.DataFactoryName `
                                    -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. Domyślnie wyzwalacz jest w stanie zatrzymania. Uruchom wyzwalacz, uruchamiając **Start-AzureRmDataFactoryV2Trigger** polecenia cmdlet. 

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                      -DataFactoryName $DataFactory.DataFactoryName `
                                      -Name "MyTrigger" 
    ```
5. Upewnij się, że wyzwalacz jest uruchomiona, uruchamiając **Get-AzureRmDataFactoryV2Trigger** polecenia cmdlet. 

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                    -DataFactoryName $DataFactoryName `
                                    -Name "MyTrigger"     
    ```    
6. Uruchom następujące polecenie, po następnej pełnej godziny. Na przykład jeśli aktualna godzina jest 3:25 czasu UTC, uruchom polecenie o 16: 00 czasu UTC. 
    
    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                       -DataFactoryName $DataFactoryName `
                                       -TriggerName "MyTrigger" `
                                       -TriggerRunStartedAfter "2017-12-06" `
                                       -TriggerRunStartedBefore "2017-12-09"
    ```

    Można uruchomić następujące zapytanie względem bazy danych SSISDB znajdujących się na serwerze Azure SQL, aby sprawdzić, czy pakiet, który został wykonany. 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>Kolejne kroki
Zobacz następujący wpis w blogu:
-   [Modernizacja i rozszerzanie przepływów pracy usługi ETL/ELT, za pomocą działania SSIS w potokach ADF](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)
