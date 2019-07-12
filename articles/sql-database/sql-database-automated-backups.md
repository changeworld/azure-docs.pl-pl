---
title: Bazy danych SQL automatycznego, geograficznie nadmiarowych kopii zapasowych Azure | Dokumentacja firmy Microsoft
description: Bazy danych SQL Database automatycznie tworzy kopię zapasową lokalnej bazy danych co kilka minut i korzysta z platformy Azure magazyn geograficznie nadmiarowy geograficznie nadmiarowości geograficznej.
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
ms.date: 06/27/2019
ms.openlocfilehash: ce16450f7f25e5703cf283c4babb2a935aad21de
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813048"
---
# <a name="automated-backups"></a>Automatyczne kopie zapasowe

SQL Database automatycznie tworzy kopie zapasowe bazy danych, które są utrzymywane od 7 do 35 dni i korzysta z platformy Azure [dostęp do odczytu magazynu geograficznie nadmiarowego (RA-GRS)](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) aby upewnić się, że zostaną zachowane nawet jeśli Centrum danych jest niedostępna. Te kopie zapasowe są tworzone automatycznie. Kopie zapasowe bazy danych są integralną część wszelkie strategii odzyskiwania firmy, jak ciągłość działalności biznesowej i odzyskiwanie po awarii, ponieważ one ochronę danych przed przypadkowym uszkodzeniem lub usunięciem. Jeśli reguł zabezpieczeń wymaga, że kopie zapasowe są dostępne przez dłuższy czas (maksymalnie 10 lat), możesz skonfigurować [długoterminowego przechowywania](sql-database-long-term-retention.md) pojedynczych baz danych i elastycznych pul.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Co to jest kopię zapasową bazy danych SQL

SQL Database przy użyciu technologii SQL Server do utworzenia [pełnych kopii zapasowych](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) co tydzień, [różnicowe kopie zapasowe](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) co 12 godzin i [kopie zapasowe dziennika transakcji](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) co 5 – 10 minut. Kopie zapasowe są przechowywane w [obiektów blob magazynu RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) które są replikowane do [sparowanym centrum danych](../best-practices-availability-paired-regions.md) do ochrony przed awariami centrum danych. Po przywróceniu bazy danych usługi wpadł na których kopie zapasowe mają być przywracane dziennika pełnej, różnicowej i transakcji.

Możesz użyć tych kopii zapasowych:

