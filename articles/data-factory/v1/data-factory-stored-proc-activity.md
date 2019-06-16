---
title: Działanie procedury składowanej programu SQL Server
description: Dowiedz się, jak działania dotyczącego procedury składowanej systemu SQL Server umożliwia wywoływanie procedury przechowywanej w usłudze Azure SQL Database lub Azure SQL Data Warehouse z potoku usługi fabryka danych.
services: data-factory
documentationcenter: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
manager: craigg
robots: noindex
ms.openlocfilehash: 77842b60108629168f423f25eb03b01079cf55e5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61256022"
---
# <a name="sql-server-stored-procedure-activity"></a>Działanie procedury składowanej programu SQL Server
> [!div class="op_single_selector" title1="Działania przekształcania"]
> * [Działanie technologii hive](data-factory-hive-activity.md)
> * [Działania technologii pig](data-factory-pig-activity.md)
> * [Działania technologii MapReduce](data-factory-map-reduce.md)
> * [Działania przesyłania strumieniowego usługi Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Działania platformy Spark](data-factory-spark.md)
> * [Działanie wykonywania wsadowego w usłudze Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Działania aktualizowania zasobów w usłudze Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Działania procedur składowanych](data-factory-stored-proc-activity.md)
> * [Działania języka U-SQL usługi Data Lake Analytics](data-factory-usql-activity.md)
> * [Niestandardowe działanie platformy .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Azure Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [przekształcania danych za pomocą działania procedury składowanej w usłudze Data Factory](../transform-data-using-stored-procedure.md).

## <a name="overview"></a>Omówienie
Użyj działania przekształcania danych w usłudze Data Factory [potoku](data-factory-create-pipelines.md) do przekształcania i Przetwarzaj danych pierwotnych w prognozy i szczegółowych informacji. Działania dotyczącego procedury składowanej jest jednym z działania przekształcania, które obsługuje usługi Data Factory. W tym artykule opiera się na [działania przekształcania danych](data-factory-data-transformation-activities.md) artykułu, który przedstawia ogólny przegląd działań przekształcania obsługiwanych w usłudze Data Factory i przekształcania danych.

Działania dotyczącego procedury składowanej umożliwia wywoływanie procedury składowanej w jednym z następujących magazynów danych w przedsiębiorstwie lub na maszynie wirtualnej platformy Azure (VM):

- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server Database. Jeśli używasz programu SQL Server, należy zainstalować bramę zarządzania danymi na tym samym komputerze, który jest hostem bazy danych lub na osobnym komputerze, który ma dostęp do bazy danych. Brama zarządzania danymi jest składnikiem, który nawiązuje połączenie danych źródła w lokalnych/na maszynie Wirtualnej platformy Azure z usługami w chmurze w sposób bezpieczny i zarządzane. Zobacz [bramy zarządzania danymi](data-factory-data-management-gateway.md) artykuł, aby uzyskać szczegółowe informacje.

> [!IMPORTANT]
> Podczas kopiowania danych do usługi Azure SQL Database lub SQL Server, można skonfigurować **SqlSink** w działaniu kopiowania, aby wywołać procedurę składowaną przy użyciu **sqlWriterStoredProcedureName** właściwości. Aby uzyskać więcej informacji, zobacz [wywołaj procedurę składowaną z działaniem kopiowania](data-factory-invoke-stored-procedure-from-copy-activity.md). Aby uzyskać szczegółowe informacje o właściwości zobacz następujące artykuły łącznika: [Usługa Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [programu SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties). Wywoływanie procedury składowanej podczas kopiowania danych do usługi Azure SQL Data Warehouse za pomocą działania kopiowania nie jest obsługiwane. Jednak działanie procedury składowanej umożliwia wywoływanie procedury przechowywanej w usłudze SQL Data Warehouse.
>
> Podczas kopiowania danych z usługi Azure SQL Database lub SQL Server lub usługi Azure SQL Data Warehouse, możesz skonfigurować **SqlSource** w działaniu kopiowania, aby wywołać procedurę przechowywaną, aby odczytać danych ze źródłowej bazy danych za pomocą  **sqlReaderStoredProcedureName** właściwości. Aby uzyskać więcej informacji zobacz następujące artykuły łącznika: [Usługa Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [programu SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

Następujące Instruktaż używa działania dotyczącego procedury składowanej w potoku, aby wywołać procedurę składowaną w bazie danych Azure SQL.

## <a name="walkthrough"></a>Przewodnik
### <a name="sample-table-and-stored-procedure"></a>Przykładowa tabela i procedury składowanej
1. Utwórz następującą **tabeli** w usłudze Azure SQL Database przy użyciu programu SQL Server Management Studio lub innego narzędzia potrafisz. Kolumna znacznikdatygodziny jest data i godzina wygenerowania odpowiedni identyfikator.

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
    Identyfikator jest unikatowy zidentyfikować, a kolumna znacznikdatygodziny jest data i godzina wygenerowania odpowiedni identyfikator.
    
    ![Dane przykładowe](./media/data-factory-stored-proc-activity/sample-data.png)

    W tym przykładzie procedura składowana jest usługi Azure SQL Database. W przypadku procedury składowanej w usłudze Azure SQL Data Warehouse i bazy danych SQL Server, to podejście jest podobny. W przypadku bazy danych programu SQL Server należy zainstalować [bramy zarządzania danymi](data-factory-data-management-gateway.md).
2. Utwórz następującą **procedury składowanej** który wstawia dane w celu **sampletable**.

    ```SQL
    CREATE PROCEDURE usp_sample @DateTime nvarchar(127)
    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime)
    END
    ```

   > [!IMPORTANT]
   > **Nazwa** i **wielkość liter w wyrazie** parametru (Data/Godzina w tym przykładzie) musi być zgodna z parametrów określonych w kodzie JSON potoku/działania. W definicji procedury składowanej, upewnij się, że **\@** służy jako prefiks dla parametru.

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
2. Kliknij przycisk **NEW** w menu po lewej stronie, kliknij polecenie **rozwiązania inteligentne + analiza**i kliknij przycisk **usługi Data Factory**.

    ![Nowa fabryka danych](media/data-factory-stored-proc-activity/new-data-factory.png)
3. W **nowa fabryka danych** bloku wprowadź **SProcDF** dla nazwy. Usługa Azure Data Factory nazwy są **globalnie unikatowa**. Musisz poprzedź nazwę fabryki danych z Twoją nazwą, aby umożliwić pomyślne utworzenie fabryki.

   ![Nowa fabryka danych](media/data-factory-stored-proc-activity/new-data-factory-blade.png)
4. Wybierz swoją **subskrypcję** platformy Azure.
5. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:
   1. Kliknij przycisk **Utwórz nową** i wprowadź nazwę grupy zasobów.
   2. Kliknij przycisk **Użyj istniejącej** i wybierz istniejącą grupę zasobów.
6. Na liście **lokalizacja** wybierz lokalizację fabryki danych.
7. Wybierz **Przypnij do pulpitu nawigacyjnego** tak, aby widoczne fabryki danych na pulpicie nawigacyjnym przy następnym zalogowaniu.
8. Kliknij przycisk **Utwórz** w bloku **Nowa fabryka danych**.
9. Zostanie wyświetlony w fabryce danych tworzony w **pulpit nawigacyjny** w witrynie Azure Portal. Po pomyślnym utworzeniu fabryki danych zostanie wyświetlona strona fabryki danych z zawartością fabryki danych.

   ![Strona główna fabryki danych](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Tworzenie połączonej usługi Azure SQL
Po utworzeniu fabryki danych, tworzenie SQL platformy Azure połączonej usługi, która łączy usługi Azure SQL database, który zawiera tabelę sampletable i usp_sample procedurą składowaną, z fabryką danych.

1. Kliknij przycisk **autora i wdrażanie** na **usługi Data Factory** bloku **SProcDF** można uruchomić edytora fabryki danych.
2. Kliknij przycisk **nowy magazyn danych** na polecenia paska, a następnie wybierz **usługi Azure SQL Database**. Powinien zostać wyświetlony skrypt JSON do tworzenia połączonej usługi Azure SQL w edytorze.

   ![Nowy magazyn danych](media/data-factory-stored-proc-activity/new-data-store.png)
3. W skrypcie JSON należy wprowadzić następujące zmiany:

   1. Zastąp `<servername>` nazwą serwera usługi Azure SQL Database.
   2. Zastąp `<databasename>` z bazą danych, w którym został utworzony w tabeli i procedury składowanej.
   3. Zastąp `<username@servername>` przy użyciu konta użytkownika, który ma dostęp do bazy danych.
   4. Zastąp `<password>` przy użyciu hasła dla konta użytkownika.

      ![Nowy magazyn danych](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. Aby wdrożyć połączoną usługę, kliknij przycisk **Wdróż** na pasku poleceń. Upewnij się, że widzisz AzureSqlLinkedService w widoku drzewa po lewej stronie.

    ![Widok drzewa z połączonej usługi](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Tworzenie wyjściowego zestawu danych
Należy określić wyjściowy zestaw danych działania procedura składowana, nawet jeśli procedura składowana nie generuje żadnych danych. To, ponieważ jest wyjściowy zestaw danych, która napędza harmonogramu działania (częstotliwość działanie jest uruchamiane — co godzinę, codziennie itp.). Wyjściowy zestaw danych należy użyć **połączoną usługę** odwołujący się do usługi Azure SQL Database, Azure SQL Data Warehouse lub bazy danych programu SQL Server ma procedurę przechowywaną, aby uruchomić. Wyjściowy zestaw danych może służyć jako sposób przekazać wyników procedury składowanej do późniejszego przetwarzania przez innego działania ([tworzenie łańcuchów działań](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) w potoku. Jednak fabryka danych nie automatycznie zapisuje dane wyjściowe procedury składowanej do tego zestawu danych. Jest procedury przechowywanej, która zapisuje do tabeli SQL, która wskazuje wyjściowy zestaw danych. W niektórych przypadkach może być wyjściowy zestaw danych **fikcyjnego dataset** (zestaw danych wskazujący tabelę, która naprawdę nie przechowuje danych wyjściowych procedury składowanej). Ten zastępczy zestaw danych jest używana tylko po to, aby określić harmonogram uruchamiania działania procedury składowanej.

1. Kliknij przycisk **... Więcej** na pasku narzędzi kliknij **nowy zestaw danych**i kliknij przycisk **Azure SQL**. **Nowy zestaw danych** na pasku poleceń i wybierz **Azure SQL**.

    ![Widok drzewa z połączonej usługi](media/data-factory-stored-proc-activity/new-dataset.png)
2. Kopiuj/wklej poniższy skrypt JSON w celu edytora JSON.

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
3. Aby wdrożyć zestaw danych, kliknij przycisk **Wdróż** na pasku poleceń. Upewnij się, że zostanie wyświetlony zestaw danych w widoku drzewa.

    ![Widok drzewa z połączonymi usługami](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>Tworzenie potoku z działaniem SqlServerStoredProcedure
Teraz Utwórzmy potoku za pomocą działania procedury składowanej.

Zwróć uwagę, następujące właściwości:

- **Typu** właściwość jest ustawiona na **SqlServerStoredProcedure**.
- **StoredProcedureName** w typie właściwości jest równa **usp_sample** (Nazwa procedury składowanej).
- **StoredProcedureParameters** sekcja zawiera jeden parametr o nazwie **daty/godziny**. Nazwa i wielkość liter w wyrazie parametru w formacie JSON muszą być zgodne, nazwa i wielkość liter w wyrazie parametr w definicji procedury składowanej. Jeśli potrzebujesz przekazać wartości null dla parametru, należy użyć składni: `"param1": null` (tylko małe litery).

1. Kliknij przycisk **... Więcej** paska poleceń i kliknij przycisk **nowy potok**.
2. Kopiuj/wklej poniższy fragment kodu JSON:

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
3. Aby wdrożyć potok, kliknij przycisk **Wdróż** na pasku narzędzi.

### <a name="monitor-the-pipeline"></a>Monitorowanie potoku
1. Kliknij przycisk **X**, aby zamknąć bloki Edytora fabryki danych i przejść z powrotem do bloku Fabryka danych, a następnie kliknij przycisk **Diagram**.

    ![Kafelek diagram](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. Na stronie **Widok diagramu** zostanie wyświetlony przegląd potoków i zestawów danych używanych w tym samouczku.

    ![Kafelek diagram](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. W widoku diagramu kliknij dwukrotnie zestaw danych `sprocsampleout`. Możesz wyświetlić wycinki w stanie gotowe. Powinna istnieć pięć wycinki, ponieważ wycinek jest generowany dla każdej godziny między czasem rozpoczęcia i godzina zakończenia w formacie JSON.

    ![Kafelek diagram](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. Gdy wycinek jest w **gotowe** stanu, uruchom `select * from sampletable` zapytania względem bazy danych Azure SQL, aby sprawdzić, czy dane został wstawione do tabeli przez procedurę składowaną.

   ![Dane wyjściowe](./media/data-factory-stored-proc-activity/output.png)

   Zobacz [monitorowanie potoku](data-factory-monitor-manage-pipelines.md) szczegółowe informacje o monitorowaniu potoków usługi Azure Data Factory.

## <a name="specify-an-input-dataset"></a>Określ wejściowego zestawu danych
W instruktażu działania procedury składowanej nie ma żadnych danych wejściowych zestawów danych. Jeśli określisz wejściowego zestawu danych, działania procedury składowanej nie działa do momentu wycinek wejściowego zestawu danych jest dostępna (w stanie gotowe). Zestaw danych może być zewnętrzny zestaw danych (który nie jest realizowane przez innego działania w potoku w tym samym) lub wewnętrzny zestaw danych, który jest wytwarzany przez nadrzędne działanie (działanie, który jest uruchamiany zanim to działanie). Można określić wiele danych wejściowych zestawów danych dla działania procedury składowanej. Jeśli tak zrobisz, działania procedury składowanej działa tylko wtedy, gdy wszystkie wycinki danych wejściowych zestawu danych są dostępne (w stanie gotowe). Wejściowy zestaw danych nie mogą być używane w procedurze składowanej jako parametr. Tylko służy do sprawdzania zależności przed rozpoczęciem działania procedury składowanej.

## <a name="chaining-with-other-activities"></a>Tworzenie łańcucha z innymi działaniami
Jeśli chcesz połączyć w łańcuch działanie w strumieniu przychodzącym za pomocą tego działania, należy określić dane wyjściowe działania nadrzędnego jako dane wejściowe tego działania. Jeśli tak zrobisz, działania procedury składowanej nie działa, dopóki nie zakończy działanie w strumieniu przychodzącym i wyjściowy zestaw danych działanie w strumieniu przychodzącym jest dostępny (w stanie gotowe). Wyjściowe zestawy danych z wielu czynności nadrzędnych można określić jako danych wejściowych zestawów danych działania procedura składowana. Jeśli tak zrobisz, działania procedury składowanej działa tylko wtedy, gdy wszystkie wycinki danych wejściowych zestawu danych są dostępne.

W poniższym przykładzie danych wyjściowych działania kopiowania jest: OutputDataset, która jest wartością wejściową działania procedury składowanej. W związku z tym działania procedury składowanej nie działa, dopóki nie kończy działanie kopiowania i wycinek OutputDataset jest dostępne (w stanie gotowe). Jeśli określisz wiele danych wejściowych zestawów danych, działania procedury składowanej nie działa, dopóki wszystkie wycinki danych wejściowych zestawu danych są dostępne (w stanie gotowe). Wejściowe zestawy danych nie można bezpośrednio jako parametry do działania procedury składowanej.

Aby uzyskać więcej informacji na temat Tworzenie łańcuchów działań, zobacz [wiele działań w potoku](data-factory-create-pipelines.md#multiple-activities-in-a-pipeline)

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

Podobnie połączyć działanie procedury magazynu za pomocą **działania podrzędne** (działań, które uruchamiane po ukończeniu działania procedury składowanej), określ wyjściowy zestaw danych działania procedura składowana jako dane wejściowe podrzędne działania w potoku.

> [!IMPORTANT]
> Podczas kopiowania danych do usługi Azure SQL Database lub SQL Server, można skonfigurować **SqlSink** w działaniu kopiowania, aby wywołać procedurę składowaną przy użyciu **sqlWriterStoredProcedureName** właściwości. Aby uzyskać więcej informacji, zobacz [wywołaj procedurę składowaną z działaniem kopiowania](data-factory-invoke-stored-procedure-from-copy-activity.md). Aby uzyskać szczegółowe informacje o właściwości zobacz następujące artykuły łącznika: [Usługa Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [programu SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties).
> 
> Podczas kopiowania danych z usługi Azure SQL Database lub SQL Server lub usługi Azure SQL Data Warehouse, możesz skonfigurować **SqlSource** w działaniu kopiowania, aby wywołać procedurę przechowywaną, aby odczytać danych ze źródłowej bazy danych za pomocą  **sqlReaderStoredProcedureName** właściwości. Aby uzyskać więcej informacji zobacz następujące artykuły łącznika: [Usługa Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [programu SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

## <a name="json-format"></a>JSON format
Oto formatu JSON do definiowania działania dotyczącego procedury składowanej:

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

W poniższej tabeli opisano te właściwości kodu JSON:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| name | Nazwa działania |Yes |
| description |Tekst opisujący przeznaczenie działania |Nie |
| type | Musi być ustawione na: **SqlServerStoredProcedure** | Yes |
| inputs | Opcjonalny. Jeśli określisz wejściowy zestaw danych musi być dostępny (w stanie "Gotowy") dla działania procedury składowanej do uruchomienia. Wejściowy zestaw danych nie mogą być używane w procedurze składowanej jako parametr. Tylko służy do sprawdzania zależności przed rozpoczęciem działania procedury składowanej. |Nie |
| outputs | Należy określić wyjściowy zestaw danych działania procedura składowana. Wyjściowy zestaw danych określa **harmonogram** działania procedury składowanej (co godzinę, co tydzień, co miesiąc, itp.). <br/><br/>Wyjściowy zestaw danych należy użyć **połączoną usługę** odwołujący się do usługi Azure SQL Database, Azure SQL Data Warehouse lub bazy danych programu SQL Server ma procedurę przechowywaną, aby uruchomić. <br/><br/>Wyjściowy zestaw danych może służyć jako sposób przekazać wyników procedury składowanej do późniejszego przetwarzania przez innego działania ([tworzenie łańcuchów działań](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) w potoku. Jednak fabryka danych nie automatycznie zapisuje dane wyjściowe procedury składowanej do tego zestawu danych. Jest procedury przechowywanej, która zapisuje do tabeli SQL, która wskazuje wyjściowy zestaw danych. <br/><br/>W niektórych przypadkach może być wyjściowy zestaw danych **fikcyjnego dataset**, która jest używana tylko po to, aby określić harmonogram uruchamiania działania procedury składowanej. |Tak |
| storedProcedureName |Określ nazwę procedury przechowywanej w bazie danych Azure SQL lub bazy danych Azure SQL Data Warehouse lub SQL Server, który jest reprezentowany przez połączoną usługę, która używa tabeli wyjściowej. |Yes |
| storedProcedureParameters |Określ wartości dla parametrów procedury składowanej. Jeśli musisz przekazać wartości null dla parametru należy użyć składni: "param1": wartość null (wszystkie małe litery). Zobacz poniższy przykład, aby dowiedzieć się więcej o korzystaniu z tej właściwości. |Nie |

## <a name="passing-a-static-value"></a>Przekazując wartość statyczną
Teraz rozważmy dodanie innej kolumny o nazwie "Scenariusza" w tabeli zawierającej wartość statyczną o nazwie "Dokumentu przykładowy".

![Przykładowe dane 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

**Tabela:**

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

Teraz Przekaż **scenariusza** parametru i wartości z działania procedury składowanej. **TypeProperties** sekcji w poprzednim przykładzie wygląda jak poniższy fragment kodu:

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

**Zestaw danych fabryki danych:**

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

**Potok usługi Data Factory**

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
