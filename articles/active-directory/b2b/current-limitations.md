---
title: Ograniczenia współpracy B2B — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Bieżące ograniczenia współpracy usługi Azure Active Directory B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: ffee01488ecf658ce02a20a41252aca19288667c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263363"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Ograniczenia współpracy usługi Azure AD B2B
Współpraca b2b usługi Azure Active Directory (Azure AD) podlega obecnie ograniczeniom opisanym w tym artykule.

## <a name="possible-double-multi-factor-authentication"></a>Możliwe podwójne uwierzytelnianie wieloskładnikowe
Za pomocą usługi Azure AD B2B można wymusić uwierzytelnianie wieloskładnikowe w organizacji zasobów (organizacji zapraszającej). Przyczyny takiego podejścia są szczegółowo opisane w [uzyskiwać dostęp warunkowy dla użytkowników współpracy B2B.](conditional-access.md) Jeśli partner ma już skonfigurowane i wymuszone uwierzytelnianie wieloskładnikowe, użytkownicy mogą być musieli wykonać uwierzytelnianie raz w organizacji domowej, a następnie ponownie w twojej.

## <a name="instant-on"></a>Natychmiastowe włączanie
W przepływach współpracy B2B dodajemy użytkowników do katalogu i dynamicznie aktualizujemy ich podczas realizacji zaproszenia, przypisywania aplikacji i tak dalej. Aktualizacje i zapisy zwykle zdarzają się w jednym wystąpieniu katalogu i muszą być replikowane we wszystkich wystąpieniach. Replikacja jest zakończona po zaktualizowaniu wszystkich wystąpień. Czasami, gdy obiekt jest zapisywany lub aktualizowany w jednym wystąpieniu, a wywołanie pobrania tego obiektu jest do innego wystąpienia, opóźnienia replikacji może wystąpić. Jeśli tak się stanie, odśwież lub ponów próbę pomocy. Jeśli piszesz aplikację przy użyciu naszego interfejsu API, a następnie ponownych prób z niektórych back-off jest dobrą, defensywną praktyką w celu złagodzenia tego problemu.

## <a name="azure-ad-directories"></a>Katalogi usługi Azure AD
Usługa Azure AD B2B podlega limitom katalogów usługi Azure AD. Aby uzyskać szczegółowe informacje na temat liczby katalogów, które użytkownik może utworzyć i liczby katalogów, do których może należeć użytkownik lub użytkownik-gość, zobacz [Limity i ograniczenia usługi Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions).

## <a name="national-clouds"></a>Chmury narodowe
[Chmury krajowe](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud) są fizycznie izolowane wystąpienia platformy Azure. Współpraca B2B nie jest obsługiwana w całej chmurze krajowej. Na przykład jeśli dzierżawa platformy Azure znajduje się w chmurze publicznej, globalnej, nie można zaprosić użytkownika, którego konto znajduje się w chmurze krajowej. Aby współpracować z użytkownikiem, poproś go o inny adres e-mail lub utwórz dla nich konto użytkownika w katalogu.

## <a name="azure-us-government-clouds"></a>Chmury dla instytucji rządowych platformy Azure w usa
W chmurze dla instytucji rządowych platformy Azure uss współpraca B2B jest obecnie obsługiwana tylko między dzierżawami, które są zarówno w chmurze platformy Azure us government, jak i obsługują współpracę B2B. Jeśli zaprosisz użytkownika w dzierżawie, który nie jest częścią chmury platformy Azure dla instytucji rządowych usa lub który nie obsługuje jeszcze współpracy B2B, zaproszenie zakończy się niepowodzeniem lub użytkownik nie będzie mógł zrealizować zaproszenia. Aby uzyskać szczegółowe informacje o innych [ograniczeniach, zobacz Odmiany usługi Azure Active Directory Premium P1 i P2](https://docs.microsoft.com/azure/azure-government/documentation-government-services-securityandidentity#azure-active-directory-premium-p1-and-p2).

### <a name="how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant"></a>Jak sprawdzić, czy współpraca B2B jest dostępna w dzierżawie usługi Azure dla instytucji rządowych w Stanach Zjednoczonych?
Aby dowiedzieć się, czy dzierżawa chmury usługi Azure us government obsługuje współpracę B2B, wykonaj następujące czynności:

1. W przeglądarce przejdź do następującego adresu URL, zastępując nazwę dzierżawy nazwą * &lt;najemcy:&gt;*

   `https://login.microsoftonline.com/<tenantname>/v2.0/.well-known/openid-configuration`

2. Znajdź `"tenant_region_scope"` w odpowiedzi JSON:

   - Jeśli `"tenant_region_scope":"USGOV”` się pojawi, B2B jest obsługiwany.
   - Jeśli `"tenant_region_scope":"USG"` pojawi się, B2B nie jest obsługiwany.

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły dotyczące współpracy usługi Azure AD B2B:

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [Delegowanie zaproszeń do współpracy B2B](delegate-invitations.md)

