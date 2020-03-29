---
title: Inteligentne przycięte miniatury - Wizja komputerowa
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z generowaniem miniatur obrazów przy użyciu interfejsu API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4874910f37b49990a659b48af0cf27921c3fcd5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68945231"
---
# <a name="generating-smart-cropped-thumbnails-with-computer-vision"></a>Generowanie miniatur przyciętych inteligentnie za pomocą funkcji Wizja komputerowa

Miniatura to reprezentacja obrazu o zmniejszonym rozmiarze. Miniatury są używane do reprezentowania obrazów i innych danych w bardziej ekonomiczny, przyjazny dla układu sposób. Interfejs API przetwarzania obrazów używa inteligentnego przycinania, wraz z jego rozmiarem, w celu utworzenia intuicyjnych miniatur dla danego obrazu.

Algorytm generowania miniatur wizji komputerowej działa w następujący sposób:

1. Usuń elementy rozpraszające z obrazu i zidentyfikuj _obszar zainteresowania_&mdash;obszaru obrazu, w którym pojawia się główny obiekt(-y).
1. Przytnij obraz na podstawie zidentyfikowanego _obszaru zainteresowania_.
1. Zmień współczynnik proporcji, aby dopasować je do docelowych wymiarów miniatur.

## <a name="area-of-interest"></a>Obszar zainteresowania

Podczas przesyłania obrazu interfejs API przetwarzania obrazów jest analizowy w celu określenia *obszaru zainteresowania*. Następnie można użyć tego regionu, aby określić, jak przyciąć obraz. Operacja przycinania zawsze będzie jednak odpowiadać żądanemu współczynnikowi proporcji, jeśli zostanie określona.

Można również uzyskać współrzędne nieprzetworzone obwiedni tego samego *obszaru zainteresowania,* wywołując **areaOfInterest** INTERFEJSU API zamiast. Następnie można użyć tych informacji, aby zmodyfikować oryginalny obraz, jak chcesz.

## <a name="examples"></a>Przykłady

Wygenerowana miniatura może się znacznie różnić w zależności od tego, co określisz dla wysokości, szerokości i inteligentnego przycinania, jak pokazano na poniższej ilustracji.

![Obraz górski obok różnych konfiguracji kadrowania](./Images/thumbnail-demo.png)

W poniższej tabeli przedstawiono typowe miniatury generowane przez wizję komputerową dla przykładowych obrazów. Miniatury zostały wygenerowane dla określonej wysokości docelowej i szerokości 50 pikseli, z włączonym inteligentnym przycinaniem.

| Image (Obraz) | Miniaturę |
|-------|-----------|
|![Góra na świeżym powietrzu o zachodzie słońca, z sylwetką osoby](./Images/mountain_vista.png) | ![Miniatura Outdoor Mountain o zachodzie słońca, z sylwetką osoby](./Images/mountain_vista_thumbnail.png) |
|![Biały kwiat z zielonym tłem](./Images/flower.png) | ![Miniatura analizy wizyjnej kwiatu](./Images/flower_thumbnail.png) |
|![Kobieta na dachu budynku mieszkalnego](./Images/woman_roof.png) | ![miniatura kobiety na dachu budynku mieszkalnego](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [oznaczaniu obrazów](concept-tagging-images.md) i [kategoryzowaniu obrazów](concept-categorizing-images.md).
