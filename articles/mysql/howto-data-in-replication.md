---
title: Konfigurowanie replikacji danych w replikacji danych do bazy danych Azure dla programu MySQL.
description: W tym artykule opisano sposób skonfigurowania danych w replikacji bazy danych Azure dla programu MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 05/18/2018
ms.openlocfilehash: 4929c770bf21ddf660ccff4720f17f9bb3136ead
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655659"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Jak skonfigurować bazy danych platformy Azure dla danych MySQL w replikacji

W tym artykule dowiesz się, jak skonfigurować replikacji w danych w bazie danych Azure dla usługi MySQL przez Konfigurowanie podstawowego i serwery repliki.

W tym artykule przyjęto założenie, że masz co najmniej pewne doświadczenie z serwerów MySQL i baz danych.

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>Utwórz serwer MySQL ma być używany jako repliki

1. Utwórz nową bazę danych Azure MySQL serwera

   Utwórz nowy serwer MySQL (np. "replica.mysql.database.azure.com"). Zapoznaj się [utworzenia Azure bazy danych MySQL serwera przy użyciu portalu Azure](quickstart-create-mysql-server-database-using-azure-portal.md) do utworzenia serwera. Ten serwer jest serwerem "repliki" w danych w ramach replikacji.

   > [!IMPORTANT]
   > Ten serwer muszą być tworzone w warstw cenowych ogólnego przeznaczenia lub zoptymalizowanych pod kątem pamięci.
   > 

2. Tworzenie tych samych kont użytkowników i odpowiednie uprawnienia.

   Konta użytkowników nie są replikowane z serwera podstawowego na serwer repliki. Jeśli planujesz zapewnia użytkownikom dostęp do serwera repliki, należy ręcznie utworzyć na nowo utworzonej bazy danych platformy Azure dla serwera MySQL wszystkich kont i odpowiednie uprawnienia.

## <a name="configure-the-primary-server"></a>Konfiguracja serwera podstawowego

1. Włączanie rejestrowania binarnego

   Sprawdź, czy włączono rejestrowania binarnego na serwerze podstawowym, uruchamiając następujące polecenie: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Jeśli zmienna [ `log_bin` ](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) jest zwracany za wartość "Włącz", rejestrowania binarnego jest włączona na tym serwerze. 

   Jeśli `log_bin` jest zwróciła wartość "OFF", Włącz rejestrowanie, edytując plik my.cnf tego pliku binarnego który `log_bin=ON` i uruchomić ponownie serwer, aby zmiany zaczęły obowiązywać.

