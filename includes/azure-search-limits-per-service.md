---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: b298c87d802314bd865bd6f38c35e4361eb69f3f
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65212015"
---
Magazyn jest ograniczony przez ilość miejsca na dysku lub stały limit na *maksymalną liczbę* indeksów, dokumentu lub inne zasoby ogólne osiągnięta jako pierwsza. Poniższej tabeli opisano limity przestrzeni dyskowej. Maksymalne limity indeksów, dokumentów i innych obiektów, zobacz [limity przez zasób](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Resource | Wolny | Podstawowe<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD<sup>2</sup> | L1 | PAMIĘCI PODRĘCZNEJ L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Poziom umowy dotyczącej usług (SLA)<sup>3</sup>  |Nie |Yes |Yes |Yes |Yes |Yes |Yes |Tak |
| Magazyn na partycję |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Partycje na usługę |ND |1 |12 |12 |12 |3 |12 |12 |
| Rozmiar partycji |ND |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Repliki |ND |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> podstawowa ma jedną partycję stały. W tej warstwie jednostek wyszukiwania dodatkowe służą do przydzielania większa liczba replik dla obciążeń związanych z zapytaniami zwiększone.

<sup>2</sup> wysoka gęstość S3 ma stały limit trzech partycji, który jest niższy niż limit partycji dla usługi S3. Niższy limit partycji wynika z tego, że liczba indeksów w przypadku usługi S3 (wysoka gęstość) jest znacznie wyższa. Biorąc pod uwagę, że istnieją limity usługi zarówno dla zasobów obliczeniowych (magazyn i przetwarzanie), jak i zawartości (indeksy i dokumenty), limit zawartości jest osiągany jako pierwszy.

<sup>3</sup> umów dotyczących poziomu usług są oferowane w przypadku płatnych usług na dedykowanych zasobów. Bezpłatnych usług i w wersji zapoznawczej funkcji mają żadne umowy SLA. W przypadku usług płatnych umowy SLA zaczną obowiązywać podczas aprowizowania nadmiarowości wystarczające dla Twojej usługi. Co najmniej dwiema replikami są wymagane dla zapytań (odczyt) umowy SLA. Co najmniej trzy repliki są wymagane dla zapytań i indeksowania umowy SLA (odczyt zapis). Liczba partycji nie jest brany pod uwagę umowy SLA. 