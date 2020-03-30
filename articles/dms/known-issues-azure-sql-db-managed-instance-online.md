---
title: Znane problemy i ograniczenia związane z migracjami online do wystąpienia zarządzanego usługi Azure SQL Database
description: Dowiedz się więcej o znanych problemach/ograniczeniach migracji skojarzonych z migracjami online do wystąpienia zarządzanego usługi Azure SQL Database.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 88e2b5894686ee93caecf33e04940803eb75f394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77648669"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database-managed-instance"></a>Znane problemy/ograniczenia migracji z migracjami online do wystąpienia zarządzanego usługi Azure SQL Database

Znane problemy i ograniczenia, które są skojarzone z migracjami online z sql server do wystąpienia zarządzanego usługi Azure SQL Database są opisane poniżej.

> [!IMPORTANT]
> W przypadku migracji online programu SQL Server do bazy danych SQL Azure migracja typów danych SQL_variant nie jest obsługiwana.

## <a name="backup-requirements"></a>Wymagania dotyczące tworzenia kopii zapasowych

- **Kopie zapasowe z sumą kontrolną**

    Usługa migracji bazy danych platformy Azure używa metody tworzenia kopii zapasowych i przywracania do migracji lokalnych baz danych do wystąpienia zarządzanego bazy danych SQL. Usługa migracji bazy danych Azure obsługuje tylko kopie zapasowe utworzone przy użyciu sumy kontrolnej.

    [Włączanie lub wyłączanie sum kontrolnych kopii zapasowych podczas tworzenia kopii zapasowej lub przywracania (SQL Server)](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017)

    > [!NOTE]
    > Jeśli wykonać kopie zapasowe bazy danych z kompresją, suma kontrolna jest zachowaniem domyślnym, chyba że jawnie wyłączone.

    W przypadku migracji w trybie offline, jeśli **wybierzesz, pozwolę usłudze migracji bazy danych platformy Azure...** usługa migracji bazy danych platformy Azure spowoduje, że kopia zapasowa bazy danych zostanie włączona z włączoną opcją sumy kontrolnej.

- **Nośnik kopii zapasowej**

    Upewnij się, że każda kopia zapasowa jest przyjmowana na oddzielnym nośniku kopii zapasowej (plikach kopii zapasowych). Usługa migracji bazy danych Azure nie obsługuje kopii zapasowych, które są dołączane do jednego pliku kopii zapasowej. Wykonaj pełną kopię zapasową i zarejestruj kopie zapasowe, aby wyodrębnić pliki kopii zapasowych.

## <a name="data-and-log-file-layout"></a>Układ danych i plików dziennika

- **Liczba plików dziennika**

    Usługa migracji bazy danych platformy Azure nie obsługuje baz danych z wieloma plikami dziennika. Jeśli masz wiele plików dziennika, zmniejsz i zreorganizuj je w jeden plik dziennika transakcji. Ponieważ nie można zdalnie rejestrować plików, które nie są puste, należy najpierw wykonać jego kopie zapasowe.

## <a name="sql-server-features"></a>Funkcje programu SQL Server

- **Tabele strumienia plików/plików**

    Wystąpienie zarządzane bazy danych SQL obecnie nie obsługuje FileStream i FileTables. W przypadku obciążeń zależnych od tych funkcji zaleca się wybranie serwerów SQL uruchomionych na maszynach wirtualnych platformy Azure jako docelowego narzędzia Azure.

- **Tabele w pamięci**

    Oltp w pamięci jest dostępny w warstwach Premium i Business Critical dla wystąpienia zarządzanego bazy danych SQL; warstwa ogólnego przeznaczenia nie obsługuje protokołu OLTP w pamięci.

## <a name="migration-resets"></a>Resetowanie migracji

- **Wdrożenia**

    Wystąpienie zarządzane bazy danych SQL to usługa PaaS z automatycznym łataniem i aktualizacjami wersji. Podczas migracji wystąpienia zarządzanego bazy danych SQL aktualizacje niekrytyczne są pomocne do 36 godzin. Następnie (i w przypadku aktualizacji krytycznych), jeśli migracja zostanie zakłócona, proces zostanie zresetowany do stanu pełnego przywracania.

    Migracja cutover można wywołać tylko po przywróceniu pełnej kopii zapasowej i dogania wszystkie kopie zapasowe dziennika. Jeśli dotyczy to cięć migracji produkcyjnej, skontaktuj się z [aliasem opinii usługi Azure DMS.](mailto:dmsfeedback@microsoft.com)
