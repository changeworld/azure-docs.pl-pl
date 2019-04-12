---
title: Usługa Azure SQL Data Warehouse możliwości zarządzania i monitorowania — zapytania działanie i wykorzystanie zasobów | Dokumentacja firmy Microsoft
description: Dowiedz się, jakie funkcje są dostępne do zarządzania i monitorowania usługi Azure SQL Data Warehouse. Użyj witryny Azure portal i dynamicznych widoków zarządzania (DMV), aby zrozumieć działanie zapytania i wykorzystania zasobów magazynu danych.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/21/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: de5d8a3cd329e023b57d887a22db5459f13c7486
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59492791"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-sql-data-warehouse"></a>Monitorowanie aktywności wykorzystanie i kwerendy zasobów w usłudze Azure SQL Data Warehouse
Usługa Azure SQL Data Warehouse zapewnia rozbudowane funkcje monitorowania w witrynie Azure portal, aby udostępniać szczegółowe informacje do obciążenia magazynu danych. Witryna Azure portal jest zalecanym narzędziem podczas monitorowania magazynu danych, ponieważ zapewnia okresów przechowywania można skonfigurować, alerty, zalecenia i możliwe do dostosowania wykresów i pulpitów nawigacyjnych metryk i dzienników. Portal umożliwia także integrację z innymi usługami monitorowania platformy Azure np. Operations Management Suite (OMS) i Azure Monitor (Dzienniki), aby zapewnić kompleksowe środowisko monitorowania dla nie tylko magazyn danych, ale także całą analizy platformy Azure Platforma zintegrowane rozwiązanie monitorowania. W tej dokumentacji opisano, jakie funkcje monitorowania są dostępne do optymalizacji i zarządzać Twoją platformą analytics z usługą SQL Data Warehouse. 

## <a name="resource-utilization"></a>Wykorzystanie zasobów 
Następujące metryki są dostępne w witrynie Azure portal dla usługi SQL Data Warehouse. Te metryki są udostępniane za pośrednictwem [usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection#metrics).

| Nazwa metryki                           | Opis     | Typ agregacji |
| --------------------------------------- | ---------------- | --------------------------------------- |
| Procent użycia procesora CPU                          | Użycie procesora CPU, we wszystkich węzłach dla magazynu danych | Maksimum      |
| Procent użycia operacji we/wy na danych                      | Wykorzystanie operacji We/Wy między wszystkie węzły w magazynie danych | Maksimum   |
| Pomyślnie nawiązane połączenia                  | Liczba pomyślnie nawiązane połączenia danych | Łącznie            |
| Połączenia zakończone niepowodzeniem                      | Liczba połączeń nie powiodło się w magazynie danych | Łącznie            |
| Blokowane przez zaporę                     | Liczba logowań w magazynie danych, które zostało zablokowane | Łącznie            |
| Limit jednostek DWU                              | Cel poziomu usługi magazynu danych | Maksimum   |
| Procent jednostek DWU                          | Maksymalna między procent użycia procesora CPU i procent we/wy danych | Maksimum   |
| Używane jednostki DWU                                | Limit jednostek DWU * jednostek DWU, procent | Maksimum   |
| Procent liczby trafień pamięci podręcznej | (trafień w pamięci podręcznej / chybień w pamięci podręcznej) * 100, gdzie trafień w pamięci podręcznej jest sumą wszystkich trafień segmentów magazynu kolumn w lokalnej pamięci podręcznej dysków SSD, a trafienia pamięci podręcznej jest segmentów magazynu kolumn chybień w lokalnej pamięci podręcznej SSD sumowane we wszystkich węzłach | Maksimum |
| Pamięć podręczna używana wartość procentowa | (pamięć podręczna używana / pojemności w pamięci podręcznej) * 100, gdzie pamięć podręczna w użyciu jest sumie wszystkich bajtów w lokalnej pamięci podręcznej dysku SSD we wszystkich węzłach, a pojemności pamięci podręcznej jest sumą lokalny dysk SSD pojemność pamięci podręcznej we wszystkich węzłach | Maksimum |
| Wartość procentowa lokalnej bazy danych tempdb | Użycie lokalnej bazy danych tempdb na wszystkich węzłach obliczeniowych — wartości są emitowane co pięć minut | Maksimum |

## <a name="query-activity"></a>Aktywność zapytań
Środowisko programistyczne podczas monitorowania usługa SQL Data Warehouse przy użyciu języka T-SQL usługi zawiera zbiór dynamicznych widoków zarządzania (DMV). Widoki te są przydatne, gdy aktywnie Rozwiązywanie problemów i identyfikuje wąskich gardeł wydajności w odniesieniu do obciążenia.

Aby wyświetlić listę dynamicznych widoków zarządzania, które oferuje usługa SQL Data Warehouse, zapoznaj się z tym [dokumentacji](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Rejestrowanie diagnostyczne i metryki
Dzienniki i metryki mogą być eksportowane do usługi Azure Monitor specjalnie [dzienniki usługi Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) składnika i można programowo uzyskać dostęp za pośrednictwem [rejestrowania zapytań](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata). Opóźnienie dziennika dla usługi SQL Data Warehouse jest około 10 – 15 minut. Szczegółowe informacje na temat czynników wpływających na czas oczekiwania można znaleźć w następującej dokumentacji.


## <a name="next-steps"></a>Kolejne kroki
Następujące przewodniki z instrukcjami opisano typowe scenariusze i zastosowań podczas monitorowania i zarządzania magazynu danych:

- [Monitorowanie obciążenia magazynu danych przy użyciu widoków DMV](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)

