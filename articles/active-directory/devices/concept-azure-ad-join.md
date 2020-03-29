---
title: Co to jest urządzenie przyłączone do usługi Azure AD?
description: Dowiedz się, jak zarządzanie tożsamościami urządzeń może pomóc w zarządzaniu urządzeniami uzyskującymi dostęp do zasobów w twoim środowisku.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40f89fbe19b93601f9e0525f0387e402bd175fe4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672678"
---
# <a name="azure-ad-joined-devices"></a>Urządzenia dołączone do usługi Azure AD

Sprzężenie usługi Azure AD jest przeznaczone dla organizacji, które chcą być oparte na chmurze lub tylko w chmurze. Każda organizacja może wdrażać urządzenia przyłączone do usługi Azure AD bez względu na rozmiar lub branżę. Dołączanie usługi Azure AD działa nawet w środowisku hybrydowym, umożliwiając dostęp zarówno do aplikacji i zasobów w chmurze, jak i lokalnych.

|   | Sprzężenia Azure AD |
| --- | --- |
| **Definicja** | Przyłączone tylko do usługi Azure AD wymagające konta instytucji do logowania się na urządzeniu |
| **Podstawowa publiczność** | Nadaje się zarówno do organizacji chmurowych, jak i hybrydowych. |
|   | Dotyczy wszystkich użytkowników w organizacji |
| **Własność urządzeń** | Organizacja |
| **Systemy operacyjne** | Wszystkie urządzenia z systemem Windows 10 |
| **Inicjowanie obsługi** | Samoobsługowe: Windows OOBE lub Ustawienia |
|   | Rejestrowanie zbiorcze |
|   | Windows Autopilot |
| **Opcje logowania urządzenia** | Konta organizacyjne używające: |
|   | Hasło |
|   | Windows Hello dla firm |
|   | Klucze zabezpieczeń FIDO2.0 (wersja zapoznawcza) |
| **Zarządzanie urządzeniami** | Zarządzanie urządzeniami przenośnymi (przykład: Usługa Microsoft Intune) |
|   | Współzarządzanie finansami za pomocą usługi Microsoft Intune i programu Microsoft Endpoint Configuration Manager |
| **Najważniejsze możliwości** | Jednokrotne zasyscie samou i jednokrotne zasobów zarówno w chmurze, jak i w środowisku lokalnym |
|   | Dostęp warunkowy za pośrednictwem rejestracji MDM i oceny zgodności mdm |
|   | Samoobsługowe resetowanie hasła i resetowanie numeru PIN systemu Windows Hello na ekranie blokady |
|   | Roaming w stanie przedsiębiorstwa na różnych urządzeniach |

Urządzenia przyłączone do usługi Azure AD są zalogowane do korzystania z konta usługi Azure AD. Dostęp do zasobów w organizacji może być dodatkowo ograniczony na podstawie tego konta usługi Azure AD i [zasad dostępu warunkowego](../conditional-access/overview.md) stosowanych do tożsamości urządzenia.

Administratorzy mogą zabezpieczać i dalej kontrolować urządzenia przyłączone do usługi Azure AD za pomocą narzędzi zarządzania urządzeniami przenośnymi (MDM), takich jak microsoft intune lub w scenariuszach współzarządzania przy użyciu programu Microsoft Endpoint Configuration Manager. Narzędzia te umożliwiają wymuszanie konfiguracji wymaganych przez organizację, takich jak wymaganie szyfrowania magazynu, złożoność haseł, instalacje oprogramowania i aktualizacje oprogramowania. Administratorzy mogą udostępniać aplikacje organizacji urządzeniom usługi Azure AD przy użyciu programu Menedżer konfiguracji do [zarządzania aplikacjami ze sklepu Microsoft Store dla firm i instytucji edukacyjnych](/configmgr/apps/deploy-use/manage-apps-from-the-windows-store-for-business).

Sprzężenie usługi Azure AD można wykonać za pomocą opcji samoobsługowych, takich jak środowisko po wyjęciu z pudełka (OOBE), rejestracja zbiorcza lub [autopilot systemu Windows.](/intune/enrollment-autopilot)

Urządzenia przyłączone do usługi Azure AD mogą nadal utrzymywać dostęp do logowania jednokrotnego do zasobów lokalnych, gdy znajdują się w sieci organizacji. Urządzenia, które są przyłączone do usługi Azure AD, mogą nadal uwierzytelniać się na serwerach lokalnych, takich jak pliki, drukowanie i inne aplikacje.

## <a name="scenarios"></a>Scenariusze

Chociaż dołączanie do usługi Azure AD jest przeznaczone głównie dla organizacji, które nie mają lokalnej infrastruktury usługi Active Directory systemu Windows Server, możesz bez obaw użyć tej funkcji w następujących scenariuszach:

- Chcesz przenieść zasoby do infrastruktury opartej na chmurze przy użyciu usługi Azure AD oraz funkcji zarządzania urządzeniami przenośnymi, np. usługi Intune.
- Nie możesz zastosować dołączania do domeny lokalnej, np. jeśli potrzebujesz kontrolować urządzenia przenośne, takie jak tablety i telefony.
- Użytkownicy przede wszystkim potrzebują dostępu do usługi Office 365 lub innych aplikacji SaaS zintegrowanych z usługą Azure AD.
- Chcesz zarządzać grupą użytkowników w usłudze Azure AD zamiast w usłudze Active Directory. Ten scenariusz może dotyczyć na przykład pracowników sezonowych, wykonawców lub studentów.
- Chcesz zapewnić możliwość dołączania pracownikom w odległych oddziałach z ograniczoną infrastrukturą lokalną.

Funkcję urządzeń dołączonych do usługi Azure AD możesz skonfigurować w przypadku urządzeń z systemem Windows 10.

Celem urządzeń dołączonych do usługi Azure AD jest uproszczenie następujących kwestii:

- Wdrożenia urządzeń należących do firmy w systemie Windows
- Uzyskiwanie dostępu do aplikacji i zasobów organizacji z dowolnego urządzenia z systemem Windows
- Oparte na chmurze zarządzanie urządzeniami należącymi do firmy
- Użytkownicy, aby zalogować się do swoich urządzeń za pomocą usługi Azure AD lub zsynchronizowane usługi Active Directory pracy lub kont szkolnych.

![Urządzenia dołączone do usługi Azure AD](./media/concept-azure-ad-join/azure-ad-joined-device.png)

Dołączenie do usługi Azure AD można wdrożyć przy użyciu dowolnej z następujących metod:

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot)
- [Wdrożenie zbiorcze](/intune/windows-bulk-enroll)
- [Środowisko samoobsługowe](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>Następne kroki

- [Planowanie implementacji dołączania do usługi Azure AD](azureadjoin-plan.md)
- [Jak zarządzać grupą administratorów lokalnych na urządzeniach przyłączonych do usługi Azure AD](assign-local-admin.md)
- [Zarządzanie tożsamościami urządzeń za pomocą witryny Azure portal](device-management-azure-portal.md)
- [Zarządzanie nieaktywnymi urządzeniami w usłudze Azure AD](manage-stale-devices.md)
