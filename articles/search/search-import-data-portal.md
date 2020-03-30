---
title: Importowanie danych do indeksu wyszukiwania przy użyciu witryny Azure portal
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak za pomocą Kreatora importowania danych w witrynie Azure portal do indeksowania danych platformy Azure z usługi Cosmos DB, magazynu obiektów blob, magazynu tabel, bazy danych SQL i programu SQL Server na maszynach wirtualnych platformy Azure.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0ed2bd7f1e03d8d5fa11f7e76010d087605f0fe1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460700"
---
# <a name="import-data-wizard-for-azure-cognitive-search"></a>Kreator importu danych dla usługi Azure Cognitive Search

Portal Azure zawiera **kreatora importu danych** na pulpicie nawigacyjnym usługi Azure Cognitive Search do tworzenia prototypów i ładowania indeksu. W tym artykule opisano zalety i ograniczenia korzystania z kreatora, dane wejściowe i wyjściowe oraz niektóre informacje o użyciu. Aby uzyskać praktyczne wskazówki dotyczące przechodzenia przez kreatora przy użyciu wbudowanych przykładowych danych, zobacz [Tworzenie indeksu usługi Azure Cognitive Search przy użyciu](search-get-started-portal.md) przewodnika Szybki start w portalu Azure.

Operacje wykonywane przez ten kreator obejmują:

1 - Połącz się z obsługiwanym źródłem danych platformy Azure.

2 - Utwórz schemat indeksu, wywnioskowane przez próbkowanie danych źródłowych.

3 - Opcjonalnie dodaj wzbogacenia AI, aby wyodrębnić lub wygenerować zawartość i strukturę.

4 - Uruchom kreatora, aby tworzyć obiekty, importować dane, ustawiać harmonogram i inne opcje konfiguracji.

Kreator wyprowadza wiele obiektów zapisanych w usłudze wyszukiwania, do których można uzyskać dostęp programowo lub w innych narzędziach.

## <a name="advantages-and-limitations"></a>Zalety i ograniczenia

Przed napisaniem kodu można użyć kreatora do prototypowania i testowania weryfikacji koncepcji. Kreator łączy się z zewnętrznymi źródłami danych, próbkuje dane w celu utworzenia indeksu początkowego, a następnie importuje dane jako dokumenty JSON do indeksu w usłudze Azure Cognitive Search. 

Próbkowanie jest procesem, w którym schemat indeksu jest wywnioskowany i ma pewne ograniczenia. Podczas tworzenia źródła danych kreator wybiera próbkę dokumentów, aby zdecydować, które kolumny są częścią źródła danych. Nie wszystkie pliki są odczytywane, ponieważ może to potrwać wiele godzin w przypadku bardzo dużych źródeł danych. Biorąc pod uwagę wybór dokumentów, metadane źródłowe, takie jak nazwa pola lub typ, jest używany do tworzenia kolekcji pól w schemacie indeksu. W zależności od złożoności danych źródłowych może być konieczne edytowanie początkowego schematu w celu uzyskania dokładności lub rozszerzenie go w celu uzupełnienia. Zmiany można wprowadzić w linii na stronie definicji indeksu.

Ogólnie rzecz biorąc zalety korzystania z kreatora są jasne: tak długo, jak wymagania są spełnione, można prototyp indeksu queryable w ciągu kilku minut. Niektóre z zawiłości indeksowania, takie jak dostarczanie danych jako dokumentów JSON, są obsługiwane przez kreatora.

Znane ograniczenia są podsumowane w następujący sposób:

+ Kreator nie obsługuje iteracji ani ponownego użycia. Każdy przebieg kreatora tworzy nowy indeks, skillset i konfiguracji indeksatora. Tylko źródła danych mogą być utrwalone i ponownie w ramach kreatora. Aby edytować lub uściślić inne obiekty, należy użyć interfejsów API REST lub .NET SDK do pobierania i modyfikowania struktur.

+ Zawartość źródłowa musi znajdować się w obsługiwanym źródle danych platformy Azure.

+ Próbkowanie znajduje się nad podzbiorem danych źródłowych. W przypadku dużych źródeł danych kreator może przegapić pola. Może być konieczne rozszerzenie schematu lub skorygowanie wywnioskowane typy danych, jeśli próbkowanie jest niewystarczające.

+ Wzbogacanie SI, ujawnione w portalu, jest ograniczone do kilku wbudowanych umiejętności. 

