---
title: Importowanie danych do użycia z interfejsu API Azure rozwiązania Cosmos DB tabeli | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zaimportować dane do użycia z interfejsu API Azure rozwiązania Cosmos bazy danych tabeli.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: b60743e2-0227-43ab-965a-0ae3ebacd917
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: sngun
ms.openlocfilehash: 7a8eaaaaa8bbfa8cf9397ce52dfa93d71b0df30c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="import-data-for-use-with-the-azure-cosmos-db-table-api"></a>Importowanie danych do użycia z interfejsu API Azure rozwiązania Cosmos DB tabeli

Ten samouczek zawiera instrukcje dotyczące importowania danych do użycia z programem Azure DB rozwiązania Cosmos [API tabeli](table-introduction.md). Jeśli masz dane przechowywane w magazynie tabel Azure, można użyć narzędzia migracji danych albo AzCopy do zaimportowania danych. Jeśli masz dane przechowywane na koncie Azure rozwiązania Cosmos tabeli bazy danych interfejsu API (wersja zapoznawcza), należy użyć narzędzia migracji danych, aby przeprowadzić migrację danych. Po zaimportowaniu danych będzie mógł korzystać z funkcji premium oferty Azure DB rozwiązania Cosmos, takich jak gotowe dystrybucji globalnych, dedykowanych przepływności, jednocyfrowej milisekundy opóźnienia w 99-ty percentyl, zagwarantować wysokiej dostępności i dodatkowej automatycznego indeksowania.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Importowanie danych za pomocą narzędzia migracji danych
> * Importowanie danych z narzędzia AzCopy
> * Migrowanie z tabeli interfejsu API (wersja zapoznawcza) do tabeli interfejsu API 

## <a name="data-migration-tool"></a>Narzędzie migracji danych

Narzędzie wiersza polecenia migracji danych DB rozwiązania Cosmos Azure (dt.exe) może służyć do zaimportowania istniejących danych magazynu tabel Azure z kontem API tabeli GA lub migracji danych z konta tabeli interfejsu API (wersja zapoznawcza) w tabeli API GA konto. Inne źródła nie są obecnie obsługiwane. Migracja danych na podstawie interfejsu użytkownika narzędzia (dtui.exe) nie jest obsługiwana dla kont tabeli interfejsu API. 

Aby przeprowadzić migrację danych z tabeli, należy wykonać następujące zadania:

1. Pobierz narzędzie do migracji z [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool).
2. Uruchom `dt.exe` przy użyciu argumentów wiersza polecenia dla danego scenariusza.

DT.exe wykonuje polecenie w następującym formacie:

    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 

Dostępne są następujące opcje dla polecenia:

    /ErrorLog: Optional. Name of the CSV file to redirect data transfer failures
    /OverwriteErrorLog: Optional. Overwrite error log file
    /ProgressUpdateInterval: Optional, default is 00:00:01. Time interval to refresh on-screen data transfer progress
    /ErrorDetails: Optional, default is None. Specifies that detailed error information should be displayed for the following errors: None, Critical, All

### <a name="command-line-source-settings"></a>Ustawienia źródła wiersza polecenia

Podczas definiowania Podgląd Azure Table Storage lub interfejsu API tabeli jako źródło migracji, należy użyć następujących opcji źródła.

    /s:AzureTable: Reads data from Azure Table storage
    /s.ConnectionString: Connection string for the table endpoint. This can be retrieved from the Azure portal
    /s.LocationMode: Optional, default is PrimaryOnly. Specifies which location mode to use when connecting to Azure Table storage: PrimaryOnly, PrimaryThenSecondary, SecondaryOnly, SecondaryThenPrimary
    /s.Table: Name of the Azure Table
    /s.InternalFields: Set to All for table migration as RowKey and PartitionKey are required for import.
    /s.Filter: Optional. Filter string to apply
    /s.Projection: Optional. List of columns to select

Aby pobrać parametry połączenia źródła podczas importowania z magazynem tabel Azure, otwórz Azure portal, a następnie kliknij przycisk **kont magazynu** > **konta**  >   **Klucze dostępu**, a następnie użyj przycisku kopiowania, aby skopiować **ciąg połączenia**.

![Zrzut ekranu HBase Opcje źródła](./media/table-import/storage-table-access-key.png)

Aby pobrać parametry połączenia źródła podczas importowania z konta Azure rozwiązania Cosmos tabeli bazy danych interfejsu API (wersja zapoznawcza), otwórz Azure portalu, kliknij przycisk **bazy danych Azure rozwiązania Cosmos** > **konta**  >  **Ciąg połączenia** i użyj przycisku kopiowania, aby skopiować **ciąg połączenia**.

![Zrzut ekranu HBase Opcje źródła](./media/table-import/cosmos-connection-string.png)

