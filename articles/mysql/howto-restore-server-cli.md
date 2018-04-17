---
title: Jak utworzyć kopii zapasowej i przywracania serwera w bazie danych Azure dla programu MySQL
description: Dowiedz się, jak wykonywanie kopii zapasowych i przywracania serwera w bazie danych Azure dla programu MySQL przy użyciu wiersza polecenia platformy Azure.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 04/01/2018
ms.openlocfilehash: 5e8ac9da4df2428191e8a7e6402f2ac06e695503
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-cli"></a>Jak wykonać kopię zapasową i przywrócić serwer w bazie danych Azure dla programu MySQL przy użyciu wiersza polecenia platformy Azure

## <a name="backup-happens-automatically"></a>Kopia zapasowa jest wykonywana automatycznie
Bazy danych platformy Azure dla programu MySQL są kopie zapasowe serwerów okresowo do włączania funkcji przywracania. Za pomocą tej funkcji można przywrócić serwer i wszystkie jego bazy danych do wcześniejszych w momencie, na nowym serwerze.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, potrzebne są:
- [Azure bazy danych MySQL serwera i bazy danych](quickstart-create-mysql-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

 

> [!IMPORTANT]
> Ten przewodnik wymaga użycia interfejsu wiersza polecenia Azure w wersji 2.0 lub nowszej. Aby upewnić się, wersja, w wierszu polecenia interfejsu wiersza polecenia Azure, wprowadź `az --version`. Aby zainstalować lub uaktualnić, zobacz [zainstalować Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="add-the-extension"></a>Dodawanie rozszerzenia
Dodaj zaktualizowane rozszerzenie zarządzania usługi Azure Database for MySQL za pomocą następującego polecenia:
```azurecli-interactive
az extension add --name rdbms
``` 

Sprawdź, czy masz zainstalowaną poprawną wersję rozszerzenia. 
```azurecli-interactive
az extension list
```

Zwracany kod JSON powinien zawierać następujące informacje: 
```json
{
    "extensionType": "whl",
    "name": "rdbms",
    "version": "0.0.5"
}
```

Jeśli nie jest zwracana wersja 0.0.5, uruchom następujące polecenie, aby zaktualizować rozszerzenie: 
```azurecli-interactive
az extension update --name rdbms
```


## <a name="set-backup-configuration"></a>Zestaw kopii zapasowych konfiguracji

Należy wybrać opcję konfigurowania serwera dla kopii zapasowych lokalnie nadmiarowego lub geograficznie nadmiarowy kopii zapasowych na utworzenie serwera. 

> [!NOTE]
> Po utworzeniu serwera, rodzaj nadmiarowość, który ma ona geograficznie nadmiarowy vs magazyn lokalnie nadmiarowy, nie zostaną włączone.
>

Podczas tworzenia serwera za pośrednictwem `az mysql server create` polecenia `--geo-redundant-backup` parametru decyduje o opcjach nadmiarowość kopii zapasowej. Jeśli `Enabled`, są pobierane z magazynu geograficznie nadmiarowego kopii zapasowych. Lub, jeśli `Disabled` są pobierane lokalnie nadmiarowego kopii zapasowych. 

Okres przechowywania kopii zapasowych jest ustawiany przez parametr `--backup-retention-days`. 

Aby uzyskać więcej informacji na temat ustawiania tych wartości podczas tworzenia, zobacz [Azure bazy danych serwera MySQL — Szybki Start interfejsu wiersza polecenia](quickstart-create-mysql-server-database-using-azure-cli.md).

Okres przechowywania kopii zapasowych serwera można zmienić w następujący sposób:

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --backup-retention-days 10
```

Powyższy przykład zmiany okres przechowywania kopii zapasowych mydemoserver 10 dni.

Okres przechowywania kopii zapasowych decyduje, jak daleko w czasie, które mogą być pobierane w momencie przywracania, ponieważ jest on oparty na kopie zapasowe dostępne. W momencie przywracania jest dalsze opisana w następnej sekcji.

## <a name="server-point-in-time-restore"></a>Serwer w momencie przywracania
Serwer można przywrócić z wcześniejszego punktu w czasie. Przywrócone dane zostaną skopiowane do nowego serwera, a istniejący serwer pozostanie niezmieniona. Na przykład jeśli przypadkowo upuszczeniu tabeli w południe dzisiaj, można przywrócić na czas bezpośrednio przed południe. Następnie można pobrać wartości Brak tabeli i dane z przywróconą kopią serwera. 

Aby przywrócić działanie serwera, należy użyć interfejsu wiersza polecenia Azure [przywracania serwera mysql az](/cli/azure/mysql/server#az_mysql_server_restore) polecenia.

### <a name="run-the-restore-command"></a>Uruchom polecenie restore

Aby przywrócić serwer, w wierszu polecenia interfejsu wiersza polecenia Azure, wprowadź następujące polecenie:

```azurecli-interactive
az mysql server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

`az mysql server restore` Polecenie wymaga następujących parametrów:
| Ustawienie | Sugerowana wartość | Opis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Grupy zasobów, w której serwer źródłowy istnieje.  |
| name | mydemoserver-restored | Nazwa nowego serwera utworzonego za pomocą polecenie przywracania. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Wybierz punkt w czasie, aby przywrócić. Ta data i godzina musi przypadać w okresie przechowywania kopii zapasowej serwera źródłowego. Użyj formatu ISO8601 daty i godziny. Na przykład można własnej lokalnej strefie czasowej, takich jak `2018-03-13T05:59:00-08:00`. Można też używać formatu UTC Zulu, na przykład `2018-03-13T13:59:00Z`. |
| source-server | mydemoserver | Nazwa lub identyfikator serwera źródłowego, z którego ma zostać przeprowadzone przywrócenie. |

Po przywróceniu serwera do wcześniejszego punktu w czasie, jest tworzony nowy serwer. Oryginalny serwer i jej baz danych z określonego punktu w czasie są kopiowane do nowego serwera.

Lokalizacji i wartości warstwy cenowej przywróconej serwera pozostać taka sama jak oryginalny serwer. 

Po ukończeniu procesu przywracania, zlokalizuj nowy serwer i sprawdź, czy dane są przywracane zgodnie z oczekiwaniami.

## <a name="geo-restore"></a>Przywracanie Geo
Skonfigurowanie serwera na potrzeby geograficznie nadmiarowy kopii zapasowych można utworzyć nowego serwera z kopii zapasowej tego istniejącego serwera. W dowolnym regionie, że baza danych Azure dla programu MySQL jest dostępny, można utworzyć nowego serwera.  

Aby utworzyć serwer przy użyciu kopii zapasowej z magazynu geograficznie nadmiarowego, użyj interfejsu wiersza polecenia Azure `az mysql server georestore` polecenia.

> [!NOTE]
> Podczas tworzenia serwera nie może być dostępne od razu do przywracania geo. Może upłynąć kilka godzin metadane potrzebne do wypełnienia.
>

Do przywrócenia geograficznie serwera, w wierszu polecenia interfejsu wiersza polecenia Azure wprowadź następujące polecenie:

```azurecli-interactive
az mysql server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen4_8 
```
To polecenie tworzy nowy serwer o nazwie *mydemoserver georestored* w wschodnie stany USA, do której należy *myresourcegroup*. Jest ogólnego przeznaczenia, Gen 4 serwera z 8 vCores. Serwer jest tworzony z geograficznie nadmiarowego kopii zapasowych *mydemoserver*, która jest również w grupie zasobów *myresourcegroup*

Jeśli chcesz utworzyć nowy serwer w innej grupie zasobów z istniejącego serwera, następnie w `--source-server` parametru umożliwi zakwalifikowanie nazwę serwera, jak w poniższym przykładzie:

```azurecli-interactive
az mysql server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforMySQL/servers/mydemoserver" --location eastus --sku-name GP_Gen4_8

```

`az mysql server georestore` Polecenia requies następujące parametry:
| Ustawienie | Sugerowana wartość | Opis  |
| --- | --- | --- |
|resource-group| myresourcegroup | Nazwa grupy zasobów nowy serwer będzie znajdować się.|
|name | mydemoserver-georestored | Nazwa nowego serwera. |
|source-server | mydemoserver | Nazwa istniejącego serwera, którego z magazynu geograficznie nadmiarowego tworzenia kopii zapasowych są używane. |
|location | eastus | Lokalizacja nowego serwera. |
|Nazwa jednostki SKU| GP_Gen4_8 | Ten parametr określa cenową warstwy, Generowanie obliczeń i liczby vCores nowego serwera. Mapuje GP_Gen4_8 do ogólnego przeznaczenia, Gen 4 serwera z 8 vCores.|


>[!Important]
>Podczas tworzenia nowego serwera przez przywrócenie geograficznie, dziedziczy tego samego rozmiaru magazynu i warstwę cenową co serwer źródłowy. Nie można zmienić tych wartości podczas tworzenia. Po utworzeniu nowego serwera, umożliwia skalowanie rozmiaru magazynu.

Po ukończeniu procesu przywracania, zlokalizuj nowy serwer i sprawdź, czy dane są przywracane zgodnie z oczekiwaniami.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o usłudze [kopii zapasowych](concepts-backup.md).
- Dowiedz się więcej o [ciągłość prowadzenia działalności biznesowej](concepts-business-continuity.md) opcje.
