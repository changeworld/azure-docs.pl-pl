---
title: Jak utworzyć kopię zapasową i przywrócić serwer w usłudze Azure Database for MySQL
description: Dowiedz się, jak utworzyć kopię zapasową i przywrócić serwer w usłudze Azure Database for MySQL przy użyciu wiersza polecenia platformy Azure.
author: rachel-msft
ms.author: raagyema
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/01/2018
ms.openlocfilehash: f3850623f5918ea9405131edb1821b941019ac34
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66160439"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-cli"></a>Jak wykonać kopię zapasową i przywrócić serwer w usłudze Azure Database for MySQL za pomocą wiersza polecenia platformy Azure

## <a name="backup-happens-automatically"></a>Kopia zapasowa odbywa się automatycznie
Usługa Azure Database for MySQL są kopie zapasowe serwerów okresowo do włączania funkcji przywracania. Za pomocą tej funkcji można przywrócić serwera i jego baz danych do wcześniejszych punktu w czasie na nowym serwerze.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, potrzebne są:
- [— Azure Database for MySQL serwera i bazy danych](quickstart-create-mysql-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

 

> [!IMPORTANT]
> Ten poradnik wymaga użycie wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby sprawdzić, która wersja, w wierszu polecenia wiersza polecenia platformy Azure, wprowadź `az --version`. Aby zainstalować lub uaktualnić, zobacz [interfejsu wiersza polecenia platformy Azure Zainstaluj]( /cli/azure/install-azure-cli).

## <a name="set-backup-configuration"></a>Ustaw konfigurację tworzenia kopii zapasowych

Upewnij się nad wyborem między skonfigurowaniem serwera kopii zapasowych lokalnie nadmiarowy lub geograficznie nadmiarowych kopii zapasowych podczas tworzenia serwera. 

> [!NOTE]
> Po utworzeniu serwera typu nadmiarowości, który ma, nie mogą być przełączane lokalnie nadmiarowy, Magazyn geograficznie nadmiarowy vs.
>

Podczas tworzenia serwera za pomocą `az mysql server create` polecenia `--geo-redundant-backup` parametru decyduje o swoją opcję nadmiarowości kopii zapasowej. Jeśli `Enabled`, geograficznie nadmiarowych kopii zapasowych są pobierane. Lub, jeśli `Disabled` są pobierane lokalnie nadmiarowych kopii zapasowych. 

Okres przechowywania kopii zapasowej jest ustawiana przez parametr `--backup-retention`. 

Aby uzyskać więcej informacji na temat ustawiania tych wartości podczas tworzenia, zobacz [serwer MySQL — Szybki Start interfejs wiersza polecenia platformy Azure Database for](quickstart-create-mysql-server-database-using-azure-cli.md).

Okres przechowywania kopii zapasowej serwera można zmienić w następujący sposób:

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

Poprzedni przykład zmienia okres przechowywania kopii zapasowych mydemoserver 10 dni.

Okres przechowywania kopii zapasowej decyduje jak daleko w czasie, które mogą być pobierane przywracania w momencie, ponieważ jest on oparty na dostępnych kopii zapasowych. W momencie przywracania jest dokładniejszym opisem zawartym w następnej sekcji.

## <a name="server-point-in-time-restore"></a>Serwer w momencie przywracania
Możesz przywrócić serwer do wcześniejszego punktu w czasie. Przywróconych danych jest kopiowany do nowego serwera, a istniejący serwer pozostanie niezmieniona. Na przykład jeśli tabela jest przypadkowo w południe już dziś, można przywrócić do czasu, po prostu przed południem. Następnie możesz pobrać brakujące tabeli i danych z przywróconej kopii serwera. 

Aby przywrócić serwer, należy użyć wiersza polecenia platformy Azure [az mysql server restore](/cli/azure/mysql/server#az-mysql-server-restore) polecenia.

### <a name="run-the-restore-command"></a>Uruchom polecenie restore

Aby przywrócić serwer w wierszu polecenia wiersza polecenia platformy Azure, wprowadź następujące polecenie:

```azurecli-interactive
az mysql server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

`az mysql server restore` Polecenie wymaga następujących parametrów:

| Ustawienie | Sugerowana wartość | Opis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Grupa zasobów, w której istnieje na serwerze źródłowym.  |
| name | mydemoserver-restored | Nazwa nowego serwera utworzonego za pomocą polecenie przywracania. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Wybierz punkt w czasie do przywrócenia. Ta data i godzina musi przypadać w okresie przechowywania kopii zapasowej serwera źródłowego. Użyj formatu ISO8601 daty i godziny. Na przykład takie jak możesz użyć własnej lokalnej strefy czasowej, `2018-03-13T05:59:00-08:00`. Można również użyć formatu UTC Zulu, na przykład `2018-03-13T13:59:00Z`. |
| source-server | mydemoserver | Nazwa lub identyfikator serwera źródłowego, z którego ma zostać przeprowadzone przywrócenie. |

Gdy można przywrócić serwer do wcześniejszego punktu w czasie, tworzony jest nowy serwer. Oryginalny serwer i jego baz danych z określonego punktu w czasie są kopiowane do nowego serwera.

Wartości lokalizacji i cen warstwy dla przywróconego serwera pozostają takie same, jak oryginalny serwer. 

Po ukończeniu procesu przywracania zlokalizuj nowy serwer, a następnie sprawdź, czy dane są przywracane zgodnie z oczekiwaniami.

## <a name="geo-restore"></a>Przywracanie geograficzne
Skonfigurowanie serwera na potrzeby geograficznie nadmiarowych kopii zapasowych można utworzyć nowy serwer z kopii zapasowej tego istniejącego serwera. Ten nowy serwer można utworzyć w dowolnym regionie, że — Azure Database for MySQL jest dostępny.  

Aby utworzyć serwer przy użyciu kopii zapasowej nadmiarowej geograficznie, należy użyć wiersza polecenia platformy Azure `az mysql server georestore` polecenia.

> [!NOTE]
> Gdy tworzona jest najpierw serwer nie może być natychmiast dostępne dla przywracania geograficznego. Może upłynąć kilka godzin metadane potrzebne do wypełnienia.
>

Przywracania geograficznego serwera, w wierszu polecenia wiersza polecenia platformy Azure wprowadź następujące polecenie:

```azurecli-interactive
az mysql server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen5_8 
```
To polecenie tworzy nowy serwer o nazwie *mydemoserver georestored* we wschodnim regionie USA, który będzie należał do *myresourcegroup*. Jest ogólnego przeznaczenia 5 ogólnego serwera z 8 rdzeniami wirtualnymi. Serwer jest tworzony z geograficznie nadmiarowej kopii zapasowej *mydemoserver*, która jest również w grupie zasobów *myresourcegroup*

Jeśli chcesz utworzyć nowy serwer w innej grupie zasobów z istniejącego serwera, następnie w `--source-server` parametru zakwalifikowanie nazwę serwera, jak w poniższym przykładzie:

```azurecli-interactive
az mysql server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforMySQL/servers/mydemoserver" --location eastus --sku-name GP_Gen5_8

```

`az mysql server georestore` Polecenie wymaga następujących parametrów:

| Ustawienie | Sugerowana wartość | Opis  |
| --- | --- | --- |
|resource-group| myresourcegroup | Nazwa grupy zasobów, nowy serwer będzie znajdować się.|
|name | mydemoserver-georestored | Nazwa nowego serwera. |
|source-server | mydemoserver | Nazwa istniejącego serwera, którego geograficznie nadmiarowy tworzenia kopii zapasowych są używane. |
|location | eastus | Lokalizacja nowego serwera. |
|sku-name| GP_Gen5_8 | Ten parametr ustawia cen warstwy, Generowanie obliczeń i liczba rdzeni wirtualnych nowego serwera. GP_Gen5_8 mapuje do ogólnego przeznaczenia 5 ogólnego serwera z 8 rdzeniami wirtualnymi.|


>[!Important]
>Podczas tworzenia nowego serwera przez Przywracanie geograficzne, dziedziczy takim samym rozmiarze magazynu i warstwę cenową co serwer źródłowy. Te wartości nie można zmienić podczas tworzenia. Po utworzeniu nowego serwera, jego rozmiar magazynu można przeskalować w górę.

Po ukończeniu procesu przywracania zlokalizuj nowy serwer, a następnie sprawdź, czy dane są przywracane zgodnie z oczekiwaniami.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o usłudze [kopie zapasowe](concepts-backup.md).
- Dowiedz się więcej o [ciągłość prowadzenia działalności biznesowej](concepts-business-continuity.md) opcje.
