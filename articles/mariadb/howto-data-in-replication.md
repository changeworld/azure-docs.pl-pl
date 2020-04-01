---
title: Konfigurowanie replikacji danych — usługa Azure Database dla bazy danych MariaDB
description: W tym artykule opisano sposób konfigurowania replikacji danych w usłudze Azure Database dla mariadb.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/30/2020
ms.openlocfilehash: 332feffead74174ba0b9b278d8de1c5957d5b9e6
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422469"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>Konfigurowanie replikacji danych w bazie danych platformy Azure dla bazy danych MariaDB

W tym artykule opisano sposób konfigurowania replikacji danych w bazie danych platformy Azure dla bazy danych MariaDB przez skonfigurowanie serwerów głównych i replik. W tym artykule założono, że masz pewne wcześniejsze doświadczenie z mariadb serwerów i baz danych.

Aby utworzyć replikę w usłudze Azure Database for MariaDB, usługa Replikacja danych w synchronizacji synchronizuje dane z głównego serwera MariaDB lokalnie, na maszynach wirtualnych (VM) lub w usługach bazy danych w chmurze.

Przejrzyj [ograniczenia i wymagania](concepts-data-in-replication.md#limitations-and-considerations) replikacji danych przed wykonaniem kroków opisanych w tym artykule.

> [!NOTE]
> Jeśli serwer główny jest w wersji 10.2 lub nowszej, zaleca się skonfigurowanie replikacji danych przy użyciu [globalnego identyfikatora transakcji](https://mariadb.com/kb/en/library/gtid/).


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>Tworzenie serwera MariaDB do użycia jako replika

1. Utwórz nową usługę Azure Database dla serwera MariaDB (na przykład replica.mariadb.database.azure.com). Serwer jest serwerem repliki w replikacji danych.

    Aby dowiedzieć się więcej o tworzeniu serwera, zobacz [Tworzenie usługi Azure Database dla serwera MariaDB przy użyciu portalu Azure.](quickstart-create-mariadb-server-database-using-azure-portal.md)

   > [!IMPORTANT]
   > Należy utworzyć usługę Azure Database for MariaDB serwera w ogólnego przeznaczenia lub pamięci zoptymalizowane warstwy cenowe.

2. Utwórz identyczne konta użytkowników i odpowiadające im uprawnienia.
    
    Konta użytkowników nie są replikowane z serwera głównego na serwer repliki. Aby zapewnić użytkownikowi dostęp do serwera repliki, należy ręcznie utworzyć wszystkie konta i odpowiednie uprawnienia na nowo utworzonym serwerze usługi Azure Database dla mariadb.

3. Dodaj adres IP serwera głównego do reguł zapory repliki. 

   Zaktualizuj reguły zapory za pomocą [witryny Azure Portal](howto-manage-firewall-portal.md) lub [interfejsu wiersza polecenia platformy Azure](howto-manage-firewall-cli.md).

## <a name="configure-the-master-server"></a>Konfigurowanie serwera głównego

Poniższe kroki należy przygotować i skonfigurować serwer MariaDB hostowany lokalnie, na maszynie Wirtualnej lub w usłudze bazy danych w chmurze dla replikacji danych. Serwer MariaDB jest wzorcem replikacji danych.

1. Przed kontynuowaniem zapoznaj się z [wymaganiami serwera głównego.](concepts-data-in-replication.md#requirements) 

   Na przykład upewnij się, że serwer główny zezwala zarówno na ruch przychodzący, jak i wychodzący na porcie 3306 i że serwer główny ma **publiczny adres IP,** system DNS jest publicznie dostępny lub ma w pełni kwalifikowaną nazwę domeny (FQDN). 
   
   Przetestuj łączność z serwerem głównym, próbując połączyć się z narzędzia, takiego jak wiersz polecenia MySQL hostowanego na innym komputerze lub z [usługi Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) dostępnej w witrynie Azure portal.

2. Włącz rejestrowanie binarne.
    
    Aby sprawdzić, czy rejestrowanie binarne jest włączone na wzorcu, wprowadź następujące polecenie:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Jeśli zmienna [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) zwraca `ON`wartość, rejestrowanie binarne jest włączone na serwerze.

   Jeśli `log_bin` zwraca `OFF`wartość, edytuj plik **my.cnf** tak, aby `log_bin=ON` włączyć rejestrowanie binarne. Uruchom ponownie serwer, aby zmiana została uwzględnić.

3. Skonfiguruj ustawienia serwera głównego.

    Replikacja w danych wymaga, aby parametr `lower_case_table_names` był spójny między serwerami głównymi i replikami. Parametr `lower_case_table_names` jest domyślnie ustawiony `1` na usługę Azure Database dla mariadb.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

4. Utwórz nową rolę replikacji i skonfiguruj uprawnienia.

   Utwórz konto użytkownika na serwerze głównym skonfigurowanym z uprawnieniami replikacji. Konto można utworzyć za pomocą poleceń SQL lub mysql workbench. Jeśli planujesz replikację z SSL, należy to określić podczas tworzenia konta użytkownika.
   
   Aby dowiedzieć się, jak dodawać konta użytkowników na serwerze głównym, zobacz [dokumentację MariaDB](https://mariadb.com/kb/en/library/create-user/).

   Za pomocą następujących poleceń, nowa rola replikacji można uzyskać dostęp do wzorca z dowolnego komputera, a nie tylko na komputerze, który obsługuje sam wzorzec. Dla tego dostępu należy określić **syncuser\@"%"** w poleceniu, aby utworzyć użytkownika.
   
   Aby dowiedzieć się więcej o dokumentacji mariadb, zobacz [określanie nazw kont](https://mariadb.com/kb/en/library/create-user/#account-names).

   **Polecenie SQL**

   - Replikacja za pomocą ssl

       Aby wymagać protokołu SSL dla wszystkich połączeń użytkownika, wprowadź następujące polecenie, aby utworzyć użytkownika:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - Replikacja bez ssl

       Jeśli ssl nie jest wymagane dla wszystkich połączeń, wprowadź następujące polecenie, aby utworzyć użytkownika:
    
       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   Aby utworzyć rolę replikacji w mysql workbench, w okienku **Zarządzanie** wybierz pozycję **Użytkownicy i uprawnienia**. Następnie wybierz pozycję **Dodaj konto**.
 
   ![Użytkownicy i uprawnienia](./media/howto-data-in-replication/users_privileges.png)

   Wprowadź nazwę użytkownika w polu **Nazwa logowania.**

   ![Synchronizowanie użytkownika](./media/howto-data-in-replication/syncuser.png)
 
   Wybierz panel **Role administracyjne,** a następnie na liście **uprawnień globalnych**wybierz pozycję **Podrzędna replikacja**. Wybierz **przycisk Zastosuj,** aby utworzyć rolę replikacji.

   ![Slave replikacji](./media/howto-data-in-replication/replicationslave.png)


5. Ustaw serwer główny w trybie tylko do odczytu.

   Przed zrzutem bazy danych serwer musi być umieszczony w trybie tylko do odczytu. W trybie tylko do odczytu wzorzec nie może przetwarzać żadnych transakcji zapisu. Aby uniknąć wpływu na działalność, należy zaplanować okno tylko do odczytu poza szczytem.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

6. Pobierz bieżącą nazwę pliku dziennika binarnego i przesunięcie.

   Aby określić bieżącą nazwę pliku dziennika [`show master status`](https://mariadb.com/kb/en/library/show-master-status/)binarnego i przesunięcie, uruchom polecenie .
    
   ```sql
   show master status;
   ```
   Wyniki powinny być podobne do poniższej tabeli:
   
   ![Wyniki stanu głównego](./media/howto-data-in-replication/masterstatus.png)

   Zanotuj nazwę pliku binarnego, ponieważ będzie ona używana w późniejszych krokach.
   
7. Uzyskaj pozycję OWHID (opcjonalnie, potrzebną do replikacji za pomocą OWHID).

   Uruchom tę [`BINLOG_GTID_POS`](https://mariadb.com/kb/en/library/binlog_gtid_pos/) funkcję, aby uzyskać pozycję OWH dla odpowiedniej nazwy pliku binlog i odsunięcia.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-master-server"></a>Zrzut i przywracanie serwera głównego

1. Zrzuć wszystkie bazy danych z serwera głównego.

   Użyj mysqldump do zrzutu wszystkich baz danych z serwera głównego. Nie jest konieczne zrzucenie biblioteki MySQL i biblioteki testowej.

    Aby uzyskać więcej informacji, zobacz [Zrzut i przywracanie](howto-migrate-dump-restore.md).

2. Ustaw serwer główny na tryb odczytu/zapisu.

   Po zrzucie bazy danych należy zmienić główny serwer MariaDB z powrotem w tryb odczytu/zapisu.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Przywróć plik zrzutu na nowy serwer.

   Przywróć plik zrzutu na serwerze utworzonym w usłudze Azure Database for MariaDB. Zobacz [Dump & Restore,](howto-migrate-dump-restore.md) aby dowiedzieć się, jak przywrócić plik zrzutu na serwerze MariaDB.

   Jeśli plik zrzutu jest duży, przekaż go do maszyny Wirtualnej na platformie Azure w tym samym regionie co serwer repliki. Przywróć go do usługi Azure Database dla serwera MariaDB z maszyny Wirtualnej.

## <a name="link-the-master-and-replica-servers-to-start-data-in-replication"></a>Łączenie serwerów głównych i replik w celu uruchomienia replikacji danych

1. Ustaw serwer główny.

   Wszystkie funkcje replikacji danych są wykonywane przez procedury przechowywane. Wszystkie procedury można znaleźć w [procedurach przechowywanych replikacji danych](reference-data-in-stored-procedures.md). Procedury przechowywane można uruchamiać w powłoce MySQL lub w warsztacie MySQL.

   Aby połączyć dwa serwery i uruchomić replikację, zaloguj się do docelowego serwera replik w usłudze Azure DB dla MariaDB. Następnie ustaw wystąpienie zewnętrzne jako serwer `mysql.az_replication_change_master` główny `mysql.az_replication_change_master_with_gtid` przy użyciu procedury lub przechowywane na serwerze usługi Azure DB dla mariadb.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```
   
   lub
   
   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: nazwa hosta serwera głównego
   - master_user: nazwa użytkownika serwera głównego
   - master_password: hasło do serwera głównego
   - master_log_file: nazwa pliku dziennika binarnego z systemem`show master status`
   - master_log_pos: pozycja dziennika binarnego z systemem`show master status`
   - master_gtid_pos: Pozycja GTID z biegu`select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: Kontekst certyfikatu urzędu certyfikacji. Jeśli nie używasz SSL, przekaż pusty ciąg.*
    
    
    *Zalecamy przejście w parametrze master_ssl_ca jako zmienną. Aby uzyskać więcej informacji, zobacz poniższe przykłady.

   **Przykłady**

   - Replikacja za pomocą ssl

       Utwórz `@cert` zmienną, uruchamiając następujące polecenia:

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE\'S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       Replikacja z SSL jest instalowana między serwerem głównym hostowanym w domenie companya.com a serwerem replik hostowanym w usłudze Azure Database for MariaDB. Ta procedura składowana jest uruchamiana w replice.
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - Replikacja bez ssl

       Replikacja bez SSL jest instalowana między serwerem głównym hostowanym w domenie companya.com a serwerem replik hostowanym w usłudze Azure Database for MariaDB. Ta procedura składowana jest uruchamiana w replice.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. Rozpocznij replikację.

   Wywołanie `mysql.az_replication_start` procedury składowanej, aby rozpocząć replikację.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Sprawdź stan replikacji.

   Wywołanie [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) polecenia na serwerze repliki, aby wyświetlić stan replikacji.
    
   ```sql
   show slave status;
   ```

   Jeśli `Slave_IO_Running` `Slave_SQL_Running` i są `yes`w stanie , `Seconds_Behind_Master` `0`a wartość jest , replikacja działa. `Seconds_Behind_Master`wskazuje, jak późno replika jest. Jeśli wartość nie `0`jest , replika przetwarza aktualizacje.

4. Zaktualizuj odpowiednie zmienne serwera, aby replikacja danych była bezpieczniejsza (wymagana tylko do replikacji bez OWH).
    
    Ze względu na nanaty ograniczenie replikacji [`sync_master_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) [`sync_relay_log_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) w MariaDB, należy ustawić i zmienne w replikacji bez scenariusza GTID.

    Sprawdź serwer podrzędny `sync_master_info` `sync_relay_log_info` i zmienne, aby upewnić się, że replikacja data-in jest stabilna, i ustaw zmienne na `1`.
    
## <a name="other-stored-procedures"></a>Inne procedury składowane

### <a name="stop-replication"></a>Zatrzymywanie replikacji

Aby zatrzymać replikację między serwerem głównym a serwerem repliki, należy użyć następującej procedury składowanej:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>Usuwanie relacji replikacji

Aby usunąć relację między serwerem głównym a serwerem repliki, należy użyć następującej procedury składowanej:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>Pomiń błąd replikacji

Aby pominąć błąd replikacji i zezwolić na replikację, należy użyć następującej procedury składowanej:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [replikacji danych dla](concepts-data-in-replication.md) usługi Azure Database dla MariaDB.
