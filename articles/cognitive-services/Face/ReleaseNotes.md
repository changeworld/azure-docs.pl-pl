---
title: Informacje o wersji — usługa Face
titleSuffix: Azure Cognitive Services
description: Informacje o wersji usługi Face zawierają historię zmian wersji dla różnych wersji.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: yluiu
ms.openlocfilehash: 767c9dec373a2bda806d75d602b194edde98c6b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76165866"
---
# <a name="face-release-notes"></a>Informacje o wydaniu twarzy

Ten artykuł dotyczy usługi Face w wersji 1.0.

### <a name="release-changes-in-june-2019"></a>Zmiany w wydaniu w czerwcu 2019 r.

* Dodano nowy model wykrywania twarzy z większą dokładnością na małych, bocznych twarzach, okludowanych i rozmytych twarzach. Użyj go za pośrednictwem [twarzy - Wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Dodaj twarz](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - Dodaj twarz](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person Person Person - Dodaj twarz](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) i [LargePersonGroup Person - Dodaj twarz,](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) określając nową nazwę `detection_02` modelu wykrywania twarzy w `detectionModel` parametrze. Więcej szczegółów w [jak określić model wykrywania](Face-API-How-to-Topics/specify-detection-model.md).

### <a name="release-changes-in-april-2019"></a>Zmiany w wydaniu w kwietniu 2019 r.

