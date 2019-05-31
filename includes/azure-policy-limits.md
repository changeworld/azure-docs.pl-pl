---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/30/2019
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 7907504401f4b47aafe6032ea895d9647e6c303c
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420676"
---
Istnieje maksymalna liczba, dla każdego typu obiektu usługi Azure Policy. Wpis _Zakres_ oznacza subskrypcję lub [grupę zarządzania](../articles/governance/management-groups/overview.md).

| Lokalizacja | Elementy | Maksymalna liczba |
|---|---|---|
| Scope | Definicje zasad | 250 |
| Scope | Definicje inicjatyw | 100 |
| Dzierżawa | Definicje inicjatyw | 1000 |
| Scope | Przypisania zasad lub inicjatywy | 100 |
| Definicja zasad | Parametry | 20 |
| Definicja inicjatywy | Zasady | 100 |
| Definicja inicjatywy | Parametry | 100 |
| Przypisania zasad lub inicjatywy | Wykluczenia (notScopes) | 400 |
| Reguła zasad | Zagnieżdżone instrukcje warunkowe | 512 |
