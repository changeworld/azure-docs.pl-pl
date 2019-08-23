---
title: Azure SQL Database automatyczne, nadmiarowe kopie zapasowe | Microsoft Docs
description: SQL Database automatycznie tworzy kopię zapasową lokalnej bazy danych co kilka minut i używa magazynu geograficznie nadmiarowego do odczytu platformy Azure na potrzeby nadmiarowości geograficznej.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 08/22/2019
ms.openlocfilehash: 551c2c02af7b996a34a138586fd91a77a0455d92
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69904322"
---
# <a name="automated-backups"></a>Automatyczne kopie zapasowe

SQL Database automatycznie tworzy kopie zapasowe bazy danych, które są przechowywane od 7 do 35 dni, i używa magazynu geograficznie nadmiarowego platformy Azure do [odczytu (RA-GRS)](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) , aby upewnić się, że są one zachowywane, nawet jeśli centrum danych jest niedostępne. Te kopie zapasowe są tworzone automatycznie. Kopie zapasowe bazy danych są istotną częścią strategii ciągłości działania i odzyskiwania po awarii, ponieważ chronią dane przed przypadkowym uszkodzeniem lub usunięciem. Jeśli reguły zabezpieczeń wymagają, aby kopie zapasowe były dostępne przez dłuższy czas (do 10 lat), można skonfigurować [długoterminowe przechowywanie](sql-database-long-term-retention.md) pojedynczych baz danych i pul elastycznych.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Co to jest SQL Database kopia zapasowa

SQL Database używa technologii SQL Server do tworzenia [pełnych kopii zapasowych](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) co tydzień, [różnicowych kopii zapasowych](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) co 12 godzin i [kopii zapasowych dziennika transakcji](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) co 5-10 minut. Kopie zapasowe są przechowywane w obiektach [blob magazynu RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) , które są replikowane do [sparowanego centrum danych](../best-practices-availability-paired-regions.md) w celu ochrony przed awarią centrum danych. W przypadku przywracania bazy danych usługa określa, które kopie zapasowe pełnych, różnicowych i dzienników transakcji muszą zostać przywrócone.

Tych kopii zapasowych można użyć do:

