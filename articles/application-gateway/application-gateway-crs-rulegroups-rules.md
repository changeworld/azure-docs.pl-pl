---
title: Azure Application Gateway zapory aplikacji sieci web CRS reguły grup i reguł
description: Ta strona zawiera informacje dotyczące grup reguł CRS zapory aplikacji sieci web i zasady.
documentationcenter: na
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 4/16/2018
ms.author: victorh
ms.openlocfilehash: 5ba291eaa93a48e3aadc11b1f0f7b48b01683b07
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="list-of-web-application-firewall-crs-rule-groups-and-rules-offered"></a>Oferowana listy grup reguł CRS zapory aplikacji sieci web i reguł

Zapora aplikacji sieci web dla aplikacji bramy (WAF) chroni aplikacji sieci web ze znanych luk i luk w zabezpieczeniach. Można to zrobić za pomocą reguł zdefiniowanych w oparciu zestawów reguł core OWASP 2.2.9 lub 3.0. Te reguły można wyłączyć na podstawie przez reguły. Ten artykuł zawiera bieżące reguły i oferowane zestawów reguł.

Następujące tabele są zasady grupy i reguł, które są dostępne podczas korzystania z bramy aplikacji z zapory aplikacji sieci web.  Każda tabela dotyczy znaleziono w grupie reguły dla określonej wersji CRS reguł.

## <a name="owasp30"></a> OWASP_3.0


### <a name="crs911"></a> <p x-ms-format-detection="none">ŻĄDANIE 911 — METODA WYMUSZANIA</p>

|RuleId|Opis|
|---|---|
|911011|Reguła 911011|
|911012|Reguła 911012|
|911100|Metoda nie jest dozwolone przez zasady|
|911013|Reguła 911013|
|911014|Reguła 911014|
|911015|Reguła 911015|
|911016|Reguła 911016|
|911017|Reguła 911017|
|911018|Reguła 911018|


### <a name="crs913"></a> <p x-ms-format-detection="none">ŻĄDANIE 913-SKANERA WYKRYWANIA</p>

|RuleId|Opis|
|---|---|
|913011|Reguła 913011|
|913012|Reguła 913012|
|913100|Znaleziono agenta użytkownika, skojarzone z skanera zabezpieczeń|
|913110|Znaleziono nagłówek żądania skojarzonego z skanera zabezpieczeń|
|913120|Znaleziono żądania/argument pliku skojarzone z skanera zabezpieczeń|
|913013|Reguła 913013|
|913014|Reguła 913014|
|913101|Znaleziono agenta użytkownika, skojarzone z klienta HTTP skryptów ogólny|
|913102|Znaleziono agenta użytkownika, skojarzone z robot przeszukiwarki web|
|913015|Reguła 913015|
|913016|Reguła 913016|
|913017|Reguła 913017|
|913018|Reguła 913018|

### <a name="crs920"></a> <p x-ms-format-detection="none">ŻĄDANIE 920-PROTOCOL-WYMUSZANIA</p>

