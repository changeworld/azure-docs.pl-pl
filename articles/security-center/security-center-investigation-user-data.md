---
title: Zarządzanie danymi użytkownika znalezionymi w badaniu usługi Azure Security Center
description: " Dowiedz się, jak zarządzać danymi użytkowników znalezionymi w funkcji badania usługi Azure Security Center. "
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
ms.openlocfilehash: aa262b0be3902f6e143a53f8f1302156fc5aede6
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582984"
---
# <a name="manage-user-data-found-in-an-azure-security-center-investigation"></a>Zarządzanie danymi użytkownika znalezionymi w badaniu usługi Azure Security Center
Ten artykuł zawiera informacje dotyczące zarządzania danymi użytkownika znalezionymi w funkcji badania usługi Azure Security Center. Dane badania są przechowywane w [dziennikach usługi Azure Monitor](../log-analytics/log-analytics-overview.md) i udostępniane w usłudze Security Center. Zarządzanie danymi użytkownika obejmuje możliwość usuwania lub eksportowania danych.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="searching-for-and-identifying-personal-data"></a>Wyszukiwanie i identyfikowanie danych osobowych
W witrynie Azure portal można użyć funkcji badania usługi Security Center do wyszukiwania danych osobowych. Funkcja dochodzenia jest dostępna w obszarze **Alerty zabezpieczeń**.

Funkcja dochodzenia pokazuje wszystkie jednostki, informacje o użytkowniku i dane na karcie **Jednostki.**

## <a name="securing-and-controlling-access-to-personal-information"></a>Zabezpieczanie i kontrolowanie dostępu do informacji osobistych
Użytkownik Centrum zabezpieczeń przypisany do roli administratora programu Reader, Owner, Contributor lub Account Administrator może uzyskać dostęp do danych klienta w narzędziu.

Zobacz [wbudowane role dla kontroli dostępu opartej na rolach platformy Azure,](../role-based-access-control/built-in-roles.md) aby dowiedzieć się więcej o rolach czytnika, właściciela i współautora. Zobacz [administratorzy subskrypcji platformy Azure,](../cost-management-billing/manage/add-change-subscription-administrator.md) aby dowiedzieć się więcej o roli Administratora konta.

## <a name="deleting-personal-data"></a>Usuwanie danych osobowych
Użytkownik Centrum zabezpieczeń przypisany do roli właściciela, współautora lub administratora konta może usunąć informacje o badaniu.

Aby usunąć dochodzenie, można `DELETE` przesłać żądanie do interfejsu API REST usługi Azure Resource Manager:

```HTTP
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents/{incidentName}
```

Dane `incidentName` wejściowe można znaleźć, wymieniając `GET` wszystkie zdarzenia przy użyciu żądania:

```HTTP
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents
```

## <a name="exporting-personal-data"></a>Eksportowanie danych osobowych
Użytkownik Centrum zabezpieczeń przypisany do roli właściciela, współautora lub administratora konta może wyeksportować informacje o badaniu. Aby wyeksportować informacje o dochodzeniu, przejdź do karty **Jednostki,** aby skopiować i wkleić odpowiednie informacje.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat zarządzania danymi użytkownika, zobacz [Zarządzanie danymi użytkowników w usłudze Azure Security Center](security-center-privacy.md).
Aby dowiedzieć się więcej o usuwaniu danych prywatnych w dziennikach usługi Azure Monitor, zobacz [Jak eksportować i usuwać dane prywatne](../azure-monitor/platform/personal-data-mgmt.md#how-to-export-and-delete-private-data).
