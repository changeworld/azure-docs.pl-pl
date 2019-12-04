---
title: Konfigurowanie replikacji danych — Azure Database for MySQL
description: W tym artykule opisano sposób konfigurowania replikacja typu data-in Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: eaebcf50084223e1c1f4df30294bece96cffda6d
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74774300"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Jak skonfigurować replikacja typu data-in Azure Database for MySQL

W tym artykule opisano sposób konfigurowania replikacja typu data-in w usłudze Azure Database for MySQL przez skonfigurowanie serwerów głównych i replik. Replikacja typu data-in pozwala synchronizować dane z głównego serwera MySQL działającego lokalnie, na maszynach wirtualnych lub usług baz danych hostowanych przez innych dostawców chmury w replice w usłudze Azure Database for MySQL. 

W tym artykule założono, że masz co najmniej Niektóre wcześniejsze środowisko z serwerami i bazami danych MySQL.

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>Utwórz serwer MySQL, który ma być używany jako replika

1. Utwórz nowy serwer Azure Database for MySQL

   Utwórz nowy serwer MySQL (np. "replica.mysql.database.azure.com"). Zapoznaj się z tematem [Tworzenie serwera Azure Database for MySQL przy użyciu Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md) do tworzenia serwera. Ten serwer jest serwerem "Replica" w replikacja typu data-in.

   > [!IMPORTANT]
   > Serwer Azure Database for MySQL należy utworzyć w warstwach cenowych Ogólnego przeznaczenia lub zoptymalizowanych pod kątem pamięci.
   > 

2. Tworzenie tych samych kont użytkowników i odpowiednich uprawnień

   Konta użytkowników nie są replikowane z serwera głównego do serwera repliki. Jeśli planujesz udostępnienie użytkownikom dostępu do serwera repliki, musisz ręcznie utworzyć wszystkie konta i odpowiednie uprawnienia na tym nowo utworzonym Azure Database for MySQL serwerze.

## <a name="configure-the-master-server"></a>Konfigurowanie serwera głównego
Poniższe kroki dotyczą przygotowania i skonfigurowania serwera MySQL hostowanego lokalnie, na maszynie wirtualnej lub usługi bazy danych hostowanej przez innych dostawców chmury dla replikacja typu data-in. Ten serwer jest "głównym" w replikacji danych. 

1. Włącz rejestrowanie binarne

   Sprawdź, czy na serwerze głównym włączono rejestrowanie binarne, uruchamiając następujące polecenie: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Jeśli zmienna [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) jest zwracana z wartością "on", rejestrowanie binarne jest włączone na serwerze. 

   Jeśli `log_bin` jest zwracana z wartością "OFF", Włącz rejestrowanie plików binarnych, edytując plik my. cnf tak, aby `log_bin=ON` i ponownie uruchomić serwer, aby zmiany zaczęły obowiązywać.

