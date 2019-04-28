---
title: Kryptografia — narzędzie do modelowania zagrożeń firmy Microsoft — Azure | Dokumentacja firmy Microsoft
description: środki zaradcze dotyczące zagrożeń, widoczne w narzędziu do modelowania zagrożeń
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 608792d8389a87bad3521d3a48947b20dd036d67
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62121218"
---
# <a name="security-frame-cryptography--mitigations"></a>Ramka zabezpieczeń: Kryptografia | Środki zaradcze 

| Produkt/usługę | Artykuł |
| --------------- | ------- |
| **Aplikacja sieci Web** | <ul><li>[Użyj tylko zatwierdzonych symetrycznego blok, szyfrowania i długości kluczy](#cipher-length)</li><li>[Użyj zatwierdzone tryby szyfrowania bloku i wektory inicjacji dla mechanizmów szyfrowania symetrycznego](#vector-ciphers)</li><li>[Użyj zatwierdzone asymetrycznych algorytmów, długości kluczy i dopełnienie](#padding)</li><li>[Użyj zatwierdzone generatorów liczb losowych](#numgen)</li><li>[Nie należy używać szyfrowania symetrycznego strumienia](#stream-ciphers)</li><li>[Użyj zatwierdzone algorytmy wyznaczania wartości skrótu MAC/HMAC/opartych na kluczach](#mac-hash)</li><li>[Użyj tylko zatwierdzonych funkcji mieszania](#hash-functions)</li></ul> |
| **Baza danych** | <ul><li>[Użyj algorytmów silnego szyfrowania do szyfrowania danych w bazie danych](#strong-db)</li><li>[Powinien być zaszyfrowany i podpisany cyfrowo pakietów usług SSIS](#ssis-signed)</li><li>[Dodaj podpis cyfrowy do obiektów zabezpieczanych krytyczne bazy danych](#securables-db)</li><li>[Użyj programu SQL server EKM do ochrony kluczy szyfrowania](#ekm-keys)</li><li>[Funkcja AlwaysEncrypted klucze szyfrowania, nie powinien uzyskać dostęp do aparatu bazy danych](#keys-engine)</li></ul> |
| **Urządzenia IoT** | <ul><li>[Bezpiecznie Store klucze szyfrowania na urządzeniu IoT](#keys-iot)</li></ul> | 
| **IoT Cloud Gateway** | <ul><li>[Wygeneruje losowy klucz symetryczny wystarczającej długości do uwierzytelniania w usłudze IoT Hub](#random-hub)</li></ul> | 
| **Dynamics CRM Mobile Client** | <ul><li>[Upewnij się, że zasady zarządzania urządzeniami jest w miejscu, wymagane jest użycie numeru PIN, która zezwala na zdalne czyszczenie danych](#pin-remote)</li></ul> | 
| **Klient programu Outlook usługi Dynamics CRM** | <ul><li>[Upewnij się, że zasady zarządzania urządzeniami znajduje się w miejscu, które wymaga kodu PIN/hasło/automatyczne blokowanie i szyfruje wszystkie dane (np. funkcją BitLocker)](#bitlocker)</li></ul> | 
| **Tożsamość serwera** | <ul><li>[Upewnij się, że klucze podpisywania są przenoszone podczas korzystania z tożsamości serwera](#rolled-server)</li><li>[Upewnij się, że identyfikator klienta silną kryptograficznie, klucz tajny klienta używany na serwerze tożsamości](#client-server)</li></ul> | 

## <a id="cipher-length"></a>Użyj tylko zatwierdzonych symetrycznego blok, szyfrowania i długości kluczy

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Produkty, należy użyć tylko symetrycznego blok, szyfrowania i skojarzone długości kluczy, które zostały jawnie zatwierdzone przez usługę Advisor kryptograficznego w Twojej organizacji. Zatwierdzone algorytmy symetryczne w firmie Microsoft obejmują następujące szyfrów blokowych:</p><ul><li>Nowy kod algorytmu AES-128, AES-192 i AES-256 są dopuszczalne</li><li>Dla zgodności z poprzednimi wersjami z istniejącym kodem 3DES trzech klucza jest dopuszczalne</li><li>W przypadku produktów za pomocą mechanizmów szyfrowania symetrycznego bloku:<ul><li>Zaawansowane Encryption Standard (AES) jest wymagana dla nowego kodu</li><li>Klucz trzech triple Data Encryption Standard (3DES) jest dozwolone w istniejącym kodzie zgodności z poprzednimi wersjami</li><li>Wszystkie inne bloku szyfrów, w tym RC2, DES, 2 klucz algorytmu 3DES, DESX i bonita, może być używana tylko w celu odszyfrowania starych danych i muszą zostać zastąpione, jeśli używana na potrzeby szyfrowania</li></ul></li><li>W przypadku algorytmów szyfrowania symetrycznego bloku wymagane jest minimalną długość klucza wynosi 128 bitów. Tylko algorytmu szyfrowania bloku zalecany dla nowego kodu jest AES (AES-128, AES-192 i AES-256 wyjscia wszystko)</li><li>Klucz trzech algorytm 3DES jest obecnie akceptowane, jeśli już w użyciu w istniejącym kodzie; Zaleca się przejście do szyfrowania AES. DES, DESX, RC2 i bonito są już traktowane jako bezpieczne. Te algorytmy może być używana tylko w celu odszyfrowania istniejących danych do celów zgodności z poprzednimi wersjami i jest ponownie szyfrować dane przy użyciu szyfrowania bloku zalecane</li></ul><p>Należy pamiętać, że wszystkie symetrycznego blok, szyfrowania musi być używany z trybu zatwierdzonych szyfrowania, który wymaga użycia wektora inicjowania odpowiednich (IV). Odpowiednie IV zazwyczaj jest liczbę losową i nigdy nie wartością stałą</p><p>Użyj starszych lub w inny sposób niezatwierdzonych algorytmów kryptograficznych i długości kluczy mniejszych związany z odczytem istniejących danych (w przeciwieństwie do zapisywania nowych danych) może być dozwolony, po Przejrzyj tablicy Crypto Twojej organizacji. Należy jednak pliku dla wyjątku dla tego wymogu. Ponadto we wdrożeniach enterprise produktów należy rozważyć Administratorzy ostrzeżenie, gdy słabego szyfrowania jest używany do odczytywania danych. Takie ostrzeżenia powinny być wyjaśniające i możliwością wykonywania akcji. W niektórych przypadkach może być odpowiednie zasady grupy kontroluje użycia funkcji słabego szyfrowania</p><p>Dozwolone algorytmy .NET zarządzanych elastyczność usług kryptograficznych (w kolejności preferencji)</p><ul><li>AesCng (zgodny ze standardem FIPS)</li><li>AuthenticatedAesCng (zgodny ze standardem FIPS)</li><li>AESCryptoServiceProvider (zgodny ze standardem FIPS)</li><li>AESManaged (FIPS niezgodne ze specyfikacją)</li></ul><p>Należy pamiętać, że żadna z tych algorytmów można określić za pomocą `SymmetricAlgorithm.Create` lub `CryptoConfig.CreateFromName` metody bez wprowadzania zmian w pliku machine.config. Należy również zauważyć, nazwie AES w wersjach programu .NET przed .NET 3.5 `RijndaelManaged`, i `AesCng` i `AuthenticatedAesCng` są > dostępne za pośrednictwem portalu CodePlex i wymagają CNG w podstawowym systemie operacyjnym</p>

## <a id="vector-ciphers"></a>Użyj zatwierdzone tryby szyfrowania bloku i wektory inicjacji dla mechanizmów szyfrowania symetrycznego

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Wszystkich mechanizmów szyfrowania symetrycznego bloku musi być używana z trybu zatwierdzonych szyfrowania symetrycznego. Tylko tryby zatwierdzonych są CBC i CTS. W szczególności należy unikać Kod elektroniczny książki (ECB) tryb działania. Użyj ECB wymaga wykonania przeglądu tablicy Crypto Twojej organizacji. Użycie wystąpień OFB, CFB, Ewidencyjne, CCM i GCM lub inny tryb szyfrowania musi przeglądane przez tablicę Crypto Twojej organizacji. Ponowne użycie tego samego wektor inicjowania (IV) przy użyciu szyfrów blokowych w "streaming tryby szyfrów" takich jak Ewidencyjne, może spowodować zaszyfrowane dane, aby uzyskać dostęp. Wszystkich mechanizmów szyfrowania symetrycznego bloku musi też być używane z wektor inicjowania odpowiednich (IV). Odpowiednie Inicjacji jest kryptograficznie silnej, losową liczbę i nigdy nie wartość stałą. |

## <a id="padding"></a>Użyj zatwierdzone asymetrycznych algorytmów, długości kluczy i dopełnienie

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Używanie zakazanych algorytmów kryptograficznych wprowadza podwyższenie ryzyka związanego z zabezpieczeniami produktu i należy unikać. Produkty należy użyć tylko te algorytmy kryptograficzne wraz ze skojarzonymi długości kluczy i dopełnienie, które zostały jawnie zatwierdzone przez tablicę Crypto Twojej organizacji.</p><ul><li>**RSA -** mogą być używane do szyfrowania i wymiany kluczy podpisu. Szyfrowanie RSA, należy użyć tylko tryby dopełnienie OAEP lub RSA KEM. Dopełnienie tryb tylko w trybie zgodności w wersji 1.5 PKCS #1 może korzystać z istniejącego kodu. Użyj dopełnienia zerowego jawnie jest niedozwolone. Klucze > = 2048 bitów jest wymagany dla nowego kodu. Istniejący kod może obsługiwać klucze < 2048 bitów tylko w przypadku wstecznej zgodności po dokonaniu przeglądu przez tablicę Crypto Twojej organizacji. Klucze < 1024 bity może być używana tylko dla deszyfrowania weryfikowanie starych danych i muszą być zastąpione, jeśli używane do szyfrowania lub operacji podpisywania</li><li>**ECDSA -** może być używany do podpisu tylko. ECDSA z > = 256-bitowe klucze są wymagane dla nowego kodu. Na podstawie ECDSA podpisów muszą używać jednej z trzech krzywych NIST, zatwierdzone (p-256, p-384 lub P521). Krzywe, które zostały przeanalizowane dokładnie mogą być używane tylko po dokonaniu przeglądu z tablicą Crypto Twojej organizacji.</li><li>**ECDH -** mogą być używane do tylko w przypadku wymiany klucza. ECDH z > = 256-bitowe klucze są wymagane dla nowego kodu. Na podstawie ECDH wymiany kluczy, należy użyć jednej z trzech krzywych NIST, zatwierdzone (p-256, p-384 lub P521). Krzywe, które zostały przeanalizowane dokładnie mogą być używane tylko po dokonaniu przeglądu z tablicą Crypto Twojej organizacji.</li><li>**DSA -** można zaakceptować po przeglądu i zatwierdzania z tablicy Crypto Twojej organizacji. Skontaktuj się z Doradca zabezpieczeń można zaplanować Przegląd tablicy Crypto Twojej organizacji. Jeśli korzystanie z DSA zostanie zatwierdzony, należy pamiętać, że konieczne będzie Stanów Zjednoczonych zabraniają używania kluczy mniej niż 2048 bitów długości. CNG obsługuje 2048-bitowe i większa długość klucza, począwszy od systemu Windows 8.</li><li>**Grupa Diffie'ego-Hellmana -** mogą być używane do sesji tylko zarządzanie kluczami. Długość klucza > = 2048 bitów jest wymagany dla nowego kodu. Istniejący kod może obsługiwać długości kluczy < 2048 bitów tylko w przypadku wstecznej zgodności po dokonaniu przeglądu przez tablicę Crypto Twojej organizacji. Klucze nie mogą być używane < 1024 bity.</li><ul>

## <a id="numgen"></a>Użyj zatwierdzone generatorów liczb losowych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Produkty, należy użyć zatwierdzonych generatorów liczb losowych. Pseudolosowych funkcje, takie jak rand funkcji środowiska uruchomieniowego języka C, klasy .NET Framework System.Random lub funkcjami systemowymi, takimi GetTickCount dlatego nie może być używane w taki kod. Użyj podwójnego krzywej eliptycznej generator (DUAL_EC_DRBG) algorytmu liczb jest zabronione.</p><ul><li>**CNG -** BCryptGenRandom (Użyj flagi BCRYPT_USE_SYSTEM_PREFERRED_RNG zalecana, chyba że obiekt wywołujący mogą zostać uruchomione w dowolnej IRQL większa niż 0 [czyli PASSIVE_LEVEL])</li><li>**CAPI -** cryptGenRandom</li><li>**Win32/64-** RtlGenRandom (nowych wdrożeniach należy używać BCryptGenRandom lub CryptGenRandom) * rand_s — * SystemPrng (dla trybu jądra)</li><li>**. NET -** RNGCryptoServiceProvider lub RNGCng</li><li>**Aplikacje Windows Store -** Windows.Security.Cryptography.CryptographicBuffer.GenerateRandom lub. GenerateRandomNumber</li><li>**Apple OS X (10.7+)/iOS(2.0+) -** int SecRandomCopyBytes (SecRandomRef losowych, size_t count, uint8_t \*bajtów)</li><li>**Apple OS X (< 10.7)-** umożliwia/dev/losowych liczb losowych do pobrania</li><li>**Java(including Google Android Java Code) -** java.security.SecureRandom klasy. Pamiętaj, że dla systemu Android 4.3 (Jelly Bean) deweloperzy muszą systemu android, zalecane rozwiązania i aktualizować aplikacje usługi można jawnie zainicjować PRNG z entropii /dev/urandom lub /dev/random</li></ul>|

## <a id="stream-ciphers"></a>Nie należy używać szyfrowania symetrycznego strumienia

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Mechanizmów szyfrowania symetrycznego strumienia, takich jak RC4, nie może być używany. Zamiast mechanizmów szyfrowania symetrycznego strumienia produktów należy używać szyfrowania bloku, w szczególności AES z kluczem o długości co najmniej 128 bitów. |

## <a id="mac-hash"></a>Użyj zatwierdzone algorytmy wyznaczania wartości skrótu MAC/HMAC/opartych na kluczach

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Produkty, należy użyć tylko zatwierdzone kod uwierzytelniania wiadomości (MAC) lub algorytmy (HMAC) kod uwierzytelniania wiadomości bazujących na skrótach.</p><p>Kod uwierzytelniania wiadomości (MAC) to fragment informacji w załączniku do wiadomości, który umożliwia użytkownikowi Sprawdź zarówno autentyczności nadawcy i integralności wiadomości za pomocą klucza tajnego. Użycie obu bazujących na skrótach komputera MAC ([HMAC](https://csrc.nist.gov/publications/nistpubs/800-107-rev1/sp800-107-rev1.pdf)) lub [szyfrowania blokowego MAC](https://csrc.nist.gov/publications/nistpubs/800-38B/SP_800-38B.pdf) jest dozwolona jako długo, jak wszystkie bazowego hash "lub" szyfrowania symetrycznego algorytmy również zostały zatwierdzone do użytku; obecnie dotyczy to Funkcje algorytmu SHA2 HMAC (HMAC SHA256 i HMAC SHA384 HMAC SHA512) i CMAC/OMAC1 i OMAC2 blokowania na podstawie szyfrowania Mac (są one oparte na AES).</p><p>HMAC-SHA1 mogą być zezwalającym na zgodność z platformą, ale konieczne będzie plik w wyjątek od tej procedury i poddawane przeglądu kryptograficznego Twojej organizacji. Obcięcie HMAC do mniej niż 128 bitów jest niedozwolone. Przy użyciu metody służące do wyznaczania wartości skrótu klucza i danych nie jest zatwierdzona i muszą zostać poddane tablicy Crypto organizacji zapoznać się przed Użyj klienta.</p>|

## <a id="hash-functions"></a>Użyj tylko zatwierdzonych funkcji mieszania

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Produkty korzystać tylko z rodziny SHA-2 algorytmów wyznaczania wartości skrótu (SHA256, SHA384 i SHA512). Jeśli krótszy wyznaczania wartości skrótu jest potrzebny, takie jak długość danych wyjściowych 128-bitowego, aby dopasować strukturę danych zaprojektowane z krótszą Skrót MD5, należy pamiętać, zespoły produktu może obciąć jeden skróty algorytmu SHA2 (zazwyczaj SHA256). Należy pamiętać, że SHA384 skrócona wersja SHA512. Obcięcie skróty kryptograficzne do mniej niż 128 bitów ze względów bezpieczeństwa nie jest dozwolone. Nowy kod nie mogą używać MD2 MD4, MD5, SHA-0, algorytm SHA-1 lub RIPEMD algorytmy wyznaczania wartości skrótu. Kolizji wyznaczania wartości skrótu są wykonalne te algorytmy, które skutecznie dzieli je.</p><p>Dozwolone algorytmy wyznaczania wartości skrótu .NET zarządzanych elastyczność usług kryptograficznych (w kolejności preferencji):</p><ul><li>SHA512Cng (zgodny ze standardem FIPS)</li><li>SHA384Cng (zgodny ze standardem FIPS)</li><li>SHA256Cng (zgodny ze standardem FIPS)</li><li>SHA512Managed (FIPS niezgodne ze specyfikacją) (Użyj SHA512 jako nazwa algorytmu w wywołaniach HashAlgorithm.Create lub CryptoConfig.CreateFromName)</li><li>SHA384Managed (FIPS niezgodne ze specyfikacją) (Użyj SHA384 jako nazwa algorytmu w wywołaniach HashAlgorithm.Create lub CryptoConfig.CreateFromName)</li><li>SHA256Managed (FIPS niezgodne ze specyfikacją) (Użyj SHA256 jako nazwa algorytmu w wywołaniach HashAlgorithm.Create lub CryptoConfig.CreateFromName)</li><li>SHA512CryptoServiceProvider (zgodny ze standardem FIPS)</li><li>SHA256CryptoServiceProvider (zgodny ze standardem FIPS)</li><li>SHA384CryptoServiceProvider (zgodny ze standardem FIPS)</li></ul>| 

## <a id="strong-db"></a>Użyj algorytmów silnego szyfrowania do szyfrowania danych w bazie danych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Wybieranie algorytmu szyfrowania](https://technet.microsoft.com/library/ms345262(v=sql.130).aspx) |
| **Kroki** | Algorytmy szyfrowania zdefiniować przekształceń danych, które nie można łatwo cofnąć przez nieautoryzowanych użytkowników. SQL Server zezwala na administratorów i deweloperów wybrać spośród wielu algorytmów, m.in. DES Triple DES, TRIPLE_DES_3KEY, RC2, RC4, 128-bitowego szyfrowania RC4, DESX, 128-bitowego szyfrowania AES, 192-bitowego szyfrowania AES i szyfrowania AES 256-bitowego |

## <a id="ssis-signed"></a>Powinien być zaszyfrowany i podpisany cyfrowo pakietów usług SSIS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Zidentyfikuj źródło pakietów za pomocą podpisów cyfrowych](https://msdn.microsoft.com/library/ms141174.aspx), [zagrożeń i luk w zabezpieczeniach zapobieganie (usług Integration Services)](https://msdn.microsoft.com/library/bb522559.aspx) |
| **Kroki** | Źródło pakietu jest osobę lub organizację, która utworzyła pakiet. Uruchamianie pakietu z nieznanej lub niezaufanej źródła może być ryzykowne. Aby zapobiec nieautoryzowanemu modyfikowaniu pakietów SSIS, należy używać podpisów cyfrowych. Ponadto w celu zapewnienia poufności pakiety podczas magazynowania/przesyłania, pakiety usług SSIS mieć szyfrowania |

## <a id="securables-db"></a>Dodaj podpis cyfrowy do obiektów zabezpieczanych krytyczne bazy danych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Dodaj podpis (Transact-SQL)](https://msdn.microsoft.com/library/ms181700) |
| **Kroki** | W przypadkach, w której integralność krytyczne zabezpieczanego bazy danych ma do weryfikacji należy używać podpisów cyfrowych. Zabezpieczanych obiektów bazy danych, takich jak procedury składowanej, funkcji, zestawu lub wyzwalacza mogą być podpisane cyfrowo. Poniżej przedstawiono przykład, gdy może to być przydatne: Poinformuj nas, że ISV (niezależny dostawca oprogramowania) ma pod warunkiem, wsparcie dla oprogramowania dostarczone do jednego ze swoich klientów. Przed świadczenie pomocy technicznej, niezależny dostawca oprogramowania, będzie chciała upewnij się, bazę danych zabezpieczanego w oprogramowaniu nie został zmodyfikowany przez pomyłkę lub przez złośliwe próby. Jeśli zabezpieczanego są podpisane cyfrowo, niezależny dostawca oprogramowania można sprawdzić podpis cyfrowy i sprawdzić jego integralność.| 

## <a id="ekm-keys"></a>Użyj programu SQL server EKM do ochrony kluczy szyfrowania

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [SQL Server rozszerzonego klucza Management (EKM)](https://msdn.microsoft.com/library/bb895340), [Rozszerzalne zarządzanie kluczami za pomocą usługi Azure Key Vault (SQL Server)](https://msdn.microsoft.com/library/dn198405) |
| **Kroki** | Rozszerzone zarządzanie kluczami programu SQL Server umożliwia kluczy szyfrowania służących do ochrony plików bazy danych, które mają być przechowywane w urządzeniu poza pole takie jak karty inteligentnej, urządzenia USB lub moduł EKM/sprzętowego modułu zabezpieczeń. Umożliwia to również ochronę administratorów bazy danych (z wyjątkiem członkowie grupy sysadmin). Dane mogą być szyfrowane przy użyciu kluczy szyfrowania, że tylko użytkownika bazy danych ma dostęp do zewnętrznego modułu EKM/sprzętowego modułu zabezpieczeń. |

## <a id="keys-engine"></a>Funkcja AlwaysEncrypted klucze szyfrowania, nie powinien uzyskać dostęp do aparatu bazy danych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | SQL Azure, OnPrem |
| **Atrybuty**              | MsSQL2016 wersja — wersja V12, SQL |
| **Odwołania**              | [Zawsze szyfrowane (aparat bazy danych)](https://msdn.microsoft.com/library/mt163865) |
| **Kroki** | Always Encrypted to funkcja zaprojektowanych w celu ochrony poufnych danych, takich jak numery kart kredytowych czy narodowe numery identyfikacyjne (np. USA numery ubezpieczenia społecznego), przechowywane w bazach danych Azure SQL Database lub SQL Server. Funkcja Always Encrypted umożliwia klientom szyfrowanie cennych danych wewnątrz aplikacji klienckich i nigdy nie ujawniania kluczy szyfrowania z aparatem bazy danych (bazy danych SQL Database lub SQL Server). W wyniku Always Encrypted umożliwia rozdzielenie tych, którzy są właścicielami danych (i mogą je wyświetlać) oraz tych, którzy zarządzania danymi (ale nie powinny mieć dostępu) |

## <a id="keys-iot"></a>Bezpiecznie Store klucze szyfrowania na urządzeniu IoT

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Urządzenia IoT | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | System operacyjny urządzenia — Windows IoT Core, łączność urządzeń — zestawy SDK urządzeń Azure IoT |
| **Odwołania**              | [Moduł TPM w systemie Windows IoT Core](https://developer.microsoft.com/windows/iot/docs/tpm), [Konfigurowanie modułu TPM na Windows IoT Core](https://docs.microsoft.com/windows/iot-core/secure-your-device/setuptpm), [TPM zestawu SDK urządzenia IoT platformy Azure](https://github.com/Azure/azure-iot-hub-vs-cs/wiki/Device-Provisioning-with-TPM) |
| **Kroki** | Symmetric lub prywatnego certyfikatu kluczy bezpiecznie w sprzęcie chronione magazynu, takich jak mikroukładami modułu TPM lub karty inteligentnej. Windows 10 IoT Core obsługuje użytkownika modułu TPM wiąże się z kilku zgodne TPM, które mogą być używane: https://docs.microsoft.com/windows/iot-core/secure-your-device/tpm#discrete-tpm-dtpm. Zalecane jest użycie oprogramowania układowego lub odrębny moduł TPM. Moduł TPM oprogramowania można używać tylko do celów projektowania i testowania. Gdy moduł TPM jest dostępny i klucze są aprowizowane w nim, kod, który generuje token powinny być zapisywane bez twardych kodowania jakichkolwiek poufnych informacji w nim. | 

### <a name="example"></a>Przykład
```
TpmDevice myDevice = new TpmDevice(0);
// Use logical device 0 on the TPM 
string hubUri = myDevice.GetHostName(); 
string deviceId = myDevice.GetDeviceId(); 
string sasToken = myDevice.GetSASToken(); 

var deviceClient = DeviceClient.Create( hubUri, AuthenticationMethodFactory. CreateAuthenticationWithToken(deviceId, sasToken), TransportType.Amqp); 
```
Jak widać, klucz podstawowy urządzenie nie jest obecny w kodzie. Zamiast tego jest on przechowywany w module TPM w gnieździe 0. Urządzenia TPM generuje krótkotrwałe tokenu sygnatury dostępu Współdzielonego, który jest następnie używany do łączenia z usługą IoT Hub. 

## <a id="random-hub"></a>Wygeneruje losowy klucz symetryczny wystarczającej długości do uwierzytelniania w usłudze IoT Hub

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | IoT Cloud Gateway | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | Wybór bramy — usługi Azure IoT Hub |
| **Odwołania**              | ND  |
| **Kroki** | Usługa IoT Hub zawiera rejestr tożsamości urządzenia i podczas aprowizowania urządzenia, automatycznie generuje losowy klucz symetryczny. Zalecane jest, aby użyć tej funkcji w rejestrze tożsamości usługi Azure IoT Hub Wygeneruj klucz używany do uwierzytelniania. Usługa IoT Hub umożliwia również klucz należy określić podczas tworzenia urządzenia. Jeśli klucz jest generowany poza usługi IoT Hub podczas inicjowania obsługi urządzeń, zalecane jest utworzyć losowy klucz symetryczny lub co najmniej 256 bitów. |

## <a id="pin-remote"></a>Upewnij się, że zasady zarządzania urządzeniami jest w miejscu, wymagane jest użycie numeru PIN, która zezwala na zdalne czyszczenie danych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dynamics CRM Mobile Client | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Upewnij się, że zasady zarządzania urządzeniami jest w miejscu, wymagane jest użycie numeru PIN, która zezwala na zdalne czyszczenie danych |

## <a id="bitlocker"></a>Upewnij się, że zasady zarządzania urządzeniami znajduje się w miejscu, które wymaga kodu PIN/hasło/automatyczne blokowanie i szyfruje wszystkie dane (np. funkcją BitLocker)

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Klient programu Outlook usługi Dynamics CRM | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Upewnij się, że zasady zarządzania urządzeniami znajduje się w miejscu, które wymaga kodu PIN/hasło/automatyczne blokowanie i szyfruje wszystkie dane (np. funkcją BitLocker) |

## <a id="rolled-server"></a>Upewnij się, że klucze podpisywania są przenoszone podczas korzystania z tożsamości serwera

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Tożsamość serwera | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Tożsamość serwera — klucze, sygnatur i kryptografii](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html) |
| **Kroki** | Upewnij się, że klucze podpisywania są przenoszone podczas korzystania z tożsamości serwera. Link w sekcji odwołań wyjaśnia, jak to planowane bez powodowania przestojów aplikacji jednostki uzależnionej na serwerze tożsamości. |

## <a id="client-server"></a>Upewnij się, że identyfikator klienta silną kryptograficznie, klucz tajny klienta używany na serwerze tożsamości

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Tożsamość serwera | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Upewnij się, że identyfikator klienta silną kryptograficznie, klucz tajny klienta używany na serwerze tożsamości. Poniższe wskazówki należy użyć podczas generowania Identyfikatora klienta oraz klucz tajny:</p><ul><li>Wygeneruj losowy identyfikator GUID identyfikatora klienta</li><li>Generuj kryptograficznie losowe 256-bitowego klucza jako klucza tajnego</li></ul>|
