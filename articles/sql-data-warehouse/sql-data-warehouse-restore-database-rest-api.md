---
title: Przywróć magazyn danych Azure SQL - interfejsu API REST | Dokumentacja firmy Microsoft
description: Przywrócenie magazynu danych SQL Azure przy użyciu interfejsów API REST.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 2e1874fdf7c11d98d369072739c5937caffe6e96
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="restore-an-azure-sql-data-warehouse-with-rest-apis"></a>Przywróć magazyn danych Azure SQL z interfejsów API REST
Przywrócenie magazynu danych SQL Azure przy użyciu interfejsów API REST.

## <a name="before-you-begin"></a>Przed rozpoczęciem
**Sprawdź, czy pojemność jednostek dtu w warstwie.** Każdy magazyn danych SQL jest obsługiwana przez serwer logiczny SQL (np. myserver.database.windows.net), który ma wartość domyślną [limit przydziału jednostek DTU](../sql-database/sql-database-what-is-a-dtu.md).  Zanim będzie można przywrócić magazyn danych SQL, upewnij się, że program SQL server ma wystarczającą ilość pozostałych limit przydziału jednostek DTU dla przywracanej bazy danych. Aby zażądać więcej jednostek dtu w warstwie, możesz [Utwórz bilet pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-an-active-or-paused-data-warehouse"></a>Przywróć hurtowni danych aktywnych lub wstrzymana
Aby przywrócić magazynu danych:

1. Pobierz listę punktów przywracania bazy danych przy użyciu operacji punkty przywracania bazy danych Get.
2. Rozpocznij przywracania przy użyciu [żądanie przywracania bazy danych utwórz](https://msdn.microsoft.com/library/azure/dn509571.aspx) operacji.
3. Śledź stan przywracania przy użyciu [operacji stan bazy danych](https://msdn.microsoft.com/library/azure/dn720371.aspx) operacji.

> [!NOTE]
> Po ukończeniu przywracania, można skonfigurować Magazyn odzyskane dane, postępując [konfiguracji bazy danych po odzyskaniu](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).
> 
> 

## <a name="restore-a-deleted-data-warehouse"></a>Przywracanie usuniętych danych magazynu
Aby przywrócić usunięte dane magazynu:

1. Listę wszystkich magazynów umożliwiająca przywrócenie usuniętych danych przy użyciu [listy dostępnych porzucić bazy danych](https://msdn.microsoft.com/library/azure/dn509562.aspx) operacji.
2. Szczegółowe informacje dla magazynu danych usuniętych, który chcesz przywrócić za pomocą operacji [Get dostępnych usuwanej bazy danych] [Get dostępnych usuwanej bazy danych].
3. Rozpocznij przywracania przy użyciu [żądanie przywracania bazy danych utwórz](https://msdn.microsoft.com/library/azure/dn509571.aspx) operacji.
4. Śledź stan przywracania przy użyciu [operacji stan bazy danych](https://msdn.microsoft.com/library/azure/dn720371.aspx) operacji.

> [!NOTE]
> Aby skonfigurować Magazyn danych po zakończeniu przywracania, zobacz [konfiguracji bazy danych po odzyskaniu](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).
> 
> 

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej o funkcjach ciągłości biznesowej wersji bazy danych SQL Azure, przeczytaj [omówienie ciągłości działalności biznesowej usługi Azure SQL Database](../sql-database/sql-database-business-continuity.md).
