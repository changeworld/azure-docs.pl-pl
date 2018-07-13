---
title: Narzędzie do migracji bazy danych dla usługi Azure Cosmos DB | Microsoft Docs
description: Dowiedz się, jak używać narzędzi do migracji bazy danych usługi Azure Cosmos DB typu open source do importowania danych do usługi Azure Cosmos DB z różnych źródeł, takich jak baza danych MongoDB, program SQL Server, usługa Table Storage, baza danych Amazon DynamoDB oraz pliki CSV i JSON. Konwersja formatu CSV do JSON.
keywords: csv do json, narzędzia do migracji bazy danych, konwersja formatu csv do json
services: cosmos-db
author: SnehaGunda
manager: kfile
editor: monicar
ms.service: cosmos-db
ms.devlang: na
ms.topic: tutorial
ms.date: 03/30/2018
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 52d5977b2a454dec803ad1233fcb12cc9573521c
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37100355"
---
# <a name="azure-cosmos-db-data-migration-tool"></a>Azure Cosmos DB: narzędzie do migracji danych

W tym samouczku znajdują się instrukcje dotyczące używania narzędzia do migracji danych usługi Azure Cosmos DB, które umożliwia importowanie danych z różnych źródeł do kolekcji i tabel usługi Azure Cosmos DB. Dane można importować z plików JSON, plików CSV, kodu SQL, bazy danych MongoDB, usługi Azure Table Storage, bazy danych Amazon DynamoDB, a nawet z kolekcji interfejsu SQL API usługi Azure Cosmos DB, a następnie migrować je do kolekcji i tabel do użycia w usłudze Azure Cosmos DB. Narzędzie do migracji danych może być również używane podczas migracji z kolekcji z pojedynczą partycją do kolekcji z wieloma partycjami na potrzeby interfejsu SQL API.

Który interfejs API będzie używany w usłudze Azure Cosmos DB? 
* **[Interfejs SQL API](documentdb-introduction.md)** — dane można importować za pomocą dowolnej opcji źródła w narzędziu do migracji danych.
* **[Interfejs Table API](table-introduction.md)**  — dane można importować za pomocą narzędzia do migracji danych lub narzędzia AzCopy. Zobacz [Import data for use with the Azure Cosmos DB Table API (Importowanie danych do użycia z interfejsem Table API usługi Azure Cosmos DB)](table-import.md), aby uzyskać więcej informacji.
* **[Interfejs MongoDB API](mongodb-introduction.md)** — narzędzie do migracji danych aktualnie nie obsługuje interfejsu MongoDB API usługi Azure Cosmos DB ani jako źródła, ani jako celu. Jeśli chcesz migrować dane do kolekcji interfejsu MongoDB API lub z tych kolekcji w usłudze Azure Cosmos DB, zapoznaj się z instrukcjami podanymi w temacie [Azure Cosmos DB: How to migrate data for the MongoDB API (Azure Cosmos DB: jak migrować dane na potrzeby interfejsu MongoDB API)](mongodb-migrate.md). Narzędzia do migracji danych można również używać do eksportowania danych z bazy danych MongoDB do kolekcji interfejsu SQL API usługi Azure Cosmos DB do użycia z interfejsem SQL API. 
* **[Interfejs Graph API](graph-introduction.md)** — obecnie narzędzie do migracji danych nie jest obsługiwanym narzędziem importu dla kont interfejsu Graph API. 

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Instalowanie narzędzia do migracji danych
> * Importowanie danych z różnych źródeł danych
> * Eksportowanie z usługi Azure Cosmos DB do formatu JSON

## <a id="Prerequisites"></a>Wymagania wstępne
Przed wykonaniem instrukcji zawartych w tym artykule upewnij się, że masz zainstalowane następujące elementy:

