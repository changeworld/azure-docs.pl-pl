---
title: Eksplorowanie danych za pomocą Eksploratora Azure czas serii Insights | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak szybko wyświetlić widok globalne dane big i sprawdzić poprawności środowiska IoT za pomocą Eksploratora Azure czas serii wgląd w przeglądarce sieci web.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: kfile
manager: jhubbard
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/30/2017
ms.openlocfilehash: d3b8973e8798e5b1f32d9e882db8a06ad7486ed8
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295342"
---
# <a name="azure-time-series-insights-explorer"></a>Azure Eksplorator Insights serii czasu
Ten artykuł opisuje różne funkcje i opcje dostępne w aplikacji sieci web explorer Insights serii czasu. Umożliwia explorer czasu serii wgląd w przeglądarce sieci web tworzenie wizualizacji danych.
 
Azure Time Series Insights to w pełni zarządzana usługa związana z analizą, przechowywaniem i wizualizacją, dzięki której z łatwością można badać i analizować miliardy zdarzeń IoT jednocześnie. Udostępnia globalny widok danych, które umożliwia szybkie sprawdzenie rozwiązania IoT i uniknąć kosztownych przestojów urządzeniom krytycznym. Można odnaleźć ukryte trendy, dodatkowych anomalii i przeprowadzania analizy głównej przyczyny problemu w najbliższym czasie rzeczywistym. Eksplorator czasu serii Insights jest aktualnie w publicznej wersji zapoznawczej.

## <a name="prerequisites"></a>Wymagania wstępne

Przed użyciem Eksplorator Insights serii czasu, należy:
- Utwórz środowisko Insights serii czasu
- Zapewniają dostęp do Twojego konta w środowisku
- Dodawanie źródła zdarzeń do pozyskiwania danych i zapisze go

## <a name="explore-and-query-data"></a>Eksploruj i zapytania na danych
W ciągu minut połączenia źródła zdarzeń w danym środowisku czasu serii Insights można eksplorować i danych serii czas zapytania.

