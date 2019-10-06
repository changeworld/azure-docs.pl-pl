---
title: Przykład — Wymagaj szyfrowania dla Data Lake Store
description: Ta przykładowa definicja zasad wymaga włączenia szyfrowania Data Lake Store.
author: DCtheGeek
ms.service: azure-policy
ms.topic: sample
ms.date: 01/23/2019
ms.author: dacoulte
ms.openlocfilehash: 9cee9f2d94f822679acee0813471e271a38a38e3
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2019
ms.locfileid: "71977193"
---
# <a name="sample---require-data-lake-store-encryption"></a>Przykład — Wymagaj szyfrowania Data Lake Store

Te wbudowane zasady odmówią żadnych kont Data Lake Store, na których szyfrowanie nie jest włączone.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Przykładowy szablon

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.DataLakeStore/accounts"
      },
      {
        "field": "Microsoft.DataLakeStore/accounts/encryptionState",
        "equals": "Disabled"
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Ten szablon można wdrożyć za pomocą [Azure Portal](#deploy-with-the-portal)za pomocą [programu PowerShell](#deploy-with-powershell) lub [interfejsu wiersza polecenia platformy Azure](#deploy-with-azure-cli). Aby uzyskać wbudowane zasady, użyj identyfikatora `a7ff3161-0087-490a-9ad9-ad6217f4f43a`.

## <a name="deploy-with-the-portal"></a>Wdrażanie przy użyciu portalu

Podczas przypisywania zasad wybierz pozycję **Wymuszaj szyfrowanie na kontach kontach datalakestore** z dostępnych wbudowanych definicji.

## <a name="deploy-with-powershell"></a>Wdrażanie przy użyciu programu PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/a7ff3161-0087-490a-9ad9-ad6217f4f43a

New-AzPolicyAssignment -name "Data Lake Store encryption" -PolicyDefinition $definition -Scope <scope>
```

### <a name="clean-up-powershell-deployment"></a>Wyczyść wdrożenie programu PowerShell

Uruchom następujące polecenie, aby usunąć przypisanie zasad.

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name "Data Lake Store encryption" -Scope <scope>
```

## <a name="deploy-with-azure-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy assignment create --scope <scope> --name "Data Lake Store encryption" --policy a7ff3161-0087-490a-9ad9-ad6217f4f43a
```

### <a name="clean-up-azure-cli-deployment"></a>Wyczyść wdrożenie interfejsu wiersza polecenia platformy Azure

Uruchom następujące polecenie, aby usunąć przypisanie zasad.

```azurecli-interactive
az policy assignment delete --name "Data Lake Store encryption" --resource-group myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

- Przejrzyj więcej przykładów na [Azure Policy przykładach](index.md)