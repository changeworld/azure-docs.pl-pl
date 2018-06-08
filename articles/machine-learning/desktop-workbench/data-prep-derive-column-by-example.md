---
title: Pochodzi kolumny przez przekształcania przykład za pomocą usługi Azure Machine Learning Workbench
description: W dokumencie referencyjnym przekształcenia pochodzi kolumny według przykładu
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 8b85f57f79cad0a01d3c5499383108450babd507
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833767"
---
# <a name="derive-column-by-example-transformation"></a>Pochodzi kolumny przez przykład przekształcania

**Kolumny pochodzi przykładzie** przekształcenia zezwala użytkownikom na tworzenie pochodną co najmniej jedną kolumnę istniejących przy użyciu przykłady podane przez użytkownika pochodnej wyniku. Pochodnej może być dowolną kombinacją obsługiwany ciąg, datę i numer przekształcenia. 

Następujący ciąg, datę i numer przekształceń są obsługiwane:

**Ciąg przekształcenia:** 

Substring tym inteligentnego wyodrębniania numeru i daty, łączenia, manipulowanie przypadku mapowania wartości stałych.

**Data przekształcenia:** 

Zmień Format daty wyodrębniania części daty, mapowanie czasu do czasu Bins.

Przekształcenia daty są dość ogólny z kilku ważne ograniczenia:
* Timezones nie są obsługiwane.
* Niektóre typowe formatów, które nie są obsługiwane:
    * ISO 8601 tydzień roku w formacie (na przykład "2009-W53-7") 
    * UNIX epoki czasu.
* Wszystkie formaty jest rozróżniana wielkość liter (szczególnie "4: 00" nie został rozpoznany jako czas, mimo że "4: 00").

**Liczba przekształcenia:** 

ROUND, Floor, Ceiling, Binning, dopełnienie z zera lub miejsca, dzielenia i mnożenia siły 1000.

**Composite — przekształcenia:** 

Dowolna kombinacja ciągu, liczby lub daty przekształcenia.

## <a name="how-to-use-this-transformation"></a>Jak używać tego przekształcenia

Do wykonania tej transformacji, wykonaj następujące kroki:
1. Wybierz jedną lub więcej kolumn, które mają pochodzić od wartości. 
2. Wybierz **kolumny pochodzi przykładzie** z **przekształca** menu. Lub kliknij prawym przyciskiem myszy nagłówek dowolnej wybranej kolumny i wybierz **kolumny pochodzi przykładzie** z menu kontekstowego. Otwiera edytor transformacji i dodawane nową kolumnę obok prawo najbardziej zaznaczonej kolumny. Wybranych kolumn mogą zostać zidentyfikowane na podstawie pól wyboru w nagłówkach kolumn. Dodawanie i usuwanie kolumn z zaznaczenia może odbywać się przy użyciu pól wyboru w nagłówkach kolumn.
3. Wpisz na przykład *dane wyjściowe* przed wiersza i naciśnij klawisz wprowadź. W tym momencie Workbench analizuje kolumny wejściowej, a także dostępnych danych wyjściowych do syntetyzowania program, który można przekształcić dane wejściowe do danych wyjściowych. Program syntezatora jest wykonywany przed wszystkie wiersze w siatce danych. W przypadku niejednoznaczne i skomplikowane mogą być wymagane wiele przykładów. W zależności od tego, czy są w trybie podstawowego lub zaawansowanego można podać wiele przykładów w różny sposób.
4. Przejrzyj dane wyjściowe i kliknij przycisk **OK** do akceptowania transformacji.

### <a name="transform-editor-basic-mode"></a>Przekształć edytora: tryb podstawowe

Tryb podstawowe zapewnia wbudowany edytowania w siatce danych. Musisz podać przykładowe dane wyjściowe, przechodząc do komórki zainteresowań i wpisując wartości. 

