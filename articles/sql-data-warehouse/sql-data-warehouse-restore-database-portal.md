---
title: Przywracanie usługi Azure SQL Data Warehouse (Azure portal) | Dokumentacja firmy Microsoft
description: Azure portal zadania przywracania usługi Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 2d59235b067d9571bc8b64c33799431be6489502
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61421391"
---
# <a name="restore-azure-sql-data-warehouse-portal"></a>Przywracanie usługi Azure SQL Data Warehouse (portal)
> [!div class="op_single_selector"]
> * [Przegląd][Overview]
> * [Portal][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 
> W tym artykule dowiesz się, jak przywrócić Azure SQL Data Warehouse przy użyciu witryny Azure portal.

## <a name="before-you-begin"></a>Przed rozpoczęciem
**Sprawdź wydajność jednostek DTU.** Każde wystąpienie usługi SQL Data Warehouse jest obsługiwana przez serwer SQL (na przykład myserver.database.windows.net), która ma domyślny przydział jednostek (DTU) przepływność danych. Przed przywróceniem SQL Data Warehouse, sprawdź, czy program SQL server ma wystarczająco pozostały limit przydziału jednostek DTU w przypadku przywracania bazy danych. Aby dowiedzieć się, jak obliczyć limit przydziału jednostek DTU lub zażądać więcej jednostek Dtu, zobacz [żądanie zmiany limitu przydziału jednostek DTU][Request a DTU quota change].

## <a name="restore-an-active-or-paused-database"></a>Przywracanie bazy danych programu active lub wstrzymana
Aby przywrócić bazę danych:

1. Zaloguj się w witrynie [Azure Portal][Azure portal].
2. W okienku po lewej stronie wybierz **Przeglądaj**, a następnie wybierz pozycję **serwerów SQL**.

    ![Wybierz pozycję Przeglądaj > serwery SQL](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. Znajdź serwer, a następnie wybierz ją.

    ![Wybierz swój serwer](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)
4. Znajdź wystąpienie usługi SQL Data Warehouse, który chcesz przywrócić z, a następnie wybierz ją.

    ![Wybierz wystąpienie usługi SQL Data Warehouse do przywrócenia](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. W górnej części bloku magazynu danych wybierz **przywrócić**.

    ![Wybierz opcję przywracania](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)
6. Określ nową **Nazwa bazy danych**.
7. Wybierz najnowszy **punkt przywracania**.

   Upewnij się, że wybrano najnowszy punkt przywracania. Ponieważ punkty przywracania są wyświetlane w uniwersalnego czasu koordynowanego (UTC), to opcja domyślna może nie być najnowszy punkt przywracania.

      ![Wybierz punkt przywracania](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)
8. Kliknij przycisk **OK**.
9. Rozpocznie się proces przywracania bazy danych i umożliwia **powiadomienia** do monitorowania procesu.

> [!NOTE]
> Po zakończeniu przywracania odzyskanej bazy danych można skonfigurować, wykonując [konfiguracji bazy danych po odzyskaniu][Configure your database after recovery].
>
>

## <a name="restore-a-deleted-database"></a>Przywracanie usuniętej bazy danych
Aby przywrócić usuniętą bazę danych:

1. Zaloguj się w witrynie [Azure Portal][Azure portal].
2. W okienku po lewej stronie wybierz **Przeglądaj**, a następnie wybierz pozycję **serwerów SQL**.

    ![Wybierz pozycję Przeglądaj > serwery SQL](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. Znajdź serwer, a następnie wybierz ją.

    ![Wybierz swój serwer](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)
4. Przewiń w dół do **operacji** sekcji w bloku serwera.
5. Wybierz **usuniętych baz danych** kafelka.

    ![Wybierz Kafelek usuniętych baz danych](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)
6. Wybierz usuniętej bazy danych, który chcesz przywrócić.

    ![Wybierz bazę danych do przywrócenia](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)
7. Określ nową **Nazwa bazy danych**.

    ![Dodaj nazwę bazy danych](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
8. Kliknij przycisk **OK**.
9. Rozpocznie się proces przywracania bazy danych i umożliwia **powiadomienia** do monitorowania procesu.

> [!NOTE]
> Aby skonfigurować bazę danych, po zakończeniu przywracania, zobacz [konfiguracji bazy danych po odzyskaniu][Configure your database after recovery].
>
>

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się o funkcje zachowywania ciągłości biznesowej, baz danych Azure SQL Database, przeczytaj [omówienie ciągłości działania usługi Azure SQL Database][Azure SQL Database business continuity overview].

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
