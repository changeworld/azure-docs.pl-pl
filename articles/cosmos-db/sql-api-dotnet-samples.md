---
title: 'Azure Cosmos DB: przykłady platformy .NET dla interfejsu SQL API'
description: Znajdź w witrynie GitHub przykłady dla platformy .NET w języku C# służące do wykonywania typowych zadań przy użyciu interfejsu SQL API w usłudze Azure Cosmos DB, w tym operacji CRUD.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 04/04/2019
ms.author: sngun
ms.openlocfilehash: cb132c8e36797e4ae089420e48d75271cddb5c04
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270544"
---
# <a name="azure-cosmos-db-net-examples-for-the-sql-api"></a>Azure Cosmos DB: przykłady platformy .NET dla interfejsu SQL API
> [!div class="op_single_selector"]
> * [Przykłady dla platformy .NET](sql-api-dotnet-samples.md)
> * [Przykłady języka Java](sql-api-java-samples.md)
> * [Przykłady Java (asynchroniczny)](sql-api-async-java-samples.md)
> * [Przykłady środowiska node.js](sql-api-nodejs-samples.md)
> * [Przykłady języka Python](sql-api-python-samples.md)
> * [Galeria przykładów kodu usługi Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

[Azure-cosmos-dotnet-v2](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples) repozytorium GitHub zawiera najnowsze rozwiązania przykładowe .NET do wykonywania operacji CRUD i innymi typowymi operacjami na zasoby usługi Azure Cosmos DB. Ten artykuł zawiera:

* Linki do zadań w poszczególnych plikach projektów przykładowych w języku C#. 
* Linki do powiązanej dokumentacji interfejsu API.

Przykłady kodu platformy .NET zestaw SDK w wersji 3.0 (wersja zapoznawcza), zobacz najnowsze przykłady w [azure-cosmos-dotnet-v3](https://github.com/Azure/azure-cosmos-dotnet-v3) repozytorium GitHub. 

## <a name="prerequisites"></a>Wymagania wstępne

Program Visual Studio 2017 z przepływem pracy programowania dla platformy Azure zainstalowany
- Możesz pobrać i używać **bezpłatne** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Podczas instalacji programu Visual Studio upewnij się, że jest włączona opcja **Programowanie na platformie Azure**. 

[Pakietu Microsoft.Azure.DocumentDB NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) 

Subskrypcja platformy Azure lub konto bezpłatnej wersji próbnej usługi Cosmos DB
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
  
- Możesz [aktywować korzyści subskrybenta programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Twoja subskrypcja programu Visual Studio zapewnia środki na korzystanie z każdego miesiąca, której można użyć na płatne usługi platformy Azure.
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

> [!NOTE]
> Przykłady są niezależne i przy użyciu wielu wywołań, a następnie ustaw Konfigurowanie i czyszczenie po same [CreateDocumentCollectionAsync()](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync). Każde wystąpienie rozlicza subskrypcję za jedną godzinę użycia warstwy wydajności kolekcji. 
> 

## <a name="database-examples"></a>Przykłady dotyczące baz danych
[RunDatabaseDemo](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/DatabaseManagement/Program.cs#L72-L121) metoda próbki *DatabaseManagement* projekt pokazuje, jak wykonać następujące zadania. Aby dowiedzieć się więcej o bazach danych Azure Cosmos przed uruchomieniem poniższych przykładów, zobacz [pracy z bazami danych, kontenerów i elementy](databases-containers-items.md). 

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie bazy danych](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/DatabaseManagement/Program.cs#L90) |[DocumentClient.CreateDatabaseAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdatabaseasync) |
| [Zapytanie dotyczące bazy danych](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/DatabaseManagement/Program.cs#L81) |[DocumentQueryable.CreateDatabaseQuery](/previous-versions/azure/dn850286(v=azure.100)) |
| [Przeczytaj bazę danych według Identyfikatora](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/DatabaseManagement/Program.cs#L102) |[DocumentClient.ReadDatabaseAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdatabaseasync) |
| [Odczyt wszystkich baz danych](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/DatabaseManagement/Program.cs#L108-L113) |[DocumentClient.ReadDatabaseFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdatabasefeedasync) |
| [Usuwanie bazy danych](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/DatabaseManagement/Program.cs#L118) |[DocumentClient.DeleteDatabaseAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.deletedatabaseasync) |

## <a name="collection-examples"></a>Przykłady dotyczące kolekcji
[RunCollectionDemo](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/CollectionManagement/Program.cs#L96-L185) metoda próbki *CollectionManagement* projekt pokazuje, jak wykonać następujące zadania. Informacje na temat kolekcji usługi Azure Cosmos przed uruchomieniem poniższych przykładów, zobacz [pracy z bazami danych, kontenerów i elementy](databases-containers-items.md). 

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie kolekcji](https://github.com/Azure/azure-documentdb-dotnet/blob/89670bc8aefd9bdd932db7f9b6d2fcb9b6acf35e/samples/code-samples/CollectionManagement/Program.cs#L101) |[DocumentClient.CreateDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync) |
| [Uzyskaj skonfigurowany wydajności kolekcji](https://github.com/Azure/azure-documentdb-dotnet/blob/95521ff51ade486bb899d6913880995beaff58ce/samples/code-samples/CollectionManagement/Program.cs#L198) |[DocumentQueryable.CreateOfferQuery](/previous-versions/azure/dn975114(v=azure.100)) |
| [Wydajność wprowadzania zmian skonfigurowane w kolekcji](https://github.com/Azure/azure-documentdb-dotnet/blob/95521ff51ade486bb899d6913880995beaff58ce/samples/code-samples/CollectionManagement/Program.cs#L207) |[DocumentClient.ReplaceOfferAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync) |
| [Pobierz kolekcję według Identyfikatora](https://github.com/Azure/azure-documentdb-dotnet/blob/89670bc8aefd9bdd932db7f9b6d2fcb9b6acf35e/samples/code-samples/CollectionManagement/Program.cs#L153) |[DocumentClient.ReadDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync) |
| [Odczyt wszystkich kolekcji w bazie danych](https://github.com/Azure/azure-documentdb-dotnet/blob/89670bc8aefd9bdd932db7f9b6d2fcb9b6acf35e/samples/code-samples/CollectionManagement/Program.cs#L162) |[DocumentClient.ReadDocumentCollectionFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentcollectionfeedasync) |
| [Usuwanie kolekcji](https://github.com/Azure/azure-documentdb-dotnet/blob/89670bc8aefd9bdd932db7f9b6d2fcb9b6acf35e/samples/code-samples/CollectionManagement/Program.cs#L175) |[DocumentClient.DeleteDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.deletedocumentcollectionasync) |

## <a name="document-examples"></a>Przykłady dotyczące dokumentów
[RunDocumentsDemo](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L97-L102) metoda próbki *DocumentManagement* projekt pokazuje, jak wykonać następujące zadania. Aby uzyskać informacje o dokumentach w usłudze Azure Cosmos przed uruchomieniem poniższych przykładów, zobacz [pracy z bazami danych, kontenerów i elementy](databases-containers-items.md). 

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie dokumentu](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L198) |[DocumentClient.CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync) |
| [Przeczytaj dokument według Identyfikatora](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L211) |[DocumentClient.ReadDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentasync) |
| [Odczyt wszystkich dokumentów w kolekcji](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L227) |[DocumentClient.ReadDocumentFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentfeedasync) |
| [Wykonywanie zapytań dotyczących dokumentów](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L248-L251) |[DocumentClient.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Zastąp dokument](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L263) |[DocumentClient.ReplaceDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentasync) |
| [UPSERT dokumentu](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L300) |[DocumentClient.UpsertDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.upsertdocumentasync) |
| [Usuwanie dokumentu](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L322) |[DocumentClient.DeleteDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.deletedocumentasync) |
| [Praca z obiektami dynamicznymi platformy .NET](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L331-L380) |[DocumentClient.CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync)<br>[DocumentClient.ReadDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentasync)<br>[DocumentClient.ReplaceDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentasync) |
| [Zastąp dokument warunkowego sprawdzania elementu ETag](https://github.com/Azure/azure-documentdb-dotnet/blob/f2b11dec45a195ddeed333560ebba63055f5ed09/samples/code-samples/DocumentManagement/Program.cs#L398-L440) |[DocumentClient.AccessCondition](/dotnet/api/microsoft.azure.documents.client.accesscondition)<br>[Documents.Client.AccessConditionType](/dotnet/api/microsoft.azure.documents.client.accessconditiontype) |
| [Odczyt dokumentu, tylko wtedy, gdy dokument został zmieniony](https://github.com/Azure/azure-documentdb-dotnet/blob/f2b11dec45a195ddeed333560ebba63055f5ed09/samples/code-samples/DocumentManagement/Program.cs#L442-L470) |[DocumentClient.AccessCondition](/dotnet/api/microsoft.azure.documents.client.accesscondition)<br>[Documents.Client.AccessConditionType](/dotnet/api/microsoft.azure.documents.client.accessconditiontype) |

## <a name="indexing-examples"></a>Przykłady dotyczące indeksowania
[RunIndexDemo](https://github.com/Azure/azure-documentdb-dotnet/blob/ea8c977b9c2f37ddc2894911ec239907ab60e40a/samples/code-samples/IndexManagement/Program.cs#L89-L117) metoda próbki *IndexManagement* projekt pokazuje, jak wykonać następujące zadania. Aby uzyskać informacje dotyczące indeksowania w usłudze Azure Cosmos DB, przed uruchomieniem poniższych przykładów, zobacz [indeks zasadach](index-policy.md), [indeksować typy](index-types.md), i [indeksu ścieżki](index-paths.md). 

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Wyklucz dokument z indeksu](https://github.com/Azure/azure-documentdb-dotnet/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L125-L163) |[IndexingDirective.Exclude](/dotnet/api/microsoft.azure.documents.indexingdirective) |
| [Użyj indeksowania ręcznego (zamiast automatycznie)](https://github.com/Azure/azure-documentdb-dotnet/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L171-L209) |[IndexingPolicy.Automatic](/dotnet/api/microsoft.azure.documents.indexingpolicy.automatic) |
| [Wyklucz ścieżki określony dokument z indeksu](https://github.com/Azure/azure-documentdb-dotnet/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L248-L297) |[IndexingPolicy.ExcludedPaths](/dotnet/api/microsoft.azure.documents.indexingpolicy.excludedpaths) |
| [Wymuś operacje skanowania zakresu na ścieżce indeksowanych wyznaczania wartości skrótu](https://github.com/Azure/azure-documentdb-dotnet/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L305-L340) |[FeedOptions.EnableScanInQuery](/dotnet/api/microsoft.azure.documents.client.feedoptions.enablescaninquery) |
| [Użyj zakresu indeksów dla ciągów](https://github.com/Azure/azure-documentdb-dotnet/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L342-L405) |[IndexingPolicy.IncludedPaths](/dotnet/api/microsoft.azure.documents.indexingpolicy.includedpaths)<br>[RangeIndex](/dotnet/api/microsoft.azure.documents.rangeindex) |
| [Wykonywać przekształcenia indeksu](https://github.com/Azure/azure-documentdb-dotnet/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L407-L464) |[ReplaceDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentcollectionasync) |

## <a name="geospatial-examples"></a>Przykłady danych geoprzestrzennych
Przykładowy plik danych geoprzestrzennych, [azure-documentdb-dotnet/samples/code-samples/Geospatial/Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs), pokazuje, jak wykonywać poniższe zadania.  Aby dowiedzieć się o GeoJSON i dane geograficzne danych przed uruchomieniem poniższych przykładów, zobacz [dane geograficzne i danych lokalizacji GeoJSON](geospatial.md). 

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Włącz indeksowanie nową kolekcję danych geoprzestrzennych](https://github.com/Azure/azure-documentdb-dotnet/blob/7b09c085817e850d683bc59bd864c2f6b552d275/samples/code-samples/Geospatial/Program.cs#L45-L63) |[indexingPolicy](/dotnet/api/microsoft.azure.documents.indexingpolicy) <br> [IndexKind.Spatial](/dotnet/api/microsoft.azure.documents.indexkind) <br>[DataType.Point](/dotnet/api/microsoft.azure.documents.datatype) |
| [Wstawianie dokumentów za pomocą punktów GeoJSON](https://github.com/Azure/azure-documentdb-dotnet/blob/7b09c085817e850d683bc59bd864c2f6b552d275/samples/code-samples/Geospatial/Program.cs#L116-L126) |[DocumentClient.CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync) </br> [DataType.Point](/dotnet/api/microsoft.azure.documents.datatype) |
| [Znajdź punkty w określonej odległości](https://github.com/Azure/azure-documentdb-dotnet/blob/7b09c085817e850d683bc59bd864c2f6b552d275/samples/code-samples/Geospatial/Program.cs#L152-L194) |[ST_DISTANCE](how-to-sql-query.md#spatial-functions) </br> [GeometryOperationExtensions.Distance](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.distance) |
| [Znajdź miejscach wewnątrz wielokąta](https://github.com/Azure/azure-documentdb-dotnet/blob/7b09c085817e850d683bc59bd864c2f6b552d275/samples/code-samples/Geospatial/Program.cs#L196-L221) |[ST_WITHIN](how-to-sql-query.md#spatial-functions) </br> [GeometryOperationExtensions.Within](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.distance) </br>[Polygon](/dotnet/api/microsoft.azure.documents.spatial.polygon) |
| [Włącz dane geograficzne indeksowanie istniejącej kolekcji](https://github.com/Azure/azure-documentdb-dotnet/blob/7b09c085817e850d683bc59bd864c2f6b552d275/samples/code-samples/Geospatial/Program.cs#L312-L336) |[DocumentClient.ReplaceDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentcollectionasync)<br>[DocumentCollection.IndexingPolicy](/dotnet/api/microsoft.azure.documents.documentcollection.indexingpolicy#P:Microsoft.Azure.Documents.DocumentCollection.IndexingPolicy) |
| [Sprawdzanie poprawności danych punktów i wielokątów](https://github.com/Azure/azure-documentdb-dotnet/blob/7b09c085817e850d683bc59bd864c2f6b552d275/samples/code-samples/Geospatial/Program.cs#L223-L265) |[ST_ISVALID](how-to-sql-query.md#spatial-functions)<br>[ST_ISVALIDDETAILED](how-to-sql-query.md#spatial-functions)<br>[GeometryOperationExtensions.IsValid](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.isvalid)<br>[GeometryOperationExtensions.IsValidDetailed](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.isvaliddetailed) |

## <a name="query-examples"></a>Przykłady zapytań
Plik dokumentu zapytania [azure-documentdb-dotnet/samples/code-samples/Queries/Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs), pokazuje, jak to zrobić, każdy z poniższych zadań przy użyciu gramatyki zapytań SQL, dostawcy LINQ, za pomocą zapytań i Lambda. Aby dowiedzieć się o dokumentacja zapytań SQL w usłudze Azure Cosmos DB, przed uruchomieniem poniższych przykładów, zobacz [przykłady zapytań SQL usługi Azure Cosmos DB](how-to-sql-query.md). 

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Wykonywanie zapytań dotyczących wszystkich dokumentów](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L122-L138) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Zapytania dotyczące korzystania z porównania ==](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L251-L268) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Zapytanie dotyczące korzystania z nierówności! = ale nie](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L270-L276) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Wykonywanie zapytań przy użyciu zakres operatorów, takich jak >, <>, =, < =](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L305-L325) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Korzystanie z operatorów zakresu względem ciągów zapytań](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L337-L346) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Zapytanie za pomocą ORDER BY](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L370-L392) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Zapytania z funkcjami agregującymi](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/Queries/Program.cs#L448-L496) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Praca z dokumentami podrzędnymi](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L394-L419) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Zapytanie z dokumentem wewnątrz sprzęga](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L421-L435) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Wykonywać zapytania za pomocą ciągu, matematyczne i operatory tablicy](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L527-L552) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Wykonywać zapytania za pomocą przy użyciu SqlQuerySpec SQL — sparametryzowane](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L140-L174) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100))<br>[SqlQuerySpec](/dotnet/api/microsoft.azure.documents.sqlqueryspec) |
| [Wykonywać zapytania za pomocą jawnego stronicowania](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L554-L576) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Kolekcje podzielone na partycje zapytanie równoległe](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L664-L734) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Wykonywać zapytania za pomocą klauzuli ORDER BY dla kolekcji podzielonych na partycje](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L737-L810) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |

## <a name="change-feed-examples"></a>Przykłady zestawienia zmian 
Zmiana źródła danych przykładowych, [azure-documentdb-dotnet/samples/code-samples/ChangeFeed/Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/ServerSideScripts/Program.cs), pokazuje, jak wykonać następujące zadania. Aby uzyskać informacje dotyczące zmiany źródła danych w usłudze Azure Cosmos DB, przed uruchomieniem poniższych przykładów, zobacz [zestawienia zmian usługi Azure Cosmos DB odczytu](read-change-feed.md) i [procesora kanału informacyjnego zmian](change-feed-processor.md). 

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Zestawienia zmian do odczytu](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/ChangeFeed/Program.cs#L132) |[DocumentClient.CreateDocumentChangeFeedQuery](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery) | 
| [Przeczytaj zakresów kluczy partycji](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/ChangeFeed/Program.cs#L118) |[DocumentClient.ReadPartitionKeyRangeFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readpartitionkeyrangefeedasync) | 

Zmiana źródła danych przykładowych procesora [ChangeFeedMigrationTool](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedMigrationTool), pokazuje, jak korzystać z biblioteką procesora zestawienia zmian do replikowania danych do innej kolekcji usługi Cosmos DB.   

## <a name="server-side-programming-examples"></a>Przykłady programowania po stronie serwera
Przykładowy plik programowania po stronie serwera, [azure-documentdb-dotnet/samples/code-samples/ServerSideScripts/Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/ServerSideScripts/Program.cs), pokazuje, jak wykonywać poniższe zadania. Aby uzyskać informacje dotyczące programowania po stronie serwera w usłudze Azure Cosmos DB, przed uruchomieniem poniższych przykładów, zobacz [procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika](stored-procedures-triggers-udfs.md). 

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Utwórz procedurę składowaną](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L112) |[DocumentClient.CreateStoredProcedureAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createstoredprocedureasync) |
| [Wykonaj procedurę składowaną](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L127) |[DocumentClient.ExecuteStoredProcedureAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.executestoredprocedureasync) |
| [Przeczytaj dokument, źródła danych dla procedury składowanej](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L194) |[DocumentClient.ReadDocumentFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentfeedasync) |
| [Utwórz procedurę przechowywaną z listą ORDER BY](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L219) |[DocumentClient.CreateStoredProcedureAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createstoredprocedureasync) |
| [Tworzenie wyzwalacza wstępnego](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L264) |[DocumentClient.CreateTriggerAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createtriggerasync) |
| [Tworzenie wyzwalacza po](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L329) |[DocumentClient.CreateTriggerAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createtriggerasync) |
| [Tworzenie funkcji zdefiniowanej przez użytkownika (UDF)](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L389) |[DocumentClient.CreateUserDefinedFunctionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createuserdefinedfunctionasync) |

## <a name="user-management-examples"></a>Przykłady zarządzania użytkownikami
Plik użytkownika administracyjnego, [azure-documentdb-dotnet/samples/code-samples/UserManagement/Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/UserManagement/Program.cs), przedstawiono sposób wykonywania następujących zadań:

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie użytkownika](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/UserManagement/Program.cs#L81) |[DocumentClient.CreateUserAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createuserasync) |
| [Ustawianie uprawnień dla kolekcji lub dokumentu](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/UserManagement/Program.cs#L85) |[DocumentClient.CreatePermissionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createpermissionasync) |
| [Pobierz listę uprawnień użytkownika](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/UserManagement/Program.cs#L218) |[DocumentClient.ReadUserAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readuserasync)<br>[DocumentClient.ReadPermissionFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readpermissionfeedasync) |

