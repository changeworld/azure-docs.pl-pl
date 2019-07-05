---
title: Ustawienia zarządzania urządzeniami Przenośnymi i zasady grupy | Dokumentacja firmy Microsoft
description: Informacje na temat zasad grupy i urządzeń przenośnych ustawień zarządzania (urządzeniami przenośnymi MDM), które powinny być używane na urządzeniach należących do firmy.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3f2b1afa67ec36da4d4da57b296e696fd6c6910
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481943"
---
# <a name="group-policy-and-mdm-settings"></a>Ustawienia zasad grupy i zarządzania urządzeniami Przenośnymi
Użyj tych zasad grupy i ustawienia zarządzania (urządzeniami przenośnymi MDM) dla urządzeń przenośnych tylko na urządzeniach należących do firmy, ponieważ te zasady są stosowane do całego urządzenia użytkownika. Stosowanie zasad zarządzania urządzeniami Przenośnymi, aby wyłączyć synchronizację ustawień do użytku osobistego, urządzenia należące do użytkownika zostanie negatywny wpływ na korzystanie z tego urządzenia. Ponadto innych kont użytkowników na urządzeniu będą również mieć wpływ na przez zasady.

Przedsiębiorstwa, które mają być zarządzane, roamingu dla urządzeń osobistych (niezarządzanego) można użyć witryny Azure portal można włączać lub wyłączać roaming, a nie za pomocą zasad grupy lub zarządzania urządzeniami przenośnymi.
W poniższych tabelach opisano dostępne ustawienia zasad.

## <a name="mdm-settings"></a>Ustawienia zarządzania urządzeniami Przenośnymi
Ustawienia zasad zarządzania urządzeniami Przenośnymi dotyczą zarówno systemu Windows 10 i Windows 10 Mobile.  Obsługa systemu Windows 10 Mobile istnieje tylko w przypadku konta Microsoft na podstawie mobilnych za pomocą konta usługi OneDrive użytkownika.  Zapoznaj się [urządzenia i punktów końcowych](enterprise-state-roaming-windows-settings-reference.md) szczegółowe informacje na temat urządzeń, które są obsługiwane w przypadku synchronizowania opartych na usłudze Azure AD.

| Name (Nazwa) | Opis |
| --- | --- |
| Zezwalaj na połączenie konta Microsoft |Umożliwia użytkownikom uwierzytelnianie przy użyciu konta Microsoft na urządzeniu |
| Zezwalaj na synchronizację ustawień |Umożliwia użytkownikom przechodzą Windows ustawienia i dane aplikacji; Wyłączenie tych zasad spowoduje wyłączenie synchronizacji, a także kopie zapasowe na urządzeniach przenośnych |

## <a name="group-policy-settings"></a>Ustawienia zasad grupy
Ustawienia zasad grupy są stosowane do urządzeń z systemem Windows 10, które są przyłączone do domeny usługi Active Directory. Uwzględniono również w starszych ustawień wyświetlanych do zarządzania ustawieniami synchronizacji, ale nie działają dla przedsiębiorstw stanu mobilnych dla systemu Windows 10, które są oznaczane przy użyciu 'nie używaj"w opisie.

Te ustawienia znajdują się na: `Computer Configuration > Administrative Templates > Windows Components > Sync your settings` 

| Name (Nazwa) | Opis |
| --- | --- |
| Konta: Blokowanie kont Microsoft |To ustawienie zasad uniemożliwia użytkownikom dodawanie nowych kont Microsoft na tym komputerze |
| Nie są synchronizowane |Uniemożliwia użytkownikom przechodzą Windows ustawienia i dane aplikacji |
| Nie są synchronizowane spersonalizować |Wyłącza synchronizację grupy motywów |
| Nie Synchronizuj ustawienia przeglądarki |Wyłącza synchronizację grupy programu Internet Explorer |
| Nie synchronizuje haseł |Wyłącza synchronizację haseł grupy |
| Nie Synchronizuj z innymi ustawieniami Windows |Wyłącza synchronizację Windows inne ustawienia grupy |
| Personalizacja pulpitu nie są synchronizowane |Nie używaj; nie ma wpływu |
| Nie są synchronizowane w przypadku mierzonych połączeń |Wyłącza roamingu w taryfowego połączenia, takich jak sieci komórkowej 3 G |
| Nie Synchronizuj aplikacje |Nie używaj; nie ma wpływu |
| Nie są synchronizowane ustawień aplikacji |Wyłącza roaming danych aplikacji |
| Nie są synchronizowane ustawień uruchamiania |Nie używaj; nie ma wpływu |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać przegląd, zobacz [omówienie roamingu stanu przedsiębiorstwa](enterprise-state-roaming-overview.md).


