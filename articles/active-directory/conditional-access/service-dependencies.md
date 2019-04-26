---
title: Co to są zależności usługi w usłudze Azure Active Directory dostępu warunkowego? | Microsoft Docs
description: Dowiedz się, jak używane warunków do wyzwolenia zasad dostępu warunkowego usługi Azure Active Directory.
services: active-directory
keywords: dostęp warunkowy do aplikacji, dostęp warunkowy w usłudze Azure AD, zabezpieczenia dostępu do zasobów firmy, zasady dostępu warunkowego
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/18/2019
ms.author: joflore
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: f727fc7133ebc9ee124e63253e8a266862b0d908
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60354385"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>Co to są zależności usługi w usłudze Azure Active Directory dostępu warunkowego? 


Za pomocą zasad dostępu warunkowego można określić wymagania dotyczące dostępu do witryn internetowych i usług. Na przykład Twoje wymagania dotyczące dostępu mogą obejmować wymaganie uwierzytelniania wieloskładnikowego (MFA) lub [urządzeń zarządzanych przy użyciu](require-managed-devices.md). 


Gdy uzyskujesz dostęp do witryny lub usługi bezpośrednio, wpływ pokrewnych zasad jest zazwyczaj ułatwia ocenę. Na przykład jeśli masz zasady, które wymagają usługi MFA dla usługi SharePoint Online skonfigurowane, uwierzytelnianie wieloskładnikowe jest wymuszana podczas każdego logowania do portalu sieci web programu SharePoint. Jednak nie zawsze jest proste, aby ocenić wpływ zasad, ponieważ istnieją aplikacje w chmurze z zależnościami do innych aplikacji w chmurze. Na przykład Microsoft Teams korzysta z programu SharePoint online. Tak gdy uzyskujesz dostęp do Microsoft Teams w naszym scenariuszu należy podlegają także zasady MFA programu SharePoint.   


## <a name="policy-enforcement"></a>Wymuszanie zasad 

W przypadku zależności usługi skonfigurowane zasady można stosować przy użyciu wymuszenia wczesnym wiązaniem lub z późnym wiązaniem. 

**Wymuszanie zasad z wczesnym wiązaniem** oznacza, że użytkownik musi spełnić wymagania zasad usług zależnych przed uzyskaniem dostępu do aplikacji wywołującej. Na przykład użytkownik musi spełniać zasady usługi SharePoint, przed zalogowaniem się do MS Teams. 

**Wymuszanie zasad z późnym wiązaniem** występuje po użytkownik zalogował do wywoływania aplikacji. Wymuszanie jest odroczone do podczas wywoływania żądań aplikacji, token usługi podrzędne. Przykłady obejmują MS Teams uzyskiwanie dostępu do aplikacji Planner i Office.com uzyskiwania dostępu do programu SharePoint. 

Poniższy diagram przedstawia zależności usług MS Teams. Stałe strzałki wskazują wymuszania wczesnym wiązaniem przerywaną strzałką dla aplikacji Planner wskazuje wymuszania z późnym wiązaniem. 



![Zależności usług MS Teams](./media/service-dependencies/01.png)



  

Najlepszym rozwiązaniem należy ustawić wspólnych zasad obejmujących wiele pokrewnych aplikacji i usług zawsze, gdy jest to możliwe. Posiadanie poziom zabezpieczeń spójne zapewnia najlepsze środowisko użytkownika. Na przykład ustawienie wspólne zasady dla usługi Exchange Online, SharePoint Online, MS Teams i Skype dla firm znacznie zmniejsza nieoczekiwane monity, które mogą wynikać z różnych zasad są stosowane do usługach niższego rzędu. 

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
| Project             | Dynamics CRM                                | Wczesnym wiązaniem |
| Skype dla firm  | Exchange                                    | Wczesnym wiązaniem |
| Visual Studio       | Usługa Microsoft Azure Management (portal i interfejsu API) | Wczesnym wiązaniem |



## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak wdrożyć dostęp warunkowy w danym środowisku, zobacz [Planowanie wdrożenia dostępu warunkowego w usłudze Azure Active Directory](plan-conditional-access.md).
