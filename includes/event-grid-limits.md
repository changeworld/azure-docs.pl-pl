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
ms.openlocfilehash: 890095718833c90a6764d0799d2081b393b1d0bd
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887820"
---
Poniższe limity mają zastosowanie do tematów systemu Azure Event Grid i tematów niestandardowych, a *nie* domen zdarzeń.

| Zasób | Limit |
| --- | --- |
| Tematy niestandardowe na subskrypcję platformy Azure | 100 |
| Subskrypcje zdarzeń na temat | 500 |
| Wskaźnik publikowania dla tematu niestandardowego (ruch przychodzący) | 5 000 zdarzeń na sekundę na temat |
| Publikuj żądania | 250 na sekundę |
| Rozmiar zdarzenia | Obsługa 64 KB ogólnie dostępna (GA). Obsługa 1 MB jest obecnie dostępna w wersji zapoznawczej. |

Poniższe limity mają zastosowanie tylko do domen zdarzeń.

| Zasób | Limit |
| --- | --- |
| Tematy na domenę zdarzeń | 100 000 |
| Subskrypcje zdarzeń na temat w domenie | 500 |
| Subskrypcje zdarzeń zakresu domeny | 50 |
| Częstotliwość publikowania dla domeny zdarzeń (ruch przychodzący) | 5 000 zdarzeń na sekundę |
| Publikuj żądania | 250 na sekundę |
| Domeny zdarzeń na subskrypcję platformy Azure | 100 |