---
title: Błąd RequestDisallowedByPolicy
description: Opisuje przyczynę błędu RequestDisallowedByPolicy podczas wdrażania zasobów przy użyciu Azure Resource Manager.
author: genlin
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: ed4008c6c6705f307f8c21bd43992523701a4ee6
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150517"
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Błąd RequestDisallowedByPolicy z zasadami zasobów platformy Azure

W tym artykule opisano przyczynę błędu RequestDisallowedByPolicy i przedstawiono w nim również rozwiązanie tego błędu.

## <a name="symptom"></a>Objaw

Podczas wdrażania może zostać wyświetlony błąd **RequestDisallowedByPolicy** , który uniemożliwia tworzenie zasobów. Poniższy przykład pokazuje błąd:

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Aby pobrać szczegóły dotyczące zasad, które zablokowały wdrożenie, należy użyć jednej z następujących metod:

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W programie PowerShell Podaj ten identyfikator zasad jako parametr `Id`, aby pobrać szczegóły dotyczące zasad, które zablokowały wdrożenie.

```powershell
(Get-AzPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

W interfejsie wiersza polecenia platformy Azure Podaj nazwę definicji zasad:

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Rozwiązanie

Aby zapewnić bezpieczeństwo lub zgodność, Administratorzy subskrypcji mogą przypisywać zasady ograniczające sposób wdrażania zasobów. Na przykład Twoja subskrypcja może mieć zasady, które uniemożliwiają tworzenie publicznych adresów IP, sieciowych grup zabezpieczeń, tras zdefiniowanych przez użytkownika lub tabel tras. Komunikat o błędzie w sekcji **objawy** zawiera nazwę zasad.
Aby rozwiązać ten problem, przejrzyj zasady zasobów i określ sposób wdrażania zasobów, które są zgodne z tymi zasadami.

Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Co to jest Azure Policy?](../governance/policy/overview.md)
- [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../governance/policy/tutorials/create-and-manage.md)
