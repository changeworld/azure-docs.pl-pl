---
title: Usługa Azure Cosmos DB Interfejs API języka Java Async SQL, zestaw SDK i zasoby
description: Poznaj interfejs API języka Java Async SQL i zestawu SDK, w tym daty wydania, daty wycofania i zmiany między poszczególnymi wersjami usługi Azure Cosmos DB SQL Async zestawu SDK Java.
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 11/29/2018
ms.author: moderakh
ms.openlocfilehash: 5284de9a5b0f4f78b3b8b68e3848c2cb2783b839
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2018
ms.locfileid: "53338621"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Java (asynchroniczny) usługi Azure Cosmos DB zestawu SDK dla interfejsu API SQL: Informacje o wersji i zasoby
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
> * [SQL](sql-api-query-reference.md)
> * [Bulkexecutor — platforma .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulkexecutor — platforma Java](sql-api-sdk-bulk-executor-java.md)

Zestaw SDK Java Async interfejsu API SQL różni się od zestawu SDK Java dla interfejsu API SQL, podając operacji asynchronicznych z obsługą [Netty biblioteki](https://netty.io/). Istniejących wcześniej [zestawu SDK Java interfejsu API SQL](sql-api-sdk-java.md) nie obsługuje operacji asynchronicznych. 

<table>

<tr><td>**Zestaw SDK do pobrania**</td><td>[Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)</td></tr>

<tr><td>**Dokumentacja interfejsu API**</td><td>[Dokumentacja interfejsu API języka Java](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client?view=azure-java-stable)</td></tr>

<tr><td>**Przyczynia się do zestawu SDK**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-java)</td></tr>

<tr><td>**Wprowadzenie**</td><td>[Wprowadzenie do zestawu SDK Java Async](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started)</td></tr>

<tr><td>**Przykładowy kod**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)</td></tr>

<tr><td>**Porady dotyczące wydajności**</td><td>[Plik readme w serwisie GitHub](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)</td></tr>

<tr><td>**Minimalne obsługiwane środowisko uruchomieniowe**</td><td>[JDK 8](https://aka.ms/azure-jdks)</td></tr>
</table></br>

## <a name="release-notes"></a>Informacje o wersji

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Usunięto usterkę wyciek zasobów.
* Dodano obsługę MultiPolygon
* Dodano obsługę niestandardowych nagłówków w RequestOptions.

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2
* Usunięto usterkę pakowania.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Usunięto usterkę NPE w ścieżce ponów próbę zapisu.
* Usunięto usterkę NPE punktu końcowego zarządzania.
* Uaktualnione narażone zależności ([GitHub #68](https://github.com/Azure/azure-cosmosdb-java/issues/68)).
* Dodano obsługę rejestrowania Netty sieci do rozwiązywania problemów.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Dodano obsługę wielu region zapisu.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Dodano obsługę serwera Proxy.
* Dodano obsługę tokenu autoryzacji zasobów.
* Usunięto usterkę w Obsługa kluczy dużych partycji ([GitHub #63](https://github.com/Azure/azure-cosmosdb-java/issues/63)).
* Dokumentacja ulepszone.
* Zestaw SDK restrukturyzacji na bardziej szczegółowym moduły.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* Usunięto usterkę dla ustawień regionalnych innych niż angielski ([GitHub 51](https://github.com/Azure/azure-cosmosdb-java/issues/51)).
* Metody pomocnicze dodanych w zasobie konflikt.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Zastępuje zależności org.json jackson ze względu na wydajność i Licencjonowanie ([GitHub #29](https://github.com/Azure/azure-cosmosdb-java/issues/29)).
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
* Usunięto usterkę w serializacji sygnatura czasowa w formacie Json ([GitHub #32](https://github.com/Azure/azure-cosmosdb-java/issues/32)).
* Usunięto usterkę w formacie Json wyliczenia serializacji.
* Usunięto usterkę w zarządzaniu dokumentów o rozmiarze 2MB ([GitHub nr 33](https://github.com/Azure/azure-cosmosdb-java/issues/33)).
* Zależność com.fasterxml.jackson.core:jackson-elementu databind uaktualnione do 2.9.5 z powodu błędu ([elementu databind jackson: GitHub #1599](https://github.com/FasterXML/jackson-databind/issues/1599))
* Zależność od dodatki rxjava, uaktualnienie do 0.8.0.17 z powodu błędu ([rxjava dodatkowe: GitHub #30](https://github.com/davidmoten/rxjava-extras/issues/30)).
* Opis metadanych w pliku pom zaktualizowane pod kątem wbudowane z pozostałą częścią dokumentacji.
* Ulepszenia składni ([GitHub #41](https://github.com/Azure/azure-cosmosdb-java/issues/41)), ([GitHub #40](https://github.com/Azure/azure-cosmosdb-java/issues/40)).

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Dodano obsługę wąskiego gardła w zapytaniu.
* Dodano obsługę dla zakres kluczy partycji o identyfikatorze w zapytaniu.
* Napraw umożliwić większej token kontynuacji w nagłówku żądania (poprawka GitHub #24).
* Zależność netty uaktualnione do 4.1.22.Final zapewnienie maszyny JVM zamyka się po zakończeniu wątku głównego.
* Napraw uniknąć przekazywania tokenu sesji podczas odczytu zasobów wzorca.
* Dodano więcej przykładów.
* Dodano więcej scenariuszy porównawczych.
* Pliki nagłówkowe stały Java generowania dokumentacji java właściwe.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Ogólnie zestawu SDK, dzięki obsłudze end-to-end bez blokowania We/Wy przy użyciu [Netty biblioteki](https://netty.io/) w trybie bramy. 

## <a name="release-and-retirement-dates"></a>Daty wydania i wycofania
Firma Microsoft zapewnia powiadomienie co najmniej **12 miesięcy** ewentualnej wycofanie zestawu SDK w celu złagodzenia przejścia do nowszych/obsługiwanych wersji.

Nowe funkcje i funkcjonalność i optymalizacje tylko są dodawane do bieżącego zestawu SDK. Dlatego zalecane jest, zawsze uaktualnienie do najnowszej wersji zestawu SDK możliwie jak najszybciej.

Wszelkie żądania do usługi Cosmos DB przy użyciu wycofane zestawu SDK zostanie odrzucone przez usługę.

<br/>

| Wersja | Data wydania | Data wygaśnięcia |
| --- | --- | --- |
| [2.3.0](#2.3.0) |29 listopada 2018 r.|--- |
| [2.2.2](#2.2.2) |8 listopada 2018 r.|--- |
| [2.2.1](#2.2.1) |2 listopada 2018 r.|--- |
| [2.2.0](#2.2.0) |22 września 2018 r.|--- |
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

