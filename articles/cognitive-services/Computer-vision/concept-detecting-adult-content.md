---
title: Opis zawartości erotycznej i przeznaczonej dla osób dorosłych — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia dotyczące wykrywania zawartości erotycznej i przeznaczonej dla osób dorosłych w obrazach za pomocą interfejsu APi przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 64db05e5e40b76d219ea0e3214c20297f32da4b5
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55861276"
---
# <a name="detecting-adult-and-racy-content"></a>Wykrywanie zawartości dla dorosłych

Spośród różnych kategorii wizualnych możemy wyróżnić grupę przeznaczoną dla zawartości dla dorosłych i o charakterze erotycznym. Umożliwia ona wykrywanie materiałów dla dorosłych i ogranicza wyświetlanie obrazów zawierających zawartość o charakterze erotycznym. Filtr umożliwiający wykrywanie zawartości dla dorosłych można ustawiać na ruchomej skali, aby dostosować go do preferencji użytkownika.

## <a name="defining-adult-and-racy-content"></a>Definiowanie zawartości dla dorosłych

Spośród różnych funkcji visual objętych [analizowanie obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa), treści dla dorosłych funkcję visual włącza wykrywanie obrazów dla dorosłych. "W treści dla dorosłych" obrazy są definiowane jako, które mają pornograficznej charakter często przedstawiać nagość i seksualnym czynności. "Erotycznej" obrazy są definiowane jako obrazy, które są przekleństwa dwuznaczne z natury i często zawierają mniej zawartość dla dorosłych przekleństwa niż obrazy oznaczone jako "Zawartość dla dorosłych." Typ treści dla dorosłych funkcji visual powszechnie umożliwia ograniczyć wyświetlanie obrazów zawierających przekleństwa dwuznaczne i jawnie seksualnym zawartości.

## <a name="identifying-adult-and-racy-content"></a>Identyfikowanie zawartości dla dorosłych

Metoda analizowanie obrazów zwraca dwie właściwości `isAdultContent` i `isRacyContent`, w odpowiedzi JSON metody, aby wskazać, odpowiednio, zawartości erotycznej i przeznaczonej dla osób dorosłych. Obie te właściwości zwracają wartość logiczną PRAWDA lub FAŁSZ. Metoda zwraca również dwie właściwości `adultScore` i `racyScore`, które reprezentują, odpowiednio, wyniki ufności identyfikowanie zawartości dla dorosłych. Filtr zaufania dla dorosłych wykrywania zawartości można ustawić na ruchomej skali umożliwiających swoje preferencje w oparciu o określonego scenariusza.

## <a name="next-steps"></a>Kolejne kroki

Pojęcia dotyczące [wykrywanie specyficznego dla domeny zawartości](concept-detecting-domain-content.md) i [wykrywanie twarzy](concept-detecting-faces.md).
