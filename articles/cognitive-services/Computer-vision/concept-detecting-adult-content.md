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
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 69a4c136e9c210dd40e004b8d5e1c1a2a8fceaa7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60368351"
---
# <a name="detect-adult-and-racy-content"></a>Wykrywanie zawartości dla dorosłych

Przetwarzania obrazów może wykryć materiał treści dla dorosłych w obrazach, dzięki czemu deweloperzy mogą ograniczyć wyświetlanie tych obrazów w oprogramowaniu. Flagi zawartości są stosowane z wynikiem od zera do jednego, dzięki czemu deweloperzy mogą interpretacji wyników na podstawie własnych preferencji. 

> [!NOTE]
> Ta funkcja jest również oferowana przez [Azure Content Moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview) usługi. Zobacz tej alternatywy dla rozwiązań bardziej rygorystyczne scenariuszach moderowanie zawartości, na przykład Moderowanie tekstu i przepływów pracy przeglądu przez ludzi.

## <a name="content-flag-definitions"></a>Definicje Flaga zawartości

**Treści dla dorosłych** obrazy są zdefiniowane jako te, które są pornograficznej z natury i często przedstawiać nagość i seksualnym czynności. 

**Erotycznej** obrazy są zdefiniowane, jak obrazy, które są przekleństwa dwuznaczne z natury i często zawierają mniej zawartość dla dorosłych przekleństwa niż obrazy oznaczone jako **treści dla dorosłych**. 

## <a name="identify-adult-and-racy-content"></a>Identyfikuj zawartość dla dorosłych

[Analizowanie](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) interfejsu API.

Metoda analizowanie obrazów zwraca dwie właściwości logiczne, `isAdultContent` i `isRacyContent`, w odpowiedzi JSON metody, aby wskazać zawartości erotycznej i przeznaczonej dla osób dorosłych, odpowiednio. Metoda zwraca również dwie właściwości `adultScore` i `racyScore`, zawierające wyniki zaufania do identyfikacji zawartości erotycznej i przeznaczonej dla osób dorosłych, odpowiednio.

## <a name="next-steps"></a>Kolejne kroki

Pojęcia dotyczące [wykrywanie specyficznego dla domeny zawartości](concept-detecting-domain-content.md) i [wykrywanie twarzy](concept-detecting-faces.md).
