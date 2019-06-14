---
title: Informacje o wersji programu Microsoft Azure Backup Server w wersji 3
description: Ten artykuł zawiera informacje na temat znanych problemów i rozwiązania dla serwera usługi Mab w wersji 3.
services: backup
author: JYOTHIRMAISURI
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 11/22/2018
ms.author: v-jysur
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: d37245d7eed39ee9d219578db9e0a50d758ba9a2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60499702"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Informacje o wersji usługi Microsoft Azure Backup Server
Ten artykuł zawiera znane problemy i rozwiązania dla programu Microsoft Azure Backup Server (MABS) w wersji 3.

##  <a name="backup-and-recovery-fails-for-clustered-workloads"></a>Kopia zapasowa i odzyskiwanie nie powiedzie się dla obciążeń klastrowanych

**Opis:** / Przywracania kopii zapasowej nie powiedzie się dla źródeł klastrowane dane, takie jak klaster funkcji Hyper-V lub klastra programu SQL (SQL Always On) lub programu Exchange w grupie dostępności bazy danych (DAG) po uaktualnieniu serwera usługi Mab V2 do V3 serwera usługi MAB.

**Obejścia:** Aby temu zapobiec, Otwórz program SQL Server Management Studio (SSMS)) i uruchom następujący skrypt SQL dla bazy danych programu DPM:


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


##  <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>Uaktualnienie do serwera usługi Mab w wersji 3 nie powiedzie się w ustawieniach regionalnych rosyjski

**Opis:** Uaktualnienie z serwera usługi Mab w wersji 2 do serwera usługi Mab w wersji 3 w ustawieniach regionalnych rosyjski zakończy się niepowodzeniem z kodem błędu **4387**.

**Obejścia:** Wykonaj poniższe kroki, aby uaktualnić do serwera usługi Mab w wersji 3 za pomocą rosyjski zainstaluj pakiet:

1.  [Kopia zapasowa](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server?view=sql-server-2017#SSMSProcedure) SQL bazy danych i odinstalowywania serwera usługi Mab w wersji 2 (chce zachować chronione dane w czasie odinstalowywania).
2.  Uaktualnianie do programu SQL 2017 (przedsiębiorstwo), a następnie odinstaluj, zgłoszenia w ramach uaktualnienia.
3. [Zainstaluj](https://docs.microsoft.com/sql/reporting-services/install-windows/install-reporting-services?view=sql-server-2017#install-your-report-server) SQL Server Reporting Services (SSRS).
4.  [Zainstaluj](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017#ssms-installation-tips-and-issues-ssms-1791) SQL Server Management Studio (SSMS).
5.  Konfigurowanie raportowania przy użyciu parametrów, zgodnie z opisem w [konfiguracji usługi SSRS z programem SQL 2017](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#upgrade-mabs).
6.  [Zainstaluj](backup-azure-microsoft-azure-backup.md) serwera usługi Mab w wersji 3.
7. [Przywróć](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms?view=sql-server-2017) SQL przy użyciu programu SSMS i uruchom narzędzie synchronizacji Program DPM, zgodnie z opisem [tutaj](https://docs.microsoft.com/previous-versions/system-center/data-protection-manager-2010/ff634215(v=technet.10)).
8.  Zaktualizuj właściwość "DataBaseVersion" w tabeli dbo.tbl_DLS_GlobalSetting za pomocą następującego polecenia:

        UPDATE dbo.tbl_DLS_GlobalSetting
        set PropertyValue = '13.0.415.0'
        where PropertyName = 'DatabaseVersion'


9.  Uruchom usługę programu MSDPM.


## <a name="next-steps"></a>Kolejne kroki

[What's New in serwera usługi Mab w wersji 3](backup-mabs-whats-new-mabs.md)
