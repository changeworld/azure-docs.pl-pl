---
title: Zarządzanie dostępem do zasobów platformy Azure z zarządzania tożsamości uprzywilejowanych (PIM)
description: Informacje o używaniu opartej na rolach zarządzania dostępem w PIM dostęp do zasobów platformy Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: ba06b8dd-4a74-4bda-87c7-8a8583e6fd14
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: fb0a1ff3821efd7114b509b72e143d5240b61b4c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="manage-access-to-azure-resources-with-privileged-identity-management"></a>Zarządzanie dostępem do zasobów platformy Azure z Privileged Identity Management

Aby chronić kont uprzywilejowanych przed złośliwymi atakami ataków, Azure Active Directory Privileged tożsamości zarządzania (PIM) służy do zmniejszenia czasu ekspozycji uprawnień i zwiększyć Twojej wgląd w ich za pośrednictwem raporty i alerty. PIM robi to poprzez ograniczenie użytkowników do wykonywania tylko na swoje uprawnienia "just in time" (JIT) lub przez przypisanie uprawnień skróconą czas, po którym uprawnienia są automatycznie odwołany. 

Teraz można PIM kontroli dostępu based (RBAC) Zarządzanie, sterowanie i monitorowanie dostępu do zasobów platformy Azure. PIM można zarządzać członkostwem ról wbudowanych i niestandardowych ułatwiają: 

- Włącz na żądanie, "just in time" dostęp do zasobów platformy Azure
- Wygaśnie, dostęp do zasobów automatycznie dla przypisanych użytkowników i grup
- Przypisz tymczasowego dostępu do zasobów platformy Azure dla zadania szybkiego lub harmonogramów na wywołanie
- Alerty, gdy nowych użytkowników lub grup są przypisane dostęp do zasobów i aktywują przypisania kwalifikujących się

Aby uzyskać więcej informacji, zobacz [Overview of Role-Based kontroli dostępu w usłudze Azure PIM](../active-directory/privileged-identity-management/azure-pim-resource-rbac.md).