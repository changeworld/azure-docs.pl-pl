---
title: Query Performance Insight w usłudze Azure Database for PostgreSQL
description: W tym artykule opisano funkcję Query Performance Insight w usłudze Azure Database for PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: cc041104169ca8c4344b9d3de597283d122e63db
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47394764"
---
# <a name="query-performance-insight"></a>Szczegółowe informacje o wydajności zapytań 

**Dotyczy:** Azure Database for postgresql w warstwie 9.6 i 10

> [!IMPORTANT]
> Funkcja Query Performance Insight jest w publicznej wersji zapoznawczej w ograniczonej liczbie regionów. 

Query Performance Insight ułatwia szybkie identyfikowanie najdłużej działających zapytań są, jak zmieniają się wraz z upływem czasu i czeka, które mają wpływ na ich.

## <a name="permissions"></a>Uprawnienia
**Właściciel** lub **Współautor** uprawnień wymaganych do wyświetlenia tekstu zapytania w Query Performance Insight. **Czytnik** mogą przeglądać wykresy i tabele, ale nie tekst zapytania.

## <a name="prerequisites"></a>Wymagania wstępne
Aby uzyskać szczegółowe informacje o wydajności zapytań funkcji, dane muszą istnieć w [Query Store](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Przeglądanie szczegółowe informacje o wydajności
[Query Performance Insight](concepts-query-performance-insight.md) widok w witrynie Azure portal ujawni wizualizacji na informacje o kluczu z Query Store. 

Na stronie portalu usługi Azure Database for postgresql w warstwie serwera, wybierz **szczegółowe informacje o wydajności zapytań** w obszarze **pomoc techniczna i rozwiązywanie problemów z** części paska menu.

![Query Performance Insight długotrwałe zapytania](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

**Długotrwałych zapytań** karta przedstawia zapytania top 5, Średni czas trwania w oparciu o wykonanie, przedstawiona w oparciu o 15-minutowych interwałach. Możesz wyświetlić więcej zapytań, wybierając z **liczby zapytań** listy rozwijanej. Gdy to zrobisz, kolorów wykresu mogą ulec zmianie dla określonego Identyfikatora zapytania.

Można kliknąć i przeciągnąć na wykresie, aby zawęzić określonego okna czasowego. Alternatywnie użyj powiększenia in i out ikony, aby wyświetlić mniejszy lub większy okres czasu, odpowiednio.

W tabeli pod wykresem przedstawiono więcej informacji na temat długo wykonywane zapytania w tym przedziale czasowym.

Wybierz **statystyki oczekiwania** kartę, aby wyświetlić odpowiednie wizualizacje w tym czasie czeka na serwerze.

![Statystyki oczekiwania szczegółowych informacji o wydajności zapytań](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [monitorowania i dostrajania](concepts-monitoring.md) w usłudze Azure Database for PostgreSQL.


