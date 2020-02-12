---
title: Zarządzanie dostępem do zasobów platformy Azure za pomocą usługi Azure AD i usług PIM
description: Dowiedz się więcej na temat zarządzania dostępem do zasobów platformy Azure przy użyciu usług Azure Active Directory Privileged Identity Management (PIM) i kontroli dostępu opartej na rolach (RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: ba06b8dd-4a74-4bda-87c7-8a8583e6fd14
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 53c6375ce04425eae27f4c935e613cc27d058dc8
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138038"
---
# <a name="manage-access-to-azure-resources-with-azure-ad-privileged-identity-management"></a>Zarządzanie dostępem do zasobów platformy Azure za pomocą Azure AD Privileged Identity Management

Aby chronić konta uprzywilejowane przed złośliwymi atakami cybernetycznymiymi, możesz użyć Azure Active Directory Privileged Identity Management (PIM), aby skrócić czas ekspozycji uprawnień i zwiększyć widoczność do użycia w raportach i alertach. W tym celu program PIM ogranicza użytkowników do podejmowania tylko uprawnień "just in Time" (JIT) lub przez przypisanie uprawnień przez skrócenie czasu trwania, po którym uprawnienia zostaną odwołane automatycznie. 

Możesz teraz używać programu PIM z kontrolą dostępu opartą na rolach (RBAC) na platformie Azure do zarządzania, kontrolowania i monitorowania dostępu do zasobów platformy Azure. PIM może zarządzać członkostwem wbudowanych i niestandardowych ról w celu ułatwienia: 

- Włącz dostęp do zasobów platformy Azure na żądanie "just in Time"
- Automatyczne wygasanie dostępu do zasobów dla przypisanych użytkowników i grup
- Przypisywanie tymczasowego dostępu do zasobów platformy Azure na potrzeby szybkich zadań lub harmonogramów połączeń
- Otrzymuj alerty w przypadku przydzielenia dostępu do zasobów nowym użytkownikom lub grupom, gdy aktywują kwalifikujące się przypisania

Aby uzyskać więcej informacji, zobacz [co to jest Azure AD Privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md).