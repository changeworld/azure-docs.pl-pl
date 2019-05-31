---
title: Ograniczenia współpracy B2B — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Bieżące ograniczenia współpracy B2B usługi Azure Active Directory
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
ms.openlocfilehash: d1466614dfeb8fa4d3d095bae070d01c6503c5bb
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357240"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Ograniczenia współpracy B2B usługi Azure AD
Współpraca z usługą Azure Active Directory (Azure AD) B2B jest obecnie dostępna z zastrzeżeniem ograniczeń, opisane w tym artykule.

## <a name="possible-double-multi-factor-authentication"></a>Możliwe double uwierzytelnianie wieloskładnikowe
Dzięki usłudze Azure AD B2B można wymusić uwierzytelnianie wieloskładnikowe w organizacji zasobów (organizacji zapraszającej). Przyczyny tego podejścia są szczegółowo opisane w [dostęp warunkowy dla użytkowników współpracy B2B](conditional-access.md). Jeśli partner ma już uwierzytelnianie wieloskładnikowe, konfigurowanie i wymuszane, użytkownicy może być konieczne przeprowadzenie uwierzytelniania raz w swojej organizacji macierzystej a następnie ponownie w należy do Ciebie.

## <a name="instant-on"></a>Natychmiastowa
W przepływach współpracy B2B możemy dodać użytkowników do katalogu i dynamicznie aktualizować podczas realizacja zaproszenia, przypisywania aplikacji i tak dalej. Aktualizacje i zapisy zwykle odbywa się w wystąpieniu jednego katalogu i musi zostać zreplikowana we wszystkich wystąpieniach. Replikacja jest wykonywana po wszystkich wystąpień są aktualizowane. Czasami w przypadku, gdy obiekt są zapisywane lub aktualizowane w jednym wystąpieniu i wywołanie do pobrania tego obiektu jest do innego wystąpienia, może wystąpić opóźnienia w replikacji. Jeśli tak się stanie, należy odświeżyć lub ponów próbę, aby pomóc. Jeśli piszesz aplikację przy użyciu naszego interfejsu API ponownych prób przy użyciu niektórych wycofywania jest rozwiązaniem dobre, obrony w celu złagodzenie tego problemu.

## <a name="azure-ad-directories"></a>Katalogi usługi Azure AD
Usługa Azure AD B2B jest podlegają usługi Azure AD directory limitów usług. Szczegółowe informacje na temat liczby katalogów, użytkownik może utworzyć i liczba katalogów do których użytkownik lub użytkownik-Gość może należą, zobacz [usługi Azure AD, ograniczenia i limity](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions).

## <a name="national-clouds"></a>Chmury krajowe
[Chmury krajowe](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud) są odizolowane fizycznie wystąpienia platformy Azure. Współpraca B2B nie jest obsługiwana w granicach chmur krajowych. Na przykład w dzierżawie platformy Azure znajduje się w chmurze publicznej, globalne, nie można zaprosić użytkownika, którego konto znajduje się w chmurze krajowej. Aby współpracować z użytkownikiem, poproś innego adresu e-mail lub Utwórz konto użytkownika elementu członkowskiego dla nich w katalogu.

## <a name="next-steps"></a>Kolejne kroki

Na współpracy B2B usługi Azure AD, zobacz następujące artykuły:

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [Delegowanie zaproszeń współpracy B2B](delegate-invitations.md)

