---
title: Przyrostowe kopiowanie wielu tabel przy użyciu usługi Azure Data Factory | Microsoft Docs
description: W tym samouczku utworzysz potok usługi Azure Data Factory służący do przyrostowego kopiowania danych różnicowych z wielu tabel w lokalnej bazie danych SQL Server do bazy danych Azure SQL Database.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: yexu
ms.openlocfilehash: 244779e647c4b184b036b1a5ea77aac199be5994
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60570688"
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-an-azure-sql-database"></a>Przyrostowe ładowanie danych z wielu tabel w programie SQL Server do bazy danych Azure SQL Database
W tym samouczku utworzysz fabrykę danych Azure Data Factory z potokiem służącym do ładowania danych różnicowych z wielu tabel na lokalnym serwerze SQL Server do bazy danych Azure SQL Database.    

Ten samouczek obejmuje następujące procedury:

> [!div class="checklist"]
> * Przygotowanie źródłowych i docelowych magazynów danych
> * Tworzenie fabryki danych.
> * Utwórz własne środowisko Integration Runtime.
> * Instalowanie środowiska Integration Runtime. 
> * Tworzenie połączonych usług. 
> * Tworzenie zestawów danych źródła, ujścia i limitu.
> * Tworzenie, uruchamianie i monitorowanie potoku
> * Przejrzyj wyniki.
> * Dodawanie lub aktualizowanie danych w tabelach źródłowych.
> * Ponowne uruchamianie i monitorowanie potoku.
> * Przegląd wyników końcowych.

## <a name="overview"></a>Omówienie
Poniżej przedstawiono ważne czynności związane z tworzeniem tego rozwiązania: 

1. **Wybierz kolumnę limitu**.
    Wybierz jedną kolumnę dla każdej tabeli w magazynie danych źródłowych, która może służyć do identyfikowania nowych lub zaktualizowanych rekordów dla każdego uruchomienia. Zazwyczaj dane w tej wybranej kolumnie (na przykład last_modify_time lub ID) rosną wraz z tworzeniem i aktualizacją wierszy. Maksymalna wartość w tej kolumnie jest używana jako limit.

1. **Przygotuj magazyn danych do przechowywania wartości limitu**.   
    W tym samouczku wartość limitu jest przechowywana w bazie danych SQL.

1. **Utwórz potok z następującymi działaniami**: 
    
    a. Utwórz działanie ForEach służące do przeprowadzania iteracji po liście nazw tabel źródłowych przekazywanych jako parametr do potoku. Dla każdej tabeli źródłowej wywołuje ono następujące działania służące do wykonywania ładowania przyrostowego dla tej tabeli.

    b. Utwórz dwa działania lookup. Użyj pierwszego działania Lookup do pobrania ostatniej wartości limitu. Użyj drugiego działania Lookup do pobrania nowej wartości limitu. Te wartości limitu są przekazywane do działania Copy.

    d. Utwórz działanie Copy, które kopiuje wiersze z magazynu danych źródłowych o wartości kolumny limitu większej niż poprzednia wartość limitu i mniejszej niż nowa wartość limitu. Następnie kopiuje dane różnicowe ze źródłowego magazynu danych do usługi Azure Blob Storage jako nowy plik.

    d. Utwórz działanie StoredProcedure, które aktualizuje wartość limitu dla potoku przy następnym uruchomieniu. 

    Diagram ogólny rozwiązania wygląda następująco: 

    ![Przyrostowe ładowanie danych](media/tutorial-incremental-copy-multiple-tables-powershell/high-level-solution-diagram.png)


Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="prerequisites"></a>Wymagania wstępne
* **SQL Server**. Użyj lokalnej bazy danych SQL Server jako źródłowego magazynu danych w tym samouczku. 
* **Usługa Azure SQL Database**. Jako magazyn danych ujścia używana jest baza danych SQL. Jeśli nie masz bazy danych SQL, utwórz ją, wykonując czynności przedstawione w artykule [Tworzenie bazy danych Azure SQL Database](../sql-database/sql-database-get-started-portal.md). 

### <a name="create-source-tables-in-your-sql-server-database"></a>Tworzenie tabel źródłowych w bazie danych SQL Server

