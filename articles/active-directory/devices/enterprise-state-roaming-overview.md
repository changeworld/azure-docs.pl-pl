---
title: Co to jest enterprise roaming stanu dla usługi Azure Active Directory? | Microsoft Docs
description: Zawiera informacje dotyczące ustawień roamingu stanu przedsiębiorstwa na urządzeniach Windows. Roaming stanu dla przedsiębiorstw udostępnia użytkownikom na ich urządzeniach Windows ujednoliconego środowiska i skraca czas potrzebny do konfigurowania nowego urządzenia.
services: active-directory
keywords: Co to jest Roaming stanu dla przedsiębiorstw, synchronizacja przedsiębiorstwa systemu windows w chmurze
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: curtand
ms.subservice: devices
ms.assetid: 83b3b58f-94c1-4ab0-be05-20e01f5ae3f0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: joflore
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3a2a81bd8aa3fc99d033564e8a8782c79261305
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60353146"
---
# <a name="what-is-enterprise-state-roaming"></a>Co to jest roaming stanu dla przedsiębiorstw?

W systemie Windows 10 [usługi Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) użytkownicy zyskują możliwość bezpiecznego zsynchronizować swoje ustawienia użytkownika i dane ustawień aplikacji w chmurze. Roaming stanu dla przedsiębiorstw udostępnia użytkownikom na ich urządzeniach Windows ujednoliconego środowiska i skraca czas potrzebny do konfigurowania nowego urządzenia. Roaming stanu dla przedsiębiorstw działa podobnie do standardowych [synchronizację ustawień odbiorcy](https://go.microsoft.com/fwlink/?linkid=2015135) która została wprowadzona w systemie Windows 8. Ponadto Roaming stanu dla przedsiębiorstw oferuje:

* **Rozdzielenie firmowych i dane klienta** — organizacje są kontrolę nad ich danymi i nie ma żadnych mieszanie danych firmowych konsumenta koncie w chmurze lub odbiorcy danych na koncie usługi enterprise cloud.
* **Zwiększone zabezpieczenia** — dane są automatycznie szyfrowane przed opuszczeniem urządzenia z systemem Windows 10 użytkownika za pomocą usługi Azure Rights Management (Azure RMS) i danych pozostaje zaszyfrowany magazynowanych w chmurze. Cała zawartość pozostaje zaszyfrowany magazynowanych w chmurze, z wyjątkiem przestrzeni nazw, takich jak ustawienia nazwy i nazwy aplikacji Windows.  
* **Lepsze zarządzanie i monitorowanie** — udostępnia kontroli i widoczności w porównaniu z który synchronizuje ustawienia w Twojej organizacji i na których urządzeniach za pośrednictwem portalu integracji usługi Azure AD. 

Roaming stanu dla przedsiębiorstw jest dostępna w wielu regionach platformy Azure. Zaktualizowaną listę dostępnych regionów można znaleźć na [usług platformy Azure według regionów](https://azure.microsoft.com/regions/#services) stronie w obszarze Azure Active Directory.

| Artykuł | Opis |
| --- | --- |
| [Włączanie roamingu stanu przedsiębiorstwa w usłudze Azure Active Directory](enterprise-state-roaming-enable.md) |Roaming stanu dla przedsiębiorstw jest dostępny dla każdej organizacji z subskrypcją Premium usługi Azure Active Directory (Azure AD). Aby uzyskać szczegółowe informacje na temat sposobu uzyskania subskrypcję usługi Azure AD, zobacz [produktu Azure AD](https://azure.microsoft.com/services/active-directory) strony. |
| [Często zadawane pytania dotyczące roaming ustawień i danych](enterprise-state-roaming-faqs.md) |W tym temacie odpowiedzi na kilka pytań, na które Administratorzy IT mogą się pojawić w ustawień i synchronizacji danych w aplikacji. |
| [Zasady grupy i ustawienia zarządzania urządzeniami Przenośnymi w celu ustawienia synchronizacji](enterprise-state-roaming-group-policy-settings.md) |Systemu Windows 10 udostępnia zasady grupy i zarządzanie urządzeniami przenośnymi (MDM) ustawienia zasad w celu ograniczenia synchronizację ustawień. |
| [Informacje dotyczące ustawień roamingu systemu Windows 10](enterprise-state-roaming-windows-settings-reference.md) |Oto Pełna lista wszystkich ustawień które będą się korzystania z roamingu i/lub kopię w górę w systemie Windows 10. |
| [Rozwiązywanie problemów](enterprise-state-roaming-troubleshooting.md) |W tym temacie omówiono niektóre podstawowe kroki rozwiązywania problemów i zawiera listę znanych problemów. |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje na temat włączania, roaming stanu dla przedsiębiorstw, zobacz [włączyć roaming stanu dla przedsiębiorstw](enterprise-state-roaming-enable.md).
