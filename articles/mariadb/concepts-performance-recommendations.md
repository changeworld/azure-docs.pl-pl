---
title: Zalecenia dotyczące wydajności — usługa Azure Database for MariaDB
description: W tym artykule opisano funkcję zalecenia o wydajności w usłudze Azure Database for MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 838a4123bd5007f987f27674862409445967a2d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528102"
---
# <a name="performance-recommendations-in-azure-database-for-mariadb"></a>Zalecenia dotyczące wydajności w usłudze Azure Database for MariaDB

**Dotyczy:** Usługa Azure Database dla mariadb 10.2

Funkcja Zalecenia dotyczące wydajności analizuje bazy danych, aby utworzyć niestandardowe sugestie w celu zwiększenia wydajności. Aby utworzyć zalecenia, analiza analizuje różne cechy bazy danych, w tym schematu. Włącz [Magazyn zapytań](concepts-query-store.md) na serwerze, aby w pełni korzystać z funkcji zalecenia dotyczące wydajności. Jeśli schemat wydajności jest wyłączony, włączenie Query Store umożliwia performance_schema i podzbiór instrumentów schematu wydajności wymaganych dla tej funkcji. Po zaimplementowanie wszelkich zaleceń dotyczących wydajności należy przetestować wydajność, aby ocenić wpływ tych zmian.

## <a name="permissions"></a>Uprawnienia

Uprawnienia **Właściciel** lub **Współautor** są wymagane do uruchamiania analiz przy użyciu funkcji Zalecenia dotyczące wydajności.

## <a name="performance-recommendations"></a>Zalecenia dotyczące wydajności

Funkcja [Zalecenia dotyczące wydajności](concepts-performance-recommendations.md) analizuje obciążenia na serwerze, aby zidentyfikować indeksy z potencjałem poprawy wydajności.

Otwórz **zalecenia dotyczące wydajności** z sekcji **Inteligentna wydajność** na pasku menu na stronie portalu Azure dla serwera MariaDB.

![Zalecenia dotyczące wydajności, strona docelowa](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Wybierz **opcję Analizuj** i wybierz bazę danych, która rozpocznie analizę. W zależności od obciążenia analiza może potrwać kilka minut. Po zakończeniu analizy w portalu zostanie wyświetlone powiadomienie. Analiza wykonuje głębokie badanie bazy danych. Zalecamy przeprowadzenie analizy w okresach poza szczytem.

W oknie **Zalecenia** zostanie wyświetlona lista zaleceń, jeśli zostały znalezione, oraz identyfikator powiązanej kwerendy, który wygenerował to zalecenie. Za pomocą identyfikatora kwerendy można użyć widoku [mysql.query_store,](concepts-query-store.md#mysqlquery_store) aby dowiedzieć się więcej o kwerendzie.

![Zalecenia dotyczące skuteczności nowa strona](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Zalecenia nie są stosowane automatycznie. Aby zastosować zalecenie, skopiuj tekst kwerendy i uruchom go od wybranego klienta. Pamiętaj, aby przetestować i monitorować, aby ocenić zalecenie.

## <a name="recommendation-types"></a>Typy rekomendacji

Obecnie obsługiwane są tylko zalecenia *dotyczące tworzenia indeksu.*

### <a name="create-index-recommendations"></a>Tworzenie rekomendacji indeksu

*Tworzenie rekomendacji indeksu* sugerują nowe indeksy, aby przyspieszyć najczęściej uruchamiane lub czasochłonne zapytania w obciążeniu. Ten typ rekomendacji wymaga włączenia [magazynu zapytań.](concepts-query-store.md) Query Store zbiera informacje o kwerendach i udostępnia szczegółowe statystyki środowiska uruchomieniowego i częstotliwości kwerendy używane do sformułowania zalecenia.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [monitorowaniu i dostrajaniu](concepts-monitoring.md) w usłudze Azure Database for MariaDB.