1. Otwórz program SQL Server Management Studio, a następnie nawiąż połączenie z lokalną bazą danych programu SQL Server.

1. W **Eksploratorze serwera** kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz pozycję **Nowe zapytanie**.

1. Uruchom następujące polecenie SQL względem bazy danych w celu utworzenia tabel o nazwach `customer_table` i `project_table`:

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
        
    INSERT INTO customer_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'John','9/1/2017 12:56:00 AM'),
    (2, 'Mike','9/2/2017 5:23:00 AM'),
    (3, 'Alice','9/3/2017 2:36:00 AM'),
    (4, 'Andy','9/4/2017 3:21:00 AM'),
    (5, 'Anny','9/5/2017 8:06:00 AM');
    
    INSERT INTO project_table
    (Project, Creationtime)
    VALUES
    ('project1','1/1/2015 0:00:00 AM'),
    ('project2','2/2/2016 1:23:00 AM'),
    ('project3','3/4/2017 5:16:00 AM');
    
    ```

### <a name="create-destination-tables-in-your-azure-sql-database"></a>Tworzenie tabel docelowych w bazie danych Azure SQL Database
1. Otwórz program SQL Server Management Studio i połącz się z bazą danych programu SQL Server.

1. W **Eksploratorze serwera** kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz pozycję **Nowe zapytanie**.

1. Uruchom następujące polecenie SQL względem bazy danych SQL w celu utworzenia tabel o nazwach `customer_table` i `project_table`:  
    
    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );

    ```

### <a name="create-another-table-in-the-azure-sql-database-to-store-the-high-watermark-value"></a>Tworzenie innej tabeli w bazie danych Azure SQL Database do przechowywania wartości górnego limitu
1. Uruchom następujące polecenie SQL względem bazy danych SQL, aby utworzyć tabelę o nazwie `watermarktable` w celu przechowywania wartości limitu: 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
1. Wstaw początkowe wartości limitu dla obu tabel źródłowych w tabeli wartości limitu.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-the-azure-sql-database"></a>Tworzenie procedury składowanej w bazie danych Azure SQL Database 

Uruchom następujące polecenie, aby utworzyć procedurę składowaną w bazie danych SQL. Ta procedura składowana służy do aktualizowania wartość limitu po każdym uruchomieniu potoku. 

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-the-azure-sql-database"></a>Tworzenie typów danych i dodatkowych procedur składowanych w bazie danych Azure SQL Database
Uruchom następujące zapytanie, aby utworzyć dwie procedury składowane i dwa typy danych w bazie danych SQL. Służą one do scalania danych z tabel źródłowych w tabelach docelowych. 

Aby ułatwić podróży w zaczynać, firma Microsoft bezpośrednio korzystać z tych procedur składowanych, przekazując dane różnicowe w za pośrednictwem zmiennej tabeli, a następnie scalić je do docelowego magazynu. Należy zachować ostrożność, jego ma być przechowywany w zmiennej tabeli nie jest oczekiwane "large" Liczba zmian wierszy (ponad 100).  

Jeśli musisz scalić dużą liczbę wierszy delta Magazyn docelowy, sugerujemy przy użyciu działania kopiowania do skopiowania danych różnicowych do tabeli tymczasowej "tymczasową" w miejscu docelowym przechowywania pierwszy, a następnie kompilowany procedury przechowywanej bez użycia vari tabeli można scalić je z tabeli "tymczasową" do tabeli "final". 


```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE usp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
AS

BEGIN
  MERGE customer_table AS target
  USING @customer_table AS source
  ON (target.PersonID = source.PersonID)
  WHEN MATCHED THEN
      UPDATE SET Name = source.Name,LastModifytime = source.LastModifytime
  WHEN NOT MATCHED THEN
      INSERT (PersonID, Name, LastModifytime)
      VALUES (source.PersonID, source.Name, source.LastModifytime);
END

GO

CREATE TYPE DataTypeforProjectTable AS TABLE(
    Project varchar(255),
    Creationtime datetime
);

GO

CREATE PROCEDURE usp_upsert_project_table @project_table DataTypeforProjectTable READONLY
AS

BEGIN
  MERGE project_table AS target
  USING @project_table AS source
  ON (target.Project = source.Project)
  WHEN MATCHED THEN
      UPDATE SET Creationtime = source.Creationtime
  WHEN NOT MATCHED THEN
      INSERT (Project, Creationtime)
      VALUES (source.Project, source.Creationtime);
END

```

