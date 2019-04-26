---
title: Uwierzytelnianie wieloskładnikowe (MFA) i PIM — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak usługa Azure AD Privileged Identity Management (PIM) sprawdza poprawność uwierzytelnianie wieloskładnikowe (MFA).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 08/31/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8831a838a403d58d8673c6400e0dda06c03dc69f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60442679"
---
# <a name="multi-factor-authentication-mfa-and-pim"></a>Uwierzytelnianie wieloskładnikowe (MFA) i usługi PIM

Firma Microsoft zaleca Wymagaj uwierzytelniania wieloskładnikowego (MFA) dla wszystkich administratorów. Zmniejsza to ryzyko ataku z powodu hasła ze złamanymi zabezpieczeniami.

Możesz wymagać, użytkownicy wykonać żądanie uwierzytelniania Wieloskładnikowego podczas logowania. Może również wymagać, że użytkownicy ukończyć żądanie usługi MFA podczas aktywacji roli w usłudze Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Dzięki temu, jeśli użytkownik nie została ukończona żądania uwierzytelniania MFA, gdy są one podpisane, ich zostanie wyświetlony monit to zrobić przez usługę PIM.

> [!IMPORTANT]
> Kliknij prawym przyciskiem myszy teraz usługi Azure MFA działa tylko w przypadku pracy lub konta służbowe, a nie konta Microsoft (zazwyczaj konta osobistego, która jest używana do logowania do usług firmy Microsoft, takich jak Skype, Xbox, Outlook.com itd.). W związku z tym każda osoba korzystająca z konta Microsoft nie może być administrator uprawniony ponieważ nie mogą używać usługi MFA do aktywacji ich ról. Jeśli Ci użytkownicy muszą kontynuować zarządzanie obciążeń przy użyciu konta Microsoft, podnoszenie ich poziomu do stałych administratorów teraz.

## <a name="how-pim-validates-mfa"></a>Jak sprawdza usługi MFA, usługi PIM

Dostępne są dwie opcje umożliwiające walidację MFA, gdy użytkownik aktywuje roli.

Jest to najprostsza opcja korzystaniem z usługi Azure MFA dla użytkowników, którzy aktywowania roli uprzywilejowanej. Aby to zrobić, najpierw sprawdź, czy Ci użytkownicy są licencjonowane w razie potrzeby i zarejestrowany dla usługi Azure MFA. Aby uzyskać więcej informacji na temat wdrażania usługi Azure MFA, zobacz [wdrażanie oparte na chmurze usługi Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md). Jest to zalecane, ale nie jest wymagane, należy skonfigurować wymuszanie uwierzytelniania Wieloskładnikowego dla tych użytkowników podczas logowania w usłudze Azure AD. Jest to spowodowane kontroli uwierzytelniania Wieloskładnikowego, które zostaną wprowadzone przez usługę PIM sam.

Alternatywnie Jeśli użytkownicy są uwierzytelniani w środowisku lokalnym może mieć dostawcy tożsamości do uwierzytelniania Wieloskładnikowego. Na przykład, jeśli skonfigurowano usługi federacyjnej AD wymagające uwierzytelniania opartego na karty inteligentnej przed uzyskaniem dostępu do usługi Azure AD [zabezpieczanie zasobów w chmurze za pomocą usługi Azure Multi-Factor Authentication i usług AD FS](../authentication/howto-mfa-adfs.md) zawiera instrukcje do konfigurowania usług AD FS do wysyłania oświadczeń do usługi Azure AD. Gdy użytkownik próbuje aktywować rolę, usługi PIM będzie akceptować czy MFA została już zweryfikowana użytkownika po odebraniu odpowiednie oświadczenia.

## <a name="next-steps"></a>Kolejne kroki

- [Konfigurowanie ustawień roli w usłudze Azure AD w usłudze PIM](pim-how-to-change-default-settings.md)
- [Konfigurowanie ustawień roli zasobów platformy Azure w usłudze PIM](pim-resource-roles-configure-role-settings.md)
