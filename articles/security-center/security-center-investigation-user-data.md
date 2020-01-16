---
title: Zarządzanie danymi użytkownika w badaniu Azure Security Centernym
description: " Dowiedz się, jak zarządzać danymi użytkowników znalezionymi w funkcji badania Azure Security Center. "
services: operations-management-suite
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2018
ms.author: memildin
ms.openlocfilehash: 9e4c6577f0b8b18aff343ac54b31ff292632f5d0
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979241"
---
# <a name="manage-user-data-found-in-an-azure-security-center-investigation"></a>Zarządzanie danymi użytkownika w badaniu Azure Security Centernym
Ten artykuł zawiera informacje na temat zarządzania danymi użytkownika znalezionymi w funkcji badania Azure Security Center. Dane badania są przechowywane w [Azure monitor dziennikach](../log-analytics/log-analytics-overview.md) i udostępniane w Security Center. Zarządzanie danymi użytkownika obejmuje możliwość usuwania lub eksportowania danych.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="searching-for-and-identifying-personal-data"></a>Wyszukiwanie i identyfikowanie danych osobowych
W Azure Portal można użyć [funkcji badania](../security-center/security-center-investigation.md) Security Center, aby wyszukać dane osobowe. Funkcja badania jest dostępna w obszarze **alerty zabezpieczeń**.

Funkcja badania pokazuje wszystkie jednostki, informacje o użytkownikach i dane na karcie **jednostki** .

## <a name="securing-and-controlling-access-to-personal-information"></a>Zabezpieczanie i kontrolowanie dostępu do informacji osobistych
Użytkownik Security Center przypisany do roli czytelnik, właściciel, współautor lub administrator konta może uzyskać dostęp do danych klienta w ramach tego narzędzia.

Zobacz [wbudowane role dla kontroli dostępu opartej na rolach na platformie Azure](../role-based-access-control/built-in-roles.md) , aby dowiedzieć się więcej na temat ról czytelnik, właściciel i współautor. Zobacz [administratorów subskrypcji platformy Azure](../cost-management-billing/manage/add-change-subscription-administrator.md) , aby dowiedzieć się więcej o roli administratora konta.

## <a name="deleting-personal-data"></a>Usuwanie danych osobowych
Użytkownik Security Center przypisany do roli właściciela, współautora lub administrator konta może usunąć informacje o badaniu.

Aby usunąć badanie, możesz przesłać żądanie `DELETE` do interfejsu API REST Azure Resource Manager:

```HTTP
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents/{incidentName}
```

Dane wejściowe `incidentName` można znaleźć, wyświetlając listę wszystkich zdarzeń przy użyciu żądania `GET`:

```HTTP
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents
```

## <a name="exporting-personal-data"></a>Eksportowanie danych osobowych
Użytkownik Security Center przypisany do roli właściciela, współautora lub administratora konta może eksportować informacje o badaniu. Aby wyeksportować informacje o badaniu, przejdź do karty **jednostki** , aby skopiować i wkleić odpowiednie informacje.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat zarządzania danymi użytkowników, zobacz [Zarządzanie danymi użytkowników w Azure Security Center](security-center-privacy.md).
Aby dowiedzieć się więcej o usuwaniu danych prywatnych w dziennikach Azure Monitor, zobacz [Jak eksportować i usuwać dane prywatne](../azure-monitor/platform/personal-data-mgmt.md#how-to-export-and-delete-private-data).
