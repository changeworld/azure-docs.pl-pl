---
title: Co to jest interfejs API rozpoznawania twarzy?
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać usługi rozpoznawania twarzy do wykrywania i analizowania twarzy na obrazach.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: 4be33f781dec93fd9fe1b1846322672266cd7350
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606909"
---
# <a name="what-is-the-azure-face-api"></a>Co to jest interfejs API rozpoznawania twarzy na platformie Azure?

Azure Cognitive Services Face API zapewnia algorytmy, które są używane do wykrywania, rozpoznaje i analizować ludzkie twarze na obrazach. Możliwość przetwarzania informacji ludzkich twarzy jest ważne w wielu scenariuszach różnego oprogramowania. Przykładowe scenariusze są zabezpieczenia, interfejs użytkownika fizycznych, analiza zawartości obrazu i zarządzania, aplikacje mobilne i związane z robotyką.

Interfejs API rozpoznawania twarzy zawiera kilka różnych funkcji. Każda funkcja jest opisany w poniższych sekcjach. Czytaj dalej, aby dowiedzieć się więcej o nich.

## <a name="face-detection"></a>Wykrywanie twarzy

Interfejs API twarzy wykrywa twarze osób na obrazie i zwraca współrzędnych prostokąta ich lokalizacji. Opcjonalnie wykrywanie twarzy można wyodrębnić szereg atrybuty dotyczące twarzy. Przykładami są poza siedzibę, płeć, wiek, emocje, zarost i okularów.

> [!NOTE]
> Funkcja wykrywania twarzy jest również dostępna za pośrednictwem [interfejs API przetwarzania obrazów](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home). Jeśli chcesz dalsze operacje przy użyciu danych rozpoznawania twarzy, należy użyć interfejsu API rozpoznawania twarzy, czyli usługi omówionych w tym artykule.

![Obraz: kobieta i mężczyzna, z prostokątami rysowane wokół ich twarzy i wieku oraz płci wyświetlane](./Images/Face.detection.jpg)

Aby uzyskać więcej informacji na temat wykrywania twarzy, zobacz [wykrywanie twarzy](concepts/face-detection.md) pojęcia artykułu. Zobacz też [API wykrywania](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) dokumentację referencyjną.

## <a name="face-verification"></a>Weryfikacja twarzy

Interfejs API weryfikacji wykonuje uwierzytelnianie względem dwóch wykrytych twarzy lub jednego obiektu osoby na podstawie jednej wykrytej twarzy. W praktyce ocenia on, czy dwie twarze należą do tej samej osoby. Ta możliwość jest potencjalnie przydatne w scenariuszach bezpieczeństwa. Aby uzyskać więcej informacji, zobacz [rozpoznawania twarzy](concepts/face-recognition.md) przewodnik pojęcia lub [Sprawdź API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) dokumentację referencyjną.

## <a name="find-similar-faces"></a>Wyszukiwanie podobnych twarzy

Znajdź API podobne porównuje twarzy docelowy zestaw twarzy Release candidate można znaleźć mniejszy zestaw twarzy, które będą przypominać twarzy docelowego. Dwa tryby pracy matchPerson i matchFace, są obsługiwane. Tryb matchPerson zwraca podobnych twarzy filtry dla tej samej osoby za pomocą [Sprawdź API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). Tryb matchFace ignoruje filtr o tej samej osoby. Zwraca listę podobnych twarzy kandydujących, którzy mogą lub nie może należeć do jednej osoby.

Poniższy przykład przedstawia twarzy docelowej:

![Uśmiechnięta kobieta](./Images/FaceFindSimilar.QueryFace.jpg)

A to są twarze do przeszukania:

![Pięć obrazów uśmiechniętych osób. Obrazy i b wskazują tę samą osobę.](./Images/FaceFindSimilar.Candidates.jpg)

