---
title: 'Synchronizacja programu Azure AD Connect: Funkcje dokumentacja | Dokumentacja firmy Microsoft'
description: Odwołanie deklaratywne wyrażenia inicjowania obsługi administracyjnej w synchronizacji programu Azure AD Connect.
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
ms.openlocfilehash: b33e993dbddc9c1567a1a6f7d3dca28af240a000
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60381149"
---
# <a name="azure-ad-connect-sync-functions-reference"></a>Synchronizacja programu Azure AD Connect: Informacje ogólne o funkcjach
W programie Azure AD Connect funkcje są używane do manipulowania wartością atrybutu podczas synchronizacji.  
Składnia funkcji jest wyrażona w następującym formacie:  
`<output type> FunctionName(<input type> <position name>, ..)`

Funkcja jest przeciążona i akceptuje wiele składnie, wyświetlane są wszystkie prawidłowe składni.  
Funkcje są silnie typizowane i weryfikuje ona, że typ przekazywany w dopasowań typu udokumentowane.  
Jeśli typ nie jest zgodny, zostanie zgłoszony błąd.

Typy są wyrażone za pomocą następującej składni:

* **pojemnik** — binarny
* **wartość logiczna** — jest to wartość logiczna
* **DT** — Data/Godzina UTC
* **wyliczenie** — wyliczenie znane — stałe
* **EXP** — wyrażenie, które powinien być wartością logiczną
* **mvbin** — binarny z wieloma wartościami
* **mvstr** — parametry wielowartościowe
* **mvref** — odwołanie z wieloma wartościami
* **Liczba** — liczbowe
* **REF** — odwołanie
* **str** — ciąg
* **var** — wariant (prawie) dowolny inny typ
* **void** — nie zwraca wartości

Funkcje z typami **mvbin**, **mvstr**, i **mvref** może działać wyłącznie względem atrybutów wielowartościowych. Funkcje z **bin**, **str**, i **ref** działają na zarówno jedno- i wielowartościowych atrybutach.

## <a name="functions-reference"></a>Informacje ogólne o funkcjach

