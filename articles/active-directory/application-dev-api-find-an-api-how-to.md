---
title: Jak znaleźć określony interfejs API potrzebne dla aplikacji niestandardowej | Dokumentacja firmy Microsoft
description: Jak skonfigurować uprawnienia, musisz mieć dostęp konkretnego interfejsu API w niestandardowych opracowała aplikację usługi Azure AD
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
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: f15e84d6efa30d2e66c7ff1d92551bece94938e7
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2018
ms.locfileid: "39363350"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Jak znaleźć określony interfejs API potrzebne dla aplikacji niestandardowej

Dostęp do interfejsów API wymagają konfiguracji, zakresów dostępu i ról. Jeśli chcesz udostępnić swoje zasobów aplikacji interfejsów API sieci web dla aplikacji klienckich, należy skonfigurować zakresy dostępu i role dla interfejsu API. Aplikacji klienckiej dostęp do interfejsu API sieci web, należy skonfigurować uprawnienia dostępu do interfejsu API w rejestracji aplikacji.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Konfigurowanie aplikacji zasobów, aby udostępnić interfejsów API sieci web

Jeśli udostępnisz swój internetowy interfejs API, wyświetlana w interfejsie API **wybierz interfejs API** podczas dodawania uprawnień do rejestracji aplikacji. Aby dodać zakresy dostępu, wykonaj czynności opisane w temacie [Dodawanie zakresy dostępu do zasobów aplikacji](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-access-scopes-to-your-resource-application).

## <a name="configuring-a-client-application-to-access-web-apis"></a>Konfigurowanie aplikacji klienckiej dostęp do interfejsów API sieci web

Po dodaniu uprawnień do rejestracji aplikacji możesz **Dodaj dostęp do interfejsu API** do narażonych interfejsów API sieci web. Aby uzyskać dostęp do interfejsów API sieci web, wykonaj czynności opisane w temacie [Dodaj poświadczenia lub uprawnienia dostępu do interfejsów API sieci web](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#to-add-credentials-or-permissions-to-access-web-apis).

## <a name="next-steps"></a>Kolejne kroki

-   [Integrating applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) (Integrowanie aplikacji za pomocą usługi Azure Active Directory)

-   [Opis manifestu aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)


