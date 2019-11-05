---
title: Synchronizuj dane z Azure SQL Database Edge przy użyciu SQL Data Sync | Microsoft Docs
description: Informacje na temat synchronizowania danych między Azure SQL Database Edge przy użyciu usługi Azure SQL Data Sync
keywords: Baza danych SQL Database, synchronizacja danych z usługi SQL Database Edge, synchronizacja danych programu SQL Database Edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 857cee30ac4c1002fb7ca57d6be5fa461a14e9ee
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501316"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-sql-database-using-sql-data-sync"></a>Samouczek: synchronizacja danych z SQL Database Edge do Azure SQL Database przy użyciu SQL Data Sync

W ramach tego samouczka nauczysz się używać *grupy synchronizacji* SQL Data Sync do przyrostowej synchronizacji danych z Azure SQL Database Edge do Azure SQL Database. SQL Data Sync to usługa oparta na Azure SQL Database, która pozwala synchronizować dane, które są wybierane dwukierunkowo w wielu bazach danych SQL i wystąpieniach SQL Server. Aby uzyskać więcej informacji na temat usługi Azure SQL Data Sync, zobacz [azure SQL Data Sync](../sql-database/sql-database-sync-data.md).

Ponieważ Azure SQL Database Edge jest oparta na najnowszych wersjach [aparatu bazy danych Microsoft SQL Server](/sql/sql-server/sql-server-technical-documentation/), każdy mechanizm synchronizacji danych, który ma zastosowanie do lokalnego wystąpienia SQL Server może również służyć do synchronizowania danych z lub z SQL Database krawędzi wystąpienie uruchomione na urządzeniu brzegowym.

## <a name="prerequisites"></a>Wymagania wstępne

Ten samouczek wymaga komputera z systemem Windows skonfigurowany przy użyciu [agenta SQL Data Sync platformy Azure](../sql-database/sql-database-data-sync-agent.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Utwórz Azure SQL Database. Aby uzyskać informacje na temat sposobu tworzenia Azure SQL Database przy użyciu Azure Portal, zobacz [Tworzenie pojedynczej bazy danych w Azure SQL Database](../sql-database/sql-database-single-database-get-started.md?tabs=azure-portal).

* Utwórz tabele i inne niezbędne obiekty we wdrożeniu Azure SQL Database.

* Utwórz niezbędne tabele i obiekty w rozmieszczeniu Azure SQL Database Edge. Aby uzyskać więcej informacji, zobacz [używanie SQL Database pakietów DAC z SQL Database Edge](stream-analytics.md).

* Zarejestruj wystąpienie Azure SQL Database Edge przy użyciu agenta SQL Data Sync platformy Azure. Aby uzyskać więcej informacji, zobacz [Dodawanie lokalnej bazy danych SQL Server](../sql-database/sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="sync-data-between-an-azure-sql-database-and-sql-database-edge"></a>Synchronizuj dane między Azure SQL Database i SQL Database Edge

Konfigurowanie synchronizacji między Azure SQL Database i wystąpieniem krawędzi SQL Database przy użyciu SQL Data Sync obejmuje trzy kluczowe kroki.  

1. Użyj Azure Portal, aby utworzyć grupę synchronizacji. Aby utworzyć grupę synchronizacji, zobacz [Tworzenie grupy synchronizacji przy użyciu Azure Portal](../sql-database/sql-database-get-started-sql-data-sync.md#create-sync-group). Ta sama baza danych *centrum* może służyć do tworzenia wielu różnych grup synchronizacji w celu synchronizowania danych z różnych SQL Database wystąpieniami krawędzi z jedną lub kilkoma bazami danych SQL na platformie Azure.

2. Dodaj elementy członkowskie synchronizacji do grupy synchronizacji. Aby dodać członków do grupy synchronizacji, [Dodaj członków do grupy SQL Data Sync](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

3. Skonfiguruj grupę synchronizacji, aby wybrać tabele, które będą częścią tej synchronizacji. Aby skonfigurować grupę synchronizacji, zobacz [Konfigurowanie grupy synchronizacji](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

Po wykonaniu powyższych kroków istnieje grupa synchronizacji zawierająca Azure SQL Database i SQL Database wystąpienie krawędzi.

Aby uzyskać więcej informacji na temat SQL Data Sync, zapoznaj się z następującymi artykułami:

* [Agent synchronizacji danych dla platformy Azure SQL Data Sync](../sql-database/sql-database-data-sync-agent.md)

* [Najlepsze rozwiązania](../sql-database/sql-database-best-practices-data-sync.md) i [Rozwiązywanie problemów z usługą Azure SQL Data Sync](../sql-database/sql-database-troubleshoot-data-sync.md)

* [Monitorowanie SQL Data Sync przy użyciu dzienników Azure Monitor](../sql-database/sql-database-sync-monitor-oms.md)

* [Aktualizowanie schematu synchronizacji przy użyciu języka Transact-SQL](../sql-database/sql-database-update-sync-schema.md) lub [programu PowerShell](../sql-database/scripts/sql-database-sync-update-schema.md)

## <a name="next-steps"></a>Następne kroki

* [Synchronizuj między Azure SQL Database i Azure SQL Database Edge przy użyciu programu PowerShell](../sql-database/scripts/sql-database-sync-data-between-azure-onprem.md). W samouczku Zastąp szczegóły bazy danych *OnPremiseServer* informacjami o Azure SQL Database Edge.