+ [Magazyn wiedzy](knowledge-store-concept-intro.md), który może być utworzony przez kreatora, jest ograniczony do kilku domyślnych projekcji. Jeśli chcesz zapisać wzbogacone dokumenty utworzone przez kreatora, kontener obiektów blob i tabele są wyposażone w domyślne nazwy i strukturę.

<a name="data-source-inputs"></a>

## <a name="data-source-input"></a>Wprowadzanie źródła danych

Kreator **importu danych** łączy się z zewnętrznym źródłem danych przy użyciu wewnętrznej logiki dostarczanej przez indeksatory usługi Azure Cognitive Search, które są wyposażone do próbkowania źródła, odczytu metadanych, pękania dokumentów do odczytu zawartości i struktury oraz serializacji zawartości jako JSON do późniejszego importowania do usługi Azure Cognitive Search.

Można importować tylko z pojedynczej tabeli, widoku bazy danych lub równoważnej struktury danych, jednak struktura może zawierać hierarchiczne lub zagnieżdżone podstruktury. Aby uzyskać więcej informacji, zobacz [Jak modelować typy złożone](search-howto-complex-data-types.md).

Należy utworzyć tę pojedynczą tabelę lub widok przed uruchomieniem kreatora i musi zawierać zawartość. Z oczywistych powodów nie ma sensu uruchamiać **kreatora importu danych** na pustym źródle danych.

|  Wybór | Opis |
| ---------- | ----------- |
| **Istniejące źródło danych** |Jeśli masz już indeksatory zdefiniowane w usłudze wyszukiwania, może mieć istniejącą definicję źródła danych, które można ponownie użyć. W usłudze Azure Cognitive Search obiekty źródła danych są używane tylko przez indeksatory. Obiekt źródła danych można utworzyć programowo lub za pomocą **Kreatora importu danych** i użyć go ponownie w razie potrzeby.|
| **Próbki**| Usługa Azure Cognitive Search udostępnia dwa wbudowane przykładowe źródła danych, które są używane w samouczkach i przewodnikach Szybki start: bazę danych SQL nieruchomości i bazę danych hoteli hostowanych w usłudze Cosmos DB. Aby uzyskać przejście na podstawie przykładu Hotele, zobacz [Tworzenie indeksu w przewodniku](search-get-started-portal.md) Szybki start portalu Azure. |
| [**Azure SQL Database**](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) |Nazwę usługi, poświadczenia użytkownika z uprawnieniem do odczytu bazy danych i nazwę bazy danych można określić na stronie lub przy użyciu parametrów połączenia ADO.NET. Wybierz opcję parametrów połączenia, aby wyświetlić lub dostosować właściwości. <br/><br/>Na stronie należy określić tabelę lub widok zawierające zestaw wierszy. Ta opcja jest dostępna po udanym nawiązaniu połączenia. Pojawia się wtedy lista rozwijana, z której można dokonać wyboru.|
| **Program SQL Server na maszynie wirtualnej platformy Azure** |Określ w pełni kwalifikowaną nazwę usługi, identyfikator użytkownika i hasło oraz bazę danych jako parametry połączenia. Aby użyć tego źródła danych, należy wcześniej zainstalować w magazynie lokalnym certyfikat szyfrujący połączenie. Aby uzyskać instrukcje, zobacz [połączenie maszyny Wirtualnej SQL z usługą Azure Cognitive Search](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md). <br/><br/>Na stronie należy określić tabelę lub widok zawierające zestaw wierszy. Ta opcja jest dostępna po udanym nawiązaniu połączenia. Pojawia się wtedy lista rozwijana, z której można dokonać wyboru. |
| [**Azure Cosmos DB**](search-howto-index-cosmosdb.md)|Wymagane jest konto, baza danych i kolekcja. Wszystkie dokumenty w kolekcji zostaną uwzględnione w indeksie. Można zdefiniować kwerendę, aby spłaszczyć lub filtrować zestaw wierszy lub pozostawić kwerendę pustą. Kwerenda nie jest wymagana w tym kreatorze.|
| [**Magazyn obiektów blob platformy Azure**](search-howto-indexing-azure-blob-storage.md) |Wymagane jest miedzy innymi konto magazynu i kontener. Opcjonalnie, jeśli nazwa obiektu blob jest zgodna z konwencją nazw wirtualnych do celów grupowania, można określić część nazwy oznaczającą katalog wirtualny jako folder w kontenerze. Więcej informacji zawiera artykuł [Indexing Blob Storage](search-howto-indexing-azure-blob-storage.md) (Indeksowanie w usłudze Blob Storage). |
| [**Azure Table Storage**](search-howto-indexing-azure-tables.md) |Wymagane jest miedzy innymi konto magazynu i nazwa tabeli. Opcjonalnie można określić zapytanie w celu pobrania podzbioru tabel. Więcej informacji zawiera artykuł [Indexing Table Storage](search-howto-indexing-azure-tables.md) (Indeksowanie w usłudze Table Storage). |

