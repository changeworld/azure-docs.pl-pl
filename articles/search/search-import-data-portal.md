---
title: Importowanie danych do indeksu wyszukiwania przy użyciu witryny Azure portal — usługa Azure Search
description: Dowiedz się, jak używać Kreatora importu danych w witrynie Azure portal Przeszukuj dane platformy Azure z usługi Cosmos DB, magazynu obiektów Blob, usługi table storage, SQL Database i programu SQL Server na maszynach wirtualnych platformy Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: a0eefe38fdffd04bb95826f960771bd6430ea687
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024780"
---
# <a name="import-data-wizard-for-azure-search"></a>Kreator importu danych dla usługi Azure Search

W witrynie Azure Portal na pulpicie nawigacyjnym usługi Azure Search znajduje się kreator **Importuj dane**, który umożliwia ładowanie danych do indeksu. W tle, Kreator konfiguruje i wywołuje *źródła danych*, *indeksu*, i *indeksatora* -automatyzując kilka kroków procesu indeksowania: 

* Łączy się z zewnętrznym źródłem danych w tej samej subskrypcji platformy Azure.
* Opcjonalnie integruje przetwarzania optyczne rozpoznawanie znaków lub języka naturalnego do wyodrębniania tekstu z danych bez struktury.
* Powoduje utworzenie indeksu na podstawie próbkowania danych i metadanych z zewnętrznym źródłem danych.
* Przeszukuje źródło danych dla zawartość do przeszukiwania, serializacja i ładowanie dokumentów JSON do indeksu.

Kreator nie może nawiązać połączenie z wstępnie zdefiniowany indeks lub uruchamianie istniejącego indeksatora, ale w kreatorze, można skonfigurować nowego indeksu i indeksatora do obsługi struktury i zachowań, które są potrzebne.

Dopiero zaczynasz korzystać z usługi Azure Search? Krok po kroku [Szybki Start: Importować, indeksu i wykonywać zapytania za pomocą narzędzia portalu](search-get-started-portal.md) będą mogli ją testować importowania i indeksowanie przy użyciu **importowania danych** i zestaw danych próbka wbudowanych nieruchomości.

## <a name="start-importing-data"></a>Rozpocznij importowanie danych

W tej sekcji wyjaśniono, jak uruchomić kreatora i zawiera ogólne omówienie każdego kroku.

