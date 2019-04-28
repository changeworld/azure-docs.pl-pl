---
title: Konfigurowanie replikacji danych w celu replikowania danych do usługi Azure Database for MySQL.
description: W tym artykule opisano sposób konfigurowania danych replikacji dla usługi Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 08/31/2018
ms.openlocfilehash: 7d56d7f8fcbd53d4f69863d260591ef80f3d7188
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459083"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Jak skonfigurować bazę danych Azure do replikacji danych MySQL

W tym artykule dowiesz się, sposobu konfigurowania replikacji danych w usłudze Azure Database for MySQL — usługi przez konfigurowanie serwerów głównego i repliki. Replikacji danych umożliwia synchronizowanie danych z głównego serwera MySQL lokalnie, w maszynach wirtualnych lub bazy danych usług hostowanych przez innych dostawców rozwiązań w chmurze do repliki w usługa Azure Database for MySQL. 

W tym artykule założono, że masz co najmniej pewne doświadczenie z serwerów MySQL i bazami danych.

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>Tworzenie serwera MySQL, który ma być używany jako repliki

1. Tworzenie nowego serwera Azure Database for MySQL

   Tworzenie nowego serwera MySQL (np.) "replica.mysql.database.azure.com"). Zapoznaj się [tworzenia serwera Azure Database for MySQL za pomocą witryny Azure portal](quickstart-create-mysql-server-database-using-azure-portal.md) tworzenia serwera. Ten serwer jest serwerem "replica" w replikacji danych.

   > [!IMPORTANT]
   > Azure Database for MySQL server musi zostać utworzona w warstw cenowych ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci.
   > 

2. Tworzenie tych samych kont użytkowników i odpowiednie uprawnienia.

   Konta użytkowników nie są replikowane na serwer repliki z serwera głównego. Jeśli planujesz, zapewniając użytkownikom dostęp do serwera repliki, należy ręcznie utworzyć wszystkich kont i odpowiednie uprawnienia na tę nowo utworzoną — Azure Database dla serwera MySQL.

## <a name="configure-the-master-server"></a>Konfigurowanie serwera głównego
Poniższe kroki Przygotuj i skonfiguruj MySQL server hostowanych lokalnie, w maszynę wirtualną lub usługę bazy danych pracujących w chmurze innych dostawców dla replikacji danych. Ten serwer jest "główną" w replikacji danych. 

1. Włącz rejestrowanie binarne

   Sprawdź, jeśli włączono rejestrowania binarnego na wzorcu, uruchamiając następujące polecenie: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Jeśli zmienna [ `log_bin` ](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) jest zwracany za pomocą wartości "ON", rejestrowania binarnego jest włączona na tym serwerze. 

   Jeśli `log_bin` jest zwracany za pomocą wartości "OFF", Włącz rejestrowanie, edytując plik my.cnf, więc plik binarny, `log_bin=ON` i uruchomić ponownie serwer, aby zmiana zaczęła obowiązywać.

