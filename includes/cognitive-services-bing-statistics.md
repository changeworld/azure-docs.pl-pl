---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: scottwhi-msft
ms.service: cognitive-services
ms.topic: include
ms.date: 04/09/2018
ms.author: scottwhi
ms.custom: include file
ms.openlocfilehash: 4e19c1afefdc5bcacebcb0d495193b48c7a6d724
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313714"
---
Statystyki Bing zapewnia analytics interfejsy API wyszukiwania usługi Bing. Analytics obejmuje wezwań, ciągi zapytania top, rozmieszczenie geograficzne i. Aby włączyć statystyki Bing podczas wyszukiwania usługi Bing płatną subskrypcję, przejdź do Twojej [pulpitu nawigacyjnego Azure](https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7), wybierz płatnej subskrypcji i kliknij pozycję Włącz statystyki Bing. Włączenie usługi Bing statystyki nieco zwiększa szybkość subskrypcji (zobacz [cennik](https://aka.ms/bingstatisticspricing)).


> [!NOTE]
> Statystyka usługi Bing jest dostępny z subskrypcją płatną tylko — nie jest dostępna z subskrypcji bezpłatnych wersji próbnych. 

> [!NOTE]
> Wszystkie dane, które są dostępne za pośrednictwem pulpitu nawigacyjnego statystyki Bing może nie używać do tworzenia aplikacji przeznaczonych do dystrybucji, do udzielania podmiotom trzecim.

Bing aktualizuje dane analityczne co 24 godziny i zachowuje się do 13 miesięcznej historii.

## <a name="accessing-your-analytics"></a>Uzyskiwanie dostępu do sieci analityka

Aby uzyskać dostęp do pulpitu nawigacyjnego analytics, przejdź do https://bingapistatistics.com. Upewnij się, że użytkownik jest zalogowany przy użyciu tego samego konta Microsoft (MSA) używany do pobierania płatnej subskrypcji.


## <a name="filtering-the-data"></a>Filtrowanie danych

Domyślnie wykresów i wykresy odzwierciedlają wszystkie dane metryk, które mają dostęp do. Można filtrować dane wyświetlane na wykresach wybierając zasoby, rynkach, punktów końcowych i okresu raportowania Cię interesują. Wykresy i wykresy zmieniać odpowiednio filtry, które należy zastosować. Poniżej opisano filtry, które mogą ulec zmianie.

- **Identyfikator zasobu**: identyfikator unikatowy zasób, którego identyfikuje subskrypcji platformy Azure. Lista zawiera wiele identyfikatorów w przypadku subskrybowania więcej niż jedną warstwę interfejsu API wyszukiwania usługi Bing. Domyślnie wybrane są wszystkie zasoby.  
  
- **Rynkach**: rynkach, skąd wyniki. Na przykład en-us (angielski, Stany Zjednoczone). Domyślnie wybrane są wszystkie rynkach. Zwróć uwagę, że na rynek TT en rynku Bing używa ich, jeśli połączenie nie określa rynku i Bing nie mógł określić rynku użytkownika.  
  
- **Punkty końcowe**: punkty końcowe interfejsu API wyszukiwania usługi Bing. Lista zawiera wszystkie punkty końcowe, do których masz subskrypcję płatną. Domyślnie są zaznaczone wszystkie punkty końcowe.  

- **Przedział czasu**: okresie raportowania. Można określić:  
  
  - Wszystkie&mdash;zawiera się do 13 miesięcznej danych  
  - Po 24 godzinach&mdash;obejmuje analytics z ostatnich 24 godzinach  
  - Ostatni tydzień&mdash;obejmuje analytics z ostatnich siedmiu dni  
  - Ostatni miesiąc&mdash;obejmuje analytics z ostatnich 30 dni  
  - Niestandardowy zakres dat&mdash;obejmuje analytics z zakresu określonej daty, jeśli jest dostępna  
  
  > [!NOTE]  
  > Może potrwać do 24 godzin metryki na powierzchni na pulpicie nawigacyjnym. Pulpit nawigacyjny zawiera Data i godzina ostatniej aktualizacji danych.  
  
  > [!NOTE]  
  > Metryki są dostępne w czasie włączyć dodatek statystyki Bing. 


## <a name="charts-and-graphs"></a>Wykresy i schematy

Pulpit nawigacyjny zawiera wykresy i schematy metryki dostępna dla wybranego punktu końcowego. Nie wszystkie metryki są dostępne dla wszystkich punktów końcowych. Schematy i wykresy dla każdego punktu końcowego są statyczne (nie można wybrać schematy i wykresy, aby wyświetlić). Pulpit nawigacyjny zawiera tylko schematy i wykresy, dla których jest danych. 

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

Poniżej przedstawiono możliwe metryki. Wszystkie metryki uwagi dotyczące ograniczenia punktu końcowego. 

- **Wywołaj woluminu**: Pokazuje liczbę wywołań w okresie raportowania. Po tym okresie raportowania na dzień wykres pokazuje liczbę wywołań na godzinę. W przeciwnym razie wykres pokazuje liczbę wywołań dziennie w tym okresie raportowania.  
  
  > [!NOTE]
  > Wolumin wywołania mogą się różnić od rozliczeń raportów, które zwykle obejmuje tylko pomyślnych wywołań. 
  
-  **Najpopularniejsze kwerendy**: Pokazuje najważniejszych zapytań oraz liczbę wystąpień każdej zapytania w tym okresie raportowania. Umożliwia skonfigurowanie liczby zapytań wyświetlane. Na przykład można wyświetlić zapytania top 25, 50 lub 75. Najważniejszych zapytań nie jest dostępna dla następujących punktów końcowych:  
  
  - /images/trending
  - / obrazów/szczegółów
  - / obrazów/visualsearch
  - /videos/trending
  - / wideo/szczegółów
  - /News
  - / wiadomości/trendingtopics
  - /Suggestions  
  
  > [!NOTE]  
  > Niektórych terminów zapytania może zostać pominięta, aby usunąć informacje poufne, takie jak wiadomości e-mail, numerów telefonów, SSN, itp.  

- **Rozmieszczenie geograficzne**: rynkach, skąd wyniki. Na przykład en-us (angielski, Stany Zjednoczone). Korzysta z usługi Bing `mkt` parametr ustalenie na rynku, zapytania, jeśli określony. W przeciwnym razie Bing używa sygnałów, takie jak adres IP obiektu wywołującego w celu określenia rynku.  
  
- **Dystrybucji kod odpowiedzi**: kodów stanu HTTP wszystkich wywołań w okresie raportowania.  
  
- **Wywołaj dystrybucji pochodzenia**: typy przeglądarek używanych przez użytkowników. Na przykład krawędzi, Chrome Safari i FireFox. Wywołania z poza przeglądarką, na przykład robotów, Postman lub przy użyciu programu curl z aplikacji konsoli, są pogrupowane w obszarze biblioteki. Punkt początkowy jest określany przy użyciu wartość nagłówka agenta użytkownika żądania. Jeśli żądanie nie zawiera nagłówka agenta użytkownika, Bing próbuje pochodzi z innymi sygnałami źródła.  
  
- **Bezpieczne dystrybucji wyszukiwania**: rozkład wartości bezpieczne wyszukiwania. Na przykład off, Średni, lub strict. `safeSearch` Zapytania parametru zawiera wartość, jeśli określony. W przeciwnym razie Bing domyślnie przyjmowana jest wartość średnia.  
  
- **Odpowiedzi na żądanie dystrybucji**: API wyszukiwania usługi sieci Web odpowiedzi, należy w `responseFilter` parametr zapytania.  
  
- **Odpowiedzi zwrócił dystrybucji**: odpowiedzi, które interfejs API sieci Web wyszukiwania zwracane w odpowiedzi.  
  
- **Odpowiedź serwera dystrybucji**: serwer aplikacji, który obsłużył żądań interfejsu API. Możliwe wartości to Bing.com (dla ruchu z stacjonarnych i przenośnych urządzeń) i Bing.com dla urządzeń przenośnych (na ruch pochodzący z urządzeń przenośnych). Serwer jest określany przy użyciu wartość nagłówka agenta użytkownika żądania. Jeśli żądanie nie zawiera nagłówka agenta użytkownika, Bing próbuje pochodzi z innymi sygnałami serwera.  
  


Poniżej przedstawiono analizy, które są dostępne dla każdego punktu końcowego.

![Rozkład według macierz obsługi punktu końcowego](./media/cognitive-services-bing-statistics/bing-statistics-matrix.PNG)


