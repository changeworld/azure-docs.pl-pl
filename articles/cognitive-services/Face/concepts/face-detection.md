---
title: Pojęcia dotyczące wykrywania twarzy i atrybutów
titleSuffix: Azure Cognitive Services
description: Wykrywanie twarzy to działanie lokalizowania ludzkich twarzy na obrazie i opcjonalnie zwracania różnych rodzajów danych związanych z twarzą.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: 15e39eb9f5b8dd3556ea9ff8240bc2c9d252cd31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73743056"
---
# <a name="face-detection-and-attributes"></a>Wykrywanie twarzy i atrybuty

W tym artykule opisano pojęcia wykrywania twarzy i danych atrybutów twarzy. Wykrywanie twarzy to działanie lokalizowania ludzkich twarzy na obrazie i opcjonalnie zwracania różnych rodzajów danych związanych z twarzą.

Operacja [Ściana — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) twarzy na obrazie służy do wykrywania twarzy. Co najmniej każda wykryta ściana odpowiada polu faceRectangle w odpowiedzi. Ten zestaw współrzędnych pikseli dla lewej, górnej, szerokości i wysokości oznacza umiejscowiony obiekt. Korzystając z tych współrzędnych, można uzyskać położenie twarzy i jej rozmiar. W odpowiedzi interfejsu API twarze są wyświetlane w kolejności rozmiar od największych do najmniejszych.

## <a name="face-id"></a>Face ID

Identyfikator twarzy jest unikatowym ciągiem identyfikacyjnym dla każdej wykrytej twarzy na obrazie. Możesz poprosić o identyfikator twarzy w [face - Detect wywołanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) interfejsu API.

## <a name="face-landmarks"></a>Punkty charakterystyczne twarzy

Punkty orientacyjne twarzy to zestaw łatwych do znalezienia punktów na twarzy, takich jak źrenice lub czubek nosa. Domyślnie istnieje 27 wstępnie zdefiniowanych punktów charakterystycznych. Na poniższej ilustracji przedstawiono wszystkie 27 punktów:

![Diagram twarzy ze wszystkimi 27 punktami orientacyjnymi oznaczonymi](../Images/landmarks.1.jpg)

Współrzędne punktów są zwracane w jednostkach pikseli.

## <a name="attributes"></a>Atrybuty

Atrybuty to zestaw funkcji, które opcjonalnie mogą być wykrywane przez [interfejs API face — detect.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) Można wykryć następujące atrybuty:

* **Wiek**. Szacowany wiek w latach danej twarzy.
* **Rozmycie**. Rozmazanie twarzy na obrazie. Ten atrybut zwraca wartość między zerem a jednym i nieformalną ocenę niskiej, średniej lub wysokiej.
* **Emocje**. Lista emocji z ich zaufaniem do wykrywania dla danej twarzy. Wyniki zaufania są znormalizowane, a wyniki we wszystkich emocjach sumują się do jednego. Powróciłe emocje to szczęście, smutek, neutralny, gniew, pogarda, obrzydzenie, zaskoczenie i strach.
* **Ekspozycja**. Ekspozycja twarzy na obrazie. Ten atrybut zwraca wartość między zerem a jednym i nieformalną oceną underExposure, goodExposure lub overExposure.
* **Zarost**. Szacowana obecność zarostu i długość dla danej twarzy.
* **Płeć**. Szacowana płeć danej twarzy. Możliwe wartości to mężczyźni, kobiety i bez płci.
* **Okulary**. Czy dana twarz ma okulary. Możliwe wartości to NoGlasses, ReadingGlasses, Sunglasses i Swimming Goggles.
* **Włosy**. Rodzaj włosów twarzy. Ten atrybut pokazuje, czy włosy są widoczne, czy łysienie jest wykrywany, i jakie kolory włosów są wykrywane.
* **Pozy głowy**. Orientacja twarzy w przestrzeni 3D. Ten atrybut jest opisany przez kąty skoku, rolki i odchylenia w stopniach. Zakresy wartości to odpowiednio od -90 stopni do 90 stopni, od -180 stopni do 180 stopni i od -90 stopni do 90 stopni. Schematy kątowe można znaleźć na poniższym diagramie:

    ![Głowica z osiami boiska, rolki i odchylenia z etykietą](../Images/headpose.1.jpg)
* **Makijaż**. Czy twarz ma makijaż. Ten atrybut zwraca wartość logiczną dla okaMakeup i lipMakeup.
* **Hałas**. Szum wizualny wykryty na obrazie twarzy. Ten atrybut zwraca wartość między zerem a jednym i nieformalną ocenę niskiej, średniej lub wysokiej.
* **Okluzja**. Czy istnieją obiekty blokujące części twarzy. Ten atrybut zwraca wartość logiczną dla oczuOccluded, foreheadOccluded i mouthOccluded.
* **Uśmiechnij się**. Wyraz uśmiechu danej twarzy. Ta wartość jest między zero dla bez uśmiechu i jeden dla wyraźnego uśmiechu.

> [!IMPORTANT]
> Atrybuty twarzy są przewidywane za pomocą algorytmów statystycznych. Mogą nie zawsze być dokładne. Należy zachować ostrożność podczas podejmowania decyzji na podstawie danych atrybutów.

## <a name="input-data"></a>Dane wejściowe

Skorzystaj z następujących wskazówek, aby upewnić się, że obrazy wejściowe dają najdokładniejsze wyniki wykrywania:

* Obsługiwane formaty obrazów wejściowych to JPEG, PNG, GIF dla pierwszej klatki i BMP.
* Rozmiar pliku obrazu nie powinien być większy niż 4 MB.
* Wykrywalny zakres rozmiaru twarzy wynosi od 36 x 36 do 4096 x 4096 pikseli. Twarze spoza tego zakresu nie zostaną wykryte.
* Niektóre twarze mogą nie zostać wykryte z powodu problemów technicznych. Ekstremalne kąty twarzy (pozy głowy) lub niedrożność twarzy (obiekty takie jak okulary przeciwsłoneczne lub dłonie, które blokują część twarzy) mogą mieć wpływ na wykrywanie. Czołowe i prawie czołowe twarze dają najlepsze rezultaty.

Jeśli wykrywasz twarze z kanału wideo, możesz poprawić wydajność, dostosowując określone ustawienia kamery wideo:

* **Wygładzanie:** wiele kamer wideo stosuje efekt wygładzania. Należy wyłączyć tę funkcję, jeśli to możliwe, ponieważ powoduje to rozmycie między ramkami i zmniejsza przejrzystość.
* **Czas otwarcia**migawki: krótszy czas otwarcia migawki zmniejsza ruch między klatkami i sprawia, że każda klatka jest wyraźniejsza. Zalecamy czas otwarcia migawki 1/60 sekundy lub szybszy.
* **Kąt otwarcia migawki:** Niektóre aparaty określają kąt otwarcia migawki zamiast czasu otwarcia migawki. Jeśli to możliwe, należy użyć niższego kąta otwarcia migawki. Spowoduje to wyraźniejsze klatki wideo.

    >[!NOTE]
    > Aparat o niższym kącie otwarcia migawki będzie otrzymywać mniej światła w każdej klatce, więc obraz będzie ciemniejszy. Musisz określić odpowiedni poziom do użycia.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz pojęcia dotyczące wykrywania twarzy, dowiedz się, jak napisać skrypt wykrywacy twarze w danym obrazie.

* [Wykrywanie twarzy na obrazie](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)