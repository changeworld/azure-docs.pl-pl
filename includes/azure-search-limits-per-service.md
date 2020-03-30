---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: search
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: 179f525b7b6a7e51889b14b66df6c537ca56bd75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272852"
---
Magazyn jest ograniczony przez miejsce na dysku lub przez twardy limit *maksymalnej liczby* indeksów, dokumentów lub innych zasobów wysokiego poziomu, w zależności od tego, co nastąpi wcześniej. Poniższa tabela dokumentuje limity przechowywania. Aby uzyskać maksymalne limity indeksów, dokumentów i innych obiektów, zobacz [Limity według zasobów](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Zasób | Bezpłatna | Podstawa<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD<sup>2</sup> | L1 (właso. | L2 (l2) |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Umowa dotyczącej poziomu usług (SLA)<sup>3</sup>  |Nie |Tak |Tak |Tak |Tak |Tak |Tak |Tak |
| Magazyn na partycję |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Partycje na usługę |Nie dotyczy |1 |12 |12 |12 |3 |12 |12 |
| Rozmiar partycji |Nie dotyczy |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Repliki |Nie dotyczy |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> Basic ma jedną stałą partycję. W tej warstwie dodatkowe jednostki wyszukiwania są używane do przydzielania większej liczby replik dla zwiększonych obciążeń zapytań.

<sup>2</sup> S3 HD ma twardy limit trzech partycji, który jest niższy niż limit partycji dla S3. Niższy limit partycji wynika z tego, że liczba indeksów w przypadku usługi S3 (wysoka gęstość) jest znacznie wyższa. Biorąc pod uwagę, że istnieją limity usługi zarówno dla zasobów obliczeniowych (magazyn i przetwarzanie), jak i zawartości (indeksy i dokumenty), limit zawartości jest osiągany jako pierwszy.

<sup>3</sup> Umowy dotyczące poziomu usług są oferowane w przypadku usług podlegania rozliczaniom w zakresie dedykowanych zasobów. Bezpłatne usługi i funkcje podglądu nie mają umowy SLA. W przypadku usług podlegania rozliczalności ujedne są izby SLA, które mają obowiązywać po udostępnieniu wystarczającej nadmiarowości dla usługi. Co najmniej dwie repliki są wymagane dla zapytań (odczytu) łasce. Co najmniej trzy repliki są wymagane dla zapytań i indeksowania (odczytu i zapisu) DL. Liczba partycji nie jest rozważana umowy SLA. 