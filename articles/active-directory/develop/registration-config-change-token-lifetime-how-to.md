---
title: Zmienianie domyślnych ustawień domyślnych okresu istnienia tokenu dla niestandardowych aplikacji usługi Azure AD
description: Jak zaktualizować zasady okresu istnienia tokenu dla aplikacji, które tworzysz w usłudze Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: fc71ebe61fba8c1bdb6b7625b16a50d8995a581a
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883357"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Jak zmienić domyślne okres istnienia tokenu dla aplikacji opracowanej na zamówienie

W tym artykule pokazano, jak używać programu Azure AD PowerShell do ustawiania zasad okresu istnienia tokenu. Usługa Azure AD Premium umożliwia deweloperom aplikacji i administratorom dzierżawy konfigurowanie okresu istnienia tokenów wystawionych dla klientów niepoufnych. Zasady okresu istnienia tokenu są ustawiane na podstawie całej dzierżawy lub zasoby są dostępne.

1. Aby ustawić zasady okresu istnienia tokenu, należy pobrać [moduł programu Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
1. Uruchom polecenie **Connect-AzureAD -Confirm.**

    Oto przykładowa zasada, która ustawia token odświeżania maksymalnego wieku pojedynczego czynnika. Utwórz zasady:```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>Następne kroki

* Zobacz [Okres istnienia tokenu konfigurowalne w usłudze Azure AD,](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) aby dowiedzieć się, jak skonfigurować okresy istnienia tokenu wydane przez usługę Azure AD, w tym jak ustawić okres istnienia tokenu dla wszystkich aplikacji w organizacji, dla aplikacji z wieloma dzierżawcami lub dla określonego podmiotu usługi w organizacji. 
* [Odwołanie do tokenu usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)
