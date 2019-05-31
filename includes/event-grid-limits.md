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
ms.openlocfilehash: 3f94481e6a8550479788d92c744327e1dc3b58c4
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66376918"
---
Poniższe limity mają zastosowanie do usługi Azure Event Grid system tematów i tematy niestandardowe *nie* domen zdarzeń.

| Resource | Limit |
| --- | --- |
| Tematy niestandardowe na subskrypcję platformy Azure | 100 |
| Subskrypcje zdarzeń przypadających na temat | 500 |
| Publikowanie współczynnik dla tematu niestandardowego (ruch przychodzący) | 5000 zdarzeń na sekundę na temat |
| Publikowanie żądań | 250 na sekundę |
| Rozmiar zdarzenia | Obsługa 64 KB ogólnie rzecz biorąc dostępność (GA). Obsługa 1 MB jest obecnie w wersji zapoznawczej. |

Następujące limity dotyczą tylko domeny zdarzeń.

| Resource | Limit |
| --- | --- |
| Tematy dla zdarzeń domeny | 1000 połączeń w publicznej wersji zapoznawczej |
| Subskrypcje zdarzeń dla tematu w domenie | 50 publicznej wersji zapoznawczej |
| Subskrypcje zdarzeń zakres domeny | 50 publicznej wersji zapoznawczej |
| Publikowanie stawki dla zdarzeń domeny (ruch przychodzący) | 5000 zdarzeń na sekundę w okresie publicznej wersji zapoznawczej |
| Publikowanie żądań | 250 na sekundę |