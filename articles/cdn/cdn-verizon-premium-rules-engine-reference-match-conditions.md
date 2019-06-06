---
title: Usługa Azure CDN from Verizon — Premium reguł warunki dopasowań aparatu | Dokumentacja firmy Microsoft
description: Dokumentacja dotycząca usługi Azure Content Delivery Network w warstwie Premium firmy Verizon reguł warunki dopasowań aparatu.
services: cdn
author: mdgattuso
ms.service: cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 5197fdfe78b1b091de713754967f58157cc4a1b3
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481657"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Pasujące platformy Azure CDN from Verizon — Premium aparat reguł

W tym artykule przedstawiono szczegółowy opis warunków dopasowania dostępne dla usługi Azure Content Delivery Network (CDN) w warstwie Premium firmy Verizon [aparat reguł](cdn-verizon-premium-rules-engine.md).

Druga część reguły jest warunkiem dopasowania. Warunek dopasowania identyfikuje określone typy żądań, dla których zostaną wykonane zestaw funkcji.

Na przykład można użyć warunek dopasowania:

- Filtrowanie żądań dla zawartości w danej lokalizacji.
- Filtrowanie żądań jest generowany na podstawie określonego adresu IP lub kraju/regionu.
- Filtruj żądania według informacje nagłówka.

## <a name="always-match-condition"></a>Zawsze warunek dopasowania

Warunek dopasowania zawsze stosuje się domyślny zestaw funkcji do wszystkich żądań.

