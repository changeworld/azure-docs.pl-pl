---
title: Zależności usługi dostępu warunkowego — usługa Azure Active Directory
description: Dowiedz się, jak warunki są używane w usłudze Azure Active Directory dostęp warunkowy do wyzwalania zasad.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: b39238575c05d35a2d87999e08c49c0c77e99bfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74380010"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>Co to są zależności usług w usłudze Azure Active Directory Dostęp warunkowy? 

Za pomocą zasad dostępu warunkowego można określić wymagania dotyczące dostępu do witryn sieci Web i usług. Na przykład wymagania dotyczące dostępu mogą obejmować wymaganie uwierzytelniania wieloskładnikowego (MFA) lub [urządzeń zarządzanych.](require-managed-devices.md) 

Gdy uzyskujesz bezpośredni dostęp do witryny lub usługi, wpływ powiązanych zasad jest zazwyczaj łatwy do oceny. Jeśli na przykład masz zasady, które wymagają skonfigurowania usługi MFA dla usługi SharePoint Online, usługa MFA jest wymuszana dla każdego logowania do portalu sieci Web programu SharePoint. Jednak nie zawsze jest prosta do oceny wpływu zasad, ponieważ istnieją aplikacje w chmurze z zależnościami do innych aplikacji w chmurze. Na przykład usługa Microsoft Teams może zapewnić dostęp do zasobów w usłudze SharePoint Online. Tak, gdy dostęp do usługi Microsoft Teams w naszym bieżącym scenariuszu, są również podlega zasadom usługi SharePoint uwierzytelniania wieloskładnikowego.   

## <a name="policy-enforcement"></a>Wymuszanie zasad 

Jeśli masz skonfigurowaną zależność usługi, zasady mogą być stosowane przy użyciu wymuszania we wczesnym lub późnym powiązaniu. 

- **Wczesne wymuszanie zasad** oznacza, że użytkownik musi spełnić zasady usługi zależnej przed uzyskaniam dostępu do aplikacji wywołującej. Na przykład użytkownik musi spełniać zasady programu SharePoint przed zalogowaniem się do MS Teams. 
- **Wymuszanie zasad z późnym opóźnieniem** występuje po zalogowaniu się użytkownika do aplikacji wywołującej. Wymuszanie jest odroczone do podczas wywoływania żądań aplikacji, token dla usługi podrzędnej. Przykłady obejmują ms teams dostępu do plannera i Office.com dostęp do programu SharePoint. 

Poniższy diagram ilustruje zależności usługi MS Teams. Strzałki bryły wskazują wczesne wymuszanie strzałki przerywanej dla aplikacji Planner, wskazujące wymuszanie z późnym opóźnieniem. 

![Zależności usług MS Teams](./media/service-dependencies/01.png)

Najlepszym rozwiązaniem jest ustawienie wspólnych zasad w powiązanych aplikacjach i usługach, gdy tylko jest to możliwe. Spójna postawa zabezpieczeń zapewnia najlepsze środowisko użytkownika. Na przykład ustawienie wspólnych zasad w usługach Exchange Online, SharePoint Online, Microsoft Teams i Skype dla firm znacznie zmniejsza nieoczekiwane monity, które mogą wynikać z różnych zasad stosowanych do usług podrzędnych. 

Poniższa tabela zawiera listę dodatkowych zależności usług, w których aplikacje klienckie muszą spełniać  

| Aplikacje klienckie         | Usługa niższego rzędu                          | Egzekwowania |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Microsoft Azure Management (portal i interfejs API) | Wczesna granica |
| Microsoft Classroom | Exchange                                    | Wczesna granica |
|                     | Program SharePoint                                  | Wczesna granica |
| Microsoft Teams     | Exchange                                    | Wczesna granica |
|                     | Planista MS                                  | Późne powiązanie  |
|                     | Program SharePoint                                  | Wczesna granica |
|                     | Skype dla firm Online                   | Wczesna granica |
| Office Portal       | Exchange                                    | Późne powiązanie  |
|                     | Program SharePoint                                  | Późne powiązanie  |
| Grupy programu Outlook      | Exchange                                    | Wczesna granica |
|                     | Program SharePoint                                  | Wczesna granica |
| PowerApps           | Microsoft Azure Management (portal i interfejs API) | Wczesna granica |
|                     | Windows Azure Active Directory              | Wczesna granica |
| Project             | Dynamics CRM                                | Wczesna granica |
| Skype dla firm  | Exchange                                    | Wczesna granica |
| Visual Studio       | Microsoft Azure Management (portal i interfejs API) | Wczesna granica |
| Microsoft Forms     | Exchange                                    | Wczesna granica |
|                     | Program SharePoint                                  | Wczesna granica |
| Microsoft To-Do     | Exchange                                    | Wczesna granica |

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zaimplementować dostęp warunkowy w swoim środowisku, zobacz [Planowanie wdrożenia dostępu warunkowego w usłudze Azure Active Directory](plan-conditional-access.md).