| Lista funkcji |  |  |  |  |
| --- | --- | --- | --- | --- |
| **Certyfikat** | | | | |
| [CertExtensionOids](#certextensionoids) |[CertFormat](#certformat) |[CertFriendlyName](#certfriendlyname) |[CertHashString](#certhashstring) | |
| [CertIssuer](#certissuer) |[CertIssuerDN](#certissuerdn) |[CertIssuerOid](#certissueroid) |[CertKeyAlgorithm](#certkeyalgorithm) | |
| [CertKeyAlgorithmParams](#certkeyalgorithmparams) |[CertNameInfo](#certnameinfo) |[CertNotAfter](#certnotafter) |[CertNotBefore](#certnotbefore) | |
| [CertPublicKeyOid](#certpublickeyoid) |[CertPublicKeyParametersOid](#certpublickeyparametersoid) |[CertSerialNumber](#certserialnumber) |[CertSignatureAlgorithmOid](#certsignaturealgorithmoid) | |
| [CertSubject](#certsubject) |[CertSubjectNameDN](#certsubjectnamedn) |[CertSubjectNameOid](#certsubjectnameoid) |[CertThumbprint](#certthumbprint) | |
[CertVersion](#certversion) |[IsCert](#iscert) | | | |
| **Konwersja** | | | | |
| [CBool](#cbool) |[CDate](#cdate) |[CGuid](#cguid) |[ConvertFromBase64](#convertfrombase64) | |
| [ConvertToBase64](#converttobase64) |[ConvertFromUTF8Hex](#convertfromutf8hex) |[ConvertToUTF8Hex](#converttoutf8hex) |[CNum](#cnum) | |
| [CRef](#cref) |[CStr](#cstr) |[StringFromGuid](#stringfromguid) |[StringFromSid](#stringfromsid) | |
| **Data / Godzina** | | | | |
| [DateAdd](#dateadd) |[DateFromNum](#datefromnum) |[formatDateTime](#formatdatetime) |[teraz](#now) | |
| [NumFromDate](#numfromdate) | | | | |
| **Directory** | | | | |
| [DNComponent](#dncomponent) |[DNComponentRev](#dncomponentrev) |[EscapeDNComponent](#escapedncomponent) | | |
| **Ocena** | | | | |
| [IsBitSet](#isbitset) |[IsDate](#isdate) |[IsEmpty](#isempty) |[IsGuid](#isguid) | |
| [IsNull](#isnull) |[IsNullOrEmpty](#isnullorempty) |[Funkcja IsNumeric](#isnumeric) |[IsPresent](#ispresent) | |
| [IsString](#isstring) | | | | |
| **Matematyczne** | | | | |
| [BitAnd](#bitand) |[BitOr](#bitor) |[RandomNum](#randomnum) | | |
| **Wielokrotne** | | | | |
| [zawiera](#contains) |[Liczba](#count) |[Element](#item) |[ItemOrNull](#itemornull) | |
| [Join](#join) |[RemoveDuplicates](#removeduplicates) |[Podziel](#split) | | |
| **Przepływ programu** | | | | |
| [Error](#error) |[IIF](#iif) |[Select](#select) |[Przełącznik](#switch) | |
| [Where](#where) |[With](#with) | | | |
| **Text** | | | | |
| [GUID](#guid) |[InStr](#instr) |[InStrRev](#instrrev) |[LCase](#lcase) | |
| [po lewej stronie](#left) |[Len](#len) |[LTrim](#ltrim) |[Mid](#mid) | |
| [PadLeft](#padleft) |[PadRight](#padright) |[PCase](#pcase) |[Zastąp](#replace) | |
| [ReplaceChars](#replacechars) |[po prawej stronie](#right) |[Przytk](#rtrim) |[TRIM](#trim) | |
| [UCase](#ucase) |[Word](#word) | | | |

- - -
### <a name="bitand"></a>BitAnd
**Opis:**  
BITAND — funkcja ustawia bity określony na podstawie wartości.

**Składnia:**  
`num BitAnd(num value1, num value2)`

* Wartość1, wartość2: wartości liczbowe, które powinny być ze sobą tematyczne

**Uwagi:**  
Ta funkcja konwertuje oba parametry do reprezentacji binarnej i ustawia bit:

* 0 — Jeśli jeden lub oba z odpowiednich bitów w *maski* i *flagi* to 0
* 1 — Jeśli są oba odpowiednich bitów 1.

Innymi słowy we wszystkich przypadkach, z wyjątkiem sytuacji, gdy bity odpowiedniej oba parametry są 1 zwraca 0.

**Przykład:**  
`BitAnd(&HF, &HF7)`  
Zwraca wartość 7, ponieważ szesnastkowe "F" i "F7" obliczyć tej wartości.

- - -
### <a name="bitor"></a>BitOr
**Opis:**  
BITOR — funkcja ustawia bity określony na podstawie wartości.

**Składnia:**  
`num BitOr(num value1, num value2)`

* Wartość1, wartość2: wartości liczbowe, które należy zsumować logicznie razem

**Uwagi:**  
Ta funkcja konwertuje oba parametry reprezentacja binarna i ustawia bit 1, jeśli jeden lub oba odpowiednich bitów w flagę i maski to 1 i 0, gdy oba odpowiednich bitów są 0. Innymi słowy zwraca 1 we wszystkich przypadkach, z wyjątkiem sytuacji, w których bity odpowiedniej oba parametry na 0.

- - -
### <a name="cbool"></a>CBool
**Opis:**  
CBool-funkcja zwraca wartość logiczną, w oparciu o obliczane wyrażenie

**Składnia:**  
`bool CBool(exp Expression)`

**Uwagi:**  
Jeśli wyrażenie ma wartość różną od zera, a następnie CBool zwraca wartość PRAWDA, przeciwnym razie zwraca wartość False.

**Przykład:**  
`CBool([attrib1] = [attrib2])`  

Zwraca wartość True, jeśli oba atrybuty mają taką samą wartość.

- - -
### <a name="cdate"></a>CDate
**Opis:**  
CDate-funkcja zwraca wartość daty/godziny UTC z ciągu. Daty i godziny nie jest typem atrybutu natywne w synchronizacji, ale jest używany przez niektóre funkcje.

**Składnia:**  
`dt CDate(str value)`

* Wartość: Ciąg zawierający daty, godziny oraz opcjonalnie strefy czasowej

**Uwagi:**  
Zwracanego ciągu jest zawsze w formacie UTC.

**Przykład:**  
`CDate([employeeStartTime])`  
Zwraca wartość typu DateTime na podstawie których pracownika czas rozpoczęcia

`CDate("2013-01-10 4:00 PM -8")`  
Zwraca wartość daty/godziny reprezentująca "2013-01-11: 00:00:00"


- - -
### <a name="certextensionoids"></a>CertExtensionOids
**Opis:**  
Zwraca wartości identyfikatora Oid rozszerzenia krytyczne obiektu certyfikatu.

**Składnia:**  
`mvstr CertExtensionOids(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowany plik binarny (DER) lub dane X.509 szyfrowany algorytmem Base64.

- - -
### <a name="certformat"></a>CertFormat
**Opis:**  
Zwraca nazwę formatu certyfikatu X.509v3.

**Składnia:**  
`str CertFormat(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowany plik binarny (DER) lub dane X.509 szyfrowany algorytmem Base64.

- - -
### <a name="certfriendlyname"></a>CertFriendlyName
**Opis:**  
Zwraca skojarzonego aliasu dla certyfikatu.

**Składnia:**  
`str CertFriendlyName(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowany plik binarny (DER) lub dane X.509 szyfrowany algorytmem Base64.

- - -
### <a name="certhashstring"></a>CertHashString
**Opis:**  
Zwraca wartość skrótu SHA1 certyfikatu X.509v3 jako ciąg szesnastkowy.

**Składnia:**  
`str CertHashString(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowany plik binarny (DER) lub dane X.509 szyfrowany algorytmem Base64.

- - -
### <a name="certissuer"></a>CertIssuer
**Opis:**  
Zwraca nazwę urzędu certyfikacji, który wystawił certyfikat X.509v3.

**Składnia:**  
`str CertIssuer(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowany plik binarny (DER) lub dane X.509 szyfrowany algorytmem Base64.

- - -
### <a name="certissuerdn"></a>CertIssuerDN
**Opis:**  
Zwraca nazwę wyróżniającą wystawcy certyfikatu.

**Składnia:**  
`str CertIssuerDN(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowany plik binarny (DER) lub dane X.509 szyfrowany algorytmem Base64.

- - -
### <a name="certissueroid"></a>CertIssuerOid
**Opis:**  
Zwraca identyfikator Oid wystawcy certyfikatu.

**Składnia:**  
`str CertIssuerOid(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowany plik binarny (DER) lub dane X.509 szyfrowany algorytmem Base64.

- - -
### <a name="certkeyalgorithm"></a>CertKeyAlgorithm
**Opis:**  
Zwraca informacje algorytm klucza dla tego certyfikatu X.509v3 jako ciąg.

**Składnia:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowany plik binarny (DER) lub dane X.509 szyfrowany algorytmem Base64.

- - -
### <a name="certkeyalgorithmparams"></a>CertKeyAlgorithmParams
**Opis:**  
Zwraca parametry algorytmu klucza dla certyfikatu X.509v3 jako ciąg szesnastkowy.

**Składnia:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowany plik binarny (DER) lub dane X.509 szyfrowany algorytmem Base64.

- - -
### <a name="certnameinfo"></a>CertNameInfo
**Opis:**  
Zwraca podmiot i Wystawca nazwy z certyfikatu.

**Składnia:**  
`str CertNameInfo(binary certificateRawData, str x509NameType, bool includesIssuerName)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowany plik binarny (DER) lub dane X.509 szyfrowany algorytmem Base64.
*   X509NameType: Wartość X509NameType tematu.
*   includesIssuerName: true, aby zawierał on nazwę wystawcy; w przeciwnym razie wartość false.

- - -
### <a name="certnotafter"></a>CertNotAfter
**Opis:**  
Zwraca datę w czasie lokalnym, po upływie którego certyfikat nie jest już prawidłowy.

**Składnia:**  
`dt CertNotAfter(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowany plik binarny (DER) lub dane X.509 szyfrowany algorytmem Base64.

- - -
### <a name="certnotbefore"></a>CertNotBefore
**Opis:**  
Zwraca datę w czasie lokalnym, na którym zaczyna obowiązywać certyfikat.

**Składnia:**  
`dt CertNotBefore(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowany plik binarny (DER) lub dane X.509 szyfrowany algorytmem Base64.

- - -
### <a name="certpublickeyoid"></a>CertPublicKeyOid
**Opis:**  
Zwraca identyfikator Oid klucza publicznego dla certyfikatu X.509v3.

**Składnia:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowany plik binarny (DER) lub dane X.509 szyfrowany algorytmem Base64.

- - -
### <a name="certpublickeyparametersoid"></a>CertPublicKeyParametersOid
**Opis:**  
Zwraca identyfikator Oid parametrów klucza publicznego certyfikatu X.509v3.

**Składnia:**  
`str CertPublicKeyParametersOid(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowany plik binarny (DER) lub dane X.509 szyfrowany algorytmem Base64.

- - -
### <a name="certserialnumber"></a>CertSerialNumber
**Opis:**  
Zwraca numer seryjny certyfikatu X.509v3.

**Składnia:**  
`str CertSerialNumber(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowany plik binarny (DER) lub dane X.509 szyfrowany algorytmem Base64.

- - -
### <a name="certsignaturealgorithmoid"></a>CertSignatureAlgorithmOid
**Opis:**  
Zwraca identyfikator Oid algorytm używany do tworzenia podpisu certyfikatu.

**Składnia:**  
`str CertSignatureAlgorithmOid(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowany plik binarny (DER) lub dane X.509 szyfrowany algorytmem Base64.

- - -
### <a name="certsubject"></a>CertSubject
**Opis:**  
Pobiera nazwa wyróżniająca podmiotu z certyfikatu.

**Składnia:**  
`str CertSubject(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowany plik binarny (DER) lub dane X.509 szyfrowany algorytmem Base64.

- - -
### <a name="certsubjectnamedn"></a>CertSubjectNameDN
**Opis:**  
Zwraca nazwa wyróżniająca podmiotu z certyfikatu.

**Składnia:**  
`str CertSubjectNameDN(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowany plik binarny (DER) lub dane X.509 szyfrowany algorytmem Base64.

- - -
### <a name="certsubjectnameoid"></a>CertSubjectNameOid
**Opis:**  
Zwraca identyfikator Oid nazwy podmiotu z certyfikatu.

**Składnia:**  
`str CertSubjectNameOid(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowany plik binarny (DER) lub dane X.509 szyfrowany algorytmem Base64.

- - -
### <a name="certthumbprint"></a>CertThumbprint
**Opis:**  
Zwraca odcisk palca certyfikatu.

**Składnia:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowany plik binarny (DER) lub dane X.509 szyfrowany algorytmem Base64.

- - -
### <a name="certversion"></a>CertVersion
**Opis:**  
Zwraca wersja formatu X.509 certyfikatu.

**Składnia:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowany plik binarny (DER) lub dane X.509 szyfrowany algorytmem Base64.

- - -
### <a name="cguid"></a>CGuid
**Opis:**  
Funkcja CGuid konwertuje ciąg reprezentujący identyfikator GUID, na jego reprezentację binarną.

**Składnia:**  
`bin CGuid(str GUID)`

* Ciąg sformatowany w tym wzorcu: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx lub {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

- - -
### <a name="contains"></a>zawiera
**Opis:**  
Funkcja zawiera umożliwia znalezienie ciągu wewnątrz atrybutu wielowartościowego

**Składnia:**  
`num Contains (mvstring attribute, str search)` -uwzględniana wielkość liter  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)` -uwzględniana wielkość liter

* Atrybut: atrybutów wielowartościowych do wyszukania.
* wyszukiwania: ciąg do znalezienia w atrybucie.
* Casetype: CaseInsensitive lub CaseSensitive.

Zwraca indeks atrybutu wielowartościowego, w którym ten ciąg został znaleziony. 0 jest zwracana, jeśli nie zostanie znaleziony ciąg.

**Uwagi:**  
Atrybuty wielowartościowe ciągu wyszukiwania umożliwia znalezienie wartości podciągów.  
W przypadku atrybutów odwołania przeszukiwanego ciągu musi dokładnie odpowiadać wartości, aby zostały uznane za dopasowanie.

**Przykład:**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
Jeśli atrybut proxyAddresses ma podstawowego adresu e-mail (wskazywanym przez wielkie litery "SMTP:"), zwracany jest atrybutem proxyAddress, w przeciwnym razie zwraca błąd.

- - -
### <a name="convertfrombase64"></a>ConvertFromBase64
**Opis:**  
Funkcja ConvertFromBase64 konwertuje wartość określonego zakodowane w formacie base64 regularne ciągu.

**Składnia:**  
`str ConvertFromBase64(str source)` -przyjmuje Unicode dla kodowania  
`str ConvertFromBase64(str source, enum Encoding)`

* Źródło: Ciąg zakodowany w formacie Base64  
* Encoding: Unicode, ASCII, UTF8

**Przykład**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Oba przykłady zwrócą "*Witaj świecie!* "

- - -
### <a name="convertfromutf8hex"></a>ConvertFromUTF8Hex
**Opis:**  
Funkcja ConvertFromUTF8Hex konwertuje określoną wartość Hex UTF8 zakodowane na ciąg.

**Składnia:**  
`str ConvertFromUTF8Hex(str source)`

* Źródło: Stingu zakodowany 2-bajtowych UTF8

**Uwagi:**  
Różnica między tej funkcji i ConvertFromBase64([],UTF8), wynik jest przyjazna dla atrybutu nazwy domeny.  
Ten format jest używany przez usługę Azure Active Directory jako nazwa Wyróżniająca.

**Przykład:**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
Zwraca "*Witaj świecie!* "

- - -
### <a name="converttobase64"></a>ConvertToBase64
**Opis:**  
Funkcja ConvertToBase64 konwertuje ciąg na ciąg base64 Unicode.  
Konwertuje wartość tablicy liczb całkowitych na jego reprezentację ciągu równoważnego, który jest kodowany przy użyciu cyfr base-64.

**Składnia:**  
`str ConvertToBase64(str source)`

**Przykład:**  
`ConvertToBase64("Hello world!")`  
Zwraca wartość "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

- - -
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Opis:**  
Funkcja ConvertToUTF8Hex konwertuje ciąg na wartość Hex UTF8 zakodowany.

**Składnia:**  
`str ConvertToUTF8Hex(str source)`

**Uwagi:**  
Format danych wyjściowych z tej funkcji jest używany przez usługę Azure Active Directory jako nazwa Wyróżniająca atrybut formatu.

**Przykład:**  
`ConvertToUTF8Hex("Hello world!")`  
Zwraca 48656C6C6F20776F726C6421

- - -
### <a name="count"></a>Count
**Opis:**  
Count — funkcja zwraca liczbę elementów w atrybutu wielowartościowego

**Składnia:**  
`num Count(mvstr attribute)`

- - -
### <a name="cnum"></a>CNum
**Opis:**  
Funkcja CNum przyjmuje ciąg i zwraca dane typu liczbowego.

**Składnia:**  
`num CNum(str value)`

- - -
### <a name="cref"></a>CRef
**Opis:**  
Konwertuje ciąg na atrybut odwołania

**Składnia:**  
`ref CRef(str value)`

**Przykład:**  
`CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

- - -
### <a name="cstr"></a>CStr
**Opis:**  
Konwertuje funkcję CStr typie danych ciągu.

**Składnia:**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* value: Może to być wartość liczbową, atrybut odwołania lub atrybut typu wartość logiczna.

**Przykład:**  
`CStr([dn])`  
Zwracanie "cn = Jan, dc = contoso, dc = com"

- - -
### <a name="dateadd"></a>DateAdd
**Opis:**  
Zwraca wartość typu Date zawierającą datę, do której dodano określony przedział czasu.

**Składnia:**  
`dt DateAdd(str interval, num value, dt date)`

* interval: Wyrażenie ciągu, który jest interwałem czasu, które chcesz dodać. Ciąg musi mieć jedną z następujących wartości:
  * rrrr roku
  * q kwartał
  * mln miesiąc
  * y dzień roku
  * d dzień
  * w dzień tygodnia
  * ww tygodnia
  * h Godzina
  * n minut
  * s drugiego
* value: Liczba jednostek, które chcesz dodać. Może być dodatnia (Aby uzyskać daty w przyszłości) lub ujemna (Aby uzyskać daty w przeszłości).
* Data: Daty/godziny reprezentująca datę, do którego zostanie dodany interwału.

**Przykład:**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
Dodaje 3 miesiące i zwraca wartość typu DateTime reprezentujący "2001-04-01".

- - -
### <a name="datefromnum"></a>DateFromNum
**Opis:**  
Konwertuje funkcję DateFromNum formatu wartości daty AD typu DateTime.

**Składnia:**  
`dt DateFromNum(num value)`

**Przykład:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Zwraca wartość typu DateTime reprezentujący 2012-01-01-23:00:00

- - -
### <a name="dncomponent"></a>DNComponent
**Opis:**  
Funkcja DNComponent zwraca wartość określonego składnika DN z lewej strony.

**Składnia:**  
`str DNComponent(ref dn, num ComponentNumber)`

* Nazwa wyróżniająca: atrybut odwołania do interpretacji
* ComponentNumber: Składnik nazwy domeny do zwrócenia

**Przykład:**  
`DNComponent(CRef([dn]),1)`  
Jeśli nazwa wyróżniająca jest "cn = Jan, ou =...," zwraca Jan

- - -
### <a name="dncomponentrev"></a>DNComponentRev
**Opis:**  
Funkcja DNComponentRev zwraca wartość określonego składnika DN, przechodząc z prawej strony (Zakończ).

**Składnia:**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

* Nazwa wyróżniająca: atrybut odwołania do interpretacji
* ComponentNumber — składnik w DN do zwrócenia
* Opcje: Kontroler domeny — Ignoruj wszystkich składników za pomocą "dc ="

**Przykład:**  
Jeśli nazwa wyróżniająca jest "cn = Jan, jednostki organizacyjnej Atlanta, ou = GA, ou = = US, dc = contoso, dc = com" następnie  
`DNComponentRev(CRef([dn]),3)`  
`DNComponentRev(CRef([dn]),1,"DC")`  
Oba zwracają USA.

- - -
### <a name="error"></a>Błąd
**Opis:**  
Funkcja błędu jest używana do zwrócenia błędu niestandardowego.

**Składnia:**  
`void Error(str ErrorMessage)`

**Przykład:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Jeśli accountName atrybut nie jest obecny, sygnalizować błąd, na obiekcie.

- - -
### <a name="escapedncomponent"></a>EscapeDNComponent
**Opis:**  
Funkcja EscapeDNComponent przyjmuje jeden składnik nazwy domen i jego specjalne, dzięki czemu mogą być reprezentowane w protokole LDAP.

**Składnia:**  
`str EscapeDNComponent(str value)`

**Przykład:**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
Zapewnia, że obiekt mogą być tworzone w katalogu LDAP, nawet wtedy, gdy atrybut displayName zawiera znaki, które muszą być wyjściowym w protokole LDAP.

- - -
### <a name="formatdatetime"></a>FormatDateTime
**Opis:**  
Funkcja FormatDateTime jest używany do formatowania daty/godziny do ciągu w określonym formacie

**Składnia:**  
`str FormatDateTime(dt value, str format)`

* wartość: wartość w formacie daty/godziny
* Format: ciąg reprezentujący w formacie, aby przekonwertować.

**Uwagi:**  
Możliwe wartości dla formatu można znaleźć tutaj: [Niestandardowa data i godzina formatów dla funkcji FORMATUJĄCEJ](https://docs.microsoft.com/dax/custom-date-and-time-formats-for-the-format-function).

**Przykład:**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-mm-dd")`  
Wyniki w "2007-12-25".

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
Może spowodować "20140905081453.0Z"

- - -
### <a name="guid"></a>Guid
**Opis:**  
Funkcja Guid generuje nowy, losowy identyfikator GUID

**Składnia:**  
`str Guid()`

- - -
### <a name="iif"></a>IIF
**Opis:**  
IIF — funkcja zwraca jeden zestaw możliwych wartości na podstawie określonego warunku.

**Składnia:**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* warunek: dowolna wartość lub wyrażenie, które może przyjąć wartość true lub false.
* valueIfTrue: Jeśli warunek jest spełniony, zwrócona wartość.
* valueIfFalse: Jeśli wyrażenie zwróci wartość false, zwracana wartość.

**Przykład:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Jeśli użytkownik jest serii, zwraca alias użytkownika za pomocą "t-" na początku jego else zwraca alias użytkownika, ponieważ jest.

- - -
### <a name="instr"></a>InStr
**Opis:**  
Funkcja InStr znajduje pierwsze wystąpienie podciągu w ciągu

**Składnia:**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

* ciąg_przeszukiwany: ciąg do wyszukania
* ciąg_poszukiwany: ciąg do znalezienia
* Start: uruchamianie pozycji, aby znaleźć podciąg
* Porównaj: vbTextCompare lub vbBinaryCompare

**Uwagi:**  
Zwraca pozycję, której podciąg został znaleziony, lub 0, jeśli nie można odnaleźć.

**Przykład:**  
`InStr("The quick brown fox","quick")`  
Evalues 5

`InStr("repEated","e",3,vbBinaryCompare)`  
Daje w wyniku 7

- - -
### <a name="instrrev"></a>InStrRev
**Opis:**  
Funkcja InStrRev znajduje ostatnie wystąpienie podciągu w ciągu

**Składnia:**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

* ciąg_przeszukiwany: ciąg do wyszukania
* ciąg_poszukiwany: ciąg do znalezienia
* Start: uruchamianie pozycji, aby znaleźć podciąg
* Porównaj: vbTextCompare lub vbBinaryCompare

**Uwagi:**  
Zwraca pozycję, której podciąg został znaleziony, lub 0, jeśli nie można odnaleźć.

**Przykład:**  
`InStrRev("abbcdbbbef","bb")`  
Zwraca 7

- - -
### <a name="isbitset"></a>IsBitSet
**Opis:**  
Funkcja IsBitSet sprawdza, czy jest to nieco została ustawiona, czy nie

**Składnia:**  
`bool IsBitSet(num value, num flag)`

* wartość: wartość liczbowa, która jest evaluated.flag: wartość liczbowa, która ma bitu, który ma zostać obliczone

**Przykład:**  
`IsBitSet(&HF,4)`  
Zwraca wartość True, ponieważ wartość szesnastkową "F" jest ustawiony bit "4"

- - -
### <a name="isdate"></a>IsDate
**Opis:**  
Jeśli wyrażenie może być ocenia jako typ Data/Godzina, a następnie funkcja IsDate ma wartość True.

**Składnia:**  
`bool IsDate(var Expression)`

**Uwagi:**  
Używany do określenia, jeśli CDate() może odnieść sukces.

- - -
### <a name="iscert"></a>IsCert
**Opis:**  
Zwraca wartość PRAWDA, jeśli danych pierwotnych może być serializowany do obiektu certyfikatu .NET X509Certificate2.

**Składnia:**  
`bool CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Reprezentacja tablicy bajtów certyfikatu X.509. Tablica bajtów może być zakodowany plik binarny (DER) lub dane X.509 szyfrowany algorytmem Base64.
- - -
### <a name="isempty"></a>IsEmpty
**Opis:**  
Jeśli atrybut znajduje się w CS lub MV, ale jest pustym ciągiem, następnie funkcja IsEmpty ma wartość True.

**Składnia:**  
`bool IsEmpty(var Expression)`

- - -
### <a name="isguid"></a>IsGuid
**Opis:**  
Jeśli ciąg można przekonwertować na identyfikator GUID, funkcja IsGuid obliczone na wartość true.

**Składnia:**  
`bool IsGuid(str GUID)`

**Uwagi:**  
Identyfikator GUID jest definiowany jako ciąg zgodnie z jedną z tych wzorców: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx lub {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

Używany do określenia, jeśli CGuid() może odnieść sukces.

**Przykład:**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
Jeśli StrAttribute ma format identyfikatora GUID, zwraca reprezentację binarną, w przeciwnym razie zwraca wartość Null.

- - -
### <a name="isnull"></a>IsNull
**Opis:**  
Jeśli wyrażenie ma wartość Null, funkcja IsNull zwraca wartość true.

**Składnia:**  
`bool IsNull(var Expression)`

**Uwagi:**  
Dla atrybutu o wartości Null jest wyrażona braku atrybutu.

**Przykład:**  
`IsNull([displayName])`  
Zwraca wartość PRAWDA, jeśli ten atrybut nie jest obecny w CS lub MV.

- - -
### <a name="isnullorempty"></a>IsNullOrEmpty
**Opis:**  
Jeśli wyrażenie ma wartość null lub pusty ciąg, funkcja IsNullOrEmpty zwraca wartość true.

**Składnia:**  
`bool IsNullOrEmpty(var Expression)`

**Uwagi:**  
Dla atrybutu to czy zwrócić wartość True, jeśli ten atrybut jest nieobecny lub jest zainstalowany, ale jest ciągiem pustym.  
Odwrotność ta funkcja nosi nazwę IsPresent.

**Przykład:**  
`IsNullOrEmpty([displayName])`  
Zwraca wartość PRAWDA, jeśli ten atrybut nie istnieje lub jest pustym ciągiem w CS lub MV.

- - -
### <a name="isnumeric"></a>IsNumeric
**Opis:**  
Funkcja IsNumeric zwraca wartość logiczną wskazującą, czy wyrażenie może przyjąć jako liczba typu.

**Składnia:**  
`bool IsNumeric(var Expression)`

**Uwagi:**  
Pozwala określić, czy CNum() mogą być pomyślnie przeanalizować wyrażenia.

- - -
### <a name="isstring"></a>IsString
**Opis:**  
Jeśli do typu ciąg, można obliczyć wyrażenia, następnie funkcja IsString ma wartość True.

**Składnia:**  
`bool IsString(var expression)`

**Uwagi:**  
Pozwala określić, czy CStr() mogą być pomyślnie przeanalizować wyrażenia.

- - -
### <a name="ispresent"></a>IsPresent
**Opis:**  
Jeśli wyrażenie na ciąg, który nie ma wartości Null i nie jest pusta, funkcja IsPresent zwraca wartość true.

**Składnia:**  
`bool IsPresent(var expression)`

**Uwagi:**  
Odwrotność ta funkcja nosi nazwę IsNullOrEmpty.

**Przykład:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

- - -
### <a name="item"></a>Element
**Opis:**  
Funkcja Item zwraca jeden element z ciągu/atrybutów wielowartościowych.

**Składnia:**  
`var Item(mvstr attribute, num index)`

* Atrybut: atrybutu wielowartościowego
* Indeks: indeks elementu w ciągu wielowartościowych.

**Uwagi:**  
Funkcja Item przydaje się wraz z funkcji zawiera od czasu ostatniego funkcja zwraca indeks elementu w atrybutów wielowartościowych.

Zgłasza błąd, jeśli indeks jest poza granicami.

**Przykład:**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
Zwraca adres podstawowy adres e-mail.

- - -
### <a name="itemornull"></a>ItemOrNull
**Opis:**  
Funkcja ItemOrNull zwraca jeden element z ciągu/atrybutów wielowartościowych.

**Składnia:**  
`var ItemOrNull(mvstr attribute, num index)`

* Atrybut: atrybutu wielowartościowego
* Indeks: indeks elementu w ciągu wielowartościowych.

**Uwagi:**  
Funkcja ItemOrNull przydaje się wraz z funkcji zawiera od czasu ostatniego funkcja zwraca indeks elementu w atrybutów wielowartościowych.

Jeśli indeks jest poza granicami, funkcja zwraca wartość Null.

- - -
### <a name="join"></a>Dołączanie
**Opis:**  
Funkcja sprzężenia przyjmuje parametry wielowartościowe i zwraca ciąg jednowartościowych przy użyciu określonego separatora wstawiany między poszczególne elementy.

**Składnia:**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

* Atrybut: Wielowartościowy atrybut zawierającą ciągi, które mają zostać połączone.
* Ogranicznik: Dowolny ciąg używany do rozdzielania podciągów w zwracanym ciągu. W przypadku pominięcia znaku spacji ("") jest używany. Jeśli ogranicznikiem jest ciągiem o zerowej długości ("") lub nic, wszystkie elementy na liście są łączone nie ogranicznikami.

**Uwagi**  
Występuje parzystość między funkcjami dołączania i podziału. Funkcja sprzężenia przyjmuje tablicę ciągów i dołączania ich przy użyciu ciągu ogranicznika, aby zwrócić pojedynczy ciąg. Funkcja Split przyjmuje ciąg i oddziela go na ogranicznika, aby zwracało tablicę ciągów. Najważniejszą różnicą jest jednak, że sprzężenia mogą ciągów się dowolnym ciągiem ogranicznik, podziału jedynie rozdzielić ciągów za pomocą pojedynczego znaku ogranicznika.

**Przykład:**  
`Join([proxyAddresses],",")`  
Zwracanie: "SMTP:john.doe@contoso.com,smtp:jd@contoso.com"

- - -
### <a name="lcase"></a>LCase
**Opis:**  
Funkcja LCase konwertuje wszystkie znaki w ciągu na małe litery.

**Składnia:**  
`str LCase(str value)`

**Przykład:**  
`LCase("TeSt")`  
Zwraca "test".

- - -
### <a name="left"></a>po lewej stronie
**Opis:**  
Po lewej stronie funkcja zwraca określoną liczbę znaków z lewej strony ciągu.

**Składnia:**  
`str Left(str string, num NumChars)`

* ciąg: ciąg do zwrócenia znaków z
* NumChars: numer identyfikujący liczba znaków do zwrócenia od początku ciągu (po lewej)

**Uwagi:**  
Ciąg zawierający pierwszych znaków numChars w ciągu:

* Jeśli numChars = 0, zwraca pusty ciąg.
* Jeśli numChars < 0, zwraca ciąg wejściowy.
* Jeśli ciąg ma wartość null, zwraca pusty ciąg.

Jeśli ciąg zawiera mniej znaków niż liczba numChars określonych w, zostanie zwrócony ciąg jest taka sama jak ciąg (który jest zawierający wszystkie znaki w parametrze 1).

**Przykład:**  
`Left("John Doe", 3)`  
Zwraca wartość "Joh".

- - -
### <a name="len"></a>Len
**Opis:**  
Funkcja Len zwraca liczbę znaków w ciągu.

**Składnia:**  
`num Len(str value)`

**Przykład:**  
`Len("John Doe")`  
Zwraca 8

- - -
### <a name="ltrim"></a>LTrim
**Opis:**  
Funkcja LTrim usuwa wiodące spacje z ciągu.

**Składnia:**  
`str LTrim(str value)`

**Przykład:**  
`LTrim(" Test ")`  
Zwraca "Test"

- - -
### <a name="mid"></a>MID
**Opis:**  
Funkcja Mid zwraca określoną liczbę znaków od określonej pozycji w ciągu.

**Składnia:**  
`str Mid(str string, num start, num NumChars)`

* ciąg: ciąg do zwrócenia znaków z
* Start: numer identyfikujący początkowy pozycji w ciągu, aby zwrócić znaków z
* NumChars: numer identyfikujący liczba znaków do zwrócenia z pozycji w ciągu

**Uwagi:**  
Rozpocznij zwracany numChars znaków, zaczynając od pozycji w ciągu.  
Ciąg zawierający znaki numChars od początku pozycja w ciągu:

* Jeśli numChars = 0, zwraca pusty ciąg.
* Jeśli numChars < 0, zwraca ciąg wejściowy.
* Jeśli start > długość ciągu, zwróć ciąg wejściowy.
* Jeśli start < = 0, zwróć ciąg wejściowy.
* Jeśli ciąg ma wartość null, zwraca pusty ciąg.

Jeśli nie są znaki numChar pozostały w ciągu od początku pozycji, jak najwięcej znaków, jak to możliwe są zwracane.

**Przykład:**  
`Mid("John Doe", 3, 5)`  
Zwraca wartość "czy hn".

`Mid("John Doe", 6, 999)`  
Zwraca wartość "Doe"

- - -
### <a name="now"></a>teraz
**Opis:**  
Teraz funkcja zwraca wartość typu DateTime, określając aktualnej daty i godziny, zgodnie z systemowej daty i godziny na komputerze.

**Składnia:**  
`dt Now()`

- - -
### <a name="numfromdate"></a>NumFromDate
**Opis:**  
Funkcja NumFromDate zwraca datę w formacie daty usługi AD.

**Składnia:**  
`num NumFromDate(dt value)`

**Przykład:**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
Zwraca 129699324000000000

- - -
### <a name="padleft"></a>padLeft
**Opis:**  
PadLeft — funkcja po lewej stronie podkładki ciąg do określonej długości, przy użyciu znaku podana dopełnienia.

**Składnia:**  
`str PadLeft(str string, num length, str padCharacter)`

* ciąg: ciąg do wypełnienia.
* Czas trwania: Liczba całkowita reprezentująca wymagana długość ciągu.
* padCharacter: Ciąg zawierający pojedynczy znak używany jako znak konsoli

**Uwagi:**

* Jeśli długość ciągu jest mniejsza niż długość, padCharacter wielokrotnie jest dołączany na początku (po lewej) ciągu do czasu jego długość równa długości.
* PadCharacter może być znakiem spacji, ale nie może być wartością null.
* Jeśli długość ciągu jest równa lub większa niż długość, ciąg jest zwracany bez zmian.
* Jeśli ciąg ma długość większą niż lub równa długości, zwracany jest taka sama jak ciąg znaków ciągu.
* Jeśli długość ciągu jest mniejsza niż długość, nowy ciąg o długości żądaną zwracany ciąg zawierający dopełniana padCharacter.
* Jeśli ciąg ma wartość null, funkcja zwraca pusty ciąg.

**Przykład:**  
`PadLeft("User", 10, "0")`  
Zwraca wartość "000000User".

- - -
### <a name="padright"></a>Padright —
**Opis:**  
Padright — funkcja po prawej stronie podkładki ciąg do określonej długości, przy użyciu znaku podana dopełnienia.

**Składnia:**  
`str PadRight(str string, num length, str padCharacter)`

* ciąg: ciąg do wypełnienia.
* Czas trwania: Liczba całkowita reprezentująca wymagana długość ciągu.
* padCharacter: Ciąg zawierający pojedynczy znak używany jako znak konsoli

**Uwagi:**

* Jeśli długość ciągu jest mniejsza niż długość, następnie padCharacter jest wielokrotnie dołączany-to-end (po prawej) ciągu do czasu jego długość jest równa długości.
* PadCharacter może być znakiem spacji, ale nie może być wartością null.
* Jeśli długość ciągu jest równa lub większa niż długość, ciąg jest zwracany bez zmian.
* Jeśli ciąg ma długość większą niż lub równa długości, zwracany jest taka sama jak ciąg znaków ciągu.
* Jeśli długość ciągu jest mniejsza niż długość, nowy ciąg o długości żądaną zwracany ciąg zawierający dopełniana padCharacter.
* Jeśli ciąg ma wartość null, funkcja zwraca pusty ciąg.

**Przykład:**  
`PadRight("User", 10, "0")`  
Zwraca wartość "User000000".

- - -
### <a name="pcase"></a>PCase
**Opis:**  
Funkcja PCase konwertuje pierwszego znaku w każdym słowie rozdzielany spacjami ciąg na wielkie litery, a wszystkie inne znaki są konwertowane na małe litery.

**Składnia:**  
`String PCase(string)`

**Uwagi:**

* Ta funkcja nie jest aktualnie dostępny odpowiedniej wielkości przekonwertować programu word, który jest całkowicie wielkie litery, takich jak akronim.

**Przykład:**  
`PCase("TEsT")`  
Zwraca wartość "Test".

`PCase(LCase("TEST"))`  
Zwraca "Test"

- - -
### <a name="randomnum"></a>RandomNum
**Opis:**  
Funkcja RandomNum Zwraca losową liczbę między określonym przedziale czasu.

**Składnia:**  
`num RandomNum(num start, num end)`

* Start: numer identyfikujący dolną granicę wartości losowej, które można wygenerować
* Koniec: numer identyfikujący górny limit liczby losowe wartości, które można wygenerować

**Przykład:**  
`Random(100,999)`  
Może zwracać 734.

- - -
### <a name="removeduplicates"></a>Removeduplicates —
**Opis:**  
Removeduplicates — funkcja przyjmuje parametry wielowartościowe i upewnij się, że każda wartość jest unikatowa.

**Składnia:**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Przykład:**  
`RemoveDuplicates([proxyAddresses])`  
Zwraca atrybutem proxyAddress oczyszczony, w której zostały usunięte wszystkie zduplikowane wartości.

- - -
### <a name="replace"></a>Replace
**Opis:**  
Funkcji Replace zamienia wszystkie wystąpienia ciągu do innego ciągu.

**Składnia:**  
`str Replace(str string, str OldValue, str NewValue)`

* Ciąg: Ciąg, aby zastąpić wartości w.
* OldValue: Ciąg do wyszukania oraz do zastąpienia.
* NewValue: Ciąg do zastąpienia w.

**Uwagi:**  
Funkcja rozpoznaje następujących monikerów specjalne:

* \n — nowy wiersz
* \r — powrót karetki
* \t — karta

**Przykład:**  
`Replace([address],"\r\n",", ")`  
Zamienia CRLF przecinek i spacja i może prowadzić do "Jeden Microsoft sposób, Redmond, WA, USA"

- - -
### <a name="replacechars"></a>ReplaceChars
**Opis:**  
Funkcja ReplaceChars zamienia wszystkie wystąpienia znaków znalezionych w ciągu ReplacePattern.

**Składnia:**  
`str ReplaceChars(str string, str ReplacePattern)`

* Ciąg: Ciąg do zastąpienia znaków.
* ReplacePattern: ciąg zawierający słownika przy użyciu znaków do zastąpienia.

Format to {źródło1}: {nazwach target1}, {źródło2}: {target2}, {źródłoN}, {targetN} której źródłem jest znak do znalezienia i target ciąg do zamiany.

**Uwagi:**

* Funkcja przyjmuje każdego wystąpienia określonych źródeł i zastępuje je z elementami docelowymi.
* Źródło musi być dokładnie jeden znak (unicode).
* Źródło nie może być pusta ani dłuższa niż jeden znak (Błąd analizy).
* Element docelowy może mieć wielu znaków, na przykład ö:oe, β:ss.
* Element docelowy może być pusta, wskazujący, że znak powinien zostać usunięty.
* Źródło jest uwzględniana wielkość liter i musi być dokładnym dopasowaniem.
* (Przecinkami) i: (dwukropek) są zastrzeżone znaki i nie można zastąpić za pomocą tej funkcji.
* Miejsca do magazynowania i inne białe znaki w ciągu ReplacePattern są ignorowane.

**Przykład:**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
Zwraca Raksmorgas

`ReplaceChars("O’Neil",%ReplaceString%)`  
Zwraca "ONeil" jeden znacznik jest zdefiniowana ma zostać usunięty.

- - -
### <a name="right"></a>Prawe
**Opis:**  
Right — funkcja zwraca określoną liczbę znaków z prawej strony (Zakończ) w ciągu.

**Składnia:**  
`str Right(str string, num NumChars)`

* ciąg: ciąg do zwrócenia znaków z
* NumChars: numer identyfikujący liczba znaków do zwrócenia z (po prawej) końca ciągu

**Uwagi:**  
Od ostatniej pozycji w ciągu są zwracane NumChars znaki.

Ciąg zawierający ostatnie znaki numChars w ciągu:

* Jeśli numChars = 0, zwraca pusty ciąg.
* Jeśli numChars < 0, zwraca ciąg wejściowy.
* Jeśli ciąg ma wartość null, zwraca pusty ciąg.

Jeśli ciąg zawiera mniej znaków niż liczba NumChars określonych w, zostanie zwrócony ciąg jest taka sama jak ciąg.

**Przykład:**  
`Right("John Doe", 3)`  
Zwraca wartość "Doe".

- - -
### <a name="rtrim"></a>Przytk
**Opis:**  
Funkcja RTrim usuwa spacje końcowe z ciągu.

**Składnia:**  
`str RTrim(str value)`

**Przykład:**  
`RTrim(" Test ")`  
Zwraca wartość "Test".

- - -
### <a name="select"></a>Wybierz
**Opis:**  
Wszystkie wartości w atrybutu wielowartościowego (lub danych wyjściowych wyrażenia) na podstawie funkcji określony proces.

**Składnia:**  
`mvattr Select(variable item, mvattr attribute, func function)`  
`mvattr Select(variable item, exp expression, func function)`

* element: Reprezentuje element atrybutu wielowartościowego
* Atrybut: atrybutu wielowartościowego
* wyrażenie: wyrażenie, które zwraca kolekcję wartości
* warunek: dowolnej funkcji, która pozwala na przetwarzanie elementu w atrybucie

**Przykłady:**  
`Select($item,[otherPhone],Replace($item,"-",""))`  
Zwraca wszystkie wartości w faksów atrybutu wielowartościowego, po usunięciu łączniki (-).

- - -
### <a name="split"></a>Podziel
**Opis:**  
Funkcja Split ciąg znaków oddzielonych ogranicznik i sprawia, że parametry wielowartościowe.

**Składnia:**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

* wartość: ciąg znakiem ogranicznika do oddzielania.
* Ogranicznik: pojedynczy znak ma być używany jako ogranicznika.
* limit: Maksymalna liczba wartości, które mogą zwracać.

**Przykład:**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
Zwraca ciąg wielokrotne z 2 elementami jest przydatne w przypadku atrybutem proxyAddress.

- - -
### <a name="stringfromguid"></a>StringFromGuid
**Opis:**  
Funkcja StringFromGuid trwa binarne identyfikator GUID i konwertuje ją na ciąg

**Składnia:**  
`str StringFromGuid(bin GUID)`

- - -
### <a name="stringfromsid"></a>StringFromSid
**Opis:**  
Funkcja StringFromSid konwertuje tablicę bajtów zawierającą identyfikator zabezpieczeń na ciąg.

**Składnia:**  
`str StringFromSid(bin ObjectSID)`  

- - -
### <a name="switch"></a>Przełącznik
**Opis:**  
Funkcja przełącznik jest używana do zwracać pojedynczą wartość, w oparciu o ocenionych warunków.

**Składnia:**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

* expr: Wyrażenie typu Variant, które ma zostać oceniona.
* value: Wartość do zwrócenia, jeśli odpowiednie wyrażenie ma wartość True.

**Uwagi:**  
Na liście argumentów funkcji przełącznika składa się z par wartości i wyrażeń. Wyrażenia są przetwarzane od lewej do prawej, a wartość skojarzoną z pierwsze wyrażenie, aby zwrócić wartość True jest zwracana. Jeśli elementy nie są prawidłowo sparowane, wystąpi błąd czasu wykonywania.

Na przykład jeśli Wyr1 ma wartość True, przełącznik zwraca wartość1. Jeśli wyrażenie-1 ma wartość FAŁSZ, ale wyrażenie-2 ma wartość True, przełącznik zwraca wartość 2 i tak dalej.

Przełącznik zwraca wartość Nothing, jeśli:

* Żadne wyrażenie ma wartość True.
* Wartość True, pierwsze wyrażenie ma odpowiadająca wartość, która ma wartość Null.

Przełącznik ocenia wszystkie wyrażenia, mimo że zwracany jest tylko jeden z nich. Z tego powodu należy uważać na niepożądane skutki uboczne. Na przykład jeśli oceny dowolne wyrażenie, w wyniku dzielenie przez zero, wystąpi błąd.

Wartość może być również funkcję błędu, co zwróciłoby niestandardowy ciąg.

**Przykład:**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
Zwraca język używany w niektórych głównych miast, w przeciwnym razie zwraca błąd.

- - -
### <a name="trim"></a>TRIM
**Opis:**  
Funkcja przycinania usuwa wiodące i końcowe białe znaki z ciągu.

**Składnia:**  
`str Trim(str value)`  

**Przykład:**  
`Trim(" Test ")`  
Zwraca wartość "Test".

`Trim([proxyAddresses])`  
Usuwa spacje dla każdej wartości w atrybucie proxyAddress początkowe i końcowe.

- - -
### <a name="ucase"></a>UCase
**Opis:**  
Funkcja UCase konwertuje wszystkie znaki w ciągu na wielkie litery.

**Składnia:**  
`str UCase(str string)`

**Przykład:**  
`UCase("TeSt")`  
Zwraca "TEST".

- - -
### <a name="where"></a>Lokalizacja

**Opis:**  
Zwraca podzestaw elementów wartości z atrybutu wielowartościowego (lub danych wyjściowych wyrażenia) na podstawie określonego warunku.

**Składnia:**  
`mvattr Where(variable item, mvattr attribute, exp condition)`  
`mvattr Where(variable item, exp expression, exp condition)`  
* element: Reprezentuje element atrybutu wielowartościowego
* Atrybut: atrybutu wielowartościowego
* warunek: dowolne wyrażenie, które mogą być obliczane na wartość true lub false
* wyrażenie: wyrażenie, które zwraca kolekcję wartości

**Przykład:**  
`Where($item,[userCertificate],CertNotAfter($item)>Now())`  
Zwraca wartości certyfikatu w userCertificate atrybutu wielowartościowego, która nie wygasła.

- - -
### <a name="with"></a>Zawiera
**Opis:**  
Funkcja With udostępnia uproszczenie złożone wyrażenie, przy użyciu zmiennej do reprezentowania Podwyrażenie, który pojawia się jeden lub więcej razy w złożonych wyrażeń.

**Składnia:** 
`With(var variable, exp subExpression, exp complexExpression)`  
* Zmienna: Reprezentuje Podwyrażenie.
* Podwyrażenie: Podwyrażenie reprezentowany przez zmienną.
* complexExpression: Wyrażenie złożone.

**Przykład:**  
`With($unExpiredCerts,Where($item,[userCertificate],CertNotAfter($item)>Now()),IIF(Count($unExpiredCerts)>0,$unExpiredCerts,NULL))`  
Jest funkcjonalnie równoważne:  
`IIF (Count(Where($item,[userCertificate],CertNotAfter($item)>Now()))>0, Where($item,[userCertificate],CertNotAfter($item)>Now()),NULL)`  
W atrybucie certyfikatu użytkownika, która zwraca tylko wartości niewygasłe certyfikatu.


- - -
### <a name="word"></a>Word
**Opis:**  
Funkcja programu Word zwraca wyrazu w ciągu, w oparciu o parametry opisujące ograniczników do używany wraz z numerem programu word do zwrócenia.

**Składnia:**  
`str Word(str string, num WordNumber, str delimiters)`

* ciąg: ciąg do zwrócenia słowa.
* WordNumber: numer, który wyraz numer identyfikacyjny powinna zostać zwrócona.
* ograniczniki: ciąg reprezentujący ograniczniki, które mają być używane do identyfikowania słów

**Uwagi:**  
Każdy ciąg znaków w ciągu, rozdzielone przez jeden ze znaków w ograniczniki są identyfikowane jako słowa:

* Jeśli liczba < 1, zwraca pusty ciąg.
* Jeśli ciąg ma wartość null, zwraca pusty ciąg.

Jeśli ciąg zawiera mniej niż liczba słów lub ciąg nie zawiera żadnych słów identyfikowane przez ograniczników, zwracany jest pusty ciąg.

**Przykład:**  
`Word("The quick brown fox",3," ")`  
Zwraca "brown"

`Word("This,string!has&many separators",3,",!&#")`  
Zwraca "jest"

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Opis wyrażeń związanych z Aprowizacją deklaratywną](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Synchronizacja programu Azure AD Connect: Dostosowywanie opcji synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