|RuleId|Opis|
|---|---|
|920011|Reguła 920011|
|920012|Reguła 920012|
|920100|Nieprawidłowe żądanie HTTP wiersza|
|920130|Nie można przeanalizować treści żądania.|
|920140|Wieloczęściowego żądanie treści nie powiodło się ścisła Weryfikacja = PE %@{REQBODY_PROCESSOR_ERROR} BQ %@{MULTIPART_BOUNDARY_QUOTED} BW %@{MULTIPART_BOUNDARY_WHITESPACE} DB %@{MULTIPART_DATA_BEFORE} DA %@{MULTIPART_DATA_AFTER} HF %@{MULTIPART_HEADER_FOLDING} LF %@{MULTIPART_LF_LINE} SM %@{MULTIPART_SEMICOLON_MISSING} IQ %@{MULTIPART_INVALID_QUOTING} %@{MULTIPART_INVALID_HEADER_FOLDING Umożliwiają} %@{MULTIPART_FILE_LIMIT_EXCEEDED Finlandia}|
|920160|Nagłówek Content-Length protokołu HTTP nie jest liczbą.|
|920170|GET lub HEAD żądania z zawartości treści.|
|920180|Brak nagłówka Content-Length wysłanie żądania POST.|
|920190|Zakres = nieprawidłowy bajt ostatni.|
|920210|Powodujące konflikt/wielu połączeń danych nagłówka znaleziono.|
|920220|Próba ataku nadużycia kodowania adresu URL|
|920240|Próba ataku nadużycia kodowania adresu URL|
|920250|UTF8 Kodowanie próby ataku nadużyć|
|920260|Próba ataku nadużycia szerokość pełnej/połowie Unicode|
|920270|Nieprawidłowy znak w żądaniu (znak null)|
|920280|Żądanie ma nagłówek hosta|
|920290|Nagłówek hosta pusty|
|920310|Żądanie ma pustą nagłówek Accept|
|920311|Żądanie ma pustą nagłówek Accept|
|920330|Nagłówek Agent użytkownika pusty|
|920340|Żądanie zawierające zawartości, ale nagłówek Content-Type Brak|
|920350|Nagłówek hosta jest adresem IP numeryczne|
|920380|Za dużo argumentów w żądaniu|
|920360|Zbyt długa nazwa argumentu|
|920370|Wartość argumentu jest zbyt długa|
|920390|Przekroczono rozmiar całkowitą argumentów|
|920400|Rozmiar przekazanego pliku jest za duży|
|920410|Całkowita liczba przekazanych plików jest zbyt duży|
|920420|Typ zawartości żądania nie jest dozwolone przez zasady|
|920430|Wersji protokołu HTTP nie jest dozwolone przez zasady|
|920440|Rozszerzenie pliku adres URL jest ograniczona przez zasady|
|920450|Nagłówek HTTP jest ograniczona przez zasady (%@{MATCHED_VAR})|
|920013|Reguła 920013|
|920014|Reguła 920014|
|920200|Zakres = zbyt wiele pól (6 lub więcej)|
|920201|Zakres = zbyt wiele pól dla plików pdf wniosek (35 lub więcej)|
|920230|Wykryto wiele kodowania adresów URL|
|920300|Żądanie ma nagłówek Accept|
|920271|Nieprawidłowy znak w żądaniu (z systemem innym niż drukowalne znaki)|
|920320|Brak nagłówka agenta użytkownika|
|920015|Reguła 920015|
|920016|Reguła 920016|
|920272|Nieprawidłowy znak w żądaniu (poza drukowalne znaki poniżej ascii 127)|
|920017|Reguła 920017|
|920018|Reguła 920018|
|920202|Zakres = zbyt wiele pól dla plików pdf wniosek (6 lub więcej)|
|920273|Nieprawidłowy znak w żądaniu (poza bardzo rygorystyczny zestaw)|
|920274|Nieprawidłowy znak w nagłówkach żądania (poza bardzo rygorystyczny zestaw)|
|920460|Reguła 920460|

### <a name="crs921"></a> <p x-ms-format-detection="none">ŻĄDANIA 921-PROTOCOL-ATAKÓW</p>

|RuleId|Opis|
|---|---|
|921011|Reguła 921011|
|921012|Reguła 921012|
|921100|Żądanie HTTP Smuggling ataku.|
|921110|Atak przemycie żądania HTTP|
|921120|Atak dzielenia odpowiedzi HTTP|
|921130|Atak dzielenia odpowiedzi HTTP|
|921140|Ataku polegającego na iniekcji nagłówka HTTP za pośrednictwem nagłówki|
|921150|Ataku polegającego na iniekcji nagłówka HTTP za pośrednictwem ładunku (Wykryto CR/LF)|
|921160|Ataku polegającego na iniekcji nagłówka HTTP za pośrednictwem ładunku (CR/LF i wykryto nazwę nagłówka)|
|921013|Reguła 921013|
|921014|Reguła 921014|
|921151|Ataku polegającego na iniekcji nagłówka HTTP za pośrednictwem ładunku (Wykryto CR/LF)|
|921015|Reguła 921015|
|921016|Reguła 921016|
|921170|Reguła 921170|
|921180|Zanieczyszczenie parametru HTTP (% @{TX.1})|
|921017|Reguła 921017|
|921018|Reguła 921018|

### <a name="crs930"></a> <p x-ms-format-detection="none">ŻĄDANIE-930-APLIKACJI ATAKU LFI</p>

|RuleId|Opis|
|---|---|
|930011|Reguła 930011|
|930012|Reguła 930012|
|930100|Ścieżka przechodzenie ataku (/... /)|
|930110|Ścieżka przechodzenie ataku (/... /)|
|930120|Próba dostępu do pliku systemu operacyjnego|
|930130|Próba dostępu do pliku ograniczone|
|930013|Reguła 930013|
|930014|Reguła 930014|
|930015|Reguła 930015|
|930016|Reguła 930016|
|930017|Reguła 930017|
|930018|Reguła 930018|

