---
title: Przykład interfejsu wiersza polecenia — tworzenie wystąpienia zarządzanego w bazie danych SQL usługi Azure
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure w celu utworzenia wystąpienia zarządzanego w bazie danych SQL usługi Azure
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: 998bd8e39863dd9520d1a05e7fff52095c3be6c1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067442"
---
# <a name="use-cli-to-create-an-azure-sql-database-managed-instance"></a>Tworzenie wystąpienia zarządzanego usługi Azure SQL Database za pomocą interfejsu wiersza

Ten przykład skryptu interfejsu wiersza polecenia platformy Azure tworzy wystąpienie zarządzanej usługi Azure SQL Database w dedykowanej podsieci w nowej sieci wirtualnej. Konfiguruje również tabelę tras i grupę zabezpieczeń sieci dla sieci wirtualnej. Po pomyślnym uruchomieniu skryptu można uzyskać dostęp do wystąpienia zarządzanego z sieci wirtualnej lub ze środowiska lokalnego. Zobacz [Konfigurowanie maszyny Wirtualnej platformy Azure w celu łączenia się z wystąpieniem zarządzanym bazy danych SQL platformy Azure](../sql-database-managed-instance-configure-vm.md) i [konfigurowanie połączenia typu "punkt-lokacja" z wystąpieniem zarządzanym bazy danych SQL platformy Azure z lokalnego](../sql-database-managed-instance-configure-p2s.md)wystąpienia.

> [!IMPORTANT]
> Aby uzyskać ograniczenia, zobacz [obsługiwane regiony](../sql-database-managed-instance-resource-limits.md#supported-regions) i [obsługiwane typy subskrypcji](../sql-database-managed-instance-resource-limits.md#supported-subscription-types).

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Przykładowy skrypt

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Uruchamianie skryptu

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/managed-instance/create-managed-instance.sh "Create managed instance")]

### <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Przykładowy numer referencyjny

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| | |
|---|---|
| [sieć az vnet](/cli/azure/network/vnet) | Polecenia sieci wirtualnej. |
| [podsieci sieci az sieci wirtualnej](/cli/azure/network/vnet/subnet) | Polecenia podsieci sieci wirtualnej. |
| [tabela tras sieciowych az](/cli/azure/network/route-table) | Polecenia tabeli tras sieciowych. |
| [az sql mi](/cli/azure/sql/mi) | Polecenia wystąpienia zarządzanego. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi SQL Database można znaleźć w [dokumentacji usługi Azure SQL Database](../sql-database-cli-samples.md).
