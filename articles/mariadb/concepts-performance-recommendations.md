---
title: Zalecenia dotyczące wydajności w usłudze Azure Database dla serwera MariaDB
description: W tym artykule opisano funkcję zalecenie dotyczące wydajności w usłudze Azure Database dla serwera MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: a2f9b7597022822272692d20976e1da654b9d524
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462054"
---
# <a name="performance-recommendations-in-azure-database-for-mariadb"></a>Zalecenia dotyczące wydajności w usłudze Azure Database dla serwera MariaDB

**Dotyczy:** Azure Database dla serwera MariaDB 10.2

> [!NOTE]
> Zalecenia dotyczące wydajności jest w wersji zapoznawczej.

Zalecenia dotyczące wydajności funkcji analizuje bazy danych w celu tworzenie niestandardowych sugestii w celu zwiększenia wydajności. Aby uzyskać zalecenia, analiza patrzy na różne cechy bazy danych, w tym schemacie. Włącz [Query Store](concepts-query-store.md) na serwerze, aby w pełni wykorzystać funkcję zalecenia dotyczące wydajności. Jeśli schemat wydajności jest WYŁĄCZONY, włączanie kwerenda Store umożliwia performance_schema i podzbiór dokumentów schematu wydajności wymaganych dla funkcji. Po zaimplementowaniu żadnych rekomendacji wydajności, należy przetestować wydajność, aby ocenić wpływ tych zmian.

## <a name="permissions"></a>Uprawnienia

Uprawnienia **Właściciel** lub **Współautor** są wymagane do uruchamiania analiz przy użyciu funkcji Zalecenia dotyczące wydajności.

## <a name="performance-recommendations"></a>Zalecenia dotyczące wydajności

Funkcja [Zalecenia dotyczące wydajności](concepts-performance-recommendations.md) analizuje obciążenia na serwerze, aby zidentyfikować indeksy z potencjałem poprawy wydajności.

Otwórz **zalecenia dotyczące wydajności** z **inteligentne wydajności** części paska menu na stronie portalu platformy Azure dla serwera MariaDB.

![Zalecenia dotyczące wydajności, strona docelowa](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Wybierz **analizy** i wybierz bazę danych, który rozpoczyna się analizy. W zależności od obciążenia analiza może potrwać kilka minut. Po zakończeniu analizy w portalu zostanie wyświetlone powiadomienie. Analiza wykonuje szczegółowe badanie bazy danych. Zalecane jest przeprowadzenie analizy w okresach poza szczytem.

**Zalecenia** okno zostanie wyświetlone, lista zaleceń, jeśli którekolwiek i identyfikator powiązanych zapytań, który wygenerował tego zalecenia. Z Identyfikatorem kwerendy, można użyć [mysql.query_store](concepts-query-store.md#mysqlquery_store) widok, aby dowiedzieć się więcej na temat zapytania.

![Nowa strona zalecenia dotyczące wydajności](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Zalecenia nie są automatycznie stosowane. Aby zastosować zalecenie, skopiuj tekst zapytania i uruchom go z wybranego klienta. Pamiętaj, aby przetestować i monitorowanie, aby ocenić zalecenia.

## <a name="recommendation-types"></a>Typami zaleceniu

Obecnie tylko *Create Index* zalecenia są obsługiwane.

### <a name="create-index-recommendations"></a>Utwórz zalecenia dotyczące indeksu

*Tworzenie indeksu* zalecenia Sugeruj nowe indeksy, aby przyspieszyć działanie najczęściej czasochłonne lub wykonywania zapytań w obciążeniu. Ten typ zalecenie wymaga [Query Store](concepts-query-store.md) włączenia. Query Store zbiera informacje o kwerendzie i zapewnia szczegółowe zapytania środowiska uruchomieniowego i częstotliwość statystycznych, które używa analizy, aby wykonać zalecenia.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [monitorowania i dostrajania](concepts-monitoring.md) w usłudze Azure Database dla serwera MariaDB.