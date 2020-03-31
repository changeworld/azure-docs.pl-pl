---
title: 'Synchronizacja usługi Azure AD Connect: odwołanie do funkcji | Dokumenty firmy Microsoft'
description: Odwołanie do deklaratywnych wyrażeń inicjowania obsługi administracyjnej w synchronizacji usługi Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 4f525ca0-be0e-4a2e-8da1-09b6b567ed5f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c3102480e316c634930c356ae02f769767b7d08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69900043"
---
# <a name="azure-ad-connect-sync-functions-reference"></a>Synchronizacja usługi Azure AD Connect: odwołanie do funkcji
W usłudze Azure AD Connect funkcje są używane do manipulowania wartością atrybutu podczas synchronizacji.  
Składnia funkcji jest wyrażona w następującym formacie:  
`<output type> FunctionName(<input type> <position name>, ..)`

Jeśli funkcja jest przeciążona i akceptuje wiele składni, wszystkie prawidłowe składni są wymienione.  
Funkcje są silnie wpisane i sprawdzają, czy typ przekazany w pasuje do udokumentowanego typu.  
Jeśli typ nie jest zgodny, zgłaszany jest błąd.

Typy są wyrażone w następującej składni:

* **bin** – binarny
* **bool** – Boolean
* **dt** – Data/Godzina UTC
* **wyliczenia** – Wyliczenie znanych stałych
* **exp** – Wyrażenie, które ma ocenić na wartość logiczną
* **mvbin** – Wielowartościowe binarne
* **mvstr** – Ciąg wielowartościowy
* **mvref** – Wielowartościowe odniesienie
* **liczba** – Numeryczna
* **ref** – Referencje
* **str** – Ciąg
* **var** – Wariant (prawie) dowolnego innego typu
* **void** – nie zwraca wartości

Funkcje z typami **mvbin**, **mvstr**i **mvref** mogą działać tylko na atrybutach wielowartościowych. Funkcje z **bin**, **str**i **ref** działają zarówno na atrybuty o pojedynczej, jak i wielowartościowej wartości.

## <a name="functions-reference"></a>Informacje ogólne o funkcjach