Workbench analizy danych i próbuje zidentyfikować przypadki krawędzi, które wymagają przejrzenia przez użytkownika. Gdy dane są analizowane, **analizowanie danych** jest wyświetlany w nagłówku Edytor transformacji. Analiza zakończeniu jednego, albo **sugestie nie** , lub **następnego wiersza sugerowane przeglądu** jest wyświetlany w nagłówku. Można nawigować przypadków krawędzi, klikając **następnego wiersza sugerowane przeglądu**. W przypadku, gdy wartość jest niepoprawna dla wiersza, powinien klucza w poprawnej wartości jako dodatkowe przykładowe. 

### <a name="transform-editor-advanced-mode"></a>Przekształć edytora: Tryb zaawansowany

Tryb zaawansowany zawiera rozbudowanej wyprowadzanie kolumn w przykładzie. Wszystkie przykłady są wyświetlane w jednym miejscu. Można także przejrzeć wszystkie przypadki krawędzi w jednym miejscu, klikając **Pokaż sugerowane przykłady**. 

W trybie zaawansowanym można dodać wszystkie wiersze jako przykład przez dwukrotne kliknięcie wierszy w siatce. Jeden wiersz jest kopiowany jako wiersz przykładu, można również edytować dane w kolumnach źródła Aby przykład syntetycznych. W ten sposób można dodać przypadki, które nie są aktualnie dostępne w przykładowych danych.

Użytkownik może przełączać się między **trybie podstawowym** i **trybu zaawansowanego** po kliknięciu łączy w edytorze transformacji.

### <a name="transform-editor-send-feedback"></a>Przekształć edytora: wysyłanie opinii

Kliknięcie **wysłać opinię** połączyć otwartym **opinii** udostępnił okna dialogowego z polem komentarze wstępnie wypełnione użytkownika przykłady. Użytkownik powinien przejrzeć zawartość pola komentarze i zawierają więcej szczegółowych informacji, aby pomóc nam zrozumieć problem. Jeśli użytkownik nie chce udostępniać dane firmy Microsoft, użytkownik powinien usunąć wstępnie przykładowe dane przed kliknięciem przycisku **Prześlij opinię** przycisku. 

### <a name="editing-existing-transformation"></a>Edytowanie istniejącego przekształcania

Użytkownik może edytować istniejące **pochodzi kolumny według przykładu** przekształcenie wybierając **Edytuj** opcji krok transformacji. Kliknięcie **Edytuj** otwiera edytor Przekształć w **trybu zaawansowanego**, i są wyświetlane wszystkie przykłady, które zostały podane podczas tworzenia transformacji.

## <a name="examples-of-string-transformations-by-example"></a>Przykładem ciągu przekształcenia przykładzie


>[!NOTE] 
>Wartości w **bold** stanowią przykłady, które zostały podane w celu prawidłowego wykonania przekształcenia w zestawie danych wyświetlanych.


### <a name="s1-extracting-file-names-from-file-paths"></a>S1. Wyodrębnianie nazwy pliku ze ścieżki plików

Liczba przykłady, które były wymagane dla tego przypadku: 2

|Dane wejściowe|Dane wyjściowe|
|:-----|:-----|
|C:\Python35\Tools\pynche\TypeinViewer.py|**TypeinViewer.py**|
|C:\Python35\Tools\pynche\webcolors.txt|webcolors.txt|
|C:\Python35\Tools\pynche\websafe.txt|websafe.txt|
|C:\Python35\Tools\pynche\X\rgb.txt|rgb.txt|
|C:\Python35\Tools\pynche\X\xlicense.txt|xlicense.txt|
|C:\Python35\Tools\Scripts\2to3.py|2to3.PY|
|C:\Python35\Tools\Scripts\analyze_dxp.py|**analyze_dxp.py**|
|C:\Python35\Tools\Scripts\byext.py|byext.PY|
|C:\Python35\Tools\Scripts\byteyears.py|byteyears.PY|
|C:\Python35\Tools\Scripts\checkappend.PY|checkappend.PY|