1. W [witryny Azure portal](https://portal.azure.com), otwórz stronę usługi wyszukiwanie z pulpitu nawigacyjnego lub [Znajdź swoją usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na liście usług.

2. Na stronie Przegląd usługi u góry kliknij **importowania danych**.

   ![Polecenie importu danych w portalu](./media/search-import-data-portal/import-data-cmd2.png "uruchomić Kreatora importu danych")

   > [!NOTE]
   > Możesz uruchomić **importowania danych** z innymi usługami platformy Azure, w tym usługi Azure Cosmos DB, Azure SQL Database i Azure Blob storage. Wyszukaj **Dodaj usługę Azure Search** w okienku nawigacji po lewej stronie, na stronie Przegląd usługi.

3. W kreatorze jest otwierany **Połącz z danymi**, którym można wybrać zewnętrznego źródła danych na potrzeby importowania. Istnieje kilka sposobów, aby ten krok można dowiedzieć się, dlatego należy przeczytać [dane wejściowe źródła danych](#data-source-inputs) sekcji, aby uzyskać więcej informacji.

   ![Kreator importu danych w portalu](./media/search-import-data-portal/import-data-wizard-startup.png "Kreatora importu danych dla usługi Azure Search")

4. Następnym ekranem jest **wyszukiwania kognitywnego Dodaj**, w przypadku, gdy chcesz dołączyć optyczne rozpoznawanie znaków (OCR) tekstu w plikach obrazu lub analiza tekstu względem danych bez struktury. Algorytmy sztucznej Inteligencji z usług Cognitive Services są pobierane dla tego zadania. Istnieją dwie części do tego kroku:
  
   Po pierwsze, [dołączenia zasobu usług Cognitive Services](cognitive-search-attach-cognitive-services.md) do usługi Azure Search zestawu umiejętności.
  
   Wybierz drugie, które wzbogacenia sztucznej Inteligencji, które mają zostać objęte zestawu umiejętności. Pokaz wskazówki, zobacz ten [Szybki Start](cognitive-search-quickstart-blob.md).

   Jeśli chcesz zaimportować dane, Pomiń ten krok i przejść bezpośrednio do definicji indeksu.

5. Następnym ekranem jest **Dostosuj indeks docelowy**, gdzie możesz zaakceptować lub zmodyfikować schemat indeksu, znajdujące się w kreatorze. Kreator wnioskuje pola i typy danych, dane próbkowania i podczas odczytywania metadanych z zewnętrznego źródła danych.

   Dla każdego pola [Sprawdź atrybuty indeksu](#index-definition) umożliwiające konkretne zachowania. Jeśli nie zaznaczysz wszelkie atrybuty indeksu nie będzie można używać. 

6. Następnym ekranem jest **Utwórz indeksator**, który jest wynikiem tego kreatora. Indeksator jest przeszukiwarką, która wyodrębnia dane z możliwością wyszukiwania i metadane ze źródła zewnętrznego danych platformy Azure. Wybranie źródła danych i dołączanie dokładniejsze (opcjonalnie) i indeksu, już został konfigurujesz indeksatora czasie poruszania się przez kolejne kroki kreatora.

   Nadaj nazwę indeksatora, a następnie kliknij przycisk **przesyłania** aby rozpocząć proces importowania. 

Indeksowanie w portalu, klikając indeksatora w można monitorować **indeksatory** listy. W miarę ładowania dokumentów liczba dokumentów dla zdefiniowanego indeksu będzie rosnąć. Czasem do odzwierciedlenia najnowszych aktualizacji na stronie portalu może minąć kilka minut.

Indeks jest gotowa do obsługi zapytań, zaraz po załadowaniu pierwszy dokument. Możesz użyć [Eksploratora wyszukiwania](search-explorer.md) dla tego zadania.

<a name="data-source-inputs"></a>

## <a name="data-source-inputs"></a>Dane wejściowe źródła danych

**Importowania danych** Kreator tworzy obiekt źródła danych trwałych, określając informacje o połączeniu ze źródłem danych zewnętrznych. Obiekt źródła danych jest używane wyłącznie w przypadku [indeksatory](search-indexer-overview.md) i mogą być tworzone dla następujących źródeł danych: 

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Usługa Azure Table Storage](search-howto-indexing-azure-tables.md) (nieobsługiwane w przypadku [wyszukiwania kognitywnego](cognitive-search-concept-intro.md) potoków)

Można importować tylko z pojedynczej tabeli, widoku bazy danych lub równoważnej struktury danych, ale struktura może zawierać podstruktury hierarchiczne lub zagnieżdżony. Aby uzyskać więcej informacji, zobacz [jak modelowanie złożonych typów](search-howto-complex-data-types.md).

Ta struktura danych należy utworzyć przed uruchomieniem kreatora, i może zawierać zawartości. Nie uruchamiaj **importowania danych** kreatora w źródle danych puste.

|  Wybór | Opis |
| ---------- | ----------- |
| **Istniejące źródło danych** |Jeśli masz już zdefiniowane indeksatory w usłudze wyszukiwania, możesz użyć istniejącej definicji źródła danych na potrzeby innego importu. W usłudze Azure Search obiekty źródła danych są używane tylko przez indeksatorów. Programistyczne tworzenie obiektu źródła danych lub za pomocą **importowania danych** kreatora.|
| **Przykłady**| Usługa Azure Search obsługuje bezpłatne globalnego Azure SQL database, można użyć, aby dowiedzieć się więcej o żądaniach importowania i zapytania w usłudze Azure Search. Zobacz [Szybki start: Importować, indeksu i wykonywać zapytania za pomocą narzędzia portalu](search-get-started-portal.md) przewodnik. |
| **Azure SQL Database** |Nazwę usługi, poświadczenia użytkownika z uprawnieniem do odczytu bazy danych i nazwę bazy danych można określić na stronie lub przy użyciu parametrów połączenia ADO.NET. Wybierz opcję parametrów połączenia, aby wyświetlić lub dostosować właściwości. <br/><br/>Na stronie należy określić tabelę lub widok zawierające zestaw wierszy. Ta opcja jest dostępna po udanym nawiązaniu połączenia. Pojawia się wtedy lista rozwijana, z której można dokonać wyboru. |
| **Program SQL Server na maszynie wirtualnej platformy Azure** |Określ w pełni kwalifikowaną nazwę, identyfikator użytkownika i hasło i bazy danych jako parametry połączenia. Aby użyć tego źródła danych, należy wcześniej zainstalować w magazynie lokalnym certyfikat szyfrujący połączenie. Aby uzyskać instrukcje, zobacz [SQL VM connection to Azure Search](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md) (Połączenie maszyny wirtualnej bazy danych SQL z usługą Azure Search). <br/><br/>Na stronie należy określić tabelę lub widok zawierające zestaw wierszy. Ta opcja jest dostępna po udanym nawiązaniu połączenia. Pojawia się wtedy lista rozwijana, z której można dokonać wyboru. |
| **Cosmos DB** |Wymagane jest konto, baza danych i kolekcja. Wszystkie dokumenty w kolekcji zostaną uwzględnione w indeksie. Można zdefiniować zapytanie w celu spłaszczenia lub filtrowania zestawu wierszy, lub pozostaw puste zapytanie. Zapytanie nie jest wymagane w tym kreatorze.|
| **Azure Blob Storage** |Wymagane jest miedzy innymi konto magazynu i kontener. Opcjonalnie, jeśli nazwa obiektu blob jest zgodna z konwencją nazw wirtualnych do celów grupowania, można określić część nazwy oznaczającą katalog wirtualny jako folder w kontenerze. Więcej informacji zawiera artykuł [Indexing Blob Storage](search-howto-indexing-azure-blob-storage.md) (Indeksowanie w usłudze Blob Storage). |
| **Azure Table Storage** |Wymagane jest miedzy innymi konto magazynu i nazwa tabeli. Opcjonalnie można określić zapytanie w celu pobrania podzbioru tabel. Więcej informacji zawiera artykuł [Indexing Table Storage](search-howto-indexing-azure-tables.md) (Indeksowanie w usłudze Table Storage). |


<a name="index-definition"></a>

## <a name="index-attributes"></a>Atrybuty indeksu

**Importowania danych** kreator wygeneruje indeksu, który zostanie wypełniony dokumentów uzyskany ze źródła danych wejściowych. 

Dla indeksu funkcjonalności upewnij się, że masz następujące elementy, które są zdefiniowane.

1. Jedno pole musi być oznaczona jako **klucz**, który jest używany do jednoznacznego zidentyfikowania poszczególnych dokumentów. **Klucz** musi być *Edm.string*. 

   Jeśli wartości pól zawierają spacje lub kreski, musisz ustawić **klucz kodowania Base-64** opcji **Utwórz indeksator** krok w obszarze **zaawansowane opcje**, aby pominąć sprawdzenie poprawności tych znaków.

1. Ustaw atrybuty indeksu dla każdego pola. Jeśli wybierzesz żadnych atrybutów, indeksu jest zasadniczo pusta, z wyjątkiem wymagane pola klucza. Jako minimum wybierz co najmniej jeden z tych atrybutów dla każdego pola.
   
   + **Pobieranie** zwracanie pola w wynikach wyszukiwania. Każde pole, które zawiera materiały do wyników wyszukiwania musi mieć tego atrybutu. Ustawienie to pole nie ma znacznego wpływu na rozmiar indeksu.
   + **Można filtrować** umożliwia pola można odwoływać się w wyrażeniach filtru. Każde pole używane w **$filter** wyrażenie musi mieć tego atrybutu. Wyrażenia filtru są dokładne dopasowania. Ponieważ ciągi tekstowe pozostaną nienaruszone, dodatkowy magazyn jest wymagany do uwzględnienia verbatim zawartości.
   + **Można wyszukiwać** umożliwia wyszukiwanie pełnotekstowe. Każde pole używane w dowolnej postaci zapytania lub w wyrażeniach zapytań musi mieć tego atrybutu. Odwrócony indeksy są tworzone dla każdego pola, które oznaczyć jako **z możliwością wyszukiwania**.

1. Opcjonalnie można ustawić tych atrybutów, zgodnie z potrzebami:

   + **Sortowanie** umożliwia pola, które ma być używany podczas sortowania. Każde pole używane w **$Orderby** wyrażenie musi mieć tego atrybutu.
   + **Tworzenie aspektów** umożliwia używanie pola w nawigacji aspektowej. Tylko pola również oznaczone jako **możliwość filtrowania** może być oznaczona jako **aspektów**.

1. Ustaw **analizatora** chcącym języka rozszerzone indeksowanie i wykonywanie zapytań. Wartość domyślna to *standardowa Lucene* , ale można wybrać *Microsoft English* Jeśli chcesz użyć analizatora firmy Microsoft dla zaawansowanego przetwarzania leksykalne, takich jak rozpoznawanie nieregularne formularzy rzeczownika i zlecenie.

   + Wybierz **z możliwością wyszukiwania** umożliwiające **analizatora** listy.
   + Wybierz analizator podany na liście. 
   
   Obecnie można określić tylko analizatory języka. Skorzystanie z analizatorów niestandardowych lub innych niż analizatory języka, takich jak analizatory słów kluczowych, wzorców itp., będzie wymagać kodu. Aby uzyskać więcej informacji o analizatorach zobacz [Tworzenie indeksu dla dokumentów w wielu językach](search-language-support.md).

1. Wybierz **Sugestora** pole wyboru, aby włączyć podpowiedzi dla zapytania z podpowiedziami pojawiającymi na wybrane pola.


## <a name="next-steps"></a>Kolejne kroki
Przejrzyj następujące linki, aby dowiedzieć się więcej o indeksatorach:

* [Indeksowanie w usłudze Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Indexing Azure Cosmos DB](search-howto-index-cosmosdb.md) (Indeksowanie w usłudze Azure Cosmos DB)
* [Indeksowanie w usłudze Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Indeksowanie w usłudze Table Storage](search-howto-indexing-azure-tables.md)