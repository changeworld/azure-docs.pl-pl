---
title: Zasady i reguły usługi Azure Application Gateway zapory aplikacji sieci Web
description: Ta strona zawiera informacje o grupach i regułach reguł KSR aplikacji sieci Web.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 4/11/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 9f90f373bd6f1cfd34de1605783bf3a7f0185f4c
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240338"
---
# <a name="web-application-firewall-crs-rule-groups-and-rules"></a>Zasady i reguły reguły KSR zapory aplikacji sieci Web

Application Gateway Zapora aplikacji sieci Web (WAF) chroni aplikacje sieci Web przed typowymi lukami w zabezpieczeniach i lukami w zabezpieczeniach. Jest to realizowane za pośrednictwem reguł zdefiniowanych w oparciu o OWASP podstawowe zestawy reguł 3,0 lub 2.2.9. Reguły te można wyłączyć na zasadzie reguły. Ten artykuł zawiera bieżące reguły i dostępne zestaw reguł.

Następujące grupy reguł i reguły są dostępne w przypadku korzystania z Application Gateway z zaporą aplikacji sieci Web.

# <a name="owasp-30tabowasp3"></a>[OWASP 3.0](#tab/owasp3)

## <a name="owasp30"></a>Zestawy reguł

### <a name="General"></a> <p x-ms-format-detection="none">Ogólne</p>

|ruleId|Opis|
|---|---|
|200004|Możliwa wieloczęściowa niezgodna granica.|

### <a name="crs911"></a> <p x-ms-format-detection="none">ŻĄDANIE-911-METODA-WYMUSZANIE</p>

|ruleId|Opis|
|---|---|
|911100|Metoda nie jest dozwolona przez zasady|


### <a name="crs913"></a> <p x-ms-format-detection="none">ŻĄDANIE-913 — WYKRYWANIE SKANERA</p>

|ruleId|Opis|
|---|---|
|913100|Znaleziono agenta użytkownika skojarzonego z skanerem zabezpieczeń|
|913110|Znaleziono nagłówek żądania skojarzony ze skanerem zabezpieczeń|
|913120|Znaleziono nazwę pliku żądania/argument skojarzony z skanerem zabezpieczeń|
|913101|Znaleziono agenta użytkownika skojarzonego z skryptem/ogólnym klientem HTTP|
|913102|Znaleziono agenta użytkownika skojarzonego z przeszukiwarką sieci Web/bot|

### <a name="crs920"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|ruleId|Opis|
|---|---|
|920100|Nieprawidłowy wiersz żądania HTTP|
|920130|Nie można przeanalizować treści żądania.|
|920140|Nieczęściowa weryfikacja treści żądania nie powiodła się|
|920160|Nagłówek HTTP o długości zawartości nie jest wartością numeryczną.|
|920170|Pobierz lub zamów żądanie z zawartością treści.|
|920180|Żądanie POST nie zawiera nagłówka Content-Length.|
|920190|Zakres = nieprawidłowa wartość ostatniego bajtu.|
|920210|Znaleziono wiele lub sprzeczne dane nagłówka połączenia.|
|920220|Próba ataku przy użyciu kodowania adresu URL|
|920240|Próba ataku przy użyciu kodowania adresu URL|
|920250|Próba ataku przy użyciu kodowania UTF8|
|920260|Próba ataków typu Pełna/połówkowa w formacie Unicode|
|920270|Nieprawidłowy znak w żądaniu (znak null)|
|920280|W żądaniu brakuje nagłówka hosta|
|920290|Pusty nagłówek hosta|
|920310|Żądanie ma pusty nagłówek Accept|
|920311|Żądanie ma pusty nagłówek Accept|
|920330|Pusty nagłówek agenta użytkownika|
|920340|Żądanie zawierające zawartość, ale brak nagłówka Content-Type|
|920350|Nagłówek hosta jest numerycznym adresem IP|
|920380|Zbyt wiele argumentów w żądaniu|
|920360|Zbyt długa nazwa argumentu|
|920370|Zbyt długa wartość argumentu|
|920390|Przekroczono łączny rozmiar argumentów|
|920400|Rozmiar przekazanego pliku jest zbyt duży|
|920410|Łączny rozmiar przekazanych plików jest zbyt duży|
|920420|Typ zawartości żądania nie jest dozwolony przez zasady|
|920430|Wersja protokołu HTTP nie jest dozwolona przez zasady|
|920440|Rozszerzenie pliku adresu URL jest ograniczone przez zasady|
|920450|Nagłówek HTTP jest ograniczony przez zasady (% @ {MATCHED_VAR})|
|920200|Zakres = zbyt wiele pól (co najmniej 6)|
|920201|Zakres = zbyt wiele pól dla żądania PDF (35 lub więcej)|
|920230|Wykryto wiele kodowań adresów URL|
|920300|W żądaniu brakuje nagłówka Accept|
|920271|Nieprawidłowy znak w żądaniu (znaki niedrukowalne)|
|920320|Brak nagłówka agenta użytkownika|
|920272|Nieprawidłowy znak w żądaniu (na zewnątrz znaków drukowalnych poniżej ASCII 127)|
|920202|Zakres = zbyt wiele pól dla żądania PDF (co najmniej 6)|
|920273|Nieprawidłowy znak w żądaniu (poza bardzo rygorystycznym zestawem)|
|920274|Nieprawidłowy znak w nagłówkach żądania (poza bardzo rygorystycznym zestawem)|
|920460|Nietypowe znaki ucieczki|

