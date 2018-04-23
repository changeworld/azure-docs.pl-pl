---
title: Samouczek usługi Azure Search dotyczący indeksowania, wykonywania zapytań i filtrowania przy użyciu portalu | Microsoft Docs
description: W witrynie Azure Portal do wygenerowania indeksu w usłudze Azure Search można użyć wstępnie zdefiniowanych danych przykładowych. Dowiedz się więcej o wyszukiwaniu pełnotekstowym, filtrach, aspektach, wyszukiwaniu rozmytym, wyszukiwaniu geograficznym i innych funkcjach.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
ms.service: search
ms.topic: tutorial
ms.date: 12/04/2017
ms.author: heidist
ms.openlocfilehash: a0dbc4ca0b3ca5ce59fb81e1a241982bca099aab
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="create-query-and-filter-an-azure-search-index-in-the-portal"></a>Tworzenie, przeszukiwanie i filtrowanie indeksu usługi Azure Search w portalu

W witrynie Azure Portal rozpocznij pracę od wstępnie zdefiniowanego przykładowego zestawu danych, aby szybko wygenerować indeks usługi Azure Search za pomocą kreatora **importu danych**. Zapoznaj się z wyszukiwaniem pełnotekstowym, filtrami, aspektami, wyszukiwaniem rozmytym i wyszukiwaniem geograficznym, korzystając z **Eksploratora wyszukiwania**.  

To wprowadzenie bez użycia kodu pozwala rozpocząć pracę ze wstępnie zdefiniowanymi danymi i umożliwia szybkie pisanie potrzebnych zapytań. Choć narzędzia portalu nie zastępują kodu, mogą one być użyteczne w przypadku następujących zadań:

+ Nabycie praktycznych umiejętności w możliwie najkrótszym czasie
+ Utworzenie prototypu indeksu przed napisaniem kodu w kreatorze **importu danych**
+ Testowanie zapytań i składni analizatora w **Eksploratorze wyszukiwania**
+ Wyświetlanie istniejącego indeksu opublikowanego w usłudze i wyszukiwanie jego atrybutów

**Szacowany czas:** około 15 minut lub dłużej, jeśli wymagane jest także utworzenie konta lub zarejestrowanie się w usłudze. 

Można również przyspieszyć pracę, zapoznając się z [opartym na kodzie wprowadzeniem do programowania usługi Azure Search w programie .NET](search-howto-dotnet-sdk.md).

## <a name="prerequisites"></a>Wymagania wstępne

Ten samouczek zakłada, że użytkownik posiada [subskrypcję platformy Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) i [usługę Azure Search](search-create-service-portal.md). 

