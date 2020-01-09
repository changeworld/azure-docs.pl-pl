---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — Importowanie do magazynu konfiguracji aplikacji
titleSuffix: Azure App Configuration
description: Zawiera informacje i przykładowe skrypty importowania do magazynu usługi Azure App Configuration
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: azure-app-configuration
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 28c04859f23b3a560166aa62ac903e1204fc5d97
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75413287"
---
# <a name="import-to-an-azure-app-configuration-store"></a>Importowanie do magazynu usługi Azure App Configuration

Ten przykładowy skrypt importuje ustawienia klucz-wartość do magazynu konfiguracji aplikacji platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia platformy Azure i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2,0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Aby zainstalować lub uaktualnić, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Należy najpierw zainstalować rozszerzenie interfejsu wiersza polecenia usługi Azure App Configuration, wykonując następujące polecenie:

        az extension add -n appconfig

## <a name="sample-script"></a>Przykładowy skrypt

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --source file --path ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń do zaimportowania do magazynu konfiguracji aplikacji. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [zaimportu AZ AppConfig KV](/cli/azure/ext/appconfig/appconfig/kv#ext-appconfig-az-appconfig-kv-import) | Importuje do zasobu magazynu konfiguracji aplikacji. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia konfiguracji aplikacji można znaleźć w [przykładach interfejsu wiersza polecenia konfiguracji aplikacji platformy Azure](../cli-samples.md).