2. Ustawienia serwera głównego

   Replikacja typu data-in wymaga spójności parametrów `lower_case_table_names` między serwerami głównymi i replikami. Domyślnie ten parametr jest 1 w Azure Database for MySQL. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Utwórz nową rolę replikacji i skonfiguruj uprawnienie

   Utwórz konto użytkownika na serwerze głównym, który jest skonfigurowany z uprawnieniami replikacji. Można to zrobić za pomocą poleceń SQL lub narzędzi, takich jak MySQL Workbench. Należy rozważyć, czy ma być przeprowadzana replikacja przy użyciu protokołu SSL, ponieważ należy ją określić podczas tworzenia użytkownika. Zapoznaj się z dokumentacją programu MySQL, aby dowiedzieć się, jak [dodać konta użytkowników](https://dev.mysql.com/doc/refman/5.7/en/adding-users.html) na serwerze głównym. 

   W poniższych poleceniach Nowa rola replikacji jest w stanie uzyskać dostęp do serwera głównego z dowolnego komputera, a nie tylko z komputera, który hostuje wzorzec. W tym celu należy określić "syncuser@"% "w poleceniu Create User. Zapoznaj się z dokumentacją programu MySQL, aby dowiedzieć się więcej o [określaniu nazw kont](https://dev.mysql.com/doc/refman/5.7/en/account-names.html).

   **Polecenie SQL**

   *Replikacja przy użyciu protokołu SSL*

   Aby wymagać protokołu SSL dla wszystkich połączeń użytkowników, użyj następującego polecenia, aby utworzyć użytkownika: 

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *Replikacja bez protokołu SSL*

   Jeśli protokół SSL nie jest wymagany dla wszystkich połączeń, użyj następującego polecenia, aby utworzyć użytkownika:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **Workbench MySQL**

   Aby utworzyć rolę replikacji w programie MySQL Workbench, Otwórz panel **Użytkownicy i uprawnienia** w panelu **zarządzania** . Następnie kliknij pozycję **Dodaj konto**. 
 
   ![Użytkownicy i uprawnienia](./media/howto-data-in-replication/users_privileges.png)

   Wpisz nazwę użytkownika w polu **Nazwa logowania** . 

   ![Użytkownik synchronizacji](./media/howto-data-in-replication/syncuser.png)
 
   Kliknij panel **role administracyjne** , a następnie wybierz pozycję **replikacja podrzędna** z listy **uprawnień globalnych**. Następnie kliknij przycisk **Zastosuj** , aby utworzyć rolę replikacji.

   ![Replikacja podrzędna](./media/howto-data-in-replication/replicationslave.png)


4. Ustaw serwer główny do trybu tylko do odczytu

   Przed rozpoczęciem zrzutu bazy danych należy umieścić serwer w trybie tylko do odczytu. W trybie tylko do odczytu, główny nie będzie w stanie przetwarzać żadnych transakcji zapisu. Oceń wpływ na swoją firmę i w razie potrzeby Zaplanuj okno tylko do odczytu w czasie poza godzinami szczytu.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Pobieranie binarnej nazwy pliku dziennika i przesunięcia

   Uruchom [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) polecenie, aby określić bieżącą nazwę pliku dziennika binarnego i przesunięcia.
    
   ```sql
   show master status;
   ```
   Wyniki powinny wyglądać jak poniżej. Pamiętaj, aby zanotować nazwę pliku binarnego, ponieważ zostanie ona użyta w kolejnych krokach.

   ![Wyniki stanu głównego](./media/howto-data-in-replication/masterstatus.png)
 
## <a name="dump-and-restore-master-server"></a>Zrzuć i przywróć serwer główny

1. Zrzuć wszystkie bazy danych z serwera głównego

   Możesz użyć mysqldump, aby zrzucić bazy danych z serwera głównego. Aby uzyskać szczegółowe informacje, zobacz [zrzut & przywracanie](concepts-migrate-dump-restore.md). Nie jest konieczne zrzucanie biblioteki MySQL i biblioteki testowej.

2. Ustaw serwer główny w trybie odczytu/zapisu

   Po zrzucie bazy danych Zmień serwer główny MySQL z powrotem na tryb odczytu/zapisu.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Przywróć plik zrzutu na nowy serwer

   Przywróć plik zrzutu do serwera utworzonego w usłudze Azure Database for MySQL. Zapoznaj się z artykułem [zrzut & Przywróć](concepts-migrate-dump-restore.md) , aby przywrócić plik zrzutu do serwera MySQL. Jeśli plik zrzutu jest duży, przekaż go do maszyny wirtualnej na platformie Azure w tym samym regionie, w którym znajduje się serwer repliki. Przywróć go do serwera Azure Database for MySQL z maszyny wirtualnej.

## <a name="link-master-and-replica-servers-to-start-data-in-replication"></a>Łączenie serwerów głównych i replik w celu uruchomienia replikacja typu data-in

1. Ustaw serwer główny

   Wszystkie funkcje replikacja typu data-in są wykonywane przez procedury składowane. Wszystkie procedury można znaleźć w [replikacja typu Data-in procedurach składowanych](reference-data-in-stored-procedures.md). Procedury składowane można uruchamiać w programie MySQL Shell lub MySQL Workbench. 

   Aby połączyć dwa serwery i rozpocząć replikację, zaloguj się do docelowego serwera repliki w usłudze Azure DB dla MySQL i ustaw wystąpienie zewnętrzne jako serwer główny. W tym celu należy użyć procedury składowanej `mysql.az_replication_change_master` na serwerze usługi Azure DB dla programu MySQL.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: Nazwa hosta serwera głównego
   - master_user: Nazwa użytkownika serwera głównego
   - master_password: hasło dla serwera głównego
   - master_log_file: nie uruchomiono binarnej nazwy pliku dziennika `show master status`
   - master_log_pos: uruchamianie binarnej lokalizacji dziennika `show master status`
   - master_ssl_ca: kontekst certyfikatu urzędu certyfikacji. Jeśli nie korzystasz z protokołu SSL, Przekaż pusty ciąg.
       - Zalecane jest, aby przekazać ten parametr jako zmienną. Więcej informacji można znaleźć w poniższych przykładach.

> [!NOTE]
> Jeśli serwer główny jest hostowany na maszynie wirtualnej platformy Azure, ustaw opcję "Zezwalaj na dostęp do usług platformy Azure" na wartość "włączone", aby umożliwić serwerom głównym i replice komunikację ze sobą. To ustawienie można zmienić przy użyciu opcji **zabezpieczeń połączenia** . Aby uzyskać więcej informacji, zapoznaj się z tematem [Zarządzanie regułami zapory przy użyciu portalu](howto-manage-firewall-using-portal.md) .

   **Przykłady**

   *Replikacja przy użyciu protokołu SSL*

   Zmienna `@cert` jest tworzona przez uruchomienie następujących poleceń MySQL: 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   Replikacja przy użyciu protokołu SSL jest skonfigurowana między serwerem głównym hostowanym w domenie "companya.com" i serwerem repliki hostowanym w Azure Database for MySQL. Ta procedura składowana jest uruchamiana w replice. 

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
   ```
   *Replikacja bez protokołu SSL*

   Replikacja bez protokołu SSL jest skonfigurowana między serwerem głównym hostowanym w domenie "companya.com" i serwerem repliki hostowanym w Azure Database for MySQL. Ta procedura składowana jest uruchamiana w replice.

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
   ```

1. Rozpocznij replikację

   Wywołaj procedurę składowaną `mysql.az_replication_start`, aby zainicjować replikację.

   ```sql
   CALL mysql.az_replication_start;
   ```

1. Sprawdź stan replikacji

   Wywołaj polecenie [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) na serwerze repliki, aby wyświetlić stan replikacji.
    
   ```sql
   show slave status;
   ```

   Jeśli stan `Slave_IO_Running` i `Slave_SQL_Running` to "yes", a wartość `Seconds_Behind_Master` to "0", replikacja działa prawidłowo. `Seconds_Behind_Master` wskazuje, jak późna jest replika. Jeśli wartość nie jest równa "0", oznacza to, że replika przetwarza aktualizacje. 

## <a name="other-stored-procedures"></a>Inne procedury składowane

### <a name="stop-replication"></a>Zatrzymywanie replikacji

Aby zatrzymać replikację między serwerem głównym a programem repliki, należy użyć następującej procedury składowanej:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Usuń relację replikacji

Aby usunąć relację między serwerem głównym a programem repliki, należy użyć następującej procedury składowanej:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>Pomiń błąd replikacji

Aby pominąć błąd replikacji i umożliwić wykonanie replikacji, należy użyć następującej procedury składowanej:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej na temat [replikacja typu data-in](concepts-data-in-replication.md) Azure Database for MySQL. 
