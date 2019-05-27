---
title: Luki w zabezpieczeniach wykryte przez usługi Azure Active Directory Identity Protection
description: Omówienie luk w zabezpieczeniach wykryte przez usługi Azure Active Directory Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: article
ms.date: 04/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e792551f4cac857f56454c67d527e01cb9c4281
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66113126"
---
# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Luki w zabezpieczeniach wykryte przez usługi Azure Active Directory Identity Protection

Luki w zabezpieczeniach występują luki w środowisku, w którym może zostać wykorzystana przez osobę atakującą. Firma Microsoft zaleca, że administratorzy rozwiązać te luki w zabezpieczeniach w celu zwiększenia poziomu bezpieczeństwa organizacji.

![Usterki zgłoszone przez ochronę tożsamości](./media/vulnerabilities/identity-protection-vulnerabilities.png)

Poniższe sekcje zawierają omówienie usterki zgłoszone przez ochronę tożsamości.

## <a name="multi-factor-authentication-registration-not-configured"></a>Rejestracja korzystająca z uwierzytelniania Multi-Factor Authentication, nieskonfigurowane

Tę lukę w zabezpieczeniach pomaga ocenić wdrożenia usługi Azure Multi-Factor Authentication w Twojej organizacji.

Usługa Azure Multi-Factor Authentication udostępnia drugą warstwę zabezpieczeń do uwierzytelniania użytkowników. Jego pomaga w zabezpieczeniu dostępu do danych i aplikacji, a jednocześnie spełnia wymagania użytkowników dotyczące prostoty procesu logowania. Usługa Azure Multi-Factor Authentication udostępnia opcje weryfikacji łatwy w użyciu, takie jak:

* Połączenie telefoniczne
* Wiadomość SMS
* Powiadomienia w aplikacji mobilnej
* Kod weryfikacyjny OTP

Firma Microsoft zaleca, wymagają usługi Azure Multi-Factor Authentication do logowania użytkownika. Uwierzytelnianie wieloskładnikowe odgrywa kluczową rolę w zasadach dostępu warunkowego na podstawie ryzyka dostępne za pośrednictwem Identity Protection.

Aby uzyskać więcej informacji, zobacz [What is Azure Multi-Factor Authentication? (Co to jest usługa Azure Multi-Factor Authentication?)](../authentication/multi-factor-authentication.md)

## <a name="unmanaged-cloud-apps"></a>Niezarządzane aplikacje w chmurze

Tę lukę w zabezpieczeniach pomaga zidentyfikować niezarządzane aplikacje w chmurze w Twojej organizacji.

Personel IT są często nie wszystkie aplikacje w chmurze w organizacji. To proste zobaczyć, dlaczego administratorzy będą niepokoją nieautoryzowanego dostępu do danych firmowych, wyciekom danych i innych zagrożeń bezpieczeństwa.

Firma Microsoft zaleca wdrażanie rozwiązania Cloud Discovery odnajdywania aplikacji w chmurze niezarządzanych i zarządzanie te aplikacje przy użyciu usługi Azure Active Directory.

Aby uzyskać więcej informacji, zobacz [rozwiązania Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="security-alerts-from-privileged-identity-management"></a>Alerty zabezpieczeń z usługi Privileged Identity Management

Tę lukę w zabezpieczeniach pomaga użytkownikom odnajdywanie ich i rozwiązywanie alertów dotyczących tożsamości uprzywilejowanych w Twojej organizacji.  

Aby użytkownicy mogli wykonywać operacje uprzywilejowane, organizacje muszą udzielić użytkownikom tymczasowy lub stały uprzywilejowanego dostępu w usłudze Azure AD, zasobów platformy Azure lub usługi Office 365 lub innych aplikacji SaaS. Każda z tych użytkowników uprzywilejowanych zwiększa obszar narażony na Twojej organizacji. Tę lukę w zabezpieczeniach ułatwia identyfikowanie użytkowników z dostępem uprzywilejowanym niepotrzebne i podjąć odpowiednie działania w celu zmniejszenia lub wyeliminowania ryzyka, które stanowią one.

Zalecamy użycie organizacji usługi Azure AD Privileged Identity Management, aby zarządzać, kontrolować i monitorowanie uprzywilejowanych tożsamości w usłudze Azure AD, a także innych usług online firmy Microsoft, takich jak usługi Office 365 lub Microsoft Intune.

Aby uzyskać więcej informacji, zobacz [Usługa Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md).

## <a name="see-also"></a>Zobacz także

[Ochrona tożsamości w usłudze Azure Active Directory](../active-directory-identityprotection.md)