### <a name="crs921"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|ruleId|Opis|
|---|---|
|921100|Atak na żądanie HTTP.|
|921110|Atak na przemycie żądania HTTP|
|921120|Atak dzielenia odpowiedzi HTTP|
|921130|Atak dzielenia odpowiedzi HTTP|
|921140|Atak z iniekcją nagłówka HTTP za pośrednictwem nagłówków|
|921150|Atak z iniekcją nagłówka HTTP za pośrednictwem ładunku (wykryto CR/LF)|
|921160|Atak z iniekcją nagłówka HTTP za pośrednictwem ładunku (wykryto znaki CR/LF i nagłówek-Name)|
|921151|Atak z iniekcją nagłówka HTTP za pośrednictwem ładunku (wykryto CR/LF)|
|921170|Zanieczyszczenia parametrów HTTP|
|921180|Zanieczyszczenia parametrów HTTP (% @ {TX. 1})|

### <a name="crs930"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|ruleId|Opis|
|---|---|
|930100|Atak przechodzenia do lokalizacji (/.. /)|
|930110|Atak przechodzenia do lokalizacji (/.. /)|
|930120|Próba dostępu do pliku systemu operacyjnego|
|930130|Próba dostępu do pliku z ograniczeniami|

### <a name="crs931"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|ruleId|Opis|
|---|---|
|931100|Możliwy atak pliku zdalnego dołączania (RFI) = adres URL przy użyciu adresu IP|
|931110|Możliwe ataki zdalnego dołączania plików (RFI) = wspólna RFI Nazwa parametru używana z ładunkiem w adresie URL|
|931120|Możliwy atak na zdalny dołączenie do pliku (RFI) = ładunek adresu URL używany z końcowym znakiem zapytania (?)|
|931130|Możliwy atak na zdalny dołączenie do pliku (RFI) = poza domenę odwołanie/łącze|

### <a name="crs932"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|ruleId|Opis|
|---|---|
|932120|Zdalne wykonywanie poleceń = znaleziono polecenie programu Windows PowerShell|
|932130|Wykonanie polecenia zdalnego = znaleziono wyrażenie powłoki systemu UNIX|
|932140|Zdalne wykonywanie poleceń = znaleziono polecenie systemu Windows FOR/IF|
|932160|Wykonanie polecenia zdalnego = znaleziono kod powłoki systemu UNIX|
|932170|Wykonanie polecenia zdalnego = Shellshock (CVE-2014-6271)|
|932171|Wykonanie polecenia zdalnego = Shellshock (CVE-2014-6271)|