2. Ustawienia serwera głównego

   Replikacji danych wymaga, aby parametr `lower_case_table_names` będzie spójny między serwerami głównego i repliki. Tego parametru to 1 domyślnie w usłudze Azure Database for MySQL. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Utwórz nową rolę replikacji i ustaw uprawnienia

   Utwórz konto użytkownika na serwerze głównym, który jest skonfigurowany z uprawnieniami replikacji. Można to zrobić za pomocą poleceń SQL lub narzędzia, takiego jak połączenia aplikacji MySQL Workbench. Należy rozważyć, czy planujesz replikacji przy użyciu protokołu SSL, ponieważ należy określić podczas tworzenia użytkownika. Zapoznaj się z dokumentacją MySQL, aby zrozumieć, jak [dodać konta użytkowników](https://dev.mysql.com/doc/refman/5.7/en/adding-users.html) na serwerze głównym. 

   W poniższych poleceń uzyskiwać dostęp do wzorca z dowolnej maszyny, a nie tylko komputer, na którym znajduje się sam wzorzec jest nowa rola replikacji, utworzone. Jest to realizowane przez określenie "syncuser@'%" "w poleceniu Utwórz użytkownika. Zapoznaj się dokumentacją MySQL, aby dowiedzieć się więcej na temat [określania nazwy kont](https://dev.mysql.com/doc/refman/5.7/en/account-names.html).

   **Polecenie SQL**

   *Replikacja za pomocą protokołu SSL*

   Aby wymagać protokołu SSL dla wszystkich połączeń użytkownika, użyj następującego polecenia, aby utworzyć użytkownika: 

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *Replikacji bez protokołu SSL*

   Jeśli protokół SSL nie jest wymagana dla wszystkich połączeń, użyj następującego polecenia, aby utworzyć użytkownika:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **Aplikację MySQL Workbench**

   Aby utworzyć rolę replikacji w aplikacji MySQL Workbench, otwórz **użytkowników i uprawnień** panelu z **zarządzania** panelu. Następnie kliknij pozycję **Dodaj konto**. 
 
   ![Użytkownicy i uprawnienia](./media/howto-data-in-replication/users_privileges.png)

   Wpisz nazwę w **nazwa logowania** pola. 

   ![Użytkownik synchronizacji](./media/howto-data-in-replication/syncuser.png)
 
   Kliknij pozycję **ról administracyjnych** panelu, a następnie wybierz pozycję **podrzędny replikacji** z listy **uprawnień globalnych**. Następnie kliknij pozycję **Zastosuj** tworzenie roli replikacji.

   ![W przypadku replikacji](./media/howto-data-in-replication/replicationslave.png)


4. Ustaw główny serwer do trybu tylko do odczytu

   Przed rozpoczęciem zrzutu na poziomie bazy danych, serwer musi być umieszczone w trybie tylko do odczytu. W trybie tylko do odczytu, nie można przetworzyć żadnych transakcji zapisu będzie wzorca. Oceń wpływ na prowadzoną działalność i zaplanowania okna tylko do odczytu w poza godzinami szczytu w razie potrzeby.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Nazwa pliku binarnego dziennika i przesunięcia

   Uruchom [ `show master status` ](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) polecenie, aby określić bieżącą nazwę pliku dziennik binarny i przesunięcie.
    
   ```sql
   show master status;
   ```
   Wyniki powinny być podobną do poniższej. Koniecznie Zanotuj nazwę pliku binarnego, ponieważ zostaną użyte w kolejnych krokach.

   ![Wzorzec stan wyników](./media/howto-data-in-replication/masterstatus.png)
 
## <a name="dump-and-restore-master-server"></a>Zrzucanie i przywracanie serwera głównego

1. Zrzut wszystkich baz danych z serwera głównego

   Z głównej usługi, można użyć polecenia mysqldump zrzutu baz danych. Aby uzyskać szczegółowe informacje, zapoznaj się [zrzutu i przywracania](concepts-migrate-dump-restore.md). Nie jest konieczne do zrzutu MySQL biblioteki i biblioteki testowej.

2. Ustaw serwer główny w trybie odczytu/zapisu

   Gdy baza danych została zrzucany, zmienić wzorzec MySQL server wstecz do odczytu/zapisu trybu.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Przywróć plik zrzutu na nowy serwer

   Przywróć plik zrzutu do serwera utworzonego w usługa Azure Database for MySQL. Zapoznaj się [zrzutu i przywracania](concepts-migrate-dump-restore.md) na sposób przywracania pliku zrzutu do serwera MySQL. Jeśli plik zrzutu jest duży, przekaż go do maszyny wirtualnej na platformie Azure, w tym samym regionie co serwer repliki. Go przywrócić do usługi Azure Database for MySQL server z maszyny wirtualnej.

## <a name="link-master-and-replica-servers-to-start-data-in-replication"></a>Połącz serwery główne i repliki można uruchomić replikacji danych

1. Serwer główny zestaw

   Wszystkie funkcje replikacji danych są wykonywane tylko przez procedury składowane. Możesz znaleźć wszystkie procedury na [w danych replikacji procedur składowanych](reference-data-in-stored-procedures.md). Procedury składowane mogą być uruchamiane w powłoce MySQL lub połączenia aplikacji MySQL Workbench. 

   Aby połączyć dwa serwery i Rozpocznij replikację, zaloguj się do serwera docelowego repliki w usłudze Azure DB dla MySQL, usługi i ustaw wystąpienia zewnętrznego jako serwer główny. Jest to wykonywane przy użyciu `mysql.az_replication_change_master` procedury składowanej w usłudze Azure DB dla serwera MySQL.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: hostname of the master server
   - master_user: nazwa użytkownika dla serwera głównego
   - master_password: hasło dla tego serwera głównego
   - master_log_file: Nazwa pliku dziennika binarne uruchamiania `show master status`
   - master_log_pos: pozycja dziennik binarny uruchamianie `show master status`
   - master_ssl_ca: Kontekst certyfikatu urzędu certyfikacji. Jeśli nie używasz protokołu SSL, należy przekazać pusty ciąg.
       - Zalecane jest przekazanie tego parametru w jako zmienną. Poniżej przedstawiono przykłady, aby uzyskać więcej informacji.

> [!NOTE]
> Jeśli serwer główny znajduje się w Maszynie wirtualnej platformy Azure, należy ustawić, "Zezwalaj na dostęp do usług platformy Azure" na "ON", aby zezwolić na serwerach głównego i repliki, aby komunikować się ze sobą. Ustawienie to można zmienić z **zabezpieczenia połączeń** opcje. Zapoznaj się [zarządzanie regułami zapory za pomocą portalu](howto-manage-firewall-using-portal.md) Aby uzyskać więcej informacji.

   **Przykłady**

   *Replikacja za pomocą protokołu SSL*

   Zmienna `@cert` jest tworzony, uruchamiając następujące polecenia MySQL: 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   Skonfigurowano replikację za pomocą protokołu SSL między głównym serwerem hostowanej w domenie "FirmaA.com" i serwerem repliki, hostowana w usłudze Azure Database for MySQL. Ta procedura składowana jest uruchamiany w replice. 

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
   ```
   *Replikacji bez protokołu SSL*

   Skonfigurowano replikację bez zastosowania protokołu SSL między głównym serwerem hostowanej w domenie "FirmaA.com" i serwerem repliki, hostowana w usłudze Azure Database for MySQL. Ta procedura składowana jest uruchamiany w replice.

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
   ```

1. Rozpocznij replikację

   Wywołaj `mysql.az_replication_start` przechowywane procedury, aby zainicjować replikacji.

   ```sql
   CALL mysql.az_replication_start;
   ```

1. Sprawdź stan replikacji

   Wywołaj [ `show slave status` ](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) polecenia na serwerze repliki tak, aby wyświetlić stan replikacji.
    
   ```sql
   show slave status;
   ```

   Jeśli stan `Slave_IO_Running` i `Slave_SQL_Running` "yes" i wartość `Seconds_Behind_Master` "0", replikacja działa prawidłowo. `Seconds_Behind_Master` Wskazuje wielkość opóźnienia jest repliką. Jeśli wartość nie jest "0", oznacza to, że replika jest przetwarzanie aktualizacji. 

## <a name="other-stored-procedures"></a>Inne procedury składowane

### <a name="stop-replication"></a>Zatrzymaj replikację

Aby zatrzymać replikację między serwera głównego i repliki, użyj następującej procedury składowanej:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Usuń relację replikacji

Aby usunąć relację między serwera głównego i repliki, użyj następującej procedury składowanej:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>Pomiń błąd replikacji

Aby pominąć błąd replikacji i zezwolenie na replikację kontynuować, należy użyć następującej procedury składowanej:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [replikacji danych](concepts-data-in-replication.md) dla usługi Azure Database for MySQL. 