## <a name="wizard-output"></a>Wyjście kreatora

Za kulisami kreator tworzy, konfiguruje i wywołuje następujące obiekty. Po uruchomieniu kreatora można znaleźć jego dane wyjściowe na stronach portalu. Na stronie Przegląd usługi znajduje się lista indeksów, indeksatorów, źródeł danych i zestawów umiejętności. Definicje indeksów można przeglądać w pełnej wersji JSON w portalu. W przypadku innych definicji można użyć [interfejsu API REST,](https://docs.microsoft.com/rest/api/searchservice/) aby uzyskać określone obiekty.

| Obiekt | Opis | 
|--------|-------------|
| [Źródło danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Utrzymuje informacje o połączeniu z danymi źródłowymi, w tym poświadczeniami. Obiekt źródła danych jest używany wyłącznie z indeksatorami. | 
| [Index](https://docs.microsoft.com/rest/api/searchservice/create-index) | Struktura danych fizycznych używana do wyszukiwania pełnotekstowych i innych zapytań. | 
| [Skillset](https://docs.microsoft.com/rest/api/searchservice/create-skillset) | Kompletny zestaw instrukcji dotyczących manipulowania, przekształcania i kształtowania zawartości, w tym analizowania i wyodrębniania informacji z plików obrazów. Z wyjątkiem bardzo prostych i ograniczonych struktur, zawiera odwołanie do zasobu usług Cognitive Services, który zapewnia wzbogacenie. Opcjonalnie może również zawierać definicję magazynu wiedzy.  | 
| [Indeksator](https://docs.microsoft.com/rest/api/searchservice/create-indexer)  | Obiekt konfiguracji określający źródło danych, indeks docelowy, opcjonalny zestaw umiejętności, opcjonalny harmonogram i opcjonalne ustawienia konfiguracji do przekazywania błędów i kodowania base-64. |


## <a name="how-to-start-the-wizard"></a>Jak uruchomić kreatora

Kreator importu danych jest uruchamiany z paska poleceń na stronie Przegląd usługi.

1. W [witrynie Azure portal](https://portal.azure.com)otwórz stronę usługi wyszukiwania z pulpitu nawigacyjnego lub [znajdź usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na liście usług.

2. Na stronie przegląd usług u góry kliknij pozycję **Importuj dane**.

   ![Polecenie Importuj dane w portalu](./media/search-import-data-portal/import-data-cmd2.png "Uruchamianie kreatora importu danych")

Można również uruchomić **importowanie danych** z innych usług platformy Azure, w tym usługi Azure Cosmos DB, usługi Azure SQL Database i magazynu obiektów Blob platformy Azure. **Poszukaj dodaj usługi Azure Cognitive Search** w lewym okienku nawigacji na stronie omówienia usługi.

<a name="index-definition"></a>

## <a name="how-to-edit-or-finish-an-index-schema-in-the-wizard"></a>Jak edytować lub kończyć schemat indeksu w kreatorze

Kreator generuje niekompletny indeks, który zostanie wypełniony dokumentami uzyskanymi z wejściowego źródła danych. Dla indeksu funkcjonalnego, upewnij się, że masz zdefiniowane następujące elementy.

1. Czy lista pól jest kompletna? Dodaj nowe pola, które nie odebrano próbkowania, i usuń wszystkie, które nie dodają wartości do środowiska wyszukiwania lub które nie będą używane w [wyrażeniu filtru](search-query-odata-filter.md) lub [profilu oceniania](index-add-scoring-profiles.md).

1. Czy typ danych jest odpowiedni dla danych przychodzących? Usługa Azure Cognitive Search obsługuje [typy danych modelu danych jednostki (EDM).](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) W przypadku danych SQL platformy Azure istnieje [wykres mapowania,](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#TypeMapping) który określa równoważne wartości. Aby uzyskać więcej informacji na ten temat, zobacz [Mapowania pól i przekształcenia](search-indexer-field-mappings.md).

1. Czy masz jedno pole, które może służyć jako *klucz?* To pole musi być ciągiem Edm.string i musi jednoznacznie identyfikować dokument. W przypadku danych relacyjnych może być mapowany na klucz podstawowy. W przypadku obiektów blob `metadata-storage-path`może to być plik . Jeśli wartości pól zawierają spacje lub kreski, należy ustawić opcję **Klucz kodowania bazy-64** w kroku **Utwórz indeksator** w obszarze **Opcje zaawansowane**, aby pominąć sprawdzanie poprawności tych znaków.

1. Ustaw atrybuty, aby określić, w jaki sposób to pole jest używane w indeksie. 

   Poświęć trochę czasu na ten krok, ponieważ atrybuty określają fizyczne wyrażenie pól w indeksie. Jeśli chcesz zmienić atrybuty później, nawet programowo, prawie zawsze trzeba upuścić i odbudować indeks. Podstawowe atrybuty, takie jak **przeszukiwalne** i **możliwe do pobrania,** mają [nieznaczny wpływ na pamięć masową.](search-what-is-an-index.md#index-size) Włączanie filtrów i używanie sugestów zwiększa wymagania dotyczące magazynowania. 
   
   + **Przeszukiwanie** umożliwia wyszukiwanie pełnotekstowe. Każde pole używane w kwerendach formularzy swobodnych lub w wyrażeniach kwerend musi mieć ten atrybut. Odwrócone indeksy są tworzone dla każdego pola, które można **oznaczyć**jako przeszukiwalne .

   + **Pobieranie** zwraca pole w wynikach wyszukiwania. Każde pole, które udostępnia zawartość do wyników wyszukiwania, musi mieć ten atrybut. Ustawienie tego pola nie powoduje znaczącego wpływu na rozmiar indeksu.

   + **Filtrowanie** umożliwia odwoływanie się do pola w wyrażeniach filtru. Każde pole używane w wyrażeniu **$filter** musi mieć ten atrybut. Wyrażenia filtrowania służą dokładnym dopasowaniom. Ponieważ ciągi tekstowe pozostają nienaruszone, dodatkowy magazyn jest wymagany do uwzględnienia dosłowneja zawartość.

   + **Facetable** umożliwia pole do nawigacji aspektowej. Tylko pola oznaczone jako **filtrowalne** mogą być oznaczone jako **Facetable**.

   + **Sortowanie** umożliwia użycie pola w sortowni. Każde pole używane w wyrażeniu **$Orderby** musi mieć ten atrybut.

1. Czy potrzebujesz [analizy leksykalne?](search-lucene-query-architecture.md#stage-2-lexical-analysis) W przypadku pól Edm.string, które można **przeszukiwać,** można ustawić **analizator,** jeśli chcesz indeksować i wyszukiwać w języku. 

   Wartość domyślna to *Standardowy Lucene,* ale można wybrać *język angielski firmy Microsoft,* jeśli chcesz użyć analizatora firmy Microsoft do zaawansowanego przetwarzania leksykalne, takiego jak rozwiązywanie nieregularnych formularzy rzeczowników i czasowników. Tylko analizatory języka można określić w portalu. Za pomocą analizatora niestandardowego lub analizatora innego języka, takich jak słowo kluczowe, wzorzec i tak dalej, muszą być wykonywane programowo. Aby uzyskać więcej informacji na temat analizatorów, zobacz [Dodawanie analizatorów języka](search-language-support.md).

1. Czy potrzebujesz funkcji typeahead w postaci autouzupełniania lub sugerowanych wyników? Zaznacz pole wyboru **Sugestator,** aby włączyć [sugestie kwerend typu w nagłówku i autouzupełnianie](index-add-suggesters.md) wybranych pól. Sugestywnie dodać do liczby tokenizowanych terminów w indeksie, a tym samym zużywają więcej miejsca.


## <a name="next-steps"></a>Następne kroki

Najlepszym sposobem, aby zrozumieć korzyści i ograniczenia kreatora jest krok przez niego. Poniższy przewodnik Szybki start prowadzi użytkownika przez każdy krok.

> [!div class="nextstepaction"]
> [Tworzenie indeksu usługi Azure Cognitive Search przy użyciu portalu Azure](search-get-started-portal.md)