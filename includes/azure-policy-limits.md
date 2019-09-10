---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/05/2019
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: ea87e51e66985f860cd6d10595a32facde1dc639
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "67133366"
---
Dla każdego typu obiektu dla Azure Policy istnieje maksymalna liczba. Wpis _Zakres_ oznacza subskrypcję lub [grupę zarządzania](../articles/governance/management-groups/overview.md).

| Gdzie | Elementy | Maksymalna liczba |
|---|---|---|
| Scope | Definicje zasad | 500 |
| Scope | Definicje inicjatyw | 100 |
| Dzierżawa | Definicje inicjatyw | 1000 |
| Scope | Zasady lub przypisania inicjatywy | 100 |
| Definicja zasad | Parametry | 20 |
| Definicja inicjatywy | Zasady | 100 |
| Definicja inicjatywy | Parametry | 100 |
| Zasady lub przypisania inicjatywy | Wykluczenia (notScopes) | 400 |
| Reguła zasad | Zagnieżdżone uwarunkowania | 512 |