### <a name="azure-powershell"></a>Azure PowerShell
Zainstaluj najnowsze moduły programu Azure PowerShell, wykonując instrukcje podane w temacie [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych
1. Zdefiniuj zmienną nazwy grupy zasobów, której użyjesz później w poleceniach programu PowerShell. Skopiuj poniższy tekst polecenia do programu PowerShell, podaj nazwę [grupy zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) w podwójnych cudzysłowach, a następnie uruchom polecenie. Może to być na przykład `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Jeśli grupa zasobów już istnieje, jej zastąpienie może być niewskazane. Przypisz inną wartość do zmiennej `$resourceGroupName` i ponownie uruchom polecenie.

1. Zdefiniuj zmienną lokalizacji fabryki danych. 

    ```powershell
    $location = "East US"
    ```
1. Aby utworzyć grupę zasobów platformy Azure, uruchom następujące polecenie: 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 
    Jeśli grupa zasobów już istnieje, jej zastąpienie może być niewskazane. Przypisz inną wartość do zmiennej `$resourceGroupName` i ponownie uruchom polecenie.

1. Zdefiniuj zmienną nazwy fabryki danych. 

    > [!IMPORTANT]
    >  Zaktualizuj nazwę fabryki danych, aby była unikatowa w skali globalnej. Może to być na przykład ADFIncMultiCopyTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFIncMultiCopyTutorialFactory";
    ```
1. Aby utworzyć fabrykę danych, uruchom następujące polecenie cmdlet **Set-AzureRmDataFactoryV2**: 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

Pamiętaj o następujących kwestiach:

* Nazwa fabryki danych musi być globalnie unikatowa. Jeśli zostanie wyświetlony następujący błąd, zmień nazwę i spróbuj ponownie:

    ```
    The specified Data Factory name 'ADFIncMultiCopyTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Aby utworzyć wystąpienia usługi Data Factory, konto użytkownika używane do logowania się na platformie Azure musi być członkiem roli współautora lub właściciela albo administratorem subskrypcji platformy Azure.
* Aby uzyskać listę regionów platformy Azure, w których obecnie jest dostępna usługa Data Factory, wybierz dane regiony na poniższej stronie, a następnie rozwiń węzeł **Analiza**, aby zlokalizować pozycję **Data Factory**: [Dostępność produktów według regionów](https://azure.microsoft.com/global-infrastructure/services/). Magazyny danych (Azure Storage, Azure SQL Database itp.) i jednostki obliczeniowe (Azure HDInsight itp.) używane przez fabrykę danych mogą mieścić się w innych regionach.

[!INCLUDE [data-factory-create-install-integration-runtime](../../includes/data-factory-create-install-integration-runtime.md)]



## <a name="create-linked-services"></a>Tworzenie połączonych usług
Połączone usługi tworzy się w fabryce danych w celu połączenia magazynów danych i usług obliczeniowych z fabryką danych. W tej sekcji utworzysz usługi połączone z używaną, lokalną bazą danych SQL Server i bazą danych SQL. 

### <a name="create-the-sql-server-linked-service"></a>Tworzenie usługi połączonej z serwerem SQL Server
W tym kroku połączysz lokalną bazę danych programu SQL Server z fabryką danych.

1. W folderze C:\ADFTutorials\IncCopyMultiTableTutorial utwórz plik JSON o nazwie SqlServerLinkedService.json z następującą zawartością. Wybierz właściwą sekcję na podstawie uwierzytelniania używanego do nawiązywania połączenia z programem SQL Server. Utwórz foldery lokalne, jeśli jeszcze nie istnieją. 

    > [!IMPORTANT]
    > Wybierz właściwą sekcję na podstawie uwierzytelniania używanego do nawiązywania połączenia z programem SQL Server.

    Jeśli używasz uwierzytelniania SQL, skopiuj następującą definicję JSON:

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }
   ```    
    Jeśli używasz uwierzytelniania systemu Windows, skopiuj następującą definicję JSON:

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<server>;Database=<database>;Integrated Security=True"
                },
                "userName": "<user> or <domain>\\<user>",
                "password": {
                    "type": "SecureString",
                    "value": "<password>"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }    
    ```
    > [!IMPORTANT]
    > - Wybierz właściwą sekcję na podstawie uwierzytelniania używanego do nawiązywania połączenia z programem SQL Server.
    > - Zastąp wartość &lt;integration runtime name> nazwą Twojego środowiska Integration Runtime.
    > - Przed zapisaniem pliku zastąp wartości &lt;servername>, &lt;databasename>, &lt;username> i &lt;password> wartościami odpowiednimi dla bazy danych programu SQL Server.
    > - Jeśli musisz użyć znaku ukośnika (`\`) w nazwie konta użytkownika lub nazwie serwera, użyj znaku ucieczki (`\`). Może to być na przykład `mydomain\\myuser`.

1. W programie PowerShell przełącz się do folderu C:\ADFTutorials\IncCopyMultiTableTutorial.

1. Uruchom polecenie cmdlet **Set-AzureRmDataFactoryV2LinkedService**, aby utworzyć połączoną usługę AzureStorageLinkedService. W poniższym przykładzie przekazujesz wartości dla parametrów *ResourceGroupName* i *DataFactoryName*: 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerLinkedService" -File ".\SqlServerLinkedService.json"
    ```

    Oto przykładowe dane wyjściowe:

    ```json
    LinkedServiceName : SqlServerLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerLinkedService
    ```

