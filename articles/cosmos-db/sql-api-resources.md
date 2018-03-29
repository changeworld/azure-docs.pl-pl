---
title: Pojęcia i model zasobów w usłudze Azure DB rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: Więcej informacji na temat modelu hierarchiczna Azure rozwiązania Cosmos DB baz danych, kolekcji, funkcji zdefiniowanych przez użytkownika (UDF), dokumentów, uprawnienia do zarządzania zasobami i inne.
keywords: Hierarchical model, cosmosdb, azure, Microsoft azure
services: cosmos-db
documentationcenter: ''
author: rafats
manager: jhubbard
ms.assetid: ef9d5c0c-0867-4317-bb1b-98e219799fd5
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2018
ms.author: rafats
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f64d79cd3929a279c7e279e74b0b21d163c0fa45
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-hierarchical-resource-model-and-core-concepts"></a>Podstawowe pojęcia i hierarchiczny model zasobów usługi Azure Cosmos DB

Jednostki bazy danych, które bazy danych Azure rozwiązania Cosmos zarządza są określane jako **zasobów**. Każdy zasób jest unikatowo identyfikowana przez logiczny identyfikator URI. Użytkownik może interakcyjnie przeprowadzić zasobów przy użyciu standardowych poleceń HTTP, nagłówki żądań i odpowiedzi i kodów stanu. 

Ten artykuł zawiera odpowiedzi na następujące pytania:

* Co to jest model zasobów Azure rozwiązania Cosmos DB?
* Co to są systemu zdefiniowanych zasobów przeciwieństwie zasoby zdefiniowane przez użytkownika?
* Jak rozwiązać zasobu?
* Jak pracować z kolekcjami
* Jak pracować z procedur składowanych, wyzwalaczy i funkcje zdefiniowane przez użytkownika (UDF)

W następujących wideo Azure Menedżera programów DB rozwiązania Cosmos Andrew Liu przeprowadzi Cię przez model zasobów bazy danych Azure rozwiązania Cosmos. 

