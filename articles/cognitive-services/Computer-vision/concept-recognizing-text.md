---
title: Rozpoznawanie tekstu drukowanego lub ręcznie — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z rozpoznawanie tekstu drukowanego i pisma odręcznego na obrazach za pomocą interfejsu API przetwarzania obrazów.
services: cognitive-services
author: deken
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: 49cba0e9b6958beb07b6f074e6dc748679514525
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "45985315"
---
# <a name="recognizing-printed-and-handwritten-text"></a>Rozpoznawanie tekstu drukowanego i pisma odręcznego

Przetwarzanie obrazów, można wykryć i wyodrębnianie tekstu drukowanych lub pisma odręcznego z obrazów różne obiekty na różnych powierzchniach i tłach, takich jak przychody, plakaty, wizytówki, litery i tablic.

Rozpoznawanie tekstu oszczędza czas i nakład pracy. Można zwiększyć produktywność biorąc obrazu, tekstu zamiast jego przepisywania. Rozpoznawanie tekstu pozwala na przekształcanie notatek do. Ta digitization pozwala na implementowanie szybkiego i prostego wyszukiwania. Zmniejsza ona również nieład związany z dużą ilością papieru.

## <a name="text-recognition-requirements"></a>Wymagania dotyczące rozpoznawania tekstu

Przetwarzanie obrazów może rozpoznać tekstu drukowanego i pisma odręcznego w obrazach, które spełniają następujące wymagania:

- Obraz, który przedstawia w formacie JPEG, PNG lub BMP
- Rozmiar pliku obrazu musi być mniejszy niż 4 megabajty (MB)
- Wymiary obrazu musi należeć do zakresu od 40 x 40 i 3200 x 3200 pikseli

> [!NOTE]
> Ta technologia jest obecnie dostępna w wersji zapoznawczej i jest dostępna tylko dla tekstu w języku angielskim.

## <a name="next-steps"></a>Kolejne kroki

Pojęcia dotyczące [możliwości wyodrębniania tekstu za pomocą oprogramowania OCR](concept-extracting-text-ocr.md).