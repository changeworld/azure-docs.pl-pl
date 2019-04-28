---
title: Jak określić model rozpoznawania — interfejs API rozpoznawania twarzy
titleSuffix: Azure Cognitive Services
description: W tym artykule Pokaż sposobu wybierania rozpoznawania modelu do korzystania z aplikacji interfejsu API rozpoznawania twarzy platformy Azure.
services: cognitive-services
author: longl
manager: nitinme
ms.service: cognitive-services
ms.component: face-api
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: longl
ms.openlocfilehash: 33348e637143b923719425b9674f99a475d848d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60815828"
---
# <a name="specify-a-face-recognition-model"></a>Określanie modelu rozpoznawania twarzy

Ten przewodnik pokazuje, jak określić model rozpoznawania twarzy, wykrywanie twarzy, identyfikacji i wyszukiwanie podobieństw przy użyciu interfejsu API rozpoznawania twarzy platformy Azure.

Interfejs API rozpoznawania twarzy używa modeli uczenia maszynowego do wykonywania operacji na twarze na obrazach. Kontynuujemy poprawić dokładność naszej modeli, w oparciu o opinie klientów oraz poprawę badań i możemy dostarczać te ulepszenia jako aktualizacje w modelu. Deweloperzy mają możliwość określenia, którą wersję model rozpoznawania twarzy chcieliby użyć. ich można wybrać model, który najlepiej pasuje do ich przypadkach użycia.