### <a name="create-the-sql-database-linked-service"></a>Tworzenie połączonej usługi bazy danych SQL
1. W folderze C:\ADFTutorials\IncCopyMultiTableTutorial utwórz plik JSON o nazwie AzureSQLDatabaseLinkedService.json z następującą zawartością. Jeśli folder ADF jeszcze nie istnieje, utwórz go. Przed zapisaniem pliku zamień parametry &lt;server&gt;, &lt;database&gt;, &lt;user id&gt; i &lt;password&gt; na nazwę swojej bazy danych programu SQL Server, nazwę bazy danych, identyfikator użytkownika i hasło. 

    ```json
    {
        "name": "AzureSQLDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "value": "Server = tcp:<server>.database.windows.net,1433;Initial Catalog=<database name>; Persist Security Info=False; User ID=<user name>; Password=<password>; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
1. W programie PowerShell uruchom polecenie cmdlet **Set-AzureRmDataFactoryV2LinkedService**, aby utworzyć połączoną usługę AzureSQLDatabaseLinkedService. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Oto przykładowe dane wyjściowe:

    ```json
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

## <a name="create-datasets"></a>Tworzenie zestawów danych
W tym kroku utworzysz zestawy danych reprezentujące źródło danych, docelową lokalizację danych i lokalizację, w której będzie przechowywana wartość limitu.

### <a name="create-a-source-dataset"></a>Tworzenie zestawu danych źródłowych

1. Utwórz plik JSON o nazwie SourceDataset.json w tym samym folderze, o następującej zawartości: 

    ```json
    {
        "name": "SourceDataset",
        "properties": {
            "type": "SqlServerTable",
            "typeProperties": {
                "tableName": "dummyName"
            },
            "linkedServiceName": {
                "referenceName": "SqlServerLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
   
    ```

    Nazwa tabeli jest nazwą fikcyjną. Działanie Copy w potoku korzysta z zapytania SQL do załadowania danych, a nie całej tabeli.

1. Uruchom polecenie cmdlet **Set-AzureRmDataFactoryV2Dataset**, aby utworzyć zestaw danych SourceDataset.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Oto przykładowe dane wyjściowe polecenia cmdlet:
    
    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Tworzenie ujścia zestawu danych