| Lista funkcji |  |  |  |  |
| --- | --- | --- | --- | --- |
| **Certyfikat** | | | | |
| [CertExtensionOids (Objawy certExtensionOids)](#certextensionoids) |[CertFormat](#certformat) |[CertFriendlyName (Nazwa)](#certfriendlyname) |[CertHashString (CertHashString)](#certhashstring) | |
| [CertyfikatIssuer](#certissuer) |[CertIssuerDN](#certissuerdn) |[CertIssuerOid](#certissueroid) |[CertKeyAlgorithm (Polski)](#certkeyalgorithm) | |
| [CertKeyAlgorithmParams](#certkeyalgorithmparams) |[CertNameInfo](#certnameinfo) |[CertNotAfter (Po](#certnotafter) |[CertNotBefore](#certnotbefore) | |
| [CertPublicKeyOid (Polski)](#certpublickeyoid) |[CertPublicKeyParametersOid](#certpublickeyparametersoid) |[Numer certSerialnumer](#certserialnumber) |[CertSignatureAlgorithmOid](#certsignaturealgorithmoid) | |
| [CertyfikatSubject](#certsubject) |[Nazwa IDN certSubject](#certsubjectnamedn) |[CertSubjectNameOid](#certsubjectnameoid) |[CertThumbprint (Drukarka certThumbprint)](#certthumbprint) | |
[Wersja cert](#certversion) |[Iscert ( Iscert )](#iscert) | | | |
| **Konwersja** | | | | |
| [Cbool](#cbool) |[Cdate](#cdate) |[CGuid (Polski)](#cguid) |[ConvertFromBase64](#convertfrombase64) | |
| [ConvertToBase64](#converttobase64) |[ConvertFromUTF8Hex](#convertfromutf8hex) |[ConvertToUTF8Hex](#converttoutf8hex) |[CNum ( CNum )](#cnum) | |
| [Cref](#cref) |[Cstr](#cstr) |[StringFromGuid (StringFromGuid)](#stringfromguid) |[StringFromSid (Wyd.](#stringfromsid) | |
| **Data i godzina** | | | | |
| [Dateadd](#dateadd) |[DataFromNum](#datefromnum) |[Formatdatetime](#formatdatetime) |[Nwo](#now) | |
| [NumFromDate (NumFromDate)](#numfromdate) | | | | |
| **Katalog** | | | | |
| [DNComponent (Skomponent DN)](#dncomponent) |[DNComponentRev](#dncomponentrev) |[EscapeDNComponent (](#escapedncomponent) | | |
| **Oceny** | | | | |
| [Zestaw IsBitSet](#isbitset) |[Isdate](#isdate) |[Isempty](#isempty) |[IsGuid (IsGuid)](#isguid) | |
| [Isnull](#isnull) |[IsNullOrEmpty ( IsNullOrEmpty )](#isnullorempty) |[IsNumeric](#isnumeric) |[Ispresent](#ispresent) | |
| [IsString (IsString)](#isstring) | | | | |
| **Math** | | | | |
| [BitAnd ( BitAnd )](#bitand) |[BitOr (BitOr)](#bitor) |[RandomNum (LosowoNum)](#randomnum) | | |
| **Wielowartościowe** | | | | |
| [Zawiera](#contains) |[Liczba](#count) |[Element](#item) |[PozycjaOrNull](#itemornull) | |
| [Dołącz](#join) |[Usuńduplika](#removeduplicates) |[Split](#split) | | |
| **Przepływ programu** | | | | |
| [Błąd](#error) |[Iif](#iif) |[Wybierz](#select) |[Przełącznik](#switch) | |
| [Gdzie](#where) |[Z](#with) | | | |
| **Tekst** | | | | |
| [Identyfikator guid](#guid) |[Instr](#instr) |[InStrRev ( InStrRev )](#instrrev) |[Lcase](#lcase) | |
| [Lewej](#left) |[Len (Len)](#len) |[Ltrim](#ltrim) |[Połowie](#mid) | |
| [Padleft](#padleft) |[Padright](#padright) |[Skrzynia pcase](#pcase) |[Zastąpić](#replace) | |
| [Zastępujechars](#replacechars) |[Prawo](#right) |[Rtrim](#rtrim) |[Przycinanie](#trim) | |
| [Pokań ucase](#ucase) |[Word](#word) | | | |

---
### <a name="bitand"></a>BitAnd ( BitAnd )
**Opis:**  
Funkcja BitAnd ustawia określone bity na wartości.

**Składni:**  
`num BitAnd(num value1, num value2)`

* value1, value2: wartości liczbowe, które powinny być i'ed razem

**Uwagi:**  
Ta funkcja konwertuje oba parametry do reprezentacji binarnej i ustawia nieco na:

* 0 - jeśli jeden lub oba z odpowiadających im bitów *w wartości1* i *wartości2* są 0
* 1 - jeśli oba odpowiednie bity są 1.

Innymi słowy zwraca 0 we wszystkich przypadkach, z wyjątkiem sytuacji, gdy odpowiednie bity obu parametrów są 1.

**Przykład:**  
`BitAnd(&HF, &HF7)`  
Zwraca wartość 7, ponieważ szesnastkowe wartości "F" i "F7" są obliczane do tej wartości.

---
### <a name="bitor"></a>BitOr (BitOr)
**Opis:**  
Funkcja BitOr ustawia określone bity na wartości.

**Składni:**  
`num BitOr(num value1, num value2)`

* value1, value2: wartości liczbowe, które powinny być or'ed razem

**Uwagi:**  
Ta funkcja konwertuje oba parametry do reprezentacji binarnej i ustawia bit na 1, jeśli jeden lub oba odpowiednie bity w masce i flagi są 1, i na 0, jeśli oba odpowiednie bity są 0. Innymi słowy zwraca 1 we wszystkich przypadkach, z wyjątkiem przypadków, gdy odpowiednie bity obu parametrów są 0.

---
### <a name="cbool"></a>Cbool
**Opis:**  
Funkcja CBool zwraca wartość logiczną na podstawie obliczonego wyrażenia

**Składni:**  
`bool CBool(exp Expression)`

**Uwagi:**  
Jeśli wyrażenie ma wartość inną niż zero, cBool zwraca true, w przeciwnym razie zwraca False.

**Przykład:**  
`CBool([attrib1] = [attrib2])`  

Zwraca wartość True, jeśli oba atrybuty mają tę samą wartość.

---
### <a name="cdate"></a>Cdate
**Opis:**  
Funkcja CDate zwraca datę UTC DateTime z ciągu. DateTime nie jest natywnym typem atrybutu w synchronizacji, ale jest używany przez niektóre funkcje.

**Składni:**  
`dt CDate(str value)`

* Wartość: ciąg z datą, godziną i opcjonalnie strefą czasową

**Uwagi:**  
Zwrócony ciąg jest zawsze w utc.

**Przykład:**  
`CDate([employeeStartTime])`  
Zwraca datetime na podstawie czasu rozpoczęcia pracownika

`CDate("2013-01-10 4:00 PM -8")`  
Zwraca datetime reprezentujący "2013-01-11 12:00 AM"


---
### <a name="certextensionoids"></a>CertExtensionOids (Objawy certExtensionOids)
**Opis:**  
Zwraca wartości Oid wszystkich krytycznych rozszerzeń obiektu certyfikatu.

**Składni:**  
`mvstr CertExtensionOids(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowana w formacie binarnym (DER) lub zakodowana w bazie Bazowej wartość danych X.509.

---
### <a name="certformat"></a>CertFormat
**Opis:**  
Zwraca nazwę formatu tego certyfikatu X.509v3.

**Składni:**  
`str CertFormat(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowana w formacie binarnym (DER) lub zakodowana w bazie Bazowej wartość danych X.509.

---
### <a name="certfriendlyname"></a>CertFriendlyName (Nazwa)
**Opis:**  
Zwraca skojarzony alias certyfikatu.

**Składni:**  
`str CertFriendlyName(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowana w formacie binarnym (DER) lub zakodowana w bazie Bazowej wartość danych X.509.

---
### <a name="certhashstring"></a>CertHashString (CertHashString)
**Opis:**  
Zwraca wartość skrótu SHA1 dla certyfikatu X.509v3 jako ciąg szesnastkowy.

**Składni:**  
`str CertHashString(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowana w formacie binarnym (DER) lub zakodowana w bazie Bazowej wartość danych X.509.

---
### <a name="certissuer"></a>CertyfikatIssuer
**Opis:**  
Zwraca nazwę urzędu certyfikacji, który wystawił certyfikat X.509v3.

**Składni:**  
`str CertIssuer(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowana w formacie binarnym (DER) lub zakodowana w bazie Bazowej wartość danych X.509.

---
### <a name="certissuerdn"></a>CertIssuerDN
**Opis:**  
Zwraca nazwę wyróżniającą wystawcy certyfikatu.

**Składni:**  
`str CertIssuerDN(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowana w formacie binarnym (DER) lub zakodowana w bazie Bazowej wartość danych X.509.

---
### <a name="certissueroid"></a>CertIssuerOid
**Opis:**  
Zwraca identyfikator wystawcy certyfikatu.

**Składni:**  
`str CertIssuerOid(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowana w formacie binarnym (DER) lub zakodowana w bazie Bazowej wartość danych X.509.

---
### <a name="certkeyalgorithm"></a>CertKeyAlgorithm (Polski)
**Opis:**  
Zwraca informacje o algorytmie klucza dla tego certyfikatu X.509v3 jako ciąg.

**Składni:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowana w formacie binarnym (DER) lub zakodowana w bazie Bazowej wartość danych X.509.

---
### <a name="certkeyalgorithmparams"></a>CertKeyAlgorithmParams
**Opis:**  
Zwraca parametry kluczowego algorytmu certyfikatu X.509v3 jako ciąg szesnastkowy.

**Składni:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowana w formacie binarnym (DER) lub zakodowana w bazie Bazowej wartość danych X.509.

---
### <a name="certnameinfo"></a>CertNameInfo
**Opis:**  
Zwraca nazwy podmiotu i wystawcy z certyfikatu.

**Składni:**  
`str CertNameInfo(binary certificateRawData, str x509NameType, bool includesIssuerName)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowana w formacie binarnym (DER) lub zakodowana w bazie Bazowej wartość danych X.509.
*   X509NameType: Wartość X509NameType dla podmiotu.
*   includesIssuerName: true to include the issuer name; w przeciwnym razie, false.

---
### <a name="certnotafter"></a>CertNotAfter (Po
**Opis:**  
Zwraca datę w czasie lokalnym, po której certyfikat nie jest już ważny.

**Składni:**  
`dt CertNotAfter(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowana w formacie binarnym (DER) lub zakodowana w bazie Bazowej wartość danych X.509.

---
### <a name="certnotbefore"></a>CertNotBefore
**Opis:**  
Zwraca datę w czasie lokalnym, w której certyfikat staje się ważny.

**Składni:**  
`dt CertNotBefore(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowana w formacie binarnym (DER) lub zakodowana w bazie Bazowej wartość danych X.509.

---
### <a name="certpublickeyoid"></a>CertPublicKeyOid (Polski)
**Opis:**  
Zwraca oid klucza publicznego dla certyfikatu X.509v3.

**Składni:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowana w formacie binarnym (DER) lub zakodowana w bazie Bazowej wartość danych X.509.

---
### <a name="certpublickeyparametersoid"></a>CertPublicKeyParametersOid
**Opis:**  
Zwraca oid parametrów klucza publicznego dla certyfikatu X.509v3.

**Składni:**  
`str CertPublicKeyParametersOid(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowana w formacie binarnym (DER) lub zakodowana w bazie Bazowej wartość danych X.509.

---
### <a name="certserialnumber"></a>Numer certSerialnumer
**Opis:**  
Zwraca numer seryjny certyfikatu X.509v3.

**Składni:**  
`str CertSerialNumber(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowana w formacie binarnym (DER) lub zakodowana w bazie Bazowej wartość danych X.509.

---
### <a name="certsignaturealgorithmoid"></a>CertSignatureAlgorithmOid
**Opis:**  
Zwraca oid algorytmu używanego do tworzenia podpisu certyfikatu.

**Składni:**  
`str CertSignatureAlgorithmOid(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowana w formacie binarnym (DER) lub zakodowana w bazie Bazowej wartość danych X.509.

---
### <a name="certsubject"></a>CertyfikatSubject
**Opis:**  
Pobiera nazwę wyróżniającą podmiot od certyfikatu.

**Składni:**  
`str CertSubject(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowana w formacie binarnym (DER) lub zakodowana w bazie Bazowej wartość danych X.509.

---
### <a name="certsubjectnamedn"></a>Nazwa IDN certSubject
**Opis:**  
Zwraca nazwę wyróżniającą podmiotu z certyfikatu.

**Składni:**  
`str CertSubjectNameDN(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowana w formacie binarnym (DER) lub zakodowana w bazie Bazowej wartość danych X.509.

---
### <a name="certsubjectnameoid"></a>CertSubjectNameOid
**Opis:**  
Zwraca nazwę podmiotu z certyfikatu.

**Składni:**  
`str CertSubjectNameOid(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowana w formacie binarnym (DER) lub zakodowana w bazie Bazowej wartość danych X.509.

---
### <a name="certthumbprint"></a>CertThumbprint (Drukarka certThumbprint)
**Opis:**  
Zwraca odcisk palca certyfikatu.

**Składni:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowana w formacie binarnym (DER) lub zakodowana w bazie Bazowej wartość danych X.509.

---
### <a name="certversion"></a>Wersja cert
**Opis:**  
Zwraca wersję certyfikatu w formacie X.509.

**Składni:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowana w formacie binarnym (DER) lub zakodowana w bazie Bazowej wartość danych X.509.

---
### <a name="cguid"></a>CGuid (Polski)
**Opis:**  
Funkcja CGuid konwertuje reprezentację ciągu identyfikatora GUID na jego reprezentację binarną.

**Składni:**  
`bin CGuid(str GUID)`

* Ciąg sformatowany w tym wzorze: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx lub {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

---
### <a name="contains"></a>Contains
**Opis:**  
Funkcja Zawiera znajduje ciąg wewnątrz atrybutu wielowartościowego

**Składni:**  
`num Contains (mvstring attribute, str search)`- rozróżniana wielkość liter  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)`- rozróżniana wielkość liter

* atrybut: atrybut wielowartościowy do wyszukiwania.
* szukaj: ciąg, aby znaleźć w atrybucie.
* Typ sprawy: CaseInsensitive lub CaseSensitive.

Zwraca indeks w atrybucie wielowartościowym, w którym znaleziono ciąg. 0 jest zwracany, jeśli ciąg nie zostanie znaleziony.

**Uwagi:**  
W przypadku atrybutów ciągów wielowartościowych wyszukiwanie znajduje podciągi w wartościach.  
W przypadku atrybutów odwołań wyszukiwany ciąg musi dokładnie odpowiadać wartości, która ma być uznana za dopasowaną.

**Przykład:**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
Jeśli atrybut proxyAddresses ma podstawowy adres e-mail (oznaczony wielkimi literami "SMTP:"), a następnie zwróć atrybut proxyAddress, w przeciwnym razie zwróć błąd.

---
### <a name="convertfrombase64"></a>ConvertFromBase64
**Opis:**  
ConvertFromBase64 Funkcja konwertuje określoną wartość zakodowaną base64 na ciąg regularny.

**Składni:**  
`str ConvertFromBase64(str source)`- zakłada Unicode do kodowania  
`str ConvertFromBase64(str source, enum Encoding)`

* źródło: Ciąg zakodowany base64  
* Kodowanie: Unicode, ASCII, UTF8

**Przykład**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Oba przykłady powracają "*Hello world!*"

---
### <a name="convertfromutf8hex"></a>ConvertFromUTF8Hex
**Opis:**  
ConvertFromODF8Hex Funkcja konwertuje określoną wartość zakodowaną przez UTF8 Hex na ciąg.

**Składni:**  
`str ConvertFromUTF8Hex(str source)`

* źródło: UTF8 2-bajtowe zakodowane żądło

**Uwagi:**  
Różnica między tą funkcją i ConvertFromBase64([],UTF8) w tym wynik jest przyjazny dla atrybutu DN.  
Ten format jest używany przez usługę Azure Active Directory jako dn.

**Przykład:**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
Powraca "*Hello world!*"

---
### <a name="converttobase64"></a>ConvertToBase64
**Opis:**  
ConvertToBase64 Funkcja konwertuje ciąg do ciągu Base64 Unicode.  
Konwertuje wartość tablicy liczb całkowitych na jej równoważną reprezentację ciągu, która jest zakodowana z podstawowych 64 cyfr.

**Składni:**  
`str ConvertToBase64(str source)`

**Przykład:**  
`ConvertToBase64("Hello world!")`  
Zwraca wartość "SABlAGwAbVACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Opis:**  
ConvertToUTF8Hex Funkcja konwertuje ciąg na wartość zakodowaną w hex UTF8.

**Składni:**  
`str ConvertToUTF8Hex(str source)`

**Uwagi:**  
Format wyjściowy tej funkcji jest używany przez usługę Azure Active Directory jako format atrybutu DN.

**Przykład:**  
`ConvertToUTF8Hex("Hello world!")`  
Zwraca 48656C6C6F20776F726C6421

---
### <a name="count"></a>Liczba
**Opis:**  
Funkcja Count zwraca liczbę elementów w atrybucie wielowartościowym

**Składni:**  
`num Count(mvstr attribute)`

---
### <a name="cnum"></a>CNum ( CNum )
**Opis:**  
Funkcja CNum przyjmuje ciąg i zwraca typ danych liczbowych.

**Składni:**  
`num CNum(str value)`

---
### <a name="cref"></a>Cref
**Opis:**  
Konwertuje ciąg na atrybut odwołania

**Składni:**  
`ref CRef(str value)`

**Przykład:**  
`CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

---
### <a name="cstr"></a>Cstr
**Opis:**  
Funkcja CStr konwertuje na typ danych ciągu.

**Składni:**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* wartość: Może być wartością liczbową, atrybutem odwołania lub wartością logiczną.

**Przykład:**  
`CStr([dn])`  
Może zwrócić "cn=Joe,dc=contoso,dc=com"

---
### <a name="dateadd"></a>DateAdd
**Opis:**  
Zwraca datę zawierającą datę, do której dodano określony przedział czasu.

**Składni:**  
`dt DateAdd(str interval, num value, dt date)`

* interwał: wyrażenie ciągu, które jest interwałem czasu, który chcesz dodać. Ciąg musi mieć jedną z następujących wartości:
  * Rok yyyy
  * q Kwartał
  * m miesiąc
  * y Dzień roku
  * d Dzień
  * w Dzień Tygodnia
  * tydzień ww
  * h Godzina
  * n Minuta
  * s Drugi
* wartość: liczba jednostek, które chcesz dodać. Może to być pozytywne (aby uzyskać daty w przyszłości) lub negatywne (aby uzyskać daty w przeszłości).
* data: DateTime reprezentujący datę, do której dodano interwał.

**Przykład:**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
Dodaje 3 miesiące i zwraca DateTime reprezentujący "2001-04-01".

---
### <a name="datefromnum"></a>DataFromNum
**Opis:**  
Funkcja DateFromNum konwertuje wartość w formacie daty usługi AD na typ DateTime.

**Składni:**  
`dt DateFromNum(num value)`

**Przykład:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Zwraca datetime reprezentujący 2012-01-01 23:00:00

---
### <a name="dncomponent"></a>DNComponent (Skomponent DN)
**Opis:**  
Funkcja DNComponent zwraca wartość określonego komponentu DN przechodzącego od lewej.

**Składni:**  
`str DNComponent(ref dn, num ComponentNumber)`

* dn: atrybut odniesienia do interpretacji
* Numer składnika: Składnik w numerze DN do zwrócenia

**Przykład:**  
`DNComponent(CRef([dn]),1)`  
Jeśli dn jest "cn=Joe,ou=...", zwraca Joe

---
### <a name="dncomponentrev"></a>DNComponentRev
**Opis:**  
Funkcja DNComponentRev zwraca wartość określonego składnika DN przechodzącego od prawej (koniec).

**Składni:**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

* dn: atrybut odniesienia do interpretacji
* ComponentNumber - Składnik w numerze DN do zwrócenia
* Opcje: DC – Ignoruj wszystkie komponenty z "dc="

**Przykład:**  
Jeśli dn to "cn=Joe,ou=Atlanta,ou=GA,ou=US, dc=contoso,dc=com", a następnie  
`DNComponentRev(CRef([dn]),3)`  
`DNComponentRev(CRef([dn]),1,"DC")`  
Oba zwracają USA.

---
### <a name="error"></a>Błąd
**Opis:**  
Funkcja Error służy do zwracania błędu niestandardowego.

**Składni:**  
`void Error(str ErrorMessage)`

**Przykład:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Jeśli accountname atrybutu nie jest obecny, zgłosić błąd na obiekcie.

---
### <a name="escapedncomponent"></a>EscapeDNComponent (
**Opis:**  
EscapeDNComponent Funkcja przyjmuje jeden składnik DN i wyjmuje go, dzięki czemu może być reprezentowana w LDAP.

**Składni:**  
`str EscapeDNComponent(str value)`

**Przykład:**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
Upewnia się, że obiekt może zostać utworzony w katalogu LDAP, nawet jeśli atrybut displayName zawiera znaki, które muszą zostać zmienione w LDAP.

---
### <a name="formatdatetime"></a>Formatdatetime
**Opis:**  
Funkcja FormatDateTime służy do formatowania datetime do ciągu o określonym formacie

**Składni:**  
`str FormatDateTime(dt value, str format)`

* wartość: wartość w formacie DateTime
* format: ciąg reprezentujący format do konwersji.

**Uwagi:**  
Możliwe wartości formatu można znaleźć tutaj: [Niestandardowe formaty daty i godziny dla funkcji FORMAT](https://docs.microsoft.com/dax/custom-date-and-time-formats-for-the-format-function).

**Przykład:**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-mm-dd")`  
Wyniki w "2007-12-25".

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
Może spowodować "20140905081453.0Z"

---
### <a name="guid"></a>Guid (identyfikator GUID)
**Opis:**  
Funkcja Guid generuje nowy losowy identyfikator GUID

**Składni:**  
`str Guid()`

---
### <a name="iif"></a>Iif
**Opis:**  
Funkcja IIF zwraca jeden z zestawu możliwych wartości na podstawie określonego warunku.

**Składni:**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* warunek: dowolna wartość lub wyrażenie, które można ocenić na wartość true lub false.
* valueIfTrue: Jeśli warunek ma wartość true, zwracaną wartość.
* valueIfFalse: Jeśli warunek ma wartość false, zwracaną wartość.

**Przykład:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Jeśli użytkownik jest stażystą, zwraca alias użytkownika z "t-" dodane na początku, w przeciwnym razie zwraca alias użytkownika, jak jest.

---
### <a name="instr"></a>Instr
**Opis:**  
Funkcja InStr znajduje pierwsze wystąpienie podciągu w ciągu

**Składni:**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

* stringcheck: ciąg do przeszukania
* stringmatch: ciąg do znalezienia
* start: pozycja początkowa, aby znaleźć podciąg
* porównaj: vbTextCompare lub vbBinaryCompare

**Uwagi:**  
Zwraca pozycję, w której znaleziono podciąg lub 0, jeśli nie znaleziono.

**Przykład:**  
`InStr("The quick brown fox","quick")`  
Evalues do 5

`InStr("repEated","e",3,vbBinaryCompare)`  
Ocenia do 7

---
### <a name="instrrev"></a>InStrRev ( InStrRev )
**Opis:**  
Funkcja InStrRev znajduje ostatnie wystąpienie podciągów w ciągu

**Składni:**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

* stringcheck: ciąg do przeszukania
* stringmatch: ciąg do znalezienia
* start: pozycja początkowa, aby znaleźć podciąg
* porównaj: vbTextCompare lub vbBinaryCompare

**Uwagi:**  
Zwraca pozycję, w której znaleziono podciąg lub 0, jeśli nie znaleziono.

**Przykład:**  
`InStrRev("abbcdbbbef","bb")`  
Zwraca 7

---
### <a name="isbitset"></a>Zestaw IsBitSet
**Opis:**  
Funkcja IsBitSet sprawdza, czy bit jest ustawiony, czy nie

**Składni:**  
`bool IsBitSet(num value, num flag)`

* wartość: wartość liczbowa, która jest obliczana.flag: wartość liczbowa, która ma bit do oceny

**Przykład:**  
`IsBitSet(&HF,4)`  
Zwraca wartość True, ponieważ bit "4" jest ustawiony w wartości szesnastkowej "F"

---
### <a name="isdate"></a>Isdate
**Opis:**  
Jeśli wyrażenie może być oblicza się jako typ DateTime, funkcja IsDate ma wartość True.

**Składni:**  
`bool IsDate(var Expression)`

**Uwagi:**  
Służy do określenia, czy CDate() może zakończyć się powodzeniem.

---
### <a name="iscert"></a>Iscert ( Iscert )
**Opis:**  
Zwraca wartość true, jeśli nieprzetworzone dane mogą być serializowane do obiektu certyfikatu .NET X509Certificate2.

**Składni:**  
`bool CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowana w formacie binarnym (DER) lub zakodowana w bazie Bazowej wartość danych X.509.
---
### <a name="isempty"></a>IsEmpty
**Opis:**  
Jeśli atrybut jest obecny w CS lub MV, ale ma wartość pustego ciągu, funkcja IsEmpty jest oblicza wartość True.

**Składni:**  
`bool IsEmpty(var Expression)`

---
### <a name="isguid"></a>IsGuid (IsGuid)
**Opis:**  
Jeśli ciąg może być konwertowany na identyfikator GUID, a następnie IsGuid funkcja oceniane true.

**Składni:**  
`bool IsGuid(str GUID)`

**Uwagi:**  
Identyfikator GUID jest zdefiniowany jako ciąg następujący po jednym z tych wzorców: xxxxxxxxxx-xxxx-xxxx-xxxxxxxxxxxx lub {xxxxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

Służy do określenia, czy CGuid() może zakończyć się powodzeniem.

**Przykład:**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
Jeśli StrAttribute ma format GUID, zwraca reprezentację binarną, w przeciwnym razie zwraca null.

---
### <a name="isnull"></a>Isnull
**Opis:**  
Jeśli wyrażenie ma wartość Null, funkcja IsNull zwraca wartość true.

**Składni:**  
`bool IsNull(var Expression)`

**Uwagi:**  
Dla atrybutu Null jest wyrażona przez brak atrybutu.

**Przykład:**  
`IsNull([displayName])`  
Zwraca wartość True, jeśli atrybut nie jest obecny w CS lub MV.

---
### <a name="isnullorempty"></a>IsNullOrEmpty ( IsNullOrEmpty )
**Opis:**  
Jeśli wyrażenie ma wartość null lub pusty ciąg, funkcja IsNullOrEmpty zwraca wartość true.

**Składni:**  
`bool IsNullOrEmpty(var Expression)`

**Uwagi:**  
Dla atrybutu to ocenić True, jeśli atrybut jest nieobecny lub jest obecny, ale jest pusty ciąg.  
Odwrotność tej funkcji nosi nazwę IsPresent.

**Przykład:**  
`IsNullOrEmpty([displayName])`  
Zwraca wartość True, jeśli atrybut nie jest obecny lub jest pustym ciągiem w CS lub MV.

---
### <a name="isnumeric"></a>IsNumeric
**Opis:**  
Funkcja IsNumeric zwraca wartość logiczną wskazującą, czy wyrażenie może być oceniane jako typ liczbowy.

**Składni:**  
`bool IsNumeric(var Expression)`

**Uwagi:**  
Służy do określenia, czy CNum() może zakończyć się pomyślnie, aby przeanalizować wyrażenie.

---
### <a name="isstring"></a>IsString (IsString)
**Opis:**  
Jeśli wyrażenie można ocenić do typu ciągu, a następnie IsString funkcja ocenia True.

**Składni:**  
`bool IsString(var expression)`

**Uwagi:**  
Służy do określenia, czy CStr() może zakończyć się pomyślnie, aby przeanalizować wyrażenie.

---
### <a name="ispresent"></a>Ispresent
**Opis:**  
Jeśli wyrażenie ma wartość ciągu, który nie jest null i nie jest pusty, a następnie IsPresent funkcja zwraca true.

**Składni:**  
`bool IsPresent(var expression)`

**Uwagi:**  
Odwrotność tej funkcji nosi nazwę IsNullOrEmpty.

**Przykład:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="item"></a>Element
**Opis:**  
Funkcja Element zwraca jeden element z wielowartościowego ciągu/atrybutu.

**Składni:**  
`var Item(mvstr attribute, num index)`

* atrybut: atrybut wielowartościowy
* index: indeks do elementu w ciągu wielowartościowym.

**Uwagi:**  
Funkcja Element jest przydatna wraz z Contains funkcji, ponieważ ta ostatnia funkcja zwraca indeks do elementu w atrybucie wielowartościowe.

Zgłasza błąd, jeśli indeks jest poza granicami.

**Przykład:**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
Zwraca podstawowy adres e-mail.

---
### <a name="itemornull"></a>PozycjaOrNull
**Opis:**  
Funkcja ItemOrNull zwraca jeden element z wielowartościowego ciągu/atrybutu.

**Składni:**  
`var ItemOrNull(mvstr attribute, num index)`

* atrybut: atrybut wielowartościowy
* index: indeks do elementu w ciągu wielowartościowym.

**Uwagi:**  
Funkcja ItemOrNull jest przydatna wraz z Contains, funkcja, ponieważ ta ostatnia funkcja zwraca indeks do elementu w atrybucie wielowartościowym.

Jeśli indeks jest poza granicami, zwraca wartość Null.

---
### <a name="join"></a>Join
**Opis:**  
Funkcja Sprzężenia przyjmuje ciąg wielowartościowy i zwraca ciąg jednowartościowy z określonym separatorem wstawionym między każdym elementem.

**Składni:**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

* atrybut: Atrybut wielowartościowy zawierający ciągi, które mają zostać połączone.
* ogranicznik: Dowolny ciąg, używany do oddzielania podciągów w zwróconym ciągu. W przypadku pominięcia, używany jest znak spacji (" "). Jeśli Ogranicznik jest ciągiem o zerowej długości ("") lub Nothing, wszystkie elementy na liście są łączone bez ograniczników.

**Uwagi**  
Istnieje parzystość między funkcjami Sprzężenie i Podziel. Join Funkcja przyjmuje tablicę ciągów i łączy je za pomocą ciągu ogranicznika, aby zwrócić pojedynczy ciąg. Funkcja Split przyjmuje ciąg i oddziela go w ograniczniku, aby zwrócić tablicę ciągów. Jednak kluczową różnicą jest to, że Join może łączyć ciągi z dowolnym ciągiem ogranicznika, Split może oddzielać tylko ciągi znaków przy użyciu ogranicznika pojedynczego znaku.

**Przykład:**  
`Join([proxyAddresses],",")`  
Może wrócić:SMTP:john.doe@contoso.comsmtp:jd@contoso.com" , "

---
### <a name="lcase"></a>Lcase
**Opis:**  
LCase Funkcja konwertuje wszystkie znaki w ciągu do małych liter.

**Składni:**  
`str LCase(str value)`

**Przykład:**  
`LCase("TeSt")`  
Zwraca wartość "test".

---
### <a name="left"></a>Lewe
**Opis:**  
Funkcja Left zwraca określoną liczbę znaków z lewej strony ciągu.

**Składni:**  
`str Left(str string, num NumChars)`

* ciąg znaków: ciąg zwrotny znaków z
* NumChars: liczba identyfikujący liczbę znaków do zwrócenia od początku (po lewej) ciągu

**Uwagi:**  
Ciąg zawierający pierwsze znaki numChars w ciągu:

* Jeśli numChars = 0, zwraca pusty ciąg.
* Jeśli numChars < 0, zwraca ciąg wejściowy.
* Jeśli ciąg ma wartość null, zwróć pusty ciąg.

Jeśli ciąg zawiera mniej znaków niż liczba określona w numChars, zwracany jest ciąg identyczny z ciągiem (czyli zawierający wszystkie znaki w parametrze 1).

**Przykład:**  
`Left("John Doe", 3)`  
Zwraca "Joh".

---
### <a name="len"></a>Len
**Opis:**  
Funkcja Len zwraca liczbę znaków w ciągu.

**Składni:**  
`num Len(str value)`

**Przykład:**  
`Len("John Doe")`  
Zwraca 8

---
### <a name="ltrim"></a>Ltrim
**Opis:**  
LTrim Funkcja usuwa początkowe odstępy z ciągu.

**Składni:**  
`str LTrim(str value)`

**Przykład:**  
`LTrim(" Test ")`  
Zwraca wartość "Test"

---
### <a name="mid"></a>Mid
**Opis:**  
Funkcja Mid zwraca określoną liczbę znaków z określonej pozycji w ciągu.

**Składni:**  
`str Mid(str string, num start, num NumChars)`

* ciąg znaków: ciąg zwrotny znaków z
* start: liczba identyfikującya pozycję początkową w ciągu, aby zwrócić znaki z
* NumChars: liczba identyfikujący liczbę znaków do zwrócenia z pozycji w ciągu

**Uwagi:**  
Zwraca znaki numChars zaczynające się od rozpoczęcia pozycji w ciągu.  
Ciąg zawierający znaki numChars od pozycji rozpoczyna się w ciągu:

* Jeśli numChars = 0, zwraca pusty ciąg.
* Jeśli numChars < 0, zwraca ciąg wejściowy.
* Jeśli rozpocznie się > długość ciągu, zwraca ciąg wejściowy.
* Jeśli <start = 0, zwraca ciąg wejściowy.
* Jeśli ciąg ma wartość null, zwróć pusty ciąg.

Jeśli nie ma znaków numChar pozostałych w ciągu od rozpoczęcia pozycji, jak najwięcej znaków, jak to możliwe są zwracane.

**Przykład:**  
`Mid("John Doe", 3, 5)`  
Zwraca wartość "hn Do".

`Mid("John Doe", 6, 999)`  
Zwraca wartość "Doe"

---
### <a name="now"></a>Now
**Opis:**  
Funkcja Teraz zwraca datetime określający bieżącą datę i godzinę, zgodnie z datą i godziną systemową komputera.

**Składni:**  
`dt Now()`

---
### <a name="numfromdate"></a>NumFromDate (NumFromDate)
**Opis:**  
Funkcja NumFromDate zwraca datę w formacie daty usługi AD.

**Składni:**  
`num NumFromDate(dt value)`

**Przykład:**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
Zwraca wartość 129699324000000000

---
### <a name="padleft"></a>Padleft
**Opis:**  
PadLeft funkcja lewej okładziny ciąg do określonej długości przy użyciu podanego znaku dopełnienia.

**Składni:**  
`str PadLeft(str string, num length, str padCharacter)`

* ciąg: ciąg do pad.
* długość: liczba całkowita reprezentująca żądaną długość ciągu.
* padCharacter: Ciąg składający się z jednego znaku do użycia jako znak padu

**Uwagi:**

* Jeśli długość ciągu jest mniejsza niż długość, padCharacter jest wielokrotnie dołączany do początku (po lewej) ciągu, dopóki nie ma długości równej długości.
* PadCharacter może być znakiem spacji, ale nie może być wartością null.
* Jeśli długość ciągu jest równa lub większa niż długość, ciąg jest zwracany bez zmian.
* Jeśli ciąg ma długość większą lub równą długości, zwracany jest ciąg identyczny z ciągiem.
* Jeśli długość ciągu jest mniejsza niż długość, zwracany jest nowy ciąg żądanej długości zawierający ciąg wyściełany padCharacter.
* Jeśli ciąg ma wartość null, funkcja zwraca pusty ciąg.

**Przykład:**  
`PadLeft("User", 10, "0")`  
Zwraca wartość "000000User".

---
### <a name="padright"></a>Padright
**Opis:**  
PadRight Funkcja prawo-pads ciąg do określonej długości przy użyciu podanego znaku dopełnienia.

**Składni:**  
`str PadRight(str string, num length, str padCharacter)`

* ciąg: ciąg do pad.
* długość: liczba całkowita reprezentująca żądaną długość ciągu.
* padCharacter: Ciąg składający się z jednego znaku do użycia jako znak padu

**Uwagi:**

* Jeśli długość ciągu jest mniejsza niż długość, padCharacter jest wielokrotnie dołączany do końca (po prawej) ciągu, dopóki nie ma długości równej długości.
* padCharacter może być znakiem spacji, ale nie może być wartością null.
* Jeśli długość ciągu jest równa lub większa niż długość, ciąg jest zwracany bez zmian.
* Jeśli ciąg ma długość większą lub równą długości, zwracany jest ciąg identyczny z ciągiem.
* Jeśli długość ciągu jest mniejsza niż długość, zwracany jest nowy ciąg żądanej długości zawierający ciąg wyściełany padCharacter.
* Jeśli ciąg ma wartość null, funkcja zwraca pusty ciąg.

**Przykład:**  
`PadRight("User", 10, "0")`  
Zwraca wartość "User000000".

---
### <a name="pcase"></a>Skrzynia pcase
**Opis:**  
Funkcja PCase konwertuje pierwszy znak każdego wyrazu rozdzielanego spacji w ciągu na wielkie litery, a wszystkie inne znaki są konwertowane na małe litery.

**Składni:**  
`String PCase(string)`

**Uwagi:**

* Ta funkcja nie zapewnia obecnie właściwej wielkości liter, aby przekonwertować wyraz, który jest całkowicie wielkimi literami, na przykład akronimem.

**Przykład:**  
`PCase("TEsT")`  
Zwraca wartość "Test".

`PCase(LCase("TEST"))`  
Zwraca wartość "Test"

---
### <a name="randomnum"></a>RandomNum (LosowoNum)
**Opis:**  
Funkcja RandomNum zwraca liczbę losową między określonym interwałem.

**Składni:**  
`num RandomNum(num start, num end)`

* start: liczba identyfikujący dolną granicę wartości losowej do wygenerowania
* koniec: liczba identyfikujący górną granicę wartości losowej do wygenerowania

**Przykład:**  
`Random(100,999)`  
Może zwrócić 734.

---
### <a name="removeduplicates"></a>Usuńduplika
**Opis:**  
RemoveDuplicates Funkcja przyjmuje ciąg wielowartościowy i upewnij się, że każda wartość jest unikatowa.

**Składni:**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Przykład:**  
`RemoveDuplicates([proxyAddresses])`  
Zwraca zdezynfekowany atrybut proxyAddress, w którym wszystkie zduplikowane wartości zostały usunięte.

---
### <a name="replace"></a>Replace
**Opis:**  
Funkcja Zamień zastępuje wszystkie wystąpienia ciągu na inny ciąg.

**Składni:**  
`str Replace(str string, str OldValue, str NewValue)`

* ciąg: Ciąg do zastąpienia wartości w.
* OldValue: Ciąg do wyszukiwania i do zastąpienia.
* NewValue: Ciąg do zastąpienia.

**Uwagi:**  
Funkcja rozpoznaje następujące specjalne monikery:

* \n – Nowa linia
* \r – Powrót karetki
* \t – Karta

**Przykład:**  
`Replace([address],"\r\n",", ")`  
Zastępuje CRLF przecinkiem i przestrzenią, a może prowadzić do "One Microsoft Way, Redmond, WA, USA"

---
### <a name="replacechars"></a>Zastępujechars
**Opis:**  
Funkcja ReplaceChars zastępuje wszystkie wystąpienia znaków znalezionych w ciągu ReplacePattern.

**Składni:**  
`str ReplaceChars(str string, str ReplacePattern)`

* ciąg znaków: Ciąg do zastąpienia znaków.
* ReplacePattern: ciąg zawierający słownik ze znakami do zastąpienia.

Format to {source1}:{target1},{source2}:{target2},{sourceN},{targetN}, {targetN}, gdzie źródłem jest znak do znalezienia i ukierunkowania ciągu do zastąpienia.

**Uwagi:**

* Funkcja przyjmuje każde wystąpienie zdefiniowanych źródeł i zastępuje je celami.
* Źródło musi być dokładnie jeden znak (unicode).
* Źródło nie może być puste lub dłuższe niż jeden znak (błąd analizowania).
* Cel może mieć wiele znaków, na przykład ö:oe, β:ss.
* Cel może być pusty, co oznacza, że znak powinien zostać usunięty.
* W źródle jest rozróżniana wielkość liter i musi być dokładne dopasowanie.
* Znaki , (przecinek) i : (dwukropek) są znakami zastrzeżonymi i nie można ich zastąpić za pomocą tej funkcji.
* Spacje i inne białe znaki w ciągu ReplacePattern są ignorowane.

**Przykład:**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
Zwraca Raksmorgas

`ReplaceChars("O’Neil",%ReplaceString%)`  
Zwraca "ONeil", pojedynczy znacznik jest zdefiniowany do usunięcia.

---
### <a name="right"></a>Prawe
**Opis:**  
Funkcja Right zwraca określoną liczbę znaków z prawej (końca) ciągu.

**Składni:**  
`str Right(str string, num NumChars)`

* ciąg znaków: ciąg zwrotny znaków z
* NumChars: liczba identyfikujący liczbę znaków do zwrócenia od końca (po prawej) ciągu

**Uwagi:**  
Znaki NumChars są zwracane z ostatniej pozycji ciągu.

Ciąg zawierający ostatnie znaki numChars w ciągu:

* Jeśli numChars = 0, zwraca pusty ciąg.
* Jeśli numChars < 0, zwraca ciąg wejściowy.
* Jeśli ciąg ma wartość null, zwróć pusty ciąg.

Jeśli ciąg zawiera mniej znaków niż liczba określona w NumChars, zwracany jest ciąg identyczny z ciągiem.

**Przykład:**  
`Right("John Doe", 3)`  
Zwraca wartość "Doe".

---
### <a name="rtrim"></a>Rtrim
**Opis:**  
Funkcja RTrim usuwa końcowe białe spacje z ciągu.

**Składni:**  
`str RTrim(str value)`

**Przykład:**  
`RTrim(" Test ")`  
Zwraca "Test".

---
### <a name="select"></a>Wybierz pozycję
**Opis:**  
Przetwarzanie wszystkich wartości w wielowartościowym atrybucie (lub danych wyjściowych wyrażenia) na podstawie określonej funkcji.

**Składni:**  
`mvattr Select(variable item, mvattr attribute, func function)`  
`mvattr Select(variable item, exp expression, func function)`

* element: Reprezentuje element w atrybucie wielowartościowym
* atrybut: atrybut wielowartościowy
* wyrażenie: wyrażenie, które zwraca zbiór wartości
* condition: dowolna funkcja, która może przetworzyć element w atrybucie

**Przykłady:**  
`Select($item,[otherPhone],Replace($item,"-",""))`  
Zwraca wszystkie wartości w wielowartościowym atrybucie otherPhone po usunięciu łączników (-).

---
### <a name="split"></a>Podział
**Opis:**  
Funkcja Split przyjmuje ciąg oddzielony ogranicznikiem i sprawia, że jest to ciąg wielowartościowy.

**Składni:**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

* wartość: ciąg ze znakiem ogranicznika do oddzielenia.
* ogranicznik: pojedynczy znak, który ma być używany jako ogranicznik.
* limit: maksymalna liczba wartości, które mogą powrócić.

**Przykład:**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
Zwraca ciąg wielowartościowy z 2 elementami przydatnymi dla atrybutu proxyAddress.

---
### <a name="stringfromguid"></a>StringFromGuid (StringFromGuid)
**Opis:**  
Funkcja StringFromGuid przyjmuje binarny identyfikator GUID i konwertuje go na ciąg znaków

**Składni:**  
`str StringFromGuid(bin GUID)`

---
### <a name="stringfromsid"></a>StringFromSid (Wyd.
**Opis:**  
Funkcja StringFromSid konwertuje tablicę bajtów zawierającą identyfikator zabezpieczeń na ciąg.

**Składni:**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="switch"></a>Przełącznik
**Opis:**  
Switch Funkcja służy do zwracania pojedynczej wartości na podstawie ocenionych warunków.

**Składni:**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

* expr: Wyrażenie wariantu, które chcesz ocenić.
* wartość: Wartość do zwrotu, jeśli odpowiednim wyrażeniem jest True.

**Uwagi:**  
Lista argumentów Funkcji Przełączania składa się z par wyrażeń i wartości. Wyrażenia są oceniane od lewej do prawej, a zwracana jest wartość skojarzona z pierwszym wyrażeniem do oceny wartości True. Jeśli części nie są poprawnie sparowane, wystąpi błąd czasu wykonywania.

Na przykład jeśli expr1 jest True, Switch zwraca wartość1. Jeśli expr-1 jest False, ale expr-2 jest True, Switch zwraca wartość-2 i tak dalej.

Przełącznik zwraca nic, jeśli:

* Żadne z wyrażeń nie są prawdziwe.
* Pierwsze wyrażenie True ma odpowiednią wartość, która jest Null.

Switch ocenia wszystkie wyrażenia, nawet jeśli zwraca tylko jeden z nich. Z tego powodu należy uważać na niepożądane skutki uboczne. Na przykład jeśli ocena dowolnego wyrażenia powoduje podział przez zero błędów, występuje błąd.

Wartość może być również funkcja Error, która zwróci ciąg niestandardowy.

**Przykład:**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
Zwraca język używany w niektórych dużych miastach, w przeciwnym razie zwraca błąd.

---
### <a name="trim"></a>Trim
**Opis:**  
Funkcja Przytnij usuwa białe spacje wiodące i końcowe z ciągu.

**Składni:**  
`str Trim(str value)`  

**Przykład:**  
`Trim(" Test ")`  
Zwraca wartość "Test".

`Trim([proxyAddresses])`  
Usuwa spacje początkowe i końcowe dla każdej wartości w atrybucie proxyAddress.

---
### <a name="ucase"></a>Pokań ucase
**Opis:**  
Funkcja UCase konwertuje wszystkie znaki w ciągu na wielkie litery.

**Składni:**  
`str UCase(str string)`

**Przykład:**  
`UCase("TeSt")`  
Zwraca wartość "TEST".

---
### <a name="where"></a>Lokalizacja

**Opis:**  
Zwraca podzbiór wartości z atrybutu wielowartościowego (lub danych wyjściowych wyrażenia) na podstawie określonego warunku.

**Składni:**  
`mvattr Where(variable item, mvattr attribute, exp condition)`  
`mvattr Where(variable item, exp expression, exp condition)`  
* element: Reprezentuje element w atrybucie wielowartościowym
* atrybut: atrybut wielowartościowy
* warunek: dowolne wyrażenie, które można ocenić na wartość true lub false
* wyrażenie: wyrażenie, które zwraca zbiór wartości

**Przykład:**  
`Where($item,[userCertificate],CertNotAfter($item)>Now())`  
Zwraca wartości certyfikatów w wielowartościowym identyfikatorze userCertificate, które nie wygasły.

---
### <a name="with"></a>With
**Opis:**  
Funkcja With umożliwia uproszczenie wyrażenia złożonego przy użyciu zmiennej do reprezentowania wyrażenia podrzędnego, które pojawia się co najmniej jeden razy w wyrażeniu złożonym.

**Składni:**
`With(var variable, exp subExpression, exp complexExpression)`  
* zmienna: reprezentuje wyrażenie podrzędne.
* podwyrażenie: podwyrażenie reprezentowane przez zmienną.
* complexExpression: Złożone wyrażenie.

**Przykład:**  
`With($unExpiredCerts,Where($item,[userCertificate],CertNotAfter($item)>Now()),IIF(Count($unExpiredCerts)>0,$unExpiredCerts,NULL))`  
Jest funkcjonalnie równoważny:  
`IIF (Count(Where($item,[userCertificate],CertNotAfter($item)>Now()))>0, Where($item,[userCertificate],CertNotAfter($item)>Now()),NULL)`  
Który zwraca tylko niewygasłe wartości certyfikatu w userCertificate atrybut.


---
### <a name="word"></a>Word
**Opis:**  
Funkcja Word zwraca wyraz zawarty w ciągu, na podstawie parametrów opisujących ograniczniki do użycia i numer wyrazu do zwrócenia.

**Składni:**  
`str Word(str string, num WordNumber, str delimiters)`

* ciąg: ciąg, z który ma zwracać wyraz.
* Numer Word: liczba identyfikujący numer wyrazu, który powinien zostać odesłany.
* ograniczniki: ciąg reprezentujący ogranicznik(-y), który powinien być używany do identyfikowania wyrazów

**Uwagi:**  
Każdy ciąg znaków w ciągu oddzielony jednym ze znaków w ogranicznikach są identyfikowane jako słowa:

* Jeśli liczba < 1, zwraca pusty ciąg.
* Jeśli ciąg ma wartość null, zwraca pusty ciąg.

Jeśli ciąg zawiera mniej niż słowa liczbowe lub ciąg nie zawiera żadnych słów zidentyfikowanych przez ograniczniki, zwracany jest pusty ciąg.

**Przykład:**  
`Word("The quick brown fox",3," ")`  
Zwraca "brązowy"

`Word("This,string!has&many separators",3,",!&#")`  
Czy powrót "ma"

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Opis wyrażenia inicjowania obsługi administracyjnej deklaratywnej](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Usługa Azure AD Connect Sync: dostosowywanie opcji synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
