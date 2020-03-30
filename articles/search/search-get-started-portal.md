---
title: Tworzenie indeksu wyszukiwania w witrynie Azure portal
titleSuffix: Azure Cognitive Search
description: W tym przewodniku Szybki start portalu Azure użyj Kreatora importu danych, aby utworzyć, załadować i zbadać swój pierwszy indeks wyszukiwania w usłudze Azure Cognitive Search.
author: tchristiani
manager: nitinme
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/10/2020
ms.openlocfilehash: 8324ca0184c508591fa4568175bad0f606f952a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369450"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-the-azure-portal"></a>Szybki start: tworzenie indeksu usługi Azure Cognitive Search w witrynie Azure portal
> [!div class="op_single_selector"]
> * [Portal](search-get-started-portal.md)
> * [C #](search-get-started-dotnet.md)
> * [Java](search-get-started-java.md)
> * [Node.js](search-get-started-nodejs.md)
> * [Powershell](search-get-started-powershell.md)
> * [Postman](search-get-started-postman.md)
> * [Python](search-get-started-python.md)

Użyj kreatora **importu danych** portalu i narzędzi **Eksploratora wyszukiwania,** aby szybko rozwinąć pojęcia i napisać interesujące zapytania względem indeksu w ciągu kilku minut.

Jeśli narzędzia są zbyt ograniczające, można rozważyć [wprowadzenie oparte na kodzie do programowania usługi Azure Cognitive Search w .NET](search-howto-dotnet-sdk.md) lub użyć [listonosza do wykonywania wywołań interfejsu API REST.](search-get-started-postman.md) 

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem. 

## <a name="prerequisites"></a>Wymagania wstępne

[Utwórz usługę Azure Cognitive Search](search-create-service-portal.md) lub znajdź [istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. Możesz skorzystać z bezpłatnej usługi dla tego szybkiego startu. 

### <a name="check-for-space"></a>Sprawdzanie ilości wolnego miejsca

Wielu klientów zaczyna od bezpłatnej usługi. Ta wersja jest ograniczona do trzech indeksów, trzech źródeł danych i trzech indeksatorów. Przed rozpoczęciem upewnij się, że dysponujesz miejscem na dodatkowe elementy. W ramach tego samouczka tworzony jest jeden obiekt każdego typu.

W sekcjach na pulpicie nawigacyjnym usługi wyświetlana jest liczba posiadanych już indeksów, indeksatorów i źródeł danych. 

![Listy indeksów, indeksatorów i źródeł danych](media/search-get-started-portal/tiles-indexers-datasources.png)

## <a name="create-an-index-and-load-data"></a><a name="create-index"></a> Tworzenie indeksu i ładowanie danych

Zapytania wyszukiwania używają iteracyjnie [*indeksu*](search-what-is-an-index.md) zawierającego dane z możliwością wyszukiwania, metadane i dodatkowe konstrukcje, które optymalizują określone zachowania związane z wyszukiwaniem.

W tym samouczku używamy wbudowanego przykładowego zestawu danych, który można przeszukiwać za pomocą [*indeksatora*](search-indexer-overview.md) za pomocą [ **kreatora importu danych** ](search-import-data-portal.md). Indeksator to przeszukiwarka specyficzna dla źródła, mająca możliwość odczytu metadanych i zawartości z obsługiwanych źródeł danych platformy Azure. Indeksatory są zazwyczaj używane programowo, ale w portalu można uzyskać do nich dostęp za pośrednictwem kreatora **Importowanie danych**. 

### <a name="step-1---start-the-import-data-wizard-and-create-a-data-source"></a>Krok 1 — Uruchomienie Kreatora importowania danych i utworzenie źródła danych

1. Na pulpicie nawigacyjnym usługi Azure Cognitive Search kliknij pozycję **Importuj dane** na pasku poleceń, aby utworzyć i wypełnić indeks wyszukiwania.

   ![Polecenie importu danych](media/search-get-started-portal/import-data-cmd.png)

2. W kreatorze kliknij pozycję **Połącz z danymi** > **Przykłady** > **hoteli.** To źródło danych jest wbudowane. Podczas tworzenia własnego źródła danych należy określić nazwę, typ i informacje o połączeniu. Po utworzeniu staje się ono „istniejącym źródłem danych”, które może zostać ponownie użyte w innych operacjach importu.

   ![Wybieranie przykładowego zestawu danych](media/search-get-started-portal/import-datasource-sample.png)

3. Przejdź do następnej strony.

### <a name="step-2---skip-the-enrich-content-page"></a>Krok 2 - Pomiń stronę "Wzbogać zawartość"

Kreator obsługuje tworzenie [potoku wzbogacania sztucznej inteligencji](cognitive-search-concept-intro.md) w celu włączenia algorytmów sztucznej inteligencji usług Cognitive Services do indeksowania. 

Na razie pominiemy ten krok i przejdziemy bezpośrednio do kroku **Dostosowywanie indeksu docelowego**.

   ![Pomijanie kroku Umiejętności poznawcze](media/search-get-started-portal/skip-cog-skill-step.png)

> [!TIP]
> Możesz przejść przez przykład indeksowania AI w przewodniku [Szybki start](cognitive-search-quickstart-blob.md) lub [samouczku](cognitive-search-tutorial-blob.md).

### <a name="step-3---configure-index"></a>Krok 3 — Konfigurowanie indeksu

Zazwyczaj tworzenie indeksu jest czynnością bazującą na kodzie, którą wykonuje się przed załadowaniem danych. Jednak, jak pokazano w tym samouczku, kreator może wygenerować indeks podstawowy dla dowolnego źródła danych, które może przeszukiwać. Indeks wymaga co najmniej nazwy i kolekcji pól. Jedno z pól powinno być oznaczone jako klucz dokumentu w celu jednoznacznego zidentyfikowania poszczególnych dokumentów. Ponadto można określić analizatory języka lub sugestory, aby stosować autouzupełnianie lub sugerowane zapytania.

Pola mają typy danych i atrybuty. Pola wyboru w górnej części są *atrybutami indeksu*, które kontrolują sposób używania pola.

* **Pobieranie** oznacza, że pole jest wyświetlane na liście wyników wyszukiwania. Poszczególne pola można oznaczyć jako limity wyłączone dla wyników wyszukiwania, czyszcząc to pole wyboru, na przykład dla pól używanych tylko w wyrażeniach filtru.
* **Klucz** to unikatowy identyfikator dokumentu. Zawsze jest ciągiem znaków i jest wymagany.
* Atrybuty **Możliwość filtrowania**, **Z możliwością sortowania** i **Możliwość tworzenia aspektów** określają, czy pola mogą być używane w strukturze nawigacji filtrowania, sortowania lub tworzenia aspektów.
* **Wyszukiwanie** oznacza, że pole jest uwzględniane podczas wyszukiwania pełnotekstowego. Ciągi można przeszukiwać. Pól liczbowych i logicznych często nie można wyszukiwać.

Wymagania dotyczące rozmiaru magazynu nie są zależne od Twojego wyboru. Na przykład jeśli ustawisz atrybut **Możliwość pobierania** dla wielu pól, nie zwiększy to wymaganego rozmiaru magazynu.

Domyślnie kreator skanuje źródło danych w poszukiwaniu unikatowych identyfikatorów jako podstawy dla pola klucza. *Ciągi* są przypisywane jako **możliwe do pobrania** i **przeszukiwania**. *Liczby całkowite* są przypisywane jako **możliwe do pobrania**, **filtrowalne**, **sortowalne**i **facetowalne**.

1. Zaakceptuj wartości domyślne. 

   Jeśli kreator zostanie ponownie uruchom po raz drugi przy użyciu istniejącego źródła danych hoteli, indeks nie zostanie skonfigurowany z atrybutami domyślnymi. Konieczne będzie ręczne wybranie atrybutów podczas przyszłych importów. 

   ![Indeks wygenerowanych hoteli](media/search-get-started-portal/hotelsindex.png)

2. Przejdź do następnej strony.


### <a name="step-4---configure-indexer"></a>Krok 4 — Konfigurowanie indeksatora

W kreatorze **importu danych** kliknij pozycję **Indeksator** > **Nazwa**, a następnie wpisz nazwę indeksatora.

Ten obiekt definiuje proces wykonywalny. Możesz go uruchamiać w ramach harmonogramu cyklicznego, ale na razie użyj opcji domyślnej, aby od razu uruchomić indeksator jeden raz.

Kliknij przycisk **Prześlij**, aby utworzyć i od razu uruchomić indeksator.

  ![indeksator hoteli](media/search-get-started-portal/hotels-indexer.png)

## <a name="monitor-progress"></a>Monitorowanie postępu

Kreator powinien przenieść Cię do listy Indeksatory, gdzie można monitorować postęp. W przypadku samodzielnej nawigacji przejdź do strony Przegląd, a następnie kliknij przycisk **Indeksatory**.

Aktualizacja strony w portalu może wymagać kilku minut, ale na liście powinien być widoczny nowo utworzony indeksator w stanie wskazującym na trwające indeksowanie lub jego pomyślne zakończenie wraz z liczbą zindeksowanych dokumentów.

   ![Komunikat o postępie indeksatora](media/search-get-started-portal/indexers-inprogress.png)

## <a name="view-the-index"></a>Wyświetlanie indeksu

Strona głównej usługi zawiera łącza do zasobów utworzonych w usłudze Azure Cognitive Search.  Aby wyświetlić właśnie utworzony indeks, kliknij pozycję **Indeksy** na liście linków. 

Poczekaj na odświeżenie strony portalu. Po kilku minutach powinien zostać wyświetlony indeks z liczbą dokumentów i rozmiarem magazynu.

   ![Lista Indeksy na pulpicie nawigacyjnym usługi](media/search-get-started-portal/indexes-list.png)

Z tej listy możesz kliknąć indeks *przykładowy hoteli,* który właśnie utworzyłeś, wyświetlić schemat indeksu. i opcjonalnie dodać nowe pola. 

Karta **Pola** zawiera schemat indeksu. Przewiń listę w dół, aby wprowadzić nowe pole. W większości przypadków nie można zmienić istniejących pól. Istniejące pola mają fizyczną reprezentację w usłudze Azure Cognitive Search i dlatego nie można ich modyfikować, nawet w kodzie. Aby całkowicie zmienić istniejące pole, utwórz nowy indeks, usuwając poprzednią wersję.

   ![Przykładowa definicja indeksu](media/search-get-started-portal/sample-index-def.png)

Inne konstrukcje, takie jak profile oceniania i opcje CORS, można dodać w dowolnym momencie.

Aby dobrze zaznajomić się z elementami, które można i których nie można edytować podczas projektowania indeksu, poświęć chwilę na zapoznanie się z opcjami definicji indeksu. Opcje wyświetlane na szaro oznaczają, że danej wartości nie można zmodyfikować ani usunąć. 

## <a name="query-using-search-explorer"></a><a name="query-index"></a> Tworzenie zapytań za pomocą Eksploratora wyszukiwania

Indeks wyszukiwania powinien być teraz gotowy do wykonywania względem niego zapytań za pomocą wbudowanej strony zapytań, noszącej nazwę [**Eksplorator wyszukiwania**](search-explorer.md). Zawiera ona pole wyszukiwania, umożliwiające testowanie dowolnych ciągów zapytań.

**Eksplorator wyszukiwania** jest przystosowany tylko do obsługi [żądań interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents), ale akceptuje zarówno [prostą składnię zapytań](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), jak i składnię [pełnego analizatora zapytań Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), a także wszystkie parametry wyszukiwania dostępne w ramach operacji [interfejsu API REST wyszukiwania dokumentów](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples).

> [!TIP]
> Poniższe kroki są przedstawione w 6m08s w [wideo przegląd usługi Azure Cognitive Search.](https://channel9.msdn.com/Events/Connect/2016/138)
>

1. Kliknij pozycję **Eksplorator wyszukiwania** na pasku poleceń.

   ![Polecenie Eksploratora wyszukiwania](media/search-get-started-portal/search-explorer-cmd.png)

2. Z listy rozwijanej **Indeks** wybierz *indeks-przykład hoteli*. Kliknij menu rozwijane **Wersja interfejsu API,** aby zobaczyć, które interfejsy API REST są dostępne. W przypadku poniższych zapytań użyj ogólnie dostępnej wersji (2019-05-06).

   ![Polecenia indeksu i interfejsu API](media/search-get-started-portal/search-explorer-changeindex.png)

3. Na pasku wyszukiwania wklej poniższe ciągi zapytań, a następnie kliknij pozycję **Wyszukaj**.

   ![Ciąg zapytania i przycisk Wyszukaj](media/search-get-started-portal/search-explorer-query-string-example.png)

## <a name="example-queries"></a>Przykładowe zapytania

Można wprowadzać terminy i frazy (podobnie jak w wyszukiwaniu Bing lub Google) lub w pełni określone wyrażenia zapytania. Wyniki są zwracane w postaci pełnych dokumentów JSON.

### <a name="simple-query-with-top-n-results"></a>Proste zapytanie z pierwszymi N wynikami

#### <a name="example-string-query-searchspa"></a>Przykład (zapytanie w postaci ciągu znaków): `search=spa`

* Parametr **wyszukiwania** służy do wprowadzania wyszukiwania słów kluczowych dla wyszukiwania pełnotekstowego, w tym przypadku zwracania danych hotelowych dla tych, które zawierają *spa* w dowolnym polu z wyszukujalnym w dokumencie.

* **Eksplorator wyszukiwania** zwraca wyniki w formacie JSON, które są szczegółowe i trudne do odczytania, jeśli dokumenty mają gęstą strukturę. Jest to zamierzone. Widoczność całego dokumentu jest ważna dla celów programowania, zwłaszcza podczas testów. Aby zwiększyć wygodę użytkowania, należy napisać kod [obsługujący wyniki wyszukiwania](search-pagination-page-layout.md) w celu wyróżnienia istotnych elementów.

* Dokumenty składają się ze wszystkich pól oznaczonych jako możliwe do pobierania w indeksie. Aby wyświetlić atrybuty indeksu w portalu, kliknij *próbkę hoteli* na liście **Indeksy.**

#### <a name="example-parameterized-query-searchspacounttruetop10"></a>Przykład (zapytanie sparametryzowane): `search=spa&$count=true&$top=10`

* Symbol **&** służy do dołączania parametrów wyszukiwania, które można określić w dowolnej kolejności.

* Parametr **$count=true** zwraca całkowitą liczbę wszystkich zwróconych dokumentów. Ta wartość jest wyświetlana na początku listy wyników wyszukiwania. Możesz zweryfikować zapytania filtru, monitorując zmiany raportowane przez parametr **$count=true**. Zmniejszająca się liczba wskazuje, że filtr działa.

* **$top=10** zwraca najwyższą rangę 10 dokumentów z sumy. Domyślnie usługa Azure Cognitive Search zwraca pierwsze 50 najlepszych dopasowań. Tę liczbę możesz zwiększyć lub zmniejszyć za pomocą parametru **$top**.

### <a name="filter-the-query"></a><a name="filter-query"></a> Filtrowanie zapytania

Żądania wyszukiwania zawierają filtry, jeśli zostanie dodany parametr **$filter**. 

#### <a name="example-filtered-searchbeachfilterrating-gt-4"></a>Przykład (filtrowane): `search=beach&$filter=Rating gt 4`

* Parametr **$filter** zwraca wyniki spełniające podane kryteria. W tym przypadku oceny większe niż 4.

* Składnia filtru to konstrukcja OData. Aby uzyskać więcej informacji, zobacz [Filter OData syntax](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) (Składnia filtrowania OData).

### <a name="facet-the-query"></a><a name="facet-query"></a> Tworzenie aspektu zapytania

Filtry aspektów są uwzględniane w żądaniach wyszukiwania. Korzystając z parametru facet, można zwrócić zagregowaną liczbę dokumentów, które są zgodne z podaną wartością aspektu.

#### <a name="example-faceted-with-scope-reduction-searchfacetcategorytop2"></a>Przykład (aspektowe ze zmniejszeniem zakresu): `search=*&facet=Category&$top=2`

* Parametr **search=*** to puste wyszukiwanie. Puste wyszukiwania umożliwiają znalezienie wszystkiego. Jednym z powodów przesłania pustego zapytania może być potrzeba przeprowadzenia filtrowania lub utworzenia aspektów pełnego zestawu dokumentów. Na przykład chcesz strukturę nawigacji aspektowe składa się ze wszystkich hoteli w indeksie.
* Parametr **facet** zwraca strukturę nawigacji, którą można przekazać do kontrolki interfejsu użytkownika. Zwraca ona kategorie i liczbę elementów. W takim przypadku kategorie są oparte na polu dogodnie nazywanym *kategoriami*. Nie ma agregacji w usłudze Azure Cognitive Search, `facet`ale można przybliżyć agregację za pośrednictwem , co daje liczbę dokumentów w każdej kategorii.

* Parametr **$top=2** powoduje zwrócenie dwóch dokumentów, co pokazuje, że za pomocą parametru `top` możesz zmniejszyć lub zwiększyć liczbę wyników.

#### <a name="example-facet-on-numeric-values-searchspafacetrating"></a>Przykład (aspektowe z wartościami liczbowymi): `search=spa&facet=Rating`

* Ta kwerenda jest aspektem oceny, w wyszukiwaniu tekstu *dla spa*. Termin *Ocena* można określić jako aspekt, ponieważ pole jest oznaczone jako możliwe do pobrania, filtrowalne i typowalne w indeksie, a wartości, które zawiera (numeryczne, od 1 do 5), nadają się do kategoryzowania aukcji na grupy.

* Aspekty mogą być tworzone tylko na podstawie pól z możliwością filtrowania. W wynikach mogą być zwracane tylko pola z możliwością pobierania.

* Pole *Ocena* jest zmiennoprzecinkowym podwójną precyzją, a grupowanie będzie według dokładnej wartości. Aby uzyskać więcej informacji na temat grupowania według interwału (na przykład "3 gwiazdki", "4 gwiazdki" itp.), zobacz [Jak zaimplementować nawigację fasetową w usłudze Azure Cognitive Search.](https://docs.microsoft.com/azure/search/search-faceted-navigation#filter-based-on-a-range)


### <a name="highlight-search-results"></a><a name="highlight-query"></a> Wyróżnianie wyników wyszukiwania

Wyróżnianie trafień oznacza dodawanie formatowania do tekstu zgodnego ze słowem kluczowym w przypadku znalezienia dopasowań w określonym polu. Jeśli termin wyszukiwania jest głęboko ukryty w opisie, możliwe jest dodanie wyróżnienia trafień w celu jego łatwiejszego zauważenia.

#### <a name="example-highlighter-searchbeachhighlightdescription"></a>Przykład (wyróżnienie): `search=beach&highlight=Description`

* W tym przykładzie sformatowana *plaża* słowa jest łatwiejsza do wykrycia w polu opisu.

#### <a name="example-linguistic-analysis-searchbeacheshighlightdescription"></a>Przykład (analiza lingwistyczna): `search=beaches&highlight=Description`

* Wyszukiwanie pełnotekstowe rozpoznaje podstawowe różnice w formularzach słownych. W tym przypadku wyniki wyszukiwania zawierają wyróżniony tekst dla "plaży", dla hoteli, które mają to słowo w polach zdatnych do wyszukiwania, w odpowiedzi na wyszukiwanie słów kluczowych na "plażach". Różne rodzaje tego samego wyrazu mogą być wyświetlane w wynikach z powodu przeprowadzenia analizy lingwistycznej. 

* Usługa Azure Cognitive Search obsługuje 56 analizatorów zarówno lucene i firmy Microsoft. Domyślnie używany przez usługę Azure Cognitive Search jest standardowy analizator Lucene.

### <a name="try-fuzzy-search"></a><a name="fuzzy-search"></a> Testowanie wyszukiwania rozmytego

Domyślnie błędnie napisane terminy zapytania, takie jak *seatle* dla "Seattle", nie zwracają dopasowań w typowym wyszukiwaniu. Poniższy przykład nie zwraca żadnych wyników.

#### <a name="example-misspelled-term-unhandled-searchseatle"></a>Przykład (błąd pisowni, nieobsługiwany): `search=seatle`

Aby obsłużyć błędy pisowni, można użyć wyszukiwania rozmytego. Wyszukiwanie rozmyte jest używane w przypadku korzystania z pełnej składni zapytań Lucene, co wymaga wykonania dwóch czynności: ustawienia parametru **queryType=full** w zapytaniu i dodania znaku **~** do wyszukiwanego ciągu.

#### <a name="example-misspelled-term-handled-searchseatlequerytypefull"></a>Przykład (błąd pisowni, obsługiwany): `search=seatle~&queryType=full`

W tym przykładzie zwraca teraz dokumenty, które zawierają dopasowania w "Seattle".

Gdy parametr **queryType** jest nieokreślony, używany jest domyślny prosty analizator zapytań. Prosty analizator zapytań jest szybszy, ale jeśli wymagane jest użycie wyszukiwania rozmytego, wyrażeń regularnych, wyszukiwania w sąsiedztwie lub innych zaawansowanych typów zapytań, konieczne będzie korzystanie z pełnej składni.

Wyszukiwanie rozmyte i wyszukiwanie z użyciem symboli wieloznacznych ma wpływ na wyniki wyszukiwania. W przypadku tych formatów zapytań nie jest wykonywana analiza lingwistyczna. Przed użyciem wyszukiwania rozmytego i wieloznacznego przejrzyj jak [działa wyszukiwanie pełnotekstowe w usłudze Azure Cognitive Search](search-lucene-query-architecture.md#stage-2-lexical-analysis) i poszukaj sekcji dotyczącej wyjątków od analizy leksykalne.

Aby uzyskać więcej informacji na temat scenariuszy kwerend włączonych przez analizator pełnej kwerendy, zobacz [Składnia kwerendy lucene w usłudze Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search).

### <a name="try-geospatial-search"></a><a name="geo-search"></a> Testowanie wyszukiwania geoprzestrzennego

 Wyszukiwanie geoprzestrzenne jest obsługiwane za pośrednictwem [typu danych edm.GeographyPoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) względem pola zawierającego współrzędne. Wyszukiwanie geograficzne jest rodzajem filtru określonego w artykule [Filter OData syntax](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) (Składnia filtrowania OData).

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-12212-4767-le-5"></a>Przykład (filtry współrzędnych geograficznych): `search=*&$count=true&$filter=geo.distance(Location,geography'POINT(-122.12 47.67)') le 5`

To zapytanie przykładowe służy do przefiltrowania wszystkich wyników danych pozycyjnych, które znajdują się w odległości mniejszej niż 5 km od danego punktu (określonego jako szerokość i długość geograficzna). Dodając parametr **$count**, możesz sprawdzić, ile wyników zostanie zwróconych, gdy zmienisz odległość lub współrzędne.

Wyszukiwanie geoprzestrzenne jest przydatne, jeśli aplikacja wyszukiwania ma funkcję „znajdź w pobliżu” lub używa nawigacji mapy. Nie jest to jednak wyszukiwanie pełnotekstowe. Jeśli masz wymagania użytkownika dotyczące wyszukiwania w mieście lub kraju/regionie według nazwy, dodaj pola zawierające nazwy miast lub krajów/regionów, oprócz współrzędnych.

## <a name="takeaways"></a>Wnioski

Ten samouczek dostarczył szybkie wprowadzenie do usługi Azure Cognitive Search przy użyciu witryny Azure portal.

Opisano tworzenie indeksu wyszukiwania przy użyciu kreatora **Importowanie danych**. Zostały przedstawione [indeksatory](search-indexer-overview.md), a także podstawowy przepływ pracy projektowania indeksu, w tym [obsługiwane modyfikacje opublikowanego indeksu](https://docs.microsoft.com/rest/api/searchservice/update-index).

Przy użyciu **Eksploratora wyszukiwania** w witrynie Azure Portal przedstawiono podstawową składnię zapytań w ramach praktycznych przykładów kluczowych możliwości, takich jak filtry, wyróżnianie trafień, wyszukiwanie rozmyte i wyszukiwanie geograficzne.

Przedstawiono również sposób znajdowania indeksów, indeksatorów i źródeł danych w portalu. Dla dowolnego nowego źródła danych możesz przy użyciu portalu szybko sprawdzić jego definicje lub kolekcje pól przy minimalnym nakładzie pracy.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli pracujesz w ramach własnej subskrypcji, dobrym pomysłem po zakończeniu projektu jest sprawdzenie, czy dalej potrzebujesz utworzonych zasobów. Nadal uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć i zarządzać nimi w portalu, korzystając z łącza **Wszystkie zasoby** lub **Grupy zasobów** w lewym okienku nawigacji.

Jeśli korzystasz z bezpłatnej usługi, należy pamiętać, że są ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby pozostać poniżej limitu. 

## <a name="next-steps"></a>Następne kroki

Użyj kreatora portalu, aby wygenerować gotową do użycia aplikację sieci web, która jest uruchamiana w przeglądarce. Możesz wypróbować tego kreatora na mały indeks, który właśnie utworzono, lub użyć jednego z wbudowanych przykładowych zestawów danych, aby uzyskać bogatsze środowisko wyszukiwania.

> [!div class="nextstepaction"]
> [Tworzenie aplikacji wyszukiwania w portalu](search-create-app-portal.md)