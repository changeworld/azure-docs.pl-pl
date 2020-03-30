---
title: Funkcje aparatu azure CDN firmy Verizon Premium | Dokumenty firmy Microsoft
description: Dokumentacja referencyjna dla usługi Azure CDN z funkcji aparatu reguł Verizon Premium.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 9177ac544c83305ae95ad681d3dc9f84ac64ea36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247581"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-features"></a>Funkcje aparatu azure CDN firmy Verizon Premium

W tym artykule wymieniono szczegółowe opisy dostępnych funkcji [aparatu reguł](cdn-verizon-premium-rules-engine.md)usługi Azure Content Delivery Network (CDN).

Trzecią częścią reguły jest funkcja. Funkcja definiuje typ akcji, która jest stosowana do typu żądania, który jest identyfikowany przez zestaw warunków dopasowania.

## <a name="access-features"></a>Funkcje dostępu

Te funkcje są przeznaczone do kontrolowania dostępu do zawartości.

Nazwa | Przeznaczenie
-----|--------
[Odmowa dostępu (403)](#deny-access-403) | Określa, czy wszystkie żądania są odrzucane za pomocą 403 Zakazana odpowiedź.
[Auth tokenu](#token-auth) | Określa, czy uwierzytelnianie oparte na tokenie jest stosowane do żądania.
[Kod odmowy eru tokenu](#token-auth-denial-code) | Określa typ odpowiedzi, która jest zwracana do użytkownika, gdy żądanie zostanie odrzucone z powodu uwierzytelniania opartego na tokenie.
[Przypadek adresu URL auth ignoruje token](#token-auth-ignore-url-case) | Określa, czy porównania adresów URL dokonywane przez uwierzytelnianie oparte na tokenie są rozróżniane.
[Parametr Eruth tokenu](#token-auth-parameter) | Określa, czy należy zmienić nazwę parametru ciągu zapytania uwierzytelniania opartego na tokenie.

## <a name="caching-features"></a>Funkcje buforowania

Te funkcje są przeznaczone do dostosowywania, kiedy i jak zawartość jest buforowana.

Nazwa | Przeznaczenie
-----|--------
[Parametry przepustowości](#bandwidth-parameters) | Określa, czy parametry ograniczania przepustowości (na przykład ec_rate i ec_prebuf) są aktywne.
[Ograniczanie przepustowości](#bandwidth-throttling) | Ogranicza przepustowość dla odpowiedzi zapewnianej przez punkt obecności (POP).
[Pomij pamięć podręczną](#bypass-cache) | Określa, czy żądanie należy pominąć buforowania.
[Leczenie nagłówka kontroli pamięci podręcznej](#cache-control-header-treatment) | Steruje generowaniem `Cache-Control` nagłówków przez pop, gdy zewnętrzna funkcja Max-Age jest aktywna.
[Ciąg zapytania klucza pamięci podręcznej](#cache-key-query-string) | Określa, czy klucz pamięci podręcznej zawiera lub wyklucza parametry ciągu zapytania skojarzone z żądaniem.
[Przepisanie klucza pamięci podręcznej](#cache-key-rewrite) | Przepisuje klucz pamięci podręcznej skojarzony z żądaniem.
[Pełne wypełnienie pamięci podręcznej](#complete-cache-fill) | Określa, co się dzieje, gdy żądanie powoduje częściową chybienie pamięci podręcznej w pop.
[Kompresowanie typów plików](#compress-file-types) | Definiuje formaty plików skompresowanych na serwerze.
[Domyślny wewnętrzny wiek maksymalny](#default-internal-max-age) | Określa domyślny interwał maksymalnego wieku dla ponownego ukończenia serwera pop do źródła pamięci podręcznej serwera.
[Wygasa leczenie nagłówka](#expires-header-treatment) | Steruje generowaniem `Expires` nagłówków przez pop, gdy zewnętrzna funkcja Max-Age jest aktywna.
[Zewnętrzny wiek maksymalny](#external-max-age) | Określa maksymalny przedział wiekowy dla ponownego ukończenia pamięci podręcznej pop przeglądarki.
[Wymuszenie wewnętrznego max-age](#force-internal-max-age) | Określa maksymalny przedział wiekowy dla ponownego ukończenia serwera pop do źródła.
[Obsługa H.264 (pobieranie progresywne HTTP)](#h264-support-http-progressive-download) | Określa typy formatów plików H.264, które mogą być używane do przesyłania strumieniowego zawartości.
[Honor No-Cache Żądanie](#honor-no-cache-request) | Określa, czy żądania braku pamięci podręcznej klienta HTTP są przekazywane do serwera pochodzenia.
[Ignoruj niejmówętną pamięć podręczną origin](#ignore-origin-no-cache) | Określa, czy sieć CDN ignoruje niektóre dyrektywy obsługiwane z serwera pochodzenia.
[Ignoruj niezadowolne zakresy](#ignore-unsatisfiable-ranges) | Określa odpowiedź, która jest zwracana do klientów, gdy żądanie generuje 416 Żądany zakres nie sytą kod stanu.
[Wewnętrzny Max-Stale](#internal-max-stale) | Określa, jak długo po normalnym czasie wygaśnięcia buforowany zasób może być obsługiwany z punktu obecności, gdy punkt obecności nie może ponownie ocenić buforowanego zasobu na serwerze pochodzenia.
[Częściowe udostępnianie pamięci podręcznej](#partial-cache-sharing) | Określa, czy żądanie może generować częściowo buforowaną zawartość.
[Wstępnie zaawaluj zawartość buforowaną](#prevalidate-cached-content) | Określa, czy zawartość buforowana kwalifikuje się do wcześniejszego ponownego ponownego przeszacowania przed wygaśnięciem czasu wygaśnięcia.
[Odświeżanie plików pamięci podręcznej o zerowym bajcie](#refresh-zero-byte-cache-files) | Określa sposób obsługi żądania klienta HTTP dla zasobu pamięci podręcznej 0-bajtowego przez usługi podręczne.
[Ustawianie kodów stanu w pamięci podręcznej](#set-cacheable-status-codes) | Definiuje zestaw kodów stanu, które mogą spowodować buforowaną zawartość.
[Nieaktualna dostawa treści w dniu błędu](#stale-content-delivery-on-error) | Określa, czy wygasła zawartość buforowana jest dostarczana, gdy wystąpi błąd podczas ponownego sprawdzaniem ważności pamięci podręcznej lub podczas pobierania żądanej zawartości z serwera pochodzenia klienta.
[Przestarzałe podczas ponownego unieważnienia](#stale-while-revalidate) | Zwiększa wydajność, zezwalając usługi YKS do obsługi starych klientów do żądania podczas ponownego oceny ma miejsce.

## <a name="comment-feature"></a>Funkcja komentowania

Ta funkcja ma na celu dostarczenie dodatkowych informacji w ramach reguły.

Nazwa | Przeznaczenie
-----|--------
[Komentarz](#comment) | Umożliwia dodawanie notatki w obrębie reguły.

## <a name="header-features"></a>Funkcje nagłówka

Te funkcje są przeznaczone do dodawania, modyfikowania lub usuwania nagłówków z żądania lub odpowiedzi.

Nazwa | Przeznaczenie
-----|--------
[Nagłówek odpowiedzi na wiek](#age-response-header) | Określa, czy nagłówek odpowiedzi wiekowej znajduje się w odpowiedzi wysłanej do żądacza.
[Nagłówki odpowiedzi pamięci podręcznej debugowania](#debug-cache-response-headers) | Określa, czy odpowiedź może zawierać nagłówek odpowiedzi X-EC-Debug, który zawiera informacje na temat zasad pamięci podręcznej dla żądanego zasobu.
[Modyfikowanie nagłówka żądania klienta](#modify-client-request-header) | Zastępuje, dołącza lub usuwa nagłówek z żądania.
[Modyfikowanie nagłówka odpowiedzi klienta](#modify-client-response-header) | Zastępuje, dołącza lub usuwa nagłówek z odpowiedzi.
[Ustawianie niestandardowego nagłówka IP klienta](#set-client-ip-custom-header) | Umożliwia adres IP żądającego klienta, który ma zostać dodany do żądania jako nagłówka żądania niestandardowego.

## <a name="logging-features"></a>Funkcje rejestrowania

Funkcje te mają na celu dostosowanie danych przechowywanych w nieprzetworzonych plikach dziennika.

Nazwa | Przeznaczenie
-----|--------
[Pole dziennika niestandardowego 1](#custom-log-field-1) | Określa format i zawartość przypisaną do pola dziennika niestandardowego w nieprzetworzonym pliku dziennika.
[Ciąg zapytania dziennika](#log-query-string) | Określa, czy ciąg zapytania jest przechowywany wraz z adresem URL w dziennikach dostępu.


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

## <a name="origin-features"></a>Funkcje Origin

Te funkcje są przeznaczone do kontrolowania sposobu komunikacji sieci CDN z serwerem pochodzenia.

Nazwa | Przeznaczenie
-----|--------
[Maksymalna liczba żądań keep-alive](#maximum-keep-alive-requests) | Definiuje maksymalną liczbę żądań dla połączenia Keep-Alive przed jego zamknięciem.
[Nagłówki specjalne serwera proxy](#proxy-special-headers) | Definiuje zestaw nagłówków żądań specyficznych dla sieci CDN, które są przekazywane z punktu obecności do serwera pochodzenia.

## <a name="specialty-features"></a>Cechy specjalne

Funkcje te zapewniają zaawansowane funkcje dla zaawansowanych użytkowników.

Nazwa | Przeznaczenie
-----|--------
[Metody HTTP z owym skrywalnie](#cacheable-http-methods) | Określa zestaw dodatkowych metod HTTP, które mogą być buforowane w sieci.
[Rozmiar treści żądania buforowanego](#cacheable-request-body-size) | Definiuje próg określania, czy odpowiedź POST może być buforowana.
[Zmienna użytkownika](#user-variable) | Tylko do użytku wewnętrznego.

## <a name="url-features"></a>Funkcje adresu URL

Te funkcje umożliwiają przekierowanie lub przepisanie żądania do innego adresu URL.

Nazwa | Przeznaczenie
-----|--------
[Śledź przekierowania](#follow-redirects) | Określa, czy żądania mogą być przekierowywane do nazwy hosta zdefiniowanej w nagłówku Lokalizacja zwracanym przez serwer pochodzenia klienta.
[Przekierowanie adresu URL](#url-redirect) | Przekierowuje żądania za pośrednictwem nagłówka Lokalizacja.
[Ponowne zapisywanie adresów URL](#url-rewrite)  | Przepisuje adres URL żądania.

## <a name="azure-cdn-from-verizon-premium-rules-engine-features-reference"></a>Odwołanie do funkcji aparatu azure CDN z reguł Verizon Premium

---

### <a name="age-response-header"></a>Nagłówek odpowiedzi na wiek

**Cel:** Określa, czy nagłówek odpowiedzi wiekowej znajduje się w odpowiedzi wysłanej do żądacza.

Wartość|Wynik
--|--
Enabled (Włączony) | Nagłówek odpowiedzi wiekowej znajduje się w odpowiedzi wysłanej do żądacza.
Disabled (Wyłączony) | Nagłówek Odpowiedzi wiekowej jest wykluczony z odpowiedzi wysłanej do żądacza.

**Domyślne zachowanie:** wyłączone.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bandwidth-parameters"></a>Parametry przepustowości

**Przeznaczenie:** Określa, czy parametry ograniczania przepustowości (na przykład ec_rate i ec_prebuf) są aktywne.

Parametry ograniczania przepustowości określają, czy szybkość transferu danych dla żądania klienta jest ograniczona do szybkości niestandardowej.

Wartość|Wynik
--|--
Enabled (Włączony)|Umożliwia punktówom kontroli otchłani żądania ograniczania przepustowości.
Disabled (Wyłączony)|Powoduje, że dostawcy usług niekierowujących ignorują parametry ograniczania przepustowości. Żądana zawartość jest obsługiwana normalnie (czyli bez ograniczania przepustowości).

**Domyślne zachowanie:** Włączone.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bandwidth-throttling"></a>Ograniczanie przepustowości

**Przeznaczenie:** Ogranicza przepustowość odpowiedzi zapewnianej przez pasy ruchu.

Obie następujące opcje muszą być zdefiniowane, aby prawidłowo skonfigurować ograniczanie przepustowości.

Opcja|Opis
--|--
Kbyty na sekundę|Ustaw tę opcję na maksymalną przepustowość (Kb na sekundę), która może służyć do dostarczania odpowiedzi.
Prebuf sekundy|Ustaw tę opcję na liczbę sekund, przez które dostawcy usług otchłań będą czekać, aż przepustowość zostanie ograniczona. Celem tego okresu nieograniczonej przepustowości jest zapobieganie zacinaniu się lub buforowaniu odtwarzacza multimedialnego z powodu ograniczania przepustowości.

**Domyślne zachowanie:** Wyłączone.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bypass-cache"></a>Pomij pamięć podręczną

**Przeznaczenie:** Określa, czy żądanie należy pominąć buforowania.

Wartość|Wynik
--|--
Enabled (Włączony)|Powoduje, że wszystkie żądania spadają do serwera pochodzenia, nawet jeśli zawartość była wcześniej buforowana na punktach widokowych.
Disabled (Wyłączony)|Powoduje, że dostawcy usług krypkowych do buforowania zasobów zgodnie z zasadami pamięci podręcznej zdefiniowanymi w nagłówkach odpowiedzi.

**Domyślne zachowanie:**

- **Duży protokół HTTP:** Wyłączone

<!---
- **ADN:** Enabled

--->

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cacheable-http-methods"></a>Metody HTTP z owym skrywalnie

**Przeznaczenie:** Określa zestaw dodatkowych metod HTTP, które mogą być buforowane w sieci.

Najważniejsze informacje:

- Ta funkcja zakłada, że odpowiedzi GET powinny być zawsze buforowane. W rezultacie metoda GET HTTP nie powinna być uwzględniana podczas ustawiania tej funkcji.
- Ta funkcja obsługuje tylko metodę POST HTTP. Włącz buforowanie odpowiedzi POST, ustawiając tę funkcję na `POST`.
- Domyślnie buforowane są tylko żądania, których treść jest mniejsza niż 14 Kb. Użyj funkcji Rozmiar treści żądania w pamięci podręcznej, aby ustawić maksymalny rozmiar treści żądania.

**Domyślne zachowanie:** Tylko odpowiedzi GET są buforowane.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cacheable-request-body-size"></a>Rozmiar treści żądania buforowanego

**Przeznaczenie:** Definiuje próg określania, czy odpowiedź POST może być buforowana.

Próg ten jest określany przez określenie maksymalnego rozmiaru treści żądania. Żądania, które zawierają większą treść żądania nie są buforowane.

Najważniejsze informacje:

- Ta funkcja ma zastosowanie tylko wtedy, gdy odpowiedzi POST kwalifikują się do buforowania. Użyj funkcji Metody HTTP w pamięci podręcznej, aby włączyć buforowanie żądań POST.
- Organ wniosku bierze się pod uwagę przy:
    - x-www-formularz-urlencoded wartości
    - Zapewnienie unikatowego klucza pamięci podręcznej
- Definiowanie dużego maksymalnego rozmiaru treści żądania może mieć wpływ na wydajność dostarczania danych.
    - **Zalecana wartość:** 14 Kb
    - **Minimalna wartość:** 1 Kb

**Domyślne zachowanie:** 14 Kb

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-control-header-treatment"></a>Leczenie nagłówka kontroli pamięci podręcznej

**Przeznaczenie:** Steruje generowaniem `Cache-Control` nagłówków przez pop, gdy zewnętrzna funkcja Max-Age jest aktywna.

Najprostszym sposobem osiągnięcia tego typu konfiguracji jest umieszczenie zewnętrznych max-age i cache-control funkcji leczenia nagłówka w tej samej instrukcji.

Wartość|Wynik
--|--
Zastąp|Zapewnia następujące akcje:<br/> - Zastępuje `Cache-Control` nagłówek generowany przez serwer pochodzenia. <br/>- Dodaje `Cache-Control` nagłówek produkowany przez funkcję Zewnętrzny Max-Age do odpowiedzi.
Przejść przez|Zapewnia, że `Cache-Control` nagłówek produkowany przez funkcję Zewnętrzny maksymalny wiek nigdy nie zostanie dodany do odpowiedzi. <br/> Jeśli serwer pochodzenia tworzy `Cache-Control` nagłówek, przechodzi przez użytkownika końcowego. <br/> Jeśli serwer pochodzenia nie `Cache-Control` tworzy nagłówka, ta opcja może spowodować, `Cache-Control` że nagłówek odpowiedzi nie zawiera nagłówka.
Dodaj, jeśli brakuje|Jeśli `Cache-Control` nagłówek nie został odebrany z serwera pochodzenia, ta opcja dodaje `Cache-Control` nagłówek wyprodukowany przez funkcję Zewnętrzny wiek maksymalny. Ta opcja jest przydatna do zapewnienia, że `Cache-Control` wszystkie zasoby są przypisane nagłówek.
Remove| Ta opcja gwarantuje, `Cache-Control` że nagłówek nie jest dołączony do odpowiedzi nagłówka. Jeśli `Cache-Control` nagłówek został już przypisany, zostanie on usunięty z odpowiedzi nagłówka.

**Domyślne zachowanie:** Zastąpić.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-key-query-string"></a>Ciąg zapytania klucza pamięci podręcznej

**Przeznaczenie:** Określa, czy klucz pamięci podręcznej zawiera lub wyklucza parametry ciągu zapytania skojarzone z żądaniem.

Najważniejsze informacje:

- Określ jedną lub więcej nazw parametrów ciągu kwerendy i oddziel każdą nazwę parametru pojedynczą spacją.
- Ta funkcja określa, czy parametry ciągu zapytania są uwzględniane lub wykluczane z klucza pamięci podręcznej. Dodatkowe informacje są podane dla każdej opcji w poniższej tabeli.

Typ|Opis
--|--
 Uwzględnij|  Wskazuje, że każdy określony parametr powinien być uwzględniony w kluczu pamięci podręcznej. Unikatowy klucz pamięci podręcznej jest generowany dla każdego żądania, który zawiera unikatową wartość parametru ciągu zapytania zdefiniowanego w tej funkcji.
 Uwzględnij wszystkie  |Wskazuje, że dla każdego żądania do zasobu, który zawiera unikatowy ciąg zapytania, jest tworzony unikatowy klucz pamięci podręcznej. Ten typ konfiguracji nie jest zazwyczaj zalecane, ponieważ może to prowadzić do niewielki procent trafień pamięci podręcznej. Mała liczba trafień pamięci podręcznej zwiększa obciążenie serwera pochodzenia, ponieważ musi obsługiwać więcej żądań. Ta konfiguracja powiela zachowanie buforowania znane jako "unikatowa pamięć podręczna" na stronie buforowanie ciągiem zapytań.
 Exclude | Wskazuje, że tylko określone parametry są wykluczone z klucza pamięci podręcznej. Wszystkie inne parametry ciągu kwerendy są zawarte w kluczu pamięci podręcznej.
 Wyklucz wszystkie  |Wskazuje, że wszystkie parametry ciągu kwerendy są wykluczone z klucza pamięci podręcznej. Ta konfiguracja powiela domyślne zachowanie buforowania "standardowej pamięci podręcznej" na stronie buforowanie ciągiem zapytań.  

Aparat reguł umożliwia dostosowanie sposobu, w jaki buforowanie ciągów zapytań jest implementowane. Na przykład można określić, że buforowanie ciągów kwerend jest wykonywane tylko w niektórych lokalizacjach lub typach plików.

Aby zduplikować zachowanie buforowania ciągów zapytań "bez pamięci podręcznej" na stronie Buforowanie ciągów zapytań, utwórz regułę zawierającą warunek dopasowania symboli wieloznacznych kwerendy URL i funkcję Bypass Cache. Ustaw warunek dopasowania symbolu wieloznacznego kwerendy URL na gwiazdkę (*).

>[!IMPORTANT]
> Jeśli autoryzacja tokenu jest włączona dla dowolnej ścieżki na tym koncie, tryb standardowej pamięci podręcznej jest jedynym trybem, który może być używany do buforowania ciągów zapytań. Aby uzyskać więcej informacji, zobacz [Control Azure CDN caching behavior with query strings](cdn-query-string-premium.md) (Sterowanie zachowaniem buforowania usługi CDN za pomocą ciągów zapytań).

#### <a name="sample-scenarios"></a>Przykładowe scenariusze

Następujące przykładowe użycie tej funkcji zapewnia przykładowe żądanie i domyślny klucz pamięci podręcznej:

- **Przykładowe żądanie:** http://wpc.0001.&lt;Domain&gt;/800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01
- **Domyślny klucz pamięci podręcznej:** /800001/Origin/folder/asset.htm

##### <a name="include"></a>Uwzględnij

Przykładowa konfiguracja:

- **Typ:** Obejmują
- **Parametr(-y):** język

Ten typ konfiguracji wygenerowałby następujący klucz pamięci podręcznej parametru ciągu kwerendy:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="include-all"></a>Uwzględnij wszystkie

Przykładowa konfiguracja:

- **Typ:** Uwzględnij wszystkie

Ten typ konfiguracji wygenerowałby następujący klucz pamięci podręcznej parametru ciągu kwerendy:

    /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01

##### <a name="exclude"></a>Exclude

Przykładowa konfiguracja:

- **Typ:** Wykluczyć
- **Parametr(-y):** identyfikator użytkownika sesowanego

Ten typ konfiguracji wygenerowałby następujący klucz pamięci podręcznej parametru ciągu kwerendy:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="exclude-all"></a>Wyklucz wszystkie

Przykładowa konfiguracja:

- **Typ:** Wyklucz wszystkie

Ten typ konfiguracji wygenerowałby następujący klucz pamięci podręcznej parametru ciągu kwerendy:

    /800001/Origin/folder/asset.htm

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-key-rewrite"></a>Przepisanie klucza pamięci podręcznej

**Przeznaczenie:** Przepisuje klucz pamięci podręcznej skojarzony z żądaniem.

Klucz pamięci podręcznej jest ścieżką względną, która identyfikuje zasób na potrzeby buforowania. Innymi słowy serwery sprawdzają, czy w pamięci podręcznej wersji zasobu zgodnie z jego ścieżką zdefiniowaną przez jego klucz pamięci podręcznej.

Skonfiguruj tę funkcję, definiując obie następujące opcje:

Opcja|Opis
--|--
Oryginalna ścieżka| Zdefiniuj ścieżkę względną do typów żądań, których klucz pamięci podręcznej jest przepisany. Ścieżkę względną można zdefiniować, wybierając podstawową ścieżkę pochodzenia, a następnie definiując wzorzec wyrażenia regularnego.
Nowa ścieżka|Zdefiniuj ścieżkę względną dla nowego klucza pamięci podręcznej. Ścieżkę względną można zdefiniować, wybierając podstawową ścieżkę pochodzenia, a następnie definiując wzorzec wyrażenia regularnego. Ta ścieżka względna może być dynamicznie konstruowana za pomocą [zmiennych HTTP](cdn-http-variables.md).

**Domyślne zachowanie:** Klucz pamięci podręcznej żądania jest określany przez identyfikator URI żądania.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="comment"></a>Komentarz

**Przeznaczenie:** Umożliwia dodawanie notatki w obrębie reguły.

Jednym z zastosowań tej funkcji jest dostarczenie dodatkowych informacji na temat ogólnego celu reguły lub dlaczego do reguły dodano określony warunek dopasowania lub funkcję.

Najważniejsze informacje:

- Można określić maksymalnie 150 znaków.
- Używaj tylko znaków alfanumerycznych.
- Ta funkcja nie wpływa na zachowanie reguły. Jest to po prostu przeznaczone do zapewnienia obszaru, w którym można podać informacje do wykorzystania w przyszłości lub które mogą pomóc w rozwiązywaniu problemów z regułą.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="complete-cache-fill"></a>Pełne wypełnienie pamięci podręcznej

**Przeznaczenie:** Określa, co się dzieje, gdy żądanie powoduje częściową chybienie pamięci podręcznej w pop.

Częściowa nietrafiona pamięć podręczna opisuje stan pamięci podręcznej zasobu, który nie został całkowicie pobrany do punktu obecności. Jeśli zasób jest tylko częściowo buforowany na pop, następne żądanie dla tego zasobu zostanie ponownie przekazane do serwera pochodzenia.
<!---
This feature is not available for the ADN platform. The typical traffic on this platform consists of relatively small assets. The size of the assets served through these platforms helps mitigate the effects of partial cache misses, since the next request will typically result in the asset being cached on that POP.

--->
Częściowa miss pamięci podręcznej zazwyczaj występuje po użytkownik przerywa pobieranie lub dla zasobów, które są wymagane wyłącznie przy użyciu żądań zakresu HTTP. Ta funkcja jest najbardziej przydatna w przypadku dużych zasobów, które zazwyczaj nie są pobierane od początku do końca (na przykład filmy). W rezultacie ta funkcja jest domyślnie włączona na dużej platformie HTTP. Jest wyłączona na wszystkich innych platformach.

Zachowaj domyślną konfigurację platformy HTTP Large, ponieważ zmniejsza obciążenie serwera pochodzenia klienta i zwiększa szybkość, z jaką klienci pobierają zawartość.

Wartość|Wynik
--|--
Enabled (Włączony)|Przywraca domyślne zachowanie. Domyślnym zachowaniem jest wymuszenie, aby punkt obecności zainicjował pobieranie zasobu w tle z serwera pochodzenia. Po tym zasób będzie w lokalnej pamięci podręcznej pop.
Disabled (Wyłączony)|Uniemożliwia pop wykonywania pobierania w tle dla zasobu. W rezultacie następne żądanie dla tego zasobu z tego regionu powoduje, że pop zażądać go od serwera pochodzenia klienta.

**Domyślne zachowanie:** Włączone.

#### <a name="compatibility"></a>Zgodność

Ze względu na sposób śledzenia ustawień pamięci podręcznej ta funkcja nie może być skojarzona z następującymi warunkami dopasowania:

- Numer AS
- Adres IP klienta
- Parametr pliku cookie
- Parametr pliku cookie Regex
- Kraj
- Urządzenie
- Nazwa Cname krawędzi Microsoftu
- Domena odsyłacająca
- Dosłowny nagłówek żądania
- Żądanie Regex nagłówka
- Symbol wieloznaczny nagłówka żądania
- Request, metoda
- Schemat żądania
- Literał zapytania adresu URL
- Regex kwerendy URL
- Symbol wieloznaczny kwerendy URL
- Parametr kwerendy url

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="compress-file-types"></a>Kompresowanie typów plików

**Przeznaczenie:** Definiuje formaty plików skompresowanych na serwerze.

Format pliku można określić przy użyciu jego typu nośnika internetowego (na przykład Typ zawartości). Typ nośnika internetowego to metadane niezależne od platformy, które umożliwiają serwerom identyfikację formatu pliku określonego zasobu. Poniżej znajduje się lista popularnych typów nośników internetowych.

Typ nośnika internetowego|Opis
--|--
tekst/zwykły|Pliki w postaci zwykłego tekstu
text/html| Pliki HTML
text/css|Kaskadowe arkusze stylów (CSS)
aplikacja/x-javascript|Javascript
aplikacja/javascript|Javascript

Najważniejsze informacje:

- Określ wiele typów nośników internetowych, wyznaczając każdy z nich z jednym miejscem.
- Ta funkcja kompresuje tylko zasoby, których rozmiar jest mniejszy niż 1 MB. Większe zasoby nie są kompresowane przez serwery.
- Niektóre typy zawartości, takie jak obrazy, materiały wideo i zasoby multimediów audio (na przykład JPG, MP3, MP4 itp.), są już skompresowane. Ponieważ dodatkowa kompresja dla tych typów zasobów nie zmniejsza znacząco rozmiaru pliku, zaleca się, aby nie włączać kompresji na nich.
- Symbole wieloznaczne, takie jak gwiazdki, nie są obsługiwane.
- Przed dodaniem tej funkcji do reguły należy ustawić opcję Wyłączona kompresja na stronie Kompresja dla platformy, do której stosowana jest ta reguła.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="custom-log-field-1"></a>Pole dziennika niestandardowego 1

**Przeznaczenie:** Określa format i zawartość, która zostanie przypisana do pola dziennika niestandardowego w nieprzetworzonym pliku dziennika.

To pole niestandardowe umożliwia określenie, które wartości nagłówka żądania i odpowiedzi są przechowywane w plikach dziennika.

Domyślnie pole dziennika niestandardowego jest nazywane "x-ec_custom-1". Nazwę tego pola można dostosować na stronie Ustawienia nieprzetworzonego dziennika.

Format określania nagłówków żądań i odpowiedzi jest zdefiniowany w następujący sposób:

Typ nagłówka|Format|Przykłady
-|-|-
Nagłówek żądania|`%{[RequestHeader]()}[i]()` | %{Zaakceptuj kodowanie}i <br/> {Referrer}i <br/> %{Autoryzacja}i
Nagłówek odpowiedzi|`%{[ResponseHeader]()}[o]()`| %{Wiek}o <br/> %{Typ zawartości}o <br/> %{Plik cookie}o

Najważniejsze informacje:

- Niestandardowe pole dziennika może zawierać dowolną kombinację pól nagłówka i zwykłego tekstu.
- Prawidłowe znaki dla tego pola są następujące: alfanumeryczne (0-9, a-z i A-Z), kreski, dwukropek, średniki, apostrofy, przecinki, kropki, podkreślenia, znaki równości, nawiasy, nawiasy i spacje. Symbol procentowy i nawiasy klamrowe są dozwolone tylko wtedy, gdy są używane do określania pola nagłówka.
- Pisownia dla każdego określonego pola nagłówka musi być zgodna z żądaną nazwą nagłówka żądania/odpowiedzi.
- Jeśli chcesz określić wiele nagłówków, użyj separatora, aby wskazać każdy nagłówek. Na przykład można użyć skrótu dla każdego nagłówka:
    - AE: %{Akceptuj kodowanie}i A: %{Autoryzacja}i CT: %{Typ zawartości}o

**Wartość domyślna:** -

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---
### <a name="debug-cache-response-headers"></a>Nagłówki odpowiedzi pamięci podręcznej debugowania

**Przeznaczenie:** Określa, czy odpowiedź może zawierać [nagłówki odpowiedzi X-EC-Debug](cdn-http-debug-headers.md), która zawiera informacje na temat zasad pamięci podręcznej żądanego zasobu.

Nagłówki odpowiedzi pamięci podręcznej debugowania zostaną uwzględnione w odpowiedzi, gdy oba z poniższych są spełnione:

- Funkcja Nagłówki odpowiedzi debugowania pamięci podręcznej została włączona w określonym żądaniu.
- Określone żądanie definiuje zestaw nagłówków odpowiedzi pamięci podręcznej debugowania, które zostaną uwzględnione w odpowiedzi.

Nagłówki odpowiedzi pamięci podręcznej debugowania mogą być wymagane przez dołączenie następującego nagłówka i określonych dyrektyw w żądaniu:

`X-EC-Debug: _&lt;Directive1&gt;_,_&lt;Directive2&gt;_,_&lt;DirectiveN&gt;_`

**Przykład:**

X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state

Wartość|Wynik
-|-
Enabled (Włączony)|Żądania nagłówków odpowiedzi pamięci podręcznej debugowania zwróci odpowiedź, która zawiera nagłówek X-EC-Debug.
Disabled (Wyłączony)|Nagłówek odpowiedzi X-EC-Debug zostanie wykluczony z odpowiedzi.

**Domyślne zachowanie:** Wyłączone.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---
### <a name="default-internal-max-age"></a>Domyślny wewnętrzny wiek maksymalny

**Przeznaczenie:** Określa domyślny interwał maksymalnego wieku dla ponownego ukończenia serwera pop do źródła pamięci podręcznej serwera. Innymi słowy, czas, który minie przed pop sprawdzi, czy buforowany zasób pasuje do zasobu przechowywanego na serwerze pochodzenia.

Najważniejsze informacje:

- Ta akcja będzie mieć miejsce tylko dla odpowiedzi z serwera pochodzenia, który `Cache-Control` `Expires` nie przypisał wskazania max-age w nagłówku lub.
- Ta akcja nie będzie mieć miejsca dla zasobów, które nie są uważane za buforowane.
- Ta akcja nie wpływa na ponowne zadanie ważności pamięci podręcznej w przeglądarce do pamięci podręcznej POP. Te typy ponownych ważności są `Cache-Control` `Expires` określane przez nagłówki lub wysyłane do przeglądarki, które można dostosować za pomocą funkcji Zewnętrzna maksymalna wiek.
- Wyniki tej akcji nie mają zauważalnego wpływu na nagłówki odpowiedzi i zawartość zwróconą z punktami odpowiedzi dla zawartości, ale może mieć wpływ na ilość ruchu ponownego badania ważności wysyłanego z punktów dostępu do serwera pochodzenia.
- Skonfiguruj tę funkcję przez:
    - Wybór kodu stanu, dla którego można zastosować domyślny wewnętrzny maksymalny wiek.
    - Określanie wartości całkowitej, a następnie wybranie żądanej jednostki czasu (na przykład sekund, minut, godzin itp.). Ta wartość definiuje domyślny wewnętrzny interwał maksymalnego wieku.

- Ustawienie jednostki czasu na "Off" spowoduje przypisanie domyślnego wewnętrznego przedziału maksymalnego wieku wynoszącego 7 dni dla żądań, które nie zostały przypisane do wskazania maksymalnego wieku w ich `Cache-Control` nagłówku. `Expires`

**Wartość domyślna:** 7 dni

#### <a name="compatibility"></a>Zgodność

Ze względu na sposób śledzenia ustawień pamięci podręcznej ta funkcja nie może być skojarzona z następującymi warunkami dopasowania:
- Numer AS
- Adres IP klienta
- Parametr pliku cookie
- Parametr pliku cookie Regex
- Kraj
- Urządzenie
- Nazwa Cname krawędzi
- Domena odsyłacająca
- Dosłowny nagłówek żądania
- Żądanie Regex nagłówka
- Symbol wieloznaczny nagłówka żądania
- Request, metoda
- Schemat żądania
- Literał zapytania adresu URL
- Regex kwerendy URL
- Symbol wieloznaczny kwerendy URL
- Parametr kwerendy url

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="deny-access-403"></a>Odmowa dostępu (403)

**Cel:** Określa, czy wszystkie żądania są odrzucane za pomocą 403 Zakazana odpowiedź.

Wartość | Wynik
------|-------
Enabled (Włączony)| Powoduje, że wszystkie żądania, które spełniają kryteria dopasowania, są odrzucane za pomocą 403 Zakazana odpowiedź.
Disabled (Wyłączony)| Przywraca domyślne zachowanie. Domyślnym zachowaniem jest umożliwienie serwerowi pochodzenia określenia typu odpowiedzi, która zostanie zwrócona.

**Domyślne zachowanie:** Wyłączone

> [!TIP]
   > Jednym z możliwych zastosowań tej funkcji jest skojarzenie go z warunkiem dopasowania nagłówka żądania, aby zablokować dostęp do odsyłaczy HTTP, które używają wbudowanych łączy do zawartości.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="expires-header-treatment"></a>Wygasa leczenie nagłówka

**Przeznaczenie:** Steruje generowaniem `Expires` nagłówków przez pop, gdy zewnętrzna funkcja Max-Age jest aktywna.

Najprostszym sposobem osiągnięcia tego typu konfiguracji jest umieszczenie zewnętrznych max-age i wygasa leczenie nagłówka funkcje w tej samej instrukcji.

Wartość|Wynik
--|--
Zastąp|Zapewnia, że zostaną podjęte następujące działania:<br/>- Zastępuje `Expires` nagłówek generowany przez serwer pochodzenia.<br/>- Dodaje `Expires` nagłówek produkowany przez funkcję Zewnętrzny Max-Age do odpowiedzi.
Przejść przez|Zapewnia, że `Expires` nagłówek produkowany przez funkcję Zewnętrzny maksymalny wiek nigdy nie zostanie dodany do odpowiedzi. <br/> Jeśli serwer pochodzenia tworzy `Expires` nagłówek, przejdzie przez użytkownika końcowego. <br/>Jeśli serwer pochodzenia nie `Expires` generuje nagłówka, ta opcja może spowodować, `Expires` że nagłówek odpowiedzi nie zawiera nagłówka.
Dodaj, jeśli brakuje| Jeśli `Expires` nagłówek nie został odebrany z serwera pochodzenia, ta opcja dodaje `Expires` nagłówek wyprodukowany przez funkcję Zewnętrzny wiek maksymalny. Ta opcja jest przydatna do zapewnienia, że wszystkie `Expires` zasoby zostaną przypisane nagłówek.
Remove| Zapewnia, że `Expires` nagłówek nie jest dołączony do odpowiedzi nagłówka. Jeśli `Expires` nagłówek został już przypisany, zostanie on usunięty z odpowiedzi nagłówka.

**Domyślne zachowanie:** Zastąpić

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="external-max-age"></a>Zewnętrzny wiek maksymalny

**Przeznaczenie:** Określa maksymalny przedział wiekowy dla ponownego ukończenia pamięci podręcznej pop przeglądarki. Innymi słowy, czas, który minie, zanim przeglądarka może sprawdzić nową wersję zasobu z punktu obecności.

Włączenie tej funkcji `Cache-Control: max-age` spowoduje `Expires` wygenerowanie i wygenerowanie nagłówków z usług podręcznych i wysłanie ich do klienta HTTP. Domyślnie te nagłówki zastąpią te nagłówki utworzone przez serwer pochodzenia. Jednak leczenie nagłówka kontroli pamięci podręcznej i wygasa funkcji leczenia nagłówka może służyć do zmiany tego zachowania.

Najważniejsze informacje:

- Ta akcja nie wpływa na ponowne zadośćuczynienia pamięci podręcznej serwera pop do źródła. Te typy ponownych zatwierdzeń `Cache-Control` `Expires` są określane przez nagłówki i otrzymane z serwera pochodzenia i można je dostosować za pomocą domyślnych wewnętrznych funkcji Max-Age i Force Internal Max-Age.
- Skonfiguruj tę funkcję, określając wartość całkowitą i wybierając żądaną jednostkę czasu (na przykład sekundy, minuty, godziny itp.).
- Ustawienie tej funkcji na wartość ujemną powoduje, `Expires` że punktów usługowych do wysyłania `Cache-Control: no-cache` i czas, który jest ustawiony w przeszłości z każdej odpowiedzi do przeglądarki. Mimo że klient HTTP nie będzie buforować odpowiedzi, to ustawienie nie wpłynie na zdolność punktów dostępu do buforowania odpowiedzi z serwera pochodzenia.
- Ustawienie jednostki czasu na "Off" spowoduje wyłączenie tej funkcji. Nagłówki `Cache-Control` `Expires` i buforowane z odpowiedzią serwera pochodzenia będą przekazywane do przeglądarki.

**Domyślne zachowanie:** Wył.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="follow-redirects"></a>Śledź przekierowania

**Przeznaczenie:** Określa, czy żądania mogą być przekierowywane do nazwy hosta zdefiniowanej w nagłówku Lokalizacja zwracanym przez serwer pochodzenia klienta.

Najważniejsze informacje:

- Żądania mogą być przekierowywane tylko do krawędzi CNAMEs, które odpowiadają tej samej platformie.

Wartość|Wynik
-|-
Enabled (Włączony)|Żądania mogą być przekierowywane.
Disabled (Wyłączony)|Żądania nie zostaną przekierowane.

**Domyślne zachowanie:** Wyłączone.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="force-internal-max-age"></a>Wymuszenie wewnętrznego max-age

**Przeznaczenie:** Określa maksymalny przedział wiekowy dla ponownego ukończenia serwera pop do źródła. Innymi słowy, czas, który minie, zanim punkt obecności może sprawdzić, czy buforowany zasób pasuje do zasobu przechowywanego na serwerze pochodzenia.

Najważniejsze informacje:

- Ta funkcja zastąpi maksymalny przedział wiekowy `Cache-Control` `Expires` zdefiniowany w nagłówkach lub nagłówków generowanych z serwera pochodzenia.
- Ta funkcja nie wpływa na ponowne zadanie ważności pamięci podręcznej w przeglądarce do pamięci podręcznej POP. Te typy ponownych ważności są `Cache-Control` `Expires` określane przez nagłówki lub wysyłane do przeglądarki.
- Ta funkcja nie ma zauważalnego wpływu na odpowiedź dostarczoną przez pop do żądacza. Może to jednak mieć wpływ na ilość ruchu ponownego zawładania ważności wysyłanego z punktów dostępu do serwera pochodzenia.
- Skonfiguruj tę funkcję przez:
    - Wybór kodu stanu, dla którego zostanie zastosowany wewnętrzny maksymalny wiek.
    - Określanie wartości całkowitej i wybieranie żądanej jednostki czasu (na przykład sekund, minut, godzin itp.). Ta wartość definiuje maksymalny interwał żądania.

- Ustawienie jednostki czasu na "Off" wyłącza tę funkcję. Wewnętrzny przedział maksymalny wiek nie zostanie przypisany do żądanych zasobów. Jeśli oryginalny nagłówek nie zawiera instrukcji buforowania, zasób zostanie buforowany zgodnie z aktywnym ustawieniem w domyślnej funkcji Wewnętrznej maksymalnej wieku.

**Domyślne zachowanie:** Wył.

#### <a name="compatibility"></a>Zgodność

Ze względu na sposób śledzenia ustawień pamięci podręcznej ta funkcja nie może być skojarzona z następującymi warunkami dopasowania:
- Numer AS
- Adres IP klienta
- Parametr pliku cookie
- Parametr pliku cookie Regex
- Kraj
- Urządzenie
- Nazwa Cname krawędzi
- Domena odsyłacająca
- Dosłowny nagłówek żądania
- Żądanie Regex nagłówka
- Symbol wieloznaczny nagłówka żądania
- Request, metoda
- Schemat żądania
- Literał zapytania adresu URL
- Regex kwerendy URL
- Symbol wieloznaczny kwerendy URL
- Parametr kwerendy url

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="h264-support-http-progressive-download"></a>Obsługa H.264 (pobieranie progresywne HTTP)

**Przeznaczenie:** Określa typy formatów plików H.264, które mogą być używane do przesyłania strumieniowego zawartości.

Najważniejsze informacje:

- Zdefiniuj zestaw dozwolonych rozszerzeń nazwy plików H.264 rozdzielanych przestrzenią w opcji Rozszerzenia plików. Opcja Rozszerzenia plików zastąpi zachowanie domyślne. Obsługa plików MP4 i F4V umożliwia uwzględnienie tych rozszerzeń nazwy plików podczas ustawiania tej opcji.
- Uwzględnij kropkę podczas określania każdego rozszerzenia nazwy pliku (na przykład _.mp4_, _.f4v_).

**Domyślne zachowanie:** Protokół HTTP Progressive Download domyślnie obsługuje nośniki MP4 i F4V.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="honor-no-cache-request"></a>Honor No-Cache Żądanie

**Przeznaczenie:** Określa, czy żądania braku pamięci podręcznej klienta HTTP będą przekazywane do serwera pochodzenia.

Żądanie braku pamięci podręcznej występuje, `Cache-Control: no-cache` gdy klient `Pragma: no-cache` HTTP wysyła i/lub nagłówek w żądaniu HTTP.

Wartość|Wynik
--|--
Enabled (Włączony)|Umożliwia klientowi HTTP bez buforowania żądań, które mają być przekazywane do serwera pochodzenia, a serwer pochodzenia zwróci nagłówki odpowiedzi i treści za pośrednictwem punktu obecności z powrotem do klienta HTTP.
Disabled (Wyłączony)|Przywraca domyślne zachowanie. Domyślnym zachowaniem jest zapobieganie przekazywaniu żądań bez pamięci podręcznej do serwera pochodzenia.

W przypadku całego ruchu produkcyjnego zdecydowanie zaleca się pozostawienie tej funkcji w domyślnym stanie wyłączonym. W przeciwnym razie serwery pochodzenia nie będą chronione przed użytkownikami końcowymi, którzy mogą przypadkowo wyzwolić wiele żądań braku pamięci podręcznej podczas odświeżania stron internetowych lub z wielu popularnych odtwarzaczy multimedialnych, które są kodowane do wysyłania nagłówka bez pamięci podręcznej przy każdym żądaniu wideo. Niemniej jednak ta funkcja może być przydatna do zastosowania do niektórych katalogów przejściowych lub testowych nieprodukcyjnych, aby umożliwić ściąganie świeżej zawartości na żądanie z serwera pochodzenia.

Stan pamięci podręcznej zgłaszany dla żądania, które można przesłać dalej `TCP_Client_Refresh_Miss`do serwera pochodzenia z powodu tej funkcji, to . Raport Stany pamięci podręcznej, który jest dostępny w module raportowania Core, zawiera informacje statystyczne według stanu pamięci podręcznej. Ten raport umożliwia śledzenie liczby i procentu żądań, które są przekazywane do serwera pochodzenia z powodu tej funkcji.

**Domyślne zachowanie:** Wyłączone.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="ignore-origin-no-cache"></a>Ignoruj niejmówętną pamięć podręczną origin

**Przeznaczenie:** Określa, czy sieć CDN zignoruje następujące dyrektywy obsługiwane z serwera pochodzenia:

- `Cache-Control: private`
- `Cache-Control: no-store`
- `Cache-Control: no-cache`
- `Pragma: no-cache`

Najważniejsze informacje:

- Skonfiguruj tę funkcję, definiując listę kodów stanu rozdzielanych przestrzenią, dla których powyższe dyrektywy będą ignorowane.
- Zestaw prawidłowych kodów stanu dla tej funkcji to: 200, 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 i 505.
- Wyłącz tę funkcję, ustawiając ją na pustą wartość.

**Domyślne zachowanie:** Domyślnym zachowaniem jest honorować powyższe dyrektywy.

#### <a name="compatibility"></a>Zgodność

Ze względu na sposób śledzenia ustawień pamięci podręcznej ta funkcja nie może być skojarzona z następującymi warunkami dopasowania:
- Numer AS
- Adres IP klienta
- Parametr pliku cookie
- Parametr pliku cookie Regex
- Kraj
- Urządzenie
- Nazwa Cname krawędzi
- Domena odsyłacająca
- Dosłowny nagłówek żądania
- Żądanie Regex nagłówka
- Symbol wieloznaczny nagłówka żądania
- Request, metoda
- Schemat żądania
- Literał zapytania adresu URL
- Regex kwerendy URL
- Symbol wieloznaczny kwerendy URL
- Parametr kwerendy url

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="ignore-unsatisfiable-ranges"></a>Ignoruj niezadowolne zakresy

**Przeznaczenie:** Określa odpowiedź, która zostanie zwrócona do klientów, gdy żądanie generuje 416 Żądany zakres nie sytą kod stanu.

Domyślnie ten kod stanu jest zwracany, gdy określone żądanie zakresu bajtów nie może zostać spełnione przez pop i nie określono pola nagłówka żądania If-Range.

Wartość|Wynik
-|-
Enabled (Włączony)|Uniemożliwia punkty dostępu TK odpowiadać na nieprawidłowe żądanie zakresu bajtów z 416 Żądany zakres nie sytą kod stanu. Zamiast tego serwery dostarczy żądany zasób i zwróci 200 OK do klienta.
Disabled (Wyłączony)|Przywraca domyślne zachowanie. Domyślnym zachowaniem jest honorować kod stanu 416 Requested Range Not Satisfiable.

**Domyślne zachowanie:** Wyłączone.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="internal-max-stale"></a>Wewnętrzny Max-Stale

**Przeznaczenie:** Określa, jak długo po normalnym czasie wygaśnięcia buforowany zasób może być obsługiwany z punktu obecności, gdy punkt obecności nie może ponownie ocenić buforowanego zasobu na serwerze pochodzenia.

Zwykle po upływie maksymalnego czasu zasobu punkt obecności wyśle żądanie ponownego ukończenia do serwera pochodzenia. Serwer pochodzenia będzie następnie odpowiadać za pomocą 304 Nie zmodyfikowano, aby dać POP nową dzierżawę na buforowanym zasobie, albo z 200 OK, aby zapewnić pop zaktualizowaną wersję buforowanego zasobu.

Jeśli punkt obecności nie może ustanowić połączenia z serwerem pochodzenia podczas próby ponownego zaszerłowania, ta wewnętrzna funkcja Max-Stale określa, czy i jak długo pop może nadal obsługiwać przestarzały zasób.

Należy zauważyć, że ten przedział czasu rozpoczyna się po wygaśnięciu maksymalnego wieku zasobu, a nie po wykonaniu nieudanego ponownego zweryfikowannia. W związku z tym maksymalny okres, w którym środek trwały może być obsługiwany bez pomyślnego ponownego walidacji, jest czasem określonym przez kombinację max-age plus max-starych. Na przykład jeśli zasób został buforowany o godzinie 9:00 z maksymalnym wiekiem 30 minut i maksymalnie nieaktualnym 15 minut, nieudana próba ponownego ukończenia 9:44 spowoduje, że użytkownik końcowy otrzyma przestarzały zasób w pamięci podręcznej, podczas gdy nieudana próba ponownego ukończenia 9:46 spowoduje, że użytkownik końcowy otrzymujący limit czasu bramy 504.

Każda wartość skonfigurowana dla tej funkcji `Cache-Control: must-revalidate` jest `Cache-Control: proxy-revalidate` zastępowana przez nagłówki lub nagłówki odebrane z serwera pochodzenia. Jeśli którykolwiek z tych nagłówków zostanie odebrany z serwera pochodzenia, gdy zasób jest początkowo buforowany, punkt obecności nie będzie obsługiwał nieaktualnego zasobu w pamięci podręcznej. W takim przypadku jeśli punkt obecności nie może ponownie ocenić ze źródłem, gdy czas maksymalnego wieku zasobu wygasł, punkt obecności zwraca błąd limitu czasu bramy 504.

Najważniejsze informacje:

- Skonfiguruj tę funkcję przez:
    - Wybór kodu stanu, dla którego zostanie zastosowany max-starych.
    - Określanie wartości całkowitej, a następnie wybranie żądanej jednostki czasu (na przykład sekund, minut, godzin itp.). Ta wartość definiuje wewnętrzny max-starych, które zostaną zastosowane.

- Ustawienie jednostki czasu na "Off" spowoduje wyłączenie tej funkcji. Zasób buforowany nie będzie obsługiwany po upływie normalnego czasu wygaśnięcia.

**Domyślne zachowanie:** Dwie minuty

#### <a name="compatibility"></a>Zgodność

Ze względu na sposób śledzenia ustawień pamięci podręcznej ta funkcja nie może być skojarzona z następującymi warunkami dopasowania:
- Numer AS
- Adres IP klienta
- Parametr pliku cookie
- Parametr pliku cookie Regex
- Kraj
- Urządzenie
- Nazwa Cname krawędzi
- Domena odsyłacająca
- Dosłowny nagłówek żądania
- Żądanie Regex nagłówka
- Symbol wieloznaczny nagłówka żądania
- Request, metoda
- Schemat żądania
- Literał zapytania adresu URL
- Regex kwerendy URL
- Symbol wieloznaczny kwerendy URL
- Parametr kwerendy url

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="log-query-string"></a>Ciąg zapytania dziennika

**Przeznaczenie:** Określa, czy ciąg zapytania będą przechowywane wraz z adresem URL w dziennikach dostępu.

Wartość|Wynik
-|-
Enabled (Włączony)|Umożliwia przechowywanie ciągów zapytań podczas rejestrowania adresów URL w dzienniku dostępu. Jeśli adres URL nie zawiera ciągu zapytania, ta opcja nie będzie miała wpływu.
Disabled (Wyłączony)|Przywraca domyślne zachowanie. Domyślnym zachowaniem jest ignorowanie ciągów zapytań podczas rejestrowania adresów URL w dzienniku dostępu.

**Domyślne zachowanie:** Wyłączone.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="maximum-keep-alive-requests"></a>Maksymalna liczba żądań keep-alive

**Przeznaczenie:** Definiuje maksymalną liczbę żądań dla połączenia Keep-Alive przed jego zamknięciem.

Ustawienie maksymalnej liczby żądań na niską wartość jest odradzane i może spowodować spadek wydajności.

Najważniejsze informacje:

- Określ tę wartość jako całą całkowitą ą.
- Nie należy uwzględniać przecinków ani kropek w określonej wartości.

**Wartość domyślna:** 10 000 żądań

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="modify-client-request-header"></a>Modyfikowanie nagłówka żądania klienta

**Przeznaczenie:** Każde żądanie zawiera zestaw nagłówków żądań, które go opisują. Ta funkcja może:

- Dołącz lub zastąrzyj wartość przypisaną do nagłówka żądania. Jeśli określony nagłówek żądania nie istnieje, ta funkcja doda go do żądania.
- Usuń nagłówek żądania z żądania.

Żądania, które są przekazywane do serwera pochodzenia będą odzwierciedlać zmiany wprowadzone przez tę funkcję.

W nagłówku żądania można wykonać jedną z następujących czynności:

Opcja|Opis|Przykład
-|-|-
Append|Określona wartość zostanie dodana na końcu istniejącej wartości nagłówka żądania.|**Wartość nagłówka żądania (klient):**<br/>Wartość1<br/>**Wartość nagłówka żądania (aparat reguł):**<br/>Wartość2 <br/>**Nowa wartość nagłówka żądania:** <br/>Wartość1Value2
Zastąp|Wartość nagłówka żądania zostanie ustawiona na określoną wartość.|**Wartość nagłówka żądania (klient):**<br/>Wartość1<br/>**Wartość nagłówka żądania (aparat reguł):**<br/>Wartość2<br/>**Nowa wartość nagłówka żądania:**<br/> Wartość2 <br/>
Usuń|Usuwa określony nagłówek żądania.|**Wartość nagłówka żądania (klient):**<br/>Wartość1<br/>**Modyfikowanie konfiguracji nagłówka żądania klienta:**<br/>Usuń dany nagłówek żądania.<br/>**Wynik:**<br/>Określony nagłówek żądania nie zostanie przekazany do serwera pochodzenia.

Najważniejsze informacje:

- Upewnij się, że wartość określona w opcji Nazwa jest dokładne dopasowanie dla nagłówka żądanego żądania.
- Sprawa nie jest brana pod uwagę w celu identyfikacji nagłówka. Na przykład do jej identyfikacji można `Cache-Control` użyć dowolnej z następujących odmian nazwy nagłówka:
    - kontrola pamięci podręcznej
    - KONTROLA PAMIĘCI PODRĘCZNEJ
    - sterowanie cachE
- Podczas określania nazwy nagłówka należy używać tylko znaków alfanumerycznych, kresek lub podkreśleń.
- Usunięcie nagłówka uniemożliwi jego przekazywanie do serwera pochodzenia przez punkt usługi punktowe.
- Następujące nagłówki są zastrzeżone i nie mogą być modyfikowane przez tę funkcję:
    - Przekazywane
    - host
    - Via
    - warning
    - x-forwarded-for
    - Wszystkie nazwy nagłówków, które zaczynają się od "x-ec" są zastrzeżone.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="modify-client-response-header"></a>Modyfikowanie nagłówka odpowiedzi klienta

Każda odpowiedź zawiera zestaw nagłówków odpowiedzi, które ją opisują. Ta funkcja może:

- Dołącz lub zastąrzyj wartość przypisaną do nagłówka odpowiedzi. Jeśli określony nagłówek odpowiedzi nie istnieje, ta funkcja doda go do odpowiedzi.
- Usuń nagłówek odpowiedzi z odpowiedzi.

Domyślnie wartości nagłówka odpowiedzi są definiowane przez serwer pochodzenia i przez punkt odpowiedzi.

W nagłówku odpowiedzi można wykonać jedną z następujących czynności:

Opcja|Opis|Przykład
-|-|-
Append|Określona wartość zostanie dodana na końcu istniejącej wartości nagłówka odpowiedzi.|**Wartość nagłówka odpowiedzi (klient):**<br />Wartość1<br/>**Wartość nagłówka odpowiedzi (aparat reguł):**<br/>Wartość2<br/>**Nowa wartość nagłówka odpowiedzi:**<br/>Wartość1Value2
Zastąp|Wartość nagłówka odpowiedzi zostanie ustawiona na określoną wartość.|**Wartość nagłówka odpowiedzi (klient):**<br/>Wartość1<br/>**Wartość nagłówka odpowiedzi (aparat reguł):**<br/>Wartość2 <br/>**Nowa wartość nagłówka odpowiedzi:**<br/>Wartość2 <br/>
Usuń|Usuwa określony nagłówek odpowiedzi.|**Wartość nagłówka odpowiedzi (klient):**<br/>Wartość1<br/>**Modyfikowanie konfiguracji nagłówka odpowiedzi klienta:**<br/>Usuń dany nagłówek odpowiedzi.<br/>**Wynik:**<br/>Określony nagłówek odpowiedzi nie zostanie przekazany do żądacza.

Najważniejsze informacje:

- Upewnij się, że wartość określona w opcji Nazwa jest dokładne dopasowanie dla nagłówka żądanej odpowiedzi.
- Sprawa nie jest brana pod uwagę w celu identyfikacji nagłówka. Na przykład do jej identyfikacji można `Cache-Control` użyć dowolnej z następujących odmian nazwy nagłówka:
    - kontrola pamięci podręcznej
    - KONTROLA PAMIĘCI PODRĘCZNEJ
    - sterowanie cachE
- Usunięcie nagłówka zapobiega jego przekazywaniu do żądającego.
- Następujące nagłówki są zastrzeżone i nie mogą być modyfikowane przez tę funkcję:
    - akceptowanie kodowania
    - wiek
    - połączenie
    - kodowanie zawartości
    - długość zawartości
    - zakres zawartości
    - date
    - serwer
    - Przyczepy
    - kodowanie transferu
    - uaktualnienie
    - Się różnić
    - Via
    - warning
    - Wszystkie nazwy nagłówków, które zaczynają się od "x-ec" są zastrzeżone.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="partial-cache-sharing"></a>Częściowe udostępnianie pamięci podręcznej

**Przeznaczenie:** Określa, czy żądanie może generować częściowo buforowaną zawartość.

Ta częściowa pamięć podręczna może następnie służyć do realizacji nowych żądań dla tej zawartości, dopóki żądana zawartość nie zostanie w pełni buforowana.

Wartość|Wynik
-|-
Enabled (Włączony)|Żądania mogą generować częściowo buforowaną zawartość.
Disabled (Wyłączony)|Żądania mogą generować tylko w pełni buforowaną wersję żądanej zawartości.

**Domyślne zachowanie:** Wyłączone.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="prevalidate-cached-content"></a>Wstępnie zaawaluj zawartość buforowaną

**Przeznaczenie:** Określa, czy zawartość buforowana będzie kwalifikować się do wcześniejszego ponownego unieważnienia przed wygaśnięciem jej czasu wygaśnięcia.

Zdefiniuj czas przed wygaśnięciem czasu wygaśnięcia żądanej zawartości, podczas którego będzie ona kwalifikowana do wcześniejszego ponownego zweryfikowania.

Najważniejsze informacje:

- Wybranie opcji "Wył." jako jednostki czasu wymaga ponownego zweryfikowanie po wygaśnięciu czasu zawartości w pamięci podręcznej. Czas nie powinien być określony i jest ignorowany.

**Domyślne zachowanie:** Wyłączony. Ponowne zweryfikowanie może mieć miejsce dopiero po wygaśnięciu czasu wygaśnięcia zawartości w pamięci podręcznej.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="proxy-special-headers"></a>Nagłówki specjalne serwera proxy

**Przeznaczenie:** Definiuje zestaw [nagłówków żądań HTTP specyficznych dla verizon,](cdn-verizon-http-headers.md) które będą przekazywane z punktu obecności do serwera pochodzenia.

Najważniejsze informacje:

- Każdy nagłówek żądania specyficzne dla sieci CDN zdefiniowany w tej funkcji jest przekazywał dalej do serwera pochodzenia. Wykluczone nagłówki nie są przekazywane dalej.
- Aby zapobiec przekazywaniu nagłówka żądania specyficznego dla sieci CDN, usuń go z listy oddzielonej przestrzenią w polu listy nagłówka.

Na liście domyślnej znajdują się następujące nagłówki HTTP:
- Via
- X-Forwarded-For
- X-Forwarded-Proto
- X-Host
- X-Midgress (X-Midgress)
- X-Gateway-Lista
- Nazwa X-EC
- Host

**Domyślne zachowanie:** Wszystkie nagłówki żądań specyficzne dla sieci CDN zostaną przekazane do serwera pochodzenia.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="refresh-zero-byte-cache-files"></a>Odświeżanie plików pamięci podręcznej o zerowym bajcie

**Przeznaczenie:** Określa sposób obsługi żądania klienta HTTP dla zasobu pamięci podręcznej 0-bajtowego przez usługi podręczne.

Prawidłowe wartości:

Wartość|Wynik
--|--
Enabled (Włączony)|Powoduje, że punkt obecności ponownie przesunie zasób z serwera pochodzenia.
Disabled (Wyłączony)|Przywraca domyślne zachowanie. Domyślnym zachowaniem jest obsługa prawidłowych zasobów pamięci podręcznej na żądanie.

Ta funkcja nie jest wymagana do prawidłowego buforowania i dostarczania zawartości, ale może być przydatna jako obejście problemu. Na przykład dynamiczne generatory zawartości na serwerach pochodzenia może przypadkowo spowodować 0-bajtowe odpowiedzi są wysyłane do punktówków zarządzania. Te typy odpowiedzi są zazwyczaj buforowane przez usługi podręczne. Jeśli wiesz, że odpowiedź 0-bajtowa nigdy nie jest prawidłową odpowiedzią dla takiej zawartości, ta funkcja może uniemożliwić klientom obsługiwanie tych typów zasobów.

**Domyślne zachowanie:** Wyłączone.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="set-cacheable-status-codes"></a>Ustawianie kodów stanu w pamięci podręcznej

**Przeznaczenie:** Definiuje zestaw kodów stanu, które mogą spowodować buforowaną zawartość.

Domyślnie buforowanie jest włączone tylko dla 200 odpowiedzi OK.

Zdefiniuj rozdzielany spację zestaw żądanych kodów stanu.

Najważniejsze informacje:

- Włącz funkcję Ignoruj no-cache pochodzenia. Jeśli ta funkcja nie jest włączona, odpowiedzi inne niż 200 OK mogą nie być buforowane.
- Zestaw prawidłowych kodów stanu dla tej funkcji to: 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 i 505.
- Tej funkcji nie można wyłączyć buforowania dla odpowiedzi, które generują kod stanu 200 OK.

**Domyślne zachowanie:** Buforowanie jest włączone tylko dla odpowiedzi, które generują kod stanu 200 OK.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="set-client-ip-custom-header"></a>Ustawianie niestandardowego nagłówka IP klienta

**Przeznaczenie:** Dodaje niestandardowy nagłówek, który identyfikuje żądającego klienta według adresu IP do żądania.

Opcja Nazwa nagłówka definiuje nazwę nagłówka żądania niestandardowego, w którym jest przechowywany adres IP klienta.

Ta funkcja umożliwia serwerowi pochodzenia klienta znajdowanie adresów IP klienta za pośrednictwem nagłówka żądania niestandardowego. Jeśli żądanie jest obsługiwane z pamięci podręcznej, serwer pochodzenia nie zostanie poinformowany o adresie IP klienta. W związku z tym zaleca się, aby ta funkcja była używana z zasobami, które nie są buforowane.

Upewnij się, że określona nazwa nagłówka nie jest zgodna z żadną z następujących nazw:

- Standardowe nazwy nagłówków żądań. Listę standardowych nazw nagłówków można znaleźć w [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).
- Zastrzeżone nazwy nagłówków:
    - przekazywane
    - host
    - Się różnić
    - Via
    - warning
    - x-forwarded-for
    - Wszystkie nazwy nagłówków, które zaczynają się od "x-ec" są zastrzeżone.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="stale-content-delivery-on-error"></a>Nieaktualna dostawa treści w dniu błędu

**Przeznaczenie:** Określa, czy wygasła zawartość buforowana zostanie dostarczona, gdy wystąpi błąd podczas ponownego sprawdzaniem ważności pamięci podręcznej lub podczas pobierania żądanej zawartości z serwera pochodzenia klienta.

Wartość|Wynik
-|-
Enabled (Włączony)|Nieaktualna zawartość jest obsługiwana żądający, gdy wystąpi błąd podczas połączenia z serwerem pochodzenia.
Disabled (Wyłączony)|Błąd serwera pochodzenia jest przekazywał do żądacza.

**Domyślne zachowanie:** Wyłączone

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="stale-while-revalidate"></a>Przestarzałe podczas ponownego unieważnienia

**Przeznaczenie:** Zwiększa wydajność, zezwalając na przestarzałe treści do żądacza podczas ponownego oceny ma miejsce.

Najważniejsze informacje:

- Zachowanie tej operacji różni się w zależności od wybranej jednostki czasu.
    - **Jednostka czasu:** Określ czas i wybierz jednostkę czasu (na przykład Sekundy, Minuty, Godziny itp.), aby umożliwić dostarczanie przestarzałej zawartości. Ten typ konfiguracji umożliwia sieci CDN wydłużenie czasu dostarczania zawartości przed wymaganiem weryfikacji zgodnie z następującym wzorem: Czas**przestarzały** **czas TTL** + podczas ponownego sprawdzania poprawności
    - **Wył.:** Wybierz opcję "Wył.", aby wymagać ponownego zadłużania się przed wyświetleniem żądania nieaktualną zawartości.
        - Nie należy określać długości czasu, ponieważ nie ma zastosowania i zostanie zignorowana.

**Domyślne zachowanie:** Wyłączony. Ponowne zweryfikowanie musi nastąpić przed wyświetleniem żądanej zawartości.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth"></a>Auth tokenu

**Przeznaczenie:** Określa, czy uwierzytelnianie oparte na tokenie zostanie zastosowane do żądania.

Jeśli uwierzytelnianie oparte na tokenie jest włączone, honorowane będą tylko żądania, które zapewniają zaszyfrowany token i spełniają wymagania określone przez ten token.

Klucz szyfrowania używany do szyfrowania i odszyfrowywania wartości tokenów jest określany przez opcje klucza podstawowego i klucza kopii zapasowej na stronie Śuth tokenu. Należy pamiętać, że klucze szyfrowania są specyficzne dla platformy.

**Domyślne zachowanie:** Wyłączone.

Ta funkcja ma pierwszeństwo przed większością funkcji, z wyjątkiem funkcji przepisywania adresów URL.

Wartość | Wynik
------|---------
Enabled (Włączony) | Chroni żądaną zawartość za pomocą uwierzytelniania opartego na tokenie. Tylko żądania od klientów, którzy dostarczają prawidłowy token i spełniają jego wymagania, będą honorowane. Transakcje FTP są wykluczone z uwierzytelniania opartego na tokenach.
Disabled (Wyłączony)| Przywraca domyślne zachowanie. Domyślnym zachowaniem jest umożliwienie konfiguracji uwierzytelniania opartego na tokenie, aby określić, czy żądanie zostanie zabezpieczone.

#### <a name="compatibility"></a>Zgodność

Nie należy używać Auth token z zawsze dopasować warunek.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-denial-code"></a>Kod odmowy eru tokenu

**Przeznaczenie:** Określa typ odpowiedzi, która zostanie zwrócona do użytkownika, gdy żądanie zostanie odrzucone z powodu uwierzytelniania opartego na tokenie.

Dostępne kody odpowiedzi są wymienione w poniższej tabeli.

Kod odpowiedzi|Nazwa odpowiedzi|Opis
-------------|-------------|--------
301|Przeniesiony na stałe|Ten kod stanu przekierowuje nieautoryzowanych użytkowników do adresu URL określonego w nagłówku Lokalizacja.
302|Znaleziono|Ten kod stanu przekierowuje nieautoryzowanych użytkowników do adresu URL określonego w nagłówku Lokalizacja. Ten kod stanu jest standardową metodą wykonywania przekierowania.
307|Tymczasowe przekierowanie|Ten kod stanu przekierowuje nieautoryzowanych użytkowników do adresu URL określonego w nagłówku Lokalizacja.
401|Brak autoryzacji|Połączenie tego kodu stanu z nagłówkiem odpowiedzi WWW-Authenticate umożliwia monitowanie użytkownika o uwierzytelnienie.
403|Forbidden|Ten komunikat jest standardową wiadomością o stanie 403 Zakazany, którą nieautoryzowany użytkownik zobaczy podczas próby uzyskania dostępu do chronionej zawartości.
404|Nie znaleziono pliku|Ten kod stanu wskazuje, że klient HTTP mógł komunikować się z serwerem, ale żądana zawartość nie została znaleziona.

#### <a name="compatibility"></a>Zgodność

Nie należy używać tokenu odmowy kod ujednawczy z zawsze dopasować warunek. Zamiast tego należy użyć niestandardowej **obsługi odmowy** w **tokenu eru** strony w **portalu zarządzania.** Aby uzyskać więcej informacji, zobacz [zabezpieczanie zasobów usługi Azure CDN przy użyciu uwierzytelniania tokenów](cdn-token-auth.md).

#### <a name="url-redirection"></a>Przekierowanie adresu URL

Ta funkcja obsługuje przekierowanie adresu URL do adresu URL zdefiniowanego przez użytkownika, gdy jest skonfigurowany do zwracania kodu stanu 3xx. Ten adres URL zdefiniowany przez użytkownika można określić, wykonując następujące kroki:

1. Wybierz kod odpowiedzi 3xx dla funkcji Kod odmowy Auth Token.
2. Wybierz "Lokalizacja" z opcjonalnej nazwy nagłówka opcji.
3. Ustaw opcjonalną wartość nagłówka na żądany adres URL.

Jeśli adres URL nie jest zdefiniowany dla kodu stanu 3xx, standardowa strona odpowiedzi dla kodu stanu 3xx zostanie zwrócona użytkownikowi.

Przekierowanie adresu URL ma zastosowanie tylko do kodów odpowiedzi 3xx.

Opcja Opcjonalna wartość nagłówka obsługuje znaki alfanumeryczne, cudzysłowy i spacje.

#### <a name="authentication"></a>Uwierzytelnianie

Ta funkcja obsługuje możliwość dołączania nagłówka WWW-Authenticate podczas odpowiadania na nieautoryzowane żądanie zawartości chronionej przez uwierzytelnianie oparte na tokenie. Jeśli nagłówek WWW-Authenticate został ustawiony na "podstawowy" w konfiguracji, nieautoryzowany użytkownik zostanie poproszony o podanie poświadczeń konta.

Powyższą konfigurację można osiągnąć, wykonując następujące kroki:

1. Wybierz "401" jako kod odpowiedzi dla funkcji Kod odmowy Auth Token.
2. Wybierz opcję "WWW-Authenticate" z opcjonalnej nazwy nagłówka.
3. Ustaw opcjonalną wartość nagłówka na "podstawową".

Nagłówek WWW-Authenticate ma zastosowanie tylko do kodów odpowiedzi 401.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-ignore-url-case"></a>Przypadek adresu URL auth ignoruje token

**Przeznaczenie:** Określa, czy porównania adresów URL dokonywane przez uwierzytelnianie oparte na tokenie są rozróżniane.

Parametry, na które ma wpływ ta funkcja, to:

- ec_url_allow
- ec_ref_allow
- ec_ref_deny

Prawidłowe wartości:

Wartość|Wynik
---|----
Enabled (Włączony)|Powoduje, że pop ignorować przypadek podczas porównywania adresów URL dla parametrów uwierzytelniania opartego na tokenie.
Disabled (Wyłączony)|Przywraca domyślne zachowanie. Domyślnym zachowaniem jest porównanie adresów URL dla uwierzytelniania tokenu, aby mieć rozróżnianą wielkość liter.

**Domyślne zachowanie:** Wyłączone.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-parameter"></a>Parametr Eruth tokenu

**Przeznaczenie:** Określa, czy należy zmienić nazwę parametru ciągu zapytania uwierzytelniania opartego na tokenie.

Najważniejsze informacje:

- Opcja Wartość definiuje nazwę parametru ciągu kwerendy, za pomocą którego można określić token.
- Nie można ustawić opcji Wartość na "ec_token".
- Upewnij się, że nazwa zdefiniowana w opcji Wartość zawiera tylko prawidłowe znaki URL.

Wartość|Wynik
----|----
Enabled (Włączony)|Opcja Wartość definiuje nazwę parametru ciągu zapytania, za pomocą którego tokeny powinny być definiowane.
Disabled (Wyłączony)|Token może być określony jako parametr niezdefiniowanego ciągu zapytania w adresie URL żądania.

**Domyślne zachowanie:** Wyłączone. Token może być określony jako parametr niezdefiniowanego ciągu zapytania w adresie URL żądania.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="url-redirect"></a>Przekierowanie adresu URL

**Przeznaczenie:** Przekierowuje żądania za pośrednictwem nagłówka Lokalizacja.

Konfiguracja tej funkcji wymaga ustawienia następujących opcji:

Opcja|Opis
-|-
Code|Wybierz kod odpowiedzi, który zostanie zwrócony do żądającego.
Wzór & źródła| Te ustawienia definiują wzorzec identyfikatora URI żądania, który identyfikuje typ żądań, które mogą być przekierowywane. Przekierowane są tylko żądania, których adres URL spełnia oba następujące kryteria: <br/> <br/> **Źródło (lub punkt dostępu do zawartości):** Wybierz ścieżkę względną identyfikującą serwer pochodzenia. Ta ścieżka jest _/XXXX/_ sekcji i nazwę punktu końcowego. <br/><br/> **Źródło (wzór):** Wzorzec identyfikujący żądania według ścieżki względnej musi być zdefiniowany. Ten wzorzec wyrażenia regularnego musi definiować ścieżkę, która rozpoczyna się bezpośrednio po wcześniej wybranym punkcie dostępu do zawartości (patrz wyżej). <br/> - Upewnij się, że kryteria identyfikatora URI żądania (czyli Source & Pattern) wcześniej zdefiniowane nie powoduje konfliktu z żadnych warunków dopasowania zdefiniowanych dla tej funkcji. <br/> - Określ wzór; Jeśli używasz pustej wartości jako wzorca, wszystkie ciągi są dopasowywały.
Element docelowy| Zdefiniuj adres URL, do którego zostaną przekierowane powyższe żądania. <br/><br/> Dynamicznie konstruuj ten adres URL za pomocą: <br/> - Wzorzec wyrażenia regularnego <br/>- [Zmienne HTTP](cdn-http-variables.md) <br/><br/> Zastąp wartości przechwycone we wzorcu źródłowym na wzorzec docelowy przy użyciu $_n,_ gdzie _n_ identyfikuje wartość według kolejności, w jakiej została przechwycona. Na przykład $1 reprezentuje pierwszą wartość przechwyconą we wzorcu źródłowym, podczas gdy $2 reprezentuje drugą wartość. <br/>

Zdecydowanie zaleca się użycie bezwzględnego adresu URL. Użycie względnego adresu URL może przekierować adresy URL sieci CDN do nieprawidłowej ścieżki.

**Przykładowy scenariusz**

W tym przykładzie pokazano, jak przekierować adres URL CNAME krawędzi,\/który jest rozpoznawany do tego podstawowego adresu URL usługi CDN: http: /marketing.azureedge.net/brochures

Kwalifikujące się żądania zostaną przekierowane do tego adresu\/URL CNAME krawędzi podstawowej: http: /cdn.mydomain.com/resources

Przekierowanie adresu URL można osiągnąć za ![pomocą następującej konfiguracji: przekierowanie adresu URL](./media/cdn-rules-engine-reference/cdn-rules-engine-redirect.png)

**Kluczowe punkty:**

- Funkcja przekierowania adresu URL definiuje adresy URL żądań, które zostaną przekierowane. W rezultacie dodatkowe warunki dopasowania nie są wymagane. Chociaż warunek dopasowania został zdefiniowany jako "Zawsze", zostaną przekierowane tylko żądania wskazujące folder "broszury" w "marketingowym" źródle pochodzenia klienta.
- Wszystkie pasujące żądania zostaną przekierowane do adresu URL CNAME krawędzi zdefiniowanego w opcji Miejsce docelowe.
    - Przykładowy scenariusz #1:
        - Przykładowy wniosek (adres URL\/sieci CDN): http: /marketing.azureedge.net/brochures/widgets.pdf
        - Adres URL żądania (po\/przekierowaniu): http: /cdn.mydomain.com/resources/widgets.pdf  
    - Przykładowy scenariusz #2:
        - Przykładowe żądanie (adres URL żądania\/CNAME krawędzi): http: /marketing.mydomain.com/brochures/widgets.pdf
        - Adres URL żądania (po\/przekierowaniu): http: /cdn.mydomain.com/resources/widgets.pdf Przykładowy scenariusz
    - Przykładowy scenariusz #3:
        - Przykładowe żądanie (adres URL żądania\/CNAME krawędzi): http: /brochures.mydomain.com/campaignA/final/productC.ppt
        - Adres URL żądania (po\/przekierowaniu): http: /cdn.mydomain.com/resources/campaignA/final/productC.ppt 
- Zmienna Schemat żądań (%{scheme}) jest dźwignięcona w opcji Miejsce docelowe, co zapewnia, że schemat żądania pozostaje niezmieniony po przekierowaniu.
- Segmenty adresów URL, które zostały przechwycone z żądania, są dołączane do nowego adresu URL za pośrednictwem "$1".

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="url-rewrite"></a>Ponowne zapisywanie adresów URL

**Przeznaczenie:** Przepisuje adres URL żądania.

Najważniejsze informacje:

- Konfiguracja tej funkcji wymaga ustawienia następujących opcji:

Opcja|Opis
-|-
 Wzór & źródła | Te ustawienia definiują wzorzec identyfikatora URI żądania, który identyfikuje typ żądań, które mogą być przepisane. Zostaną przepisane tylko żądania, których adres URL spełnia oba następujące kryteria: <br/><br/>  - **Źródło (lub punkt dostępu do zawartości):** Wybierz ścieżkę względną identyfikującą serwer pochodzenia. Ta ścieżka jest _/XXXX/_ sekcji i nazwę punktu końcowego. <br/><br/> - **Źródło (wzór):** Wzorzec identyfikujący żądania według ścieżki względnej musi być zdefiniowany. Ten wzorzec wyrażenia regularnego musi definiować ścieżkę, która rozpoczyna się bezpośrednio po wcześniej wybranym punkcie dostępu do zawartości (patrz wyżej). <br/> Sprawdź, czy kryteria identyfikatora URI żądania (czyli Źródło & Wzorzec) wcześniej zdefiniowane nie powoduje konfliktu z żadnym z warunków dopasowania zdefiniowanych dla tej funkcji. Określ szyk; Jeśli używasz pustej wartości jako wzorca, wszystkie ciągi są dopasowywały.
 Element docelowy  |Zdefiniuj względny adres URL, do którego powyższe żądania zostaną przepisane przez: <br/>    1. Wybór punktu dostępu do zawartości, który identyfikuje serwer pochodzenia. <br/>    2. Definiowanie ścieżki względnej za pomocą: <br/>        - Wzorzec wyrażenia regularnego <br/>        - [Zmienne HTTP](cdn-http-variables.md) <br/> <br/> Zastąp wartości przechwycone we wzorcu źródłowym na wzorzec docelowy przy użyciu $_n,_ gdzie _n_ identyfikuje wartość według kolejności, w jakiej została przechwycona. Na przykład $1 reprezentuje pierwszą wartość przechwyconą we wzorcu źródłowym, podczas gdy $2 reprezentuje drugą wartość.

 Ta funkcja umożliwia dostawców usług owych ponowne przeredagowanie adresu URL bez wykonywania tradycyjnego przekierowania. Oznacza to, że żądający otrzymuje ten sam kod odpowiedzi, tak jakby zażądano przepisanych adresów URL.

**Przykładowy scenariusz 1**

W tym przykładzie pokazano, jak przekierować adres URL CNAME krawędzi,\/który jest rozpoznawany do tego podstawowego adresu URL usługi CDN: http: /marketing.azureedge.net/brochures/

Kwalifikujące się żądania zostaną przekierowane do tego adresu\/URL CNAME krawędzi podstawowej: http: /MyOrigin.azureedge.net/resources/

Przekierowanie adresu URL można osiągnąć za ![pomocą następującej konfiguracji: przekierowanie adresu URL](./media/cdn-rules-engine-reference/cdn-rules-engine-rewrite.png)

**Przykładowy scenariusz 2**

W tym przykładzie pokazano, jak przekierować adres URL CNAME krawędzi z wielkich liter do małych przy użyciu wyrażeń regularnych.

Przekierowanie adresu URL można osiągnąć za ![pomocą następującej konfiguracji: przekierowanie adresu URL](./media/cdn-rules-engine-reference/cdn-rules-engine-to-lowercase.png)

**Kluczowe punkty:**

- Funkcja przepisywania adresów URL definiuje adresy URL żądań, które zostaną przepisane. W rezultacie dodatkowe warunki dopasowania nie są wymagane. Chociaż warunek dopasowania został zdefiniowany jako "Zawsze", zostaną przepisane tylko żądania wskazujące folder "broszury" w "marketingowym" pochodzeniu klienta.

- Segmenty adresów URL, które zostały przechwycone z żądania, są dołączane do nowego adresu URL za pośrednictwem "$1".

#### <a name="compatibility"></a>Zgodność

Ta funkcja zawiera kryteria dopasowywania, które muszą być spełnione, zanim będzie można zastosować do żądania. Aby zapobiec konfigurowaniu sprzecznych kryteriów dopasowania, ta funkcja jest niezgodna z następującymi warunkami dopasowania:

- Numer AS
- Pochodzenie CDN
- Adres IP klienta
- Pochodzenie klienta
- Schemat żądania
- Katalog ścieżki adresu URL
- Rozszerzenie ścieżki adresu URL
- Nazwa pliku ścieżki adresu URL
- Literał ścieżki adresu URL
- Ścieżka adresu URL Regex
- Symbol wieloznaczny ścieżki adresu URL
- Literał zapytania adresu URL
- Parametr kwerendy url
- Regex kwerendy URL
- Symbol wieloznaczny kwerendy URL

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="user-variable"></a>Zmienna użytkownika

**Przeznaczenie:** Tylko do użytku wewnętrznego.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

## <a name="next-steps"></a>Następne kroki

- [Odwołanie do aparatu reguł](cdn-verizon-premium-rules-engine-reference.md)
- [Reguły wyrażeń warunkowych aparatu](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Zasady warunków dopasowania silnika](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Zastępowanie zachowania HTTP przy użyciu aparatu reguł](cdn-verizon-premium-rules-engine.md)
- [Omówienie usługi Azure CDN](cdn-overview.md)