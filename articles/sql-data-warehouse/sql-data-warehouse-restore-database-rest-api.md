---
title: Przywracanie usługi Azure SQL Data Warehouse — interfejs API REST | Dokumentacja firmy Microsoft
description: Przywracanie usługi Azure SQL Data Warehouse przy użyciu interfejsów API REST.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: daf013472e5fa533912920e4c14a552905b5d333
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60935691"
---
# <a name="restore-an-azure-sql-data-warehouse-with-rest-apis"></a>Przywracanie usługi Azure SQL Data Warehouse przy użyciu interfejsów API REST
Przywracanie usługi Azure SQL Data Warehouse przy użyciu interfejsów API REST.

## <a name="before-you-begin"></a>Przed rozpoczęciem
**Sprawdź wydajność jednostek DTU.** Każda usługa SQL Data Warehouse jest hostowana przez serwer logiczny SQL (np. myserver.database.windows.net) domyślna [limit przydziału jednostek DTU](../sql-database/sql-database-what-is-a-dtu.md).  Przed przywróceniem usłudze SQL Data Warehouse, upewnij się, że program SQL server ma wystarczającą ilość pozostały limit przydziału jednostek DTU dla przywracana baza danych. Aby zażądać więcej jednostek DTU, możesz [Utwórz bilet pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-an-active-or-paused-data-warehouse"></a>Przywracanie magazynu danych active lub wstrzymana
Aby przywrócić magazyn danych:

1. Pobierz listę punktów przywracania bazy danych przy użyciu operacji Pobierz punkty przywracania bazy danych.
2. Rozpocznij przywracania przy użyciu [żądania przywracania bazy danych utwórz](https://msdn.microsoft.com/library/azure/dn509571.aspx) operacji.
3. Śledzić stan operacji przywracania za pomocą [stan operacji bazy danych](https://msdn.microsoft.com/library/azure/dn720371.aspx) operacji.

> [!NOTE]
> Po zakończeniu przywracania można skonfigurować Magazyn danych danymi odzyskanymi, postępując zgodnie z [konfiguracji bazy danych po odzyskaniu](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).
> 
> 

## <a name="restore-a-deleted-data-warehouse"></a>Przywracanie usuniętego magazynu danych
Aby przywrócić usunięty magazyn danych:

1. Wyświetlić listę wszystkich magazynów umożliwiająca przywrócenie usuniętych danych przy użyciu [lista umożliwiająca przywrócenie porzucić bazy danych](https://msdn.microsoft.com/library/azure/dn509562.aspx) operacji.
2. Uzyskaj szczegółowe informacje dla magazynu usunięte dane, które chcesz przywrócić, używając operacji [Get umożliwiająca przywrócenie usuwanej bazy danych] [Get umożliwiająca przywrócenie usuwanej bazy danych].
3. Rozpocznij przywracania przy użyciu [żądania przywracania bazy danych utwórz](https://msdn.microsoft.com/library/azure/dn509571.aspx) operacji.
4. Śledzić stan operacji przywracania za pomocą [stan operacji bazy danych](https://msdn.microsoft.com/library/azure/dn720371.aspx) operacji.

> [!NOTE]
> Aby skonfigurować Magazyn danych, po ukończeniu przywracania, zobacz [konfiguracji bazy danych po odzyskaniu](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).
> 
> 

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się o funkcje zachowywania ciągłości biznesowej, baz danych Azure SQL Database, należy przeczytać [omówienie ciągłości działania usługi Azure SQL Database](../sql-database/sql-database-business-continuity.md).
