---
title: Dane programu SQL Server do usługi SQL Azure z usługą Azure Data Factory — zespołu danych dla celów naukowych
description: Konfigurowanie potoku usługi ADF, który komponuje się dwa działania migracji danych, które przenoszą dane ze sobą codziennie między bazami danych w środowisku lokalnym i w chmurze.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 8f696f1c6c414cd9db082e79e0f34c56156e1ee0
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722496"
---
# <a name="move-data-from-an-on-premises-sql-server-to-sql-azure-with-azure-data-factory"></a>Przenoszenie danych z lokalnego programu SQL server do usługi SQL Azure z usługą Azure Data Factory

W tym artykule pokazano, jak przenieść dane z lokalnej bazy danych SQL Server do bazy danych SQL Azure za pośrednictwem platformy Azure Blob Storage przy użyciu Azure Data Factory (ADF): Ta metoda jest obsługiwanym starszym podejściem, które ma zalety zreplikowanej kopii przemieszczania, ale [sugerujemy zajrzeć na stronę migracji danych, aby uzyskać najnowsze opcje](https://datamigration.microsoft.com/scenario/sql-to-azuresqldb?step=1).

W przypadku tabeli podsumowującej różne opcje przenoszenia danych do Azure SQL Database, zobacz [przenoszenie danych do Azure SQL Database Azure Machine Learning](move-sql-azure.md).

## <a name="intro"></a>Wprowadzenie: co to jest ADF i kiedy należy go używać do migrowania danych?
Usługa Azure Data Factory to usługa integracji danych w chmurze w pełni zarządzana, która organizuje i automatyzuje operacje przenoszenia i przekształcania danych. Kluczową koncepcją w modelu usługi ADF jest potok. Potoki to logiczne grupy działań, z których każdy definiuje akcje do wykonania na danych zawartych w zestawach danych. Połączone usługi są używane do definiowania informacje wymagane przez usługę Data Factory, aby połączyć się z zasobami danych.

Za pomocą usługi ADF może być składana istniejących usług przetwarzania danych, potoki danych, które są wysoko dostępne i zarządzane w chmurze. Te błędy potoków danych mogą być planowane do pozyskiwania, przygotowywania, przekształcania, analizowanie i publikować dane i usługi ADF zarządza i organizuje złożonych danych i przetwarzania zależności. Rozwiązania można szybko skompilować i wdrożyć w chmurze, łączenie z coraz większej liczbie w środowisku lokalnym i źródeł danych w chmurze.

Rozważ użycie usługi ADF:

* gdy dane muszą stale migracji w scenariusza hybrydowego, który uzyskuje dostęp do obu — lokalnych i zasobów w chmurze
* gdy dane wymagają przekształceń lub podczas migrowania są do niej dodawane logiki biznesowej.

ADF umożliwia planowanie i monitorowanie zadań przy użyciu prostych skryptów JSON, które zarządzają przenoszenia danych w regularnych odstępach czasu. ADF ma także inne możliwości, takie jak obsługa złożonych operacji. Aby uzyskać więcej informacji na temat ADF, zapoznaj się z dokumentacją w [Azure Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/).

## <a name="scenario"></a>Scenariusz
Skonfigurujemy potoku usługi ADF, który komponuje się dwa działania migracji danych. Jednocześnie przenosimy dane codziennie między SQL Databaseami lokalnymi i Azure SQL Database w chmurze. Są dwa działania:

* Kopiowanie danych z lokalnej bazy danych programu SQL Server do konta usługi Azure Blob Storage
* Kopiowanie danych z konta usługi Azure Blob Storage do usługi Azure SQL Database.

> [!NOTE]
> Kroki przedstawione w tym miejscu zostały dostosowane z bardziej szczegółowego samouczka dostarczonego przez zespół usługi ADF: [Kopiowanie danych z lokalnej bazy danych SQL Server do usługi Azure Blob Storage](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal/) odwołania do odpowiednich sekcji tego tematu są udostępniane w razie potrzeby.
>
>

## <a name="prereqs"></a>Wymagania wstępne
W tym samouczku przyjęto założenie, że masz:

* **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Azure Storage**. Używasz konta usługi Azure storage do przechowywania danych w ramach tego samouczka. Jeśli nie masz konta usługi Azure Storage, zobacz artykuł [Tworzenie konta magazynu](../../storage/common/storage-account-create.md) . Po utworzeniu konta magazynu, należy uzyskać klucz konta, które umożliwiają dostęp do magazynu. Zobacz [Zarządzanie kluczami dostępu do konta magazynu](../../storage/common/storage-account-keys-manage.md).
* Dostęp do **Azure SQL Database**. Jeśli konieczne jest skonfigurowanie Azure SQL Database, temat [wprowadzenie z Microsoft Azure SQL Database](../../sql-database/sql-database-get-started.md) zawiera informacje na temat sposobu aprowizacji nowego wystąpienia Azure SQL Database.
* Zainstalowano i skonfigurowano **Azure PowerShell** lokalnie. Aby uzyskać instrukcje, zobacz [How to Install and configure Azure PowerShell](/powershell/azure/overview).

> [!NOTE]
> Ta procedura używa [Azure Portal](https://portal.azure.com/).
>
>

## <a name="upload-data"></a>Przekaż dane do lokalnego SQL Server
Używamy [zestawu danych NYC taksówki](https://chriswhong.com/open-data/foil_nyc_taxi/) w celu zademonstrowania procesu migracji. Zestaw danych NYC taksówki jest dostępny w tym wpisie w przypadku [danych o taksówkach](https://www.andresmh.com/nyctaxitrips/)w usłudze Azure Blob Storage NYC. Dane mają dwa pliki, plik trip_data.csv, który zawiera szczegóły podróży, i plik trip_far.csv, który zawiera szczegółowe informacje o klasie opłacony każdego podróży. Przykład i opis tych plików znajdują się w [opisie zestawu danych NYC taksówki](sql-walkthrough.md#dataset).

Możesz dostosować procedury w tym miejscu do zestawu danych użytkownika lub zgodnie z opisem przy użyciu zestawu danych taksówek NYC postępuj zgodnie z instrukcjami. Aby przekazać zestaw danych NYC taksówki do lokalnej bazy danych SQL Server, postępuj zgodnie z procedurą opisaną w temacie [zbiorcze Importowanie danych do SQL Server Database](sql-walkthrough.md#dbload). Te instrukcje są przeznaczone dla programu SQL Server na maszynie wirtualnej platformy Azure, ale procedura przekazywania do lokalnego programu SQL Server jest taka sama.

## <a name="create-adf"></a>Tworzenie Azure Data Factory
Poniżej [Azure Data Factory](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-data-factory)przedstawiono instrukcje dotyczące tworzenia nowych Azure Data Factory i grupy zasobów w [Azure Portal](https://portal.azure.com/) . Nazwij nowe wystąpienie ADF *adfdsp* i Nazwij grupę zasobów utworzoną *adfdsprg*.

## <a name="install-and-configure-azure-data-factory-integration-runtime"></a>Instalowanie i konfigurowanie usługi Azure Data Factory Integration Runtime
Integration Runtime to infrastruktura integracji danych zarządzana przez klienta używana przez program Azure Data Factory do zapewniania możliwości integracji danych w różnych środowiskach sieciowych. To środowisko wykonawcze był wcześniej nazywany "Brama zarządzania danymi".

Aby skonfigurować, [postępuj zgodnie z instrukcjami dotyczącymi tworzenia potoku](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal#create-a-pipeline)

## <a name="adflinkedservices"></a>Tworzenie połączonych usług w celu łączenia się z zasobami danych
Połączona usługa definiuje informacje wymagane przez usługę Azure Data Factory, aby nawiązać połączenie z zasobem danych. Mamy trzy zasoby w tym scenariuszu, dla którego połączone usługi są potrzebne:

1. Na lokalnym serwerze SQL Server
2. Azure Blob Storage
3. Azure SQL Database

Procedury krok po kroku dotyczące tworzenia połączonych usług są dostępne w temacie [Tworzenie połączonych usług](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).


## <a name="adf-tables"></a>Definiowanie i tworzenie tabel w celu określenia sposobu uzyskiwania dostępu do zestawów danych
Tworzenie tabel, które określają strukturę, lokalizacji i dostępność zestawów danych przy użyciu poniższych procedur opartych na skryptach. Pliki JSON służą do definiowania tabel. Aby uzyskać więcej informacji na temat struktury tych plików, zobacz [zestawy danych](../../data-factory/concepts-datasets-linked-services.md).

> [!NOTE]
> Przed uruchomieniem polecenia cmdlet [New-AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx) należy wykonać polecenie cmdlet `Add-AzureAccount`, aby upewnić się, że wybrano odpowiednią subskrypcję platformy Azure do wykonania polecenia. Aby uzyskać dokumentację tego polecenia cmdlet, zobacz [Add-AzureAccount](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-3.7.0).
>
>

Definicje opartych na formacie JSON w tabelach stosować następujących nazw:

* **Nazwa tabeli** w lokalnym programie SQL server jest *nyctaxi_data*
* **nazwa kontenera** na koncie usługi Azure Blob Storage to *ContainerName*

Trzy definicje tabel są wymagane przez ten potok ADF:

1. [Tabela lokalna SQL](#adf-table-onprem-sql)
2. [Tabela obiektów BLOB](#adf-table-blob-store)
3. [Tabela SQL Azure](#adf-table-azure-sql)

> [!NOTE]
> Te procedury użyj programu Azure PowerShell, definiować i tworzyć działania usługi ADF. Jednak te zadania może być również wykonywane przy użyciu witryny Azure portal. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie zestawów danych](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

### <a name="adf-table-onprem-sql"></a>Tabela lokalna SQL
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

Nazwy kolumn nie zostały uwzględnione w tym miejscu. W tym miejscu możesz wybrać nazwy kolumn, dołączając je tutaj (Aby uzyskać szczegółowe informacje, zapoznaj się z tematem [Dokumentacja usługi ADF](../../data-factory/copy-activity-overview.md) .

Skopiuj definicję JSON tabeli do pliku o nazwie *onpremtabledef. JSON* i Zapisz ją w znanej lokalizacji (przyjęto, że jest *C:\temp\onpremtabledef.JSON*). Tworzenie tabeli w usłudze ADF za pomocą następującego polecenia cmdlet programu Azure PowerShell:

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


### <a name="adf-table-blob-store"></a>Tabela obiektów BLOB
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

Skopiuj definicję JSON tabeli do pliku o nazwie *bloboutputtabledef. JSON* i Zapisz ją w znanej lokalizacji (przyjęto, że jest *C:\temp\bloboutputtabledef.JSON*). Tworzenie tabeli w usłudze ADF za pomocą następującego polecenia cmdlet programu Azure PowerShell:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json

### <a name="adf-table-azure-sql"></a>Tabela SQL Azure
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

Skopiuj definicję JSON tabeli do pliku o nazwie *wartość azuresqltable. JSON* i Zapisz ją w znanej lokalizacji (przyjęto, że jest *C:\temp\AzureSqlTable.JSON*). Tworzenie tabeli w usłudze ADF za pomocą następującego polecenia cmdlet programu Azure PowerShell:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json


## <a name="adf-pipeline"></a>Definiowanie i tworzenie potoku
Określ działania, które należą do potoku, a następnie utwórz potok z następującymi procedurami opartych na skryptach. Plik w formacie JSON jest używany do definiowania właściwości potoku.

* Skrypt zakłada, że **Nazwa potoku** to *AMLDSProcessPipeline*.
* Należy również zauważyć, że możemy ustawić okresowości potoku wykonywane codziennie i używanie domyślny czas wykonywania zadania (00: 00 UTC).

> [!NOTE]
> Poniższych procedur, użyj programu Azure PowerShell do definiowania i instrukcje tworzenia potoku usługi ADF. Ale to zadanie może być również wykonywane przy użyciu witryny Azure portal. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie potoku](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
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

Skopiuj tę definicję JSON potoku do pliku o nazwie *pipelinedef. JSON* i Zapisz ją w znanej lokalizacji (przyjęto, że jest *C:\temp\pipelinedef.JSON*). Tworzenie potoku w usłudze ADF za pomocą następującego polecenia cmdlet programu Azure PowerShell:

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json


## <a name="adf-pipeline-start"></a>Uruchamianie potoku
Teraz można uruchomić potok przy użyciu następującego polecenia:

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

Wartości parametrów *StartDate* i *EndDate* muszą zostać zastąpione rzeczywistymi datami, między którymi ma zostać uruchomiony potok.

Po wykonaniu potok, należy wyświetlić dane wyświetlane w kontenerze, który został wybrany dla obiektu blob, jeden plik na dzień.

Nie wykorzystano funkcji zapewnianych przez funkcję ADF w przyrostowym potoku danych. Aby uzyskać więcej informacji na temat tego, jak to zrobić, i innych możliwości zapewnianych przez funkcję ADF, zobacz [dokumentację ADF](https://azure.microsoft.com/services/data-factory/).
