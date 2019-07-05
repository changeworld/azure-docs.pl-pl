---
title: Co to są usługi Azure AD zostało zarejestrowane urządzenia?
description: Dowiedz się, jak Zarządzanie tożsamościami urządzeń może ułatwić Ci zarządzanie urządzeniami, które uzyskują dostęp do zasobów w danym środowisku.
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
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462761"
---
# <a name="azure-ad-registered-devices"></a>Urządzenia zarejestrowane w usłudze Azure AD

Celem usługi Azure AD, zarejestrowanych urządzeń jest zapewnienie użytkownikom Obsługa urządzeń (PRZYNIEŚ własne) lub scenariusze dla urządzeń przenośnych. W tych scenariuszach użytkownik mógł korzystać z zasobów usługi Azure Active Directory kontrolowane organizacji za pomocą urządzeń osobistych.

|   | Zarejestrowana z usługi Azure AD |
| --- | --- |
| **Definicja** | Zarejestrowany do usługi Azure AD bez konieczności konta organizacyjnego do logowania się na urządzeniu |
| **Głównymi odbiorcami** | Ma zastosowanie do wszystkich użytkowników z następującymi kryteriami: |
|   | Przynieś własne urządzenie (BYOD) |
|   | Urządzenia przenośne |
| **Własność urządzeń** | Użytkownik lub organizacji |
| **Systemy operacyjne** | Windows 10, iOS, Android i MacOS |
| **Aprowizacja** | System Windows 10 — ustawienia |
|   | iOS/Android — aplikacja Microsoft Authenticator lub portalu firmy |
|   | MacOS — Portal firmy |
| **Opcje logowania urządzenia** | Lokalne poświadczenia użytkownika końcowego |
|   | Hasło |
|   | Windows Hello |
|   | NUMER PIN |
|   | Biometria lub wzorzec, w przypadku innych urządzeń |
| **Zarządzanie urządzeniami** | Zarządzanie urządzeniami przenośnymi (przykład: Usługi Microsoft Intune) |
|   | zarządzanie aplikacjami mobilnymi |
| **Najważniejsze funkcje** | Logowanie Jednokrotne do zasobów w chmurze |
|   | Dostęp warunkowy w przypadku zarejestrowania w usłudze Intune |
|   | Dostęp warunkowy przy użyciu zasad ochrony aplikacji |
|   | Włącza Phone Zaloguj się przy użyciu aplikacji Microsoft Authenticator |

![Urządzenia zarejestrowane w usłudze Azure AD](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Urządzenia usługi Azure AD zarejestrowany zalogowano się za pomocą konta lokalnego, takich jak konta Microsoft na urządzeniu z systemem Windows 10, ale również mieć konto usługi Azure AD, dołączone do dostępu do zasobów organizacji. Dostęp do zasobów organizacji może być dalsze ograniczone na podstawie tego konta usługi Azure AD i zasad dostępu warunkowego, które dotyczą tożsamości urządzenia.

Administratorzy mogą zabezpieczyć i jeszcze bardziej kontrolować tych urządzeń do usługi Azure AD zarejestrowane przy użyciu narzędzi zarządzania urządzeniami przenośnymi (MDM), takich jak Microsoft Intune. Zarządzania urządzeniami Przenośnymi umożliwia wymuszanie konfiguracji wymaganych w organizacji, takich jak wymagania magazynu były szyfrowane, złożoność hasła i oprogramowanie zabezpieczające przechowywane zaktualizowane. 

Rejestracja w usłudze Azure AD można osiągnąć podczas uzyskiwania dostępu do aplikacji pracy po raz pierwszy lub ręcznie przy użyciu menu systemu Windows 10. 

## <a name="scenarios"></a>Scenariusze

Użytkownik w organizacji chce dostęp do narzędzi do obsługi poczty e-mail, raportowania czasu wolnego i rejestracji korzyści z ich komputera głównego. Twoja organizacja ma tych narzędzi za zasady dostępu warunkowego, które wymagają dostępu do usługi Intune zgodne z urządzenia z systemem. Użytkownik dodaje swojego konta organizacji i rejestruje macierzystego komputera z usługą Azure AD i wymagane zasady usługi Intune są wymuszane, dając użytkownikowi dostęp do swoich zasobów.

Inny użytkownik chce dostęp do poczty e-mail organizacji na ich osobistych telefon z systemem Android, które zostały umieszczone. Twoja firma wymaga zgodnego urządzenia i utworzeniu zasad zgodności usługi Intune, aby zablokować wszystkie urządzenia z odblokowanym dostępem. Pracownik jest zablokowany dostęp do zasobów organizacji na tym urządzeniu.

## <a name="next-steps"></a>Kolejne kroki

- [Zarządzanie tożsamościami urządzeń przy użyciu witryny Azure portal](device-management-azure-portal.md)
- [Zarządzanie urządzeniami starych w usłudze Azure AD](manage-stale-devices.md)
