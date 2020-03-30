---
title: Query Performance Insight — usługa Azure Database for MariaDB
description: W tym artykule opisano funkcję wglądu w dane dotyczące wydajności kwerend w usłudze Azure Database for MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 88777ee44551ed6abdb7a6c7c909d6bf55db48c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527848"
---
# <a name="query-performance-insight-in-azure-database-for-mariadb"></a>Analiza wydajności zapytań w usłudze Azure Database for MariaDB

**Dotyczy:** Usługa Azure Database dla mariadb 10.2

Query Performance Insight pomaga szybko zidentyfikować, jakie są najdłużej działające zapytania, jak zmieniają się w czasie i co czeka wpływa na nich.

## <a name="common-scenarios"></a>Typowe scenariusze

### <a name="long-running-queries"></a>Długotrwałe zapytania

- Identyfikowanie najdłużej działających zapytań w ciągu ostatnich X godzin
- Identyfikowanie najlepszych kwerend N oczekujących na zasoby
 
### <a name="wait-statistics"></a>Statystyki oczekiwania

- Opis charakteru oczekiwania na kwerendę
- Opis trendów oczekiwania na zasoby i miejsca, w którym istnieje rywalizacja o zasoby

## <a name="permissions"></a>Uprawnienia

Uprawnienia **Właściciel** lub **Współautor** są wymagane do wyświetlania tekstu zapytań w widoku Szczegółowe informacje o wydajności zapytań. **Czytelnik** może wyświetlać wykresy i tabele, ale nie tekst zapytań.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wgląd w wydajność kwerend działał, dane muszą istnieć w [Magazynie zapytań](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Wyświetlanie szczegółowych informacji o wydajności

W widoku [Szczegółowe informacje o wydajności zapytań](concepts-query-performance-insight.md) w witrynie Azure Portal będą ujawniane wizualizacje kluczowych informacji z magazynu zapytań.

Na stronie portalu serwera usługi Azure Database dla mariadb wybierz opcję **Wgląd wydajności zapytań** w sekcji **Inteligentna wydajność** na pasku menu.

### <a name="long-running-queries"></a>Długotrwałe zapytania

Na karcie **Długo działające kwerendy** przedstawiono 5 najlepszych zapytań według średniego czasu trwania na wykonanie, agregowanych w odstępach 15-minutowych. Można wyświetlić więcej zapytań, wybierając z **listy rozwijanej Liczba zapytań.** Gdy to zrobisz, kolory wykresu dla określonego identyfikatora zapytania mogą ulec zmianie.

Klikając i przeciągając na wykresie, możesz zawęzić go do konkretnego przedziału czasu. Możesz też użyć ikon powiększania i pomniejszania, aby wyświetlić odpowiednio mniejszy lub większy okres czasu.

![Kwerendy w ucho.](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

### <a name="wait-statistics"></a>Statystyki oczekiwania 

> [!NOTE]
> Statystyki oczekiwania są przeznaczone do rozwiązywania problemów z wydajnością kwerendy. Zaleca się, aby włączyć tylko do celów rozwiązywania problemów. <br>Jeśli zostanie wyświetlony komunikat o błędzie w witrynie Azure portal "*Problem napotkany dla 'Microsoft.DBforMariaDB'; nie może spełnić żądania. Jeśli ten problem będzie się powtarzał lub jest nieoczekiwany, skontaktuj się z pomocą techniczną z tymi informacjami.*" podczas przeglądania statystyk oczekiwania należy użyć mniejszego okresu.

Statystyki oczekiwania zapewnia widok zdarzeń oczekiwania, które występują podczas wykonywania określonej kwerendy. Dowiedz się więcej o typach zdarzeń oczekiwania w [dokumentacji aparatu MySQL](https://go.microsoft.com/fwlink/?linkid=2098206).

Wybierz kartę **Statystyki oczekiwania**, aby wyświetlić odpowiednie wizualizacje oczekiwań na serwerze.

Kwerendy wyświetlane w widoku statystyk oczekiwania są pogrupowane według kwerend, które wykazują największe oczekiwania w określonym przedziale czasu.

![Query Performance Insight czeka statystyki](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [monitorowaniu i dostrajaniu](concepts-monitoring.md) w usłudze Azure Database for MariaDB.