### <a name="s2-case-manipulation-during-string-extraction"></a>S2. Wielkość manipulacji podczas wyodrębniania ciągu

Liczba przykłady, które były wymagane dla tego przypadku: 3

|Dane wejściowe|Dane wyjściowe|
|:-----|:-----|
|RENIFERÓW IERZ & ZAKOŃCZONE NIEPOMYŚLNIE;  NOWE HANOWER; Stacja 332; 2015-12-10 @ 17:10:52;|**Nowe Hanower**|
|ŚCIEŻKA BRIAR & WHITEMARSH LN;  HATFIELD TOWNSHIP; Stacja 345; 2015-12-10 @ 17:29:21;|Hatfield Township|
|ZAPISZ HAWS; NORRISTOWN; 2015-12-10 @ 14:39:21-stacji: STA27;|**Norristown**|
|AIRY ST & ST JAREGO;  NORRISTOWN; Stacja 308A; 2015-12-10 @ 16:47:36;|**Norristown**|
|IERZ CHERRYWOOD & ZAKOŃCZONE NIEPOMYŚLNIE;  NIŻSZE POTTSGROVE; Stacja 329; 2015-12-10 @ 16:56:52;|Niższe Pottsgrove|
|ARMATKI ZAPI & ST 9 W;  LANSDALE; Stacja 345; 2015-12-10 @ 15:39:04;|Lansdale|
|Drzewo LAUROWE ZAPI & APISZ OAKDALE;  HORSHAM; Stacja 352; 2015-12-10 @ 16:46:48;|Horsham|
|COLLEGEVILLE usług pulpitu zdalnego i usług pulpitu zdalnego LYWISKI;  SKIPPACK; Stacja 336; 2015-12-10 @ 16:17:05;|Skippack|
|MAIN ST & SZCZUPAK SUMNEYTOWN stary;  NIŻSZE SALFORD; Stacja 344; 2015-12-10 @ 16:51:42;|Niższe Salford|
|BLUEROUTE &AMP; NB I476 NARASTANIA DO CHEMICZNYCH USŁUG PULPITU ZDALNEGO; PLYMOUTH; 2015-12-10 @ 17:35:41;|Plymouth|
|RT202 PKWY &AMP; KNAPP USŁUG PULPITU ZDALNEGO; MONTGOMERY; 2015-12-10 @ 17:33:50;|Montgomery|
|ŹRÓDLANY USŁUG PULPITU ZDALNEGO &AMP; COLWELL LN; PLYMOUTH; 2015-12-10 @ 16:32:10;|Plymouth|

### <a name="s3-date-format-manipulation-during-string-extraction"></a>S3. Format daty manipulacji podczas wyodrębniania ciągu

Liczba przykłady, które były wymagane dla tego przypadku: 1

|Dane wejściowe|Dane wyjściowe|
|:-----|:-----|
|MONTGOMERY ZAPI & WOODSIDE usług pulpitu zdalnego;  NIŻSZE MERION; Stacja 313; 2015-12-11 @ 04:11:35;|**12 listopada 2015 4: 00**|
|DREYCOTT LN z & APISZ LANCASTER W;  NIŻSZE MERION; Stacja 313; 2015-12-11 @ 01:29:52;|12 listopada 2015 1: 00|
|E LEVERING MILL USŁUG PULPITU ZDALNEGO &AMP; CONSHOHOCKEN STANU USŁUG PULPITU ZDALNEGO; NIŻSZE MERION; 2015-12-11 @ 07:29:58;|12 listopada 2015 7: 00|
|PENN DOLINY usług pulpitu zdalnego i usług pulpitu zdalnego MANOR;  NIŻSZE MERION; Stacja 313; 2015-12-10 @ 20:53:30;|12 2015 Oct 20: 00|
|BELMONT ZAPI &AMP; OVERHILL USŁUG PULPITU ZDALNEGO; NIŻSZE MERION; 2015-12-10 @ 23:02:27;|12 2015 Oct 23: 00|
|W MONTGOMERY ZAPI &AMP; PENNSWOOD USŁUG PULPITU ZDALNEGO; NIŻSZE MERION; 2015-12-10 @ 19:25:22;|12 2015 Oct 19: 00|
|ROSEMONT ZAPI & ZAKOŃCZONE NIEPOMYŚLNIE;  NIŻSZE MERION; Stacja 313; 2015-12-10 @ 18:43:07;|12 2015 Oct 18: 00|
|AVIGNON DR & ZAKOŃCZONE NIEPOMYŚLNIE; NIŻSZE MERION; 2015-12-10 @ 20:01:29 — stacji: STA24;|12 2015 Oct 20: 00|

