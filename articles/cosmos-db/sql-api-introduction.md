---
title: 'Azure Cosmos DB: Wprowadzenie do interfejsu API SQL | Microsoft Docs'
description: Dowiedz się, jak korzystać z usługi Azure Cosmos DB do przechowywania i wysyłania zapytań do ogromnych wolumenów dokumentów JSON z niskim opóźnieniem przy użyciu składni SQL i JavaScript.
keywords: baza danych json, baza danych dokumentów
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: na
ms.topic: overview
ms.date: 05/22/2017
ms.author: rafats
ms.openlocfilehash: 4b469d306c4cd642e375e4eb15b0d26df828b308
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/17/2018
ms.locfileid: "42023972"
---
# <a name="introduction-to-azure-cosmos-db-sql-api"></a>Wprowadzenie do usługi Azure Cosmos DB: Interfejs API SQL

[Azure Cosmos DB](introduction.md) to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft do aplikacji o krytycznym znaczeniu. Usługa Azure Cosmos DB zapewnia [kompleksową globalną dystrybucję](distribute-data-globally.md), [elastyczne skalowanie przepływności i pamięci](partition-data.md) w skali globalnej, milisekundowe opóźnienia w 99. percentylu, [pięć odpowiednio zdefiniowanych poziomów spójności](consistency-levels.md) oraz gwarantowaną wysoką dostępność, wspierane przez [wiodące w branży umowy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Usługa Azure Cosmos DB [automatycznie indeksuje dane](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) bez konieczności zarządzania schematami i indeksami. To usługa wielomodelowa, obsługująca modele dokumentowe, klucz-wartość, wykresy i kolumny.

![Interfejs API SQL platformy Azure](./media/sql-api-introduction/cosmosdb-sql-api.png) 

Interfejs API SQL w usłudze Azure Cosmos DB zapewnia szerokie i powszechnie znane [możliwości zapytań SQL](sql-api-sql-query.md) w połączeniu ze stale niskimi opóźnieniami dla danych JSON bez schematu. Ten artykuł zawiera omówienie interfejsu API SQL usługi Azure Cosmos DB oraz sposobów przechowywania olbrzymich ilości danych JSON, wykonywania zapytań z opóźnieniem liczonym w milisekundach i łatwego rozwijania schematu za jego pomocą. 

## <a name="what-capabilities-and-key-features-does-azure-cosmos-db-offer"></a>Jakie możliwości i kluczowe funkcje oferuje usługa Azure Cosmos DB?
Usługa Azure Cosmos DB, za pośrednictwem interfejsu API SQL, oferuje następujące kluczowe funkcje i korzyści:

* **Elastycznie skalowalne przepływność i magazyn**: łatwe skalowanie bazy danych JSON w celu dostosowania do potrzeb aplikacji. Dane są przechowywane na dyskach półprzewodnikowych (SSD, solid-state drive) dla zapewnienia przewidywalnych, niskich opóźnień. Usługa Azure Cosmos DB obsługuje kontenery do przechowywania danych JSON zwane kolekcjami, które można skalować do niemal nieograniczonego rozmiaru magazynu i aprowizowanej przepływności. W miarę wzrostu aplikacji usługę Azure Cosmos DB można bezproblemowo elastycznie skalować z przewidywalną wydajnością. 


* **Replikacja w wielu regionach:** Usługa Azure Cosmos DB w sposób przezroczysty replikuje dane do wszystkich regionów skojarzonych z kontem usługi Azure Cosmos DB, umożliwiając tworzenie aplikacji wymagających globalnego dostępu do danych, a jednocześnie zapewniając kompromis między spójnością, dostępnością i wydajnością z odpowiednimi gwarancjami. Usługa Azure Cosmos DB zawiera przezroczyste rozwiązania regionalnej pracy awaryjnej z międzyregionalnymi interfejsami API oraz możliwość elastycznego skalowania przepływności i magazynów na całym świecie. Więcej informacji zawiera artykuł [Distribute data globally with Azure Cosmos DB](distribute-data-globally.md) (Globalna dystrybucja danych przy użyciu usługi Azure Cosmos DB).

* **Zapytania ad hoc z użyciem dobrze znanej składni SQL**: przechowywanie heterogenicznych dokumentów JSON i wykonywanie względem nich zapytań przy użyciu dobrze znanej składni SQL. Usługa Azure Cosmos DB wykorzystuje wysoce współbieżną, nieblokującą, opartą na strukturze dziennika technologię indeksowania do automatycznego indeksowania całej zawartości dokumentu. Umożliwia to wykonywanie zaawansowanych zapytań w czasie rzeczywistym bez konieczności określania wskazówek schematu, indeksów pomocniczych czy widoków. Więcej informacji zawiera temat [Tworzenie zapytań względem usługi Azure Cosmos DB](sql-api-sql-query.md). 
* **Wykonywanie skryptów JavaScript w bazie danych**: Wyrażanie logiki aplikacji jako procedur składowanych, wyzwalaczy i funkcji definiowanych przez użytkownika (funkcji UDF) przy użyciu standardowego języka JavaScript. Dzięki temu logika aplikacji może pracować nad danymi bez problemów z niezgodnością między aplikacją a schematem bazy danych. Interfejs API SQL zapewnia pełne transakcyjne wykonywanie logiki aplikacji JavaScript bezpośrednio wewnątrz aparatu bazy danych. Głęboka integracja języka JavaScript umożliwia wykonywanie operacji INSERT, REPLACE, DELETE i SELECT z poziomu programu JavaScript jako izolowanych transakcji. Aby dowiedzieć się więcej, zobacz [Programowanie po stronie serwera w języku SQL](programming.md).

* **Możliwość dostosowania poziomu spójności**: Możliwość wyboru spośród pięciu dobrze zdefiniowanych poziomów spójności w celu osiągnięcia optymalnego kompromisu między wydajnością a spójnością. Dla zapytań i operacji odczytu usługa Azure Cosmos DB oferuje pięć różnych poziomów spójności: „silna”, „powiązana nieaktualność”, „sesja”, „spójny prefiks” i „ostateczna”. Te szczegółowe, dokładnie zdefiniowane poziomy spójności umożliwiają ustalanie optymalnych kompromisów między spójnością, dostępnością i opóźnieniem. Aby dowiedzieć się więcej, zobacz [Maksymalizowanie dostępności i wydajności za pomocą poziomów spójności](consistency-levels.md).

* **W pełni zarządzana**: eliminuje konieczność zarządzania zasobami maszyn i bazy danych. Ponieważ jest to w pełni zarządzana usługa platformy Microsoft Azure, nie musisz zarządzać maszynami wirtualnymi, wdrażać i konfigurować oprogramowania, zarządzać skalowaniem ani obsługiwać złożonych aktualizacji warstwy danych. Dla każdej bazy danych jest automatycznie tworzona kopia zapasowa i jest ona chroniona przed regionalnymi awariami. Możesz łatwo dodać konto usługi Azure Cosmos DB i aprowizować pojemność odpowiednio do potrzeb, co pozwala skupić się na aplikacji, zamiast zajmować się obsługą bazy danych i zarządzaniem nią. 

* **Celowa ogólnodostępność**: można szybko rozpocząć pracę, wykorzystując posiadane umiejętności i narzędzia. Programowanie w oparciu o interfejs API SQL jest łatwe, przystępne i nie wymaga wdrażania nowych narzędzi ani niestandardowych rozszerzeń formatu JSON czy języka JavaScript. Możesz uzyskać dostęp do wszystkich funkcji bazy danych, w tym operacji CRUD, zapytań i przetwarzania języka JavaScript przy użyciu prostego interfejsu RESTful protokołu HTTP. Interfejs API SQL wykorzystuje istniejące formaty, języki i standardy, jednocześnie oferując oparte na nich i zapewniające wysoką wartość możliwości bazy danych.

* **Automatyczne indeksowanie:** domyślnie usługa Azure Cosmos DB automatycznie indeksuje wszystkie dokumenty w bazie danych i nie oczekuje ani nie wymaga żadnego schematu lub tworzenia indeksów pomocniczych. Nie chcesz indeksować wszystkiego? Nie martw się, możesz również [zrezygnować ze ścieżki plików JSON](indexing-policies.md).

* **Obsługa kanału informacyjnego zmian:** Kanał informacyjny zmian zapewnia posortowaną listę dokumentów w ramach kolekcji usługi Azure Cosmos DB w kolejności, w której zostały zmodyfikowane. Ten kanał informacyjny może służyć do nasłuchiwania pod kątem modyfikacji danych w celu replikowania danych, wyzwalania wywołań interfejsu API lub wykonywania przetwarzania strumienia względem aktualizacji. Kanał informacyjny zmian jest automatycznie włączany i łatwy w użyciu: [dowiedz się więcej na temat kanału informacyjnego zmian](https://docs.microsoft.com/azure/cosmos-db/change-feed). 

## <a name="data-management"></a>Jak zarządzać danymi przy użyciu interfejsu API SQL?
Interfejs API SQL pomaga zarządzać danymi JSON za pomocą dobrze zdefiniowanych zasobów bazy danych. Te zasoby są replikowane w celu zapewnienia wysokiej dostępności i unikatowo adresowane przez ich logiczny identyfikator URI. Interfejs API SQL oferuje prosty model programowania RESTful oparty na protokole HTTP dla wszystkich zasobów. 


Konto bazy danych usługi Azure Cosmos DB jest unikatową przestrzenią nazw, która umożliwia dostęp do usługi Azure Cosmos DB. Przed utworzeniem konta bazy danych musisz mieć subskrypcję platformy Azure, która umożliwia dostęp do różnych usług platformy Azure. 

Wszystkie zasoby w usłudze Azure Cosmos DB są modelowane i przechowywane jako dokumenty JSON. Zasoby są zarządzane jako elementy, które są dokumentami JSON zawierającymi metadane, oraz jako źródła danych, które są kolekcjami elementów. Zestawy elementów są zawarte w odpowiednich źródłach danych.

Na poniższym obrazie przedstawiono relacje między zasobami usługi Azure Cosmos DB:

![Hierarchiczna relacja między zasobami w usłudze Azure Cosmos DB][1] 

Konto bazy danych składa się z zestawu baz danych, każda z nich zawiera wiele kolekcji, a każda kolekcja może zawierać procedury składowane, wyzwalacze, funkcje UDF, dokumenty i powiązane załączniki. Baza danych ma również skojarzonych użytkowników, a każdy użytkownik ma zestaw uprawnień dostępu do różnych innych kolekcji, procedur składowanych, wyzwalaczy, funkcji UDF, dokumentów lub załączników. Bazy danych, użytkownicy, uprawnienia i kolekcje są zasobami zdefiniowanymi przez system za pomocą dobrze znanych schematów, natomiast dokumenty, procedury składowane, wyzwalacze, funkcje UDF i załączniki zawierają dowolną, zdefiniowaną przez użytkownika zawartość JSON.  

## <a name="develop"></a> Jak można tworzyć aplikacje przy użyciu interfejsu API SQL?

Usługa Azure Cosmos DB udostępnia zasoby za pośrednictwem interfejsów API REST, które można wywoływać za pomocą dowolnego języka realizującego żądania HTTP i HTTPS. Ponadto oferujemy biblioteki programistyczne dla kilku popularnych języków dla interfejsu API SQL. Biblioteki klienckie upraszczają wiele aspektów pracy z interfejsem API dzięki obsłudze szczegółów, takich jak buforowanie adresów, zarządzanie wyjątkami, automatyczne ponawianie prób i tak dalej. Biblioteki są obecnie dostępne dla następujących języków i platform:  

| Do pobrania | Dokumentacja |
| --- | --- |
| [Zestaw SDK platformy .NET](http://go.microsoft.com/fwlink/?LinkID=402989) |[Biblioteka języka .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet) |
| [Zestaw SDK dla platformy Node.js](http://go.microsoft.com/fwlink/?LinkID=402990) |[Biblioteka języka Node.js](https://github.com/Azure/azure-cosmosdb-node) |
| [Zestaw SDK Java](http://go.microsoft.com/fwlink/?LinkID=402380) |[Biblioteka języka Java](/java/api/com.microsoft.azure.documentdb) |
| [Zestaw SDK dla języka JavaScript](https://github.com/Azure/azure-cosmos-js) |[Biblioteka języka JavaScript](https://github.com/Azure/azure-cosmos-js) |
| Nie dotyczy |[Zestaw SDK dla języka JavaScript po stronie serwera](https://github.com/Azure/azure-cosmosdb-js-server) |
| [Zestaw SDK dla języka Python](https://pypi.python.org/pypi/pydocumentdb) |[Biblioteka języka Python](http://azure.github.io/azure-documentdb-python/) |
| Nie dotyczy | [Interfejs API dla usługi MongoDB](mongodb-introduction.md)


Za pomocą [emulatora usługi Azure Cosmos DB](local-emulator.md) możesz lokalnie opracowywać i testować swoją aplikację przy użyciu interfejsu API SQL bez tworzenia subskrypcji platformy Azure ani generowania kosztów. Jeśli sposób działania aplikacji w emulatorze jest zadowalający, możesz przełączyć się do korzystania z konta usługi Azure Cosmos DB w chmurze.

Poza podstawowymi operacjami tworzenia, odczytu, aktualizacji i usuwania interfejs API SQL udostępnia interfejs zaawansowanych zapytań SQL na potrzeby pobierania dokumentów JSON oraz obsługę po stronie serwera na potrzeby transakcyjnego wykonywania logiki aplikacji JavaScript. Interfejsy wykonywania zapytań i skryptów są dostępne za pośrednictwem bibliotek wszystkich platform, a także interfejsów API REST. 

### <a name="sql-query"></a>Zapytanie SQL
Usługa Azure Cosmos DB obsługuje tworzenie zapytań dla dokumentów za pomocą języka SQL, osadzonego w systemie typów języka JavaScript, oraz wyrażenia z obsługą zapytań relacyjnych, hierarchicznych i przestrzennych. Język zapytań usługi Azure Cosmos DB jest prostym, ale zaawansowanym interfejsem do tworzenia zapytań dotyczących dokumentów JSON. Język obsługuje podzbiór gramatyki ANSI SQL i dodaje głęboką integrację obiektów, tablic, konstrukcji obiektów i wywoływania funkcji języka JavaScript. Interfejs API SQL udostępnia swój model zapytań bez żadnego jawnego schematu ani wskazówek indeksowania od dewelopera.

Przy użyciu interfejsu API SQL można rejestrować funkcje zdefiniowane przez użytkownika i odwoływać się do nich jako elementu zapytania SQL, rozszerzając w ten sposób gramatykę na potrzeby obsługi niestandardowej logiki aplikacji. Te funkcje UDF są zapisywane jako programy JavaScript i wykonywane w bazie danych. 

Deweloperom platformy .NET [zestaw SDK dla platformy .NET](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.aspx) interfejsu API SQL oferuje również dostawcę zapytań LINQ. 

### <a name="transactions-and-javascript-execution"></a>Transakcje i wykonywanie kodu JavaScript
Interfejs API SQL umożliwia pisanie logiki aplikacji jako nazwanych programów napisanych w całości w języku JavaScript. Te programy są rejestrowane dla kolekcji i mogą wyzwalać operacje bazy danych na dokumentach w ramach danej kolekcji. Kod JavaScript można zarejestrować do wykonania jako wyzwalacz, procedurę składowaną lub funkcję zdefiniowaną przez użytkownika. Wyzwalacze i procedury składowane mogą tworzyć, odczytywać, aktualizować i usuwać dokumenty, natomiast funkcje zdefiniowane przez użytkownika są wykonywane jako część logiki wykonywania zapytania, bez dostępu do zapisu w kolekcji.

Wykonywanie kodu JavaScript w usłudze Cosmos DB jest modelowane zgodnie z koncepcjami obsługiwanymi przez systemy relacyjnych baz danych, z użyciem języka JavaScript jako nowoczesnego zamiennika języka Transact-SQL. Cała logika JavaScript jest wykonywana w ramach transakcji ACID otoczenia z izolacją migawki. Jeśli w trakcie wykonywania logiki JavaScript zgłosi ona wyjątek, cała transakcja zostaje przerwana.

## <a name="next-steps"></a>Następne kroki
Masz już konto platformy Azure? Następnie można rozpocząć pracę z usługą Azure Cosmos DB, wykonując zadania opisane w przewodnikach [Szybki start](../cosmos-db/create-sql-api-dotnet.md), które poprowadzą Cię przez proces tworzenia konta i rozpoczynania pracy z usługą Cosmos DB.

[1]: ./media/sql-api-introduction/json-database-resources1.png

