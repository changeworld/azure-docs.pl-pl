---
title: "Dzielenie kolumny według przekształcania przykład za pomocą usługi Azure Machine Learning Workbench"
description: "W dokumencie referencyjnym transformacji \"Podział kolumny przykładzie\""
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 497c1725fc4554792add11c0ec069d1628a89fbd
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2018
---
# <a name="split-column-by-example-transformation"></a>Podziel kolumnę według przykład przekształcania
Tej transformacji predictively dzieli zawartość kolumny na granicach znaczące bez wprowadzania danych przez użytkownika. Algorytm podziału wybiera granice po przeanalizowaniu zawartości kolumny. Te granice może być zdefiniowana przez
* Stałe ogranicznika
* Wiele, ograniczniki dowolnego znajdujących się w szczególności kontekstów, lub,
* Wzorce danych lub niektórych typów jednostek

Użytkownicy mogą kontrolować podział zachowania w trybie zaawansowanym, gdzie można określić ograniczniki lub przez zawierają przykłady żądaną podziału.

Teoretycznie podziału operacje mogą być wykonywane w Workbench, używając szeregu *kolumny pochodzi przykładzie* przekształca. Jednak w przypadku kilku kolumn Wyprowadzanie każdego z tych indywidualnie nawet przy użyciu podejścia przez przykład może być bardzo czasochłonne. Podziel predykcyjnej umożliwia łatwe dzielenia bez konieczności zawierają przykłady dla każdej kolumny użytkownika. 

## <a name="how-to-perform-this-transformation"></a>Jak wykonać tego przekształcenia

1. Wybierz kolumnę, którą chcesz podzielić. 
2. Wybierz **podzielona kolumna przykładzie** z **przekształca** menu. Lub, kliknij prawym przyciskiem myszy nagłówka wybranej kolumny i wybierz **podzielona kolumna przykładzie** z menu kontekstowego. Otwiera edytora transformacji i obok zaznaczonej kolumnie są dodawane nowe kolumny. W tym momencie Workbench analizuje kolumny wejściowej, określa granice podziału i zsyntetyzuje zmniejszonych programu do dzielenia kolumny wyświetlane w siatce. Program syntezatora jest wykonywany przed wszystkie wiersze w kolumnie. Ogranicznik, są wykluczane z wyników końcowych.
3. Możesz kliknąć **trybu zaawansowanego** uzyskać bardziej precyzyjną kontrolę nad przekształcania podziału. 
4. Przejrzyj dane wyjściowe i kliknij przycisk **OK** do akceptowania transformacji. 

Transformacja ma na celu utworzyć taką samą liczbę kolumn wynikowe dla wszystkich wierszy. Jeśli wszystkie wiersze, nie można podzielić na podstawie granice, tworzy *null* dla wszystkich kolumn domyślnie. To zachowanie można zmienić w **trybu zaawansowanego**.

### <a name="transform-editor-advanced-mode"></a>Przekształć edytora: Tryb zaawansowany
**Tryb zaawansowany** zapewnia bardziej rozbudowane środowisko do dzielenia kolumny. 

Wybieranie **Zachowaj kolumny ogranicznik** zawiera ograniczniki w wynik końcowy. Ograniczniki wykluczonych domyślnie.

Określanie **ograniczniki** zastępuje logiki wyboru automatycznego ogranicznika. Wiele ograniczników, jeden w każdym wierszu, można określić jako **ograniczniki**. Te znaki są używane jako ograniczników do dzielenia kolumny.

Czasami podziału wartości na podstawie granice i tworzy różną liczbę kolumn niż większość pozostałych. W takich przypadkach **wypełnienia kierunek** służy do określania kolejności, w którym powinno być wypełnione kolumny.

Kliknięcie **Pokaż sugerowane przykłady** wyświetla wiersze reprezentatywny dla użytkownika, który powinien podać przykład podziału. Użytkownik może kliknąć na **się** strzałkę po prawej stronie sugerowane wiersza, aby promować wiersza jako przykład. 

Użytkownik może **kolumna usunąć** lub **nowe kolumny do wstawienia** klikając prawym przyciskiem myszy w nagłówku **przykłady tabeli**.

Użytkownik może kopiować i Wklej wartości z jedną komórkę do innego celu zapewnienia przykład podziału.

Użytkownik może przełączać się między **trybie podstawowym** i **trybu zaawansowanego** po kliknięciu łączy w edytorze transformacji.

### <a name="transform-editor-send-feedback"></a>Przekształć edytora: wysyłanie opinii

