---
title: RequestDisallowedByPolicy error (Błąd RequestDisallowedByPolicy)
description: Opisuje przyczynę błędu RequestDisallowedByPolicy podczas wdrażania zasobów za pomocą usługi Azure Resource Manager.
author: genlin
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 41581ba48da2f2e717c5abf2a749f8fd2b86ac06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75477670"
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Błąd RequestDisallowedByPolicy z zasadami zasobów platformy Azure

W tym artykule opisano przyczynę requestdisallowedByPolicy błąd, zapewnia również rozwiązanie tego błędu.

## <a name="symptom"></a>Objaw

Podczas wdrażania może pojawić się błąd **RequestDisallowedByPolicy,** który uniemożliwia tworzenie zasobów. W poniższym przykładzie pokazano błąd:

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Aby pobrać szczegółowe informacje o zasadach, które zablokowały wdrożenie, użyj jednej z następujących metod:

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

W programie PowerShell podaj `Id` identyfikator zasad jako parametr, aby pobrać szczegółowe informacje o zasadach, które zablokowały wdrożenie.

```powershell
(Get-AzPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

W usłudze Azure CLI podaj nazwę definicji zasad:

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Rozwiązanie

Ze względów bezpieczeństwa lub zgodności administratorzy subskrypcji mogą przypisywać zasady ograniczające sposób wdrażania zasobów. Na przykład subskrypcja może mieć zasady, które uniemożliwiają tworzenie publicznych adresów IP, grup zabezpieczeń sieci, tras zdefiniowanych przez użytkownika lub tabel tras. Komunikat o błędzie w **symptomy** sekcji pokazuje nazwę zasad.
Aby rozwiązać ten problem, przejrzyj zasady zasobów i określ, jak wdrożyć zasoby zgodne z tymi zasadami.

Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Co to jest Azure Policy?](../../governance/policy/overview.md)
- [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../../governance/policy/tutorials/create-and-manage.md)
