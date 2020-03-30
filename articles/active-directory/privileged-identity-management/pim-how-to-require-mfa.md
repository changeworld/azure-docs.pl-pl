---
title: Usługa MFA lub 2FA i zarządzanie tożsamościami uprzywilejowanymi — usługa Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak usługa Azure AD Privileged Identity Management (PIM) sprawdza poprawność uwierzytelniania wieloskładnikowego (MFA).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6df593909c3ae5962e413eb84e64196fade0326b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022152"
---
# <a name="multi-factor-authentication-and-privileged-identity-management"></a>Uwierzytelnianie wieloskładnikowe i zarządzanie tożsamościami uprzywilejowanymi

Zaleca się, aby wymagać uwierzytelniania wieloskładnikowego (MFA) dla wszystkich administratorów. Zmniejsza to ryzyko ataku z powodu złamanego hasła.

Podczas logowania użytkownicy mogą wymagać, aby ukończyli wyzwanie uwierzytelniania wieloskładnikowego. Można również wymagać, aby użytkownicy ukończyli wyzwanie uwierzytelniania wieloskładnikowego podczas aktywowania roli w usłudze Azure Active Directory (Azure AD) Privileged Identity Management (PIM). W ten sposób, jeśli użytkownik nie ukończył wyzwanie uwierzytelniania wieloskładnikowego po zalogowaniu się, zostanie poproszony o to przez zarządzanie tożsamościami uprzywilejowanymi.

> [!IMPORTANT]
> Obecnie uwierzytelnianie wieloskładnikowe platformy Azure działa tylko z kontami służbowymi lub szkolnymi, a nie kontami osobistymi Microsoft (zwykle kontem osobistym używanym do logowania się do usług firmy Microsoft, takich jak Skype, Xbox lub Outlook.com). Z tego powodu każda osoba korzystająca z konta osobistego nie może być uprawnionym administratorem, ponieważ nie może aktywować swoich ról za pomocą uwierzytelniania wieloskładnikowego. Jeśli ci użytkownicy muszą nadal zarządzać obciążeniami przy użyciu konta Microsoft, podnieś je na stałe administratorów.

## <a name="how-pim-validates-mfa"></a>Jak pim sprawdza poprawność usługi MFA

Istnieją dwie opcje sprawdzania poprawności uwierzytelniania wieloskładnikowego, gdy użytkownik aktywuje rolę.

Najprostszą opcją jest poleganie na usłudze Azure Multi-Factor Authentication dla użytkowników, którzy aktywują rolę uprzywilejowaną. Aby to zrobić, najpierw sprawdź, czy ci użytkownicy są licencjonowane, jeśli to konieczne, i zarejestrowali się dla usługi Azure Uwierzytelnianie wieloskładnikowe. Aby uzyskać więcej informacji na temat wdrażania uwierzytelniania wieloskładnikowego platformy Azure, zobacz [Wdrażanie uwierzytelniania wieloskładnikowego platformy Azure w chmurze.](../authentication/howto-mfa-getstarted.md) Zaleca się, ale nie jest wymagane, aby skonfigurować usługę Azure AD, aby wymusić uwierzytelnianie wieloskładnikowe dla tych użytkowników podczas logowania. Dzieje się tak, ponieważ sprawdzanie uwierzytelniania wieloskładnikowego będzie przeprowadzane przez samo zarządzanie tożsamościami uprzywilejowanymi.

Alternatywnie, jeśli użytkownicy uwierzytelniają się lokalnie, dostawca tożsamości może być odpowiedzialny za uwierzytelnianie wieloskładnikowe. Na przykład jeśli skonfigurowano usługi federacyjne usługi AD, aby wymagały uwierzytelniania opartego na kartach inteligentnych przed uzyskaniem dostępu do usługi Azure AD, [zabezpieczanie zasobów w chmurze za pomocą uwierzytelniania wieloskładnikowego platformy Azure i usług AD FS](../authentication/howto-mfa-adfs.md) zawiera instrukcje konfigurowania usług AD FS do wysyłania oświadczeń do usługi Azure AD. Gdy użytkownik próbuje aktywować rolę, Zarządzanie tożsamością uprzywilejowaną zaakceptuje, że uwierzytelnianie wieloskładnikowe zostało już zweryfikowane dla użytkownika po otrzymaniu odpowiednich oświadczeń.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie ustawień roli usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi](pim-how-to-change-default-settings.md)
- [Konfigurowanie ustawień roli zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi](pim-resource-roles-configure-role-settings.md)
