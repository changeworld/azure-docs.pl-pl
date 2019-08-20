---
title: Usługa Azure Cosmos DB Interfejs API asynchronicznego języka Java SQL, zasoby & SDK
description: Poznaj interfejs API języka Java Async SQL i zestawu SDK, w tym daty wydania, daty wycofania i zmiany między poszczególnymi wersjami usługi Azure Cosmos DB SQL Async zestawu SDK Java.
author: moderakh
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 07/01/2019
ms.author: moderakh
ms.openlocfilehash: 3bee473adde9c4762cf400dff6190eb76a4cc8d7
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624689"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Async SDK Java dla interfejsu API SQL: Informacje o wersji i zasoby
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
> * [Wykonawca zbiorczy — .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Moduł wykonawczy zbiorczej — Java](sql-api-sdk-bulk-executor-java.md)

Zestaw SDK Java Async interfejsu API SQL różni się od zestawu SDK Java dla interfejsu API SQL, podając operacji asynchronicznych z obsługą [Netty biblioteki](https://netty.io/). Istniejących wcześniej [zestawu SDK Java interfejsu API SQL](sql-api-sdk-java.md) nie obsługuje operacji asynchronicznych. 

| |  |
|---|---|
| **Zestaw SDK do pobrania** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**Dokumentacja interfejsu API** |[Dokumentacja interfejsu API języka Java](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient?view=azure-java-stable) | 
|**Przyczynia się do zestawu SDK** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**Wprowadzenie** | [Wprowadzenie do zestawu SDK Java Async](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**Przykładowy kod** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **Porady dotyczące wydajności**| [Plik Readme usługi GitHub](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **Minimalne obsługiwane środowisko uruchomieniowe**|[JDK 8](https://aka.ms/azure-jdks) | 

## <a name="release-notes"></a>Informacje o wersji

### <a name="a-name250250"></a><a name="2.5.0"/>2.5.0
* Tryb TCP domyślnie włączony
* Metryki zapytań na wielu partycjach teraz zwracają wszystkie partycje
* Globalne silne teraz działa prawidłowo
* Przejście w tryb failover dla zapytań nie powoduje ponowienia próby dla wielu wzorców
* Zderzaki zależności dotyczące poprawek zabezpieczeń

### <a name="a-name245245"></a><a name="2.4.5"/>2.4.5
* Poprawka dla obsługi skrótu w wersji 2

### <a name="a-name243243"></a><a name="2.4.3"/>zasadniczy
* Poprawka dla przecieku zasobów na kliencie # Close () ([github #88](https://github.com/Azure/azure-cosmosdb-java/issues/88)).

### <a name="a-name242242"></a><a name="2.4.2"/>2.4.2
* Dodano obsługę tokenu kontynuacji zapytań między partycjami.

### <a name="a-name241241"></a><a name="2.4.1"/>2.4.1
* Rozwiązano pewne usterki w trybie bezpośrednim.
* Ulepszone rejestrowanie w trybie bezpośrednim.
* Ulepszone zarządzanie połączeniami.

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0
* Łączność w trybie bezpośrednim jest teraz ogólnie dostępna (GA). Aby uzyskać przykład, który używa łączności w trybie bezpośrednim, zobacz [Azure-cosmosdb-Java](https://github.com/Azure/azure-cosmosdb-java) GitHub Repository.
* Dodano obsługę QueryMetrics.
* Zmieniono interfejsy API akceptujące Java. util. Collection, dla których kolejność jest ważna, aby zamiast tego zaakceptować język Java. util. list. Teraz ConnectionPolicy # getPreferredLocations (), JsonSerialization i PartitionKey (.).

### <a name="a-name240-beta-1240-beta-1"></a><a name="2.4.0-beta-1"/>2.4.0-beta-1
* Dodano obsługę łączności w trybie bezpośrednim.
* Zmieniono interfejsy API akceptujące Java. util. Collection, dla których kolejność jest ważna, aby zamiast tego zaakceptować język Java. util. list.
  Teraz ConnectionPolicy # getPreferredLocations (), JsonSerialization i PartitionKey (.).
* Rozwiązano błąd sesji dla kwerendy dokumentu w trybie bramy.
* Uaktualnione zależności (w [#79 GitHub](https://github.com/Azure/azure-cosmosdb-java/issues/79)0.4.20, RxJava 1.3.8).

### <a name="a-name231231"></a><a name="2.3.1"/>2.3.1
* Poprawki obsługujące bardzo duże odpowiedzi na zapytania.
* Naprawia obsługę tokenu zasobów podczas tworzenia wystąpienia klienta ([#78 GitHub](https://github.com/Azure/azure-cosmosdb-java/issues/78)).
* Uaktualnione zależnie od usterki Diagram Jacksona-wywołaniu ([github #77](https://github.com/Azure/azure-cosmosdb-java/pull/77)).

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Usunięto usterkę wyciek zasobów.
* Dodano obsługę MultiPolygon
* Dodano obsługę niestandardowych nagłówków w RequestOptions.

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2
* Usunięto usterkę pakowania.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Usunięto usterkę NPE w ścieżce ponów próbę zapisu.
* Usunięto usterkę NPE punktu końcowego zarządzania.
* Uaktualnione zależności zagrożone ([#68 GitHub](https://github.com/Azure/azure-cosmosdb-java/issues/68)).
* Dodano obsługę rejestrowania Netty sieci do rozwiązywania problemów.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Dodano obsługę wielu region zapisu.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Dodano obsługę serwera Proxy.
* Dodano obsługę tokenu autoryzacji zasobów.
* Rozwiązano błąd podczas obsługi kluczy dużych partycji ([#63 GitHub](https://github.com/Azure/azure-cosmosdb-java/issues/63)).
* Dokumentacja ulepszone.
* Zestaw SDK restrukturyzacji na bardziej szczegółowym moduły.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* Rozwiązano błąd dla ustawień regionalnych innych niż angielskie ([GitHub #51](https://github.com/Azure/azure-cosmosdb-java/issues/51)).
* Metody pomocnicze dodanych w zasobie konflikt.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Zamieniono zależność org. JSON przez Diagram Jacksona ze względu na wydajność i Licencjonowanie ([GitHub #29](https://github.com/Azure/azure-cosmosdb-java/issues/29)).
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
* Naprawiono usterkę w serializacji sygnatur czasowych JSON ([#32 GitHub](https://github.com/Azure/azure-cosmosdb-java/issues/32)).
* Usunięto usterkę w formacie Json wyliczenia serializacji.
* Naprawiono usterkę zarządzania dokumentami o rozmiarze 2 MB ([GitHub #33](https://github.com/Azure/azure-cosmosdb-java/issues/33)).
* Zależność com. fasterxml. Jacksona. Core: Diagram Jacksona — uaktualniono do 2.9.5 z powodu błędu ([Diagram Jacksona-DataBind: #1599](https://github.com/FasterXML/jackson-databind/issues/1599)GitHub)
* Zależność od rxjava-Dodatki zostało uaktualnione do 0.8.0.17 z powodu błędu[(rxjava-Extras: #30](https://github.com/davidmoten/rxjava-extras/issues/30)GitHub).
* Opis metadanych w pliku pom zaktualizowane pod kątem wbudowane z pozostałą częścią dokumentacji.
* Poprawa składni ([#41 GitHub](https://github.com/Azure/azure-cosmosdb-java/issues/41)), ([#40 GitHub](https://github.com/Azure/azure-cosmosdb-java/issues/40)).

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Dodano obsługę wąskiego gardła w zapytaniu.
* Dodano obsługę dla zakres kluczy partycji o identyfikatorze w zapytaniu.
* Popraw, aby zezwolić na większy token kontynuacji w nagłówku żądania (poprawka GitHub #24).
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
| [2.4.3](#2.4.3) |Mar 5, 2019|--- |
| [2.4.2](#2.4.2) |Mar 1, 2019|--- |
| [2.4.1](#2.4.1) |20 lutego 2019|--- |
| [2.4.0](#2.4.0) |8 lutego 2019|--- |
| [2.4.0-beta-1](#2.4.0-beta-1) |4 lutego 2019|--- |
| [2.3.1](#2.3.1) |15 stycznia 2019|--- |
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

