---
title: Nagłówki HTTP X-EC-Debug dla aparatu reguł usługi Azure CDN | Dokumenty firmy Microsoft
description: Nagłówek żądania pamięci podręcznej debugowania X-EC-Debug zawiera dodatkowe informacje na temat zasad pamięci podręcznej, które są stosowane do żądanego zasobu. Te nagłówki są specyficzne dla Verizon.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2018
ms.author: magattus
ms.openlocfilehash: dec753d7c891d226aa2e6d3efa993d8d24adfbaa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593840"
---
# <a name="x-ec-debug-http-headers-for-azure-cdn-rules-engine"></a>Nagłówki HTTP X-EC-Debug dla aparatu reguł usługi Azure CDN
Nagłówek `X-EC-Debug`żądania pamięci podręcznej debugowania zawiera dodatkowe informacje o zasadach pamięci podręcznej, które są stosowane do żądanego zasobu. Te nagłówki są specyficzne dla **usługi Azure CDN Premium z** produktów Verizon.

## <a name="usage"></a>Sposób użycia
Odpowiedź wysyłana z serwerów POP `X-EC-Debug` do użytkownika zawiera nagłówek tylko wtedy, gdy spełnione są następujące warunki:

- [Funkcja Nagłówków odpowiedzi debugowania pamięci podręcznej](cdn-verizon-premium-rules-engine-reference-features.md#debug-cache-response-headers) została włączona w silniku reguł dla określonego żądania.
- Określone żądanie definiuje zestaw nagłówków odpowiedzi pamięci podręcznej debugowania, które zostaną uwzględnione w odpowiedzi.

## <a name="requesting-debug-cache-information"></a>Żądanie informacji o pamięci podręcznej debugowania
Użyj następujących dyrektyw w określonym żądaniu, aby zdefiniować informacje o pamięci podręcznej debugowania, które zostaną uwzględnione w odpowiedzi:

Nagłówek żądania | Opis |
---------------|-------------|
X-EC-Debug: x-ec-cache | [Kod stanu pamięci podręcznej](#cache-status-code-information)
X-EC-Debug: x-ec-cache-remote | [Kod stanu pamięci podręcznej](#cache-status-code-information)
X-EC-Debug: x-ec-check-cacheable | [Buforowalne](#cacheable-response-header)
X-EC-Debug: x-ec-cache-key | [Klucz pamięci podręcznej](#cache-key-response-header)
X-EC-Debug: x-ec-cache-state | [Stan pamięci podręcznej](#cache-state-response-header)

### <a name="syntax"></a>Składnia

Nagłówki odpowiedzi pamięci podręcznej debugowania mogą być wymagane przez dołączenie następującego nagłówka i określonych dyrektyw w żądaniu:

`X-EC-Debug: Directive1,Directive2,DirectiveN`

### <a name="sample-x-ec-debug-header"></a>Przykładowy nagłówek X-EC-Debug

`X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state`

## <a name="cache-status-code-information"></a>Informacje o kodzie stanu pamięci podręcznej
Nagłówek odpowiedzi X-EC-Debug może identyfikować serwer i sposób obsługi odpowiedzi za pośrednictwem następujących dyrektyw:

Nagłówek | Opis
-------|------------
X-EC-Debug: x-ec-cache | Ten nagłówek jest zgłaszany za każdym razem, gdy zawartość jest kierowana za pośrednictwem sieci CDN. Identyfikuje serwer POP, który spełnił żądanie.
X-EC-Debug: x-ec-cache-remote | Ten nagłówek jest zgłaszany tylko wtedy, gdy żądana zawartość została buforowana na serwerze osłony pochodzenia lub na serwerze bramy ADN.

### <a name="response-header-format"></a>Format nagłówka odpowiedzi

Nagłówek X-EC-Debug raportuje informacje o stanie pamięci podręcznej w następującym formacie:

- `X-EC-Debug: x-ec-cache: <StatusCode from Platform (POP/ID)>`

- `X-EC-Debug: x-ec-cache-remote: <StatusCode from Platform (POP/ID)>`

Terminy użyte w powyższej składni nagłówka odpowiedzi są zdefiniowane w następujący sposób:
- StatusCode: Wskazuje, jak żądana zawartość została obsługiwana przez sieć CDN, która jest reprezentowana za pośrednictwem kodu stanu pamięci podręcznej.
    
    Kod stanu TCP_DENIED może być zgłaszany zamiast NONE, gdy nieautoryzowane żądanie zostanie odrzucone z powodu uwierzytelniania opartego na tokenie. Jednak kod stanu NONE będzie nadal używany podczas wyświetlania raportów o stanie pamięci podręcznej lub nieprzetworzonych danych dziennika.

- Platforma: wskazuje platformę, na której zażądano zawartości. Dla tego pola obowiązują następujące kody:

    Code  | Platforma
    ------| --------
    EcAcc (europejski wypadek 201 | Duży protokół HTTP
    Ecs   | HTTP Mały
    Ecd   | Sieć dostarczania aplikacji (ADN)

- POP: Wskazuje [pop,](cdn-pop-abbreviations.md) który obsługiwał żądanie. 

### <a name="sample-response-headers"></a>Przykładowe nagłówki odpowiedzi

Następujące przykładowe nagłówki zawierają informacje o kodzie stanu pamięci podręcznej dla żądania:

- `X-EC-Debug: x-ec-cache: TCP_HIT from ECD (lga/0FE8)`

- `X-EC-Debug: x-ec-cache-remote: TCP_HIT from ECD (dca/EF00)`

## <a name="cacheable-response-header"></a>Nagłówek odpowiedzi z owujemcym w pamięci podręcznej
Nagłówek `X-EC-Debug: x-ec-check-cacheable` odpowiedzi wskazuje, czy żądana zawartość mogła być buforowana.

Ten nagłówek odpowiedzi nie wskazuje, czy buforowanie miało miejsce. Wskazuje raczej, czy wniosek kwalifikował się do buforowania.

### <a name="response-header-format"></a>Format nagłówka odpowiedzi

Nagłówek `X-EC-Debug` odpowiedzi informujący o tym, czy żądanie mogło zostać zapisane w pamięci podręcznej, jest w następującym formacie:

`X-EC-Debug: x-ec-check-cacheable: <cacheable status>`

Termin używany w powyższej składni nagłówka odpowiedzi jest zdefiniowany w następujący sposób:

Wartość  | Opis
-------| --------
TAK    | Wskazuje, że żądana zawartość kwalifikowała się do buforowania.
NO     | Wskazuje, że żądana zawartość nie kwalifikuje się do buforowania. Ten status może być spowodowany jedną z następujących przyczyn: <br /> - Konfiguracja specyficzna dla klienta: Konfiguracja specyficzna dla Twojego konta może uniemożliwić serwerom pop buforowanie zasobu. Na przykład Aparat reguł może uniemożliwić buforowanie zasobu, włączając funkcję Pomij pamięć podręczną dla kwalifikujących się żądań.<br /> - Nagłówki odpowiedzi pamięci podręcznej: Żądane nagłówki Cache-Control i Expires może uniemożliwić serwerom POP buforowanie go.
UNKNOWN | Wskazuje, że serwery nie były w stanie ocenić, czy żądany zasób był buforowany. Ten stan zazwyczaj występuje, gdy żądanie jest odrzucane z powodu uwierzytelniania opartego na tokenie.

### <a name="sample-response-header"></a>Przykładowy nagłówek odpowiedzi

Poniższy przykładowy nagłówek odpowiedzi wskazuje, czy żądana zawartość mogła zostać buforowana:

`X-EC-Debug: x-ec-check-cacheable: YES`

## <a name="cache-key-response-header"></a>Nagłówek odpowiedzi klucza pamięci podręcznej
Nagłówek `X-EC-Debug: x-ec-cache-key` odpowiedzi wskazuje fizyczny klucz pamięci podręcznej skojarzony z żądaną zawartością. Fizyczny klucz pamięci podręcznej składa się ze ścieżki identyfikującej zasób na potrzeby buforowania. Innymi słowy serwery będą sprawdzać, czy w pamięci podręcznej wersji zasobu zgodnie z jego ścieżką, zgodnie z definicją przez jego klucz pamięci podręcznej.

Ten fizyczny klucz pamięci podręcznej rozpoczyna się od podwójnego ukośnika do przodu (//), po którym następuje protokół używany do żądania zawartości (HTTP lub HTTPS). Po tym protokole następuje ścieżka względna do żądanego zasobu, która rozpoczyna się od punktu dostępu do zawartości (na przykład _/000001/_).

Domyślnie platformy HTTP są skonfigurowane do *używania standardowej pamięci podręcznej*, co oznacza, że ciągi zapytań są ignorowane przez mechanizm buforowania. Ten typ konfiguracji uniemożliwia klucz pamięci podręcznej z dołączania danych ciągu zapytania.

Jeśli ciąg zapytania jest rejestrowany w kluczu pamięci podręcznej, jest konwertowany na jego odpowiednik skrótu, a następnie&lt;wstawiany między nazwą żądanego zasobu a jego rozszerzeniem pliku (na przykład wartość&gt;skrótu zasobu .html).

### <a name="response-header-format"></a>Format nagłówka odpowiedzi

Nagłówek `X-EC-Debug` odpowiedzi raportuje informacje o kluczu pamięci podręcznej w następującym formacie:

`X-EC-Debug: x-ec-cache-key: CacheKey`

### <a name="sample-response-header"></a>Przykładowy nagłówek odpowiedzi

Poniższy przykładowy nagłówek odpowiedzi wskazuje fizyczny klucz pamięci podręcznej dla żądanej zawartości:

`X-EC-Debug: x-ec-cache-key: //http/800001/origin/images/foo.jpg`

## <a name="cache-state-response-header"></a>Nagłówek odpowiedzi stanu pamięci podręcznej
Nagłówek `X-EC-Debug: x-ec-cache-state` odpowiedzi wskazuje stan pamięci podręcznej żądanej zawartości w czasie, gdy została zażądana.

### <a name="response-header-format"></a>Format nagłówka odpowiedzi

Nagłówek `X-EC-Debug` odpowiedzi raportuje informacje o stanie pamięci podręcznej w następującym formacie:

`X-EC-Debug: x-ec-cache-state: max-age=MASeconds (MATimePeriod); cache-ts=UnixTime (ddd, dd MMM yyyy HH:mm:ss GMT); cache-age=CASeconds (CATimePeriod); remaining-ttl=RTSeconds (RTTimePeriod); expires-delta=ExpiresSeconds`

Terminy użyte w powyższej składni nagłówka odpowiedzi są zdefiniowane w następujący sposób:

- MASeconds: Wskazuje maksymalny wiek (w sekundach) zdefiniowany przez żądane nagłówki cache-control.

- MATimePeriod: Konwertuje wartość maksymalnego wieku (czyli MASeconds) na przybliżony odpowiednik większej jednostki (na przykład dni). 

- UnixTime: wskazuje sygnaturę czasną pamięci podręcznej żądanej zawartości w czasie Unixa (znany również jako czas POSIX lub epoka Uniksa). Sygnatura czasowa pamięci podręcznej wskazuje datę/godzinę początkową, z której zostanie obliczony czas wygaśnięcia zasobu. 

    Jeśli serwer pochodzenia nie korzysta z zewnętrznego serwera buforowania HTTP lub jeśli ten serwer nie zwraca nagłówka Odpowiedzi wiekowej, sygnatura czasowa pamięci podręcznej będzie zawsze datą/godziną, kiedy zasób został pobrany lub ponownie zweryfikowany. W przeciwnym razie serwery POP użyją pola Wiek do obliczania czasu wygaśnięcia zasobu w następujący sposób: Pobieranie/Ponowne obliczanieDadate - Wiek.

- ddd, dd MMM yyyy HH:mm:ss GMT: Wskazuje sygnaturę czasowa pamięci podręcznej żądanej zawartości. Aby uzyskać więcej informacji, zobacz termin UnixTime powyżej.

- CASeconds: Wskazuje liczbę sekund, które upłynęły od sygnatury czasowej pamięci podręcznej.

- RTSeconds: Wskazuje liczbę pozostałych sekund, dla których zawartość w pamięci podręcznej zostanie uznana za świeżą. Ta wartość jest obliczana w następujący sposób: RTSeconds = max-age - wiek pamięci podręcznej.

- RTTimePeriod: Konwertuje pozostałą wartość TTL (czyli RTSeconds) na przybliżony odpowiednik większej jednostki (na przykład dni).

- ExpiresSecons: Wskazuje liczbę sekund pozostałych przed datą/godziną `Expires` określoną w nagłówku odpowiedzi. Jeśli `Expires` nagłówek odpowiedzi nie został uwzględniony w odpowiedzi, wartość tego terminu jest *żadna*.

### <a name="sample-response-header"></a>Przykładowy nagłówek odpowiedzi

Poniższy przykładowy nagłówek odpowiedzi wskazuje stan pamięci podręcznej żądanej zawartości w czasie, gdy była żądana:

```X-EC-Debug: x-ec-cache-state: max-age=604800 (7d); cache-ts=1341802519 (Mon, 09 Jul 2012 02:55:19 GMT); cache-age=0 (0s); remaining-ttl=604800 (7d); expires-delta=none```

