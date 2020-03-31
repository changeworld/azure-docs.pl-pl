---
title: Przykład interfejsu wiersza polecenia — włącz funkcję BYOK TDE — wystąpienie zarządzanej bazy danych Azure SQL
description: Dowiedz się, jak skonfigurować wystąpienie zarządzanego sql platformy Azure, aby rozpocząć korzystanie z szyfrowania danych (TDE) byok dla szyfrowania w spoczynku przy użyciu programu PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: azurecli
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 11/05/2019
ms.openlocfilehash: 8e8c0e2db1f87cca52c44d33ce14d7ce4f00e895
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061731"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Zarządzanie funkcją Transparent Data Encryption w wystąpieniu zarządzanym przy użyciu własnego klucza z usługi Azure Key Vault

Ten przykład skryptu interfejsu wiersza polecenia platformy Azure konfiguruje przezroczyste szyfrowanie danych (TDE) za pomocą klucza zarządzanego przez klienta dla wystąpienia zarządzanego sql platformy Azure przy użyciu klucza z usługi Azure Key Vault. Jest to często określane jako Bring Your Own Key scenariusz TDE. Aby dowiedzieć się więcej o TDE z kluczem zarządzanym przez klienta, zobacz [TDE Bring Your Own Key to Azure SQL](../transparent-data-encryption-byok-azure-sql.md).

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Przykładowy skrypt

### <a name="prerequisites"></a>Wymagania wstępne

Istniejące wystąpienie zarządzane, zobacz Tworzenie wystąpienia zarządzanego usługi Azure SQL Database [za pomocą interfejsu wiersza polecenia azure.](sql-database-create-configure-managed-instance-cli.md)

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Uruchamianie skryptu

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.sh "Set up BYOK TDE for SQL Managed Instance")]

### <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Przykładowy numer referencyjny

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| | |
|---|---|
| [az sql db](/cli/azure/sql/db) | Polecenia bazy danych. |
| [az sql grupa trybu failover](/cli/azure/sql/failover-group) | Polecenia grupy trybu failover. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi SQL Database można znaleźć w [dokumentacji usługi Azure SQL Database](../sql-database-cli-samples.md).