1. Utwórz w tym samym folderze plik JSON SinkDataset.json o następującej zawartości. Element tableName jest dynamicznie ustawiany przez potok w czasie wykonywania. Działanie ForEach w potoku przeprowadza iterację po liście nazw i przekazuje nazwę tabeli do tego zestawu danych w każdej iteracji. 

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": {
                    "value": "@{dataset().SinkTableName}",
                    "type": "Expression"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            },
            "parameters": {
                "SinkTableName": {
                    "type": "String"
                }
            }
        }
    }
    ```

1. Uruchom polecenie cmdlet **Set-AzureRmDataFactoryV2Dataset**, aby utworzyć zestaw danych SinkDataset.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Oto przykładowe dane wyjściowe polecenia cmdlet:
    
    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-a-watermark"></a>Tworzenie zestawu danych dla limitu
W tym kroku utworzysz zestaw danych do przechowywania wartości górnego limitu. 

1. Utwórz plik JSON o nazwie WatermarkDataset.json w tym samym folderze, o następującej zawartości: 

    ```json
    {
        "name": " WatermarkDataset ",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "watermarktable"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }    
    ```
1. Uruchom polecenie cmdlet **Set-AzureRmDataFactoryV2Dataset**, aby utworzyć zestaw danych WatermarkDataset.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "WatermarkDataset" -File ".\WatermarkDataset.json"
    ```

    Oto przykładowe dane wyjściowe polecenia cmdlet:
    
    ```json
    DatasetName       : WatermarkDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : <data factory name>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset    
    ```

## <a name="create-a-pipeline"></a>Tworzenie potoku
Potok przyjmuje listę nazw tabel jako parametr. Działanie ForEach służy do przeprowadzania iteracji po liście nazw tabel i wykonywania następujących operacji: 

1. Użyj działania Lookup do pobrania starej wartość limitu (wartości początkowej lub wartości użytej w ostatniej iteracji).

1. Użyj działania Lookup do pobrania nowej wartości limitu (maksymalnej wartości kolumny limitu w tabeli źródłowej).

1. Użyj działania Copy do skopiowania danych między tymi dwiema wartościami limitu ze źródłowej bazy danych do docelowej bazy danych.

1. Użyj działania StoredProcedure do zaktualizowania starej wartości limitu, która zostanie użyta w pierwszym kroku następnej iteracji. 

### <a name="create-the-pipeline"></a>Tworzenie potoku
1. Utwórz w tym samym folderze plik JSON IncrementalCopyPipeline.json o następującej zawartości: 

    ```json
    {
        "name": "IncrementalCopyPipeline",
        "properties": {
            "activities": [{
    
                "name": "IterateSQLTables",
                "type": "ForEach",
                "typeProperties": {
                    "isSequential": "false",
                    "items": {
                        "value": "@pipeline().parameters.tableList",
                        "type": "Expression"
                    },
    
                    "activities": [
                        {
                            "name": "LookupOldWaterMarkActivity",
                            "type": "Lookup",
                            "typeProperties": {
                                "source": {
                                    "type": "SqlSource",
                                    "sqlReaderQuery": "select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'"
                                },
    
                                "dataset": {
                                    "referenceName": "WatermarkDataset",
                                    "type": "DatasetReference"
                                }
                            }
                        },
                        {
                            "name": "LookupNewWaterMarkActivity",
                            "type": "Lookup",
                            "typeProperties": {
                                "source": {
                                    "type": "SqlSource",
                                    "sqlReaderQuery": "select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}"
                                },
    
                                "dataset": {
                                    "referenceName": "SourceDataset",
                                    "type": "DatasetReference"
                                }
                            }
                        },
    
                        {
                            "name": "IncrementalCopyActivity",
                            "type": "Copy",
                            "typeProperties": {
                                "source": {
                                    "type": "SqlSource",
                                    "sqlReaderQuery": "select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'"
                                },
                                "sink": {
                                    "type": "SqlSink",
                                    "SqlWriterTableType": "@{item().TableType}",
                                    "SqlWriterStoredProcedureName": "@{item().StoredProcedureNameForMergeOperation}"
                                }
                            },
                            "dependsOn": [{
                                    "activity": "LookupNewWaterMarkActivity",
                                    "dependencyConditions": [
                                        "Succeeded"
                                    ]
                                },
                                {
                                    "activity": "LookupOldWaterMarkActivity",
                                    "dependencyConditions": [
                                        "Succeeded"
                                    ]
                                }
                            ],
    
                            "inputs": [{
                                "referenceName": "SourceDataset",
                                "type": "DatasetReference"
                            }],
                            "outputs": [{
                                "referenceName": "SinkDataset",
                                "type": "DatasetReference",
                                "parameters": {
                                    "SinkTableName": "@{item().TableType}"
                                }
                            }]
                        },
    
                        {
                            "name": "StoredProceduretoWriteWatermarkActivity",
                            "type": "SqlServerStoredProcedure",
                            "typeProperties": {
    
                                "storedProcedureName": "usp_write_watermark",
                                "storedProcedureParameters": {
                                    "LastModifiedtime": {
                                        "value": "@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}",
                                        "type": "datetime"
                                    },
                                    "TableName": {
                                        "value": "@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}",
                                        "type": "String"
                                    }
                                }
                            },
    
                            "linkedServiceName": {
                                "referenceName": "AzureSQLDatabaseLinkedService",
                                "type": "LinkedServiceReference"
                            },
    
                            "dependsOn": [{
                                "activity": "IncrementalCopyActivity",
                                "dependencyConditions": [
                                    "Succeeded"
                                ]
                            }]
                        }
    
                    ]
    
                }
            }],
    
            "parameters": {
                "tableList": {
                    "type": "Object"
                }
            }
        }
    }
    ```
