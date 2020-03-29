---
title: Zarządzanie replikami odczytu — interfejs wiersza polecenia interfejsu wiersza polecenia sieci web, interfejs API REST — usługa Azure Database for PostgreSQL — pojedynczy serwer
description: Dowiedz się, jak zarządzać replikami odczytu w bazie danych Azure dla postgreSQL — pojedynczy serwer z interfejsu API interfejsu wiersza polecenia platformy Azure i interfejsu SPOCZYN-u
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: b10ac3b4bc9dacd723b8b1265911df721b781189
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774804"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>Tworzenie replik odczytu i zarządzanie nimi z interfejsu API interfejsu wiersza polecenia platformy Azure, interfejsu API REST

W tym artykule dowiesz się, jak tworzyć repliki odczytu i zarządzać nimi w usłudze Azure Database for PostgreSQL przy użyciu interfejsu API platformy Azure i interfejsu API REST. Aby dowiedzieć się więcej o replikach odczytu, zobacz [omówienie](concepts-read-replicas.md).

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Repliki odczytu i zarządzanie nimi można tworzyć przy użyciu interfejsu wiersza polecenia platformy Azure.

### <a name="prerequisites"></a>Wymagania wstępne

- [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Serwer usługi Azure Database for PostgreSQL](quickstart-create-server-up-azure-cli.md) ma być serwerem głównym.


### <a name="prepare-the-master-server"></a>Przygotowanie serwera głównego
Te kroki muszą służyć do przygotowania serwera głównego w warstwach ogólnego przeznaczenia lub zoptymalizowane pod kątem pamięci.

Parametr `azure.replication_support` musi być ustawiony na **REPLIKA** na serwerze głównym. Po zmianie tego parametru statycznego, ponowne uruchomienie serwera jest wymagane, aby zmiana została wniesienia.

1. Ustaw `azure.replication_support` na REPLIKA.

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

> [!NOTE]
> Jeśli zostanie wyświetlony błąd "Nieprawidłowa wartość podana" podczas próby ustawienia azure.replication_support z interfejsu wiersza polecenia platformy Azure, prawdopodobnie serwer ma już domyślnie ustawioną replikę. Błąd uniemożliwia poprawne odzwierciedlenie tego ustawienia na nowszych serwerach, na których replika jest domyślnie wewnętrzną. <br><br>
> Można pominąć kroki wzorca przygotowania i przejść do utworzenia repliki. <br><br>
> Jeśli chcesz potwierdzić, że serwer należy do tej kategorii, odwiedź stronę replikacji serwera w witrynie Azure portal. "Wyłącz replikację" zostanie wyszarzony, a na pasku narzędzi "Dodaj replikę".

2. Uruchom ponownie serwer, aby zastosować zmianę.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>Tworzenie repliki odczytu

Polecenie [tworzenia repliki serwera az postgres](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) wymaga następujących parametrów:

| Ustawienie | Przykładowa wartość | Opis  |
| --- | --- | --- |
| resource-group | myresourcegroup |  Grupa zasobów, w której zostanie utworzony serwer repliki.  |
| name | replika mydemoserver | Nazwa nowego serwera replik, który jest tworzony. |
| source-server | mydemoserver | Nazwa lub identyfikator zasobu istniejącego serwera głównego do replikacji. |

W poniższym przykładzie interfejsu wiersza polecenia replika jest tworzona w tym samym regionie co wzorzec.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Aby utworzyć replikę odczytu `--location` międzyregionami, należy użyć parametru. Poniższy przykład interfejsu wiersza polecenia tworzy replikę w zachodnie stany USA.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Aby dowiedzieć się więcej o regionach, w których można utworzyć replikę, odwiedź [artykuł o pojęciach dotyczących repliki do odczytu](concepts-read-replicas.md). 

Jeśli `azure.replication_support` parametr nie został ustawiony na **REPLIKA** na serwerze głównym ogólnego przeznaczenia lub zoptymalizowanym pod kątem pamięci i ponownie uruchomiony serwer, zostanie wyświetlony błąd. Wykonaj te dwa kroki przed utworzeniem repliki.

Replika jest tworzona przy użyciu tych samych ustawień obliczeniowych i magazynu, co wzorzec. Po utworzeniu repliki można zmienić kilka ustawień niezależnie od serwera głównego: generowanie obliczeń, wirtualne, magazynowanie i okres przechowywania kopii zapasowej. Warstwę cenową można również zmienić niezależnie, z wyjątkiem warstwy Podstawowa lub podstawowa.

> [!IMPORTANT]
> Przed zaktualizowaniem ustawienia serwera głównego do nowej wartości należy zaktualizować ustawienie repliki do wartości równej lub większej. Ta akcja pomaga repliki nadążyć za wszelkimi zmianami wprowadzonymi do wzorca.

