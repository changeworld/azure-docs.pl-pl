---
title: Zmień ustawienia domyślne okresu istnienia tokenu dla niestandardowych aplikacji usługi Azure AD | Microsoft Docs
description: Jak zaktualizować zasady okresu istnienia tokenu dla aplikacji opracowywanej w usłudze Azure AD
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: a603be6d57581541c0868b9f48a0bf9997cadd71
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962835"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Jak zmienić wartości domyślne okresu istnienia tokenu dla aplikacji opracowanej niestandardowo

W tym artykule pokazano, jak ustawić zasady okresu istnienia tokenu przy użyciu programu Azure AD PowerShell. Azure AD — wersja Premium umożliwia deweloperom aplikacji i administratorom dzierżawy skonfigurowanie okresu istnienia tokenów wystawionych dla klientów niepoufnych. Zasady okresu istnienia tokenu są ustawiane dla całej dzierżawy lub do zasobów, do których uzyskuje się dostęp.

1. Aby ustawić zasady istnienia tokenu, należy pobrać [moduł programu PowerShell usługi Azure AD](https://www.powershellgallery.com/packages/AzureADPreview).
1. Uruchom polecenie **Connect-AzureAD-Confirm** .

    Poniżej przedstawiono przykładowe zasady ustawiające maksymalny wiek tokenu odświeżania jednego czynnika. Tworzenie zasad: ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>Następne kroki

* Zobacz [konfigurowalne okresy istnienia tokenu w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) , aby dowiedzieć się, jak skonfigurować okresy istnienia tokenów wystawionych przez usługę Azure AD, w tym informacje o sposobie ustawiania okresów istnienia tokenu dla wszystkich aplikacji w organizacji, dla aplikacji z wieloma dzierżawami lub dla określonej jednostki usługi w organizacji. 
* [Odwołanie do tokenu usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)
