---
title: SQL Database odzyskiwanie po awarii | Dokumentacja firmy Microsoft
description: Dowiedz się, wskazówki i najlepsze rozwiązania dotyczące usługi Azure SQL Database, aby wykonać odzyskiwanie po awarii.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: 5d754ae558d485036a9a55f573a3f40162ed9f84
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60725435"
---
# <a name="performing-disaster-recovery-drill"></a>Wykonywanie próbnego odzyskiwania po awarii

Zalecane jest, okresowo wykonywać sprawdzanie poprawności gotowość aplikacji do przepływu pracy odzyskiwania. Weryfikowanie zachowanie aplikacji oraz wpływ utraty danych i/lub zakłócenie polega na tryb failover jest dobrą praktykę. Jest również wymagane przez większość standardy branżowe w ramach certyfikacji ciągłości biznesowej.

Wykonywanie próbnego odzyskiwania po awarii składa się z:

* Symulowanie awarii warstwy danych
* Odzyskiwanie
* Sprawdź poprawność odzyskiwania wpis integralność aplikacji

Zależności od tego, jak możesz [zaprojektowane aplikacji w celu zachowania ciągłości](sql-database-business-continuity.md), przepływ pracy do wykonania testowania odzyskiwania po awarii mogą się różnić. W tym artykule opisano najlepsze rozwiązania dotyczące przeprowadzanie próbnego odzyskiwania po awarii, w ramach usługi Azure SQL Database.

## <a name="geo-restore"></a>Przywracanie geograficzne

Aby zapobiec utracie danych podczas przeprowadzania odzyskiwania po awarii, należy wykonać testowania odzyskiwania po awarii przy użyciu środowiska testowego, tworząc kopię w środowisku produkcyjnym i użycie go do sprawdzenia aplikacji trybu failover w przepływie pracy.

### <a name="outage-simulation"></a>Symulacja awarii

Symulowanie awarii, można zmienić nazwy źródłowej bazy danych. Ta zmiana powoduje, że błędów łączności aplikacji.

### <a name="recovery"></a>Odzyskiwanie

* Wykonaj Przywracanie geograficzne bazy danych na innym serwerze, zgodnie z opisem [tutaj](sql-database-disaster-recovery.md).
* Zmień konfigurację aplikacji, aby nawiązać połączenie z odzyskanej bazy danych i postępuj zgodnie z [skonfigurować bazę danych po odzyskaniu](sql-database-disaster-recovery.md) przewodniku w celu ukończenia odzyskiwania.

### <a name="validation"></a>Walidacja

Wykonaj testowania odzyskiwania po awarii, upewniając się, odzyskiwanie po integralność aplikacji (w tym parametry połączenia, logowania, podstawowe funkcje testowania lub innych operacji sprawdzania poprawności część procedury signoffs standardowej aplikacji).

## <a name="failover-groups"></a>Grupy trybu failover

Dla bazy danych, który jest chroniony za pomocą grupy trybu failover w tym ćwiczeniu testowania odzyskiwania po awarii obejmuje planowany tryb failover na serwer pomocniczy. Planowanego trybu failover gwarantuje, że podstawowe i pomocnicze bazy danych w grupie trybu failover pozostają zsynchronizowane podczas przełączania ról. Inaczej niż w przypadku nieplanowanego trybu failover ta operacja nie powoduje utraty danych, dzięki czemu testowania odzyskiwania po awarii mogą być wykonywane w środowisku produkcyjnym.

### <a name="outage-simulation"></a>Symulacja awarii

Symulowanie awarii, można wyłączyć aplikacji sieci web lub maszyna wirtualna połączona z bazą danych. Tej symulacji awarii skutkuje błędami łączności dla klientów sieci web.

### <a name="recovery"></a>Odzyskiwanie

* Upewnij się, że konfiguracja aplikacji w punktach regionu odzyskiwania po awarii przez byłego pomocniczego, która staje się nową podstawową dla w pełni dostępne.
* Zainicjuj [zaplanowanym powrocie po awarii](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) grupy trybu failover z serwera pomocniczego.
* Postępuj zgodnie z [skonfigurować bazę danych po odzyskaniu](sql-database-disaster-recovery.md) przewodniku w celu ukończenia odzyskiwania.

### <a name="validation"></a>Walidacja

Ukończ testowania odzyskiwania po awarii, upewniając się, odzyskiwanie po integralność aplikacji (w tym łączności, podstawowe funkcje testowania lub innych operacji sprawdzania poprawności wymagane dla signoffs testowania odzyskiwania po awarii).

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się o ciągłość działania — scenariusze, zobacz [ciągłość działania — scenariusze](sql-database-business-continuity.md).
* Aby dowiedzieć się więcej na temat usługi Azure SQL Database automatyczne kopie zapasowe, zobacz [bazy danych SQL, automatyczne kopie zapasowe](sql-database-automated-backups.md)
* Aby dowiedzieć się więcej o korzystaniu z automatycznych kopii zapasowych do odzyskania, zobacz [przywrócić bazę danych z kopii zapasowych zainicjowanych przez usługę](sql-database-recovery-using-backups.md).
* Aby dowiedzieć się o szybsze opcje odzyskiwania, zobacz [aktywnej replikacji geograficznej](sql-database-active-geo-replication.md) i [automatyczny tryb failover grupy](sql-database-auto-failover-group.md).
