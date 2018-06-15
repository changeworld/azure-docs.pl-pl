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
ms.openlocfilehash: 0289604cce7f956406d65743d5b058ec92111724
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32182661"
---
### <a name="retrieve-host-name-ports-and-access-keys-using-azure-cli"></a>Pobieranie nazwy hosta, portów i kluczy dostępu przy użyciu interfejsu wiersza polecenia platformy Azure
Aby pobrać nazwę hosta i porty przy użyciu interfejsu wiersza polecenia platformy Azure CLI w wersji 2.0, możesz wywołać pozycję [az redis show](https://docs.microsoft.com/cli/azure/redis#az_redis_show), a aby pobrać kluczem, możesz wywołać pozycję [az redis list-keys](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys). Poniższy skrypt wywołuje te dwa polecenia i przesyła nazwę hosta, porty i klucze do konsoli.

```azurecli
#/bin/bash

# Retrieve the hostname, ports, and keys for contosoCache located in contosoGroup

# Retrieve the hostname and ports for an Azure Redis Cache instance
redis=($(az redis show --name contosoCache --resource-group contosoGroup --query [hostName,enableNonSslPort,port,sslPort] --output tsv))

# Retrieve the keys for an Azure Redis Cache instance
keys=($(az redis list-keys --name contosoCache --resource-group contosoGroup --query [primaryKey,secondaryKey] --output tsv))

# Display the retrieved hostname, keys, and ports
echo "Hostname:" ${redis[0]}
echo "Non SSL Port:" ${redis[2]}
echo "Non SSL Port Enabled:" ${redis[1]}
echo "SSL Port:" ${redis[3]}
echo "Primary Key:" ${keys[0]}
echo "Secondary Key:" ${keys[1]}
```

Aby uzyskać więcej informacji na temat tego skryptu, zobacz [Get the hostname, ports, and keys for Azure Redis Cache](../articles/redis-cache/scripts/cache-keys-ports.md) (Uzyskiwanie nazwy hosta, portów i kluczy dla usługi Azure Redis Cache). Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure w wersji 2.0, zobacz [Install Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (Instalowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0) i [Get started with Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) (Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure w wersji 2.0).
