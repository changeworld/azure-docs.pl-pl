---
title: 'Azure rozwiązania Cosmos bazy danych: Interfejsu API języka Java Async SQL, zestaw SDK & zasobów | Dokumentacja firmy Microsoft'
description: Dowiedz się wszystkiego o interfejsu API języka Java Async SQL i zestawu SDK, w tym daty wydania, daty wycofania i zmiany między poszczególnymi wersjami rozwiązania Cosmos bazy danych SQL Async Java zestawu SDK platformy Azure.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 06/20/2018
ms.author: sngun
ms.openlocfilehash: e4a3b3a482f56065c54525a4d9cd7971f50f5b2a
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300683"
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
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Dostawca zasobów REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

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

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Zastępuje zależności org.json jackson z powodu ze względu na wydajność i Licencjonowanie ([github #29](https://github.com/Azure/azure-cosmosdb-java/issues/29)).
* Usunąć przestarzałe klasy OfferV2.
* Metodę dostępu dodany do klasy oferta dla zawartości przepływności.
* Wszystkie metody w zwracanie typy org.json zmieniony na typ obiektu zwrotnego jackson dokumentu/zasobów.
* Metoda getObject(.) klas, rozszerzanie JsonSerializable zmiany do zwrócenia jackson ObjectNode typu.
* Metoda getCollection(.) została zmieniona do zwrócenia kolekcji ObjectNode.
* Usunięto podklasy JsonSerializable konstruktorów z org.json.JSONObject arg.
* Teraz JsonSerializable.toJson (SerializationFormattingPolicy.Indented) używa dwóch spacji wcięcia.
  
### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2
* Dodano obsługę unikatowe zasady indeksu.
* Dodano obsługę ograniczenie rozmiaru token kontynuacji odpowiedzi w opcjach kanału informacyjnego.
* Dodano obsługę podziału partycji w zapytaniu dla wielu partycji.
* Rozwiązane usterki w serializacji Json sygnatury czasowej ([github #32](https://github.com/Azure/azure-cosmosdb-java/issues/32)).
* Rozwiązane usterki w serializacji enum Json.
* Stałe usterki w zarządzaniu dokumentów o rozmiarze 2MB ([github #33](https://github.com/Azure/azure-cosmosdb-java/issues/33)).
* Zależności com.fasterxml.jackson.core:jackson-databind uaktualnione do 2.9.5 z powodu błędu ([jackson databind: github #1599](https://github.com/FasterXML/jackson-databind/issues/1599))
* Zależność od rxjava dodatki uaktualnione do 0.8.0.17 z powodu błędu ([rxjava dodatki: github #30](https://github.com/davidmoten/rxjava-extras/issues/30)).
* Opis metadanych w pliku pom zaktualizowany w celu zapewnienia wbudowany z resztą dokumentacji.
* Poprawa składni ([github #41](https://github.com/Azure/azure-cosmosdb-java/issues/41)), ([github #40](https://github.com/Azure/azure-cosmosdb-java/issues/40)).

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Dodano obsługę wąskiego gardła w zapytaniu.
* Dodano obsługę identyfikator zakresem kluczy partycji w zapytaniu.
* Napraw umożliwić większą token kontynuacji w nagłówku żądania (bugfix github #24).
* zależności netty uaktualnione do 4.1.22.Final zapewnienie JVM zamykany po zakończeniu głównego wątku.
* Napraw uniknąć przekazywania tokenu sesji podczas odczytu zasobów wzorca.
* Dodano więcej przykładów.
* Dodano więcej najlepszymi scenariuszy.
* Pliki nagłówkowe stałym Java generowania dokumentu prawidłowego java.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Zestaw SDK GA o obsługę end-to-end nieblokujące We/Wy przy użyciu [Netty biblioteki](http://netty.io/) w trybie bramy. 

## <a name="release-and-retirement-dates"></a>Wersja i wycofania dat
Firma Microsoft udostępni powiadomienia co najmniej **12 miesięcy** klienta z wyprzedzeniem wycofanie SDK w celu złagodzenia przejścia do nowszej/nieobsługiwaną wersję.

Nowe funkcje i funkcjonalność i optymalizację, które są dodawane tylko do bieżącego zestawu SDK. Dlatego zaleca się zawsze uaktualnienie do najnowszej wersji zestawu SDK możliwie jak najszybciej.

Każde żądanie do rozwiązania Cosmos bazy danych przy użyciu wycofane zestawu SDK będą odrzucane przez usługę.

<br/>

| Wersja | Data wydania | Dacie wycofania |
| --- | --- | --- |
| [2.0.0](#2.0.0) |20 czerwca 2018|--- |
| [1.0.2](#1.0.2) |18 maja 2018|--- |
| [1.0.1](#1.0.1) |20 kwietnia 2018|--- |
| [1.0.0](#1.0.0) |27 lutego 2018 r.|--- |

## <a name="faq"></a>Często zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat rozwiązania Cosmos bazy danych, zobacz [bazy danych programu Microsoft Azure rozwiązania Cosmos](https://azure.microsoft.com/services/cosmos-db/) stronę usługi.

