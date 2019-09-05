---
title: Jak określić model rozpoznawania — interfejs API rozpoznawania twarzy
titleSuffix: Azure Cognitive Services
description: W tym artykule opisano sposób wybierania modelu rozpoznawania, który będzie używany w aplikacji interfejs API rozpoznawania twarzy platformy Azure.
services: cognitive-services
author: longl
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: longl
ms.openlocfilehash: 23c54a69f709ec97d895ed5965841e43ebdc560c
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70306562"
---
# <a name="specify-a-face-recognition-model"></a>Określanie modelu rozpoznawania twarzy

W tym przewodniku pokazano, jak określić model rozpoznawania kroju na potrzeby wykrywania i identyfikowania kroju oraz wyszukiwania podobieństw przy użyciu interfejs API rozpoznawania twarzy platformy Azure.

Interfejs API rozpoznawania twarzy używa modeli uczenia maszynowego do wykonywania operacji na ludzkich twarzy na obrazach. Kontynuujemy ulepszanie dokładności naszych modeli w oparciu o opinie klientów i postępy w badaniach, a firma Microsoft zapewnia te udoskonalenia jako aktualizacje modelu. Deweloperzy mogą określić, która wersja modelu rozpoznawania kroju ma być używana; mogą wybrać model, który najlepiej pasuje do swojego przypadku użycia.

