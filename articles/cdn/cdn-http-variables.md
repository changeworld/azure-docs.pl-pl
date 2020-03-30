---
title: Zmienne HTTP dla aparatu reguł usługi Azure CDN | Dokumenty firmy Microsoft
description: Zmienne HTTP umożliwiają pobieranie metadanych żądań i odpowiedzi HTTP.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus
ms.openlocfilehash: 53ad0c516547e17801bd57c2fd6b0d1704383797
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593814"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>Zmienne HTTP dla aparatu reguł usługi Azure CDN
Zmienne HTTP zapewniają środki, za pomocą których można pobrać metadane żądania i odpowiedzi HTTP. Te metadane mogą być następnie używane do dynamicznej zmiany żądania lub odpowiedzi. Użycie zmiennych HTTP jest ograniczone do następujących funkcji aparatu reguł:

- [Przepisanie klucza pamięci podręcznej](cdn-verizon-premium-rules-engine-reference-features.md#cache-key-rewrite)
- [Modyfikowanie nagłówka żądania klienta](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-request-header)
- [Modyfikowanie nagłówka odpowiedzi klienta](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-response-header)
- [Przekierowanie adresu URL](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect)
- [Ponowne zapisywanie adresów URL](cdn-verizon-premium-rules-engine-reference-features.md#url-rewrite)

## <a name="definitions"></a>Definicje
W poniższej tabeli opisano obsługiwane zmienne HTTP. Pusta wartość jest zwracana, gdy metadane GEO (na przykład kod pocztowy) są niedostępne dla określonego żądania.


| Nazwa | Zmienna | Opis | Wartość przykładowa |
| ---- | -------- | ----------- | ------------ |
| ASN (wzywający) | %{geo_asnum} | Wskazuje numer AS żądający. <br /><br />**Przestarzałe:** %{virt_dst_asnum}. <br />Ta zmienna została przestarzała na korzyść %{geo_asnum}. Mimo że reguła, która używa tej przestarzałej zmiennej będzie nadal działać, należy zaktualizować ją, aby użyć nowej zmiennej. | AS15133 |
| Miasto (Wzywający) | %{geo_city} | Wskazuje miasto wzywającego. | Los Angeles |
| Kontynent (Wzywający) | %{geo_continent} | Wskazuje kontynent wnioskujący poprzez jego skrót. <br />Prawidłowe wartości: <br />AF: Afryka<br />AS: Azja<br />UE: Europa<br />NA: Ameryka Północna<br />OC: Oceania<br />SA: Ameryka Południowa<br /><br />**Przestarzałe:** %{virt_dst_continent}. <br />Ta zmienna została przestarzała na korzyść %{geo_continent}. <br />Mimo że reguła, która używa tej przestarzałej zmiennej będzie nadal działać, należy zaktualizować ją, aby użyć nowej zmiennej.| Nie dotyczy |
| Wartość pliku cookie | %{cookie_Cookie} | Zwraca wartość odpowiadającą kluczowi pliku cookie identyfikowanemu przez termin Cookie. | Przykładowe użycie: <br />%{cookie__utma}<br /><br />Wartość próbki:<br />111662281.2.10.1222100123 |
| Kraj (wzywający) | %{geo_country} | Wskazuje kraj pochodzenia wnioskujący za pomocą kodu kraju. <br />**Przestarzałe:** %{virt_dst_country}. <br /><br />Ta zmienna została przestarzała na korzyść %{geo_country}. Mimo że reguła, która używa tej przestarzałej zmiennej będzie nadal działać, należy zaktualizować ją, aby użyć nowej zmiennej. | USA |
| Wyznaczony obszar rynku (wzywający) | %{geo_dma_code} |Wskazuje rynek nośników żądający według jego kodu regionu. <br /><br />To pole ma zastosowanie tylko do żądań pochodzących ze Stanów Zjednoczonych.| 745 |
| Metoda żądania HTTP | %{request_method} | Wskazuje metodę żądania HTTP. | GET |
| Kod stanu HTTP | %{stan} | Wskazuje kod stanu HTTP dla odpowiedzi. | 200 |
| Adres IP (żądający) | %{virt_dst_addr} | Wskazuje adres IP żądający. | 192.168.1.1 |
| Szerokość geograficzna (wzywający) | %{geo_latitude} | Wskazuje szerokość geograficzną żądający. | 34.0995 |
| Długość geograficzna (Wzywający) | %{geo_longitude} | Wskazuje długość geograficzną żądacza. | -118.4143 |
| Metropolitalny Obszar Statystyczny (Wzywający) | %{geo_metro_code} | Wskazuje obszar metropolitalny wnioskujący. <br /><br />To pole ma zastosowanie tylko do żądań pochodzących ze Stanów Zjednoczonych.<br />| 745 |
| Port (wzywający) | %{virt_dst_port} | Wskazuje tymczasowy port żądający. | 55885 |
| Kod pocztowy (wzywający) | %{geo_postal_code} | Wskazuje kod pocztowy żądający. | 90210 |
| Znaleziono ciąg zapytania | %{is_args} | Wartość dla tej zmiennej różni się w zależności od tego, czy żądanie zawiera ciąg zapytania.<br /><br />- Znaleziono ciąg zapytania: ?<br />- Brak ciągu zapytania: NULL | ? |
| Znaleziono parametr ciągu kwerendy | %{is_amp} | Wartość dla tej zmiennej różni się w zależności od tego, czy żądanie zawiera co najmniej jeden parametr ciągu zapytania.<br /><br />- Znaleziono parametr: &<br />- Brak parametrów: NULL | & |
| Wartość parametru ciągu kwerendy | %{arg_&lt;parametr&gt;} | Zwraca wartość odpowiadającą parametrowi ciągu zapytania &lt;&gt; identyfikowanym przez termin parametru. | Przykładowe użycie: <br />%{arg_language}<br /><br />Przykładowy parametr ciągu kwerendy: <br />?language=pl<br /><br />Wartość próbki: en |
| Wartość ciągu zapytania | %{query_string} | Wskazuje całą wartość ciągu zapytania zdefiniowaną w adresie URL żądania. |key1=val1&key2=val2&key3=val3 |
| Domena odsyłacza | %{referring_domain} | Wskazuje domenę zdefiniowaną w nagłówku żądania odsyłacza. | <www.google.com> |
| Region (wzywający) | %{geo_region} | Wskazuje region żądający (na przykład stan lub prowincję) za pomocą jego skrótu alfanumerycznego. | CA |
| Wartość nagłówka żądania | %{http_RequestHeader} | Zwraca wartość odpowiadającą nagłówkowi żądania identyfikowanemu przez termin RequestHeader. <br /><br />Jeśli nazwa nagłówka żądania zawiera myślnik (na przykład User-Agent), zastąp go podkreśleniem (na przykład User_Agent).| Przykładowe użycie: %{http_Connection}<br /><br />Przykładowa wartość: Keep-Alive | 
| Zażądaj hosta | %{host} | Wskazuje hosta zdefiniowanego w adresie URL żądania. | <www.mydomain.com> |
| Protokół żądania | %{request_protocol} | Wskazuje protokół żądania. | Protokół HTTP/1.1 |
| Schemat żądania | %{schemat} | Wskazuje schemat żądania. |http |
| Żądanie identyfikatora URI (względnego) | %{request_uri} | Wskazuje ścieżkę względną, w tym ciąg zapytania, zdefiniowaną w identyfikatorze URI żądania. | /marketing/foo.js?loggedin=true |
| Żądanie identyfikatora URI (względny bez ciągu zapytania) | %{uri} | Wskazuje względną ścieżkę do żądanej zawartości. <br /><br/>Najważniejsze informacje:<br />- Ta ścieżka względna wyklucza ciąg zapytania.<br />- Ta ścieżka względna odzwierciedla przepisywanie adresów URL. Adres URL zostanie przepisany pod następującymi warunkami:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Funkcja przepisywania adresów URL: Ta funkcja przepisuje względną ścieżkę zdefiniowaną w identyfikatorze URI żądania.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Adres URL CNAME krawędzi: Ten typ żądania jest przepisywany do odpowiedniego adresu URL sieci CDN. |/800001/corigin/rewrittendir/foo.js |
| Identyfikator URI żądania | %{żądanie} | Opisuje żądanie. <br />Składnia: &lt;&gt; &lt;metoda HTTP&gt; &lt;względna ścieżka protokołu HTTP&gt; | GET /marketing/foo.js?loggedin=prawdziwy HTTP/1.1 |
| Wartość nagłówka odpowiedzi | %{resp_&lt;Odpowiedzigłówek&gt;} | Zwraca wartość odpowiadającą nagłówkowi odpowiedzi &lt;identyfikowanemu przez termin ResponseHeader.&gt; <br /><br />Jeśli nazwa nagłówka odpowiedzi zawiera myślnik (na przykład User-Agent), zastąp go podkreśleniem (na przykład User_Agent). | Przykładowe użycie: %{resp_Content_Length}<br /><br />Wartość próbki: 100 |

## <a name="usage"></a>Sposób użycia
W poniższej tabeli opisano właściwą składnię określania zmiennej HTTP.


| Składnia | Przykład | Opis |
| ------ | -------- | ---------- |
| %{&lt;HttpVariable&gt;} | %{host} | Użyj tej składni, aby uzyskać całą &lt;wartość odpowiadającą&gt;określonemu httpvariable . |
| %{&lt;HttpVariableDelimiter&gt;} | %{host,} | Ta składnia służy do ustawiania przypadku dla całej &lt;wartości odpowiadającej określonemu&gt;identyfikatorowi HTTPVariableDelimiter . |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{host/=^www\.([^\.]+)\.\.([^ :]+)/cdn.$2.$3:80} | Użyj wyrażenia regularnego &lt;dla HTTPVariableDelimiterExpression,&gt; aby zastąpić, usunąć lub manipulować wartością zmiennej HTTP. |

Nazwy zmiennych HTTP obsługują tylko znaki alfabetyczne i podkreślenia. Konwertuj nieobsługiwalne znaki na znaki podkreślenia.

## <a name="delimiter-reference"></a>Odwołanie do ogranicznika
Ogranicznik można określić po zmiennej HTTP, aby osiągnąć dowolny z następujących efektów:

- Przekształć wartość skojarzoną ze zmienną.

     Przykład: Przekonwertować całą wartość na małe litery.

- Usuń wartość skojarzoną ze zmienną.

- Manipulować wartością skojarzoną ze zmienną.

     Przykład: Użyj wyrażeń regularnych, aby zmienić wartość skojarzoną ze zmienną HTTP.

Ograniczniki są opisane w poniższej tabeli.

| Ogranicznik | Opis |
| --------- | ----------- |
| := | Wskazuje, że do zmiennej zostanie przypisana wartość domyślna, gdy jest ona przypisana: <br />- Brak <br />- Ustaw wartość NULL. |
| :+ | Wskazuje, że do zmiennej zostanie przypisana wartość domyślna, gdy została do niej przypisana wartość. |
| : | Wskazuje, że podciąg wartości przypisanej do zmiennej zostanie rozwinięty. |
| # | Wskazuje, że wzorzec określony po tym ograniczniku powinny zostać usunięte, gdy znajduje się na początku wartości skojarzonej ze zmienną. |
| % | Wskazuje, że wzorzec określony po tym ograniczniku powinny zostać usunięte, gdy znajduje się na końcu wartości skojarzonej ze zmienną. <br />Ta definicja ma zastosowanie tylko wtedy, gdy symbol % jest używany jako ogranicznik. |
| / | Rozgranicza zmienną HTTP lub wzorzec. |
| // | Znajdź i zastąp wszystkie wystąpienia określonego wzorca. |
| /= | Znajdź, skopiuj i przepisz wszystkie wystąpienia określonego wzorca. |
| , | Przekonwertuj wartość skojarzoną ze zmienną HTTP na małe litery. |
| ^ | Przekonwertuj wartość skojarzoną ze zmienną HTTP na wielkie litery. |
| ,, | Przekonwertuj wszystkie wystąpienia określonego znaku w wartości skojarzonej ze zmienną HTTP na małe litery. |
| ^^ | Przekonwertuj wszystkie wystąpienia określonego znaku w wartości skojarzonej ze zmienną HTTP na wielkie litery. |

## <a name="exceptions"></a>Wyjątki
W poniższej tabeli opisano okoliczności, w których określony tekst nie jest traktowany jako zmienna HTTP.

| Warunek | Opis | Przykład |
| --------- | ----------- | --------|
| Symbol ucieczki % | Symbol procentowy można wysunąć za pomocą ukośnika odwrotnego. <br />Przykładowa wartość po prawej stronie będzie traktowana jako wartość literału, a nie jako zmienna HTTP.| \%{host} |
| Nieznane zmienne | Pusty ciąg jest zawsze zwracany dla nieznanych zmiennych. | %{unknown_variable} |
| Nieprawidłowe znaki lub składnia | Zmienne zawierające nieprawidłowe znaki lub składnię są traktowane jako wartości literału. <br /><br />Przykład #1: Określona wartość zawiera nieprawidłowy znak (na przykład -). <br /><br />Przykład #2: Określona wartość zawiera podwójny zestaw nawiasów klamrowych. <br /><br />Przykład #3: W określonej wartości brakuje zamykającego nawiasu klamrowego.<br /> | Przykład #1: %{resp_user-agent} <br /><br />Przykład #2: %{{host}} <br /><br />Przykład #3: %{host |
| Brak nazwy zmiennej | Wartość NULL jest zawsze zwracana, gdy zmienna nie jest określona. | %{} |
| Znaki końcowe | Znaki, które szlak zmiennej są traktowane jako wartości literału. <br />Wartość próbki po prawej stronie zawiera końcowe nawiasy klamrowe, które będą traktowane jako wartość literału. | %{host}} |

## <a name="setting-default-header-values"></a>Ustawianie domyślnych wartości nagłówka
Wartość domyślną można przypisać do nagłówka, gdy spełnia którykolwiek z następujących warunków:
- Brak/unset
- Ustaw wartość NULL.

W poniższej tabeli opisano sposób definiowania wartości domyślnej.

| Warunek | Składnia | Przykład | Opis |
| --------- | ------ | --------| ----------- |
| Ustaw nagłówek na wartość domyślną, gdy spełnia którykolwiek z następujących warunków: <br /><br />- Brak nagłówka <br /><br />- Wartość nagłówka jest ustawiona na WARTOŚĆ NULL.| %{Zmienna:=wartość} | %{http_referrer:=nieokreślony} | Nagłówek odsyłacza zostanie ustawiony na nieokreślony tylko *wtedy,* gdy brakuje go lub zostanie ustawiony na wartość NULL. Żadne działanie nie będzie miało miejsca, jeśli zostało ustawione. |
| Ustaw nagłówek na wartość domyślną, gdy jej brakuje. | %{Zmienna=wartość} | %{http_referrer=nieokreślony} | Nagłówek odsyłający zostanie ustawiony na nieokreślony tylko *wtedy,* gdy go nie ma. Żadne działanie nie będzie miało miejsca, jeśli zostało ustawione. |
| Ustaw nagłówek na wartość domyślną, gdy nie spełnia żadnego z następujących warunków: <br /><br />- Brak<br /><br /> - Ustaw wartość NULL. | %{Zmienna:+Wartość} | %{http_referrer:+nieokreślony} | Nagłówek odsyłający zostanie ustawiony na *nieokreślony* tylko wtedy, gdy wartość została do niego przypisana. Żadna akcja nie będzie się odbywać, jeśli brakuje lub jest ustawiona na NULL. |

## <a name="manipulating-variables"></a>Manipulowanie zmiennymi
Zmiennymi można manipulować w następujący sposób:

- Rozszerzanie podciągów
- Usuwanie wzorców

### <a name="substring-expansion"></a>Rozszerzenie podciągów
Domyślnie zmienna rozwinie się do pełnej wartości. Użyj następującej składni, aby rozwinąć tylko podciąg wartości zmiennej.

`%<Variable>:<Offset>:<Length>}`

Najważniejsze informacje:

- Wartość przypisana do terminu Przesunięcie określa znak początkowy podciągu:

     - Dodatni: Początkowy znak podciągów jest obliczany od pierwszego znaku w ciągu.
     - Zero: Początkowy znak podciągów jest pierwszym znakiem w ciągu.
     - Ujemny: Początkowy znak podciągów jest obliczany na podstawie ostatniego znaku w ciągu.

- Długość podciągów jest określana przez okres *Długość:*

     - Pominięto: Pominięcie okresu Długość umożliwia podciągowi dołączyć wszystkie znaki między znakiem początkowym a końcem ciągu.
     - Dodatnie: Określa długość podciągu od znaku początkowego do prawej.
     - Negatyw: Określa długość podciągu od znaku początkowego do lewej.

#### <a name="example"></a>Przykład:

Poniższy przykład opiera się na następującym przykładowym adresie URL żądania:

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

Poniższy ciąg demonstruje różne metody manipulowania zmiennymi:

https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

Na podstawie przykładowego adresu URL żądania powyższa manipulacja zmiennymi spowoduje uzyskanie następującej wartości:

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### <a name="pattern-removal"></a>Usuwanie wzoru
Tekst, który pasuje do określonego wzorca można usunąć z początku lub na końcu wartości zmiennej.

| Składnia | Akcja |
| ------ | ------ |
| %{Zmienna#wzorzec} | Usuń tekst, gdy określony wzorzec zostanie znaleziony na początku wartości zmiennej. |
| %{Zmienny}Wzorzec} | Usuń tekst, gdy określony wzorzec zostanie znaleziony na końcu wartości zmiennej. |

#### <a name="example"></a>Przykład:

W tym przykładowym scenariuszu zmienna *request_uri* jest ustawiona na:

`/800001/myorigin/marketing/product.html?language=en-US`

W poniższej tabeli pokazano, jak działa ta składnia.

| Składnia przykładu | Wyniki | |
| ------------- | ------- | --- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html?language=en-US | Ponieważ zmienna rozpoczyna się od wzorca, została zastąpiona. |
| %{request_uri%html}htm | /800001/myorigin/marketing/product.html?language=en-US | Ponieważ zmienna nie kończy się na wzorcu, nie nastąpiła żadna zmiana.|

### <a name="find-and-replace"></a>Znajdowanie i zamienianie
Składnia znajdź i zamień jest opisana w poniższej tabeli.

| Składnia | Akcja |
| ------ | ------ |
| %{Zmienna/Znajdź/Zamień} | Znajdź i zastąp pierwsze wystąpienie określonego wzorca. |
| %{Zmienna//Znajdź/Zamień} | Znajdź i zastąp wszystkie wystąpienia określonego wzorca. |
| %{Zmienna^} |Przekonwertuj całą wartość na wielkie litery. |
| %{Zmienna^Znajdź} | Przekonwertować pierwsze wystąpienie określonego wzorca na wielkie litery. |
| %{Zmienna,} | Przekonwertować całą wartość na małe litery. |
| %{Zmienna,Znajdź} | Przekonwertować pierwsze wystąpienie określonego wzorca na małe litery. |

### <a name="find-and-rewrite"></a>Znajdowanie i przepisywanie
Aby uzyskać wariację na temat znajdowania i zamieniania, użyj tekstu, który pasuje do określonego wzorca podczas przepisywania go. Składnia znajdowania i przepisywania jest opisana w poniższej tabeli.

| Składnia | Akcja |
| ------ | ------ |
| %{Zmienna/=Znajdź/Przepisz} | Znajdź, skopiuj i przepisz wszystkie wystąpienia określonego wzorca. |
| %{Zmienna/^Znajdź/Przepisz} | Znajdź, skopiuj i przepisz określony wzorzec, gdy wystąpi na początku zmiennej. |
| %{Zmienna/$Find/Przepisanie} | Znajdź, skopiuj i przepisz określony wzorzec, gdy występuje na końcu zmiennej. |
| %{Zmienna/Znajdź} | Znajdź i usuń wszystkie wystąpienia określonego wzorca. |

Najważniejsze informacje:

- Rozwiń tekst, który pasuje do określonego wzorca, określając znak dolara, po którym następuje cała liczba całkowita (na przykład $1).

- Można określić wiele wzorców. Kolejność, w której wzorzec jest określony określa całkowitą, która zostanie przypisana do niego. W poniższym przykładzie pierwszy wzorzec pasuje do "www.", drugi wzorzec pasuje do domeny drugiego poziomu, a trzeci do domeny najwyższego poziomu:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- Wartość przepisana może składać się z dowolnej kombinacji tekstu i tych symboli zastępczych.

    W poprzednim przykładzie nazwa hosta jest `cdn.$2.$3:80` przepisana do (na przykład cdn.mydomain.com:80).

- Przypadek symbolu zastępczego wzorca (na przykład $1) można zmodyfikować za pomocą następujących flag:
     - U: Wielkie litery wartości rozwiniętej.

         Składnia przykładu:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: Małe litery wartości rozwiniętej.

         Składnia przykładu:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- Operator musi być określony przed wzorcem. Określony operator określa zachowanie przechwytywania wzorców:

     - `=`: Wskazuje, że wszystkie wystąpienia określonego wzorca muszą zostać przechwycone i przepisane.
     - `^`: Wskazuje, że tylko tekst rozpoczynający się od określonego wzorca zostanie przechwycony.
     - `$`: Wskazuje, że przechwytywanie będzie tylko tekstem zakończonym określonym wzorcem.
 
- Jeśli pominięto */Rewrite* wartość, tekst, który pasuje do wzorca jest usuwany.


