---
title: 'Szybki start: Tworzenie, obciążenia i tworzenie zapytań względem indeksu w witrynie Azure portal — usługa Azure Search'
description: Twórz i zapytania pierwszego indeksu w usłudze Azure Search przy użyciu wbudowanych przykładowych danych i Kreatora importu danych w witrynie Azure portal.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: a1cf72d9e3f5c2c6e919304d4d886a607c54f359
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59282665"
---
# <a name="quickstart-use-built-in-portal-tools-for-azure-search-import-indexing-and-queries"></a>Szybki start: Korzystanie z wbudowanych narzędzi portalu do importowania, indeksowania i wykonywania zapytań w usłudze Azure Search
> [!div class="op_single_selector"]
> * [Portal](search-get-started-portal.md)
> * [Program PowerShell](search-howto-dotnet-sdk.md)
> * [Postman](search-fiddler.md)
> * [C#](search-create-index-dotnet.md)
>*

Aby szybko poznać działanie usługi Azure Search, wypróbuj narzędzia wbudowane w witrynie Azure Portal. Kreatory i edytory nie oferują pełnej równoważności z interfejsami API .NET i REST, ale możesz szybko zacząć pracę bez korzystania z kodu, w ciągu kilku minut pisząc ciekawe zapytania odnoszące się do danych przykładowych.

> [!div class="checklist"]
> * Rozpocznij od publicznego bezpłatnego zestawu przykładowych danych hostowanego na platformie Azure
> * Uruchom kreatora **Importowanie danych** w usłudze Azure Search, aby załadować dane i wygenerować indeks
> * Monitoruj w portalu postęp indeksowania
> * Wyświetlaj istniejący indeks i opcje służące do modyfikowania go
> * Zapoznaj się z wyszukiwaniem pełnotekstowym, filtrami, aspektami, wyszukiwaniem rozmytym i wyszukiwaniem geograficznym, korzystając z **Eksploratora wyszukiwania**

Jeśli te narzędzia oferują zbyt ograniczone funkcje, możesz skorzystać z [wprowadzenia do programowania dla usługi Azure Search z użyciem kodu .NET](search-howto-dotnet-sdk.md) albo skorzystać z narzędzi [Postman lub Fiddler do wykonywania wywołań interfejsu API REST](search-fiddler.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). Możesz także obejrzeć 6-minutowy pokaz czynności wykonywanych w ramach tego samouczka, rozpoczynający się od około trzeciej minuty tego [klipu wideo z omówieniem usługi Azure Search](https://channel9.msdn.com/Events/Connect/2016/138).

## <a name="prerequisites"></a>Wymagania wstępne

[Tworzenie usługi Azure Search](search-create-service-portal.md) lub [znaleźć istniejącej usługi](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach Twojej bieżącej subskrypcji. Umożliwia to bezpłatna usługa dla tego przewodnika Szybki Start. 

### <a name="check-for-space"></a>Sprawdzanie ilości wolnego miejsca

Wielu klientów zaczyna od bezpłatnej usługi. Ta wersja jest ograniczona do trzech indeksów, trzech źródeł danych i trzech indeksatorów. Przed rozpoczęciem upewnij się, że dysponujesz miejscem na dodatkowe elementy. W ramach tego samouczka tworzony jest jeden obiekt każdego typu.

W sekcjach na pulpicie nawigacyjnym usługi wyświetlana jest liczba posiadanych już indeksów, indeksatorów i źródeł danych. 

![Listy indeksów, indeksatorów i źródeł danych](media/search-get-started-portal/tiles-indexers-datasources2.png)

## <a name="create-index"></a> Tworzenie indeksu i ładowanie danych

Zapytania wyszukiwania używają iteracyjnie [*indeksu*](search-what-is-an-index.md) zawierającego dane z możliwością wyszukiwania, metadane i dodatkowe konstrukcje, które optymalizują określone zachowania związane z wyszukiwaniem.

W tym samouczku zostanie użyty wbudowany przykładowy zestaw danych, który można przeszukiwać przy użyciu [*indeksatora*](search-indexer-overview.md) za pośrednictwem **kreatora importu danych**. Indeksator to przeszukiwarka specyficzna dla źródła, mająca możliwość odczytu metadanych i zawartości z obsługiwanych źródeł danych platformy Azure. Indeksatory są zazwyczaj używane programowo, ale w portalu można uzyskać do nich dostęp za pośrednictwem kreatora **Importowanie danych**. 

### <a name="step-1---start-the-import-data-wizard-and-create-a-data-source"></a>Krok 1 — Uruchomienie Kreatora importowania danych i utworzenie źródła danych

1. Na pulpicie nawigacyjnym usługi Azure Search kliknij pozycję **Importuj dane** na pasku poleceń, aby utworzyć i wypełnić indeks wyszukiwania.

   ![Polecenie importu danych](media/search-get-started-portal/import-data-cmd2.png)

2. W kreatorze kliknij kolejno pozycje **Połącz z danymi** > **Przykłady** > **realestate-us-sample**. To źródło danych jest wbudowane. Podczas tworzenia własnego źródła danych należy określić nazwę, typ i informacje o połączeniu. Po utworzeniu staje się ono „istniejącym źródłem danych”, które może zostać ponownie użyte w innych operacjach importu.

   ![Wybieranie przykładowego zestawu danych](media/search-get-started-portal/import-datasource-sample2.png)

3. Przejdź do następnej strony.

   ![Przycisk następnej strony dla wyszukiwania poznawczego](media/search-get-started-portal/next-button-add-cog-search.png)

### <a name="step-2---skip-cognitive-skills"></a>Krok 2 — Pomijanie kroku Umiejętności poznawcze

Kreator obsługuje tworzenie [potoku umiejętności poznawczych](cognitive-search-concept-intro.md) w celu wykorzystania algorytmów sztucznej inteligencji usługi Cognitive Services do indeksowania. 

Na razie pominiemy ten krok i przejdziemy bezpośrednio do kroku **Dostosowywanie indeksu docelowego**.

   ![Pomijanie kroku Umiejętności poznawcze](media/search-get-started-portal/skip-cog-skill-step.png)

> [!TIP]
> Możesz wypróbować nową funkcję wyszukiwania poznawczego (wersja zapoznawcza) dla usługi Azure Search z poziomu [przewodnika Szybki start dotyczącego wyszukiwania poznawczego](cognitive-search-quickstart-blob.md) lub [samouczka](cognitive-search-tutorial-blob.md).

### <a name="step-3---configure-index"></a>Krok 3 — Konfigurowanie indeksu

Zazwyczaj tworzenie indeksu jest czynnością bazującą na kodzie, którą wykonuje się przed załadowaniem danych. Jednak, jak pokazano w tym samouczku, kreator może wygenerować indeks podstawowy dla dowolnego źródła danych, które może przeszukiwać. Indeks wymaga co najmniej nazwy i kolekcji pól. Jedno z pól powinno być oznaczone jako klucz dokumentu w celu jednoznacznego zidentyfikowania poszczególnych dokumentów. Ponadto można określić analizatory języka lub sugestory, aby stosować autouzupełnianie lub sugerowane zapytania.

Pola mają typy danych i atrybuty. Pola wyboru w górnej części są *atrybutami indeksu*, które kontrolują sposób używania pola.

* **Pobieranie** oznacza, że pole jest wyświetlane na liście wyników wyszukiwania. Możesz oznaczyć poszczególne pola tak, aby nie były uwzględniane w wynikach wyszukiwania. W tym celu wystarczy usunąć zaznaczenie danego pola wyboru, na przykład wtedy, gdy pola są używane tylko w wyrażeniach filtrowania.
* **Klucz** to unikatowy identyfikator dokumentu. Zawsze jest ciągiem znaków i jest wymagany.
* Atrybuty **Możliwość filtrowania**, **Z możliwością sortowania** i **Możliwość tworzenia aspektów** określają, czy pola mogą być używane w strukturze nawigacji filtrowania, sortowania lub tworzenia aspektów.
* **Wyszukiwanie** oznacza, że pole jest uwzględniane podczas wyszukiwania pełnotekstowego. Ciągi można przeszukiwać. Pól liczbowych i logicznych często nie można wyszukiwać.

Wymagania dotyczące rozmiaru magazynu nie są zależne od Twojego wyboru. Na przykład jeśli ustawisz atrybut **Możliwość pobierania** dla wielu pól, nie zwiększy to wymaganego rozmiaru magazynu.

Domyślnie kreator skanuje źródło danych w poszukiwaniu unikatowych identyfikatorów jako podstawy dla pola klucza. *Ciągi* mają ustawione atrybuty **Pobieranie** i **Wyszukiwanie**. *Liczby całkowite* mają ustawione atrybuty **Pobieranie**, **Możliwość filtrowania**, **Z możliwością sortowania** i **Możliwość tworzenia aspektów**.

1. Zaakceptuj wartości domyślne. 

   Jeśli uruchomisz kreatora po raz drugi przy użyciu istniejącego źródła danych realestate, indeks nie zostanie skonfigurowany z atrybutami domyślnymi. Konieczne będzie ręczne wybranie atrybutów podczas przyszłych importów.

   ![Wygenerowany indeks realestate](media/search-get-started-portal/realestateindex2.png)

2. Przejdź do następnej strony.

   ![Następna strona — tworzenie indeksatora](media/search-get-started-portal/next-button-create-indexer.png)

### <a name="step-4---configure-indexer"></a>Krok 4 — Konfigurowanie indeksatora

W kreatorze **importu danych** kliknij pozycję **Indeksator** > **Nazwa**, a następnie wpisz nazwę indeksatora.

Ten obiekt definiuje proces wykonywalny. Możesz go uruchamiać w ramach harmonogramu cyklicznego, ale na razie użyj opcji domyślnej, aby od razu uruchomić indeksator jeden raz.

Kliknij przycisk **Prześlij**, aby utworzyć i jednocześnie uruchomić indeksator.

  ![Indeksator realestate](media/search-get-started-portal/realestate-indexer2.png)

## <a name="monitor-progress"></a>Monitorowanie postępu

Kreator powinien przenieść Cię do listy Indeksatory, gdzie można monitorować postęp. W przypadku samodzielnej nawigacji przejdź do strony Przegląd, a następnie kliknij przycisk **Indeksatory**.

Aktualizacja strony w portalu może wymagać kilku minut, ale na liście powinien być widoczny nowo utworzony indeksator w stanie wskazującym na trwające indeksowanie lub jego pomyślne zakończenie wraz z liczbą zindeksowanych dokumentów.

   ![Komunikat o postępie indeksatora](media/search-get-started-portal/indexers-inprogress2.png)

## <a name="view-the-index"></a>Wyświetlanie indeksu

Strona główna usługi zawiera linki do zasobów utworzonych w usłudze Azure Search.  Aby wyświetlić właśnie utworzony indeks, kliknij pozycję **Indeksy** na liście linków. 

   ![Lista Indeksy na pulpicie nawigacyjnym usługi](media/search-get-started-portal/indexes-list.png)

Na tej liście możesz kliknąć właśnie utworzony indeks *realestate-us-sample*, aby wyświetlić schemat indeksu i opcjonalnie dodać nowe pola. 

Karta **Pola** zawiera schemat indeksu. Przewiń listę w dół, aby wprowadzić nowe pole. W większości przypadków nie można zmienić istniejących pól. Istniejące pola mają fizyczną reprezentację w usłudze Azure Search i w związku z tym nie można ich modyfikować nawet za pomocą kodu. Aby całkowicie zmienić istniejące pole, utwórz nowy indeks, usuwając poprzednią wersję.

   ![Przykładowa definicja indeksu](media/search-get-started-portal/sample-index-def.png)

Inne konstrukcje, takie jak profile oceniania i opcje CORS, można dodać w dowolnym momencie.

Aby dobrze zaznajomić się z elementami, które można i których nie można edytować podczas projektowania indeksu, poświęć chwilę na zapoznanie się z opcjami definicji indeksu. Opcje wyświetlane na szaro oznaczają, że danej wartości nie można zmodyfikować ani usunąć. 

## <a name="query-index"></a> Tworzenie zapytań za pomocą Eksploratora wyszukiwania

Indeks wyszukiwania powinien być teraz gotowy do wykonywania względem niego zapytań za pomocą wbudowanej strony zapytań, noszącej nazwę [**Eksplorator wyszukiwania**](search-explorer.md). Zawiera ona pole wyszukiwania, umożliwiające testowanie dowolnych ciągów zapytań.

**Eksplorator wyszukiwania** jest przystosowany tylko do obsługi [żądań interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents), ale akceptuje zarówno [prostą składnię zapytań](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), jak i składnię [pełnego analizatora zapytań Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), a także wszystkie parametry wyszukiwania dostępne w ramach operacji [interfejsu API REST wyszukiwania dokumentów](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples).

> [!TIP]
> Następujące kroki są prezentowane od momentu 6:08 w [klipie wideo z omówieniem usługi Azure Search](https://channel9.msdn.com/Events/Connect/2016/138).
>

1. Kliknij pozycję **Eksplorator wyszukiwania** na pasku poleceń.

   ![Polecenie Eksploratora wyszukiwania](media/search-get-started-portal/search-explorer-cmd2.png)

2. Na pasku poleceń kliknij pozycję **Zmień indeks**, aby przełączyć się na zestaw danych *realestate-us-sample*. Na pasku poleceń kliknij pozycję **Ustaw wersję interfejsu API**, aby wyświetlić dostępne interfejsy API REST. W przypadku poniższych zapytań użyj ogólnie dostępnej wersji (2017-11-11).

   ![Polecenia indeksu i interfejsu API](media/search-get-started-portal/search-explorer-changeindex-se2.png)

3. Na pasku wyszukiwania wklej poniższe ciągi zapytań, a następnie kliknij pozycję **Wyszukaj**.

   ![Ciąg zapytania i przycisk Wyszukaj](media/search-get-started-portal/search-explorer-query-string-example.png)

## <a name="example-queries"></a>Przykładowe zapytania

Można wprowadzać terminy i frazy (podobnie jak w wyszukiwaniu Bing lub Google) lub w pełni określone wyrażenia zapytania. Wyniki są zwracane w postaci pełnych dokumentów JSON.

### <a name="simple-query-with-top-n-results"></a>Proste zapytanie z pierwszymi N wynikami

#### <a name="example-string-query-searchseattle"></a>Przykład (zapytanie w postaci ciągu znaków): `search=seattle`

* Parametr **search** służy do wprowadzania pełnotekstowego wyszukiwania słów kluczowych — w tym przypadku zwrócone zostaną oferty w King County w stanie Waszyngton zawierające ciąg *Seattle* w dowolnym polu możliwym do przeszukania w dokumencie.

* **Eksplorator wyszukiwania** zwraca wyniki w formacie JSON, które są szczegółowe i trudne do odczytania, jeśli dokumenty mają gęstą strukturę. Jest to zamierzone. Widoczność całego dokumentu jest ważna dla celów programowania, zwłaszcza podczas testów. Aby zwiększyć wygodę użytkowania, należy napisać kod [obsługujący wyniki wyszukiwania](search-pagination-page-layout.md) w celu wyróżnienia istotnych elementów.

* Dokumenty składają się ze wszystkich pól oznaczonych jako możliwe do pobierania w indeksie. Aby wyświetlić atrybuty indeksu w portalu, kliknij pozycję *realestate-us-sample* na liście **Indeksy**.

#### <a name="example-parameterized-query-searchseattlecounttruetop100"></a>Przykład (zapytanie sparametryzowane): `search=seattle&$count=true&$top=100`

* Symbol **&** służy do dołączania parametrów wyszukiwania, które mogą być podane w dowolnej kolejności.

* Parametr **$count = true** powoduje zwrócenie łącznej liczby wszystkich zwróconych dokumentów. Ta wartość jest wyświetlana na początku listy wyników wyszukiwania. Możesz zweryfikować zapytania filtru, monitorując zmiany raportowane przez parametr **$count=true**. Zmniejszająca się liczba wskazuje, że filtr działa.

* Parametr **$top=100** powoduje zwrócenie 100 dokumentów o najwyższej randze spośród wszystkich dokumentów. Domyślnie usługa Azure Search zwraca 50 pierwszych najlepszych dopasowań. Tę liczbę możesz zwiększyć lub zmniejszyć za pomocą parametru **$top**.

### <a name="filter-query"></a> Filtrowanie zapytania

Żądania wyszukiwania zawierają filtry, jeśli zostanie dodany parametr **$filter**. 

#### <a name="example-filtered-searchseattlefilterbeds-gt-3"></a>Przykład (filtrowane): `search=seattle&$filter=beds gt 3`

* Parametr **$filter** zwraca wyniki spełniające podane kryteria. W tym przypadku są to oferty z liczbą sypialni większą niż 3.

* Składnia filtru to konstrukcja OData. Aby uzyskać więcej informacji, zobacz [Filter OData syntax](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) (Składnia filtrowania OData).

### <a name="facet-query"></a> Tworzenie aspektu zapytania

Filtry aspektów są uwzględniane w żądaniach wyszukiwania. Korzystając z parametru facet, można zwrócić zagregowaną liczbę dokumentów, które są zgodne z podaną wartością aspektu.

#### <a name="example-faceted-with-scope-reduction-searchfacetcitytop2"></a>Przykład (aspektowe ze zmniejszeniem zakresu): `search=*&facet=city&$top=2`

* Parametr **search=*** to puste wyszukiwanie. Puste wyszukiwania umożliwiają znalezienie wszystkiego. Jednym z powodów przesłania pustego zapytania może być potrzeba przeprowadzenia filtrowania lub utworzenia aspektów pełnego zestawu dokumentów. Na przykład może być potrzebne utworzenie aspektów struktury nawigacji składającej się ze wszystkich miast w indeksie.

* Parametr **facet** zwraca strukturę nawigacji, którą można przekazać do kontrolki interfejsu użytkownika. Zwraca ona kategorie i liczbę elementów. W takim przypadku kategorie są oparte na określonej liczbie miast. Nie istnieje żadna agregacja w usłudze Azure Search, ale możesz przybliżyć się do agregacji za pomocą parametru `facet`, który podaje liczbę dokumentów w poszczególnych kategoriach.

* Parametr **$top=2** powoduje zwrócenie dwóch dokumentów, co pokazuje, że za pomocą parametru `top` możesz zmniejszyć lub zwiększyć liczbę wyników.

#### <a name="example-facet-on-numeric-values-searchseattlefacetbeds"></a>Przykład (aspektowe z wartościami liczbowymi): `search=seattle&facet=beds`**

* To zapytanie to aspekt liczby sypialni w wyszukiwaniu tekstowym dla kategorii *Seattle*. Termin *beds* można określić jako aspekt, ponieważ w indeksie jest oznaczony jako pole z możliwością pobierania, filtrowania i tworzenia aspektów, a wartości, które zawiera (liczbowe od 1 do 5), są odpowiednie na potrzeby kategoryzowania ofert w grupach (oferty z 3 i 4 sypialniami).

* Aspekty mogą być tworzone tylko na podstawie pól z możliwością filtrowania. W wynikach mogą być zwracane tylko pola z możliwością pobierania.

### <a name="highlight-query"></a> Wyróżnianie wyników wyszukiwania

Wyróżnianie trafień oznacza dodawanie formatowania do tekstu zgodnego ze słowem kluczowym w przypadku znalezienia dopasowań w określonym polu. Jeśli termin wyszukiwania jest głęboko ukryty w opisie, możliwe jest dodanie wyróżnienia trafień w celu jego łatwiejszego zauważenia.

#### <a name="example-highlighter-searchgranite-countertopshighlightdescription"></a>Przykład (wyróżnienie): `search=granite countertops&highlight=description`

* W tym przykładzie sformatowana fraza *granite countertops* jest lepiej widoczna w polu opisu.

#### <a name="example-linguistic-analysis-searchmicehighlightdescription"></a>Przykład (analiza lingwistyczna): `search=mice&highlight=description`

* Wyszukiwanie pełnotekstowe umożliwia znalezienie wyrazów z podobną semantyką. W takim przypadku wyniki wyszukiwania zawierają wyróżniony tekst „mouse” (mysz) dla domów z plagą myszy zwracanych w rezultacie wyszukiwania słowa kluczowego „mice” (myszy). Różne rodzaje tego samego wyrazu mogą być wyświetlane w wynikach z powodu przeprowadzenia analizy lingwistycznej.

* Usługa Azure Search obsługuje 56 analizatorów — zarówno oprogramowania Lucene, jak i firmy Microsoft. W usłudze Azure Search domyślnie używany jest standardowy analizator Lucene.

### <a name="fuzzy-search"></a> Testowanie wyszukiwania rozmytego

Domyślnie, błędnie napisane terminów zapytania, takie jak *samamish* dla płaska Sammamish w obszarze Seattle, niepowodzeniem równiny samammish w typowym wyszukiwaniu. Poniższy przykład nie zwraca żadnych wyników.

#### <a name="example-misspelled-term-unhandled-searchsamamish"></a>Przykład (błąd pisowni, nieobsługiwany): `search=samamish`

Aby obsłużyć błędy pisowni, można użyć wyszukiwania rozmytego. Wyszukiwanie rozmyte jest używane w przypadku korzystania z pełnej składni zapytań Lucene, co wymaga wykonania dwóch czynności: ustawienia parametru **queryType=full** w zapytaniu i dodania znaku **~** do wyszukiwanego ciągu.

#### <a name="example-misspelled-term-handled-searchsamamishquerytypefull"></a>Przykład (błąd pisowni, obsługiwany): `search=samamish~&queryType=full`

Teraz zwracane są dokumenty zawierające dopasowany termin „Sammamish”.

Gdy parametr **queryType** jest nieokreślony, używany jest domyślny prosty analizator zapytań. Prosty analizator zapytań jest szybszy, ale jeśli wymagane jest użycie wyszukiwania rozmytego, wyrażeń regularnych, wyszukiwania w sąsiedztwie lub innych zaawansowanych typów zapytań, konieczne będzie korzystanie z pełnej składni.

Wyszukiwanie rozmyte i wyszukiwanie z użyciem symboli wieloznacznych ma wpływ na wyniki wyszukiwania. W przypadku tych formatów zapytań nie jest wykonywana analiza lingwistyczna. Zanim zastosujesz wyszukiwanie rozmyte i wyszukiwanie z użyciem symboli wieloznacznych, zobacz [How full text search works in Azure Search (Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Search)](search-lucene-query-architecture.md#stage-2-lexical-analysis) i zapoznaj się z sekcją dotyczącą wyjątków w przypadku analizy leksykalnej.

Aby uzyskać więcej informacji o scenariuszach zapytań dostępnych przy użyciu pełnego analizatora zapytań, zobacz [Lucene query syntax in Azure Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) (Składnia zapytań Lucene w usłudze Azure Search).

### <a name="geo-search"></a> Testowanie wyszukiwania geoprzestrzennego

Wyszukiwanie geoprzestrzenne jest obsługiwane za pośrednictwem [typu danych edm.GeographyPoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) względem pola zawierającego współrzędne. Wyszukiwanie geograficzne jest rodzajem filtru określonego w artykule [Filter OData syntax](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) (Składnia filtrowania OData).

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-122121513-47673988-le-5"></a>Przykład (filtry współrzędnych geograficznych): `search=*&$count=true&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5`

To zapytanie przykładowe służy do przefiltrowania wszystkich wyników danych pozycyjnych, które znajdują się w odległości mniejszej niż 5 km od danego punktu (określonego jako szerokość i długość geograficzna). Dodając parametr **$count**, możesz sprawdzić, ile wyników zostanie zwróconych, gdy zmienisz odległość lub współrzędne.

Wyszukiwanie geoprzestrzenne jest przydatne, jeśli aplikacja wyszukiwania ma funkcję „znajdź w pobliżu” lub używa nawigacji mapy. Nie jest to jednak wyszukiwanie pełnotekstowe. Jeśli istnieją wymagania użytkownika dotyczące wyszukiwania w mieście lub kraju według nazwy, oprócz współrzędnych dodaj pola zawierające nazwy miasta lub kraju.

## <a name="takeaways"></a>Wnioski

W tym samouczku podano krótkie wprowadzenie do korzystania z usługi Azure Search za pomocą witryny Azure Portal.

Opisano tworzenie indeksu wyszukiwania przy użyciu kreatora **Importowanie danych**. Zostały przedstawione [indeksatory](search-indexer-overview.md), a także podstawowy przepływ pracy projektowania indeksu, w tym [obsługiwane modyfikacje opublikowanego indeksu](https://docs.microsoft.com/rest/api/searchservice/update-index).

Przy użyciu **Eksploratora wyszukiwania** w witrynie Azure Portal przedstawiono podstawową składnię zapytań w ramach praktycznych przykładów kluczowych możliwości, takich jak filtry, wyróżnianie trafień, wyszukiwanie rozmyte i wyszukiwanie geograficzne.

Przedstawiono również sposób znajdowania indeksów, indeksatorów i źródeł danych w portalu. Dla dowolnego nowego źródła danych możesz przy użyciu portalu szybko sprawdzić jego definicje lub kolekcje pól przy minimalnym nakładzie pracy.

## <a name="clean-up"></a>Czyszczenie

Jeśli w tym samouczku usługa Azure Search była używana po raz pierwszy, usuń grupę zasobów zawierającą usługę Azure Search. W przeciwnym razie wyszukaj odpowiednią nazwę grupy zasobów na liście usług i usuń ją.

## <a name="next-steps"></a>Kolejne kroki

Możesz lepiej poznać usługę Azure Search przy użyciu narzędzi programistycznych:

* [Tworzenie indeksu przy użyciu zestawu .NET SDK](https://docs.microsoft.com/azure/search/search-create-index-dotnet)
* [Tworzenie indeksu przy użyciu interfejsów API REST](https://docs.microsoft.com/azure/search/search-create-index-rest-api)
* [Tworzenie indeksu przy użyciu narzędzia Postman lub Fiddler i interfejsów API REST usługi Azure Search](search-fiddler.md)
