---
title: Zmienne HTTP dla aparatu reguł usługi Azure CDN | Dokumentacja firmy Microsoft
description: Zmienne HTTP umożliwiają pobieranie metadanych żądań i odpowiedzi HTTP.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus
ms.openlocfilehash: 8d4fc5fbdc3185c46f00d94537b197ec03f66755
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60709924"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>Zmienne HTTP dla usługi Azure CDN aparatu reguł
Zmienne HTTP udostępniają oznacza, że za pomocą którego można pobrać metadanych żądań i odpowiedzi HTTP. Następnie można te metadane dynamicznie zmieniać żądania lub odpowiedzi. Używanie zmiennych HTTP jest ograniczone do następujących funkcje aparatu reguł:

- [Napisz ponownie klucz pamięci podręcznej](cdn-rules-engine-reference-features.md#cache-key-rewrite)
- [Modyfikowanie nagłówek żądania klienta](cdn-rules-engine-reference-features.md#modify-client-request-header)
- [Modyfikowanie nagłówek odpowiedzi klienta](cdn-rules-engine-reference-features.md#modify-client-response-header)
- [Adres URL przekierowania](cdn-rules-engine-reference-features.md#url-redirect)
- [Ponowne zapisywanie adresów URL](cdn-rules-engine-reference-features.md#url-rewrite)

## <a name="definitions"></a>Definicje
W poniższej tabeli opisano obsługiwane zmienne HTTP. Po geograficznie metadanych (na przykład, kod pocztowy) nie są dostępne dla poszczególnych żądań, zwracana jest wartość pusta.


| Name (Nazwa) | Zmienna | Opis | Wartość przykładowa |
| ---- | -------- | ----------- | ------------ |
| Numer ASN (żądającego) | % {geo_asnum} | Wskazuje żądającego jako numer. <br /><br />**Przestarzałe:** % {virt_dst_asnum}. <br />Ta zmienna została zastąpiona % {geo_asnum}. Mimo że regułę, która korzysta z tej zmiennej przestarzałe będą nadal działać, należy zaktualizować go do używania nowej zmiennej. | AS15133 |
| Miasto (żądającego) | %{geo_city} | Wskazuje Miasto żądającego. | Los Angeles |
| Kontynent (żądającego) | % {geo_continent} | Wskazuje, kontynent żądającego za pośrednictwem jej skrót. <br />Prawidłowe wartości to: <br />AF: Afryka<br />AS: Azja<br />UE: Europa<br />NAZWA: Ameryka Północna<br />OC: Oceania<br />AMERYKA POŁUDNIOWA: Ameryka Południowa<br /><br />**Przestarzałe:** % {virt_dst_continent}. <br />Ta zmienna została zastąpiona % {geo_continent}. <br />Mimo że regułę, która korzysta z tej zmiennej przestarzałe będą nadal działać, należy zaktualizować go do używania nowej zmiennej.| ND |
| Wartość pliku cookie | %{cookie_Cookie} | Zwraca wartość odpowiadającą kluczowi plik cookie identyfikowane przez określenie pliku Cookie. | Przykładowe zastosowanie: <br />%{cookie__utma}<br /><br />Wartość próbek:<br />111662281.2.10.1222100123 |
| Kraj (żądającego) | % {geo_country} | Wskazuje żądającego kraju pochodzenia za pośrednictwem jego numer kierunkowy kraju. <br />**Przestarzałe:** % {virt_dst_country}. <br /><br />Ta zmienna została zastąpiona % {geo_country}. Mimo że regułę, która korzysta z tej zmiennej przestarzałe będą nadal działać, należy zaktualizować go do używania nowej zmiennej. | USA |
| Obszar wyznaczonym rynku (żądającego) | %{geo_dma_code} |Wskazuje na rynku media żądającego przez jego kod regionu. <br /><br />To pole ma zastosowanie tylko do żądania, które pochodzą ze Stanów Zjednoczonych.| 745 |
| Metoda żądania HTTP | %{request_method} | Wskazuje, Metoda żądania HTTP. | GET |
| Kod stanu HTTP | %{status} | Wskazuje kod stanu HTTP dla odpowiedzi. | 200 |
| Adres IP (żądającego) | %{virt_dst_addr} | Wskazuje adres IP żądającego. | 192.168.1.1 |
| Szerokość (żądającego) | %{geo_latitude} | Określa szerokość żądającego. | 34.0995 |
| Długość geograficzną (żądającego) | %{geo_longitude} | Wskazuje żądającego długości geograficznej. | -118.4143 |
| Statystyczne obszarze metropolitarnym (żądającego) | %{geo_metro_code} | Wskazuje obszarze metropolitarnym żądającego. <br /><br />To pole ma zastosowanie tylko do żądania, które pochodzą ze Stanów Zjednoczonych.<br />| 745 |
| Port (żądającego) | %{virt_dst_port} | Wskazuje portów efemerycznych żądającego. | 55885 |
| Kod pocztowy (żądającego) | % {geo_postal_code} | Wskazuje kod pocztowy żądającego. | 90210 |
| Znaleziono ciąg zapytania | %{is_args} | Wartość tej zmiennej, zależy od tego, czy żądanie zawiera ciąg zapytania.<br /><br />-Znaleziono ciąg zapytania:?<br />-Brak ciągu zapytania: NULL | ? |
| Znaleziono parametr ciągu zapytania | % {is_amp} | Wartość tej zmiennej, zależy od tego, czy żądanie zawiera co najmniej jeden parametr ciągu zapytania.<br /><br />-Znaleziono parametru: &<br />-Brak parametry: NULL | & |
| Wartość parametru ciągu zapytania | %{arg_&lt;parameter&gt;} | Zwraca wartość, odpowiadającą parametr ciągu zapytania, które są identyfikowane za pomocą &lt;parametru&gt; termin. | Przykładowe zastosowanie: <br />% {arg_language}<br /><br />Przykładowy parametr ciągu zapytania: <br />? języka = pl<br /><br />Przykładowe wartości: pl |
| Wartość ciągu zapytania | % {query_string} | Wskazuje wartość ciągu całe zapytanie, które są zdefiniowane w adresie URL żądania. |key1=val1&key2=val2&key3=val3 |
| Domeny odwołania | % {referring_domain} | Wskazuje domenę zdefiniowane w nagłówku żądania odwołania. | <www.google.com> |
| Region (żądającego) | %{geo_region} | Wskazuje region żądającego (na przykład wtedy, Województwo) za pośrednictwem jej skrót alfanumeryczne. | CA |
| Wartość nagłówka żądania | %{http_RequestHeader} | Zwraca wartość odpowiadającą identyfikowane przez określenie RequestHeader nagłówka żądania. <br /><br />Jeśli nazwa nagłówka żądania zawiera znak łącznika (na przykład agenta użytkownika), zastąp go znakiem podkreślenia (na przykład User_Agent).| Przykładowe zastosowanie: % {http_Connection}<br /><br />Wartość próbek: Keep-Alive | 
| Host żądania | %{host} | Wskazuje hosta zdefiniowane w adresie URL żądania. | <www.mydomain.com> |
| Żądanie protokołu | %{request_protocol} | Określa protokół żądania. | HTTP/1.1 |
| Schemat żądania | %{scheme} | Wskazuje schemat żądania. |http |
| Identyfikator URI (względny) żądania | %{request_uri} | Wskazuje względną ścieżkę, łącznie z ciągu zapytania, zdefiniowane w identyfikatorze URI żądania. | /Marketing/foo.js?loggedin=true |
| Identyfikator URI (względem bez ciągu zapytania) żądania | % {Identyfikator uri} | Określa ścieżkę względną do żądanej zawartości. <br /><br/>Informacje o kluczu:<br />— W tym ścieżki względnej wyklucza ciągu zapytania.<br />— W tym ścieżki względnej odzwierciedla ponownego adresu URL. Adres URL będzie przepisany w następujących warunkach:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;— Funkcja ponowne zapisywanie adresów URL: Ta funkcja jest ponownie zapisuje ścieżki względnej, zdefiniowane w identyfikatorze URI żądania.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-Adres URL CNAME edge: Ten typ żądania jest przepisany do odpowiedniego adresu URL usługi CDN. |/800001/corigin/rewrittendir/foo.js |
| Identyfikator URI żądania | % {żądania} | W tym artykule opisano żądania. <br />Składnia: &lt;Metoda HTTP&gt; &lt;ścieżki względnej&gt; &lt;protokołu HTTP&gt; | Pobierz /marketing/foo.js?loggedin=true HTTP/1.1 |
| Wartość nagłówka odpowiedzi | %{resp_&lt;ResponseHeader&gt;} | Zwraca wartość, odpowiadającą nagłówek odpowiedzi identyfikowane przez &lt;ResponseHeader&gt; termin. <br /><br />Jeśli nazwa nagłówka żądania zawiera znak łącznika (na przykład agenta użytkownika), należy go zastąpić od znaku podkreślenia (na przykład User_Agent). | Przykładowe zastosowanie: % {resp_Content_Length}<br /><br />Wartość próbek: 100 |

## <a name="usage"></a>Sposób użycia
W poniższej tabeli opisano poprawna składnia określająca zmienną HTTP.


| Składnia | Przykład | Opis |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | %{host} | Użyj następującej składni, aby uzyskać całą wartość odpowiadającą określonej &lt;HTTPVariable&gt;. |
| %{&lt;HTTPVariableDelimiter&gt;} | % {hosta} | Użyj następującej składni można ustawić w przypadku całą wartość odpowiadającą określonej &lt;HTTPVariableDelimiter&gt;. |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{Host/=^www\.([^\.] +)\.([^\.:] +) /cdn.$2.$3:80} | Użycie wyrażenia regularnego dla &lt;HTTPVariableDelimiterExpression&gt; zastąpić, usunąć lub przetwarzać wartość zmiennej HTTP. |

Nazwy zmiennych HTTP obsługują tylko znaki alfabetyczne i znaki podkreślenia. Przekonwertować nieobsługiwane znaki podkreślenia.

## <a name="delimiter-reference"></a>Ogranicznik odwołania
Ogranicznik, można określić po zmienną HTTP, aby osiągnąć następujące efekty:

- Przekształć wartość skojarzona ze zmienną.

     Przykład: Całą wartość przekonwertowania na małe litery.

- Usuń wartość skojarzona ze zmienną.

- Manipulowanie wartość skojarzona ze zmienną.

     Przykład: Używanie wyrażeń regularnych, aby zmienić wartość skojarzona ze zmienną HTTP.

Ograniczniki są opisane w poniższej tabeli.

| Ogranicznik | Opis |
| --------- | ----------- |
| := | Wskazuje, że wartość domyślna będzie można przypisać do zmiennej, gdy jest ona: <br />-Brak <br />-Ustawienia na wartość NULL. |
| :+ | Wskazuje, że wartości domyślnej zostanie przypisany do zmiennej wartości przypisane do niego. |
| : | Wskazuje, zostanie rozszerzona podciągiem wartość przypisana do zmiennej. |
| # | Wskazuje, że należy usunąć ze wzorcem określonym ogranicznikiem tego, po jego odnalezieniu na początku wartość skojarzona ze zmienną. |
| % | Wskazuje, że należy usunąć ze wzorcem określonym ogranicznikiem tego, po jego odnalezieniu na końcu wartość skojarzona ze zmienną. <br />Ta definicja ma zastosowanie tylko w przypadku, gdy symbolu % jest używana jako ogranicznika. |
| / | Rozgranicza zmienną HTTP lub wzorzec. |
| // | Znajdź i Zamień wszystkie wystąpienia określonego wzorca. |
| /= | Znajdź, a następnie skopiuj i Zapisz ponownie wszystkie wystąpienia określonego wzorca. |
| ,  | Konwertuj wartość skojarzona ze zmienną HTTP na małe litery. |
| ^ | Konwertuj wartość skojarzona ze zmienną HTTP na wielkie litery. |
| ,, | Przekonwertuj wszystkie wystąpienia określony znak w wartości skojarzone ze zmienną HTTP na małe litery. |
| ^^ | Przekonwertuj wszystkie wystąpienia określony znak w wartości skojarzone ze zmienną HTTP na wielkie litery. |

## <a name="exceptions"></a>Wyjątki
W poniższej tabeli opisano okoliczności, w których określony tekst nie jest traktowane jako zmienną HTTP.

| Warunek | Opis | Przykład |
| --------- | ----------- | --------|
| Anulowania zapewnianego element % symboli | Symbol procentu można wyjściowym przy użyciu ukośnik odwrotny. <br />Przykładowa wartość po prawej stronie będzie traktowana jako wartość literału, a nie zmienną HTTP.| \%{host} |
| Nieznany zmiennych | Pusty ciąg jest zawsze zwracane nieznany zmiennych. | % {unknownvariable} |
| Nieprawidłowe znaki lub nieprawidłowa składnia | Zmienne, które zawierają nieprawidłowe znaki lub nieprawidłowa składnia są traktowane jako wartości literału. <br /><br />Przykład #1: Określona wartość zawiera nieprawidłowy znak (na przykład-). <br /><br />Przykład #2: Określona wartość zawiera zbiór podwójne nawiasy klamrowe. <br /><br />Przykład #3: Określona wartość Brak nawiasu zamykającego nawiasu klamrowego.<br /> | Przykład #1: % {resp_user agenta} <br /><br />Przykład #2: % {{hosta}} <br /><br />Przykład #3: % {hosta |
| Brak nazwy zmiennej | Zawsze zostanie zwrócona wartość NULL, jeśli zmienna nie zostanie określony. | %{} |
| Tylko | Znaki, które końcu zmiennej są traktowane jako wartości literału. <br />Przykładowa wartość po prawej stronie zawiera końcowe nawiasów klamrowych, który będzie traktowane jako wartości literału. | %{host}} |

## <a name="setting-default-header-values"></a>Ustawienie domyślne wartości nagłówka
Wartość domyślną można przypisać do nagłówka, gdy spełnia dowolne z następujących warunków:
- Brak usunięta
- Ustaw wartość NULL.

W poniższej tabeli opisano sposób definiowania wartości domyślnej.

| Warunek | Składnia | Przykład | Opis |
| --------- | ------ | --------| ----------- |
| Ustaw nagłówek w wartości domyślnej spełnia dowolne z następujących warunków: <br /><br />-Brak nagłówka <br /><br />-Header wartość jest równa NULL.| % {Zmiennej: = wartość} | %{http_referer:=unspecified} | Nagłówka Odnośnik będzie można ustawić tylko *nieokreślony* po go lub nie jest ustawiona na wartość NULL. Żadna akcja będzie mieć miejsce, jeśli został ustawiony. |
| Nagłówek należy ustawić wartość domyślną, gdy jest Brak. | % {Zmiennej = wartość} | %{http_referer=unspecified} | Nagłówka Odnośnik będzie można ustawić tylko *nieokreślony* gdy jest Brak. Żadna akcja będzie mieć miejsce, jeśli został ustawiony. |
| Ustaw nagłówek na wartość domyślną, gdy go nie spełnia żadnego z następujących warunków: <br /><br />-Brak<br /><br /> -Ustawienia na wartość NULL. | % {Zmiennej: + wartość} | % {http_referer: + nieokreślony} | Nagłówka Odnośnik będzie można ustawić tylko *nieokreślony* po wartości zostały przypisane do niego. Żadna akcja będzie miała miejsce, jeśli go lub nie jest ustawiona na wartość NULL. |

## <a name="manipulating-variables"></a>Manipulowanie zmiennych
Zmienne można manipulować w następujący sposób:

- Rozwijanie podciągów
- Usuwanie wzorców

### <a name="substring-expansion"></a>Podciąg rozszerzenia
Domyślnie zmienna zostanie rozwinięte jego pełna wartość. Tylko rozszerzenia podciągiem wartość zmiennej, należy użyć następującej składni.

`%<Variable>:<Offset>:<Length>}`

Informacje o kluczu:

- Wartość przypisana do termin przesunięcie Określa początkowy znak podciąg:

     - Dodatnia: Początkowy znak podciągu jest obliczana na podstawie pierwszego znaku w ciągu.
     - Zero: Początkowy znak podciąg to pierwszy znak w ciągu.
     - Ujemne: Początkowy znak podciągu jest obliczany od ostatniego znaku w ciągu.

- Długość podciągu jest określana przez *długość* termin:

     - Pominięto: Pominięcie termin długość umożliwia podciąg uwzględnić wszystkie znaki między początkowy znak i końca ciągu.
     - Dodatnia: Określa długość podciągu z początkowy znak po prawej stronie.
     - Ujemne: Określa długość podciągu z początkowy znak w lewo.

#### <a name="example"></a>Przykład:

Poniższy przykład opiera się na następujący adres URL żądania próbki:

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

Następujący ciąg przedstawia różne metody do manipulowania zmiennych:

https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

Oparte na adresie URL żądania próbki, powyżej manipulowania zmiennej dadzą następującą wartość:

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### <a name="pattern-removal"></a>Wzorzec usuwania
Tekst, który jest zgodny ze wzorcem określonym można usunąć od początku lub końcu wartości zmiennej.

| Składnia | Akcja |
| ------ | ------ |
| %{Variable#Pattern} | Usuń tekst, gdy określony wzorzec znajduje się na początku wartość zmiennej. |
| %{Variable%Pattern} | Usuń tekst, gdy określony wzorzec znajduje się na końcu wartości zmiennej. |

#### <a name="example"></a>Przykład:

W tym przykładowym scenariuszu *request_uri* zmienna jest ustawiona na:

`/800001/myorigin/marketing/product.html?language=en-US`

Poniższa tabela przedstawia, jak działa ta składnia.

| Przykład składni | Wyniki | |
| ------------- | ------- | --- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html?language=en-US | Ponieważ zmienna rozpoczyna się od wzorca, został zastąpiony. |
| %{request_uri%html}htm | /800001/myorigin/marketing/product.html?language=en-US | Ponieważ zmienna nie kończy się za pomocą wzorca, nie było zmiany.|

### <a name="find-and-replace"></a>Znajdź i Zamień
W poniższej tabeli opisano składnię Znajdź i Zamień.

| Składnia | Akcja |
| ------ | ------ |
| %{Variable/Find/replace} | Znajdź i Zamień pierwsze wystąpienie określonego wzorca. |
| %{Variable//Find/replace} | Znajdź i Zamień wszystkie wystąpienia określonego wzorca. |
| % {Zmiennej ^} |Całą wartość przekonwertowania na wielkie litery. |
| %{Variable^Find} | Pierwsze wystąpienie określonego wzorca przekonwertowania na wielkie litery. |
| % {Zmiennej} | Całą wartość przekonwertowania na małe litery. |
| {Zmienna, Znajdź} % | Pierwsze wystąpienie określonego wzorca przekonwertowania na małe litery. |

### <a name="find-and-rewrite"></a>Znajdź i ponowne zapisywanie adresów
W użytku odmianę Znajdź i Zamień tekst, który pasuje do wzorca określonego podczas poprawiania go. W poniższej tabeli opisano składnię wyszukiwania i ponowne zapisywanie adresów.

| Składnia | Akcja |
| ------ | ------ |
| %{Variable/=Find/Rewrite} | Znajdź, a następnie skopiuj i Zapisz ponownie wszystkie wystąpienia określonego wzorca. |
| %{Variable/^Find/Rewrite} | Znajdź, kopiowanie i ponowne zapisywanie adresów określony wzorzec, jeśli występuje na początku zmiennej. |
| %{Variable/$Find/Rewrite} | Znajdź, kopiowanie i ponowne zapisywanie adresów określony wzorzec, jeśli występuje na końcu zmiennej. |
| %{Variable/Find} | Znajdź i Usuń wszystkie wystąpienia określonego wzorca. |

Informacje o kluczu:

- Rozwiń tekst, który pasuje do wzorca określonego przez określenie znaku dolara, a następnie liczbą całkowitą (na przykład $1).

- Można określić wielu wzorców. Kolejność, w którym określono wzorzec Określa liczbę całkowitą, która zostanie przypisana do niego. W poniższym przykładzie pierwszy dopasowania do wzorca "www,". drugi wzorzec pasuje do domeny drugiego poziomu, a trzeci wzorzec pasuje do domeny najwyższego poziomu:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- Wartość nowych może zawierać dowolną kombinację tekstu i te symbole zastępcze.

    W poprzednim przykładzie nazwa hosta jest przepisane, aby `cdn.$2.$3:80` (na przykład cdn.mydomain.com:80).

- W przypadku symbolu zastępczego wzorca (na przykład $1) można modyfikować za pomocą następujących flag:
     - U: Wielkie litery rozwinięte wartości.

         Przykład składni:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: Małe litery, rozwinięte wartości.

         Przykład składni:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- Operator muszą zostać określone przed wzorzec. Określony operator określa zachowanie przechwytywania wzorca:

     - `=`: Wskazuje, że wszystkie wystąpienia określony wzorzec musi być przechwytywane i przepisany.
     - `^`: Wskazuje, czy będzie można przechwycić tylko tekst, który rozpoczyna się z określonym wzorcem.
     - `$`: Wskazuje, że tylko tekst, który kończy się z określonym wzorcem będzie przechwytywania.
 
- Jeżeli pominięto */ponownego zapisywania* wartości, tekst, który pasuje do wzorca jest usuwany.


