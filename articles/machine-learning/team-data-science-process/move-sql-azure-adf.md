---
title: Dane programu SQL Server do usługi SQL Azure z usługą Azure Data Factory — zespołu danych dla celów naukowych
description: Konfigurowanie potoku usługi ADF, który komponuje się dwa działania migracji danych, które przenoszą dane ze sobą codziennie między bazami danych w środowisku lokalnym i w chmurze.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 59f8b8b253fc914e5723a9c41475ec78bc3f376e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61429352"
---
# <a name="move-data-from-an-on-premises-sql-server-to-sql-azure-with-azure-data-factory"></a>Przenoszenie danych z lokalnego programu SQL server do usługi SQL Azure z usługą Azure Data Factory

W tym artykule pokazano, jak przenieść dane z bazy danych lokalnego programu SQL Server do usługi Azure SQL Database za pomocą usługi Azure Blob Storage przy użyciu usługi Azure Data Factory (ADF).

Dla tabeli, który podsumowuje różne opcje przenoszenia danych do usługi Azure SQL Database, zobacz [przenoszenie danych do usługi Azure SQL Database dla usługi Azure Machine Learning](move-sql-azure.md).

## <a name="intro"></a>Wprowadzenie: Co to jest ADF i kiedy należy go użyć do migracji danych?
Usługa Azure Data Factory to usługa integracji danych w chmurze w pełni zarządzana, która organizuje i automatyzuje operacje przenoszenia i przekształcania danych. Kluczową koncepcją w modelu usługi ADF jest potok. Potoki to logiczne grupy działań, z których każdy definiuje akcje do wykonania na danych zawartych w zestawach danych. Połączone usługi są używane do definiowania informacje wymagane przez usługę Data Factory, aby połączyć się z zasobami danych.

Za pomocą usługi ADF może być składana istniejących usług przetwarzania danych, potoki danych, które są wysoko dostępne i zarządzane w chmurze. Te błędy potoków danych mogą być planowane do pozyskiwania, przygotowywania, przekształcania, analizowanie i publikować dane i usługi ADF zarządza i organizuje złożonych danych i przetwarzania zależności. Rozwiązania można szybko skompilować i wdrożyć w chmurze, łączenie z coraz większej liczbie w środowisku lokalnym i źródeł danych w chmurze.

Rozważ użycie usługi ADF:

* gdy dane muszą stale migracji w scenariusza hybrydowego, który uzyskuje dostęp do obu — lokalnych i zasobów w chmurze
* Jeśli danych jest wykonany lub musi być modyfikowany ani logiki biznesowej do niej dodać, gdy migrowane.

ADF umożliwia planowanie i monitorowanie zadań przy użyciu prostych skryptów JSON, które zarządzają przenoszenia danych w regularnych odstępach czasu. ADF ma także inne możliwości, takie jak obsługa złożonych operacji. Aby uzyskać więcej informacji na temat usługi ADF, zobacz dokumentację w [usługi Azure Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/).

## <a name="scenario"></a>Scenariusz
Skonfigurujemy potoku usługi ADF, który komponuje się dwa działania migracji danych. Razem przechodzą danych codziennie między bazą danych SQL database w środowisku lokalnym i usługi Azure SQL Database w chmurze. Są dwa działania:

* Kopiowanie danych z lokalnej bazy danych programu SQL Server do konta usługi Azure Blob Storage
* Kopiowanie danych z konta usługi Azure Blob Storage do usługi Azure SQL Database.

> [!NOTE]
> Kroki opisane w tym miejscu zostały zaczerpnięte z bardziej szczegółowy samouczek dotyczący dostarczane przez zespół usługi ADF: [Kopiowanie danych z lokalnej bazy danych programu SQL Server do usługi Azure Blob storage](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal/) odwołania do odpowiednich sekcji tego tematu znajdują się w odpowiednich przypadkach.
>
>

## <a name="prereqs"></a>Wymagania wstępne
W tym samouczku przyjęto założenie, że masz:

