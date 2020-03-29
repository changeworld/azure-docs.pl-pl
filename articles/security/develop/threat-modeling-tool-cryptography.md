---
title: Kryptografia - Narzędzie do modelowania zagrożeń firmy Microsoft - Azure | Dokumenty firmy Microsoft
description: zagrożeniach ujawnionych w narzędziu do modelowania zagrożeń
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: c9116472af5b400ded0fea24f98b07bad9d9039b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728187"
---
# <a name="security-frame-cryptography--mitigations"></a>Ramka zabezpieczeń: Kryptografia | Czynniki 

| Produkt/usługa | Artykuł |
| --------------- | ------- |
| **Aplikacja internetowa** | <ul><li>[Używaj tylko zatwierdzonych szyfrów blokowych symetrycznych i długości klawiszy](#cipher-length)</li><li>[Użyj zatwierdzonych trybów szyfrowania blokowego i wektorów inicjowania dla szyfrów symetrycznych](#vector-ciphers)</li><li>[Używanie zatwierdzonych algorytmów asymetrycznych, długości klawiszy i dopełnienia](#padding)</li><li>[Użyj zatwierdzonych generatorów liczb losowych](#numgen)</li><li>[Nie używaj szyfrów strumienia symetrycznego](#stream-ciphers)</li><li>[Użyj zatwierdzonych algorytmów mieszania MAC/HMAC/keyed](#mac-hash)</li><li>[Używaj tylko zatwierdzonych funkcji skrótu kryptograficznego](#hash-functions)</li></ul> |
| **baza danych** | <ul><li>[Używanie silnych algorytmów szyfrowania do szyfrowania danych w bazie danych](#strong-db)</li><li>[Pakiety SSIS powinny być szyfrowane i podpisane cyfrowo](#ssis-signed)</li><li>[Dodawanie podpisu cyfrowego do krytycznych zabezpieczaczy bazy danych](#securables-db)</li><li>[Ochrona kluczy szyfrowania za pomocą serwera SQL EKM](#ekm-keys)</li><li>[Użyj funkcji AlwaysEncrypted, jeśli klucze szyfrowania nie powinny być ujawniane aparatowi bazy danych](#keys-engine)</li></ul> |
| **Urządzenie IoT** | <ul><li>[Bezpieczne przechowywanie kluczy kryptograficznych na urządzeniu IoT](#keys-iot)</li></ul> | 
| **Brama w chmurze IoT** | <ul><li>[Generowanie losowego klucza symetrycznego o wystarczającej długości do uwierzytelniania w Centrum IoT](#random-hub)</li></ul> | 
| **Klient mobilny Dynamics CRM** | <ul><li>[Upewnij się, że obowiązują zasady zarządzania urządzeniami, które wymagają użycia kodu PIN i umożliwiają zdalne wycieranie](#pin-remote)</li></ul> | 
| **Klient programu Dynamics CRM Outlook** | <ul><li>[Upewnij się, że obowiązują zasady zarządzania urządzeniami, które wymagają kodu PIN/hasła/automatycznej blokady i szyfrują wszystkie dane (np. funkcja BitLocker)](#bitlocker)</li></ul> | 
| **Serwer tożsamości** | <ul><li>[Upewnij się, że klucze podpisywania są przerzucone podczas korzystania z serwera tożsamości](#rolled-server)</li><li>[Upewnij się, że kryptograficznie silny identyfikator klienta, klucz tajny klienta są używane w serwerze tożsamości](#client-server)</li></ul> | 

## <a name="use-only-approved-symmetric-block-ciphers-and-key-lengths"></a><a id="cipher-length"></a>Używaj tylko zatwierdzonych szyfrów blokowych symetrycznych i długości klawiszy

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | <p>Produkty muszą używać tylko tych symetrycznych szyfrów blokowych i skojarzonych długości kluczy, które zostały jawnie zatwierdzone przez Crypto Advisor w organizacji. Zatwierdzone algorytmy symetryczne firmy Microsoft obejmują następujące szyfry blokowe:</p><ul><li>Dla nowego kodu AES-128, AES-192 i AES-256 dopuszczalne są</li><li>W celu zapewnienia wstecznej zgodności z istniejącym kodem, trzykluczywędła 3DES jest dopuszczalna</li><li>W przypadku produktów wykorzystujących szyfry blokowe symetryczne:<ul><li>Advanced Encryption Standard (AES) jest wymagany dla nowego kodu</li><li>Trzykluczeń potrójny standard szyfrowania danych (3DES) jest dopuszczalny w istniejącym kodzie dla zgodności z powrotem</li><li>Wszystkie inne szyfry blokowe, w tym RC2, DES, 2 Key 3DES, DESX i Skipjack, mogą być używane tylko do odszyfrowywania starych danych i muszą być zastąpione, jeśli są używane do szyfrowania</li></ul></li><li>W przypadku algorytmów szyfrowania bloków symetrycznych wymagana jest minimalna długość klucza 128 bitów. Jedynym algorytmem szyfrowania blokowego zalecanym dla nowego kodu jest AES -128, AES-192 i AES-256)</li><li>Trzykluczywówkowy 3DES jest obecnie dopuszczalne, jeśli już w użyciu w istniejącym kodzie; zaleca się przejście na AES. DES, DESX, RC2 i Skipjack nie są już uważane za bezpieczne. Algorytmy te mogą być używane tylko do odszyfrowywania istniejących danych ze względu na zgodność z powrotem, a dane powinny być ponownie szyfrowane przy użyciu zalecanego szyfrowania blokowego</li></ul><p>Należy pamiętać, że wszystkie szyfry blokowe symetryczne muszą być używane z zatwierdzonym trybem szyfrowania, który wymaga użycia odpowiedniego wektora inicjowania (IV). Odpowiedni IV, jest zazwyczaj liczbą losową i nigdy nie jest stałą wartością</p><p>Korzystanie ze starszych lub w inny sposób niezatwierdzonych algorytmów kryptograficznych i mniejszych długości kluczy do odczytywania istniejących danych (w przeciwieństwie do zapisywania nowych danych) może być dozwolone po przeglądzie Rady Kryptowalut w organizacji. Jednak należy złożyć wyjątek od tego wymogu. Ponadto w wdrożeniach korporacyjnych produkty powinny uwzględniać ostrzeganie administratorów, gdy słabe krypto jest używane do odczytywania danych. Takie ostrzeżenia powinny być objaśniające i wykonalne. W niektórych przypadkach właściwe może być kontrolowanie przez zasady grupy</p><p>Dozwolone algorytmy .NET dla zarządzanej wydajności kryptograficznej (w kolejności preferencji)</p><ul><li>AesCng (zgodny z FIPS)</li><li>AuthenticatedAesCng (zgodny z FIPS)</li><li>AESCryptoServiceProvider (zgodny ze standardem FIPS)</li><li>AESManaged (niezgodne z FIPS)</li></ul><p>Należy pamiętać, że żaden z tych `SymmetricAlgorithm.Create` algorytmów nie może być określony za pomocą lub `CryptoConfig.CreateFromName` metod bez wprowadzania zmian w pliku machine.config. Należy również pamiętać, że AES w wersjach platformy .NET `RijndaelManaged`przed `AesCng` .NET 3.5 jest nazwany i `AuthenticatedAesCng` są >dostępne za pośrednictwem programu CodePlex i wymagają CNG w podstawowym os</p>

## <a name="use-approved-block-cipher-modes-and-initialization-vectors-for-symmetric-ciphers"></a><a id="vector-ciphers"></a>Użyj zatwierdzonych trybów szyfrowania blokowego i wektorów inicjowania dla szyfrów symetrycznych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Wszystkie szyfry blokowe symetryczne muszą być używane z zatwierdzonym trybem szyfrowania symetrycznego. Jedynymi zatwierdzonymi trybami są CBC i CTS. W szczególności należy unikać trybu działania elektronicznego kodeksu (EBC); korzystanie z EBC wymaga przeglądu Rady Kryptograficznej twojej organizacji. Wszelkie użycie OFB, CFB, CTR, CCM i GCM lub innego trybu szyfrowania musi być sprawdzone przez Radę Krypto organizacji. Ponowne przywykanie tego samego wektora inicjowania (IV) z szyframi blokowymi w trybach "szyfrowania strumieniowego", takich jak CTR, może spowodować ujawnienie zaszyfrowanych danych. Wszystkie szyfry blokowe symetryczne muszą być również używane z odpowiednim wektorem inicjowania (IV). Odpowiedni IV jest kryptograficznie silną, losową liczbą i nigdy nie jest stałą wartością. |

## <a name="use-approved-asymmetric-algorithms-key-lengths-and-padding"></a><a id="padding"></a>Używanie zatwierdzonych algorytmów asymetrycznych, długości klawiszy i dopełnienia

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | <p>Stosowanie zakazanych algorytmów kryptograficznych wprowadza znaczne ryzyko dla bezpieczeństwa produktu i należy ich unikać. Produkty muszą używać tylko tych algorytmów kryptograficznych i skojarzonych długości kluczy i dopełnienia, które zostały jawnie zatwierdzone przez Radę Kryptograficzną organizacji.</p><ul><li>**RSA-** może być używany do szyfrowania, wymiany kluczy i podpisu. Szyfrowanie RSA musi używać tylko trybów dopełnienia OAEP lub RSA-KEM. Istniejący kod może używać PKCS #1 trybie dopełnienia w wersji 1.5 tylko dla zgodności. Użycie null dopełnienie jest jawnie zakazane. Klucze >= 2048 bitów jest wymagane dla nowego kodu. Istniejący kod może obsługiwać klucze < 2048 bitów tylko dla zgodności z powrotem po przejrzeniu przez radę krypto organizacji. Klucze < 1024 bitów mogą być używane tylko do odszyfrowywania/weryfikowania starych danych i muszą być zastąpione, jeśli są używane do operacji szyfrowania lub podpisywania</li><li>**ECDSA-** może być używany tylko do podpisu. ECDSA z >= klucze 256-bitowe jest wymagane dla nowego kodu. Podpisy oparte na ECDSA muszą zawierać jedną z trzech krzywych zatwierdzonych przez NIST (P-256, P-384 lub P521). Krzywe, które zostały dokładnie przeanalizowane, mogą być używane tylko po przejrzeniu z tablicą crypto organizacji.</li><li>**ECDH-** może być stosowany wyłącznie do wymiany kluczy. ECDH z >= 256-bitowe klucze jest wymagane dla nowego kodu. Wymiana kluczy oparta na ECDH musi opierać się na jednej z trzech krzywych zatwierdzonych przez NIST (P-256, P-384 lub P521). Krzywe, które zostały dokładnie przeanalizowane, mogą być używane tylko po przejrzeniu z tablicą crypto organizacji.</li><li>**DSA-** może być dopuszczalne po przejrzeniu i zatwierdzeniu przez Radę Krypto organizacji. Skontaktuj się z doradcą zabezpieczeń, aby zaplanować przegląd rady kryptograficznej w organizacji. Jeśli użycie DSA jest zatwierdzone, należy pamiętać, że należy zakazać używania kluczy o długości mniejszej niż 2048 bitów. CNG obsługuje 2048-bitowe i większe długości klawiszy od systemu Windows 8.</li><li>**Diffie-Hellman-** może być używany tylko do zarządzania kluczami sesji. Długość klucza >= 2048 bitów jest wymagane dla nowego kodu. Istniejący kod może obsługiwać długości kluczy < 2048 bitów tylko dla zgodności z powrotem po przejrzeniu przez Radę Kryptografiii organizacji. Nie można używać kluczy < 1024 bitów.</li><ul>

## <a name="use-approved-random-number-generators"></a><a id="numgen"></a>Użyj zatwierdzonych generatorów liczb losowych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | <p>Produkty muszą używać zatwierdzonych generatorów liczb losowych. Pseudolosowe funkcje, takie jak funkcja środowiska wykonawczego C rand, klasa .NET Framework System.Random lub funkcje systemowe, takie jak GetTickCount, nigdy nie mogą być używane w takim kodzie. Używanie algorytmu generatora liczb losowych (DUAL_EC_DRBG) z podwójną krzywą eliptyczną jest zabronione</p><ul><li>**Cng-** BCryptGenRandom(użycie flagi BCRYPT_USE_SYSTEM_PREFERRED_RNG zalecane, chyba że wywołujący może działać w dowolnym IRQL większe niż 0 [czyli PASSIVE_LEVEL])</li><li>**CAPI-** cryptGenRandom</li><li>**Win32/64-** RtlGenRandom (nowe implementacje powinny używać BCryptGenRandom lub CryptGenRandom) * rand_s * SystemPrng (dla trybu jądra)</li><li>**. NET-** RNGCryptoServiceProvider lub RNGCng</li><li>**Aplikacje ze Sklepu Windows-** Windows.Security.Cryptography.CryptographicBuffer.GenerateRandom lub . Generowanieliczba urojenia</li><li>**Apple OS X (10.7+)/iOS(2.0+)-** int SecRandomCopyBytes (SecRandomRef losowe, liczba \*size_t, uint8_t bajtów)</li><li>**Apple OS X (<10,7)-** Użyj /dev/random, aby pobrać liczby losowe</li><li>**Java (w tym google android java kod)-** java.security.SecureRandom klasy. Należy zauważyć, że dla Androida 4.3 (Jelly Bean), deweloperzy muszą postępować zgodnie z android zalecane obejście i zaktualizować swoje aplikacje, aby jawnie zainicjować PRNG z entropią z /dev/ urandom lub / dev / random</li></ul>|

## <a name="do-not-use-symmetric-stream-ciphers"></a><a id="stream-ciphers"></a>Nie używaj szyfrów strumienia symetrycznego

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Nie wolno używać szyfrów strumienia symetrycznego, takich jak RC4. Zamiast szyfrów strumienia symetrycznego produkty powinny używać szyfru blokowego, w szczególności AES o długości klucza co najmniej 128 bitów. |

## <a name="use-approved-machmackeyed-hash-algorithms"></a><a id="mac-hash"></a>Użyj zatwierdzonych algorytmów mieszania MAC/HMAC/keyed

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | <p>Produkty muszą używać tylko algorytmów kodu uwierzytelniania wiadomości zatwierdzonego (MAC) lub kodu uwierzytelniania wiadomości opartego na mieszaniu (HMAC).</p><p>Kod uwierzytelniania wiadomości (MAC) to informacja dołączona do wiadomości, która umożliwia jej adresatowi sprawdzenie zarówno autentyczności nadawcy, jak i integralności wiadomości przy użyciu klucza tajnego. Użycie maca opartego na mieszaniu[(HMAC)](https://csrc.nist.gov/publications/nistpubs/800-107-rev1/sp800-107-rev1.pdf)lub [mac opartego na szyfrach blokowych](https://csrc.nist.gov/publications/nistpubs/800-38B/SP_800-38B.pdf) jest dopuszczalne, o ile wszystkie podstawowe algorytmy szyfrowania mieszania lub symetryczne są również zatwierdzone do użytku; Obecnie obejmuje to funkcje HMAC-SHA2 (HMAC-SHA256, HMAC-SHA384 i HMAC-SHA512) oraz CMAC/OMAC1 i OMAC2 blokowe szyfry oparte na CMAC2 (są one oparte na AES).</p><p>Korzystanie z HMAC-SHA1 może być dopuszczalne ze względu na zgodność z platformami, ale konieczne będzie złożeniu wyjątku od tej procedury i poddania się przeglądowi kryptograficznemu w organizacji. Obcięcie hmaców do mniej niż 128 bitów jest niedozwolone. Przy użyciu metod klienta do mieszania klucza i danych nie jest zatwierdzony i musi przejść przeglądu Rady Kryptograficznej organizacji przed użyciem.</p>|

## <a name="use-only-approved-cryptographic-hash-functions"></a><a id="hash-functions"></a>Używaj tylko zatwierdzonych funkcji skrótu kryptograficznego

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | <p>Produkty muszą używać algorytmów mieszania z rodziny SHA-2 (SHA256, SHA384 i SHA512). Jeśli potrzebny jest krótszy skrót, na przykład 128-bitowa długość wyjściowa w celu dopasowania struktury danych zaprojektowanej z myślą o krótszym mieszaniu MD5, zespoły produktów mogą obcięć jeden z skrótów SHA2 (zazwyczaj SHA256). Należy zauważyć, że SHA384 jest obciętą wersją SHA512. Obcięcie skrótów kryptograficznych ze względów bezpieczeństwa do mniej niż 128 bitów jest niedozwolone. Nowy kod nie może używać algorytmów mieszania MD2, MD4, MD5, SHA-0, SHA-1 lub RIPEMD. Kolizje mieszania są obliczeniowo wykonalne dla tych algorytmów, co skutecznie je psuje.</p><p>Dozwolone algorytmy mieszania .NET dla zarządzanej wydajności kryptograficznej (w kolejności preferencji):</p><ul><li>SHA512Cng (zgodny z FIPS)</li><li>SHA384Cng (zgodny z FIPS)</li><li>SHA256Cng (zgodny z FIPS)</li><li>SHA512Managed (niezgodny z FIPS) (użyj SHA512 jako nazwy algorytmu w wywołaniach HashAlgorithm.Create lub CryptoConfig.CreateFromName)</li><li>SHA384Managed (niezgodny z FIPS) (użyj SHA384 jako nazwy algorytmu w wywołaniach HashAlgorithm.Create lub CryptoConfig.CreateFromName)</li><li>SHA256Managed (niezgodny z FIPS) (użyj SHA256 jako nazwy algorytmu w wywołaniach HashAlgorithm.Create lub CryptoConfig.CreateFromName)</li><li>SHA512CryptoServiceProvider (zgodny ze standardem FIPS)</li><li>SHA256CryptoServiceProvider (zgodny ze standardem FIPS)</li><li>SHA384CryptoServiceProvider (zgodny ze standardem FIPS)</li></ul>| 

## <a name="use-strong-encryption-algorithms-to-encrypt-data-in-the-database"></a><a id="strong-db"></a>Używanie silnych algorytmów szyfrowania do szyfrowania danych w bazie danych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Wybieranie algorytmu szyfrowania](https://technet.microsoft.com/library/ms345262(v=sql.130).aspx) |
| **Kroki** | Algorytmy szyfrowania definiują przekształcenia danych, których nie można łatwo odwrócić przez nieautoryzowanych użytkowników. SQL Server umożliwia administratorom i programistom wybór spośród kilku algorytmów, w tym DES, Triple DES, TRIPLE_DES_3KEY, RC2, RC4, 128-bitowy RC4, DESX, 128-bitowy AES, 192-bitowy system AES i 256-bitowy system AES |

## <a name="ssis-packages-should-be-encrypted-and-digitally-signed"></a><a id="ssis-signed"></a>Pakiety SSIS powinny być szyfrowane i podpisane cyfrowo

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Identyfikowanie źródła pakietów za pomocą podpisów cyfrowych,](https://msdn.microsoft.com/library/ms141174.aspx) [ograniczania zagrożeń i luk w zabezpieczeniach (Integration Services)](https://msdn.microsoft.com/library/bb522559.aspx) |
| **Kroki** | Źródłem pakietu jest osoba lub organizacja, która utworzyła pakiet. Uruchamianie pakietu z nieznanego lub niezaufanego źródła może być ryzykowne. Aby zapobiec nieautoryzowanemu manipulowaniu pakietami SSIS, należy używać podpisów cyfrowych. Ponadto, aby zapewnić poufność paczek podczas przechowywania/przesyłania, pakiety SSIS muszą być szyfrowane |

## <a name="add-digital-signature-to-critical-database-securables"></a><a id="securables-db"></a>Dodawanie podpisu cyfrowego do krytycznych zabezpieczaczy bazy danych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [DODAJ PODPIS (Transact-SQL)](https://msdn.microsoft.com/library/ms181700) |
| **Kroki** | W przypadkach, gdy należy zweryfikować integralność krytycznej bazy danych, należy używać podpisów cyfrowych. Securables bazy danych, takich jak procedura składowana, funkcja, zestaw lub wyzwalacz mogą być podpisane cyfrowo. Poniżej znajduje się przykład, kiedy może to być przydatne: Powiedzmy, że niezależny dostawca oprogramowania (niezależny dostawca oprogramowania) zapewnił wsparcie dla oprogramowania dostarczonego jednemu z ich klientów. Przed udzieleniem pomocy technicznej, isv będzie chciał upewnić się, że baza danych zabezpieczanych w oprogramowaniu nie został zmodyfikowany przez pomyłkę lub przez złośliwą próbę. Jeśli zabezpieczalny jest podpisany cyfrowo, firma isv może zweryfikować swój podpis cyfrowy i zweryfikować jego integralność.| 

## <a name="use-sql-server-ekm-to-protect-encryption-keys"></a><a id="ekm-keys"></a>Ochrona kluczy szyfrowania za pomocą serwera SQL EKM

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Zarządzanie kluczami rozszerzalnymi programu SQL Server (EKM)](https://msdn.microsoft.com/library/bb895340)— [rozszerzalne zarządzanie kluczami przy użyciu usługi Azure Key Vault (SQL Server)](https://msdn.microsoft.com/library/dn198405) |
| **Kroki** | Sql Server Extensible Key Management umożliwia klucze szyfrowania, które chronią pliki bazy danych mają być przechowywane w urządzeniu off-box, takich jak karta inteligentna, urządzenie USB lub moduł EKM/HSM. Umożliwia to również ochronę danych przed administratorami bazy danych (z wyjątkiem członków grupy sysadmin). Dane mogą być szyfrowane przy użyciu kluczy szyfrowania, do których tylko użytkownik bazy danych ma dostęp do zewnętrznego modułu EKM/HSM. |

## <a name="use-alwaysencrypted-feature-if-encryption-keys-should-not-be-revealed-to-database-engine"></a><a id="keys-engine"></a>Użyj funkcji AlwaysEncrypted, jeśli klucze szyfrowania nie powinny być ujawniane aparatowi bazy danych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | SQL Azure, OnPrem |
| **Atrybuty**              | Wersja SQL — V12, MsSQL2016 |
| **Odwołania**              | [Zawsze szyfrowane (aparat bazy danych)](https://msdn.microsoft.com/library/mt163865) |
| **Kroki** | Zawsze szyfrowane to funkcja przeznaczona do ochrony poufnych danych, takich jak numery kart kredytowych lub krajowe numery identyfikacyjne (np. numery ubezpieczenia społecznego USA), przechowywane w bazach danych usługi Azure SQL Database lub SQL Server. Zawsze szyfrowane umożliwia klientom szyfrowanie poufnych danych wewnątrz aplikacji klienckich i nigdy nie ujawniają kluczy szyfrowania do aparatu bazy danych (bazy danych SQL lub programu SQL Server). W rezultacie Always Encrypted zapewnia separację między tymi, którzy są właścicielami danych (i mogą je przeglądać) a tymi, którzy zarządzają danymi (ale nie powinni mieć dostępu) |

## <a name="store-cryptographic-keys-securely-on-iot-device"></a><a id="keys-iot"></a>Bezpieczne przechowywanie kluczy kryptograficznych na urządzeniu IoT

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Urządzenie IoT | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | System operacyjny urządzenia — rdzeń IoT systemu Windows, łączność urządzeń — zestaw SDK urządzeń IoT platformy Azure |
| **Odwołania**              | [Moduł TPM w systemie Windows IoT Core](https://developer.microsoft.com/windows/iot/docs/tpm), [Konfigurowanie modułu TPM w systemie Windows IoT Core](https://docs.microsoft.com/windows/iot-core/secure-your-device/setuptpm), [moduł TPM zestawu SDK urządzenia Usługi Azure IoT](https://github.com/Azure/azure-iot-hub-vs-cs/wiki/Device-Provisioning-with-TPM) |
| **Kroki** | Symetryczne lub certyfikatowe klucze prywatne bezpiecznie w pamięci chronionej sprzętowo, takiej jak układy TPM lub karty inteligentnej. System Windows 10 IoT Core obsługuje użytkownika modułu TPM i istnieje kilka https://docs.microsoft.com/windows/iot-core/secure-your-device/tpm#discrete-tpm-dtpmzgodnych modułów TPM, których można używać: . Zaleca się używanie modułu TPM oprogramowania układowego lub dyskretnego. Moduł TPM oprogramowania powinien być używany wyłącznie do celów programisty i testowania. Gdy moduł TPM jest dostępny, a klucze są aprowizacji w nim, kod, który generuje token powinien być zapisywany bez twardego kodowania żadnych poufnych informacji w nim. | 

### <a name="example"></a>Przykład
```
TpmDevice myDevice = new TpmDevice(0);
// Use logical device 0 on the TPM 
string hubUri = myDevice.GetHostName(); 
string deviceId = myDevice.GetDeviceId(); 
string sasToken = myDevice.GetSASToken(); 

var deviceClient = DeviceClient.Create( hubUri, AuthenticationMethodFactory. CreateAuthenticationWithToken(deviceId, sasToken), TransportType.Amqp); 
```
Jak widać, klucz podstawowy urządzenia nie jest obecny w kodzie. Zamiast tego jest przechowywany w modułie TPM w gnieździe 0. Urządzenie TPM generuje krótkotrwały token Sygnatury dostępu Współdzielonego, który jest następnie używany do łączenia się z centrum IoT Hub. 

## <a name="generate-a-random-symmetric-key-of-sufficient-length-for-authentication-to-iot-hub"></a><a id="random-hub"></a>Generowanie losowego klucza symetrycznego o wystarczającej długości do uwierzytelniania w Centrum IoT

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Brama w chmurze IoT | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Wybór bramy — usługa Azure IoT Hub |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Usługa IoT Hub zawiera rejestr tożsamości urządzenia i podczas inicjowania obsługi administracyjnej urządzenia automatycznie generuje losowy klucz symetryczny. Zaleca się użycie tej funkcji rejestru tożsamości usługi Azure IoT Hub do wygenerowania klucza używanego do uwierzytelniania. Usługa IoT Hub umożliwia również określenie klucza podczas tworzenia urządzenia. Jeśli klucz jest generowany poza Centrum IoT podczas inicjowania obsługi administracyjnej urządzenia, zaleca się utworzenie losowego klucza symetrycznego lub co najmniej 256 bitów. |

## <a name="ensure-a-device-management-policy-is-in-place-that-requires-a-use-pin-and-allows-remote-wiping"></a><a id="pin-remote"></a>Upewnij się, że obowiązują zasady zarządzania urządzeniami, które wymagają użycia kodu PIN i umożliwiają zdalne wycieranie

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Klient mobilny Dynamics CRM | 
| **Faza SDL**               | wdrażania |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Upewnij się, że obowiązują zasady zarządzania urządzeniami, które wymagają użycia kodu PIN i umożliwiają zdalne wycieranie |

## <a name="ensure-a-device-management-policy-is-in-place-that-requires-a-pinpasswordauto-lock-and-encrypts-all-data-eg-bitlocker"></a><a id="bitlocker"></a>Upewnij się, że obowiązują zasady zarządzania urządzeniami, które wymagają kodu PIN/hasła/automatycznej blokady i szyfrują wszystkie dane (np. funkcja BitLocker)

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Klient programu Dynamics CRM Outlook | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Upewnij się, że obowiązują zasady zarządzania urządzeniami, które wymagają kodu PIN/hasła/automatycznej blokady i szyfrują wszystkie dane (np. funkcja BitLocker) |

## <a name="ensure-that-signing-keys-are-rolled-over-when-using-identity-server"></a><a id="rolled-server"></a>Upewnij się, że klucze podpisywania są przerzucone podczas korzystania z serwera tożsamości

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Serwer tożsamości | 
| **Faza SDL**               | wdrażania |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Serwer tożsamości — klucze, podpisy i kryptografia](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html) |
| **Kroki** | Upewnij się, że klucze podpisywania są przerzucone podczas korzystania z serwera tożsamości. Łącze w sekcji odwołania wyjaśnia, jak należy to zaplanować bez powodowania awarii aplikacji korzystających z serwera tożsamości. |

## <a name="ensure-that-cryptographically-strong-client-id-client-secret-are-used-in-identity-server"></a><a id="client-server"></a>Upewnij się, że kryptograficznie silny identyfikator klienta, klucz tajny klienta są używane w serwerze tożsamości

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Serwer tożsamości | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | <p>Upewnij się, że kryptograficznie silny identyfikator klienta, klucz tajny klienta są używane w serwerze tożsamości. Podczas generowania identyfikatora klienta i klucza tajnego należy stosować następujące wytyczne:</p><ul><li>Generowanie losowego identyfikatora GUID jako identyfikatora klienta</li><li>Generowanie kryptograficznie losowego klucza 256-bitowego jako klucza tajnego</li></ul>|
