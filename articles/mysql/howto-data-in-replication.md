---
title: Konfigurowanie replikacji danych — usługa Azure Database dla mysql
description: W tym artykule opisano sposób konfigurowania replikacji danych dla usługi Azure Database dla mysql.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/27/2020
ms.openlocfilehash: 2148ce41267627d9d6e0437897a99a8dbdbe0746
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382770"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Jak skonfigurować usługę Azure Database dla replikacji danych MySQL

W tym artykule dowiesz się, jak skonfigurować replikację danych w usłudze Azure Database for MySQL, konfigurując serwery główne i repliki. Replikacja w danych umożliwia synchronizowanie danych z głównego serwera MySQL działającego lokalnie, na maszynach wirtualnych lub usługach bazy danych hostowanych przez innych dostawców chmury w replikę w usłudze Azure Database for MySQL. 

W tym artykule założono, że masz co najmniej pewne wcześniejsze doświadczenie z serwerami i bazami danych MySQL.

Przejrzyj [ograniczenia i wymagania](concepts-data-in-replication.md#limitations-and-considerations) replikacji danych przed wykonaniem kroków opisanych w tym artykule.

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>Tworzenie serwera MySQL, który ma być używany jako replika

1. Tworzenie nowej bazy danych platformy Azure dla serwera MySQL

   Utwórz nowy serwer MySQL (np. "replica.mysql.database.azure.com"). Zapoznaj się [z tworzeniem bazy danych platformy Azure dla serwera MySQL przy użyciu portalu Azure](quickstart-create-mysql-server-database-using-azure-portal.md) do tworzenia serwera. Ten serwer jest serwerem "repliki" w replikacji danych.

   > [!IMPORTANT]
   > Serwer usługi Azure Database for MySQL musi zostać utworzony w warstwach cenowych ogólnego przeznaczenia lub zoptymalizowanej pod kątem pamięci.
   > 

2. Tworzenie tych samych kont użytkowników i odpowiednich uprawnień

   Konta użytkowników nie są replikowane z serwera głównego na serwer repliki. Jeśli planujesz zapewnienie użytkownikom dostępu do serwera repliki, należy ręcznie utworzyć wszystkie konta i odpowiednie uprawnienia na tym nowo utworzonym serwerze usługi Azure Database for MySQL.

3. Dodaj adres IP serwera głównego do reguł zapory repliki. 

   Zaktualizuj reguły zapory za pomocą [witryny Azure Portal](howto-manage-firewall-using-portal.md) lub [interfejsu wiersza polecenia platformy Azure](howto-manage-firewall-using-cli.md).

## <a name="configure-the-master-server"></a>Konfigurowanie serwera głównego
Poniższe kroki należy przygotować i skonfigurować serwer MySQL hostowany lokalnie, na maszynie wirtualnej lub w usłudze bazy danych hostowanych przez innych dostawców chmury dla replikacji danych. Ten serwer jest "wzorcem" w replikacji data-in.


1. Przed kontynuowaniem zapoznaj się z [wymaganiami serwera głównego.](concepts-data-in-replication.md#requirements) 

   Na przykład upewnij się, że serwer główny zezwala zarówno na ruch przychodzący, jak i wychodzący na porcie 3306 i że serwer główny ma **publiczny adres IP,** system DNS jest publicznie dostępny lub ma w pełni kwalifikowaną nazwę domeny (FQDN). 
   
   Przetestuj łączność z serwerem głównym, próbując połączyć się z narzędzia, takiego jak wiersz polecenia MySQL hostowanego na innym komputerze lub z [usługi Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) dostępnej w witrynie Azure portal 

2. Włączanie rejestrowania binarnego

   Sprawdź, czy rejestrowanie binarne zostało włączone we wzorcu, uruchamiając następujące polecenie: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Jeśli zmienna [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) jest zwracana z wartością "ON", rejestrowanie binarne jest włączone na serwerze. 

   Jeśli `log_bin` zostanie zwrócona z wartością "OFF", włącz rejestrowanie binarne, `log_bin=ON` edytując plik my.cnf tak, aby i ponownie uruchomić serwer, aby zmiana została zaa tracona.

3. Ustawienia serwera głównego

   Replikacja w danych `lower_case_table_names` wymaga, aby parametr był spójny między serwerami głównymi i replikami. Ten parametr jest domyślnie 1 w usłudze Azure Database dla MySQL. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

4. Tworzenie nowej roli replikacji i konfigurowanie uprawnień

   Utwórz konto użytkownika na serwerze głównym skonfigurowanym z uprawnieniami replikacji. Można to zrobić za pomocą poleceń SQL lub narzędzia, takiego jak MySQL Workbench. Należy wziąć pod uwagę, czy planujesz replikowanie za pomocą SSL, ponieważ będzie to musiało zostać określone podczas tworzenia użytkownika. Zapoznaj się z dokumentacją MySQL, aby dowiedzieć się, jak [dodać konta użytkowników](https://dev.mysql.com/doc/refman/5.7/en/adding-users.html) na serwerze głównym. 

   W poniższych poleceniach utworzona nowa rola replikacji jest w stanie uzyskać dostęp do wzorca z dowolnego komputera, a nie tylko z komputera, na którym znajduje się sam wzorzec. Odbywa się to przez określenie "syncuser@'%" w poleceniu create user. Więcej informacji na temat [określania nazw kont](https://dev.mysql.com/doc/refman/5.7/en/account-names.html)można znaleźć w dokumentacji MySQL.

   **Polecenie SQL**

   *Replikacja za pomocą ssl*

   Aby wymagać protokołu SSL dla wszystkich połączeń użytkownika, użyj następującego polecenia, aby utworzyć użytkownika: 

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *Replikacja bez ssl*

   Jeśli ssl nie jest wymagane dla wszystkich połączeń, użyj następującego polecenia, aby utworzyć użytkownika:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   Aby utworzyć rolę replikacji w mysql workbench, otwórz panel **Użytkownicy i uprawnienia** z panelu **Zarządzanie.** Następnie kliknij **Dodaj konto**. 
 
   ![Użytkownicy i uprawnienia](./media/howto-data-in-replication/users_privileges.png)

   Wpisz nazwę użytkownika w polu **Nazwa logowania.** 

   ![Synchronizowanie użytkownika](./media/howto-data-in-replication/syncuser.png)
 
   Kliknij panel **Role administracyjne,** a następnie wybierz pozycję **Podrzędna replikacja** z listy **uprawnień globalnych**. Następnie kliknij **zastosuj,** aby utworzyć rolę replikacji.

   ![Slave replikacji](./media/howto-data-in-replication/replicationslave.png)


5. Ustawianie serwera głównego w trybie tylko do odczytu

   Przed rozpoczęciem zrzutu bazy danych serwer musi zostać umieszczony w trybie tylko do odczytu. W trybie tylko do odczytu wzorzec nie będzie mógł przetworzyć żadnych transakcji zapisu. Oceń wpływ na firmę i zaplanuj okno tylko do odczytu w czasie poza szczytem, jeśli to konieczne.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

6. Uzyskaj nazwę pliku dziennika binarnego i przesunięcie

   Uruchom [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) polecenie, aby określić bieżącą nazwę pliku dziennika binarnego i przesunięcie.
    
   ```sql
   show master status;
   ```
   Wyniki powinny być jak następujące. Pamiętaj, aby zanotować nazwę pliku binarnego, ponieważ będzie ona używana w późniejszych krokach.

   ![Wyniki stanu głównego](./media/howto-data-in-replication/masterstatus.png)
 
## <a name="dump-and-restore-master-server"></a>Zrzut i przywracanie serwera głównego

1. Zrzut wszystkich baz danych z serwera głównego

   Możesz użyć mysqldump do zrzutu baz danych ze swojego wzorca. Szczegółowe informacje można znaleźć w [pliku Dump & Restore](concepts-migrate-dump-restore.md). Nie ma potrzeby zrzutu biblioteki MySQL i biblioteki testowej.

2. Ustawianie serwera głównego w tryb odczytu/zapisu

   Gdy baza danych została zrzucona, zmień główny serwer MySQL z powrotem na tryb odczytu/zapisu.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Przywracanie pliku zrzutu na nowy serwer

   Przywróć plik zrzutu na serwerze utworzonym w usłudze Azure Database for MySQL. Zobacz [Dump & Restore,](concepts-migrate-dump-restore.md) aby dowiedzieć się, jak przywrócić plik zrzutu na serwerze MySQL. Jeśli plik zrzutu jest duży, przekaż go do maszyny wirtualnej na platformie Azure w tym samym regionie co serwer repliki. Przywróć go do usługi Azure Database dla serwera MySQL z maszyny wirtualnej.

## <a name="link-master-and-replica-servers-to-start-data-in-replication"></a>Łączenie serwerów głównych i replik w celu uruchomienia replikacji danych

1. Ustawianie serwera głównego

   Wszystkie funkcje replikacji danych są wykonywane przez procedury przechowywane. Wszystkie procedury można znaleźć w [procedurach przechowywanych replikacji danych](reference-data-in-stored-procedures.md). Procedury przechowywane można uruchomić w powłoce MySQL lub MySQL Workbench. 

   Aby połączyć dwa serwery i uruchomić replikację, zaloguj się do docelowego serwera replik w usłudze Azure DB for MySQL i ustaw wystąpienie zewnętrzne jako serwer główny. Odbywa się to `mysql.az_replication_change_master` przy użyciu procedury składowanej na serwerze usługi Azure DB dla mysql.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: nazwa hosta serwera głównego
   - master_user: nazwa użytkownika serwera głównego
   - master_password: hasło do serwera głównego
   - master_log_file: nazwa pliku dziennika binarnego z systemem`show master status`
   - master_log_pos: pozycja dziennika binarnego z systemem`show master status`
   - master_ssl_ca: Kontekst certyfikatu urzędu certyfikacji. Jeśli nie używasz SSL, przekaż pusty ciąg.
       - Zaleca się przekazać ten parametr jako zmienną. Zobacz poniższe przykłady, aby uzyskać więcej informacji.

> [!NOTE]
> Jeśli serwer główny jest obsługiwany w maszynie Wirtualnej platformy Azure, ustaw "Zezwalaj na dostęp do usług platformy Azure" na "ON", aby umożliwić serwerom głównym i replikom komunikowanie się ze sobą. To ustawienie można zmienić za 100 **000 000 000 000 000 000** Aby uzyskać więcej informacji, zapoznaj się [z programem Zarządzanie regułami zapory przy użyciu portalu.](howto-manage-firewall-using-portal.md)

   **Przykłady**

   *Replikacja za pomocą ssl*

   Zmienna `@cert` jest tworzona przez uruchomienie następujących poleceń MySQL: 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE'`S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   Replikacja z SSL jest instalowana między serwerem głównym hostowanym w domenie "companya.com" a serwerem replik hostowanym w usłudze Azure Database for MySQL. Ta procedura składowana jest uruchamiana w replice. 

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
   ```
   *Replikacja bez ssl*

   Replikacja bez SSL jest instalowana między serwerem głównym hostowanym w domenie "companya.com" a serwerem replik hostowanym w usłudze Azure Database for MySQL. Ta procedura składowana jest uruchamiana w replice.

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
   ```

1. Rozpocznij replikację

   Wywołanie `mysql.az_replication_start` procedury składowanej w celu zainicjowania replikacji.

   ```sql
   CALL mysql.az_replication_start;
   ```

1. Sprawdzanie stanu replikacji

   Wywołanie [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) polecenia na serwerze repliki, aby wyświetlić stan replikacji.
    
   ```sql
   show slave status;
   ```

   Jeśli stan `Slave_IO_Running` i `Slave_SQL_Running` są "tak", a `Seconds_Behind_Master` wartość jest "0", replikacja działa dobrze. `Seconds_Behind_Master`wskazuje, jak późno replika jest. Jeśli wartość nie jest "0", oznacza to, że replika przetwarza aktualizacje. 

## <a name="other-stored-procedures"></a>Inne procedury składowane

### <a name="stop-replication"></a>Zatrzymywanie replikacji

Aby zatrzymać replikację między serwerem głównym a serwerem repliki, należy użyć następującej procedury składowanej:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Usuwanie relacji replikacji

Aby usunąć relację między serwerem głównym a serwerem repliki, należy użyć następującej procedury składowanej:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>Pomiń błąd replikacji

Aby pominąć błąd replikacji i zezwolić na kontynuowanie replikacji, należy użyć następującej procedury składowanej:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [replikacji](concepts-data-in-replication.md) danych dla usługi Azure Database for MySQL. 