### <a name="list-replicas"></a>Lista replik
Listę replik serwera głównego można wyświetlić za pomocą polecenia [az postgres server replica list.](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list)

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>Zatrzymywać replikację na serwerze repliki
Replikację między serwerem głównym a repliką odczytu można zatrzymać za pomocą polecenia [zatrzymania repliki serwera az postgres.](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop)

Po zatrzymaniu replikacji na serwerze głównym i repliki odczytu nie można jej cofnąć. Replika odczytu staje się autonomicznym serwerem, który obsługuje zarówno odczyty, jak i zapisy. Nie można ponownie przekształcić serwera autonomicznego w replikę.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-master-or-replica-server"></a>Usuwanie serwera wzorca lub repliki
Aby usunąć serwer wzorca lub repliki, należy użyć polecenia [delete serwera az postgres.](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete)

Po usunięciu serwera głównego replikacja wszystkich replik odczytu jest zatrzymywana. Repliki odczytu stają się autonomicznymi serwerami, które teraz obsługują zarówno odczyty, jak i zapisy.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>Interfejs API REST
Repliki odczytu można tworzyć i zarządzać nimi za pomocą [interfejsu API usługi Azure REST](/rest/api/azure/).

### <a name="prepare-the-master-server"></a>Przygotowanie serwera głównego
Te kroki muszą służyć do przygotowania serwera głównego w warstwach ogólnego przeznaczenia lub zoptymalizowane pod kątem pamięci.

Parametr `azure.replication_support` musi być ustawiony na **REPLIKA** na serwerze głównym. Po zmianie tego parametru statycznego, ponowne uruchomienie serwera jest wymagane, aby zmiana została wniesienia.

1. Ustaw `azure.replication_support` na REPLIKA.

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

   ```JSON
   {
    "properties": {
    "value": "replica"
    }
   }
   ```

2. [Uruchom ponownie serwer,](/rest/api/postgresql/servers/restart) aby zastosować zmianę.

   ```http
   POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/restart?api-version=2017-12-01
   ```

### <a name="create-a-read-replica"></a>Tworzenie repliki odczytu
Replikę odczytu można utworzyć przy użyciu [interfejsu API tworzenia:](/rest/api/postgresql/servers/create)

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> Aby dowiedzieć się więcej o regionach, w których można utworzyć replikę, odwiedź [artykuł o pojęciach dotyczących repliki do odczytu](concepts-read-replicas.md). 

Jeśli `azure.replication_support` parametr nie został ustawiony na **REPLIKA** na serwerze głównym ogólnego przeznaczenia lub zoptymalizowanym pod kątem pamięci i ponownie uruchomiony serwer, zostanie wyświetlony błąd. Wykonaj te dwa kroki przed utworzeniem repliki.

Replika jest tworzona przy użyciu tych samych ustawień obliczeniowych i magazynu, co wzorzec. Po utworzeniu repliki można zmienić kilka ustawień niezależnie od serwera głównego: generowanie obliczeń, wirtualne, magazynowanie i okres przechowywania kopii zapasowej. Warstwę cenową można również zmienić niezależnie, z wyjątkiem warstwy Podstawowa lub podstawowa.


> [!IMPORTANT]
> Przed zaktualizowaniem ustawienia serwera głównego do nowej wartości należy zaktualizować ustawienie repliki do wartości równej lub większej. Ta akcja pomaga repliki nadążyć za wszelkimi zmianami wprowadzonymi do wzorca.

### <a name="list-replicas"></a>Lista replik
Listę replik serwera głównego można wyświetlić za pomocą [interfejsu API listy replik:](/rest/api/postgresql/replicas/listbyserver)

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Zatrzymywać replikację na serwerze repliki
Replikację między serwerem głównym a repliką odczytu można zatrzymać przy użyciu [interfejsu API aktualizacji](/rest/api/postgresql/servers/update).

Po zatrzymaniu replikacji na serwerze głównym i repliki odczytu nie można jej cofnąć. Replika odczytu staje się autonomicznym serwerem, który obsługuje zarówno odczyty, jak i zapisy. Nie można ponownie przekształcić serwera autonomicznego w replikę.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-master-or-replica-server"></a>Usuwanie serwera wzorca lub repliki
Aby usunąć serwer wzorca lub repliki, należy użyć [interfejsu API usuwania:](/rest/api/postgresql/servers/delete)

Po usunięciu serwera głównego replikacja wszystkich replik odczytu jest zatrzymywana. Repliki odczytu stają się autonomicznymi serwerami, które teraz obsługują zarówno odczyty, jak i zapisy.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [odczytywania replik w usłudze Azure Database for PostgreSQL](concepts-read-replicas.md).
* Dowiedz się, jak [tworzyć repliki odczytu i zarządzać nimi w portalu Azure.](howto-read-replicas-portal.md)
