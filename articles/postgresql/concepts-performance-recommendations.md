---
title: Zalecenia dotyczące wydajności w usłudze Azure Database for PostgreSQL
description: W tym artykule opisano funkcję zalecenie dotyczące wydajności w usłudze Azure Database for PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/28/2018
ms.openlocfilehash: c5324618eeda90b4ef1a512385fb2f14bf391215
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564462"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql"></a>Zalecenia dotyczące wydajności w bazie danych Azure database for PostgreSQL

**Dotyczy:** Azure Database for postgresql w warstwie 9.6 i 10

Zalecenia dotyczące wydajności funkcji analizy bazy danych w celu tworzenie niestandardowych sugestii w celu zwiększenia wydajności. Aby uzyskać zalecenia, analiza patrzy na różne cechy bazy danych, w tym schemacie. Włącz [Query Store](concepts-query-store.md) na serwerze, aby w pełni wykorzystać funkcję zalecenia dotyczące wydajności. Po zaimplementowaniu żadnych rekomendacji wydajności, należy przetestować wydajność, aby ocenić wpływ tych zmian. 

## <a name="permissions"></a>Uprawnienia
Uprawnienia **Właściciel** lub **Współautor** są wymagane do uruchamiania analiz przy użyciu funkcji Zalecenia dotyczące wydajności.

## <a name="performance-recommendations"></a>Zalecenia dotyczące wydajności
Funkcja [Zalecenia dotyczące wydajności](concepts-performance-recommendations.md) analizuje obciążenia na serwerze, aby zidentyfikować indeksy z potencjałem poprawy wydajności.

Otwórz **zalecenia dotyczące wydajności** z **inteligentne wydajności** części paska menu na stronie portalu platformy Azure dla Twojego serwera PostgreSQL.

![Zalecenia dotyczące wydajności, strona docelowa](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Wybierz **analizy** i wybierz bazę danych, który rozpoczyna się analizy. W zależności od obciążenia th analiza może potrwać kilka minut. Po zakończeniu analizy w portalu zostanie wyświetlone powiadomienie. Analiza wykonuje szczegółowe badanie bazy danych. Zalecane jest przeprowadzenie analizy w okresach poza szczytem. 

**Zalecenia** okno zostanie wyświetlone lista zaleceń, jeśli którekolwiek.

![Nowa strona zalecenia dotyczące wydajności](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Zalecenia nie są automatycznie stosowane. Aby zastosować zalecenie, skopiuj tekst zapytania i uruchom go z wybranego klienta. Pamiętaj, aby przetestować i monitorowanie, aby ocenić zalecenia. 

## <a name="recommendation-types"></a>Typami zaleceniu

Obecnie obsługiwane są dwa rodzaje zalecenia: *Tworzenie indeksu* i *Drop Index*.

### <a name="create-index-recommendations"></a>Utwórz zalecenia dotyczące indeksu
*Tworzenie indeksu* zalecenia Sugeruj nowe indeksy, aby przyspieszyć działanie najczęściej czasochłonne lub wykonywania zapytań w obciążeniu. Ten typ zalecenie wymaga [Query Store](concepts-query-store.md) włączenia. Query Store zbiera informacje o kwerendzie i zapewnia szczegółowe zapytania środowiska uruchomieniowego i częstotliwość statystycznych, które używa analizy, aby wykonać zalecenia.

### <a name="drop-index-recommendations"></a>Upuść zalecenia dotyczące indeksu
Oprócz wykrywania brakujące indeksy, — Azure Database for postgresql w warstwie analizuje wydajność istniejące indeksy. Jeśli indeks jest rzadko używane lub nadmiarowe, analizator zaleca porzuceniem jej.


## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [monitorowaniu i dostrajaniu](concepts-monitoring.md) w usłudze Azure Database for PostgreSQL.

