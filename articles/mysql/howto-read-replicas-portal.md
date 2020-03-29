---
title: Zarządzanie replikami odczytu — witryna Azure portal — usługa Azure Database for MySQL
description: Dowiedz się, jak skonfigurować repliki odczytu i zarządzać nimi w usłudze Azure Database for MySQL przy użyciu witryny Azure portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: b7226e5ae8c468339e02dbe87e279266e4609da8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063488"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-portal"></a>Jak tworzyć repliki odczytu i zarządzać nimi w usłudze Azure Database for MySQL przy użyciu portalu Azure

W tym artykule dowiesz się, jak tworzyć repliki odczytu i zarządzać nimi w usłudze Azure Database for MySQL przy użyciu portalu Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- [Usługa Azure Database dla serwera MySQL,](quickstart-create-mysql-server-database-using-azure-portal.md) który będzie używany jako serwer główny.

> [!IMPORTANT]
> Funkcja repliki odczytu jest dostępna tylko dla usługi Azure Database dla serwerów MySQL w warstwach cenowych ogólnego przeznaczenia lub zoptymalizowanej pod kątem pamięci. Upewnij się, że serwer główny znajduje się w jednej z tych warstw cenowych.

## <a name="create-a-read-replica"></a>Tworzenie repliki odczytu

Serwer replik odczytu można utworzyć przy użyciu następujących kroków:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/).

2. Wybierz istniejącą usługę Azure Database dla serwera MySQL, którego chcesz użyć jako wzorca. Ta akcja powoduje otwarcie strony **Przegląd.**

3. Wybierz **opcję Replikacja** z menu w obszarze **USTAWIENIA**.

4. Wybierz **pozycję Dodaj replikę**.

   ![Usługa Azure Database for MySQL — replikacja](./media/howto-read-replica-portal/add-replica.png)

5. Wprowadź nazwę serwera replik.

    ![Usługa Azure Database for MySQL — nazwa repliki](./media/howto-read-replica-portal/replica-name.png)

6. Wybierz lokalizację serwera replik. Domyślna lokalizacja jest taka sama jak na serwerze głównym.

    ![Usługa Azure Database for MySQL — lokalizacja repliki](./media/howto-read-replica-portal/replica-location.png)

   > [!NOTE]
   > Aby dowiedzieć się więcej o regionach, w których można utworzyć replikę, odwiedź [artykuł o pojęciach dotyczących repliki do odczytu](concepts-read-replicas.md). 

7. Wybierz **przycisk OK,** aby potwierdzić utworzenie repliki.

> [!NOTE]
> Repliki odczytu są tworzone przy tej samej konfiguracji serwera co wzorzec. Konfigurację serwera repliki można zmienić po jej utworzeniu. Zaleca się, aby konfiguracja serwera repliki była utrzymywana na równych lub większych wartościach niż wzorca, aby zapewnić, że replika jest w stanie nadążyć za wzorcem.

Po utworzeniu serwera repliki można go wyświetlić z bloku **Replikacja.**

   ![Usługa Azure Database for MySQL — lista replik](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Zatrzymywać replikację na serwerze repliki

> [!IMPORTANT]
> Zatrzymanie replikacji na serwerze jest nieodwracalne. Po zatrzymaniu replikacji między wzorcem a repliką nie można jej cofnąć. Serwer repliki staje się następnie serwerem autonomicznym i obsługuje teraz zarówno odczyt, jak i zapis. Ten serwer nie może być ponownie przekształcony w replikę.

Aby zatrzymać replikację między serwerem wzorcowym a serwerem replik z witryny Azure Portal, należy wykonać następujące kroki:

1. W witrynie Azure portal wybierz główną usługę Azure Database dla serwera MySQL. 

2. Wybierz **opcję Replikacja** z menu w obszarze **USTAWIENIA**.

3. Wybierz serwer repliki, dla którego chcesz zatrzymać replikację.

   ![Usługa Azure Database for MySQL — zatrzymaj serwer wybierania replikacji](./media/howto-read-replica-portal/stop-replication-select.png)

4. Wybierz **pozycję Zatrzymaj replikację**.

   ![Usługa Azure Database for MySQL — zatrzymaj replikację](./media/howto-read-replica-portal/stop-replication.png)

5. Potwierdź, że chcesz zatrzymać replikację, klikając przycisk **OK**.

   ![Usługa Azure Database for MySQL — potwierdzenie zatrzymania replikacji](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Usuwanie serwera repliki

Aby usunąć odczyt serwera replik z witryny Azure portal, należy wykonać następujące kroki:

1. W witrynie Azure portal wybierz główną usługę Azure Database dla serwera MySQL.

2. Wybierz **opcję Replikacja** z menu w obszarze **USTAWIENIA**.

3. Wybierz serwer repliki, który chcesz usunąć.

   ![Usługa Azure Database for MySQL — usuwanie serwera wyboru repliki](./media/howto-read-replica-portal/delete-replica-select.png)

4. Wybierz **usuń replikę**

   ![Usługa Azure Database for MySQL — usuwanie repliki](./media/howto-read-replica-portal/delete-replica.png)

5. Wpisz nazwę repliki i kliknij przycisk **Usuń,** aby potwierdzić usunięcie repliki.  

   ![Usługa Azure Database for MySQL — potwierdzenie usunięcia repliki](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Usuwanie serwera głównego

> [!IMPORTANT]
> Usunięcie serwera głównego powoduje zatrzymanie replikacji do wszystkich serwerów repliki i usunięcie samego serwera głównego. Serwery repliki stają się serwerami autonomicznymi, które teraz obsługują zarówno odczyt, jak i zapis.

Aby usunąć serwer główny z witryny Azure portal, należy wykonać następujące kroki:

1. W witrynie Azure portal wybierz główną usługę Azure Database dla serwera MySQL.

2. Z **przeglądu**wybierz pozycję **Usuń**.

   ![Usługa Azure Database for MySQL — usuń wzorzec](./media/howto-read-replica-portal/delete-master-overview.png)

3. Wpisz nazwę serwera głównego i kliknij przycisk **Usuń,** aby potwierdzić usunięcie serwera głównego.  

   ![Usługa Azure Database for MySQL — usuń wzorzec](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Monitorowanie replikacji

1. W [witrynie Azure portal](https://portal.azure.com/)wybierz replikę usługi Azure Database dla serwera MySQL, który chcesz monitorować.

2. W sekcji **Monitorowanie** paska bocznego wybierz **Metryki:**

3. Wybierz **opóźnienie replikacji w sekundach** z listy rozwijanej dostępnych metryk.

   ![Wybierz opóźnienie replikacji](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Wybierz zakres czasu, który chcesz wyświetlić. Poniższy obrazek wybiera 30-minutowy zakres czasu.

   ![Wybierz zakres czasu](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Służy do wyświetlania opóźnienia replikacji dla wybranego zakresu czasu. Poniższy obraz ekspozycjowy przedstawia ostatnie 30 minut.

   ![Wybierz zakres czasu](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [replikach odczytu](concepts-read-replicas.md)