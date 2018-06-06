---
title: Azure zasady usługi Active Directory dostępu warunkowego urządzeń dla usług Office 365 | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu obsługi administracyjnej zasady dostępu warunkowego urządzeń, aby sprawić, że zasoby firmy więcej bezpieczeństwo, zachowując zgodność użytkownika i dostęp do usług.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8664c0bb-bba1-4012-b321-e9c8363080a0
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: cc385edb6bf0b850eb97f0f6a233dd5081acbb3e
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34723962"
---
# <a name="active-directory-conditional-access-device-policies-for-office-365-services"></a>Active Directory dostępu warunkowego zasady urządzeń dla usług Office 365

Dostęp warunkowy wymaga wielu elementów pracy. Obejmuje usługi Multi-Factor uwierzytelnianego użytkownika uwierzytelnionego urządzenia i zgodnych urządzeń, między innymi. W tym artykule firma Microsoft głównie skupić się na warunki oparta na urządzeniach, które organizacja może używać ułatwiające kontroli dostępu do usług Office 365. 

Firmowej użytkownicy mają dostęp do usługi Office 365 usług takich jak Exchange i SharePoint Online w pracy lub nauki z urządzeń osobistych. Aby uzyskiwać dostęp do zabezpieczenia. Można udostępnić zasady dostępu warunkowego urządzeń do zabezpieczania zasobów firmy, jednocześnie udzielać dostępu do usługi dla użytkowników, którzy korzystają ze zgodnych urządzeń. Można ustawić zasady dostępu warunkowego do usługi Office 365 w portalu Microsoft Intune dostępu warunkowego.

Azure Active Directory (Azure AD) wymusza zasady dostępu warunkowego, aby zabezpieczać dostęp do usług Office 365. Można utworzyć zasady dostępu warunkowego, która blokuje użytkownika, który używa urządzenia niezgodne z uzyskiwanie dostępu do usługi Office 365. Użytkownik musi być zgodna z zasadami firmy urządzenie, zanim zostanie przyznany dostęp do usługi. Alternatywnie można utworzyć zasadę, która wymaga od użytkowników do rejestrowania swoich urządzeń w celu uzyskania dostępu do usługi Office 365. Zasady mogą być stosowane do wszystkich użytkowników w organizacji lub ograniczone do kilku grup docelowych. Możesz dodać więcej grup docelowych dla zasad wraz z upływem czasu.

Wymagane w przypadku wymuszania zasad urządzeń to użytkownicy muszą zarejestrować swoje urządzenia w usłudze rejestracji urządzeń usługi Azure AD. Można włączyć, aby włączyć uwierzytelnianie wieloskładnikowe dla urządzeń, które rejestrują się przy użyciu usługi rejestracji urządzeń usługi Azure AD. Uwierzytelnianie wieloskładnikowe jest zalecane dla usługi rejestracji urządzeń usługi Azure Active Directory. Po włączeniu usługi Multi-Factor authentication dla uwierzytelniania dwuskładnikowego wąskie użytkownikom rejestrowanie swoich urządzeń w usłudze rejestracji urządzeń usługi Azure AD.

## <a name="how-does-a-conditional-access-policy-work"></a>Jak działa zasady dostępu warunkowego?

Podczas żądania dostępu użytkownika do usługi Office 365 z platformy obsługiwane urządzenia usługi Azure AD uwierzytelnia użytkownika i urządzenia. Usługi Azure AD udziela dostępu do usługi tylko, jeśli użytkownik jest zgodny z zasadami, ustaw dla usługi. Użytkownicy na urządzeniach, które nie są zarejestrowane otrzymuje instrukcje na temat sposobu zarejestrować i zapewnić zgodność dostępu do firmowej usług Office 365. Użytkownicy na urządzeniach z systemem Android i iOS muszą zarejestrować swoje urządzenia za pomocą aplikacji Portal firmy usługi Intune. Gdy użytkownik rejestruje urządzenia, urządzenie jest zarejestrowane w usłudze Azure AD i jest ono zarejestrowane na potrzeby zarządzania urządzeniami i zgodności. Należy użyć usługi rejestracji urządzeń usługi Azure AD w usłudze Microsoft Intune do zarządzania urządzeniami przenośnymi dla usługi Office 365. Rejestracji urządzenia jest wymagany w przypadku użytkowników usługi Office 365 dostęp do usług, gdy urządzenie zasady są wymuszane.

Gdy użytkownik pomyślnie rejestruje urządzenie, urządzenie staje się zaufany. Usługi Azure AD zapewnia uwierzytelnionego użytkownika pojedynczy znak na dostęp do aplikacji firmy. Usługi Azure AD wymusza zasady dostępu warunkowego, aby udzielić dostępu do usługi nie tylko po raz pierwszy użytkownik żąda dostępu, ale zawsze użytkownika odnawia na żądanie dostępu. Podczas poświadczenia logowania są zmieniane, utraty lub kradzieży urządzenia lub w czasie żądania odnowienia nie są spełnione warunki zasad, użytkownik otrzyma odmowę dostępu do usług.

## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

Usługi rejestracji urządzeń usługi Azure AD musi być używany do rejestrowania urządzeń.

Jeśli użytkownicy lokalnych są o należy uwierzytelnieni, Active Directory Federation Services (AD FS) (w wersji 1.0 lub nowszym) jest wymagana. Uwierzytelnianie wieloskładnikowe dla dołączania nie powiedzie się, gdy dostawca tożsamości nie jest zdolny do usługi Multi-Factor Authentication. Na przykład nie można użyć uwierzytelniania wieloskładnikowego z usługami AD FS 2.0. Upewnij się, że lokalnej usługi AD FS współpracuje z usługi Multi-Factor authentication, a metoda prawidłowy uwierzytelnianie wieloskładnikowe jest na miejscu przed włączeniem uwierzytelniania wieloskładnikowego dla usługi rejestracji urządzeń usługi Azure AD. Na przykład usługi AD FS w systemie Windows Server 2012 R2 ma funkcje usługi Multi-Factor authentication. Należy również ustawić metodę dodatkowe prawidłowy uwierzytelniania (uwierzytelnianie wieloskładnikowe) na serwerze usług AD FS przed włączeniem uwierzytelniania wieloskładnikowego dla usługi rejestracji urządzeń usługi Azure AD. Aby uzyskać więcej informacji na temat metod obsługiwanych uwierzytelnianie wieloskładnikowe w usługach AD FS, zobacz [Konfigurowanie dodatkowych metod uwierzytelniania usług AD FS](/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs).

## <a name="next-steps"></a>Kolejne kroki

*   Aby uzyskać odpowiedzi na często zadawane pytania, zobacz [dostępu warunkowego w usłudze Azure Active Directory — często zadawane pytania](active-directory-conditional-faqs.md).
