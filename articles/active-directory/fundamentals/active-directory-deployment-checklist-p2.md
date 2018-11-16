---
title: Usługa Azure AD Lista kontrolna wdrażania 30 dni, w ciągu 90 dni, a nie tylko
description: Usługa Azure Active Directory Premium P2 funkcji listy kontrolnej dotyczącej wdrożenia
services: active-directory
ms.service: active-directory
ms.component: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: ''
ms.openlocfilehash: 86561cd835a36282ca1b38638ab4372c6b360617
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2018
ms.locfileid: "51705655"
---
# <a name="azure-active-directory-premium-p2-licensing-feature-checklist"></a>Usługa Azure Active Directory Premium P2 licencjonowania lista funkcji

Może wydawać się trudne do wdrożenia usługi Azure Active Directory (Azure AD) w Twojej organizacji i przechowuj w bezpiecznym miejscu. W tym artykule identyfikuje niektóre typowe zadania, które przydatne klientów. Klientów zwykle wykonać te zadania w ciągu 30 dni, 90 dni lub znajduje się poza, aby zwiększyć ich poziom zabezpieczeń. Nawet organizacji, którzy już wdrożyli usługę Azure AD umożliwia ta lista kontrolna: Upewnij się, że przygotowujesz się one w pełni wykorzystać ich inwestycję.

Infrastruktura tożsamości dobrze zaplanowane i są wykonywane drogę do bardziej bezpiecznego dostępu wydajność obciążeń i danych tylko przez funkcje uwierzytelniania użytkowników i urządzeń.

## <a name="prerequisites"></a>Wymagania wstępne

W tym przewodniku założono, że masz licencje usługi Azure AD Premium P2, pakietu Enterprise Mobility + Security E5, Microsoft 365 E5 lub podobnego pakietu licencji.

[Licencjonowanie usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)

[Microsoft 365 Enterprise](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)

[Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)

## <a name="plan-and-deploy-day-1-30"></a>Planowanie i wdrażanie: 1 – 30 dni

- Wyznaczenie więcej niż jednego administratora globalnego (break szkła konta)
   - [Zarządzanie kontami administracyjnymi z dostępem awaryjnego w usłudze Azure AD](../users-groups-roles/directory-emergency-access.md)
- Włączanie usługi Azure AD Privileged Identity Management (PIM), aby wyświetlić raporty
   - [Rozpoczynanie korzystania z usługi PIM](../privileged-identity-management/pim-getting-started.md)
- Jeśli jest to możliwe, należy użyć nieglobalnych ról administracyjnych.
   - [Przypisywanie ról administratorów w usłudze Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)
