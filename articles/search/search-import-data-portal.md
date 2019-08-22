---
title: Importowanie danych do indeksu wyszukiwania przy użyciu Azure Portal-Azure Search
description: Dowiedz się, jak za pomocą Kreatora importu danych w Azure Portal przeszukiwać dane platformy Azure z Cosmos DB, BLOB Storage, Table Storage, SQL Database i SQL Server na maszynach wirtualnych platformy Azure.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: e784cbf351bd062712e0fd66332799907a3bcae8
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648248"
---
# <a name="import-data-wizard-for-azure-search"></a>Kreator importu danych dla Azure Search

W witrynie Azure Portal na pulpicie nawigacyjnym usługi Azure Search znajduje się kreator **Importuj dane**, który umożliwia ładowanie danych do indeksu. W tle Kreator konfiguruje i wywołuje *Źródło danych*, *indeks*i *indeksator* — automatyzując kilka kroków procesu indeksowania: 

* Nawiązuje połączenie z zewnętrznym źródłem danych w ramach tej samej subskrypcji platformy Azure.
* Opcjonalnie integruje optyczne rozpoznawanie znaków lub przetwarzanie języka naturalnego w celu wyodrębnienia tekstu z danych bez struktury.
* Generuje indeks na podstawie próbkowania danych i metadanych zewnętrznego źródła danych.
* Przeszukuje źródło danych w celu przeszukiwania zawartości, serializacji i ładowania dokumentów JSON do indeksu.

Kreator nie może połączyć się ze wstępnie zdefiniowanym indeksem lub uruchomić istniejącego indeksatora, ale w kreatorze można skonfigurować nowy indeks lub indeksator, aby obsługiwał potrzebną strukturę i zachowania.

Dopiero zaczynasz korzystać z usługi Azure Search? Przejdź do [przewodnika Szybki Start: Importowanie, indeksowanie i wykonywanie zapytań przy użyciu](search-get-started-portal.md) narzędzi portalu do testowania importowania i indeksowania przy użyciu **danych importu** oraz wbudowanego zestawu danych przykładowej nieruchomości.

## <a name="start-importing-data"></a>Rozpocznij Importowanie danych

W tej sekcji wyjaśniono, jak uruchomić kreatora i przedstawiono ogólne omówienie każdego kroku.

