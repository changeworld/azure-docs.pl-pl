---
title: Co to jest roaming w stanie przedsiębiorstwa w usłudze Azure Active Directory?
description: Roaming w stanie przedsiębiorstwa zapewnia użytkownikom ujednolicone środowisko na urządzeniach z systemem Windows
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: c22baf0a08718883f0c0c9844cc395f607b5b20d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77194283"
---
# <a name="what-is-enterprise-state-roaming"></a>Co to jest roaming stanu dla przedsiębiorstw?

Dzięki systemowi Windows 10 [użytkownicy usługi Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) uzyskują możliwość bezpiecznej synchronizacji ustawień użytkownika i danych ustawień aplikacji z chmurą. Roaming w stanie przedsiębiorstwa zapewnia użytkownikom ujednolicone środowisko na swoich urządzeniach z systemem Windows i skraca czas potrzebny na skonfigurowanie nowego urządzenia. Roaming stanu przedsiębiorstwa działa podobnie do [standardowej synchronizacji ustawień konsumenta,](https://go.microsoft.com/fwlink/?linkid=2015135) która została po raz pierwszy wprowadzona w systemie Windows 8. Ponadto usługi Roamingu w stanie przedsiębiorstwa oferują:

* **Oddzielenie danych firmowych i konsumenckich** — organizacje kontrolują swoje dane i nie ma mieszania danych firmowych na koncie w chmurze konsumenta lub danych konsumenta na koncie w chmurze przedsiębiorstwa.
* **Zwiększone bezpieczeństwo** — dane są automatycznie szyfrowane przed opuszczeniem urządzenia z systemem Windows 10 użytkownika przy użyciu usługi Azure Rights Management (Azure RMS), a dane pozostają zaszyfrowane w spoczynku w chmurze. Cała zawartość pozostaje zaszyfrowana w chmurze, z wyjątkiem obszarów nazw, takich jak nazwy ustawień i nazwy aplikacji systemu Windows.  
* **Lepsze zarządzanie i monitorowanie** — zapewnia kontrolę i wgląd w to, kto synchronizuje ustawienia w organizacji i na jakich urządzeniach za pośrednictwem integracji z portalem usługi Azure AD. 

Roaming w stanie przedsiębiorstwa jest dostępny w wielu regionach platformy Azure. Zaktualizowaną listę dostępnych regionów można znaleźć na stronie [Usługi platformy Azure według regionów](https://azure.microsoft.com/regions/#services) w obszarze Usługa Azure Active Directory.

| Artykuł | Opis |
| --- | --- |
| [Włączanie roamingu stanu przedsiębiorstwa w usłudze Azure Active Directory](enterprise-state-roaming-enable.md) |Roaming w stanie przedsiębiorstwa jest dostępny dla każdej organizacji z subskrypcją usługi Azure Active Directory (Azure AD). Aby uzyskać więcej informacji na temat sposobu uzyskania subskrypcji usługi Azure AD, zobacz stronę [produktu usługi Azure AD.](https://azure.microsoft.com/services/active-directory) |
| [Roaming ustawień i danych — często zadawane pytania](enterprise-state-roaming-faqs.md) |Ten artykuł zawiera odpowiedzi na niektóre pytania, jakie mogą mieć administratorzy IT dotyczące ustawień i synchronizacji danych aplikacji. |
| [Ustawienia zasad grupy i mdm synchronizacji ustawień](enterprise-state-roaming-group-policy-settings.md) |System Windows 10 udostępnia ustawienia zasad zasad zasad grupy i zarządzania urządzeniami przenośnymi (MDM), aby ograniczyć synchronizację ustawień. |
| [Informacje dotyczące ustawień roamingu w systemie Windows 10](enterprise-state-roaming-windows-settings-reference.md) |Lista ustawień, które będą poruszać się i/lub archiwizowane w systemie Windows 10. |
| [Rozwiązywanie problemów](enterprise-state-roaming-troubleshooting.md) |W tym artykule opisano kilka podstawowych kroków rozwiązywania problemów i zawiera listę znanych problemów. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat włączania roamingu w stanie przedsiębiorstwa, zobacz [włączanie roamingu w stanie przedsiębiorstwa](enterprise-state-roaming-enable.md).