- Authentication
   - [Wdrażanie funkcji samoobsługowego resetowania haseł](../authentication/howto-sspr-deployment.md)
   - Wdrażanie ochrony haseł usługi Azure AD (wersja zapoznawcza)
      - [Eliminowanie nieprawidłowych haseł w organizacji](../authentication/concept-password-ban-bad.md)
      - [Wymuszanie ochrona za pomocą hasła usługi Azure AD dla usługi Active Directory systemu Windows Server](../authentication/concept-password-ban-bad-on-premises.md)
   - Skonfiguruj zasady blokady konta
      - [Usługa Azure Active Directory, inteligentnej blokady](../authentication/howto-password-smart-lockout.md)
      - [Usług AD FS blokady ekstranetu i blokowanie ekstranetu w usługach inteligentne](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)
   - [Wdrażanie usługi Azure AD Multi-Factor Authentication za pomocą zasad dostępu warunkowego](../authentication/howto-mfa-getstarted.md)
   - [Włącz konwergentnej rejestracji samoobsługowego resetowania haseł i uwierzytelniania wieloskładnikowego w usłudze Azure AD (wersja zapoznawcza)](../authentication/concept-registration-mfa-sspr-converged.md)
   - [Włączanie usługi Azure Active Directory Identity Protection](../identity-protection/enable.md)
      - [Korzystanie ze zdarzeń o podwyższonym ryzyku wyzwalacza usługi Multi-Factor Authentication i zmiany hasła](../authentication/tutorial-risk-based-sspr-mfa.md)
   - [Wskazówki dotyczące haseł](https://www.microsoft.com/research/publication/password-guidance/)
      - Obsługa znaku ośmiu minimalnej długości wynoszącej, już nie jest zawsze lepiej.
      - Usuń znak kompozycji wymagania.
      - [Eliminowanie resetowania obowiązkowe okresowe hasła dla kont użytkowników.](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire)
- Synchronizowanie użytkowników z usługi Active Directory w środowisku lokalnym
   - [Instalowanie programu Azure AD Connect](../connect/active-directory-aadconnect-select-installation.md)
   - [Implementowanie synchronizacji skrótów haseł](../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)
   - [Implementowanie funkcji zapisywania zwrotnego haseł](../authentication/howto-sspr-writeback.md)
   - [Implementowanie usługi Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md)
- [Przypisywanie licencji do użytkowników, członkostwa w grupach w usłudze Azure Active Directory](../users-groups-roles/licensing-groups-assign.md)

## <a name="plan-and-deploy-day-31-90"></a>Planowanie i wdrażanie: 31-90 dni

- [Planowanie dostępu użytkowników-gości](../b2b/what-is-b2b.md)
   - [Dodają użytkowników we współpracy B2B usługi Azure Active Directory w witrynie Azure portal](../b2b/add-users-administrator.md)
   - [Zezwalaj na zaproszenia lub blokowanie ich dla użytkowników B2B z określonym organizacjom](../b2b/allow-deny-list.md)
   - [Użytkowników B2B przydział w usłudze Azure AD dostęp do aplikacji w środowisku lokalnym](../b2b/hybrid-cloud-to-on-premises.md)
- Decyzje dotyczące strategii zarządzania cyklem życia użytkowników
- [Podejmij decyzję strategii zarządzania urządzeniami](../devices/overview.md)
   - [Scenariusze użycia i zagadnienia dotyczące wdrażania usługi Azure AD JOIN](../devices/azureadjoin-plan.md)
- [Zarządzaj Windows Hello dla firm w organizacji](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization)

## <a name="plan-and-deploy-day-90-and-beyond"></a>Planowanie i wdrażanie: 90 dni i nie tylko

- [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md)
   - [Konfigurowanie ustawień roli w katalogu usługi Azure AD w usłudze PIM](../privileged-identity-management/pim-how-to-change-default-settings.md)
   - [Przypisywanie ról katalogu usługi Azure AD w usłudze PIM](../privileged-identity-management/pim-how-to-add-role-to-user.md)
- [Kończenie przeglądu dostępu dla ról katalogu usługi Azure AD w usłudze PIM](../privileged-identity-management/pim-how-to-start-security-review.md)
- Całościowe zarządzanie cyklem życia użytkowników
   - Usługa Azure AD ma podejście do zarządzania cyklem życia tożsamości
   - Usuń wymagane ręczne wykonanie czynności z cyklu życia dla konta pracowników, aby uniemożliwić nieautoryzowany dostęp:
      - Synchronizowanie tożsamości ze źródłem prawdziwych danych (w systemie HR) do usługi Azure AD. Link do obsługiwane systemy Kadrowe)
      - [Aby automatycznie przypisać użytkowników do grup na podstawie ich atrybutów z działu KADR (lub źródło prawdziwych danych), takich jak dział, tytuł, region i inne atrybuty, należy użyć grup dynamicznych.](../users-groups-roles/groups-dynamic-membership.md)
      - [Użyj dostępu oparte na grupach zarządzania aprowizacji automatycznie aprowizować użytkowników do aplikacji SaaS.](../manage-apps/what-is-access-management.md)

## <a name="next-steps"></a>Kolejne kroki

[Konfiguracje dostępu tożsamości i urządzenia](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

[Typowe zalecane zasady dostępu do tożsamości i urządzenia](https://docs.microsoft.com/microsoft-365/enterprise/identity-access-policies)
