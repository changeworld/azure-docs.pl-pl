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
ms.openlocfilehash: 7f2b97479ffcdb7ec8b3a1a635562d1fe68c3269
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158426"
---
# <a name="configure-your-hosted-ui-experience"></a>Konfigurowanie środowiska obsługiwanego interfejsu użytkownika
Po skonfigurowaniu wystąpienia wyszukiwania niestandardowego, możesz wywołać niestandardowy interfejs API wyszukiwania, aby uzyskać wyniki wyszukiwania i wyświetlić je w swojej aplikacji. Lub, jeśli Twoja aplikacja jest aplikacją sieci web, można użyć hostowanej interfejs użytkownika, który zapewnia wyszukiwania niestandardowego.   

## <a name="configure-custom-hosted-ui"></a>Konfigurowanie niestandardowego hostowanej interfejsu użytkownika
Skorzystaj z poniższych instrukcji, aby skonfigurować hostowanej interfejsu użytkownika, aby uwzględnić w swojej aplikacji sieci web.
1.  Zaloguj się do niestandardowego wyszukiwania [portal](https://customsearch.ai).
2.  Kliknij wystąpienie wyszukiwania niestandardowego. Aby utworzyć wystąpienie, zobacz [Tworzenie pierwszego wystąpienia wyszukiwania niestandardowego Bing](quick-start.md).
3.  Kliknij przycisk **hostowanych interfejsu użytkownika** kartę.
4.  Wybierz układ.
    - Pasek wyszukiwania i wyniki (ustawienie domyślne) &mdash; Wyświetl pole wyszukiwania i wyniki wyszukiwania
    - Powoduje tylko &mdash; nie Wyświetl pole wyszukiwania, Pokaż tylko wyniki
    - Tryb failover POP &mdash; nie Wyświetl pole wyszukiwania, Pokaż tylko wyniki w nakładce przewijania
    
   > [!IMPORTANT]
   > Należy uwzględnić parametr zapytania customConfig podczas wybierania **powoduje tylko** układu, zobacz [parametry zapytania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters).

5.  W obszarze **dodatkowe konfiguracje**, podaj wartości odpowiednie dla twojej aplikacji. Te ustawienia są opcjonalne. Aby zobaczyć efekt zastosowania lub usuwania ich, zobacz okienko podglądu po prawej stronie.  Są dostępne opcje konfiguracji:
    - Konfiguracje wyszukiwania w sieci Web:
        - Wyniki włączone w sieci Web &mdash; decyduje, jeśli zostaną zwrócone wyniki wyszukiwania w sieci web
        - Włącz automatyczne sugerowanie &mdash; Określa czy niestandardowego automatycznego sugerowania jest włączony
        - Wyniki na stronie internetowej &mdash; liczbę wyników wyszukiwania w sieci web do wyświetlenia w czasie
        - Etykieta obrazu &mdash; Określa, czy obrazy są wyświetlane z wynikami wyszukiwania
        - Podświetlania wyrazów &mdash; Określa, czy wyniki są wyświetlane z warunkami wyszukiwania wytłuszczonym drukiem
    - Konfiguracje wyszukiwania obrazów:
        - Obraz wyników włączone &mdash; decyduje, jeśli zostaną zwrócone wyniki wyszukiwania obrazów
    - Dodatkowe konfiguracje:
        - Tytuł strony &mdash; tekst wyświetlany w obszarze tytuł strony
        - Motyw narzędzi &mdash; Określa kolor tła obszaru tytułu strony
        - Symbol zastępczy tekstu w polu wyszukiwania &mdash; tekstu wyświetlanego w przed pole wyszukiwania jako danych wejściowych
        - Adres url linku tytuł &mdash; cel link tytułu
        - Adres url logo &mdash; obraz wyświetlany obok tytułu 
        - Adres url favicon &mdash; ikony wyświetlanej w pasku tytułu w przeglądarce

   > [!IMPORTANT]
   > Musi być włączona co najmniej jeden z wyszukiwania obrazów lub wyszukiwania w Internecie.

6.  Wprowadź klucz subskrypcji wyszukiwania lub wybierz ją z listy rozwijanej. Z listy rozwijanej jest wypełniana przy użyciu kluczy z Twojego konta subskrypcji platformy Azure. Zobacz [konta interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
7.  Jeśli włączono automatyczne sugerowanie, wprowadź klucz subskrypcji automatycznego sugerowania lub wybierać je z listy rozwijanej. Z listy rozwijanej jest wypełniana przy użyciu kluczy z Twojego konta subskrypcji platformy Azure. Funkcję niestandardowego automatycznego sugerowania wymaga określony warstwy subskrypcji, zobacz [stronach z cennikami](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

> [!NOTE]
> Po wprowadzeniu dowolnych zmian do konfiguracji niestandardowej hostowanej interfejsu użytkownika w okienku po prawej stronie zawiera odwołanie w visual zmian wprowadzonych. Wyświetlane wyniki nie są rzeczywiste wyniki dla swojego wystąpienia

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>Używanie niestandardowego interfejsu użytkownika
Korzystanie z albo Interfejsie hostowanej: 

- Obejmują skryptu na stronie sieci web
    ``` html
    <html>
        <body>
            <script type="text/javascript"
                id="bcs_js_snippet"            
                src="https://ui.customsearch.ai/api/ux/render?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate">            
            </script>
        </body>    
    </html>
    ```

- Użyj adresu URL, pod warunkiem `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`

  > [!IMPORTANT]
  > Nie można wyświetlić strony, zasad zachowania poufności informacji lub inne uwagi i warunki. Może się różnić gotowości do użycia.  

Aby uzyskać dodatkowe informacje, w tym niestandardowy identyfikator konfiguracji, przejdź do **punktów końcowych** w obszarze **produkcji** kartę.

## <a name="next-steps"></a>Kolejne kroki
- [Korzystanie ze znaczników dekoracji, aby wyróżnić tekst](./hit-highlighting.md)
- [Strona stron sieci Web](./page-webpages.md)