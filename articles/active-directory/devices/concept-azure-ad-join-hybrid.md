---
title: Co to jest hybrydowa usługa Azure AD przyłączone urządzenie?
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
ms.openlocfilehash: 5c57180ba10322cb790c05b3f8f48043ca08b545
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462748"
---
# <a name="hybrid-azure-ad-joined-devices"></a>Urządzenia dołączone hybrydowo do usługi Azure AD

Przez ponad dekadę wiele organizacji używało przyłączania do domeny lokalnej usługi Active Directory, aby umożliwić wykonywanie następujących czynności:

- Zarządzanie urządzeniami firmowymi z centralnej lokalizacji przez działy IT.
- Logowanie się przez użytkowników na urządzeniach przy użyciu kont służbowych usługi Active Directory.

Zazwyczaj organizacje z dużą ilością zasobów lokalnych polegają na metodach obrazowania w przypadku aprowizacji urządzeń, a także często używają programu **System Center Configuration Manager (SCCM)** lub **zasad grupy** do zarządzania nimi.

Jeśli środowisko ma lokalną infrastrukturę usługi AD i chcesz również wykorzystać możliwości zapewniane przez usługę Azure Active Directory, możesz wdrożyć urządzenia dołączone hybrydowo do usługi Azure AD. Urządzenia te to urządzenia, które są przyłączone do lokalnej usługi Active Directory i zarejestrowane w usłudze Azure Active Directory.

|   | Dołączenie do hybrydowej usługi Azure AD |
| --- | --- |
| **Definicja** | Przyłączone do środowiska lokalnego usługi AD i usługi Azure AD wymaga konta organizacyjnego, zaloguj się do urządzenia |
| **Głównymi odbiorcami** | Odpowiednia dla organizacji hybrydowych przy użyciu istniejącej infrastruktury usługi Active Directory lokalnych |
|   | Ma zastosowanie do wszystkich użytkowników w organizacji |
| **Własność urządzeń** | Organizacja |
| **Systemy operacyjne** | Windows 10, 8.1 i 7 |
|   | Windows Server 2008 R2, 2012/R2 2016 i 2019 r |
| **Aprowizacja** | System Windows 10, Windows Server 2016/2019 |
|   | Przyłączanie do domeny przez IT i autosprzęganie za pośrednictwem usługi Azure AD Connect lub usług AD FS konfiguracji |
|   | Przyłączanie do domeny przez rozwiązania Windows Autopilot i autosprzęganie za pomocą konfiguracji usługi Azure AD Connect lub usług AD FS |
|   | Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 i Windows Server 2008 R2 — wymagają pliku MSI |
| **Opcje logowania urządzenia** | Przy użyciu konta organizacji: |
|   | Hasło |
|   | Funkcja Windows Hello dla firm dla Windows 10 |
| **Zarządzanie urządzeniami** | Zasady grupy |
|   | Autonomiczny program Configuration Manager w programie System Center lub współzarządzania w usłudze Microsoft Intune |
| **Najważniejsze funkcje** | Logowanie Jednokrotne do zasobów w chmurze i lokalnych |
|   | Warunkowy dostęp za pośrednictwem przyłączania do domeny lub usługi Intune, jeśli zarządzane |
|   | Samoobsługowe Resetowanie hasła i numeru PIN Windows Hello zresetować na ekranie blokady |
|   | Roaming stanu dla przedsiębiorstw na urządzeniach |

![Urządzenia dołączone hybrydowo do usługi Azure AD](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>Scenariusze

Użyj usługi Azure AD urządzenia przyłączone do hybrydowej jeśli:

- Masz aplikacje Win32 wdrożone na tych urządzeniach, które polegają na uwierzytelnianiu maszynowym usługi Active Directory.
- Chcesz zarządzać konfiguracją urządzenia przy użyciu zasad grupy w dalszym ciągu.
- Chcesz nadal używać istniejących rozwiązań do przetwarzania obrazów, wdrażania i konfigurowania urządzenia.
- Musi obsługiwać niższego poziomu Windows 7 i urządzenia w wersji 8.1 oprócz systemu Windows 10

## <a name="next-steps"></a>Kolejne kroki

- [Planowanie implementacji przyłączania do hybrydowej usługi Azure AD](hybrid-azuread-join-plan.md)
- [Zarządzanie tożsamościami urządzeń przy użyciu witryny Azure portal](device-management-azure-portal.md)
- [Zarządzanie urządzeniami starych w usłudze Azure AD](manage-stale-devices.md)
