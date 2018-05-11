---
title: Zmienne HTTP dla usługi Azure CDN aparatu reguł | Dokumentacja firmy Microsoft
description: Zmienne HTTP umożliwiają pobierania metadanych żądań i odpowiedzi HTTP.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: v-deasim
ms.openlocfilehash: 36c1b20219fabd1b7c02247d9a93bb7b7cfc898d
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>Zmienne HTTP dla aparatu reguł Azure CDN
Zmienne HTTP pozwalają, za pomocą którego można pobrać metadanych żądań i odpowiedzi HTTP. Następnie można użyć tego metadanych dynamicznie zmieniać żądania lub odpowiedzi. Użycie zmiennych HTTP jest ograniczone do następujących funkcji aparatu reguł:

- [Napisz ponownie klucz pamięci podręcznej](cdn-rules-engine-reference-features.md#cache-key-rewrite)
- [Modyfikowanie nagłówek żądania klienta](cdn-rules-engine-reference-features.md#modify-client-request-header)
- [Modyfikowanie nagłówka odpowiedzi klienta](cdn-rules-engine-reference-features.md#modify-client-response-header)
- [Adres URL przekierowania](cdn-rules-engine-reference-features.md#url-redirect)
- [Ponowne zapisywanie adresów URL](cdn-rules-engine-reference-features.md#url-rewrite)

## <a name="definitions"></a>Definicje
W poniższej tabeli opisano obsługiwane zmienne HTTP. Po geograficznie metadanych (na przykład, kod pocztowy) jest niedostępna dla określonego żądania, zwracana jest wartość pusta.


| Name (Nazwa) | Zmienna | Opis | Wartość przykładowa |
| ---- | -------- | ----------- | ------------ |
| Numer ASN (żądającego) | % {geo_asnum} | Wskazuje żądającego jako numer. <br /><br />**Przestarzałe:** % {virt_dst_asnum}. <br />Ta zmienna została zastąpiona % {geo_asnum}. Mimo że regułę, która używa tej zmiennej przestarzałe będą nadal działać, należy zaktualizować go do użycia nowej zmiennej. | AS15133 |
| Miasto (żądającego) | % {geo_city} | Wskazuje Miasto żądającego. | Los Angeles |
| Kontynencie (żądającego) | % {geo_continent} | Wskazuje kontynencie żądającego za pośrednictwem jego skrót. <br />Prawidłowe wartości to: <br />AF: Afryka<br />AS: Azja<br />Europa: Europa<br />NA: Ameryka Północna<br />° C: Oceanii<br />SA: Ameryka Południowa<br /><br />**Przestarzałe:** % {virt_dst_continent}. <ber />Ta zmienna została zastąpiona % {geo_continent}. <br />Mimo że regułę, która używa tej zmiennej przestarzałe będą nadal działać, należy zaktualizować go do użycia nowej zmiennej.| ND |
| Wartość pliku cookie | % {cookie_Cookie} | Zwraca wartość odpowiadającą klucz pliku cookie, identyfikowany przez określenie pliku Cookie. | Przykładowe zastosowanie: <br />% {cookie__utma}<br /><br />Przykładowa wartość:<br />111662281.2.10.1222100123 |
| Country (żądającego) | % {geo_country} | Wskazuje kraju żądającego pochodzenia za pośrednictwem jego numer kierunkowy kraju. <br />**Przestarzałe:** % {virt_dst_country}. <br /><br />Ta zmienna została zastąpiona % {geo_country}. Mimo że regułę, która używa tej zmiennej przestarzałe będą nadal działać, należy zaktualizować go do użycia nowej zmiennej. | USA |
| Obszar wyznaczonych rynku (żądającego) | % {geo_dma_code} |Wskazuje żądającego nośnika rynku na podstawie kodu regionu. <br /><br />To pole ma zastosowanie tylko do żądań, które pochodzą ze Stanów Zjednoczonych.| 745 |
| Metoda żądania HTTP | % {request_method} | Wskazuje, Metoda żądania HTTP. | GET |
| Kod stanu HTTP | % {status} | Wskazuje kod stanu HTTP dla odpowiedzi. | 200 |
| Adres IP (żądającego) | % {virt_dst_addr} | Wskazuje adres IP żądającego. | 192.168.1.1 |
| Współrzędne (żądającego) | % {geo_latitude} | Określa szerokość żądającego. | 34.0995 |
| Długość (żądającego) | % {geo_longitude} | Wskazuje geograficzne żądającego. | -118.4143 |
| Statystyczne obszaru metropolitalnego (żądającego) | % {geo_metro_code} | Wskazuje, obszaru metropolitalnego żądającego. <br /><br />To pole ma zastosowanie tylko do żądań, które pochodzą ze Stanów Zjednoczonych.<br />| 745 |
| Port (żądającego) | % {virt_dst_port} | Wskazuje portów efemerycznych żądającego. | 55885 |
| Kod pocztowy (żądającego) | % {geo_postal_code} | Wskazuje kod pocztowy żądającego. | 90210 |
| Znaleziono ciąg zapytania | % {is_args} | Wartość tej zmiennej, zależy od tego, czy żądanie zawiera ciąg zapytania.<br /><br />-Znaleziono ciąg kwerendy:?<br />-Brak ciągu zapytania: wartość NULL | ? |
| Znaleziono parametr ciągu zapytania | % {is_amp} | Wartość tej zmiennej, zależy od tego, czy żądanie zawiera co najmniej jeden parametr ciągu zapytania.<br /><br />-Znaleziono parametru: &<br />-Brak parametrów: wartość NULL | & |
| Wartość parametru ciągu zapytania | % {arg_&lt;parametru&gt;} | Zwraca wartość odpowiadającą identyfikowane przez parametr ciągu zapytania &lt;parametru&gt; terminu. | Przykładowe zastosowanie: <br />% {arg_language}<br /><br />Przykładowe parametru ciągu zapytania: <br />? języka = en<br /><br />Przykładowe wartości: en |
| Wartość ciągu kwerendy | % {query_string} | Wskazuje wartość ciągu kwerendy całego zdefiniowane w adresie URL żądania. |klucz1 = val1 & klucz2 = Wart2 & klucz3 = val3 |
| Domeny odwołania | % {referring_domain} | Określa domenę zdefiniowane w nagłówku żądania Odnośnik. | www.google.com |
| Region (żądającego) | % {geo_region} | Wskazuje region żądającego (na przykład województwo) za pośrednictwem jego skrót alfanumeryczne. | Urząd certyfikacji |
| Wartość nagłówka żądania | % {http_RequestHeader} | Zwraca wartość odpowiadającą identyfikowane przez określenie RequestHeader nagłówek żądania. <br /><br />Jeśli nazwa nagłówka żądania zawiera, łącznik (na przykład agenta użytkownika), należy ją zastąpić znaku podkreślenia (na przykład User_Agent).| Przykładowe zastosowanie: % {http_Connection}<br /><br />Przykładowa wartość: Keep-Alive | 
| Host żądania | % {hosta} | Wskazuje hosta zdefiniowane w adresie URL żądania. | www.myDomain.com |
| Żądanie protokołu | % {request_protocol} | Wskazuje żądanie protokołu. | HTTP/1.1 |
| Schemat żądania | % {Schemat} | Wskazuje schemat żądania. |http |
| Identyfikator URI (względem) żądania | % {request_uri} | Określa ścieżkę względną, łącznie z ciągu zapytania, zdefiniowane w identyfikatorze URI żądania. | /Marketing/foo.js?loggedin=true |
| Identyfikator URI (względem bez ciągu zapytania) żądania | {Identyfikator uri} % | Określa ścieżkę względną do żądanej zawartości. <br /><br/>Informacje o kluczu:<br />-Ta ścieżka względna nie obejmuje ciągu zapytania.<br />-Ta ścieżka względna odzwierciedla ponownego adresu URL. Adres URL będzie ulegną w następujących warunkach:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-Funkcja ponowne zapisywanie adresów URL: Ta funkcja ponownie zapisuje ścieżki względnej zdefiniowane w identyfikatorze URI żądania.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-Edge CNAME adres URL: Ten typ żądania jest napisany od nowa do odpowiedni adres URL usługi CDN. |/800001/corigin/rewrittendir/foo.js |
| Identyfikator URI żądania | {żądanie} % | W tym artykule opisano żądania. <br />Składnia: &lt;metoda HTTP&gt; &lt;ścieżki względnej&gt; &lt;protokołu HTTP&gt; | Pobierz /marketing/foo.js?loggedin=true HTTP/1.1 |
| Wartość nagłówka odpowiedzi | % {resp_&lt;ResponseHeader&gt;} | Zwraca wartość odpowiadającą nagłówka odpowiedzi identyfikowane przez &lt;ResponseHeader&gt; terminu. <br /><br />Jeśli nazwa nagłówka odpowiedzi zawiera, łącznik (na przykład agenta użytkownika), należy ją zastąpić znaku podkreślenia (na przykład User_Agent). | Przykładowe zastosowanie: % {resp_Content_Length}<br /><br />Przykładowe wartości: 100 |

## <a name="usage"></a>Sposób użycia
W poniższej tabeli opisano dokładny opis składni służącą do zmiennej HTTP.


| Składnia | Przykład | Opis |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | % {hosta} | Użyj następującej składni, aby uzyskać całą wartość odpowiadający określonym &lt;HTTPVariable&gt;. |
| %{&lt;HTTPVariableDelimiter&gt;} | % {hosta} | Użyj następującej składni, aby ustawić w przypadku całą wartość odpowiadający określonym &lt;HTTPVariableDelimiter&gt;. |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{Host/=^www\.([^\.] +)\.([^\.:] +) /cdn.$2.$3:80} | Użycie wyrażenia regularnego dla &lt;HTTPVariableDelimiterExpression&gt; Aby zastąpić, usunąć lub przetwarzać wartość zmiennej HTTP. |

Nazwy zmiennych HTTP obsługują tylko znaki alfabetyczne i znaki podkreślenia. Przekonwertować nieobsługiwane znaki podkreślenia.

## <a name="delimiter-reference"></a>Ogranicznik odwołania
Ogranicznik można podać po Zmienna HTTP, aby osiągnąć cele następujące skutki:

- Przekształć wartości skojarzone ze zmienną.

     Przykład: Konwertowanie całą wartość na małe litery.

- Usuń wartość skojarzoną ze zmienną.

- Manipulowanie wartość skojarzoną ze zmienną.

     Przykład: Używanie wyrażeń regularnych do zmiany wartości skojarzone ze zmienną HTTP.

Ograniczniki są opisane w poniższej tabeli.

| Ogranicznik | Opis |
| --------- | ----------- |
| := | Wskazuje, że wartości domyślnej zostanie przypisany do zmiennej podczas jest ona: <br />-Brak <br />-Ustawiana wartość NULL. |
| :+ | Wskazuje, że wartości domyślnej zostanie przypisany do zmiennej podczas wartość przypisano do niej. |
| : | Wskazuje, będą rozszerzane podciąg wartość przypisaną do zmiennej. |
| # | Wskazuje, że wzorzec określona po tym ogranicznikiem powinien zostać usunięty po jego odnalezieniu na początku wartości skojarzone ze zmienną. |
| % | Wskazuje, że wzorzec określona po tym ogranicznikiem powinien zostać usunięty po jego odnalezieniu na końcu wartość skojarzoną ze zmienną. <br />Ta definicja ma zastosowanie tylko w przypadku, gdy % symbol jest używany jako ogranicznik. |
| / | Rozgranicza zmiennej HTTP lub wzorzec. |
| // | Znajdź i Zamień wszystkie wystąpienia określonego wzorca. |
| /= | Znajdź, kopiowanie i Zapisz ponownie wszystkie wystąpienia określonego wzorca. |
| , | Konwertowanie wartości skojarzone ze zmienną HTTP na małe litery. |
| ^ | Konwertowanie wartości skojarzone ze zmienną HTTP na wielkie litery. |
| ,, | Przekonwertuj wszystkie wystąpienia określony znak w wartości skojarzone ze zmienną HTTP na małe litery. |
| ^^ | Przekonwertuj wszystkie wystąpienia określony znak w wartości skojarzone ze zmienną HTTP na wielkie litery. |

## <a name="exceptions"></a>Wyjątki
W poniższej tabeli opisano okoliczności, w których określony tekst nie jest traktowane jako zmienną HTTP.

| Warunek | Opis | Przykład |
| --------- | ----------- | --------|
| Anulowanie % symbol | Symbol procentu można wyjściowym przy użyciu ukośnik odwrotny. <br />Przykładowa wartość po prawej stronie będą traktowane jako wartość literału, a nie do zmiennej HTTP.| \%{hosta} |
| Nieznany zmiennych | Ciąg pusty jest zawsze zwrócił nieznany zmiennych. | % {unknownvariable} |
| Nieprawidłowe znaki lub składni | Zmienne, które zawierają nieprawidłowe znaki lub składni są traktowane jako wartości literału. <br /><br />Przykład #1: Określona wartość zawiera nieprawidłowy znak (na przykład-). <br /><br />Przykład #2: Określona wartość zawiera podwójne zestaw nawiasów klamrowych. <br /><br />Przykład #3: Określona wartość nie ma zamykający nawias klamrowy.<br /> | Przykład #1: % {resp_user agenta} <br /><br />Przykład #2: % {{hosta}} <br /><br />Przykład #3: % {hosta |
| Brak nazwy zmiennej | Zawsze jest zwracana wartość NULL, gdy zmienna nie jest określona. | %{} |
| Tylko | Znaki, które końcu zmienną są traktowane jako wartości literału. <br />Przykładowa wartość po prawej stronie zawiera końcowe nawias klamrowy, który będzie traktowany jako wartość literału. | % {hosta}} |

## <a name="setting-default-header-values"></a>Ustawianie wartości domyślnych nagłówka
Wartość domyślna można przypisać do nagłówka podczas spełnia żadnego z następujących warunków:
- Brak Cofnij
- Ustaw wartość null.

W poniższej tabeli opisano sposób definiowania wartości domyślnej.

| Warunek | Składnia | Przykład | Opis |
| --------- | ------ | --------| ----------- |
| Wartość domyślna należy ustawić nagłówka, gdy spełnia żadnego z następujących warunków: <br /><br />-Brak nagłówka <br /><br />-Header wartość jest równa NULL.| % {Zmiennej: = wartość} | % {http_referer: = nieokreślony} | Nagłówek Odnośnik będzie można ustawić tylko *nieokreślony* po jest lub nie jest ustawiony na wartość NULL. Żadna akcja ma miejsce, jeśli została ustawiona. |
| Nagłówek należy ustawić wartość domyślna, gdy jest on niedostępny. | % {Zmiennej = wartość} | % {http_referer = nieokreślony} | Nagłówek Odnośnik będzie można ustawić tylko *nieokreślony* gdy jest on niedostępny. Żadna akcja ma miejsce, jeśli została ustawiona. |
| Ustaw nagłówek na wartość domyślną, jeśli go nie spełnia żadnego z następujących warunków: <br /><br />-Brak<br /><br /> -Ustawiana wartość NULL. | % {Zmiennej: + wartość} | % {http_referer: + nieokreślony} | Nagłówek Odnośnik będzie można ustawić tylko *nieokreślony* po przypisać wartości do niego. Żadna akcja ze strony będzie miała miejsce, jeśli jest lub nie jest ustawiony na wartość NULL. |

## <a name="manipulating-variables"></a>Manipulowanie zmiennych
Zmienne można manipulować w następujący sposób:

- Rozszerzanie podciągów
- Usuwanie wzorców

### <a name="substring-expansion"></a>Substring rozszerzenia
Domyślnie zmienna zostanie rozwinięty w celu jego pełnej wartości. Należy użyć następującej składni, aby rozwinąć tylko podciąg wartość zmiennej.

`%<Variable>:<Offset>:<Length>}`

Informacje o kluczu:

- Wartość przypisana do terminu Offset Określa początkowy znak podciągu:

     - Dodatnia: Początkowy znak podciągu jest obliczana na podstawie pierwszego znaku w ciągu.
     - Zero: Początkowy znak podciągu to pierwszy znak w ciągu.
     - Ujemna: Początkowy znak podciągu jest obliczana na podstawie ostatni znak w ciągu.

- Długość podciąg jest określany przez *długość* termin:

     - Pominięto: Pominięcie termin długość umożliwia podciąg uwzględnić wszystkie znaki od początkowego znaku lub końca ciągu.
     - Dodatnia: Określa podciąg początkowy znak w prawo.
     - Ujemna: Określa podciąg początkowy znak w lewo.

#### <a name="example"></a>Przykład:

Poniższy przykład opiera się na następujący adres URL żądania próbki:

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

Następujący ciąg przedstawiono różne metody zmiennych:

https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

W oparciu o adresie URL żądania próbki, powyżej manipulowania zmiennej utworzy następującą wartość:

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### <a name="pattern-removal"></a>Usunięcie wzorca
Tekst, który jest zgodna z określonym wzorcem można usunąć od początku lub na końcu wartości zmiennej.

| Składnia | Akcja |
| ------ | ------ |
| {Zmienna #Pattern} % | Usuń tekst, gdy określony wzorzec znajduje się na początku wartość zmiennej. |
| {Zmienna % wzorzec} % | Usuń tekst, gdy określony wzorzec znajduje się na końcu wartość zmiennej. |

#### <a name="example"></a>Przykład:

W tym przykładowym scenariuszu *request_uri* zmienna jest ustawiona na:

`/800001/myorigin/marketing/product.html?language=en-US`

Poniższa tabela przedstawia, jak działa ta składnia.

| Przykład składni | Wyniki |
| ------------- | ------- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/Marketing/Product.HTML?Language=en-us | Ponieważ zmiennej rozpoczyna się od wzorzec, został zastąpiony. |
| htm % {request_uri % html} | /800001/myorigin/Marketing/Product.HTML?Language=en-us | Ponieważ zmiennej nie zakończyć za pomocą wzorca, nie było zmiany.|

### <a name="find-and-replace"></a>Znajdź i Zamień
Znajdź i Zamień składnia jest opisane w poniższej tabeli.

| Składnia | Akcja |
| ------ | ------ |
| %{Variable/Find/replace} | Znajdź i Zamień pierwsze wystąpienie określonego wzorca. |
| %{Variable//Find/replace} | Znajdź i Zamień wszystkie wystąpienia określonego wzorca. |
| % {Zmiennej ^} |Konwertuj całą wartość na wielkie litery. |
| % {Zmiennej ^ znaleźć} | Pierwsze wystąpienie określonego wzorca przekonwertowania na wielkie litery. |
| {Zmienna,} % | Konwertuj całą wartość na małe litery. |
| {Zmienna, Znajdź} % | Pierwsze wystąpienie określonego wzorca przekonwertowania na małe litery. |

### <a name="find-and-rewrite"></a>Znajdź i ponownego napisania
Zmiany w Znajdź i Zamień można użyć w tekst, który pasuje do określonego wzorca podczas ponownego tworzenia go. W poniższej tabeli opisano składnię Znajdź i ponowne zapisywanie adresów.

| Składnia | Akcja |
| ------ | ------ |
| %{Variable/=Find/Rewrite} | Znajdź, kopiowanie i Zapisz ponownie wszystkie wystąpienia określonego wzorca. |
| %{Variable/^Find/Rewrite} | Znajdź, kopiowanie i edycji określony wzorzec, jeśli występuje on na początku zmiennej. |
| %{Variable/$Find/Rewrite} | Znajdź, kopiowanie i edycji określony wzorzec, jeśli występuje on na końcu zmiennej. |
| %{Variable/Find} | Znajdź i Usuń wszystkie wystąpienia określonego wzorca. |

Informacje o kluczu:

- Rozwiń tekst, który pasuje do określonego wzorca określając dolara, a następnie liczbą całkowitą (na przykład $1).

- Można określić wielu wzorców. Kolejność, w którym określono wzorzec określa liczba całkowita, która zostanie przypisana do niego. W poniższym przykładzie pierwszego dopasowania wzorca "www.," drugi wzorzec pasuje do domeny drugiego poziomu, a trzeci wzorzec pasuje do domeny najwyższego poziomu:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- Ponownie zapisane wartość może zawierać dowolną kombinację tekstu i te symbole zastępcze.

    W poprzednim przykładzie nazwa hosta jest napisany od nowa do `cdn.$2.$3:80` (na przykład cdn.mydomain.com:80).

- W przypadku wzorca symbolu zastępczego (na przykład $1) można modyfikować za pomocą następujących flag:
     - U: wielkie rozwinięte wartość.

         Przykład składni:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: małymi literami rozwinięte wartość.

         Przykład składni:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- Operator muszą zostać określone przed wzorzec. Określony operator określa zachowanie Przechwytywanie wzorca:

     - `=`: Wskazuje, że wszystkie wystąpienia określony wzorzec musi przechwycony i napisany od nowa.
     - `^`: Wskazuje, że będzie można przechwycić tylko tekst, który rozpoczyna się od określonego wzorca.
     - `$`: Oznacza, że tylko tekst, który kończy się z określonym wzorcem będzie przechwytywania.
 
- W przypadku pominięcia */przepisywania* wartość, tekst, który odpowiada wzorcowi zostanie usunięta.


