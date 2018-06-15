---
title: Tworzenie użytkowników w bazie danych Azure PostgreSQL serwera
description: W tym artykule opisano sposób tworzenia nowych kont użytkowników do interakcji z bazą danych Azure dla serwera PostgreSQL.
services: postgresql
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
manager: jhubbard
ms.service: postgresql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 87a73929185112190d5dd6698e014db225ebc08e
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2018
ms.locfileid: "29575639"
---
# <a name="create-users-in-azure-database-for-postgresql-server"></a>Tworzenie użytkowników w bazie danych Azure PostgreSQL serwera 
W tym artykule opisano sposób tworzenia użytkowników w bazie danych Azure PostgreSQL serwera.

## <a name="the-server-admin-account"></a>Konto administratora serwera
Podczas tworzenia bazy danych Azure dla PostgreSQL, podać nazwę użytkownika administratora serwera i hasło. Aby uzyskać więcej informacji, możesz wykonać [szybkiego startu](quickstart-create-server-database-portal.md) wyświetlić podejścia krok po kroku. Nazwa użytkownika administratora serwera jest niestandardową nazwę, można znaleźć wybranego serwera Nazwa użytkownika administratora w portalu Azure.

Baza danych Azure, dla serwera PostgreSQL jest tworzony z 3 domyślne role zdefiniowane. Role te można wyświetlić, uruchamiając polecenie: `SELECT rolname FROM pg_roles;`
- azure_pg_admin
- azure_superuser
- użytkownika administratora serwera

Użytkownika administratora serwera jest członkiem roli azure_pg_admin. Jednak konto administratora serwera nie jest częścią roli azure_superuser. Ponieważ ta usługa jest zarządzana usługa PaaS, tylko Microsoft wchodzi w skład rolę użytkownika nadrzędnego. 

Aparat PostgreSQL używa uprawnienia do kontrolowania dostępu do obiektów bazy danych, zgodnie z opisem w [dokumentacji produktu PostgreSQL](https://www.postgresql.org/docs/current/static/sql-createrole.html). Użytkownik Administrator serwera w bazie danych Azure PostgreSQL, udzieleniu tych uprawnień: logowania, NOSUPERUSER, dziedziczenia, CREATEDB, CREATEROLE, NOREPLICATION

Konto użytkownika Administrator serwera może służyć do tworzenie dodatkowych użytkowników i przyznania tych użytkowników do roli azure_pg_admin. Ponadto konto administratora serwera mogą służyć do tworzenia mniej uprzywilejowanej użytkownicy i role, które mają dostęp do pojedynczych baz danych i schematów.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-postgresql"></a>Jak utworzyć dodatkowych administratorów PostgreSQL w bazie danych Azure
1. Pobierz nazwę połączenia administrator i informacji użytkownika.
   Aby można było nawiązać połączenie z serwerem bazy danych, potrzebne są pełna nazwa serwera i poświadczenia logowania administratora. Łatwo można znaleźć nazwy serwera oraz informacje o rejestracji z serwera **omówienie** strony lub **właściwości** strony w portalu Azure. 

2. Użyj konta administratora i hasła do łączenia się z serwerem bazy danych. Narzędzie z preferowanego klienta, takich jak pgAdmin lub psql.
   Jeśli nie wiesz, jak połączyć, zobacz [Połącz z bazą danych PostgreSQL przy użyciu psql w powłoce chmury](./quickstart-create-server-database-portal.md#connect-to-the-postgresql-database-by-using-psql-in-cloud-shell)

3. Zmodyfikuj i uruchom następujący kod SQL. Zamień na nową nazwę użytkownika, symbol zastępczy wartości < new_user > i Zastąp hasło symbolu zastępczego silne hasło. 

   ```sql
   CREATE ROLE <new_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT azure_pg_admin TO <new_user>;
   ```

## <a name="how-to-create-database-users-in-azure-database-for-postgresql"></a>Tworzenie użytkowników bazy danych w bazie danych Azure dla PostgreSQL

1. Pobierz nazwę połączenia administrator i informacji użytkownika.
   Aby można było nawiązać połączenie z serwerem bazy danych, potrzebne są pełna nazwa serwera i poświadczenia logowania administratora. Łatwo można znaleźć nazwy serwera oraz informacje o rejestracji z serwera **omówienie** strony lub **właściwości** strony w portalu Azure. 

2. Użyj konta administratora i hasła do łączenia się z serwerem bazy danych. Narzędzie z preferowanego klienta, takich jak pgAdmin lub psql.

3. Zmodyfikuj i uruchom następujący kod SQL. Zastąp wartość symbolu zastępczego `<db_user>` z zamierzonym nową nazwę użytkownika, a wartość symbolu zastępczego `<newdb>` nazwą bazy danych. Zastąp symbol zastępczy hasło silne hasło. 

   Ta składnia kodu sql tworzy nową bazę danych o nazwie programu testdb, przykładowo. Następnie tworzy nowego użytkownika w usłudze PostgreSQL i przyznaje nawiązać połączenia z uprawnieniami nowej bazy danych dla tego użytkownika. 

   ```sql
   CREATE DATABASE <newdb>;
   
   CREATE ROLE <db_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB NOCREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT CONNECT ON DATABASE testdb TO <db_user>;
   ```

4. Przy użyciu konta administratora, może być konieczne przyznanie dodatkowych uprawnień do zabezpieczania obiektów w bazie danych. Zapoznaj się [PostgreSQL dokumentacji](https://www.postgresql.org/docs/current/static/ddl-priv.html) więcej szczegółowych informacji dotyczących ról bazy danych i uprawnień. Na przykład: 
   ```sql
   GRANT ALL PRIVILEGES ON DATABASE <newdb> TO <db_user>;
   ```

5. Zaloguj się do serwera, określając wyznaczonych bazy danych przy użyciu nowej nazwy użytkownika i hasła. Ten przykład przedstawia psql wiersza polecenia. Z tego polecenia zostanie wyświetlony monit o hasło dla nazwy użytkownika. Zastąp własną nazwę serwera, nazwa bazy danych i nazwa użytkownika.

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=db_user@mydemoserver --dbname=newdb
   ```

## <a name="next-steps"></a>Kolejne kroki
Otwórz zaporę dla adresów IP maszyn nowych użytkowników, aby umożliwić im nawiązywanie: [tworzenie i zarządzanie bazą danych Azure dla reguł zapory PostgreSQL przy użyciu portalu Azure](howto-manage-firewall-using-portal.md) lub [interfejsu wiersza polecenia Azure](howto-manage-firewall-using-cli.md).

Aby uzyskać więcej informacji na temat zarządzania kontami użytkowników, zobacz dokumentację produktu PostgreSQL dla [ról bazy danych i uprawnień](https://www.postgresql.org/docs/current/static/user-manag.html), [składni GRANT](https://www.postgresql.org/docs/current/static/sql-grant.html), i [uprawnienia](https://www.postgresql.org/docs/current/static/ddl-priv.html).
