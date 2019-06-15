---
title: Często zadawane pytania (FAQ) — usługa Azure Search
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące usługi Microsoft Azure Search, Usługa wyszukiwania hostowane w chmurze w systemie Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/03/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: c77f26187914b2c6e52426bb2a07303b22ccb2b0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65023991"
---
# <a name="azure-search---frequently-asked-questions-faq"></a>Usługa Azure Search — często zadawane pytania (FAQ)

 Znajdź odpowiedzi na często zadawane pytania na temat pojęć, kodu i scenariusze dotyczące usługi Azure Search.

## <a name="platform"></a>Platforma

### <a name="how-is-azure-search-different-from-full-text-search-in-my-dbms"></a>Czym różni się usługa Azure Search z wyszukiwanie pełnotekstowe w mojej DBMS?

Usługa Azure Search obsługuje wiele źródeł danych, [analizy językowej dla wielu języków](https://docs.microsoft.com/rest/api/searchservice/language-support), [niestandardowe analiza danych wejściowych interesujące i nietypowe](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search), wyszukaj rangi kontrolki za pomocą [oceniania Profile](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)i środowisko użytkownika funkcji, takich jak typeahead, wyróżnianie trafień i nawigacji aspektowej. Zawiera również inne funkcje, takie jak synonimów i składnia obsługą zaawansowanych zapytań, ale te są zwykle nie rozróżnienie tych funkcji.

### <a name="what-is-the-difference-between-azure-search-and-elasticsearch"></a>Jaka jest różnica między usługi Azure Search i Elasticsearch?

Podczas porównywania technologie wyszukiwania, klienci często pytają kątem specyfiki na porównanie usługi Azure Search za pomocą usługi Elasticsearch. Klientów, którzy zdecydowali usługi Azure Search za pośrednictwem usługi Elasticsearch na ich wyszukiwanie projektów aplikacji zwykle to zrobić, ponieważ wprowadziliśmy kluczowych zadań łatwiej lub potrzebują wbudowana Integracja z innymi technologiami firmy Microsoft:

+ Usługa Azure Search jest w pełni zarządzana usługa w chmurze za pomocą 99,9% umów dotyczących poziomu usług (SLA), gdy aprowizowane z wystarczającą nadmiarowości (2 repliki do odczytu, 3 repliki do odczytu i zapisu).
+ Firmy Microsoft [procesory języka naturalnego](https://docs.microsoft.com/rest/api/searchservice/language-support) oferują analizy językowej krawędzi.  
+ [Usługa Azure Search indeksatory](search-indexer-overview.md) może przeszukiwać różnych źródeł danych platformy Azure dla początkowa i przyrostowa indeksowania.
+ Jeśli potrzebujesz szybkiej reakcji na wahania zapytania lub indeksowania woluminów, możesz użyć [kontrolki suwaka](search-manage.md#scale-up-or-down) w witrynie Azure portal lub wykonywania [skrypt programu PowerShell](search-manage-powershell.md), bezpośrednio z pominięciem zarządzania fragmentami.  
+ [Ocenianie i dostosowywanie funkcji](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) zapewniają środki do wywieranie wpływu na wyszukiwanie randze poza samodzielnie aparat wyszukiwania, co umożliwia.

### <a name="can-i-pause-azure-search-service-and-stop-billing"></a>Można wstrzymać usługę Azure Search i zatrzymać Naliczanie opłaty?

Nie można wstrzymać usługę. Zasoby obliczeniowe i magazynowe są przydzielane do wyłącznego użytku podczas tworzenia usługi. Nie jest możliwe zwolnić i odzyskać te zasoby na żądanie.

## <a name="indexing-operations"></a>Operacje indeksowania

### <a name="backup-and-restore-or-download-and-move-indexes-or-index-snapshots"></a>Indeksy kopii zapasowej i przywracania (lub pobierania i przenieś) lub indeksu migawki?

Chociaż możesz [Pobierz definicję indeksu](https://docs.microsoft.com/rest/api/searchservice/get-index) w dowolnym momencie i nie ma indeksu wyodrębniania, migawki lub funkcji przywracania kopii zapasowej do pobierania *wypełnione* indeksu działające w chmurze do systemu lokalnego lub przenoszenie go do innej usługi Azure Search.

Indeksy są wbudowane i wypełnione z kodu, który napiszesz i wykonywać tylko na usłudze Azure Search w chmurze. Zazwyczaj klienci, którzy mają zostać przeniesione indeksu do innej usługi, to zrobić, edytując kod, aby używać nowego punktu końcowego, a następnie ponownie uruchom indeksowanie. Jeśli mają możliwość migawki lub utworzyć kopię zapasową indeksu, rzutowanie głos na [User Voice](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index).

### <a name="can-i-restore-my-index-or-service-once-it-is-deleted"></a>Czy można przywrócić indeksu lub usługi po jej usunięciu?

Nie, nie można przywrócić indeksów lub usługi. Po usunięciu indeksu usługi Azure Search, operacja jest ostateczne i indeksu nie może zostać odzyskany. Wszystkie indeksy w usłudze są trwale usuwane po usunięciu usługi Azure Search. Ponadto jeśli usuniesz grupę zasobów platformy Azure, która zawiera co najmniej jednej usługi Azure Search, wszystkie usługi są trwale usuwane.  

Przywracanie zasobów, takich jak indeksy, indeksatory, źródła danych i dokładniejsze wymaga odtworzyć je z poziomu kodu. W przypadku indeksów ponowna indeksacja dane ze źródeł zewnętrznych. Z tego powodu zdecydowanie zaleca się zachować kopii głównej lub kopia zapasowa oryginalnego danych w innym magazynie danych, takich jak Azure SQL Database i Cosmos DB.

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexershttpsdocsmicrosoftcomazuresearchsearch-howto-connecting-azure-sql-database-to-azure-search-using-indexers"></a>Umożliwia indeksowanie z repliki bazy danych SQL (dotyczy [indeksatorów usługi Azure SQL Database](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-database-to-azure-search-using-indexers))

Nie ma żadnych ograniczeń dotyczących używania repliki podstawowej lub dodatkowej jako źródło danych, podczas tworzenia indeksu od podstaw. Odświeżanie indeksu za pomocą aktualizacji przyrostowych (oparte na zmienionych rekordów) wymaga jednak znajduje się replika podstawowa. To wymaganie jest dostarczany z bazy danych SQL, które gwarantuje Zmień śledzenie na tylko podstawowy replik. Jeśli spróbujesz przy użyciu repliki pomocniczej w przypadku obciążeń odświeżania indeksu, nie ma gwarancji uzyskasz wszystkie dane.

## <a name="search-operations"></a>Operacji wyszukiwania

### <a name="can-i-search-across-multiple-indexes"></a>Można wyszukiwać w wiele indeksów?

Nie, ta operacja nie jest obsługiwana. Wyszukiwanie jest zawsze ograniczony do jednego indeksu.

### <a name="can-i-restrict-search-index-access-by-user-identity"></a>Czy można ograniczyć dostęp do indeksu wyszukiwania, przez tożsamość użytkownika?

Możesz zaimplementować [filtrów zabezpieczeń](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search) z `search.in()` filtru. Filtr komponuje się również z [usługi zarządzania tożsamościami, takich jak Azure Active o](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search-with-aad) trim wyniki wyszukiwania oparte na zdefiniowanych członkostwa w grupie użytkowników.

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>Dlaczego są zero odpowiada na warunkach, znanych jako prawidłowy?

Najbardziej typowe przypadek nie jest wiedza, że każdy typ zapytania obsługuje różne wyszukiwań i poziomy analizy językowej. Wyszukiwanie pełnotekstowe, czyli dominujący obciążenia zawiera język faza analizy, która narusza postanowienia do głównego formularzy. Ten aspekt zapytania analizy rzutuje szersze net za pośrednictwem możliwych dopasowań, ponieważ tokenami termin jest zgodna z większą liczbą wariantów.

Symbol wieloznaczny rozmyte zapytania wyrażeń regularnych, jednak nie są analizowane, takie jak regularne termin lub frazę do zapytania i może prowadzić do słabej odwołania, jeśli zapytanie nie jest zgodny z przeanalizowany formularz programu word w indeksie wyszukiwania. Aby uzyskać więcej informacji na temat przetwarzania zapytań i analizy, zobacz [zapytania architektury](https://docs.microsoft.com/azure/search/search-lucene-query-architecture).

### <a name="my-wildcard-searches-are-slow"></a>Moje wyszukiwania symboli wieloznacznych są powolne.

Większość zapytań wyszukiwania symboli wieloznacznych, takie jak prefiks rozmyte i wyrażeń regularnych, są wewnętrznie przepisany przy użyciu pasujące terminy w indeksie wyszukiwania. To dodatkowe przetwarzanie skanowania indeksu wyszukiwania dodaje do opóźnienia. Dalsze wyszukiwania szerokiego wysyła zapytanie, takie jak `a*` na przykład, które są prawdopodobnie zostać przepisane, za pomocą wielu warunków mogą być bardzo wolne. Wydajne wyszukiwania symboli wieloznacznych, należy wziąć pod uwagę definiowanie [analizatora niestandardowego](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search).

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>Dlaczego jest wyszukiwarkach ocenę stałej lub równa 1.0 dla każdego trafień?

Domyślnie wyniki wyszukiwania są oceniane na podstawie [warunków dopasowania właściwości statystyczne](search-lucene-query-architecture.md#stage-4-scoring)i uporządkowany zestaw wysoko lub nisko w wyniku. Jednak niektóre zapytania typów (symbol wieloznaczny, prefiksu, wyrażenie regularne) zawsze współtworzyć stałej wynik ogólny wynik dokumentu. To zachowanie jest celowe. Usługa Azure Search nakłada stałej wynik umożliwia dopasowań za pośrednictwem rozszerzenia zapytania mają zostać uwzględnione w wynikach, bez wywierania wpływu na kolejność.

Na przykład załóżmy, że dane wejściowe "samouczek *" w wyszukiwaniu symboli wieloznacznych tworzy dopasowanie "samouczki", "tourettes" i "tourmaline". Ze względu na charakter tych wyników, nie ma możliwości rozsądnie wywnioskowania warunki, które są bardziej przydatne niż inne. Z tego powodu firma Microsoft zignorować określenie częstotliwości oceny wyników w kwerendach typów symboli wieloznacznych, prefiksu i wyrażeń regularnych. Wyniki wyszukiwania oparte na częściowe dane wejściowe podane są stałe wynik w celu uniknięcia odchylenie kierunku potencjalnie nieoczekiwany dopasowań.

## <a name="design-patterns"></a>Wzorce projektowe

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>Co to jest najlepszym rozwiązaniem dla implementacji wyszukiwania zlokalizowanych?

Większość klientów wybierz dedykowanych pól przez kolekcję, jeśli chodzi o obsłudze różnych ustawień regionalnych (języki), w tym samym indeksie. Pola specyficzne dla ustawień regionalnych, należy można przypisać odpowiednie analizatora. Na przykład przypisywania analizatora francuskiej firmy Microsoft do pola zawierające francuskie ciągi. Ułatwia także filtrowanie. Jeśli wiesz, że zapytanie jest inicjowana na stronie fr-fr, można ograniczyć wyniki wyszukiwania do tego pola. Lub Utwórz [profil oceniania](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) aby dać więcej względną wagę pola. Usługa Azure Search obsługuje protokoły [50 analizatory języka](https://docs.microsoft.com/azure/search/search-language-support) do wyboru.

## <a name="next-steps"></a>Kolejne kroki

To pytanie dotyczące brakujących funkcji lub funkcje? Żądania funkcji na [witrynę User Voice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="see-also"></a>Zobacz także

 [StackOverflow: Azure Search](https://stackoverflow.com/questions/tagged/azure-search)   
 [Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Search](search-lucene-query-architecture.md)  
 [Co to jest usługa Azure Search?](search-what-is-azure-search.md)
