---
title: Jak działa buforowanie | Dokumentacja firmy Microsoft
description: Buforowanie jest procesem przechowywania danych lokalnie, tak aby przyszłych żądań dotyczących szybciej dostępne dane.
services: cdn
documentationcenter: ''
author: dksimpson
manager: ''
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2017
ms.author: v-deasim
ms.openlocfilehash: da43e122c3e7d5e852107d4e4cca237ce4824267
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="how-caching-works"></a>Jak działa buforowanie

Ten artykuł zawiera omówienie ogólne koncepcje buforowania i w jaki sposób [Azure sieci dostarczania zawartości (CDN)](cdn-overview.md) używa buforowanie, aby zwiększyć wydajność. Jeśli chcesz dowiedzieć się więcej o tym, jak dostosować zachowanie buforowania na punkt końcowy CDN, zobacz [kontroli usługi Azure CDN zachowanie buforowania z buforowaniem reguły](cdn-caching-rules.md) i [kontroli usługi Azure CDN buforowanie z ciągami zapytań](cdn-query-string.md).

## <a name="introduction-to-caching"></a>Wprowadzenie do buforowania

Buforowanie jest procesem przechowywania danych lokalnie, tak aby przyszłych żądań dotyczących szybciej dostępne dane. W najczęściej spotykanym typem buforowania, buforowanie przeglądarki sieci web, sieci web przeglądarce przechowuje kopie danych statycznych lokalnie na lokalnym dysku twardym. Korzystając z buforowania, przeglądarki sieci web można uniknąć wiele rund do serwera i zamiast tego dostęp do tych samych danych lokalnie, dzięki czemu można oszczędzić czas i zasoby. Buforowanie jest dobrze nadaje się do lokalnego zarządzania małej, statycznej dane, takie jak statyczne obrazów, plików CSS i JavaScript plików.

Podobnie buforowanie służy sieci dostarczania zawartości na serwerach krawędzi bliski użytkownika w celu uniknięcia żądań przesyłanych do źródła i zmniejsza opóźnienia użytkownika końcowego. W przeciwieństwie do buforu przeglądarki sieci web, która jest używana tylko dla jednego użytkownika, sieć CDN ma współużytkowanej pamięci podręcznej. W pamięci podręcznej z sieci CDN udostępnionego pliku żądanego przez jednego użytkownika są dostępne później przez innych użytkowników, które znacząco zmniejsza liczbę żądań do serwera pochodzenia.

Nie można buforować dynamicznych zasobów, które często się zmieniają lub są unikatowe dla poszczególnych użytkowników. Tych typów zasobów, jednak można korzystać z optymalizacji acceleration (DSA) dynamiczne witryny Azure sieci dostarczania zawartości dla poprawy wydajności.

Buforowanie może wystąpić na różnych poziomach między serwerem pochodzenia i użytkownika końcowego:

- Serwer sieci Web: używa współużytkowanej pamięci podręcznej (w przypadku wielu użytkowników).
- Sieci dostarczania zawartości: używa współużytkowanej pamięci podręcznej (w przypadku wielu użytkowników).
- Usługodawcy internetowego (ISP): używa współużytkowanej pamięci podręcznej (w przypadku wielu użytkowników).
- Przeglądarki sieci Web: używa prywatnego pamięci podręcznej (dla jednego użytkownika).

