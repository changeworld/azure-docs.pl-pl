---
title: Luk w zabezpieczeniach wykrywanych przez usługę Azure Active Directory Identity Protection | Dokumentacja firmy Microsoft
description: Przegląd luk w zabezpieczeniach wykryta przez usługę Azure Active Directory Identity Protection.
services: active-directory
keywords: ochronę tożsamości usługi Azure active directory, usługa cloud app discovery, zarządzanie aplikacjami, zabezpieczeń, ryzyka, poziom ryzyka, luki w zabezpieczeniach, zasady zabezpieczeń
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 92233a5b-cb34-4d28-88cc-d5d29c0f3256
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 0cb37a29847e69dae4a0e0682591bae092b38360
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Luk w zabezpieczeniach wykrywanych przez usługę Azure Active Directory Identity Protection
Luki w zabezpieczeniach występują słabe strony w danym środowisku, które może być wykorzystane przez atakującego. Zaleca się rozwiązać te luki w zabezpieczeniach aby poprawić stan zabezpieczeń organizacji i uniemożliwić osobom atakującym ich wykorzystania.


![luki w zabezpieczeniach](./media/active-directory-identityprotection-vulnerabilities/101.png "luk w zabezpieczeniach")



Poniższe sekcje zawierają przegląd luk w zabezpieczeniach zgłoszone przez ochronę tożsamości.

## <a name="multi-factor-authentication-registration-not-configured"></a>Rejestracja korzystająca z uwierzytelniania wieloskładnikowego nie jest skonfigurowana
Ta luka w zabezpieczeniach ułatwia kontrolowanie wdrażania usługi Azure Multi-Factor Authentication w organizacji. 

Usługa Azure Multi-Factor authentication udostępnia drugą warstwę zabezpieczeń w celu uwierzytelnienia użytkownika. Go pomaga w zabezpieczaniu dostępu do danych i aplikacji spełniając zapotrzebowanie na prosty proces logowania. Zapewnia silne uwierzytelnianie za pomocą różnych opcji weryfikacji łatwe — połączenie telefoniczne, wiadomość tekstowa lub aplikacji mobilnej weryfikacji lub powiadamiania o kod i 3rd strona tokenów OATH.

Firma Microsoft zaleca wymusić uwierzytelnianie wieloskładnikowe Azure logowania użytkownika. Uwierzytelnianie wieloskładnikowe odgrywa kluczową rolę w zasadach dostępu warunkowego opartego na ryzyko dostępne za pośrednictwem Identity Protection.

Aby uzyskać więcej informacji, zobacz [co to jest uwierzytelnianie wieloskładnikowe Azure?](authentication/multi-factor-authentication.md)

## <a name="unmanaged-cloud-apps"></a>Niezarządzane aplikacje w chmurze
Ta luka w zabezpieczeniach pomaga zidentyfikować aplikacje w chmurze niezarządzane w Twojej organizacji.

W przedsiębiorstwach nowoczesnego działu IT są często zna wszystkie aplikacje chmury, które użytkownicy w organizacji korzystają z ich w pracy. To proste zobaczyć, dlaczego Administratorzy byłyby problemów dotyczących nieautoryzowanego dostępu do danych firmowych, wycieku danych i inne zagrożenia dla bezpieczeństwa. 

Firma Microsoft zaleca wdrożenia Cloud App Discovery do wykrywania aplikacji w chmurze niezarządzane oraz do zarządzania te aplikacje przy użyciu usługi Azure Active Directory w organizacji.

Aby uzyskać więcej informacji, zobacz [znajdowania aplikacji w chmurze niezarządzane z usługi Cloud App Discovery](manage-apps/cloud-app-discovery.md).

## <a name="security-alerts-from-privileged-identity-management"></a>Alerty zabezpieczeń z usługi Privileged Identity Management
Ta luka w zabezpieczeniach pomaga wykrywać oraz rozwiązywanie alertów dotyczących tożsamości uprzywilejowanych w Twojej organizacji.  

Aby umożliwić użytkownikom do wykonywania uprzywilejowanych operacji, organizacja musi udzielić użytkownikom tymczasowych lub trwałych uprzywilejowanego dostępu w usłudze Azure AD, Azure lub usługi Office 365 zasobów lub innych aplikacji SaaS. Każdy z tych użytkownicy o odpowiednich uprawnieniach zwiększa obszar narażony Twojej organizacji. Ta luka w zabezpieczeniach pomaga zidentyfikować użytkowników z niepotrzebnych uprzywilejowanego dostępu i podjąć odpowiednie działania w celu ograniczenie lub wyeliminowanie ryzyka, które stanowią one. 

Zaleca się Twoja organizacja korzysta z usługi Azure AD Privileged Identity Management w celu zarządzania kontrolą, i monitor uprzywilejowany tożsamości i dostępu do zasobów w usłudze Azure AD, a także innych usług online firmy Microsoft, takich jak usługi Office 365 lub Microsoft Intune.

Aby uzyskać więcej informacji, zobacz [Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md). 

## <a name="see-also"></a>Zobacz także
* [Ochronę tożsamości usługi Azure Active Directory](active-directory-identityprotection.md)

