---
title: Dodawanie analizy do interfejsu API wyszukiwania w sieci Web usługi Bing
titleSuffix: Azure Cognitive Services
description: Bing Statistics zapewnia analizę interfejsu API wyszukiwania obrazów Bing. Analytics obejmują wolumin wywołań, górne ciągi zapytań, dystrybucję geograficzną i inne.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68882772"
---
# <a name="add-analytics-to-the-bing-search-apis"></a>Dodawanie analiz do interfejsów API wyszukiwania Bing

Statystyki Bing udostępnia analizy dla interfejsów API wyszukiwania Bing. Te analizy obejmują wolumin wywołań, górne ciągi zapytań, dystrybucję geograficzną i inne. Statystyki Bing można włączyć w [witrynie Azure portal,](https://ms.portal.azure.com) przechodząc do zasobu platformy Azure i klikając **pozycję Włącz statystyki Bing**.

> [!IMPORTANT]
> * Statystyki Bing nie jest dostępna z bezpłatną subskrypcją `F0` próbną lub zasobami w bezpłatnej warstwie cenowej.
> * Nie można używać żadnych danych dostępnych za pośrednictwem pulpitu nawigacyjnego statystyk Bing do tworzenia aplikacji do dystrybucji do stron trzecich.
> * Włączenie statystyki Bing nieznacznie zwiększa stawkę subskrypcji. Zobacz [ceny, aby](https://aka.ms/bingstatisticspricing) uzyskać szczegółowe informacje.


Na poniższej ilustracji przedstawiono dostępne analizy dla każdego punktu końcowego interfejsu API wyszukiwania Bing.

![Macierz obsługi rozkładu według punktu końcowego](./media/bing-statistics/bing-statistics-matrix.png)

## <a name="access-your-analytics"></a>Dostęp do analiz

Bing aktualizuje dane analityczne co 24 godziny i przechowuje do 13 miesięcy historii, do której można uzyskać dostęp z [pulpitu nawigacyjnego analizy.](https://bingapistatistics.com) Upewnij się, że zalogowano się przy użyciu tego samego konta Microsoft (MSA), którego użyto do zarejestrowania się w usłudze Statystyki Bing.

> [!NOTE]  
> * Może upłynąć do 24 godzin dla metryki do powierzchni na pulpicie nawigacyjnym. Pulpit nawigacyjny pokazuje datę i godzinę ostatniej aktualizacji danych.  
> * Metryki są dostępne od momentu włączenia dodatku Statystyki Bing.

## <a name="filter-the-data"></a>Filtrowanie danych

Domyślnie na wykresach i wykresach są wyświetlane wszystkie metryki i dane, do których masz dostęp. Dane wyświetlane na wykresach i wykresach można filtrować, wybierając zasoby, rynki, punkty końcowe i okres raportowania, który Cię interesuje. Można zmienić następujące filtry:

- **Identyfikator zasobu:** unikatowy identyfikator zasobu, który identyfikuje subskrypcję platformy Azure. Lista zawiera wiele identyfikatorów, jeśli subskrybujesz więcej niż jedną warstwę interfejsu API wyszukiwania Bing. Domyślnie są zaznaczone wszystkie zasoby.  
  
- **Rynki**: Rynki, z których pochodzą wyniki. Na przykład en-us (angielski, Stany Zjednoczone). Domyślnie wybrano wszystkie rynki. Rynek `en-WW` jest rynkiem, z którego korzysta Bing, jeśli wezwanie nie określa rynku, a Bing nie jest w stanie określić rynku użytkownika.  
  
- **Punkty końcowe:** punkty końcowe interfejsu API wyszukiwania Bing. Lista zawiera wszystkie punkty końcowe, dla których masz płatną subskrypcję. Domyślnie zaznaczone są wszystkie punkty końcowe.  

- **Ramy czasowe:** okres sprawozdawczy. Można określić:
  - **Wszystkie**: Zawiera do 13 miesięcy wartości danych  
  - **Ostatnie 24 godziny**: obejmuje analizy z ostatnich 24 godzin  
  - **Miniony tydzień**: obejmuje analizy z poprzednich siedmiu dni  
  - **Miniony miesiąc**: obejmuje analizy z poprzednich 30 dni  
  - **Niestandardowy zakres dat:** zawiera analizy z określonego zakresu dat, jeśli są dostępne  

## <a name="charts-and-graphs"></a>Wykresy i wykresy

Pulpit nawigacyjny pokazuje wykresy i wykresy metryk dostępnych dla wybranego punktu końcowego. Nie wszystkie metryki są dostępne dla wszystkich punktów końcowych. Wykresy i wykresy dla każdego punktu końcowego są statyczne (nie można wybrać wykresów i wykresów do wyświetlenia). Pulpit nawigacyjny pokazuje tylko wykresy i wykresy, dla których są dane.

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

Poniżej przedstawiono możliwe metryki i ograniczenia punktu końcowego.

- **Liczba połączeń:** pokazuje liczbę połączeń wykonanych w okresie raportowania. Jeśli okres raportowania dotyczy dnia, na wykresie jest wyświetlana liczba połączeń na godzinę. W przeciwnym razie wykres pokazuje liczbę połączeń wykonanych dziennie w okresie raportowania.  
  
  > [!NOTE]
  > Wielkość połączeń może różnić się od raportów rozliczeniowych, które zazwyczaj obejmują tylko pomyślne połączenia.

- **Najlepsze kwerendy:** Pokazuje najlepsze zapytania i liczbę wystąpień każdej kwerendy w okresie raportowania. Można skonfigurować liczbę wyświetlanych zapytań. Na przykład można wyświetlić top 25, 50 lub 75 zapytań. Najważniejsze zapytania nie są dostępne dla następujących punktów końcowych:  

  - /images/trendy
  - /images/details
  - /images/visualsearch
  - /filmy/trendy
  - /filmy/szczegóły
  - /aktualności
  - /news/trendytopy
  - /sugestie  
  
  > [!NOTE]  
  > Niektóre terminy kwerend mogą być pomijane w celu usunięcia poufnych informacji, takich jak wiadomości e-mail, numery telefonów, SSN itp.

- **Rozmieszczenie geograficzne:** rynki, na których pochodzą wyniki wyszukiwania. Na przykład `en-us` (angielski, Stany Zjednoczone). Bing używa `mkt` parametru kwerendy do określenia rynku, jeśli jest określony. W przeciwnym razie Bing używa sygnałów, takich jak adres IP wywołującego do określenia rynku.

- **Dystrybucja kodu odpowiedzi**: Kody stanu HTTP wszystkich wywołań w okresie raportowania.

- **Zadzwoń Do Dystrybucji Origin:** Typy przeglądarek używanych przez użytkowników. Na przykład Microsoft Edge, Chrome, Safari i FireFox. Wywołania nawiązywane z zewnątrz przeglądarki (takie jak boty, listonosz lub za pomocą curl z aplikacji konsoli) są grupowane w obszarze Biblioteki. Początek jest określany przy użyciu wartości nagłówka user-agent żądania. Jeśli żądanie nie zawiera nagłówka agenta użytkownika, Bing próbuje wyprowadzić pochodzenie z innych sygnałów.  

- **Dystrybucja bezpiecznego wyszukiwania:** dystrybucja bezpiecznych wartości wyszukiwania. Na przykład wyłączone, umiarkowane lub ścisłe. Parametr `safeSearch` kwerendy zawiera wartość, jeśli jest określona. W przeciwnym razie Bing domyślnie wartość jest umiarkowana.  

- **Odpowiedzi Żądana dystrybucja:** Interfejs API wyszukiwania w sieci `responseFilter` Web odpowiada na żądane w parametrze zapytania.  

- **Odpowiedzi zwrócone dystrybucja:** Odpowiedzi, które interfejs API wyszukiwania w sieci Web zwrócił w odpowiedzi.

- **Dystrybucja serwera odpowiedzi:** serwer aplikacji, który obsługiwał żądania interfejsu API. Możliwe wartości to Bing.com (dla ruchu obsługiwanego z komputerów stacjonarnych i laptopów) oraz Bing.com-mobile (dla ruchu obsługiwanego z urządzeń mobilnych). Serwer jest określany przy użyciu wartości nagłówka user-agent żądania. Jeśli żądanie nie zawiera nagłówka agenta użytkownika, Bing próbuje wyprowadzić serwer z innych sygnałów.

## <a name="next-steps"></a>Następne kroki

* [Co to są interfejsy API wyszukiwania Bing?](bing-api-comparison.md)
* [Wymagania dotyczące użycia i wyświetlania interfejsu API wyszukiwania Bing](use-display-requirements.md)
