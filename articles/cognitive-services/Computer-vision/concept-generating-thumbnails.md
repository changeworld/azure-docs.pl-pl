---
title: Generowanie miniatur
titleSuffix: Computer Vision - Cognitive Services - Azure
description: Pojęcia dotyczące generowania miniatur obrazów przy użyciu przetwarzania obrazów w usługach Azure Cognitive Services.
services: cognitive-services
author: deken
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: 92de0c0a428c9010188131a768074234241ed604
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44725500"
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