### <a name="s4-concatenating-strings"></a>S4. Łączenie ciągów

Liczba przykłady, które były wymagane dla tego przypadku: 1

>[!NOTE] 
>W tym przykładzie · znaki specjalne reprezentuje spacje w kolumnie wyników.

|Imię|Imienia|Nazwisko|Dane wyjściowe|
|:-----|:-----|:-----|:-----|
|Laquanda||Lohmann|Laquanda··Lohmann|
|Claudio|A|Żucia|**Claudio·A·Chew**|
|Magdalena Anetą|S|Smith|Jane· Anetą S· Smith|
|Brandi||Blumenthal|Brandi·· Blumenthal|
|Jesusita|R|Przebieg|Jesusita·R·Journey|
|Hermina||Hults|Hermina·· Hults|
|Anne-Marie|W|Kowalski|Anne-Marie·W·Jones|
|Rico||Ropp|Rico·· Ropp|
|Może Lauren||Fullmer|Lauren May·· Fullmer|
|Wytłoków|T|Maine|Marc· T· Maine|
|Angie||Adelmana|Angie··Adelman|
|John-Paul||Smith|John-Paul··Smith|
|Utworu|W|Staller|Song·W·Staller|
|Jill||Jefferies|Jill·· Jefferies|
|Ruby-Grace|M|Simmons|Ruby-Grace·M·Simmons|

### <a name="s5-generating-initials"></a>S5. Generowanie skrótów

Liczba przykłady, które były wymagane dla tego przypadku: 2

|Pełna nazwa|Dane wyjściowe|
|:-----|:-----|
|Laquanda Lohmann|**L.L.**|
|Żucia Claudio|C.C.|
|Magdalena Anetą Smith|S.S.|
|Brandi Blumenthal|B.B.|
|Przebieg Jesusita|J.J.|
|Hermina Hults|H.H.|
|Marii Anna Nowak|A.J.|
|Rico Ropp|R.R.|
|Może Lauren Fullmer|L.F.|
|Maine wytłoków|M.M.|
|Angie Adelman|A.A.|
|Pawła Jan Kowalski|**J.S.**|
|Staller utworu|S.S.|
|Jill Jefferies|J.J.|
|Simmons prolongaty Ruby|R.S.|


### <a name="s6-mapping-constant-values"></a>S6. Mapowanie wartości stałych

Liczba przykłady, które były wymagane dla tego przypadku: 3

|Płci administracyjne|Dane wyjściowe|
|:-----|:-----:|
|Mężczyzna|**0**|
|Kobieta|**1**|
|Nieznane|**2**|
|Kobieta|1|
|Kobieta|1|
|Mężczyzna|0|
|Nieznane|2|
|Mężczyzna|0|
|Kobieta|1|

## <a name="examples-of-number-transformations-by-example"></a>Przykłady numer przekształcenia przykładzie

>[!NOTE] 
>Wartości w **bold** stanowią przykłady, które zostały podane w celu prawidłowego wykonania przekształcenia w zestawie danych wyświetlanych.