Kliknięcie **wysłać opinię** połączyć otwartym **opinii** udostępnił okna dialogowego z polem komentarze wstępnie wypełnione wybrane parametry, a użytkownik przykłady. Użytkownik powinien przejrzeć zawartość pola komentarze i zawierają więcej szczegółowych informacji, aby pomóc nam zrozumieć problem. Jeśli użytkownik nie chce udostępniać dane firmy Microsoft, użytkownik powinien usunąć wstępnie przykładowe dane przed kliknięciem przycisku **Prześlij opinię** przycisku. 


### <a name="editing-an-existing-transformation"></a>Edytowanie istniejącego przekształcania

Użytkownik może edytować istniejące **kolumny podzielone przez przykład** przekształcenie wybierając **Edytuj** opcji krok transformacji. Kliknięcie **Edytuj** otwiera edytor Przekształć w **trybu zaawansowanego**, i są wyświetlane wszystkie przykłady, które zostały podane podczas tworzenia transformacji.

## <a name="examples-of-splitting-on-a-fixed-single-character-delimiter"></a>Przykłady dzielenia na stałe, jeden znak ogranicznika

Jest typowe dla pól danych oddzielić pojedynczy ogranicznik stałym takich jak przecinka w formacie CSV. Przekształcanie podziału próbuje automatycznie wnioskować te ograniczników. Na przykład w poniższym scenariuszu on automatycznie wnioskuje "." jako ogranicznik.

### <a name="splitting-ip-addresses"></a>Adresy IP dzielenia

Wartości w pierwszej kolumnie predictively są dzielone na cztery kolumny.

|Adres IP|IP_1|IP_2|IP_3|IP_4|
|:-----|:-----|:-----|:-----|:-----|
|192.168.0.102|192|168|0|102|
|192.138.0.101|192|138|0|101|
|192.168.0.102|192|168|0|102|
|192.158.1.202|192|158|1|202|
|192.168.0.102|192|168|0|102|
|192.169.1.102|192|169|1|102|

## <a name="examples-of-splitting-on-multiple-delimiters-within-particular-contexts"></a>Przykłady podzielić na wiele ograniczniki w szczególności kontekstów

Dane użytkownika może zawierać wiele różnych ograniczników oddzielanie polami. Ponadto tylko niektórych wystąpień ciągu rozdzielające mogą być ogranicznik, ale nie wszystkich. Na przykład w przypadku następujących zestaw ograniczników wymagane jest "-",","i":" Aby utworzyć żądanego wyniku. Jednak nie wszystkie wystąpienia ":" powinna być punkt podziału, ponieważ nie chcemy Podziel momencie, ale pozostawić ją w jednej kolumnie. Przekształcanie podziału wnioskuje ograniczniki w sytuacjach, w których występują w danych wejściowych, a nie wszystkie możliwe wystąpieniu ogranicznika. Transformacja jest również pamiętać o typowych danych, takich jak daty i godziny.   

### <a name="splitting-store-opening-timings"></a>Dzielenie chronometrażu otwierania magazynu

Wartości w następującym *chronometrażu* kolumny predictively uzyskać podzielony na dziewięć kolumny wyświetlane w tabeli poniżej.

|Chronometrażu|
|:-----|
|Poniedziałek — piątek: 7:00:00 - 18:00:00, sobota: 9:00 am - 5:00 pm, niedziela: zamknięte|
|Poniedziałek — piątek: 9:00 am — 18:00:00, sobota: 4:00:00 - 4:00 pm, niedziela: zamknięte|
|Poniedziałek — piątek: 8:30 am - 7:00 pm, sobota: 3:00 am — 14:30:00, niedziela: zamknięte|
|Poniedziałek — piątek: 8:00 am — 18:00:00, sobota: 2:00 am - 3:00 pm, niedziela: zamknięte|
|Poniedziałek — piątek: 4:00:00 - 7:00 pm, sobota: 9:00 am — 4:00 pm, niedziela: zamknięte|
|Poniedziałek — piątek: 8:30 am — 4:30 będzie, sobota: 9:00 am - 5:00 pm, niedziela: zamknięte|
|Poniedziałek — piątek: 5:30:00 - 18:30:00, sobota: 5:00:00 - 4:00 pm, niedziela: zamknięte|
|Poniedziałek — piątek: 8:30 am — 8:30 będzie, sobota: 6:00:00 - 5:00 pm, niedziela: zamknięte|
|Poniedziałek — piątek: 8:00 am — 21:00:00, sobota: 9:00 am — 8:00 pm, niedziela: zamknięte|
|Poniedziałek — piątek: 10:00 am — 9:30 będzie, sobota: 9:30 am - 3:00 pm, niedziela: zamknięte|

