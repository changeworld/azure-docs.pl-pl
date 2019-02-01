---
title: Błąd RequestDisallowedByPolicy zasady zasobów platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano przyczyny tego błędu RequestDisallowedByPolicy.
services: azure-resource-manager
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 2d55d5958568deef5ce350c9678e55e2e37a2ff8
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55496401"
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Błąd RequestDisallowedByPolicy zasady zasobów platformy Azure

W tym artykule opisano przyczyny tego błędu RequestDisallowedByPolicy, również udostępnia rozwiązanie dla tego błędu.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Objaw

Podczas wdrażania, może pojawić się **RequestDisallowedByPolicy** błąd, który uniemożliwia tworzenie zasobów. Poniższy przykład pokazuje błąd:

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Aby uzyskać szczegółowe informacje o zasadach, które zablokowały wdrożenia, użyj następujących metod:

### <a name="powershell"></a>PowerShell

W programie PowerShell, podaj identyfikator zasad jako `Id` parametru, aby pobrać szczegóły dotyczące zasad, które zablokowały wdrożenia.

```PowerShell
(Get-AzPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

W interfejsie wiersza polecenia platformy Azure należy podać nazwę definicji zasad:

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Rozwiązanie

Pod kątem bezpieczeństwa ani zgodności administratorów subskrypcji może przypisywać zasady, które ograniczają sposób wdrożenia zasobów. Na przykład Twoja subskrypcja może mieć zasady, które uniemożliwiają tworzenie trasy zdefiniowane przez użytkownika adresów sieciowych grup zabezpieczeń, publiczny adres IP lub tabele tras. Komunikat o błędzie w **objawy** sekcja zawiera nazwę zasad.
Aby rozwiązać ten problem, przejrzyj zasady zasobów i dowiedzieć się, jak wdrażać zasoby, które są zgodne z tymi zasadami.

Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Co to jest usługa Azure Policy?](../azure-policy/azure-policy-introduction.md)
- [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../azure-policy/create-manage-policy.md)
