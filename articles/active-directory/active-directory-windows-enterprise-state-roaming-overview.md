---
title: Opis roamingu stanu przedsiębiorstwa | Dokumentacja firmy Microsoft
description: Zawiera informacje dotyczące ustawień roamingu stanu przedsiębiorstwa na urządzeniach z systemem Windows. Roamingu stanu przedsiębiorstwa udostępnia użytkownikom środowisko unified na ich urządzeniach z systemem Windows i skraca czas potrzebny na konfigurowanie nowego urządzenia.
services: active-directory
keywords: Co to jest roamingu stanu przedsiębiorstwa, synchronizacji przedsiębiorstwa systemu windows w chmurze
documentationcenter: ''
author: tanning
manager: mtillman
editor: curtand
ms.assetid: 83b3b58f-94c1-4ab0-be05-20e01f5ae3f0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: markvi
ms.openlocfilehash: 2e1ebf5a9bfc4a1f0f92ff85f9406ecc7d538819
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2017
ms.locfileid: "26614953"
---
# <a name="enterprise-state-roaming-overview"></a>Omówienie roamingu stanu przedsiębiorstwa
Z systemem Windows 10 [usługi Azure Active Directory (Azure AD)](active-directory-whatis.md) użytkownikom możliwość bezpiecznego synchronizowanie ich ustawień i danych ustawienia aplikacji w chmurze. Roamingu stanu przedsiębiorstwa udostępnia użytkownikom środowisko unified na ich urządzeniach z systemem Windows i skraca czas potrzebny na konfigurowanie nowego urządzenia. Roamingu stanu przedsiębiorstwa działa podobnie do standardowego [konsumenta ustawienia synchronizacji](http://windows.microsoft.com/en-US/windows-8/sync-settings-pcs) która została wprowadzona w systemie Windows 8. Ponadto roamingu stanu przedsiębiorstwa oferuje:

* **Rozdzielenie firmowych i konsumentów danych** — organizacje mają kontrolę nad ich danych i nie istnieje żadne mieszanie danych firmowych w konta użytkownika w chmurze lub danych konsumentów w konta chmury przedsiębiorstwa.
* **Ulepszone zabezpieczenia** — dane są automatycznie szyfrowane przed opuszczeniem urządzenie użytkownika systemu Windows 10 za pomocą usługi Azure Rights Management (Azure RMS) i danych pozostaje zaszyfrowany magazynowanych w chmurze. Cała zawartość pozostaje zaszyfrowany magazynowanych w chmurze, z wyjątkiem przestrzeni nazw, takich jak ustawienia nazwy i nazwy aplikacji systemu Windows.  
* **Lepsze monitorowanie i zarządzanie** — zapewnia widoczność i kontroli, przez który synchronizuje ustawienia w Twojej organizacji i na których urządzeniach dzięki integracji portalu usługi Azure AD. 

Roamingu stanu przedsiębiorstwa jest dostępne w wielu regionach platformy Azure. Zaktualizowaną listę dostępnych regionów można znaleźć na [usługi Azure uporządkowane według regionów](https://azure.microsoft.com/regions/#services) strony w usłudze Azure Active Directory.

| Artykuł | Opis |
| --- | --- |
| [Włącz Roaming stanu przedsiębiorstwa w usłudze Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md) |Roamingu stanu przedsiębiorstwa jest dostępne dla każdej organizacji z subskrypcją Premium usługi Azure Active Directory (Azure AD). Aby uzyskać więcej informacji na temat sposobu uzyskania subskrypcji usługi Azure AD, zobacz [produktu usługi Azure AD](https://azure.microsoft.com/services/active-directory) strony. |
| [Ustawienia i dane mobilne — często zadawane pytania](active-directory-windows-enterprise-state-roaming-faqs.md) |W tym temacie odpowiedzi na pytania Administratorzy IT mogą mieć o ustawieniach i synchronizacji danych aplikacji. |
| [Zasady grupy i ustawienia zarządzania urządzeniami Przenośnymi dla ustawień synchronizacji](active-directory-windows-enterprise-state-roaming-group-policy-settings.md) |Windows 10 udostępnia zasad grupy i zarządzanie urządzeniami przenośnymi (MDM) ustawienia zasad, aby ograniczyć ustawienia synchronizacji. |
| [Informacje dotyczące ustawień roamingu systemu Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md) |Poniżej znajduje się pełna lista wszystkich ustawień, które będą przekazywane i/lub być zapasową w systemie Windows 10. |
| [Rozwiązywanie problemów](active-directory-windows-enterprise-state-roaming-troubleshooting.md) |W tym temacie niektóre podstawowe kroki do rozwiązywania problemów i zawiera listę znanych problemów. |

