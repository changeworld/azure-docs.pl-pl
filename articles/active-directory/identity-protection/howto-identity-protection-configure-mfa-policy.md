---
title: Konfigurowanie zasad rejestracji usługi MFA — ochrona tożsamości usługi Azure Active Directory
description: Dowiedz się, jak skonfigurować zasady rejestracji uwierzytelniania wieloskładnikowego usługi Azure AD Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fa6a4cf184b426355f62117ea51642127eee529
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382141"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>Jak: Konfigurowanie zasad rejestracji uwierzytelniania wieloskładnikowego platformy Azure

Usługa Azure AD Identity Protection ułatwia zarządzanie wdrażaniem rejestracji usługi Azure Multi-Factor Authentication (MFA), konfigurując zasady dostępu warunkowego, aby wymagać rejestracji usługi MFA bez względu na to, do jakiej nowoczesnej aplikacji uwierzytelniania się logujesz.

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Co to jest zasada rejestracji uwierzytelniania wieloskładnikowego platformy Azure?

Uwierzytelnianie wieloskładnikowe platformy Azure umożliwia sprawdzenie, kogo używasz więcej niż tylko nazwy użytkownika i hasła. Zapewnia drugą warstwę zabezpieczeń do logowania użytkownika. Aby użytkownicy mogli odpowiadać na monity usługi MFA, muszą najpierw zarejestrować się w celu uwierzytelniania wieloskładnikowego platformy Azure.

Zaleca się, aby wymagać uwierzytelniania wieloskładnikowego platformy Azure dla logowania użytkowników, ponieważ:

- Zapewnia silne uwierzytelnianie za pomocą wielu opcji weryfikacji.
- Odgrywa kluczową rolę w przygotowaniu organizacji do samodzielnego korygowania z wykrywania ryzyka w ochronie tożsamości.

Aby uzyskać więcej informacji na temat uwierzytelniania wieloskładnikowego platformy Azure, zobacz [Co to jest uwierzytelnianie wieloskładnikowe platformy Azure?](../authentication/howto-mfa-getstarted.md)

## <a name="policy-configuration"></a>Konfiguracja zasad

1. Przejdź do [witryny Azure portal](https://portal.azure.com).
1. Przejdź do zasad rejestracji usługi **Azure Security** > **Security** > **Identity Protection** > **MFA**.
   1. W obszarze **Przydziały**
      1. **Użytkownicy** — wybierz **wszystkich użytkowników** lub Wybierz osoby **i grupy,** jeśli ograniczysz wdrożenie.
         1. Opcjonalnie można wykluczyć użytkowników z zasad.
   1. W obszarze **Kontrole**
      1. Upewnij się, że pole wyboru **Wymagaj rejestracji usługi Azure MFA** jest zaznaczone i wybierz wybierz **.**
   1. **Wymuszaj zasady** - **włączone**
   1. **Zapisz**

## <a name="user-experience"></a>Środowisko użytkownika

Usługa Azure Active Directory Identity Protection poprosi użytkowników o zarejestrowanie się przy następnym loguchaniu interaktywnym i będą mieli 14 dni na ukończenie rejestracji. W tym 14-dniowym okresie mogą pominąć rejestrację, ale pod koniec okresu będą musieli się zarejestrować, zanim będą mogli zakończyć proces logowania.

Aby uzyskać omówienie powiązanych doświadczeń użytkownika, zobacz:

- [Środowisko logowania za pomocą usługi Azure AD Identity Protection](concept-identity-protection-user-experience.md).  

## <a name="next-steps"></a>Następne kroki

- [Włączanie zasad logowania i ryzyka użytkownika](howto-identity-protection-configure-risk-policies.md)

- [Włączanie samoobsługowego resetowania hasła usługi Azure AD](../authentication/howto-sspr-deployment.md)

- [Włączanie usługi Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
