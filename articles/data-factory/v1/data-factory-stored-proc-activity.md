---
title: Działanie procedury przechowywane programu SQL Server
description: Dowiedz się, jak SQL Server działania dotyczącego procedury składowanej umożliwia wywołanie procedury przechowywanej w bazie danych SQL Azure lub usługi Azure SQL Data Warehouse z potoku fabryki danych.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: douglasl
robots: noindex
ms.openlocfilehash: a628374841126a750ebf6881f8adec66340c1d29
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="sql-server-stored-procedure-activity"></a>Działanie procedury przechowywane programu SQL Server
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Działanie gałęzi](data-factory-hive-activity.md) 
> * [Działanie pig](data-factory-pig-activity.md)
> * [Działania MapReduce](data-factory-map-reduce.md)
> * [Działaniu przesyłania strumieniowego usługi Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Działanie Spark](data-factory-spark.md)
> * [Działanie wykonywania wsadowego w usłudze Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Działania aktualizowania zasobów w usłudze Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Działania procedur składowanych](data-factory-stored-proc-activity.md)
> * [Działania języka U-SQL usługi Data Lake Analytics](data-factory-usql-activity.md)
> * [Działania niestandardowe .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Azure Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [Przekształcanie danych za pomocą działania procedury składowanej w fabryce danych w wersji 2](../transform-data-using-stored-procedure.md).

## <a name="overview"></a>Przegląd
Użyj działania przekształcania danych w fabryce danych [potoku](data-factory-create-pipelines.md) do transformacji i przetwarzać dane pierwotne służące do przewidywania i szczegółowych informacji. Działania dotyczącego procedury składowanej jest jednym z działania przekształcania, które obsługuje fabryki danych. W tym artykule opiera się na [działań przekształcania danych](data-factory-data-transformation-activities.md) artykułu, który przedstawia ogólny przegląd transformacji danych i działań przekształcania obsługiwanych w fabryce danych.

Działania dotyczącego procedury składowanej umożliwia wywołanie procedury składowanej w jednym z następujących magazynów danych w przedsiębiorstwie lub na maszynie wirtualnej platformy Azure (VM): 

- Azure SQL Database
- Azure SQL Data Warehouse
- Baza danych programu SQL Server.  Jeśli używasz programu SQL Server, należy zainstalować bramę zarządzania danymi na tym samym komputerze, który jest hostem bazy danych lub na osobnym komputerze, który ma dostęp do bazy danych. Brama zarządzania danymi jest składnik, który nawiązuje połączenie danych źródeł na lokalnym/na maszynie Wirtualnej platformy Azure z usługami w chmurze w sposób bezpieczny i zarządzanie nimi. Zobacz [brama zarządzania danymi](data-factory-data-management-gateway.md) artykułu, aby uzyskać szczegółowe informacje.

> [!IMPORTANT]
> Podczas kopiowania danych do usługi Azure SQL Database lub SQL Server, można skonfigurować **SqlSink** w przypadku działania kopiowania, aby wywołać procedurę składowaną przy użyciu **sqlWriterStoredProcedureName** właściwości. Aby uzyskać więcej informacji, zobacz [wywołaj procedurę składowaną z działania kopiowania](data-factory-invoke-stored-procedure-from-copy-activity.md). Aby uzyskać więcej informacji dotyczących właściwości, zobacz następujące artykuły łącznika: [bazy danych SQL Azure](data-factory-azure-sql-connector.md#copy-activity-properties), [programu SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties). Wywoływanie procedury składowanej podczas kopiowania danych do usługi Azure SQL Data Warehouse przy użyciu aktywność kopiowania nie jest obsługiwane. Jednak działania procedury składowanej służy do wywołania procedury przechowywanej w usłudze SQL Data Warehouse. 
>  
> Podczas kopiowania danych z bazy danych SQL Azure lub programu SQL Server lub magazyn danych SQL Azure, możesz skonfigurować **SqlSource** w przypadku działania kopiowania, aby wywołać procedurę składowaną można odczytać danych ze źródłowej bazy danych przy użyciu  **sqlReaderStoredProcedureName** właściwości. Aby uzyskać więcej informacji, zobacz następujące artykuły łącznika: [bazy danych SQL Azure](data-factory-azure-sql-connector.md#copy-activity-properties), [programu SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [magazyn danych SQL Azure](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)          


Poniższe wskazówki używa działania dotyczącego procedury składowanej w potoku do wywołania procedury przechowywanej w bazie danych Azure SQL. 

## <a name="walkthrough"></a>Przewodnik
### <a name="sample-table-and-stored-procedure"></a>Przykładowa tabela i procedury składowanej
1. Utwórz następującą **tabeli** w bazie danych SQL Azure przy użyciu programu SQL Server Management Studio lub innych narzędzi potrafisz. Kolumna datetimestamp jest data i godzina wygenerowania odpowiedni identyfikator.

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
    Identyfikator jest unikatowy zidentyfikować i kolumna datetimestamp jest data i godzina wygenerowania odpowiedni identyfikator.
    
    ![Dane przykładowe](./media/data-factory-stored-proc-activity/sample-data.png)

    W tym przykładzie procedura składowana jest w bazie danych SQL Azure. Jeśli procedura składowana jest magazyn danych SQL Azure i bazy danych programu SQL Server, to rozwiązanie jest podobne. Dla bazy danych programu SQL Server, należy zainstalować [brama zarządzania danymi](data-factory-data-management-gateway.md).
2. Utwórz następującą **procedury składowanej** która wstawia dane w celu **sampletable**.

    ```SQL
    CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime)
    END
    ```

   > [!IMPORTANT]
   > **Nazwa** i **wielkości liter** parametru (DateTime w tym przykładzie) musi być zgodna z parametr określony w potoku/aktywność JSON. W definicji procedury składowanej, upewnij się, że **@** służy jako prefiksu w parametrze.

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
2. Kliknij przycisk **nowy** w menu po lewej stronie kliknij **analizy i analiza**i kliknij przycisk **fabryki danych**.

    ![Nowa fabryka danych](media/data-factory-stored-proc-activity/new-data-factory.png)    
3. W **nowa fabryka danych** bloku, wprowadź **SProcDF** dla nazwy. Nazwy fabryki danych Azure są **unikatowych**. Musisz prefiksu nazwy fabryki danych z nazwą, aby umożliwić pomyślne utworzenie fabryki.

   ![Nowa fabryka danych](media/data-factory-stored-proc-activity/new-data-factory-blade.png)         
4. Wybierz użytkownika **subskrypcji platformy Azure**.
5. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:
   1. Kliknij przycisk **Utwórz nowy** , a następnie wprowadź nazwę grupy zasobów.
   2. Kliknij przycisk **Użyj istniejącego** i wybierz istniejącą grupę zasobów.  
6. Na liście **lokalizacja** wybierz lokalizację fabryki danych.
7. Wybierz **Przypnij do pulpitu nawigacyjnego** , dzięki czemu można wyświetlić fabryki danych na pulpicie nawigacyjnym następnym zalogowaniu.
8. Kliknij przycisk **Utwórz** w bloku **Nowa fabryka danych**.
9. Zobacz tworzony w fabryce danych **pulpitu nawigacyjnego** portalu Azure. Po pomyślnym utworzeniu fabryki danych zostanie wyświetlona strona fabryki danych z zawartością fabryki danych.

   ![Strona główna fabryki danych](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Tworzenie usługi SQL Azure połączone
Po utworzeniu fabryki danych, możesz utworzyć Azure SQL połączonej usługi, która łączy bazy danych Azure SQL, który zawiera tabelę sampletable i sp_sample przechowywane procedury z fabryką danych.

1. Kliknij przycisk **tworzenie i wdrażanie** na **fabryki danych** bloku **SProcDF** można uruchomić Edytor fabryki danych.
2. Kliknij przycisk **nowy magazyn danych** polecenie Pasek i wybierz polecenie **bazy danych SQL Azure**. Powinna zostać wyświetlona skryptu JSON do tworzenia usług SQL Azure połączone w edytorze.

   ![Nowy magazyn danych](media/data-factory-stored-proc-activity/new-data-store.png)
3. W skrypcie JSON wprowadź następujące zmiany:

   1. Zastąp `<servername>` z nazwy serwera bazy danych SQL Azure.
   2. Zastąp `<databasename>` z bazą danych, w którym został utworzony w tabeli i procedury składowanej.
   3. Zastąp `<username@servername>` przy użyciu konta użytkownika, który ma dostęp do bazy danych.
   4. Zastąp `<password>` hasłem do konta użytkownika.

      ![Nowy magazyn danych](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. Aby wdrożyć połączonej usługi, kliknij przycisk **Wdróż** na pasku poleceń. Upewnij się, że widoczny AzureSqlLinkedService w widoku drzewa po lewej stronie.

    ![Widok drzewa połączonej usługi](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Tworzenie wyjściowego zestawu danych
Należy określić wyjściowy zestaw danych działania procedura składowana nawet wtedy, gdy procedura składowana nie zwraca żadnych danych. To, ponieważ jest on wyjściowy zestaw danych, które dyski harmonogram działania (częstotliwość działanie jest uruchamiane — co godzinę, codziennie, itp.). Wyjściowy zestaw danych musi używać **połączona usługa** odwołujący się do bazy danych SQL Azure lub usługi Azure SQL Data Warehouse lub bazy danych programu SQL Server w której ma zostać procedurę składowaną, aby uruchomić. Wyjściowy zestaw danych może stanowić sposób przekazać wyników procedury składowanej dla kolejnych przetwarzania przez inne działanie ([łańcucha działania](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) w potoku. Jednak fabryki danych nie automatycznie zapisuje dane wyjściowe procedury składowanej do tego elementu dataset. Jest procedury przechowywanej, która zapisuje do tabeli SQL, która wskazuje wyjściowego zestawu danych. W niektórych przypadkach może być wyjściowy zestaw danych **fikcyjny dataset** (zestawu danych, który wskazuje tabelę, która naprawdę nie przechowuje dane wyjściowe procedury składowanej). Tego fikcyjny zestawu danych jest używany tylko w celu określenia harmonogram uruchamiania działania procedury składowanej. 

1. Kliknij przycisk **... Więcej** na pasku narzędzi kliknij **nowy zestaw danych**i kliknij przycisk **Azure SQL**. **Nowy zestaw danych** na pasku poleceń i wybierz **Azure SQL**.

    ![Widok drzewa połączonej usługi](media/data-factory-stored-proc-activity/new-dataset.png)
2. Skopiuj/Wklej poniższy skrypt JSON w celu edytora JSON.

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
3. Aby wdrożyć zestawu danych, kliknij przycisk **Wdróż** na pasku poleceń. Upewnij się, że jest wyświetlany element dataset w widoku drzewa.

    ![Widok drzewa z połączonych usług](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>Utworzyć potok z działaniem SqlServerStoredProcedure
Teraz Utwórzmy potoku z działania procedury składowanej. 

Zwróć uwagę następujące właściwości: 

- **Typu** właściwość jest ustawiona na **SqlServerStoredProcedure**. 
- **StoredProcedureName** w typ właściwości jest równa **sp_sample** (Nazwa procedury składowanej).
- **StoredProcedureParameters** sekcja zawiera jeden parametr o nazwie **DateTime**. Nazwa i wielkość liter w wyrazie parametru w formacie JSON muszą być zgodne, nazwa i wielkość liter w wyrazie parametru w definicji procedury składowanej. Jeśli należy przekazać wartość null dla parametru, należy użyć składni: `"param1": null` (tylko małe litery).
 
1. Kliknij przycisk **... Więcej** na pasku poleceń i kliknij przycisk **nowy potok**.
2. Skopiuj/Wklej poniższy fragment kodu JSON:   

    ```JSON
    {
        "name": "SprocActivitySamplePipeline",
        "properties": {
            "activities": [
                {
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
                        "storedProcedureName": "sp_sample",
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

    ![Diagram kafelka](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. Na stronie **Widok diagramu** zostanie wyświetlony przegląd potoków i zestawów danych używanych w tym samouczku.

    ![Diagram kafelka](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. W widoku diagramu, kliknij dwukrotnie element dataset `sprocsampleout`. Zostanie wyświetlony wycinków w stanie gotowe. Powinien istnieć pięć wycinków, ponieważ wycinek jest tworzone dla każdej godziny od czasu rozpoczęcia i godzina zakończenia w formacie JSON.

    ![Diagram kafelka](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. Gdy wycinek jest **gotowe** stanu, uruchom `select * from sampletable` zapytanie w bazie danych Azure SQL, aby sprawdzić, czy dane dodano do tabeli przez procedurę składowaną.

   ![Dane wyjściowe](./media/data-factory-stored-proc-activity/output.png)

   Zobacz [monitorować potoku](data-factory-monitor-manage-pipelines.md) szczegółowe informacje o monitorowaniu potoki fabryki danych Azure.  


## <a name="specify-an-input-dataset"></a>Określ zestaw danych wejściowych
W tym przewodnikiem działania procedury składowanej nie ma żadnych wejściowe zestawy danych. Jeśli określisz zestaw danych wejściowych działania procedury składowanej nie działa do momentu wycinka wejściowy zestaw danych jest dostępna (w stanie innym niż Ready). Zestaw danych może być zewnętrzny zestaw danych, (tj. nie jest generowany przez innego działania w tym samym potoku) lub wewnętrzny zestawu danych, który jest generowany przez działanie w strumieniu przychodzącym (działania wykonywane przed tego działania). Można określić wiele zestawów wejściowych danych działania procedury składowanej. Jeśli tak zrobisz, działania procedury składowanej działa tylko wtedy, gdy wszystkie fragmenty wejściowy zestaw danych są dostępne (w stanie gotowe). Wejściowy zestaw danych nie mogą być używane w procedurze składowanej jako parametr. Tylko służy do sprawdzania zależności przed rozpoczęciem działania procedury składowanej.

## <a name="chaining-with-other-activities"></a>Łańcuch serwerów z innymi działaniami
Jeśli chcesz tworzyć łańcuchy działania nadrzędnego tego działania, określ dane wyjściowe działania nadrzędnego jako dane wejściowe tego działania. Po wykonaniu tego działania procedury składowanej nie działa, dopiero po ukończeniu działania nadrzędnego i wyjściowy zestaw danych działania nadrzędnego jest dostępna (w stanie gotowe). Wyjściowe zestawy danych z wielu działań nadrzędnego może służyć jako wejściowe zestawy danych działania procedury składowanej. Po wykonaniu tego działania procedury składowanej działa tylko wtedy, gdy są dostępne wszystkie fragmenty wejściowy zestaw danych.  

W poniższym przykładzie jest dane wyjściowe działania kopiowania: OutputDataset, która jest wartością wejściową działania procedury składowanej. W związku z tym działania procedury składowanej nie działa, dopiero po ukończeniu działania kopiowania i wycinek OutputDataset jest dostępna (w stanie gotowe). Jeśli określisz wiele zestawów danych wejściowych działania procedury składowanej nie działa, dopóki wszystkie fragmenty wejściowy zestaw danych są dostępne (w stanie gotowe). Wejściowe zestawy danych nie można użyć bezpośrednio jako parametry działania procedury składowanej. 

Aby uzyskać więcej informacji na tworzenie łańcucha działań, zobacz [wielu działań w potoku](data-factory-create-pipelines.md#multiple-activities-in-a-pipeline)

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

Podobnie Aby połączyć działania procedury magazynu o **działania podrzędne** (działań uruchamianych po ukończeniu działania procedury składowanej), określ wyjściowy zestaw danych działania procedura składowana jako danych wejściowych działanie podrzędne w potoku.

> [!IMPORTANT]
> Podczas kopiowania danych do usługi Azure SQL Database lub SQL Server, można skonfigurować **SqlSink** w przypadku działania kopiowania, aby wywołać procedurę składowaną przy użyciu **sqlWriterStoredProcedureName** właściwości. Aby uzyskać więcej informacji, zobacz [wywołaj procedurę składowaną z działania kopiowania](data-factory-invoke-stored-procedure-from-copy-activity.md). Aby uzyskać więcej informacji dotyczących właściwości, zobacz następujące artykuły łącznika: [bazy danych SQL Azure](data-factory-azure-sql-connector.md#copy-activity-properties), [programu SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties).
>  
> Podczas kopiowania danych z bazy danych SQL Azure lub programu SQL Server lub magazyn danych SQL Azure, możesz skonfigurować **SqlSource** w przypadku działania kopiowania, aby wywołać procedurę składowaną można odczytać danych ze źródłowej bazy danych przy użyciu  **sqlReaderStoredProcedureName** właściwości. Aby uzyskać więcej informacji, zobacz następujące artykuły łącznika: [bazy danych SQL Azure](data-factory-azure-sql-connector.md#copy-activity-properties), [programu SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [magazyn danych SQL Azure](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)          

## <a name="json-format"></a>JSON format
Oto formatu JSON określających działania dotyczącego procedury składowanej:

```JSON
{
    "name": "SQLSPROCActivity",
    "description": "description",
    "type": "SqlServerStoredProcedure",
    "inputs":  [ { "name": "inputtable"  } ],
    "outputs":  [ { "name": "outputtable" } ],
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

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| name | Nazwa działania |Yes |
| description |Tekst opisujący działanie służy do |Nie |
| type | Musi mieć wartość: **SqlServerStoredProcedure** | Yes |
| Dane wejściowe | Opcjonalny. Jeśli określisz wejściowy zestaw danych, musi być dostępny (w stanie "Gotowe") dla działania procedury składowanej do uruchomienia. Wejściowy zestaw danych nie mogą być używane w procedurze składowanej jako parametr. Tylko służy do sprawdzania zależności przed rozpoczęciem działania procedury składowanej. |Nie |
| wyjścia | Należy określić zestaw danych wyjściowych dla działania procedury składowanej. Określa wyjściowy zestaw danych **harmonogram** działania procedury składowanej (co godzinę, co tydzień, co miesiąc, itp.). <br/><br/>Wyjściowy zestaw danych musi używać **połączona usługa** odwołujący się do bazy danych SQL Azure lub usługi Azure SQL Data Warehouse lub bazy danych programu SQL Server w której ma zostać procedurę składowaną, aby uruchomić. <br/><br/>Wyjściowy zestaw danych może stanowić sposób przekazać wyników procedury składowanej dla kolejnych przetwarzania przez inne działanie ([łańcucha działania](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) w potoku. Jednak fabryki danych nie automatycznie zapisuje dane wyjściowe procedury składowanej do tego elementu dataset. Jest procedury przechowywanej, która zapisuje do tabeli SQL, która wskazuje wyjściowego zestawu danych. <br/><br/>W niektórych przypadkach może być wyjściowy zestaw danych **fikcyjny zestawu danych**, które służą tylko do określ harmonogram uruchamiania działania procedury składowanej. |Yes |
| storedProcedureName |Określ nazwę procedury składowanej bazy danych Azure SQL lub w bazie danych magazynu danych SQL Azure lub programu SQL Server, reprezentowanego przez połączonej usługi, która używa tabeli wyników. |Yes |
| storedProcedureParameters |Określ wartości dla parametrów procedury składowanej. Jeśli chcesz przekazać wartości null dla parametru należy użyć składni: "param1": wartość null (małe litery). Zobacz poniższy przykład, aby dowiedzieć się więcej o korzystaniu z tej właściwości. |Nie |

## <a name="passing-a-static-value"></a>Przekazywanie wartości statycznej
Teraz załóżmy należy rozważyć dodanie inną kolumnę o nazwie "Scenariusza" w tabeli zawierającej wartości statycznej o nazwie "Próbki dokumentu".

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
CREATE PROCEDURE sp_sample2 @DateTime nvarchar(127) , @Scenario nvarchar(127)

AS

BEGIN
    INSERT INTO [sampletable2]
    VALUES (newid(), @DateTime, @Scenario)
END
```

Teraz, Przekaż **scenariusza** parametr i wartość z działania procedury składowanej. **TypeProperties** sekcji w poprzednim przykładzie wygląda następująco: następujący fragment kodu:

```JSON
"typeProperties":
{
    "storedProcedureName": "sp_sample",
    "storedProcedureParameters":
    {
        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
        "Scenario": "Document sample"
    }
}
```

**Zestaw danych z fabryki danych:**

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
                    "storedProcedureName": "sp_sample2",
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
