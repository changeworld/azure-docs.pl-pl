---
title: Konfigurowanie hostowanego interfejsu użytkownika dla wyszukiwania niestandardowego usługi Bing | Dokumenty firmy Microsoft
titleSuffix: Azure Cognitive Services
description: Ten artykuł służy do konfigurowania i integrowania hostowanego interfejsu użytkownika dla wyszukiwania niestandardowego Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: 2cc89bf57167db75404c044f58d18ab48edfaf38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854083"
---
# <a name="configure-your-hosted-ui-experience"></a>Konfigurowanie środowiska hostowanego interfejsu użytkownika

Wyszukiwanie niestandardowe bing zapewnia hostowany interfejs użytkownika, który można łatwo zintegrować ze stronami sieci Web i aplikacjami sieci Web jako fragment kodu JavaScript. Korzystając z portalu wyszukiwania niestandardowego Bing, można skonfigurować opcje układu, koloru i wyszukiwania interfejsu użytkownika.



## <a name="configure-the-custom-hosted-ui"></a>Konfigurowanie niestandardowego hostowanego interfejsu użytkownika

Aby skonfigurować hostowany interfejs użytkownika dla aplikacji sieci web, wykonaj następujące kroki. Po dokonywaniu zmian okienko po prawej stronie zapewnia podgląd interfejsu użytkownika. Wyświetlane wyniki wyszukiwania nie są rzeczywistymi wynikami wystąpienia.

