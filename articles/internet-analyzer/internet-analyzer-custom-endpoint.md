---
title: Tworzenie niestandardowego punktu końcowego | Dokumenty firmy Microsoft
description: W tym artykule dowiesz się, jak skonfigurować niestandardowy punkt końcowy do pomiaru za pomocą zasobu analizatora internetowego.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: quickstart
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 6a781e767393f35a1ec22f195ba787e3018d8d1e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76713086"
---
# <a name="measure-custom-endpoints-to-evaluate-in-your-internet-analyzer-tests"></a>Mierzenie niestandardowych punktów końcowych do oceny w testach analizatora internetowego 

W tym artykule pokazano, jak skonfigurować niestandardowy punkt końcowy do pomiaru w ramach testów analizatora internetowego. Niestandardowe punkty końcowe pomagają w ocenie obciążeń lokalnych, obciążeń uruchomionych u innych dostawców chmury i niestandardowych konfiguracji platformy Azure.  Porównywanie dwóch niestandardowych punktów końcowych w jednym teście jest możliwe, jeśli jeden punkt końcowy jest zasobem platformy Azure. Aby uzyskać więcej informacji na temat analizatora internetowego, zobacz [omówienie](internet-analyzer-overview.md). 

> [!IMPORTANT]
> Ten podgląd publiczny nie jest objęty umową dotyczącą poziomu usług i nie należy korzystać z niego w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Przed rozpoczęciem

Upewnij się, że skonfigurowano zasób analizatora internetowego i wybierz opcję "Niestandardowy punkt końcowy". Analizator internetowy zakłada, że niestandardowy punkt końcowy jest dostępny w Internecie. Aby uzyskać więcej informacji, zobacz [Tworzenie zasobu analizatora internetowego](internet-analyzer-create-test-portal.md).


## <a name="create-custom-endpoint"></a>Tworzenie niestandardowego punktu końcowego

1. Pobierz przezroczysty obraz testowy o jednym pikselu [tutaj](https://fpc.msedge.net/apc/trans.gif). Ten obraz jednopikselowy jest zasobem pobranym przez klienta JavaScript w celu pomiaru wydajności.
2. W niestandardowej aplikacji sieci web wdrożyć obraz testowy w ścieżce publicznie dostępne. Ścieżka powinna działać za pośrednictwem protokołu HTTPS. 
3. Skopiuj pełny niestandardowy adres https://contoso.com/test/trans.gif) URL punktu końcowego (np. do niestandardowego pola punktu końcowego podczas tworzenia testu.

## <a name="next-steps"></a>Następne kroki

Przeczytaj często zadawane [pytania dotyczące analizatora internetowego](internet-analyzer-faq.md)

