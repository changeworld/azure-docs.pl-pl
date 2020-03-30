---
title: Informacje o wersji dla serwera kopii zapasowych platformy Microsoft Azure w wersji 3
description: Ten artykuł zawiera informacje o znanych problemach i obejściach dotyczących programu Microsoft Azure Backup Server (MABS) w wersji 3.
ms.topic: conceptual
ms.date: 11/22/2018
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: b47d83e0e3714f3f035397acaadeac9cda39d12a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172277"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Informacje o wersji dla serwera kopii zapasowych platformy Microsoft Azure

Ten artykuł zawiera znane problemy i obejścia dotyczące programu Microsoft Azure Backup Server (MABS) V3.

## <a name="backup-and-recovery-fails-for-clustered-workloads"></a>Tworzenie kopii zapasowych i odzyskiwanie kończy się niepowodzeniem dla obciążeń klastrowanych

**Opis:** Kopia zapasowa/przywracanie kończy się niepowodzeniem dla klastrowanych źródeł danych, takich jak klaster funkcji Hyper-V lub klaster SQL (SQL Always On) lub Exchange w grupie dostępności bazy danych (DAG) po uaktualnieniu mabs v2 do MABS V3.

**Obejście:** Aby temu zapobiec, otwórz program SQL Server Management Studio (SSMS)) i uruchom następujący skrypt SQL w dpm DB:

```sql
    IF EXISTS (SELECT * FROM dbo.sysobjects
        WHERE id = OBJECT_ID(N'[dbo].[tbl_PRM_DatasourceLastActiveServerMap]')
        AND OBJECTPROPERTY(id, N'IsUserTable') = 1)
        DROP TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap]
        GO

        CREATE TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] (
            [DatasourceId]          [GUID]          NOT NULL,
            [ActiveNode]            [nvarchar](256) NULL,
            [IsGCed]                [bit]           NOT NULL
            ) ON [PRIMARY]
        GO

        ALTER TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] ADD
    CONSTRAINT [pk__tbl_PRM_DatasourceLastActiveServerMap__DatasourceId] PRIMARY KEY NONCLUSTERED
        (
            [DatasourceId]
        )  ON [PRIMARY],

    CONSTRAINT [DF_tbl_PRM_DatasourceLastActiveServerMap_IsGCed] DEFAULT
        (
            0
        ) FOR [IsGCed]
    GO
```

## <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>Uaktualnienie do MABS V3 kończy się niepowodzeniem w rosyjskich ustawieniach regionalnych

**Opis:** Uaktualnienie z MABS V2 do MABS V3 w języku rosyjskim nie powiedzie się z kodem błędu **4387**.

**Obejście:** Wykonaj następujące kroki, aby uaktualnić do mabs V3 przy użyciu rosyjskiego pakietu instalacyjnego:

1. [Utwórz kopię zapasową](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server?view=sql-server-2017#SSMSProcedure) bazy danych SQL i odinstaluj mabs v2 (wybierz zachować chronione dane podczas odinstalowywania).
2. Uaktualnij do programu SQL 2017 (Enterprise) i odinstaluj raportowanie w ramach uaktualniania.
3. [Instalacja](https://docs.microsoft.com/sql/reporting-services/install-windows/install-reporting-services?view=sql-server-2017#install-your-report-server) Usługi SQL Server Reporting Services (SSRS).
4. [Instalacja](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) SQL Server Management Studio (SSMS).
5. Skonfiguruj raportowanie przy użyciu parametrów udokumentowanych w [konfiguracji SSRS za pomocą programu SQL 2017](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#upgrade-mabs).
6. [Instalacja](backup-azure-microsoft-azure-backup.md) MABS V3.
7. [Przywracanie](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms?view=sql-server-2017) SQL przy użyciu SSMS i uruchomić narzędzie DPM-Sync, jak opisano [tutaj](https://docs.microsoft.com/previous-versions/system-center/data-protection-manager-2010/ff634215(v=technet.10)).
8. Zaktualizuj właściwość "DataBaseVersion" w tabeli dbo.tbl_DLS_GlobalSetting za pomocą następującego polecenia:

```sql
        UPDATE dbo.tbl_DLS_GlobalSetting
        set PropertyValue = '13.0.415.0'
        where PropertyName = 'DatabaseVersion'
```

9. Uruchom usługę MSDPM.

## <a name="next-steps"></a>Następne kroki

[Co nowego w MABS V3](backup-mabs-whats-new-mabs.md)
