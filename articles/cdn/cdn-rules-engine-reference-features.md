---
title: Zasady usługi Azure CDN aparat funkcji | Dokumentacja firmy Microsoft
description: Dokumentacja referencyjna dla usługi Azure CDN zasady funkcje aparatu.
services: cdn
documentationcenter: ''
author: Lichard
manager: akucer
editor: ''
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: rli
ms.openlocfilehash: fd670e3b01812b7fa8fc708a02d02210b598ac6a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cdn-rules-engine-features"></a>Zasady usługi Azure CDN aparat funkcji
W tym artykule przedstawiono szczegółowe opisy funkcji dostępnych dla Azure Content Delivery Network (CDN) [aparatu reguł](cdn-rules-engine.md).

Trzeci część reguły jest funkcja. Funkcja definiuje typ akcji, która jest stosowana do żądania typu identyfikowane przez zestaw warunków dopasowania.

## <a name="access-features"></a>Funkcje dostępu

Te funkcje są przeznaczone do kontrolowania dostępu do zawartości.

Name (Nazwa) | Przeznaczenie
-----|--------
[Odmowa dostępu (403)](#deny-access-403) | Określa, czy wszystkie żądania są odrzucane odpowiedź 403 Zabroniony.
[Token uwierzytelniania](#token-auth) | Określa, czy uwierzytelnianie na podstawie tokenu jest stosowany na żądanie.
[Kod odmowa tokenu uwierzytelniania](#token-auth-denial-code) | Określa typ odpowiedzi, który jest zwracany do użytkownika, gdy żądanie zostanie odrzucone z powodu uwierzytelniania opartego na tokenie.
[Token uwierzytelniania Ignoruj wielkość liter adresu URL](#token-auth-ignore-url-case) | Określa, czy wprowadzone przez uwierzytelniania opartego na tokenie porównania adres URL jest rozróżniana wielkość liter.
[Parametr tokenu uwierzytelniania](#token-auth-parameter) | Określa, czy parametr ciągu zapytania uwierzytelniania opartego na tokenie powinny zostać zmienione.


## <a name="caching-features"></a>Buforowanie funkcji

Te funkcje są przeznaczone do dostosowania, kiedy i jak zawartość jest buforowana.

Name (Nazwa) | Przeznaczenie
-----|--------
[Parametry przepustowości](#bandwidth-parameters) | Określa, czy parametry ograniczania przepustowości (na przykład ec_rate i ec_prebuf) są aktywne.
[Ograniczanie przepustowości](#bandwidth-throttling) | Ogranicza przepustowość dla odpowiedzi dostarczonych przez punkt z — obecności (POP).
[Pomiń pamięć podręczną](#bypass-cache) | Określa, czy żądanie należy pominąć buforowanie.
[Traktowanie nagłówek Cache-Control](#cache-control-header-treatment) | Kontroluje Generowanie `Cache-Control` nagłówki przez POP, gdy funkcja zewnętrznych Max-Age jest aktywny.
[Cache-Key Query String](#cache-key-query-string) | Określa, czy klucz pamięci podręcznej zawiera, czy wyklucza parametrów ciągu zapytania skojarzonego z żądaniem.
[Napisz ponownie klucz pamięci podręcznej](#cache-key-rewrite) | Ponownie zapisuje klucz pamięci podręcznej skojarzonej z żądaniem.
[Zakończenie wypełnienie pamięci podręcznej](#complete-cache-fill) | Określa, co się stanie, gdy żądanie powoduje Chybienie pamięci podręcznej częściowe na punktu obecności.
[Kompresuj typów plików](#compress-file-types) | Definiuje formatach plików, które są kompresowane na serwerze.
[Max-Age wewnętrzny domyślne](#default-internal-max-age) | Określa domyślny interwał maksymalny wiek dla punktu obecności ponowna Walidacja buforu serwera pochodzenia.
[Wygasa traktowania nagłówka](#expires-header-treatment) | Kontroluje Generowanie `Expires` nagłówki przez POP, gdy funkcja zewnętrznych Max-Age jest aktywny.
[Max-Age zewnętrznych](#external-max-age) | Określa maksymalny wiek interwał przeglądarce ponowna Walidacja buforu POP.
[Wymuszanie wewnętrznych Max-Age.](#force-internal-max-age) | Określa interwał maksymalny wiek dla punktu obecności ponowna Walidacja buforu serwera pochodzenia.
[Obsługa H.264 (pobierania progresywnego HTTP)](#h264-support-http-progressive-download) | Określa typy H.264 formatów plików, które mogą służyć do strumieniowego przesyłania zawartości.
[Honoruj No-Cache żądania](#honor-no-cache-request) | Określa, czy klient HTTP żądań pamięci podręcznej nie są przekazywane do serwera pochodzenia.
[Ignoruj pochodzenia No-Cache](#ignore-origin-no-cache) | Określa, czy usługa CDN ignoruje niektórych dyrektyw z serwera pochodzenia.
[Ignoruj Unsatisfiable zakresów](#ignore-unsatisfiable-ranges) | Określa odpowiedź, które są zwracane do klientów, gdy żądanie generuje kod stanu żądany zakres nie niewłaściwego 416.
[Wewnętrzny odświeżona maksymalna](#internal-max-stale) | Formanty, jak długo późniejsza niż godzina wygaśnięcia normalne zasobów pamięci podręcznej mogą być udostępniane przez punktu obecności podczas POP nie będzie mógł ponownie sprawdź poprawność buforowanych zasobów w serwerze źródłowym.
[Udostępnianie częściowe pamięci podręcznej](#partial-cache-sharing) | Określa, czy żądanie może wygenerować częściowo buforowaną zawartość.
[Prevalidate zawartości w pamięci podręcznej](#prevalidate-cached-content) | Określa, czy zawartość z pamięci podręcznej kwalifikuje się do wcześniejszego ponowna Walidacja przed wygaśnięciem wartość TTL.
[Odśwież Zero bajtów pamięci podręcznej plików](#refresh-zero-byte-cache-files) | Określa sposób obsługi żądania klienta HTTP dla trwałego 0 bajtów pamięci podręcznej przez punkty obecności.
[Kody stanu Buforowalnej zestawu](#set-cacheable-status-codes) | Definiuje zestaw kodów stanu, które mogą skutkować zawartości w pamięci podręcznej.
[Stałe dostarczanie zawartości w przypadku błędu](#stale-content-delivery-on-error) | Określa, czy ważność zawartości w pamięci podręcznej jest dostarczany w przypadku wystąpienia błędu podczas ponownego sprawdzania poprawności pamięci podręcznej lub podczas pobierania żądanej zawartości z serwera pochodzenia klienta.
[Nieaktualne podczas Revalidate](#stale-while-revalidate) | Zwiększa wydajność, umożliwiając POP do obsługi starych klienta do zleceniodawcy podczas ponownego sprawdzania poprawności ma miejsce.

## <a name="comment-feature"></a>Funkcja komentarza

Ta funkcja pozwala uzyskać dodatkowe informacje w regule.

Name (Nazwa) | Przeznaczenie
-----|--------
[Komentarz](#comment) | Umożliwia Uwaga do dodania w regule.
 
## <a name="header-features"></a>Funkcje nagłówka

Te funkcje zostały zaprojektowane tak, aby dodać, zmodyfikować lub usunąć nagłówków z żądania lub odpowiedzi.

Name (Nazwa) | Przeznaczenie
-----|--------
[Nagłówek odpowiedzi wieku](#age-response-header) | Określa, czy nagłówek odpowiedzi wieku są uwzględniane w odpowiedzi wysyłane do zleceniodawcy.
[Debugowanie nagłówki odpowiedzi pamięci podręcznej](#debug-cache-response-headers) | Określa, czy odpowiedź może zawierać nagłówek odpowiedzi we-X-Debug, który zawiera informacje dotyczące zasady pamięci podręcznej dla żądanego zasobu.
[Modyfikowanie nagłówek żądania klienta](#modify-client-request-header) | Zastępowanie, dołącza lub usuwa nagłówek z żądania.
[Modyfikowanie nagłówka odpowiedzi klienta](#modify-client-response-header) | Zastępowanie, dołącza lub usuwa nagłówek z odpowiedzi.
[Wartość niestandardowego nagłówka adresu IP klienta](#set-client-ip-custom-header) | Zezwala na adres IP klienta mają zostać dodane do żądania jako nagłówek żądania niestandardowych.


## <a name="logging-features"></a>Funkcje rejestrowania

Te funkcje są przeznaczone do dostosowania dane przechowywane w plikach dziennika raw.

Name (Nazwa) | Przeznaczenie
-----|--------
[Pole niestandardowe dziennika 1](#custom-log-field-1) | Określa format i zawartość, która jest przypisany do pola dziennik niestandardowy w pliku dziennika raw.
[Ciąg zapytania dziennika](#log-query-string) | Określa, czy ciąg zapytania są przechowywane wraz z adresu URL w dziennikach dostępu.


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

Te funkcje zostały zaprojektowane do kontrolowania sposobu CDN komunikuje się z serwerem pochodzenia.

Name (Nazwa) | Przeznaczenie
-----|--------
[Maksymalna liczba żądań Keep-Alive](#maximum-keep-alive-requests) | Określa maksymalną liczbę żądań Keep-Alive połączenia przed jego zamknięciem.
[Serwer proxy specjalnych nagłówków](#proxy-special-headers) | Definiuje zestaw specyficzne dla usługi CDN nagłówków żądania, które są przekazywane z punktu obecności na serwerze źródłowym.


## <a name="specialty-features"></a>Funkcje specjalne

Funkcje te zapewniają zaawansowane funkcje dla użytkowników zaawansowanych.

Name (Nazwa) | Przeznaczenie
-----|--------
[Metody HTTP buforowalnej](#cacheable-http-methods) | Określa zestaw dodatkowych metod HTTP, które mogą być buforowane w sieci.
[Rozmiar treści żądania buforowalnej](#cacheable-request-body-size) | Określa próg do określenia, czy odpowiedź POST mogą być buforowane.
[Zmienna użytkownika](#user-variable) | Tylko do użytku wewnętrznego.

 
## <a name="url-features"></a>Adres URL funkcji

Te funkcje umożliwiają żądanie jest przekierowywane lub ulegną do innego adresu URL.

Name (Nazwa) | Przeznaczenie
-----|--------
[Wykonaj przekierowania](#follow-redirects) | Określa, czy nazwa hosta zdefiniowane w nagłówku lokalizacji zwróconych przez serwer pochodzenia klienta można przekierować żądania.
[Adres URL przekierowania](#url-redirect) | Przekierowuje żądania za pośrednictwem nagłówek lokalizacji.
[Ponowne zapisywanie adresów URL](#url-rewrite)  | Ponownie zapisuje adresu URL żądania.



## <a name="azure-cdn-rules-engine-features-reference"></a>Odwołanie do funkcji aparatu reguły Azure CDN

---
### <a name="age-response-header"></a>Nagłówek odpowiedzi wieku
**Cel**: Określa, czy nagłówek odpowiedzi wieku są uwzględniane w odpowiedzi wysyłane do zleceniodawcy.
Wartość|Wynik
--|--
Enabled (Włączony) | Nagłówek odpowiedzi wieku znajduje się w odpowiedzi wysyłane do zleceniodawcy.
Disabled (Wyłączony) | Nagłówek odpowiedzi wieku został wykluczony z odpowiedzi wysyłane do zleceniodawcy.

**Domyślne zachowanie**: wyłączone.

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="bandwidth-parameters"></a>Parametry przepustowości
**Cel:** Określa, czy parametry ograniczania przepustowości (na przykład ec_rate i ec_prebuf) są aktywne.

Parametry ograniczania przepustowości określają, czy szybkość transferu danych dla żądania klienta są ograniczone do szybkości niestandardowych.

Wartość|Wynik
--|--
Enabled (Włączony)|Umożliwia POP uwzględnić przepustowości żądania.
Disabled (Wyłączony)|Powoduje, że POP zignorować parametry ograniczenia przepustowości. Żądana zawartość jest zwykle obsługiwany (czyli bez ograniczania przepustowości).

**Domyślne zachowanie:** włączone.
 
[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="bandwidth-throttling"></a>Ograniczanie przepustowości
**Cel:** ogranicza przepustowość dla odpowiedzi dostarczonych przez lokalizacji POP.

Obie z poniższych opcji, należy zdefiniować Aby poprawnie skonfigurować ograniczanie przepustowości.

Opcja|Opis
--|--
KB na sekundę|Ustaw tę opcję, aby maksymalnej przepustowości (Kb na sekundę), które mogą być używane w celu dostarczenia odpowiedzi.
Prebuf sekund|Ustaw tę opcję na liczbę sekund POP poczekać, aż przepustowości jest ograniczany. Przepustowości nieograniczony okres ten ma na celu uniemożliwić Windows media player występują problemy z przestoje w odtwarzaniu lub buforowania z powodu ograniczania przepustowości.

**Domyślne zachowanie:** wyłączone.

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="bypass-cache"></a>Pomiń pamięć podręczną
**Cel:** Określa, czy żądanie należy pominąć buforowanie.

Wartość|Wynik
--|--
Enabled (Włączony)|Powoduje, że wszystkie żądania przejść do serwera pochodzenia nawet, jeśli zawartość była wcześniej buforowane na POP.
Disabled (Wyłączony)|Powoduje, że POP do pamięci podręcznej zasobów zgodnie z zasadami pamięci podręcznej określonych w jego nagłówków odpowiedzi.

**Domyślne zachowanie:**

- **Duże HTTP:** wyłączone

<!---
- **ADN:** Enabled

--->

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cacheable-http-methods"></a>Metody HTTP buforowalnej
**Cel:** określa zestaw dodatkowych metod HTTP, które mogą być buforowane w sieci.

Informacje o kluczu:

- Ta funkcja przyjęto założenie, że zawsze mają być buforowane odpowiedzi GET. W związku z tym metodę GET HTTP nie należy włączyć podczas ustawiania tej funkcji.
- Ta funkcja obsługuje metodę POST HTTP. Włącz buforowanie odpowiedzi POST przez ustawienie dla tej funkcji `POST`.
- Domyślnie tylko żądania, których treść jest mniejszy niż 14 Kb są buforowane. Użyj funkcji Buforowalnej rozmiar treści żądania, aby ustawić żądania maksymalny rozmiar treści.

**Domyślne zachowanie:** tylko odpowiedzi GET są buforowane.

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cacheable-request-body-size"></a>Rozmiar treści żądania buforowalnej
**Cel:** określa próg do określenia, czy odpowiedź POST mogą być buforowane.

Wartość progu jest określana przez określania rozmiaru treści żądania maksymalna. Żądań zawierających większą treści żądania nie są buforowane.

Informacje o kluczu:

- Ta funkcja ma zastosowanie tylko w przypadku, gdy odpowiedzi POST kwalifikują się do buforowania. Funkcja Buforowalnej HTTP metod Aby włączyć buforowanie żądania POST.
- Treść żądania jest brana pod uwagę dla:
    - wartości x--www-form-urlencoded
    - Zapewnienie Unikatowy klucz pamięci podręcznej
- Definiowanie dużego żądania maksymalny rozmiar treści może mieć wpływ na wydajność dostarczania danych.
    - **Zalecana wartość:** 14 Kb
    - **Wartość minimalna:** 1 Kb

**Domyślne zachowanie:** 14 Kb

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cache-control-header-treatment"></a>Traktowanie nagłówek Cache-Control
**Cel:** kontroluje Generowanie `Cache-Control` nagłówki przez POP, gdy zewnętrzne funkcji Max-Age jest aktywny.

Najprostszym sposobem uzyskania tego typu konfiguracji jest można umieścić w tej samej instrukcji zewnętrznych maksymalny wiek i funkcji do przetwarzania nagłówek Cache-Control.

Wartość|Wynik
--|--
Zastąp|Zapewnia, że są wykonywane następujące akcje:<br/> -Zastępuje `Cache-Control` nagłówka wygenerowane przez serwer pochodzenia. <br/>-Dodaje `Cache-Control` nagłówka utworzonej przez funkcję zewnętrznych Max-Age do odpowiedzi.
Przekazywanie|Zapewnia, że `Cache-Control` nagłówka utworzonej przez funkcję zewnętrznych Max-Age nigdy nie zostanie dodany do odpowiedzi. <br/> Jeśli serwer pochodzenia `Cache-Control` nagłówka, przechodzą przez użytkownika końcowego. <br/> Jeśli na serwerze źródłowym nie tworzy `Cache-Control` nagłówek, a następnie ta opcja może spowodować nagłówek odpowiedzi nie będzie zawierał `Cache-Control` nagłówka.
Jeśli brakuje dodać|Jeśli `Cache-Control` nagłówka nie odebrano z serwera pochodzenia, a następnie ta opcja dodaje `Cache-Control` nagłówka utworzonej przez funkcję zewnętrznych Max-Age. Ta opcja jest przydatna do zapewnienia, że wszystkie zasoby są przypisane `Cache-Control` nagłówka.
Remove| Tej opcji zapewnia, że `Cache-Control` nagłówka nie jest dołączony do odpowiedzi nagłówek. Jeśli `Cache-Control` nagłówka została już przypisana, a następnie jest usuwany z nagłówka odpowiedzi.

**Domyślne zachowanie:** zastąpić.

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cache-key-query-string"></a>Ciąg zapytania klucz pamięci podręcznej
**Cel:** Określa, czy klucz pamięci podręcznej zawiera czy wyklucza parametrów ciągu zapytania skojarzonego z żądaniem.

Informacje o kluczu:

- Określ co najmniej jedną nazwę parametru ciągu zapytania i Oddziel poszczególne nazwy parametru z jednego miejsca.
- Ta funkcja określa, czy parametry ciągu zapytania są dołączone lub wykluczone z klucza pamięci podręcznej. Dodatkowe informacje są udostępniane dla każdej opcji w poniższej tabeli.

Typ|Opis
--|--
 Uwzględnij|  Wskazuje, że każdy określony parametr powinny być uwzględnione w klucz pamięci podręcznej. Unikatowy klucz pamięci podręcznej jest generowany dla każdego żądania, który zawiera unikatową wartość dla parametru ciągu zapytania, zdefiniowane w tej funkcji. 
 Uwzględnij wszystkie  |Wskazuje, czy unikatowy klucz pamięci podręcznej jest tworzony dla każdego żądania do zasobu, który zawiera ciąg zapytania unikatowy. Ten typ konfiguracji nie jest zwykle zalecane, ponieważ może to prowadzić do niewielki procent trafień w pamięci podręcznej. Mała liczba trafień w pamięci podręcznej zwiększa obciążenie na serwerze źródłowym, ponieważ musi obsłużyć więcej żądań. Ta konfiguracja jest duplikatem zachowanie buforowania, nazywany "Unikatowy pamięci podręcznej" na stronie buforowanie ciągu zapytania. 
 Wyklucz | Wskazuje, czy tylko określonych parametrów jest wykluczony z klucza pamięci podręcznej. Wszystkie pozostałe parametry ciągu zapytania są zawarte w kluczu pamięci podręcznej. 
 Wyklucz wszystkie  |Wskazuje, że wszystkie parametry ciągu zapytania są wykluczone z klucza pamięci podręcznej. Ta konfiguracja jest duplikatem domyślne "pamięci podręcznej standard" buforowanie na stronie buforowanie ciągu zapytania.  

Aparat reguł umożliwia dostosowanie sposobu, w którym zaimplementowana jest buforowanie ciągu zapytania. Na przykład można określić, czy buforowanie ciągu zapytania jest wykonywana tylko w określonych lokalizacjach lub typów plików.

Aby zduplikować zachowanie na stronie buforowanie ciągu zapytania buforowania ciągu kwerendy "no-cache", należy utworzyć regułę, która zawiera warunek dopasowanie symbolu wieloznacznego zapytanie adresu URL i funkcja pomijania pamięci podręcznej. Warunek dopasowanie symbolu wieloznacznego zapytanie adresu URL na znak gwiazdki (*).

>[!IMPORTANT] 
> Jeśli token autoryzacji jest włączona dla dowolną ścieżkę na tym koncie, tryb standardowy pamięci podręcznej jest tylko tryb, który może służyć do buforowania ciągu kwerendy. Aby uzyskać więcej informacji, zobacz [Control Azure CDN caching behavior with query strings](cdn-query-string-premium.md) (Sterowanie zachowaniem buforowania usługi CDN za pomocą ciągów zapytań).

#### <a name="sample-scenarios"></a>Przykładowe scenariusze

Następujące przykładowe użycie tej funkcji zawiera przykładowe żądanie i klucza pamięci podręcznej domyślne:

- **Przykładowe żądanie:** http://wpc.0001.&lt; Domeny&gt;/800001/Origin/folder/asset.htm?sessionid=1234 i język = EN & userid = 01
- **Domyślny klucz pamięci podręcznej:** /800001/Origin/folder/asset.htm

##### <a name="include"></a>Uwzględnij

Przykładowa konfiguracja:

- **Typ:** obejmują
- **Parametry:** języka

Ten typ konfiguracji będzie generowania następujące kwerendy ciąg parametru pamięci podręcznej — klucza:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="include-all"></a>Uwzględnij wszystkie

Przykładowa konfiguracja:

- **Typ:** obejmują wszystkie

Ten typ konfiguracji będzie generowania następujące kwerendy ciąg parametru pamięci podręcznej — klucza:

    /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01

##### <a name="exclude"></a>Wyklucz

Przykładowa konfiguracja:

- **Typ:** wykluczenia
- **Parametry:** sessionid userid

Ten typ konfiguracji będzie generowania następujące kwerendy ciąg parametru pamięci podręcznej — klucza:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="exclude-all"></a>Wyklucz wszystkie

Przykładowa konfiguracja:

- **Typ:** wykluczyć wszystkie

Ten typ konfiguracji będzie generowania następujące kwerendy ciąg parametru pamięci podręcznej — klucza:

    /800001/Origin/folder/asset.htm

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cache-key-rewrite"></a>Napisz ponownie klucz pamięci podręcznej
**Cel:** ponownie zapisuje klucz pamięci podręcznej skojarzonej z żądaniem.

Klucz pamięci podręcznej jest ścieżką względną identyfikujący zasób na potrzeby buforowania. Innymi słowy serwery Sprawdź, czy wersja buforowana trwałego zgodnie z jego ścieżki zgodnie z definicją w jej klucz pamięci podręcznej.

Tej funkcji można skonfigurować, definiując obu z następujących opcji:

Opcja|Opis
--|--
Oryginalna ścieżka| Zdefiniuj ścieżkę względną do typów żądań, których klucz pamięci podręcznej jest napisany od nowa. Ścieżka względna mogą być definiowane przez wybranie ścieżka do podstawowego źródła, a następnie wzorzec wyrażenia regularnego.
Nowa ścieżka|Zdefiniuj ścieżkę względną nowy klucz pamięci podręcznej. Ścieżka względna mogą być definiowane przez wybranie ścieżka do podstawowego źródła, a następnie wzorzec wyrażenia regularnego. Ta ścieżka względna można dynamicznie utworzyć przy użyciu protokołu HTTP, zmiennych
**Domyślne zachowanie:** klucz pamięci podręcznej żądania jest określana przez identyfikator URI żądania.

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="comment"></a>Komentarz
**Cel:** umożliwia Uwaga do dodania w regule.

Jedno użycie tej funkcji jest zawierają dodatkowe informacje o ogólnego przeznaczenia, reguły lub dlaczego określonego zgodne z warunkiem lub funkcja została dodana do reguły.

Informacje o kluczu:

- Można określić maksymalnie 150 znaków.
- Używaj tylko znaków alfanumerycznych.
- Ta funkcja nie ma wpływu na zachowanie reguły. Go jedynie ma zapewnić obszaru, w którym można podać informacje do przyszłego wykorzystania lub które mogą ułatwić podczas rozwiązywania problemów reguły.

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="complete-cache-fill"></a>Zakończenie wypełnienie pamięci podręcznej
**Cel:** Określa, co się dzieje, gdy żądanie powoduje Chybienie częściowe pamięci podręcznej na punktu obecności.

Chybienia pamięci podręcznej częściowe opisuje stan pamięci podręcznej dla zasobu, który nie został całkowicie pobrana do punktu obecności. W przypadku zasobów są tylko częściowo buforowane na POP, następnie następnego żądania dla tego zasobu zostanie przekazany ponownie do serwera pochodzenia.
<!---
This feature is not available for the ADN platform. The typical traffic on this platform consists of relatively small assets. The size of the assets served through these platforms helps mitigate the effects of partial cache misses, since the next request will typically result in the asset being cached on that POP.

--->
Chybienia pamięci podręcznej częściowe zazwyczaj występuje po użytkownik porzuca pobieranie lub trwałych żądanych wyłącznie przy użyciu żądania range HTTP. Ta funkcja jest najbardziej przydatny w przypadku dużych zasobów, które zwykle nie zostaną pobrane od początku do końca (na przykład pliki wideo). W związku z tym ta funkcja jest włączona domyślnie na platformie dużych HTTP. Jest ona wyłączona w innych platform.

Zachowaj konfigurację domyślną dla dużych HTTP platformy, ponieważ zmniejsza obciążenie serwera pochodzenia klienta i przyspiesza, w którym klienci pobierania zawartości.

Wartość|Wynik
--|--
Enabled (Włączony)|Przywraca domyślne zachowanie. Domyślnym zachowaniem jest wymuszenie POP, aby zainicjować pobieranie w tle zasobu z serwera pochodzenia. Po upływie którego będzie elementu zawartości w lokalnej pamięci podręcznej punktu obecności.
Disabled (Wyłączony)|POP uniemożliwia wykonywanie pobieranie w tle dla elementu zawartości. Wynik jest, że przy następnym żądaniu dla tego zasobu z tego regionu powoduje POP żądania do serwera pochodzenia klienta.

**Domyślne zachowanie:** włączone.

#### <a name="compatibility"></a>Zgodność
Ze względu na sposób, w których pamięci podręcznej ustawienia są śledzone ta funkcja nie może być skojarzony z następujących warunków dopasowania: 
- JAKO liczba
- Adres IP klienta
- Parametr pliku cookie
- Wyrażenie regularne parametru pliku cookie
- Kraj
- Urządzenie
- Krawędź Cname
- Odwoływanie domeny
- Literał nagłówka żądania
- Wyrażenie regularne nagłówka żądania
- Symbol wieloznaczny nagłówka żądania
- Request — metoda
- Schemat żądania
- Adres URL zapytania literału
- Adres URL zapytania Regex
- Adres URL zapytania z symboli wieloznacznych
- Adres URL parametru zapytania

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="compress-file-types"></a>Kompresuj typów plików
**Cel:** definiuje formatach plików, które są kompresowane na serwerze.

Format pliku można określić za pomocą jego typ nośnika Internet (na przykład Content-Type). Typ nośnika Internet jest metadanych niezależne od platformy, które umożliwia serwerom zidentyfikować format pliku określonego zasobu. Listę typowych nośnika Internet podano poniżej.

Typ nośnika Internet|Opis
--|--
zwykły tekst|Pliki w formacie zwykłego tekstu
tekst i html| Pliki HTML
tekst/css|Kaskadowe arkusze stylów (CSS)
application/x-javascript|Javascript
Aplikacja/javascript|Javascript
Informacje o kluczu:

- Oddzielającego każdej z nich z jednego miejsca, aby określić wiele typów nośników Internet. 
- Ta funkcja kompresuje tylko zasoby, którego rozmiar jest mniejszy niż 1 MB. Większe zasoby nie są kompresowane przez serwery.
- Niektórych typów zawartości, takich jak obrazy, wideo i audio nośnika zasoby (na przykład, JPG, MP3, MP4, itp.), są już kompresowane. Ponieważ dodatkowe kompresję dla tych typów zasobów nie znacznie zmniejsza rozmiar pliku, zalecane jest, nie należy włączać kompresji na nich.
- Symbole wieloznaczne, takie jak gwiazdek, nie są obsługiwane.
- Przed dodaniem tej funkcji do reguły upewnij się, że ustawieniu opcji kompresji wyłączone na stronie kompresji dla platformy, do którego jest stosowana ta reguła.

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="custom-log-field-1"></a>Pole niestandardowe dziennika 1
**Cel:** Określa format i zawartość, która zostanie przypisana do pola dziennik niestandardowy w pierwotnych pliku dziennika.

To pole niestandardowe pozwala określić, które wartości nagłówka żądania i odpowiedzi są przechowywane w plikach dziennika.

Domyślnie pole dziennik niestandardowy jest nazywany "x-ec_custom-1." Na stronie pierwotnych ustawień dziennika można dostosować nazwę tego pola.

Format służący do określania nagłówki żądań i odpowiedzi jest zdefiniowane w następujący sposób:

Header — typ|Format|Przykłady
-|-|-
Nagłówek żądania|%{[RequestHeader]()}[i]() | %{Accept-Encoding}i <br/> {Odnośnik} i <br/> %{Authorization}i
Nagłówek odpowiedzi|%{[ResponseHeader]()}[o]()| %{Age}o <br/> %{Content-Type}o <br/> %{Cookie}o

Informacje o kluczu:

- Pola niestandardowe dziennika może zawierać dowolną kombinację pola nagłówka i zwykły tekst.
- Prawidłowe znaki dla tego pola są następujące: alfanumeryczne (0-9, a-z, a A-Z), łączniki, dwukropki średnikami, apostrofów, przecinkami, kropki, podkreślenia, znaku równości, nawiasy, nawiasy i spacje. Symbol procentu i nawiasy klamrowe są dozwolone tylko po używany do określenia pola nagłówka.
- Pisownia dla każdego pola określony nagłówek musi odpowiadać nazwie nagłówka odpowiednie żądanie/odpowiedź.
- Jeśli chcesz określić wiele nagłówków, umożliwia wskazanie każdy nagłówek separatora. Na przykład można użyć skrótu dla każdego nagłówka:
    - AE: % {zaakceptować kodowania} i odpowiedź: % {autoryzacji} i IERZ: % {Content-Type} o 

**Wartość domyślna:** -

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="debug-cache-response-headers"></a>Debugowanie nagłówki odpowiedzi pamięci podręcznej
**Cel:** Określa, czy odpowiedź może zawierać nagłówek odpowiedzi we-X-Debug, który zawiera informacje dotyczące zasady pamięci podręcznej dla żądanego zasobu.

Debugowanie odpowiedzi pamięci podręcznej, który nagłówki mają być uwzględnieni w odpowiedzi, gdy są spełnione oba poniższe:

- Funkcja debugowania nagłówki odpowiedzi pamięci podręcznej został włączony na odpowiednie żądania.
- Żądanie powyżej definiuje zestaw debugowania nagłówki odpowiedzi pamięci podręcznej, które zostaną uwzględnione w odpowiedzi.

Debugowanie nagłówki może wystąpić przy tym następujący nagłówek i odpowiednie dyrektywy w żądaniu odpowiedzi pamięci podręcznej:

X-WE Debug: _Directive1_,_Directive2_,_DirectiveN_

**Przykład:**

WE-X-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state

Wartość|Wynik
-|-
Enabled (Włączony)|Żądania dla strony nagłówki odpowiedzi pamięci podręcznej debugowania będzie zwracać odpowiedzi, który zawiera nagłówek X-WE-Debug.
Disabled (Wyłączony)|Nagłówka X-WE-Debug odpowiedzi zostaną wykluczone z odpowiedzi.

**Domyślne zachowanie:** wyłączone.

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="default-internal-max-age"></a>Max-Age wewnętrzny domyślne
**Cel:** określa maksymalny wiek domyślny interwał dla punktu obecności, ponowna Walidacja buforu serwera pochodzenia. Innymi słowy ilość czasu, jaki upłynie przed punktu obecności sprawdzi, czy buforowanych zasobów zgodny zasobów przechowywanych na serwerze źródłowym.

Informacje o kluczu:

- Ta akcja ma miejsce tylko dla odpowiedzi z serwera pochodzenia, które nie zostały przypisane oznaczenie maksymalny wiek w `Cache-Control` lub `Expires` nagłówka.
- Ta akcja nie zostanie przeprowadzone zasobów, które nie są uważane za buforowalnej.
- Ta akcja nie ma wpływu na przeglądarce POP revalidations pamięci podręcznej. Tego rodzaju revalidations są określane przez `Cache-Control` lub `Expires` nagłówki wysyłany do przeglądarki, które można dostosować za pomocą funkcji Max-Age zewnętrznych.
- Wyniki tej akcji nie mają zauważalne wpływ na nagłówki odpowiedzi i zawartość zwróconą z POP dla zawartości, ale może mieć wpływ na ilość ruchu sieciowego ponowna Walidacja wysyłane z POP do serwera pochodzenia.
- Konfigurowanie tej funkcji przez:
    - Wybieranie kod stanu, dla którego można zastosować domyślnego wewnętrzny max wieku.
    - Określanie wartość całkowitą, a następnie wybierając jednostkę żądany czas (na przykład sekundy, minuty, godziny itd.). Ta wartość Określa domyślny interwał maksymalny wiek wewnętrznego.

- Ustawienie jednostkę czasu na wartość "Off" przypisze domyślny wewnętrzny maksymalny wiek interwał 7 dni dla żądań, które nie zostały przypisane oznaczenie maksymalny wiek w ich `Cache-Control` lub `Expires` nagłówka.

**Wartość domyślna:** 7 dni

#### <a name="compatibility"></a>Zgodność
Ze względu na sposób, w których pamięci podręcznej ustawienia są śledzone ta funkcja nie może być skojarzony z następujących warunków dopasowania: 
- JAKO liczba
- Adres IP klienta
- Parametr pliku cookie
- Wyrażenie regularne parametru pliku cookie
- Kraj
- Urządzenie
- Krawędź Cname
- Odwoływanie domeny
- Literał nagłówka żądania
- Wyrażenie regularne nagłówka żądania
- Symbol wieloznaczny nagłówka żądania
- Request — metoda
- Schemat żądania
- Adres URL zapytania literału
- Adres URL zapytania Regex
- Adres URL zapytania z symboli wieloznacznych
- Adres URL parametru zapytania

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="deny-access-403"></a>Odmowa dostępu (403)
**Cel**: Określa, czy wszystkie żądania są odrzucane odpowiedź 403 Zabroniony.

Wartość | Wynik
------|-------
Enabled (Włączony)| Powoduje, że wszystkie żądania, które spełniają kryteria dopasowywania procesów odrzucona z odpowiedź 403 Zabroniony.
Disabled (Wyłączony)| Przywraca domyślne zachowanie. Domyślnym zachowaniem jest umożliwienie do serwera pochodzenia, aby ustalić typ odpowiedzi, który zostanie zwrócony.

**Domyślne zachowanie**: wyłączone

> [!TIP]
   > Jedno możliwe użycie tej funkcji jest aby skojarzyć ją z warunkiem dopasowania nagłówek żądania, aby zablokować dostęp do odwołań HTTP, korzystających z wbudowanym łącza do zawartości.

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="expires-header-treatment"></a>Wygasa traktowania nagłówka
**Cel:** kontroluje Generowanie `Expires` nagłówki przez POP, gdy funkcja zewnętrznych Max-Age jest aktywny.

Najprostszym sposobem uzyskania tego typu konfiguracji jest umieścić zewnętrznych maksymalny wiek i funkcje wygasa traktowania nagłówka w tej samej instrukcji.

Wartość|Wynik
--|--
Zastąp|Zapewnia, że będzie zostaną wykonane następujące czynności:<br/>-Zastępuje `Expires` nagłówka wygenerowane przez serwer pochodzenia.<br/>-Dodaje `Expires` nagłówka utworzonej przez funkcję zewnętrznych Max-Age do odpowiedzi.
Przekazywanie|Zapewnia, że `Expires` nagłówka utworzonej przez funkcję zewnętrznych Max-Age nigdy nie zostanie dodany do odpowiedzi. <br/> Jeśli serwer pochodzenia `Expires` nagłówka, jego przechodziła przez użytkownika końcowego. <br/>Jeśli na serwerze źródłowym nie tworzy `Expires` nagłówek, a następnie ta opcja może spowodować nagłówek odpowiedzi nie będzie zawierał `Expires` nagłówka.
Jeśli brakuje dodać| Jeśli `Expires` nagłówka nie odebrano z serwera pochodzenia, a następnie ta opcja dodaje `Expires` nagłówka utworzonej przez funkcję zewnętrznych Max-Age. Ta opcja jest przydatna do zapewnienia, że wszystkie zasoby zostaną przypisane `Expires` nagłówka.
Remove| Zapewnia, że `Expires` nagłówka nie jest dołączony do odpowiedzi nagłówek. Jeśli `Expires` nagłówka została już przypisana, a następnie jest usuwany z nagłówka odpowiedzi.

**Domyślne zachowanie:** zastępowania

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="external-max-age"></a>Max-Age zewnętrznych
**Cel:** określa maksymalny wiek interwał przeglądarce ponowna Walidacja buforu POP. Innymi słowy ilość czasu, jaki upłynie przed przeglądarką można sprawdzić nową wersję elementu zawartości z punktu obecności.

Włączenie tej funkcji spowoduje wygenerowanie `Cache-Control: max-age` i `Expires` nagłówków z lokalizacji POP i wysyłać je do klienta HTTP. Domyślnie te nagłówki spowoduje zastąpienie utworzone przez serwer pochodzenia. Jednak traktowania nagłówek Cache-Control i funkcje wygasa traktowania nagłówka pozwala zmienić to zachowanie.

Informacje o kluczu:

- Ta akcja nie wpływa na POP do revalidations pamięci podręcznej serwera pochodzenia. Tego rodzaju revalidations są określane przez `Cache-Control` i `Expires` nagłówki otrzymany z serwera pochodzenia i można dostosować za pomocą domyślnych wewnętrznych Max-Age i funkcji Force wewnętrzny Max-Age.
- Tej funkcji można skonfigurować, określając wartość całkowitą i wybierając jednostkę żądany czas (na przykład sekundy, minuty, godziny itd.).
- Ustawienie tej funkcji na wartość ujemna powoduje POP wysłać `Cache-Control: no-cache` i `Expires` czas, które są ustawione w przeszłości z każdym odpowiedzi do przeglądarki. Mimo że klient HTTP nie będzie buforować odpowiedzi, to ustawienie nie wpłynie POP możliwość buforowania odpowiedzi z serwera pochodzenia.
- Ustawienie jednostkę czasu na wartość "Off" spowoduje wyłączenie tej funkcji. `Cache-Control` i `Expires` nagłówków pamięci podręcznej z odpowiedzią serwera pochodzenia mają być przekazywane do przeglądarki.

**Domyślne zachowanie:** wyłączone

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="follow-redirects"></a>Wykonaj przekierowania
**Cel:** Określa, czy nazwa hosta zdefiniowane w nagłówku lokalizacji zwróconych przez serwer pochodzenia klienta można przekierować żądania.

Informacje o kluczu:

- Tylko można przekierować żądania do krawędzi rekordów CNAME, które odpowiadają tej samej platformy.

Wartość|Wynik
-|-
Enabled (Włączony)|Można przekierować żądania.
Disabled (Wyłączony)|Nie będzie można przekierować żądania.

**Domyślne zachowanie:** wyłączone.

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="force-internal-max-age"></a>Wymuszanie wewnętrznych Max-Age.
**Cel:** Określa interwał maksymalny wiek dla punktu obecności, ponowna Walidacja buforu serwera pochodzenia. Innymi słowy ilość czasu, jaki upłynie przed punktu obecności można sprawdzić, czy zasób pamięci podręcznej odpowiada zasobów przechowywanych na serwerze źródłowym.

Informacje o kluczu:

- Ta funkcja spowoduje zastąpienie interwału maksymalny wiek zdefiniowane w `Cache-Control` lub `Expires` nagłówki wygenerowane z serwera pochodzenia.
- Ta funkcja nie ma wpływu na przeglądarce POP revalidations pamięci podręcznej. Tego rodzaju revalidations są określane przez `Cache-Control` lub `Expires` nagłówki wysyłany do przeglądarki.
- Ta funkcja nie ma efektu zauważalne w odpowiedzi dostarczonych przez punkt obecności do zleceniodawcy. Jednak może mieć wpływ na ilość ruchu sieciowego ponowna Walidacja wysyłane z lokalizacji POP do serwera pochodzenia.
- Konfigurowanie tej funkcji przez:
    - Wybieranie kod stanu, dla których zostaną zastosowane wewnętrzny wieku max.
    - Określanie całkowitą i wybierając jednostkę żądany czas (na przykład sekundy, minuty, godziny itd.). Ta wartość Określa interwał maksymalny wiek żądania.

- Ustawienie jednostkę czasu na wartość "Off" powoduje wyłączenie tej funkcji. Wewnętrzny interwał maksymalny wiek nie zostanie przypisana do żądanych zasobów. Jeśli oryginalny nagłówek nie zawiera instrukcji buforowania, zasobu będą buforowane zgodnie z ustawieniem active w funkcji domyślne wewnętrzny Max-Age.

**Domyślne zachowanie:** wyłączone

#### <a name="compatibility"></a>Zgodność
Ze względu na sposób, w których pamięci podręcznej ustawienia są śledzone ta funkcja nie może być skojarzony z następujących warunków dopasowania: 
- JAKO liczba
- Adres IP klienta
- Parametr pliku cookie
- Wyrażenie regularne parametru pliku cookie
- Kraj
- Urządzenie
- Krawędź Cname
- Odwoływanie domeny
- Literał nagłówka żądania
- Wyrażenie regularne nagłówka żądania
- Symbol wieloznaczny nagłówka żądania
- Request — metoda
- Schemat żądania
- Adres URL zapytania literału
- Adres URL zapytania Regex
- Adres URL zapytania z symboli wieloznacznych
- Adres URL parametru zapytania

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="h264-support-http-progressive-download"></a>Obsługa H.264 (pobierania progresywnego HTTP)
**Cel:** Określa typy H.264 formatów plików, które mogą służyć do strumieniowego przesyłania zawartości.

Informacje o kluczu:

- Zdefiniuj zestaw dozwolonych rozszerzenia nazw plików H.264 rozdzielonych spacjami w opcji rozszerzenia plików. Opcja rozszerzenia pliku zastępują domyślne zachowanie. Obsługa MP4 i F4V pomocy technicznej przez dołączenie te rozszerzenia nazw plików, ustawiając tę opcję. 
- Należy uwzględnić okres podczas określania każde rozszerzenie nazwy pliku (na przykład plik MP4 .f4v).

**Domyślne zachowanie:** pobierania progresywnego HTTP obsługuje nośników MP4 i F4V domyślnie.

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="honor-no-cache-request"></a>Honoruj No-Cache żądania
**Cel:** Określa, czy klient HTTP przez nie pamięci podręcznej żądań zostaną przekazane do serwera pochodzenia.

Żądanie pamięci podręcznej nie występuje po wysłaniu klienta HTTP `Cache-Control: no-cache` i/lub `Pragma: no-cache` nagłówka w żądaniu HTTP.

Wartość|Wynik
--|--
Enabled (Włączony)|Umożliwia żądania klienta HTTP pamięci podręcznej nie mają być przekazywane do serwera pochodzenia i serwera pochodzenia zwróci nagłówki odpowiedzi i treści za pośrednictwem protokołu POP do klienta HTTP.
Disabled (Wyłączony)|Przywraca domyślne zachowanie. Domyślnym zachowaniem jest aby zapobiec żądań pamięci podręcznej nie są przekazywane do serwera pochodzenia.

Dla całego ruchu w środowisku produkcyjnym zaleca pozostaw tę funkcję w stanie domyślnym wyłączone. W przeciwnym razie pochodzenia serwery będą nie można włączyć osłony użytkowników końcowych, którzy mogą przypadkowo wyzwalać wiele żądań pamięci podręcznej nie podczas odświeżania strony sieci web lub z wielu odtwarzacze multimedialne popularnych, które są zakodowane na wysyłaj nagłówek nie pamięci podręcznej z każdym żądaniem wideo. Niemniej jednak ta funkcja może być przydatne do zastosowania do niektórych nieprodukcyjnych przemieszczania lub testowania katalogów, aby umożliwić nowej zawartości na żądanie pobrania z serwera pochodzenia.

Stan pamięci podręcznej, która będzie zgłaszana dla żądania, który może być przekazywane do serwera pochodzenia z powodu ta funkcja jest TCP_Client_Refresh_Miss. Raport stany pamięci podręcznej, który jest dostępny w podstawowej modułu raportowania, informacje statystyczne według stanu pamięci podręcznej. Dzięki temu można śledzić liczbę i odsetek żądań, które są przesyłane do serwera pochodzenia z powodu tej funkcji.

**Domyślne zachowanie:** wyłączone.

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="ignore-origin-no-cache"></a>Ignoruj pochodzenia No-Cache
**Cel:** Określa, czy sieć CDN będzie ignorować następujące dyrektywy udostępniane przez serwer pochodzenia:

- `Cache-Control: private`
- `Cache-Control: no-store`
- `Cache-Control: no-cache`
- `Pragma: no-cache`

Informacje o kluczu:

- Tej funkcji można skonfigurować, definiując rozdzieloną spacjami listę kodów stanu, dla których zostaną zignorowane powyżej dyrektywy.
- Zestaw kodów stanu prawidłowy dla tej funkcji: 200, 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 i 505.
- Wyłączyć tę funkcję, ustawiając wartość pustą.

**Domyślne zachowanie:** domyślne zachowanie to uwzględnić dyrektywy powyżej.

#### <a name="compatibility"></a>Zgodność
Ze względu na sposób, w których pamięci podręcznej ustawienia są śledzone ta funkcja nie może być skojarzony z następujących warunków dopasowania: 
- JAKO liczba
- Adres IP klienta
- Parametr pliku cookie
- Wyrażenie regularne parametru pliku cookie
- Kraj
- Urządzenie
- Krawędź Cname
- Odwoływanie domeny
- Literał nagłówka żądania
- Wyrażenie regularne nagłówka żądania
- Symbol wieloznaczny nagłówka żądania
- Request — metoda
- Schemat żądania
- Adres URL zapytania literału
- Adres URL zapytania Regex
- Adres URL zapytania z symboli wieloznacznych
- Adres URL parametru zapytania

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="ignore-unsatisfiable-ranges"></a>Ignoruj Unsatisfiable zakresów 
**Cel:** określa odpowiedź, który będzie zwracanych do klientów, gdy żądanie generuje 416 żądany zakres nie niewłaściwego kod stanu.

Domyślnie ten kod stanu jest zwracany podczas żądania zakresu bajtów nie mogą być spełnione przez punkt obecności i nie określono pola nagłówka żądania If-Range.

Wartość|Wynik
-|-
Enabled (Włączony)|Zapobiega lokalizacji POP odpowiada na żądania nieprawidłowy zakres bajtów z 416 żądany zakres nie niewłaściwego kodem stanu. Zamiast tego serwery dostarczyć żądanych zasobów i zwrócić 200 OK do klienta.
Disabled (Wyłączony)|Przywraca domyślne zachowanie. Domyślnym zachowaniem jest uwzględnić 416 żądany zakres nie niewłaściwego kod stanu.

**Domyślne zachowanie:** wyłączone.

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="internal-max-stale"></a>Wewnętrzny odświeżona maksymalna
**Cel:** kontroli, jak długo późniejsza niż godzina wygaśnięcia normalne, zasobów pamięci podręcznej mogą być udostępniane przez punktu obecności, gdy POP nie może ponownie sprawdź poprawność buforowanych zasobów w serwerze źródłowym.

Zwykle po upływie czasu maksymalny wiek zasobów, punktu obecności wyśle żądanie ponownego sprawdzania poprawności do serwera pochodzenia. Ze źródła — wersja serwera zostanie następnie odpowiedź z obu 304 nie zmodyfikowane w celu nadać POP świeża dzierżawy w pamięci podręcznej zasobów lub z 200 OK zapewnienie POP zaktualizowaną wersję elementu zawartości pamięci podręcznej.

W przypadku nie można nawiązać połączenia z serwerem pochodzenia Podczas próby takich ponownego sprawdzania poprawności, a następnie ta funkcja wewnętrzny odświeżona Max Określa, czy i jak długo POP, punktu obecności mogą nadal służyć zasobów obecnie przestarzały.

Należy pamiętać, że dany interwał czasu zaczyna się po wygaśnięciu wieku max elementu zawartości, nie, jeśli nie powiodło się ponowna Walidacja występuje. Dlatego maksymalny okres, w którym mogą być przekazywane zasób bez pomyślnego ponownego sprawdzania poprawności jest określone przez kombinację maksymalny wiek plus odświeżona maksymalny czas. Na przykład jeśli zasób był buforowany 9:00, maksymalny wiek 30 minut i max przestarzały 15 minut, następnie ponowna Walidacja nie powiodło się próba 9:44 w rezultacie użytkownik końcowy odbieranie starych zasobów pamięci podręcznej, podczas ponownego sprawdzania poprawności nie powiodło się próba 9:46 spowodowałoby en Użytkownik d odbieranie 504 Limit czasu bramy.

Dowolna wartość skonfigurowane dla tej funkcji są zastępowane przez `Cache-Control: must-revalidate` lub `Cache-Control: proxy-revalidate` nagłówki otrzymany z serwera pochodzenia. Odebranie jednej z tych nagłówków z serwera pochodzenia kiedy zasób początkowo są buforowane, następnie POP nie będzie obsługiwać starych zasobów pamięci podręcznej. W takim przypadku jeśli POP nie będzie mógł ponownie zatwierdzać ze źródła, gdy wygaśnie interwał maksymalny wiek elementu zawartości, punktu obecności zwraca 504 błąd upływu limitu czasu bramy.

Informacje o kluczu:

- Konfigurowanie tej funkcji przez:
    - Wybieranie kod stanu, dla których zostaną zastosowane odświeżona max.
    - Określanie wartość całkowitą, a następnie wybierając jednostkę żądany czas (na przykład sekundy, minuty, godziny itd.). Ta wartość określa wewnętrzny max odświeżona które zostaną zastosowane.

- Ustawienie jednostkę czasu na wartość "Off" spowoduje wyłączenie tej funkcji. Zasobów pamięci podręcznej nie zostanie obsłużona poza jego czas wygaśnięcia normalnego.

**Domyślne zachowanie:** dwie minuty

#### <a name="compatibility"></a>Zgodność
Ze względu na sposób, w których pamięci podręcznej ustawienia są śledzone ta funkcja nie może być skojarzony z następujących warunków dopasowania: 
- JAKO liczba
- Adres IP klienta
- Parametr pliku cookie
- Wyrażenie regularne parametru pliku cookie
- Kraj
- Urządzenie
- Krawędź Cname
- Odwoływanie domeny
- Literał nagłówka żądania
- Wyrażenie regularne nagłówka żądania
- Symbol wieloznaczny nagłówka żądania
- Request — metoda
- Schemat żądania
- Adres URL zapytania literału
- Adres URL zapytania Regex
- Adres URL zapytania z symboli wieloznacznych
- Adres URL parametru zapytania

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="log-query-string"></a>Ciąg zapytania dziennika
**Cel:** Określa, czy ciąg zapytania będą przechowywane wraz z adresu URL w dziennikach dostępu.

Wartość|Wynik
-|-
Enabled (Włączony)|Umożliwia przechowywanie ciągów zapytania podczas rejestrowania w dzienniku dostępu do adresów URL. Jeśli adres URL zawiera ciąg zapytania, następnie ta opcja nie będzie miało wpływu.
Disabled (Wyłączony)|Przywraca domyślne zachowanie. Domyślnym zachowaniem jest zignorowanie ciągi zapytań podczas rejestrowania w dzienniku dostępu do adresów URL.

**Domyślne zachowanie:** wyłączone.

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="maximum-keep-alive-requests"></a>Maksymalna liczba żądań Keep-Alive
**Cel:** określa maksymalną liczbę żądań Keep-Alive połączenia przed jego zamknięciem.

Maksymalna liczba żądań niskiej wartości jest zalecane i może spowodować obniżenie wydajności.

Informacje o kluczu:

- Tę wartość można określić jako liczbą całkowitą.
- Nie należy dołączać kropki i przecinki w określonej wartości.

**Wartość domyślna:** 10000 żądań

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="modify-client-request-header"></a>Modyfikowanie nagłówek żądania klienta
**Cel:** każdego żądania zawiera zestaw nagłówków żądań, które zawiera jego opis. Ta funkcja może być:

- Dołącz lub zastąpić wartość przypisana do nagłówka żądania. Jeśli określonego nagłówka żądania nie istnieje, następnie ta funkcja zostanie dodane do żądania.
- Usuń nagłówek żądania z żądania.

Żądania, które są przekazywane do serwera pochodzenia, zostaną one zastosowane zmiany wprowadzone przez tę funkcję.

W nagłówku żądania można wykonać jedną z następujących czynności:

Opcja|Opis|Przykład
-|-|-
Append|Określona wartość zostanie dodany na końcu istniejącą wartość nagłówka żądania.|**Wartość nagłówka (klient) żądania:**wartość1 <br/> **Żądanie wartość nagłówka (aparat reguł HTTP):** wartość2 <br/>**Nowa wartość nagłówka żądania:** Value1Value2
Zastąp|Wartość nagłówka żądania zostanie ustawiona na określoną wartość.|**Wartość nagłówka (klient) żądania:**wartość1 <br/>**Żądanie wartość nagłówka (aparat reguł HTTP):** wartość2 <br/>**Nowa wartość nagłówka żądania:** wartość2 <br/>
Usuwanie|Usuwa określonego nagłówka żądania.|**Wartość nagłówka (klient) żądania:**wartość1 <br/> **Zmodyfikuj konfigurację nagłówek żądania klienta:** usunąć w nagłówku żądania. <br/>**Wynik:** określonego nagłówka żądania nie zostaną przekazane do serwera pochodzenia.

Informacje o kluczu:

- Upewnij się, że wartość określona w opcji Nazwa jest dokładnym odpowiednikiem nagłówka odpowiednie żądania.
- Case nie jest brana pod uwagę w celu zidentyfikowania nagłówka. Na przykład żadnego z następujących zmian `Cache-Control` nazwa nagłówka może służyć do identyfikowania:
    - cache-control
    - CACHE-CONTROL
    - cachE-Control
- Podczas określania nazwy nagłówka, należy użyć tylko znaki alfanumeryczne, łączniki lub podkreślenia.
- Usuwanie nagłówka będą zapobiegać jej są przekazywane do serwera pochodzenia przez lokalizacji POP.
- Następujące nagłówki są zarezerwowane i nie można modyfikować za pomocą tej funkcji:
    - przekazany
    - host
    - za pomocą
    - ostrzeżenie
    - x przekazywane do
    - Wszystkie nazwy nagłówka rozpoczynających się od "x WE" są zastrzeżone.

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="modify-client-response-header"></a>Modyfikowanie nagłówka odpowiedzi klienta
Każda odpowiedź zawiera zestaw opisujące go nagłówków odpowiedzi. Ta funkcja może być:

- Dołącz lub zastąpić wartość przypisana do nagłówka odpowiedzi. Jeśli określonego nagłówka odpowiedzi nie istnieje, następnie ta funkcja zostanie dodane do odpowiedzi.
- Usuń nagłówek odpowiedzi z odpowiedzi.

Domyślnie wartości nagłówka odpowiedzi są definiowane przez serwer pochodzenia i lokalizacji POP.

W nagłówku odpowiedzi można wykonać jedną z następujących czynności:

Opcja|Opis|Przykład
-|-|-
Append|Określona wartość zostanie dodany na końcu istniejącej wartości nagłówka odpowiedzi.|**Wartość nagłówka odpowiedzi (klient):**wartość1 <br/> **Wartość nagłówka odpowiedzi (aparat reguł HTTP):** wartość2 <br/>**Nowa wartość nagłówka odpowiedzi:** Value1Value2
Zastąp|Będzie można ustawić wartości nagłówka odpowiedzi na określoną wartość.|**Wartość nagłówka odpowiedzi (klient):**wartość1 <br/>**Wartość nagłówka odpowiedzi (aparat reguł HTTP):** wartość2 <br/>**Nowa wartość nagłówka odpowiedzi:** wartość2 <br/>
Usuwanie|Usuwa określonego nagłówka odpowiedzi.|**Wartość nagłówka odpowiedzi (klient):** wartość1 <br/> **Zmodyfikuj konfigurację nagłówka odpowiedzi klienta:** usunąć zagrożona nagłówka odpowiedzi. <br/>**Wynik:** określonego nagłówka odpowiedzi nie zostaną przekazane do zleceniodawcy.

Informacje o kluczu:

- Upewnij się, że wartość określona w opcji Nazwa jest dokładnym odpowiednikiem dla nagłówka odpowiedzi żądany. 
- Case nie jest brana pod uwagę w celu zidentyfikowania nagłówka. Na przykład żadnego z następujących zmian `Cache-Control` nazwa nagłówka może służyć do identyfikowania:
    - cache-control
    - CACHE-CONTROL
    - cachE-Control
- Usuwanie nagłówka uniemożliwia jego przesyłane dalej do zleceniodawcy.
- Następujące nagłówki są zarezerwowane i nie można modyfikować za pomocą tej funkcji:
    - accept-encoding
    - okres ważności
    - połączenie
    - content-encoding
    - content-length
    - zakres zawartości
    - data
    - serwer
    - przyczepy
    - Transfer-encoding
    - Uaktualnienie
    - różnią się
    - za pomocą
    - ostrzeżenie
    - Wszystkie nazwy nagłówka rozpoczynających się od "x WE" są zastrzeżone.

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="partial-cache-sharing"></a>Udostępnianie częściowe pamięci podręcznej
**Cel:** Określa, czy żądanie może wygenerować częściowo buforowaną zawartość.

Ta częściowa pamięć podręczna może następnie służyć do spełnienia nowych żądań dla tej zawartości do momentu żądanej zawartości jest w pełni pamięci podręcznej.

Wartość|Wynik
-|-
Enabled (Włączony)|Żądania mogą generować częściowo buforowaną zawartość.
Disabled (Wyłączony)|Żądania można generować tylko pełni buforowanej wersji żądanej zawartości.

**Domyślne zachowanie:** wyłączone.

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="prevalidate-cached-content"></a>Prevalidate zawartości w pamięci podręcznej
**Cel:** Określa, czy przed wygaśnięciem wartość TTL będzie kwalifikuje się do wcześniejszego ponowna Walidacja zawartości w pamięci podręcznej.

Zdefiniuj czas przed wygaśnięciem TTL żądanej zawartości, w którym będzie kwalifikuje się do wcześniejszego ponownego sprawdzania poprawności.

Informacje o kluczu:

- Wybieranie "Off" jako jednostka czasu wymaga ponownego sprawdzania poprawności została wykonana po zawartości pamięci podręcznej TTL utracił ważność. Czas nie należy określać i jest ignorowana.

**Domyślne zachowanie:** Off. Ponowna Walidacja może mieć miejsce tylko, po upływie czas wygaśnięcia zawartości pamięci podręcznej.

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="proxy-special-headers"></a>Serwer proxy specjalnych nagłówków
**Cel:** definiuje zestaw specyficzne dla sieci CDN w warstwie nagłówków żądań, które będą przesyłane z punktu obecności do serwera pochodzenia.

Informacje o kluczu:

- Każdy nagłówek żądania specyficzne dla usługi CDN zdefiniowany w tej funkcji zostanie przekazany do serwera pochodzenia.
- Nagłówek żądania specyficzne dla usługi CDN uniemożliwić są przekazywane do serwera pochodzenia przez usunięcie go z tej listy.

**Domyślne zachowanie:** wszystkie nagłówki żądania specyficzne dla usługi CDN zostaną przekazane do serwera pochodzenia.

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="refresh-zero-byte-cache-files"></a>Odśwież Zero bajtów pamięci podręcznej plików
**Cel:** określa sposób obsługi żądań klienta HTTP dla trwałego 0 bajtów pamięci podręcznej przez punkty obecności.

Prawidłowe wartości to:

Wartość|Wynik
--|--
Enabled (Włączony)|Powoduje, że POP do refetch zasobów z serwera pochodzenia.
Disabled (Wyłączony)|Przywraca domyślne zachowanie. Domyślnym zachowaniem jest do obsługi się zasoby prawidłowy pamięci podręcznej na żądanie.
Ta funkcja nie jest wymagany do buforowania poprawne i dostarczania zawartości, ale może służyć jako obejście tego problemu. Na przykład dynamiczne generatory zawartości na serwerach pochodzenia przypadkowo może spowodować 0 bajtów odpowiedzi są wysyłane do lokalizacji POP. Tych typów odpowiedzi są buforowane w lokalizacji POP. Jeśli wiesz, że odpowiedź 0-bajtowych nigdy nie jest prawidłowa odpowiedź 

takie zawartości następnie tej funkcji uniemożliwia tych typów zasobów jest obsługiwane dla klientów.

**Domyślne zachowanie:** wyłączone.

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="set-cacheable-status-codes"></a>Kody stanu Buforowalnej zestawu
**Cel:** definiuje zestaw kodów stanu, które mogą skutkować zawartości w pamięci podręcznej.

Domyślnie buforowanie jest włączona tylko 200 OK odpowiedzi.

Zdefiniuj zestaw rozdzielonych spacjami kodów żądany stan.

Informacje o kluczu:

- Włącz funkcję Ignoruj pochodzenia No-Cache. Jeśli ta funkcja nie jest włączona, następnie odpowiedzi z systemem innym niż 200 OK może nie być pamięci podręcznej.
- Zestaw kodów stanu prawidłowy dla tej funkcji: 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 i 505.
- Nie można użyć tej funkcji można wyłączyć buforowanie odpowiedzi, które generują kod 200 OK stanu.

**Domyślne zachowanie:** buforowanie jest włączone tylko w przypadku odpowiedzi generujących kod 200 OK stanu.

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="set-client-ip-custom-header"></a>Wartość niestandardowego nagłówka adresu IP klienta
**Cel:** dodaje niestandardowy nagłówek, który identyfikuje klienta za pomocą adresu IP na żądanie.

Opcja nazwy nagłówka definiuje nazwę nagłówka żądania niestandardowe, gdzie znajduje się adres IP klienta.

Ta funkcja umożliwia klienta adresy serwera źródłowego, aby dowiedzieć się, IP klienta za pośrednictwem nagłówków żądań niestandardowych. Jeśli żądanie jest podawana z pamięci podręcznej, serwer pochodzenia nie wyświetli się informacja o adres IP klienta. W związku z tym zaleca się, że ta funkcja ma być używana z zasobów, które nie są buforowane.

Upewnij się, że nazwa określonego nagłówka nie pasuje do żadnego z następujących nazw:

- Nazwy nagłówków żądań standardowych. Lista nazw standardowy nagłówek znajdują się w [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).
- Nazwy nagłówków zastrzeżone:
    - przekazywane do
    - host
    - różnią się
    - za pomocą
    - ostrzeżenie
    - x przekazywane do
    - Wszystkie nazwy nagłówka rozpoczynających się od "x WE" są zastrzeżone.

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="stale-content-delivery-on-error"></a>Stałe dostarczanie zawartości w przypadku błędu
**Cel:** Określa, czy po wystąpieniu błędu podczas ponownego sprawdzania poprawności pamięci podręcznej lub podczas pobierania żądanej zawartości z serwera pochodzenia odbiorcy będą dostarczane wygasłej zawartości pamięci podręcznej.

Wartość|Wynik
-|-
Enabled (Włączony)|Zawartość jest udostępniany żądający po wystąpieniu błędu podczas połączenia z serwerem pochodzenia.
Disabled (Wyłączony)|Błąd na serwerze źródłowym jest przekazywany do zleceniodawcy.

**Domyślne zachowanie:** wyłączone

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="stale-while-revalidate"></a>Nieaktualne podczas Revalidate
**Cel:** zwiększa wydajność, zezwalając POP, które udostępniają zawartość zleceniodawca podczas ponownego sprawdzania poprawności ma miejsce.

Informacje o kluczu:

- Zachowanie tej funkcji zależy od jednostki wybrana wartość czasu.
    - **Jednostka czasu:** określ długość czasu, a następnie wybierz jednostki czasu (na przykład sekundy, minuty, godziny, itp.) umożliwia starych dostarczania zawartości. Ten typ Instalatora umożliwia CDN rozszerzenie czas, który może zostać zawartości przed wymaganiem weryfikacji według następującego wzoru: **TTL** + **opcję czas starych podczas Sprawdź poprawność ponownie** 
    - **OFF:** wybierz pozycję "wyłączone" Aby wymagać ponownego sprawdzania poprawności, zanim żądanie dla może zostać wyświetlona zawartość.
        - Nie określaj długość czasu, ponieważ nie ma zastosowania i zostaną zignorowane.

**Domyślne zachowanie:** Off. Ponowna Walidacja musi odbywać się przed żądanej zawartości mogą być udostępniane.

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth"></a>Token uwierzytelniania
**Cel:** Określa, czy uwierzytelnianie na podstawie tokenu zostaną zastosowane do żądania.

Włączenie uwierzytelniania opartego na tokenie tylko żądania, które zapewniają zaszyfrowany token i są zgodne z wymogami określone przez token będą honorowane.

Klucz szyfrowania, który jest używany do szyfrowania i odszyfrowywania tokenów wartości jest określana przez klucz podstawowy i opcje tworzenia kopii zapasowej klucza na stronie tokenu uwierzytelniania. Należy pamiętać, że klucze szyfrowania są specyficzne dla platformy.

**Domyślne zachowanie:** wyłączone.

Ta funkcja ma pierwszeństwo przed większość funkcji, z wyjątkiem funkcja ponowne zapisywanie adresów URL.

Wartość | Wynik
------|---------
Enabled (Włączony) | Chroni żądanej zawartości przy użyciu uwierzytelniania opartego na tokenie. Tylko żądania od klientów, podaj prawidłowy token, które spełniają jej wymagań dotyczących będą honorowane. Transakcje FTP są wykluczone z uwierzytelniania opartego na tokenie.
Disabled (Wyłączony)| Przywraca domyślne zachowanie. Domyślnym zachowaniem jest umożliwienie konfiguracji uwierzytelniania opartego na tokenie, aby ustalić, czy żądanie zostanie zabezpieczone.

#### <a name="compatibility"></a>Zgodność
Nie należy używać tokenu uwierzytelniania z warunkiem zawsze dopasowania. 

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth-denial-code"></a>Kod odmowa tokenu uwierzytelniania
**Cel:** Określa typ odpowiedzi, który zostanie zwrócony użytkownikowi, gdy żądanie zostanie odrzucone z powodu uwierzytelniania opartego na tokenie.

Kody odpowiedzi dostępne są wymienione w poniższej tabeli.

Kod odpowiedzi|Nazwa odpowiedzi|Opis
-------------|-------------|--------
301|Trwale przeniesiona|Ten kod stanu przekierowania nieautoryzowanym użytkownikom na adres URL określony w nagłówku lokalizacji.
302|Znaleziono|Ten kod stanu przekierowania nieautoryzowanym użytkownikom na adres URL określony w nagłówku lokalizacji. Ten kod stanu jest branży standardową metodą wykonania przekierowania.
307|Przekierowanie tymczasowe|Ten kod stanu przekierowania nieautoryzowanym użytkownikom na adres URL określony w nagłówku lokalizacji.
401|Brak autoryzacji|Łączenie z nagłówka WWW-Authenticate odpowiedzi ten kod stanu umożliwia Monituj użytkownika do uwierzytelniania.
403|Zabroniony|Ten komunikat jest standardowe 403 Zabroniony komunikat o stanie nieautoryzowany użytkownik zostanie wyświetlony podczas próby dostępu do chronionej zawartości.
404|Nie znaleziono pliku|Ten kod stanu wskazuje, że klient HTTP był w stanie komunikować się z serwerem, ale nie można odnaleźć żądanej zawartości.

#### <a name="compatibility"></a>Zgodność
Nie używaj kodu odmowa tokenu uwierzytelniania z warunkiem zawsze dopasowania. Zamiast tego należy użyć **niestandardowe odmowa obsługi** sekcji **Token uwierzytelniania** strony **Zarządzaj** portalu. Aby uzyskać więcej informacji, zobacz [zasobów Zabezpieczanie usługi Azure CDN z tokenu uwierzytelniania](cdn-token-auth.md).

#### <a name="url-redirection"></a>Adres URL przekierowania

Ta funkcja obsługuje adres URL przekierowania do adresu URL zdefiniowane przez użytkownika, gdy jest on skonfigurowany do zwrócenia 3xx kod stanu. Ten adres URL zdefiniowany przez użytkownika, można określić, wykonując następujące czynności:

1. Wybierz kod odpowiedzi 3xx dla funkcji kodu odmowa tokenu uwierzytelniania.
2. Wybierz "Lokalizacja" z opcją opcjonalna nazwa nagłówka.
3. Ustaw opcję opcjonalna wartość nagłówka do żądanego adresu URL.

Jeśli adres URL nie jest zdefiniowany dla kodu stanu 3xx, strony standardowe odpowiedzi dla kodu stanu 3xx będzie zwrócił dla użytkownika.

Adres URL przekierowania dotyczy tylko 3xx kody odpowiedzi.

Opcja opcjonalna wartość nagłówka obsługuje znaki alfanumeryczne, znaki cudzysłowu i spacje.

#### <a name="authentication"></a>Authentication

Ta funkcja obsługuje możliwość dołączyć nagłówka WWW-Authenticate wysyłanej do nieautoryzowanego żądania dla zawartości chronionej przez uwierzytelniania opartego na tokenie. Jeśli nagłówka WWW-Authenticate została ustawiona na "basic" w konfiguracji, nieautoryzowany użytkownik zostanie monit o poświadczenia konta.

Powyższej konfiguracji można osiągnąć, wykonując następujące czynności:

1. Wybierz "401" jako kod odpowiedzi dla funkcji kodu odmowa tokenu uwierzytelniania.
2. Wybierz "WWW-Authenticate" z opcją opcjonalna nazwa nagłówka.
3. Ustaw opcję opcjonalna wartość nagłówka "podstawowy".

Nagłówek WWW-Authenticate dotyczy tylko kodów odpowiedzi 401.

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth-ignore-url-case"></a>Token uwierzytelniania Ignoruj wielkość liter adresu URL
**Cel:** Określa, czy wprowadzone przez uwierzytelniania opartego na tokenie porównania adres URL jest rozróżniana wielkość liter.

Parametry wpływ tej funkcji są:

- ec_url_allow
- ec_ref_allow
- ec_ref_deny

Prawidłowe wartości to:

Wartość|Wynik
---|----
Enabled (Włączony)|Powoduje, że POP ignorowanie wielkości liter podczas porównywania adresów URL dla uwierzytelniania opartego na tokenie parametrów.
Disabled (Wyłączony)|Przywraca domyślne zachowanie. Domyślnym zachowaniem jest adres URL porównania dla tokenu uwierzytelniania będzie uwzględniana wielkość liter.

**Domyślne zachowanie:** wyłączone.

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth-parameter"></a>Parametr tokenu uwierzytelniania
**Cel:** Określa, czy parametr ciągu zapytania uwierzytelniania opartego na tokenie powinny zostać zmienione.

Informacje o kluczu:

- Opcja wartość Określa nazwę parametru ciągu zapytania, za pomocą których można określić token.
- Nie można ustawić opcji wartość "ec_token."
- Upewnij się, że z nazwą zdefiniowaną w opcji wartość zawiera tylko prawidłowe znaki adresu URL.

Wartość|Wynik
----|----
Enabled (Włączony)|Opcja wartość Określa nazwę parametru ciągu zapytania, za pomocą którego można zdefiniować tokenów.
Disabled (Wyłączony)|Tokenu można określić jako parametr ciągu zapytania niezdefiniowana w adresie URL żądania.

**Domyślne zachowanie:** wyłączone. Tokenu można określić jako parametr ciągu zapytania niezdefiniowana w adresie URL żądania.

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="url-redirect"></a>Adres URL przekierowania
**Cel:** przekierowuje żądania za pośrednictwem nagłówek lokalizacji.

Konfiguracja ta funkcja wymaga ustawienia następujących opcji:

Opcja|Opis
-|-
Kod|Wybierz kod odpowiedzi, który zostanie zwrócony do zleceniodawcy.
Wzorzec & źródła| Te ustawienia definiują wzorzec identyfikatora URI żądania, który identyfikuje typ żądania, które mogą zostać przekierowane. Nastąpi przekierowanie tylko żądania, którego adres URL spełnia oba następujące kryteria: <br/> <br/> **Źródło (lub punktu dostępu do zawartości):** wybierz ścieżkę względną, którą identyfikuje serwer pochodzenia. Ta ścieżka jest _/XXXX/_ sekcji i nazwa punktu końcowego. <br/> **Źródło (wzorzec):** wzorca, który identyfikuje żądania za pomocą ścieżki względnej musi być zdefiniowany. Ten wzorzec wyrażenia regularnego musi definiować ścieżki, która rozpoczyna się bezpośrednio po poprzednio wybranego dostępu do zawartości punktu (zobacz powyżej). <br/> -Upewnij się, że żądanie identyfikatora URI kryteria (to znaczy źródła & wzorzec) wcześniej zdefiniowane nie koliduje to z warunkom dopasowania zdefiniowane dla tej funkcji. <br/> -Określ wzorzec; Jeśli wartość pusta jest używany jako wzorzec, wszystkie ciągi są dopasowywane.
Element docelowy| Zdefiniuj adres URL, do którego zostanie przekierowany żądań powyżej. <br/> Dynamicznie utworzyć przy użyciu tego adresu URL: <br/> -Wzorzec wyrażenia regularnego <br/>-Zmienne HTTP <br/> Zastąp wartości przechwytywane we wzorcu źródła do wzorca docelowego przy użyciu $_n_ gdzie _n_ identyfikuje wartość według kolejności, w którym została przechwycona. Na przykład $1 reprezentuje pierwszą wartość przechwycone we wzorcu źródła, podczas gdy druga wartość reprezentuje $2. <br/> 
Zdecydowanie zaleca się używania bezwzględnego adresu URL. Użycie względny adres URL może przekierować CDN adresy URL do nieprawidłowej ścieżki.

**Przykładowy scenariusz**

W tym przykładzie pokazano, jak przekierować krawędzi URL CNAME, który jest rozpoznawany jako ten podstawowy adres URL usługi CDN: http://marketing.azureedge.net/brochures

Kwalifikowanie żądania nastąpi przekierowanie do tej krawędzi podstawowy adres URL CNAME: http://cdn.mydomain.com/resources

Ten adres URL przekierowania można osiągnąć za pomocą następującej konfiguracji: ![](./media/cdn-rules-engine-reference/cdn-rules-engine-redirect.png)

**Kwestie kluczowe:**

- Żądanie definiuje funkcję adres URL przekierowania adresów URL, które zostanie przekierowany. W rezultacie dopasowanie dodatkowe warunki nie są wymagane. Mimo że warunek dopasowania został zdefiniowany jako "Always", zostanie przekierowany tylko żądania, które wskazują folder "broszury" na "marketing" pochodzenia klienta. 
- Wszystkie żądania zgodne, zostanie przekierowany do granicy, zdefiniowanych w opcji docelowy adres URL CNAME. 
    - Przykładowy scenariusz #1: 
        - Przykładowe żądanie (adres URL usługi CDN): http://marketing.azureedge.net/brochures/widgets.pdf 
        - Adres URL żądania (po przekierowania): http://cdn.mydomain.com/resources/widgets.pdf  
    - Przykładowy scenariusz #2: 
        - Przykładowe żądanie (krawędzi CNAME adres URL): http://marketing.mydomain.com/brochures/widgets.pdf 
        - Adres URL żądania (po przekierowania): http://cdn.mydomain.com/resources/widgets.pdf przykładowy scenariusz
    - Przykładowy scenariusz #3: 
        - Przykładowe żądanie (krawędzi CNAME adres URL): http://brochures.mydomain.com/campaignA/final/productC.ppt 
        - Adres URL żądania (po przekierowania): http://cdn.mydomain.com/resources/campaignA/final/productC.ppt  
- Zmienna schemat żądania (% {schemat}) jest wykorzystywana w opcji docelowego, który zapewnia, że schemat żądania nie jest zmieniany po przekierowaniu.
- Segmenty adresu URL, które są przechwytywane żądania są dołączane do nowego adresu URL za pośrednictwem "$1."

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="url-rewrite"></a>Ponowne zapisywanie adresów URL
**Cel:** ponownie zapisuje adresu URL żądania.

Informacje o kluczu:

- Konfiguracja ta funkcja wymaga ustawienia następujących opcji:

Opcja|Opis
-|-
 Wzorzec & źródła | Te ustawienia definiują wzorzec identyfikatora URI żądania, który identyfikuje typ żądania, które mogą być napisany od nowa. Zostanie ponownego napisania tylko żądania, którego adres URL spełnia oba następujące kryteria: <br/>     - **Źródło (lub punktu dostępu do zawartości):** wybierz ścieżkę względną, którą identyfikuje serwer pochodzenia. Ta ścieżka jest _/XXXX/_ sekcji i nazwa punktu końcowego. <br/> - **Źródło (wzorzec):** wzorca, który identyfikuje żądania za pomocą ścieżki względnej musi być zdefiniowany. Ten wzorzec wyrażenia regularnego musi definiować ścieżki, która rozpoczyna się bezpośrednio po poprzednio wybranego dostępu do zawartości punktu (zobacz powyżej). <br/> Sprawdź, czy żądanie identyfikatora URI kryteria (to znaczy źródła & wzorzec) wcześniej zdefiniowane nie koliduje to z jednego z warunków dopasowania zdefiniowane dla tej funkcji. Określ wzorzec; Jeśli wartość pusta jest używany jako wzorzec, wszystkie ciągi są dopasowywane. 
 Element docelowy  |Określ względny adres URL, do którego powyżej żądania będą ulegną przez: <br/>    1. Wybieranie punktu dostępu do zawartości, który identyfikuje serwer pochodzenia. <br/>    2. Definiowanie za pomocą ścieżki względnej: <br/>        -Wzorzec wyrażenia regularnego <br/>        -Zmienne HTTP <br/> <br/> Zastąp wartości przechwytywane we wzorcu źródła do wzorca docelowego przy użyciu $_n_ gdzie _n_ identyfikuje wartość według kolejności, w którym została przechwycona. Na przykład $1 reprezentuje pierwszą wartość przechwycone we wzorcu źródła, podczas gdy druga wartość reprezentuje $2. 
 Ta funkcja umożliwia POP zmodyfikować adres URL bez wykonywania tradycyjnych przekierowania. Oznacza to osoby żądającej odbiera ten sam kod odpowiedzi tak, jakby zażąda ponownie zapisane adresu URL.

**Przykładowy scenariusz 1**

W tym przykładzie pokazano, jak przekierować krawędzi URL CNAME, który jest rozpoznawany jako ten podstawowy adres URL usługi CDN: http://marketing.azureedge.net/brochures/

Kwalifikowanie żądania nastąpi przekierowanie do tej krawędzi podstawowy adres URL CNAME: http://MyOrigin.azureedge.net/resources/

Ten adres URL przekierowania można osiągnąć za pomocą następującej konfiguracji: ![](./media/cdn-rules-engine-reference/cdn-rules-engine-rewrite.png)

**Przykładowy scenariusz 2**

W tym przykładzie pokazano, jak przekierowywanie adresu URL CNAME z wielkimi literami na małe litery, za pomocą wyrażeń regularnych krawędzi.

Ten adres URL przekierowania można osiągnąć za pomocą następującej konfiguracji: ![](./media/cdn-rules-engine-reference/cdn-rules-engine-to-lowercase.png)


**Kwestie kluczowe:**

- Funkcja ponowne zapisywanie adresów URL definiuje żądania adresów URL, które będą napisany od nowa. W rezultacie dopasowanie dodatkowe warunki nie są wymagane. Mimo że warunek dopasowania został zdefiniowany jako "Always", tylko żądania, które wskazują folder "broszury" na "marketing" pochodzenia klienta zostanie napisany od nowa.

- Segmenty adresu URL, które są przechwytywane żądania są dołączane do nowego adresu URL za pośrednictwem "$1."

#### <a name="compatibility"></a>Zgodność
Ta funkcja obejmuje spełniających kryteria, które muszą zostać spełnione, aby można było zastosować na żądanie. Aby uniknąć konfigurowania kryteriów powodujących konflikt, ta funkcja jest niezgodny z następujące warunki dopasowania:

- JAKO liczba
- Źródła usługi CDN
- Adres IP klienta
- Pochodzenie klienta
- Schemat żądania
- Adres URL ścieżki katalogu
- Rozszerzenie ścieżki adresu URL
- Nazwa pliku ścieżki adresu URL
- Literał ścieżki adresu URL
- Wyrażenie regularne ścieżki adresu URL
- Symbol wieloznaczny ścieżki adresu URL
- Adres URL zapytania literału
- Adres URL parametru zapytania
- Adres URL zapytania Regex
- Adres URL zapytania z symboli wieloznacznych

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

---
### <a name="user-variable"></a>Zmienna użytkownika
**Cel:** tylko do użytku wewnętrznego.

[Powrót do początku](#azure-cdn-rules-engine-features)

</br>

## <a name="next-steps"></a>Następne kroki
* [Dokumentacja aparatu reguł](cdn-rules-engine-reference.md)
* [Wyrażenia warunkowe aparatu reguł](cdn-rules-engine-reference-conditional-expressions.md)
* [Warunki dopasowań aparatu reguł](cdn-rules-engine-reference-match-conditions.md)
* [Zastąpienie zachowania HTTP przy użyciu aparatu reguł](cdn-rules-engine.md)
* [Omówienie usługi Azure CDN](cdn-overview.md)
