---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/25/2020
ms.author: dacoulte
ms.openlocfilehash: 19b43c857058b3af50db3bc1b42ec1ae558ee884
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272666"
---
Istnieje maksymalna liczba dla każdego typu obiektu dla zasad platformy Azure. Wpis _Zakres_ oznacza subskrypcję lub [grupę zarządzania](../articles/governance/management-groups/overview.md).

| Lokalizacja | Elementy | Maksymalna liczba |
|---|---|---|
| Zakres | Definicje zasad | 500 |
| Zakres | Definicje inicjatyw | 100 |
| Dzierżawa | Definicje inicjatyw | 1000 |
| Zakres | Przypisania zasad lub inicjatyw | 100 |
| Definicja zasad | Parametry | 20 |
| Definicja inicjatywy | Zasady | 100 |
| Definicja inicjatywy | Parametry | 100 |
| Przypisania zasad lub inicjatyw | Wykluczenia (notScopes) | 400 |
| Reguła zasad | Zagnieżdżone warunki warunkowe | 512 |
| Zadanie korygowania | Resources | 1000 |
