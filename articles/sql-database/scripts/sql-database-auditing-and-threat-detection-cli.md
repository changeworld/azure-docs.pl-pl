---
title: Przykład interfejsu wiersza polecenia inspekcji i zaawansowanej ochrony przed zagrożeniami — baza danych SQL azure
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure do konfigurowania inspekcji i zaawansowanej ochrony przed zagrożeniami w bazie danych SQL usługi Azure
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: security
ms.devlang: azurecli
ms.topic: sample
author: ronitr
ms.author: ronitr
ms.reviewer: carlrab, vanto
ms.date: 08/05/2019
ms.openlocfilehash: 9f15c4a8b0410003bccd9fb42aa2c34c42d9b56b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061884"
---
# <a name="use-cli-to-configure-sql-database-auditing-and-advanced-threat-protection"></a>Konfigurowanie inspekcji bazy danych SQL i zaawansowanej ochrony przed zagrożeniami za pomocą interfejsu wiersza polecenia

W tym przykładowym przykładzie skryptu interfejsu wiersza polecenia platformy Azure skonfigurowano inspekcję bazy danych SQL i zaawansowaną ochronę przed zagrożeniami.

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten temat będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Przykładowy skrypt

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Uruchamianie skryptu

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/database-auditing-and-threat-detection/database-auditing-and-threat-detection.sh "Configure auditing and threat detection")]

### <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Przykładowy numer referencyjny

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| | |
|---|---|
| [az sql db audit-policy](/cli/azure/sql/db/audit-policy) | Ustawia zasady inspekcji dla bazy danych. |
| [az sql db threat-policy az sql db threat-policy az sql db threat-policy az](/cli/azure/sql/db/threat-policy) | Ustawia zasady zaawansowanej ochrony przed zagrożeniami w bazie danych. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi SQL Database można znaleźć w [dokumentacji usługi Azure SQL Database](../sql-database-cli-samples.md).
