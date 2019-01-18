---
title: Zarządzanie odczytu replik w witrynie Azure portal dla usługi Azure Database for PostgreSQL
description: W tym artykule opisano zarządzanie usługi Azure Database for PostgreSQL odczytu replik w witrynie Azure portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/17/2019
ms.openlocfilehash: 6c1a0a4a13a70daec157ede98f850f87150f8d93
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2019
ms.locfileid: "54383528"
---
# <a name="how-to-create-and-manage-read-replicas-in-the-azure-portal"></a>Jak utworzyć i zarządzać nimi odczytu replik w witrynie Azure portal
W tym artykule dowiesz się, jak tworzyć i zarządzać nimi odczytu replik w usłudze Azure Database dla PostgreSQL za pomocą witryny Azure portal. Aby dowiedzieć się więcej na temat odczytu repliki, [zapoznaj się z dokumentacją pojęcia](concepts-read-replicas.md).

## <a name="prerequisites"></a>Wymagania wstępne
- [— Azure Database for postgresql w warstwie serwera](quickstart-create-server-database-portal.md) się serwerem głównym.

## <a name="prepare-the-master-server"></a>Przygotowanie serwera głównego
W tym kroku przygotowania głównego dotyczy tylko serwerów ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci.

**Azure.replication_support** parametru musi być równa REPLIKI na serwerze głównym. Zmiana ten parametr wymaga ponownego uruchomienia serwera, aby zastosować zmiany.

1. W witrynie Azure portal wybierz istniejącego serwera Azure Database for postgresql w warstwie, którą chcesz używać jako wzorca.

2. Wybierz **parametrów serwera** z menu po lewej stronie.

3. Wyszukaj **azure.replication_support**.

   ![Azure Database for PostgreSQL — azure.replication_support](./media/howto-read-replicas-portal/azure-replication-parameter.png)

4. Ustaw **azure.replication_support** do REPLIKI. **Zapisz** zmiany.

   ![Azure Database for PostgreSQL — REPLIKI, a następnie zapisz](./media/howto-read-replicas-portal/save-parameter-replica.png)

5. Po zakończeniu zapisywania, otrzymasz powiadomienie.

   ![Azure Database for PostgreSQL — Zapisz powiadomień](./media/howto-read-replicas-portal/parameter-save-notification.png)

6. Uruchom ponownie serwer, aby zastosować zmiany po zapisaniu. Zobacz [dokumentacji ponownego uruchomienia](howto-restart-server-portal.md) dowiesz się, jak uruchomić ponownie serwer.

## <a name="create-a-read-replica"></a>Tworzenie repliki do odczytu
Można utworzyć odczytu repliki wykonując następujące czynności:
1.  Wybieranie istniejącego serwera Azure Database for postgresql w warstwie, którą chcesz używać jako wzorca. 

2.  Wybierz opcję replikacji z menu, w obszarze Ustawienia.

   Jeśli nie został ustawiony **azure.replication_support** do REPLIKI na ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci głównej i uruchomić ją ponownie serwer, zobaczysz komunikat z instrukcją dotyczącą Aby to zrobić. To zrobić przed kontynuowaniem utwórz.

3.  Wybieranie opcji dodawania repliki.

   ![Azure Database for PostgreSQL — Dodawanie repliki](./media/howto-read-replicas-portal/add-replica.png)

4.  Wprowadź nazwę dla serwera repliki, a następnie wybierz przycisk OK, aby potwierdzić Tworzenie repliki.

   ![Azure Database for PostgreSQL — nazwa repliki](./media/howto-read-replicas-portal/name-replica.png) 

> [!IMPORTANT]
> Repliki do odczytu są tworzone przy użyciu tej samej konfiguracji serwera jako wzorzec. Po utworzeniu repliki warstwa cenowa (z wyjątkiem do i z Basic), generacja obliczeń, rdzenie wirtualne, Magazyn i okres przechowywania kopii zapasowej można zmienić niezależnie od serwera głównego.

> [!IMPORTANT]
> Zanim Konfiguracja serwera głównego jest aktualizowany do nowych wartości, konfiguracji tych replik należy zaktualizować większa lub równa wartości. Takie próby w przeciwnym razie spowoduje błąd. Dzięki temu, że repliki są w stanie na bieżąco ze zmianami wprowadzonymi do poziomu głównego. 


Po utworzeniu serwera repliki mogą być wyświetlane z okna replikacji.

