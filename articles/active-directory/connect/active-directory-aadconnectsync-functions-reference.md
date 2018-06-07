---
title: 'Synchronizacja programu Azure AD Connect: odwołanie do funkcji | Dokumentacja firmy Microsoft'
description: Odwołanie deklaratywne wyrażenia inicjowania obsługi administracyjnej w synchronizacja programu Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 4f525ca0-be0e-4a2e-8da1-09b6b567ed5f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 4814d53a86b0d90cf16f76e75c7044448cf791eb
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34595159"
---
# <a name="azure-ad-connect-sync-functions-reference"></a>Synchronizacja programu Azure AD Connect: odwołanie do funkcji
W programie Azure AD Connect funkcje są używane do modyfikowania wartości atrybutu podczas synchronizacji.  
Składnia funkcji jest wyrażona w następującym formacie:  
`<output type> FunctionName(<input type> <position name>, ..)`

Funkcji jest przeciążony i przyjmuje wiele składni, wyświetlane są wszystkie prawidłowe składni.  
Funkcje są silnie typizowane i sprawdź, czy typ przekazany dopasowań udokumentowanym typem.  
Jeśli typ nie jest zgodny, jest zgłaszany błąd.

Typy są wyrażane przy użyciu następującej składni:

* **bin** — binarne
* **wartość logiczna** — wartość logiczna
* **DT** — UTC daty/godziny
* **wyliczenia** — wyliczenie znanych — stałe
* **EXP** — wyrażenie, które powinien zwrócić wartość logiczna
* **mvbin** — binarnego z wieloma wartościami
* **mvstr** — ciąg z wieloma wartościami
* **mvref** — odwołanie z wieloma wartościami
* **num** — numeryczne
* **REF** — odwołanie
* **str** — ciąg
* **var** — wariant (prawie) żadnego innego typu
* **void** — nie zwraca wartości

Funkcje z typami **mvbin**, **mvstr**, i **mvref** może pracować tylko na atrybuty wielowartościowe. Funkcje z **bin**, **str**, i **ref** pracować nad atrybuty zarówno jedno- i wielowartościowych.

