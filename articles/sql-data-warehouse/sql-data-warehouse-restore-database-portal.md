---
title: Przywracanie usługi Azure SQL Data Warehouse (Azure portal) | Dokumentacja firmy Microsoft
description: Azure portalu zadania przywracania magazyn danych SQL Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 6268f500d44bf7ceaaccbcc8e2c2134b42032197
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31526876"
---
# <a name="restore-azure-sql-data-warehouse-portal"></a>Przywracanie usługi Azure SQL Data Warehouse (portal)
> [!div class="op_single_selector"]
> * [Omówienie][Overview]
> * [Portal][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
>
>
W tym artykule dowiesz się, jak przywrócić Azure SQL Data Warehouse przy użyciu portalu Azure.

## <a name="before-you-begin"></a>Przed rozpoczęciem
**Sprawdź, czy pojemność jednostek dtu w warstwie.** Każde wystąpienie usługi SQL Data Warehouse jest hostowana przez serwer SQL (na przykład myserver.database.windows.net), który ma domyślnego przydziału (bazy danych DTU) jednostki przepływności danych. Zanim będzie można przywrócić SQL Data Warehouse, sprawdź, czy program SQL server ma wystarczająco pozostałych limit przydziału jednostek DTU w przypadku przywracania bazy danych. Aby dowiedzieć się jak obliczyć limit przydziału jednostek DTU lub zażądać więcej jednostek Dtu, zobacz [żądanie zmiany limitu przydziału jednostek dtu w warstwie][Request a DTU quota change].

## <a name="restore-an-active-or-paused-database"></a>Przywróć bazę danych aktywnej lub wstrzymana
Przywracanie bazy danych:

1. Zaloguj się w witrynie [Azure Portal][Azure portal].
2. W okienku po lewej stronie wybierz **Przeglądaj**, a następnie wybierz **serwerów SQL**.

    ![Wybierz opcję Przeglądaj > serwery SQL](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. Znajdź serwer, a następnie wybierz go.

    ![Wybierz serwer](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)
4. Znajdź wystąpienie programu SQL Data Warehouse, który chcesz przywrócić z, a następnie wybierz go.

    ![Wybierz wystąpienie programu SQL magazynu danych do przywrócenia](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. W górnej części bloku hurtowni danych, wybierz **przywrócić**.

    ![Wybierz przywracania](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)
6. Określ nowy **Nazwa bazy danych**.
7. Wybierz najnowszą **punkt przywracania**.

   Upewnij się, że została wybrana do ostatniego punktu przywracania. Ponieważ punkty przywracania są wyświetlane w uniwersalnego czasu koordynowanego (UTC), opcją domyślną może nie być do ostatniego punktu przywracania.

      ![Wybierz punkt przywracania](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)
8. Kliknij przycisk **OK**.
9. Rozpocznie się proces przywracania bazy danych, a następnie można użyć **powiadomienia** do monitorowania procesu.

> [!NOTE]
> Po zakończeniu przywracania, można skonfigurować odzyskanej bazy danych, wykonując [konfiguracji bazy danych po odzyskaniu][Configure your database after recovery].
>
>

## <a name="restore-a-deleted-database"></a>Przywracanie usuniętej bazy danych
Aby przywrócić usunięte bazy danych:

1. Zaloguj się w witrynie [Azure Portal][Azure portal].
2. W okienku po lewej stronie wybierz **Przeglądaj**, a następnie wybierz **serwerów SQL**.

    ![Wybierz opcję Przeglądaj > serwery SQL](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. Znajdź serwer, a następnie wybierz go.

    ![Wybierz serwer](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)
4. Przewiń w dół do **operacji** sekcji w bloku serwera.
5. Wybierz **usunięte bazy danych** kafelka.

    ![Wybierz Kafelek usunięte bazy danych](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)
6. Wybierz usuniętej bazy danych, który chcesz przywrócić.

    ![Wybierz bazę danych do przywrócenia](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)
7. Określ nowy **Nazwa bazy danych**.

    ![Dodaj nazwę bazy danych](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
8. Kliknij przycisk **OK**.
9. Rozpocznie się proces przywracania bazy danych, a następnie można użyć **powiadomienia** do monitorowania procesu.

> [!NOTE]
> Aby skonfigurować bazę danych, po zakończeniu przywracania, zobacz [konfiguracji bazy danych po odzyskaniu][Configure your database after recovery].
>
>

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej o funkcjach ciągłości biznesowej wersji bazy danych SQL Azure, przeczytaj [omówienie ciągłości działalności biznesowej usługi Azure SQL Database][Azure SQL Database business continuity overview].

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md

<!--MSDN references-->

<!--Blog references-->

<!--Other Web references-->
[Azure portal]: https://portal.azure.com/
