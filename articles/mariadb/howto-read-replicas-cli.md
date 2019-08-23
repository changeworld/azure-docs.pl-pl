---
title: Tworzenie replik odczytu i zarządzanie nimi w Azure Database for MariaDB
description: W tym artykule opisano sposób konfigurowania replik odczytu i zarządzania nimi w Azure Database for MariaDB przy użyciu interfejsu wiersza polecenia platformy Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 7944f985f2317690f3a13add783192c49acbe22f
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907659"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-cli"></a>Tworzenie replik odczytu i zarządzanie nimi w Azure Database for MariaDB przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule dowiesz się, jak tworzyć repliki odczytu i zarządzać nimi w tym samym regionie platformy Azure, co wzorzec w usłudze Azure Database for MariaDB przy użyciu interfejsu wiersza polecenia platformy Azure.

> [!IMPORTANT]
> Replikę odczytu można utworzyć w tym samym regionie, w którym znajduje się serwer główny, lub w dowolnym innym wybranym regionie świadczenia usługi Azure. Repliki odczytu (ten sam region i wiele regionów) są obecnie dostępne w publicznej wersji zapoznawczej.

## <a name="prerequisites"></a>Wymagania wstępne

- [Zainstaluj interfejs wiersza polecenia platformy Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Serwer Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md) , który będzie używany jako serwer główny. 

> [!IMPORTANT]
> Funkcja odczytu repliki jest dostępna tylko dla serwerów Azure Database for MariaDB w warstwach cenowych Ogólnego przeznaczenia lub zoptymalizowanych pod kątem pamięci. Upewnij się, że serwer główny znajduje się w jednej z tych warstw cenowych.

## <a name="create-a-read-replica"></a>Tworzenie repliki do odczytu

Serwer repliki odczytu mogą być tworzone przy użyciu następującego polecenia:

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

`az mariadb server replica create` Polecenie wymaga następujących parametrów:

| Ustawienie | Przykładowa wartość | Opis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Grupa zasobów, do której zostanie utworzona na serwerze repliki.  |
| name | mydemoreplicaserver | Nazwa nowego serwera repliki, który jest tworzony. |
| source-server | mydemoserver | Nazwa lub identyfikator istniejącego serwera głównego, aby replikować z. |

Aby utworzyć replikę odczytu między regionami, `--location` Użyj parametru. Poniższy przykład interfejsu wiersza polecenia tworzy replikę w regionie zachodnie stany USA.

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Aby dowiedzieć się więcej na temat regionów, w których można utworzyć replikę, zapoznaj się z [artykułem dotyczącym pojęć dotyczących repliki](concepts-read-replicas.md). 

> [!NOTE]
> Repliki do odczytu są tworzone przy użyciu tej samej konfiguracji serwera jako wzorzec. Konfiguracja serwera repliki można zmienić po jego utworzeniu. Zaleca się, że konfiguracja serwera repliki należy przechowywać w większa lub równa wartości niż główny, aby upewnić się, że replika jest w stanie na bieżąco ze wzorcem.

## <a name="stop-replication-to-a-replica-server"></a>Zatrzymywanie replikacji na serwer repliki

> [!IMPORTANT]
> Zatrzymywanie replikacji na serwerze jest nieodwracalne. Po replikacji została zatrzymana między głównego i repliki, nie można cofnąć. Serwer repliki następnie staje się serwerem autonomicznym i obsługuje teraz zarówno odczytu i zapisu. Ten serwer nie wprowadzać ponownie do repliki.

Można zatrzymać replikacji na serwerze repliki odczytu przy użyciu następującego polecenia:

```azurecli-interactive
az mariadb server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

`az mariadb server replica stop` Polecenie wymaga następujących parametrów:

| Ustawienie | Przykładowa wartość | Opis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Grupa zasobów, w której istnieje serwer repliki.  |
| name | mydemoreplicaserver | Nazwa serwera repliki, aby zatrzymać replikację. |

## <a name="delete-a-replica-server"></a>Usuwanie serwera repliki

Usuwanie serwera repliki odczytu można wykonać, uruchamiając polecenie **[AZ MariaDB Server Delete](/cli/azure/mariadb/server)** .

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

## <a name="delete-a-master-server"></a>Usuń serwer główny

> [!IMPORTANT]
> Usuwanie serwera głównego zatrzymanie replikacji na wszystkich serwerach repliki i usuwa samego serwera głównego. Serwer funkcji replica stają się autonomicznymi serwerami, które obsługują teraz zarówno odczytu i zapisu.

Aby usunąć serwer główny, można uruchomić polecenie **[AZ MariaDB Server Delete](/cli/azure/mariadb/server)** .

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="list-replicas-for-a-master-server"></a>Lista replik dla serwera głównego

Aby wyświetlić wszystkie repliki dla danego serwera głównego, uruchom następujące polecenie: 

```azurecli-interactive
az mariadb server replica list --server-name mydemoserver --resource-group myresourcegroup
```

`az mariadb server replica list` Polecenie wymaga następujących parametrów:

| Ustawienie | Przykładowa wartość | Opis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Grupa zasobów, do której zostanie utworzona na serwerze repliki.  |
| nazwa-serwera | mydemoserver | Nazwa lub identyfikator serwera głównego. |

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [odczytu replik](concepts-read-replicas.md)
