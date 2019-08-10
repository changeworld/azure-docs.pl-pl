---
title: Dodawanie analizy do interfejs API wyszukiwania w sieci Web Bing
titleSuffix: Azure Cognitive Services
description: Statystyki Bing umożliwiają analizowanie interfejs API wyszukiwania obrazów Bing. Analiza obejmuje wolumin wywołań, najpopularniejsze ciągi zapytań, dystrybucję geograficzną i wiele innych.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 5d1b0b19523eb37aa83aa59b24114be9f76ffa55
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882772"
---
# <a name="add-analytics-to-the-bing-search-apis"></a>Dodawanie analizy do interfejsy API wyszukiwania Bing

Statystyka Bing udostępnia analizę dla interfejsy API wyszukiwania Bing. Te analizy obejmują wolumin wywołań, najpopularniejsze ciągi zapytań, dystrybucję geograficzną i wiele innych. Statystykę Bing można włączyć w [Azure Portal](https://ms.portal.azure.com) , przechodząc do zasobu platformy Azure, a następnie klikając pozycję **Włącz statystyki Bing**.

> [!IMPORTANT]
> * Statystyka usługi Bing nie jest dostępna w przypadku subskrypcji bezpłatnych wersji próbnych `F0` ani zasobów w warstwie cenowej bezpłatna.
> * Nie można używać żadnych danych za pośrednictwem pulpitu nawigacyjnego statystyk Bing do tworzenia aplikacji do dystrybucji do stron trzecich.
> * Włączenie statystyk Bing znacznie wydłuża swoją liczbę subskrypcji. Aby uzyskać szczegółowe informacje, zobacz [Cennik](https://aka.ms/bingstatisticspricing) .


Na poniższej ilustracji przedstawiono dostępne analizy poszczególnych punktów końcowych interfejsu API Wyszukiwanie Bing.

![Dystrybucja według macierzy obsługi punktu końcowego](./media/bing-statistics/bing-statistics-matrix.png)

## <a name="access-your-analytics"></a>Dostęp do analizy

Bing aktualizuje dane analityczne co 24 godziny i utrzymuje okres do 13 miesięcy, do których można uzyskać dostęp za pośrednictwem [pulpitu nawigacyjnego analizy](https://bingapistatistics.com). Upewnij się, że logujesz się przy użyciu tego samego konto Microsoft (MSA) użytego do zarejestrowania się w celu uzyskania statystyk usługi Bing.

> [!NOTE]  
> * Metryki na pulpicie nawigacyjnym mogą potrwać do 24 godzin. Na pulpicie nawigacyjnym jest wyświetlana data i godzina ostatniej aktualizacji danych.  
> * Metryki są dostępne od momentu włączenia dodatku Statystyka Bing.

## <a name="filter-the-data"></a>Filtrowanie danych

Domyślnie wykresy i wykresy zawierają wszystkie metryki i dane, do których masz dostęp. Dane wyświetlane na wykresach i wykresach można filtrować, wybierając odpowiednie zasoby, rynki, punkty końcowe i okres raportowania. Można zmienić następujące filtry:

- **Identyfikator zasobu**: Unikatowy identyfikator zasobu, który identyfikuje subskrypcję platformy Azure. Lista zawiera wiele identyfikatorów w przypadku subskrybowania więcej niż jednej Wyszukiwanie Bing warstwy interfejsu API. Domyślnie zaznaczone są wszystkie zasoby.  
  
- **Rynki**: Rynki, z których pochodzą wyniki. Na przykład en-us (angielski, Stany Zjednoczone). Domyślnie wszystkie rynki są zaznaczone. `en-WW` Rynek jest rynkiem, którego używa Usługa Bing, jeśli wywołanie nie określa rynku, a usługa Bing nie może ustalić rynku użytkownika.  
  
- **Punkty końcowe**: Punkty końcowe interfejsu API Wyszukiwanie Bing. Lista zawiera wszystkie punkty końcowe, dla których masz płatną subskrypcję. Domyślnie są zaznaczone wszystkie punkty końcowe.  

- **Przedział czasu**: Okres sprawozdawczy. Można określić:
  - **Wszystkie**: Obejmuje do 13 miesięcy danych  
  - **Ostatnie 24 godziny**: Obejmuje analizę z ostatnich 24 godzin  
  - **Ostatni tydzień**: Obejmuje analizę z ostatnich siedmiu dni  
  - **Ostatni miesiąc**: Obejmuje analizę z ostatnich 30 dni  
  - **Niestandardowy zakres dat**: Zawiera analizę z określonego zakresu dat, jeśli jest dostępna  

## <a name="charts-and-graphs"></a>Wykresy i grafy

Na pulpicie nawigacyjnym są wyświetlane wykresy i wykresy metryk dostępne dla wybranego punktu końcowego. Nie wszystkie metryki są dostępne dla wszystkich punktów końcowych. Wykresy i wykresy dla każdego punktu końcowego są statyczne (nie można wybierać wykresów i wykresów do wyświetlenia). Na pulpicie nawigacyjnym są wyświetlane tylko wykresy i wykresy, dla których znajdują się dane.

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

Dostępne są następujące metryki i ograniczenia punktów końcowych.

- **Głośność wywołania**: Pokazuje liczbę wywołań podejmowanych w okresie raportowania. Jeśli okres raportowania przypada na dzień, wykres pokazuje liczbę wywołań wykonanych na godzinę. W przeciwnym razie wykres pokazuje liczbę wywołań wykonanych na dzień okresu raportowania.  
  
  > [!NOTE]
  > Wolumin wywołania może różnić się od raportów rozliczeń, które zwykle zawierają tylko pomyślne wywołania.

- **Najpopularniejsze zapytania**: Pokazuje najpopularniejsze zapytania oraz liczbę wystąpień każdego zapytania w okresie raportowania. Można skonfigurować liczbę wyświetlanych zapytań. Można na przykład wyświetlić 25 najważniejszych zapytań, 50 lub 75. Najważniejsze zapytania nie są dostępne dla następujących punktów końcowych:  

  - /images/trending
  - /images/details
  - /images/visualsearch
  - /videos/trending
  - /videos/details
  - /news
  - /news/trendingtopics
  - /suggestions  
  
  > [!NOTE]  
  > Niektóre terminy zapytania mogą być pomijane w celu usunięcia poufnych informacji, takich jak wiadomości e-mail, numery telefonów, SSN itd.

- **Rozkład geograficzny**: Na rynkach, na których pochodzą wyniki wyszukiwania. Na przykład `en-us` (angielski, Stany Zjednoczone). W przypadku wybrania rynku Bing używa `mkt` parametru zapytania. W przeciwnym razie Bing używa sygnałów, takich jak adres IP wywołującego, aby określić rynek.

- **Dystrybucja kodu odpowiedzi**: Kody stanu HTTP wszystkich wywołań w okresie raportowania.

- **Rozkład pochodzenia wywołania**: Typy przeglądarek używanych przez użytkowników. Na przykład Microsoft Edge, Chrome, Safari i FireFox. Wywołania wykonane z zewnątrz przeglądarki (takie jak botów, Poster lub using z poziomu aplikacji konsolowej) są pogrupowane w obszarze biblioteki. Początek jest określany przy użyciu wartości nagłówka User-Agent żądania. Jeśli żądanie nie zawiera nagłówka User-Agent, Bing próbuje utworzyć źródło z innych sygnałów.  

- **Dystrybucja bezpiecznego wyszukiwania**: Dystrybucja wartości bezpiecznego wyszukiwania. Na przykład wyłączanie, umiarkowane lub rygorystyczne. Parametr `safeSearch` zapytania zawiera wartość, jeśli została określona. W przeciwnym razie, Bing domyślnie wartość średnia.  

- **Odpowiedź żądana dystrybucja**: Wyszukiwanie w sieci Web odpowiedzi interfejsu API, które zażądały `responseFilter` w parametrze zapytania.  

- **Odpowiedzi zwrócono do dystrybucji**: Odpowiedzi zwrócone przez interfejs API wyszukiwanie w sieci Web w odpowiedzi.

- **Dystrybucja serwera odpowiedzi**: Serwer aplikacji, który obsłużył żądania interfejsu API. Możliwe wartości to Bing.com (dla ruchu obsługiwanego z urządzeń stacjonarnych i laptopów) oraz Bing.com-Mobile (dla ruchu obsługiwanego z urządzeń przenośnych). Serwer jest określany przy użyciu wartości nagłówka User-Agent żądania. Jeśli żądanie nie zawiera nagłówka User-Agent, Bing próbuje uzyskać dostęp do serwera z innych sygnałów.

## <a name="next-steps"></a>Następne kroki

* [Co to jest interfejsy API wyszukiwania Bing?](bing-api-comparison.md)
* [Użycie interfejsu API Wyszukiwanie Bing i wymagania dotyczące wyświetlania](use-display-requirements.md)
