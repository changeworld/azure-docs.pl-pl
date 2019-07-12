---
title: Zmienne HTTP dla aparatu reguł usługi Azure CDN | Dokumentacja firmy Microsoft
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
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593814"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>Zmienne HTTP dla usługi Azure CDN aparatu reguł
Zmienne HTTP udostępniają oznacza, że za pomocą którego można pobrać metadanych żądań i odpowiedzi HTTP. Następnie można te metadane dynamicznie zmieniać żądania lub odpowiedzi. Używanie zmiennych HTTP jest ograniczone do następujących funkcje aparatu reguł:

- [Napisz ponownie klucz pamięci podręcznej](cdn-verizon-premium-rules-engine-reference-features.md#cache-key-rewrite)
- [Modyfikowanie nagłówek żądania klienta](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-request-header)
- [Modyfikowanie nagłówek odpowiedzi klienta](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-response-header)
- [Adres URL przekierowania](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect)
- [Ponowne zapisywanie adresów URL](cdn-verizon-premium-rules-engine-reference-features.md#url-rewrite)

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

## <a name="usage"></a>Użycie
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
| , | Konwertuj wartość skojarzona ze zmienną HTTP na małe litery. |
| ^ | Konwertuj wartość skojarzona ze zmienną HTTP na wielkie litery. |
| ,, | Przekonwertuj wszystkie wystąpienia określony znak w wartości skojarzone ze zmienną HTTP na małe litery. |
| ^^ | Przekonwertuj wszystkie wystąpienia określony znak w wartości skojarzone ze zmienną HTTP na wielkie litery. |

## <a name="exceptions"></a>Wyjątki
W poniższej tabeli opisano okoliczności, w których określony tekst nie jest traktowane jako zmienną HTTP.

| Warunek | Opis | Przykład |
| --------- | ----------- | --------|
| Anulowania zapewnianego element % symboli | Symbol procentu można wyjściowym przy użyciu ukośnik odwrotny. <br />Przykładowa wartość po prawej stronie będzie traktowana jako wartość literału, a nie zmienną HTTP.| \%{host} |
| Unknown variables | An empty string is always returned for unknown variables. | %{unknown_variable} |
| Invalid characters or syntax | Variables that contain invalid characters or syntax are treated as literal values. <br /><br />Example #1: The specified value contains an invalid character (for example, -). <br /><br />Example #2: The specified value contains a double set of curly braces. <br /><br />Example #3: The specified value is missing a closing curly brace.<br /> | Example #1: %{resp_user-agent} <br /><br />Example #2: %{{host}} <br /><br />Example #3: %{host |
| Missing variable name | A NULL value is always returned when a variable is not specified. | %{} |
| Trailing characters | Characters that trail a variable are treated as literal values. <br />The sample value to the right contains a trailing curly brace that will be treated as a literal value. | %{host}} |

## Setting default header values
A default value can be assigned to a header when it meets any of the following conditions:
- Missing/unset
- Set to NULL.

The following table describes how to define a default value.

| Condition | Syntax | Example | Description |
| --------- | ------ | --------| ----------- |
| Set a header to a default value when it meets any of the following conditions: <br /><br />- Missing Header <br /><br />- Header value is set to NULL.| %{Variable:=Value} | %{http_referrer:=unspecified} | The Referrer header will only be set to *unspecified* when it is either missing or set to NULL. No action will take place if it has been set. |
| Set a header to a default value when it is missing. | %{Variable=Value} | %{http_referrer=unspecified} | The Referrer header will only be set to *unspecified* when it is missing. No action will take place if it has been set. |
| Set the header to a default value when it does not meet any of the following conditions: <br /><br />- Missing<br /><br /> - Set to NULL. | %{Variable:+Value} | %{http_referrer:+unspecified} | The Referrer header will only be set to *unspecified* when a value has been assigned to it. No action will take place if it is either missing or set to NULL. |

## Manipulating variables
Variables can be manipulated in the following ways:

- Expanding substrings
- Removing patterns

### Substring expansion
By default, a variable will expand to its full value. Use the following syntax to only expand a substring of the variable's value.

`%<Variable>:<Offset>:<Length>}`

Key information:

- The value assigned to the Offset term determines the starting character of the substring:

     - Positive: The starting character of the substring is calculated from the first character in the string.
     - Zero: The starting character of the substring is the first character in the string.
     - Negative: The starting character of the substring is calculated from the last character in the string.

- The length of the substring is determined by the *Length* term:

     - Omitted: Omitting the Length term allows the substring to include all characters between the starting character and the end of the string.
     - Positive: Determines the length of the substring from the starting character to the right.
     - Negative: Determines the length of the substring from the starting character to the left.

#### Example:

The following example relies on the following sample request URL:

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

The following string demonstrates various methods for manipulating variables:

https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

Based on the sample request URL, the above variable manipulation will produce the following value:

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### Pattern removal
Text that matches a specific pattern can be removed from either the beginning or the end of a variable's value.

| Syntax | Action |
| ------ | ------ |
| %{Variable#Pattern} | Remove text when the specified pattern is found at the beginning of a variable's value. |
| %{Variable%Pattern} | Remove text when the specified pattern is found at the end of a variable's value. |

#### Example:

In this sample scenario, the *request_uri* variable is set to:

`/800001/myorigin/marketing/product.html?language=en-US`

The following table demonstrates how this syntax works.

| Sample syntax | Results | |
| ------------- | ------- | --- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html?language=en-US | Because the variable starts with the pattern, it was replaced. |
| %{request_uri%html}htm | /800001/myorigin/marketing/product.html?language=en-US | Because the variable doesn't end with the pattern, there was no change.|

### Find and replace
The find and replace syntax is described in the following table.

| Syntax | Action |
| ------ | ------ |
| %{Variable/Find/Replace} | Find and replace first occurrence of the specified pattern. |
| %{Variable//Find/Replace} | Find and replace all occurrences of the specified pattern. |
| %{Variable^} |Convert the entire value to uppercase. |
| %{Variable^Find} | Convert the first occurrence of the specified pattern to uppercase. |
| %{Variable,} | Convert the entire value to lowercase. |
| %{Variable,Find} | Convert the first occurrence of the specified pattern to lowercase. |

### Find and rewrite
For a variation on find and replace, use the text that matches the specified pattern when rewriting it. The find and rewrite syntax is described in the following table.

| Syntax | Action |
| ------ | ------ |
| %{Variable/=Find/Rewrite} | Find, copy, and rewrite all occurrences of the specified pattern. |
| %{Variable/^Find/Rewrite} | Find, copy, and rewrite the specified pattern when it occurs at the start of the variable. |
| %{Variable/$Find/Rewrite} | Find, copy, and rewrite the specified pattern when it occurs at the end of the variable. |
| %{Variable/Find} | Find and delete all occurrences of the specified pattern. |

Key information:

- Expand text that matches the specified pattern by specifying a dollar sign followed by a whole integer (for example, $1).

- Multiple patterns can be specified. The order in which the pattern is specified determines the integer that will be assigned to it. In the following example, the first pattern matches "www.," the second pattern matches the second-level domain, and the third pattern matches the top-level domain:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- The rewritten value can consist of any combination of text and these placeholders.

    In the previous example, the hostname is rewritten to `cdn.$2.$3:80` (for example, cdn.mydomain.com:80).

- The case of a pattern placeholder (for example, $1) can be modified through the following flags:
     - U: Uppercase the expanded value.

         Sample syntax:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: Lowercase the expanded value.

         Sample syntax:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- An operator must be specified before the pattern. The specified operator determines the pattern-capturing behavior:

     - `=`: Indicates that all occurrences of the specified pattern must be captured and rewritten.
     - `^`: Indicates that only text that starts with the specified pattern will be captured.
     - `$`: Indicates that only text that ends with the specified pattern will be capture.
 
- If you omit the */Rewrite* value, the text that matches the pattern is deleted.

\`{host}le: HTTP variables for Azure CDN rules engine | Microsoft Docs
description: HTTP variables allow you to retrieve HTTP request and response metadata.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''

ms.assetid: 
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus


---
# HTTP variables for Azure CDN rules engine
HTTP variables provide the means through which you can retrieve HTTP request and response metadata. This metadata can then be used to dynamically alter a request or a response. The use of HTTP variables is restricted to the following rules engine features:

- [Cache-Key Rewrite](cdn-verizon-premium-rules-engine-reference-features.md#cache-key-rewrite)
- [Modify Client Request Header](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-request-header)
- [Modify Client Response Header](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-response-header)
- [URL Redirect](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect)
- [URL Rewrite](cdn-verizon-premium-rules-engine-reference-features.md#url-rewrite)

## Definitions
The following table describes the supported HTTP variables. A blank value is returned when GEO metadata (for example, postal code) is unavailable for a particular request.


| Name | Variable | Description | Sample value |
| ---- | -------- | ----------- | ------------ |
| ASN (Requester) | %{geo_asnum} | Indicates the requester's AS number. <br /><br />**Deprecated:** %{virt_dst_asnum}. <br />This variable has been deprecated in favor of %{geo_asnum}. Although a rule that uses this deprecated variable will continue to work, you should update it to use the new variable. | AS15133 |
| City (Requester) | %{geo_city} | Indicates the requester's city. | Los Angeles |
| Continent (Requester) | %{geo_continent} | Indicates the requester's continent through its abbreviation. <br />Valid values are: <br />AF: Africa<br />AS: Asia<br />EU: Europe<br />NA: North America<br />OC: Oceania<br />SA: South America<br /><br />**Deprecated:** %{virt_dst_continent}. <br />This variable has been deprecated in favor of %{geo_continent}. <br />Although a rule that uses this deprecated variable will continue to work, you should update it to use the new variable.| N/A |
| Cookie Value | %{cookie_Cookie} | Returns the value corresponding to the cookie key identified by the Cookie term. | Sample Usage: <br />%{cookie__utma}<br /><br />Sample Value:<br />111662281.2.10.1222100123 |
| Country (Requester) | %{geo_country} | Indicates the requester's country of origin through its country code. <br />**Deprecated:** %{virt_dst_country}. <br /><br />This variable has been deprecated in favor of %{geo_country}. Although a rule that uses this deprecated variable will continue to work, you should update it to use the new variable. | US |
| Designated Market Area (Requester) | %{geo_dma_code} |Indicates the requester's media market by its region code. <br /><br />This field is only applicable to requests that originate from the United States.| 745 |
| HTTP Request Method | %{request_method} | Indicates the HTTP request method. | GET |
| HTTP Status Code | %{status} | Indicates the HTTP status code for the response. | 200 |
| IP Address (Requester) | %{virt_dst_addr} | Indicates the requester's IP address. | 192.168.1.1 |
| Latitude (Requester) | %{geo_latitude} | Indicates the requester's latitude. | 34.0995 |
| Longitude (Requester) | %{geo_longitude} | Indicates the requester's longitude. | -118.4143 |
| Metropolitan Statistical Area (Requester) | %{geo_metro_code} | Indicates the requester's metropolitan area. <br /><br />This field is only applicable to requests that originate from the United States.<br />| 745 |
| Port (Requester) | %{virt_dst_port} | Indicates the requester's ephemeral port. | 55885 |
| Postal Code (Requester) | %{geo_postal_code} | Indicates the requester's postal code. | 90210 |
| Query String Found | %{is_args} | The value for this variable varies according to whether the request contains a query string.<br /><br />- Query String Found: ?<br />- No Query String: NULL | ? |
| Query String Parameter Found | %{is_amp} | The value for this variable varies according to whether the request contains at least one query string parameter.<br /><br />- Parameter Found: &<br />- No Parameters: NULL | & |
| Query String Parameter Value | %{arg_&lt;parameter&gt;} | Returns the value corresponding to the query string parameter identified by the &lt;parameter&gt; term. | Sample Usage: <br />%{arg_language}<br /><br />Sample Query String Parameter: <br />?language=en<br /><br />Sample Value: en |
| Query String Value | %{query_string} | Indicates the entire query string value defined in the request URL. |key1=val1&key2=val2&key3=val3 |
| Referrer Domain | %{referring_domain} | Indicates the domain defined in the Referrer request header. | <www.google.com> |
| Region (Requester) | %{geo_region} | Indicates the requester's region (for example, state or province) through its alphanumeric abbreviation. | CA |
| Request Header Value | %{http_RequestHeader} | Returns the value corresponding to the request header identified by the RequestHeader term. <br /><br />If the name of the request header contains a dash (for example, User-Agent), replace it with an underscore (for example, User_Agent).| Sample Usage: %{http_Connection}<br /><br />Sample Value: Keep-Alive | 
| Request Host | %{host} | Indicates the host defined in the request URL. | <www.mydomain.com> |
| Request Protocol | %{request_protocol} | Indicates the request protocol. | HTTP/1.1 |
| Request Scheme | %{scheme} | Indicates the request scheme. |http |
| Request URI (Relative) | %{request_uri} | Indicates the relative path, including the query string, defined in the request URI. | /marketing/foo.js?loggedin=true |
| Request URI (Relative without query string) | %{uri} | Indicates the relative path to the requested content. <br /><br/>Key information:<br />- This relative path excludes the query string.<br />- This relative path reflects URL rewrites. A URL will be rewritten under the following conditions:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- URL Rewrite Feature: This feature rewrites the relative path defined in the request URI.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Edge CNAME URL: This type of request is rewritten to the corresponding CDN URL. |/800001/corigin/rewrittendir/foo.js |
| Request URI | %{request} | Describes the request. <br />Syntax: &lt;HTTP method&gt; &lt;relative path&gt; &lt;HTTP protocol&gt; | GET /marketing/foo.js?loggedin=true HTTP/1.1 |
| Response Header Value | %{resp_&lt;ResponseHeader&gt;} | Returns the value corresponding to the response header identified by the &lt;ResponseHeader&gt; term. <br /><br />If the name of the response header contains a dash (for example, User-Agent), replace it with an underscore (for example, User_Agent). | Sample Usage: %{resp_Content_Length}<br /><br />Sample Value: 100 |

## Usage
The following table describes the proper syntax for specifying an HTTP variable.


| Syntax | Example | Description |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | %{host} | Use this syntax to get the entire value corresponding to the specified &lt;HTTPVariable&gt;. |
| %{&lt;HTTPVariableDelimiter&gt;} | %{host,} | Use this syntax to set the case for the entire value corresponding to the specified  &lt;HTTPVariableDelimiter&gt;. |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80} | Use a regular expression for &lt;HTTPVariableDelimiterExpression&gt; to replace, delete, or manipulate an HTTP variable's value. |

HTTP variable names only support alphabetic characters and underscores. Convert unsupported characters to underscores.

## Delimiter reference
A delimiter can be specified after an HTTP variable to achieve any of the following effects:

- Transform the value associated with the variable.

     Example: Convert the entire value to lowercase.

- Delete the value associated with the variable.

- Manipulate the value associated with the variable.

     Example: Use regular expressions to change the value associated with the HTTP variable.

The delimiters are described in the following table.

| Delimiter | Description |
| --------- | ----------- |
| := | Indicates that a default value will be assigned to the variable when it is either: <br />- Missing <br />- Set to NULL. |
| :+ | Indicates that a default value will be assigned to the variable when a value has been assigned to it. |
| : | Indicates that a substring of the value assigned to the variable will be expanded. |
| # | Indicates that the pattern specified after this delimiter should be deleted when it is found at the beginning of the value associated with the variable. |
| % | Indicates that the pattern specified after this delimiter should be deleted when it is found at the end of the value associated with the variable. <br />This definition is only applicable when the % symbol is used as a delimiter. |
| / | Delimits an HTTP variable or a pattern. |
| // | Find and replace all instances of the specified pattern. |
| /= | Find, copy, and rewrite all occurrences of the specified pattern. |
| , | Convert the value associated with the HTTP variable to lowercase. |
| ^ | Convert the value associated with the HTTP variable to uppercase. |
| ,, | Convert all instances of the specified character in the value associated with the HTTP variable to lowercase. |
| ^^ | Convert all instances of the specified character in the value associated with the HTTP variable to uppercase. |

## Exceptions
The following table describes circumstances under which the specified text isn't treated as an HTTP variable.

| Condition | Description | Example |
| --------- | ----------- | --------|
| Escaping % symbol | The percentage symbol can be escaped through the use of a backslash. <br />The sample value to the right will be treated as a literal value and not as an HTTP variable.| \%{host} |
| Nieznany zmiennych | Pusty ciąg jest zawsze zwracane nieznany zmiennych. | % {unknown_variable} |
| Nieprawidłowe znaki lub nieprawidłowa składnia | Zmienne, które zawierają nieprawidłowe znaki lub nieprawidłowa składnia są traktowane jako wartości literału. <br /><br />Przykład #1: Określona wartość zawiera nieprawidłowy znak (na przykład-). <br /><br />Przykład #2: Określona wartość zawiera zbiór podwójne nawiasy klamrowe. <br /><br />Przykład #3: Określona wartość Brak nawiasu zamykającego nawiasu klamrowego.<br /> | Przykład #1: % {resp_user agenta} <br /><br />Przykład #2: % {{hosta}} <br /><br />Przykład #3: % {hosta |
| Brak nazwy zmiennej | Zawsze zostanie zwrócona wartość NULL, jeśli zmienna nie zostanie określony. | %{} |
| Znaki końcowe | Znaki, które końcu zmiennej są traktowane jako wartości literału. <br />Przykładowa wartość po prawej stronie zawiera końcowe nawiasów klamrowych, który będzie traktowane jako wartości literału. | %{host}} |

## <a name="setting-default-header-values"></a>Ustawienie domyślne wartości nagłówka
Wartość domyślną można przypisać do nagłówka, gdy spełnia dowolne z następujących warunków:
- Brak usunięta
- Ustaw wartość NULL.

W poniższej tabeli opisano sposób definiowania wartości domyślnej.

| Warunek | Składnia | Przykład | Opis |
| --------- | ------ | --------| ----------- |
| Ustaw nagłówek w wartości domyślnej spełnia dowolne z następujących warunków: <br /><br />-Brak nagłówka <br /><br />-Header wartość jest równa NULL.| % {Zmiennej: = wartość} | %{http_referrer:=unspecified} | Nagłówka odnośnika będzie można ustawić tylko *nieokreślony* po go lub nie jest ustawiona na wartość NULL. Żadna akcja będzie mieć miejsce, jeśli został ustawiony. |
| Nagłówek należy ustawić wartość domyślną, gdy jest Brak. | % {Zmiennej = wartość} | %{http_referrer=unspecified} | Nagłówka odnośnika będzie można ustawić tylko *nieokreślony* gdy jest Brak. Żadna akcja będzie mieć miejsce, jeśli został ustawiony. |
| Ustaw nagłówek na wartość domyślną, gdy go nie spełnia żadnego z następujących warunków: <br /><br />-Brak<br /><br /> -Ustawienia na wartość NULL. | % {Zmiennej: + wartość} | % {http_referrer: + nieokreślony} | Nagłówka odnośnika będzie można ustawić tylko *nieokreślony* po wartości zostały przypisane do niego. Żadna akcja będzie miała miejsce, jeśli go lub nie jest ustawiona na wartość NULL. |

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

| Składnia | Action |
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

| Składnia | Action |
| ------ | ------ |
| %{Variable/Find/replace} | Znajdź i Zamień pierwsze wystąpienie określonego wzorca. |
| %{Variable//Find/replace} | Znajdź i Zamień wszystkie wystąpienia określonego wzorca. |
| % {Zmiennej ^} |Całą wartość przekonwertowania na wielkie litery. |
| %{Variable^Find} | Pierwsze wystąpienie określonego wzorca przekonwertowania na wielkie litery. |
| % {Zmiennej} | Całą wartość przekonwertowania na małe litery. |
| {Zmienna, Znajdź} % | Pierwsze wystąpienie określonego wzorca przekonwertowania na małe litery. |

### <a name="find-and-rewrite"></a>Znajdź i ponowne zapisywanie adresów
W użytku odmianę Znajdź i Zamień tekst, który pasuje do wzorca określonego podczas poprawiania go. W poniższej tabeli opisano składnię wyszukiwania i ponowne zapisywanie adresów.

| Składnia | Action |
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


