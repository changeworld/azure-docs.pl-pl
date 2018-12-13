---
title: Planowana konserwacja — Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przygotować w planowanych pracach konserwacyjnych do usługi Azure SQL Data Warehouse.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/19/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: c1e8f94a0131ace6354d070e932e414a1897260e
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2018
ms.locfileid: "53166306"
---
# <a name="planning-for-maintenance-on-your-azure-sql-data-warehouse"></a>Planowanie obsługi usługi Azure SQL data warehouse

Dowiedz się, jak przygotować się do zdarzenia planowanej konserwacji usługi Azure SQL data warehouse.

## <a name="what-is-a-planned-maintenance-event"></a>Co to jest zdarzenie planowanej konserwacji?
Zdarzenie planowanej konserwacji jest oknem czasu, gdy magazyn danych musi być w trybie offline dla operacji konserwacji. Te zdarzenia są możliwości stosowania uaktualnienia usługi, nowych funkcji ani poprawek. 

## <a name="frequency"></a>Częstotliwość
Średnio co najmniej jedno zdarzenie planowanej konserwacji występuje każdego miesiąca. 

## <a name="notifications-and-downtime"></a>Powiadomienia i przestojów
Otrzymasz powiadomienie przed każde zdarzenie planowanej konserwacji. Zdarzenia konserwacji anuluje wszystkie uruchomione zapytania i przełącza w tryb offline magazynu danych. Przewidywany czas przestoju dla każdego magazynu danych to około 30 minut. Można oczekiwać, że powiadomienie po zakończeniu konserwacji. 

## <a name="setting-up-alerts"></a>Konfigurowanie alertów

Firma Microsoft zaleca używanie [usługi Azure Monitor](../azure-monitor/platform/alerts-activity-log-service-notifications.md) skonfigurować planowanej konserwacji alertów dzienników. Alerty będą pomocne podczas planowania konserwacji wymagane zminimalizować wpływ na prowadzoną działalność. 

Aby skonfigurować powiadomienia, należy użyć tych [dziennika alertów instrukcje](../azure-monitor/platform/alerts-activity-log-service-notifications.md). 

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat monitorowania zobacz [monitorowanie obciążenia](sql-data-warehouse-manage-monitor.md).
