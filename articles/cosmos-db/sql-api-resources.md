---
title: Pojęcia i model zasobów usługi Azure Cosmos DB | Dokumentacja firmy Microsoft
description: Więcej informacji na temat usługi Azure Cosmos DB hierarchiczny model bazy danych, kolekcje, funkcja zdefiniowana przez użytkownika (UDF), dokumentów i uprawnienia do zarządzania zasobami i nie tylko.
keywords: Hierarchical model, cosmosdb, azure, Microsoft azure
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: rafats
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3ba02e7760d7400b5168a902415f16c4b276b3a7
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2018
ms.locfileid: "43287957"
---
# <a name="azure-cosmos-db-hierarchical-resource-model-and-core-concepts"></a>Podstawowe pojęcia i hierarchiczny model zasobów usługi Azure Cosmos DB

Jednostki bazy danych, którymi zarządza usługa Azure Cosmos DB są określane jako **zasobów**. Każdy zasób jest unikatowo identyfikowana przez logiczny identyfikator URI. Możesz porozmawiać z zasobami przy użyciu standardowych poleceń HTTP, nagłówków żądań/odpowiedzi i kodów stanu. 

Ten artykuł zawiera odpowiedzi na następujące pytania:

* Co to jest model zasobów usługi Azure Cosmos DB?
* Co to jest system zdefiniowane zasobów, w przeciwieństwie do zasobami zdefiniowanymi przez użytkownika?
* Jak rozwiązać zasób?
* Jak pracować z kolekcjami
* Jak pracować z procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika (UDF)

## <a name="hierarchical-resource-model"></a>Hierarchiczny model zasobów
Jak na poniższym diagramie przedstawiono usługi Azure Cosmos DB hierarchiczne **model zasobów** składa się z zestawów zasobów w ramach konta bazy danych, każdy mogą być adresowane za pośrednictwem logicznych i stabilnych identyfikatora URI. Zestaw zasobów, są nazywane **kanału informacyjnego** w tym artykule. 

> [!NOTE]
> Usługa Azure Cosmos DB oferuje wysoko wydajnej protokołu TCP, który jest również zgodne ze specyfikacją REST swój model komunikacji, dostępne za pośrednictwem [klienta SQL .NET API](sql-api-sdk-dotnet.md).
> 
> 

![Usługa Azure Cosmos DB hierarchiczny model zasobów][1]  
**Hierarchiczny model zasobów**   

Aby rozpocząć pracę z zasobami, musisz mieć [Tworzenie konta bazy danych](create-sql-api-dotnet.md) przy użyciu subskrypcji platformy Azure. Konto bazy danych może składać się z szeregu **baz danych**, każdy z nich zawierający wiele **kolekcji**, każdy z kolei zawierających **procedury składowane, wyzwalacze, funkcje UDF, dokumenty i powiązane załączniki**. Bazy danych ma również skojarzonych **użytkowników**, każdy zestaw **uprawnienia** do uzyskania dostępu do kolekcji, procedury składowane, wyzwalacze, funkcje zdefiniowane przez użytkownika, dokumentów lub załączników. Bazy danych, użytkownicy, uprawnienia i kolekcje są zasobami zdefiniowanymi przez system, za pomocą dobrze znanych schematów, dokumenty i załączniki zawierają dowolną, zdefiniowane przez użytkownika zawartość JSON.  

| Zasób | Opis |
| --- | --- |
| Konto bazy danych |Konto bazy danych jest skojarzona z zestawu baz danych i stały przydział magazynu obiektów blob dla załączników. Można utworzyć co najmniej jednego konta bazy danych przy użyciu subskrypcji platformy Azure. Aby uzyskać więcej informacji, odwiedź stronę [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/). |
| Database (Baza danych) |Baza danych jest kontenerem logicznym magazynu dokumentów podzielonym na partycje w kolekcjach. Jest również kontenerem użytkowników. |
| Użytkownik |Przestrzeń nazw logiczne do określania zakresu uprawnień. |
| Uprawnienie |Token autoryzacji skojarzonych z użytkownikiem w celu uzyskania dostępu do określonego zasobu. |
| Collection |Kolekcja jest kontenerem dokumentów JSON i skojarzonej logiki aplikacji JavaScript. Kolekcje mogą znajdować się na jednej lub wielu partycjach/serwerach i mogą być skalowane do obsługi praktycznie nieograniczonej ilości magazynu lub przepływności. |
| Procedura składowana |Logika aplikacji napisanych w języku JavaScript, który jest zarejestrowany w kolekcji i transakcyjnie w ramach aparatu bazy danych. |
| Wyzwalacz |Logika aplikacji napisanych w języku JavaScript wykonywane przed lub po obu insert, Zamień lub Usuń operację. |
| UDF |Logika aplikacji napisanych w języku JavaScript. Funkcje zdefiniowane przez użytkownika umożliwiają operatora zapytania niestandardowego modelu i tym samym rozszerzenie podstawowego interfejsu API SQL języka zapytań. |
| Dokument |Zdefiniowane przez użytkownika zawartość JSON (dowolną). Domyślnie żaden schemat musi być zdefiniowany ani indeksów pomocniczych, trzeba podać wszystkie dokumenty, które są dodawane do kolekcji. |
| Załącznik |Otwarcie chronionego załącznika będzie specjalny dokument zawierający odwołania i skojarzone metadane obiektu blob/nośników zewnętrznych. Deweloper można mieć obiektów blob, zarządzane przez usługi Cosmos DB, czy zapisać je z dostawcą usług zewnętrznych obiektów blob, takich jak OneDrive, Dropbox itp. |

