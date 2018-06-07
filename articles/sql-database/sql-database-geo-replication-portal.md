---
title: 'Portalu Azure: replikacja geograficzna bazy danych SQL | Dokumentacja firmy Microsoft'
description: Skonfiguruj — replikacja geograficzna bazy danych SQL Azure w portalu Azure i zainicjuj tryb failover
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: e6c699fdd57eecc31c51b26ccb7c06c6edbcfd03
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646902"
---
# <a name="configure-active-geo-replication-for-azure-sql-database-in-the-azure-portal-and-initiate-failover"></a>Skonfiguruj aktywna replikacja geograficzna bazy danych SQL Azure w portalu Azure i zainicjuj tryb failover

W tym artykule przedstawiono sposób konfigurowania bazy danych SQL w aktywna replikacja geograficzna [portalu Azure](http://portal.azure.com) i rozpoczęcie pracy awaryjnej.

Aby zainicjować trybu failover przy użyciu portalu Azure, zobacz [zainicjowanie planowanego lub nieplanowanego trybu failover dla bazy danych SQL Azure przy użyciu portalu Azure](sql-database-geo-replication-portal.md).

Aktywna replikacja geograficzna należy skonfigurować przy użyciu portalu Azure, potrzebne są następujące zasoby:

* Baza danych Azure SQL: podstawowej bazy danych, które mają być replikowane w innym regionie geograficznym.

> [!Note]
Aktywna replikacja geograficzna musi należeć do zakresu od bazy danych w tej samej subskrypcji.

## <a name="add-a-secondary-database"></a>Dodać pomocniczą bazę danych
Następujące kroki tworzenia nowego pomocniczej bazy danych współpracują — replikacja geograficzna.  

Aby dodać pomocniczą bazę danych, musi być właścicielem subskrypcji lub współwłaściciel.

Dodatkowej bazy danych ma taką samą nazwę co podstawowa baza danych i ma domyślnie na tym samym poziomie usługi. Dodatkowej bazy danych może być pojedynczą bazę danych lub bazy danych w puli elastycznej. Aby uzyskać więcej informacji, zobacz [na podstawie jednostek dtu w warstwie model kupna](sql-database-service-tiers-dtu.md) i [na podstawie vCore model kupna (wersja zapoznawcza)](sql-database-service-tiers-vcore.md).
Po utworzeniu i rozpoczęta pomocniczej, danych rozpoczyna replikację z podstawowej bazy danych do nowej dodatkowej bazy danych.

> [!NOTE]
> Jeśli baza danych partnera już istnieje (na przykład w wyniku zakończenia dotychczasowej relacji replikacji geograficznej) polecenie kończy się niepowodzeniem.
> 

1. W [portalu Azure](http://portal.azure.com), przejdź do bazy danych, który ma zostać skonfigurowany do replikacji geograficznej.
2. Na stronie bazy danych SQL, wybierz **— replikacja geograficzna**, a następnie wybierz region do tworzenia pomocniczej bazy danych. Można wybrać dowolny region inny niż region podstawowy bazę danych, ale zaleca się [sparowanego region](../best-practices-availability-paired-regions.md).
   
    ![Konfigurowanie replikacji geograficznej](./media/sql-database-geo-replication-portal/configure-geo-replication.png)
3. Wybierz i skonfiguruj serwer i warstwę cenową dla pomocniczej bazy danych.
   
    ![Konfigurowanie dodatkowej](./media/sql-database-geo-replication-portal/create-secondary.png)
4. Opcjonalnie można dodać pomocniczą bazę danych z puli elastycznej. Aby utworzyć dodatkowej bazy danych w puli, kliknij przycisk **puli elastycznej** i wybierz pulę na serwerze docelowym. Pula musi już istnieć na serwerze docelowym. Ten przepływ pracy nie powoduje utworzenia puli.
5. Kliknij przycisk **Utwórz** można dodać pomocniczy.
6. Utworzono dodatkowej bazy danych i rozpocznie się proces rozmieszczania.
   
    ![Konfigurowanie dodatkowej](./media/sql-database-geo-replication-portal/seeding0.png)
7. Po zakończeniu procesu rozmieszczania dodatkowej bazy danych wyświetla jego stan.
   
    ![Wstępne wypełnianie ukończone](./media/sql-database-geo-replication-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Zainicjuj tryb failover

Dodatkowej bazy danych mogą być przełączane do stają się serwerem podstawowym.  

1. W [portalu Azure](http://portal.azure.com), przejdź do podstawowej bazy danych — replikacja geograficzna wspólnie.
2. W bloku bazy danych SQL, wybierz **wszystkie ustawienia** > **— replikacja geograficzna**.
3. W **pomocniczych** listy, wybierz bazę danych, aby stać się nową podstawową, a następnie kliknij przycisk **pracy awaryjnej**.
   
    ![tryb failover](./media/sql-database-geo-replication-failover-portal/secondaries.png)
4. Kliknij przycisk **tak** do rozpoczęcia pracy awaryjnej.

Polecenie natychmiast zmienia dodatkowej bazy danych do roli podstawowego. 

Istnieje krótki okres, podczas którego obie bazy danych są niedostępne (rzędu 0 do 25 sekund), podczas przełączania ról. Jeśli podstawowa baza danych ma wiele baz danych w dodatkowej, polecenie automatycznie skonfiguruje replik pomocniczych do nawiązania połączenia nową podstawową. Cała operacja powinny zająć mniej niż minutę w normalnych okolicznościach. 

> [!NOTE]
> To polecenie jest przeznaczona dla Szybkie odzyskiwanie bazy danych w przypadku awarii. Wyzwala trybu failover bez synchronizacji danych (wymuszone trybu failover).  Jeśli podstawowy jest w trybie online i może wystąpić, zatwierdzania transakcji, gdy wydano polecenie utratę danych. 
> 
> 

## <a name="remove-secondary-database"></a>Usuń z dodatkowej bazy danych
Ta operacja trwale kończy replikację do dodatkowej bazy danych i zmiany roli pomocniczej zwykłej odczytu i zapisu bazy danych. Łączność z dodatkowej bazy danych jest uszkodzona, polecenie powiedzie się, ale ma dodatkowej, staje się odczytu i zapisu, dopóki łączność zostanie przywrócona.  

1. W [portalu Azure](http://portal.azure.com), przejdź do podstawowej bazy danych — replikacja geograficzna wspólnie.
2. Na stronie bazy danych SQL, wybierz **— replikacja geograficzna**.
3. W **pomocniczych** listy, wybierz bazę danych, aby usunąć z powiązania — replikacja geograficzna.
4. Kliknij przycisk **Zatrzymaj replikację,**.
   
    ![Usuń pomocniczej](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. Zostanie wyświetlone okno potwierdzenia. Kliknij przycisk **tak** usunąć bazę danych z powiązania — replikacja geograficzna. (Ustawia go do odczytu i zapisu bazy danych nie jest częścią replikacji).

## <a name="next-steps"></a>Kolejne kroki
* Aby dowiedzieć się więcej na temat aktywna replikacja geograficzna, zobacz [aktywna replikacja geograficzna](sql-database-geo-replication-overview.md).
* Omówienie ciągłości działalności biznesowej i scenariuszy, zobacz [omówienie ciągłości działalności biznesowej](sql-database-business-continuity.md).

