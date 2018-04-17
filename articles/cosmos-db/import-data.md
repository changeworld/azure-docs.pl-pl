---
title: Narzędzie migracji bazy danych dla bazy danych Azure rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać narzędzia migracji danych bazy danych Azure rozwiązania Cosmos typu open source do importowania danych do bazy danych Azure rozwiązania Cosmos z różnych źródeł, takich jak pliki bazy danych MongoDB, SQL Server tabeli magazynu, Amazon DynamoDB, CSV i JSON. CSV do konwersji do formatu JSON.
keywords: CSV do formatu json, narzędzi migracji bazy danych, przekonwertować csv do ciągu json
services: cosmos-db
author: andrewhoh
manager: kfile
editor: monicar
documentationcenter: ''
ms.assetid: d173581d-782a-445c-98d9-5e3c49b00e25
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: anhoh
ms.custom: mvc
ms.openlocfilehash: 9c4908775ab9a471201cc4bb0c35ab863f5d6d50
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmos-db-data-migration-tool"></a>Azure rozwiązania Cosmos bazy danych: Narzędzie migracji danych

W tym samouczku instrukcje na temat używania narzędzia migracji danych DB rozwiązania Cosmos Azure, które można importować dane z różnych źródeł do bazy danych Azure rozwiązania Cosmos kolekcje i tabele. Można importować z JSON, CSV pliki, SQL, bazy danych MongoDB, tabel Azure magazynu, Amazon DynamoDB, plików i nawet kolekcje interfejsu API Azure rozwiązania Cosmos bazy danych SQL i migracji, czy dane do kolekcji i tabel dla używania z usługą Azure DB rozwiązania Cosmos. Narzędzie do migracji danych można również podczas migracji z kolekcji jednej partycji do kolekcji wielu partycji dla interfejsu API SQL.

API, które będą korzystać z bazy danych rozwiązania Cosmos Azure? 
* **[Interfejsu API SQL](documentdb-introduction.md)**  — można użyć opcji źródła w narzędzia migracji danych do importowania danych.
* **[Tabela interfejsu API](table-introduction.md)**  — narzędzia do migracji danych lub narzędzia AzCopy można użyć do importowania danych. Zobacz [importowania danych do użycia z interfejsu API Azure rozwiązania Cosmos DB tabeli](table-import.md) Aby uzyskać więcej informacji.
* **[Interfejs API bazy danych MongoDB](mongodb-introduction.md)**  — narzędzia do migracji danych aktualnie nie obsługuje interfejsu API Azure rozwiązania Cosmos bazy danych MongoDB jako źródło lub jako element docelowy. Jeśli chcesz przeprowadzić migrację danych do lub z kolekcji bazy danych MongoDB interfejsu API w usłudze Azure DB rozwiązania Cosmos odwołują się do [bazy danych Azure rozwiązania Cosmos: jak przeprowadzić migrację danych dla bazy danych MongoDB interfejsu API](mongodb-migrate.md) instrukcje. Do eksportowania danych z bazy danych MongoDB do interfejsu API Azure rozwiązania Cosmos bazy danych SQL kolekcji do użycia przy użyciu interfejsu API SQL, można nadal używać narzędzia migracji danych. 
* **[Interfejs API programu Graph](graph-introduction.md)**  — narzędzia do migracji danych nie jest narzędziem obsługiwanych importu dla interfejsu API programu Graph kont w tym momencie. 

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Instalacja narzędzia do migracji danych
> * Importowanie danych z różnych źródeł danych
> * Eksportowanie z Azure rozwiązania Cosmos bazy danych do formatu JSON

## <a id="Prerequisites"></a>Wymagania wstępne
Przed wykonaniem instrukcji zawartych w tym artykule, upewnij się, że masz następujące elementy:

