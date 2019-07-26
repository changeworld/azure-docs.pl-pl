---
title: Luki w zabezpieczeniach wykryte przez Azure Active Directory Identity Protection
description: Omówienie luk w zabezpieczeniach wykrytych przez Azure Active Directory Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 446e2ef33b714afe06f24f3dbfc46bc6a42790b8
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335154"
---
# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Luki w zabezpieczeniach wykryte przez Azure Active Directory Identity Protection

Luki w zabezpieczeniach w środowisku, które mogą być wykorzystywane przez osobę atakującą, są słabe. Zaleca się, aby administratorzy usuwali te luki w zabezpieczeniach, aby zwiększyć bezpieczeństwo stan organizacji.

![Luki w zabezpieczeniach zgłoszone przez program Identity Protection](./media/vulnerabilities/identity-protection-vulnerabilities.png)

Poniższe sekcje zawierają omówienie luk w zabezpieczeniach raportowanych przez program Identity Protection.

## <a name="multi-factor-authentication-registration-not-configured"></a>Nie skonfigurowano rejestracji uwierzytelniania wieloskładnikowego

Ta luka w zabezpieczeniach pomaga ocenić wdrożenie usługi Azure MFA Authentication w organizacji.

Uwierzytelnianie wieloskładnikowe systemu Azure zapewnia drugą warstwę zabezpieczeń do uwierzytelniania użytkowników. Pomaga w zabezpieczeniu dostępu do danych i aplikacji, a jednocześnie spełnia wymagania użytkowników dotyczące prostego procesu logowania. Uwierzytelnianie wieloskładnikowe systemu Azure umożliwia łatwe korzystanie z opcji weryfikacji, takich jak:

* Połączenie telefoniczne
* Wiadomość SMS
* Powiadomienia w aplikacji mobilnej
* Kod weryfikacyjny OTP

Zalecamy wymaganie uwierzytelniania wieloskładnikowego na platformie Azure na potrzeby logowania użytkowników. Uwierzytelnianie wieloskładnikowe odgrywa kluczową rolę w zasadach dostępu warunkowego opartego na ryzyku, które są dostępne za pośrednictwem usługi Identity Protection.

Aby uzyskać więcej informacji, zobacz [What is Azure Multi-Factor Authentication? (Co to jest usługa Azure Multi-Factor Authentication?)](../authentication/multi-factor-authentication.md)

## <a name="unmanaged-cloud-apps"></a>Niezarządzane aplikacje w chmurze

Ta luka w zabezpieczeniach ułatwia identyfikowanie niezarządzanych aplikacji w chmurze w organizacji.

Pracownicy IT często nie znają wszystkich aplikacji w chmurze w organizacji. Można łatwo sprawdzić, dlaczego Administratorzy mają wątpliwości dotyczące nieautoryzowanego dostępu do danych firmowych, możliwego wycieku danych i innych zagrożeń bezpieczeństwa.

Zalecamy wdrożenie Cloud Discovery w celu odnajdywania niezarządzanych aplikacji w chmurze oraz zarządzania tymi aplikacjami przy użyciu Azure Active Directory.

Aby uzyskać więcej informacji, zobacz [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="security-alerts-from-privileged-identity-management"></a>Alerty zabezpieczeń z usługi Privileged Identity Management

Ta luka w zabezpieczeniach ułatwia odnajdywanie i rozwiązywanie alertów dotyczących tożsamości uprzywilejowanych w organizacji.  

Aby umożliwić użytkownikom wykonywanie uprzywilejowanych operacji, organizacje muszą udzielić użytkownikom tymczasowego lub trwałego dostępu uprzywilejowanego w usłudze Azure AD, zasobach platformy Azure lub Office 365 lub innych aplikacjach SaaS. Każdy z tych uprzywilejowanych użytkowników zwiększa obszar ataków w organizacji. Ta luka w zabezpieczeniach ułatwia identyfikowanie użytkowników z niepotrzebnym dostępem uprzywilejowanym oraz podejmowanie odpowiednich działań w celu ograniczenia lub wyeliminowania ryzyka, które stanowią to.

Zalecamy organizacjom używanie Azure AD Privileged Identity Management do zarządzania, kontrolowania i monitorowania tożsamości uprzywilejowanych w usłudze Azure AD, a także innych Usługi online, takich jak Office 365 lub Microsoft Intune.

Aby uzyskać więcej informacji, zobacz [Usługa Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md).

## <a name="see-also"></a>Zobacz także

[Ochrona tożsamości w usłudze Azure Active Directory](../active-directory-identityprotection.md)
