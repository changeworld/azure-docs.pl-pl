---
title: Nagłówki HTTP X-WE-Debug dla aparatu reguł usługi Azure CDN | Dokumentacja firmy Microsoft
description: Nagłówek żądania X-WE-Debug debugowania pamięci podręcznej zawiera dodatkowe informacje na temat zasad pamięci podręcznej, które są stosowane do żądanego zasobu. Tych nagłówków są specyficzne dla firmy Verizon.
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
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593840"
---
# <a name="x-ec-debug-http-headers-for-azure-cdn-rules-engine"></a>Nagłówki HTTP X-WE-Debug dla usługi Azure CDN aparatu reguł
Nagłówek żądania debugowania w pamięci podręcznej, `X-EC-Debug`, zawiera dodatkowe informacje na temat zasad pamięci podręcznej, które są stosowane do żądanego zasobu. Tych nagłówków są specyficzne dla **Azure CDN Premium from Verizon** produktów.

## <a name="usage"></a>Użycie
Odpowiedź wysłana z serwerów POP do użytkownika zawiera `X-EC-Debug` nagłówka tylko wtedy, gdy są spełnione następujące warunki:

- [Funkcja Debuguj pamięć podręczna nagłówki odpowiedzi](cdn-verizon-premium-rules-engine-reference-features.md#debug-cache-response-headers) został włączony na aparat reguł dla określonego żądania.
- Określone żądanie definiuje zestaw nagłówków odpowiedzi pamięci podręcznej debugowania, które zostaną uwzględnione w odpowiedzi.

## <a name="requesting-debug-cache-information"></a>Żądanie pamięci podręcznej informacje o debugowaniu
Umożliwia następujące dyrektywy w określonym żądaniu zdefiniować informacje debugowania pamięci podręcznej, która zostanie umieszczona w odpowiedzi:

Nagłówek żądania | Opis |
---------------|-------------|
X-EC-Debug: x-ec-cache | [Kod stanu pamięci podręcznej](#cache-status-code-information)
X-EC-Debug: x-ec-cache-remote | [Kod stanu pamięci podręcznej](#cache-status-code-information)
X-EC-Debug: x-ec-check-cacheable | [Podlega buforowaniu](#cacheable-response-header)
X-EC-Debug: x-ec-cache-key | [Cache-key](#cache-key-response-header)
X-EC-Debug: x-ec-cache-state | [Stan pamięci podręcznej](#cache-state-response-header)

### <a name="syntax"></a>Składnia

Debugowanie odpowiedzi z pamięci podręcznej, który może zostać wyświetlony nagłówków, umieszczając następujący nagłówek i dyrektyw określony w żądaniu:

`X-EC-Debug: Directive1,Directive2,DirectiveN`

### <a name="sample-x-ec-debug-header"></a>Nagłówek X-WE-debugowanie próbki

`X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state`

## <a name="cache-status-code-information"></a>Informacje o kodzie stanu pamięci podręcznej
Nagłówek odpowiedzi X WE debugowania można określić serwer i sposób jej obsługi odpowiedzi przez następujące dyrektywy:

nagłówek | Opis
-------|------------
X-EC-Debug: x-ec-cache | Tego pliku nagłówkowego jest zgłaszany w każdym przypadku, gdy zawartość jest kierowany przez usługę CDN. Identyfikuje serwer protokołu POP, która zrealizowała żądanie.
X-EC-Debug: x-ec-cache-remote | Tego pliku nagłówkowego jest zgłaszany tylko wtedy, gdy buforowano żądanej zawartości na serwer pochodzenia tarczy lub serwer bramy w sieci ADN.

### <a name="response-header-format"></a>Format Nagłówek odpowiedzi

Nagłówek X-WE-Debug raportów informacji o kodzie stanu pamięci podręcznej w następującym formacie:

- `X-EC-Debug: x-ec-cache: <StatusCode from Platform (POP/ID)>`

- `X-EC-Debug: x-ec-cache-remote: <StatusCode from Platform (POP/ID)>`

Terminy używane w powyższej składni nagłówek odpowiedzi są zdefiniowane w następujący sposób:
- StatusCode: Wskazuje, jak żądanej zawartości został obsłużony przez usługę CDN, jest przedstawiana za pomocą kodu stanu pamięci podręcznej.
    
    Kod stanu TCP_DENIED może być zgłaszany zamiast NONE, jeśli nieautoryzowanego żądania zostanie odrzucone z powodu uwierzytelniania opartego na tokenach. Jednakże kod stanu NONE będą w dalszym ciągu można używać w przypadku wyświetlania raportów o stanie pamięci podręcznej lub nieprzetworzonych danych dzienników.

- Platforma: Wskazuje platformy, na którym żądano zawartości. Następujące kody są prawidłowe dla tego pola:

    Kod  | Platforma
    ------| --------
    ECAcc | Duże HTTP
    ECS   | Mała liczba godzin HTTP
    ECD   | Application Delivery Network (sieci ADN)

- POP: Wskazuje [POP](cdn-pop-abbreviations.md) , obsługi żądania. 

### <a name="sample-response-headers"></a>Próbki, nagłówki odpowiedzi

Następujące nagłówki przykładowe zawierają informacje o kodzie stanu pamięci podręcznej dla żądania:

- `X-EC-Debug: x-ec-cache: TCP_HIT from ECD (lga/0FE8)`

- `X-EC-Debug: x-ec-cache-remote: TCP_HIT from ECD (dca/EF00)`

## <a name="cacheable-response-header"></a>Nagłówek odpowiedzi podlega buforowaniu
`X-EC-Debug: x-ec-check-cacheable` Nagłówek odpowiedzi wskazuje, czy żądana zawartość może być buforowane.

Ten nagłówek odpowiedzi nie wskazuje, czy buforowanie miało miejsce. Przeciwnie oznacza to, czy żądanie zostało kwalifikuje się do buforowania.

### <a name="response-header-format"></a>Format Nagłówek odpowiedzi

`X-EC-Debug` Nagłówek odpowiedzi raportowania, czy żądanie może być buforowane znajduje się w następującym formacie:

`X-EC-Debug: x-ec-check-cacheable: <cacheable status>`

Termin używany w powyższej składni nagłówek odpowiedzi jest zdefiniowana w następujący sposób:

Value  | Opis
-------| --------
TAK    | Wskazuje, czy żądana zawartość została kwalifikuje się do buforowania.
NO     | Wskazuje, czy żądana zawartość została nie kwalifikuje się do buforowania. Ten stan może być spowodowane jedną z następujących powodów: <br /> Konfiguracja klienta specyficzne dla: Konfiguracja właściwych dla Twojego konta można zapobiec buforowanie zawartości pop serwerów. Na przykład aparat reguł uniemożliwia zasobu buforowana przez włączenie funkcji pomijania pamięci podręcznej do kwalifikowania żądania.<br /> -W pamięci podręcznej nagłówki odpowiedzi: Nagłówki Cache-Control i Expires żądanego elementu zawartości można zapobiec buforowanie ich serwerów POP.
NIEZNANY | Wskazuje, że serwery nie udało się oceny, czy żądany zasób został podlega buforowaniu. Zazwyczaj ten stan występuje, gdy żądanie zostanie odrzucone z powodu uwierzytelniania opartego na tokenach.

### <a name="sample-response-header"></a>Przykładowy nagłówek odpowiedzi

Poniższy przykładowy nagłówek odpowiedzi wskazuje, czy żądana zawartość może być buforowane:

`X-EC-Debug: x-ec-check-cacheable: YES`

## <a name="cache-key-response-header"></a>Nagłówek odpowiedzi klucz pamięci podręcznej
`X-EC-Debug: x-ec-cache-key` Nagłówek odpowiedzi wskazuje fizycznej pamięci podręcznej — klucz skojarzony z żądanej zawartości. Fizyczne klucz pamięci podręcznej składa się z ścieżką, która identyfikuje zasób usługi na potrzeby buforowania. Innymi słowy serwery będzie sprawdzał dostępność pamięci podręcznej wersji zasobu zgodnie z jego ścieżki, zgodnie z definicją według jego klucza pamięci podręcznej.

To fizyczne klucz pamięci podręcznej rozpoczyna się od podwójnego ukośnika (/ /) i protokół używany do żądania zawartości (HTTP lub HTTPS). Ten protokół jest następuje ścieżkę względną do żądanego elementu zawartości, która rozpoczyna się od punktu dostępu do zawartości (na przykład _/000001/_ ).

Domyślnie platformami HTTP są skonfigurowane do używania *pamięci podręcznej standardu*, co oznacza, że ciągi zapytania są ignorowane przez mechanizm buforowania. Ten typ konfiguracji uniemożliwia klucz pamięci podręcznej w tym dane ciągu zapytania.

Jeśli ciąg zapytania jest zarejestrowany w klucz pamięci podręcznej, ma konwertowana na odpowiadającą jej wyznaczania wartości skrótu i następnie wstawione między nazwa żądanego elementu zawartości i jego rozszerzenie pliku (na przykład zasób&lt;wartość skrótu&gt;.html).

### <a name="response-header-format"></a>Format Nagłówek odpowiedzi

`X-EC-Debug` Nagłówek odpowiedzi raporty fizycznych informacje klucz pamięci podręcznej w następującym formacie:

`X-EC-Debug: x-ec-cache-key: CacheKey`

### <a name="sample-response-header"></a>Przykładowy nagłówek odpowiedzi

Poniższy przykładowy nagłówek odpowiedzi wskazuje fizycznych klucz pamięci podręcznej dla żądanej zawartości:

`X-EC-Debug: x-ec-cache-key: //http/800001/origin/images/foo.jpg`

## <a name="cache-state-response-header"></a>Nagłówek odpowiedzi stanu pamięci podręcznej
`X-EC-Debug: x-ec-cache-state` Nagłówek odpowiedzi wskazuje stan żądaną zawartość w pamięci podręcznej w momencie jej żądanie zostało.

### <a name="response-header-format"></a>Format Nagłówek odpowiedzi

`X-EC-Debug` Nagłówek odpowiedzi raportuje informacje o stanie pamięci podręcznej w następującym formacie:

`X-EC-Debug: x-ec-cache-state: max-age=MASeconds (MATimePeriod); cache-ts=UnixTime (ddd, dd MMM yyyy HH:mm:ss GMT); cache-age=CASeconds (CATimePeriod); remaining-ttl=RTSeconds (RTTimePeriod); expires-delta=ExpiresSeconds`

Terminy używane w powyższej składni nagłówek odpowiedzi są zdefiniowane w następujący sposób:

- MASeconds: Wskazuje maksymalny wiek (w sekundach), zgodnie z definicją nagłówki Cache-Control żądanej zawartości.

- MATimePeriod: Konwertuje wartość max-age (czyli MASeconds) stanowiących wielokrotność większej jednostki (na przykład w dniach). 

- UnixTime: Określa znacznik czasu pamięci podręcznej żądanej zawartości w programie time systemu Unix (znany także jako POSIX czasu lub epoki Unix). Znacznik czasu: pamięć podręczna wskazuje, począwszy od daty/godziny z której będzie obliczana czas wygaśnięcia elementu zawartości. 

    Jeśli serwer pochodzenia nie używa HTTP innych firm, buforowanie serwera lub jeśli ten serwer nie zwraca nagłówek odpowiedzi wiek, następnie znacznik czasu pamięci podręcznej jest zawsze daty/godziny po pobierane lub ponownie sprawdzić poprawności nazwy zasobu. W przeciwnym razie serwerów POP użyje pola Wiek do obliczania czasu wygaśnięcia zasobu w następujący sposób: Pobieranie/RevalidateDateTime - wieku.

- ddd, dd MMM yyyy hh: mm: GMT: Wskazuje, znacznik czasu pamięci podręcznej żądanej zawartości. Aby uzyskać więcej informacji zobacz termin UnixTime powyżej.

- CASeconds: Określa liczbę sekund, które upłynęły od znacznika czasu pamięci podręcznej.

- RTSeconds: Wskazuje liczbę Pozostało sekund, dla którego zawartości w pamięci podręcznej będą uznawane za od nowa. Ta wartość jest obliczana w następujący sposób: RTSeconds = max-age — wiek w pamięci podręcznej.

- RTTimePeriod: Konwertuje pozostała wartość czasu wygaśnięcia (czyli RTSeconds) stanowiących wielokrotność większej jednostki (na przykład w dniach).

- ExpiresSeconds: Wskazuje liczbę sekund pozostałych daty/godziny określone w `Expires` nagłówka odpowiedzi. Jeśli `Expires` nagłówek odpowiedzi nie została uwzględniona w odpowiedzi, a następnie wartość pojęcie to jest *Brak*.

### <a name="sample-response-header"></a>Przykładowy nagłówek odpowiedzi

Poniższy przykładowy nagłówek odpowiedzi wskazuje stan żądaną zawartość w pamięci podręcznej w czasie, która została zażądana:

```X-EC-Debug: x-ec-cache-state: max-age=604800 (7d); cache-ts=1341802519 (Mon, 09 Jul 2012 02:55:19 GMT); cache-age=0 (0s); remaining-ttl=604800 (7d); expires-delta=none```

