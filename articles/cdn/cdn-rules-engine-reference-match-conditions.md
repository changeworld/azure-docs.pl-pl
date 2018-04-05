---
title: Aparat dopasowania warunki zasady usługi Azure CDN | Dokumentacja firmy Microsoft
description: Dokumentacja referencyjna dla usługi Azure Content Delivery Network zasady warunków dopasowania aparatu.
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
ms.date: 12/21/2017
ms.author: rli
ms.openlocfilehash: f8dac5469e7160fae93e8251ab7f4195a383f8b4
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="azure-cdn-rules-engine-match-conditions"></a>Pasujące aparatu reguł usługi Azure CDN 
W tym artykule przedstawiono szczegółowe opisy warunki dopasowania dostępne dla usługi Azure sieci dostarczania zawartości (CDN) [aparatu reguł](cdn-rules-engine.md).

Druga część reguły jest warunkiem dopasowania. Warunek dopasowania identyfikuje określone typy żądań, dla których będą wykonywane zestawem funkcji.

Na przykład można użyć warunku dopasowania do:
- Filtrowanie żądań dla zawartości w określonej lokalizacji.
- Filtrowanie żądań jest generowany na podstawie określonego adresu IP lub kraju.
- Filtruj żądania według informacje nagłówka.

## <a name="always-match-condition"></a>Zawsze odpowiadać warunku

Warunek dopasowania zawsze stosuje domyślny zestaw funkcji do wszystkich żądań.

