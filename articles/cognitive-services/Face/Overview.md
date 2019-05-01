---
title: Co to jest interfejs API rozpoznawania twarzy?
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać usługi rozpoznawania twarzy do wykrywania i analizowania twarzy na obrazach.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 02/20/2019
ms.author: pafarley
ms.openlocfilehash: 6fba9e6387e60f2156ee01461c3bba22405376fc
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64729560"
---
# <a name="what-is-the-azure-face-api"></a>Co to jest interfejs API rozpoznawania twarzy na platformie Azure?

Interfejs API rozpoznawania twarzy na platformie Azure to usługa Cognitive Service, która obsługuje algorytmy wykrywania, rozpoznawania i analizowania ludzkich twarzy na obrazach. Możliwość przetwarzania informacji o ludzkich twarzach jest ważna w wielu różnych scenariuszach programowych, w tym dotyczących zabezpieczeń, naturalnego interfejsu użytkownika, analizy zawartości obrazu i zarządzania nią, aplikacji mobilnych i robotyki.

Interfejs API rozpoznawania twarzy oferuje kilka różnych funkcji opisanych w poniższych sekcjach. Czytaj dalej, aby dowiedzieć się więcej na temat każdej z nich.

## <a name="face-detection"></a>Wykrywanie twarzy

Interfejs API rozpoznawania twarzy może wykrywać ludzkie twarzy na obrazie i zwracać współrzędne prostokąta ich lokalizacji. Opcjonalnie wykrywanie twarzy można wyodrębnić szereg związane z twarzy atrybutów, takich jak poza siedzibę, płeć, wiek, emocje, zarost i okularów.

> [!NOTE]
> Funkcja wykrywania twarzy jest również dostępna za pośrednictwem [interfejsu API przetwarzania obrazów](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), ale aby wykonywać dalsze operacje przy użyciu danych twarzy, należy użyć interfejsu API rozpoznawania twarzy (tej usługi).

![Obraz przedstawiający kobietę i mężczyznę z prostokątami narysowanymi wokół twarzy oraz wyświetlonym wiekiem i płcią](./Images/Face.detection.jpg)

Aby uzyskać więcej informacji na temat wykrywania twarzy, zobacz [wykrywanie twarzy](concepts/face-detection.md) pojęcia artykule lub Przejdź [API wykrywania](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) dokumentację referencyjną.

## <a name="face-verification"></a>Weryfikacja twarzy

Interfejs API weryfikacji wykonuje uwierzytelnianie względem dwóch wykrytych twarzy lub jednego obiektu osoby na podstawie jednej wykrytej twarzy. W praktyce ocenia on, czy dwie twarze należą do tej samej osoby. Jest to potencjalnie przydatne w scenariuszach związanych z ochroną bezpieczeństwa. Aby uzyskać więcej informacji, zobacz [rozpoznawania twarzy](concepts/face-recognition.md) przewodnik pojęcia lub [Sprawdź API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) dokumentację referencyjną.

## <a name="find-similar-faces"></a>Wyszukiwanie podobnych twarzy

Interfejs API wyszukiwania podobnych twarzy pobiera twarz docelową i zestaw twarzy do przeszukania, a następnie znajduje mały zestaw twarzy, które są najbardziej podobne do twarzy docelowej. Są obsługiwane dwa tryby pracy: **matchPerson** i **matchFace**. Tryb **matchPerson** zwraca podobne twarze po zakończeniu filtrowania pod kątem tej samej osoby (przy użyciu [interfejsu API weryfikacji](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)). Tryb **matchFace** ignoruje filtr dla tej samej osoby i zwraca listę podobnych twarzy, które mogą (lub nie) należeć do jednej osoby.

W poniższym przykładzie to jest twarz docelowa:

![Uśmiechnięta kobieta](./Images/FaceFindSimilar.QueryFace.jpg)

A to są twarze do przeszukania:

![Pięć obrazów uśmiechniętych osób. Obrazy a) i b) przedstawiają tę samą osobę](./Images/FaceFindSimilar.Candidates.jpg)

