---
title: Zarządzanie odczytu replik dla usługi Azure Database for PostgreSQL w witrynie Azure portal
description: Dowiedz się, jak zarządzać — Azure Database for PostgreSQL odczytu replik w witrynie Azure portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2019
ms.openlocfilehash: bf1fb1c1343173949ecb6348284cb537282b277b
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2019
ms.locfileid: "58846965"
---
# <a name="create-and-manage-read-replicas-from-the-azure-portal"></a>Tworzenie i zarządzanie nimi odczytu replik w witrynie Azure portal

W tym artykule dowiesz się, jak tworzyć i zarządzać odczytu replik w usłudze Azure Database for PostgreSQL w witrynie Azure portal. Aby dowiedzieć się więcej na temat odczytu repliki, zobacz [Przegląd](concepts-read-replicas.md).


## <a name="prerequisites"></a>Wymagania wstępne
[— Azure Database for postgresql w warstwie serwera](quickstart-create-server-database-portal.md) jako serwer główny.

## <a name="prepare-the-master-server"></a>Przygotowanie serwera głównego
Te kroki musi służyć do przygotowania serwera głównego w warstwach ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci. Serwer główny jest przygotowana do replikacji, ustawiając parametr azure.replication_support. Po zmianie parametrów replikacji ponownego uruchomienia serwera jest wymagany, aby zmiana zaczęła obowiązywać. W witrynie Azure portal, następujące dwa kroki są hermetyzowane przez jednego przycisku **Włącz obsługę replikacji**.

1. W witrynie Azure portal wybierz istniejących Azure Database dla serwera PostgreSQL do użycia jako główny.

2. Na pasku bocznym serwera w obszarze **ustawienia**, wybierz opcję **replikacji**.

3. Wybierz **Włączanie obsługi replikacji**. 

   ![Włączanie obsługi replikacji](./media/howto-read-replicas-portal/enable-replication-support.png)

4. Upewnij się, że chcesz włączyć obsługę replikacji. Ta operacja zostanie uruchomiony ponownie serwera głównego. 

   ![Upewnij się, Włącz obsługę replikacji](./media/howto-read-replicas-portal/confirm-enable-replication.png)
   
5. Po zakończeniu operacji otrzymasz dwa powiadomienia z portalu Azure. Istnieje jedno powiadomienie do aktualizowania parametrów serwera. Brak kolejne powiadomienie do ponownego uruchomienia serwera, który następuje od razu.

   ![Włącz powiadomienia sukces —](./media/howto-read-replicas-portal/success-notifications-enable.png)

6. Odśwież stronę witryny Azure portal do aktualizacji narzędzi replikacji. Teraz można utworzyć odczytu replik dla tego serwera.

   ![Zaktualizowany pasek narzędzi](./media/howto-read-replicas-portal/updated-toolbar.png)
   
Włączanie obsługi replikacji jest to jednorazowa operacja na serwerze głównym. A **Wyłącz Replication Support** przycisk jest dostarczana dla wygody. Nie zaleca się wyłączenie obsługi replikacji, chyba że masz pewność, że nigdy nie utworzy repliki na tym serwerze głównym. Nie można wyłączyć obsługę replikacji, gdy główny serwer ma istniejących replik.


## <a name="create-a-read-replica"></a>Tworzenie repliki do odczytu
Aby utworzyć odczytu repliki, wykonaj następujące kroki:

1. Wybierz istniejący Azure bazę danych dla serwera PostgreSQL do użycia jako serwer główny. 

2. Na pasku bocznym serwera w obszarze **ustawienia**, wybierz opcję **replikacji**.

3. Wybierz **dodać repliki**.

   ![Dodaj replikę](./media/howto-read-replicas-portal/add-replica.png)

4. Wprowadź nazwę dla odczytu repliki. Wybierz **OK** o potwierdzenie utworzenia repliki.

   ![Nazwa repliki](./media/howto-read-replicas-portal/name-replica.png) 

Replika jest tworzona przy użyciu tej samej konfiguracji serwera jako wzorzec. Po utworzeniu repliki, niektóre ustawienia można zmienić niezależnie z serwera głównego: obliczenia generacji, rdzenie wirtualne, Magazyn i okres przechowywania kopii zapasowych. Warstwę cenową można zmienić niezależnie, z wyjątkiem do lub z warstwy podstawowa.

> [!IMPORTANT]
> Konfiguracja serwera głównego jest aktualizowany do nowych wartości, aktualizacja konfiguracji repliki większa lub równa wartości. Ta akcja zagwarantuje, że repliki może nadążyć za wszelkie zmiany wprowadzone do poziomu głównego.

Po utworzeniu repliki odczytu mogą być wyświetlane z **replikacji** okna:

![Wyświetl nową replikę w oknie replikacji](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>Zatrzymaj replikację
Można zatrzymać replikację między głównym serwerem i odczytu repliki.

> [!IMPORTANT]
> Po zatrzymaniu replikację do serwera głównego i odczytu repliki, nie można cofnąć. Przeczytaj replika staje się serwer autonomiczny, który obsługuje operacje odczytu i zapisu. Serwer autonomiczny nie wprowadzać ponownie do repliki.

Aby zatrzymać replikację między głównym serwerem i odczytu replik w witrynie Azure portal, wykonaj następujące kroki:

1. W witrynie Azure portal wybierz swoje wzorca serwera Azure Database for postgresql w warstwie.

2. W menu serwer w ramach **ustawienia**, wybierz opcję **replikacji**.

3. Wybierz serwer repliki, do których chcesz zatrzymać replikację.

   ![Wybierz replikę](./media/howto-read-replicas-portal/select-replica.png)
 
4. Wybierz **zatrzymanie replikacji**.

   ![Wybierz zatrzymanie replikacji](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5. Wybierz **OK** zatrzymania replikacji.

   ![Upewnij się, aby zatrzymać replikację](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master-server"></a>Usuń serwer główny
Aby usunąć serwer główny, należy użyć te same kroki, aby usunąć autonomiczne bazy danych Azure Database dla serwera PostgreSQL. 

> [!IMPORTANT]
> Po usunięciu serwera głównego, replikacja wszystkich replik do odczytu został zatrzymany. Odczytu replik stają się autonomicznymi serwerami, które teraz obsługują operacje odczytu i zapisu.

Aby usunąć serwer z witryny Azure portal, wykonaj następujące kroki:

1. W witrynie Azure portal wybierz swoje wzorca serwera Azure Database for postgresql w warstwie.

2. Otwórz **Przegląd** stronie serwera. Wybierz pozycję **Usuń**.

   ![Na stronie Przegląd serwera wybierz, aby usunąć serwer główny](./media/howto-read-replicas-portal/delete-server.png)
 
3. Wprowadź nazwę serwera głównego do usunięcia. Wybierz **Usuń** o potwierdzenie usunięcia serwera głównego.

   ![Upewnij się, aby usunąć serwer główny](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>Usuwanie repliki
Możesz usunąć odczytu repliki, podobnie jak usunąć serwer główny.

- W witrynie Azure portal Otwórz **Przegląd** strona odczytu replik. Wybierz pozycję **Usuń**.

   ![Na stronie Przegląd replika wybierz, aby usunąć replikę](./media/howto-read-replicas-portal/delete-replica.png)
 
Możesz także usunąć odczytu repliki z **replikacji** okna, wykonując następujące czynności:

1. W witrynie Azure portal wybierz swoje wzorca serwera Azure Database for postgresql w warstwie.

2. W menu serwer w ramach **ustawienia**, wybierz opcję **replikacji**.

3. Wybierz pozycję odczytu repliki, aby usunąć.

   ![Wybierz replikę tak, aby usunąć](./media/howto-read-replicas-portal/select-replica.png)
 
4. Wybierz **Usuń replikę**.

   ![Wybierz opcję Usuń replikę](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5. Wprowadź nazwę replikę tak, aby usunąć. Wybierz **Usuń** o potwierdzenie usunięcia replik.

   ![Upewnij się, aby usunąć replikę Usuń](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>Monitor repliki
Dwie metryki są dostępne dla monitorowania odczytu replik.

### <a name="max-lag-across-replicas-metric"></a>Metryka opóźnienie między maksymalna liczba replik
**Opóźnienie między maksymalna liczba replik** Metryka przedstawia opóźnienie w bajtach między głównym serwerem i repliką większość opóźnione. 

1.  W witrynie Azure portal wybierz główny usługi Azure Database for postgresql w warstwie serwera.

2.  Wybierz pozycję **Metryki**. W **metryki** wybierz **opóźnienie między maksymalna liczba replik**.

    ![Monitor Maksymalny odstęp między replikami](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  Dla Twojej **agregacji**, wybierz opcję **Max**.


### <a name="replica-lag-metric"></a>Metryka Lag repliki
**Lag repliki** pomiar przedstawia czas od czasu ostatniego powtórzone transakcji w replice. Jeśli nie ma żadnych transakcji, występujących na wzorca usługi, metryki odzwierciedla to opóźnienie czasowe.

1. W witrynie Azure portal wybierz bazę danych Azure database for PostgreSQL odczytu replik.

2. Wybierz pozycję **Metryki**. W **metryki** wybierz **Lag repliki**.

   ![Monitor lag repliki](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3. Dla Twojej **agregacji**, wybierz opcję **Max**. 
 
## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [odczytu replik w usłudze Azure Database for PostgreSQL](concepts-read-replicas.md).