- **Przywróć istniejącą bazę danych do punktu w czasie w ciągu ostatnich** w okresie przechowywania przy użyciu Azure Portal, Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub API REST. W przypadku pojedynczej bazy danych i pul elastycznych ta operacja spowoduje utworzenie nowej bazy danych na tym samym serwerze, na którym znajduje się oryginalna baza danych. W wystąpieniu zarządzanym ta operacja umożliwia utworzenie kopii bazy danych lub tego samego lub innego wystąpienia zarządzanego w ramach tej samej subskrypcji.
  - Aby skonfigurować zasady tworzenia kopii zapasowych, należy **[zmienić okres przechowywania kopii zapasowych](#how-to-change-the-pitr-backup-retention-period)** od 7 do 35 dni.
  - **Zmień długoterminowe zasady przechowywania do 10 lat** na pojedyncza baza danych i elastycznych pul przy użyciu [Azure Portal](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies) lub [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#use-powershell-to-manage-long-term-backups).
- **Przywracanie usuniętej bazy danych do czasu jej usunięcia** lub w dowolnym momencie w okresie przechowywania. Usunięta baza danych może zostać przywrócona tylko na tym samym serwerze logicznym lub wystąpieniu zarządzanym, w którym została utworzona oryginalna baza danych.
- **Przywracanie bazy danych do innego regionu**geograficznego. Funkcja przywracania geograficznego umożliwia odzyskanie danych po awarii geograficznej, gdy nie można uzyskać dostępu do serwera i bazy danych. Tworzy nową bazę danych na dowolnym istniejącym serwerze w dowolnym miejscu na świecie.
- **Przywracanie bazy danych z określonej długoterminowej kopii zapasowej** na pojedyncza baza danych lub Pula elastyczna, jeśli baza danych została skonfigurowana z użyciem długoterminowych zasad przechowywania. LTR umożliwia przywrócenie starej wersji bazy danych przy użyciu [Azure Portal](sql-database-long-term-backup-retention-configure.md#view-backups-and-restore-from-a-backup-using-azure-portal) lub [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#use-powershell-to-manage-long-term-backups) w celu spełnienia żądania zgodności lub w celu uruchomienia starej wersji aplikacji. Aby uzyskać więcej informacji, zobacz [Długoterminowe przechowywanie](sql-database-long-term-retention.md).
- Aby wykonać przywracanie, zobacz [przywracanie bazy danych z kopii zapasowych](sql-database-recovery-using-backups.md).

> [!NOTE]
> W usłudze Azure Storage termin *replikacja* dotyczy kopiowania plików z jednej lokalizacji do innej. *Replikacja bazy danych* SQL dotyczy utrzymywania synchronizacji wielu pomocniczych baz danych z podstawową bazą danych.

Niektóre z tych operacji można wypróbować, korzystając z następujących przykładów:

| | Witryna Azure Portal | Azure PowerShell |
|---|---|---|
| Zmień przechowywanie kopii zapasowych | [pojedyncza baza danych](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-the-azure-portal) <br/> [Wystąpienie zarządzane](sql-database-automated-backups.md#change-pitr-for-a-managed-instance) | [pojedyncza baza danych](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-powershell) <br/>[Wystąpienie zarządzane](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Zmiana długoterminowego przechowywania kopii zapasowych | [Pojedyncza baza danych](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Wystąpienie zarządzane — nie dotyczy  | [pojedyncza baza danych](sql-database-long-term-backup-retention-configure.md#use-powershell-to-manage-long-term-backups)<br/>Wystąpienie zarządzane — nie dotyczy  |
| Przywracanie bazy danych z punktu w czasie | [Pojedyncza baza danych](sql-database-recovery-using-backups.md#point-in-time-restore) | [Pojedyncza baza danych](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Wystąpienie zarządzane](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Przywracanie usuniętej bazy danych | [Pojedyncza baza danych](sql-database-recovery-using-backups.md#deleted-database-restore-using-the-azure-portal) | [Pojedyncza baza danych](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Wystąpienie zarządzane](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Przywracanie bazy danych z usługi Azure Blob Storage | Pojedyncza baza danych — nie dotyczy <br/>Wystąpienie zarządzane — nie dotyczy  | Pojedyncza baza danych — nie dotyczy <br/>[Wystąpienie zarządzane](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="how-long-are-backups-kept"></a>Jak długo są przechowywane kopie zapasowe

Wszystkie bazy danych Azure SQL Database (pojedyncze, w puli i zarządzane wystąpienia bazy danych) mają domyślny okres przechowywania kopii zapasowych wynoszący **siedem** dni. [Okres przechowywania kopii zapasowej można zmienić do 35 dni](#how-to-change-the-pitr-backup-retention-period).

Jeśli usuniesz bazę danych, SQL Database będą zachować kopie zapasowe w taki sam sposób, jak w przypadku bazy danych w trybie online. Na przykład po usunięciu podstawowej bazy danych, która ma okres przechowywania wynoszący siedem dni, kopia zapasowa, która jest starsza niż cztery dni, jest zapisywana przez trzy więcej dni.

Jeśli chcesz zachować kopie zapasowe dłużej niż maksymalny okres przechowywania, możesz zmodyfikować właściwości kopii zapasowej, aby dodać jeden lub więcej długoterminowych okresów przechowywania do bazy danych. Aby uzyskać więcej informacji, zobacz [Długoterminowe przechowywanie](sql-database-long-term-retention.md).

> [!IMPORTANT]
> Usunięcie programu Azure SQL Server, który hostuje bazy danych SQL, spowoduje również usunięcie wszystkich pul elastycznych i baz danych, które należą do serwera, i nie będzie można ich odzyskać. Nie można przywrócić usuniętego serwera. Jeśli jednak skonfigurowano długoterminowe przechowywanie, kopie zapasowe baz danych z tą literą nie zostaną usunięte i można przywrócić te bazy danych.

## <a name="how-often-do-backups-happen"></a>Jak często wykonywane są kopie zapasowe

### <a name="backups-for-point-in-time-restore"></a>Kopie zapasowe do przywracania do punktu w czasie

SQL Database obsługuje samoobsługowe przywracanie do punktu w czasie (kopie) przez automatyczne tworzenie pełnych kopii zapasowych, różnicowych kopii zapasowych i kopii zapasowych dziennika transakcji. Tworzenie pełnych kopii zapasowych bazy danych jest tworzone co tydzień, różnicowe kopie zapasowe bazy danych są zwykle tworzone co 12 godzin, a kopie zapasowe dziennika transakcji są zwykle tworzone co 5-10 minut, z częstotliwością opartą na wielkości i liczbie działań związanych z bazą danych. Pierwsza pełna kopia zapasowa jest zaplanowana natychmiast po utworzeniu bazy danych. Zwykle kończy się to w ciągu 30 minut, ale może trwać dłużej, gdy baza danych ma znaczący rozmiar. Na przykład początkowa kopia zapasowa może trwać dłużej w przywróconej bazie danych lub kopii bazy danych. Po wykonaniu pierwszej pełnej kopii zapasowej wszystkie dalsze kopie zapasowe są automatycznie zaplanowane i zarządzane w trybie dyskretnym w tle. Dokładny chronometraż wszystkich kopii zapasowych bazy danych jest określany przez usługę SQL Database w miarę zrównoważenia całkowitego obciążenia systemu. Nie można zmienić ani wyłączyć zadań tworzenia kopii zapasowej. 

Kopie zapasowe kopie są dublowane geograficznie i chronione przez międzyregionalną [replikację usługi Azure Storage](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)

Aby uzyskać więcej informacji, zobacz [przywracanie do punktu w czasie](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="backups-for-long-term-retention"></a>Tworzenie kopii zapasowych na potrzeby długoterminowego przechowywania

Pojedyncze i w puli bazy danych oferują możliwość skonfigurowania długoterminowego przechowywania (LTR) dla pełnych kopii zapasowych w okresie do 10 lat w usłudze Azure Blob Storage. Jeśli zasada LTR jest włączona, cotygodniowe pełne kopie zapasowe są automatycznie kopiowane do innego kontenera magazynu RA-GRS. Aby spełnić inne wymagania dotyczące zgodności, można wybrać różne okresy przechowywania dla cotygodniowych, comiesięcznych i/lub corocznych kopii zapasowych. Użycie magazynu zależy od wybranej częstotliwości wykonywania kopii zapasowych i okresów przechowywania. Możesz użyć kalkulatora [cen ltr](https://azure.microsoft.com/pricing/calculator/?service=sql-database) , aby oszacować koszt magazynu ltr.

Podobnie jak w przypadku kopie, kopie zapasowe oddzielone są geograficznie nadmiarowe i chronione przez replikację międzyregionalną [usługi Azure Storage](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage).

Aby uzyskać więcej informacji, zobacz [długoterminowe przechowywanie kopii zapasowych](sql-database-long-term-retention.md).

## <a name="storage-costs"></a>Koszty magazynowania
W przypadku pojedynczych baz danych jest dostępna minimalna ilość miejsca w magazynie kopii zapasowych równa 100% rozmiaru bazy danych. W przypadku pul elastycznych minimalna ilość miejsca w magazynie kopii zapasowych równa 100% przydzielony magazyn danych dla puli jest udostępniana bez dodatkowych opłat. Dodatkowe użycie magazynu kopii zapasowych wiąże się z comiesięczną opłatą za ilość używanych GB. To dodatkowe zużycie będzie zależeć od obciążenia i rozmiaru poszczególnych baz danych.

Aby uzyskać więcej informacji o cenach magazynu, zobacz [](https://azure.microsoft.com/pricing/details/sql-database/single/) stronę z cennikiem. 

## <a name="are-backups-encrypted"></a>Czy kopie zapasowe są szyfrowane

Jeśli baza danych jest zaszyfrowana przy użyciu TDE, kopie zapasowe są automatycznie szyfrowane w stanie spoczynku, łącznie z kopiami zapasowymi LTR. Gdy TDE jest włączona dla bazy danych Azure SQL, kopie zapasowe również są szyfrowane. Wszystkie nowe bazy danych SQL Azure są domyślnie skonfigurowane z włączoną funkcją TDE. Aby uzyskać więcej informacji na temat TDE, zobacz [transparent Data Encryption z Azure SQL Database](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="how-does-microsoft-ensure-backup-integrity"></a>Jak firma Microsoft zapewnia integralność kopii zapasowych

W związku z tym zespół inżynierów Azure SQL Database automatycznie testuje przywracanie zautomatyzowanych kopii zapasowych baz danych znajdujących się na serwerach logicznych i w pulach elastycznych (nie jest to dostępne w wystąpieniu zarządzanym). Po przywróceniu do momentu bazy danych otrzymują również kontrolę integralności przy użyciu polecenia DBCC CHECKDB.

Wystąpienie zarządzane pobiera automatycznie początkową kopię zapasową z `CHECKSUM` baz danych przywróconych przy użyciu natywnego `RESTORE` polecenia lub usługi migracji danych po zakończeniu migracji.

Wszelkie problemy znalezione podczas kontroli integralności spowodują powstanie alertu dla zespołu inżynieryjnego. Aby uzyskać więcej informacji na temat integralności danych w Azure SQL Database, zobacz [integralność danych w Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="how-do-automated-backups-impact-compliance"></a>Jak automatyczne kopie zapasowe wpływają na zgodność

W przypadku migrowania bazy danych z warstwy usług opartych na protokole DTU o wartości domyślnej kopie retencji 35 dni do warstwy usługi opartej na rdzeń wirtualny, przechowywanie kopie jest zachowywane w celu zapewnienia, że zasady odzyskiwania danych aplikacji nie zostały naruszone. Jeśli domyślne przechowywanie nie spełnia wymagań dotyczących zgodności, można zmienić okres przechowywania kopie przy użyciu programu PowerShell lub interfejsu API REST. Aby uzyskać więcej informacji, zobacz [Zmienianie okresu przechowywania kopii zapasowych](#how-to-change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-the-pitr-backup-retention-period"></a>Jak zmienić okres przechowywania kopii zapasowej kopie

Domyślny okres przechowywania kopii zapasowej kopie można zmienić przy użyciu Azure Portal, programu PowerShell lub interfejsu API REST. Obsługiwane są następujące wartości: 7, 14, 21, 28 lub 35 dni. W poniższych przykładach pokazano, jak zmienić przechowywanie kopie na 28 dni.

> [!WARNING]
> W przypadku skrócenia bieżącego okresu przechowywania wszystkie istniejące kopie zapasowe starsze niż nowy okres przechowywania nie będą już dostępne. W przypadku zwiększenia bieżącego okresu przechowywania program SQL Database będzie przechowywał istniejące kopie zapasowe do momentu osiągnięcia dłuższego okresu przechowywania.

> [!NOTE]
> Te interfejsy API będą mieć wpływ tylko na kopie okres przechowywania. Jeśli skonfigurowano opcję LTR dla bazy danych, nie będzie to miało wpływu. Aby uzyskać więcej informacji na temat zmiany okresu przechowywania LTR, zobacz [długoterminowe przechowywanie](sql-database-long-term-retention.md)danych.

### <a name="change-pitr-backup-retention-period-using-the-azure-portal"></a>Zmień okres przechowywania kopii zapasowej kopie przy użyciu Azure Portal

Aby zmienić kopie okres przechowywania kopii zapasowej przy użyciu Azure Portal, przejdź do obiektu serwera, którego okres przechowywania chcesz zmienić w portalu, a następnie wybierz odpowiednią opcję na podstawie tego, który obiekt serwera jest modyfikowany.

#### <a name="change-pitr-for-a-sql-database-server"></a>Zmień kopie dla serwera SQL Database

![Zmień Azure Portal kopie](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="change-pitr-for-a-managed-instance"></a>Zmień kopie dla wystąpienia zarządzanego

![Zmień Azure Portal kopie](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

### <a name="change-pitr-backup-retention-period-using-powershell"></a>Zmienianie okresu przechowywania kopii zapasowych kopie przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-pitr-retention-period-using-rest-api"></a>Zmień okres przechowywania kopie przy użyciu interfejsu API REST

#### <a name="sample-request"></a>Przykładowe żądanie

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>Treść żądania

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>Przykładowa odpowiedź

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

Aby uzyskać więcej informacji, zobacz [interfejs API REST przechowywania kopii zapasowych](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies).

## <a name="next-steps"></a>Następne kroki

- Kopie zapasowe bazy danych są istotną częścią strategii ciągłości działania i odzyskiwania po awarii, ponieważ chronią dane przed przypadkowym uszkodzeniem lub usunięciem. Aby dowiedzieć się więcej na temat innych Azure SQL Database rozwiązań z zakresu ciągłości biznesowej, zobacz temat ciągłość działania [— Omówienie](sql-database-business-continuity.md).
- Aby przywrócić do punktu w czasie za pomocą Azure Portal, zobacz [przywracanie bazy danych do punktu w czasie przy użyciu Azure Portal](sql-database-recovery-using-backups.md).
- Aby przywrócić do punktu w czasie za pomocą programu PowerShell, zobacz [przywracanie bazy danych do punktu w czasie za pomocą programu PowerShell](scripts/sql-database-restore-database-powershell.md).
- Aby skonfigurować, zarządzać i przywracać długoterminowe przechowywanie zautomatyzowanych kopii zapasowych w usłudze Azure Blob Storage za pomocą Azure Portal, zobacz [Zarządzanie długoterminową przechowywaniem kopii zapasowych przy użyciu Azure Portal](sql-database-long-term-backup-retention-configure.md).
- Aby skonfigurować, zarządzać i przywracać długoterminowe przechowywanie zautomatyzowanych kopii zapasowych w usłudze Azure Blob Storage przy użyciu programu PowerShell, zobacz [Zarządzanie długoterminowym przechowywaniem kopii zapasowych za pomocą programu PowerShell](sql-database-long-term-backup-retention-configure.md).
