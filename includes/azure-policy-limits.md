---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 57cec39bde460c6079091490acf541761c61e003
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66119303"
---
Istnieje maksymalna liczba, dla każdego typu obiektu usługi Azure Policy. Wpis _Zakres_ oznacza subskrypcję lub [grupę zarządzania](../articles/governance/management-groups/overview.md).

| Gdzie | Elementy | Maksymalna liczba |
|---|---|---|
| Scope | Definicje zasad | 250 |
| Scope | Definicje inicjatyw | 100 |
| Dzierżawa | Definicje inicjatyw | 1000 |
| Scope | Przypisania zasad lub inicjatywy | 100 |
| Definicja zasad | Parametry | 20 |
| Definicja inicjatywy | Zasady | 100 |
| Definicja inicjatywy | Parametry | 100 |
| Przypisania zasad lub inicjatywy | Wykluczenia (notScopes) | 250 |
| Reguła zasad | Zagnieżdżone instrukcje warunkowe | 512 |