|Timings_1|Timings_2|Timings_3|Timings_4|Timings_5|Timings_6|Timings_7|Timings_8|Timings_9|
|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|
|Poniedziałek|Piątek|7:00:00|18:00:00|Sobota|9:00|5:00 pm.|Niedziela|Zamknięte|
|Poniedziałek|Piątek|9:00|18:00:00|Sobota|4:00:00|4:00 pm.|Niedziela|Zamknięte|
|Poniedziałek|Piątek|8:30 am|7:00 pm.|Sobota|3:00 am|2:30 będzie|Niedziela|Zamknięte|
|Poniedziałek|Piątek|8:00 am|18:00:00|Sobota|2:00 am|3:00 pm|Niedziela|Zamknięte|
|Poniedziałek|Piątek|4:00:00|7:00 pm.|Sobota|9:00|4:00 pm.|Niedziela|Zamknięte|
|Poniedziałek|Piątek|8:30 am|4:30 będzie|Sobota|9:00|5:00 pm.|Niedziela|Zamknięte|
|Poniedziałek|Piątek|5:30:00|18:30:00|Sobota|5:00:00|4:00 pm.|Niedziela|Zamknięte|
|Poniedziałek|Piątek|8:30 am|8:30 będzie|Sobota|6:00:00|5:00 pm.|Niedziela|Zamknięte|
|Poniedziałek|Piątek|8:00 am|9:00 pm.|Sobota|9:00|8:00 pm.|Niedziela|Zamknięte|
|Poniedziałek|Piątek|10:00 am|9:30 będzie|Sobota|9:30 am|3:00 pm|Niedziela|Zamknięte|

### <a name="splitting-iis-log"></a>Podział dziennik IIS

Oto przykład innego wielu dowolnego ograniczników. W tym przykładzie dołączono również kontekstowe ogranicznika "/", która nie musi być podzielony wewnątrz adresy URL lub ścieżki pliku. Jest niewygodne i wykonać taki podział przy użyciu wielu *kolumny pochodzi przykładzie* transformacji i podając przykłady dla każdego pola. Za pomocą elementu transform podziału oferujemy, predykcyjnej dzielenia bez nadawania przykładami.

|logtext|
|:-----|
|192.128.138.20 — [2016-16-Oct 16:22:33-0200] "/images/picture.gif GET protokołu HTTP/1.1" 234 343 www.yahoo.com "http://www.example.com/" "Mozilla/4.0 (zgodny; MSIE 4)""-"|
|10.128.72.213 — [2016-17-Oct 12:43:12 +0300] "GET /news/stuff.html HTTP/1.1" 200 6233 www.aol.com "http://www.sample.com/" "Mozilla/5.0 (MSIE)" "-"|
|192.165.71.165 - - [12/Nov/2016 14:22:44 -0500] "GET /sample.ico HTTP/1.1" 342 7342 www.facebook.com "-" "Mozilla/5.0 (Windows; U; Windows NT 5.1; rv:1.7.3)" "-"|
|10.166.64.165 — [2016-23-lis 01:52:45 -0800] "GET /style.css HTTP/1.1" 200 www.google.com 2552 "http://www.test.com/index.html" "Mozilla/5.0 (system Windows)" "-"|
|192.167.1.193 - - [16/Jan/2017 22:34:56 +0200] "GET /js/ads.js HTTP/1.1" 200 23462 www.microsoft.com "http://www.illustration.com/index.html" "Mozilla/5.0 (Windows)" "-"|
|192.147.76.193 — [2017-28/Jan 26:36:16 +0800] "GET /search.php HTTP/1.1" 400 1777 www.bing.com "-" "Mozilla/4.0 (zgodny; MSIE 6.0; Windows NT 5.1)""-"|
|192.166.64.165 — [2017-23/marca 01:55:25 -0800] "GET /style.css HTTP/1.1" 200 www.google.com 2552 "http://www.test.com/index.html" "Mozilla/5.0 (system Windows)" "-"|
|11.167.1.193 - - [16/Apr/2017 11:34:36 +0200] "GET /js/ads.js HTTP/1.1" 200 23462 www.microsoft.com "http://www.illustration.com/index.html" "Mozilla/5.0 (Windows)" "-"|

Pobiera podzielony na:

|logtext_1|logtext_2|logtext_3|logtext_4|logtext_5|logtext_6|logtext_7|logtext_8|logtext_9|logtext_10|logtext_11|logtext_12|logtext_13|logtext_14|logtext_15|
|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|
|192.128.138.20|2016-16-Oct|16:22:33|-0200|GET|images/picture.gif|HTTP|1.1|234|343|www.yahoo.com|http://www.example.com/|Mozilla|4.0|zgodny; MSIE 4|
|10.128.72.213|2016-17-Oct|12:43:12|+0300|GET|news/stuff.html|HTTP|1.1|200|6233|www.aol.com|http://www.sample.com/|Mozilla|5.0|MSIE|
|192.165.71.165|2016-12-lis|14:22:44|-0500|GET|sample.ico|HTTP|1.1|342|7342|www.facebook.com|-|Mozilla|5.0|Windows; U; Windows NT 5.1; rv:1.7.3|
|10.166.64.165|2016-23-lis|01:52:45|-0800|GET|style.css|HTTP|1.1|200|2552|www.google.com|http://www.test.com/index.html|Mozilla|5.0|Windows|
|192.167.1.193|2017-16/Jan|22:34:56|+0200|GET|js/ads.js|HTTP|1.1|200|23462|www.microsoft.com|http://www.illustration.com/index.html|Mozilla|5.0|Windows|
|192.147.76.193|2017-28/Jan|26:36:16|+0800|GET|Search.php|HTTP|1.1|400|1777|www.bing.com|-|Mozilla|4.0|zgodny; MSIE 6.0; Windows NT w wersji 5.1|
|192.166.64.165|2017-23/marca|01:55:25|-0800|GET|style.css|HTTP|1.1|200|2552|www.google.com|http://www.test.com/index.html|Mozilla|5.0|Windows|
|11.167.1.193|2017-16/kwietnia|11:34:36|+0200|GET|js/ads.js|HTTP|1.1|200|23462|www.microsoft.com|http://www.illustration.com/index.html|Mozilla|5.0|Windows|

## <a name="examples-of-splitting-without-delimiters"></a>Przykłady dzielenia bez ograniczników
W niektórych przypadkach nie ma żadnych rzeczywistych ograniczniki, a pola danych, mogą występować ciągłym obok siebie. W takim przypadku przekształcania podziału automatycznie wykrywa wzorce w danych na potrzeby wnioskowania dotyczącego prawdopodobnie punkty podziału. Na przykład w poniższym scenariuszu chcemy wartość z innej niż typ waluty i podziału automatycznie wnioskuje granicę między danych liczbowych i nieliczbowy jako punkt podziału.

### <a name="splitting-amount-with-currency-symbol"></a>Podział ilości do symbolu waluty

|Ilość|Amount_1|Amount_2|
|:-----|:-----|:-----|
|\$14|$|14|
|£9|£|9|
|\$34|$|34|
|€ 18|€ |18|
|\$42|$|42|
|\$7|$|7|
|£42|£|42|
|\$16|$|16|
|€ 16|€ |16|
|\$15|$|15|
|\$16|$|16|
|€ 64|€ |64|

W poniższym przykładzie chcemy oddzielanie wartości wagi z jednostki miary. Ponownie wnioskowania podziału automatycznie wykrywa znaczący granic i takie jak preferowana przez inne ograniczniki możliwe "." znaków. 

### <a name="splitting-weights-with-units"></a>Dzielenie wag z jednostki

|Waga|Weight_1|Weight_2|
|:-----|:-----|:-----|
|2.27KG|2.27|KG|
|1L|1|L|
|2.5KG|2.5|KG|
|2KG|2|KG|
|1.7KGA|1.7|KGA|
|3KG|3|KG|
|2KG|2|KG|
|125G|125|G|
|500G|500|G|
|1.5KGA|1.5|KGA|

## <a name="technical-notes"></a>Uwagi techniczne

Funkcja transformacji podziału jest oparta na **predykcyjnej syntezy Program** technik. W tej metodzie programy przekształcania danych są rozpoznawane automatycznie w oparciu o dane wejściowe. Te programy są syntezatora języka specyficznego dla domeny. DSL jest oparta na ograniczniki i pola, które występują w szczególności kontekstów wyrażenia regularnego. Więcej informacji na temat tej technologii znajduje się w [ostatnie publikacji na ten temat](https://www.microsoft.com/en-us/research/publication/automated-data-extraction-using-predictive-program-synthesis/). 