### <a name="crs933"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|ruleId|Opis|
|---|---|
|933100|Atak z iniekcją PHP = znaleziono tag otwierającego/zamykającego|
|933110|Atak z iniekcją PHP = znaleziono plik skryptu PHP|
|933120|Atak z iniekcją PHP = znaleziono dyrektywę Configuration|
|933130|Atak z iniekcją PHP = znaleziono zmienne|
|933150|Atak z iniekcją PHP = znaleziono nazwę funkcji PHP o wysokim ryzyku|
|933160|Atak z iniekcją PHP = znaleziono wywołanie funkcji PHP o wysokim ryzyku|
|933180|Atak z iniekcją PHP = znaleziono wywołanie funkcji zmiennej|
|933151|Atak z iniekcją PHP = znaleziono nazwę funkcji PHP o średnim ryzyku|
|933131|Atak z iniekcją PHP = znaleziono zmienne|
|933161|Atak z iniekcją PHP = znaleziono wywołanie funkcji PHP o niskiej wartości|
|933111|Atak z iniekcją PHP = znaleziono plik skryptu PHP|

### <a name="crs941"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|ruleId|Opis|
|---|---|
|941100|Atak XSS wykryty za pośrednictwem libinjection|
|941110|Filtr XSS — kategoria 1 = wektor tagów skryptu|
|941130|Filtr XSS — Kategoria 3 = wektor atrybutu|
|941140|Filtr XSS — Kategoria 4 = wektor identyfikatora URI JavaScript|
|941150|Filtr XSS — kategoria 5 = niedozwolone atrybuty HTML|
|941180|Słowa kluczowe lista zablokowanych modułów weryfikacji węzła|
|941190|XSS przy użyciu arkuszy stylów|
|941200|XSS przy użyciu ramek VML|
|941210|XSS przy użyciu zasłoniętego języka JavaScript|
|941220|XSS przy użyciu zasłoniętego skryptu VB|
|941230|XSS przy użyciu tagu "embed"|
|941240|XSS przy użyciu atrybutu "Import" lub "implementacja"|
|941260|XSS przy użyciu tagu "meta"|
|941270|XSS przy użyciu "link" href|
|941280|XSS przy użyciu tagu "Base"|
|941290|XSS przy użyciu tagu "applet"|
|941300|XSS przy użyciu tagu "Object"|
|941310|Filtr XSS z nieprawidłowo sformułowanym kodowaniem ASCII — wykryto atak.|
|941330|Filtry XSS programu IE — wykryto atak.|
|941340|Filtry XSS programu IE — wykryto atak.|
|941350|Kodowanie UTF-7 IE XSS-atak wykryty.|
|941320|Możliwe ataki XSS wykryto procedurę obsługi tagów HTML|

