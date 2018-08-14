---
title: Samouczek dotyczący indeksowania, wykonywania zapytań i filtrowania w usłudze Azure Search przy użyciu portalu | Microsoft Docs
description: Podczas pracy z tym samouczkiem użyjesz wstępnie zdefiniowanych danych przykładowych do wygenerowania indeksu w usłudze Azure Search za pomocą witryny Azure Portal. Dowiedz się więcej o wyszukiwaniu pełnotekstowym, filtrach, aspektach, wyszukiwaniu rozmytym, wyszukiwaniu geograficznym i innych funkcjach.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.topic: tutorial
ms.date: 07/10/2018
ms.author: heidist
ms.openlocfilehash: aac579da3aaf6ab1507bbc12d79a5b183a82d665
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39592759"
---
# <a name="tutorial-use-built-in-tools-for-azure-search-indexing-and-queries"></a>Samouczek: korzystanie z wbudowanych narzędzi do indeksowania i wykonywania zapytań w usłudze Azure Search

Aby szybko przejrzeć i poznać pojęcia dotyczące usługi Azure Search, możesz użyć wbudowanych narzędzi dostępnych na stronie usługi Azure Search w witrynie Azure Portal. Te narzędzia mogą nie oferować pełnej funkcjonalności platformy .NET i interfejsów API REST. Jednak kreatory i edytory zapewniają wprowadzenie do usługi Azure Search bez używania kodu, dzięki czemu możesz od razu zacząć pisać interesujące zapytania dotyczące przykładowego zestawu danych.

> [!div class="checklist"]
> * Na początek skorzystaj z publicznie dostępnych danych przykładowych i wygeneruj automatycznie indeks usługi Azure Search za pomocą **kreatora importu danych**.
> * Wyświetl schemat i atrybuty dowolnego indeksu opublikowanego w usłudze Azure Search.
> * Zapoznaj się z wyszukiwaniem pełnotekstowym, filtrami, aspektami, wyszukiwaniem rozmytym i wyszukiwaniem geograficznym, korzystając z **Eksploratora wyszukiwania**.  

