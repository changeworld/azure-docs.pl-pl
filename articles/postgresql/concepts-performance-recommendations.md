---
title: Zalecenia dotyczące wydajności — usługa Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano funkcję zalecenia o wydajności w usłudze Azure Database for PostgreSQL — Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: a30af0c8bef47a37fe3439e885d3895a2c826225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768473"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql---single-server"></a>Zalecenia dotyczące wydajności w bazie danych platformy Azure dla postgreSQL — pojedynczy serwer

**Dotyczy:** Usługa Azure Database for PostgreSQL — pojedynczy serwer w wersjach 9.6, 10, 11

Funkcja Zalecenia dotyczące wydajności analizuje bazy danych, aby utworzyć niestandardowe sugestie w celu zwiększenia wydajności. Aby utworzyć zalecenia, analiza analizuje różne cechy bazy danych, w tym schematu. Włącz [Magazyn zapytań](concepts-query-store.md) na serwerze, aby w pełni korzystać z funkcji zalecenia dotyczące wydajności. Po zaimplementowanie wszelkich zaleceń dotyczących wydajności należy przetestować wydajność, aby ocenić wpływ tych zmian. 

## <a name="permissions"></a>Uprawnienia
Uprawnienia **Właściciel** lub **Współautor** są wymagane do uruchamiania analiz przy użyciu funkcji Zalecenia dotyczące wydajności.

## <a name="performance-recommendations"></a>Zalecenia dotyczące wydajności
Funkcja [Zalecenia dotyczące wydajności](concepts-performance-recommendations.md) analizuje obciążenia na serwerze, aby zidentyfikować indeksy z potencjałem poprawy wydajności.

Otwórz **zalecenia dotyczące wydajności** z sekcji **Inteligentna wydajność** na pasku menu na stronie portalu Azure dla serwera PostgreSQL.

![Zalecenia dotyczące wydajności, strona docelowa](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Wybierz **opcję Analizuj** i wybierz bazę danych, która rozpocznie analizę. W zależności od obciążenia th analizy może potrwać kilka minut, aby zakończyć. Po zakończeniu analizy w portalu zostanie wyświetlone powiadomienie. Analiza wykonuje głębokie badanie bazy danych. Zalecamy przeprowadzenie analizy w okresach poza szczytem. 

W oknie **Zalecenia** zostanie wyświetlona lista zaleceń, jeśli zostały znalezione.

![Zalecenia dotyczące skuteczności nowa strona](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Zalecenia nie są stosowane automatycznie. Aby zastosować zalecenie, skopiuj tekst kwerendy i uruchom go od wybranego klienta. Pamiętaj, aby przetestować i monitorować, aby ocenić zalecenie. 

## <a name="recommendation-types"></a>Typy rekomendacji

Obecnie obsługiwane są dwa typy zaleceń: *Tworzenie indeksu* i *indeksu upuszczania*.

### <a name="create-index-recommendations"></a>Tworzenie rekomendacji indeksu
*Tworzenie rekomendacji indeksu* sugerują nowe indeksy, aby przyspieszyć najczęściej uruchamiane lub czasochłonne zapytania w obciążeniu. Ten typ rekomendacji wymaga włączenia [magazynu zapytań.](concepts-query-store.md) Query Store zbiera informacje o kwerendach i udostępnia szczegółowe statystyki środowiska uruchomieniowego i częstotliwości kwerendy używane do sformułowania zalecenia.

### <a name="drop-index-recommendations"></a>Rekomendacje indeksu upuszczania
Oprócz wykrywania brakujących indeksów usługa Azure Database for PostgreSQL analizuje wydajność istniejących indeksów. Jeśli indeks jest rzadko używany lub nadmiarowy, analizator zaleca upuszczenie go.

## <a name="considerations"></a>Zagadnienia do rozważenia
* Zalecenia dotyczące wydajności nie są dostępne dla [replik odczytu](concepts-read-replicas.md).
## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [monitorowaniu i dostrajaniu](concepts-monitoring.md) w usłudze Azure Database for PostgreSQL.