![Azure Database for PostgreSQL — nowej repliki](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>Zatrzymaj replikację

> [!IMPORTANT]
> Zatrzymywanie replikacji na serwerze jest nieodwracalne. Po replikacji została zatrzymana między głównego i repliki, nie można cofnąć. Serwer repliki następnie staje się serwerem autonomicznym i obsługuje teraz zarówno odczytu i zapisu. Ten serwer nie wprowadzać ponownie do repliki.

Aby zatrzymać replikacji między serwerem głównym i repliki z witryny Azure portal, użyj następujących kroków:
1.  W witrynie Azure portal wybierz swoje wzorca serwera Azure Database for postgresql w warstwie.

2.  Wybierz opcję replikacji z menu, w obszarze Ustawienia.

3.  Wybierz serwer repliki, który chcesz zatrzymać replikację.

   ![Azure Database for PostgreSQL — replika wybierz](./media/howto-read-replicas-portal/select-replica.png)
 
4.  Wybierz zatrzymanie replikacji.

   ![Azure Database for PostgreSQL — Wybierz zatrzymanie replikacji](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5.  Upewnij się, że chcesz zatrzymać replikację, klikając przycisk OK.

   ![Azure Database for PostgreSQL — potwierdzenie zatrzymania replikacji](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master"></a>Usuwanie wzorca

> [!IMPORTANT]
> Usuwanie serwera głównego zatrzymanie replikacji na wszystkich serwerach repliki. Serwer funkcji replica stają się autonomicznymi serwerami, które obsługują teraz zarówno odczytu i zapisu.
Usuwanie wzorca przedstawiono te same kroki, jak w przypadku autonomicznej usługi Azure Database dla serwera PostgreSQL. Aby usunąć serwer z witryny Azure portal, wykonaj następujące czynności:

1.  W witrynie Azure portal wybierz swoje wzorca serwera Azure Database for postgresql w warstwie.

2.  Od omówienia wybierz opcję Usuń.

   ![Usługa Azure Database for PostgreSQL — usuwanie serwera](./media/howto-read-replicas-portal/delete-server.png)
 
3.  Wpisz nazwę serwera głównego, a następnie wybierz opcję Usuń, aby potwierdzić usunięcie głównego serwera.

   ![Azure Database for PostgreSQL — potwierdzenie usunięcia](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>Usuwanie repliki
Aby usunąć replikę odczytu, wykonaj te same kroki podobnie jak w przypadku usuwania serwera głównego powyżej. Najpierw otwórz stronę przeglądu replik, a następnie wybierz opcję Usuń.

   ![Azure Database for PostgreSQL — Usuń replikę](./media/howto-read-replicas-portal/delete-replica.png)
 
Alternatywnie można go usunąć z okna replikacji.
1.  W witrynie Azure portal wybierz swoje wzorca serwera Azure Database for postgresql w warstwie.

2.  Wybierz opcję replikacji z menu, w obszarze Ustawienia.

3.  Wybierz serwer repliki, który chcesz usunąć. 

   ![Azure Database for PostgreSQL — replika wybierz](./media/howto-read-replicas-portal/select-replica.png)
 
4.  Wybierz opcję usunięcia repliki.

   ![Azure Database for PostgreSQL — wybierz polecenie Usuń replikę](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5.  Wpisz nazwę repliki, a następnie wybierz opcję Usuń, aby potwierdzić usunięcie repliki.

   ![Azure Database for PostgreSQL — upewnij się, Usuń replikę](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>Monitor repliki
### <a name="max-lag-across-replicas"></a>Maksymalna liczba opóźnienie między replikami
**Maksymalna zwłoka między replikami** Metryka przedstawia czas opóźnienia między główną i najbardziej opóźnione repliką. 

1.  W witrynie Azure portal wybierz **wzorca** — Azure Database for postgresql w warstwie serwera.

2.  Wybierz metryki. W oknie metryk wybierz **opóźnienie między maksymalna liczba replik**.

    ![Azure Database for PostgreSQL — Monitor max opóźnienie między replikami](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  Wybierz **Max** jako usługi agregacji. 

### <a name="replica-lag"></a>Opóźnienie repliki
**Lag repliki** pomiar przedstawia czas od czasu ostatniego powtórzone transakcji dla tej repliki. Jeśli nie ma żadnych transakcji, występujących na wzorca usługi, metryki odzwierciedla to opóźnienie czasowe.

1.  W witrynie Azure portal wybierz **repliki** — Azure Database for postgresql w warstwie serwera.

2.  Wybierz metryki. W oknie metryk wybierz **Lag repliki**.

   ![Azure Database for PostgreSQL — opóźnienie repliki monitora](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3.  Wybierz **Max** jako usługi agregacji. 
 
## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [odczytu replik w usłudze Azure Database for PostgreSQL](concepts-read-replicas.md).