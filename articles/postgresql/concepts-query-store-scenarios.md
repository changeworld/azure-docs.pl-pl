---
title: Scenariusze magazynu zapytań — usługa Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano niektóre scenariusze dla magazynu zapytań w usłudze Azure Database dla postgreSQL — pojedynczy serwer.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 31e3f82b6ea1b1fc15c0832dc03edce2a59f1e1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768354"
---
# <a name="usage-scenarios-for-query-store"></a>Scenariusze użycia dla Magazynu zapytań

**Dotyczy:** Usługa Azure Database for PostgreSQL — pojedynczy serwer w wersjach 9.6, 10, 11

Można użyć Query Store w wielu różnych scenariuszach, w których śledzenie i utrzymywanie przewidywalnej wydajności obciążenia ma kluczowe znaczenie. Rozważmy następujące przykłady: 
- Identyfikowanie i dostrajanie najdroższych zapytań 
- Testowanie A/B 
- Utrzymanie stabilnej wydajności podczas aktualizacji 
- Identyfikowanie i ulepszanie obciążeń ad hoc 

## <a name="identify-and-tune-expensive-queries"></a>Identyfikowanie i dostrajanie kosztownych zapytań 

### <a name="identify-longest-running-queries"></a>Identyfikowanie najdłużej działających zapytań 
Użyj widoku [wgląd wydajności kwerend](concepts-query-performance-insight.md) w witrynie Azure portal, aby szybko zidentyfikować najdłużej działające kwerendy. Te zapytania zazwyczaj mają tendencję do zużywają znaczną ilość zasobów. Optymalizacja najdłużej działających pytań może zwiększyć wydajność, zwalniając zasoby do użycia przez inne zapytania uruchomione w systemie. 

### <a name="target-queries-with-performance-deltas"></a>Zapytania docelowe z deltami wydajności 
Query Store wcina dane o wydajności w okna czasowe, dzięki czemu można śledzić wydajność kwerendy w czasie. Pomaga to dokładnie określić, które zapytania przyczyniają się do zwiększenia całkowitego czasu spędzonego. W rezultacie można wykonać ukierunkowane rozwiązywanie problemów z obciążeniem.

### <a name="tuning-expensive-queries"></a>Dostrajanie kosztownych zapytań 
Po zidentyfikowaniu kwerendy o nieoptymalnej skuteczności, podejmowana akcja zależy od charakteru problemu: 
- Użyj [zalecenia dotyczące wydajności,](concepts-performance-recommendations.md) aby ustalić, czy istnieją jakieś sugerowane indeksy. Jeśli tak, utwórz indeks, a następnie użyj Query Store do oceny wydajności kwerendy po utworzeniu indeksu. 
- Upewnij się, że statystyki są aktualne dla tabel podstawowych używanych przez kwerendę.
- Rozważ przepisanie kosztownych zapytań. Na przykład skorzystaj z parametryzacji zapytania i zmniejsz użycie dynamicznego języka SQL. Implementuj optymalną logikę podczas odczytywania danych, takich jak stosowanie filtrowania danych po stronie bazy danych, a nie po stronie aplikacji. 


## <a name="ab-testing"></a>Testowanie A/B 
Użyj Query Store, aby porównać wydajność obciążenia przed i po zmianie aplikacji, którą zamierzasz wprowadzić. Przykłady scenariuszy użycia Magazynu zapytań do oceny wpływu zmiany środowiska lub aplikacji na wydajność obciążenia: 
- Wdrażanie nowej wersji aplikacji. 
- Dodawanie dodatkowych zasobów do serwera. 
- Tworzenie brakujących indeksów w tabelach, do których odwołują się kosztowne kwerendy. 
 
W każdym z tych scenariuszy zastosuj następujący przepływ pracy: 
1. Uruchom obciążenie z Magazynu zapytań przed planowaną zmianą, aby wygenerować linię bazową wydajności. 
2. Zastosuj zmiany aplikacji w kontrolowanym momencie w czasie. 
3. Kontynuuj uruchamianie obciążenia wystarczająco długo, aby wygenerować obraz wydajności systemu po zmianie. 
4. Porównaj wyniki sprzed i po zmianie. 
5. Zdecyduj, czy zachować zmianę, czy wycofać. 


## <a name="identify-and-improve-ad-hoc-workloads"></a>Identyfikowanie i ulepszanie obciążeń ad hoc 
Niektóre obciążenia nie mają dominujących zapytań, które można dostroić, aby poprawić ogólną wydajność aplikacji. Te obciążenia zazwyczaj charakteryzują się stosunkowo dużą liczbą unikatowych zapytań, z których każdy zużywa część zasobów systemowych. Każde unikatowe zapytanie jest wykonywane rzadko, więc indywidualnie ich zużycie środowiska uruchomieniowego nie jest krytyczne. Z drugiej strony, biorąc pod uwagę, że aplikacja generuje nowe zapytania przez cały czas, znaczna część zasobów systemowych jest wydawana na kompilację kwerend, co nie jest optymalne. Zazwyczaj taka sytuacja ma miejsce, jeśli aplikacja generuje kwerendy (zamiast przy użyciu procedur przechowywanych lub sparametryzowanych kwerend) lub jeśli opiera się na strukturach mapowania obiektowego, które domyślnie generują kwerendy. 
 
Jeśli masz kontrolę nad kodem aplikacji, możesz rozważyć przepisanie warstwy dostępu do danych w celu użycia procedur przechowywanych lub sparametryzowanych zapytań. Jednak tę sytuację można również poprawić bez zmian aplikacji, wymuszając parametryzację kwerend dla całej bazy danych (wszystkie kwerendy) lub dla poszczególnych szablonów zapytań z tym samym hash kwerendy. 

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [sprawdzonych praktykach dotyczących korzystania z Magazynu zapytań](concepts-query-store-best-practices.md)