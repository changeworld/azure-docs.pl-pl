---
title: Tworzenie użytkowników w bazie danych Azure dla serwera MySQL
description: W tym artykule opisano sposób tworzenia nowych kont użytkowników do interakcji z bazą danych Azure dla serwera MySQL.
services: mysql
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
manager: kfile
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 9fd592efe48adefc6aca7a6caea24f546da23fa5
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
---
# <a name="create-users-in-azure-database-for-mysql-server"></a>Tworzenie użytkowników w bazie danych Azure dla serwera MySQL 
W tym artykule opisano sposób tworzenia użytkowników w bazie danych Azure dla serwera MySQL.

Podczas tworzenia bazy danych Azure dla programu MySQL, podać nazwę użytkownika logowania administratora serwera i hasło. Aby uzyskać więcej informacji, możesz wykonać [szybkiego startu](quickstart-create-mysql-server-database-using-azure-portal.md). Można znaleźć nazwy użytkownika logowania administratora serwera z portalu Azure.

Użytkownik Administrator serwera pobiera określone uprawnienia serwera wymienionych: Wybierz, WSTAWIANIA, aktualizacji, usuwanie, utworzyć, PORZUCIĆ, Załaduj ponownie, proces, odwołania, INDEKSU, ALTER, Pokaż baz danych, tworzenie tabel tymczasowych, tabele blokady, EXECUTE, PODRZĘDNA replikacji, replikacji KLIENTA, UTWORZYĆ WIDOK, POKAŻ WIDOK, TWORZENIE PROCEDURY, ALTER PROCEDURY, WYZWALACZA UŻYTKOWNIKA, ZDARZENIE,

Po utworzeniu bazy danych MySQL serwera Azure umożliwia pierwszego konta użytkownika administratora serwera tworzenie dodatkowych użytkowników i udzielanie dostępu administratora do nich. Ponadto konto administratora serwera mogą pozwala utworzyć mniej uprzywilejowanych użytkowników, którzy mają dostęp do schematów poszczególne bazy danych.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-mysql"></a>Jak utworzyć dodatkowych administratorów w bazie danych Azure dla programu MySQL
1. Pobierz nazwę połączenia administrator i informacji użytkownika.
   Aby można było nawiązać połączenie z serwerem bazy danych, potrzebne są pełna nazwa serwera i poświadczenia logowania administratora. Łatwo można znaleźć nazwy serwera oraz informacje o rejestracji z serwera **omówienie** strony lub **właściwości** strony w portalu Azure. 

2. Użyj konta administratora i hasła do łączenia się z serwerem bazy danych. Narzędzie z preferowanego klienta, takich jak MySQL Workbench, mysql.exe, HeidiSQL lub innych użytkowników. 
   Jeśli nie wiesz, jak połączyć, zobacz [MySQL Workbench używany do nawiązywania połączeń i zapytania na danych](./connect-workbench.md)

3. Zmodyfikuj i uruchom następujący kod SQL. Zamień na nową nazwę użytkownika, symbol zastępczy wartości `new_master_user`. Ta składnia przyznaje listy uprawnień na wszystkich schematów bazy danych (*.*) do nazwy użytkownika (new_master_user w tym przykładzie). 

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

## <a name="how-to-create-database-users-in-azure-database-for-mysql"></a>Tworzenie użytkowników bazy danych w bazie danych Azure dla programu MySQL

1. Pobierz nazwę połączenia administrator i informacji użytkownika.
   Aby można było nawiązać połączenie z serwerem bazy danych, potrzebne są pełna nazwa serwera i poświadczenia logowania administratora. Łatwo można znaleźć nazwy serwera oraz informacje o rejestracji z serwera **omówienie** strony lub **właściwości** strony w portalu Azure. 

2. Użyj konta administratora i hasła do łączenia się z serwerem bazy danych. Narzędzie z preferowanego klienta, takich jak MySQL Workbench, mysql.exe, HeidiSQL lub innych użytkowników. 
   Jeśli nie wiesz, jak połączyć, zobacz [MySQL Workbench używany do nawiązywania połączeń i zapytania na danych](./connect-workbench.md)

3. Zmodyfikuj i uruchom następujący kod SQL. Zastąp wartość symbolu zastępczego `db_user` z zamierzonym nową nazwę użytkownika, a wartość symbolu zastępczego `testdb` nazwą bazy danych.

   Ta składnia kodu sql tworzy nową bazę danych o nazwie programu testdb przykładowo. Tworzy nowego użytkownika w usłudze MySQL przyznaje wszystkie uprawnienia do nowego schematu bazy danych, a następnie (programu testdb.\*) dla tego użytkownika. 

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

5. Zaloguj się do serwera, określając wyznaczonych bazy danych przy użyciu nowej nazwy użytkownika i hasła. Ten przykład przedstawia mysql wiersza polecenia. Z tego polecenia zostanie wyświetlony monit o hasło dla nazwy użytkownika. Zastąp własną nazwę serwera, nazwa bazy danych i nazwa użytkownika.

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

## <a name="next-steps"></a>Kolejne kroki
Otwórz zaporę dla adresów IP maszyn nowych użytkowników, aby umożliwić im nawiązywanie: [tworzenie i zarządzanie nimi Azure bazy danych MySQL reguł zapory przy użyciu portalu Azure](howto-manage-firewall-using-portal.md) lub [interfejsu wiersza polecenia Azure](howto-manage-firewall-using-cli.md).

Aby uzyskać więcej informacji na temat zarządzania kontami użytkowników, zobacz dokumentację produktu MySQL dla [Zarządzanie kontami użytkowników](https://dev.mysql.com/doc/refman/5.7/en/user-account-management.html), [składni GRANT](https://dev.mysql.com/doc/refman/5.7/en/grant.html), i [uprawnienia](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html).
