---
title: Planowanie zdarzeń konserwacji platformy Azure
description: Dowiedz się, jak przygotować się do zdarzeń planowanej konserwacji w bazie danych SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
ms.date: 01/30/2019
ms.openlocfilehash: ba882176fbe17f7b74c786f421dde8fadd58d9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821316"
---
# <a name="planning-for-azure-maintenance-events-in-azure-sql-database"></a>Planowanie zdarzeń konserwacji platformy Azure w usłudze Azure SQL Database

Dowiedz się, jak przygotować się do zdarzeń planowanej konserwacji w bazie danych SQL platformy Azure.

## <a name="what-is-a-planned-maintenance-event"></a>Co to jest planowane zdarzenie konserwacyjne

Dla każdej bazy danych usługi Azure SQL DB przechowuje kworum replik bazy danych, gdzie jedna replika jest podstawowa. Przez cały czas replika podstawowa musi być obsługa online, a co najmniej jedna replika pomocnicza musi być w dobrej kondycji. Podczas planowanej konserwacji członkowie kworum bazy danych będą przechodzić w tryb offline po jednym na raz, z zamiarem, że istnieje jedna replika podstawowa i co najmniej jedna replika pomocnicza w trybie online, aby zapewnić brak przestojów klienta. Gdy replika podstawowa musi zostać przesunięta do trybu offline, wystąpi proces ponownej konfiguracji/pracy awaryjnej, w którym jedna replika pomocnicza stanie się nową repliką podstawową.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Czego można się spodziewać podczas planowanego wydarzenia konserwacyjnego

Ponowne konfiguracje/przerómienie awaryjne zwykle kończy się w ciągu 30 sekund — średnia wynosi 8 sekund. Jeśli już jest podłączony, aplikacja musi ponownie połączyć się z dobrej kondycji skopiować nową replikę podstawową bazy danych. Jeśli zostanie podjęta próba nowego połączenia podczas ponownej konfiguracji bazy danych przed przejściem nowej repliki podstawowej w tryb online, zostanie wyświetlony błąd 40613 (Baza danych niedostępna): "Baza danych '{databasename}' na serwerze '{servername}' nie jest obecnie dostępna. Ponów próbę połączenia później.". Jeśli baza danych ma długo działającą kwerendę, ta kwerenda zostanie przerwana podczas ponownej konfiguracji i będzie musiała zostać ponownie uruchomiona.

## <a name="retry-logic"></a>Logika ponawiania próby

Każda aplikacja produkcyjna klienta, która łączy się z usługą bazy danych w chmurze należy zaimplementować logikę [ponawiania ponawiania](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)połączenia. Pomoże to złagodzić te sytuacje i powinno ogólnie sprawić, że błędy będą przejrzyste dla użytkownika końcowego.

## <a name="frequency"></a>Częstotliwość

Średnio co miesiąc dochodzi do 1,7 planowanych zdarzeń konserwacyjnych.

## <a name="resource-health"></a>Kondycja zasobów

Jeśli w bazie danych SQL występują błędy logowania, sprawdź okno [Kondycja zasobów](../service-health/resource-health-overview.md#get-started) w [witrynie Azure portal](https://portal.azure.com) dla bieżącego stanu. Sekcja Historia kondycji zawiera przyczynę przestoju dla każdego zdarzenia (jeśli jest dostępna).


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [kondycji zasobów](sql-database-resource-health.md) dla bazy danych SQL
- Aby uzyskać więcej informacji na temat logiki ponawiania prób, zobacz [Ponawianie prób logiki błędów przejściowych](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
