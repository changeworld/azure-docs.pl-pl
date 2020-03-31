---
title: Zarządzanie dostępem do zasobów platformy Azure za pomocą usługi Azure AD i usługi PIM
description: Dowiedz się więcej o zarządzaniu dostępem do zasobów platformy Azure przy użyciu zarządzania tożsamościami uprzywilejowanymi usługi Azure w usłudze Azure i kontroli dostępu opartej na rolach (RBAC).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77138038"
---
# <a name="manage-access-to-azure-resources-with-azure-ad-privileged-identity-management"></a>Zarządzanie dostępem do zasobów platformy Azure za pomocą zarządzania tożsamościami uprzywilejowanymi usługą Azure AD

Aby chronić konta uprzywilejowane przed złośliwymi atakami cybernetycznymi, można użyć usługi Azure Active Directory Privileged Identity Management (PIM), aby obniżyć czas narażenia uprawnień i zwiększyć wgląd w ich użycie za pomocą raportów i alertów. Pim robi to, ograniczając użytkowników tylko biorąc na ich uprawnienia "just in time" (JIT) lub przypisując uprawnienia na skrócony okres, po którym uprawnienia są automatycznie odwoływane. 

Teraz można używać usługi PIM z kontrolą dostępu opartą na rolach platformy Azure (RBAC) do zarządzania, kontrolowania i monitorowania dostępu do zasobów platformy Azure. Pim może zarządzać członkostwem ról wbudowanych i niestandardowych, aby pomóc: 

- Włącz dostęp do zasobów platformy Azure na żądanie i "w sam raz"
- Automatyczny wygaszczenie dostępu do zasobów dla przypisanych użytkowników i grup
- Przypisywanie tymczasowego dostępu do zasobów platformy Azure do szybkich zadań lub harmonogramów dyżurów
- Otrzymuj alerty, gdy nowym użytkownikom lub grupom jest przypisany dostęp do zasobów i gdy aktywują kwalifikujące się przydziały

Aby uzyskać więcej informacji, zobacz [Co to jest zarządzanie tożsamościami uprzywilejowanymi usługi Azure AD?](../active-directory/privileged-identity-management/pim-configure.md).