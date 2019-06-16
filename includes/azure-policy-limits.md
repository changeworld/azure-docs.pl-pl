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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133366"
---
Istnieje maksymalna liczba, dla każdego typu obiektu usługi Azure Policy. Wpis _Zakres_ oznacza subskrypcję lub [grupę zarządzania](../articles/governance/management-groups/overview.md).

| Lokalizacja | Elementy | Maksymalna liczba |
|---|---|---|
| Scope | Definicje zasad | 500 |
| Scope | Definicje inicjatyw | 100 |
| Dzierżawa | Definicje inicjatyw | 1000 |
| Scope | Przypisania zasad lub inicjatywy | 100 |
| Definicja zasad | Parametry | 20 |
| Definicja inicjatywy | Zasady | 100 |
| Definicja inicjatywy | Parametry | 100 |
| Przypisania zasad lub inicjatywy | Wykluczenia (notScopes) | 400 |
| Reguła zasad | Zagnieżdżone instrukcje warunkowe | 512 |