### <a name="n1-rounding-to-nearest-10"></a>N1. Zaokrąglenie do najbliższej 10

Liczba przykłady, które były wymagane dla tego przypadku: 1

|Dane wejściowe|Dane wyjściowe|
|-----:|-----:|
|112|**110**|
|117|120|
|11112|11110|
|11119|11120|
|548|550|

### <a name="n2-rounding-down-to-nearest-10"></a>N2. Zaokrąglenie w dół do najbliższej 10

Liczba przykłady, które były wymagane dla tego przypadku: 2

|Dane wejściowe|Dane wyjściowe|
|-----:|-----:|
|112|**110**|
|117|**110**|
|11112|11110|
|11119|11110|
|548|540|

### <a name="n3-rounding-to-nearest-005"></a>N3. Zaokrąglenie do najbliższej 0,05

Liczba przykłady, które były wymagane dla tego przypadku: 2

|Dane wejściowe|Dane wyjściowe|
|-----:|-----:|
|-75.5812935|**-75.60**|
|-75.2646799|-75.25|
|-75.3519752|-75.35|
|-75.343513|**-75.35**|
|-75.6033497|-75.60|
|-75.283245|-75.30|

### <a name="n4-binning"></a>N4. Binning

Liczba przykłady, które były wymagane dla tego przypadku: 1

|Dane wejściowe|Dane wyjściowe|
|-----:|:-----:|
|20.16|**20-25**|
|14.32|10-15|
|5.44|5-10|
|3.84|0-5|
|3.73|0-5|
|7.36|5-10|

### <a name="n5-scaling-by-1000"></a>N5. Skalowanie przez 1000

Liczba przykłady, które były wymagane dla tego przypadku: 1

|Dane wejściowe|Dane wyjściowe|
|-----:|-----:|
|-243|**-243000**|
|-12.5|-12500|
|-2345.23292|-2345232.92|
|-1202.3433|-1202343.3|
|1202.3433|1202343.3|

### <a name="n6-padding"></a>N6. Dopełnienie

Liczba przykłady, które były wymagane dla tego przypadku: 1

|Kod|Dane wyjściowe|
|-----:|-----:|
|5828|**05828**|
|44130|44130|
|49007|49007|
|29682|29682|
|4759|04759|
|10029|10029|
|7204|07204|

## <a name="examples-of-date-transformations-by-example"></a>Przykłady przekształcenia Data według przykładu

>[!NOTE] 
>Wartości w **bold** stanowią przykłady, które zostały podane w celu prawidłowego wykonania przekształcenia w zestawie danych wyświetlanych.


### <a name="d1-extracting-date-parts"></a>D1. Wyodrębnianie części dat.

Te części daty zostały wyodrębnione przy użyciu różnych przez przykład przekształcenia na tym samym zestawie danych. Ciągi Bold reprezentuje przykłady, które zostały podane w ich odpowiednich transformacji.

