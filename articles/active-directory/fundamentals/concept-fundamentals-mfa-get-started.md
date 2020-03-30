---
title: Uwierzytelnianie wieloskładnikowe platformy Azure dla Twojej organizacji — usługa Azure Active Directory
description: Dowiedz się więcej o dostępnych funkcjach uwierzytelniania wieloskładnikowego platformy Azure dla twojej organizacji na podstawie modelu licencji
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: febdb708c637ac322c0ca884eae627da9bd5904c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530397"
---
# <a name="overview-of-azure-multi-factor-authentication-for-your-organization"></a>Omówienie uwierzytelniania wieloskładnikowego platformy Azure dla twojej organizacji

Istnieje wiele sposobów włączania uwierzytelniania wieloskładnikowego platformy Azure dla użytkowników usługi Azure Active Directory (AD) na podstawie licencji, które jest właścicielem organizacji. 

![Badanie sygnałów i egzekwowanie pomocy makrofinansowej w razie potrzeby](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

Na podstawie naszych badań, Twoje konto jest o ponad 99,9% mniej prawdopodobne, aby zostać naruszone, jeśli używasz uwierzytelniania wieloskładnikowego (MFA).

Więc jak twoja organizacja włączyć MFA nawet za darmo, zanim stanie się statystyką?

## <a name="free-option"></a>Darmowa opcja

Klienci korzystający z bezpłatnych zalet usługi Azure AD mogą używać [ustawień domyślnych zabezpieczeń,](../fundamentals/concept-fundamentals-security-defaults.md) aby włączyć uwierzytelnianie wieloskładnikowe w swoim środowisku.

## <a name="office-365-business-premium-e3-or-e5"></a>Usługa Office 365 Business Premium, E3 lub E5

W przypadku klientów korzystających z usługi Office 365 dostępne są dwie opcje:

* Uwierzytelnianie wieloskładnikowe platformy Azure jest włączone lub wyłączone dla wszystkich użytkowników dla wszystkich zdarzeń logowania. Nie ma możliwości włączania tylko uwierzytelniania wieloskładnikowego dla podzbioru użytkowników lub tylko w niektórych scenariuszach. Zarządzanie odbywa się za pośrednictwem portalu usługi Office 365. 
* Aby uzyskać lepsze środowisko użytkownika, uaktualnij do usługi Azure AD Premium P1 lub P2 i użyj dostępu warunkowego. Aby uzyskać więcej informacji, zobacz bezpieczne zasoby usługi Office 365 z uwierzytelnianiem wieloskładnikowym.

## <a name="azure-ad-premium-p1"></a>Usługa Azure AD — warstwa Premium P1

Dla klientów z usługą Azure AD Premium P1 lub podobnymi licencjami, które zawierają tę funkcję, taką jak Enterprise Mobility + Security E3, Microsoft 365 F1 lub Microsoft 365 E3: 

Użyj [dostępu warunkowego usługi Azure AD,](../conditional-access/overview.md) aby monitować użytkowników o uwierzytelnianie wieloskładnikowe podczas niektórych scenariuszy lub zdarzeń, aby dopasować je do wymagań biznesowych.

## <a name="azure-ad-premium-p2"></a>Usługa Azure AD — warstwa Premium P2

Dla klientów z usługą Azure AD Premium P2 lub podobnymi licencjami, które zawierają tę funkcję, taką jak Enterprise Mobility + Security E5 lub Microsoft 365 E5: 

Zapewnia najsilniejszą pozycję zabezpieczeń i lepsze środowisko użytkownika. Dodaje [oparty na ryzyku dostęp warunkowy](../conditional-access/howto-conditional-access-policy-risk.md) do funkcji usługi Azure AD Premium P1, która dostosowuje się do wzorców użytkownika i minimalizuje monity o uwierzytelnianie wieloskładnikowe.

## <a name="authentication-methods"></a>Metody uwierzytelniania

|   | Domyślne ustawienia zabezpieczeń | Wszystkie inne metody |
| --- | --- | --- |
| Powiadomienie za pośrednictwem aplikacji mobilnej | X | X |
| Kod weryfikacyjny z aplikacji mobilnej lub tokenu sprzętowego |   | X |
| Wiadomość SMS na telefon |   | X |
| Zadzwoń na telefon |   | X |

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć, zapoznaj się z samouczkiem, aby [zabezpieczyć zdarzenia logowania użytkownika za pomocą uwierzytelniania wieloskładnikowego platformy Azure](../authentication/tutorial-enable-azure-mfa.md).

Aby uzyskać więcej informacji na temat licencjonowania, zobacz [Funkcje i licencje dla uwierzytelniania wieloskładnikowego platformy Azure](../authentication/concept-mfa-licensing.md).
