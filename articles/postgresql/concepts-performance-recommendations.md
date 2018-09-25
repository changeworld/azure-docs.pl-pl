---
title: Zalecenia dotyczące wydajności w usłudze Azure Database for PostgreSQL
description: W tym artykule opisano zalecenia dotyczące wydajności, z których jeden można uzyskać w usłudze Azure Database for PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 25ab2f735cfd4b0870729cb86992665fa8984580
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46976380"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql"></a>Zalecenia dotyczące wydajności w bazie danych Azure database for PostgreSQL

**Dotyczy:** Azure Database for postgresql w warstwie 9.6 i 10

> [!IMPORTANT]
> Zalecenia dotyczące wydajności znajduje się w publicznej wersji zapoznawczej.

Zalecenia dotyczące wydajności funkcji identyfikuje najważniejsze indeksy, które mogą być tworzone w usługi Azure Database for postgresql w warstwie serwera w celu zwiększenia wydajności. Aby uzyskać zalecenia dotyczące indeksu, funkcja bierze pod uwagę różne cechy bazy danych, w tym jego schematu i obciążenia zgłoszonej Query Store. Po zaimplementowaniu żadnych rekomendacji wydajności, klientów należy przetestować wydajność, aby ocenić wpływ tych zmian. 

## <a name="permissions"></a>Uprawnienia
**Właściciel** lub **Współautor** uprawnienia wymagane do uruchamiania analizy przy użyciu funkcji zalecenia dotyczące wydajności.

## <a name="performance-recommendations"></a>Zalecenia dotyczące wydajności
[Zalecenia dotyczące wydajności](concepts-performance-recommendations.md) funkcja analizuje obciążenia dla serwera do identyfikacji indeksów, z których można zwiększyć wydajność.

Otwórz **zalecenia dotyczące wydajności** z **pomoc techniczna i rozwiązywanie problemów z** części paska menu na stronie portalu platformy Azure dla Twojego serwera PostgreSQL.

![Zalecenia dotyczące wydajności, strona docelowa](./media/concepts-performance-recommendations/performance-recommendations-landing-page.png)

Wybierz **analizy** i wybierz bazę danych. Rozpoczyna się analizy. W zależności od obciążenia, może to potrwać kilka minut. Po zakończeniu analizy będą powiadomienia w portalu.

**Zalecenia dotyczące wydajności** okno zostanie wyświetlone lista zaleceń, jeśli którekolwiek. Zalecenia są wyświetlane informacje o odpowiednią **bazy danych**, **tabeli**, **kolumny**, i **rozmiar indeksu**.

![Nowa strona zalecenia dotyczące wydajności](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Do wykonania zalecenia, skopiuj tekst zapytania i uruchom go z wybranego klienta.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [monitorowania i dostrajania](concepts-monitoring.md) w usłudze Azure Database for PostgreSQL.