[Przykład polecenia Azure Table Storage](#azure-table-storage)

[Przykład polecenia Azure rozwiązania Cosmos tabeli bazy danych interfejsu API (wersja zapoznawcza)](#table-api-preview)

### <a name="command-line-target-settings"></a>Ustawienia obiektu docelowego wiersza polecenia

Podczas definiowania interfejsu API Azure rozwiązania Cosmos DB tabeli jako miejsce docelowe migracji, należy użyć następujących opcji docelowej.

    /t:TableAPIBulk: Uploads data into Azure CosmosDB Table in batches
    /t.ConnectionString: Connection string for the table endpoint
    /t.TableName: Specifies the name of the table to write to
    /t.Overwrite: Optional, default is false. Specifies if existing values should be overwritten
    /t.MaxInputBufferSize: Optional, default is 1GB. Approximate estimate of input bytes to buffer before flushing data to sink
    /t.Throughput: Optional, service defaults if not specified. Specifies throughput to configure for table
    /t.MaxBatchSize: Optional, default is 2MB. Specify the batch size in bytes

<a id="azure-table-storage"></a>
### <a name="sample-command-source-is-azure-table-storage"></a>Przykładowe polecenia: źródło jest magazynem tabel Azure

Oto przykład wiersza polecenia przedstawiający sposób importowania z magazynu tabel Azure Table do tabeli interfejsu API:

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```
<a id="table-api-preview"></a>
### <a name="sample-command-source-is-azure-cosmos-db-table-api-preview"></a>Przykładowe polecenia: źródło jest interfejs API tabeli bazy danych rozwiązania Cosmos Azure (wersja zapoznawcza)

Oto przykład wiersza polecenia do importowania z tabeli interfejsu API w wersji zapoznawczej do interfejsu API tabeli GA:

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Table API preview account name>;AccountKey=<Table API preview account key>;TableEndpoint=https://<Account Name>.documents.azure.com; /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="azcopy-command"></a>Polecenia programu AzCopy

Przy użyciu wiersza polecenia azcopy jest opcję przeprowadzenia migracji danych z magazynem tabel Azure do interfejsu API Azure rozwiązania Cosmos bazy danych tabeli. Aby użyć narzędzia AzCopy, najpierw wyeksportować dane zgodnie z opisem w [eksportowania danych z magazynu tabel](../storage/common/storage-use-azcopy.md#export-data-from-table-storage), następnie zaimportuj dane do bazy danych rozwiązania Cosmos Azure, zgodnie z opisem w [interfejsu API Azure rozwiązania Cosmos DB tabeli](../storage/common/storage-use-azcopy.md#import-data-into-table-storage).

Podczas importowania do bazy danych Azure rozwiązania Cosmos, odwoływać się do poniższego przykładu. Należy pamiętać, że wartość/dest używa cosmosdb, nie core.

Przykład polecenia import:

```
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="migrate-from-table-api-preview-to-table-api"></a>Migracja z tabeli interfejsu API (wersja zapoznawcza) do tabeli interfejsu API

> [!WARNING]
> Jeśli chcesz od razu korzystać z zalet tabel ogólnie dostępna, a następnie przeprowadź migrację z istniejącymi tabelami Podgląd określonych w tej sekcji, w przeciwnym razie firma Microsoft będzie wykonywać automatycznych migracji dla istniejących klientów w wersji zapoznawczej w najbliższych tygodniach, należy pamiętać, jednak który auto migracji tabel Podgląd ma pewne ograniczenia do nich, które nowo utworzone zostanie tabele nie.
> 

Interfejs API tabeli teraz jest ogólnie dostępna (GA). Istnieją różnice między Podgląd i wersją GA tabel zarówno w kodzie, który działa w chmurze, a także w kod uruchamiany po stronie klienta. W związku z tym nie jest zalecana próby mieszać przy użyciu konta GA tabeli API klienta SDK w wersji zapoznawczej i na odwrót. Tabela interfejsu API w wersji zapoznawczej klientów, którzy chcesz nadal używać ich istniejące tabele, ale w środowisku produkcyjnym konieczne migracji z wersji zapoznawczej w środowisku GA lub poczekaj na potrzeby automatycznej migracji. Aby czekać na potrzeby automatycznej migracji, dowiesz się ograniczeń dotyczących tabel zmigrowane. Po zakończeniu migracji można utworzyć nowe tabele na istniejące konto bez ograniczeń (tylko tabele migrowanych będzie mieć ograniczenia).

Aby przeprowadzić migrację z tabeli interfejsu API (wersja zapoznawcza) ogólnie dostępna API tabeli:

1. Utwórz nowe konto bazy danych Azure rozwiązania Cosmos i ustaw jej typ interfejsu API do tabel Azure, zgodnie z opisem w [Tworzenie konta bazy danych](create-table-dotnet.md#create-a-database-account).

2. Zmień klienci używają wersji GA [zestawów SDK interfejsu API tabeli](table-sdk-dotnet.md).

3. Migrację danych klienta z wersji zapoznawczej tabel do tabel GA przy użyciu narzędzia do migracji danych. Instrukcje dotyczące używania narzędzia migracji danych w tym celu są opisane w [narzędzia migracji danych](#data-migration-tool). 

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Importuj dane z tego narzędzia migracji danych
> * Importuj dane z narzędzia AzCopy
> * Migracja z tabeli interfejsu API (wersja zapoznawcza) do tabeli interfejsu API

Możesz teraz przejść do następnego samouczek i Dowiedz się, jak wykonać zapytanie dotyczące danych przy użyciu interfejsu API Azure rozwiązania Cosmos DB tabeli. 

> [!div class="nextstepaction"]
>[Jak wykonać zapytanie dotyczące danych?](../cosmos-db/tutorial-query-table.md)
