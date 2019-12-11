---
title: Co to jest interfejs API rozpoznawania twarzy?
titleSuffix: Azure Cognitive Services
description: Usługa Azure Cognitive Services interfejs API rozpoznawania twarzy udostępnia algorytmy, które są używane do wykrywania, rozpoznawania i analizowania ludzkich twarzy na obrazach.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 75f2969562dd2de041ffaa146c394f1a18c7756b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74961543"
---
# <a name="what-is-the-azure-face-api"></a>Co to jest interfejs API rozpoznawania twarzy na platformie Azure?

Usługa Azure Cognitive Services interfejs API rozpoznawania twarzy udostępnia algorytmy, które są używane do wykrywania, rozpoznawania i analizowania ludzkich twarzy na obrazach. Możliwość przetwarzania informacji o człowieku jest ważna w wielu różnych scenariuszach oprogramowania. Przykładowe scenariusze to zabezpieczenia, interfejs użytkownika naturalnego, analiza zawartości obrazów oraz zarządzanie, aplikacje mobilne i programowe.

Interfejs API rozpoznawania twarzy udostępnia kilka różnych funkcji. Każda funkcja jest przedkreślona w poniższych sekcjach. Przeczytaj, aby dowiedzieć się więcej o nich.

## <a name="face-detection"></a>Wykrywanie twarzy

Interfejs API rozpoznawania twarzy wykrywa ludzkie twarze w obrazie i zwraca współrzędne ich lokalizacji. Opcjonalnie wykrywanie czołowe może wyodrębnić szereg atrybutów związanych ze stroną. Przykłady to ułożenia głowy, płeć, wiek, rozpoznawania emocji, twarzy i okulary.

> [!NOTE]
> Funkcja wykrywania kroju jest również dostępna za pomocą [interfejs API przetwarzania obrazów](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home). Jeśli chcesz wykonać dalsze operacje z danymi o kroju, użyj interfejs API rozpoznawania twarzy, która jest usługą omówioną w tym artykule.

![Obraz kobieta i Man, z prostokątami rysowanymi wokół ich powierzchni i wieku oraz wyświetlanych płci](./Images/Face.detection.jpg)

Aby uzyskać więcej informacji o wykrywaniu czołowym, zobacz artykuł dotyczący pojęć dotyczących [wykrywania](concepts/face-detection.md) . Zapoznaj się również z dokumentacją dotyczącą [wykrywania interfejsów API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) .

## <a name="face-verification"></a>Weryfikacja twarzy

Sprawdź, czy interfejs API wykonuje uwierzytelnianie w odniesieniu do dwóch wykrytych twarzy lub od jednej wykrytych funkcji do jednego obiektu osoby. W praktyce ocenia on, czy dwie twarze należą do tej samej osoby. Ta funkcja jest potencjalnie przydatna w scenariuszach zabezpieczeń. Aby uzyskać więcej informacji, zobacz Przewodnik dotyczący pojęć związanych z [rozpoznawaniem](concepts/face-recognition.md) i sprawdzanie dokumentacji dotyczącej [interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) .

## <a name="find-similar-faces"></a>Wyszukiwanie podobnych twarzy

Interfejs API znajdowania podobnego porównuje miarę docelową z zestawem kandydatów, aby znaleźć mniejszy zestaw twarzy, który wygląda podobnie do powierzchni docelowej. Obsługiwane są dwa tryby pracy, matchPerson i matchFace. Tryb matchPerson zwraca podobne twarze po filtrach dla tej samej osoby za pomocą narzędzia [verify API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). Tryb matchFace ignoruje ten sam filtr osoby. Zwraca listę podobnych powierzchni kandydujących, które mogą lub nie należą do tej samej osoby.

W poniższym przykładzie pokazano tarczę docelową:

![Uśmiechnięta kobieta](./Images/FaceFindSimilar.QueryFace.jpg)

Obrazy te są następujące:

![Pięć obrazów uśmiechniętych osób. Obrazy a i b przedstawiają tę samą osobę.](./Images/FaceFindSimilar.Candidates.jpg)

