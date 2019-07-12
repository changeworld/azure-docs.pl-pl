---
title: Usługa Azure CDN from Verizon — Premium reguł warunki dopasowań aparatu | Dokumentacja firmy Microsoft
description: Dokumentacja dotycząca usługi Azure Content Delivery Network w warstwie Premium firmy Verizon reguł warunki dopasowań aparatu.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 1660dca34b2f128ef5889145fcdeed0d2523b9bb
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593197"
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

Name (Nazwa) | Cel
-----|--------
[zawsze](#always) | Stosuje się domyślny zestaw funkcji do wszystkich żądań.

## <a name="device-match-condition"></a>Warunek dopasowania urządzenia

Warunek dopasowania urządzenia identyfikuje żądań wysyłanych z urządzenia przenośnego na podstawie jego właściwości.  

Name (Nazwa) | Cel
-----|--------
[urządzenia](#device) | Identyfikuje żądań wysyłanych z urządzenia przenośnego na podstawie jego właściwości.

## <a name="location-match-conditions"></a>Warunki dopasowań lokalizacji

Warunki dopasowań lokalizacji zidentyfikować żądania na podstawie lokalizacji żądającego.

Name (Nazwa) | Cel
-----|--------
[JAKO liczba](#as-number) | Identyfikuje żądań pochodzących z określonej sieci.
[Country](#country) | Identyfikuje żądań pochodzących z określonego krajów/regionów.

## <a name="origin-match-conditions"></a>Warunki dopasowań źródła

Warunki dopasowań źródła identyfikowania żądań, które wskazują Content Delivery Network magazynu lub serwer pochodzenia klienta.

Name (Nazwa) | Cel
-----|--------
[Źródło usługi CDN](#cdn-origin) | Identyfikuje żądań dotyczących zawartości przechowywanej w magazynie Content Delivery Network.
[Źródłem klienta](#customer-origin) | Identyfikuje żądań dotyczących zawartości przechowywanej w serwer pochodzenia określonego klienta.

## <a name="request-match-conditions"></a>Warunki dopasowań żądania

Warunki dopasowań żądania zidentyfikować żądania na podstawie ich właściwości.

Name (Nazwa) | Cel
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

Name (Nazwa) | Cel
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
- Składnia blok adresów IP jest adres IP podstawowej następuje ukośnik i rozmiar prefiksu. Przykład:
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

### <a name="country"></a>Country

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

Type     | Opis
---------|------------
literał  | Wybierz tę opcję, aby uniemożliwić większości znaków tworzenia specjalnego znaczenia przy użyciu ich [wartości literału](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Symbol wieloznaczny | Wybierz tę opcję, aby móc korzystać z wszystkich [symboli wieloznacznych] ([wartości symboli wieloznacznych](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
Regex    | Wybierz tę opcję, aby użyć [wyrażeń regularnych](cdn-verizon-premium-rules-engine-reference.md#regular-expressions). Wyrażenia regularne są przydatne w przypadku definiowania wzorzec znaków.

#### <a name="wurfl-capabilities"></a>Możliwości WURFL

Możliwość WURFL odnosi się do kategorii, w tym artykule opisano urządzeń przenośnych. Wybrane możliwości Określa typ opisu urządzenia przenośnego, który służy do identyfikowania żądań.

W poniższej tabeli wymieniono możliwości WURFL oraz ich zmienne dla aparatu reguł.

> [!NOTE]
> Następujące zmienne są obsługiwane w **modyfikowania nagłówka żądania klienta** i **zmodyfikować nagłówek odpowiedzi klienta** funkcji.

Możliwość | Zmienna | Opis | Przykładowe wartości
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
     - CDN URL: http://wpc.0001.&lt ;domain&gt; /800001/CustomerOrigin/path/asset.htm
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

Value                   | Względem    | Wynik 
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

Name (Nazwa)  | Value |  Wynik
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

   Note that percentage symbols must be escaped.

- Double-escape special regular expression characters (for example, \^$.+) to include a backslash in the regular expression.

   For example:

   Value | Interpreted As 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Wildcard

Compares the specified value(s) against the request's query string.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified wildcard value.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified wildcard value.

Key information:

- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
- Parameter values can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values):
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

     Character | URL Encoding
     ----------|---------
     Space     | %20
     &         | %25

- Specify multiple values by delimiting each one with a single space.

   For example: *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- Only exact matches to at least one of the specified query string patterns satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

 Name                 | Description
 ---------------------|------------
user=joe              | This pattern is matched when the query string for a requested URL is "?user=joe."
\*user=\* \*optout=\* | This pattern is matched when the CDN URL query contains either the user or optout parameter.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

## Next steps

- [Azure Content Delivery Network overview](cdn-overview.md)
- [Rules engine reference](cdn-verizon-premium-rules-engine-reference.md)
- [Rules engine conditional expressions](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Rules engine features](cdn-verizon-premium-rules-engine-reference-features.md)
- [Overriding default HTTP behavior using the rules engine](cdn-verizon-premium-rules-engine.md)\`20
title: Azure CDN from Verizon Premium rules engine match conditions | Microsoft Docs
description: Reference documentation for Azure Content Delivery Network from Verizon Premium rules engine match conditions.
services: cdn
author: mdgattuso

ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus

---

# Azure CDN from Verizon Premium rules engine match conditions

This article lists detailed descriptions of the available match conditions for the Azure Content Delivery Network (CDN) from Verizon Premium [rules engine](cdn-verizon-premium-rules-engine.md).

The second part of a rule is the match condition. A match condition identifies specific types of requests for which a set of features will be performed.

For example, you can use a match condition to:

- Filter requests for content at a particular location.
- Filter requests generated from a particular IP address or country/region.
- Filter requests by header information.

## Always match condition

The Always match condition applies a default set of features to all requests.

Name | Purpose
-----|--------
[Always](#always) | Applies a default set of features to all requests.

## Device match condition

The Device match condition identifies requests made from a mobile device based on its properties.  

Name | Purpose
-----|--------
[Device](#device) | Identifies requests made from a mobile device based on its properties.

## Location match conditions

The Location match conditions identify requests based on the requester's location.

Name | Purpose
-----|--------
[AS Number](#as-number) | Identifies requests that originate from a particular network.
[Country](#country) | Identifies requests that originate from the specified countries/regions.

## Origin match conditions

The Origin match conditions identify requests that point to Content Delivery Network storage or a customer origin server.

Name | Purpose
-----|--------
[CDN Origin](#cdn-origin) | Identifies requests for content stored in Content Delivery Network storage.
[Customer Origin](#customer-origin) | Identifies requests for content stored on a specific customer origin server.

## Request match conditions

The Request match conditions identify requests based on their properties.

Name | Purpose
-----|--------
[Client IP Address](#client-ip-address) | Identifies requests that originate from a particular IP address.
[Cookie Parameter](#cookie-parameter) | Checks the cookies associated with each request for the specified value.
[Cookie Parameter Regex](#cookie-parameter-regex) | Checks the cookies associated with each request for the specified regular expression.
[Edge Cname](#edge-cname) | Identifies requests that point to a specific edge CNAME.
[Referring Domain](#referring-domain) | Identifies requests that were referred from the specified host names.
[Request Header Literal](#request-header-literal) | Identifies requests that contain the specified header set to a specified value.
[Request Header Regex](#request-header-regex) | Identifies requests that contain the specified header set to a value that matches the specified regular expression.
[Request Header Wildcard](#request-header-wildcard) | Identifies requests that contain the specified header set to a value that matches the specified pattern.
[Request Method](#request-method) | Identifies requests by their HTTP method.
[Request Scheme](#request-scheme) | Identifies requests by their HTTP protocol.

## URL match conditions

The URL match conditions identify requests based on their URLs.

Name | Purpose
-----|--------
[URL Path Directory](#url-path-directory) | Identifies requests by their relative path.
[URL Path Extension](#url-path-extension) | Identifies requests by their file name extension.
[URL Path Filename](#url-path-filename) | Identifies requests by their file name.
[URL Path Literal](#url-path-literal) | Compares a request's relative path to the specified value.
[URL Path Regex](#url-path-regex) | Compares a request's relative path to the specified regular expression.
[URL Path Wildcard](#url-path-wildcard) | Compares a request's relative path to the specified pattern.
[URL Query Literal](#url-query-literal) | Compares a request's query string to the specified value.
[URL Query Parameter](#url-query-parameter) | Identifies requests that contain the specified query string parameter set to a value that matches a specified pattern.
[URL Query Regex](#url-query-regex) | Identifies requests that contain the specified query string parameter set to a value that matches a specified regular expression.
[URL Query Wildcard](#url-query-wildcard) | Compares the specified value against the request's query string.

## Reference for rules engine match conditions

---

### Always

The Always match condition applies a default set of features to all requests.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### AS Number

The AS Number network is defined by its autonomous system number (ASN). 

The **Matches**/**Does Not Match** option determines the conditions under which the AS Number match condition is met:

- **Matches**: Requires that the ASN of the client network matches one of the specified ASNs. 
- **Does Not Match**: Requires that the ASN of the client network does not match any of the specified ASNs.

Key information:

- Specify multiple ASNs by delimiting each one with a single space. For example, 64514 64515 matches requests that arrive from either 64514 or 64515.
- Certain requests might not return a valid ASN. A question mark (?) will match requests for which a valid ASN could not be determined.
- Specify the entire ASN for the desired network. Partial values will not be matched.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### CDN Origin

The CDN Origin match condition is met when both of the following conditions are met:

- Content from CDN storage was requested.
- The request URI uses the type of content access point (for example, /000001) that's defined in this match condition:
  - CDN URL: The request URI must contain the selected content access point.
  - Edge CNAME URL: The corresponding edge CNAME configuration must point to the selected content access point.
  
Key information:

- The content access point identifies the service that should serve the requested content.
- Don't use an AND IF statement to combine certain match conditions. For example, combining a CDN Origin match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two CDN Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Client IP Address

The **Matches**/**Does Not Match** option determines the conditions under which the Client IP Address match condition is met:

- **Matches**: Requires that the client's IP address matches one of the specified IP addresses. 
- **Does Not Match**: Requires that the client's IP address does not match any of the specified IP addresses. 

Key information:

- Use CIDR notation.
- Specify multiple IP addresses and/or IP address blocks by delimiting each one with a single space. For example:
  - **IPv4 example**: 1.2.3.4 10.20.30.40 matches any requests that arrive from either address 1.2.3.4 or 10.20.30.40.
  - **IPv6 example**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 matches any requests that arrive from either address 1:2:3:4:5:6:7:8 or 10:20:30:40:50:60:70:80.
- The syntax for an IP address block is the base IP address followed by a forward slash and the prefix size. For example:
  - **IPv4 example**: 5.5.5.64/26 matches any requests that arrive from addresses 5.5.5.64 through 5.5.5.127.
  - **IPv6 example**: 1:2:3:/48 matches any requests that arrive from addresses 1:2:3:0:0:0:0:0 through 1:2:3:ffff:ffff:ffff:ffff:ffff.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Cookie Parameter

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match any of the values that are defined in this match condition.
  
Key information:

- Cookie name:
  - Because wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - Specify multiple cookie values by delimiting each one with a single space.
  - A cookie value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - If a wildcard value has not been specified, then only an exact match will satisfy this match condition. For example, specifying "Value" will match "Value," but not "Value1" or "Value2."
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)
</br>

---

### Cookie Parameter Regex

The Cookie Parameter Regex match condition defines a cookie name and value. You can use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) to define the desired cookie value.

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter Regex match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches the specified regular expression.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match the specified regular expression.
  
Key information:

- Cookie name:
  - Because regular expressions and wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - A cookie value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Country

You can specify a country through its country code. 

The **Matches**/**Does Not Match** option determines the conditions under which the Country match condition is met:

- **Matches**: Requires the request to contain the specified country code values. 
- **Does Not Match**: Requires that the request does not contain the specified country code values.

Key information:

- Specify multiple country codes by delimiting each one with a single space.
- Wildcards are not supported when you're specifying a country code.
- The "EU" and "AP" country codes do not encompass all IP addresses in those regions.
- Certain requests might not return a valid country code. A question mark (?) will match requests for which a valid country code could not be determined.
- Country codes are case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### Implementing Country Filtering by using the rules engine

This match condition allows you to perform a multitude of customizations based on the location from which a request originated. For example, the behavior of the Country Filtering feature can be replicated through the following configuration:

- URL Path Wildcard match: Set the [URL Path Wildcard match condition](#url-path-wildcard) to the directory that will be secured. 
    Append an asterisk to the end of the relative path to ensure that access to all of its children will be restricted by this rule.

- Country match: Set the Country match condition to the desired set of countries.
  - Allow: Set the Country match condition to **Does Not Match** to allow only the specified countries access to content stored in the location defined by the URL Path Wildcard match condition.
  - Block: Set the Country match condition to **Matches** to block the specified countries from accessing content stored in the location defined by the URL Path Wildcard match condition.

- Deny Access (403) Feature: Enable the [Deny Access (403) feature](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) to replicate the allow or block portion of the Country Filtering feature.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Customer Origin

Key information:

- The Customer Origin match condition is met regardless of whether content is requested through a CDN URL or an edge CNAME URL that points to the selected customer origin.
- A customer origin configuration that's referenced by a rule cannot be deleted from the Customer Origin page. Before you attempt to delete a customer origin configuration, make sure that the following configurations do not reference it:
  - A Customer Origin match condition
  - An edge CNAME configuration
- Don't use an AND IF statement to combine certain match conditions. For example, combining a Customer Origin match condition with a CDN Origin match condition would create a match pattern that could never be matched. For this reason, two Customer Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Device

The Device match condition identifies requests made from a mobile device based on its properties. Mobile device detection is achieved through [WURFL](http://wurfl.sourceforge.net/). 

The **Matches**/**Does Not Match** option determines the conditions under which the Device match condition is met:

- **Matches**: Requires the requester's device to match the specified value. 
- **Does Not Match**: Requires that the requester's device does not match the specified value.

Key information:

- Use the **Ignore Case** option to specify whether the specified value is case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### String Type

A WURFL capability typically accepts any combination of numbers, letters, and symbols. Due to the flexible nature of this capability, you must choose how the value associated with this match condition is interpreted. The following table describes the available set of options:

Type     | Description
---------|------------
Literal  | Select this option to prevent most characters from taking on special meaning by using their [literal value](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Wildcard | Select this option to take advantage of all [wildcard characters]([wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
Regex    | Select this option to use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions). Regular expressions are useful for defining a pattern of characters.

#### WURFL capabilities

A WURFL capability refers to a category that describes mobile devices. The selected capability determines the type of mobile device description that is used to identify requests.

The following table lists WURFL capabilities and their variables for the rules engine.

> [!NOTE]
> The following variables are supported in the **Modify Client Request Header** and **Modify Client Response Header** features.

Capability | Variable | Description | Sample values
-----------|----------|-------------|----------------
Brand Name | %{wurfl_cap_brand_name} | A string that indicates the brand name of the device. | Samsung
Device OS | %{wurfl_cap_device_os} | A string that indicates the operating system installed on the device. | IOS
Device OS Version | %{wurfl_cap_device_os_version} | A string that indicates the version number of the operating system installed on the device. | 1.0.1
Dual Orientation | %{wurfl_cap_dual_orientation} | A Boolean that indicates whether the device supports dual orientation. | true
HTML Preferred DTD | %{wurfl_cap_html_preferred_dtd} | A string that indicates the mobile device's preferred document type definition (DTD) for HTML content. | none<br/>xhtml_basic<br/>html5
Image Inlining | %{wurfl_cap_image_inlining} | A Boolean that indicates whether the device supports Base64 encoded images. | false
Is Android | %{wurfl_vcap_is_android} | A Boolean that indicates whether the device uses the Android OS. | true
Is IOS | %{wurfl_vcap_is_ios} | A Boolean that indicates whether the device uses iOS. | false
Is Smart TV | %{wurfl_cap_is_smarttv} | A Boolean that indicates whether the device is a smart TV. | false
Is Smartphone | %{wurfl_vcap_is_smartphone} | A Boolean that indicates whether the device is a smartphone. | true
Is Tablet | %{wurfl_cap_is_tablet} | A Boolean that indicates whether the device is a tablet. This description is  OS-independent. | true
Is Wireless Device | %{wurfl_cap_is_wireless_device} | A Boolean that indicates whether the device is considered a wireless device. | true
Marketing Name | %{wurfl_cap_marketing_name} | A string that indicates the device's marketing name. | BlackBerry 8100 Pearl
Mobile Browser | %{wurfl_cap_mobile_browser} | A string that indicates the browser that's used to request content from the device. | Chrome
Mobile Browser Version | %{wurfl_cap_mobile_browser_version} | A string that indicates the version of the browser that's used to request content from the device. | 31
Model Name | %{wurfl_cap_model_name} | A string that indicates the device's model name. | s3
Progressive Download | %{wurfl_cap_progressive_download} | A Boolean that indicates whether the device supports the playback of audio and video while it is still being downloaded. | true
Release Date | %{wurfl_cap_release_date} | A string that indicates the year and month on which the device was added to the WURFL database.<br/><br/>Format: `yyyy_mm` | 2013_december
Resolution Height | %{wurfl_cap_resolution_height} | An integer that indicates the device's height in pixels. | 768
Resolution Width | %{wurfl_cap_resolution_width} | An integer that indicates the device's width in pixels. | 1024

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Edge Cname

Key information:

- The list of available edge CNAMEs is limited to those edge CNAMEs that have been configured on the Edge CNAMEs page for the platform on which the rules engine is being configured.
- Before you attempt to delete an edge CNAME configuration, make sure that an Edge Cname match condition does not reference it. Edge CNAME configurations that have been defined in a rule cannot be deleted from the Edge CNAMEs page.
- Don't use an AND IF statement to combine certain match conditions. For example, combining an Edge Cname match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two Edge Cname match conditions cannot be combined through an AND IF statement.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Referring Domain

The host name associated with the referrer through which content was requested determines whether the Referring Domain condition is met.

The **Matches**/**Does Not Match** option determines the conditions under which the Referring Domain match condition is met:

- **Matches**: Requires the referring host name to match the specified values. 
- **Does Not Match**: Requires that the referring host name does not match the specified value.

Key information:

- Specify multiple host names by delimiting each one with a single space.
- This match condition supports [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- If the specified value does not contain an asterisk, it must be an exact match for the referrer's host name. For example, specifying "mydomain.com" would not match "www.mydomain.com."
- Use the **Ignore Case** option to control whether a case-sensitive comparison is made.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Literal

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Literal match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the one that's defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the one that's defined in this match condition.
  
Key information:

- Header name comparisons are always case-insensitive. Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Regex

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Regex match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the pattern that's defined in the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the specified regular expression.

Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Replace spaces in the header name with "%20."
- Header value:
  - A header value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - The match condition is met only when a header value exactly matches at least one of the specified patterns.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Header Wildcard

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Wildcard match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match any of the specified values.
  
Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Spaces in the header name should be replaced with "%20." You can also use this value to specify spaces in a header value.
- Header value:
  - A header value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - This match condition is met when a header value exactly matches to at least one of the specified patterns.
  - Specify multiple values by delimiting each one with a single space.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Method

The Request Method match condition is met only when assets are requested through the selected request method. The available request methods are:

- GET
- HEAD
- POST
- OPTIONS
- PUT
- DELETE
- TRACE
- CONNECT

Key information:

- By default, only the GET request method can generate cached content on the network. All other request methods are proxied through the network.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Scheme

The Request Scheme match condition is met only when assets are requested through the selected protocol. The available protocols are:

- HTTP
- HTTPS

Key information:

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Directory

Identifies a request by its relative path, which excludes the file name of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Directory match condition is met.

- **Matches**: Requires the request to contain a relative URL path, excluding the file name, that matches the specified URL pattern.
- **Does Not Match**: Requires the request to contain a relative URL path, excluding file name, that does not match the specified URL pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison starts before or after the content access point. The content access point is the portion of the path that appears between the Verizon CDN hostname and the relative path to the requested asset (for example, /800001/CustomerOrigin). It identifies a location by server type (for example, CDN or customer origin) and your customer account number.

   The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname. 

  For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder**/index.htm

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

  For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder**/index.htm 

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder**/index.htm

- An edge CNAME URL is rewritten to a CDN URL prior to the URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path.
  - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
    
  - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
  - Custom domain: https:\//my.domain.com/path/asset.htm
    
    - URL path (relative to root): /800001/CustomerOrigin/path/
    
    - URL path (relative to origin): /path/

- The portion of the URL that is used for the URL comparison ends just before the file name of the requested asset. A trailing forward slash is the last character in this type of path.

- Replace any spaces in the URL path pattern with "%20."

- Each URL path pattern can contain one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- Specify multiple URL paths in the pattern by delimiting each one with a single space.

    For example: */sales/ */marketing/

- A URL path specification can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Extension

Identifies requests by the file extension of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Extension match condition is met.

- **Matches**: Requires the URL of the request to contain a file extension that exactly matches the specified pattern.

   For example, if you specify "htm", "htm" assets are matched, but not "html" assets.  

- **Does Not Match**: Requires the URL request to contain a file extension that does not match the specified pattern.

Key information:

- Specify the file extensions to match in the **Value** box. Do not include a leading period; for example, use htm instead of .htm.

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- Specify multiple file extensions by delimiting each extension with a single space. 

    For example: htm html

- For example, specifying "htm" matches "htm" assets, but not "html" assets.

#### Sample Scenario

The following sample configuration assumes that this match condition is met when a request matches one of the specified extensions.

Value specification: asp aspx php html

This match condition is met when it finds URLs that end with the following extensions:

- .asp
- .aspx
- .php
- .html

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Filename

Identifies requests by the file name of the requested asset. For the purposes of this match condition, a file name consists of the name of the requested asset, a period, and the file extension (for example, index.html).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Filename match condition is met.

- **Matches**: Requires the request to contain a file name in its URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a file name in its URL path that does not match the specified pattern.

Key information:

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- To specify multiple file extensions, separate each extension with a single space.

    For example: index.htm index.html

- Replace spaces in a file name value with "%20."

- A file name value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). For example, each file name pattern can consist of one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- If wildcard characters are not specified, then only an exact match will satisfy this match condition.

    For example, specifying "presentation.ppt" matches an asset named "presentation.ppt," but not one named "presentation.pptx."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Literal

Compares a request's URL path, including file name, to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Literal match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison point begins before or after the content access point. 

    The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

    For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

    For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to a URL comparison.

For example, both of the following URLs point to the same asset and therefore have the same URL path:

- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
- Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

    Additional information:
    
    - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
   
    - URL path (relative to origin): /path/asset.htm

- Query strings in the URL are ignored.
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
- The value specified for this match condition is compared against the relative path of the exact request made by the client.

- To match all requests made to a particular directory, use the [URL Path Directory](#url-path-directory) or the [URL Path Wildcard](#url-path-wildcard) match condition.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Regex

Compares a request's URL path to the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Regex match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified regular expression.

Key information:

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both URLs point to the same asset and therefore have the same URL path.

     - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm

     - Edge CNAME URL: http:\//my.domain.com/path/asset.htm

    Additional information:
    
     - URL path: /800001/CustomerOrigin/path/asset.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Spaces in the URL path should be replaced with "%20."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Wildcard

Compares a request's relative URL path to the specified wildcard pattern.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified wildcard pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified wildcard pattern.

Key information:

- **Relative to** option: This option determines whether the URL comparison point begins before or after the content access point.

   This option can have the following values:
     - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

       For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

     - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

       For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

     This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path:
     - CDN URL: http://wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
     - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
    
     - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
    
     - URL path (relative to origin): /path/asset.htm
    
- Specify multiple URL paths by delimiting each one with a single space.

   For example: /marketing/asset.* /sales/*.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Replace spaces in the URL path with "%20."
    
- The value specified for a URL path can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Each URL path pattern can contain one or more asterisks (*), where each asterisk can match a sequence of one or more characters.

#### Sample Scenarios

The sample configurations in the following table assume that this match condition is met when a request matches the specified URL pattern:

Value                   | Relative to    | Result 
------------------------|----------------|-------
*/test.html */test.php  | Root or Origin | This pattern is matched by requests for assets named "test.html" or "test.php" in any folder.
/80ABCD/origin/text/*   | Root           | This pattern is matched when the requested asset meets the following criteria: <br />- It must reside on a customer origin called "origin." <br />- The relative path must start with a folder called "text." That is, the requested asset can either reside in the "text" folder or one of its recursive subfolders.
*/css/* */js/*          | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs that contain a css or js folder.
*.jpg *.gif *.png       | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs ending with .jpg, .gif, or .png. An alternative way to specify this pattern is with the [URL Path Extension match condition](#url-path-extension).
/images/* /media/*      | Origin         | This pattern is matched by CDN or edge CNAME URLs whose relative path starts with an "images" or "media" folder. <br />- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/images/sales/event1.png<br />- Sample edge CNAME URL: http:\//cdn.mydomain.com/images/sales/event1.png

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Literal

Compares a request's query string to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Literal match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified query string.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified query string.

Key information:

- Only exact query string matches satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Do not include a leading question mark (?) in the query string value text.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding
   ----------|---------
   Space     | %20
   &         | %25

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Parameter

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified pattern. Query string parameters (for example, parameter=value) in the request URL determine whether this condition is met. This match condition identifies a query string parameter by its name and accepts one or more values for the parameter value. 

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Parameter match condition is met.

- **Matches**: Requires a request to contain the specified parameter with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified parameter.
  - It contains the specified parameter, but its value does not match any of the values that are defined in this match condition.

This match condition provides an easy way to specify parameter name/value combinations. For more flexibility if you are matching a query string parameter, consider using the [URL Query Wildcard](#url-query-wildcard) match condition.

Key information:

- Only a single URL query parameter name can be specified per instance of this match condition.
    
- Because wildcard values are not supported when a parameter name is specified, only exact parameter name matches are eligible for comparison.
- Parameter value(s) can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

       Character | URL Encoding
       ----------|---------
       Space     | %20
       &         | %25

- Specify multiple query string parameter values by delimiting each one with a single space. This match condition is met when a request contains one of the specified name/value combinations.

   - Example 1:

     - Configuration:

       ValueA ValueB

     - This configuration matches the following query string parameters:

       Parameter1=ValueA
    
       Parameter1=ValueB

   - Example 2:

     - Configuration: 

        Value%20A Value%20B

     - This configuration matches the following query string parameters:

       Parameter1=Value%20A

       Parameter1=Value%20B

- This match condition is met only when there is an exact match to at least one of the specified query string name/value combinations.

   For example, if you use the configuration in the previous example, the parameter name/value combination "Parameter1=ValueAdd" would not be considered a match. However, if you specify either of the following values, it will match that name/value combination:

   - ValueA ValueB ValueAdd
   - ValueA* ValueB

- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

Name  | Value |  Result
------|-------|--------
User  | Joe   | This pattern is matched when the query string for a requested URL is "?user=joe."
User  | *     | This pattern is matched when the query string for a requested URL contains a User parameter.
Email | Joe\* | This pattern is matched when the query string for a requested URL contains an Email parameter that starts with "Joe."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Regex

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Regex match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified regular expression.

Key information:

- Only exact matches to the specified regular expression satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding | Value
   ----------|--------------|------
   Space     | %20          | \%20
   &         | %25          | \%25

   Note that percentage symbols must be escaped.

- Double-escape special regular expression characters (for example, \^$.+) to include a backslash in the regular expression.

   For example:

   Value | Interpreted As 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Wildcard

Compares the specified value(s) against the request's query string.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified wildcard value.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified wildcard value.

Key information:

- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
- Parameter values can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values):
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

     Character | URL Encoding
     ----------|---------
     Space     | %20
     &         | %25

- Specify multiple values by delimiting each one with a single space.

   For example: *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- Only exact matches to at least one of the specified query string patterns satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

 Name                 | Description
 ---------------------|------------
user=joe              | This pattern is matched when the query string for a requested URL is "?user=joe."
\*user=\* \*optout=\* | This pattern is matched when the CDN URL query contains either the user or optout parameter.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

## Next steps

- [Azure Content Delivery Network overview](cdn-overview.md)
- [Rules engine reference](cdn-verizon-premium-rules-engine-reference.md)
- [Rules engine conditional expressions](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Rules engine features](cdn-verizon-premium-rules-engine-reference-features.md)
- [Overriding default HTTP behavior using the rules engine](cdn-verizon-premium-rules-engine.md)\`25
title: Azure CDN from Verizon Premium rules engine match conditions | Microsoft Docs
description: Reference documentation for Azure Content Delivery Network from Verizon Premium rules engine match conditions.
services: cdn
author: mdgattuso

ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus

---

# Azure CDN from Verizon Premium rules engine match conditions

This article lists detailed descriptions of the available match conditions for the Azure Content Delivery Network (CDN) from Verizon Premium [rules engine](cdn-verizon-premium-rules-engine.md).

The second part of a rule is the match condition. A match condition identifies specific types of requests for which a set of features will be performed.

For example, you can use a match condition to:

- Filter requests for content at a particular location.
- Filter requests generated from a particular IP address or country/region.
- Filter requests by header information.

## Always match condition

The Always match condition applies a default set of features to all requests.

Name | Purpose
-----|--------
[Always](#always) | Applies a default set of features to all requests.

## Device match condition

The Device match condition identifies requests made from a mobile device based on its properties.  

Name | Purpose
-----|--------
[Device](#device) | Identifies requests made from a mobile device based on its properties.

## Location match conditions

The Location match conditions identify requests based on the requester's location.

Name | Purpose
-----|--------
[AS Number](#as-number) | Identifies requests that originate from a particular network.
[Country](#country) | Identifies requests that originate from the specified countries/regions.

## Origin match conditions

The Origin match conditions identify requests that point to Content Delivery Network storage or a customer origin server.

Name | Purpose
-----|--------
[CDN Origin](#cdn-origin) | Identifies requests for content stored in Content Delivery Network storage.
[Customer Origin](#customer-origin) | Identifies requests for content stored on a specific customer origin server.

## Request match conditions

The Request match conditions identify requests based on their properties.

Name | Purpose
-----|--------
[Client IP Address](#client-ip-address) | Identifies requests that originate from a particular IP address.
[Cookie Parameter](#cookie-parameter) | Checks the cookies associated with each request for the specified value.
[Cookie Parameter Regex](#cookie-parameter-regex) | Checks the cookies associated with each request for the specified regular expression.
[Edge Cname](#edge-cname) | Identifies requests that point to a specific edge CNAME.
[Referring Domain](#referring-domain) | Identifies requests that were referred from the specified host names.
[Request Header Literal](#request-header-literal) | Identifies requests that contain the specified header set to a specified value.
[Request Header Regex](#request-header-regex) | Identifies requests that contain the specified header set to a value that matches the specified regular expression.
[Request Header Wildcard](#request-header-wildcard) | Identifies requests that contain the specified header set to a value that matches the specified pattern.
[Request Method](#request-method) | Identifies requests by their HTTP method.
[Request Scheme](#request-scheme) | Identifies requests by their HTTP protocol.

## URL match conditions

The URL match conditions identify requests based on their URLs.

Name | Purpose
-----|--------
[URL Path Directory](#url-path-directory) | Identifies requests by their relative path.
[URL Path Extension](#url-path-extension) | Identifies requests by their file name extension.
[URL Path Filename](#url-path-filename) | Identifies requests by their file name.
[URL Path Literal](#url-path-literal) | Compares a request's relative path to the specified value.
[URL Path Regex](#url-path-regex) | Compares a request's relative path to the specified regular expression.
[URL Path Wildcard](#url-path-wildcard) | Compares a request's relative path to the specified pattern.
[URL Query Literal](#url-query-literal) | Compares a request's query string to the specified value.
[URL Query Parameter](#url-query-parameter) | Identifies requests that contain the specified query string parameter set to a value that matches a specified pattern.
[URL Query Regex](#url-query-regex) | Identifies requests that contain the specified query string parameter set to a value that matches a specified regular expression.
[URL Query Wildcard](#url-query-wildcard) | Compares the specified value against the request's query string.

## Reference for rules engine match conditions

---

### Always

The Always match condition applies a default set of features to all requests.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### AS Number

The AS Number network is defined by its autonomous system number (ASN). 

The **Matches**/**Does Not Match** option determines the conditions under which the AS Number match condition is met:

- **Matches**: Requires that the ASN of the client network matches one of the specified ASNs. 
- **Does Not Match**: Requires that the ASN of the client network does not match any of the specified ASNs.

Key information:

- Specify multiple ASNs by delimiting each one with a single space. For example, 64514 64515 matches requests that arrive from either 64514 or 64515.
- Certain requests might not return a valid ASN. A question mark (?) will match requests for which a valid ASN could not be determined.
- Specify the entire ASN for the desired network. Partial values will not be matched.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### CDN Origin

The CDN Origin match condition is met when both of the following conditions are met:

- Content from CDN storage was requested.
- The request URI uses the type of content access point (for example, /000001) that's defined in this match condition:
  - CDN URL: The request URI must contain the selected content access point.
  - Edge CNAME URL: The corresponding edge CNAME configuration must point to the selected content access point.
  
Key information:

- The content access point identifies the service that should serve the requested content.
- Don't use an AND IF statement to combine certain match conditions. For example, combining a CDN Origin match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two CDN Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Client IP Address

The **Matches**/**Does Not Match** option determines the conditions under which the Client IP Address match condition is met:

- **Matches**: Requires that the client's IP address matches one of the specified IP addresses. 
- **Does Not Match**: Requires that the client's IP address does not match any of the specified IP addresses. 

Key information:

- Use CIDR notation.
- Specify multiple IP addresses and/or IP address blocks by delimiting each one with a single space. For example:
  - **IPv4 example**: 1.2.3.4 10.20.30.40 matches any requests that arrive from either address 1.2.3.4 or 10.20.30.40.
  - **IPv6 example**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 matches any requests that arrive from either address 1:2:3:4:5:6:7:8 or 10:20:30:40:50:60:70:80.
- The syntax for an IP address block is the base IP address followed by a forward slash and the prefix size. For example:
  - **IPv4 example**: 5.5.5.64/26 matches any requests that arrive from addresses 5.5.5.64 through 5.5.5.127.
  - **IPv6 example**: 1:2:3:/48 matches any requests that arrive from addresses 1:2:3:0:0:0:0:0 through 1:2:3:ffff:ffff:ffff:ffff:ffff.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Cookie Parameter

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match any of the values that are defined in this match condition.
  
Key information:

- Cookie name:
  - Because wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - Specify multiple cookie values by delimiting each one with a single space.
  - A cookie value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - If a wildcard value has not been specified, then only an exact match will satisfy this match condition. For example, specifying "Value" will match "Value," but not "Value1" or "Value2."
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)
</br>

---

### Cookie Parameter Regex

The Cookie Parameter Regex match condition defines a cookie name and value. You can use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) to define the desired cookie value.

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter Regex match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches the specified regular expression.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match the specified regular expression.
  
Key information:

- Cookie name:
  - Because regular expressions and wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - A cookie value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Country

You can specify a country through its country code. 

The **Matches**/**Does Not Match** option determines the conditions under which the Country match condition is met:

- **Matches**: Requires the request to contain the specified country code values. 
- **Does Not Match**: Requires that the request does not contain the specified country code values.

Key information:

- Specify multiple country codes by delimiting each one with a single space.
- Wildcards are not supported when you're specifying a country code.
- The "EU" and "AP" country codes do not encompass all IP addresses in those regions.
- Certain requests might not return a valid country code. A question mark (?) will match requests for which a valid country code could not be determined.
- Country codes are case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### Implementing Country Filtering by using the rules engine

This match condition allows you to perform a multitude of customizations based on the location from which a request originated. For example, the behavior of the Country Filtering feature can be replicated through the following configuration:

- URL Path Wildcard match: Set the [URL Path Wildcard match condition](#url-path-wildcard) to the directory that will be secured. 
    Append an asterisk to the end of the relative path to ensure that access to all of its children will be restricted by this rule.

- Country match: Set the Country match condition to the desired set of countries.
  - Allow: Set the Country match condition to **Does Not Match** to allow only the specified countries access to content stored in the location defined by the URL Path Wildcard match condition.
  - Block: Set the Country match condition to **Matches** to block the specified countries from accessing content stored in the location defined by the URL Path Wildcard match condition.

- Deny Access (403) Feature: Enable the [Deny Access (403) feature](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) to replicate the allow or block portion of the Country Filtering feature.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Customer Origin

Key information:

- The Customer Origin match condition is met regardless of whether content is requested through a CDN URL or an edge CNAME URL that points to the selected customer origin.
- A customer origin configuration that's referenced by a rule cannot be deleted from the Customer Origin page. Before you attempt to delete a customer origin configuration, make sure that the following configurations do not reference it:
  - A Customer Origin match condition
  - An edge CNAME configuration
- Don't use an AND IF statement to combine certain match conditions. For example, combining a Customer Origin match condition with a CDN Origin match condition would create a match pattern that could never be matched. For this reason, two Customer Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Device

The Device match condition identifies requests made from a mobile device based on its properties. Mobile device detection is achieved through [WURFL](http://wurfl.sourceforge.net/). 

The **Matches**/**Does Not Match** option determines the conditions under which the Device match condition is met:

- **Matches**: Requires the requester's device to match the specified value. 
- **Does Not Match**: Requires that the requester's device does not match the specified value.

Key information:

- Use the **Ignore Case** option to specify whether the specified value is case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### String Type

A WURFL capability typically accepts any combination of numbers, letters, and symbols. Due to the flexible nature of this capability, you must choose how the value associated with this match condition is interpreted. The following table describes the available set of options:

Type     | Description
---------|------------
Literal  | Select this option to prevent most characters from taking on special meaning by using their [literal value](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Wildcard | Select this option to take advantage of all [wildcard characters]([wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
Regex    | Select this option to use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions). Regular expressions are useful for defining a pattern of characters.

#### WURFL capabilities

A WURFL capability refers to a category that describes mobile devices. The selected capability determines the type of mobile device description that is used to identify requests.

The following table lists WURFL capabilities and their variables for the rules engine.

> [!NOTE]
> The following variables are supported in the **Modify Client Request Header** and **Modify Client Response Header** features.

Capability | Variable | Description | Sample values
-----------|----------|-------------|----------------
Brand Name | %{wurfl_cap_brand_name} | A string that indicates the brand name of the device. | Samsung
Device OS | %{wurfl_cap_device_os} | A string that indicates the operating system installed on the device. | IOS
Device OS Version | %{wurfl_cap_device_os_version} | A string that indicates the version number of the operating system installed on the device. | 1.0.1
Dual Orientation | %{wurfl_cap_dual_orientation} | A Boolean that indicates whether the device supports dual orientation. | true
HTML Preferred DTD | %{wurfl_cap_html_preferred_dtd} | A string that indicates the mobile device's preferred document type definition (DTD) for HTML content. | none<br/>xhtml_basic<br/>html5
Image Inlining | %{wurfl_cap_image_inlining} | A Boolean that indicates whether the device supports Base64 encoded images. | false
Is Android | %{wurfl_vcap_is_android} | A Boolean that indicates whether the device uses the Android OS. | true
Is IOS | %{wurfl_vcap_is_ios} | A Boolean that indicates whether the device uses iOS. | false
Is Smart TV | %{wurfl_cap_is_smarttv} | A Boolean that indicates whether the device is a smart TV. | false
Is Smartphone | %{wurfl_vcap_is_smartphone} | A Boolean that indicates whether the device is a smartphone. | true
Is Tablet | %{wurfl_cap_is_tablet} | A Boolean that indicates whether the device is a tablet. This description is  OS-independent. | true
Is Wireless Device | %{wurfl_cap_is_wireless_device} | A Boolean that indicates whether the device is considered a wireless device. | true
Marketing Name | %{wurfl_cap_marketing_name} | A string that indicates the device's marketing name. | BlackBerry 8100 Pearl
Mobile Browser | %{wurfl_cap_mobile_browser} | A string that indicates the browser that's used to request content from the device. | Chrome
Mobile Browser Version | %{wurfl_cap_mobile_browser_version} | A string that indicates the version of the browser that's used to request content from the device. | 31
Model Name | %{wurfl_cap_model_name} | A string that indicates the device's model name. | s3
Progressive Download | %{wurfl_cap_progressive_download} | A Boolean that indicates whether the device supports the playback of audio and video while it is still being downloaded. | true
Release Date | %{wurfl_cap_release_date} | A string that indicates the year and month on which the device was added to the WURFL database.<br/><br/>Format: `yyyy_mm` | 2013_december
Resolution Height | %{wurfl_cap_resolution_height} | An integer that indicates the device's height in pixels. | 768
Resolution Width | %{wurfl_cap_resolution_width} | An integer that indicates the device's width in pixels. | 1024

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Edge Cname

Key information:

- The list of available edge CNAMEs is limited to those edge CNAMEs that have been configured on the Edge CNAMEs page for the platform on which the rules engine is being configured.
- Before you attempt to delete an edge CNAME configuration, make sure that an Edge Cname match condition does not reference it. Edge CNAME configurations that have been defined in a rule cannot be deleted from the Edge CNAMEs page.
- Don't use an AND IF statement to combine certain match conditions. For example, combining an Edge Cname match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two Edge Cname match conditions cannot be combined through an AND IF statement.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Referring Domain

The host name associated with the referrer through which content was requested determines whether the Referring Domain condition is met.

The **Matches**/**Does Not Match** option determines the conditions under which the Referring Domain match condition is met:

- **Matches**: Requires the referring host name to match the specified values. 
- **Does Not Match**: Requires that the referring host name does not match the specified value.

Key information:

- Specify multiple host names by delimiting each one with a single space.
- This match condition supports [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- If the specified value does not contain an asterisk, it must be an exact match for the referrer's host name. For example, specifying "mydomain.com" would not match "www.mydomain.com."
- Use the **Ignore Case** option to control whether a case-sensitive comparison is made.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Literal

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Literal match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the one that's defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the one that's defined in this match condition.
  
Key information:

- Header name comparisons are always case-insensitive. Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Regex

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Regex match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the pattern that's defined in the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the specified regular expression.

Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Replace spaces in the header name with "%20."
- Header value:
  - A header value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - The match condition is met only when a header value exactly matches at least one of the specified patterns.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Header Wildcard

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Wildcard match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match any of the specified values.
  
Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Spaces in the header name should be replaced with "%20." You can also use this value to specify spaces in a header value.
- Header value:
  - A header value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - This match condition is met when a header value exactly matches to at least one of the specified patterns.
  - Specify multiple values by delimiting each one with a single space.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Method

The Request Method match condition is met only when assets are requested through the selected request method. The available request methods are:

- GET
- HEAD
- POST
- OPTIONS
- PUT
- DELETE
- TRACE
- CONNECT

Key information:

- By default, only the GET request method can generate cached content on the network. All other request methods are proxied through the network.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Scheme

The Request Scheme match condition is met only when assets are requested through the selected protocol. The available protocols are:

- HTTP
- HTTPS

Key information:

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Directory

Identifies a request by its relative path, which excludes the file name of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Directory match condition is met.

- **Matches**: Requires the request to contain a relative URL path, excluding the file name, that matches the specified URL pattern.
- **Does Not Match**: Requires the request to contain a relative URL path, excluding file name, that does not match the specified URL pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison starts before or after the content access point. The content access point is the portion of the path that appears between the Verizon CDN hostname and the relative path to the requested asset (for example, /800001/CustomerOrigin). It identifies a location by server type (for example, CDN or customer origin) and your customer account number.

   The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname. 

  For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder**/index.htm

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

  For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder**/index.htm 

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder**/index.htm

- An edge CNAME URL is rewritten to a CDN URL prior to the URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path.
  - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
    
  - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
  - Custom domain: https:\//my.domain.com/path/asset.htm
    
    - URL path (relative to root): /800001/CustomerOrigin/path/
    
    - URL path (relative to origin): /path/

- The portion of the URL that is used for the URL comparison ends just before the file name of the requested asset. A trailing forward slash is the last character in this type of path.

- Replace any spaces in the URL path pattern with "%20."

- Each URL path pattern can contain one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- Specify multiple URL paths in the pattern by delimiting each one with a single space.

    For example: */sales/ */marketing/

- A URL path specification can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Extension

Identifies requests by the file extension of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Extension match condition is met.

- **Matches**: Requires the URL of the request to contain a file extension that exactly matches the specified pattern.

   For example, if you specify "htm", "htm" assets are matched, but not "html" assets.  

- **Does Not Match**: Requires the URL request to contain a file extension that does not match the specified pattern.

Key information:

- Specify the file extensions to match in the **Value** box. Do not include a leading period; for example, use htm instead of .htm.

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- Specify multiple file extensions by delimiting each extension with a single space. 

    For example: htm html

- For example, specifying "htm" matches "htm" assets, but not "html" assets.

#### Sample Scenario

The following sample configuration assumes that this match condition is met when a request matches one of the specified extensions.

Value specification: asp aspx php html

This match condition is met when it finds URLs that end with the following extensions:

- .asp
- .aspx
- .php
- .html

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Filename

Identifies requests by the file name of the requested asset. For the purposes of this match condition, a file name consists of the name of the requested asset, a period, and the file extension (for example, index.html).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Filename match condition is met.

- **Matches**: Requires the request to contain a file name in its URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a file name in its URL path that does not match the specified pattern.

Key information:

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- To specify multiple file extensions, separate each extension with a single space.

    For example: index.htm index.html

- Replace spaces in a file name value with "%20."

- A file name value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). For example, each file name pattern can consist of one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- If wildcard characters are not specified, then only an exact match will satisfy this match condition.

    For example, specifying "presentation.ppt" matches an asset named "presentation.ppt," but not one named "presentation.pptx."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Literal

Compares a request's URL path, including file name, to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Literal match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison point begins before or after the content access point. 

    The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

    For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

    For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to a URL comparison.

For example, both of the following URLs point to the same asset and therefore have the same URL path:

- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
- Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

    Additional information:
    
    - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
   
    - URL path (relative to origin): /path/asset.htm

- Query strings in the URL are ignored.
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
- The value specified for this match condition is compared against the relative path of the exact request made by the client.

- To match all requests made to a particular directory, use the [URL Path Directory](#url-path-directory) or the [URL Path Wildcard](#url-path-wildcard) match condition.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Regex

Compares a request's URL path to the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Regex match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified regular expression.

Key information:

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both URLs point to the same asset and therefore have the same URL path.

     - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm

     - Edge CNAME URL: http:\//my.domain.com/path/asset.htm

    Additional information:
    
     - URL path: /800001/CustomerOrigin/path/asset.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Spaces in the URL path should be replaced with "%20."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Wildcard

Compares a request's relative URL path to the specified wildcard pattern.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified wildcard pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified wildcard pattern.

Key information:

- **Relative to** option: This option determines whether the URL comparison point begins before or after the content access point.

   This option can have the following values:
     - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

       For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

     - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

       For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

     This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path:
     - CDN URL: http://wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
     - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
    
     - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
    
     - URL path (relative to origin): /path/asset.htm
    
- Specify multiple URL paths by delimiting each one with a single space.

   For example: /marketing/asset.* /sales/*.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Replace spaces in the URL path with "%20."
    
- The value specified for a URL path can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Each URL path pattern can contain one or more asterisks (*), where each asterisk can match a sequence of one or more characters.

#### Sample Scenarios

The sample configurations in the following table assume that this match condition is met when a request matches the specified URL pattern:

Value                   | Relative to    | Result 
------------------------|----------------|-------
*/test.html */test.php  | Root or Origin | This pattern is matched by requests for assets named "test.html" or "test.php" in any folder.
/80ABCD/origin/text/*   | Root           | This pattern is matched when the requested asset meets the following criteria: <br />- It must reside on a customer origin called "origin." <br />- The relative path must start with a folder called "text." That is, the requested asset can either reside in the "text" folder or one of its recursive subfolders.
*/css/* */js/*          | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs that contain a css or js folder.
*.jpg *.gif *.png       | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs ending with .jpg, .gif, or .png. An alternative way to specify this pattern is with the [URL Path Extension match condition](#url-path-extension).
/images/* /media/*      | Origin         | This pattern is matched by CDN or edge CNAME URLs whose relative path starts with an "images" or "media" folder. <br />- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/images/sales/event1.png<br />- Sample edge CNAME URL: http:\//cdn.mydomain.com/images/sales/event1.png

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Literal

Compares a request's query string to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Literal match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified query string.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified query string.

Key information:

- Only exact query string matches satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Do not include a leading question mark (?) in the query string value text.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding
   ----------|---------
   Space     | %20
   &         | %25

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Parameter

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified pattern. Query string parameters (for example, parameter=value) in the request URL determine whether this condition is met. This match condition identifies a query string parameter by its name and accepts one or more values for the parameter value. 

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Parameter match condition is met.

- **Matches**: Requires a request to contain the specified parameter with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified parameter.
  - It contains the specified parameter, but its value does not match any of the values that are defined in this match condition.

This match condition provides an easy way to specify parameter name/value combinations. For more flexibility if you are matching a query string parameter, consider using the [URL Query Wildcard](#url-query-wildcard) match condition.

Key information:

- Only a single URL query parameter name can be specified per instance of this match condition.
    
- Because wildcard values are not supported when a parameter name is specified, only exact parameter name matches are eligible for comparison.
- Parameter value(s) can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

       Character | URL Encoding
       ----------|---------
       Space     | %20
       &         | %25

- Specify multiple query string parameter values by delimiting each one with a single space. This match condition is met when a request contains one of the specified name/value combinations.

   - Example 1:

     - Configuration:

       ValueA ValueB

     - This configuration matches the following query string parameters:

       Parameter1=ValueA
    
       Parameter1=ValueB

   - Example 2:

     - Configuration: 

        Value%20A Value%20B

     - This configuration matches the following query string parameters:

       Parameter1=Value%20A

       Parameter1=Value%20B

- This match condition is met only when there is an exact match to at least one of the specified query string name/value combinations.

   For example, if you use the configuration in the previous example, the parameter name/value combination "Parameter1=ValueAdd" would not be considered a match. However, if you specify either of the following values, it will match that name/value combination:

   - ValueA ValueB ValueAdd
   - ValueA* ValueB

- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

Name  | Value |  Result
------|-------|--------
User  | Joe   | This pattern is matched when the query string for a requested URL is "?user=joe."
User  | *     | This pattern is matched when the query string for a requested URL contains a User parameter.
Email | Joe\* | This pattern is matched when the query string for a requested URL contains an Email parameter that starts with "Joe."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Regex

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Regex match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified regular expression.

Key information:

- Only exact matches to the specified regular expression satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding | Value
   ----------|--------------|------
   Space     | %20          | \%20
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
