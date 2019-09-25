---
title: Informacje o kluczach Azure Key Vault, wpisach tajnych i certyfikatach — Azure Key Vault
description: Omówienie Azure Key Vault interfejsu REST i szczegółów dla deweloperów kluczy, wpisów tajnych i certyfikatów.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: cca4f794fd3f84b991c7882307f74bcfadf6835b
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71241053"
---
# <a name="about-keys-secrets-and-certificates"></a>Klucze, wpisy tajne i certyfikaty — informacje

Azure Key Vault umożliwia aplikacjom Microsoft Azure i użytkownikom przechowywanie i używanie kilku typów danych tajnych/kluczy:

- Klucze kryptograficzne: Obsługuje wiele typów kluczy i algorytmów oraz umożliwia korzystanie z sprzętowych modułów zabezpieczeń (HSM) dla kluczy o wysokiej wartości. 
- Klucz Zapewnia bezpieczny magazyn wpisów tajnych, takich jak hasła i parametry połączenia bazy danych.
- Przystawki Obsługuje certyfikaty, które są oparte na kluczach i wpisach tajnych i dodają funkcję automatycznego odnawiania.
- Azure Storage: Może zarządzać kluczami konta usługi Azure Storage. Wewnętrznie Key Vault mogą wyświetlać (synchronizować) klucze przy użyciu konta usługi Azure Storage i ponownie generować (obrócić) klucze okresowo. 

Aby uzyskać więcej ogólnych informacji na temat Key Vault, zobacz [co to jest Azure Key Vault?](/azure/key-vault/key-vault-overview)

## <a name="azure-key-vault"></a>W usłudze Azure Key Vault

Poniższe sekcje zawierają ogólne informacje dotyczące implementacji usługi Key Vault.

### <a name="supporting-standards"></a>Standardy obsługi

Specyfikacja JavaScript Object Notation (JSON) i podpisywanie i szyfrowanie obiektów JavaScript są ważnymi informacjami w tle.  

