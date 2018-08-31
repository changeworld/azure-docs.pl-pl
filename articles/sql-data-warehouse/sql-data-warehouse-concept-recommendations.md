---
title: SQL Data Warehouse zalecenia — pojęcia | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o zaleceniach SQL Data Warehouse i jak są one generowane
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 07/27/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 35ae3d1a8a6de2d348f90e2f55b732421b879917
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307641"
---
# <a name="sql-data-warehouse-recommendations"></a>Zalecenia dotyczące usługi SQL Data Warehouse

W tym artykule opisano zalecenia dotyczące obsługiwanych przez program SQL Data Warehouse za pomocą usługi Azure Advisor.  

Usługa SQL Data Warehouse zapewnia zaleceń, aby zapewnić magazyn danych jest stale zoptymalizowana pod kątem wydajności. Zalecenia dotyczące magazynu danych jest ściśle zintegrowany z [usługi Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) do przedstawienia najlepszych rozwiązań bezpośrednio w ramach [witryny Azure portal](https://aka.ms/Azureadvisor). Usługa SQL Data Warehouse analizuje bieżący stan magazynu danych, zbiera dane telemetryczne i powierzchnie zalecenia dotyczące obciążenia active codziennie. Scenariusze zalecenie dotyczące magazynu danych obsługiwane są przedstawione poniżej oraz sposobu stosowania zalecanych akcji.

Jeśli podzielić się opinią na Advisor magazynu danych SQL lub pojawią się problemy, skontaktuj się z [ sqldwadvisor@service.microsoft.com ](mailto:sqldwadvisor@service.microsoft.com).   

Kliknij przycisk [tutaj](https://aka.ms/Azureadvisor) do sprawdzenia zalecenia już dziś! Obecnie ta funkcja ma zastosowanie do Gen2 tylko magazyny danych. 

## <a name="data-skew"></a>Niesymetryczność danych

Niesymetryczność danych może spowodować wąskie gardła przenoszenia lub zasób dodatkowe dane podczas uruchamiania obciążenia. Poniższa dokumentacja opisuje pokaz, aby zidentyfikować niesymetryczność danych i zapobiec sytuacji, wybierając klucza dystrybucji optymalne.

- [Identyfikowanie i usuwanie przesunięcia czasowego](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>Nie lub przestarzałymi statystyki

Prowadząc statystyki suboptymalny może poważnie obniżyć wydajność zapytań, ponieważ może to spowodować Optymalizator zapytań SQL Data Warehouse można wygenerować planów zapytań suboptymalny. Poniższa dokumentacja zawiera opis najlepszych rozwiązań dotyczących tworzenia i aktualizowania statystyk:

- [Tworzenie i aktualizowanie statystyk tabeli](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistic)

Dla tych dwóch zaleceń doradcy stale działa następujące [skryptu T-SQL](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables) do identyfikacji tabel wpływ przesunięcia czasowego, statystyk i zalecenia.
