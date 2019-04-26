---
title: Zarządzanie dostępem do zasobów platformy Azure za pomocą usługi Azure AD Privileged Identity Management (PIM)
description: Więcej informacji na temat zarządzania dostępem do zasobów platformy Azure przy użyciu usługi Azure Active Directory Privileged Identity Management (PIM) i kontrola dostępu oparta na rolach (RBAC).
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
ms.openlocfilehash: 757068034868744b408c9402b521a0e4c73950f7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60344618"
---
# <a name="manage-access-to-azure-resources-with-azure-ad-privileged-identity-management"></a>Zarządzanie dostępem do zasobów platformy Azure z usługą Azure AD Privileged Identity Management

Aby chronić uprzywilejowanych kont przed złośliwymi atakami cybernetycznymi, Azure Active Directory Privileged Identity Management (PIM) służy do zmniejszyć czas narażenia uprawnień i lepszy wgląd w ich używanie przez raporty i alerty. Usługa PIM robi to poprzez ograniczenie użytkowników do uwzględnienia tylko swoje uprawnienia "dokładnie na czas" (JIT), lub przypisując uprawnienia skróconą czas, po upływie którego uprawnienia są odwoływane automatycznie. 

Można teraz używać aplikacji PIM przy użyciu kontroli dostępu opartej na rolach na platformie Azure (RBAC), zarządzanie, sterowanie i monitorowanie dostępu do zasobów platformy Azure. PIM można zarządzać członkostwem ról wbudowanych i niestandardowych ułatwią Ci: 

- Włącz na żądanie, "just in time" dostęp do zasobów platformy Azure
- Unieważnij dostęp zasobów automatycznie dla przypisanych użytkowników i grup
- Przypisz tymczasowego dostępu do zasobów platformy Azure dla szybkich zadań lub harmonogramów dyżurów
- Otrzymuj alerty, gdy nowych użytkowników lub grup są przypisywane dostęp do zasobów, a podczas aktywacji z kwalifikującymi się przypisaniami

Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure AD Privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md).