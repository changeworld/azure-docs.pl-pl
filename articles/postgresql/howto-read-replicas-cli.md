---
title: Zarządzanie replikami odczytów dla Azure Database for PostgreSQL-pojedynczego serwera z poziomu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak zarządzać replikami odczytu w ramach Azure Database for PostgreSQL-pojedynczego serwera z poziomu interfejsu wiersza polecenia platformy Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 63a8acad3c393a4c4d9c6a3b6750f1f934dad43d
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907438"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli"></a>Tworzenie replik odczytu i zarządzanie nimi za pomocą interfejsu wiersza polecenia platformy Azure

W tym artykule dowiesz się, jak tworzyć repliki odczytu i zarządzać nimi w Azure Database for PostgreSQL z poziomu interfejsu wiersza polecenia platformy Azure. Aby dowiedzieć się więcej o replikach odczytu, zobacz [Omówienie](concepts-read-replicas.md).

> [!IMPORTANT]
> Replikę odczytu można utworzyć w tym samym regionie, w którym znajduje się serwer główny, lub w dowolnym innym wybranym regionie świadczenia usługi Azure. Replikacja między regionami jest obecnie dostępna w publicznej wersji zapoznawczej.

## <a name="prerequisites"></a>Wymagania wstępne
- [Serwer Azure Database for PostgreSQL](quickstart-create-server-up-azure-cli.md) być serwerem głównym.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby sprawdzić zainstalowaną wersję, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 


## <a name="prepare-the-master-server"></a>Przygotowywanie serwera głównego
Te kroki muszą być używane do przygotowywania serwera głównego w warstwach zoptymalizowanych pod kątem Ogólnego przeznaczenia lub pamięci.

Parametr musi być ustawiony na replikę na serwerze głównym. `azure.replication_support` Po zmianie tego parametru statycznego ponowne uruchomienie serwera jest wymagane, aby zmiany zaczęły obowiązywać.

1. Ustaw `azure.replication_support` na replikę.

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

2. Uruchom ponownie, aby zastosować zmianę na serwerze.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

## <a name="create-a-read-replica"></a>Tworzenie repliki do odczytu

Polecenie [AZ Postgres Server Replica Create](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) wymaga następujących parametrów:

| Ustawienie | Przykładowa wartość | Opis  |
| --- | --- | --- |
| resource-group | myresourcegroup |  Grupa zasobów, w której zostanie utworzony serwer repliki.  |
| name | mydemoserver — replika | Nazwa nowego serwera repliki, który jest tworzony. |
| source-server | mydemoserver | Nazwa lub identyfikator zasobu istniejącego serwera głównego, z którego ma być wykonywana replikacja. |

W poniższym przykładzie interfejsu wiersza polecenia replika jest tworzona w tym samym regionie co główny.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Aby utworzyć replikę odczytu między regionami, `--location` Użyj parametru. Poniższy przykład interfejsu wiersza polecenia tworzy replikę w regionie zachodnie stany USA.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Aby dowiedzieć się więcej na temat regionów, w których można utworzyć replikę, zapoznaj się z [artykułem dotyczącym pojęć dotyczących repliki](concepts-read-replicas.md). 

Jeśli nie ustawisz `azure.replication_support` parametru na replikę na serwerze głównym ogólnego przeznaczenia lub zoptymalizowanym pod kątem pamięci i ponownie uruchomiono serwer, zostanie wyświetlony komunikat o błędzie. Przed utworzeniem repliki wykonaj te dwa kroki.

Replika jest tworzona przy użyciu tych samych ustawień obliczeniowych i magazynu co główny. Po utworzeniu repliki można zmienić kilka ustawień niezależnie od serwera głównego: generowanie obliczeń, rdzeni wirtualnych, magazyn i okres przechowywania zapasowego. Warstwę cenową można także zmienić niezależnie, z wyjątkiem warstwy Podstawowa lub z niej.

> [!IMPORTANT]
> Przed zaktualizowaniem ustawienia serwera głównego do nowej wartości należy zaktualizować ustawienie repliki na wartość równą lub większą. Ta akcja ułatwia przeprowadzenie replikacji ze wszystkimi zmianami wprowadzonymi do wzorca.

## <a name="list-replicas"></a>Wyświetlanie listy replik
Listę replik serwera głównego można wyświetlić za pomocą polecenia [AZ Postgres Server Replica list](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list) .

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

## <a name="stop-replication-to-a-replica-server"></a>Zatrzymywanie replikacji na serwer repliki
Można zatrzymać replikację między serwerem głównym a repliką odczytu przy użyciu polecenia [AZ Postgres Server Replica Stop](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop) .

Po zatrzymaniu replikacji na serwer główny i replikę odczytu nie można jej cofnąć. Replika odczytu jest autonomicznym serwerem, który obsługuje zarówno operacje odczytu, jak i zapisu. Serwer autonomiczny nie może zostać ponownie utworzony w replice.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

## <a name="delete-a-master-or-replica-server"></a>Usuwanie serwera głównego lub repliki
Aby usunąć serwer główny lub serwera repliki, użyj polecenia [AZ Postgres Server Delete](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete) .

Po usunięciu serwera głównego replikacja do wszystkich replik odczytu zostanie zatrzymana. Repliki odczytu stają się autonomicznymi serwerami, które teraz obsługują operacje odczytu i zapisu.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o odczytaniu [replik w Azure Database for PostgreSQL](concepts-read-replicas.md).
* Dowiedz się, jak [tworzyć repliki odczytu i zarządzać nimi w Azure Portal](howto-read-replicas-portal.md).