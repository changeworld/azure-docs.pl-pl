---
title: Znane problemy i ograniczenia dotyczące migracji w trybie online w celu Azure SQL Database wystąpienia zarządzanego
description: Informacje na temat znanych problemów/ograniczeń migracji skojarzonych z migracją online w celu Azure SQL Database wystąpienia zarządzanego.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 12/18/2019
ms.openlocfilehash: bc1cbfc1e86db758a4f4d0240f81f5363f817312
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483143"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database-managed-instance"></a>Znane problemy/ograniczenia migracji z migracją online do Azure SQL Database wystąpienia zarządzanego

Poniżej opisano znane problemy i ograniczenia związane z migracją online z SQL Server do Azure SQL Database wystąpienia zarządzanego.

> [!IMPORTANT]
> W przypadku migracji w trybie online SQL Server do Azure SQL Database migracja SQL_variant typów danych nie jest obsługiwana.

## <a name="backup-requirements"></a>Wymagania dotyczące kopii zapasowych

- **Kopie zapasowe z sumą kontrolną**

    Azure Database Migration Service używa metody Backup i Restore do migrowania lokalnych baz danych do SQL Database wystąpienia zarządzanego. Azure Database Migration Service obsługuje tylko kopie zapasowe utworzone przy użyciu sum kontrolnych.

    [Włącz lub Wyłącz sumy kontrolne kopii zapasowych podczas wykonywania kopii zapasowej lub przywracania (SQL Server)](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017)

    > [!NOTE]
    > Jeśli kopie zapasowe bazy danych są wykonywane przy użyciu kompresji, suma kontrolna jest zachowaniem domyślnym, chyba że zostanie jawnie wyłączona.

    W przypadku migracji w trybie offline w przypadku wybrania opcji **Zezwól na Azure Database Migration Service...** , a następnie Azure Database Migration Service będzie korzystać z kopii zapasowej bazy danych z włączoną opcją sum kontrolnych.

- **Nośnik kopii zapasowej**

    Upewnij się, że wszystkie kopie zapasowe są wykonywane na osobnym nośniku kopii zapasowych (pliki kopii zapasowej). Azure Database Migration Service nie obsługuje kopii zapasowych, które są dołączane do pojedynczego pliku kopii zapasowej. Utwórz pełną kopię zapasową i Rejestruj kopie zapasowe w oddzielnych plikach kopii zapasowej.

## <a name="data-and-log-file-layout"></a>Układ danych i plików dziennika

- **Liczba plików dziennika**

    Azure Database Migration Service nie obsługuje baz danych z wieloma plikami dziennika. Jeśli masz wiele plików dziennika, Zmniejsz je i uporządkuj je w jeden plik dziennika transakcji. Ponieważ nie można zdalnie rejestrować plików, które nie są puste, należy najpierw utworzyć kopię zapasową pliku dziennika.

## <a name="sql-server-features"></a>Funkcje SQL Server

- **FileStream/FileTable**

    SQL Database wystąpienie zarządzane obecnie nie obsługuje plików FileStream i FileTable. W przypadku obciążeń zależnych od tych funkcji Zalecamy wybór serwerów SQL działających na maszynach wirtualnych platformy Azure jako element docelowy platformy Azure.

- **Tabele w pamięci**

    Pakiet OLTP w pamięci jest dostępny w warstwach Premium i Krytyczne dla działania firmy dla SQL Database wystąpienia zarządzanego; warstwa Ogólnego przeznaczenia nie obsługuje przetwarzania OLTP w pamięci.

## <a name="migration-resets"></a>Resetowanie migracji

- **Wdrożenia**

    SQL Database wystąpienie zarządzane to usługa PaaS z automatyczną poprawką poprawek i aktualizacjami wersji. Podczas migracji SQL Database wystąpienia zarządzanego, aktualizacje niekrytyczne są łatwiejsze do 36 godzin. Następnie (i w przypadku aktualizacji krytycznych), jeśli migracja zostanie zakłócona, proces resetuje do stanu pełnego przywracania.

    Uruchomienie produkcyjne migracji można wywołać tylko po przywróceniu pełnej kopii zapasowej i przejściu do wszystkich kopii zapasowych dziennika. Jeśli dotyczy to jednorazowe migracji produkcyjnej, skontaktuj się z [aliasem opinii platformy Azure](mailto:dmsfeedback@microsoft.com).
