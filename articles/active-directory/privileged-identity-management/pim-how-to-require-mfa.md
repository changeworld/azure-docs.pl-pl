---
title: Uwierzytelnianie wieloskładnikowe (MFA) i PIM-Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak Azure AD Privileged Identity Management (PIM) sprawdza poprawność uwierzytelniania wieloskładnikowego (MFA).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 08/31/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 686c39c9fb1f1ff7c0ecf068a6612f530620d5dc
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804301"
---
# <a name="multi-factor-authentication-mfa-and-pim"></a>Uwierzytelnianie wieloskładnikowe (MFA) i PIM

Zalecamy wymaganie uwierzytelniania wieloskładnikowego (MFA) dla wszystkich administratorów. Zmniejsza to ryzyko ataków spowodowanych przez złamane hasło.

Możesz wymagać, aby użytkownicy ukończyli wyzwanie usługi MFA podczas logowania. Możesz również wymagać, aby użytkownicy ukończyli wyzwanie usługi MFA, gdy aktywują rolę w usłudze Azure Active Directory (Azure AD) Privileged Identity Management (PIM). W ten sposób, jeśli użytkownik nie wykonał żądania MFA po zalogowaniu się, zostanie wyświetlony monit o jego przeprowadzenie przez program PIM.

> [!IMPORTANT]
> Obecnie usługa Azure MFA działa tylko z kontami służbowymi, a nie kontami firmy Microsoft (zazwyczaj konta osobistego, które jest używane do logowania się do usług firmy Microsoft, takich jak Skype, Xbox, Outlook.com itp.). W związku z tym każda osoba korzystająca z konto Microsoft nie może być uprawnionym administratorem, ponieważ nie mogą aktywować ich ról przy użyciu usługi MFA. Jeśli Ci użytkownicy muszą kontynuować zarządzanie obciążeniami przy użyciu konto Microsoft, w razie potrzeby Podnieś poziom uprawnień do stałych administratorów.

## <a name="how-pim-validates-mfa"></a>Jak PIM sprawdza poprawność uwierzytelniania MFA

Istnieją dwie opcje sprawdzania poprawności usługi MFA, gdy użytkownik aktywuje rolę.

Najprostszą opcją jest poleganie na usłudze Azure MFA dla użytkowników, którzy aktywują rolę uprzywilejowaną. Aby to zrobić, najpierw sprawdź, czy Ci użytkownicy mają licencję, w razie potrzeby, i zarejestrowano usługę Azure MFA. Aby uzyskać więcej informacji o sposobie wdrażania usługi Azure MFA, zobacz [wdrażanie usługi azure Multi-Factor Authentication opartej na chmurze](../authentication/howto-mfa-getstarted.md). Zalecane jest, ale nie wymagane, skonfigurowanie usługi Azure AD w celu wymuszania uwierzytelniania MFA dla tych użytkowników podczas logowania. Wynika to z faktu, że sprawdzanie MFA zostanie przeprowadzone przez program PIM.

Alternatywnie, jeśli użytkownicy uwierzytelniają się lokalnie, dostawca tożsamości może być odpowiedzialny za usługę MFA. Jeśli na przykład skonfigurowano usługi federacyjne AD do wymagania uwierzytelniania opartego na karcie inteligentnej przed uzyskaniem dostępu do usługi Azure AD, [Zabezpieczanie zasobów w chmurze za pomocą usługi azure Multi-Factor Authentication i AD FS](../authentication/howto-mfa-adfs.md) zawiera instrukcje dotyczące konfigurowania AD FS do programu Wysyłanie oświadczeń do usługi Azure AD. Gdy użytkownik próbuje aktywować rolę, program PIM przyjmie, że uwierzytelnianie wieloskładnikowe zostało już zweryfikowane dla użytkownika po odebraniu odpowiednich oświadczeń.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie ustawień roli usługi Azure AD w usłudze PIM](pim-how-to-change-default-settings.md)
- [Konfigurowanie ustawień roli zasobów platformy Azure w usłudze PIM](pim-resource-roles-configure-role-settings.md)
