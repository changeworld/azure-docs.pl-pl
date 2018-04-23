---
title: Szybki start — tworzenie bazy danych Azure Database for PostgreSQL za pomocą interfejsu wiersza polecenia platformy Azure
description: Przewodnik Szybki start dotyczący tworzenia serwera usługi Azure Database for PostgreSQL i zarządzania nim przy użyciu interfejsu wiersza polecenia platformy Azure.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: quickstart
ms.date: 04/01/2018
ms.custom: mvc
ms.openlocfilehash: daa7ea345abb6228bee2d1ca5bfcc3850aaff9c3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="quickstart-create-an-azure-database-for-postgresql-using-the-azure-cli"></a>Szybki start: tworzenie bazy danych Azure Database for PostgreSQL za pomocą interfejsu wiersza polecenia platformy Azure
Azure Database for PostgreSQL to usługa zarządzana, która umożliwia uruchamianie i skalowanie w chmurze baz danych PostgreSQL o wysokiej dostępności, a także zarządzanie nimi. Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów Azure i zarządzanie nimi z poziomu wiersza polecenia lub skryptów. W tym przewodniku Szybki start przedstawiono, jak utworzyć serwer usługi Azure Database for PostgreSQL w [grupie zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) za pomocą interfejsu wiersza polecenia platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby sprawdzić zainstalowaną wersję, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli). 

