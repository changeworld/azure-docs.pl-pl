---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 04/04/2018
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: b4062aab5a453505ef4586f422a124d4bbf715cb
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
Magazyn jest ograniczane przez miejsca na dysku lub nienaruszalne ograniczenie na *maksymalną liczbę* indeksów, dokumentu lub innych zasobów wysokiego poziomu, zależnie od zostanie osiągnięty jako pierwszy. Poniższej tabeli opisano limity magazynu. Maksymalna limitów indeksów, dokumentów i innych obiektów, zobacz [limity przez zasób](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Zasób | Bezpłatna | Podstawowe&nbsp;<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>2</sup> |
| -------- | --- | --- | --- | --- | --- | --- |
| Umowa dotycząca poziomu (SLA) usług <sup>3</sup>  |Nie |Yes |Yes |Yes |Yes |Yes |
| Magazyn na partycję |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |
| Partycje na usługę |ND |1 |12 |12 |12 |3 |
| Rozmiar partycji |ND |2 GB |25 GB |100 GB |200 GB |200 GB |
| Repliki |ND |3 |12 |12 |12 |12 |

<sup>1</sup> basic ma jedną partycję stałym. W tej warstwie SUs dodatkowe służą do przydzielania więcej replik dla obciążeń zwiększona zapytania.

<sup>2</sup> Usługa S3 (wysoka gęstość) ma stały limit 3 partycji, który jest niższy niż limit partycji dla usługi S3. Niższy limit partycji wynika z tego, że liczba indeksów w przypadku usługi S3 (wysoka gęstość) jest znacznie wyższa. Biorąc pod uwagę, że istnieją limity usługi zarówno dla zasobów obliczeniowych (magazyn i przetwarzanie), jak i zawartości (indeksy i dokumenty), limit zawartości jest osiągany jako pierwszy.

<sup>3</sup> umów dotyczących poziomu usług (SLA) są oferowane rozliczeniowy usług zasobów dedykowanych. Bezpłatne funkcji usług i Podgląd ma nie umowy dotyczącej poziomu usług. Dla usług rozliczeniowy umowy SLA zaczną obowiązywać podczas obsługi administracyjnej nadmiarowość wystarczające dla Twojej usługi. Co najmniej dwa repliki są wymagane do umowy SLA kwerendy (odczyt). Co najmniej trzech repliki są wymagane dla zapytań i indeksowania SLA (odczytu i zapisu). Liczba partycji nie jest brany pod uwagę umowy dotyczącej poziomu usług. 