Aby znaleźć cztery podobnych twarzy, zwraca tryb matchPerson i b, które pokazują ta sama osoba twarzy docelowego. Zwraca tryb matchFace a, b, c i d, dokładnie cztery kandydatów, nawet jeśli niektóre nie są w tej samej osoby jako element docelowy lub ma niski podobieństwa. Aby uzyskać więcej informacji, zobacz [rozpoznawania twarzy](concepts/face-recognition.md) przewodnik pojęcia lub [znaleźć podobne API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) dokumentację referencyjną.

## <a name="face-grouping"></a>Grupowanie twarzy

Interfejs API grupowania dzieli zestaw nieznanych twarzy na kilka grup na podstawie podobieństwa. Każda grupa jest odpowiednim rozłącznym podzestawem oryginalnego zestawu twarzy. Wszystkie twarze na grupy mogą należeć do tej samej osoby. Może istnieć kilka różnych grup dla jednej osoby. Grupy są zróżnicowane według innym czynnikiem, takie jak wyrażenie, na przykład. Aby uzyskać więcej informacji, zobacz [rozpoznawania twarzy](concepts/face-recognition.md) przewodnik koncepcje lub [API grupy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) dokumentację referencyjną.

## <a name="person-identification"></a>Identyfikacja osoby

API identyfikacji służy do identyfikowania wykryte twarzy na bazie danych osób. Ta funkcja może być przydatne do automatycznego obraz znakowania w oprogramowanie do zarządzania zdjęć. Wcześniej utworzyć bazę danych i można go edytować wraz z upływem czasu.

Na poniższej ilustracji przedstawiono przykład bazy danych o nazwie `"myfriends"`. Każda grupa może zawierać obiekty innej osobie do 1 miliona. Dla każdego obiektu osoby może być zarejestrowanych maksymalnie 248 twarzy.

![Siatka zawierająca trzy kolumny dla różnych osób, każdy z trzema wierszami, obrazów twarzy](./Images/person.group.clare.jpg)

Po utworzeniu i uczenie bazy danych można wykonywać identyfikacji względem grupy za pomocą nowej twarzy wykryte. Jeśli twarz zostanie zidentyfikowana jako należąca do osoby w grupie, zostanie zwrócony obiekt osoby.

Aby uzyskać więcej informacji na temat identyfikacji osoby zobacz [rozpoznawania twarzy](concepts/face-recognition.md) przewodnik pojęcia lub [identyfikowania interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) dokumentację referencyjną.

## <a name="use-containers"></a>Korzystanie z kontenerów

[Użyj kontenera twarzy](face-how-to-install-containers.md) do wykrywania, rozpoznaje i identyfikowanie twarzy, instalując bliżej standardowy kontener platformy Docker do swoich danych.

## <a name="sample-apps"></a>Przykładowe aplikacje

Następujące przykładowe aplikacje przedstawiają kilka sposobów korzystania z interfejsu API rozpoznawania twarzy:

- [Interfejs API rozpoznawania twarzy firmy Microsoft: Biblioteka klienta Windows i przykładowa](https://github.com/Microsoft/Cognitive-Face-Windows) jest aplikacją programu WPF, która pokazuje kilka scenariuszy wykrywania twarzy, analizy i identyfikacji.
- [Aplikacja platformy UWP FamilyNotes](https://github.com/Microsoft/Windows-appsample-familynotes) jest aplikacją platformy uniwersalnej Windows (UWP), używa twarzy identyfikacji wraz z funkcji rozpoznawania mowy, Cortana, pisma odręcznego i kamery w przypadku udostępniania Uwaga rodziny.

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Podobnie jak w przypadku wszystkich zasobów usług Cognitive Services deweloperzy, którzy korzystają z usługi rozpoznawania twarzy musi mieć świadomość zasady firmy Microsoft w sprawie danych klienta. Aby uzyskać więcej informacji, zobacz [strony usług Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na Microsoft Trust Center.

## <a name="next-steps"></a>Kolejne kroki

Postępuj zgodnie z szybkiego startu, aby zaimplementować scenariusz wykrywanie twarzy w kodzie:

- [Szybki start: Wykrywanie twarzy na obrazie za pomocą zestawu SDK platformy .NET przy użyciu C# ](quickstarts/csharp.md). Inne języki są dostępne.
