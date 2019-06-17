---
title: Uwierzytelnianie przy użyciu usługi Azure Active Directory w chmurach krajowych
description: Więcej informacji na temat rejestracji oraz uwierzytelniania punktów końcowych aplikacji chmur krajowych.
services: active-directory
documentationcenter: ''
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: negoe,CelesteDG
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd37366697a9c1f5019d2864e6d81a4dcd02e3a2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66235487"
---
# <a name="national-clouds"></a>Chmury krajowe

Chmury krajowe są odizolowane fizycznie wystąpienia platformy Azure. Te regiony platformy Azure są przeznaczone do upewnij się, że wymagania dotyczące zgodności, niezależności i rezydencji danych są honorowane w granicach geograficznych.

W tym chmury globalnej usługi Azure Active Directory (Azure AD) jest wdrażany w chmurach narodowych następujące:  

- Azure Government
- Azure (Niemcy)
- Azure w Chinach — 21Vianet

Chmury krajowe są unikatowe i oddzielnego środowiska z globalnej platformy Azure. Należy mieć świadomość podstawowe różnice podczas tworzenia aplikacji dla tych środowisk. Różnice obejmują rejestrowanie aplikacji i uzyskiwania tokenów certyfikatu oraz konfigurowanie punktów końcowych.

## <a name="app-registration-endpoints"></a>Punkty końcowe rejestracji aplikacji

Brak osobnego portalu platformy Azure dla każdej z nich chmur krajowych. Do zintegrowania aplikacji z platformą tożsamości Microsoft w chmurze krajowej, musisz zarejestrować aplikację oddzielnie w każdej witrynie Azure portal, które są specyficzne dla środowiska.

Poniższa tabela zawiera listę podstawowych adresów URL punktów końcowych usługi Azure AD używane do rejestrowania aplikacji w przypadku każdej chmury krajowe.

| Chmury krajowe | Usługi AD portalu punkt końcowy platformy Azure |
|----------------|--------------------------|
| Usługa Azure AD dla instytucji rządowych USA | `https://portal.azure.us` |
| Niemiecka wersja platformy Azure AD | `https://portal.microsoftazure.de` |
| Usługi AD chińska wersja platformy Azure obsługiwane przez firmę 21Vianet | `https://portal.azure.cn` |
| Usługa Azure AD (usługa globalne) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Punkty końcowe uwierzytelniania usługi Azure AD

Chmur krajowych uwierzytelniać użytkowników oddzielnie w ramach każdego środowiska i punkty końcowe uwierzytelniania oddzielne.

Poniższa tabela zawiera listę podstawowych adresów URL punktów końcowych usługi Azure AD używany do uzyskiwania tokenów dla każdej chmury krajowe.

| Chmury krajowe | Punkt końcowy uwierzytelniania usługi Azure AD |
|----------------|-------------------------|
| Usługa Azure AD dla instytucji rządowych USA | `https://login.microsoftonline.us` |
| Niemiecka wersja platformy Azure AD| `https://login.microsoftonline.de` |
| Usługi AD chińska wersja platformy Azure obsługiwane przez firmę 21Vianet | `https://login.chinacloudapi.cn` |
| Usługa Azure AD (usługa globalne)| `https://login.microsoftonline.com` |

Formularza z żądania do usługi Azure AD autoryzacji lub tokenu punktów końcowych, za pomocą odpowiednich specyficzne dla regionu podstawowego adresu URL. Na przykład Azure (Niemcy):

  - Typowe punkt końcowy autoryzacji jest `https://login.microsoftonline.de/common/oauth2/authorize`.
  - Typowe punktu końcowego tokenu jest `https://login.microsoftonline.de/common/oauth2/token`.

Dla jednej dzierżawy aplikacji Zastąp "typowych" w poprzednich adresy URL z Identyfikatorem dzierżawy lub nazwa. Może to być na przykład `https://login.microsoftonline.de/contoso.com`.

> [!NOTE]
> [Usługi Azure AD v2.0 autoryzacji]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview) i tokenu punkty końcowe są dostępne tylko w przypadku usługi global service. Nie są obsługiwane w przypadku wdrożeń chmur krajowych.

## <a name="microsoft-graph-api"></a>Interfejs API programu Microsoft Graph

Aby dowiedzieć się, jak do wywoływania interfejsów API programu Microsoft Graph w środowisku chmury krajowe, przejdź do [programu Microsoft Graph w przypadku wdrożeń chmury krajowe](https://developer.microsoft.com/graph/docs/concepts/deployments).

> [!IMPORTANT]
> Niektórych usług i funkcji, które znajdują się w określonych regionach usługi global service mogą nie być dostępne we wszystkich chmurach krajowych. Aby dowiedzieć się, jakie usługi są dostępne, przejdź do [dostępność produktów według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

Aby dowiedzieć się, jak utworzyć aplikację, korzystając z platformą Microsoft identity, postępuj zgodnie z [Samouczek Microsoft Authentication Library (MSAL)](msal-national-cloud.md). W szczególności tej aplikacji do logowania użytkownika i uzyskania tokenu dostępu do wywołania interfejsu API programu Microsoft Graph.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o usługach:

- [Platforma Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure (Niemcy)](https://docs.microsoft.com/azure/germany/)
- [Podstawy uwierzytelniania w usłudze Azure AD](authentication-scenarios.md)
