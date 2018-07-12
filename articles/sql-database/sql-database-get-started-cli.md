---
title: 'Interfejs wiersza polecenia platformy Azure: tworzenie bazy danych SQL | Microsoft Docs'
description: Dowiedz się, jak utworzyć serwer logiczny, regułę zapory na poziomie serwera i bazy danych usługi SQL Database przy użyciu interfejsu wiersza polecenia platformy Azure.
keywords: samouczek usługi sql database, tworzenie bazy danych sql
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,DBs & servers
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 03/23/2018
ms.author: carlrab
ms.openlocfilehash: d4bb27ddc4ff9385fd46fc7554af2af16ef40558
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38597544"
---
# <a name="create-a-single-azure-sql-database-using-the-azure-cli"></a>Tworzenie pojedynczej bazy danych Azure SQL Database za pomocą interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów Azure i zarządzanie nimi z poziomu wiersza polecenia lub skryptów. W tym przewodniku znajdują się szczegółowe informacje dotyczące użycia interfejsu wiersza polecenia platformy Azure na potrzeby wdrażania bazy danych Azure SQL Database w [grupie zasobów Azure](../azure-resource-manager/resource-group-overview.md) na [serwerze logicznym Azure SQL Database](sql-database-features.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="define-variables"></a>Definiowanie zmiennych

Zdefiniuj zmienne do wykorzystania w skryptach w tym przewodniku Szybki start.

```azurecli-interactive
# The data center and resource name for your resources
export resourcegroupname = myResourceGroup
export location = westeurope
# The logical server name: Use a random value or replace with your own value (do not capitalize)
export servername = server-$RANDOM
# Set an admin login and password for your database
export adminlogin = ServerAdmin
export password = ChangeYourAdminPassword1
# The ip address range that you want to allow to access your DB
export startip = "0.0.0.0"
export endip = "0.0.0.0"
# The database name
export databasename = mySampleDatabase
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz [grupę zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myResourceGroup` w lokalizacji `westeurope`.

```azurecli-interactive
az group create --name $resourcegroupname --location $location
```
## <a name="create-a-logical-server"></a>Tworzenie serwera logicznego

Utwórz [serwer logiczny Azure SQL Database](sql-database-features.md) za pomocą polecenia [az sql server create](/cli/azure/sql/server#az_sql_server_create). Serwer logiczny zawiera grupę baz danych zarządzanych jako grupa. Poniższy przykład obejmuje tworzenie serwera o losowo wybranej nazwie w grupie zasobów za pomocą identyfikatora logowania administratora o nazwie `ServerAdmin` i z hasłem `ChangeYourAdminPassword1`. Zastąp te wstępnie zdefiniowane wartości zgodnie z potrzebami.

```azurecli-interactive
az sql server create --name $servername --resource-group $resourcegroupname --location $location \
    --admin-user $adminlogin --admin-password $password
```

## <a name="configure-a-server-firewall-rule"></a>Konfigurowanie reguły zapory serwera

Utwórz [regułę zapory na poziomie serwera Azure SQL Database](sql-database-firewall-configure.md) za pomocą polecenia [az sql server firewall create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create). Reguła zapory na poziomie serwera umożliwia zewnętrznej aplikacji, takiej jak SQL Server Management Studio lub narzędzie SQLCMD, nawiązanie połączenia z bazą danych SQL za pośrednictwem zapory usługi SQL Database. W poniższym przykładzie zapora jest otwarta tylko dla innych zasobów platformy Azure. Aby włączyć łączność zewnętrzną, zmień adres IP na adres odpowiedni dla danego środowiska. Aby otworzyć wszystkie adresy IP, użyj wartości 0.0.0.0 jako początkowego adresu IP i wartości 255.255.255.255 jako adresu końcowego.  

```azurecli-interactive
az sql server firewall-rule create --resource-group $resourcegroupname --server $servername \
    -n AllowYourIp --start-ip-address $startip --end-ip-address $endip
```

> [!NOTE]
> Usługa SQL Database nawiązuje komunikację na porcie 1433. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 1433 może być zablokowany przez firmową zaporę. Jeśli zachodzi taka sytuacja, nie będzie można nawiązać połączenia z serwerem Azure SQL Database, chyba że dział IT otworzy port 1433.
>

## <a name="create-a-database-in-the-server-with-sample-data"></a>Tworzenie na serwerze bazy danych z przykładowymi danymi

Utwórz bazę danych [o poziomie wydajności S0](sql-database-service-tiers-dtu.md) na serwerze za pomocą polecenia [az sql db create](/cli/azure/sql/db#az_sql_db_create). Poniższy przykład tworzy bazę danych o nazwie `mySampleDatabase` i ładuje do niej przykładowe dane AdventureWorksLT. Zastąp te wstępnie zdefiniowane wartości zgodnie z potrzebami (inne przewodniki Szybki start w tej kolekcji bazują na wartościach z tego przewodnika).

```azurecli-interactive
az sql db create --resource-group $resourcegroupname --server $servername \
    --name $databasename --sample-name AdventureWorksLT --service-objective S0
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Inne przewodniki Szybki start w tej kolekcji bazują na tym przewodniku. 

> [!TIP]
> Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki start, nie usuwaj zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie planujesz kontynuować pracy, wykonaj następujące czynności, aby usunąć wszystkie zasoby utworzone w witrynie Azure Portal w ramach tego przewodnika Szybki start.
>

```azurecli-interactive
az group delete --name $resourcegroupname
```

## <a name="next-steps"></a>Następne kroki

- Teraz, gdy już masz bazę danych, możesz [nawiązać z nią połączenie i uruchamiać zapytania](sql-database-connect-query.md) za pomocą jednego z Twoich ulubionych narzędzi lub języków. 
- Aby dowiedzieć się, jak zaprojektować pierwszą bazę danych, tworzyć tabele i wstawiać dane, zapoznaj się z jednym z następujących samouczków:
 - [Projektowanie pierwszej bazy danych Azure SQL Database przy użyciu programu SSMS](sql-database-design-first-database.md)
  - [Projektowanie bazy danych Azure SQL Database i nawiązywanie połączenia za pomocą języka C# i narzędzia ADO.NET](sql-database-design-first-database-csharp.md)


