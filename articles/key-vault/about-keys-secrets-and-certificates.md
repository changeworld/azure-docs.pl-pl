---
title: Informacje o kluczach, wpisach tajnych i certyfikatach usługi Azure Key Vault — usługa Azure Key Vault
description: Omówienie interfejsu REST usługi Azure Key Vault i szczegółów dewelopera dla kluczy, wpisów tajnych i certyfikatów.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: dd8be482009e067bf9016cc8e351fc42a2db39c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271735"
---
# <a name="about-keys-secrets-and-certificates"></a>Informacje o kluczach, wpisach tajnych i certyfikatach

Usługa Azure Key Vault umożliwia aplikacjom i użytkownikom platformy Microsoft Azure przechowywanie i używanie kilku typów danych tajnych/kluczowych:

- Klucze kryptograficzne: Obsługuje wiele typów kluczy i algorytmów i umożliwia korzystanie z sprzętowych modułów zabezpieczeń (HSM) dla kluczy o wysokiej wartości. 
- Wpisy tajne: zapewnia bezpieczne przechowywanie wpisów tajnych, takich jak hasła i parametry połączenia bazy danych.
- Certyfikaty: Obsługuje certyfikaty, które są zbudowane na kluczach i wpisach tajnych i dodają funkcję automatycznego odnawiania.
- Usługa Azure Storage: może zarządzać kluczami konta usługi Azure Storage. Wewnętrznie Usługa Key Vault może wymieniać (synchronizować) klucze za pomocą konta usługi Azure Storage i okresowo ponownie wygenerować (obrócić) klucze. 

Aby uzyskać bardziej ogólne informacje na temat usługi Key Vault, zobacz [Co to jest usługa Azure Key Vault?](/azure/key-vault/key-vault-overview)

## <a name="azure-key-vault"></a>W usłudze Azure Key Vault

Poniższe sekcje oferują ogólne informacje mające zastosowanie do implementacji usługi Usługi Usługi Key Vault.

### <a name="supporting-standards"></a>Standardy wspierające

Specyfikacje notacji i szyfrowania obiektów JavaScript (JSON) i JavaScript Object Signing and Encryption (JOSE) są ważnymi informacjami w tle.  

