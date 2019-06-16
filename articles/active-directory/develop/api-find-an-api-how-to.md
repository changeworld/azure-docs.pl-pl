---
title: Jak znaleźć określony interfejs API potrzebne dla aplikacji niestandardowej | Dokumentacja firmy Microsoft
description: Jak skonfigurować uprawnienia, musisz mieć dostęp konkretnego interfejsu API w niestandardowych opracowała aplikację usługi Azure AD
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a017c13008288b26ddb11bf58be1966d652bbae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65540556"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Jak znaleźć określony interfejs API potrzebne dla aplikacji niestandardowej

Dostęp do interfejsów API wymagają konfiguracji, zakresów dostępu i ról. Jeśli chcesz udostępnić swoje zasobów aplikacji interfejsów API sieci web dla aplikacji klienckich, należy skonfigurować zakresy dostępu i role dla interfejsu API. Aplikacji klienckiej dostęp do interfejsu API sieci web, należy skonfigurować uprawnienia dostępu do interfejsu API w rejestracji aplikacji.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Konfigurowanie aplikacji zasobów na potrzeby uwidoczniania internetowych interfejsów API

Jeśli udostępnisz swój internetowy interfejs API, wyświetlana w interfejsie API **wybierz interfejs API** podczas dodawania uprawnień do rejestracji aplikacji. Aby dodać zakresy dostępu, wykonaj czynności opisane w temacie [Dodawanie zakresy dostępu do zasobów aplikacji](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).

## <a name="configuring-a-client-application-to-access-web-apis"></a>Konfigurowanie aplikacji klienckiej dostęp do interfejsów API sieci web

Po dodaniu uprawnień do rejestracji aplikacji możesz **Dodaj dostęp do interfejsu API** do narażonych interfejsów API sieci web. Aby uzyskać dostęp do interfejsów API sieci web, wykonaj czynności opisane w temacie [Dodaj poświadczenia lub uprawnienia dostępu do interfejsów API sieci web](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).

## <a name="next-steps"></a>Kolejne kroki

-   [Integrating applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) (Integrowanie aplikacji za pomocą usługi Azure Active Directory)

-   [Opis manifestu aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)


