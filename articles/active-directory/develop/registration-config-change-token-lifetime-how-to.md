---
title: Jak zmienić okres istnienia tokenu ustawienia domyślne dla aplikacji niestandardowej | Dokumentacja firmy Microsoft
description: Jak zaktualizować zasady okres istnienia tokenu dla aplikacji, który tworzysz w usłudze Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: celested
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04abdedf5ac19be3d5a43e7502cbc97f8f5fee43
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2019
ms.locfileid: "59360312"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Jak zmienić domyślny okres istnienia tokenu dla aplikacji niestandardowej

W tym artykule pokazano, jak ustawić zasady okres istnienia tokenu przy użyciu programu Azure AD PowerShell. Usługa Azure AD Premium pozwala deweloperom aplikacji i administratorów dzierżawy, aby skonfigurować okres istnienia tokenów wystawionych dla klientów — poufne. Okres istnienia tokenu zasady są skonfigurowane na poziomie dzierżawy lub udostępnianych im zasobów.

1. Aby ustawić zasady okres istnienia tokenu, musisz pobrać [modułu Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
1. Uruchom **Connect-AzureAD-Potwierdź** polecenia.

    Oto przykład zasad, która ustawia token odświeżania pojedynczy czynnik maksymalnego wieku. Utwórz zasady:
  ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>Kolejne kroki

* Zobacz [okresów istnienia tokenu można skonfigurować w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) informacje na temat Konfigurowanie okresów istnienia tokenu wystawionego przez usługę Azure AD, w tym o ustawianiu okresów istnienia tokenu dla wszystkich aplikacji w Twojej organizacji, dla aplikacji wielodostępnych lub dla określonej usługi podmiot zabezpieczeń w organizacji. 
* [Odwołanie tokenu usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)