* [Program Microsoft .NET Framework 4.51](https://www.microsoft.com/download/developer-tools.aspx) lub nowszej.

## <a id="Overviewl"></a>Omówienie
Narzędzie do migracji danych jest rozwiązaniem typu open source, który importuje dane do bazy danych Azure rozwiązania Cosmos z różnych źródeł, takich jak:

* Pliki JSON
* MongoDB
* Oprogramowanie SQL Server
* Pliki CSV
* Azure Table Storage
* Amazon DynamoDB
* HBase
* Kolekcje usługi Azure DB rozwiązania Cosmos

Podczas importowania narzędziu graficznego interfejsu użytkownika (dtui.exe), mogą być określane także z poziomu wiersza polecenia (dt.exe). W rzeczywistości ma opcji output skojarzone polecenie po skonfigurowaniu importu za pośrednictwem interfejsu użytkownika. Tabelaryczne źródła danych (np. programu SQL Server lub plików CSV) można je przekształcać w taki sposób, że relacje hierarchiczne (dokumentów podrzędnych) można utworzyć podczas importowania. Zachowaj odczytu, aby dowiedzieć się więcej o opcjach źródła, przykładowe wiersze polecenia do importowania z każdego źródła, target — opcje i importowanie wyświetlanie wyników.

## <a id="Install"></a>Instalacja
Kod źródłowy narzędzie migracji jest dostępne w witrynie GitHub w [to repozytorium](https://github.com/azure/azure-documentdb-datamigrationtool). Możesz pobrać i Skompiluj rozwiązanie lokalnie, lub [Pobierz wstępnie skompilowanym plikiem binarnym](https://cosmosdbportalstorage.blob.core.windows.net/datamigrationtool/2018.02.28-1.8.1/dt-1.8.1.zip), następnie uruchom dowolne:

* **Dtui.exe**: wersja interfejsu graficznego narzędzia
* **DT.exe**: wersja narzędzia wiersza polecenia

## <a name="select-data-source"></a>Wybierz źródło danych

Po zainstalowaniu narzędzia jest czas w celu zaimportowania danych. Jakiego rodzaju dane chcesz importować?

* [Pliki w formacie JSON](#JSON)
* [MongoDB](#MongoDB)
* [Pliki eksportu bazy danych MongoDB](#MongoDBExport)
* [SQL Server](#SQL)
* [Pliki CSV](#CSV)
* [Azure Table storage](#AzureTableSource)
* [Amazon DynamoDB](#DynamoDBSource)
* [Blob](#BlobImport)
* [Kolekcje usługi Azure DB rozwiązania Cosmos](#SQLSource)
* [HBase](#HBaseSource)
* [Importowania zbiorczego w usłudze Azure DB rozwiązania Cosmos](#SQLBulkImport)
* [Importowania platformy Azure kolejny rekord DB rozwiązania Cosmos](#DocumentDSeqTarget)


## <a id="JSON"></a>Zaimportuj pliki w formacie JSON
Opcja importera źródło pliku JSON umożliwia import jeden lub więcej plików JSON pojedynczego dokumentu lub pliki JSON, że każdy zawierać tablicę dokumentów JSON. Podczas dodawania foldery zawierające pliki w formacie JSON do zaimportowania, istnieje możliwość rekursywnie szukania plików w podfolderach.

![Opcje źródłowego pliku zrzut ekranu JSON - narzędzia migracji bazy danych](./media/import-data/jsonsource.png)

Oto niektóre przykłady wiersza polecenia, aby zaimportować pliki w formacie JSON:

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

## <a id="MongoDB"></a>Importuj z bazy danych MongoDB

> [!IMPORTANT]
> Jeśli import odbywa się do konta bazy danych Azure rozwiązania Cosmos z obsługą bazy danych MongoDB, postępuj zgodnie z następującymi [instrukcje](mongodb-migrate.md).
> 
> 

Opcja importera źródła bazy danych MongoDB służy do importowania z poszczególnych kolekcji bazy danych MongoDB i opcjonalnie filtrować dokumentów za pomocą zapytania i/lub modyfikowanie strukturę dokumentu przy użyciu projekcji.  

![Zrzut ekranu MongoDB Opcje źródła](./media/import-data/mongodbsource.png)

Ciąg połączenia jest w standardowym formacie bazy danych MongoDB:

    mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database>

> [!NOTE]
> Aby upewnić się, że wystąpienie bazy danych MongoDB, określonego w polu Parametry połączenia są dostępne, należy użyć polecenia Sprawdź.
> 
> 

Wprowadź nazwę kolekcji, w którym dane zostaną zaimportowane. Opcjonalnie możesz określić lub udostępnić plik dla zapytania (np. {pop: {$gt: 5000}}) i/lub projekcji (np. {loc:0}) do filtrowania i dane do zaimportowania kształtu.

Oto niektóre przykłady wiersza polecenia, można zaimportować z bazy danych MongoDB:

    #Import all documents from a MongoDB collection
    dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZips /t.IdField:_id /t.CollectionThroughput:2500

    #Import documents from a MongoDB collection which match the query and exclude the loc field
    dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /s.Query:{pop:{$gt:50000}} /s.Projection:{loc:0} /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZipsTransform /t.IdField:_id/t.CollectionThroughput:2500

## <a id="MongoDBExport"></a>Importowanie plików eksportu bazy danych MongoDB

> [!IMPORTANT]
> Jeśli import odbywa się do konta bazy danych Azure rozwiązania Cosmos z obsługą bazy danych MongoDB, postępuj zgodnie z następującymi [instrukcje](mongodb-migrate.md).
> 
> 

Opcja importera źródło pliku bazy danych MongoDB eksportowania JSON pozwala zaimportować co najmniej jeden plik JSON utworzony z narzędzia mongoexport.  

![Opcje źródła eksportu zrzut ekranu z bazy danych MongoDB](./media/import-data/mongodbexportsource.png)

Podczas dodawania foldery zawierające pliki w formacie JSON eksportu bazy danych MongoDB do zaimportowania, istnieje możliwość rekursywnie szukania plików w podfolderach.

Oto przykład wiersza polecenia do importowania z bazy danych MongoDB eksportu JSON plików:

    dt.exe /s:MongoDBExport /s.Files:D:\mongoemployees.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:employees /t.IdField:_id /t.Dates:Epoch /t.CollectionThroughput:2500

## <a id="SQL"></a>Importuj z programu SQL Server
Opcja importera źródła SQL umożliwia importowanie z poszczególnych bazy danych programu SQL Server i opcjonalnie filtrowanie rekordów do zaimportowania przy użyciu zapytania. Ponadto można zmodyfikować strukturę dokumentu, określając zagnieżdżenia separatora (więcej informacji na temat którego za chwilę).  

![Zrzut ekranu SQL źródła opcji - narzędzia migracji bazy danych](./media/import-data/sqlexportsource.png)

Format ciągu połączenia jest standardowy format parametrów połączenia SQL.

> [!NOTE]
> Aby upewnić się, że wystąpienie serwera SQL określony w polu Parametry połączenia są dostępne, należy użyć polecenia Sprawdź.
> 
> 

Właściwość separatora zagnieżdżenia służy do tworzenia hierarchicznych (podrzędne dokumenty) podczas importowania. Należy wziąć pod uwagę następujące zapytanie SQL:

*Wybierz RZUTOWANIA (BusinessEntityID AS varchar) jako identyfikator, nazwę, typ adresu jako [Address.AddressType], AddressLine1 jako [Address.AddressLine1], miasta jako [Address.Location.City], StateProvinceName jako [Address.Location.StateProvinceName], KodPocztowy jako [ Address.PostalCode] CountryRegionName jako [Address.CountryRegionName] z Sales.vStoreWithAddresses gdzie typ adresu = "Siedzibą główną"*

Która zwraca następujące wyniki (częściowe):

![Zrzut ekranu SQL wyników zapytania](./media/import-data/sqlqueryresults.png)

Należy pamiętać, aliasy, takie jak Address.AddressType i Address.Location.StateProvinceName. Określając zagnieżdżenia separatora z ".", narzędzia importu tworzy adres i Address.Location dokumentów podrzędnych podczas importowania. Oto przykład wynikowy dokumentu w usłudze Azure DB rozwiązania Cosmos:

*{"id": "956", "Name": "Bardziej precyzyjną sprzedaży i usługa", "Adres": {"Typ adresu": "Urząd główny", "AddressLine1": "#500 75 O'Connor ulicy", "Lokalizacja": {"Miasto": "Ottawie", "StateProvinceName": "Ontario"}, "KodPocztowy": "K4B 1S2", "CountryRegionName": " Kanada"}}*

Oto niektóre przykłady wiersza polecenia do importowania z programu SQL Server:

    #Import records from SQL which match a query
    dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, * from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Stores /t.IdField:Id /t.CollectionThroughput:2500

    #Import records from sql which match a query and create hierarchical relationships
    dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /s.NestingSeparator:. /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:StoresSub /t.IdField:Id /t.CollectionThroughput:2500

## <a id="CSV"></a>Importowanie plików CSV i konwertowanie woluminu CSV do ciągu JSON
Opcja importera źródło pliku CSV umożliwia importowanie jeden lub więcej plików CSV. Podczas dodawania foldery zawierające pliki CSV do zaimportowania, istnieje możliwość rekursywnie szukania plików w podfolderach.

![Zrzut ekranu CSV źródła opcji - CSV do ciągu JSON](media/import-data/csvsource.png)

Podobnie jak źródło SQL, zagnieżdżenia właściwości separatora to może służyć do tworzenia hierarchicznych (podrzędne dokumenty) podczas importowania. Rozważmy następujący nagłówek CSV wierszy wiersza i dane:

![Zrzut ekranu CSV przykładowych rekordów — CSV do ciągu JSON](./media/import-data/csvsample.png)

Należy pamiętać, aliasy, takie jak DomainInfo.Domain_Name i RedirectInfo.Redirecting. Określając zagnieżdżenia separatora z ".", narzędzia importu utworzy DomainInfo i RedirectInfo dokumentów podrzędnych podczas importowania. Oto przykład wynikowy dokumentu w usłudze Azure DB rozwiązania Cosmos:

*{"DomainInfo": {"Nazwa_domeny": "ACUS.GOV", "Domain_Name_Address": "http://www.ACUS.GOV"}, "Federalne agencji": "administracyjne konferencji ze Stanów Zjednoczonych", "RedirectInfo": {"Przekierowywanie": "0", "Redirect_Destination": ""}, "id": " 9cc565c5-ebcd-1c03-ebd3-cc3e2ecd814d"}*

Narzędzia importu próbuje rozpoznać informacji o typie wartości bez cudzysłowów w plikach CSV (wartości w cudzysłowie zawsze są traktowane jako ciągi).  Typy są identyfikowane w następującej kolejności: numer, datetime, boolean.  

Istnieją dwie czynności uwagi na temat importowania danych CSV:

1. Domyślnie bez cudzysłowów wartości są zawsze usuwane dla kart i spacje, gdy wartości w cudzysłowie są zachowywane jako — jest. To zachowanie może zostać zastąpiona przez opcji wiersza polecenia /s.TrimQuoted lub wyboru przycinania wartości w cudzysłowie.
2. Domyślnie bez cudzysłowów wartość null jest traktowana jako wartość null. To zachowanie można przesłonić (to znaczy Traktuj bez cudzysłowów null jako ciąg "null") z Traktuj nienotowane NULL jako ciąg wyboru lub /s.NoUnquotedNulls opcja wiersza polecenia.

Oto przykład wiersza polecenia do importowania danych CSV:

    dt.exe /s:CsvFile /s.Files:.\Employees.csv /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Employees /t.IdField:EntityID /t.CollectionThroughput:2500

## <a id="AzureTableSource"></a>Importuj z magazynem tabel Azure
Opcja importera źródła magazynu tabel Azure umożliwia importowanie z pojedynczej tabeli magazynu tabel Azure. Opcjonalnie można filtrować jednostek tabeli do zaimportowania. 

Dane importowane z magazynu tabel Azure może być dane wyjściowe do bazy danych Azure rozwiązania Cosmos tabel i jednostek, do użycia przy użyciu interfejsu API tabeli, lub do kolekcji i dokumentów do użycia przy użyciu interfejsu API SQL. Jednak; Tabela interfejsu API tylko jest dostępna jako miejsce docelowe w narzędziu wiersza polecenia, nie można wyeksportować do interfejsu API tabeli za pomocą interfejsu użytkownika narzędzia migracji danych. Aby uzyskać więcej informacji, zobacz [importowania danych do użycia z interfejsu API Azure rozwiązania Cosmos DB tabeli](table-import.md). 

![Opcje źródłowego magazynu tabel zrzut ekranu Azure](./media/import-data/azuretablesource.png)

Format ciągu połączenia magazynu tabel Azure to:

    DefaultEndpointsProtocol=<protocol>;AccountName=<Account Name>;AccountKey=<Account Key>;

> [!NOTE]
> Aby upewnić się, że wystąpienie magazynu tabel Azure, określonego w polu Parametry połączenia są dostępne, należy użyć polecenia Sprawdź.
> 
> 

Wprowadź nazwę tabeli platformy Azure z importowanego. Opcjonalnie można określić [filtru](https://msdn.microsoft.com/library/azure/ff683669.aspx).

Opcja importera źródła magazynu tabel Azure ma następujące dodatkowe opcje:

1. Uwzględnij pola wewnętrznego
   1. -Obejmują wszystkie pola wewnętrzne (PartitionKey RowKey i sygnatura czasowa)
   2. Brak — Wyklucz wszystkie pola wewnętrznego
   3. RowKey - zawierać tylko pola RowKey
2. Wybieranie kolumn
   1. Filtrów magazynu tabel Azure nie obsługuje prognoz. Jeśli chcesz importować tylko określonych właściwości jednostki tabel Azure, dodaj je do listy wybierz kolumny. Wszystkie właściwości obiektu są ignorowane.

Oto przykład wiersza polecenia do importowania z magazynem tabel Azure:

    dt.exe /s:AzureTable /s.ConnectionString:"DefaultEndpointsProtocol=https;AccountName=<Account Name>;AccountKey=<Account Key>" /s.Table:metrics /s.InternalFields:All /s.Filter:"PartitionKey eq 'Partition1' and RowKey gt '00001'" /s.Projection:ObjectCount;ObjectSize  /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:metrics /t.CollectionThroughput:2500

## <a id="DynamoDBSource"></a>Importuj z usługi Amazon DynamoDB
Opcja importera źródła Amazon DynamoDB służy do importowania z pojedynczej tabeli Amazon DynamoDB i opcjonalnie filtrować jednostki do zaimportowania. Kilka szablonów znajdują się tak, aby ustawienie importu jest równie proste, jak to możliwe.

![Zrzut ekranu Amazon DynamoDB Opcje źródła - narzędzia migracji bazy danych](./media/import-data/dynamodbsource1.png)

![Zrzut ekranu Amazon DynamoDB Opcje źródła - narzędzia migracji bazy danych](./media/import-data/dynamodbsource2.png)

Format ciągu połączenia Amazon DynamoDB jest:

    ServiceURL=<Service Address>;AccessKey=<Access Key>;SecretKey=<Secret Key>;

> [!NOTE]
> Użyj polecenia Sprawdź, aby upewnić się, że wystąpienia Amazon DynamoDB, określonego w polu Parametry połączenia są dostępne.
> 
> 

Oto przykład wiersza polecenia do importowania z usługi Amazon DynamoDB:

    dt.exe /s:DynamoDB /s.ConnectionString:ServiceURL=https://dynamodb.us-east-1.amazonaws.com;AccessKey=<accessKey>;SecretKey=<secretKey> /s.Request:"{   """TableName""": """ProductCatalog""" }" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<Azure Cosmos DB Endpoint>;AccountKey=<Azure Cosmos DB Key>;Database=<Azure Cosmos DB Database>;" /t.Collection:catalogCollection /t.CollectionThroughput:2500

## <a id="BlobImport"></a>Importuj z magazynu obiektów Blob platformy Azure
Plik JSON, plik eksportu bazy danych MongoDB i opcje importera źródło pliku CSV można importować jeden lub więcej plików z magazynu obiektów Blob platformy Azure. Po określeniu adresu URL kontenera obiektów Blob i klucz konta usługi, należy podać wyrażenie regularne, aby wybrać pliki do zaimportowania.

![Opcje źródłowego pliku zrzutu ekranu obiektów Blob](./media/import-data/blobsource.png)

Oto przykład wiersza polecenia, aby zaimportować pliki w formacie JSON z magazynu obiektów Blob platformy Azure:

    dt.exe /s:JsonFile /s.Files:"blobs://<account key>@account.blob.core.windows.net:443/importcontainer/.*" /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:doctest

## <a id="SQLSource"></a>Importuj z kolekcji interfejsu API SQL
Opcja importera źródłowej bazy danych Azure rozwiązania Cosmos umożliwia importowanie danych z jednej lub kilku kolekcjach bazy danych Azure rozwiązania Cosmos i opcjonalnie filtrowania dokumentów za pomocą zapytania.  

![Opcje źródła zrzut ekranu z rozwiązania Cosmos bazy danych Azure](./media/import-data/documentdbsource.png)

Format ciągu połączenia bazy danych Azure rozwiązania Cosmos jest:

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

Parametry połączenia bazy danych Azure rozwiązania Cosmos konta można pobrać ze strony klucze w portalu Azure, zgodnie z opisem w [jak zarządzać konta bazy danych Azure rozwiązania Cosmos](manage-account.md), ale nazwa bazy danych musi być dołączony do ciągu połączenia w następującym formacie:

    Database=<CosmosDB Database>;

> [!NOTE]
> Aby upewnić się, że wystąpienie bazy danych Azure rozwiązania Cosmos określoną w polu Parametry połączenia są dostępne, należy użyć polecenia Sprawdź.
> 
> 

Aby zaimportować z jednej bazy danych Azure rozwiązania Cosmos kolekcji, wprowadź nazwę kolekcji do importowania danych z. Aby zaimportować z wielu kolekcji bazy danych Azure rozwiązania Cosmos, podaj wyrażenia regularnego do dopasowania co najmniej jedną nazwę kolekcji (na przykład collection01 | collection02 | collection03). Możesz Opcjonalnie określ lub udostępnić plik dla zapytania do filtrowania i kształtu dane do zaimportowania.

> [!NOTE]
> Ponieważ pola kolekcji akceptuje wyrażeń regularnych, jeśli import odbywa się z jednej kolekcji, których nazwa zawiera znaki wyrażenie regularne, te znaki muszą wyjściowym odpowiednio.
> 
> 

Opcja bazy danych Azure rozwiązania Cosmos źródła importer ma następujące opcje zaawansowane:

1. Obejmują pola wewnętrzne: Określa, czy do dołączenia bazy danych Azure rozwiązania Cosmos dokument, właściwości systemu eksportu (na przykład _rid, _ts).
2. Liczba ponownych prób w przypadku niepowodzenia: Określa liczbę ponownych prób połączenia do bazy danych Azure rozwiązania Cosmos w przypadku błędów przejściowych (na przykład łączności przerwy w działaniu sieci).
3. Interwał ponawiania prób: Określa, jak długo czekać między ponowieniem próby połączenia do bazy danych Azure rozwiązania Cosmos w przypadku błędów przejściowych (na przykład łączności przerwy w działaniu sieci).
4. Tryb połączenia: Określa tryb połączenia do korzystania z bazy danych Azure rozwiązania Cosmos. Dostępne opcje to DirectTcp, DirectHttps i bramy. Tryby bezpośredniego połączenia są szybsze, przy włączonym trybie bramy zapory więcej przyjazną jak tylko używa portu 443.

![Zaawansowane opcje źródła zrzut ekranu z rozwiązania Cosmos bazy danych Azure](./media/import-data/documentdbsourceoptions.png)

> [!TIP]
> Narzędzie importu domyślnie tryb połączenia DirectTcp. Jeśli występują problemy z zaporą, Przełącz do trybu połączenia bramy, ponieważ wymaga ona tylko portu 443.
> 
> 

Oto niektóre przykłady wiersza polecenia do importowania z bazy danych Azure rozwiązania Cosmos:

    #Migrate data from one Azure Cosmos DB collection to another Azure Cosmos DB collections
    dt.exe /s:CosmosDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:TEColl /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:TESessions /t.CollectionThroughput:2500

    #Migrate data from multiple Azure Cosmos DB collections to a single Azure Cosmos DB collection
    dt.exe /s:CosmosDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:comp1|comp2|comp3|comp4 /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:singleCollection /t.CollectionThroughput:2500

    #Export an Azure Cosmos DB collection to a JSON file
    dt.exe /s:CosmosDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:StoresSub /t:JsonFile /t.File:StoresExport.json /t.Overwrite /t.CollectionThroughput:2500

> [!TIP]
> Narzędzie importowania danych DB rozwiązania Cosmos Azure obsługuje również importowanie danych z [Azure rozwiązania Cosmos DB emulatora](local-emulator.md). Podczas importowania danych z lokalnego emulatora, ustawić punkt końcowy `https://localhost:<port>`. 
> 
> 

## <a id="HBaseSource"></a>Importuj z bazy danych HBase
Opcja importera źródła HBase służy do importowania danych z tabeli HBase i opcjonalnie filtrowania danych. Kilka szablonów znajdują się tak, aby ustawienie importu jest równie proste, jak to możliwe.

![Zrzut ekranu HBase Opcje źródła](./media/import-data/hbasesource1.png)

![Zrzut ekranu HBase Opcje źródła](./media/import-data/hbasesource2.png)

Format ciągu połączenia bazy danych HBase Stargate jest:

    ServiceURL=<server-address>;Username=<username>;Password=<password>

> [!NOTE]
> Użyj polecenia Sprawdź, aby upewnić się, że wystąpienie bazy danych HBase, określoną w polu Parametry połączenia są dostępne.
> 
> 

Oto przykład wiersza polecenia do importowania z bazy danych HBase:

    dt.exe /s:HBase /s.ConnectionString:ServiceURL=<server-address>;Username=<username>;Password=<password> /s.Table:Contacts /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:hbaseimport

## <a id="SQLBulkTarget"></a>Importuj do interfejsu API SQL (importowania zbiorczego)
Importer zbiorczego DB rozwiązania Cosmos Azure umożliwia zaimportowanie od dowolnej spośród opcji dostępnego źródła przy użyciu procedury przechowywane bazy danych rozwiązania Cosmos Azure w celu zwiększenia wydajności. Narzędzie obsługuje importu do jednej kolekcji podzielone na partycje pojedynczej bazy danych Azure rozwiązania Cosmos, a także podzielonej importu, zgodnie z którymi danych jest podzielonym na partycje w wielu kolekcji podzielone na partycje pojedynczej bazy danych Azure rozwiązania Cosmos. Aby uzyskać więcej informacji na temat partycjonowania danych, zobacz [dzielenia na partycje i skalowania w usłudze Azure DB rozwiązania Cosmos](partition-data.md). Narzędzie tworzy, wykonuje, a następnie usuwa procedury składowanej kolekcji docelowej.  

![Opcje zbiorczego zrzut ekranu z rozwiązania Cosmos bazy danych Azure](./media/import-data/documentdbbulk.png)

Format ciągu połączenia bazy danych Azure rozwiązania Cosmos jest:

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

Parametry połączenia bazy danych Azure rozwiązania Cosmos konta można pobrać ze strony klucze w portalu Azure, zgodnie z opisem w [jak zarządzać konta bazy danych Azure rozwiązania Cosmos](manage-account.md), ale nazwa bazy danych musi być dołączony do ciągu połączenia w następującym formacie:

    Database=<CosmosDB Database>;

> [!NOTE]
> Aby upewnić się, że wystąpienie bazy danych Azure rozwiązania Cosmos określoną w polu Parametry połączenia są dostępne, należy użyć polecenia Sprawdź.
> 
> 

Aby zaimportować do jednej kolekcji, wprowadź nazwę kolekcji do importowania danych z i kliknij przycisk Dodaj. Aby zaimportować do wielu kolekcji, wprowadź nazwy kolekcji indywidualnie lub należy użyć następującej składni, aby określić wiele kolekcji: *collection_prefix*[Indeks - end indeks początkowy]. Podczas określania wielu kolekcji za pomocą składni wyżej wymienione, pamiętać o następujących wytycznych:

1. Obsługiwane są tylko liczby całkowitej wzorce nazwy zakresu. Na przykład określenie kolekcji [0-3] tworzy następujące kolekcje: collection0, collection1, collection2, collection3.
2. Za pomocą składni skróconej: [3] kolekcji tworzy ten sam zestaw kolekcje wymienionych w kroku 1.
3. Można podać więcej niż jeden podstawienia. Na przykład, Kolekcja [0-1] [0-9] generuje 20 nazwy kolekcji z zerami (collection01,... 02... 03).

Po wskazaniu nazwy kolekcji, wybierz żądany przepływność kolekcji (RUs 400 do 10 000 RUs). Aby uzyskać najlepszą wydajność importu wybierz wyższej przepustowości. Aby uzyskać więcej informacji na temat poziomów wydajności, zobacz [poziomy wydajności w usłudze Azure DB rozwiązania Cosmos](performance-levels.md).

> [!NOTE]
> Ustawienia wydajności przepływności ma zastosowanie tylko do tworzenia kolekcji. Jeśli istnieje już określonej kolekcji, jego przepływności nie jest modyfikowany.
> 
> 

Podczas importowania do wielu kolekcji, narzędzia importu obsługuje skrótu na podstawie dzielenia na fragmenty. W tym scenariuszu, należy określić właściwość dokumentu chcesz użyć jako klucza partycji (Jeśli klucz partycji jest puste, dokumenty są podzielonej losowo w kolekcji docelowej).

Może opcjonalnie określić, które pole w źródle importu będzie używana jako właściwość identyfikatora dokumentu bazy danych Azure rozwiązania Cosmos podczas importowania (należy pamiętać, że jeśli dokumenty nie zawierają tej właściwości, następnie narzędzia importu generuje identyfikator GUID jako wartość właściwości identyfikator).

Brak dostępnych kilka opcji zaawansowanych podczas importowania. Najpierw podczas to narzędzie zawiera procedury składowanej (BulkInsert.js) importowania zbiorczego domyślne, możesz określić własne Procedura importowania przechowywane:

 ![Opcje sproc wstawiania zbiorczego zrzut ekranu z rozwiązania Cosmos bazy danych Azure](./media/import-data/bulkinsertsp.png)

Ponadto podczas importowania typów danych (na przykład z programu SQL Server lub bazy danych MongoDB), można wybrać jedną z trzech opcji importowania:

 ![Opcje importowania czasu Data zrzut ekranu z rozwiązania Cosmos bazy danych Azure](./media/import-data/datetimeoptions.png)

* Ciąg: Wartość ciągu utrzymana
* Epoka: Utrwalić jako wartość liczbową epoka.
* Zarówno: Utrwalanie zarówno ciąg, jak i epoki wartości liczbowe. Opcja ta tworzy podrzędnego, na przykład: "date_joined": {"Value": "2013-10-21T21:17:25.2410000Z", "epoki": 1382390245}

Importer zbiorczego DB rozwiązania Cosmos Azure ma następujące dodatkowe opcje zaawansowane:

1. Rozmiar partii: Narzędzie domyślnie rozmiar partii 50.  W przypadku dużych dokumentów do zaimportowania należy rozważyć, co zmniejsza rozmiar partii. Z drugiej strony w przypadku małych dokumentów do zaimportowania należy rozważyć zwiększenie rozmiaru partii.
2. Maksymalny rozmiar skryptu (w bajtach): domyślne narzędzie do skryptu Maksymalny rozmiar 512 KB.
3. Wyłącz automatyczne generowanie identyfikator: Jeśli każdy dokument do zaimportowania zawiera pole identyfikatora, wybierając tę opcję można zwiększyć wydajność. Brak pola Unikatowy identyfikator dokumenty nie zostaną zaimportowane.
4. Aktualizacja istniejące dokumenty: Narzędzie domyślnie nie zastąpienie istniejących dokumentów konflikt identyfikatorów. Wybranie tej opcji pozwala na zastępowanie istniejących dokumentów ze zgodnymi identyfikatorami. Ta funkcja jest przydatne w przypadku migracji danych zaplanowane, które zaktualizować istniejące dokumenty.
5. Liczba ponownych prób w przypadku niepowodzenia: Określa liczbę ponownych prób połączenia do bazy danych Azure rozwiązania Cosmos w przypadku błędów przejściowych (na przykład łączności przerwy w działaniu sieci).
6. Interwał ponawiania prób: Określa, jak długo czekać między ponowieniem próby połączenia do bazy danych Azure rozwiązania Cosmos w przypadku błędów przejściowych (na przykład łączności przerwy w działaniu sieci).
7. Tryb połączenia: Określa tryb połączenia do korzystania z bazy danych Azure rozwiązania Cosmos. Dostępne opcje to DirectTcp, DirectHttps i bramy. Tryby bezpośredniego połączenia są szybsze, przy włączonym trybie bramy zapory więcej przyjazną jak tylko używa portu 443.

![Zaawansowane opcje importowania zbiorczego zrzut ekranu z rozwiązania Cosmos bazy danych Azure](./media/import-data/docdbbulkoptions.png)

> [!TIP]
> Narzędzie importu domyślnie tryb połączenia DirectTcp. Jeśli występują problemy z zaporą, Przełącz do trybu połączenia bramy, ponieważ wymaga ona tylko portu 443.
> 
> 

## <a id="SQLSeqTarget"></a>Importuj do interfejsu API SQL (importu sekwencyjnych rekordu)
Importer sekwencyjnych rekordu bazy danych rozwiązania Cosmos Azure umożliwia zaimportowanie z Opcje dostępnego źródła na podstawie rekordu na podstawie. Możesz wybrać tę opcję, jeśli importowany do istniejącej kolekcji, która osiągnęła limit przydziału procedur składowanych. Narzędzie obsługuje importu kolekcji rozwiązania Cosmos Azure DB jednym (jednej partycji i wielu partycji), a także podzielonej importu, zgodnie z którymi danych jest podzielona na partycje w wielu kolekcjach bazy danych Azure rozwiązania Cosmos jednej partycji i/lub wielu partycji. Aby uzyskać więcej informacji na temat partycjonowania danych, zobacz [dzielenia na partycje i skalowania w usłudze Azure DB rozwiązania Cosmos](partition-data.md).

![Zrzut ekranu Azure DB rozwiązania Cosmos opcje sekwencyjnych importowania rekordów](./media/import-data/documentdbsequential.png)

Format ciągu połączenia bazy danych Azure rozwiązania Cosmos jest:

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

Parametry połączenia bazy danych Azure rozwiązania Cosmos konta można pobrać ze strony klucze w portalu Azure, zgodnie z opisem w [jak zarządzać konta bazy danych Azure rozwiązania Cosmos](manage-account.md), ale nazwa bazy danych musi być dołączony do ciągu połączenia w następującym formacie:

    Database=<Azure Cosmos DB Database>;

> [!NOTE]
> Aby upewnić się, że wystąpienie bazy danych Azure rozwiązania Cosmos określoną w polu Parametry połączenia są dostępne, należy użyć polecenia Sprawdź.
> 
> 

Aby zaimportować do jednej kolekcji, wprowadź nazwę kolekcji, do którego dane zostaną zaimportowane i kliknij przycisk Dodaj. Aby zaimportować do wielu kolekcji, wprowadź nazwy kolekcji indywidualnie lub należy użyć następującej składni, aby określić wiele kolekcji: *collection_prefix*[Indeks - end indeks początkowy]. Podczas określania wielu kolekcji za pomocą składni wyżej wymienione, pamiętać o następujących wytycznych:

1. Obsługiwane są tylko liczby całkowitej wzorce nazwy zakresu. Na przykład określenie kolekcji [0-3] tworzy następujące kolekcje: collection0, collection1, collection2, collection3.
2. Za pomocą składni skróconej: [3] kolekcji tworzy ten sam zestaw kolekcje wymienionych w kroku 1.
3. Można podać więcej niż jeden podstawienia. Na przykład, Kolekcja [0-1] [0-9] tworzy 20 nazwy kolekcji z zerami (collection01,... 02... 03).

Po wskazaniu nazwy kolekcji, wybierz żądany przepływność kolekcji (RUs 400 do 250 000 RUs). Aby uzyskać najlepszą wydajność importu wybierz wyższej przepustowości. Aby uzyskać więcej informacji na temat poziomów wydajności, zobacz [poziomy wydajności w usłudze Azure DB rozwiązania Cosmos](performance-levels.md). Wszelkie importu do kolekcji o przepływności > 10 000 RUs wymagają klucza partycji. Jeśli wybierzesz więcej niż 250 000 RUs, należy do pliku żądania w portalu, aby zwiększyć konta.

> [!NOTE]
> Ustawienie przepływności ma zastosowanie tylko do tworzenia kolekcji. Jeśli istnieje już określonej kolekcji, jego przepływności nie zostaną zmodyfikowane.
> 
> 

Podczas importowania do wielu kolekcji, narzędzia importu obsługuje skrótu na podstawie dzielenia na fragmenty. W tym scenariuszu, należy określić właściwość dokumentu chcesz użyć jako klucza partycji (Jeśli klucz partycji jest puste, dokumenty są podzielonej losowo w kolekcji docelowej).

Może opcjonalnie określić, które pole w źródle importu będzie używana jako właściwość identyfikatora dokumentu bazy danych Azure rozwiązania Cosmos podczas importowania (należy pamiętać, że jeśli dokumenty nie zawierają tej właściwości, następnie narzędzia importu generuje identyfikator GUID jako wartość właściwości identyfikator).

Brak dostępnych kilka opcji zaawansowanych podczas importowania. Najpierw podczas importowania typów danych (na przykład z programu SQL Server lub bazy danych MongoDB), można wybrać jedną z trzech opcji importowania:

 ![Opcje importowania czasu Data zrzut ekranu z rozwiązania Cosmos bazy danych Azure](./media/import-data/datetimeoptions.png)

* Ciąg: Wartość ciągu utrzymana
* Epoka: Utrwalić jako wartość liczbową epoka.
* Zarówno: Utrwalanie zarówno ciąg, jak i epoki wartości liczbowe. Opcja ta tworzy podrzędnego, na przykład: "date_joined": {"Value": "2013-10-21T21:17:25.2410000Z", "epoki": 1382390245}

Azure rozwiązania Cosmos DB — importer kolejny rekord ma następujące dodatkowe opcje zaawansowane:

1. Liczba żądań równoległych: domyślne narzędzie do dwóch równoległych żądań. W przypadku małych dokumentów do zaimportowania należy rozważyć zwiększenie numeru równoległych żądań. Należy pamiętać, że jeśli ta liczba jest zbyt dużo wywołane, importu mogą wystąpić ograniczenia przepustowości.
2. Wyłącz automatyczne generowanie identyfikator: Jeśli każdy dokument do zaimportowania zawiera pole identyfikatora, wybierając tę opcję można zwiększyć wydajność. Brak pola Unikatowy identyfikator dokumenty nie zostaną zaimportowane.
3. Aktualizacja istniejące dokumenty: Narzędzie domyślnie nie zastąpienie istniejących dokumentów konflikt identyfikatorów. Wybranie tej opcji pozwala na zastępowanie istniejących dokumentów ze zgodnymi identyfikatorami. Ta funkcja jest przydatne w przypadku migracji danych zaplanowane, które zaktualizować istniejące dokumenty.
4. Liczba ponownych prób w przypadku niepowodzenia: Określa liczbę ponownych prób połączenia do bazy danych Azure rozwiązania Cosmos w przypadku błędów przejściowych (na przykład łączności przerwy w działaniu sieci).
5. Interwał ponawiania prób: Określa, jak długo czekać między ponowieniem próby połączenia do bazy danych Azure rozwiązania Cosmos w przypadku błędów przejściowych (na przykład łączności przerwy w działaniu sieci).
6. Tryb połączenia: Określa tryb połączenia do korzystania z bazy danych Azure rozwiązania Cosmos. Dostępne opcje to DirectTcp, DirectHttps i bramy. Tryby bezpośredniego połączenia są szybsze, przy włączonym trybie bramy zapory więcej przyjazną jak tylko używa portu 443.

![Zrzut ekranu z rozwiązania Cosmos bazy danych platformy Azure kolejny rekord importu zaawansowane opcje](./media/import-data/documentdbsequentialoptions.png)

> [!TIP]
> Narzędzie importu domyślnie tryb połączenia DirectTcp. Jeśli występują problemy z zaporą, Przełącz do trybu połączenia bramy, ponieważ wymaga ona tylko portu 443.
> 
> 

## <a id="IndexingPolicy"></a>Określ zasady indeksowania
Jeśli zezwolisz na narzędzie do migracji do tworzenia kolekcji interfejsu API Azure rozwiązania Cosmos bazy danych SQL podczas importu, można określić zasady indeksowania w kolekcji. W sekcji Zaawansowane opcje importowania zbiorczego DB rozwiązania Cosmos Azure oraz Azure rozwiązania Cosmos DB sekwencyjnych opcje rekordów przejdź do sekcji zasady indeksowania.

![Zrzut ekranu Azure rozwiązania Cosmos DB indeksowania zasad opcje zaawansowane](./media/import-data/indexingpolicy1.png)

Przy użyciu zaawansowanych opcji zasady indeksowania, wybierz plik zasady indeksowania, ręcznie wprowadzić zasady indeksowania lub wybierać zestaw domyślnych szablonów (na przykład przez kliknięcie prawym przyciskiem myszy w polu tekstowym indeksowania zasad).

Szablony zasad, które udostępnia narzędzie są:

* Domyślne. Ta zasada jest najlepszy, gdy jest wykonywanie zapytań o równość dotyczących ciągów oraz przy użyciu ORDER BY, zakresu i zapytań o równość dotyczących liczb. Ta zasada ma mniejszy narzut magazynu indeksu niż zakres.
* Zakres. Ta zasada jest najlepszy, gdy używasz ORDER BY zakresu i zapytań o równość na liczb i ciągów. Ta zasada ma wyższy narzut na przechowywanie indeksu niż domyślne lub wyznaczania wartości skrótu.

![Zrzut ekranu Azure rozwiązania Cosmos DB indeksowania zasad opcje zaawansowane](./media/import-data/indexingpolicy2.png)

> [!NOTE]
> Jeśli nie określisz zasady indeksowania, stosowana jest zasada domyślna. Aby uzyskać więcej informacji na temat zasad indeksowania, zobacz [Azure DB rozwiązania Cosmos zasady indeksowania](indexing-policies.md).
> 
> 

## <a name="export-to-json-file"></a>Wyeksportuj do pliku JSON
Eksporter JSON DB rozwiązania Cosmos Azure pozwala na wyeksportowanie Opcje dostępnego źródła do pliku JSON, który zawiera tablicę dokumentów JSON. To narzędzie obsługuje eksportu dla Ciebie, lub można wybrać wyświetlić wynikowe polecenia migracji i uruchom polecenie samodzielnie. Wynikowy plik JSON mogą być przechowywane lokalnie lub w magazynie obiektów Blob Azure.

![Opcja eksportowania pliku lokalnego zrzut ekranu z Azure rozwiązania Cosmos bazy danych JSON](./media/import-data/jsontarget.png)

![Opcja eksportowania magazynu zrzut ekranu z rozwiązania Cosmos bazy danych JSON Azure obiektów Blob platformy Azure](./media/import-data/jsontarget2.png)

Opcjonalnie można wybrać opcję prettify wynikowy JSON, który spowoduje zwiększenie rozmiaru utworzonego dokumentu podczas przesyłania zawartości więcej do odczytu.

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
Na ekranie konfiguracji zaawansowanej Określ lokalizację pliku dziennika, do którego chcesz błędy zapisane. Ta strona mają zastosowanie następujące reguły:

1. Jeśli nie podano nazwy pliku, na stronie wyniki są zwracane wszystkie błędy.
2. Jeśli nazwa pliku bez katalogu, zostanie następnie plik jest utworzony (lub zastąpione) w katalogu bieżącego środowiska.
3. W przypadku wybrania istniejącego pliku, a następnie plik zostanie zastąpiony, nie jest dostępna opcja dołączania.

Następnie wybierz, czy rejestrować wszystkie, krytyczne, lub żadne komunikaty o błędach. Na koniec zdecydować, jak często na ekranie transferu wiadomości został zaktualizowany o postępie.

    ![Screenshot of Advanced configuration screen](./media/import-data/AdvancedConfiguration.png)

## <a name="confirm-import-settings-and-view-command-line"></a>Potwierdź ustawienia importowania i widok wiersza polecenia
1. Po określeniu informacji o źródle, informacji o docelowej i konfiguracji zaawansowanej, Przejrzyj podsumowanie migracji i, opcjonalnie, widok/skopiuj wynikowy polecenia migracji (kopiowanie polecenia jest przydatna do automatyzacji operacji importu):
   
    ![Zrzut ekranu przedstawiający ekran podsumowania](./media/import-data/summary.png)
   
    ![Zrzut ekranu przedstawiający ekran podsumowania](./media/import-data/summarycommand.png)
2. Po zakończeniu opcje źródłowego i docelowego kliknij **importu**. Czas, który upłynął, liczba przekazanych i informacje o błędzie (Jeśli nie podasz nazwę pliku w konfiguracji zaawansowanej) aktualizacji, jak importu jest w toku. Po wykonaniu tych czynności możesz wyeksportować wyniki (na przykład na wypadek niepowodzenia import).
   
    ![Zrzut ekranu z Azure rozwiązania Cosmos bazy danych JSON opcji eksportu](./media/import-data/viewresults.png)
3. Może także uruchomić nowy import, aktualizowania istniejących ustawień (na przykład ciąg połączenia wybór informacji, źródłowa i docelowa itp.) lub zresetowanie wszystkich wartości.
   
    ![Zrzut ekranu z Azure rozwiązania Cosmos bazy danych JSON opcji eksportu](./media/import-data/newimport.png)

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku wykonane następujące zadania:

> [!div class="checklist"]
> * Zainstalowane narzędzie do migracji danych
> * Importowane dane z różnych źródeł danych
> * Wyeksportowane z systemu Azure rozwiązania Cosmos bazy danych do formatu JSON

Możesz teraz przejść do następnego samouczek i Dowiedz się, jak wykonać zapytanie dotyczące danych przy użyciu bazy danych Azure rozwiązania Cosmos. 

> [!div class="nextstepaction"]
>[Jak wykonać zapytanie dotyczące danych?](../cosmos-db/tutorial-query-sql-api.md)