## <a name="system-vs-user-defined-resources"></a>System a zasobami zdefiniowanymi przez użytkownika
Zasoby, takie jak konta bazy danych, baz danych, kolekcje, użytkownikami, uprawnieniami, procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika — wszystkie mają stały schemat i są nazywane zasobów systemowych. Z kolei zasobów, takich jak dokumenty i załączników w schemacie, nie ma żadnych ograniczeń i są przykładami zasobami zdefiniowanymi przez użytkownika. W usłudze Cosmos DB zarówno w systemie, jak i zasoby zdefiniowane przez użytkownika są reprezentowane i zarządzane jako standardowe zgodne JSON. Wszystkie zasoby, system lub użytkownik zdefiniowane, mają następujące wspólne właściwości:

> [!NOTE]
> Wszystkie właściwości wygenerowanej przez system w zasobie są poprzedzone znakiem podkreślenia (_) w ich reprezentacji JSON.
> 
> 

<table>
    <tbody>
        <tr>
            <td valign="top"><p><strong>Właściwość</strong></p></td>
            <td valign="top"><p><strong>Użytkownika można ustawić lub generowane przez system?</strong></p></td>
            <td valign="top"><p><strong>Cel</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>_rid</p></td>
            <td valign="top"><p>Generowane przez system</p></td>
            <td valign="top"><p>Generowany przez system, unikatowe i hierarchiczne identyfikator zasobu</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_etag</p></td>
            <td valign="top"><p>Generowane przez system</p></td>
            <td valign="top"><p>Element etag zasobów wymaganych do mechanizmu kontroli optymistycznej współbieżności</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_ts</p></td>
            <td valign="top"><p>Generowane przez system</p></td>
            <td valign="top"><p>Znacznik czasu ostatniej aktualizacji zasobu</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_self</p></td>
            <td valign="top"><p>Generowane przez system</p></td>
            <td valign="top"><p>Unikatowe adresy identyfikator URI zasobu</p></td>
        </tr>
        <tr>
            <td valign="top"><p>id</p></td>
            <td valign="top"><p>Albo</p></td>
            <td valign="top"><p>Zdefiniowane przez użytkownika unikatowa nazwa zasobu (z taką samą wartość klucza partycji). Jeśli użytkownik nie podasz identyfikatora, identyfikator jest generowane przez system</p></td>
        </tr>
    </tbody>
</table>

### <a name="wire-representation-of-resources"></a>Przewodowy reprezentacja zasobów
Usługa cosmos DB nie zmusza do jakichkolwiek rozszerzeń własności do kodowania standardowe lub specjalne JSON; działa z standardowych zgodne dokumentów JSON.  

### <a name="addressing-a-resource"></a>Odnoszący się do zasobu
Wszystkie zasoby są adresy identyfikatora URI. Wartość **_self** właściwość zasobu reprezentuje względny identyfikator URI zasobu. Format identyfikatora URI, który składa się z /\<kanału informacyjnego\>/ segmenty ścieżki {_rid}:  

| Wartość _self | Opis |
| --- | --- |
| /DBS |Źródło danych, baz danych w ramach konta bazy danych |
| /dbs/{dbName} |Baza danych o identyfikatorze zgodne z wartością {dbName} |
| /dbs/{dbName}/colls/ |Źródło danych kolekcji w bazie danych |
| /dbs/{dbName}/colls/{collName} |Kolekcja o identyfikatorze zgodne z wartością {collName} |
| /dbs/{dbName}/colls/{collName}/docs |Źródło danych dokumentów w kolekcji |
| /dbs/{dbName}/colls/{collName}/docs/{docId} |Dokument o identyfikatorze zgodne z wartością {doc} |
| /dbs/{dbName}/users/ |Źródło danych użytkowników w ramach bazy danych |
| /dbs/{dbName}/users/{userId} |Użytkownik o identyfikatorze zgodne z wartością {user} |
| /dbs/{dbName}/users/{userId}/permissions |Kanał uprawnień przez użytkownika |
| /dbs/{dbName}/users/{userId}/permissions/{permissionId} |Uprawnienia o identyfikatorze zgodne z wartością {uprawnienie} |

Każdy zasób ma unikatową nazwę użytkownika udostępniane za pośrednictwem właściwości identyfikatora. Uwaga: w przypadku dokumentów, jeśli użytkownik nie określono identyfikatora, zestawy SDK automatycznie wygenerować unikatowy identyfikator dla dokumentu. Identyfikator jest ciągiem zdefiniowanej przez użytkownika, maksymalnie 256 znaków, które są unikatowe w ramach zasobu konkretny nadrzędny. 

Każdy zasób ma również identyfikator zasobu hierarchiczne wygenerowanej przez system (nazywane również identyfikatorów RID), który jest dostępny za pośrednictwem właściwości _rid. Identyfikator RID koduje całej hierarchii danego zasobu i jest wygodny wewnętrznej reprezentacji umożliwia wymuszanie integralności referencyjnej w sposób rozproszony. RID jest unikatowa w obrębie konta bazy danych i jej jest używana wewnętrznie przez usługi Cosmos DB dla wydajny routing, bez konieczności wyszukiwania wielu partycji. Wartości właściwości _rid i _self są alternatywne i canonical reprezentacji zasobu. 

Interfejsy API REST obsługują adresowania zasobów i routing żądań przez identyfikator i właściwości _rid.

## <a name="database-accounts"></a>Konta bazy danych
Możesz aprowizować konta bazy danych co najmniej jeden usługi Cosmos DB przy użyciu subskrypcji platformy Azure.

