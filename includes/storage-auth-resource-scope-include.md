---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/17/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 74ef8270b5efcd3b7cdf756c103dcc2e1c935508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75460498"
---
Przed przypisaniem roli RBAC do podmiotu zabezpieczeń należy określić zakres dostępu, który powinien mieć podmiot zabezpieczeń. Najlepsze rozwiązania określają, że zawsze najlepiej jest przyznać tylko najwęższy możliwy zakres.

Na poniższej liście opisano poziomy, na których można mieć dostęp do zasobów obiektów blob i kolejek platformy Azure, zaczynając od najwęższego zakresu:

- **Pojedynczy pojemnik.** W tym zakresie przypisanie roli ma zastosowanie do wszystkich obiektów blob w kontenerze, a także właściwości kontenera i metadanych.
- **Pojedyncza kolejka.** W tym zakresie przypisanie roli ma zastosowanie do wiadomości w kolejce, a także właściwości kolejki i metadanych.
- **Konto magazynu.** W tym zakresie przypisanie roli ma zastosowanie do wszystkich kontenerów i ich obiektów blob lub do wszystkich kolejek i ich wiadomości.
- **grupa zasobów.** W tym zakresie przypisanie roli ma zastosowanie do wszystkich kontenerów lub kolejek we wszystkich kontach magazynu w grupie zasobów.
- **Subskrypcja.** W tym zakresie przypisanie roli ma zastosowanie do wszystkich kontenerów lub kolejek we wszystkich kontach magazynu we wszystkich grupach zasobów w ramach subskrypcji.

> [!IMPORTANT]
> Jeśli subskrypcja zawiera obszar nazw Usługi Azure DataBricks, role, które są objęte zakresem subskrypcji nie udzieli dostępu do danych obiektów blob i kolejki. Zamiast tego role zakresu w grupie zasobów, koncie magazynu lub kontenerze lub kolejce.     
