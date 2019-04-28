---
title: 'Witryna Azure Portal: Replikacja geograficzna bazy danych SQL Database | Dokumentacja firmy Microsoft'
description: Konfigurowanie replikacji geograficznej dla pojedynczej lub puli bazy danych w usłudze Azure SQL Database przy użyciu witryny Azure portal i zainicjuj tryb failover
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 02/13/2019
ms.openlocfilehash: 8bada96c648881a9943176c45115627a829fcc58
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60864136"
---
# <a name="configure-active-geo-replication-for-azure-sql-database-in-the-azure-portal-and-initiate-failover"></a>Konfigurowanie aktywnej replikacji geograficznej dla usługi Azure SQL Database w witrynie Azure portal i zainicjuj tryb failover

W tym artykule dowiesz się, jak skonfigurować [aktywnej replikacji geograficznej dla pojedynczej i puli baz danych](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities) w usłudze Azure SQL Database przy użyciu [witryny Azure portal](https://portal.azure.com) i zainicjować trybu failover.

Aby uzyskać informacji o grupach automatyczny tryb failover z jednym i puli baz danych, zobacz [najlepsze rozwiązania dotyczące korzystania z grup trybu failover z jednym i puli baz danych](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). Aby uzyskać informacji o grupach automatyczny tryb failover za pomocą wystąpienia zarządzanego (wersja zapoznawcza), zobacz [najlepsze rozwiązania dotyczące korzystania z grup trybu failover przy użyciu wystąpienia zarządzane przez usługę](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-managed-instances).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować aktywną replikację geograficzną za pomocą witryny Azure portal, potrzebne są następujące zasoby:

* Azure SQL database: Podstawowej bazy danych, którą chcesz zreplikować do innego regionu geograficznego.

> [!Note]
> Przy użyciu witryny Azure portal, można tylko utworzyć pomocniczą bazę danych w ramach tej samej subskrypcji, jako podstawowy. Jeśli pomocnicza baza danych musi znajdować się w innej subskrypcji, użyj [tworzenie bazy danych interfejsu API REST](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) lub [ALTER DATABASE języka Transact-SQL API](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql).

## <a name="add-a-secondary-database"></a>Dodawanie dodatkowej bazy danych

Poniższe kroki umożliwiają utworzenie nowej pomocniczej bazy danych w partnerstwie replikacji geograficznej.  

Aby dodać pomocniczą bazę danych, musi być właścicielem lub współwłaścicielem.

Pomocniczej bazy danych ma taką samą nazwę jak podstawowa baza danych i domyślnie, ma tę samą usługę warstwy i obliczenia rozmiaru. Pomocniczej bazy danych może być pojedynczą bazę danych lub bazy danych w puli. Aby uzyskać więcej informacji, zobacz [modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md) i [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md).
Po pomocnicza jest tworzony i zasilany, rozpoczyna się danych replikowanych z podstawowej bazy danych do nowej pomocniczej bazy danych.

> [!NOTE]
> Jeśli baza danych partnera już istnieje (na przykład w wyniku zakończenia poprzedniej relacji replikacji geograficznej) polecenie kończy się niepowodzeniem.

1. W [witryny Azure portal](https://portal.azure.com), przejdź do bazy danych, która ma zostać skonfigurowane dla replikacji geograficznej.
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

1. W [witryny Azure portal](https://portal.azure.com), przejdź do podstawowej bazy danych w ramach partnerstwa replikacji geograficznej.
2. W bloku bazy danych SQL zaznaczyć **wszystkie ustawienia** > **geografickou replikaci**.
3. W **pomocnicze bazy danych** , wybierz bazę danych do nowej podstawowej, a następnie kliknij pozycję na liście **trybu Failover**.

    ![tryb failover](./media/sql-database-geo-replication-failover-portal/secondaries.png)
4. Kliknij przycisk **tak** do rozpoczęcia pracy w trybie failover.

Polecenie natychmiast zmienia pomocniczej bazy danych do roli podstawowej. Zwykle ten proces powinno zakończyć się w ciągu 30 sekund lub mniej.

Brak krótki okres, podczas którego obu bazach danych są niedostępne (rzędu kilku 0 do 25 sekund), podczas przełączania ról. Jeśli podstawowa baza danych ma wiele pomocniczych baz danych, to polecenie automatycznie skonfiguruje innych serwerów pomocniczych, aby nawiązać połączenie z nową podstawową. Cała operacja powinna zająć mniej niż minutę w normalnych warunkach.

> [!NOTE]
> To polecenie jest przeznaczona do szybkiego odzyskiwania bazy danych, w przypadku awarii. Wyzwala trybu failover bez synchronizacji danych (wymuszony trybu failover).  Jeśli podstawowy jest w trybie online i zatwierdzania transakcji, gdy polecenie jest utrata danych mogą wystąpić.

## <a name="remove-secondary-database"></a>Usuń pomocniczą bazę danych

Ta operacja trwale przerywa replikację do dodatkowej bazy danych i zmiany roli pomocniczej zwykłej bazy danych odczytu i zapisu. Łączność z dodatkowej bazy danych jest uszkodzona, polecenie zakończy się pomyślnie, ale ma dodatkowych, staje się odczytu / zapisu, dopóki łączność zostanie przywrócona.  

1. W [witryny Azure portal](https://portal.azure.com), przejdź do podstawowej bazy danych w ramach partnerstwa replikacji geograficznej.
2. Na stronie bazy danych SQL zaznaczyć **geografickou replikaci**.
3. W **pomocnicze bazy danych** , wybierz bazę danych, którą chcesz usunąć z powiązanie replikacji geograficznej na liście.
4. Kliknij przycisk **zatrzymać replikację**.

    ![Usuń pomocniczy](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. Zostanie otwarte okno potwierdzenia. Kliknij przycisk **tak** usunąć bazę danych z powiązanie replikacji geograficznej. (Ustawić go do odczytu i zapisu bazy danych nie jest częścią replikacji.)

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat aktywnej replikacji geograficznej, zobacz [aktywnej replikacji geograficznej](sql-database-active-geo-replication.md).
* Aby dowiedzieć się więcej o grupach automatyczny tryb failover, zobacz [grup automatyczny tryb failover](sql-database-auto-failover-group.md)
* Omówienie ciągłości działania i scenariuszach można znaleźć [omówienie ciągłości działania](sql-database-business-continuity.md).
