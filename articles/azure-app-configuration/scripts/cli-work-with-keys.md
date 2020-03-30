---
title: Przykład skryptu interfejsu wiersza polecenia platformy Azure — praca z wartościami kluczy w sklepie App Configuration Store
titleSuffix: Azure App Configuration
description: Tworzenie, wyświetlanie, aktualizowanie i usuwanie kluczowych wartości z magazynu konfiguracji aplikacji za pomocą skryptu interfejsu wiersza polecenia platformy Azure
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 1a4edabe666a554ccd01d110f0f71226221dfc67
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77523655"
---
# <a name="work-with-key-values-in-an-azure-app-configuration-store"></a>Praca z parami klucz-wartość w magazynie usługi Azure App Configuration

Ten przykładowy skrypt pokazuje, jak:
* Tworzenie nowej pary klucz-wartość
* Wyświetl listę wszystkich istniejących par klucz-wartość
* Aktualizowanie wartości nowo utworzonego klucza
* Usuwanie nowej pary klucz-wartość

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Przykładowy skrypt

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
newKey="TestKey"
refKey="KeyVaultReferenceTestKey"
uri="[URL to value stored in Key Vault]"
uri2="[URL to another value stored in Key Vault]"

# Create a new key-value 
az appconfig kv set --name $appConfigName --key $newKey --value "Value 1"

# List current key-values
az appconfig kv list --name $appConfigName

# Update new key's value
az appconfig kv set --name $appConfigName --key $newKey --value "Value 2"

# List current key-values
az appconfig kv list --name $appConfigName

# Create a new key-value referencing a value stored in Azure Key Vault
az appconfig kv set --name $appConfigName --key $refKey --content-type "application/vnd.microsoft.appconfig.keyvaultref+json;charset=utf-8" --value "{\"uri\":\"$uri\"}"

# List current key-values
az appconfig kv list --name $appConfigName

# Update Key Vault reference
az appconfig kv set --name $appConfigName --key $refKey --value "{\"uri\":\"$uri2\"}"

# List current key-values
az appconfig kv list --name $appConfigName

# Delete new key
az appconfig kv delete  --name $appConfigName --key $newKey

# Delete Key Vault reference
az appconfig kv delete --name $appConfigName --key $refKey

# List current key-values
az appconfig kv list --name $appConfigName
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tej tabeli wymieniono polecenia używane w naszym przykładowym skrypcie. 

| Polecenie | Uwagi |
|---|---|
| [az appconfig kv set](/cli/azure/appconfig/kv#az-appconfig-kv-set) | Utwórz lub zaktualizuj parę klucz-wartość. |
| [az appconfig kv list](/cli/azure/appconfig/kv#az-appconfig-kv-list) | Lista par klucz-wartość w magazynie konfiguracji aplikacji. |
| [az appconfig kv delete](/cli/azure/appconfig/kv#az-appconfig-kv-delete) | Usuń parę klucz-wartość. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia konfiguracji aplikacji można znaleźć w [przykładach interfejsu wiersza polecenia konfiguracji aplikacji platformy Azure](../cli-samples.md).