### <a name="crs942"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|ruleId|Opis|
|---|---|
|942100|Atak na wstrzyknięcie kodu SQL został wykryty za pośrednictwem libinjection|
|942110|Atak iniekcji kodu SQL: Wykryto wspólne testowanie iniekcji|
|942130|Atak iniekcji kodu SQL: Wykryto tautology języka SQL.|
|942140|Atak iniekcji SQL = wykryto wspólną nazwę bazy danych|
|942160|Wykrywa testy niewidomych SQLi za pomocą uśpienia () lub testu porównawczego ().|
|942170|Wykrywa testy porównawcze SQL i iniekcje uśpienia, w tym zapytania warunkowe|
|942190|Wykrywa wykonywanie kodu MSSQL i prób zbierania informacji|
|942200|Wykrywa Komentarze MySQL — iniekcje/Space-Obfuscated i zakończenie taktu|
|942230|Wykrywa warunkowe próby iniekcji SQL|
|942260|Wykrywa podstawowe próby obejścia uwierzytelniania SQL 2/3|
|942270|Wyszukiwanie podstawowej iniekcji kodu SQL. Typowy ciąg ataku dla programu MySQL Oracle i innych.|
|942290|Znajdowanie podstawowych prób iniekcji kodu SQL MongoDB|
|942300|Wykrywa Komentarze programu MySQL, warunki i wstrzyknięcia języka r (a)|
|942310|Wykrywa łańcuch prób iniekcji kodu SQL 2/2|
|942320|Wykrywa procedury składowane MySQL i PostgreSQL oraz iniekcje funkcji.|
|942330|Wykrywa badanie klasycznego wstrzykiwania kodu SQL 1/2|
|942340|Wykrywa podstawowe próby obejścia uwierzytelniania SQL 3/3|
|942350|Wykrywa wprowadzanie danych UDF w formacie MySQL oraz inne próby manipulowania danymi/strukturą|
|942360|Wykrywa połączone, podstawowe próby iniekcji SQL i SQLLFI|
|942370|Wykrywa badanie klasycznego wstrzykiwania kodu SQL 2/2|
|942150|Atak iniekcji kodu SQL|
|942410|Atak iniekcji kodu SQL|
|942430|Ograniczone wykrywanie anomalii znaków SQL (args): Przekroczono liczbę znaków specjalnych (12)|
|942440|Wykryto sekwencję komentarzy SQL.|
|942450|Zidentyfikowano kodowanie szesnastkowe języka SQL|
|942251|Wykrywa iniekcje|
|942460|Alert wykrywania anomalii z nieprawidłowym znakiem — powtarzające się znaki niebędące słowami|

### <a name="crs943"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|ruleId|Opis|
|---|---|
|943100|Możliwy atak utrwalania sesji = Ustawianie wartości plików cookie w kodzie HTML|
|943110|Możliwy atak utrwalania sesji = Nazwa parametru SessionID z odwołującym się poza domenę|
|943120|Możliwy atak utrwalania sesji = Nazwa parametru SessionID bez odwołującego się|

# <a name="owasp-229tabowasp2"></a>[OWASP 2.2.9](#tab/owasp2)

## <a name="owasp229"></a>Zestawy reguł

### <a name="crs20"></a>crs_20_protocol_violations

|ruleId|Opis|
|---|---|
|960911|Nieprawidłowy wiersz żądania HTTP|
|981227|Błąd Apache = nieprawidłowy identyfikator URI w żądaniu.|
|960912|Nie można przeanalizować treści żądania.|
|960914|Nieczęściowa weryfikacja treści żądania nie powiodła się|
|960915|Parser wieloczęściowy wykrył możliwe niepasującą granicę.|
|960016|Nagłówek HTTP o długości zawartości nie jest wartością numeryczną.|
|960011|Pobierz lub zamów żądanie z zawartością treści.|
|960012|Żądanie POST nie zawiera nagłówka Content-Length.|
|960902|Nieprawidłowe użycie kodowania tożsamości.|
|960022|Oczekiwano, że nagłówek nie jest dozwolony dla HTTP 1,0.|
|960020|Nagłówek Pragma wymaga nagłówka kontroli pamięci podręcznej dla żądań HTTP/1.1.|
|958291|Zakres = pole istnieje i zaczyna się od 0.|
|958230|Zakres = nieprawidłowa wartość ostatniego bajtu.|
|958295|Znaleziono wiele lub sprzeczne dane nagłówka połączenia.|
|950107|Próba ataku przy użyciu kodowania adresu URL|
|950109|Wykryto wiele kodowań adresów URL|
|950108|Próba ataku przy użyciu kodowania adresu URL|
|950801|Próba ataku przy użyciu kodowania UTF8|
|950116|Próba ataków typu Pełna/połówkowa w formacie Unicode|
|960901|Nieprawidłowy znak w żądaniu|
|960018|Nieprawidłowy znak w żądaniu|

### <a name="crs21"></a>crs_21_protocol_anomalies