Każda usługa pamięć podręczna zwykle zarządza własną świeżości zasobów i sprawdza poprawność, gdy plik jest przestarzała. To zachowanie jest zdefiniowany w protokole HTTP, buforowanie specyfikacji, [RFC 7234](https://tools.ietf.org/html/rfc7234).

### <a name="resource-freshness"></a>Świeżości zasobów

Ponieważ buforowanych zasobów potencjalnie mogą być nieaktualne ani starych (w porównaniu do odpowiednich zasobów na serwerze źródłowym), jest ważna w przypadku dowolnego mechanizm buforowania do formantu po odświeżeniu zawartości. Aby zapisać zużycie czasu i przepustowości, zasobów pamięci podręcznej nie była porównywana do wersji na serwerze źródłowym za każdym razem, gdy jest on dostępny. Zamiast tego należy tak długo, jak zasobów pamięci podręcznej jest traktowany jako pierwszą, jego zakłada, że aktualna i jest wysyłany bezpośrednio do klienta. Buforowanych zasobów uważa się za świeże, gdy jego okres ważności jest mniejsza niż okres ważności lub okresu zdefiniowanego za pomocą ustawienia pamięci podręcznej. Na przykład podczas przeglądarką ponowne załadowanie strony sieci web, sprawdzi, czy wszystkie zasoby pamięci podręcznej na dysku twardym jest odświeżona i ładuje go. Jeśli zasób nie jest odświeżona (przestarzałe) aktualnej kopii są ładowane z serwera.

### <a name="validation"></a>Walidacja

Jeśli zasób jest uznawane za przestarzałe, serwerze źródłowym jest proszony o sprawdzanie poprawności, oznacza to, określić, czy dane w pamięci podręcznej nadal odpowiada elementom na serwerze źródłowym. Jeśli plik został zmodyfikowany na serwerze źródłowym, pamięci podręcznej aktualizuje swoją wersję zasobu. W przeciwnym razie, jeśli zasób jest nowa, dane są dostarczane bezpośrednio z pamięci podręcznej bez sprawdzania poprawności najpierw.

### <a name="cdn-caching"></a>Buforowanie CDN

Buforowanie jest integralną częścią CDN funkcjonowania przyspieszenia dostarczania i zmniejszyć obciążenie źródła dla statycznych zasobów, takich jak obrazy, czcionki i wideo. W sieci CDN buforowania, zasoby statyczne selektywnie są przechowywane w strategicznie rozmieszczonych serwerów, które są bardziej lokalnego do użytkownika i ma następujące zalety:

- Ponieważ większość ruchu w sieci web jest statyczny (na przykład, obrazy, czcionki i filmy wideo), buforowanie CDN zmniejsza opóźnienia sieci za pomocą przenoszenia zawartości bliżej użytkownikowi, co zmniejsza odległość przesyłania danych.

- Dzięki przeniesieniu pracy CDN, buforowanie można zmniejszyć obciążenie sieci oraz obciążenia na serwerze źródłowym. Dzięki temu zmniejsza koszt i zasoby wymagania dotyczące aplikacji, nawet w przypadku dużej liczby użytkowników.

Podobnie jak buforowanie jest zaimplementowana w przeglądarce sieci web można kontrolować jak buforowanie odbywa się w sieci CDN przez wysyłanie nagłówków pamięci podręcznej dyrektywy. Dyrektywa pamięci podręcznej nagłówki są nagłówków HTTP, które zwykle są dodawane przez serwer pochodzenia. Mimo że większość tych nagłówków pierwotnie zostały zaprojektowane do adresów pamięci podręcznej w przeglądarkach klientów, teraz również są używane przez wszystkie pośrednie pamięci podręcznych, takich jak CDN. 

Dwa nagłówki mogą być używane do definiowania świeżości pamięci podręcznej: `Cache-Control` i `Expires`. `Cache-Control` jest bardziej aktualny i ma pierwszeństwo przed `Expires`, jeśli istnieją. Istnieją również dwa typy nagłówków używany do sprawdzania poprawności (nazywane moduły weryfikacji): `ETag` i `Last-Modified`. `ETag` jest bardziej aktualny i ma pierwszeństwo przed `Last-Modified`, jeśli są określone oba.  

## <a name="cache-directive-headers"></a>Nagłówki pamięci podręcznej — dyrektywa

> [!IMPORTANT]
> Domyślnie usługi Azure CDN punktu końcowego, który jest zoptymalizowana pod kątem DSA ignoruje nagłówków pamięci podręcznej dyrektywy i pomija buforowania. Dla **Azure CDN from Verizon Standard** i **Azure CDN from Akamai Standard** profile, można dostosować sposób punktu końcowego usługi Azure CDN traktuje te nagłówki przy użyciu [reguły buforowania CDN](cdn-caching-rules.md)Aby włączyć buforowanie. Dla **Azure CDN from Verizon** tylko profile, użyj [aparatu reguł](cdn-rules-engine.md) Aby włączyć buforowanie.

Usługi Azure CDN obsługuje następujące dyrektywy pamięci podręcznej nagłówki HTTP, które definiują czas trwania pamięci podręcznej i współużytkowania pamięci podręcznej.

**Cache-Control:**
- Wprowadzone w protokołu HTTP 1.1, zapewniają większą kontrolę nad swoją zawartość sieci web wydawcy i adresu ograniczenia `Expires` nagłówka.
- Zastępuje `Expires` nagłówka, jeśli obie go i `Cache-Control` są zdefiniowane.
- Gdy jest używany w żądaniu HTTP `Cache-Control` jest ignorowana przez usługi Azure CDN domyślnie.
- **Usługi Azure CDN from Verizon** profile obsługuje wszystkie `Cache-Control` dyrektywy, gdy są używane w odpowiedzi HTTP.
- **Azure CDN from Akamai** profile obsługuje tylko następujące dyrektywy, gdy są używane w odpowiedzi HTTP; pozostałe są ignorowane:
   - `max-age`: Liczby sekund określonej pamięć podręczna może przechowywać zawartość. Na przykład `Cache-Control: max-age=5`. Ta dyrektywa określa maksymalną ilość czasu, w których zawartość została uznana za świeże.
   - `no-cache`: Zawartość pamięci podręcznej, ale sprawdzenie poprawności zawartości każdorazowym przed dostarczeniem go z pamięci podręcznej. Odpowiednikiem `Cache-Control: max-age=0`.
   - `no-store`: Nigdy nie pamięci podręcznej zawartości. Usuwanie zawartości, jeśli zostały wcześniej zapisane.

**Wygasa:**
- Nagłówek starszych wprowadzone w HTTP 1.0; obsługiwane w przypadku zapewnienia zgodności.
- Używa czas na podstawie daty wygaśnięcia z dokładnością do drugiego. 
- Podobnie jak `Cache-Control: max-age`.
- Używane podczas `Cache-Control` nie istnieje.

**Pragma:**
   - Nie honorowane przez usługi Azure CDN domyślnie.
   - Nagłówek starszych wprowadzone w HTTP 1.0; obsługiwane w przypadku zapewnienia zgodności.
   - Używane jako nagłówek żądania klientów o następujące dyrektywy: `no-cache`. Ta dyrektywa nakazuje serwerowi dostarczania nową wersję tego zasobu.
   - `Pragma: no-cache` jest odpowiednikiem `Cache-Control: no-cache`.

## <a name="validators"></a>Moduły weryfikacji

Gdy pamięci podręcznej jest przestarzała, modułów sprawdzania poprawności HTTP pamięci podręcznej są używane do porównania buforowanej wersji pliku z wersją na serwerze źródłowym. **Usługi Azure CDN from Verizon** obsługuje zarówno `ETag` i `Last-Modified` modułów sprawdzania poprawności, domyślnie podczas **Azure CDN from Akamai** obsługuje tylko `Last-Modified` domyślnie.

**ETag:**
- **Usługi Azure CDN from Verizon** używa `ETag` domyślnie podczas **Azure CDN from Akamai** nie.
- `ETag` definiuje ciąg, który jest unikatowy dla każdego pliku i wersja pliku. Na przykład `ETag: "17f0ddd99ed5bbe4edffdd6496d7131f"`.
- Wprowadzona w protokołu HTTP 1.1 i jest nowszy niż `Last-Modified`. Przydatne w przypadku daty ostatniej modyfikacji trudno jest określić.
- Obsługuje zarówno silnej weryfikacji i sprawdzania poprawności słaby; Jednak usługi Azure CDN obsługuje tylko silnej weryfikacji. Do weryfikacji silnej reprezentacji dwóch zasobów musi być bajtów dla bajtu identyczne. 
- Pamięć podręczna sprawdza poprawność pliku, która używa `ETag` wysyłając `If-None-Match` nagłówek z co najmniej jednego `ETag` modułów sprawdzania poprawności w żądaniu. Na przykład `If-None-Match: "17f0ddd99ed5bbe4edffdd6496d7131f"`. Jeśli wersja serwera jest zgodna `ETag` modułu sprawdzania poprawności na liście, wysyła kod stanu 304 (nie jest modyfikowany) w odpowiedzi. W przypadku różnych wersji, serwer odpowiada z kodem stanu 200 (OK) i zaktualizowanego zasobu.

**Last-Modified:**
- Aby uzyskać **Azure CDN from Verizon tylko**, `Last-Modified` jest używana, gdy `ETag` nie jest częścią odpowiedzi HTTP. 
- Określa datę i godzinę serwera pochodzenia wykrył, że zasób ostatniej modyfikacji. Na przykład `Last-Modified: Thu, 19 Oct 2017 09:28:00 GMT`.
- Weryfikuje pamięci podręcznej plików przy użyciu `Last-Modified` wysyłając `If-Modified-Since` nagłówek o datę i godzinę w żądaniu. Serwer pochodzenia porównuje tą datą `Last-Modified` nagłówka najnowszych zasobów. Jeśli zasób nie został zmodyfikowany od określonego czasu, serwer zwraca kod stanu 304 (nie jest modyfikowany) w odpowiedzi. Jeśli zasób został zmodyfikowany, serwer zwraca stanu 200 (OK) i zaktualizowanego zasobu kodu.

## <a name="determining-which-files-can-be-cached"></a>Określanie, które pliki mogą być buforowane.

Nie wszystkie zasoby mogą być buforowane. W poniższej tabeli przedstawiono, jakie zasoby mogą być buforowane, na podstawie typu odpowiedzi HTTP. Nie można buforować zasoby dostarczane z odpowiedzi HTTP, które nie zostaną spełnione wszystkie następujące warunki. Aby uzyskać **Azure CDN from Verizon Premium** , aparat reguł umożliwia dostosowanie niektórych z tych warunków.

|                   | Usługi Azure CDN from Verizon | Azure CDN from Akamai            |
|------------------ |------------------------|----------------------------------|
| Kody stanu HTTP | 200                    | 200, 203 300, 301, 302 i 401 |
| Metoda HTTP       | GET                    | GET                              |
| Rozmiar pliku         | 300 GB                 | — Optymalizacja dostarczania ogólne sieci web: 1,8 GB<br />-Multimediów strumieniowych optymalizacje: 1,8 GB<br />-Optymalizacja dużych plików: 150 GB |

## <a name="default-caching-behavior"></a>Domyślne zachowanie buforowania

W poniższej tabeli opisano domyślne zachowanie dla produkty Azure CDN i ich optymalizacje buforowania.

|                    | Verizon: dostarczanie ogólne web | Verizon: DSA | Akamai: dostarczanie ogólne web | Akamai: DSA | Akamai: pobieranie plików o dużym | Akamai: ogólne lub przesyłania strumieniowego multimediów VOD |
|--------------------|--------|------|-----|----|-----|-----|
| **Honoruj źródła**   | Yes    | Nie   | Yes | Nie | Yes | Yes |
| **Czas trwania pamięci podręcznej CDN** | 7 dni | None | 7 dni | Brak | 1 dzień | 1 rok |

**Uznawać pochodzenia**: Określa, czy należy przestrzegać [obsługiwanych nagłówków pamięci podręcznej dyrektywy](#http-cache-directive-headers) Jeśli istnieją w odpowiedzi HTTP z serwera pochodzenia.

**Czas trwania pamięci podręcznej CDN**: Określa ilość czasu, dla którego jest buforowana zasobu w sieci CDN w warstwie Azure. Jednak jeśli **honorować pochodzenia** brzmią "tak" i odpowiedzi HTTP z serwera pochodzenia zawiera nagłówek pamięci podręcznej dyrektywy `Expires` lub `Cache-Control: max-age`, wartości czasu trwania użyty przez nagłówek używa usługi Azure CDN. 

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się, jak dostosować i zastąpić domyślne zachowanie w sieci CDN do buforowania reguły buforowania, zobacz [kontroli usługi Azure CDN zachowanie buforowania z buforowaniem reguły](cdn-caching-rules.md). 
- Aby dowiedzieć się jak używać ciągów zapytania, aby formant zachowanie buforowania, zobacz [kontroli usługi Azure CDN buforowanie z ciągami zapytań](cdn-query-string.md).



