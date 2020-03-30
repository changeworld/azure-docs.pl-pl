---
title: Ćwiczenia odzyskiwania po awarii
description: Poznaj wskazówki i najlepsze rozwiązania dotyczące korzystania z usługi Azure SQL Database do wykonywania ćwiczeń odzyskiwania po awarii.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 12/18/2018
ms.openlocfilehash: 3ca00a03976ae38b7956616b8287220a7bc5998c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73825851"
---
# <a name="performing-disaster-recovery-drill"></a>Wykonywanie ćwiczeń odzyskiwania po awarii

Zaleca się, że sprawdzanie poprawności gotowości aplikacji do przepływu pracy odzyskiwania jest wykonywane okresowo. Weryfikowanie zachowania aplikacji i implikacje utraty danych i/lub zakłóceń, które obejmuje pracy awaryjnej jest dobrą praktyką inżynieryjną. Jest to również wymóg większości standardów branżowych w ramach certyfikacji ciągłości działania.

Wykonywanie ćwiczenia odzyskiwania po awarii składa się z:

* Symulowanie awarii warstwy danych
* Odzyskiwanie
* Sprawdzanie poprawności integralności aplikacji po odzyskaniu

W zależności od sposobu [zaprojektowania aplikacji dla ciągłości biznesowej](sql-database-business-continuity.md)przepływ pracy do wykonania wiertła może się różnić. W tym artykule opisano najlepsze rozwiązania dotyczące przeprowadzania wiertła odzyskiwania po awarii w kontekście usługi Azure SQL Database.

## <a name="geo-restore"></a>Przywracanie geograficzne

Aby zapobiec potencjalnej utracie danych podczas przeprowadzania ćwiczenia odzyskiwania po awarii, wykonaj wiertło przy użyciu środowiska testowego, tworząc kopię środowiska produkcyjnego i używając go do weryfikacji przepływu pracy trybu failover aplikacji.

### <a name="outage-simulation"></a>Symulacja awarii

Aby symulować awarię, można zmienić nazwę źródłowej bazy danych. Ta zmiana nazwy powoduje błędy łączności aplikacji.

### <a name="recovery"></a>Odzyskiwanie

* Wykonaj przywracanie geograficzne bazy danych na innym serwerze, jak opisano [w tym miejscu.](sql-database-disaster-recovery.md)
* Zmień konfigurację aplikacji, aby połączyć się z odzyskaną bazą danych i postępuj zgodnie z [instrukcją Konfigurowanie bazy danych po zakończeniu odzyskiwania.](sql-database-disaster-recovery.md)

### <a name="validation"></a>Sprawdzanie poprawności

Ukończ ćwiczenie, weryfikując integralność aplikacji po odzyskaniu (w tym parametry połączenia, logowania, podstawowe testowanie funkcjonalności lub inne weryfikacje część standardowych procedur podpisywania aplikacji).

## <a name="failover-groups"></a>Grupy trybu failover

W przypadku bazy danych, która jest chroniona przy użyciu grup trybu failover, ćwiczenie ćwiczeń wiertła obejmuje planowane przejście w tryb failover do serwera pomocniczego. Planowana praca awaryjna zapewnia, że podstawowe i pomocnicze bazy danych w grupie trybu failover pozostają zsynchronizowane po przełączeniu ról. W przeciwieństwie do nieplanowanej pracy awaryjnej ta operacja nie powoduje utraty danych, więc wiertło może być wykonywane w środowisku produkcyjnym.

### <a name="outage-simulation"></a>Symulacja awarii

Aby symulować awarię, można wyłączyć aplikację sieci web lub maszynę wirtualną połączoną z bazą danych. Ta symulacja awarii powoduje błędy łączności dla klientów sieci web.

### <a name="recovery"></a>Odzyskiwanie

* Upewnij się, że konfiguracja aplikacji w regionie odzyskiwania po awarii wskazuje na byłego pomocniczego, który staje się w pełni dostępne nowy podstawowy.
* Inicjowanie [planowanej pracy awaryjnej](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) grupy trybu failover z serwera pomocniczego.
* Postępuj zgodnie z [Instrukcja konfigurowania bazy danych po odzyskaniu,](sql-database-disaster-recovery.md) aby zakończyć odzyskiwanie.

### <a name="validation"></a>Sprawdzanie poprawności

Ukończ ćwiczenie, weryfikując integralność aplikacji po odzyskaniu (w tym łączność, podstawowe testowanie funkcjonalności lub inne sprawdzanie poprawności wymagane dla podpisów wiertła).

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o scenariuszach ciągłości biznesowej, zobacz [Scenariusze ciągłości](sql-database-business-continuity.md).
* Aby dowiedzieć się więcej o automatycznych kopiach zapasowych usługi Azure SQL Database, zobacz [Automatyczne kopie zapasowe bazy danych SQL](sql-database-automated-backups.md)
* Aby dowiedzieć się więcej na temat używania automatycznych kopii zapasowych do odzyskiwania, zobacz [przywracanie bazy danych z kopii zapasowych inicjowanych przez usługę](sql-database-recovery-using-backups.md).
* Aby dowiedzieć się więcej o szybszych opcjach odzyskiwania, zobacz [Aktywne grupy replikacji geograficznej](sql-database-active-geo-replication.md) i [Auto-failover](sql-database-auto-failover-group.md).
