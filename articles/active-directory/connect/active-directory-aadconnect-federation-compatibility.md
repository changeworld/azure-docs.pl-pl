---
title: Lista zgodności usługi Azure AD z usługami federacyjnymi
description: Ta strona zawiera dostawców tożsamości innych firm, które mogą służyć do implementowania rejestracji jednokrotnej.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.assetid: 22c8693e-8915-446d-b383-27e9587988ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: billmath
ms.openlocfilehash: e7239ae88c6b4e56fa7c49f64c30fe602d8ec5fe
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="azure-ad-federation-compatibility-list"></a>Lista zgodności usługi Azure AD z usługami federacyjnymi
Usługa Azure Active Directory zapewnia jednokrotnego na i rozszerzone zabezpieczenia dostępu do aplikacji dla usługi Office 365 i innych usług Online firmy Microsoft dla implementacji tylko w chmurze i hybrydowa bez konieczności wszelkich rozwiązań innych firm. Usługi Office 365, takich jak większość usług Online firmy Microsoft, jest zintegrowany z usługą Azure Active Directory usług katalogowych, uwierzytelniania i autoryzacji. Usługa Azure Active Directory udostępnia jednokrotnego do tysięcy aplikacji SaaS i lokalnej aplikacji sieci web. Zobacz usługi Azure Active Directory [galerii aplikacji](https://azuremarketplace.microsoft.com/marketplace/apps) obsługiwanych aplikacji SaaS. 

## <a name="idp-validation"></a>Sprawdzanie poprawności IDP
Jeśli Twoja organizacja korzysta z rozwiązania federacyjne innych firm, można skonfigurować rejestrację jednokrotną dla lokalnych użytkowników usługi Active Directory z usług Online firmy Microsoft, takich jak Office 365, pod warunkiem rozwiązania federacyjne innych firm jest zgodna z platformą Azure Usługi Active Directory.  Pytania dotyczące zgodności skontaktuj się z dostawcą tożsamości.  Jeśli chcesz wyświetlić listę dostawców tożsamości, które wcześniej zostały przetestowane na zgodność z usługą Azure AD przez firmę Microsoft, kliknij przycisk [tutaj](https://www.microsoft.com/download/details.aspx?id=56843). 

>[!NOTE]
>Firma Microsoft udostępnia już weryfikacji testowanie dostawców niezależnych tożsamości dla zgodności z usługą Azure Active Directory. Jeśli chcesz przetestować produktu współdziałania zapoznaj się z tych [wytyczne](https://www.microsoft.com/download/details.aspx?id=56843). 

## <a name="next-steps"></a>Następne kroki

- [Integrowanie katalogów lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md)
- [Program Azure AD Connect a federacja](active-directory-aadconnectfed-whatis.md)
