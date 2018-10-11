---
title: Temat usługi Azure Key Vault kluczy, wpisów tajnych i certyfikatów
description: Omówienie usługi Azure Key Vault REST interfejs i deweloperów szczegółowe informacje o kluczy, wpisów tajnych i certyfikatów.
services: key-vault
documentationcenter: ''
author: BryanLa
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: abd1b743-1d58-413f-afc1-d08ebf93828a
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: bryanla
ms.openlocfilehash: b1330f2f912f3e5974a43e43f649576561fbcc11
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079229"
---
# <a name="about-keys-secrets-and-certificates"></a>Informacje o kluczach, wpisów tajnych i certyfikatów

Usługa Azure Key Vault umożliwia aplikacji Microsoft Azure i użytkowników do przechowywania i korzystania z kilku typów danych do/z klucz tajny:

- Klucze kryptograficzne: obsługuje wiele typów kluczy i algorytmów i umożliwia użycie sprzętowych modułów zabezpieczeń (HSM) o wysokiej wartości kluczy. 
- Wpisy tajne: Umożliwia użytkownikom bezpieczne przechowywanie wpisów tajnych, takich jak hasła. Klucze tajne są obiektami octet ograniczony rozmiar z nie określonej semantyką. 
- Certyfikaty: Certyfikaty obsługuje, które są oparte na kluczach i wpisach tajnych i dodać funkcję automatycznego odnawiania.
- Usługa Azure Storage: Mogą zarządzać klucze konta usługi Azure Storage dla Ciebie. Wewnętrznie usługa Key Vault można wyświetlić listę kluczy (synchronizacja) za pomocą konta usługi Azure Storage i ponownie wygenerować (Obróć) okresowo kluczy. 

Aby uzyskać więcej ogólnych informacji na temat usługi Key Vault, zobacz [co to jest usługa Azure Key Vault?](/azure/key-vault/key-vault-whatis)

## <a name="azure-key-vault"></a>W usłudze Azure Key Vault

Poniższe sekcje zapewniają ogólne informacje dotyczące różnych implementacji usługi Key Vault.

###  <a name="supporting-standards"></a>Obsługa standardów

JavaScript Object Notation (JSON) i specyfikacje JavaScript obiektu podpisywania i szyfrowania (blog JOSE) są ważne informacje.  

