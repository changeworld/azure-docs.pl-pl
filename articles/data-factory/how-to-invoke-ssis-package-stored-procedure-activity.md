---
title: Wywołanie pakietów SSIS przy użyciu fabryki danych Azure - działania dotyczącego procedury składowanej | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak można wywołać z potoku fabryki danych Azure za pomocą działania dotyczącego procedury składowanej pakiet SQL Server Integration Services (SSIS).
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: article
ms.date: 12/07/2017
ms.author: jingwang
ms.openlocfilehash: 00a4401a9116d8ebbfefa56194fe45802bcf198e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Wywołanie pakietów SSIS za pomocą działania procedury składowanej w fabryce danych Azure
W tym artykule opisano sposób wywołania pakietów SSIS z potoku fabryki danych Azure za pomocą działania procedury składowanej. 

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 usługi fabryka danych, która jest ogólnie dostępna (GA), zobacz [pakietów SSIS wywołać przy użyciu działania procedury składowanej w wersji 1](v1/how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="azure-sql-database"></a>Azure SQL Database 
Wskazówki w tym artykule używa bazy danych Azure SQL katalogiem usług SSIS. Można również użyć Azure zarządzane wystąpienia SQL (wersja zapoznawcza).

## <a name="create-an-azure-ssis-integration-runtime"></a>Tworzenie środowiska Azure SSIS Integration Runtime
Tworzenie środowiska uruchomieniowego integracji usług SSIS Azure, jeśli nie masz, wykonując instrukcje krok po kroku w [samouczek: pakiety wdrażania usług SSIS](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="data-factory-ui-azure-portal"></a>Fabryka danych interfejsu użytkownika (Azure portal)
W tej sekcji służy do utworzyć potok fabryki danych z działaniem procedury składowanej wywołująca pakietów SSIS interfejsu użytkownika z fabryki danych.

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

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Utworzyć potok z działania procedury składowanej
W tym kroku używasz interfejsu użytkownika z fabryki danych do utworzenia potoku. Dodaj działania procedury składowanej do potoku i skonfigurować go do uruchamiania pakietów SSIS za pomocą procedury przechowywanej sp_executesql. 

1. W strony wprowadzenie, kliknij przycisk **tworzenie potoku**: 

    ![Strona Wprowadzenie](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. W **działania** przybornika, rozwiń węzeł **ogólne**i przeciągnij upuść **procedury składowanej** działania na powierzchnię projektanta potoku. 

    ![Działania procedury składowanej przeciągnij i upuść](./media/how-to-invoke-ssis-package-stored-procedure-activity/drag-drop-sproc-activity.png)
3. W oknie dialogowym właściwości działania procedury składowanej, przełącz się do **konto SQL** , a następnie kliknij pozycję **+ nowy**. Utwórz połączenie z bazą danych Azure SQL obsługującego katalogu SSIS (SSIDB bazy danych). 
   
    ![Przycisk Nowa połączona usługa](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-linked-service-button.png)
4. W oknie **Nowa połączona usługa** wykonaj następujące czynności: 

    1. Wybierz **baza danych Azure SQL** dla **typu**.
    2. Wybierz serwer Azure SQL obsługującym bazę danych usług SSIS dla **nazwy serwera** pola.
    3. Wybierz **SSISDB** dla **Nazwa bazy danych**.
    4. Aby uzyskać **nazwy użytkownika**, wprowadź nazwę użytkownika, który ma dostęp do bazy danych.
    5. Aby uzyskać **hasło**, wprowadź hasło użytkownika. 
    6. Testowanie połączenia z bazą danych, klikając **połączenie testowe** przycisku.
    7. Zapisz połączonej usługi, klikając **zapisać** przycisku. 

        ![Połączona usługa Azure SQL Database](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-settings.png)
5. W oknie właściwości, przełącz się do **procedury składowanej** karcie z **konto SQL** , i wykonaj następujące czynności: 

    1. Wybierz pozycję **Edit** (Edytuj). 
    2. Dla **nazwę procedury przechowywane** pola Enter `sp_executesql`. 
    3. Kliknij przycisk **+ nowy** w **parametry procedury przechowywane** sekcji. 
    4. Aby uzyskać **nazwa** parametru, wpisz **instrukcji INSERT**. 
    5. Aby uzyskać **typu** parametru, wpisz **ciąg**. 
    6. Aby uzyskać **wartość** parametru, wpisz poniższe zapytanie SQL:

        W zapytaniu SQL określ wartości prawo **nazwa_folderu**, **project_name**, i **nazwa_pakietu** parametrów. 

        ```sql
        DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER name in SSIS Catalog>', @project_name=N'<PROJECT name in SSIS Catalog>', @package_name=N'<PACKAGE name>.dtsx', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END
        ```

        ![Połączona usługa Azure SQL Database](./media/how-to-invoke-ssis-package-stored-procedure-activity/stored-procedure-settings.png)
6. Aby sprawdzić poprawność konfiguracji potoku, kliknij przycisk **weryfikacji** na pasku narzędzi. Aby zamknąć okno **Raport weryfikacji potoku**, kliknij pozycję **>>**.

    ![Weryfikowanie potoku](./media/how-to-invoke-ssis-package-stored-procedure-activity/validate-pipeline.png)
7. Publikowanie potoku fabryki danych, klikając **publikowania wszystkich** przycisku. 

    ![Publikowanie](./media/how-to-invoke-ssis-package-stored-procedure-activity/publish-all-button.png)    

### <a name="run-and-monitor-the-pipeline"></a>Uruchomić i monitorować potoku
W tej sekcji wyzwalanie wykonywania potoku, a następnie monitorować go. 

1. Aby wyzwolić potoku uruchamiania, kliknij przycisk **wyzwalacza** na pasku narzędzi, a następnie kliknij przycisk **uruchomić teraz**. 

    ![Wyzwól teraz](./media/how-to-invoke-ssis-package-stored-procedure-activity/trigger-now.png)
2. W oknie **Uruchomienie potoku** wybierz pozycję **Zakończ**. 
3. Przejdź do karty **Monitorowanie** po lewej stronie. Zostanie wyświetlony potoku uruchamiania i jego stan oraz innych informacji (na przykład czas uruchomienia Start). Aby odświeżyć widok, kliknij przycisk **Odśwież**.

    ![Uruchomienia potoków](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)
3. Kliknij link **Wyświetl uruchomienia działania** w kolumnie **Akcje**. Użytkownik widzi tylko jedno działanie Uruchom jako potoku ma tylko jedno działanie (działania procedury składowanej).

    ![Uruchomień działania](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-runs.png) można uruchomić następujące 4 **zapytania** względem bazy danych SSISDB bazy danych na serwerze Azure SQL, aby sprawdzić, czy pakiet wykonywane. 

    ```sql
    select * from catalog.executions
    ```

    ![Sprawdź wykonaniami pakietu](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)


> [!NOTE]
> Można również tworzyć wyzwalacz zaplanowane do potoku sieci, dzięki czemu potoku działa zgodnie z harmonogramem (co godzinę, codziennie, itp.). Na przykład zobacz [tworzenie fabryki danych - UI fabryki danych](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="azure-powershell"></a>Azure PowerShell
W tej sekcji umożliwia programu Azure PowerShell utworzyć potok fabryki danych z działaniem procedury składowanej wywołująca pakietów SSIS. 

Zainstaluj najnowsze moduły programu Azure PowerShell, wykonując instrukcje podane w temacie [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-azurerm-ps). 

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych
Możesz użyć tej samej fabryki danych zawierający IR Azure SSIS lub tworzenie fabryki danych. Poniższa procedura zawiera kroki, aby utworzyć fabryki danych. Możesz utworzyć potok z działaniem procedury składowanej w tej fabryce danych. Działania procedury składowanej wykonuje procedurę przechowywaną w bazie danych usług SSIS do uruchamiania pakietu SSIS. 

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
    $DataFactory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName -Location $ResGrp.Location -Name $dataFactoryName 
    ```

Pamiętaj o następujących kwestiach:

* Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli zostanie wyświetlony następujący błąd, zmień nazwę i spróbuj ponownie.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Aby utworzyć wystąpienia usługi Data Factory, konto użytkownika używane do logowania się na platformie Azure musi być członkiem roli **współautora** lub **właściciela** albo **administratorem** subskrypcji platformy Azure.
* Obecnie usługa Data Factory w wersji 2 umożliwia tworzenie fabryk danych tylko w regionach Wschodnie stany USA, Wschodnie stany USA 2 i Europa Zachodnia. Magazyny danych (Azure Storage, Azure SQL Database itp.) i jednostki obliczeniowe (HDInsight itp.) używane przez fabrykę danych mogą mieścić się w innych regionach.

### <a name="create-an-azure-sql-database-linked-service"></a>Tworzenie połączonej usługi Azure SQL Database
Tworzenie połączonej usługi, aby połączyć bazy danych Azure SQL obsługującego katalogu SSIS z fabryką danych. Fabryka danych używa informacji dostępnych w tej połączonej usługi do łączenia z bazą danych usług SSIS i wykonuje procedurę składowaną do uruchamiania pakietów SSIS. 

1. Utwórz plik JSON o nazwie **AzureSqlDatabaseLinkedService.json** w **C:\ADF\RunSSISPackage** folderu o następującej treści: 

    > [!IMPORTANT]
    > Zastąp &lt;servername&gt;, &lt;username&gt;, i &lt;hasło&gt; z wartościami bazy danych SQL Azure przed zapisaniem pliku.

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

3. Uruchom polecenie cmdlet **Set-AzureRmDataFactoryV2LinkedService**, aby utworzyć połączoną usługę: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Utworzyć potok z działania procedury składowanej 
W tym kroku możesz utworzyć potok z działania procedury składowanej. Działanie wywołuje procedury przechowywanej sp_executesql, aby uruchomić pakiet SSIS. 

1. Utwórz plik JSON o nazwie **RunSSISPackagePipeline.json** w **C:\ADF\RunSSISPackage** folderu o następującej treści:

    > [!IMPORTANT]
    > Zastąp &lt;nazwa folderu&gt;, &lt;Nazwa projektu&gt;, &lt;nazwy pakietu&gt; z nazwami folderu projektu i pakietu w katalogu usług SSIS przed zapisaniem pliku. 

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

2. Można utworzyć potoku: **RunSSISPackagePipeline**Uruchom **AzureRmDataFactoryV2Pipeline zestaw** polecenia cmdlet.

    ```powershell
    $DFPipeLine = Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
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
$RunId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline-run"></a>Monitorowanie działania potoku

Uruchom następujący skrypt programu PowerShell, aby stale sprawdzać stan uruchomienia potoku do momentu zakończenia kopiowania danych. Skopiuj/wklej poniższy skrypt w oknie programu PowerShell i naciśnij klawisz ENTER. 

```powershell
while ($True) {
    $Run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -PipelineRunId $RunId

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
W poprzednim kroku należy wywołać potoku na żądanie. Można również utworzyć wyzwalacz harmonogramu uruchamiania potoku zgodnie z harmonogramem (co godzinę, codziennie, itp.).

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
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. Domyślnie wyzwalacza jest w stanie zatrzymania. Uruchom wyzwalacza, uruchamiając **Start AzureRmDataFactoryV2Trigger** polecenia cmdlet. 

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" 
    ```
5. Upewnij się, że wyzwalacz rozpoczyna się przez uruchomienie **Get-AzureRmDataFactoryV2Trigger** polecenia cmdlet. 

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"     
    ```    
6. Uruchom następujące polecenie, po następnej godziny. Na przykład jeśli bieżący czas UTC 3:25 PM, uruchom polecenie w 16: 00 czasu UTC. 
    
    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-06" -TriggerRunStartedBefore "2017-12-09"
    ```

    Można uruchomić następujące zapytanie w bazie danych usług SSIS znajdujących się na serwerze Azure SQL, aby sprawdzić, czy pakiet wykonywane. 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>Kolejne kroki
Można również monitorować potoku przy użyciu portalu Azure. Aby uzyskać instrukcje, zobacz [monitorować potoku](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).
