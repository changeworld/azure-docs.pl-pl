---
title: Tworzenie i zarządzanie nimi odczytu replik w usłudze Azure Database for MySQL
description: W tym artykule opisano sposób konfigurowania i zarządzania nią odczytu replik w usłudze Azure Database for MySQL za pomocą portalu.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: b422718a1eaec483acdc2c8ab37442b9aea78aaa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65510769"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-portal"></a>Jak utworzyć i zarządzać nimi odczytu replik w usłudze Azure Database for MySQL za pomocą witryny Azure portal

W tym artykule dowiesz się, jak tworzyć i zarządzać nimi odczytu replik w usługa Azure Database for MySQL za pomocą witryny Azure portal.

> [!IMPORTANT]
> Można utworzyć odczytu repliki, w tym samym regionie co serwer główny lub w innym regionie platformy Azure wybranym. Replikacja w wielu regionach jest obecnie w publicznej wersji zapoznawczej.

## <a name="prerequisites"></a>Wymagania wstępne

- [— Azure Database for MySQL server](quickstart-create-mysql-server-database-using-azure-portal.md) który będzie używany jako serwer główny.

> [!IMPORTANT]
> Odczytu replik funkcja jest dostępna tylko dla usługi Azure Database dla serwerów MySQL w warstw cenowych ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci. Upewnij się, że serwer główny znajduje się w jednej z tych warstw cenowych.

## <a name="create-a-read-replica"></a>Tworzenie repliki do odczytu

Serwer odczytu repliki można utworzyć wykonując następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).

2. Wybierz istniejącą bazę danych Azure dla serwera MySQL, którego chcesz używać jako wzorca. Ta akcja powoduje otwarcie **Przegląd** strony.

3. Wybierz **replikacji** z menu, w obszarze **ustawienia**.

4. Wybierz **dodać repliki**.

   ![Azure Database for MySQL — replikacji](./media/howto-read-replica-portal/add-replica.png)

5. Wprowadź nazwę dla serwera repliki.

    ![Azure Database for MySQL — nazwa repliki](./media/howto-read-replica-portal/replica-name.png)

6. Wybierz lokalizację dla serwera repliki. W dowolnym regionie systemu Azure, można utworzyć repliki. Domyślna lokalizacja jest taka sama jak serwer główny

    ![Azure Database for MySQL — lokalizacji repliki](./media/howto-read-replica-portal/replica-location.png)

7. Wybierz **OK** o potwierdzenie utworzenia repliki.

> [!NOTE]
> Repliki do odczytu są tworzone przy użyciu tej samej konfiguracji serwera jako wzorzec. Konfiguracja serwera repliki można zmienić po jego utworzeniu. Zaleca się, że konfiguracja serwera repliki należy przechowywać w większa lub równa wartości niż główny, aby upewnić się, że replika jest w stanie na bieżąco ze wzorcem.

Po utworzeniu serwera repliki mogą być wyświetlane z **replikacji** bloku.

   ![Azure Database for MySQL — listy replik](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Zatrzymywanie replikacji na serwer repliki

> [!IMPORTANT]
> Zatrzymywanie replikacji na serwerze jest nieodwracalne. Po replikacji została zatrzymana między głównego i repliki, nie można cofnąć. Serwer repliki następnie staje się serwerem autonomicznym i obsługuje teraz zarówno odczytu i zapisu. Ten serwer nie wprowadzać ponownie do repliki.

Zatrzymania replikacji między główny i serwer repliki w witrynie Azure portal, wykonaj następujące kroki:

1. W witrynie Azure portal wybierz swoje głównego serwera Azure Database for MySQL. 

2. Wybierz **replikacji** z menu, w obszarze **ustawienia**.

3. Wybierz serwer repliki, który chcesz zatrzymać replikację.

   ![Azure Database for MySQL — zatrzymywanie replikacji wybierania serwera](./media/howto-read-replica-portal/stop-replication-select.png)

4. Wybierz **zatrzymanie replikacji**.

   ![Azure Database for MySQL — zatrzymanie replikacji](./media/howto-read-replica-portal/stop-replication.png)

5. Upewnij się, aby zatrzymać replikację, klikając **OK**.

   ![Potwierdź zatrzymanie replikacji usługi Azure Database for MySQL —](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Usuwanie serwera repliki

Aby usunąć serwer repliki odczytu w witrynie Azure portal, użyj następujących kroków:

1. W witrynie Azure portal wybierz swoje głównego serwera Azure Database for MySQL.

2. Wybierz **replikacji** z menu, w obszarze **ustawienia**.

3. Wybierz serwer repliki, który chcesz usunąć.

   ![Usługa Azure Database for MySQL — Usuń replika wybierz serwer](./media/howto-read-replica-portal/delete-replica-select.png)

4. Wybierz **Usuń replikę**

   ![Azure Database for MySQL — Usuń replikę](./media/howto-read-replica-portal/delete-replica.png)

5. Wpisz nazwę repliki i kliknij przycisk **Usuń** o potwierdzenie usunięcia replik.  

   ![Upewnij się, usługa Azure Database for MySQL — Usuń replikę](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Usuń serwer główny

> [!IMPORTANT]
> Usuwanie serwera głównego zatrzymanie replikacji na wszystkich serwerach repliki i usuwa samego serwera głównego. Serwer funkcji replica stają się autonomicznymi serwerami, które obsługują teraz zarówno odczytu i zapisu.

Aby usunąć serwer główny w witrynie Azure portal, wykonaj następujące kroki:

1. W witrynie Azure portal wybierz swoje głównego serwera Azure Database for MySQL.

2. Z **Przegląd**, wybierz opcję **Usuń**.

   ![Azure Database for MySQL — wzorzec usuwania](./media/howto-read-replica-portal/delete-master-overview.png)

3. Wpisz nazwę serwera głównego, a następnie kliknij przycisk **Usuń** o potwierdzenie usunięcia serwera głównego.  

   ![Azure Database for MySQL — wzorzec usuwania](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Monitorowanie replikacji

1. W [witryny Azure portal](https://portal.azure.com/), wybierz replikę bazy danych Azure Database dla serwera MySQL, którą chcesz monitorować.

2. W obszarze **monitorowanie** części paska bocznego wybierz **metryki**:

3. Wybierz **opóźnienie replikacji w ciągu kilku sekund** z listy rozwijanej dostępnych metryk.

   ![Wybierz opóźnienie replikacji](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Wybierz zakres czasu, który chcesz wyświetlić. Na poniższej ilustracji wybiera zakres czasu 30 minut.

   ![Wybierz zakres czasu](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Wyświetl opóźnienie replikacji dla wybranego zakresu czasu. Poniższy obraz przedstawia ostatnich 30 minut.

   ![Wybierz zakres czasu](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [odczytu replik](concepts-read-replicas.md)