### <a name="crs931"></a> <p x-ms-format-detection="none">ŻĄDANIE-931-APLIKACJI ATAKU RFI</p>

|RuleId|Opis|
|---|---|
|931011|Reguła 931011|
|931012|Reguła 931012|
|931100|Możliwości ataku dołączenie pliku zdalnego (RFI) = parametr adresu URL przy użyciu adresu IP|
|931110|Możliwości ataku dołączenie pliku zdalnego (RFI) = RFI narażone parametru Nazwa pospolita używane w/URL ładunku|
|931120|Możliwe pliku zdalnego ataku włączenia (RFI) = adres URL ładunku używane w/kończących pytanie znak (?)|
|931013|Reguła 931013|
|931014|Reguła 931014|
|931130|Możliwe pliku zdalnego ataku włączenia (RFI) = łącze odwołania poza domeny|
|931015|Reguła 931015|
|931016|Reguła 931016|
|931017|Reguła 931017|
|931018|Reguła 931018|

### <a name="crs932"></a> <p x-ms-format-detection="none">ŻĄDANIE-932-APLIKACJI ATAKU RZ</p>

|RuleId|Opis|
|---|---|
|932011|Reguła 932011|
|932012|Reguła 932012|
|932120|Wykonanie polecenia zdalnego = znaleziono polecenia programu PowerShell systemu Windows|
|932130|Wykonanie polecenia zdalnego = znaleziono wyrażenia powłoki systemu Unix|
|932140|Wykonanie polecenia zdalnego = systemu Windows, jeśli znaleziono polecenia /|
|932160|Wykonanie polecenia zdalnego = znaleziono kodu powłoki systemu Unix|
|932170|Wykonanie polecenia zdalnego = Shellshock (CVE-2014-6271)|
|932171|Wykonanie polecenia zdalnego = Shellshock (CVE-2014-6271)|
|932013|Reguła 932013|
|932014|Reguła 932014|
|932015|Reguła 932015|
|932016|Reguła 932016|
|932017|Reguła 932017|
|932018|Reguła 932018|

### <a name="crs933"></a> <p x-ms-format-detection="none">ŻĄDANIE-933-APLIKACJI ATAKU PHP</p>

|RuleId|Opis|
|---|---|
|933011|Reguła 933011|
|933012|Reguła 933012|
|933100|Ataku polegającego na iniekcji PHP = znaleziono tagu otwierania i zamykania|
|933110|Ataku polegającego na iniekcji PHP = przekazywanie pliku skryptu języka PHP znaleziono|
|933120|Ataku polegającego na iniekcji PHP = konfiguracji znaleziono — dyrektywa|
|933130|Ataku polegającego na iniekcji PHP = zmienne|
|933150|Ataku polegającego na iniekcji PHP = nazwa funkcji PHP o wysokim ryzyku, znaleziono|
|933160|Ataku polegającego na iniekcji PHP = znaleziono wywołanie funkcji PHP o wysokim ryzyku|
|933180|Ataku polegającego na iniekcji PHP = wywołanie funkcji zmiennej znaleziono|
|933013|Reguła 933013|
|933014|Reguła 933014|
|933151|Ataku polegającego na iniekcji PHP = można odnaleźć nazwy funkcji PHP średniego ryzyka|
|933015|Reguła 933015|
|933016|Reguła 933016|
|933131|Ataku polegającego na iniekcji PHP = zmienne|
|933161|Ataku polegającego na iniekcji PHP = znaleziono wywołanie funkcji PHP niskiej wartości|
|933111|Ataku polegającego na iniekcji PHP = przekazywanie pliku skryptu języka PHP znaleziono|
|933017|Reguła 933017|
|933018|Reguła 933018|

### <a name="crs941"></a> <p x-ms-format-detection="none">ŻĄDANIE-941-APLIKACJI ATAKU XSS</p>

