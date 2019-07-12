---
title: Wywoływanie pakietów SSIS za pomocą usługi Azure Data Factory — działania dotyczącego procedury składowanej | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak celu wywołania pakietu usług SQL Server Integration Services (SSIS) z potoku usługi Azure Data Factory przy użyciu działania dotyczącego procedury składowanej.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: jingwang
ms.openlocfilehash: 030617d3afd73c68793ca0a1d6185264c92b791f
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839901"
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Wywoływanie pakietów SSIS za pomocą działania procedury składowanej w usłudze Azure Data Factory
W tym artykule opisano jak wywołać pakietu SSIS z potoku usługi Azure Data Factory za pomocą działania procedury składowanej. 

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [wywoływanie pakietów usług SSIS za pomocą działania procedury składowanej w](../how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="azure-sql-database"></a>Azure SQL Database 
Instrukcje przedstawione w tym artykule używa usługi Azure SQL database, który hostuje katalog usług SSIS. Można również użyć bazy danych wystąpienia zarządzanego Azure SQL.

### <a name="create-an-azure-ssis-integration-runtime"></a>Tworzenie środowiska Azure SSIS Integration Runtime
Tworzenie środowiska Azure-SSIS integration runtime, jeśli nie masz, wykonując instrukcje krok po kroku instrukcji w [samouczka: Wdrażanie pakietów usług SSIS](../tutorial-create-azure-ssis-runtime-portal.md). Data Factory w wersji 1 nie można używać do tworzenia środowiska Azure-SSIS integration runtime. 

## <a name="azure-powershell"></a>Azure PowerShell
W tej sekcji użyjesz programu Azure PowerShell do utworzenia potoku usługi fabryka danych za pomocą działania procedury składowanej, która wywołuje pakietu SSIS.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Zainstaluj najnowsze moduły programu Azure PowerShell, wykonując instrukcje podane w temacie [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych
Poniższa procedura zawiera kroki, aby utworzyć fabrykę danych. Utworzysz potok z działaniem procedury składowanej w tej fabryce danych. Działanie procedury składowanej wykonuje procedurę składowaną w bazie danych SSISDB do uruchamiania pakietu SSIS.

1. Zdefiniuj zmienną nazwy grupy zasobów, której użyjesz później w poleceniach programu PowerShell. Skopiuj poniższy tekst polecenia do programu PowerShell, podaj nazwę [grupy zasobów platformy Azure](../../azure-resource-manager/resource-group-overview.md) w podwójnych cudzysłowach, a następnie uruchom polecenie. Na przykład: `"adfrg"`. 
   
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

5. Aby utworzyć fabrykę danych, uruchom następujące polecenie **New AzDataFactory** polecenia cmdlet, używając właściwości Location i ResourceGroupName ze zmiennej $ResGrp: 
    
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
Utwórz połączoną usługę służącą do połączenia z bazą danych Azure SQL hostującym wykazu usług SSIS z fabryką danych. Data Factory używa informacji w tej połączonej usługi, do łączenia z bazą danych SSISDB i wykonuje procedurę składowaną do uruchamiania pakietów SSIS. 

1. Utwórz plik JSON o nazwie **AzureSqlDatabaseLinkedService.json** w **C:\ADF\RunSSISPackage** folderu o następującej zawartości: 

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
3. Uruchom **New AzDataFactoryLinkedService** polecenia cmdlet, aby utworzyć połączoną usługę: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>Tworzenie wyjściowego zestawu danych
Ten wyjściowy zestaw danych jest fikcyjnego zestaw danych kieruje harmonogramem potoku. Należy zauważyć, że parametr frequency ma wartość Hour, interval jest ustawiona na 1. W związku z tym potok jest uruchamiany, gdy godziny w ramach potoku godziny rozpoczęcia i zakończenia. 

1. Utwórz plik OutputDataset.json o następującej zawartości: 
    
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
2. Uruchom **New AzDataFactoryDataset** polecenia cmdlet, aby utworzyć zestaw danych. 

    ```powershell
    New-AzDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Tworzenie potoku za pomocą działania procedury składowanej 
W tym kroku utworzysz potok z działaniem procedury składowanej. Działanie wywołuje procedury przechowywanej sp_executesql do uruchamiania pakietu SSIS. 

1. Utwórz plik JSON o nazwie **MyPipeline.json** w **C:\ADF\RunSSISPackage** folderu o następującej zawartości:

    > [!IMPORTANT]
    > Zastąp &lt;nazwa folderu&gt;, &lt;Nazwa projektu&gt;, &lt;nazwy pakietu&gt; nazwą folderu projektu i pakietu w katalogu usług SSIS przed zapisaniem pliku.

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

2. Aby utworzyć potok: **RunSSISPackagePipeline**Uruchom **New AzDataFactoryPipeline** polecenia cmdlet.

    ```powershell
    $DFPipeLine = New-AzDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>Monitorowanie działania potoku

1. Uruchom **Get AzDataFactorySlice** Aby uzyskać szczegółowe informacje na temat wszystkich wycinków elementu wyjściowego zestawu danych **, który stanowi tabelę wyjściową potoku.

    ```powershell
    Get-AzDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Zauważ, że określana tutaj właściwość StartDateTime oznacza taki sam czas rozpoczęcia jak określony w potoku JSON. 
1. Uruchom **Get AzDataFactoryRun** Aby uzyskać szczegółowe informacje o uruchomieniach działania dla określonego wycinka.

    ```powershell
    Get-AzDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    Możesz kontynuować uruchamianie tego polecenia cmdlet do momentu, gdy wycinek będzie widoczny w stanie **Gotowe** lub **Niepowodzenie**. 

    Można uruchomić następujące zapytanie względem bazy danych SSISDB znajdujących się na serwerze Azure SQL, aby sprawdzić, czy pakiet, który został wykonany. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać szczegółowe informacje dotyczące działania procedury składowanej, zobacz [działania procedura składowana](data-factory-stored-proc-activity.md) artykułu.

