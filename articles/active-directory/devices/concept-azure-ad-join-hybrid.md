---
title: Co to jest hybrydowe urządzenie przyłączone do usługi Azure AD?
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
ms.openlocfilehash: 15cdaba7d63d72aab25757e7ba6f5eadc48e026a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76512253"
---
# <a name="hybrid-azure-ad-joined-devices"></a>Urządzenia dołączone hybrydowo do usługi Azure AD

Przez ponad dekadę wiele organizacji używało przyłączania do domeny lokalnej usługi Active Directory, aby umożliwić wykonywanie następujących czynności:

- Zarządzanie urządzeniami firmowymi z centralnej lokalizacji przez działy IT.
- Logowanie się przez użytkowników na urządzeniach przy użyciu kont służbowych usługi Active Directory.

Zazwyczaj organizacje z lokalnymi footprintami polegają na metodach przetwarzania obrazu w celu inicjowania obsługi administracyjnej urządzeń i często używają **programu Menedżer konfiguracji** lub zasad grupy **(GP)** do zarządzania nimi.

Jeśli środowisko ma lokalną infrastrukturę usługi AD i chcesz również wykorzystać możliwości zapewniane przez usługę Azure Active Directory, możesz wdrożyć urządzenia dołączone hybrydowo do usługi Azure AD. Te urządzenia są urządzeniami, które są przyłączone do lokalnej usługi Active Directory i zarejestrowane w usłudze Azure Active Directory.

|   | Hybrydowe sprzężenie usługi AD |
| --- | --- |
| **Definicja** | Połączone z lokalną usługą AD i usługą Azure AD wymagającą zalogowania się do urządzenia przez konto instytucji |
| **Podstawowa publiczność** | Odpowiednie dla organizacji hybrydowych z istniejącą lokalną infrastrukturą AD |
|   | Dotyczy wszystkich użytkowników w organizacji |
| **Własność urządzeń** | Organizacja |
| **Systemy operacyjne** | Windows 10, 8.1 i 7 |
|   | Windows Server 2008/R2, 2012/R2, 2016 i 2019 |
| **Inicjowanie obsługi** | Windows 10, Windows Server 2016/2019 |
|   | Dołączanie do domeny przez dział IT i automatyczne dołączanie za pośrednictwem usługi Azure AD Connect lub konfiguracji usługi ADFS |
|   | Dołączanie do domeny przez program Windows Autopilot i automatyczne dołączanie za pośrednictwem usługi Azure AD Connect lub konfiguracji usługi ADFS |
|   | Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 i Windows Server 2008 R2 — Wymagaj msi |
| **Opcje logowania urządzenia** | Konta organizacyjne używające: |
|   | Hasło |
|   | Windows Hello dla firm dla wersji Win10 |
| **Zarządzanie urządzeniami** | Zasady grupy |
|   | Samodzielne zarządzanie programem Configuration Manager lub współzarządzanie za pomocą usługi Microsoft Intune |
| **Najważniejsze możliwości** | Jednokrotne zasyscie samou i jednokrotne zasobów zarówno w chmurze, jak i w środowisku lokalnym |
|   | Dostęp warunkowy za pośrednictwem sprzężenia domeny lub usługi Intune, jeśli jest współzarządzany |
|   | Samoobsługowe resetowanie hasła i resetowanie numeru PIN systemu Windows Hello na ekranie blokady |
|   | Roaming w stanie przedsiębiorstwa na różnych urządzeniach |

![Urządzenia dołączone hybrydowo do usługi Azure AD](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>Scenariusze

Użyj urządzeń sprzężonych hybrydowych usługi Azure AD, jeśli:

- Masz aplikacje Win32 wdrożone na tych urządzeniach, które polegają na uwierzytelnianiu maszynowym usługi Active Directory.
- Chcesz nadal używać zasad grupy do zarządzania konfiguracją urządzenia.
- Chcesz nadal używać istniejących rozwiązań do przetwarzania obrazu do wdrażania i konfigurowania urządzeń.
- Oprócz systemu Windows 10 musisz obsługiwać urządzenia z systemem Windows 7 i 8.1 w dół

## <a name="next-steps"></a>Następne kroki

- [Planowanie implementacji dołączania do hybrydowej usługi Azure AD](hybrid-azuread-join-plan.md)
- [Zarządzanie tożsamościami urządzeń za pomocą witryny Azure portal](device-management-azure-portal.md)
- [Zarządzanie nieaktywnymi urządzeniami w usłudze Azure AD](manage-stale-devices.md)
