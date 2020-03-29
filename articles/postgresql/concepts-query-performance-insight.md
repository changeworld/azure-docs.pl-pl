---
title: Query Performance Insight — usługa Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano funkcję wglądu w wydajność kwerendy w usłudze Azure Database for PostgreSQL — Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: dd5b4ec53d82421ddd9d680ca41e48eeecc43c2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768388"
---
# <a name="query-performance-insight"></a>Szczegółowe informacje o wydajności zapytań 

**Dotyczy:** Usługa Azure Database for PostgreSQL — pojedynczy serwer w wersjach 9.6, 10, 11

Query Performance Insight pomaga szybko zidentyfikować, jakie są najdłużej działające zapytania, jak zmieniają się w czasie i co czeka wpływa na nich.

## <a name="permissions"></a>Uprawnienia
Uprawnienia **Właściciel** lub **Współautor** są wymagane do wyświetlania tekstu zapytań w widoku Szczegółowe informacje o wydajności zapytań. **Czytelnik** może wyświetlać wykresy i tabele, ale nie tekst zapytań.

## <a name="prerequisites"></a>Wymagania wstępne
Aby wgląd w wydajność kwerend działał, dane muszą istnieć w [Magazynie zapytań](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Wyświetlanie szczegółowych informacji o wydajności
W widoku [Szczegółowe informacje o wydajności zapytań](concepts-query-performance-insight.md) w witrynie Azure Portal będą ujawniane wizualizacje kluczowych informacji z magazynu zapytań. 

Na stronie portalu usługi Azure Database for PostgreSQL wybierz opcję **Wgląd w wydajność kwerendy** w sekcji **Inteligentna wydajność** na pasku menu.

![Kwerendy w ucho.](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

Na karcie **Długo działające kwerendy** przedstawiono pięć pierwszych zapytań według średniego czasu trwania na wykonanie, agregowanych w odstępach 15-minutowych. Możesz wyświetlić więcej zapytań, wybierając wartość z listy rozwijanej **Liczba zapytań**. Gdy to zrobisz, kolory wykresu dla określonego identyfikatora zapytania mogą ulec zmianie.

Klikając i przeciągając na wykresie, możesz zawęzić go do konkretnego przedziału czasu. Możesz też użyć ikon powiększania i pomniejszania, aby wyświetlić odpowiednio mniejszy lub większy okres czasu.

Tabela poniżej wykresu zawiera więcej szczegółów na temat długotrwałych zapytań w tym oknie czasu.

Wybierz kartę **Statystyki oczekiwania**, aby wyświetlić odpowiednie wizualizacje oczekiwań na serwerze.

![Query Performance Insight czeka statystyki](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="considerations"></a>Zagadnienia do rozważenia
* Usługa Query Performance Insight nie jest dostępna dla [replik odczytu](concepts-read-replicas.md).

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [monitorowaniu i dostrajaniu](concepts-monitoring.md) w usłudze Azure Database for PostgreSQL.


