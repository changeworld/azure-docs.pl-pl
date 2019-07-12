---
title: Jak działa buforowanie | Dokumentacja firmy Microsoft
description: Buforowanie jest proces przechowywania danych lokalnie, tak aby przyszłe żądania dotyczące szybciej dostępne dane.
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
ms.date: 04/30/2018
ms.author: magattus
ms.openlocfilehash: 92d93fbf9fa2f8df15acb62802d7ac53db836dc1
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593856"
---
# <a name="how-caching-works"></a>Jak działa buforowanie

Ten artykuł zawiera omówienie ogólnych pojęć, buforowania i w jaki sposób [Azure Content Delivery Network (CDN)](cdn-overview.md) używa buforowania w celu zwiększenia wydajności. Jeśli chcesz dowiedzieć się więcej o tym, jak dostosować zachowanie buforowania w punkcie końcowym usługi CDN, zobacz [kontroli Azure działanie buforowania usługi CDN przy użyciu reguł buforowania](cdn-caching-rules.md) i [kontroli usługi Azure CDN caching zachowanie z ciągami zapytań](cdn-query-string.md).

## <a name="introduction-to-caching"></a>Wprowadzenie do buforowania

Buforowanie jest proces przechowywania danych lokalnie, tak aby przyszłe żądania dotyczące szybciej dostępne dane. W najbardziej typowe rodzaj buforowania, pamięć podręczną przeglądarki sieci web, sieci web przeglądarce przechowuje kopie danych statycznych lokalnie na lokalnym dysku twardym. Korzystając z buforowania, przeglądarki sieci web należy unikać wprowadzania wiele rund do serwera i zamiast tego dostęp do tych samych danych lokalnie, dzięki czemu można oszczędzić czas i zasoby. Buforowanie jest dobrze nadaje się do lokalnego zarządzania małe, statyczne dane, takie jak obrazy statyczne, pliki CSS i JavaScript plików.

Podobnie użycia pamięci podręcznej przez sieć dostarczania zawartości na serwerach granicznych blisko użytkownika w celu uniknięcia żądań przesyłanych z powrotem do źródła i zredukowanie opóźnień przez użytkownika końcowego. W odróżnieniu od przeglądarki pamięci podręcznej sieci web, która jest używana tylko dla jednego użytkownika, usługa CDN ma udostępnionej pamięci podręcznej. W pamięci podręcznej z sieci CDN udostępnionych plików, które są wymagane przez jednego użytkownika jest możliwy później przez innych użytkowników, co znacznie zmniejsza liczbę żądań do serwera pochodzenia.

Nie można buforować dynamicznych zasobów, które zmieniają się często, lub są unikatowe dla poszczególnych użytkowników. Te typy zasobów, jednak można skorzystać z witryn dynamicznych przyspieszanie (witryn dynamicznych DSA) optymalizacji na platformie Azure Content Delivery Network zwiększające wydajność.

Buforowanie może wystąpić na różnych poziomach między serwerem pochodzenia i użytkownika końcowego:

- Serwer sieci Web: Używa udostępnionej pamięci podręcznej (w przypadku wielu użytkowników).
- Usługa content delivery network: Używa udostępnionej pamięci podręcznej (w przypadku wielu użytkowników).
- Usługodawcy internetowego (ISP): Używa udostępnionej pamięci podręcznej (w przypadku wielu użytkowników).
- Przeglądarka sieci Web: Używa prywatnej pamięci podręcznej (dla jednego użytkownika).