|ruleId|Opis|
|---|---|
|960008|W żądaniu brakuje nagłówka hosta|
|960007|Pusty nagłówek hosta|
|960015|W żądaniu brakuje nagłówka Accept|
|960021|Żądanie ma pusty nagłówek Accept|
|960009|W żądaniu brakuje nagłówka agenta użytkownika|
|960006|Pusty nagłówek agenta użytkownika|
|960904|Żądanie zawierające zawartość, ale brak nagłówka Content-Type|
|960017|Nagłówek hosta jest numerycznym adresem IP|

### <a name="crs23"></a>crs_23_request_limits

|ruleId|Opis|
|---|---|
|960209|Zbyt długa nazwa argumentu|
|960208|Zbyt długa wartość argumentu|
|960335|Zbyt wiele argumentów w żądaniu|
|960341|Przekroczono łączny rozmiar argumentów|
|960342|Rozmiar przekazanego pliku jest zbyt duży|
|960343|Łączny rozmiar przekazanych plików jest zbyt duży|

### <a name="crs30"></a>crs_30_http_policy

|ruleId|Opis|
|---|---|
|960032|Metoda nie jest dozwolona przez zasady|
|960010|Typ zawartości żądania nie jest dozwolony przez zasady|
|960034|Wersja protokołu HTTP nie jest dozwolona przez zasady|
|960035|Rozszerzenie pliku adresu URL jest ograniczone przez zasady|
|960038|Nagłówek HTTP jest ograniczony przez zasady|

### <a name="crs35"></a>crs_35_bad_robots

|ruleId|Opis|
|---|---|
|990002|Żądanie oznacza, że skaner zabezpieczeń przeskanował lokację|
|990901|Żądanie oznacza, że skaner zabezpieczeń przeskanował lokację|
|990902|Żądanie oznacza, że skaner zabezpieczeń przeskanował lokację|
|990012|Nieautoryzowany przeszukiwarka witryn sieci Web|

### <a name="crs40"></a>crs_40_generic_attacks

|ruleId|Opis|
|---|---|
|960024|Alert wykrywania anomalii z nieprawidłowym znakiem — powtarzające się znaki niebędące słowami|
|950008|Iniekcja nieudokumentowanych tagów ColdFusion|
|950010|Atak iniekcji LDAP|
|950011|Atak z iniekcją SSI|
|950018|Wykryto adres URL XSS uniwersalnego pliku PDF.|
|950019|Atak z iniekcją poczty e-mail|
|950012|Atak na żądanie HTTP.|
|950910|Atak dzielenia odpowiedzi HTTP|
|950911|Atak dzielenia odpowiedzi HTTP|
|950117|Atak z dołączeniem do pliku zdalnego|
|950118|Atak z dołączeniem do pliku zdalnego|
|950119|Atak z dołączeniem do pliku zdalnego|
|950120|Możliwy atak na zdalny dołączenie do pliku (RFI) = poza domenę odwołanie/łącze|
|981133|Reguła 981133|
|981134|Reguła 981134|
|950009|Atak utrwalania sesji|
|950003|Utrwalanie sesji|
|950000|Utrwalanie sesji|
|950005|Próba dostępu do pliku zdalnego|
|950002|Dostęp do poleceń systemowych|
|950006|Iniekcja poleceń systemowych|
|959151|Atak z iniekcją PHP|
|958976|Atak z iniekcją PHP|
|958977|Atak z iniekcją PHP|

### <a name="crs41sql"></a>crs_41_sql_injection_attacks

