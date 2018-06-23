---
title: Jak zmienić okres istnienia tokenu domyślnie rozwinięte niestandardowych aplikacji | Dokumentacja firmy Microsoft
description: Jak aktualizować zasady okres istnienia tokenu aplikacji, które tworzysz usługi Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: bd963253ee796fb56405e8da855475df2d2f5d08
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335343"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Jak zmienić domyślne okres istnienia tokenu dla aplikacji utworzonych niestandardowych

Azure AD Premium umożliwia deweloperom aplikacji i administratorów dzierżawy, aby skonfigurować okres istnienia tokenów wystawionych dla klientów z systemem innym niż poufne. Okres istnienia tokenu zasady są skonfigurowane na poziomie dzierżawy lub zasobów, do której uzyskuje dostęp.

 * Aby skonfigurować zasady okres istnienia tokenu, należy pobrać [modułu Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

 * Uruchom **Connect AzureAD-Potwierdź** polecenia.

 * Oto przykład zasad, która ustawia token odświeżania pojedynczy czynnik maksymalny wiek. Tworzenie zasad: ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

 * Wyewidencjonowanie [okres istnienia tokenu Konfigurowanie](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) dokumentu, aby dowiedzieć się, jak utworzyć inne niestandardowe.

## <a name="next-steps"></a>Kolejne kroki
[Konfigurowanie okres istnienia tokenu](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes)<br>

[Odwołanie do tokenu usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)

