---
title: Zarządzanie danymi użytkownika w Centrum zabezpieczeń Azure dochodzenia | Dokumentacja firmy Microsoft
description: " Dowiedz się, jak zarządzać danymi użytkownika w Centrum zabezpieczeń Azure badanie funkcji. "
services: operations-management-suite
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: terrylan
ms.openlocfilehash: 6685db4eeda72928753c74c64b4b26539ccb1eb9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655719"
---
# <a name="manage-user-data-found-in-an-azure-security-center-investigation"></a>Zarządzanie danymi użytkownika w Centrum zabezpieczeń Azure dochodzenia
Ten artykuł zawiera informacje na temat zarządzania danych użytkownika w Centrum zabezpieczeń Azure badanie funkcji. Badanie dane są przechowywane w [Azure Log Analytics](../log-analytics/log-analytics-overview.md) i widoczne w Centrum zabezpieczeń. Zarządzanie danymi użytkownika obejmuje możliwość usunięcia lub eksportowania danych.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="searching-for-and-identifying-personal-data"></a>Wyszukiwanie i zidentyfikowaniu danych osobowych
W portalu Azure, można użyć Centrum zabezpieczeń [funkcja badania](../security-center/security-center-investigation.md) do wyszukiwania danych osobowych. Funkcja badania jest dostępna w obszarze **alerty zabezpieczeń**.

Funkcja badania zawiera wszystkie jednostki, informacje o użytkowniku i dane w ramach **jednostek** kartę.

## <a name="securing-and-controlling-access-to-personal-information"></a>Zabezpieczanie i kontrolowanie dostępu do danych osobowych
Centrum zabezpieczeń użytkownika przypisano rolę czytelnika, właściciela, współautora lub Administrator konta ma dostęp do danych klienta w ramach narzędzia.

Zobacz [wbudowanych ról dla kontroli dostępu opartej na rolach na platformie Azure](../role-based-access-control/built-in-roles.md) Aby dowiedzieć się więcej o rolach czytnik, właściciela i współautor. Zobacz [Administratorzy subskrypcji platformy Azure](../billing/billing-add-change-azure-subscription-administrator.md) Aby dowiedzieć się więcej o roli administratora konta.

## <a name="deleting-personal-data"></a>Usuwanie danych osobowych
Centrum zabezpieczeń użytkownika przypisaną rolę właściciela, współautora, lub konto administratora można usunąć informacje dotyczące badania.

Aby usunąć dochodzenia, można kierować `DELETE` żądania interfejsu REST API usługi Azure Resource Manager:

```HTTP
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents/{incidentName}
```

`incidentName` Można znaleźć danych wejściowych poprzez wyszczególnienie wszystkich zdarzeń przy użyciu `GET` żądania:

```HTTP
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents
```

## <a name="exporting-personal-data"></a>Eksportowanie danych osobowych
Centrum zabezpieczeń użytkownika przypisaną rolę właściciela, współautora, lub konto administratora można wyeksportować informacje o analizie. Aby wyeksportować informacje o analizie, przejdź do **jednostek** kartę, aby skopiować i wkleić istotne informacje.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat zarządzania danymi użytkownika, zobacz [zarządzać danymi użytkownika w Centrum zabezpieczeń Azure](security-center-privacy.md).
