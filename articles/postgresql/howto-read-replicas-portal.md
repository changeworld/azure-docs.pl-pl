---
title: Zarządzanie odczytu replik dla usługi Azure Database for PostgreSQL w witrynie Azure portal
description: Dowiedz się, jak zarządzać — Azure Database for PostgreSQL odczytu replik w witrynie Azure portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: 24a37775298d6c6b40ec49f34158fcb77f26a379
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "58113218"
---
# <a name="create-and-manage-read-replicas-from-the-azure-portal"></a>Tworzenie i zarządzanie nimi odczytu replik w witrynie Azure portal

W tym artykule dowiesz się, jak tworzyć i zarządzać odczytu replik w usłudze Azure Database for PostgreSQL w witrynie Azure portal. Aby dowiedzieć się więcej na temat odczytu repliki, zobacz [Przegląd](concepts-read-replicas.md).

> [!IMPORTANT]
> Funkcja odczytu repliki jest w publicznej wersji zapoznawczej.

## <a name="prerequisites"></a>Wymagania wstępne
[— Azure Database for postgresql w warstwie serwera](quickstart-create-server-database-portal.md) jako serwer główny.

## <a name="prepare-the-master-server"></a>Przygotowanie serwera głównego
Te kroki musi służyć do przygotowania serwera głównego w warstwach ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci.

`azure.replication_support` Parametru musi być równa **REPLIKI** na serwerze głównym. Po zmianie tego parametru ponownego uruchomienia serwera jest wymagany, aby zmiana zaczęła obowiązywać.

1. W witrynie Azure portal wybierz istniejących Azure Database dla serwera PostgreSQL do użycia jako główny.

2. W menu po lewej stronie wybierz **parametrów serwera**.

3. Wyszukaj `azure.replication_support` parametru.

   ![Wyszukaj parametr azure.replication_support](./media/howto-read-replicas-portal/azure-replication-parameter.png)

4. Ustaw `azure.replication_support` wartości parametru **REPLIKI**. Wybierz **Zapisz** Aby zachować zmiany.

   ![Ustaw dla parametru do REPLIKI, a następnie zapisz zmiany](./media/howto-read-replicas-portal/save-parameter-replica.png)

5. Po zapisaniu zmian zostanie wyświetlone powiadomienie:

   ![Zapisz powiadomień](./media/howto-read-replicas-portal/parameter-save-notification.png)

6. Uruchom ponownie serwer, aby zastosować zmiany. Aby dowiedzieć się, jak uruchomić ponownie serwer, zobacz [ponownego uruchomienia usługi Azure Database for postgresql w warstwie serwera](howto-restart-server-portal.md).


## <a name="create-a-read-replica"></a>Tworzenie repliki do odczytu
Aby utworzyć odczytu repliki, wykonaj następujące kroki:

1. Wybierz istniejący Azure bazę danych dla serwera PostgreSQL do użycia jako serwer główny. 

2. W menu serwer w ramach **ustawienia**, wybierz opcję **replikacji**.

   Jeśli nie został ustawiony `azure.replication_support` parametr **REPLIKI** na ogólnego przeznaczenia lub zoptymalizowane pod kątem pamięci głównego serwera i uruchom ponownie serwer, otrzymasz powiadomienie. Przed przystąpieniem do tworzenia repliki, wykonaj te kroki.

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