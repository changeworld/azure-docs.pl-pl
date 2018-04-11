---
title: "Zarządzanie dostępem do zasobów platformy Azure w usłudze Azure Active Directory"
description: "Więcej informacji na temat sposobów, aby zarządzać dostępem do zasobów platformy Azure przy użyciu różnych funkcji usługi Azure Active Directory."
services: active-directory
documentationcenter: 
author: skwan
manager: mtillman
editor: bryanla
ms.assetid: f66abf54-3809-436c-92b6-018e1179780e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/05/2017
ms.author: skwan
ms.openlocfilehash: eee4353c183aeec19f72f8e1dec6c20b6c5bb226
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2017
---
# <a name="manage-access-to-azure-resources-with-azure-active-directory"></a>Zarządzanie dostępem do zasobów platformy Azure w usłudze Azure Active Directory

Zarządzanie tożsamościami i dostępem do zasobów w chmurze jest funkcją krytyczne dla każdej organizacji korzystających z chmury. Azure Active Directory (Azure AD) jest tożsamości i dostępu do systemu Microsoft Azure.  

Przed rozpoczęciem pracy z towarzyszące funkcji obszary usługi Azure AD, zapoznaj się z poniższym klipie wideo: "Zablokowanie dostępu do chmury platformy Azure przy użyciu logowania jednokrotnego, kontroli dostępu na podstawie ról i warunkowe." W ramach tego informacje o:

- Najlepsze rozwiązania dotyczące konfigurowania rejestracji jednokrotnej w portalu Azure, z lokalną usługą Active Directory.
- Przy użyciu usługi Azure RBAC dla szczegółowej kontroli dostępu do zasobów w ramach subskrypcji.
- Wymuszania reguł silne uwierzytelnianie za pomocą usługi Azure AD warunkowego dostępu.
- Pojęcie zarządzane tożsamość usługi, którym zasobów platformy Azure można automatycznie uwierzytelniania w usłudze Azure, a deweloperzy nie ma potrzeby obsługi interfejsu API kluczy lub kluczy tajnych.

> [!VIDEO https://www.youtube.com/embed/FKBoWWKRnvI]

## <a name="feature-areas"></a>Obszarów funkcji
Usługa Azure AD zapewnia następujące możliwości zarządzania dostępem do zasobów platformy Azure:

|||
|---|---|
| [Relacja między dzierżaw usługi Azure AD i subskrypcji](active-directory-understanding-resource-access.md) | Dowiedz się więcej o usłudze Azure AD jest zaufanego źródła, użytkowników i grup dla subskrypcji platformy Azure. |
| [Kontrola dostępu oparta na rolach (RBAC)](role-based-access-control-what-is.md) | Oferują precyzyjne zarządzanie dostępem za pomocą ról przypisane do użytkowników, grupy lub podmiotów zabezpieczeń usługi. |
| [Zarządzanie tożsamościami uprzywilejowanymi o RBAC](pim-azure-resource.md) | Formant wysokiej uprzywilejowany dostęp przez przypisanie ról uprzywilejowanych just-in-time. |
| [Dostęp warunkowy dla zarządzania platformy Azure](conditional-access-azure-management.md) | Konfigurowanie zasad dostępu warunkowego, aby umożliwić lub zablokować dostęp do punktów końcowych zarządzania platformy Azure. |
| [Tożsamość usługi zarządzanej (MSI)](msi-overview.md) | Nadaj zasobów platformy Azure, takich jak tożsamość własne maszyny wirtualne, dzięki czemu nie trzeba wprowadzić poświadczenia w kodzie. |

 