## <a name="functions-reference"></a>Informacje ogólne o funkcjach
| Lista funkcji |  |  |  |  |
| --- | --- | --- | --- | --- | --- |
| **Certyfikat** | | | | |
| [CertExtensionOids](#certextensionoids) |[CertFormat](#certformat) |[CertFriendlyName](#certfriendlyname) |[CertHashString](#certhashstring) | |
| [CertIssuer](#certissuer) |[CertIssuerDN](#certissuerdn) |[CertIssuerOid](#certissueroid) |[CertKeyAlgorithm](#certkeyalgorithm) | |
| [CertKeyAlgorithmParams](#certkeyalgorithmparams) |[CertNameInfo](#certnameinfo) |[CertNotAfter](#certnotafter) |[CertNotBefore](#certnotbefore) | |
| [CertPublicKeyOid](#certpublickeyoid) |[CertPublicKeyParametersOid](#certpublickeyparametersoid) |[CertSerialNumber](#certserialnumber) |[CertSignatureAlgorithmOid](#certsignaturealgorithmoid) | |
| [CertSubject](#certsubject) |[CertSubjectNameDN](#certsubjectnamedn) |[CertSubjectNameOid](#certsubjectnameoid) |[certThumbprint](#certthumbprint) | |
[ CertVersion](#certversion) |[IsCert](#iscert) | | | |
| **Konwersja** | | | | |
| [CBool](#cbool) |[CDate](#cdate) |[CGuid](#cguid) |[ConvertFromBase64](#convertfrombase64) | |
| [ConvertToBase64](#converttobase64) |[ConvertFromUTF8Hex](#convertfromutf8hex) |[ConvertToUTF8Hex](#converttoutf8hex) |[CNum](#cnum) | |
| [CRef](#cref) |[CStr](#cstr) |[StringFromGuid](#StringFromGuid) |[StringFromSid](#stringfromsid) | |
| **Data i godzina** | | | | |
| [DateAdd](#dateadd) |[DateFromNum](#datefromnum) |[FormatDateTime](#formatdatetime) |[Teraz](#now) | |
| [NumFromDate](#numfromdate) | | | | |
| **Katalog** | | | | |
| [DNComponent](#dncomponent) |[DNComponentRev](#dncomponentrev) |[EscapeDNComponent](#escapedncomponent) | | |
| **Ocena** | | | | |
| [IsBitSet](#isbitset) |[IsDate](#isdate) |[IsEmpty](#isempty) |[IsGuid](#isguid) | |
| [IsNull](#isnull) |[IsNullOrEmpty](#isnullorempty) |[IsNumeric](#isnumeric) |[IsPresent](#ispresent) | |
| [IsString](#isstring) | | | | |
| **Matematyczne** | | | | |
| [BitAnd](#bitand) |[BitOr](#bitor) |[RandomNum](#randomnum) | | |
| **Wielowartościowe** | | | | |
| [zawiera](#contains) |[Liczba](#count) |[Element](#item) |[ItemOrNull](#itemornull) | |
| [Dołącz](#join) |[Removeduplicates —](#removeduplicates) |[Podziel](#split) | | |
| **Przepływ programu** | | | | |
| [Error](#error) |[IIF](#iif) |[Wybierz](#select) |[Przełącznik](#switch) | |
| [Where](#where) |[With](#with) | | | |
| **Tekst** | | | | |
| [IDENTYFIKATOR GUID](#guid) |[InStr](#instr) |[InStrRev](#instrrev) |[LCase](#lcase) | |
| [Po lewej](#left) |[Długość](#len) |[Przytp](#ltrim) |[Mid](#mid) | |
| [PadLeft](#padleft) |[PadRight](#padright) |[PCase](#pcase) |[Zamień](#replace) | |
| [ReplaceChars](#replacechars) |[Prawo](#right) |[Przytk](#rtrim) |[TRIM](#trim) | |
| [UCase](#ucase) |[Word](#word) | | | |

- - -
### <a name="bitand"></a>BitAnd
**Opis:**  
Funkcja BitAnd ustawia określonym usługi bits na wartość.

**Składnia:**  
`num BitAnd(num value1, num value2)`

* Wartość1, wartość2: wartości liczbowe, które powinny być tematyczne razem

**Uwagi:**  
Ta funkcja konwertuje oba parametry reprezentacja binarna i ustawia bit:

* 0 — Jeśli jeden lub oba odpowiednich bitów w *maski* i *flagi* 0
* 1 — Jeśli odpowiednich bitów są 1.

Innymi słowy zwraca 0 we wszystkich przypadkach, z wyjątkiem przypadków, gdy odpowiednich bitów oba parametry są 1.

**Przykład:**  
`BitAnd(&HF, &HF7)`  
Zwraca 7, ponieważ ta wartość szesnastkową "F" i "F7".

- - -
### <a name="bitor"></a>BitOr
**Opis:**  
Funkcja BitOr ustawia określonym usługi bits na wartość.

**Składnia:**  
`num BitOr(num value1, num value2)`

* Wartość1, wartość2: wartości liczbowe, które powinny być zsumować logicznie razem

**Uwagi:**  
Ta funkcja konwertuje oba parametry reprezentacja binarna i ustawia bit 1, jeśli jeden lub oba odpowiednich bitów w maski i flagi są 1 i 0, gdy oba odpowiednich bitów są 0. Innymi słowy zwraca 1 we wszystkich przypadkach, z wyjątkiem przypadków, w którym odpowiednich bitów oba parametry są równe 0.

- - -
### <a name="cbool"></a>CBool
**Opis:**  
CBool-funkcja zwraca wartość logiczną na podstawie na obliczane wyrażenie

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
CDate-funkcja zwraca wartość typu DateTime UTC z ciągu. Data i godzina nie jest typem atrybutu natywnego zsynchronizowane, ale jest używany przez niektóre funkcje.

**Składnia:**  
`dt CDate(str value)`

* Wartość: Ciąg daty, godziny i opcjonalnie strefy czasowej

**Uwagi:**  
Zwrócony ciąg jest zawsze w formacie UTC.

**Przykład:**  
`CDate([employeeStartTime])`  
Zwraca wartość typu DateTime na jego podstawie godzina rozpoczęcia

`CDate("2013-01-10 4:00 PM -8")`  
Zwraca daty/godziny reprezentująca "2013-01-11: 00:00:00"


- - -
### <a name="certextensionoids"></a>CertExtensionOids
**Opis:**  
Zwraca wartości identyfikatora Oid rozszerzenia krytyczne obiektu certyfikatu.

**Składnia:**  
`mvstr CertExtensionOids(binary certificateRawData)`  
*   certificateRawData: certyfikat X.509 reprezentację tablicy bajtów. Tablica bajtów może być zakodowane pliku binarnego (DER) lub danych X.509 z kodowaniem Base64.

- - -
### <a name="certformat"></a>CertFormat
**Opis:**  
Zwraca nazwę formatu tego certyfikatu X.509v3.

**Składnia:**  
`str CertFormat(binary certificateRawData)`  
*   certificateRawData: certyfikat X.509 reprezentację tablicy bajtów. Tablica bajtów może być zakodowane pliku binarnego (DER) lub danych X.509 z kodowaniem Base64.

- - -
### <a name="certfriendlyname"></a>CertFriendlyName
**Opis:**  
Zwraca skojarzonego alias dla certyfikatu.

**Składnia:**  
`str CertFriendlyName(binary certificateRawData)`  
*   certificateRawData: certyfikat X.509 reprezentację tablicy bajtów. Tablica bajtów może być zakodowane pliku binarnego (DER) lub danych X.509 z kodowaniem Base64.

- - -
### <a name="certhashstring"></a>CertHashString
**Opis:**  
Zwraca wartość skrótu SHA1 certyfikatu X.509v3 jako ciąg szesnastkowy.

**Składnia:**  
`str CertHashString(binary certificateRawData)`  
*   certificateRawData: certyfikat X.509 reprezentację tablicy bajtów. Tablica bajtów może być zakodowane pliku binarnego (DER) lub danych X.509 z kodowaniem Base64.

- - -
### <a name="certissuer"></a>CertIssuer
**Opis:**  
Zwraca nazwę urzędu certyfikacji, który wystawił certyfikat X.509v3.

**Składnia:**  
`str CertIssuer(binary certificateRawData)`  
*   certificateRawData: certyfikat X.509 reprezentację tablicy bajtów. Tablica bajtów może być zakodowane pliku binarnego (DER) lub danych X.509 z kodowaniem Base64.

- - -
### <a name="certissuerdn"></a>CertIssuerDN
**Opis:**  
Zwraca nazwa wyróżniająca wystawcy certyfikatu.

**Składnia:**  
`str CertIssuerDN(binary certificateRawData)`  
*   certificateRawData: certyfikat X.509 reprezentację tablicy bajtów. Tablica bajtów może być zakodowane pliku binarnego (DER) lub danych X.509 z kodowaniem Base64.

- - -
### <a name="certissueroid"></a>CertIssuerOid
**Opis:**  
Zwraca identyfikator Oid wystawcy certyfikatu.

**Składnia:**  
`str CertIssuerOid(binary certificateRawData)`  
*   certificateRawData: certyfikat X.509 reprezentację tablicy bajtów. Tablica bajtów może być zakodowane pliku binarnego (DER) lub danych X.509 z kodowaniem Base64.

- - -
### <a name="certkeyalgorithm"></a>CertKeyAlgorithm
**Opis:**  
Zwraca informacje algorytm klucza dla tego certyfikatu X.509v3 jako ciąg.

**Składnia:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: certyfikat X.509 reprezentację tablicy bajtów. Tablica bajtów może być zakodowane pliku binarnego (DER) lub danych X.509 z kodowaniem Base64.

- - -
### <a name="certkeyalgorithmparams"></a>CertKeyAlgorithmParams
**Opis:**  
Zwraca parametry algorytm klucza certyfikatu X.509v3 jako ciąg szesnastkowy.

**Składnia:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: certyfikat X.509 reprezentację tablicy bajtów. Tablica bajtów może być zakodowane pliku binarnego (DER) lub danych X.509 z kodowaniem Base64.

- - -
### <a name="certnameinfo"></a>CertNameInfo
**Opis:**  
Zwraca podmiot i Wystawca nazwy z certyfikatu.

**Składnia:**  
`str CertNameInfo(binary certificateRawData, str x509NameType, bool includesIssuerName)`  
*   certificateRawData: certyfikat X.509 reprezentację tablicy bajtów. Tablica bajtów może być zakodowane pliku binarnego (DER) lub danych X.509 z kodowaniem Base64.
*   X509NameType: Wartość X509NameType tematu.
*   includesIssuerName: wartość true, aby uwzględnić nazwę wystawcy; w przeciwnym razie wartość false.

- - -
### <a name="certnotafter"></a>CertNotAfter
**Opis:**  
Zwraca datę według czasu lokalnego, po której certyfikat nie jest już prawidłowy.

**Składnia:**  
`dt CertNotAfter(binary certificateRawData)`  
*   certificateRawData: certyfikat X.509 reprezentację tablicy bajtów. Tablica bajtów może być zakodowane pliku binarnego (DER) lub danych X.509 z kodowaniem Base64.

- - -
### <a name="certnotbefore"></a>CertNotBefore
**Opis:**  
Zwraca datę według czasu lokalnego, od której zaczyna obowiązywać certyfikat.

**Składnia:**  
`dt CertNotBefore(binary certificateRawData)`  
*   certificateRawData: certyfikat X.509 reprezentację tablicy bajtów. Tablica bajtów może być zakodowane pliku binarnego (DER) lub danych X.509 z kodowaniem Base64.

- - -
### <a name="certpublickeyoid"></a>CertPublicKeyOid
**Opis:**  
Zwraca identyfikator Oid klucza publicznego certyfikatu X.509v3.

**Składnia:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: certyfikat X.509 reprezentację tablicy bajtów. Tablica bajtów może być zakodowane pliku binarnego (DER) lub danych X.509 z kodowaniem Base64.

- - -
### <a name="certpublickeyparametersoid"></a>CertPublicKeyParametersOid
**Opis:**  
Zwraca identyfikator Oid parametrów klucza publicznego certyfikatu X.509v3.

**Składnia:**  
`str CertPublicKeyParametersOid(binary certificateRawData)`  
*   certificateRawData: certyfikat X.509 reprezentację tablicy bajtów. Tablica bajtów może być zakodowane pliku binarnego (DER) lub danych X.509 z kodowaniem Base64.

- - -
### <a name="certserialnumber"></a>CertSerialNumber
**Opis:**  
Zwraca numer seryjny certyfikatu X.509v3.

**Składnia:**  
`str CertSerialNumber(binary certificateRawData)`  
*   certificateRawData: certyfikat X.509 reprezentację tablicy bajtów. Tablica bajtów może być zakodowane pliku binarnego (DER) lub danych X.509 z kodowaniem Base64.

- - -
### <a name="certsignaturealgorithmoid"></a>CertSignatureAlgorithmOid
**Opis:**  
Zwraca identyfikator Oid algorytm używany do tworzenia podpisu certyfikatu.

**Składnia:**  
`str CertSignatureAlgorithmOid(binary certificateRawData)`  
*   certificateRawData: certyfikat X.509 reprezentację tablicy bajtów. Tablica bajtów może być zakodowane pliku binarnego (DER) lub danych X.509 z kodowaniem Base64.

- - -
### <a name="certsubject"></a>CertSubject
**Opis:**  
Pobiera nazwa wyróżniająca podmiotu z certyfikatu.

**Składnia:**  
`str CertSubject(binary certificateRawData)`  
*   certificateRawData: certyfikat X.509 reprezentację tablicy bajtów. Tablica bajtów może być zakodowane pliku binarnego (DER) lub danych X.509 z kodowaniem Base64.

- - -
### <a name="certsubjectnamedn"></a>CertSubjectNameDN
**Opis:**  
Zwraca nazwa wyróżniająca podmiotu z certyfikatu.

**Składnia:**  
`str CertSubjectNameDN(binary certificateRawData)`  
*   certificateRawData: certyfikat X.509 reprezentację tablicy bajtów. Tablica bajtów może być zakodowane pliku binarnego (DER) lub danych X.509 z kodowaniem Base64.

- - -
### <a name="certsubjectnameoid"></a>CertSubjectNameOid
**Opis:**  
Zwraca identyfikator Oid nazwy podmiotu z certyfikatu.

**Składnia:**  
`str CertSubjectNameOid(binary certificateRawData)`  
*   certificateRawData: certyfikat X.509 reprezentację tablicy bajtów. Tablica bajtów może być zakodowane pliku binarnego (DER) lub danych X.509 z kodowaniem Base64.

- - -
### <a name="certthumbprint"></a>certThumbprint
**Opis:**  
Zwraca odcisk palca certyfikatu.

**Składnia:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: certyfikat X.509 reprezentację tablicy bajtów. Tablica bajtów może być zakodowane pliku binarnego (DER) lub danych X.509 z kodowaniem Base64.

- - -
### <a name="certversion"></a>CertVersion
**Opis:**  
Zwraca wersja formatu X.509 certyfikatu.

**Składnia:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: certyfikat X.509 reprezentację tablicy bajtów. Tablica bajtów może być zakodowane pliku binarnego (DER) lub danych X.509 z kodowaniem Base64.

- - -
### <a name="cguid"></a>CGuid
**Opis:**  
Funkcja CGuid konwertuje jego reprezentacja binarna reprezentacja ciągu identyfikatora GUID.

**Składnia:**  
`bin CGuid(str GUID)`

* Ciąg sformatowany w tym wzorcu: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx lub {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

- - -
### <a name="contains"></a>Contains
**Opis:**  
Funkcja zawiera wyszukuje ciąg wewnątrz atrybutu wielowartościowego

**Składnia:**  
`num Contains (mvstring attribute, str search)` -uwzględniana wielkość liter  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)` -uwzględniana wielkość liter

* Atrybut: atrybutu wielowartościowego do wyszukiwania.
* wyszukiwania: ciąg można znaleźć w atrybucie.
* Casetype: CaseInsensitive lub CaseSensitive w tabeli.

Zwraca indeks atrybutów wielowartościowych, w którym zostało znalezione ciąg. 0 jest zwracany, jeśli nie zostanie znaleziony ciąg.

**Uwagi:**  
Dla atrybutów wielowartościowych ciąg wyszukiwania wyszukuje podciągów w wartości.  
Dla atrybutów odwołania przeszukane ciąg musi dokładnie odpowiadać wartości być uznawane za zgodne.

**Przykład:**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
Jeśli atrybut proxyAddresses ma podstawowego adresu e-mail (wskazywanym przez wielkimi literami "SMTP:"), zwracany jest ten atrybut proxyAddress, przeciwnym razie zwraca błąd.

- - -
### <a name="convertfrombase64"></a>ConvertFromBase64
**Opis:**  
Funkcja ConvertFromBase64 konwertuje wartość określonego kodowanie base64 na regularne ciągu.

**Składnia:**  
`str ConvertFromBase64(str source)` -zakłada do kodowania Unicode  
`str ConvertFromBase64(str source, enum Encoding)`

* Źródło: ciąg kodowany w formacie Base64  
* Kodowanie: UTF8 Unicode i ASCII,

**Przykład**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Zwraca zarówno przykłady "*Witaj świecie!*"

- - -
### <a name="convertfromutf8hex"></a>ConvertFromUTF8Hex
**Opis:**  
Funkcja ConvertFromUTF8Hex konwertuje określoną wartość zakodowany szesnastkowo UTF8 na ciąg.

**Składnia:**  
`str ConvertFromUTF8Hex(str source)`

* Źródło: ciągu zakodowanego 2-bajtowych UTF8

**Uwagi:**  
Różnica między tej funkcji i ConvertFromBase64([],UTF8) że wynik jest przyjazna nazwa Wyróżniająca atrybutu.  
Ten format jest używany przez usługę Azure Active Directory jako nazwa Wyróżniająca.

**Przykład:**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
Zwraca "*Witaj świecie!*"

- - -
### <a name="converttobase64"></a>ConvertToBase64
**Opis:**  
Funkcja ConvertToBase64 konwertuje ciąg na ciąg Unicode w formacie base64.  
Konwertuje wartość tablicy liczb całkowitych do reprezentacji ciągu równoważne, który jest algorytmem base-64 cyfr.

**Składnia:**  
`str ConvertToBase64(str source)`

**Przykład:**  
`ConvertToBase64("Hello world!")`  
Zwraca "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

- - -
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Opis:**  
Funkcja ConvertToUTF8Hex konwertuje ciąg na wartość zakodowany szesnastkowo UTF8.

**Składnia:**  
`str ConvertToUTF8Hex(str source)`

**Uwagi:**  
Format danych wyjściowych tej funkcji jest używany przez usługę Azure Active Directory jako nazwa Wyróżniająca atrybutu format.

**Przykład:**  
`ConvertToUTF8Hex("Hello world!")`  
Zwraca 48656C6C6F20776F726C6421

- - -
### <a name="count"></a>Licznik
**Opis:**  
Funkcja Count zwraca liczbę elementów w atrybutu wielowartościowego

**Składnia:**  
`num Count(mvstr attribute)`

- - -
### <a name="cnum"></a>CNum
**Opis:**  
Funkcja CNum ciąg znaków i zwraca dane typu liczbowego.

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
Konwertuje funkcję CStr dla typu danych string.

**Składnia:**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* wartość: może być liczbą, atrybut odwołania lub typu Boolean.

**Przykład:**  
`CStr([dn])`  
Może zwrócić "cn = Jan, dc = contoso, dc = com"

- - -
### <a name="dateadd"></a>DateAdd
**Opis:**  
Zwraca wartość typu Date zawierającą datę, do której dodano określony interwał.

**Składnia:**  
`dt DateAdd(str interval, num value, dt date)`

* Interwał: String wyrażenie przedział czasu, które chcesz dodać. Ciąg musi mieć jedną z następujących wartości:
  * rrrr roku
  * q kwartału
  * m miesiąc
  * y dzień roku
  * d dzień
  * w dzień tygodnia
  * TT tygodnia
  * h Godzina
  * n minutę
  * s drugi
* wartość: liczba jednostek, które chcesz dodać. Może być liczbą dodatnią (Aby uzyskać daty w przyszłości) lub ujemną (Aby uzyskać daty w przeszłości).
* Data: daty/godziny reprezentująca datę, do którego jest dodawany interwał.

**Przykład:**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
Dodaje 3 miesiące i zwraca wartość typu DateTime reprezentujący "2001-04-01".

- - -
### <a name="datefromnum"></a>DateFromNum
**Opis:**  
Konwertuje funkcję DateFromNum wartość daty AD w formacie do typu Data/Godzina.

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

* Nazwa wyróżniająca: atrybut odwołania, aby zinterpretować
* ComponentNumber: Składnik DN do zwrócenia

**Przykład:**  
`DNComponent(CRef([dn]),1)`  
Jeśli nazwa wyróżniająca "cn = Jan, ou =..." zwraca Jan

- - -
### <a name="dncomponentrev"></a>DNComponentRev
**Opis:**  
Funkcja DNComponentRev zwraca wartość określonego składnika DN z prawej strony (Zakończ).

**Składnia:**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

* Nazwa wyróżniająca: atrybut odwołania, aby zinterpretować
* ComponentNumber - składnik w DN do zwrócenia
* Opcje: Kontroler domeny — Ignoruj wszystkie składniki z "dc ="

**Przykład:**  
Jeśli nazwa wyróżniająca "cn Jan, ou = (Atlanta), ou = GA, ou = = US, dc = contoso, dc = com" następnie  
`DNComponentRev(CRef([dn]),3)`  
`DNComponentRev(CRef([dn]),1,"DC")`  
Obie zwracają NAS.

- - -
### <a name="error"></a>Błąd
**Opis:**  
Funkcja błąd służy do zwracania błędów niestandardowych.

**Składnia:**  
`void Error(str ErrorMessage)`

**Przykład:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Jeśli atrybut accountName nie jest obecny, zgłoś błąd w obiekcie.

- - -
### <a name="escapedncomponent"></a>EscapeDNComponent
**Opis:**  
Funkcja EscapeDNComponent przyjmuje jeden składnik DN i jego specjalne, dlatego może być reprezentowany w protokole LDAP.

**Składnia:**  
`str EscapeDNComponent(str value)`

**Przykład:**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
Zapewnia, że można można utworzyć obiektu w katalogu LDAP, nawet wtedy, gdy atrybut displayName zawiera znaki, które należy użyć znaków ucieczki w protokole LDAP.

- - -
### <a name="formatdatetime"></a>FormatDateTime
**Opis:**  
Funkcja FormatDateTime jest używany do formatowania wartości daty i godziny do ciągu w określonym formacie

**Składnia:**  
`str FormatDateTime(dt value, str format)`

* wartość: wartości w formacie daty/godziny
* Format: ciąg reprezentujący można przekonwertować na format.

**Uwagi:**  
Możliwe wartości dla formatu można znaleźć tutaj: [zdefiniowane przez użytkownika (funkcji Format) formaty daty i godziny](http://msdn2.microsoft.com/library/73ctwf33\(VS.90\).aspx)

**Przykład:**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-mm-dd")`  
Wynikiem "2007-12-25".

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
Może spowodować "20140905081453.0Z"

- - -
### <a name="guid"></a>Identyfikator GUID
**Opis:**  
Funkcja Guid generuje losowe nowego identyfikatora GUID

**Składnia:**  
`str Guid()`

- - -
### <a name="iif"></a>IIF
**Opis:**  
Funkcja IIF zwraca jeden zestaw możliwych wartości na podstawie określonego warunku.

**Składnia:**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* warunek: dowolna wartość lub wyrażenie, które może przyjąć wartość true lub false.
* Wartość_dla_prawdy: Jeśli warunek zwraca wartość true, zwracana wartość.
* Wartość_dla_fałszu: Jeśli warunek nie ma wartość false, zwracana wartość.

**Przykład:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Jeśli użytkownik jest intern, zwraca alias użytkownika z "t-" dodaną na początku jej inny alias użytkownika, ponieważ jest.

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
* Rozpocznij: uruchamianie stanie Znajdź podciąg
* porównywanie: vbTextCompare lub vbBinaryCompare

**Uwagi:**  
Zwraca pozycję, jeżeli został znaleziony podciąg lub 0, jeśli nie można odnaleźć.

**Przykład:**  
`InStr("The quick brown fox","quick")`  
Evalues 5

`InStr("repEated","e",3,vbBinaryCompare)`  
Oblicza 7

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
* Rozpocznij: uruchamianie stanie Znajdź podciąg
* porównywanie: vbTextCompare lub vbBinaryCompare

**Uwagi:**  
Zwraca pozycję, jeżeli został znaleziony podciąg lub 0, jeśli nie można odnaleźć.

**Przykład:**  
`InStrRev("abbcdbbbef","bb")`  
Zwraca 7

- - -
### <a name="isbitset"></a>IsBitSet
**Opis:**  
Funkcja IsBitSet testów, jeśli jest to nieco jest ustawiony lub nie

**Składnia:**  
`bool IsBitSet(num value, num flag)`

* wartość: wartość liczbową evaluated.flag: wartość liczbową bit ma zostać obliczone

**Przykład:**  
`IsBitSet(&HF,4)`  
Zwraca wartość True, ponieważ ustawiono bit "4" w wartości szesnastkowej "F"

- - -
### <a name="isdate"></a>IsDate
**Opis:**  
Jeśli wyrażenie może być oblicza jako typu DateTime, a następnie funkcja IsDate ma wartość True.

**Składnia:**  
`bool IsDate(var Expression)`

**Uwagi:**  
Używany do określenia, czy CDate() mogą być skutecznie.

- - -
### <a name="iscert"></a>IsCert
**Opis:**  
Zwraca wartość PRAWDA, jeśli można serializować danych pierwotnych do obiektu certyfikatu .NET X509Certificate2.

**Składnia:**  
`bool CertThumbprint(binary certificateRawData)`  
*   certificateRawData: certyfikat X.509 reprezentację tablicy bajtów. Tablica bajtów może być zakodowane pliku binarnego (DER) lub danych X.509 z kodowaniem Base64.
- - -
### <a name="isempty"></a>IsEmpty
**Opis:**  
Jeśli atrybut znajduje się w CS lub MV, ale zwraca pusty ciąg, następnie funkcja IsEmpty ma wartość True.

**Składnia:**  
`bool IsEmpty(var Expression)`

- - -
### <a name="isguid"></a>IsGuid
**Opis:**  
Jeśli ciąg można przekonwertować na identyfikator GUID, funkcja IsGuid obliczone na wartość true.

**Składnia:**  
`bool IsGuid(str GUID)`

**Uwagi:**  
Identyfikator GUID jest zdefiniowany jako ciąg, po jednej z tych wzorców: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx lub {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

Używany do określenia, czy CGuid() mogą być skutecznie.

**Przykład:**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
Jeśli StrAttribute ma format identyfikatora GUID, zwróć reprezentacja binarna, w przeciwnym razie zwraca wartość Null.

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
Dla atrybutu to może zwrócić wartość True, jeśli ten atrybut nie istnieje lub jest obecny, ale jest pustym ciągiem.  
Odwrotność funkcji ten nosi nazwę IsPresent.

**Przykład:**  
`IsNullOrEmpty([displayName])`  
Zwraca wartość PRAWDA, jeśli ten atrybut nie jest obecny lub jest pustym ciągiem CS lub MV.

- - -
### <a name="isnumeric"></a>IsNumeric
**Opis:**  
Funkcja IsNumeric zwraca wartość logiczną wskazującą, czy wyrażenie może przyjąć jako liczba typu.

**Składnia:**  
`bool IsNumeric(var Expression)`

**Uwagi:**  
Używany do określenia, czy CNum() mogą być skutecznie można przeanalizować wyrażenia.

- - -
### <a name="isstring"></a>IsString
**Opis:**  
Jeśli do typu ciągu można obliczyć wyrażenia, następnie funkcja IsString ma wartość True.

**Składnia:**  
`bool IsString(var expression)`

**Uwagi:**  
Używany do określenia, czy CStr() mogą być skutecznie można przeanalizować wyrażenia.

- - -
### <a name="ispresent"></a>IsPresent
**Opis:**  
Jeśli wyrażenie na ciąg, który nie jest równa Null i nie jest pusta, funkcja IsPresent zwraca wartość true.

**Składnia:**  
`bool IsPresent(var expression)`

**Uwagi:**  
Odwrotność funkcji ten nosi nazwę IsNullOrEmpty.

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
Funkcja Item przydaje się wraz z funkcji zawiera, ponieważ to drugie funkcja zwraca indeks elementu w atrybutów wielowartościowych.

Zwraca błąd, jeśli indeks jest poza zakresem.

**Przykład:**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
Zwraca podstawowego adresu e-mail.

- - -
### <a name="itemornull"></a>ItemOrNull
**Opis:**  
Funkcja ItemOrNull zwraca jeden element z ciągu/atrybutów wielowartościowych.

**Składnia:**  
`var ItemOrNull(mvstr attribute, num index)`

* Atrybut: atrybutu wielowartościowego
* Indeks: indeks elementu w ciągu wielowartościowych.

**Uwagi:**  
Funkcja ItemOrNull przydaje się wraz z funkcji zawiera, ponieważ to drugie funkcja zwraca indeks elementu w atrybutów wielowartościowych.

Jeśli indeks jest poza zakresem, zwraca wartość Null.

- - -
### <a name="join"></a>Join
**Opis:**  
Funkcja sprzężenia wielowartościowe ciąg znaków i zwraca ciąg pojedynczej wartości z określonego separatorem wstawione między każdym z elementów.

**Składnia:**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

* Atrybut: zawierającego ciągi, które ma zostać umieszczony atrybutów wielowartościowych.
* Ogranicznik: dowolny ciąg używany do rozdzielania podciągów w zwracany ciąg. Pominięcie znak spacji ("") jest używany. Jeśli wartość ogranicznika jest ciągiem o zerowej długości ("") lub Nothing, wszystkie elementy na liście są połączone z ogranicznikami nie.

**Uwagi**  
Występuje parzystość między funkcji dołączania i podziału. Funkcja sprzężenia pobiera tablicę ciągów i dołącza je za pomocą ciągu ogranicznik, aby zwrócić pojedynczy ciąg. Funkcja podziału ciąg znaków i oddziela go na ogranicznik, aby zwracało tablicę ciągów. Najważniejsza różnica jest jednak czy sprzężenia można ciągów z dowolnym ciągiem ogranicznik, podziału tylko rozdzielić ciągów za pomocą pojedynczy znak ogranicznika.

**Przykład:**  
`Join([proxyAddresses],",")`  
Może zwrócić: "SMTP:john.doe@contoso.com,smtp:jd@contoso.com"

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
### <a name="left"></a>Do lewej
**Opis:**  
Po lewej stronie funkcja zwraca określoną liczbę znaków z lewej strony ciągu.

**Składnia:**  
`str Left(str string, num NumChars)`

* ciągu: ciąg do zwracania znaków z
* NumChars: Liczba identyfikująca liczbę znaków do zwrócenia z początku ciąg (po lewej)

**Uwagi:**  
Ciąg zawierający pierwszych znaków numChars w ciągu:

* Jeśli numChars = 0, zwraca pusty ciąg.
* Jeśli numChars < 0, zwraca ciąg wejściowy.
* Jeśli ciąg ma wartość null, zwraca pusty ciąg.

Jeśli ciąg zawiera mniej znaków niż liczba numChars określony w, zostanie zwrócony ciąg jest taki sam jak ciąg (tj. zawierający wszystkie znaki w parametrze 1).

**Przykład:**  
`Left("John Doe", 3)`  
Zwraca wartość "Joh".

- - -
### <a name="len"></a>Długość
**Opis:**  
Funkcja Len zwraca liczbę znaków w ciągu.

**Składnia:**  
`num Len(str value)`

**Przykład:**  
`Len("John Doe")`  
Zwraca 8

- - -
### <a name="ltrim"></a>Przytp
**Opis:**  
Funkcja LTrim usuwa spacji wiodących z ciągu.

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

* ciągu: ciąg do zwracania znaków z
* Rozpocznij: Liczba identyfikująca początkowe położenie w ciągu do zwracania znaków z
* NumChars: Liczba identyfikująca liczbę znaków do zwrócenia z pozycji w ciągu

**Uwagi:**  
Uruchom numChars zwracanych znaków, zaczynając od pozycji w ciągu.  
Ciąg zawierający numChars znaków od początku pozycji w ciągu:

* Jeśli numChars = 0, zwraca pusty ciąg.
* Jeśli numChars < 0, zwraca ciąg wejściowy.
* Jeśli start > długość ciągu, zwraca ciąg wejściowy.
* Jeśli start < = 0, zwraca ciąg wejściowy.
* Jeśli ciąg ma wartość null, zwraca pusty ciąg.

Jeśli nie są znaki numChar pozostałych w ciągu od początku pozycji, jako wiele znaków, jak to możliwe są zwracane.

**Przykład:**  
`Mid("John Doe", 3, 5)`  
Zwraca wartość "nie hn".

`Mid("John Doe", 6, 999)`  
Zwraca "Nowak"

- - -
### <a name="now"></a>Teraz
**Opis:**  
Teraz funkcja zwraca wartość typu DateTime Określanie aktualnej daty i godziny, zgodnie z komputera datę i godzinę systemową.

**Składnia:**  
`dt Now()`

- - -
### <a name="numfromdate"></a>NumFromDate
**Opis:**  
Funkcja NumFromDate zwraca datę w formacie daty przez usługi AD.

**Składnia:**  
`num NumFromDate(dt value)`

**Przykład:**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
Zwraca 129699324000000000

- - -
### <a name="padleft"></a>PadLeft
**Opis:**  
PadLeft funkcja lewej podkładki ciąg do określonej długości, przy użyciu znaków podany dopełnienia.

**Składnia:**  
`str PadLeft(str string, num length, str padCharacter)`

* ciągu: ciąg do konsoli.
* długość: liczba całkowita reprezentująca wymagana długość ciągu.
* padCharacter: ciąg składający się z jednego znaku do użycia jako znak konsoli

**Uwagi:**

* Jeśli długość ciągu jest mniejsza niż długość, padCharacter wielokrotnie jest dołączany na początku (po lewej) ciągu do momentu jego długość jest równa długości.
* padCharacter może być znaku spacji, ale nie może mieć wartości null.
* Jeśli długość ciągu jest równa lub większa niż długość, ciąg zostanie zwrócony bez zmian.
* Jeśli ciąg ma długość większą niż lub równa długości, zostanie zwrócony ciąg jest taki sam jak ciąg.
* Jeśli długość ciągu jest mniejsza niż długość, nowy ciąg o długości żądaną zostanie zwrócony ciąg zawierający dopełniane przy padCharacter.
* Jeśli ciąg ma wartość null, funkcja zwraca pusty ciąg.

**Przykład:**  
`PadLeft("User", 10, "0")`  
Zwraca wartość "000000User".

- - -
### <a name="padright"></a>PadRight
**Opis:**  
PadRight funkcja prawo podkładki ciąg do określonej długości, przy użyciu znaków podany dopełnienia.

**Składnia:**  
`str PadRight(str string, num length, str padCharacter)`

* ciągu: ciąg do konsoli.
* długość: liczba całkowita reprezentująca wymagana długość ciągu.
* padCharacter: ciąg składający się z jednego znaku do użycia jako znak konsoli

**Uwagi:**

* Jeśli długość ciągu jest mniejsza niż długość, następnie padCharacter jest wielokrotnie dodane do (po prawej) końca ciągu dopóki nie długości równej długości.
* padCharacter może być znaku spacji, ale nie może mieć wartości null.
* Jeśli długość ciągu jest równa lub większa niż długość, ciąg zostanie zwrócony bez zmian.
* Jeśli ciąg ma długość większą niż lub równa długości, zostanie zwrócony ciąg jest taki sam jak ciąg.
* Jeśli długość ciągu jest mniejsza niż długość, nowy ciąg o długości żądaną zostanie zwrócony ciąg zawierający dopełniane przy padCharacter.
* Jeśli ciąg ma wartość null, funkcja zwraca pusty ciąg.

**Przykład:**  
`PadRight("User", 10, "0")`  
Zwraca wartość "User000000".

- - -
### <a name="pcase"></a>PCase
**Opis:**  
Funkcja PCase konwertuje pierwszy znak każdego wyrazu odstępami w ciągu na wielkie litery, a wszystkie inne znaki są konwertowane na małe litery.

**Składnia:**  
`String PCase(string)`

**Uwagi:**

* Ta funkcja nie jest aktualnie dostępny prawidłowego wielkość liter można przekonwertować słowo, które jest całkowicie wielkie litery, takich jak skrót.

**Przykład:**  
`PCase("TEsT")`  
Zwraca "Test".

`PCase(LCase("TEST"))`  
Zwraca "Test"

- - -
### <a name="randomnum"></a>RandomNum
**Opis:**  
Funkcja RandomNum zwraca liczbę losową określonego interwału.

**Składnia:**  
`num RandomNum(num start, num end)`

* Rozpocznij: Liczba identyfikująca dolny limit losowych wartości do wygenerowania
* końcowy: Liczba identyfikująca górny limit losowych wartości do wygenerowania

**Przykład:**  
`Random(100,999)`  
Może zwrócić 734.

- - -
### <a name="removeduplicates"></a>Removeduplicates —
**Opis:**  
Removeduplicates — funkcja wielowartościowe ciąg znaków i upewnij się, że każda wartość jest unikatowa.

**Składnia:**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Przykład:**  
`RemoveDuplicates([proxyAddresses])`  
Zwraca atrybutu oczyszczony proxyAddress, w której zostały usunięte wszystkie zduplikowane wartości.

- - -
### <a name="replace"></a>Replace
**Opis:**  
Funkcja Replace zamienia wszystkie wystąpienia ciągu na inny ciąg.

**Składnia:**  
`str Replace(str string, str OldValue, str NewValue)`

* ciągu: ciąg, aby zastąpić wartości.
* OldValue: Ciąg do wyszukania oraz do zastąpienia.
* NewValue: Ciąg do zastąpienia w.

**Uwagi:**  
Funkcja rozpoznaje następujących monikerów specjalne:

* \n — nowy wiersz
* \r — powrót karetki
* \t — karta

**Przykład:**  
`Replace([address],"\r\n",", ")`  
Zastępuje CRLF przecinek i spacja i może prowadzić do "Co Microsoft sposób, Redmond, WA, USA"

- - -
### <a name="replacechars"></a>ReplaceChars
**Opis:**  
Funkcja ReplaceChars zamienia wszystkie wystąpienia znaków w ciągu ReplacePattern.

**Składnia:**  
`str ReplaceChars(str string, str ReplacePattern)`

* ciągu: ciąg, aby zastąpić znaków.
* ReplacePattern: ciąg zawierający słownik z znaki do zamiany.

Format to {źródło1}: {nazwach target1}, {źródło2}: {target2}, {źródłoN}, {targetN} gdzie źródła jest znak można znaleźć i docelowego należy zastąpić ciąg.

**Uwagi:**

* Funkcja przyjmuje każdego wystąpienia określonych źródeł i zastępuje je z elementami docelowymi.
* Źródło musi zawierać dokładnie jeden znak (unicode).
* Źródło nie może być pusta ani więcej niż jeden znak (Błąd analizy).
* Element docelowy może mieć wielu znaków, na przykład ö:oe, β:ss.
* Element docelowy może być pusta, wskazującą, czy znak powinna zostać usunięta.
* Źródło jest rozróżniana wielkość liter i muszą być identyczne.
* (Przecinek) i: (dwukropek) są zastrzeżone znaki i nie można zastąpić przy użyciu tej funkcji.
* Spacje i inne białe znaki w ciągu ReplacePattern są ignorowane.

**Przykład:**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
Zwraca Raksmorgas

`ReplaceChars("O’Neil",%ReplaceString%)`  
Zwraca "ONeil", jeden znacznik zdefiniowano do usunięcia.

- - -
### <a name="right"></a>Do prawej
**Opis:**  
Funkcja PRAWY zwraca określoną liczbę znaków z prawej strony (Zakończ), ciągu.

**Składnia:**  
`str Right(str string, num NumChars)`

* ciągu: ciąg do zwracania znaków z
* NumChars: Liczba identyfikująca liczbę znaków, które ma zostać zwrócony przez (po prawej) końca ciągu

**Uwagi:**  
Od ostatniej pozycji w ciągu są zwracane NumChars znaki.

Ciąg zawierający ostatnie znaki numChars w ciągu:

* Jeśli numChars = 0, zwraca pusty ciąg.
* Jeśli numChars < 0, zwraca ciąg wejściowy.
* Jeśli ciąg ma wartość null, zwraca pusty ciąg.

Jeśli ciąg zawiera mniej znaków niż liczba NumChars określony w, zostanie zwrócony ciąg jest taki sam jak ciąg.

**Przykład:**  
`Right("John Doe", 3)`  
Zwraca wartość "Nowak".

- - -
### <a name="rtrim"></a>Przytk
**Opis:**  
Funkcja RTrim usuwa spacje końcowe z ciąg.

**Składnia:**  
`str RTrim(str value)`

**Przykład:**  
`RTrim(" Test ")`  
Zwraca "Test".

- - -
### <a name="select"></a>Wybierz pozycję
**Opis:**  
Wszystkie wartości w atrybutu wielowartościowego (lub dane wyjściowe wyrażenia) na podstawie funkcji określony proces.

**Składnia:**  
`mvattr Select(variable item, mvattr attribute, func function)`  
`mvattr Select(variable item, exp expression, func function)`

* element: reprezentuje element w atrybutu wielowartościowego
* Atrybut: atrybutu wielowartościowego
* wyrażenie: wyrażenie, które zwraca kolekcję wartości
* warunek: dowolnej funkcji, które może przetworzyć elementu w atrybucie

**Przykłady:**  
`Select($item,[otherPhone],Replace($item,"-",""))`  
Zwraca wszystkie wartości w faksów atrybutu wielowartościowego, po usunięciu łączniki (-).

- - -
### <a name="split"></a>Podziel
**Opis:**  
Funkcja podziału oddzielone znakiem ogranicznika ciąg znaków i ułatwia wielokrotne wartości ciągu.

**Składnia:**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

* wartość: ciąg do oddzielania znakiem ogranicznika.
* Ogranicznik: pojedynczy znak ma być używany jako ogranicznik.
* limit: maksymalną liczbę wartości, które może zwracać.

**Przykład:**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
Zwraca ciąg wielowartościowe z 2 elementami jest przydatne w przypadku atrybutu proxyAddress.

- - -
### <a name="stringfromguid"></a>StringFromGuid
**Opis:**  
Funkcja StringFromGuid przyjmuje binarny identyfikator GUID i konwertuje ją na ciąg

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
Funkcja przełącznika służy do zwracania pojedynczą wartość na podstawie ocenionych warunków.

**Składnia:**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

* wyrażenie: użytkownik chce ocenić wyrażenia Variant.
* wartość: wartość zwracaną, jeśli odpowiednie wyrażenie ma wartość PRAWDA.

**Uwagi:**  
Lista argumentów funkcji przełącznika składa się z par wyrażeń i wartości. Wyrażenia są przetwarzane od lewej do prawej i jest zwracana wartość skojarzoną z pierwszym wyrażeniem, aby zwrócić wartość True. Jeśli elementy nie są poprawnie sparowane, występuje błąd w czasie wykonywania.

Na przykład jeśli Wyr1 ma wartość PRAWDA, przełącznik zwraca wartość1. Jeśli wyrażenie-1 jest wartość FAŁSZ, ale wyrażenie-2 ma wartość PRAWDA, przełącznik zwraca wartość-2 i tak dalej.

Przełącznik zwraca rób jeśli:

* Brak wyrażenia mają wartość PRAWDA.
* Pierwsze wyrażenie True ma odpowiadającą mu wartość, która ma wartość Null.

Przełącznik ocenia wszystkie wyrażenia, mimo że zwracany jest tylko jeden z nich. Z tego powodu należy uważać na niepożądane skutki uboczne. Na przykład jeśli oceny dowolne wyrażenie powoduje dzielenie przez zero, wystąpi błąd.

Wartość może być również funkcja błąd, co powoduje zwrócenie niestandardowy ciąg.

**Przykład:**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
Zwraca język używany w niektórych miastach głównych, w przeciwnym razie zwraca błąd.

- - -
### <a name="trim"></a>Przytnij
**Opis:**  
Funkcja przycinania usuwa spacji wiodących i końcowych białych z ciągu.

**Składnia:**  
`str Trim(str value)`  

**Przykład:**  
`Trim(" Test ")`  
Zwraca "Test".

`Trim([proxyAddresses])`  
Usuwa spacje początkowe i końcowe spacje dla każdej wartości w atrybucie proxyAddress.

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
### <a name="where"></a>Gdzie

**Opis:**  
Zwraca podzbiór wartości z atrybutu wielowartościowego (lub dane wyjściowe wyrażenia) na podstawie określonego warunku.

**Składnia:**  
`mvattr Where(variable item, mvattr attribute, exp condition)`  
`mvattr Where(variable item, exp expression, exp condition)`  
* element: reprezentuje element w atrybutu wielowartościowego
* Atrybut: atrybutu wielowartościowego
* warunek: dowolne wyrażenie, które może przyjąć wartość true lub false
* wyrażenie: wyrażenie, które zwraca kolekcję wartości

**Przykład:**  
`Where($item,[userCertificate],CertNotAfter($item)>Now())`  
Zwraca wartości certyfikatu w atrybutów wielowartościowych certyfikatu użytkownika, które nie są wygasło.

- - -
### <a name="with"></a>Zawiera
**Opis:**  
Funkcja With umożliwia Uprość wyrażenie złożone, za pomocą zmiennej do reprezentowania Podwyrażenie, która pojawia się jeden lub więcej razy w złożonych wyrażeń.

**Składnia:**
`With(var variable, exp subExpression, exp complexExpression)`  
* Zmienna: reprezentuje Podwyrażenie.
* Podwyrażenie: Podwyrażenie reprezentowany przez zmienną.
* complexExpression: wyrażenie złożone.

**Przykład:**  
`With($unExpiredCerts,Where($item,[userCertificate],CertNotAfter($item)>Now()),IIF(Count($unExpiredCerts)>0,$unExpiredCerts,NULL))`  
Jest funkcjonalnym odpowiednikiem:  
`IIF (Count(Where($item,[userCertificate],CertNotAfter($item)>Now()))>0, Where($item,[userCertificate],CertNotAfter($item)>Now()),NULL)`  
Która zwraca tylko niewygasłe certyfikatu wartości w atrybucie certyfikatu użytkownika.


- - -
### <a name="word"></a>Word
**Opis:**  
Funkcja Word zwraca słowa w ciągu, na podstawie parametrów opisujące ograniczniki oraz numer programu word do zwrócenia.

**Składnia:**  
`str Word(str string, num WordNumber, str delimiters)`

* ciągu: ciąg do zwrócenia słowa.
* WordNumber: Liczba identyfikująca numer word powinna zostać zwrócona.
* Ogranicznik: ciąg reprezentujący delimiter(s), które mają być używane do identyfikowania słowa

**Uwagi:**  
Każdy ciąg znaków w ciągu, rozdzielone co znaków w ograniczniki są identyfikowane jako słowa:

* Jeśli liczba < 1, zwraca pusty ciąg.
* Jeśli ciąg ma wartość null, zwraca pusty ciąg.

Jeśli ciąg zawiera mniej niż liczba słów lub ciąg nie zawiera słów identyfikowane przez ograniczniki, zwracany jest pustym ciągiem.

**Przykład:**  
`Word("The quick brown fox",3," ")`  
Zwraca "brązowy"

`Word("This,string!has&many separators",3,",!&#")`  
Zwróci "ma"

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Opis deklaratywne wyrażenia inicjowania obsługi administracyjnej](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
* [Azure AD Connect Sync: Dostosowywanie opcji synchronizacji](active-directory-aadconnectsync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md)
