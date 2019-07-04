---
title: Usługa Azure CDN from Verizon — Premium funkcje aparatu reguł | Dokumentacja firmy Microsoft
description: Dokumentacja dotycząca usługi Azure CDN from Verizon — Premium funkcje aparatu reguł.
services: cdn
author: mdgattuso
ms.service: cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 7e75a6ffe28aa74ea2fad30bbe2728317712d86b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443490"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-features"></a>Usługa Azure CDN from funkcje aparatu reguł Premium firmy Verizon

W tym artykule przedstawiono szczegółowe opisy dostępnych funkcji dla usługi Azure Content Delivery Network (CDN) [aparat reguł](cdn-verizon-premium-rules-engine.md).

Trzecia część reguły jest funkcja. Funkcja określa typ akcji, która jest stosowana do typu żądania, która jest identyfikowana przez zestaw warunków dopasowania.

## <a name="access-features"></a>Funkcje dostępu

Te funkcje są przeznaczone do kontrolowania dostępu do zawartości.

Name (Nazwa) | Przeznaczenie
-----|--------
[Odmowa dostępu (403)](#deny-access-403) | Określa, czy wszystkie żądania są odrzucane odpowiedź 403 Zabroniony.
[Token uwierzytelniania](#token-auth) | Określa, czy uwierzytelnianie za pomocą tokenu jest stosowany do żądania.
[Kod typu "odmowa" tokenu uwierzytelniania](#token-auth-denial-code) | Określa typ odpowiedzi, która jest zwracana do użytkownika, jeśli żądanie zostanie odrzucone z powodu uwierzytelniania opartego na tokenach.
[Token uwierzytelniania ignorowanie wielkości liter adresu URL](#token-auth-ignore-url-case) | Określa, czy adres URL porównania przez uwierzytelnianie oparte na tokenie uwzględniają wielkość liter.
[Parametr tokenu uwierzytelniania](#token-auth-parameter) | Określa, czy parametr ciągu zapytania uwierzytelniania opartego na tokenach powinny zostać zmienione.

## <a name="caching-features"></a>Funkcje pamięci podręcznej

Te funkcje są przeznaczone do dostosowywania, kiedy i jak zawartość jest buforowana.

Name (Nazwa) | Przeznaczenie
-----|--------
[Parametry przepustowości](#bandwidth-parameters) | Określa, czy parametry ograniczania przepustowości (na przykład ec_rate i ec_prebuf) są aktywne.
[Ograniczanie przepustowości](#bandwidth-throttling) | Ogranicza przepustowość dla odpowiedzi dostarczonych przez punkt Point of presence (POP, POINT).
[Bypass Cache](#bypass-cache) | Określa, czy żądanie powinno Pomiń buforowanie.
[Traktowanie nagłówek Cache-Control](#cache-control-header-treatment) | Kontroluje Generowanie `Cache-Control` nagłówków, POP, gdy funkcja Max-Age zewnętrznych jest aktywny.
[Cache-Key Query String](#cache-key-query-string) | Określa, czy klucz pamięci podręcznej obejmuje, czy nie obejmuje parametry ciągu zapytania skojarzony z żądaniem.
[Napisz ponownie klucz pamięci podręcznej](#cache-key-rewrite) | Ponownie zapisuje klucz pamięci podręcznej skojarzonej z żądaniem.
[Wypełnienie pamięci podręcznej ukończone](#complete-cache-fill) | Określa, co się dzieje podczas żądania skutkuje to Chybienie pamięci podręcznej częściowych w punktu obecności.
[Kompresuj typów plików](#compress-file-types) | Definiuje formatów plików, które są kompresowane na serwerze.
[Max-Age wewnętrzny domyślny](#default-internal-max-age) | Określa domyślny interwał max-age POP do źródła serwera pamięci podręcznej ponownego sprawdzania poprawności.
[Wygasa traktowania nagłówka](#expires-header-treatment) | Kontroluje Generowanie `Expires` nagłówków, POP, gdy funkcja Max-Age zewnętrznych jest aktywny.
[Max-Age zewnętrznych](#external-max-age) | Określa maksymalny wiek interwał ponownego sprawdzania poprawności POP pamięci podręcznej w przeglądarce.
[Wymuszanie wewnętrznych Max-Age.](#force-internal-max-age) | Określa interwał max-age POP do źródła serwera pamięci podręcznej ponownego sprawdzania poprawności.
[Obsługa H.264 (pobierania progresywnego HTTP)](#h264-support-http-progressive-download) | Określa typy H.264 formatów plików, które mogą być używane do strumieniowego przesyłania zawartości.
[Żądanie pamięci podręcznej nie honor](#honor-no-cache-request) | Określa, czy klienta HTTP nie pamięci podręcznej żądania są przekazywane do serwera pochodzenia.
[Ignoruj pochodzenia No-Cache](#ignore-origin-no-cache) | Określa, czy usługa CDN ignoruje niektórych dyrektyw, udostępniana z serwera pochodzenia.
[Ignoruj Unsatisfiable zakresów](#ignore-unsatisfiable-ranges) | Określa odpowiedź, która jest zwracana do klientów, gdy żądanie generuje 416 żądany zakres nie żądania kod stanu.
[Maksymalna liczba wewnętrznych — stare](#internal-max-stale) | Formanty, jak długo późniejsza niż godzina wygaśnięcia normalne trwałego pamięci podręcznej mogą być obsługiwani z punktu POP po punkcie POP nie może przechowywać w pamięci podręcznej zawartości z serwera pochodzenia.
[Udostępnianie częściowe pamięci podręcznej](#partial-cache-sharing) | Określa, czy żądanie może generować częściowo buforowanej zawartości.
[Prevalidate zawartości w pamięci podręcznej](#prevalidate-cached-content) | Określa, czy zawartość z pamięci podręcznej kwalifikują się do początku ponownego sprawdzania poprawności przed jego wygaśnięcia.
[Odśwież Zero bajtów pamięci podręcznej plików](#refresh-zero-byte-cache-files) | Określa, jak żądanie klienta HTTP dla zawartości pamięci podręcznej 0 bajtów jest obsługiwany przez lokalizacji POP.
[Kody stanu podlega buforowaniu, na zestaw](#set-cacheable-status-codes) | Definiuje zestaw kodów stanu, które mogą skutkować zawartości w pamięci podręcznej.
[Stałe dostarczanie zawartości w przypadku błędu](#stale-content-delivery-on-error) | Określa, czy wygasła zawartości w pamięci podręcznej jest dostarczana, gdy wystąpi błąd podczas ponownego sprawdzania poprawności w pamięci podręcznej lub podczas pobierania żądanej zawartości z serwera pochodzenia klienta.
[Starych podczas Revalidate](#stale-while-revalidate) | Zwiększa wydajność, umożliwiając POP obsługiwać nieodświeżeni klienci do zleceniodawcy podczas ponownego sprawdzania poprawności.

## <a name="comment-feature"></a>Funkcja komentarz

Ta funkcja została zaprojektowana podać dodatkowe informacje w obrębie reguły.

Name (Nazwa) | Przeznaczenie
-----|--------
[Komentarz](#comment) | Umożliwia notatkę do dodania w obrębie reguły.

## <a name="header-features"></a>Funkcje nagłówka

Te funkcje są przeznaczone do Dodawanie, modyfikowanie lub usuwanie nagłówków żądania lub odpowiedzi.

Name (Nazwa) | Przeznaczenie
-----|--------
[Nagłówek odpowiedzi wiek](#age-response-header) | Określa, czy nagłówek odpowiedzi wiek znajduje się odpowiedzi wysyłane do zleceniodawcy.
[Debugowanie pamięci podręcznej nagłówki odpowiedzi](#debug-cache-response-headers) | Określa, czy odpowiedź może obejmować nagłówek odpowiedzi X-WE-Debug, który zawiera informacje dotyczące zasad pamięci podręcznej dla żądanego zasobu.
[Modyfikowanie nagłówek żądania klienta](#modify-client-request-header) | Zastępuje, dołącza lub usuwa nagłówek z żądania.
[Modyfikowanie nagłówek odpowiedzi klienta](#modify-client-response-header) | Zastępuje, dołącza lub usuwa nagłówek z odpowiedzi.
[Ustaw niestandardowy nagłówek IP klienta](#set-client-ip-custom-header) | Zezwala na adres IP klienta mają zostać dodane do żądania jako nagłówek żądania niestandardowych.

## <a name="logging-features"></a>Funkcje rejestrowania

Te funkcje pozwalają dostosować dane przechowywane w plikach dziennika raw.

Name (Nazwa) | Przeznaczenie
-----|--------
[Pole dziennika niestandardowego 1](#custom-log-field-1) | Określa format i zawartość, która jest przypisana do pola dziennika niestandardowego w pliku dziennika raw.
[Ciąg zapytania dziennika](#log-query-string) | Określa, czy ciąg zapytania są przechowywane wraz z adresu URL na uzyskiwanie dostępu do dzienników.


<!---
## Optimize

These features determine whether a request will undergo the optimizations provided by Edge Optimizer.

Name | Purpose
-----|--------
Edge Optimizer | Determines whether Edge Optimizer can be applied to a request.
Edge Optimizer – Instantiate Configuration | Instantiates or activates the Edge Optimizer configuration associated with a site.

### Edge Optimizer
**Purpose:** Determines whether Edge Optimizer can be applied to a request.

If this feature has been enabled, then the following criteria must also be met before the request will be processed by Edge Optimizer:

- The requested content must use an edge CNAME URL.
- The edge CNAME referenced in the URL must correspond to a site whose configuration has been activated in a rule.

This feature requires the ADN platform and the Edge Optimizer feature.

Value|Result
-|-
Enabled|Indicates that the request is eligible for Edge Optimizer processing.
Disabled|Restores the default behavior. The default behavior is to deliver content over the ADN platform without any additional processing.

**Default Behavior:** Disabled


### Edge Optimizer - Instantiate Configuration
**Purpose:** Instantiates or activates the Edge Optimizer configuration associated with a site.

This feature requires the ADN platform and the Edge Optimizer feature.

Key information:

- Instantiation of a site configuration is required before requests to the corresponding edge CNAME can be processed by Edge Optimizer.
- This instantiation only needs to be performed a single time per site configuration. A site configuration that has been instantiated will remain in that state until the Edge Optimizer – Instantiate Configuration feature that references it is removed from the rule.
- The instantiation of a site configuration does not mean that all requests to the corresponding edge CNAME will automatically be processed by Edge Optimizer. The Edge Optimizer feature determines whether an individual request will be processed.

If the desired site does not appear in the list, then you should edit its configuration and verify that the Active option has been marked.

**Default Behavior:** Site configurations are inactive by default.
--->

## <a name="origin-features"></a>Funkcje źródła

Te funkcje są przeznaczone do sterowania, jak usługa CDN komunikuje się z serwera pochodzenia.

Name (Nazwa) | Przeznaczenie
-----|--------
[Maksymalna liczba żądań Keep-Alive](#maximum-keep-alive-requests) | Określa maksymalną liczbę żądań połączenia Keep-Alive, po którym jest ono zamknięte.
[Serwer proxy specjalnych nagłówków](#proxy-special-headers) | Definiuje zestaw nagłówków żądań specyficzne dla usługi CDN, które są przekazywane z punktu POP do serwera pochodzenia.

## <a name="specialty-features"></a>Funkcje specjalne

Te funkcje zapewniają zaawansowane funkcje dla użytkowników zaawansowanych.

Name (Nazwa) | Przeznaczenie
-----|--------
[Metody HTTP podlega buforowaniu](#cacheable-http-methods) | Określa zestaw dodatkowych metod HTTP, które mogą być buforowane w sieci.
[Rozmiar treści podlega buforowaniu, na żądanie](#cacheable-request-body-size) | Definiuje wartość progowa określająca, czy mogą być buforowane odpowiedzi WPIS.
[Zmiennej użytkownika](#user-variable) | Tylko do użytku wewnętrznego.

## <a name="url-features"></a>Adres URL funkcji

Te funkcje umożliwiają żądanie, aby być przekierowywany lub przepisane, aby inny adres URL.

Name (Nazwa) | Przeznaczenie
-----|--------
[Wykonaj przekierowania](#follow-redirects) | Określa, czy żądania mogą zostać przekierowane do nazwy hosta, zdefiniowany w nagłówku Location zwróconych przez serwer pochodzenia klienta.
[Adres URL przekierowania](#url-redirect) | Przekierowuje żądania za pośrednictwem nagłówek lokalizacji.
[Ponowne zapisywanie adresów URL](#url-rewrite)  | Ponownie zapisuje adresu URL żądania.

## <a name="azure-cdn-from-verizon-premium-rules-engine-features-reference"></a>Usługa Azure CDN from Premium firmy Verizon odwołanie do funkcji aparatu reguł

---

### <a name="age-response-header"></a>Nagłówek odpowiedzi wiek

**Cel**: Określa, czy nagłówek odpowiedzi wiek znajduje się odpowiedzi wysyłane do zleceniodawcy.

Wartość|Wynik
--|--
Enabled (Włączony) | Nagłówek odpowiedzi wiek znajduje się w odpowiedzi wysyłane do zleceniodawcy.
Wyłączone | Nagłówek odpowiedzi okres ważności jest wykluczony z odpowiedzi wysyłane do zleceniodawcy.

**Domyślne zachowanie**: Wyłączone.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bandwidth-parameters"></a>Parametry przepustowości

**Cel:** Określa, czy parametry ograniczania przepustowości (na przykład ec_rate i ec_prebuf) są aktywne.

Parametry ograniczania przepustowości określają, czy szybkość transferu danych dla żądania klienta są ograniczone do szybkości niestandardowych.

Wartość|Wynik
--|--
Enabled (Włączony)|Umożliwia POP respektować żądania ograniczenia przepustowości.
Wyłączone|Powoduje, że POP zignorować parametry ograniczenia przepustowości. Żądanej zawartości jest zwykle obsługiwany (to znaczy bez ograniczania przepustowości).

**Domyślne zachowanie:** Włączone.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bandwidth-throttling"></a>Ograniczanie przepustowości

**Cel:** Ogranicza przepustowość dla odpowiedzi dostarczonych przez lokalizacji POP.

Oba z następujących opcji, musi być zdefiniowany poprawnie skonfigurować ograniczenie przepustowości.

Opcja|Opis
--|--
W kilobajtach na sekundę|Ustaw tę opcję, aby maksymalnej przepustowości (Kb na sekundę), która może być używana do dostarczania odpowiedzi.
Prebuf sekund|Ustaw tę opcję na czas w sekundach dla punktów obecności, poczekać, aż przepustowości jest ograniczany. Przepustowość nieograniczony okres ten ma na celu uniemożliwić odtwarzacz multimediów problemy przestoje w odtwarzaniu lub buforowania ze względu na ograniczenie przepustowości.

**Domyślne zachowanie:** Wyłączone.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bypass-cache"></a>Pomiń pamięć podręczną

**Cel:** Określa, czy żądanie powinno Pomiń buforowanie.

Wartość|Wynik
--|--
Enabled (Włączony)|Powoduje, że wszystkie żądania przechodzić do serwera pochodzenia, nawet jeśli zawartość wcześniej był buforowany w lokalizacji POP.
Wyłączone|Powoduje, że POP do buforowania zasobów zgodnie z zasadami pamięci podręcznej, zdefiniowane w jego nagłówków odpowiedzi.

**Domyślne zachowanie:**

- **Duże HTTP:** Wyłączone

<!---
- **ADN:** Enabled

--->

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cacheable-http-methods"></a>Metody HTTP podlega buforowaniu

**Cel:** Określa zestaw dodatkowych metod HTTP, które mogą być buforowane w sieci.

Informacje o kluczu:

- Tej funkcji przyjęto założenie, że zawsze powinny być buforowane odpowiedzi GET. W rezultacie metodę GET HTTP nie należy włączyć podczas ustawiania tej funkcji.
- Ta funkcja obsługuje tylko metodę POST HTTP. Włącz buforowanie odpowiedzi POST, ustawiając tę funkcję `POST`.
- Domyślnie tylko żądania, których treść jest mniejszy niż 14 Kb są buforowane. Funkcja podlega buforowaniu, na żądanie treści rozmiar można ustawić rozmiar treści żądania maksymalnej.

**Domyślne zachowanie:** Tylko odpowiedzi GET są buforowane.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cacheable-request-body-size"></a>Rozmiar treści podlega buforowaniu, na żądanie

**Cel:** Definiuje wartość progowa określająca, czy mogą być buforowane odpowiedzi WPIS.

Próg ten jest określany przez określania rozmiaru treść żądania maksymalnej. Żądań, które zawierają większych treści żądania nie są buforowane.

Informacje o kluczu:

- Ta funkcja ma zastosowanie tylko w przypadku, gdy odpowiedzi na WPIS kwalifikują się do buforowania. Aby włączyć buforowanie żądania POST, należy użyć podlega buforowaniu, na funkcji metod HTTP.
- Treść żądania jest brana pod uwagę dla:
    - wartości x--www-form-urlencoded
    - Zapewnienie Unikatowy klucz pamięci podręcznej
- Definiowanie rozmiar treści duża maksymalna żądania może mieć wpływ na wydajność dostarczanie danych.
    - **Zalecana wartość:** 14 Kb
    - **Minimalna wartość:** 1 Kb

**Domyślne zachowanie:** 14 Kb

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-control-header-treatment"></a>Traktowanie nagłówek Cache-Control

**Cel:** Kontroluje Generowanie `Cache-Control` nagłówków, POP, gdy funkcji Max-Age zewnętrznego jest aktywny.

Najprostszym sposobem osiągnięcia tego typu konfiguracją jest umieszczenie zewnętrznych Max-Age i funkcji przetwarzania nagłówka Cache-Control w tej samej instrukcji.

Wartość|Wynik
--|--
Zastąp|Zapewnia, że są wykonywane następujące akcje:<br/> -Zastępuje `Cache-Control` nagłówka wygenerowane przez serwer pochodzenia. <br/>-Dodaje `Cache-Control` nagłówka generowane przez funkcję zewnętrznych Max-Age w odpowiedzi.
Przekazuj|Zapewnia, że `Cache-Control` nagłówka generowane przez funkcję zewnętrznych Max-Age nigdy nie zostanie dodany do odpowiedzi. <br/> Jeśli serwer pochodzenia generuje `Cache-Control` nagłówka, go przechodzi przez użytkownika końcowego. <br/> Jeśli serwer pochodzenia nie `Cache-Control` nagłówka, a następnie ta opcja może spowodować, że nagłówek odpowiedzi nie będzie zawierać `Cache-Control` nagłówka.
Dodawanie, jeśli brak|Jeśli `Cache-Control` z serwera pochodzenia nie otrzymano nagłówka, a następnie ta opcja dodaje `Cache-Control` nagłówka generowane przez funkcję zewnętrznych Max-Age. Ta opcja przydaje się do zapewnienia, że wszystkie zasoby są przypisane `Cache-Control` nagłówka.
Usuń| Ta opcja zapewnia, że `Cache-Control` nagłówka nie jest dołączony do odpowiedzi nagłówek. Jeśli `Cache-Control` nagłówka został już przypisany, a następnie zostanie on usunięty z odpowiedzi nagłówka.

**Domyślne zachowanie:** Zastąpienie.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-key-query-string"></a>Ciąg zapytania klucz pamięci podręcznej

**Cel:** Określa, czy klucz pamięci podręcznej obejmuje, czy nie obejmuje parametry ciągu zapytania skojarzony z żądaniem.

Informacje o kluczu:

- Określ co najmniej jedną nazwę parametru ciągu zapytania i Oddziel poszczególne nazwy parametru z jednego miejsca.
- Ta funkcja określa, czy parametry ciągu zapytania są dołączone lub wykluczone z klucza pamięci podręcznej. Dodatkowe informacje są udostępniane dla każdej opcji w poniższej tabeli.

Typ|Opis
--|--
 Obejmują|  Wskazuje, że każdy określony parametr powinny być uwzględnione w klucz pamięci podręcznej. Unikatowy klucz pamięci podręcznej jest generowany dla każdego żądania, który zawiera unikatową wartość dla parametru ciągu zapytania, zdefiniowane w tej funkcji.
 Uwzględnij wszystkie  |Wskazuje, że Unikatowy klucz pamięci podręcznej jest tworzony dla każdego żądania do elementu zawartości, która zawiera ciąg zapytania unikatowy. Ten typ konfiguracji jest zwykle niezalecane, ponieważ może to prowadzić do niewielkiego odsetka trafień w pamięci podręcznej. Najmniejszą liczbę trafień w pamięci podręcznej zwiększa obciążenie na serwerze źródłowym, ponieważ musi on obsługiwać żądań więcej. Ta konfiguracja jest duplikatem zachowanie buforowania, znane jako "unikatowe pamięci podręcznej" na stronie buforowanie ciągu zapytania.
 Wyklucz | Wskazuje określony parametry są wykluczane klucz pamięci podręcznej. Wszystkie inne parametry ciągu zapytania są objęte klucz pamięci podręcznej.
 Wyklucz wszystkie  |Wskazuje, że wszystkie parametry ciągu zapytania są wykluczone z klucza pamięci podręcznej. Ta konfiguracja duplikuje domyślną "Standardowa pamięć podręczną" buforowania zachowanie na stronie buforowanie ciągu zapytania.  

Aparat reguł pozwala dostosować sposób, w którym buforowanie ciągu zapytania jest zaimplementowana. Na przykład można określić, czy buforowanie ciągu zapytania jest wykonywane tylko w określonych lokalizacjach lub typów plików.

Aby zduplikować zachowanie, na stronie buforowanie ciągu zapytania buforowania ciągu kwerendy "no-cache", należy utworzyć regułę, która zawiera adres URL zapytania z symbolami wieloznacznymi warunek dopasowania i funkcja pomijania pamięci podręcznej. Warunek dopasowania adresu URL zapytania z symbolami wieloznacznymi na znak gwiazdki (*).

>[!IMPORTANT]
> Jeśli token autoryzacji jest włączona dla dowolnej ścieżki dla tego konta, tryb pamięci podręcznej standardu jest jedynym trybem, który może służyć do buforowania ciągu kwerendy. Aby uzyskać więcej informacji, zobacz [Control Azure CDN caching behavior with query strings](cdn-query-string-premium.md) (Sterowanie zachowaniem buforowania usługi CDN za pomocą ciągów zapytań).

#### <a name="sample-scenarios"></a>Przykładowe scenariusze

Następujące przykładowe zastosowanie dla tej funkcji zawiera przykładowe żądanie i klucza pamięci podręcznej domyślne:

- **Przykładowe żądanie:** http://wpc.0001.&lt ; domeny&gt; język & /800001/Origin/folder/asset.htm?sessionid=1234 = pl & userid = 01
- **Domyślny klucz pamięci podręcznej:** /800001/Origin/folder/asset.htm

##### <a name="include"></a>Obejmują

Przykładowa konfiguracja:

- **Typ:** Obejmują
- **Parametry:** języka

Ten typ konfiguracji wygeneruje następujący ciąg parametru pamięci podręcznej — klucz zapytania:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="include-all"></a>Uwzględnij wszystkie

Przykładowa konfiguracja:

- **Typ:** Uwzględnij wszystkie

Ten typ konfiguracji wygeneruje następujący ciąg parametru pamięci podręcznej — klucz zapytania:

    /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01

##### <a name="exclude"></a>Wyklucz

Przykładowa konfiguracja:

- **Typ:** Wyklucz
- **Parametry:** sessioned userid

Ten typ konfiguracji wygeneruje następujący ciąg parametru pamięci podręcznej — klucz zapytania:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="exclude-all"></a>Wyklucz wszystkie

Przykładowa konfiguracja:

- **Typ:** Wyklucz wszystkie

Ten typ konfiguracji wygeneruje następujący ciąg parametru pamięci podręcznej — klucz zapytania:

    /800001/Origin/folder/asset.htm

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-key-rewrite"></a>Napisz ponownie klucz pamięci podręcznej

**Cel:** Ponownie zapisuje klucz pamięci podręcznej skojarzonej z żądaniem.

Klucz pamięci podręcznej jest ścieżki względnej, która identyfikuje zasób usługi na potrzeby buforowania. Innymi słowy serwery Sprawdź, czy w pamięci podręcznej wersji zasobu zgodnie z jego ścieżki zgodnie z definicją według jego klucza pamięci podręcznej.

Skonfiguruj tę funkcję, definiując oba z następujących opcji:

Opcja|Opis
--|--
Oryginalna ścieżka| Zdefiniuj ścieżkę względną do typów żądań, których klucz pamięci podręcznej jest przepisany. Ścieżka względna mogą być definiowane przez wybranie ścieżka do podstawowego źródła, a następnie wzorzec wyrażenia regularnego.
Nowa ścieżka|Zdefiniuj ścieżkę względną do nowego klucza pamięci podręcznej. Ścieżka względna mogą być definiowane przez wybranie ścieżka do podstawowego źródła, a następnie wzorzec wyrażenia regularnego. Tej ścieżki względnej można dynamicznie skonstruować za pośrednictwem [zmiennych HTTP](cdn-http-variables.md).

**Domyślne zachowanie:** Klucz pamięci podręcznej żądania jest określana przez identyfikator URI żądania.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="comment"></a>Komentarz

**Cel:** Umożliwia notatkę do dodania w obrębie reguły.

Jest jednym z zastosowań tej funkcji zawiera dodatkowe informacje na temat ogólnego przeznaczenia, reguły lub dlaczego określonego dopasować stan lub funkcja została dodana do reguły.

Informacje o kluczu:

- Można określić maksymalnie 150 znaków.
- Używaj tylko znaków alfanumerycznych.
- Funkcja ta nie wpływa na działanie reguły. Oznacza jedynie zapewnienie obszar, w którym możesz podać informacje dotyczące przyszłości lub który może pomóc podczas rozwiązywania problemów z reguły.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="complete-cache-fill"></a>Wypełnienie pamięci podręcznej ukończone

**Cel:** Określa, co się dzieje podczas żądania skutkuje to Chybienie pamięci podręcznej częściowych w punktu obecności.

To Chybienie pamięci podręcznej częściowe opisuje stan pamięci podręcznej dla zasobu, który nie został całkowicie pobrane do punktu obecności. Jeśli element zawartości jest tylko częściowo buforowane na punktu POP, następnie dalej dla tego zasobu zostanie ona przesłana ponownie do serwera pochodzenia.
<!---
This feature is not available for the ADN platform. The typical traffic on this platform consists of relatively small assets. The size of the assets served through these platforms helps mitigate the effects of partial cache misses, since the next request will typically result in the asset being cached on that POP.

--->
To Chybienie pamięci podręcznej częściowe zazwyczaj występuje po użytkownik porzuca dostępny do pobrania lub zasobów, które są wymagane wyłącznie przy użyciu żądania range HTTP. Ta funkcja jest najbardziej przydatny w przypadku dużych zasoby, które nie są zwykle pobierane od początku do końca (na przykład filmy wideo). W rezultacie ta funkcja jest włączona domyślnie na platformie dużych HTTP. Jest ona wyłączona na innych platformach.

Zachowaj domyślną konfigurację HTTP dużych platformy, ponieważ zmniejsza obciążenie serwera pochodzenia klienta i zwiększyć szybkość jaką klientów pobierania zawartości.

Wartość|Wynik
--|--
Enabled (Włączony)|Przywraca domyślne zachowanie. Domyślnym zachowaniem jest wymuszenie POP, aby zainicjować pobieranie w tle elementu zawartości z serwera pochodzenia. Po upływie którego element zawartości będą znajdować się w lokalnej pamięci podręcznej punktu obecności.
Wyłączone|POP uniemożliwia wykonywanie pobieranie w tle dla zasobu. Powoduje to, że następnego żądania dla tego zasobu z tego regionu powoduje, że POP do żądania do serwera pochodzenia klienta.

**Domyślne zachowanie:** Włączone.

#### <a name="compatibility"></a>Zgodność

Ze względu na sposób, w której pamięci podręcznej ustawienia są śledzone ta funkcja nie może być skojarzony z następujących warunków dopasowania:

- JAKO liczba
- Adres IP klienta
- Parametr plików cookie
- Wyrażenie regularne parametru pliku cookie
- Country
- Urządzenie
- Microsoft Edge Cname
- Odwołujące się domeny
- Literał nagłówka żądania
- Wyrażenie regularne nagłówka żądania
- Symbol wieloznaczny nagłówka żądania
- Metoda żądania
- Schemat żądania
- Adres URL zapytania literału
- Adres URL zapytania z wyrażeniem regularnym
- Adres URL zapytania z symbolami wieloznacznymi
- Parametr zapytania adresu URL

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="compress-file-types"></a>Kompresuj typów plików

**Cel:** Definiuje formatów plików, które są kompresowane na serwerze.

Format pliku można określić za pomocą jego typ nośnika Internet (na przykład Content-Type). Typ nośnika Internet to metadane niezależne od platformy, które umożliwia serwerom zidentyfikować format pliku określonego zasobu. Lista popularnych typów nośnika w Internet znajduje się poniżej.

Typ nośnika w Internet|Opis
--|--
zwykły tekst|Pliki w postaci zwykłego tekstu
text/html| Pliki HTML
tekst/css|Kaskadowe arkusze stylów (CSS)
application/x-javascript|Javascript
application/javascript|Javascript

Informacje o kluczu:

- Aby określić wiele typów nośników Internet, rozdzielający każdej z nich z jednego miejsca.
- Ta funkcja kompresuje tylko zasoby, których rozmiar jest mniejszy niż 1 MB. Większe zasoby nie są kompresowane przez serwery.
- Niektórych typów zawartości, takiej jak obrazy, wideo i zasobów multimedialnych audio (na przykład, JPG, MP3, w formacie MP4, itp.), są już skompresowane. Ponieważ dodatkowe kompresji dla tych typów zasobów nie znacznie zmniejsza rozmiar pliku, zalecane jest, nie należy włączać kompresji dla nich.
- Znaki symboli wieloznacznych, takich jak gwiazdek, nie są obsługiwane.
- Przed dodaniem tej funkcji do reguły, upewnij się, że możesz Kompresja wyłączona opcja na stronie kompresji dla platformy, do której ta reguła jest stosowana.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="custom-log-field-1"></a>Pole dziennika niestandardowego 1

**Cel:** Określa format i zawartość, która zostanie przypisana do pola dziennik niestandardowy plik dziennika raw.

To pole niestandardowe pozwala określić, które wartości nagłówka żądania i odpowiedzi są przechowywane w plikach dziennika.

Domyślnie pole dziennika niestandardowego jest o nazwie "x-ec_custom-1". Nazwa tego pola można dostosować w taki sposób, na stronie Ustawienia dziennika Raw.

Format do określania nagłówków żądania i odpowiedzi jest zdefiniowana w następujący sposób:

Typ nagłówka|Format|Przykłady
-|-|-
Nagłówek żądania|`%{[RequestHeader]()}[i]()` | %{Accept-Encoding}i <br/> {Referrer}i <br/> %{Authorization}i
Nagłówek odpowiedzi|`%{[ResponseHeader]()}[o]()`| %{Age}o <br/> %{Content-Type}o <br/> %{Cookie}o

Informacje o kluczu:

- Pole dziennika niestandardowego może zawierać dowolną kombinację nagłówka pól i zwykłego tekstu.
- Prawidłowe znaki to pole są następujące: alfanumeryczne (0-9, a do z i A-Z), łączniki, dwukropki, średnikami, apostrofy, przecinkami, kropki, podkreślenia, znaków równości, nawiasów, nawiasy i miejsca do magazynowania. Symbol procentu i nawiasy klamrowe są dozwolone tylko w przypadku zastosowania do należy określić to pole nagłówka.
- Sprawdzanie pisowni dla każdego pola określony nagłówek musi odpowiadać nazwie nagłówka żądaną żądania/odpowiedzi.
- Jeśli chcesz określić wiele nagłówków, Użyj separatora, aby wskazać każdy nagłówek. Na przykład można użyć skrótu dla każdego nagłówka:
    - AE: %{Accept-Encoding}i A: %{Authorization}i CT: %{Content-Type}o

**Wartość domyślna:**  -

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---
### <a name="debug-cache-response-headers"></a>Debugowanie pamięci podręcznej nagłówki odpowiedzi

**Cel:** Określa, czy odpowiedź może zawierać [nagłówki odpowiedzi X-WE-Debug](cdn-http-debug-headers.md), który zawiera informacje dotyczące zasad pamięci podręcznej dla żądanego zasobu.

Debugowanie odpowiedzi z pamięci podręcznej, które nagłówki zostaną uwzględnione w odpowiedzi, gdy są spełnione oba z następujących czynności:

- Funkcja Debuguj pamięć podręczna nagłówki odpowiedzi została włączona dla określonego żądania.
- Określone żądanie definiuje zestaw nagłówków odpowiedzi pamięci podręcznej debugowania, które zostaną uwzględnione w odpowiedzi.

Debugowanie odpowiedzi z pamięci podręcznej, który może zostać wyświetlony nagłówków, umieszczając następujący nagłówek i dyrektyw określony w żądaniu:

`X-EC-Debug: _&lt;Directive1&gt;_,_&lt;Directive2&gt;_,_&lt;DirectiveN&gt;_`

**Przykład:**

X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state

Wartość|Wynik
-|-
Enabled (Włączony)|Żądania dla nagłówków odpowiedzi w pamięci podręcznej debugowania zwróci odpowiedź, która zawiera nagłówek X-WE-debugowanie.
Wyłączone|Nagłówek odpowiedzi X WE debugowania zostaną wykluczone z odpowiedzi.

**Domyślne zachowanie:** Wyłączone.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---
### <a name="default-internal-max-age"></a>Max-Age wewnętrzny domyślny

**Cel:** Określa domyślny interwał max-age POP do źródła serwera pamięci podręcznej ponownego sprawdzania poprawności. Innymi słowy ilość czasu, jaki upłynie punktu POP sprawdzi, czy zasób pamięci podręcznej pasuje do zasobów przechowywanych na serwerze źródłowym.

Informacje o kluczu:

- Ta akcja ma miejsce tylko dla odpowiedzi z serwera pochodzenia, które nie zostały przypisane oznaczenie max-age `Cache-Control` lub `Expires` nagłówka.
- Ta akcja nie będą wykonywane zasobów, które nie są uważane za podlega buforowaniu.
- Ta akcja nie ma wpływu na przeglądarce revalidations pamięci podręcznej POP. Tego rodzaju revalidations są określane przez `Cache-Control` lub `Expires` nagłówki wysyłane do przeglądarki, które można dostosować za pomocą funkcji Max-Age zewnętrznych.
- Wyniki tej akcji nie masz dostrzegalnych wpływ na nagłówki odpowiedzi i zawartości zwrócony z punktów obecności dla zawartości, ale może mieć wpływ na ilość ponownego sprawdzania poprawności wysyłania danych z lokalizacji POP do serwera pochodzenia.
- Konfigurowanie tej funkcji przez:
    - Wybieranie kod stanu, dla którego można zastosować domyślne wewnętrznego max-age.
    - Określając wartość całkowitą, a następnie wybierając jednostki żądany czas (na przykład sekundy, minuty, godziny itd.). Ta wartość Określa domyślny interwał max-age wewnętrznego.

- Ustawienie jednostkę czasu na wartość "Wyłączone" spowoduje przypisanie domyślnego wewnętrznego max-age interwału wynoszącego 7 dni w przypadku żądań, które nie zostały przypisane oznaczeniem max-age w ich `Cache-Control` lub `Expires` nagłówka.

**Wartość domyślna:** 7 dni

#### <a name="compatibility"></a>Zgodność

Ze względu na sposób, w której pamięci podręcznej ustawienia są śledzone ta funkcja nie może być skojarzony z następujących warunków dopasowania:
- JAKO liczba
- Adres IP klienta
- Parametr plików cookie
- Wyrażenie regularne parametru pliku cookie
- Country
- Urządzenie
- Krawędź Cname
- Odwołujące się domeny
- Literał nagłówka żądania
- Wyrażenie regularne nagłówka żądania
- Symbol wieloznaczny nagłówka żądania
- Metoda żądania
- Schemat żądania
- Adres URL zapytania literału
- Adres URL zapytania z wyrażeniem regularnym
- Adres URL zapytania z symbolami wieloznacznymi
- Parametr zapytania adresu URL

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="deny-access-403"></a>Odmowa dostępu (403)

**Cel**: Określa, czy wszystkie żądania są odrzucane odpowiedź 403 Zabroniony.

Wartość | Wynik
------|-------
Enabled (Włączony)| Powoduje, że wszystkie żądania, które spełniają kryteria dopasowania odrzucone, odpowiedź 403 Zabroniony.
Wyłączone| Przywraca domyślne zachowanie. Domyślnym zachowaniem jest zezwalająca na serwerze źródłowym, można ustalić typu odpowiedzi, który zostanie zwrócony.

**Domyślne zachowanie**: Wyłączone

> [!TIP]
   > Jedno możliwe użycie tej funkcji jest ją skojarzyć z warunkiem dopasowania nagłówka żądania, aby zablokować dostęp do odwołań HTTP, które korzystają z linków w tekście do zawartości.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="expires-header-treatment"></a>Wygasa traktowania nagłówka

**Cel:** Kontroluje Generowanie `Expires` nagłówków, POP, gdy funkcja Max-Age zewnętrznych jest aktywny.

Najprostszym sposobem osiągnięcia tego typu konfiguracją jest umieszczenie zewnętrznych Max-Age i funkcje wygasa traktowania nagłówka w tej samej instrukcji.

Wartość|Wynik
--|--
Zastąp|Zapewnia, że będzie zostaną wykonane następujące czynności:<br/>-Zastępuje `Expires` nagłówka wygenerowane przez serwer pochodzenia.<br/>-Dodaje `Expires` nagłówka generowane przez funkcję zewnętrznych Max-Age w odpowiedzi.
Przekazuj|Zapewnia, że `Expires` nagłówka generowane przez funkcję zewnętrznych Max-Age nigdy nie zostanie dodany do odpowiedzi. <br/> Jeśli serwer pochodzenia generuje `Expires` nagłówka, jego przechodziła przez użytkownika końcowego. <br/>Jeśli serwer pochodzenia nie `Expires` nagłówka, a następnie ta opcja może spowodować, że nagłówek odpowiedzi nie będzie zawierać `Expires` nagłówka.
Dodawanie, jeśli brak| Jeśli `Expires` z serwera pochodzenia nie otrzymano nagłówka, a następnie ta opcja dodaje `Expires` nagłówka generowane przez funkcję zewnętrznych Max-Age. Ta opcja jest przydatna dla zapewnienia przypisania wszystkie zasoby `Expires` nagłówka.
Usuń| Zapewnia, że `Expires` nagłówka nie jest dołączony do odpowiedzi nagłówek. Jeśli `Expires` nagłówka został już przypisany, a następnie zostanie on usunięty z odpowiedzi nagłówka.

**Domyślne zachowanie:** Zastąp

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="external-max-age"></a>Max-Age zewnętrznych

**Cel:** Określa maksymalny wiek interwał ponownego sprawdzania poprawności POP pamięci podręcznej w przeglądarce. Innymi słowy ilość czasu, jaki upłynie przed przeglądarki można sprawdzić nową wersję elementu zawartości z punktu obecności.

Włączenie tej funkcji spowoduje wygenerowanie `Cache-Control: max-age` i `Expires` nagłówki z lokalizacji POP i wysyłać je do klienta HTTP. Domyślnie te nagłówki spowoduje zastąpienie tych nagłówków utworzonych przez serwer pochodzenia. Jednak traktowania nagłówek Cache-Control i funkcji do traktowania nagłówek wygaśnięcia można zmienić to zachowanie.

Informacje o kluczu:

- Ta akcja nie ma wpływu na POP do revalidations pamięci podręcznej serwera pochodzenia. Tego rodzaju revalidations są określane przez `Cache-Control` i `Expires` nagłówki otrzymany z serwera pochodzenia i można dostosować, używając domyślnych wewnętrznych Max-Age i funkcji Force wewnętrznego Max-Age.
- Skonfiguruj tę funkcję, określając wartość całkowitą i wybranie jednostki żądany czas (na przykład sekundy, minuty, godziny itd.).
- Ustawienie tej funkcji na wartość ujemna powoduje POP wysłać `Cache-Control: no-cache` i `Expires` czas, który jest ustawiony w przeszłości, z każdym odpowiedzi do przeglądarki. Mimo że klienta HTTP nie będzie buforować odpowiedzi, to ustawienie nie wpłynie możliwość POP buforować odpowiedzi z serwera pochodzenia.
- Ustawienie jednostkę czasu na wartość "Wyłączone" spowoduje wyłączenie tej funkcji. `Cache-Control` i `Expires` nagłówki pamięci podręcznej z odpowiedzią serwera pochodzenia przekaże do przeglądarki.

**Domyślne zachowanie:** Wyłączone

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="follow-redirects"></a>Wykonaj przekierowania

**Cel:** Określa, czy żądania mogą zostać przekierowane do nazwy hosta, zdefiniowany w nagłówku Location zwróconych przez serwer pochodzenia klienta.

Informacje o kluczu:

- Tylko można przekierować żądania do krawędzi rekordów CNAME, które odnoszą się do tej samej platformy.

Wartość|Wynik
-|-
Enabled (Włączony)|Można przekierować żądania.
Wyłączone|Nie nastąpi przekierowanie żądania.

**Domyślne zachowanie:** Wyłączone.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="force-internal-max-age"></a>Wymuszanie wewnętrznych Max-Age.

**Cel:** Określa interwał max-age POP do źródła serwera pamięci podręcznej ponownego sprawdzania poprawności. Innymi słowy ilość czasu, jaki upłynie menu Podręcznym można sprawdzić, czy zasób pamięci podręcznej pasuje do zasobów przechowywanych na serwerze źródłowym.

Informacje o kluczu:

- Ta funkcja spowoduje zastąpienie interwału max-age zdefiniowane w `Cache-Control` lub `Expires` nagłówki wygenerowane z serwera pochodzenia.
- Ta funkcja nie ma wpływu na przeglądarce revalidations pamięci podręcznej POP. Tego rodzaju revalidations są określane przez `Cache-Control` lub `Expires` nagłówki wysyłane do przeglądarki.
- Ta funkcja nie ma efektu zauważalne w odpowiedzi, dostarczone przez punkt POP do zleceniodawcy. Jednakże może mieć wpływ na ilość ruchu sieciowego ponownego sprawdzania poprawności wysyłane z lokalizacji POP do serwera pochodzenia.
- Konfigurowanie tej funkcji przez:
    - Wybieranie kod stanu, dla których zostaną zastosowane wewnętrznego max-age.
    - Określenie wartości liczby całkowitej i wybierając pozycję jednostki żądany czas (na przykład sekundy, minuty, godziny itd.). Ta wartość Określa interwał max-age żądania.

- Ustawienie jednostkę czasu na wartość "Wyłączone" powoduje wyłączenie tej funkcji. Nie można przypisać wewnętrznego interwał max-age do żądanych zasobów. Jeśli oryginalny nagłówek nie zawiera instrukcji buforowania, zgodnie z ustawieniem active w funkcji domyślnych wewnętrznych Max-Age buforowane jest element zawartości.

**Domyślne zachowanie:** Wyłączone

#### <a name="compatibility"></a>Zgodność

Ze względu na sposób, w której pamięci podręcznej ustawienia są śledzone ta funkcja nie może być skojarzony z następujących warunków dopasowania:
- JAKO liczba
- Adres IP klienta
- Parametr plików cookie
- Wyrażenie regularne parametru pliku cookie
- Country
- Urządzenie
- Krawędź Cname
- Odwołujące się domeny
- Literał nagłówka żądania
- Wyrażenie regularne nagłówka żądania
- Symbol wieloznaczny nagłówka żądania
- Metoda żądania
- Schemat żądania
- Adres URL zapytania literału
- Adres URL zapytania z wyrażeniem regularnym
- Adres URL zapytania z symbolami wieloznacznymi
- Parametr zapytania adresu URL

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="h264-support-http-progressive-download"></a>Obsługa H.264 (pobierania progresywnego HTTP)

**Cel:** Określa typy H.264 formatów plików, które mogą być używane do strumieniowego przesyłania zawartości.

Informacje o kluczu:

- W opcji rozszerzenia plików, należy zdefiniować zestaw dozwolone rozszerzenia nazw plików H.264 rozdzielonych spacjami. Opcja rozszerzenia plików przesłoni zachowanie domyślne. Obsługa MP4 i F4V pomocy technicznej, umieszczając te rozszerzenia nazw plików, gdy ustawienie tej opcji.
- Obejmują okres, po określeniu każdego rozszerzenia nazwy pliku (na przykład _MP4_, _.f4v_).

**Domyślne zachowanie:** Domyślnie pobierania progresywnego HTTP obsługuje multimediów w formacie MP4 oraz F4V.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="honor-no-cache-request"></a>Żądanie pamięci podręcznej nie honor

**Cel:** Określa, czy żądania nie pamięci podręcznej klienta HTTP będą przekazywane do serwera pochodzenia.

Żądanie pamięci podręcznej nie występuje, gdy klient HTTP wysyła `Cache-Control: no-cache` i/lub `Pragma: no-cache` nagłówka w żądaniu HTTP.

Wartość|Wynik
--|--
Enabled (Włączony)|Umożliwia klienta HTTP nie pamięci podręcznej żądań były przekazywane do serwera pochodzenia i serwer pochodzenia zwraca nagłówki odpowiedzi i treści za pośrednictwem punktu POP do klienta HTTP.
Wyłączone|Przywraca domyślne zachowanie. Domyślnym zachowaniem jest aby zapobiec żądań pamięci podręcznej nie są przekazywane do serwera pochodzenia.

Dla całego ruchu w środowisku produkcyjnym zaleca pozostaw tę funkcję w stanie domyślnym wyłączone. W przeciwnym razie serwerów źródłowych będzie nie można włączyć osłony od użytkowników końcowych, którzy mogą przypadkowo wyzwolić wiele żądań pamięci podręcznej nie w przypadku odświeżanie stron sieci web lub z wielu popularnych odtwarzaczy multimedialnych, które są kodowane, aby wysyłaj nagłówek nie pamięci podręcznej, z każdym żądaniem wideo. Niemniej jednak ta funkcja może być przydatne do zastosowania do niektórych nieprodukcyjnych przemieszczania lub testowania katalogi, aby umożliwić nowości zostać pobrane na żądanie z serwera pochodzenia.

Stan pamięci podręcznej, zgłaszany jest żądanie, które mogą być przekazywane do serwera pochodzenia ze względu na tę funkcję `TCP_Client_Refresh_Miss`. Raport stan pamięci podręcznej, który jest dostępny w obszarach podstawowych modułu raportowania, zapewnia informacje statystyczne według stanu pamięci podręcznej. Ten raport umożliwia śledzenie liczba i Procent żądań, które są przesyłane do serwera pochodzenia ze względu na tę funkcję.

**Domyślne zachowanie:** Wyłączone.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="ignore-origin-no-cache"></a>Ignoruj pochodzenia No-Cache

**Cel:** Określa, czy sieć CDN będzie ignorować następujące dyrektywy, udostępniana z serwera pochodzenia:

- `Cache-Control: private`
- `Cache-Control: no-store`
- `Cache-Control: no-cache`
- `Pragma: no-cache`

Informacje o kluczu:

- Skonfiguruj tę funkcję, definiując rozdzieloną spacjami listę kodów stanu, dla których zostaną zignorowane powyższych dyrektyw.
- Zestaw kodów stanu prawidłowy dla tej funkcji to: 200, 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 i 505.
- Tę funkcję można wyłączyć, ustawiając dla niej do pustej wartości.

**Domyślne zachowanie:** Domyślnym zachowaniem jest respektować powyższych dyrektyw.

#### <a name="compatibility"></a>Zgodność

Ze względu na sposób, w której pamięci podręcznej ustawienia są śledzone ta funkcja nie może być skojarzony z następujących warunków dopasowania:
- JAKO liczba
- Adres IP klienta
- Parametr plików cookie
- Wyrażenie regularne parametru pliku cookie
- Country
- Urządzenie
- Krawędź Cname
- Odwołujące się domeny
- Literał nagłówka żądania
- Wyrażenie regularne nagłówka żądania
- Symbol wieloznaczny nagłówka żądania
- Metoda żądania
- Schemat żądania
- Adres URL zapytania literału
- Adres URL zapytania z wyrażeniem regularnym
- Adres URL zapytania z symbolami wieloznacznymi
- Parametr zapytania adresu URL

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="ignore-unsatisfiable-ranges"></a>Ignoruj Unsatisfiable zakresów

**Cel:** Określa odpowiedź, która zostanie przywrócony do klientów, gdy żądanie generuje 416 żądany zakres nie żądania kod stanu.

Domyślnie ten kod stanu jest zwracane żądania zakresu bajtów nie mogą być spełnione przez punkt POP i nie określono pola nagłówka żądania If-Range.

Wartość|Wynik
-|-
Enabled (Włączony)|Zapobiega lokalizacji POP odpowiada na żądanie nieprawidłowy zakres bajtów z 416 żądany zakres nie żądania kodem stanu. Zamiast tego serwery dostarczanie żądanego zasobu i zwrócić 200 OK do klienta.
Wyłączone|Przywraca domyślne zachowanie. Domyślnym zachowaniem jest respektować 416 żądany zakres nie żądania kod stanu.

**Domyślne zachowanie:** Wyłączone.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="internal-max-stale"></a>Internal Max-Stale

**Cel:** Formanty, jak długo późniejsza niż godzina wygaśnięcia normalne trwałego pamięci podręcznej mogą być obsługiwani z punktu POP po punkcie POP nie może przechowywać w pamięci podręcznej zawartości z serwera pochodzenia.

Zwykle po wygaśnięciu czasu max-age elementu zawartości, punkt POP wyśle żądanie ponownego sprawdzania poprawności do serwera pochodzenia. Źródła serwera zostanie następnie odpowiedź z albo 304 niezmodyfikowane można nadać POP nowej dzierżawy trwałego pamięci podręcznej lub z 200 OK zapewnienie punkt POP zaktualizowaną wersję trwałego pamięci podręcznej.

W przypadku nie można nawiązać połączenia z serwerem pochodzenia Podczas próby wykonania takich ponownego sprawdzania poprawności, a następnie tej funkcji wewnętrznych nieodświeżone maksymalna liczba Określa, czy i na jak długo punkt POP, punkt POP mogą nadal obsługiwać zasób nieodświeżone teraz.

Należy pamiętać, że dany interwał czasu zaczyna się po wygaśnięciu zasobu max-age nie, jeśli nie powiodło się ponowne sprawdzenie poprawności występuje. Dlatego maksymalny okres, podczas którego element zawartości mogą być obsługiwane bez pomyślnego ponownego sprawdzania poprawności jest określone przez kombinację maksymalny wiek i maksymalna nieodświeżone czas. Na przykład jeśli element zawartości był buforowany o 9:00, za pomocą max-age 30 minut i nieodświeżone maksymalna wynosząca 15 minut, następnie nie powiodło się ponowne sprawdzenie poprawności próba 9:44 w rezultacie użytkownik końcowy odbieranie starych trwały pamięci podręcznej, a nie powiodło się ponowne sprawdzenie poprawności próba 9:46 mogłoby spowodować en odbieranie 504 Gateway Timeout d użytkownika.

Dowolna wartość skonfigurowane dla tej funkcji został zastąpiony przez `Cache-Control: must-revalidate` lub `Cache-Control: proxy-revalidate` nagłówki otrzymany z serwera pochodzenia. Odebranie jedną z tych nagłówków z serwera pochodzenia kiedy zasobu początkowo są buforowane, następnie POP nie obsłuży starych trwałego pamięci podręcznej. Jeśli punkt POP nie może przechowywać za pomocą źródła, gdy wygaśnie interwał max-age elementu zawartości, w takim przypadku punkt POP zwraca 504 Błąd limitu czasu bramy.

Informacje o kluczu:

- Konfigurowanie tej funkcji przez:
    - Wybieranie kod stanu, dla których zostaną zastosowane max nieodświeżone.
    - Określając wartość całkowitą, a następnie wybierając jednostki żądany czas (na przykład sekundy, minuty, godziny itd.). Ta wartość określa wewnętrznego max stare, zostaną zastosowane.

- Ustawienie jednostkę czasu na wartość "Wyłączone" spowoduje wyłączenie tej funkcji. Zasób pamięci podręcznej nie będzie udostępniania poza czas wygaśnięcia normalne.

**Domyślne zachowanie:** Dwie minuty

#### <a name="compatibility"></a>Zgodność

Ze względu na sposób, w której pamięci podręcznej ustawienia są śledzone ta funkcja nie może być skojarzony z następujących warunków dopasowania:
- JAKO liczba
- Adres IP klienta
- Parametr plików cookie
- Wyrażenie regularne parametru pliku cookie
- Country
- Urządzenie
- Krawędź Cname
- Odwołujące się domeny
- Literał nagłówka żądania
- Wyrażenie regularne nagłówka żądania
- Symbol wieloznaczny nagłówka żądania
- Metoda żądania
- Schemat żądania
- Adres URL zapytania literału
- Adres URL zapytania z wyrażeniem regularnym
- Adres URL zapytania z symbolami wieloznacznymi
- Parametr zapytania adresu URL

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="log-query-string"></a>Ciąg zapytania dziennika

**Cel:** Określa, czy ciąg zapytania będą przechowywane wraz z adresu URL na uzyskiwanie dostępu do dzienników.

Wartość|Wynik
-|-
Enabled (Włączony)|Umożliwia magazynu ciągów zapytania, podczas rejestrowania adresów URL w dzienniku dostępu. Jeśli adres URL zawiera ciąg zapytania, ta opcja będzie ma wpływ.
Wyłączone|Przywraca domyślne zachowanie. Domyślnym zachowaniem jest Ignoruj ciągi zapytań, podczas rejestrowania adresów URL w dzienniku dostępu.

**Domyślne zachowanie:** Wyłączone.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="maximum-keep-alive-requests"></a>Maksymalna liczba żądań Keep-Alive

**Cel:** Określa maksymalną liczbę żądań połączenia Keep-Alive, po którym jest ono zamknięte.

Maksymalna liczba żądań niską wartość odradza się i może spowodować obniżenie wydajności.

Informacje o kluczu:

- Tę wartość można określić jako liczbą całkowitą.
- Nie dołączaj kropki i przecinki się określoną wartością.

**Wartość domyślna:** 10 000 żądań

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="modify-client-request-header"></a>Modyfikowanie nagłówek żądania klienta

**Cel:** Każde żądanie zawiera zestaw nagłówków żądań, które zawiera jego opis. Ta funkcja może być:

- Dołącz lub zastąpić wartość przypisana do nagłówka żądania. Jeśli nie ma określonego nagłówka żądania, następnie ta funkcja doda go do żądania.
- Usuń nagłówek żądania z żądania.

Żądania, które są przekazywane do serwera pochodzenia odzwierciedlają zmiany wprowadzone przez tę funkcję.

W nagłówku żądania można wykonać jedną z następujących czynności:

Opcja|Opis|Przykład
-|-|-
Append|Określona wartość zostanie dodany na końcu istniejącej wartości nagłówka żądania.|**Wartość nagłówka żądania (klient):**<br/>Wartość1<br/>**Wartość nagłówka żądania (aparat reguł):**<br/>Value2 <br/>**Nowa wartość nagłówka żądania:** <br/>Value1Value2
Zastąp|Wartość nagłówka żądania ustawi określoną wartość.|**Wartość nagłówka żądania (klient):**<br/>Wartość1<br/>**Wartość nagłówka żądania (aparat reguł):**<br/>Value2<br/>**Nowa wartość nagłówka żądania:**<br/> Value2 <br/>
Usuwanie|Usuwa określonego nagłówka żądania.|**Wartość nagłówka żądania (klient):**<br/>Wartość1<br/>**Zmodyfikuj konfigurację nagłówka żądania klienta:**<br/>Usuń w nagłówku żądania.<br/>**Wynik:**<br/>Określonego nagłówka żądania nie zostaną przekazane do serwera pochodzenia.

Informacje o kluczu:

- Upewnij się, że wartość określona w opcji Nazwa dokładnego dopasowania dla żądanego żądanego nagłówka.
- Przypadek nie jest brana pod uwagę w celu identyfikowania nagłówka. Na przykład, żadnego z następujących wariantów `Cache-Control` nazwę nagłówka może służyć do identyfikacji:
    - cache-control
    - KONTROLA PAMIĘCI PODRĘCZNEJ
    - cachE-Control
- Podczas określania nazwy nagłówka, należy użyć tylko znaki alfanumeryczne, łączniki i podkreślenia.
- Usunięcie nagłówka uniemożliwi jej są przekazywane do serwera pochodzenia przez lokalizacji POP.
- Następujące nagłówki są zarezerwowane i nie można zmodyfikować przez tę funkcję:
    - przekazane
    - host
    - za pomocą
    - ostrzeżenie
    - x-forwarded-for
    - Wszystkie nazwy nagłówka rozpoczynających się od "x WE" są zastrzeżone.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="modify-client-response-header"></a>Modyfikowanie nagłówek odpowiedzi klienta

Poszczególne odpowiedzi zawiera zestaw nagłówków odpowiedzi, które zawiera jego opis. Ta funkcja może być:

- Dołącz lub zastąpić wartość przypisana do nagłówka odpowiedzi. Jeśli nie ma określonego nagłówka żądania, następnie ta funkcja doda go do odpowiedzi.
- Usuń nagłówek odpowiedzi z odpowiedzi.

Domyślnie wartości nagłówka odpowiedzi są definiowane przez serwer pochodzenia i lokalizacji POP.

W nagłówku odpowiedzi można wykonać jedną z następujących czynności:

Opcja|Opis|Przykład
-|-|-
Append|Określona wartość zostanie dodany na końcu istniejącej wartości nagłówka odpowiedzi.|**Wartość nagłówka odpowiedzi (klient):**<br />Wartość1<br/>**Wartość nagłówka odpowiedzi (aparat reguł):**<br/>Value2<br/>**Nowa wartość nagłówka odpowiedzi:**<br/>Value1Value2
Zastąp|Wartość nagłówka odpowiedzi ustawi określoną wartość.|**Wartość nagłówka odpowiedzi (klient):**<br/>Wartość1<br/>**Wartość nagłówka odpowiedzi (aparat reguł):**<br/>Value2 <br/>**Nowa wartość nagłówka odpowiedzi:**<br/>Value2 <br/>
Usuwanie|Usuwa określonego nagłówka żądania.|**Wartość nagłówka odpowiedzi (klient):**<br/>Wartość1<br/>**Zmodyfikuj konfigurację nagłówek odpowiedzi klienta:**<br/>Usuń nagłówek odpowiedzi w danym.<br/>**Wynik:**<br/>Określonego nagłówka żądania nie zostaną przekazane do zleceniodawcy.

Informacje o kluczu:

- Upewnij się, że wartość określona w opcji Nazwa dokładnego dopasowania dla nagłówka żądanej odpowiedzi.
- Przypadek nie jest brana pod uwagę w celu identyfikowania nagłówka. Na przykład, żadnego z następujących wariantów `Cache-Control` nazwę nagłówka może służyć do identyfikacji:
    - cache-control
    - KONTROLA PAMIĘCI PODRĘCZNEJ
    - cachE-Control
- Usuwanie nagłówek uniemożliwia jego przesyłane dalej do zleceniodawcy.
- Następujące nagłówki są zarezerwowane i nie można zmodyfikować przez tę funkcję:
    - accept-encoding
    - Wiek
    - połączenie
    - content-encoding
    - content-length
    - zakres zawartości
    - date
    - server
    - Zwiastuny —
    - kodowanie transferu
    - upgrade
    - różnią się
    - za pomocą
    - ostrzeżenie
    - Wszystkie nazwy nagłówka rozpoczynających się od "x WE" są zastrzeżone.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="partial-cache-sharing"></a>Udostępnianie częściowe pamięci podręcznej

**Cel:** Określa, czy żądanie może generować częściowo buforowanej zawartości.

Ta pamięć podręczna w częściowej może użyte do spełnienia nowych żądań dla tej zawartości do momentu żądanej zawartości pełni są buforowane.

Wartość|Wynik
-|-
Enabled (Włączony)|Żądania mogą generować częściowe buforowanej zawartości.
Wyłączone|Żądania może generować jedynie pełni zbuforowaną wersję żądanej zawartości.

**Domyślne zachowanie:** Wyłączone.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="prevalidate-cached-content"></a>Prevalidate zawartości w pamięci podręcznej

**Cel:** Określa, czy zawartość z pamięci podręcznej będą kwalifikować się do początku ponownego sprawdzania poprawności, przed jego wygaśnięcia.

Zdefiniuj ilość czasu przed upłynięciem czasu wygaśnięcia żądanej zawartości, w którym będą kwalifikować się do początku ponownego sprawdzania poprawności.

Informacje o kluczu:

- Wybieranie "Off", jako jednostka czasu wymaga ponownego sprawdzania poprawności została wykonana po zawartości pamięci podręcznej wygasł czas wygaśnięcia. Czas nie powinna być określona i jest ignorowana.

**Domyślne zachowanie:** Off. Ponowne sprawdzenie poprawności tylko może mieć miejsce, po upływie czasu wygaśnięcia zawartości pamięci podręcznej.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="proxy-special-headers"></a>Serwer proxy specjalnych nagłówków

**Cel:** Definiuje zestaw [nagłówków żądań HTTP specyficzne dla firmy Verizon](cdn-verizon-http-headers.md) , zostaną przesłane dalej od punktu POP do serwera pochodzenia.

Informacje o kluczu:

- Każdy nagłówek żądania specyficzne dla usługi CDN, zdefiniowane w tej funkcji są przekazywane do serwera pochodzenia. Wykluczone nagłówki nie są przesyłane dalej.
- Aby zapobiec sytuacji, w której nagłówek żądania specyficzne dla sieci CDN są przekazywane, usuń go z rozdzielonej spacjami listy w polu listy nagłówka.

Następujące nagłówki HTTP znajdują się na domyślnej liście:
- za pomocą
- X-Forwarded-For
- X-Forwarded-Proto
- X-Host
- X Midgress
- X-Gateway-List
- X-EC-Name
- Host

**Domyślne zachowanie:** Wszystkie nagłówki żądania specyficzne dla usługi CDN będzie przekazywany do serwera pochodzenia.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="refresh-zero-byte-cache-files"></a>Odśwież Zero bajtów pamięci podręcznej plików

**Cel:** Określa, jak żądanie klienta HTTP dla zawartości pamięci podręcznej 0 bajtów jest obsługiwany przez lokalizacji POP.

Prawidłowe wartości to:

Wartość|Wynik
--|--
Enabled (Włączony)|Powoduje, że punkt POP ponownie Pobierz element zawartości z serwera pochodzenia.
Wyłączone|Przywraca domyślne zachowanie. Zachowanie domyślne jest do obsługi pamięci podręcznej prawidłowe zasoby na żądanie.

Ta funkcja nie jest wymagany poprawny pamięci podręcznej i dostarczania zawartości, ale może być przydatne jako obejście tego problemu. Na przykład dynamiczne generatorów zawartości na serwerach pochodzenia przypadkowo może spowodować 0 bajtów odpowiedzi są wysyłane do lokalizacji POP. Tego rodzaju odpowiedzi są buforowane przez lokalizacji POP. Jeśli wiesz, że odpowiedzi 0 bajtów nigdy nie jest prawidłowa odpowiedź do takiej zawartości, ta funkcja może uniemożliwić tych typów zasobów z obsługiwanych klientów.

**Domyślne zachowanie:** Wyłączone.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="set-cacheable-status-codes"></a>Kody stanu podlega buforowaniu, na zestaw

**Cel:** Definiuje zestaw kodów stanu, które mogą skutkować zawartości w pamięci podręcznej.

Domyślnie pamięć podręczna jest włączona tylko dla 200 OK odpowiedzi.

Zdefiniuj na zestaw kodów żądany stan rozdzielonych spacjami.

Informacje o kluczu:

- Włącz funkcję Ignoruj pochodzenia No-Cache. Jeśli ta funkcja nie jest włączona, następnie odpowiedzi inne niż 200 OK może nie być pamięci podręcznej.
- Zestaw kodów stanu prawidłowy dla tej funkcji to: 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 i 505.
- Nie można użyć tej funkcji można wyłączyć buforowanie odpowiedzi, które generują kod stanu 200 OK.

**Domyślne zachowanie:** Buforowanie jest włączone tylko w przypadku odpowiedzi generujących kod stanu 200 OK.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="set-client-ip-custom-header"></a>Ustaw niestandardowy nagłówek IP klienta

**Cel:** Dodaje niestandardowy nagłówek, który identyfikuje klienta przy użyciu adresu IP na żądanie.

Opcja nazwę nagłówka definiuje nazwę nagłówka żądania niestandardowych, gdzie znajduje się adres IP klienta.

Ta funkcja pozwala klientowi serwerze źródłowym, aby dowiedzieć się, adres IP klienta adresy przy użyciu nagłówka niestandardowego żądania. Jeśli żądanie jest obsługiwany z pamięci podręcznej, serwer pochodzenia nie będzie można poinformowany o adres IP klienta. Dlatego zalecane jest, można użyć funkcji, z zasobami, które nie są buforowane.

Upewnij się, że nazwa określonego nagłówka nie pasuje do żadnego z następujących nazw:

- Nazwy nagłówków żądań standardowych. Lista nazw standardowy nagłówek znajdują się w [dokumencie RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).
- Nazwy nagłówków zarezerwowanych:
    - forwarded-for
    - host
    - różnią się
    - za pomocą
    - ostrzeżenie
    - x-forwarded-for
    - Wszystkie nazwy nagłówka rozpoczynających się od "x WE" są zastrzeżone.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="stale-content-delivery-on-error"></a>Stałe dostarczanie zawartości w przypadku błędu

**Cel:** Określa, czy wygasła zawartości w pamięci podręcznej zostaną dostarczone, gdy wystąpi błąd podczas ponownego sprawdzania poprawności w pamięci podręcznej lub podczas pobierania żądanej zawartości z serwera pochodzenia klienta.

Wartość|Wynik
-|-
Enabled (Włączony)|Zawartość są dostarczane do zleceniodawcy po wystąpieniu błędu podczas połączenia z serwerem pochodzenia.
Wyłączone|Błąd serwera pochodzenia jest przekazywany do zleceniodawcy.

**Domyślne zachowanie:** Wyłączone

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="stale-while-revalidate"></a>Starych podczas Revalidate

**Cel:** Zwiększa wydajność, umożliwiając POP obsługiwać zawartość do zleceniodawcy podczas ponownego sprawdzania poprawności.

Informacje o kluczu:

- Działanie tej funkcji zależy od jednostki wybrana wartość czasu.
    - **Jednostka czasu:** Określ długość czasu, a następnie wybrać jednostkę czasu (na przykład sekundy, minuty, godziny, itp.) umożliwia stałe dostarczanie zawartości. Ten typ Instalatora umożliwia wydłużyć czas, który może ona dostarczać w sieci CDN w zawartości przed wymaganiem sprawdzania poprawności, zgodnie z następującą formułę: **Czas wygaśnięcia** + **starych podczas Revalidate czasu**
    - **Wyłączone:** Wybierz "Off", aby wymagać ponownego sprawdzania poprawności, zanim żądanie zawartość może być obsługiwana.
        - Nie należy określać długość czasu, ponieważ nie ma zastosowania i zostaną zignorowane.

**Domyślne zachowanie:** Off. Ponowne sprawdzenie poprawności zależnego musi odbywać się przed żądaną zawartość może być obsługiwany.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth"></a>Token uwierzytelniania

**Cel:** Określa, czy uwierzytelniania opartego na tokenach zostaną zastosowane na żądanie.

Jeśli jest włączone uwierzytelnianie oparte na tokenie, tylko żądań, które zapewniają zaszyfrowany token i są zgodne z wymaganiami określonymi przez ten token zostanie uznane.

Klucz szyfrowania, który jest używany do szyfrowania i odszyfrowywania tokenów wartości jest określany za pomocą klucza podstawowego i opcje kopii zapasowej klucza na stronie tokenu uwierzytelniania. Należy pamiętać, że klucze szyfrowania są specyficzne dla platformy.

**Domyślne zachowanie:** Wyłączone.

Ta funkcja ma pierwszeństwo przed większość funkcji, z wyjątkiem funkcji ponownego zapisywania adresów URL.

Wartość | Wynik
------|---------
Enabled (Włączony) | Chroni żądanej zawartości przy użyciu uwierzytelniania opartego na tokenach. Tylko żądania od klientów, podaj prawidłowy token, a jej wymagań, które będą uznawane. Transakcje FTP są wykluczone z uwierzytelniania opartego na tokenach.
Wyłączone| Przywraca domyślne zachowanie. Domyślnym zachowaniem jest umożliwienie konfiguracji uwierzytelniania opartego na tokenie, aby określić, czy żądanie zostanie zabezpieczone.

#### <a name="compatibility"></a>Zgodność

Nie należy używać tokenu uwierzytelniania z warunkiem dopasowania zawsze.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-denial-code"></a>Kod typu "odmowa" tokenu uwierzytelniania

**Cel:** Określa typ odpowiedzi, które zostaną zwrócone do użytkownika, jeśli żądanie zostanie odrzucone z powodu uwierzytelniania opartego na tokenach.

Kody odpowiedzi dostępne są wymienione w poniższej tabeli.

Kod odpowiedzi|Nazwa odpowiedzi|Opis
-------------|-------------|--------
301|Trwale przeniesiona|Ten kod stanu przekierowania nieautoryzowanym użytkownikom na adres URL określony w nagłówku Location.
302|Znaleziono|Ten kod stanu przekierowania nieautoryzowanym użytkownikom na adres URL określony w nagłówku Location. Ten kod stanu jest branży standardową metodę wykonywania przekierowania.
307|Przekierowanie tymczasowe|Ten kod stanu przekierowania nieautoryzowanym użytkownikom na adres URL określony w nagłówku Location.
401|Brak autoryzacji|Łącząc ten kod stanu z nagłówka WWW-Authenticate odpowiedzi pozwala na monitowanie użytkownika o uwierzytelnienie.
403|Zabroniony|Ta wiadomość jest standardowa 403 Zabroniony komunikat o stanie nieautoryzowany użytkownik zostanie wyświetlony podczas próby uzyskania dostępu do chronionej zawartości.
404|Nie można odnaleźć pliku|Ten kod stanu wskazuje, że klient HTTP był w stanie nawiązać połączenia z serwerem, ale nie można odnaleźć żądanej zawartości.

#### <a name="compatibility"></a>Zgodność

Nie należy używać kodu odmowa tokenu uwierzytelniania z warunkiem dopasowania zawsze. Zamiast tego należy użyć **niestandardowe odmowa obsługi** sekcji **tokenu uwierzytelniania** strony **Zarządzaj** portalu. Aby uzyskać więcej informacji, zobacz [zasoby w Zabezpieczanie usługi Azure CDN przy użyciu tokenu uwierzytelniania](cdn-token-auth.md).

#### <a name="url-redirection"></a>Przekierowywanie adresu URL

Ta funkcja obsługuje adresu URL przekierowania do adresu URL zdefiniowanych przez użytkownika, gdy jest skonfigurowana, aby zwrócić kod stanu 3xx. Ten adres URL zdefiniowanych przez użytkownika, można określić, wykonując następujące czynności:

1. Wybierz kod odpowiedzi 3xx funkcji kodu odmowa tokenu uwierzytelniania.
2. Wybierz "Lokalizacja" z opcją opcjonalną nazwę nagłówka.
3. Ustaw opcję opcjonalnych wartości nagłówka do żądanego adresu URL.

Jeśli adres URL nie został zdefiniowany dla kodu stanu 3xx, strona odpowiedzi standardowy kod stanu 3xx będą zwracane użytkownikowi.

Przekierowywanie adresu URL dotyczy tylko 3xx kody odpowiedzi.

Opcja opcjonalnych wartości nagłówka obsługuje znaki alfanumeryczne, znaki cudzysłowu i miejsca do magazynowania.

#### <a name="authentication"></a>Authentication

Ta funkcja obsługuje możliwość uwzględnienia nagłówka WWW-Authenticate podczas odpowiadania na nieautoryzowanego żądania dla zawartości chronionej przez uwierzytelnianie oparte na tokenie. Jeśli nagłówka WWW-Authenticate została ustawiona na "basic" w swojej konfiguracji, a następnie nieautoryzowany użytkownik jest monitowany o poświadczenia konta.

Powyższych konfiguracji można osiągnąć, wykonując następujące czynności:

1. Wybierz "401" jako kod odpowiedzi dla funkcji kodu odmowa tokenu uwierzytelniania.
2. Wybierz pozycję "WWW-Authenticate" z opcją opcjonalną nazwę nagłówka.
3. Ustaw opcję opcjonalna wartość nagłówka "podstawowa".

Nagłówek WWW-Authenticate dotyczy tylko kody odpowiedzi 401.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-ignore-url-case"></a>Token uwierzytelniania ignorowanie wielkości liter adresu URL

**Cel:** Określa, czy adres URL porównania przez uwierzytelnianie oparte na tokenie uwzględniają wielkość liter.

Parametry wpływ tej funkcji są następujące:

- ec_url_allow
- ec_ref_allow
- ec_ref_deny

Prawidłowe wartości to:

Wartość|Wynik
---|----
Enabled (Włączony)|Powoduje, że punkt POP zignorować wielkość liter podczas porównywania adresy URL dla uwierzytelniania opartego na tokenach parametrów.
Wyłączone|Przywraca domyślne zachowanie. Domyślnym zachowaniem jest adres URL porównania dla uwierzytelniania tokenu być uwzględniana wielkość liter.

**Domyślne zachowanie:** Wyłączone.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-parameter"></a>Parametr tokenu uwierzytelniania

**Cel:** Określa, czy parametr ciągu zapytania uwierzytelniania opartego na tokenach powinny zostać zmienione.

Informacje o kluczu:

- Opcja wartość definiuje nazwę parametru ciągu zapytania za pomocą których można określić token.
- Nie można ustawić opcji wartość "ec_token."
- Upewnij się, że z nazwą zdefiniowaną w opcji wartość zawiera tylko prawidłowe znaki adresu URL.

Wartość|Wynik
----|----
Enabled (Włączony)|Opcja wartość definiuje nazwę parametru ciągu zapytania za pomocą których można zdefiniować tokenów.
Wyłączone|Token może być określona jako parametr ciągu zapytania niezdefiniowana w adresie URL żądania.

**Domyślne zachowanie:** Wyłączone. Token może być określona jako parametr ciągu zapytania niezdefiniowana w adresie URL żądania.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="url-redirect"></a>Adres URL przekierowania

**Cel:** Przekierowuje żądania za pośrednictwem nagłówek lokalizacji.

Konfiguracja ta funkcja wymaga ustawienie następujących opcji:

Opcja|Opis
-|-
Kod|Wybierz kod odpowiedzi, który zostanie zwrócony do zleceniodawcy.
Źródło & wzorzec| Te ustawienia definiują wzorzec identyfikatora URI żądania, który identyfikuje typy żądań, które mogą zostać przekierowane. Nastąpi przekierowanie tylko żądania, którego adres URL spełnia oba następujące kryteria: <br/> <br/> **Źródło (lub punkt dostępu do zawartości):** Wybierz ścieżkę względną, która identyfikuje serwer pochodzenia. Ta ścieżka jest _/XXXX/_ sekcji i nazwa punktu końcowego. <br/><br/> **Źródło (wzorzec):** Wzorzec, który identyfikuje żądania za pomocą ścieżki względnej, musi być zdefiniowany. Ten wzorzec wyrażenia regularnego należy zdefiniować ścieżkę, która rozpoczyna się bezpośrednio po wybrana wcześniej dostępu do zawartości punktu (zobacz powyżej). <br/> — Upewnij się, że żądanie identyfikatora URI kryteria (czyli źródła & wzorca) uprzednio zdefiniowany nie powodują konfliktów z żadnych warunków dopasowania zdefiniowane dla tej funkcji. <br/> -Określ wzorzec; Jeśli używasz pustej wartości jako wzorzec wszystkie ciągi są dopasowywane.
Miejsce docelowe| Zdefiniuj adres URL, do której nastąpi przekierowanie powyżej żądań. <br/><br/> Dynamicznie utworzyć przy użyciu tego adresu URL: <br/> -Wzorzec wyrażenia regularnego <br/>- [Zmienne HTTP](cdn-http-variables.md) <br/><br/> Zastąp wartości przechwytywane we wzorcu źródła do wzorca docelowego przy użyciu ciągu $_n_ gdzie _n_ identyfikuje wartość według kolejności, w której został przechwycony. Na przykład $1 reprezentuje pierwszą wartość przechwycone we wzorcu źródła, gdy $2 reprezentuje drugiej wartości. <br/>

Zdecydowanie zaleca się użyć bezwzględnego adresu URL. Użycie względny adres URL może przekierować adresów URL usługi CDN do nieprawidłowej ścieżki.

**Przykładowy scenariusz**

Ten przykład pokazuje, jak przekierowywanie adresu URL CNAME, który jest rozpoznawany jako ten podstawowy adres URL usługi CDN edge: http:\//marketing.azureedge.net/brochures

Kwalifikowanie żądania nastąpi przekierowanie do tej krawędzi podstawowy adres URL CNAME: http:\//cdn.mydomain.com/resources

Ten adres URL przekierowania można osiągnąć przy użyciu następującej konfiguracji: ![Adres URL przekierowania](./media/cdn-rules-engine-reference/cdn-rules-engine-redirect.png)

**Najważniejsze kwestie:**

- Funkcja Przekierowywanie adresu URL definiuje żądania adresów URL, które zostanie przekierowany. W rezultacie dopasowanie dodatkowe warunki nie są wymagane. Mimo że warunek dopasowania został zdefiniowany jako "Always", zostanie przekierowany tylko żądania, które wskazują folder "broszury" na "marketing" źródłem klienta.
- Wszystkie dopasowania żądania nastąpi przekierowanie na urządzeniach brzegowych, zdefiniowanych przez adres URL CNAME w opcji docelowego.
    - Przykładowy scenariusz #1:
        - Przykładowe żądanie (adres URL usługi CDN): http:\//marketing.azureedge.net/brochures/widgets.pdf
        - Adres URL żądania (po przekierowania): http:\//cdn.mydomain.com/resources/widgets.pdf  
    - Przykładowy scenariusz #2:
        - Przykładowe żądanie (Edge CNAME adres URL): http:\//marketing.mydomain.com/brochures/widgets.pdf
        - Adres URL żądania (po przekierowania): http:\//cdn.mydomain.com/resources/widgets.pdf przykładowy scenariusz
    - Przykładowy scenariusz #3:
        - Przykładowe żądanie (Edge CNAME adres URL): http:\//brochures.mydomain.com/campaignA/final/productC.ppt
        - Adres URL żądania (po przekierowania): http:\//cdn.mydomain.com/resources/campaignA/final/productC.ppt 
- Zmienna schemat żądania (% {scheme}) jest używany w przypadku opcji docelowego i gwarantuje, że schemat żądania pozostaje niezmieniony po przekierowaniu.
- Segmenty adresu URL, które zostały przechwycone z żądania są dołączane do nowego adresu URL za pośrednictwem "$1".

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="url-rewrite"></a>Ponowne zapisywanie adresów URL

**Cel:** Ponownie zapisuje adresu URL żądania.

Informacje o kluczu:

- Konfiguracja ta funkcja wymaga ustawienie następujących opcji:

Opcja|Opis
-|-
 Źródło & wzorzec | Te ustawienia definiują wzorzec identyfikatora URI żądania, który identyfikuje typ żądania, które może być ponowne napisanie. Będzie przebudować tylko żądania, którego adres URL spełnia oba następujące kryteria: <br/><br/>  - **Źródło (lub punkt dostępu do zawartości):** Wybierz ścieżkę względną, która identyfikuje serwer pochodzenia. Ta ścieżka jest _/XXXX/_ sekcji i nazwa punktu końcowego. <br/><br/> - **Źródło (wzorzec):** Wzorzec, który identyfikuje żądania za pomocą ścieżki względnej, musi być zdefiniowany. Ten wzorzec wyrażenia regularnego należy zdefiniować ścieżkę, która rozpoczyna się bezpośrednio po wybrana wcześniej dostępu do zawartości punktu (zobacz powyżej). <br/> Sprawdź, czy żądanie identyfikatora URI kryteria (czyli źródła & wzorca) uprzednio zdefiniowany jest zgodny z którykolwiek z warunków dopasowania zdefiniowane dla tej funkcji. Określ wzorzec; Jeśli używasz pustej wartości jako wzorzec wszystkie ciągi są dopasowywane.
 Miejsce docelowe  |Zdefiniuj względny adres URL, do którego powyżej żądań będzie przepisany przez: <br/>    1. Wybieranie punktu dostępu do zawartości, która identyfikuje serwer pochodzenia. <br/>    2. Definiowanie przy użyciu ścieżki względnej: <br/>        -Wzorzec wyrażenia regularnego <br/>        - [Zmienne HTTP](cdn-http-variables.md) <br/> <br/> Zastąp wartości przechwytywane we wzorcu źródła do wzorca docelowego przy użyciu ciągu $_n_ gdzie _n_ identyfikuje wartość według kolejności, w której został przechwycony. Na przykład $1 reprezentuje pierwszą wartość przechwycone we wzorcu źródła, gdy $2 reprezentuje drugiej wartości.

 Ta funkcja umożliwia POP do ponownego zapisywania adresów URL bez wykonywania tradycyjnych przekierowania. Oznacza to, że osoby żądającej otrzymuje ten sam kod odpowiedzi, tak, jakby poproszono nowych adresu URL.

**Przykładowy scenariusz 1**

W tym przykładzie pokazano, jak przekierowywanie adresu URL CNAME, który jest rozpoznawany jako ten podstawowy adres URL usługi CDN edge: http:\//marketing.azureedge.net/brochures/

Kwalifikowanie żądania nastąpi przekierowanie do tej krawędzi podstawowy adres URL CNAME: http:\//MyOrigin.azureedge.net/resources/

Ten adres URL przekierowania można osiągnąć przy użyciu następującej konfiguracji: ![Adres URL przekierowania](./media/cdn-rules-engine-reference/cdn-rules-engine-rewrite.png)

**Przykładowy scenariusz 2**

W tym przykładzie przedstawiono sposób przekierowania URL CNAME z wielkie litery na małe litery, używając wyrażeń regularnych w węzłach brzegowych.

Ten adres URL przekierowania można osiągnąć przy użyciu następującej konfiguracji: ![Adres URL przekierowania](./media/cdn-rules-engine-reference/cdn-rules-engine-to-lowercase.png)

**Najważniejsze kwestie:**

- Funkcja ponowne zapisywanie adresów URL definiuje żądania adresów URL, które będą przepisany. W rezultacie dopasowanie dodatkowe warunki nie są wymagane. Mimo że warunek dopasowania został zdefiniowany jako "Always", będzie przepisany tylko żądania, które wskazują folder "broszury" na "marketing" źródłem klienta.

- Segmenty adresu URL, które zostały przechwycone z żądania są dołączane do nowego adresu URL za pośrednictwem "$1".

#### <a name="compatibility"></a>Zgodność

Ta funkcja obejmuje spełniające kryteria, które muszą zostać spełnione, można było zastosować na żądanie. Aby uniknąć konfigurowania kryteriów dopasowania powodujące konflikt, ta funkcja jest niezgodna z następujących warunków dopasowania:

- JAKO liczba
- Źródło usługi CDN
- Adres IP klienta
- Źródłem klienta
- Schemat żądania
- Adres URL ścieżki katalogu
- Rozszerzenie ścieżki adresu URL
- Adres URL, nazwa_pliku ścieżki
- Literał ścieżki adresu URL
- Wyrażenie regularne ścieżki adresu URL
- Symbol wieloznaczny ścieżki adresu URL
- Adres URL zapytania literału
- Parametr zapytania adresu URL
- Adres URL zapytania z wyrażeniem regularnym
- Adres URL zapytania z symbolami wieloznacznymi

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="user-variable"></a>Zmiennej użytkownika

**Cel:** Tylko do użytku wewnętrznego.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

## <a name="next-steps"></a>Kolejne kroki

- [Dokumentacja aparatu reguł](cdn-verizon-premium-rules-engine-reference.md)
- [Wyrażenia warunkowe aparatu reguł](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Warunki dopasowań aparatu reguł](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Zastępowanie zachowania HTTP przy użyciu aparatu reguł](cdn-verizon-premium-rules-engine.md)
- [Omówienie usługi Azure CDN](cdn-overview.md)