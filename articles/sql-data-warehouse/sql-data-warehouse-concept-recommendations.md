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
ms.openlocfilehash: 57bce631a570f549d46a9b0beefcb5adce4decfc
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44380118"
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

Aby wyświetlić listę tabel, których to dotyczy, według tych zaleceń, uruchom następujące polecenie [skryptu T-SQL](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables). Doradcy nieprzerwanie wykonuje ten sam skrypt języka T-SQL do wygenerowania tych zaleceń.
