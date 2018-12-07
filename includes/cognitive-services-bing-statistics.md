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
ms.openlocfilehash: a8d588b186652ab86ee1e8152bd9be08f0f1ef04
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52978701"
---
Dodatek Statystyka Bing udostępnia analizy dla interfejsów API wyszukiwania Bing. Analytics zawiera wolumin wywołań, ciągi zapytania dotyczącego początkowych, rozmieszczenie geograficzne i inne. Aby włączyć dodatek Statystyka Bing wyszukiwania Bing płatną subskrypcję, przejdź do swojej [pulpitu nawigacyjnego platformy Azure](https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7), wybierz swoją subskrypcję płatną i kliknij przycisk Włącz dodatek Statystyka Bing. Włączanie dodatek Statystyka Bing nieco zwiększa szybkość subskrypcji (zobacz [ceny](https://aka.ms/bingstatisticspricing)).

> [!NOTE]
> Dodatek Statystyka Bing jest dostępny za pomocą tylko płatnych subskrypcji — nie jest dostępna z subskrypcji bezpłatnych wersji próbnych.

> [!NOTE]
> Nie można stosować wszystkie dane, które są dostępne za pośrednictwem pulpitu nawigacyjnego dodatek Statystyka Bing do tworzenia aplikacji w celu dystrybucji do podmiotów trzecich.

Bing aktualizuje dane analizy, co 24 godziny i utrzymuje się do 13 miesięcznej historii.

## <a name="accessing-your-analytics"></a>Uzyskiwanie dostępu do analizy

Aby uzyskać dostęp do pulpitu nawigacyjnego analizy, przejdź do https://bingapistatistics.com. Upewnij się, że zalogowano Cię przy użyciu tego samego konta Microsoft (MSA) użyte do uzyskania płatną subskrypcję.

## <a name="filtering-the-data"></a>Filtrowanie danych

Domyślnie wykresy i diagramy odzwierciedlać wszystkich danych metryk, które mają dostęp do. Można filtrować dane wyświetlane w wykresy i diagramy, wybierając zasoby, rynków, punktów końcowych, a okres raportowania interesuje Cię. Wykresy i diagramy zmieniają się zgodnie z filtrami, które stosujesz. Poniżej opisano filtry, które mogą ulec zmianie.

- **Identyfikator zasobu**: identyfikator unikatowy zasób, którego identyfikuje subskrypcji platformy Azure. Lista zawiera wiele identyfikatorów, jeśli użytkownik subskrybuje więcej niż jedną warstwę interfejsu API wyszukiwania Bing. Domyślnie wybrane są wszystkie zasoby.  
  
- **Rynki**: rynków, skąd pochodzą wyniki. Na przykład en-us (angielski, Stany Zjednoczone). Domyślnie są zaznaczone wszystkie rynki. Zwróć uwagę, że na rynku en-WW oczekuje, że usługa Bing używa, jeśli połączenie nie określa rynku i Bing nie może określić rynku użytkownika.  
  
- **Punkty końcowe**: punkty końcowe interfejsu API wyszukiwania Bing. Lista zawiera wszystkie punkty końcowe, do których masz subskrypcję płatną. Domyślnie wszystkie punkty końcowe są wybierane.  

- **Przedział czasu**: okresie raportowania. Można określić:
  - Wszystkie&mdash;ma zapasowych do 13 miesięcznej danych  
  - Ostatnie 24 godziny&mdash;obejmuje analiza danych z ostatnich 24 godzinach  
  - Ubiegły tydzień&mdash;obejmuje analiza danych z ostatnich siedmiu dni  
  - Ubiegły miesiąc&mdash;obejmuje analiza danych z ostatnich 30 dni  
  - Niestandardowy zakres dat&mdash;obejmuje funkcje analizy usługi w określonym zakresie dat., jeśli jest dostępny  

  > [!NOTE]  
  > Może upłynąć do 24 godzin metryki, aby przedstawić na pulpicie nawigacyjnym. Pulpit nawigacyjny przedstawia datę i godzinę ostatniej aktualizacji danych.  

  > [!NOTE]  
  > Metryki są dostępne od czasu, należy włączyć dodatek Statystyka Bing.

## <a name="charts-and-graphs"></a>Wykresy i diagramy

Na pulpicie nawigacyjnym prezentowana wykresy i diagramy dotyczące metryk, które są dostępne dla wybranego punktu końcowego. Nie wszystkie metryki są dostępne dla wszystkich punktów końcowych. Wykresy i diagramy, dla każdego punktu końcowego są statyczne (nie można wybrać wykresów i schematów, aby wyświetlić). Na pulpicie nawigacyjnym prezentowana tylko wykresy i diagramy, w których ma danych.

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

Poniżej przedstawiono możliwe metryki. Wszystkie metryki. informacje o ograniczenia punktu końcowego.

- **Wywołanie woluminu**: Pokazuje liczbę wywołań w tym okresie raportowania. Jeśli w okresie raportowania jest na dzień, wykres pokazuje liczbę wywołań na godzinę. W przeciwnym razie wykres pokazuje liczbę wywołań dziennie okresie raportowania.  
  
  > [!NOTE]
  > Wolumin wywołań może różnić się od rozliczeń raportów, które zwykle zawiera tylko pomyślnych wywołań.

- **Najpopularniejsze kwerendy**: zawiera najpopularniejsze zapytania i liczbę wystąpień każdej kwerendy w okresie raportowania. Można skonfigurować liczbę zapytań wyświetlane. Na przykład można wyświetlić zapytania top 25 i 50, 75. Najpopularniejsze zapytania nie jest dostępna dla następujących punktów końcowych:  

  - /images/trending
  - / obrazy/szczegóły
  - / obrazy/visualsearch
  - /videos/trending
  - / filmy/szczegóły
  - /News
  - / wiadomości/trendingtopics
  - /Suggestions  
  
  > [!NOTE]  
  > Można pominąć kilka terminów zapytania, aby usunąć informacje poufne, takie jak wiadomości e-mail, numerów telefonów, numer PESEL, itp.

- **Rozmieszczenie geograficzne**: rynków, skąd pochodzą wyniki. Na przykład en-us (angielski, Stany Zjednoczone). Usługa Bing używa `mkt` zapytania parametru do określenia na rynku, jeśli określony. W przeciwnym razie Bing używa sygnały, takie jak adres IP obiektu wywołującego w celu określenia rynku.

- **Dystrybucja kodów odpowiedzi**: kody stanu HTTP dla wszystkich wywołań w tym okresie raportowania.

- **Wywołaj dystrybucji pochodzenia**: typy przeglądarek, które posługują się użytkownicy. Na przykład Edge, Chrome, Safari i FireFox. Wywołania z poza przeglądarki, takich jak Boty, Postman lub przy użyciu programu curl z poziomu aplikacji konsolowej, są zgrupowane w bibliotekach. Punkt początkowy jest określany przy użyciu wartości nagłówka agenta użytkownika żądania. Jeśli żądanie nie zawiera nagłówka agenta użytkownika, Bing próbuje wyprowadzenia źródła z innymi sygnałami.  

- **Bezpieczne wyszukiwanie dystrybucji**: rozkładu wartości bezpiecznego wyszukiwania. Na przykład wyłączyć, umiarkowany, lub strict. `safeSearch` Zapytania parametru zawiera wartość, jeśli określony. W przeciwnym razie Bing wartością domyślną jest wartość średnia.  

- **Odpowiedzi na żądanie dystrybucji**: interfejs API wyszukiwania w sieci Web odpowiedzi, możesz w `responseFilter` parametr zapytania.  

- **Odpowiedzi zwrócony dystrybucji**: odpowiedzi, które API wyszukiwania w Internecie zwrócona w odpowiedzi.

- **Odpowiedź serwera dystrybucji**: serwerze aplikacji, który obsłużył żądań interfejsu API. Możliwe wartości to Bing.com (na ruch z urządzeń stacjonarnych i przenośnych) i Bing.com dla urządzeń przenośnych (na ruch z urządzeń przenośnych). Serwer jest określany przy użyciu wartości nagłówka agenta użytkownika żądania. Jeśli żądanie nie zawiera nagłówka agenta użytkownika, Bing próbuje pochodzi z serwera z innymi sygnałami.

Na poniższym obrazie przedstawiono analizy, które są dostępne dla każdego punktu końcowego.

![Rozkład według macierz obsługi punktu końcowego](./media/cognitive-services-bing-statistics/bing-statistics-matrix.PNG)
