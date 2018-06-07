---
title: Temat kluczy, kluczy tajnych i certyfikatów
description: Przegląd interfejsu REST i szczegóły developer KV
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
ms.openlocfilehash: a6bd63598781a60c2dd717c07b96fcb498a67d30
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636610"
---
# <a name="about-keys-secrets-and-certificates"></a>Temat kluczy, kluczy tajnych i certyfikatów
Usługa Azure Key Vault umożliwia użytkownikom przechowywać i wykorzystywać kluczy kryptograficznych w środowisku Microsoft Azure. Magazyn kluczy obsługuje wiele typów kluczy i algorytmów i umożliwia korzystanie z sprzętowych modułów zabezpieczeń (HSM) dla kluczy o wysokiej wartości. Ponadto Key Vault umożliwia użytkownikom bezpieczne przechowywanie kluczy tajnych. Klucze tajne są obiektami octet ograniczony z nie szczególnych semantyki. Magazyn kluczy obsługuje także certyfikaty, które są oparte na kluczach i kluczach tajnych i dodać funkcję automatycznego odnawiania.

Aby uzyskać więcej ogólnych informacji na temat usługi Azure Key Vault, zobacz [co to jest usługa Azure Key Vault?](/azure/key-vault/key-vault-whatis)

**Ogólne informacje magazynie kluczy**

