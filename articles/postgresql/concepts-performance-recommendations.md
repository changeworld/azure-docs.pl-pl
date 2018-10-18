---
title: Zalecenia dotyczące wydajności w usłudze Azure Database for PostgreSQL
description: W tym artykule opisano zalecenia dotyczące wydajności, z których jeden można uzyskać w usłudze Azure Database for PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: 46a4e69ecb08276e12ccc197de2d3ad838628b78
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49378605"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql"></a>Zalecenia dotyczące wydajności w bazie danych Azure database for PostgreSQL

**Dotyczy:** Azure Database for postgresql w warstwie 9.6 i 10

> [!IMPORTANT]
> Zalecenia dotyczące wydajności znajduje się w publicznej wersji zapoznawczej.

Zalecenia dotyczące wydajności funkcji identyfikuje najważniejsze indeksy, które mogą być tworzone w usługi Azure Database for postgresql w warstwie serwera w celu zwiększenia wydajności. Aby uzyskać zalecenia dotyczące indeksu, funkcja bierze pod uwagę różne cechy bazy danych, w tym jego schematu i obciążenia zgłoszonej Query Store. Po zaimplementowaniu żadnych rekomendacji wydajności, klientów należy przetestować wydajność, aby ocenić wpływ tych zmian. 

## <a name="permissions"></a>Uprawnienia
Uprawnienia **Właściciel** lub **Współautor** są wymagane do uruchamiania analiz przy użyciu funkcji Zalecenia dotyczące wydajności.

## <a name="performance-recommendations"></a>Zalecenia dotyczące wydajności
Funkcja [Zalecenia dotyczące wydajności](concepts-performance-recommendations.md) analizuje obciążenia na serwerze, aby zidentyfikować indeksy z potencjałem poprawy wydajności.

Otwórz **Zalecenia dotyczące wydajności** w sekcji **Pomoc techniczna i rozwiązywanie problemów** paska menu na stronie serwera PostgreSQL w witrynie Azure Portal.

![Zalecenia dotyczące wydajności, strona docelowa](./media/concepts-performance-recommendations/performance-recommendations-landing-page.png)

Wybierz opcję **Analiza** i wybierz bazę danych. Spowoduje to rozpoczęcie analizy. W zależności od obciążenia, może to potrwać kilka minut. Po zakończeniu analizy w portalu zostanie wyświetlone powiadomienie.

W oknie **Zalecenia dotyczące wydajności** zostanie wyświetlona lista zaleceń, jeśli jakieś znaleziono. Zalecenia będą zawierały informacje o odpowiedniej **bazie danych**, **tabeli**, **kolumnie** i **rozmiarze indeksu**.

![Nowa strona zalecenia dotyczące wydajności](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Aby wdrożyć zalecenie, skopiuj tekst zapytania i uruchom je z poziomu wybranego klienta.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [monitorowaniu i dostrajaniu](concepts-monitoring.md) w usłudze Azure Database for PostgreSQL.