|RuleId|Opis|
|---|---|
|941011|Reguła 941011|
|941012|Reguła 941012|
|941100|XSS ataku wykryte za pomocą libinjection|
|941110|Filtr XSS - kategorii 1 = wektor Tag skryptu|
|941130|Filtr XSS - kategorii 3 = wektor atrybutu|
|941140|Filtr XSS - kategorii 4 = wektor Javascript URI|
|941150|Filtr XSS - kategorii 5 = atrybuty HTML niedozwolone|
|941180|Słowa kluczowe lista zablokowanych modułów weryfikacji węzła|
|941190|Filtry IE XSS - wykryto ataku.|
|941200|Filtry IE XSS - wykryto ataku.|
|941210|Filtry IE XSS - wykryto ataku.|
|941220|Filtry IE XSS - wykryto ataku.|
|941230|Filtry IE XSS - wykryto ataku.|
|941240|Filtry IE XSS - wykryto ataku.|
|941260|Filtry IE XSS - wykryto ataku.|
|941270|Filtry IE XSS - wykryto ataku.|
|941280|Filtry IE XSS - wykryto ataku.|
|941290|Filtry IE XSS - wykryto ataku.|
|941300|Filtry IE XSS - wykryto ataku.|
|941310|US ASCII źle sformułowane kodowania Filtr XSS - wykryto ataku.|
|941350|UTF-7 kodowania IE XSS - wykryto ataku.|
|941013|Reguła 941013|
|941014|Reguła 941014|
|941320|Wykryto możliwe XSS ataku - obsługi tagu HTML|
|941015|Reguła 941015|
|941016|Reguła 941016|
|941017|Reguła 941017|
|941018|Reguła 941018|

### <a name="crs942"></a> <p x-ms-format-detection="none">ŻĄDANIE-942-APLIKACJI ATAKU SQLI</p>

|RuleId|Opis|
|---|---|
|942011|Reguła 942011|
|942012|Reguła 942012|
|942100|Wykryto ataku w programie SQL iniekcji za pośrednictwem libinjection|
|942140|Ataku polegającego na iniekcji SQL = wspólnej nazwy bazy danych wykryto|
|942160|Wykrywa ukryta sqli testy przy użyciu sleep() lub benchmark().|
|942170|Wykrywa prób iniekcji SQL testu porównawczego i uśpienia warunkowy zapytania w tym|
|942230|Wykrywa warunkowego prób iniekcji kodu SQL|
|942270|Trwa wyszukiwanie iniekcja kodu sql podstawowe. Typowe ciąg ataku dla mysql oracle i inne.|
|942290|Odnajduje podstawowe SQL bazy danych MongoDB prób iniekcji kodu|
|942320|Wykrywa, MySQL, PostgreSQL i przechowywane procedury/funkcji iniekcji|
|942350|Wykrywa iniekcji MySQL UDF i manipulowania nimi inne struktury danych/prób|
|942013|Reguła 942013|
|942014|Reguła 942014|
|942150|Ataku polegającego na iniekcji SQL|
|942410|Ataku polegającego na iniekcji SQL|
|942440|Wykryto sekwencji Komentarz SQL.|
|942450|SQL szesnastkowych kodowanie określone|
|942015|Reguła 942015|
|942016|Reguła 942016|
|942251|Wykrywa iniekcji HAVING|
|942460|Alert wykrycia anomalii meta-znak - powtarzające się znaki-Word|
|942017|Reguła 942017|
|942018|Reguła 942018|

### <a name="crs943"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId|Opis|
|---|---|
|943011|Reguła 943011|
|943012|Reguła 943012|
|943100|Możliwe sesji utrwalania ataku = ustawienie wartości plików Cookie w formacie HTML|
|943110|Możliwe sesji utrwalania ataku = Nazwa parametru identyfikatora sesji z odwołania poza domeny|
|943120|Możliwe sesji utrwalania ataku = Nazwa parametru identyfikatora sesji z nie odwołania|
|943013|Reguła 943013|
|943014|Reguła 943014|
|943015|Reguła 943015|
|943016|Reguła 943016|
|943017|Reguła 943017|
|943018|Reguła 943018|

##<a name="owasp229"></a> OWASP_2.2.9

### <a name="crs20"></a> crs_20_protocol_violations

