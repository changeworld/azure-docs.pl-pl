---
title: Temat kluczy, wpisów tajnych i certyfikatów
description: Omówienie interfejsu REST i KV szczegóły oferty developer
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: abd1b743-1d58-413f-afc1-d08ebf93828a
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: 77675b3c0b2ed9fcdb923c92638384d215bddc40
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972404"
---
# <a name="about-keys-secrets-and-certificates"></a>Informacje o kluczach, wpisów tajnych i certyfikatów
Usługa Azure Key Vault umożliwia użytkownikom do przechowywania i korzystania z kluczy kryptograficznych w środowisku Microsoft Azure. Key Vault obsługuje wiele typów kluczy i algorytmów i umożliwia użycie sprzętowych modułów zabezpieczeń (HSM) o wysokiej wartości kluczy. Ponadto usługi Key Vault umożliwia użytkownikom bezpieczne przechowywanie wpisów tajnych. Klucze tajne są obiektami octet ograniczony rozmiar z nie określonej semantyką. Usługa Key Vault obsługuje także certyfikaty, które są oparte na kluczach i wpisach tajnych i dodać funkcję automatycznego odnawiania.

Aby uzyskać więcej ogólnych informacji na temat usługi Azure Key Vault, zobacz [co to jest usługa Azure Key Vault?](/azure/key-vault/key-vault-whatis)

**Ogólne informacje usługi Key Vault**

