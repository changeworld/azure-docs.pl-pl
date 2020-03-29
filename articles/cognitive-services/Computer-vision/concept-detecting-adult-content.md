---
title: Treści dla dorosłych, rasistowskie, gory - Computer Vision
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z wykrywaniem zawartości dla dorosłych na obrazach za pomocą interfejsu APi wizji komputerowej.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ee18916a59bb081d65494f46e7aba7c29c7177cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "71718513"
---
# <a name="detect-adult-content"></a>Wykrywanie treści dla dorosłych

Computer Vision może wykrywać materiały dla dorosłych na obrazach, dzięki czemu deweloperzy mogą ograniczyć wyświetlanie tych obrazów w swoim oprogramowaniu. Flagi zawartości są stosowane z wynikiem między zerem a punktem, dzięki czemu deweloperzy mogą interpretować wyniki zgodnie z własnymi preferencjami.

> [!NOTE]
> Wiele z tych funkcji jest oferowanych przez usługę [Azure Content Moderator.](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview) Zobacz tę alternatywę dla rozwiązań bardziej rygorystycznych scenariuszy moderowania zawartości, takich jak moderowanie tekstu i przepływy pracy przeglądu ludzkiego.

## <a name="content-flag-definitions"></a>Definicje flag zawartości

W klasyfikacji "dla dorosłych" znajduje się kilka różnych kategorii:

- Obrazy **dla dorosłych** są definiowane jako te, które mają charakter o charakterze wyraźnie seksualnym i często przedstawiają nagość i akty seksualne.
- **Obrazy racy** są definiowane jako obrazy, które są seksualnie sugestywne w przyrodzie i często zawierają mniej treści o charakterze jednoznacznie seksualnym niż obrazy oznaczone jako **Adult**.
- **Gory** obrazy są zdefiniowane jako te, które przedstawiają gore.

## <a name="use-the-api"></a>Używanie interfejsu API

Zawartość dla dorosłych można wykryć za pomocą interfejsu API [analizowania obrazu.](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Po dodaniu wartości `Adult` parametru **kwerendy visualFeatures** interfejs API zwraca&mdash;`isAdultContent` `isRacyContent`trzy `isGoryContent` &mdash;właściwości logiczne , a w odpowiedzi JSON. Metoda zwraca również odpowiednie&mdash;`adultScore` `racyScore`właściwości `goreScore` &mdash;, i które reprezentują wyniki zaufania między oderem a jednym dla każdej odpowiedniej kategorii.

- [Szybki start: analizowanie obrazu (SDK.NET)](./quickstarts-sdk/csharp-analyze-sdk.md)
- [Szybki start: analizowanie obrazu (INTERFEJS API REST)](./quickstarts/csharp-analyze.md)
