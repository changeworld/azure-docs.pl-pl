---
title: Tożsamość urządzeń i wirtualizacja pulpitu — usługa Azure Active Directory
description: Dowiedz się, jak tożsamości urządzeń usługi VDI i usługi Azure AD mogą być używane razem
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b431cee3b8e5fc168dec2766442d6f6b9869d1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900368"
---
# <a name="device-identity-and-desktop-virtualization"></a>Tożsamość urządzenia i wirtualizacja pulpitu

Administratorzy często wdrażają platformy infrastruktury pulpitu wirtualnego (VDI) obsługujące systemy operacyjne Windows w swoich organizacjach. Administratorzy wdrażają vdi w:

- Usprawnij zarządzanie.
- Obniż koszty dzięki konsolidacji i centralizacji zasobów.
- Zapewnij użytkownikom końcowym mobilność i swobodę dostępu do wirtualnych pulpitów w dowolnym miejscu i czasie i na dowolnym urządzeniu.

Istnieją dwa podstawowe typy pulpitów wirtualnych:

- Stale
- Nietrwałe

Wersje trwałe używają unikatowego obrazu pulpitu dla każdego użytkownika lub puli użytkowników. Te unikalne pulpity można dostosować i zapisać do wykorzystania w przyszłości. 

Wersje nietrwałe używają kolekcji pulpitów, do których użytkownicy mogą uzyskać dostęp w razie potrzeby. Te nietrwałe pulpity są przywracane do stanu pierwotnego po wylogowywania się użytkownika.

W tym artykule okryje się wskazówki firmy Microsoft dla administratorów dotyczące obsługi tożsamości urządzenia i VDI. Aby uzyskać więcej informacji na temat tożsamości urządzenia, zobacz artykuł [Co to jest tożsamość urządzenia](overview.md).

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Przed skonfigurowaniem tożsamości urządzeń w usłudze Azure AD dla środowiska VDI należy zapoznać się z obsługiwanymi scenariuszami. W poniższej tabeli przedstawiono, które scenariusze inicjowania obsługi administracyjnej są obsługiwane. Inicjowanie obsługi administracyjnej w tym kontekście oznacza, że administrator może skonfigurować tożsamości urządzeń na dużą skalę bez konieczności interakcji z użytkownikiem końcowym.

| Typ tożsamości urządzenia | Infrastruktura tożsamości | Urządzenia z systemem Windows | Wersja platformy VDI | Obsługiwane |
| --- | --- | --- | --- | --- |
| hybrydowym dołączonym do usługi Azure AD. | Federacyjne* | Windows current*** i Windows down-level**** | Stale | Tak |
|   |   | Bieżący system Windows | Nietrwałe | Nie |
|   |   | System Windows niższego poziomu | Nietrwałe | Tak |
|   | Zarządzane** | Bieżący system Windows i system Windows na poziomie down | Stale | Tak |
|   |   | Bieżący system Windows | Nietrwałe | Nie |
|   |   | System Windows niższego poziomu | Nietrwałe | Tak |
| Dołączone do usługi Azure AD | Federacyjni | Bieżący system Windows | Stale | Nie |
|   |   |   | Nietrwałe | Nie |
|   | Zarządzani | Bieżący system Windows | Stale | Nie |
|   |   |   | Nietrwałe | Nie |
| Zarejestrowane w usłudze Azure AD | Federacyjni | Bieżący system Windows | Stale | Nie |
|   |   |   | Nietrwałe | Nie |
|   | Zarządzani | Bieżący system Windows | Stale | Nie |
|   |   |   | Nietrwałe | Nie |

\*Środowisko infrastruktury tożsamości **federacyjnej** reprezentuje środowisko z dostawcą tożsamości, takim jak ad fs lub inny dostawca tożsamości innej firmy.

