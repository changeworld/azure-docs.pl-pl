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
ms.openlocfilehash: a0d4586df23548854f4acbfefd32081a36906097
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067894"
---
# <a name="national-clouds"></a>Chmury krajowe

Chmury krajowe są odizolowane fizycznie wystąpienia platformy Azure. Te regiony platformy Azure są przeznaczone do upewnij się, że wymagania dotyczące zgodności, niezależności i rezydencji danych są honorowane w granicach geograficznych.

W tym chmury globalnej usługi Azure Active Directory jest wdrażany w chmurach narodowych następujące:  

- Wersja platformy Azure dla administracji USA
- Azure (Niemcy)
- Azure w Chinach — 21Vianet

Chmury krajowe są unikatowe i różne środowiska niż globalna platforma Azure. W związku z tym należy mieć świadomość niektóre podstawowe różnice podczas tworzenia aplikacji dla tych środowisk, takich jak rejestrowanie aplikacji i uzyskiwania tokenów certyfikatu oraz konfigurowanie punktów końcowych.

## <a name="app-registration-endpoints"></a>Punkty końcowe rejestracji aplikacji

Brak osobnego portalu platformy Azure dla każdej z nich chmur krajowych. Do zintegrowania aplikacji z platformą tożsamości Microsoft w chmurze krajowej, musisz zarejestrować aplikację oddzielnie w każdej z witryny Azure portal specyficzne dla środowiska.

Poniższa tabela zawiera listę podstawowych adresach URL używany do rejestrowania aplikacji w przypadku każdej chmury krajowe punktów końcowych usługi Azure Active Directory (Azure AD).

| Chmury krajowe | Usługi AD portalu punkt końcowy platformy Azure |
|----------------|--------------------------|
| Usługa Azure AD dla instytucji rządowych USA | `https://portal.azure.us` |
| Niemiecka wersja platformy Azure AD | `https://portal.microsoftazure.de` |
| Usługi AD chińska wersja platformy Azure obsługiwane przez firmę 21Vianet | `https://portal.azure.cn` |
| Usługa Azure AD (usługa globalne) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Punkty końcowe uwierzytelniania usługi Azure AD

Chmur krajowych uwierzytelniać użytkowników oddzielnie w ramach każdego środowiska i punkty końcowe uwierzytelniania oddzielne.

Poniższa tabela zawiera listę podstawowych adresach URL używany do uzyskiwania tokenów dla każdej chmury krajowe punktów końcowych usługi Azure Active Directory (Azure AD).

| Chmury krajowe | Punkt końcowy uwierzytelniania usługi Azure AD |
|----------------|-------------------------|
| Usługa Azure AD dla instytucji rządowych USA | `https://login.microsoftonline.us` |
| Niemiecka wersja platformy Azure AD| `https://login.microsoftonline.de` |
| Usługi AD chińska wersja platformy Azure obsługiwane przez firmę 21Vianet | `https://login.chinacloudapi.cn` |
| Usługa Azure AD (usługa globalne)| `https://login.microsoftonline.com` |

- Żądania do punktów końcowych autoryzacji lub tokenu usługi Azure AD można utworzyć przy użyciu odpowiednich specyficzne dla regionu podstawowego adresu URL. Na przykład Azure (Niemcy):

  - Typowe punkt końcowy autoryzacji jest `https://login.microsoftonline.de/common/oauth2/authorize`.
  - Typowe punktu końcowego tokenu jest `https://login.microsoftonline.de/common/oauth2/token`.

- Dla jednej dzierżawy aplikacji, Zastąp często używany w poprzednim adresy URL z Identyfikatorem dzierżawy lub nazwa, na przykład `https://login.microsoftonline.de/contoso.com`.

> [!NOTE]
> [Usługi Azure AD v2.0 autoryzacji]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview) i tokenu punkty końcowe są dostępne tylko dla usługi global service. Nie jest jeszcze obsługiwane w przypadku wdrożeń chmur krajowych.

## <a name="microsoft-graph-api"></a>Interfejs API programu Microsoft Graph

Aby dowiedzieć się, jak wywoływać interfejsy API programu Graph firmy Microsoft w środowisku chmury krajowe, przejdź do [programu Microsoft Graph w chmurze krajowej](https://developer.microsoft.com/graph/docs/concepts/deployments).

> [!IMPORTANT]
> Niektórych usług i funkcji, które znajdują się w określonych regionach usługi global service mogą nie być dostępne we wszystkich chmurach krajowych. Aby dowiedzieć się, jakie usługi są dostępne przejdź do [dostępność produktów według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

Postępuj zgodnie z tym [Samouczek Microsoft Authentication Library (MSAL)](msal-national-cloud.md) informacje na temat tworzenia aplikacji przy użyciu platformy tożsamości firmy Microsoft. Ściślej mówiąc ta aplikacja będzie logowania użytkownika, Uzyskaj token dostępu do wywołania interfejsu API programu Microsoft Graph.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o usługach:

- [Platforma Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure (Niemcy)](https://docs.microsoft.com/azure/germany/)
- [Podstawy uwierzytelniania w usłudze Azure AD](authentication-scenarios.md)
