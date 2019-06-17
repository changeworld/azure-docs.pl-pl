---
title: Tworzenie i zarządzanie nimi odczytu replik w usłudze Azure Database for MySQL
description: W tym artykule opisano sposób konfigurowania i zarządzania nią odczytu replik w usłudze Azure Database for MySQL za pomocą wiersza polecenia platformy Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 05/28/2019
ms.openlocfilehash: ba8af55f7467e361136e4b0c57c97b4fa187cec0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66304957"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-cli"></a>Jak utworzyć i zarządzać nimi odczytu replik w usłudze Azure Database for MySQL przy użyciu wiersza polecenia platformy Azure

W tym artykule dowiesz się, jak tworzyć i zarządzać nimi odczytu replik w tym samym regionie platformy Azure jako główną w usłudze Azure Database dla MySQL za pomocą wiersza polecenia platformy Azure.

> [!IMPORTANT]
> Można utworzyć odczytu repliki, w tym samym regionie co serwer główny lub w innym regionie platformy Azure wybranym. Replikacja w wielu regionach jest obecnie w publicznej wersji zapoznawczej.

## <a name="prerequisites"></a>Wymagania wstępne

- [Zainstaluj interfejs wiersza polecenia platformy Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [— Azure Database for MySQL server](quickstart-create-mysql-server-database-using-azure-portal.md) który będzie używany jako serwer główny. 

> [!IMPORTANT]
> Odczytu replik funkcja jest dostępna tylko dla usługi Azure Database dla serwerów MySQL w warstw cenowych ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci. Upewnij się, że serwer główny znajduje się w jednej z tych warstw cenowych.

## <a name="create-a-read-replica"></a>Tworzenie repliki do odczytu

Serwer repliki odczytu mogą być tworzone przy użyciu następującego polecenia:

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

`az mysql server replica create` Polecenie wymaga następujących parametrów:

| Ustawienie | Przykładowa wartość | Opis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Grupa zasobów, do której zostanie utworzona na serwerze repliki.  |
| name | mydemoreplicaserver | Nazwa nowego serwera repliki, który jest tworzony. |
| source-server | mydemoserver | Nazwa lub identyfikator istniejącego serwera głównego, aby replikować z. |

Do utworzenia wielu regionów odczytu repliki, należy użyć `--location` parametru. Poniższy przykład interfejsu wiersza polecenia tworzy repliki w regionie zachodnie stany USA.

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Repliki do odczytu są tworzone przy użyciu tej samej konfiguracji serwera jako wzorzec. Konfiguracja serwera repliki można zmienić po jego utworzeniu. Zaleca się, że konfiguracja serwera repliki należy przechowywać w większa lub równa wartości niż główny, aby upewnić się, że replika jest w stanie na bieżąco ze wzorcem.

## <a name="stop-replication-to-a-replica-server"></a>Zatrzymywanie replikacji na serwer repliki

> [!IMPORTANT]
> Zatrzymywanie replikacji na serwerze jest nieodwracalne. Po replikacji została zatrzymana między głównego i repliki, nie można cofnąć. Serwer repliki następnie staje się serwerem autonomicznym i obsługuje teraz zarówno odczytu i zapisu. Ten serwer nie wprowadzać ponownie do repliki.

Można zatrzymać replikacji na serwerze repliki odczytu przy użyciu następującego polecenia:

```azurecli-interactive
az mysql server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

`az mysql server replica stop` Polecenie wymaga następujących parametrów:

| Ustawienie | Przykładowa wartość | Opis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Grupa zasobów, w której istnieje serwer repliki.  |
| name | mydemoreplicaserver | Nazwa serwera repliki, aby zatrzymać replikację. |

## <a name="delete-a-replica-server"></a>Usuwanie serwera repliki

Usuwanie serwera repliki odczytu może odbywać się przez uruchomienie **[az mysql server delete](/cli/azure/mysql/server)** polecenia.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

## <a name="delete-a-master-server"></a>Usuń serwer główny

> [!IMPORTANT]
> Usuwanie serwera głównego zatrzymanie replikacji na wszystkich serwerach repliki i usuwa samego serwera głównego. Serwer funkcji replica stają się autonomicznymi serwerami, które obsługują teraz zarówno odczytu i zapisu.

Aby usunąć serwer główny, możesz uruchomić **[az mysql server delete](/cli/azure/mysql/server)** polecenia.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="list-replicas-for-a-master-server"></a>Lista replik dla serwera głównego

Aby wyświetlić wszystkie repliki dla danego serwera głównego, uruchom następujące polecenie: 

```azurecli-interactive
az mysql server replica list --server-name mydemoserver --resource-group myresourcegroup
```

`az mysql server replica list` Polecenie wymaga następujących parametrów:

| Ustawienie | Przykładowa wartość | Opis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Grupa zasobów, do której zostanie utworzona na serwerze repliki.  |
| nazwa-serwera | mydemoserver | Nazwa lub identyfikator serwera głównego. |

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [odczytu replik](concepts-read-replicas.md)
