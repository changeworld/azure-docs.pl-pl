---
title: Błąd RequestDisallowedByPolicy z zasadami zasobów platformy Azure | Microsoft Docs
description: Opisuje przyczynę błędu RequestDisallowedByPolicy.
services: azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: e862637c688fd473b112fdfc0ee197da0444d02f
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71121230"
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

W programie PowerShell podaj identyfikator zasad jako `Id` parametr, aby pobrać szczegóły dotyczące zasad, które zablokowały wdrożenie.

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