W przypadku wyszukiwania czterech podobnych twarzy tryb **matchPerson** zwraca obrazy (a) i (b), czyli twarze należące do tej samej osoby, co twarz docelowa. Tryb **matchFace** zwraca obrazy (a), (b), (c) i (d) &mdash; dokładnie cztery wybrane obrazy, nawet jeśli niektóre z nich przedstawiają inne osoby lub jeśli podobieństwo jest niewielkie. Aby uzyskać więcej informacji, zobacz [rozpoznawania twarzy](concepts/face-recognition.md) przewodnik pojęcia lub [znaleźć podobne API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) dokumentację referencyjną.

## <a name="face-grouping"></a>Grupowanie twarzy

Interfejs API grupowania dzieli zestaw nieznanych twarzy na kilka grup na podstawie podobieństwa. Każda grupa jest odpowiednim rozłącznym podzestawem oryginalnego zestawu twarzy. Wszystkie twarze w grupie prawdopodobnie należą do tej samej osoby, ale dla jednej osoby może istnieć kilka różnych grup (różniących się jednym elementem, na przykład wyrazem twarzy). Aby uzyskać więcej informacji, zobacz [rozpoznawania twarzy](concepts/face-recognition.md) przewodnik koncepcje lub [API grupy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) dokumentację referencyjną.

## <a name="person-identification"></a>Identyfikacja osoby

Interfejs API identyfikacji może służyć do identyfikowania wykrytej twarzy względem bazy danych osób. Może to być przydatne w przypadku automatycznego tagowania obrazów w oprogramowaniu do zarządzania zdjęciami. Utwórz bazę wcześniej, aby móc edytować ją z upływem czasu.

Poniższa ilustracja przedstawia przykład bazy danych o nazwie „myfriends”. Każda grupa może zawierać maksymalnie 1 000 000 różnych obiektów osób, a każdy obiekt osoby może mieć maksymalnie 248 zarejestrowanych twarzy.

![Siatka z 3 kolumnami dla różnych osób, każda z 3 wierszami obrazów twarzy](./Images/person.group.clare.jpg)

Po utworzeniu i przeprowadzeniu uczenia bazy danych można wykonać identyfikację nowo wykrytej twarzy względem grupy. Jeśli twarz zostanie zidentyfikowana jako należąca do osoby w grupie, zostanie zwrócony obiekt osoby.

Aby uzyskać więcej informacji na temat identyfikacji osoby zobacz [rozpoznawania twarzy](concepts/face-recognition.md) przewodnik pojęcia lub [identyfikowania interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) dokumentację referencyjną.

## <a name="use-containers"></a>Korzystanie z kontenerów

Po zainstalowaniu standardowego kontenera platformy Docker blisko danych można wykrywać, rozpoznawać oraz identyfikować twarze, korzystając z [kontenera rozpoznawania twarzy](face-how-to-install-containers.md).

## <a name="sample-apps"></a>Przykładowe aplikacje

Następujące przykładowe aplikacje pokazują kilka sposobów używania interfejsu API rozpoznawania twarzy.

- [Interfejs API rozpoznawania twarzy firmy Microsoft: biblioteka klienta systemu Windows i przykład](https://github.com/Microsoft/Cognitive-Face-Windows) — przykładowa aplikacja WPF przedstawiająca kilka scenariuszy wykrywania, analizy i identyfikacji twarzy.
- [Aplikacja platformy UWP FamilyNotes](https://github.com/Microsoft/Windows-appsample-familynotes) — aplikacja platformy uniwersalnej systemu Windows (UWP), która używa funkcji identyfikacji twarzy wraz z funkcją rozpoznawania mowy, Cortaną, funkcją pisma odręcznego i kamerą w scenariuszu udostępniania notatek w rodzinie.

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Podobnie jak w przypadku wszystkich usług Cognitive Services, deweloperzy korzystający z usługi rozpoznawania twarzy powinni wiedzieć o zasadach firmy Microsoft dotyczących danych klientów. Zobacz [stronę usług Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) w Centrum zaufania firmy Microsoft, aby dowiedzieć się więcej.

## <a name="next-steps"></a>Kolejne kroki

Wykonaj czynności opisane w przewodniku Szybki start, aby zaimplementować prosty scenariusz wykrywania twarzy w kodzie.

- [Szybki start: wykrywanie twarzy na obrazie za pomocą zestawu .NET SDK przy użyciu języka C#](quickstarts/csharp.md) (inne języki są dostępne)