|DateTime|dzień tygodnia|Date|Miesiąc|Rok|Godzina|Minuta|Sekunda|
|-----:|-----:|-----:|-----:|-----:|-----:|-----:|-----:|
|31-Jan-2031 05:54:18|**PT**|**31**|**Jan**|**2031**|**5**|**54**|**18**|
|17-Jan-1990 13:32:01|Śr|17|Sty|1990|13|32|01|
|14 — luty-2034 05:36:07|Wt|14|Lut|2034|5|36|07|
|14-marca-2002 13:16:16|Cz|14|Mar|2002|13|16|16|
|21-Jan-1985 05:44:43|Pn|21|Sty|1985|5|44|**43**|
|16-Aug-1985 01:11:56|Pt|16|Sie|1985|1|11|56|
|20-gru-2033 18:36:29|Wt|20|Gru|2033|18|36|29|
|16-Jul-1984 10:21:59|Pn|16|Lip|1984|10|21|59|
|13 stycznia 2038 r. 10:59:36|Śr|13|Sty|2038|10|59|36|
|14-Aug-1982 15:13:54|So|14|Sie|1982|15|13|54|
|22-Nov-2030 08:18:08|Pt|22|Lis|2030|8|18|08|
|21-Oct-1997 08:42:58|Wt|21|Paź|1997|8|42|58|
|28-Nov-2006 14:19:15|Wt|28|Lis|2006|14|19|15|
|29-kwietnia-2031 04:59:45.|Wt|29|Kwi|2031|4|59|45|
|29-Jan-2032 02:38:36|Cz|29|Sty|2032|2|38|36|
|2028-11-maja 15:31:52|Cz|11|maj|2028|15|31|52|
|15-lip-1977 12:45:39|Pt|15|Lip|1977|12|45|39|
|27-Jan-2029 05:55:41|So|27|Sty|2029|5|55|41|
|2024-03-marca 10:17:49|N|3|Mar|2024|10|17|49|
|00:23:13 kwietnia-14-2010|Śr|14|Kwi|2010|0|23|13|

### <a name="d2-formatting-dates"></a>D2. Formatowanie dat

Te formattings Data zostały wykonane przy użyciu różnych przez przykład przekształcenia na tym samym zestawie danych. Ciągi Bold reprezentuje przykłady, które zostały podane w ich odpowiednich transformacji.

|DateTime|Format1|Format2|Format3|Format4|Format5|
|-----:|-----:|-----:|-----:|-----:|-----:|
|31-Jan-2031 05:54:18|**1/31/2031**|**Piątek, 31 stycznia 2031**|**01312031 5:54**|**31/1/2031 5:54:00**|**Q1 2031**|
|17-Jan-1990 13:32:01|1/17/1990|Środa, 17 stycznia 1990|01171990 13:32|17/1/1990 1:32 PM.|K1 1990|
|14 — luty-2034 05:36:07|2/14/2034|Wtorek, 14 lutego 2034|02142034 5:36|14/2/2034 5:36:00|2034 1.
|14-marca-2002 13:16:16|3/14/2002|Czwartek 14 marca 2002|03142002 13:16|3-14/2002 13:16:00|Q1 2002
|21-Jan-1985 05:44:43|1/21/1985|Poniedziałek 21 stycznia 1985|01211985 5:44|21/1/1985 5:44:00|Q1 1985
|16-Aug-1985 01:11:56|8/16/1985|Piątek, 16 sierpnia 1985|08161985 1:11|16/8/1985 1:11:00|Q3 1985
|20-gru-2033 18:36:29|12/20/2033|Wtorek, 20 grudnia 2033|12202033 18:36|20-12 2033 6:36 PM|2033 KWARTAŁ 4
|16-Jul-1984 10:21:59|7/16/1984|Poniedziałek, 16 lipca 1984|07161984 10:21|1984-16/7 10:21 AM|Q3 1984
|13 stycznia 2038 r. 10:59:36|1/13/2038|Środa, 13 stycznia 2038|01132038 10:59|13/1/2038 10:59 AM|K1 2038
|14-Aug-1982 15:13:54|8/14/1982|Sobota, 14 sierpnia 1982 r.|08141982 15:13|14/8/1982 15:13:00|Q3 1982
|22-Nov-2030 08:18:08|11/22/2030|Piątek, 22 listopada 2030|11222030 8:18|22/11/2030 8:18 AM|2030 KWARTAŁ 4
|21-Oct-1997 08:42:58|10/21/1997|Wtorek, 21 października 1997|10211997 8:42|1997-21-10 8:42 AM|KWARTAŁ 4 1997
|28-Nov-2006 14:19:15|11/28/2006|Wtorek, 28 listopada 2006 roku.|11282006 14:19|11/28/2006 14:19:00|2006 KWARTAŁ 4
|29-kwietnia-2031 04:59:45.|4/29/2031|Wtorek, 29 kwietnia 2031|04292031 4:59|2031-29/4 4:59:00|2031 K2
|29-Jan-2032 02:38:36|1/29/2032|Czwartek, 29 stycznia 2032|01292032 2:38|29/1/2032 2:38 AM|2032 1.
|2028-11-maja 15:31:52|5/11/2028|Czwartek, 11 maja 2028|05112028 15:31|11/5/2028 3:31 PM|2028 K2
|15-lip-1977 12:45:39|7/15/1977|Piątek, 15 lipca 1977|07151977 12:45|15/7/1977 12:45 PM|K3 1977
|27-Jan-2029 05:55:41|1/27/2029|Sobota 27 stycznia 2029|01272029 5:55|27/1/2029 5:55:00|2029 1.
|2024-03-marca 10:17:49|3/3/2024|Niedziela, 3 marca 2024|03032024 10:17|2024-3/3 10:17 AM|2024 1.
|00:23:13 kwietnia-14-2010|4/14/2010|Środa, 14 April 2010|04142010 0:23|2010-14-4 00:23:00|K2 2010


