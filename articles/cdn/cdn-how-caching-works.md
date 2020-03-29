---
title: Jak działa buforowanie | Dokumenty firmy Microsoft
description: Buforowanie to proces przechowywania danych lokalnie, dzięki czemu przyszłe żądania dotyczące tych danych są dostępne szybciej.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593856"
---
# <a name="how-caching-works"></a>Jak działa buforowanie

Ten artykuł zawiera omówienie ogólnych pojęć buforowania i jak [usługa Azure Content Delivery Network (CDN)](cdn-overview.md) używa buforowania w celu zwiększenia wydajności. Jeśli chcesz dowiedzieć się, jak dostosować zachowanie buforowania w punkcie końcowym usługi CDN, zobacz [Kontrolowanie zachowania buforowania usługi Azure CDN za pomocą reguł buforowania](cdn-caching-rules.md) i [kontrolowanie zachowania buforowania usługi Azure CDN za pomocą ciągów zapytań.](cdn-query-string.md)

## <a name="introduction-to-caching"></a>Wprowadzenie do buforowania

Buforowanie to proces przechowywania danych lokalnie, dzięki czemu przyszłe żądania dotyczące tych danych są dostępne szybciej. W najczęstszym typie buforowania, buforowaniu przeglądarki internetowej, przeglądarka internetowa przechowuje kopie danych statycznych lokalnie na lokalnym dysku twardym. Za pomocą buforowania, przeglądarka internetowa może uniknąć wielu rund do serwera i zamiast tego uzyskać dostęp do tych samych danych lokalnie, oszczędzając w ten sposób czas i zasoby. Buforowanie doskonale nadaje się do lokalnego zarządzania małymi, statycznymi danymi, takimi jak obrazy statyczne, pliki CSS i pliki JavaScript.

Podobnie buforowanie jest używane przez sieć dostarczania zawartości na serwerach brzegowych w pobliżu użytkownika, aby uniknąć żądań podróżujących z powrotem do źródła i zmniejszając opóźnienie użytkownika końcowego. W przeciwieństwie do pamięci podręcznej przeglądarki sieci Web, która jest używana tylko dla pojedynczego użytkownika, sieć CDN ma współużytkowaną pamięć podręczną. W udostępnionej pamięci podręcznej sieci CDN plik wymagany przez jednego użytkownika jest później dostępny dla innych użytkowników, co znacznie zmniejsza liczbę żądań do serwera pochodzenia.

Nie można buforować zasobów dynamicznych, które często się zmieniają lub są unikatowe dla poszczególnych użytkowników. Te typy zasobów można jednak skorzystać z optymalizacji dynamicznego przyspieszania witryny (DSA) w sieci dostarczania zawartości platformy Azure w celu poprawy wydajności.

Buforowanie może wystąpić na wielu poziomach między serwerem pochodzenia a użytkownikiem końcowym:

- Serwer sieci Web: używa udostępnionej pamięci podręcznej (dla wielu użytkowników).
- Sieć dostarczania zawartości: używa udostępnionej pamięci podręcznej (dla wielu użytkowników).
- Dostawca usług internetowych (ISP): Używa udostępnionej pamięci podręcznej (dla wielu użytkowników).
- Przeglądarka internetowa: używa prywatnej pamięci podręcznej (dla jednego użytkownika).

