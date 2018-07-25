---
title: Przykładowe skrypty programu Azure PowerShell dla usługi SQL Database | Microsoft Docs
description: Przykładowe skrypty programu Azure PowerShell umożliwiające tworzenie serwerów, elastycznych pul, baz danych i zapór usługi Azure SQL Database oraz zarządzanie nimi.
services: sql-database
documentationcenter: sql-database
author: CarlRabeler
manager: craigg
editor: tysonn
tags: azure-service-management
ms.service: sql-database
ms.custom: overview-samples, mvc
ms.devlang: PowerShell
ms.topic: sample
ms.date: 07/16/2018
ms.author: carlrab
ms.openlocfilehash: 81f39c2ecc014ee53dcff2fab88ff0f890e1a610
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39091893"
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Przykłady programu Azure PowerShell dla usługi Azure SQL Database

Poniższa tabela zawiera linki do przykładowych skryptów programu Azure PowerShell dla usługi Azure SQL Database.

| |  |
|---|---|
|**Tworzenie pojedynczej bazy danych i elastycznej puli**||
| [Tworzenie pojedynczej bazy danych i konfigurowanie reguły zapory](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu Azure PowerShell tworzy jedną bazę danych Azure SQL Database i konfiguruje regułę zapory na poziomie serwera. |
| [Tworzenie pul elastycznych i przenoszenie baz danych w puli](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu Azure PowerShell tworzy elastyczne pule usługi Azure SQL Database, przenosi bazy danych w puli i zmienia poziomy wydajności.|
|**Konfigurowanie replikacji geograficznej i trybu failover**||
| [Konfigurowanie pojedynczej bazy danych i wprowadzanie jej w tryb failover przy użyciu funkcji aktywnej replikacji geograficznej](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ten skrypt programu PowerShell umożliwia skonfigurowanie aktywnej replikacji geograficznej dla jednej bazy danych Azure SQL Database i wprowadzenie jej w tryb failover do repliki pomocniczej. |
| [Konfigurowanie bazy danych w puli i wprowadzanie jej w tryb failover przy użyciu funkcji aktywnej replikacji geograficznej](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ten skrypt programu PowerShell umożliwia skonfigurowanie aktywnej replikacji geograficznej dla bazy danych Azure SQL Database w elastycznej puli SQL i wprowadzenie jej w tryb failover do repliki pomocniczej. |
| [Konfigurowanie grupy trybu failover i wprowadzanie jej w tryb failover dla jednej bazy danych](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell umożliwia skonfigurowanie grupy trybu failover dla wystąpienia serwera Azure SQL Database, dodanie bazy danych do grupy trybu failover oraz wprowadzenie jej w tryb failover na serwerze pomocniczym. |
|**Skalowanie pojedynczej bazy danych i puli elastycznej**||
| [Skalowanie pojedynczej bazy danych](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell monitoruje metryki wydajności bazy danych Azure SQL, skaluje ją na wyższy poziom wydajności i tworzy regułę alertu dla jednej z metryk wydajności. |
| [Skalowanie puli elastycznej](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell monitoruje metryki wydajności elastycznej puli Azure SQL Database, skaluje ją na wyższy poziom wydajności oraz tworzy regułę alertu dla jednej z metryk wydajności.  |
| **Inspekcja i wykrywanie zagrożeń** |
| [Konfigurowanie inspekcji i wykrywania zagrożeń](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ten skrypt programu PowerShell umożliwia skonfigurowanie inspekcji i wykrywania zagrożeń dla bazy danych Azure SQL Database. |
| **Przywracanie, kopiowanie i importowanie bazy danych**||
| [Przywracanie bazy danych](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ten skrypt programu PowerShell umożliwia przywrócenie bazy danych Azure SQL Database z geograficznie nadmiarowej kopii zapasowej i przywrócenie najnowszej kopii zapasowej usuniętej bazy danych Azure SQL Database. |
| [Kopiowanie bazy danych na nowy serwer](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ten skrypt programu PowerShell umożliwia utworzenie kopii istniejącej bazy danych Azure SQL Database na nowym serwerze Azure SQL. |
| [Importowanie bazy danych z pliku BACPAC](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ten skrypt programu PowerShell umożliwia zaimportowanie bazy danych z pliku BACPAC na serwer Azure SQL. |
| **Synchronizowanie danych między bazami danych**||
| [Synchronizowanie danych między bazami danych SQL](scripts/sql-database-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell umożliwia skonfigurowanie funkcji synchronizacji danych w celu przeprowadzenia synchronizacji wielu baz danych usługi Azure SQL Database. |
| [Synchronizowanie danych między bazą danych SQL Database i lokalnym programem SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell umożliwia skonfigurowanie synchronizacji danych w celu przeprowadzenia synchronizacji pomiędzy bazą danych usługi Azure SQL Database i lokalną bazą danych programu SQL Server. |
| [Aktualizacja schematu synchronizacji usługi SQL Data Sync](scripts/sql-database-sync-update-schema.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell umożliwia dodanie lub usunięcie elementu ze schematu synchronizacji usługi Data Sync. |
|||
