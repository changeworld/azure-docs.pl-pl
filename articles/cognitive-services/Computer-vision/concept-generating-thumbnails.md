---
title: Generowanie miniatur — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia dotyczące generowania miniatur obrazów przy użyciu interfejsu API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 9cb82b40d1fbec513b0219f26d1959fbd7f64570
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2018
ms.locfileid: "49343966"
---
# <a name="generating-thumbnails"></a>Generowanie miniatur

Miniatura jest reprezentację mały obraz w pełnym rozmiarze. Zależeć od urządzeń, takich jak telefony, tablety i komputery utworzyć na potrzeby innego użytkownika, układy środowiska i rozmiary miniatur. Za pomocą inteligentne przycinanie, ta funkcja przetwarzania obrazów pomaga w rozwiązaniu problemu.

Po przekazaniu obrazu, generuje wysokiej jakości miniaturę przetwarzania obrazów, a następnie analizuje obiekty na obrazie do identyfikowania *regionu zainteresowania* (ROI). Można go opcjonalnie przyciąć obraz zgodnie z wymaganiami, o zwrot z inwestycji. Wygenerowane miniatury można użyć dowolnego współczynnik proporcji, która różni się od proporcji niż oryginalny obraz, spełniają Twoje wymagania.

Algorytm miniatury działa w następujący sposób:

1. Usuwa zbędne elementy z obrazu i identyfikuje główny obiekt regionu zainteresowania.
2. Przycina obraz na podstawie zidentyfikowanych regionu zainteresowania.
3. Zmienia współczynnika proporcji do rozmiaru miniatur docelowego.

Miniatury wygenerowanej mogą się znacznie zmieniać w zależności od tego, określ wysokość, szerokość i inteligentne przycinanie jak pokazano na poniższej ilustracji.

![Miniatury](./Images/thumbnail-demo.png)

## <a name="thumbnail-generation-examples"></a>Generowanie miniatur przykłady

W poniższej tabeli przedstawiono typowe miniatury generowane przez przetwarzania obrazów, na przykład obrazy. Wygenerowane miniatury wysokości określonego obiektu docelowego i szerokość 50 pikseli i inteligentne przycinanie włączone.

| Image (Obraz) | Miniatura |
|-------|-----------|
|![Aktywność na świeżym powietrzu górski](./Images/mountain_vista.png) | ![Górski możliwością miniatury](./Images/mountain_vista_thumbnail.png) |
|![Wizja analizowanie Kwiatek](./Images/flower.png) | ![Wizja analizowanie Kwiatek miniatury](./Images/flower_thumbnail.png) |
|![Kobieta dachu](./Images/woman_roof.png) | ![Kobieta dachu miniatury](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>Kolejne kroki

Pojęcia dotyczące [tagowanie obrazów](concept-tagging-images.md) i [kategoryzowanie obrazów](concept-categorizing-images.md).