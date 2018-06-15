---
title: Nagłówki HTTP X-WE-Debug aparat reguł Azure CDN | Dokumenty Microsoft
description: Nagłówek żądania X-WE-Debug debugowania pamięci podręcznej zawiera dodatkowe informacje o zasadach pamięci podręcznej, która jest stosowana do żądanego środka trwałego. Nagłówki te są specyficzne dla Verizon.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2018
ms.author: v-deasim
ms.openlocfilehash: 3a99e322d81748c54585e7dd0eb06959bfeb9569
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31517436"
---
# <a name="x-ec-debug-http-headers-for-azure-cdn-rules-engine"></a>Nagłówki HTTP X-WE-Debug aparat reguł Azure CDN
Nagłówek żądania debugowania pamięci podręcznej, `X-EC-Debug`, informacje dodatkowe dotyczące zasady pamięci podręcznej, która jest stosowana do żądanego środka trwałego. Nagłówki te są specyficzne dla **Azure CDN Premium z Verizon** produktów.

## <a name="usage"></a>Sposób użycia
Odpowiedź wysłana z serwerów POP do użytkownika zawiera `X-EC-Debug` nagłówka tylko wtedy, gdy spełnione są następujące warunki:

- [Funkcji debugowania pamięci podręcznej nagłówków odpowiedzi](cdn-rules-engine-reference-features.md#debug-cache-response-headers) został włączony aparat reguł dla określonego żądania.
- Określone żądanie definiuje zestaw debugowania nagłówki odpowiedzi pamięci podręcznej, które zostaną uwzględnione w odpowiedzi.

## <a name="requesting-debug-cache-information"></a>Żądanie informacje debugowania pamięci podręcznej
Następujące dyrektywy w określone żądanie można używać do definiowania informacje debugowania pamięci podręcznej, które zostaną uwzględnione w odpowiedzi:

Nagłówek żądania | Opis |
---------------|-------------|
X-WE Debug: x WE cache | [Kod stanu pamięci podręcznej](#cache-status-code-information)
X-WE Debug: x WE-pamięci podręcznej remote | [Kod stanu pamięci podręcznej](#cache-status-code-information)
X-WE Debug: x WE wyboru buforowalne | [Buforowalnej](#cacheable-response-header)
X-WE Debug: x WE klucz pamięci podręcznej | [Klucz pamięci podręcznej](#cache-key-response-header)
X-WE Debug: x WE--stan buforu | [Stan pamięci podręcznej](#cache-state-response-header)

### <a name="syntax"></a>Składnia

Debugowanie nagłówki może wystąpić przy tym następujący nagłówek i dyrektywy określony w żądaniu odpowiedzi pamięci podręcznej:

`X-EC-Debug: Directive1,Directive2,DirectiveN`

### <a name="sample-x-ec-debug-header"></a>Próbki X-WE-Debug nagłówka

`X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state`

## <a name="cache-status-code-information"></a>Informacje o stanie kodu z pamięci podręcznej
X-WE-Debug nagłówka odpowiedzi można zidentyfikować serwer i sposób jej obsługi odpowiedzi przez następujące dyrektywy:

Nagłówek | Opis
-------|------------
X-WE Debug: x WE cache | Ten nagłówek jest raportowany w każdym przypadku, gdy zawartość jest przesyłana za pośrednictwem sieci CDN. Identyfikuje serwer POP, który spełnił żądanie.
X-WE Debug: x WE-pamięci podręcznej remote | Ten nagłówek jest zgłaszane tylko wtedy, gdy żądana zawartość była buforowana na serwer pochodzenia tarczy lub serwer bramy ADN.

### <a name="response-header-format"></a>Format nagłówka odpowiedzi

Nagłówek X-WE-Debug raportuje informacje kod stanu pamięci podręcznej w następującym formacie:

- `X-EC-Debug: x-ec-cache: <StatusCode from Platform (POP/ID)>`

- `X-EC-Debug: x-ec-cache-remote: <StatusCode from Platform (POP/ID)>`

Terminy używane w powyższej składni nagłówka odpowiedzi są zdefiniowane w następujący sposób:
- StatusCode: Wskazuje, jak żądanej zawartości został obsłużony przez CDN, która jest reprezentowana przez kod stanu pamięci podręcznej.
    
    Kod stanu TCP_DENIED może podać zamiast NONE, jeśli nieautoryzowany żądanie zostało odrzucone z powodu tokenów uwierzytelniania. Jednak kod stanu Brak będzie nadal ma być używany podczas wyświetlania raportów o stanie pamięci podręcznej lub nieprzetworzone dane dziennika.

- Platforma: Wskazuje platformy, na którego zażądano zawartości. Prawidłowa dla tego pola są następujące kody:

    Kod  | Platforma
    ------| --------
    ECAcc | Duże HTTP
    ECS   | Małych HTTP
    ECD   | Sieci dostarczania aplikacji (ADN)

- Punkt POP: Wskazuje [POP](cdn-pop-abbreviations.md) że obsłużone żądanie. 

### <a name="sample-response-headers"></a>Przykładowe nagłówki odpowiedzi

Następujące przykładowe nagłówki zawierają informacje kod stanu pamięci podręcznej dla żądania:

- `X-EC-Debug: x-ec-cache: TCP_HIT from ECD (lga/0FE8)`

- `X-EC-Debug: x-ec-cache-remote: TCP_HIT from ECD (dca/EF00)`

## <a name="cacheable-response-header"></a>Nagłówek odpowiedzi buforowalne
`X-EC-Debug: x-ec-check-cacheable` Nagłówka odpowiedzi wskazuje, czy żądana zawartość może być buforowane.

Ten nagłówek odpowiedzi nie wskazuje, czy buforowanie miała miejsce. Przeciwnie wskazuje, czy wniosek był uprawniony do buforowania.

### <a name="response-header-format"></a>Format nagłówka odpowiedzi

`X-EC-Debug` Zgłoszenie, czy wniosek może być buforowane nagłówka odpowiedzi znajduje się w następującym formacie:

`X-EC-Debug: x-ec-check-cacheable: <cacheable status>`

Termin używany w powyższej składni nagłówka odpowiedzi jest zdefiniowany w następujący sposób:

Wartość  | Opis
-------| --------
TAK    | Wskazuje, że żądana zawartość była uprawniona do buforowania.
NIE     | Wskazuje, że żądana zawartość nie kwalifikują się do buforowania. Ten stan może być spowodowane jedną z następujących przyczyn: <br /> — Konfiguracja specyficzne dla klienta: konfiguracji specyficzne dla Twojego konta może uniemożliwić buforowanie składnika aktywów pop serwerów. Na przykład aparat reguł można zapobiec składnika aktywów buforowana przez włączenie funkcji pomijania pamięci podręcznej dla kwalifikujących się wniosków.<br /> -Nagłówki odpowiedzi cache: Trwały żądanego nagłówka Cache-Control i Expires nagłówka można zapobiec serwerów POP buforowanie go.
NIEZNANE | Wskazuje, że serwery były w stanie ocenić, czy żądany zasób był buforowalne. Status ten zazwyczaj występuje, gdy żądanie zostanie odrzucone z powodu tokenów uwierzytelniania.

### <a name="sample-response-header"></a>Przykładowy nagłówek odpowiedzi

Następujący przykładowy nagłówek odpowiedzi wskazuje, czy żądana zawartość może być buforowane:

`X-EC-Debug: x-ec-check-cacheable: YES`

## <a name="cache-key-response-header"></a>Nagłówek odpowiedzi klucz pamięci podręcznej
`X-EC-Debug: x-ec-cache-key` Nagłówka odpowiedzi wskazuje fizycznej pamięci podręcznej klucza skojarzonego z żądanej zawartości. Fizyczny klucz pamięci podręcznej składa się ścieżki, który identyfikuje składnik aktywów do celów buforowania. Innymi słowy serwery będą sprawdzać dostępności buforowanej wersji zasobów zgodnie z jego ścieżki zgodnie z definicją w jej klucz pamięci podręcznej.

To fizyczne klucz pamięci podręcznej zaczyna się od podwójnego ukośnika (/ /) następuje protokół używany do żądania zawartości (HTTP lub HTTPS). Niniejszy Protokół jest po ścieżkę względną do żądanego środka trwałego, która rozpoczyna się od punktu dostępu do zawartości (na przykład _/000001/_).

Domyślnie, platform HTTP są skonfigurowane do używania *pamięci podręcznej standard*, co oznacza, że ciągi kwerendy są ignorowane przez mechanizm buforowania. Ten typ konfiguracji uniemożliwia tym dane ciągu kwerendy klucz pamięci podręcznej.

Jeśli ciąg kwerendy jest rejestrowane w kluczu pamięci podręcznej, ma konwertowane na jej odpowiednik mieszania i następnie dodaje między nazwa żądanego składnika aktywów a jego rozszerzenie pliku (na przykład trwałego&lt;wartość mieszania&gt;.html).

### <a name="response-header-format"></a>Format nagłówka odpowiedzi

`X-EC-Debug` Nagłówka odpowiedzi raporty fizyczny klucz pamięci podręcznej informacje w następującym formacie:

`X-EC-Debug: x-ec-cache-key: CacheKey`

### <a name="sample-response-header"></a>Przykładowy nagłówek odpowiedzi

Następujący przykładowy nagłówek odpowiedzi wskazuje fizyczny klucz pamięci podręcznej dla żądanej zawartości:

`X-EC-Debug: x-ec-cache-key: //http/800001/origin/images/foo.jpg`

## <a name="cache-state-response-header"></a>Nagłówek odpowiedzi stan pamięci podręcznej
`X-EC-Debug: x-ec-cache-state` Nagłówka odpowiedzi wskazuje stan pamięci podręcznej żądanej zawartości w czasie ich zażądano.

### <a name="response-header-format"></a>Format nagłówka odpowiedzi

`X-EC-Debug` Nagłówka odpowiedzi zgłasza informacje o stanie pamięci podręcznej w następującym formacie:

`X-EC-Debug: x-ec-cache-state: max-age=MASeconds (MATimePeriod); cache-ts=UnixTime (ddd, dd MMM yyyy HH:mm:ss GMT); cache-age=CASeconds (CATimePeriod); remaining-ttl=RTSeconds (RTTimePeriod); expires-delta=ExpiresSeconds`

Terminy używane w powyższej składni nagłówka odpowiedzi są zdefiniowane w następujący sposób:

- MASeconds: Wskazuje maksymalny wiek (w sekundach), zgodnie z definicją nagłówki kontroli pamięci podręcznej żądanej zawartości.

- MATimePeriod: Konwertuje wartość wieku max (czyli MASeconds) na przybliżony odpowiednik większe jednostki (na przykład, dni). 

- UnixTime: Wskazuje sygnatury czasowej żądanej zawartości pamięci podręcznej w czasie Unix (vel POSIX czasu lub epoch systemu Unix). Sygnatura czasowa pamięci podręcznej wskazuje początkowa data/godzina z którego oblicza się wartość TTL składnika aktywów. 

    Jeśli serwer pochodzenia nie jest używane buforowanie serwera lub jeśli ten serwer nie zwraca wieku nagłówka odpowiedzi HTTP firm, następnie sygnatury czasowej pamięci podręcznej zawsze będzie data/godzina kiedy źródło lub przywróconą ważność zasobu. W przeciwnym wypadku serwerów POP zostanie użyte pole wieku do obliczania czasu wygaśnięcia TTL danego składnika aktywów w następujący sposób: pobieranie/RevalidateDateTime - wieku.

- ddd, dd MMM rrrr hh: mm: GMT: wskazuje sygnatury czasowej żądanej zawartości pamięci podręcznej. Aby uzyskać więcej informacji zobacz temat powyższy termin UnixTime.

- CASeconds: Wskazuje liczbę sekund, które upłynęły od pamięci podręcznej sygnatury czasowej.

- RTSeconds: Wskazuje liczbę sekund pozostała, dla którego zawartość pamięci podręcznej uznaje się za świeże. Ta wartość jest obliczana w następujący sposób: RTSeconds = max wiek - wiek w pamięci podręcznej.

- RTTimePeriod: Konwertuje pozostałą wartość TTL (czyli RTSeconds) przybliżony odpowiednik większe jednostki (na przykład, dni).

- ExpiresSeconds: Wskazuje liczbę sekund pozostała przed Data/Godzina, określona w `Expires` nagłówka odpowiedzi. Jeśli `Expires` nagłówka odpowiedzi nie została uwzględniona w odpowiedzi, a następnie wartość termin ten jest *Brak*.

### <a name="sample-response-header"></a>Przykładowy nagłówek odpowiedzi

Następujący przykładowy nagłówek odpowiedzi wskazuje stan pamięci podręcznej żądanej zawartości w czasie, który został zgłoszony:

```X-EC-Debug: x-ec-cache-state: max-age=604800 (7d); cache-ts=1341802519 (Mon, 09 Jul 2012 02:55:19 GMT); cache-age=0 (0s); remaining-ttl=604800 (7d); expires-delta=none```

