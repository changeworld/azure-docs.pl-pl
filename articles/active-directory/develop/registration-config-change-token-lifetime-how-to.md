---
title: Jak zmienić okres istnienia tokenu ustawienia domyślne dla aplikacji niestandardowej | Dokumentacja firmy Microsoft
description: Jak zaktualizować zasady okres istnienia tokenu dla aplikacji, który tworzysz w usłudze Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.openlocfilehash: 4a730c340ea4d1e1137a7449c6d1005ea59917bf
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48814012"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Jak zmienić domyślny okres istnienia tokenu dla aplikacji niestandardowej

Usługa Azure AD Premium pozwala deweloperom aplikacji i administratorów dzierżawy, aby skonfigurować okres istnienia tokenów wystawionych dla klientów — poufne. Okres istnienia tokenu zasady są skonfigurowane na poziomie dzierżawy lub udostępnianych im zasobów.

1. Aby ustawić zasady okres istnienia tokenu, musisz pobrać [modułu Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
1. Uruchom **Connect-AzureAD-Potwierdź** polecenia.

    Oto przykład zasad, która ustawia token odświeżania pojedynczy czynnik maksymalnego wieku. Utwórz zasady: ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>Kolejne kroki

* Zobacz [okresów istnienia tokenu można skonfigurować w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) informacje na temat Konfigurowanie okresów istnienia tokenu wystawionego przez usługę Azure AD, w tym o ustawianiu okresów istnienia tokenu dla wszystkich aplikacji w Twojej organizacji, dla aplikacji wielodostępnych lub dla określonej usługi podmiot zabezpieczeń w organizacji. 
* [Odwołanie tokenu usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)

