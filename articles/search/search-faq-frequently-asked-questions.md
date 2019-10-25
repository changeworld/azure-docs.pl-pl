---
title: Często zadawane pytania
titleSuffix: Azure Cognitive Search
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące usługi Microsoft Azure Wyszukiwanie poznawcze, hostowanej usługi wyszukiwania w chmurze w Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fee74cb6ec5acd5fa0f171eab9769a833f04ad66
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792907"
---
# <a name="azure-cognitive-search---frequently-asked-questions-faq"></a>Wyszukiwanie poznawcze platformy Azure — często zadawane pytania

 Znajdź odpowiedzi na często zadawane pytania dotyczące pojęć, kodu i scenariuszy związanych z usługą Azure Wyszukiwanie poznawcze.

## <a name="platform"></a>Platforma

### <a name="how-is-azure-cognitive-search-different-from-full-text-search-in-my-dbms"></a>Jak platforma Azure Wyszukiwanie poznawcze różni się od wyszukiwania pełnotekstowego w systemie DBMS?

Usługa Azure Wyszukiwanie poznawcze obsługuje wiele źródeł danych, [analizę językową dla wielu języków](https://docs.microsoft.com/rest/api/searchservice/language-support), [niestandardową analizę dla interesujących i nietypowych danych wejściowych](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search), wyszukiwanie rangi przy użyciu [profilów oceniania](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)i środowiska użytkownika funkcje takie jak typeahead, wyróżnianie trafień i nawigacja aspektów. Zawiera również inne funkcje, takie jak synonimy i rozbudowana składnia zapytań, ale zazwyczaj nie odróżniają funkcji.

### <a name="what-is-the-difference-between-azure-cognitive-search-and-elasticsearch"></a>Jaka jest różnica między usługą Azure Wyszukiwanie poznawcze i Elasticsearch?

Podczas porównywania technologii wyszukiwania klienci często pytają, w jaki sposób usługa Azure Wyszukiwanie poznawcze porównuje z Elasticsearch. Klienci, którzy wybierają platformę Azure Wyszukiwanie poznawcze za pośrednictwem Elasticsearch na potrzeby projektów aplikacji wyszukiwania, zwykle to dlatego, że wprowadziliśmy kluczowe zadanie w łatwiejszy sposób, lub potrzebują wbudowanej integracji z innymi technologiami firmy Microsoft:

+ Azure Wyszukiwanie poznawcze to w pełni zarządzana usługa w chmurze z 99,9% umowami dotyczącymi poziomu usług (SLA) w przypadku udostępnienia wystarczającej nadmiarowości (2 repliki do odczytu i zapisu).
+ [Procesory języka naturalnego](https://docs.microsoft.com/rest/api/searchservice/language-support) firmy Microsoft oferują wiodącą analizę lingwistyczną.  
+ [Indeksatory usługi azure wyszukiwanie poznawcze](search-indexer-overview.md) mogą przeszukiwać różne źródła danych platformy Azure na potrzeby indeksowania początkowego i przyrostowego.
+ Jeśli potrzebujesz szybkiej reakcji na fluktuacje na woluminach zapytań lub indeksowania, możesz użyć [kontrolek suwaka](search-manage.md#scale-up-or-down) w Azure Portal lub uruchomić [skrypt programu PowerShell](search-manage-powershell.md), pomijając fragmentu Management bezpośrednio.  
+ [Funkcje oceniania i dostrajania](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) zapewniają środki pozwalające na wpływ na wyniki wyszukiwania rangi wykraczające poza wyszukiwarkę.

### <a name="can-i-pause-azure-cognitive-search-service-and-stop-billing"></a>Czy mogę wstrzymać usługę Azure Wyszukiwanie poznawcze i zatrzymać rozliczanie?

Nie można wstrzymać usługi. Zasoby obliczeniowe i magazynowe są przypisywane do wyłącznego użytku podczas tworzenia usługi. Nie jest możliwe udostępnianie i odzyskiwanie tych zasobów na żądanie.

## <a name="indexing-operations"></a>Operacje indeksowania

### <a name="move-backup-and-restore-indexes-or-index-snapshots"></a>Przenoszenie, wykonywanie kopii zapasowych i przywracanie indeksów lub migawek indeksów?

Podczas fazy tworzenia warto przenieść indeks między usługami wyszukiwania. Na przykład możesz użyć warstwy cenowej podstawowa lub bezpłatna do opracowania indeksu, a następnie przenieść ją do warstwy Standardowa lub wyższa do użycia w środowisku produkcyjnym. 

Można również utworzyć kopię zapasową migawki indeksu w plikach, których można użyć do późniejszego przywrócenia. 

Wszystkie te elementy można wykonać za pomocą przykładowego kodu **index-Backup-Restore** w tym [repozytorium przykładowym platformy Azure wyszukiwanie poznawcze .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples). 

W dowolnym momencie możesz również [uzyskać definicję indeksu](https://docs.microsoft.com/rest/api/searchservice/get-index) przy użyciu interfejsu API REST usługi Azure wyszukiwanie poznawcze.

W Azure Portal obecnie nie ma wbudowanej funkcji wyodrębniania indeksów, migawek ani przywracania kopii zapasowych. Rozważamy jednak Dodawanie funkcji tworzenia kopii zapasowych i przywracania w przyszłej wersji. Jeśli chcesz pokazać pomoc techniczną dotyczącą tej funkcji, dokonaj głosowania na [głos użytkownika](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index).

### <a name="can-i-restore-my-index-or-service-once-it-is-deleted"></a>Czy można przywrócić indeks lub usługę po jej usunięciu?

Nie, jeśli usuniesz indeks Wyszukiwanie poznawcze lub usługę platformy Azure, nie będzie można jej odzyskać. Po usunięciu usługi Azure Wyszukiwanie poznawcze wszystkie indeksy w usłudze są trwale usuwane. Jeśli usuniesz grupę zasobów platformy Azure zawierającą co najmniej jedną usługę Wyszukiwanie poznawcze platformy Azure, wszystkie usługi zostaną trwale usunięte.  

Ponowne tworzenie zasobów, takich jak indeksy, indeksatory, źródła danych i umiejętności, wymaga ponownego utworzenia ich z kodu. 

Aby odtworzyć indeks, należy ponownie zindeksować dane ze źródeł zewnętrznych. Z tego powodu zaleca się zachowywanie kopii głównej lub kopii zapasowej oryginalnych danych w innym magazynie danych, takim jak Azure SQL Database lub Cosmos DB.

Alternatywnie możesz użyć przykładowego kodu w ramach **przywracania z użyciem indeksu kopii zapasowej** w ramach tego [przykładowego repozytorium platformy Azure wyszukiwanie poznawcze .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples) , aby utworzyć kopię zapasową definicji indeksu i migawki indeksu do serii plików JSON. Później można użyć narzędzia i plików, aby przywrócić indeks, w razie potrzeby.  

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexershttpsdocsmicrosoftcomazuresearchsearch-howto-connecting-azure-sql-database-to-azure-search-using-indexers"></a>Czy można indeksować repliki baz danych SQL (dotyczy [Azure SQL Database indeksatorów](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-database-to-azure-search-using-indexers))

Podczas kompilowania indeksu od podstaw nie ma żadnych ograniczeń dotyczących używania replik podstawowych lub pomocniczych jako źródła danych. Jednak odświeżanie indeksu przy użyciu aktualizacji przyrostowych (na podstawie zmienionych rekordów) wymaga repliki podstawowej. To wymaganie pochodzi z SQL Database, co gwarantuje śledzenie zmian tylko w replikach podstawowych. Jeśli spróbujesz użyć replik pomocniczych dla obciążenia odświeżania indeksu, nie ma gwarancji, że uzyskasz wszystkie dane.

## <a name="search-operations"></a>Operacje wyszukiwania

### <a name="can-i-search-across-multiple-indexes"></a>Czy mogę przeszukiwać wiele indeksów?

Nie. Ta operacja nie jest obsługiwana. Wyszukiwanie jest zawsze ograniczone do jednego indeksu.

### <a name="can-i-restrict-search-index-access-by-user-identity"></a>Czy mogę ograniczyć dostęp do indeksu wyszukiwania przez tożsamość użytkownika?

[Filtry zabezpieczeń](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search) można zaimplementować z filtrem `search.in()`. Filtr jest dobrze złożony z [usług zarządzania tożsamościami, takich jak Azure Active Directory (AAD)](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search-with-aad) , aby przyciąć wyniki wyszukiwania na podstawie zdefiniowanego członkostwa w grupie użytkowników.

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>Dlaczego nie ma żadnych dopasowań dotyczących poprawnych warunków?

Najpopularniejszym przypadkiem nie jest znajomość, że każdy typ zapytania obsługuje różne zachowania wyszukiwania i poziomy analiz językowych. Wyszukiwanie pełnotekstowe, które jest głównym obciążeniem, obejmuje fazę analizy języka, która przerywa warunki do formularzy głównych. Ten aspekt analizy zapytania rzutuje szerszy dostęp do sieci, ponieważ termin tokenów pasuje do większej liczby wariantów.

Zapytania o symbole wieloznaczne, rozmyte i wyrażenia regularnego nie są jednak analizowane jak regularne zapytania warunkowe lub zwrotne i mogą prowadzić do słabego odwołania, jeśli zapytanie nie jest zgodne z przeanalizowanym formularzem wyrazu w indeksie wyszukiwania. Aby uzyskać więcej informacji na temat analizy i analizy zapytań, zobacz temat [Architektura zapytań](https://docs.microsoft.com/azure/search/search-lucene-query-architecture).

### <a name="my-wildcard-searches-are-slow"></a>Wyszukiwanie przy użyciu symboli wieloznacznych jest powolne.

Większość kwerend wyszukiwania wieloznacznego, takich jak prefiks, rozmyte i wyrażenie regularne, są ponownie zapisywane wewnętrznie przy użyciu pasujących terminów w indeksie wyszukiwania. To dodatkowe przetwarzanie skanowania indeksu wyszukiwania powoduje dodanie opóźnienia. Dalsze, szerokie zapytania wyszukiwania, takie jak `a*` na przykład, które mogą być ponownie zapisywane z wieloma terminami, może być bardzo powolne. W przypadku przeszukiwanych symboli wieloznacznych należy rozważyć zdefiniowanie [analizatora niestandardowego](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search).

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>Dlaczego wyszukiwanie określa stałą lub równą ocenę 1,0 dla każdego trafienia?

Domyślnie wyniki wyszukiwania są oceniane na podstawie [Właściwości statystycznych zgodnych terminów](search-lucene-query-architecture.md#stage-4-scoring)i uporządkowane z wysokim poziomem do niskiego poziomu w zestawie wyników. Jednak niektóre typy zapytań (symbol wieloznaczny, prefiks, wyrażenie regularne) zawsze tworzą stały wynik do ogólnego wyniku dokumentu. To zachowanie jest celowe. Platforma Azure Wyszukiwanie poznawcze nakłada stałą wartość, aby umożliwić dołączenie dopasowań w wyniku rozszerzania zapytania, bez wywierania wpływu na klasyfikację.

Załóżmy na przykład, że dane wejściowe "przewodnik *" w wyszukiwaniu symboli wieloznacznych generują dopasowania w "przewodnikach", "Tourettes" i "Tourmaline". Mając na względzie charakter tych wyników, nie ma możliwości racjonalnego wyznaczenia, które warunki są bardziej cenne niż inne. Z tego powodu ignorujemy okresowe częstotliwości, gdy ocenia wyniki w kwerendach typów symboli wieloznacznych, prefiksów i wyrażeń regularnych. Wyniki wyszukiwania oparte na danych wejściowych są przydzielone stałym wskaźnikiem, aby uniknąć odliczania się do potencjalnie nieoczekiwanych dopasowań.

## <a name="design-patterns"></a>Wzorce projektowe

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>Jaki jest najlepszy sposób implementacji zlokalizowanego wyszukiwania?

Większość klientów wybiera dedykowane pola w kolekcji, gdy ma ona obsługiwać różne ustawienia regionalne (języki) w tym samym indeksie. Pola specyficzne dla ustawień regionalnych umożliwiają przypisanie odpowiedniej analizatora. Na przykład przypisanie analizatora francuskiego firmy Microsoft do pola zawierającego ciągi francuski. Upraszcza to również filtrowanie. Jeśli wiesz, że zapytanie jest inicjowane na stronie fr-fr, możesz ograniczyć wyniki wyszukiwania do tego pola. Lub Utwórz [profil oceniania](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) , aby nadać polu bardziej względną wagę. Usługa Azure Wyszukiwanie poznawcze obsługuje ponad [50 analizatorów języka](https://docs.microsoft.com/azure/search/search-language-support) w celu wyboru.

## <a name="next-steps"></a>Następne kroki

Czy masz pytanie dotyczące brakującej funkcji lub funkcji? Zażądaj funkcji w [witrynie sieci Web głosu użytkownika](https://feedback.azure.com/forums/263029-azure-search).

## <a name="see-also"></a>Zobacz także

 [StackOverflow: usługa Azure Wyszukiwanie poznawcze](https://stackoverflow.com/questions/tagged/azure-search)   
 [Jak działa wyszukiwanie pełnotekstowe na platformie Azure Wyszukiwanie poznawcze](search-lucene-query-architecture.md)  
 [Co to jest platforma Azure Wyszukiwanie poznawcze?](search-what-is-azure-search.md)
