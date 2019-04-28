---
title: SQL Data Warehouse zalecenia — pojęcia | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o zaleceniach SQL Data Warehouse i jak są one generowane
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 11/05/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b275f23209979e1a8068ecd99465f7b52392bc6c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61421226"
---
# <a name="sql-data-warehouse-recommendations"></a>Zalecenia dotyczące usługi SQL Data Warehouse

W tym artykule opisano zalecenia dotyczące obsługiwanych przez program SQL Data Warehouse za pomocą usługi Azure Advisor.  

Usługa SQL Data Warehouse zapewnia zaleceń, aby zapewnić magazyn danych jest stale zoptymalizowana pod kątem wydajności. Zalecenia dotyczące magazynu danych są ściśle zintegrowane z [usługi Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) do przedstawienia najlepszych rozwiązań bezpośrednio w ramach [witryny Azure portal](https://aka.ms/Azureadvisor). Usługa SQL Data Warehouse analizuje bieżący stan magazynu danych, zbiera dane telemetryczne i powierzchnie zalecenia dotyczące obciążenia active codziennie. Scenariusze zalecenie dotyczące magazynu danych obsługiwane są przedstawione poniżej oraz sposobu stosowania zalecanych akcji.

Jeśli podzielić się opinią na Advisor magazynu danych SQL lub pojawią się problemy, skontaktuj się [ sqldwadvisor@service.microsoft.com ](mailto:sqldwadvisor@service.microsoft.com).   

Kliknij przycisk [tutaj](https://aka.ms/Azureadvisor) do sprawdzenia zalecenia już dziś! Obecnie ta funkcja ma zastosowanie do Gen2 tylko magazyny danych. 

## <a name="data-skew"></a>Niesymetryczność danych

Niesymetryczność danych może spowodować wąskie gardła przenoszenia lub zasób dodatkowe dane podczas uruchamiania obciążenia. Poniższa dokumentacja opisuje pokaz, aby zidentyfikować niesymetryczność danych i zapobiec sytuacji, wybierając klucza dystrybucji optymalne.

- [Identyfikowanie i usuwanie przesunięcia czasowego](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>Nie lub przestarzałymi statystyki

Prowadząc statystyki suboptymalny może poważnie obniżyć wydajność zapytań, ponieważ może to spowodować Optymalizator zapytań SQL Data Warehouse można wygenerować planów zapytań suboptymalny. Poniższa dokumentacja zawiera opis najlepszych rozwiązań dotyczących tworzenia i aktualizowania statystyk:

- [Tworzenie i aktualizowanie statystyk tabeli](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)

Aby wyświetlić listę tabel, których to dotyczy, według tych zaleceń, uruchom następujące polecenie [skryptu T-SQL](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables). Klasyfikator nieprzerwanie wykonuje ten sam skrypt języka T-SQL do wygenerowania tych zaleceń.

## <a name="replicate-tables"></a>Replikowane tabele

Zaleceń replikowanej tabeli Advisor wykrywa kandydatów tabeli na podstawie następujących właściwości fizyczne:

- Replikowane rozmiar tabeli
- Liczba kolumn
- Typ dystrybucji tabeli
- Liczba partycji

Advisor stale wykorzystuje oparte na obciążeniu Algorytm heurystyczny, takie jak częstotliwość dostępu do tabeli, średnio zwracane wiersze i progi wokół danych magazynu, rozmiar i działanie, aby upewnić się, że wysokiej jakości zalecenia są generowane. 

Poniżej opisano heurystyki na podstawie obciążenia, które można znaleźć w witrynie Azure portal. każde zalecenie dotyczące replikowanej tabeli:

- Skanowanie avg — średni procent wierszy, które zostały zwrócone z tabeli dla każdego dostępu tabeli w ciągu ostatnich siedmiu dni
- Odczyt częste, żadna aktualizacja — wskazuje, tabeli nie został zaktualizowany w ciągu ostatnich siedmiu dni, podczas wyświetlania działanie dostępu
- Współczynnik odczyt/aktualizowanie — stosunek jak często tabeli uzyskano względem gdy zostanie zaktualizowany w ciągu ostatnich siedmiu dni
- Działanie — mierzy użycie, w oparciu o działanie dostępu. To porównanie działania dostępu do tabeli względem tabeli średni działania dostępu w magazynie danych, w ciągu ostatnich siedmiu dni. 

Obecnie usługi Advisor będzie wyświetlana tylko co najwyżej kandydatów cztery replikowanej tabeli jednocześnie z klastrowane indeksy magazynu kolumn priorytetyzowanie najwyższy działania.

> [!IMPORTANT]
> Zalecenie dotyczące replikowanej tabeli nie jest pełna weryfikacja i nie uwzględnia operacje przenoszenia danych konta. Pracujemy nad dodaniem tym jako o heurystykę, ale do tego czasu należy zawsze sprawdzić, czy obciążenie po zastosowaniu rekomendacji. Skontaktuj się z pomocą sqldwadvisor@service.microsoft.com Jeśli użytkownik zauważy zalecenia dotyczące replikowanej tabeli, które powoduje, że obciążenia, aby zbadanie. Aby dowiedzieć się więcej na temat zreplikowane tabele, odwiedź poniższą [dokumentacji](https://docs.microsoft.com/azure/sql-data-warehouse/design-guidance-for-replicated-tables#what-is-a-replicated-table).
