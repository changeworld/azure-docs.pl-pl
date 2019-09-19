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
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "67183611"
---
Magazyn jest ograniczony przez miejsce na dysku lub stały limit *maksymalnej liczby* indeksów, dokumentu lub innych zasobów wysokiego poziomu, zależnie od tego, co nastąpi wcześniej. W poniższej tabeli przedstawiono limity magazynu. Aby uzyskać maksymalne limity dotyczące indeksów, dokumentów i innych obiektów, zobacz [ograniczenia według zasobów](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Resource | Wolne | Podstawowa<sup>1</sup> | S1 | S2 | S3 | WYJŚCIE&nbsp;S3 HD<sup>2</sup> | L1 | PAMIĘCI PODRĘCZNEJ L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Umowa dotycząca poziomu usług (SLA)<sup>3</sup>  |Nie |Yes |Yes |Yes |Yes |Yes |Yes |Tak |
| Magazyn na partycję |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Partycje na usługę |ND |1 |12 |12 |12 |3 |12 |12 |
| Rozmiar partycji |ND |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Repliki |ND |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> podstawowa ma jedną stałą partycję. W tej warstwie dodatkowe jednostki wyszukiwania są używane do przydzielania większej liczby replik w celu zwiększenia obciążeń związanych z zapytaniami.

<sup>2</sup> S3 HD ma sztywny limit trzech partycji, który jest mniejszy niż limit partycji dla S3. Niższy limit partycji wynika z tego, że liczba indeksów w przypadku usługi S3 (wysoka gęstość) jest znacznie wyższa. Biorąc pod uwagę, że istnieją limity usługi zarówno dla zasobów obliczeniowych (magazyn i przetwarzanie), jak i zawartości (indeksy i dokumenty), limit zawartości jest osiągany jako pierwszy.

<sup>3</sup> umowy dotyczące poziomu usług są oferowane dla usług rozliczanych w dedykowanych zasobach. Bezpłatne usługi i funkcje wersji zapoznawczej nie mają umowy SLA. W przypadku usług rozliczanych umowy SLA zacznie obowiązywać po wprowadzeniu wystarczającej nadmiarowości dla usługi. Co najmniej dwie repliki są wymagane dla zapytania (Read) umowy SLA. Co najmniej trzy repliki są wymagane do wykonywania zapytań i indeksowania (do odczytu i zapisu) umowy SLA. Liczba partycji nie jest objęta umową SLA. 