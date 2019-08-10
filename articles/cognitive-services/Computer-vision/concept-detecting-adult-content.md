---
title: Zawartość dla dorosłych i erotycznej — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z wykrywaniem treści dla dorosłych i erotycznej w obrazach przy użyciu interfejsu APi przetwarzanie obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ca5f35ab47822d74de556671c38886942d23d9ff
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946281"
---
# <a name="detect-adult-and-racy-content"></a>Wykrywanie zawartości dla osób dorosłych i erotycznej

Przetwarzanie obrazów może wykryć dorosłe materiały w obrazach, dzięki czemu deweloperzy mogą ograniczyć wyświetlanie takich obrazów w oprogramowaniu. Flagi zawartości są stosowane z wynikiem między wartością zero a jedną, tak aby deweloperzy mogli interpretować wyniki zgodnie z ich własnymi preferencjami. 

> [!NOTE]
> Ta funkcja jest również oferowana przez usługę [Content moderator platformy Azure](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview) . Zapoznaj się z tą alternatywą dla rozwiązań bardziej rygorystycznych scenariuszy moderowania zawartości, takich jak moderowanie tekstu i przepływy pracy przeglądu przez ludzi.

## <a name="content-flag-definitions"></a>Definicje flag zawartości

Obrazy **dla dorosłych** są definiowane jako te, które są pornograficznej w charakterze i często przedstawiają nagość i seksualne działania. 

Obrazy **erotycznej** są definiowane jako obrazy, które mają charakter płciowo sugerujący i często zawierają mniej wyraźną zawartość z Seksem niż obrazy otagowane jako **osoba dorosła**. 

## <a name="identify-adult-and-racy-content"></a>Zidentyfikuj zawartość dla dorosłych i erotycznej

Interfejs API [analizy](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) .

Metoda Analizuj obraz zwraca dwie właściwości logiczne, `isAdultContent` a `isRacyContent`w odpowiedzi JSON metody, aby wskazać odpowiednio zawartość dla dorosłych i erotycznej. Metoda zwraca również dwie właściwości `adultScore` i `racyScore`, które reprezentują oceny wiarygodności odpowiednio do identyfikacji zawartości dla dorosłych i erotycznej.

## <a name="next-steps"></a>Następne kroki

Poznaj koncepcje dotyczące [wykrywania zawartości specyficznej dla domeny](concept-detecting-domain-content.md) i [wykrywania](concept-detecting-faces.md)twarzy.
