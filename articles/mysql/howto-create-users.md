---
title: Tworzenie użytkowników — usługa Azure Database for MySQL
description: W tym artykule opisano, jak utworzyć nowe konta użytkowników do interakcji z usługą Azure Database dla serwera MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/2/2020
ms.openlocfilehash: f8c020da72e9cdf8777a3eefac266e97e4a312bd
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263475"
---
# <a name="create-users-in-azure-database-for-mysql-server"></a>Tworzenie użytkowników w bazie danych platformy Azure dla serwera MySQL

W tym artykule opisano, jak można tworzyć użytkowników w bazie danych azure dla serwera MySQL.

Podczas pierwszego tworzenia bazy danych usługi Azure database dla MySQL podano nazwę użytkownika logowania administratora serwera i hasło. Aby uzyskać więcej informacji, możesz śledzić [przewodnik Szybki start](quickstart-create-mysql-server-database-using-azure-portal.md). Nazwę użytkownika logowania administratora serwera można zlokalizować w witrynie Azure Portal.

Administrator serwera otrzymuje pewne uprawnienia dla serwera na liście: SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER

Po utworzeniu serwera usługi Azure Database for MySQL można użyć pierwszego konta administratora serwera, aby utworzyć dodatkowych użytkowników i udzielić im dostępu administratora. Ponadto konto administratora serwera może służyć do tworzenia mniej uprzywilejowanych użytkowników, którzy mają dostęp do poszczególnych schematów bazy danych.

> [!NOTE]
> Uprawnienie SUPER i rola DBA nie są obsługiwane. Przejrzyj [uprawnienia](concepts-limits.md#privilege-support) w artykule ograniczenia, aby zrozumieć, co nie jest obsługiwane w usłudze.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-mysql"></a>Jak utworzyć dodatkowych administratorów w usłudze Azure Database dla MySQL

1. Pobierz informacje o połączeniu i nazwę użytkownika administratora.
   Aby można było nawiązać połączenie z serwerem bazy danych, potrzebne są pełna nazwa serwera i poświadczenia logowania administratora. Nazwę serwera i informacje logowania można łatwo znaleźć ze strony **Przegląd** serwera lub na stronie **Właściwości** w witrynie Azure portal.

2. Użyj konta administratora i hasła, aby połączyć się z serwerem bazy danych. Użyj preferowanego narzędzia klienta, takiego jak MySQL Workbench, mysql.exe, HeidiSQL lub innych.
   Jeśli nie masz pewności, jak się połączyć, zobacz [Łączenie danych z programem MySQL Workbench](./connect-workbench.md)

3. Edytuj i uruchom następujący kod SQL. Zastąp nową nazwę użytkownika `new_master_user`dla wartości zastępczej . Ta składnia przyznaje wymienione uprawnienia we wszystkich schematach bazy danych (*.*) do nazwy użytkownika (new_master_user w tym przykładzie).

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION;

   FLUSH PRIVILEGES;
   ```

4. Weryfikacja dotacji

   ```sql
   USE sys;

   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="how-to-create-database-users-in-azure-database-for-mysql"></a>Jak utworzyć użytkowników bazy danych w usłudze Azure Database for MySQL

1. Pobierz informacje o połączeniu i nazwę użytkownika administratora.
   Aby można było nawiązać połączenie z serwerem bazy danych, potrzebne są pełna nazwa serwera i poświadczenia logowania administratora. Nazwę serwera i informacje logowania można łatwo znaleźć ze strony **Przegląd** serwera lub na stronie **Właściwości** w witrynie Azure portal.

2. Użyj konta administratora i hasła, aby połączyć się z serwerem bazy danych. Użyj preferowanego narzędzia klienta, takiego jak MySQL Workbench, mysql.exe, HeidiSQL lub innych.
   Jeśli nie masz pewności, jak się połączyć, zobacz [Łączenie danych z programem MySQL Workbench](./connect-workbench.md)

3. Edytuj i uruchom następujący kod SQL. Zastąp `db_user` wartość symbolu zastępczego nową nazwą `testdb` użytkownika i wartością zastępczą własną nazwą bazy danych.

   Ta składnia kodu SQL tworzy nową bazę danych o nazwie testdb dla przykładowych celów. Następnie tworzy nowego użytkownika w usłudze MySQL i udziela wszystkich uprawnień do\*nowego schematu bazy danych (testdb. ) dla tego użytkownika.

   ```sql
   CREATE DATABASE testdb;

   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';

   FLUSH PRIVILEGES;
   ```

4. Sprawdź dotacje w bazie danych.

   ```sql
   USE testdb;

   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Zaloguj się do serwera, określając wyznaczoną bazę danych, używając nowej nazwy użytkownika i hasła. W tym przykładzie pokazano wiersz polecenia mysql. Za pomocą tego polecenia zostanie wyświetlony monit o podanie hasła do nazwy użytkownika. Zastąp własną nazwę serwera, nazwę bazy danych i nazwę użytkownika.

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

## <a name="next-steps"></a>Następne kroki

Otwórz zaporę dla adresów IP maszyn nowych użytkowników, aby umożliwić im nawiązanie połączenia: [tworzenie reguł zapory usługi Azure Database dla mySQL i zarządzanie nimi przy użyciu witryny Azure portal](howto-manage-firewall-using-portal.md) lub interfejsu [wiersza polecenia platformy Azure.](howto-manage-firewall-using-cli.md)

Aby uzyskać więcej informacji dotyczących zarządzania kontem użytkownika, zobacz Dokumentacja produktu MySQL dotycząca [zarządzania kontem użytkownika,](https://dev.mysql.com/doc/refman/5.7/en/access-control.html) [składni GRANT](https://dev.mysql.com/doc/refman/5.7/en/grant.html)i [uprawnień](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html).