1. W [Azure Portal](https://portal.azure.com)Otwórz stronę usługi wyszukiwania na pulpicie nawigacyjnym lub [Znajdź usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na liście usług.

2. Na stronie Przegląd usługi u góry kliknij pozycję **Importuj dane**.

   ![Importuj dane — polecenie w portalu](./media/search-import-data-portal/import-data-cmd2.png "Uruchom Kreatora importu danych")

   > [!NOTE]
   > Można uruchamiać **Importowanie danych** z innych usług platformy Azure, w tym Azure Cosmos DB, Azure SQL Database i usługi Azure Blob Storage. Poszukaj **Azure Search** w okienku nawigacji po lewej stronie Przegląd usługi.

3. Kreator zostanie otwarty w celu **nawiązania połączenia z danymi**, na których można wybrać zewnętrzne źródło danych do użycia w ramach tego importu. Istnieje kilka rzeczy, na które należy zapoznać się z tym krokiem. Pamiętaj, aby przeczytać sekcję dane [wejściowe źródła danych](#data-source-inputs) , aby uzyskać więcej szczegółów.

   ![Kreator importu danych w portalu](./media/search-import-data-portal/import-data-wizard-startup.png "Kreator importu danych dla Azure Search")

4. Następnie **Dodaj wyszukiwanie poznawcze**, w przypadku których chcesz uwzględnić optyczne rozpoznawanie znaków (OCR) tekstu w plikach obrazów lub analizę tekstu na danych bez struktury. Algorytmy AI z Cognitive Services są pobierane dla tego zadania. Ten krok obejmuje dwie części:
  
   Najpierw [Dołącz zasób Cognitive Services](cognitive-search-attach-cognitive-services.md) do Azure Search zestawu umiejętności.
  
   Następnie wybierz, które wzbogacania AI mają być uwzględnione w zestawu umiejętności. Demonstrację przewodnika można znaleźć w tym [przewodniku szybki start](cognitive-search-quickstart-blob.md).

   Jeśli chcesz jedynie zaimportować dane, Pomiń ten krok i przejdź bezpośrednio do definicji indeksu.

5. Następnie **Dostosuj indeks docelowy**, w którym możesz zaakceptować lub zmodyfikować schemat indeksu przedstawiony w kreatorze. Kreator wnioskuje pola i typy danych, pobierając dane próbkowania i odczytując metadane z zewnętrznego źródła danych.

   Dla każdego pola [Sprawdź atrybuty indeksu](#index-definition) , aby włączyć określone zachowania. Jeśli nie wybrano żadnych atrybutów, indeks nie będzie przydatny. 

6. Następnie **Utwórz indeksator**, który jest produktem tego kreatora. Indeksator to przeszukiwarka, która wyodrębnia dane z możliwością wyszukiwania i metadane z zewnętrznego źródła danych platformy Azure. Wybierając źródło danych i dołączając umiejętności (opcjonalnie) i indeks, można skonfigurować indeksator podczas przechodzenia przez poszczególne kroki kreatora.

   Nadaj Indeksatorowi nazwę, a następnie kliknij pozycję **Prześlij** , aby rozpocząć proces importowania. 

Indeksowanie można monitorować w portalu, klikając indeksator na liście indeksatorów. W miarę ładowania dokumentów liczba dokumentów dla zdefiniowanego indeksu będzie rosnąć. Czasem do odzwierciedlenia najnowszych aktualizacji na stronie portalu może minąć kilka minut.

Indeks jest gotowy do wykonywania zapytań zaraz po załadowaniu pierwszego dokumentu. Możesz użyć [Eksploratora wyszukiwania](search-explorer.md) dla tego zadania.

<a name="data-source-inputs"></a>

## <a name="data-source-inputs"></a>Dane wejściowe źródła danych

Kreator **importu danych** tworzy trwały obiekt źródła danych, określając informacje o połączeniu z zewnętrznym źródłem danych. Obiekt źródła danych jest używany wyłącznie z [indeksatorami](search-indexer-overview.md) i można go utworzyć dla następujących źródeł danych: 

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Usługi Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Table Storage platformy Azure](search-howto-indexing-azure-tables.md) (nieobsługiwane w przypadku potoków [wyszukiwania poznawczego](cognitive-search-concept-intro.md) )

Można importować tylko z pojedynczej tabeli, widoku bazy danych lub równoważnej struktury danych, jednak struktura może zawierać hierarchiczne lub zagnieżdżone struktury podstruktur. Aby uzyskać więcej informacji, zobacz [jak modelować typy złożone](search-howto-complex-data-types.md).

Tę strukturę danych należy utworzyć przed uruchomieniem kreatora i musi zawierać zawartość. Nie uruchamiaj kreatora **importowania danych** w pustym źródle danych.

|  Wybór | Opis |
| ---------- | ----------- |
| **Istniejące źródło danych** |Jeśli masz już zdefiniowane indeksatory w usłudze wyszukiwania, możesz użyć istniejącej definicji źródła danych na potrzeby innego importu. W Azure Search obiekty źródła danych są używane tylko przez indeksatory. Obiekt źródła danych można utworzyć programowo lub za pomocą kreatora **importu danych** .|
| **Przykłady**| Azure Search obsługuje bezpłatną globalną bazę danych SQL Azure, której można użyć, aby dowiedzieć się więcej o importowaniu i wysyłaniu zapytań w Azure Search. Zobacz [Szybki start: Zaimportuj, Indeksuj i zapytaj przy](search-get-started-portal.md) użyciu narzędzi portalu, aby zapoznać się z przewodnikiem. |
| **Azure SQL Database** |Nazwę usługi, poświadczenia użytkownika z uprawnieniem do odczytu bazy danych i nazwę bazy danych można określić na stronie lub przy użyciu parametrów połączenia ADO.NET. Wybierz opcję parametrów połączenia, aby wyświetlić lub dostosować właściwości. <br/><br/>Na stronie należy określić tabelę lub widok zawierające zestaw wierszy. Ta opcja jest dostępna po udanym nawiązaniu połączenia. Pojawia się wtedy lista rozwijana, z której można dokonać wyboru. |
| **Program SQL Server na maszynie wirtualnej platformy Azure** |Określ w pełni kwalifikowaną nazwę usługi, identyfikator użytkownika i hasło oraz bazę danych jako parametry połączenia. Aby użyć tego źródła danych, należy wcześniej zainstalować w magazynie lokalnym certyfikat szyfrujący połączenie. Aby uzyskać instrukcje, zobacz [SQL VM connection to Azure Search](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md) (Połączenie maszyny wirtualnej bazy danych SQL z usługą Azure Search). <br/><br/>Na stronie należy określić tabelę lub widok zawierające zestaw wierszy. Ta opcja jest dostępna po udanym nawiązaniu połączenia. Pojawia się wtedy lista rozwijana, z której można dokonać wyboru. |
| **Cosmos DB** |Wymagane jest konto, baza danych i kolekcja. Wszystkie dokumenty w kolekcji zostaną uwzględnione w indeksie. Można zdefiniować zapytanie, aby spłaszczyć lub filtrować zestaw wierszy, lub pozostawić zapytanie puste. Zapytanie nie jest wymagane w tym kreatorze.|
| **Azure Blob Storage** |Wymagane jest miedzy innymi konto magazynu i kontener. Opcjonalnie, jeśli nazwa obiektu blob jest zgodna z konwencją nazw wirtualnych do celów grupowania, można określić część nazwy oznaczającą katalog wirtualny jako folder w kontenerze. Więcej informacji zawiera artykuł [Indexing Blob Storage](search-howto-indexing-azure-blob-storage.md) (Indeksowanie w usłudze Blob Storage). |
| **Azure Table Storage** |Wymagane jest miedzy innymi konto magazynu i nazwa tabeli. Opcjonalnie można określić zapytanie w celu pobrania podzbioru tabel. Więcej informacji zawiera artykuł [Indexing Table Storage](search-howto-indexing-azure-tables.md) (Indeksowanie w usłudze Table Storage). |


<a name="index-definition"></a>

## <a name="index-attributes"></a>Atrybuty indeksu

Kreator **importu danych** generuje indeks, który zostanie wypełniony dokumentami uzyskanymi ze źródła danych wejściowych. 

W przypadku indeksów funkcjonalnych upewnij się, że masz zdefiniowane następujące elementy.

1. Jedno pole musi być oznaczone jako **klucz**, który służy do jednoznacznego identyfikowania każdego dokumentu. **Klucz** musi mieć wartość *EDM. String*. 

   Jeśli wartości pola zawierają spacje lub kreski, należy ustawić opcję **kodowania Base-64** w kroku **Utwórz indeksator** w obszarze **Opcje zaawansowane**, aby pominąć sprawdzanie poprawności tych znaków.

1. Ustaw atrybuty indeksu dla każdego pola. W przypadku wybrania opcji brak atrybutów indeks jest zasadniczo pusty, z wyjątkiem wymaganego pola klucza. Należy wybrać co najmniej jeden z tych atrybutów dla każdego pola.
   
   + Możliwość **pobierania** zwraca pole w wynikach wyszukiwania. Każde pole, które zapewnia zawartość do wyników wyszukiwania, musi mieć ten atrybut. Ustawienie tego pola nie ma znaczącego wpływu na rozmiar indeksu.
   + **Filtrowanie** umożliwia odwoływanie się do pola w wyrażeniach filtru. Każde pole używane w wyrażeniu **$Filter** musi mieć ten atrybut. Wyrażenia filtru są dla dokładnych dopasowań. Ponieważ ciągi tekstowe pozostają nienaruszone, do obsługi zawartości Verbatim jest wymagany dodatkowy magazyn.
   + **Wyszukiwanie** umożliwia wyszukiwanie pełnotekstowe. Każde pole używane w kwerendach w dowolnej postaci lub w wyrażeniach zapytań musi mieć ten atrybut. Odwrócone indeksy są tworzone dla każdego pola, które jest oznaczonejako możliwe do przeszukania.

1. Opcjonalnie ustaw te atrybuty zgodnie z wymaganiami:

   + Sortowanie umożliwia używanie pola w sortowaniu. Każde pole używane w wyrażeniu **$OrderBy** musi mieć ten atrybut.
   + Element **kroju** umożliwia pole dla nawigacji aspektowej. Tylko pola oznaczone jako możliwe do **filtrowania** mogą być oznaczone jako **kroju**.

1. Ustaw **Analizator** , jeśli chcesz, aby ulepszone język indeksowania i zapytania. Wartość domyślna to *standardowa Lucene* , ale można wybrać *język angielski firmy Microsoft* , jeśli chcesz użyć analizatora firmy Microsoft do zaawansowanego przetwarzania leksykalnego, takiego jak rozwiązywanie nietypowych form rzeczowników i czasowników.

   + Wybierz opcję **wyszukiwania** , aby włączyć listę **analizatora** .
   + Wybierz Analizator podany na liście. 
   
   Obecnie można określić tylko analizatory języka. Skorzystanie z analizatorów niestandardowych lub innych niż analizatory języka, takich jak analizatory słów kluczowych, wzorców itp., będzie wymagać kodu. Aby uzyskać więcej informacji na temat analizatorów, zobacz [Tworzenie indeksu dla dokumentów w wielu językach](search-language-support.md).

1. Zaznacz pole wyboru **Sugeruj** , aby włączyć sugestie zapytania typu z wyprzedzeniem dla wybranych pól.


## <a name="next-steps"></a>Następne kroki
Przejrzyj następujące linki, aby dowiedzieć się więcej o indeksatorach:

* [Indeksowanie w usłudze Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Indexing Azure Cosmos DB](search-howto-index-cosmosdb.md) (Indeksowanie w usłudze Azure Cosmos DB)
* [Indeksowanie w usłudze Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Indeksowanie w usłudze Table Storage](search-howto-indexing-azure-tables.md)