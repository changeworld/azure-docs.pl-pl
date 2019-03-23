---
title: Tworzenie użytkowników w usłudze Azure Database dla serwera PostgreSQL
description: W tym artykule opisano sposób tworzenia nowych kont użytkowników do interakcji z usługi Azure Database for postgresql w warstwie serwera.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/16/2018
ms.openlocfilehash: 33c107c46b314136fa3d43f8e7881e096afa374c
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351086"
---
# <a name="create-users-in-azure-database-for-postgresql-server"></a>Tworzenie użytkowników w usłudze Azure Database dla serwera PostgreSQL 
W tym artykule opisano sposób tworzenia użytkowników w usługi Azure Database for postgresql w warstwie serwera.

## <a name="the-server-admin-account"></a>Konto administratora serwera
Podczas pierwszego utworzenia usługi Azure Database for PostgreSQL, podać nazwę użytkownika administratora i hasła. Aby uzyskać więcej informacji, możesz wykonać [Szybki Start](quickstart-create-server-database-portal.md) aby zobaczyć szczegółowe podejście. Ponieważ nazwa użytkownika administratora serwera jest nazwa niestandardowa, można znaleźć nazwy użytkownika administratora wybranego serwera w witrynie Azure portal.

Azure Database for postgresql w warstwie serwera jest tworzony przy użyciu ról domyślnych 3, zdefiniowane. Te role można wyświetlić, uruchamiając polecenie: `SELECT rolname FROM pg_roles;`
- azure_pg_admin
- azure_superuser
- Użytkownik Administrator usługi serwera

Użytkownik Administrator usługi serwera jest elementem członkowskim roli azure_pg_admin. Jednak konta administratora serwera nie jest częścią roli azure_superuser. Ponieważ ta usługa to zarządzana usługa PaaS, tylko firma Microsoft jest częścią roli administratora. 

Aparat PostgreSQL używa uprawnień do kontrolowania dostępu do obiektów bazy danych, zgodnie z opisem w [dokumentacji produktu PostgreSQL](https://www.postgresql.org/docs/current/static/sql-createrole.html). W usłudze Azure Database for PostgreSQL użytkownik Administrator serwera otrzymuje tych uprawnień: IDENTYFIKATOR LOGOWANIA, NOSUPERUSER, DZIEDZICZĄ CREATEDB, CREATEROLE, NOREPLICATION

Konta użytkownika Administrator serwera może służyć do tworzenia dodatkowych użytkowników i przyznawanie tych użytkowników do roli azure_pg_admin. Ponadto konta administratora serwera można utworzyć mniej uprzywilejowane użytkownicy i role, które mają dostęp do poszczególnych baz danych i schematów.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-postgresql"></a>Jak utworzyć użytkowników dodatkowego administratora w usłudze Azure Database for PostgreSQL
1. Pobierz nazwę użytkownika informacji i administratora połączenia.
   Aby można było nawiązać połączenie z serwerem bazy danych, potrzebne są pełna nazwa serwera i poświadczenia logowania administratora. Możesz łatwo odnaleźć nazwę serwera i informacje logowania z serwera **Przegląd** strony lub **właściwości** strony w witrynie Azure portal. 

2. Nawiązać połączenia z serwerem bazy danych, należy użyć konta administratora i hasła. Narzędzie Twojego preferowanego klienta, takich jak narzędzia pgAdmin lub psql.
   Jeśli masz pewności, jak połączyć, zobacz [tego przewodnika Szybki Start](./quickstart-create-server-database-portal.md)

3. Poddaj edycji, a następnie uruchom poniższy kod SQL. Zamień na nową nazwę użytkownika, aby uzyskać wartość symbolu zastępczego < new_user > i Zastąp symbol zastępczy hasłem silne hasło. 

   ```sql
   CREATE ROLE <new_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT azure_pg_admin TO <new_user>;
   ```

## <a name="how-to-create-database-users-in-azure-database-for-postgresql"></a>Sposób tworzenia użytkowników bazy danych w usłudze Azure Database for PostgreSQL

1. Pobierz nazwę użytkownika informacji i administratora połączenia.
   Aby można było nawiązać połączenie z serwerem bazy danych, potrzebne są pełna nazwa serwera i poświadczenia logowania administratora. Możesz łatwo odnaleźć nazwę serwera i informacje logowania z serwera **Przegląd** strony lub **właściwości** strony w witrynie Azure portal. 

2. Nawiązać połączenia z serwerem bazy danych, należy użyć konta administratora i hasła. Narzędzie Twojego preferowanego klienta, takich jak narzędzia pgAdmin lub psql.

3. Poddaj edycji, a następnie uruchom poniższy kod SQL. Zastąp wartość symbolu zastępczego `<db_user>` z zamierzonym nową nazwę użytkownika i wartość symbolu zastępczego `<newdb>` własną nazwą bazy danych. Zastąp symbol zastępczy hasłem silne hasło. 

   Ta składnia kodu sql tworzy nową bazę danych o nazwie testdb, w celach demonstracyjnych. Następnie tworzy nowego użytkownika w usłudze PostgreSQL i przyznaje połączyć uprawnienia dla nowej bazy danych dla tego użytkownika. 

   ```sql
   CREATE DATABASE <newdb>;
   
   CREATE ROLE <db_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB NOCREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT CONNECT ON DATABASE <newdb> TO <db_user>;
   ```

4. Przy użyciu konta administratora, konieczne może być przyznawać dodatkowe uprawnienia do zabezpieczania obiektów w bazie danych. Zapoznaj się [dokumentacji PostgreSQL](https://www.postgresql.org/docs/current/static/ddl-priv.html) więcej szczegółowych informacji dotyczących ról bazy danych i uprawnień. Na przykład: 
   ```sql
   GRANT ALL PRIVILEGES ON DATABASE <newdb> TO <db_user>;
   ```

5. Zaloguj się do serwera, określając wyznaczonym bazy danych przy użyciu nowej nazwy użytkownika i hasła. Ten przykład przedstawia wiersza polecenia psql. Za pomocą tego polecenia zostanie wyświetlony monit o hasło dla nazwy użytkownika. Zastąp własną nazwę serwera, nazwę bazy danych i nazwę użytkownika.

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=db_user@mydemoserver --dbname=newdb
   ```

## <a name="next-steps"></a>Kolejne kroki
Otwierająca zaporę dla adresów IP maszyn nowych użytkowników, aby umożliwić im na połączenie: [Tworzenie i zarządzanie nimi — Azure Database for postgresql w warstwie reguł zapory przy użyciu witryny Azure portal](howto-manage-firewall-using-portal.md) lub [wiersza polecenia platformy Azure](howto-manage-firewall-using-cli.md).

Aby uzyskać więcej informacji na temat zarządzania kontami użytkowników, zapoznaj się z dokumentacją produktu PostgreSQL dla [ról bazy danych i uprawnień](https://www.postgresql.org/docs/current/static/user-manag.html), [składni GRANT](https://www.postgresql.org/docs/current/static/sql-grant.html), i [uprawnienia](https://www.postgresql.org/docs/current/static/ddl-priv.html).
