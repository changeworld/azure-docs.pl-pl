---
title: Ustawienia zarządzania urządzeniami Przenośnymi i zasady grupy | Dokumentacja firmy Microsoft
description: Informacje na temat zasad grupy i urządzeń przenośnych ustawień zarządzania (urządzeniami przenośnymi MDM), które powinny być używane na urządzeniach należących do firmy. Te zasady są stosowane do całego urządzenia użytkownika.
services: active-directory
keywords: Co to są grupy zasad i ustawień zarządzania urządzeniami Przenośnymi Roaming stanu dla przedsiębiorstw, Roaming stanu dla przedsiębiorstw, chmura systemu windows
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: curtand
ms.component: devices
ms.assetid: 6471a9b3-8dd4-4237-89d1-bfbeca9f8252
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: markvi
ms.openlocfilehash: 9db0fa29f6af0053d45f9f0238b52ac34fdb464a
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223265"
---
# <a name="group-policy-and-mdm-settings"></a>Ustawienia zasad grupy i zarządzania urządzeniami Przenośnymi
Użyj tych zasad grupy i ustawienia zarządzania (urządzeniami przenośnymi MDM) dla urządzeń przenośnych tylko na urządzeniach należących do firmy, ponieważ te zasady są stosowane do całego urządzenia użytkownika. Stosowanie zasad zarządzania urządzeniami Przenośnymi, aby wyłączyć synchronizację ustawień do użytku osobistego, urządzenia należące do użytkownika zostanie negatywny wpływ na korzystanie z tego urządzenia. Ponadto innych kont użytkowników na urządzeniu będą również mieć wpływ na przez zasady.

Przedsiębiorstwa, które mają być zarządzane, roamingu dla urządzeń osobistych (niezarządzanego) można użyć witryny Azure portal można włączać lub wyłączać roaming, a nie za pomocą zasad grupy lub zarządzania urządzeniami przenośnymi.
W poniższych tabelach opisano dostępne ustawienia zasad.

## <a name="mdm-settings"></a>Ustawienia zarządzania urządzeniami Przenośnymi
Ustawienia zasad zarządzania urządzeniami Przenośnymi dotyczą zarówno systemu Windows 10 i Windows 10 Mobile.  Obsługa systemu Windows 10 Mobile istnieje tylko w przypadku konta Microsoft na podstawie mobilnych za pomocą konta usługi OneDrive użytkownika.  Można znaleźć sekcji "Urządzeń i punkty końcowe", aby uzyskać szczegółowe informacje, na jakie urządzenia są obsługiwane w przypadku synchronizacji usługi Azure AD na podstawie.

| Name (Nazwa) | Opis |
| --- | --- |
| Zezwalaj na połączenie konta Microsoft |Umożliwia użytkownikom uwierzytelnianie przy użyciu konta Microsoft na urządzeniu |
| Zezwalaj na synchronizację ustawień |Umożliwia użytkownikom przechodzą Windows ustawienia i dane aplikacji; Wyłączenie tych zasad spowoduje wyłączenie synchronizacji, a także kopie zapasowe na urządzeniach przenośnych |

## <a name="group-policy-settings"></a>Ustawienia zasad grupy
Ustawienia zasad grupy zastosować do urządzeń z systemem Windows 10, które są przyłączone do domeny usługi Active Directory. Uwzględniono również w starszych ustawień wyświetlanych do zarządzania ustawieniami synchronizacji, ale nie działają dla przedsiębiorstw stanu mobilnych dla systemu Windows 10, które są oznaczane przy użyciu 'nie używaj"w opisie.

| Name (Nazwa) | Opis |
| --- | --- |
| Konta: Konta Microsoft blok |To ustawienie zasad uniemożliwia użytkownikom dodawanie nowych kont Microsoft na tym komputerze |
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

## <a name="related-topics"></a>Powiązane tematy
* [Omówienie roamingu stanu przedsiębiorstwa](active-directory-windows-enterprise-state-roaming-overview.md)
* [Włączanie roamingu stanu przedsiębiorstwa w usłudze Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
* [Często zadawane pytania dotyczące roaming ustawień i danych](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Informacje dotyczące ustawień roamingu systemu Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
* [Rozwiązywanie problemów](active-directory-windows-enterprise-state-roaming-troubleshooting.md)