### <a name="d3-mapping-time-to-time-periods"></a>D3. Mapowanie okresach czasu

Te wartości Datetimes okresu mapowań zostały wykonane przy użyciu różnych przez przykład przekształcenia na tym samym zestawie danych. Ciągi Bold reprezentuje przykłady, które zostały podane w ich odpowiednich transformacji.

|DateTime|Period(seconds)|Period(minutes)|Okres (2 godziny)|Okres (30 minut)|
|-----:|-----:|-----:|-----:|-----:|
|31-Jan-2031 05:54:18|**0-20**|**45-60**|**5AM-7AM**|**5:30-6:00**|
|17-Jan-1990 13:32:01|**0-20**|30-45|13: 00 - 15: 00|13:30-14:00|
|14 — luty-2034 05:36:07|0-20|30-45|5AM-7AM|5:30-6:00|
|14-marca-2002 13:16:16|0-20|15-30|13: 00 - 15: 00|13:00-13:30|
|21-Jan-1985 05:44:43|40-60|30-45|5AM-7AM|5:30-6:00|
|16-Aug-1985 01:11:56|40-60|0-15|1AM-3AM|1:00-1:30|
|20-gru-2033 18:36:29|20-40|30-45|5PM-7PM|18:30-19:00|
|16-Jul-1984 10:21:59|40-60|15-30|9AM-11AM|10:00-10:30|
|13 stycznia 2038 r. 10:59:36|20-40|45-60|9AM-11AM|10:30-11:00|
|14-Aug-1982 15:13:54|40-60|0-15|15: 00 - 17: 00|15:00-15:30|
|22-Nov-2030 08:18:08|0-20|15-30|7AM-9AM|8:00-8:30|
|21-Oct-1997 08:42:58|40-60|30-45|7AM-9AM|8:30-9:00|
|28-Nov-2006 14:19:15|0-20|15-30|13: 00 - 15: 00|14:00-14:30|
|29-kwietnia-2031 04:59:45.|40-60|45-60|3AM-5AM|4:30-5:00|
|29-Jan-2032 02:38:36|20-40|30-45|1AM-3AM|2:30-3:00|
|2028-11-maja 15:31:52|40-60|30-45|15: 00 - 17: 00|15:30-16:00|
|15-lip-1977 12:45:39|20-40|45-60|11: 00 - 13: 00|12:30-13:00|
|27-Jan-2029 05:55:41|40-60|45-60|5AM-7AM|5:30-6:00|
|2024-03-marca 10:17:49|40-60|15-30|9AM-11AM|10:00-10:30|
|00:23:13 kwietnia-14-2010|0-20|15-30|11PM-1AM|0:00-0:30|

## <a name="examples-of-composite-transformations-by-example"></a>Przykłady composite — przekształcenia przykładzie