Name (Nazwa) | Przeznaczenie
-----|--------
[zawsze](#always) | Stosuje się domyślny zestaw funkcji do wszystkich żądań.

## <a name="device-match-condition"></a>Warunek dopasowania urządzenia

Warunek dopasowania urządzenia identyfikuje żądań wysyłanych z urządzenia przenośnego na podstawie jego właściwości.  

Name (Nazwa) | Przeznaczenie
-----|--------
[urządzenia](#device) | Identyfikuje żądań wysyłanych z urządzenia przenośnego na podstawie jego właściwości.

## <a name="location-match-conditions"></a>Warunki dopasowań lokalizacji

Warunki dopasowań lokalizacji zidentyfikować żądania na podstawie lokalizacji żądającego.

Name (Nazwa) | Przeznaczenie
-----|--------
[JAKO liczba](#as-number) | Identyfikuje żądań pochodzących z określonej sieci.
[Country](#country) | Identyfikuje żądań pochodzących z określonego krajów/regionów.

## <a name="origin-match-conditions"></a>Warunki dopasowań źródła

Warunki dopasowań źródła identyfikowania żądań, które wskazują Content Delivery Network magazynu lub serwer pochodzenia klienta.

Name (Nazwa) | Przeznaczenie
-----|--------
[Źródło usługi CDN](#cdn-origin) | Identyfikuje żądań dotyczących zawartości przechowywanej w magazynie Content Delivery Network.
[Źródłem klienta](#customer-origin) | Identyfikuje żądań dotyczących zawartości przechowywanej w serwer pochodzenia określonego klienta.

## <a name="request-match-conditions"></a>Warunki dopasowań żądania

Warunki dopasowań żądania zidentyfikować żądania na podstawie ich właściwości.

Name (Nazwa) | Przeznaczenie
-----|--------
[Adres IP klienta](#client-ip-address) | Identyfikuje żądań pochodzących z określonego adresu IP.
[Parametr plików cookie](#cookie-parameter) | Sprawdza, czy pliki cookie skojarzone z każdym żądaniem dla określonej wartości.
[Wyrażenie regularne parametru pliku cookie](#cookie-parameter-regex) | Sprawdza, czy pliki cookie skojarzone z każdym żądaniem dla określonego wyrażenia regularnego.
[Krawędź Cname](#edge-cname) | Identyfikuje żądań, które wskazują przewagę określonego rekordu CNAME.
[Odwołujące się domeny](#referring-domain) | Identyfikuje żądań, które zostały przekazane z nazw określonym hoście.
[Literał nagłówka żądania](#request-header-literal) | Identyfikuje żądań, które zawierają określony nagłówek równa określonej wartości.
[Request Header Regex](#request-header-regex) | Identyfikuje żądań, które zawierają określony nagłówek ustawiona na wartość, która odpowiada określonemu wyrażeniu regularnemu.
[Symbol wieloznaczny nagłówka żądania](#request-header-wildcard) | Identyfikuje żądań, które zawierają określony nagłówek ustawiona na wartość, która pasuje do wzorca określonego.
[Metoda żądania](#request-method) | Umożliwia określenie żądania przez metodę HTTP.
[Schemat żądania](#request-scheme) | Identyfikuje żądań według ich protokołu HTTP.

## <a name="url-match-conditions"></a>Warunki dopasowań adresów URL

Warunki dopasowań adresów URL identyfikowania żądań w oparciu o ich adresy URL.

Name (Nazwa) | Przeznaczenie
-----|--------
[Adres URL ścieżki katalogu](#url-path-directory) | Identyfikuje żądania za pomocą ich ścieżek względnych.
[Rozszerzenie ścieżki adresu URL](#url-path-extension) | Identyfikuje żądań według ich rozszerzenia nazwy pliku.
[Adres URL, nazwa_pliku ścieżki](#url-path-filename) | Identyfikuje żądania według nazwy pliku.
[Literał ścieżki adresu URL](#url-path-literal) | Porównuje ścieżki względnej żądania do określonej wartości.
[Wyrażenie regularne ścieżki adresu URL](#url-path-regex) | Porównuje względna ścieżka żądania do określonego wyrażenia regularnego.
[Symbol wieloznaczny ścieżki adresu URL](#url-path-wildcard) | Porównuje względna ścieżka żądania do określonego wzorca.
[Adres URL zapytania literału](#url-query-literal) | Porównuje ciąg zapytania żądania do określonej wartości.
[Parametr zapytania adresu URL](#url-query-parameter) | Identyfikuje żądań, które zawierają określony parametr ciągu zapytania ustawiona na wartość, która pasuje do określonego wzorca.
[Adres URL zapytania z wyrażeniem regularnym](#url-query-regex) | Identyfikuje żądań, które zawierają określony parametr ciągu zapytania ustawiona na wartość, która odpowiada określonemu wyrażeniu regularnemu.
[Adres URL zapytania z symbolami wieloznacznymi](#url-query-wildcard) | Porównuje wartość określona dla ciągu zapytania żądania.

## <a name="reference-for-rules-engine-match-conditions"></a>Dokumentacja dotycząca warunki dopasowań aparatu reguł

---

### <a name="always"></a>zawsze

Warunek dopasowania zawsze stosuje się domyślny zestaw funkcji do wszystkich żądań.

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="as-number"></a>JAKO liczba

Numer sieci jest definiowany przez jego numer systemu autonomicznego (ASN). 

**Dopasowania**/**nie odpowiada** opcja umożliwia określenie warunków, jakich numer AS zgodny warunek jest spełniony:

- **Dopasowuje**: Wymaga się, że numer ASN klienta sieci zgodny z jednym z określonym numery ASN. 
- **Nie odpowiada**: Wymaga się, że numer ASN klienta sieci pasuje do żadnego określonego numery ASN.

Informacje o kluczu:

- Aby określić wiele numerów ASN, rozdzielający każdej z nich z jednego miejsca. Na przykład 64514 64515 pasuje do żądania, odbierania 64514 lub 64515.
- Niektóre żądania mogą nie zwracać prawidłowy numer ASN. Znak zapytania (?) będą zgodne z żądaniami, dla których nie można określić prawidłowy numer ASN.
- Określ cały numer ASN dla żądanej sieci. Nie będzie można dopasować wartości częściowe.
- Ze względu na sposób, w której pamięci podręcznej ustawienia są śledzone ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Wypełnienie pamięci podręcznej ukończone
  - Max-Age wewnętrzny domyślny
  - Wymuszanie wewnętrznych Max-Age.
  - Ignoruj pochodzenia No-Cache
  - Internal Max-Stale

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cdn-origin"></a>Źródło usługi CDN

Warunek dopasowania źródło usługi CDN jest spełniony, gdy spełnione są oba poniższe warunki:

- Zażądano zawartości z magazynu usługi CDN.
- Typ punktu dostępu do zawartości (na przykład /000001), który jest zdefiniowany w tym warunku dopasowania korzysta z identyfikatora URI żądania:
  - ADRES URL USŁUGI CDN: Identyfikator URI żądania musi zawierać punkt wybranego dostęp do zawartości.
  - Adres URL CNAME Edge: Odpowiedniej konfiguracji CNAME krawędzi musi wskazywać na punkcie wybranego dostęp do zawartości.
  
Informacje o kluczu:

- Punkt dostępu do zawartości identyfikuje usługę umożliwiającą powinny służyć żądanej zawartości.
- Nie należy używać instrukcji IF i połączyć pewnych warunków dopasowania. Na przykład łącząc CDN Origin warunek dopasowania z warunkiem dopasowania źródłem klienta utworzyłoby wzorzec dopasowywania, która nigdy nie być zgodne. Z tego powodu dwóch warunków dopasowania źródło usługi CDN nie można łączyć za pomocą instrukcji IF i.

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="client-ip-address"></a>Adres IP klienta

**Dopasowania**/**nie odpowiada** opcja umożliwia określenie warunków, w których adres IP klienta odpowiada warunek jest spełniony:

- **Dopasowuje**: Wymaga się, że adres IP klienta pasuje do jednej z określonych adresów IP. 
- **Nie odpowiada**: Wymaga się, że adres IP klienta nie pasuje do żadnego z określonych adresów IP. 

Informacje o kluczu:

- Notacja CIDR.
- Określ wiele adresów IP i/lub bloki adresów IP, rozdzielający każdej z nich z jednego miejsca. Na przykład:
  - **Przykład IPv4**: 1.2.3.4 10.20.30.40 dopasowuje wszystkie żądania przychodzące z adresu 1.2.3.4 lub 10.20.30.40.
  - **Przykład IPv6**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 dopasowuje wszystkie żądania przychodzące z adresu 1:2:3:4:5:6:7:8 lub 10:20:30:40:50:60:70:80.
- Składnia blok adresów IP jest adres IP podstawowej następuje ukośnik i rozmiar prefiksu. Na przykład:
  - **Przykład IPv4**: 5.5.5.64/26 dopasowuje wszystkie żądania przychodzące z adresów 5.5.5.64 za pośrednictwem 5.5.5.127.
  - **Przykład IPv6**: 1:2:3: / 48 dopasowuje wszystkie żądania przychodzące z adresów 1:2:3:0:0:0:0:0 za pośrednictwem 1:2:3:ffff:ffff:ffff:ffff:ffff.
- Ze względu na sposób, w której pamięci podręcznej ustawienia są śledzone ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Wypełnienie pamięci podręcznej ukończone
  - Max-Age wewnętrzny domyślny
  - Wymuszanie wewnętrznych Max-Age.
  - Ignoruj pochodzenia No-Cache
  - Internal Max-Stale

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cookie-parameter"></a>Parametr plików cookie

**Dopasowania**/**nie odpowiada** opcja określa warunki, w których parametr plik Cookie jest zgodny warunek jest spełniony.

- **Dopasowuje**: Wymaga żądania zawiera określony plik cookie z wartością, która pasuje do co najmniej jednej wartości, które zostały zdefiniowane w tym stanie dopasowania.
- **Nie odpowiada**: Wymaga, że żądanie spełnia żadnego z następujących kryteriów:
  - Nie zawiera określonego pliku cookie.
  - Zawiera on określony plik cookie, ale jego wartość nie pasuje do żadnej wartości, które są zdefiniowane w tym stanie dopasowania.
  
Informacje o kluczu:

- Nazwa pliku cookie:
  - Ponieważ wartości symboli wieloznacznych, w tym gwiazdki (*) są nieobsługiwane w przypadku, gdy określasz nazwę pliku cookie, tylko dokładnie pliku cookie do dopasowania nazw kwalifikuje się do porównania.
  - Można określić tylko nazwę jednego pliku cookie na każde wystąpienie tego warunku dopasowania.
  - Bez uwzględniania wielkości liter podczas porównywania nazw plików cookie.
- Wartość pliku cookie:
  - Rozdzielający każdej z nich z jednego miejsca, aby określić wiele wartości pliku cookie.
  - Wartość pliku cookie korzystać z zalet [wartości symboli wieloznacznych](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Jeśli nie określono wartość symbolu wieloznacznego, dokładne dopasowanie zostanie spełnienia tego warunku dopasowania. Na przykład określenie "Value" będą zgodne "Value", ale nie "Wartość1" lub "Wartość2".
  - Użyj **Ignoruj przypadek** opcji do sterowania, czy wielkość liter jest porównywany wartość pliku cookie żądania.
- Ze względu na sposób, w której pamięci podręcznej ustawienia są śledzone ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Wypełnienie pamięci podręcznej ukończone
  - Max-Age wewnętrzny domyślny
  - Wymuszanie wewnętrznych Max-Age.
  - Ignoruj pochodzenia No-Cache
  - Internal Max-Stale

[Powrót do początku](#reference-for-rules-engine-match-conditions)
</br>

---

### <a name="cookie-parameter-regex"></a>Wyrażenie regularne parametru pliku cookie

Warunek dopasowania wyrażenia regularnego parametr plików Cookie definiuje nazwę pliku cookie i wartość. Możesz użyć [wyrażeń regularnych](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) do definiowania wartości żądanego pliku cookie.

**Dopasowania**/**nie odpowiada** opcja umożliwia określenie warunków, w jakich z wyrażeniem regularnym parametru pliku Cookie pasuje warunek jest spełniony.

- **Dopasowuje**: Wymaga żądania zawiera określony plik cookie z wartością, który odpowiada określonemu wyrażeniu regularnemu.
- **Nie odpowiada**: Wymaga, że żądanie spełnia żadnego z następujących kryteriów:
  - Nie zawiera określonego pliku cookie.
  - Zawiera on określony plik cookie, ale jej wartość jest niezgodna z określonego wyrażenia regularnego.
  
Informacje o kluczu:

- Nazwa pliku cookie:
  - Ponieważ wyrażeń regularnych i wartości symboli wieloznacznych, w tym gwiazdki (*) nie są obsługiwane, gdy określasz nazwę pliku cookie, tylko dokładnie pliku cookie do dopasowania nazw kwalifikuje się do porównania.
  - Można określić tylko nazwę jednego pliku cookie na każde wystąpienie tego warunku dopasowania.
  - Bez uwzględniania wielkości liter podczas porównywania nazw plików cookie.
- Wartość pliku cookie:
  - Wartość pliku cookie mogą korzystać z wyrażeń regularnych.
  - Użyj **Ignoruj przypadek** opcji do sterowania, czy wielkość liter jest porównywany wartość pliku cookie żądania.
- Ze względu na sposób, w której pamięci podręcznej ustawienia są śledzone ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Wypełnienie pamięci podręcznej ukończone
  - Max-Age wewnętrzny domyślny
  - Wymuszanie wewnętrznych Max-Age.
  - Ignoruj pochodzenia No-Cache
  - Internal Max-Stale

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="country"></a>Kraj

Można określić kraj za pośrednictwem jego numer kierunkowy kraju. 

**Dopasowania**/**nie odpowiada** opcja określa warunki, na jakich kraju odpowiada warunek jest spełniony:

- **Dopasowuje**: Wymaga zawierają wartości kodu kraju określonego żądania. 
- **Nie odpowiada**: Wymaga się, że żądanie nie zawiera wartości kodu kraju określonego.

Informacje o kluczu:

- Rozdzielający każdej z nich z jednego miejsca, aby określić wiele numerów kierunkowych krajów.
- Symbole wieloznaczne nie są obsługiwane, gdy wpisujesz kod kraju.
- Kody krajów "Europa" i "AP" nie obejmują wszystkich adresów IP w tych regionach.
- Niektóre żądania mogą nie zwracać prawidłowy kod kraju. Znak zapytania (?) będą zgodne z żądaniami, dla których nie można określić prawidłowy kod kraju.
- Kody krajów jest rozróżniana wielkość liter.
- Ze względu na sposób, w której pamięci podręcznej ustawienia są śledzone ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Wypełnienie pamięci podręcznej ukończone
  - Max-Age wewnętrzny domyślny
  - Wymuszanie wewnętrznych Max-Age.
  - Ignoruj pochodzenia No-Cache
  - Internal Max-Stale

#### <a name="implementing-country-filtering-by-using-the-rules-engine"></a>Implementowanie filtrowanie kraju, za pomocą aparatu reguł

Ten warunek dopasowania umożliwia wykonywanie wielu dostosowań na podstawie lokalizacji, z której pochodzi żądanie. Na przykład zachowanie funkcji filtrowanie kraju mogą być replikowane za pomocą następującej konfiguracji:

- Adres URL ścieżki symboli wieloznacznych: Ustaw [symboli wieloznacznych ścieżki adresu URL dopasować stan](#url-path-wildcard) do katalogu, który zostanie zabezpieczone. 
    Dołącz gwiazdkę na końcu ścieżki względnej, aby upewnić się, że dostęp do wszystkich jego obiektów podrzędnych będą ograniczone przez tę regułę.

- Dopasowanie kraju: Warunek dopasowania kraju dla żądanej grupy krajów.
  - Zezwalaj na: Warunek dopasowania kraju na **nie odpowiada** Aby zezwolić na dostęp w określonym krajów do zawartości przechowywanej w lokalizacji zdefiniowanej przez warunek dopasowania symboli wieloznacznych ścieżki adresu URL.
  - Blok: Warunek dopasowania kraju na **dopasowania** blokowania określonego krajów dostęp do zawartości przechowywanej w lokalizacji zdefiniowanej przez warunek dopasowania symboli wieloznacznych ścieżki adresu URL.

- Odmów dostępu (403) funkcji: Włącz [funkcja odmowy dostępu (403)](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) do replikowania dozwolonych lub zablokowanych część funkcji filtrowanie kraju.

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="customer-origin"></a>Źródłem klienta

Informacje o kluczu:

- Niezależnie od tego, czy żądanie zawartości za pośrednictwem adresu URL usługi CDN lub krawędź CNAME URL wskazujące pochodzenie wybranego klienta jest spełniony warunek dopasowania źródłem klienta.
- Konfiguracja źródła klienta, która odwołuje się do niej reguła nie można usunąć ze strony źródłem klienta. Przed przystąpieniem do usuwania konfiguracji pochodzenia klienta, upewnij się, że następujące konfiguracje nie odwołują się do jego:
  - Warunek dopasowania źródłem klienta
  - Konfiguracji CNAME usługi edge
- Nie należy używać instrukcji IF i połączyć pewnych warunków dopasowania. Na przykład łącząc warunek dopasowania źródłem klienta z warunkiem dopasowania CDN Origin utworzyłoby wzorzec dopasowywania, która nigdy nie być zgodne. Z tego powodu dwóch warunków dopasowania źródłem klienta nie można łączyć za pomocą instrukcji IF i.

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="device"></a>Urządzenie

Warunek dopasowania urządzenia identyfikuje żądań wysyłanych z urządzenia przenośnego na podstawie jego właściwości. Wykrywanie urządzeń przenośnych odbywa się za pośrednictwem [WURFL](http://wurfl.sourceforge.net/). 

**Dopasowania**/**nie odpowiada** opcja umożliwia określenie warunków, zgodnie z którymi urządzenie odpowiada warunek jest spełniony:

- **Dopasowuje**: Wymaga urządzenia żądającego odpowiadający określonej wartości. 
- **Nie odpowiada**: Wymaga się, że urządzenia żądającego nie odpowiada wartości określonej.

Informacje o kluczu:

- Użyj **Ignoruj przypadek** opcję, aby określić, czy określona wartość jest rozróżniana wielkość liter.
- Ze względu na sposób, w której pamięci podręcznej ustawienia są śledzone ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Wypełnienie pamięci podręcznej ukończone
  - Max-Age wewnętrzny domyślny
  - Wymuszanie wewnętrznych Max-Age.
  - Ignoruj pochodzenia No-Cache
  - Internal Max-Stale

#### <a name="string-type"></a>String — typ

Funkcja WURFL zazwyczaj przyjmuje dowolną kombinację cyfr, liter i symboli. Ze względu na charakter elastyczne tej funkcji możesz wybrać, jak wartość skojarzoną z tym warunkiem dopasowania jest interpretowany. W poniższej tabeli opisano zestaw dostępnych opcji:

Typ     | Opis
---------|------------
literał  | Wybierz tę opcję, aby uniemożliwić większości znaków tworzenia specjalnego znaczenia przy użyciu ich [wartości literału](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Symbol wieloznaczny | Wybierz tę opcję, aby móc korzystać z wszystkich [symboli wieloznacznych] ([wartości symboli wieloznacznych](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
Regex    | Wybierz tę opcję, aby użyć [wyrażeń regularnych](cdn-verizon-premium-rules-engine-reference.md#regular-expressions). Wyrażenia regularne są przydatne w przypadku definiowania wzorzec znaków.

#### <a name="wurfl-capabilities"></a>Możliwości WURFL

Możliwość WURFL odnosi się do kategorii, w tym artykule opisano urządzeń przenośnych. Wybrane możliwości Określa typ opisu urządzenia przenośnego, który służy do identyfikowania żądań.

W poniższej tabeli wymieniono możliwości WURFL oraz ich zmienne dla aparatu reguł.

> [!NOTE]
> Następujące zmienne są obsługiwane w **modyfikowania nagłówka żądania klienta** i **zmodyfikować nagłówek odpowiedzi klienta** funkcji.

Możliwości | Zmienna | Opis | Przykładowe wartości
-----------|----------|-------------|----------------
Nazwa marki | %{wurfl_cap_brand_name} | Ciąg, który wskazuje nazwę urządzenia. | Samsung
System operacyjny urządzenia | %{wurfl_cap_device_os} | Ciąg, który wskazuje system operacyjny zainstalowany na urządzeniu. | IOS
Wersja systemu operacyjnego urządzenia | %{wurfl_cap_device_os_version} | Ciąg, który wskazuje numer wersji systemu operacyjnego zainstalowanego na urządzeniu. | 1.0.1
Podwójna orientacji | %{wurfl_cap_dual_orientation} | Wartość logiczna, która wskazuje, czy urządzenie obsługuje podwójną orientacji. | true
HTML preferowane DTD | %{wurfl_cap_html_preferred_dtd} | Ciąg, który wskazuje, definicja typu dokumentu preferowanego urządzenia przenośnego (DTD) dla zawartości HTML. | Brak<br/>xhtml_basic<br/>html5
Wbudowanie obrazu | %{wurfl_cap_image_inlining} | Wartość logiczna wskazująca, czy urządzenie obsługuje Base64 zakodowany obrazów. | false
Program Android | %{wurfl_vcap_is_android} | Wartość logiczna, która wskazuje, czy jest używany system operacyjny Android. | true
IOS | %{wurfl_vcap_is_ios} | Wartość logiczna, która wskazuje, czy urządzenie korzysta z systemem iOS. | false
Jest telewizor Smart TV | %{wurfl_cap_is_smarttv} | Wartość logiczna, która wskazuje, czy urządzenie jest telewizor smart TV. | false
Jest Smartphone | % {wurfl_vcap_is_smartphone} | Wartość logiczna, która wskazuje, czy urządzenie jest smartfonie. | true
Jest typu Tablet | %{wurfl_cap_is_tablet} | Wartość logiczna, która wskazuje, czy urządzenie jest na komputerze typu tablet. Ten opis jest niezależny od systemu operacyjnego. | true
Czy urządzenie sieci bezprzewodowej | %{wurfl_cap_is_wireless_device} | Wartość logiczna, która wskazuje, czy urządzenie jest uznawane za urządzenia bezprzewodowego. | true
Nazwa marketingu | %{wurfl_cap_marketing_name} | Ciąg, który wskazuje nazwę marketingu urządzenia. | Perłowej blackBerry 8100
Przeglądarce dla urządzeń przenośnych | %{wurfl_cap_mobile_browser} | Ciąg, który wskazuje przeglądarki, która służy do żądania zawartości od urządzenia. | Chrome
Wersja w przeglądarce dla urządzeń przenośnych | %{wurfl_cap_mobile_browser_version} | Ciąg, który wskazuje wersję przeglądarki, która służy do żądania zawartości od urządzenia. | 31
Nazwa modelu | %{wurfl_cap_model_name} | Ciąg, który wskazuje nazwę modelu urządzenia. | s3
Pobierania progresywnego | %{wurfl_cap_progressive_download} | Wartość logiczna, która wskazuje, czy urządzenie obsługuje odtwarzanie dźwięku i wideo, gdy są nadal pobierane. | true
Data wydania | %{wurfl_cap_release_date} | Ciąg, który wskazuje, rok i miesiąc dodanie urządzenia do WURFL bazy danych.<br/><br/>Format: `yyyy_mm` | 2013_december
Rozdzielczość wysokości | %{wurfl_cap_resolution_height} | Liczba całkowita, która wskazuje urządzenia wysokość w pikselach. | 768
Szerokość rozwiązania | %{wurfl_cap_resolution_width} | Liczba całkowita, która wskazuje urządzenia szerokość w pikselach. | 1024

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="edge-cname"></a>Krawędź Cname

Informacje o kluczu:

- Lista dostępnych krawędzi rekordów CNAME jest ograniczona do tych rekordów CNAME krawędzi, skonfigurowane na stronie krawędzi rekordy CNAME dla platformy, na którym jest konfigurowane aparat reguł.
- Przed podjęciem próby usunięcia konfiguracji CNAME usługi edge, upewnij się, że warunek dopasowania Edge Cname nie odwołuje się on. Nie można usunąć krawędzi CNAME konfiguracje, które zostały zdefiniowane w regule ze strony rekordów CNAME krawędzi.
- Nie należy używać instrukcji IF i połączyć pewnych warunków dopasowania. Na przykład łącząc warunek dopasowania krawędzi Cname z warunkiem dopasowania źródłem klienta utworzyłoby wzorzec dopasowywania, która nigdy nie być zgodne. Z tego powodu dwóch warunków dopasowania Edge Cname nie można łączyć za pomocą instrukcji IF i.
- Ze względu na sposób, w której pamięci podręcznej ustawienia są śledzone ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Wypełnienie pamięci podręcznej ukończone
  - Max-Age wewnętrzny domyślny
  - Wymuszanie wewnętrznych Max-Age.
  - Ignoruj pochodzenia No-Cache
  - Internal Max-Stale

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="referring-domain"></a>Odwołujące się domeny

Nazwa hosta skojarzony z odwołania za pomocą którego zażądano zawartość określa, czy jest spełniony warunek odwołujące się do domeny.

**Dopasowania**/**nie odpowiada** opcja określa warunki, na jakich domeny odwołujące się dopasowania warunek jest spełniony:

- **Dopasowuje**: Wymaga nazwę hosta odwołujący się do pasuje do określonej wartości. 
- **Nie odpowiada**: Wymaga się, że odwołujący się nazwa hosta nie jest zgodna określonej wartości.

Informacje o kluczu:

- Rozdzielający każdej z nich z jednego miejsca, aby określić wiele nazw hostów.
- Ten warunek dopasowania obsługuje [wartości symboli wieloznacznych](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- Jeśli określona wartość nie zawiera znak gwiazdki, musi być dokładne dopasowanie dla nazwy hosta odwołania. Na przykład określenie "mojadomena.com" nie będzie odpowiadać "www.mydomain.com."
- Użyj **Ignoruj przypadek** opcji do sterowania, czy wykonano porównania uwzględniającego wielkość liter.
- Ze względu na sposób, w której pamięci podręcznej ustawienia są śledzone ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Wypełnienie pamięci podręcznej ukończone
  - Max-Age wewnętrzny domyślny
  - Wymuszanie wewnętrznych Max-Age.
  - Ignoruj pochodzenia No-Cache
  - Internal Max-Stale

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-literal"></a>Literał nagłówka żądania

**Dopasowania**/**nie odpowiada** opcja umożliwia określenie warunków, jakich żądania nagłówka literału dopasowania warunek jest spełniony.

- **Dopasowuje**: Wymaga zawiera określonego nagłówka żądania. Jego wartość musi odpowiadać jeden, który jest zdefiniowany w tym warunku dopasowania.
- **Nie odpowiada**: Wymaga, że żądanie spełnia żadnego z następujących kryteriów:
  - Nie zawiera określonego nagłówka.
  - Zawiera on określony nagłówek, ale jego wartość nie jest zgodna z ten, który jest zdefiniowany w tym stanie dopasowania.
  
Informacje o kluczu:

- Bez uwzględniania wielkości liter podczas porównywania nazw nagłówka. Użyj **Ignoruj przypadek** opcja kontrolowania uwzględnianie wielkości liter dla porównania wartości nagłówka.
- Ze względu na sposób, w której pamięci podręcznej ustawienia są śledzone ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Wypełnienie pamięci podręcznej ukończone
  - Max-Age wewnętrzny domyślny
  - Wymuszanie wewnętrznych Max-Age.
  - Ignoruj pochodzenia No-Cache
  - Internal Max-Stale

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-regex"></a>Wyrażenie regularne nagłówka żądania

**Dopasowania**/**nie odpowiada** opcja określa warunki, na jakich Regex nagłówka żądania odpowiada warunek jest spełniony.

- **Dopasowuje**: Wymaga zawiera określonego nagłówka żądania. Jego wartość musi odpowiadać wzorzec zdefiniowany w określonym [wyrażenia regularnego](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).
- **Nie odpowiada**: Wymaga, że żądanie spełnia żadnego z następujących kryteriów:
  - Nie zawiera określonego nagłówka.
  - Zawiera on określony nagłówek, ale jej wartość jest niezgodna z określonego wyrażenia regularnego.

Informacje o kluczu:

- Nazwa nagłówka:
  - Bez uwzględniania wielkości liter podczas porównywania nazw nagłówka.
  - Zastąp spacje w nazwie nagłówka "% 20."
- Wartość nagłówka:
  - Wartość nagłówka korzystać z zalet wyrażeń regularnych.
  - Użyj **Ignoruj przypadek** opcja kontrolowania uwzględnianie wielkości liter dla porównania wartości nagłówka.
  - Warunek dopasowania jest spełniony, tylko wtedy, gdy wartość nagłówka dokładnie pasuje do co najmniej jeden określony wzorców.
- Ze względu na sposób, w której pamięci podręcznej ustawienia są śledzone ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Wypełnienie pamięci podręcznej ukończone
  - Max-Age wewnętrzny domyślny
  - Wymuszanie wewnętrznych Max-Age.
  - Ignoruj pochodzenia No-Cache
  - Internal Max-Stale

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-header-wildcard"></a>Symbol wieloznaczny nagłówka żądania

**Dopasowania**/**nie odpowiada** opcja umożliwia określenie warunków, zgodnie z którymi nagłówka żądania symbol wieloznaczny pasuje warunek jest spełniony.

- **Dopasowuje**: Wymaga zawiera określonego nagłówka żądania. Jego wartość musi odpowiadać przynajmniej jedną z wartości, które zostały zdefiniowane w tym stanie dopasowania.
- **Nie odpowiada**: Wymaga, że żądanie spełnia żadnego z następujących kryteriów:
  - Nie zawiera określonego nagłówka.
  - Zawiera on określony nagłówek, ale jego wartość nie pasuje do żadnego z określonymi wartościami.
  
Informacje o kluczu:

- Nazwa nagłówka:
  - Bez uwzględniania wielkości liter podczas porównywania nazw nagłówka.
  - Miejsca do magazynowania w nazwie nagłówka powinny być zastąpione przez "% 20." Umożliwia także tę wartość do określenia miejsca do magazynowania w wartości nagłówka.
- Wartość nagłówka:
  - Wartość nagłówka korzystać z zalet [wartości symboli wieloznacznych](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Użyj **Ignoruj przypadek** opcja kontrolowania uwzględnianie wielkości liter dla porównania wartości nagłówka.
  - Ten warunek dopasowania jest spełniony, gdy wartość nagłówka dokładnie pasuje do co najmniej jeden określony wzorców.
  - Rozdzielający każdej z nich z jednego miejsca, aby określić wiele wartości.
- Ze względu na sposób, w której pamięci podręcznej ustawienia są śledzone ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Wypełnienie pamięci podręcznej ukończone
  - Max-Age wewnętrzny domyślny
  - Wymuszanie wewnętrznych Max-Age.
  - Ignoruj pochodzenia No-Cache
  - Internal Max-Stale

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-method"></a>Metoda żądania

Warunek dopasowania metody żądania jest spełniony, tylko wtedy, gdy zasoby są żądane za pośrednictwem metody wybranego żądania. Metody dostępne żądania są:

- GET
- GŁÓWNY
- POST
- OPCJE
- PUT
- DELETE
- TRACE
- POŁĄCZ

Informacje o kluczu:

- Domyślnie metoda żądania GET może generować buforowanej zawartości w sieci. Wszystkie inne metody żądania są przekierowywane za pośrednictwem sieci.
- Ze względu na sposób, w której pamięci podręcznej ustawienia są śledzone ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Wypełnienie pamięci podręcznej ukończone
  - Max-Age wewnętrzny domyślny
  - Wymuszanie wewnętrznych Max-Age.
  - Ignoruj pochodzenia No-Cache
  - Internal Max-Stale

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-scheme"></a>Schemat żądania

Jest spełniony warunek dopasowania schemat żądania, tylko wtedy, gdy zasoby są żądane za pomocą wybranego protokołu. Protokoły dostępne są następujące:

- HTTP
- HTTPS

Informacje o kluczu:

- Ze względu na sposób, w której pamięci podręcznej ustawienia są śledzone ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Wypełnienie pamięci podręcznej ukończone
  - Max-Age wewnętrzny domyślny
  - Wymuszanie wewnętrznych Max-Age.
  - Ignoruj pochodzenia No-Cache
  - Internal Max-Stale

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-directory"></a>Adres URL ścieżki katalogu

Identyfikuje żądanie przy użyciu ścieżki względnej, co wyklucza nazwy pliku żądanego zasobu.

**Dopasowania**/**nie odpowiada** opcja umożliwia określenie warunków, jakich katalog ścieżka URL dopasowania warunek jest spełniony.

- **Dopasowuje**: Wymaga żądania zawiera ścieżką względnego adresu URL, z wyłączeniem nazwę pliku, który pasuje do określonego wzorca adresu URL.
- **Nie odpowiada**: Wymaga żądania zawiera ścieżką względnego adresu URL, z wyjątkiem nazwy pliku, który nie pasuje do określonego wzorca adresu URL.

Informacje o kluczu:

- Użyj **względem** opcję, aby określić, czy porównanie adres URL rozpoczyna się przed lub po nim punkt dostępu do zawartości. Punkt dostępu do zawartości jest część ścieżki, która pojawia się między usługi CDN firmy Verizon nazwy hosta i ścieżkę względną do żądanego zasobu (na przykład /800001/CustomerOrigin). Identyfikuje lokalizację przez typ serwera (na przykład źródła usługi CDN lub klienta) i numer konta klienta.

   Następujące wartości są dostępne dla **względem** opcji:
  - **Główny**: Wskazuje, że punkt Porównanie adres URL rozpoczyna się bezpośrednio po hosta CDN. 

  Na przykład: http:\//wpc.0001.&lt; domeny&gt;/**800001/myorigin/MójFolder**/index.htm

  - **Pochodzenie**: Wskazuje, że punkt Porównanie adres URL rozpoczyna się po punkt dostępu do zawartości (na przykład myorigin /000001 lub/800001 /). Ponieważ \*. azureedge.net CNAME jest tworzony domyślnie względem katalogu origin na nazwę hosta usługi CDN firmy Verizon, sieć CDN systemu Azure użytkownicy powinni używać **pochodzenia** wartość. 

  Na przykład: https:\//&lt;punktu końcowego&gt;.azureedge.net/**MójFolder**/index.htm 

  Ten adres URL wskazuje następujące nazwy hosta usługi CDN firmy Verizon: http:\//wpc.0001.&lt; domeny&gt;/800001/myorigin/**MójFolder**/index.htm

- Krawędź CNAME adres URL jest przepisane, aby adres URL usługi CDN przed porównania adresu URL.

    Na przykład oba następujące adresy URL wskazują ten sam zasób i dlatego mają tę samą ścieżkę adresu URL.
  - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
    
  - Adres URL CNAME Edge: http:\//&lt;punktu końcowego&gt;.azureedge.net/path/asset.htm
    
    Aby uzyskać dodatkowe informacje:
  - Domena niestandardowa: https:\//my.domain.com/path/asset.htm
    
    - Ścieżka adresu URL (względem katalogu głównego): / 800001/CustomerOrigin/path /
    
    - Ścieżka adresu URL (względem źródła): /path/

- Część adresu URL, który jest używany do adresu URL porównanie kończy się tuż przed nazwy pliku żądanego zasobu. Ukośnik jest ostatni znak w tego rodzaju ścieżki.

- Zamień wszystkie spacje w wzorzec ścieżki adresu URL "% 20."

- Każdy wzorzec ścieżki adresu URL może zawierać jeden lub więcej gwiazdki (*), gdzie każdy gwiazdka odpowiada sekwencji co najmniej jeden znak.

- Należy określić wiele ścieżek URL we wzorcu, przez rozdzielający każdej z nich z jednego miejsca.

    Na przykład: * /sales/ * /marketing/

- Specyfikacja ścieżki adresu URL korzystać z zalet [wartości symboli wieloznacznych](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Użyj **Ignoruj przypadek** opcji do sterowania czy odbywa się porównania uwzględniającego wielkość liter.

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-extension"></a>Rozszerzenie ścieżki adresu URL

Umożliwia określenie żądania przez rozszerzenie pliku żądanego zasobu.

**Dopasowania**/**nie odpowiada** opcja umożliwia określenie warunków, jakich rozszerzenie ścieżki adresu URL dopasowania warunek jest spełniony.

- **Dopasowuje**: Wymaga adresu URL żądania zawiera rozszerzenie pliku, który dokładnie pasuje do wzorca określonego.

   Na przykład jeśli określisz "htm", "htm" zasoby są spełnione, ale nie "html" zasoby.  

- **Nie odpowiada**: Wymaga adresu URL żądania zawiera rozszerzenie pliku, który nie pasuje do wzorca określonego.

Informacje o kluczu:

- Określanie rozszerzeń plików, aby dopasować w **wartość** pole. Nie dołączaj rozpoczynać się od kropki; na przykład użyć htm, zamiast .htm.

- Użyj **Ignoruj przypadek** opcji do sterowania czy odbywa się porównania uwzględniającego wielkość liter.

- Aby określić wiele rozszerzeń plików, rozdzielający każdego rozszerzenia z jednego miejsca. 

    Na przykład: htm, html

- Na przykład określenie "htm" pasuje do "htm" zasoby, ale nie "html" zasoby.

#### <a name="sample-scenario"></a>Przykładowy scenariusz

Następujące Przykładowa konfiguracja zakłada spełnienia tego warunku dopasowania, gdy żądanie pasuje do jednej z określonych rozszerzeń.

Wartość specyfikacji: asp aspx php html

Ten warunek dopasowania jest spełniony, gdy znajdzie adresów URL, które kończą się z następującymi rozszerzeniami:

- ASP
- .aspx
- PHP
- .html

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-filename"></a>Adres URL, nazwa_pliku ścieżki

Identyfikuje żądania według nazwy pliku żądanego zasobu. Do celów tego warunku dopasowania nazwa pliku składa się z nazwy żądanej zawartości, okres i rozszerzenie pliku (na przykład index.html).

**Dopasowania**/**nie odpowiada** opcja umożliwia określenie warunków, jakich Filename ścieżki adresu URL dopasowania warunek jest spełniony.

- **Dopasowuje**: Wymaga żądania zawiera nazwę pliku w ścieżce adresu URL, który pasuje do wzorca określonego.
- **Nie odpowiada**: Wymaga żądania zawiera nazwę pliku w ścieżce adresu URL, który nie pasuje do wzorca określonego.

Informacje o kluczu:

- Użyj **Ignoruj przypadek** opcji do sterowania czy odbywa się porównania uwzględniającego wielkość liter.

- Aby określić wiele rozszerzeń plików, należy oddzielić każdego rozszerzenia z jednego miejsca.

    Na przykład: index.htm index.html

- Zastąp spacje wartość nazwy pliku "% 20."

- Wartość nazwy pliku korzystać z zalet [wartości symboli wieloznacznych](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Na przykład każdy wzorzec nazwy pliku może składać się z przynajmniej jednej gwiazdki (*), gdzie każdy gwiazdka odpowiada sekwencji co najmniej jeden znak.

- Jeśli symbole wieloznaczne nie są określone, dokładne dopasowanie będzie spełniać tego warunku dopasowania.

    Na przykład określenie "presentation.ppt" pasuje do elementu zawartości o nazwie "presentation.ppt", ale nie jeden o nazwie "presentation.pptx."

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-literal"></a>Literał ścieżki adresu URL

Porównuje Ścieżka adresu URL żądania, łącznie z nazwą pliku, z podaną wartością.

**Dopasowania**/**nie odpowiada** opcja umożliwia określenie warunków, jakich literał ścieżki adresu URL dopasowania warunek jest spełniony.

- **Dopasowuje**: Wymaga żądania zawierają ścieżkę adresu URL, który pasuje do wzorca określonego.
- **Nie odpowiada**: Wymaga żądania zawierają ścieżkę adresu URL, który nie pasuje do wzorca określonego.

Informacje o kluczu:

- Użyj **względem** opcję, aby określić, czy punkt Porównanie adres URL rozpoczyna się przed lub po nim punkt dostępu do zawartości. 

    Następujące wartości są dostępne dla **względem** opcji:
  - **Główny**: Wskazuje, że punkt Porównanie adres URL rozpoczyna się bezpośrednio po hosta CDN.

    Na przykład: http:\//wpc.0001.&lt; domeny&gt;/**800001/myorigin/myfolder/index.htm**

  - **Pochodzenie**: Wskazuje, że punkt Porównanie adres URL rozpoczyna się po punkt dostępu do zawartości (na przykład myorigin /000001 lub/800001 /). Ponieważ \*. azureedge.net CNAME jest tworzony domyślnie względem katalogu origin na nazwę hosta usługi CDN firmy Verizon, sieć CDN systemu Azure użytkownicy powinni używać **pochodzenia** wartość. 

    Na przykład: https:\//&lt;punktu końcowego&gt;.azureedge.net/**myfolder/index.htm**

  Ten adres URL wskazuje następujące nazwy hosta usługi CDN firmy Verizon: http:\//wpc.0001.&lt; domeny&gt;/800001/myorigin/**myfolder/index.htm**

- Krawędź CNAME adres URL jest przepisane, aby adres URL usługi CDN przed porównaniem adresu URL.

Na przykład oba następujące adresy URL wskazują ten sam zasób i dlatego mają tę samą ścieżkę adresu URL:

- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
- Adres URL CNAME Edge: http:\//&lt;punktu końcowego&gt;.azureedge.net/path/asset.htm

    Aby uzyskać dodatkowe informacje:
    
    - Ścieżka adresu URL (względem katalogu głównego): /800001/CustomerOrigin/path/asset.htm
   
    - Ścieżka adresu URL (względem źródła): /path/asset.htm

- Ciągi zapytania w adresie URL są ignorowane.
- Użyj **Ignoruj przypadek** opcji do sterowania czy odbywa się porównania uwzględniającego wielkość liter.
- Wartość określona dla tego warunku dopasowania jest porównywana ścieżki względnej dokładnie żądania wysłane przez klienta.

- Aby dopasować wszystkich żądań do określonego katalogu, użyj [adresu URL ścieżki katalogu](#url-path-directory) lub [symboli wieloznacznych ścieżki adresu URL](#url-path-wildcard) warunku dopasowania.

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-regex"></a>Wyrażenie regularne ścieżki adresu URL

Porównuje ścieżki adresu URL żądania do określonej [wyrażenia regularnego](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

**Dopasowania**/**nie odpowiada** opcja umożliwia określenie warunków, zgodnie z którymi z wyrażeniem regularnym ścieżki adresu URL dopasowania warunek jest spełniony.

- **Dopasowuje**: Wymaga żądania zawierają ścieżkę adresu URL, który odpowiada określonemu wyrażeniu regularnemu.
- **Nie odpowiada**: Wymaga żądania zawierają ścieżkę adresu URL, który nie jest zgodny z określonym wyrażeniem regularnym.

Informacje o kluczu:

- Krawędź CNAME adres URL jest przepisane, aby adres URL usługi CDN przed porównania adresu URL.

    Na przykład oba adresy URL wskazują ten sam zasób i dlatego mają tę samą ścieżkę adresu URL.

     - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm

     - Edge CNAME URL: http:\//my.domain.com/path/asset.htm

    Aby uzyskać dodatkowe informacje:
    
     - Ścieżka adresu URL: /800001/CustomerOrigin/path/asset.htm

- Ciągi zapytania w adresie URL są ignorowane.
    
- Użyj **Ignoruj przypadek** opcji do sterowania czy odbywa się porównania uwzględniającego wielkość liter.
    
- Miejsca do magazynowania w ścieżce adresu URL powinny być zastąpione przez "% 20."

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-wildcard"></a>Symbol wieloznaczny ścieżki adresu URL

Porównuje żądania względną ścieżkę URL do wzorca określonego symbolu wieloznacznego.

**Dopasowania**/**nie odpowiada** opcja umożliwia określenie warunków, jakich symbolu wieloznacznego ścieżki adresu URL dopasowania warunek jest spełniony.

- **Dopasowuje**: Wymaga żądania zawierają ścieżkę adresu URL, który pasuje do wzorca określonego symbolu wieloznacznego.
- **Nie odpowiada**: Wymaga żądania zawierają ścieżkę adresu URL, który nie pasuje do wzorca określonego symbolu wieloznacznego.

Informacje o kluczu:

- **Względem** opcji: Ta opcja określa, czy punkt Porównanie adres URL rozpoczyna się przed lub po nim punkt dostępu do zawartości.

   Ta opcja może mieć następujące wartości:
     - **Główny**: Wskazuje, że punkt Porównanie adres URL rozpoczyna się bezpośrednio po hosta CDN.

       Na przykład: http:\//wpc.0001.&lt; domeny&gt;/**800001/myorigin/myfolder/index.htm**

     - **Pochodzenie**: Wskazuje, że punkt Porównanie adres URL rozpoczyna się po punkt dostępu do zawartości (na przykład myorigin /000001 lub/800001 /). Ponieważ \*. azureedge.net CNAME jest tworzony domyślnie względem katalogu origin na nazwę hosta usługi CDN firmy Verizon, sieć CDN systemu Azure użytkownicy powinni używać **pochodzenia** wartość. 

       Na przykład: https:\//&lt;punktu końcowego&gt;.azureedge.net/**myfolder/index.htm**

     Ten adres URL wskazuje następujące nazwy hosta usługi CDN firmy Verizon: http:\//wpc.0001.&lt; domeny&gt;/800001/myorigin/**myfolder/index.htm**

- Krawędź CNAME adres URL jest przepisane, aby adres URL usługi CDN przed porównania adresu URL.

    Na przykład oba następujące adresy URL wskazują ten sam zasób i dlatego mają tę samą ścieżkę adresu URL:
     - CDN URL: http://wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
     - Adres URL CNAME Edge: http:\//&lt;punktu końcowego&gt;.azureedge.net/path/asset.htm
    
    Aby uzyskać dodatkowe informacje:
    
     - Ścieżka adresu URL (względem katalogu głównego): /800001/CustomerOrigin/path/asset.htm
    
     - Ścieżka adresu URL (względem źródła): /path/asset.htm
    
- Rozdzielający każdej z nich z jednego miejsca, aby określić wiele ścieżek URL.

   Na przykład: /marketing/asset.* /sales/*.htm

- Ciągi zapytania w adresie URL są ignorowane.
    
- Użyj **Ignoruj przypadek** opcji do sterowania czy odbywa się porównania uwzględniającego wielkość liter.
    
- Zastąp spacje w ścieżce adresu URL "% 20."
    
- Wartość określona dla ścieżki adresu URL korzystać z zalet [wartości symboli wieloznacznych](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Każdy wzorzec ścieżki adresu URL może zawierać jeden lub więcej gwiazdek (*), gdzie każdy gwiazdki można dopasować sekwencji jednego lub większej liczby znaków.

#### <a name="sample-scenarios"></a>Przykładowe scenariusze

Przykładowe konfiguracje w poniższej tabeli przyjęto założenie, czy ten warunek dopasowania jest spełniony, gdy żądanie pasuje do określonego wzorca adresu URL:

Wartość                   | Względem    | Wynik 
------------------------|----------------|-------
*/test.html */test.php  | Katalog główny lub punkt początkowy | Ten wzorzec jest takie samo przez żądania zasobów o nazwie "test.html" lub "test.php" w dowolnym folderze.
/ 80ABCD/źródła/tekst / *   | Główny           | Ten wzorzec jest dopasowywany gdy żądany zasób spełnia następujące kryteria: <br />-Musi znajdować się w źródle klienta, o nazwie "origin". <br />— Ścieżka względna musi rozpoczynać się folder o nazwie "text". Oznacza to, że żądany zasób może się znajdować albo w folderze "text" lub jednej z jego podfolderów cykliczne.
*/CSS/* */js/*          | Katalog główny lub punkt początkowy | Ten wzorzec jest takie samo przez wszystkie sieci CDN lub Microsoft edge adresy URL CNAME, który zawiera folder css i js.
*.jpg *.gif *.png       | Katalog główny lub punkt początkowy | Ten wzorzec jest takie samo przez wszystkie sieci CDN lub Microsoft edge CNAME adresy URL kończą się ciągiem jpg, GIF lub PNG. Alternatywny sposób, aby określić ten wzorzec jest [rozszerzenie ścieżki adresu URL dopasować stan](#url-path-extension).
/ obrazy / * / media / *      | Origin         | Ten wzorzec jest takie samo przez sieć CDN lub Microsoft edge CNAME adresy URL, którego ścieżka względna zaczyna się od "obrazy" lub "nośnika" folder. <br />-Adres URL usługi CDN: http:\//wpc.0001.&lt; domeny&gt;/800001/myorigin/images/sales/event1.png<br />- Sample edge CNAME URL: http:\//cdn.mydomain.com/images/sales/event1.png

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-literal"></a>Adres URL zapytania literału

Porównuje ciąg zapytania żądania do określonej wartości.

**Dopasowania**/**nie odpowiada** opcja określa warunki, na jakich literału zapytania URL dopasować stan jest spełniony.

- **Dopasowuje**: Wymaga żądania zawierać ciągu zapytania adresu URL, który odpowiada określony ciąg zapytania.
- **Nie odpowiada**: Wymaga żądania zawierać ciągu zapytania adresu URL, który nie jest zgodny z określony ciąg zapytania.

Informacje o kluczu:

- Tylko dokładne zapytania dopasowuje ciąg spełnia tego warunku dopasowania.
    
- Użyj **Ignoruj przypadek** opcja kontrolowania uwzględnianie wielkości liter porównania ciągu zapytania.
    
- Nie dołączaj wiodących znaku zapytania (?) w tekście wartości ciągu zapytania.
    
- Niektórych znaków wymaga kodowania adresów URL. Użyj symbol procentu do adresu URL zakodować następujących znaków:

   Znak | Kodowanie adresu URL
   ----------|---------
   Przestrzeń kosmiczna     | %20
   &         | %25

- Ze względu na sposób, w której pamięci podręcznej ustawienia są śledzone ten warunek dopasowania jest niezgodny z następującymi funkcjami:
   - Wypełnienie pamięci podręcznej ukończone
   - Max-Age wewnętrzny domyślny
   - Wymuszanie wewnętrznych Max-Age.
   - Ignoruj pochodzenia No-Cache
   - Internal Max-Stale

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-parameter"></a>Parametr zapytania adresu URL

Identyfikuje żądań, które zawierają parametr ciągu zapytania określona. Ten parametr jest równa wartości, który odpowiada określonemu wzorcowi. Parametry ciągu zapytania (na przykład parametr = wartość) w żądaniu adresu URL ustalić, czy ten warunek jest spełniony. Ten warunek dopasowania identyfikuje jako parametr ciągu zapytania za pomocą nazwy i przyjmuje jedną lub więcej wartości dla wartości parametru. 

**Dopasowania**/**nie odpowiada** opcja określa warunki, na jakich parametr zapytania adresu URL jest zgodny warunek jest spełniony.

- **Dopasowuje**: Wymaga żądanie zawiera określonego parametru z wartością, która pasuje do co najmniej jednej wartości, które zostały zdefiniowane w tym stanie dopasowania.
- **Nie odpowiada**: Wymaga, że żądanie spełnia żadnego z następujących kryteriów:
  - Nie zawiera określonego parametru.
  - Zawiera on określony parametr, ale jego wartość nie pasuje do żadnej wartości, które są zdefiniowane w tym stanie dopasowania.

Ten warunek dopasowania zapewnia prosty sposób określić kombinacji nazwy i wartości parametrów. Większa elastyczność dzięki jeśli są dopasowywania parametru ciągu zapytania, należy rozważyć użycie [adresu URL zapytania z symbolami wieloznacznymi](#url-query-wildcard) warunku dopasowania.

Informacje o kluczu:

- Dla każdego wystąpienia tego warunku dopasowania można określić tylko pojedynczego adresu URL zapytania nazwę parametru.
    
- Ponieważ wartości symboli wieloznacznych nie są obsługiwane, gdy nazwa parametru jest określona, tylko parametr dokładnego dopasowania nazw kwalifikuje się do porównania.
- Może zawierać wartości parametrów [wartości symboli wieloznacznych](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
   - Każdy wzorzec wartość parametru może składać się z co najmniej jeden gwiazdki (*), gdzie każdy gwiazdki można dopasować sekwencji jednego lub większej liczby znaków.
   - Niektórych znaków wymaga kodowania adresów URL. Użyj symbol procentu do adresu URL zakodować następujących znaków:

       Znak | Kodowanie adresu URL
       ----------|---------
       Przestrzeń kosmiczna     | %20
       &         | %25

- Aby określić wiele wartości parametru ciągu zapytania, rozdzielający każdej z nich z jednego miejsca. Ten warunek dopasowania jest spełniony, gdy żądanie zawiera jedną z kombinacji określona nazwa/wartość.

   - Przykład 1:

     - Konfiguracja:

       Wartośćb Wartośća

     - Ta konfiguracja jest zgodny z następujących parametrów ciągu zapytania:

       Parametr Parameter1 = Wartośća
    
       Parametr Parameter1 = Wartośćb

   - Przykład 2:

     - Konfiguracja: 

        Wartość % 20A wartość % 20B

     - Ta konfiguracja jest zgodny z następujących parametrów ciągu zapytania:

       Parametr Parameter1 = wartość % 20A

       Parametr Parameter1 = wartość % 20B

- Ten warunek dopasowania jest spełniony tylko wtedy, gdy istnieje dokładne dopasowanie do co najmniej jednej kombinacji nazwa/wartość ciągu określonego zapytania.

   Na przykład, jeśli używasz konfiguracji w poprzednim przykładzie parametr nazwa/wartość kombinacji "parametr1 = ValueAdd" nie może być uznane za pasujące. Jednakże jeśli określisz jednej z następujących wartości będą zgodne tej kombinacji nazwa/wartość:

   - ValueAdd Wartośćb Wartośća
   - Wartośćb Wartośća *

- Użyj **Ignoruj przypadek** opcja kontrolowania uwzględnianie wielkości liter porównania ciągu zapytania.
    
- Ze względu na sposób, w której pamięci podręcznej ustawienia są śledzone ten warunek dopasowania jest niezgodny z następującymi funkcjami:
   - Wypełnienie pamięci podręcznej ukończone
   - Max-Age wewnętrzny domyślny
   - Wymuszanie wewnętrznych Max-Age.
   - Ignoruj pochodzenia No-Cache
   - Internal Max-Stale

#### <a name="sample-scenarios"></a>Przykładowe scenariusze

W poniższym przykładzie pokazano, jak ta opcja działa w określonych sytuacjach:

Name (Nazwa)  | Wartość |  Wynik
------|-------|--------
Użytkownik  | Joe   | Ten wzorzec jest dopasowywany gdy ciąg zapytania dla żądanego adresu URL jest "? użytkownika = Jan."
Użytkownik  | *     | Ten wzorzec jest dopasowywany gdy ciąg zapytania dla żądanego adresu URL zawiera parametr użytkownika.
Email | Joe\* | Ten wzorzec jest dopasowywany gdy ciąg zapytania dla żądanego adresu URL zawiera parametr poczty E-mail, który rozpoczyna się od "Jan".

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-regex"></a>Adres URL zapytania z wyrażeniem regularnym

Identyfikuje żądań, które zawierają parametr ciągu zapytania określona. Ten parametr jest ustawiona na wartość, która pasuje do określonej [wyrażenia regularnego](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

**Dopasowania**/**nie odpowiada** opcja umożliwia określenie warunków, zgodnie z którymi z wyrażeniem regularnym zapytania URL dopasowania warunek jest spełniony.

- **Dopasowuje**: Wymaga żądania zawierać ciągu zapytania adresu URL, który odpowiada określonemu wyrażeniu regularnemu.
- **Nie odpowiada**: Wymaga żądania zawierać ciągu zapytania adresu URL, który nie jest zgodny z określonym wyrażeniem regularnym.

Informacje o kluczu:

- Tylko dokładne dopasowania do określonego wyrażenia regularnego spełnienia tego warunku dopasowania.
    
- Użyj **Ignoruj przypadek** opcja kontrolowania uwzględnianie wielkości liter porównania ciągu zapytania.
    
- Dla celów tej opcji ciąg zapytania rozpoczyna się pierwszy znak po znaku zapytania (?) / / / Ogranicznik ciągu zapytania.
    
- Niektórych znaków wymaga kodowania adresów URL. Użyj symbol procentu do adresu URL zakodować następujących znaków:

   Znak | Kodowanie adresu URL | Wartość
   ----------|--------------|------
   Przestrzeń kosmiczna     | %20          | \%20
   &         | %25          | \%25

   Należy zwrócić uwagę na to, czy symbole procentu należy użyć znaków ucieczki.

- Znaki specjalne wyrażenia regularnego ucieczki podwójnej precyzji (na przykład \^$. +) Aby zawrzeć ukośnik odwrotny w wyrażeniu regularnym.

   Na przykład:

   Wartość | Interpretowane jako 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Ze względu na sposób, w której pamięci podręcznej ustawienia są śledzone ten warunek dopasowania jest niezgodny z następującymi funkcjami:
   - Wypełnienie pamięci podręcznej ukończone
   - Max-Age wewnętrzny domyślny
   - Wymuszanie wewnętrznych Max-Age.
   - Ignoruj pochodzenia No-Cache
   - Internal Max-Stale

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-wildcard"></a>Adres URL zapytania z symbolami wieloznacznymi

Porównuje określonej wartości dla ciągu zapytania żądania.

**Dopasowania**/**nie odpowiada** opcja umożliwia określenie warunków, jakich symbolu wieloznacznego zapytania URL dopasowania warunek jest spełniony.

- **Dopasowuje**: Wymaga żądania zawierać ciągu zapytania adresu URL, który pasuje do wartości określonego symbolu wieloznacznego.
- **Nie odpowiada**: Wymaga żądania zawierać ciągu zapytania adresu URL, który nie jest zgodna z wartością określoną symboli wieloznacznych.

Informacje o kluczu:

- Dla celów tej opcji ciąg zapytania rozpoczyna się pierwszy znak po znaku zapytania (?) / / / Ogranicznik ciągu zapytania.
- Może zawierać wartości parametrów [wartości symboli wieloznacznych](cdn-verizon-premium-rules-engine-reference.md#wildcard-values):
   - Każdy wzorzec wartość parametru może składać się z co najmniej jeden gwiazdki (*), gdzie każdy gwiazdki można dopasować sekwencji jednego lub większej liczby znaków.
   - Niektórych znaków wymaga kodowania adresów URL. Użyj symbol procentu do adresu URL zakodować następujących znaków:

     Znak | Kodowanie adresu URL
     ----------|---------
     Przestrzeń kosmiczna     | %20
     &         | %25

- Rozdzielający każdej z nich z jednego miejsca, aby określić wiele wartości.

   Na przykład: *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- Tylko dokładne dopasowania do co najmniej jednym z wzorców ciągu określonego zapytania spełnienia tego warunku dopasowania.
    
- Użyj **Ignoruj przypadek** opcja kontrolowania uwzględnianie wielkości liter porównania ciągu zapytania.
    
- Ze względu na sposób, w której pamięci podręcznej ustawienia są śledzone ten warunek dopasowania jest niezgodny z następującymi funkcjami:
   - Wypełnienie pamięci podręcznej ukończone
   - Max-Age wewnętrzny domyślny
   - Wymuszanie wewnętrznych Max-Age.
   - Ignoruj pochodzenia No-Cache
   - Internal Max-Stale

#### <a name="sample-scenarios"></a>Przykładowe scenariusze

W poniższym przykładzie pokazano, jak ta opcja działa w określonych sytuacjach:

 Name (Nazwa)                 | Opis
 ---------------------|------------
user=joe              | Ten wzorzec jest dopasowywany gdy ciąg zapytania dla żądanego adresu URL jest "? użytkownika = Jan."
\*user=\* \*optout=\* | Ten wzorzec jest dopasowywany gdy zapytanie adresu URL usługi CDN zawiera użytkownika lub parametru optout.

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

## <a name="next-steps"></a>Kolejne kroki

- [Omówienie usługi Azure Content Delivery Network](cdn-overview.md)
- [Dokumentacja aparatu reguł](cdn-verizon-premium-rules-engine-reference.md)
- [Wyrażenia warunkowe aparatu reguł](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funkcje aparatu reguł](cdn-verizon-premium-rules-engine-reference-features.md)
- [Zastępowanie domyślnego zachowania HTTP przy użyciu aparatu reguł](cdn-verizon-premium-rules-engine.md)
