---
title: Analizuj dane urządzenia w aplikacji usługi Azure IoT Central | Dokumentacja firmy Microsoft
description: Analizuj dane urządzenia w aplikacji usługi Azure IoT Central.
author: lmasieri
ms.author: lmasieri
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: d0b964c089e8aa234cd5eee56413ff56599ad5df
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2019
ms.locfileid: "57307279"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>Jak używać usługi analytics do analizowania danych urządzenia


*Ten artykuł dotyczy operatorów, konstruktorów i administratorów.*


Usługa Azure IoT Central zapewnia bogate możliwości analizy możliwości sensu dużych ilości danych z urządzeń. Aby rozpocząć pracę, odwiedź stronę **Analytics** w menu nawigacji po lewej stronie. 

  ![IoT Central nawigację do analizy](media/howto-create-analytics/analytics-navigation.png)

## <a name="querying-your-data"></a>Wykonywanie zapytań do danych

Musisz wybrać **zestaw urządzeń**, Dodaj **filtru** (opcjonalnie) i wybierz **okres** na rozpoczęcie pracy. Po wykonaniu tych czynności wybierz **Pokaż wyniki** można uruchomić wizualizowania danych.


* **Zestawy urządzeń:** A [zestaw urządzeń](howto-use-device-sets.md) jest zdefiniowane przez użytkownika grupy urządzeń. Na przykład wszystkie lodówki w Oakland lub wszystkich wer. 2.0 turbiny wiatru.

<!---
to-do: confirm if 10 is the max number of filters
to-do: do we need to explain how fiters work?
--->

* **Filtry:** Opcjonalnie można dodać filtry do wyszukiwania w taki sposób, aby stworzyć na podstawie posiadanych danych. W czasie, można dodać filtry do 10. Na przykład w ramach wszystkich lodówki w Oakland, znaleźć go te, które miały temperatura powyżej 60 stopni. 
* **Okres:** Firma Microsoft, który domyślnie będzie pobierać dane z ostatnich 10 minut. Możesz zmienić tę wartość do jednego z zakresów wstępnie zdefiniowanego czasu, lub wybrać niestandardowy okres. 

 ![Zapytanie analizy](media/howto-create-analytics/analytics-query.png)

## <a name="visualizing-your-data"></a>Wizualizowanie danych

Po zostało zniekształcone danych będzie możliwe jej uruchomienie go wizualizacji. Użytkownik może Pokaż/Ukryj pomiarów, zmiana sposobu, w których dane są agregowane i dalsze podzielenie danych za pomocą właściwości innego urządzenia.  

* **Podziel według:** Dzielenie danych według właściwości urządzenia pozwala na dalsze Przechodzenie do szczegółów w swoje dane. Na przykład możesz podzielić wyniki według Identyfikatora urządzenia lub lokalizacji.
<!---
to-do: confirm if 10 is the max number of measurements
--->
* **Pomiary:** Można je pokazać lub ukryć maksymalnie 10 elementów różnych danych telemetrycznych zgłaszane przez urządzenia w danym momencie. Pomiary są elementy, takie jak temperatury i wilgotności. 
* **Agregacji:** Domyślnie możemy agregować dane według jego średniej, ale można zmienić sposobu agregacji danych się czymś innym, odpowiednio do potrzeb. 

   ![Analiza wizualizacji](media/howto-create-analytics/analytics-visualize.png) <br/><br/>
   ![Wizualizacja Analytics podział według](media/howto-create-analytics/analytics-splitby.png)

## <a name="interacting-with-your-data"></a>Interakcje z danymi

Masz różne sposoby, w których można zmienić dalsze wyniki zapytania do własnych potrzeb wizualizacji. Można zamiennie z widoku wykresu i widoku siatki, powiększać/pomniejszać, odświeżanie zestawu danych i zmienić sposób wyświetlania wierszy.

* **Pokaż siatkę:** Wyniki wyszukiwania będą dostępne w postaci tabeli, które umożliwia wyświetlenie określonej wartości dla każdego punktu danych. Ten widok jest również zgodna z standardów ułatwień dostępu. 
* **Pokaż wykres:** Wyniki będą wyświetlane w formacie wiersza, aby łatwo chętnych górę/dół trendów i anomalii. 

 ![Wyświetlanie widoku siatki do celów analizy](media/howto-create-analytics/analytics-showgrid.png)

Powiększenie umożliwia możesz skoncentrować się na podstawie posiadanych danych. Jeśli okaże się okres czasu, którą chcesz się skupić w ramach zestawu wyników, należy użyć kursor do pobrania obszar, który chcesz powiększyć, a następnie wykonaj jedną z następujących czynności za pomocą dostępne kontrolki:
* **Powiększ:** Po wybraniu okres powiększenia w zostanie włączone i umożliwiają powiększyć do swoich danych.
* **Pomniejsz:** Ten formant umożliwia jeden poziom pomniejszenie usługi ostatni powiększenia. Na przykład przy powiększeniu już danych trzy razy, pomniejszania zajmie kopii jeden krok w danym momencie.
* **Resetuj Powiększenie:** Po zostało wykonane różne poziomy powiększania, można użyć resetowania powiększenia, aby powrócić do oryginalnego zestawu wyników. 

 ![Wykonaj powiększanie na podstawie danych](media/howto-create-analytics/analytics-zoom.png)


Możesz zmienić styl linii do własnych potrzeb. Dostępne cztery opcje do wyboru:
* **Wiersz:** Płaski wiersz między poszczególnymi punktami danych zostaną utworzone. 
* **Gładki:** Krzywa linię między każdym punkcie będzie sformatowany
* **Krok:** Wiersz między każdy punkt wykresu utworzy wykres kroku
* **Wykres punktowy:** Wszystkie punkty zostaną nakreślone na wykresie bez linii, łącząc je. 

 ![Innym wierszu typów dostępnych w usłudze Analytics](media/howto-create-analytics/analytics-linetypes.png)

Ponadto można rozmieścić dane w osi y, wybierając jedną z trzech trybów:

* **Skumulowany:** Wykres dla każdego pomiaru jest skumulowany i wykresów mają własne osi y. Skumulowane wykresy są przydatne, gdy masz wiele miar zaznaczone i mają widoków pomiarów.
* **Nieskumulowanym:** Wykres dla Każda miara jest wykreślony względem jedną oś y, ale wartości dla osi y są zmieniane w oparciu o wyróżnione miary. Wykresy nieskumulowanych są przydatne do nakładki wiele miar i chcesz zobaczyć wzorców w tych środków dla tego samego zakresu czasu.
* **Oś y udostępnione:** Wszystkich wykresów udostępnianie tej samej osi y i nie zmieniaj wartości dla osi. Udostępnione wykresy osi y są przydatne, gdy chcesz Przyjrzyj się pojedynczej mierze podczas tworzenia wycinków danych przy użyciu podziału przez.

 ![Porządkowanie danych na osi y w trybach inny typ wizualizacji](media/howto-create-analytics/analytics-yaxis.png)

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak utworzyć niestandardowe analizy dotyczące aplikacji usługi Azure IoT Central tutaj sugerowane następnym krokiem jest:

> [!div class="nextstepaction"]
> [Przygotuj i łączenie aplikacji Node.js](howto-connect-nodejs.md)