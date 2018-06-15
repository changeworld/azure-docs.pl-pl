---
title: Kryptografia — narzędzie Microsoft Threat modelowania - Azure | Dokumentacja firmy Microsoft
description: środki zaradcze w przypadku zagrożeń widoczne w narzędziu modelowania zagrożeń
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 5e5d487c4c793a49ce1d4ac17f6fcd672e09bb90
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30911662"
---
# <a name="security-frame-cryptography--mitigations"></a>Ramka zabezpieczeń: Kryptografia | Środki zaradcze 
| Produktów i usług | Artykuł |
| --------------- | ------- |
| **Aplikacja sieci Web** | <ul><li>[Użyj tylko zatwierdzone symetrycznego blok, szyfrowania i długości kluczy](#cipher-length)</li><li>[Użyj zatwierdzone tryby szyfrowania bloku i wektory inicjacji dla szyfrowania symetrycznego](#vector-ciphers)</li><li>[Użyj zatwierdzone asymetrycznych algorytmów, długości kluczy i uzupełniania](#padding)</li><li>[Użyj zatwierdzone losowych liczb generatory kodu](#numgen)</li><li>[Nie należy używać szyfrowania symetrycznego strumienia](#stream-ciphers)</li><li>[Użyj zatwierdzone algorytmy wyznaczania wartości skrótu MAC/HMAC/z kluczem](#mac-hash)</li><li>[Użyj tylko zatwierdzone funkcji skrótu kryptograficznego](#hash-functions)</li></ul> |
| **Baza danych** | <ul><li>[Użyj silnego szyfrowania algorytmów szyfrowania danych w bazie danych](#strong-db)</li><li>[Pakiety usług SSIS powinny być szyfrowane i podpisane cyfrowo](#ssis-signed)</li><li>[Dodaj podpis cyfrowy do obiektów zabezpieczanych krytyczne bazy danych](#securables-db)</li><li>[Użyj programu SQL server EKM do ochrony kluczy szyfrowania](#ekm-keys)</li><li>[Funkcja AlwaysEncrypted klucze szyfrowania nie powinny uzyskać dostęp do aparatu bazy danych](#keys-engine)</li></ul> |
| **Urządzenia IoT** | <ul><li>[Bezpieczne przechowywanie kluczy szyfrowania na urządzeniu IoT](#keys-iot)</li></ul> | 
| **Brama chmury IoT** | <ul><li>[Wygeneruje losowy klucz symetryczny wystarczającej długości do uwierzytelniania w Centrum IoT](#random-hub)</li></ul> | 
| **Klienta mobilnego programu Dynamics CRM** | <ul><li>[Upewnij się, że zasady zarządzania urządzeniami jest, że wymagane jest użycie numeru PIN i umożliwia zdalne czyszczenie danych](#pin-remote)</li></ul> | 
| **Klient programu Outlook Dynamics CRM** | <ul><li>[Upewnij się, że zasady zarządzania urządzenia w miejscu, które wymaga blokady auto-kodu PIN/hasła i szyfruje wszystkie dane (np. funkcją Bitlocker)](#bitlocker)</li></ul> | 
| **Tożsamości serwera** | <ul><li>[Upewnij się, że klucze podpisywania są przetwarzane przy użyciu tożsamości serwera](#rolled-server)</li><li>[Upewnij się, że identyfikator klienta silną kryptograficznie, klucz tajny klienta są używane na serwerze tożsamości](#client-server)</li></ul> | 

## <a id="cipher-length"></a>Użyj tylko zatwierdzone symetrycznego blok, szyfrowania i długości kluczy

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Produkty należy używać tylko symetrycznego blok, szyfrowania i związanych z nimi długości klucza, które zostały jawnie zatwierdzone przez doradcę kryptograficznego w Twojej organizacji. Zatwierdzone algorytmy symetryczne w firmie Microsoft obejmują następujące blok, szyfrowania:</p><ul><li>Nowy kod AES-128, AES 192 i AES 256 są dozwolone</li><li>Zgodności z poprzednimi wersjami z istniejącego kodu jest dopuszczalne trzech klucz 3DES</li><li>Przy użyciu szyfrowania symetrycznego bloku produktów:<ul><li>Advanced Encryption (Standard AES) jest wymagany dla nowego kodu</li><li>Klucz trzech triple Data Encryption Standard (3DES) jest dozwolone w istniejącym kodzie zgodności z poprzednimi wersjami</li><li>Wszystkie inne bloku szyfrów, w tym RC2, DES 2 klucz 3DES, DESX i bonito, może być używana tylko w celu odszyfrowania starych danych i muszą zostać zastąpione, jeśli jest używany do szyfrowania</li></ul></li><li>Dla algorytmów szyfrowania symetrycznego bloku minimalną długość klucza 128 bitów jest wymagana. Tylko algorytm szyfrowania bloku zalecane dla nowego kodu jest AES (AES-128, AES 192 i AES 256 są wszystkie dozwolone)</li><li>Trzy klucz 3DES jest obecnie akceptowane, jeśli już używani w istniejącym kodzie; Przejście do AES jest zalecane. DES, DESX RC2 i bonito są już uznawane za bezpieczne. Algorytmy te może być używana tylko w celu odszyfrowania istniejących danych ze względu na zgodność z poprzednimi wersjami, a dane powinny być ponownie szyfrowane, przy użyciu szyfrowania bloku zalecane</li></ul><p>Należy pamiętać, że wszystkie szyfrowania symetrycznego bloku musi być używany z trybu zatwierdzonych szyfrowania, który wymaga użycia wektora odpowiednie inicjowania (IV). Odpowiednie IV jest zwykle liczbę losową i nigdy nie wartością stałą</p><p>Starsze lub w inny sposób niezatwierdzonych algorytmów kryptograficznych i długości kluczy mniejszych do odczytywania istniejących danych (w przeciwieństwie do zapisywania nowych danych) dozwolone może być po Przejrzyj tablicy kryptograficznego w organizacji. Jednak należy pliku wyjątku względem tego wymagania. Ponadto w przedsiębiorstwach, produktów należy wziąć pod uwagę Administratorzy ostrzeżenie podczas słabe algorytmy kryptograficzne jest używany do odczytu danych. Takie ostrzeżenia powinna być wyjaśniające i możliwością wykonania akcji. W niektórych przypadkach może być odpowiednie grupy zasad kontroluje użycia funkcji słabe algorytmy kryptograficzne</p><p>Dozwolone algorytmy .NET dla zarządzanych elastyczność usług kryptograficznych (w kolejności preferencji)</p><ul><li>AesCng (zgodny ze standardem FIPS)</li><li>AuthenticatedAesCng (zgodny ze standardem FIPS)</li><li>AESCryptoServiceProvider (zgodny ze standardem FIPS)</li><li>AESManaged (z systemem innym niż — zgodne ze standardem FIPS)</li></ul><p>Należy pamiętać, że żaden z tych algorytmów można określić za pomocą `SymmetricAlgorithm.Create` lub `CryptoConfig.CreateFromName` metody bez wprowadzania zmian w pliku machine.config. Zauważ również, czy nosi nazwę AES w wersjach programu .NET przed .NET 3.5 `RijndaelManaged`, i `AesCng` i `AuthenticatedAesCng` są > dostępna za pośrednictwem portalu CodePlex i wymagają CNG w podstawowej wersji systemu operacyjnego</p>

## <a id="vector-ciphers"></a>Użyj zatwierdzone tryby szyfrowania bloku i wektory inicjacji dla szyfrowania symetrycznego

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Wszystkie szyfrowania symetrycznego bloku musi być używana z trybu zatwierdzonych szyfrowania symetrycznego. Tylko zatwierdzone tryby są CBC i CTS. W szczególności należy unikać Kod elektroniczny tryb działania książki (ECB); Użyj ECB wymaga wykonania przeglądu kryptograficznego tablicy w organizacji. Użycie wszystkich OFB, CFB, Ewidencyjne, CCM i GCM lub innych tryb szyfrowania musi zrecenzowane przez tablicy kryptograficznego w organizacji. Ponowne użycie tej samej wektor inicjowania (IV) z bloku szyfrów w "strumieniowych tryby szyfrów", takie jak Ewidencyjne, może spowodować zaszyfrowane dane, aby uzyskać dostęp. Wszystkie szyfrowania symetrycznego bloku musi również z wektora odpowiednie inicjowania (IV). Odpowiednie IV jest kryptograficznie silne, liczbę losową i nigdy nie stałej wartości. |

## <a id="padding"></a>Użyj zatwierdzone asymetrycznych algorytmów, długości kluczy i uzupełniania

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Użyj algorytmów kryptograficznych zabronione wprowadzono znaczące zagrożenie dla bezpieczeństwa produktu i należy unikać. Produktów należy używać tylko tych algorytmów kryptograficznych i skojarzone długości kluczy i wypełnieniem jawnie zatwierdzone przez kryptograficznego w organizacji.</p><ul><li>**RSA -** mogą być używane do szyfrowania, wymiana klucza i podpis. Szyfrowanie RSA, należy użyć tylko tryb uzupełniania OAEP lub RSA KEM. Tryb zgodności tylko uzupełniania w wersji 1.5 PKCS #1 może korzystać z istniejącego kodu. Użyj wartości null uzupełnienia jawnie jest niedozwolone. Klucze > = 2048 bitów jest wymagany dla nowego kodu. Istniejący kod może obsługiwać klucze < 2048 bitów tylko w przypadku wstecz zgodności po dokonaniu przeglądu przez kryptograficznego w organizacji. Klucze < 1024 bity może być używana tylko do odszyfrowywania weryfikowanie starych danych i muszą być zastąpiony, jeśli używane do operacji podpisywania lub szyfrowania</li><li>**ECDSA -** mogą służyć tylko podpis. ECDSA z > = klucze 256-bitowy jest wymagany dla nowego kodu. Na podstawie ECDSA podpisów muszą używać jednej z trzech krzywych NIST zatwierdzone (p-256, p-384 lub P521). Krzywe, które zostały przeanalizowane dokładnie może być używany tylko po dokonaniu przeglądu tablica kryptograficznego w organizacji.</li><li>**ECDH -** mogą służyć tylko wymiany kluczy. ECDH z > = klucze 256-bitowy jest wymagany dla nowego kodu. Na podstawie ECDH wymiany kluczy muszą używać jednej z trzech krzywych NIST, zatwierdzone (p-256, p-384 lub P521). Krzywe, które zostały przeanalizowane dokładnie może być używany tylko po dokonaniu przeglądu tablica kryptograficznego w organizacji.</li><li>**DSA -** można zaakceptować po przeglądu i zatwierdzania od tablicy kryptograficznego w organizacji. Skontaktuj się z Doradca zabezpieczeń można zaplanować przeglądu kryptograficznego tablicy w organizacji. Po zatwierdzeniu użytkowania DSA należy pamiętać, że konieczne będzie Zabroń używania kluczy mniej niż 2048 bitów długości. CNG obsługuje długości klucza 2048-bitowe i większa począwszy od systemu Windows 8.</li><li>**Diffie-Hellman -** może służyć do sesji tylko zarządzania kluczami. Długość klucza > = 2048 bitów jest wymagany dla nowego kodu. Istniejący kod może obsługiwać długości kluczy < 2048 bitów tylko dla zapewnienia zgodności po dokonaniu przeglądu przez kryptograficznego w organizacji. Klucze < 1024 bity nie mogą być używane.</li><ul>

## <a id="numgen"></a>Użyj zatwierdzone losowych liczb generatory kodu

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Produkty muszą używać zatwierdzonych generatory liczb losowych. Pseudolosowych funkcji, takich jak rand funkcji środowiska uruchomieniowego C, .NET Framework klasy System.Random lub funkcji systemu, takich jak GetTickCount w związku z tym nigdy nie należy w taki kod. Podwójne algorytm krzywej eliptycznej losowych liczb generator (DUAL_EC_DRBG) jest zabronione</p><ul><li>**CNG -** BCryptGenRandom (Użyj flagi BCRYPT_USE_SYSTEM_PREFERRED_RNG zalecane, chyba że obiekt wywołujący mogą zostać uruchomione w dowolnej IRQL większa niż 0 [czyli PASSIVE_LEVEL])</li><li>**CAPI -** cryptGenRandom</li><li>**Win32/64-** RtlGenRandom (Nowe implementacje powinny używać BCryptGenRandom lub CryptGenRandom) * rand_s — * SystemPrng (dla trybu jądra)</li><li>**. NET -** RNGCryptoServiceProvider lub RNGCng</li><li>**Aplikacje Sklepu Windows -** Windows.Security.Cryptography.CryptographicBuffer.GenerateRandom lub. GenerateRandomNumber</li><li>**Apple OS X (10.7+)/iOS(2.0+) -** int SecRandomCopyBytes (SecRandomRef losowych, liczba size_t, uint8_t \*bajtów)</li><li>**Apple OS X (< 10,7)-** Użyj/dev/losowe można pobrać liczby losowe</li><li>**Java(including Google Android Java Code) -** java.security.SecureRandom klasy. Zauważ, że dla systemu Android 4.3 (galaretki ziarna), deweloperzy musi wykonać Android zalecane rozwiązania i zaktualizować swoje aplikacje, aby jawnie zainicjować PRNG z entropii z /dev/urandom lub /dev/random</li></ul>|

## <a id="stream-ciphers"></a>Nie należy używać szyfrowania symetrycznego strumienia

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Szyfry symetrycznego strumienia, takich jak RC4, nie mogą być używane. Zamiast szyfrowania symetrycznego strumienia produktów należy używać szyfrowania bloku, w szczególności AES z kluczem o długości co najmniej 128 bitów. |

## <a id="mac-hash"></a>Użyj zatwierdzone algorytmy wyznaczania wartości skrótu MAC/HMAC/z kluczem

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Produkty należy używać tylko zatwierdzone kod uwierzytelniania wiadomości (MAC) lub algorytmy kodu (HMAC) uwierzytelniania na podstawie wyznaczania wartości skrótu wiadomości.</p><p>Kod uwierzytelniania wiadomości (MAC) jest dołączony do wiadomości, który umożliwia użytkownikowi Sprawdź zarówno autentyczności nadawcy i integralności wiadomości za pomocą klucza tajnego informacji. Użycie jednej podstawie skrótu MAC ([HMAC](http://csrc.nist.gov/publications/nistpubs/800-107-rev1/sp800-107-rev1.pdf)) lub [MAC z systemem szyfrowania bloku](http://csrc.nist.gov/publications/nistpubs/800-38B/SP_800-38B.pdf) jest dopuszczalna tak długo, jak są wszystkie podstawowe wyznaczania wartości skrótu lub algorytmów szyfrowania symetrycznego również zatwierdzone do użytku; aktualnie obejmuje funkcje algorytmu SHA2 HMAC (HMAC SHA256, HMAC SHA384 i HMAC SHA512) i CMAC/OMAC1 i OMAC2 blokowania na podstawie szyfrowania Mac (są one oparte na AES).</p><p>HMAC SHA1 mogą być dopuszczalne zgodności platformy, ale będzie konieczne pliku wyjątek od tej procedury i przechodzą przegląd usług kryptograficznych w organizacji. Obcięcie HMAC do mniej niż 128 bitów nie jest dozwolone. Przy użyciu metody do wyznaczania wartości skrótu klucza i danych nie jest zatwierdzona i muszą zostać poddane tablicy kryptograficznego organizacji zapoznać się przed użycia klienta.</p>|

## <a id="hash-functions"></a>Użyj tylko zatwierdzone funkcji skrótu kryptograficznego

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Produkty muszą używać z rodziny SHA-2 algorytmów wyznaczania wartości skrótu (SHA256, SHA384 i SHA512). Jeśli jest potrzebny krótszą wyznaczania wartości skrótu, takie jak długość danych wyjściowych 128-bitowego, w celu dopasowania to struktura danych o krótszej wyznaczania wartości skrótu MD5 pamiętać, zespoły produktu może obciąć jedną z wartości skrótu algorytmu SHA2 (zazwyczaj SHA256). Należy pamiętać, że SHA384 skrócona wersja SHA512. Obcięcie skróty kryptograficzne do mniej niż 128 bitów ze względów bezpieczeństwa nie jest dozwolone. Nowy kod nie może używać MD2, MD4, MD5, SHA-0, algorytm SHA-1 lub RIPEMD algorytmów wyznaczania wartości skrótu. Kolizji wyznaczania wartości skrótu są wykonalne dla te algorytmy, które skutecznie dzieli je.</p><p>Dozwolone .NET algorytmów wyznaczania wartości skrótu dla zarządzanych elastyczność usług kryptograficznych (w kolejności preferencji):</p><ul><li>SHA512Cng (zgodny ze standardem FIPS)</li><li>SHA384Cng (zgodny ze standardem FIPS)</li><li>SHA256Cng (zgodny ze standardem FIPS)</li><li>SHA512Managed (z systemem innym niż — zgodne ze standardem FIPS) (Użyj SHA512 jako nazwa algorytmu w wywołaniach HashAlgorithm.Create lub CryptoConfig.CreateFromName)</li><li>SHA384Managed (z systemem innym niż — zgodne ze standardem FIPS) (Użyj SHA384 jako nazwa algorytmu w wywołaniach HashAlgorithm.Create lub CryptoConfig.CreateFromName)</li><li>SHA256Managed (z systemem innym niż — zgodne ze standardem FIPS) (Użyj SHA256 jako nazwa algorytmu w wywołaniach HashAlgorithm.Create lub CryptoConfig.CreateFromName)</li><li>SHA512CryptoServiceProvider (zgodny ze standardem FIPS)</li><li>SHA256CryptoServiceProvider (zgodny ze standardem FIPS)</li><li>SHA384CryptoServiceProvider (zgodny ze standardem FIPS)</li></ul>| 

## <a id="strong-db"></a>Użyj silnego szyfrowania algorytmów szyfrowania danych w bazie danych

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Wybieranie algorytmu szyfrowania](https://technet.microsoft.com/library/ms345262(v=sql.130).aspx) |
| **Kroki** | Algorytmy szyfrowania zdefiniuj przekształcenia danych, których nie można łatwo cofnąć przez nieautoryzowanych użytkowników. SQL Server umożliwia Administratorzy i deweloperzy wybrać spośród kilku algorytmy, w tym DES, Triple DES, TRIPLE_DES_3KEY, RC2, RC4, 128-bitowego szyfrowania RC4, DESX, AES 128-bitowego, 192-bitowego szyfrowania AES i AES 256-bitowy |

## <a id="ssis-signed"></a>Pakiety usług SSIS powinny być szyfrowane i podpisane cyfrowo

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Identyfikować źródło pakietów z podpisami cyfrowymi](https://msdn.microsoft.com/library/ms141174.aspx), [zagrożeń i zapobieganie luki w zabezpieczeniach (usług integracji)](https://msdn.microsoft.com/library/bb522559.aspx) |
| **Kroki** | Źródło pakietu jest osoby lub organizacji, która opracowała pakiet. Uruchamianie pakietu z nieznaną lub niezaufaną źródła może być ryzykowne. Aby zapobiec modyfikowaniu nieautoryzowanego pakietów SSIS, można użyć podpisów cyfrowych. Ponadto zapewnienie poufności pakiety podczas przesyłania/magazynu, pakietów SSIS mają do zaszyfrowania |

## <a id="securables-db"></a>Dodaj podpis cyfrowy do obiektów zabezpieczanych krytyczne bazy danych

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [ADD SIGNATURE (Transact-SQL)](https://msdn.microsoft.com/library/ms181700) |
| **Kroki** | W przypadkach, gdy ma integralność krytyczne zabezpieczanego bazy danych należy zweryfikować można użyć podpisów cyfrowych. Zabezpieczanych obiektów bazy danych, takich jak procedury składowanej, funkcji, zestawu lub wyzwalacza mogą być podpisane cyfrowo. Poniżej przedstawiono przykładowy gdy może to być przydatne: Daj nam powiedzieć niezależnego dostawcy oprogramowania (niezależnego dostawcy oprogramowania) udostępnił pomocy technicznej do oprogramowania dostarczone do jednego ze swoich klientów. Przed zapewnianie pomocy technicznej, warto niezależnego dostawcy oprogramowania, aby zapewnić zabezpieczanego w oprogramowaniu bazy danych nie została zmieniona przez pomyłkę lub przez złośliwe próba. Jeśli zabezpieczanego jest podpisany cyfrowo, niezależnego dostawcy oprogramowania można zweryfikować podpisu cyfrowego i sprawdzić poprawności integralności.| 

## <a id="ekm-keys"></a>Użyj programu SQL server EKM do ochrony kluczy szyfrowania

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Rozszerzonego klucza zarządzania programu SQL Server (EKM)](https://msdn.microsoft.com/library/bb895340), [rozszerzonego zarządzania kluczami, za pomocą usługi Azure Key Vault (SQL Server)](https://msdn.microsoft.com/library/dn198405) |
| **Kroki** | Rozszerzone zarządzanie kluczami programu SQL Server umożliwia kluczy szyfrowania służących do ochrony plików bazy danych mają być przechowywane w urządzenie wyłączone pole, takie jak karty inteligentnej, urządzenie USB lub modułu EKM/HSM. Dzięki temu ochrony danych ze strony administratorów bazy danych (z wyjątkiem członków grupy sysadmin). Dane mogą być szyfrowane przy użyciu kluczy szyfrowania, że tylko użytkownik bazy danych ma dostęp do zewnętrznego modułu EKM/HSM. |

## <a id="keys-engine"></a>Funkcja AlwaysEncrypted klucze szyfrowania nie powinny uzyskać dostęp do aparatu bazy danych

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | SQL Azure, OnPrem |
| **Atrybuty**              | MsSQL2016 - 12 SQL |
| **Odwołania**              | [Zawsze zaszyfrowane (aparat bazy danych)](https://msdn.microsoft.com/library/mt163865) |
| **Kroki** | Zawsze zaszyfrowane jest funkcją zaprojektowane w celu ochrony poufnych danych, takich jak numery kart kredytowych lub numerów identyfikacyjnych national (np. USA numerów ubezpieczenia społecznego), przechowywane w bazach danych usługi Azure SQL Database lub SQL Server. Zawsze zaszyfrowane umożliwia klientom do szyfrowania poufnych danych w aplikacjach klienckich i nigdy nie podawaj kluczy szyfrowania z aparatem bazy danych (bazy danych SQL lub SQL Server). W związku z tym zawsze zaszyfrowane zapewnia oddzielenie tych, którzy należą dane (i mogły go wyświetlać) oraz tych, którzy zarządzania danymi (ale powinien nie mają dostępu) |

## <a id="keys-iot"></a>Bezpieczne przechowywanie kluczy szyfrowania na urządzeniu IoT

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Urządzenia IoT | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | Urządzenie systemu operacyjnego — Windows IoT Core, łączność urządzeń - zestawy SDK urządzenia Azure IoT |
| **Odwołania**              | [Moduł TPM w systemie Windows IoT Core](https://developer.microsoft.com/windows/iot/docs/tpm), [Konfigurowanie modułu TPM w systemie Windows IoT Core](https://developer.microsoft.com/windows/iot/win10/setuptpm), [TPM zestawem SDK urządzenia IoT Azure](https://github.com/Azure/azure-iot-hub-vs-cs/wiki/Device-Provisioning-with-TPM) |
| **Kroki** | Klucze Symmetric lub prywatny certyfikatu bezpiecznie na sprzętu chronione magazynu, takich jak moduły TPM lub karty inteligentnej. Windows 10 IoT Core obsługuje użytkownika modułu TPM i istnieje kilka zgodne moduły TPM, które mogą być używane: https://developer.microsoft.com/windows/iot/win10/tpm. Zaleca się za pomocą oprogramowania układowego lub odrębny modułu TPM. Moduł TPM oprogramowania należy używać tylko do programowania i testowania. Po modułu TPM jest dostępny i klucze są udostępniane w nim, kod, który generuje token powinien być zapisywany bez twardych kodowania żadnych poufnych informacji w nim. | 

### <a name="example"></a>Przykład
```
TpmDevice myDevice = new TpmDevice(0);
// Use logical device 0 on the TPM 
string hubUri = myDevice.GetHostName(); 
string deviceId = myDevice.GetDeviceId(); 
string sasToken = myDevice.GetSASToken(); 

var deviceClient = DeviceClient.Create( hubUri, AuthenticationMethodFactory. CreateAuthenticationWithToken(deviceId, sasToken), TransportType.Amqp); 
```
Jak widać, klucz podstawowy urządzenia nie jest obecny w kodzie. Zamiast tego jest on przechowywany w module TPM w gniazda 0. Moduł TPM urządzenie wygeneruje krótkim okresie tokenu sygnatury dostępu Współdzielonego, który jest następnie używany do nawiązania połączenia Centrum IoT. 

## <a id="random-hub"></a>Wygeneruje losowy klucz symetryczny wystarczającej długości do uwierzytelniania w Centrum IoT

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Brama chmury IoT | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | Wybór bramy - Centrum IoT Azure |
| **Odwołania**              | ND  |
| **Kroki** | Centrum IoT zawiera urządzenia rejestru tożsamości i podczas inicjowania obsługi administracyjnej urządzeniu, automatycznie generuje losowe klucza symetrycznego. Zalecane jest, aby użyć tej funkcji rejestru tożsamości Centrum IoT Azure, aby wygenerować klucz używany do uwierzytelniania. Centrum IoT umożliwia również klucz, aby określić podczas tworzenia urządzenia. Jeśli klucz został wygenerowany poza Centrum IoT podczas inicjowania obsługi urządzeń, zalecane jest tworzenie losowego klucza symetrycznego lub co najmniej 256 bitów. |

## <a id="pin-remote"></a>Upewnij się, że zasady zarządzania urządzeniami jest, że wymagane jest użycie numeru PIN i umożliwia zdalne czyszczenie danych

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dynamics CRM Mobile Client | 
| **Faza SDL**               | Wdrożenie |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Upewnij się, że zasady zarządzania urządzeniami jest, że wymagane jest użycie numeru PIN i umożliwia zdalne czyszczenie danych |

## <a id="bitlocker"></a>Upewnij się, że zasady zarządzania urządzenia w miejscu, które wymaga blokady auto-kodu PIN/hasła i szyfruje wszystkie dane (np. funkcją Bitlocker)

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Klient programu Outlook Dynamics CRM | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Upewnij się, że zasady zarządzania urządzenia w miejscu, które wymaga blokady auto-kodu PIN/hasła i szyfruje wszystkie dane (np. funkcją Bitlocker) |

## <a id="rolled-server"></a>Upewnij się, że klucze podpisywania są przetwarzane przy użyciu tożsamości serwera

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Tożsamości serwera | 
| **Faza SDL**               | Wdrożenie |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Tożsamość serwera — kluczy, podpisy i szyfrowania ](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html) |
| **Kroki** | Upewnij się, że klucze podpisywania są przetwarzane przy użyciu tożsamości serwera. Link w sekcji odwołań wyjaśniono, jak to planowane bez powodowania przestojów do jednostki uzależnionej na serwerze tożsamości aplikacji. |

## <a id="client-server"></a>Upewnij się, że identyfikator klienta silną kryptograficznie, klucz tajny klienta są używane na serwerze tożsamości

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Tożsamości serwera | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Upewnij się, że identyfikator klienta silną kryptograficznie, klucz tajny klienta są używane na serwerze tożsamości. Podczas generowania identyfikator klienta i klucz tajny należy użyć następujących wytycznych:</p><ul><li>Generowanie losowy identyfikator GUID jako Identyfikatora klienta</li><li>Generuj kryptograficznie losowe 256-bitowego klucza jako klucz tajny</li></ul>|