-   [Klucz internetowy JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [Szyfrowanie sieci Web JSON (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [Algorytmy sieci Web JSON (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [Podpis internetowy JSON (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature)  

### <a name="data-types"></a>Typy danych

Zapoznaj się ze specyfikacją JOSE dla odpowiednich typów danych dla kluczy, szyfrowania i podpisywania.  

-   **algorytm** — obsługiwany algorytm dla operacji klucza, na przykład RSA1_5  
-   tekst **szyfrowany-wartość** — umożliwia szyfrowanie oktetów tekstu zakodowanych przy użyciu Base64URL  
-   **Digest-Value** — dane wyjściowe algorytmu wyznaczania wartości skrótu zakodowane przy użyciu Base64URL  
-   **Typ klucza** — jeden z obsługiwanych typów kluczy, na przykład RSA (Rivest-Shamir-Adleman).  
-   **zwykły tekst —** oktety w postaci zwykłego tekstu, kodowane przy użyciu Base64URL  
-   **sygnatura-wartość** — dane wyjściowe algorytmu podpisu zakodowane przy użyciu Base64URL  
-   **base64URL** -a BASE64URL [RFC4648] zakodowana wartość binarną  
-   wartość **logiczna** — true lub false  
-   **Tożsamość** — tożsamość z Azure Active Directory (AAD).  
-   **IntDate** — wartość dziesiętna JSON reprezentująca liczbę sekund od 1970-01-01T0:0: 0Z UTC do określonej daty/godziny UTC. Zobacz RFC3339, aby uzyskać szczegółowe informacje dotyczące daty/godziny, ogólnie i czasu UTC.  

### <a name="objects-identifiers-and-versioning"></a>Obiekty, identyfikatory i przechowywanie wersji

Obiekty przechowywane w Key Vault są używane w wersji za każdym razem, gdy zostanie utworzone nowe wystąpienie obiektu. Każda wersja ma przypisany unikatowy identyfikator i adres URL. Gdy obiekt jest tworzony, otrzymuje unikatowy identyfikator wersji i jest oznaczony jako bieżąca wersja obiektu. Utworzenie nowego wystąpienia o tej samej nazwie obiektu daje nowemu obiektowi unikatowy identyfikator wersji, powodując, że staje się on bieżącą wersją.  

Obiekty w Key Vault mogą być rozkierowane przy użyciu bieżącego identyfikatora lub identyfikatora specyficznego dla wersji. Na przykład, jeśli klucz o nazwie `MasterKey`, wykonywanie operacji z bieżącym identyfikatorem powoduje, że system używa najnowszej dostępnej wersji. Wykonywanie operacji z identyfikatorem specyficznym dla wersji powoduje, że system używa tej konkretnej wersji obiektu.  

Obiekty są jednoznacznie identyfikowane w Key Vault przy użyciu adresu URL. Żadne dwa obiekty w systemie nie mają tego samego adresu URL, niezależnie od lokalizacji geograficznej. Pełny adres URL do obiektu jest nazywany identyfikatorem obiektu. Adres URL składa się z prefiksu, który identyfikuje Key Vault, typ obiektu, nazwę obiektu dostarczoną przez użytkownika i wersję obiektu. Nazwa obiektu nie uwzględnia wielkości liter i jest niezmienna. Identyfikatory, które nie zawierają wersji obiektu, są określane jako identyfikatory podstawowe.  

Aby uzyskać więcej informacji, zobacz [uwierzytelnianie, żądania i odpowiedzi](authentication-requests-and-responses.md)

Identyfikator obiektu ma następujący format ogólny:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Gdzie:  

|||  
|-|-|  
|`keyvault-name`|Nazwa magazynu kluczy w usłudze Microsoft Azure Key Vault.<br /><br /> Nazwy Key Vault są wybrane przez użytkownika i są unikatowe globalnie.<br /><br /> Nazwa Key Vault musi być ciągiem znaków 3-24, zawierającym tylko 0-9, a-z, A-Z i-.|  
|`object-type`|Typ obiektu ("klucze" lub "tajemnice").|  
|`object-name`|A `object-name` to nazwa podana przez użytkownika dla i musi być unikatowa w obrębie Key Vault. Nazwa musi być ciągiem znaków 1-127, zawierającym tylko 0-9, a-z, A-Z i-.|  
|`object-version`|`object-version` Jest wygenerowanym przez system 32 identyfikatorem ciągu znaków, który jest opcjonalnie używany * o adres unikatowej wersji obiektu.|  

## <a name="key-vault-keys"></a>Klucze Key Vault

### <a name="keys-and-key-types"></a>Klucze i typy kluczy

Klucze kryptograficzne w Key Vault są reprezentowane jako obiekty klucza internetowego JSON [JWK]. Podstawowe specyfikacje JWK/JWA są również rozszerzane w celu włączenia typów kluczy unikatowych dla implementacji Key Vault. Na przykład Importowanie kluczy za pomocą pakietów HSM specyficznych dla dostawcy umożliwia bezpieczne transport kluczy, których można używać tylko w Key Vault sprzętowych modułów zabezpieczeń.  

- **Klucze "miękkie"** : Klucz przetworzony w oprogramowaniu Key Vault, ale jest szyfrowany w spoczynku przy użyciu klucza systemowego, który znajduje się w module HSM. Klienci mogą zaimportować istniejący klucz RSA lub we (krzywa eliptyczna) albo żądanie, które Key Vault wygenerować.
- **Klucze "twarde"** : Klucz przetworzony w module HSM (sprzętowego modułu zabezpieczeń). Te klucze są chronione w jednym z Key Vault światowych zabezpieczeń środowiska HSM (istnieje jeden świat zabezpieczeń na geografię, aby zachować izolację). Klienci mogą zaimportować klucz RSA lub we w postaci miękkiej lub eksportu z zgodnego urządzenia HSM. Klienci mogą również zażądać Key Vault wygenerowania klucza. Ten typ klucza dodaje atrybut key_hsm do JWK do przenoszenia materiału klucza HSM.

     Aby uzyskać więcej informacji na temat granic geograficznych, zobacz [Microsoft Azure Centrum zaufania](https://azure.microsoft.com/support/trust-center/privacy/)  

Key Vault obsługuje tylko klucze RSA i eliptyczna krzywej eliptycznej. 

-   **EC**: "Miękki" klucz krzywej eliptycznej.
-   **EC-HSM**: "Twarda" wartość klucza krzywej eliptycznej.
-   **KLUCZ RSA**: Klucz RSA "Soft".
-   **RSA-HSM**: "Twarda" klucz RSA.

Key Vault obsługuje klucze RSA o rozmiarach 2048, 3072 i 4096. Key Vault obsługuje typy kluczy krzywej eliptycznej P-256, P-384, P-521 i P-256 K (SECP256K1).

### <a name="cryptographic-protection"></a>Ochrona kryptograficzna

Moduły kryptograficzne, które Key Vault używają, czy moduł HSM lub oprogramowanie są sprawdzone (Federal Information Processing Standards). Nie musisz wykonywać żadnych specjalnych czynności do uruchamiania w trybie FIPS. Klucze **utworzone** lub **zaimportowane** jako chronione przez moduł HSM są przetwarzane w module HSM, zweryfikowane pod kątem standardu FIPS 140-2 Level 2. Klucze **utworzone** lub **zaimportowane** jako chronione przez oprogramowanie są przetwarzane w ramach modułów kryptograficznych zweryfikowanych pod kątem standardu FIPS 140-2 Level 1. Aby uzyskać więcej informacji, zobacz [klucze i typy kluczy](#keys-and-key-types).

###  <a name="ec-algorithms"></a>Algorytmy we
 W Key Vault są obsługiwane następujące identyfikatory algorytmów w przypadku kluczy modułu HSM we i w systemie. 

#### <a name="curve-types"></a>Typy krzywych

-   **P-256** -krzywa NIST p-256, zdefiniowana w trybie [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-256 k** — krzywa s SECP256K1, zdefiniowana w [s 2: Zalecane parametry](https://www.secg.org/sec2-v2.pdf)domeny krzywej eliptycznej.
-   **P-384** -krzywa NIST p-384, zdefiniowana w trybie [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-521** -krzywa NIST p-521, zdefiniowana w trybie [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).

#### <a name="signverify"></a>PODPISZ/WERYFIKUJ

-   **ES256** -ECDSA dla skrótów SHA-256 i kluczy utworzonych przy użyciu krzywej P-256. Ten algorytm został opisany w [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES256K** -ECDSA dla skrótów SHA-256 i kluczy utworzonych przy użyciu krzywej P-256 k. Ten algorytm oczekuje na standaryzację.
-   **ES384** -ECDSA dla skrótów SHA-384 i kluczy utworzonych przy użyciu krzywej P-384. Ten algorytm został opisany w [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES512** -ECDSA dla skrótów SHA-512 i kluczy utworzonych przy użyciu krzywej P-521. Ten algorytm został opisany w [RFC7518](https://tools.ietf.org/html/rfc7518).

###  <a name="rsa-algorithms"></a>Algorytmy RSA  
 W Key Vault są obsługiwane następujące identyfikatory algorytmów RSA i RSA-HSM.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, SZYFROWANIE/ODSZYFROWYWANIE

-   **RSA1_5** -RSAES-PKCS1-V1_5 [RFC3447] szyfrowanie klucza  
-   **RSA-OAEP** -RSAES przy użyciu optymalnego wypełnienia asymetrycznego szyfrowania (OAEP) [RFC3447] z domyślnymi parametrami określonymi w dokumencie RFC 3447 w sekcji A. 2.1. Te parametry domyślne używają funkcji hash algorytmu SHA-1 i funkcji generowania maski MGF1 z algorytmem SHA-1.  

#### <a name="signverify"></a>PODPISZ/WERYFIKUJ

-   **RS256** -RSASSA-PKCS-v1_5 przy użyciu algorytmu SHA-256. Wartość skrótu podana w aplikacji musi być obliczona przy użyciu algorytmu SHA-256 i musi mieć długość 32 bajtów.  
-   **RS384** -RSASSA-PKCS-v1_5 przy użyciu algorytmu SHA-384. Wartość skrótu podana w aplikacji musi być obliczona przy użyciu algorytmu SHA-384 i musi mieć długość 48 bajtów.  
-   **RS512** -RSASSA-PKCS-v1_5 przy użyciu algorytmu SHA-512. Wartość skrótu podana w aplikacji musi być obliczona przy użyciu algorytmu SHA-512 i musi mieć długość 64 bajtów.  
-   **RSNULL** — Zobacz [RFC2437], wyspecjalizowany przypadek użycia, aby włączyć określone scenariusze protokołu TLS.  

###  <a name="key-operations"></a>Najważniejsze operacje

Key Vault obsługuje następujące operacje na obiektach kluczowych:  

-   **Utwórz**: Umożliwia klientowi utworzenie klucza w Key Vault. Wartość klucza jest generowana przez Key Vault i jest przechowywana i nie jest wydawana dla klienta. Klucze asymetryczne mogą być tworzone w Key Vault.  
-   **Importuj**: Umożliwia klientowi Importowanie istniejącego klucza do Key Vault. Klucze asymetryczne mogą być importowane do Key Vault przy użyciu wielu różnych metod pakowania w ramach konstrukcji JWK. 
-   **Aktualizacja**: Umożliwia klientowi z wystarczającymi uprawnieniami do modyfikowania metadanych (atrybutów kluczowych) skojarzonych z kluczem, który został wcześniej zapisany w Key Vault.  
-   **Usuń**: Zezwala klientowi z odpowiednimi uprawnieniami do usuwania klucza z Key Vault.  
-   **Lista**: Umożliwia klientowi Wyświetlanie listy wszystkich kluczy w danym Key Vault.  
-   **Wersje list**: Umożliwia klientowi Wyświetlanie listy wszystkich wersji danego klucza w danym Key Vault.  
-   **Pobierz**: Zezwala klientowi na pobieranie publicznych części danego klucza w Key Vault.  
-   **Kopia zapasowa**: Eksportuje klucz w formularzu chronionym.  
-   **Przywróć**: Importuje poprzednio kopię zapasową klucza.  

Aby uzyskać więcej informacji, zobacz [kluczowe operacje w dokumentacji interfejsu API REST Key Vault](/rest/api/keyvault).  

Po utworzeniu klucza w Key Vault można wykonać następujące operacje kryptograficzne przy użyciu klucza:  

-   **Podpisz i Weryfikuj**: W przypadku tej operacji jest to "podpisywanie skrótu" lub "Weryfikuj wartość skrótu", ponieważ Key Vault nie obsługuje mieszania zawartości w ramach tworzenia podpisu. Aplikacje powinny skrótować dane, które mają być podpisane lokalnie, a następnie żądać Key Vault podpisywania skrótu. Weryfikacja podpisywanych skrótów jest obsługiwana jako wygodna operacja dla aplikacji, które mogą nie mieć dostępu do materiału klucza [Public]. Aby zapewnić najlepszą wydajność aplikacji, należy sprawdzić, czy operacje są wykonywane lokalnie.  
-   **Szyfrowanie klucza/Zawijanie**: Klucz przechowywany w Key Vault może służyć do ochrony innego klucza, zazwyczaj klucza szyfrowania zawartości symetrycznej (CEK). Gdy klucz w Key Vault jest asymetryczny, używane jest szyfrowanie klucza. Na przykład RSA-OAEP i operacje WRAPKEY/UNWRAPKEY są równoważne z SZYFROWANIEm/ODSZYFROWYWAniem. Gdy klucz w Key Vault jest symetryczny, używane jest Zawijanie kluczy. Na przykład AES-KW. Operacja WRAPKEY jest obsługiwana jako wygoda dla aplikacji, które mogą nie mieć dostępu do materiału klucza [Public]. W celu uzyskania najlepszej wydajności aplikacji WRAPKEY operacje powinny być wykonywane lokalnie.  
-   **Szyfrowanie i odszyfrowywanie**: Klucz zapisany w Key Vault może służyć do szyfrowania lub odszyfrowywania pojedynczego bloku danych. Rozmiar bloku jest określany na podstawie typu klucza i wybranego algorytmu szyfrowania. Operacja szyfrowania została udostępniona dla wygody dla aplikacji, które mogą nie mieć dostępu do materiału klucza [Public]. W celu uzyskania najlepszej wydajności aplikacji należy wykonać operacje szyfrowania lokalnie.  

Podczas gdy WRAPKEY/UNWRAPKEY użycie kluczy asymetrycznych może wydawać się zbędne (ponieważ operacja jest równoważna z SZYFROWANIEm/ODSZYFROWYWAniem), użycie odrębnych operacji jest ważne. Rozróżnienie zapewnia semantykę i separację autoryzacji tych operacji oraz spójność, gdy inne typy kluczy są obsługiwane przez usługę.  

Key Vault nie obsługuje operacji EKSPORTOWANIA. Po zainicjowaniu klucza w systemie nie można go wyodrębnić ani zmodyfikować materiału klucza. Jednak użytkownicy Key Vault mogą wymagać ich klucza dla innych przypadków użycia, na przykład po usunięciu. W takim przypadku mogą oni użyć operacji tworzenia kopii zapasowej i przywracania, aby wyeksportować/zaimportować klucz w formularzu chronionym. Klucze utworzone przez operację tworzenia kopii zapasowej nie mogą być używane poza Key Vault. Alternatywnie operacja importowania może być używana z wieloma wystąpieniami Key Vault.  

Użytkownicy mogą ograniczyć każdą z operacji kryptograficznych, które Key Vault obsługiwane przez poszczególne klucze przy użyciu właściwości key_ops obiektu JWK.  

Aby uzyskać więcej informacji na temat obiektów JWK, zobacz [JSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key).  

###  <a name="key-attributes"></a>Atrybuty klucza

Oprócz materiału kluczowego można określić następujące atrybuty. W żądaniu JSON atrybuty słowo kluczowe i nawiasy klamrowe "{" "}" są wymagane nawet wtedy, gdy nie określono żadnych atrybutów.  

- *włączone*: wartość logiczna, opcjonalna, **wartość**domyślna to true. Określa, czy klucz jest włączony i użyteczny dla operacji kryptograficznych. *Włączony* atrybut jest używany w połączeniu z *NBF* i *EXP*. Gdy operacja przejdzie między *NBF* i *EXP*, będzie dozwolona tylko wtedy, gdy wartość *Enabled* jest ustawiona na **true**. Operacje poza oknem*EXP* protokołu *NBF* / są automatycznie niedozwolone, z wyjątkiem niektórych typów operacji w [określonych warunkach](#date-time-controlled-operations).
- *nbf*: IntDate, opcjonalnie, domyślnie jest teraz. Atrybut *NBF* (nie wcześniej) określa czas, po którym klucz nie może być używany do operacji kryptograficznych, z wyjątkiem niektórych typów operacji w [określonych warunkach](#date-time-controlled-operations). Przetwarzanie atrybutu *NBF* wymaga, aby bieżąca data/godzina musiała być późniejsza lub równa wartości nie wcześniejsza niż data/godzina określona w atrybucie *NBF* . Key Vault może przewidywać nieco małych Leeway, zwykle nie więcej niż kilka minut, aby uwzględnić pochylenie zegara. Wartość musi być liczbą zawierającą wartość IntDate.  
- *EXP*: IntDate, opcjonalne, wartość domyślna to "nieskończona". Atrybut *EXP* (czas wygaśnięcia) określa czas wygaśnięcia dla lub po upływie którego klucz nie może być używany do operacji kryptograficznej, z wyjątkiem niektórych typów operacji w [określonych warunkach](#date-time-controlled-operations). Przetwarzanie atrybutu *EXP* wymaga, aby bieżąca data/godzina musiała być wcześniejsza niż data/godzina wygaśnięcia określona w atrybucie *EXP* . Key Vault może przewidywać nieco małych Leeway, zwykle nie więcej niż kilka minut, do konta w celu przesunięcia zegara. Wartość musi być liczbą zawierającą wartość IntDate.  

Istnieją dodatkowe atrybuty tylko do odczytu, które znajdują się w dowolnej odpowiedzi zawierającej atrybuty klucza:  

- *utworzono*: IntDate, opcjonalnie. *Utworzony* atrybut wskazuje, kiedy ta wersja klucza została utworzona. Wartość jest zerowa dla kluczy utworzonych przed dodaniem tego atrybutu. Wartość musi być liczbą zawierającą wartość IntDate.  
- *Zaktualizowano*: IntDate, opcjonalnie. *Zaktualizowany* atrybut wskazuje, kiedy ta wersja klucza została zaktualizowana. Wartość jest równa null dla kluczy, które były ostatnio aktualizowane przed dodaniem tego atrybutu. Wartość musi być liczbą zawierającą wartość IntDate.  

Aby uzyskać więcej informacji na temat IntDate i innych typów danych, zobacz [typy danych](#data-types)  

#### <a name="date-time-controlled-operations"></a>Operacje kontrolowane przez datę i godzinę

Klucze, które nie są jeszcze prawidłowe i wygasły, poza oknem*EXP* usługi *NBF* / , będądziałały w przypadku operacji odszyfrowania, odpakowania i **weryfikacji** (nie zwróci 403, zabronione). Uzasadnienie użycia nieprawidłowego stanu ma umożliwić przetestowanie klucza przed użyciem produkcji. Uzasadnienie dotyczące korzystania z stanu wygasłego polega na umożliwieniu operacji odzyskiwania danych, które zostały utworzone, gdy klucz był prawidłowy. Ponadto można wyłączyć dostęp do klucza przy użyciu zasad Key Vault lub przez zaktualizowanie atrybutu *Enabled* klucza na **wartość false**.

Aby uzyskać więcej informacji na temat typów danych, zobacz [typy danych](#data-types).

Aby uzyskać więcej informacji na temat innych możliwych atrybutów, zobacz [klucz internetowy JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key).

### <a name="key-tags"></a>Najważniejsze Tagi

W postaci tagów można określić dodatkowe metadane specyficzne dla aplikacji. Key Vault obsługuje do 15 tagów, z których każdy może mieć nazwę znaku 256 i wartość znaku 256.  

>[!Note]
>Znaczniki są odczytywane przez obiekt wywołujący, jeśli mają *listę* lub *uzyskują* uprawnienia do tego typu obiektu (klucze, wpisy tajne lub certyfikaty).

###  <a name="key-access-control"></a>Kontrola dostępu do kluczy

Kontrola dostępu do kluczy zarządzanych przez Key Vault jest udostępniana na poziomie Key Vault, który działa jako kontener kluczy. Zasady kontroli dostępu dla kluczy różnią się od zasad kontroli dostępu dla wpisów tajnych w tym samym Key Vault. Użytkownicy mogą utworzyć jeden lub więcej magazynów w celu przechowywania kluczy i muszą utrzymywać odpowiednie segmentacje i zarządzanie kluczami. Kontrola dostępu do kluczy jest niezależna od kontroli dostępu do wpisów tajnych.  

Następujące uprawnienia można udzielić dla poszczególnych użytkowników/głównych usług, w wpis kontroli dostępu do kluczy w magazynie. Te uprawnienia ściśle duplikują operacje dozwolone dla obiektu klucza.  Udzielanie dostępu do jednostki usługi w magazynie kluczy jest operacją jednorazowej, która pozostanie taka sama dla wszystkich subskrypcji platformy Azure. Można go użyć do wdrożenia dowolnej liczby certyfikatów. 

- Uprawnienia do operacji zarządzania kluczami
  - *Pobierz*: Odczytaj publiczną część klucza oraz jej atrybuty
  - *Lista*: Wyświetl listę kluczy lub wersji klucza przechowywanego w magazynie kluczy
  - *Aktualizacja*: Aktualizowanie atrybutów klucza
  - *Utwórz*: Utwórz nowe klucze
  - *Importuj*: Importowanie klucza do magazynu kluczy
  - *Usuń*: Usuń obiekt klucza
  - *Odzyskaj*: Odzyskaj usunięty klucz
  - *kopia zapasowa*: Tworzenie kopii zapasowej klucza w magazynie kluczy
  - *Przywróć*: Przywracanie klucza kopii zapasowej w magazynie kluczy

- Uprawnienia do operacji kryptograficznych
  - *Odszyfruj*: Użyj klucza, aby wyłączyć ochronę sekwencji bajtów
  - *szyfrowanie*: Użyj klucza do ochrony dowolnej sekwencji bajtów
  - *unwrapKey*: Użyj klucza, aby wyłączyć ochronę opakowanych kluczy symetrycznych
  - *wrapKey*: Użyj klucza do ochrony klucza symetrycznego
  - *Weryfikuj*: Użyj klucza, aby zweryfikować skróty  
  - *Podpisz*: Użyj klucza do podpisywania skróconych
    
- Uprawnienia dla operacji uprzywilejowanych
  - *przeczyszczanie*: Przeczyszczanie (trwałe usuwanie) usuniętego klucza

Aby uzyskać więcej informacji na temat pracy z kluczami, zobacz [najważniejsze operacje w temacie Informacje o interfejsie API REST Key Vault](/rest/api/keyvault). Aby uzyskać informacje dotyczące ustanawiania uprawnień, zobacz temat [magazyny — Tworzenie lub aktualizowanie](/rest/api/keyvault/vaults/createorupdate) i [magazyny — zasady dostępu aktualizacji](/rest/api/keyvault/vaults/updateaccesspolicy). 

## <a name="key-vault-secrets"></a>Key Vault wpisy tajne 

### <a name="working-with-secrets"></a>Praca z wpisami tajnymi

Z perspektywy deweloperów Key Vault interfejsy API akceptują i zwracają wartości tajne jako ciągi. Wewnętrznie program Key Vault przechowuje klucze tajne i zarządza nimi jako sekwencje oktetów (8-bitowych bajtów), a każdy z nich ma maksymalny rozmiar 25k bajtów. Usługa Key Vault nie zapewnia semantyki dla wpisów tajnych. Tylko akceptuje dane, szyfruje je, zapisuje i zwraca identyfikator tajny ("ID"). Identyfikatora można użyć do pobrania klucza tajnego w późniejszym czasie.  

W przypadku wysoce poufnych danych klienci powinni rozważyć dodatkowe warstwy ochrony danych. Szyfrowanie danych przy użyciu oddzielnego klucza ochrony przed magazynem w Key Vault jest jednym z przykładów.  

Key Vault obsługuje również pole ContentType dla wpisów tajnych. Klienci mogą określić typ zawartości wpisu tajnego, aby pomóc w interpretacji danych tajnych podczas pobierania. Maksymalna długość tego pola to 255 znaków. Brak wstępnie zdefiniowanych wartości. Sugerowane użycie jest wskazówką dotyczącą interpretacji danych tajnych. Na przykład implementacja może przechowywać hasła i certyfikaty jako wpisy tajne, a następnie używać tego pola do rozróżniania. Brak wstępnie zdefiniowanych wartości.  

### <a name="secret-attributes"></a>Atrybuty wpisu tajnego

Oprócz danych tajnych można określić następujące atrybuty:  

- *EXP*: IntDate, opcjonalne, wartość domyślnato nieskończoność. Atrybut *EXP* (czas wygaśnięcia) określa czas wygaśnięcia lub po którym dane tajne nie powinny być pobierane, z wyjątkiem [określonych sytuacji](#date-time-controlled-operations). To pole służy tylko do celów informacyjnych, ponieważ informuje użytkowników usługi magazynu kluczy, że nie można użyć określonego klucza tajnego. Wartość musi być liczbą zawierającą wartość IntDate.   
- *nbf*: IntDate, opcjonalnie, domyślnie jest **teraz**. Atrybut *NBF* (nie wcześniej) określa czas, po którym dane tajne nie powinny być pobierane, z wyjątkiem [określonych sytuacji](#date-time-controlled-operations). To pole służy tylko do celów informacyjnych. Wartość musi być liczbą zawierającą wartość IntDate. 
- *włączone*: wartość logiczna, opcjonalna, **wartość**domyślna to true. Ten atrybut określa, czy można pobrać dane tajne. Atrybut Enabled jest używany w połączeniu z *NBF* i *EXP* , gdy operacja przejdzie między *NBF* i *EXP*, będzie dozwolona tylko wtedy, gdy ustawienie Enabled ma **wartość true**. Operacje poza oknem *NBF* i *EXP* są automatycznie niedozwolone, z wyjątkiem [określonych sytuacji](#date-time-controlled-operations).  

Istnieją dodatkowe atrybuty tylko do odczytu, które znajdują się w dowolnej odpowiedzi zawierającej atrybuty tajne:  

- *utworzono*: IntDate, opcjonalnie. Utworzony atrybut wskazuje, kiedy ta wersja wpisu tajnego została utworzona. Ta wartość jest zerowa dla wpisów tajnych utworzonych przed dodaniem tego atrybutu. Wartość musi być liczbą zawierającą wartość IntDate.  
- *Zaktualizowano*: IntDate, opcjonalnie. Zaktualizowany atrybut wskazuje, kiedy ta wersja wpisu tajnego została zaktualizowana. Ta wartość jest zerowa dla wpisów tajnych, które były ostatnio aktualizowane przed dodaniem tego atrybutu. Wartość musi być liczbą zawierającą wartość IntDate.

#### <a name="date-time-controlled-operations"></a>Operacje kontrolowane przez datę i godzinę

Operacja **pobrania** wpisu tajnego będzie działała w przypadku nieprawidłowych i wygasłych wpisów tajnych poza oknem*wygaśnięcia* protokołu *NBF* / . Wywoływanie operacji **Get** wpisu tajnego dla nieprawidłowego hasła tajnego może być używane do celów testowych. Pobieranie (**pobieranie**) wygasłego klucza tajnego może być używane na potrzeby operacji odzyskiwania.

Aby uzyskać więcej informacji na temat typów danych, zobacz [typy danych](#data-types).  

### <a name="secret-access-control"></a>Kontrola dostępu do wpisów tajnych

Access Control dla wpisów tajnych zarządzanych w programie Key Vault jest dostępny na poziomie Key Vault, który zawiera te klucze tajne. Zasady kontroli dostępu dla wpisów tajnych, różnią się od zasad kontroli dostępu dla kluczy w tym samym Key Vault. Użytkownicy mogą utworzyć jeden lub więcej magazynów w celu przechowywania wpisów tajnych i muszą utrzymywać odpowiednie segmentacje i zarządzanie wpisami tajnymi.   

Następujące uprawnienia mogą być używane dla każdego podmiotu zabezpieczeń w ramach wpisu kontroli dostępu tajnego w magazynie i ściśle duplikaty operacji dozwolonych na obiekcie tajnym:  

- Uprawnienia do operacji zarządzania kluczami tajnymi
  - *Pobierz*: Odczytaj wpis tajny  
  - *Lista*: Wyświetlenie listy tajnych lub wersji klucza tajnego przechowywanych w Key Vault  
  - *Ustaw*: Tworzenie wpisu tajnego  
  - *Usuń*: Usuń klucz tajny  
  - *Odzyskaj*: Odzyskiwanie usuniętego klucza tajnego
  - *kopia zapasowa*: Tworzenie kopii zapasowej wpisu tajnego w magazynie kluczy
  - *Przywróć*: Przywróć kopię zapasową wpisu tajnego do magazynu kluczy

- Uprawnienia dla operacji uprzywilejowanych
  - *przeczyszczanie*: Przeczyszczanie (trwałe usuwanie) usuniętego klucza tajnego

Aby uzyskać więcej informacji na temat pracy z wpisami tajnymi, zobacz [Secret Operations w temacie Informacje o interfejsie API REST Key Vault](/rest/api/keyvault). Aby uzyskać informacje dotyczące ustanawiania uprawnień, zobacz temat [magazyny — Tworzenie lub aktualizowanie](/rest/api/keyvault/vaults/createorupdate) i [magazyny — zasady dostępu aktualizacji](/rest/api/keyvault/vaults/updateaccesspolicy). 

### <a name="secret-tags"></a>Tagi tajne  
W postaci tagów można określić dodatkowe metadane specyficzne dla aplikacji. Key Vault obsługuje do 15 tagów, z których każdy może mieć nazwę znaku 256 i wartość znaku 256.  

>[!Note]
>Znaczniki są odczytywane przez obiekt wywołujący, jeśli mają *listę* lub *uzyskują* uprawnienia do tego typu obiektu (klucze, wpisy tajne lub certyfikaty).

## <a name="key-vault-certificates"></a>Key Vault certyfikaty

Obsługa certyfikatów Key Vault umożliwia zarządzanie certyfikatami x509 i następującymi zachowaniami:  

-   Umożliwia właścicielowi certyfikatu utworzenie certyfikatu za pomocą procesu tworzenia Key Vault lub przez zaimportowanie istniejącego certyfikatu. Obejmuje certyfikaty wygenerowane zarówno z podpisem własnym, jak i urzędu certyfikacji.
-   Umożliwia właścicielowi certyfikatu Key Vault implementowanie bezpiecznego magazynu i zarządzanie certyfikatami x509 bez interakcji z materiałem klucza prywatnego.  
-   Umożliwia właścicielowi certyfikatu utworzenie zasad, które kierują Key Vault do zarządzania cyklem życia certyfikatu.  
-   Umożliwia właścicielom certyfikatu udostępnianie informacji kontaktowych w celu powiadomienia o zdarzeniach cyklu życia i odnowieniu certyfikatu.  
-   Obsługuje automatyczne odnawianie z wybranymi wystawcami — dostawcy certyfikatów x509 Key Vault partner/urzędy certyfikacji.

>[!Note]
>Niebędący dostawcami/urzędy są również dozwolone, ale nie obsługują funkcji autoodnawiania.

### <a name="composition-of-a-certificate"></a>Składanie certyfikatu

Po utworzeniu certyfikatu Key Vault zostanie również utworzony klucz adresowy i wpis tajny o tej samej nazwie. Klucz Key Vault pozwala na wykonywanie kluczowych operacji, a klucz tajny Key Vault umożliwia pobieranie wartości certyfikatu jako klucza tajnego. Certyfikat Key Vault zawiera również publiczne metadane certyfikatu x509.  

Identyfikator i wersja certyfikatów są podobne do tych kluczy i wpisów tajnych. W odpowiedzi na certyfikat Key Vault jest dostępna określona wersja klucza i wpisu tajnego, który został utworzony przy użyciu wersji certyfikatu Key Vault.
 
![Certyfikaty są obiektami złożonymi](media/azure-key-vault.png)

### <a name="exportable-or-non-exportable-key"></a>Klucz możliwy do eksportu lub niemożliwy do eksportu

Po utworzeniu certyfikatu Key Vault można go pobrać ze klucza prywatnego z adresami w formacie PFX lub PEM. Zasady użyte do utworzenia certyfikatu muszą wskazywać, że klucz jest eksportowalny. Jeśli zasady wskazują, że nie można eksportować, klucz prywatny nie jest częścią wartości, gdy zostanie pobrany jako wpis tajny.  

Klucz adresowania jest bardziej istotny dla niemożliwych do eksportu certyfikatów KV. Operacje na kluczu KV z adresami są mapowane z pola *użycie* klucza dla zasad certyfikatu KV użytych do utworzenia certyfikatu kV.  

Obsługiwane są dwa typy kluczy — moduł HSM *RSA* lub *RSA* z certyfikatami. Eksport jest dozwolony tylko za pomocą RSA, ale nie jest obsługiwany przez moduł HSM RSA.  

### <a name="certificate-attributes-and-tags"></a>Atrybuty i Tagi certyfikatu

Oprócz metadanych certyfikatu, klucz adresowy i wpis tajny adresowane, certyfikat Key Vault również zawiera atrybuty i Tagi.  

#### <a name="attributes"></a>Atrybuty

Atrybuty certyfikatu są duplikowane do atrybutów klucza, który można adresować i wpisu tajnego utworzonych podczas tworzenia certyfikatu KV.  

Certyfikat Key Vault ma następujące atrybuty:  

-   *włączone*: wartość logiczna, opcjonalna, **wartość**domyślna to true. Można określić, aby wskazać, czy dane certyfikatu mogą być pobierane jako tajne, czy jako klucz. Używany także w połączeniu z *NBF* i *EXP* , gdy operacja występuje między *NBF* i *EXP*, i będzie dozwolona tylko wtedy, gdy wartość Enabled jest ustawiona na true. Operacje poza oknem *NBF* i *EXP* są automatycznie niedozwolone.  

Istnieją dodatkowe atrybuty tylko do odczytu, które znajdują się w odpowiedzi:

-   *utworzono*: IntDate: wskazuje, kiedy ta wersja certyfikatu została utworzona.  
-   *Zaktualizowano*: IntDate: wskazuje, kiedy ta wersja certyfikatu została zaktualizowana.  
-   *EXP*: IntDate: zawiera wartość daty wygaśnięcia certyfikatu x509.  
-   *nbf*: IntDate: zawiera wartość daty certyfikatu x509.  

> [!Note] 
> Jeśli certyfikat Key Vault wygaśnie, klucz z adresami i wpis tajny stają się nieobsługiwane.  

#### <a name="tags"></a>`Tags`

 Określony przez klienta słownik par wartości klucza, podobny do tagów w kluczach i wpisach tajnych.  

 > [!Note]
> Znaczniki są odczytywane przez obiekt wywołujący, jeśli mają *listę* lub *uzyskują* uprawnienia do tego typu obiektu (klucze, wpisy tajne lub certyfikaty).

### <a name="certificate-policy"></a>Zasady certyfikatów

Zasady dotyczące certyfikatów zawierają informacje dotyczące sposobu tworzenia i zarządzania cyklem życia certyfikatu Key Vault. Gdy certyfikat z kluczem prywatnym zostanie zaimportowany do magazynu kluczy, zasady domyślne są tworzone przez odczytanie certyfikatu x509.  

Po utworzeniu certyfikatu Key Vault od podstaw należy podać zasady. Zasady określają sposób tworzenia tej wersji certyfikatu Key Vault lub następnej wersji certyfikatu Key Vault. Po ustanowieniu zasad nie jest to wymagane z kolejnymi operacjami tworzenia dla przyszłych wersji. Istnieje tylko jedno wystąpienie zasad dla wszystkich wersji certyfikatu Key Vault.  

Na wysokim poziomie zasady certyfikatów zawierają następujące informacje:  

-   Właściwości certyfikatu x509: Zawiera nazwę podmiotu, alternatywne nazwy podmiotu i inne właściwości używane do tworzenia żądania certyfikatu x509.  
-   Właściwości klucza: zawiera typ klucza, Długość klucza, możliwe do eksportowania i ponownie używane pola klucza. Te pola instruują Magazyn kluczy, aby utworzyć klucz.  
-   Właściwości klucza tajnego: zawiera właściwości tajne, takie jak typ zawartości klucza tajnego adresowanego do wygenerowania wartości klucza tajnego w celu pobierania certyfikatu jako klucza tajnego.  
-   Akcje okresu istnienia: zawiera akcje okresu istnienia dla certyfikatu KV. Każda akcja okresu istnienia zawiera:  

     - Wyzwalacz: określony przez dni przed upływem wartości procentowej wygaśnięcia lub okresu istnienia  

     - Akcja: Określanie typu akcji — *emailContacts* lub *autorenew*  

-   Issuer Parametry dotyczące wystawcy certyfikatu, które mają być używane do wystawiania certyfikatów x509.  
-   Atrybuty zasad: zawiera atrybuty skojarzone z zasadami  

#### <a name="x509-to-key-vault-usage-mapping"></a>Mapowanie użycia x509 do Key Vault

Poniższa tabela przedstawia mapowanie zasad użycia klucza x509 do efektywnych operacji kluczowych klucza utworzonego w ramach tworzenia certyfikatu Key Vault.

|**Flagi użycia klucza x509**|**Key Vault kluczowe operacje**|**Zachowanie domyślne**|
|----------|--------|--------|
|DataEncipherment|szyfrowanie, odszyfrowywanie| ND |
|DecipherOnly|zawartości| ND  |
|Bity digitalSignature|Podpisz, zweryfikuj| Key Vault domyślne bez określenia użycia podczas tworzenia certyfikatu | 
|EncipherOnly|encrypt| ND |
|KeyCertSign|Podpisz, zweryfikuj|ND|
|KeyEncipherment|wrapKey, unwrapKey| Key Vault domyślne bez określenia użycia podczas tworzenia certyfikatu | 
|Niemożność wyparcia|Podpisz, zweryfikuj| ND |
|crlsign bit|Podpisz, zweryfikuj| ND |

### <a name="certificate-issuer"></a>Wystawca certyfikatu

Obiekt certyfikatu Key Vault zawiera konfigurację służącą do komunikowania się z wybranym dostawcą wystawcy certyfikatu w celu uporządkowania certyfikatów x509.  

-   Key Vault partnerów z następującymi dostawcami wystawcy certyfikatów dla certyfikatów SSL

|**Nazwa dostawcy**|**Lokalizacje**|
|----------|--------|
|DigiCert|Obsługiwane we wszystkich lokalizacjach usługi magazynu kluczy w chmurze publicznej i Azure Government|
|GlobalSign|Obsługiwane we wszystkich lokalizacjach usługi magazynu kluczy w chmurze publicznej i Azure Government|

Przed utworzeniem wystawcy certyfikatu w Key Vault należy pomyślnie wykonać następujące czynności wstępne 1 i 2.  

1. Dołączanie do dostawców urzędu certyfikacji  

    -   Administrator organizacji musi być na pokładzie firmy (np. Firma Contoso) z co najmniej jednym dostawcą urzędu certyfikacji.  

2. Administrator tworzy poświadczenia żądającego Key Vault w celu zarejestrowania (i odnowienia) certyfikatów SSL  

    -   Zapewnia konfigurację, która ma zostać użyta do utworzenia obiektu wystawcy dostawcy w magazynie kluczy  

Aby uzyskać więcej informacji na temat tworzenia obiektów wystawcy z poziomu portalu certyfikatów, zobacz [blog Key Vault Certificates](https://aka.ms/kvcertsblog)  

Key Vault umożliwia tworzenie wielu obiektów wystawcy z inną konfiguracją dostawcy wystawcy. Po utworzeniu obiektu Issuer jego nazwy można odwoływać się w jednej lub wielu zasadach certyfikatów. Odwołujący się do obiektu wystawcy instruuje Key Vault, aby użyć konfiguracji określonej w obiekcie Issuer podczas żądania certyfikatu x509 od dostawcy urzędu certyfikacji podczas tworzenia i odnawiania certyfikatu.  

Obiekty wystawcy są tworzone w magazynie i mogą być używane tylko z certyfikatami KV w tym samym magazynie.  

### <a name="certificate-contacts"></a>Kontakty certyfikatów

Kontakty certyfikatów zawierają informacje kontaktowe do wysyłania powiadomień wyzwalanych przez zdarzenia okresu istnienia certyfikatu. Informacje o kontaktach są współużytkowane przez wszystkie certyfikaty w magazynie kluczy. Powiadomienie zostanie wysłane do wszystkich określonych kontaktów dla zdarzenia dowolnego certyfikatu w magazynie kluczy.  

Jeśli zasada certyfikatu jest ustawiona na automatyczne odnawianie, zostanie wysłane powiadomienie dotyczące następujących zdarzeń.  

- Przed odnowieniem certyfikatu
- Po odnowieniu certyfikatu, informując o tym, czy certyfikat został pomyślnie odnowiony, czy wystąpił błąd, wymagając ręcznego odnowienia certyfikatu.  

  Gdy zasady certyfikatu ustawione do ręcznego odnawiania (tylko wiadomości e-mail), po chwili odnowienia certyfikatu zostanie wysłane powiadomienie.  

### <a name="certificate-access-control"></a>Access Control certyfikatów

 Kontrola dostępu do certyfikatów jest zarządzana przez Key Vault i jest dostarczana przez Key Vault, które zawierają te certyfikaty. Zasady kontroli dostępu dla certyfikatów różnią się od zasad kontroli dostępu dla kluczy i wpisów tajnych w tym samym Key Vault. Użytkownicy mogą utworzyć jeden lub więcej magazynów w celu przechowywania certyfikatów, aby zachować scenariusz odpowiedniej segmentacji i zarządzania certyfikatami.  

 Następujące uprawnienia mogą być używane dla każdego podmiotu zabezpieczeń w pozycji kontrola dostępu do kluczy tajnych w magazynie kluczy i ściśle odzwierciedlają operacje dozwolone na obiekcie tajnym:  

- Uprawnienia do operacji zarządzania certyfikatami
  - *Pobierz*: Pobierz bieżącą wersję certyfikatu lub dowolną wersję certyfikatu 
  - *Lista*: Wyświetl listę bieżących certyfikatów lub wersji certyfikatu  
  - *Aktualizacja*: Aktualizowanie certyfikatu
  - *Utwórz*: Tworzenie certyfikatu Key Vault
  - *Importuj*: Importuj materiał certyfikatu do Key Vaultego certyfikatu
  - *Usuń*: Usuń certyfikat, jego zasady i wszystkie jego wersje  
  - *Odzyskaj*: Odzyskiwanie usuniętego certyfikatu
  - *kopia zapasowa*: Tworzenie kopii zapasowej certyfikatu w magazynie kluczy
  - *Przywróć*: Przywracanie kopii zapasowej certyfikatu w magazynie kluczy
  - *managecontacts*: Zarządzaj kontaktami z certyfikatem Key Vault  
  - *manageissuers*: Zarządzanie Key Vault urzędów certyfikacji/wystawców
  - getemitencis: Pobieranie urzędów certyfikacji/wystawców certyfikatów
  - *listissuers*: Wyświetlanie listy urzędów certyfikacji/wystawców certyfikatów  
  - setemitencis: Tworzenie lub aktualizowanie urzędów/wystawców certyfikatu Key Vault  
  - *deleteissuers*: Usuwanie urzędów certyfikacji i wystawców certyfikatów Key Vault  
 
- Uprawnienia dla operacji uprzywilejowanych
  - *przeczyszczanie*: Przeczyszczanie (trwałe usuwanie) usuniętego certyfikatu

Aby uzyskać więcej informacji, zobacz [operacje na certyfikatach w dokumentacji interfejsu API REST Key Vault](/rest/api/keyvault). Aby uzyskać informacje dotyczące ustanawiania uprawnień, zobacz temat [magazyny — Tworzenie lub aktualizowanie](/rest/api/keyvault/vaults/createorupdate) i [magazyny — zasady dostępu aktualizacji](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="azure-storage-account-key-management"></a>Zarządzanie kluczami konta usługi Azure Storage

Key Vault może zarządzać kluczami konta usługi Azure Storage:

- Wewnętrznie, Key Vault mogą wyświetlać (synchronizować) klucze za pomocą konta usługi Azure Storage. 
- Key Vault regeneruje ponownie klucze (obraca) okresowo.
- Wartości klucza nigdy nie są zwracane w odpowiedzi na obiekt wywołujący.
- Key Vault zarządza kluczami obu kont magazynu i klasycznych kont magazynu.

Aby uzyskać więcej informacji, zobacz [Azure Key Vault kluczy konta magazynu](key-vault-ovw-storage-keys.md)

### <a name="storage-account-access-control"></a>Kontrola dostępu do konta magazynu

Podczas autoryzacji użytkownika lub podmiotu zabezpieczeń aplikacji można używać następujących uprawnień do wykonywania operacji na zarządzanym koncie magazynu:  

- Uprawnienia do zarządzanego konta magazynu i operacji w definicji sygnatury dostępu współdzielonego
  - *Pobierz*: Pobiera informacje o koncie magazynu 
  - *Lista*: Wyświetlanie listy kont magazynu zarządzanych przez Key Vault
  - *Aktualizacja*: Aktualizowanie konta magazynu
  - *Usuń*: Usuwanie konta magazynu  
  - *Odzyskaj*: Odzyskiwanie usuniętego konta magazynu
  - *kopia zapasowa*: Tworzenie kopii zapasowej konta magazynu
  - *Przywróć*: Przywracanie kopii zapasowej konta magazynu do Key Vault
  - *Ustaw*: Utwórz lub zaktualizuj konto magazynu
  - *regeneratekey*: Wygeneruj ponownie określoną wartość klucza dla konta magazynu
  - *getsas*: Uzyskaj informacje na temat definicji sygnatury dostępu współdzielonego dla konta magazynu
  - *listsas*: Wyświetlanie listy definicji sygnatury dostępu współdzielonego magazynu dla konta magazynu
  - *deletesas*: Usuwanie definicji sygnatury dostępu współdzielonego z konta magazynu
  - *setsas*: Utwórz lub zaktualizuj nowe definicje/atrybuty sygnatury dostępu współdzielonego dla konta magazynu

- Uprawnienia dla operacji uprzywilejowanych
  - *przeczyszczanie*: Przeczyszczanie (trwałe usuwanie) zarządzanego konta magazynu

Aby uzyskać więcej informacji, zobacz [operacje związane z kontem magazynu w temacie Informacje o interfejsie API REST Key Vault](/rest/api/keyvault). Aby uzyskać informacje dotyczące ustanawiania uprawnień, zobacz temat [magazyny — Tworzenie lub aktualizowanie](/rest/api/keyvault/vaults/createorupdate) i [magazyny — zasady dostępu aktualizacji](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="see-also"></a>Zobacz też

- [Uwierzytelnianie, żądania i odpowiedzi](authentication-requests-and-responses.md)
- [Przewodnik dewelopera usługi Key Vault](/azure/key-vault/key-vault-developers-guide)