-   [Obsługa standardów](about-keys-secrets-and-certificates.md#BKMK_Standards)
-   [Typy danych](about-keys-secrets-and-certificates.md#BKMK_DataTypes)  
-   [Obiekty, identyfikatory i przechowywania wersji](about-keys-secrets-and-certificates.md#BKMK_ObjId)  

**Klucze — informacje**

-   [Klucze i typy kluczy](about-keys-secrets-and-certificates.md#BKMK_KeyTypes)  
-   [Algorytmy RSA](about-keys-secrets-and-certificates.md#BKMK_RSAAlgorithms)  
-   [Algorytmy RSA modułu HSM](about-keys-secrets-and-certificates.md#BKMK_RSA-HSMAlgorithms)  
-   [Kryptograficzna ochrona](about-keys-secrets-and-certificates.md#BKMK_Cryptographic)
-   [Kluczowe operacje](about-keys-secrets-and-certificates.md#BKMK_KeyOperations)  
-   [Kluczowych atrybutów](about-keys-secrets-and-certificates.md#BKMK_KeyAttributes)  
-   [Klucz tagów](about-keys-secrets-and-certificates.md#BKMK_Keytags)  

**Dotyczące wpisów tajnych** 

-   [Praca z wpisami tajnymi](about-keys-secrets-and-certificates.md#BKMK_WorkingWithSecrets)  
-   [Atrybuty wpisu tajnego](about-keys-secrets-and-certificates.md#BKMK_SecretAttrs)  
-   [Tagi wpisu tajnego](about-keys-secrets-and-certificates.md#BKMK_SecretTags)  
-   [Kontrola dostępu do kluczy tajnych](about-keys-secrets-and-certificates.md#BKMK_SecretAccessControl)  

**Dotyczących certyfikatów**

-   [Tworzenie certyfikatu](#BKMK_CompositionOfCertificate)  
-   [Certyfikat atrybuty i znaczniki](#BKMK_CertificateAttributesAndTags)  
-   [Zasady dotyczące certyfikatów](#BKMK_CertificatePolicy)  
-   [Wystawca certyfikatu](#BKMK_CertificateIssuer)  
-   [Kontakty certyfikatu](#BKMK_CertificateContacts)  
-   [Kontrola dostępu do certyfikatu](#BKMK_CertificateAccessControl)  

--

## <a name="key-vault-general-details"></a>Ogólne informacje usługi Key Vault

Poniższe sekcje zapewniają ogólne informacje dotyczące dla wdrożenia usługi Azure Key Vault.

###  <a name="BKMK_Standards"></a> Obsługa standardów

JavaScript Object Notation (JSON) i specyfikacje JavaScript obiektu podpisywania i szyfrowania (blog JOSE) są ważne informacje.  

-   [Klucza internetowego JSON (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [Szyfrowanie sieci Web JSON (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [Algorytmy sieci Web JSON (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [Sygnatura sieci Web JSON (JWS)](http://tools.ietf.org/html/draft-ietf-jose-json-web-signature)  

### <a name="BKMK_DataTypes"></a> typy danych

Zapoznaj się [specyfikacje JOSE](#BKMK_Standards) dla typów danych odpowiednich dla kluczy szyfrowania i podpisywania.  

-   **Algorytm** -obsługiwany algorytm dla kluczowych operacji, na przykład RSA1_5  
-   **wartość tekstu szyfrowanego** -cipher oktetów tekstu, kodowane w formacie Base64URL  
-   **wartość skrótu** — dane wyjściowe algorytmu wyznaczania wartości skrótu, kodowane w formacie Base64URL  
-   **Typ klucza** — jeden z obsługiwanych typów kluczy, na przykład RSA.  
-   **wartości w postaci zwykłego tekstu** -oktetów zwykłego tekstu, kodowane w formacie Base64URL  
-   **wartość podpisu** — dane wyjściowe algorytmu podpisu, kodowane w formacie Base64URL  
-   **base64URL** -Base64URL [RFC4648] zakodowana wartość binarna  
-   **wartość logiczna** — wartość PRAWDA lub FAŁSZ  
-   **Tożsamość** — tożsamości z usługi Azure Active Directory (AAD).  
-   **IntDate** — wartość dziesiętną JSON reprezentujący liczbę sekund od 1970-01-01T0:0:0Z UTC do daty/godziny określonej UTC. Zobacz temat [RFC3339] Aby uzyskać szczegółowe informacje dotyczące dat/godzin ogólnie rzecz biorąc i czasem UTC, w szczególności.  

###  <a name="BKMK_ObjId"></a> Obiekty, identyfikatory i przechowywania wersji

Obiekty przechowywane w usłudze Azure Key Vault przechowywać wersje zawsze wtedy, gdy tworzone jest nowe wystąpienie obiektu, a każda wersja ma unikatowy identyfikator i adres URL. W chwili utworzenia obiektu jest podany identyfikator unikatowy wersji i jest oznaczony jako bieżąca wersja obiektu. Tworzenie nowego wystąpienia o takiej samej nazwie obiektu zapewnia nowy obiekt o identyfikatorze wersji unikatowy i powoduje, że przestanie bieżącej wersji.  

Obiekty w usłudze Azure Key Vault może zostać zlikwidowane przy użyciu bieżącego identyfikatora lub identyfikator specyficzny dla wersji. Na przykład biorąc pod uwagę klucz o nazwie "MasterKey", wykonywanie operacji z bieżącym identyfikatorem sprawia, że system użyj najnowszej dostępnej wersji. Wykonywanie operacji na wartościach identyfikator specyficzny dla wersji powoduje, że system ma używać tej wersji określonego obiektu.  

Obiekty są identyfikowane w usłudze Azure Key Vault przy użyciu adresu URL w taki sposób, że żadne dwa obiekty w systemie, niezależnie od lokalizacji geograficznej, mają ten sam adres URL. Pełny adres URL do obiektu jest nazywany identyfikator obiektu i składa się z część prefiks, który identyfikuje usługę Key Vault, typ obiektu, podawana przez użytkownika nazwa obiektu i wersji obiektów. Nazwa obiektu jest rozróżniana wielkość liter i niezmienne. Identyfikatory, które nie zawierają wersja obiektu są określane jako podstawa identyfikatorów.  

Aby uzyskać więcej informacji, zobacz [uwierzytelniania, żądań i odpowiedzi](authentication-requests-and-responses.md)

Identyfikator obiektu ma następujący format Ogólne:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Gdzie:  

|||  
|-|-|  
|`keyvault-name`|Nazwa magazynu kluczy w usłudze Microsoft Azure Key Vault.<br /><br /> Nazwy usługi Key Vault są wybierane przez użytkownika i są globalnie unikatowe.<br /><br /> Nazwa magazynu kluczy musi być ciągu od 3 do 24 znaków długości, zawierający tylko (0-9, a – z, A-Z, a -).|  
|`object-type`|Typ obiektu, "klucze" lub "klucze tajne".|  
|`object-name`|`object-name` Jest dostarczone przez użytkownika nazwę, a musi być unikatowa w obrębie usługi Key Vault. Nazwa musi być ciągiem 1 do 127 znaków długości, zawierający tylko 0-9, a – z, A-Z, - i.|  
|`object-version`|`object-version` Jest generowane przez system, identyfikator ciągu 32 znaków, który opcjonalnie jest wykorzystywana do adresowania unikatową wersję obiektu.|  

## <a name="key-vault-keys"></a>Klucze usługi Key Vault

###  <a name="BKMK_KeyTypes"></a> Klucze i typy kluczy

Klucze szyfrowania w usłudze Azure Key Vault jest reprezentowane przez obiekty klucza internetowego JSON [JWK]. Podstawowej specyfikacji JWK/JWA również zostają rozszerzone, aby włączyć typy kluczy, które są unikatowe dla wdrożenia usługi Azure Key Vault, na przykład importowanie kluczy w usłudze Azure Key Vault przy użyciu pakietu określonego dostawcy (firmy Thales) przez moduł HSM w celu udostępnienia bezpieczne przenoszenie kluczy, takie tego mogą one można używać tylko w HSM usługi Azure Key Vault.  

Początkowa wersja usługi Azure Key Vault obsługuje tylko klucze RSA przyszłe wersje może obsługiwać inne typy kluczy, takie jak krzywej symetrycznego i korzenia.  

-   **RSA**: 2048-bitowego klucza RSA. Jest to klucz "elastyczne", który jest przetwarzany w oprogramowaniu, za pomocą usługi Key Vault, ale jest przechowywany jako zaszyfrowany za pomocą klucz systemu, który znajduje się w sprzętowym module zabezpieczeń. Klienci mogą być Importuj istniejący klucz RSA lub żądania usługi Azure Key Vault wygenerowanie takiego.  
-   **RSA HSM**: klucz RSA, który jest przetwarzany w module HSM. Klucze RSA HSM są chronione w jednym z środowiska Azure klucza magazynu przez sprzętowy moduł zabezpieczeń Security World (Brak środowiska zabezpieczeń Security World dla lokalizacji geograficznej w celu zachowania izolacji). Klienci mogą być Importowanie klucza RSA w formie nietrwałego lub eksportowanie z zgodnego urządzenia sprzętowego modułu zabezpieczeń lub żądania usługi Azure Key Vault wygenerowanie takiego. Ten typ klucza dodaje atrybut T JWK pobrać do przenoszenia materiału klucza sprzętowego modułu zabezpieczeń.  

     Aby uzyskać więcej informacji o granicach geograficznych, zobacz [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/privacy/)  

###  <a name="BKMK_RSAAlgorithms"></a> Algorytmy RSA  
 Następujące identyfikatory algorytm są obsługiwane przy użyciu kluczy RSA w usłudze Azure Key Vault.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, SZYFROWANIA/ODSZYFROWYWANIA

-   **RSA1_5** — szyfrowanie klucza RSAES PKCS1 V1_5 [RFC3447]  
-   **RSA OAEP** — RSAES optymalne asymetrycznego szyfrowania dopełnienie (OAEP) [RFC3447], przy użyciu parametrów domyślnych, określony przez 3447 RFC w sekcji A.2.1. Te domyślne parametry za pomocą funkcji skrótu SHA-1 i funkcja generowania maski MGF1 SHA-1.  

#### <a name="signverify"></a>SPRAWDŹ/LOGOWANIA

-   **RS256** — RSASSA-PKCS-v1_5 przy użyciu algorytmu SHA-256. Wartość skrótu dostarczona aplikacja musi być obliczane przy użyciu algorytmu SHA-256 i musi być 32 bajtów długości.  
-   **RS384** — RSASSA-PKCS-v1_5 przy użyciu algorytmu SHA-384. Wartość skrótu dostarczona aplikacja musi być obliczane przy użyciu algorytmu SHA-384 i musi być 48 bajtów długości.  
-   **RS512** — RSASSA-PKCS-v1_5 przy użyciu algorytmu SHA-512. Wartość skrótu dostarczona aplikacja musi być obliczane przy użyciu algorytmu SHA-512 i muszą być 64 bajtów długości.  
-   **RSNULL** — Zobacz [RFC2437] wyspecjalizowane przypadek użycia niektórych scenariuszy TLS.  

###  <a name="BKMK_RSA-HSMAlgorithms"></a> Algorytmy RSA modułu HSM  
Następujące identyfikatory algorytm są obsługiwane przy użyciu kluczy RSA modułu HSM w usłudze Azure Key Vault.  

### <a name="BKMK_Cryptographic"></a> Kryptograficzna ochrona

Modułów kryptograficznych, które korzysta z usługi Azure Key Vault, czy oprogramowania, lub przez moduł HSM są zweryfikowane w trybie FIPS. Nie trzeba podejmować żadnych działań specjalne do uruchamiania w trybie FIPS. Jeśli możesz **tworzenie** lub **zaimportować** klucze jako chroniony przez moduł HSM, mają gwarancję, że do przetworzenia wewnątrz sprzętowych modułów zabezpieczeń zweryfikowane FIPS 140-2 poziom 2 lub nowszej. Jeśli użytkownik **tworzenie** lub **zaimportować** klucze jako chronionego przez oprogramowanie, a następnie są przetwarzane wewnątrz modułów kryptograficznych służących do sprawdzania poprawności zgodnych ze standardami FIPS 140-2 poziom 1 lub nowszym. Aby uzyskać więcej informacji, zobacz [kluczy i typy kluczy](about-keys-secrets-and-certificates.md#BKMK_KeyTypes).

#### <a name="wrapunwrap-encryptdecrypt"></a>OPAKOWYWANIE/ODPAKOWYWANIA, SZYFROWANIA/ODSZYFROWYWANIA

-   **RSA1_5** -RSAES PKCS1 V1_5 [RFC3447] klucza szyfrowania.  
-   **RSA OAEP** — RSAES optymalne asymetrycznego szyfrowania dopełnienie (OAEP) [RFC3447], przy użyciu parametrów domyślnych, określony przez 3447 RFC w sekcji A.2.1. Te domyślne parametry za pomocą funkcji skrótu SHA-1 i funkcja generowania maski MGF1 SHA-1.  

 #### <a name="signverify"></a>SPRAWDŹ/LOGOWANIA  

-   **RS256** — RSASSA-PKCS-v1_5 przy użyciu algorytmu SHA-256. Wartość skrótu dostarczona aplikacja musi być obliczane przy użyciu algorytmu SHA-256 i musi być 32 bajtów długości.  
-   **RS384** — RSASSA-PKCS-v1_5 przy użyciu algorytmu SHA-384. Wartość skrótu dostarczona aplikacja musi być obliczane przy użyciu algorytmu SHA-384 i musi być 48 bajtów długości.  
-   **RS512** — RSASSA-PKCS-v1_5 przy użyciu algorytmu SHA-512. Wartość skrótu dostarczona aplikacja musi być obliczane przy użyciu algorytmu SHA-512 i muszą być 64 bajtów długości.  
-   RSNULL: Zobacz [RFC2437] wyspecjalizowane przypadek użycia niektórych scenariuszy TLS.  

###  <a name="BKMK_KeyOperations"></a> Kluczowe operacje

Usługa Azure Key Vault obsługuje następujące operacje na obiektach klucza:  

-   **Utwórz**: umożliwia klientowi utworzyć klucz w usłudze Azure Key Vault. Wartość klucza jest generowany przez usługę Azure Key Vault i przechowywane i nie jest wydawane dla klienta. Asymetryczny (i w przyszłości krzywej eliptycznej i Symmetric) mogą zostać utworzone w usłudze Azure Key Vault.  
-   **Importowanie**: umożliwia klientowi zaimportować istniejący klucz do usługi Azure Key Vault. Asymetryczny (i w przyszłości krzywej eliptycznej i Symmetric) kluczy może je zaimportować do usługi Azure Key Vault przy użyciu szeregu metod tworzenia różnych pakietów w konstrukcji JWK.  
-   **Aktualizacja**: umożliwia klientowi z wystarczającymi uprawnieniami do modyfikowania metadanych (atrybutów kluczy) skojarzony z kluczem, który został wcześniej są przechowywane w usłudze Azure Key Vault.  
-   **Usuń**: umożliwia klientowi z wystarczającymi uprawnieniami do usunięcia klucza z usługi Azure Key Vault.  
-   **Lista**: umożliwia klientowi wyświetlić listę wszystkich kluczy w danej usłudze Azure Key Vault.  
-   **Lista wersji**: umożliwia klientowi wyświetlić listę wszystkich wersji danego klucza w danej usłudze Azure Key Vault.  
-   **Pobierz**: umożliwia klientowi pobrać publiczny części danego klucza w usłudze Azure Key Vault.  
-   **Kopia zapasowa**: eksportuje klucza w postaci chronionych.  
-   **Przywróć**: importuje wcześniej kopii zapasowej klucza.  

Aby uzyskać więcej informacji, zobacz [klucza operacje](/rest/api/keyvault/key-operations).  

Po utworzeniu klucz w usłudze Azure Key Vault może wykonać następujące operacje kryptograficzne przy użyciu klucza:  

-   **Logowanie i sprawdź, czy**: ściśle, ta operacja jest "skrót znaku" lub "zweryfikował skrótu", ponieważ usługa Azure Key Vault nie obsługuje wyznaczania wartości skrótu zawartości w ramach tworzenia podpisu. Aplikacje powinny skrótu danych można być podpisany lokalnie, a następnie zażądać tego logowania usługi Azure Key Vault skrót. Weryfikacji podpisanego skróty jest obsługiwany jako operacja jako udogodnienie dla aplikacji, które mogą nie mieć dostępu do materiału klucza [publiczny]; Firma Microsoft zaleca, aby uzyskać najlepszą wydajność aplikacji, sprawdź, czy operacje są wykonywane lokalnie.  
-   **Klucz szyfrowania / zawijanie**: klucza przechowywanego w usłudze Azure Key Vault może służyć do ochrony klucza inny, zwykle klucza szyfrowania symetrycznego zawartości (CEK). Po asymetrycznego klucz w usłudze Azure Key Vault jest używane szyfrowanie klucza, na przykład RSA OAEP i WRAPKEY/UNWRAPKEY operacje, które są równoważne szyfrowania/ODSZYFROWYWANIA. Po symetrycznego klucza w usłudze Azure Key Vault jest używany klucz zawijania; na przykład AES KW. Operacja WRAPKEY jest obsługiwana jako udogodnienie dla aplikacji, które mogą nie mieć dostępu do materiału klucza [publiczny]; zalecane jest, aby uzyskać najlepszą wydajność aplikacji, WRAPKEY operacje są wykonywane lokalnie.  
-   **Szyfrowanie i odszyfrowywanie**: klucza przechowywanego w usłudze Azure Key Vault może służyć do szyfrowania i odszyfrowywania jednego bloku danych, której wielkość zależy od typu klucza i algorytm szyfrowania wybranych. Operacja szyfrowania jest dostarczany jako udogodnienie dla aplikacji, które mogą nie mieć dostępu do materiału klucza [publiczny]; zalecane jest, aby uzyskać najlepszą wydajność aplikacji, szyfrowanie lokalnie można wykonać operacji.  

Gdy WRAPKEY/UNWRAPKEY przy użyciu kluczy asymetrycznych może wydawać się zbędne, ponieważ operacja jest odpowiednikiem szyfrowania/ODSZYFROWYWANIA, korzystanie z różnych operacji jest uznawany za istotne dla zapewnienia semantycznego i autoryzacji rozdzielenie tych operacji i spójność gdy inne typy kluczy są obsługiwane przez usługę.  

Usługa Azure Key Vault nie obsługuje operacji EKSPORTOWANIA: po zainicjowaniu obsługi administracyjnej klucza w systemie nie można wyodrębnić lub zmodyfikować jego materiał klucza.  Jednak użytkowników usługi Azure Key Vault, który może wymagać swój klucz dla innych przypadków użycia lub po usunięciu w usłudze Azure Key Vault może używać operacji kopii zapasowej i przywracania eksportu/importu klucza w postaci chronionych. Kluczy utworzonych przez Operacja tworzenia kopii zapasowej nie są użyteczne spoza usługi Azure Key Vault. Alternatywnie operacja IMPORTOWANIA może służyć względem wielu wystąpień usługi Azure Key Vault.  

Użytkownicy mogą ograniczać dowolne operacje kryptograficzne, które obsługuje usługi Azure Key Vault, na podstawie-key, przy użyciu właściwości key_ops JWK obiektu.  

Aby uzyskać więcej informacji na temat JWK obiektów, zobacz [klucza sieci Web JSON (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key).  

###  <a name="BKMK_KeyAttributes"></a> Kluczowych atrybutów

Oprócz materiał klucza można określić następujące atrybuty. Żądania JSON, słowo kluczowe atrybuty i nawiasów klamrowych "{" "}", są wymagane, nawet gdy pojawią się nie określone atrybuty.  

- *włączone*: wartość logiczna, opcjonalna, wartością domyślną jest **true**. Określa, czy klucz jest włączone i niemożliwe do operacji kryptograficznych. *Włączone* atrybut jest używany w połączeniu z *nbf* i *exp*. Podczas operacji odbywa się między *nbf* i *exp*, będzie można dopuścić tylko, jeśli *włączone* jest ustawiona na **true**. Operacje poza *nbf* / *exp* okno automatycznie są niedozwolone, z wyjątkiem niektórych typów operacji w ramach [szczególnych warunków](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops).
- *NBF*: IntDate, opcjonalne, domyślnie teraz wynosi. *Nbf* (nie wcześniej niż) atrybut określa czas, przed którym klucz nie może być używany dla operacji kryptograficznych, z wyjątkiem niektórych typów operacji w ramach [szczególnych warunków](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops). Przetwarzanie *nbf* atrybut wymaga, że bieżąca data/godzina musi przypadać po lub nie ma wartości — przed daty/godziny na liście *nbf* atrybutu. Usługa Azure Key Vault może przewidzieć kilka małych swobodę zwykle nie więcej niż kilka minut, na wypadek zegara pochylenia. Jego wartość musi być liczbą zawierającą wartość IntDate.  
- *EXP*: IntDate, opcjonalnie, wartość domyślna to "nieskończoność". *Exp* atrybut (czas wygaśnięcia) określa czas wygaśnięcia r. lub później, którego klucz nie może służyć do operacji kryptograficznych, z wyjątkiem niektórych typów operacji w ramach [szczególnych warunków](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops). Przetwarzanie *exp* atrybut wymaga bieżącej daty/godziny należy przed Data/godzina wygaśnięcia na liście *exp* atrybutu. Usługa Azure Key Vault może przewidzieć kilka małych swobodę zwykle nie więcej niż kilka minut, na wypadek zegara pochylenia. Jego wartość musi być liczbą zawierającą wartość IntDate.  

Istnieją dodatkowe atrybuty tylko do odczytu, które są objęte żadnej odpowiedzi, która obejmuje kluczowych atrybutów:  

- *utworzone*: IntDate opcjonalne. *Utworzone* atrybut wskazuje, kiedy utworzono tę wersję klucza. Ta wartość jest pusta dla kluczy utworzonych przed dodaniem tego atrybutu. Jego wartość musi być liczbą zawierającą wartość IntDate.  
- *Zaktualizowano*: IntDate opcjonalne. *Zaktualizowane* atrybut wskazuje, kiedy ta wersja klucza została zaktualizowana. Ta wartość jest pusta dla kluczy, które zostały ostatnio zaktualizowane przed dodaniem tego atrybutu. Jego wartość musi być liczbą zawierającą wartość IntDate.  

Aby uzyskać więcej informacji na temat IntDate a innymi typami danych, zobacz [typy danych](about-keys-secrets-and-certificates.md#BKMK_DataTypes)  

#### <a name="BKMK_key-date-time-ctrld-ops"></a> Data i godzina kontrolowane operacji

Nie zostały jeszcze nieprawidłowe lub wygasłe klucze, poza tymi *nbf* / *exp* okna, będzie działać w przypadku **odszyfrować**, **Odkodowywanie** i **Sprawdź** operacji (nie zwraca 403, zabronione). Uzasadnienie wykorzystania nie zostały jeszcze prawidłowego stanu jest Zezwalaj na klucz ma zostać przetestowana przed użycia w środowisku produkcyjnym. Racjonalne uzasadnienie przy użyciu stanu wygaśnięcia jest Zezwalaj na operacje odzyskiwania danych, który został utworzony, gdy klucz był nieprawidłowy. Ponadto, możesz wyłączyć dostęp do klucza przy użyciu zasad usługi Key Vault lub aktualizując *włączone* atrybutu klucza do **false**.

Aby uzyskać więcej informacji na temat danych typów, zobacz [typy danych](about-keys-secrets-and-certificates.md#BKMK_DataTypes).

Więcej informacji na temat innych możliwych atrybutów, zobacz [klucza sieci Web JSON (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key).

### <a name="BKMK_Keytags"></a> Klucz tagów

Można określić dodatkowe metadane specyficzne dla aplikacji w postaci tagów. Usługa Azure Key Vault obsługuje maksymalnie 15 tagów, z których każdy może mieć nazwy 256 znaków i wartości 256 znaków.  

>[!Note]
>Tagi są odczytany przez obiekt wywołujący, jeśli mają one *listy* lub *uzyskać* uprawnienia do tego typu obiektu; kluczy, wpisów tajnych lub certyfikatów.

###  <a name="BKMK_KeyAccessControl"></a> Kontrola dostępu do kluczy

Kontrola dostępu do kluczy zarządzanych przez usługę Key Vault znajduje się na poziomie usługi Key Vault, który działa jako kontener kluczy. Brak zasad kontroli dostępu do kluczy, który różni się od zasad kontroli dostępu dotyczących wpisów tajnych w tej samej usłudze Key Vault. Użytkownicy mogą utworzyć co najmniej jeden magazynów do przechowywania kluczy i są zobowiązane do zachowania segmentacji odpowiedni scenariusz i zarządzanie kluczami. Kontrola dostępu do kluczy jest niezależna od kontroli dostępu do kluczy tajnych.  

Następujące uprawnienia mogą być udzielone na na użytkownika / usługi głównej podstawy w wpisu kontroli dostępu kluczy w magazynie. Te uprawnienia duplikatów ściśle dozwolone klucza obiektu operacje:  

-   *Utwórz*: tworzenie nowych kluczy
-   *Pobierz*: Odczyt publiczną część klucza, a także jego atrybuty
-   *Lista*: Utwórz listę kluczy lub wersji tego klucza, przechowywanych w magazynie kluczy
-   *Aktualizuj*: zaktualizować atrybutów dla klucza
-   *Usuń*: usuwanie klucza obiektu
-   *znak*: Użyj skróty służące klucza do podpisywania
-   *Sprawdź*: Sprawdź skróty przy użyciu klucza
-   *wrapKey*: ochrona klucza symetrycznego za pomocą klawisza
-   *unwrapKey*: Użyj klucza do usunięcia ochrony opakowane kluczy symetrycznych
-   *Szyfruj*: Użyj klucza do ochrony dowolnego sekwencji bajtów
-   *odszyfrowywanie*: Użyj klucza do usunięcia ochrony sekwencja bajtów
-   *Importowanie*: Importuj klucz do magazynu kluczy
-   *kopii zapasowej*: kopii zapasowej klucza w magazynie kluczy
-   *Przywróć*: Przywracanie kopii zapasowej klucza do magazynu kluczy
-   *wszystkie*: wszystkie uprawnienia

## <a name="key-vault-secrets"></a>Wpisy tajne usługi Key Vault 

###  <a name="BKMK_WorkingWithSecrets"></a> Praca z wpisami tajnymi

Klucze tajne w usłudze Azure Key Vault to sekwencje oktetu o maksymalnym rozmiarze 25 KB każdego. W usłudze Azure Key Vault nie zapewnia żadnych semantyki wpisów tajnych; go jedynie akceptuje dane, są szyfrowane i zapisuje go, zwracając identyfikator wpisu tajnego "id", który może służyć do pobierania klucza tajnego w późniejszym czasie.  

W przypadku wysoce poufnych danych klientów należy wziąć pod uwagę dodatkowych warstw ochrony danych, która jest przechowywana w usłudze Azure Key Vault; na przykład dane, szyfrując wstępnie przy użyciu oddzielnych ochrony klucza.  

Usługa Azure Key Vault obsługuje również pola contentType wpisów tajnych. Klienci mogą określić typ zawartości, właściwość "contentType", aby ułatwić interpretowanie poufne dane podczas pobierania klucza tajnego. Maksymalna długość tego pola to 255 znaków. Istnieją wstępnie zdefiniowane wartości. Sugerowane użycia jest jako wskazówka do interpretacji danych klucza tajnego. Na przykład implementacja może przechowywać hasła i certyfikaty jako wpisy tajne, a następnie użyj tego pola, aby wskazać, które. Istnieją wstępnie zdefiniowane wartości.  

###  <a name="BKMK_SecretAttrs"></a> Atrybuty wpisu tajnego

Oprócz tajnych danych można określić następujące atrybuty:  

- *EXP*: IntDate, opcjonalnie, wartością domyślną jest **nieskończona**. *Exp* atrybut (czas wygaśnięcia) określa czas wygaśnięcia r. lub później, który można pobrać tajnego danych nie może, z wyjątkiem [sytuacji, w szczególności](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops). Przetwarzanie *exp* atrybut wymaga bieżącej daty/godziny należy przed Data/godzina wygaśnięcia na liście *exp* atrybutu. Usługa Azure Key Vault może przewidzieć kilka małych swobodę zwykle nie więcej niż kilka minut, na wypadek zegara pochylenia. Jego wartość musi być liczbą zawierającą wartość IntDate.  
- *NBF*: IntDate, opcjonalnie, wartością domyślną jest **teraz**. *Nbf* (nie wcześniej niż) atrybut określa czas, przed którym nie można pobrać dane poufne, z wyjątkiem [sytuacji, w szczególności](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops). Przetwarzanie *nbf* atrybut wymaga, że bieżąca data/godzina musi przypadać po lub nie ma wartości — przed daty/godziny na liście *nbf* atrybutu. Usługa Azure Key Vault może przewidzieć kilka małych swobodę zwykle nie więcej niż kilka minut, na wypadek zegara pochylenia. Jego wartość musi być liczbą zawierającą wartość IntDate.  
- *włączone*: wartość logiczna, opcjonalna, wartością domyślną jest **true**. Ten atrybut określa, czy można pobrać tajnego danych. Atrybut włączony jest używany w połączeniu z i *exp* podczas operacji odbywa się między i exp, jej zostanie dopuszczona, jeśli włączone jest ustawiona na **true**. Operacje poza *nbf* i *exp* automatycznie są niedozwolone, z wyjątkiem w [sytuacji, w szczególności](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops).  

Istnieją dodatkowe atrybuty tylko do odczytu, które są objęte żadnej odpowiedzi, która zawiera atrybuty wpisu tajnego:  

- *utworzone*: IntDate opcjonalne. Utworzonego atrybutu wskazuje, kiedy utworzono tej wersji klucza tajnego. Ta wartość jest pusta dla wpisów tajnych utworzone przed dodaniem tego atrybutu. Jego wartość musi być liczbą zawierającą wartość IntDate.  
- *Zaktualizowano*: IntDate opcjonalne. Zaktualizowano atrybut wskazuje, gdy zaktualizowano tę wersję klucza tajnego. Ta wartość jest pusta dla wpisów tajnych, które zostały ostatnio zaktualizowane przed dodaniem tego atrybutu. Jego wartość musi być liczbą zawierającą wartość IntDate.

#### <a name="BKMK_secret-date-time-ctrld-ops"></a> Data i godzina kontrolowane operacji

Klucz tajny **uzyskać** operacji będzie działać w przypadku nie zostały jeszcze nieprawidłowe lub wygasłe wpisów tajnych oraz ich poza *nbf* / *exp* okna. Klucz tajny podczas wywoływania **uzyskać** nie zostały jeszcze prawidłowy klucz tajny, operacja może służyć do celów testowych. Trwa pobieranie (**uzyskać**ing) wygasły klucz tajny, może służyć do operacji odzyskiwania.

Aby uzyskać więcej informacji na temat danych typów, zobacz [typy danych](about-keys-secrets-and-certificates.md#BKMK_DataTypes).  

###  <a name="BKMK_SecretAccessControl"></a> Kontrola dostępu do kluczy tajnych

Kontrola dostępu do kluczy tajnych zarządzane w usłudze Azure Key Vault znajduje się na poziomie usługi Key Vault, który działa jako kontener tych kluczy tajnych. Brak zasad kontroli dostępu do kluczy tajnych różni się od zasad kontroli dostępu do kluczy w tej samej usłudze Key Vault. Użytkownicy mogą utworzyć co najmniej jeden magazynów do przechowywania wpisów tajnych i są zobowiązane do zachowania segmentacji odpowiedni scenariusz i zarządzania wpisami tajnymi. Kontrola dostępu do kluczy tajnych są niezależne od kontroli dostępu do kluczy.  

Następujące uprawnienia mogą być używane, na podstawie na jednostkę w wpisu kontroli dostępu wpisów tajnych w magazynie i dokładnie takie same jak stosowane dozwolone w obiekcie wpisu tajnego operacje:  

-   *Ustaw*: tworzenie nowych wpisów tajnych  
-   *Pobierz*: odczytu wpisu tajnego  
-   *Lista*: Utwórz listę kluczy tajnych lub wersji klucza tajnego przechowywanych w usłudze Key Vault  
-   *Usuń*: usuwanie klucza tajnego  
-   *wszystkie*: wszystkie uprawnienia  

Aby uzyskać więcej informacji na temat pracy z wpisy tajne, zobacz [operacje dotyczące wpisów tajnych](/rest/api/keyvault/secret-operations).  

###  <a name="BKMK_SecretTags"></a> Tagi wpisu tajnego  
Można określić dodatkowe metadane specyficzne dla aplikacji w postaci tagów. Usługa Azure Key Vault obsługuje maksymalnie 15 tagów, z których każdy może mieć nazwy 256 znaków i wartości 256 znaków.  

>[!Note]
>Tagi są odczytany przez obiekt wywołujący, jeśli mają one *listy* lub *uzyskać* uprawnienia do tego typu obiektu; kluczy, wpisów tajnych lub certyfikatów.

## <a name="key-vault-certificates"></a>Certyfikaty usługi Key Vault

Pomoc techniczna certyfikaty usługi Key Vault zapewnia zarządzanie x509 swoje certyfikaty i następujące zachowania:  

-   Umożliwia właściciela certyfikatu utworzyć certyfikat za pośrednictwem procesu tworzenia usługi Key Vault lub importowania istniejącego certyfikatu. Dotyczy to zarówno z podpisem własnym, a urząd certyfikacji generowanych certyfikaty.
-   Umożliwia właścicielowi certyfikatu usługi Key Vault, do zaimplementowania bezpieczne przechowywanie i zarządzania X509 certyfikaty bez interakcji z materiał klucza prywatnego.  
-   Umożliwia właściciela certyfikatu utworzyć zasadę, który kieruje Key Vault, aby zarządzać cyklem życia certyfikatu.  
-   Umożliwia właścicielom certyfikat, podaj informacje kontaktowe dla powiadomień dotyczących zdarzeń cyklu życia wygaśnięcia i odnawiania certyfikatu.  
-   Obsługuje automatycznego odnawiania z wystawców wybranych - partnera usługi Key Vault X509 certyfikatu dostawcy / urzędy certyfikacji.

>[!Note]
>Partnerstwo inne niż dostawcy/urzędy są również dozwolone, ale nie będzie obsługiwał funkcji automatycznego odnawiania.

###  <a name="BKMK_CompositionOfCertificate"></a> Tworzenie certyfikatu

Po utworzeniu certyfikatem usługi Key Vault, mogą być adresowane kluczem i wpisem tajnym są również tworzone o takiej samej nazwie. Klucz usługi Key Vault umożliwia wykonywanie operacji klucza i wpisu tajnego usługi Key Vault umożliwia pobieranie wartości certyfikat jako wpis tajny. Certyfikatem usługi Key Vault zawiera także x509 publicznego certyfikatu metadanych.  

Identyfikator i wersja certyfikatów jest podobny do kluczy i wpisów tajnych. Określoną wersję mogą być adresowane kluczem i wpisem tajnym, utworzony za pomocą wersji certyfikatu usługi Key Vault jest dostępna w odpowiedzi certyfikatu usługi Key Vault.
 
![Cetificates to złożone obiekty](media/azure-key-vault.png)

###  <a name="BKMK_CertificateExportableOrNonExportableKey"></a> Można eksportować lub nie można eksportować klucza

Po utworzeniu certyfikatem usługi Key Vault go można pobrać z adresowalnych wpisu tajnego z kluczem prywatnym w formacie PFX lub PEM Jeżeli zasady stosowane do utworzenia certyfikatu wskazane, że klucz jest eksportowalny. Jeżeli klucz, który ma być nieeksportowalnej zasad użyty do utworzenia certyfikatu usługi Key Vault, następnie klucz prywatny nie jest część wartości podczas pobierania jako klucz tajny.  

Mogą być adresowane klucz staje się bardziej przydatna przy użyciu nieeksportowalnej KV certyfikatów. Operacje mogą być adresowane klucz KV są mapowane z *keyusage* pole KV zasad certyfikat użyty do utworzenia certyfikatu KV.  

Obsługiwane są dwa rodzaje klucz — *RSA* lub *sprzętowego modułu zabezpieczeń RSA* za pomocą certyfikatów. Można eksportować jest dozwolona tylko w RSA, nie są obsługiwane przez moduł HSM RSA.  

###  <a name="BKMK_CertificateAttributesAndTags"></a> Certyfikat atrybuty i znaczniki

Oprócz metadanymi certyfikatu, mogą być adresowane klucz i mogą być adresowane klucz tajny, certyfikatem usługi Key Vault zawiera też atrybuty i znaczniki.  

#### <a name="attributes"></a>Atrybuty

Atrybuty mogą być adresowane klucza i wpisu tajnego tworzone podczas tworzenia certyfikatu KV są dublowane atrybuty certyfikatu.  

Certyfikatem usługi Key Vault ma następujące atrybuty:  

-   *włączone*: wartość logiczna, opcjonalna, wartością domyślną jest **true**. Ten atrybut można określić do wskazania, jeśli dane certyfikatu można pobrać jako poufne lub wykonywalny jako klucz. Jest on używany w połączeniu z *nbf* i *exp* podczas operacji odbywa się między *nbf* i *exp*, będzie można dopuścić tylko, jeśli włączona jest ustawiona na wartość true. Operacje poza *nbf* i *exp* okno automatycznie są niedozwolone.  

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
> Tagi są odczytany przez obiekt wywołujący, jeśli mają one *listy* lub *uzyskać* uprawnienia do tego typu obiektu; kluczy, wpisów tajnych lub certyfikatów.

###  <a name="BKMK_CertificatePolicy"></a> Zasady dotyczące certyfikatów

Zasady certyfikatów zawiera informacje dotyczące sposobu tworzenia i zarządzania cyklem życia certyfikatu KV. Po zaimportowaniu certyfikatu z kluczem prywatnym w magazynie kluczy, czytając x509 tworzone są zasady domyślne certyfikatu.  

Po utworzeniu certyfikatu KV od podstaw zasad musi były dostarczane do usługi Key Vault, aby poinformować go na temat tworzenia tej wersji certyfikatu KV lub kolejnej wersji certyfikatu KV. Po ustanowieniu zasad, nie jest wymagane w przypadku kolejnych operacji na tworzenie następnej wersji certyfikatu KV tworzenia.  

Istnieje tylko jedno wystąpienie zasad we wszystkich wersjach KV certyfikatu.  

Na wysokim poziomie zasad certyfikatów zawiera następujące informacje:  

-   X509 certyfikatu właściwości: zawiera nazwy podmiotu, nazwy alternatywnej podmiotu itp. użyty do utworzenia x509 żądania certyfikatu.  
-   Właściwości klucza: zawiera typ klucza, długość, który można eksportować klucza i ponowne użycie pola klucza. Te pola poinstruować magazynu kluczy na temat generowania klucza.  
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

###  <a name="BKMK_CertificateIssuer"></a> Wystawca certyfikatu

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

###  <a name="BKMK_CertificateContacts"></a> Kontakty certyfikatu

Kontakty certyfikatu zawierają informacji kontaktowych, aby wysyłać powiadomienia wyzwalane przez zdarzenia okresu istnienia certyfikatu. Informacje o kontaktach jest udostępniany wszystkie certyfikaty w magazynie kluczy. Powiadomienie jest wysyłane do wszystkich kontaktów określonego zdarzenia dla każdego certyfikatu w magazynie kluczy.  

Jeśli zasady dotyczące certyfikatów jest ustawiony na automatyczne odnawianie, powiadomienie jest wysyłane następujących zdarzeń.  

-   Odnowienia certyfikatów
-   Po odnowieniu certyfikatu, podając, jeśli certyfikat został pomyślnie odnowiony, lub jeśli wystąpił błąd, wymagających ręczne odnowienie certyfikatu.  

 Jeśli ustawiono zasady dotyczące certyfikatów można ręcznie odnowione (tylko do wiadomości e-mail), a następnie powiadomienie jest wysyłane, gdy nadszedł czas, aby odnowić certyfikat.  

###  <a name="BKMK_CertificateAccessControl"></a> Kontrola dostępu do certyfikatu

 Kontrola dostępu do certyfikatów jest zarządzana przez usługę Key Vault i znajduje się na poziomie usługi Key Vault, który działa jako kontener tych certyfikatów. Brak zasad kontroli dostępu w przypadku certyfikatów różni się od zasad kontroli dostępu do kluczy i wpisów tajnych w tej samej usłudze Key Vault. Użytkownicy mogą utworzyć co najmniej jeden magazynów do przechowywania certyfikatów i są zobowiązane do zachowania segmentacji odpowiedni scenariusz i zarządzania certyfikatami.  

 Następujące uprawnienia mogą służyć na zasadzie na jednostkę w wpisu kontroli dostępu wpisami tajnymi magazynu kluczy i ściśle wstecznych dozwolone w obiekcie wpisu tajnego operacje:  

-   *Pobierz*: umożliwia get bieżąca wersja certyfikatu lub dowolna wersja certyfikatu 
-   *Lista*: umożliwia listy certyfikatów bieżącego lub wersje certyfikatu  
-   *Usuń*: umożliwia usunięcie certyfikatu, jego zasad i wszystkich jego wersji  
-   *Utwórz*: umożliwia tworzenie z certyfikatem usługi Key Vault.  
-   *Importowanie*: umożliwia importowanie certyfikatów materiału do certyfikatu klucza magazynu.  
-   *Aktualizuj*: pozwala zaktualizować certyfikat.  
-   *manageconnects*: umożliwia zarządzanie kontaktów certyfikatu usługi Key Vault  
-   *getissuers*: umożliwia get wystawców certyfikatów  
-   *listissuers*: umożliwia lista wystawców certyfikatu  
-   *setissuers*: umożliwia tworzenie lub aktualizowanie wystawców certyfikatów usługi Key Vault  
-   *deleteissuers*: umożliwia usunięcie wystawcy certyfikatu usługi Key Vault  
-   *wszystkie*: umożliwia przyznanie wszystkich uprawnień  

## <a name="additional-information-for-certificates"></a>Dodatkowe informacje dotyczące certyfikatów

- [Certyfikaty i zasady](/rest/api/keyvault/certificates-and-policies)
- [Wystawców certyfikatów](/rest/api/keyvault/certificate-issuers)
- [Kontakty certyfikatu](/rest/api/keyvault/certificate-contacts)

## <a name="see-also"></a>Zobacz też

- [Uwierzytelnianie, żądań i odpowiedzi](authentication-requests-and-responses.md)
- [Wersje usługi Key Vault](key-vault-versions.md)
- [Przewodnik dewelopera magazynu kluczy](/azure/key-vault/key-vault-developers-guide)
