---
title: Uwierzytelnianie usługi Azure AD & chmury krajowe | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o punktach końcowych rejestracji aplikacji i uwierzytelniania dla chmur krajowych.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: negoe
ms.reviewer: negoe,celested
ms.custom: aaddev
ms.openlocfilehash: 20a053369149dc29d6485c49bb091a75bb9fb591
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262999"
---
# <a name="national-clouds"></a>Chmury narodowe

Chmury krajowe są fizycznie izolowane wystąpienia platformy Azure. Te regiony platformy Azure zostały zaprojektowane tak, aby upewnić się, że wymagania dotyczące rezydencji danych, suwerenności i zgodności są przestrzegane w granicach geograficznych.

W tym chmury globalnej, usługa Azure Active Directory (Azure AD) jest wdrażana w następujących chmurach krajowych:  

- Azure Government
- Azure (Niemcy)
- Azure w Chinach — 21Vianet

Chmury krajowe są unikatowe i oddzielne środowisko od platformy Azure global. Ważne jest, aby zdawać sobie sprawę z kluczowych różnic podczas tworzenia aplikacji dla tych środowisk. Różnice obejmują rejestrowanie aplikacji, pobieranie tokenów i konfigurowanie punktów końcowych.

## <a name="app-registration-endpoints"></a>Punkty końcowe rejestracji aplikacji

Istnieje oddzielny portal platformy Azure dla każdej chmury krajowej. Aby zintegrować aplikacje z platformą tożsamości firmy Microsoft w chmurze krajowej, musisz zarejestrować aplikację oddzielnie w każdym portalu platformy Azure, który jest specyficzny dla środowiska.

W poniższej tabeli wymieniono podstawowe adresy URL dla punktów końcowych usługi Azure AD używanych do rejestrowania aplikacji dla każdej chmury krajowej.

| Chmura krajowa | Punkt końcowy portalu usługi Azure AD |
|----------------|--------------------------|
| Usługa Azure AD dla instytucji rządowych usa | `https://portal.azure.us` |
| Usługa Azure AD w Niemczech | `https://portal.microsoftazure.de` |
| Platforma Azure AD China obsługiwana przez firmę 21Vianet | `https://portal.azure.cn` |
| Usługa Azure AD (usługa globalna) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Punkty końcowe uwierzytelniania usługi Azure AD

Wszystkie chmury krajowe uwierzytelniają użytkowników oddzielnie w każdym środowisku i mają oddzielne punkty końcowe uwierzytelniania.

W poniższej tabeli wymieniono podstawowe adresy URL dla punktów końcowych usługi Azure AD używanych do uzyskiwania tokenów dla każdej chmury krajowej.

| Chmura krajowa | Punkt końcowy uwierzytelniania usługi Azure AD |
|----------------|-------------------------|
| Usługa Azure AD dla instytucji rządowych usa | `https://login.microsoftonline.us` |
| Usługa Azure AD w Niemczech| `https://login.microsoftonline.de` |
| Platforma Azure AD China obsługiwana przez firmę 21Vianet | `https://login.chinacloudapi.cn` |
| Usługa Azure AD (usługa globalna)| `https://login.microsoftonline.com` |

Żądania można tworzyć do autoryzacji usługi Azure AD lub tokenu końcowego przy użyciu odpowiedniego adresu URL podstawowego specyficzne dla regionu. Na przykład dla platformy Azure Niemcy:

  - Autoryzacyjny `https://login.microsoftonline.de/common/oauth2/authorize`wspólny punkt końcowy to .
  - Wspólny punkt końcowy `https://login.microsoftonline.de/common/oauth2/token`tokenu to .

W przypadku aplikacji z jedną dzierżawą zastąp "wspólne" w poprzednich adresach URL identyfikatorem lub nazwą dzierżawy. Może to być na przykład `https://login.microsoftonline.de/contoso.com`.

## <a name="microsoft-graph-api"></a>Interfejs API programu Microsoft Graph

Aby dowiedzieć się, jak wywoływać interfejsy API programu Microsoft Graph w środowisku chmury krajowej, przejdź do [programu Microsoft Graph we wdrożeniach chmury krajowej](https://developer.microsoft.com/graph/docs/concepts/deployments).

> [!IMPORTANT]
> Niektóre usługi i funkcje, które znajdują się w określonych regionach usługi globalnej, mogą nie być dostępne we wszystkich chmurach krajowych. Aby dowiedzieć się, jakie usługi są dostępne, przejdź do [strony Produkty dostępne według regionu](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

Aby dowiedzieć się, jak utworzyć aplikację przy użyciu platformy tożsamości firmy Microsoft, wykonaj [samouczek biblioteki uwierzytelniania firmy Microsoft (MSAL).](msal-national-cloud.md) W szczególności ta aplikacja zaloguje się użytkownika i uzyskać token dostępu do wywołania interfejsu API programu Microsoft Graph.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o usługach:

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure w Chinach — 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure (Niemcy)](https://docs.microsoft.com/azure/germany/)
- [Podstawowe informacje o uwierzytelnianiu usługi Azure AD](authentication-scenarios.md)
