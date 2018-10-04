---
title: Czym jest narzędzie do wyszukiwania anomalii? — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
description: Użyj zaawansowanych algorytmów w narzędzie do wyszukiwania anomalii, aby identyfikować anomalie w danych szeregów czasowych i zwracają informacje w usługach Microsoft Cognitive Services.
services: cognitive-services
author: tonyxing
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 04/19/2018
ms.author: tonyxing
ms.openlocfilehash: 2a0715d3becf695600ed84edbae38151acf055a8
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48246846"
---
# <a name="what-is-anomaly-finder"></a>Czym jest narzędzie do wyszukiwania anomalii?

[!INCLUDE [PrivatePreviewNote](../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Narzędzie do wyszukiwania anomalii pozwala na monitorowanie danych wraz z upływem czasu i wykrywanie anomalii w usłudze machine learning, która dostosowuje się do unikatowe dane, automatycznie stosując bezpośrednio statystyczne modelu niezależnie od branży, scenariusz lub ilości danych. Jako dane wejściowe, używając szeregu czasowego, zwraca interfejs API wyszukiwania anomalii punktu danych jest anomalii, czy też nie określa oczekiwaną wartość i górne i dolne granice dla wizualizacji. Jako wstępnie utworzone usługi sztucznej Inteligencji narzędzie do wyszukiwania anomalii nie wymaga Dowolna maszyna z uczenia doświadczenie poza zrozumienia sposobu używania interfejsu API RESTful. Ułatwia to projektowanie proste i wszechstronne ponieważ współpracuje z dowolnym danych szeregów czasowych i mogą również być wbudowane w danych przesyłanych strumieniowo systemów. Narzędzie do wyszukiwania anomalii obejmują szeroki zakres zastosowań — na przykład financial narzędzia do zarządzanie oszustwa, kradzież, zmiana rynków i potencjalne zdarzenia biznesowe lub monitorowanie ruchu urządzenia IoT, przy jednoczesnym zachowaniu anonimowości. To rozwiązanie może również sprzedawane w jako część usługi dla klientów końcowych zrozumieć zmiany danych, wydatki, zwrot z inwestycji lub aktywności użytkownika.
Wypróbuj interfejs API wyszukiwania anomalii i lepiej zrozumieć dane. 

Zobacz, co można skompilować przy użyciu tego interfejsu API:

* Dowiedz się, jak przewidzieć oczekiwanych wartości, na podstawie danych historycznych w szeregach czasowych
* Sprawdzić, czy punkt danych jest anomalii poza wzorzec historycznych
* Generuj obiekt band wizualizacji zakresem wartości "normal"

![Anomaly_Finder](./media/anomaly_detection1.png) 

Rysunek 1: Wykrycia anomalii w sprzedaży, przychodów

![Anomaly_Finder](./media/anomaly_detection2.png)

Rysunek 2: Wykrywanie zmian wzorców w żądania obsługi

## <a name="requirements"></a>Wymagania

- Minimalną ilość danych na dane wejściowe czas serii: co najmniej 13 danych punkty dla szeregów czasowych bez wyczyść okresowości, co najmniej 4 cykle danych punkty dla szeregów czasowych za pomocą znanych okresowości. 
- Integralność danych: czas serii punktów danych są oddzielone w tym samym interwale i nie brakujących punktów. 

## <a name="identify-anomalies"></a>Identyfikować anomalie

Interfejs API wykrywania anomalii zwraca wynik, czy wszystkie punkty danych są anomalie, czy nie, a także dodatkowe informacje znajdują się w następujący sposób
* Okres — wskazuje okresowości, używany przez interfejs API wykrywania anomalii.
* WarningText — możliwe ostrzeżenia.
* ExpectedValue — wartością prognozowaną ucząc model oparty na protokole
* IsAnomaly — wynik dla tego, czy punkty danych są anomalie, czy nie
* IsAnomaly_Neg — wynik dla tego, czy punkty danych są anomalie w kierunku ujemna (spadku)
* IsAnomaly_Pos — wynik dla tego, czy punkty danych są anomalie w kierunku dodatnią (wzrostów)
* UpperMargin — Suma ExpectedValue i UpperMargin okaże się, że górna granica, która punktu danych jest nadal traktować w zwykły sposób
* LowerMargin - (ExpectedValue - LowerMargin) określa dolna granica, że punkt danych nadal jest uznawane za w zwykły sposób

> [!Note]
> UpperMargin i LowerMargin może służyć do generowania poza pasmem w całym rzeczywisty czas serii, aby wizualizować zakres wartości Normalny. 

## <a name="adjusting-lower-and-upper-bounds-in-post-processing-on-the-response"></a>Dostosowywanie dolną i górną granicę w wpis przetwarzania na odpowiedź

Interfejs API wykrywania anomalii zwróci wynik domyślny czy punktu danych jest anomalii, czy nie, a górną i dolną granicę, można obliczyć z ExpectedValue i UpperMargin/LowerMargin. Te wartości domyślne powinny działać prawidłowo w większości przypadków. Jednak niektóre scenariusze wymagają granice innego niż domyślne. Rozwiązanie zaleca się są stosowane coefficiency na UpperMargin lub LowerMargin dopasować granice dynamicznych.

### <a name="examples-with-1152-as-coefficiency"></a>Przykłady z 1/1.5/2 jako coefficiency

![Domyślna ważność](./media/sensitivity_1.png)

![1.5 czułość](./media/sensitivity_1.5.png)

![Czułość 2](./media/sensitivity_2.png)

Żądania z przykładowymi danymi

[!INCLUDE [Request](./includes/request.md)]

Przykładowa odpowiedź JSON

[!INCLUDE [Response](./includes/response.md)]