Jeśli jesteś nowym użytkownikiem, zalecamy użycie najnowszego modelu. Przeczytaj, aby dowiedzieć się, jak określić ją w różnych operacjach związanych z działaniem, unikając konfliktów między modelami. Jeśli jesteś użytkownikiem zaawansowanym i nie wiesz, czy chcesz przełączyć się do najnowszego modelu, przejdź do sekcji [Oceń różne modele](#evaluate-different-models) , aby obliczyć nowy model i porównać wyniki przy użyciu bieżącego zestawu danych.

## <a name="prerequisites"></a>Wymagania wstępne

Należy zapoznać się z pojęciami wykrywania i identyfikacji elementu AI. Jeśli nie, zapoznaj się z następującymi przewodnikami:

* [Jak wykrywać twarze w obrazie](HowtoDetectFacesinImage.md)
* [Jak identyfikować twarze na obrazie](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Wykrywanie twarzy z określonym modelem

Wykrywanie twarzy służy do identyfikowania wizualizacji ludzkich i znajdowania ich lokalizacji granicznych. Wyodrębnia również funkcje i zapisuje je do użycia w identyfikacji. Wszystkie te informacje stanowią reprezentację jednej z nich.

Model rozpoznawania jest używany podczas wyodrębniania funkcji rozpoznawania, dlatego można określić wersję modelu podczas wykonywania operacji wykrywania.

W przypadku korzystania z interfejsu API [Wykrywanie kroju] i przypisania wersji `recognitionModel` modelu do parametru. Dostępne są następujące wartości:

* `recognition_01`
* `recognition_02`

Opcjonalnie można określić parametr _returnRecognitionModel_ (domyślnie **false**), aby wskazać, czy _recognitionModel_ powinien być zwracany w odpowiedzi. Dlatego adres URL żądania dla interfejsu API REST [Wykrywanie kroju] będzie wyglądać następująco:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

Jeśli używasz biblioteki klienckiej, możesz przypisać wartość `recognitionModel` przez przekazanie ciągu reprezentującego wersję.
Jeśli pozostawisz nie przypisane, zostanie użyta domyślna wersja modelu (_recognition_01_). Zobacz Poniższy przykład kodu dla biblioteki klienta .NET.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>Identyfikowanie twarzy z określonym modelem

Interfejs API rozpoznawania twarzy może wyodrębnić dane dotyczące kroju z obrazu i skojarzyć go z obiektem **osoby** (za pośrednictwem wywołania interfejsu API [dodawania](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) , na przykład), a wiele obiektów **osób** może być przechowywanych razem w danej **osobie**. Następnie można porównać nową miarę z grupą **osób** (z wywołaniem o [Twarz — identyfikacja] ) i można zidentyfikować osobę pasującą w tej grupie.

Grupa **osób** powinna mieć jeden unikatowy model rozpoznawania dla wszystkich **osób**i można ją określić przy użyciu `recognitionModel` parametru podczas tworzenia grupy ([Element PersonGroup — tworzenie] lub [Element LargePersonGroup — tworzenie]). Jeśli ten parametr nie jest określony, używany jest oryginalny `recognition_01` model. Grupa zawsze będzie używać modelu rozpoznawania, który został utworzony za pomocą programu, a nowe twarze zostaną skojarzone z tym modelem po ich dodaniu. nie można go zmienić po utworzeniu grupy. Aby sprawdzić model, za pomocą którego jest konfigurowana dana **osoba** , użyj interfejsu API [Osoba — Pobierz] z parametrem _returnRecognitionModel_ ustawionym jako **true**.

Zobacz Poniższy przykład kodu dla biblioteki klienta .NET.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

W tym kodzie zostanie utworzona zbiór **osób** z identyfikatorem `mypersongroupid` i jest on skonfigurowany do korzystania z modelu _recognition_02_ w celu wyodrębnienia funkcji rozpoznawania.

Odpowiednio należy określić model, który ma być używany podczas wykrywania twarzy w celu porównania z tą **osobą** (za pomocą interfejsu API [Wykrywanie kroju] przez funkcję). Używany model powinien zawsze być zgodny z konfiguracją **osoby**. w przeciwnym razie operacja zakończy się niepowodzeniem z powodu niezgodnych modeli.

Nie wprowadzono zmian w interfejsie API [Twarz — identyfikacja] . wystarczy określić wersję modelu do wykrycia.

## <a name="find-similar-faces-with-specified-model"></a>Znajdź podobne twarze z określonym modelem

Możesz również określić model rozpoznawania dla wyszukiwania podobieństwa. Wersję modelu można przypisać do `recognitionModel` programu podczas tworzenia listy czołowej za pomocą interfejsu API [FaceList — tworzenie] lub [LargeFaceList — tworzenie]. Jeśli ten parametr nie jest określony, używany jest oryginalny `recognition_01` model. Lista twarzy zawsze będzie używać modelu rozpoznawania, który został utworzony za pomocą programu, a nowe twarze zostaną skojarzone z tym modelem po ich dodaniu do niego. nie można go zmienić po utworzeniu. Aby sprawdzić model, z którym jest skonfigurowana lista czołowa, użyj interfejsu API [FaceList — Pobierz] z parametrem _returnRecognitionModel_ ustawionym jako **true**.

Zobacz Poniższy przykład kodu dla biblioteki klienta .NET.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

Ten kod tworzy listę czołową o `My face collection`nazwie przy użyciu modelu _recognition_02_ na potrzeby wyodrębniania funkcji. Po przeszukiwaniu tej listy twarzy pod kątem podobnych twarzy na nowej wykrytej powierzchni należy wykrycie tej twarzy ([Wykrywanie kroju]) przy użyciu modelu _recognition_02_ . Jak w poprzedniej sekcji, model musi być spójny.

Nie wprowadzono zmian w funkcji [Głowa — Znajdź podobne] interfejsu API; należy określić wersję modelu tylko do wykrywania.

## <a name="verify-faces-with-specified-model"></a>Weryfikuj twarze z określonym modelem

Interfejs API [Sprawdzanie kroju] sprawdza, czy dwie twarze należą do tej samej osoby. Nie wprowadzono zmian w sprawdzaniu interfejsu API w odniesieniu do modeli rozpoznawania, ale można tylko porównać twarze, które zostały wykryte w tym samym modelu. W związku z tym obie twarze muszą zostać wykryte przy użyciu `recognition_01` lub. `recognition_02`

## <a name="evaluate-different-models"></a>Oceń różne modele

Jeśli chcesz porównać wyniki modeli _recognition_01_ i _recognition_02_ z danymi, musisz wykonać następujące działania:

1. Utwórz odpowiednio dwie **osoby**z _recognition_01_ i _recognition_02_ .
1. Użyj danych obrazu w celu wykrycia twarzy i zarejestrowania ich dla **osób**trzecich dla tych dwóch **osób**i wyzwolenie procesu szkolenia z interfejsem API [Element PersonGroup — uczenie] się w ramach usługi Person.
1. Przetestuj ze [Twarz — identyfikacja] iporównujej wyniki.

Jeśli zwykle określono próg ufności (wartość z zakresu od zero do 1, która określa, jak pewność, że model musi być identyfikowany jako), może być konieczne użycie różnych progów dla różnych modeli. Próg dla jednego modelu nie powinien być współużytkowany z innym i nie musi dawać takich samych wyników.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób określania modelu rozpoznawania, który ma być używany z różnymi interfejsami API usługi kroju. Następnie postępuj zgodnie z przewodnikiem Szybki Start, aby rozpocząć korzystanie z wykrywania czołowego.

* [Zestaw SDK platformy .NET](../Quickstarts/csharp-sdk.md)
* [Zestaw SDK dla języka Python](../Quickstarts/python-sdk.md)

[Wykrywanie kroju]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Głowa — Znajdź podobne]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Twarz — identyfikacja]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Sprawdzanie kroju]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[Element PersonGroup — tworzenie]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[Osoba — Pobierz]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[Element PersonGroup — uczenie]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[Element LargePersonGroup — tworzenie]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList — tworzenie]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList — Pobierz]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList — tworzenie]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
