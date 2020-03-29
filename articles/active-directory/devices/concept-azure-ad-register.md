---
title: Co to są urządzenia zarejestrowane w usłudze Azure AD?
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
ms.openlocfilehash: 7e2a8cad7cd4410a95a6ebd60ada22de456737bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67462761"
---
# <a name="azure-ad-registered-devices"></a>Urządzenia zarejestrowane w usłudze Azure AD

Celem urządzeń zarejestrowanych w usłudze Azure AD jest zapewnienie użytkownikom obsługi scenariuszy bring your own device (BYOD) lub urządzeń przenośnych. W tych scenariuszach użytkownik może uzyskać dostęp do zasobów kontrolowanych przez usługę Azure Active Directory w organizacji przy użyciu urządzenia osobistego.

|   | Zarejestrowana usługa Azure AD |
| --- | --- |
| **Definicja** | Zarejestrowane w usłudze Azure AD bez konieczności logowania się do urządzenia bez konieczności logowania się do urządzenia |
| **Podstawowa publiczność** | Dotyczy wszystkich użytkowników z następującymi kryteriami: |
|   | „Przynieś własne urządzenie” (BYOD) |
|   | Urządzenia przenośne |
| **Własność urządzeń** | Użytkownik lub organizacja |
| **Systemy operacyjne** | Windows 10, iOS, Android i MacOS |
| **Inicjowanie obsługi** | Windows 10 – Ustawienia |
|   | iOS/Android — portal firmy lub aplikacja Microsoft Authenticator |
|   | MacOS – Portal firmy |
| **Opcje logowania urządzenia** | Poświadczenia lokalne użytkownika końcowego |
|   | Hasło |
|   | Windows Hello |
|   | Kod PIN |
|   | Dane biometryczne lub wzór dla innych urządzeń |
| **Zarządzanie urządzeniami** | Zarządzanie urządzeniami przenośnymi (przykład: Usługa Microsoft Intune) |
|   | zarządzanie aplikacjami mobilnymi |
| **Najważniejsze możliwości** | SSO do zasobów w chmurze |
|   | Dostęp warunkowy po zarejestrowaniu się w usłudze Intune |
|   | Dostęp warunkowy za pośrednictwem zasad ochrony aplikacji |
|   | Włącza logowanie telefonu za pomocą aplikacji Microsoft Authenticator |

![Urządzenia zarejestrowane w usłudze Azure AD](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Urządzenia zarejestrowane w usłudze Azure AD są zalogowane do korzystania z konta lokalnego, takiego jak konto Microsoft na urządzeniu z systemem Windows 10, ale dodatkowo mają konto usługi Azure AD dołączone do dostępu do zasobów organizacji. Dostęp do zasobów w organizacji może być dodatkowo ograniczony na podstawie tego konta usługi Azure AD i zasad dostępu warunkowego stosowanych do tożsamości urządzenia.

Administratorzy mogą zabezpieczyć i dalej kontrolować te urządzenia zarejestrowane w usłudze Azure AD za pomocą narzędzi do zarządzania urządzeniami przenośnymi (MDM), takich jak microsoft intune. MdM zapewnia środki do wymuszania konfiguracji wymaganych przez organizację, takich jak wymaganie szyfrowania magazynu, złożoność hasła i oprogramowanie zabezpieczające aktualizowane. 

Rejestracji usługi Azure AD można wykonać podczas uzyskiwania dostępu do aplikacji służbowej po raz pierwszy lub ręcznie przy użyciu menu Ustawienia systemu Windows 10. 

## <a name="scenarios"></a>Scenariusze

Użytkownik w organizacji chce uzyskać dostęp do narzędzi do obsługi poczty e-mail, raportowania czasu wolnego i korzysta z rejestracji na komputerze domowym. Organizacja ma te narzędzia za zasadami dostępu warunkowego, które wymagają dostępu z urządzenia zgodnego z usługi Intune. Użytkownik dodaje swoje konto organizacji i rejestruje swój komputer domowy za pomocą usługi Azure AD, a wymagane zasady usługi Intune są wymuszane, dając użytkownikowi dostęp do ich zasobów.

Inny użytkownik chce uzyskać dostęp do swojej organizacyjnej poczty e-mail na swoim osobistym telefonie z Androidem, który został zakorzeniony. Firma wymaga zgodnego urządzenia i utworzyła zasady zgodności usługi Intune, aby zablokować wszystkie zakorzenione urządzenia. Pracownik nie może uzyskiwać dostępu do zasobów organizacji na tym urządzeniu.

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie tożsamościami urządzeń za pomocą witryny Azure portal](device-management-azure-portal.md)
- [Zarządzanie nieaktywnymi urządzeniami w usłudze Azure AD](manage-stale-devices.md)
