---
title: Ograniczenia współpracy usługi Azure Active Directory B2B | Dokumentacja firmy Microsoft
description: Bieżących ograniczeń dotyczących współpracy usługi Azure Active Directory B2B
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/23/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 562076e9529ffeac4cb0f99c1ffd4d4866d0bd1a
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
ms.locfileid: "34260058"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Ograniczenia dotyczące współpracy B2B usługi Azure AD
Azure współpracy B2B usługi Active Directory (Azure AD) podlega obecnie ograniczenia opisane w tym artykule.

## <a name="possible-double-multi-factor-authentication"></a>Możliwe podwójne uwierzytelnianie wieloskładnikowe
Z B2B usługi Azure AD można wymusić uwierzytelnianie wieloskładnikowe w organizacji zasobów (zaproszenia organizacji). Przyczyny tego podejścia wyszczególnione w [dostępu warunkowego dla użytkowników współpracy B2B](conditional-access.md). Jeśli partnera już uwierzytelnianie wieloskładnikowe, konfigurowanie i wymuszane, użytkowników może być konieczne przeprowadzenie uwierzytelniania raz w domu organizacji, a następnie ponownie w należy do Ciebie.

## <a name="instant-on"></a>Natychmiastowa
W przepływach współpracy B2B możemy dodać użytkowników do katalogu i dynamiczne aktualizowanie ich podczas realizacji zaproszenia, przypisanie aplikacji i tak dalej. Aktualizacje i zapisy zwykle się tak zdarzyć w przypadku jednego katalogu i musi zostać zreplikowana we wszystkich wystąpieniach. Gdy wszystkie wystąpienia są aktualizowane zakończeniem replikacji. Czasami, gdy obiekt jest zapisywane lub zaktualizowane w jedno wystąpienie, a wywołanie do pobrania tego obiektu jest do innego wystąpienia, może wystąpić opóźnienia w replikacji. Jeśli tak się stanie, Odśwież lub ponów próbę, aby pomóc. Jeśli piszesz aplikację przy użyciu naszego interfejsu API ponownych prób z niektórych wycofania jest rozwiązaniem obrony, właściwą rozwiązanie tego problemu.

## <a name="next-steps"></a>Kolejne kroki

Na współpracy B2B usługi Azure AD, zobacz następujące artykuły:

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [Delegowanie B2bB współpracy zaproszenia](delegate-invitations.md)