Każda usługa pamięć podręczna zazwyczaj zarządza własną świeżości zasobów i wykonuje sprawdzanie poprawności, gdy plik jest nieaktualny. To zachowanie jest zdefiniowany w protokole HTTP, buforowaniem specyfikacji [RFC 7234](https://tools.ietf.org/html/rfc7234).

### <a name="resource-freshness"></a>Świeżości zasobów

Ponieważ zasobów pamięci podręcznej może potencjalnie być nieaktualne lub nieaktualne (w porównaniu do odpowiednich zasobów na serwerze źródłowym), ważne jest, aby każdy mechanizm buforowania do sterowania podczas odświeżania zawartości. Aby zaoszczędzić czas oraz przepustowości łącza, zasób pamięci podręcznej nie była porównywana z wersją na serwerze źródłowym za każdym razem, gdy jest on dostępny. Zamiast tego należy tak długo, jak zasób pamięci podręcznej jest uważana za świeże, jego zakłada, że aktualna wersja i są wysyłane bezpośrednio do klienta. Zasób pamięci podręcznej uważany jest za świeże, gdy jego okres ważności jest mniejsza niż okres ważności lub okresie zdefiniowanym przez ustawienie pamięci podręcznej. Na przykład gdy przeglądarka ponowne załadowanie strony sieci web, sprawdzi, czy każdy zasób pamięci podręcznej na dysku twardym jest odświeżona i ładuje je. Jeśli zasób nie jest świeże (przestarzałe) aktualną kopię załadowane z serwera.

### <a name="validation"></a>Walidacja

Jeśli zasób jest uznawane za przestarzałe, serwer pochodzenia jest proszony o weryfikacji, to znaczy, określić, czy dane w pamięci podręcznej nadal odpowiada elementom na serwerze źródłowym. Jeśli plik został zmodyfikowany na serwerze źródłowym, pamięci podręcznej aktualizuje jego wersję zasobu. W przeciwnym razie, jeśli zasób jest świeże, dane są dostarczane bezpośrednio z pamięci podręcznej bez najpierw sprawdzania poprawności.

### <a name="cdn-caching"></a>Pamięć podręczna usługi CDN

Buforowanie jest integralną częścią funkcjonowania usługi CDN, dostawy i zmniejszyć obciążenie źródła dla statycznych zasobów, takich jak obrazy, czcionki i filmy wideo. W pamięci podręcznej usługi CDN, zasoby statyczne selektywnie są przechowywane w strategicznie rozmieszczonych serwerów, które są bardziej lokalne z użytkownikiem i ma następujące zalety:

- Ponieważ większość ruchu w sieci web są statyczne (na przykład, obrazy, czcionki i filmy wideo), buforowania usługi CDN zmniejsza opóźnienie sieci dzięki przeniesieniu zawartości bliżej użytkownikowi, zmniejszając w ten sposób odległość danych przybliżone ilości tych danych.

- Dzięki przeniesieniu pracy do usługi CDN, buforowanie może zmniejszyć ruch sieciowy i obciążenie na serwerze źródłowym. Zmniejszy koszty oraz zapotrzebowania na zasoby dla aplikacji, nawet w przypadku dużej liczby użytkowników.

Podobnie do sposobu implementacji buforowania w przeglądarce sieci web, można kontrolować, jak buforowanie odbywa się w sieci CDN, wysyłając nagłówki z dyrektywami. Nagłówki z dyrektywami są nagłówki HTTP, które zazwyczaj są dodawane przez serwer pochodzenia. Mimo że większość z tych nagłówków pierwotnie był przeznaczony do adresów pamięci podręcznej w przeglądarkach klientów, teraz również są używane przez wszystkie pośrednie pamięciami podręcznymi, takich jak usługi CDN. 

Dwa nagłówki mogą być używane do definiowania świeżości pamięci podręcznej: `Cache-Control` i `Expires`. `Cache-Control` jest bardziej aktualny i ma pierwszeństwo przed `Expires`, jeśli istnieją obie. Istnieją również dwa rodzaje nagłówków używanych do weryfikacji (o nazwie moduły weryfikacji): `ETag` i `Last-Modified`. `ETag` jest bardziej aktualny i ma pierwszeństwo przed `Last-Modified`, jeśli obie są zdefiniowane.  

## <a name="cache-directive-headers"></a>Nagłówki z dyrektywami

> [!IMPORTANT]
> Domyślny punkt końcowy usługi Azure CDN, który jest zoptymalizowany pod kątem DSA ignoruje nagłówki z dyrektywami, a pomija buforowania. Dla **Azure CDN Standard from Verizon** i **Azure CDN Standard from Akamai** profile, można dostosować sposób traktuje te nagłówki punktu końcowego usługi Azure CDN przy użyciu [reguły buforowania sieci CDN](cdn-caching-rules.md)Aby włączyć buforowanie. Aby uzyskać **Azure CDN Premium from Verizon** tylko profile, użyj [aparat reguł](cdn-rules-engine.md) Aby włączyć buforowanie.

Usługa Azure CDN obsługuje następujące nagłówki dyrektywami HTTP, które określają czas trwania pamięci podręcznej i współużytkowania pamięci podręcznej.

**Cache-Control:**
- Wprowadzona w protokołu HTTP 1.1, aby zapewnić większą kontrolę nad ich zawartość sieci web wydawcy i adresu ograniczenia `Expires` nagłówka.
- Zastępuje `Expires` nagłówka, jeśli obie go i `Cache-Control` są zdefiniowane.
- Gdy jest używana w żądaniu HTTP od klienta do POP sieci CDN `Cache-Control` jest ignorowane przez wszystkie profile usługi Azure CDN, domyślnie.
- Gdy są używane w odpowiedzi HTTP od klienta do POP sieci CDN:
     - **Usługa Azure CDN standardowa/Premium from Verizon** i **Azure CDN Standard from Microsoft** obsługują wszystkie `Cache-Control` dyrektywy.
     - **Usługa Azure CDN Standard from Akamai** obsługuje tylko następujące `Cache-Control` dyrektywy; wszystkie inne są ignorowane:
         - `max-age`: Pamięć podręczna może przechowywać zawartość dla liczby sekund określonej. Na przykład `Cache-Control: max-age=5`. Ta dyrektywa określa maksymalną ilość czasu, w których zawartość jest uważany za od nowa.
         - `no-cache`: Zawartość w pamięci podręcznej, ale zweryfikować zawartość, za każdym razem, gdy przed dostarczeniem jej z pamięci podręcznej. Odpowiednikiem `Cache-Control: max-age=0`.
         - `no-store`: Nigdy nie buforują zawartości. Usuń zawartość, jeśli zostały wcześniej zapisane.

**Wygasa:**
- Nagłówek starszej wersji, wprowadzona w wersji 1.0 HTTP; obsługiwane w przypadku zapewnienia zgodności.
- Używa czasu wygaśnięcia na podstawie daty z dokładnością do drugiego. 
- Podobnie jak `Cache-Control: max-age`.
- Używany podczas `Cache-Control` nie istnieje.

**Pragma:**
   - Nie uznawane przez usługę Azure CDN, domyślnie.
   - Nagłówek starszej wersji, wprowadzona w wersji 1.0 HTTP; obsługiwane w przypadku zapewnienia zgodności.
   - Używana jako nagłówek żądania klienta z następującą dyrektywę: `no-cache`. Ta dyrektywa nakazuje serwerowi dostarczaj nową wersję zasobu.
   - `Pragma: no-cache` jest odpowiednikiem `Cache-Control: no-cache`.

## <a name="validators"></a>Moduły weryfikacji

Gdy pamięć podręczna jest przestarzałe, modułów sprawdzania poprawności HTTP pamięci podręcznej są używane do porównania zbuforowaną wersję pliku z wersją na serwerze źródłowym. **Usługa Azure CDN standardowa/Premium from Verizon** obsługuje zarówno `ETag` i `Last-Modified` modułów sprawdzania poprawności, domyślnie podczas **Azure CDN Standard from Microsoft** i **Azure CDN Standard from Akamai** obsługuje tylko `Last-Modified` domyślnie.

**Element ETag:**
- **Usługa Azure CDN standardowa/Premium from Verizon** obsługuje `ETag` domyślnie podczas **Azure CDN Standard from Microsoft** i **Azure CDN Standard from Akamai** nie obsługują.
- `ETag` definiuje ciąg, który jest unikatowy dla każdego pliku i wersja pliku. Na przykład `ETag: "17f0ddd99ed5bbe4edffdd6496d7131f"`.
- Wprowadzona w wersji 1.1 protokołu HTTP i jest nowszy niż `Last-Modified`. Parametr jest przydatne, gdy daty ostatniej modyfikacji trudno jest określić.
- Obsługuje zarówno weryfikacji silnych i słabych weryfikacji; Jednak usługa Azure CDN obsługuje tylko silnej weryfikacji. Dla weryfikacji silnych reprezentacji dwóch zasobów musi być na bajt identyczne. 
- Pamięć podręczna sprawdza poprawność pliku, która używa `ETag` , wysyłając `If-None-Match` nagłówka z jednej lub kilku `ETag` modułów sprawdzania poprawności w żądaniu. Na przykład `If-None-Match: "17f0ddd99ed5bbe4edffdd6496d7131f"`. Jeśli zgodna z wersją serwera `ETag` modułu sprawdzania poprawności na liście, wysyła kod stanu 304 (nie zmodyfikowano) w swojej odpowiedzi. Jeśli wersja jest inna, serwer odpowiada, zwracając kod stanu 200 (OK) i zaktualizowanego zasobu.

**Last-Modified:**
- Dla **Azure CDN standardowa/Premium from Verizon** tylko `Last-Modified` jest używany, gdy `ETag` nie jest częścią odpowiedzi HTTP. 
- Określa datę i godzinę, który serwer pochodzenia wykrył, że ostatniej modyfikacji zasobu. Na przykład `Last-Modified: Thu, 19 Oct 2017 09:28:00 GMT`.
- Pamięć podręczna sprawdza poprawność pliku przy użyciu `Last-Modified` , wysyłając `If-Modified-Since` nagłówek daty i godziny w żądaniu. Serwer pochodzenia porównuje tej daty za pomocą `Last-Modified` nagłówka najnowszych zasobów. Jeśli zasób nie został zmodyfikowany od czasu określonym czasie, serwer zwraca kod stanu 304 (nie zmodyfikowano) w swojej odpowiedzi. Jeśli zasób został zmodyfikowany, serwer zwraca stan kod 200 (OK) i zaktualizowanego zasobu.

## <a name="determining-which-files-can-be-cached"></a>Określanie, które pliki mogą być buforowane.

Nie wszystkie zasoby mogą być buforowane. W poniższej tabeli przedstawiono, jakie zasoby mogą być buforowane, na podstawie typu odpowiedzi HTTP. Nie można buforować zasoby dostarczane z odpowiedzi HTTP, które nie są spełnione wszystkie następujące warunki. Aby uzyskać **Azure CDN Premium from Verizon** tylko, można użyć aparatu reguł można dostosować niektóre z tych warunków.

|                   | Usługa Azure CDN firmy Microsoft          | Usługa Azure CDN from Verizon | Usługa Azure CDN from Akamai        |
|-------------------|-----------------------------------|------------------------|------------------------------|
| Kody stanu HTTP | 200, 203, 206, 300, 301, 410, 416 | 200                    | 200, 203, 300, 301, 302, 401 |
| Metody HTTP      | GET, HEAD                         | GET                    | GET                          |
| Limity rozmiaru plików  | 300 GB                            | 300 GB                 | -Optymalizacja dostarczania w sieci web ogólne: 1,8 GB<br />-Optymalizacje przesyłania strumieniowego nośnika: 1,8 GB<br />-Optymalizacja dużych plików: 150 GB |

Aby uzyskać **Azure CDN w warstwie standardowa firmy Microsoft** buforowania do pracy nad zasobu, serwer pochodzenia musi obsługiwać wszystkie główne żądania GET HTTP i wartości długości zawartości musi być taka sama, HEAD i GET HTTP w całości, odpowiedzi dla zasobu. Aby żądanie HEAD serwer pochodzenia musi obsługiwać żądania HEAD i musi odpowiadać, podając ten sam nagłówek tak, jakby jego otrzymała żądanie GET.

## <a name="default-caching-behavior"></a>Domyślne zachowanie buforowania

W poniższej tabeli opisano domyślne zachowanie dla produktów Azure CDN i ich optymalizacji buforowania.

|    | Microsoft: Ogólne dostarczanie w Internecie | Verizon: Ogólne dostarczanie w Internecie | Verizon: DSA | Akamai: Ogólne dostarczanie w Internecie | Akamai: DSA | Akamai: Pobieranie dużych plików | Akamai: ogólne lub strumieniowego przesyłania multimediów wideo na żądanie |
|------------------------|--------|-------|------|--------|------|-------|--------|
| **Honoruj źródła**       | Yes    | Yes   | Nie   | Yes    | Nie   | Yes   | Yes    |
| **Czas trwania pamięci podręcznej usługi CDN** | 2 dni |7 dni | Brak | 7 dni | Brak | 1 dzień | 1 rok |

**Uwzględnić pochodzenia**: Określa, czy uwzględnić obsługiwanych nagłówki z dyrektywami, jeśli istnieją w odpowiedzi HTTP z serwera pochodzenia.

**Czas trwania pamięci podręcznej usługi CDN**: Określa ilość czasu, dla której zasób jest buforowany w usłudze Azure CDN. Jednak jeśli **respektować pochodzenia** jest tak i odpowiedzi HTTP z serwera pochodzenia zawiera nagłówek dyrektywami `Expires` lub `Cache-Control: max-age`, wysokiej dostępności treści Azure używa wartości czasu trwania, zamiast tego określony w nagłówku. 

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak dostosować i zastąpić domyślne zachowanie w usłudze CDN przy użyciu reguł buforowania buforowania, zobacz [kontroli Azure działanie buforowania usługi CDN przy użyciu reguł buforowania](cdn-caching-rules.md). 
- Aby dowiedzieć się, jak używać ciągów zapytań do kontroli zachowania buforowania, zobacz [kontroli usługi Azure CDN caching zachowanie z ciągami zapytań](cdn-query-string.md).



