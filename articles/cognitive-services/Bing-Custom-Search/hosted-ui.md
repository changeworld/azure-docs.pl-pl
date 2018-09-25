---
title: 'Bing Custom Search: Wyszukiwanie w witrynie | Dokumentacja firmy Microsoft'
description: W tym artykule opisano sposób konfigurowania hostowanej interfejsu użytkownika
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 2aec8ba969fb639f2d785a429441f6ed4bbf7dfb
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987683"
---
# <a name="configure-your-hosted-ui-experience"></a>Konfigurowanie środowiska obsługiwanego interfejsu użytkownika

Po skonfigurowaniu wystąpienia wyszukiwania niestandardowego, możesz wywołać niestandardowy interfejs API wyszukiwania, aby uzyskać wyniki wyszukiwania i wyświetlić je w swojej aplikacji. Lub, jeśli Twoja aplikacja jest aplikacją sieci web, można użyć hostowanej interfejs użytkownika, który zapewnia wyszukiwania niestandardowego.   

## <a name="configure-custom-hosted-ui"></a>Konfigurowanie niestandardowego hostowanej interfejsu użytkownika

Aby skonfigurować hostowanej interfejsu użytkownika dla aplikacji sieci web, wykonaj następujące kroki:

1. Zaloguj się do niestandardowego wyszukiwania [portal](https://customsearch.ai).  
  
2. Kliknij wystąpienie wyszukiwania niestandardowego. Aby utworzyć wystąpienie, zobacz [Tworzenie pierwszego wystąpienia wyszukiwania niestandardowego Bing](quick-start.md).  

3. Kliknij przycisk **hostowanych interfejsu użytkownika** kartę.  
  
4. Wybierz układ.
  
  - Pasek wyszukiwania i wyniki (ustawienie domyślne) &mdash; ten układ jest strony tradycyjnych wyszukiwania przy użyciu pola wyszukiwania i wyniki wyszukiwania.
  - Powoduje tylko &mdash; wyświetla tylko wyniki wyszukiwania, ten układ. Ten układ nie są wyświetlane pola wyszukiwania. Należy podać zapytanie wyszukiwania, dodając parametr zapytania (& q =\<ciągu zapytania >) do adresu URL żądania w fragment kodu JavaScript lub łącza do endpoint HTML.
  - Tryb failover POP &mdash; ten układ zawiera pole wyszukiwania i wyświetla wyniki wyszukiwania w nakładce przewijania.
      
5. Wybierz motyw kolorów. Motywy możliwe są następujące: 
  
  - Wdrożenie klasyczne
  - Ciemny
  - Niebieski skyline

  Kliknij każdą z tematów, aby zobaczyć, który motyw działa najlepiej z aplikacją sieci web. Jeśli musisz dostosowywać motyw kolorów, aby lepiej Integracja z aplikacją sieci web, kliknij przycisk **motyw Dostosuj**. Nie wszystkie konfiguracje kolor mają zastosowanie do wszystkich motywy układu. Aby zmienić kolor, wprowadź wartość SZESNASTKOWY RGB koloru (na przykład #366eb8) w odpowiednim polu tekstowym. Ewentualnie kliknij przycisk koloru, a następnie kliknij przycisk odcienia, który Ci odpowiada. 
  
  Po zmianie koloru, zapoznaj się z wpływ zmiany na przykład (wersja zapoznawcza) po prawej stronie. Zawsze można kliknąć przycisk **Przywróć domyślne** aby wrócić do domyślne kolory dla wybranego motywu.

  > [!NOTE]
  > Kiedy zmieniasz motyw kolorów, należy wziąć pod uwagę ułatwienia dostępu podczas wybierania kolorów.

5. W obszarze **dodatkowe konfiguracje**, podaj wartości odpowiednie dla twojej aplikacji. Te ustawienia są opcjonalne. Aby zobaczyć efekt zastosowania lub usuwania ich, zobacz okienko podglądu po prawej stronie. Są dostępne opcje konfiguracji:  
  
  - Konfiguracje wyszukiwania w sieci Web:
    - Wyniki włączone w sieci Web &mdash; Określa, czy włączono wyszukiwania w Internecie (będzie widoczna na karcie sieci Web, w górnej części strony).
    - Włącz automatyczne sugerowanie &mdash; Określa czy niestandardowego automatycznego sugerowania jest włączony (zobacz [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) za dodatkową opłatą).
    - Wyniki na stronie internetowej &mdash; liczbę wyników wyszukiwania w sieci web do wyświetlania w danym momencie (maksymalna liczba to 50 wyników na stronę).
    - Etykieta obrazu &mdash; Określa, czy obrazy są wyświetlane z wynikami wyszukiwania.
  
    Następujące konfiguracje są wyświetlane po kliknięciu **Pokaż zaawansowane konfiguracje**.  
  
    - Podświetlania wyrazów &mdash; Określa, czy wyniki są wyświetlane z warunkami wyszukiwania pogrubioną czcionką. 
    - Cel łącza &mdash; decyduje, jeśli strony sieci Web zostanie otwarty w nowej karcie przeglądarki (pusty) lub tej samej karcie przeglądarki, (własne), gdy użytkownik kliknie wynik wyszukiwania. 

  - Konfiguracje wyszukiwania obrazów:
    - Obraz wyników włączone &mdash; Określa, czy włączono wyszukiwania obrazów (zobaczysz kartę obrazy w górnej części strony).   
    - Obraz wyników na stronę &mdash; liczbę wyników wyszukiwania obrazów do wyświetlania w danym momencie (maksymalna liczba to 150 wyników na stronę).  
  
    Następująca konfiguracja jest wyświetlany po kliknięciu **Pokaż zaawansowane konfiguracje**.  
  
    - Włącz filtry &mdash; dodaje filtry, które użytkownik może użyć do filtrowania obrazów, które zwraca Bing. Na przykład użytkownik można filtrować wyniki tylko animowane pliki GIF.

  - Konfiguracje wyszukiwania klipów wideo:
    - Wyników dotyczących wideo z włączoną &mdash; Określa, czy włączono wyszukiwania klipów wideo (będzie widoczna na karcie filmów wideo w górnej części strony).  
    - Na stronie wyników dotyczących wideo &mdash; liczbę wyników wyszukiwania klipów wideo do wyświetlenia w danym momencie (maksymalna liczba to 150 wyników na stronę).
  
    Następująca konfiguracja jest wyświetlany po kliknięciu **Pokaż zaawansowane konfiguracje**.  
  
    - Włącz filtry &mdash; dodaje filtry, które użytkownik może użyć do filtrowania pliki wideo, które zwraca Bing. Na przykład użytkownik może filtrować wyniki dla filmów wideo z określoną rozdzielczością lub wideo odnaleziono w ostatnich 24 godzinach.

  - Dodatkowe konfiguracje:
    - Tytuł strony &mdash; tekst wyświetlany w obszarze tytułu strony wyników wyszukiwania, (a nie dla układu pop over).
    - Motyw narzędzi &mdash; Określa kolor tła obszaru tytułu strony wyników wyszukiwania.  
  
    Następujące konfiguracje są wyświetlane po kliknięciu **Pokaż zaawansowane konfiguracje**.  
  
    - Symbol zastępczy tekstu w polu wyszukiwania &mdash; tekstu wyświetlanego w przed pole wyszukiwania jako danych wejściowych.
    - Adres url linku tytuł &mdash; docelowego dla linku tytuł.
    - Adres url logo &mdash; obraz wyświetlany obok tytułu. 
    - Adres url favicon &mdash; ikony wyświetlanej w pasku tytułu w przeglądarce.  

    Następujące konfiguracje mają zastosowanie tylko wtedy, gdy wykorzystasz hostowanych interfejsu użytkownika za pośrednictwem punktu końcowego HTML (nie można ich stosować użycie fragment kodu JavaScript).
    
    - Tytuł strony
    - Motyw paska narzędzi
    - Adres URL linku tytułu
    - Adres URL logo
    - Adres URL Faviicon  
  
6. Wprowadź klucz subskrypcji wyszukiwania lub wybierz ją z listy rozwijanej. Na liście rozwijanej jest wypełniana przy użyciu kluczy z subskrypcji konta platformy Azure. Zobacz [konta interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).  

7. Jeśli włączono automatyczne sugerowanie, wprowadź klucz subskrypcji automatycznego sugerowania, lub wybierz jednego z listy rozwijanej. Na liście rozwijanej jest wypełniana przy użyciu kluczy z subskrypcji konta platformy Azure. Funkcję niestandardowego automatycznego sugerowania wymaga warstwy konkretną subskrypcję, zobacz [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

> [!NOTE]
> Po wprowadzeniu dowolnych zmian do konfiguracji niestandardowej hostowanej interfejsu użytkownika w okienku po prawej stronie zawiera odwołanie w visual zmian wprowadzonych. Wyniki wyszukiwania w wyświetlonej nie są rzeczywiste wyniki dla wystąpienia.

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

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
  > Dodaj następujące parametry zapytania do adresu URL, zgodnie z potrzebami. Aby uzyskać informacje na temat tych parametrów, zobacz [interfejs API wyszukiwania niestandardowego](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters) odwołania.
  >
  > - pytania i odpowiedzi
  > - mkt
  > - bezpieczne wyszukiwanie
  > - setlang

  > [!IMPORTANT]
  > Nie można wyświetlić strony, zasad zachowania poufności informacji lub inne uwagi i warunki. Może się różnić gotowości do użycia.  

Aby uzyskać dodatkowe informacje, w tym niestandardowy identyfikator konfiguracji, przejdź do **punktów końcowych** w obszarze **produkcji** kartę.

## <a name="next-steps"></a>Kolejne kroki

- [Korzystanie ze znaczników dekoracji, aby wyróżnić tekst](./hit-highlighting.md)
- [Strona stron sieci Web](./page-webpages.md)