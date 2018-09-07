---
title: 'Azure Cosmos DB: Interfejs API języka Java Async SQL, zestawu SDK i zasoby | Dokumentacja firmy Microsoft'
description: Poznaj interfejs API języka Java Async SQL i zestawu SDK, w tym daty wydania, daty wycofania i zmiany między poszczególnymi wersjami usługi Azure Cosmos DB SQL Async zestawu SDK Java.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 09/05/2018
ms.author: sngun
ms.openlocfilehash: e90c5640e571aaf28e184e9439f6228e3a5bbc6b
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2018
ms.locfileid: "44023566"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Usługa Azure Cosmos DB Async zestawu Java SDK dla interfejsu API SQL: informacje o wersji i zasoby
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Kanał informacyjny zmian .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java (asynchroniczny)](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Dostawca zasobów REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [Bulkexecutor — platforma .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulkexecutor — platforma Java](sql-api-sdk-bulk-executor-java.md)

Zestaw SDK Java Async interfejsu API SQL różni się od zestawu SDK Java dla interfejsu API SQL, podając operacji asynchronicznych z obsługą [Netty biblioteki](http://netty.io/). Istniejących wcześniej [zestawu SDK Java interfejsu API SQL](sql-api-sdk-java.md) nie obsługuje operacji asynchronicznych. 

<table>

<tr><td>**Zestaw SDK do pobrania**</td><td>[Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)</td></tr>

<tr><td>**Dokumentacja interfejsu API**</td><td>[Dokumentacja interfejsu API języka Java](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client?view=azure-java-stable)</td></tr>

<tr><td>**Przyczynia się do zestawu SDK**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-java)</td></tr>

<tr><td>**Wprowadzenie**</td><td>[Wprowadzenie do zestawu SDK Java Async](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started)</td></tr>

<tr><td>**Przykładowy kod**</td><td>[Github](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)</td></tr>

<tr><td>**Porady dotyczące wydajności**</td><td>[Plik readme w serwisie Github](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)</td></tr>

<tr><td>**Minimalne obsługiwane środowisko uruchomieniowe**</td><td>[JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)</td></tr>
</table></br>

## <a name="release-notes"></a>Informacje o wersji

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Dodano obsługę serwera Proxy.
* Dodano obsługę tokenu autoryzacji zasobów.
* Usunięto usterkę w Obsługa kluczy dużych partycji ([github #63](https://github.com/Azure/azure-cosmosdb-java/issues/63)).
* Dokumentacja ulepszone.
* Zestaw SDK restrukturyzacji na bardziej szczegółowym moduły.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* Usunięto usterkę dla ustawień regionalnych innych niż angielski ([github #51](https://github.com/Azure/azure-cosmosdb-java/issues/51)).
* Metody pomocnicze dodanych w zasobie konflikt.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Zastępuje zależności org.json jackson ze względu na wydajność i Licencjonowanie ([github #29](https://github.com/Azure/azure-cosmosdb-java/issues/29)).
* Usunięto klasy OfferV2 przestarzałe.
* Dodano metody dostępu w celu klasy oferta dla zawartości przepływności.
* Metoda zwracania typów org.json zmieniony na typ obiektu zwrotnego jackson dokumentu lub zasobu.
* Metoda getObject(.) klas, rozszerzanie JsonSerializable zmiany do zwrócenia jackson ObjectNode typu.
* Metoda getCollection(.) zmienione do zwrócenia kolekcji ObjectNode.
* Usunięty podklasy JsonSerializable konstruktorów z org.json.JSONObject arg.
* JsonSerializable.toJson (SerializationFormattingPolicy.Indented) używa teraz dwie spacje wcięcia.
  
### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2
* Dodano obsługę unikatowy indeks zasadach.
* Dodano obsługę ograniczanie odpowiedzi rozmiaru token kontynuacji w opcjach kanału informacyjnego.
* Dodano obsługę podziału partycji zapytania obejmujące wiele partycji.
* Usunięto usterkę w serializacji sygnatura czasowa w formacie Json ([github #32](https://github.com/Azure/azure-cosmosdb-java/issues/32)).
* Usunięto usterkę w formacie Json wyliczenia serializacji.
* Usunięto usterkę w zarządzaniu dokumentów o rozmiarze 2MB ([github #33](https://github.com/Azure/azure-cosmosdb-java/issues/33)).
* Zależność com.fasterxml.jackson.core:jackson-elementu databind uaktualnione do 2.9.5 z powodu błędu ([elementu databind jackson: github #1599](https://github.com/FasterXML/jackson-databind/issues/1599))
* Zależność od dodatki rxjava, uaktualnienie do 0.8.0.17 z powodu błędu ([dodatki rxjava: github #30](https://github.com/davidmoten/rxjava-extras/issues/30)).
* Opis metadanych w pliku pom zaktualizowane pod kątem wbudowane z pozostałą częścią dokumentacji.
* Ulepszenia składni ([github #41](https://github.com/Azure/azure-cosmosdb-java/issues/41)), ([github #40](https://github.com/Azure/azure-cosmosdb-java/issues/40)).

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Dodano obsługę wąskiego gardła w zapytaniu.
* Dodano obsługę dla zakres kluczy partycji o identyfikatorze w zapytaniu.
* Napraw umożliwić większej token kontynuacji w nagłówku żądania (poprawka github #24).
* Zależność netty uaktualnione do 4.1.22.Final zapewnienie maszyny JVM zamyka się po zakończeniu wątku głównego.
* Napraw uniknąć przekazywania tokenu sesji podczas odczytu zasobów wzorca.
* Dodano więcej przykładów.
* Dodano więcej scenariuszy porównawczych.
* Pliki nagłówkowe stały Java generowania dokumentacji java właściwe.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Ogólnie zestawu SDK, dzięki obsłudze end-to-end bez blokowania We/Wy przy użyciu [Netty biblioteki](http://netty.io/) w trybie bramy. 

## <a name="release-and-retirement-dates"></a>Daty wydania i wycofania
Firma Microsoft zapewnia powiadomienie co najmniej **12 miesięcy** ewentualnej wycofanie zestawu SDK w celu złagodzenia przejścia do nowszych/obsługiwanych wersji.

Nowe funkcje i funkcjonalność i optymalizacje tylko są dodawane do bieżącego zestawu SDK. Dlatego zalecane jest, zawsze uaktualnienie do najnowszej wersji zestawu SDK możliwie jak najszybciej.

Wszelkie żądania do usługi Cosmos DB przy użyciu wycofane zestawu SDK zostanie odrzucone przez usługę.

<br/>

| Wersja | Data wydania | Data wygaśnięcia |
| --- | --- | --- |
| [2.1.0](#2.1.0) |5 września 2018 r.|--- |
| [2.0.1](#2.0.1) |16 sierpnia 2018 r.|--- |
| [2.0.0](#2.0.0) |20 czerwca 2018 r.|--- |
| [1.0.2](#1.0.2) |18 maja 2018 r.|--- |
| [1.0.1](#1.0.1) |20 kwietnia 2018 r.|--- |
| [1.0.0](#1.0.0) |27 lutego 2018 r.|--- |

## <a name="faq"></a>Często zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat usługi Cosmos DB, zobacz [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) stronę usługi.

