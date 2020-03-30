---
title: Analizowanie danych urządzenia w aplikacji Azure IoT Central | Dokumenty firmy Microsoft
description: Analizowanie danych urządzenia w aplikacji Azure IoT Central.
author: ankitscribbles
ms.author: ankitgup
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 40460b58ede0ca0da8fe25475906bdbe41bfffe0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158286"
---
# <a name="how-to-use-analytics-to-analyze-device-data"></a>Jak analizować dane urządzenia za pomocą analizy

*Ten artykuł dotyczy operatorów, konstruktorów i administratorów.*



Usługa Azure IoT Central zapewnia zaawansowane funkcje analityczne do analizowania trendów historycznych i korelowania różnych telemetrii z urządzeniami. Aby rozpocząć, odwiedź **Analytics** w lewym okienku.

## <a name="understanding-the-analytics-ui"></a>Opis interfejsu analitycznego
Interfejs użytkownika Analytics składa się z trzech głównych składników:
- **Panel konfiguracji danych:** Na panelu konfiguracji zacznij od wybrania grupy urządzeń, dla której chcesz analizować dane. Następnie wybierz dane telemetryczne, które chcesz analizować i wybierz metodę agregacji dla każdej telemetrii. **Formant Podziel przez** pomaga grupować dane przy użyciu właściwości urządzenia jako wymiarów.

- **Kontrola czasu:** Kontrola czasu służy do wybierania czasu, dla którego chcesz analizować dane. Można przeciągnąć albo koniec suwaka czasu, aby wybrać przedział czasu. Kontrola czasu ma również **suwak rozmiar interwału,** który steruje zasobnikiem lub rozmiarem interwału używanym do agregowania danych. 

- **Sterowanie wykresem:** Kontrola wykresu wizualizuje dane jako wykres liniowy. Widoczność określonych linii można przełączać, komunikując się z legendą wykresu. 


  ![Omówienie interfejsu użytkownika Analytics](media/howto-create-analytics/analyticsui.png)


## <a name="querying-your-data"></a>Wykonywanie zapytań o dane

Należy rozpocząć od wybrania **grupy urządzeń**i danych telemetrycznych, które chcesz przeanalizować. Po zakończeniu wybierz **pozycję Analizuj,** aby rozpocząć wizualizację danych.

- **Grupa urządzeń:** [Grupa urządzeń](tutorial-use-device-groups.md) to zdefiniowana przez użytkownika grupa urządzeń. Na przykład wszystkie lodówki w Oakland lub Wszystkie turbiny wiatrowe w wersji 2.0.

- **Telemetria:** Wybierz dane telemetryczne, które chcesz analizować i eksplorować. Można wybrać wiele telemetrii do wspólnej analizy. Domyślna metoda agregacji jest ustawiona odpowiednio na Średnia dla numerycznych i Liczba dla typu danych ciągu. Obsługiwane metody agregacji dla typów danych liczbowych to Średnia, Maksymalna, Minimalna, Liczba i Suma.  Obsługiwane metody agregacji dla typu danych ciągu są liczą.

- **Podzielone przez:** Kontrola "Podziel według" pomaga grupować dane przy użyciu właściwości urządzenia jako wymiarów. Wartości urządzenia i właściwości chmury są dołączane wraz z telemetrii, gdy jest wysyłany przez urządzenie. Jeśli właściwość chmury lub urządzenia została zaktualizowana, na wykresie zostanie wyświetlona dane telemetryczne pogrupowane według różnych wartości.

    > [!TIP]
    > Aby wyświetlić dane dla każdego urządzenia oddzielnie, wybierz identyfikator urządzenia w formancie "Podziel według".

## <a name="interacting-with-your-data"></a>Interakcja z danymi

Po zapytaniu danych można rozpocząć wizualizację ich na wykresie liniowym. Można wyświetlać/ukrywać dane telemetryczne, zmieniać czas trwania, wyświetlać dane telemetryczne w siatce danych.

