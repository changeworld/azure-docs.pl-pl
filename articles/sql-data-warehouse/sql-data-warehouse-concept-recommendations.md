---
title: Zalecenia dotyczące analizy SQL
description: Informacje na temat zaleceń dotyczących analizy SQL i sposobu ich generowania
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/05/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 5471236c09737eeef2d4cb7542c245d3087e726c
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195961"
---
# <a name="sql-analytics-recommendations"></a>Zalecenia dotyczące analizy SQL

W tym artykule opisano zalecenia dotyczące analizy SQL obsługiwane za pomocą Azure Advisor.  

Analiza SQL udostępnia zalecenia, aby zapewnić ciągłą optymalizację obciążenia magazynu danych pod kątem wydajności. Zalecenia są ściśle zintegrowane z [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) , aby zapewnić najlepszym praktykom bezpośrednio w ramach [Azure Portal](https://aka.ms/Azureadvisor). Usługa SQL Analytics zbiera zalecenia dotyczące danych telemetrycznych i powierzchni dla aktywnego obciążenia w codziennym erze. Obsługiwane scenariusze rekomendacji opisano poniżej wraz z sposobem zastosowania zalecanych akcji.

Możesz [sprawdzić swoje rekomendacje](https://aka.ms/Azureadvisor) już dzisiaj! Obecnie ta funkcja ma zastosowanie tylko do magazynów danych Gen2. 

## <a name="data-skew"></a>Pochylenie danych

Pochylenie danych może spowodować dodatkowe wąskie gardła lub zasoby związane z zasobami podczas uruchamiania obciążenia. Poniższa dokumentacja zawiera opis pochylenia danych i uniemożliwia jego zachowanie, wybierając optymalny klucz dystrybucji.

- [Zidentyfikuj i Usuń pochylenie](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>Nieaktualne dane statystyczne

Posiadanie nieoptymalnych statystyk może poważnie wpływać na wydajność zapytań, ponieważ może to spowodować, że optymalizator zapytań SQL generuje nieoptymalne plany zapytań. W poniższej dokumentacji opisano najlepsze rozwiązania dotyczące tworzenia i aktualizowania statystyk:

- [Tworzenie i aktualizowanie statystyk tabeli](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)

Aby wyświetlić listę tabel, których dotyczy problem, należy uruchomić następujący [skrypt T-SQL](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables). Program Advisor ciągle uruchamia ten sam skrypt T-SQL w celu wygenerowania tych zaleceń.

## <a name="replicate-tables"></a>Replikowanie tabel

W przypadku zaleceń dotyczących zreplikowanej tabeli program Advisor wykrywa kandydatów tabeli na podstawie następujących cech fizycznych:

- Rozmiar zreplikowanej tabeli
- Liczba kolumn
- Typ dystrybucji tabeli
- Liczba partycji

Doradca ciągle korzysta z algorytmów heurystycznych opartych na obciążeniu, takich jak częstotliwość dostępu do tabel, wiersze zwrócone na średnim i progi wokół rozmiaru i aktywności magazynu danych w celu zapewnienia, że generowane są zalecenia o wysokiej jakości. 

Poniżej opisano algorytmy heurystyczne oparte na obciążeniu, które mogą znajdować się w Azure Portal dla każdego zalecenia zreplikowanej tabeli:

- Skanowanie Średni — Średni procent wierszy zwróconych z tabeli dla każdego dostępu do tabeli w ciągu ostatnich siedmiu dni
- Często odczytywane, brak aktualizacji — wskazuje, że tabela nie została zaktualizowana w ciągu ostatnich siedmiu dni podczas wyświetlania działania dostępu
- Współczynnik odczytu/aktualizacji — stosunek częstotliwości uzyskiwania dostępu do tabeli względem czasu, gdy zostanie on zaktualizowany w ciągu ostatnich siedmiu dni
- Działanie — mierzy użycie na podstawie działania dostępu. Porównuje to działanie dostępu do tabel względem średniej aktywności dostępu do tabeli w magazynie danych w ciągu ostatnich siedmiu dni. 

Obecnie w usłudze Advisor będą widoczne tylko cztery kandydujące zreplikowane tabele jednocześnie z klastrowanymi indeksami magazynu kolumn o priorytetach o najwyższej aktywności.

> [!IMPORTANT]
> Zalecenie zreplikowanej tabeli nie jest pełną próbą i nie uwzględnia operacji przenoszenia danych. Pracujemy nad dodaniem tego elementu jako algorytmu heurystycznego, ale w międzyczasie należy zawsze sprawdzić poprawność obciążenia po zastosowaniu zalecenia. Skontaktuj się z sqldwadvisor@service.microsoft.com, jeśli odkryjesz zreplikowane tabele, które powodują przeprowadzenie obciążenia. Aby dowiedzieć się więcej na temat zreplikowanych tabel, zapoznaj się z poniższą [dokumentacją](https://docs.microsoft.com/azure/sql-data-warehouse/design-guidance-for-replicated-tables#what-is-a-replicated-table).