|RuleId|Opis|
|---|---|
|960911|Nieprawidłowe żądanie HTTP wiersza|
|981227|Błąd Apache = nieprawidłowy identyfikator URI żądania.|
|960912|Nie można przeanalizować treści żądania.|
|960914|Wieloczęściowego żądanie treści nie powiodło się ścisła Weryfikacja = PE %@{REQBODY_PROCESSOR_ERROR} BQ %@{MULTIPART_BOUNDARY_QUOTED} BW %@{MULTIPART_BOUNDARY_WHITESPACE} DB %@{MULTIPART_DATA_BEFORE} DA %@{MULTIPART_DATA_AFTER} HF %@{MULTIPART_HEADER_FOLDING} LF %@{MULTIPART_LF_LINE} SM %@{MULTIPART_SEMICOLON_MISSING} IQ %@{MULTIPART_INVALID_QUOTING} %@{MULTIPART_INVALID_HEADER_FOLDING Umożliwiają} %@{MULTIPART_FILE_LIMIT_EXCEEDED Finlandia}|
|960915|Parser wieloczęściowej wiadomości wykryto niedopasowane granicę możliwe.|
|960016|Nagłówek Content-Length protokołu HTTP nie jest liczbą.|
|960011|GET lub HEAD żądania z zawartości treści.|
|960012|Brak nagłówka Content-Length wysłanie żądania POST.|
|960902|Nieprawidłowe użycie kodowania tożsamości.|
|960022|Oczekiwane nagłówka nie jest dozwolone dla protokołu HTTP 1.0.|
|960020|Nagłówek pragma wymaga nagłówek Cache-Control dla żądania HTTP/1.1.|
|958291|Zakres = pole istnieje i rozpoczyna się od 0.|
|958230|Zakres = nieprawidłowy bajt ostatni.|
|958295|Powodujące konflikt/wielu połączeń danych nagłówka znaleziono.|
|950107|Próba ataku nadużycia kodowania adresu URL|
|950109|Wykryto wiele kodowania adresów URL|
|950108|Próba ataku nadużycia kodowania adresu URL|
|950801|UTF8 Kodowanie próby ataku nadużyć|
|950116|Próba ataku nadużycia szerokość pełnej/połowie Unicode|
|960901|Nieprawidłowy znak w żądaniu|
|960018|Nieprawidłowy znak w żądaniu|

### <a name="crs21"></a> crs_21_protocol_anomalies

|RuleId|Opis|
|---|---|
|960008|Żądanie ma nagłówek hosta|
|960007|Nagłówek hosta pusty|
|960015|Żądanie ma nagłówek Accept|
|960021|Żądanie ma pustą nagłówek Accept|
|960009|Brak nagłówka agenta użytkownika żądania|
|960006|Nagłówek Agent użytkownika pusty|
|960904|Żądanie zawierające zawartości, ale nagłówek Content-Type Brak|
|960017|Nagłówek hosta jest adresem IP numeryczne|

### <a name="crs23"></a> crs_23_request_limits

|RuleId|Opis|
|---|---|
|960209|Zbyt długa nazwa argumentu|
|960208|Wartość argumentu jest zbyt długa|
|960335|Za dużo argumentów w żądaniu|
|960341|Przekroczono rozmiar całkowitą argumentów|
|960342|Rozmiar przekazanego pliku jest za duży|
|960343|Całkowita liczba przekazanych plików jest zbyt duży|

### <a name="crs30"></a> crs_30_http_policy

|RuleId|Opis|
|---|---|
|960032|Metoda nie jest dozwolone przez zasady|
|960010|Typ zawartości żądania nie jest dozwolone przez zasady|
|960034|Wersji protokołu HTTP nie jest dozwolone przez zasady|
|960035|Rozszerzenie pliku adres URL jest ograniczona przez zasady|
|960038|Nagłówek HTTP jest ograniczona przez zasady|

### <a name="crs35"></a> crs_35_bad_robots

|RuleId|Opis|
|---|---|
|990002|Wskazuje żądanie program skanera zabezpieczeń lokacji|
|990901|Wskazuje żądanie program skanera zabezpieczeń lokacji|
|990902|Wskazuje żądanie program skanera zabezpieczeń lokacji|
|990012|Nieautoryzowany przeszukiwarki witryny sieci web|

### <a name="crs40"></a> crs_40_generic_attacks

