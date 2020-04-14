---
title: Usługa Azure CDN firmy Verizon Premium określa warunki dopasowania silnika do reguł Verizon Premium | Dokumenty firmy Microsoft
description: Dokumentacja referencyjna dla usługi Azure Content Delivery Network z reguł Verizon Premium do warunków dopasowania aparatu.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: e2361590118668f2cdf22c4a29534b16790b90e4
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253445"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Warunki dopasowania aparatu usługi Azure CDN firmy Verizon Premium

W tym artykule wymieniono szczegółowe opisy dostępnych warunków dopasowania dla usługi Azure Content Delivery Network (CDN) z [aparatu reguł](cdn-verizon-premium-rules-engine.md)Verizon Premium .

Drugą częścią reguły jest warunek dopasowania. Warunek dopasowania identyfikuje określone typy żądań, dla których zostanie wykonany zestaw funkcji.

Na przykład można użyć warunku dopasowania do:

- Filtrowanie żądań zawartości w określonej lokalizacji.
- Filtrowanie żądań generowanych z określonego adresu IP lub kraju/regionu.
- Filtrowanie żądań według informacji nagłówka.

## <a name="always-match-condition"></a>Zawsze dopasowywały warunki

Warunek Zawsze dopasowywać stosuje domyślny zestaw funkcji do wszystkich żądań.