-   [Klucza internetowego JSON (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [Szyfrowanie sieci Web JSON (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [Algorytmy sieci Web JSON (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [Sygnatura sieci Web JSON (JWS)](http://tools.ietf.org/html/draft-ietf-jose-json-web-signature)  

### <a name="data-types"></a>Typy danych

Zapoznaj się ze specyfikacją JOSE dla typów danych odpowiednich dla kluczy szyfrowania i podpisywania.  

-   **Algorytm** -obsługiwany algorytm dla kluczowych operacji, na przykład RSA1_5  
-   **wartość tekstu szyfrowanego** -cipher oktetów tekstu, kodowane w formacie Base64URL  
-   **wartość skrótu** — dane wyjściowe algorytmu wyznaczania wartości skrótu, kodowane w formacie Base64URL  
-   **Typ klucza** — jeden z obsługiwanych typów kluczy, na przykład RSA (Rivest-Shamir-Adleman).  
-   **wartości w postaci zwykłego tekstu** -oktetów zwykłego tekstu, kodowane w formacie Base64URL  
-   **wartość podpisu** — dane wyjściowe algorytmu podpisu, kodowane w formacie Base64URL  
-   **base64URL** -Base64URL [RFC4648] zakodowana wartość binarna  
-   **wartość logiczna** — wartość PRAWDA lub FAŁSZ  
-   **Tożsamość** — tożsamości z usługi Azure Active Directory (AAD).  
-   **IntDate** — wartość dziesiętną JSON reprezentujący liczbę sekund od 1970-01-01T0:0:0Z UTC do daty/godziny określonej UTC. Zobacz, w szczególności RFC3339, aby uzyskać szczegółowe informacje dotyczące date/times ogólnie rzecz biorąc i czasem UTC.  

###  <a name="objects-identifiers-and-versioning"></a>Obiekty, identyfikatory i przechowywania wersji

Obiekty przechowywane w usłudze Key Vault są wersjonowane zawsze wtedy, gdy tworzone jest nowe wystąpienie obiektu. Każda wersja jest przypisany unikatowy identyfikator i adres URL. W chwili utworzenia obiektu ma podany identyfikator unikatowy wersji i oznaczona jako bieżąca wersja obiektu. Tworzenie nowego wystąpienia o takiej samej nazwie obiektu zapewnia nowy obiekt identyfikator unikatowy wersji powoduje przekształcenie go w bieżącej wersji.  

Obiekty w usłudze Key Vault może zostać zlikwidowane przy użyciu bieżącego identyfikatora lub identyfikator specyficzny dla wersji. Na przykład, biorąc pod uwagę klucz o nazwie `MasterKey`, wykonywanie operacji z bieżącym identyfikatorem powoduje, że system ma używać najnowszej dostępnej wersji. Wykonywanie operacji na wartościach identyfikator specyficzny dla wersji powoduje, że system ma używać tej wersji określonego obiektu.  

Obiekty są identyfikowane w usłudze Key Vault przy użyciu adresu URL. Żadne dwa obiekty w systemie ma ten sam adres URL, niezależnie od lokalizacji geograficznej. Pełny adres URL do obiektu jest nazywany identyfikatora obiektu. Adres URL składa się z prefiksu, który identyfikuje usługę Key Vault, typ obiektu, podany przez użytkownika nazwa obiektu i wersji obiektów. Nazwa obiektu jest rozróżniana wielkość liter i niezmienne. Identyfikatory, które nie zawierają wersja obiektu są określane jako podstawa identyfikatorów.  

Aby uzyskać więcej informacji, zobacz [uwierzytelniania, żądań i odpowiedzi](authentication-requests-and-responses.md)

Identyfikator obiektu ma następujący format Ogólne:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Gdzie:  

|||  
|-|-|  
|`keyvault-name`|Nazwa magazynu kluczy w usłudze Microsoft Azure Key Vault.<br /><br /> Nazwy usługi Key Vault są wybierane przez użytkownika i są globalnie unikatowe.<br /><br /> Nazwa magazynu kluczy musi być ciągiem 3 do 24 znaków, zawierający tylko 0-9, a-z, A-Z, - i.|  
|`object-type`|Typ obiektu, "klucze" lub "klucze tajne".|  
|`object-name`|`object-name` Jest dostarczone przez użytkownika nazwę, a musi być unikatowa w obrębie usługi Key Vault. Nazwa musi być ciągiem znaków 1-127, zawierający tylko 0-9, a-z, A-Z, - i.|  
|`object-version`|`object-version` Jest generowanych przez system, 32 identyfikator ciągu znaków, który opcjonalnie jest wykorzystywana do adresowania unikatowy wersji obiektu.|  

## <a name="key-vault-keys"></a>Klucze usługi Key Vault

###  <a name="keys-and-key-types"></a>Klucze i typy kluczy

Klucze szyfrowania w usłudze Key Vault jest reprezentowane przez obiekty klucza internetowego JSON [JWK]. Podstawowej specyfikacji JWK/JWA również zostały rozszerzone umożliwiające typy kluczy, które są unikatowe dla implementacji usługi Key Vault. Umożliwia importowanie kluczy przy użyciu pakietu specyficzne dla dostawcy sprzętowego modułu zabezpieczeń, na przykład bezpiecznego transportu kluczy, które mogą być używane tylko w sprzętowych modułach zabezpieczeń klucza magazynu.  

- **Klucze "Elastyczne"**: klucz przetwarzane w oprogramowania za pomocą usługi Key Vault, ale są szyfrowane w stanie spoczynku przy użyciu klucza system, który znajduje się w module HSM. Klienci mogą Importuj istniejący klucz RSA lub WE (krzywej eliptycznej) lub żądania usługi Key Vault wygenerowanie takiego.
- **Klucze "Twarde"**: klucz przetwarzane w sprzętowym module zabezpieczeń (sprzętowy moduł zabezpieczeń). Te klucze są chronione w jednym z środowiska klucza magazynu przez sprzętowy moduł zabezpieczeń Security World (Brak jednego środowiska zabezpieczeń Security World dla lokalizacji geograficznej w celu zachowania izolacji). Klientów można zaimportować klucza RSA lub WE, w postaci nietrwałego lub eksportowanie z zgodnego urządzenia sprzętowego modułu zabezpieczeń. Klienci mogą również poprosić o usłudze Key Vault, aby wygenerować klucz. Ten typ klucza dodaje atrybut T JWK pobrać do przenoszenia materiału klucza sprzętowego modułu zabezpieczeń.

     Aby uzyskać więcej informacji o granicach geograficznych, zobacz [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/privacy/)  

Usługa Key Vault obsługuje jedynie klucze RSA i krzywej eliptycznej. Przyszłe wersje może obsługiwać inne typy kluczy, takie jak symetryczne.

-   **WE**: klucz "Elastyczne" krzywej eliptycznej.
-   **Modułu HSM we**: klucz "Twardym" krzywej eliptycznej.
-   **RSA**: klucz RSA "Elastyczne".
-   **RSA HSM**: klucz RSA "Twarde".

Usługa Key Vault obsługuje klucze RSA rozmiarów, 2048, 3072 do 4096. Key Vault obsługuje krzywej eliptycznej klucz typy p-256, p-384, p-521 i P - 256 KB.

### <a name="cryptographic-protection"></a>Kryptograficzna ochrona

Modułów kryptograficznych, które korzysta z usługi Key Vault, czy oprogramowania, lub przez moduł HSM są zweryfikowane w trybie FIPS (Federal Information przetwarzania normy). Nie trzeba podejmować żadnych działań specjalne do uruchamiania w trybie FIPS. Jeśli użytkownik **tworzenie** lub **zaimportować** klucze jako chroniony przez moduł HSM, są one gwarantowane mają być przetwarzane w ramach modułu HSM zweryfikowane FIPS 140-2 poziom 2 lub nowszej. Jeśli użytkownik **tworzenie** lub **zaimportować** klucze jako chronionego przez oprogramowanie, są one przetworzone wewnątrz modułów kryptograficznych służących do sprawdzania poprawności certyfikatu FIPS 140-2 poziom 1 lub nowszy. Aby uzyskać więcej informacji, zobacz [kluczy i typy kluczy](#keys-and-key-types).

###  <a name="ec-algorithms"></a>Algorytmy WE
 Następujące identyfikatory algorytm są obsługiwane przy użyciu kluczy WE i WE modułu HSM w usłudze Key Vault. 

#### <a name="signverify"></a>SPRAWDŹ/LOGOWANIA

-   **ES256** — skróty służące ECDSA SHA-256 i klucze są tworzone z krzywej p-256. Ten algorytm jest opisany w [RFC7518].
-   **ES256K** — skróty służące ECDSA SHA-256 i klucze są tworzone z krzywej P-256_K. Ten algorytm oczekuje normalizacji.
-   **ES384** — skróty służące ECDSA dla algorytmu SHA-384 i klucze są tworzone z krzywej p-384. Ten algorytm jest opisany w [RFC7518].
-   **ES512** — skróty służące ECDSA dla SHA-512 i klucze są tworzone z krzywej p-521. Ten algorytm jest opisany w [RFC7518].

###  <a name="rsa-algorithms"></a>Algorytmy RSA  
 Następujące identyfikatory algorytm są obsługiwane przy użyciu kluczy RSA i RSA modułu HSM w usłudze Key Vault.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, SZYFROWANIA/ODSZYFROWYWANIA

-   **RSA1_5** — szyfrowanie klucza RSAES PKCS1 V1_5 [RFC3447]  
-   **RSA OAEP** — RSAES optymalne asymetrycznego szyfrowania dopełnienie (OAEP) [RFC3447], przy użyciu parametrów domyślnych, określony przez 3447 RFC w sekcji A.2.1. Te domyślne parametry za pomocą funkcji skrótu SHA-1 i funkcja generowania maski MGF1 SHA-1.  

#### <a name="signverify"></a>SPRAWDŹ/LOGOWANIA

-   **RS256** — RSASSA-PKCS-v1_5 przy użyciu algorytmu SHA-256. Wartość skrótu dostarczona aplikacja musi być obliczane przy użyciu algorytmu SHA-256 i musi być 32 bajtów długości.  
-   **RS384** — RSASSA-PKCS-v1_5 przy użyciu algorytmu SHA-384. Wartość skrótu dostarczona aplikacja musi być obliczane przy użyciu algorytmu SHA-384 i musi być 48 bajtów długości.  
-   **RS512** — RSASSA-PKCS-v1_5 przy użyciu algorytmu SHA-512. Wartość skrótu dostarczona aplikacja musi być obliczane przy użyciu algorytmu SHA-512 i muszą być 64 bajtów długości.  
-   **RSNULL** — Zobacz [RFC2437] wyspecjalizowane przypadek użycia niektórych scenariuszy TLS.  

###  <a name="key-operations"></a>Kluczowe operacje

Usługa Key Vault obsługuje następujące operacje na obiektach klucza:  

-   **Utwórz**: umożliwia klientowi utworzyć klucz w usłudze Key Vault. Wartość klucza jest generowany przez usługę Key Vault i przechowywane, a nie do klienta. Asymetryczny (i w przyszłości krzywej eliptycznej i Symmetric) mogą zostać utworzone w usłudze Key Vault.  
-   **Importowanie**: umożliwia klientowi zaimportować istniejący klucz usługi Key Vault. Klucze asymetryczne może zaimportować do usługi Key Vault przy użyciu szeregu metod tworzenia różnych pakietów w konstrukcji JWK. W przyszłości krzywej eliptycznej i Symmetric również będą takie same w pakiecie.
-   **Aktualizacja**: umożliwia klientowi z wystarczającymi uprawnieniami do modyfikowania metadanych (atrybutów kluczy) skojarzony z kluczem, który został wcześniej są przechowywane w usłudze Key Vault.  
-   **Usuń**: umożliwia klientowi z wystarczającymi uprawnieniami do usunięcia klucza z usługi Key Vault.  
-   **Lista**: umożliwia klientowi wyświetlić listę wszystkich kluczy w danej usłudze Key Vault.  
-   **Lista wersji**: umożliwia klientowi wyświetlić listę wszystkich wersji danego klucza w danej usłudze Key Vault.  
-   **Pobierz**: umożliwia klientowi pobrać publiczny części danego klucza w usłudze Key Vault.  
-   **Kopia zapasowa**: eksportuje klucza w postaci chronionych.  
-   **Przywróć**: importuje wcześniej kopii zapasowej klucza.  

Aby uzyskać więcej informacji, zobacz [klucza operacje w dokumentacji interfejsu API REST usługi Key Vault](/rest/api/keyvault).  

Po utworzeniu klucz w usłudze Key Vault może wykonać następujące operacje kryptograficzne przy użyciu klucza:  

-   **Logowanie i sprawdź, czy**: ściśle, ta operacja jest "skrót znaku" lub "Weryfikuj wyznaczania wartości skrótu,", ponieważ usługa Key Vault nie obsługuje wyznaczania wartości skrótu zawartości w ramach tworzenia podpisu. Aplikacje powinny wyznaczania wartości skrótu danych były podpisane lokalnie, a następnie żądanie logowania tej usługi Key Vault skrót. Weryfikacji podpisanego skróty jest obsługiwany jako operacja jako udogodnienie dla aplikacji, które mogą nie mieć dostępu do [publiczny] materiału klucza. Aby uzyskać najlepszą wydajność aplikacji Sprawdź, czy operacje są wykonywane lokalnie.  
-   **Klucz szyfrowania / zawijanie**: klucza przechowywanego w usłudze Key Vault może służyć do ochrony klucza inny, zwykle klucza szyfrowania symetrycznego zawartości (CEK). Gdy klucz w usłudze Key Vault jest asymetrycznego, kluczy szyfrowania jest używany. Na przykład RSA OAEP i WRAPKEY/UNWRAPKEY operacje, które są równoważne do szyfrowania/ODSZYFROWYWANIA. Gdy klucz w usłudze Key Vault jest symetryczne, jest używany klucz zawijania. Na przykład AES KW. Operacja WRAPKEY jest obsługiwana jako udogodnienie dla aplikacji, które mogą nie mieć dostępu do [publiczny] materiału klucza. Aby uzyskać najlepszą wydajność aplikacji WRAPKEY operacji powinno być przeprowadzane lokalnie.  
-   **Szyfrowanie i odszyfrowywanie**: klucza przechowywanego w usłudze Key Vault może służyć do szyfrowania i odszyfrowywania jednego bloku danych. Rozmiar bloku zależy od typu klucza i algorytm szyfrowania wybrane. Operacja szyfrowania jest dostarczany jako udogodnienie, dla aplikacji, które mogą nie mieć dostępu do [publiczny] materiału klucza. Aby uzyskać najlepszą wydajność aplikacji szyfrowanie operacje, które powinny być wykonywane lokalnie.  

Podczas WRAPKEY/UNWRAPKEY przy użyciu kluczy asymetrycznych może wydawać się zbędne (co operacji jest odpowiednikiem szyfrowania/ODSZYFROWYWANIA), ważne jest korzystanie z różnych operacji. Rozróżnienie zawiera semantyczną i autoryzacji rozdzielenie tych operacji i spójności w przypadku innych typów kluczy są obsługiwane przez usługę.  

Usługa Key Vault nie obsługuje operacji EKSPORTOWANIA. Gdy klucz zostanie zaaprowizowana w systemie, nie można wyodrębnić lub zmodyfikować jego materiał klucza. Jednak użytkownicy usługi Key Vault może wymagać swój klucz dla innych zastosowań, takich jak po został usunięty. W takim przypadku mogą używać operacji kopii zapasowej i przywracania do eksportu/importu klucza w postaci chronionych. Kluczy utworzonych przez Operacja tworzenia kopii zapasowej nie są użyteczne spoza usługi Key Vault. Alternatywnie operacja IMPORTOWANIA może służyć względem wielu wystąpień usługi Key Vault.  

Użytkownicy mogą ograniczać dowolne operacje kryptograficzne, które obsługuje usługi Key Vault, na podstawie-key, przy użyciu właściwości key_ops obiektu JWK.  

Aby uzyskać więcej informacji na temat JWK obiektów, zobacz [klucza sieci Web JSON (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key).  

###  <a name="key-attributes"></a>Kluczowych atrybutów

Oprócz materiał klucza można określić następujące atrybuty. Żądania JSON, słowo kluczowe atrybuty i nawiasów klamrowych "{" "}", są wymagane, nawet gdy pojawią się nie określone atrybuty.  

- *włączone*: wartość logiczna, opcjonalna, wartością domyślną jest **true**. Określa, czy klucz jest włączone i niemożliwe do operacji kryptograficznych. *Włączone* atrybut jest używany w połączeniu z *nbf* i *exp*. Podczas operacji odbywa się między *nbf* i *exp*, będzie można dopuścić tylko, jeśli *włączone* jest ustawiona na **true**. Operacje poza *nbf* / *exp* okno automatycznie są niedozwolone, z wyjątkiem niektórych typów operacji w ramach [szczególnych warunków](#date-time-controlled-operations).
- *NBF*: IntDate, opcjonalne, domyślnie teraz wynosi. *Nbf* (nie wcześniej niż) atrybut określa czas, przed którym klucz nie może być używany dla operacji kryptograficznych, z wyjątkiem niektórych typów operacji w ramach [szczególnych warunków](#date-time-controlled-operations). Przetwarzanie *nbf* atrybut wymaga, że bieżąca data/godzina musi przypadać po lub nie ma wartości — przed daty/godziny na liście *nbf* atrybutu. Usługa Key Vault może przewidzieć kilka małych swobodę zwykle nie więcej niż kilka minut, aby uwzględnić zegara pochylanie. Jego wartość musi być liczbą zawierającą wartość IntDate.  
- *EXP*: IntDate, opcjonalnie, wartość domyślna to "nieskończoność". *Exp* atrybut (czas wygaśnięcia) określa czas wygaśnięcia r. lub później, którego klucz nie może służyć do operacji kryptograficznych, z wyjątkiem niektórych typów operacji w ramach [szczególnych warunków](#date-time-controlled-operations). Przetwarzanie *exp* atrybut wymaga bieżącej daty/godziny należy przed Data/godzina wygaśnięcia na liście *exp* atrybutu. Usługa Key Vault może przewidzieć kilka małych swobodę zwykle nie więcej niż kilka minut, aby uwzględnić zegara pochylanie. Jego wartość musi być liczbą zawierającą wartość IntDate.  

Istnieją dodatkowe atrybuty tylko do odczytu, które są objęte żadnej odpowiedzi, która obejmuje kluczowych atrybutów:  

- *utworzone*: IntDate opcjonalne. *Utworzone* atrybut wskazuje, kiedy utworzono tę wersję klucza. Ma wartość null dla kluczy utworzonych przed dodaniem tego atrybutu. Jego wartość musi być liczbą zawierającą wartość IntDate.  
- *Zaktualizowano*: IntDate opcjonalne. *Zaktualizowane* atrybut wskazuje, kiedy ta wersja klucza została zaktualizowana. Ma wartość null dla kluczy, które zostały ostatnio zaktualizowane przed dodaniem tego atrybutu. Jego wartość musi być liczbą zawierającą wartość IntDate.  

Aby uzyskać więcej informacji na temat IntDate a innymi typami danych, zobacz [typy danych](#data-types)  

#### <a name="date-time-controlled-operations"></a>Data i godzina kontrolowane operacji

Nie zostały jeszcze prawidłowy i klucze, poza *nbf* / *exp* okna, będzie działać w przypadku **odszyfrować**, **Odkodowywanie**i **Sprawdź** operacji (nie zwraca 403, zabronione). Uzasadnienie wykorzystania nie zostały jeszcze prawidłowego stanu jest Zezwalaj na klucz ma zostać przetestowana przed użycia w środowisku produkcyjnym. Racjonalne uzasadnienie przy użyciu stanu wygaśnięcia jest Zezwalaj na operacje odzyskiwania danych, który został utworzony, gdy klucz był nieprawidłowy. Ponadto, możesz wyłączyć dostęp do klucza przy użyciu zasad usługi Key Vault lub aktualizując *włączone* atrybutu klucza do **false**.

Aby uzyskać więcej informacji na temat typów danych, zobacz [typy danych](#data-types).

Aby uzyskać więcej informacji o innych atrybutach możliwych zobacz [klucza sieci Web JSON (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key).

### <a name="key-tags"></a>Klucz tagów

Można określić dodatkowe metadane specyficzne dla aplikacji w postaci tagów. Magazyn kluczy obsługuje maksymalnie 15 tagów, z których każdy może mieć nazwy 256 znaków i wartości 256 znaków.  

>[!Note]
>Tagi są odczytany przez obiekt wywołujący, jeśli mają one *listy* lub *uzyskać* uprawnienie do tego typu obiektów (kluczy, wpisów tajnych lub certyfikatów).

###  <a name="key-access-control"></a>Kontrola dostępu do kluczy

Kontrola dostępu do kluczy zarządzanych przez usługę Key Vault znajduje się na poziomie usługi Key Vault, który działa jako kontener kluczy. Zasady kontroli dostępu do kluczy, różni się od zasad kontroli dostępu dotyczących wpisów tajnych w tej samej usłudze Key Vault. Użytkownicy mogą utworzyć co najmniej jeden magazynów do przechowywania kluczy i są zobowiązane do zachowania segmentacji odpowiedni scenariusz i zarządzanie kluczami. Kontrola dostępu do kluczy jest niezależna od kontroli dostępu do kluczy tajnych.  

Następujące uprawnienia mogą być udzielone na na użytkownika / usługi głównej podstawy w wpisu kontroli dostępu kluczy w magazynie. Te uprawnienia duplikatów ściśle dozwolone klucza obiektu operacje:  

- Uprawnienia dla operacji zarządzania kluczami
  - *Pobierz*: Odczyt publiczną część klucza, a także jego atrybuty
  - *Lista*: Utwórz listę kluczy lub wersji tego klucza, przechowywanych w magazynie kluczy
  - *Aktualizuj*: zaktualizować atrybutów dla klucza
  - *Utwórz*: tworzenie nowych kluczy
  - *Importowanie*: Importuj klucz do magazynu kluczy
  - *Usuń*: usuwanie klucza obiektu
  - *Odzyskaj*: odzyskać usunięty klucz
  - *kopii zapasowej*: tworzenie kopii zapasowej klucza w magazynie kluczy
  - *Przywróć*: Przywracanie kopii zapasowej klucza do magazynu kluczy

- Uprawnienia dla operacji kryptograficznych
  - *odszyfrowywanie*: Użyj klucza do usunięcia ochrony sekwencja bajtów
  - *Szyfruj*: Użyj klucza do ochrony dowolnego sekwencji bajtów
  - *unwrapKey*: Użyj klucza do usunięcia ochrony opakowane kluczy symetrycznych
  - *wrapKey*: ochrona klucza symetrycznego za pomocą klawisza
  - *Sprawdź*: Sprawdź skróty przy użyciu klucza  
  - *znak*: Użyj skróty służące klucza do podpisywania
    
- Uprawnienia dla uprzywilejowanych operacji korzystających z
  - *Przeczyść*: wyczyść (trwałe usunięcie) usunięty klucz

Aby uzyskać więcej informacji na temat pracy z kluczami, zobacz [klucza operacje w dokumentacji interfejsu API REST usługi Key Vault](/rest/api/keyvault). Aby uzyskać informacje na ustanawianie uprawnień, zobacz [magazynów — Utwórz lub zaktualizuj](/rest/api/keyvault/vaults/createorupdate) i [magazynów — zasady dostępu do aktualizacji](/rest/api/keyvault/vaults/updateaccesspolicy). 

## <a name="key-vault-secrets"></a>Wpisy tajne usługi Key Vault 

### <a name="working-with-secrets"></a>Praca z wpisami tajnymi

Klucze tajne w usłudze Key Vault to sekwencje oktetu o maksymalnym rozmiarze 25 KB każdego. Usługa Key Vault nie zapewnia semantykę, dotyczących wpisów tajnych. Go jedynie akceptuje dane, szyfruje je, przechowuje go i zwraca identyfikator wpisu tajnego ("id"). Identyfikator może służyć do pobierania klucza tajnego w późniejszym czasie.  

W przypadku wysoce poufnych danych klientów należy wziąć pod uwagę dodatkowych warstw ochrony danych. Szyfruje dane przy użyciu oddzielnych ochrony klucza przed magazynu w usłudze Key Vault jest jednym z przykładów.  

Usługa Key Vault obsługuje również pola contentType wpisów tajnych. Klienci mogą określać typu zawartości, aby ułatwić interpretowanie poufne dane podczas pobierania klucza tajnego. Maksymalna długość tego pola to 255 znaków. Istnieją wstępnie zdefiniowane wartości. Sugerowane użycia jest jako wskazówka do interpretacji danych klucza tajnego. Na przykład implementacja może przechowywać hasła i certyfikaty jako wpisy tajne, a następnie to pole służy do rozróżnienia. Istnieją wstępnie zdefiniowane wartości.  

### <a name="secret-attributes"></a>Atrybuty wpisu tajnego

Oprócz tajnych danych można określić następujące atrybuty:  

- *EXP*: IntDate, opcjonalnie, wartością domyślną jest **nieskończona**. *Exp* atrybut (czas wygaśnięcia) określa czas wygaśnięcia na lub po którym wpisu tajnego powinna nie można pobrać danych, z wyjątkiem [sytuacji, w szczególności](#date-time-controlled-operations). To pole jest **informacyjny** tylko wtedy, gdy informuje użytkowników magazynu kluczy usługi określonego klucza tajnego nie mogą być używane w celach. Jego wartość musi być liczbą zawierającą wartość IntDate.   
- *NBF*: IntDate, opcjonalnie, wartością domyślną jest **teraz**. *Nbf* (nie wcześniej niż) atrybut określa czas, przed którą dane tajne powinna nie można pobrać, z wyjątkiem [sytuacji, w szczególności](#date-time-controlled-operations). To pole jest **informacyjny** wyłącznie do celów. Jego wartość musi być liczbą zawierającą wartość IntDate. 
- *włączone*: wartość logiczna, opcjonalna, wartością domyślną jest **true**. Ten atrybut określa, czy można pobrać tajnego danych. Atrybut włączony jest używany w połączeniu z i *exp* podczas operacji odbywa się między i exp, jej zostanie dopuszczona, jeśli włączone jest ustawiona na **true**. Operacje poza *nbf* i *exp* automatycznie są niedozwolone, z wyjątkiem w [sytuacji, w szczególności](#date-time-controlled-operations).  

Istnieją dodatkowe atrybuty tylko do odczytu, które są objęte żadnej odpowiedzi, która zawiera atrybuty wpisu tajnego:  

- *utworzone*: IntDate opcjonalne. Utworzonego atrybutu wskazuje, kiedy utworzono tej wersji klucza tajnego. Ta wartość jest pusta dla wpisów tajnych utworzone przed dodaniem tego atrybutu. Jego wartość musi być liczbą zawierającą wartość IntDate.  
- *Zaktualizowano*: IntDate opcjonalne. Zaktualizowano atrybut wskazuje, gdy zaktualizowano tę wersję klucza tajnego. Ta wartość jest pusta dla wpisów tajnych, które zostały ostatnio zaktualizowane przed dodaniem tego atrybutu. Jego wartość musi być liczbą zawierającą wartość IntDate.

#### <a name="date-time-controlled-operations"></a>Data i godzina kontrolowane operacji

Klucz tajny **uzyskać** operacji będzie działać w przypadku nie zostały jeszcze nieprawidłowe lub wygasłe wpisów tajnych oraz ich poza *nbf* / *exp* okna. Klucz tajny podczas wywoływania **uzyskać** nie zostały jeszcze prawidłowy klucz tajny, operacja może służyć do celów testowych. Trwa pobieranie (**uzyskać**ing) wygasły klucz tajny, może służyć do operacji odzyskiwania.

Aby uzyskać więcej informacji na temat typów danych, zobacz [typy danych](#data-types).  

### <a name="secret-access-control"></a>Kontrola dostępu do wpisów tajnych

Kontrola dostępu do kluczy tajnych zarządzane w usłudze Key Vault, znajduje się na poziomie usługi Key Vault, która zawiera te wpisy tajne. Zasady kontroli dostępu dotyczących wpisów tajnych, różni się od zasad kontroli dostępu do kluczy w tej samej usłudze Key Vault. Użytkownicy mogą utworzyć co najmniej jeden magazynów do przechowywania wpisów tajnych i są zobowiązane do zachowania segmentacji odpowiedni scenariusz i zarządzania wpisami tajnymi.   

Następujące uprawnienia mogą być używane, na podstawie na jednostkę w wpisu kontroli dostępu wpisów tajnych w magazynie i dokładnie takie same jak stosowane dozwolone w obiekcie wpisu tajnego operacje:  

- Uprawnienia dla operacji zarządzania wpisami tajnymi
  - *Pobierz*: odczytu wpisu tajnego  
  - *Lista*: Utwórz listę kluczy tajnych lub wersji klucza tajnego przechowywanych w usłudze Key Vault  
  - *Ustaw*: Tworzenie klucza tajnego  
  - *Usuń*: Usuń klucz tajny  
  - *Odzyskaj*: odzyskania usuniętego wpisu tajnego
  - *kopii zapasowej*: kopii zapasowej wpisu tajnego w magazynie kluczy
  - *Przywróć*: Przywracanie kopii zapasowej klucza tajnego do magazynu kluczy

- Uprawnienia dla uprzywilejowanych operacji korzystających z
  - *Przeczyść*: wyczyść (trwałe usunięcie) usuniętego wpisu tajnego

Aby uzyskać więcej informacji na temat pracy z wpisy tajne, zobacz [operacje dotyczące wpisów tajnych w dokumentacji interfejsu API REST usługi Key Vault](/rest/api/keyvault). Aby uzyskać informacje na ustanawianie uprawnień, zobacz [magazynów — Utwórz lub zaktualizuj](/rest/api/keyvault/vaults/createorupdate) i [magazynów — zasady dostępu do aktualizacji](/rest/api/keyvault/vaults/updateaccesspolicy). 

### <a name="secret-tags"></a>Tagi wpisu tajnego  
Można określić dodatkowe metadane specyficzne dla aplikacji w postaci tagów. Magazyn kluczy obsługuje maksymalnie 15 tagów, z których każdy może mieć nazwy 256 znaków i wartości 256 znaków.  

>[!Note]
>Tagi są odczytany przez obiekt wywołujący, jeśli mają one *listy* lub *uzyskać* uprawnienie do tego typu obiektów (kluczy, wpisów tajnych lub certyfikatów).

## <a name="key-vault-certificates"></a>Certyfikaty usługi Key Vault

Pomoc techniczna certyfikaty usługi Key Vault zapewnia zarządzanie x509 swoje certyfikaty i następujące zachowania:  

-   Umożliwia właściciela certyfikatu utworzyć certyfikat za pośrednictwem procesu tworzenia usługi Key Vault lub importowania istniejącego certyfikatu. Dotyczy to zarówno z podpisem własnym, a urząd certyfikacji generowanych certyfikaty.
-   Umożliwia właścicielowi certyfikatu usługi Key Vault, do zaimplementowania bezpieczne przechowywanie i zarządzania X509 certyfikaty bez interakcji z materiał klucza prywatnego.  
-   Umożliwia właściciela certyfikatu utworzyć zasadę, który kieruje Key Vault, aby zarządzać cyklem życia certyfikatu.  
-   Umożliwia właścicielom certyfikat, podaj informacje kontaktowe dla powiadomień dotyczących zdarzeń cyklu życia wygaśnięcia i odnawiania certyfikatu.  
-   Obsługuje automatycznego odnawiania z wystawców wybranych - partnera usługi Key Vault X509 certyfikatu dostawcy / urzędy certyfikacji.

>[!Note]
>Partnerstwo inne niż dostawcy/urzędy są również dozwolone, ale nie będzie obsługiwał funkcji automatycznego odnawiania.

### <a name="composition-of-a-certificate"></a>Tworzenie certyfikatu

Po utworzeniu certyfikatem usługi Key Vault, mogą być adresowane kluczem i wpisem tajnym są również tworzone o takiej samej nazwie. Klucz usługi Key Vault umożliwia wykonywanie operacji klucza i wpisu tajnego usługi Key Vault umożliwia pobieranie wartości certyfikat jako wpis tajny. Certyfikatem usługi Key Vault zawiera także x509 publicznego certyfikatu metadanych.  

Identyfikator i wersja certyfikatów jest podobny do kluczy i wpisów tajnych. Określoną wersję mogą być adresowane kluczem i wpisem tajnym, utworzony za pomocą wersji certyfikatu usługi Key Vault jest dostępna w odpowiedzi certyfikatu usługi Key Vault.
 
![Certyfikaty są złożone obiekty](media/azure-key-vault.png)

### <a name="exportable-or-non-exportable-key"></a>Można eksportować lub nie można eksportować klucza

Po utworzeniu certyfikatem usługi Key Vault można pobrać z adresowalnych wpisu tajnego z kluczem prywatnym w formacie PFX lub PEM. Zasady stosowane do utworzenia certyfikatu musi wskazywać, że klucz jest eksportowalny. Jeśli zasady nie można eksportować, klucz prywatny nie jest częścią wartości podczas pobierania jako klucz tajny.  

Mogą być adresowane klucz staje się bardziej przydatna przy użyciu nieeksportowalnej KV certyfikatów. Operacje mogą być adresowane klucz KV są mapowane z *keyusage* pole KV zasad certyfikat użyty do utworzenia certyfikatu KV.  

Obsługiwane są dwa rodzaje klucz — *RSA* lub *sprzętowego modułu zabezpieczeń RSA* za pomocą certyfikatów. Można eksportować jest dozwolona tylko w RSA, nie są obsługiwane przez moduł HSM RSA.  

### <a name="certificate-attributes-and-tags"></a>Certyfikat atrybuty i znaczniki

Oprócz metadanymi certyfikatu klucza adresowalnych i mogą być adresowane klucz tajny certyfikatem usługi Key Vault zawiera też atrybuty i znaczniki.  

#### <a name="attributes"></a>Atrybuty

Atrybuty mogą być adresowane klucza i wpisu tajnego tworzone podczas tworzenia certyfikatu KV są dublowane atrybuty certyfikatu.  

Certyfikatem usługi Key Vault ma następujące atrybuty:  

-   *włączone*: wartość logiczna, opcjonalna, wartością domyślną jest **true**. Można określić tak, aby wskazać, jeśli dane certyfikatu można pobrać jako poufne lub wykonywalny jako klucz. Również używany w połączeniu z *nbf* i *exp* podczas operacji odbywa się między *nbf* i *exp*i tylko będą dozwolone, jeśli włączona jest ustawiona na wartość true. Operacje poza *nbf* i *exp* okno automatycznie są niedozwolone.  

Istnieją dodatkowe atrybuty tylko do odczytu, które znajdują się w odpowiedzi:

-   *utworzone*: IntDate: wskazuje, kiedy utworzono tę wersję certyfikatu.  
-   *Zaktualizowano*: IntDate: wskazuje, kiedy ta wersja certyfikatu została zaktualizowana.  
-   *EXP*: IntDate: zawiera wartość daty wygaśnięcia x509 certyfikatu.  
-   *NBF*: IntDate: zawiera wartość daty x509 certyfikatu.  

> [!Note] 
> Jeśli upłynie certyfikatem usługi Key Vault, jest adresowalnych klucza i wpisu tajnego przestać działać w.  

#### <a name="tags"></a>Tagi

 Klient określonego słownika par kluczy i wartości, podobnie jak tagów w kluczy i wpisów tajnych.  

 > [!Note]
> Tagi są odczytany przez obiekt wywołujący, jeśli mają one *listy* lub *uzyskać* uprawnienie do tego typu obiektów (kluczy, wpisów tajnych lub certyfikatów).

### <a name="certificate-policy"></a>Zasady dotyczące certyfikatów

Zasady certyfikatów zawiera informacje dotyczące sposobu tworzenia i zarządzania cyklem życia z certyfikatem usługi Key Vault. Po zaimportowaniu certyfikatu z kluczem prywatnym w magazynie kluczy, czytając x509 tworzone są zasady domyślne certyfikatu.  

Po utworzeniu certyfikatem usługi Key Vault od podstaw zasad musi podać. Zasady określają sposób tworzenia tej wersji certyfikatu usługi Key Vault lub kolejnej wersji certyfikatu usługi Key Vault. Po ustanowieniu zasad, nie jest wymagane w przypadku kolejnych operacji w przyszłych wersjach tworzenia. Istnieje tylko jedno wystąpienie zasad dla wszystkich wersji z certyfikatem usługi Key Vault.  

Na wysokim poziomie zasad certyfikatów zawiera następujące informacje:  

-   X509 certyfikatu właściwości: zawiera nazwy podmiotu, alternatywnej nazwy podmiotu i inne właściwości, używany do tworzenia x509 żądania certyfikatu.  
-   Właściwości klucza: zawiera typ klucza, długość, który można eksportować, klucza i ponowne użycie pola klucza. Te pola poinstruować magazynu kluczy na temat generowania klucza.  
-   Właściwości wpisu tajnego: zawiera właściwości wpisu tajnego, takie jak typ zawartości mogą być adresowane klucz tajny, aby wygenerować wartość wpisu tajnego do pobierania certyfikatu jako klucz tajny.  
-   Okres istnienia akcje: zawiera akcje okres istnienia certyfikatu KV. Każda akcja okres istnienia zawiera:  

     - Wyzwalacz: określona za pomocą dni przed wygaśnięciem lub okres istnienia procent zakresu  

     - Akcja: Określanie typu akcji — *emailContacts* lub *automatycznego odnawiania*  

-   Wystawca: Parametry o wystawcy certyfikatu na potrzeby wystawiania x509 certyfikatów.  
-   Atrybuty zasad: zawiera atrybuty skojarzone z zasadami  

#### <a name="x509-to-key-vault-usage-mapping"></a>X509 do mapowania użycia usługi Key Vault

Poniższa tabela przedstawia mapowanie x509 użycia klucza zasady obowiązujące kluczowych operacji klucza utworzona w ramach tworzenia certyfikatu usługi Key Vault.

|**X509 flagi użycia klucza**|**Platforma ops klucza usługi Key Vault**|**Zachowanie domyślne**|
|----------|--------|--------|
|DataEncipherment|szyfrowanie, odszyfrowywanie| ND |
|DecipherOnly|/ odszyfrowywania| ND  |
|Bity DigitalSignature|Zaloguj się, sprawdź| Domyślne usługi Key Vault bez użycia specyfikacji w czasie tworzenia certyfikatów | 
|EncipherOnly|encrypt| ND |
|KeyCertSign|Zaloguj się, sprawdź|ND|
|KeyEncipherment|wrapKey i unwrapKey| Domyślne usługi Key Vault bez użycia specyfikacji w czasie tworzenia certyfikatów | 
|Niemożność wyparcia|Zaloguj się, sprawdź| ND |
|crlsign|Zaloguj się, sprawdź| ND |

### <a name="certificate-issuer"></a>Wystawca certyfikatu

Obiektu certyfikatu usługi Key Vault przechowuje konfigurację, używany do komunikowania się z dostawcą wystawcy certyfikatu wybranego w kolejności x509 certyfikaty.  

-   Usługa Key Vault w ramach której partnerzy z następującymi dostawcy wystawcy certyfikatu dla certyfikatów SSL

|**Nazwa dostawcy**|**Lokalizacje**|
|----------|--------|
|DigiCert|Obsługiwane we wszystkich lokalizacjach usługi magazynu kluczy w chmurze publicznej i Azure dla instytucji rządowych|
|GlobalSign|Obsługiwane we wszystkich lokalizacjach usługi magazynu kluczy w chmurze publicznej i Azure dla instytucji rządowych|

Wystawca certyfikatu można było utworzyć w usłudze Key Vault, musisz pomyślnie wykonano następujące wstępnie wymagane kroki 1 i 2.  

1. Dołączanie do certyfikatu urzędu certyfikacji dostawców  

    -   Administrator organizacja musi dołączyć firmy (np.) Contoso) z co najmniej jeden dostawca urzędu certyfikacji.  

2. Administrator tworzy żądającego poświadczeń dla usługi Key Vault do rejestrowania (i odnawiania) certyfikaty SSL  

    -   Udostępnia konfigurację, który ma być używany do utworzenia obiektu wystawca dostawcy w usłudze key vault  

Aby uzyskać więcej informacji na temat tworzenia obiektów wystawcy w portalu certyfikatów, zobacz [blogu certyfikaty magazynu kluczy](http://aka.ms/kvcertsblog)  

Usługa Key Vault umożliwia utworzenie wielu obiektów wystawcy z konfiguracją dostawcy inną wystawcy. Po utworzeniu obiektu wystawcy jego nazwę można odwoływać się do jednej lub wielu zasad certyfikatu. Odwołanie do obiektu wystawcy powoduje, że usługi Key Vault, aby użyć konfiguracji określonych w obiekcie wystawcy, podczas żądania x509 certyfikatu od dostawcy urzędu certyfikacji podczas tworzenia certyfikatu i odnawiania.  

Obiekty wystawców są tworzone w magazynie i mogą być używane tylko z certyfikatami KV, w tym samym magazynie.  

### <a name="certificate-contacts"></a>Kontakty certyfikatu

Kontakty certyfikatu zawierają informacji kontaktowych, aby wysyłać powiadomienia wyzwalane przez zdarzenia okresu istnienia certyfikatu. Informacje o kontaktach jest udostępniany wszystkie certyfikaty w magazynie kluczy. Powiadomienie jest wysyłane do wszystkich kontaktów określonego zdarzenia dla każdego certyfikatu w magazynie kluczy.  

Jeśli ustawiono zasady dotyczące certyfikatów automatycznego odnawiania, powiadomienie jest wysyłane następujących zdarzeń.  

-   Odnowienia certyfikatów
-   Po odnowieniu certyfikatu, podając, jeśli certyfikat został pomyślnie odnowiony, lub jeśli wystąpił błąd, wymagających ręczne odnowienie certyfikatu.  

 Jeśli ustawiono zasady dotyczące certyfikatów można ręcznie odnowione (tylko do wiadomości e-mail), a następnie powiadomienie jest wysyłane, gdy nadszedł czas, aby odnowić certyfikat.  

### <a name="certificate-access-control"></a>Kontrola dostępu do certyfikatu

 Kontrola dostępu do certyfikatów jest zarządzana przez usługę Key Vault i są dostarczane przez usługę Key Vault, która zawiera te certyfikaty. Zasady kontroli dostępu w przypadku certyfikatów różni się od zasad kontroli dostępu do kluczy i wpisów tajnych w tej samej usłudze Key Vault. Użytkownicy mogą tworzyć magazyny co najmniej jeden do przechowywania certyfikatów do utrzymania segmentacji odpowiedni scenariusz i zarządzania certyfikatami.  

 Następujące uprawnienia mogą służyć na zasadzie na jednostkę w wpisu kontroli dostępu wpisami tajnymi magazynu kluczy i ściśle wstecznych dozwolone w obiekcie wpisu tajnego operacje:  

- Uprawnienia dla operacje zarządzania certyfikatami
  - *Pobierz*: pobieranie bieżącej wersji certyfikatu lub dowolna wersja certyfikatu 
  - *Lista*: Lista certyfikatów bieżącego lub wersje certyfikatu  
  - *Aktualizuj*: zaktualizuj certyfikat
  - *Utwórz*: Utwórz certyfikat z usługi Key Vault
  - *Importowanie*: importowanie certyfikatu materiały do certyfikatem usługi Key Vault
  - *Usuń*: Usuń certyfikat, jego zasad i wszystkich jego wersji  
  - *Odzyskaj*: odzyskiwanie usunięto certyfikat
  - *kopii zapasowej*: tworzenie kopii zapasowej certyfikatu w magazynie kluczy
  - *Przywróć*: Przywracanie kopii zapasowej certyfikatu do magazynu kluczy
  - *managecontacts*: kontaktów certyfikatu zarządzania usługi Key Vault  
  - *manageissuers*: wystawców urzędów certyfikatów zarządzania usługi Key Vault
  - *getissuers*: Pobierz urzędy/wystawców certyfikatów
  - *listissuers*: listy urzędów/wystawców certyfikatów  
  - *setissuers*: Tworzenie lub aktualizowanie urzędów/wystawcy certyfikatu usługi Key Vault  
  - *deleteissuers*: Usuń urzędy/wystawcy certyfikatu usługi Key Vault  
 
- Uprawnienia dla uprzywilejowanych operacji korzystających z
  - *Przeczyść*: wyczyść (trwałe usunięcie) usunięto certyfikat

Aby uzyskać więcej informacji, zobacz [certyfikatu operacje w dokumentacji interfejsu API REST usługi Key Vault](/rest/api/keyvault). Aby uzyskać informacje na ustanawianie uprawnień, zobacz [magazynów — Utwórz lub zaktualizuj](/rest/api/keyvault/vaults/createorupdate) i [magazynów — zasady dostępu do aktualizacji](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="azure-storage-account-key-management"></a>Usługa Azure zarządzania kluczami konta magazynu

Usługa Key Vault można zarządzać kluczy konta magazynu platformy Azure:

- Wewnętrznie usługa Key Vault można wyświetlić listę kluczy (synchronizacja) za pomocą konta usługi Azure storage. 
- Ponownie generuje usługi Key Vault (rotuje) okresowo kluczy.
- Wartości kluczy nigdy nie są zwracane w odpowiedzi na obiekt wywołujący.
- Usługa Key Vault zarządza klucze kont magazynu i klasycznych kont magazynu.

Aby uzyskać więcej informacji, zobacz [kluczy konta magazynu dla klucza magazynu, Azure](key-vault-ovw-storage-keys.md)

### <a name="storage-account-access-control"></a>Kontrola dostępu do konta magazynu

Następujące uprawnienia mogą służyć podczas autoryzowania użytkownika lub główną aplikację do wykonywania operacji na koncie magazynu zarządzanego:  

- Uprawnienia dla konta magazynu zarządzanego i operacje definicja sygnatury dostępu współdzielonego
  - *Pobierz*: pobiera informacje o koncie magazynu 
  - *Lista*: wyświetlanie listy kont magazynu zarządzanych przez usługę Key Vault
  - *Aktualizuj*: Aktualizacja konta magazynu
  - *Usuń*: usuwanie konta magazynu  
  - *Odzyskaj*: odzyskać usunięte konto magazynu
  - *kopii zapasowej*: tworzenie kopii zapasowej konta magazynu
  - *Przywróć*: Przywracanie na koncie magazynu kopii zapasowych do usługi Key Vault
  - *Ustaw*: Tworzenie lub aktualizowanie konta magazynu
  - *regeneratekey*: Wygeneruj ponownie określoną wartością klucza konta magazynu
  - *getsas*: Uzyskaj informacje o definicji sygnatury dostępu Współdzielonego dla konta magazynu
  - *listsas*: Lista magazynu definicje sygnatur dostępu Współdzielonego dla konta magazynu
  - *deletesas*: Usuń definicję sygnatury dostępu Współdzielonego z konta magazynu
  - *setsas*: Utwórz lub zaktualizuj nowe sygnatury dostępu Współdzielonego definicji/atrybuty dla konta magazynu

- Uprawnienia dla uprzywilejowanych operacji korzystających z
  - *Przeczyść*: wyczyść (trwałe usunięcie) konto magazynu zarządzanego

Aby uzyskać więcej informacji, zobacz [operacje kontem magazynu w dokumentacji interfejsu API REST usługi Key Vault](/rest/api/keyvault). Aby uzyskać informacje na ustanawianie uprawnień, zobacz [magazynów — Utwórz lub zaktualizuj](/rest/api/keyvault/vaults/createorupdate) i [magazynów — zasady dostępu do aktualizacji](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="see-also"></a>Zobacz też

- [Uwierzytelnianie, żądań i odpowiedzi](authentication-requests-and-responses.md)
- [Wersje usługi Key Vault](key-vault-versions.md)
- [Przewodnik dewelopera magazynu kluczy](/azure/key-vault/key-vault-developers-guide)
