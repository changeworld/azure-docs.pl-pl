---
title: Jak określić model wykrywania - Ściana
titleSuffix: Azure Cognitive Services
description: W tym artykule pokazano, jak wybrać model wykrywania twarzy do użycia z aplikacją Azure Face.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.openlocfilehash: 40ca1dbf981c5a9025cf5a0bac6b007709d69a77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76934575"
---
# <a name="specify-a-face-detection-model"></a>Określanie modelu wykrywania twarzy

W tym przewodniku pokazano, jak określić model wykrywania twarzy dla usługi Azure Face.

Usługa Face używa modeli uczenia maszynowego do wykonywania operacji na ludzkich twarzach na obrazach. Nadal poprawiamy dokładność naszych modeli w oparciu o opinie klientów i postępy w badaniach, a także dostarczamy te ulepszenia jako aktualizacje modelu. Deweloperzy mają możliwość określenia, której wersji modelu wykrywania twarzy, którą chcieliby użyć; mogą wybrać model, który najlepiej pasuje do ich przypadku użycia.

Czytaj dalej, aby dowiedzieć się, jak określić model wykrywania twarzy w niektórych operacjach twarzy. Usługa Face używa wykrywania twarzy za każdym razem, gdy konwertuje obraz twarzy na inną formę danych.

Jeśli nie masz pewności, czy należy użyć najnowszego modelu, przejdź do sekcji [Oceń różne modele,](#evaluate-different-models) aby ocenić nowy model i porównać wyniki przy użyciu bieżącego zestawu danych.

## <a name="prerequisites"></a>Wymagania wstępne

Należy zapoznać się z koncepcją wykrywania twarzy AI. Jeśli nie, zobacz przewodnik koncepcyjny wykrywania twarzy lub instrukcje:

* [Koncepcje wykrywania twarzy](../concepts/face-detection.md)
* [Jak wykryć twarze na obrazie](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Wykrywanie ścian z określonym modelem

Wykrywanie twarzy znajduje lokalizacje obwiedni ludzkich twarzy i identyfikuje ich wizualne punkty orientacyjne. Wyodrębnia funkcje twarzy i przechowuje je do późniejszego użycia w operacjach [rozpoznawania.](../concepts/face-recognition.md)

Korzystając z interfejsu [API Face — Detect,] można `detectionModel` przypisać wersję modelu z parametrem. Dostępne wartości to:

* `detection_01`
* `detection_02`

Adres URL żądania dla interfejsu API [Face — Detect] REST będzie wyglądać następująco:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]&subscription-key=<Subscription key>`

Jeśli używasz biblioteki klienta, można przypisać `detectionModel` wartość, przekazując w odpowiednim ciągu. Jeśli pozostanie on nieprzypisany, interfejs API`detection_01`użyje domyślnej wersji modelu ( ). Zobacz poniższy przykład kodu dla biblioteki klienta .NET.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_02", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>Dodawanie ściany do osoby z określonym modelem

Usługa Face może wyodrębnić dane twarzy z obrazu i skojarzyć je z **obiektem Osoba** za pośrednictwem interfejsu API [persongroup persongroup osoba — dodawanie twarzy.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) W tym wywołaniu interfejsu API można określić model wykrywania w taki sam sposób, jak w [face - Detect].

Zobacz poniższy przykład kodu dla biblioteki klienta .NET.

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");

string personId = (await faceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

Ten kod tworzy **PersonGroup** `mypersongroupid` z identyfikatorem i dodaje **do niego osobę.** Następnie dodaje twarz do **Person** tej `detection_02` osoby za pomocą modelu. Jeśli parametr *DetectionModel* nie określi, interfejs API użyje `detection_01`modelu domyślnego.

> [!NOTE]
> Nie trzeba używać tego samego modelu wykrywania dla wszystkich twarzy w **Person** obiektu i nie trzeba używać tego samego modelu wykrywania podczas wykrywania nowych twarzy do porównania z **osoba** obiektu (na przykład w [Face — Identyfikowanie] interfejsu API).

## <a name="add-face-to-facelist-with-specified-model"></a>Dodawanie twarzy do listy twarzy z określonym modelem

Można również określić model wykrywania podczas dodawania ściany do istniejącego obiektu **FaceList.** Zobacz poniższy przykład kodu dla biblioteki klienta .NET.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

Ten kod tworzy **FaceList** wywołane `My face collection` i dodaje `detection_02` Face do niego z modelu. Jeśli parametr *DetectionModel* nie określi, interfejs API użyje `detection_01`modelu domyślnego.

> [!NOTE]
> Nie trzeba używać tego samego modelu wykrywania dla wszystkich ścian w **facelist** obiektu i nie trzeba używać tego samego modelu wykrywania podczas wykrywania nowych twarzy do porównania z **FaceList** obiektu.

## <a name="evaluate-different-models"></a>Ocenianie różnych modeli

Różne modele wykrywania twarzy są zoptymalizowane pod kątem różnych zadań. Zobacz poniższą tabelę, aby zapoznać się z różnicami.

|**detection_01**  |**detection_02**  |
|---------|---------|
|Domyślny wybór dla wszystkich operacji wykrywania twarzy. | Wydany w maju 2019 r. i dostępny opcjonalnie we wszystkich operacjach wykrywania twarzy.
|Nie jest zoptymalizowany pod kątem małych, bocznych lub rozmytych twarzy.  | Poprawiono dokładność na małych, bocznych widokach i rozmytych twarzach. |
|Zwraca atrybuty twarzy (ułożenie głowy, wiek, emocje itd.), jeśli są określone w wywołaniu wykrywania. |  Nie zwraca atrybutów twarzy.     |
|Zwraca punkty orientacyjne, jeśli są one określone w wywołaniu wykrywania.   | Nie zwraca punktów orientacyjnych twarzy.  |

Najlepszym sposobem porównania wydajności `detection_01` i `detection_02` modeli jest użycie ich w przykładowym zestawie danych. Zaleca się wywołanie [face — wykrywanie] interfejsu API na różnych obrazów, zwłaszcza obrazów wielu twarzy lub twarzy, które są trudne do zobaczenia, przy użyciu każdego modelu wykrywania. Należy zwrócić uwagę na liczbę ścian, które każdy model zwraca.

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak określić model wykrywania do użycia z różnymi interfejsami API twarzy. Następnie postępuj zgodnie z przewodnikiem Szybki start, aby rozpocząć korzystanie z wykrywania twarzy.

* [Ściana .NET SDK](../Quickstarts/csharp-sdk.md)
* [Face Python SDK](../Quickstarts/python-sdk.md)
* [Face Go SDK](../Quickstarts/go-sdk.md)

[Face - Detect]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Find Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Face - Identify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Face - Verify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[FaceList - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250
[LargeFaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
