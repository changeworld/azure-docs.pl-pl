---
title: Zarządzanie odczytu replik dla usługi Azure Database for PostgreSQL — jeden serwer z wiersza polecenia platformy Azure
description: Dowiedz się, jak zarządzać odczytu replik w usłudze Azure Database for PostgreSQL — jeden serwer z wiersza polecenia platformy Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 9730faf3191ef2e2bd0b6c3caddefa0492b33fc5
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510240"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli"></a>Tworzenie i zarządzanie odczytu replik z wiersza polecenia platformy Azure

W tym artykule dowiesz się, jak tworzyć i zarządzać odczytu replik w usłudze Azure Database for PostgreSQL z wiersza polecenia platformy Azure. Aby dowiedzieć się więcej na temat odczytu repliki, zobacz [Przegląd](concepts-read-replicas.md).

> [!NOTE]
> Wiersza polecenia platformy Azure nie obsługuje jeszcze tworzenia replik w różnych regionach, z serwera głównego. Aby utworzyć replikę między regionami, należy użyć [witryny Azure portal](howto-read-replicas-portal.md).

## <a name="prerequisites"></a>Wymagania wstępne
- [— Azure Database for postgresql w warstwie serwera](quickstart-create-server-up-azure-cli.md) jako serwer główny.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby sprawdzić zainstalowaną wersję, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 


## <a name="prepare-the-master-server"></a>Przygotowanie serwera głównego
Te kroki musi służyć do przygotowania serwera głównego w warstwach ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci.

`azure.replication_support` Parametru musi być równa **REPLIKI** na serwerze głównym. Po zmianie tego parametru statycznego ponownego uruchomienia serwera jest wymagany, aby zmiana zaczęła obowiązywać.

1. Ustaw `azure.replication_support` do REPLIKI.

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

2. Uruchom ponownie, aby zastosować zmianę na serwerze.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

## <a name="create-a-read-replica"></a>Tworzenie repliki do odczytu

[Tworzenie az postgres server repliki](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) polecenie wymaga następujących parametrów:

| Ustawienie | Przykładowa wartość | Opis  |
| --- | --- | --- |
| resource-group | myresourcegroup |  Grupa zasobów, w którym zostanie utworzona na serwerze repliki.  |
| name | mydemoserver-replica | Nazwa nowego serwera repliki, który jest tworzony. |
| source-server | mydemoserver | Nazwa lub zasób identyfikator istniejącego serwera głównego jako źródła replikacji. |

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Jeśli nie został ustawiony `azure.replication_support` parametr **REPLIKI** na ogólnego przeznaczenia lub zoptymalizowane pod kątem pamięci głównego serwera i uruchom ponownie serwer, otrzymasz komunikat o błędzie. Te dwa kroki należy wykonać przed przystąpieniem do tworzenia repliki.

Replika jest tworzona przy użyciu tej samej konfiguracji serwera jako wzorzec. Po utworzeniu repliki, niektóre ustawienia można zmienić niezależnie z serwera głównego: obliczenia generacji, rdzenie wirtualne, Magazyn i okres przechowywania kopii zapasowych. Warstwę cenową można zmienić niezależnie, z wyjątkiem do lub z warstwy podstawowa.

> [!IMPORTANT]
> Konfiguracja serwera głównego jest aktualizowany do nowych wartości, aktualizacja konfiguracji repliki większa lub równa wartości. Ta akcja zagwarantuje, że repliki może nadążyć za wszelkie zmiany wprowadzone do poziomu głównego.

## <a name="list-replicas"></a>Lista repliki
Można wyświetlić listę replik serwera głównego przy użyciu [az postgres server repliki listy](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list) polecenia.

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

## <a name="stop-replication-to-a-replica-server"></a>Zatrzymywanie replikacji na serwer repliki
Można zatrzymać replikację między głównym serwerem i odczytu repliki, za pomocą [az postgres server repliki stop](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop) polecenia.

Po zatrzymaniu replikację do serwera głównego i odczytu repliki, nie można cofnąć. Przeczytaj replika staje się serwer autonomiczny, który obsługuje operacje odczytu i zapisu. Serwer autonomiczny nie wprowadzać ponownie do repliki.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

## <a name="delete-a-master-or-replica-server"></a>Usuwanie serwera głównego lub replik
Aby usunąć serwer główny lub repliki, należy użyć [az postgres server delete](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete) polecenia.

Po usunięciu serwera głównego, replikacja wszystkich replik do odczytu został zatrzymany. Odczytu replik stają się autonomicznymi serwerami, które teraz obsługują operacje odczytu i zapisu.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [odczytu replik w usłudze Azure Database for PostgreSQL](concepts-read-replicas.md).