> [!VIDEO https://www.youtube.com/embed/luWFgTP0IL4]
>
>

## <a name="hierarchical-resource-model"></a>Model hierarchiczna zasobów
Jak na poniższym diagramie przedstawiono rozwiązania Cosmos bazy danych Azure hierarchiczna **model zasobów** składa się z zestawów zasobów w ramach konta bazy danych, każda mogą być adresowane za pomocą logicznych i stabilny identyfikator URI. Zestaw zasobów są nazywane **źródła danych** w tym artykule. 

> [!NOTE]
> Azure DB rozwiązania Cosmos oferuje wysoce efektywne protokołu TCP, który jest również RESTful jego model komunikacji, dostępne za pośrednictwem [interfejs API klienta SQL .NET](sql-api-sdk-dotnet.md).
> 
> 

![Azure DB rozwiązania Cosmos hierarchiczna zasobów modelu][1]  
**Model hierarchiczna zasobów**   

Aby rozpocząć pracę z zasobami, należy najpierw [Tworzenie konta bazy danych](create-sql-api-dotnet.md) przy użyciu subskrypcji platformy Azure. Konto bazy danych może składać się z zestawem **baz danych**, każda z nich zawiera wiele **kolekcje**, każdy z które z kolei zawierają ** procedury składowane, wyzwalacze, funkcje UDF, dokumenty i powiązane  **załączniki**. Bazy danych ma również skojarzonych **użytkowników**, każdy z zestawem **uprawnienia** można uzyskać dostępu do kolekcji, procedur składowanych, wyzwalaczy, funkcji UDF, dokumentów lub załączników. Bazy danych, użytkownicy, uprawnienia i kolekcje są zdefiniowane w systemie zasoby z dobrze znanych schematów, dokumentów i załączników zawierać dowolne, zdefiniowane przez użytkownika zawartość JSON.  

| Zasób | Opis |
| --- | --- |
| Konto bazy danych |Konto bazy danych jest skojarzona z zestawu baz danych i stałą magazynu obiektów blob dla załączników. Można utworzyć co najmniej jedno konto bazy danych przy użyciu subskrypcji platformy Azure. Aby uzyskać więcej informacji, odwiedź stronę [cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/). |
| Database (Baza danych) |Baza danych jest kontenerem logicznym magazynu dokumentów podzielonym na partycje w kolekcjach. Istnieje również kontener użytkowników. |
| Użytkownik |Logiczne przestrzeń nazw dla zakresu uprawnień. |
| Uprawnienie |Token autoryzacji skojarzonych z użytkownikiem, aby uzyskać dostęp do określonego zasobu. |
| Collection |Kolekcja jest kontenerem dokumentów JSON i skojarzonej logiki aplikacji JavaScript. Kolekcja to płatna jednostka, której [koszt](performance-levels.md) zależy od poziomu wydajności skojarzonego z tą kolekcją. Kolekcje mogą znajdować się na jednej lub wielu partycjach/serwerach i mogą być skalowane do obsługi praktycznie nieograniczonej ilości magazynu lub przepływności. |
| Procedura składowana |Logika aplikacji napisane w języku JavaScript, który jest zarejestrowany w kolekcji i transakcyjnie wykonywane w ramach aparatu bazy danych. |
| Wyzwalacz |Logiki aplikacji napisane w języku JavaScript wykonane przed lub po każdej operacji insert, Zamień lub operacji usuwania. |
| UDF |Logika aplikacji napisane w języku JavaScript. Funkcje UDF umożliwiają modelu operator niestandardowe zapytania i tym samym rozszerzać język zapytań SQL interfejsu API. |
| Dokument |Zdefiniowane przez użytkownika (dowolną) zawartość JSON. Domyślnie Brak schematu musi być zdefiniowany ani indeksów pomocniczych, trzeba podać dla wszystkich dokumentów dodany do kolekcji. |
| Załącznik |Załącznik jest specjalny dokument zawierający odniesienia i skojarzone metadane dla obiekt blob/nośnika zewnętrznego. Deweloper można wybrać obiektu blob zarządza DB rozwiązania Cosmos lub zapisz go z dostawcą usług zewnętrznych obiektów blob, np. OneDrive, Dropbox. |

## <a name="system-vs-user-defined-resources"></a>System i zasoby zdefiniowane przez użytkownika
Zasoby, takie jak konta bazy danych, bazy danych, kolekcji, użytkownicy, uprawnienia, procedur składowanych, wyzwalaczy i funkcji UDF — wszystkie mają stałego schematu i są nazywane zasobów systemowych. Z kolei zasobów, takich jak dokumenty i załączników w schemacie nie obowiązują żadne ograniczenia i przedstawiono zasoby zdefiniowane przez użytkownika. Do rozwiązania Cosmos bazy danych zarówno system i zasoby zdefiniowane przez użytkownika są reprezentowane i zarządzane jako zgodne standard JSON. Wszystkie zasoby, system lub użytkownik zdefiniowane, należy mieć następujące właściwości wspólne:

> [!NOTE]
> Wszystkie właściwości wygenerowany przez system w zasobie mają przedrostek znaku podkreślenia (_) w ich reprezentacja JSON.
> 
> 

<table>
    <tbody>
        <tr>
            <td valign="top"><p><strong>Właściwość</strong></p></td>
            <td valign="top"><p><strong>Użytkownika można ustawić lub wygenerowany przez system?</strong></p></td>
            <td valign="top"><p><strong>Cel</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>_rid</p></td>
            <td valign="top"><p>Wygenerowany przez system</p></td>
            <td valign="top"><p>System generowany unikatowy i hierarchicznego identyfikator zasobu</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_etag</p></td>
            <td valign="top"><p>Wygenerowany przez system</p></td>
            <td valign="top"><p>etag wymaga optymistyczne sterowanie współbieżnością zasobów</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_ts</p></td>
            <td valign="top"><p>Wygenerowany przez system</p></td>
            <td valign="top"><p>Zaktualizowano sygnatura czasowa ostatniego zasobu</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_self</p></td>
            <td valign="top"><p>Wygenerowany przez system</p></td>
            <td valign="top"><p>Unikatowy identyfikator adresowanego URI zasobu</p></td>
        </tr>
        <tr>
            <td valign="top"><p>id</p></td>
            <td valign="top"><p>Albo</p></td>
            <td valign="top"><p>Zdefiniowane przez użytkownika unikatowa nazwa zasobu (o tej samej wartości klucza partycji). Jeśli użytkownik nie określono identyfikatora, identyfikator jest wygenerowany przez system</p></td>
        </tr>
    </tbody>
</table>

### <a name="wire-representation-of-resources"></a>Reprezentacja przewodowy zasobów
Rozwiązania cosmos DB nie wprowadzić wszystkich rozszerzeń zastrzeżonych do kodowania standard lub specjalne JSON; działa ze standardowych zgodne dokumentów JSON.  

### <a name="addressing-a-resource"></a>Adresowanie zasobu
Wszystkie zasoby są adresowanego identyfikatora URI. Wartość **_self** właściwość zasobu reprezentuje względny identyfikator URI zasobu. Format identyfikatora URI składa się z /\<źródła danych\>/ {_rid} segmenty ścieżki:  

| Wartość _self | Opis |
| --- | --- |
| /DBS |Źródło danych bazy danych w ramach konta bazy danych |
| /dbs/{dbName} |Baza danych o identyfikatorze odpowiadającym wartości {dbName} |
| /dbs/{dbName}/colls/ |Źródło danych kolekcji w bazie danych |
| /dbs/{dbName}/colls/{collName} |Kolekcja o identyfikatorze odpowiadającym wartości {collName} |
| /dbs/{dbName}/colls/{collName}/docs |Źródła danych dokumentów w kolekcji |
| /dbs/{dbName}/colls/{collName}/docs/{docId} |Dokument o identyfikatorze odpowiadającym wartości {doc} |
| /dbs/{dbName}/users/ |Źródło danych użytkowników w bazie danych |
| /dbs/{dbName}/users/{userId} |Użytkownik o identyfikatorze odpowiadającym wartości {użytkownika} |
| /dbs/{dbName}/users/{userId}/permissions |Źródło danych uprawnienia użytkownika |
| /dbs/{dbName}/users/{userId}/permissions/{permissionId} |Uprawnienie o identyfikatorze odpowiadającym wartości {uprawnienie} |

Każdy zasób ma unikatową nazwę użytkownika udostępniane za pośrednictwem funkcji właściwość identyfikatora. Uwaga: w przypadku dokumentów, jeśli użytkownik nie określono identyfikatora, zestawy SDK automatycznie generować Unikatowy identyfikator dla dokumentu. Identyfikator jest zdefiniowane przez użytkownika ciąg, maksymalnie 256 znaków, które jest unikatowa w ramach zasobu określonego nadrzędnej. 

Każdego zasobu ma również identyfikator zasobu hierarchiczna wygenerowany przez system (zwaną także identyfikatorów RID), który jest dostępny za pośrednictwem właściwości _rid. Identyfikator RID koduje całej hierarchii zasobu i jest wygodny reprezentacji wewnętrznej używanych do wymuszania integralności referencyjnej w sposób rozproszony. Identyfikator RID jest unikatowy w ramach konta bazy danych i ona jest używana wewnętrznie przez rozwiązania Cosmos bazy danych dla wydajny routing, bez konieczności wyszukiwań krzyżowego partycji. Wartości _self i właściwości _rid są zarówno alternatywnych, jak i canonical reprezentacji zasobu. 

Interfejsy API REST obsługują adresowania zasobów i routingu żądań przez zarówno identyfikatora i właściwości _rid.

## <a name="database-accounts"></a>Konta bazy danych
Można udostępnić co najmniej jednego rozwiązania Cosmos DB bazy danych konta przy użyciu subskrypcji platformy Azure.

Możesz utworzyć i zarządzać kontami bazy danych DB rozwiązania Cosmos za pośrednictwem portalu Azure pod adresem [ http://portal.azure.com/ ](https://portal.azure.com/). Tworzenie i zarządzanie nimi konta bazy danych wymaga dostępu administracyjnego i może zostać wykonane tylko w ramach Twojej subskrypcji platformy Azure. 

### <a name="database-account-properties"></a>Właściwości konta bazy danych
W ramach inicjowania obsługi i zarządzania nimi konta bazy danych można skonfigurować i przeczytaj następujące właściwości:  

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Nazwa właściwości</strong></p></td>
            <td valign="top"><p><strong>Opis</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Zasady spójności</p></td>
            <td valign="top"><p>Ustaw tę właściwość, aby skonfigurować domyślny poziom spójności dla wszystkich zbiorów przy użyciu tego konta bazy danych. Można zmienić poziomu zgodności dla danego żądania przy użyciu nagłówek żądania [x-ms--poziomu spójności]. <p><p>Ta właściwość ma zastosowanie tylko do <i>zasoby zdefiniowane przez użytkownika</i>. System wszystkich zdefiniowanych zasobów są skonfigurowane do obsługi operacji odczytu/zapytania z wysoki poziom spójności.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Klucze autoryzacji</p></td>
            <td valign="top"><p>Klucze podstawowe i pomocnicze wzorca i tylko do odczytu zapewniające dostęp administracyjny do wszystkich zasobów w ramach konta bazy danych.</p></td>
        </tr>
    </tbody>
</table>

Oprócz udostępniania, konfigurowanie i zarządzanie nimi konta bazy danych z portalu Azure można również programowo utworzyć i zarządzać za pomocą konta bazy danych DB rozwiązania Cosmos [interfejsów API REST usługi Azure rozwiązania Cosmos DB](/rest/api/documentdb/) oraz [zestawów SDK klienta](sql-api-sdk-dotnet.md).  

## <a name="databases"></a>Bazy danych
Bazy danych DB rozwiązania Cosmos jest kontenerem logicznym jedną lub więcej kolekcji i użytkowników, jak pokazano na poniższym diagramie. Można utworzyć dowolną liczbę baz danych w ramach konta bazy danych DB rozwiązania Cosmos limitom oferty.  

![Model bazy danych konta i kolekcje hierarchiczne][2]  
**Baza danych jest kontenerem logicznym użytkowników i kolekcji**

Bazy danych może zawierać nieograniczoną dokumentu magazynu na partycje w kolekcjach.

### <a name="elastic-scale-of-an-azure-cosmos-db-database"></a>Elastyczne skalowanie bazy danych Azure DB rozwiązania Cosmos
Baza danych DB rozwiązania Cosmos jest elastyczny domyślnie — od kilka GB do poziomu petabajtów dyski SSD kopię dokumentu, a udostępnionej przepływności. 

W przeciwieństwie do bazy danych w tradycyjnych RDBMS bazy danych w bazie danych rozwiązania Cosmos jest poza zakresem na jednym komputerze. DB rozwiązania Cosmos potrzeb skalowania aplikacji rośnie, można utworzyć więcej kolekcji i baz danych. W rzeczywistości różne aplikacje firm pierwszy w programie Microsoft korzystają bazy danych Azure rozwiązania Cosmos w skali konsumenta przez utworzenie bardzo dużych baz danych Azure DB rozwiązania Cosmos każdego zawierającego tysiące kolekcje z terabajtów miejsca do magazynowania dokumentu. Można zwiększać i zmniejszać bazy danych przez dodanie lub usunięcie kolekcji w celu spełnienia wymagań skali aplikacji. 

Można utworzyć dowolną liczbę kolekcji w bazie danych może ulec oferty. Każda kolekcja ma kopii dyski SSD, a zainicjowanej dla Ciebie w zależności od warstwy wydajności wybranej przepływności.

Baza danych bazy danych Azure rozwiązania Cosmos jest również kontener użytkowników. Użytkownik, w ruchu jest logiczną przestrzeń nazw dla zestaw uprawnień, który zawiera szczegółowe autoryzacji i dostępu do kolekcji, dokumentów i załączników.  

Zgodnie z innymi zasobami w model zasobów bazy danych Azure rozwiązania Cosmos baz danych można tworzyć, zastąpiony, usunięty, odczytać lub wyliczyć łatwo za pomocą [interfejsów API REST](/rest/api/documentdb/) lub [zestawów SDK klienta](sql-api-sdk-dotnet.md). Azure DB rozwiązania Cosmos zapewnia wysoki poziom spójności odczytu lub zapytanie dotyczące metadanych zasobu bazy danych. Automatyczne usunięcie bazy danych zapewnia, że nie masz dostępu do żadnego z kolekcji lub użytkowników w nim zawarte.   

## <a name="collections"></a>Kolekcje
Kolekcja DB rozwiązania Cosmos jest kontenerem dokumentów JSON. 

### <a name="elastic-ssd-backed-document-storage"></a>Elastyczne SSD kopii magazynu dokumentów
Kolekcja jest bardzo elastyczny — automatycznie rozwoju i zmniejszana, jak dodać lub usunąć dokumentów. Kolekcje są zasoby logicznej i może obejmować co najmniej jednej partycji fizycznej lub serwerów. Liczba partycji w ramach kolekcji zależy od rozwiązania Cosmos bazy danych na podstawie rozmiaru pamięci masowej i przepływność kolekcji. Każdej partycji w bazie danych rozwiązania Cosmos ma stałą kopie dysków SSD magazynu skojarzone z nim i są replikowane w celu zapewnienia wysokiej dostępności. Zarządzanie partycji pełni zarządza bazy danych Azure rozwiązania Cosmos i nie trzeba pisania złożonego kodu lub Zarządzanie partycjami. Kolekcje rozwiązania cosmos bazy danych są **praktycznie nieograniczonej** pod względem pamięci masowej i przepływność. 

### <a name="automatic-indexing-of-collections"></a>Automatycznego indeksowania w kolekcji
Azure DB rozwiązania Cosmos jest systemem true bazy danych bez schematu. Nie przyjmuje ani nie wymaga żadnego schematu dla dokumentów JSON. Dokumenty podczas dodawania do kolekcji, bazy danych rozwiązania Cosmos Azure automatycznie indeksuje je i są dostępne dla Ciebie do zapytania. Automatyczne indeksowanie dokumentów, bez konieczności schematu lub indeksów pomocniczych jest kluczowych możliwości bazy danych Azure rozwiązania Cosmos i jest włączony technikami konserwacji indeksu zoptymalizowanych pod kątem zapisu, zwolnić blokady i opartą na strukturze dziennika. Azure DB rozwiązania Cosmos obsługuje stałej ilości bardzo szybkiego zapisu służąc nadal spójne zapytania. Dokument programu i indeksu magazynu są używane do obliczania magazynu wykorzystanych w ramach każdej kolekcji. Można kontrolować, magazynu i wydajności kompromisy skojarzone z indeksowania Konfigurując zasady indeksowania dla kolekcji. 

### <a name="configuring-the-indexing-policy-of-a-collection"></a>Konfigurowanie zasady indeksowania w kolekcji
Zasady indeksowania wszystkich kolekcji pozwala wprowadzić wydajności i magazynu kompromisy skojarzone z indeksowania. W ramach konfiguracji indeksowania dostępne są następujące opcje:  

* Wybierz, czy kolekcja automatycznie indeksuje wszystkie dokumenty lub nie. Domyślnie wszystkie dokumenty są automatycznie indeksowane. Można wyłączyć automatyczne indeksowanie i selektywnie dodać tylko dokumenty do indeksu. Z drugiej strony selektywnie można wykluczyć tylko określonych dokumentów. Można to osiągnąć przez ustawienie automatyczne właściwość jako wartość true lub false na indexingPolicy kolekcji i użycie nagłówka żądania [x-ms-indexingdirective] podczas wstawiania, zastępowanie lub usuwanie dokumentu.  
* Określ, czy do dołączania lub wykluczania określonych ścieżek lub wzorce w dokumentach z indeksu. Można to osiągnąć przez ustawienie includedPaths i excludedPaths na indexingPolicy kolekcji odpowiednio. Można również skonfigurować magazynu i wydajności kompromisy dla zapytań o zakres i wyznaczania wartości skrótu dla wzorców określonej ścieżki. 
* Wybór między synchroniczne (zgodne) i aktualizacje asynchroniczne indeksu (lazy). Domyślnie indeks jest aktualizowana synchronicznie na każdym insert, replace lub usuwania dokumentu do kolekcji. Dzięki temu zapytania uwzględnić poziomu spójności co odczytuje dokument. Podczas zapisu zoptymalizowany i obsługuje woluminy utrzymujących zapisów dokumentu oraz konserwacji synchroniczne indeksu i obsługujący spójne zapytania bazy danych Azure rozwiązania Cosmos można skonfigurować pewne kolekcji, aby zaktualizować ich indeksu w trybie opóźnienia. Indeksowanie z opóźnieniem zwiększa wydajność zapisu dalsze i jest idealny dla scenariuszy wprowadzanie zbiorczego głównie ciężki odczytu kolekcji.

Zasady indeksowania można zmienić, wykonując PUT w kolekcji. Można to osiągnąć za pośrednictwem [klienta SDK](sql-api-sdk-dotnet.md), [portalu Azure](https://portal.azure.com) lub [interfejsów API REST](/rest/api/documentdb/).

### <a name="querying-a-collection"></a>Wykonywanie zapytania kolekcji
Dokumentów w ramach kolekcji może zawierać dowolne schematów i dokumentów w kolekcji można zbadać bez podawania żadnego schematu lub wyprzedzeniem indeksów pomocniczych. Można zbadać za pomocą kolekcji [odwołania do składni SQL DB rozwiązania Cosmos Azure](https://msdn.microsoft.com/library/azure/dn782250.aspx), zapewniające sformatowanego operatorów hierarchiczna relacyjnych i przestrzennych i rozszerzalność dzięki oparte na języku JavaScript funkcji UDF. Gramatyka JSON umożliwia modelowanie dokumentów JSON jako drzewa z etykietami jako węzłami drzewa. To jest wykorzystywana zarówno przez techniki automatycznego indeksowania SQL API, a także dialekt SQL Azure rozwiązania Cosmos DB. Język zapytań SQL składa się z trzech głównych aspektach:   

1. Niewielki zestaw operacji zapytania, które mapowania naturalnie struktura drzewa, w tym hierarchiczna zapytania i prognozy. 
2. Podzbiór relacyjne operacji, w tym kompozycji, filtr projekcje, agreguje i self sprzężenia. 
3. Czysty JavaScript na podstawie funkcji UDF, które współpracują z (1) i (2).  

Model zapytań bazy danych Azure rozwiązania Cosmos próbuje równowagę między funkcji, wydajności i prostota. Aparat bazy danych Azure DB rozwiązania Cosmos natywnie kompiluje i wykonuje instrukcje zapytania SQL. Można zbadać kolekcji przy użyciu [interfejsów API REST](/rest/api/documentdb/) lub [zestawów SDK klienta](sql-api-sdk-dotnet.md). Zestaw .NET SDK jest dostarczany z dostawcy LINQ.

> [!TIP]
> Można wypróbować interfejsu API SQL i wykonywania kwerend SQL do naszego zestawu danych w [Plac zabaw dla zapytań](https://www.documentdb.com/sql/demo).
> 
> 

## <a name="multi-document-transactions"></a>Transakcji wielu dokumentów
Transakcji bazy danych Podaj bezpieczne i przewidywalne model programowania zajmujących się równoczesnych zmian danych. W RDBMS, należy napisać tradycyjny sposób zapisu logiki biznesowej **procedur składowanych** i/lub **wyzwalaczy** i wysyłane do serwera bazy danych dla transakcyjnego wykonywania. W RDBMS programistom jest niezbędnego do dwóch różnych języków programowania: 

* Aplikacja (nietransakcyjnej) programowania w języku (na przykład, JavaScript, Python, C#, Java, itp.)
* T-SQL, transakcyjnego języka programowania, natywnie wykonywanym przez bazę danych

Ze względu na jego głębokie zaangażowanie JavaScript i JSON bezpośrednio wewnątrz aparatu bazy danych, bazy danych Azure rozwiązania Cosmos zapewnia model programowania intuicyjne JavaScript wykonywanie logiki aplikacji bezpośrednio na podstawie kolekcji pod względem procedur składowanych i Wyzwalacze. Dzięki temu dla obu z następujących czynności:

* Efektywne wykonanie współbieżności kontrolować odzyskiwania automatycznego indeksowania wykresów obiektów JSON bezpośrednio w aparacie bazy danych
* Naturalnie wyrażanie przepływu sterowania, zmiennej zakresu przypisania i integracji w nim elementów podstawowych transakcji bazy danych bezpośrednio pod względem JavaScript język programowania obsługi wyjątków

Logiki JavaScript zarejestrowany na poziomie kolekcji można następnie wystawiania operacje bazy danych na dokumentach danej kolekcji. Azure DB rozwiązania Cosmos niejawnie opakowuje oparty na języku JavaScript procedury składowane i wyzwalacze w ramach transakcje ACID otoczenia, z użyciem izolacji migawki wszystkich dokumentów w kolekcji. Jeśli w trakcie wykonywania logiki JavaScript zgłosi ona wyjątek, cała transakcja zostaje przerwana. Jest bardzo prosty model programowania wynikowy jeszcze zaawansowanych. Deweloperzy JavaScript Pobierz model programowania "trwałe" podczas nadal przy użyciu konstrukcji języka znanych i biblioteki w nim elementów podstawowych.   

Możliwość wykonania JavaScript bezpośrednio wewnątrz aparatu bazy danych w przestrzeni adresowej jako pula buforów umożliwia wydajności i transakcyjnego wykonywania operacji bazy danych dla dokumentów w kolekcji. Ponadto aparat bazy danych DB rozwiązania Cosmos ułatwia głębokie zaangażowanie JSON i JavaScript eliminuje wszelkie impedancji niezgodność między systemami typu aplikacji i bazy danych.   

Po utworzeniu kolekcji, możesz zarejestrować procedur składowanych, wyzwalaczy i funkcji UDF z kolekcji przy użyciu [interfejsów API REST](/rest/api/documentdb/) lub [zestawów SDK klienta](sql-api-sdk-dotnet.md). Po rejestracji można odwoływać się i ich wykonanie. Należy rozważyć następujące procedury składowanej napisanych w całości w JavaScript, poniższy kod przyjmuje dwa argumenty (Nazwa książki i nazwisko autora) i tworzy nowy dokument, wysyła zapytanie do dokumentu i następnie aktualizuje go — wszystko w ramach transakcji ACID niejawnej. W dowolnym momencie podczas wykonywania Jeśli zgłoszono wyjątek JavaScript, cała transakcja jest przerywana.

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

Klienta można "wysłać" powyżej logiki JavaScript do bazy danych dla transakcyjnego wykonywania za pośrednictwem protokołu HTTP POST. Aby uzyskać więcej informacji o korzystaniu z metody HTTP, zobacz [RESTful interakcji z zasobami Azure DB rozwiązania Cosmos](https://msdn.microsoft.com/library/azure/mt622086.aspx). 

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


Należy zauważyć, że ponieważ bazy danych natywnie obsługuje usługę JSON oraz języka JavaScript, nie jest nie niezgodność typów w systemie, "OR mapowania" ani wymagane magic generowania kodu.   

Procedury składowane i wyzwalaczy współdziałają z kolekcji i dokumentów w kolekcji za pomocą modelu dobrze zdefiniowanego obiektu, który udostępnia bieżącego kontekstu kolekcji.  

Kolekcje w interfejsie API SQL można tworzyć, usunięto, odczytu lub wyliczeniowego, łatwo za pomocą [interfejsów API REST](/rest/api/documentdb/) lub [zestawów SDK klienta](sql-api-sdk-dotnet.md). Interfejsu API SQL zawsze zapewnia wysoki poziom spójności odczytu lub zapytanie dotyczące metadanych kolekcji. Usuwanie kolekcji automatycznie gwarantuje, że nie masz dostępu do tych dokumentów, załączniki, procedur składowanych, wyzwalaczy i funkcji UDF w nim zawarte.   

## <a name="stored-procedures-triggers-and-user-defined-functions-udf"></a>Procedur składowanych, wyzwalaczy i funkcji zdefiniowanych użytkownika (UDF)
Zgodnie z opisem w poprzedniej sekcji, można napisać logiki aplikacji do uruchamiania bezpośrednio z poziomu transakcji wewnątrz aparatu bazy danych. Logiki aplikacji mogą być zapisywane w całości w języku JavaScript i mogą być modelowane jako procedury przechowywanej, wyzwalacza lub funkcji zdefiniowanej przez użytkownika. JavaScript można wstawić kodu w procedurze składowanej lub wyzwalacza, replace, delete, odczytu lub zapytania dokumentów w kolekcji. Z drugiej strony JavaScript w funkcji zdefiniowanej przez użytkownika nie może wstawić, Zamień lub usuwanie dokumentów. Funkcje UDF wyliczyć dokumenty zestawu wyników zapytania i utworzyć inny zestaw wyników. Dla wielu dzierżawców Azure DB rozwiązania Cosmos wymusza ładu strict oparte na rezerwacjach zasobów. Każdy przechowywane procedury, wyzwalacza lub UDF pobiera stałym quantum zasobów systemu operacyjnego, aby wykonać swoją pracę. Ponadto procedur składowanych, wyzwalaczy i funkcji UDF nie można połączyć z zewnętrznej biblioteki języka JavaScript i traktowane są jako zabronione przekraczających budżetów zasobów przydzielonych do nich. Możesz zarejestrować, wyrejestruj procedur składowanych, wyzwalaczy i funkcji UDF z kolekcji przy użyciu interfejsów API REST.  Po rejestracji procedury przechowywanej, wyzwalacza lub funkcji zdefiniowanej przez użytkownika jest wstępnie skompilowany i przechowywane jako kod bajtowy, który jest wykonywany później. Następujące illustrateshow ssection zestawu JavaScript SDK usługi Azure rozwiązania Cosmos bazy danych służy do rejestrowania, wykonaj i wyrejestruj procedury składowanej, wyzwalaczy i funkcji zdefiniowanej przez użytkownika. Zestaw SDK JavaScript jest proste otoki za pośrednictwem [interfejsów API REST](/rest/api/documentdb/). 

### <a name="registering-a-stored-procedure"></a>Rejestrowanie procedury składowanej
Rejestracja procedury składowanej tworzy nowy zasób procedury składowanej w kolekcji za pośrednictwem protokołu HTTP POST.  

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
Wykonywanie procedury składowanej odbywa się przez wystawienie przez przekazywanie parametrów do procedury w treści żądania HTTP POST z istniejącego zasobu procedury składowanej.

    var inputDocument = {id : "document1", author: "G. G. Marquez"};
    client.executeStoredProcedureAsync(createdStoredProcedure.resource._self, inputDocument)
        .then(function(executionResult) {
            assert.equal(executionResult, "success - created DOCUMENT1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-stored-procedure"></a>Wyrejestrowywanie procedury składowanej
Wyrejestrowywanie procedury składowanej po prostu odbywa się przez wystawienie HTTP DELETE przed istniejący zasób procedury składowanej.   

    client.deleteStoredProcedureAsync(createdStoredProcedure.resource._self)
        .then(function (response) {
            return;
        }, function(error) {
            console.log("Error");
        });


### <a name="registering-a-pre-trigger"></a>Rejestrowanie wyzwalacz wstępnego
Rejestracja wyzwalacz należy utworzyć nowy zasób wyzwalacza w kolekcji za pośrednictwem protokołu HTTP POST. Można określić wyzwalacza jest wstępnie lub wyzwalacza post i typ operacji może być skojarzony z (na przykład utwórz, Replace, Delete lub wszystkie).   

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

### <a name="executing-a-pre-trigger"></a>Wykonywania wyzwalacza wstępnego
Odbywa się wykonywania wyzwalacza, określając nazwę istniejącego wyzwalacza w momencie żądania POST/PUT/usuwania zasobu dokumentu za pomocą nagłówek żądania.  

    client.createDocumentAsync(collection._self, { id: "doc1", key: "Love in the Time of Cholera" }, { preTriggerInclude: "upperCaseId" })
        .then(function(createdDocument) {
            assert.equal(createdDocument.resource.id, "DOC1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-pre-trigger"></a>Wyrejestrowywanie wyzwalacz wstępnego
Wyrejestrowywanie wyzwalacz po prostu odbywa się za pośrednictwem wydania HTTP DELETE przed istniejący zasób wyzwalacza.  

    client.deleteTriggerAsync(createdPreTrigger._self);
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

### <a name="registering-a-udf"></a>Rejestrowanie funkcji zdefiniowanej przez użytkownika
Rejestracja funkcji zdefiniowanej przez użytkownika została wykonana przez utworzenie nowego zasobu funkcji zdefiniowanej przez użytkownika na kolekcję za pośrednictwem protokołu HTTP POST.  

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

### <a name="executing-a-udf-as-part-of-the-query"></a>Wykonywanie UDF w ramach zapytania
Funkcji zdefiniowanej przez użytkownika może być określona jako część zapytania SQL i jest używany jako sposób rozszerzać [język zapytań SQL](sql-api-sql-query-reference.md).

    var filterQuery = "SELECT udf.mathSqrt(r.Age) AS sqrtAge FROM root r WHERE r.FirstName='John'";
    client.queryDocuments(collection._self, filterQuery).toArrayAsync();
        .then(function(queryResponse) {
            var queryResponseDocuments = queryResponse.feed;
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-udf"></a>Wyrejestrowywanie funkcji zdefiniowanej przez użytkownika
Wyrejestrowywanie UDF po prostu odbywa się przez wystawienie HTTP DELETE przed istniejący zasób funkcji zdefiniowanej przez użytkownika.  

    client.deleteUserDefinedFunctionAsync(createdUdf._self)
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

Mimo że wstawki powyżej wykazało rejestracji (POST), Wyrejestrowanie (PUT), odczytu/listy (GET) i wykonywania (POST) za pośrednictwem [JavaScript SDK](https://github.com/Azure/azure-documentdb-js), można również użyć [interfejsów API REST](/rest/api/documentdb/) lub innych [zestawów SDK klienta](sql-api-sdk-dotnet.md). 

## <a name="documents"></a>Dokumenty
Można wstawić, Zastąp, usuwanie, odczytu, wyliczania i zapytania dowolnych dokumentów JSON w kolekcji. Azure DB rozwiązania Cosmos nie wprowadzić żadnego schematu i nie wymaga indeksów pomocniczych w celu zapewnienia obsługi zapytań za pośrednictwem dokumentów w kolekcji. Maksymalny rozmiar dokumentu jest 2 MB.   

Trwa usługi naprawdę otwartej bazy danych, bazy danych Azure rozwiązania Cosmos nie magazynowa żadnych specjalnych typów (na przykład data i godzina) lub określonego kodowania dla dokumentów JSON. Azure DB rozwiązania Cosmos nie wymaga żadnych specjalnych konwencje JSON do skodyfikować relacje między różnymi dokumentami; Składnia SQL Azure DB rozwiązania Cosmos zapewnia bardzo zaawansowane hierarchicznej i relacyjne kwerenda operatory zapytań i projektu dokumentów bez adnotacji specjalnych lub konieczność skodyfikować relacje dokumentów za pomocą różnych właściwości.  

Zgodnie z wszystkimi innymi zasobami dokumenty mogą być tworzone zastąpiony, usunięty, odczytu, wyliczyć i wyświetlić łatwo przy użyciu interfejsów API REST lub dowolnym [zestawów SDK klienta](sql-api-sdk-dotnet.md). Usuwanie dokumentu natychmiast zwolni przydziału odpowiadający wszystkich zagnieżdżonych załączników. Poziom spójności odczytu dokumentów następuje zasady spójności na konto bazy danych. Ta zasada może być zastąpiona na podstawie danego żądania, w zależności od wymagań spójności danych aplikacji. Podczas wykonywania zapytań dotyczących dokumentów, spójności odczytu następuje trybu indeksowania ustawić w kolekcji. Dla "spójne" wynika to zasady spójności dla konta. 

## <a name="attachments-and-media"></a>Załączników i nośników
Azure DB rozwiązania Cosmos służy do przechowywania obiektów blob binary/nośnika z bazy danych Azure rozwiązania Cosmos (maksymalnie 2 GB na konto) lub do magazynu zdalnego nośnika. Można też do reprezentowania metadanych nośnika pod względem specjalny dokument o nazwie załącznika. Załącznik w usłudze Azure DB rozwiązania Cosmos jest specjalny dokument (JSON), który odwołuje się do nośnika blob przechowywane w innym miejscu. Załącznik jest po prostu specjalny dokument, który przechwytuje metadane nośników przechowywanych w magazynie zdalnym nośnika (na przykład lokalizacji, autora itp.). 

Należy wziąć pod uwagę aplikacji odczytu społecznościowych, która używa bazy danych rozwiązania Cosmos Azure do przechowywania odręcznego i metadanych, w tym komentarzy, wyróżnia, zakładki, klasyfikacje, podobne/dislikes itp., skojarzony e-book danego użytkownika.   

* Książki sam jest przechowywana zawartość w magazynie nośnika albo dostępne jako część konta bazy danych DB rozwiązania Cosmos Azure lub magazynu zdalnego nośnika. 
* Aplikacja może przechowywać metadanych każdego użytkownika jako odrębne dokument — na przykład metadane Jana dla Skoroszyt1 są przechowywane w dokumencie odwołuje się /colls/joe/docs/book1. 
* Załączniki wskazuje zawartość, którą stron księgi danego użytkownika są przechowywane w dokumencie odpowiednie na przykład, /colls/joe/docs/book1/chapter1, /colls/joe/docs/book1/chapter2 itp. 

Powyższe przykłady Użyj przyjaznej nazwy do przekazania hierarchii zasobów. Zasoby są dostępne za pośrednictwem interfejsów API REST za pośrednictwem zasobów unikatowych identyfikatorów. 

Dla nośnika, który jest zarządzany przez bazy danych Azure rozwiązania Cosmos właściwość _media załącznika odwołuje się do nośnika przez jego identyfikator URI. Zapewnia Azure DB rozwiązania Cosmos pamięci zbieranie nośnik, po upuszczeniu wszystkich oczekujących odwołań. Azure DB rozwiązania Cosmos automatycznie generuje załącznika podczas przekazywania nowego nośnika i wypełnia _media wskaż nowo dodanego nośnika. Jeśli zdecydujesz się przechowywać nośnika w magazynie obiektów blob zdalnego zarządzany przez użytkownika (na przykład OneDrive, usługi Azure Storage itp DropBox), do odwołania nośnik można nadal używać załączników. W takim przypadku utworzysz załącznik samodzielnie i wypełnić jego właściwość _media.   

Zgodnie z wszystkimi innymi zasobami załączniki mogą być tworzone zastąpione, usunięty, odczytać lub wyliczyć za pomocą interfejsów API REST lub w dowolnej z zestawów SDK klienta. Podobnie jak w przypadku dokumentów, poziomu spójności odczytu załączników następuje zasad spójności na konto bazy danych. Ta zasada może być zastąpiona na podstawie danego żądania, w zależności od wymagań spójności danych aplikacji. Podczas wykonywania zapytania dla załączników, spójności odczytu następuje trybu indeksowania ustawić w kolekcji. Dla "spójne" wynika to zasady spójności dla konta. 
 

## <a name="users"></a>Użytkownicy
Użytkownik bazy danych Azure rozwiązania Cosmos reprezentuje logiczny obszar nazw, do grupowania uprawnień. Użytkownik bazy danych Azure rozwiązania Cosmos mogą odpowiadać użytkownika w systemie zarządzania tożsamości lub ról wstępnie zdefiniowanych aplikacji. Dla bazy danych rozwiązania Cosmos platformy Azure użytkownik po prostu reprezentuje abstrakcję do grupowania zestawu uprawnień w bazie danych.   

Do wykonywania wielu dzierżawców w aplikacji, można utworzyć użytkowników w usłudze Azure DB rozwiązania Cosmos odpowiadający rzeczywistych użytkowników lub dzierżawcami tej aplikacji. Następnie można utworzyć uprawnienia dla danego użytkownika, które odpowiadają kontroli dostępu w różnych kolekcjach dokumentów, załączniki, itp.   

Jak aplikacje, muszą skalować z rozwojem użytkownika, można przyjąć różne sposoby niezależnego fragmentu danych. Każdy z użytkowników może modelu w następujący sposób:   

* Każdy użytkownik mapuje do bazy danych.
* Każdy użytkownik mapuje do kolekcji. 
* Dokumenty odpowiadający wielu użytkowników przejdź do dedykowanych kolekcji. 
* Dokumenty odpowiadający wielu użytkowników przejdź do zestaw kolekcji.   

Niezależnie od strategii dzielenia na fragmenty określonych wybierzesz, można modelu rzeczywistych użytkowników jako użytkowników w usłudze Azure DB rozwiązania Cosmos w bazie danych i skojarzyć szczegółowe uprawnienia do poszczególnych użytkowników.  

![Kolekcje użytkowników][3]  
**Strategii dzielenia na fragmenty i modelowania użytkowników**

Podobnie jak wszystkie inne zasoby użytkowników w usłudze Azure DB rozwiązania Cosmos mogą być tworzone, zastąpione, usunięty, odczytać lub wyliczyć za pomocą interfejsów API REST lub w dowolnej z zestawów SDK klienta. Azure DB rozwiązania Cosmos zawsze zapewnia wysoki poziom spójności odczytu lub zapytanie dotyczące metadanych użytkownika zasobu. Warto wskazujące, to automatycznie usunięcie użytkownika zapewnia, że nie masz dostępu do tych uprawnień w nim zawarte. Mimo że Azure DB rozwiązania Cosmos zwraca przydziału uprawnień w ramach usuwanego użytkownika w tle, usunięto uprawnienia są dostępne natychmiast ponownie do użycia.  

## <a name="permissions"></a>Uprawnienia
Z punktu widzenia kontroli dostępu do zasobów, takich jak konta bazy danych, bazy danych użytkowników i uprawnienia są traktowane jako *administracyjne* zasobów, ponieważ wymagają one wykonywania uprawnień administracyjnych. Z drugiej strony, zasobów, w tym kolekcje, dokumenty, załączniki, procedury składowane, wyzwalacze, i funkcje UDF są zakresu w określonej bazie danych i uznane za *zasoby aplikacji*. Odpowiadającej dwa typy zasobów i role, które uzyskują dostęp do nich (to znaczy administratora i użytkownika), modelu autoryzacji definiuje dwa typy *klucze dostępu*: *klucza głównego* i  *Klucz zasobu*. Klucz główny jest część konta bazy danych i został dostarczony do deweloperów (lub administratora) który aprowizacji konta bazy danych. Ten klucz główny ma semantykę administrator może służyć do autoryzacji dostępu do zasobów z aplikacją i administracyjnych. Z kolei klucz zasobu jest kluczem szczegółowego dostępu, który zezwala na dostęp do *określonych* zasobów aplikacji. W związku z tym umożliwia przechwytywanie relacji między użytkownikiem bazy danych i uprawnienia użytkownika dla określonego zasobu (na przykład kolekcji, dokumentów, załącznik, procedury składowanej, wyzwalacza lub funkcji zdefiniowanej przez użytkownika).   

Jedynym sposobem na uzyskanie klucza zasobu jest tworzenie zasobu uprawnień danego użytkownika. Należy pamiętać, że aby było możliwe utworzenie ani pobrać uprawnień, klucz główny należy przedstawić w nagłówku autoryzacji. Zasób uprawnienia wiąże zasobu, dostęp do niej oraz użytkownika. Po utworzeniu zasobu uprawnienia, użytkownik musi tylko do prezentowania klucza zasobów w celu uzyskania dostępu do odpowiednich zasobów. W związku z tym klucz zasobu można wyświetlić jako logicznych i compact reprezentacji zasobu uprawnienia.  

Zgodnie z wszystkimi innymi zasobami, można utworzyć uprawnienia w usłudze Azure DB rozwiązania Cosmos, zastąpione, usunięty, odczytać lub wyliczyć za pomocą interfejsów API REST lub w dowolnej z zestawów SDK klienta. Azure DB rozwiązania Cosmos zawsze zapewnia wysoki poziom spójności odczytu lub zapytanie dotyczące metadanych uprawnienia. 

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o pracy z zasobami przy użyciu poleceń HTTP w [RESTful interakcji z zasobami Azure DB rozwiązania Cosmos](https://msdn.microsoft.com/library/azure/mt622086.aspx).

[1]: media/sql-api-resources/resources1.png
[2]: media/sql-api-resources/resources2.png
[3]: media/sql-api-resources/resources3.png