Jeśli te narzędzia oferują zbyt ograniczone funkcje, możesz skorzystać z [wprowadzenia do programowania dla usługi Azure Search z użyciem kodu .NET](search-howto-dotnet-sdk.md) lub [internetowych narzędzi do testowania wywołań interfejsu API REST](search-fiddler.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). Możesz także obejrzeć 6-minutowy pokaz czynności wykonywanych w ramach tego samouczka, rozpoczynający się od około trzeciej minuty tego [klipu wideo z omówieniem usługi Azure Search](https://channel9.msdn.com/Events/Connect/2016/138).

## <a name="prerequisites"></a>Wymagania wstępne

[Utwórz usługę Azure Search](search-create-service-portal.md) lub znajdź istniejącą usługę w ramach bieżącej subskrypcji.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Otwórz pulpit nawigacyjny usługi Azure Search. Jeśli nie przypniesz kafelka usługi do pulpitu nawigacyjnego, usługę można znaleźć w następujący sposób:

   * Na pasku dostępu kliknij pozycję **Wszystkie usługi** w lewym okienku nawigacji.
   * W polu wyszukiwania wpisz *search*, aby uzyskać listę usług wyszukiwania dostępnych w Twojej subskrypcji. Kliknij pozycję **Search services** (Usługi wyszukiwania). Usługa powinna zostać wyświetlona na liście.

### <a name="check-for-space"></a>Sprawdzanie ilości wolnego miejsca

Wielu klientów zaczyna od bezpłatnej usługi. Ta wersja jest ograniczona do trzech indeksów, trzech źródeł danych i trzech indeksatorów. Przed rozpoczęciem upewnij się, że dysponujesz miejscem na dodatkowe elementy. W ramach tego samouczka tworzony jest jeden obiekt każdego typu.

> [!TIP]
> Na kafelkach na pulpicie nawigacyjnym usługi wyświetlana jest liczba posiadanych już indeksów, indeksatorów i źródeł danych. Kafelek Indeksator zawiera wskaźniki sukcesów i niepowodzeń. Kliknij kafelek, aby wyświetlić liczbę indeksatorów.
>
> ![Kafelki dla indeksatorów i źródeł danych][1]
>

## <a name="create-index"></a> Tworzenie indeksu i ładowanie danych

Zapytania wyszukiwania używają iteracyjnie [*indeksu*](search-what-is-an-index.md) zawierającego dane z możliwością wyszukiwania, metadane i dodatkowe konstrukcje, które optymalizują określone zachowania związane z wyszukiwaniem.

W tym samouczku zostanie użyty wbudowany przykładowy zestaw danych, który można przeszukiwać przy użyciu [*indeksatora*](search-indexer-overview.md) za pośrednictwem **kreatora importu danych**. Indeksator to przeszukiwarka specyficzna dla źródła, mająca możliwość odczytu metadanych i zawartości z obsługiwanych źródeł danych platformy Azure. Te indeksatory są widoczne w portalu za pośrednictwem kreatora **Importowanie danych**. Później możesz programowo tworzyć indeksatory i zarządzać nimi jako niezależnymi zasobami.

### <a name="step-1-start-the-import-data-wizard"></a>Krok 1. Uruchamianie kreatora importu danych

1. Na pulpicie nawigacyjnym usługi Azure Search kliknij pozycję **Importuj dane** na pasku poleceń, aby uruchomić kreatora. Ten kreator ułatwia tworzenie i wypełnianie indeksu wyszukiwania.

    ![Polecenie importu danych][2]

2. W kreatorze kliknij kolejno pozycje **Połącz z danymi** > **Przykłady** > **realestate-us-sample**. To źródło danych jest wstępnie skonfigurowane przy użyciu nazwy, typu i informacji o połączeniu. Po utworzeniu staje się ono „istniejącym źródłem danych”, które może zostać ponownie użyte w innych operacjach importu.

    ![Wybieranie przykładowego zestawu danych][9]

3. Kliknij pozycję **OK**, aby go użyć.

### <a name="skip-cognitive-skills"></a>Pomijanie kroku Umiejętności poznawcze

Obszar **Importuj dane** zawiera opcjonalny krok dotyczący umiejętności poznawczych, który umożliwia dodanie niestandardowych algorytmów sztucznej inteligencji do indeksowania. Na razie pomiń ten krok i przejdź do kroku **Dostosowywanie indeksu docelowego**.

> [!TIP]
> Możesz wypróbować nową funkcję wyszukiwania poznawczego (wersja zapoznawcza) dla usługi Azure Search z poziomu [przewodnika Szybki start dotyczącego wyszukiwania poznawczego](cognitive-search-quickstart-blob.md) lub [samouczka](cognitive-search-tutorial-blob.md).

   ![Pomijanie kroku Umiejętności poznawcze][11]

### <a name="step-2-define-the-index"></a>Krok 2. Definiowanie indeksu

Zazwyczaj indeks jest tworzony ręcznie przy użyciu kodu. W tym samouczku kreator może wygenerować indeks dla dowolnego źródła danych, które może przeszukiwać. Indeks wymaga co najmniej nazwy i kolekcji pól. Jedno z pól powinno być oznaczone jako klucz dokumentu w celu jednoznacznego zidentyfikowania poszczególnych dokumentów.

Pola mają typy danych i atrybuty. Pola wyboru w górnej części są *atrybutami indeksu*, które kontrolują sposób używania pola.

* **Pobieranie** oznacza, że pole jest wyświetlane na liście wyników wyszukiwania. Możesz oznaczyć poszczególne pola tak, aby nie były uwzględniane w wynikach wyszukiwania. W tym celu wystarczy usunąć zaznaczenie danego pola wyboru, na przykład wtedy, gdy pola są używane tylko w wyrażeniach filtrowania.
* Atrybuty **Filtrowanie**, **Sortowanie** i **Tworzenie aspektów** określają, czy pole może być używane w strukturze nawigacji tworzenia aspektów, filtrowania lub sortowania.
* **Wyszukiwanie** oznacza, że pole jest uwzględniane podczas wyszukiwania pełnotekstowego. Ciągi można przeszukiwać. Pól liczbowych i logicznych często nie można wyszukiwać.

Domyślnie kreator skanuje źródło danych w poszukiwaniu unikatowych identyfikatorów jako podstawy dla pola klucza. Ciągi są określane jako możliwe do pobierania i przeszukiwania. Liczby całkowite są określane jako możliwe do pobierania, filtrowania, sortowania i tworzenia aspektów.

  ![Wygenerowany indeks realestate][3]

Kliknij przycisk **OK**, aby utworzyć indeks.

### <a name="step-3-define-the-indexer"></a>Krok 3. Definiowanie indeksatora

W kreatorze **importu danych** kliknij pozycję **Indeksator** > **Nazwa**, a następnie wpisz nazwę indeksatora.

Ten obiekt definiuje proces wykonywalny. Możesz go uruchamiać w ramach harmonogramu cyklicznego, ale na razie użyj opcji domyślnej, aby uruchomić indeksatora jednorazowo natychmiast po kliknięciu przycisku **OK**.  

  ![Indeksator realestate][8]

### <a name="check-progress"></a>Sprawdzanie postępu

Aby monitorować importowanie danych, wróć do pulpitu nawigacyjnego usługi, przewiń w dół, a następnie kliknij dwukrotnie kafelek **Indeksatory**, aby otworzyć listę indeksatorów. Na liście powinien być widoczny nowo utworzony indeksator w stanie wskazującym trwające indeksowanie lub pomyślne jego zakończenie wraz z liczbą zindeksowanych dokumentów.

   ![Komunikat o postępie indeksatora][4]

### <a name="step-4-view-the-index"></a>Krok 4. Wyświetlanie indeksu

Kafelki na pulpicie nawigacyjnym usługi przedstawiają podsumowanie informacji o różnych obiektach w zasobach, a także umożliwiają dostęp do informacji szczegółowych. Kafelek **Indeksy** zawiera listę istniejących indeksów, zawierającą między innymi indeks *realestate-us-sample* utworzony w poprzednim kroku.

Kliknij teraz indeks *realestate-us-sample*, aby wyświetlić dostępne w portalu opcje dotyczące jego definicji. Opcja **Dodaj/edytuj pola** umożliwia tworzenie nowych pól i dodawanie do nich atrybutów. Istniejące pola mają fizyczną reprezentację w usłudze Azure Search i w związku z tym nie można ich modyfikować nawet za pomocą kodu. Aby wprowadzić poważne zmiany w istniejącym polu, utwórz nowe pole i usuń poprzednią wersję.

   ![Przykładowa definicja indeksu][10]

Inne konstrukcje, takie jak profile oceniania i opcje CORS, można dodać w dowolnym momencie.

Aby dobrze zaznajomić się z elementami, które można i których nie można edytować podczas projektowania indeksu, poświęć chwilę na zapoznanie się z opcjami definicji indeksu. Opcje wyświetlane na szaro oznaczają, że danej wartości nie można zmodyfikować ani usunąć. Podobnie, pomiń na razie pola wyboru Analizator i Sugestor.

## <a name="query-index"></a> Wykonywanie zapytań względem indeksu

Indeks wyszukiwania powinien być teraz gotowy do wykonywania względem niego zapytań za pomocą wbudowanej strony zapytań, noszącej nazwę [**Eksplorator wyszukiwania**](search-explorer.md). Zawiera ona pole wyszukiwania, umożliwiające testowanie dowolnych ciągów zapytań.

> [!TIP]
> Następujące kroki są prezentowane od momentu 6:08 w [klipie wideo z omówieniem usługi Azure Search](https://channel9.msdn.com/Events/Connect/2016/138).
>

1. Kliknij pozycję **Eksplorator wyszukiwania** na pasku poleceń.

   ![Polecenie Eksploratora wyszukiwania][5]

2. Na pasku poleceń kliknij pozycję **Zmień indeks**, aby przełączyć się na zestaw danych *realestate-us-sample*. Na pasku poleceń kliknij pozycję **Ustaw wersję interfejsu API**, aby wyświetlić dostępne interfejsy API REST. W przypadku poniższych zapytań użyj ogólnie dostępnej wersji (2017-11-11).

   ![Polecenia indeksu i interfejsu API][6]

3. Na pasku wyszukiwania wprowadź poniższe ciągi zapytań, a następnie kliknij pozycję **Wyszukaj**.

    > [!NOTE]
    > **Eksplorator wyszukiwania** umożliwia tylko obsługę [żądań interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents). Akceptuje on zarówno [prostą składnię zapytań](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), jak i składnię [pełnego analizatora zapytań Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), a także wszystkie parametry wyszukiwania dostępne w ramach operacji [Wyszukiwania dokumentów](https://docs.microsoft.com/rest/api/searchservice/search-documents).
    >

### <a name="simple-query-with-top-n-results"></a>Proste zapytanie z pierwszymi N wynikami

#### <a name="example-string-searchseattle"></a>Przykład (ciąg): `search=seattle`

* Parametr **search** służy do wprowadzania pełnotekstowego wyszukiwania słów kluczowych — w tym przypadku zwrócone zostaną oferty w King County w stanie Waszyngton zawierające ciąg *Seattle* w dowolnym polu możliwym do przeszukania w dokumencie.

* **Eksplorator wyszukiwania** zwraca wyniki w formacie JSON, które są szczegółowe i trudne do odczytania, jeśli dokumenty mają gęstą strukturę. Jest to zamierzone. Widoczność całego dokumentu jest ważne z punktu widzenia celów programowania, zwłaszcza podczas testów. Aby zwiększyć wygodę użytkowania, należy napisać kod [obsługujący wyniki wyszukiwania](search-pagination-page-layout.md) w celu wyróżnienia istotnych elementów.

* Dokumenty składają się ze wszystkich pól oznaczonych jako możliwe do pobierania w indeksie. Aby wyświetlić atrybuty indeksu w portalu, kliknij zestaw danych *realestate-us-sample* w kafelku **Indeksy**.

#### <a name="example-parameterized-searchseattlecounttruetop100"></a>Przykład (sparametryzowane): `search=seattle&$count=true&$top=100`

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

Domyślnie, terminy wyszukiwania z błędami pisowni, na przykład *samamish* zamiast prawidłowej nazwy równiny Sammamish w pobliżu Seattle, spowodują, że w typowym wyszukiwaniu nie zostaną zwrócone żadne dopasowania. Poniższy przykład nie zwraca żadnych wyników.

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

W tym samouczku podano krótkie wprowadzenie do korzystania z usługi Azure Search w witrynie Azure Portal.

Opisano tworzenie indeksu wyszukiwania przy użyciu kreatora **Importowanie danych**. Zostały przedstawione [indeksatory](search-indexer-overview.md), a także podstawowy przepływ pracy projektowania indeksu, w tym [obsługiwane modyfikacje opublikowanego indeksu](https://docs.microsoft.com/rest/api/searchservice/update-index).

Przy użyciu **Eksploratora wyszukiwania** w witrynie Azure Portal przedstawiono podstawową składnię zapytań w ramach praktycznych przykładów kluczowych możliwości, takich jak filtry, wyróżnianie trafień, wyszukiwanie rozmyte i wyszukiwanie geograficzne.

Przedstawiono też używanie kafelków na pulpicie nawigacyjnym portalu dla indeksu wyszukiwania, indeksatora i źródeł danych. Dla dowolnego nowego źródła danych możesz przy użyciu portalu szybko sprawdzić jego definicje lub kolekcje pól przy minimalnym nakładzie pracy.

## <a name="clean-up"></a>Czyszczenie

Jeśli w tym samouczku usługa Azure Search była używana po raz pierwszy, usuń grupę zasobów zawierającą usługę Azure Search. W przeciwnym razie wyszukaj odpowiednią nazwę grupy zasobów na liście usług i usuń ją.

## <a name="next-steps"></a>Następne kroki

Możesz lepiej poznać usługę Azure Search przy użyciu narzędzi programistycznych:

* [Tworzenie indeksu za pomocą zestawu SDK platformy .NET](https://docs.microsoft.com/azure/search/search-create-index-dotnet)
* [Tworzenie indeksu za pomocą interfejsów API REST](https://docs.microsoft.com/azure/search/search-create-index-rest-api)
* Używanie [narzędzia do testowania w sieci Web, takiego jak Postman lub Fiddler, do wywoływania interfejsów API REST usługi Azure Search](search-fiddler.md)

<!--Image references-->
[1]: ./media/search-get-started-portal/tiles-indexers-datasources2.png
[2]: ./media/search-get-started-portal/import-data-cmd2.png
[3]: ./media/search-get-started-portal/realestateindex2.png
[4]: ./media/search-get-started-portal/indexers-inprogress2.png
[5]: ./media/search-get-started-portal/search-explorer-cmd2.png
[6]: ./media/search-get-started-portal/search-explorer-changeindex-se2.png
[7]: ./media/search-get-started-portal/search-explorer-query2.png
[8]: ./media/search-get-started-portal/realestate-indexer2.png
[9]: ./media/search-get-started-portal/import-datasource-sample2.png
[10]: ./media/search-get-started-portal/sample-index-def.png
[11]: ./media/search-get-started-portal/skip-cog-skill-step.png