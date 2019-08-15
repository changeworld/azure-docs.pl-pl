---
title: Azure SQL Data Warehouse możliwości zarządzania i monitorowania — aktywność zapytań, wykorzystanie zasobów | Microsoft Docs
description: Dowiedz się, jakie funkcje są dostępne do monitorowania Azure SQL Data Warehouse i zarządzania nimi. Użyj Azure Portal i dynamicznych widoków zarządzania (widoków DMV), aby zrozumieć aktywność zapytań i wykorzystanie zasobów magazynu danych.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/09/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 7f7575daa91cef5cb5be6274a699323fafe67a68
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935132"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-sql-data-warehouse"></a>Monitorowanie działania użycia zasobów i zapytań w Azure SQL Data Warehouse
Azure SQL Data Warehouse zapewnia rozbudowane środowisko monitorowania w ramach Azure Portal do uzyskiwania szczegółowych informacji o obciążeniu magazynu danych. Azure Portal jest zalecanym narzędziem do monitorowania magazynu danych, ponieważ umożliwia on Konfigurowanie okresów przechowywania, alertów, zaleceń oraz dostosowywalnych wykresów i pulpitów nawigacyjnych na potrzeby metryk i dzienników. Portal umożliwia także integrację z innymi usługami monitorowania platformy Azure, takimi jak Operations Management Suite (OMS) i Azure Monitor (dzienniki), aby zapewnić kompleksowe środowisko monitorowania nie tylko dla magazynu danych, ale również całą analizę platformy Azure Platforma umożliwiająca zintegrowane środowisko monitorowania. W tej dokumentacji opisano, jakie funkcje monitorowania są dostępne, aby zoptymalizować platformę analityczną i zarządzać nią za pomocą SQL Data Warehouse. 

## <a name="resource-utilization"></a>Wykorzystanie zasobów 
Następujące metryki są dostępne w Azure Portal SQL Data Warehouse. Te metryki są naAzure Monitor [](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection#metrics).


| Nazwa metryki             | Opis                                                  | Typ agregacji |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| Procent użycia procesora CPU          | Użycie procesora CPU we wszystkich węzłach dla hurtowni danych      | Maksimum          |
| We/wy danych (procent)      | Użycie we/wy we wszystkich węzłach dla hurtowni danych       | Maksimum          |
| Procent pamięci       | Użycie pamięci (SQL Server) we wszystkich węzłach dla hurtowni danych | Maksimum          |
| Udane połączenia  | Liczba pomyślnych połączeń z danymi                 | Łącznie            |
| Połączenia zakończone niepowodzeniem      | Liczba nieudanych połączeń z magazynem danych           | Łącznie            |
| Zablokowane przez zaporę     | Liczba logowań do magazynu danych, który został zablokowany     | Łącznie            |
| Limit jednostek dwu               | Cel poziomu usługi hurtowni danych                | Maksimum          |
| JEDNOSTEK dwu procent          | Maksimum między wartością procentową procesora CPU a wartością procentową operacji we/wy        | Maksimum          |
| JEDNOSTEK dwu używane                | Limit jednostek dwu * jednostek dwu procent                                   | Maksimum          |
| Procent trafień w pamięci podręcznej    | (trafienia w pamięci podręcznej/chybień w pamięci podręcznej) * 100, gdzie trafienia pamięci podręcznej jest sumą wszystkich trafień segmentów magazynu kolumn w lokalnej pamięci podręcznej dysków SSD i Chybienia pamięci podręcznej to segmenty magazynu kolumn chybień w lokalnej pamięci | Maksimum          |
| Procent użycia pamięci podręcznej   | (użyta pamięć podręczna/pojemność pamięci podręcznej) * 100 gdzie używana pamięć podręczna jest sumą wszystkich bajtów w lokalnej pamięci podręcznej SSD między wszystkimi węzłami i pojemnością pamięci podręcznej jest sumą pojemności magazynu lokalnej pamięci podręcznej SSD dla wszystkich węzłów | Maksimum          |
| Procent lokalnej bazy danych tempdb | Użycie lokalnej bazy danych tempdb we wszystkich węzłach obliczeniowych — wartości są emitowane co pięć minut. | Maksimum          |

> Zagadnienia, które należy wziąć pod uwagę podczas przeglądania metryk i ustawień alertów:
>
> - Nie powiodło się i pomyślne połączenia są raportowane dla określonego magazynu danych — nie dla serwera logicznego

## <a name="query-activity"></a>Działanie zapytania
W przypadku środowiska programistycznego podczas monitorowania SQL Data Warehouse przy użyciu języka T-SQL Usługa udostępnia zestaw dynamicznych widoków zarządzania (widoków DMV). Te widoki są przydatne podczas aktywnego rozwiązywania problemów i identyfikowania wąskich gardeł wydajności w obciążeniu.

Aby wyświetlić listę widoków DMV, które zawiera SQL Data Warehouse, zapoznaj się z tą [dokumentacją](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Rejestrowanie metryk i diagnostyki
Wszystkie metryki i dzienniki można eksportować do Azure Monitor, w odniesieniu do składnika [dzienników Azure monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) , i można programowo uzyskać do nich dostęp za pomocą [zapytań dzienników](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata). Opóźnienie dziennika dla SQL Data Warehouse wynosi około 10-15 minut. Więcej informacji o czynnikach wpływających na opóźnienia można znaleźć w następującej dokumentacji.


## <a name="next-steps"></a>Następne kroki
Poniższe przewodniki dotyczące wykonywania opisują typowe scenariusze i przypadki użycia podczas monitorowania magazynu danych i zarządzania nim:

- [Monitorowanie obciążenia magazynu danych za pomocą widoków DMV](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)