- **Panel edytora czasu:** Domyślnie będziemy pobierać dane z ostatniego dnia. Można przeciągnąć albo koniec suwaka czasu, aby zmienić czas trwania. Za pomocą formantu kalendarza można również wybrać jedno ze wstępnie zdefiniowanych zasobników czasu lub wybrać niestandardowy zakres czasu. Kontrola czasu ma również **suwak rozmiar interwału,** który steruje zasobnikiem lub rozmiarem interwału używanym do agregowania danych.

    ![Edytor czasu](media/howto-create-analytics/timeeditorpanel.png)

    - **Narzędzie suwaka wewnętrzny zakres dat:** Użyj dwóch kontrolek punktu końcowego, przeciągając je w odpowiednim przedziale czasu. Ten wewnętrzny zakres dat jest ograniczony przez kontrolkę suwaka zewnętrznego zakresu dat.
    
   
    - **Kontrolka suwaka zewnętrznego zakresu dat:** Użyj kontrolek punktu końcowego, aby wybrać zewnętrzny zakres dat, który będzie dostępny dla wewnętrznej kontroli zakresu dat.

    - **Zwiększanie i zmniejszanie przycisków zakresu dat:** Zwiększ lub zmniejsz swój czas, wybierając jeden z przycisków odpowiedniego interwału.

    - **Suwak o rozmiarze interwału:** służy do powiększania i pomniejszania interwałów w tym samym przedziale czasu. Ta akcja zapewnia bardziej precyzyjną kontrolę ruchu między dużymi wycinkami czasu. Można go używać, aby wyświetlić szczegółowe widoki danych w wysokiej rozdzielczości, nawet w milisekundach. Domyślny punkt początkowy suwaka jest ustawiony jako najbardziej optymalny widok danych z wyboru, który równoważy rozdzielczość, szybkość zapytania i szczegółowość.
    
    - **Selektor zakresu dat:** Dzięki tej formancie sieci Web można łatwo wybrać żądane zakresy dat i godzin. Można również użyć formantu, aby przełączać się między różnymi strefami czasowymi. Po wjedniejszce do bieżącego obszaru roboczego wybierz pozycję Zapisz.

    > [!TIP]
    > Rozmiar interwału jest określany dynamicznie na podstawie wybranego przedziału czasu. Mniejsze przedziały czasowe umożliwi agregowanie danych w bardzo szczegółowe odstępy czasu do kilku sekund.


- **Legenda wykresu:** Legenda wykresu pokazuje wybrane dane telemetryczne na wykresie. Możesz najechać kursorem na każdy element legendy, aby skupić się na wykresie. W przypadku korzystania z opcji "Podziel przez" dane telemetryczne są pogrupowane według odpowiednich wartości wybranego wymiaru. Można przełączyć widoczność poszczególnych danych telemetrycznych lub całej grupy, klikając nazwę grupy.  


- **Sterowanie formatem osi Y:** tryb osi y przechodzi przez dostępne opcje widoku osi Y. Ten formant jest dostępny tylko wtedy, gdy wizualizowane są różne telemetry. Oś y można ustawić, wybierając jeden z trzech trybów:

    - **Skumulowane:** Wykres dla każdej telemetrii jest skumulowany, a każdy z wykresów ma własną oś y. Ten tryb jest ustawiony jako domyślny.
    - **Udostępnione:** Wykres dla każdej telemetrii jest kreślony względem tej samej osi y.
    - **Nakładanie się:** Służy do układania wielu wierszy na tej samej osi y, przy czym dane osi y zmieniają się na podstawie wybranego wiersza.

  ![Rozmieszczanie danych na osi y za pomocą różnych trybów wizualizacji](media/howto-create-analytics/yaxiscontrol.png)

- **Kontrolka powiększenia:** Zoom umożliwia dalsze przechodzenie do szczegółów danych. Jeśli znajdziesz okres, na którym chcesz się skupić w zestawie wyników, użyj wskaźnika myszy, aby pobrać obszar, a następnie przeciągnij go do wybranego punktu końcowego. Następnie kliknij prawym przyciskiem myszy zaznaczony obszar i kliknij polecenie Powiększ.

  ![Powiększanie danych](media/howto-create-analytics/zoom.png)

W obszarze wielokropek istnieje więcej formantów wykresu do interakcji z danymi.

- **Siatka wyświetlania:** Wyniki są dostępne w formacie tabeli, co umożliwia wyświetlanie określonej wartości dla każdego punktu danych.

- **Upuść znacznik:** Kontrolka "Znacznik upuszczania" umożliwia zakotwienie niektórych punktów danych na wykresie. Jest to przydatne, gdy próbujesz porównać dane dla wielu wierszy w różnych okresach.

  ![Wyświetlanie widoku siatki dla analityki](media/howto-create-analytics/additionalchartcontrols.png)