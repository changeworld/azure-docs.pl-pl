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
ms.openlocfilehash: a50eb45291ada23f55057f3c440c5b8b23cc4bce
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58622641"
---
Przed przypisaniem roli RBAC do podmiotu zabezpieczeń, należy określić zakres dostępu, którą powinien posiadać podmiotu zabezpieczeń. Najlepsze rozwiązania określają, że zawsze najlepiej jest udzielić tylko to możliwe najwęższego zakresu.

Na poniższej liście opisano poziomów, w których możesz ograniczyć dostęp do zasobów platformy Azure obiektów blob i kolejek, począwszy od najwęższego zakresu:

- **Pojedynczy kontener.** W tym zakresie podmiotu zabezpieczeń ma dostęp do wszystkich obiektów blob w kontenerze, a także właściwości kontenera i metadanych.
- **Pojedynczej kolejki.** W tym zakresie podmiotu zabezpieczeń ma dostęp do wiadomości w kolejce, a także właściwości kolejki i metadanych.
- **Konto magazynu.** W tym zakresie podmiotu zabezpieczeń ma dostęp do wszystkich kontenerów i ich obiektów blob lub wszystkich kolejek i ich wiadomości.
- **Grupa zasobów.** W tym zakresie podmiotu zabezpieczeń ma dostęp do wszystkich kontenerów lub kolejek we wszystkich kont magazynu w grupie zasobów.
- **Subskrypcja.** W tym zakresie podmiotu zabezpieczeń ma dostęp do wszystkich kontenerów lub kolejek we wszystkich kont magazynu we wszystkich grupach zasobów w subskrypcji.

> [!IMPORTANT]
> Jeśli Twoja subskrypcja obejmuje przestrzeni nazw usługi Azure DataBricks, role przypisane w zakresie subskrypcji zostanie zablokowany udzielanie dostępu do danych obiektów blob i kolejek.