- **Przywróć istniejącą bazę danych do punktu w czasie w przeszłości** w okresie przechowywania, przy użyciu witryny Azure portal, programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST. W pojedynczej bazy danych i pul elastycznych ta operacja spowoduje utworzenie nowej bazy danych, w tym samym serwerze co oryginalna baza danych. W wystąpieniu zarządzanym tę operację można utworzyć kopię bazy danych lub tego samego lub innego wystąpienia zarządzanego w ramach tej samej subskrypcji.
  - **[Zmiana okresu przechowywania kopii zapasowej](#how-to-change-the-pitr-backup-retention-period)**  od 7 do 35 dni, aby skonfigurować zasady tworzenia kopii zapasowej.
  - **Zmień zasady przechowywania długoterminowego na 10 lat** na pojedynczej bazy danych i pul elastycznych za pomocą [witryny Azure portal](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies) lub [programu Azure PowerShell](sql-database-long-term-backup-retention-configure.md#use-powershell-to-configure-long-term-retention-policies-and-restore-backups).
- **Przywracanie usuniętej bazy danych do chwili, została ona usunięta** lub w dowolnym momencie w okresie przechowywania. Tylko można przywrócić usuniętą bazę danych w tym samym serwerze logicznym lub wystąpienia zarządzanego, w której utworzono oryginalnej bazy danych.
- **Przywracanie bazy danych do innego regionu geograficznego**. Funkcja przywracania geograficznego umożliwia odzyskanie geograficzne po awarii, jeśli nie masz dostępu do serwera i bazy danych. Tworzy nową bazę danych w dowolnej istniejącego serwera, w dowolnym miejscu na świecie.
- **Przywracanie bazy danych z określonego długoterminowej kopii zapasowej** na pojedynczej bazy danych lub elastycznej puli, jeśli baza danych została skonfigurowana przy użyciu długoterminowe zasady przechowywania (od lewej do prawej). Od lewej do prawej umożliwia przywracanie bazy danych przy użyciu starszej wersji [witryny Azure portal](sql-database-long-term-backup-retention-configure.md#view-backups-and-restore-from-a-backup-using-azure-portal) lub [programu Azure PowerShell](sql-database-long-term-backup-retention-configure.md#use-powershell-to-configure-long-term-retention-policies-and-restore-backups) zrealizować żądanie zgodności lub uruchom starą wersję aplikacji. Aby uzyskać więcej informacji, zobacz [Długoterminowe przechowywanie](sql-database-long-term-retention.md).
- Aby wykonać przywracanie, zobacz [przywrócić bazę danych z kopii zapasowych](sql-database-recovery-using-backups.md).

> [!NOTE]
> W usłudze Azure storage termin *replikacji* odwołuje się do kopiowania plików z jednej lokalizacji do innej. SQL *replikacji bazy danych* odwołuje się do przechowywanie wielu pomocniczych baz danych synchronizowane z podstawowej bazy danych.

Możesz wypróbować niektóre z tych operacji przy użyciu poniższych przykładach:

| | Witryna Azure Portal | Azure PowerShell |
|---|---|---|
| Zmiana okresu przechowywania kopii zapasowych | [Pojedynczą bazę danych](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-the-azure-portal) <br/> [Wystąpienie zarządzane](sql-database-automated-backups.md#change-pitr-for-a-managed-instance) | [Pojedynczą bazę danych](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-powershell) <br/>[Wystąpienie zarządzane](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Zmiana długoterminowego przechowywania kopii zapasowych | [Pojedyncza baza danych](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Wystąpienie zarządzane - n/d  | [Pojedynczą bazę danych](sql-database-long-term-backup-retention-configure.md#use-powershell-to-configure-long-term-retention-policies-and-restore-backups)<br/>Wystąpienie zarządzane - n/d  |
| Przywracanie bazy danych z punktu w czasie | [Pojedyncza baza danych](sql-database-recovery-using-backups.md#point-in-time-restore) | [Pojedyncza baza danych](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Wystąpienie zarządzane](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Przywracanie usuniętej bazy danych | [Pojedyncza baza danych](sql-database-recovery-using-backups.md#deleted-database-restore-using-the-azure-portal) | [Pojedyncza baza danych](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Wystąpienie zarządzane](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Przywracanie bazy danych z usługi Azure Blob Storage | Pojedynczą bazę danych - n/d <br/>Wystąpienie zarządzane - n/d  | Pojedynczą bazę danych - n/d <br/>[Wystąpienie zarządzane](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="how-long-are-backups-kept"></a>Jak długo są przechowywane kopie zapasowe

Każda baza danych SQL ma domyślny okres przechowywania kopii zapasowych, od 7 do 35 dni, od których zależy model zakupu i warstwy usług. Można zaktualizować okresu przechowywania kopii zapasowej dla bazy danych na serwerze bazy danych SQL. Aby uzyskać więcej informacji, zobacz [okres przechowywania kopii zapasowej zmiany](#how-to-change-the-pitr-backup-retention-period).

Jeśli usuniesz bazę danych, SQL Database zostanie zachowana kopie zapasowe w taki sam sposób jak dla bazy danych online. Na przykład po usunięciu podstawowej bazy danych zawierającej okresu przechowywania siedmiu dni, kopii zapasowej, która jest cztery dni zostanie zapisany przez trzy kolejne dni.

Jeśli zachodzi potrzeba przechowywanie kopii zapasowych przez okres dłuższy niż maksymalny okres przechowywania, można zmodyfikować właściwości kopii zapasowej, aby dodać jeden lub więcej okresów przechowywania długoterminowego do bazy danych. Aby uzyskać więcej informacji, zobacz [Długoterminowe przechowywanie](sql-database-long-term-retention.md).

> [!IMPORTANT]
> Jeśli usuniesz serwera Azure SQL, który jest hostem bazy danych SQL, wszystkie elastycznych pul i baz danych, które należą do serwera również zostaną usunięte i nie można odzyskać. Nie można przywrócić usuniętego serwera. Ale jeśli skonfigurowano przechowywanie długoterminowe kopie zapasowe baz danych przy użyciu od lewej do prawej, nie zostaną usunięte i te bazy danych, które mogą zostać przywrócone.

### <a name="default-backup-retention-period"></a>Domyślny okres przechowywania kopii zapasowych

#### <a name="dtu-based-purchasing-model"></a>Model zakupu w oparciu o jednostki DTU

Domyślny okres przechowywania dla bazy danych utworzone za pomocą modelu zakupu opartego na jednostkach DTU zależy od warstwy usługi:

- Warstwa podstawowa usług jest **jeden** tygodnia.
- Warstwa usług w warstwie standardowa jest **pięć** tygodni.
- Warstwa Premium usług jest **pięć** tygodni.

#### <a name="vcore-based-purchasing-model"></a>Model zakupów oparty na rdzeniach wirtualnych

Jeśli używasz [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md), domyślny okres przechowywania kopii zapasowych to **siedem** dni (dla pojedynczego, puli i wystąpienia bazy danych). Dla wszystkich baz danych Azure SQL (pojedynczy, puli i bazy danych wystąpień, możesz [zmienić okres przechowywania kopii zapasowej do 35 dni](#how-to-change-the-pitr-backup-retention-period).

> [!WARNING]
> Czy można zmniejszyć bieżącym okresem przechowywania, wszystkie istniejące kopie zapasowe starsze niż nowy okres przechowywania nie są już dostępne. Jeśli zwiększysz bieżącego okresu przechowywania bazy danych SQL Database zostanie zachowana istniejące kopie zapasowe, aż do osiągnięcia dłuższy okres przechowywania danych.

## <a name="how-often-do-backups-happen"></a>Jak często stanie kopie zapasowe

### <a name="backups-for-point-in-time-restore"></a>Tworzenie kopii zapasowych do punktu w czasie przywracania

SQL Database obsługuje samoobsługowego przywracania w momencie (Odzyskiwanie) przez automatyczne tworzenie pełnej kopii zapasowej, różnicowe kopie zapasowe i kopie zapasowe dziennika transakcji. Pełne kopie zapasowe są tworzone co tydzień, różnicowe kopie zapasowe są zazwyczaj tworzone co 12 godzin i kopie zapasowe dziennika transakcji są zazwyczaj tworzone co 5 – 10 minut, z częstotliwością, na podstawie rozmiaru obliczeń i zmniejszenia liczby działań bazy danych. Pierwsza pełna kopia zapasowa jest zaplanowane, natychmiast, po utworzeniu bazy danych. Zazwyczaj zostanie zakończona w ciągu 30 minut, ale może to trwać dłużej, gdy baza danych znajduje się o znacznym rozmiarze. Na przykład tworzenia początkowej kopii zapasowej może potrwać dłużej przywróconej bazy danych lub kopii bazy danych. Po pierwszej pełnej kopii zapasowej wszystkie dodatkowe kopie zapasowe są zaplanowane automatycznie i zarządzane w trybie dyskretnym w tle. Dokładny czas wszystkie kopie zapasowe bazy danych jest określany przez usługę SQL Database jako jej całkowitego obciążenia systemu. Nie można zmienić ani wyłączyć zadania tworzenia kopii zapasowej. 

Odzyskiwanie kopii zapasowych są geograficznie nadmiarowy i chronione przez [replikacji między regionami w usłudze Azure Storage](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)

Aby uzyskać więcej informacji, zobacz [punktu w czasie przywracania](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="backups-for-long-term-retention"></a>Długoterminowe przechowywanie kopii zapasowych

Jedno- i puli baz danych umożliwiają konfigurowanie długoterminowego przechowywania danych (od lewej do prawej) pełnych kopii zapasowych dla maksymalnie 10 lat w usłudze Azure Blob storage. Włączenie zasad pisowni LTR tygodniowe pełne kopie zapasowe są automatycznie kopiowane do innego kontenera magazynu RA-GRS. Aby spełnić wymagania zgodności w różnych, możesz wybrać różnych okresów przechowywania dla kopii zapasowych co tydzień, miesięczny lub roczny. Użycie magazynu zależy od wybranej częstotliwości tworzenia kopii zapasowych i okresy przechowywania. Możesz użyć [Kalkulator cen od lewej do prawej](https://azure.microsoft.com/pricing/calculator/?service=sql-database) do szacowania kosztów magazynowania od lewej do prawej.

Podobnie jak Odzyskiwanie, kopie zapasowe od lewej do prawej są geograficznie nadmiarowy i chronione przez [replikacji między regionami w usłudze Azure Storage](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage).

Aby uzyskać więcej informacji, zobacz [długoterminowego przechowywania kopii zapasowych](sql-database-long-term-retention.md).

## <a name="storage-costs"></a>Koszty magazynowania
Domyślnie automatyczne kopie zapasowe baz danych z siedmu dni są kopiowane do magazynu obiektów blob w warstwie RA-GRS Standardowa. Magazyn jest używany przez tygodniowe pełne kopie zapasowe, codzienne różnicowe kopie zapasowe oraz kopie zapasowe dzienników transakcji kopiowane co 5 minut. Rozmiar dziennika transakcji, zależy od szybkości zmian bazy danych. Bez dodatkowych kosztów jest zapewniany minimalny magazyn o rozmiarze wynoszącym 100% rozmiaru bazy danych. Dodatkowe użycie magazynu kopii zapasowych wiąże się z comiesięczną opłatą za ilość używanych GB.

Aby uzyskać więcej informacji na temat cen magazynu, zobacz [ceny](https://azure.microsoft.com/pricing/details/sql-database/single/) strony. 

## <a name="are-backups-encrypted"></a>Kopie zapasowe są szyfrowane

Jeśli baza danych jest szyfrowana za pomocą funkcji TDE, kopie zapasowe są automatycznie szyfrowane w stanie spoczynku w tym kopie zapasowe od lewej do prawej. Gdy funkcja TDE jest włączona dla usługi Azure SQL database, tworzenie kopii zapasowych również są szyfrowane. Wszystkie nowe bazy danych Azure SQL są skonfigurowane za pomocą funkcji TDE jest domyślnie włączone. Aby uzyskać więcej informacji na temat funkcji TDE, zobacz [funkcji Transparent Data Encryption z usługą Azure SQL Database](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="how-does-microsoft-ensure-backup-integrity"></a>Jak usługa Microsoft zabezpiecza integralność kopii zapasowej

Na bieżąco usługa Azure SQL Database, zespół inżynierów automatycznie testy przywracania automatycznych kopii zapasowych baz danych rozmieszczonych w serwerów logicznych i pul elastycznych (nie jest dostępny w wystąpieniu zarządzanym usługi). Podczas przywracania do punktu w czasie baz danych uzyskują również sprawdzania integralności za pomocą polecenia DBCC CHECKDB.

Wystąpienie zarządzane Trwa automatyczne tworzenie początkowej kopii zapasowej za pomocą `CHECKSUM` baz danych, przywracać za pomocą natywnego `RESTORE` polecenia lub program Data Migration Service po zakończeniu migracji.

Problemy znalezione podczas sprawdzania integralności spowoduje alert do zespołu inżynieryjnego. Aby uzyskać więcej informacji o funkcji integralność danych w usłudze Azure SQL Database, zobacz [integralność danych w usłudze Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="how-do-automated-backups-impact-compliance"></a>Jak automatycznie tworzonymi kopiami zapasowymi wpływ na zgodność

Podczas migracji bazy danych z warstwy usług oparte na jednostkach DTU z przechowywaniem Odzyskiwanie domyślne 35 dni do warstwy usług oparte na rdzeniach wirtualnych, aby upewnić się, że zasady odzyskiwania danych aplikacji nie są zagrożone zachowywana jest przechowywania Odzyskiwanie. Jeśli domyślny okres przechowywania nie spełnia wymagań dotyczących zgodności, możesz zmienić okres przechowywania Odzyskiwanie przy użyciu programu PowerShell lub interfejsu API REST. Aby uzyskać więcej informacji, zobacz [okres przechowywania kopii zapasowej zmiany](#how-to-change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-the-pitr-backup-retention-period"></a>Jak zmienić okres przechowywania kopii zapasowej Odzyskiwanie

Można zmienić domyślny okres przechowywania kopii zapasowych Odzyskiwanie przy użyciu witryny Azure portal, programu PowerShell lub interfejsu API REST. Obsługiwane wartości to: 7, 14, 21, 28 lub 35 dni. Poniższe przykłady ilustrują zmiana okresu przechowywania Odzyskiwanie 28 dni.

> [!NOTE]
> Te interfejsy API będą mają wpływ tylko na okres przechowywania Odzyskiwanie. Jeśli od lewej do prawej są skonfigurowane dla bazy danych, nie będzie mieć wpływ. Aby uzyskać więcej informacji na temat zmiany okresy przechowywania od lewej do prawej, zobacz [długoterminowego przechowywania](sql-database-long-term-retention.md).

### <a name="change-pitr-backup-retention-period-using-the-azure-portal"></a>Zmiana okresu przechowywania kopii zapasowej Odzyskiwanie przy użyciu witryny Azure portal

Aby zmienić okres przechowywania kopii zapasowej Odzyskiwanie przy użyciu witryny Azure portal, przejdź do obiektu serwera okres przechowywania, którego chcesz zmienić w portalu, a następnie wybierz odpowiednią opcję na podstawie na obiektu serwera, który jest modyfikowany.

#### <a name="change-pitr-for-a-sql-database-server"></a>Zmiana Odzyskiwanie dla serwera bazy danych SQL

![Portal Azure Odzyskiwanie zmiany](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="change-pitr-for-a-managed-instance"></a>Zmiana Odzyskiwanie dla wystąpienia zarządzanego

![Portal Azure Odzyskiwanie zmiany](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

### <a name="change-pitr-backup-retention-period-using-powershell"></a>Zmiana okresu przechowywania kopii zapasowej Odzyskiwanie przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł programu PowerShell usługi Azure Resource Manager jest nadal obsługiwane przez usługę Azure SQL Database, ale wszystkie przyszłego rozwoju jest Az.Sql modułu. Dla tych poleceń cmdlet, zobacz [elementu AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w Az module, a w modułach AzureRm są zasadniczo identyczne.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-pitr-retention-period-using-rest-api"></a>Zmiana okresu przechowywania Odzyskiwanie przy użyciu interfejsu API REST

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

Aby uzyskać więcej informacji, zobacz [interfejsu API REST przechowywania kopii zapasowej](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies).

## <a name="next-steps"></a>Kolejne kroki

- Kopie zapasowe bazy danych są integralną część wszelkie strategii odzyskiwania firmy, jak ciągłość działalności biznesowej i odzyskiwanie po awarii, ponieważ one ochronę danych przed przypadkowym uszkodzeniem lub usunięciem. Aby poznać inne usługi Azure SQL Database rozwiązania zapewniające ciągłość działania, zobacz temat [omówienie ciągłości działania](sql-database-business-continuity.md).
- Aby przywrócić do punktu w czasie za pomocą witryny Azure portal, zobacz [przywrócić bazę danych do punktu w czasie za pomocą witryny Azure portal](sql-database-recovery-using-backups.md).
- Aby przywrócić do punktu w czasie za pomocą programu PowerShell, zobacz [przywrócić bazę danych do punktu w czasie za pomocą programu PowerShell](scripts/sql-database-restore-database-powershell.md).
- Konfigurowanie, zarządzanie i przywracać długoterminowego przechowywania automatycznych kopii zapasowych w magazynie obiektów Blob platformy Azure przy użyciu witryny Azure portal, zobacz [Zarządzanie długotrwałym przechowywaniem kopii zapasowych przy użyciu witryny Azure portal](sql-database-long-term-backup-retention-configure.md).
- Konfigurowanie, zarządzanie i przywracać długoterminowego przechowywania automatycznych kopii zapasowych w usłudze Azure Blob storage przy użyciu programu PowerShell, zobacz [Zarządzanie długotrwałym przechowywaniem kopii zapasowych przy użyciu programu PowerShell](sql-database-long-term-backup-retention-configure.md).
