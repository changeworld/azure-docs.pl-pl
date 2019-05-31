---
title: Konfigurowanie hostowanej interfejsu użytkownika dla usługi Bing Custom Search | Dokumentacja firmy Microsoft
titlesuffix: Azure Cognitive Services
description: Użyj w tym artykule, aby skonfigurować i integrowanie hostowanej interfejsu użytkownika w celu wyszukiwania niestandardowego Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: 17c0d19ad0c4a846d5f75905fe9072f2fe0e0f78
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66386771"
---
# <a name="configure-your-hosted-ui-experience"></a>Konfigurowanie środowiska obsługiwanego interfejsu użytkownika

Usługa Bing Custom Search udostępnia hostowanej interfejsu użytkownika, którą można łatwo zintegrować ze swoimi aplikacjami sieci web i stron sieci Web jako fragment kodu języka JavaScript. Za pomocą portalu wyszukiwania niestandardowego Bing, można skonfigurować układu, kolor i opcje wyszukiwania interfejsu użytkownika.



## <a name="configure-the-custom-hosted-ui"></a>Konfigurowanie niestandardowego hostowanej interfejsu użytkownika

Aby skonfigurować hostowanej interfejsu użytkownika dla aplikacji sieci web, wykonaj następujące kroki. W miarę wprowadzania zmian w okienku po prawej stronie zapewni (wersja zapoznawcza) interfejsu użytkownika. Wyniki wyszukiwania w wyświetlonej nie są rzeczywiste wyniki dla wystąpienia.

