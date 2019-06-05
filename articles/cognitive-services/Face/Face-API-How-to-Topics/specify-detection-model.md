---
title: Jak określić model wykrywania — interfejs API rozpoznawania twarzy
titleSuffix: Azure Cognitive Services
description: W tym artykule Pokaż jak wybrać model wykrywania twarzy za pomocą aplikacji interfejsu API rozpoznawania twarzy platformy Azure.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.component: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.openlocfilehash: dde5623bf5bd579a13fa7271dfba64f9df61bad1
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66576699"
---
# <a name="specify-a-face-detection-model"></a>Określ model wykrywania twarzy

Ten przewodnik pokazuje, jak określić model wykrywania twarzy dla interfejsu API rozpoznawania twarzy platformy Azure.

Interfejs API rozpoznawania twarzy używa modeli uczenia maszynowego do wykonywania operacji na twarze na obrazach. Kontynuujemy poprawić dokładność naszej modeli, w oparciu o opinie klientów oraz poprawę badań i możemy dostarczać te ulepszenia jako aktualizacje w modelu. Deweloperzy mają możliwość określenia, którą wersję modelu wykrywania twarzy chcieliby użyć; ich można wybrać model, który najlepiej pasuje do ich przypadkach użycia.

Czytaj dalej, aby dowiedzieć się, jak określić model wykrywania twarzy w przypadku niektórych operacji twarzy. Interfejs API rozpoznawania twarzy używa wykrywanie twarzy w każdym przypadku, gdy jej konwertuje obraz twarzy na inny rodzaj danych.

Jeśli nie masz pewności, czy należy używać najnowszy model, przejdź do [ocenić różne modele](#evaluate-different-models) sekcji, aby obliczyć nowego modelu i porównaj wyniki przy użyciu bieżącego zestawu danych.

## <a name="prerequisites"></a>Wymagania wstępne

Należy zapoznać się z pojęciem wykrywanie twarzy sztucznej Inteligencji. Jeśli nie masz, zobacz Przewodnik koncepcyjny wykrywania twarzy lub przewodniku z instrukcjami:

* [Pojęcia dotyczące wykrywania twarzy](../concepts/face-detection.md)
* [Jak wykrywanie twarzy na obrazie](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Wykrywanie twarzy przy użyciu określonego modelu

Wykrywanie twarzy umożliwia znalezienie lokalizacji obwiedni ludzkich twarzy i identyfikuje ich visual charakterystycznych elementów krajobrazu. Wyodrębnia funkcje twarzy i zapisuje je do późniejszego użycia z [rozpoznawania](../concepts/face-recognition.md) operacji.

Kiedy używasz [twarzy — wykrywanie] interfejsu API, można przypisać wersji modelu przy użyciu `detectionModel` parametru. Dostępne wartości to:

* `detection_01`
* `detection_02`

Adres URL żądania [twarzy — wykrywanie] interfejsu API REST będzie wyglądać następująco:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]
&subscription-key=<Subscription key>`

Jeśli korzystasz z biblioteki klienta, można przypisać wartość `detectionModel` , przekazując odpowiednie parametry. Jeśli pozostawisz nieprzypisane, interfejsu API użyje domyślnej wersji modelu (`detection_01`). Zobacz poniższy przykład kodu dla biblioteki klienckiej .NET.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceServiceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_02", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>Dodawanie rozpoznawania twarzy do osoby, przy użyciu określonego modelu

Interfejs API rozpoznawania twarzy można wyodrębnić dane rozpoznawania twarzy z obrazu i skojarz ją z **osoby** obiektu za pomocą [grupie osoba — Dodaj twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) interfejsu API. W tym wywołaniu interfejsu API można określić model wykrywania w taki sam sposób jak w [twarzy — wykrywanie].

Zobacz poniższy przykład kodu dla biblioteki klienckiej .NET.

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceServiceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");

string personId = (await faceServiceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

Ten kod tworzy **grupie** o identyfikatorze `mypersongroupid` i dodaje **osoby** do niego. A następnie dodaje do tej powierzchni **osoby** przy użyciu `detection_02` modelu. Jeśli nie określisz *detectionModel* parametru, interfejs API użyje domyślnego modelu `detection_01`.

> [!NOTE]
> Nie trzeba używać tego samego modelu wykrywania dla wszystkich twarze na **osoby** obiektu, a nie trzeba używać tego samego modelu wykrywanie podczas wykrywania nowe powierzchnie do porównania z **osoby** obiektu (w [Twarz — identyfikacja] interfejsu API, na przykład).

## <a name="add-face-to-facelist-with-specified-model"></a>Dodawanie rozpoznawania twarzy do FaceList przy użyciu określonego modelu

Można również określić model wykrywania, po dodaniu twarzy do istniejącego **FaceList** obiektu. Zobacz poniższy przykład kodu dla biblioteki klienckiej .NET.

```csharp
await faceServiceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

Ten kod tworzy **FaceList** o nazwie `My face collection` i dodaje twarzy do niego za pomocą `detection_02` modelu. Jeśli nie określisz *detectionModel* parametru, interfejs API użyje domyślnego modelu `detection_01`.

> [!NOTE]
> Nie trzeba używać tego samego modelu wykrywania dla wszystkich twarze na **FaceList** obiektu, a nie trzeba używać tego samego modelu wykrywanie podczas wykrywania nowe powierzchnie do porównania z **FaceList** obiektu.

## <a name="evaluate-different-models"></a>Ocena różnych modeli

Modele wykrywania twarzy różnych są zoptymalizowane pod kątem różnych zadań. Zobacz poniższą tabelę, aby uzyskać omówienie różnic.

|**detection_01**  |**detection_02**  |
|---------|---------|
|Wybór domyślny dla wszystkich operacji wykrywania twarzy. | Wydane w maja 2019 r i jest dostępny opcjonalnie we wszystkich operacjach wykrywania twarzy.
|Nie zoptymalizowane pod kątem małych, Wyświetl bocznych lub rozmyte twarzy.  | Polepszenia dokładności w małych, widok strony i rozmyty twarzy. |
|Zwraca twarzy atrybutów (poza siedzibę, wiek, emocje itd.), jeśli są one określone w wywołaniu wykrywania. |  Zwraca atrybuty twarzy.     |
|Zwraca twarzy charakterystycznych elementów krajobrazu, jeśli są one określone w wywołaniu wykrywania.   | Nie zwraca to punktów charakterystycznych.  |

Najlepszym sposobem, aby porównać parametrów `detection_01` i `detection_02` modele to z nich korzystać w przykładowym zestawie danych. Firma Microsoft zaleca wywołanie [twarzy — wykrywanie] interfejsu API na wielu różnych obrazów, szczególnie obrazy wielu lub powierzchnie, które są trudne do znajduje się za pomocą każdego modelu wykrywania. Należy zwrócić uwagę na liczbę twarzy, które zwraca każdego modelu.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób określania model wykrywania za pomocą różnych interfejsów API rozpoznawania twarzy. Następnie postępuj zgodnie z szybkiego startu, aby rozpocząć pracę, za pomocą wykrywania twarzy.

* [Wykrywanie twarzy na obrazie (.NET SDK)](../quickstarts/csharp-detect-sdk.md)

[Twarzy — wykrywanie]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Find Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Twarz — identyfikacja]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
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