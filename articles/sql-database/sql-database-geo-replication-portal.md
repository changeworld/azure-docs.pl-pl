---
title: 'Witryna Azure portal: replikacji geograficznej bazy danych SQL Database | Dokumentacja firmy Microsoft'
description: Konfigurowanie replikacji geograficznej usługi Azure SQL Database w witrynie Azure portal i zainicjuj tryb failover
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 8faf6713a5fd8287b5f9e30976e80172c2c42f05
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2018
ms.locfileid: "45631297"
---
# <a name="configure-active-geo-replication-for-azure-sql-database-in-the-azure-portal-and-initiate-failover"></a>Konfigurowanie aktywnej replikacji geograficznej dla usługi Azure SQL Database w witrynie Azure portal i zainicjuj tryb failover

W tym artykule dowiesz się, jak skonfigurować aktywną replikację geograficzną dla bazy danych SQL w [witryny Azure portal](http://portal.azure.com) i zainicjować trybu failover.

Aby zainicjować trybu failover w witrynie Azure portal, zobacz [inicjowanie planowanego lub nieplanowanego trybu failover na potrzeby usługi Azure SQL Database w witrynie Azure portal](sql-database-geo-replication-portal.md).

Aby skonfigurować aktywną replikację geograficzną za pomocą witryny Azure portal, potrzebne są następujące zasoby:

* Azure SQL database: podstawowej bazy danych, które mają być replikowane w innym regionie geograficznym.

> [!Note]
Aktywna replikacja geograficzna musi być między bazami danych w tej samej subskrypcji.

## <a name="add-a-secondary-database"></a>Dodawanie dodatkowej bazy danych
Poniższe kroki umożliwiają utworzenie nowej pomocniczej bazy danych w partnerstwie replikacji geograficznej.  

Aby dodać pomocniczą bazę danych, musi być właścicielem lub współwłaścicielem.

Pomocniczej bazy danych ma taką samą nazwę jak podstawowa baza danych i jest domyślnie taki sam poziom usługi. Pomocniczej bazy danych może być pojedynczą bazę danych lub bazę danych w puli elastycznej. Aby uzyskać więcej informacji, zobacz [modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md) i [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md).
Po pomocnicza jest tworzony i zasilany, rozpoczyna się danych replikowanych z podstawowej bazy danych do nowej pomocniczej bazy danych.

> [!NOTE]
> Jeśli baza danych partnera już istnieje (na przykład w wyniku zakończenia poprzedniej relacji replikacji geograficznej) polecenie kończy się niepowodzeniem.
> 

1. W [witryny Azure portal](http://portal.azure.com), przejdź do bazy danych, która ma zostać skonfigurowane dla replikacji geograficznej.
2. Na stronie bazy danych SQL zaznaczyć **geografickou replikaci**, a następnie wybierz region, aby utworzyć pomocniczej bazy danych. Można wybrać dowolny region innych niż region podstawowy bazę danych, ale zalecamy [sparowanym regionie](../best-practices-availability-paired-regions.md).
   
    ![Konfigurowanie replikacji geograficznej](./media/sql-database-geo-replication-portal/configure-geo-replication.png)
3. Wybierz lub skonfiguruj serwer i warstwę cenową dla pomocniczej bazy danych.
   
    ![Konfigurowanie pomocniczy](./media/sql-database-geo-replication-portal/create-secondary.png)
4. Opcjonalnie można dodać pomocniczą bazę danych do puli elastycznej. Aby utworzyć pomocniczej bazy danych w puli, kliknij przycisk **puli elastycznej** i wybierz pulę na serwerze docelowym. Pula musi już istnieć na serwerze docelowym. Ten przepływ pracy nie powoduje utworzenia puli.
5. Kliknij przycisk **Utwórz** można dodać pomocniczy.
6. Pomocniczej bazy danych jest tworzony i rozpocznie się proces rozmieszczania.
   
    ![Konfigurowanie pomocniczy](./media/sql-database-geo-replication-portal/seeding0.png)
7. Po zakończeniu procesu rozmieszczania pomocniczej bazy danych wyświetla jego stan.
   
    ![Wstępne wypełnianie ukończone](./media/sql-database-geo-replication-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Zainicjuj tryb failover

Pomocniczej bazy danych można dostosować do roli podstawowej.  

1. W [witryny Azure portal](http://portal.azure.com), przejdź do podstawowej bazy danych w ramach partnerstwa replikacji geograficznej.
2. W bloku bazy danych SQL zaznaczyć **wszystkie ustawienia** > **geografickou replikaci**.
3. W **pomocnicze bazy danych** , wybierz bazę danych do nowej podstawowej, a następnie kliknij pozycję na liście **trybu Failover**.
   
    ![Tryb failover](./media/sql-database-geo-replication-failover-portal/secondaries.png)
4. Kliknij przycisk **tak** do rozpoczęcia pracy w trybie failover.

Polecenie natychmiast zmienia pomocniczej bazy danych do roli podstawowej. 

Brak krótki okres, podczas którego obu bazach danych są niedostępne (rzędu kilku 0 do 25 sekund), podczas przełączania ról. Jeśli podstawowa baza danych ma wiele pomocniczych baz danych, to polecenie automatycznie skonfiguruje innych serwerów pomocniczych, aby nawiązać połączenie z nową podstawową. Cała operacja powinna zająć mniej niż minutę w normalnych warunkach. 

> [!NOTE]
> To polecenie jest przeznaczona do szybkiego odzyskiwania bazy danych, w przypadku awarii. Wyzwala trybu failover bez synchronizacji danych (wymuszony trybu failover).  Jeśli podstawowy jest w trybie online i zatwierdzania transakcji, gdy polecenie jest utrata danych mogą wystąpić. 
> 
> 

## <a name="remove-secondary-database"></a>Usuń pomocniczą bazę danych
Ta operacja trwale przerywa replikację do dodatkowej bazy danych i zmiany roli pomocniczej zwykłej bazy danych odczytu i zapisu. Łączność z dodatkowej bazy danych jest uszkodzona, polecenie zakończy się pomyślnie, ale ma dodatkowych, staje się odczytu / zapisu, dopóki łączność zostanie przywrócona.  

1. W [witryny Azure portal](http://portal.azure.com), przejdź do podstawowej bazy danych w ramach partnerstwa replikacji geograficznej.
2. Na stronie bazy danych SQL zaznaczyć **geografickou replikaci**.
3. W **pomocnicze bazy danych** , wybierz bazę danych, którą chcesz usunąć z powiązanie replikacji geograficznej na liście.
4. Kliknij przycisk **zatrzymać replikację**.
   
    ![Usuń pomocniczy](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. Zostanie otwarte okno potwierdzenia. Kliknij przycisk **tak** usunąć bazę danych z powiązanie replikacji geograficznej. (Ustawić go do odczytu i zapisu bazy danych nie jest częścią replikacji.)

## <a name="next-steps"></a>Kolejne kroki
* Aby dowiedzieć się więcej na temat aktywnej replikacji geograficznej, zobacz [aktywnej replikacji geograficznej](sql-database-geo-replication-overview.md).
* Omówienie ciągłości działania i scenariuszach można znaleźć [omówienie ciągłości działania](sql-database-business-continuity.md).

