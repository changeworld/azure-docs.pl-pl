---
title: Dane programu SQL Server do platformy SQL Azure z usługą Azure Data Factory — proces nauki danych zespołowych
description: Skonfiguruj potok ADF, który komponuje dwa działania związane z migracją danych, które codziennie przenoszą dane między bazami danych lokalnie i w chmurze.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722496"
---
# <a name="move-data-from-an-on-premises-sql-server-to-sql-azure-with-azure-data-factory"></a>Przenoszenie danych z lokalnego serwera SQL do platformy SQL Azure za pomocą usługi Azure Data Factory

W tym artykule pokazano, jak przenieść dane z lokalnej bazy danych programu SQL Server do bazy danych SQL Azure Database za pośrednictwem usługi Azure Blob Storage przy użyciu usługi Azure Data Factory (ADF): ta metoda jest obsługiwanym podejściem starszej wersji, które ma zalety replikowanej kopii przemieszczania, chociaż [sugerujemy, aby spojrzeć na naszą stronę migracji danych dla najnowszych opcji.](https://datamigration.microsoft.com/scenario/sql-to-azuresqldb?step=1)

Aby uzyskać tabelę, która podsumowuje różne opcje przenoszenia danych do bazy danych SQL Azure, zobacz [Przenoszenie danych do bazy danych SQL Azure dla usługi Azure Machine Learning](move-sql-azure.md).

## <a name="introduction-what-is-adf-and-when-should-it-be-used-to-migrate-data"></a><a name="intro"></a>Wprowadzenie: Co to jest ADF i kiedy powinien być używany do migracji danych?
Usługa Azure Data Factory to w pełni zarządzana usługa integracji danych oparta na chmurze, która organizuje i automatyzuje przenoszenie i przekształcanie danych. Kluczową koncepcją w modelu ADF jest potok. Potok jest logiczną grupą działań, z których każda definiuje akcje do wykonania na danych zawartych w zestawach danych. Połączone usługi są używane do definiowania informacji potrzebnych do połączenia się z zasobami danych w układzie danych.

Dzięki usłudze ADF istniejące usługi przetwarzania danych mogą składać się z potoków danych, które są wysoce dostępne i zarządzane w chmurze. Te potoki danych można zaplanować w celu pozyskiwania, przygotowywania, przekształcania, analizowania i publikowania danych, a usługa ADF zarządza złożonymi danymi i jego zależnościami oraz aranżuje je. Rozwiązania można szybko sbudować i wdrożyć w chmurze, łącząc rosnącą liczbę lokalnych i chmurowych źródeł danych.

Rozważ użycie podajnika ADF:

* gdy dane muszą być stale migrowane w scenariuszu hybrydowym, który uzyskuje dostęp zarówno do zasobów lokalnych, jak i w chmurze
* gdy dane wymagają przekształceń lub logiki biznesowej dodane do niego podczas migracji.

Usługa ADF umożliwia planowanie i monitorowanie zadań przy użyciu prostych skryptów JSON, które zarządzają przepływem danych okresowo. ADF ma również inne możliwości, takie jak obsługa złożonych operacji. Aby uzyskać więcej informacji na temat usługi ADF, zobacz dokumentację usługi [Azure Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/).

## <a name="the-scenario"></a><a name="scenario"></a>Scenariusz
Firma Asfigurujemy potok ADF, który składa dwie działania związane z migracją danych. Razem przenoszą dane codziennie między lokalną bazą danych SQL a bazą danych SQL Azure w chmurze. Te dwa działania to:

* kopiowanie danych z lokalnej bazy danych programu SQL Server na konto usługi Azure Blob Storage
* kopiowanie danych z konta usługi Azure Blob Storage do bazy danych SQL Azure.

> [!NOTE]
> Kroki pokazane w tym miejscu zostały dostosowane z bardziej szczegółowego samouczka dostarczonego przez zespół usługi ADF: [Kopiuj dane z lokalnej bazy danych programu SQL Server do magazynu obiektów Blob platformy Azure](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal/) Odwołania do odpowiednich sekcji tego tematu są dostarczane w razie potrzeby.
>
>

## <a name="prerequisites"></a><a name="prereqs"></a>Wymagania wstępne
W tym samouczku założono, że masz:

* **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* **Konto magazynu platformy Azure**. Do przechowywania danych w tym samouczku używasz konta magazynu platformy Azure. Jeśli nie masz konta magazynu platformy Azure, zobacz artykuł [Tworzenie konta magazynu.](../../storage/common/storage-account-create.md) Po utworzeniu konta magazynu należy uzyskać klucz konta używany do uzyskiwania dostępu do magazynu. Zobacz [Zarządzanie kluczami dostępu do konta magazynu](../../storage/common/storage-account-keys-manage.md).
* Dostęp do **bazy danych SQL azure**. Jeśli musisz skonfigurować bazę danych SQL Azure, temat [Wprowadzenie do bazy danych SQL platformy Microsoft Azure](../../sql-database/sql-database-get-started.md) zawiera informacje na temat sposobu aprowizowania nowego wystąpienia bazy danych SQL azure.
* Zainstalowano i skonfigurowano **program Azure PowerShell** lokalnie. Aby uzyskać instrukcje, zobacz [Jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview).

> [!NOTE]
> Ta procedura korzysta z [witryny Azure portal](https://portal.azure.com/).
>
>

## <a name="upload-the-data-to-your-on-premises-sql-server"></a><a name="upload-data"></a>Przekazywanie danych do lokalnego programu SQL Server
Używamy [zestawu danych taksówki NYC,](https://chriswhong.com/open-data/foil_nyc_taxi/) aby zademonstrować proces migracji. Zestaw danych taksówki NYC jest dostępny, jak wspomniano w tym poście, na platformie Azure blob storage [NYC Taxi Data](https://www.andresmh.com/nyctaxitrips/). Dane zawierają dwa pliki: trip_data.csv, który zawiera szczegóły podróży, oraz plik trip_far.csv, który zawiera szczegóły taryfy zapłaconej za każdą podróż. Przykład i opis tych plików znajdują się w [NYC Taxi Trips Dataset Description](sql-walkthrough.md#dataset).

Można dostosować procedurę podana w tym miejscu do zestawu własnych danych lub wykonać kroki opisane przy użyciu zestawu danych taksówki NYC. Aby przekazać zestaw danych taksówki NYC do lokalnej bazy danych programu SQL Server, postępuj zgodnie z procedurą opisaną w [zbiorczej bazie danych importu do bazy danych programu SQL Server](sql-walkthrough.md#dbload). Te instrukcje są dla programu SQL Server na maszynie wirtualnej platformy Azure, ale procedura przekazywania do lokalnego programu SQL Server jest taka sama.

## <a name="create-an-azure-data-factory"></a><a name="create-adf"></a>Tworzenie fabryki danych platformy Azure
Instrukcje dotyczące tworzenia nowej fabryki danych platformy Azure i grupy zasobów w [witrynie Azure portal](https://portal.azure.com/) są dostarczane [Tworzenie fabryki danych platformy Azure.](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-data-factory) Nazwij nowe *wystąpienie ADF adfdsp* i nazwij grupę zasobów *utworzoną adfdsprg*.

## <a name="install-and-configure-azure-data-factory-integration-runtime"></a>Instalowanie i konfigurowanie środowiska wykonawczego integracji usługi Azure Data Factory Integration
Środowisko wykonawcze integracji to infrastruktura integracji danych zarządzana przez klienta używana przez usługę Azure Data Factory w celu zapewnienia możliwości integracji danych w różnych środowiskach sieciowych. To środowisko wykonawcze było wcześniej nazywane "bramą zarządzania danymi".

Aby skonfigurować, [postępuj zgodnie z instrukcjami dotyczącymi tworzenia potoku](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal#create-a-pipeline)

## <a name="create-linked-services-to-connect-to-the-data-resources"></a><a name="adflinkedservices"></a>Tworzenie połączonych usług w celu łączenia się z zasobami danych
Połączona usługa definiuje informacje potrzebne do połączenia usługi Azure Data Factory z zasobem danych. W tym scenariuszu mamy trzy zasoby, dla których potrzebne są połączone usługi:

1. Lokalny program SQL Server
2. Azure Blob Storage
3. Azure SQL Database

Procedura krok po kroku tworzenia połączonych usług jest dostępna w [temacie Tworzenie usług połączonych](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).


## <a name="define-and-create-tables-to-specify-how-to-access-the-datasets"></a><a name="adf-tables"></a>Definiowanie i tworzenie tabel w celu określenia sposobu uzyskiwania dostępu do zestawów danych
Utwórz tabele określające strukturę, lokalizację i dostępność zestawów danych za pomocą następujących procedur opartych na skryptach. Pliki JSON służą do definiowania tabel. Aby uzyskać więcej informacji na temat struktury tych plików, zobacz [Zestawy danych](../../data-factory/concepts-datasets-linked-services.md).

> [!NOTE]
> Należy wykonać `Add-AzureAccount` polecenie cmdlet przed wykonaniem [polecenia new-AzureDataFactoryTable,](https://msdn.microsoft.com/library/azure/dn835096.aspx) aby potwierdzić, że do wykonania polecenia wybrano odpowiednią subskrypcję platformy Azure. Aby uzyskać dokumentację tego polecenia cmdlet, zobacz [Add-AzureAccount](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-3.7.0).
>
>

Definicje oparte na JSON w tabelach używają następujących nazw:

* **nazwa tabeli** w lokalnym serwerze SQL jest *nyctaxi_data*
* **nazwa kontenera** na koncie usługi Azure Blob Storage to *nazwa kontenera*

Dla tego potoku ADF potrzebne są trzy definicje tabel:

1. [Tabela lokalna SQL](#adf-table-onprem-sql)
2. [Tabela obiektów blob](#adf-table-blob-store)
3. [Tabela sql azure](#adf-table-azure-sql)

> [!NOTE]
> Te procedury używają programu Azure PowerShell do definiowania i tworzenia działań usługi ADF. Ale te zadania można również wykonać za pomocą witryny Azure portal. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie zestawów danych](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

### <a name="sql-on-premises-table"></a><a name="adf-table-onprem-sql"></a>Tabela lokalna SQL
Definicja tabeli dla lokalnego programu SQL Server jest określona w następującym pliku JSON:

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

Nazwy kolumn nie zostały uwzględnione w tym miejscu. Można podnaprojektować nazwy kolumn, dołączając je tutaj (szczegółowe informacje można znaleźć w temacie [dokumentacji podabu.](../../data-factory/copy-activity-overview.md)

Skopiuj definicję JSON tabeli do pliku o nazwie *plik onpremtabledef.json* i zapisz ją w znanej lokalizacji (w tym przypadku przyjęto, że jest *to C:\temp\onpremtabledef.json*). Utwórz tabelę w usłudze ADF przy następującym policzeniu cmdlet programu Azure PowerShell:

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


### <a name="blob-table"></a><a name="adf-table-blob-store"></a>Tabela obiektów blob
Definicja tabeli dla lokalizacji wyjściowego obiektu blob jest w następujący sposób (ta mapuje przyjmowane dane z lokalnego obiektu blob platformy Azure):

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

Skopiuj definicję JSON tabeli do pliku o nazwie *bloboutputtabledef.json* i zapisz ją w znanej lokalizacji (w tym przypadku przyjęto, że jest *to C:\temp\bloboutputtabledef.json*). Utwórz tabelę w usłudze ADF przy następującym policzeniu cmdlet programu Azure PowerShell:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json

### <a name="sql-azure-table"></a><a name="adf-table-azure-sql"></a>Tabela sql azure
Definicja tabeli dla danych wyjściowych platformy SQL Azure jest w następujący sposób (ten schemat mapuje dane pochodzące z obiektu blob):

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

Skopiuj definicję JSON tabeli do pliku o nazwie *Plik AzureSqlTable.json* i zapisz ją w znanej lokalizacji (w tym przypadku przyjęto, że jest *to C:\temp\AzureSqlTable.json).* Utwórz tabelę w usłudze ADF przy następującym policzeniu cmdlet programu Azure PowerShell:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json


## <a name="define-and-create-the-pipeline"></a><a name="adf-pipeline"></a>Definiowanie i tworzenie potoku
Określ działania, które należą do potoku i utwórz potok z następującymi procedurami opartymi na skrypcie. Plik JSON służy do definiowania właściwości potoku.

* Skrypt zakłada, że **nazwa potoku** jest *AMLDSProcessPipeline*.
* Należy również zauważyć, że możemy ustawić okresowość potoku, które mają być wykonywane codziennie i użyć domyślnego czasu wykonywania dla zadania (12 am UTC).

> [!NOTE]
> Poniższe procedury używają programu Azure PowerShell do definiowania i tworzenia potoku usługi ADF. Ale to zadanie można również wykonać za pomocą witryny Azure portal. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie potoku](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

Przy użyciu definicji tabel dostarczonych wcześniej, definicja potoku dla podajnika ADF jest określona w następujący sposób:

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

Skopiuj tę definicję JSON potoku do pliku o nazwie *plik pipelinedef.json* i zapisz go w znanej lokalizacji (w tym przypadku przyjęto, że jest *to C:\temp\pipelinedef.json*). Utwórz potok w usłudze ADF przy następującym powiększe polecenia cmdlet usługi Azure PowerShell:

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json


## <a name="start-the-pipeline"></a><a name="adf-pipeline-start"></a>Uruchamianie potoku
Potok można teraz uruchomić za pomocą następującego polecenia:

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

Wartości *parametrów data początkowa* i *końcowa* należy zastąpić rzeczywistymi datami, między którymi ma zostać uruchomiony potok.

Po wykonaniu potoku, powinny być widoczne dane pojawiają się w kontenerze wybranym dla obiektu blob, jeden plik dziennie.

Nie wykorzystaliśmy funkcji oferowanych przez podajnik ADF do tworzenia danych stopniowo. Aby uzyskać więcej informacji na temat tego i innych funkcji oferowanych przez adf, zobacz [dokumentację ADF](https://azure.microsoft.com/services/data-factory/).