-   [Klucz sieci Web JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)  
-   [Szyfrowanie sieci Web JSON (JWE)](https://tools.ietf.org/html/draft-ietf-jose-json-web-encryption-40)  
-   [Algorytmy JSON Web (JWA)](https://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms-40)  
-   [Podpis JSON Web (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature-41)  

### <a name="data-types"></a>Typy danych

Informacje na temat odpowiednich typów danych dotyczących kluczy, szyfrowania i podpisywania można znaleźć w specyfikacjach jose.  

-   **algorytm** - obsługiwany algorytm dla operacji klucza, na przykład RSA1_5  
-   **wartość tekstu szyfrowania** - oktety tekstu szyfrowanego, zakodowane przy użyciu base64URL  
-   **digest-value** - dane wyjściowe algorytmu mieszania, zakodowane przy użyciu Base64URL  
-   **typ klucza** - jeden z obsługiwanych typów kluczy, na przykład RSA (Rivest-Shamir-Adleman).  
-   **wartość zwykłego tekstu** - oktety w postaci zwykłego tekstu, zakodowane przy użyciu base64URL  
-   **signature-value** - dane wyjściowe algorytmu podpisu zakodowanego przy użyciu base64URL  
-   **base64URL** - wartość binarna zakodowana w base64URL [RFC4648]  
-   **wartość logiczna** — prawda lub fałsz  
-   **Tożsamość** — tożsamość z usługi Azure Active Directory (AAD).  
-   **IntDate** - wartość dziesiętna JSON reprezentująca liczbę sekund od 1970-01-01T0:0:0Z UTC do określonej daty/godziny UTC. Szczegółowe informacje na temat daty/godziny można znaleźć w 19339 r., a w szczególności w przypadku czasu utc.  

### <a name="objects-identifiers-and-versioning"></a>Obiekty, identyfikatory i przechowywanie wersji

Obiekty przechowywane w przechowalni kluczy są wersjonatowane przy każdym utworzeniu nowego wystąpienia obiektu. Każdej wersji jest przypisywany unikatowy identyfikator i adres URL. Po utworzeniu obiektu nadana jest unikatowy identyfikator wersji i oznaczona jako bieżąca wersja obiektu. Utworzenie nowego wystąpienia o tej samej nazwie obiektu nadaje nowemu obiektowi unikatowy identyfikator wersji, co powoduje, że staje się on bieżącą wersją.  

Obiekty w magazynie kluczy można rozwiązać przy użyciu bieżącego identyfikatora lub identyfikatora specyficznego dla wersji. Na przykład, biorąc pod `MasterKey`uwagę Klucz o nazwie , wykonywanie operacji z bieżącym identyfikatorem powoduje, że system do korzystania z najnowszej dostępnej wersji. Wykonywanie operacji z identyfikatorem specyficznym dla wersji powoduje, że system używa tej określonej wersji obiektu.  

Obiekty są jednoznacznie identyfikowane w magazynie kluczy przy użyciu adresu URL. Żadne dwa obiekty w systemie nie mają tego samego adresu URL, niezależnie od lokalizacji geograficznej. Pełny adres URL obiektu nosi nazwę identyfikatora obiektu. Adres URL składa się z prefiksu identyfikującego magazyn kluczy, typ obiektu, nazwę obiektu pod warunkiem użytkownika i wersję obiektu. Nazwa obiektu jest niewrażliwa na argumenty i niezmienna. Identyfikatory, które nie zawierają wersji obiektu są określane jako identyfikatory podstawowe.  

Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie, żądania i odpowiedzi](authentication-requests-and-responses.md)

Identyfikator obiektu ma następujący ogólny format:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Gdzie:  

|||  
|-|-|  
|`keyvault-name`|Nazwa magazynu kluczy w usłudze Microsoft Azure Key Vault.<br /><br /> Nazwy magazynu kluczy są wybierane przez użytkownika i są unikatowe globalnie.<br /><br /> Nazwa magazynu kluczy musi być ciągiem znaków 3-24, zawierającym tylko 0-9, a-z, A-Z i -.|  
|`object-type`|Typ obiektu, "klucze" lub "wpisy tajne".|  
|`object-name`|Nazwa `object-name` użytkownika jest nazwą podana przez użytkownika i musi być unikatowa w magazynie kluczy. Nazwa musi być ciągiem znaków 1-127, zawierającym tylko 0-9, a-z, A-Z i -.|  
|`object-version`|An `object-version` jest generowany przez system, 32-znakowy identyfikator ciągu, który jest opcjonalnie używany do adresu unikatową wersję obiektu.|  

## <a name="key-vault-keys"></a>Klucze przechowalni kluczy

### <a name="keys-and-key-types"></a>Klawisze i typy klawiszy

Klucze kryptograficzne w magazynie kluczy są reprezentowane jako obiekty JSON Web Key [JWK]. Podstawowe specyfikacje JWK/JWA są również rozszerzone, aby umożliwić typy kluczy unikatowe dla implementacji usługi Key Vault. Na przykład importowanie kluczy przy użyciu opakowania specyficznego dla dostawcy modułu HSM umożliwia bezpieczny transport kluczy, które mogą być używane tylko w modułach HSM usługi Key Vault.  

- **"Miękkie" klucze:** Klucz przetwarzany w oprogramowaniu przez Key Vault, ale jest szyfrowany w spoczynku przy użyciu klucza systemowego, który znajduje się w modułie HSM. Klienci mogą zaimportować istniejący klucz RSA lub EC (Krzywa eliptyczna) lub zażądać wygenerowania klucza Usługi Key Vault.
- **"Twarde" klucze:** Klucz przetwarzany w module HSM (Hardware Security Module). Te klucze są chronione w jednym z rekordów zabezpieczeń usługi Key Vault HSM Security Worlds (istnieje jeden świat zabezpieczeń na geografię w celu zachowania izolacji). Klienci mogą importować klucz RSA lub EC w formie miękkiej lub eksportując z kompatybilnego urządzenia HSM. Klienci mogą również zażądać magazynu kluczy do wygenerowania klucza. Ten typ klucza dodaje atrybut key_hsm do JWK uzyskać do przenoszenia materiału klucza HSM.

     Aby uzyskać więcej informacji na temat granic geograficznych, zobacz [Centrum zaufania platformy Microsoft Azure](https://azure.microsoft.com/support/trust-center/privacy/)  

Usługa Key Vault obsługuje tylko klawisze RSA i Elliptic Curve. 

-   **EC**: "Miękki" klawisz krzywej eliptycznej.
-   **EC-HSM**: "Twardy" klawisz krzywej eliptycznej.
-   **RSA**: "Miękki" klawisz RSA.
-   **RSA-HSM**: "Twardy" klucz RSA.

Key Vault obsługuje klucze RSA o rozmiarach 2048, 3072 i 4096. Key Vault obsługuje typy kluczy krzywej eliptycznej P-256, P-384, P-521 i P-256K (SECP256K1).

### <a name="cryptographic-protection"></a>Ochrona kryptograficzna

Moduły kryptograficzne używane przez program Key Vault, niezależnie od tego, czy są modułami HSM, czy oprogramowaniem, są sprawdzane fips (Federal Information Processing Standards). Nie musisz robić nic specjalnego, aby uruchomić w trybie FIPS. Klucze **utworzone** lub **zaimportowane** jako chronione przez moduł HSM są przetwarzane wewnątrz modułu HSM, sprawdzane zgodnie z fips 140-2 Poziom 2. Klucze **utworzone** lub **zaimportowane** jako chronione programem są przetwarzane wewnątrz modułów kryptograficznych zweryfikowanych zgodnie z fips 140-2 Poziom 1. Aby uzyskać więcej informacji, zobacz [Klucze i typy kluczy](#keys-and-key-types).

###  <a name="ec-algorithms"></a>Algorytmy WE
 Następujące identyfikatory algorytmów są obsługiwane za pomocą kluczy EC i EC-HSM w magazynie kluczy. 

#### <a name="curve-types"></a>Typy krzywych

-   **P-256** - Krzywa NIST P-256, zdefiniowana w [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-256K** - Krzywa SEC SEC SECP256K1, zdefiniowana w [SEC 2: Zalecane parametry domeny krzywej eliptycznej](https://www.secg.org/sec2-v2.pdf).
-   **P-384** - Krzywa NIST P-384, zdefiniowana w [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-521** - Krzywa NIST P-521, zdefiniowana w [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).

#### <a name="signverify"></a>PODPISZ/SPRAWDŹ

-   **ES256** - ECDSA dla skrótów SHA-256 i kluczy utworzonych za pomocą krzywej P-256. Algorytm ten jest opisany w [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES256K** - ECDSA dla skrótów SHA-256 i kluczy utworzonych za pomocą krzywej P-256K. Ten algorytm oczekuje na standaryzację.
-   **ES384** - ECDSA dla skrótów SHA-384 i kluczy utworzonych za pomocą krzywej P-384. Algorytm ten jest opisany w [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES512** - ECDSA dla skrótów SHA-512 i kluczy utworzonych za pomocą krzywej P-521. Algorytm ten jest opisany w [RFC7518](https://tools.ietf.org/html/rfc7518).


###  <a name="rsa-algorithms"></a>Algorytmy RSA  
 Następujące identyfikatory algorytmów są obsługiwane za pomocą kluczy RSA i RSA-HSM w ucho.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, SZYFRUJ/ODSZYFROWYWANIE

-   **RSA1_5** - szyfrowanie kluczy RSAES-PKCS1-V1_5 [RFC3447]  
-   **RSA-OAEP** - RSAES przy użyciu optymalnego szyfrowania asymetrycznego padding (OAEP) [RFC3447], z domyślnymi parametrami określonymi przez RFC 3447 w sekcji A.2.1. Te parametry domyślne są przy użyciu funkcji mieszania SHA-1 i funkcji generowania maski MGF1 z SHA-1.  

#### <a name="signverify"></a>PODPISZ/SPRAWDŹ

-   **PS256** - RSASSA-PSS przy użyciu SHA-256 i MGF1 z SHA-256, jak opisano w [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS384** - RSASSA-PSS przy użyciu SHA-384 i MGF1 z SHA-384, jak opisano w [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS512** - RSASSA-PSS przy użyciu SHA-512 i MGF1 z SHA-512, jak opisano w [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **RS256** - RSASSA-PKCS-v1_5 za pomocą SHA-256. Wartość skrótu dostarczona przez aplikację musi być obliczona przy użyciu sha-256 i musi mieć długość 32 bajtów.  
-   **RS384** - RSASSA-PKCS-v1_5 za pomocą SHA-384. Wartość skrótu dostarczona przez aplikację musi być obliczona przy użyciu sha-384 i musi mieć długość 48 bajtów.  
-   **RS512** - RSASSA-PKCS-v1_5 za pomocą SHA-512. Wartość skrótu dostarczona przez aplikację musi być obliczona przy użyciu sha-512 i musi mieć długość 64 bajtów.  
-   **RSNULL** — zobacz [RFC2437], przypadek użycia specjalistyczne, aby włączyć niektóre scenariusze TLS.  

###  <a name="key-operations"></a>Kluczowe operacje

Usługa Key Vault obsługuje następujące operacje na kluczowych obiektach:  

-   **Utwórz**: Umożliwia klientowi utworzenie klucza w przechowalni kluczy. Wartość klucza jest generowany przez magazyn klucza i przechowywane i nie jest zwalniany do klienta. Klucze asymetryczne mogą być tworzone w przechowalni kluczy.  
-   **Import:** Umożliwia klientowi zaimportować istniejący klucz do usługi Key Vault. Klucze asymetryczne mogą być importowane do usługi Key Vault przy użyciu wielu różnych metod pakowania w konstrukcji JWK. 
-   **Aktualizacja**: Umożliwia klientowi z wystarczającymi uprawnieniami modyfikowanie metadanych (atrybutów klucza) skojarzonych z kluczem wcześniej przechowywanym w magazynie kluczy.  
-   **Usuń**: Umożliwia klientowi z wystarczającymi uprawnieniami usunięcie klucza z usługi Key Vault.  
-   **Lista**: Umożliwia klientowi wyświetlanie listy wszystkich kluczy w danym magazynie kluczy.  
-   **Wersje listy:** Umożliwia klientowi wyświetlanie listy wszystkich wersji danego klucza w danym magazynie kluczy.  
-   **Pobierz:** Umożliwia klientowi pobieranie publicznych części danego klucza w magazynie kluczy.  
-   **Kopia zapasowa**: Eksportuje klucz w postaci chronionej.  
-   **Przywracanie:** Importuje wcześniej utworzony klucz.  

Aby uzyskać więcej informacji, zobacz [Operacje klucza w odwołaniu interfejsu API REST magazynu kluczy](/rest/api/keyvault).  

Po utworzeniu klucza w magazynie kluczy można wykonać następujące operacje kryptograficzne za pomocą klucza:  

-   **Podpisz i sprawdź:** Ściśle, ta operacja jest "sign hash" lub "verify hash", jak Usługa Key Vault nie obsługuje mieszania zawartości w ramach tworzenia podpisu. Aplikacje powinny mieszać dane, które mają być podpisane lokalnie, a następnie zażądać, aby usługa Key Vault podpisała skrót. Weryfikacja podpisanych skrótów jest obsługiwana jako wygodna operacja dla aplikacji, które mogą nie mieć dostępu do [publicznych] materiałów kluczowych. Aby uzyskać najlepszą wydajność aplikacji, sprawdź, czy operacje są wykonywane lokalnie.  
-   **Szyfrowanie kluczy / zawijanie:** Klucz przechowywany w magazynie kluczy może być używany do ochrony innego klucza, zazwyczaj symetrycznego klucza szyfrowania zawartości (CEK). Gdy klucz w przechowalni kluczy jest asymetryczny, używane jest szyfrowanie kluczy. Na przykład RSA-OAEP i WRAPKEY/UNWRAPKEY operacje są równoważne szyfrować/odszyfrować. Gdy klucz w przechowalni kluczy jest symetryczny, używane jest zawijanie kluczy. Na przykład AES-KW. Operacja WRAPKEY jest obsługiwana jako udogodnienie dla aplikacji, które mogą nie mieć dostępu do [public] materiału klucza. Aby uzyskać najlepszą wydajność aplikacji, operacje WRAPKEY powinny być wykonywane lokalnie.  
-   **Szyfruj i odszyfrowywanie:** Klucz przechowywany w magazynie kluczy może być używany do szyfrowania lub odszyfrowywania pojedynczego bloku danych. Rozmiar bloku jest określany przez typ klucza i wybrany algorytm szyfrowania. Operacja Szyfrowanie jest dostępna dla wygody dla aplikacji, które mogą nie mieć dostępu do [publicznych] materiałów kluczowych. Aby uzyskać najlepszą wydajność aplikacji, operacje szyfrowania powinny być wykonywane lokalnie.  

Podczas WRAPKEY/UNWRAPKEY przy użyciu kluczy asymetrycznych może wydawać się zbędne (ponieważ operacja jest odpowiednikiem szyfrowania/odszyfrowywania), użycie różnych operacji jest ważne. Rozróżnienie zapewnia separacji semantyczne i autoryzacji tych operacji i spójności, gdy inne typy kluczy są obsługiwane przez usługę.  

Usługa Key Vault nie obsługuje operacji EXPORT. Po udostępnieniu klucza w systemie nie można go wyodrębnić ani zmodyfikować jego kluczowego materiału. Jednak użytkownicy usługi Key Vault mogą wymagać klucza w innych przypadkach użycia, na przykład po jego usunięciu. W takim przypadku mogą użyć operacji KOPIA ZAPASOWA i PRZYWRACANIE do eksportowania/importowania klucza w postaci chronionej. Klucze utworzone przez operację KOPIA ZAPASOWA nie mogą być użyteczne poza magazynem kluczy. Alternatywnie operacja IMPORT może być używana dla wielu wystąpień usługi Key Vault.  

Użytkownicy mogą ograniczyć dowolną operacje kryptograficzne, które usługa Key Vault obsługuje na podstawie klucza, przy użyciu właściwości key_ops obiektu JWK.  

Aby uzyskać więcej informacji na temat obiektów JWK, zobacz [JSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).  

###  <a name="key-attributes"></a>Kluczowe atrybuty

Oprócz materiału kluczowego można określić następujące atrybuty. W żądaniu JSON słowo kluczowe atrybuty i nawiasy klamrowe ,{' '}', są wymagane, nawet jeśli nie określono żadnych atrybutów.  

- *włączone*: logiczne, opcjonalne, domyślnie jest **prawdziwe**. Określa, czy klucz jest włączony i użyteczny dla operacji kryptograficznych. Włączony *enabled* atrybut jest używany w połączeniu z *nbf* i *exp*. Gdy operacja występuje między *nbf* i *exp*, będzie dozwolone tylko *wtedy, gdy włączona* jest ustawiona na **true**. Operacje poza oknem *nbf* / *exp* są automatycznie niedozwolone, z wyjątkiem niektórych typów operacji w [określonych warunkach.](#date-time-controlled-operations)
- *nbf*: IntDate, opcjonalnie, domyślnie jest teraz. Atrybut *nbf* (nie wcześniej) określa czas, przed którym klucz NIE MOŻE być używany do operacji kryptograficznych, z wyjątkiem niektórych typów operacji w [określonych warunkach](#date-time-controlled-operations). Przetwarzanie atrybutu *nbf* wymaga, aby bieżąca data/godzina MUSI być po lub równa daty/godzinie nieprzedna wymienionej w atrybucie *nbf.* Key Vault może zapewnić pewną małą swobodę, zwykle nie więcej niż kilka minut, aby uwzględnić pochylenie zegara. Jego wartość MUSI być liczbą zawierającą wartość IntDate.  
- *exp:* IntDate, opcjonalnie, domyślnie jest "na zawsze". Atrybut *exp* (czas wygaśnięcia) określa czas wygaśnięcia, po którym klucz NIE MOŻE być używany do operacji kryptograficznej, z wyjątkiem niektórych typów operacji w [określonych warunkach.](#date-time-controlled-operations) Przetwarzanie atrybutu *exp* wymaga, aby bieżąca data/godzina musi być przed datą/godziną wygaśnięcia wymienioną w atrybucie *exp.* Key Vault może zapewnić pewne małe pole manewru, zazwyczaj nie więcej niż kilka minut, aby uwzględnić pochylenie zegara. Jego wartość MUSI być liczbą zawierającą wartość IntDate.  

Istnieją dodatkowe atrybuty tylko do odczytu, które są zawarte w każdej odpowiedzi, która zawiera kluczowe atrybuty:  

- *utworzony*: IntDate, opcjonalnie. Utworzony *created* atrybut wskazuje, kiedy ta wersja klucza została utworzona. Wartość jest null dla kluczy utworzonych przed dodaniem tego atrybutu. Jego wartość MUSI być liczbą zawierającą wartość IntDate.  
- *aktualizacja*: IntDate, opcjonalnie. *Zaktualizowany* atrybut wskazuje, kiedy ta wersja klucza została zaktualizowana. Wartość jest null dla kluczy, które zostały ostatnio zaktualizowane przed dodaniem tego atrybutu. Jego wartość MUSI być liczbą zawierającą wartość IntDate.  

Aby uzyskać więcej informacji na temat IntDate i innych typów danych, zobacz [Typy danych](#data-types)  

#### <a name="date-time-controlled-operations"></a>Operacje kontrolowane przez datę i godzinę

Nieuprawnione i wygasłe klucze, poza oknem *nbf* / *exp,* będą działać w celu **odszyfrowania,** **rozpakuj**i **weryfikują** operacje (nie zwróci 403, Zabronione). Uzasadnieniem użycia stanu nieuwzwoleniu jest zezwolenie na testowanie klucza przed użyciem w produkcji. Uzasadnieniem użycia stanu wygasłe jest umożliwienie operacji odzyskiwania na danych, który został utworzony, gdy klucz był prawidłowy. Ponadto można wyłączyć dostęp do klucza przy użyciu zasad usługi Key Vault lub zaktualizować *atrybut klucza włączonego* do **false**.

Aby uzyskać więcej informacji na temat typów danych, zobacz [Typy danych](#data-types).

Aby uzyskać więcej informacji na temat innych możliwych atrybutów, zobacz [JSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).

### <a name="key-tags"></a>Znaczniki kluczy

Można określić dodatkowe metadane specyficzne dla aplikacji w postaci tagów. Usługa Key Vault obsługuje maksymalnie 15 znaczników, z których każdy może mieć 256 znaków i wartość 256 znaków.  

>[!Note]
>Znaczniki są czytelne przez obiekt wywołujący, jeśli mają *listę* lub *uzyskać* uprawnienia do tego typu obiektu (klucze, wpisy tajne lub certyfikaty).

###  <a name="key-access-control"></a>Kontrola dostępu do kluczy

Kontrola dostępu dla kluczy zarządzanych przez magazyn kluczy jest dostępna na poziomie magazynu kluczy, który działa jako kontener kluczy. Zasady kontroli dostępu dla kluczy różni się od zasad kontroli dostępu dla wpisów tajnych w tym samym Magazynie kluczy. Użytkownicy mogą utworzyć jeden lub więcej magazynów do przechowywania kluczy i są wymagane do obsługi scenariusza odpowiedniej segmentacji i zarządzania kluczami. Kontrola dostępu do kluczy jest niezależna od kontroli dostępu do wpisów tajnych.  

Następujące uprawnienia mogą być przyznane, na podstawie jednostki użytkownika / usługi, we wpisie kontroli dostępu kluczy w przechowalni. Te uprawnienia ściśle dubluje operacje dozwolone na kluczowym obiekcie.  Udzielanie dostępu do jednostki usługi w magazynie kluczy jest operacją jednorazową i pozostanie taka sama dla wszystkich subskrypcji platformy Azure. Można go użyć do wdrożenia dowolną liczbę certyfikatów. 

- Uprawnienia do operacji zarządzania kluczami
  - *get*: Przeczytaj publiczną część klucza, a także jego atrybuty
  - *lista*: Lista kluczy lub wersji klucza przechowywanego w magazynie kluczy
  - *update*: Aktualizowanie atrybutów klucza
  - *tworzenie*: Tworzenie nowych kluczy
  - *import*: Importowanie klucza do magazynu kluczy
  - *delete*: Usuwanie obiektu klucza
  - *odzyskiwanie*: Odzyskiwanie usuniętego klucza
  - *kopia zapasowa*: Tworzenie kopii zapasowej klucza w magazynie kluczy
  - *przywracanie*: Przywracanie kopii zapasowej klucza do magazynu kluczy

- Uprawnienia do operacji kryptograficznych
  - *odszyfrowywanie*: Użyj klucza, aby odchować sekwencję bajtów
  - *szyfrowanie*: Użyj klucza, aby chronić dowolną sekwencję bajtów
  - *unwrapKey*: Użyj klucza, aby odchronić zawinięte klucze symetryczne
  - *wrapKey*: Użyj klucza, aby chronić klucz symetryczny
  - *verify*: Użyj klucza do weryfikacji skrótów  
  - *znak*: Użyj klawisza, aby podpisać skróty
    
- Uprawnienia dla operacji uprzywilejowanych
  - *przeczyszczanie*: Przeczyszcz (trwale usuń) usunięty klucz

Aby uzyskać więcej informacji na temat pracy z kluczami, zobacz [Operacje klucza w odwołaniu do interfejsu API REST magazynu kluczy](/rest/api/keyvault). Aby uzyskać informacje dotyczące ustanawiania uprawnień, zobacz [Vaults - Tworzenie lub aktualizowanie](/rest/api/keyvault/vaults/createorupdate) i [przechowalnia — Aktualizowanie zasad dostępu](/rest/api/keyvault/vaults/updateaccesspolicy). 

## <a name="key-vault-secrets"></a>Wpisy tajne magazynu kluczy 

### <a name="working-with-secrets"></a>Praca z sekretami

Z punktu widzenia dewelopera interfejsy API usługi Key Vault akceptują i zwracają tajne wartości jako ciągi. Wewnętrznie Usługa Key Vault przechowuje i zarządza wpisami tajnymi jako sekwencje oktetów (8-bitowych bajtów), o maksymalnym rozmiarze 25 k bajtów. Usługa Usługi Usługi Przechowalnia kluczy nie zapewnia semantyki wpisów tajnych. Po prostu akceptuje dane, szyfruje je, przechowuje i zwraca tajny identyfikator ("id"). Identyfikator może służyć do pobierania klucza tajnego w późniejszym czasie.  

W przypadku wysoce poufnych danych klienci powinni rozważyć zastosowanie dodatkowych warstw ochrony danych. Jednym z przykładów jest szyfrowanie danych przy użyciu oddzielnego klucza ochrony w celu przechowywania ich w usłudze Key Vault.  

Usługa Key Vault obsługuje również pole contentType dla wpisów tajnych. Klienci mogą określić typ zawartości klucza tajnego, aby pomóc w interpretacji tajnych danych podczas ich pobierania. Maksymalna długość tego pola wynosi 255 znaków. Nie ma wstępnie zdefiniowanych wartości. Sugerowane użycie jest wskazówką do interpretacji tajnych danych. Na przykład implementacja może przechowywać hasła i certyfikaty jako wpisy tajne, a następnie użyć tego pola do rozróżnienia. Nie ma wstępnie zdefiniowanych wartości.  

### <a name="secret-attributes"></a>Atrybuty tajne

Oprócz tajnych danych można określić następujące atrybuty:  

- *exp*: IntDate, opcjonalnie, domyślnie jest **zawsze**. Atrybut *exp* (czas wygaśnięcia) identyfikuje czas wygaśnięcia, po którym tajne dane nie powinny być pobierane, z wyjątkiem [szczególnych sytuacji](#date-time-controlled-operations). To pole służy do celów **informacyjnych** tylko wtedy, gdy informuje użytkowników usługi magazynu kluczy, że określony klucz tajny nie może być używany. Jego wartość MUSI być liczbą zawierającą wartość IntDate.   
- *nbf*: IntDate, opcjonalnie, domyślnie jest **teraz**. Atrybut *nbf* (nie wcześniej) określa czas, przed którym tajne dane NIE powinny być pobierane, z wyjątkiem [szczególnych sytuacji](#date-time-controlled-operations). To pole służy wyłącznie celom **informacyjnym.** Jego wartość MUSI być liczbą zawierającą wartość IntDate. 
- *włączone*: logiczne, opcjonalne, domyślnie jest **prawdziwe**. Ten atrybut określa, czy tajne dane mogą być pobierane. Włączony atrybut jest używany w połączeniu z *nbf* i *exp,* gdy operacja występuje między *nbf* i *exp*, będzie dozwolone tylko wtedy, gdy włączona jest ustawiona na **true**. Operacje poza oknem *nbf* i *exp* są automatycznie niedozwolone, z wyjątkiem [szczególnych sytuacji.](#date-time-controlled-operations)  

Istnieją dodatkowe atrybuty tylko do odczytu, które są zawarte w każdej odpowiedzi, która zawiera atrybuty tajne:  

- *utworzony*: IntDate, opcjonalnie. Utworzony atrybut wskazuje, kiedy ta wersja klucza tajnego została utworzona. Ta wartość jest null dla wpisów tajnych utworzonych przed dodaniem tego atrybutu. Jego wartość musi być liczbą zawierającą wartość IntDate.  
- *aktualizacja*: IntDate, opcjonalnie. Zaktualizowany atrybut wskazuje, kiedy ta wersja klucza tajnego została zaktualizowana. Ta wartość jest null dla wpisów tajnych, które zostały ostatnio zaktualizowane przed dodaniem tego atrybutu. Jego wartość musi być liczbą zawierającą wartość IntDate.

#### <a name="date-time-controlled-operations"></a>Operacje kontrolowane przez datę i godzinę

Operacja **get** secret będzie działać dla nie-jeszcze ważne i wygasłe wpisy tajne, poza *nbf* / *exp* okna. Wywołanie operacji **get** klucza tajnego, dla nieustałego jeszcze tajnego, może służyć do celów testowych. Pobieranie **(get**ting) wygasły klucz tajny, może służyć do odzyskiwania operacji.

Aby uzyskać więcej informacji na temat typów danych, zobacz [Typy danych](#data-types).  

### <a name="secret-access-control"></a>Kontrola dostępu do wpisów tajnych

Kontrola dostępu dla wpisów tajnych zarządzanych w magazynie kluczy jest dostarczana na poziomie magazynu kluczy, który zawiera te wpisy tajne. Zasady kontroli dostępu dla wpisów tajnych, różni się od zasad kontroli dostępu dla kluczy w tym samym Magazynie kluczy. Użytkownicy mogą utworzyć jeden lub więcej magazynów do przechowywania wpisów tajnych i są wymagane do utrzymania scenariusza odpowiedniej segmentacji i zarządzania wpisami tajnymi.   

Następujące uprawnienia mogą być używane, na zasadzie jednostkowej, we wpisie kontroli dostępu do wpisu nieudzielonych wpisów w przechowalni i ściśle dublować operacje dozwolone w obiekcie tajnym:  

- Uprawnienia do tajnych operacji zarządzania
  - *get*: Przeczytaj tajemnicę  
  - *lista*: Lista wpisów tajnych lub wersji klucza tajnego przechowywanego w magazynie kluczy  
  - *zestaw*: Tworzenie klucza tajnego  
  - *delete*: Usuwanie klucza tajnego  
  - *recover*: Odzyskiwanie usuniętego klucza tajnego
  - *kopia zapasowa*: Tworzenie kopii zapasowej klucza tajnego w magazynie kluczy
  - *przywracanie*: Przywracanie kopii zapasowej klucza tajnego do magazynu kluczy

- Uprawnienia dla operacji uprzywilejowanych
  - *przeczyścić*: Przeczyścić (trwale usunąć) usunięty klucz tajny

Aby uzyskać więcej informacji na temat pracy z wpisami tajnymi, zobacz [Operacje tajne w odwołaniu do interfejsu API REST magazynu kluczy](/rest/api/keyvault). Aby uzyskać informacje dotyczące ustanawiania uprawnień, zobacz [Vaults - Tworzenie lub aktualizowanie](/rest/api/keyvault/vaults/createorupdate) i [przechowalnia — Aktualizowanie zasad dostępu](/rest/api/keyvault/vaults/updateaccesspolicy). 

### <a name="secret-tags"></a>Tajne tagi  
Można określić dodatkowe metadane specyficzne dla aplikacji w postaci tagów. Usługa Key Vault obsługuje maksymalnie 15 znaczników, z których każdy może mieć 256 znaków i wartość 256 znaków.  

>[!Note]
>Znaczniki są czytelne przez obiekt wywołujący, jeśli mają *listę* lub *uzyskać* uprawnienia do tego typu obiektu (klucze, wpisy tajne lub certyfikaty).

## <a name="key-vault-certificates"></a>Certyfikaty magazynu kluczy

Obsługa certyfikatów usługi Key Vault zapewnia zarządzanie certyfikatami x509 i następującymi zachowaniami:  

-   Umożliwia właścicielowi certyfikatu utworzenie certyfikatu za pośrednictwem procesu tworzenia magazynu kluczy lub za pomocą importu istniejącego certyfikatu. Obejmuje certyfikaty z podpisem własnym i certyfikatami wygenerowanymi przez urząd certyfikacji.
-   Umożliwia właścicielowi certyfikatu usługi Key Vault implementowanie bezpiecznego przechowywania i zarządzania certyfikatami X509 bez interakcji z materiałem klucza prywatnego.  
-   Umożliwia właścicielowi certyfikatu utworzenie zasad, które nakazuje magazynowi kluczy zarządzanie cyklem życia certyfikatu.  
-   Umożliwia właścicielom certyfikatów dostarczanie informacji kontaktowych w celu powiadamiania o zdarzeniach cyklu życia wygaśnięcia i odnawiania certyfikatu.  
-   Obsługuje automatyczne odnawianie z wybranymi wystawcami — dostawcami certyfikatów /urzędami certyfikacji programu Key Vault partner X509.

>[!Note]
>Dostawcy/władze niebędące partnerami również są dozwolone, ale nie obsługują funkcji automatycznego odnawiania.

### <a name="composition-of-a-certificate"></a>Skład certyfikatu

Podczas tworzenia certyfikatu usługi Key Vault tworzony jest również klucz adresowa i klucz tajny o tej samej nazwie. Klucz przechowalni kluczy umożliwia operacje klucza, a klucz tajny magazynu kluczy umożliwia pobieranie wartości certyfikatu jako klucza tajnego. Certyfikat Usługi Key Vault zawiera również publiczne metadane certyfikatu x509.  

Identyfikator i wersja certyfikatów jest podobny do kluczy i wpisów tajnych. W odpowiedzi na certyfikat usługi Key Vault dostępna jest określona wersja klucza adresowalnej i klucza tajnego utworzonego za pomocą wersji certyfikatu usługi Key Vault.
 
![Certyfikaty są obiektami złożonymi](media/azure-key-vault.png)

### <a name="exportable-or-non-exportable-key"></a>Klucz do eksportu lub nieeksportowania

Po utworzeniu certyfikatu Usługi Key Vault można go pobrać z adresowalnej tajemnicy tajnej za pomocą klucza prywatnego w formacie PFX lub PEM. Zasady używane do tworzenia certyfikatu muszą wskazywać, że klucz można wyeksportować. Jeśli zasada wskazuje, że nie można eksportować, klucz prywatny nie jest częścią wartości po pobraniu jako klucz tajny.  

Klucz adresowalny staje się bardziej odpowiedni w przypadku certyfikatów KV nieeksportowalnych. Operacje adresowalne klucza KV są mapowane z pola *keyusage* zasad certyfikatu KV używanych do tworzenia certyfikatu KV.  

Obsługiwane są dwa typy kluczy — *RSA* lub *RSA HSM* z certyfikatami. Eksportowanie jest dozwolone tylko przy pomocy rsa, nie jest obsługiwane przez moduł HSM RSA.  

### <a name="certificate-attributes-and-tags"></a>Atrybuty certyfikatów i znaczniki

Oprócz metadanych certyfikatów, adresowalny klucz i adresowalny klucz tajny, certyfikat Usługi Key Vault zawiera również atrybuty i tagi.  

#### <a name="attributes"></a>Atrybuty

Atrybuty certyfikatu są dublowane do atrybutów klucza adresowalnej i klucza tajnego utworzonego podczas tworzenia certyfikatu KV.  

Certyfikat Usługi Key Vault ma następujące atrybuty:  

-   *włączone*: logiczne, opcjonalne, domyślnie jest **prawdziwe**. Można określić, aby wskazać, czy dane certyfikatu mogą być pobierane jako tajne lub obsługiwane jako klucz. Używany również w połączeniu z *nbf* i *exp,* gdy operacja występuje między *nbf* i *exp*, i będzie dozwolone tylko wtedy, gdy włączona jest ustawiona na true. Operacje poza oknem *nbf* i *exp* są automatycznie niedozwolone.  

Istnieją dodatkowe atrybuty tylko do odczytu, które są zawarte w odpowiedzi:

-   *utworzony*: IntDate: wskazuje, kiedy ta wersja certyfikatu została utworzona.  
-   *aktualizacja*: IntDate: wskazuje, kiedy ta wersja certyfikatu została zaktualizowana.  
-   *exp*: IntDate: zawiera wartość daty wygaśnięcia certyfikatu x509.  
-   *nbf*: IntDate: zawiera wartość daty certyfikatu x509.  

> [!Note] 
> Jeśli certyfikat Usługi Key Vault wygaśnie, adresowalny klucz i klucz tajny stają się niesprawne.  

#### <a name="tags"></a>Tagi

 Klient określony słownik par wartości klucza, podobne do tagów w klucze i wpisy tajne.  

 > [!Note]
> Znaczniki są czytelne przez obiekt wywołujący, jeśli mają *listę* lub *uzyskać* uprawnienia do tego typu obiektu (klucze, wpisy tajne lub certyfikaty).

### <a name="certificate-policy"></a>Zasady dotyczące certyfikatów

Zasady certyfikatów zawierają informacje dotyczące tworzenia i zarządzania cyklem życia certyfikatu usługi Key Vault. Gdy certyfikat z kluczem prywatnym jest importowany do magazynu kluczy, domyślna zasada jest tworzona przez odczyt certyfikatu x509.  

Gdy certyfikat usługi Key Vault jest tworzony od podstaw, należy podać zasadę. Zasady określają sposób tworzenia tej wersji certyfikatu usługi Key Vault lub następnej wersji certyfikatu Usługi Key Vault. Po ustanowieniu zasad nie jest wymagane z kolejnych operacji tworzenia dla przyszłych wersji. Istnieje tylko jedno wystąpienie zasad dla wszystkich wersji certyfikatu usługi Key Vault.  

Na wysokim poziomie zasady certyfikatów zawierają następujące informacje:  

-   Właściwości certyfikatu X509: zawiera nazwę podmiotu, alternatywne nazwy podmiotu i inne właściwości używane do tworzenia żądania certyfikatu x509.  
-   Właściwości klucza: zawiera pola klucza typu, długości klucza, eksportowania i ponownego użycia. Te pola instruują przechowalnię kluczy dotyczące sposobu generowania klucza.  
-   Właściwości klucza tajnego: zawiera tajne właściwości, takie jak typ zawartości adresowalny klucz tajny do generowania wartości tajnej, do pobierania certyfikatu jako klucz tajny.  
-   Akcje okresu istnienia: zawiera akcje okresu istnienia certyfikatu KV. Każda czynność dożywotnia zawiera:  

     - Wyzwalacz: określony przez dni przed wygaśnięciem lub procent zakresu okresu istnienia  

     - Akcja: określanie typu akcji — *emailContacts* lub *autoRenew*  

-   Wystawca: Parametry dotyczące wystawcy certyfikatu używanego do wystawiania certyfikatów x509.  
-   Atrybuty zasad: zawiera atrybuty skojarzone z zasadami  

#### <a name="x509-to-key-vault-usage-mapping"></a>Mapowanie użycia od X509 do usługi Key Vault

Poniższa tabela przedstawia mapowanie zasad użycia klucza x509 do skutecznych operacji klucza klucza utworzonego w ramach tworzenia certyfikatu usługi Key Vault.

|**Flagi użycia kluczy X509**|**Operacje kluczy przechowalni kluczy**|**Zachowanie domyślne**|
|----------|--------|--------|
|DataSeksfera|szyfruj, odszyfrowywanie| Nie dotyczy |
|DecipherOnly|Odszyfrować| Nie dotyczy  |
|Podpis cyfrowy|podpisz, sprawdź| Domyślna usługa Key Vault bez specyfikacji użycia w czasie tworzenia certyfikatu | 
|EncipherOnly|encrypt| Nie dotyczy |
|Znak KeyCertSign|podpisz, sprawdź|Nie dotyczy|
|KeyEncipherment (Szyfrowanie klawiszowe)|wrapKey, odrapać Klucz| Domyślna usługa Key Vault bez specyfikacji użycia w czasie tworzenia certyfikatu | 
|Brak zmian|podpisz, sprawdź| Nie dotyczy |
|znak crlsign|podpisz, sprawdź| Nie dotyczy |

### <a name="certificate-issuer"></a>Wystawca certyfikatu

Obiekt certyfikatu Usługi Key Vault zawiera konfigurację używaną do komunikowania się z wybranym dostawcą wystawcy certyfikatów w celu zamówienia certyfikatów x509.  

-   Key Vault współpracuje z następującymi dostawcami wystawców certyfikatów dla certyfikatów TLS/SSL

|**Nazwa dostawcy**|**Lokalizacje**|
|----------|--------|
|DigiCert|Obsługiwane we wszystkich lokalizacjach usługi magazynu kluczy w chmurze publicznej i usłudze Azure Dla Instytucji Rządowych|
|GlobalSign|Obsługiwane we wszystkich lokalizacjach usługi magazynu kluczy w chmurze publicznej i usłudze Azure Dla Instytucji Rządowych|

Aby wystawca certyfikatu mógł zostać utworzony w magazynie kluczy, należy pomyślnie wykonać kroki 1 i 2 wymagań wstępnych.  

1. Dołączanie do dostawców urzędu certyfikacji  

    -   Administrator organizacji musi w swojej firmie (np. Contoso) z co najmniej jednym dostawcą urzędu certyfikacji.  

2. Administrator tworzy poświadczenia żądania dla usługi Key Vault w celu rejestrowania (i odnawiania) certyfikatów TLS/SSL  

    -   Udostępnia konfigurację, która ma być używana do utworzenia obiektu wystawcy dostawcy w magazynie kluczy  

Aby uzyskać więcej informacji na temat tworzenia obiektów wystawców z portalu Certyfikaty, zobacz [blog Certyfikaty magazynu kluczy](https://aka.ms/kvcertsblog)  

Usługa Key Vault umożliwia tworzenie wielu obiektów wystawców z inną konfiguracją dostawcy wystawcy. Po utworzeniu obiektu wystawcy można odwoływać się do jego nazwy w jednej lub wielu zasadach certyfikatów. Odwoływanie się do obiektu wystawcy nakazuje magazynowi kluczy funkcji używanie konfiguracji określonej w obiekcie wystawcy podczas żądania certyfikatu x509 od dostawcy urzędu certyfikacji podczas tworzenia i odnawiania certyfikatu.  

Obiekty wystawcy są tworzone w przechowalni i mogą być używane tylko z certyfikatami KV w tym samym magazynie.  

### <a name="certificate-contacts"></a>Kontakty certyfikatów

Kontakty certyfikatów zawierają informacje kontaktowe do wysyłania powiadomień wyzwalanych przez zdarzenia okresu istnienia certyfikatu. Informacje o kontaktach są udostępniane przez wszystkie certyfikaty w magazynie kluczy. Powiadomienie jest wysyłane do wszystkich określonych kontaktów dla zdarzenia dla dowolnego certyfikatu w magazynie kluczy.  

Jeśli zasady certyfikatu są ustawione na automatyczne odnawianie, powiadomienie jest wysyłane w następujących zdarzeniach.  

- Przed odnowieniem certyfikatu
- Po odnowieniu certyfikatu, podając, czy certyfikat został pomyślnie odnowiony lub jeśli wystąpił błąd, wymagających ręcznego odnowienia certyfikatu.  

  Gdy zasady certyfikatów, które mają być odnawiane ręcznie (tylko wiadomości e-mail), powiadomienie jest wysyłane, gdy nadszedł czas, aby odnowić certyfikat.  

### <a name="certificate-access-control"></a>Kontrola dostępu do certyfikatów

 Kontrola dostępu dla certyfikatów jest zarządzana przez magazyn kluczy i jest dostarczana przez Magazyn kluczy, który zawiera te certyfikaty. Zasady kontroli dostępu dla certyfikatów różni się od zasad kontroli dostępu dla kluczy i wpisów tajnych w tym samym Magazynie kluczy. Użytkownicy mogą utworzyć jeden lub więcej magazynów do przechowywania certyfikatów, aby zachować scenariusz odpowiedniej segmentacji i zarządzania certyfikatami.  

 Następujące uprawnienia mogą być używane, na zasadzie jednostkowej, we wpisie kontroli dostępu do wpisu nieudzielonych wpisów w magazynie kluczy i ściśle odzwierciedla operacje dozwolone w obiekcie tajnym:  

- Uprawnienia do operacji zarządzania certyfikatami
  - *pobierz*: Pobierz bieżącą wersję certyfikatu lub dowolną wersję certyfikatu 
  - *lista*: Lista bieżących certyfikatów lub wersji certyfikatu  
  - *update*: Aktualizowanie certyfikatu
  - *tworzenie*: Tworzenie certyfikatu usługi Key Vault
  - *import*: Importowanie materiału certyfikatu do certyfikatu magazynu kluczy
  - *delete*: Usuwanie certyfikatu, jego zasad i wszystkich jego wersji  
  - *odzyskiwanie*: Odzyskiwanie usuniętego certyfikatu
  - *kopia zapasowa*: Tworzenie kopii zapasowej certyfikatu w magazynie kluczy
  - *przywracanie*: Przywracanie kopii zapasowej certyfikatu do magazynu kluczy
  - *zarządzanie kontaktami*: Zarządzanie kontaktami certyfikatów usługi Key Vault  
  - *zarządzanieissuers*: Zarządzanie urzędami certyfikatów usługi Key Vault /wystawców
  - *getissuers*: Pobierz certyfikat organów / emitentów
  - *listissuers*: Lista organów/wystawców certyfikatu  
  - *setissuers*: Tworzenie lub aktualizowanie urzędów/wystawców certyfikatu usługi Key Vault  
  - *deleteissuers*: Usuwanie organów/wystawców certyfikatu magazynu kluczy  
 
- Uprawnienia dla operacji uprzywilejowanych
  - *przeczyszczanie*: Przeczyszcz (trwale usuń) usunięty certyfikat

Aby uzyskać więcej informacji, zobacz [operacje certyfikatów w interfejsie API REST magazynu kluczy](/rest/api/keyvault). Aby uzyskać informacje dotyczące ustanawiania uprawnień, zobacz [Vaults - Tworzenie lub aktualizowanie](/rest/api/keyvault/vaults/createorupdate) i [przechowalnia — Aktualizowanie zasad dostępu](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="azure-storage-account-key-management"></a>Zarządzanie kluczami konta usługi Azure Storage

Usługa Key Vault może zarządzać kluczami kont magazynu platformy Azure:

- Wewnętrznie usługa Key Vault może wymieniać (synchronizować) klucze z kontem magazynu platformy Azure. 
- Magazyn kluczy okresowo regeneruje (obraca) klawisze.
- Wartości klucza nigdy nie są zwracane w odpowiedzi na wywołującego.
- Usługa Key Vault zarządza kluczami zarówno kont magazynu, jak i klasycznych kont magazynu.

Aby uzyskać więcej informacji, zobacz [Klucze kont usługi Azure Key Storage](key-vault-ovw-storage-keys.md)

### <a name="storage-account-access-control"></a>Kontrola dostępu do konta magazynu

Podczas autoryzowania jednostki zabezpieczeń użytkownika lub podmiotu zabezpieczeń aplikacji można używać następujących uprawnień:  

- Uprawnienia dla zarządzanych kont magazynu i operacji definicji SaS
  - *get*: Pobiera informacje o koncie magazynu 
  - *lista*: Lista kont magazynu zarządzanych przez Magazyn kluczy
  - *aktualizacja*: Aktualizowanie konta magazynu
  - *delete*: Usuwanie konta magazynu  
  - *odzyskiwanie*: Odzyskiwanie usuniętego konta magazynu
  - *kopia zapasowa*: Tworzenie kopii zapasowej konta magazynu
  - *przywracanie*: Przywracanie konta magazynu kopii zapasowej do magazynu kluczy
  - *zestaw*: Tworzenie lub aktualizowanie konta magazynu
  - *regeneratekey*: Ponowne generowanie określonej wartości klucza dla konta magazynu
  - *getsas*: Uzyskaj informacje o definicji sygnatury dostępu Współdzielonego dla konta magazynu
  - *listsas*: Lista definicji sygnatury dostępu Współdzielonego magazynu dla konta magazynu
  - *deletesas*: Usuwanie definicji sygnatury dostępu Współdzielonego z konta magazynu
  - *setsas*: Tworzenie lub aktualizowanie nowej definicji/atrybutów sygnatury dostępu Współdzielonego dla konta magazynu

- Uprawnienia dla operacji uprzywilejowanych
  - *przeczyszczanie*: Przeczyszcz (trwale usuń) zarządzane konto magazynu

Aby uzyskać więcej informacji, zobacz [operacje konta magazynu w interfejsie API REST magazynu kluczy](/rest/api/keyvault). Aby uzyskać informacje dotyczące ustanawiania uprawnień, zobacz [Vaults - Tworzenie lub aktualizowanie](/rest/api/keyvault/vaults/createorupdate) i [przechowalnia — Aktualizowanie zasad dostępu](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="see-also"></a>Zobacz też

- [Uwierzytelnianie, żądania i odpowiedzi](authentication-requests-and-responses.md)
- [Przewodnik dewelopera usługi Key Vault](/azure/key-vault/key-vault-developers-guide)
