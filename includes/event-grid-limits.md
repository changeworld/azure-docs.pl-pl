---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 05/22/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: ee80c04a6365c2cf337c4033a90df8d2993c299d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76845976"
---
Następujące limity dotyczą tematów systemu usługi Azure Event Grid i tematów niestandardowych, *a nie* domen zdarzeń.

| Zasób | Limit |
| --- | --- |
| Tematy niestandardowe na subskrypcję platformy Azure | 100 |
| Subskrypcje zdarzeń na temat | 500 |
| Szybkość publikowania dla tematu niestandardowego (ruch przychodzący) | 5000 zdarzeń na sekundę na temat |
| Publikowanie żądań | 250 na sekundę |
| Rozmiar wydarzenia | 1 MB (naładowany w wielu zdarzeniach o 64 KB) |

Następujące limity dotyczą tylko domen zdarzeń.

| Zasób | Limit |
| --- | --- |
| Tematy na domenę zdarzeń | 100 000 |
| Subskrypcje zdarzeń na temat w domenie | 500 |
| Subskrypcje zdarzeń zakresu domeny | 50 |
| Szybkość publikowania dla domeny zdarzeń (ruch przychodzący) | 5000 zdarzeń na sekundę |
| Publikowanie żądań | 250 na sekundę |
| Domeny zdarzeń na subskrypcję platformy Azure | 100 |