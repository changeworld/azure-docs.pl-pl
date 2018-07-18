---
title: Bazy danych SQL automatycznego, geograficznie nadmiarowych kopii zapasowych Azure | Dokumentacja firmy Microsoft
description: Bazy danych SQL Database automatycznie tworzy kopię zapasową lokalnej bazy danych co kilka minut i korzysta z platformy Azure magazyn geograficznie nadmiarowy geograficznie nadmiarowości geograficznej.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.workload: Active
ms.date: 07/16/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: fcc860daddbaa0b3275116027136bcde9dbcf256
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39092031"
---
# <a name="learn-about-automatic-sql-database-backups"></a>Dowiedz się więcej o automatycznych kopii zapasowych bazy danych SQL

SQL Database automatycznie tworzy kopie zapasowe baz danych i używa platformy Azure dostęp do odczytu magazynu geograficznie nadmiarowego (RA-GRS), aby zapewnić nadmiarowość geograficzna. Te kopie zapasowe są tworzone automatycznie i bez dodatkowych opłat. Nie trzeba nic robić, aby je zastosować. Kopie zapasowe bazy danych są integralną część wszelkie strategii odzyskiwania firmy, jak ciągłość działalności biznesowej i odzyskiwanie po awarii, ponieważ one ochronę danych przed przypadkowym uszkodzeniem lub usunięciem. Reguły zabezpieczeń wymaga, że kopie zapasowe są dostępne przez dłuższy czas, po skonfigurowaniu długoterminowe zasady przechowywania kopii zapasowych. Aby uzyskać więcej informacji, zobacz [Długoterminowe przechowywanie](sql-database-long-term-retention.md).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Co to jest kopię zapasową bazy danych SQL?

