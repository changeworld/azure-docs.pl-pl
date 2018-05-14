---
title: Dodawanie użytkowników współpracy B2B do usługi Azure Active Directory bez zaproszenia | Dokumentacja firmy Microsoft
description: Możesz pozwolić, aby dodać inne gości do usługi Azure AD bez realizowanie zaproszenie w usłudze Azure Active Directory B2B współpracy użytkownika gościa.
services: active-directory
documentationcenter: ''
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 5a37a5a14dcb07db7e078558072f7edad7432d9b
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation"></a>Dodaj gości współpracy B2B bez zaproszenia

> [!NOTE]
> Teraz, Gość użytkownicy nie muszą już wiadomość e-mail z zaproszeniem z wyjątkiem w niektórych przypadkach specjalnych. Aby uzyskać więcej informacji, zobacz [realizacji zaproszenia współpracy B2B](active-directory-b2b-redemption-experience.md).  

Można zezwolić użytkownikowi, takich jak przedstawiciela partnera, aby dodać użytkowników od partnera do organizacji bez konieczności zaproszenia do zostać zrealizowane. Wszystko, co należy wykonać to nadać temu użytkownikowi wyliczenie uprawnienia w katalogu używanego org. partnera 

Udziel tych uprawnień, gdy:

1. Użytkownik w organizacji hosta (na przykład WoodGrove) zaprasza jeden użytkownik z organizacji partnerskiej (na przykład Sam@litware.com) jako Gość.
2. Administrator w organizacji hosta [konfiguruje zasady](active-directory-b2b-delegate-invitations.md) umożliwiające Sam do identyfikowania i dodać inni użytkownicy z organizacji partnerskiej (Litware).
3. Teraz Sam można dodawać innych użytkowników z Litware do katalogu WoodGrove, grupy lub aplikacji bez konieczności zaproszenia do zostać zrealizowane. Jeśli Sam ma wyliczenie odpowiednie uprawnienia w Litware, odbywa się automatycznie.

### <a name="next-steps"></a>Kolejne kroki

- [Czym jest współpraca B2B w usłudze Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Jak pracownicy przetwarzający informacje dodać użytkowników współpracy B2B?](active-directory-b2b-iw-add-users.md)
- [Realizacji zaproszenia współpracy B2B](active-directory-b2b-redemption-experience.md)
- [Delegowanie zaproszeń do skorzystania z usługi Azure Active Directory B2B współpracy](active-directory-b2b-delegate-invitations.md)

