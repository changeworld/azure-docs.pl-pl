---
title: Co to są zależności usługi Azure Active Directory dostępu warunkowego? | Microsoft Docs
description: Dowiedz się, jak warunki są używane w usłudze Azure Active Directory dostępu warunkowego do wyzwolenia zasad.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9aca2e4ea5e107358ff72e83562057830ece2cc
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509354"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>Co to są zależności usługi Azure Active Directory dostępu warunkowego? 

Za pomocą zasad dostępu warunkowego można określić wymagania dotyczące dostępu do witryn internetowych i usług. Na przykład Twoje wymagania dotyczące dostępu mogą obejmować wymaganie uwierzytelniania wieloskładnikowego (MFA) lub [urządzeń zarządzanych przy użyciu](require-managed-devices.md). 

Gdy uzyskujesz dostęp do witryny lub usługi bezpośrednio, wpływ pokrewnych zasad jest zazwyczaj ułatwia ocenę. Na przykład jeśli masz zasady, które wymagają usługi MFA dla usługi SharePoint Online skonfigurowane, uwierzytelnianie wieloskładnikowe jest wymuszana podczas każdego logowania do portalu sieci web programu SharePoint. Jednak nie zawsze jest proste, aby ocenić wpływ zasad, ponieważ istnieją aplikacje w chmurze z zależnościami do innych aplikacji w chmurze. Na przykład Microsoft Teams można zapewnić dostęp do zasobów w usłudze SharePoint Online. Tak gdy uzyskujesz dostęp do Microsoft Teams w naszym scenariuszu należy podlegają także zasady MFA programu SharePoint.   

## <a name="policy-enforcement"></a>Wymuszanie zasad 

W przypadku zależności usługi skonfigurowane zasady można stosować przy użyciu wymuszenia wczesnym wiązaniem lub z późnym wiązaniem. 

- **Wymuszanie zasad z wczesnym wiązaniem** oznacza, że użytkownik musi spełnić wymagania zasad usług zależnych przed uzyskaniem dostępu do aplikacji wywołującej. Na przykład użytkownik musi spełniać zasady usługi SharePoint, przed zalogowaniem się do MS Teams. 
- **Wymuszanie zasad z późnym wiązaniem** występuje po użytkownik zalogował do wywoływania aplikacji. Wymuszanie jest odroczone do podczas wywoływania żądań aplikacji, token usługi podrzędne. Przykłady obejmują MS Teams uzyskiwanie dostępu do aplikacji Planner i Office.com uzyskiwania dostępu do programu SharePoint. 

Poniższy diagram przedstawia zależności usług MS Teams. Stałe strzałki wskazują wymuszania wczesnym wiązaniem przerywaną strzałką dla aplikacji Planner wskazuje wymuszania z późnym wiązaniem. 

![Zależności usług MS Teams](./media/service-dependencies/01.png)

Najlepszym rozwiązaniem należy ustawić wspólnych zasad obejmujących wiele pokrewnych aplikacji i usług zawsze, gdy jest to możliwe. Posiadanie poziom zabezpieczeń spójne zapewnia najlepsze środowisko użytkownika. Na przykład ustawienie wspólne zasady dla usługi Exchange Online, SharePoint Online, Microsoft Teams i Skype dla firm znacznie zmniejsza nieoczekiwane monity, które mogą wynikać z różnych zasad są stosowane do usługach niższego rzędu. 

Poniższa tabela zawiera listę zależności usług dodatkowych, gdzie muszą spełniać aplikacje klienckie  

| Aplikacje klienckie         | Usługa transmisji                          | Wymuszanie |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Usługa Microsoft Azure Management (portal i interfejsu API) | Wczesnym wiązaniem |
| Microsoft Classroom | Exchange                                    | Wczesnym wiązaniem |
|                     | Sharepoint                                  | Wczesnym wiązaniem  |
| Microsoft Teams     | Exchange                                    | Wczesnym wiązaniem |
|                     | MS Planner                                  | Z późnym wiązaniem  |
|                     | Sharepoint                                  | Wczesnym wiązaniem |
|                     | Skype dla firm Online                   | Wczesnym wiązaniem |
| Office Portal       | Exchange                                    | Z późnym wiązaniem  |
|                     | Sharepoint                                  | Z późnym wiązaniem  |
| Grupy programu Outlook      | Exchange                                    | Wczesnym wiązaniem |
|                     | Sharepoint                                  | Wczesnym wiązaniem |
| PowerApps           | Usługa Microsoft Azure Management (portal i interfejsu API) | Wczesnym wiązaniem |
|                     | Windows Azure Active Directory              | Wczesnym wiązaniem |
| Projekt             | Dynamics CRM                                | Wczesnym wiązaniem |
| Skype dla firm  | Exchange                                    | Wczesnym wiązaniem |
| Visual Studio       | Usługa Microsoft Azure Management (portal i interfejsu API) | Wczesnym wiązaniem |

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak wdrożyć dostęp warunkowy w danym środowisku, zobacz [Planowanie wdrożenia dostępu warunkowego w usłudze Azure Active Directory](plan-conditional-access.md).