|RuleId|Opis|
|---|---|
|960024|Alert wykrycia anomalii meta-znak - powtarzające się znaki-Word|
|950008|Iniekcji Nieudokumentowanej ColdFusion tagów|
|950010|Ataku polegającego na iniekcji LDAP|
|950011|SSI iniekcji ataków|
|950018|Wykryto adres URL uniwersalnych XSS PDF.|
|950019|Ataku polegającego na iniekcji poczty e-mail|
|950012|Żądanie HTTP Smuggling ataku.|
|950910|Atak dzielenia odpowiedzi HTTP|
|950911|Atak dzielenia odpowiedzi HTTP|
|950117|Atak dołączenie pliku zdalnego|
|950118|Atak dołączenie pliku zdalnego|
|950119|Atak dołączenie pliku zdalnego|
|950120|Możliwe pliku zdalnego ataku włączenia (RFI) = łącze odwołania poza domeny|
|981133|Reguła 981133|
|981134|Reguła 981134|
|950009|Atak utrwalania sesji|
|950003|Utrwalanie sesji|
|950000|Utrwalanie sesji|
|950005|Próba dostępu do pliku zdalnego|
|950002|Dostęp do poleceń systemu|
|950006|Uruchomienie polecenia systemu|
|959151|Ataku polegającego na iniekcji PHP|
|958976|Ataku polegającego na iniekcji PHP|
|958977|Ataku polegającego na iniekcji PHP|

### <a name="crs41sql"></a> crs_41_sql_injection_attacks

|RuleId|Opis|
|---|---|
|981231|Wykryto sekwencji Komentarz SQL.|
|981260|SQL szesnastkowych kodowanie określone|
|981320|Ataku polegającego na iniekcji SQL = wspólnej nazwy bazy danych wykryto|
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
|981317|Alert wykrycia anomalii w usłudze SQL w instrukcji SELECT|
|950007|Ataku polegającego na iniekcji ukryta SQL|
|950001|Ataku polegającego na iniekcji SQL|
|950908|Ataku polegającego na iniekcji SQL.|
|959073|Ataku polegającego na iniekcji SQL|
|981272|Wykrywa ukryta sqli testy przy użyciu sleep() lub benchmark().|
|981250|Wykrywa prób iniekcji SQL testu porównawczego i uśpienia warunkowy zapytania w tym|
|981241|Wykrywa warunkowego prób iniekcji kodu SQL|
|981276|Trwa wyszukiwanie iniekcja kodu sql podstawowe. Typowe ciąg ataku dla mysql oracle i inne.|
|981270|Odnajduje podstawowe SQL bazy danych MongoDB prób iniekcji kodu|
|981253|Wykrywa, MySQL, PostgreSQL i przechowywane procedury/funkcji iniekcji|
|981251|Wykrywa iniekcji MySQL UDF i manipulowania nimi inne struktury danych/prób|

### <a name="crs41xss"></a> crs_41_xss_attacks

