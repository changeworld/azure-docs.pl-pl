---
title: Zaplanowanej konserwacji - Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przygotować do zdarzeń planowanych konserwacji do magazynu danych SQL Azure.
services: sql-data-warehouse
author: antvgski
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/19/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: a273ad4c256267865d3af324f0ef755a6cb75c5c
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="planning-for-maintenance-on-your-azure-sql-data-warehouse"></a>Planowanie konserwacji w magazynie danych Azure SQL

Dowiedz się, jak przygotować do zdarzeń planowanych konserwacji w magazynie danych Azure SQL.

## <a name="what-is-a-planned-maintenance-event"></a>Co to jest zdarzenie zaplanowanej konserwacji?
Zdarzeń planowanych konserwacji jest oknem czasu, gdy magazyn danych musi być w trybie offline dla operacji konserwacji. Te zdarzenia są możliwości stosowania uaktualnienia usługi, nowe funkcje i poprawki. 

## <a name="frequency"></a>Częstotliwość
Średnia co najmniej jedno zdarzenie zaplanowanej konserwacji występuje każdego miesiąca. 

## <a name="notifications-and-downtime"></a>Powiadomienia i przestoje
Otrzymasz powiadomienie przed każdym zaplanowanego zdarzenia konserwacji. Zdarzenia konserwacji anuluje wszystkie uruchomione zapytania i przejście magazynu danych w trybie offline. Przewidywany czas przestoju dla każdego data warehouse to około 30 minut. Można oczekiwać, że powiadomienie po zakończeniu obsługi. 

## <a name="setting-up-alerts"></a>Konfigurowanie alertów

Firma Microsoft zaleca używanie [Azure Monitor](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md) do konfigurowania planowanych konserwacji dziennika alerty. Alerty ułatwiają planowanie wymagane konserwacji zminimalizować wpływ dla Twojej firmy. 

Aby skonfigurować powiadomienia, należy użyć tych [dziennika alertu instrukcje](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md). 

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat monitorowania, zobacz [monitorować obciążenie](sql-data-warehouse-manage-monitor.md).
