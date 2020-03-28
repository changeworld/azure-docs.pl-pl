---
title: Synchronizowanie danych z usługi Azure SQL Database Edge przy użyciu synchronizacji danych SQL | Dokumenty firmy Microsoft
description: Dowiedz się więcej o synchronizowaniu danych z usługi Azure SQL Database Edge przy użyciu usługi Azure SQL Data Sync
keywords: krawędź bazy danych sql, synchronizacja danych z krawędzi bazy danych SQL, synchronizacja danych krawędzi bazy danych SQL
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 7c38ba6dbabef4affd8672295a93d46fd4b0e494
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74384181"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-sql-database-by-using-sql-data-sync"></a>Samouczek: Synchronizowanie danych z usługi SQL Database Edge z bazą danych SQL za pomocą synchronizacji danych SQL

W tym samouczku dowiesz się, jak używać *grupy synchronizacji sql* sql do stopniowego synchronizowania danych z usługi Azure SQL Database Edge do bazy danych SQL Azure. SQL Data Sync to usługa oparta na bazie danych SQL Azure, która umożliwia synchronizację wybranych danych dwukierunkowo w wielu bazach danych SQL i wystąpieniach programu SQL Server. Aby uzyskać więcej informacji na temat synchronizacji danych SQL, zobacz [Azure SQL Data Sync](../sql-database/sql-database-sync-data.md).

Ponieważ krawędź bazy danych SQL jest zbudowana na najnowszych wersjach [aparatu bazy danych programu SQL Server,](/sql/sql-server/sql-server-technical-documentation/)każdy mechanizm synchronizacji danych, który ma zastosowanie do lokalnego wystąpienia programu SQL Server, może być również używany do synchronizowania danych z lub z wystąpienia usługi SQL Database Edge uruchomionego na urządzeniu brzegowym.

## <a name="prerequisites"></a>Wymagania wstępne

Ten samouczek wymaga komputera z systemem Windows skonfigurowanego z [agentem synchronizacji danych dla usługi Azure SQL Data Sync](../sql-database/sql-database-data-sync-agent.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Utworzenie bazy danych Azure SQL Database. Aby uzyskać informacje na temat tworzenia bazy danych SQL platformy Azure przy użyciu portalu Azure, zobacz [Tworzenie pojedynczej bazy danych w bazie danych SQL azure.](../sql-database/sql-database-single-database-get-started.md?tabs=azure-portal)

* Tworzenie tabel i innych niezbędnych obiektów we wdrożeniu usługi Azure SQL Database.

* Tworzenie niezbędnych tabel i obiektów we wdrożeniu usługi Azure SQL Database Edge. Aby uzyskać więcej informacji, zobacz [Korzystanie z pakietów DAC bazy danych SQL z krawędzią bazy danych SQL](stream-analytics.md).

* Zarejestruj wystąpienie usługi Azure SQL Database Edge za pomocą agenta synchronizacji danych dla usługi Azure SQL Data Sync. Aby uzyskać więcej informacji, zobacz [Dodawanie lokalnej bazy danych programu SQL Server](../sql-database/sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="sync-data-between-an-azure-sql-database-and-sql-database-edge"></a>Synchronizowanie danych między bazą danych SQL platformy Azure a usługą SQL Database Edge

Konfigurowanie synchronizacji między bazą danych SQL platformy Azure a wystąpieniem usługi SQL Database Edge przy użyciu synchronizacji danych SQL obejmuje trzy kluczowe kroki:  

1. Użyj witryny Azure Portal, aby utworzyć grupę synchronizacji. Aby uzyskać więcej informacji, zobacz [Tworzenie grupy synchronizacji](../sql-database/sql-database-get-started-sql-data-sync.md#create-sync-group). Za pomocą jednej bazy danych *centrum* można utworzyć wiele grup synchronizacji w celu zsynchronizowania danych z różnych wystąpień usługi SQL Database Edge do jednej lub więcej baz danych SQL na platformie Azure.

2. Dodawanie członków synchronizacji do grupy synchronizacji. Aby uzyskać więcej informacji, zobacz [Dodawanie członków synchronizacji](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

3. Skonfiguruj grupę synchronizacji, aby wybrać tabele, które będą częścią synchronizacji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie grupy synchronizacji](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

Po wykonaniu powyższych kroków będziesz mieć grupę synchronizacji, która zawiera bazę danych SQL platformy Azure i wystąpienie usługi SQL Database Edge.

Aby uzyskać więcej informacji na temat synchronizacji danych SQL, zobacz następujące artykuły:

* [Agent synchronizacji danych dla synchronizacji danych SQL usługi Azure](../sql-database/sql-database-data-sync-agent.md)

* [Najważniejsze wskazówki](../sql-database/sql-database-best-practices-data-sync.md) i [jak rozwiązywać problemy z synchronizacją danych SQL usługi Azure](../sql-database/sql-database-troubleshoot-data-sync.md)

* [Monitorowanie synchronizacji danych SQL za pomocą dzienników usługi Azure Monitor](../sql-database/sql-database-sync-monitor-oms.md)

* [Aktualizowanie schematu synchronizacji za pomocą programu Transact-SQL](../sql-database/sql-database-update-sync-schema.md) lub [programu PowerShell](../sql-database/scripts/sql-database-sync-update-schema.md)

## <a name="next-steps"></a>Następne kroki

* [Synchronizacja między usługą Azure SQL Database database i usługą Azure SQL Database Edge za pomocą programu PowerShell.](../sql-database/scripts/sql-database-sync-data-between-azure-onprem.md) W tym samouczku `OnPremiseServer` zastąp szczegóły bazy danych szczegółami usługi Azure SQL Database Edge.
