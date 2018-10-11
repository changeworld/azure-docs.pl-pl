---
title: 'Szybki start: tworzenie serwera usługi Azure Database for MariaDB za pomocą interfejsu wiersza polecenia platformy Azure'
description: W tym przewodniku Szybki start opisano, jak utworzyć serwer usługi Azure Database for MariaDB w grupie zasobów platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.devlang: azure-cli
ms.topic: quickstart
ms.date: 09/24/2018
ms.custom: mvc
ms.openlocfilehash: 9e87dacb80aa7fc5f5b073e631fc06ae74b3ad00
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46996608"
---
# <a name="create-an-azure-database-for-mariadb-server-using-azure-cli"></a>Tworzenie serwera usługi Azure Database for MariaDB za pomocą interfejsu wiersza polecenia platformy Azure
W tym przewodniku Szybki start opisano, jak utworzyć serwer usługi Azure Database for MariaDB w grupie zasobów platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure w czasie około pięciu minut. Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów Azure i zarządzanie nimi z poziomu wiersza polecenia lub skryptów.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli). 

Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w której zasób istnieje lub dla której są za niego naliczane opłaty. Wybierz określony identyfikator subskrypcji na Twoim koncie za pomocą polecenia [az account set](/cli/azure/account#az-account-set).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Utwórz [grupę zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy.

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myresourcegroup` w lokalizacji `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>Tworzenie serwera usługi Azure Database for MariaDB
Utwórz serwer usługi Azure Database for MariaDB za pomocą polecenia **[az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create)**. Serwer umożliwia zarządzanie wieloma bazami danych. Zwykle dla każdego projektu lub użytkownika używana jest oddzielna baza danych.

**Ustawienie** | **Wartość przykładowa** | **Opis**
---|---|---
name | mydemoserver | Wybierz unikatową nazwę, która identyfikuje serwer usługi Azure Database for MariaDB. Nazwa serwera może zawierać tylko małe litery, cyfry i znaki łącznika (-). Musi zawierać od 3 do 63 znaków.
resource-group | myresourcegroup | Podaj nazwę grupy zasobów platformy Azure.
sku-name | GP_Gen5_2 | Nazwa jednostki SKU. Zgodnie z konwencją {warstwa cenowa}_{generacja obliczeniowa}_{rdzenie wirtualne} w skrócie. Zobacz poniższą tabelę, aby uzyskać więcej informacji na temat parametru sku-name.
backup-retention | 7 | Jak długo należy przechowywać kopię zapasową. Jednostka to dni. Zakres wynosi od 7 do 35. 
geo-redundant-backup | Disabled (Wyłączony) | Określa, czy dla tego serwera powinny być włączone kopie zapasowe geograficznie nadmiarowe. Dozwolone wartości: Enabled (włączone) i Disabled (wyłączone).
location | westus | Lokalizacja platformy Azure dla serwera.
ssl-enforcement | Enabled (Włączony) | Określa, czy dla serwera powinien być włączony protokół SSL. Dozwolone wartości: Enabled (włączone) i Disabled (wyłączone).
storage-size | 51200 | Pojemność magazynu serwera (w megabajtach). Rozmiar magazynu powinien wynosić co najmniej 5120 MB i można go zwiększać o wielokrotność 1024 MB. Zobacz dokument dotyczący [warstw cenowych](./concepts-pricing-tiers.md), aby uzyskać więcej informacji na temat limitu rozmiaru magazynu. 
version | 10.2 | Wersja główna aparatu MariaDB.
admin-user | myadmin | Nazwa użytkownika w przypadku logowania administratora. Nie może być to **azure_superuser**, **admin**, **administrator**, **root**, **guest** ani **public**.
admin-password | Password123 | Hasło użytkownika administratora. Musi zawierać od 8 do 128 znaków. Hasło musi zawierać znaki z trzech z następujących kategorii: wielkie litery angielskie, małe litery angielskie, cyfry i znaki inne niż alfanumeryczne.

Wartość parametru sku-name jest zgodna z konwencją {warstwa cenowa}\_{generacja obliczeniowa}\_{rdzenie wirtualne}, jak pokazano w przykładach poniżej:
+ `--sku-name B_Gen5_4` — warstwa podstawowa, 5. generacja, 4 rdzenie wirtualne.
+ `--sku-name GP_Gen5_32` — warstwa ogólnego przeznaczenia, 5. generacja, 32 rdzenie wirtualne.
+ `--sku-name MO_Gen5_2` — warstwa zoptymalizowana pod kątem pamięci, 5. generacja, 2 rdzenie wirtualne.

Aby ustalić poprawne wartości dla poszczególnych regionów i warstw, zapoznaj się z dokumentacją [warstw cenowych](./concepts-pricing-tiers.md).

W poniższym przykładzie serwer o nazwie `mydemoserver` zostanie utworzony w grupie zasobów `myresourcegroup` w regionie Zachodnie stany USA przy użyciu identyfikatora logowania administratora serwera `myadmin`. Jest to serwer **5. generacji** **ogólnego przeznaczenia** z 2 **rdzeniami wirtualnymi**. Nazwa serwera jest mapowana na nazwę DNS i dlatego musi być globalnie unikatowa w ramach platformy Azure. Zastąp zmienną `<server_admin_password>` swoją własną wartością.
```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.2
```

## <a name="configure-firewall-rule"></a>Konfigurowanie reguły zapory
Utwórz regułę zapory na poziomie serwera usługi Azure Database for MariaDB za pomocą polecenia **[az mariadb server firewall-rule create](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create)**. Reguła zapory na poziomie serwera pozwala aplikacji zewnętrznej, takiej jak narzędzie wiersza polecenia **mysql** lub program MySQL Workbench, na nawiązywanie połączeń z Twoim serwerem przez zaporę usługi Azure MariaDB. 

Poniższy przykład powoduje utworzenie reguły zapory o nazwie `AllowMyIP`, która zezwala na połączenia z określonego adresu IP – 192.168.0.1. Zastąp ten adres adresem IP lub zakresem adresów IP odpowiednim dla urządzeń, z których będzie nawiązywane połączenie. 

```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Połączenia z usługą Azure Database for MariaDB korzystają z portu 3306. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 3306 może być zablokowany. W takim przypadku nie będzie można nawiązać połączenia z serwerem, chyba że dział informatyczny otworzy port 3306.
> 

## <a name="configure-ssl-settings"></a>Konfigurowanie ustawień SSL
Domyślnie połączenia SSL między Twoim serwerem i aplikacjami klienckimi są wymuszane. Zapewnia to bezpieczeństwo danych „w ruchu” przez szyfrowanie strumienia danych przesyłanych przez Internet. Aby uprościć ten przewodnik Szybki start, wyłącz połączenia SSL dla Twojego serwera. Nie zaleca się wyłączania połączenia SSL w przypadku serwerów produkcyjnych. Aby uzyskać więcej informacji, zobacz [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL (Konfigurowanie łączności SSL w aplikacji w celu bezpiecznego nawiązywania połączeń z usługą Azure Database for MariaDB)](./howto-configure-ssl.md).

W poniższym przykładzie jest wyłączane wymuszanie stosowania protokołu SSL na Twoim serwerze MariaDB.
 
 ```azurecli-interactive
 az mariadb server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Disabled
 ```

## <a name="get-the-connection-information"></a>Pobieranie informacji o połączeniu

Aby nawiązać połączenie z serwerem, musisz podać informacje o hoście i poświadczenia dostępu.

```azurecli-interactive
az mariadb server show --resource-group myresourcegroup --name mydemoserver
```

Wynik jest w formacie JSON. Zanotuj wartości **fullyQualifiedDomainName** i **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mariadb.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMariaDB/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
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
  "type": "Microsoft.DBforMariaDB/servers",
  "userVisibleState": "Ready",
  "version": "10.2"
}
```

## <a name="connect-to-server-using-mysql-command-line"></a>Nawiązywanie połączenia z serwerem za pomocą wiersza polecenia mysql
Nawiąż połączenie z serwerem za pomocą narzędzia wiersza polecenia **mysql**. Możesz pobrać to narzędzie wiersza polecenia [tutaj](https://dev.mysql.com/downloads/) i zainstalować je na komputerze. Zamiast tego możesz też kliknąć przycisk **Wypróbuj** przy przykładach kodu lub przycisk `>_` na pasku narzędzi po prawej stronie u góry w witrynie Azure Portal i uruchomić usługę **Azure Cloud Shell**.

Wpisz kolejne polecenia: 

1. Nawiąż połączenie z serwerem za pomocą narzędzia wiersza polecenia **mysql**:
```azurecli-interactive
 mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
```

2. Wyświetl stan serwera po pojawieniu się monitu mysql>:
```sql
status
```
Jeśli wszystko pójdzie dobrze, narzędzie wiersza polecenia powinno zwrócić następujący tekst:

```bash
C:\Users\>mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
Enter password: ***********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 65512
Server version: 5.6.39.0 MariaDB Server

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64)

Connection id:          64681
Current database:
Current user:           myadmin@40.118.201.21
SSL:                    Cipher in use is AES256-SHA
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server version:         5.6.39.0 MariaDB Server
Protocol version:       10
Connection:             mydemoserver.mariadb.database.azure.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    utf8
Conn.  characterset:    utf8
TCP port:               3306
Uptime:                 1 day 3 hours 28 min 50 sec

Threads: 10  Questions: 29002  Slow queries: 0  Opens: 33  Flush tables: 3  Open tables: 1  Queries per second avg: 0.293
--------------

mysql>
```

> [!TIP]
> Aby zapoznać się z dodatkowymi poleceniami, zobacz [MySQL 5.7 Reference Manual - Chapter 4.5.1 (Podręcznik programu MySQL 5.7 — Rozdział 4.5.1)](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

## <a name="connect-to-server-using-mysql-workbench"></a>Nawiązywanie połączenia z serwerem za pomocą aplikacji MySQL Workbench
1.  Uruchom aplikację MySQL Workbench na swoim komputerze klienckim. Aplikację MySQL Workbench możesz pobrać i zainstalować [stąd](https://dev.mysql.com/downloads/workbench/).

2.  W oknie dialogowym **Konfigurowanie nowego połączenia** wprowadź poniższe informacje na karcie **Parametry**:

   ![konfigurowanie nowego połączenia](./media/quickstart-create-mariadb-server-database-using-azure-cli/setup-new-connection.png)

| **Ustawienie** | **Sugerowana wartość** | **Opis** |
|---|---|---|
|   Nazwa połączenia | Moje połączenie | Podaj etykietę dla tego połączenia (może to być dowolny tekst) |
| Metoda połączenia | wybierz opcję Standardowa (TCP/IP) | Użyj protokołu TCP/IP do nawiązania połączenia z usługą Azure Database for MariaDB |
| Nazwa hosta | mydemoserver.mariadb.database.azure.com | Wcześniej zanotowana nazwa serwera. |
| Port | 3306 | Używany jest domyślny port dla programu MariaDB. |
| Nazwa użytkownika | myadmin@mydemoserver | Identyfikator logowania administratora serwera zanotowany przez Ciebie wcześniej. |
| Hasło | **** | Użyj hasła do konta administratora, które zostało ustawione wcześniej. |

Kliknij przycisk **Testuj połączenie**, aby sprawdzić, czy wszystkie parametry zostały prawidłowo skonfigurowane.
Teraz możesz kliknąć połączenie, aby pomyślnie nawiązać połączenie z serwerem.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli te zasoby nie są Ci potrzebne do pracy z innym przewodnikiem Szybki start lub samouczkiem, możesz je usunąć, uruchamiając następujące polecenie: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Jeśli po prostu chcesz usunąć jeden z nowo utworzonych serwerów, możesz uruchomić polecenie **[az mariadb server delete](/cli/azure/mariadb/server#az-mariadb-server-delete)**.
```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

<!--
## Next steps

> [!div class="nextstepaction"]
> [Design a MariaDB Database with Azure CLI](./tutorial-design-database-using-cli.md)
-->