\*\*Środowisko infrastruktury tożsamości **zarządzanej** reprezentuje środowisko z usługą Azure AD jako dostawca tożsamości wdrożony z [synchronizacją skrótów haseł (PHS)](../hybrid/whatis-phs.md) lub [uwierzytelnianiem pass-through (PTA)](../hybrid/how-to-connect-pta.md) z [bezproblemowym logowaniem jednokrotnym.](../hybrid/how-to-connect-sso.md)

\*\*\***Obecne** urządzenia z systemem Windows reprezentują systemy Windows 10, Windows Server 2016 i Windows Server 2019.

\*\*\*\*Urządzenia **z systemem Windows w dół** reprezentują systemy Windows 7, Windows 8.1, Windows Server 2008 R2, Windows Server 2012 i Windows Server 2012 R2. Aby uzyskać informacje dotyczące pomocy technicznej w systemie Windows 7, zobacz [Pomoc techniczna dla systemu Windows 7 kończy się](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support). Aby uzyskać informacje dotyczące pomocy technicznej w systemie Windows Server 2008 R2, zobacz [Przygotowywanie dla systemu Windows Server 2008 po zakończeniu pomocy technicznej](https://www.microsoft.com/cloud-platform/windows-server-2008).

## <a name="microsofts-guidance"></a>Wskazówki firmy Microsoft

Administratorzy powinni odwoływać się do następujących artykułów, na podstawie ich infrastruktury tożsamości, aby dowiedzieć się, jak skonfigurować hybrydowe sprzężenie usługi Azure AD.

- [Konfigurowanie hybrydowego sprzężenia usługi Azure Active Directory dla środowiska federacyjnego](hybrid-azuread-join-federated-domains.md)
- [Konfigurowanie hybrydowego sprzężenia usługi Azure Active Directory dla środowiska zarządzanego](hybrid-azuread-join-managed-domains.md)

Jeśli korzystasz z narzędzia przygotowania systemu (sysprep.exe) i jeśli używasz obrazu sprzed systemu Windows 10 1809 do instalacji, upewnij się, że obraz nie pochodzi z urządzenia, które jest już zarejestrowane w usłudze Azure AD jako przyłączony hybrydowy usługi Azure AD.

Jeśli korzystasz z migawki maszyny wirtualnej (VM) w celu utworzenia dodatkowych maszyn wirtualnych, upewnij się, że migawka nie pochodzi z maszyny wirtualnej, która jest już zarejestrowana w usłudze Azure AD jako sprzężenie usługi Hybrid Azure AD.

Podczas wdrażania nietrwałej interfejsu VDI administratorzy IT powinni zwracać szczególną uwagę na zarządzanie przestarzałymi urządzeniami w usłudze Azure AD. Firma Microsoft zaleca administratorom IT wdrożenie poniższych wskazówek. Nie wykonywana zostanie ta decyzja spowoduje, że twój katalog będzie miał wiele starych urządzeń przyłączonych do usługi Azure AD, które zostały zarejestrowane na nietrwałej platformie VDI.

- Utwórz i użyj prefiksu nazwy wyświetlanej komputera, który wskazuje pulpit jako oparty na interfejsie VDI.
- Zaimplementuj następujące polecenie jako część skryptu wylogowywania. To polecenie wyzwoli wezwanie do usługi Azure AD, aby usunąć urządzenie.
   - Dla urządzeń z systemem Windows w dół — autoworkplace.exe /leave
- Definiowanie i wdrażanie procesu [zarządzania przestarzałymi urządzeniami](manage-stale-devices.md).
   - Po uzyskaniu strategii identyfikowania nietrwałych urządzeń połączonych z usługą Hybrid Azure AD, możesz być bardziej agresywny podczas czyszczenia tych urządzeń, aby upewnić się, że katalog nie zostanie zużyty przy wielu starych urządzeniach.
 
## <a name="next-steps"></a>Następne kroki

[Konfigurowanie hybrydowego sprzężenia usługi Azure Active Directory dla środowiska federacyjnego](hybrid-azuread-join-federated-domains.md)
