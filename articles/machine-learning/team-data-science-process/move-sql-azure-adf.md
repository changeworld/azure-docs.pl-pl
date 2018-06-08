---
title: Przenoszenie danych z lokalnego programu SQL Server SQL Azure z fabryką danych Azure | Dokumentacja firmy Microsoft
description: Skonfiguruj potok fabryki danych AZURE, który Redaguj dwa działania migracji danych, które razem przenoszenia danych w trybie dziennym między bazami danych lokalnych i w chmurze.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: 36837c83-2015-48be-b850-c4346aa5ae8a
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: e9f6de3d4f4f731c2e727889bef1aef129cb00bf
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838105"
---
# <a name="move-data-from-an-on-premises-sql-server-to-sql-azure-with-azure-data-factory"></a>Przenoszenie danych z lokalnego programu SQL server SQL Azure z fabryką danych Azure
W tym temacie pokazano, jak przenieść dane z lokalną bazą danych serwera SQL z bazą danych SQL Azure za pomocą usługi Azure Blob Storage przy użyciu fabryki danych Azure (ADF).

Dla tabeli, która zawiera podsumowanie różne opcje przenoszenia danych do bazy danych SQL Azure, zobacz [przenieść dane do bazy danych SQL Azure dla usługi Azure Machine Learning](move-sql-azure.md).

## <a name="intro"></a>Wprowadzenie: Co to jest ADF i kiedy należy go użyć do migracji danych?
Fabryka danych Azure to usługa integracji pełni zarządzanych danych oparte na chmurze, która organizuje i automatyzuje przepływu i przekształcania danych. Koncepcja klucza w modelu ADF jest potok. Potok to logiczne grupowanie działań, z których każdy definiuje akcje do wykonania na dane zawarte w zestawach danych. Połączone usługi są używane do definiowania informacje wymagane dla fabryki danych do łączenia się z zasobami danych.

Z fabryki danych AZURE istniejące usługi przetwarzania danych mogą być składane w potokach danych, które są wysokiej dostępności i zarządzane w chmurze. Potoki tych danych można zaplanować pozyskiwania, przygotowywanie, przekształcanie, analizowanie i publikować dane, a ADF zarządza i organizuje dane złożone i przetwarzania zależności. Rozwiązania można szybko wbudowane i wdrażać w chmurze, łączenie coraz lokalnie i w chmurze źródeł danych.

Należy rozważyć użycie ADF:

* gdy dane muszą być stale migracji w scenariusza hybrydowego, który uzyskuje dostęp do obu lokalnych i dostępnych zasobów w chmurze
* Jeśli dane jest nietransakcyjnego lub musi być modyfikowany lub ma do niego dodana gdy migrowane logiki biznesowej.