Jeśli nie chcesz od razu aprowizować usługi, możesz obejrzeć 6-minutowy pokaz czynności w ramach tego samouczka rozpoczynający się od około trzeciej minuty tego [klipu wideo z omówieniem usługi Azure Search](https://channel9.msdn.com/Events/Connect/2016/138).

## <a name="find-your-service"></a>Znajdowanie usługi
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Otwórz pulpit nawigacyjny usługi Azure Search. Jeśli nie przypniesz kafelka usługi do pulpitu nawigacyjnego, usługę można znaleźć w następujący sposób: 
   
   * Na pasku dostępu kliknij pozycję **Wszystkie usługi** w lewym okienku nawigacji.
   * W polu wyszukiwania wpisz łańcuch *search*, aby uzyskać listę usług wyszukiwania dla Twojej subskrypcji. Usługa powinna zostać wyświetlona na liście. 

## <a name="check-for-space"></a>Sprawdzanie ilości wolnego miejsca
Wielu klientów zaczyna od bezpłatnej usługi. Ta wersja jest ograniczona do trzech indeksów, trzech źródeł danych i trzech indeksatorów. Przed rozpoczęciem upewnij się, że dysponujesz miejscem na dodatkowe elementy. W ramach tego samouczka tworzony jest jeden obiekt każdego typu. 

> [!TIP] 
> Na kafelkach na pulpicie nawigacyjnym usługi wyświetlana jest liczba posiadanych już indeksów, indeksatorów i źródeł danych. Kafelek Indeksator zawiera wskaźniki sukcesów i niepowodzeń. Kliknij kafelek, aby wyświetlić liczbę indeksatorów. 
>
> ![Kafelki dla indeksatorów i źródeł danych][1]
>

## <a name="create-index"></a> Tworzenie indeksu i ładowanie danych
Zapytania wyszukiwania przechodzą przez kolejne pozycje *indeksu* zawierającego dane z możliwością wyszukiwania, metadane i konstrukcje używane do optymalizacji niektórych zachowań wyszukiwania.

Aby podczas tego zadania korzystać jedynie z portalu, użyty zostanie wbudowany przykładowy zestaw danych, który może być przeszukiwany przy użyciu indeksatora za pośrednictwem kreatora **importu danych**. 

#### <a name="step-1-start-the-import-data-wizard"></a>Krok 1. Uruchamianie kreatora importu danych
1. Na pulpicie nawigacyjnym usługi Azure Search kliknij pozycję **Importuj dane** na pasku poleceń, aby uruchomić kreatora, który zarówno tworzy, jak i wypełnia indeks.
   
    ![Polecenie importu danych][2]

2. W kreatorze kliknij pozycję **Źródło danych** > **Przykłady** > **realestate-us-sample**. To źródło danych jest wstępnie skonfigurowane przy użyciu nazwy, typu i informacji o połączeniu. Po utworzeniu staje się ono „istniejącym źródłem danych”, które może zostać ponownie użyte w innych operacjach importu.

    ![Wybieranie przykładowego zestawu danych][9]

3. Kliknij pozycję **OK**, aby go użyć.

#### <a name="step-2-define-the-index"></a>Krok 2. Definiowanie indeksu
Tworzenie indeksu zwykle odbywa się ręcznie i z użyciem kodu, ale kreator może wygenerować indeks dla dowolnego źródła danych, które może przeszukiwać. Indeks wymaga co najmniej nazwy i kolekcji pól, z jednym polem oznaczonym jako klucz dokumentu w celu jednoznacznego zidentyfikowania poszczególnych dokumentów.

Pola mają typy danych i atrybuty. Pola wyboru w górnej części są *atrybutami indeksu*, które kontrolują sposób używania pola. 

* **Pobieranie** oznacza, że pole jest wyświetlane na liście wyników wyszukiwania. Możesz oznaczyć poszczególne pola tak, aby nie były uwzględniane w wynikach wyszukiwania. W tym celu wystarczy usunąć zaznaczenie danego pola wyboru, na przykład wtedy, gdy pola są używane tylko w wyrażeniach filtrowania. 
* Atrybuty **Filtrowanie**, **Sortowanie** i **Tworzenie aspektów** określają, czy pole może być używane w strukturze nawigacji tworzenia aspektów, filtrowania lub sortowania. 
* **Wyszukiwanie** oznacza, że pole jest uwzględniane podczas wyszukiwania pełnotekstowego. Ciągi można przeszukiwać. Pól liczbowych i logicznych często nie można wyszukiwać. 

Domyślnie kreator skanuje źródło danych w poszukiwaniu unikatowych identyfikatorów jako podstawy dla pola klucza. Ciągi są określane jako możliwe do pobierania i przeszukiwania. Liczby całkowite są określane jako możliwe do pobierania, filtrowania, sortowania i tworzenia aspektów.

  ![Wygenerowany indeks realestate][3]

Kliknij przycisk **OK**, aby utworzyć indeks.

#### <a name="step-3-define-the-indexer"></a>Krok 3. Definiowanie indeksatora
W kreatorze **importu danych** kliknij pozycję **Indeksator** > **Nazwa**, a następnie wpisz nazwę indeksatora. 

Ten obiekt definiuje proces wykonywalny. Możesz go uruchamiać w ramach harmonogramu cyklicznego, ale na razie użyj opcji domyślnej, aby uruchomić indeksatora jednorazowo natychmiast po kliknięciu przycisku **OK**.  

  ![Indeksator realestate][8]

## <a name="check-progress"></a>Sprawdzanie postępu
Aby monitorować importowanie danych, wróć do pulpitu nawigacyjnego usługi, przewiń w dół, a następnie kliknij dwukrotnie kafelek **Indeksatory**, aby otworzyć listę indeksatorów. Na liście powinien być widoczny nowo utworzony indeksator w stanie wskazującym trwające indeksowanie lub pomyślne jego zakończenie wraz z liczbą zindeksowanych dokumentów.

   ![Komunikat o postępie indeksatora][4]

## <a name="query-index"></a> Wykonywanie zapytań względem indeksu
Dysponujesz już indeksem wyszukiwania, którego możesz używać do wykonywania zapytań. **Eksplorator wyszukiwania** jest narzędziem do wykonywania zapytań wbudowanym w portal. Zawiera on pole wyszukiwania, dzięki czemu można zweryfikować, czy wyniki wyszukiwania są zgodne z oczekiwaniami. 

> [!TIP]
> W [klipie wideo z omówieniem usługi Azure Search](https://channel9.msdn.com/Events/Connect/2016/138) następujące kroki są prezentowane od momentu 6:08.
>

1. Kliknij pozycję **Eksplorator wyszukiwania** na pasku poleceń.

   ![Polecenie Eksploratora wyszukiwania][5]

2. Na pasku poleceń kliknij pozycję **Zmień indeks**, aby przełączyć się na zestaw danych *realestate-us-sample*.

   ![Polecenia indeksu i interfejsu API][6]

3. Na pasku poleceń kliknij pozycję **Ustaw wersję interfejsu API**, aby wyświetlić dostępne interfejsy API REST. Interfejsy API w wersji zapoznawczej umożliwiają dostęp do nowych funkcji, które nie zostały jeszcze ogólnie wydane. W przypadku poniższych zapytań użyj ogólnie dostępnej wersji (2016-09-01), chyba że zostanie podana inna informacja. 

    > [!NOTE]
    > [Interfejs API REST usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice/search-documents) i [biblioteka .NET](search-howto-dotnet-sdk.md#core-scenarios) są całkowicie równoważne, ale **Eksplorator wyszukiwania** umożliwia tylko obsługę wywołań REST. Akceptuje on zarówno [prostą składnię zapytań](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), jak i składnię [pełnego analizatora zapytań Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), a także wszystkie parametry wyszukiwania dostępne w ramach operacji [Wyszukiwania dokumentów](https://docs.microsoft.com/rest/api/searchservice/search-documents).
    > 

4. Na pasku wyszukiwania wprowadź poniższe ciągi zapytań, a następnie kliknij pozycję **Wyszukaj**.

  ![Przykład zapytania wyszukiwania][7]

**`search=seattle`**

+ Parametr **search** służy do wprowadzania pełnotekstowego wyszukiwania słów kluczowych — w tym przypadku zwrócone zostaną oferty w King County w stanie Waszyngton zawierające ciąg *Seattle* w dowolnym polu możliwym do przeszukania w dokumencie. 

+ **Eksplorator wyszukiwania** zwraca wyniki w formacie JSON, które są szczegółowe i trudne do odczytania, jeśli dokumenty mają gęstą strukturę. W zależności od dokumentów konieczne może być napisanie kodu obsługującego wyniki wyszukiwania w celu wyodrębnienia ważnych elementów. 

+ Dokumenty składają się ze wszystkich pól oznaczonych jako możliwe do pobierania w indeksie. Aby wyświetlić atrybuty indeksu w portalu, kliknij zestaw danych *realestate-us-sample* w kafelku **Indeksy**.

**`search=seattle&$count=true&$top=100`**

+ Symbol **&** służy do dołączania parametrów wyszukiwania, które mogą być podane w dowolnej kolejności. 

+  Parametr **$count=true** zwraca liczbę wszystkich zwróconych dokumentów. Możesz zweryfikować zapytania filtru, monitorując zmiany raportowane przez parametr **$count=true**. 

+ Parametr **$top=100** powoduje zwrócenie 100 dokumentów o najwyższej randze spośród wszystkich dokumentów. Domyślnie usługa Azure Search zwraca 50 pierwszych najlepszych dopasowań. Tę liczbę możesz zwiększyć lub zmniejszyć za pomocą parametru **$top**.


## <a name="filter-query"></a> Filtrowanie zapytania

Żądania wyszukiwania zawierają filtry, jeśli zostanie dodany parametr **$filter**. 

**`search=seattle&$filter=beds gt 3`**

+ Parametr **$filter** zwraca wyniki spełniające podane kryteria. W tym przypadku są to oferty z liczbą sypialni większą niż 3. 

+ Składnia filtru to konstrukcja OData. Aby uzyskać więcej informacji, zobacz [Filter OData syntax](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) (Składnia filtrowania OData).

## <a name="facet-query"></a> Tworzenie aspektu zapytania

Filtry aspektów są uwzględniane w żądaniach wyszukiwania. Korzystając z parametru facet, można zwrócić zagregowaną liczbę dokumentów, które są zgodne z podaną wartością aspektu. 

**`search=*&facet=city&$top=2`**

+ Parametr **search=*** to puste wyszukiwanie. Puste wyszukiwania umożliwiają znalezienie wszystkiego. Jednym z powodów przesłania pustego zapytania może być potrzeba przeprowadzenia filtrowania lub utworzenia aspektów pełnego zestawu dokumentów. Na przykład może być potrzebne utworzenie aspektów struktury nawigacji składającej się ze wszystkich miast w indeksie.

+  Parametr **facet** zwraca strukturę nawigacji, którą można przekazać do kontrolki interfejsu użytkownika. Zwraca ona kategorie i liczbę elementów. W takim przypadku kategorie są oparte na określonej liczbie miast. Nie istnieje żadna agregacja w usłudze Azure Search, ale możesz przybliżyć się do agregacji za pomocą parametru `facet`, który podaje liczbę dokumentów w poszczególnych kategoriach.

+ Parametr **$top=2** powoduje zwrócenie dwóch dokumentów, co pokazuje, że za pomocą parametru `top` możesz zmniejszyć lub zwiększyć liczbę wyników.

**`search=seattle&facet=beds`**

+ To zapytanie to aspekt liczby sypialni w wyszukiwaniu tekstowym dla kategorii *Seattle*. Termin *beds* można określić jako aspekt, ponieważ w indeksie jest oznaczony jako pole z możliwością pobierania, filtrowania i tworzenia aspektów, a wartości, które zawiera (liczbowe od 1 do 5), są odpowiednie na potrzeby kategoryzowania ofert w grupach (oferty z 3 i 4 sypialniami). 

+ Aspekty mogą być tworzone tylko na podstawie pól z możliwością filtrowania. W wynikach mogą być zwracane tylko pola z możliwością pobierania.

## <a name="highlight-query"></a> Dodawanie wyróżnienia

Wyróżnianie trafień oznacza dodawanie formatowania do tekstu zgodnego ze słowem kluczowym w przypadku znalezienia dopasowań w określonym polu. Jeśli termin wyszukiwania jest głęboko ukryty w opisie, możliwe jest dodanie wyróżnienia trafień w celu jego łatwiejszego zauważenia. 

**`search=granite countertops&highlight=description`**

+ W tym przykładzie sformatowana fraza *granite countertops* jest lepiej widoczna w polu opisu.

**`search=mice&highlight=description`**

+ Wyszukiwanie pełnotekstowe umożliwia znalezienie wyrazów z podobną semantyką. W takim przypadku wyniki wyszukiwania zawierają wyróżniony tekst „mouse” (mysz) dla domów z plagą myszy zwracanych w rezultacie wyszukiwania słowa kluczowego „mice” (myszy). Różne rodzaje tego samego wyrazu mogą być wyświetlane w wynikach z powodu przeprowadzenia analizy lingwistycznej. 

+ Usługa Azure Search obsługuje 56 analizatorów — zarówno oprogramowania Lucene, jak i firmy Microsoft. W usłudze Azure Search domyślnie używany jest standardowy analizator Lucene. 

## <a name="fuzzy-search"></a> Korzystanie z wyszukiwania rozmytego

Wyrazy z błędami pisowni, na przykład *samamish* zamiast równiny Sammamish w pobliżu Seattle, spowodują, że w typowym wyszukiwaniu nie zostaną zwrócone żadne dopasowania. Aby obsłużyć literówki, można użyć wyszukiwania rozmytego, które zostało opisane w następnym przykładzie.

**`search=samamish`**

+ W tym przykładzie błędnie napisano nazwę obszaru w pobliżu Seattle.

**`search=samamish~&queryType=full`**

+ Wyszukiwanie rozmyte jest włączane po określeniu symbolu **~** i użyciu pełnego analizatora zapytań, który interpretuje i poprawnie analizuje składnię **~**. 

+ Wyszukiwanie rozmyte jest dostępne po włączeniu pełnego analizatora zapytań, co następuje w chwili ustawienia parametru **queryType=full**. Aby uzyskać więcej informacji o scenariuszach zapytań dostępnych przy użyciu pełnego analizatora zapytań, zobacz [Lucene query syntax in Azure Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) (Składnia zapytań Lucene w usłudze Azure Search).

+ Gdy parametr **queryType** jest nieokreślony, używany jest domyślny prosty analizator zapytań. Prosty analizator zapytań jest szybszy, ale jeśli wymagane jest użycie wyszukiwania rozmytego, wyrażeń regularnych, wyszukiwania w sąsiedztwie lub innych zaawansowanych typów zapytań, konieczne będzie korzystanie z pełnej składni. 

## <a name="geo-search"></a> Testowanie wyszukiwania geoprzestrzennego

Wyszukiwanie geoprzestrzenne jest obsługiwane za pośrednictwem [typu danych edm.GeographyPoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) względem pola zawierającego współrzędne. Wyszukiwanie geograficzne jest rodzajem filtru określonego w artykule [Filter OData syntax](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) (Składnia filtrowania OData). 

**`search=*&$count=true&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5`**

+ To zapytanie przykładowe służy do przefiltrowania wszystkich wyników danych pozycyjnych, które znajdują się w odległości mniejszej niż 5 km od danego punktu (określonego jako szerokość i długość geograficzna). Dodając parametr **$count**, możesz sprawdzić, ile wyników zostanie zwróconych, gdy zmienisz odległość lub współrzędne. 

+ Wyszukiwanie geoprzestrzenne jest przydatne, jeśli aplikacja wyszukiwania ma funkcję „znajdź w pobliżu” lub używa nawigacji mapy. Nie jest to jednak wyszukiwanie pełnotekstowe. Jeśli istnieją wymagania użytkownika dotyczące wyszukiwania w mieście lub kraju według nazwy, oprócz współrzędnych dodaj pola zawierające nazwy miasta lub kraju.

## <a name="next-steps"></a>Następne kroki

+ Zmodyfikuj dowolny właśnie utworzony obiekt. Po jednokrotnym uruchomieniu kreatora możesz wrócić i wyświetlić lub zmodyfikować poszczególne składniki: indeks, indeksator lub źródło danych. Niektóre zmiany, takie jak zmiana typu danych pola, są niedozwolone w indeksie, ale większość właściwości i ustawień można modyfikować.

  Aby wyświetlić poszczególne składniki, kliknij kafelek **Indeks**, **Indeksator** lub **Źródła danych** na pulpicie nawigacyjnym w celu wyświetlenia listy istniejących obiektów. Aby dowiedzieć się więcej na temat operacji edytowania indeksu, które nie wymagają ponownej kompilacji, zobacz [Update Index (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/update-index) (Aktualizowanie indeksu — interfejs API REST usługi Azure Search).

+ Wypróbuj te narzędzia i kroki z innymi źródłami danych. Przykładowy zestaw danych, `realestate-us-sample`, pochodzi z bazy danych Azure SQL Database, którą można przeszukiwać za pomocą usługi Azure Search. Oprócz usługi Azure SQL Database usługa Azure Search może przeszukiwać płaskie struktury danych w usługach Azure Table Storage, Blob Storage i Azure Cosmos DB oraz w programie SQL Server na maszynie wirtualnej platformy Azure. Na ich podstawie jest też tworzony indeks. Wszystkie te źródła danych są obsługiwane w kreatorze. W kodzie można w łatwy sposób wypełnić indeks przy użyciu *indeksatora*.

+ Wszystkie inne źródła danych niebędące indeksatorami są obsługiwane za pośrednictwem modelu wypychania, w ramach którego za pomocą kodu do indeksu wypychane są nowe i zmienione zestawy wierszy w formacie JSON. Aby uzyskać więcej informacji, zobacz [Add, update, or delete documents in Azure Search](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) (Dodawanie, aktualizowanie lub usuwanie dokumentów w usłudze Azure Search).

Aby dowiedzieć się więcej o innych funkcjach wymienionych w tym artykule, odwiedź następujące linki:

* [Indexers overview](search-indexer-overview.md) (Omówienie indeksatorów)
* [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) (Tworzenie indeksu) — zawiera szczegółowe wyjaśnienie atrybutów indeksu
* [Eksplorator wyszukiwania](search-explorer.md)
* [Search Documents](https://docs.microsoft.com/rest/api/searchservice/search-documents) (Wyszukiwanie dokumentów) — zawiera przykłady składni zapytania


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