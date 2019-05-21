---
title: Wykrywanie twarzy i pojęcia dotyczące atrybutów
titleSuffix: Azure Cognitive Services
description: Pojęcia dotyczące wykrywanie twarzy i atrybutów twarzy.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: e61048eeab9d7061c18f3237db22fc87ca52f526
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65891153"
---
# <a name="face-detection-and-attributes"></a>Wykrywanie twarzy i atrybuty

W tym artykule opisano pojęcia wykrywanie twarzy i danych atrybutów twarzy. Wykrywanie twarzy jest to akcja lokalizowania ludzkich twarzy na obrazie i opcjonalnie zwracania różnych rodzajów danych związanych z rozpoznawania twarzy.

Możesz użyć [twarzy — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) operację, aby wykrywanie twarzy na obrazie. Co najmniej każdej twarzy wykryte odnosi się do pola faceRectangle w odpowiedzi. Ten zestaw współrzędnych pikseli od lewej górnej, szerokość i wysokość oznaczyć zlokalizować twarzy. Te współrzędne można uzyskać lokalizacji twarzy i jego rozmiaru. W odpowiedzi interfejsu API twarzy są wymienione w kolejności rozmiar od największej do najmniejszej wartości.

## <a name="face-id"></a>Identyfikator twarzy

Funkcji face ID jest ciągiem Unikatowy identyfikator dla każdego wykrytego twarzy na obrazie. Możesz poprosić o funkcji face ID w swojej [twarzy — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) wywołania interfejsu API.

## <a name="face-landmarks"></a>Punkty charakterystyczne twarzy

To punktów charakterystycznych to zbiór łatwe do znalezienia punkty na powierzchni, takich jak uczniów lub porady czołowych. Domyślnie istnieje 27 wstępnie zdefiniowanych punktów charakterystycznych. Na poniższej ilustracji przedstawiono wszystkie punkty 27:

![Diagramu twarzy ze wszystkich 27 charakterystycznych punktów etykietą](../Images/landmarks.1.jpg)

Współrzędne punktów są zwracane w jednostkach pikseli.

## <a name="attributes"></a>Atrybuty

Atrybuty to zestaw funkcji, które opcjonalnie mogą być wykryte przez [twarzy — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) interfejsu API. Może zostać wykryte następujące atrybuty:

* **Wiek**. Szacowany wiek w latach konkretnej powierzchnię.
* **Rozmycie**. Blurriness z twarzy na obrazie. Ten atrybut zwraca wartość od zera i jeden do nieformalne ocena niski, średni lub wysoki.
* **Emocji**. Lista emocje, wykrywanie ich pewnie danej twarzy. Wyniki zaufania są znormalizowane zgodnie, a wyniki we wszystkich emocje, Dodaj do jednego. Emocje, zwracane są szczęście, smutek, Neutralna, gniew, pogarda, obrzydzenie, Zaskoczenie i strach.
* **Narażenia**. Narażenie twarzy na obrazie. Ten atrybut zwraca wartość od zera i jeden do niedoświetlenia, goodExposure lub prześwietlenia klasyfikacji nieformalnej.
* **Zarost**. Obecność szacowany zarost i długość dla danej twarzy.
* **Płeć**. Szacowany płeć danej twarzy. Możliwe wartości to mężczyzna żeńskiego i genderless.
* **Okulary**. Czy danej twarzy ma okularów. Możliwe wartości to NoGlasses ReadingGlasses, okularów przeciwsłonecznych w Nakierowaniu i okulary Swimming.
* **Włosów**. Typ włosów powierzchni. Ten atrybut zawiera czy włosów jest widoczna, czy baldness jest wykryty i jakie kolory włosów są wykrywane.
* **Stanowić główny**. Orientacja twarzy w przestrzeni 3D. Ten atrybut jest opisana przez pomysłu, wdrożenie i kąta odchylenia w stopniach. Zakresy wartości są stopniach-90 do 90 stopni, - 180 stopni do 180 stopni i stopni-90 do 90 stopni. Zobacz poniższy diagram mapowań kąta:

    ![Główny z pomysłu, wdrażanie i yaw osi etykietą](../Images/headpose.1.jpg)
* **Korzeń**. Czy twarz ma korzeń. Ten atrybut zwraca wartość typu Boolean eyeMakeup i lipMakeup.
* **Szumu**. Wizualne hałas wykryte w obraz twarzy. Ten atrybut zwraca wartość od zera i jeden do nieformalne ocena niski, średni lub wysoki.
* **Zamknięcia**. Czy istnieją obiekty blokowania części powierzchni. Ten atrybut zwraca wartość typu Boolean eyeOccluded, foreheadOccluded i mouthOccluded.
* **Uśmiech**. Wyrażenie uśmiech danej twarzy. Ta wartość jest między zero, aby nie uśmiech i jeden dla uśmiech Wyczyść.

> [!IMPORTANT]
> Atrybuty twarzy są przewidzieć przy użyciu algorytmów statystycznych. Nie zawsze są dokładne. Należy zachować ostrożność podczas podejmowania decyzji na podstawie danych atrybutu.

## <a name="input-data"></a>Dane wejściowe

Skorzystaj z następujących porad, aby upewnić się, że obrazy wejściowe zapewniają najbardziej dokładne wyniki wykrywania:

* Formaty obrazu wejściowego obsługiwane są JPEG, PNG, GIF dla pierwszej ramki i BMP.
* Rozmiar pliku obrazu powinna być nie większy niż 4 MB.
* Zakres rozmiaru wykrywalny twarzy to 36 x 36 do 4096 x 4096 pikseli. Nie można wykryć twarzy poza tym zakresem.
* Niektóre twarzy mogą nie zostać wykryte ze względu na problemy techniczne. Extreme kąty twarzy (poza siedzibę) lub zamknięcia twarzy (obiekty, takie jak okularów przeciwsłonecznych w nakierowaniu lub ćwiczenia, które blokują części powierzchni) może mieć wpływ na wykrywanie. Czołowa i niemal czołowego twarzy zapewniają najlepsze rezultaty.

W przypadku wykrycia twarze na nagraniach wideo z kanału informacyjnego, można zwiększyć wydajność przez dostosowanie niektórych ustawień na kamery wideo:

* **Wygładzanie**: Wiele kamery wideo wygładzania efekt. Należy wyłączyć to jeśli to możliwe, ponieważ tworzy Rozmycie między ramek i zmniejsza przejrzystości.
* **Migawki szybkość**: Szybsze migawki zmniejsza ilość ruchu między ramek i sprawia, że każdej ramce bardziej zrozumiały. Firma Microsoft zaleca migawki szybkości 1/60 sekundy lub szybciej.
* **Migawki kąt**: Niektóre aparaty fotograficzne, określ kąt migawki zamiast migawki. Jeśli to możliwe należy używać niższe kąt migawki. Spowoduje to bardziej zrozumiały klatki wideo.

    >[!NOTE]
    > Kamery z niższym kąt migawki otrzyma mniej światła w każdej klatce, obraz, który będzie ciemniejszy. Należy określić odpowiedni poziom do użycia.

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz już pojęcia dotyczące wykrywania twarzy, Dowiedz się, jak napisać skrypt, który wykrywa twarze danego obrazu.

* [Wykrywanie twarzy na obrazie](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)