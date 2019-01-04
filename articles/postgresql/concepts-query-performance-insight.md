---
title: Query Performance Insight w usłudze Azure Database for PostgreSQL
description: W tym artykule opisano funkcję Query Performance Insight w usłudze Azure Database for PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: 1a71fb81acc91036ce12ff15f6b2762b808c7473
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2018
ms.locfileid: "53541623"
---
# <a name="query-performance-insight"></a>Szczegółowe informacje o wydajności zapytań 

**Dotyczy:** Azure Database for postgresql w warstwie 9.6 i 10

> [!IMPORTANT]
> Funkcja Query Performance Insight jest w publicznej wersji zapoznawczej. 

Query Performance Insight ułatwia szybkie identyfikowanie najdłużej działających zapytań są, jak zmieniają się wraz z upływem czasu i czeka, które mają wpływ na ich.

## <a name="permissions"></a>Uprawnienia
Uprawnienia **Właściciel** lub **Współautor** są wymagane do wyświetlania tekstu zapytań w widoku Szczegółowe informacje o wydajności zapytań. **Czytelnik** może wyświetlać wykresy i tabele, ale nie tekst zapytań.

## <a name="prerequisites"></a>Wymagania wstępne
Aby uzyskać szczegółowe informacje o wydajności zapytań funkcji, dane muszą istnieć w [Query Store](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Przeglądanie szczegółowe informacje o wydajności
W widoku [Szczegółowe informacje o wydajności zapytań](concepts-query-performance-insight.md) w witrynie Azure Portal będą ujawniane wizualizacje kluczowych informacji z magazynu zapytań. 

Na stronie portalu usługi Azure Database for postgresql w warstwie serwera, wybierz **szczegółowe informacje o wydajności zapytań** w obszarze **pomoc techniczna i rozwiązywanie problemów z** części paska menu.

![Query Performance Insight długotrwałe zapytania](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

Na karcie **Długotrwałe zapytania** jest wyświetlane pięć pierwszych zapytań według średniego czasu trwania na wykonanie, agregowanych w piętnastominutowych interwałach. Możesz wyświetlić więcej zapytań, wybierając wartość z listy rozwijanej **Liczba zapytań**. Gdy to zrobisz, kolory wykresu dla określonego identyfikatora zapytania mogą ulec zmianie.

Klikając i przeciągając na wykresie, możesz zawęzić go do konkretnego przedziału czasu. Alternatywnie użyj powiększenia in i out ikony, aby wyświetlić mniejszy lub większy okres czasu, odpowiednio.

W tabeli pod wykresem przedstawiono więcej informacji na temat długo wykonywane zapytania w tym przedziale czasowym.

Wybierz kartę **Statystyki oczekiwania**, aby wyświetlić odpowiednie wizualizacje oczekiwań na serwerze.

![Szczegółowe informacje o wydajności zapytań — statystyki oczekiwania](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [monitorowaniu i dostrajaniu](concepts-monitoring.md) w usłudze Azure Database for PostgreSQL.


