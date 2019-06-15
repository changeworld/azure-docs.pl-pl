---
title: Query Performance Insight w usłudze Azure Database dla serwera MariaDB
description: W tym artykule opisano funkcję Query Performance Insight w usłudze Azure Database dla serwera MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: be9d5c4745cb03d9d3eaa324b7191d82b9d4a14e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079420"
---
# <a name="query-performance-insight-in-azure-database-for-mariadb"></a>Query Performance Insight w usłudze Azure Database dla serwera MariaDB

**Dotyczy:**  Azure Database dla serwera MariaDB 10.2

> [!NOTE]
> Query Performance Insight jest w wersji zapoznawczej. Obsługa Query Performance Insight w witrynie Azure portal jest wdrażana i mogą jeszcze być niedostępne w Twoim regionie.

Query Performance Insight ułatwia szybkie identyfikowanie najdłużej działających zapytań są, jak zmieniają się wraz z upływem czasu i czeka, które mają wpływ na ich.

## <a name="permissions"></a>Uprawnienia

**Właściciel** lub **Współautor** uprawnień wymaganych do wyświetlenia tekstu zapytania w Query Performance Insight. ** Czytnik** mogą przeglądać wykresy i tabele, ale nie tekst zapytania.

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać szczegółowe informacje o wydajności zapytań funkcji, dane muszą istnieć w [Query Store](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Przeglądanie szczegółowe informacje o wydajności

W widoku [Szczegółowe informacje o wydajności zapytań](concepts-query-performance-insight.md) w witrynie Azure Portal będą ujawniane wizualizacje kluczowych informacji z magazynu zapytań.

Na stronie portalu usługi Azure Database dla serwera MariaDB, wybierz **Query Performance Insight** w obszarze **inteligentne wydajności** części paska menu.

![Query Performance Insight długotrwałe zapytania](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

 **Długotrwałych zapytań** karta przedstawia zapytania top 5, Średni czas trwania w oparciu o wykonanie, przedstawiona w oparciu o 15-minutowych interwałach. Możesz wyświetlić więcej zapytań, wybierając z **liczby zapytań** listy rozwijanej. Gdy to zrobisz, kolory wykresu dla określonego identyfikatora zapytania mogą ulec zmianie.

Klikając i przeciągając na wykresie, możesz zawęzić go do konkretnego przedziału czasu. Alternatywnie użyj powiększenia in i out ikony, aby wyświetlić mniejszy lub większy okres odpowiednio.

Wybierz **statystyki oczekiwania** kartę, aby wyświetlić odpowiednie wizualizacje w tym czasie czeka na serwerze.

Wyświetlane w widoku statystyki oczekiwania zapytania są pogrupowane według zapytania, które wykazują największych czeka w określonym interwale.

![Query Performance Insight czeka statystyki](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [monitorowania i dostrajania](concepts-monitoring.md) w usłudze Azure Database dla serwera MariaDB.