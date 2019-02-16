---
title: Rozpoznawanie tekstu drukowanego, pisma odręcznego - przetwarzania obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z rozpoznawanie tekstu drukowanego i pisma odręcznego na obrazach za pomocą interfejsu API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 48ce15a11c3e3282535420f3e1bb1915276d70f5
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2019
ms.locfileid: "56313181"
---
# <a name="recognizing-printed-and-handwritten-text"></a>Rozpoznawanie tekstu drukowanego i pisma odręcznego

Przetwarzanie obrazów, można wykryć i wyodrębnianie tekstu drukowanych lub pisma odręcznego z obrazów różne obiekty na różnych powierzchniach i tłach, takich jak przychody, plakaty, wizytówki, litery i tablic.

Funkcja rozpoznawania tekstu jest bardzo podobny do [optyczne rozpoznawanie znaków (OCR)](concept-extracting-text-ocr.md), ale w przeciwieństwie do optyczne rozpoznawanie znaków wykonuje asynchronicznie i używa aktualizacji modeli rozpoznawania.

> [!NOTE]
> Ta technologia jest obecnie dostępna w wersji zapoznawczej i jest dostępna tylko dla tekstu w języku angielskim.

## <a name="text-recognition-requirements"></a>Wymagania dotyczące rozpoznawania tekstu

Przetwarzanie obrazów może rozpoznać tekstu drukowanego i pisma odręcznego w obrazach, które spełniają następujące wymagania:

- Obraz, który przedstawia w formacie JPEG, PNG lub BMP
- Rozmiar pliku obrazu musi być mniejszy niż 4 megabajty (MB)
- Wymiary obrazu musi należeć do zakresu od 50 x 50 i 4200 x 4200 pikseli

## <a name="next-steps"></a>Kolejne kroki

Zobacz [rozpoznawanie tekstu, dokumentów referencyjnych](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) Aby dowiedzieć się więcej.