---
title: 'Usługa Azure Cosmos DB: interfejs API asynchronii SQL, zestaw SDK & zasoby'
description: Dowiedz się wszystkiego o interfejsie API i SDK asynchronii języka SQL Async, w tym datach wydania, datach wycofania i zmianach wprowadzonych między każdą wersją narzędzia Azure Cosmos DB SQL Async Java SDK.
author: moderakh
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 07/01/2019
ms.author: moderakh
ms.openlocfilehash: 7dad1e929c2beb32605f67219ed30d2fd9a3a394
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985477"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Zestaw Azure Cosmos DB Async Java SDK dla interfejsu API SQL: informacje o wersji i zasoby
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Plik danych o zmianach w sieci .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java (asynchroniczny)](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Dostawca zasobów REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Wykonawca zbiorczy - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Wykonawca luzem - Java](sql-api-sdk-bulk-executor-java.md)

Interfejs API SQL Async Java SDK różni się od interfejsu JAVA SDK interfejsu SQL, zapewniając operacje asynchroniczne z obsługą [biblioteki Netty](https://netty.io/). Istniejący interfejs [SDK java interfejsu API SQL](sql-api-sdk-java.md) nie obsługuje operacji asynchronicznych. 

| |  |
|---|---|
| **Pobieranie SDK** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**Dokumentacja interfejsu API** |[Dokumentacja referencyjna interfejsu API w języku Java](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient?view=azure-java-stable) | 
|**Współtworzenie sdk** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**Rozpoczęcie pracy** | [Wprowadzenie do asynchronii Java SDK](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**Przykład kodu** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **Porady dotyczące wydajności**| [GitHub readme](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **Minimalny obsługiwany czas wykonywania**|[JDK 8](/java/azure/jdk/?view=azure-java-stable) | 

[!INCLUDE [Release notes](~/azure-cosmosdb-java-v2/changelog/README.md)]
## <a name="faq"></a>Często zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz też
Aby dowiedzieć się więcej o usłudze Cosmos DB, zobacz stronę usługi [Microsoft Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/)

