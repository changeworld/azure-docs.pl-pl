---
title: Lista zgodności usługi Azure AD z usługami federacyjnymi
description: Ta strona zawiera dostawców tożsamości innych niż Microsoft, które mogą służyć do implementowania logowania jednokrotnego.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 22c8693e-8915-446d-b383-27e9587988ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/23/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54f5090101c486562e33de56402db348c6038c8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60244756"
---
# <a name="azure-ad-federation-compatibility-list"></a>Lista zgodności usługi Azure AD z usługami federacyjnymi
Usługa Azure Active Directory zapewnia logowanie jednokrotne i rozszerzone zabezpieczenia dostępu do aplikacji dla usługi Office 365 i innych usług Microsoft Online dla implementacji hybrydowych i tylko w chmurze bez konieczności stosowania rozwiązania innych firm. Usługa Office 365, podobnie jak większość usług Online firmy Microsoft, jest zintegrowana z usługą Azure Active Directory dla usług katalogowych, uwierzytelniania i autoryzacji. Usługa Azure Active Directory zapewnia również logowanie jednokrotne do tysięcy aplikacji SaaS i lokalnych aplikacji sieci Web. Zobacz [galerię aplikacji](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) usługi Azure Active Directory dla obsługiwanych aplikacji SaaS. 

## <a name="idp-validation"></a>Sprawdzanie poprawności IDP
Jeśli twoja organizacja korzysta z rozwiązania federacyjnego innej firmy, możesz skonfigurować logowanie jednokrotne dla lokalnych użytkowników usługi Active Directory za pomocą usług Microsoft Online, takich jak Office 365, pod warunkiem że rozwiązanie federacyjne innej firmy jest zgodne z platformą Azure Active Directory.  W przypadku pytań dotyczących zgodności skontaktuj się z dostawcą tożsamości.  Jeśli chcesz wyświetlić listę dostawców tożsamości, którzy wcześniej zostali przetestowani pod kątem zgodności z usługą Azure AD, microsoft, kliknij [tutaj](https://www.microsoft.com/download/details.aspx?id=56843). 

>[!NOTE]
>Firma Microsoft nie zapewnia już testowania sprawdzania poprawności niezależnym dostawcom tożsamości w celu zapewnienia zgodności z usługą Azure Active Directory. Jeśli chcesz przetestować swój produkt pod kątem interoperacyjności, zapoznaj się z tymi [wytycznymi.](https://www.microsoft.com/download/details.aspx?id=56843) 

## <a name="next-steps"></a>Następne kroki

- [Integrowanie katalogów lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
- [Usługa Azure AD Connect i federacja](how-to-connect-fed-whatis.md)