Fabryki danych AZURE umożliwia planowanie i monitorowania zadań przy użyciu prostych skryptów JSON, które zarządzają przepływu danych w regularnych odstępach czasu. ADF ma również innych funkcji, takich jak obsługa złożonych operacji. Aby uzyskać więcej informacji dotyczących fabryki danych AZURE, zobacz dokumentację w [fabryki danych Azure (ADF)](https://azure.microsoft.com/services/data-factory/).

## <a name="scenario"></a>Scenariusz
Skonfigurowanie potok fabryki danych AZURE, który Redaguj dwóch działań migracji danych. Razem ich przenoszenia danych w trybie dziennym między lokalną bazą danych SQL i bazy danych SQL Azure w chmurze. Są dwa działania:

* Kopiowanie danych z lokalną bazą danych programu SQL Server do konta magazynu obiektów Blob Azure
* Skopiuj dane z konta magazynu obiektów Blob Azure do bazy danych SQL Azure.

> [!NOTE]
> Kroki opisane w tym miejscu zostały dostosowane z bardziej szczegółowy samouczek udostępnione przez zespół ADF: [przenoszenie danych między lokalnych źródeł i w chmurze z brama zarządzania danymi](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md) odwołań do odpowiednich sekcji tego tematu znajdują się w stosownych.
>
>

## <a name="prereqs"></a>Wymagania wstępne
Ten samouczek zakłada, że masz:

* **Subskrypcji platformy Azure**. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* **Konto magazynu Azure**. Używasz konta magazynu Azure do przechowywania danych w tym samouczku. Jeśli nie masz konta magazynu platformy Azure, zobacz [Utwórz konto magazynu](../../storage/common/storage-create-storage-account.md#create-a-storage-account) artykułu. Po utworzeniu konta magazynu, należy uzyskać klucz konto umożliwia dostęp do magazynu. Zobacz [zarządzanie kluczami dostępu do magazynu](../../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys).
* Dostęp do **baza danych Azure SQL**. Jeśli należy skonfigurować bazy danych SQL Azure, temat [wprowadzenie do korzystania z bazy danych SQL Azure Microsoft ](../../sql-database/sql-database-get-started.md) zawiera informacje o tym, jak zainicjować nowe wystąpienie bazy danych SQL Azure.
* Zainstalowano i skonfigurowano **programu Azure PowerShell** lokalnie. Aby uzyskać instrukcje, zobacz [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

> [!NOTE]
> Ta procedura wykorzystuje [portalu Azure](https://portal.azure.com/).
>
>

## <a name="upload-data"></a> Przekazywanie danych do lokalnego serwera SQL
Używamy [dataset taksówki NYC](http://chriswhong.com/open-data/foil_nyc_taxi/) aby zademonstrować procesu migracji. Taksówki NYC zestawu danych jest dostępna, zgodnie z opisem w tym blogu w magazynie obiektów blob platformy Azure [danych taksówki NYC](http://www.andresmh.com/nyctaxitrips/). Dane mają dwóch plików, pliku trip_data.csv, który zawiera szczegóły podróży, i pliku trip_far.csv, który zawiera szczegółowe informacje o klasie płatnej w odniesieniu do każdej podróży. Przykładowe i opis te pliki znajdują się w [opis zestawu danych rund taksówki NYC](sql-walkthrough.md#dataset).

Można dostosować procedury podaną poniżej do zestawu danych użytkownika lub wykonaj kroki opisane przy użyciu taksówki NYC zestawu danych. Aby przekazać taksówki NYC zestawu danych do lokalnej bazy danych programu SQL Server, wykonaj procedurę opisaną w [zbiorczego importowania danych do bazy danych serwera SQL](sql-walkthrough.md#dbload). Te instrukcje dotyczą programu SQL Server na maszynie wirtualnej platformy Azure, ale procedura przekazywania do lokalnego serwera SQL jest taka sama.

## <a name="create-adf"></a> Tworzenie fabryki danych Azure
Instrukcje dotyczące tworzenia nowych fabryki danych Azure i grupy zasobów w [portalu Azure](https://portal.azure.com/) podano [tworzenie fabryki danych Azure](../../data-factory/v1/data-factory-build-your-first-pipeline-using-editor.md#create-a-data-factory). Nazwa nowego wystąpienia ADF *adfdsp* i nazwę grupy zasobów utworzonej *adfdsprg*.

## <a name="install-and-configure-up-the-data-management-gateway"></a>Instalowanie i konfigurowanie się brama zarządzania danymi
Aby włączyć Twojej potoki w fabryce danych Azure do pracy z lokalnym programem SQL Server, należy go dodać jako połączonej usługi z fabryką danych. Aby utworzyć połączonej usługi dla lokalnego programu SQL Server, należy:

* Pobierz i zainstaluj bramę zarządzania danymi firmy Microsoft na komputerze lokalnym.
* Skonfiguruj połączonej usługi dla źródła danych lokalnych użyć bramy.

Brama zarządzania danymi serializuje i deserializuje źródłowy i odbiorczy dane na komputerze, na którym jest hostowany.

Aby uzyskać instrukcje dotyczące konfiguracji oraz szczegółowe informacje w bramie zarządzania danymi, zobacz [przenoszenie danych między lokalnych źródeł i w chmurze z brama zarządzania danymi](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md)

## <a name="adflinkedservices"></a>Utwórz połączone usługi, aby połączyć się z zasobami danych
Połączona usługa definiuje informacje wymagane dla fabryki danych Azure połączyć się z zasobem danych. Firma Microsoft udostępnia trzy zasoby w tym scenariuszu, dla których wymagane są połączone usługi:

1. Lokalny serwer SQL
2. Azure Blob Storage
3. Baza danych SQL Azure

Krok po kroku procedury tworzenia połączonych usług znajduje się w [utworzenia połączonych usług](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md#create-linked-services).


## <a name="adf-tables"></a>Definiowanie i tworzenie tabel, aby określić sposób uzyskać dostępu do zestawów danych
Tworzenie tabel, które określają strukturę, lokalizacji i dostępności zestawy danych z następującymi procedurami opartych na skryptach. Pliki w formacie JSON są używane do definiowania tabel. Aby uzyskać więcej informacji o strukturze tych plików, zobacz [zestawów danych](../../data-factory/v1/data-factory-create-datasets.md).

> [!NOTE]
> Powinien zostać wykonany `Add-AzureAccount` polecenia cmdlet przed wykonaniem [AzureDataFactoryTable nowy](https://msdn.microsoft.com/library/azure/dn835096.aspx) polecenia cmdlet, aby upewnić się, że prawo Azure subskrypcji został wybrany do wykonania polecenia. Aby uzyskać dokumentację tego polecenia cmdlet, zobacz [Add-AzureAccount](/powershell/module/azure/add-azureaccount?view=azuresmps-3.7.0).
>
>

Definicje opartych na formacie JSON w tabelach stosować następujących nazw:

* **nazwy tabeli** jest serwer SQL lokalnymi *nyctaxi_data*
* **nazwa kontenera** w magazynie obiektów Blob Azure jest konto *containername*  

Trzy definicje tabel są wymagane dla tego potoku ADF:

1. [SQL lokalnej tabeli](#adf-table-onprem-sql)
2. [Tabela obiektów blob ](#adf-table-blob-store)
3. [SQL tabeli platformy Azure](#adf-table-azure-sql)

> [!NOTE]
> Te procedury użyciu programu Azure PowerShell do definiowania i tworzenia działań ADF. Jednak te zadania mogą być również wykonywane przy użyciu portalu Azure. Aby uzyskać więcej informacji, zobacz [utworzyć zestawy danych](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md#create-datasets).
>
>

### <a name="adf-table-onprem-sql"></a>SQL lokalnej tabeli
Definicja tabeli dla lokalnego programu SQL Server jest określona w następującym pliku JSON:

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

Nazwy kolumn nie zostały uwzględnione w tym miejscu. Można wybierać podrzędnych na nazwy kolumn przez włączenie ich w tym miejscu (Aby uzyskać szczegółowe informacje, sprawdź [dokumentacji ADF](../../data-factory/v1/data-factory-data-movement-activities.md) tematu.

Kopiuj do pliku definicji JSON tabeli o nazwie *onpremtabledef.json* pliku i zapisz go w znanej lokalizacji (w tym miejscu zakłada się, że *C:\temp\onpremtabledef.json*). Tworzenie tabeli w ADF za pomocą następującego polecenia cmdlet programu Azure PowerShell:

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


### <a name="adf-table-blob-store"></a>Tabela obiektów blob
Definicja tabeli dla lokalizacji obiektu blob danych wyjściowych jest w następującej (mapuje pozyskiwane danych z lokalnego do obiektów blob platformy Azure):

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

Kopiuj do pliku definicji JSON tabeli o nazwie *bloboutputtabledef.json* pliku i zapisz go w znanej lokalizacji (w tym miejscu zakłada się, że *C:\temp\bloboutputtabledef.json*). Tworzenie tabeli w ADF za pomocą następującego polecenia cmdlet programu Azure PowerShell:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json  

### <a name="adf-table-azure-sql"></a>SQL tabeli platformy Azure
Definicja tabeli SQL Azure jest poniżej (w tym schemacie mapowania danych z obiektu blob) wyjściowy:

    {
        "name": "OutputSQLAzureTable",
        "properties":
        {
            "structure":
            [
                { "name": "column1", type": "String"},
                { "name": "column2", type": "String"}                
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

Kopiuj do pliku definicji JSON tabeli o nazwie *AzureSqlTable.json* pliku i zapisz go w znanej lokalizacji (w tym miejscu zakłada się, że *C:\temp\AzureSqlTable.json*). Tworzenie tabeli w ADF za pomocą następującego polecenia cmdlet programu Azure PowerShell:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json  


## <a name="adf-pipeline"></a>Definiowanie i utworzyć potoku
Określ działania, które należą do potoku i utworzyć proces z następującymi procedurami opartych na skryptach. Plik JSON służy do definiowania właściwości potoku.

* Skrypt przy założeniu, że **nazwy potoku** jest *AMLDSProcessPipeline*.
* Należy również zauważyć, że ustawiliśmy okresowości potoku można wykonać na codziennie i użycie domyślny czas wykonywania zadania (00: 00 UTC).

> [!NOTE]
> Poniższe procedury użyciu programu Azure PowerShell do definiowania i utworzyć potok fabryki danych AZURE. Jednak to zadanie może być również wykonywane przy użyciu portalu Azure. Aby uzyskać więcej informacji, zobacz [tworzenie potoku](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md#create-pipeline).
>
>

Za pomocą definicji tabeli podano wcześniej, definicji potoku ADF wygląda następująco:

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

Kopię tej definicji JSON potoku do pliku o nazwie *pipelinedef.json* pliku i zapisz go w znanej lokalizacji (w tym miejscu zakłada się, że *C:\temp\pipelinedef.json*). Tworzenie potoku w ADF za pomocą następującego polecenia cmdlet programu Azure PowerShell:

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json


## <a name="adf-pipeline-start"></a>Uruchom potoku
Obecnie można uruchomić potoku za pomocą następującego polecenia:

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

*Data_rozpoczęcia* i *enddate* wartości parametrów trzeba można zastąpić rzeczywistej daty, między którymi ma potoku do uruchamiania.

Gdy jest wykonywana potoku, należy wyświetlić dane wyświetlane w kontenerze wybranym dla obiekt blob, jeden plik na dzień.

Należy pamiętać, że firma Microsoft ma nie wykorzystać funkcje udostępniane przez ADF przyrostowo danych potoku. Aby uzyskać więcej informacji na temat tego i innych możliwości oferowane przez fabryki danych AZURE, zobacz [dokumentacji ADF](https://azure.microsoft.com/services/data-factory/).
