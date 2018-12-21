---
title: Przykładowe skrypty programu Azure PowerShell dla usługi SQL Database | Microsoft Docs
description: Przykładowe skrypty programu Azure PowerShell umożliwiające tworzenie serwerów, elastycznych pul, baz danych i zapór usługi Azure SQL Database oraz zarządzanie nimi.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 055ddfa5c0fb399eea4bbe46126626b60bfe9cff
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52966934"
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Przykłady programu Azure PowerShell dla usługi Azure SQL Database

Poniższa tabela zawiera linki do przykładowych skryptów programu Azure PowerShell dla usługi Azure SQL Database.

| |  |
|---|---|
|**Tworzenie i konfigurowanie wystąpienia zarządzanego, pojedynczej bazy danych oraz elastycznej puli**||
| [Tworzenie pojedynczej bazy danych i konfigurowanie reguły zapory](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu Azure PowerShell tworzy jedną bazę danych Azure SQL Database i konfiguruje regułę zapory na poziomie serwera. |
| [Tworzenie pul elastycznych i przenoszenie baz danych w puli](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell tworzy elastyczne pule usługi Azure SQL Database, przenosi bazy danych w puli i zmienia rozmiar zasobów obliczeniowych.|
| [Tworzenie wystąpienia zarządzanego i zarządzanie nim](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/) | Ten skrypt programu PowerShell pokazuje, jak utworzyć wystąpienie zarządzane i zarządzać nim przy użyciu programu Azure PowerShell |
| [Tworzenie wystąpienia zarządzanego za pomocą szablonu usługi Azure Resource Manager oraz zarządzanie nim](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell pokazuje, jak utworzyć wystąpienie zarządzane i zarządzać nim przy użyciu programu Azure PowerShell i szablonu usługi Azure Resource Manager.|
|**Konfigurowanie replikacji geograficznej i trybu failover**||
| [Konfigurowanie pojedynczej bazy danych i wprowadzanie jej w tryb failover przy użyciu funkcji aktywnej replikacji geograficznej](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ten skrypt programu PowerShell umożliwia skonfigurowanie aktywnej replikacji geograficznej dla jednej bazy danych Azure SQL Database i wprowadzenie jej w tryb failover do repliki pomocniczej. |
| [Konfigurowanie bazy danych w puli i wprowadzanie jej w tryb failover przy użyciu funkcji aktywnej replikacji geograficznej](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ten skrypt programu PowerShell umożliwia skonfigurowanie aktywnej replikacji geograficznej dla bazy danych Azure SQL Database w elastycznej puli SQL i wprowadzenie jej w tryb failover do repliki pomocniczej. |
| [Konfigurowanie grupy trybu failover i wprowadzanie jej w tryb failover dla jednej bazy danych](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell umożliwia skonfigurowanie grupy trybu failover dla wystąpienia serwera Azure SQL Database, dodanie bazy danych do grupy trybu failover oraz wprowadzenie jej w tryb failover na serwerze pomocniczym. |
|**Skalowanie pojedynczej bazy danych i puli elastycznej**||
| [Skalowanie pojedynczej bazy danych](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell monitoruje metryki wydajności bazy danych SQL platformy Azure, skaluje w górę jej zasoby obliczeniowe i tworzy regułę alertu dla jednej z metryk wydajności. |
| [Skalowanie puli elastycznej](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell monitoruje metryki wydajności elastycznej puli Azure SQL Database, skaluje w górę jej zasoby obliczeniowe i tworzy regułę alertu dla jednej z metryk wydajności.  |
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
| **Konfigurowanie funkcji Transparent Data Encryption (TDE)**||
| [Zarządzanie funkcją Transparent Data Encryption w wystąpieniu zarządzanym przy użyciu własnego klucza z usługi Azure Key Vault](scripts/transparent-data-encryption-byok-sql-managed-instance-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ten skrypt programu PowerShell konfiguruje funkcję Transparent Data Encryption (TDE) w scenariuszu własnego klucza (BYOK, Bring Your Own Key) dla wystąpienia zarządzanego usługi Azure SQL przy użyciu klucza z usługi Azure Key Vault|
|||
