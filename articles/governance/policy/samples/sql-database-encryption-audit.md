---
title: Przykładowe — Inspekcja przezroczystego szyfrowania danych dla SQL Database
description: Ta przykładowa definicja zasad przeprowadza inspekcję, jeśli w usłudze SQL Database nie włączono przezroczystego szyfrowania danych.
author: DCtheGeek
ms.service: azure-policy
ms.topic: sample
ms.date: 01/23/2019
ms.author: dacoulte
ms.openlocfilehash: de7819f43b2d0ce4d6d047b324db94d3e5f85eec
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981323"
---
# <a name="sample---audit-sql-database-encryption"></a>Próbkowanie — Inspekcja szyfrowania bazy danych SQL

Ta wbudowana Inspekcja zasad, jeśli usługa SQL Database nie ma włączonej przezroczystego szyfrowania danych.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Przykładowy szablon

[!code-json[main](../../../../policy-templates/samples/SQL/audit-sql-db-tde-status/azurepolicy.json "Audit TDE for SQL Database")]

Ten szablon można wdrożyć za pomocą [Azure Portal](#deploy-with-the-portal)za pomocą [programu PowerShell](#deploy-with-powershell) lub [interfejsu wiersza polecenia platformy Azure](#deploy-with-azure-cli). Aby uzyskać wbudowane zasady, użyj identyfikatora `17k78e20-9358-41c9-923c-fb736d382a12`.

## <a name="deploy-with-the-portal"></a>Wdrażanie przy użyciu portalu

Podczas przypisywania zasad wybierz pozycję **Przeprowadź inspekcję stanu przezroczystego szyfrowania danych** z dostępnych wbudowanych definicji.

## <a name="deploy-with-powershell"></a>Wdrażanie przy użyciu programu PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/17k78e20-9358-41c9-923c-fb736d382a12

New-AzPolicyAssignment -name "SQL TDE Audit" -PolicyDefinition $definition -Scope <scope>
```

### <a name="clean-up-powershell-deployment"></a>Wyczyść wdrożenie programu PowerShell

Uruchom następujące polecenie, aby usunąć przypisanie zasad.

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name "SQL TDE Audit" -Scope <scope>
```

## <a name="deploy-with-azure-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy assignment create --scope <scope> --name "SQL TDE Audit" --policy 17k78e20-9358-41c9-923c-fb736d382a12
```

### <a name="clean-up-azure-cli-deployment"></a>Wyczyść wdrożenie interfejsu wiersza polecenia platformy Azure

Uruchom następujące polecenie, aby usunąć przypisanie zasad.

```azurecli-interactive
az policy assignment delete --name "SQL TDE Audit" --resource-group myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

- Przejrzyj więcej przykładów na [Azure Policy przykładach](index.md)