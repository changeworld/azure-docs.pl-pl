---
title: Query Store scenariusze użycia w usłudze Azure Database for PostgreSQL
description: W tym artykule opisano niektóre scenariusze Store zapytania w usłudze Azure Database for PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/26/2018
ms.openlocfilehash: 873462354b70d13e56ca108c3257031ef34873f8
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58480858"
---
# <a name="usage-scenarios-for-query-store"></a>Scenariusze użycia dotyczące Query Store

**Dotyczy:** Azure Database for postgresql w warstwie 9.6 i 10

Możesz użyć Store zapytania w wielu różnych scenariuszy, w których śledzenie i zachowując wydajność przewidywalnego obciążenia ma kluczowe znaczenie. Należy wziąć pod uwagę następujące przykłady: 
- Identyfikowanie i dostosowywanie najpopularniejsze kosztowne zapytania 
- A / B, testowanie 
- Zachowanie wydajności stabilne podczas uaktualniania 
- Identyfikowanie i ulepszanie obciążeń ad-hoc 

## <a name="identify-and-tune-expensive-queries"></a>Identyfikowanie i dostosowywanie kosztownych zapytań 

### <a name="identify-longest-running-queries"></a>Identyfikowanie najdłuższy uruchomionych zapytań 
Użyj [Query Performance Insight](concepts-query-performance-insight.md) widok w witrynie Azure portal w celu szybkiego identyfikowania najdłuższy uruchamianie zapytań. Te zapytania są zwykle zwykle zużywać znaczną ilość zasobów. Optymalizacja najdłużej działających pytania może poprawić wydajność przy zwalnianiu zasobów przeznaczonych do użycia przez inne zapytania w systemie. 

### <a name="target-queries-with-performance-deltas"></a>Docelowy zapytania z różnic w wydajności 
Query Store dzieli dane dotyczące wydajności na okna czasowe, dzięki czemu można śledzić wydajność zapytań, wraz z upływem czasu. Pomaga to identyfikować dokładnie które zapytania są przyczyniające się do wzrostu całkowity czas spędzony. W rezultacie można zrobić, docelowych Rozwiązywanie problemów z obciążeń.

### <a name="tuning-expensive-queries"></a>Dostrajanie kosztownych zapytań 
Po zidentyfikowaniu zapytania z wydajnością suboptymalny akcję, którą należy wykonać zależy od rodzaju problem: 
- Użyj [zalecenia dotyczące wydajności](concepts-performance-recommendations.md) ustalenie, jeśli istnieją sugerowane indeksów. Jeśli tak, Utwórz indeks, a następnie użyj Query Store, aby ocenić wydajność zapytań, po utworzeniu indeksu. 
- Upewnij się, że statystyki są aktualne dla tabel użyta przez zapytanie.
- Należy rozważyć ponowne napisanie zapytania kosztowne. Na przykład korzystać z zalet parametryzacji zapytań i zmniejszyć użycie dynamiczny język SQL. Podczas odczytywania danych, takich jak stosowanie danych filtrowania po stronie bazy danych, nie znajduje się na stronie aplikacji, należy zaimplementować logikę optymalne. 


## <a name="ab-testing"></a>A / B, testowanie 
Użyj Query Store Aby porównać wydajność obciążeń przed i po zmianie aplikacji, które planujesz wprowadzić. Przykładowe scenariusze dotyczące używania Query Store w celu oceny wpływu środowiska lub aplikacji, Zmień na wydajność obciążenia: 
- Wprowadza nową wersję aplikacji. 
- Dodawanie dodatkowych zasobów do serwera. 
- Tworzenie brakujące indeksy tabel odwołują się kosztowne zapytania. 
 
W dowolnym z tych scenariuszy Zastosuj poniższy przepływ pracy: 
1. Uruchom obciążenia za pomocą Query Store przed planowanej zmiany w celu wygenerowania punkt odniesienia wydajności. 
2. Zastosowanie zmian aplikacji na kontrolowany moment w czasie. 
3. Kontynuuj, obciążenie czas wystarczający do generowania obrazu wydajności systemu po zmianie. 
4. Porównaj wyniki z przed i po zmianie. 
5. Zdecyduj, czy zachować zmiany lub wycofywania. 


## <a name="identify-and-improve-ad-hoc-workloads"></a>Identyfikuj i Zwiększaj obciążeń ad-hoc 
Niektórych obciążeń dominujący zapytania, które można dostosować wydajność ogólną aplikacji nie jest konieczne. Te obciążenia są zazwyczaj określony przy użyciu stosunkowo dużej liczby zapytań unikatowy każdego z nich wykorzystywanie część zasobów systemowych. Każdy unikatowy zapytanie jest wykonywane, rzadko, więc osobno, ich użycie środowiska uruchomieniowego nie jest krytyczny. Z drugiej strony biorąc pod uwagę, że aplikacja jest generowanie nowych zapytań przez cały czas, znaczna część zasobów systemowych odbywa się na kompilację, które nie są optymalne. Zazwyczaj ta sytuacja ma miejsce, jeśli aplikacja generuje zapytania (zamiast przy użyciu procedur składowanych i sparametryzowanych zapytań), lub jeśli zależy od struktury mapowania obiektowo relacyjny, które generują zapytania domyślnie. 
 
Jeśli masz kontrolę nad tym kod aplikacji, można rozważyć ponowne napisanie warstwy dostępu do danych, aby użyć procedur składowanych i sparametryzowanych zapytań. Jednak takiej sytuacji można również zwiększyć bez wprowadzania zmian w aplikacji, wymuszając parametryzacji zapytań dla całej bazy danych (wszystkie zapytania) lub szablonów poszczególnych zapytań, za pomocą tego samego skrótu zapytania. 

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [najlepsze rozwiązania dotyczące korzystania z zapytania Store](concepts-query-store-best-practices.md)