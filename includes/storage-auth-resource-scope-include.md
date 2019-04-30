---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/28/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 518c57bc3327511b70deef143826f2a1b9df8639
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483616"
---
Przed przypisaniem roli RBAC do podmiotu zabezpieczeń, należy określić zakres dostępu, którą powinien posiadać podmiotu zabezpieczeń. Najlepsze rozwiązania określają, że zawsze najlepiej jest udzielić tylko to możliwe najwęższego zakresu.

Na poniższej liście opisano poziomów, w których możesz ograniczyć dostęp do zasobów platformy Azure obiektów blob i kolejek, począwszy od najwęższego zakresu:

- **Pojedynczy kontener.** W tym zakresie przypisania roli ma zastosowanie do wszystkich obiektów blob w kontenerze, a także właściwości kontenera i metadanych.
- **Pojedynczej kolejki.** W tym zakresie przypisania roli ma zastosowanie do wiadomości w kolejce, a także właściwości kolejki i metadanych.
- **Konto magazynu.** W tym zakresie przypisania roli dotyczy wszystkich kontenerów i ich obiektów blob lub wszystkich kolejek i ich wiadomości.
- **Grupa zasobów.** W tym zakresie przypisania roli ma zastosowanie do wszystkich kontenerów lub kolejek we wszystkich kont magazynu w grupie zasobów.
- **Subskrypcja.** W tym zakresie przypisania roli ma zastosowanie do wszystkich kontenerów lub kolejek we wszystkich kont magazynu we wszystkich grupach zasobów w subskrypcji.

> [!IMPORTANT]
> Jeśli Twoja subskrypcja obejmuje przestrzeni nazw usługi Azure DataBricks, role przypisane w zakresie subskrypcji zostanie zablokowany udzielanie dostępu do danych obiektów blob i kolejek.