|ruleId|Opis|
|---|---|
|981231|Wykryto sekwencję komentarzy SQL.|
|981260|Zidentyfikowano kodowanie szesnastkowe języka SQL|
|981320|Atak iniekcji SQL = wykryto wspólną nazwę bazy danych|
|981300|Reguła 981300|
|981301|Reguła 981301|
|981302|Reguła 981302|
|981303|Reguła 981303|
|981304|Reguła 981304|
|981305|Reguła 981305|
|981306|Reguła 981306|
|981307|Reguła 981307|
|981308|Reguła 981308|
|981309|Reguła 981309|
|981310|Reguła 981310|
|981311|Reguła 981311|
|981312|Reguła 981312|
|981313|Reguła 981313|
|981314|Reguła 981314|
|981315|Reguła 981315|
|981316|Reguła 981316|
|981317|Alert wykrywania anomalii w instrukcji SELECT języka SQL|
|950007|Atak polegający na wstrzyknięciu kodu SQL|
|950001|Atak iniekcji kodu SQL|
|950908|Atak iniekcji kodu SQL.|
|959073|Atak iniekcji kodu SQL|
|981272|Wykrywa testy niewidomych SQLi za pomocą uśpienia () lub testu porównawczego ().|
|981250|Wykrywa testy porównawcze SQL i iniekcje uśpienia, w tym zapytania warunkowe|
|981241|Wykrywa warunkowe próby iniekcji SQL|
|981276|Wyszukiwanie podstawowej iniekcji kodu SQL. Typowy ciąg ataku dla programu MySQL Oracle i innych.|
|981270|Znajdowanie podstawowych prób iniekcji kodu SQL MongoDB|
|981253|Wykrywa procedury składowane MySQL i PostgreSQL oraz iniekcje funkcji.|
|981251|Wykrywa wprowadzanie danych UDF w formacie MySQL oraz inne próby manipulowania danymi/strukturą|

### <a name="crs41xss"></a>crs_41_xss_attacks

