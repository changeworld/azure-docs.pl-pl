---
title: Tworzenie użytkowników — usługa Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano, jak można utworzyć nowe konta użytkowników do interakcji z usługą Azure Database dla PostgreSQL — pojedynczy serwer.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2019
ms.openlocfilehash: 127d484d6cfc35368803069f9c3d602e787baa56
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384351"
---
# <a name="create-users-in-azure-database-for-postgresql---single-server"></a>Tworzenie użytkowników w bazie danych azure dla postgreSQL — pojedynczy serwer

W tym artykule opisano, jak można tworzyć użytkowników w ramach usługi Azure Database dla serwera PostgreSQL.

Jeśli chcesz dowiedzieć się, jak tworzyć i zarządzać użytkownikami subskrypcji platformy Azure i ich uprawnieniami, możesz odwiedzić [artykuł kontroli dostępu (RBAC) opartej na rolach platformy Azure](../role-based-access-control/built-in-roles.md) lub [przejrzeć sposób dostosowywania ról.](../role-based-access-control/custom-roles.md)

## <a name="the-server-admin-account"></a>Konto administratora serwera

Podczas pierwszego tworzenia bazy danych usługi Azure database dla postgreSQL podano nazwę użytkownika administratora serwera i hasło. Aby uzyskać więcej informacji, można wykonać [szybki start,](quickstart-create-server-database-portal.md) aby wyświetlić podejście krok po kroku. Ponieważ nazwa użytkownika administratora serwera jest nazwą niestandardową, można zlokalizować wybraną nazwę użytkownika administratora serwera z witryny Azure portal.

Usługa Azure Database for PostgreSQL server is created with the 3 default roles defined. Te role można wyświetlić, uruchamiając polecenie:`SELECT rolname FROM pg_roles;`

- azure_pg_admin
- azure_superuser
- twój administrator serwera

Użytkownik administratora serwera jest członkiem roli azure_pg_admin. Jednak konto administratora serwera nie jest częścią roli azure_superuser. Ponieważ ta usługa jest zarządzaną usługą PaaS, tylko firma Microsoft jest częścią roli superużytnia.

Aparat PostgreSQL używa uprawnień do kontrolowania dostępu do obiektów bazy danych, jak omówiono w [dokumentacji produktu PostgreSQL](https://www.postgresql.org/docs/current/static/sql-createrole.html). W usłudze Azure Database for PostgreSQL administrator serwera otrzymuje następujące uprawnienia: LOGIN, NOSUPERUSER, INHERIT, CREATEDB, CREATEROLE, NOREPLICATION

Konto użytkownika administratora serwera może służyć do tworzenia dodatkowych użytkowników i przyznawania tych użytkowników do roli azure_pg_admin. Ponadto konto administratora serwera może służyć do tworzenia mniej uprzywilejowanych użytkowników i ról, które mają dostęp do poszczególnych baz danych i schematów.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-postgresql"></a>Jak utworzyć dodatkowych użytkowników administratorów w usłudze Azure Database for PostgreSQL

1. Pobierz informacje o połączeniu i nazwę użytkownika administratora.
   Aby można było nawiązać połączenie z serwerem bazy danych, potrzebne są pełna nazwa serwera i poświadczenia logowania administratora. Nazwę serwera i informacje logowania można łatwo znaleźć ze strony **Przegląd** serwera lub na stronie **Właściwości** w witrynie Azure portal.

2. Użyj konta administratora i hasła, aby połączyć się z serwerem bazy danych. Użyj preferowanego narzędzia klienta, takiego jak pgAdmin lub psql.
   Jeśli nie masz pewności, jak się połączyć, [zobacz](./quickstart-create-server-database-portal.md)

3. Edytuj i uruchom następujący kod SQL. Zastąp nową nazwę użytkownika dla wartości zastępczej <new_user> i zastąp hasło zastępcze własnym silnym hasłem. 

   ```sql
   CREATE ROLE <new_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';

   GRANT azure_pg_admin TO <new_user>;
   ```

## <a name="how-to-create-database-users-in-azure-database-for-postgresql"></a>Jak utworzyć użytkowników bazy danych w usłudze Azure Database for PostgreSQL

1. Pobierz informacje o połączeniu i nazwę użytkownika administratora.
   Aby można było nawiązać połączenie z serwerem bazy danych, potrzebne są pełna nazwa serwera i poświadczenia logowania administratora. Nazwę serwera i informacje logowania można łatwo znaleźć ze strony **Przegląd** serwera lub na stronie **Właściwości** w witrynie Azure portal.

2. Użyj konta administratora i hasła, aby połączyć się z serwerem bazy danych. Użyj preferowanego narzędzia klienta, takiego jak pgAdmin lub psql.

3. Edytuj i uruchom następujący kod SQL. Zastąp `<db_user>` wartość symbolu zastępczego nową nazwą `<newdb>` użytkownika i wartością zastępczą własną nazwą bazy danych. Zastąp hasło zastępcze własnym silnym hasłem.

   Ta składnia kodu SQL tworzy nową bazę danych o nazwie testdb, na przykład celów. Następnie tworzy nowego użytkownika w usłudze PostgreSQL i udziela uprawnień do łączenia się z nową bazą danych dla tego użytkownika.

   ```sql
   CREATE DATABASE <newdb>;
   
   CREATE ROLE <db_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB NOCREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT CONNECT ON DATABASE <newdb> TO <db_user>;
   ```

4. Za pomocą konta administratora, może być konieczne przyznanie dodatkowych uprawnień do zabezpieczania obiektów w bazie danych. Więcej informacji na temat ról i uprawnień bazy danych można znaleźć w [dokumentacji PostgreSQL.](https://www.postgresql.org/docs/current/static/ddl-priv.html) Przykład:

   ```sql
   GRANT ALL PRIVILEGES ON DATABASE <newdb> TO <db_user>;
   ```

5. Zaloguj się do serwera, określając wyznaczoną bazę danych, używając nowej nazwy użytkownika i hasła. W tym przykładzie pokazano wiersz polecenia psql. Za pomocą tego polecenia zostanie wyświetlony monit o podanie hasła do nazwy użytkownika. Zastąp własną nazwę serwera, nazwę bazy danych i nazwę użytkownika.

   ```shell
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=db_user@mydemoserver --dbname=newdb
   ```

## <a name="next-steps"></a>Następne kroki

Otwórz zaporę dla adresów IP maszyn nowych użytkowników, aby umożliwić im nawiązanie połączenia: [tworzenie i zarządzanie regułami zapory usługi Azure Database dla postgreSQL przy użyciu witryny Azure portal](howto-manage-firewall-using-portal.md) lub interfejsu [wiersza polecenia platformy Azure.](howto-manage-firewall-using-cli.md)

Aby uzyskać więcej informacji dotyczących zarządzania kontem użytkowników, zobacz Dokumentacja produktu PostgreSQL dla [ról i uprawnień bazy danych,](https://www.postgresql.org/docs/current/static/user-manag.html) [przyznać składnię](https://www.postgresql.org/docs/current/static/sql-grant.html)i [uprawnienia](https://www.postgresql.org/docs/current/static/ddl-priv.html).
