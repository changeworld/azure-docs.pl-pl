---
title: Co to jest anomalii wyszukiwania? -Microsoft kognitywnych usług | Dokumentacja firmy Microsoft
description: Umożliwia zaawansowane algorytmy w wyszukiwarce anomalii zidentyfikować anomalii w czasie serii danych i zwracają informacje w kognitywnych usług firmy Microsoft.
services: cognitive-services
author: tonyxing
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 04/19/2018
ms.author: tonyxing
ms.openlocfilehash: 1080bb0ad1d901a8b9a5ace4993d4e0d46924a03
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348180"
---
# <a name="what-is-anomaly-finder"></a>Co to jest anomalii wyszukiwania?

Wyszukiwanie anomalii pozwala na monitorowanie danych wraz z upływem czasu i wykrywanie anomalii z uczenia maszynowego, która dostosowuje się do danych unikatowy automatycznie stosując prawo statystyczne modelu niezależnie od branży, scenariusz lub danych woluminu. Przy użyciu szeregów czasowych jako dane wejściowe, zwraca anomalii wyszukiwania z interfejsu API czy punktu danych jest anomalii określa oczekiwaną wartością i górne i dolne granice dla wizualizacji. Jako wbudowane usługi AI anomalii wyszukiwania nie wymaga żadnej maszyny uczenia doświadczenia poza zrozumieć, jak użyć interfejsu API RESTful. Dzięki temu programowanie proste i elastyczne od współpracuje z żadnych danych serii czasu, a także można wbudować do przesyłania strumieniowego systemów. Wyszukiwanie anomalii obejmuje szeroki zakres przypadki użycia — na przykład finansowe narzędzia do zarządzania oszustwa, kradzież, zmiana rynków i potencjalne zdarzenia biznesowe lub monitorowania ruchu urządzenia IoT przy zachowaniu anonimowość. To rozwiązanie może być również monetized jako część usługi dla klientów końcowych dowiedzieć się zmiany danych wydatków, zwrot z inwestycji lub aktywności użytkownika.
Wypróbowanie API Finder anomalii i lepiej zrozumieć dane. 

Zobacz, jakie można tworzyć za pomocą tego interfejsu API:

* Dowiedz się do prognozowania na podstawie danych historycznych w szeregu czasowym oczekiwanych wartości
* Sprawdzić, czy punkt danych jest anomalii poza historycznych wzorca
* Generowanie poza pasmem w celu wizualizacji zakresem wartości "normal"

![Anomaly_Finder](./media/anomaly_detection1.png) 

Rysunek 1: Wykrycia anomalii w sprzedaży przychodów

![Anomaly_Finder](./media/anomaly_detection2.png)

Rysunek 2: Wykryj zmiany wzorca w żądania obsługi

## <a name="requirements"></a>Wymagania

- Serie czasu minimalną ilość danych na dane wejściowe: co najmniej 13 danych punkty dla szeregów czasowych bez wyczyść okresowości, co najmniej 4 cykle danych wskazuje szeregu czasowego z znane okresowości. 
- Integralność danych: czas serii punktów danych są rozdzielone samego interwału i nie brakuje punktów. 

## <a name="identify-anomalies"></a>Zidentyfikuj anomalii

Zwraca wynik czy punktów podane dane są anomalii lub nie wykrywania anomalii interfejsu API i dodatkowe informacje znajdują się w następujący sposób
* Okres — okresowości, który interfejsu API używany do wykrywania anomalii punktów.
* WarningText — możliwe ostrzeżenia.
* ExpectedValue - wartością prognozowaną przez naukę na podstawie modelu
* IsAnomaly - wyników na to, czy punkty danych są anomalii lub nie
* IsAnomaly_Neg - wyników na to, czy punkty danych są anomalii w kierunku ujemną (DIP)
* IsAnomaly_Pos - wyników na to, czy punkty danych są anomalii w kierunku dodatnią (nagłego)
* UpperMargin — Suma ExpectedValue i UpperMargin określa górną granicę punktu danych jest nadal traktować jako standardowa
* LowerMargin - (ExpectedValue - LowerMargin) określa dolnej granicy, że punktu danych jest nadal traktować jako standardowa

> [!Note]
> UpperMargin i LowerMargin może służyć do generowania pasmem wokół szeregów czasowych rzeczywiste do wizualizacji normalnej wartości z zakresu. 

## <a name="adjusting-lower-and-upper-bounds-in-post-processing-on-the-response"></a>Dopasowywanie dolną i górną granicę w post przetwarzania w odpowiedzi

Wykrywania anomalii w usłudze API zwraca wynik domyślny czy punktu danych jest anomalii lub nie, a górną i dolną granicą może zostać obliczona z ExpectedValue i UpperMargin/LowerMargin. Te wartości domyślne powinny działać świetnie w większości przypadków. Jednak w niektórych scenariuszach wymagane granice innego niż domyślne. Rozwiązanie zalecane jest stosowania coefficiency na UpperMargin lub LowerMargin dopasować granice dynamicznej.

### <a name="examples-with-1152-as-coefficiency"></a>Przykłady z 1/1.5/2 jako coefficiency

![Czułość domyślne](./media/sensitivity_1.png)

![1.5 czułość](./media/sensitivity_1.5.png)

![Czułość 2](./media/sensitivity_2.png)

Żądania z przykładowymi danymi

[!INCLUDE [Request](./includes/request.md)]

Przykładowa odpowiedź JSON

[!INCLUDE [Response](./includes/response.md)]
