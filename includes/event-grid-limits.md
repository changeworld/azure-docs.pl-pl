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
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845976"
---
Poniższe limity mają zastosowanie do tematów systemu Azure Event Grid i tematów niestandardowych, a *nie* domen zdarzeń.

| Zasób | Limit |
| --- | --- |
| Tematy niestandardowe na subskrypcję platformy Azure | 100 |
| Subskrypcje zdarzeń na temat | 500 |
| Wskaźnik publikowania dla tematu niestandardowego (ruch przychodzący) | 5 000 zdarzeń na sekundę na temat |
| Publikuj żądania | 250 na sekundę |
| Rozmiar zdarzenia | 1 MB (opłata naliczana jako wiele zdarzeń 64 KB) |

Poniższe limity mają zastosowanie tylko do domen zdarzeń.

| Zasób | Limit |
| --- | --- |
| Tematy na domenę zdarzeń | 100 000 |
| Subskrypcje zdarzeń na temat w domenie | 500 |
| Subskrypcje zdarzeń zakresu domeny | 50 |
| Częstotliwość publikowania dla domeny zdarzeń (ruch przychodzący) | 5 000 zdarzeń na sekundę |
| Publikuj żądania | 250 na sekundę |
| Domeny zdarzeń na subskrypcję platformy Azure | 100 |