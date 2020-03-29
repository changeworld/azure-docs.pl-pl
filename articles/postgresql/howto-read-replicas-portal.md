---
title: Zarządzanie replikami odczytu — witryna Azure portal — usługa Azure Database for PostgreSQL — single server
description: Dowiedz się, jak zarządzać replikami odczytu usługi Azure Database for PostgreSQL — pojedynczy serwer z witryny Azure portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: dd79618b8d9f016c92166edb9ecdb0bfb113947e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76768959"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>Tworzenie replik odczytu i zarządzanie nimi w bazie danych Azure dla postgreSQL — pojedynczy serwer z witryny Azure portal

W tym artykule dowiesz się, jak tworzyć repliki odczytu i zarządzać nimi w usłudze Azure Database for PostgreSQL z witryny Azure portal. Aby dowiedzieć się więcej o replikach odczytu, zobacz [omówienie](concepts-read-replicas.md).


## <a name="prerequisites"></a>Wymagania wstępne
[Serwer usługi Azure Database for PostgreSQL](quickstart-create-server-database-portal.md) ma być serwerem głównym.

## <a name="prepare-the-master-server"></a>Przygotowanie serwera głównego
Te kroki muszą służyć do przygotowania serwera głównego w warstwach ogólnego przeznaczenia lub zoptymalizowane pod kątem pamięci. Serwer główny jest przygotowany do replikacji przez ustawienie parametru azure.replication_support. Po zmianie parametru replikacji do zmiany wymagane jest ponowne uruchomienie serwera. W witrynie Azure portal te dwa kroki są hermetyzowane za pomocą jednego przycisku **Włącz obsługę replikacji**.

1. W witrynie Azure portal wybierz istniejącą usługę Azure Database dla serwera PostgreSQL, który będzie używany jako wzorzec.

2. Na pasku bocznym serwera w obszarze **USTAWIENIA**wybierz pozycję **Replikacja**.

> [!NOTE] 
> Jeśli zostanie wyświetlony komunikat **Wyłącz obsługę replikacji** wyszarzony, ustawienia replikacji są już domyślnie ustawione na serwerze. Możesz pominąć następujące kroki i przejść do utworzenia repliki odczytu. 

3. Wybierz **pozycję Włącz obsługę replikacji**. 

   ![Włącz obsługę replikacji](./media/howto-read-replicas-portal/enable-replication-support.png)

4. Potwierdź, że chcesz włączyć obsługę replikacji. Ta operacja spowoduje ponowne uruchomienie serwera głównego. 

   ![Potwierdź obsługę replikacji](./media/howto-read-replicas-portal/confirm-enable-replication.png)
   
5. Po zakończeniu operacji otrzymasz dwa powiadomienia portalu Azure. Istnieje jedno powiadomienie o aktualizacji parametru serwera. Istnieje inne powiadomienie o ponownym uruchomieniu serwera, które następuje natychmiast.

   ![Powiadomienia o sukcesie — włącz](./media/howto-read-replicas-portal/success-notifications-enable.png)

6. Odśwież stronę portalu Platformy Azure, aby zaktualizować pasek narzędzi Replikacja. Teraz można utworzyć repliki odczytu dla tego serwera.

   ![Zaktualizowany pasek narzędzi](./media/howto-read-replicas-portal/updated-toolbar.png)
   
Włączenie obsługi replikacji jest operacją jednorazową na serwer główny. **Przycisk Wyłącz obsługę replikacji** jest dla Twojej wygody. Nie zaleca się wyłączania obsługi replikacji, chyba że masz pewność, że nigdy nie utworzysz repliki na tym serwerze głównym. Nie można wyłączyć obsługi replikacji, gdy serwer główny ma istniejące repliki.


## <a name="create-a-read-replica"></a>Tworzenie repliki odczytu
Aby utworzyć replikę odczytu, wykonaj następujące czynności:

1. Wybierz istniejącą usługę Azure Database dla serwera PostgreSQL, który będzie używany jako serwer główny. 

2. Na pasku bocznym serwera w obszarze **USTAWIENIA**wybierz pozycję **Replikacja**.

3. Wybierz **pozycję Dodaj replikę**.

   ![Dodawanie repliki](./media/howto-read-replicas-portal/add-replica.png)

4. Wprowadź nazwę repliki odczytu. 

    ![Nadaj nazwę repliki](./media/howto-read-replicas-portal/name-replica.png)

5. Wybierz lokalizację repliki. Domyślna lokalizacja jest taka sama jak na serwerze głównym.

    ![Wybieranie lokalizacji](./media/howto-read-replicas-portal/location-replica.png)

   > [!NOTE]
   > Aby dowiedzieć się więcej o regionach, w których można utworzyć replikę, odwiedź [artykuł o pojęciach dotyczących repliki do odczytu](concepts-read-replicas.md). 

6. Wybierz **przycisk OK,** aby potwierdzić utworzenie repliki.

Replika jest tworzona przy użyciu tych samych ustawień obliczeniowych i magazynu, co wzorzec. Po utworzeniu repliki można zmienić kilka ustawień niezależnie od serwera głównego: generowanie obliczeń, wirtualne, magazynowanie i okres przechowywania kopii zapasowej. Warstwę cenową można również zmienić niezależnie, z wyjątkiem warstwy Podstawowa lub podstawowa.