Jeśli używasz interfejsu wiersza polecenia lokalnie, musisz zalogować się na swoje konto za pomocą polecenia [az login](/cli/azure/authenticate-azure-cli?view=interactive-log-in). Zanotuj właściwość **id** z danych wyjściowych polecenia dla odpowiedniej nazwy subskrypcji.
```azurecli-interactive
az login
```

Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Wybierz określony identyfikator subskrypcji na Twoim koncie za pomocą polecenia [az account set](/cli/azure/account#az_account_set). Zastąp właściwość **id** z danych wyjściowych polecenia **az login** dla subskrypcji symbolem zastępczym identyfikatora subskrypcji.
```azurecli-interactive
az account set --subscription <subscription id>
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz [grupę zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy. Należy podać unikatową nazwę. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myresourcegroup` w lokalizacji `westus`.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Tworzenie serwera usługi Azure Database for PostgreSQL

Utwórz [serwer usługi Azure Database for PostgreSQL](overview.md) za pomocą polecenia [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create). Serwer zawiera grupę baz danych zarządzanych jako grupa. 

W poniższym przykładzie serwer o nazwie `mydemoserver` zostanie utworzony w grupie zasobów `myresourcegroup` w regionie Zachodnie stany USA przy użyciu identyfikatora logowania administratora serwera `myadmin`. Jest to serwer **4. generacji** **ogólnego przeznaczenia** z 2 **rdzeniami wirtualnymi**. Nazwa serwera jest mapowana na nazwę DNS i dlatego musi być globalnie unikatowa w ramach platformy Azure. Zastąp zmienną `<server_admin_password>` swoją własną wartością.
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 9.6
```

> [!IMPORTANT]
> Nazwa logowania i hasło administratora serwera określone w tym miejscu będą wymagane do logowania do serwera i jego baz danych w późniejszej części tego przewodnika Szybki start. Zapamiętaj lub zapisz te informacje do wykorzystania w przyszłości.

Domyślnie baza danych **postgres** zostanie utworzona na Twoim serwerze. Baza danych [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) to domyślna baza danych przeznaczona do użycia dla użytkowników oraz na potrzeby narzędzi i aplikacji innych firm. 


## <a name="configure-a-server-level-firewall-rule"></a>Konfigurowanie reguły zapory na poziomie serwera

Utwórz regułę zapory na poziomie serwera Azure PostgreSQL za pomocą polecenia [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create). Reguła zapory na poziomie serwera pozwala aplikacji zewnętrznej, takiej jak narzędzie [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) lub [PgAdmin](https://www.pgadmin.org/), na nawiązywanie połączeń z Twoim serwerem przez zaporę usługi Azure PostgreSQL. 

Możesz ustawić regułę zapory uwzględniającą zakres adresów IP, aby mieć możliwość nawiązywania połączeń z Twojej sieci. W poniższym przykładzie użyto polecenia [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create) w celu utworzenia reguły zapory `AllowAllIps` dla zakresu adresów IP. Aby otworzyć wszystkie adresy IP, użyj wartości 0.0.0.0 jako początkowego adresu IP i wartości 255.255.255.255 jako adresu końcowego.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Serwer Azure PostgreSQL komunikuje się przez port 5432. Podczas nawiązywania połączenia z sieci firmowej ruch wychodzący przez port 5432 może być blokowany przez zaporę sieciową. Aby było możliwe nawiązywanie połączenia z serwerem usługi Azure PostgreSQL, poproś dział IT o otwarcie portu 5432.

## <a name="get-the-connection-information"></a>Pobieranie informacji o połączeniu

Aby nawiązać połączenie z serwerem, musisz podać informacje o hoście i poświadczenia dostępu.
```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

Wynik jest w formacie JSON. Zanotuj wartości **administratorLogin** i **fullyQualifiedDomainName**.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-postgresql-database-using-psql"></a>Nawiązywanie połączenia z bazą danych PostgreSQL za pomocą narzędzia psql

Jeśli na Twoim komputerze klienckim jest zainstalowany program PostgreSQL, możesz użyć lokalnego wystąpienia narzędzia [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html), aby nawiązać połączenie z serwerem Azure PostgreSQL. Użyj teraz narzędzia wiersza polecenia psql, aby nawiązać połączenie z serwerem Azure PostgreSQL.

1. Uruchom poniższe polecenie psql w celu nawiązania połączenia z serwerem usługi Azure Database for PostgreSQL
```azurecli-interactive
psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
```

  Na przykład poniższe polecenie służy do nawiązywania połączenia z domyślną bazą danych o nazwie **postgres** na Twoim serwerze PostgreSQL **mydemoserver.postgres.database.azure.com** za pomocą poświadczeń dostępu. Gdy zostanie wyświetlony monit o podanie hasła, wprowadź wybrane hasło `<server_admin_password>`.
  
  ```azurecli-interactive
psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
```

2.  Po nawiązaniu połączenia z serwerem utwórz pustą bazę danych za pomocą wiersza polecenia.
```sql
CREATE DATABASE mypgsqldb;
```

3.  W wierszu polecenia wykonaj poniższe polecenie, aby przełączyć połączenie na nowo utworzoną bazę danych **mypgsqldb**:
```sql
\c mypgsqldb
```

## <a name="connect-to-the-postgresql-server-using-pgadmin"></a>Nawiązywanie połączenia z serwerem PostgreSQL za pomocą narzędzia pgAdmin

pgAdmin to narzędzie open source używane z bazą danych PostgreSQL. Aplikację pgAdmin można zainstalować z [witryny internetowej pgAdmin](http://www.pgadmin.org/). Wersja narzędzia pgAdmin, której używasz, może różnić się od narzędzia używanego w tym poradniku Szybki start. Przeczytaj dokumentację narzędzia pgAdmin, jeśli potrzebujesz dodatkowych wskazówek.

1. Otwórz aplikację pgAdmin na swoim komputerze klienckim.

2. Na pasku narzędzi wybierz pozycję **Object** (Obiekt), umieść wskaźnik myszy nad opcją **Create** (Utwórz), a następnie wybierz pozycję **Server** (Serwer).

3. W oknie dialogowym **Create — Server** (Tworzenie — Serwer) na karcie **General** (Ogólne) wprowadź unikatową przyjazną nazwę serwera, np. **mydemoserver**.

   ![Karta „General” (Ogólne)](./media/quickstart-create-server-database-azure-cli/9-pgadmin-create-server.png)

4. W oknie dialogowym **Create - Server** (Tworzenie — Serwer) na karcie **Connection** (Połączenie) uzupełnij tabelę ustawień.

   ![Karta „Connection” (Połączenie)](./media/quickstart-create-server-database-azure-cli/10-pgadmin-create-server.png)

    Parametr narzędzia pgAdmin |Wartość|Opis
    ---|---|---
    Nazwa lub adres hosta | Nazwa serwera | Wartość nazwy serwera, która została użyta wcześniej podczas tworzenia serwera usługi Azure Database for PostgreSQL. Przykładowy serwer to **mydemoserver.postgres.database.azure.com**. Użyj w pełni kwalifikowanej nazwy domeny (**\*.postgres.database.azure.com**), jak pokazano w przykładzie. Jeśli nie pamiętasz nazwy serwera, postępuj zgodnie z instrukcjami w poprzedniej sekcji, aby uzyskać informacje dotyczące połączenia. 
    Port | 5432 | Port używany podczas łączenia z serwerem usługi Azure Database for PostgreSQL. 
    Baza danych konserwacji | *postgres* | Domyślna nazwa bazy danych wygenerowana przez system.
    Nazwa użytkownika | Nazwa logowania administratora serwera | Nazwa logowania administratora serwera, którą podano wcześniej podczas tworzenia serwera usługi Azure Database for PostgreSQL. Jeśli nie pamiętasz nazwy użytkownika, postępuj zgodnie z instrukcjami w poprzedniej sekcji, aby uzyskać informacje dotyczące połączenia. Format to *username@servername*.
    Hasło | Hasło administratora | Hasło wybrane podczas tworzenia serwera wcześniej w tym przewodniku Szybki start.
    Rola | Pozostaw puste | Nie trzeba podawać nazwy roli w tym momencie. Pozostaw pole puste.
    Tryb SSL | *Wymagane* | Tryb SSL możesz ustawić na karcie SSL narzędzia pgAdmin. Domyślnie wszystkie serwery usługi Azure Database for PostgreSQL są tworzone z włączonym wymuszaniem protokołu SSL. Aby wyłączyć wymuszanie protokołu SSL, zobacz [Wymuszanie protokołu SSL](./concepts-ssl-connection-security.md).
    
5. Wybierz pozycję **Zapisz**.

6. W okienku **Przeglądarka** z lewej strony rozwiń węzeł **Serwery**. Wybierz serwer, np. **mydemoserver**. Kliknij, aby się z nim połączyć.

7. Rozwiń węzeł serwera, a następnie rozwiń jego podrzędny element **Bazy danych**. Lista powinna obejmować istniejącą bazę danych *postgres* oraz wszelkie inne utworzone bazy danych. Za pomocą usługi Azure Database for PostgreSQL można utworzyć wiele baz danych na jednym serwerze.

8. Kliknij prawym przyciskiem myszy pozycję **Bazy danych**, wybierz menu **Utwórz**, a następnie wybierz pozycję **Baza danych**.

9. W polu **Database** (Baza danych) wpisz wybraną nazwę bazy danych, np. **mypgsqldb2**.

10. Z listy rozwijanej wybierz pozycję **Właściciel** dla bazy danych. Wybierz nazwę logowania administratora serwera, np. przykładową nazwę **my admin**.

   ![Tworzenie bazy danych w narzędziu pgAdmin](./media/quickstart-create-server-database-azure-cli/11-pgadmin-database.png)

11. Wybierz pozycję **Zapisz**, aby utworzyć nową pustą bazę danych.

12. W okienku **Browser** (Przeglądarka) utworzona baza danych zostanie wyświetlona na liście baz danych w ramach pozycji nazwy serwera.




## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Wyczyść wszystkie zasoby utworzone w tym przewodniku Szybki start, usuwając [grupę zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md).

> [!TIP]
> Inne przewodniki Szybki start w tej kolekcji bazują na tym przewodniku. Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki start, nie usuwaj zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie planujesz kontynuować pracy, wykonaj następujące czynności, aby usunąć wszystkie zasoby utworzone za pomocą interfejsu wiersza polecenia platformy Azure w ramach tego przewodnika Szybki start.

```azurecli-interactive
az group delete --name myresourcegroup
```

Jeśli po prostu chcesz usunąć jeden z nowo utworzonych serwerów, możesz uruchomić polecenie [az postgres server delete](/cli/azure/postgres/server#az_postgres_server_delete).
```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Migrowanie bazy danych przy użyciu funkcji eksportowania i importowania](./howto-migrate-using-export-and-import.md)

