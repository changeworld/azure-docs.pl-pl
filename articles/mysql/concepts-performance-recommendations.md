---
title: Zalecenia dotyczące wydajności w Azure Database for MySQL
description: W tym artykule opisano funkcję rekomendacji wydajności w Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1371b286f5e9bd82345e17c11e910c68fac9fed2
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73603145"
---
# <a name="performance-recommendations-in-azure-database-for-mysql"></a>Zalecenia dotyczące wydajności w Azure Database for MySQL

**Dotyczy:** Azure Database for MySQL 5,7

Funkcja zalecenia dotyczące wydajności analizuje bazy danych w celu utworzenia niestandardowych sugestii zwiększających wydajność. Aby utworzyć zalecenia, analiza analizuje różne cechy bazy danych, w tym schemat. Włącz [Magazyn zapytań](concepts-query-store.md) na serwerze, aby w pełni wykorzystać funkcję zaleceń dotyczących wydajności. Jeśli schemat wydajności jest wyłączony, włączenie magazynu zapytań umożliwia performance_schema i podzbiór instrumentów schematu wydajności wymaganych przez funkcję. Po wdrożeniu wszelkich zaleceń dotyczących wydajności należy przetestować wydajność, aby oszacować wpływ tych zmian.

## <a name="permissions"></a>Uprawnienia

Uprawnienia **Właściciel** lub **Współautor** są wymagane do uruchamiania analiz przy użyciu funkcji Zalecenia dotyczące wydajności.

## <a name="performance-recommendations"></a>Zalecenia dotyczące wydajności

Funkcja [Zalecenia dotyczące wydajności](concepts-performance-recommendations.md) analizuje obciążenia na serwerze, aby zidentyfikować indeksy z potencjałem poprawy wydajności.

Zaleceń dotyczących **wydajności** w sekcji **Intelligent Performance** na pasku menu na stronie Azure Portal serwera MySQL.

![Zalecenia dotyczące wydajności, strona docelowa](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Wybierz pozycję **Analizuj** i wybierz bazę danych, która rozpocznie analizę. W zależności od obciążenia analiza może potrwać kilka minut. Po zakończeniu analizy w portalu zostanie wyświetlone powiadomienie. Analiza wykonuje głębokie badanie bazy danych. Zalecamy przeprowadzanie analiz poza okresami szczytu.

W oknie **rekomendacje** zostanie wyświetlona lista zaleceń, jeśli zostały znalezione i powiązanego identyfikatora zapytania, które wygenerowało to zalecenie. Korzystając z identyfikatora zapytania, można dowiedzieć się więcej o zapytaniu za pomocą widoku [MySQL. query_store](concepts-query-store.md#mysqlquery_store) .

![Zaleceń dotyczących wydajności — Nowa strona](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Zalecenia nie są automatycznie stosowane. Aby zastosować zalecenie, skopiuj tekst zapytania i uruchom go z wybranego klienta. Należy pamiętać o przetestowaniu i monitorowaniu w celu ocenienia zalecenia.

## <a name="recommendation-types"></a>Typy rekomendacji

Obecnie obsługiwane są tylko zalecenia dotyczące *tworzenia indeksów* .

### <a name="create-index-recommendations"></a>Tworzenie zaleceń dotyczących indeksów

*Tworzenie* zaleceń dotyczących indeksów Sugeruj nowe indeksy, aby przyspieszyć najczęściej wykonywane lub czasochłonne zapytania w obciążeniu. Ten typ rekomendacji wymaga włączenia [magazynu zapytań](concepts-query-store.md) . Magazyn zapytań zbiera informacje o zapytaniach i zawiera szczegółowe dane dotyczące środowiska uruchomieniowego zapytań i częstotliwości, których analiza używa do wykonania zalecenia.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [monitorowaniu i dostrajania](concepts-monitoring.md) w Azure Database for MySQL.