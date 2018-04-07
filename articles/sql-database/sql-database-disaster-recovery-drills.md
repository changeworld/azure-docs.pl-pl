---
title: Testowanie odzyskiwania po awarii bazy danych SQL | Dokumentacja firmy Microsoft
description: Dowiedz się, wskazówki i najlepsze rozwiązania dotyczące używania bazy danych SQL Azure, aby wykonać testowanie odzyskiwania po awarii.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 04/01/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 948b8c2c7eac7306336e5dacafffb62a337b8191
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="performing-disaster-recovery-drill"></a>Wykonywanie wyszczególniania odzyskiwania po awarii
Zalecane jest okresowo wykonywać weryfikacji aplikacja jest gotowa do przepływu pracy odzyskiwania. Weryfikowanie zachowanie aplikacji oraz wpływ utraty danych i/lub przerw w działaniu obejmuje czy tryb failover jest dobrym rozwiązaniem engineering. Również jest wymagane przez większość standardy branżowe w ramach certyfikacji ciągłości biznesowej.

Obejmuje wykonywania wyszczególniania odzyskiwania po awarii:

* Symulowanie symulacje awarii warstwy danych
* Odzyskiwanie
* Sprawdź poprawność odzyskiwania post integralności aplikacji

W zależności od tego, jak możesz [przeznaczony dla ciągłość prowadzenia działalności biznesowej aplikacji](sql-database-business-continuity.md), przepływu pracy można wykonać drążenie może się różnić. W tym artykule opisano najważniejsze wskazówki dotyczące przeprowadzania wyszczególniania odzyskiwania po awarii, w kontekście bazy danych SQL Azure.

## <a name="geo-restore"></a>Przywracanie geograficzne
Aby zapobiec utracie danych podczas przeprowadzania wyszczególniania odzyskiwania po awarii, należy wykonać drążenie, tworząc kopię w środowisku produkcyjnym i użycie go do sprawdzenia przepływu pracy awaryjnej aplikacji przy użyciu środowiska testowego.

#### <a name="outage-simulation"></a>Symulacji awarii
Aby symulować awarii, można zmienić nazwy źródłowej bazy danych. Powoduje to błędów łączności aplikacji.

#### <a name="recovery"></a>Odzyskiwanie
* Wykonać geograficznie przywracania bazy danych na innym serwerze, zgodnie z opisem [tutaj](sql-database-disaster-recovery.md).
* Zmień konfigurację aplikacji, aby nawiązać połączenie odzyskanej bazy danych i postępuj zgodnie z [skonfigurować bazę danych po odzyskaniu](sql-database-disaster-recovery.md) przewodniku, aby zakończyć odzyskiwanie.

#### <a name="validation"></a>Walidacja
* Zakończ Drąż weryfikowanie odzyskiwania post integralności aplikacji (w tym parametry połączenia, logowania, podstawowych funkcji testowania lub innych operacji sprawdzania poprawności część procedur signoffs standardowej aplikacji).

## <a name="failover-groups"></a>Grupy trybu failover
Dla bazy danych, która jest chroniona przy użyciu grup trybu failover wykonywania Przechodzenie do szczegółów obejmuje planowany tryb failover na serwer pomocniczy. Planowany tryb failover zapewnia, że podstawowej i pomocniczej bazy danych w grupie trybu failover pozostają zsynchronizowane podczas przełączania ról. Inaczej niż w przypadku nieplanowanego trybu failover ta operacja nie powoduje utraty danych, więc Drąż mogą być wykonywane w środowisku produkcyjnym.

#### <a name="outage-simulation"></a>Symulacji awarii
Aby symulować awarii, można wyłączyć maszyny wirtualnej, połączony z bazą danych lub aplikacji sieci web. W efekcie awarie połączenia dla klientów sieci web.

#### <a name="recovery"></a>Odzyskiwanie
* Sprawdź konfigurację aplikacji w punktach region DR byłego dodatkowej, która staje się dostępny w pełni nową podstawową.
* Zainicjuj [planowanego trybu failover](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) grupy trybu failover z serwera pomocniczego.
* Postępuj zgodnie z [skonfigurować bazę danych po odzyskaniu](sql-database-disaster-recovery.md) przewodniku, aby zakończyć odzyskiwanie.

#### <a name="validation"></a>Walidacja
Zakończ Drąż weryfikowanie odzyskiwania post integralności aplikacji (w tym łączności, podstawowych funkcji testowania lub innych operacji sprawdzania poprawności wymaganego dla signoffs Przechodzenie do szczegółów).

## <a name="next-steps"></a>Kolejne kroki
* Aby dowiedzieć się więcej o scenariuszach ciągłości biznesowej, zobacz [scenariusze ciągłości](sql-database-business-continuity.md).
* Aby dowiedzieć się więcej na temat usługi Azure SQL bazy danych automatycznego tworzenia kopii zapasowych, zobacz [bazy danych SQL automatycznego tworzenia kopii zapasowych](sql-database-automated-backups.md)
* Aby dowiedzieć się więcej o używaniu kopie zapasowe automatycznego odzyskiwania, zobacz [przywrócić bazę danych z kopii zapasowych inicjowane przez usługę](sql-database-recovery-using-backups.md).
* Informacje na temat opcji odzyskiwania szybsze, zobacz [aktywnych grup — replikacja geograficzna i pracy awaryjnej](sql-database-geo-replication-overview.md).  
