---
title: Ustawienia zasad grupy i mdm dla ESR — usługa Azure Active Directory
description: Ustawienia zarządzania dla roamingu w stanie przedsiębiorstwa
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdffbc3a140bd13dcd6d352db8c192803d39b03e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672364"
---
# <a name="group-policy-and-mdm-settings"></a>Ustawienia zasad grupy i mdm

Użyj tych zasad grupy i ustawień zarządzania urządzeniami przenośnymi (MDM) tylko na urządzeniach należących do firmy, ponieważ te zasady są stosowane do całego urządzenia użytkownika. Zastosowanie zasad MDM w celu wyłączenia synchronizacji ustawień dla osobistego urządzenia należącego do użytkownika negatywnie wpłynie na korzystanie z tego urządzenia. Ponadto zasady będą miały również wpływ na inne konta użytkowników na urządzeniu.

Przedsiębiorstwa, które chcą zarządzać roamingiem dla urządzeń osobistych (niezarządzanych), mogą używać witryny Azure Portal do włączania lub wyłączania roamingu, a nie przy użyciu zasad grupy lub usług MDM.
W poniższych tabelach opisano dostępne ustawienia zasad.

> [!NOTE]
> Ten artykuł dotyczy przeglądarki microsoft edge legacy w formacie HTML uruchomionej w systemie Windows 10 w lipcu 2015 r. Artykuł nie ma zastosowania do nowej przeglądarki opartej na chromie Microsoft Edge, wydanej 15 stycznia 2020 roku. Aby uzyskać więcej informacji na temat zachowania synchronizacji dla nowej przeglądarki Microsoft Edge, zobacz artykuł [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

## <a name="mdm-settings"></a>Ustawienia MDM

Ustawienia zasad MDM dotyczą zarówno systemu Windows 10, jak i systemu Windows 10 Mobile.  Obsługa systemu Windows 10 Mobile istnieje tylko w przypadku roamingu opartego na koncie Microsoft za pośrednictwem konta usługi OneDrive użytkownika. Szczegółowe informacje na temat urządzeń obsługiwanych w przypadku synchronizacji opartej na usłudze Azure AD można znaleźć w [obszarze Urządzenia i punkty końcowe.](enterprise-state-roaming-windows-settings-reference.md)

| Nazwa | Opis |
| --- | --- |
| Zezwalaj na połączenie z kontem Microsoft |Umożliwia użytkownikom uwierzytelnienie przy użyciu konta Microsoft na urządzeniu |
| Zezwalaj na synchronizację moich ustawień |Umożliwia użytkownikom poruszanie się po ustawieniach systemu Windows i danych aplikacji; Wyłączenie tych zasad spowoduje wyłączenie synchronizacji oraz kopii zapasowych na urządzeniach mobilnych |

## <a name="group-policy-settings"></a>Ustawienia zasad grupy

Ustawienia zasad grupy dotyczą urządzeń z systemem Windows 10, które są przyłączone do domeny usługi Active Directory. Tabela zawiera również starsze ustawienia, które wydają się zarządzać ustawieniami synchronizacji, ale które nie działają w roamingu korporacyjnym dla systemu Windows 10, które są odnotowane w opisie "Nie używaj".

Te ustawienia znajdują się pod adresem:`Computer Configuration > Administrative Templates > Windows Components > Sync your settings` 

| Nazwa | Opis |
| --- | --- |
| Konta: blokowanie kont Microsoft |To ustawienie zasad uniemożliwia użytkownikom dodawanie nowych kont Microsoft na tym komputerze |
| Nie synchronizuj |Uniemożliwia użytkownikom poruszanie się po ustawieniach systemu Windows i danych aplikacji |
| Nie synchronizuj personalizacji |Wyłącza synchronizację grupy Motywy |
| Nie synchronizuj ustawień przeglądarki |Wyłącza synchronizację grupy programu Internet Explorer |
| Nie synchronizuj haseł |Wyłącza synchronizację grupy Hasła |
| Nie synchronizuj innych ustawień systemu Windows |Wyłącza synchronizację z grupą Inne ustawienia systemu Windows |
| Nie synchronizuj personalizacji pulpitu |Nie używać; nie ma wpływu |
| Nie synchronizuj na połączeniach taryfowych |Wyłącza roaming na połączeniach taryfowych, takich jak komórkowe 3G |
| Nie synchronizuj aplikacji |Nie używać; nie ma wpływu |
| Nie synchronizuj ustawień aplikacji |Wyłącza roaming danych aplikacji |
| Nie synchronizuj ustawień startu |Nie używać; nie ma wpływu |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać omówienie, zobacz [omówienie roamingu stanu przedsiębiorstwa](enterprise-state-roaming-overview.md).