2. Ustawienia serwera podstawowego

   Dane w replikacji wymaga parametru `lower_case_table_names` aby było spójne między serwerami podstawowym i repliki. Tego parametru to 1 domyślnie w bazie danych Azure dla programu MySQL. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Utwórz nową rolę replikacji i ustaw uprawnienia

   Tworzenie konta użytkownika na serwerze podstawowym, który jest skonfigurowany z uprawnieniami replikacji. Można to zrobić za pomocą poleceń SQL lub narzędzia, takiego jak MySQL Workbench. Należy rozważyć, czy jest planowane replikację przy użyciu protokołu SSL, jak to należy określić podczas tworzenia użytkownika. Zapoznaj się z dokumentacją MySQL, aby zrozumieć, jak [dodawania kont użytkowników](https://dev.mysql.com/doc/refman/5.7/en/adding-users.html) na serwerze podstawowym. 

   W poniższe polecenia uzyskiwać dostęp do serwera podstawowego z dowolnej maszyny nie tylko maszynę, która obsługuje sam podstawowy jest utworzona nowa rola replikacji. Jest to realizowane przez określenie "syncuser@'%" "polecenia create użytkownika. Aby dowiedzieć się więcej na temat dokumentacji MySQL [określenie nazwy kont](https://dev.mysql.com/doc/refman/5.7/en/account-names.html).

   **Polecenie SQL**

   *Replikacja z użyciem protokołu SSL*

   Aby wymagać protokołu SSL dla wszystkich połączeń użytkownika, wpisz następujące polecenie, aby utworzyć użytkownika: 

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *Replikacji bez zastosowania protokołu SSL*

   Jeśli protokół SSL nie jest wymagane dla wszystkich połączeń, użyj następującego polecenia, aby utworzyć użytkownika:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   Aby utworzyć rolę replikacji w MySQL Workbench, otwórz **użytkowników i uprawnienia** panelu z **zarządzania** panelu. Następnie kliknij polecenie **Dodaj konto**. 
 
   ![Użytkownicy i uprawnień](./media/howto-data-in-replication/users_privileges.png)

   Typ użytkownika w **nazwa logowania** pola. 

   ![Użytkownik synchronizacji](./media/howto-data-in-replication/syncuser.png)
 
   Polecenie **ról administracyjnych** panelu, a następnie wybierz **podrzędna replikacji** z listy **uprawnień globalnych**. Następnie kliknij polecenie **Zastosuj** Aby utworzyć rolę replikacji.

   ![Podrzędny replikacji](./media/howto-data-in-replication/replicationslave.png)


4. Ustaw serwera podstawowego do trybu tylko do odczytu

   Przed rozpoczęciem zrzutu limit bazy danych, serwer musi być umieszczona w trybie tylko do odczytu. W trybie tylko do odczytu, nie można przetworzyć wszystkich transakcji zapisu będzie serwera podstawowego. Ocena wpływu na firmę i Zaplanuj okna tylko do odczytu w poza godzinami szczytu w razie potrzeby.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Nazwa pliku binarnego dziennika i przesunięcie

   Uruchom [ `show master status` ](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) polecenie w celu ustalenia bieżącej nazwy pliku dziennik binarny i przesunięcie.
    
   ```sql
   show master status;
   ```
   Wyniki powinny być podobnie jak poniżej. Koniecznie Zanotuj nazwę pliku binarnego, ponieważ będzie on używany w kolejnych krokach.

   ![Wyniki stanu głównego](./media/howto-data-in-replication/masterstatus.png)
 
## <a name="dump-and-restore-primary-server"></a>Zrzut i przywracania serwera podstawowego

1. Zrzut wszystkich baz danych z serwera podstawowego

   Mysqldump można użyć do baz danych zrzutu z Twojego podstawowego. Aby uzyskać więcej informacji, zapoznaj się [zrzutu & Przywróć](concepts-migrate-dump-restore.md). Nie jest konieczne zrzutu MySQL biblioteki i biblioteki testowej.

2. Ustaw serwera podstawowego do trybu odczytu/zapisu

   Po bazy danych zostały utworzyć zrzutu, zmienić podstawowy MySQL serwera wstecz do odczytu/zapisu w trybie.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Przywróć plik zrzutu do nowego serwera

   Przywróć plik zrzutu serwerowi utworzone w bazie danych Azure dla usługi MySQL. Zapoznaj się [zrzutu & Przywróć](concepts-migrate-dump-restore.md) jak przywrócić plik zrzutu z serwerem MySQL. Jeśli plik zrzutu jest duży, przekaż go do maszyny wirtualnej platformy Azure w tym samym regionie co serwer repliki. Przywróć ją do bazy danych MySQL serwera Azure z poziomu maszyny wirtualnej.

## <a name="link-primary-and-replica-servers-to-start-data-in-replication"></a>Łącze podstawowym i serwerem funkcji replica można uruchomić replikacji danych w

1. Serwer podstawowy zestaw

   Wszystkie funkcje replikacji w danych są wykonywane przez procedury składowane. Możesz znaleźć wszystkie procedury na [w danych przechowywane procedury replikacji](reference-data-in-stored-procedures.md). Procedury składowane można uruchomić w powłoce MySQL lub MySQL Workbench. 

   Aby połączyć dwóch serwerów i Rozpocznij replikację, logowanie do docelowego serwera repliki w usłudze Azure DB usługi MySQL i ustaw wystąpienia zewnętrznego jako serwer podstawowy. Jest to zrobić za pomocą `mysql.az_replication_change_primary` procedurę składowaną dla bazy danych Azure dla serwera MySQL.

   ```sql
   CALL mysql.az_replication_change_primary('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: Nazwa hosta serwera podstawowego
   - master_user: nazwa użytkownika dla głównego serwera
   - master_password: hasło dla serwera podstawowego
   - master_log_file: Nazwa pliku dziennika binarne uruchamiania `show master status`
   - master_log_pos: położenie dziennik binarny uruchamianie `show master status`
   - master_ssl_ca: kontekstu certyfikatu urzędu certyfikacji. Jeśli nie używa protokołu SSL, przekazać pusty ciąg.
       - Zalecane jest przekazanie parametru w jako zmienną. Poniżej przedstawiono przykłady, aby uzyskać więcej informacji.

   **Przykłady**

   *Replikacja z użyciem protokołu SSL*

   Zmienna `@cert` jest tworzona po uruchomieniu następujących poleceń MySQL: 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   Skonfigurowano replikacji przy użyciu protokołu SSL między podstawowy serwer hostowanej w domenie "FirmaA.com" i serwer repliki hostowanych w bazie danych Azure dla programu MySQL. Ta procedura składowana jest uruchamiane w replice. 

   ```sql
   CALL mysql.az_replication_change_primary('primary.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
   ```
   *Replikacji bez zastosowania protokołu SSL*

   Skonfigurowano replikacji bez zastosowania protokołu SSL między podstawowy serwer hostowanej w domenie "FirmaA.com" i serwer repliki hostowanych w bazie danych Azure dla programu MySQL. Ta procedura składowana jest uruchamiane w replice.

   ```sql
   CALL mysql.az_replication_change_primary('primary.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
   ```

2. Rozpocznij replikację

   Wywołanie `mysql.az_replication_start` inicjują replikację procedury składowanej.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Sprawdź stan replikacji

   Wywołanie [ `show slave status` ](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) polecenia na serwerze repliki, aby wyświetlić stan replikacji.
    
   ```sql
   show slave status;
   ```

   Jeśli stan `Slave_IO_Running` i `Slave_SQL_Running` są "tak", a wartość `Seconds_Behind_Master` ma wartość "0", replikacja działa prawidłowo. `Seconds_Behind_Master` Wskazuje wielkość opóźnienia repliki. Jeśli nie jest wartość "0", oznacza to, że replika jest przetwarzanie aktualizacji. 

## <a name="other-stored-procedures"></a>Inne procedury składowane

### <a name="stop-replication"></a>Zatrzymywanie replikacji

Aby zatrzymać replikację między serwerem podstawowym i repliki, należy użyć następującej procedury składowanej:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Usuń relację replikacji

Aby usunąć relacji między serwerem podstawowym i repliki, należy użyć następującej procedury składowanej:

```sql
CALL mysql.az_replication_remove_primary;
```

### <a name="skip-replication-error"></a>Pomiń błąd replikacji

Aby pominąć błąd replikacji i umożliwić replikacji kontynuować, należy użyć następującej procedury składowanej:
    
```sql
CALL mysql.az_replication_skip_counter;
```