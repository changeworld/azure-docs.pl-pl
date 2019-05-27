---
title: Uruchamianie pakietów SSIS za pomocą działania dotyczącego procedury składowanej — Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób uruchamiania pakietu usług SQL Server Integration Services (SSIS) w potoku usługi Azure Data Factory przy użyciu działania dotyczącego procedury składowanej.
services: data-factory
documentationcenter: ''
author: swinarko
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: sawinark
ms.openlocfilehash: b71a954da746ba04aeaa0797c13bf2c81838179d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66154946"
---
# <a name="run-an-ssis-package-with-the-stored-procedure-activity-in-azure-data-factory"></a>Uruchamianie pakietów SSIS za pomocą działania procedury składowanej w usłudze Azure Data Factory
W tym artykule opisano sposób uruchamiania pakietu SSIS w potoku usługi Azure Data Factory za pomocą działania procedury składowanej. 

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="azure-sql-database"></a>Azure SQL Database 
Instrukcje przedstawione w tym artykule używa usługi Azure SQL database, który hostuje katalog usług SSIS. Można również użyć bazy danych wystąpienia zarządzanego Azure SQL.

## <a name="create-an-azure-ssis-integration-runtime"></a>Tworzenie środowiska Azure SSIS Integration Runtime
Tworzenie środowiska Azure-SSIS integration runtime, jeśli nie masz, wykonując instrukcje krok po kroku instrukcji w [samouczka: Wdrażanie pakietów usług SSIS](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="data-factory-ui-azure-portal"></a>Interfejs użytkownika usługi Data Factory (witryna Azure portal)
W tej sekcji użyjesz interfejsu użytkownika usługi Data Factory do tworzenia potoku usługi fabryka danych za pomocą działania procedury składowanej, która wywołuje pakietu SSIS.

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
7. Kliknij pozycję **Utwórz**.
8. Na pulpicie nawigacyjnym jest widoczny następujący kafelek ze stanem: **Wdrażanie fabryki danych**. 

     ![kafelek Wdrażanie fabryki danych](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. Po zakończeniu tworzenia zostanie wyświetlona strona **Fabryka danych**, jak pokazano na poniższej ilustracji.
   
     ![Strona główna fabryki danych](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Kliknij kafelek **Tworzenie i monitorowanie**, aby w osobnej karcie uruchomić interfejs użytkownika usługi Azure Data Factory. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Tworzenie potoku za pomocą działania procedury składowanej
Aby utworzyć potok, w tym kroku Użyj interfejs użytkownika usługi Data Factory. Możesz dodać działanie procedury składowanej do potoku i jest skonfigurowana do uruchamiania pakietów SSIS za pomocą procedury przechowywanej sp_executesql. 

1. Na stronie wprowadzenia kliknij **Utwórz potok**: 

    ![Strona Wprowadzenie](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. W **działania** przybornika, rozwiń węzeł **ogólne**i przeciąganie i upuszczanie **procedury składowanej** działania do powierzchni projektanta potoku. 

    ![Przeciągnij i upuść działanie procedury składowanej](./media/how-to-invoke-ssis-package-stored-procedure-activity/drag-drop-sproc-activity.png)
3. W oknie dialogowym właściwości działania procedury składowanej, przejdź do **konta SQL** kartę, a następnie kliknij przycisk **+ nowy**. Utwórz połączenie z bazą danych Azure SQL który hostuje katalog usług SSIS (baza danych SSIDB). 
   
    ![Przycisk Nowa połączona usługa](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-linked-service-button.png)
4. W oknie **Nowa połączona usługa** wykonaj następujące czynności: 

    1. Wybierz **usługi Azure SQL Database** dla **typu**.
    2. Wybierz **domyślne** Azure Integration Runtime do łączenia z usługą Azure SQL Database, który jest hostem `SSISDB` bazy danych.
    3. Wybierz bazę danych SQL Azure, który hostuje bazę danych SSISDB **nazwy serwera** pola.
    4. Wybierz **SSISDB** dla **Nazwa bazy danych**.
    5. Aby uzyskać **nazwa_użytkownika**, wprowadź nazwę użytkownika, który ma dostęp do bazy danych.
    6. Aby uzyskać **hasło**, wprowadź hasło użytkownika. 
    7. Przetestuj połączenie z bazą danych, klikając **Testuj połączenie** przycisku.
    8. Zapisać połączoną usługę, klikając **Zapisz** przycisku. 

        ![Połączona usługa Azure SQL Database](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-settings.png)
5. W oknie właściwości przejdź do **procedury składowanej** z karty **konta SQL** , a następnie wykonaj następujące czynności: 

    1. Wybierz pozycję **Edit** (Edytuj). 
    2. Aby uzyskać **Nazwa procedury składowanej** pole, wprowadź `sp_executesql`. 
    3. Kliknij przycisk **+ nowy** w **parametry procedury składowanej** sekcji. 
    4. Aby uzyskać **nazwa** parametru, wprowadź **instrukcji INSERT**. 
    5. Aby uzyskać **typu** parametru, wprowadź **ciąg**. 
    6. Aby uzyskać **wartość** parametru, wpisz następujące zapytanie SQL:

        W zapytaniu SQL podaj odpowiednie wartości dla **nazwa_folderu**, **project_name**, i **nazwa_pakietu** parametrów. 

        ```sql
        DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER name in SSIS Catalog>', @project_name=N'<PROJECT name in SSIS Catalog>', @package_name=N'<PACKAGE name>.dtsx', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END
        ```

        ![Połączona usługa Azure SQL Database](./media/how-to-invoke-ssis-package-stored-procedure-activity/stored-procedure-settings.png)
6. Aby sprawdzić poprawność konfiguracji potoku, kliknij przycisk **weryfikacji** na pasku narzędzi. Aby zamknąć okno **Raport weryfikacji potoku**, kliknij pozycję **>>**.

    ![Weryfikowanie potoku](./media/how-to-invoke-ssis-package-stored-procedure-activity/validate-pipeline.png)
7. Opublikuj potok w fabryce Data Factory, klikając **Opublikuj wszystkie** przycisku. 

    ![Opublikuj](./media/how-to-invoke-ssis-package-stored-procedure-activity/publish-all-button.png)    

### <a name="run-and-monitor-the-pipeline"></a>Uruchamianie i monitorowanie potoku
W tej sekcji możesz wyzwolić uruchomienie potoku, a następnie monitorować je. 

1. Aby wyzwolić uruchomienie potoku, kliknij pozycję **wyzwalacza** na pasku narzędzi, a następnie kliknij przycisk **Wyzwól teraz**. 

    ![Wyzwól teraz](media/how-to-invoke-ssis-package-stored-procedure-activity/trigger-now.png)

2. W oknie **Uruchomienie potoku** wybierz pozycję **Zakończ**. 
3. Przejdź do karty **Monitorowanie** po lewej stronie. Widzisz uruchomienie potoku i jego stan, oraz inne informacje (na przykład uruchom Start czasu). Aby odświeżyć widok, kliknij przycisk **Odśwież**.

    ![Uruchomienia potoków](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

3. Kliknij link **Wyświetl uruchomienia działania** w kolumnie **Akcje**. Zobaczysz tylko jedno działanie, Uruchom jako potok ma tylko jedno działanie (działanie procedury składowanej).

    ![Uruchomienia działania](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-runs.png)

4. Możesz uruchomić następujące polecenia, **zapytania** względem bazy danych SSISDB bazy danych serwera Azure SQL, aby sprawdzić, czy pakiet, który został wykonany. 

    ```sql
    select * from catalog.executions
    ```

    ![Sprawdź pakiet wykonań](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)


> [!NOTE]
> Można również utworzyć zaplanowane wyzwalanie dla potoku, tak aby potok jest uruchamiany zgodnie z harmonogramem (co godzinę, codziennie itp.). Aby uzyskać przykład, zobacz [tworzenie fabryki danych — interfejs użytkownika usługi Data Factory](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tej sekcji użyjesz programu Azure PowerShell do utworzenia potoku usługi fabryka danych za pomocą działania procedury składowanej, która wywołuje pakietu SSIS. 

Zainstaluj najnowsze moduły programu Azure PowerShell, wykonując instrukcje podane w temacie [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-az-ps). 

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych
Możesz używaj tego samego fabryki danych, który ma Azure-SSIS IR lub Utwórz fabrykę danych. Poniższa procedura zawiera kroki, aby utworzyć fabrykę danych. Utworzysz potok z działaniem procedury składowanej w tej fabryce danych. Działanie procedury składowanej wykonuje procedurę składowaną w bazie danych SSISDB do uruchamiania pakietu SSIS. 

1. Zdefiniuj zmienną nazwy grupy zasobów, której użyjesz później w poleceniach programu PowerShell. Skopiuj poniższy tekst polecenia do programu PowerShell, podaj nazwę [grupy zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) w podwójnych cudzysłowach, a następnie uruchom polecenie. Na przykład: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Jeśli grupa zasobów już istnieje, możesz zrezygnować z jej zastąpienia. Przypisz inną wartość do zmiennej `$ResourceGroupName` i ponownie uruchom polecenie.
2. Aby utworzyć grupę zasobów platformy Azure, uruchom następujące polecenie: 

    ```powershell
    $ResGrp = New-AzResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Jeśli grupa zasobów już istnieje, możesz zrezygnować z jej zastąpienia. Przypisz inną wartość do zmiennej `$ResourceGroupName` i ponownie uruchom polecenie. 
3. Zdefiniuj zmienną nazwy fabryki danych. 

    > [!IMPORTANT]
    >  Zaktualizuj nazwę fabryki danych, aby była unikatowa w skali globalnej, 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. Aby utworzyć fabrykę danych, uruchom następujące polecenie **AzDataFactoryV2 zestaw** polecenia cmdlet, używając właściwości Location i ResourceGroupName ze zmiennej $ResGrp: 
    
    ```powershell       
    $DataFactory = Set-AzDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName -Location $ResGrp.Location -Name $dataFactoryName 
    ```

Pamiętaj o następujących kwestiach:

* Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli zostanie wyświetlony następujący błąd, zmień nazwę i spróbuj ponownie.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Aby utworzyć wystąpienia usługi Data Factory, konto użytkownika używane do logowania się na platformie Azure musi być członkiem roli **współautora** lub **właściciela** albo **administratorem** subskrypcji platformy Azure.
* Aby uzyskać listę regionów platformy Azure, w których obecnie jest dostępna usługa Data Factory, wybierz dane regiony na poniższej stronie, a następnie rozwiń węzeł **Analiza**, aby zlokalizować pozycję **Data Factory**: [Dostępność produktów według regionów](https://azure.microsoft.com/global-infrastructure/services/). Magazyny danych (Azure Storage, Azure SQL Database itp.) i jednostki obliczeniowe (HDInsight itp.) używane przez fabrykę danych mogą mieścić się w innych regionach.

### <a name="create-an-azure-sql-database-linked-service"></a>Tworzenie połączonej usługi Azure SQL Database
Utwórz połączoną usługę służącą do połączenia z bazą danych Azure SQL hostującym wykazu usług SSIS z fabryką danych. Data Factory używa informacji w tej połączonej usługi, do łączenia z bazą danych SSISDB i wykonuje procedurę składowaną do uruchamiania pakietów SSIS. 

1. Utwórz plik JSON o nazwie **AzureSqlDatabaseLinkedService.json** w **C:\ADF\RunSSISPackage** folderu o następującej zawartości: 

    > [!IMPORTANT]
    > Zastąp &lt;servername&gt;, &lt;username&gt;, i &lt;hasło&gt; przy użyciu wartości usługi Azure SQL Database przed zapisaniem pliku.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                }
            }
        }
    }
    ```

2. W **programu Azure PowerShell**, przełącz się do **C:\ADF\RunSSISPackage** folderu.

3. Uruchom **AzDataFactoryV2LinkedService zestaw** polecenia cmdlet, aby utworzyć połączoną usługę: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Tworzenie potoku za pomocą działania procedury składowanej 
W tym kroku utworzysz potok z działaniem procedury składowanej. Działanie wywołuje procedury przechowywanej sp_executesql do uruchamiania pakietu SSIS. 

1. Utwórz plik JSON o nazwie **RunSSISPackagePipeline.json** w **C:\ADF\RunSSISPackage** folderu o następującej zawartości:

    > [!IMPORTANT]
    > Zastąp &lt;nazwa folderu&gt;, &lt;Nazwa projektu&gt;, &lt;nazwy pakietu&gt; nazwą folderu projektu i pakietu w katalogu usług SSIS przed zapisaniem pliku. 

    ```json
    {
        "name": "RunSSISPackagePipeline",
        "properties": {
            "activities": [
                {
                    "name": "My SProc Activity",
                    "description":"Runs an SSIS package",
                    "type": "SqlServerStoredProcedure",
                    "linkedServiceName": {
                        "referenceName": "AzureSqlDatabaseLinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "typeProperties": {
                        "storedProcedureName": "sp_executesql",
                        "storedProcedureParameters": {
                            "stmt": {
                                "value": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER NAME>', @project_name=N'<PROJECT NAME>', @package_name=N'<PACKAGE NAME>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                            }
                        }
                    }
                }
            ]
        }
    }
    ```

2. Aby utworzyć potok: **RunSSISPackagePipeline**Uruchom **AzDataFactoryV2Pipeline zestaw** polecenia cmdlet.

    ```powershell
    $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
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
Użyj **Invoke AzDataFactoryV2Pipeline** polecenia cmdlet, aby uruchomić potok. Polecenie cmdlet zwraca identyfikator uruchomienia potoku w celu monitorowania w przyszłości.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline-run"></a>Monitorowanie działania potoku

Uruchom następujący skrypt programu PowerShell, aby stale sprawdzać stan uruchomienia potoku do momentu zakończenia kopiowania danych. Skopiuj/wklej poniższy skrypt w oknie programu PowerShell i naciśnij klawisz ENTER. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -PipelineRunId $RunId

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

### <a name="create-a-trigger"></a>Tworzenie wyzwalacza
W poprzednim kroku należy wywołać potoku na żądanie. Można również utworzyć wyzwalacz harmonogramu, aby uruchomić potok zgodnie z harmonogramem (co godzinę, codziennie itp.).

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
3. Uruchom **AzDataFactoryV2Trigger zestaw** polecenia cmdlet, który tworzy wyzwalacz. 

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. Domyślnie wyzwalacz jest w stanie zatrzymania. Uruchom wyzwalacz, uruchamiając **Start AzDataFactoryV2Trigger** polecenia cmdlet. 

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" 
    ```
5. Upewnij się, że wyzwalacz jest uruchomiona, uruchamiając **Get AzDataFactoryV2Trigger** polecenia cmdlet. 

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"     
    ```    
6. Uruchom następujące polecenie, po następnej pełnej godziny. Na przykład jeśli aktualna godzina jest 3:25 czasu UTC, uruchom polecenie o 16: 00 czasu UTC. 
    
    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-06" -TriggerRunStartedBefore "2017-12-09"
    ```

    Można uruchomić następujące zapytanie względem bazy danych SSISDB znajdujących się na serwerze Azure SQL, aby sprawdzić, czy pakiet, który został wykonany. 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>Kolejne kroki
Można również monitorować potok przy użyciu witryny Azure portal. Aby uzyskać instrukcje krok po kroku, zobacz [monitorowanie potoku](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).