Name (Nazwa) | Przeznaczenie
-----|--------
[Zawsze](#always) | Stosuje się domyślny zestaw funkcji do wszystkich żądań.

## <a name="device-match-condition"></a>Urządzenie zgodne warunku

Warunek zgodne urządzenie identyfikuje żądań wysyłanych z urządzenia przenośnego na podstawie jego właściwości.  

Name (Nazwa) | Przeznaczenie
-----|--------
[Urządzenia](#device) | Identyfikuje żądań wysyłanych z urządzenia przenośnego na podstawie jego właściwości.

## <a name="location-match-conditions"></a>Warunki dopasowania lokalizacji

Warunki dopasowania lokalizacji zidentyfikować żądania na podstawie lokalizacji żądającego.

Name (Nazwa) | Przeznaczenie
-----|--------
[JAKO liczba](#as-number) | Identyfikuje żądań pochodzących z określoną siecią.
[Kraju](#country) | Identyfikuje żądań pochodzących z określonego krajów.

## <a name="origin-match-conditions"></a>Warunki dopasowania źródła

Warunki dopasowania pochodzenia zidentyfikować żądań, które wskazują serwer pochodzenia klienta lub magazynu Content Delivery Network.

Name (Nazwa) | Przeznaczenie
-----|--------
[CDN Origin](#cdn-origin) | Identyfikuje żądania dotyczące zawartości przechowywanej w magazynie sieci dostarczania zawartości.
[Pochodzenie klienta](#customer-origin) | Identyfikuje żądania dotyczące zawartości przechowywanej na serwerze źródłowym określonego klienta.

## <a name="request-match-conditions"></a>Warunki dopasowania żądania

Warunki dopasowania żądania zidentyfikować żądania na podstawie ich właściwości.

Name (Nazwa) | Przeznaczenie
-----|--------
[Adres IP klienta](#client-ip-address) | Identyfikuje żądań pochodzących z określonego adresu IP.
[Parametr pliku cookie](#cookie-parameter) | Sprawdza, czy pliki cookie skojarzone z każdym żądaniem określoną wartość.
[Wyrażenie regularne parametru pliku cookie](#cookie-parameter-regex) | Sprawdza, czy pliki cookie skojarzone z każdym żądaniem dla określonego wyrażenia regularnego.
[Edge Cname](#edge-cname) | Identyfikuje żądań, które wskazują określonej krawędzi CNAME.
[Odwoływanie domeny](#referring-domain) | Identyfikuje żądań, które zostały przekazane z nazw określonych hostów.
[Literał nagłówka żądania](#request-header-literal) | Identyfikuje żądań zawierających określony nagłówek równa określonej wartości.
[Wyrażenie regularne nagłówka żądania](#request-header-regex) | Identyfikuje żądań zawierających określony nagłówek ustawioną wartość, która odpowiada określonemu wyrażeniu regularnemu.
[Symbol wieloznaczny nagłówka żądania](#request-header-wildcard) | Identyfikuje żądań zawierających określony nagłówek ustawioną wartość zgodny z określonym wzorcem.
[Request — metoda](#request-method) | Identyfikuje żądań ich metodą HTTP.
[Schemat żądania](#request-scheme) | Identyfikuje żądań przez ich protokołu HTTP.

## <a name="url-match-conditions"></a>Adres URL warunków dopasowania

Adres URL warunków dopasowania zidentyfikować oparte na ich adresy URL żądania.

Name (Nazwa) | Przeznaczenie
-----|--------
[Adres URL ścieżki katalogu](#url-path-directory) | Identyfikuje żądania za pomocą ich ścieżek względnych.
[Rozszerzenie ścieżki adresu URL](#url-path-extension) | Identyfikuje żądania za pomocą ich rozszerzenia nazwy pliku.
[Nazwa pliku ścieżki adresu URL](#url-path-filename) | Identyfikuje żądań według nazwy pliku.
[Literał ścieżki adresu URL](#url-path-literal) | Porównuje żądania ścieżkę względną do określonej wartości.
[Wyrażenie regularne ścieżki adresu URL](#url-path-regex) | Porównuje żądania ścieżkę względną do określonego wyrażenia regularnego.
[Symbol wieloznaczny ścieżki adresu URL](#url-path-wildcard) | Porównuje żądania ścieżkę względną do określonego wzorca.
[Adres URL zapytania literału](#url-query-literal) | Porównuje ciąg zapytania z podaną wartością.
[Adres URL parametru zapytania](#url-query-parameter) | Identyfikuje żądań zawierających parametr ciągu zapytania określona ustawioną wartość, która jest zgodna z określonym wzorcem.
[Adres URL zapytania Regex](#url-query-regex) | Identyfikuje żądań zawierających parametr ciągu zapytania określona wartość, który odpowiada określonemu wyrażeniu regularnemu.
[Adres URL zapytania z symboli wieloznacznych](#url-query-wildcard) | Porównuje określonej wartości z ciągu zapytania żądania.


## <a name="reference-for-rules-engine-match-conditions"></a>Informacje dotyczące reguł aparatu dopasowania warunków

---
### <a name="always"></a>Zawsze

Warunek dopasowania zawsze stosuje domyślny zestaw funkcji do wszystkich żądań.

[Powrót do początku](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="as-number"></a>JAKO liczba 
W sieci jako numer jest zdefiniowana za pomocą numeru systemu autonomicznego (ASN). 

**Dopasowań**/**jest niezgodny** opcja umożliwia określenie warunków, w jakich numer AS odpowiada warunek jest spełniony:
- **Dopasowuje**: wymaga się, że numer ASN sieci klienta jest zgodny z określonym numerów ASN. 
- **Jest niezgodna**: wymaga się, że numer ASN sieci klienta nie pasuje do żadnego z określonym numerów ASN.

Informacje o kluczu:
- Oddzielającego każdej z nich z jednego miejsca, aby określić wiele numerów ASN. Na przykład 64514 64515 dopasowuje żądań który odbierane z 64514 lub 64515.
- Niektóre żądania mogą nie zwracać prawidłowy numer ASN. Znak zapytania (?) będzie pasował do żądania, dla których nie można określić prawidłowy numer ASN.
- Określ cały numer ASN dla żądanej sieci. Nie będzie można dopasować wartości częściowe.
- Ze względu na sposób, w których pamięci podręcznej ustawienia są śledzone ten warunek dopasowania jest niezgodny z następujących funkcji:
  - Zakończenie wypełnienie pamięci podręcznej
  - Max-Age wewnętrzny domyślne
  - Wymuszanie wewnętrznych Max-Age.
  - Ignoruj pochodzenia No-Cache
  - Wewnętrzny odświeżona maksymalna

[Powrót do początku](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cdn-origin"></a>Źródła usługi CDN
Pochodzenie CDN dopasowania warunek jest spełniony, gdy są spełnione oba poniższe warunki:
- Zażądano zawartość z sieci CDN w warstwie magazynu.
- Identyfikator URI żądania używa typu punktu dostępu do zawartości (na przykład /000001), który jest zdefiniowany w tym stanie dopasowania:
  - Adres URL usługi CDN: Identyfikator URI żądania musi zawierać punkt dostępu do zawartości wybranego.
  - Adres URL CNAME krawędzi: Odpowiedniej konfiguracji CNAME krawędzi musi wskazywać dostęp do zawartości wybranego punktu.
  
Informacje o kluczu:
 - Punkt dostępu do zawartości identyfikuje usługę, która powinna obsługiwać żądanej zawartości.
 - Nie używaj instrukcji IF i połączyć określone warunki dopasowania. Na przykład łączenie pochodzenia CDN warunek dopasowania z warunkiem dopasowania pochodzenia klienta utworzyć wzorca dopasowania, który nigdy nie być zgodne. Z tego powodu dwa warunki dopasowania źródła usługi CDN nie można łączyć za pomocą instrukcji IF i.

[Powrót do początku](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="client-ip-address"></a>Adres IP klienta
**Dopasowań**/**jest niezgodny** opcja umożliwia określenie warunków, w których adres IP klienta pasuje warunek jest spełniony:
- **Dopasowuje**: wymaga się, że adres IP klienta pasuje do jednej z określonych adresów IP. 
- **Jest niezgodna**: wymaga się, że adres IP klienta nie pasuje do żadnego z określonych adresów IP. 

Informacje o kluczu:
- Notacja CIDR.
- Określ wiele adresów IP i/lub bloki adresów IP przez oddzielającego każdej z nich z jednego miejsca. Na przykład:
  - **Przykład IPv4**: 1.2.3.4 10.20.30.40 dopasowuje wszystkie żądania przychodzące z adresu 1.2.3.4 lub 10.20.30.40.
  - **Przykład IPv6**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 dopasowuje wszystkie żądania przychodzące z adresu 1:2:3:4:5:6:7:8 lub 10:20:30:40:50:60:70:80.
- Składnia blok adresów IP to adres IP podstawowego następuje ukośnik i rozmiar prefiks. Na przykład:
  - **Przykład IPv4**: 5.5.5.64/26 dopasowuje wszystkie żądania przychodzące z adresów 5.5.5.64 za pośrednictwem 5.5.5.127.
  - **Przykład IPv6**: 1:2:3: / 48 dopasowuje wszystkie żądania przychodzące z adresów 1:2:3:0:0:0:0:0 za pośrednictwem 1:2:3:ffff:ffff:ffff:ffff:ffff.
- Ze względu na sposób, w których pamięci podręcznej ustawienia są śledzone ten warunek dopasowania jest niezgodny z następujących funkcji:
  - Zakończenie wypełnienie pamięci podręcznej
  - Max-Age wewnętrzny domyślne
  - Wymuszanie wewnętrznych Max-Age.
  - Ignoruj pochodzenia No-Cache
  - Wewnętrzny odświeżona maksymalna

[Powrót do początku](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cookie-parameter"></a>Parametr pliku cookie
**Dopasowań**/**jest niezgodny** opcja umożliwia określenie warunków, zgodnie z którymi parametr plik Cookie pasuje warunek jest spełniony.
- **Dopasowuje**: wymaga żądania zawiera określony plik cookie z wartością, która pasuje do co najmniej jednej wartości, które są zdefiniowane w tym stanie dopasowania.
- **Nie odpowiada**: wymaga żądanie spełnia żadnego z następujących kryteriów:
  - Nie zawiera określonego pliku cookie.
  - Zawiera on określony plik cookie, ale jego wartość nie pasuje do żadnej wartości, które są zdefiniowane w tym stanie dopasowania.
  
Informacje o kluczu:
- Nazwa pliku cookie: 
  - Wartości symboli wieloznacznych, w tym gwiazdki (*) nie są obsługiwane, gdy w przypadku określenia nazwy pliku cookie, tylko cookie dokładnego dopasowania nazw kwalifikują się do porównania.
  - Można określić tylko nazwę jednego pliku cookie na każde wystąpienie tego warunku dopasowania.
  - Bez uwzględniania wielkości liter podczas porównywania nazw plików cookie.
- Wartość pliku cookie: 
  - Oddzielającego każdej z nich z jednego miejsca, aby określić wiele wartości pliku cookie.
  - Wartość pliku cookie mogą wykorzystać [wartości symbolu wieloznacznego](cdn-rules-engine-reference.md#wildcard-values). 
  - Jeśli nie określono wartości symbolu wieloznacznego, dokładnego dopasowania zostanie spełnienia tego warunku dopasowania. Na przykład określenie "Value" będzie odpowiadała "Value", ale nie "Wartość1" lub "Wartość2".
  - Użyj **Ignoruj przypadku** opcji do formantu, czy wielkość liter jest porównywana przed wartość pliku cookie żądania.
- Ze względu na sposób, w których pamięci podręcznej ustawienia są śledzone ten warunek dopasowania jest niezgodny z następujących funkcji:
  - Zakończenie wypełnienie pamięci podręcznej
  - Max-Age wewnętrzny domyślne
  - Wymuszanie wewnętrznych Max-Age.
  - Ignoruj pochodzenia No-Cache
  - Wewnętrzny odświeżona maksymalna

[Powrót do początku](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cookie-parameter-regex"></a>Wyrażenie regularne parametru pliku cookie
Warunek dopasowanie wyrażenia regularnego parametru plików Cookie definiuje nazwę pliku cookie i wartość. Można użyć [wyrażeń regularnych](cdn-rules-engine-reference.md#regular-expressions) definiujące wartość żądanego pliku cookie. 

**Dopasowań**/**jest niezgodny** opcja umożliwia określenie warunków, w jakich Regex parametru pliku Cookie pasuje warunek jest spełniony.
- **Dopasowuje**: wymaga żądania zawiera określony plik cookie z wartością, która odpowiada określonemu wyrażeniu regularnemu.
- **Nie odpowiada**: wymaga żądanie spełnia żadnego z następujących kryteriów:
  - Nie zawiera określonego pliku cookie.
  - Zawiera on określony plik cookie, ale jego wartość nie odpowiada określonemu wyrażeniu regularnemu.
  
Informacje o kluczu:
- Nazwa pliku cookie: 
  - Wyrażenia regularne i wartości symbolu wieloznacznego gwiazdki (*), w tym nie są obsługiwane, gdy w przypadku określenia nazwy pliku cookie, tylko cookie dokładnego dopasowania nazw kwalifikują się do porównania.
  - Można określić tylko nazwę jednego pliku cookie na każde wystąpienie tego warunku dopasowania.
  - Bez uwzględniania wielkości liter podczas porównywania nazw plików cookie.
- Wartość pliku cookie: 
  - Wartość pliku cookie mogą wykorzystać wyrażeń regularnych.
  - Użyj **Ignoruj przypadku** opcji do formantu, czy wielkość liter jest porównywana przed wartość pliku cookie żądania.
- Ze względu na sposób, w których pamięci podręcznej ustawienia są śledzone ten warunek dopasowania jest niezgodny z następujących funkcji:
  - Zakończenie wypełnienie pamięci podręcznej
  - Max-Age wewnętrzny domyślne
  - Wymuszanie wewnętrznych Max-Age.
  - Ignoruj pochodzenia No-Cache
  - Wewnętrzny odświeżona maksymalna

[Powrót do początku](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

--- 
### <a name="country"></a>Kraj
Można określić kraju za pośrednictwem jego numer kierunkowy kraju. 

**Dopasowań**/**jest niezgodny** opcja określa spełnić warunki, w jakich kraju dopasować stan:
- **Dopasowań**: wymaga, aby żądania zawiera wartości kodów określonego kraju. 
- **Nie odpowiada**: wymaga żądania nie zawiera wartości kodów określonego kraju.

Informacje o kluczu:
- Oddzielającego każdej z nich z jednego miejsca, aby określić wiele numerów kierunkowych krajów.
- Symbole wieloznaczne nie są obsługiwane, gdy określasz kodu kraju.
- Numerów kierunkowych "UE" i "Region" nie obejmują wszystkie adresy IP w regionach.
- Niektóre żądania mogą nie zwracać prawidłowy kod kraju. Znak zapytania (?) będzie pasował do żądania, dla których nie można określić prawidłowy kod kraju.
- Numerów kierunkowych jest rozróżniana wielkość liter.
- Ze względu na sposób, w których pamięci podręcznej ustawienia są śledzone ten warunek dopasowania jest niezgodny z następujących funkcji:
  - Zakończenie wypełnienie pamięci podręcznej
  - Max-Age wewnętrzny domyślne
  - Wymuszanie wewnętrznych Max-Age.
  - Ignoruj pochodzenia No-Cache
  - Wewnętrzny odświeżona maksymalna

#### <a name="implementing-country-filtering-by-using-the-rules-engine"></a>Implementowanie filtrowanie kraju przy użyciu aparatu reguł
Ten warunek dopasowania umożliwia wykonywanie wielu dostosowań na podstawie lokalizacji, z której pochodzi żądanie. Na przykład zachowanie funkcji filtrowanie kraju mogą być replikowane za pomocą następującej konfiguracji:

- Ścieżka adresu URL symboli wieloznacznych: Ustaw [symbolu wieloznacznego ścieżki adresu URL zgodne z warunkiem](#url-path-wildcard) do katalogu, który będzie zabezpieczony. 
    Dołączyć do końca ścieżki względnej, aby upewnić się, że dostęp do wszystkich jego elementów podrzędnych zostanie ograniczony przez tę regułę.

- Dopasowanie kraju: warunek dopasowania kraju na żądany zestaw krajach.
   - Zezwalaj: Ustaw kraju dopasować stan do **jest niezgodny** się zezwolić na dostęp w krajach określony do zawartości przechowywanej w lokalizacji zdefiniowane przez warunek dopasowanie symbolu wieloznacznego ścieżki adresu URL.
   - Zablokuj: Ustaw kraju dopasować stan do **dopasowań** blokowanie określonego krajów dostęp do zawartości przechowywanej w lokalizacji zdefiniowane przez warunek dopasowanie symbolu wieloznacznego ścieżki adresu URL.

- Odmów dostępu (403) funkcja: Włącz [funkcja odmowy dostępu (403)](cdn-rules-engine-reference-features.md#deny-access-403) replikowanie dozwolonych lub zablokowanych część funkcji filtrowanie kraju.

[Powrót do początku](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="customer-origin"></a>Pochodzenie klienta

Informacje o kluczu: 
- Niezależnie od tego, czy zawartość jest żądanie za pośrednictwem adresu CDN lub krawędzi CNAME URL wskazujące pochodzenie wybranego klienta jest spełniony warunek dopasowania pochodzenia klienta.
- Nie można usunąć konfiguracji ze źródła — wersja klienta, która odwołuje się do niego reguły ze strony klienta źródła. Przed przystąpieniem do usuwania konfiguracji ze źródła — wersja klienta, upewnij się, następujące konfiguracje go nie odwołania:
  - Warunek dopasowania pochodzenia klienta
  - Konfiguracja CNAME krawędzi
- Nie używaj instrukcji IF i połączyć określone warunki dopasowania. Na przykład łączenie warunek dopasowania pochodzenia klienta z warunkiem dopasowania pochodzenia CDN utworzyć wzorca dopasowania, który nigdy nie być zgodne. Z tego powodu dwa warunki zgodne ze źródła — wersja klienta nie można łączyć za pomocą instrukcji IF i.

[Powrót do początku](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="device"></a>Urządzenie

Warunek zgodne urządzenie identyfikuje żądań wysyłanych z urządzenia przenośnego na podstawie jego właściwości. Wykrywanie urządzenia przenośnego odbywa się za pośrednictwem [WURFL](http://wurfl.sourceforge.net/). 

**Dopasowań**/**jest niezgodny** opcja umożliwia określenie warunków, zgodnie z którymi urządzenie odpowiada warunek jest spełniony:
- **Dopasowań**: wymaga żądającego urządzenia, aby pasowała do określonej wartości. 
- **Jest niezgodna**: wymaga żądającego urządzenia nie pasuje określonej wartości.

Informacje o kluczu:

- Użyj **Ignoruj przypadku** opcję, aby określić, czy określona wartość jest rozróżniana wielkość liter.
- Ze względu na sposób, w których pamięci podręcznej ustawienia są śledzone ten warunek dopasowania jest niezgodny z następujących funkcji:
  - Zakończenie wypełnienie pamięci podręcznej
  - Max-Age wewnętrzny domyślne
  - Wymuszanie wewnętrznych Max-Age.
  - Ignoruj pochodzenia No-Cache
  - Wewnętrzny odświeżona maksymalna

#### <a name="string-type"></a>String — typ
Możliwość WURFL akceptuje zwykle dowolną kombinację liczb, liter i symboli. Ze względu na elastyczne rodzaj tej funkcji musisz wybrać interpretacji wartość skojarzoną z tym warunkiem dopasowania. W poniższej tabeli opisano zestaw dostępnych opcji:

Typ     | Opis
---------|------------
Literału  | Wybierz tę opcję, aby uniemożliwić znaki biorąc na specjalne znaczenie za pomocą ich [wartość literału](cdn-rules-engine-reference.md#literal-values).
Symbol wieloznaczny | Wybierz tę opcję, aby móc korzystać z wszystkich [symboli wieloznacznych] ([wartości symbolu wieloznacznego](cdn-rules-engine-reference.md#wildcard-values).
wyrażenia regularnego    | Wybierz tę opcję, aby użyć [wyrażeń regularnych](cdn-rules-engine-reference.md#regular-expressions). Wyrażenia regularne są przydatne w przypadku definiowania wzorzec znaków.

#### <a name="wurfl-capabilities"></a>Możliwości WURFL
Możliwość WURFL odwołuje się do kategorii, który opisuje urządzeń przenośnych. Możliwości wybranej Określa typ opis urządzenia przenośnego, które służy do identyfikowania żądań.

W poniższej tabeli wymieniono możliwości WURFL oraz ich zmienne dla aparatu reguł.
<br>
> [!NOTE] 
> Obsługiwane są następujące zmienne w **modyfikowania nagłówka żądania klienta** i **zmodyfikować nagłówek odpowiedzi klienta** funkcji.

Możliwości | Zmienna | Opis | Przykładowe wartości
-----------|----------|-------------|----------------
Nazwa marki | %{wurfl_cap_brand_name} | Ciąg, który wskazuje nazwę urządzenia. | Samsung
System operacyjny urządzenia | %{wurfl_cap_device_os} | Ciąg, który wskazuje system operacyjny zainstalowany na urządzeniu. | iOS
Wersja systemu operacyjnego urządzenia | %{wurfl_cap_device_os_version} | Ciąg, który wskazuje numer wersji systemu operacyjnego zainstalowanego na urządzeniu. | 1.0.1
Podwójna orientacji | %{wurfl_cap_dual_orientation} | Wartość logiczna, która wskazuje, czy urządzenie obsługuje dwa orientacji. | true
HTML preferowane DTD | %{wurfl_cap_html_preferred_dtd} | Ciąg, który wskazuje definicja typu dokumentu preferowanych urządzeń przenośnych (DTD) dla zawartości HTML. | brak<br/>xhtml_basic<br/>html5
Obraz ze Śródwierszowaniem | %{wurfl_cap_image_inlining} | Wartość logiczna wskazująca, czy urządzenie obsługuje Base64 zakodowany obrazów. | false
Is Android | %{wurfl_vcap_is_android} | Wartość logiczna, która wskazuje, czy urządzenie używa system operacyjny Android. | true
Is IOS | %{wurfl_vcap_is_ios} | Wartość logiczna wskazująca, czy urządzenie korzysta z systemem iOS. | false
Jest inteligentne TV | %{wurfl_cap_is_smarttv} | Wartość logiczna wskazująca, czy urządzenie jest inteligentne TV. | false
Jest Smartphone | %{wurfl_vcap_is_smartphone} | Wartość logiczna wskazująca, czy urządzenie jest smartfona. | true
Jest typu Tablet | %{wurfl_cap_is_tablet} | Wartość logiczna wskazująca, czy urządzenie jest na komputerze typu tablet. Ten opis jest niezależny od systemu operacyjnego. | true
To urządzenie sieci bezprzewodowej | %{wurfl_cap_is_wireless_device} | Wartość logiczna wskazująca, czy urządzenie jest uznawane za urządzeniem sieci bezprzewodowej. | true
Nazwa Marketing | %{wurfl_cap_marketing_name} | Ciąg, który wskazuje nazwę marketing dla urządzenia. | Perłowej blackBerry 8100
Przeglądarkę dla telefonów | %{wurfl_cap_mobile_browser} | Ciąg, który wskazuje przeglądarki, która służy do żądania zawartości od urządzenia. | Chrome
Wersja przenośnego przeglądarki | %{wurfl_cap_mobile_browser_version} | Ciąg, który wskazuje wersję przeglądarki, która służy do żądania zawartości od urządzenia. | 31
Nazwa modelu | %{wurfl_cap_model_name} | Ciąg, który wskazuje nazwę modelu urządzenia. | s3
Pobierania progresywnego | %{wurfl_cap_progressive_download} | Wartość logiczna, która wskazuje, czy urządzenie obsługuje odtwarzania audio i wideo, gdy nadal są pobierane. | true
Data wydania | %{wurfl_cap_release_date} | Ciąg, który wskazuje rok i miesiąc dodawania do bazy danych WURFL urządzenia.<br/><br/>Format: `yyyy_mm` | 2013_december
Wysokość rozwiązania | %{wurfl_cap_resolution_height} | Liczba całkowita, która wskazuje urządzenia wysokość w pikselach. | 768
Szerokość rozwiązania | %{wurfl_cap_resolution_width} | Liczba całkowita, która wskazuje szerokość urządzenia w pikselach. | 1024

[Powrót do początku](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="edge-cname"></a>Krawędź Cname
Informacje o kluczu: 
- Lista dostępnych krawędzi rekordów CNAME jest ograniczone do tych rekordów CNAME krawędzi, które zostały skonfigurowane na stronie krawędzi CNAME dla platformy, na którym jest konfigurowane przez aparat reguł.
- Przed przystąpieniem do usunięcia konfiguracji CNAME krawędzi, upewnij się, że warunek dopasowania krawędzi Cname nie odwołuje się on. Nie można usunąć krawędzi CNAME konfiguracje, które zostały zdefiniowane w regule ze strony CNAME krawędzi. 
- Nie używaj instrukcji IF i połączyć określone warunki dopasowania. Na przykład łączenie warunek dopasowania krawędzi Cname z warunkiem dopasowania pochodzenia klienta utworzyć wzorca dopasowania, który nigdy nie być zgodne. Z tego powodu dwa warunki dopasowania krawędzi Cname nie można łączyć za pomocą instrukcji IF i.
- Ze względu na sposób, w których pamięci podręcznej ustawienia są śledzone ten warunek dopasowania jest niezgodny z następujących funkcji:
  - Zakończenie wypełnienie pamięci podręcznej
  - Max-Age wewnętrzny domyślne
  - Wymuszanie wewnętrznych Max-Age.
  - Ignoruj pochodzenia No-Cache
  - Wewnętrzny odświeżona maksymalna

[Powrót do początku](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="referring-domain"></a>Odwoływanie domeny
Nazwa hosta skojarzony z odwołania za pośrednictwem której zażądano zawartości określa, czy jest spełniony warunek odwołujących się do domeny. 

**Dopasowań**/**jest niezgodny** opcja określa spełnić warunki, w jakich domeny odwołujących się zgodne warunkiem:
- **Dopasowań**: wymaga nazwę hosta odwołujący się do dopasowania określonej wartości. 
- **Jest niezgodna**: wymaga, aby odwołujący się nazwa hosta nie odpowiadał określonej wartości.

Informacje o kluczu:
- Oddzielającego każdej z nich z jednego miejsca, aby określić wiele nazw hostów.
- Ten warunek dopasowania obsługuje [wartości symbolu wieloznacznego](cdn-rules-engine-reference.md#wildcard-values).
- Jeśli określona wartość nie zawiera znak gwiazdki, musi być dokładnego dopasowania dla odwołania nazwy hosta. Na przykład określenie "mojadomena.com" będzie niezgodna "www.mydomain.com."
- Użyj **Ignoruj przypadku** opcji w celu sterowania czy porównania z uwzględnieniem wielkości liter.
- Ze względu na sposób, w których pamięci podręcznej ustawienia są śledzone ten warunek dopasowania jest niezgodny z następujących funkcji:
  - Zakończenie wypełnienie pamięci podręcznej
  - Max-Age wewnętrzny domyślne
  - Wymuszanie wewnętrznych Max-Age.
  - Ignoruj pochodzenia No-Cache
  - Wewnętrzny odświeżona maksymalna

[Powrót do początku](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---  
### <a name="request-header-literal"></a>Literał nagłówka żądania
**Dopasowań**/**jest niezgodny** opcja określa warunki, zgodnie z którymi żądania nagłówek literału odpowiada warunek jest spełniony.
- **Dopasowań**: wymaga zawierać określonego nagłówka żądania. Wartość musi być zgodna ten, który jest zdefiniowany w tym stanie dopasowania.
- **Nie odpowiada**: wymaga żądanie spełnia żadnego z następujących kryteriów:
  - Nie zawiera określonego nagłówka.
  - Zawiera on określony nagłówek, ale jego wartość jest niezgodna z jedną, która jest zdefiniowana w tym stanie dopasowania.
  
Informacje o kluczu:
- Bez uwzględniania wielkości liter podczas porównywania nazw nagłówka. Użyj **Ignoruj przypadku** opcja kontrolująca istnieją porównania wartości nagłówka.
- Ze względu na sposób, w których pamięci podręcznej ustawienia są śledzone ten warunek dopasowania jest niezgodny z następujących funkcji:
  - Zakończenie wypełnienie pamięci podręcznej
  - Max-Age wewnętrzny domyślne
  - Wymuszanie wewnętrznych Max-Age.
  - Ignoruj pochodzenia No-Cache
  - Wewnętrzny odświeżona maksymalna

[Powrót do początku](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---  
### <a name="request-header-regex"></a>Wyrażenie regularne nagłówka żądania
**Dopasowań**/**jest niezgodny** opcja określa warunki, w jakich Regex nagłówka żądania odpowiada warunek jest spełniony.
- **Dopasowań**: wymaga zawierać określonego nagłówka żądania. Wartość musi być zgodna wzorzec, który jest zdefiniowany w określonym [wyrażenie regularne](cdn-rules-engine-reference.md#regular-expressions).
- **Nie odpowiada**: wymaga żądanie spełnia żadnego z następujących kryteriów:
  - Nie zawiera określonego nagłówka.
  - Zawiera on określony nagłówek, ale jego wartość nie odpowiada określonemu wyrażeniu regularnemu.

Informacje o kluczu:
- Nazwa nagłówka: 
  - Bez uwzględniania wielkości liter podczas porównywania nazw nagłówka.
  - Zastąp spacje w nazwie nagłówka "% 20". 
- Wartość nagłówka: 
  - Wartość nagłówka mogą wykorzystać wyrażeń regularnych.
  - Użyj **Ignoruj przypadku** opcja kontrolująca istnieją porównania wartości nagłówka.
  - Dopasowanie warunek jest spełniony, tylko wtedy, gdy wartość nagłówka dokładnie odpowiada co najmniej jeden z określonych wzorców.
- Ze względu na sposób, w których pamięci podręcznej ustawienia są śledzone ten warunek dopasowania jest niezgodny z następujących funkcji:
  - Zakończenie wypełnienie pamięci podręcznej
  - Max-Age wewnętrzny domyślne
  - Wymuszanie wewnętrznych Max-Age.
  - Ignoruj pochodzenia No-Cache
  - Wewnętrzny odświeżona maksymalna 

[Powrót do początku](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-header-wildcard"></a>Symbol wieloznaczny nagłówka żądania
**Dopasowań**/**jest niezgodny** opcja określa warunki, zgodnie z którymi symbol wieloznaczny nagłówka żądania odpowiada warunek jest spełniony.
- **Dopasowań**: wymaga zawierać określonego nagłówka żądania. Wartość musi odpowiadać co najmniej jedna z wartości zdefiniowanych w tym stanie dopasowania.
- **Nie odpowiada**: wymaga żądanie spełnia żadnego z następujących kryteriów:
  - Nie zawiera określonego nagłówka.
  - Zawiera on określony nagłówek, ale jego wartość nie pasuje do żadnego z określonymi wartościami.
  
Informacje o kluczu:
- Nazwa nagłówka: 
  - Bez uwzględniania wielkości liter podczas porównywania nazw nagłówka.
  - Spacje w nazwie nagłówka powinny być zastąpione przez "% 20". Ta wartość umożliwia również określić spacje w wartości nagłówka.
- Wartość nagłówka: 
  - Wartość nagłówka można korzystać z [wartości symbolu wieloznacznego](cdn-rules-engine-reference.md#wildcard-values).
  - Użyj **Ignoruj przypadku** opcja kontrolująca istnieją porównania wartości nagłówka.
  - Ten warunek dopasowanie jest spełniony, gdy wartość nagłówka jest identyczny z co najmniej jednym określonym wzorce.
  - Oddzielającego każdej z nich z jednego miejsca, aby określić wiele wartości.
- Ze względu na sposób, w których pamięci podręcznej ustawienia są śledzone ten warunek dopasowania jest niezgodny z następujących funkcji:
  - Zakończenie wypełnienie pamięci podręcznej
  - Max-Age wewnętrzny domyślne
  - Wymuszanie wewnętrznych Max-Age.
  - Ignoruj pochodzenia No-Cache
  - Wewnętrzny odświeżona maksymalna

[Powrót do początku](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-method"></a>Request — metoda
Metoda żądania dopasowania warunek jest spełniony, tylko wtedy, gdy zasoby są żądane przez metodę wybranego żądania. Sposoby żądanie dostępne są następujące:
- GET
- HEAD 
- POST 
- OPCJE 
- UMIEŚĆ 
- DELETE 
- ŚLEDZENIA 
- POŁĄCZ 

Informacje o kluczu:
- Domyślnie tylko metodę żądania GET może generować zawartości w pamięci podręcznej w sieci. Wszystkie inne metody żądania są przekazywane przez serwer proxy za pośrednictwem sieci.
- Ze względu na sposób, w których pamięci podręcznej ustawienia są śledzone ten warunek dopasowania jest niezgodny z następujących funkcji:
  - Zakończenie wypełnienie pamięci podręcznej
  - Max-Age wewnętrzny domyślne
  - Wymuszanie wewnętrznych Max-Age.
  - Ignoruj pochodzenia No-Cache
  - Wewnętrzny odświeżona maksymalna

[Powrót do początku](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-scheme"></a>Schemat żądania
Schemat żądania dopasowania warunek jest spełniony, tylko wtedy, gdy zasoby są żądane za pomocą wybranego protokołu. Protokoły dostępne są następujące: 
- HTTP
- HTTPS

Informacje o kluczu:
- Ze względu na sposób, w których pamięci podręcznej ustawienia są śledzone ten warunek dopasowania jest niezgodny z następujących funkcji:
  - Zakończenie wypełnienie pamięci podręcznej
  - Max-Age wewnętrzny domyślne
  - Wymuszanie wewnętrznych Max-Age.
  - Ignoruj pochodzenia No-Cache
  - Wewnętrzny odświeżona maksymalna

[Powrót do początku](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-directory"></a>Adres URL ścieżki katalogu
Identyfikuje żądanie za pomocą ścieżki względnej, co wyklucza nazwy pliku żądanego zasobu.

**Dopasowań**/**jest niezgodny** opcja umożliwia określenie warunków, zgodnie z którymi katalogu ścieżki adresu URL odpowiada warunek jest spełniony.
- **Dopasowuje**: wymaga, aby żądania zawiera ścieżką względnego adresu URL, z wyłączeniem nazwę pliku, który jest zgodny z określonym wzorcem adresu URL.
- **Jest niezgodna**: wymaga, aby żądania zawiera ścieżką względnego adresu URL, z wyjątkiem nazwy pliku, który jest niezgodny z określonym wzorcem adresu URL.

Informacje o kluczu:
- Użyj **względem** opcję, aby określić, czy porównania adres URL rozpoczyna się przed lub po punkt dostępu do zawartości. Punkt dostępu do zawartości jest częścią ścieżki wyświetlonym między Verizon CDN nazwa hosta i ścieżkę względną do żądanego zasobu (na przykład /800001/CustomerOrigin). Identyfikuje lokalizacji przez typ serwera (na przykład pochodzenia CDN lub klienta) i numer konta klienta.

   Następujące wartości są dostępne dla **względem** opcji:
   - **Główny**: wskazuje, że adres URL punktu porównania rozpoczyna się bezpośrednio po hosta CDN. 

     Na przykład: http:\//wpc.0001.&lt; domeny&gt;/**800001/myorigin/MójFolder**/index.htm

   - **Pochodzenie**: wskazuje, że adres URL punktu porównania rozpoczyna się po punkt dostępu do zawartości (na przykład /000001 lub/800001/myorigin). Ponieważ \*. azureedge.net CNAME jest tworzony domyślnie względem katalogu pochodzenia na hosta Verizon CDN, Azure CDN użytkownicy powinni używać **pochodzenia** wartość. 

     Na przykład: https:\//&lt;punktu końcowego&gt;.azureedge.net/**MójFolder**/index.htm 

     Ten adres URL wskazuje następujące hosta Verizon CDN: http:\//wpc.0001.&lt; domeny&gt;/800001/myorigin/**MójFolder**/index.htm

- Krawędź CNAME adres URL jest napisany od nowa do adresu URL usługi CDN przed porównania adresu URL.

    Na przykład obu następujących adresów URL wskaż danego elementu zawartości i dlatego mają taką samą ścieżkę adresu URL.
    - Adres URL usługi CDN: http:\//wpc.0001.&lt; domeny&gt;/800001/CustomerOrigin/path/asset.htm
    
    - Adres URL CNAME krawędzi: http:\//&lt;punktu końcowego&gt;.azureedge.net/path/asset.htm
    
    Dodatkowe informacje:
    - Domena niestandardowa: https:\//my.domain.com/path/asset.htm
    
    - Ścieżka adresu URL (względem katalogu głównego): / 800001/CustomerOrigin/ścieżka /
    
    - Ścieżka adresu URL (względem źródła): /path/

- Część adres URL, który jest używany do celów porównania adres URL bezpośrednio przed nazwy pliku żądanego zasobu. Końcowy ukośnik jest ostatni znak w tego typu ścieżki.
    
- Zastąp spacje w wzorzec ścieżki adresu URL "% 20".
    
- Wzorzec ścieżki każdy adres URL może zawierać co najmniej jeden gwiazdki (*), gdzie każdy gwiazdka odpowiada sekwencję co najmniej jeden znak.
    
- Określić wiele ścieżek adresu URL we wzorcu, oddzielającego każdej z nich z jednego miejsca.

    Na przykład: * /sales/ * /marketing/

- Specyfikacja ścieżki adresu URL można korzystać z [wartości symbolu wieloznacznego](cdn-rules-engine-reference.md#wildcard-values).

- Użyj **Ignoruj przypadku** opcji w celu sterowania czy odbywa się porównania z uwzględnieniem wielkości liter.

[Powrót do początku](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-extension"></a>Rozszerzenie ścieżki adresu URL
Identyfikuje żądania przez rozszerzenie pliku żądanego zasobu.

**Dopasowań**/**jest niezgodny** opcja określa warunki, zgodnie z którymi rozszerzenie ścieżki adresu URL odpowiada warunek jest spełniony.
- **Dopasowuje**: wymaga adresu URL żądania zawiera rozszerzenie pliku, która dokładnie odpowiada określonym wzorcem.

   Na przykład jeśli określisz "HTML", "HTML" zasoby są spełnione, ale nie "kod html" zasoby.  

- **Jest niezgodna**: wymaga żądanie adresu URL, który zawiera rozszerzenie pliku, który jest niezgodny z określonym wzorcem.

Informacje o kluczu:
- Określ rozszerzenia pliku do dopasowania w **wartość** pole. Nie należy dołączać kropki na początku; na przykład użyć htm zamiast .htm.

- Użyj **Ignoruj przypadku** opcji w celu sterowania czy odbywa się porównania z uwzględnieniem wielkości liter.

- Oddzielającego każde rozszerzenie z jednego miejsca, aby określić wiele rozszerzeń plików. 

    Na przykład: htm, html

- Na przykład określenie "HTML" pasuje zasoby "HTML", ale nie "kod html" zasoby.


#### <a name="sample-scenario"></a>Przykładowy scenariusz

Następujące Przykładowa konfiguracja zakłada spełnienia tego warunku dopasowania, gdy żądanie pasuje do jednej z określonych rozszerzeń.

Wartość specyfikacji: asp aspx php html

Ten warunek dopasowanie jest spełniony, gdy znajdzie adresów URL, które kończą się z następującymi rozszerzeniami:
- ASP
- .aspx
- .php
- .html

[Powrót do początku](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-filename"></a>Nazwa pliku ścieżki adresu URL
Identyfikuje żądań według nazwy pliku żądanego zasobu. Do celów tego warunku dopasowania nazwa pliku składa się z nazwy żądanej zawartości, okres i rozszerzenie pliku (na przykład index.html).

**Dopasowań**/**jest niezgodny** opcja umożliwia określenie warunków, zgodnie z którymi Filename ścieżki adresu URL odpowiada warunek jest spełniony.
- **Dopasowuje**: wymaga żądania zawiera nazwę pliku w jego ścieżki adresu URL, zgodny z określonym wzorcem.
- **Jest niezgodna**: wymaga żądania zawiera nazwę pliku w jego ścieżki adresu URL, który jest niezgodny z określonym wzorcem.

Informacje o kluczu:
- Użyj **Ignoruj przypadku** opcji w celu sterowania czy odbywa się porównania z uwzględnieniem wielkości liter.

- Aby określić wiele rozszerzeń plików, należy oddzielić każdego rozszerzenia, pojedyncze spacje.

    Na przykład: index.htm index.html

- Zastąp spacje wartość nazwy pliku "% 20".
    
- Wartość nazwy pliku można korzystać z [wartości symbolu wieloznacznego](cdn-rules-engine-reference.md#wildcard-values). Na przykład każdy wzorzec nazwy pliku może składać się z przynajmniej jednej gwiazdki (*), gdzie każdy gwiazdka odpowiada sekwencję co najmniej jeden znak.
    
- Jeśli symbole wieloznaczne nie są określone, dokładnego dopasowania zostanie spełnienia tego warunku dopasowania.

    Na przykład określenie "presentation.ppt" odpowiada zasób o nazwie "presentation.ppt", ale nie jedną o nazwie "presentation.pptx."

[Powrót do początku](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-literal"></a>Literał ścieżki adresu URL
Porównuje ścieżki adresu URL żądania, w tym nazwa pliku określona wartość.

**Dopasowań**/**jest niezgodny** opcja umożliwia określenie warunków, zgodnie z którymi literału ścieżki adresu URL odpowiada warunek jest spełniony.
- **Dopasowuje**: wymaga, aby żądania zawiera ścieżki adresu URL, który jest zgodny z określonym wzorcem.
- **Jest niezgodna**: wymaga, aby żądania zawiera ścieżki adresu URL, który jest niezgodny z określonym wzorcem.

Informacje o kluczu:
- Użyj **względem** opcję, aby określić, czy punkt Porównanie adres URL rozpoczyna się przed lub po punkt dostępu do zawartości. 

    Następujące wartości są dostępne dla **względem** opcji:
     - **Główny**: wskazuje, że adres URL punktu porównania rozpoczyna się bezpośrednio po hosta CDN.

       Na przykład: http:\//wpc.0001.&lt; domeny&gt;/**800001/myorigin/myfolder/index.htm**

     - **Pochodzenie**: wskazuje, że adres URL punktu porównania rozpoczyna się po punkt dostępu do zawartości (na przykład /000001 lub/800001/myorigin). Ponieważ \*. azureedge.net CNAME jest tworzony domyślnie względem katalogu pochodzenia na hosta Verizon CDN, Azure CDN użytkownicy powinni używać **pochodzenia** wartość. 

       Na przykład: https:\//&lt;punktu końcowego&gt;.azureedge.net/**myfolder/index.htm**

     Ten adres URL wskazuje następujące hosta Verizon CDN: http:\//wpc.0001.&lt; domeny&gt;/800001/myorigin/**myfolder/index.htm**

- Krawędź CNAME adres URL jest napisany od nowa do adresu URL usługi CDN przed porównaniem adresu URL.

    Na przykład obu następujących adresów URL wskaż danego elementu zawartości i dlatego mają taką samą ścieżkę adresu URL:
    - Adres URL usługi CDN: http:\//wpc.0001.&lt; domeny&gt;/800001/CustomerOrigin/path/asset.htm
    - Adres URL CNAME krawędzi: http:\//&lt;punktu końcowego&gt;.azureedge.net/path/asset.htm
    
    Dodatkowe informacje:
    
    - Ścieżka adresu URL (względem katalogu głównego): /800001/CustomerOrigin/path/asset.htm
   
    - Ścieżka adresu URL (względem źródła): /path/asset.htm

- Ciągi zapytania w adresie URL są ignorowane.
- Użyj **Ignoruj przypadku** opcji w celu sterowania czy odbywa się porównania z uwzględnieniem wielkości liter.
- Wartość określona dla tego warunku dopasowanie jest porównywana ścieżki względnej dokładne żądania przez klienta.

- Aby uwzględnić wszystkie żądania kierowane do określonego katalogu, należy użyć [adresu URL ścieżki katalogu](#url-path-directory) lub [symbolu wieloznacznego ścieżki adresu URL](#url-path-wildcard) dopasować stan.

[Powrót do początku](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-regex"></a>Wyrażenie regularne ścieżki adresu URL
Porównuje ścieżki adresu URL żądania do określonego [wyrażenie regularne](cdn-rules-engine-reference.md#regular-expressions).

**Dopasowań**/**jest niezgodny** opcja umożliwia określenie warunków, zgodnie z którymi Regex ścieżki adresu URL odpowiada warunek jest spełniony.
- **Dopasowuje**: wymaga żądania zawiera ścieżki adresu URL, który odpowiada określonemu wyrażeniu regularnemu.
- **Jest niezgodna**: wymaga, aby żądania zawiera ścieżki adresu URL, który nie odpowiada określonemu wyrażeniu regularnemu.

Informacje o kluczu:
- Krawędź CNAME adres URL jest napisany od nowa do adresu URL usługi CDN przed porównania adresu URL. 
 
    Na przykład oba adresy URL wskaż danego elementu zawartości i dlatego mają taką samą ścieżkę adresu URL.

     - Adres URL usługi CDN: http:\//wpc.0001.&lt; domeny&gt;/800001/CustomerOrigin/path/asset.htm

     - Adres URL CNAME krawędzi: http:\//my.domain.com/path/asset.htm
    
    Dodatkowe informacje:
    
     - Ścieżka adresu URL: /800001/CustomerOrigin/path/asset.htm

- Ciągi zapytania w adresie URL są ignorowane.
    
- Użyj **Ignoruj przypadku** opcji w celu sterowania czy odbywa się porównania z uwzględnieniem wielkości liter.
    
- Spacje w ścieżce URL powinny być zastąpione przez "% 20".

[Powrót do początku](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-wildcard"></a>Symbol wieloznaczny ścieżki adresu URL
Porównuje względnej ścieżki adresu URL żądania z wzorcem określony symbol wieloznaczny.

**Dopasowań**/**jest niezgodny** opcja umożliwia określenie warunków, zgodnie z którymi symbol wieloznaczny ścieżki adresu URL odpowiada warunek jest spełniony.
- **Dopasowuje**: wymaga żądania zawiera ścieżki adresu URL, zgodny z wzorcem określony symbol wieloznaczny.
- **Jest niezgodna**: wymaga, aby żądania zawiera ścieżki adresu URL, który nie pasuje do wzorca określony symbol wieloznaczny.

Informacje o kluczu:
- **Względem** opcja: Ta opcja określa, czy adres URL punktu porównania rozpoczyna się przed lub po punkt dostępu do zawartości.

   Ta opcja może mieć następujące wartości:
     - **Główny**: wskazuje, że adres URL punktu porównania rozpoczyna się bezpośrednio po hosta CDN.

       Na przykład: http:\//wpc.0001.&lt; domeny&gt;/**800001/myorigin/myfolder/index.htm**

     - **Pochodzenie**: wskazuje, że adres URL punktu porównania rozpoczyna się po punkt dostępu do zawartości (na przykład /000001 lub/800001/myorigin). Ponieważ \*. azureedge.net CNAME jest tworzony domyślnie względem katalogu pochodzenia na hosta Verizon CDN, Azure CDN użytkownicy powinni używać **pochodzenia** wartość. 

       Na przykład: https:\//&lt;punktu końcowego&gt;.azureedge.net/**myfolder/index.htm**

     Ten adres URL wskazuje następujące hosta Verizon CDN: http:\//wpc.0001.&lt; domeny&gt;/800001/myorigin/**myfolder/index.htm**

- Krawędź CNAME adres URL jest napisany od nowa do adresu URL usługi CDN przed porównania adresu URL.

    Na przykład obu następujących adresów URL wskaż danego elementu zawartości i dlatego mają taką samą ścieżkę adresu URL:
     - Adres URL usługi CDN: http://wpc.0001.&lt; domeny&gt;/800001/CustomerOrigin/path/asset.htm
     - Adres URL CNAME krawędzi: http:\//&lt;punktu końcowego&gt;.azureedge.net/path/asset.htm
    
    Dodatkowe informacje:
    
     - Ścieżka adresu URL (względem katalogu głównego): /800001/CustomerOrigin/path/asset.htm
    
     - Ścieżka adresu URL (względem źródła): /path/asset.htm
    
- Oddzielającego każdej z nich z jednego miejsca, aby określić wiele ścieżek adresu URL.

   Na przykład: /marketing/asset.* /sales/*.htm

- Ciągi zapytania w adresie URL są ignorowane.
    
- Użyj **Ignoruj przypadku** opcji w celu sterowania czy odbywa się porównania z uwzględnieniem wielkości liter.
    
- Zastąp spacje w ścieżce URL "% 20".
    
- Wartość określona dla ścieżki adresu URL można korzystać z [wartości symbolu wieloznacznego](cdn-rules-engine-reference.md#wildcard-values). Wzorzec ścieżki każdy adres URL może zawierać co najmniej jeden gwiazdki (*), gdzie każdy gwiazdki może dopasować sekwencję co najmniej jeden znak.

#### <a name="sample-scenarios"></a>Przykładowe scenariusze

Przykładowych konfiguracji w tabeli poniżej założono spełnienia tego warunku dopasowania, gdy żądanie pasuje do określonego wzorca adresu URL:

Wartość                   | Względem    | Wynik 
------------------------|----------------|-------
*/test.html */test.php  | Główny lub punkt początkowy | Ten wzorzec jest uwzględniony przez żądania dotyczące zasobów o nazwie "test.html" lub "test.php" w dowolnym folderze.
/ 80ABCD/pochodzenia/tekstu / *   | Katalog główny           | Ten wzorzec jest zgodny, gdy żądanych zasobów spełnia następujące kryteria: <br />-Musi znajdować się w źródle klienta o nazwie "origin". <br />— Ścieżka względna musi rozpoczynać się od folder o nazwie "text". Oznacza to żądanych zasobów albo może znajdować się w folderze "text" lub jednej z jego podfolderach cyklicznego.
*/CSS/* */js/*          | Główny lub punkt początkowy | Ten wzorzec jest uwzględniony przez wszystkie CDN lub krawędzi adresy URL CNAME, który zawiera folder css i js.
*.jpg *.gif *.png       | Główny lub punkt początkowy | Ten wzorzec jest uwzględniony przez wszystkie CDN lub krawędzi CNAME adresy URL kończą się ciągiem jpg, GIF lub PNG. Alternatywny sposób Określ ten wzorzec jest z [rozszerzenie ścieżki adresu URL zgodne z warunkiem](#url-path-extension).
/ obrazów / * / media / *      | Origin         | Ten wzorzec jest uwzględniony przez CDN lub krawędzi CNAME adresów URL, którego ścieżka względna rozpoczyna się od "obrazy" lub "nośnika" folder. <br />-Adres URL CDN: http:\//wpc.0001.&lt; domeny&gt;/800001/myorigin/images/sales/event1.png<br />-Przykładowe krawędzi CNAME adresu URL: http:\//cdn.mydomain.com/images/sales/event1.png

[Powrót do początku](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-query-literal"></a>Adres URL zapytania literału
Porównuje ciąg zapytania z podaną wartością.

**Dopasowań**/**jest niezgodny** opcja określa warunki, zgodnie z którymi literału zapytania URL odpowiada warunek jest spełniony.
- **Dopasowuje**: wymaga, aby żądania zawiera ciąg zapytania URL pasującej do ciągu określonego zapytania.
- **Jest niezgodna**: wymaga, aby żądania zawiera ciąg zapytania adres URL, który nie jest zgodny z ciągiem określonego zapytania.

Informacje o kluczu:

- Tylko dokładne zapytanie ciąg dopasowań spełnienia tego warunku dopasowania.
    
- Użyj **Ignoruj przypadku** opcja kontrolująca uwzględnianie wielkości liter porównania ciągu zapytania.
    
- Nie dołączaj wiodące znak zapytania (?) w tekście wartości ciągu zapytania.
    
- Niektóre znaki wymaga kodowania adresów URL. Użyj symbol procentu do adresu URL kodowania następujących znaków:

   Znak | Kodowanie adresu URL
   ----------|---------
   Przestrzeń kosmiczna     | %20
   &         | %25

- Ze względu na sposób, w których pamięci podręcznej ustawienia są śledzone ten warunek dopasowania jest niezgodny z następujących funkcji:
   - Zakończenie wypełnienie pamięci podręcznej
   - Max-Age wewnętrzny domyślne
   - Wymuszanie wewnętrznych Max-Age.
   - Ignoruj pochodzenia No-Cache
   - Wewnętrzny odświeżona maksymalna

[Powrót do początku](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-query-parameter"></a>Adres URL parametru zapytania
Identyfikuje żądań zawierających parametru ciągu zapytania określona. Ten parametr ma ustawioną wartość, która jest zgodna z określonym wzorcem. Parametry ciągu zapytania (na przykład parametr = wartość) w żądaniu URL ustalić, czy ten warunek jest spełniony. Ten warunek dopasowania identyfikuje parametr ciągu zapytania przy użyciu nazwy i akceptuje jedną lub więcej wartości dla wartości parametru. 

**Dopasowań**/**jest niezgodny** opcja określa warunki, zgodnie z którymi parametru zapytania URL odpowiada warunek jest spełniony.
- **Dopasowuje**: wymaga żądania zawiera określony parametr z wartością, która pasuje do co najmniej jednej wartości, które są zdefiniowane w tym stanie dopasowania.
- **Nie odpowiada**: wymaga żądanie spełnia żadnego z następujących kryteriów:
  - Nie zawiera określonego parametru.
  - Zawiera on określony parametr, ale jego wartość nie pasuje do żadnej wartości, które są zdefiniowane w tym stanie dopasowania.

Ten warunek dopasowania zapewnia prosty sposób określić kombinacji nazwy i wartości parametrów. Dla większą elastyczność i jeśli są dopasowania parametr ciągu zapytania, należy rozważyć użycie [adres URL zapytania wieloznaczny](#url-query-wildcard) dopasować stan.

Informacje o kluczu:
- Dla każdego wystąpienia tego warunku dopasowania można określić tylko jeden adres URL zapytania nazwę parametru.
    
- Wartości symboli wieloznacznych nie są obsługiwane, gdy jest określona nazwa parametru, tylko parametr dokładnego dopasowania nazw kwalifikują się do porównania.
- Wartości parametrów mogą obejmować [wartości symbolu wieloznacznego](cdn-rules-engine-reference.md#wildcard-values).
   - Wzorzec wartość każdego parametru może składać się z co najmniej jeden gwiazdki (*), gdzie każdy gwiazdki może dopasować sekwencję co najmniej jeden znak.
   - Niektóre znaki wymaga kodowania adresów URL. Użyj symbol procentu do adresu URL kodowania następujących znaków:

       Znak | Kodowanie adresu URL
       ----------|---------
       Przestrzeń kosmiczna     | %20
       &         | %25

- Oddzielającego każdej z nich z jednego miejsca, aby określić wiele wartości parametru ciągu zapytania. To dopasowanie warunek jest spełniony, jeśli żądanie zawiera jedną z kombinacji określona nazwa/wartość.

   - Przykład 1:

     - Konfiguracja:

       ValueA ValueB

     - Ta konfiguracja dopasowuje następujących parametrów ciągu zapytania:

       Parametr1 = ValueA
    
       Parametr1 = ValueB

   - Przykład 2:

     - Konfiguracja: 

        Wartość % 20A % wartość 20B

     - Ta konfiguracja dopasowuje następujących parametrów ciągu zapytania:

       Parametr1 = wartość % 20A

       Parametr1 = wartość % 20B

- To dopasowanie warunek jest spełniony, tylko wtedy, gdy dokładnego dopasowania do co najmniej jednej kombinacji nazwy i wartości ciągu określonego zapytania.

   Na przykład jeśli używasz konfiguracji w poprzednim przykładzie parametr nazwa/wartość kombinacja "parametr1 = ValueAdd" nie może zostać uznane za pasujące. Jednak jeśli można określić jedną z następujących wartości, będzie odpowiadała tej kombinacji nazwy i wartości:

   - ValueA ValueB ValueAdd
   - ValueB ValueA *

- Użyj **Ignoruj przypadku** opcja kontrolująca uwzględnianie wielkości liter porównania ciągu zapytania.
    
- Ze względu na sposób, w których pamięci podręcznej ustawienia są śledzone ten warunek dopasowania jest niezgodny z następujących funkcji:
   - Zakończenie wypełnienie pamięci podręcznej
   - Max-Age wewnętrzny domyślne
   - Wymuszanie wewnętrznych Max-Age.
   - Ignoruj pochodzenia No-Cache
   - Wewnętrzny odświeżona maksymalna

#### <a name="sample-scenarios"></a>Przykładowe scenariusze
W poniższym przykładzie pokazano, jak ta opcja działa w następujących sytuacjach:

Name (Nazwa)  | Wartość |  Wynik
------|-------|--------
Użytkownik  | Jan   | Ten wzorzec jest zgodny, gdy ciąg zapytania dla żądanego adresu URL jest "? użytkownika = Jan."
Użytkownik  | *     | Ten wzorzec jest zgodny, gdy parametr użytkownika zawiera ciąg zapytania dla żądanego adresu URL.
Email | Jan\* | Ten wzorzec jest zgodny, jeśli ciąg zapytania dla żądanego adresu URL zawiera parametr poczty E-mail, który rozpoczyna się od "Jan".

[Powrót do początku](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-query-regex"></a>Adres URL zapytania Regex
Identyfikuje żądań zawierających parametru ciągu zapytania określona. Ten parametr ma ustawioną wartość, która jest zgodna z określoną [wyrażenie regularne](cdn-rules-engine-reference.md#regular-expressions).

**Dopasowań**/**jest niezgodny** opcja określa warunki, w jakich Regex zapytania URL odpowiada warunek jest spełniony.
- **Dopasowuje**: wymaga, aby żądania zawiera ciąg zapytania adres URL, który odpowiada określonemu wyrażeniu regularnemu.
- **Jest niezgodna**: wymaga, aby żądania zawiera ciąg zapytania adres URL, który nie odpowiada określonemu wyrażeniu regularnemu.

Informacje o kluczu:
- Tylko dokładne dopasowania do określonego wyrażenia regularnego spełnienia tego warunku dopasowania.
    
- Użyj **Ignoruj przypadku** opcja kontrolująca uwzględnianie wielkości liter porównania ciągu zapytania.
    
- Dla celów tej opcji ciąg zapytania rozpoczyna się od pierwszego znaku po znaku zapytania (?) Ogranicznik ciągu zapytania.
    
- Niektóre znaki wymaga kodowania adresów URL. Użyj symbol procentu do adresu URL kodowania następujących znaków:

   Znak | Kodowanie adresu URL | Wartość
   ----------|--------------|------
   Przestrzeń kosmiczna     | %20          | \%20
   &         | %25          | \%25

   Należy pamiętać, że należy zastosować ucieczkę procent symboli.

- Znaki specjalne wyrażenia regularnego podwójnego anulowania (na przykład \^$. +) do uwzględnienia w wyrażeniu regularnym ukośnik odwrotny.

   Na przykład:

   Wartość | Interpretowane jako 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Ze względu na sposób, w których pamięci podręcznej ustawienia są śledzone ten warunek dopasowania jest niezgodny z następujących funkcji:
   - Zakończenie wypełnienie pamięci podręcznej
   - Max-Age wewnętrzny domyślne
   - Wymuszanie wewnętrznych Max-Age.
   - Ignoruj pochodzenia No-Cache
   - Wewnętrzny odświeżona maksymalna


[Powrót do początku](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-query-wildcard"></a>Adres URL zapytania z symboli wieloznacznych
Porównuje określonej wartości z ciągu zapytania żądania.

**Dopasowań**/**jest niezgodny** opcja umożliwia określenie warunków, zgodnie z którymi symbol wieloznaczny zapytania URL odpowiada warunek jest spełniony.
- **Dopasowuje**: wymaga, aby żądania zawiera ciąg zapytania adres URL, który jest zgodna z wartością określony symbol wieloznaczny.
- **Jest niezgodna**: wymaga, aby żądania zawiera ciąg zapytania adres URL, który nie jest zgodna z wartością określony symbol wieloznaczny.

Informacje o kluczu:
- Dla celów tej opcji ciąg zapytania rozpoczyna się od pierwszego znaku po znaku zapytania (?) Ogranicznik ciągu zapytania.
- Wartości parametrów mogą obejmować [wartości symbolu wieloznacznego](cdn-rules-engine-reference.md#wildcard-values):
   - Wzorzec wartość każdego parametru może składać się z co najmniej jeden gwiazdki (*), gdzie każdy gwiazdki może dopasować sekwencję co najmniej jeden znak.
   - Niektóre znaki wymaga kodowania adresów URL. Użyj symbol procentu do adresu URL kodowania następujących znaków:

     Znak | Kodowanie adresu URL
     ----------|---------
     Przestrzeń kosmiczna     | %20
     &         | %25

- Oddzielającego każdej z nich z jednego miejsca, aby określić wiele wartości.

   Na przykład: *parametr1 = ValueA* *ValueB* *parametr1 = ValueC & Parameter2 = ValueD*

- Tylko dokładne dopasowania do co najmniej jednego z wzorców ciąg zapytania określony spełnienia tego warunku dopasowania.
    
- Użyj **Ignoruj przypadku** opcja kontrolująca uwzględnianie wielkości liter porównania ciągu zapytania.
    
- Ze względu na sposób, w których pamięci podręcznej ustawienia są śledzone ten warunek dopasowania jest niezgodny z następujących funkcji:
   - Zakończenie wypełnienie pamięci podręcznej
   - Max-Age wewnętrzny domyślne
   - Wymuszanie wewnętrznych Max-Age.
   - Ignoruj pochodzenia No-Cache
   - Wewnętrzny odświeżona maksymalna

#### <a name="sample-scenarios"></a>Przykładowe scenariusze
W poniższym przykładzie pokazano, jak ta opcja działa w następujących sytuacjach:

 Name (Nazwa)                 | Opis
 ---------------------|------------
user=joe              | Ten wzorzec jest zgodny, gdy ciąg zapytania dla żądanego adresu URL jest "? użytkownika = Jan."
\*user=\* \*optout=\* | Ten wzorzec jest zgodny, gdy zapytanie adresu URL CDN zawiera użytkownika lub parametru optout.

[Powrót do początku](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

## <a name="next-steps"></a>Kolejne kroki
* [Omówienie usługi Azure Content Delivery Network](cdn-overview.md)
* [Dokumentacja aparatu reguł](cdn-rules-engine-reference.md)
* [Wyrażenia warunkowe aparatu reguł](cdn-rules-engine-reference-conditional-expressions.md)
* [Funkcje aparatu reguł](cdn-rules-engine-reference-features.md)
* [Zastępowanie domyślnego zachowania HTTP przy użyciu aparatu reguł](cdn-rules-engine.md)

