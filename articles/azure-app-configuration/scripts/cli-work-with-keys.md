---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — praca z parami klucz-wartość w magazynie usługi Azure App Configuration | Microsoft Docs
description: Zawiera informacje na temat pracy z parami klucz-wartość w magazynie usługi Azure App Configuration
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: azure-app-configuration
ms.date: 11/08/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 51697aa426a638ad536390c4114df5faa18a41ef
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184868"
---
# <a name="work-with-key-values-in-an-azure-app-configuration-store"></a>Praca z parami klucz-wartość w magazynie usługi Azure App Configuration

Ten przykładowy skrypt tworzy nową parę klucz-wartość w magazynie usługi Azure App Configuration, pokazuje listę wszystkich par klucz-wartość, aktualizuje wartość nowo utworzonego klucza i na końcu usuwa go.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Należy najpierw zainstalować rozszerzenie interfejsu wiersza polecenia usługi Azure App Configuration, wykonując następujące polecenie:

        az extension add -n appconfig

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

Ten skrypt używa następujących poleceń do obsługi wartości kluczy w magazynie konfiguracji aplikacji. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az appconfig kv set](/cli/azure/ext/appconfig/appconfig/kv#ext-appconfig-az-appconfig-kv-set) | Tworzy lub aktualizuje parę klucz-wartość. |
| [az appconfig kv list](/cli/azure/ext/appconfig/appconfig/kv#ext-appconfig-az-appconfig-kv-list) | Wyświetla listę wartości kluczy w magazynie konfiguracji aplikacji. |
| [az appconfig kv delete](/cli/azure/ext/appconfig/appconfig/kv#ext-appconfig-az-appconfig-kv-delete) | Usuwa parę klucz-wartość. |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia konfiguracji aplikacji można znaleźć w [przykładach interfejsu wiersza polecenia konfiguracji aplikacji platformy Azure](../cli-samples.md).
