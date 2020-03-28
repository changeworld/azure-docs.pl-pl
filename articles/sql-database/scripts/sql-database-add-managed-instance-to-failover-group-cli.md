---
title: Przykład interfejsu wiersza polecenia — grupa trybu failover — wystąpienie zarządzanego usługi Azure SQL Database
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure, aby utworzyć wystąpienie zarządzanego usługi Azure SQL Database, dodać go do grupy trybu failover i przetestować przetwórstwo awaryjne.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: 8ffe40662ffaf8a1fb35a3d31acfaea78ea0fbeb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061914"
---
# <a name="use-cli-to-add-an-azure-sql-database-managed-instance-to-a-failover-group"></a>Dodawanie wystąpienia zarządzanego usługi Azure SQL Database za pomocą interfejsu wiersza polecenia do grupy trybu failover

W tym przykładzie interfejsu wiersza polecenia platformy Azure tworzy dwa wystąpienia zarządzane, dodaje je do grupy trybu failover, a następnie testuje przebłaganie w pracy awaryjnej z podstawowego wystąpienia zarządzanego do pomocniczego wystąpienia zarządzanego.

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Przykładowy skrypt

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Uruchamianie skryptu

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-cli.sh "Add managed instance to a failover group")]

### <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby. Należy usunąć grupę zasobów dwa razy. Usunięcie grupy zasobów po raz pierwszy spowoduje usunięcie wystąpienia zarządzanego i klastrów `az group delete : Long running operation failed with status 'Conflict'.`wirtualnych, ale spowoduje niepowodzenie z komunikatem o błędzie . Uruchom polecenie usuwania grupy az po raz drugi, aby usunąć wszystkie zasoby resztkowe, a także grupę zasobów.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Przykładowy numer referencyjny

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| | |
|---|---|
| [sieć az vnet](/cli/azure/network/vnet) | Polecenia sieci wirtualnej.  |
| [podsieci sieci az sieci wirtualnej](/cli/azure/network/vnet/subnet) | Polecenia podsieci sieci wirtualnej. |
| [sieć az nsg](/cli/azure/network/nsg) | Polecenia sieciowej grupy zabezpieczeń. |
| [tabela tras sieciowych az](/cli/azure/network/route-table) | Polecenia tabeli marszruty. |
| [az sql mi](/cli/azure/sql/mi) | Polecenia wystąpienia zarządzanego. |
| [az sieć public-ip](/cli/azure/network/public-ip) | Sieciowe polecenia publicznego adresu IP. |
| [az sieć vnet-brama](/cli/azure/network/vnet-gateway) | Polecenia bramy sieci wirtualnej. |
| [az sql wystąpienie-praca awaryjna-grupa](/cli/azure/sql/instance-failover-group) | Polecenia grupy trybu failover wystąpienia zarządzanego. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi SQL Database można znaleźć w [dokumentacji usługi Azure SQL Database](../sql-database-cli-samples.md).