Aby znaleźć cztery podobne twarze, tryb matchPerson zwraca a i b, które przedstawiają tę samą osobę co tarczę docelową. Tryb matchFace zwraca wartości, b, c i d&mdash;dokładnie cztery kandydatów, nawet jeśli niektóre nie są takie same, jak w celu lub mają niską podobieństwo. Aby uzyskać więcej informacji, zobacz Przewodnik dotyczący pojęć dotyczących [rozpoznawania](concepts/face-recognition.md) i dokumentacji dotyczącej [ZNAJDOWANIA podobnych interfejsów API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) .

## <a name="face-grouping"></a>Grupowanie twarzy

Interfejs API grupowania dzieli zestaw nieznanych twarzy na kilka grup na podstawie podobieństwa. Każda grupa jest odpowiednim rozłącznym podzestawem oryginalnego zestawu twarzy. Wszystkie powierzchnie w grupie mogą należeć do tej samej osoby. Dla jednej osoby może istnieć kilka różnych grup. Grupy są odróżniane według innego czynnika, takiego jak wyrażenie, na przykład. Aby uzyskać więcej informacji, zobacz Przewodnik dotyczący pojęć dotyczących [rozpoznawania](concepts/face-recognition.md) i dokumentacja [interfejsu API grupy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) .

## <a name="person-identification"></a>Identyfikacja osoby

Identyfikuj interfejs API służy do identyfikowania wykrytej na bazie danych osób. Ta funkcja może być przydatna w przypadku automatycznego tagowania obrazów w oprogramowaniu do zarządzania fotografiami. Baza danych jest tworzona z góry i można ją edytować w miarę upływu czasu.

Na poniższej ilustracji przedstawiono przykład bazy danych o nazwie `"myfriends"`. Każda grupa może zawierać maksymalnie 1 000 000 różnych obiektów osób. Dla każdego obiektu osoby może być zarejestrowanych maksymalnie 248 twarzy.

![Siatka z trzema kolumnami dla różnych osób, z których każdy ma trzy wiersze obrazów](./Images/person.group.clare.jpg)

Po utworzeniu i przeprowadzeniu uczenia bazy danych można przeprowadzić identyfikację grupy z nową wykrytą stroną. Jeśli twarz zostanie zidentyfikowana jako należąca do osoby w grupie, zostanie zwrócony obiekt osoby.

Aby uzyskać więcej informacji na temat identyfikacji osób, zobacz Przewodnik dotyczący pojęć związanych z [rozpoznawaniem](concepts/face-recognition.md) i dokumentacja [interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) .

## <a name="use-containers"></a>Korzystanie z kontenerów

[Użyj kontenera twarzy](face-how-to-install-containers.md) , aby wykrywać, rozpoznawać i identyfikować twarze, instalując ustandaryzowany kontener platformy Docker bliżej Twoich danych.

## <a name="sample-apps"></a>Przykładowe aplikacje

Następujące przykładowe aplikacje pokazują kilka sposobów korzystania z interfejs API rozpoznawania twarzy:

- [Microsoft interfejs API rozpoznawania twarzy: Biblioteka klienta systemu Windows i Przykładowa](https://github.com/Microsoft/Cognitive-Face-Windows) aplikacja platformy WPF, która ilustruje kilka scenariuszy wykrywania, analizy i identyfikacji.
- [FAMILYNOTES platformy UWP App](https://github.com/Microsoft/Windows-appsample-familynotes) to aplikacja platforma uniwersalna systemu Windows (platformy UWP), która korzysta z identyfikacji czołowej oraz mowy, Cortany, atramentu i kamery w scenariuszu udostępniania notatek z rodziny.

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Podobnie jak w przypadku wszystkich zasobów Cognitive Services, deweloperzy korzystający z usługi kroju muszą mieć świadomość zasad firmy Microsoft dotyczących danych klientów. Aby uzyskać więcej informacji, zobacz [stronę Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) w centrum zaufania firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z przewodnikiem Szybki Start, aby zaimplementować scenariusz wykrywania kroju w kodzie:

- [Szybki Start: wykrywanie twarzy w obrazie przy użyciu zestawu .NET SDK C#z ](quickstarts/csharp.md). Dostępne są inne języki.