-   [Obsługa standardów](about-keys-secrets-and-certificates.md#BKMK_Standards)
-   [Typy danych](about-keys-secrets-and-certificates.md#BKMK_DataTypes)  
-   [Obiekty, identyfikatorów i kontroli wersji](about-keys-secrets-and-certificates.md#BKMK_ObjId)  

**Klucze — informacje**

-   [Klucze i typów kluczy](about-keys-secrets-and-certificates.md#BKMK_KeyTypes)  
-   [Algorytmy RSA](about-keys-secrets-and-certificates.md#BKMK_RSAAlgorithms)  
-   [Algorytmy RSA HSM](about-keys-secrets-and-certificates.md#BKMK_RSA-HSMAlgorithms)  
-   [Ochrona usług kryptograficznych](about-keys-secrets-and-certificates.md#BKMK_Cryptographic)
-   [Operacje kluczy](about-keys-secrets-and-certificates.md#BKMK_KeyOperations)  
-   [Atrybuty klucza](about-keys-secrets-and-certificates.md#BKMK_KeyAttributes)  
-   [Tagi klucza](about-keys-secrets-and-certificates.md#BKMK_Keytags)  

**Klucze tajne — informacje** 

-   [Praca z kluczy tajnych](about-keys-secrets-and-certificates.md#BKMK_WorkingWithSecrets)  
-   [Tajny atrybutów](about-keys-secrets-and-certificates.md#BKMK_SecretAttrs)  
-   [Tajny tagów](about-keys-secrets-and-certificates.md#BKMK_SecretTags)  
-   [Kontrola dostępu tajny](about-keys-secrets-and-certificates.md#BKMK_SecretAccessControl)  

**Dotyczących certyfikatów**

-   [Kompozycja certyfikatu](#BKMK_CompositionOfCertificate)  
-   [Atrybuty certyfikatu i tagów](#BKMK_CertificateAttributesAndTags)  
-   [Zasady certyfikatów](#BKMK_CertificatePolicy)  
-   [Wystawca certyfikatu](#BKMK_CertificateIssuer)  
-   [Kontakty certyfikatu](#BKMK_CertificateContacts)  
-   [Kontrola dostępu do certyfikatu](#BKMK_CertificateAccessControl)  

--

## <a name="key-vault-general-details"></a>Ogólne informacje magazynie kluczy

Poniższe sekcje zapewniają ogólne informacje dotyczące między wdrożenia usługi Azure Key Vault.

###  <a name="BKMK_Standards"></a> Obsługa standardów

JavaScript Object Notation (JSON) i specyfikacje JavaScript obiektu podpisywania i szyfrowania (JOSE) są ważne informacje.  

-   [Klucz sieci Web JSON (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [Szyfrowanie sieci Web JSON (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [Algorytmy sieci Web JSON (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [Podpis sieci Web JSON (JWS)](http://tools.ietf.org/html/draft-ietf-jose-json-web-signature)  

### <a name="BKMK_DataTypes"></a> Typy danych

Zapoznaj się [specyfikacje JOSE](#BKMK_Standards) dla typów danych dla kluczy szyfrowania i podpisywania.  

-   **Algorytm** -obsługiwany algorytm klucza operacji, na przykład RSA1_5  
-   **wartość tekstu szyfrowanego** -szyfrowania oktety tekstu, kodowane w formacie Base64URL  
-   **wartość skrótu** — dane wyjściowe algorytmu wyznaczania wartości skrótu, kodowane w formacie Base64URL  
-   **Typ klucza** — jeden z obsługiwanych typów kluczy, na przykład RSA.  
-   **wartości w postaci zwykłego tekstu** -oktety w postaci zwykłego tekstu, kodowane w formacie Base64URL  
-   **wartość podpisu** — wyjściowych algorytmu podpisu, kodowane w formacie Base64URL  
-   **base64URL** -Base64URL [RFC4648] zakodowana wartość binarna  
-   **wartość logiczna** — wartość PRAWDA lub FAŁSZ  
-   **Tożsamość** — tożsamości z usługi Azure Active Directory (AAD).  
-   **IntDate** — wartość dziesiętną JSON reprezentującą liczbę sekund z rokiem 1970-01-01T0:0:0Z UTC do określonego Data/Godzina (UTC). Zobacz [RFC3339] Aby uzyskać szczegółowe informacje dotyczące daty/godziny na ogół i czasu UTC, w szczególności.  

###  <a name="BKMK_ObjId"></a> Obiekty, identyfikatory i kontroli wersji

Obiekty przechowywane w usłudze Azure Key Vault zachować wersje zawsze, gdy tworzone jest nowe wystąpienie obiektu, a każda wersja ma unikatowy identyfikator i adres URL. Podczas tworzenia obiektu jest podawany identyfikator unikatowy wersji i jest oznaczony jako bieżąca wersja obiektu. Utworzenie nowego wystąpienia o takiej samej nazwie obiektu zapewnia nowy obiekt o identyfikatorze wersji unikatowy i powoduje go w bieżącej wersji.  

Obiekty w usłudze Azure Key Vault może zostać zlikwidowane przy użyciu bieżącego identyfikatora lub identyfikatora określonej wersji. Na przykład danego klucza o nazwie "Umożliwia", wykonywanie operacji z bieżącym identyfikatorem powoduje, że system, aby korzystać z najnowszej dostępnej wersji. Wykonywanie operacji o identyfikatorze wersji powoduje, że używanie tej wersji określonego obiektu w systemie.  

Obiekty są identyfikowane w ramach usługi Azure Key Vault przy użyciu adresu URL w taki sposób, że nie dwa obiekty w systemie, niezależnie od lokalizacji geograficznej, nie mają tego samego adresu URL. Pełny adres URL do obiektu jest nazywany identyfikatorem obiektu i składa się z fragment prefiks, który identyfikuje usługi Key Vault, typ obiektu, podawana przez użytkownika nazwa obiektu, a wersja obiektu. Nazwa obiektu jest rozróżniana wielkość liter i modyfikować. Identyfikatory, które nie zawierają wersję obiektu są określane jako podstawa identyfikatorów.  

Aby uzyskać więcej informacji, zobacz [uwierzytelniania, żądań i odpowiedzi](authentication-requests-and-responses.md)

Identyfikator obiektu ma następujący format Ogólne:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Gdzie:  

|||  
|-|-|  
|`keyvault-name`|Nazwa magazynu kluczy w usłudze Microsoft Azure Key Vault.<br /><br /> Nazwy usługi Key Vault są wybrane przez użytkownika i są globalnie unikatowe.<br /><br /> Nazwa magazynu kluczy musi być w ciągu 3 do 24 znaków długości zawierający tylko (0-9, a-z, A-Z, a -).|  
|`object-type`|Typ obiektu, "klucze" lub "kluczy tajnych".|  
|`object-name`|`object-name` Jest nazwą podane przez użytkownika i muszą być unikatowe w obrębie magazynu kluczy. Nazwa musi być ciągiem 1 do 127 znaków długości i zawierać tylko 0-9, a-z, A-Z, a -.|  
|`object-version`|`object-version` Jest wygenerowany przez system, identyfikator ciągu 32 znaków, który opcjonalnie jest wykorzystywana do adresowania unikatowy wersji obiektu.|  

## <a name="key-vault-keys"></a>Klucze usługi Key Vault

###  <a name="BKMK_KeyTypes"></a> Klucze i typów kluczy

Klucze szyfrowania w usłudze Azure Key Vault są reprezentowane jako obiektów JSON klucza sieci Web [JWK]. Podstawowy specyfikacje JWK/JWA również zostały rozszerzone na potrzeby włączenia typów kluczy, które są unikatowe dla wdrożenia usługi Azure Key Vault, na przykład importu klucze do usługi Azure Key Vault za pomocą HSM opakowywanie określonego dostawcy (Thales) umożliwia bezpieczne przenoszenie kluczy, takie tego mogą one być używane tylko w modułów HSM usługi Azure Key Vault.  

Początkowa wersja usługi Azure Key Vault obsługuje kluczy RSA. przyszłych wydaniach może obsługiwać inne typy kluczy, takie jak krzywej symetrycznego i korzenia.  

-   **RSA**: 2048-bitowego klucza RSA. Jest to klucz "słowo soft", który jest przetwarzany w oprogramowaniu przez usługi Key Vault, ale jest przechowywany jako zaszyfrowany magazynowane przy użyciu klucza system, który znajduje się w module HSM. Klienci mogą istniejącego klucza RSA importowanie lub zwróć się usługi Azure Key Vault Generowanie jednego.  
-   **RSA HSM**: klucza RSA, która została przetworzona w module HSM. Klucze RSA HSM są chronione w jednym z środowiska Azure klucza magazynu sprzętowego modułu zabezpieczeń Security World (Brak zabezpieczeń Security World według lokalizacji geograficznej do obsługi izolacji). Klienci mogą Importowanie kluczy RSA w formie nietrwałego lub eksportowanie z zgodnego urządzenia sprzętowego modułu zabezpieczeń, lub zwróć się usługi Azure Key Vault Generowanie jednego. Ten typ klucza dodaje atrybut T do JWK uzyskać do przenoszenia materiału klucza HSM.  

     Aby uzyskać więcej informacji na granicach geograficznych, zobacz [Microsoft Azure Trust Center](https://azure.microsoft.com/en-us/support/trust-center/privacy/)  

###  <a name="BKMK_RSAAlgorithms"></a> Algorytmy RSA  
 Następujące identyfikatory algorytmu są obsługiwane z kluczy RSA w usłudze Azure Key Vault.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY SZYFROWANIA/ODSZYFROWYWANIA

-   **RSA1_5** -RSAES PKCS1 V1_5 [RFC3447] klucza szyfrowania  
-   **RSA OAEP** — RSAES przy użyciu optymalne asymetrycznego szyfrowania dopełnienie (OAEP) [RFC3447], z określonymi przez 3447 RFC w sekcji A.2.1 parametrami domyślne. Te parametry domyślne za pomocą funkcji skrótu SHA-1 i funkcja generowania maski MGF1 SHA-1.  

#### <a name="signverify"></a>SPRAWDŹ/LOGOWANIA

-   **RS256** — RSASSA-PKCS-v1_5 przy użyciu algorytmu SHA-256. Wartość skrótu dostarczona aplikacja musi być obliczane przy użyciu algorytmu SHA-256 i musi być 32 bajtów długości.  
-   **RS384** — RSASSA-PKCS-v1_5 przy użyciu algorytmu SHA-384. Wartość skrótu dostarczona aplikacja musi być obliczane przy użyciu algorytmu SHA-384 i musi być 48 bajtów długości.  
-   **RS512** — RSASSA-PKCS-v1_5 przy użyciu algorytmu SHA-512. Wartość skrótu dostarczona aplikacja musi być obliczane przy użyciu algorytmu SHA-512 i musi być 64 bajtów długości.  
-   **RSNULL** — Zobacz [RFC2437] specjalne przypadek użycia na potrzeby niektórych scenariuszy TLS.  

###  <a name="BKMK_RSA-HSMAlgorithms"></a> Algorytmy RSA HSM  
Następujące identyfikatory algorytmu są obsługiwane z kluczy RSA HSM usługi Azure Key Vault.  

### <a name="BKMK_Cryptographic"></a> Ochrona usług kryptograficznych

Modułów kryptograficznych, które korzysta z usługi Azure Key Vault, czy HSM lub oprogramowania, są zweryfikowane w trybie FIPS. Nie trzeba wykonywać żadnych czynności specjalne do uruchamiania w trybie FIPS. Jeśli użytkownik **utworzyć** lub **zaimportować** kluczy jako chronionego przez moduł HSM, ich dotrą do przetworzenia wewnątrz sprzętowych modułów zabezpieczeń zweryfikowane FIPS 140-2 poziom 2 lub nowszej. Jeśli użytkownik **utworzyć** lub **zaimportować** kluczy jako chronionego przez oprogramowanie, a następnie są przetwarzane wewnątrz modułów kryptograficznych sprawdzane FIPS 140-2 poziom 1 lub nowszej. Aby uzyskać więcej informacji, zobacz [kluczy i typów kluczy](about-keys-secrets-and-certificates.md#BKMK_KeyTypes).

#### <a name="wrapunwrap-encryptdecrypt"></a>ZAWIJAJ/ROZPAKOWYWANIA, SZYFROWANIA/ODSZYFROWYWANIA

-   **RSA1_5** -RSAES PKCS1 V1_5 [RFC3447] klucza szyfrowania.  
-   **RSA OAEP** — RSAES przy użyciu optymalne asymetrycznego szyfrowania dopełnienie (OAEP) [RFC3447], z określonymi przez 3447 RFC w sekcji A.2.1 parametrami domyślne. Te parametry domyślne za pomocą funkcji skrótu SHA-1 i funkcja generowania maski MGF1 SHA-1.  

 #### <a name="signverify"></a>SPRAWDŹ/LOGOWANIA  

-   **RS256** — RSASSA-PKCS-v1_5 przy użyciu algorytmu SHA-256. Wartość skrótu dostarczona aplikacja musi być obliczane przy użyciu algorytmu SHA-256 i musi być 32 bajtów długości.  
-   **RS384** — RSASSA-PKCS-v1_5 przy użyciu algorytmu SHA-384. Wartość skrótu dostarczona aplikacja musi być obliczane przy użyciu algorytmu SHA-384 i musi być 48 bajtów długości.  
-   **RS512** — RSASSA-PKCS-v1_5 przy użyciu algorytmu SHA-512. Wartość skrótu dostarczona aplikacja musi być obliczane przy użyciu algorytmu SHA-512 i musi być 64 bajtów długości.  
-   RSNULL: Zobacz [RFC2437] specjalne przypadek użycia na potrzeby niektórych scenariuszy TLS.  

###  <a name="BKMK_KeyOperations"></a> Operacje kluczy

Usługa Azure Key Vault obsługuje następujące operacje na obiektach klucza:  

-   **Utwórz**: umożliwia klientowi utworzenia klucza w usłudze Azure Key Vault. Wartość klucza jest generowany przez usługi Azure Key Vault i przechowywane i nie jest zwalniany do klienta. Asymetryczne (i w przyszłości krzywej eliptycznej i Symmetric) mogą zostać utworzone w usłudze Azure Key Vault.  
-   **Importuj**: umożliwia klientowi zaimportować istniejący klucz do usługi Azure Key Vault. Asymetryczne (i w przyszłości krzywej eliptycznej i Symmetric) klucze mogą je zaimportować do usługi Azure Key Vault przy użyciu metod tworzenia różnych pakietów w konstrukcji JWK.  
-   **Aktualizacja**: umożliwia klientowi z wystarczającymi uprawnieniami do modyfikowania metadanych (atrybutów klucza) skojarzone z kluczem wcześniej zapisanych w ramach usługi Azure Key Vault.  
-   **Usuń**: umożliwia klientowi z wystarczającymi uprawnieniami, aby usunąć klucz z usługi Azure Key Vault.  
-   **Lista**: umożliwia klientowi listę wszystkich kluczy w danej usługi Azure Key Vault.  
-   **Lista wersji**: umożliwia klientowi listę wszystkich wersji z danym kluczem w danej usługi Azure Key Vault.  
-   **Pobierz**: temu klient może pobrać części publiczny klucz w usłudze Azure Key Vault.  
-   **Kopia zapasowa**: eksportuje klucza w formie chronionych.  
-   **Przywróć**: importuje wcześniej kopii zapasowej klucza.  

Aby uzyskać więcej informacji, zobacz [klucza operacji](/rest/api/keyvault/key-operations.md)  

Po klucz został utworzony w usłudze Azure Key Vault, może wykonać następujące operacje kryptograficzne przy użyciu klucza:  

-   **Znak, a następnie sprawdź, czy**: ściśle, ta operacja jest "skrót znaku" lub "Weryfikuj wyznaczania wartości skrótu", ponieważ usługa Azure Key Vault nie obsługuje tworzenia skrótów zawartości w ramach tworzenia podpisu. Aplikacje powinny skrótu danych być podpisany lokalnie, a następnie zażądać tego rejestrowania usługi Azure Key Vault skrót. Weryfikacji podpisanej skrótów jest obsługiwany jako operacja wygody dla aplikacji, które może nie mieć dostępu do [publicznego] materiału klucza; Firma Microsoft zaleca, aby uzyskać najlepszą wydajność aplikacji, sprawdź, czy operacje są wykonywane lokalnie.  
-   **Klucz szyfrowania / zawijania**: klucza przechowywanego w usłudze Azure Key Vault może służyć do ochrony klucza innego, zwykle klucza szyfrowania symetrycznego zawartości (CEK). Po klucz w usłudze Azure Key Vault ma asymetrycznego, używane jest szyfrowanie klucza, na przykład RSA OAEP i operacje WRAPKEY/UNWRAPKEY są równoważne do szyfrowania/ODSZYFROWYWANIA. W przypadku symetrycznego klucza w usłudze Azure Key Vault jest używany klucz zawijania; na przykład AES KW. Operacja WRAPKEY jest obsługiwana dla wygody dla aplikacji, które może nie mieć dostępu do [publicznego] materiału klucza; zalecane jest, aby uzyskać najlepszą wydajność aplikacji WRAPKEY operacje są wykonywane lokalnie.  
-   **Szyfrowanie i odszyfrowywanie**: klucza przechowywanego w usłudze Azure Key Vault może służyć do szyfrowania lub odszyfrowywania jednego bloku danych, których rozmiar jest określany przez typ klucza i algorytm szyfrowania wybrane. Operacja szyfrowania jest udostępniana udogodnienie dla aplikacji, które może nie mieć dostępu do [publicznego] materiału klucza; zalecane jest, aby uzyskać najlepszą wydajność aplikacji, szyfrują lokalnie można wykonać operacji.  

Gdy WRAPKEY/UNWRAPKEY przy użyciu kluczy asymetrycznych może wydawać się zbędny, ponieważ operacja jest odpowiednikiem szyfrowania/ODSZYFROWYWANIA, różnych operacji jest uznawany za zapewnienie semantycznego i autoryzacji oddzielenie tych operacji i spójności gdy innych typów kluczy są obsługiwane przez usługę.  

Usługa Azure Key Vault nie obsługuje operacji EKSPORTOWANIA: po zainicjowaniu obsługi klucza w systemie nie można wyodrębnić lub zmodyfikować jego materiału klucza.  Jednak użytkowników usługi Azure Key Vault, który może wymagać ich klucza dla innych zastosowań lub po usunięciu w usłudze Azure Key Vault, może używać operacji tworzenia kopii zapasowej i przywracania eksport klucza w formie chronionych. Klucze utworzone przez operację tworzenia kopii zapasowej nie są można używać poza usługą Azure Key Vault. Alternatywnie operacja IMPORTOWANIA może być używana przed wiele wystąpień usługi Azure Key Vault.  

Użytkownicy mogą ograniczyć wszelkie operacje kryptograficzne, które obsługuje usługi Azure Key Vault na podstawie na klucz, za pomocą właściwości key_ops obiektu JWK.  

Aby uzyskać więcej informacji na obiektach JWK, zobacz [klucza sieci Web JSON (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key).  

###  <a name="BKMK_KeyAttributes"></a> Atrybuty klucza

Oprócz materiał kluczy można określić następujące atrybuty. Żądanie JSON, atrybuty — słowo kluczowe i nawiasów klamrowych "{" "}", są wymagane, nawet jeśli nie mają żadnych określonych atrybutów.  

- *włączone*: wartość logiczna, opcjonalnie, domyślnie jest **true**. Określa, czy klucz jest włączone i użyteczny dla operacji kryptograficznych. *Włączone* atrybut jest używany w połączeniu z *nbf* i *exp*. Jeśli operacja odbywa się między *nbf* i *exp*, będzie mieć uprawnienia tylko, jeśli *włączone* ma ustawioną wartość **true**. Operacje poza *nbf* / *exp* okno automatycznie są niedozwolone, z wyjątkiem niektórych typów operacji w obszarze [określonych warunkach](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops).
- *NBF*: IntDate, opcjonalnie, domyślny jest teraz. *Nbf* (nie wcześniej niż) atrybut określa czas, przed którym klucza nie mogą być używane dla operacji kryptograficznych, z wyjątkiem niektórych typów operacji w obszarze [określonych warunkach](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops). Przetwarzanie *nbf* atrybut wymaga bieżącej daty/godziny musi przypadać po lub nie ma wartości — przed daty/godziny na liście *nbf* atrybutu. Usługa Azure Key Vault mogą zapewnić niektórych małych swobodę, zwykle nie więcej niż kilka minut, na wypadek zegara pochylenia. Wartość musi być liczbą zawierającą wartość IntDate.  
- *EXP*: IntDate, opcjonalnie, domyślna to "nieskończoność". *Exp* atrybut (czas wygaśnięcia) określa czas wygaśnięcia na lub po którym klucz nie może służyć do operacji kryptograficznej, z wyjątkiem niektórych typów operacji w obszarze [określonych warunkach](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops). Przetwarzanie *exp* atrybut wymaga bieżącej daty/godziny należy przed Data/godzina wygaśnięcia na liście *exp* atrybutu. Usługa Azure Key Vault mogą zapewnić niektórych małych swobodę, zwykle nie więcej niż kilka minut, na wypadek zegara pochylenia. Wartość musi być liczbą zawierającą wartość IntDate.  

Istnieją dodatkowe atrybuty tylko do odczytu, które znajdują się w odpowiedzi zawierający atrybutów klucza:  

- *utworzony*: IntDate opcjonalne. *Utworzony* atrybut wskazuje utworzenia tej wersji klucza. Ta wartość jest pusta dla kluczy utworzonych przed dodaniem tego atrybutu. Wartość musi być liczbą zawierającą wartość IntDate.  
- *Zaktualizowano*: IntDate opcjonalne. *Zaktualizowane* atrybut wskazuje, kiedy ta wersja klucza zostało zaktualizowane. Ta wartość jest równa null dla kluczy, które zostały ostatnio zaktualizowane przed dodaniem tego atrybutu. Wartość musi być liczbą zawierającą wartość IntDate.  

Aby uzyskać więcej informacji na IntDate i inne typy danych, zobacz [typy danych](about-keys-secrets-and-certificates.md#BKMK_DataTypes)  

#### <a name="BKMK_key-date-time-ctrld-ops"></a> Operacje kontrolowane daty i godziny

Klucze nie zostały jeszcze nieprawidłowe lub wygasłe, te poza *nbf* / *exp* okna, będzie działać w przypadku **odszyfrować**, **unwrap** i **Sprawdź** operacji (nie zwrócą 403, zabroniony). Uzasadnienie przy użyciu nie zostały jeszcze prawidłowego stanu jest umożliwienie klucza do sprawdzenia przed użycia w środowisku produkcyjnym. Uzasadnienie przy użyciu stanu wygaśnięcia jest umożliwienie odzyskiwania operacji na danych, który został utworzony, gdy klucz jest prawidłowa. Ponadto możesz wyłączyć dostęp do klucza przy użyciu zasad usługi Key Vault lub aktualizując *włączone* atrybutu klucza do **false**.

Aby uzyskać więcej informacji na temat danych typów, zobacz [typy danych](about-keys-secrets-and-certificates.md#BKMK_DataTypes).

Aby uzyskać więcej informacji na inne atrybuty, zobacz [klucza sieci Web JSON (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key).

### <a name="BKMK_Keytags"></a> Tagi klucza

W formularzu tagów można określić dodatkowe metadane specyficzne dla aplikacji. Usługa Azure Key Vault obsługuje maksymalnie 15 znaczników, z których każda może mieć nazwę 256 znaków i wartością 256 znaków.  

>[!Note]
>Tagi są do odczytu przez obiekt wywołujący, jeśli mają *listy* lub *uzyskać* uprawnienia do tego typu obiektu; kluczy, kluczy tajnych ani certyfikatów.

###  <a name="BKMK_KeyAccessControl"></a> Kontrola dostępu do klucza

Kontrola dostępu do kluczy zarządza Key Vault znajduje się na poziomie magazyn kluczy, który działa jako kontener kluczy. Brak zasad kontroli dostępu dla kluczy różni się od zasad kontroli dostępu do kluczy tajnych w magazynie tego samego klucza. Użytkownicy mogą utworzyć co najmniej jeden magazynów do przechowywania kluczy i są zobowiązane do zachowania segmentacji odpowiednie scenariusza i zarządzanie kluczami. Kontrola dostępu do kluczy jest niezależna od kontroli dostępu do kluczy tajnych.  

Następujące uprawnienia może zostać przydzielony na użytkownika / service zleceniodawcy w wpisu kontroli dostępu kluczy w magazynie. Te uprawnienia duplikatów ściśle dozwolona dla obiekt główny operacje:  

-   *Utwórz*: tworzenie nowych kluczy
-   *Pobierz*: publicznej części klucza, a także jego atrybuty do odczytu
-   *Lista*: listy kluczy lub wersji klucza przechowywanego w magazynie kluczy
-   *Zaktualizuj*: aktualizacji atrybutów dla klucza
-   *Usuń*: usuwanie klucza obiektu
-   *znak*: Użyj skróty służące klucza do podpisywania
-   *Sprawdź*: Użyj klucza, aby sprawdzić roztwór
-   *wrapKey*: ochrona klucza symetrycznego za pomocą klawisza
-   *unwrapKey*: Użyj klucz do usunięcia ochrony opakowana kluczy symetrycznych
-   *szyfrowanie*: Użyj klawisza, aby chronić kolejnością bajtów
-   *Odszyfrowanie*: Użyj klucza do usunięcia ochrony sekwencję bajtów
-   *Importuj*: Importowanie klucza do magazynu kluczy
-   *Kopia zapasowa*: kopia zapasowa klucza w magazynie kluczy
-   *Przywróć*: Przywracanie kopii zapasowej klucza do magazynu kluczy
-   *wszystkie*: wszystkie uprawnienia

## <a name="key-vault-secrets"></a>Klucze tajne magazyn kluczy 

###  <a name="BKMK_WorkingWithSecrets"></a> Praca z kluczy tajnych

Kluczy tajnych w magazynie kluczy Azure są sekwencje octet o maksymalnym rozmiarze 25 KB każdego. Usługa Azure Key Vault nie zapewnia żadnych semantyki kluczy tajnych; go jedynie akceptuje dane, są szyfrowane i przechowywane on zwracania identyfikatora tajne "id", który może służyć do pobierania klucza tajnego w późniejszym czasie.  

W przypadku bardzo poufnych danych klientów należy wziąć pod uwagę dodatkowych warstw ochrony danych, który jest przechowywany w usłudze Azure Key Vault; na przykład przez wstępnie szyfrowanie danych przy użyciu oddzielnych ochrony klucza.  

Usługa Azure Key Vault obsługuje również pola contentType do kluczy tajnych. Klienci mogą określić typ zawartości, "contentType", aby ułatwić interpretowanie danych tajnych podczas pobierania klucza tajnego. Maksymalna długość tego pola to 255 znaków. Nie znajdują się wstępnie zdefiniowane wartości. Sugerowane użycia jest jako podpowiedź dla interpretowanie danych tajnych. Na przykład implementacja może przechowywać hasła i certyfikaty jako klucze tajne, a następnie użyj tego pola, aby wskazać, który. Nie znajdują się wstępnie zdefiniowane wartości.  

###  <a name="BKMK_SecretAttrs"></a> Tajny atrybutów

Oprócz dane poufne można określić następujące atrybuty:  

- *EXP*: IntDate, opcjonalnie, domyślnie jest **nieskończona**. *Exp* atrybut (czas wygaśnięcia) określa czas wygaśnięcia na lub po której można pobrać tajnego danych nie może, z wyjątkiem [sytuacji, w szczególności](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops). Przetwarzanie *exp* atrybut wymaga bieżącej daty/godziny należy przed Data/godzina wygaśnięcia na liście *exp* atrybutu. Usługa Azure Key Vault mogą zapewnić niektórych małych swobodę, zwykle nie więcej niż kilka minut, na wypadek zegara pochylenia. Wartość musi być liczbą zawierającą wartość IntDate.  
- *NBF*: IntDate, opcjonalnie, domyślnie jest **teraz**. *Nbf* (nie wcześniej niż) atrybut określa czas, przed którym nie można pobrać danych tajnych, z wyjątkiem [sytuacji, w szczególności](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops). Przetwarzanie *nbf* atrybut wymaga bieżącej daty/godziny musi przypadać po lub nie ma wartości — przed daty/godziny na liście *nbf* atrybutu. Usługa Azure Key Vault mogą zapewnić niektórych małych swobodę, zwykle nie więcej niż kilka minut, na wypadek zegara pochylenia. Wartość musi być liczbą zawierającą wartość IntDate.  
- *włączone*: wartość logiczna, opcjonalnie, domyślnie jest **true**. Ten atrybut określa, czy można pobrać tajnego danych. Włączony atrybut jest używany w połączeniu z i *exp* gdy operacja odbywa się między i exp, jego będą tylko dozwolone włączenie ustawiono **true**. Operacje poza *nbf* i *exp* okno automatycznie są niedozwolone, chyba że w [sytuacji, w szczególności](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops).  

Istnieją dodatkowe atrybuty tylko do odczytu, które znajdują się w odpowiedzi zawierającego poufna atrybuty:  

- *utworzony*: IntDate opcjonalne. Utworzonego atrybutu wskazuje utworzenia tej wersji klucza tajnego. Ta wartość jest pusta dla kluczy tajnych utworzone przed dodaniem tego atrybutu. Wartość musi być liczbą zawierającą wartość IntDate.  
- *Zaktualizowano*: IntDate opcjonalne. Zaktualizowany atrybut wskazuje, kiedy ta wersja klucza tajnego zostało zaktualizowane. Ta wartość jest pusta dla klucze tajne, które zostały ostatnio zaktualizowane przed dodaniem tego atrybutu. Wartość musi być liczbą zawierającą wartość IntDate.

#### <a name="BKMK_secret-date-time-ctrld-ops"></a> Operacje kontrolowane daty i godziny

Klucz tajny **uzyskać** operacji będzie działać w przypadku kluczy tajnych nie zostały jeszcze nieprawidłowe lub wygasłe, poza *nbf* / *exp* okna. Wywoływanie klucz tajny **uzyskać** operacji nie zostały jeszcze prawidłowy klucz tajny, może służyć do celów testowych. Pobieranie (**uzyskać**lasycznego, dokującego) wygasły klucz tajny, można go używać w operacjach odzyskiwania.

Aby uzyskać więcej informacji na temat danych typów, zobacz [typy danych](about-keys-secrets-and-certificates.md#BKMK_DataTypes).  

###  <a name="BKMK_SecretAccessControl"></a> Kontrola dostępu tajny

Kontrola dostępu do kluczy tajnych zarządzane w usłudze Azure Key Vault znajduje się na poziomie magazyn kluczy, który działa jako kontener tych kluczy tajnych. Brak zasad kontroli dostępu do kluczy tajnych różni się od zasad kontroli dostępu do kluczy w tej samej usługi Key Vault. Użytkownicy mogą utworzyć co najmniej jeden magazynów do przechowywania kluczy tajnych i są zobowiązane do zachowania segmentacji odpowiedni scenariusz oraz zarządzanie nimi kluczy tajnych. Kontrola dostępu do kluczy tajnych są niezależne od kontroli dostępu do kluczy.  

Następujące uprawnienia mogą być używane, na podstawie na principal w wpisu kontroli dostępu kluczy tajnych w magazynie oraz ściśle duplikatów operacji dozwolona dla obiekt tajny:  

-   *Ustaw*: Tworzenie nowego hasła  
-   *Pobierz*: odczytać klucz tajny  
-   *Lista*: listę wersji klucza tajnego przechowywane w magazynie kluczy lub kluczy tajnych  
-   *Usuń*: usuwanie klucza tajnego  
-   *wszystkie*: wszystkie uprawnienia  

Aby uzyskać więcej informacji na temat pracy z kluczy tajnych, zobacz [operacje kluczy tajnych](/rest/api/keyvault/secret-operations.md).  

###  <a name="BKMK_SecretTags"></a> Tajny tagów  
W formularzu tagów można określić dodatkowe metadane specyficzne dla aplikacji. Usługa Azure Key Vault obsługuje maksymalnie 15 znaczników, z których każda może mieć nazwę 256 znaków i wartością 256 znaków.  

>[!Note]
>Tagi są do odczytu przez obiekt wywołujący, jeśli mają *listy* lub *uzyskać* uprawnienia do tego typu obiektu; kluczy, kluczy tajnych ani certyfikatów.

## <a name="key-vault-certificates"></a>Magazyn kluczy certyfikatów

Obsługa certyfikatów usługi Key Vault umożliwia zarządzanie x509 Twojego certyfikaty i następujące zachowania:  

-   Umożliwia właścicielowi certyfikatu utworzyć certyfikat za pośrednictwem procesu tworzenia usługi Key Vault lub importowania istniejącego certyfikatu. Dotyczy to zarówno z podpisem własnym, a urząd certyfikacji generowane certyfikatów.
-   Umożliwia właścicielowi certyfikatu usługi Key Vault, do implementowania bezpiecznego magazynu i zarządzania X509 certyfikatów bez interakcji z materiału klucza prywatnego.  
-   Umożliwia właścicielowi certyfikatu utworzyć zasadę, która określa, że magazyn kluczy, aby zarządzać cyklu życia certyfikatu.  
-   Umożliwia właścicieli certyfikatu, podaj informacje kontaktowe dla powiadomień dotyczących zdarzeń cyklu życia wygaśnięcia i odnawiania certyfikatów.  
-   Obsługuje automatycznego odnawiania z wystawców wybranych - Key Vault partnera X509 certyfikatu dostawców / urzędy certyfikacji.

>[!Note]
>Współpracę inne niż dostawców/urzędów również są dozwolone, ale nie będzie obsługiwać funkcję automatycznego odnawiania.

###  <a name="BKMK_CompositionOfCertificate"></a> Kompozycja certyfikatu

Po utworzeniu certyfikatu usługi Key Vault adresowanego klucza i klucz tajny są również tworzone o takiej samej nazwie. Klucz usługi Key Vault umożliwia operacje kluczy i secret Key Vault umożliwia pobieranie wartości certyfikatu jako klucz tajny. Certyfikat usługi Key Vault zawiera także x509 publicznego certyfikatu metadanych.  

Identyfikator i wersja certyfikatów jest podobny do kluczy i kluczy tajnych. Określonej wersji adresowanego klucza i klucz tajny utworzonych za pomocą wersji certyfikatu usługi Key Vault jest dostępna w odpowiedzi certyfikatu usługi Key Vault.
 
![Cetificates są złożonych obiektów](media/azure-key-vault.png)

###  <a name="BKMK_CertificateExportableOrNonExportableKey"></a> Klucz można eksportować lub nie można eksportować

Po utworzeniu certyfikatu usługi Key Vault go może można pobrać z adresowanego klucz tajny z kluczem prywatnym w formacie PFX lub PEM Jeśli zasady stosowane do utworzenia certyfikatu wskazane, że klucz jest możliwy do eksportu. Jeżeli zasady stosowane do utworzenia magazynu kluczy certyfikatu klucza, który ma być nie może zostać wyeksportowany, następnie klucz prywatny nie jest częścią wartości po pobraniu jako klucz tajny.  

Klucz adresowanego staje się bardziej odpowiednie certyfikaty KV nie może zostać wyeksportowany. Operacje mogą być adresowane klucza KV są mapowane z *keyusage* pole KV zasad certyfikat użyty do utworzenia certyfikatu KV.  

Obsługiwane są dwa typy klucza — *RSA* lub *RSA HSM* z certyfikatami. Możliwy do eksportu jest dozwolona tylko w RSA, nie są obsługiwane przez moduł HSM RSA.  

###  <a name="BKMK_CertificateAttributesAndTags"></a> Atrybuty certyfikatu i tagów

Oprócz metadanymi certyfikatu, klucza adresowanego i adresowanego klucz tajny, certyfikat usługi Key Vault zawiera także atrybuty i znaczniki.  

#### <a name="attributes"></a>Atrybuty

Atrybuty mogą być adresowane klucza i klucz tajny tworzone podczas tworzenia certyfikatu KV są dublowane atrybuty certyfikatu.  

Certyfikat usługi Key Vault ma następujące atrybuty:  

-   *włączone*: wartość logiczna, opcjonalnie, domyślnie jest **true**. Ten atrybut można określić wskazująca, czy można pobrać danych certyfikatu jako poufne lub obsługiwane jako klucz. Jest on używany w połączeniu z *nbf* i *exp* gdy operacja odbywa się między *nbf* i *exp*, będzie mieć uprawnienia tylko, jeśli włączone jest ustawiony na wartość true. Operacje poza *nbf* i *exp* okno automatycznie są niedozwolone.  

Istnieją dodatkowe atrybuty tylko do odczytu, które znajdują się w odpowiedzi:

-   *utworzony*: IntDate: wskazuje utworzenia tej wersji certyfikatu.  
-   *Zaktualizowano*: IntDate: wskazuje, kiedy ta wersja certyfikatu został zaktualizowany.  
-   *EXP*: IntDate: zawiera wartość x509 daty wygaśnięcia certyfikatu.  
-   *NBF*: IntDate: zawiera wartość daty x509 certyfikatu.  

> [!Note] 
> Jeśli certyfikat usługi Key Vault wygaśnie, jest klucz adresowanego i klucz tajny przestać działać.  

#### <a name="tags"></a>Tagi

 Klient określonego słownika par wartości klucza, podobnie jak tagi w kluczy i kluczy tajnych.  

 > [!Note]
> Tagi są do odczytu przez obiekt wywołujący, jeśli mają *listy* lub *uzyskać* uprawnienia do tego typu obiektu; kluczy, kluczy tajnych ani certyfikatów.

###  <a name="BKMK_CertificatePolicy"></a> Zasady certyfikatów

Zasady certyfikatów zawiera informacje dotyczące sposobu tworzenia i zarządzanie cyklem życia certyfikatu KV. Po zaimportowaniu certyfikatu z kluczem prywatnym w magazynie kluczy, odczytując x509 tworzone są zasady domyślne certyfikatu.  

Po utworzeniu certyfikatu KV od podstaw zasad musi zostać dostarczone do usługi Key Vault informują o tym na temat tworzenia tego KV certyfikatu w wersji lub dalej KV certyfikatu. Po ustanowieniu zasad, nie jest wymagane w przypadku kolejnych tworzenia operacji do tworzenia następnej wersji certyfikatu KV.  

Istnieje tylko jedno wystąpienie zasady dla wszystkich wersji KV certyfikatu.  

Na wysokim poziomie zasady dotyczące certyfikatów zawiera następujące elementy:  

-   Właściwości certyfikatu X509: zawiera nazwy podmiotu, alternatywne nazwy podmiotu itp. Pozwala utworzyć x509 żądania certyfikatu.  
-   Właściwości klucza: typ klucza zawiera długość można eksportować klucza i ponowne użycie pola klucza. Te pola poinstruuj magazynu kluczy w sposób generowania klucza.  
-   Właściwości tajny: zawiera poufną właściwości, takie jak typ zawartości mogą być adresowane klucz tajny, aby wygenerować wartość tajna, pobierania certyfikatu, ponieważ klucz tajny.  
-   Akcje okres istnienia: zawiera akcje okres istnienia certyfikatu KV. Każda akcja okres istnienia zawiera:  

     - Wyzwalacz: określona za pomocą dni przed wygaśnięcia lub okres istnienia procent zakresu  

     - Akcja: Określanie typu akcji — *emailContacts* lub *automatycznie odnawiać*  

-   Wystawca: Parametry o wystawca certyfikatu na potrzeby wystawiania x509 certyfikatów.  
-   Atrybuty zasad: atrybuty skojarzone z zasadami zawiera  

#### <a name="x509-to-key-vault-usage-mapping"></a>X509 do mapowania użycia usługi Key Vault

Poniższa tabela przedstawia mapowanie x509 zasad użycia klucza do wprowadzenia operacje kluczy klucza utworzona w ramach tworzenia certyfikatu usługi Key Vault.

|**X509 flagi użycie klucza**|**Ops klucza Key Vault**|**Domyślne zachowanie**|
|----------|--------|--------|
|DataEncipherment|szyfrowanie, odszyfrowywanie| ND |
|DecipherOnly|Odszyfrowywanie| ND  |
|Bity DigitalSignature|Zaloguj się, sprawdź| Domyślne usługi Key Vault bez użycia specyfikację w czasie tworzenia certyfikatu | 
|EncipherOnly|encrypt| ND |
|KeyCertSign|Zaloguj się, sprawdź|ND|
|KeyEncipherment|wrapKey, unwrapKey| Domyślne usługi Key Vault bez użycia specyfikację w czasie tworzenia certyfikatu | 
|Niemożność|Zaloguj się, sprawdź| ND |
|crlsign|Zaloguj się, sprawdź| ND |

###  <a name="BKMK_CertificateIssuer"></a> Wystawca certyfikatu

Obiektu certyfikatu usługi Key Vault przechowuje konfigurację używanych do komunikowania się z dostawcą wystawcy wybranego certyfikatu z certyfikatami kolejności x509.  

-   Magazyn kluczy współpracuje z następującego dostawcy wystawcy certyfikatu dla certyfikatów SSL

|**Nazwa dostawcy**|**Lokalizacje**|
|----------|--------|
|DigiCert|Obsługiwane we wszystkich lokalizacjach usługi Magazyn kluczy w chmurze publicznej i Azure dla instytucji rządowych|
|GlobalSign|Obsługiwane we wszystkich lokalizacjach usługi Magazyn kluczy w chmurze publicznej i Azure dla instytucji rządowych|

Aby można było utworzyć wystawcę certyfikatu w magazynie kluczy, musi pomyślnie wykonać następujące wstępnie wymagane kroki 1 i 2.  

1. Dołączyć do certyfikatu urzędu certyfikacji dostawców  

    -   Administrator organizacji musi lokalnego firmowych (np. Contoso) z co najmniej jednego dostawcę urzędu certyfikacji.  

2. Administrator tworzy żądający poświadczenia dla usługi Key Vault do rejestrowania (i odnawiania) certyfikaty SSL  

    -   Udostępnia konfiguracji, który ma być używany do utworzenia obiektu wystawcy dostawcy w magazynie kluczy  

Aby uzyskać więcej informacji dotyczących tworzenia obiektów wystawcy z portalu certyfikatów, zobacz [blogu certyfikaty w magazynie kluczy](http://aka.ms/kvcertsblog)  

Magazyn kluczy umożliwia tworzenie wielu obiektów wystawcy z konfiguracją dostawcy inną wystawcy. Po utworzeniu obiektu wystawcy jego nazwę można odwoływać się do jednego lub wielu zasad certyfikatów. Odwołanie do obiektu wystawcy powoduje, że magazyn kluczy, aby użyć konfiguracji określone w obiekcie wystawcy podczas żądania x509 certyfikatu od urzędu certyfikacji dostawcy podczas tworzenia certyfikatu i odnawiania.  

Wystawca obiekty są tworzone w magazynie i można używać tylko z certyfikatami KV w tym samym magazynie.  

###  <a name="BKMK_CertificateContacts"></a> Kontakty certyfikatu

Kontakty certyfikat zawierający informacje kontaktowe, aby wysyłać powiadomienia wyzwalane przez zdarzenia okres istnienia certyfikatu. Informacje o kontaktach jest współużytkowana przez wszystkie certyfikaty w magazynie kluczy. Powiadomienia są wysyłane do wszystkich kontaktów określonego zdarzenia dla każdego certyfikatu w magazynie kluczy.  

Jeśli zasady dotyczące certyfikatów ustawiono automatycznego odnawiania, powiadomienie jest wysyłane następujących zdarzeń.  

-   Przed odnawiania certyfikatu
-   Po odnowieniu certyfikatu, jeśli certyfikat został pomyślnie odnowiony z informacją, lub jeśli wystąpił błąd, wymaganie ręczne odnawianie certyfikatu.  

 Jeśli zasady dotyczące certyfikatów jest ustawiony na ręcznie odnowionego (tylko e-mail), a następnie powiadomienie jest wysyłane gdy nadejdzie czas, aby odnowić certyfikat.  

###  <a name="BKMK_CertificateAccessControl"></a> Kontrola dostępu do certyfikatu

 Kontrola dostępu dla certyfikatów jest zarządzany przez magazyn kluczy i jest dostępne na poziomie magazyn kluczy, który działa jako kontener tych certyfikatów. Brak zasad kontroli dostępu dla certyfikatów różni się od zasad kontroli dostępu do kluczy i kluczy tajnych w magazynie tego samego klucza. Użytkownicy mogą utworzyć co najmniej jeden magazynów do przechowywania certyfikatów i są zobowiązane do zachowania segmentacji odpowiednie scenariusza i zarządzania certyfikatami.  

 Następujące uprawnienia mogą być używane na podstawie na principal w kluczy tajnych wpisu kontroli dostępu na magazyn kluczy i ściśle wstecznych operacji dozwolona dla obiekt tajny:  

-   *Pobierz*: umożliwia get bieżącą wersję certyfikatu lub dowolna wersja certyfikatu 
-   *Lista*: umożliwia listy certyfikatów bieżącego lub wersji certyfikatu  
-   *Usuń*: umożliwia usunięcie certyfikatu swoje zasady i wszystkie jego wersje  
-   *Utwórz*: umożliwia tworzenie certyfikatu usługi Key Vault.  
-   *Importuj*: umożliwia importowanie certyfikatu materiału do certyfikatu klucza magazynu.  
-   *Zaktualizuj*: pozwala zaktualizować certyfikat.  
-   *manageconnects*: umożliwia zarządzanie magazynu kluczy certyfikatu kontaktów  
-   *getissuers*: umożliwia get wystawców certyfikatów  
-   *listissuers*: umożliwia lista wystawców certyfikatu  
-   *setissuers*: umożliwia tworzenie lub aktualizowanie wystawców certyfikatów usługi Key Vault  
-   *deleteissuers*: umożliwia usunięcie wystawców certyfikatów usługi Key Vault  
-   *wszystkie*: przyznaje wszystkie uprawnienia  

## <a name="additional-information-for-certificates"></a>Dodatkowe informacje dotyczące certyfikatów

- [Certyfikaty i zasady](/rest/api/keyvault/certificates-and-policies.md)
- [Wystawców certyfikatów](/rest/api/keyvault/certificate-issuers.md)
- [Kontakty certyfikatu](/rest/api/keyvault/certificate-contacts.md)

## <a name="see-also"></a>Zobacz też

- [Uwierzytelnianie żądań i odpowiedzi](authentication-requests-and-responses.md)
- [Wersje usługi Key Vault](key-vault-versions.md)
- [Przewodnik dewelopera magazynu kluczy](/azure/key-vault/key-vault-developers-guide)
