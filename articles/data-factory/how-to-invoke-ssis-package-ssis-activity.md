---
title: Uruchom pakiet SSIS przy użyciu wykonania działania pakietu SSIS w fabryce danych Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób uruchamiania pakietu SQL Server Integration Services (SSIS) z potoku fabryki danych Azure za pomocą działania wykonanie pakietu usług SSIS.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: douglasl
ms.openlocfilehash: fed4e10fcaaa5282c37b175f355b94522c3b2b46
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34700489"
---
# <a name="run-an-ssis-package-using-the-execute-ssis-package-activity-in-azure-data-factory"></a>Uruchom pakiet SSIS za pomocą działania wykonanie pakietu usług SSIS w fabryce danych Azure
W tym artykule opisano sposób uruchamiania pakietów SSIS z potoku fabryki danych Azure za pomocą działania wykonanie pakietu usług SSIS. 

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Działanie wykonanie pakietu usług SSIS nie jest dostępna w wersji 1 usługi fabryka danych, która jest ogólnie dostępna (GA). Alternatywne metody do uruchamiania pakietów SSIS wersji 1 usługi fabryka danych, zobacz [pakietów SSIS Uruchom za pomocą działania procedury składowanej w wersji 1](v1/how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="azure-sql-database"></a>Azure SQL Database 
Wskazówki w tym artykule używa bazy danych Azure SQL katalogiem usług SSIS. Można również użyć Azure zarządzane wystąpienia SQL (wersja zapoznawcza).

## <a name="create-an-azure-ssis-integration-runtime"></a>Tworzenie środowiska Azure SSIS Integration Runtime
Tworzenie środowiska uruchomieniowego integracji usług SSIS Azure, jeśli nie masz, wykonując instrukcje krok po kroku w [samouczek: pakiety wdrażania usług SSIS](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="data-factory-ui-azure-portal"></a>Fabryka danych interfejsu użytkownika (Azure portal)
W tej sekcji służy do utworzyć potok fabryki danych z uruchomionym pakietów SSIS działanie wykonanie pakietu usług SSIS interfejsu użytkownika z fabryki danych.

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych
Pierwszym krokiem jest tworzenie fabryki danych przy użyciu portalu Azure. 

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
4. Wybierz wartość **V2 (wersja zapoznawcza)** dla **wersji**.
5. Na liście **lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko lokalizacje obsługiwane przez usługę Data Factory. Magazyny danych (Azure Storage, Azure SQL Database itp.) i jednostki obliczeniowe (HDInsight itp.) używane przez fabrykę danych mogą mieścić się w innych lokalizacjach.
6. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**.     
7. Kliknij przycisk **Utwórz**.
8. Na pulpicie nawigacyjnym jest widoczny następujący kafelek ze stanem: **Wdrażanie fabryki danych**. 

    ![kafelek Wdrażanie fabryki danych](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. Po zakończeniu tworzenia zostanie wyświetlona strona **Fabryka danych**, jak pokazano na poniższej ilustracji.
   
    ![Strona główna fabryki danych](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Kliknij kafelek **Tworzenie i monitorowanie**, aby w osobnej karcie uruchomić interfejs użytkownika usługi Azure Data Factory. 

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Tworzenie potoku do działania wykonanie pakietu usług SSIS
W tym kroku używasz interfejsu użytkownika z fabryki danych do utworzenia potoku. Należy dodać działanie wykonanie pakietu usług SSIS do potoku i skonfigurować je do uruchamiania pakietów SSIS. 

1. W strony wprowadzenie, kliknij przycisk **tworzenie potoku**: 

    ![Strona Wprowadzenie](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. W **działania** przybornika, rozwiń węzeł **ogólne**, przeciągnij i upuść **wykonanie pakietu usług SSIS** działania na powierzchnię projektanta potoku. 

   ![Przeciągnij działanie SSIS powierzchnię projektanta](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. Na **ogólne** kartę właściwości działania wykonanie pakietu usług SSIS, podaj nazwę i opis dla działania. Ustaw limit czasu opcjonalne, a następnie ponów wartości.

    ![Ustaw właściwości na karcie Ogólne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. Na **ustawienia** na karcie właściwości pakietu SSIS wykonania działania, wybierz środowiska uruchomieniowego integracji usług SSIS Azure skojarzony z `SSISDB` bazy danych, gdy pakiet jest wdrażany. Podaj ścieżkę pakietu w `SSISDB` bazy danych w formacie `<folder name>/<project name>/<package name>.dtsx`. Opcjonalnie określ wykonywania 32-bitowe i poziom rejestrowania wstępnie zdefiniowanych lub niestandardowych i podaj ścieżkę środowiska w formacie `<folder name>/<environment name>`.

    ![Ustaw właściwości na karcie Ustawienia](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

5. Aby sprawdzić poprawność konfiguracji potoku, kliknij przycisk **weryfikacji** na pasku narzędzi. Aby zamknąć okno **Raport weryfikacji potoku**, kliknij pozycję **>>**.

6. Publikowanie potoku fabryki danych, klikając **publikowania wszystkich** przycisku. 

### <a name="optionally-parameterize-the-activity"></a>Opcjonalnie parametryzacja działania

Opcjonalnie można przypisać wartości, wyrażenia lub funkcje, które mogą odwoływać się do fabryki danych zmienne systemowe, aby parametry projektu lub pakietu w formacie JSON na **zaawansowane** kartę. Na przykład można przypisać parametry potoku fabryki danych do projektu SSIS lub pakietu parametrów, jak pokazano na poniższym zrzucie ekranu:

![Dodawanie parametrów do działania wykonanie pakietu usług SSIS](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-parameters.png)

### <a name="run-and-monitor-the-pipeline"></a>Uruchomić i monitorować potoku
W tej sekcji wyzwalanie wykonywania potoku, a następnie monitorować go. 

1. Aby wyzwolić potoku uruchamiania, kliknij przycisk **wyzwalacza** na pasku narzędzi, a następnie kliknij przycisk **uruchomić teraz**. 

    ![Wyzwól teraz](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. W oknie **Uruchomienie potoku** wybierz pozycję **Zakończ**. 

3. Przejdź do karty **Monitorowanie** po lewej stronie. Zostanie wyświetlony potoku uruchamiania i jego stan oraz innych informacji (na przykład czas uruchomienia Start). Aby odświeżyć widok, kliknij przycisk **Odśwież**.

    ![Uruchomienia potoków](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

4. Kliknij link **Wyświetl uruchomienia działania** w kolumnie **Akcje**. Użytkownik widzi tylko jedno działanie Uruchom jako potoku ma tylko jedno działanie (działanie wykonanie pakietu usług SSIS).

    ![Uruchomienia działania](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

5. Można uruchomić następujące **zapytania** względem bazy danych SSISDB bazy danych na serwerze Azure SQL, aby sprawdzić, czy pakiet wykonywane. 

    ```sql
    select * from catalog.executions
    ```

    ![Sprawdź wykonaniami pakietu](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

6. Można również Pobierz identyfikator wykonywania SSISDB z danych wyjściowych wykonywania działania potoku i użyj identyfikator, aby sprawdzić bardziej szczegółowe dzienniki wykonywania i komunikaty o błędach w programie SSMS.

    ![Pobierz identyfikator wykonania.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

> [!NOTE]
> Można również tworzyć wyzwalacz zaplanowane do potoku sieci, dzięki czemu potoku działa zgodnie z harmonogramem (co godzinę, codziennie, itp.). Na przykład zobacz [tworzenie fabryki danych - UI fabryki danych](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="azure-powershell"></a>Azure PowerShell
W tej sekcji umożliwia programu Azure PowerShell utworzyć potok fabryki danych z działaniem SSIS uruchomioną pakietów SSIS. 

Zainstaluj najnowsze moduły programu Azure PowerShell, wykonując instrukcje podane w temacie [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-azurerm-ps). 

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych
Możesz użyć tej samej fabryki danych zawierający IR Azure SSIS lub tworzenie fabryki danych. Poniższa procedura zawiera kroki, aby utworzyć fabryki danych. Możesz utworzyć potok aktywnością SSIS w tej fabryce danych. Działanie SSIS uruchamia pakietu SSIS. 

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
* Obecnie fabryki danych w wersji 2 umożliwia tworzenie fabryki danych tylko w wschodnie stany USA, wschodnie US2, Europa Zachodnia i regiony Azja południowo-wschodnia. Magazyny danych (Azure Storage, Azure SQL Database itp.) i jednostki obliczeniowe (HDInsight itp.) używane przez fabrykę danych mogą mieścić się w innych regionach.

### <a name="create-a-pipeline-with-an-ssis-activity"></a>Tworzenie potoku do działania SSIS 
W tym kroku możesz utworzyć potok z działaniem SSIS. Działanie jest uruchomione pakietu SSIS. 

1. Utwórz plik JSON o nazwie **RunSSISPackagePipeline.json** w **C:\ADF\RunSSISPackage** folder z zawartością, podobnie do poniższego przykładu:

    > [!IMPORTANT]
    > Zamień nazwy obiektów, opisy i ścieżki, właściwości i wartości parametrów, hasła i inne wartości zmiennych przed zapisaniem pliku. 

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

2.  W programie Azure PowerShell, przełącz się do `C:\ADF\RunSSISPackage` folderu.

3. Można utworzyć potoku **RunSSISPackagePipeline**Uruchom **AzureRmDataFactoryV2Pipeline zestaw** polecenia cmdlet.

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
Użyj **Invoke AzureRmDataFactoryV2Pipeline** polecenia cmdlet, aby uruchomić potoku. Polecenie cmdlet zwraca identyfikator uruchomienia potoku w celu monitorowania w przyszłości.

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

Można również monitorować potoku przy użyciu portalu Azure. Aby uzyskać instrukcje, zobacz [monitorować potoku](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="create-a-trigger"></a>Tworzenie wyzwalacza
W poprzednim kroku uruchomiono potoku na żądanie. Można również utworzyć wyzwalacz harmonogramu uruchamiania potoku zgodnie z harmonogramem (co godzinę, codziennie, itp.).

1. Utwórz plik JSON o nazwie **MyTrigger.json** w **C:\ADF\RunSSISPackage** folderu o następującej treści: 

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
3. Uruchom **AzureRmDataFactoryV2Trigger zestaw** polecenia cmdlet, który tworzy wyzwalacz. 

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                    -DataFactoryName $DataFactory.DataFactoryName `
                                    -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. Domyślnie wyzwalacza jest w stanie zatrzymania. Uruchom wyzwalacza, uruchamiając **Start AzureRmDataFactoryV2Trigger** polecenia cmdlet. 

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                      -DataFactoryName $DataFactory.DataFactoryName `
                                      -Name "MyTrigger" 
    ```
5. Upewnij się, że wyzwalacz rozpoczyna się przez uruchomienie **Get-AzureRmDataFactoryV2Trigger** polecenia cmdlet. 

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                    -DataFactoryName $DataFactoryName `
                                    -Name "MyTrigger"     
    ```    
6. Uruchom następujące polecenie, po następnej godziny. Na przykład jeśli bieżący czas UTC 3:25 PM, uruchom polecenie w 16: 00 czasu UTC. 
    
    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                       -DataFactoryName $DataFactoryName `
                                       -TriggerName "MyTrigger" `
                                       -TriggerRunStartedAfter "2017-12-06" `
                                       -TriggerRunStartedBefore "2017-12-09"
    ```

    Można uruchomić następujące zapytanie w bazie danych usług SSIS znajdujących się na serwerze Azure SQL, aby sprawdzić, czy pakiet wykonywane. 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>Kolejne kroki
Zobacz następującym wpisie w blogu:
-   [Modernizacji i rozszerzanie przepływy pracy ETL/ELT z działaniami SSIS w potokach ADF](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)
