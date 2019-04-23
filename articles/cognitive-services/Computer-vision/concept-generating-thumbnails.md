---
title: Generowanie miniatur — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia dotyczące generowania miniatur obrazów przy użyciu interfejsu API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 8bbc86f5c6fe0f30968a1ba5bd5fa28160ef6963
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60002446"
---
# <a name="generating-smart-cropped-thumbnails-with-computer-vision"></a>Generowanie miniatur przycięte inteligentnej przy użyciu przetwarzania obrazów

Reprezentacja zmniejszyć rozmiar obrazu jest miniatura. Miniatury są używane do reprezentowania obrazy i inne dane w sposób bardziej ekonomiczne, przyjazne dla układu. Interfejs API przetwarzania obrazów używa inteligentne przycinanie, wraz z zmiany rozmiaru obrazu, w celu utworzenia intuicyjne miniatury dla danego obrazu.

Generowanie miniatur algorytm przetwarzania obrazów działa w następujący sposób:

1. Usuń zbędne elementy z obrazu i zidentyfikować _obszar zainteresowania_&mdash;obszar obrazu, w którym pojawia się obiekty główne.
1. Przytnij obraz w oparciu o wskazywanego przez nią _obszar zainteresowania_.
1. Zmienianie współczynnika proporcji do rozmiaru miniatur docelowego.

## <a name="area-of-interest"></a>Obszar zainteresowania

Po przekazaniu obrazu interfejs API przetwarzania obrazów analizuje je, aby określić *obszar zainteresowania*. Go następnie użyty następujący region, aby określić, jak przyciąć obraz. Przycinania operacji, jednak będzie zawsze zgodna żądaną współczynnik proporcji Jeśli mapa została określona.

Można również uzyskać pierwotne współrzędne pola ograniczenia tego samego *obszar zainteresowania* przez wywołanie metody **areaOfInterest** API zamiast tego. Można następnie użyć tych informacji do modyfikowania oryginalnego obrazu, ale chcesz.

## <a name="examples"></a>Przykłady

Miniatury wygenerowanej mogą się znacznie zmieniać w zależności od tego, określ wysokość, szerokość i inteligentne przycinanie jak pokazano na poniższej ilustracji.

![Obraz górski obok różne konfiguracje przycinania](./Images/thumbnail-demo.png)

W poniższej tabeli przedstawiono typowe miniatury generowane przez przetwarzania obrazów, na przykład obrazy. Wygenerowane miniatury wysokości określonego obiektu docelowego i szerokość 50 pikseli i inteligentne przycinanie włączone.

| Image | Miniatura |
|-------|-----------|
|![Górski możliwością zachodzie słońca z nakładające osoby](./Images/mountain_vista.png) | ![Miniatura z możliwością górski zachodzie słońca z nakładające osoby](./Images/mountain_vista_thumbnail.png) |
|![Białe Kwiatek zielonym tłem](./Images/flower.png) | ![Wizja analizowanie Kwiatek miniatury](./Images/flower_thumbnail.png) |
|![Kobieta na dachu budynku apartamentu](./Images/woman_roof.png) | ![Miniatura kobieta na dachu budynku apartamentu](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [tagowanie obrazów](concept-tagging-images.md) i [kategoryzowanie obrazów](concept-categorizing-images.md).
