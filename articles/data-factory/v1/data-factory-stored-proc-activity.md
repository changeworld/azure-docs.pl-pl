---
title: Działanie procedury przechowywanej programu SQL Server
description: Dowiedz się, jak za pomocą działania procedury składowanej programu SQL Server można wywołać procedurę składowaną w bazie danych SQL Azure lub usłudze Azure SQL Data Warehouse z potoku usługi Data Factory.
services: data-factory
documentationcenter: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
manager: anandsub
robots: noindex
ms.openlocfilehash: 45aa49de51f42b26c653b15e79c865e3f5647c39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931630"
---
# <a name="sql-server-stored-procedure-activity"></a>Działanie procedury przechowywanej programu SQL Server
> [!div class="op_single_selector" title1="Działania związane z transformacją"]
> * [Aktywność gałęzi](data-factory-hive-activity.md)
> * [Aktywność świń](data-factory-pig-activity.md)
> * [Działanie mapreduce](data-factory-map-reduce.md)
> * [Aktywność strumieniowania Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Aktywność iskra](data-factory-spark.md)
> * [Działanie wykonywania wsadowego w usłudze Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Działanie aktualizowania zasobów w usłudze Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Działania procedur składowanych](data-factory-stored-proc-activity.md)
> * [Działania języka U-SQL usługi Data Lake Analytics](data-factory-usql-activity.md)
> * [Działanie niestandardowe platformy .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Azure Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [przekształcanie danych przy użyciu działania procedury składowanej w usłudze Data Factory](../transform-data-using-stored-procedure.md).

## <a name="overview"></a>Omówienie
Działania przekształcania danych w [potoku](data-factory-create-pipelines.md) fabryki danych służy do przekształcania i przetwarzania nieprzetworzonych danych do prognoz i szczegółowych informacji. Działanie procedury składowanej jest jednym z działań transformacji, które obsługuje usługa Data Factory. W tym artykule opiera się na [działaniach transformacji danych,](data-factory-data-transformation-activities.md) który przedstawia ogólne omówienie transformacji danych i obsługiwanych działań transformacji w fabryce danych.

Działanie procedury składowanej służy do wywoływania procedury składowanej w jednym z następujących magazynów danych w przedsiębiorstwie lub na maszynie wirtualnej platformy Azure(VM):

- Azure SQL Database
- Azure SQL Data Warehouse
- Baza danych programu SQL Server. Jeśli używasz programu SQL Server, zainstaluj bramę zarządzania danymi na tym samym komputerze, na którym znajduje się baza danych, lub na oddzielnym komputerze, który ma dostęp do bazy danych. Brama zarządzania danymi to składnik, który łączy źródła danych lokalnie/na maszynie Wirtualnej platformy Azure z usługami w chmurze w bezpieczny i zarządzany sposób. Szczegółowe informacje można znaleźć w artykule [Brama zarządzania danymi.](data-factory-data-management-gateway.md)

> [!IMPORTANT]
> Podczas kopiowania danych do bazy danych SQL Sql Database lub programu SQL Server można skonfigurować **sqlsink** w copy activity do wywołania procedury składowanej przy użyciu właściwości **sqlWriterStoredProcedureName.** Aby uzyskać więcej informacji, zobacz [Wywoływanie procedury składowanej z działania kopiowania](data-factory-invoke-stored-procedure-from-copy-activity.md). Aby uzyskać szczegółowe informacje na temat właściwości, zobacz następujące artykuły łącznika: [Usługa Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), SQL [Server](data-factory-sqlserver-connector.md#copy-activity-properties). Wywoływanie procedury składowanej podczas kopiowania danych do usługi Azure SQL Data Warehouse przy użyciu działania kopiowania nie jest obsługiwane. Ale można użyć działania procedury składowanej do wywołania procedury składowanej w magazynie danych SQL.
>
> Podczas kopiowania danych z bazy danych SQL Azure lub programu SQL Server lub usługi Azure SQL Data Warehouse można skonfigurować **sqlsource** w działaniu kopiowania, aby wywołać procedurę składowaną w celu odczytu danych ze źródłowej bazy danych przy użyciu właściwości **sqlReaderStoredProcedureName.** Aby uzyskać więcej informacji, zobacz następujące artykuły łącznika: [Usługa Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), SQL [Server](data-factory-sqlserver-connector.md#copy-activity-properties), usługa Azure SQL [Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

Poniższy instruktaż używa działania procedury składowanej w potoku do wywołania procedury składowanej w bazie danych SQL platformy Azure.

## <a name="walkthrough"></a>Przewodnik
### <a name="sample-table-and-stored-procedure"></a>Przykładowa tabela i procedura składowana
1. Utwórz poniższą **tabelę** w bazie danych SQL Database platformy Azure przy użyciu programu SQL Server Management Studio lub innego narzędzia, z którego użytkownik jest wygodny. Kolumna datasygnamp jest datą i godziną wygenerowania odpowiedniego identyfikatora.

    ```SQL
    CREATE TABLE dbo.sampletable
    (
        Id uniqueidentifier,
        datetimestamp nvarchar(127)
    )
    GO

    CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
    GO
    ```
    Identyfikator jest unikatowy zidentyfikowane i datetimestamp kolumna jest data i godzina, kiedy generowany jest odpowiedni identyfikator.
    
    ![Dane przykładowe](./media/data-factory-stored-proc-activity/sample-data.png)

    W tym przykładzie procedura składowana znajduje się w bazie danych SQL Azure. Jeśli procedura składowana znajduje się w usłudze Azure SQL Data Warehouse i bazy danych programu SQL Server, podejście jest podobne. W przypadku bazy danych programu SQL Server należy zainstalować [bramę zarządzania danymi](data-factory-data-management-gateway.md).
2. Utwórz następującą **procedurę składowaną,** która wstawia dane do **tabeli próbkowania**.

    ```SQL
    CREATE PROCEDURE usp_sample @DateTime nvarchar(127)
    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime)
    END
    ```

   > [!IMPORTANT]
   > **Nazwa** i **wielkość liter** parametru (DateTime w tym przykładzie) musi być zgodna z parametrem określonym w potoku/działania JSON. W definicji procedury **\@** składowanej upewnij się, że jest używany jako prefiks dla parametru.

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych
1. Zaloguj się do [portalu Azure](https://portal.azure.com/).
2. Kliknij **przycisk NOWY** w menu po lewej stronie, kliknij pozycję **Inteligencja + Analiza**i kliknij pozycję **Fabryka danych**.

    ![Nowa fabryka danych](media/data-factory-stored-proc-activity/new-data-factory.png)
3. W **bloku Nowa fabryka danych** wprowadź **SProcDF** dla Name. Nazwy usługi Azure Data Factory są **unikatowe globalnie.** Należy prefiks nazwę fabryki danych z nazwą, aby umożliwić pomyślne utworzenie fabryki.

   ![Nowa fabryka danych](media/data-factory-stored-proc-activity/new-data-factory-blade.png)
4. Wybierz swoją **subskrypcję platformy Azure**.
5. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:
   1. Kliknij **pozycję Utwórz nowy** i wprowadź nazwę grupy zasobów.
   2. Kliknij **pozycję Użyj istniejącego** i wybierz istniejącą grupę zasobów.
6. Na liście **lokalizacja** wybierz lokalizację fabryki danych.
7. Wybierz **pozycję Przypnij do pulpitu nawigacyjnego, aby** podczas następnego logowania można było wyświetlić fabrykę danych na pulpicie nawigacyjnym.
8. Kliknij przycisk **Utwórz** w bloku **Nowa fabryka danych**.
9. Zobaczysz, że fabryka danych jest tworzona na **pulpicie nawigacyjnym** witryny Azure portal. Po pomyślnym utworzeniu fabryki danych zostanie wyświetlona strona fabryki danych z zawartością fabryki danych.

   ![Strona główna usługi Data Factory](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Tworzenie połączonej usługi SQL platformy Azure
Po utworzeniu fabryki danych należy utworzyć usługę połączona sql platformy Azure, która łączy bazę danych SQL platformy Azure, która zawiera tabelę tabeli usp_sample procedury składowanej, z fabryką danych.

1. Kliknij **pozycję Autor i wdrożyć** w bloku fabryki **danych** dla **SProcDF,** aby uruchomić Edytor fabryki danych.
2. Kliknij **pozycję Nowy magazyn danych** na pasku poleceń i wybierz pozycję Azure SQL **Database**. Powinien zostać wyświetlony skrypt JSON do tworzenia usługi połączonej sql platformy Azure w edytorze.

   ![Nowy magazyn danych](media/data-factory-stored-proc-activity/new-data-store.png)
3. W skrypcie JSON wykonuj następujące zmiany:

   1. Zamień `<servername>` na nazwę serwera bazy danych SQL azure.
   2. Zamień `<databasename>` ją na bazę danych, w której utworzono tabelę i procedurę składowaną.
   3. Zamień `<username@servername>` konto użytkownika, które ma dostęp do bazy danych.
   4. Zamień `<password>` na hasło do konta użytkownika.

      ![Nowy magazyn danych](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. Aby wdrożyć usługę połączaną, kliknij przycisk **Wdrażanie** na pasku poleceń. Upewnij się, że po lewej stronie jest widoczna usługa AzureSqlLinkedService.

    ![widok drzewa z usługą połączony](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Tworzenie wyjściowego zestawu danych
Należy określić wyjściowy zestaw danych dla działania procedury składowanej, nawet jeśli procedura składowana nie generuje żadnych danych. To dlatego, że jest to wyjściowy zestaw danych, który napędza harmonogram działania (jak często działanie jest uruchamiane - co godzinę, codziennie itp.). Wyjściowy zestaw danych musi używać **połączonej usługi,** która odwołuje się do bazy danych SQL Azure lub usługi Azure SQL Data Warehouse lub bazy danych programu SQL Server, w której ma zostać uruchomiony procedura składowana. Wyjściowy zestaw danych może służyć jako sposób przekazywania wyników procedury składowanej do późniejszego przetwarzania przez inne działanie[(tworzenie łańcucha działań](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) w potoku. Jednak usługa Data Factory nie automatycznie zapisuje dane wyjściowe procedury składowanej do tego zestawu danych. Jest to procedura składowana, która zapisuje do tabeli SQL, że dane wyjściowe wskazuje na. W niektórych przypadkach wyjściowy zestaw danych może być **fikcyjnym zestawem danych** (zestawem danych, który wskazuje tabelę, która tak naprawdę nie przechowuje danych wyjściowych procedury składowanej). Ten fikcyjny zestaw danych jest używany tylko do określenia harmonogramu uruchamiania działania procedury składowanej.

1. Kliknij **... Więcej** informacji na pasku narzędzi kliknij pozycję **Nowy zestaw danych**i kliknij pozycję Azure **SQL**. **Nowy zestaw danych** na pasku poleceń i wybierz **pozycję Azure SQL**.

    ![widok drzewa z usługą połączony](media/data-factory-stored-proc-activity/new-dataset.png)
2. Skopiuj/wklej następujący skrypt JSON do edytora JSON.

    ```JSON
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "sampletable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
3. Aby wdrożyć zestaw danych, kliknij przycisk **Wdrażanie** na pasku poleceń. Upewnij się, że jest widoczny zestaw danych w widoku drzewa.

    ![widok drzewa z połączonymi usługami](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>Tworzenie potoku za pomocą działania SqlServerStoredProcedure
Teraz utwórzmy potok ze składowaną procedurą działania.

Zwróć uwagę na następujące właściwości:

- Właściwość **typu** jest ustawiona na **SqlServerStoredProcedure**.
- **StoredProcedureName** we właściwościach typu jest ustawiona na **usp_sample** (nazwa procedury składowanej).
- Sekcja **storedProcedureParameters** zawiera jeden parametr o nazwie **DateTime**. Nazwa i wielkość liter parametru w JSON muszą być zgodne z nazwą i wielkością liter parametru w definicji procedury składowanej. Jeśli potrzebujesz przekazać null dla parametru, użyj `"param1": null` składni: (wszystkie małe litery).

1. Kliknij **... Więcej** na pasku poleceń i kliknij pozycję **Nowy potok**.
2. Skopiuj/wklej następujący fragment kodu JSON:

    ```JSON
    {
        "name": "SprocActivitySamplePipeline",
        "properties": {
            "activities": [
                {
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
                        "storedProcedureName": "usp_sample",
                        "storedProcedureParameters": {
                            "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                        }
                    },
                    "outputs": [
                        {
                            "name": "sprocsampleout"
                        }
                    ],
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "SprocActivitySample"
                }
            ],
            "start": "2017-04-02T00:00:00Z",
            "end": "2017-04-02T05:00:00Z",
            "isPaused": false
        }
    }
    ```
3. Aby wdrożyć potok, kliknij przycisk **Wdrażanie** na pasku narzędzi.

### <a name="monitor-the-pipeline"></a>Monitorowanie potoku
1. Kliknij przycisk **X**, aby zamknąć bloki Edytora fabryki danych i przejść z powrotem do bloku Fabryka danych, a następnie kliknij przycisk **Diagram**.

    ![kafelek diagramu](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. W **widoku diagramu**zostanie wyświetlony przegląd potoków i zestawów danych używanych w tym samouczku.

    ![kafelek diagramu](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. W widoku diagramu kliknij dwukrotnie `sprocsampleout`zestaw danych . Plasterki są widoczne w stanie Gotowości. Powinno być pięć plasterków, ponieważ plasterek jest produkowany dla każdej godziny między godziną rozpoczęcia i czasu zakończenia z JSON.

    ![kafelek diagramu](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. Gdy plasterek jest w stanie `select * from sampletable` **Gotowości,** uruchom kwerendę względem bazy danych SQL platformy Azure, aby sprawdzić, czy dane zostały wstawione do tabeli przez procedurę składowaną.

   ![Dane wyjściowe](./media/data-factory-stored-proc-activity/output.png)

   Zobacz [Monitorowanie potoku, aby](data-factory-monitor-manage-pipelines.md) uzyskać szczegółowe informacje na temat monitorowania potoków usługi Azure Data Factory.

## <a name="specify-an-input-dataset"></a>Określanie wejściowego zestawu danych
W instruktażu działanie procedury składowanej nie ma żadnych zestawów danych wejściowych. Jeśli określisz wejściowy zestaw danych, działanie procedury składowanej nie zostanie uruchomione, dopóki nie będzie dostępny wycinek wejściowego zestawu danych (w stanie Gotowe). Zestaw danych może być zewnętrznym zestawem danych (który nie jest produkowany przez inne działanie w tym samym potoku) lub wewnętrznym zestawem danych, który jest produkowany przez działanie nadrzędne (działanie, które jest uruchamiane przed tym działaniem). Można określić wiele wejściowych zestawów danych dla działania procedury składowanej. Jeśli to zrobisz, działanie procedury składowanej jest uruchamiane tylko wtedy, gdy dostępne są wszystkie wycinki wejściowego zestawu danych (w stanie Gotowości). Wejściowy zestaw danych nie może być wykorzystany w procedurze składowanej jako parametr. Jest on używany tylko do sprawdzania zależności przed rozpoczęciem działania procedury składowanej.

## <a name="chaining-with-other-activities"></a>Łączenie się z innymi działaniami
Jeśli chcesz łańcuch działania nadrzędnego z tego działania, należy określić dane wyjściowe działania nadrzędnego jako dane wejściowe tego działania. Po wykonaniu tej czynności procedura składowana nie jest uruchamiana, dopóki działanie nadrzędne nie zostanie ukończone, a zestaw danych wyjściowych działania nadrzędnego jest dostępny (w stanie Gotowe). Można określić wyjściowe zestawy danych wielu działań nadrzędnych jako wejściowe zestawy danych działania procedury składowanej. Po wykonaniu tej czynności procedura składowana działa tylko wtedy, gdy dostępne są wszystkie wycinki zestawu danych wejściowych.

W poniższym przykładzie dane wyjściowe działania kopiowania jest: OutputDataset, który jest dane wejściowe działania procedury składowanej. W związku z tym działanie procedury składowanej nie jest uruchamiany, dopóki działanie kopiowania zostanie zakończone i fragment OutputDataset jest dostępny (w stanie Gotowe). Jeśli określisz wiele wejściowych zestawów danych, działanie procedury składowanej nie będzie uruchamiane, dopóki nie będą dostępne wszystkie wycinki zestawu danych wejściowych (w stanie Gotowe). Wejściowe zestawy danych nie mogą być używane bezpośrednio jako parametry do działania procedury składowanej.

Aby uzyskać więcej informacji na temat działań związanych z łańcuchami, zobacz [wiele działań w potoku](data-factory-create-pipelines.md#multiple-activities-in-a-pipeline)

```json
{
    "name": "ADFTutorialPipeline",
    "properties": {
        "description": "Copy data from a blob to blob",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [ { "name": "InputDataset" } ],
                "outputs": [ { "name": "OutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst"
                },
                "name": "CopyFromBlobToSQL"
            },
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "SPSproc"
                },
                "inputs": [ { "name": "OutputDataset" } ],
                "outputs": [ { "name": "SQLOutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunStoredProcedure"
            }
        ],
        "start": "2017-04-12T00:00:00Z",
        "end": "2017-04-13T00:00:00Z",
        "isPaused": false,
    }
}
```

Podobnie, aby połączyć działanie procedury magazynu z **działaniami niższego rzędu** (działania, które są uruchamiane po zakończeniu działania procedury składowanej), należy określić wyjściowy zestaw danych działania procedury składowanej jako dane wejściowe działania podrzędnego w potoku.

> [!IMPORTANT]
> Podczas kopiowania danych do bazy danych SQL Sql Database lub programu SQL Server można skonfigurować **sqlsink** w copy activity do wywołania procedury składowanej przy użyciu właściwości **sqlWriterStoredProcedureName.** Aby uzyskać więcej informacji, zobacz [Wywoływanie procedury składowanej z działania kopiowania](data-factory-invoke-stored-procedure-from-copy-activity.md). Aby uzyskać szczegółowe informacje na temat właściwości, zobacz następujące artykuły łącznika: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), SQL [Server](data-factory-sqlserver-connector.md#copy-activity-properties).
> 
> Podczas kopiowania danych z bazy danych SQL Azure lub programu SQL Server lub usługi Azure SQL Data Warehouse można skonfigurować **sqlsource** w działaniu kopiowania, aby wywołać procedurę składowaną w celu odczytu danych ze źródłowej bazy danych przy użyciu właściwości **sqlReaderStoredProcedureName.** Aby uzyskać więcej informacji, zobacz następujące artykuły łącznika: [Usługa Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), SQL [Server](data-factory-sqlserver-connector.md#copy-activity-properties), usługa Azure SQL [Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

## <a name="json-format"></a>Format JSON
Oto format JSON do definiowania działania procedury składowanej:

```JSON
{
    "name": "SQLSPROCActivity",
    "description": "description",
    "type": "SqlServerStoredProcedure",
    "inputs": [ { "name": "inputtable" } ],
    "outputs": [ { "name": "outputtable" } ],
    "typeProperties":
    {
        "storedProcedureName": "<name of the stored procedure>",
        "storedProcedureParameters":
        {
            "param1": "param1Value"
            …
        }
    }
}
```

W poniższej tabeli opisano te właściwości JSON:

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| name | Nazwa działania |Tak |
| description |Tekst opisujący, do czego jest używane działanie |Nie |
| type | Musi być ustawiona na: **SqlServerStoredProcedure** | Tak |
| Wejścia | Element opcjonalny. Jeśli określisz wejściowy zestaw danych, musi on być dostępny (w stanie "Gotowy") dla działania procedury składowanej do uruchomienia. Wejściowy zestaw danych nie może być wykorzystany w procedurze składowanej jako parametr. Jest on używany tylko do sprawdzania zależności przed rozpoczęciem działania procedury składowanej. |Nie |
| Wyjść | Należy określić wyjściowy zestaw danych dla działania procedury składowanej. Wyjściowy zestaw danych określa **harmonogram** działania procedury składowanej (co godzinę, co tydzień, co miesiąc itp.). <br/><br/>Wyjściowy zestaw danych musi używać **połączonej usługi,** która odwołuje się do bazy danych SQL Azure lub usługi Azure SQL Data Warehouse lub bazy danych programu SQL Server, w której ma zostać uruchomiony procedura składowana. <br/><br/>Wyjściowy zestaw danych może służyć jako sposób przekazywania wyników procedury składowanej do późniejszego przetwarzania przez inne działanie[(tworzenie łańcucha działań](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) w potoku. Jednak usługa Data Factory nie automatycznie zapisuje dane wyjściowe procedury składowanej do tego zestawu danych. Jest to procedura składowana, która zapisuje do tabeli SQL, że dane wyjściowe wskazuje na. <br/><br/>W niektórych przypadkach wyjściowy zestaw danych może być **fikcyjnym zestawem danych,** który jest używany tylko do określenia harmonogramu uruchamiania działania procedury składowanej. |Tak |
| przechowywaneProcedureName |Określ nazwę procedury składowanej w bazie danych Sql Azure lub w bazie danych usługi Azure SQL Data Warehouse lub SQL Server, która jest reprezentowana przez połączaną usługę używaną przez tabelę danych wyjściowych. |Tak |
| przechowywaneParametryprocedure |Określ wartości parametrów procedury składowanej. Jeśli chcesz przekazać null dla parametru, użyj składni: "param1": null (wszystkie małe litery). Zobacz poniższy przykład, aby dowiedzieć się więcej na temat korzystania z tej właściwości. |Nie |

## <a name="passing-a-static-value"></a>Przekazywanie wartości statycznej
Teraz rozważmy dodanie innej kolumny o nazwie "Scenariusz" w tabeli zawierającej wartość statyczną o nazwie "Przykład dokumentu".

![Przykładowe dane 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

**Tabeli:**

```SQL
CREATE TABLE dbo.sampletable2
(
    Id uniqueidentifier,
    datetimestamp nvarchar(127),
    scenario nvarchar(127)
)
GO

CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable2(Id);
```

**Procedura składowana:**

```SQL
CREATE PROCEDURE usp_sample2 @DateTime nvarchar(127) , @Scenario nvarchar(127)

AS

BEGIN
    INSERT INTO [sampletable2]
    VALUES (newid(), @DateTime, @Scenario)
END
```

Teraz przekaż **Scenario** parametru i wartość z działania procedury składowanej. Sekcja **typeProperties** w poprzednim przykładzie wygląda następująco:

```JSON
"typeProperties":
{
    "storedProcedureName": "usp_sample",
    "storedProcedureParameters":
    {
        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
        "Scenario": "Document sample"
    }
}
```

**Zestaw danych Data Factory:**

```JSON
{
    "name": "sprocsampleout2",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "sampletable2"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Potok fabryki danych**

```JSON
{
    "name": "SprocActivitySamplePipeline2",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample2",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
                        "Scenario": "Document sample"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout2"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
        "start": "2016-10-02T00:00:00Z",
        "end": "2016-10-02T05:00:00Z"
    }
}
```
