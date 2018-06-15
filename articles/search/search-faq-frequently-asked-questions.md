---
title: Często zadawane pytania dotyczące usługi Azure Search | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące usługi Microsoft Azure Search
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/03/2017
ms.author: heidist
ms.openlocfilehash: d731faffe1b2448670a5fafa0278ff8c7fb21722
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31792736"
---
# <a name="azure-search---frequently-asked-questions-faq"></a>Usługa wyszukiwanie Azure — często zadawane pytania (FAQ)

 Odpowiedzi na często zadawane pytania dotyczące pojęć, kodu i scenariusze związane z usługi Azure Search.

## <a name="platform"></a>Platforma

### <a name="how-is-azure-search-different-from-full-text-search-in-my-dbms"></a>Jak usługi Azure Search jest inny niż wyszukiwanie pełnotekstowe w mojej DBMS?

Wyszukiwanie Azure obsługuje wiele źródeł danych, [językową analiza wielu języków](https://docs.microsoft.com/rest/api/searchservice/language-support), [analizy niestandardowego dla danych wejściowych danych interesujące i nietypowych](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search), wyszukaj rangi formantów za pomocą [oceniania Profile](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)oraz funkcji środowiska użytkownika, takich jak typeahead wyróżnianie trafień i nawigacji aspektowej. Zawiera również inne funkcje, takie jak synonimy i składnia kwerendy sformatowanej, ale te są zwykle nie rozróżnianie funkcji.

### <a name="what-is-the-difference-between-azure-search-and-elasticsearch"></a>Jaka jest różnica między usługi Azure Search i Elasticsearch?

Porównując technologii wyszukiwania, klienci często uzyskać charakterystykę na jak usługi Azure Search porównuje Elasticsearch. Klienci, którzy wybierz usługi Azure Search za pośrednictwem Elasticsearch wyszukiwania projektów aplikacji zwykle zrobić ponieważ wprowadziliśmy klucza zadań łatwiejsze lub potrzebują wbudowanej integracji z innymi technologiami firmy Microsoft:

+ Wyszukiwanie Azure jest usługą w chmurze pełni zarządzany z wartością 99,9% umów dotyczących poziomu usług (SLA) podczas obsługi administracyjnej z nadmiarowością wystarczające (2 repliki do odczytu, 3 repliki do odczytu i zapisu).
+ Firmy Microsoft [procesorów języka naturalnego](https://docs.microsoft.com/rest/api/searchservice/language-support) oferują językową analiza krawędzi.  
+ [Usługa Azure Search indeksatory](search-indexer-overview.md) może przeszukiwać różnych źródeł danych Azure początkowa i przyrostowa indeksowania.
+ Jeśli potrzebujesz szybką odpowiedź na wahań zapytania lub indeksowania woluminów, możesz użyć [suwaków](search-manage.md#scale-up-or-down) na platformie Azure, w portalu lub wykonywania [skrypt programu PowerShell](search-manage-powershell.md), bezpośrednio obchodzenia zarządzania niezależnego fragmentu.  
+ [Ocenianie i dostrajania funkcji](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) Podaj środki wpływające na wyszukiwanie randze poza co można podać tylko aparatu wyszukiwania.

### <a name="can-i-pause-azure-search-service-and-stop-billing"></a>Można wstrzymać usługi Azure Search i Zatrzymaj rozliczeń?

Nie można wstrzymać usługi. Przydzielania zasobów obliczeniowych i magazynu do wyłącznego użytku podczas tworzenia usługi. Nie jest możliwe wersji i odzyskania tych zasobów na żądanie.

## <a name="indexing-operations"></a>Operacje indeksowania

### <a name="backup-and-restore-or-download-and-move-indexes-or-index-snapshots"></a>Indeksy kopii zapasowej i przywracania (lub pobierania i przenieś) lub indeks migawki?

Mimo że można [pobrać definicji indeksu](https://docs.microsoft.com/rest/api/searchservice/get-index) w dowolnym momencie, nie jest dostępna wyodrębniania indeksu, migawki ani funkcja przywracania kopii zapasowej pobierania *wypełnione* indeksu działającą w chmurze w systemie lokalnym, lub przeniesienie jej do innej usługi Azure Search.

Indeksy są wbudowane i wypełnione z kodu, który zapisu i uruchomić tylko na usłudze Azure Search w chmurze. Zazwyczaj klientów, którzy mają zostać przeniesione do innej usługi indeksu zrobić, edytując ich kodu do nowego punktu końcowego i spróbować ponownie indeksowania. Utworzenie migawki i kopii zapasowej indeksu należy rzutować głosowanie [User Voice](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index).

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexershttpsdocsmicrosoftcomazuresearchsearch-howto-connecting-azure-sql-database-to-azure-search-using-indexers"></a>Może indeksować z repliki bazy danych SQL (dotyczy [indeksatory bazy danych SQL Azure](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-database-to-azure-search-using-indexers))

Nie ma żadnych ograniczeń na korzystanie z repliki podstawowej lub pomocniczej jako źródła danych, podczas tworzenia indeksu od początku. Odświeżanie indeksu z aktualizacji przyrostowych (uwzględnieniem zmienionych rekordów) wymaga jednak repliki podstawowej. To wymaganie jest dostarczany z bazy danych SQL, które gwarantuje Zmień śledzenie na tylko podstawowy replik. Jeśli spróbujesz się przy użyciu repliki pomocniczej dla obciążenia odświeżania indeksu, nie ma żadnej gwarancji, uzyskasz wszystkie dane.

## <a name="search-operations"></a>Operacji wyszukiwania

### <a name="can-i-search-across-multiple-indexes"></a>Umożliwia wyszukiwanie w wielu indeksów?

Nie, ta operacja nie jest obsługiwana. Indeks zawsze obejmuje wyszukiwania.

### <a name="can-i-restrict-search-corpus-access-by-user-identity"></a>Czy można ograniczyć dostęp Boże wyszukiwania przez tożsamość użytkownika

Można zaimplementować [filtrów zabezpieczeń](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search) z `search.in()` filtru. Filtr Redaguj z [tożsamości usługi zarządzania, takich jak Azure Active Directory(AAD)](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search-with-aad) trim wyniki wyszukiwania na podstawie zdefiniowanych członkostwo w grupie użytkowników.

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>Dlaczego są zero odpowiada na postanowienia, które sprawdzić ważność?

Najbardziej typowych przypadkach jest nie wiedząc, że każdy typ zapytania obsługuje zachowań wyszukiwania różnych i poziomy językowe analiz. Wyszukiwanie pełnotekstowe, czyli dominujący obciążenie obejmuje faza analizy języka, który dzieli warunków do głównego formularzy. Ten aspekt analizowania zapytania rzutuje szerszych net za pośrednictwem dopasowania, ponieważ tokenami termin jest zgodna z większą liczbą elementów typu Variant.

Symboli wieloznacznych, rozmytego regex zapytań, jednak nie są analizowane jak regularne termin lub frazę zapytania i może prowadzić do Odwołaj słabej, jeśli zapytanie nie jest zgodny z przeanalizowane formularz programu word w indeksie wyszukiwania. Aby uzyskać więcej informacji dotyczących analizy zapytania i analizy, zobacz [zapytania architektura](https://docs.microsoft.com/azure/search/search-lucene-query-architecture).

### <a name="my-wildcard-searches-are-slow"></a>Moje wyszukiwania symboli wieloznacznych jest mała.

Większość zapytania wyszukiwania symboli wieloznacznych, takie jak prefiks rozmytego i wyrażeń regularnych, są ulegną wewnętrznie spełniające warunki w indeksie wyszukiwania. To dodatkowe przetwarzanie skanowania indeksu wyszukiwania dodaje do opóźnienia. Ponadto szerokie wyszukiwania wysyła zapytanie, tak samo, jak `a*` na przykład, które są prawdopodobnie ponownego z wielu warunki mogą być bardzo wolno. Wyszukiwanie symboli wieloznacznych wydajności, rozważ zdefiniowanie [analizatora niestandardowego](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search).

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>Dlaczego jest ranking wyszukiwania wynikiem stałej lub równa 1.0 dla każdego trafień?

Domyślnie wyniki wyszukiwania są oceniane na podstawie [statystyczne właściwości pasujących warunki](search-lucene-query-architecture.md#stage-4-scoring)i wysoko lub nisko w wyniku zestaw uporządkowany. Jednak niektóre typy (symbol wieloznaczny, prefiksu, wyrażenie regularne) zapytań zawsze współtworzenia stałej wynik do ogólny wynik dokumentu. To zachowanie jest celowe. Wyszukiwanie Azure nakłada stałej wynik, aby umożliwić dopasowań za pośrednictwem rozszerzenia zapytania mają zostać uwzględnione w wynikach, bez wywierania wpływu na kolejność.

Na przykład załóżmy, że danych wejściowych "samouczek *" w wyszukiwaniu symboli wieloznacznych tworzy dopasowania "przewodniki", "tourettes" i "tourmaline". Biorąc pod uwagę rodzaj tych wyników, nie istnieje sposób rozsądnych rozpoznać terminów są większej wartości niż inne. Z tego powodu możemy Ignoruj określenie częstotliwości podczas oceniania wyników w kwerendach typów symboli wieloznacznych, prefiksu i wyrażenia regularnego. Wyniki wyszukiwania na podstawie częściowe danych wprowadzonych podano stałej wynik w celu uniknięcia odchylenia kierunku potencjalnie nieoczekiwany dopasowań.

## <a name="design-patterns"></a>Wzorce projektowe

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>Co to jest najlepszym rozwiązaniem dla Implementowanie wyszukiwania zlokalizowanych?

Większość klientów wybierz pola dedykowanych przez kolekcję po przejściu do obsługi różnych ustawień regionalnych (języki) w tym samym indeksie. Pola ustawień regionalnych umożliwiają przypisać odpowiednie analizatora. Na przykład przypisywanie analizatora francuski firmy Microsoft do pola zawierającego ciągi francuskim. Ułatwia także filtrowania. Jeśli wiesz, że zapytanie jest inicjowana na stronie fr-fr, można ograniczyć wyniki wyszukiwania do tego pola. Lub Utwórz [profilu oceniania](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) umożliwiają więcej względną wagę pola. Usługa Azure Search obsługuje przez [50 analizatorów języka](https://docs.microsoft.com/azure/search/search-language-support) do wyboru.

## <a name="next-steps"></a>Kolejne kroki

To pytanie dotyczące funkcji lub funkcjonalności? Żądania funkcji na [witryny User Voice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="see-also"></a>Zobacz także

 [StackOverflow: Usługa Azure Search](https://stackoverflow.com/questions/tagged/azure-search)   
 [Ile wyszukiwanie pełnotekstowe działa w usłudze Azure Search](search-lucene-query-architecture.md)  
 [Co to jest usługa Azure Search?](search-what-is-azure-search.md)
