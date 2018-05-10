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
ms.openlocfilehash: d4efa6ad1d2768a41a670fae4c490942b5a814ad
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Ograniczenia dotyczące współpracy B2B usługi Azure AD
Azure współpracy B2B usługi Active Directory (Azure AD) podlega obecnie ograniczenia opisane w tym artykule.

## <a name="possible-double-multi-factor-authentication"></a>Możliwe podwójne uwierzytelnianie wieloskładnikowe
Z B2B usługi Azure AD można wymusić uwierzytelnianie wieloskładnikowe w organizacji zasobów (zaproszenia organizacji). Przyczyny tego podejścia wyszczególnione w [dostępu warunkowego dla użytkowników współpracy B2B](active-directory-b2b-mfa-instructions.md). Jeśli partnera już uwierzytelnianie wieloskładnikowe, konfigurowanie i wymuszane, użytkowników może być konieczne przeprowadzenie uwierzytelniania raz w domu organizacji, a następnie ponownie w należy do Ciebie.

## <a name="instant-on"></a>Natychmiastowa
W przepływach współpracy B2B możemy dodać użytkowników do katalogu i dynamiczne aktualizowanie ich podczas realizacji zaproszenia, przypisanie aplikacji i tak dalej. Aktualizacje i zapisy zwykle się tak zdarzyć w przypadku jednego katalogu i musi zostać zreplikowana we wszystkich wystąpieniach. Gdy wszystkie wystąpienia są aktualizowane zakończeniem replikacji. Czasami, gdy obiekt jest zapisywane lub zaktualizowane w jedno wystąpienie, a wywołanie do pobrania tego obiektu jest do innego wystąpienia, może wystąpić opóźnienia w replikacji. Jeśli tak się stanie, Odśwież lub ponów próbę, aby pomóc. Jeśli piszesz aplikację przy użyciu naszego interfejsu API ponownych prób z niektórych wycofania jest rozwiązaniem obrony, właściwą rozwiązanie tego problemu.

## <a name="next-steps"></a>Kolejne kroki

Na współpracy B2B usługi Azure AD, zobacz następujące artykuły:

- [Czym jest współpraca B2B w usłudze Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Delegowanie B2bB współpracy zaproszenia](active-directory-b2b-delegate-invitations.md)