SQL Database przy użyciu technologii SQL Server do utworzenia [pełne](https://msdn.microsoft.com/library/ms186289.aspx), [różnicowej](http://msdn.microsoft.com/library/ms175526.aspx), i [dziennika transakcji](https://msdn.microsoft.com/library/ms191429.aspx) kopii zapasowych na potrzeby punktu w czasie przywracania (Odzyskiwanie). Kopie zapasowe dziennika transakcji ogólnie się tak zdarzyć co 5 – 10 minut, z częstotliwością, na podstawie poziomu wydajności i zmniejszenia liczby działań bazy danych. Kopie zapasowe dziennika transakcji, za pomocą pełnych i różnicowych kopii zapasowych, umożliwiają przywrócenie bazy danych do określonego punktu w czasie na tym samym serwerze, który hostuje bazę danych. Po przywróceniu bazy danych usługi wpadł na których kopie zapasowe mają być przywracane dziennika pełnej, różnicowej i transakcji.


Możesz użyć tych kopii zapasowych:

* Przywracanie bazy danych do punktu w czasie w okresie przechowywania. Ta operacja spowoduje utworzenie nowej bazy danych, w tym samym serwerze co oryginalna baza danych.
* Przywracanie usuniętej bazy danych do czasu, który został usunięty lub w dowolnym momencie w okresie przechowywania. Usuniętą bazę danych można przywrócić tylko na tym samym serwerze, na którym utworzono oryginalnej bazy danych.
* Przywracanie bazy danych do innego regionu geograficznego. Dzięki temu można odzyskać z geograficznego po awarii, jeśli nie masz dostępu do serwera i bazy danych. Tworzy nową bazę danych w dowolnej istniejącego serwera, w dowolnym miejscu na świecie. 
* Przywracanie bazy danych z określonego długoterminowej kopii zapasowej, jeśli baza danych została skonfigurowana przy użyciu długoterminowe zasady przechowywania (od lewej do prawej). Dzięki temu można przywrócić starą wersję bazy danych, aby zrealizować żądanie zgodności lub uruchom starą wersję aplikacji. Zobacz [długoterminowego przechowywania](sql-database-long-term-retention.md).
* Aby wykonać przywracanie, zobacz [przywrócić bazę danych z kopii zapasowych](sql-database-recovery-using-backups.md).

> [!NOTE]
> W usłudze Azure storage termin *replikacji* odwołuje się do kopiowania plików z jednej lokalizacji do innej. SQL *replikacji bazy danych* odwołuje się do przechowywanie wielu pomocniczych baz danych synchronizowane z podstawowej bazy danych. 
> 

## <a name="how-long-are-backups-kept"></a>Jak długo są przechowywane kopie zapasowe?
Każda kopia zapasowa bazy danych SQL ma domyślny okres przechowywania, zależy od warstwy usługi bazy danych, która różni się między [modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md) i [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md). Można zaktualizować okresu przechowywania kopii zapasowej dla bazy danych. Zobacz [okres przechowywania kopii zapasowej zmiany](#how-to-change-backup-retention-period) Aby uzyskać więcej informacji.

Jeśli usuniesz bazę danych, SQL Database zostanie zachowana kopie zapasowe w taki sam sposób jak dla bazy danych online. Na przykład po usunięciu podstawowej bazy danych zawierającej okresu przechowywania siedmiu dni, kopii zapasowej, która jest cztery dni zostanie zapisany przez trzy kolejne dni.

Jeśli zachodzi potrzeba przechowywanie kopii zapasowych przez okres dłuższy niż maksymalny okres przechowywania Odzyskiwanie, można zmodyfikować właściwości kopii zapasowej, aby dodać jeden lub więcej okresów długoterminowe retetion do bazy danych. Zobacz [długoterminowego przechowywania kopii zapasowych](sql-database-long-term-retention.md) Aby uzyskać więcej informacji.

> [!IMPORTANT]
> Jeśli usuniesz serwera Azure SQL, który jest hostem bazy danych SQL, wszystkie elastycznych pul i baz danych, które należą do serwera również zostaną usunięte i nie można odzyskać. Nie można przywrócić usuniętego serwera. Ale jeśli skonfigurowano przechowywanie długoterminowe kopie zapasowe baz danych przy użyciu od lewej do prawej, nie zostaną usunięte i te bazy danych, które mogą zostać przywrócone.

### <a name="pitr-retention-for-dtu-based-service-tiers"></a>Odzyskiwanie przechowywania dla warstwy usług oparte na jednostkach DTU
Domyślny okres przechowywania dla bazy danych utworzone za pomocą modelu zakupu opartego na jednostkach DTU zależy od warstwy usługi:

* Warstwa podstawowa usług jest 1 tydzień.
* Warstwy usług w warstwie standardowa wynosi 5 tygodni.
* Warstwa Premium usług jest 5 tygodni.

Czy można zmniejszyć bieżącym okresem przechowywania Odzyskiwanie, wszystkie istniejące kopie zapasowe starsze niż nowy okres przechowywania nie będzie już dostępna. 

Jeśli zwiększysz bieżącym okresem przechowywania Odzyskiwanie bazy danych SQL Database zostanie zachowana istniejące kopie zapasowe, aż do osiągnięcia dłuższy okres przechowywania danych.

## <a name="how-often-do-backups-happen"></a>Jak często stanie kopie zapasowe?
### <a name="backups-for-point-in-time-restore"></a>Tworzenie kopii zapasowych do punktu w czasie przywracania
SQL Database obsługuje samoobsługowego przywracania w momencie (Odzyskiwanie) przez automatyczne tworzenie pełnej kopii zapasowej, różnicowe kopie zapasowe i kopie zapasowe dziennika transakcji. Pełne kopie zapasowe są tworzone co tydzień, różnicowe kopie zapasowe są tworzone co kilka godzin i kopie zapasowe dziennika transakcji są tworzone co 5 – 10 minut. Pierwsza pełna kopia zapasowa jest zaplanowane, natychmiast, po utworzeniu bazy danych. Zazwyczaj zostanie zakończona w ciągu 30 minut, ale może to trwać dłużej, gdy baza danych znajduje się o znacznym rozmiarze. Na przykład tworzenia początkowej kopii zapasowej może potrwać dłużej przywróconej bazy danych lub kopii bazy danych. Po pierwszej pełnej kopii zapasowej wszystkie dodatkowe kopie zapasowe są zaplanowane automatycznie i zarządzane w trybie dyskretnym w tle. Dokładny czas wszystkie kopie zapasowe bazy danych jest określany przez usługę SQL Database jako jej całkowitego obciążenia systemu.

Odzyskiwanie kopii zapasowych są geograficznie nadmiarowy i chronione przez [replikacji między regionami w usłudze Azure Storage](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)

Aby uzyskać więcej informacji, zobacz [punktu w czasie przywracania](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="backups-for-long-term-retention"></a>Długoterminowe przechowywanie kopii zapasowych
SQL Database oferuje możliwość Konfigurowanie długoterminowego przechowywania danych (od lewej do prawej) pełnych kopii zapasowych dla maksymalnie 10 lat. Włączenie zasad pisowni LTR tygodniowe pełne kopie zapasowe są automatycznie kopiowane do innego kontenera magazynu RA-GRS. Aby spełnić wymagania zgodności w różnych, możesz wybrać różnych okresów przechowywania dla kopii zapasowych co tydzień, miesięczny lub roczny. Użycie magazynu zależy od wybranej częstotliwości tworzenia kopii zapasowych i okresy przechowywania. Możesz użyć [Kalkulator cen od lewej do prawej](https://azure.microsoft.com/pricing/calculator/?service=sql-database) do szacowania kosztów magazynowania od lewej do prawej. 

Podobnie jak Odzyskiwanie, kopie zapasowe od lewej do prawej są geograficznie nadmiarowy i chronione przez [replikacji między regionami w usłudze Azure Storage](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage).

Aby uzyskać więcej informacji, zobacz [długoterminowego przechowywania kopii zapasowych](sql-database-long-term-retention.md).

## <a name="are-backups-encrypted"></a>Kopie zapasowe są szyfrowane?

Jeśli baza danych jest szyfrowana za pomocą funkcji TDE, kopie zapasowe są automatycznie szyfrowane w stanie spoczynku w tym kopie zapasowe od lewej do prawej. Gdy funkcja TDE jest włączona dla usługi Azure SQL database, tworzenie kopii zapasowych również są szyfrowane. Wszystkie nowe bazy danych Azure SQL są skonfigurowane za pomocą funkcji TDE jest domyślnie włączone. Aby uzyskać więcej informacji na temat funkcji TDE, zobacz [funkcji Transparent Data Encryption z usługą Azure SQL Database](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="how-do-automated-backups-impact-my-compliance"></a>Jak automatycznie tworzonymi kopiami zapasowymi wpływ na Moje zgodności?

Podczas migracji bazy danych z warstwy usług oparte na jednostkach DTU z przechowywaniem Odzyskiwanie domyślne 35 dni do warstwy usług oparte na rdzeniach wirtualnych, aby upewnić się, że zasady odzyskiwania danych aplikacji nie jest compromized zachowywana jest przechowywania Odzyskiwanie. Jeśli domyślny okres przechowywania nie spełnia wymagań dotyczących zgodności, możesz zmienić okres przechowywania Odzyskiwanie przy użyciu programu PowerShell lub interfejsu API REST. Zobacz [okres przechowywania kopii zapasowej zmiany](#how-to-change-backup-retention-period) Aby uzyskać więcej informacji.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-backup-retention-period"></a>Jak zmienić okres przechowywania kopii zapasowej
Można zmienić domyślny okres przechowywania, przy użyciu interfejsu API REST lub programu PowerShell. Obsługiwane wartości to: 7, 14, 21, 28 lub 35 dni. Poniższe przykłady ilustrują zmiana okresu przechowywania Odzyskiwanie 28 dni. 

> [!NOTE]
> Te interfejsy API wpływają tylko na okres przechowywania Odzyskiwanie. Jeśli od lewej do prawej są skonfigurowane dla bazy danych, nie będzie mieć wpływ. Zobacz [długoterminowego przechowywania kopii zapasowych](sql-database-long-term-retention.md) Aby uzyskać szczegółowe informacje o sposobie zmieniania okresy przechowywania od lewej do prawej.

### <a name="change-pitr-backup-retention-period-using-powershell"></a>Zmiana okresu przechowywania kopii zapasowej Odzyskiwanie przy użyciu programu PowerShell
```powershell
Set-AzureRmSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```
> [!IMPORTANT]
> Ten interfejs API jest uwzględnione w elementu AzureRM.Sql modułu programu PowerShell, począwszy od wersji [4.7.0-preview](https://www.powershellgallery.com/packages/AzureRM.Sql/4.7.0-preview). 

### <a name="change-pitr-retention-period-using-rest-api"></a>Zmiana okresu przechowywania Odzyskiwanie przy użyciu interfejsu API REST
**Przykładowe żądanie**
```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```
**Treść żądania**
```json
{
  "properties":{  
      "retentionDays":28
   }
}
```
**Przykładowa odpowiedź**

Kod stanu: 200
```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```
Zobacz [interfejsu API REST przechowywania kopii zapasowej](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies) Aby uzyskać więcej informacji.

## <a name="next-steps"></a>Kolejne kroki

- Kopie zapasowe bazy danych są integralną część wszelkie strategii odzyskiwania firmy, jak ciągłość działalności biznesowej i odzyskiwanie po awarii, ponieważ one ochronę danych przed przypadkowym uszkodzeniem lub usunięciem. Aby poznać inne usługi Azure SQL Database rozwiązania zapewniające ciągłość działania, zobacz temat [omówienie ciągłości działania](sql-database-business-continuity.md).
- Aby przywrócić do punktu w czasie za pomocą witryny Azure portal, zobacz [przywrócić bazę danych do punktu w czasie za pomocą witryny Azure portal](sql-database-recovery-using-backups.md).
- Aby przywrócić do punktu w czasie za pomocą programu PowerShell, zobacz [przywrócić bazę danych do punktu w czasie za pomocą programu PowerShell](scripts/sql-database-restore-database-powershell.md).
- Konfigurowanie, zarządzanie i przywracać długoterminowego przechowywania automatycznych kopii zapasowych w magazynie usług odzyskiwania Azure przy użyciu witryny Azure portal, zobacz [Zarządzanie długotrwałym przechowywaniem kopii zapasowych przy użyciu witryny Azure portal](sql-database-long-term-backup-retention-configure.md).
- Konfigurowanie, zarządzanie i przywracać długoterminowego przechowywania automatycznych kopii zapasowych w magazynie usług odzyskiwania Azure przy użyciu programu PowerShell, zobacz [Zarządzanie długotrwałym przechowywaniem kopii zapasowych przy użyciu programu PowerShell](sql-database-long-term-backup-retention-configure.md).
