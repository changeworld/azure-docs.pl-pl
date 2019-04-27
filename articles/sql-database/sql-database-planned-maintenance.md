---
title: Planowanie konserwacji platformy Azure - zdarzenia usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przygotować się do zdarzenia planowanej konserwacji usługi Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
manager: craigg
ms.date: 01/30/2019
ms.openlocfilehash: 928338a911efae051df7164239dbd19f9317338a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60584611"
---
# <a name="planning-for-azure-maintenance-events-in-azure-sql-database"></a>Planowanie zdarzenia konserwacji platformy Azure w usłudze Azure SQL Database

Dowiedz się, jak przygotować w planowanych pracach konserwacyjnych na bazie danych Azure SQL.

## <a name="what-is-a-planned-maintenance-event"></a>Co to jest zdarzenie planowanej konserwacji

Dla każdej bazy danych bazy danych SQL Azure przechowuje kworum replik bazy danych, w której jedna replika jest serwerem podstawowym. W sytuacjach repliki podstawowej musi być Obsługa w trybie online, a co najmniej jedna replika pomocnicza musi być w dobrej kondycji. Podczas zaplanowanej konserwacji elementy członkowskie kworum bazy danych przechodzi w trybie offline pojedynczo, z zamiarem, że istnieje jeden odpowiada replikę podstawową i co najmniej jedna replika pomocnicza online zapewnienie przestojów klienta. Gdy replika podstawowa musi zostać przełączone do trybu offline, proces ponownej konfiguracji/trybu failover nastąpi, w której jedna replika pomocnicza staną się nową podstawową.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Czego można oczekiwać podczas zdarzeń planowanej konserwacji

Reconfigurations/przejścia w tryb failover zakończyła się zazwyczaj w ciągu 30 sekund — średnia jest 8 sekund. Jeśli już połączony, aplikację należy nawiązać ponownie połączenie do nowej repliki podstawowej kopii dobrej kondycji bazy danych. Nowe połączenie zostanie podjęta, gdy baza danych jest w trakcie zmiana konfiguracji przed nową replikę podstawową jest w trybie online, spowoduje wyświetlenie błędu 40613 (niedostępna baza danych): "Baza danych"{databasename}"na serwerze"{servername}"nie jest obecnie dostępna. Ponów próbę połączenia później. ". Jeśli baza danych zawiera wolno działające zapytanie, to zapytanie zostaną przerwane w trakcie zmiana konfiguracji i będzie wymagać ponownego uruchomienia.

## <a name="retry-logic"></a>Logika ponawiania próby

Każda aplikacja kliencka produkcji, który nawiązuje połączenie z usługą bazy danych w chmurze powinny implementować niezawodne połączenia [Logika ponawiania próby](sql-database-connectivity-issues.md#retry-logic-for-transient-errors). To pomoże rozwiązać tych sytuacji i ogólnie upewnić błędy przezroczysty dla użytkownika końcowego.

## <a name="frequency"></a>Częstotliwość

Średnio 1.7 zdarzenia planowanej konserwacji występują każdego miesiąca.

## <a name="resource-health"></a>Kondycja zasobów

Jeśli występują niepowodzenia logowania do usługi SQL database, sprawdź [Resource Health](../service-health/resource-health-overview.md#getting-started) okna [witryny Azure portal](https://portal.azure.com) dla bieżącego stanu. Sekcja Historia kondycji zawiera Przyczyna przestojów dla każdego zdarzenia (jeśli jest dostępna).


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [Resource Health](sql-database-resource-health.md) dla bazy danych SQL
- Aby uzyskać więcej informacji na temat logikę ponawiania próby zobacz [Logika ponawiania próby dla błędów przejściowych](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
