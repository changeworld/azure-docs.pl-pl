---
title: Wyrażenia i odwołanie do funkcji w chmurze usługi Azure AD Connect
description: reference
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51c14fd7f427c29c47521a7355309e62ab2254ca
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78298619"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Pisanie wyrażeń mapowań atrybutów w usłudze Azure Active Directory
Podczas konfigurowania inicjowania obsługi administracyjnej chmury, jednym z typów mapowań atrybutów, które można określić jest mapowanie wyrażeń. 

Mapowanie wyrażeń umożliwia dostosowanie atrybutów przy użyciu wyrażenia podobnego do skryptu.  Dzięki temu można przekształcić dane lokalne w nową lub inną wartość.  Na przykład można połączyć dwa atrybuty w jeden atrybut, ponieważ ten pojedynczy atrybut jest używany przez jedną z aplikacji w chmurze.

Poniższy dokument obejmie wyrażenia podobne do skryptu, które są używane do przekształcania danych.  To tylko część procesu.  Następnie należy użyć tego wyrażenia i umieścić go w żądaniu sieci web do dzierżawy.  Aby uzyskać więcej informacji na ten temat, zobacz [Przekształcenia](how-to-transformation.md)

## <a name="syntax-overview"></a>Omówienie składni
Składnia wyrażeń dla mapowań atrybutów przypomina funkcje języka Visual Basic for Applications (VBA).

* Całe wyrażenie musi być zdefiniowane w kategoriach funkcji, które składają się z nazwy, po której następują argumenty w nawiasach: <br>
  *FunctionName(`<<argument 1>>``<<argument N>>`, )*
* Możesz zagnieżdżać funkcje w sobie nawzajem. Przykład: <br> *FunctionOne(FunctionTwo(`<<argument1>>`))*
* Można przekazać trzy różne typy argumentów do funkcji:
  
  1. Atrybuty, które muszą być ujęte w nawiasy kwadratowe. Na przykład: [attributeName]
  2. Stałe ciągów, które muszą być ujęte w cudzysłowy. Na przykład: "Stany Zjednoczone"
  3. Inne funkcje. Na przykład: FunctionOne(`<<argument1>>`,`<<argument2>>`FunctionTwo( ))
