---
title: Kopia zapasowa i przywracanie — narzędzie interfejsu wiersza polecenia platformy Azure — usługa Azure Database for PostgreSQL — pojedynczy serwer
description: Dowiedz się, jak ustawić konfiguracje kopii zapasowych i przywrócić serwer w usłudze Azure Database for PostgreSQL — single server przy użyciu interfejsu wiersza polecenia platformy Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: f0ea24133d7b6acdc4b099ee21a8711a2d99095d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74775708"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Jak zrobić kopię zapasową i przywrócić serwer w usłudze Azure Database for PostgreSQL — pojedynczy serwer przy użyciu interfejsu wiersza polecenia platformy Azure

Usługa Azure Database for PostgreSQL serwerów są okresowo archiwizowane, aby włączyć funkcje przywracania. Za pomocą tej funkcji można przywrócić serwer i wszystkie jego bazy danych do wcześniejszego punktu w czasie, na nowym serwerze.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, potrzebujesz:
- [Usługa Azure Database dla serwera i bazy danych PostgreSQL](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

 

> [!IMPORTANT]
> Ten przewodnik instrukcja wymaga użycia interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby potwierdzić wersję, w wierszu `az --version`polecenia interfejsu wiersza polecenia platformy Azure należy wprowadzić polecenie . Aby zainstalować lub uaktualnić, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="set-backup-configuration"></a>Ustawianie konfiguracji kopii zapasowej

Można dokonać wyboru między konfigurowaniem serwera dla lokalnie nadmiarowych kopii zapasowych lub geograficznie nadmiarowych kopii zapasowych podczas tworzenia serwera. 

> [!NOTE]
> Po utworzeniu serwera nie można przełączyć tego rodzaju nadmiarowości, która jest nadmiarowa geograficznie i lokalnie nadmiarowa.
>

Podczas tworzenia serwera za `az postgres server create` pomocą `--geo-redundant-backup` polecenia parametr decyduje o opcji nadmiarowości kopii zapasowej. Jeśli `Enabled`zostaną wykonane geograficznie nadmiarowe kopie zapasowe. Lub `Disabled` jeśli lokalnie nadmiarowe kopie zapasowe są pobierane. 

Okres przechowywania kopii zapasowej jest `--backup-retention-days`ustawiany przez parametr . 

Aby uzyskać więcej informacji na temat ustawiania tych wartości podczas tworzenia, zobacz [usługę Azure Database for PostgreSQL server CLI Quickstart](quickstart-create-server-database-azure-cli.md).

Okres przechowywania kopii zapasowej serwera można zmienić w następujący sposób:

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

W poprzednim przykładzie zmienia okres przechowywania kopii zapasowej serwera mydemoserver na 10 dni.

Okres przechowywania kopii zapasowej określa, jak daleko wstecz w czasie można pobrać przywracanie punktu w czasie, ponieważ jest ono oparte na dostępnych kopiach zapasowych. Przywracanie w czasie jest opisane w dalszej części następnej sekcji.

## <a name="server-point-in-time-restore"></a>Przywracanie punktu w czasie serwera
Serwer można przywrócić do poprzedniego punktu w czasie. Przywrócone dane są kopiowane na nowy serwer, a istniejący serwer pozostaje w stanie, w jakim jest. Na przykład jeśli tabela zostanie przypadkowo upuszczona w południe dzisiaj, można przywrócić czas tuż przed południem. Następnie można pobrać brakującą tabelę i dane z przywróconej kopii serwera. 

Aby przywrócić serwer, należy użyć polecenia [przywracania serwera poczty az az](/cli/azure/postgres/server) platformy Azure.

### <a name="run-the-restore-command"></a>Uruchamianie polecenia przywracania

Aby przywrócić serwer, w wierszu polecenia interfejsu wiersza polecenia platformy Azure wprowadź następujące polecenie:

```azurecli-interactive
az postgres server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

Polecenie `az postgres server restore` wymaga następujących parametrów:

| Ustawienie | Sugerowana wartość | Opis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Grupa zasobów, w której istnieje serwer źródłowy.  |
| name | mydemoserver-restored | Nazwa nowego serwera utworzonego za pomocą polecenie przywracania. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Wybierz punkt w czasie, do której chcesz przywrócić. Ta data i godzina musi przypadać w okresie przechowywania kopii zapasowej serwera źródłowego. Użyj formatu daty i godziny ISO8601. Na przykład można użyć własnej lokalnej strefy `2018-03-13T05:59:00-08:00`czasowej, takiej jak . Można również użyć formatu UTC Zulu, na przykład. `2018-03-13T13:59:00Z` |
| source-server | mydemoserver | Nazwa lub identyfikator serwera źródłowego, z którego ma zostać przeprowadzone przywrócenie. |

Po przywróceniu serwera do wcześniejszego punktu w czasie tworzony jest nowy serwer. Oryginalny serwer i jego bazy danych z określonego punktu w czasie są kopiowane na nowy serwer.

Wartości warstwy lokalizacji i warstwy cenowej dla przywróconego serwera pozostają takie same jak oryginalny serwer. 

Po zakończeniu procesu przywracania zlokalizuj nowy serwer i sprawdź, czy dane zostaną przywrócone zgodnie z oczekiwaniami. Nowy serwer ma tę samą nazwę logowania administratora serwera i hasło, które było prawidłowe dla istniejącego serwera w momencie zainicjowania przywracania. Hasło można zmienić na stronie **Przegląd** nowego serwera.

Nowy serwer utworzony podczas przywracania nie ma reguł zapory ani punktów końcowych usługi sieci wirtualnej, które istniały na oryginalnym serwerze. Te reguły należy skonfigurować oddzielnie dla tego nowego serwera.

## <a name="geo-restore"></a>Przywracanie geograficzne
Jeśli serwer został skonfigurowany do geograficznie nadmiarowych kopii zapasowych, można utworzyć nowy serwer z kopii zapasowej tego istniejącego serwera. Ten nowy serwer można utworzyć w dowolnym regionie, który jest dostępny w usłudze Azure Database for PostgreSQL.  

Aby utworzyć serwer przy użyciu geograficznie nadmiarowej kopii zapasowej, należy użyć polecenia interfejsu wiersza polecenia `az postgres server georestore` platformy Azure.

> [!NOTE]
> Po utworzeniu serwera może nie być natychmiast dostępny do przywracania geograficznego. Może upłynąć kilka godzin, aby wypełnić niezbędne metadane.
>

Aby przywrócić geograficznie serwer, w wierszu polecenia interfejsu wiersza polecenia platformy Azure wprowadź następujące polecenie:

```azurecli-interactive
az postgres server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen4_8 
```
To polecenie tworzy nowy serwer o nazwie *mydemoserver-georestored* we wschodnich stanach USA, który będzie należał do *myresourcegroup*. Jest to serwer ogólnego przeznaczenia Gen 4 z 8 vCorami. Serwer jest tworzony z geob nadmiarowej kopii zapasowej *mydemoserver*, który jest również w grupie zasobów *myresourcegroup*

Jeśli chcesz utworzyć nowy serwer w innej grupie zasobów niż istniejący serwer, w parameterie `--source-server` należy zakwalifikować nazwę serwera, tak jak w poniższym przykładzie:

```azurecli-interactive
az postgres server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver" --location eastus --sku-name GP_Gen4_8

```

Polecenie `az postgres server georestore` wymaga następujących parametrów:

| Ustawienie | Sugerowana wartość | Opis  |
| --- | --- | --- |
|resource-group| myresourcegroup | Nazwa grupy zasobów, do której będzie należeć nowy serwer.|
|name | mydemoserver-georestored | Nazwa nowego serwera. |
|source-server | mydemoserver | Nazwa istniejącego serwera, którego geograficznie nadmiarowe kopie zapasowe są używane. |
|location | eastus | Lokalizacja nowego serwera. |
|sku-name| GP_Gen4_8 | Ten parametr ustawia warstwę cenową, generowanie obliczeń i liczbę korów wirtualnych nowego serwera. GP_Gen4_8 mapy do ogólnego przeznaczenia, Gen 4 serwer z 8 vCores.|

Podczas tworzenia nowego serwera przez przywracanie geograficzne dziedziczy ten sam rozmiar magazynu i warstwę cenową co serwer źródłowy. Wartości tych nie można zmienić podczas tworzenia. Po utworzeniu nowego serwera jego rozmiar magazynu można skalować w górę.

Po zakończeniu procesu przywracania zlokalizuj nowy serwer i sprawdź, czy dane zostaną przywrócone zgodnie z oczekiwaniami. Nowy serwer ma tę samą nazwę logowania administratora serwera i hasło, które było prawidłowe dla istniejącego serwera w momencie zainicjowania przywracania. Hasło można zmienić na stronie **Przegląd** nowego serwera.

Nowy serwer utworzony podczas przywracania nie ma reguł zapory ani punktów końcowych usługi sieci wirtualnej, które istniały na oryginalnym serwerze. Te reguły należy skonfigurować oddzielnie dla tego nowego serwera.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [kopiach zapasowych](concepts-backup.md) usługi
- Dowiedz się więcej o [replikach](concepts-read-replicas.md)
- Dowiedz się więcej o opcjach [ciągłości biznesowej](concepts-business-continuity.md)
