---
title: Co to jest usługa Azure AD przyłączone urządzenie?
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
ms.openlocfilehash: 4af3aea7218ea8792bb66188e8df7baf9f460b0b
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462813"
---
# <a name="azure-ad-joined-devices"></a>Urządzenia dołączone do usługi Azure AD

Funkcja Azure AD join jest przeznaczona dla organizacji, które mają być chmurę lub tylko w chmurze. Każda organizacja może wdrożyć urządzeń przyłączonych do usługi Azure AD, niezależnie od tego, czy rozmiar lub branżowych. Przyłączanie do usługi Azure AD działa, nawet w środowisku hybrydowym, umożliwiając dostęp do aplikacji w chmurze i lokalnych i zasobów.

|   | Azure AD Join |
| --- | --- |
| **Definicja** | Dołączony tylko do usługi Azure AD wymaga konta organizacyjnego do logowania się na urządzeniu |
| **Głównymi odbiorcami** | Odpowiednie dla obu tylko w chmurze i hybrydowych organizacji. |
|   | Ma zastosowanie do wszystkich użytkowników w organizacji |
| **Własność urządzeń** | Organizacja |
| **Systemy operacyjne** | Wszystkie urządzenia z systemem Windows 10 |
| **Aprowizacja** | Samoobsługa: Windows OOBE lub ustawienia |
|   | Rejestrowanie zbiorcze |
|   | Rozwiązania Windows Autopilot |
| **Opcje logowania urządzenia** | Przy użyciu konta organizacji: |
|   | Hasło |
|   | Windows Hello dla firm |
|   | FIDO2.0 kluczy zabezpieczeń (wersja zapoznawcza) |
| **Zarządzanie urządzeniami** | Zarządzanie urządzeniami przenośnymi (przykład: Usługi Microsoft Intune) |
|   | Współzarządzanie za pomocą usługi Microsoft Intune i programu System Center Configuration Manager |
| **Najważniejsze funkcje** | Logowanie Jednokrotne do zasobów w chmurze i lokalnych |
|   | Dostęp warunkowy przy użyciu rejestracji w rozwiązaniu MDM i ocena zgodności oprogramowania MDM |
|   | Samoobsługowe Resetowanie hasła i numeru PIN Windows Hello zresetować na ekranie blokady |
|   | Roaming stanu dla przedsiębiorstw na urządzeniach |

Urządzenia usługi Azure AD, przyłączone do zalogowano się do korzystania z organizacji konta usługi Azure AD. Dostęp do zasobów w organizacji można jeszcze bardziej ograniczone oparte na tym koncie usługi Azure AD i [zasady dostępu warunkowego](../conditional-access/overview.md) stosowane do tożsamości urządzenia.

Administratorzy mogą zabezpieczyć i dalsze kontroli usługi Azure AD przyłączone do urządzenia za pomocą narzędzi zarządzania urządzeniami przenośnymi (MDM), takie jak Microsoft Intune lub w scenariuszach współzarządzania przy użyciu programu System Center Configuration Manager. Te narzędzia umożliwiają wymuszenie konfiguracje wymagane w organizacji, takich jak wymagania magazynu były szyfrowane, złożoność hasła, instalacje oprogramowania i aktualizacji oprogramowania. Administratorzy mogą udostępnić aplikacji w organizacji na urządzeniach przyłączonych do usługi Azure AD przy użyciu [System Center Configuration Manager i Microsoft Store dla firm](https://docs.microsoft.com/sccm/apps/deploy-use/manage-apps-from-the-windows-store-for-business).

Przyłączanie do usługi Azure AD można osiągnąć za pomocą opcji Samoobsługowe jak poza pole środowiska (OOBE), rejestracji zbiorczej lub [rozwiązania Windows Autopilot](https://docs.microsoft.com/intune/enrollment-autopilot).

Urządzenia usługi Azure AD, przyłączone nadal mogą zachować dostęp pojedynczego logowania jednokrotnego do zasobów lokalnych, znajdujących się w sieci organizacji. Urządzenia, które są dołączone do usługi Azure AD, nadal może uwierzytelnić się na serwerach lokalnych, takich jak plik, drukowania i innych aplikacji.

## <a name="scenarios"></a>Scenariusze

Chociaż dołączanie do usługi Azure AD jest przeznaczone głównie dla organizacji, które nie mają lokalnej infrastruktury usługi Active Directory systemu Windows Server, możesz bez obaw użyć tej funkcji w następujących scenariuszach:

- Chcesz przenieść zasoby do infrastruktury opartej na chmurze przy użyciu usługi Azure AD oraz funkcji zarządzania urządzeniami przenośnymi, np. usługi Intune.
- Nie możesz zastosować dołączania do domeny lokalnej, np. jeśli potrzebujesz kontrolować urządzenia przenośne, takie jak tablety i telefony.
- Użytkownicy przede wszystkim potrzebują dostępu do usługi Office 365 lub innych aplikacji SaaS zintegrowanych z usługą Azure AD.
- Chcesz zarządzać grupą użytkowników w usłudze Azure AD zamiast w usłudze Active Directory. W tym scenariuszu można zastosować, na przykład, w odniesieniu do pracownikom sezonowym, Zleceniobiorcami ani studentów.
- Chcesz zapewnić możliwość dołączania pracownikom w odległych oddziałach z ograniczoną infrastrukturą lokalną.

Funkcję urządzeń dołączonych do usługi Azure AD możesz skonfigurować w przypadku urządzeń z systemem Windows 10.

Celem urządzeń dołączonych do usługi Azure AD jest uproszczenie następujących kwestii:

- Wdrożenia urządzeń należących do firmy w systemie Windows
- Uzyskiwanie dostępu do aplikacji i zasobów organizacji z dowolnego urządzenia z systemem Windows
- Oparte na chmurze zarządzanie urządzeniami należącymi do firmy
- Użytkownikom na logowanie się do ich urządzeń przy użyciu ich w usłudze Azure AD lub zsynchronizowanych usługi Active Directory kont służbowych.

![Urządzenia dołączone do usługi Azure AD](./media/concept-azure-ad-join/azure-ad-joined-device.png)

Dołączenie do usługi Azure AD można wdrożyć przy użyciu dowolnej z następujących metod:

- [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot)
- [Wdrożenie zbiorcze](https://docs.microsoft.com/intune/windows-bulk-enroll)
- [Środowisko samoobsługowe](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>Kolejne kroki

- [Planowanie wdrożenia usługi Azure AD join](azureadjoin-plan.md)
- [Urządzenia przyłączone do sposobu zarządzania do lokalnej grupy administratorów w usłudze Azure AD](assign-local-admin.md)
- [Zarządzanie tożsamościami urządzeń przy użyciu witryny Azure portal](device-management-azure-portal.md)
- [Zarządzanie urządzeniami starych w usłudze Azure AD](manage-stale-devices.md)