* W przypadku stałych ciągów, jeśli potrzebujesz ukośnika odwrotnego ( \ ) lub cudzysłowu ( " ) w ciągu, musi on zostać zmieniony z symbolem ukośnika odwrotnego ( \ ). Na przykład: "Nazwa \\firmy:\\"Contoso ""

## <a name="list-of-functions"></a>Lista funkcji
| Lista funkcji | Opis |
|-----|----|
|[Append](#append)|Pobiera wartość ciągu źródłowego i dołącza sufiks na jego końcu.|
|[BitAnd ( BitAnd )](#bitand)|Funkcja BitAnd ustawia określone bity na wartości.|
|[Cbool](#cbool)|Funkcja CBool zwraca wartość logiczną na podstawie obliczonego wyrażenia|
|[ConvertFromBase64](#convertfrombase64)|ConvertFromBase64 Funkcja konwertuje określoną wartość zakodowaną base64 na ciąg regularny.|
|[ConvertToBase64](#converttobase64)|ConvertToBase64 Funkcja konwertuje ciąg do ciągu Base64 Unicode. |
|[ConvertToUTF8Hex](#converttoutf8hex)|ConvertToUTF8Hex Funkcja konwertuje ciąg na wartość zakodowaną w hex UTF8.|
|[Liczba](#count)|Funkcja Count zwraca liczbę elementów w atrybucie wielowartościowym|
|[Cstr](#cstr)|Funkcja CStr konwertuje na typ danych ciągu.|
|[DataFromNum](#datefromnum)|Funkcja DateFromNum konwertuje wartość w formacie daty usługi AD na typ DateTime.|
|[DNComponent (Skomponent DN)](#dncomponent)|Funkcja DNComponent zwraca wartość określonego komponentu DN przechodzącego od lewej.|
|[Błąd](#error)|Funkcja Error służy do zwracania błędu niestandardowego.|
|[Formatdatetime](#formatdatetime) |Pobiera ciąg daty z jednego formatu i konwertuje go na inny format.| 
|[Identyfikator guid](#guid)|Funkcja Guid generuje nowy losowy identyfikator GUID.|           
|[Iif](#iif)|Funkcja IIF zwraca jeden z zestawu możliwych wartości na podstawie określonego warunku.|
|[Instr](#instr)|InStr Funkcja znajduje pierwsze wystąpienie podciąg w ciągu.|
|[Isnull](#isnull)|Jeśli wyrażenie ma wartość Null, funkcja IsNull zwraca wartość true.|
|[IsNullOrEmpty ( IsNullOrEmpty )](#isnullorempty)|Jeśli wyrażenie ma wartość null lub pusty ciąg, funkcja IsNullOrEmpty zwraca wartość true.|         
|[Ispresent](#ispresent)|Jeśli wyrażenie ma wartość ciągu, który nie jest null i nie jest pusty, a następnie IsPresent funkcja zwraca true.|    
|[IsString (IsString)](#isstring)|Jeśli wyrażenie można ocenić do typu ciągu, a następnie IsString funkcja ocenia True.|
|[Element](#item)|Funkcja Element zwraca jeden element z wielowartościowego ciągu/atrybutu.|
|[Dołącz](#join) |Join() jest podobny do Append(), z tą różnicą, że może łączyć wiele wartości **ciągów źródłowych** w jeden ciąg, a każda wartość zostanie oddzielona ciągiem **separatora.**| 
|[Lewej](#left)|Funkcja Left zwraca określoną liczbę znaków z lewej strony ciągu.|
|[Połowie](#mid) |Zwraca podciąg wartości źródłowej. Podciąg to ciąg, który zawiera tylko niektóre znaki z ciągu źródłowego.|
|[NormalizeDiakryty](#normalizediacritics)|Wymaga jednego argumentu ciągu. Zwraca ciąg, ale z dowolnymi znakami diakrytycznymi zastąpionymi równoważnymi znakami niekrytycznymi.|
|[Not](#not) |Odwraca wartość logiczną **źródła**. Jeśli wartość **źródła** to "*True*", zwraca "*False*". W przeciwnym razie zwraca "*True*".| 
|[Usuńduplika](#removeduplicates)|RemoveDuplicates Funkcja przyjmuje ciąg wielowartościowy i upewnij się, że każda wartość jest unikatowa.| 
|[Zastąpić](#replace) |Zastępuje wartości w ciągu. | 
|[Wybierz Wartość Nieoczyszka](#selectuniquevalue)|Wymaga co najmniej dwóch argumentów, które są regułami generowania unikatowych wartości zdefiniowanych przy użyciu wyrażeń. Funkcja ocenia każdą regułę, a następnie sprawdza wartość wygenerowaną pod kątem unikatowości w docelowej aplikacji/katalogu.| 
|[Znak SingleAppRoleAssignment](#singleapproleassignment)|Zwraca pojedynczą przypisanie aplikacjiRoleAssignment z listy wszystkich appRoleAssignments przypisanych do użytkownika dla danej aplikacji.| 
|[Split](#split)|Dzieli ciąg na tablicę wielowartościową, używając określonego znaku ogranicznika.|
|[StringFromSID (WYD.](#stringfromsid)|Funkcja StringFromSid konwertuje tablicę bajtów zawierającą identyfikator zabezpieczeń na ciąg.| 
|[StripSpaces (Przestrzenie ze striptizem](#stripspaces) |Usuwa wszystkie znaki spacji (" ") z ciągu źródłowego.| 
|[Przełącznik](#switch)|Gdy wartość **źródłowa** jest zgodna z **kluczem,** zwraca **wartość** tego **klucza**. | 
|[Tolower](#tolower)|Pobiera wartość ciągu *źródłowego* i konwertuje go na małe litery przy użyciu reguł kultury, które są określone.| 
|[Toupper](#toupper)|Pobiera wartość ciągu *źródłowego* i konwertuje go na wielkie litery przy użyciu reguł kultury, które są określone.|
|[Przycinanie](#trim)|Funkcja Przytnij usuwa białe spacje wiodące i końcowe z ciągu.|
|[Word](#word)|Funkcja Word zwraca wyraz zawarty w ciągu, na podstawie parametrów opisujących ograniczniki do użycia i numer wyrazu do zwrócenia.|

---
### <a name="append"></a>Append
**Funkcja:**<br> Dołącz(źródło, sufiks)

**Opis:**<br> Pobiera wartość ciągu źródłowego i dołącza sufiks na jego końcu.

**Parametry:**<br> 

   | Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **Źródła** |Wymagany |Ciąg |Zazwyczaj nazwa atrybutu z obiektu źródłowego. |
   | **Sufiks** |Wymagany |Ciąg |Ciąg, który chcesz dołączyć na końcu wartości źródłowej. |

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
 
 `BitAnd(&HF, &HF7)`</br>
 Zwraca wartość 7, ponieważ szesnastkowe wartości "F" i "F7" są obliczane do tej wartości.

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
### <a name="error"></a>Błąd
**Opis:**  
Funkcja Error służy do zwracania błędu niestandardowego.

**Składni:**  
`void Error(str ErrorMessage)`

**Przykład:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Jeśli accountname atrybutu nie jest obecny, zgłosić błąd na obiekcie.

---
### <a name="formatdatetime"></a>Formatdatetime
**Funkcja:**<br> FormatDateTime(źródło, inputFormat, outputFormat)

**Opis:**<br> Pobiera ciąg daty z jednego formatu i konwertuje go na inny format.

**Parametry:**<br> 

   | Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **Źródła** |Wymagany |Ciąg |Zazwyczaj nazwa atrybutu z obiektu źródłowego. |
   | **inputFormat** |Wymagany |Ciąg |Oczekiwany format wartości źródłowej. Aby uzyskać obsługiwane formaty, zobacz [https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
   | **wyjścieFormat** |Wymagany |Ciąg |Format daty wyjściowej. |

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
### <a name="isstring"></a>IsString (IsString)
**Opis:**  
Jeśli wyrażenie można ocenić do typu ciągu, a następnie IsString funkcja ocenia True.

**Składni:**  
`bool IsString(var expression)`

**Uwagi:**  
Służy do określenia, czy CStr() może zakończyć się pomyślnie, aby przeanalizować wyrażenie.

---
### <a name="join"></a>Join
**Funkcja:**<br> Sprzężenie(separator, source1, source2, ...)

**Opis:**<br> Join() jest podobny do Append(), z tą różnicą, że może łączyć wiele wartości **ciągów źródłowych** w jeden ciąg, a każda wartość zostanie oddzielona ciągiem **separatora.**

Jeśli jedna z wartości źródłowych jest atrybutem wielowartościowym, każda wartość w tym atrybucie zostanie połączona, oddzielona wartością separatora.

**Parametry:**<br> 

   | Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **Separator** |Wymagany |Ciąg |Ciąg używany do oddzielania wartości źródłowych, gdy są one łączone w jeden ciąg. Może być "", jeśli separator nie jest wymagany. |
   | **źródło1 ... sourceN** |Wymagana, zmienna liczba razy |Ciąg |Wartości ciągu, które mają być połączone ze sobą. |

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
Zwraca wartość `Joh`.

---
### <a name="mid"></a>Mid
**Funkcja:**<br> Środek (źródło, początek, długość)

**Opis:**<br> Zwraca podciąg wartości źródłowej. Podciąg to ciąg, który zawiera tylko niektóre znaki z ciągu źródłowego.

**Parametry:**<br> 

   | Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **Źródła** |Wymagany |Ciąg |Zazwyczaj nazwa atrybutu. |
   | **Uruchomić** |Wymagany |liczba całkowita |Indeks w ciągu **źródłowym,** gdzie podciąg powinien się rozpocząć. Pierwszy znak w ciągu będzie miał indeks 1, drugi znak będzie miał indeks 2 i tak dalej. |
   | **Długość** |Wymagany |liczba całkowita |Długość podciągu. Jeśli długość kończy się poza **ciągiem źródłowym,** funkcja zwróci podciąg z indeksu **startowego** do końca ciągu **źródłowego.** |

---
### <a name="normalizediacritics"></a>NormalizeDiakryty
**Funkcja:**<br> NormalizeDiacritics(źródło)

**Opis:**<br> Wymaga jednego argumentu ciągu. Zwraca ciąg, ale z dowolnymi znakami diakrytycznymi zastąpionymi równoważnymi znakami niekrytycznymi. Zazwyczaj używane do konwertowania imion i nazwisk zawierających znaki diakrytyczne (znaki akcentujące) na wartości prawne, które mogą być używane w różnych identyfikatorach użytkowników, takich jak główne nazwy użytkowników, nazwy kont SAM i adresy e-mail.

**Parametry:**<br> 

   | Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **Źródła** |Wymagany |Ciąg | Zwykle atrybut imienia lub nazwiska. |

---
### <a name="not"></a>Not
**Funkcja:**<br> Not(źródło)

**Opis:**<br> Odwraca wartość logiczną **źródła**. Jeśli wartość **źródła** to "*True*", zwraca "*False*". W przeciwnym razie zwraca "*True*".

**Parametry:**<br> 

   | Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **Źródła** |Wymagany |Ciąg logiczny |Oczekiwane wartości **źródłowe** to "True" lub "False". |

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
**Funkcja:**<br> Zamień(źródło, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, szablon)

**Opis:**<br>
Zastępuje wartości w ciągu. Działa inaczej w zależności od podanych parametrów:

* Gdy **oldValue** i **replacementValue** są dostarczane:
  
  * Zastępuje wszystkie wystąpienia **staregoValue** w **źródle** **z replacementValue**
* Gdy **stareWartość** i **szablon** są dostarczane:
  
  * Zastępuje wszystkie wystąpienia **starejWartość** w **szablonie** wartością **źródłową**
* Gdy **regexPattern** i **replacementValue** są dostarczane:

  * Funkcja stosuje **wyrażenie regularnePattern** do ciągu **źródłowego** i można użyć nazw grup **replacementValue** odc.
* Gdy **regexPattern**, **regexGroupName**, **replacementValue** są dostarczane:
  
  * Funkcja stosuje **wyrażenie regularnePattern** do ciągu **źródłowego** i zastępuje wszystkie wartości pasujące **do wyrażenia regularnegoGroupName** z **replacementValue**
* Gdy **regexPattern**, **regexGroupName**, **replacementAttributeName** są dostarczane:
  
  * Jeśli **źródło** nie ma wartości, **zwracane** jest źródło
  * Jeśli **źródło** ma wartość, funkcja stosuje **wyrażenie regularnePattern** do ciągu **źródłowego** i zastępuje wszystkie wartości pasujące **regexGroupName** z wartością skojarzoną z **replacementAttributeName**

**Parametry:**<br> 

   | Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **Źródła** |Wymagany |Ciąg |Zazwyczaj nazwa atrybutu z obiektu **źródłowego.** |
   | **Oldvalue** |Optional (Opcjonalność) |Ciąg |Wartość do wymiany w **źródle** lub **szablonie**. |
   | **regexPattern** |Optional (Opcjonalność) |Ciąg |Wzorzec regex dla wartości, która ma zostać zastąpiona w **źródle**. Lub, gdy używana jest **nazwa zastępczaPropertyName,** wzorzec wyodrębnić wartość z **replacementPropertyName**. |
   | **nazwa grupy regex** |Optional (Opcjonalność) |Ciąg |Nazwa grupy wewnątrz **regexPattern**. Tylko wtedy, gdy używana jest **nazwa zastępczaPropertyName,** wyodrębnimy wartość tej grupy jako **replacementValue** z **replacementPropertyName**. |
   | **wymianaValue** |Optional (Opcjonalność) |Ciąg |Nowa wartość, aby zastąpić stary. |
   | **replacementAttributeName** |Optional (Opcjonalność) |Ciąg |Nazwa atrybutu, który ma być używany dla wartości zastępczej |
   | **Szablonu** |Optional (Opcjonalność) |Ciąg |Gdy wartość **szablonu** jest podana, będziemy szukać **oldValue** wewnątrz szablonu i zastąpić go wartością **źródłową.** |

---
### <a name="selectuniquevalue"></a>Wybierz Wartość Nieoczyszka
**Funkcja:**<br> SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, ...)

**Opis:**<br> Wymaga co najmniej dwóch argumentów, które są regułami generowania unikatowych wartości zdefiniowanych przy użyciu wyrażeń. Funkcja ocenia każdą regułę, a następnie sprawdza wartość wygenerowaną pod kątem unikatowości w docelowej aplikacji/katalogu. Pierwszą znalezioną unikatową wartością będzie ta zwrócona. Jeśli wszystkie wartości już istnieją w docelowych, wpis zostanie zdeesowany i powód zostanie zarejestrowany w dziennikach inspekcji. Nie ma górnej granicy liczby argumentów, które mogą być dostarczone.

> [!NOTE]
> - Jest to funkcja najwyższego poziomu, nie może być zagnieżdżona.
> - Tej funkcji nie można zastosować do atrybutów, które mają pasujące pierwszeństwo.  
> - Ta funkcja jest przeznaczona tylko do tworzenia wpisu. Podczas używania go z atrybutem ustaw właściwość **Zastosuj mapowanie** **na Tylko podczas tworzenia obiektu**.
> - Ta funkcja jest obecnie obsługiwana tylko dla "Workday to Active Directory User Provisioning". Nie można używać z innymi aplikacjami inicjowania obsługi administracyjnej. 


**Parametry:**<br> 

   | Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **uniqueValueRule1 ... uniqueValueRuleN** |Wymagane są co najmniej 2, nie |Ciąg | Lista unikatowych reguł generowania wartości do oceny. |


---
### <a name="singleapproleassignment"></a>Znak SingleAppRoleAssignment
**Funkcja:**<br> SingleAppRoleAssignment([appRoleAssignments])

**Opis:**<br> Zwraca pojedynczą przypisanie aplikacjiRoleAssignment z listy wszystkich appRoleAssignments przypisanych do użytkownika dla danej aplikacji. Ta funkcja jest wymagana do konwersji appRoleAssignments obiektu do ciągu nazwy pojedynczej roli. Należy zauważyć, że najlepszym rozwiązaniem jest zapewnienie tylko jeden appRoleAssignment jest przypisany do jednego użytkownika w czasie, a jeśli wiele ról są przypisane ciąg roli zwracany może nie być przewidywalne. 

**Parametry:**<br> 

  | Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
  |--- | --- | --- | --- |
  | **[appRoleAssignments]** |Wymagany |Ciąg |**[appRoleAssignments]** obiekt. |

---
### <a name="split"></a>Podział
**Funkcja:**<br> Split(źródło, ogranicznik)

**Opis:**<br> Dzieli ciąg na tablicę wielowartościową, używając określonego znaku ogranicznika.

**Parametry:**<br> 

   | Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **Źródła** |Wymagany |Ciąg |wartości **źródłowego** do aktualizacji. |
   | **Ogranicznik** |Wymagany |Ciąg |Określa znak, który będzie używany do dzielenia ciągu (przykład: ",") |

---
### <a name="stringfromsid"></a>StringFromSid (Wyd.
**Opis:**  
Funkcja StringFromSid konwertuje tablicę bajtów zawierającą identyfikator zabezpieczeń na ciąg.

**Składni:**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="stripspaces"></a>StripSpaces (Przestrzenie ze striptizem
**Funkcja:**<br> StripSpaces(źródło)

**Opis:**<br> Usuwa wszystkie znaki spacji (" ") z ciągu źródłowego.

**Parametry:**<br> 

   | Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **Źródła** |Wymagany |Ciąg |wartości **źródłowego** do aktualizacji. |

---
### <a name="switch"></a>Przełącznik
**Funkcja:**<br> Przełącznik(źródło, defaultValue, key1, value1, key2, value2, ...)

**Opis:**<br> Gdy wartość **źródłowa** jest zgodna z **kluczem,** zwraca **wartość** tego **klucza**. Jeśli wartość **źródła** nie jest zgodna z żadnymi kluczami, zwraca **wartość domyślną**.  **Parametry** **klucza** i wartości muszą zawsze być w parach. Funkcja zawsze oczekuje parzyste liczby parametrów.

**Parametry:**<br> 

   | Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **Źródła** |Wymagany |Ciąg |**Wartość źródła** do sprawdzenia. |
   | **Defaultvalue** |Optional (Opcjonalność) |Ciąg |Wartość domyślna, która ma być używana, gdy źródło nie pasuje do żadnych kluczy. Może być pustym ciągiem (""). |
   | **key** |Wymagany |Ciąg |**Klucz** do porównania wartości **źródłowych** z. |
   | **value** |Wymagany |Ciąg |Wartość zastępcza dla **źródła** pasującego do klucza. |

---
### <a name="tolower"></a>Tolower
**Funkcja:**<br> ToLower(źródło, kultura)

**Opis:**<br> Pobiera wartość ciągu *źródłowego* i konwertuje go na małe litery przy użyciu reguł kultury, które są określone. Jeśli nie określono żadnych informacji o *kulturze,* użyje ono kultury niezmiennej.

**Parametry:**<br> 

   | Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **Źródła** |Wymagany |Ciąg |Zazwyczaj nazwa atrybutu z obiektu źródłowego |
   | **kultura** |Optional (Opcjonalność) |Ciąg |Format nazwy kultury na podstawie RFC 4646 to *languagecode2-country/regioncode2*, gdzie *languagecode2* jest dwuliterowym kodem języka, a *kod kraju/regionu2* jest dwuliterowym kodem subkultury. Przykłady obejmują ja-JP dla języka japońskiego (Japonia) i en-US dla języka angielskiego (Stany Zjednoczone). W przypadkach, gdy dwuliterowy kod języka nie jest dostępny, używany jest trzyliterowy kod pochodzący z iso 639-2.|

---

### <a name="toupper"></a>Toupper
**Funkcja:**<br> ToUpper(źródło, kultura)

**Opis:**<br> Pobiera wartość ciągu *źródłowego* i konwertuje go na wielkie litery przy użyciu reguł kultury, które są określone. Jeśli nie określono żadnych informacji o *kulturze,* użyje ono kultury niezmiennej.

**Parametry:**<br> 

  | Nazwa | Wymagane/ powtarzanie | Typ | Uwagi |
  | --- | --- | --- | --- |
  | **Źródła** |Wymagany |Ciąg |Zazwyczaj nazwa atrybutu z obiektu źródłowego. |
  | **kultura** |Optional (Opcjonalność) |Ciąg |Format nazwy kultury na podstawie RFC 4646 to *languagecode2-country/regioncode2*, gdzie *languagecode2* jest dwuliterowym kodem języka, a *kod kraju/regionu2* jest dwuliterowym kodem subkultury. Przykłady obejmują ja-JP dla języka japońskiego (Japonia) i en-US dla języka angielskiego (Stany Zjednoczone). W przypadkach, gdy dwuliterowy kod języka nie jest dostępny, używany jest trzyliterowy kod pochodzący z iso 639-2.|

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

## <a name="examples"></a>Przykłady
### <a name="strip-known-domain-name"></a>Rozsyłanie znanej nazwy domeny
Aby uzyskać nazwę użytkownika, musisz usunąć znaną nazwę domeny z wiadomości e-mail użytkownika. <br>
Na przykład, jeśli domena jest "contoso.com", można użyć następującego wyrażenia:

**Wyrażenie:** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Przykładowe wejście / wyjście:** <br>

* **WEJŚCIE** (poczta):john.doe@contoso.com" "
* **WYJŚCIE:**"john.doe"

### <a name="append-constant-suffix-to-user-name"></a>Dołączanie stałego sufiksu do nazwy użytkownika
Jeśli używasz piaskownicy Salesforce, może być konieczne dołączenie dodatkowego sufiksu do wszystkich nazw użytkowników przed ich synchronizacją.

**Wyrażenie:** <br>
`Append([userPrincipalName], ".test")`

**Przykładowe wejście/wyjście:** <br>

* **WEJŚCIE**: (userPrincipalName): "John.Doe@contoso.com"
* **WYJŚCIE**:John.Doe@contoso.com.test" "

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Generowanie aliasu użytkownika przez łączenie części imienia i nazwiska
Musisz wygenerować alias użytkownika, biorąc pierwsze 3 litery imienia użytkownika i pierwsze 5 liter nazwiska użytkownika.

**Wyrażenie:** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Przykładowe wejście/wyjście:** <br>

* **INPUT** (givenName): "John"
* **INPUT** (nazwisko): "Doe"
* **WYJŚCIE:**"JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Usuwanie znaków diakrutycznych z ciągu
Należy zastąpić znaki zawierające znaki akcentu równoważnymi znakami, które nie zawierają znaczników akcentu.

**Wyrażenie:** <br>
NormalizeDiacritics([givenName])

**Przykładowe wejście/wyjście:** <br>

* **INPUT** (givenName): "Zoë"
* **WYJŚCIE**: "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Dzielenie ciągu na tablicę o wielu wartościach
Należy wziąć listę ciągów rozdzielanych przecinkami i podzielić je na tablicę, którą można podłączyć do atrybutu wielowartościowego, takiego jak atrybut Uprawnienia Zestawy uprawnień Salesforce. W tym przykładzie lista zestawów uprawnień została wypełniona w extensionAttribute5 w usłudze Azure AD.

**Wyrażenie:** <br>
Split([extensionAttribute5], ",")

**Przykładowe wejście/wyjście:** <br>

* **INPUT** (extensionAttribute5): "PermissionSetOne, PermisionSetTwo"
* **WYJŚCIE**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Data wyjściowa jako ciąg znaków w określonym formacie
Chcesz wysłać daty do aplikacji SaaS w określonym formacie. <br>
Na przykład chcesz sformatować daty servicenow.

**Wyrażenie:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Przykładowe wejście/wyjście:**

* **INPUT** (extensionAttribute1): "20150123105347.1Z"
* **WYJŚCIE**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Zastępowanie wartości na podstawie wstępnie zdefiniowanego zestawu opcji

Należy zdefiniować strefę czasową użytkownika na podstawie kodu stanu przechowywanego w usłudze Azure AD. <br>
Jeśli kod stanu nie pasuje do żadnej ze wstępnie zdefiniowanych opcji, użyj domyślnej wartości "Australia/Sydney".

**Wyrażenie:** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Przykładowe wejście/wyjście:**

* **WEJŚCIE** (stan): "QLD"
* **PRODUKCJA**: "Australia/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Zamienianie znaków przy użyciu wyrażenia regularnego
Musisz znaleźć znaki pasujące do wartości wyrażenia regularnego i usunąć je.

**Wyrażenie:** <br>

Zamień([mailNickname], "[a-zA-Z_]*", , "", ,

**Przykładowe wejście/wyjście:**

* **WEJŚCIE** (mailNickname: "john_doe72"
* **WYJŚCIE:**"72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Konwertowanie wygenerowanej wartości userPrincipalName (UPN) na małe
W poniższym przykładzie wartość UPN jest generowana przez łączenie pól źródłowych PreferredFirstName i PreferredLastName, a funkcja ToLower działa na wygenerowanym ciągu w celu przekonwertowania wszystkich znaków na małe litery. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Przykładowe wejście/wyjście:**

* **WEJŚCIE** (PreferredFirstName): "John"
* **INPUT** (PreferredLastName): "Smith"
* **WYJŚCIE**:john.smith@contoso.com" "

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Generowanie unikatowej wartości atrybutu userPrincipalName (UPN)
Na podstawie imienia, drugiego imienia i nazwiska użytkownika należy wygenerować wartość atrybutu UPN i sprawdzić jego unikatowość w docelowym katalogu usługi AD przed przypisaniem tej wartości do atrybutu NAZWY UPN.

**Wyrażenie:** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**Przykładowe wejście/wyjście:**

* **WEJŚCIE** (PreferredFirstName): "John"
* **INPUT** (PreferredLastName): "Smith"
* **WYJŚCIE**:John.Smith@contoso.com" " jeśli John.Smith@contoso.com wartość UPN nie istnieje jeszcze w katalogu
* **WYJŚCIE**:J.Smith@contoso.com" " jeśli John.Smith@contoso.com wartość UPN już istnieje w katalogu
* **WYJŚCIE**:Jo.Smith@contoso.com" ", jeśli powyższe dwie wartości UPN już istnieją w katalogu


## <a name="next-steps"></a>Następne kroki 

- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Co to jest aprowizacja w chmurze programu Azure AD Connect?](what-is-cloud-provisioning.md)