1. Aby rozpocząć, otwórz [Eksplorator Insights serii czasu](https://insights.timeseries.azure.com/) w przeglądarce sieci web i wybierz środowisko po lewej stronie okna. Wszystkie środowiska, które mają dostęp do są wymienione w kolejności alfabetycznej.

2. Po wybraniu środowisku, albo użyć **FROM** i **do** konfiguracje u góry, lub kliknij i przeciągnij za pośrednictwem sieci zakres żądany czas.  Kliknij przycisk Lupa u góry po prawej, lub kliknij prawym przyciskiem myszy na wybrany zakres czasu i wybierz polecenie **wyszukiwania**.  

3. Można również odświeżyć dostępności automatycznie co minutę, wybierając **automatycznie na** przycisku.  Zauważ, że przycisk "Auto-na" dotyczy tylko na wykresie dostępności nie zawartość głównego wizualizacji.

4. Zwróć uwagę, ikona chmury Azure umożliwia przejście do środowiska w portalu Azure.

   ![Środowisko usługi Time Series Insights](media/time-series-insights-explorer/explorer1.png)

5. Następnie zostanie wyświetlony wykres pokazuje liczbę wszystkich zdarzeń podczas wybrany zakres czasu.  W tym miejscu masz wiele formantów:

    **Warunki Panel Edytor**: miejsce termin jest where zapytań środowiska.  Znajduje się po lewej stronie ekranu, umożliwia 
      - **Miara**: to listy rozwijanej pokazuje wszystkie kolumny liczbowe (symulacyjnych)
      - **Podziel przez**: Ta lista rozwijana zawiera kolumny podzielone na kategorie (ciągi)
      - Można włączyć interpolacji krok, Pokaż minimalne i maksymalne i dostosować osi y, z Panelu sterowania dalej, aby zmierzyć.  Ponadto można dostosować, czy dane wyświetlane jest liczba, średnia lub sumę danych.
      - Można dodać do pięciu postanowienia do wyświetlenia na tej samej osi x.  Użyj **kopiowania rozwijanej** przycisk, aby dodać dodatkowe termin lub kliknij przycisk **Dodaj** przycisk, aby dodać nowego warunku.
     
        ![Panel Edytor warunków](media/time-series-insights-explorer/explorer2.png)

      - **Predykat**: Predykat umożliwia szybkie filtrowanie zdarzeń przy użyciu zestawu operandy wymienionych poniżej. Jeśli wyszukiwania, wybierając klikając, predykatu zostanie automatycznie aktualizacji na podstawie tego wyszukiwania.      Operand obsługiwane typy:

         |Operacja  |Obsługiwane typy  |Uwagi  |
         |---------|---------|---------|
         |<, >, <=, >=     |  Double, DateTime, TimeSpan       |         |
         |=, !=, <>     | String, Bool, Double, DateTime, TimeSpan, wartość NULL        |         |
         |W     | String, Bool, Double, DateTime, TimeSpan, wartość NULL        |  Wszystkie argumenty powinny być tego samego typu lub być stała NULL.        |
         |MA     | Ciąg        |  Tylko Literały ciągu stałych mogą się po prawej stronie. Pusty ciąg i NULL są niedozwolone.       |

      - **Przykłady zapytań**
      
         ![Przykładowe zapytania](media/time-series-insights-explorer/explorer9.png)

6. **Rozmiar interwału** narzędzia suwaka umożliwia powiększanie i interwały za pośrednictwem tego samego timespan.  Zapewnia to dokładniejszą kontrolę ruchu między dużych okresy przedstawiających smooth trendów w dół wycinków jak milisekund, dzięki czemu można zobaczyć szczegółowe, wysokiej rozdzielczości części danych. Suwak domyślny punkt początkowy jest ustawiony jako optymalny wgląd w dane z zaznaczenia; Równoważenie rozpoznawania, szybkość wykonywania kwerend i stopniu szczegółowości.

7. **Czasu pędzla** narzędzia można łatwo przejść z jednego zakresu czasu do innego umieszczanie intuicyjne Centrum bezproblemowe ruchu między przedziałów czasu i przodu środowiska użytkownika.

8. **Zapisać** polecenie pozwala zapisać bieżącego zapytania i włącz ją dla udostępniania innym użytkownikom środowiska. Przy użyciu **Otwórz**, możesz wyświetlać wszystkie zapisane kwerendy i innych użytkowników w środowiskach, musisz mieć dostęp do wszelkich udostępnione zapytania. 

   ![Zapytania](media/time-series-insights-explorer/explorer3.png)

9. **Widoku perspektywy** narzędzie wyświetla jednoczesnych maksymalnie cztery unikatowe zapytania. Przycisk widoku perspektywy znajduje się w prawym górnym rogu wykresu.  

   ![Widok perspektywy](media/time-series-insights-explorer/explorer4.png)

10. **Wykresu** pozwala wizualnie eksplorować dane. Narzędzi wykresu należą:

   - Wybierz/kliknięcia, co umożliwia wybór określonego timespan lub pojedynczej serii danych.  
   - W czasie span zaznaczenia, można powiększyć lub Eksploruj zdarzenia.  
   - W serii danych można podział serii według innej kolumny, Dodaj jako nowy termin serii, pokazuj tylko wybrane serii, Wyklucz wybrane serii, zbadaj tej serii lub eksplorować zdarzenia w wybranej serii.
   - W obszarze filtru do lewego rogu wykresu można Zobacz wszystkie szeregi wyświetlanych danych i zmienić kolejność według wartości lub nazwy, wyświetlanie wszystkich serii danych lub w szczególności przypiętych lub odpiąć serii.  Możesz również wybierz jedną serię danych i podział serii według innej kolumny, Dodaj jako nowy termin serii, Pokaż tylko wybrane serii, Wyklucz wybrane serii, przypiąć tej serii lub eksplorować zdarzenia w wybranej serii.
   - Podczas wyświetlania jednocześnie wiele warunków, można stosu, anulowania, zobacz dodatkowe dane dotyczące serii danych i Użyj tej samej osi y na wszystkie warunki za pomocą przycisków w prawym górnym rogu wykresu.
 
   ![Narzędzi wykresu](media/time-series-insights-explorer/explorer5.png) 

11. **Heatmap** może służyć do szybkiego wychwycenia serii danych unikatowy lub nietypowe w określonego zapytania. Tylko jeden wyszukiwany termin można wywołać jako heatmap.    

   ![Mapa cieplna](media/time-series-insights-explorer/explorer6.png)

12. **Zdarzenia**: po wybraniu Eksploruj zdarzenia, wybierając lub prawym przyciskiem myszy nad, panelu zdarzeń ma zostać udostępnione.  W tym miejscu można wyświetlić wszystkie nieprzetworzone zdarzeń i eksportowanie zdarzeń jako JSON lub plików CSV. Należy pamiętać, że czas serii Insights przechowuje wszystkie dane pierwotne.

   ![Zdarzenia](media/time-series-insights-explorer/explorer7.png)

13. Kliknij przycisk **Statystyka** kartę po eksploracji zdarzenia do udostępnienia wzorców i statystyka kolumny.  

   - **Wzorce**: Ta funkcja udostępnia aktywnego najbardziej statystycznie istotne wzorce w regionie wybranych danych. To zwalnia z konieczności przyjrzeć się tysiące zdarzeń, aby zrozumieć, jakie wzorce najbardziej gwarantuje czasu i energii. Ponadto czas serii Insights umożliwia przejście bezpośrednio do tych wzorców statystycznie istotne, aby kontynuować, przeprowadzanie analizy. Ta funkcja jest również przydatne w przypadku których post dochodzenia w danych historycznych. 

   - **Statystyka kolumny**: Podaj Statystyka kolumn wykresów i tabel, które podziału danych z każdej kolumny serii wybranych danych w wybranym okresie.  
 
      ![STATYSTYKI](media/time-series-insights-explorer/explorer8.png) 

Teraz już wspomniano różnych funkcji i opcji dostępnych w aplikacji sieci web explorer Insights serii czasu. 

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
>[Diagnozowanie i rozwiązywanie problemów w środowisku Insights serii czasu](time-series-insights-diagnose-and-solve-problems.md)