> [!IMPORTANT]
> Przed zaktualizowaniem ustawienia serwera głównego do nowej wartości należy zaktualizować ustawienie repliki do wartości równej lub większej. Ta akcja pomaga repliki nadążyć za wszelkimi zmianami wprowadzonymi do wzorca.

Po utworzeniu repliki odczytu można ją wyświetlić w oknie **Replikacja:**

![Wyświetlanie nowej repliki w oknie Replikacja](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>Zatrzymywanie replikacji
Replikację można zatrzymać między serwerem głównym a repliką odczytu.

> [!IMPORTANT]
> Po zatrzymaniu replikacji na serwerze głównym i repliki odczytu nie można jej cofnąć. Replika odczytu staje się autonomicznym serwerem, który obsługuje zarówno odczyty, jak i zapisy. Nie można ponownie przekształcić serwera autonomicznego w replikę.

Aby zatrzymać replikację między serwerem głównym a repliką odczytu z witryny Azure Portal, wykonaj następujące kroki:

1. W witrynie Azure portal wybierz główną usługę Azure Database dla serwera PostgreSQL.

2. W menu serwera w obszarze **USTAWIENIA**wybierz pozycję **Replikacja**.

3. Wybierz serwer repliki, dla którego ma być zatrzymana replikacja.

   ![Wybierz replikę](./media/howto-read-replicas-portal/select-replica.png)
 
4. Wybierz **pozycję Zatrzymaj replikację**.

   ![Wybierz replikację zatrzymania](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5. Wybierz **przycisk OK,** aby zatrzymać replikację.

   ![Potwierdź, aby zatrzymać replikację](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master-server"></a>Usuwanie serwera głównego
Aby usunąć serwer główny, należy wykonać te same kroki, aby usunąć autonomiczną usługę Azure Database dla serwera PostgreSQL. 

> [!IMPORTANT]
> Po usunięciu serwera głównego replikacja wszystkich replik odczytu jest zatrzymywana. Repliki odczytu stają się autonomicznymi serwerami, które teraz obsługują zarówno odczyty, jak i zapisy.

Aby usunąć serwer z witryny Azure portal, wykonaj następujące kroki:

1. W witrynie Azure portal wybierz główną usługę Azure Database dla serwera PostgreSQL.

2. Otwórz stronę **Przegląd** serwera. Wybierz pozycję **Usuń**.

   ![Na stronie Przegląd serwera wybierz pozycję, aby usunąć serwer główny](./media/howto-read-replicas-portal/delete-server.png)
 
3. Wprowadź nazwę serwera głównego do usunięcia. Wybierz **pozycję Usuń,** aby potwierdzić usunięcie serwera głównego.

   ![Potwierdź, aby usunąć serwer główny](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>Usuwanie repliki
Można usunąć replikę odczytu podobną do sposobu usuwania serwera głównego.

- W witrynie Azure portal otwórz stronę **Przegląd** repliki odczytu. Wybierz pozycję **Usuń**.

   ![Na stronie Przegląd repliki wybierz pozycję, aby usunąć replikę](./media/howto-read-replicas-portal/delete-replica.png)
 
Replikę odczytu można również usunąć z okna **Replikacja,** wykonując następujące kroki:

1. W witrynie Azure portal wybierz główną usługę Azure Database dla serwera PostgreSQL.

2. W menu serwera w obszarze **USTAWIENIA**wybierz pozycję **Replikacja**.

3. Wybierz replikę odczytu do usunięcia.

   ![Wybierz replikę do usunięcia](./media/howto-read-replicas-portal/select-replica.png)
 
4. Wybierz pozycję **Usuń replikę**.

   ![Wybierz replikę usuwania](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5. Wprowadź nazwę repliki do usunięcia. Wybierz **pozycję Usuń,** aby potwierdzić usunięcie repliki.

   ![Potwierdź, aby usunąć replikę te](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>Monitorowanie repliki
Dostępne są dwie metryki do monitorowania replik odczytu.

### <a name="max-lag-across-replicas-metric"></a>Maksymalna liczba opóźnień w replikach metryka
**Metryka Maksymalna liczba opóźnień** w replikach pokazuje opóźnienie w bajtach między serwerem głównym a repliką o największym opóźnieniu. 

1.  W witrynie Azure portal wybierz główną bazę danych platformy Azure dla serwera PostgreSQL.

2.  Wybierz pozycję **Metryki**. W oknie **Metryki** wybierz pozycję **Max Lag Across Replicas**.

    ![Monitorowanie maksymalnych opóźnień między replikami](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  Dla **agregacji**wybierz **Max**.


### <a name="replica-lag-metric"></a>Metryka opóźnienia repliki
**Metryka Opóźnienie repliki** pokazuje czas od ostatniej powtórzonejednoraz na replikę. Jeśli na wzorcu nie ma żadnych transakcji, metryka odzwierciedla opóźnienie tego czasu.

1. W witrynie Azure portal wybierz replikę odczytu usługi Azure Database for PostgreSQL.

2. Wybierz pozycję **Metryki**. W oknie **Metryki** wybierz pozycję **Opóźnienie repliki**.

   ![Monitorowanie opóźnienia repliki](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3. Dla **agregacji**wybierz **Max**. 
 
## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [odczytywania replik w usłudze Azure Database for PostgreSQL](concepts-read-replicas.md).
* Dowiedz się, jak [tworzyć repliki odczytu i zarządzać nimi w interfejsie API interfejsu wiersza polecenia platformy Azure i rest.](howto-read-replicas-cli.md)