|tripduration|czas rozpoczęcia|Uruchom identyfikator stacji|rozpocząć stacja współrzędnych|rozpocząć stacja geograficzne|UserType|Kolumna|
|-----:|-----:|-----:|-----:|-----:|-----:|-----:|
|61|2016-01-08 16:09:32|107|42.3625|-71.08822|Subskrybent|**Subskrybent pobrane roweru ze stacji 107 lat/long (42.363,-71.088) na 08 stycznia 2016, około 16: 00. Czas trwania podróży została 61 minut**|
|61|2016-01-17 09:28:10|74|42.373268|-71.118579|Klient|Klient pobrane roweru ze stacji 74, lat/long (42.373,-71.119), 17 stycznia 2016, około 9 AM. Czas trwania podróży została 61 minut|
|62|2016-01-25 08:10:26|176|42.386748020450561|-71.119018793106079|Subskrybent|Subskrybent pobrane roweru ze stacji 176 lat/long (42.387,-71.119) na 25 stycznia 2016, około 8 AM. Czas trwania podróży została 62 minut|
|63|2016-01-08 10:10:29|107|42.3625|-71.08822|Subskrybent|Subskrybent pobrane roweru ze stacji 107 lat/long (42.363,-71.088) na 08 stycznia 2016, około 10 AM. Czas trwania podróży została 63 minut|
|64|2016-01-15 19:42:08|68|42.36507|-71.1031|Subskrybent|Subskrybent pobrane roweru ze stacji 68, lat/long (42.365,-71.103) na 15 stycznia 2016, około 19: 00. Czas trwania podróży została 64 minut|
|64|2016-01-22 18:16:13|115|42.387995|-71.119084|Subskrybent|Subskrybent pobrane roweru ze stacji 115 lat/long (42.388,-71.119) na 22 stycznia 2016, około 18: 00. Czas trwania podróży została 64 minut|
|68|2016-01-18 09:51:52|178|42.359573201090441|-71.101294755935669|Subskrybent|Subskrybent pobrane roweru ze stacji 178, lat/long (42.360,-71.101) na 18 stycznia 2016, około 9 AM. Czas trwania podróży została 68 minut|
|69|2016-01-14 08:57:55|176|42.386748020450561|-71.119018793106079|Subskrybent|Subskrybent pobrane roweru ze stacji 176 lat/long (42.387,-71.119) na 14 stycznia 2016, około 8 AM. Czas trwania podróży została 69 minut|
|69|2016-01-13 22:12:55|141|42.363560158429884|-71.08216792345047|Subskrybent|Subskrybent pobrane roweru ze stacji 141, lat/long (42.364,-71.082) na 13 stycznia 2016, około 10 PM. Czas trwania podróży została 69 minut|
|69|2016-01-15 08:13:09|176|42.386748020450561|-71.119018793106079|Subskrybent|Subskrybent pobrane roweru ze stacji 176 lat/long (42.387,-71.119) na 15 stycznia 2016, około 8 AM. Czas trwania podróży została 69 minut|


## <a name="technical-notes"></a>Uwagi techniczne

### <a name="conditional-transformations"></a>Warunkowe przekształcenia
W niektórych przypadkach pojedyncze przekształcenie nie można znaleźć odpowiadającej danej przykłady. W takich przypadkach pochodzi kolumny przez przekształcenie przykład próbuje grupy wzorca niektóre z danych wejściowych i Dowiedz się oddzielne transformacji dla każdej grupy. Nazywamy to **warunkowego przekształcania**. **Transformacja warunkowego** zostanie podjęta tylko przekształcenia z jedną kolumną wejściowego. 

### <a name="reference"></a>Informacje ogólne
Więcej informacji na temat przekształcania ciągu przez technologię przykład znajduje się w [tej publikacji](https://www.microsoft.com/research/publication/automating-string-processing-spreadsheets-using-input-output-examples/).