Można tworzyć i zarządzać kontami bazy danych usługi Cosmos DB za pomocą witryny Azure portal pod [ http://portal.azure.com/ ](https://portal.azure.com/). Tworzenie i zarządzanie kontem bazy danych wymaga dostępu administracyjnego i może zostać wykonana tylko w ramach Twojej subskrypcji platformy Azure. 

### <a name="database-account-properties"></a>Właściwości konta bazy danych
W ramach inicjowania obsługi administracyjnej i zarządzanie kontem bazy danych można skonfigurować i przeczytaj następujące właściwości:  

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Nazwa właściwości</strong></p></td>
            <td valign="top"><p><strong>Opis</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Zasady spójności</p></td>
            <td valign="top"><p>Ustaw tę właściwość, aby skonfigurować domyślny poziom spójności dla wszystkich kolekcji na koncie bazy danych. Można zastąpić, poziomu spójności dla danego żądania przy użyciu nagłówka żądania [x-ms--poziomu spójności]. <p><p>Ta właściwość ma zastosowanie tylko do <i>zasobami zdefiniowanymi przez użytkownika</i>. System wszystkie zasoby zdefiniowane są skonfigurowane do obsługi odczytów i zapytań za pomocą silnej spójności.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Klucze autoryzacji</p></td>
            <td valign="top"><p>Klucze podstawowe i pomocnicze głównego i tylko do odczytu, które zapewniają dostęp administracyjny do wszystkich zasobów w ramach konta bazy danych.</p></td>
        </tr>
    </tbody>
</table>

Oprócz inicjowania obsługi administracyjnej, konfigurowanie i zarządzanie kontem bazy danych w witrynie Azure portal, można też programowo tworzyć i zarządzać kontami bazy danych usługi Cosmos DB przy użyciu [interfejsów API REST usługi Azure Cosmos DB](/rest/api/cosmos-db/) oraz [zestawów SDK klienta](sql-api-sdk-dotnet.md).  

## <a name="databases"></a>Bazy danych
Bazy danych Cosmos DB to logiczny kontener zawierający jeden lub więcej kolekcji i użytkowników, jak pokazano na poniższym diagramie. Można utworzyć dowolną liczbę baz danych w ramach konta bazy danych Cosmos DB z zastrzeżeniem ograniczeń oferty.  

![Model bazy danych konta i kolekcje hierarchicznych][2]  
**Baza danych jest kontenerem logicznym, użytkowników i kolekcji**

Baza danych może zawierać nieograniczoną dokumentu magazynu podzielone na partycje w kolekcjach.

### <a name="elastic-scale-of-an-azure-cosmos-db-database"></a>Elastyczne skalowanie bazy danych Azure Cosmos DB
Bazy danych Cosmos DB jest elastyczne domyślnie — od kilku GB do petabajtów dokumentu SSD i aprowizowanej przepływności. 

W przeciwieństwie do bazy danych w tradycyjnych RDBMS bazę danych w usłudze Cosmos DB jest poza zakresem na jednym komputerze. Za pomocą usługi Cosmos DB miarę skalowania aplikacji potrzeb rośnie, utworzeniem więcej kolekcji i/lub baz danych. W rzeczywistości różnych pierwszej aplikacji innych firm w ramach firmy Microsoft masz doświadczenie z usługi Azure Cosmos DB skali konsumenta, tworząc każdego zawierającego tysięcy kolekcji bardzo dużych baz danych Azure Cosmos DB z terabajtów miejsca do przechowywania dokumentów. Użytkownik może rosnąć lub maleć bazę danych, dodając lub usuwając kolekcje w celu spełnienia wymagań skalowania aplikacji. 

Można utworzyć dowolną liczbę kolekcji w bazie danych, z zastrzeżeniem oferty. Każdej kolekcji lub zestaw kolekcji (w ramach bazy danych) zawiera dysk SSD magazynowania i przepływnością aprowizowaną dla Ciebie w zależności od wybranej oferty.

Bazę danych usługi Azure Cosmos DB jest również kontenerem użytkowników. Użytkownik, w pozycji jest logiczny obszar nazw dla zestawów uprawnień, który zapewnia precyzyjną autoryzacji i dostępu do kolekcji, dokumentów i załączników.  

Zgodnie z innymi zasobami w modelu zasobów usługi Azure Cosmos DB, bazy danych można tworzyć, zastąpione, usunięte, odczytać lub wyliczenia, łatwo za pomocą [interfejsów API REST](/rest/api/cosmos-db/) ani żadnego z [zestawów SDK klienta](sql-api-sdk-dotnet.md). Usługa Azure Cosmos DB gwarantuje wysoki poziom spójności odczytu lub przeszukując metadane zasobów bazy danych. Automatyczne usunięcie bazy danych gwarantuje, że nie masz dostępu do kolekcji lub użytkowników w nim zawarte.   

## <a name="collections"></a>Kolekcje
Kolekcja usługi Cosmos DB jest kontenerem dokumentów JSON. 

### <a name="elastic-ssd-backed-document-storage"></a>Elastyczne dysk SSD do przechowywania dokumentów
Kolekcja jest bardzo elastyczna — automatycznie wraz ze wzrostem i zmniejsza Dodawanie lub usuwanie dokumentów. Kolekcje są zasobami logicznymi i mogą znajdować się jeden lub więcej partycji fizycznych lub serwerów. Liczba partycji przypisane do kolekcji zależy od usługi Cosmos DB na podstawie rozmiaru magazynu i przepływnością aprowizowaną dla kolekcji lub zestaw kolekcji. Każdej partycji w usłudze Cosmos DB ma ustaloną ilość opartych na dyskach SSD magazynu skojarzone z nim, a są replikowane w celu zapewnienia wysokiej dostępności. Zarządzanie partycja jest w pełni zarządzana przez usługę Azure Cosmos DB, a nie masz do pisania złożonego kodu lub zarządzania partycjami. Kolekcje usługi cosmos DB są **nieograniczone** pod względem magazynu i przepływności. 

### <a name="automatic-indexing-of-collections"></a>Automatyczne indeksowanie w kolekcji
Usługa Azure Cosmos DB to system true bazy danych bez schematu. Go nie zakłada ani nie wymaga żadnego schematu dla dokumentów JSON. W miarę dodawania dokumenty w kolekcji, usługi Azure Cosmos DB automatycznie indeksuje je i są dostępne do wykonywania zapytań. Automatyczne indeksowanie dokumentów, bez konieczności schematu lub indeksów pomocniczych to kluczowy czynnik wpływający usługi Azure Cosmos DB i został włączony przez techniki obsługi zoptymalizowanej pod kątem zapisu, wolne od blokady i strukturze dziennika indeksu. Usługa Azure Cosmos DB obsługuje dużych ilości bardzo szybkiego zapisu podczas nadal obsługuje zapytania zgodne. Dokument i indeks magazynu są używane do obliczania zajmowanego w magazynie przez każdej kolekcji. Można kontrolować magazyn i wydajność wad i zalet skojarzony indeksowania przez skonfigurowanie zasad indeksowania w kolekcji. 

### <a name="configuring-the-indexing-policy-of-a-collection"></a>Konfigurowanie zasad indeksowania w kolekcji
Zasady indeksowania wszystkich kolekcji umożliwia kompromisu wydajności i magazynu skojarzone z indeksowania. Poniższe opcje są dostępne w ramach konfiguracji indeksowania:  

* Wybierz, czy kolekcja automatycznie indeksuje wszystkie dokumenty czy nie. Domyślnie wszystkie dokumenty są automatycznie indeksowane. Można wyłączyć automatyczne indeksowanie i selektywnie dodać tylko do określonych dokumentów do indeksu. Z drugiej strony można selektywnie wskazać wykluczyć tylko określonych dokumentów. Można to osiągnąć przez ustawienie automatycznego właściwość jako wartość true lub false na indexingPolicy kolekcji i przy użyciu nagłówka żądania [x-ms-indexingdirective] podczas wstawiania, zastępowanie lub usuwanie dokumentu.  
* Wybierz, czy do dołączania lub wykluczania określonej ścieżki lub wzorce w dokumentach z indeksu. Można to osiągnąć przez ustawienie includedPaths i excludedPaths na indexingPolicy kolekcji odpowiednio. Można także skonfigurować Magazyn i wydajność wad i zalet zapytań zakresu i wyznaczania wartości skrótu dla określonej ścieżki wzorców. 
* Wybierz między synchroniczne (jak) i aktualizacje indeksu w asynchronicznego (z opóźnieniem). Domyślnie zaktualizowaniu indeksu synchronicznie na każdym insert, zastępowanie lub usuwanie dokumentów w kolekcji. Dzięki temu zapytań, aby przestrzegać tego samego poziomu spójności, jak w przypadku operacji odczytu dokumentu. Azure Cosmos DB jest zoptymalizowane pod kątem zapisu i obsługuje stałą woluminy zapisów dokumentów wraz z konserwacja indeksu synchroniczne i obsługuje zapytania zgodne, można skonfigurować w niektórych kolekcjach można zaktualizować ich indeksu opóźnieniem. Indeksowanie z opóźnieniem zwiększa wydajność zapisu jeszcze bardziej i jest idealny dla scenariuszy pozyskiwania zbiorcze przede wszystkim odczycie kolekcji.

Zasady indeksowania można zmienić, wykonując PUT w kolekcji. Może to być realizowane za pośrednictwem [zestawu SDK klienta](sql-api-sdk-dotnet.md), [witryny Azure portal](https://portal.azure.com), lub [interfejsów API REST](/rest/api/cosmos-db/).

### <a name="querying-a-collection"></a>Kolekcja zapytań
Dokumenty w kolekcji może mieć dowolnych schematów i dokumenty w kolekcji można wykonać zapytanie, nie oferuje żadnych schematów ani indeksów pomocniczych. Można tworzyć zapytania przy użyciu kolekcji [dokumentacja składni SQL usługi Azure Cosmos DB](https://msdn.microsoft.com/library/azure/dn782250.aspx), który zapewnia rozbudowane operatory hierarchiczne, relacyjne i przestrzennych i rozszerzalność oparte na języku JavaScript funkcji zdefiniowanych przez użytkownika. Gramatyka JSON umożliwia modelowanie dokumentów JSON jako drzewa z etykietami jako węzłami drzewa. Jest to wykorzystać zarówno przez techniki automatycznego indeksowania interfejsu SQL API, a także dialekt SQL usługi Azure Cosmos DB. Język zapytań SQL obejmuje trzy główne aspekty:   

1. W małej grupie operacje zapytań, naturalny mapowane na strukturze drzewa, w tym reguł zapytań hierarchiczne i projekcji. 
2. Podzbiór relacyjnych operacje, w tym kompozycji, filtr, projekcje, agregacje i samodzielnie sprzężenia. 
3. Na podstawie czystego kodu JavaScript, funkcje zdefiniowane przez użytkownika, które działają z (1) i (2).  

Model zapytań usługi Azure Cosmos DB próbuje zachować równowagę pomiędzy funkcjonalności, wydajność i prostotę. Aparat bazy danych Azure Cosmos DB natywnie kompiluje i wykonuje instrukcje zapytania SQL. Można tworzyć zapytania przy użyciu kolekcji [interfejsów API REST](/rest/api/cosmos-db/) ani żadnego z [zestawów SDK klienta](sql-api-sdk-dotnet.md). Zestaw SDK platformy .NET jest powiązana z dostawcy LINQ.

> [!TIP]
> Możesz wypróbować interfejs API SQL i uruchamiać zapytania SQL względem naszego zestawu danych w [Plac zabaw dla zapytań](https://www.documentdb.com/sql/demo).
> 
> 

## <a name="multi-document-transactions"></a>Transakcji obejmujących wiele dokumentów
Transakcje bazy danych zapewniają bezpieczny i przewidywalne modelu programowania radzenia sobie z równoczesnych zmian danych. W systemie RDBMS tradycyjny sposób zapisanie logiki biznesowej jest napisanie **procedur składowanych** i/lub **wyzwalaczy** i dostarczaj je na serwerze bazy danych na potrzeby transakcyjnego wykonywania. W systemie RDBMS programista aplikacji jest wymagany do czynienia z dwóch różnych języków programowania: 

* Aplikacja (nietransakcyjnej) programowania języka (na przykład, JavaScript, Python, C#, Java itp.)
* T-SQL, transakcji języka programowania, wykonywanego przez bazę danych

Ze względu na jej głębokie zaangażowanie w języku JavaScript i JSON bezpośrednio wewnątrz aparatu bazy danych, Azure Cosmos DB zapewnia intuicyjnego modelu programowania dla JavaScript wykonywanie logiki aplikacji bezpośrednio na podstawie kolekcji pod względem procedur składowanych i Wyzwalacze. Umożliwia to z następującymi czynnościami:

* Wydajne implementacji współbieżności kontrolować, odzyskiwanie, automatyczne indeksowanie wykresów obiektów JSON bezpośrednio w aparacie bazy danych
* Naturalnie wyrażanie przepływ sterowania, zmienną zakresu, przypisania i integracji z transakcjami bazy danych bezpośrednio pod względem języka JavaScript, język programowania w nim elementów podstawowych obsługi wyjątków

Logiki JavaScript zarejestrowany na poziomie kolekcji mogą następnie wydać operacji bazy danych na dokumentach w danej kolekcji. Usługa Azure Cosmos DB niejawnie opakowuje kod JavaScript, na podstawie procedur składowanych i wyzwalaczy w ramach transakcji ACID otoczenia z izolacją migawki w dokumentach w obrębie kolekcji. Jeśli w trakcie wykonywania logiki JavaScript zgłosi ona wyjątek, cała transakcja zostaje przerwana. Wynikowy model programowania jest prosty jeszcze zaawansowane. Deweloperzy języka JavaScript występują "trwałość" model programowania podczas nadal przy użyciu konstrukcji językowych znanych i biblioteki w nim elementów podstawowych.   

Możliwość wykonywania języka JavaScript bezpośrednio wewnątrz aparatu bazy danych w przestrzeni adresowej jako pula buforów umożliwia wydajne i transakcyjne wykonywanie operacji bazy danych względem dokumentów w kolekcji. Ponadto aparat bazy danych Cosmos DB sprawia, że głębokie zaangażowanie w formacie JSON i języka JavaScript pozwala wyeliminować wszelkie niezgodności impedancji między systemami typu aplikacji i bazy danych.   

Po utworzeniu kolekcji, procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika za pomocą można rejestrować za pomocą kolekcji [interfejsów API REST](/rest/api/cosmos-db/) ani żadnego z [zestawów SDK klienta](sql-api-sdk-dotnet.md). Po rejestracji można odwoływać się i wykonać je. Należy wziąć pod uwagę następujące procedury składowanej napisanych w całości w języku JavaScript, poniższy kod przyjmuje dwa argumenty (Nazwa książki i nazwisko autora) i tworzy nowy dokument, wysyła zapytanie do danego dokumentu i następnie aktualizuje — wszystko w ramach transakcji ACID niejawnej. W dowolnym momencie podczas wykonywania Jeśli zostanie zgłoszony wyjątek języka JavaScript, cała transakcja przerywa.

    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();        
        var collectionLink = collectionManager.getSelfLink()

        // create a new document.
        collectionManager.createDocument(collectionLink,
            {id: name, author: author},
            function(err, documentCreated) {
                if(err) throw new Error(err.message);

                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function(err, matchingDocuments) {
                        if(err) throw new Error(err.message);

                        context.getResponse().setBody(matchingDocuments.length);

                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don’t need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);   
                        }
                    })
            })
    };

Klient może "Publikuj" powyżej logiki JavaScript w bazie danych potrzeby transakcyjnego wykonywania za pomocą żądania HTTP POST. Aby uzyskać więcej informacji o korzystaniu z metody HTTP, zobacz [RESTful interakcji z zasobami usługi Azure Cosmos DB](https://msdn.microsoft.com/library/azure/mt622086.aspx). 

    client.createStoredProcedureAsync(collection._self, {id: "CRUDProc", body: businessLogic})
       .then(function(createdStoredProcedure) {
            return client.executeStoredProcedureAsync(createdStoredProcedure.resource._self,
                "NoSQL Distilled",
                "Martin Fowler");
        })
        .then(function(result) {
            console.log(result);
        },
        function(error) {
            console.log(error);
        });


Zwróć uwagę, ponieważ baza danych natywną obsługę JSON i języka JavaScript, nie niezgodność system typów, nie ma "Mapowania" lub"lub magic generowania kodu wymagane.   

Procedury składowane i wyzwalacze wchodzić w interakcje z kolekcji i dokumentów w kolekcji za pomocą dobrze zdefiniowany model obiektów programu, który uwidacznia bieżącego kontekstu kolekcji.  

Kolekcje w interfejsie API SQL można tworzyć, usunięto, odczytu lub wyliczeniowego, łatwo za pomocą [interfejsów API REST](/rest/api/cosmos-db/) ani żadnego z [zestawów SDK klienta](sql-api-sdk-dotnet.md). Interfejs API SQL zawsze zapewnia wysoki poziom spójności odczytu lub przeszukując metadanych w kolekcji. Usunięcie kolekcji automatycznie zapewnia, że nie masz dostępu do dokumentów, załączniki, procedur składowanych, wyzwalaczy i funkcji UDF w nim zawarte.   

## <a name="stored-procedures-triggers-and-user-defined-functions-udf"></a>Procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika (UDF)
Zgodnie z opisem w poprzedniej sekcji, można napisać logikę aplikacji, aby uruchomić bezpośrednio z poziomu transakcji wewnątrz aparatu bazy danych. Logika aplikacji mogą być zapisywane w całości w języku JavaScript i mogą być modelowane jako procedurę przechowywaną, wyzwalacza lub funkcji definiowanych przez użytkownika. Języka JavaScript można wstawić kodu w ramach procedury składowanej lub wyzwalacza, replace, delete, odczytu lub tworzenia zapytań dotyczących dokumentów w kolekcji. Z drugiej strony JavaScript, w ramach funkcji zdefiniowanej przez użytkownika nie można wstawić, Zamień lub usuwanie dokumentów. Funkcje zdefiniowane przez użytkownika wyliczyć dokumenty zestawu wyników zapytania i utworzyć inny zestaw wyników. W przypadku wielu dzierżawców usługi Azure Cosmos DB wymusza ścisłe oparte nadzór nad zasobami. Każdy przechowywane procedury, wyzwalacza lub funkcji definiowanych przez użytkownika pobiera stały quantum zasobów systemu operacyjnego, aby wykonać swoją pracę. Ponadto procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika nie można połączyć z zewnętrznej biblioteki JavaScript i jest na czarnej liście, jeśli przekraczają budżet zasobów przydzielonych do nich. Można zarejestrować, wyrejestruj procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika z kolekcji przy użyciu interfejsów API REST.  Po rejestracji procedury składowanej, wyzwalacza lub funkcji definiowanych przez użytkownika jest wstępnie skompilowany i przechowywane jako kod bajtowy, która pobiera wykonane później. Poniższej sekcji pokazano, jak można użyć zestawu SDK języka JavaScript usługi Azure Cosmos DB do rejestracji, wykonywanie i wyrejestrować procedury składowanej, wyzwalaczy i funkcji zdefiniowanej przez użytkownika. Zestaw SDK języka JavaScript jest proste otoką nad [interfejsów API REST](/rest/api/cosmos-db/). 

### <a name="registering-a-stored-procedure"></a>Rejestrowanie procedury składowanej
Rejestracja procedura składowana utworzy nowy zasób procedury składowanej w kolekcji za pomocą żądania HTTP POST.  

    var storedProc = {
        id: "validateAndCreate",
        body: function (documentToCreate) {
            documentToCreate.id = documentToCreate.id.toUpperCase();

            var collectionManager = getContext().getCollection();
            collectionManager.createDocument(collectionManager.getSelfLink(),
                documentToCreate,
                function(err, documentCreated) {
                    if(err) throw new Error('Error while creating document: ' + err.message;
                    getContext().getResponse().setBody('success - created ' + 
                            documentCreated.name);
                });
        }
    };

    client.createStoredProcedureAsync(collection._self, storedProc)
        .then(function (createdStoredProcedure) {
            console.log("Successfully created stored procedure");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-stored-procedure"></a>Wykonywanie procedury przechowywanej
Wykonywanie procedury składowanej odbywa się przez wysłanie żądania HTTP POST dla istniejącego zasobu procedury składowanej przez przekazanie parametrów do procedur w treści żądania.

    var inputDocument = {id : "document1", author: "G. G. Marquez"};
    client.executeStoredProcedureAsync(createdStoredProcedure.resource._self, inputDocument)
        .then(function(executionResult) {
            assert.equal(executionResult, "success - created DOCUMENT1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-stored-procedure"></a>Wyrejestrowywanie procedury składowanej
Wyrejestrowywanie procedury składowanej odbywa się przez wystawienie HTTP DELETE dla istniejącego zasobu procedury składowanej.   

    client.deleteStoredProcedureAsync(createdStoredProcedure.resource._self)
        .then(function (response) {
            return;
        }, function(error) {
            console.log("Error");
        });


### <a name="registering-a-pre-trigger"></a>Rejestrowanie przed wyzwalacza
Rejestracja wyzwalacza odbywa się przez utworzenie nowego zasobu wyzwalacza w kolekcji za pomocą żądania HTTP POST. Można określić, jeśli wyzwalacz nie działa pre lub wyzwalacz post oraz typ operacji może być skojarzony z (na przykład, Utwórz, Replace, Delete lub wszystkie).   

    var preTrigger = {
        id: "upperCaseId",
        body: function() {
                var item = getContext().getRequest().getBody();
                item.id = item.id.toUpperCase();
                getContext().getRequest().setBody(item);
        },
        triggerType: TriggerType.Pre,
        triggerOperation: TriggerOperation.All
    }

    client.createTriggerAsync(collection._self, preTrigger)
        .then(function (createdPreTrigger) {
            console.log("Successfully created trigger");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-pre-trigger"></a>Wykonywanie wyzwalacza wstępnego
Wykonywanie wyzwalacza odbywa się, określając nazwę istniejącego wyzwalacza w czasie wysyłania żądania PUT/POST/DELETE zasobu dokumentu za pomocą nagłówka żądania.  

    client.createDocumentAsync(collection._self, { id: "doc1", key: "Love in the Time of Cholera" }, { preTriggerInclude: "upperCaseId" })
        .then(function(createdDocument) {
            assert.equal(createdDocument.resource.id, "DOC1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-pre-trigger"></a>Wyrejestrowywanie wstępne wyzwalacza
Wyrejestrowywanie wyzwalacza odbywa się za pośrednictwem wystawianie HTTP DELETE dla istniejącego zasobu wyzwalacza.  

    client.deleteTriggerAsync(createdPreTrigger._self);
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

### <a name="registering-a-udf"></a>Rejestrowanie funkcji zdefiniowanej przez użytkownika
Rejestracja funkcji zdefiniowanej przez użytkownika odbywa się przez utworzenie nowego zasobu funkcji zdefiniowanej przez użytkownika na kolekcję za pomocą żądania HTTP POST.  

    var udf = { 
        id: "mathSqrt",
        body: function(number) {
                return Math.sqrt(number);
        },
    };
    client.createUserDefinedFunctionAsync(collection._self, udf)
        .then(function (createdUdf) {
            console.log("Successfully created stored procedure");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-udf-as-part-of-the-query"></a>Wykonywanie funkcji zdefiniowanej przez użytkownika jako część zapytania
Funkcji zdefiniowanej przez użytkownika, można określić jako część zapytania SQL i jest używana jako sposób rozszerzać [język zapytań SQL](sql-api-sql-query-reference.md).

    var filterQuery = "SELECT udf.mathSqrt(r.Age) AS sqrtAge FROM root r WHERE r.FirstName='John'";
    client.queryDocuments(collection._self, filterQuery).toArrayAsync();
        .then(function(queryResponse) {
            var queryResponseDocuments = queryResponse.feed;
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-udf"></a>Wyrejestrowywanie funkcji zdefiniowanej przez użytkownika
Wyrejestrowywanie funkcji zdefiniowanej przez użytkownika po prostu odbywa się przez wystawienie HTTP DELETE dla istniejącego zasobu funkcji zdefiniowanej przez użytkownika.  

    client.deleteUserDefinedFunctionAsync(createdUdf._self)
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

Mimo że powyżej fragmenty wykazało, że rejestracji (POST), Wyrejestrowanie (PUT), Odczyt/list (GET) i wykonywania (POST) za pośrednictwem [zestaw JavaScript SDK](https://github.com/Azure/azure-documentdb-js), można również użyć [interfejsów API REST](/rest/api/cosmos-db/) lub innych [zestawów SDK klienta](sql-api-sdk-dotnet.md). 

## <a name="documents"></a>Dokumenty
Można wstawić, Zastąp, usuwanie, odczytu, wyliczanie i zapytania dowolnych dokumentów JSON w kolekcji. Usługa Azure Cosmos DB nie zmusza do dowolnego schematu i nie wymaga indeksów pomocniczych w celu obsługi zapytań dokumenty w kolekcji. Maksymalny rozmiar dla dokumentu jest 2 MB.   

Jest to usługa oferowanie prawdziwie otwartej bazy danych, usługi Azure Cosmos DB nie Wymyśl wszystkich typach wyspecjalizowanych danych (na przykład data i godzina) lub określonego kodowania dla dokumentów JSON. Usługa Azure Cosmos DB nie wymaga żadnych specjalnych konwencje JSON do skodyfikować relacje między różnymi dokumentami; Składnia SQL w usłudze Azure Cosmos DB zapewnia zaawansowane relacyjnych i hierarchicznych kwerend operatorów w dokumentach zapytania i projektu bez adnotacji specjalne lub konieczność skodyfikować relacje dokumenty przy użyciu różnych właściwości.  

Zgodnie z innymi zasobami dokumenty mogą być tworzone zastąpiony, usunięte, odczytu, wyliczyć i wyświetlić łatwo przy użyciu interfejsów API REST lub dowolny z [zestawów SDK klienta](sql-api-sdk-dotnet.md). Natychmiastowe usuwanie dokumentu zwolni limitu przydziału, odpowiadające na wszystkie zagnieżdżone załączników. Poziom spójności odczytu dokumentów obowiązują zasady spójności na koncie bazy danych. Te zasady można zastąpić na podstawie danego żądania, w zależności od wymagań spójności danych aplikacji. Podczas wykonywania zapytań dotyczących dokumentów, spójności odczytu następuje indeksowania trybie określonym w kolekcji. Dla "zgodne" to obowiązują zasady spójności dla konta. 

## <a name="attachments-and-media"></a>Załączniki i multimedia
Usługa Azure Cosmos DB umożliwia przechowywanie obiektów blob binarnego/media za pomocą usługi Azure Cosmos DB (maksymalnie 2 GB na jednym koncie) lub do magazynu zdalnego nośnika. Umożliwia on również reprezentują metadanych multimediów w specjalnym dokumencie nazywanym załącznika. Załącznik w usłudze Azure Cosmos DB to specjalny dokument (JSON), który odwołuje się nośnik blob przechowywane w innym miejscu. Załącznik jest po prostu specjalny dokument, który przechwytuje metadanych (na przykład, lokalizacji, autor itp.) nośnika, przechowywane w magazynie zdalnym nośnika. 

Należy wziąć pod uwagę aplikacji społecznościowych odczytu, która używa usługi Azure Cosmos DB do przechowywania adnotacje odręczne, a metadane, takie jak komentarze, wyróżnia zakładek, klasyfikacje, polubień/dislikes itp., skojarzony e-book danego użytkownika.   

* Książki, sam jest przechowywana zawartość w magazynie nośnika albo dostępne jako część konta bazy danych Azure Cosmos DB lub sklepie z multimediami zdalnego. 
* Aplikacja może przechowywać metadanych każdego użytkownika jako odrębne dokumentu — na przykład metadanych Jana Skoroszyt1 znajduje się w dokumencie, który odwołuje się /colls/joe/docs/book1. 
* Załączniki, wskazując stron zawartości książki danego użytkownika są przechowywane w odpowiedniej dokumentu, na przykład /colls/joe/docs/book1/chapter1, /colls/joe/docs/book1/chapter2 itp. 

W przykładach wymienionych powyżej Użyj identyfikatorów przyjazne do przekazania hierarchii zasobów. Zasoby są dostępne za pośrednictwem interfejsów API REST za pośrednictwem zasobów unikatowych identyfikatorów. 

Dla nośnika, który jest zarządzany przez usługę Azure Cosmos DB właściwość _nośnika załącznika odwołuje się do elementu multimedialnego przydzielone przez jego identyfikatora URI. Usługa Azure Cosmos DB zapewnia wyrzucania elementów zbieranie nośnik, po wszystkich innych odwołań są porzucane. Usługa Azure Cosmos DB automatycznie generuje załącznika podczas przekazywania nowego nośnika i wypełnia _nośnika, aby wskazać nowo dodanych nośnika. Jeśli zdecydujesz się przechowywać nośnika w magazynie obiektów blob zdalnego zarządzany przez użytkownika (na przykład, OneDrive, usługi Azure Storage, DropBox itp.), nadal można załączniki odwoływać się do nośnika. W takim przypadku utworzysz załącznika samodzielnie i wypełnianie jej właściwość _nośnika.   

Zgodnie z innymi zasobami, można utworzyć załączniki, zastąpione, usunięte, odczytać lub wyliczenia przy użyciu interfejsów API REST lub dowolne zestawy SDK klientów. Podobnie jak w przypadku dokumentów, poziomu spójności odczytu załączniki obowiązują zasady spójności na koncie bazy danych. Te zasady można zastąpić na podstawie danego żądania, w zależności od wymagań spójności danych aplikacji. Podczas badania, pod kątem załączników, spójności odczytu następuje indeksowania trybie określonym w kolekcji. Dla "zgodne" to obowiązują zasady spójności dla konta. 
 

## <a name="users"></a>Użytkownicy
Użytkownik usługi Azure Cosmos DB reprezentuje logiczny obszar nazw do grupowania uprawnień. Użytkownik usługi Azure Cosmos DB może odpowiadać użytkownika systemu zarządzania tożsamościami lub ról wstępnie zdefiniowanych aplikacji. Usługi Azure Cosmos DB użytkownik po prostu reprezentuje klasą abstrakcyjną, aby zgrupować zestaw uprawnień w bazie danych.   

Do wykonania wielu dzierżawców w aplikacji, mogą tworzyć użytkowników w usłudze Azure Cosmos DB, która odnosi się do rzeczywistych użytkowników lub dzierżaw w aplikacji. Następnie można utworzyć uprawnienia dla danego użytkownika, które odpowiadają kontrolę dostępu do różnych kolekcji dokumentów, załączniki, itp.   

Jak aplikacje muszą skalowanie za pomocą wzrostem użytkownika, można przyjąć różne sposoby fragmentów danych. Każdy z użytkowników może modelować w następujący sposób:   

* Każdy użytkownik mapuje do bazy danych.
* Każdy użytkownik mapuje do kolekcji. 
* Dokumenty odpowiadający wielu użytkowników przejdź do dedykowanych kolekcji. 
* Dokumenty odpowiadający wielu użytkowników przejdź do zestawu kolekcji.   

Niezależnie od tego, strategię fragmentowania określonych wybierzesz, możesz modelować rzeczywistych użytkowników jako użytkowników w bazie danych Azure Cosmos DB i skojarzyć szczegółowe uprawnienia do poszczególnych użytkowników.  

![Kolekcje użytkowników][3]  
**Strategie fragmentowania i modelowania użytkowników**

Podobnie jak inne zasoby użytkowników w usłudze Azure Cosmos DB można utworzyć, zastąpione, usunięty, odczytać lub wyliczenia przy użyciu interfejsów API REST lub dowolne zestawy SDK klientów. Usługa Azure Cosmos DB jest zawsze zapewnia wysoki poziom spójności odczytu lub przeszukując metadane użytkownika zasobu. Warto wskazywanie, to usunięcie użytkownika automatycznie zapewnia, że nie masz dostępu do jakichkolwiek uprawnień w nim zawarte. Mimo że usługę Azure Cosmos DB odzyskuje przydział uprawnień jako część usuniętego użytkownika w tle, usunięto uprawnienia są dostępne natychmiast ponownie do użycia.  

## <a name="permissions"></a>Uprawnienia
Z punktu widzenia kontroli dostępu do zasobów, takich jak konta bazy danych, baz danych, użytkowników i uprawnienia są traktowane jako *administracyjne* zasobów, ponieważ wymagają one uprawnienia administracyjne. Z drugiej strony, zasobów, w tym kolekcje, dokumenty, załączniki, procedury składowane, wyzwalacze, a funkcje zdefiniowane przez użytkownika są o określonym zakresie w ramach określonej bazy danych i uznane za *zasobów aplikacji*. Odpowiadający dwa typy zasobów i role, które dostęp do nich (to znaczy administratora i użytkownika), modelu autoryzacji definiuje dwa typy *klucze dostępu*: *klucz główny* i  *Klucz zasobu*. Klucz główny w ramach konta bazy danych są udostępniane i dewelopera (lub administratora) trwa inicjowanie obsługi konta bazy danych. Ten klucz główny ma semantykę administratora może służyć do autoryzowania dostępu do zasobów administracyjnych i aplikacji. Klucz zasobu jest klucz dostępu szczegółowej, który umożliwia dostęp do *określonych* zasobów aplikacji. W związku z tym przechwytuje relację między użytkownikiem bazy danych i uprawnienia użytkownika dla określonego zasobu (na przykład kolekcji, dokumentów, załącznik, procedury składowanej, wyzwalacza lub funkcji definiowanych przez użytkownika).   

Jedynym sposobem uzyskania klucza zasobu jest utworzenie uprawnień zasobami dostępnymi w ramach danego użytkownika. Aby można było utworzyć lub pobrać uprawnień, należy przedstawić klucz główny w nagłówku autoryzacji. Zasób uprawnienie łączy zasobu, dostępu oraz użytkownika. Po utworzeniu zasobu uprawnień, użytkownik musi tylko przedstawić klucz skojarzony zasób w celu uzyskania dostępu do odpowiednich zasobów. Z tego powodu klucz zasobu mogą być wyświetlane jako logicznych i compact reprezentację zasobu uprawnienia.  

Zgodnie z innymi zasobami, uprawnieniami w usłudze Azure Cosmos DB można utworzyć, zastąpione, usunięte, odczytać lub wyliczenia przy użyciu interfejsów API REST lub poszczególnych zestawów SDK klienta. Usługa Azure Cosmos DB jest zawsze zapewnia wysoki poziom spójności odczytu lub przeszukując metadanych uprawnienia. 

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o pracy z zasobami przy użyciu poleceń HTTP w [RESTful interakcji z zasobami usługi Azure Cosmos DB](https://msdn.microsoft.com/library/azure/mt622086.aspx).

[1]: media/sql-api-resources/resources1.png
[2]: media/sql-api-resources/resources2.png
[3]: media/sql-api-resources/resources3.png

