---
title: Grupa ustawień zasad i zarządzania urządzeniami Przenośnymi | Dokumentacja firmy Microsoft
description: Zawiera informacje dotyczące zasad grupy i urządzeń przenośnych ustawień zarządzania urządzeniami Przenośnymi, które powinny być używane na urządzeniach należących do firmy. Te zasady są stosowane do wszystkich danych użytkownika z urządzenia.
services: active-directory
keywords: Co to są zasady grupy i ustawień zarządzania urządzeniami Przenośnymi roamingu stanu przedsiębiorstwa, roamingu stanu przedsiębiorstwa, chmury systemu windows
documentationcenter: ''
author: tanning
manager: mtillman
editor: curtand
ms.assetid: 6471a9b3-8dd4-4237-89d1-bfbeca9f8252
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: markvi
ms.openlocfilehash: 588084481ffc5cbbeed34e9527271179fa359ed5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2017
ms.locfileid: "26614911"
---
# <a name="group-policy-and-mdm-settings"></a>Ustawienia zasad grupy i zarządzania urządzeniami Przenośnymi
Użyj tych zasad grupy i ustawienia zarządzania urządzeniami Przenośnymi dla urządzeń przenośnych tylko na urządzeniach należących do firmy, ponieważ te zasady są stosowane do wszystkich danych użytkownika z urządzenia. Stosowanie zasad zarządzania urządzeniami Przenośnymi, można wyłączyć ustawienia synchronizacji dla osobistego urządzenia należące do użytkownika będzie negatywny wpływ na korzystanie z tego urządzenia. Ponadto inne konta użytkownika na urządzeniu zostanie dotyczy także przez zasady.

Przedsiębiorstw, które mają być zarządzane, mobilnych dla urządzenia osobiste (niezarządzany) mogą korzystać z portalu Azure można włączać lub wyłączać roaming, a nie za pomocą zasad grupy lub zarządzania urządzeniami przenośnymi.
W poniższych tabelach opisano dostępne ustawienia zasad.

## <a name="mdm-settings"></a>Ustawienia zarządzania urządzeniami Przenośnymi
Ustawienia zasad zarządzania urządzeniami Przenośnymi dotyczą zarówno systemu Windows 10 i Windows 10 Mobile.  Obsługa systemu Windows 10 Mobile istnieje tylko w przypadku konta Microsoft na podstawie mobilnych za pomocą konta usługi OneDrive użytkownika.  Zapoznaj się z rozdziałem "Urządzeń i punkty końcowe" szczegółowe informacje na temat obsługiwane urządzenia do synchronizacji usługi Azure AD na podstawie.

| Nazwa | Opis |
| --- | --- |
| Zezwalaj na połączenie konta Microsoft |Umożliwia użytkownikom uwierzytelnianie przy użyciu konta Microsoft na urządzeniu |
| Zezwalaj na synchronizację ustawień |Pozwala użytkownikom korzystać z roamingu, ustawienia systemu Windows i danych aplikacji; Wyłączenie tych zasad spowoduje wyłączenie synchronizacji, a także kopie zapasowe na urządzeniach przenośnych |

## <a name="group-policy-settings"></a>Ustawienia zasad grupy
Ustawienia zasad grupy mają zastosowanie do urządzeń z systemem Windows 10, które są przyłączone do domeny usługi Active Directory. Uwzględniono również w starszej wersji ustawień wyświetlanych do zarządzania ustawieniami synchronizacji, ale nie działają dla przedsiębiorstwa stanu mobilnych dla systemu Windows 10, które są oznaczane przy użyciu 'używam"w opisie.

| Nazwa | Opis |
| --- | --- |
| Konta: Blokowanie kont Microsoft |To ustawienie zasad uniemożliwia użytkownikom dodawanie nowych kont Microsoft na tym komputerze |
| Nie Synchronizuj |Uniemożliwia użytkownikom korzystać z roamingu, ustawienia systemu Windows i danych aplikacji |
| Nie Synchronizuj spersonalizować |Wyłącza synchronizację grupy motywów |
| Nie synchronizować ustawienia przeglądarki |Wyłącza synchronizację grupy programu Internet Explorer |
| Nie synchronizuje haseł |Wyłącza synchronizację haseł grupy |
| Nie Synchronizuj inne ustawienia systemu Windows |Wyłącza synchronizację inne okna Ustawienia grupy |
| Nie Synchronizuj personalizacja pulpitu |Nie używaj; nie ma wpływu |
| Synchronizacji w przypadku mierzonych połączeń |Wyłącza mobilnego na mierzonych połączeń, takich jak komórkowej sieci 3 G |
| Nie Synchronizuj aplikacje |Nie używaj; nie ma wpływu |
| Nie Synchronizuj ustawienia aplikacji |Wyłącza roaming danych aplikacji |
| Nie Synchronizuj ustawienia uruchamiania |Nie używaj; nie ma wpływu |

## <a name="related-topics"></a>Powiązane tematy
* [Opis roamingu stanu przedsiębiorstwa](active-directory-windows-enterprise-state-roaming-overview.md)
* [Włącz roaming stanu przedsiębiorstwa w usłudze Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
* [Ustawienia i dane mobilne — często zadawane pytania](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Informacje dotyczące ustawień roamingu systemu Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
* [Rozwiązywanie problemów](active-directory-windows-enterprise-state-roaming-troubleshooting.md)

