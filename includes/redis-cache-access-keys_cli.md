---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 03/28/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 778151e401624398b70101a242e4cf0be8e0a1b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485360"
---
### <a name="retrieve-host-name-ports-and-access-keys-using-azure-cli"></a>Pobieranie nazwy hosta, portów i kluczy dostępu przy użyciu interfejsu wiersza polecenia platformy Azure

Można pobrać nazwy hosta i porty przy użyciu wiersza polecenia platformy Azure, możesz wywołać [az redis show](https://docs.microsoft.com/cli/azure/redis#az_redis_show)oraz aby pobrać klucze, można wywołać [az redis list-keys](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys). Poniższy skrypt wywołuje te dwa polecenia i zwraca nazwę hosta, porty i klucze do konsoli.

```azurecli
#/bin/bash

# Retrieve the hostname, ports, and keys for contosoCache located in contosoGroup

# Retrieve the hostname and ports for an Azure Cache for Redis instance
redis=($(az redis show --name contosoCache --resource-group contosoGroup --query [hostName,enableNonSslPort,port,sslPort] --output tsv))

# Retrieve the keys for an Azure Cache for Redis instance
keys=($(az redis list-keys --name contosoCache --resource-group contosoGroup --query [primaryKey,secondaryKey] --output tsv))

# Display the retrieved hostname, keys, and ports
echo "Hostname:" ${redis[0]}
echo "Non SSL Port:" ${redis[2]}
echo "Non SSL Port Enabled:" ${redis[1]}
echo "SSL Port:" ${redis[3]}
echo "Primary Key:" ${keys[0]}
echo "Secondary Key:" ${keys[1]}
```

Aby uzyskać więcej informacji na temat tego skryptu, zobacz [uzyskać nazwę hosta, portów i kluczy dla usługi Azure Cache Redis](../articles/azure-cache-for-redis/scripts/cache-keys-ports.md). Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [interfejsu wiersza polecenia platformy Azure Zainstaluj](https://docs.microsoft.com/cli/azure/install-azure-cli) i [Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).
