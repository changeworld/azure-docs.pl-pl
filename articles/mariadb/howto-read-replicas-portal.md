---
title: Tworzenie replik odczytu i zarządzanie nimi w Azure Database for MariaDB
description: W tym artykule opisano sposób konfigurowania replik odczytu i zarządzania nimi w Azure Database for MariaDB przy użyciu portalu
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: cceb1814089436efe4d4f9352f40f24d6eae790d
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123596"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-portal"></a>Tworzenie replik odczytu i zarządzanie nimi w Azure Database for MariaDB przy użyciu Azure Portal

W tym artykule przedstawiono sposób tworzenia replik odczytu i zarządzania nimi w usłudze Azure Database for MariaDB przy użyciu Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

- [Serwer Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md) , który będzie używany jako serwer główny.

> [!IMPORTANT]
> Funkcja odczytu repliki jest dostępna tylko dla serwerów Azure Database for MariaDB w warstwach cenowych Ogólnego przeznaczenia lub zoptymalizowanych pod kątem pamięci. Upewnij się, że serwer główny znajduje się w jednej z tych warstw cenowych.

## <a name="create-a-read-replica"></a>Tworzenie repliki do odczytu

Serwer repliki odczytu można utworzyć, wykonując następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).

2. Wybierz istniejący serwer Azure Database for MariaDB, który ma być używany jako główny. Ta akcja powoduje otwarcie **Przegląd** strony.

3. Wybierz opcję **replikacja** z menu, w obszarze **Ustawienia**.

4. Wybierz pozycję **Dodaj**replikę.

   ![Azure Database for MariaDB — replikacja](./media/howto-read-replica-portal/add-replica.png)

5. Wprowadź nazwę serwera repliki.

    ![Azure Database for MariaDB — nazwa repliki](./media/howto-read-replica-portal/replica-name.png)

6. Wybierz lokalizację serwera repliki. Lokalizacja domyślna jest taka sama jak w przypadku serwera głównego.

    ![Azure Database for MariaDB — lokalizacja repliki](./media/howto-read-replica-portal/replica-location.png)

   > [!NOTE]
   > Replikacja między regionami jest w wersji zapoznawczej. Aby dowiedzieć się więcej na temat regionów, w których można utworzyć replikę, zapoznaj się z [artykułem dotyczącym pojęć dotyczących repliki](concepts-read-replicas.md). 

7. Wybierz **przycisk OK** , aby potwierdzić utworzenie repliki.

> [!NOTE]
> Repliki do odczytu są tworzone przy użyciu tej samej konfiguracji serwera jako wzorzec. Konfiguracja serwera repliki można zmienić po jego utworzeniu. Zaleca się, że konfiguracja serwera repliki należy przechowywać w większa lub równa wartości niż główny, aby upewnić się, że replika jest w stanie na bieżąco ze wzorcem.

Po utworzeniu serwera repliki można go wyświetlić w bloku **replikacja** .

   ![Repliki list Azure Database for MariaDB](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Zatrzymywanie replikacji na serwer repliki

> [!IMPORTANT]
> Zatrzymywanie replikacji na serwerze jest nieodwracalne. Po replikacji została zatrzymana między głównego i repliki, nie można cofnąć. Serwer repliki następnie staje się serwerem autonomicznym i obsługuje teraz zarówno odczytu i zapisu. Ten serwer nie wprowadzać ponownie do repliki.

Aby zatrzymać replikację między wzorcem i serwerem repliki z Azure Portal, wykonaj następujące czynności:

1. W Azure Portal wybierz serwer główny Azure Database for MariaDB. 

2. Wybierz opcję **replikacja** z menu, w obszarze **Ustawienia**.

3. Wybierz serwer repliki, dla którego ma zostać zatrzymana replikacja.

   ![Azure Database for MariaDB — zatrzymywanie replikacji wybierz serwer](./media/howto-read-replica-portal/stop-replication-select.png)

4. Wybierz pozycję **Zatrzymaj replikację**.

   ![Azure Database for MariaDB — zatrzymywanie replikacji](./media/howto-read-replica-portal/stop-replication.png)

5. Potwierdź, że chcesz zatrzymać replikację, klikając przycisk **OK**.

   ![Azure Database for MariaDB — potwierdzenie zatrzymywania replikacji](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Usuwanie serwera repliki

Aby usunąć serwer repliki odczytu z Azure Portal, wykonaj następujące czynności:

1. W Azure Portal wybierz serwer główny Azure Database for MariaDB.

2. Wybierz opcję **replikacja** z menu, w obszarze **Ustawienia**.

3. Wybierz serwer repliki, który chcesz usunąć.

   ![Azure Database for MariaDB — usuwanie repliki wybierz serwer](./media/howto-read-replica-portal/delete-replica-select.png)

4. Wybierz pozycję **Usuń** replikę

   ![Azure Database for MariaDB-Usuń replikę](./media/howto-read-replica-portal/delete-replica.png)

5. Wpisz nazwę repliki, a następnie kliknij przycisk **Usuń** , aby potwierdzić usunięcie repliki.  

   ![Potwierdzenie usunięcia repliki Azure Database for MariaDB](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Usuń serwer główny

> [!IMPORTANT]
> Usuwanie serwera głównego zatrzymanie replikacji na wszystkich serwerach repliki i usuwa samego serwera głównego. Serwer funkcji replica stają się autonomicznymi serwerami, które obsługują teraz zarówno odczytu i zapisu.

Aby usunąć serwer główny z Azure Portal, wykonaj następujące czynności:

1. W Azure Portal wybierz serwer główny Azure Database for MariaDB.

2. W obszarze **Przegląd**wybierz pozycję **Usuń**.

   ![Azure Database for MariaDB-Usuń wzorzec](./media/howto-read-replica-portal/delete-master-overview.png)

3. Wpisz nazwę serwera głównego, a następnie kliknij przycisk **Usuń** , aby potwierdzić usunięcie serwera głównego.  

   ![Azure Database for MariaDB-Usuń wzorzec](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Monitoruj replikację

1. W [Azure Portal](https://portal.azure.com/)wybierz serwer repliki Azure Database for MariaDB, który chcesz monitorować.

2. W sekcji **monitorowanie** na pasku bocznym wybierz pozycję **metryki**:

3. Wybierz pozycję **opóźnienie replikacji w sekundach** z listy rozwijanej dostępnych metryk.

   ![Wybierz opóźnienie replikacji](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Wybierz zakres czasu, który chcesz wyświetlić. Poniższy obraz wybiera 30-minutowy zakres czasu.

   ![Wybierz zakres czasu](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Wyświetl zwłokę replikacji dla wybranego zakresu czasu. Na poniższej ilustracji przedstawiono ostatnie 30 minut dla dużego obciążenia.

   ![Wybierz zakres czasu](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [odczytu replik](concepts-read-replicas.md)