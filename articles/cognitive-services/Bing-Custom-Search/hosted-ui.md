---
title: 'Wyszukiwanie niestandardowe Bing: Wyszukiwanie w witrynie | Dokumentacja firmy Microsoft'
description: Opisuje sposób konfigurowania hostowanej interfejsu użytkownika
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 593ea4d23f8ddcec8efc4be632afa2aab1a5210f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347869"
---
# <a name="configure-your-hosted-ui-experience"></a>Konfigurowanie środowiska hostowanej interfejsu użytkownika
Po skonfigurowaniu wyszukiwania niestandardowego wystąpienia, należy wywołać niestandardowy interfejs API wyszukiwania, aby uzyskać wyniki wyszukiwania i wyświetlić je w aplikacji. Lub jeśli aplikacji jest to aplikacja sieci web, możesz użyć hostowanej interfejsu użytkownika, który umożliwia wyszukiwanie niestandardowe.   

## <a name="configure-custom-hosted-ui"></a>Konfigurowanie niestandardowego hostowanej interfejsu użytkownika
Użyj poniższych instrukcji, aby skonfigurować hostowanej interfejsu użytkownika do uwzględnienia w aplikacji sieci web.
1.  Zaloguj się do niestandardowego wyszukiwania [portal](https://customsearch.ai).
2.  Kliknij przycisk wystąpienie wyszukiwanie niestandardowe. Aby utworzyć wystąpienie, zobacz [Tworzenie pierwszego wystąpienia wyszukiwania usługi Bing niestandardowe](quick-start.md).
3.  Kliknij przycisk **hostowanej interfejsu użytkownika** kartę.
4.  Wybierz układ.
    - Pasek wyszukiwania i wyników (ustawienie domyślne) &mdash; Wyświetl pole wyszukiwania i wyników wyszukiwania
    - Powoduje tylko &mdash; nie wyświetlać okno wyszukiwania, Pokaż tylko wyniki
    - Tryb failover POP &mdash; nie wyświetlać okno wyszukiwania, Pokaż wyniki tylko w metodzie przesuwanego nakładki
    
   > [!IMPORTANT]
   > Należy uwzględnić parametr zapytania customConfig, wybierając **powoduje tylko** układu, zobacz [parametry zapytania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters).

5.  W obszarze **dodatkowe konfiguracje**, podaj wartości na potrzeby aplikacji. Te ustawienia są opcjonalne. Aby zobaczyć efekt zastosowania lub usuwania ich, zobacz okienko podglądu po prawej stronie.  Są dostępne opcje konfiguracji:
    - Konfiguracje wyszukiwania w sieci Web:
        - Sieci Web wyniki włączone &mdash; Określa, czy są zwracane wyniki wyszukiwania w sieci web
        - Włączanie automatycznego sugerowania &mdash; Określa czy automatycznego sugerowania niestandardowy jest włączone
        - Sieci Web wyników na stronie &mdash; liczbę wyników wyszukiwania w sieci web do wyświetlenia w czasie
        - Etykieta obrazu &mdash; Określa, czy obrazy są wyświetlane z wynikami wyszukiwania
        - Podświetlania wyrazów &mdash; Określa, czy wyniki są wyświetlane z warunkami wyszukiwania wytłuszczonym drukiem
    - Obraz wyszukiwania konfiguracje:
        - Obraz wyników włączone &mdash; Określa, czy obraz wyniki wyszukiwania zostaną zwrócone
    - Różne konfiguracje:
        - Tytuł strony &mdash; tekst wyświetlany w obszarze tytuł strony
        - Motyw narzędzi &mdash; Określa kolor tła obszaru tytułu strony
        - Pole tekst zastępczy wyszukiwania &mdash; tekst wyświetlany w danych wejściowych przed umożliwieniem pole wyszukiwania
        - Adres url linku tytuł &mdash; docelowego dla łącza tytułu
        - Adres url logo &mdash; obraz wyświetlany obok nazwy 
        - Adres url favicon &mdash; ikona wyświetlana na pasku tytułu przeglądarki

   > [!IMPORTANT]
   > Musi być włączony co najmniej jeden z obrazów wyszukiwania lub sieci Web.

6.  Wprowadź klucz wyszukiwania subskrypcji, lub wybierz jedną z listy rozwijanej. Na liście rozwijanej jest wypełniane przy użyciu kluczy z konta subskrypcji platformy Azure. Zobacz [kognitywnych interfejsu API usług konta](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
7.  Jeśli włączono Autosugerowanie wprowadź klucz Autosugerowanie subskrypcji lub wybierz jedną z listy rozwijanej. Na liście rozwijanej jest wypełniane przy użyciu kluczy z konta subskrypcji platformy Azure. Niestandardowe Autosugerowanie wymaga warstwy subskrypcji określony, zobacz [cennik stron](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

> [!NOTE]
> Po wprowadzeniu dowolnych zmian do konfiguracji niestandardowej hostowanej interfejsu użytkownika, w okienku po prawej stronie zapewnia odwołanie w visual zmian. Nie są wyświetlane wyniki rzeczywiste wyniki dla swojego wystąpienia

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>Korzystanie z niestandardowego interfejsu użytkownika
Użycie hostowanej interfejsu użytkownika, albo: 

- Obejmują skrypt na stronie sieci web
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

- Użyj podanego adresu URL `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`

  > [!IMPORTANT]
  > Nie można wyświetlić strony, zachowania poufności lub inne uwagi i warunki. Przydatności do użycia, może się różnić.  

Aby uzyskać dodatkowe informacje, łącznie z niestandardowy identyfikator konfiguracji, przejdź do **punkty końcowe** w obszarze **produkcji** kartę.

## <a name="next-steps"></a>Kolejne kroki
- [Umożliwia znaczników decoration wyróżnianie tekstu](./hit-highlighting.md)
- [Strona stron sieci Web](./page-webpages.md)