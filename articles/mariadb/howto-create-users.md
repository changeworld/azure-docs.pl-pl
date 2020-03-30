---
title: Tworzenie użytkowników — usługa Azure Database for MariaDB
description: W tym artykule opisano, jak utworzyć nowe konta użytkowników do interakcji z usługą Azure Database dla serwera MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 23e3c9db6302d2db597283358ff71c3693ecf435
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530176"
---
# <a name="create-users-in-azure-database-for-mariadb"></a>Tworzenie kont użytkowników w usłudze Azure Database for MariaDB 
W tym artykule opisano, jak można tworzyć użytkowników w usłudze Azure Database dla MariaDB.

Podczas pierwszego tworzenia bazy danych usługi Azure database dla MariaDB, podano nazwę użytkownika logowania administratora serwera i hasło. Aby uzyskać więcej informacji, możesz śledzić [przewodnik Szybki start](quickstart-create-mariadb-server-database-using-azure-portal.md). Nazwę użytkownika logowania administratora serwera można zlokalizować w witrynie Azure Portal.

Użytkownik administratora serwera otrzymuje pewne uprawnienia dla serwera, jak na liście: SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, ReLOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION KLIENT, TWORZENIE WIDOKU, WYŚWIETLANIE WIDOKU, TWORZENIE PROCEDURY, ZMIENIANIE PROCEDURY, TWORZENIE UŻYTKOWNIKA, ZDARZENIE, WYZWALACZ

Po utworzeniu serwera usługi Azure Database for MariaDB można użyć pierwszego konta administratora serwera, aby utworzyć dodatkowych użytkowników i udzielić im dostępu administratora. Ponadto konto administratora serwera może służyć do tworzenia mniej uprzywilejowanych użytkowników, którzy mają dostęp do poszczególnych schematów bazy danych.

## <a name="create-additional-admin-users"></a>Tworzenie dodatkowych użytkowników administratorów
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

## <a name="create-database-users"></a>Tworzenie użytkowników bazy danych

1. Pobierz informacje o połączeniu i nazwę użytkownika administratora.
   Aby można było nawiązać połączenie z serwerem bazy danych, potrzebne są pełna nazwa serwera i poświadczenia logowania administratora. Nazwę serwera i informacje logowania można łatwo znaleźć ze strony **Przegląd** serwera lub na stronie **Właściwości** w witrynie Azure portal. 

2. Użyj konta administratora i hasła, aby połączyć się z serwerem bazy danych. Użyj preferowanego narzędzia klienta, takiego jak MySQL Workbench, mysql.exe, HeidiSQL lub innych. 
   Jeśli nie masz pewności, jak się połączyć, zobacz [Łączenie danych z programem MySQL Workbench](./connect-workbench.md)

3. Edytuj i uruchom następujący kod SQL. Zastąp `db_user` wartość symbolu zastępczego nową nazwą `testdb` użytkownika i wartością zastępczą własną nazwą bazy danych.

   Ta składnia kodu SQL tworzy nową bazę danych o nazwie testdb dla przykładowych celów. Następnie tworzy nowego użytkownika w usłudze Azure Database for MariaDB i udziela wszystkich uprawnień\*do nowego schematu bazy danych (testdb. ) dla tego użytkownika. 

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

   ```bash
   mysql --host mydemoserver.mariadb.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```
   Aby uzyskać więcej informacji dotyczących zarządzania kontem użytkownika, zobacz Dokumentacja MariaDB dotycząca [zarządzania kontem użytkownika,](https://mariadb.com/kb/en/library/user-account-management/) [składni GRANT](https://mariadb.com/kb/en/library/grant/)i [uprawnień](https://mariadb.com/kb/en/library/grant/#privilege-levels).

## <a name="next-steps"></a>Następne kroki
Otwórz zaporę dla adresów IP maszyn nowych użytkowników, aby umożliwić im nawiązanie połączenia: [tworzenie reguł zapory usługi Azure Database dla mariadb i zarządzanie nimi przy użyciu witryny Azure portal](howto-manage-firewall-portal.md)  

<!--or [Azure CLI](howto-manage-firewall-using-cli.md).-->
