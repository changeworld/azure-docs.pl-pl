---
title: Zmienianie domyślnych ustawień domyślnych okresu istnienia tokenu dla niestandardowych aplikacji usługi Azure AD | Dokumenty firmy Microsoft
description: Jak zaktualizować zasady okresu istnienia tokenu dla aplikacji, które tworzysz w usłudze Azure AD
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: 431f18b9babb52b5000d3bf4cca75a0f5e29bb93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76702814"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Jak zmienić domyślne okres istnienia tokenu dla aplikacji opracowanej na zamówienie

W tym artykule pokazano, jak używać programu Azure AD PowerShell do ustawiania zasad okresu istnienia tokenu. Usługa Azure AD Premium umożliwia deweloperom aplikacji i administratorom dzierżawy konfigurowanie okresu istnienia tokenów wystawionych dla klientów niepoufnych. Zasady okresu istnienia tokenu są ustawiane na podstawie całej dzierżawy lub zasoby są dostępne.

1. Aby ustawić zasady okresu istnienia tokenu, należy pobrać [moduł programu Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
1. Uruchom polecenie **Connect-AzureAD -Confirm.**

    Oto przykładowa zasada, która ustawia token odświeżania maksymalnego wieku pojedynczego czynnika. Utwórz zasady:```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>Następne kroki

* Zobacz [Okres istnienia tokenu konfigurowalne w usłudze Azure AD,](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) aby dowiedzieć się, jak skonfigurować okresy istnienia tokenu wydane przez usługę Azure AD, w tym jak ustawić okres istnienia tokenu dla wszystkich aplikacji w organizacji, dla aplikacji z wieloma dzierżawcami lub dla określonego podmiotu usługi w organizacji. 
* [Odwołanie do tokenu usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)
