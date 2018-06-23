---
title: Jak znaleźć interfejsu API wymagane dla aplikacji utworzonych niestandardowych | Dokumentacja firmy Microsoft
description: Jak skonfigurować uprawnienia, musisz mieć dostęp do danego interfejsu API w niestandardowe opracowała aplikację usługi Azure AD
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
ms.openlocfilehash: 94f3c68e9a1433d2a433e2a3bbe557b487e7bf75
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36332062"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Jak znaleźć interfejsu API wymagane dla aplikacji utworzonych niestandardowych

Dostęp do interfejsów API wymaga konfiguracji zakresów dostępu i ról. Jeśli chcesz udostępnić sieci web aplikacji zasobów interfejsów API dla aplikacji klienckich, należy skonfigurować zakresy dostępu i role dla interfejsu API. Aplikację klienta do dostępu do interfejsu API sieci web, należy skonfigurować uprawnienia dostępu do interfejsu API w rejestracji aplikacji.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Konfigurowanie aplikacji zasobów, aby ujawnić interfejsów API sieci web

Gdy uwidacznia interfejs API, sieci web interfejsu API można wyświetlić w **wybierz interfejs API** listy podczas dodawania uprawnień do rejestracji aplikacji. Aby dodać zakresy dostępu, wykonaj czynności opisane w temacie [Dodawanie zakresy dostępu do zasobów aplikacji](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-access-scopes-to-your-resource-application).

## <a name="configuring-a-client-application-to-access-web-apis"></a>Konfigurowanie aplikacji klienta, aby uzyskać dostępu do interfejsów API sieci web

Po dodaniu uprawnień do rejestracji aplikacji można **dodać dostępu do interfejsu API** do narażonych interfejsów API sieci web. Aby uzyskać dostęp do interfejsów API sieci web, wykonaj czynności opisane w temacie [dodać poświadczeń lub uprawnienia dostępu do interfejsów API sieci web](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#to-add-credentials-or-permissions-to-access-web-apis).

## <a name="next-steps"></a>Kolejne kroki

-   [Integrating applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) (Integrowanie aplikacji za pomocą usługi Azure Active Directory)

-   [Opis manifestu aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)


