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
ms.openlocfilehash: 3293067190386738efbfeb433bd38453c9e5699f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/26/2019
ms.locfileid: "64572556"
---
# <a name="face-detection-and-attributes"></a>Wykrywanie twarzy i atrybuty

W tym artykule opisano pojęcia wykrywanie twarzy i danych atrybutów twarzy. Wykrywanie twarzy jest to akcja lokalizowania ludzkich twarzy na obrazie i opcjonalnie zwracania różnych danych dotyczących rozpoznawania twarzy.

Możesz użyć [twarzy — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) operację, aby wykrywanie twarzy na obrazie. Co najmniej każdej wykrytej odpowiada powierzchni **faceRectangle** pola w odpowiedzi. Jest to zestaw współrzędnych pikseli (po lewej stronie, top, szerokość, wysokość) oznaczanie zlokalizować twarzy. Te współrzędne można uzyskać lokalizacji twarz, a także jego rozmiaru. W odpowiedzi interfejsu API twarzy są wymienione w kolejności rozmiar od największej do najmniejszej wartości.

## <a name="face-id"></a>Identyfikator twarzy

Funkcji face ID jest po prostu ciąg unikatowy identyfikator dla każdego wykrytego twarzy na obrazie. Możesz poprosić o funkcji face ID w swojej [twarzy — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) wywołania interfejsu API.

## <a name="face-landmarks"></a>Punkty charakterystyczne twarzy

To punktów charakterystycznych to zbiór łatwe do znalezienia punkty na powierzchni, takich jak uczniów lub porady czołowych. Domyślnie istnieje 27 wstępnie zdefiniowanych punktów charakterystycznych. Na poniższej ilustracji przedstawiono wszystkie punkty 27:

![Diagramu twarzy ze wszystkich 27 charakterystycznych punktów etykietą](../Images/landmarks.1.jpg)

Współrzędne punktów są zwracane w jednostkach pikseli.

## <a name="attributes"></a>Atrybuty

Atrybuty to zestaw funkcji dodatkową powierzchnię opcjonalnie mogą być wykryte przez [twarzy — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) interfejsu API. Poniżej przedstawiono atrybuty, które mogą zostać wykryte:

* **Wiek** szacowany wiek, lat konkretnej powierzchnię.
* **Rozmycie** blurriness z twarzy na obrazie. To zwraca wartość zero i jeden, a także ocenę nieformalne ("low", "średnie", "wysoka").
* **Narażenia** narażenia twarzy na obrazie. To zwraca wartość zero i jeden, a także ocenę nieformalne ("niedoświetlenia", "goodExposure", "prześwietlenia").
* **Emocji** listę emocje, wykrywanie ich pewnie danej twarzy. Wyniki zaufania są znormalizowane zgodnie: wyniki we wszystkich emocji zostanie dodany do jednego. Emocje, zwracane są szczęście, smutek, Neutralna, gniew, pogarda, obrzydzenie, Zaskoczenie i strach.
* **Zarost** obecności szacowany zarost oraz długość danej twarzy.
* **Płeć** szacowany płeć danej twarzy. Możliwe wartości to "mężczyzna", "żeńskiego" i "genderless".
* **Okulary** czy danej twarzy ma okularów. Możliwe wartości to "NoGlasses", "ReadingGlasses", "Okularów przeciwsłonecznych w Nakierowaniu" i "Swimming okulary".
* **Włosów** styl włosów powierzchni. Pokazuje to, czy włosów jest widoczna, czy baldness jest wykryty i jakie kolory włosów są wykrywane.
* **Stanowić główny** orientacji twarzy w przestrzeni 3D. Jest to opisane pomysłu, wdrożenie i kąta odchylenia w stopniach. Zakresy wartości są [-90, 90], [-180, 180] i [-90, 90] stopni odpowiednio. Zobacz poniższy diagram mapowań kąta:

    ![Główny z pomysłu, wdrażanie i yaw osi etykietą](../Images/headpose.1.jpg)
* **Korzeń** czy twarz ma korzeń. To zwraca wartość logiczną parametru "eyeMakeup" i "lipMakeup".
* **Szumu** visual hałas wykryte w obraz twarzy. To zwraca wartość zero i jeden, a także ocenę nieformalne ("low", "średnie", "wysoka").
* **Zamknięcia** czy istnieją obiekty blokuje części powierzchni. To zwraca wartość logiczną parametru "eyeOccluded", "foreheadOccluded" i "mouthOccluded".
* **Uśmiech** wyrażenia uśmiech danej twarzy. Jest to wartość z zakresu od zera (nie uśmiech) i jeden (Wyczyść uśmiech).

> [!IMPORTANT]
> Atrybuty twarzy oczekuje przy użyciu algorytmów statystycznych i nie zawsze są dokładne. Należy zachować ostrożność podczas podejmowania decyzji na podstawie danych atrybutu.

## <a name="input-data"></a>Dane wejściowe

Użyj następujących wskazówek, aby upewnij się, że Twoje obrazy wejściowe zapewniają najbardziej dokładne wyniki wykrywania:

* Formaty obrazu wejściowego obsługiwane są JPEG, PNG, GIF (pierwsza ramka), BMP.
* Rozmiar pliku obrazu powinna być nie większy niż 4 MB.
* Zakres rozmiaru wykrywalny twarzy to 36 x 36 do 4096 x 4096 pikseli. Nie można wykryć twarzy poza tym zakresem.
* Niektóre twarzy mogą nie zostać wykryte ze względu na problemy techniczne. Extreme kąty twarzy (poza siedzibę) lub zamknięcia twarzy (obiekty, takie jak okularów przeciwsłonecznych w nakierowaniu lub ręce blokuje części powierzchni) może mieć wpływ na wykrywanie. Czołowa i niemal czołowego twarzy zapewniają najlepsze rezultaty.

## <a name="next-steps"></a>Kolejne kroki

Skoro znasz koncepcji wykrywania twarzy, Dowiedz się, jak napisać prosty skrypt, który wykrywa twarze danego obrazu.

* [Jak wykrywanie twarzy na obrazie](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)