1. Uruchom polecenie cmdlet **Set-AzureRmDataFactoryV2Pipeline**, aby utworzyć potok IncrementalCopyPipeline.
    
   ```powershell
   Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ``` 

   Oto przykładowe dane wyjściowe: 

   ```json
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```
 
## <a name="run-the-pipeline"></a>Uruchamianie potoku

1. Utwórz w tym samym folderze plik parametrów Parameters.json o następującej zawartości:

    ```json
    {
        "tableList": 
        [
            {
                "TABLE_NAME": "customer_table",
                "WaterMark_Column": "LastModifytime",
                "TableType": "DataTypeforCustomerTable",
                "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
            },
            {
                "TABLE_NAME": "project_table",
                "WaterMark_Column": "Creationtime",
                "TableType": "DataTypeforProjectTable",
                "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
            }
        ]
    }
    ```
1. Uruchom potok IncrementalCopyPipeline za pomocą polecenia cmdlet **Invoke-AzureRmDataFactoryV2Pipeline**. Zastąp symbole zastępcze własną nazwą grupy zasobów i fabryki danych.

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"        
    ``` 

## <a name="monitor-the-pipeline"></a>Monitorowanie potoku

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **Wszystkie usługi**, przeprowadź wyszukiwanie za pomocą słowa kluczowego *Fabryki danych*, a następnie wybierz pozycję **Fabryki danych**. 

    ![Menu fabryk danych](media/tutorial-incremental-copy-multiple-tables-powershell/monitor-data-factories-menu-1.png)

1. Wyszukaj używaną fabrykę danych na liście fabryk danych, a następnie wybierz ją, aby otworzyć stronę **Fabryka danych**. 

    ![Wyszukiwanie fabryki danych](media/tutorial-incremental-copy-multiple-tables-powershell/monitor-search-data-factory-2.png)

1. Na stronie **Fabryka danych** wybierz kafelek **Monitorowanie i zarządzanie**. 

    ![Kafelek Monitorowanie i zarządzanie](media/tutorial-incremental-copy-multiple-tables-powershell/monitor-monitor-manage-tile-3.png)

1. Na osobnej karcie zostanie otwarta **aplikacja Integracja danych**. Wyświetlone zostaną wszystkie uruchomienia potoków wraz z ich stanami. Zwróć uwagę, że w poniższym przykładzie stan uruchomienia potoku to **Powodzenie**. Wybierz link w kolumnie **Parametry**, aby sprawdzić parametry przekazywane do potoku. Jeśli wystąpił błąd, w kolumnie **Błąd** zostanie wyświetlony link. Wybierz link w kolumnie **Akcje**. 

    ![Uruchomienia potoków](media/tutorial-incremental-copy-multiple-tables-powershell/monitor-pipeline-runs-4.png)    
1. Po wybraniu linków w kolumnie **Akcje** zostanie wyświetlona następująca strona, na której będą się znajdować wszystkie uruchomienia działań dla potoku: 

    ![Uruchomienia działania](media/tutorial-incremental-copy-multiple-tables-powershell/monitor-activity-runs-5.png)

1. Aby powrócić do widoku **Uruchomienia potoków**, wybierz pozycję **Potoki** w sposób przedstawiony na ilustracji. 

## <a name="review-the-results"></a>Sprawdzanie wyników
W programu SQL Server Management Studio uruchom następujące zapytania względem docelowej bazy danych Azure SQL Database, aby sprawdzić, czy dane zostały skopiowane z tabel źródłowych do tabel docelowych: 

**Zapytanie** 
```sql
select * from customer_table
```

**Dane wyjściowe**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           Alice   2017-09-03 02:36:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**Zapytanie**

```sql
select * from project_table
```

**Dane wyjściowe**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
```

