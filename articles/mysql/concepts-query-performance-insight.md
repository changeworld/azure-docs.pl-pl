---
title: Query Performance Insight w usłudze Azure Database for MySQL
description: W tym artykule opisano funkcję Query Performance Insight w usłudze Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: MySQL
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: fe6dce58714f8221625d13af1f8458662a19eaf6
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461754"
---
# <a name="query-performance-insight-in-azure-database-for-mysql"></a>Query Performance Insight w usłudze Azure Database for MySQL

**Dotyczy:**  Azure Database for MySQL 5.7

> [!NOTE]
> Query Performance Insight jest w wersji zapoznawczej.

Query Performance Insight ułatwia szybkie identyfikowanie najdłużej działających zapytań są, jak zmieniają się wraz z upływem czasu i czeka, które mają wpływ na ich.

## <a name="common-scenarios"></a>Typowe scenariusze

### <a name="long-running-queries"></a>Długotrwałe zapytania

- Identyfikowanie najdłuższy uruchamianie zapytań w przeszłości X godzin
- Identyfikowanie pierwszych N zapytań, które oczekują na zasoby
 
### <a name="wait-statistics"></a>Statystyki oczekiwania

- Omówienie charakteru oczekiwania dla zapytania
- Zrozumienie trendów dla zasobów w tym czasie czeka i której istnieje rywalizacji o zasoby

## <a name="permissions"></a>Uprawnienia

**Właściciel** lub **Współautor** uprawnień wymaganych do wyświetlenia tekstu zapytania w Query Performance Insight. ** Czytnik** mogą przeglądać wykresy i tabele, ale nie tekst zapytania.

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać szczegółowe informacje o wydajności zapytań funkcji, dane muszą istnieć w [Query Store](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Przeglądanie szczegółowe informacje o wydajności

W widoku [Szczegółowe informacje o wydajności zapytań](concepts-query-performance-insight.md) w witrynie Azure Portal będą ujawniane wizualizacje kluczowych informacji z magazynu zapytań.

Na stronie portalu usługi Azure Database for MySQL server, wybierz **Query Performance Insight** w obszarze **inteligentne wydajności** części paska menu.

### <a name="long-running-queries"></a>Długotrwałe zapytania

 **Długotrwałych zapytań** karta przedstawia zapytania top 5, Średni czas trwania w oparciu o wykonanie, przedstawiona w oparciu o 15-minutowych interwałach. Możesz wyświetlić więcej zapytań, wybierając z **liczby zapytań** listy rozwijanej. Gdy to zrobisz, kolory wykresu dla określonego identyfikatora zapytania mogą ulec zmianie.

Klikając i przeciągając na wykresie, możesz zawęzić go do konkretnego przedziału czasu. Alternatywnie użyj powiększenia in i out ikony, aby wyświetlić mniejszy lub większy okres odpowiednio.

![Query Performance Insight długotrwałe zapytania](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png) 

### <a name="wait-statistics"></a>Statystyki oczekiwania

> [!NOTE]
> Statystyki oczekiwania są przeznaczony do rozwiązywania problemów z wydajnością zapytań. Zalecane jest włączone tylko na potrzeby rozwiązywania problemów.

Statystyki oczekiwania zawiera widok zdarzeń oczekiwania, które występują podczas wykonywania określonej kwerendy. Dowiedz się więcej na temat typów zdarzeń oczekiwania w [dokumentację aparatu MySQL](https://go.microsoft.com/fwlink/?linkid=2098206).

Wybierz **statystyki oczekiwania** kartę, aby wyświetlić odpowiednie wizualizacje w tym czasie czeka na serwerze.

Wyświetlane w widoku statystyki oczekiwania zapytania są pogrupowane według zapytania, które wykazują największych czeka w określonym interwale.

![Query Performance Insight czeka statystyki](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [monitorowania i dostrajania](concepts-monitoring.md) w usłudze Azure Database for MySQL.