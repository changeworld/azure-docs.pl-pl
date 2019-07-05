---
title: Konfigurowanie replikacji danych w usłudze Azure Database dla serwera MariaDB | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania danych replikacji w usłudze Azure Database dla serwera MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 21e8a88cc6f03b4d54a6c5299b0b6be36cc32d6d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444804"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>Konfigurowanie replikacji danych w usłudze Azure Database dla serwera MariaDB

W tym artykule opisano sposób konfigurowania danych replikacji w usłudze Azure Database dla serwera MariaDB przez konfigurowanie serwerów głównego i repliki. W tym artykule założono, że ma pewne doświadczenie z MariaDB, serwerami i bazami danych.

Aby utworzyć replikę w usłudze Azure Database dla usługi MariaDB, replikacji danych synchronizuje dane z głównej MariaDB serwera lokalnie na maszynach wirtualnych (VM) lub bazy danych w usługach w chmurze.

> [!NOTE]
> Jeśli serwer główny jest wersji 10.2 lub nowszej, firma Microsoft zaleca, Konfigurowanie replikacji danych przy użyciu [globalny identyfikator transakcji](https://mariadb.com/kb/en/library/gtid/).


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>Tworzenie serwera MariaDB do użycia jako replika

1. Utwórz nową bazę danych Azure dla serwera MariaDB (na przykład replica.mariadb.database.azure.com). Serwer jest serwerem repliki w replikacji danych.

    Aby dowiedzieć się o Tworzenie serwera, zobacz [Tworzenie usługi Azure Database dla serwera MariaDB przy użyciu witryny Azure portal](quickstart-create-mariadb-server-database-using-azure-portal.md).

   > [!IMPORTANT]
   > Azure Database dla serwera MariaDB należy utworzyć w warstw cenowych ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci.

2. Tworzenie kont użytkowników identyczne i odpowiednie uprawnienia.
    
    Konta użytkowników nie są replikowane z serwera głównego na serwer repliki. Aby zapewnić użytkownikom dostęp do serwera repliki, należy ręcznie utworzyć na nowo utworzonej usługi Azure Database dla serwera MariaDB wszystkich kont i odpowiednie uprawnienia.

## <a name="configure-the-master-server"></a>Konfigurowanie serwera głównego

Poniższe kroki Przygotuj i skonfiguruj MariaDB server hostowanych lokalnie, na maszynie Wirtualnej lub usługi bazy danych w chmurze do replikacji danych. Serwer MariaDB jest wzorzec w replikacji danych.

1. Włącz rejestrowanie binarne.
    
    Aby sprawdzić, czy we wzorcu włączono rejestrowania binarnego, wprowadź następujące polecenie:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Jeśli zmienna [ `log_bin` ](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) zwraca wartość `ON`, rejestrowania binarnego jest włączona na tym serwerze.

   Jeśli `log_bin` zwraca wartość `OFF`, Edytuj **my.cnf** pliku tak, aby `log_bin=ON` włącza funkcję rejestrowania binarnego. Uruchom ponownie serwer, aby zmiany zaczęły obowiązywać.

2. Skonfiguruj ustawienia serwera głównego.

    Replikacji danych wymaga, aby parametr `lower_case_table_names` będzie spójny między serwerami głównego i repliki. `lower_case_table_names` Parametr ma wartość `1` domyślnie w usłudze Azure Database dla serwera MariaDB.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Tworzenie nowej roli replikacji i konfigurować uprawnienia.

   Utwórz konto użytkownika na serwerze głównym, który jest skonfigurowany z uprawnieniami replikacji. Aby utworzyć konto, należy za pomocą poleceń SQL lub MySQL Workbench. Jeśli planowana jest replikacja za pomocą protokołu SSL, należy określić podczas tworzenia konta użytkownika.
   
   Aby dowiedzieć się, jak dodać konta użytkowników na serwerze głównym, zobacz [dokumentacji MariaDB](https://mariadb.com/kb/en/library/create-user/).

   Za pomocą następujących poleceń, nowa rola replikacji można uzyskać dostęp do wzorca z dowolnego komputera, a nie tylko komputer, na którym znajduje się sam wzorzec. Ten dostęp, można określić **syncuser\@"%"** w poleceniu, aby utworzyć użytkownika.
   
   Aby dowiedzieć się więcej na temat dokumentacji MariaDB, zobacz [określania nazwy kont](https://mariadb.com/kb/en/library/create-user/#account-names).

   **Polecenie SQL**

   - Replikacja za pomocą protokołu SSL

       Aby wymagać protokołu SSL dla wszystkich połączeń użytkownika, wprowadź następujące polecenie, aby utworzyć użytkownika:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - Replikacji bez protokołu SSL

       Jeśli protokół SSL nie jest wymagane dla wszystkich połączeń, wprowadź następujące polecenie, aby utworzyć użytkownika:
    
       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **Aplikację MySQL Workbench**

   Aby utworzyć rolę replikacji w aplikacji MySQL Workbench w **zarządzania** okienku wybierz **użytkowników i uprawnień**. Następnie wybierz pozycję **Dodaj konto**.
 
   ![Użytkownicy i uprawnienia](./media/howto-data-in-replication/users_privileges.png)

   Wprowadź nazwę użytkownika w **nazwa logowania** pola.

   ![Użytkownik synchronizacji](./media/howto-data-in-replication/syncuser.png)
 
   Wybierz **ról administracyjnych** panelu, a następnie na liście **uprawnień globalnych**, wybierz opcję **podrzędny replikacji**. Wybierz **Zastosuj** tworzenie roli replikacji.

   ![W przypadku replikacji](./media/howto-data-in-replication/replicationslave.png)


4. Ustaw główny serwer do trybu tylko do odczytu.

   Zanim zrzut bazy danych serwera muszą być umieszczone w trybie tylko do odczytu. Podczas pracy w trybie tylko do odczytu, wzorzec nie może przetworzyć żadnych transakcji zapisu. Aby uniknąć wpływu na działalność biznesową, należy zaplanować okno tylko do odczytu podczas poza godzinami szczytu.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Pobieranie bieżącej nazwy pliku dziennik binarny i przesunięcie.

   Aby określić bieżącą nazwę pliku dziennik binarny i przesunięcie, uruchom polecenie [ `show master status` ](https://mariadb.com/kb/en/library/show-master-status/).
    
   ```sql
   show master status;
   ```
   Wyniki powinny wyglądać podobnie do poniższej tabeli:
   
   ![Wzorzec stan wyników](./media/howto-data-in-replication/masterstatus.png)

   Należy pamiętać nazwa pliku binarnego, ponieważ będzie ona używana w dalszych krokach.
   
6. Pobierz położenie GTID (opcjonalne, wymagana dla replikacji z GTID).

   Uruchom funkcję [ `BINLOG_GTID_POS` ](https://mariadb.com/kb/en/library/binlog_gtid_pos/) można uzyskać położenie GTID odpowiednia nazwa pliku binlog i przesunięcie.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-master-server"></a>Zrzucanie i przywracanie serwera głównego

1. Zrzut wszystkich baz danych z serwera głównego.

   Użyj polecenia mysqldump do porzucenia wszystkich baz danych z serwera głównego. Nie jest konieczne zrzutu biblioteki MySQL i biblioteki testowej.

    Aby uzyskać więcej informacji, zobacz [zrzutu i przywracania](howto-migrate-dump-restore.md).

2. Ustawić serwera głównego w trybie odczytu/zapisu.

   Po bazy danych zostały zrzucone, zmienić wzorzec kopii serwera MariaDB trybu odczytu/zapisu.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Przywróć plik zrzutu na nowy serwer.

   Przywróć plik zrzutu na serwerze, który został utworzony w usłudze Azure Database dla usługi MariaDB. Zobacz [zrzutu i przywracania](howto-migrate-dump-restore.md) dla serwera MariaDB Przywracanie pliku zrzutu.

   Jeśli plik zrzutu jest duży, przekaż go do maszyny Wirtualnej na platformie Azure, w tym samym regionie co serwer repliki. Go przywrócić do usługi Azure Database dla serwera MariaDB z maszyny Wirtualnej.

## <a name="link-the-master-and-replica-servers-to-start-data-in-replication"></a>Połącz serwery ze wzorca i repliki można uruchomić replikacji danych

1. Ustawić serwera głównego.

   Wszystkie funkcje replikacji danych są wykonywane tylko przez procedury składowane. Możesz znaleźć wszystkie procedury na [w danych replikacji procedur składowanych](reference-data-in-stored-procedures.md). Procedury składowane mogą być uruchamiane w powłoce MySQL lub połączenia aplikacji MySQL Workbench.

   Aby połączyć dwa serwery i Rozpocznij replikację, zaloguj się do docelowego serwera repliki w usłudze Azure DB dla usługi MariaDB. Następnym etapem jest skonfigurowanie wystąpienia zewnętrznego jako serwer główny przy użyciu `mysql.az_replication_change_master` lub `mysql.az_replication_change_master_with_gtid` procedury składowanej w usłudze Azure DB dla serwera MariaDB.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```
   
   lub
   
   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: hostname of the master server
   - master_user: nazwa użytkownika dla serwera głównego
   - master_password: hasło dla tego serwera głównego
   - master_log_file: Nazwa pliku dziennika binarne uruchamiania `show master status`
   - master_log_pos: pozycja dziennik binarny uruchamianie `show master status`
   - master_gtid_pos: Pozycja GTID uruchamianie `select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: Kontekst certyfikatu urzędu certyfikacji. Jeśli nie używasz protokołu SSL, Przekaż pustą string.*
    
    
    \* Firma Microsoft zaleca przekazywanie w parametrze master_ssl_ca jako zmienną. Aby uzyskać więcej informacji zobacz następujące przykłady.

   **Przykłady**

   - Replikacja za pomocą protokołu SSL

       Utwórz zmienną `@cert` , uruchamiając następujące polecenia:

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       Skonfigurowano replikację za pomocą protokołu SSL między głównym serwerem hostowanej w FirmaA.com domeny i serwer repliki, hostowana w usłudze Azure Database dla serwera MariaDB. Ta procedura składowana jest uruchamiany w replice.
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - Replikacji bez protokołu SSL

       Skonfigurowano replikację bez zastosowania protokołu SSL między głównym serwerem hostowanej w FirmaA.com domeny i serwer repliki, hostowana w usłudze Azure Database dla serwera MariaDB. Ta procedura składowana jest uruchamiany w replice.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. Uruchomienie replikacji.

   Wywołaj `mysql.az_replication_start` przechowywane procedury, aby uruchomić replikację.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Sprawdź stan replikacji.

   Wywołaj [ `show slave status` ](https://mariadb.com/kb/en/library/show-slave-status/) polecenia na serwerze repliki tak, aby wyświetlić stan replikacji.
    
   ```sql
   show slave status;
   ```

   Jeśli `Slave_IO_Running` i `Slave_SQL_Running` znajdują się w stanie `yes`i wartość `Seconds_Behind_Master` jest `0`, replikacja działa. `Seconds_Behind_Master` Wskazuje wielkość opóźnienia jest repliką. Jeśli wartość nie jest `0`, a następnie repliki jest przetwarzanie aktualizacji.

4. Zaktualizuj odpowiednie zmiennych serwera, aby dane replikacji bezpieczniejsze (wymagane tylko w przypadku replikacji bez GTID).
    
    Ze względu na ograniczenia replikacji natywne w MariaDB, należy ustawić [ `sync_master_info` ](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) i [ `sync_relay_log_info` ](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) zmiennych na replikacji bez scenariusza GTID.

    Sprawdź serwer podrzędny `sync_master_info` i `sync_relay_log_info` zmienne, aby upewnić się, jest replikacji danych czasu trwania stanu stabilnego, a następnie ustaw zmienne `1`.
    
## <a name="other-stored-procedures"></a>Inne procedury składowane

### <a name="stop-replication"></a>Zatrzymywanie replikacji

Aby zatrzymać replikację między serwera głównego i repliki, użyj następującej procedury składowanej:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>Usuń relację replikacji

Aby usunąć relację między serwera głównego i repliki, użyj następującej procedury składowanej:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>Pomiń błąd replikacji

Aby pominąć błąd replikacji i zezwolenie na replikację, użyj następującej procedury składowanej:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [replikacji danych](concepts-data-in-replication.md) dla usługi Azure Database dla serwera MariaDB.
