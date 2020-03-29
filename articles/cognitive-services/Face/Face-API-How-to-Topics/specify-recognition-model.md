---
title: Jak określić model rozpoznawania - Ściana
titleSuffix: Azure Cognitive Services
description: W tym artykule pokazano, jak wybrać model rozpoznawania do użycia z aplikacją Azure Face.
services: cognitive-services
author: longli0
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: longl
ms.openlocfilehash: da9ad5576d146c007e45124668875e9681860ce6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76938820"
---
# <a name="specify-a-face-recognition-model"></a>Określanie modelu rozpoznawania twarzy

W tym przewodniku pokazano, jak określić model rozpoznawania twarzy do wykrywania twarzy, identyfikacji i wyszukiwania podobieństwa za pomocą usługi Azure Face.

Usługa Face używa modeli uczenia maszynowego do wykonywania operacji na ludzkich twarzach na obrazach. Nadal poprawiamy dokładność naszych modeli w oparciu o opinie klientów i postępy w badaniach, a także dostarczamy te ulepszenia jako aktualizacje modelu. Deweloperzy mają możliwość określenia, której wersji modelu rozpoznawania twarzy, którą chcieliby użyć; mogą wybrać model, który najlepiej pasuje do ich przypadku użycia.

Jeśli jesteś nowym użytkownikiem, zalecamy użycie najnowszego modelu. Czytaj dalej, aby dowiedzieć się, jak określić go w różnych operacjach face, unikając konfliktów modelu. Jeśli jesteś zaawansowanym użytkownikiem i nie masz pewności, czy należy przełączyć się na najnowszy model, przejdź do sekcji [Oceń różne modele,](#evaluate-different-models) aby ocenić nowy model i porównać wyniki przy użyciu bieżącego zestawu danych.

## <a name="prerequisites"></a>Wymagania wstępne

Należy zapoznać się z pojęciami wykrywania i identyfikacji twarzy AI. Jeśli nie, najpierw zobacz te instrukcje:

* [Jak wykryć twarze na obrazie](HowtoDetectFacesinImage.md)
* [Jak zidentyfikować twarze na obrazie](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Wykrywanie ścian z określonym modelem

Wykrywanie twarzy identyfikuje wizualne punkty orientacyjne ludzkich twarzy i znajduje ich lokalizacje obwiedni. Wyodrębnia również cechy twarzy i przechowuje je do użycia w identyfikacji. Wszystkie te informacje stanowią reprezentację jednej twarzy.

Model rozpoznawania jest używany, gdy operacje twarzy są wyodrębniane, dzięki czemu można określić wersję modelu podczas wykonywania operacji Wykrywanie.

Podczas korzystania z [interfejsu API Face —] `recognitionModel` detect przypisz wersję modelu z parametrem. Dostępne wartości to:

* `recognition_01`
* `recognition_02`

Opcjonalnie można określić _returnRecognitionModel_ parametr (domyślne **false),** aby wskazać, czy _recognitionModel_ powinny być zwracane w odpowiedzi. Tak więc adres URL żądania dla interfejsu API [Face — Detect] REST będzie wyglądać następująco:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

Jeśli używasz biblioteki klienta, można przypisać `recognitionModel` wartość, przekazując ciąg reprezentujący wersję.
Jeśli pozostawisz go nieprzypisaną, zostanie użyta domyślna wersja modelu (_recognition_01)._ Zobacz poniższy przykład kodu dla biblioteki klienta .NET.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>Identyfikowanie ścian z określonym modelem

Usługa Twarz może wyodrębnić dane twarzy z obrazu i skojarzyć je z **obiektem Osoba** (na przykład za pośrednictwem wywołania Dodaj interfejs API [twarzy),](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) a wiele **person** obiektów może być przechowywanych razem w **grupie persongroup**. Następnie można porównać nową twarz z **grupą osób** (z [wywołaniem Face — Identify)] i można zidentyfikować pasującą osobę w tej grupie.

Grupa **osób** powinna mieć jeden unikatowy model rozpoznawania dla wszystkich **osób** `recognitionModel` i można to określić za pomocą parametru podczas tworzenia grupy ([PersonGroup - Create] lub [LargePersonGroup - Create]). Jeśli ten parametr nie zostanie `recognition_01` określony, używany jest oryginalny model. Grupa zawsze będzie używać modelu rozpoznawania, z którym została utworzona, a nowe twarze zostaną skojarzone z tym modelem, gdy zostaną do niego dodane; nie można tego zmienić po utworzeniu grupy. Aby zobaczyć, za pomocą którego modelu jest skonfigurowana **grupa persongroup,** należy użyć [programu PersonGroup — Pobierz] interfejs API z parametrem _returnRecognitionModel_ ustawionym jako **true**.

Zobacz poniższy przykład kodu dla biblioteki klienta .NET.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

W tym kodzie **persongroup** `mypersongroupid` z identyfikatorem jest tworzony i jest skonfigurowany do korzystania z _modelu recognition_02_ do wyodrębniania operacji twarzy.

Odpowiednio należy określić, który model ma być używany podczas wykrywania twarzy do porównania z tą **grupą osób** (za pośrednictwem interfejsu [— wykrywanie] interfejsu API). Używany model powinien być zawsze zgodny z konfiguracją **persongroup;** w przeciwnym razie operacja zakończy się niepowodzeniem z powodu niezgodnych modeli.

Nie ma żadnych zmian w [face - identyfikowanie] interfejsu API; wystarczy określić wersję modelu w wykrywaniu.

## <a name="find-similar-faces-with-specified-model"></a>Znajdowanie podobnych ścian z określonym modelem

Można również określić model rozpoznawania dla wyszukiwania podobieństwa. Wersję modelu można przypisać `recognitionModel` podczas tworzenia listy twarzy za pomocą [listy facelist - Tworzenie] interfejsu API lub [LargeFaceList - Create]. Jeśli ten parametr nie zostanie `recognition_01` określony, używany jest oryginalny model. Lista twarzy zawsze będzie używać modelu rozpoznawania, z którym został utworzony, a nowe twarze zostaną skojarzone z tym modelem, gdy zostaną do niego dodane; nie można tego zmienić po utworzeniu. Aby zobaczyć, z jakim modelem jest skonfigurowana lista twarzy, użyj [facelist - Get] API z parametrem _returnRecognitionModel_ ustawionym jako **true**.

Zobacz poniższy przykład kodu dla biblioteki klienta .NET.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

Ten kod tworzy listę `My face collection`twarzy o nazwie , przy użyciu _modelu recognition_02_ do wyodrębniania operacji. Podczas wyszukiwania tej listy twarzy w poszukiwaniu podobnych twarzy do nowej wykrytej twarzy, ta twarz musi zostać wykryta[(Twarz - Wykryj)]przy użyciu _modelu recognition_02._ Podobnie jak w poprzedniej sekcji, model musi być spójny.

Nie ma żadnych zmian w [Face - Znajdź podobny] interfejs API; należy określić tylko wersję modelu w wykrywaniu.

## <a name="verify-faces-with-specified-model"></a>Weryfikowanie ścian za pomocą określonego modelu

[Twarz — Sprawdź] interfejs API sprawdza, czy dwie twarze należą do tej samej osoby. Nie ma żadnych zmian w Verify API w odniesieniu do modeli rozpoznawania, ale można porównać tylko twarze, które zostały wykryte za pomocą tego samego modelu. Tak więc, obie twarze będą musiały `recognition_01` zostać `recognition_02`wykryte za pomocą lub .

## <a name="evaluate-different-models"></a>Ocenianie różnych modeli

Jeśli chcesz porównać wyniki _recognition_01_ i _recognition_02_ modeli na swoich danych, musisz:

1. Utwórz dwie **grupy osób**z _recognition_01_ i _recognition_02_ odpowiednio.
1. Użyj danych obrazu, aby wykryć twarze i zarejestrować je do **osoby**s dla tych dwóch **PersonGroup**s i wyzwolić proces szkolenia z [PersonGroup — Train] API.
1. Test z [twarzą — identyfikować] zarówno **osoby grupy**s i porównać wyniki.

Jeśli zwykle określisz próg ufności (wartość między zerem a tą, która określa, jak pewny siebie musi być model do identyfikowania ściany), może być konieczne użycie różnych progów dla różnych modeli. Próg dla jednego modelu nie ma być współużytkowane do innego i nie musi produkować takie same wyniki.

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak określić model rozpoznawania do użycia z różnymi interfejsami API usługi Face. Następnie postępuj zgodnie z przewodnikiem Szybki start, aby rozpocząć korzystanie z wykrywania twarzy.

* [Ściana .NET SDK](../Quickstarts/csharp-sdk.md)
* [Face Python SDK](../Quickstarts/python-sdk.md)
* [Face Go SDK](../Quickstarts/go-sdk.md)

[Face - Detect]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Twarz - Znajdź podobne]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Face - Identify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Twarz - Sprawdź]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[Element PersonGroup — tworzenie]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Pobierz]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[Element PersonGroup — uczenie]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[Element LargePersonGroup — tworzenie]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Tworzenie]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Pobierz]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList — tworzenie]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