1. Zaloguj się do usługi Bing Custom Search [portal](https://customsearch.ai).  
  
2. Wybierz wystąpienie usługi Bing Custom Search.

3. Kliknij kartę **Hosted UI** (Hostowany interfejs użytkownika).  
  
4. Wybierz układ.

    |  |  |
    |---------|---------|
    |Pasek wyszukiwania i wyniki (ustawienie domyślne)    | Wyświetla pole wyszukiwania z wynikami wyszukiwania poniżej.         |
    |Tylko wyniki     | Wyświetla wyniki wyszukiwania, bez pola wyszukiwania. Korzystając z tego układu, należy podać zapytanie wyszukiwania (`&q=<query string>`). Dodaj parametr zapytania do adresu URL żądania w fragment kodu JavaScript lub link do endpoint HTML.        |
    |Tryb failover POP     | Zawiera pole wyszukiwania, a następnie wyświetli wyniki wyszukiwania w nakładce przewijania.        |
    
5. Wybierz motyw kolorów. Można dostosować kolory, aby dopasować aplikację, klikając **motyw Dostosuj**. Aby zmienić kolor, wprowadź SZESNASTKOWY RGB koloru (na przykład `#366eb8`), lub kliknij polecenie Podgląd koloru.

   Można wyświetlać podgląd zmian po prawej stronie portalu. Klikając **Przywróć domyślne** zostaną przywrócone zmiany kolory domyślne dla wybranego motywu.

   > [!NOTE]
   > Podczas wybierania kolorów, należy wziąć pod uwagę ułatwienia dostępu.

6. W obszarze **dodatkowe konfiguracje**, podaj wartości odpowiednie dla twojej aplikacji. Te ustawienia są opcjonalne. Aby zobaczyć efekt zastosowania lub usuwania ich, zobacz okienko podglądu po prawej stronie. Są dostępne opcje konfiguracji:  

7. Wprowadź klucz subskrypcji wyszukiwania lub wybierz ją z listy rozwijanej. Na liście rozwijanej jest wypełniana przy użyciu kluczy z subskrypcji konta platformy Azure. Zobacz [konta interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).  

8. Jeśli włączono automatyczne sugerowanie, wprowadź klucz subskrypcji automatycznego sugerowania, lub wybierz jednego z listy rozwijanej. Na liście rozwijanej jest wypełniana przy użyciu kluczy z subskrypcji konta platformy Azure. Funkcję niestandardowego automatycznego sugerowania wymaga warstwy konkretną subskrypcję, zobacz [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>Używanie niestandardowego interfejsu użytkownika

Korzystanie z albo Interfejsie hostowanej: 

- Obejmują skryptu na stronie sieci web  
  
  ```html
  <html>
      <body>
          <script type="text/javascript" 
              id="bcs_js_snippet"
              src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
          </script>
      </body>    
  </html>
  ```

- Możesz też użyć następującego adresu URL w przeglądarce sieci Web.   
  
  `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`  
  
  > [!NOTE]
  > Dodaj następujące parametry zapytania do adresu URL, zgodnie z potrzebami. Aby uzyskać informacje na temat tych parametrów, zobacz [interfejs API wyszukiwania niestandardowego](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) odwołania.
  >
  > - q
  > - mkt
  > - bezpieczne wyszukiwanie
  > - setlang

  > [!IMPORTANT]
  > Nie można wyświetlić strony, zasad zachowania poufności informacji lub inne uwagi i warunki. Może się różnić gotowości do użycia.  

Aby uzyskać dodatkowe informacje, w tym niestandardowy identyfikator konfiguracji, przejdź do **punktów końcowych** w obszarze **produkcji** kartę.

## <a name="configuration-options"></a>Opcje konfiguracji

Można skonfigurować zachowanie hostowanej interfejsu użytkownika, klikając **dodatkowe konfiguracje**i podając wartości. Te ustawienia są opcjonalne. Aby zobaczyć efekt zastosowania lub usuwania ich, zobacz okienko podglądu po prawej stronie. 

### <a name="web-search-configurations"></a>Konfiguracje wyszukiwania w sieci Web

|  |  |
|---------|---------|
|Wyniki z Internetu włączona    | Określa, czy włączono wyszukiwania w Internecie (będzie widoczna na karcie sieci Web, w górnej części strony)        |
|Włącz automatyczne sugerowanie     | Określa, w przypadku niestandardowego automatycznego sugerowania jest włączona (zobacz [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) za dodatkową opłatą).        |
|Wyniki z Internetu na stronę    | Liczba wyników wyszukiwania w sieci web do wyświetlania w danym momencie (maksymalna liczba to 50 wyników na stronę).        |
|Etykieta obrazu   | Określa, gdy obrazy są wyświetlane z wynikami wyszukiwania.|


Następujące konfiguracje są wyświetlane po kliknięciu **Pokaż zaawansowane konfiguracje**:


|  | |
|---------|---------|
|Podświetlania wyrazów     | Określa, jeśli wyniki są wyświetlane z warunkami wyszukiwania pogrubioną czcionką.         |
|Cel łącza    |  Określa, jeśli strony sieci Web zostanie otwarty w nowej karcie przeglądarki (pusty) lub tej samej karcie przeglądarki, (własne), gdy użytkownik kliknie wynik wyszukiwania.        |

### <a name="image-search-configurations"></a>Konfiguracje wyszukiwania obrazów

| | |
|---------|---------|
|Obraz wyników włączone     | Określa, czy włączono wyszukiwania obrazów (zobaczysz kartę obrazy w górnej części strony).            |
|Obraz wyników na stronę     | Liczba wyników wyszukiwania obrazów do wyświetlania w danym momencie (maksymalna liczba to 150 wyników na stronę).          |

Następująca konfiguracja jest wyświetlany po kliknięciu **Pokaż zaawansowane konfiguracje**.  
  
| | |
|---------|---------|
| Włącz filtry     | Dodaje filtry, które użytkownik może użyć do filtrowania obrazów, które zwraca Bing. Na przykład użytkownik można filtrować wyniki tylko animowane pliki GIF.|

### <a name="video-search-configurations"></a>Konfiguracje wyszukiwania klipów wideo

|  | |
|---------|---------|
|Włączone wyników dotyczących wideo     | Określa, czy włączono wyszukiwania klipów wideo (będzie widoczna na karcie filmów wideo w górnej części strony).           |
|Na stronie wyników dotyczących wideo   | Liczba wyników wyszukiwania klipów wideo do wyświetlenia w danym momencie (maksymalna liczba to 150 wyników na stronę).        |

Następująca konfiguracja jest wyświetlany po kliknięciu **Pokaż zaawansowane konfiguracje**.  
  
|  | |
|---------|---------|
|Włącz filtry    | Dodaje filtry, które użytkownik może użyć do filtrowania pliki wideo, które zwraca Bing. Na przykład użytkownik może filtrować wyniki dla filmów wideo z określoną rozdzielczością lub wideo odnaleziono w ostatnich 24 godzinach.          |

### <a name="miscellaneous-configurations"></a>Różne konfiguracje


| |  |
|---------|---------|
|Tytuł strony   | Tekst wyświetlany w obszarze tytułu strony wyników wyszukiwania, (a nie dla układu pop over).        |
|Motyw paska narzędzi    | Określa kolor tła obszaru tytułu strony wyników wyszukiwania. |

Następujące konfiguracje są wyświetlane po kliknięciu **Pokaż zaawansowane konfiguracje**.  

|Kolumna1  |Kolumna2  |
|---------|---------|
|Symbol zastępczy tekstu pola wyszukiwania   | Tekst wyświetlany w przed pole wyszukiwania jako danych wejściowych.        |
|Adres url linku tytułu    |Obiekt docelowy łącza tytuł.         |
|Adres URL logo     | Obraz wyświetlany obok tytułu.         |
|Favicon    | Ikona wyświetlana na pasku tytułu w przeglądarce.          |

Następujące konfiguracje mają zastosowanie tylko wtedy, gdy wykorzystasz hostowanych interfejsu użytkownika za pośrednictwem punktu końcowego HTML (nie można ich stosować użycie fragment kodu JavaScript).

- Tytuł strony
- Motyw paska narzędzi
- Adres URL linku tytułu
- Adres URL logo
- Adres URL Faviicon  

## <a name="next-steps"></a>Kolejne kroki

- [Wyróżnianie tekstu za pomocą znaczników dekoracji](./hit-highlighting.md)
- [Dzielenie na strony wyników wyszukiwania stron internetowych](./page-webpages.md)