**Zapytanie**

```sql
select * from watermarktable
```

**Dane wyjściowe**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

Należy zauważyć, że wartości limitu dla obu tabel zostały zaktualizowane. 

## <a name="add-more-data-to-the-source-tables"></a>Dodawanie większej ilości danych do tabel źródłowych

Uruchom następujące zapytanie względem źródłowej bazy danych programu SQL Server, aby zaktualizować istniejący wiersz w tabeli customer_table. Wstaw nowy wiersz do tabeli project_table. 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>Ponowne uruchamianie potoku

1. Teraz ponownie uruchom proces, wykonując następujące polecenie programu PowerShell:

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupname -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"
    ```
1. Monitoruj uruchomienia potoków, postępując zgodnie z instrukcjami w sekcji [Monitorowanie potoku](#monitor-the-pipeline). Ponieważ stan potoku ma wartość **W toku**, w kolumnie **Akcje** zostanie wyświetlony inny link służący do anulowania uruchomienia potoku. 

    ![Uruchomienia potoków W toku](media/tutorial-incremental-copy-multiple-tables-powershell/monitor-pipeline-runs-6.png)

1. Wybierz pozycję **Odśwież**, aby odświeżać listę do momentu, aż uruchomienie potoku zakończy się pomyślnie. 

    ![Odświeżanie uruchomień potoków](media/tutorial-incremental-copy-multiple-tables-powershell/monitor-pipeline-runs-succeded-7.png)

1. Opcjonalnie wybierz link **Wyświetl uruchomienia działań** w kolumnie **Akcje**, aby wyświetlić wszystkie uruchomienia działań skojarzone z tym uruchomieniem potoku. 

## <a name="review-the-final-results"></a>Przegląd wyników końcowych
W programie SQL Server Management Studio uruchom następujące zapytania względem docelowej bazy danych, aby sprawdzić, czy zaktualizowane lub nowe dane zostały skopiowane z tabel źródłowych do tabel docelowych. 

**Zapytanie** 
```sql
select * from customer_table
```

**Dane wyjściowe**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           NewName 2017-09-08 00:00:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

Zwróć uwagę na nowe wartości właściwości **Name** i **LastModifytime** dla identyfikatora **PersonID** numeru 3. 

**Zapytanie**

```sql
select * from project_table
```

**Dane wyjściowe**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

Zwróć uwagę, że do tabeli project_table dodano pozycję **NewProject**. 

**Zapytanie**

```sql
select * from watermarktable
```

**Dane wyjściowe**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

Należy zauważyć, że wartości limitu dla obu tabel zostały zaktualizowane.
     
## <a name="next-steps"></a>Kolejne kroki
W ramach tego samouczka wykonano następujące procedury: 

> [!div class="checklist"]
> * Przygotowanie źródłowych i docelowych magazynów danych
> * Tworzenie fabryki danych.
> * Tworzenie własnego środowiska Integration Runtime.
> * Instalowanie środowiska Integration Runtime.
> * Tworzenie połączonych usług. 
> * Tworzenie zestawów danych źródła, ujścia i limitu.
> * Tworzenie, uruchamianie i monitorowanie potoku
> * Przejrzyj wyniki.
> * Dodawanie lub aktualizowanie danych w tabelach źródłowych.
> * Ponowne uruchamianie i monitorowanie potoku.
> * Przegląd wyników końcowych.

Przejdź do następującego samouczka, aby dowiedzieć się więcej o przekształcaniu danych za pomocą klastra Spark na platformie Azure:

> [!div class="nextstepaction"]
>[Przyrostowe ładowanie danych z bazy danych Azure SQL Database do magazynu Azure Blob Storage z użyciem technologii Change Tracking](tutorial-incremental-copy-change-tracking-feature-powershell.md)


