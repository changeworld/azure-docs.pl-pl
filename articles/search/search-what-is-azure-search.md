---
title: Wprowadzenie do wyszukiwania poznawczego na platformie Azure
titleSuffix: Azure Cognitive Search
description: Usługa Azure Cognitive Search to w pełni zarządzana usługa wyszukiwania w chmurze hostowanego firmy Microsoft. Przeczytaj opisy funkcji, przepływ pracy deweloperów, porównania z innymi produktami wyszukiwania firmy Microsoft i jak rozpocząć pracę.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 01/06/2020
ms.openlocfilehash: fee7c8eb73fe0bb7c9fd0bd9de9aa57bd8c40215
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77580655"
---
# <a name="what-is-azure-cognitive-search"></a>Co to jest wyszukiwanie poznawcze na platformie Azure?

Usługa Azure Cognitive Search[(wcześniej znana jako "Usługa Azure Search"](whats-new.md#new-service-name)– to rozwiązanie w chmurze typu search-as-a-service, które udostępnia deweloperom interfejsy API i narzędzia do dodawania bogatego środowiska wyszukiwania za pośrednictwem prywatnej, niejednorodnej zawartości w aplikacjach sieci Web, urządzeniach mobilnych i przedsiębiorstwach. Kod lub narzędzie wywołuje pozyskiwania danych (indeksowanie) do tworzenia i ładowania indeksu. Opcjonalnie można dodać umiejętności poznawcze, aby zastosować procesy AI podczas indeksowania. W ten sposób można dodać nowe informacje i struktury przydatne do wyszukiwania i innych scenariuszy.

Po drugiej stronie usługi kod aplikacji wystawia żądania zapytań i obsługuje odpowiedzi. Środowisko wyszukiwania jest zdefiniowany w kliencie przy użyciu funkcji z usługi Azure Cognitive Search, z wykonywaniem zapytań za pośrednictwem utrwalonego indeksu, który tworzysz, jesteś właścicielem i przechowujesz w usłudze.

![Architektura usługi Azure Cognitive Search](media/search-what-is-azure-search/azure-search-diagram.svg "Architektura usługi Azure Cognitive Search")

Funkcje są uwidaczniane za pośrednictwem prostego [interfejsu API REST](/rest/api/searchservice/) lub [zestawu SDK platformy .NET](search-howto-dotnet-sdk.md), które pozwalają zamaskować złożoność związaną z używaniem pobierania informacji. Oprócz interfejsów API witryna Azure Portal udostępnia funkcje administrowania i zarządzania zawartością oraz narzędzia służące do tworzenia prototypów i wykonywania zapytań względem indeksów. Ponieważ usługa ta działa w chmurze, zarządzaniem infrastrukturą i dostępnością zajmuje się firma Microsoft.

## <a name="when-to-use-azure-cognitive-search"></a>Kiedy używać usługi Azure Cognitive Search

Usługa Azure Cognitive Search doskonale nadaje się do następujących scenariuszy aplikacji:

+ Konsolidacja heterogenicznych typów zawartości w prywatny, pojedynczy indeks z możliwością wyszukiwania. Zapytania są zawsze za pomocą indeksu, który można utworzyć i załadować z dokumentami, a indeks zawsze znajduje się w chmurze w usłudze Azure Cognitive Search. Indeks można wypełnić strumieniami dokumentów JSON z dowolnego źródła lub platformy. Alternatywnie dla zawartości pozyskiwanej na platformie Azure można użyć *indeksatora* do ściągania danych do indeksu. Definicja indeksu i zarządzanie/własność jest kluczowym powodem korzystania z usługi Azure Cognitive Search.

+ Zawartość nieprzetworzona to duży niezróżiły tekst, pliki obrazów lub pliki aplikacji, takie jak typy zawartości pakietu Office w źródle danych platformy Azure, takim jak magazyn obiektów Blob platformy Azure lub usługa Cosmos DB. Umiejętności poznawcze można zastosować podczas indeksowania, aby dodać strukturę lub wyodrębnić znaczenie z plików obrazów i aplikacji.

+ Łatwa implementacja funkcji związanych z wyszukiwaniem. Interfejsy API usługi Azure Cognitive Search upraszczają budowę zapytań, nawigację fasetacjową, filtry (w tym wyszukiwanie geoprzestrzenne), mapowanie synonimów, zapytania typu w nagłówku i dostrajanie trafności. Korzystając z wbudowanych funkcji, można spełnić oczekiwania użytkowników końcowych dotyczące wyszukiwania podobnego do komercyjnych wyszukiwarek internetowych.

+ Indeksowanie tekstu bez struktury lub wyodrębnianie tekstu i informacji z plików obrazów. Funkcja [wzbogacania sztucznej inteligencji](cognitive-search-concept-intro.md) usługi Azure Cognitive Search dodaje przetwarzanie sztucznej inteligencji do potoku indeksowania. Niektóre typowe przypadki użycia obejmują OCR nad zeskanowanym dokumentem, rozpoznawanie jednostek i wyodrębnianie fraz kluczowych w dużych dokumentach, wykrywanie języka i tłumaczenie tekstu oraz analizę tonacji.

+ Wymagania językowe spełnione przy użyciu analizatorów niestandardowych i językowych usługi Azure Cognitive Search. Jeśli masz zawartość nieanglojęzyczną, usługa Azure Cognitive Search obsługuje zarówno analizatory Lucene, jak i procesory języka naturalnego firmy Microsoft. Można również skonfigurować analizatory w celu uzyskania specjalistycznego przetwarzania zawartości pierwotnej, takiej jak filtrowanie znaków diakrutycznych.

<a name="feature-drilldown"></a>

## <a name="feature-descriptions"></a>Opisy funkcji

| Wyszukiwanie&nbsp;podstawowe&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Funkcje |
|-------------------|----------|
|Wyszukiwanie tekstowe w dowolnej postaci | [**Wyszukiwanie pełnotekstowe**](search-lucene-query-architecture.md) jest podstawowym przypadkiem użycia większości aplikacji opartych na wyszukiwaniu. Zapytania można formułować za pomocą obsługiwanej składni. <br/><br/>[**Prosta składnia zapytań**](query-simple-syntax.md) zawiera operatory logiczne, operatory wyszukiwania fraz, operatory sufiksów oraz operatory pierwszeństwa.<br/><br/>[**Składnia zapytań Lucene**](query-lucene-syntax.md) obejmuje wszystkie operacje prostej składni rozszerzone o wyszukiwanie rozmyte, wyszukiwanie w sąsiedztwie, promowanie terminów i wyrażenia regularne.|
| Trafność | [**Proste ocenianie**](index-add-scoring-profiles.md) jest kluczową zaletą usługi Azure Cognitive Search. Profile oceniania służą do modelowania trafności jako funkcji wartości w samych dokumentach. Na przykład nowsze produkty lub produkty o obniżonej cenie mogą być wyświetlane na początku wyników wyszukiwania. Do tworzenia profilów oceniania można również używać tagów spersonalizowanej oceny opartych na preferencjach klientów, śledzonych i przechowywanych oddzielnie. |
| Wyszukiwanie geograficzne | Usługa Azure Cognitive Search przetwarza, filtruje i wyświetla lokalizacje geograficzne. Pozwala ona użytkownikom eksplorować dane na podstawie zbliżenia wyniku wyszukiwania do lokalizacji fizycznej. Aby dowiedzieć się więcej, [obejrzyj ten film](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) lub [zapoznaj się z przykładem](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). |
| Filtry i aspekty | [**Nawigacja aspektowa**](search-faceted-navigation.md) jest włączana za pomocą jednego parametru zapytania. Usługa Azure Cognitive Search zwraca strukturę nawigacji aspektowej, której można użyć jako kodu za listą kategorii, do samodzielnego filtrowania (na przykład do filtrowania elementów katalogu według przedziału cenowego lub marki). <br/><br/> [**Filtry**](query-odata-filter-orderby-syntax.md) umożliwiają integrowanie nawigacji aspektowej z interfejsem użytkownika aplikacji, rozbudowywanie zapytań oraz filtrowanie na podstawie kryteriów określonych przez użytkownika lub dewelopera. Do tworzenie filtrów służy składnia OData. |
| Funkcje środowiska użytkownika | [**Autouzupełnianie**](search-autocomplete-tutorial.md) można włączyć dla zapytań typu z wyprzedzeniem na pasku wyszukiwania. <br/><br/>[**Sugestie dotyczące wyszukiwania**](https://docs.microsoft.com/rest/api/searchservice/suggesters) działają również na podstawie tekstu częściowego wpisanego w pasku wyszukiwania, ale wyniki są faktycznymi dokumentami w indeksie, a nie terminami zapytania. <br/><br/>[**Synonimy**](search-synonyms.md) umożliwiają kojarzenie równoważnych terminów, co niejawnie rozszerza zakres zapytania — bez potrzeby podawania terminów alternatywnych przez użytkownika. <br/><br/>[**Wyróżnianie trafień**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) umożliwia zastosowanie formatowania tekstu do pasującego słowa kluczowego w wynikach wyszukiwania. Można wybrać pola, które zwracają wyróżnione fragmenty.<br/><br/>[**Sortowanie**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) jest dostępne dla wielu pól za pośrednictwem schematu indeksu. Można je przełączać w czasie wykonywania zapytania za pomocą pojedynczego parametru wyszukiwania.<br/><br/> [**Stronicowanie**](search-pagination-page-layout.md) i ograniczanie wyników wyszukiwania jest proste dzięki precyzyjnie dostrojonej kontroli, jaką usługa Azure Cognitive Search oferuje w wynikach wyszukiwania.  <br/><br/>|

| Wzbogacanie&nbsp;SI&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;       | Funkcje |
|-------------------|----------|
|Przetwarzanie SI podczas indeksowania | [**Wzbogacanie si dla**](cognitive-search-concept-intro.md) analizy obrazu i tekstu można zastosować do potoku indeksowania w celu wyodrębnienia informacji tekstowych z zawartości nieprzetworzonej. Kilka przykładów [wbudowanych możliwości](cognitive-search-predefined-skills.md) to optyczne rozpoznawanie znaków (które pozwala na przeszukiwanie plików JPEG), rozpoznawanie jednostek (identyfikowanie organizacji, nazwy lub lokalizacji) oraz rozpoznawanie kluczowych fraz. W celu dołączenia do potoku można również użyć [możliwości kodu niestandardowego](cognitive-search-create-custom-skill-example.md). |
| Przechowywanie wzbogaconej zawartości do analizy i konsumpcji w scenariuszach innych niż wyszukiwanie | [**Magazyn wiedzy (wersja zapoznawcza)**](knowledge-store-concept-intro.md) jest rozszerzeniem indeksowania opartego na si. Z usługi Azure storage jako wewnętrznej bazy danych można zapisać wzbogacenia utworzone podczas indeksowania. Te artefakty mogą służyć do projektowania lepszych skillsets lub tworzenia kształtu i struktury z amorficznych lub niejednoznacznych danych. Można utworzyć projekcje tych struktur, które są przeznaczone dla określonych obciążeń lub użytkowników. Można również bezpośrednio analizować wyodrębnione dane lub załadować je do innych aplikacji.<br/><br/> |
| Zawartość buforowana | [**Przyrostowe wzbogacanie (wersja zapoznawcza)**](cognitive-search-incremental-indexing-conceptual.md) ogranicza przetwarzanie tylko do dokumentów, które są zmieniane przez określoną edycję do potoku, przy użyciu buforowanej zawartości dla części potoku, które nie zmieniają się. |

| Importowanie/indeksowanie danych&nbsp; | Funkcje |
|----------------------------------|----------|
| Źródła danych | Indeksy usługi Azure Cognitive Search akceptują dane z dowolnego źródła, pod warunkiem, że są przesyłane jako struktura danych JSON. <br/><br/> [**Indeksatory**](search-indexer-overview.md) automatyzują pozyskiwanie danych dla obsługiwanych źródeł danych platformy Azure i obsługują serializację JSON. Połącz się z [usługą Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Usługa Azure Cosmos DB](search-howto-index-cosmosdb.md)lub [magazynem obiektów blob platformy Azure,](search-howto-indexing-azure-blob-storage.md) aby wyodrębnić zawartość z przeszukiwalną w podstawowych magazynach danych. Indeksatory obiektów blob platformy Azure mogą przeprowadzać *„łamanie zabezpieczeń dokumentów”* w celu [wyodrębniania tekstu z głównych formatów plików](search-howto-indexing-azure-blob-storage.md), na przykład plików PDF i HTML oraz dokumentów pakietu Microsoft Office. |
| Hierarchiczne i zagnieżdżone struktury danych | [**Złożone typy**](search-howto-complex-data-types.md) i kolekcje umożliwiają modelowanie praktycznie dowolnego typu struktury JSON jako indeksu usługi Azure Cognitive Search. Kardynalność jeden do wielu i wiele do wielu może być wyrażona natywnie za pośrednictwem kolekcji, typów złożonych i kolekcji typów złożonych.|
| Analiza lingwistyczna | Analizatory to składniki używane do przetwarzania tekstu podczas operacji indeksowania i wyszukiwania. Są dwa typy analizatorów. <br/><br/>[**Niestandardowe analizatory leksykalne**](index-add-custom-analyzers.md) są używane w złożonych zapytaniach wyszukiwania korzystających z dopasowania fonetycznego i wyrażeń regularnych. <br/><br/>[**Analizatory języków**](index-add-language-analyzers.md), opracowane przez firmę Lucene lub Microsoft, są używane do inteligentnej obsługi struktur lingwistycznych, m.in. czasów gramatycznych, rodzajów i rzeczowników z nieregularną liczbą mnogą (na przykład „mouse” i „mice” w języku angielskim), a także rozkładania i dzielenia wyrazów (w przypadku języków, w których nie używa się odstępów). <br/><br/>|


| Poziom&nbsp;platformy&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| Funkcje |
|-------------------|----------|
| Narzędzia służące do tworzenia prototypów i przeprowadzania inspekcji | Portal udostępnia [**kreatora importowania danych**](search-import-data-portal.md) umożliwiającego konfigurowanie indeksatorów, projektanta indeksów służącego do wdrożenia indeksu oraz [**eksploratora wyszukiwania**](search-explorer.md), który pozwala testować zapytania i dostosowywać profile oceniania. Można również otworzyć dowolny indeks, aby wyświetlić jego schemat. |
| Monitorowanie i diagnostyka | [**Włącz funkcje monitorowania,**](search-monitor-usage.md) aby wyjść poza metryki w skrócie, które są zawsze widoczne w portalu. Metryki dotyczące liczby zapytań na sekundę, opóźnienia i ograniczania są przechwytywane i udostępniane na stronach portalu bez konieczności konfigurowania dodatkowych ustawień.|
| Szyfrowanie po stronie serwera | [**Zarządzane przez firmę Microsoft szyfrowanie w spoczynku**](search-security-overview.md#encrypted-transmission-and-storage) jest wbudowane w wewnętrzną warstwę pamięci masowej i jest nieodwołalne. Opcjonalnie można uzupełnić szyfrowanie domyślne [**kluczami szyfrowania zarządzanymi przez klienta**](search-security-manage-encryption-keys.md). Klucze, które można utworzyć i zarządzać w usłudze Azure Key Vault są używane do szyfrowania indeksów i map synonimów w usłudze Azure Cognitive Search. |
| Infrastruktura | **Platforma o wysokiej dostępności** zapewnia niezawodne działanie usługi wyszukiwania. Po prawidłowym skalowaniu [usługa Azure Cognitive Search oferuje 99,9% umowy SLA.](https://azure.microsoft.com/support/legal/sla/search/v1_0/)<br/><br/> **W pełni zarządzane i skalowalne** jako kompleksowe rozwiązanie usługa Azure Cognitive Search nie wymaga absolutnie żadnego zarządzania infrastrukturą. Skalowanie w dwóch wymiarach pozwala dostosować usługę do swoich potrzeb, na przykład zwiększyć miejsce do magazynowania dokumentów czy zapewnić obsługę większych obciążeń zapytaniami.<br/><br/>|

## <a name="how-to-use-azure-cognitive-search"></a>Jak korzystać z usługi Azure Cognitive Search
### <a name="step-1-provision-service"></a>Krok 1. Aprowizowanie usługi
Usługę Azure Cognitive Search można aprowizować w [witrynie Azure portal](https://portal.azure.com/) lub za pośrednictwem [interfejsu API zarządzania zasobami platformy Azure.](/rest/api/searchmanagement/) Możesz wybrać wersję bezpłatną, współużytkowaną z innymi subskrybentami, lub [warstwę płatną](https://azure.microsoft.com/pricing/details/search/), która pozwala rezerwować zasoby używane przez konkretną usługę. W warstwach płatnych dostępne są dwa wymiary skalowania usługi: 

- Dodawanie replik w celu zwiększenia pojemności umożliwiającej obsługę dużych obciążeń zapytaniami.   
- Dodawanie partycji w celu zwiększenia magazynu dla dokumentów. 

Dzięki oddzielnej obsłudze magazynu dokumentów i przepływności zapytań możesz kalibrować zasoby na podstawie wymagań środowiska produkcyjnego.

### <a name="step-2-create-index"></a>Krok 2. Tworzenie indeksu
Przed przekazaniem zawartości z godną wyszukiwania należy najpierw zdefiniować indeks usługi Azure Cognitive Search. Indeks przypomina tabelę bazy danych, która przechowuje dane i może akceptować zapytania wyszukiwania. Musisz zdefiniować schemat indeksu odzwierciedlający strukturę dokumentów, które chcesz przeszukiwać, podobnie jak w przypadku pól w bazie danych.

Schemat możesz utworzyć w witrynie Azure Portal lub programowo przy użyciu [zestawu SDK platformy .NET](search-howto-dotnet-sdk.md) lub [interfejsu API REST](/rest/api/searchservice/).

### <a name="step-3-load-data"></a>Krok 3. Ładowanie danych
Po zdefiniowaniu indeksu możesz zacząć przekazywać zawartość. Możesz użyć modelu wypychania lub modelu ściągania.

W modelu ściągania dane są pobierane z zewnętrznych źródeł danych. Jest on obsługiwany przez *indeksatory*, które usprawniają i automatyzują aspekty pozyskiwania danych, takie jak łączenie się z danymi oraz ich odczytywanie i serializowanie. [Indeksatory](/rest/api/searchservice/Indexer-operations) są dostępne dla usług Azure Cosmos DB, Azure SQL Database, Azure Blob Storage oraz programu SQL Server hostowanego na maszynie wirtualnej platformy Azure. Indeksator możesz skonfigurować na potrzeby zaplanowanego odświeżania danych lub odświeżania danych na żądanie.

Model wypychania jest oparty na zestawie SDK lub interfejsach API REST i umożliwia wysyłanie zaktualizowanych dokumentów do indeksu. Dane możesz wypychać praktycznie z każdego zestawu danych w formacie JSON. Aby uzyskać wskazówki dotyczące ładowania danych, zobacz [Dodawanie, aktualizowanie lub usuwanie dokumentów](/rest/api/searchservice/addupdate-or-delete-documents) lub [Jak używać zestawu SDK dla platformy .NET](search-howto-dotnet-sdk.md).

### <a name="step-4-search"></a>Krok 4. Wyszukiwanie
Po zapełnieniu indeksu można [wystawiać zapytania wyszukiwania](search-query-overview.md) do punktu końcowego usługi przy użyciu prostych żądań HTTP za pomocą [interfejsu API REST](/rest/api/searchservice/Search-Documents) lub [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations).

Krok po [utworzeniu pierwszej aplikacji wyszukiwania](tutorial-csharp-create-first-app.md) do utworzenia, a następnie rozszerzyć stronę sieci Web, która zbiera dane wejściowe użytkownika i obsługuje wyniki. Można również użyć [postman dla interaktywnych](search-get-started-postman.md) wywołań REST lub wbudowanego [Eksploratora wyszukiwania](search-explorer.md) w witrynie Azure portal do kwerendy istniejącego indeksu.

## <a name="how-it-compares"></a>Porównanie z innymi rozwiązaniami

Klienci często pytają, jak usługa Azure Cognitive Search porównuje się z innymi rozwiązaniami związanymi z wyszukiwaniem. Poniższa tabela zawiera podsumowanie podstawowych różnic.

| W porównaniu do | Podstawowe różnice |
|-------------|-----------------|
|Bing | [Interfejs API wyszukiwania w sieci Web Bing](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/) wyszukuje przesłane pasujące terminy w indeksach witryny Bing.com. Indeksy są tworzone na podstawie plików HTML i XML oraz innej zawartości internetowej pochodzącej z publicznych witryn. Na takiej samej zasadzie [Wyszukiwanie niestandardowe Bing](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/) udostępnia tę samą przeszukiwarkę dla typów zawartości internetowej, ograniczoną do poszczególnych witryn.<br/><br/>Usługa Azure Cognitive Search przeszukuje zdefiniowany przez firmę indeks, wypełniony danymi i dokumentami, które posiadasz, często z różnych źródeł. Usługa Azure Cognitive Search ma możliwości robota dla niektórych źródeł danych za pośrednictwem [indeksatorów,](search-indexer-overview.md)ale można wypchnąć dowolny dokument JSON, który jest zgodny ze schematem indeksu do jednego, skonsolidowanego zasobu z możliwością wyszukiwania. |
|Wyszukiwanie w bazie danych | Wiele platform baz danych udostępnia wbudowane środowisko wyszukiwania. Platforma SQL Server oferuje [wyszukiwanie pełnotekstowe](https://docs.microsoft.com/sql/relational-databases/search/full-text-search). Usługa Cosmos DB i podobne technologie mają indeksy odpytywalne. W przypadku oceny produktów, które łączą funkcje wyszukiwania i magazynu, może być trudno wybrać najlepsze rozwiązanie. Wiele rozwiązań używa zarówno: dbms dla magazynu i usługi Azure Cognitive Search dla wyspecjalizowanych funkcji wyszukiwania.<br/><br/>W porównaniu z wyszukiwaniem dbms, usługa Azure Cognitive Search przechowuje zawartość z heterogenicznych źródeł i oferuje wyspecjalizowane funkcje przetwarzania tekstu, takie jak przetwarzanie tekstu z uwzględnieniem języka (wynikające, lemmatyzacja, formy słów) w [56 językach.](https://docs.microsoft.com/rest/api/searchservice/language-support) Obsługuje ona również autokorektę błędnie napisanych wyrazów, [synonimy](https://docs.microsoft.com/rest/api/searchservice/synonym-map-operations), [sugestie](https://docs.microsoft.com/rest/api/searchservice/suggestions), [formanty oceniania](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [aspekty](https://docs.microsoft.com/azure/search/search-filters-facets)i [tokenizację niestandardową](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search). [Pełnotekstowa wyszukiwarka](search-lucene-query-architecture.md) w usłudze Azure Cognitive Search jest zbudowana na Apache Lucene, branżowym standardzie w pobieraniu informacji. Usługa Azure Cognitive Search utrzymuje dane w postaci odwróconego indeksu, ale rzadko zastępuje prawdziwy magazyn danych. Więcej informacji znajduje się we [wpisie na forum](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data). <br/><br/>Użycie zasobów to kolejny etap w tej kategorii. Indeksowanie i niektóre operacje zapytań są często intensywne z obliczeniowego punktu widzenia. Przeniesienie wyszukiwania z DBMS do dedykowanego rozwiązania w chmurze pozwala zachować zasoby systemowe dla przetwarzania transakcyjnego. Dzięki temu można łatwo dostosować skalę do liczby zapytań.|
|Dedykowane rozwiązanie wyszukiwania | Jeśli przyjmiemy, że decydujesz się na dedykowane wyszukiwanie z pełnego zakresu funkcjonalności, ostateczne porównanie w oparciu o kategorie odbywa się miedzy rozwiązaniami lokalnymi a usługą w chmurze. Wiele technologii wyszukiwania umożliwia sterowanie indeksowaniem i potokami zapytań oraz dostęp do zaawansowanej składni zapytań i filtrów. Pozwalają one sterować rangą i trafnością, a także udostępniają funkcje samodzielnego i inteligentnego wyszukiwania. <br/><br/>Usługę w chmurze warto wybrać, jeśli potrzebne jest gotowe rozwiązanie z regulowaną skalą, które prawie nie wymaga konserwacji i nakładu pracy. <br/><br/>W ramach modelu chmury kilku dostawców oferuje porównywalne funkcje bazowe, obejmujące wyszukiwanie pełnotekstowe, wyszukiwanie geograficzne oraz obsługę określonego poziomu niejednoznaczności w danych wejściowych wyszukiwania. Zwykle korzysta się z [wyspecjalizowanej funkcji](#feature-drilldown) lub wybiera się łatwość użytkowania i ogólną prostotę interfejsów API, narzędzi i funkcji zarządzania. |

Wśród dostawców chmury usługa Azure Cognitive Search jest najsilniejsza w przypadku obciążeń wyszukiwania pełnotekstowego w magazynach zawartości i bazach danych na platformie Azure dla aplikacji, które opierają się głównie na wyszukiwaniu zarówno pobierania informacji, jak i nawigacji po zawartości. 

Oto najważniejsze zalety:

+ Integracja danych platformy Azure (przeszukiwarki) w warstwie indeksowania
+ Witryna Azure Portal umożliwiająca centralne zarządzanie
+ Skalowalność i niezawodność platformy Azure oraz dostępność światowej klasy
+ Przetwarzanie nieprzetworzonych danych przez szesnasta, aby uczynić go bardziej przeszukiwalnym, w tym tekstem z obrazów lub znajdowaniem wzorców w zawartości nieustrukturyzowanego.
+ Analiza językowa i niestandardowa z analizatorami umożliwiającymi spójne wyszukiwanie pełnotekstowe w 56 językach
+ [Podstawowe funkcje, często używane w aplikacjach służących do wyszukiwania](#feature-drilldown): ocenianie, kategoryzowanie, sugestie, synonimy, wyszukiwanie geograficzne i inne.

> [!Note]
> Źródła danych spoza platformy Azure są w pełni obsługiwane, ale nie korzystają z indeksatorów, tylko metodologii wypychania wymagającej użycia większej ilości kodu. Za pomocą interfejsów API, można potok dowolnej kolekcji dokumentów JSON do indeksu usługi Azure Cognitive Search.

Wśród naszych klientów, którzy mogą korzystać z najszerszego zakresu funkcji w usłudze Azure Cognitive Search obejmują katalogi online, programy biznesowe i aplikacje do odnajdowania dokumentów.

## <a name="rest-api--net-sdk"></a>Interfejs API REST | Zestaw .NET SDK

Podczas gdy wiele zadań można wykonać w portalu, usługa Azure Cognitive Search jest przeznaczona dla deweloperów, którzy chcą zintegrować funkcje wyszukiwania z istniejącymi aplikacjami. Dostępne są następujące interfejsy programowania.

|Platforma |Opis |
|-----|------------|
|[Reszta](/rest/api/searchservice/) | Polecenia HTTP obsługiwane przez dowolną platformę i język programowania, w tym Xamarin, Java i JavaScript|
|[Zestaw SDK platformy .NET](search-howto-dotnet-sdk.md) | Otoka .NET dla interfejsu API REST umożliwia efektywne kodowanie w języku C# oraz w innych językach z kodem zarządzanym, przeznaczonych dla platformy .NET |

## <a name="free-trial"></a>Bezpłatna wersja próbna
Subskrybenci platformy Azure mogą [aprowizować usługę w warstwie Bezpłatna](search-create-service-portal.md).

Jeśli nie masz subskrypcji, możesz [otworzyć bezpłatne konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). Otrzymasz środki umożliwiające wypróbowanie płatnych usług platformy Azure. Nawet po ich wyczerpaniu możesz zachować konto i korzystać z [bezpłatnych usług platformy Azure](https://azure.microsoft.com/free/). Karta kredytowa nie zostanie obciążona, chyba że jawnie zmienisz ustawienia i poprosisz o jej obciążenie.

Możesz też [aktywować korzyści subskrybenta MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) — w ramach subskrypcji MSDN co miesiąc otrzymasz kredyt, który można przeznaczyć na płatne usługi platformy Azure. 

## <a name="how-to-get-started"></a>Jak zacząć

1. Utwórz [bezpłatną usługę](search-create-service-portal.md). Wszystkie samouczki i przewodniki Szybki Start można ukończyć, korzystając z bezpłatnej usługi.

2. Wykonaj kroki opisane w [samouczku dotyczącym korzystania z wbudowanych narzędzi do indeksowania i tworzenia zapytań](search-get-started-portal.md). Pozwoli ci to zapoznać się z ważnymi pojęciami oraz informacjami udostępnianymi w portalu.

3. Rozpocznij pracę z kodem, korzystając z platformy .NET lub interfejsu API REST:

   + [Jak używać zestawu SDK dla platformy .NET](search-howto-dotnet-sdk.md) — przedstawia podstawowy przepływ pracy w kodzie zarządzanym.  
   + [Wprowadzenie do interfejsu API REST](https://github.com/Azure-Samples/search-rest-api-getting-started) — zawiera te same kroki, wykonywane za pomocą interfejsu API REST. Za pomocą tego przewodnika Szybki start można również wywołać interfejsy API REST z postmana lub skrzypka: [eksploruj interfejsy API rest usługi Azure Cognitive Search.](search-get-started-postman.md)

## <a name="watch-this-video"></a>Obejrzyj ten film wideo

Wyszukiwarki to podstawowe instrumenty do pobierania informacji w internecie, aplikacjach mobilnych i firmowych magazynach danych. Usługa Azure Cognitive Search udostępnia narzędzia do tworzenia środowiska wyszukiwania podobnego do tych w dużych komercyjnych witrynach sieci Web.

W tym 9-minutowym filmie wideo menedżer programu Liam Cavanagh przedstawia korzyści związane z integracją wyszukiwarki z aplikacją. Krótkie wersje demonstracyjne obejmują kluczowe funkcje usługi Azure Cognitive Search i wygląd typowego przepływu pracy. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0–3 minuta — omówienie kluczowych funkcji i przypadków użycia.
+ 3–4 minuta — omówienie aprowizowania usługi. 
+ 4–6 minuta — omówienie kreatora importowania danych używanego do utworzenia indeksu na podstawie wbudowanego zestawu danych dotyczących nieruchomości.
+ 6–9 minuta — omówienie eksploratora wyszukiwania i różnych zapytań.