* Program [Microsoft .NET Framework 4.51](https://www.microsoft.com/download/developer-tools.aspx) lub nowszy.

* Zwiększenie przepływności: czas trwania migracji danych zależy od przepływności skonfigurowanej dla pojedynczej kolekcji lub dla zestawu kolekcji. Pamiętaj o zwiększeniu przepływności w przypadku większych migracji danych. Po ukończeniu migracji zmniejsz przepływność, aby ograniczyć koszty. Aby uzyskać więcej informacji na temat zwiększania przepływności w witrynie Azure Portal, zobacz Performance levels and pricing tiers in Azure Cosmos DB (Poziomy wydajności i warstwy cenowe w usłudze Azure Cosmos DB).

## <a id="Overviewl"></a>Omówienie
Narzędzie do migracji danych to rozwiązanie typu open source, które importuje dane do usługi Azure Cosmos DB z różnych źródeł, takich jak:

* Pliki JSON
* MongoDB
* Oprogramowanie SQL Server
* Pliki CSV
* Azure Table Storage
* Baza danych Amazon DynamoDB
* HBase
* Kolekcje usługi Azure Cosmos DB

Narzędzie do importowania ma graficzny interfejs użytkownika (dtui.exe), ale może być również zarządzane z poziomu wiersza polecenia (dt.exe). W rzeczywistości istnieje opcja wyprowadzenia skojarzonego polecenia po skonfigurowaniu importu za pośrednictwem interfejsu użytkownika. Tabelaryczne źródła danych (np. program SQL Server lub pliki CSV) można przekształcać tak, aby podczas importowania można było tworzyć relacje hierarchiczne (dokumenty podrzędne). W dalszej części artykułu przedstawiamy dalsze informacje na temat opcji źródeł, przykładowe polecenia do importowania z poszczególnych źródeł, opcje docelowe oraz informacje o wyświetlaniu wyników importu.

## <a id="Install"></a>Instalacja
Kod źródłowy narzędzia do migracji jest dostępny w witrynie GitHub w [tym repozytorium](https://github.com/azure/azure-documentdb-datamigrationtool). Możesz pobrać i skompilować rozwiązanie lokalnie lub [pobrać wstępnie skompilowany plik binarny](https://cosmosdbportalstorage.blob.core.windows.net/datamigrationtool/2018.02.28-1.8.1/dt-1.8.1.zip), a następnie uruchomić jeden z plików:

* **Dtui.exe**: wersja interfejsu graficznego narzędzia
* **Dt.exe**: wersja wiersza polecenia narzędzia

## <a name="select-data-source"></a>Wybieranie źródła danych

Po zainstalowaniu narzędzia można rozpocząć importowanie danych. Jakiego rodzaju dane chcesz importować?

* [Pliki JSON](#JSON)
* [MongoDB](#MongoDB)
* [Pliki eksportu bazy danych MongoDB](#MongoDBExport)
* [SQL Server](#SQL)
* [Pliki CSV](#CSV)
* [Azure Table storage](#AzureTableSource)
* [Baza danych Amazon DynamoDB](#DynamoDBSource)
* [Obiekt blob](#BlobImport)
* [Kolekcje usługi Azure Cosmos DB](#SQLSource)
* [HBase](#HBaseSource)
* [Import zbiorczy w usłudze Azure Cosmos DB](#SQLBulkImport)
* [Sekwencyjny import rekordów w usłudze Azure Cosmos DB](#DocumentDSeqTarget)


## <a id="JSON"></a>Importowanie plików JSON
Opcja importera źródła dla plików JSON umożliwia importowanie plików JSON z co najmniej jednym dokumentem lub plików JSON, z których każdy zawiera tablicę dokumentów JSON. W przypadku dodawania folderów zawierających pliki JSON do zaimportowania możesz wybrać opcję cyklicznego wyszukiwania plików w podfolderach.

![Zrzut ekranu przedstawiający opcje źródła dla plików JSON — narzędzia do migracji bazy danych](./media/import-data/jsonsource.png)

Oto niektóre przykłady wiersza polecenia dotyczące importowania plików JSON:

    #Import a single JSON file
    dt.exe /s:JsonFile /s.Files:.\Sessions.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

    #Import a directory of JSON files
    dt.exe /s:JsonFile /s.Files:C:\TESessions\*.json /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

    #Import a directory (including sub-directories) of JSON files
    dt.exe /s:JsonFile /s.Files:C:\LastFMMusic\**\*.json /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Music /t.CollectionThroughput:2500

    #Import a directory (single), directory (recursive), and individual JSON files
    dt.exe /s:JsonFile /s.Files:C:\Tweets\*.*;C:\LargeDocs\**\*.*;C:\TESessions\Session48172.json;C:\TESessions\Session48173.json;C:\TESessions\Session48174.json;C:\TESessions\Session48175.json;C:\TESessions\Session48177.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:subs /t.CollectionThroughput:2500

    #Import a single JSON file and partition the data across 4 collections
    dt.exe /s:JsonFile /s.Files:D:\\CompanyData\\Companies.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:comp[1-4] /t.PartitionKey:name /t.CollectionThroughput:2500

## <a id="MongoDB"></a>Importowanie z bazy danych MongoDB

> [!IMPORTANT]
> W przypadku importowania do konta usługi Azure Cosmos DB z obsługą bazy danych MongoDB postępuj zgodnie z następującymi [instrukcjami](mongodb-migrate.md).
> 
> 

Opcja importera źródła dla bazy danych MongoDB umożliwia importowanie z pojedynczej kolekcji bazy danych MongoDB i opcjonalnie filtrowanie dokumentów za pomocą zapytania i/lub modyfikowanie struktury dokumentu przy użyciu projekcji.  

![Zrzut ekranu przedstawiający opcje źródła dla bazy danych MongoDB](./media/import-data/mongodbsource.png)

Parametry połączenia mają standardowy format bazy danych MongoDB:

    mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database>

> [!NOTE]
> Aby upewnić się, że wystąpienie bazy danych MongoDB określone w polu parametrów połączenia jest dostępne, użyj polecenia weryfikacji.
> 
> 

Wprowadź nazwę kolekcji, z której dane zostaną zaimportowane. Opcjonalnie możesz określić lub udostępnić plik dla zapytania (na przykład {pop: {$gt:5000}}) i/lub projekcji (na przykład {loc:0}) na potrzeby filtrowania i kształtowania danych do zaimportowania.

Oto niektóre przykłady wiersza polecenia dotyczące importowania z bazy danych MongoDB:

    #Import all documents from a MongoDB collection
    dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZips /t.IdField:_id /t.CollectionThroughput:2500

    #Import documents from a MongoDB collection which match the query and exclude the loc field
    dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /s.Query:{pop:{$gt:50000}} /s.Projection:{loc:0} /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZipsTransform /t.IdField:_id/t.CollectionThroughput:2500

## <a id="MongoDBExport"></a>Importowanie plików eksportu bazy danych MongoDB

> [!IMPORTANT]
> W przypadku importowania do konta usługi Azure Cosmos DB z obsługą bazy danych MongoDB postępuj zgodnie z następującymi [instrukcjami](mongodb-migrate.md).
> 
> 

Opcja importera źródła dla pliku JSON eksportu bazy danych MongoDB umożliwia importowanie co najmniej jednego pliku JSON utworzonego przez narzędzie mongoexport.  

![Zrzut ekranu przedstawiający opcje źródła eksportu bazy danych MongoDB](./media/import-data/mongodbexportsource.png)

W przypadku dodawania folderów zawierających pliki JSON eksportu bazy danych MongoDB do zaimportowania możesz wybrać opcję cyklicznego wyszukiwania plików w podfolderach.

Oto przykład wiersza polecenia dotyczący importowania z plików JSON eksportu bazy danych MongoDB:

    dt.exe /s:MongoDBExport /s.Files:D:\mongoemployees.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:employees /t.IdField:_id /t.Dates:Epoch /t.CollectionThroughput:2500

## <a id="SQL"></a>Importowanie z programu SQL Server
Opcja importera źródła SQL umożliwia importowanie z pojedynczej bazy danych programu SQL Server i opcjonalnie filtrowanie rekordów do zaimportowania przy użyciu zapytania. Ponadto można zmodyfikować strukturę dokumentu, określając separator zagnieżdżania (więcej informacji na temat można znaleźć w dalszej części artykułu).  

![Zrzut ekranu przedstawiający opcje źródła dla kodu SQL — narzędzia do migracji bazy danych](./media/import-data/sqlexportsource.png)

Format parametrów połączenia to standardowy format parametrów połączenia SQL.

> [!NOTE]
> Aby upewnić się, że wystąpienie programu SQL Server określone w polu parametrów połączenia jest dostępne, należy użyć polecenia weryfikacji.
> 
> 

Właściwość separatora zagnieżdżania służy do tworzenia relacji hierarchicznych (dokumentów podrzędnych) podczas importowania. Rozważmy następujące zapytanie SQL:

*select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'*

Zwraca ono następujące wyniki (częściowe):

![Zrzut ekranu przedstawiający wyniki zapytania SQL](./media/import-data/sqlqueryresults.png)

Zwróć uwagę na aliasy, takie jak Address.AddressType i Address.Location.StateProvinceName. Określając separator zagnieżdżania „.”, narzędzie importowania tworzy dokumenty podrzędne Address i Address.Location podczas importu. Oto przykład dokumentu wynikowego w usłudze Azure Cosmos DB:

*{ "id": "956", "Name": "Finer Sales and Service", "Address": { "AddressType": "Main Office", "AddressLine1": "#500-75 O'Connor Street", "Location": { "City": "Ottawa", "StateProvinceName": "Ontario" }, "PostalCode": "K4B 1S2", "CountryRegionName": "Canada" } }*

Oto niektóre przykłady wiersza polecenia dotyczące importowania z programu SQL Server:

    #Import records from SQL which match a query
    dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, * from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Stores /t.IdField:Id /t.CollectionThroughput:2500

    #Import records from sql which match a query and create hierarchical relationships
    dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /s.NestingSeparator:. /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:StoresSub /t.IdField:Id /t.CollectionThroughput:2500

## <a id="CSV"></a>Importowanie plików CSV i konwertowanie formatu CSV na JSON
Opcja importera źródła dla pliku CSV umożliwia importowanie co najmniej jednego pliku CSV. W przypadku dodawania folderów zawierających pliki CSV do zaimportowania możesz wybrać opcję cyklicznego wyszukiwania plików w podfolderach.

![Zrzut ekranu przedstawiający opcje źródła CSV — CSV do JSON](media/import-data/csvsource.png)

Podobnie jak w przypadku źródła kodu SQL, właściwość separatora zagnieżdżania może być używana do tworzenia relacji hierarchicznych (dokumentów podrzędnych) podczas importowania. Rozważmy następujące wiersze danych i wiersz nagłówka CSV:

![Zrzut ekranu przedstawiający przykładowe rekordy CSV — CSV do JSON](./media/import-data/csvsample.png)

Zwróć uwagę na aliasy, takie jak DomainInfo.Domain_Name i RedirectInfo.Redirecting. Określając separator zagnieżdżania „.”, narzędzie importowania utworzy dokumenty podrzędne DomainInfo i RedirectInfo podczas importu. Oto przykład dokumentu wynikowego w usłudze Azure Cosmos DB:

*{ "DomainInfo": { "Domain_Name": "ACUS.GOV", "Domain_Name_Address": "http://www.ACUS.GOV" }, "Federal Agency": "Administrative Conference of the United States", "RedirectInfo": { "Redirecting": "0", "Redirect_Destination": "" }, "id": "9cc565c5-ebcd-1c03-ebd3-cc3e2ecd814d" }*

Narzędzie importowania próbuje wywnioskować informacje dotyczące typu dla wartości bez cudzysłowów w plikach CSV (wartości w cudzysłowie są zawsze traktowane jak ciągi).  Typy są identyfikowane w następującej kolejności: liczba, data/godzina, wartość logiczna.  

Istnieją dwie kwestie, o których warto wspomnieć w związku z importem plików CSV:

1. Domyślnie wartości bez cudzysłowów są zawsze przycinane o tabulatory i spacje, a wartości w cudzysłowie są zachowywane w oryginalnej postaci. To zachowanie można przesłonić przy użyciu pola wyboru Przytnij wartości w cudzysłowie lub opcji wiersza polecenia /s.TrimQuoted.
2. Domyślnie wartość null bez cudzysłowów jest traktowana jako wartość null. To zachowanie można przesłonić (czyli traktować wartość null bez cudzysłowów null jako ciąg „null”) przy użyciu pola wyboru Traktuj wartość NULL bez cudzysłowów jako ciąg lub opcji wiersza polecenia /s.NoUnquotedNulls.

Oto przykład wiersza polecenia dotyczący importowania danych CSV:

    dt.exe /s:CsvFile /s.Files:.\Employees.csv /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Employees /t.IdField:EntityID /t.CollectionThroughput:2500

## <a id="AzureTableSource"></a>Importowanie z usługi Azure Table Storage
Opcja importera źródła dla usługi Azure Table Storage umożliwia importowanie z pojedynczej tabeli usługi Azure Table Storage. Opcjonalnie można filtrować jednostki tabeli do zaimportowania. 

Dane importowane z usługi Azure Table Storage mogą być danymi wyjściowymi tabel i jednostek usługi Azure Cosmos DB do użycia przez interfejs Table API lub danymi wyjściowymi kolekcji i dokumentów do użycia przez interfejs SQL API. Interfejs Table API jest jednak dostępny tylko jako miejsce docelowe w narzędziu wiersza polecenia. Nie można eksportować do interfejsu Table API za pomocą interfejsu użytkownika narzędzia do migracji danych. Aby uzyskać więcej informacji, zobacz [Import data for use with the Azure Cosmos DB Table API (Importowanie danych do użycia z interfejsem Table API usługi Azure Cosmos DB)](table-import.md). 

![Zrzut ekranu przedstawiający opcje źródła dla usługi Azure Table Storage](./media/import-data/azuretablesource.png)

Format parametrów połączenia usługi Azure Table Storage to:

    DefaultEndpointsProtocol=<protocol>;AccountName=<Account Name>;AccountKey=<Account Key>;

> [!NOTE]
> Aby upewnić się, że wystąpienie usługi Azure Table Storage określone w polu parametrów połączenia jest dostępne, należy użyć polecenia weryfikacji.
> 
> 

Wprowadź nazwę tabeli platformy Azure, z której chcesz importować dane. Możesz opcjonalnie określić [filtr](../vs-azure-tools-table-designer-construct-filter-strings.md).

Opcja importera źródła dla usługi Azure Table Storage ma następujące opcje dodatkowe:

1. Uwzględnij pola wewnętrzne
   1. Wszystkie — uwzględnianie wszystkich pól wewnętrznych (PartitionKey, RowKey i Znacznik czasu)
   2. Brak — wykluczanie wszystkich pól wewnętrznych
   3. RowKey — uwzględnianie tylko pola RowKey
2. Wybieranie kolumn
   1. Filtry usługi Azure Table Storage nie obsługują projekcji. Jeśli chcesz zaimportować tylko określone właściwości jednostek usługi Azure Table, dodaj je do listy Wybierz kolumny. Wszystkie inne właściwości jednostek zostaną zignorowane.

Oto przykład wiersza polecenia dotyczący importowania danych z usługi Azure Table Storage:

    dt.exe /s:AzureTable /s.ConnectionString:"DefaultEndpointsProtocol=https;AccountName=<Account Name>;AccountKey=<Account Key>" /s.Table:metrics /s.InternalFields:All /s.Filter:"PartitionKey eq 'Partition1' and RowKey gt '00001'" /s.Projection:ObjectCount;ObjectSize  /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:metrics /t.CollectionThroughput:2500

## <a id="DynamoDBSource"></a>Importowanie z bazy danych Amazon DynamoDB
Opcja importera źródła dla bazy danych Amazon DynamoDB umożliwia importowanie z pojedynczej tabeli bazy danych Amazon DynamoDB i opcjonalnie filtrowanie jednostek do zaimportowania. Udostępniono kilka szablonów, aby konfigurowanie importu było jak najprostsze.

![Zrzut ekranu przedstawiający opcje źródła dla bazy danych Amazon DynamoDB — narzędzia do migracji bazy danych](./media/import-data/dynamodbsource1.png)

![Zrzut ekranu przedstawiający opcje źródła dla bazy danych Amazon DynamoDB — narzędzia do migracji bazy danych](./media/import-data/dynamodbsource2.png)

Format parametrów połączenia dla bazy danych Amazon DynamoDB to:

    ServiceURL=<Service Address>;AccessKey=<Access Key>;SecretKey=<Secret Key>;

> [!NOTE]
> Aby upewnić się, że wystąpienie bazy danych Amazon DynamoDB określone w polu parametrów połączenia jest dostępne, użyj polecenia weryfikacji.
> 
> 

Oto przykład wiersza polecenia służący do importowania danych z bazy danych Amazon DynamoDB:

    dt.exe /s:DynamoDB /s.ConnectionString:ServiceURL=https://dynamodb.us-east-1.amazonaws.com;AccessKey=<accessKey>;SecretKey=<secretKey> /s.Request:"{   """TableName""": """ProductCatalog""" }" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<Azure Cosmos DB Endpoint>;AccountKey=<Azure Cosmos DB Key>;Database=<Azure Cosmos DB Database>;" /t.Collection:catalogCollection /t.CollectionThroughput:2500

## <a id="BlobImport"></a>Importowanie z usługi Azure Blob Storage
Opcje importera źródła dla pliku JSON, pliku eksportu bazy danych MongoDB i pliku CSV umożliwiają importowanie co najmniej jednego pliku z usług Azure Blob Storage. Po określeniu adresu URL kontenera obiektów blob i klucza konta wprowadź wyrażenie regularne, aby wybrać pliki do zaimportowania.

![Zrzut ekranu przedstawiający opcje źródła dla plików obiektów blob](./media/import-data/blobsource.png)

Oto przykład wiersza polecenia dotyczący importowania plików JSON z usługi Azure Blob Storage:

    dt.exe /s:JsonFile /s.Files:"blobs://<account key>@account.blob.core.windows.net:443/importcontainer/.*" /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:doctest

## <a id="SQLSource"></a>Importowanie z kolekcji interfejsu SQL API
Opcja importera źródła dla usługi Azure Cosmos DB umożliwia importowanie danych z co najmniej jednej kolekcji usługi Azure Cosmos DB i opcjonalnie filtrowanie dokumentów przy użyciu zapytania.  

![Zrzut ekranu przedstawiający opcje źródła dla usługi Azure Cosmos DB](./media/import-data/documentdbsource.png)

Format parametrów połączenia usługi Azure Cosmos DB to:

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

Parametry połączenia konta usługi Azure Cosmos DB można pobrać ze strony kluczy w witrynie Azure Portal zgodnie z opisem w temacie [How to manage an Azure Cosmos DB account (Jak zarządzać kontem usługi Azure Cosmos DB)](manage-account.md), ale do parametrów połączenia należy dołączyć bazę danych w następującym formacie:

    Database=<CosmosDB Database>;

> [!NOTE]
> Aby upewnić się, że wystąpienie usługi Azure Cosmos DB określone w polu parametrów połączenia jest dostępne, użyj polecenia weryfikacji.
> 
> 

Aby importować z pojedynczej kolekcji usługi Azure Cosmos DB, wprowadź nazwę kolekcji, z której chcesz zaimportować dane. Aby importować z wielu kolekcji usługi Azure Cosmos DB, podaj wyrażenie regularne w celu dopasowania co najmniej jednej nazwy kolekcji (na przykład collection01 | collection02 | collection03). Opcjonalnie możesz określić lub udostępnić plik dla zapytania na potrzeby filtrowania i kształtowania danych do zaimportowania.

> [!NOTE]
> Ponieważ w polu kolekcji są akceptowane wyrażenia regularne, jeśli import odbywa się z pojedynczej kolekcji, której nazwa zawiera znaki wyrażenia regularnego, te znaki muszą odpowiednio zmienić znaczenie.
> 
> 

Opcja importera źródła dla usługi Azure Cosmos DB ma następujące opcje zaawansowane:

1. Uwzględnij pola wewnętrzne: określa, czy podczas eksportu mają być uwzględniane właściwości systemu dokumentów usługi Azure Cosmos DB (np. _rid, _ts).
2. Liczba ponownych prób przy niepowodzeniu: określa, ile razy ma zostać ponowiona próba nawiązania połączenia z usługą Azure Cosmos DB w przypadku przejściowych niepowodzeń (np. zakłócenia łączności sieciowej).
3. Interwał ponownych prób: określa, jak długo trzeba czekać między ponownymi próbami nawiązania połączenia z usługą Azure Cosmos DB w przypadku przejściowych niepowodzeń (np. zakłócenia łączności sieciowej).
4. Tryb połączenia: określa tryb połączenia do użycia z usługą Azure Cosmos DB. Dostępne opcje to DirectTcp, DirectHttps i Gateway. Tryby połączenia bezpośredniego są szybsze, ale tryb bramy jest korzystniejszy dla zapory, ponieważ używa tylko portu 443.

![Zrzut ekranu przedstawiający zaawansowane opcje źródła dla usługi Azure Cosmos DB](./media/import-data/documentdbsourceoptions.png)

> [!TIP]
> Narzędzie importowania jest domyślnie ustawiane na tryb połączenia DirectTcp. Jeśli występują problemy z zaporą, przełącz się do trybu połączenia Gateway, ponieważ wymaga on tylko portu 443.
> 
> 

Oto niektóre przykłady wiersza polecenia dotyczące importowania z usługi Azure Cosmos DB:

    #Migrate data from one Azure Cosmos DB collection to another Azure Cosmos DB collections
    dt.exe /s:CosmosDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:TEColl /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:TESessions /t.CollectionThroughput:2500

    #Migrate data from multiple Azure Cosmos DB collections to a single Azure Cosmos DB collection
    dt.exe /s:CosmosDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:comp1|comp2|comp3|comp4 /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:singleCollection /t.CollectionThroughput:2500

    #Export an Azure Cosmos DB collection to a JSON file
    dt.exe /s:CosmosDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:StoresSub /t:JsonFile /t.File:StoresExport.json /t.Overwrite /t.CollectionThroughput:2500

> [!TIP]
> Narzędzie importowania danych usługi Azure Cosmos DB obsługuje również import danych z [emulatora usługi Azure Cosmos DB](local-emulator.md). W przypadku importowania danych z emulatora lokalnego ustaw punkt końcowy na `https://localhost:<port>`. 
> 
> 

## <a id="HBaseSource"></a>Importowanie z bazy danych HBase
Opcja importera źródła dla bazy danych HBase umożliwia importowanie danych z tabeli HBase i opcjonalnie filtrowanie danych. Udostępniono kilka szablonów, aby konfigurowanie importu było jak najprostsze.

![Zrzut ekranu przedstawiający opcje źródła dla bazy danych HBase](./media/import-data/hbasesource1.png)

![Zrzut ekranu przedstawiający opcje źródła dla bazy danych HBase](./media/import-data/hbasesource2.png)

Format parametrów połączenia bazy danych HBase Stargate to:

    ServiceURL=<server-address>;Username=<username>;Password=<password>

> [!NOTE]
> Aby upewnić się, że wystąpienie bazy danych HBase określone w polu parametrów połączenia jest dostępne, użyj polecenia weryfikacji.
> 
> 

Oto przykład wiersza polecenia dotyczący importowania z bazy danych HBase:

    dt.exe /s:HBase /s.ConnectionString:ServiceURL=<server-address>;Username=<username>;Password=<password> /s.Table:Contacts /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:hbaseimport

## <a id="SQLBulkTarget"></a>Importowanie do interfejsu SQL API (import zbiorczy)
Importer zbiorczy usługi Azure Cosmos DB umożliwia importowanie z dowolnych opcji źródła przy użyciu procedury składowanej usługi Azure Cosmos DB w celu zwiększenia wydajności. Narzędzie obsługuje import do kolekcji usługi Azure Cosmos DB z pojedynczą partycją, a także import pofragmentowany, w przypadku którego dane są podzielone na partycje w wielu kolekcjach usługi Azure Cosmos DB z pojedynczą partycją. Aby uzyskać więcej informacji na temat partycjonowania danych, zobacz [Partitioning and scaling in Azure Cosmos DB (Partycjonowanie i skalowanie w usłudze Azure Cosmos DB)](partition-data.md). Narzędzie tworzy, wykonuje, a następnie usuwa procedurę składowaną z kolekcji docelowych.  

![Zrzut ekranu przedstawiający opcje zbiorcze usługi Azure Cosmos DB](./media/import-data/documentdbbulk.png)

Format parametrów połączenia usługi Azure Cosmos DB to:

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

Parametry połączenia konta usługi Azure Cosmos DB można pobrać ze strony kluczy w witrynie Azure Portal zgodnie z opisem w temacie [How to manage an Azure Cosmos DB account (Jak zarządzać kontem usługi Azure Cosmos DB)](manage-account.md), ale do parametrów połączenia należy dołączyć bazę danych w następującym formacie:

    Database=<CosmosDB Database>;

> [!NOTE]
> Aby upewnić się, że wystąpienie usługi Azure Cosmos DB określone w polu parametrów połączenia jest dostępne, użyj polecenia weryfikacji.
> 
> 

Aby importować do pojedynczej kolekcji, wprowadź nazwę kolekcji, z której chcesz zaimportować dane, i kliknij przycisk Dodaj. Aby importować do wielu kolekcji, wprowadź pojedynczo nazwy poszczególnych kolekcji lub użyj poniższej składni, aby określić wiele kolekcji: *prefiks_kolekcji*[indeks początkowy–indeks końcowy]. W przypadku określania wielu kolekcji za pomocą wyżej wymienionej składni pamiętaj o następujących wytycznych:

1. Obsługiwane są tylko wzorce nazw zakresów liczb całkowitych. Na przykład określenie kolekcji collection[0–3] powoduje utworzenie następujących kolekcji: collection0, collection1, collection2, collection3.
2. Można użyć składni skróconej: collection[3] powoduje utworzenie tego samego zestawu kolekcji wspomnianego w kroku 1.
3. Można określić więcej niż jedno podstawienie. Na przykład instrukcja collection[0–1] [0–9] powoduje wygenerowanie 20 nazw kolekcji z zerami początkowymi (collection01, ...02, ...03).

Po wskazaniu nazw kolekcji należy wybrać żądaną przepływność kolekcji (od 400 do 10 000 jednostek RU). Aby uzyskać najlepszą wydajność importowania, wybierz wyższą przepustowość. Więcej informacji na temat poziomów wydajności można znaleźć w temacie [Performance levels in Azure Cosmos DB (Poziomy wydajności w usłudze Azure Cosmos DB)](performance-levels.md).

> [!NOTE]
> Ustawienie przepływności wydajności ma zastosowanie tylko do tworzenia kolekcji. Jeśli określona kolekcja już istnieje, jej przepływność nie jest modyfikowana.
> 
> 

W przypadku importowania do wielu kolekcji narzędzie importowania obsługuje dzielenie na fragmenty na podstawie skrótów. W tym scenariuszu należy określić właściwość dokumentu, której chcesz użyć jako klucza partycji (jeśli pole klucza partycji jest puste, dokumenty są losowo dzielone na fragmenty w obrębie kolekcji docelowych).

Można opcjonalnie wybrać pole w źródle importu, które ma być używane jako właściwość identyfikatora dokumentu usługi Azure Cosmos DB podczas importowania (jeśli dokumenty nie zawierają tej właściwości, narzędzie importowania generuje identyfikator GUID jako wartość właściwości identyfikatora).

Podczas importowania jest dostępnych kilka opcji zaawansowanych. Najpierw, mimo że narzędzie oferuje domyślną procedurę składowaną importu zbiorczego (BulkInsert.js), możesz określić własną procedurę składowaną importowania:

 ![Zrzut ekranu przedstawiający opcję procedury składowanej wstawiania zbiorczego usługi Azure Cosmos DB](./media/import-data/bulkinsertsp.png)

Dodatkowo w przypadku importowania typów danych (na przykład z programu SQL Server lub bazy danych MongoDB) można wybrać jedną z trzech opcji importowania:

 ![Zrzut ekranu przedstawiający opcje importowania daty/godziny w usłudze Azure Cosmos DB](./media/import-data/datetimeoptions.png)

* Ciąg: utrzymywanie jako wartości ciągu
* Epoka: utrzymywanie jako wartości liczbowej epoki
* Oba: utrzymywanie wartości ciągu i wartości liczbowej epoki. Ta opcja powoduje utworzenie dokumentu podrzędnego, na przykład:"date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

Importer zbiorczy usługi Azure Cosmos DB ma następujące opcje zaawansowane:

1. Rozmiar partii: narzędzie domyślnie ustawia rozmiar partii na 50.  W przypadku importowania dużych dokumentów należy rozważyć zmniejszenie rozmiaru partii. I odwrotnie: w przypadku importowania małych dokumentów należy rozważyć zwiększenie rozmiaru partii.
2. Maksymalny rozmiar skryptu (w bajtach): narzędzie domyślnie ustawia maksymalny rozmiar skryptu na 512 KB.
3. Wyłącz automatyczne generowanie identyfikatora: jeśli każdy dokument do zaimportowania zawiera pole identyfikatora, wybierając tę opcję, można zwiększyć wydajność. Dokumenty bez pola unikatowego identyfikatora nie są importowane.
4. Aktualizuj istniejące dokumenty: narzędzie domyślnie nie zastępuje istniejących dokumentów z konfliktami identyfikatorów. Wybranie tej opcji pozwala na zastępowanie istniejących dokumentów z pasującymi identyfikatorami. Ta funkcja jest przydatna w przypadku planowanych migracji danych, które aktualizują istniejące dokumenty.
5. Liczba ponownych prób przy niepowodzeniu: określa, ile razy ma zostać ponowiona próba nawiązania połączenia z usługą Azure Cosmos DB w przypadku przejściowych niepowodzeń (np. zakłócenia łączności sieciowej).
6. Interwał ponownych prób: określa, jak długo trzeba czekać między ponownymi próbami nawiązania połączenia z usługą Azure Cosmos DB w przypadku przejściowych niepowodzeń (np. zakłócenia łączności sieciowej).
7. Tryb połączenia: określa tryb połączenia do użycia z usługą Azure Cosmos DB. Dostępne opcje to DirectTcp, DirectHttps i Gateway. Tryby połączenia bezpośredniego są szybsze, ale tryb bramy jest korzystniejszy dla zapory, ponieważ używa tylko portu 443.

![Zrzut ekranu przedstawiający zaawansowane opcje importu zbiorczego dla usługi Azure Cosmos DB](./media/import-data/docdbbulkoptions.png)

> [!TIP]
> Narzędzie importowania jest domyślnie ustawiane na tryb połączenia DirectTcp. Jeśli występują problemy z zaporą, przełącz się do trybu połączenia Gateway, ponieważ wymaga on tylko portu 443.
> 
> 

## <a id="SQLSeqTarget"></a>Importowanie do interfejsu SQL API (sekwencyjny import rekordów)
Sekwencyjny import rekordów usługi Azure Cosmos DB umożliwia importowanie kolejnych rekordów przy użyciu dowolnych spośród dostępnych opcji źródła. Można wybrać tę opcję w przypadku importowania do istniejącej kolekcji, która osiągnęła limit przydziału procedur składowanych. Narzędzie obsługuje import do pojedynczej kolekcji usługi Azure Cosmos DB (z pojedynczą partycją lub wieloma partycjami), a także import pofragmentowany, w przypadku którego dane są podzielone na partycje w wielu kolekcjach usługi Azure Cosmos DB z pojedynczą partycją i/lub wieloma partycjami. Aby uzyskać więcej informacji na temat partycjonowania danych, zobacz [Partitioning and scaling in Azure Cosmos DB (Partycjonowanie i skalowanie w usłudze Azure Cosmos DB)](partition-data.md).

![Zrzut ekranu przedstawiający opcje sekwencyjnego importu rekordów usługi Azure Cosmos DB](./media/import-data/documentdbsequential.png)

Format parametrów połączenia usługi Azure Cosmos DB to:

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

Parametry połączenia konta usługi Azure Cosmos DB można pobrać ze strony kluczy w witrynie Azure Portal zgodnie z opisem w temacie [How to manage an Azure Cosmos DB account (Jak zarządzać kontem usługi Azure Cosmos DB)](manage-account.md), ale do parametrów połączenia należy dołączyć bazę danych w następującym formacie:

    Database=<Azure Cosmos DB Database>;

> [!NOTE]
> Aby upewnić się, że wystąpienie usługi Azure Cosmos DB określone w polu parametrów połączenia jest dostępne, użyj polecenia weryfikacji.
> 
> 

Aby importować do pojedynczej kolekcji, wprowadź nazwę kolekcji, do której dane będą importowane, i kliknij przycisk Dodaj. Aby importować do wielu kolekcji, wprowadź pojedynczo nazwy poszczególnych kolekcji lub użyj poniższej składni, aby określić wiele kolekcji: *prefiks_kolekcji*[indeks początkowy–indeks końcowy]. W przypadku określania wielu kolekcji za pomocą wyżej wymienionej składni pamiętaj o następujących wytycznych:

1. Obsługiwane są tylko wzorce nazw zakresów liczb całkowitych. Na przykład określenie kolekcji collection[0–3] powoduje utworzenie następujących kolekcji: collection0, collection1, collection2, collection3.
2. Można użyć składni skróconej: collection[3] powoduje utworzenie tego samego zestawu kolekcji wspomnianego w kroku 1.
3. Można określić więcej niż jedno podstawienie. Na przykład instrukcja collection[0–1] [0–9] powoduje utworzenie 20 nazw kolekcji z zerami początkowymi (collection01, ...02, ...03).

Po wskazaniu nazw kolekcji należy wybrać żądaną przepływność kolekcji (od 400 do 250 000 jednostek RU). Aby uzyskać najlepszą wydajność importowania, wybierz wyższą przepustowość. Więcej informacji na temat poziomów wydajności można znaleźć w temacie [Performance levels in Azure Cosmos DB (Poziomy wydajności w usłudze Azure Cosmos DB)](performance-levels.md). Wszystkie operacje importowania do kolekcji o przepływności większej niż 10 000 jednostek RU wymagają klucza partycji. Jeśli wybierzesz liczbę większą niż 250 000 jednostek RU, musisz w portalu przesłać żądanie zwiększenia możliwości konta.

> [!NOTE]
> Ustawienie przepływności ma zastosowanie tylko do tworzenia kolekcji lub bazy danych. Jeśli określona kolekcja już istnieje, jej przepływność nie będzie modyfikowana.
> 
> 

W przypadku importowania do wielu kolekcji narzędzie importowania obsługuje dzielenie na fragmenty na podstawie skrótów. W tym scenariuszu należy określić właściwość dokumentu, której chcesz użyć jako klucza partycji (jeśli pole klucza partycji jest puste, dokumenty są losowo dzielone na fragmenty w obrębie kolekcji docelowych).

Można opcjonalnie wybrać pole w źródle importu, które ma być używane jako właściwość identyfikatora dokumentu usługi Azure Cosmos DB podczas importowania (jeśli dokumenty nie zawierają tej właściwości, narzędzie importowania generuje identyfikator GUID jako wartość właściwości identyfikatora).

Podczas importowania jest dostępnych kilka opcji zaawansowanych. Najpierw w przypadku importowania typów danych (na przykład z programu SQL Server lub bazy danych MongoDB) można wybrać jedną z trzech opcji importowania:

 ![Zrzut ekranu przedstawiający opcje importowania daty/godziny w usłudze Azure Cosmos DB](./media/import-data/datetimeoptions.png)

* Ciąg: utrzymywanie jako wartości ciągu
* Epoka: utrzymywanie jako wartości liczbowej epoki
* Oba: utrzymywanie wartości ciągu i wartości liczbowej epoki. Ta opcja powoduje utworzenie dokumentu podrzędnego, na przykład:"date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

Sekwencyjny importer rekordów usługi Azure Cosmos DB ma następujące zaawansowane opcje dodatkowe:

1. Liczba żądań równoległych: domyślnie narzędzie obsługuje dwa żądania równoległe. W przypadku importowania małych dokumentów należy rozważyć zwiększenie liczby żądań równoległych. Jeśli zostanie ta liczba zostanie za bardzo zwiększona, podczas importowania mogą wystąpić ograniczenia prędkości.
2. Wyłącz automatyczne generowanie identyfikatora: jeśli każdy dokument do zaimportowania zawiera pole identyfikatora, wybierając tę opcję, można zwiększyć wydajność. Dokumenty bez pola unikatowego identyfikatora nie są importowane.
3. Aktualizuj istniejące dokumenty: narzędzie domyślnie nie zastępuje istniejących dokumentów z konfliktami identyfikatorów. Wybranie tej opcji pozwala na zastępowanie istniejących dokumentów z pasującymi identyfikatorami. Ta funkcja jest przydatna w przypadku planowanych migracji danych, które aktualizują istniejące dokumenty.
4. Liczba ponownych prób przy niepowodzeniu: określa, ile razy ma zostać ponowiona próba nawiązania połączenia z usługą Azure Cosmos DB w przypadku przejściowych niepowodzeń (np. zakłócenia łączności sieciowej).
5. Interwał ponownych prób: określa, jak długo trzeba czekać między ponownymi próbami nawiązania połączenia z usługą Azure Cosmos DB w przypadku przejściowych niepowodzeń (np. zakłócenia łączności sieciowej).
6. Tryb połączenia: określa tryb połączenia do użycia z usługą Azure Cosmos DB. Dostępne opcje to DirectTcp, DirectHttps i Gateway. Tryby połączenia bezpośredniego są szybsze, ale tryb bramy jest korzystniejszy dla zapory, ponieważ używa tylko portu 443.

![Zrzut ekranu przedstawiający zaawansowane opcje sekwencyjnego importowania rekordów usługi Azure Cosmos DB](./media/import-data/documentdbsequentialoptions.png)

> [!TIP]
> Narzędzie importowania jest domyślnie ustawiane na tryb połączenia DirectTcp. Jeśli występują problemy z zaporą, przełącz się do trybu połączenia Gateway, ponieważ wymaga on tylko portu 443.
> 
> 

## <a id="IndexingPolicy"></a>Określanie zasad indeksowania
W przypadku zezwolenia narzędziu do migracji na tworzenie kolekcji interfejsu SQL API usługi Azure Cosmos DB podczas importowania można określić zasady indeksowania kolekcji. W sekcji zaawansowanych opcji importu zbiorczego usługi Azure Cosmos DB i opcji sekwencyjnych rekordów usługi Azure Cosmos DB przejdź do sekcji Zasady indeksowania.

![Zrzut ekranu przedstawiający zaawansowane opcje zasad indeksowania dla usługi Azure Cosmos DB](./media/import-data/indexingpolicy1.png)

Przy użyciu zaawansowanej opcji zasad indeksowania wybierz plik zasad indeksowania, ręcznie wprowadź zasady indeksowania lub wybierz szablon z zestawu szablonów domyślnych (na przykład przez kliknięcie prawym przyciskiem myszy w polu tekstowym zasad indeksowania).

Szablony zasad udostępniane przez narzędzie to:

* Domyślne. Te zasady to najlepszy wybór w przypadku wykonywania zapytań dotyczących równości względem ciągów, a także zapytań typu ORDER BY oraz dotyczących zakresu i równości względem liczb. Mają one mniejszy narzut na przechowywanie indeksu niż szablon Zakres.
* Zakres. Te zasady to najlepszy wybór w przypadku wykonywania zapytań typu ORDER BY oraz dotyczących zakresu i równości względem liczb i ciągów. Mają one większy narzut na przechowywanie indeksu niż szablon Domyślny lub Skrót.

![Zrzut ekranu przedstawiający zaawansowane opcje zasad indeksowania dla usługi Azure Cosmos DB](./media/import-data/indexingpolicy2.png)

> [!NOTE]
> Jeśli nie określisz zasad indeksowania, zostaną zastosowane zasady domyślne. Aby uzyskać więcej informacji na temat zasad indeksowania, zobacz [Azure Cosmos DB indexing policies (Zasady indeksowania w usłudze Azure Cosmos DB)](indexing-policies.md).
> 
> 

## <a name="export-to-json-file"></a>Eksportowanie do pliku JSON
Eksporter JSON w usłudze Azure Cosmos DB umożliwia eksportowanie dowolnej z dostępnych opcji źródeł do pliku JSON, który zawiera tablicę dokumentów JSON. Narzędzie obsługuje eksport w imieniu użytkownika, ale można wybrać opcję wyświetlania wynikowego polecenia migracji i jego samodzielnego uruchamiania. Wynikowy plik JSON może być przechowywany lokalnie lub w usłudze Azure Blob Storage.

![Zrzut ekranu przedstawiający opcję eksportowania lokalnego pliku JSON usługi Azure Cosmos DB](./media/import-data/jsontarget.png)

![Zrzut ekranu przedstawiający opcję eksportowania magazynu usługi Azure Blob Storage w formacie JSON w usłudze Azure Cosmos DB](./media/import-data/jsontarget2.png)

Opcjonalnie można wybrać opcję ulepszenia wynikowego pliku JSON, co spowoduje zwiększenie rozmiaru dokumentu wynikowego, poprawiając równocześnie czytelność zawartości widocznej dla użytkownika.

    Standard JSON export
    [{"id":"Sample","Title":"About Paris","Language":{"Name":"English"},"Author":{"Name":"Don","Location":{"City":"Paris","Country":"France"}},"Content":"Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.","PageViews":10000,"Topics":[{"Title":"History of Paris"},{"Title":"Places to see in Paris"}]}]

    Prettified JSON export
    [
     {
    "id": "Sample",
    "Title": "About Paris",
    "Language": {
      "Name": "English"
    },
    "Author": {
      "Name": "Don",
      "Location": {
        "City": "Paris",
        "Country": "France"
      }
    },
    "Content": "Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.",
    "PageViews": 10000,
    "Topics": [
      {
        "Title": "History of Paris"
      },
      {
        "Title": "Places to see in Paris"
      }
    ]
    }]

## <a name="advanced-configuration"></a>Konfiguracja zaawansowana
Na ekranie konfiguracji zaawansowanej określ lokalizację pliku dziennika, w którym chcesz zapisywać błędy. Na tej stronie obowiązują następujące reguły:

1. Jeśli nie podano nazwy pliku, wszystkie błędy są zwracane na stronie wyników.
2. Jeśli podano nazwę pliku bez katalogu, plik jest tworzony (lub zastępowany) w katalogu bieżącego środowiska.
3. Jeśli wybrano istniejący plik, plik jest zastępowany i nie ma dostępnej opcji dołączania.

Następnie należy wybrać opcję rejestrowania komunikatów o błędach: wszystkich, krytycznych lub żadnych. Na koniec należy zdecydować, jak często wyświetlany na ekranie komunikat dotyczący transferu będzie aktualizowany przy użyciu informacji o jego postępie.

    ![Screenshot of Advanced configuration screen](./media/import-data/AdvancedConfiguration.png)

## <a name="confirm-import-settings-and-view-command-line"></a>Potwierdzanie ustawień importu i wyświetlanie wiersza polecenia
1. Po podaniu informacji o źródle i celu oraz konfiguracji zaawansowanej przejrzyj podsumowanie migracji i opcjonalnie wyświetl/skopiuj wynikowe polecenie migracji (kopiowanie polecenia jest przydatne w przypadku automatyzowania operacji importu):
   
    ![Zrzut ekranu przedstawiający ekran podsumowania](./media/import-data/summary.png)
   
    ![Zrzut ekranu przedstawiający ekran podsumowania](./media/import-data/summarycommand.png)
2. Po wybraniu żądanych opcji źródła i celu kliknij przycisk **Importuj**. Czas, który upłynął, liczba przetransferowanych plików i informacje o błędach (jeśli nie podano nazwy pliku w obszarze konfiguracji zaawansowanej) są aktualizowane w miarę postępu importu. Po ukończeniu możesz wyeksportować wyniki (na przykład na wypadek błędów importu).
   
    ![Zrzut ekranu przedstawiający opcję eksportowania pliku JSON usługi Azure Cosmos DB](./media/import-data/viewresults.png)
3. Możesz także uruchomić nowy import, zachowując istniejące ustawienia (np. informacje o parametrach połączenia, wybór źródła i celu itp.) lub resetując wszystkie wartości.
   
    ![Zrzut ekranu przedstawiający opcję eksportowania pliku JSON usługi Azure Cosmos DB](./media/import-data/newimport.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku wykonano następujące zadania:

> [!div class="checklist"]
> * Zainstalowano narzędzie do migracji danych
> * Zaimportowano dane z różnych źródeł danych
> * Przeprowadzono eksportowanie z usługi Azure Cosmos DB do formatu JSON

Teraz możesz przejść do następnego samouczka, aby dowiedzieć się, jak wykonywać zapytania dotyczące danych za pomocą usługi Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Jak wykonywać zapytania dotyczące danych?](../cosmos-db/tutorial-query-sql-api.md)