Jeśli jesteś nowego użytkownika, firma Microsoft zaleca użyj najnowszego modelu. Czytaj dalej, aby dowiedzieć się, jak określić jej w innych operacjach twarzy, unikając konfliktów modelu. Jeśli jesteś użytkownikiem zaawansowanym i w związku z tym nie pewności, czy należy przełączasz się do najnowszego modelu, przejdź do [ocenić różne modele](#evaluate-different-models) sekcji, aby obliczyć nowego modelu i porównaj wyniki przy użyciu bieżącego zestawu danych.

## <a name="prerequisites"></a>Wymagania wstępne

Należy zapoznać się z koncepcji, wykrywanie twarzy sztucznej Inteligencji i identyfikacji. Jeśli nie masz, najpierw Zobacz te przewodniki z instrukcjami:

* [Jak wykrywanie twarzy na obrazie](HowtoDetectFacesinImage.md)
* [Identyfikowanie twarzy na obrazie](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Wykrywanie twarzy przy użyciu określonego modelu

Wykrywanie twarzy identyfikuje visual charakterystycznych elementów krajobrazu ludzkich twarzy i wyszukuje lokalizacjach obwiedni. Również wyodrębnia funkcje twarzy i zapisuje je do użytku w identyfikacji. Wszystkie te informacje stanowi reprezentację jednej powierzchni.

Model rozpoznawania służy są wyodrębniane z funkcji rozpoznawania twarzy, dzięki czemu można określić wersji modelu, podczas wykonywania operacji wykrywania.

Korzystając z [twarzy — wykrywanie] interfejsu API, przypisywanie wersja modelu przy użyciu `recognitionModel` parametru. Dostępne wartości to:

* `recognition_01`
* `recognition_02`

Opcjonalnie można określić _returnRecognitionModel_ parametru (domyślny **false**) aby wskazać, czy _recognitionModel_ ma zostać zwrócone w odpowiedzi. Dlatego adresie URL żądania dla [twarzy — wykrywanie] interfejsu API REST będzie wyglądać następująco:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]
&subscription-key=<Subscription key>`

Jeśli korzystasz z biblioteki klienta, można przypisać wartość `recognitionModel` , przekazując ciąg reprezentujący wersję.
Jeśli pozostawisz nieprzypisane, domyślna wersja modelu (_recognition_01_) będą używane. Zobacz poniższy przykład kodu dla biblioteki klienckiej .NET.

```csharp
string imageUrl = "http://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceServiceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>Identyfikowanie twarzy przy użyciu określonego modelu

Interfejs API rozpoznawania twarzy można wyodrębnić dane rozpoznawania twarzy z obrazu i skojarz ją z **osoby** obiektu (za pośrednictwem [Dodaj twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) na przykład wywołanie interfejsu API) i wielu **osoby** obiekty mogą być przechowywane razem w **grupie**. Następnie nowej twarzy można porównać z **grupie** (przy użyciu [Twarz — identyfikacja] wywołania), i mogą być identyfikowane pasującego osoby w tej grupie.

A **grupie** powinien mieć jeden model rozpoznawania unikatowe dla wszystkich **osoby**s, na które można określić to przy użyciu `recognitionModel` parametru podczas tworzenia grupy ([Element PersonGroup — tworzenie] lub [Element LargePersonGroup — tworzenie]). Jeśli nie określisz tego parametru, oryginalnym `recognition_01` model jest używany. Grupy zawsze będzie korzystać z rozpoznawania modelu, który został utworzony za pomocą, a nowe powierzchnie będzie skojarzony z tym modelu po ich dodaniu do niego; Nie można zmienić po utworzeniu grupy. Aby zobaczyć, jakie modelu **grupie** jest skonfigurowany, należy użyć [grupie - Get] interfejsu API za pomocą _returnRecognitionModel_ zestaw parametrów jako **true**.

Zobacz poniższy przykład kodu dla biblioteki klienckiej .NET.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceServiceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

W tym kodzie **grupie** o identyfikatorze `mypersongroupid` zostanie utworzony, a jego skonfigurowano do użycia _recognition_02_ modelu można wyodrębnić funkcji rozpoznawania twarzy.

Należy odpowiednio określić model, który można używać podczas wykrywania twarzy, aby porównać to **grupie** (za pośrednictwem [twarzy — wykrywanie] interfejsu API). Model można używać zawsze powinny być zgodne z **grupie**w konfiguracji; w przeciwnym razie operacja zakończy się niepowodzeniem ze względu na niezgodną modeli.

Nie ma zmian w [Twarz — identyfikacja] API; należy tylko określić wersję modelu w wykrywania.

## <a name="find-similar-faces-with-specified-model"></a>Znajdź podobnych twarzy przy użyciu określonego modelu

Można również określić model uznawania osiągnięć podobieństwa wyszukiwania. Możesz przypisać wersji modelu przy użyciu `recognitionModel` podczas tworzenia list twarzy z [FaceList — tworzenie] interfejsu API lub [LargeFaceList - Create]. Jeśli nie określisz tego parametru, oryginalnym `recognition_01` model jest używany. Lista twarzy zawsze będzie korzystać z rozpoznawania modelu, który został utworzony za pomocą i nowe powierzchnie będzie skojarzony z tym modelu po ich dodaniu do niego; Nie można zmienić po utworzeniu. Aby zobaczyć, jakie modelu skonfigurowano list twarzy, użyj [FaceList - Get] interfejsu API za pomocą _returnRecognitionModel_ zestaw parametrów jako **true**.

Zobacz poniższy przykład kodu dla biblioteki klienckiej .NET.

```csharp
await faceServiceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

Ten kod tworzy listę rozpoznawania twarzy, nazywaną `My face collection`przy użyciu _recognition_02_ modelu dla funkcji wyodrębniania. Podczas wyszukiwania listy twarzy podobnych twarzy do nowej twarzy wykryte napotykane przez musi zostały wykryte ([twarzy — wykrywanie]) przy użyciu _recognition_02_ modelu. Tak jak w poprzedniej sekcji modelu musi być zgodne.

Nie ma zmian w [Twarzy — wyszukiwanie podobnych] API; można określić tylko wersję modelu wykrywania.

## <a name="verify-faces-with-specified-model"></a>Weryfikuj twarze za pomocą określonego modelu

[Twarzy — weryfikowanie] API sprawdza, czy dwie twarze należą do tej samej osoby. Nie ma zmian w interfejsie API Sprawdź w odniesieniu do modeli rozpoznawania, ale można porównać tylko twarzy, które zostały wykryte za pomocą tego samego modelu. Dlatego te dwie twarze zarówno musisz zostały wykryte za pomocą `recognition_01` lub `recognition_02`.

## <a name="evaluate-different-models"></a>Ocena różnych modeli

Jeśli chcesz porównać parametrów _recognition_01_ i _recognition_02_ modeli na podstawie danych, konieczne będzie:

1. Utworzyć dwa **grupie**s _recognition_01_ i _recognition_02_ odpowiednio.
1. Korzystanie z Twoich danych obrazu do wykrywania twarzy i zarejestruj je do **osoby**pod kątem tych dwóch **grupie**procesu s i wyzwalacza szkolenia z [Element PersonGroup — uczenie] interfejsu API.
1. Testowanie za pomocą [Twarz — identyfikacja] zarówno **grupie**s i porównać wyniki.

Jeśli określisz zwykle próg pewności (wartość od zera do jednego, który określa, jak pewność modelu musi być do identyfikacji twarzy), może być konieczne użytek progi różnią się w różnych modelach. Próg dla jednego modelu nie jest przeznaczona do udostępnienia do innego, a nie przyniesie niekoniecznie te same wyniki.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób określania model rozpoznawania za pomocą różnych interfejsów API usług rozpoznawania twarzy. Następnie postępuj zgodnie z szybkiego startu, aby rozpocząć pracę, za pomocą wykrywania twarzy.

* [Wykrywanie twarzy na obrazie](../quickstarts/csharp-detect-sdk.md)

[Twarzy — wykrywanie]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Twarzy — wyszukiwanie podobnych]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Twarz — identyfikacja]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Twarzy — weryfikowanie]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[Element PersonGroup — tworzenie]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[Grupie - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[Element PersonGroup — uczenie]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[Element LargePersonGroup — tworzenie]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList — tworzenie]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
