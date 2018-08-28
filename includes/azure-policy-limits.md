---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/16/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 0e5d1214a8e1af8299cb40d1a3b31ff6eafc8c5c
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/17/2018
ms.locfileid: "40185367"
---
Istnieje maksymalna liczba dla każdego typu obiektu usługi Azure Policy. Wpis _Zakres_ oznacza subskrypcję lub [grupę zarządzania](../articles/azure-resource-manager/management-groups-overview.md).

| Lokalizacja | Elementy | Maksymalna liczba |
|---|---|---|
| Zakres | Definicje zasad | 250 |
| Zakres | Definicje inicjatyw | 100 |
| Dzierżawa | Definicje inicjatyw | 1000 |
| Zakres | Przypisania zasad/inicjatyw | 100 |
| Definicja zasad | Parametry | 20 |
| Definicja inicjatywy | Zasady | 100 |
| Definicja inicjatywy | Parametry | 100 |
| Przypisania zasad/inicjatyw | Wykluczenia (notScopes) | 100 |
| Reguła zasad | Zagnieżdżone instrukcje warunkowe | 512 |