* Poprawiono ogólną `age` dokładność `headPose` i atrybuty. Atrybut `headPose` jest również aktualizowany `pitch` z włączoną wartością teraz. Użyj tych atrybutów, określając `returnFaceAttributes` je w parametrze [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parametru. 

* Poprawiono szybkość [twarzy - Wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Dodaj twarz](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - Dodaj twarz](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person Person Person - Dodaj twarz](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) i [LargePersonGroup Person - Dodaj twarz](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

### <a name="release-changes-in-march-2019"></a>Zmiany w wydaniu w marcu 2019 r.

* Dodano nowy model rozpoznawania twarzy z większą dokładnością. Użyj go za pośrednictwem [twarzy - Wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [LargeFaceList - Tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [PersonGroup - Tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) i [LargePersonGroup - Tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) przez określenie nazwy nowego modelu rozpoznawania twarzy `recognition_02` w `recognitionModel` parametrze. Więcej szczegółów w [jak określić model rozpoznawania](Face-API-How-to-Topics/specify-recognition-model.md).

### <a name="release-changes-in-january-2019"></a>Zmiany w wersji w styczniu 2019 r.

* Dodano funkcję migawki do obsługi migracji danych między subskrypcjami: [Migawka](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get). Więcej szczegółów w [sekcji Jak przenieść dane twarzy do innej subskrypcji twarzy](Face-API-How-to-Topics/how-to-migrate-face-data.md).

### <a name="release-changes-in-october-2018"></a>Zmiany w wydaniu w październiku 2018 r.

* Udoskonalony `status`opis `createdDateTime` `lastActionDateTime`dla `lastSuccessfulTrainingDateTime` , , i w [PersonGroup - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247), [LargePersonGroup - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5)i [LargeFaceList - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf).

### <a name="release-changes-in-may-2018"></a>Zmiany wydania w maju 2018 r.

* `gender` Poprawiono atrybut znacznie, `age`a `glasses` `facialHair`także `hair` `makeup` ulepszono atrybuty , , , . Użyj ich za pomocą [parametru Twarz — wykryj.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` 

* Zwiększono limit rozmiaru pliku obrazu wejściowego z 4 MB do 6 MB w [face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person Person Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) and [LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

### <a name="release-changes-in-march-2018"></a>Zmiany wydania w marcu 2018 r.

* Dodano kontener w skali miliona: [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) i [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). Więcej szczegółów w [jak korzystać z funkcji na dużą skalę](Face-API-How-to-Topics/how-to-use-large-scale.md).

* Zwiększona [ściana — identyfikowanie parametru](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` z [1, 5] do [1, 100] i domyślnie do 10.

### <a name="release-changes-in-may-2017"></a>Zmiany wydania w maju 2017 r.

* Dodano `hair` `makeup`, `accessory` `occlusion`, `blur` `exposure`, `noise` , i atrybuty w [face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parametr.

* Obsługiwane osoby 10K w persongroup i [twarzy - Identyfikowanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Obsługiwane podziały na strony w osobie Osoba `start` - `top` [Lista](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) z opcjonalnymi parametrami: i .

* Obsługiwana współbieżność w dodawaniu/usuwaniu twarzy względem różnych list twarzy i różnych osób w persongroup.

### <a name="release-changes-in-march-2017"></a>Zmiany wydania w marcu 2017 r.
* Dodano `emotion` atrybut w [face - detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parametr.

* Naprawiono, że twarz nie mogła zostać ponownie ekstekowana prostokątem zwróconym z [twarzy - Wykryj](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) jak `targetFace` na [facelistze - Dodaj twarz](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) i osobę osoba - Dodaj [ścianę](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

* Poprawiono wykrywalny rozmiar twarzy, aby upewnić się, że jest on ściśle od 36x36 do 4096x4096 pikseli.

### <a name="release-changes-in-november-2016"></a>Zmiany wydania w listopadzie 2016 r.
* Dodano subskrypcję Face Storage Standard do przechowywania dodatkowych utrwalonych twarzy podczas korzystania z [persongroup person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) lub [FaceList - Dodaj ścianę](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) do identyfikacji lub dopasowania podobieństwa. Opłata za przechowywane obrazy jest naliczana przy użyciu stawki wynoszącej 0,5 USD za 1000 twarzy proporcjonalnie do liczby dni. Bezpłatne subskrypcje warstwy są nadal ograniczone do 1000 osób.

### <a name="release-changes-in-october-2016"></a>Zmiany wydania w październiku 2016 r.
* Zmieniono komunikat o błędzie więcej niż jednej twarzy w targetFace z "Na obrazie jest więcej niż jedna twarz" na "Na obrazie jest więcej niż jedna twarz" na [facelistze - Dodaj twarz](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) i [osobę - Dodaj twarz](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

### <a name="release-changes-in-july-2016"></a>Zmiany w wydaniu w lipcu 2016 r.
* Obsługiwane uwierzytelnianie obiektu twarz do osoby w [trybie Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

* Dodano `mode` opcjonalny parametr umożliwiający wybór `matchPerson` dwóch `matchFace` trybów pracy: oraz `matchPerson`w face - Find [Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) and default is .

* Dodano `confidenceThreshold` opcjonalny parametr dla użytkownika, aby ustawić próg, czy jedna ściana należy do obiektu Osoba w [face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Dodano `start` opcjonalne i `top` parametry w [persongroup - lista,](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) aby umożliwić użytkownikowi określenie punktu początkowego i łącznej liczby persongroups do listy.

### <a name="v10-changes-from-v0"></a>Zmiany w wersji 1.0 z V0
* Zaktualizowano główny punkt ```https://westus.api.cognitive.microsoft.com/face/v0/``` ```https://westus.api.cognitive.microsoft.com/face/v1.0/```końcowy usługi od do . Zmiany zastosowane do: [Ściana - Wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [Twarz - Identyfikacja](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Twarz - Znajdź podobne](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) i Twarz [- Grupa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

* Zaktualizowano minimalny wykrywalny rozmiar twarzy do 36x36 pikseli. Ściany mniejsze niż 36x36 pikseli nie zostaną wykryte.

* Przestarzałe dane osoby i osoby w osoponiewu V0. Nie można uzyskać dostępu do tych danych za pomocą usługi Face V1.0.

* Przestarzały punkt końcowy V0 interfejsu API twarzy w dniu 30 czerwca 2016 r.