|ruleId|Opis|
|---|---|
|973336|Filtr XSS — kategoria 1 = wektor tagów skryptu|
|973338|Filtr XSS — Kategoria 3 = wektor identyfikatora URI JavaScript|
|981136|Reguła 981136|
|981018|Reguła 981018|
|958016|Atak na skrypty między lokacjami (XSS)|
|958414|Atak na skrypty między lokacjami (XSS)|
|958032|Atak na skrypty między lokacjami (XSS)|
|958026|Atak na skrypty między lokacjami (XSS)|
|958027|Atak na skrypty między lokacjami (XSS)|
|958054|Atak na skrypty między lokacjami (XSS)|
|958418|Atak na skrypty między lokacjami (XSS)|
|958034|Atak na skrypty między lokacjami (XSS)|
|958019|Atak na skrypty między lokacjami (XSS)|
|958013|Atak na skrypty między lokacjami (XSS)|
|958408|Atak na skrypty między lokacjami (XSS)|
|958012|Atak na skrypty między lokacjami (XSS)|
|958423|Atak na skrypty między lokacjami (XSS)|
|958002|Atak na skrypty między lokacjami (XSS)|
|958017|Atak na skrypty między lokacjami (XSS)|
|958007|Atak na skrypty między lokacjami (XSS)|
|958047|Atak na skrypty między lokacjami (XSS)|
|958410|Atak na skrypty między lokacjami (XSS)|
|958415|Atak na skrypty między lokacjami (XSS)|
|958022|Atak na skrypty między lokacjami (XSS)|
|958405|Atak na skrypty między lokacjami (XSS)|
|958419|Atak na skrypty między lokacjami (XSS)|
|958028|Atak na skrypty między lokacjami (XSS)|
|958057|Atak na skrypty między lokacjami (XSS)|
|958031|Atak na skrypty między lokacjami (XSS)|
|958006|Atak na skrypty między lokacjami (XSS)|
|958033|Atak na skrypty między lokacjami (XSS)|
|958038|Atak na skrypty między lokacjami (XSS)|
|958409|Atak na skrypty między lokacjami (XSS)|
|958001|Atak na skrypty między lokacjami (XSS)|
|958005|Atak na skrypty między lokacjami (XSS)|
|958404|Atak na skrypty między lokacjami (XSS)|
|958023|Atak na skrypty między lokacjami (XSS)|
|958010|Atak na skrypty między lokacjami (XSS)|
|958411|Atak na skrypty między lokacjami (XSS)|
|958422|Atak na skrypty między lokacjami (XSS)|
|958036|Atak na skrypty między lokacjami (XSS)|
|958000|Atak na skrypty między lokacjami (XSS)|
|958018|Atak na skrypty między lokacjami (XSS)|
|958406|Atak na skrypty między lokacjami (XSS)|
|958040|Atak na skrypty między lokacjami (XSS)|
|958052|Atak na skrypty między lokacjami (XSS)|
|958037|Atak na skrypty między lokacjami (XSS)|
|958049|Atak na skrypty między lokacjami (XSS)|
|958030|Atak na skrypty między lokacjami (XSS)|
|958041|Atak na skrypty między lokacjami (XSS)|
|958416|Atak na skrypty między lokacjami (XSS)|
|958024|Atak na skrypty między lokacjami (XSS)|
|958059|Atak na skrypty między lokacjami (XSS)|
|958417|Atak na skrypty między lokacjami (XSS)|
|958020|Atak na skrypty między lokacjami (XSS)|
|958045|Atak na skrypty między lokacjami (XSS)|
|958004|Atak na skrypty między lokacjami (XSS)|
|958421|Atak na skrypty między lokacjami (XSS)|
|958009|Atak na skrypty między lokacjami (XSS)|
|958025|Atak na skrypty między lokacjami (XSS)|
|958413|Atak na skrypty między lokacjami (XSS)|
|958051|Atak na skrypty między lokacjami (XSS)|
|958420|Atak na skrypty między lokacjami (XSS)|
|958407|Atak na skrypty między lokacjami (XSS)|
|958056|Atak na skrypty między lokacjami (XSS)|
|958011|Atak na skrypty między lokacjami (XSS)|
|958412|Atak na skrypty między lokacjami (XSS)|
|958008|Atak na skrypty między lokacjami (XSS)|
|958046|Atak na skrypty między lokacjami (XSS)|
|958039|Atak na skrypty między lokacjami (XSS)|
|958003|Atak na skrypty między lokacjami (XSS)|
|973300|Możliwe ataki XSS wykryto procedurę obsługi tagów HTML|
|973301|Wykryto atak typu XSS|
|973302|Wykryto atak typu XSS|
|973303|Wykryto atak typu XSS|
|973304|Wykryto atak typu XSS|
|973305|Wykryto atak typu XSS|
|973306|Wykryto atak typu XSS|
|973307|Wykryto atak typu XSS|
|973308|Wykryto atak typu XSS|
|973309|Wykryto atak typu XSS|
|973311|Wykryto atak typu XSS|
|973313|Wykryto atak typu XSS|
|973314|Wykryto atak typu XSS|
|973331|Filtry XSS programu IE — wykryto atak.|
|973315|Filtry XSS programu IE — wykryto atak.|
|973330|Filtry XSS programu IE — wykryto atak.|
|973327|Filtry XSS programu IE — wykryto atak.|
|973326|Filtry XSS programu IE — wykryto atak.|
|973346|Filtry XSS programu IE — wykryto atak.|
|973345|Filtry XSS programu IE — wykryto atak.|
|973324|Filtry XSS programu IE — wykryto atak.|
|973323|Filtry XSS programu IE — wykryto atak.|
|973348|Filtry XSS programu IE — wykryto atak.|
|973321|Filtry XSS programu IE — wykryto atak.|
|973320|Filtry XSS programu IE — wykryto atak.|
|973318|Filtry XSS programu IE — wykryto atak.|
|973317|Filtry XSS programu IE — wykryto atak.|
|973329|Filtry XSS programu IE — wykryto atak.|
|973328|Filtry XSS programu IE — wykryto atak.|

### <a name="crs42"></a>crs_42_tight_security

|ruleId|Opis|
|---|---|
|950103|Atak z przechodzeniem ścieżki|

### <a name="crs45"></a>crs_45_trojans

|ruleId|Opis|
|---|---|
|950110|Dostęp tylne wejście|
|950921|Dostęp tylne wejście|
|950922|Dostęp tylne wejście|

---

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak wyłączyć reguły WAF: [Dostosuj reguły WAF](application-gateway-customize-waf-rules-portal.md)