Każda pamięć podręczna zazwyczaj zarządza własną świeżością zasobów i wykonuje sprawdzanie poprawności, gdy plik jest przestarzały. To zachowanie jest zdefiniowane w specyfikacji buforowania HTTP, [RFC 7234](https://tools.ietf.org/html/rfc7234).

### <a name="resource-freshness"></a>Świeżość zasobów

Ponieważ zasób buforowany może być potencjalnie nieaktualny lub przestarzały (w porównaniu z odpowiednim zasobem na serwerze pochodzenia), ważne jest, aby każdy mechanizm buforowania kontrolował, kiedy zawartość jest odświeżana. Aby zaoszczędzić czas i zużycie przepustowości, buforowany zasób nie jest porównywany z wersją na serwerze pochodzenia za każdym razem, gdy jest uzyskiwał dostęp. Zamiast tego tak długo, jak buforowany zasób jest uważany za świeży, zakłada się, że jest najbardziej aktualną wersją i jest wysyłany bezpośrednio do klienta. Zasób buforowany jest uważany za świeży, gdy jego wiek jest mniejszy niż wiek lub okres zdefiniowany przez ustawienie pamięci podręcznej. Na przykład gdy przeglądarka ponownie ładuje stronę sieci web, sprawdza, czy każdy buforowany zasób na dysku twardym jest świeży i ładuje go. Jeśli zasób nie jest świeży (nieaktualny), aktualna kopia jest ładowana z serwera.

### <a name="validation"></a>Sprawdzanie poprawności

Jeśli zasób jest uważany za przestarzały, serwer pochodzenia jest proszony o jego sprawdzenie poprawności, czyli o ustalenie, czy dane w pamięci podręcznej nadal są zgodne z danymi na serwerze pochodzenia. Jeśli plik został zmodyfikowany na serwerze pochodzenia, pamięć podręczna aktualizuje swoją wersję zasobu. W przeciwnym razie jeśli zasób jest świeży, dane są dostarczane bezpośrednio z pamięci podręcznej bez sprawdzania poprawności go najpierw.

### <a name="cdn-caching"></a>Buforowanie cdn

Buforowanie jest integralną częścią sposobu działania sieci CDN w celu przyspieszenia dostarczania i zmniejszenia obciążenia źródła dla zasobów statycznych, takich jak obrazy, czcionki i filmy wideo. W buforowaniu sieci CDN zasoby statyczne są selektywnie przechowywane na strategicznie rozmieszczonych serwerach, które są bardziej lokalne dla użytkownika i oferują następujące korzyści:

- Ponieważ większość ruchu w sieci web jest statyczna (na przykład obrazy, czcionki i filmy wideo), buforowanie sieci CDN zmniejsza opóźnienie sieci, zbliżając zawartość do użytkownika, zmniejszając w ten sposób odległość, jaką przemieszczają się dane.

- Odciążając pracę do sieci CDN, buforowanie może zmniejszyć ruch sieciowy i obciążenie serwera pochodzenia. Zmniejsza to wymagania dotyczące kosztów i zasobów dla aplikacji, nawet jeśli istnieje duża liczba użytkowników.

Podobnie jak buforowanie jest implementowana w przeglądarce sieci web, można kontrolować sposób buforowania jest wykonywana w sieci CDN, wysyłając nagłówki dyrektywy pamięci podręcznej. Nagłówki dyrektywy pamięci podręcznej są nagłówkami HTTP, które są zazwyczaj dodawane przez serwer pochodzenia. Chociaż większość z tych nagłówków została pierwotnie zaprojektowana do obsługi buforowania w przeglądarkach klienckich, są one teraz również używane przez wszystkie pośrednie pamięci podręczne, takie jak sieci CDN. 

Dwa nagłówki mogą być używane do `Cache-Control` definiowania świeżości pamięci podręcznej: i `Expires`. `Cache-Control`jest bardziej aktualny i `Expires`ma pierwszeństwo przed , jeśli oba istnieją. Istnieją również dwa typy nagłówków używanych do `ETag` `Last-Modified`sprawdzania poprawności (nazywane walidatorami): i . `ETag`jest bardziej aktualny i `Last-Modified`ma pierwszeństwo przed , jeśli oba są zdefiniowane.  

## <a name="cache-directive-headers"></a>Nagłówki dyrektywy w pamięci podręcznej

> [!IMPORTANT]
> Domyślnie punkt końcowy usługi Azure CDN, który jest zoptymalizowany pod kątem DSA ignoruje nagłówki dyrektywy pamięci podręcznej i pomija buforowanie. W przypadku **usługi Azure CDN Standard firmy Verizon** i Azure **CDN Standard z profilów Akamai** można dostosować sposób, w jaki punkt końcowy usługi Azure CDN traktuje te nagłówki przy użyciu [reguł buforowania usługi CDN](cdn-caching-rules.md) w celu umożliwienia buforowania. Tylko w przypadku profilów **usługi Azure CDN Premium z usług Verizon** aparat [reguł](cdn-rules-engine.md) umożliwia buforowanie.

Usługa Azure CDN obsługuje następujące nagłówki http cache-dyrektywy, które definiują czas trwania pamięci podręcznej i udostępnianie pamięci podręcznej.

**Kontrola pamięci podręcznej:**
- Wprowadzony w HTTP 1.1, aby dać wydawcom internetowym większą kontrolę `Expires` nad ich zawartością i rozwiązać ograniczenia nagłówka.
- Zastępuje `Expires` nagłówek, jeśli oba i `Cache-Control` są zdefiniowane.
- Gdy jest używany w żądaniu HTTP od `Cache-Control` klienta do punktu obecności CDN, jest domyślnie ignorowany przez wszystkie profile usługi Azure CDN.
- W przypadku użycia w odpowiedzi HTTP od klienta do punktu obecności CDN:
     - **Usługa Azure CDN Standard/Premium firmy Verizon** i Azure **CDN Standard firmy Microsoft** obsługują wszystkie `Cache-Control` dyrektywy.
     - **Usługa Azure CDN Standard firmy Akamai** obsługuje tylko następujące `Cache-Control` dyrektywy; wszystkie inne są ignorowane:
         - `max-age`: Pamięć podręczna może przechowywać zawartość przez określoną liczbę sekund. Na przykład `Cache-Control: max-age=5`. Niniejsza dyrektywa określa maksymalny czas, przez który zawartość jest uważana za świeżą.
         - `no-cache`: Buforuj zawartość, ale sprawdź poprawność zawartości za każdym razem przed dostarczeniem jej z pamięci podręcznej. Odpowiednik `Cache-Control: max-age=0`.
         - `no-store`: Nigdy nie buforuj zawartości. Usuń zawartość, jeśli została wcześniej zapisana.

**Wygasa:**
- Starszy nagłówek wprowadzony w http 1.0; wstecznej zgodności.
- Używa czasu wygaśnięcia opartego na dacie z drugą precyzją. 
- Podobnie `Cache-Control: max-age`jak w .
- Używane, `Cache-Control` gdy nie istnieje.

**Pragmy:**
   - Domyślnie nie jest honorowany przez usługę Azure CDN.
   - Starszy nagłówek wprowadzony w http 1.0; wstecznej zgodności.
   - Używany jako nagłówek żądania klienta z `no-cache`następującą dyrektywą: . Ta dyrektywa nakazuje serwerowi dostarczanie nowej wersji zasobu.
   - `Pragma: no-cache`odpowiada `Cache-Control: no-cache`.

## <a name="validators"></a>Walidatory

Gdy pamięć podręczna jest przestarzała, moduły sprawdzania poprawności pamięci podręcznej HTTP są używane do porównywania buforowanej wersji pliku z wersją na serwerze pochodzenia. **Usługa Azure CDN Standard/Premium firmy Verizon** domyślnie obsługuje oba `ETag` `Last-Modified` i walidatory, podczas gdy usługa Azure **CDN Standard firmy Microsoft** i Azure **CDN Standard firmy Akamai** obsługuje tylko `Last-Modified` domyślnie.

**Etag:**
- **Usługa Azure CDN Standard/Premium firmy Verizon** obsługuje `ETag` domyślnie, podczas gdy usługa Azure **CDN Standard firmy Microsoft** i Azure **CDN Standard firmy Akamai** nie obsługuje.
- `ETag`definiuje ciąg, który jest unikatowy dla każdego pliku i wersji pliku. Na przykład `ETag: "17f0ddd99ed5bbe4edffdd6496d7131f"`.
- Wprowadzony w HTTP 1.1 i `Last-Modified`jest bardziej aktualny niż . Przydatne, gdy data ostatniej modyfikacji jest trudna do określenia.
- Obsługuje zarówno silne sprawdzanie poprawności i słabe sprawdzanie poprawności; jednak usługa Azure CDN obsługuje tylko silne sprawdzanie poprawności. W przypadku silnej weryfikacji dwie reprezentacje zasobów muszą być identyczne bajt-for-byte. 
- Pamięć podręczna sprawdza poprawność `ETag` pliku, `If-None-Match` który używa, `ETag` wysyłając nagłówek z co najmniej jednym modułem sprawdzania poprawności w żądaniu. Na przykład `If-None-Match: "17f0ddd99ed5bbe4edffdd6496d7131f"`. Jeśli wersja serwera pasuje `ETag` do walidatora na liście, wysyła kod stanu 304 (Nie zmodyfikowano) w odpowiedzi. Jeśli wersja jest inna, serwer odpowiada kodem stanu 200 (OK) i zaktualizowanym zasobem.

**Ostatnia modyfikacja:**
- Tylko dla **usługi Azure CDN Standard/Premium firmy Verizon** jest używana, `Last-Modified` jeśli `ETag` nie jest częścią odpowiedzi HTTP. 
- Określa datę i godzinę, która określiła ostatnio zasób przez serwer pochodzenia. Na przykład `Last-Modified: Thu, 19 Oct 2017 09:28:00 GMT`.
- Pamięć podręczna sprawdza `Last-Modified` poprawność `If-Modified-Since` pliku przy użyciu, wysyłając nagłówek z datą i godziną w żądaniu. Serwer pochodzenia porównuje tę datę `Last-Modified` z nagłówkiem najnowszego zasobu. Jeśli zasób nie został zmodyfikowany od określonego czasu, serwer zwraca kod stanu 304 (Nie zmodyfikowano) w odpowiedzi. Jeśli zasób został zmodyfikowany, serwer zwraca kod stanu 200 (OK) i zaktualizowany zasób.

## <a name="determining-which-files-can-be-cached"></a>Określanie, które pliki mogą być buforowane

Nie wszystkie zasoby mogą być buforowane. W poniższej tabeli przedstawiono, jakie zasoby mogą być buforowane na podstawie typu odpowiedzi HTTP. Zasoby dostarczane z odpowiedziami HTTP, które nie spełniają wszystkich tych warunków, nie mogą być buforowane. Tylko w przypadku **usługi Azure CDN Premium firmy Verizon** można użyć aparatu reguł, aby dostosować niektóre z tych warunków.

|                   | Usługa Azure CDN firmy Microsoft          | Usługa Azure CDN firmy Verizon | Usługa Azure CDN firmy Akamai        |
|-------------------|-----------------------------------|------------------------|------------------------------|
| Kody stanu HTTP | 200, 203, 206, 300, 301, 410, 416 | 200                    | 200, 203, 300, 301, 302, 401 |
| Metody HTTP      | GET, GŁOWA                         | GET                    | GET                          |
| Limity rozmiaru pliku  | 300 GB                            | 300 GB                 | - Ogólna optymalizacja dostarczania stron internetowych: 1,8 GB<br />- Optymalizacja przesyłania strumieniowego multimediów: 1,8 GB<br />- Optymalizacja dużych plików: 150 GB |

Dla **standardu usługi Azure CDN firmy Microsoft** do pracy nad zasobem, serwer pochodzenia musi obsługiwać wszystkie żądania HEAD i GET HTTP, a wartości długości zawartości muszą być takie same dla wszystkich odpowiedzi HEAD i GET HTTP dla zasobu. W przypadku żądania HEAD serwer pochodzenia musi obsługiwać żądanie HEAD i musi odpowiadać tymi samymi nagłówkami, tak jakby otrzymał żądanie GET.

## <a name="default-caching-behavior"></a>Domyślne zachowanie buforowania

W poniższej tabeli opisano domyślne zachowanie buforowania produktów usługi Azure CDN i ich optymalizacje.

|    | Microsoft: Ogólne dostarczanie sieci Web | Verizon: Ogólne dostarczanie stron internetowych | Verizon: DSA | Akamai: Ogólne dostarczanie stron internetowych | Akamai: DSA | Akamai: Duży plik do pobrania | Akamai: ogólne lub VOD media streaming |
|------------------------|--------|-------|------|--------|------|-------|--------|
| **Cześć pochodzenia**       | Tak    | Tak   | Nie   | Tak    | Nie   | Tak   | Tak    |
| **Czas trwania pamięci podręcznej sieci CDN** | 2 dni |7 dni | Brak | 7 dni | Brak | 1 dzień | 1 rok |

**Honorowe pochodzenie:** Określa, czy mają być honorowane obsługiwane nagłówki dyrektywy pamięci podręcznej, jeśli istnieją one w odpowiedzi HTTP z serwera pochodzenia.

**Czas trwania pamięci podręcznej sieci CDN:** Określa czas, przez jaki zasób jest buforowany na sieci AZURE CDN. Jeśli jednak **pochodzenie Honoru** jest tak, a odpowiedź HTTP z `Expires` `Cache-Control: max-age`serwera pochodzenia zawiera nagłówek dyrektywy pamięci podręcznej lub , usługa Azure CDN używa wartości czasu trwania określonej przez nagłówek. 

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak dostosować i zastąpić domyślne zachowanie buforowania w sieci CDN za pomocą reguł buforowania, zobacz [Kontrolowanie zachowania buforowania usługi Azure CDN za pomocą reguł buforowania](cdn-caching-rules.md). 
- Aby dowiedzieć się, jak używać ciągów zapytań do kontrolowania zachowania buforowania, zobacz [Kontrolowanie zachowania buforowania usługi Azure CDN za pomocą ciągów zapytań.](cdn-query-string.md)



