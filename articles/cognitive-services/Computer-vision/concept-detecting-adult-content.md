---
title: Opisujące zawartość dla dorosłych — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia dotyczące wykrywania zawartości erotycznej i przeznaczonej dla osób dorosłych w obrazach za pomocą interfejsu APi przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 71866149e3d2dca4b39585ce8da73aae658a4d59
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344935"
---
# <a name="detecting-adult-and-racy-content"></a>Wykrywanie zawartości dla dorosłych

Z różnych kategorii visual jest grupy dla dorosłych, która włącza wykrywanie materiałów treści dla dorosłych oraz ogranicza wyświetlanie obrazów zawierającą seksualnym zawartość. Filtr dla dorosłych wykrywania zawartości można ustawić na ruchomej skali umożliwiających preferencji użytkownika.

## <a name="defining-adult-and-racy-content"></a>Definiowanie zawartości dla dorosłych

Spośród różnych funkcji visual objętych [analizowanie obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa), treści dla dorosłych funkcję visual włącza wykrywanie obrazów dla dorosłych. "W treści dla dorosłych" obrazy są definiowane jako, które mają pornograficznej charakter często przedstawiać nagość i seksualnym czynności. "Erotycznej" obrazy są definiowane jako obrazy, które są przekleństwa dwuznaczne z natury i często zawierają mniej zawartość dla dorosłych przekleństwa niż obrazy oznaczone jako "Zawartość dla dorosłych." Typ treści dla dorosłych funkcji visual powszechnie umożliwia ograniczyć wyświetlanie obrazów zawierających przekleństwa dwuznaczne i jawnie seksualnym zawartości.

## <a name="identifying-adult-and-racy-content"></a>Identyfikowanie zawartości dla dorosłych

Metoda analizowanie obrazów zwraca dwie właściwości `isAdultContent` i `isRacyContent`, w odpowiedzi JSON metody, aby wskazać, odpowiednio, zawartości erotycznej i przeznaczonej dla osób dorosłych. Obie te właściwości zwracają wartość logiczną PRAWDA lub FAŁSZ. Metoda zwraca również dwie właściwości `adultScore` i `racyScore`, które reprezentują, odpowiednio, wyniki ufności identyfikowanie zawartości dla dorosłych. Filtr zaufania dla dorosłych wykrywania zawartości można ustawić na ruchomej skali umożliwiających swoje preferencje w oparciu o określonego scenariusza.

## <a name="next-steps"></a>Kolejne kroki

Pojęcia dotyczące [wykrywanie specyficznego dla domeny zawartości](concept-detecting-domain-content.md) i [wykrywanie twarzy](concept-detecting-faces.md).