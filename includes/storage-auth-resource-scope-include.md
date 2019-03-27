---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/21/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ad4b244b58d741ad45463297df5bd358f3ae9918
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58450027"
---
Przed przypisaniem roli RBAC do podmiotu zabezpieczeń, należy określić zakres dostępu, którą powinien posiadać podmiotu zabezpieczeń. Najlepsze rozwiązania określają, że zawsze najlepiej jest udzielić tylko to możliwe najwęższego zakresu.

Na poniższej liście opisano poziomów, w których możesz ograniczyć dostęp do zasobów platformy Azure obiektów blob i kolejek, począwszy od najwęższego zakresu:

- **Pojedynczy kontener.** W tym zakresie podmiotu zabezpieczeń ma dostęp do wszystkich obiektów blob w kontenerze, a także właściwości kontenera i metadanych.
- **Pojedynczej kolejki.** W tym zakresie podmiotu zabezpieczeń ma dostęp do wiadomości w kolejce, a także właściwości kolejki i metadanych.
- **Konto magazynu.** W tym zakresie podmiotu zabezpieczeń ma dostęp do wszystkich kontenerów i ich obiektów blob lub wszystkich kolejek i ich wiadomości.
- **Grupa zasobów.** W tym zakresie podmiotu zabezpieczeń ma dostęp do wszystkich kontenerów lub kolejek we wszystkich kont magazynu w grupie zasobów.
- **Subskrypcja.** W tym zakresie podmiotu zabezpieczeń ma dostęp do wszystkich kontenerów lub kolejek we wszystkich kont magazynu we wszystkich grupach zasobów w subskrypcji.
