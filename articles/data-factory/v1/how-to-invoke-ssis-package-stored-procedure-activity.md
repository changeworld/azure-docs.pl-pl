---
title: Wywoływanie pakietu SSIS przy użyciu usługi Azure Data Factory — działanie procedury składowanej
description: W tym artykule opisano sposób wywoływania pakietu usług integracyjnych programu SQL Server (SSIS) z potoku usługi Azure Data Factory przy użyciu działania procedury składowanej.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: jingwang
ms.openlocfilehash: ea86c4670a8eb6dc5e2133ed01045e8aada0f707
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438784"
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Wywoływanie pakietu SSIS przy użyciu działania procedury składowanej w usłudze Azure Data Factory
W tym artykule opisano sposób wywoływania pakietu SSIS z potoku usługi Azure Data Factory przy użyciu działania procedury składowanej. 

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Wywoływanie pakietów SSIS przy użyciu działania procedury składowanej w](../how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="azure-sql-database"></a>Azure SQL Database 
W przewodniku w tym artykule używa bazy danych SQL platformy Azure, która obsługuje wykaz SSIS. Można również użyć wystąpienia zarządzanego bazy danych SQL platformy Azure.

### <a name="create-an-azure-ssis-integration-runtime"></a>Tworzenie środowiska Azure SSIS Integration Runtime
Utwórz środowisko uruchomieniowe integracji platformy Azure-SSIS, jeśli go nie masz, postępując zgodnie z instrukcją krok po kroku w [samouczku: Wdrażanie pakietów SSIS](../tutorial-create-azure-ssis-runtime-portal.md). Nie można użyć usługi Data Factory w wersji 1 do utworzenia środowiska wykonawczego integracji platformy Azure-SSIS. 

## <a name="azure-powershell"></a>Azure PowerShell
W tej sekcji używasz programu Azure PowerShell do utworzenia potoku fabryki danych ze składowaną procedurą, która wywołuje pakiet SSIS.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Zainstaluj najnowsze moduły programu Azure PowerShell, postępując zgodnie z instrukcjami w [temacie Jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych
Poniższa procedura zawiera kroki, aby utworzyć fabrykę danych. Tworzenie potoku z działaniem procedury składowanej w tej fabryce danych. Działanie procedury składowanej wykonuje procedurę składowaną w bazie danych SSISDB, aby uruchomić pakiet SSIS.

1. Zdefiniuj zmienną nazwy grupy zasobów, której użyjesz później w poleceniach programu PowerShell. Skopiuj poniższy tekst polecenia do programu PowerShell, podaj nazwę [grupy zasobów platformy Azure](../../azure-resource-manager/management/overview.md) w podwójnych cudzysłowach, a następnie uruchom polecenie. Na przykład: `"adfrg"`. 
   
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

5. Aby utworzyć fabrykę danych, uruchom następujące polecenie cmdlet **New-AzDataFactory** przy użyciu właściwości Lokalizacja i ResourceGroupName ze zmiennej $ResGrp: 
    
    ```powershell       
    $df = New-AzDataFactory -ResourceGroupName $ResourceGroupName -Name $dataFactoryName -Location "East US"
    ```

Pamiętaj o następujących kwestiach:

* Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli zostanie wyświetlony następujący błąd, zmień nazwę i spróbuj ponownie.

    ```
    The specified Data Factory name 'ADFTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Aby utworzyć wystąpienia usługi Data Factory, konto użytkownika używane do logowania się na platformie Azure musi być członkiem roli **współautora** lub **właściciela** albo **administratorem** subskrypcji platformy Azure.

### <a name="create-an-azure-sql-database-linked-service"></a>Tworzenie połączonej usługi Azure SQL Database
Utwórz połączną usługę, aby połączyć bazę danych SQL platformy Azure, która obsługuje katalog SSIS z fabryką danych. Usługa Data Factory używa informacji w tej połączonej usłudze do łączenia się z bazą danych SSISDB i wykonuje procedurę składowaną w celu uruchomienia pakietu SSIS. 

1. Utwórz plik JSON o nazwie **AzureSqlDatabaseLinkedService.json** w folderze **C:\ADF\RunSSISPackage** z następującą zawartością: 

    > [!IMPORTANT]
    > Przed &lt;zapisaniem&gt; &lt;pliku&gt;@&lt;zamień nazwę serwera , nazwę użytkownika&gt; i &lt;hasło&gt; na wartości bazy danych SQL Azure.

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
2. W **programie Azure PowerShell**przełącz się do folderu **C:\ADF\RunSSISPackage.**
3. Uruchom polecenie cmdlet **New-AzDataFactoryLinkedService** w celu utworzenia połączonej usługi: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>Tworzenie wyjściowego zestawu danych
Ten wyjściowy zestaw danych jest fikcyjnym zestawem danych, który napędza harmonogram potoku. Należy zauważyć, że częstotliwość jest ustawiona na Godzina i interwał jest ustawiona na 1. W związku z tym potok działa raz na godzinę w czasie rozpoczęcia i zakończenia potoku. 

1. Utwórz plik OutputDataset.json z następującą zawartością: 
    
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
2. Uruchom polecenie cmdlet **Zestawu danych New-AzDataFactoryDataSet,** aby utworzyć zestaw danych. 

    ```powershell
    New-AzDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Tworzenie potoku ze składowaną procedurą 
W tym kroku utworzysz potok ze składowaną procedurą działania. Działanie wywołuje sp_executesql procedura składowana do uruchomienia pakietu SSIS. 

1. Utwórz plik JSON o nazwie **MyPipeline.json** w folderze **C:\ADF\RunSSISPackage** z następującą zawartością:

    > [!IMPORTANT]
    > Przed zapisaniem pliku&gt; &lt;należy&gt; zamienić &lt;nazwę&gt; &lt;folderu, nazwę projektu, nazwę pakietu nazwami folderów, projektów i pakietów w katalogu SSIS.

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

2. Aby utworzyć potok: **RunSSISPackagePipeline**, uruchom polecenie cmdlet **New-AzDataFactoryPipeline.**

    ```powershell
    $DFPipeLine = New-AzDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>Monitorowanie działania potoku

1. Uruchom **Get-AzDataFactorySlice,** aby uzyskać szczegółowe informacje o wszystkich wycinkach wyjściowego zestawu danych**, który jest tabelą danych wyjściowych potoku.

    ```powershell
    Get-AzDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Zauważ, że określana tutaj właściwość StartDateTime oznacza taki sam czas rozpoczęcia jak określony w potoku JSON. 
1. Uruchom **Get-AzDataFactoryRun,** aby uzyskać szczegółowe informacje o działaniu uruchamia dla określonego plasterka.

    ```powershell
    Get-AzDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    Możesz kontynuować uruchamianie tego polecenia cmdlet do momentu, gdy wycinek będzie widoczny w stanie **Gotowe** lub **Niepowodzenie**. 

    Można uruchomić następującą kwerendę względem bazy danych SSISDB na serwerze SQL platformy Azure, aby sprawdzić, czy pakiet został wykonany. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać szczegółowe informacje na temat działania procedury składowanej, zobacz artykuł [działania procedura składowana.](data-factory-stored-proc-activity.md)

