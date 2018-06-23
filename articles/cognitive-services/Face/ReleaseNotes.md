---
title: Informacje o wersji dla usługi interfejsu API krój | Dokumentacja firmy Microsoft
titleSuffix: Microsoft Cognitive Services
description: Informacje o wersji dla usługi interfejsu API powierzchni obejmują historii zmian wersji dla różnych wersji.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 918b3ea5dbaaa44e4eee1a679354c7becffd4686
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349337"
---
# <a name="face-api-release-notes"></a>Informacje o wersji interfejsu API krój

Ten artykuł dotyczy usługi interfejsu API krój firmy Microsoft w wersji 1.0.

### <a name="release-changes-in-may-2018"></a>Zmiany wersji może 2018

* Ulepszone `gender` atrybutu znacznie, a także udoskonalono `age`, `glasses`, `facialHair`, `hair`, `makeup` atrybutów. Korzystania z nich za pośrednictwem [stają przed — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parametru. 

* Zwiększona obrazu wejściowego limit rozmiaru pliku z 4 MB do 6 MB w [stają przed — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList — Dodaj krój](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList — Dodaj krój](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [osoba PersonGroup — Dodaj Krój](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) i [osoba LargePersonGroup — Dodaj krój](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

### <a name="release-changes-in-march-2018"></a>Zmiany wersji 2018 marca

* Dodano skali milionów kontenera: [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) i [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). Więcej szczegółów w [używania funkcji na dużą skalę](Face-API-How-to-Topics/how-to-use-large-scale.md).

* Zwiększona [stają przed — ustalenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` parametr [1, 5] do [1, 100] i domyślne do 10.

### <a name="release-changes-in-may-2017"></a>Zmiany wersji może 2017

* Dodaje `hair`, `makeup`, `accessory`, `occlusion`, `blur`, `exposure`, i `noise` atrybutów w [stają przed — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parametru.

* Obsługiwane 10K osób w PersonGroup i [stają przed — ustalenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Obsługiwane podział na strony w [listy osoby PersonGroup -](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) o następujące parametry opcjonalne: `start` i `top`.

* Obsługiwane współbieżności podczas dodawania/usuwania kroje przed różnych FaceLists i różnych osób w PersonGroup.

### <a name="release-changes-in-march-2017"></a>Zmiany wersji 2017 marca
* Dodaje `emotion` atrybutu w [stają przed — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parametru.

* Stały, nie można ponownie wykryte krój z prostokąt zwrócony z [stają przed — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) jako `targetFace` w [FaceList — Dodaj krój](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) i [PersonGroup osoba — Dodaj krój](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

* Stały rozmiar wykrywalny krój do upewnij się, że jest ściśle od 36 x 36 do 4096 x 4096 pikseli.

### <a name="release-changes-in-november-2016"></a>Zmiany wersji listopada 2016
* Dodane krój magazynu standardowego subskrypcji do przechowywania dodatkowe kroje utrwalonego, korzystając z [PersonGroup osoba — Dodaj krój](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) lub [FaceList — Dodaj krój](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) do identyfikacji lub podobieństwa dopasowania. Obrazy przechowywane są naliczane według $0,5 na powierzchni 1000, a ta stawka jest proporcjonalna codziennie. Warstwa bezpłatna subskrypcji nadal maksymalnie 1000 całkowita osób.

### <a name="release-changes-in-october-2016"></a>Zmiany wersji października 2016
* Zmienione w komunikacie o błędzie z więcej niż jeden stojących w targetFace z "Istnieją więcej niż jeden krój w obrazie" do "Istnieje więcej niż jeden krój w obrazie" [FaceList — Dodaj krój](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) i [PersonGroup osoba — Dodaj krój](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

### <a name="release-changes-in-july-2016"></a>Zmiany wersji lipca 2016 r.
* Obsługiwane krój do uwierzytelniania obiektu osoby w [stają przed - Sprawdź, czy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

* Dodaje opcjonalne `mode` parametru umożliwiającego wybór dwa tryby pracy: `matchPerson` i `matchFace` w [krój — Znajdź podobne](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) i domyślna to `matchPerson`.

* Dodaje opcjonalne `confidenceThreshold` parametr dla użytkownika ustawić próg czy jeden krój należy do obiektu osoby w [stają przed — ustalenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Dodaje opcjonalne `start` i `top` parametrów w [PersonGroup — lista](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) umożliwia użytkownikowi określenie punktu początkowego i całkowitą liczbę PersonGroups do listy.

### <a name="v10-changes-from-v0"></a>Zmiany w wersji 1.0 z V0
* Zaktualizowano punkt końcowy głównego usługi z ```https://westus.api.cognitive.microsoft.com/face/v0/``` do ```https://westus.api.cognitive.microsoft.com/face/v1.0/```. Zmiany zostały zastosowane do: [stają przed — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [stają przed — ustalenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [krój — Znajdź podobne](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) i [stają przed — grupa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

* Zaktualizowano rozmiar minimalny krój wykrywalny do 36 x 36 pikseli. Mniejsze niż 36 x 36 pikseli kroje nie zostanie wykryty.

* Przestarzałe dane PersonGroup i osoby V0 twarzy na obrazie. Te dane nie są dostępne w usłudze krój 1.0.

* Przestarzałe punktu końcowego V0 powierzchni interfejsu API w dniu 30 czerwca 2016 r.