* **Subskrypcji platformy Azure**. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* **Konta usługi Azure storage**. Używasz konta usługi Azure storage do przechowywania danych w ramach tego samouczka. Jeśli nie masz konta usługi Azure storage, zobacz [Tworzenie konta magazynu](../../storage/common/storage-quickstart-create-account.md) artykułu. Po utworzeniu konta magazynu, należy uzyskać klucz konta, które umożliwiają dostęp do magazynu. Zobacz [zarządzanie kluczami dostępu do magazynu](../../storage/common/storage-account-manage.md#access-keys).
* Dostęp do **usługi Azure SQL Database**. Jeśli musisz skonfigurować usługi Azure SQL Database, temat [wprowadzenie do programu Microsoft Azure SQL Database](../../sql-database/sql-database-get-started.md) zawiera informacje o udostępnienie nowego wystąpienia usługi Azure SQL Database.
* Zainstalowany i skonfigurowany **programu Azure PowerShell** lokalnie. Aby uzyskać instrukcje, zobacz [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview).

> [!NOTE]
> Ta procedura wykorzystuje [witryny Azure portal](https://portal.azure.com/).
>
>

## <a name="upload-data"></a> Przekazywanie danych do programu SQL Server w środowisku lokalnym
Używamy [zestawu danych taksówek NYC](https://chriswhong.com/open-data/foil_nyc_taxi/) aby zademonstrować procesu migracji. Zestaw danych taksówek NYC jest dostępna, jak wspomniano w tym wpisie w usłudze Azure blob storage [dane taksówek NYC](https://www.andresmh.com/nyctaxitrips/). Dane mają dwa pliki, plik trip_data.csv, który zawiera szczegóły podróży, i plik trip_far.csv, który zawiera szczegółowe informacje o klasie opłacony każdego podróży. Próbki i opisy te pliki znajdują się w [opis zestawu danych podróży taksówek NYC](sql-walkthrough.md#dataset).

Możesz dostosować procedury w tym miejscu do zestawu danych użytkownika lub zgodnie z opisem przy użyciu zestawu danych taksówek NYC postępuj zgodnie z instrukcjami. Aby przekazać zestaw danych taksówek NYC do lokalnej bazy danych programu SQL Server, wykonaj procedury opisane w [zbiorcze importowanie danych do bazy danych serwera SQL](sql-walkthrough.md#dbload). Te instrukcje są przeznaczone dla programu SQL Server na maszynie wirtualnej platformy Azure, ale procedura przekazywania do lokalnego programu SQL Server jest taka sama.

## <a name="create-adf"></a> Tworzenie fabryki danych platformy Azure
Instrukcje dotyczące tworzenia nowej fabryki danych platformy Azure i grupę zasobów w [witryny Azure portal](https://portal.azure.com/) znajdują się [Tworzenie usługi Azure Data Factory](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-data-factory). Nazwa nowego wystąpienia usługi ADF *adfdsp* i nazwy grupy zasobów utworzonej *adfdsprg*.

## <a name="install-and-configure-azure-data-factory-integration-runtime"></a>Instalowanie i konfigurowanie usługi Azure Data Factory Integration Runtime
Środowisko IR to infrastruktura integracji danych zarządzanych klientów, używane przez usługi Azure Data Factory w celu zapewnienia możliwości integracji danych w różnych środowiskach sieciowych. To środowisko wykonawcze był wcześniej nazywany "Brama zarządzania danymi".

Aby skonfigurować, [postępuj zgodnie z instrukcjami dotyczącymi tworzenia potoku](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal#create-a-pipeline)

## <a name="adflinkedservices"></a>Utwórz połączone usługi, aby połączyć się z zasobami danych
Połączona usługa definiuje informacje wymagane przez usługę Azure Data Factory, aby nawiązać połączenie z zasobem danych. Mamy trzy zasoby w tym scenariuszu, dla którego połączone usługi są potrzebne:

1. Na lokalnym serwerze SQL Server
2. Azure Blob Storage
3. Baza danych Azure SQL Database

Procedura krok po kroku dotyczące tworzenia połączonej usługi jest podawany jako [Tworzenie połączonych usług](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).


## <a name="adf-tables"></a>Definiowanie i tworzenie tabel, aby określić sposób dostępu do zestawów danych
Tworzenie tabel, które określają strukturę, lokalizacji i dostępność zestawów danych przy użyciu poniższych procedur opartych na skryptach. Pliki JSON służą do definiowania tabel. Aby uzyskać więcej informacji na temat struktury te pliki, zobacz [zestawów danych](../../data-factory/concepts-datasets-linked-services.md).

> [!NOTE]
> Powinien zostać wykonany `Add-AzureAccount` polecenia cmdlet, przed wykonaniem [New AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx) polecenia cmdlet, aby upewnić się, że wykonanie polecenia została wybrana subskrypcja bezpośrednio do platformy Azure. Aby uzyskać dokumentację tego polecenia cmdlet, zobacz [Add-AzureAccount](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-3.7.0).
>
>

Definicje opartych na formacie JSON w tabelach stosować następujących nazw:

* **nazwy tabeli** lokalnego serwera SQL server jest *nyctaxi_data*
* **nazwa kontenera** w usłudze Azure Blob Storage jest konto *containername*

Trzy definicje tabel są wymagane przez ten potok ADF:

1. [Tabela programu on-premises SQL](#adf-table-onprem-sql)
2. [Tabela obiektów blob](#adf-table-blob-store)
3. [SQL tabeli platformy Azure](#adf-table-azure-sql)

> [!NOTE]
> Te procedury użyj programu Azure PowerShell, definiować i tworzyć działania usługi ADF. Jednak te zadania może być również wykonywane przy użyciu witryny Azure portal. Aby uzyskać więcej informacji, zobacz [tworzenie zestawów danych](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

### <a name="adf-table-onprem-sql"></a>Tabela programu on-premises SQL
Definicja tabeli dla lokalnego programu SQL Server jest określony w pliku JSON:

```json
{
    "name": "OnPremSQLTable",
    "properties":
    {
        "location":
        {
            "type": "OnPremisesSqlServerTableLocation",
            "tableName": "nyctaxi_data",
            "linkedServiceName": "adfonpremsql"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1,
            "waitOnExternal":
            {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Nazwy kolumn nie zostały uwzględnione w tym miejscu. Można wybierać podrzędnych na nazwy kolumn, uwzględniając je w tym miejscu (Aby uzyskać szczegółowe informacje, sprawdź [dokumentacji usługi ADF](../../data-factory/copy-activity-overview.md) tematu.

Kopiuj do pliku definicji JSON tabeli o nazwie *onpremtabledef.json* plik i zapisz go w znanej lokalizacji (w tym miejscu zakłada się, że *C:\temp\onpremtabledef.json*). Tworzenie tabeli w usłudze ADF za pomocą następującego polecenia cmdlet programu Azure PowerShell:

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


### <a name="adf-table-blob-store"></a>Tabela obiektów blob
Definicja tabeli dla lokalizacji obiektu blob danych wyjściowych znajduje się w następujących (mapuje odebrane dane ze środowiska lokalnego do obiektów blob platformy Azure):

```json
{
    "name": "OutputBlobTable",
    "properties":
    {
        "location":
        {
            "type": "AzureBlobLocation",
            "folderPath": "containername",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": "\t"
            },
            "linkedServiceName": "adfds"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Kopiuj do pliku definicji JSON tabeli o nazwie *bloboutputtabledef.json* plik i zapisz go w znanej lokalizacji (w tym miejscu zakłada się, że *C:\temp\bloboutputtabledef.json*). Tworzenie tabeli w usłudze ADF za pomocą następującego polecenia cmdlet programu Azure PowerShell:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json

### <a name="adf-table-azure-sql"></a>SQL tabeli platformy Azure
Definicja tabeli SQL Azure, danych wyjściowych znajduje się w następujących (w tym schemacie mapuje dane pochodzące z obiektu blob):

```json
{
    "name": "OutputSQLAzureTable",
    "properties":
    {
        "structure":
        [
            { "name": "column1", "type": "String"},
            { "name": "column2", "type": "String"}
        ],
        "location":
        {
            "type": "AzureSqlTableLocation",
            "tableName": "your_db_name",
            "linkedServiceName": "adfdssqlazure_linked_servicename"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Kopiuj do pliku definicji JSON tabeli o nazwie *AzureSqlTable.json* plik i zapisz go w znanej lokalizacji (w tym miejscu zakłada się, że *C:\temp\AzureSqlTable.json*). Tworzenie tabeli w usłudze ADF za pomocą następującego polecenia cmdlet programu Azure PowerShell:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json


## <a name="adf-pipeline"></a>Zdefiniuj, a następnie utwórz potok
Określ działania, które należą do potoku, a następnie utwórz potok z następującymi procedurami opartych na skryptach. Plik w formacie JSON jest używany do definiowania właściwości potoku.

* Skrypt założono, że **Nazwa potoku** jest *AMLDSProcessPipeline*.
* Należy również zauważyć, że możemy ustawić okresowości potoku wykonywane codziennie i używanie domyślny czas wykonywania zadania (00: 00 UTC).

> [!NOTE]
> Poniższych procedur, użyj programu Azure PowerShell do definiowania i instrukcje tworzenia potoku usługi ADF. Ale to zadanie może być również wykonywane przy użyciu witryny Azure portal. Aby uzyskać więcej informacji, zobacz [Utwórz potok](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

Korzystając z definicji tabeli, podane wcześniej, definicji potoku dla usługi ADF jest określony w następujący sposób:

```json
{
    "name": "AMLDSProcessPipeline",
    "properties":
    {
        "description" : "This pipeline has one Copy activity that copies data from an on-premises SQL to Azure blob",
        "activities":
        [
            {
                "name": "CopyFromSQLtoBlob",
                "description": "Copy data from on-premises SQL server to blob",
                "type": "CopyActivity",
                "inputs": [ {"name": "OnPremSQLTable"} ],
                "outputs": [ {"name": "OutputBlobTable"} ],
                "transformation":
                {
                    "source":
                    {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from nyctaxi_data"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "Policy":
                {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            },
            {
                "name": "CopyFromBlobtoSQLAzure",
                "description": "Push data to Sql Azure",
                "type": "CopyActivity",
                "inputs": [ {"name": "OutputBlobTable"} ],
                "outputs": [ {"name": "OutputSQLAzureTable"} ],
                "transformation":
                {
                    "source":
                    {
                        "type": "BlobSource"
                    },
                    "sink":
                    {
                        "type": "SqlSink",
                        "WriteBatchTimeout": "00:5:00",
                    }
                },
                "Policy":
                {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 2,
                    "timeout": "02:00:00"
                }
            }
        ]
    }
}
```

Kopii tej definicji JSON potoku do pliku o nazwie *pipelinedef.json* plik i zapisz go w znanej lokalizacji (w tym miejscu zakłada się, że *C:\temp\pipelinedef.json*). Tworzenie potoku w usłudze ADF za pomocą następującego polecenia cmdlet programu Azure PowerShell:

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json


## <a name="adf-pipeline-start"></a>Uruchamianie potoku
Teraz można uruchomić potok przy użyciu następującego polecenia:

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

*Startdate* i *enddate* wartości parametrów należy zamieniony rzeczywiste daty, między którymi chcesz uruchamianie potoku.

Po wykonaniu potok, należy wyświetlić dane wyświetlane w kontenerze, który został wybrany dla obiektu blob, jeden plik na dzień.

Należy pamiętać, że firma Microsoft ma nie wykorzystać funkcje udostępniane przez usługę ADF do potoku danych przyrostowe. Aby uzyskać więcej informacji na temat jak to zrobić, to i inne możliwości oferowane przez usługę ADF, zobacz [dokumentacji usługi ADF](https://azure.microsoft.com/services/data-factory/).