|RuleId|Opis|
|---|---|
|973336|Filtr XSS - kategorii 1 = wektor Tag skryptu|
|973338|Filtr XSS - kategorii 3 = wektor Javascript URI|
|981136|Reguła 981136|
|981018|Reguła 981018|
|958016|Atak skryptów między witrynami (XSS)|
|958414|Atak skryptów między witrynami (XSS)|
|958032|Atak skryptów między witrynami (XSS)|
|958026|Atak skryptów między witrynami (XSS)|
|958027|Atak skryptów między witrynami (XSS)|
|958054|Atak skryptów między witrynami (XSS)|
|958418|Atak skryptów między witrynami (XSS)|
|958034|Atak skryptów między witrynami (XSS)|
|958019|Atak skryptów między witrynami (XSS)|
|958013|Atak skryptów między witrynami (XSS)|
|958408|Atak skryptów między witrynami (XSS)|
|958012|Atak skryptów między witrynami (XSS)|
|958423|Atak skryptów między witrynami (XSS)|
|958002|Atak skryptów między witrynami (XSS)|
|958017|Atak skryptów między witrynami (XSS)|
|958007|Atak skryptów między witrynami (XSS)|
|958047|Atak skryptów między witrynami (XSS)|
|958410|Atak skryptów między witrynami (XSS)|
|958415|Atak skryptów między witrynami (XSS)|
|958022|Atak skryptów między witrynami (XSS)|
|958405|Atak skryptów między witrynami (XSS)|
|958419|Atak skryptów między witrynami (XSS)|
|958028|Atak skryptów między witrynami (XSS)|
|958057|Atak skryptów między witrynami (XSS)|
|958031|Atak skryptów między witrynami (XSS)|
|958006|Atak skryptów między witrynami (XSS)|
|958033|Atak skryptów między witrynami (XSS)|
|958038|Atak skryptów między witrynami (XSS)|
|958409|Atak skryptów między witrynami (XSS)|
|958001|Atak skryptów między witrynami (XSS)|
|958005|Atak skryptów między witrynami (XSS)|
|958404|Atak skryptów między witrynami (XSS)|
|958023|Atak skryptów między witrynami (XSS)|
|958010|Atak skryptów między witrynami (XSS)|
|958411|Atak skryptów między witrynami (XSS)|
|958422|Atak skryptów między witrynami (XSS)|
|958036|Atak skryptów między witrynami (XSS)|
|958000|Atak skryptów między witrynami (XSS)|
|958018|Atak skryptów między witrynami (XSS)|
|958406|Atak skryptów między witrynami (XSS)|
|958040|Atak skryptów między witrynami (XSS)|
|958052|Atak skryptów między witrynami (XSS)|
|958037|Atak skryptów między witrynami (XSS)|
|958049|Atak skryptów między witrynami (XSS)|
|958030|Atak skryptów między witrynami (XSS)|
|958041|Atak skryptów między witrynami (XSS)|
|958416|Atak skryptów między witrynami (XSS)|
|958024|Atak skryptów między witrynami (XSS)|
|958059|Atak skryptów między witrynami (XSS)|
|958417|Atak skryptów między witrynami (XSS)|
|958020|Atak skryptów między witrynami (XSS)|
|958045|Atak skryptów między witrynami (XSS)|
|958004|Atak skryptów między witrynami (XSS)|
|958421|Atak skryptów między witrynami (XSS)|
|958009|Atak skryptów między witrynami (XSS)|
|958025|Atak skryptów między witrynami (XSS)|
|958413|Atak skryptów między witrynami (XSS)|
|958051|Atak skryptów między witrynami (XSS)|
|958420|Atak skryptów między witrynami (XSS)|
|958407|Atak skryptów między witrynami (XSS)|
|958056|Atak skryptów między witrynami (XSS)|
|958011|Atak skryptów między witrynami (XSS)|
|958412|Atak skryptów między witrynami (XSS)|
|958008|Atak skryptów między witrynami (XSS)|
|958046|Atak skryptów między witrynami (XSS)|
|958039|Atak skryptów między witrynami (XSS)|
|958003|Atak skryptów między witrynami (XSS)|
|973300|Wykryto możliwe XSS ataku - obsługi tagu HTML|
|973301|Wykryto ataku XSS|
|973302|Wykryto ataku XSS|
|973303|Wykryto ataku XSS|
|973304|Wykryto ataku XSS|
|973305|Wykryto ataku XSS|
|973306|Wykryto ataku XSS|
|973307|Wykryto ataku XSS|
|973308|Wykryto ataku XSS|
|973309|Wykryto ataku XSS|
|973311|Wykryto ataku XSS|
|973313|Wykryto ataku XSS|
|973314|Wykryto ataku XSS|
|973331|Filtry IE XSS - wykryto ataku.|
|973315|Filtry IE XSS - wykryto ataku.|
|973330|Filtry IE XSS - wykryto ataku.|
|973327|Filtry IE XSS - wykryto ataku.|
|973326|Filtry IE XSS - wykryto ataku.|
|973346|Filtry IE XSS - wykryto ataku.|
|973345|Filtry IE XSS - wykryto ataku.|
|973324|Filtry IE XSS - wykryto ataku.|
|973323|Filtry IE XSS - wykryto ataku.|
|973348|Filtry IE XSS - wykryto ataku.|
|973321|Filtry IE XSS - wykryto ataku.|
|973320|Filtry IE XSS - wykryto ataku.|
|973318|Filtry IE XSS - wykryto ataku.|
|973317|Filtry IE XSS - wykryto ataku.|
|973329|Filtry IE XSS - wykryto ataku.|
|973328|Filtry IE XSS - wykryto ataku.|

### <a name="crs42"></a> crs_42_tight_security

|RuleId|Opis|
|---|---|
|950103|Ścieżka przechodzenie ataków|

### <a name="crs45"></a> crs_45_trojans

|RuleId|Opis|
|---|---|
|950110|Tylne wejście do systemu|
|950921|Tylne wejście do systemu|
|950922|Tylne wejście do systemu|

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak można wyłączyć reguły zapory aplikacji sieci Web, odwiedzając: [reguły zapory dostosowywania aplikacji sieci Web](application-gateway-customize-waf-rules-portal.md)

[1]: ./media/application-gateway-integration-security-center/figure1.png