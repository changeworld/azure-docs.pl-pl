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
ms.date: 01/19/2018
ms.author: jingwang
ms.openlocfilehash: 309a64c47eb95ca2ca05bb82bc1c41256305823d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Wywołanie pakietów SSIS za pomocą działania procedury składowanej w fabryce danych Azure
W tym artykule opisano sposób wywołania pakietów SSIS z potoku fabryki danych Azure za pomocą działania procedury składowanej. 

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi fabryka danych, która jest ogólnie dostępna. Jeśli używasz wersji 2 usługi fabryka danych, który znajduje się w publicznej wersji zapoznawczej, zobacz [pakietów SSIS wywołać przy użyciu działania procedury składowanej w wersji 2](../how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="azure-sql-database"></a>Azure SQL Database 
Wskazówki w tym artykule używa bazy danych Azure SQL katalogiem usług SSIS. Można również użyć Azure zarządzane wystąpienia SQL (wersja zapoznawcza).

### <a name="create-an-azure-ssis-integration-runtime"></a>Tworzenie środowiska Azure SSIS Integration Runtime
Tworzenie środowiska uruchomieniowego integracji usług SSIS Azure, jeśli nie masz, wykonując instrukcje krok po kroku w [samouczek: pakiety wdrażania usług SSIS](../tutorial-create-azure-ssis-runtime-portal.md). Należy utworzyć fabryki danych w wersji 2, aby utworzyć środowiska uruchomieniowego integracji usług SSIS Azure. 

## <a name="azure-portal"></a>Azure Portal
W tej sekcji Użyj portalu Azure można utworzyć potok fabryki danych z działaniem procedury składowanej wywołująca pakietów SSIS.

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych
Pierwszym krokiem jest tworzenie fabryki danych przy użyciu portalu Azure. 

1. Przejdź do witryny [Azure Portal](https://portal.azure.com). 
2. Kliknij przycisk **Nowy** w lewym menu, kliknij pozycję **Dane + analiza**, a następnie kliknij pozycję **Data Factory**. 
   
   ![Nowy-> Fabryka danych](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. Na stronie **Nowa fabryka danych** wprowadź wartość **ADFTutorialDataFactory** w polu **Nazwa**. 
      
     ![Strona Nowa fabryka danych](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   Nazwa fabryki danych platformy Azure musi być **globalnie unikatowa**. Jeśli dla pola nazwy wystąpi poniższy błąd, zmień nazwę fabryki danych (np. twojanazwaADFTutorialDataFactory). Artykuł [Data Factory — Naming Rules (Usługa Data Factory — reguły nazewnictwa)](data-factory-naming-rules.md) zawiera reguły nazewnictwa artefaktów usługi Data Factory.

    `Data factory name ADFTutorialDataFactory is not available`
3. Wybierz **subskrypcję** Azure, w której chcesz utworzyć fabrykę danych. 
4. Dla opcji **Grupa zasobów** wykonaj jedną z następujących czynności:
     
      - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej. 
      - Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów.   
         
    Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../../azure-resource-manager/resource-group-overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
4. Wybierz **V1** dla **wersji**.
5. Na liście **lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko lokalizacje obsługiwane przez usługę Data Factory. Magazyny danych (Azure Storage, Azure SQL Database itp.) i jednostki obliczeniowe (HDInsight itp.) używane przez fabrykę danych mogą mieścić się w innych lokalizacjach.
6. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**.     
7. Kliknij przycisk **Utwórz**.
8. Na pulpicie nawigacyjnym jest widoczny następujący kafelek ze stanem: **Wdrażanie fabryki danych**. 

    ![kafelek Wdrażanie fabryki danych](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. Po zakończeniu tworzenia zostanie wyświetlona strona **Fabryka danych**, jak pokazano na poniższej ilustracji.
   
    ![Strona główna fabryki danych](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Kliknij przycisk **tworzenie i wdrażanie** Kafelek, aby uruchomić Edytor fabryki danych.

    ![Edytor fabryki danych](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-editor.png)

### <a name="create-an-azure-sql-database-linked-service"></a>Tworzenie połączonej usługi Azure SQL Database
Tworzenie połączonej usługi, aby połączyć bazy danych Azure SQL obsługującego katalogu SSIS z fabryką danych. Fabryka danych używa informacji dostępnych w tej połączonej usługi do łączenia z bazą danych usług SSIS i wykonuje procedurę składowaną do uruchamiania pakietów SSIS. 

1. W edytorze fabryki danych, kliknij polecenie **nowy magazyn danych** na pasku menu, a następnie kliknij **bazy danych SQL Azure**. 

    ![Baza danych SQL Azure -> Nowy magazyn danych](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-sql-database-linked-service-menu.png)
2. W okienku po prawej stronie wykonaj następujące czynności:

    1. Zastąp `<servername>` z nazwą serwera Azure SQL. 
    2. Zastąp `<databasename>` z **SSISDB** (nazwa bazy danych katalogu usług SSIS). 
    3. Zastąp `<username@servername>` z nazwą użytkownika, który ma dostęp do serwera Azure SQL. 
    4. Zastąp `<password>` przy użyciu hasła dla użytkownika. 
    5. Wdróż połączonej usługi, klikając **Wdróż** przycisk na pasku narzędzi. 

        ![Połączona usługa Azure SQL Database](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-definition.png)

### <a name="create-a-dummy-dataset-for-output"></a>Utwórz fikcyjny zestawu danych wyjściowych
Ten zestaw danych wyjściowych jest fikcyjny zestawu danych, który dyski harmonogram potoku. Należy zauważyć, że ustawiono częstotliwość godzinę i interwał jest ustawiona na 1. W związku z tym potoku uruchomieniu godzinę w potoku godziny rozpoczęcia i zakończenia. 

1. W okienku po lewej stronie edytor fabryki danych, kliknij przycisk **... Więcej** -> **nowy zestaw danych** -> **Azure SQL**.

    ![Więcej -> Nowy zestaw danych](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-dataset-menu.png)
2. Skopiuj poniższy fragment kodu JSON do edytora JSON w okienku po prawej stronie. 
    
    ```json
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": { },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
3. Kliknij pozycję **Wdróż** na pasku narzędzi. Ta akcja wdraża zestaw danych do usługi fabryki danych Azure. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Utworzyć potok z działania procedury składowanej 
W tym kroku możesz utworzyć potok z działania procedury składowanej. Działanie wywołuje procedury przechowywanej sp_executesql, aby uruchomić pakiet SSIS. 

1. W okienku po lewej stronie kliknij **... Więcej** i **Nowy potok**.
2. Skopiuj poniższy fragment kodu JSON do edytora JSON: 

    > [!IMPORTANT]
    > Zastąp &lt;nazwa folderu&gt;, &lt;Nazwa projektu&gt;, &lt;nazwy pakietu&gt; z nazwami folderu projektu i pakietu w katalogu usług SSIS przed zapisaniem pliku.

    ```json
    {
        "name": "MyPipeline",
        "properties": {
            "activities": [{
                "name": "SprocActivitySample",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_executesql",
                    "storedProcedureParameters": {
                        "stmt": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<folder name>', @project_name=N'<project name>', @package_name=N'<package name>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                    }
                },
                "outputs": [{
                    "name": "sprocsampleout"
                }],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }],
            "start": "2018-01-19T00:00:00Z",
            "end": "2018-01-19T05:00:00Z",
            "isPaused": false
        }
    }    
    ```
3. Kliknij pozycję **Wdróż** na pasku narzędzi. Ta akcja wdraża potoku usługi fabryka danych Azure. 

### <a name="monitor-the-pipeline-run"></a>Monitorowanie działania potoku
Harmonogram dla zestawu danych wyjściowych jest zdefiniowany jako co godzinę. Godzina zakończenia potoku jest pięć godzin od godziny rozpoczęcia. W związku z tym zostanie wyświetlony pięć testów potoku. 

1. Zamknij okna edytora, aby zobaczyć stronę główną dla fabryki danych. Kliknij przycisk **Monitor & Zarządzaj** kafelka. 

    ![Kafelek Diagram](./media/how-to-invoke-ssis-package-stored-procedure-activity/monitor-manage-tile.png)
2. Aktualizacja **godzina rozpoczęcia** i **czas zakończenia** do **2018-01/18 08:30 AM** i **2018-01/20 08:30 AM**i kliknij przycisk **Zastosuj**. Powinny pojawić się **okien działania** skojarzone z potoku uruchamiania. 

    ![Działanie systemu windows](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-windows.png)

Aby uzyskać więcej informacji na temat monitorowania potoków, zobacz [monitorowanie i zarządzanie nimi potoki fabryki danych Azure za pomocą aplikacji do zarządzania i monitorowania](data-factory-monitor-manage-app.md).

## <a name="azure-powershell"></a>Azure PowerShell
W tej sekcji umożliwia programu Azure PowerShell utworzyć potok fabryki danych z działaniem procedury składowanej wywołująca pakietów SSIS.

Zainstaluj najnowsze moduły programu Azure PowerShell, wykonując instrukcje podane w temacie [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-azurerm-ps).

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych
Poniższa procedura zawiera kroki, aby utworzyć fabryki danych. Możesz utworzyć potok z działaniem procedury składowanej w tej fabryce danych. Działania procedury składowanej wykonuje procedurę przechowywaną w bazie danych usług SSIS do uruchamiania pakietu SSIS.

1. Zdefiniuj zmienną nazwy grupy zasobów, której użyjesz później w poleceniach programu PowerShell. Skopiuj poniższy tekst polecenia do programu PowerShell, podaj nazwę [grupy zasobów platformy Azure](../../azure-resource-manager/resource-group-overview.md) w podwójnych cudzysłowach, a następnie uruchom polecenie. Na przykład: `"adfrg"`. 
   
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

5. Można utworzyć fabryki danych, uruchom następujące polecenie **AzureRmDataFactory nowy** polecenia cmdlet, za pomocą właściwości lokalizacji i grupy zasobów o nazwie zmiennej $ResGrp: 
    
    ```powershell       
    $df = New-AzureRmDataFactory -ResourceGroupName $ResourceGroupName -Name $dataFactoryName -Location "East US"
    ```

Pamiętaj o następujących kwestiach:

* Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli zostanie wyświetlony następujący błąd, zmień nazwę i spróbuj ponownie.

    ```
    The specified Data Factory name 'ADFTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Aby utworzyć wystąpienia usługi Data Factory, konto użytkownika używane do logowania się na platformie Azure musi być członkiem roli **współautora** lub **właściciela** albo **administratorem** subskrypcji platformy Azure.

### <a name="create-an-azure-sql-database-linked-service"></a>Tworzenie połączonej usługi Azure SQL Database
Tworzenie połączonej usługi, aby połączyć bazy danych Azure SQL obsługującego katalogu SSIS z fabryką danych. Fabryka danych używa informacji dostępnych w tej połączonej usługi do łączenia z bazą danych usług SSIS i wykonuje procedurę składowaną do uruchamiania pakietów SSIS. 

1. Utwórz plik JSON o nazwie **AzureSqlDatabaseLinkedService.json** w **C:\ADF\RunSSISPackage** folderu o następującej treści: 

    > [!IMPORTANT]
    > Zastąp &lt;servername&gt;, &lt;username&gt;@&lt;servername&gt; i &lt;hasło&gt; z wartościami bazy danych SQL Azure przed Zapisywanie pliku.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
        }
    ```
2. W **programu Azure PowerShell**, przełącz się do **C:\ADF\RunSSISPackage** folderu.
3. Uruchom **AzureRmDataFactoryLinkedService nowy** , aby utworzyć połączonej usługi: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzureRmDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>Tworzenie wyjściowego zestawu danych
Ten zestaw danych wyjściowych jest fikcyjny zestawu danych, który dyski harmonogram potoku. Należy zauważyć, że ustawiono częstotliwość godzinę i interwał jest ustawiona na 1. W związku z tym potoku uruchomieniu godzinę w potoku godziny rozpoczęcia i zakończenia. 

1. Utwórz plik OuputDataset.json o następującej treści: 
    
    ```json
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": { },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
2. Uruchom **AzureRmDataFactoryDataset nowy** polecenia cmdlet można utworzyć zestawu danych. 

    ```powershell
    New-AzureRmDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Utworzyć potok z działania procedury składowanej 
W tym kroku możesz utworzyć potok z działania procedury składowanej. Działanie wywołuje procedury przechowywanej sp_executesql, aby uruchomić pakiet SSIS. 

1. Utwórz plik JSON o nazwie **MyPipeline.json** w **C:\ADF\RunSSISPackage** folderu o następującej treści:

    > [!IMPORTANT]
    > Zastąp &lt;nazwa folderu&gt;, &lt;Nazwa projektu&gt;, &lt;nazwy pakietu&gt; z nazwami folderu projektu i pakietu w katalogu usług SSIS przed zapisaniem pliku.

    ```json
    {
        "name": "MyPipeline",
        "properties": {
            "activities": [{
                "name": "SprocActivitySample",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_executesql",
                    "storedProcedureParameters": {
                        "stmt": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<folder name>', @project_name=N'<project name>', @package_name=N'<package name>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                    }
                },
                "outputs": [{
                    "name": "sprocsampleout"
                }],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }],
            "start": "2017-10-01T00:00:00Z",
            "end": "2017-10-01T05:00:00Z",
            "isPaused": false
        }
    }    
    ```

2. Można utworzyć potoku: **RunSSISPackagePipeline**Uruchom **AzureRmDataFactoryPipeline nowy** polecenia cmdlet.

    ```powershell
    $DFPipeLine = New-AzureRmDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>Monitorowanie działania potoku

2. Uruchom **Get AzureRmDataFactorySlice** Aby uzyskać szczegółowe informacje o wszystkich wycinków wyjściowego zestawu danych **, czyli tabeli wyjściowej potoku.

    ```PowerShell
    Get-AzureRmDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Zauważ, że określana tutaj właściwość StartDateTime oznacza taki sam czas rozpoczęcia jak określony w potoku JSON. 
3. Uruchom polecenie **Get-AzureRmDataFactoryRun**, aby uzyskać szczegółowe informacje o uruchomieniach działania dla określonego wycinka.

    ```PowerShell
    Get-AzureRmDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    Możesz kontynuować uruchamianie tego polecenia cmdlet do momentu, gdy wycinek będzie widoczny w stanie **Gotowe** lub **Niepowodzenie**. 

    Można uruchomić następujące zapytanie w bazie danych usług SSIS znajdujących się na serwerze Azure SQL, aby sprawdzić, czy pakiet wykonywane. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać szczegółowe informacje dotyczące działania procedury składowanej, zobacz [działania procedura składowana](data-factory-stored-proc-activity.md) artykułu.

