---
title: 'Azure rozwiązania Cosmos bazy danych: Interfejsu API języka Java Async SQL, zestaw SDK & zasobów | Dokumentacja firmy Microsoft'
description: Dowiedz się wszystkiego o interfejsu API języka Java Async SQL i zestawu SDK, w tym daty wydania, daty wycofania i zmiany między poszczególnymi wersjami rozwiązania Cosmos bazy danych SQL Async Java zestawu SDK platformy Azure.
services: cosmos-db
documentationcenter: java
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: a452ffa2-c15d-4b0a-a8c1-ec9b750ce52b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 02/28/2018
ms.author: mimig
ms.openlocfilehash: d117f0571bef8f8a2ec48a067d7796f03c0e535a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure rozwiązania Cosmos DB Async Java SDK dla interfejsu API SQL: informacje o wersji i zasoby
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Źródła danych zmian .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java (asynchroniczny)](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [Dostawca zasobów REST](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

SDK Java asynchronicznego interfejsu API SQL różni się od zestawu SDK Java interfejsu API SQL zapewniając operacji asynchronicznych z obsługą programu [Netty biblioteki](http://netty.io/). Istniejących wcześniej [zestawu SDK Java interfejsu API SQL](sql-api-sdk-java.md) nie obsługuje operacji asynchronicznych. 

<table>

<tr><td>**Pobierz zestaw SDK**</td><td>[Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)</td></tr>

<tr><td>**Dokumentacja interfejsu API**</td><td>[Dokumentacji interfejsu API języka Java](https://azure.github.io/azure-cosmosdb-java/)</td></tr>

<tr><td>**Przyczyniają się do zestawu SDK**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-java)</td></tr>

<tr><td>**Wprowadzenie**</td><td>[Wprowadzenie do zestawu SDK Java Async](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started)</td></tr>

<tr><td>**Przykładowy kod**</td><td>[Github](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)</td></tr>

<tr><td>**Porady dotyczące wydajności**</td><td>[Plik readme Github](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)</td></tr>

<tr><td>**Minimalna obsługiwana środowiska wykonawczego**</td><td>[JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)</td></tr>
</table></br>

## <a name="release-notes"></a>Informacje o wersji

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Zestaw SDK GA o obsługę end-to-end nieblokujące We/Wy przy użyciu [Netty biblioteki](http://netty.io/) w trybie bramy. 

## <a name="release-and-retirement-dates"></a>Wersja i wycofania dat
Firma Microsoft udostępni powiadomienia co najmniej **12 miesięcy** klienta z wyprzedzeniem wycofanie SDK w celu złagodzenia przejścia do nowszej/nieobsługiwaną wersję.

Nowe funkcje i funkcjonalność i optymalizację, które są dodawane tylko do bieżącego zestawu SDK, w związku jest zalecane, zawsze uaktualnienie SDK najnowszą tak szybko jak to możliwe.

Każde żądanie do rozwiązania Cosmos bazy danych przy użyciu wycofane zestawu SDK będą odrzucane przez usługę.

<br/>

| Wersja | Data wydania | Dacie wycofania |
| --- | --- | --- |
| [1.0.0](#1.0.0) |27 lutego 2018 r.|--- |

## <a name="faq"></a>Często zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat rozwiązania Cosmos bazy danych, zobacz [bazy danych programu Microsoft Azure rozwiązania Cosmos](https://azure.microsoft.com/services/cosmos-db/) stronę usługi.