1. Zaloguj się do [portalu](https://customsearch.ai)wyszukiwania niestandardowego Bing .  
  
2. Wybierz wystąpienie wyszukiwania niestandardowego Bing.

3. Kliknij kartę **Hosted UI** (Hostowany interfejs użytkownika).  
  
4. Wybierz układ.

    |  |  |
    |---------|---------|
    |Pasek wyszukiwania i wyniki (domyślnie)    | Wyświetla pole wyszukiwania z wynikami wyszukiwania pod nim.         |
    |Tylko wyniki     | Wyświetla tylko wyniki wyszukiwania bez pola wyszukiwania. Podczas korzystania z tego układu należy`&q=<query string>`podać zapytanie wyszukiwania ( ). Dodaj parametr kwerendy do adresu URL żądania we urywek kodu JavaScript lub łączu punktu końcowego HTML.        |
    |Wyskakujące okienko     | Zawiera pole wyszukiwania i wyświetla wyniki wyszukiwania w nakładce przesuwnej.        |
    
5. Wybierz motyw kolorów. Kolory można dostosować do aplikacji, klikając pozycję **Dostosuj motyw**. Aby zmienić kolor, wprowadź wartość RGB HEX koloru (na `#366eb8`przykład) lub kliknij podgląd kolorów.

   Zmiany można wyświetlać po prawej stronie portalu. Kliknięcie **przycisku Reset do wartości domyślnej** spowoduje przywrócenie zmian do domyślnych kolorów dla wybranego motywu.

   > [!NOTE]
   > Podczas wybierania kolorów należy wziąć pod uwagę dostępność.

6. W **obszarze Konfiguracje dodatkowe**podaj wartości odpowiednie dla aplikacji. Te ustawienia są opcjonalne. Aby zobaczyć efekt ich zastosowania lub usunięcia, zobacz okienko podglądu po prawej stronie. Dostępne opcje konfiguracji to:  

7. Wprowadź klucz subskrypcji wyszukiwania lub wybierz go z listy rozwijanej. Lista rozwijana jest wypełniona kluczami z subskrypcji konta platformy Azure. Zobacz [konto interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).  

8. Jeśli włączono autosueputat, wprowadź klucz subskrypcji autosuełkowej lub wybierz go z listy rozwijanej. Lista rozwijana jest wypełniona kluczami z subskrypcji konta platformy Azure. Niestandardowy autosuesznik wymaga określonej warstwy subskrypcji, zobacz [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>Korzystanie z niestandardowego interfejsu użytkownika

Aby korzystać z hostowanego interfejsu użytkownika: 

- Dołączanie skryptu do strony sieci Web  
  
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
  > W razie potrzeby dodaj następujące parametry zapytania do adresu URL. Aby uzyskać informacje na temat tych parametrów, zobacz odwołanie do [interfejsu API wyszukiwania niestandardowego.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)
  >
  > - q
  > - mkt
  > - Safesearch
  > - setlang ( setlang )

  > [!IMPORTANT]
  > Strona nie może wyświetlać twojego oświadczenia o ochronie prywatności ani innych informacji i warunków. Przydatność do użytku może się różnić.  

Aby uzyskać dodatkowe informacje, w tym identyfikator konfiguracji niestandardowej, przejdź do **punktów końcowych** na karcie **Produkcja.**

## <a name="configuration-options"></a>Opcje konfiguracji

Zachowanie hostowanego interfejsu użytkownika można skonfigurować, klikając pozycję **Dodatkowe konfiguracje**i podając wartości. Te ustawienia są opcjonalne. Aby zobaczyć efekt ich zastosowania lub usunięcia, zobacz okienko podglądu po prawej stronie. 

### <a name="web-search-configurations"></a>Konfiguracje wyszukiwania w sieci Web

|  |  |
|---------|---------|
|Włączono wyniki sieci Web    | Określa, czy wyszukiwanie w internecie jest włączone (u góry strony zostanie wyświetlona karta Sieć Web)        |
|Włącz autosuggest     | Określa, czy automatyczny zasysanie jest włączone (zobacz [cennik](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) dodatkowych kosztów).        |
|Wyniki sieci Web na stronę    | Liczba wyników wyszukiwania w internecie do wyświetlenia w czasie (maksymalna liczba wyników to 50 na stronę).        |
|Podpis obrazu   | Określa, czy obrazy są wyświetlane z wynikami wyszukiwania.|


Następujące konfiguracje są wyświetlane po **kliknięciu przycisku Pokaż konfiguracje zaawansowane:**


|  | |
|---------|---------|
|Wyróżnianie wyrazów     | Określa, czy wyniki są wyświetlane z pogrubionymi terminami wyszukiwania.         |
|Miejsce docelowe łącza    |  Określa, czy strona sieci Web zostanie otwarta na nowej karcie przeglądarki (Puste) lub na tej samej karcie przeglądarki (jaźń), gdy użytkownik kliknie wynik wyszukiwania.        |

### <a name="image-search-configurations"></a>Konfiguracje wyszukiwania obrazów

| | |
|---------|---------|
|Włączona funkcja wyników obrazu     | Określa, czy wyszukiwanie obrazów jest włączone (u góry strony zostanie wyświetlona karta Obrazy).            |
|Wyniki obrazów na stronę     | Liczba wyników wyszukiwania obrazów do wyświetlenia w czasie (maksymalna liczba wyników to 150 na stronę).          |

Następująca konfiguracja jest wyświetlana po **kliknięciu przycisku Pokaż konfiguracje zaawansowane**.  
  
| | |
|---------|---------|
| Włączanie filtrów     | Dodaje filtry, których użytkownik może używać do filtrowania obrazów zwracanych przez bing. Na przykład użytkownik może filtrować wyniki tylko dla animowanych plików GIF.|

### <a name="video-search-configurations"></a>Konfiguracje wyszukiwania wideo

|  | |
|---------|---------|
|Włączone wyniki wideo     | Określa, czy wyszukiwanie wideo jest włączone (u góry strony zobaczysz kartę Wideo).           |
|Wyniki wideo na stronę   | Liczba wyników wyszukiwania wideo do wyświetlenia w czasie (maksymalna liczba wyników to 150 na stronie).        |

Następująca konfiguracja jest wyświetlana po **kliknięciu przycisku Pokaż konfiguracje zaawansowane**.  
  
|  | |
|---------|---------|
|Włączanie filtrów    | Dodaje filtry, których użytkownik może używać do filtrowania filmów wideo zwracanych przez bing. Na przykład użytkownik może filtrować wyniki dla filmów o określonej rozdzielczości lub filmów wykrytych w ciągu ostatnich 24 godzin.          |

### <a name="miscellaneous-configurations"></a>Różne konfiguracje


| |  |
|---------|---------|
|Tytuł strony   | Tekst wyświetlany w obszarze tytułu strony wyników wyszukiwania (nie dla układu podręcznego).        |
|Kompozycja Pasek narzędzi    | Określa kolor tła obszaru tytułu strony wyników wyszukiwania. |

Następujące konfiguracje są wyświetlane po **kliknięciu przycisku Pokaż konfiguracje zaawansowane**.  

|Kolumna 1  |Kolumna 2  |
|---------|---------|
|Symbol zastępczy tekstu pola wyszukiwania   | Tekst wyświetlany w polu wyszukiwania przed wprowadzeniem.        |
|Adres URL linku do tytułu    |Miejsce docelowe dla linku do tytułu.         |
|Logo URL     | Obraz wyświetlany obok tytułu.         |
|Favicon    | Ikona wyświetlana na pasku tytułu przeglądarki.          |

Następujące konfiguracje mają zastosowanie tylko wtedy, gdy użytkownik korzysta z hostowanego interfejsu użytkownika za pośrednictwem punktu końcowego HTML (nie mają one zastosowania, jeśli używasz fragmentu kodu JavaScript).

- Tytuł strony
- Kompozycja Pasek narzędzi
- Adres URL łącza tytułu
- Logo URL
- Faviicon URL  

## <a name="next-steps"></a>Następne kroki

- [Wyróżnianie tekstu za pomocą znaczników dekoracji](../bing-web-search/hit-highlighting.md)
- [Dzielenie na strony wyników wyszukiwania stron internetowych](./page-webpages.md)