Nazwa | Przeznaczenie
-----|--------
[Zawsze](#always) | Stosuje domyślny zestaw funkcji do wszystkich żądań.

## <a name="device-match-condition"></a>Warunek dopasowania urządzenia

Warunek dopasowania urządzenia identyfikuje żądania z urządzenia przenośnego na podstawie jego właściwości.  

Nazwa | Przeznaczenie
-----|--------
[Urządzenie](#device) | Identyfikuje żądania z urządzenia przenośnego na podstawie jego właściwości.

## <a name="location-match-conditions"></a>Warunki dopasowania lokalizacji

Warunki dopasowania lokalizacji identyfikują żądania na podstawie lokalizacji osoby żądawczej.

Nazwa | Przeznaczenie
-----|--------
[Numer AS](#as-number) | Identyfikuje żądania pochodzące z określonej sieci.
[Country](#country) | Identyfikuje żądania pochodzące z określonych krajów/regionów.

## <a name="origin-match-conditions"></a>Warunki dopasowania pochodzenia

Warunki dopasowania pochodzenia identyfikują żądania wskazujące magazyn sieci dostarczania zawartości lub serwer pochodzenia klienta.

Nazwa | Przeznaczenie
-----|--------
[Pochodzenie CDN](#cdn-origin) | Identyfikuje żądania dotyczące zawartości przechowywanej w magazynie sieci dostarczania zawartości.
[Pochodzenie klienta](#customer-origin) | Identyfikuje żądania zawartości przechowywane na określonym serwerze pochodzenia klienta.

## <a name="request-match-conditions"></a>Warunki dopasowania żądania

Warunki dopasowania żądania identyfikują żądania na podstawie ich właściwości.

Nazwa | Przeznaczenie
-----|--------
[Adres IP klienta](#client-ip-address) | Identyfikuje żądania, które pochodzą z określonego adresu IP.
[Parametr pliku cookie](#cookie-parameter) | Sprawdza pliki cookie skojarzone z każdym żądaniem dla określonej wartości.
[Parametr pliku cookie Regex](#cookie-parameter-regex) | Sprawdza pliki cookie skojarzone z każdym żądaniem dla określonego wyrażenia regularnego.
[Nazwa Cname krawędzi](#edge-cname) | Identyfikuje żądania, które wskazują na określoną krawędź CNAME.
[Domena odsyłacająca](#referring-domain) | Identyfikuje żądania, które zostały odwołowe z określonych nazw hostów.
[Dosłowny nagłówek żądania](#request-header-literal) | Identyfikuje żądania, które zawierają określony nagłówek ustawiony na określoną wartość.
[Żądanie Regex nagłówka](#request-header-regex) | Identyfikuje żądania, które zawierają określony nagłówek ustawiony na wartość, która odpowiada określonej wyrażeniu regularnego.
[Symbol wieloznaczny nagłówka żądania](#request-header-wildcard) | Identyfikuje żądania, które zawierają określony nagłówek ustawiony na wartość, która pasuje do określonego wzorca.
[Request, metoda](#request-method) | Identyfikuje żądania według metody HTTP.
[Schemat żądania](#request-scheme) | Identyfikuje żądania przez ich protokół HTTP.

## <a name="url-match-conditions"></a>Warunki dopasowania adresu URL

Warunki dopasowania adresu URL identyfikują żądania na podstawie ich adresów URL.

Nazwa | Przeznaczenie
-----|--------
[Katalog ścieżki adresu URL](#url-path-directory) | Identyfikuje żądania według ich ścieżki względnej.
[Rozszerzenie ścieżki adresu URL](#url-path-extension) | Identyfikuje żądania przez ich rozszerzenie nazwy pliku.
[Nazwa pliku ścieżki adresu URL](#url-path-filename) | Identyfikuje żądania według ich nazwy pliku.
[Literał ścieżki adresu URL](#url-path-literal) | Porównuje ścieżkę względną żądania z określoną wartością.
[Ścieżka adresu URL Regex](#url-path-regex) | Porównuje ścieżkę względną żądania z określonym wyrażeniem regularnym.
[Symbol wieloznaczny ścieżki adresu URL](#url-path-wildcard) | Porównuje ścieżkę względną żądania z określonym wzorcem.
[Literał zapytania adresu URL](#url-query-literal) | Porównuje ciąg zapytania żądania z określoną wartością.
[Parametr kwerendy url](#url-query-parameter) | Identyfikuje żądania, które zawierają określony parametr ciągu kwerendy ustawiony na wartość, która pasuje do określonego wzorca.
[Regex kwerendy URL](#url-query-regex) | Identyfikuje żądania, które zawierają określony parametr ciągu kwerendy ustawiony na wartość, która odpowiada określonej wyrażeniu regularnego.
[Symbol wieloznaczny kwerendy URL](#url-query-wildcard) | Porównuje określoną wartość z ciągiem zapytania żądania.

## <a name="reference-for-rules-engine-match-conditions"></a>Informacje na kątem reguł warunków dopasowania silnika

---

### <a name="always"></a>Zawsze

Warunek Zawsze dopasowywać stosuje domyślny zestaw funkcji do wszystkich żądań.

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="as-number"></a>Numer AS

Sieć AS Number jest definiowana przez numer systemu autonomicznego (ASN). 

Opcja **Dopasowuje**/**nie pasuje** określa warunki, w których warunek dopasowania numeru AS jest spełniony:

- **Dopasowania:** Wymaga, aby sieć ASN sieci klienta była zgodna z jednym z określonych sieci ASN. 
- **Nie jest zgodny:** Wymaga, aby numer ASN sieci klienta nie odpowiadał żadnym określonym numerom ASN.

Najważniejsze informacje:

- Określ wiele sieci ASN, wyznaczając każdy z nich z jedną spacją. Na przykład 64514 64515 pasuje do żądań, które przychodzą z 64514 lub 64515.
- Niektóre żądania mogą nie zwracać prawidłowego asn. Znak zapytania (?) będzie pasować do żądań, dla których nie można ustalić prawidłowego ASN.
- Określ cały ASN dla żądanej sieci. Wartości częściowe nie zostaną dopasowane.
- Ze względu na sposób śledzenia ustawień pamięci podręcznej ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Pełne wypełnienie pamięci podręcznej
  - Domyślny wewnętrzny wiek maksymalny
  - Wymuszenie wewnętrznego max-age
  - Ignoruj niejmówętną pamięć podręczną origin
  - Wewnętrzny Max-Stale

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cdn-origin"></a>Pochodzenie CDN

Warunek dopasowania CDN Origin jest spełniony, gdy spełnione są oba następujące warunki:

- Zażądano zawartości z magazynu sieci CDN.
- Identyfikator URI żądania używa typu punktu dostępu do zawartości (na przykład /000001), który jest zdefiniowany w tym warunku dopasowania:
  - Adres URL sieci CDN: identyfikator URI żądania musi zawierać wybrany punkt dostępu do zawartości.
  - Adres URL CNAME krawędzi: odpowiednia konfiguracja CNAME krawędzi musi wskazywać wybrany punkt dostępu do zawartości.
  
Najważniejsze informacje:

- Punkt dostępu do zawartości identyfikuje usługę, która powinna obsługiwać żądaną zawartość.
- Nie używaj instrukcji AND IF, aby połączyć określone warunki dopasowania. Na przykład połączenie warunku dopasowania CDN Origin z warunkiem dopasowania pochodzenia klienta spowoduje utworzenie wzorca dopasowania, który nigdy nie może być dopasowany. Z tego powodu dwóch warunków dopasowania CDN Origin nie można łączyć za pomocą instrukcji AND IF.

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="client-ip-address"></a>Adres IP klienta

Opcja **Dopasowuje**/**nie pasuje** określa warunki, w których warunek dopasowania adresu IP klienta jest spełniony:

- **Dopasowania:** Wymaga, aby adres IP klienta odpowiadał jednemu z określonych adresów IP. 
- **Nie pasuje:** Wymaga, aby adres IP klienta nie odpowiadał żadnym z określonych adresów IP. 

Najważniejsze informacje:

- Użyj notacji CIDR.
- Określ wiele adresów IP i/lub bloków adresów IP, wyznaczając każdy z nich z jednym miejscem. Przykład:
  - **Przykład IPv4:** 1.2.3.4 10.20.30.40 pasuje do wszystkich żądań, które przychodzą z adresu 1.2.3.4 lub 10.20.30.40.
  - **Przykład IPv6**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 pasuje do wszelkich żądań 1:2:3:4:5:6:7:8 lub 10:20:30:40:50:60:70:80.
- Składnia bloku adresów IP jest podstawowym adresem IP, po którym następuje ukośnik do przodu i rozmiar prefiksu. Przykład:
  - **Przykład IPv4:** 5.5.5.64/26 pasuje do wszystkich żądań, które przychodzą z adresów 5.5.5.64 do 5.5.5.127.
  - **Przykład IPv6**: 1:2:3:/48 pasuje do wszelkich żądań, które przychodzą z adresów 1:2:3:0:0:0:0 do 1:2:3:ffff:ffff:ffff:ffff:ffff.
- Ze względu na sposób śledzenia ustawień pamięci podręcznej ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Pełne wypełnienie pamięci podręcznej
  - Domyślny wewnętrzny wiek maksymalny
  - Wymuszenie wewnętrznego max-age
  - Ignoruj niejmówętną pamięć podręczną origin
  - Wewnętrzny Max-Stale

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cookie-parameter"></a>Parametr pliku cookie

Opcja **Dopasowuje**/**nie pasuje** określa warunki, w których warunek dopasowania parametru pliku cookie jest spełniony.

- **Dopasowania:** Wymaga żądania zawierają określony plik cookie z wartością, która odpowiada co najmniej jednej z wartości, które są zdefiniowane w tym warunku dopasowania.
- **Nie pasuje:** Wymaga, aby żądanie spełniał jeden z następujących kryteriów:
  - Nie zawiera określonego pliku cookie.
  - Zawiera określony plik cookie, ale jego wartość nie pasuje do żadnej z wartości, które są zdefiniowane w tym warunku dopasowania.
  
Najważniejsze informacje:

- Nazwa pliku cookie:
  - Ponieważ wartości symboli wieloznacznych, w tym gwiazdki (*), nie są obsługiwane podczas określania nazwy pliku cookie, tylko dokładne dopasowania nazw plików cookie kwalifikują się do porównania.
  - Tylko jedna nazwa pliku cookie można określić na wystąpienie tego warunku dopasowania.
  - Porównania nazw plików cookie są niewrażliwe na argumenty.
- Wartość pliku cookie:
  - Określ wiele wartości plików cookie, wyznaczając każdy z nich z jednym spacją.
  - Wartość pliku cookie może korzystać z [wartości symboli wieloznacznych](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Jeśli wartość symboli wieloznacznych nie została określona, tylko dokładne dopasowanie spełni ten warunek dopasowania. Na przykład określenie "Value" będzie zgodne z "Value", ale nie "Value1" lub "Value2".
  - Użyj opcji **Ignoruj wielkość liter,** aby kontrolować, czy porównanie z uwzględnieniem wielkości liter jest dokonywane z wartością pliku cookie żądania.
- Ze względu na sposób śledzenia ustawień pamięci podręcznej ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Pełne wypełnienie pamięci podręcznej
  - Domyślny wewnętrzny wiek maksymalny
  - Wymuszenie wewnętrznego max-age
  - Ignoruj niejmówętną pamięć podręczną origin
  - Wewnętrzny Max-Stale

[Powrót do początku](#reference-for-rules-engine-match-conditions)
</br>

---

### <a name="cookie-parameter-regex"></a>Parametr pliku cookie Regex

Warunek dopasowania parametru cookie Regex definiuje nazwę i wartość pliku cookie. [Wyrażeń regularnych](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) można używać do definiowania żądanej wartości pliku cookie.

Opcja **Dopasowuje**/**nie pasuje** określa warunki, w których parametr pliku cookie Regex warunek dopasowania jest spełniony.

- **Dopasowania:** Wymaga żądania zawierającego określony plik cookie o wartości odpowiadającej określonej wyrażeniu regularnej.
- **Nie pasuje:** Wymaga, aby żądanie spełniał jeden z następujących kryteriów:
  - Nie zawiera określonego pliku cookie.
  - Zawiera określony plik cookie, ale jego wartość nie jest zgodna z określonym wyrażeniem regularnym.
  
Najważniejsze informacje:

- Nazwa pliku cookie:
  - Ponieważ wyrażenia regularne i wartości symboli wieloznacznych, w tym gwiazdki (*), nie są obsługiwane podczas określania nazwy pliku cookie, tylko dokładne dopasowania nazw plików cookie kwalifikują się do porównania.
  - Tylko jedna nazwa pliku cookie można określić na wystąpienie tego warunku dopasowania.
  - Porównania nazw plików cookie są niewrażliwe na argumenty.
- Wartość pliku cookie:
  - Wartość pliku cookie może korzystać z wyrażeń regularnych.
  - Użyj opcji **Ignoruj wielkość liter,** aby kontrolować, czy porównanie z uwzględnieniem wielkości liter jest dokonywane z wartością pliku cookie żądania.
- Ze względu na sposób śledzenia ustawień pamięci podręcznej ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Pełne wypełnienie pamięci podręcznej
  - Domyślny wewnętrzny wiek maksymalny
  - Wymuszenie wewnętrznego max-age
  - Ignoruj niejmówętną pamięć podręczną origin
  - Wewnętrzny Max-Stale

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="country"></a>Kraj

Kraj można określić za pomocą kodu kraju. 

Opcja **Mecze**/**nie pasuje** określa warunki, w których warunek meczu kraju jest spełniony:

- **Dopasowania:** Wymaga, aby żądanie zawierało określone wartości kodu kraju. 
- **Nie pasuje:** Wymaga, aby żądanie nie zawierało określonych wartości kodu kraju.

Najważniejsze informacje:

- Określ wiele kodów krajów, wyznaczając każdy z nich z jednym spacją.
- Symbole wieloznaczne nie są obsługiwane podczas określania kodu kraju.
- Kody krajów "UE" i "AP" nie obejmują wszystkich adresów IP w tych regionach.
- Niektóre żądania mogą nie zwracać prawidłowego kodu kraju. Znak zapytania (?) będzie zgodny z żądaniami, dla których nie można ustalić prawidłowego kodu kraju.
- W kodach krajów rozróżniana jest wielkość liter.
- Ze względu na sposób śledzenia ustawień pamięci podręcznej ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Pełne wypełnienie pamięci podręcznej
  - Domyślny wewnętrzny wiek maksymalny
  - Wymuszenie wewnętrznego max-age
  - Ignoruj niejmówętną pamięć podręczną origin
  - Wewnętrzny Max-Stale

#### <a name="implementing-country-filtering-by-using-the-rules-engine"></a>Wdrażanie filtrowania kraju przy użyciu aparatu reguł

Ten warunek dopasowania umożliwia wykonywanie wielu dostosowań na podstawie lokalizacji, z której pochodzi żądanie. Na przykład zachowanie funkcji filtrowania kraju można replikować za pomocą następującej konfiguracji:

- Dopasowanie symboli wieloznacznych ścieżki adresu URL: ustaw [warunek dopasowania symboli wieloznacznych ścieżki adresu URL](#url-path-wildcard) do katalogu, który zostanie zabezpieczony. 
    Dołącz gwiazdkę na końcu ścieżki względnej, aby upewnić się, że dostęp do wszystkich jej wiązek podrzędnych będzie ograniczony przez tę regułę.

- Mecz w kraju: ustaw warunek dopasowania kraju na żądany zestaw krajów.
  - Zezwól: Ustaw warunek Dopasowania kraju na **Niepasuje,** aby zezwolić tylko określonym krajom na dostęp do zawartości przechowywanej w lokalizacji zdefiniowanej przez warunek dopasowania symboli wieloznacznych ścieżki url.
  - Blok: Ustaw warunek Dopasowania kraju na **Dopasowania,** aby zablokować określonym krajom dostęp do zawartości przechowywanej w lokalizacji zdefiniowanej przez warunek dopasowania symboli wieloznacznych ścieżki adresu URL.

- Funkcja odmowy dostępu (403): Włącz [funkcję Odmowa dostępu (403),](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) aby replikować część zezwalania lub blokowania funkcji filtrowania kraju.

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="customer-origin"></a>Pochodzenie klienta

Najważniejsze informacje:

- Warunek dopasowania pochodzenia klienta jest spełniony niezależnie od tego, czy zawartość jest żądana za pośrednictwem adresu URL sieci CDN, czy adresu URL CNAME krawędzi, który wskazuje wybrane pochodzenie klienta.
- Nie można usunąć konfiguracji pochodzenia klienta, do którego odwołuje się reguła, ze strony Pochodzenie klienta. Przed podjęciem próby usunięcia konfiguracji pochodzenia klienta upewnij się, że następujące konfiguracje nie odwołują się do niej:
  - Warunek dopasowania pochodzenia klienta
  - Konfiguracja CNAME krawędzi
- Nie używaj instrukcji AND IF, aby połączyć określone warunki dopasowania. Na przykład połączenie warunku dopasowania pochodzenia klienta z warunkiem dopasowania CDN Origin spowoduje utworzenie wzorca dopasowania, który nigdy nie może być dopasowany. Z tego powodu dwóch warunków dopasowania pochodzenia klienta nie można łączyć za pomocą instrukcji AND IF.

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="device"></a>Urządzenie

Warunek dopasowania urządzenia identyfikuje żądania z urządzenia przenośnego na podstawie jego właściwości. Wykrywanie urządzeń mobilnych odbywa się za pośrednictwem [WURFL](http://wurfl.sourceforge.net/). 

Opcja **Dopasowuje**/**nie pasuje** określa warunki, w których warunek dopasowania urządzenia jest spełniony:

- **Dopasowania:** Wymaga urządzenia zowarciem dla użytkownika określonej wartości. 
- **Nie pasuje:** Wymaga, aby urządzenie żądającego nie odpowiadał określonej wartości.

Najważniejsze informacje:

- Użyj opcji **Ignoruj wielkość liter,** aby określić, czy określona wartość jest rozróżniana wielkość liter.
- Ze względu na sposób śledzenia ustawień pamięci podręcznej ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Pełne wypełnienie pamięci podręcznej
  - Domyślny wewnętrzny wiek maksymalny
  - Wymuszenie wewnętrznego max-age
  - Ignoruj niejmówętną pamięć podręczną origin
  - Wewnętrzny Max-Stale

#### <a name="string-type"></a>Typ ciągu

Funkcja WURFL zazwyczaj akceptuje dowolną kombinację cyfr, liter i symboli. Ze względu na elastyczny charakter tej funkcji należy wybrać sposób interpretowania wartości skojarzonej z tym warunkiem dopasowania. W poniższej tabeli opisano dostępny zestaw opcji:

Typ     | Opis
---------|------------
Literału  | Wybierz tę opcję, aby zapobiec nabierania przez większość znaków specjalnego znaczenia przy użyciu ich [wartości literału](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Symbol wieloznaczny | Wybierz tę opcję, aby korzystać ze wszystkich [symboli wieloznacznych]([wartości symboli wieloznacznych](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
Regex    | Wybierz tę opcję, aby używać [wyrażeń regularnych](cdn-verizon-premium-rules-engine-reference.md#regular-expressions). Wyrażenia regularne są przydatne do definiowania wzorca znaków.

#### <a name="wurfl-capabilities"></a>Możliwości WURFL

Funkcja WURFL odnosi się do kategorii opisującej urządzenia przenośne. Wybrana funkcja określa typ opisu urządzenia przenośnego, który jest używany do identyfikowania żądań.

W poniższej tabeli wymieniono możliwości WURFL i ich zmienne dla aparatu reguł.

> [!NOTE]
> Następujące zmienne są obsługiwane w **funkcji Modyfikuj nagłówek żądania klienta** i **modyfikuj nagłówek odpowiedzi klienta.**

Możliwości | Zmienna | Opis | Przykładowe wartości
-----------|----------|-------------|----------------
Nazwa marki | %{wurfl_cap_brand_name} | Ciąg wskazujący nazwę marki urządzenia. | Samsung
System operacyjny urządzenia | %{wurfl_cap_device_os} | Ciąg wskazujący system operacyjny zainstalowany na urządzeniu. | System iOS
Wersja systemu operacyjnego urządzenia | %{wurfl_cap_device_os_version} | Ciąg wskazujący numer wersji systemu operacyjnego zainstalowanego na urządzeniu. | 1.0.1
Podwójna orientacja | %{wurfl_cap_dual_orientation} | Wartość logiczna wskazująca, czy urządzenie obsługuje podwójną orientację. | true
Preferowany kod DTD HTML | %{wurfl_cap_html_preferred_dtd} | Ciąg wskazujący preferowaną definicję typu dokumentu (DTD) urządzenia przenośnego dla zawartości HTML. | brak<br/>xhtml_basic<br/>html5
Inlining obrazu | %{wurfl_cap_image_inlining} | Wartość logiczna wskazująca, czy urządzenie obsługuje obrazy zakodowane w kodowanym base64. | false
Jest Android | %{wurfl_vcap_is_android} | Wartość logiczna, która wskazuje, czy urządzenie korzysta z systemu operacyjnego Android. | true
Czy iOS | %{wurfl_vcap_is_ios} | Wartość logiczna wskazująca, czy urządzenie korzysta z systemu iOS. | false
Czy Smart TV | %{wurfl_cap_is_smarttv} | Wartość logiczna wskazująca, czy urządzenie jest telewizorem Smart TV. | false
Czy smartphone | %{wurfl_vcap_is_smartphone} | Wartość logiczna wskazująca, czy urządzenie jest smartfonem. | true
Czy tablet | %{wurfl_cap_is_tablet} | Wartość logiczna wskazująca, czy urządzenie jest tabletem. Ten opis jest niezależny od systemu operacyjnego. | true
Czy urządzenie bezprzewodowe | %{wurfl_cap_is_wireless_device} | Wartość logiczna wskazująca, czy urządzenie jest uważane za urządzenie bezprzewodowe. | true
Nazwa marketingowa | %{wurfl_cap_marketing_name} | Ciąg wskazujący nazwę marketingową urządzenia. | BlackBerry 8100 Perła
Przeglądarka mobilna | %{wurfl_cap_mobile_browser} | Ciąg wskazujący przeglądarkę, która jest używana do żądania zawartości z urządzenia. | Chrome
Wersja przeglądarki mobilnej | %{wurfl_cap_mobile_browser_version} | Ciąg wskazujący wersję przeglądarki, która jest używana do żądania zawartości z urządzenia. | 31
Nazwa modelu | %{wurfl_cap_model_name} | Ciąg wskazujący nazwę modelu urządzenia. | s3
Pobieranie progresywne | %{wurfl_cap_progressive_download} | Wartość logiczna wskazująca, czy urządzenie obsługuje odtwarzanie dźwięku i wideo podczas pobierania. | true
Data wydania | %{wurfl_cap_release_date} | Ciąg wskazujący rok i miesiąc, w którym urządzenie zostało dodane do bazy danych WURFL.<br/><br/>Formacie:`yyyy_mm` | 2013_december
Wysokość rozdzielczości | %{wurfl_cap_resolution_height} | Liczba całkowita wskazująca wysokość urządzenia w pikselach. | 768
Szerokość rozdzielczości | %{wurfl_cap_resolution_width} | Liczba całkowita wskazująca szerokość urządzenia w pikselach. | 1024

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="edge-cname"></a>Nazwa Cname krawędzi

Najważniejsze informacje:

- Lista dostępnych CNAME krawędzi jest ograniczona do tych CNAMEs krawędzi, które zostały skonfigurowane na stronie CNAMEs krawędzi dla platformy, na której jest konfigurowany aparat reguł.
- Przed podjęciem próby usunięcia konfiguracji CNAME krawędzi, upewnij się, że warunek dopasowania Cname krawędzi nie odwołuje się do niego. Nie można usunąć konfiguracji CNAME krawędzi, które zostały zdefiniowane w regule ze strony Edge CNAMEs.
- Nie używaj instrukcji AND IF, aby połączyć określone warunki dopasowania. Na przykład połączenie warunku dopasowania Edge Cname z warunkiem dopasowania pochodzenia klienta spowoduje utworzenie wzorca dopasowania, który nigdy nie może być dopasowany. Z tego powodu dwóch warunków dopasowania Cname krawędzi nie można łączyć za pomocą instrukcji AND IF.
- Ze względu na sposób śledzenia ustawień pamięci podręcznej ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Pełne wypełnienie pamięci podręcznej
  - Domyślny wewnętrzny wiek maksymalny
  - Wymuszenie wewnętrznego max-age
  - Ignoruj niejmówętną pamięć podręczną origin
  - Wewnętrzny Max-Stale

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="referring-domain"></a>Domena odsyłacająca

Nazwa hosta skojarzona z odsyłaczem, za pośrednictwem którego żądano zawartości, określa, czy warunek domeny odsyłacza jest spełniony.

Opcja **Dopasuje**/**nie pasuje** określa warunki, w których warunek dopasowania domeny odsyłacej jest spełniony:

- **Dopasowania:** Wymaga nazwy hosta odwołującego się do określonych wartości. 
- **Nie pasuje:** Wymaga, aby nazwa hosta odwołującego się nie była zgodna z określoną wartością.

Najważniejsze informacje:

- Określ wiele nazw hostów, rozdzielając każdy z nich z jednym spacją.
- Ten warunek dopasowania obsługuje [wartości symboli wieloznacznych](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- Jeśli określona wartość nie zawiera gwiazdki, musi być dokładne dopasowanie dla nazwy hosta odsyłacza. Na przykład określenie "mydomain.com" nie byłoby zgodne z "www.mydomain.com".
- Użyj **opcji Ignoruj wielkość liter,** aby kontrolować, czy jest dokonywane porównanie z uwzględnieniem wielkości liter.
- Ze względu na sposób śledzenia ustawień pamięci podręcznej ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Pełne wypełnienie pamięci podręcznej
  - Domyślny wewnętrzny wiek maksymalny
  - Wymuszenie wewnętrznego max-age
  - Ignoruj niejmówętną pamięć podręczną origin
  - Wewnętrzny Max-Stale

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-literal"></a>Dosłowny nagłówek żądania

Opcja **Dopasowuje**/**nie pasuje** określa warunki, w których warunek dopasowania dosłowny nagłówka żądania jest spełniony.

- **Dopasowania:** Wymaga, aby żądanie zawierało określony nagłówek. Jego wartość musi być zgodna z tą, która jest zdefiniowana w tym warunku dopasowania.
- **Nie pasuje:** Wymaga, aby żądanie spełniał jeden z następujących kryteriów:
  - Nie zawiera określonego nagłówka.
  - Zawiera określony nagłówek, ale jego wartość nie jest zgodna z tą, która jest zdefiniowana w tym warunku dopasowania.
  
Najważniejsze informacje:

- Porównania nazw nagłówków są zawsze niewrażliwe na wielkości liter. Użyj opcji **Ignoruj wielkość liter,** aby kontrolować wielkość liter porównania wartości nagłówka.
- Ze względu na sposób śledzenia ustawień pamięci podręcznej ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Pełne wypełnienie pamięci podręcznej
  - Domyślny wewnętrzny wiek maksymalny
  - Wymuszenie wewnętrznego max-age
  - Ignoruj niejmówętną pamięć podręczną origin
  - Wewnętrzny Max-Stale

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-regex"></a>Żądanie Regex nagłówka

Opcja **Dopasowuje**/**nie pasuje** określa warunki, w których warunek dopasowania Regex nagłówka żądania jest spełniony.

- **Dopasowania:** Wymaga, aby żądanie zawierało określony nagłówek. Jego wartość musi być zgodna ze wzorcem zdefiniowanym w określonym [wyrażeniu regularnym](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).
- **Nie pasuje:** Wymaga, aby żądanie spełniał jeden z następujących kryteriów:
  - Nie zawiera określonego nagłówka.
  - Zawiera określony nagłówek, ale jego wartość nie jest zgodna z określonym wyrażeniem regularnym.

Najważniejsze informacje:

- Nazwa nagłówka:
  - Porównania nazw nagłówków są niewrażliwe na wielkości liter.
  - Zastąp spacje w nazwie nagłówka na "%20".
- Wartość nagłówka:
  - Wartość nagłówka może korzystać z wyrażeń regularnych.
  - Użyj opcji **Ignoruj wielkość liter,** aby kontrolować wielkość liter porównania wartości nagłówka.
  - Warunek dopasowania jest spełniony tylko wtedy, gdy wartość nagłówka dokładnie odpowiada co najmniej jeden z określonych wzorców.
- Ze względu na sposób śledzenia ustawień pamięci podręcznej ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Pełne wypełnienie pamięci podręcznej
  - Domyślny wewnętrzny wiek maksymalny
  - Wymuszenie wewnętrznego max-age
  - Ignoruj niejmówętną pamięć podręczną origin
  - Wewnętrzny Max-Stale

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-header-wildcard"></a>Symbol wieloznaczny nagłówka żądania

Opcja **Dopasowuje**/**nie pasuje** określa warunki, w których warunek dopasowania symboli wieloznacznych nagłówka żądania jest spełniony.

- **Dopasowania:** Wymaga, aby żądanie zawierało określony nagłówek. Jego wartość musi być zgodna z co najmniej jedną z wartości, które są zdefiniowane w tym warunku dopasowania.
- **Nie pasuje:** Wymaga, aby żądanie spełniał jeden z następujących kryteriów:
  - Nie zawiera określonego nagłówka.
  - Zawiera określony nagłówek, ale jego wartość nie pasuje do żadnej z określonych wartości.
  
Najważniejsze informacje:

- Nazwa nagłówka:
  - Porównania nazw nagłówków są niewrażliwe na wielkości liter.
  - Spacje w nazwie nagłówka należy zastąpić "%20". Można również użyć tej wartości, aby określić spacje w wartości nagłówka.
- Wartość nagłówka:
  - Wartość nagłówka może korzystać z [wartości symboli wieloznacznych](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Użyj opcji **Ignoruj wielkość liter,** aby kontrolować wielkość liter porównania wartości nagłówka.
  - Ten warunek dopasowania jest spełniony, gdy wartość nagłówka dokładnie odpowiada co najmniej jednemu z określonych wzorców.
  - Określ wiele wartości, rozdzielając każdą z nich pojedynczą spacją.
- Ze względu na sposób śledzenia ustawień pamięci podręcznej ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Pełne wypełnienie pamięci podręcznej
  - Domyślny wewnętrzny wiek maksymalny
  - Wymuszenie wewnętrznego max-age
  - Ignoruj niejmówętną pamięć podręczną origin
  - Wewnętrzny Max-Stale

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-method"></a>Request, metoda

Warunek dopasowania metody żądania jest spełniony tylko wtedy, gdy zasoby są wymagane za pośrednictwem wybranej metody żądania. Dostępne metody żądania to:

- GET
- HEAD
- POST
- Opcje
- PUT
- DELETE
- TRACE
- Połączyć

Najważniejsze informacje:

- Domyślnie tylko metoda żądania GET może generować zawartość buforowaną w sieci. Wszystkie inne metody żądania są przesuwają się za pośrednictwem sieci.
- Ze względu na sposób śledzenia ustawień pamięci podręcznej ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Pełne wypełnienie pamięci podręcznej
  - Domyślny wewnętrzny wiek maksymalny
  - Wymuszenie wewnętrznego max-age
  - Ignoruj niejmówętną pamięć podręczną origin
  - Wewnętrzny Max-Stale

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-scheme"></a>Schemat żądania

Warunek dopasowania schematu żądania jest spełniony tylko wtedy, gdy zasoby są wymagane za pośrednictwem wybranego protokołu. Dostępne protokoły to:

- HTTP
- HTTPS

Najważniejsze informacje:

- Ze względu na sposób śledzenia ustawień pamięci podręcznej ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Pełne wypełnienie pamięci podręcznej
  - Domyślny wewnętrzny wiek maksymalny
  - Wymuszenie wewnętrznego max-age
  - Ignoruj niejmówętną pamięć podręczną origin
  - Wewnętrzny Max-Stale

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-directory"></a>Katalog ścieżki adresu URL

Identyfikuje żądanie według jego ścieżki względnej, która wyklucza nazwę pliku żądanego zasobu.

Opcja **Dopasowuje**/**nie pasuje** określa warunki, w których adres URL Path Directory warunek dopasowania jest spełniony.

- **Dopasowania:** Wymaga, aby żądanie zawierało względną ścieżkę adresu URL, z wyłączeniem nazwy pliku, która pasuje do określonego wzorca adresu URL.
- **Nie pasuje:** Wymaga, aby żądanie zawierało względną ścieżkę adresu URL, z wyłączeniem nazwy pliku, która nie jest zgodna z określonym wzorcem adresu URL.

Najważniejsze informacje:

- Użyj opcji **Względem, aby** określić, czy porównanie adresów URL rozpoczyna się przed punktem dostępu do zawartości, czy po niej. Punkt dostępu do zawartości jest częścią ścieżki, która pojawia się między nazwa hosta Verizon CDN i względną ścieżką do żądanego zasobu (na przykład /800001/CustomerOrigin). Identyfikuje lokalizację według typu serwera (na przykład cdn lub pochodzenia klienta) i numer konta klienta.

   Dla opcji **Względem do** dostępne są następujące wartości:
  - **Katalog główny**: Wskazuje, że punkt porównania adresów URL rozpoczyna się bezpośrednio po nazwach hosta sieci CDN. 

  Na przykład:\/http: /wpc.0001. &lt;domena&gt;/**800001/myorigin/myfolder**/index.htm

  - **Pochodzenie**: Wskazuje, że punkt porównania adresu URL rozpoczyna się po punkcie dostępu do zawartości (na przykład /000001 lub /800001/myorigin). Ponieważ \*azureedge.net CNAME jest tworzony względem katalogu pochodzenia na Verizon CDN nazwa hosta domyślnie, użytkownicy usługi Azure CDN należy użyć wartości **pochodzenia.** 

  \//&lt;Na przykład: https:&gt;punkt końcowy .azureedge.net/**myfolder**/index.htm 

  Ten adres URL wskazuje następującą nazwa hosta\/Verizon CDN: http: /wpc.0001. &lt;domena&gt;/800001/myorigin/**myfolder**/index.htm

- Adres URL CNAME krawędzi jest przepisywany do adresu URL sieci CDN przed porównaniem adresu URL.

    Na przykład oba poniższe adresy URL wskazują ten sam zasób i dlatego mają tę samą ścieżkę adresu URL.
  - Adres URL cdn: http:\//wpc.0001. &lt;domena&gt;/800001/CustomerOrigin/path/asset.htm
    
  - Adres URL CNAME\//&lt;krawędzi:&gt;http: endpoint .azureedge.net/path/asset.htm
    
    Informacje dodatkowe:
  - Domena niestandardowa:\/https: /my.domain.com/path/asset.htm
    
    - Ścieżka adresu URL (względem katalogu głównego): /800001/CustomerOrigin/path/
    
    - Ścieżka adresu URL (względem pochodzenia): /path/

- Część adresu URL używana do porównywania adresów URL kończy się tuż przed nazwą pliku żądanego zasobu. Ukośnik do przodu jest ostatnim znakiem w tego typu ścieżce.

- Zastąp wszystkie spacje we wzorcu ścieżki adresu URL na "%20".

- Każdy wzorzec ścieżki adresu URL może zawierać jedną lub więcej gwiazdek (*), gdzie każda gwiazdka pasuje do sekwencji jednego lub więcej znaków.

- Określ wiele ścieżek URL we wzorcu, rozdzielając każdą z nich z jedną spacją.

    Na przykład: */sales/ */marketing/

- Specyfikacja ścieżki adresu URL może korzystać z [wartości symboli wieloznacznych](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Użyj opcji **Ignoruj wielkość liter,** aby kontrolować, czy jest wykonywane porównanie z uwzględnieniem wielkości liter.

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-extension"></a>Rozszerzenie ścieżki adresu URL

Identyfikuje żądania przez rozszerzenie pliku żądanego zasobu.

Opcja **Dopasowuje**/**nie pasuje** określa warunki, w których warunek dopasowania rozszerzenia ścieżki url jest spełniony.

- **Dopasowania:** Wymaga, aby adres URL żądania zawierał rozszerzenie pliku, które dokładnie odpowiada określonej szyku.

   Na przykład, jeśli określisz zasoby "htm", "htm", ale nie zasoby "html".  

- **Nie pasuje:** Wymaga, aby żądanie adresu URL zawierało rozszerzenie pliku, które nie pasuje do określonego wzorca.

Najważniejsze informacje:

- Określ rozszerzenia plików, które mają być zgodne w polu **Wartość.** Nie należy uwzględniać okresu wiodącego; na przykład użyj htm zamiast .htm.

- Użyj opcji **Ignoruj wielkość liter,** aby kontrolować, czy jest wykonywane porównanie z uwzględnieniem wielkości liter.

- Określ wiele rozszerzeń plików, rozdzielając każde rozszerzenie za pomocą jednego miejsca. 

    Na przykład: htm html

- Na przykład określenie "htm" pasuje do zasobów "htm", ale nie do zasobów "html".

#### <a name="sample-scenario"></a>Przykładowy scenariusz

Następująca konfiguracja przykładowa zakłada, że ten warunek dopasowania jest spełniony, gdy żądanie pasuje do jednego z określonych rozszerzeń.

Specyfikacja wartości: asp aspx php html

Ten warunek dopasowania jest spełniony, gdy znajdzie adresy URL, które kończą się następującymi rozszerzeniami:

- .asp
- .aspx
- .php
- .html,

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-filename"></a>Nazwa pliku ścieżki adresu URL

Identyfikuje żądania według nazwy pliku żądanego zasobu. Na potrzeby tego warunku dopasowania nazwa pliku składa się z nazwy żądanego zasobu, kropki i rozszerzenia pliku (na przykład index.html).

Opcja **Dopasowuje**/**nie pasuje** określa warunki, w których adres URL Path Filename match condition is met.

- **Dopasowania:** Wymaga, aby żądanie zawierało nazwę pliku w ścieżce adresu URL, która pasuje do określonego wzorca.
- **Nie pasuje:** Wymaga, aby żądanie zawierało nazwę pliku w ścieżce adresu URL, która nie jest zgodna z określonym wzorcem.

Najważniejsze informacje:

- Użyj opcji **Ignoruj wielkość liter,** aby kontrolować, czy jest wykonywane porównanie z uwzględnieniem wielkości liter.

- Aby określić wiele rozszerzeń plików, należy oddzielić każde rozszerzenie jednym miejscem.

    Na przykład: index.htm index.html

- Zamień spacje w wartości nazwy pliku na "%20".

- Wartość nazwy pliku może korzystać z [wartości symboli wieloznacznych](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Na przykład każdy wzorzec nazwy pliku może składać się z jednej lub więcej gwiazdek (*), gdzie każda gwiazdka pasuje do sekwencji jednego lub więcej znaków.

- Jeśli znaki wieloznaczne nie są określone, to tylko dokładne dopasowanie spełni ten warunek dopasowania.

    Na przykład określenie "presentation.ppt" pasuje do zasobu o nazwie "presentation.ppt", ale nie do zasobu o nazwie "presentation.pptx".

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-literal"></a>Literał ścieżki adresu URL

Porównuje ścieżkę adresu URL żądania, w tym nazwę pliku, z określoną wartością.

Opcja **Dopasowuje**/**nie pasuje** określa warunki, w których adres URL Ścieżka dosłowne warunek dopasowania jest spełniony.

- **Dopasowania:** Wymaga, aby żądanie zawierało ścieżkę adresu URL, która pasuje do określonego wzorca.
- **Nie pasuje:** Wymaga, aby żądanie zawierało ścieżkę adresu URL, która nie jest zgodna z określonym wzorcem.

Najważniejsze informacje:

- Użyj opcji **Względem, aby** określić, czy punkt porównania adresu URL rozpoczyna się przed lub po punkcie dostępu do zawartości. 

    Dla opcji **Względem do** dostępne są następujące wartości:
  - **Katalog główny**: Wskazuje, że punkt porównania adresów URL rozpoczyna się bezpośrednio po nazwach hosta sieci CDN.

    Na przykład:\/http: /wpc.0001. &lt;&gt;/**800001/myorigin/myfolder/index.htm** domen

  - **Pochodzenie**: Wskazuje, że punkt porównania adresu URL rozpoczyna się po punkcie dostępu do zawartości (na przykład /000001 lub /800001/myorigin). Ponieważ \*azureedge.net CNAME jest tworzony względem katalogu pochodzenia na Verizon CDN nazwa hosta domyślnie, użytkownicy usługi Azure CDN należy użyć wartości **pochodzenia.** 

    \//&lt;Na przykład: https:&gt;punkt końcowy .azureedge.net/**myfolder/index.htm**

  Ten adres URL wskazuje następującą nazwa hosta\/Verizon CDN: http: /wpc.0001. &lt;domena&gt;/800001/myorigin/**myfolder/index.htm**

- Adres URL CNAME krawędzi jest przepisywany do adresu URL sieci CDN przed porównaniem adresu URL.

Na przykład oba poniższe adresy URL wskazują ten sam zasób i dlatego mają tę samą ścieżkę adresu URL:

- Adres URL cdn: http:\//wpc.0001. &lt;domena&gt;/800001/CustomerOrigin/path/asset.htm
- Adres URL CNAME\//&lt;krawędzi:&gt;http: endpoint .azureedge.net/path/asset.htm

    Informacje dodatkowe:
    
    - Ścieżka adresu URL (względem katalogu głównego): /800001/CustomerOrigin/path/asset.htm
   
    - Ścieżka adresu URL (względem pochodzenia): /path/asset.htm

- Ciągi zapytań w adresie URL są ignorowane.
- Użyj opcji **Ignoruj wielkość liter,** aby kontrolować, czy jest wykonywane porównanie z uwzględnieniem wielkości liter.
- Wartość określona dla tego warunku dopasowania jest porównywana ze ścieżką względną dokładnego żądania złożonego przez klienta.

- Aby dopasować wszystkie żądania do określonego katalogu, użyj [katalogu ścieżki adresu URL](#url-path-directory) lub warunku dopasowania [symboli wieloznacznych ścieżki adresu URL.](#url-path-wildcard)

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-regex"></a>Ścieżka adresu URL Regex

Porównuje ścieżkę adresu URL żądania z określonym [wyrażeniem regularnym](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

Opcja **Dopasowuje**/**nie pasuje** określa warunki, w których adres URL Path Regex warunek dopasowania jest spełniony.

- **Dopasowania:** Wymaga, aby żądanie zawierało ścieżkę adresu URL, która pasuje do określonego wyrażenia regularnego.
- **Nie pasuje:** Wymaga, aby żądanie zawierało ścieżkę adresu URL, która nie jest zgodna z określonym wyrażeniem regularnym.

Najważniejsze informacje:

- Adres URL CNAME krawędzi jest przepisywany do adresu URL sieci CDN przed porównaniem adresów URL.

    Na przykład oba adresy URL wskazują ten sam zasób i dlatego mają tę samą ścieżkę adresu URL.

     - Adres URL cdn: http:\//wpc.0001. &lt;domena&gt;/800001/CustomerOrigin/path/asset.htm

     - Adres URL nazwy CNAME krawędzi: http:\//my.domain.com/path/asset.htm

    Informacje dodatkowe:
    
     - Ścieżka adresu URL: /800001/CustomerOrigin/path/asset.htm

- Ciągi zapytań w adresie URL są ignorowane.
    
- Użyj opcji **Ignoruj wielkość liter,** aby kontrolować, czy jest wykonywane porównanie z uwzględnieniem wielkości liter.
    
- Spacje w ścieżce adresu URL powinny zostać zastąpione wartością "%20".

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-wildcard"></a>Symbol wieloznaczny ścieżki adresu URL

Porównuje ścieżkę względnego adresu URL żądania z określonym wzorcem symboli wieloznacznych.

Opcja **Dopasowuje**/**nie pasuje** określa warunki, w których warunek dopasowania symboli wieloznacznych ścieżki URL jest spełniony.

- **Dopasowania:** Wymaga, aby żądanie zawierało ścieżkę adresu URL, która pasuje do określonego wzorca symboli wieloznacznych.
- **Nie pasuje:** Wymaga, aby żądanie zawierało ścieżkę adresu URL, która nie jest zgodna z określonym wzorcem symboli wieloznacznych.

Najważniejsze informacje:

- **Względem** opcji: Ta opcja określa, czy punkt porównania adresu URL rozpoczyna się przed lub po punkcie dostępu do zawartości.

   Ta opcja może mieć następujące wartości:
     - **Katalog główny**: Wskazuje, że punkt porównania adresów URL rozpoczyna się bezpośrednio po nazwach hosta sieci CDN.

       Na przykład:\/http: /wpc.0001. &lt;&gt;/**800001/myorigin/myfolder/index.htm** domen

     - **Pochodzenie**: Wskazuje, że punkt porównania adresu URL rozpoczyna się po punkcie dostępu do zawartości (na przykład /000001 lub /800001/myorigin). Ponieważ \*azureedge.net CNAME jest tworzony względem katalogu pochodzenia na Verizon CDN nazwa hosta domyślnie, użytkownicy usługi Azure CDN należy użyć wartości **pochodzenia.** 

       \//&lt;Na przykład: https:&gt;punkt końcowy .azureedge.net/**myfolder/index.htm**

     Ten adres URL wskazuje następującą nazwa hosta\/Verizon CDN: http: /wpc.0001. &lt;domena&gt;/800001/myorigin/**myfolder/index.htm**

- Adres URL CNAME krawędzi jest przepisywany do adresu URL sieci CDN przed porównaniem adresów URL.

    Na przykład oba poniższe adresy URL wskazują ten sam zasób i dlatego mają tę samą ścieżkę adresu URL:
     - Adres URL http://wpc.0001.&ltsieci CDN:&gt;;dawis /800001/CustomerOrigin/path/asset.htm
     - Adres URL CNAME\//&lt;krawędzi:&gt;http: endpoint .azureedge.net/path/asset.htm
    
    Informacje dodatkowe:
    
     - Ścieżka adresu URL (względem katalogu głównego): /800001/CustomerOrigin/path/asset.htm
    
     - Ścieżka adresu URL (względem pochodzenia): /path/asset.htm
    
- Określ wiele ścieżek url, wyznaczając każdy z nich z jednej spacji.

   Na przykład: /marketing/asset.* /sales/*.htm

- Ciągi zapytań w adresie URL są ignorowane.
    
- Użyj opcji **Ignoruj wielkość liter,** aby kontrolować, czy jest wykonywane porównanie z uwzględnieniem wielkości liter.
    
- Zamień spacje w ścieżce adresu URL na "%20".
    
- Wartość określona dla ścieżki adresu URL może korzystać z [wartości symboli wieloznacznych](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Każdy wzorzec ścieżki adresu URL może zawierać jedną lub więcej gwiazdek (*), gdzie każda gwiazdka może odpowiadać sekwencji jednego lub więcej znaków.

#### <a name="sample-scenarios"></a>Przykładowe scenariusze

Przykładowe konfiguracje w poniższej tabeli zakładają, że ten warunek dopasowania jest spełniony, gdy żądanie pasuje do określonego wzorca adresu URL:

Wartość                   | W stosunku do    | Wynik 
------------------------|----------------|-------
*/test.html */test.php  | Korzeń lub pochodzenie | Ten wzorzec jest dopasowywał żądania dotyczące zasobów o nazwie "test.html" lub "test.php" w dowolnym folderze.
/80ABCD/pochodzenie/tekst/*   | Główny           | Ten wzorzec jest dopasowywał się, gdy żądany zasób spełnia następujące kryteria: <br />- Musi znajdować się na pochodzeniu klienta o nazwie "pochodzenie". <br />- Ścieżka względna musi zaczynać się od folderu o nazwie "tekst". Oznacza to, że żądany zasób może przebywać w folderze "tekst" lub w jednym z jego cyklicznych podfolderów.
*/css/* */js/*          | Korzeń lub pochodzenie | Ten wzorzec jest dopasowywał wszystkie adresy URL CNAME sieci CDN lub krawędzi, które zawierają folder css lub js.
*.jpg *.gif *.png       | Korzeń lub pochodzenie | Ten wzorzec jest dopasowyany do wszystkich adresów URL sieci CDN lub CNAME zakończonych jpg, gif lub png. Alternatywnym sposobem określenia tego wzorca jest [warunek dopasowania rozszerzenia ścieżki adresu URL](#url-path-extension).
/images/* /media/*      | Origin         | Ten wzorzec jest dopasowywał adresy URL sieci CDN lub CNAME krawędzi, których ścieżka względna zaczyna się od folderu "obrazy" lub "media". <br />- Adres URL CDN: http:\//wpc.0001. &lt;domena&gt;/800001/myorigin/images/sales/event1.png<br />- Przykładowy adres URL CNAME krawędzi: http:\//cdn.mydomain.com/images/sales/event1.png

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-literal"></a>Literał zapytania adresu URL

Porównuje ciąg zapytania żądania z określoną wartością.

Opcja **Dopasowuje**/**nie pasuje** określa warunki, w których adres URL zapytanie dosłowne warunek dopasowania jest spełniony.

- **Dopasowania:** Wymaga, aby żądanie zawierało ciąg zapytania url, który pasuje do określonego ciągu zapytania.
- **Nie pasuje:** Wymaga, aby żądanie zawierało ciąg zapytania adresu URL, który nie jest zgodny z określonym ciągiem zapytania.

Najważniejsze informacje:

- Tylko dokładne dopasowanie ciągu zapytania spełnia ten warunek dopasowania.
    
- Użyj opcji **Ignoruj wielkość liter,** aby kontrolować wielkość liter porównania ciągów zapytań.
    
- Nie należy dołączać wiodącego znaku zapytania (?) w tekście wartości ciągu zapytania.
    
- Niektóre znaki wymagają kodowania adresów URL. Użyj symbolu procentowego, aby zakodować adres URL następujących znaków:

   Znak | Kodowanie adresów URL
   ----------|---------
   Miejsce     | %20
   &         | %25

- Ze względu na sposób śledzenia ustawień pamięci podręcznej ten warunek dopasowania jest niezgodny z następującymi funkcjami:
   - Pełne wypełnienie pamięci podręcznej
   - Domyślny wewnętrzny wiek maksymalny
   - Wymuszenie wewnętrznego max-age
   - Ignoruj niejmówętną pamięć podręczną origin
   - Wewnętrzny Max-Stale

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-parameter"></a>Parametr kwerendy url

Identyfikuje żądania, które zawierają określony parametr ciągu kwerendy. Ten parametr jest ustawiony na wartość, która pasuje do określonego wzorca. Parametry ciągu kwerendy (na przykład parametr=wartość) w adresie URL żądania określają, czy ten warunek jest spełniony. Ten warunek dopasowania identyfikuje parametr ciągu zapytania według jego nazwy i akceptuje jedną lub więcej wartości dla wartości parametru. 

Opcja **Dopasowuje**/**nie pasuje** określa warunki, w których warunek dopasowania parametru kwerendy URL jest spełniony.

- **Dopasowania:** Wymaga żądania zawierają określony parametr z wartością, która odpowiada co najmniej jednej z wartości, które są zdefiniowane w tym warunku dopasowania.
- **Nie pasuje:** Wymaga, aby żądanie spełniał jeden z następujących kryteriów:
  - Nie zawiera określonego parametru.
  - Zawiera określony parametr, ale jego wartość nie pasuje do żadnej z wartości, które są zdefiniowane w tym warunku dopasowania.

Ten warunek dopasowania umożliwia łatwe określenie kombinacji nazwy/wartości parametrów. Aby uzyskać większą elastyczność, jeśli dopasowujesz parametr ciągu zapytania, należy rozważyć użycie warunku dopasowania [symboli wieloznacznych kwerendy url.](#url-query-wildcard)

Najważniejsze informacje:

- Tylko pojedyncza nazwa parametru kwerendy adresu URL może być określona na wystąpienie tego warunku dopasowania.
    
- Ponieważ wartości symboli wieloznacznych nie są obsługiwane, gdy określono nazwę parametru, tylko dokładne dopasowanie nazwy parametru kwalifikuje się do porównania.
- Wartość parametrów może zawierać [wartości symboli wieloznacznych](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
   - Każdy wzorzec wartości parametru może składać się z jednej lub więcej gwiazdek (*), gdzie każda gwiazdka może odpowiadać sekwencji jednego lub więcej znaków.
   - Niektóre znaki wymagają kodowania adresów URL. Użyj symbolu procentowego, aby zakodować adres URL następujących znaków:

       Znak | Kodowanie adresów URL
       ----------|---------
       Miejsce     | %20
       &         | %25

- Określ wiele wartości parametrów ciągu kwerendy, rozdzielając każdy z nich z jedną spacją. Ten warunek dopasowania jest spełniony, gdy żądanie zawiera jedną z kombinacji określonej nazwy/wartości.

   - Przykład 1:

     - Konfiguracja:

       Wartość ValueAB

     - Ta konfiguracja jest zgodna z następującymi parametrami ciągu kwerendy:

       Parametr1=WartośćA
    
       Parametr1=WartośćB

   - Przykład 2:

     - Konfiguracja: 

        Wartość%20A Wartość%20B

     - Ta konfiguracja jest zgodna z następującymi parametrami ciągu kwerendy:

       Parametr1=Wartość%20A

       Parametr1=Wartość%20B

- Ten warunek dopasowania jest spełniony tylko wtedy, gdy istnieje dokładne dopasowanie do co najmniej jednej z kombinacji nazwy/wartości ciągu kwerendy.

   Na przykład jeśli używasz konfiguracji w poprzednim przykładzie, kombinacja nazwa parametru/wartość "Parameter1=ValueAdd" nie będzie uważana za dopasowanie. Jeśli jednak określisz jedną z następujących wartości, będzie ona zgodna z tą kombinacją nazwa/wartość:

   - Wartość ValueA ValueBAdd
   - Wartość* ValueB

- Użyj opcji **Ignoruj wielkość liter,** aby kontrolować wielkość liter porównania ciągów zapytań.
    
- Ze względu na sposób śledzenia ustawień pamięci podręcznej ten warunek dopasowania jest niezgodny z następującymi funkcjami:
   - Pełne wypełnienie pamięci podręcznej
   - Domyślny wewnętrzny wiek maksymalny
   - Wymuszenie wewnętrznego max-age
   - Ignoruj niejmówętną pamięć podręczną origin
   - Wewnętrzny Max-Stale

#### <a name="sample-scenarios"></a>Przykładowe scenariusze

Poniższy przykład pokazuje, jak ta opcja działa w określonych sytuacjach:

Nazwa  | Wartość |  Wynik
------|-------|--------
Użytkownik  | Jan   | Ten wzorzec jest dopasowywał się, gdy ciąg zapytania żądanego adresu URL to "?user=joe".
Użytkownik  | *     | Ten wzorzec jest dopasowywał się, gdy ciąg zapytania dla żądanego adresu URL zawiera parametr Użytkownika.
Adres e-mail | Jan\* | Ten wzorzec jest dopasowywał się, gdy ciąg zapytania dla żądanego adresu URL zawiera parametr Email, który zaczyna się od "Joe".

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-regex"></a>Regex kwerendy URL

Identyfikuje żądania, które zawierają określony parametr ciągu kwerendy. Ten parametr jest ustawiony na wartość odpowiadającą określonej [wyrażeniu regularnej](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

Opcja **Dopasowuje**/**nie pasuje** określa warunki, w których adres URL query Regex warunek dopasowania jest spełniony.

- **Dopasowania:** Wymaga, aby żądanie zawierało ciąg zapytania url, który pasuje do określonego wyrażenia regularnego.
- **Nie pasuje:** Wymaga, aby żądanie zawierało ciąg zapytania adresu URL, który nie jest zgodny z określonym wyrażeniem regularnym.

Najważniejsze informacje:

- Tylko dokładne dopasowania do określonego wyrażenia regularnego spełniają ten warunek dopasowania.
    
- Użyj opcji **Ignoruj wielkość liter,** aby kontrolować wielkość liter porównania ciągów zapytań.
    
- Na potrzeby tej opcji ciąg zapytania rozpoczyna się od pierwszego znaku po ograniczniku znaku zapytania (?) dla ciągu zapytania.
    
- Niektóre znaki wymagają kodowania adresów URL. Użyj symbolu procentowego, aby zakodować adres URL następujących znaków:

   Znak | Kodowanie adresów URL | Wartość
   ----------|--------------|------
   Miejsce     | %20          | \%20
   &         | %25          | \%25

   Należy zauważyć, że symbole procentowe muszą być zmienione.

- Podwójne ucieczki znaków specjalnych wyrażeń regularnych (na przykład \^$.+), aby uwzględnić ukośnik odwrotny w wyrażeniu regularnym.

   Przykład:

   Wartość | Interpretowane jako 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Ze względu na sposób śledzenia ustawień pamięci podręcznej ten warunek dopasowania jest niezgodny z następującymi funkcjami:
   - Pełne wypełnienie pamięci podręcznej
   - Domyślny wewnętrzny wiek maksymalny
   - Wymuszenie wewnętrznego max-age
   - Ignoruj niejmówętną pamięć podręczną origin
   - Wewnętrzny Max-Stale

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-wildcard"></a>Symbol wieloznaczny kwerendy URL

Porównuje określone wartości z ciągiem zapytania żądania.

Opcja **Dopasowuje**/**nie pasuje** określa warunki, w których adres URL kwerendy wildcard warunek dopasowania jest spełniony.

- **Dopasowania:** Wymaga, aby żądanie zawierało ciąg zapytania adresu URL, który odpowiada określonej wartości symboli wieloznacznych.
- **Nie pasuje:** Wymaga, aby żądanie zawierało ciąg zapytania adresu URL, który nie jest zgodny z określoną wartością symboli wieloznacznych.

Najważniejsze informacje:

- Na potrzeby tej opcji ciąg zapytania rozpoczyna się od pierwszego znaku po ograniczniku znaku zapytania (?) dla ciągu zapytania.
- Wartości parametrów mogą zawierać [wartości symboli wieloznacznych:](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)
   - Każdy wzorzec wartości parametru może składać się z jednej lub więcej gwiazdek (*), gdzie każda gwiazdka może odpowiadać sekwencji jednego lub więcej znaków.
   - Niektóre znaki wymagają kodowania adresów URL. Użyj symbolu procentowego, aby zakodować adres URL następujących znaków:

     Znak | Kodowanie adresów URL
     ----------|---------
     Miejsce     | %20
     &         | %25

- Określ wiele wartości, rozdzielając każdą z nich pojedynczą spacją.

   Na przykład: *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- Tylko dokładne dopasowania do co najmniej jednego z wzorców ciągów kwerendy spełniają ten warunek dopasowania.
    
- Użyj opcji **Ignoruj wielkość liter,** aby kontrolować wielkość liter porównania ciągów zapytań.
    
- Ze względu na sposób śledzenia ustawień pamięci podręcznej ten warunek dopasowania jest niezgodny z następującymi funkcjami:
   - Pełne wypełnienie pamięci podręcznej
   - Domyślny wewnętrzny wiek maksymalny
   - Wymuszenie wewnętrznego max-age
   - Ignoruj niejmówętną pamięć podręczną origin
   - Wewnętrzny Max-Stale

#### <a name="sample-scenarios"></a>Przykładowe scenariusze

Poniższy przykład pokazuje, jak ta opcja działa w określonych sytuacjach:

 Nazwa                 | Opis
 ---------------------|------------
user=joe              | Ten wzorzec jest dopasowywał się, gdy ciąg zapytania żądanego adresu URL to "?user=joe".
\*użytkownik\* \*= optout=\* | Ten wzorzec jest dopasowywał się, gdy kwerenda url sieci CDN zawiera parametr użytkownika lub optout.

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

## <a name="next-steps"></a>Następne kroki

- [Omówienie usługi Azure Content Delivery Network](cdn-overview.md)
- [Odwołanie do aparatu reguł](cdn-verizon-premium-rules-engine-reference.md)
- [Reguły wyrażeń warunkowych aparatu](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Reguły funkcji aparatu](cdn-verizon-premium-rules-engine-reference-features.md)
- [Zastępowanie domyślnego zachowania HTTP przy użyciu aparatu reguł](cdn-verizon-premium-rules-engine.md)
