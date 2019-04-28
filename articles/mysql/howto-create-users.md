---
title: Tworzenie użytkowników w usłudze Azure Database dla serwera MySQL
description: W tym artykule opisano sposób tworzenia nowych kont użytkowników do interakcji z serwerem Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 4cd2305ed3d7f88f6c3825d8f7cdb5d81f9a0f6b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61460188"
---
# <a name="create-users-in-azure-database-for-mysql-server"></a>Tworzenie użytkowników w usłudze Azure Database dla serwera MySQL 
W tym artykule opisano sposób tworzenia użytkowników w usłudze Azure Database dla serwera MySQL.

Podczas pierwszego utworzenia usługi Azure Database for MySQL, możesz podać nazwę użytkownika logowania administratora serwera i hasło. Aby uzyskać więcej informacji, możesz wykonać [Szybki Start](quickstart-create-mysql-server-database-using-azure-portal.md). Umożliwia zlokalizowanie Twoja nazwa logowania administratora serwera użytkownika w witrynie Azure portal.

Użytkownik Administrator serwera pobiera określone uprawnienia dla serwera zgodnie z opisem: WYBIERZ, WSTAWIANIA, AKTUALIZACJI, USUŃ, TWORZYĆ, PORZUCIĆ, ZAŁADUJ PONOWNIE, PRZETWARZAĆ, ODWOŁANIA, INDEX, ALTER, WYŚWIETL BAZY DANYCH, TWORZENIE TABEL TYMCZASOWYCH, ZABLOKOWAĆ TABEL, WYKONANIE, W PRZYPADKU REPLIKACJI, KLIENT REPLIKACJI, TWORZENIE WYŚWIETLIĆ, POKAŻ WIDOK, TWORZENIE PROCEDURY, ALTER PROCEDURY, TWORZENIE UŻYTKOWNIKA , ZDARZENIA WYZWALACZA

Po utworzeniu serwera Azure Database for MySQL umożliwia pierwszego konta użytkownika administratora serwera tworzenie dodatkowych użytkowników i udzielanie dostępu administratora do nich. Ponadto konta administratora serwera można utworzyć mniej uprzywilejowanych użytkowników, którzy mają dostęp do poszczególnych baz danych, schematów.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-mysql"></a>Jak utworzyć użytkowników dodatkowego administratora w usłudze Azure Database for MySQL
1. Pobierz nazwę użytkownika informacji i administratora połączenia.
   Aby można było nawiązać połączenie z serwerem bazy danych, potrzebne są pełna nazwa serwera i poświadczenia logowania administratora. Możesz łatwo odnaleźć nazwę serwera i informacje logowania z serwera **Przegląd** strony lub **właściwości** strony w witrynie Azure portal. 

2. Nawiązać połączenia z serwerem bazy danych, należy użyć konta administratora i hasła. Narzędzie Twojego preferowanego klienta, takich jak połączenia aplikacji MySQL Workbench, mysql.exe, HeidiSQL lub inne osoby. 
   Jeśli masz pewności, jak połączyć, zobacz [użyciu aplikacji MySQL Workbench do nawiązywania połączeń i wykonywanie zapytań dotyczących danych](./connect-workbench.md)

3. Poddaj edycji, a następnie uruchom poniższy kod SQL. Zamień na nową nazwę użytkownika, aby uzyskać wartość symbolu zastępczego `new_master_user`. Ta składnia przyznaje uprawnienia wymienione na schematy bazy danych (*.*) do nazwy użytkownika (new_master_user w tym przykładzie). 

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION; 
   
   FLUSH PRIVILEGES;
   ```

4. Sprawdź przyznaje 
   ```sql
   USE sys;
   
   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="how-to-create-database-users-in-azure-database-for-mysql"></a>Sposób tworzenia użytkowników bazy danych w usłudze Azure Database for MySQL

1. Pobierz nazwę użytkownika informacji i administratora połączenia.
   Aby można było nawiązać połączenie z serwerem bazy danych, potrzebne są pełna nazwa serwera i poświadczenia logowania administratora. Możesz łatwo odnaleźć nazwę serwera i informacje logowania z serwera **Przegląd** strony lub **właściwości** strony w witrynie Azure portal. 

2. Nawiązać połączenia z serwerem bazy danych, należy użyć konta administratora i hasła. Narzędzie Twojego preferowanego klienta, takich jak połączenia aplikacji MySQL Workbench, mysql.exe, HeidiSQL lub inne osoby. 
   Jeśli masz pewności, jak połączyć, zobacz [użyciu aplikacji MySQL Workbench do nawiązywania połączeń i wykonywanie zapytań dotyczących danych](./connect-workbench.md)

3. Poddaj edycji, a następnie uruchom poniższy kod SQL. Zastąp wartość symbolu zastępczego `db_user` z zamierzonym nową nazwę użytkownika i wartość symbolu zastępczego `testdb` własną nazwą bazy danych.

   Ta składnia kodu sql tworzy nową bazę danych o nazwie testdb w celach demonstracyjnych. Następnie tworzy nowego użytkownika w usłudze MySQL i przyznaje wszystkie uprawnienia do nowego schematu bazy danych (testdb.\*) dla tego użytkownika. 

   ```sql
   CREATE DATABASE testdb;
   
   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';
   
   FLUSH PRIVILEGES;
   ```

4. Sprawdź przyznaje w bazie danych.
   ```sql
   USE testdb;
   
   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Zaloguj się do serwera, określając wyznaczonym bazy danych przy użyciu nowej nazwy użytkownika i hasła. Ten przykład przedstawia wiersz polecenia mysql. Za pomocą tego polecenia zostanie wyświetlony monit o hasło dla nazwy użytkownika. Zastąp własną nazwę serwera, nazwę bazy danych i nazwę użytkownika.

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

## <a name="next-steps"></a>Kolejne kroki
Otwierająca zaporę dla adresów IP maszyn nowych użytkowników, aby umożliwić im na połączenie: [Tworzenie i zarządzanie nimi — Azure Database dla MySQL reguł zapory przy użyciu witryny Azure portal](howto-manage-firewall-using-portal.md) lub [wiersza polecenia platformy Azure](howto-manage-firewall-using-cli.md).

Aby uzyskać więcej informacji na temat zarządzania kontami użytkowników, zapoznaj się z dokumentacją produktu MySQL dla [Zarządzanie kontami użytkowników](https://dev.mysql.com/doc/refman/5.7/en/user-account-management.html), [składni GRANT](https://dev.mysql.com/doc/refman/5.7/en/grant.html), i [uprawnienia](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html).
