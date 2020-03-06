---
title: Azure AD Connect wyrażeń aprowizacji w chmurze i odwołania do funkcji
description: Odwołanie
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
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78298619"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Pisanie wyrażeń mapowania atrybutów w Azure Active Directory
Podczas konfigurowania aprowizacji w chmurze jednym z typów mapowań atrybutów, które można określić, jest mapowanie wyrażenia. 

Mapowanie wyrażenia umożliwia dostosowanie atrybutów przy użyciu wyrażenia przypominającego skrypt.  Dzięki temu można przekształcić dane lokalne w nową lub inną wartość.  Na przykład możesz chcieć połączyć dwa atrybuty w jeden atrybut, ponieważ ten pojedynczy atrybut jest używany przez jedną z aplikacji w chmurze.

Następujący dokument obejmuje wyrażenia podobne do skryptu, które są używane do przekształcania danych.  Jest to tylko część procesu.  Następnie konieczne będzie użycie tego wyrażenia i umieszczenie go w żądaniu sieci Web w dzierżawie.  Aby uzyskać więcej informacji na temat, zobacz [przekształcenia](how-to-transformation.md)

## <a name="syntax-overview"></a>Omówienie składni
Składnia wyrażeń do mapowania atrybutów jest przypominający języka Visual Basic for Applications (VBA) funkcje.

* Całe wyrażenie musi być zdefiniowany w zakresie funkcji, które składają się z nazwy argumentów w nawiasach: <br>
  *FunctionName (`<<argument 1>>`,`<<argument N>>`)*
* Może być zagnieżdżony funkcji w ramach siebie nawzajem. Na przykład: <br> *FunctionOne (FunctionTwo (`<<argument1>>`))*
* Trzy różne rodzaje argumenty można przekazać do funkcji:
  
  1. Atrybuty, które muszą być ujęte w nawiasy kwadratowe. Na przykład: [attributeName]
  2. Stałe typu String, które muszą być ujęte w cudzysłów. Na przykład: "United States"
  3. Inne funkcje. Na przykład: FunctionOne (`<<argument1>>`, FunctionTwo (`<<argument2>>`))
* Dla stałych ciągów Jeśli potrzebujesz kreski ułamkowej odwróconej (\) lub cudzysłowu (") w ciągu go należy użyć znaków ucieczki symbolem kreski ułamkowej odwróconej (\). Na przykład: "Nazwa firmy: \\" contoso\\""

## <a name="list-of-functions"></a>Lista funkcji
| Lista funkcji | Opis |
|-----|----|
|[Łączono](#append)|Przyjmuje wartość ciągu źródła i dołącza sufiks na końcu.|
|[BitAnd](#bitand)|Funkcja BitAnd ustawia określoną liczbę bitów w wartości.|
|[CBool](#cbool)|Funkcja CBool zwraca wartość logiczną opartą na obliczanym wyrażeniu|
|[ConvertFromBase64](#convertfrombase64)|Funkcja ConvertFromBase64 konwertuje określoną zakodowaną wartość Base64 na zwykły ciąg.|
|[ConvertToBase64](#converttobase64)|Funkcja ConvertToBase64 konwertuje ciąg na ciąg Unicode Base64. |
|[ConvertToUTF8Hex](#converttoutf8hex)|Funkcja ConvertToUTF8Hex konwertuje ciąg na zakodowaną wartość szesnastkową UTF8.|
|[Liczbą](#count)|Funkcja count zwraca liczbę elementów w atrybucie wielowartościowym|
|[CStr](#cstr)|Funkcja CStr konwertuje na typ danych ciągu.|
|[DateFromNum](#datefromnum)|Funkcja DateFromNum konwertuje wartość w formacie daty usługi AD na typ DateTime.|
|[DNComponent](#dncomponent)|Funkcja DNComponent zwraca wartość określonego składnika DN z lewej strony.|
|[Porn](#error)|Funkcja Error służy do zwrócenia błędu niestandardowego.|
|[FormatDateTime](#formatdatetime) |Pobiera ciąg daty z jednego formatu i konwertuje je do innego formatu.| 
|[IDENT](#guid)|Identyfikator GUID funkcji generuje nowy losowy identyfikator GUID.|           
|[IIF](#iif)|Funkcja IIF zwraca jeden z zestawu możliwych wartości na podstawie określonego warunku.|
|[InStr](#instr)|Funkcja InStr Znajdowanie pierwszego wystąpienia podciągu w ciągu.|
|[IsNull](#isnull)|Jeśli wyrażenie daje w wyniku wartość null, Funkcja IsNull zwraca wartość true.|
|[IsNullOrEmpty](#isnullorempty)|Jeśli wyrażenie ma wartość null lub jest pustym ciągiem, funkcja IsNullOrEmpty zwraca wartość true.|         
|[Isobecne](#ispresent)|Jeśli wyrażenie daje w wyniku ciąg, który nie ma wartości null i nie jest pusty, funkcja isprezent zwraca wartość true.|    
|[IsString](#isstring)|Jeśli wyrażenie może być szacowane do typu ciągu, funkcja IsString daje w wyniku wartość true.|
|[Element](#item)|Funkcja Item zwraca jeden element z wielowartościowego ciągu/atrybutu.|
|[Dołącz](#join) |Join () jest podobny do dołączania (), z tą różnicą, że może połączyć wiele wartości ciągu **źródłowego** w jeden ciąg, a każda wartość zostanie oddzielona przez ciąg **separatora** .| 
|[Lewym](#left)|Funkcja Left Zwraca określoną liczbę znaków z lewej strony ciągu.|
|[Mid](#mid) |Zwraca podciąg wartość źródła. Podciąg jest ciąg zawierający tylko niektóre ze znaków z ciągu źródłowego.|
|[NormalizeDiacritics](#normalizediacritics)|Wymaga jednego argumentu ciągu. Zwraca ciąg, ale za pomocą wszystkie znaki diakrytyczne zastępowane znakami diakrytyczne równoważne.|
|[Niemożliwe](#not) |Odwraca wartość logiczną **źródła**. Jeśli wartością **źródłową** jest "*true*", zwraca wartość "*false*". W przeciwnym razie zwraca wartość "*true*".| 
|[RemoveDuplicates —](#removeduplicates)|Funkcja RemoveDuplicates — przyjmuje ciąg o wartości wielowartościowej i upewnij się, że każda wartość jest unikatowa.| 
|[Stępować](#replace) |Zamienia wartości ciągu. | 
|[SelectUniqueValue](#selectuniquevalue)|Wymaga co najmniej dwa argumenty, które są definiowane przy użyciu wyrażeń zasad generowania unikatową wartość. Funkcja ocenia każdą regułę, a następnie sprawdza wartość generowane unikatowość w katalogu/aplikacji docelowej.| 
|[SingleAppRoleAssignment](#singleapproleassignment)|Zwraca pojedynczy appRoleAssignment z listy wszystkich appRoleAssignments przypisanych do użytkownika dla danej aplikacji.| 
|[Podziału](#split)|Dzieli ciąg na tablicę wielowartościową przy użyciu określonego znaku ogranicznika.|
|[StringFromSID](#stringfromsid)|Funkcja StringFromSid konwertuje tablicę bajtową zawierającą identyfikator zabezpieczeń na ciąg.| 
|[StripSpaces](#stripspaces) |Usuwa wszystkie spacje ("") znaków z ciągu źródłowego.| 
|[Przełącznika](#switch)|Gdy wartość **źródłowa** jest zgodna z **kluczem**, zwraca **wartość** dla tego **klucza**. | 
|[ToLower](#tolower)|Pobiera wartość ciągu *źródłowego* i konwertuje ją na małe litery przy użyciu określonych reguł kultury.| 
|[ToUpper](#toupper)|Pobiera wartość ciągu *źródłowego* i konwertuje ją na wielkie litery przy użyciu określonych reguł kultury.|
|[Trim](#trim)|Funkcja Trim usuwa wiodące i końcowe białe znaki z ciągu.|
|[Słow](#word)|Funkcja słowa zwraca słowo zawarte w ciągu, w oparciu o parametry opisujące ograniczniki do użycia i numer wyrazu do zwrócenia.|

---
### <a name="append"></a>Append
**Funkcyjn**<br> Append(source, suffix)

**Zharmonizowan**<br> Przyjmuje wartość ciągu źródła i dołącza sufiks na końcu.

**Wejściowe**<br> 

   | Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **zewnętrz** |Wymagany |Ciąg |Zazwyczaj nazwa atrybutu z obiektu źródłowego. |
   | **przedrostk** |Wymagany |Ciąg |Ciąg, który chcesz dołączyć do końca wartość źródła. |

---
### <a name="bitand"></a>BitAnd
**Zharmonizowan**  
Funkcja BitAnd ustawia określoną liczbę bitów w wartości.

**Obowiązuje**  
`num BitAnd(num value1, num value2)`

* wartość1, wartość2: wartości liczbowe, które powinny być AND'ed razem

**Uwagi**  
Ta funkcja konwertuje oba parametry na reprezentację binarną i ustawia bit na:

* 0 — Jeśli jedna lub obie odpowiadające bity w *wartość1* i *wartość2* są równe 0
* 1 — Jeśli obie odpowiadające bity są 1.

Innymi słowy zwraca 0 we wszystkich przypadkach, z wyjątkiem sytuacji, gdy odpowiadające im bity obu parametrów są 1.

**Przykład:**  
 
 `BitAnd(&HF, &HF7)`</br>
 Zwraca wartość 7, ponieważ w liczbie szesnastkowej "F" i "F7" można obliczyć tę wartości.

---

### <a name="cbool"></a>CBool
**Zharmonizowan**  
Funkcja CBool zwraca wartość logiczną opartą na obliczanym wyrażeniu

**Obowiązuje**  
`bool CBool(exp Expression)`

**Uwagi**  
Jeśli wyrażenie daje w wyniku wartość różną od zera, Funkcja CBool zwraca wartość true, w przeciwnym razie zwraca wartość false.

**Przykład:**  
`CBool([attrib1] = [attrib2])`  

Zwraca wartość true, jeśli oba atrybuty mają tę samą wartość.

---
### <a name="convertfrombase64"></a>ConvertFromBase64
**Zharmonizowan**  
Funkcja ConvertFromBase64 konwertuje określoną zakodowaną wartość Base64 na zwykły ciąg.

**Obowiązuje**  
`str ConvertFromBase64(str source)` — przyjmuje kod Unicode do kodowania  
`str ConvertFromBase64(str source, enum Encoding)`

* Źródło: zakodowany ciąg Base64  
* Kodowanie: Unicode, ASCII, UTF8

**Przykład**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Oba przykłady zwracają "*Hello World!* "

---
### <a name="converttobase64"></a>ConvertToBase64
**Zharmonizowan**  
Funkcja ConvertToBase64 konwertuje ciąg na ciąg Unicode Base64.  
Konwertuje wartość tablicy liczb całkowitych na równoważną reprezentację w postaci ciągu, która jest zakodowana przy użyciu cyfry Base-64.

**Obowiązuje**  
`str ConvertToBase64(str source)`

**Przykład:**  
`ConvertToBase64("Hello world!")`  
Zwraca wartość "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Zharmonizowan**  
Funkcja ConvertToUTF8Hex konwertuje ciąg na zakodowaną wartość szesnastkową UTF8.

**Obowiązuje**  
`str ConvertToUTF8Hex(str source)`

**Uwagi**  
Format danych wyjściowych tej funkcji jest używany przez Azure Active Directory jako format atrybutu nazwy wyróżniającej.

**Przykład:**  
`ConvertToUTF8Hex("Hello world!")`  
Zwraca 48656C6C6F20776F726C6421

---
### <a name="count"></a>Licznik
**Zharmonizowan**  
Funkcja count zwraca liczbę elementów w atrybucie wielowartościowym

**Obowiązuje**  
`num Count(mvstr attribute)`

---
### <a name="cstr"></a>CStr
**Zharmonizowan**  
Funkcja CStr konwertuje na typ danych ciągu.

**Obowiązuje**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* wartość: może być wartością liczbową, atrybutem odwołania lub wartością logiczną.

**Przykład:**  
`CStr([dn])`  
Może zwrócić wartość "CN = Jan, DC = contoso, DC = com"

---
### <a name="datefromnum"></a>DateFromNum
**Zharmonizowan**  
Funkcja DateFromNum konwertuje wartość w formacie daty usługi AD na typ DateTime.

**Obowiązuje**  
`dt DateFromNum(num value)`

**Przykład:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Zwraca datę i godzinę reprezentującą 2012-01-01 23:00:00

---
### <a name="dncomponent"></a>DNComponent
**Zharmonizowan**  
Funkcja DNComponent zwraca wartość określonego składnika DN z lewej strony.

**Obowiązuje**  
`str DNComponent(ref dn, num ComponentNumber)`

* DN: atrybut Reference do zinterpretowania
* ComponentNumber: składnik w nazwie DN do zwrócenia

**Przykład:**  
`DNComponent(CRef([dn]),1)`  
Jeśli nazwa DN to "CN = Jan, OU =...", zwraca Jan

---
### <a name="error"></a>Błąd
**Zharmonizowan**  
Funkcja Error służy do zwrócenia błędu niestandardowego.

**Obowiązuje**  
`void Error(str ErrorMessage)`

**Przykład:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Jeśli atrybut AccountName nie istnieje, zgłoś błąd w obiekcie.

---
### <a name="formatdatetime"></a>FormatDateTime
**Funkcyjn**<br> FormatDateTime (źródło, inputFormat outputFormat)

**Zharmonizowan**<br> Pobiera ciąg daty z jednego formatu i konwertuje je do innego formatu.

**Wejściowe**<br> 

   | Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **zewnętrz** |Wymagany |Ciąg |Zazwyczaj nazwa atrybutu z obiektu źródłowego. |
   | **inputFormat** |Wymagany |Ciąg |Oczekiwany format wartość źródła. Obsługiwane formaty można znaleźć w temacie [https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
   | **outputFormat** |Wymagany |Ciąg |Format wyjściowej daty. |

---
### <a name="guid"></a>Guid
**Zharmonizowan**  
Identyfikator GUID funkcji generuje nowy losowy identyfikator GUID

**Obowiązuje**  
`str Guid()`

---
### <a name="iif"></a>IIF
**Zharmonizowan**  
Funkcja IIF zwraca jeden z zestawu możliwych wartości na podstawie określonego warunku.

**Obowiązuje**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* warunek: dowolna wartość lub wyrażenie, które może przyjąć wartość PRAWDA lub FAŁSZ.
* valueIfTrue: Jeśli wynikiem warunku jest wartość true, zwrócona wartość.
* valueIfFalse: Jeśli warunek ma wartość false, zwrócona wartość.

**Przykład:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Jeśli użytkownik jest informatykiem, zwraca alias użytkownika ze znakiem "t-", który został dodany do początku tego elementu, w przeciwnym razie zwraca alias użytkownika.

---
### <a name="instr"></a>InStr
**Zharmonizowan**  
Funkcja InStr Znajdowanie pierwszego wystąpienia podciągu w ciągu

**Obowiązuje**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

* stringcheck: ciąg do przeszukania
* stringmatch: ciąg, który ma zostać znaleziony
* Początek: pozycja początkowa, aby znaleźć podciąg
* Porównanie: vbTextCompare lub vbBinaryCompare

**Uwagi**  
Zwraca pozycję, w której znaleziono podciąg lub wartość 0, jeśli nie została znaleziona.

**Przykład:**  
`InStr("The quick brown fox","quick")`  
Evalues do 5

`InStr("repEated","e",3,vbBinaryCompare)`  
Szacuje się w 7

---
### <a name="isnull"></a>IsNull
**Zharmonizowan**  
Jeśli wyrażenie daje w wyniku wartość null, Funkcja IsNull zwraca wartość true.

**Obowiązuje**  
`bool IsNull(var Expression)`

**Uwagi**  
W przypadku atrybutu wartość null jest wyrażana przez nieobecność atrybutu.

**Przykład:**  
`IsNull([displayName])`  
Zwraca wartość true, jeśli atrybut nie jest obecny w CS lub MV.

---
### <a name="isnullorempty"></a>IsNullOrEmpty
**Zharmonizowan**  
Jeśli wyrażenie ma wartość null lub jest pustym ciągiem, funkcja IsNullOrEmpty zwraca wartość true.

**Obowiązuje**  
`bool IsNullOrEmpty(var Expression)`

**Uwagi**  
W przypadku atrybutu wynikiem tego jest wartość true, jeśli atrybut jest nieobecny lub jest obecny, ale jest ciągiem pustym.  
Odwrotność tej funkcji ma nazwę isobecne.

**Przykład:**  
`IsNullOrEmpty([displayName])`  
Zwraca wartość true, jeśli atrybut nie jest obecny lub jest pustym ciągiem w CS lub MV.

---
### <a name="ispresent"></a>Isobecne
**Zharmonizowan**  
Jeśli wyrażenie daje w wyniku ciąg, który nie ma wartości null i nie jest pusty, funkcja isprezent zwraca wartość true.

**Obowiązuje**  
`bool IsPresent(var expression)`

**Uwagi**  
Odwrotność tej funkcji ma nazwę IsNullOrEmpty.

**Przykład:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="item"></a>Element
**Zharmonizowan**  
Funkcja Item zwraca jeden element z wielowartościowego ciągu/atrybutu.

**Obowiązuje**  
`var Item(mvstr attribute, num index)`

* attribute: Atrybut wielowartościowy
* index: indeks do elementu w ciągu wielowartościowym.

**Uwagi**  
Funkcja Item jest przydatna razem z funkcją Contains, ponieważ Ostatnia funkcja zwraca indeks do elementu w atrybucie wielowartościowym.

Zgłasza błąd, jeśli indeks jest poza zakresem.

**Przykład:**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
Zwraca podstawowy adres e-mail.

---
### <a name="isstring"></a>IsString
**Zharmonizowan**  
Jeśli wyrażenie może być szacowane do typu ciągu, funkcja IsString daje w wyniku wartość true.

**Obowiązuje**  
`bool IsString(var expression)`

**Uwagi**  
Służy do określenia, czy CStr () może pomyślnie przeanalizować wyrażenie.

---
### <a name="join"></a>Join
**Funkcyjn**<br> Dołącz do (separator, źródło1 źródło2...)

**Zharmonizowan**<br> Join () jest podobny do dołączania (), z tą różnicą, że może połączyć wiele wartości ciągu **źródłowego** w jeden ciąg, a każda wartość zostanie oddzielona przez ciąg **separatora** .

Jeśli jedna z wartości źródłowych jest atrybutem wielowartościowym, każda wartość w tym atrybucie zostanie przyłączona wspólnie, oddzielona przez wartość separatora.

**Wejściowe**<br> 

   | Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **rozdzielając** |Wymagany |Ciąg |Ciąg używany do oddzielania wartości źródła, gdy są one połączone w jeden ciąg. Może być "" Jeśli separator nie jest wymagana. |
   | **source1 ... sourceN** |Wymagana zmienna — liczba razy |Ciąg |Ciąg wartości, które mają zostać połączone ze sobą. |

---
### <a name="left"></a>Lewym
**Zharmonizowan**  
Funkcja Left Zwraca określoną liczbę znaków z lewej strony ciągu.

**Obowiązuje**  
`str Left(str string, num NumChars)`

* String: ciąg, z którego mają zostać zwrócone znaki
* NumChars: numer identyfikujący liczbę znaków do zwrócenia od początku (po lewej) ciągu

**Uwagi**  
Ciąg zawierający pierwsze znaki numChars w ciągu:

* Jeśli numChars = 0, zwraca pusty ciąg.
* Jeśli numChars < 0, zwracany ciąg wejściowy.
* Jeśli ciąg ma wartość null, zwracany jest pusty ciąg.

Jeśli ciąg zawiera mniej znaków niż liczba określona w numChars, zwracany jest ciąg identyczny jak ciąg (czyli zawierający wszystkie znaki w parametrze 1).

**Przykład:**  
`Left("John Doe", 3)`  
Zwraca wartość `Joh`.

---
### <a name="mid"></a>MID
**Funkcyjn**<br> MID (źródło, początek, długość)

**Zharmonizowan**<br> Zwraca podciąg wartość źródła. Podciąg jest ciąg zawierający tylko niektóre ze znaków z ciągu źródłowego.

**Wejściowe**<br> 

   | Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **zewnętrz** |Wymagany |Ciąg |Zazwyczaj nazwa atrybutu. |
   | **start** |Wymagany |liczba całkowita |Indeks w ciągu **źródłowym** , w którym powinien zostać uruchomiony podciąg. Pierwszy znak w ciągu ma indeks 1, drugi znak ma indeksu 2 i tak dalej. |
   | **Długość** |Wymagany |liczba całkowita |Długość podciągu. Jeśli długość kończy się poza ciągiem **źródłowym** , funkcja zwróci podciąg z **początkowego** indeksu do końca ciągu **źródłowego** . |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Funkcyjn**<br> NormalizeDiacritics(source)

**Zharmonizowan**<br> Wymaga jednego argumentu ciągu. Zwraca ciąg, ale za pomocą wszystkie znaki diakrytyczne zastępowane znakami diakrytyczne równoważne. Zwykle używane do konwersji imiona i nazwiska, zawierające znaki diakrytyczne (znaki akcentu) do wartości prawne, które mogą być używane w różne identyfikatory użytkownika, takie jak nazwy głównej użytkownika, nazwy konta SAM i adresy e-mail.

**Wejściowe**<br> 

   | Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **zewnętrz** |Wymagany |Ciąg | Zwykle jest to atrybut imię i nazwisko. |

---
### <a name="not"></a>Not
**Funkcyjn**<br> Not(Source)

**Zharmonizowan**<br> Odwraca wartość logiczną **źródła**. Jeśli wartością **źródłową** jest "*true*", zwraca wartość "*false*". W przeciwnym razie zwraca wartość "*true*".

**Wejściowe**<br> 

   | Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **zewnętrz** |Wymagany |Wartości logicznych |Oczekiwane wartości **źródłowe** to "true" lub "false". |

---
### <a name="removeduplicates"></a>RemoveDuplicates —
**Zharmonizowan**  
Funkcja RemoveDuplicates — przyjmuje ciąg o wartości wielowartościowej i upewnij się, że każda wartość jest unikatowa.

**Obowiązuje**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Przykład:**  
`RemoveDuplicates([proxyAddresses])`  
Zwraca oczyszczony atrybut proxyAddress, w którym wszystkie zduplikowane wartości zostały usunięte.

---
### <a name="replace"></a>Replace
**Funkcyjn**<br> Zastąp (źródło oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, szablon)

**Zharmonizowan**<br>
Zamienia wartości ciągu. Działa inaczej w zależności od parametrów podanych:

* Gdy są podane **OldValue** i **replacementValue** :
  
  * Zamienia wszystkie wystąpienia wartości **OldValue** w **źródle** na **replacementValue**
* Gdy jest udostępniana **OldValue** i **szablon** :
  
  * Zamienia wszystkie wystąpienia wartości **OldValue** w **szablonie** na wartość **Source**
* Jeśli **regexPattern** i **replacementValue** są podane:

  * Funkcja stosuje **regexPattern** do ciągu **źródłowego** i można użyć nazw grup wyrażeń regularnych do konstruowania ciągu dla **replacementValue**
* Gdy **regexPattern**, **regexGroupName**, **replacementValue** są podane:
  
  * Funkcja stosuje **regexPattern** do ciągu **źródłowego** i zastępuje wszystkie wartości pasujące do **regexGroupName** **replacementValue**
* Gdy **regexPattern**, **regexGroupName**, **replacementAttributeName** są podane:
  
  * Jeśli **Źródło** nie ma wartości, zwracane jest **Źródło**
  * Jeśli **Źródło** ma wartość, funkcja stosuje **regexPattern** do ciągu **źródłowego** i zastępuje wszystkie wartości pasujące do **regexGroupName** wartością skojarzoną z **replacementAttributeName**

**Wejściowe**<br> 

   | Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **zewnętrz** |Wymagany |Ciąg |Zwykle nazwa atrybutu w obiekcie **źródłowym** . |
   | **oldValue** |Optional (Opcjonalność) |Ciąg |Wartość, która ma zostać zastąpiona w **źródle** lub **szablonie**. |
   | **regexPattern** |Optional (Opcjonalność) |Ciąg |Wzorzec wyrażenia regularnego dla wartości, która ma zostać zastąpiona w **źródle**. Lub, gdy **replacementPropertyName** jest używany, wzorzec wyodrębniania wartości z **replacementPropertyName**. |
   | **regexGroupName** |Optional (Opcjonalność) |Ciąg |Nazwa grupy w **regexPattern**. Tylko wtedy, gdy **replacementPropertyName** jest używany, wyodrębnimy wartość tej grupy jako **replacementValue** z **replacementPropertyName**. |
   | **replacementValue** |Optional (Opcjonalność) |Ciąg |Nowa wartość, aby zastąpić stary certyfikat za pomocą. |
   | **replacementAttributeName** |Optional (Opcjonalność) |Ciąg |Nazwa atrybutu, który ma być używany na potrzeby wartości zamiennej |
   | **formularza** |Optional (Opcjonalność) |Ciąg |Gdy zostanie podana wartość **szablonu** , poszukamy wartości **OldValue** wewnątrz szablonu i Zastąp ją wartością **Source** . |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Funkcyjn**<br> SelectUniqueValue (uniqueValueRule1, uniqueValueRule2 uniqueValueRule3...)

**Zharmonizowan**<br> Wymaga co najmniej dwa argumenty, które są definiowane przy użyciu wyrażeń zasad generowania unikatową wartość. Funkcja ocenia każdą regułę, a następnie sprawdza wartość generowane unikatowość w katalogu/aplikacji docelowej. Pierwszy unikatową wartość znalezione, zostanie zwrócony jeden. Jeśli wszystkie wartości już istnieje w docelowej, wpis będzie pobrać zdeponowane i przyczynę pobiera rejestrowane w dziennikach inspekcji. Nie ma żadnych górnej granicy liczby argumentów, które mogą być podane.

> [!NOTE]
> - To jest funkcja najwyższego poziomu, nie mogą być zagnieżdżone.
> - Nie można zastosować tej funkcji do atrybutów, które mają pasujące pierwszeństwo.  
> - Ta funkcja jest przeznaczone tylko do użytku z Tworzenie wpisu. Gdy jest używany z atrybutem, ustaw właściwość **Zastosuj mapowanie** na **tylko podczas tworzenia obiektu**.
> - Ta funkcja jest obecnie obsługiwana tylko w przypadku "Workday, Active Directory aprowizacji użytkowników". Nie można jej używać z innymi aplikacjami aprowizacji. 


**Wejściowe**<br> 

   | Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **uniqueValueRule1 ... uniqueValueRuleN** |Co najmniej 2 są wymagane, nie górnej granicy |Ciąg | Lista unikatowych reguł generowania wartości do obliczenia. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Funkcyjn**<br> SingleAppRoleAssignment([appRoleAssignments])

**Zharmonizowan**<br> Zwraca pojedynczy appRoleAssignment z listy wszystkich appRoleAssignments przypisanych do użytkownika dla danej aplikacji. Ta funkcja jest wymagana do przekonwertowania obiektu appRoleAssignments na ciąg o pojedynczej nazwie roli. Należy pamiętać, że najlepszym rozwiązaniem jest upewnienie się, że tylko jedna appRoleAssignment jest przypisana do jednego użytkownika w danym momencie i Jeśli przypiszesz wiele ról, zwracany ciąg roli może nie być przewidywalny. 

**Wejściowe**<br> 

  | Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
  |--- | --- | --- | --- |
  | **AppRoleAssignments** |Wymagany |Ciąg |**[appRoleAssignments]** obiekt. |

---
### <a name="split"></a>Podział
**Funkcyjn**<br> Split (Źródło, ogranicznik)

**Zharmonizowan**<br> Dzieli ciąg na tablicę wielowartościową przy użyciu określonego znaku ogranicznika.

**Wejściowe**<br> 

   | Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **zewnętrz** |Wymagany |Ciąg |wartość **źródłowa** do zaktualizowania. |
   | **ogranicznik** |Wymagany |Ciąg |Określa znak, który będzie używany do dzielenia ciągu (przykład: ",") |

---
### <a name="stringfromsid"></a>StringFromSid
**Zharmonizowan**  
Funkcja StringFromSid konwertuje tablicę bajtową zawierającą identyfikator zabezpieczeń na ciąg.

**Obowiązuje**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="stripspaces"></a>StripSpaces
**Funkcyjn**<br> StripSpaces(source)

**Zharmonizowan**<br> Usuwa wszystkie spacje ("") znaków z ciągu źródłowego.

**Wejściowe**<br> 

   | Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **zewnętrz** |Wymagany |Ciąg |wartość **źródłowa** do zaktualizowania. |

---
### <a name="switch"></a>Przełącznik
**Funkcyjn**<br> Przełącznik (źródło, defaultValue, klucz1, wartość1, klucz2, wartość2,...)

**Zharmonizowan**<br> Gdy wartość **źródłowa** jest zgodna z **kluczem**, zwraca **wartość** dla tego **klucza**. Jeśli wartość **źródłowa** nie jest zgodna z żadnymi kluczami, zwraca wartość **DefaultValue**.  Parametry **klucza** i **wartości** muszą zawsze znajdować się w parach. Funkcja zawsze oczekuje parzystą liczbą parametrów.

**Wejściowe**<br> 

   | Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **zewnętrz** |Wymagany |Ciąg |Wartość **źródłowa** do sprawdzenia. |
   | **defaultValue** |Optional (Opcjonalność) |Ciąg |Wartość domyślna ma być używany, gdy źródło nie jest zgodna żadnych kluczy. Może być pustym ciągiem (""). |
   | **Klucz** |Wymagany |Ciąg |**Klucz** do porównywania wartości **źródłowej** z. |
   | **value** |Wymagany |Ciąg |Wartość zastępcza dla **źródła** pasującego do klucza. |

---
### <a name="tolower"></a>toLower
**Funkcyjn**<br> ToLower (Źródło, kultura)

**Zharmonizowan**<br> Pobiera wartość ciągu *źródłowego* i konwertuje ją na małe litery przy użyciu określonych reguł kultury. Jeśli nie określono informacji o *kulturze* , będzie ona używać niezmiennej kultury.

**Wejściowe**<br> 

   | Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
   | --- | --- | --- | --- |
   | **zewnętrz** |Wymagany |Ciąg |Zazwyczaj nazwa atrybutu z obiektu źródłowego |
   | **dziedzinie** |Optional (Opcjonalność) |Ciąg |Format nazwy kultury opartej na dokumencie RFC 4646 to *languagecode2-Country/regioncode2*, gdzie *languagecode2* to kod języka dwuliterowego i *kraj/regioncode2* to kod podkultury dwuliterowej. Przykłady obejmują ja-JP dla języka japońskiego (Japonia) i EN-US dla języka angielskiego (Stany Zjednoczone). W przypadkach, gdy kod języka dwuliterowego nie jest dostępny, używany jest trzyliterowy kod pochodzący z normy ISO 639-2.|

---

### <a name="toupper"></a>ToUpper
**Funkcyjn**<br> ToUpper (Źródło, kultura)

**Zharmonizowan**<br> Pobiera wartość ciągu *źródłowego* i konwertuje ją na wielkie litery przy użyciu określonych reguł kultury. Jeśli nie określono informacji o *kulturze* , będzie ona używać niezmiennej kultury.

**Wejściowe**<br> 

  | Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
  | --- | --- | --- | --- |
  | **zewnętrz** |Wymagany |Ciąg |Zazwyczaj nazwa atrybutu z obiektu źródłowego. |
  | **dziedzinie** |Optional (Opcjonalność) |Ciąg |Format nazwy kultury opartej na dokumencie RFC 4646 to *languagecode2-Country/regioncode2*, gdzie *languagecode2* to kod języka dwuliterowego i *kraj/regioncode2* to kod podkultury dwuliterowej. Przykłady obejmują ja-JP dla języka japońskiego (Japonia) i EN-US dla języka angielskiego (Stany Zjednoczone). W przypadkach, gdy kod języka dwuliterowego nie jest dostępny, używany jest trzyliterowy kod pochodzący z normy ISO 639-2.|

---

### <a name="trim"></a>Trim
**Zharmonizowan**  
Funkcja Trim usuwa wiodące i końcowe białe znaki z ciągu.

**Obowiązuje**  
`str Trim(str value)`  

**Przykład:**  
`Trim(" Test ")`  
Zwraca "test".

`Trim([proxyAddresses])`  
Usuwa spacje wiodące i końcowe dla każdej wartości w atrybucie proxyAddress.

---
### <a name="word"></a>Word
**Zharmonizowan**  
Funkcja słowa zwraca słowo zawarte w ciągu, w oparciu o parametry opisujące ograniczniki do użycia i numer wyrazu do zwrócenia.

**Obowiązuje**  
`str Word(str string, num WordNumber, str delimiters)`

* String: ciąg, z którego ma zostać zwrócony wyraz.
* WordNumber: numer identyfikujący, który numer wyrazu powinien zwrócić.
* Ograniczniki: ciąg reprezentujący ograniczniki, które powinny być używane do identyfikowania wyrazów

**Uwagi**  
Każdy ciąg znaków w ciągu rozdzielony przez jeden ze znaków w ogranicznikach jest identyfikowany jako wyrazy:

* Jeśli liczba < 1, zwraca pusty ciąg.
* Jeśli ciąg ma wartość null, zwraca pusty ciąg.

Jeśli ciąg zawiera mniej niż liczbowe słowa lub ciąg nie zawiera słów identyfikowanych przez ograniczniki, zwracany jest pusty ciąg.

**Przykład:**  
`Word("The quick brown fox",3," ")`  
Zwraca "brązowy"

`Word("This,string!has&many separators",3,",!&#")`  
Zwróci "ma"

## <a name="examples"></a>Przykłady
### <a name="strip-known-domain-name"></a>Nazwa domeny znanych paska
Musisz usunąć nazwę domeny znane z wiadomości e-mail użytkownika, aby uzyskać nazwę użytkownika. <br>
Na przykład jeśli domena "contoso.com", następnie można użyć następującego wyrażenia:

**Wyrażenia** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Przykładowe dane wejściowe/wyjściowe:** <br>

* **Dane wejściowe** (poczta): "john.doe@contoso.com"
* **Wynik**: "Jan. Nowak"

### <a name="append-constant-suffix-to-user-name"></a>Dołącz stałej sufiks do nazw użytkowników
Jeśli używasz piaskownicy usługi Salesforce, może być konieczne dołączyć dodatkowe sufiks do nazw użytkowników przed ich zsynchronizowaniem.

**Wyrażenia** <br>
`Append([userPrincipalName], ".test")`

**Przykładowe dane wejściowe/wyjściowe:** <br>

* **Wejście**: (userPrincipalName): "John.Doe@contoso.com"
* **Dane wyjściowe**: "John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Generowanie alias użytkownika przez złączenie części imię i nazwisko
Należy wygenerować użytkownika aliasu, wykonując pierwsze 3 litery imienia użytkownika i 5 pierwszych liter nazwisko użytkownika.

**Wyrażenia** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Przykładowe dane wejściowe/wyjściowe:** <br>

* **Dane wejściowe** (imię): "Jan"
* **Dane wejściowe** (nazwisko): "Nowak"
* **Dane wyjściowe**: "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Usuń znaki diakrytyczne z ciągu
Należy zastąpić znaki zawierające znaki akcentu równoważne znaki, które nie zawierają znaki akcentu.

**Wyrażenia** <br>
NormalizeDiacritics([givenName])

**Przykładowe dane wejściowe/wyjściowe:** <br>

* **Dane wejściowe** (o podanej): "Zoë"
* **Dane wyjściowe**: "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Dzielenie ciągu na tablicę wielowartościową
Należy wykonać rozdzielaną przecinkami listę ciągów i podzielić je na tablicę, która może być podłączona do atrybutu wielowartościowego, takiego jak PermissionSets. W tym przykładzie lista zestawów uprawnień została wypełniona w extensionAttribute5 w usłudze Azure AD.

**Wyrażenia** <br>
Split ([extensionAttribute5], ",")

**Przykładowe dane wejściowe/wyjściowe:** <br>

* **Input** (extensionAttribute5): "PermissionSetOne, PermisionSetTwo"
* **Output**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Dane wyjściowe daty w postaci ciągu w określonym formacie
Chcesz wysłać daty do aplikacji SaaS w określonym formacie. <br>
Na przykład chcesz formatować daty dla usługi ServiceNow.

**Wyrażenia** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Przykładowe dane wejściowe/wyjściowe:**

* **Wejście** (extensionAttribute1): "20150123105347.1 z"
* **Dane wyjściowe**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Zastąp wartość, na podstawie zestawu wstępnie zdefiniowanych opcji

Musisz zdefiniować strefy czasowej użytkownika, na podstawie kodu stanu, przechowywane w usłudze Azure AD. <br>
Jeśli kod stanu nie odpowiada żadnemu z wstępnie zdefiniowanych opcji, należy użyć wartości domyślnej "Australia/Sydney".

**Wyrażenia** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Przykładowe dane wejściowe/wyjściowe:**

* **Wejście** (stan): "Qld"
* **Dane wyjściowe**: "Australia/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Zamienianie znaków przy użyciu wyrażenia regularnego
Musisz znaleźć znaki, które pasują do wartości wyrażenia regularnego, i usunąć je.

**Wyrażenia** <br>

Replace ([mailNickname],, "[a-za-Z_] *",, "",,)

**Przykładowe dane wejściowe/wyjściowe:**

* **Dane wejściowe** (mailNickname: "john_doe72"
* **Dane wyjściowe**: "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Konwertuj wygenerowaną wartość userPrincipalName (UPN) na małe litery
W poniższym przykładzie wartość UPN jest generowana przez połączenie pól źródłowych PreferredFirstName i PreferredLastName, a funkcja ToLower działa na wygenerowanym ciągu, aby przekonwertować wszystkie znaki na małe litery. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Przykładowe dane wejściowe/wyjściowe:**

* **Wejście** (PreferredFirstName): "Jan"
* **Wejście** (PreferredLastName): "Smith"
* **Dane wyjściowe**: "john.smith@contoso.com"

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Generowanie unikatową wartość dla atrybutu userPrincipalName (UPN)
Oparte na użytkownika imię, drugie imię i nazwisko, należy do generowania wartości atrybutu nazwy UPN i sprawdzić jego unikatowości w katalogu docelowym AD przed przypisaniem wartości do atrybutu nazwy UPN.

**Wyrażenia** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**Przykładowe dane wejściowe/wyjściowe:**

* **Wejście** (PreferredFirstName): "Jan"
* **Wejście** (PreferredLastName): "Smith"
* **Wynik**: "John.Smith@contoso.com", jeśli wartość UPN John.Smith@contoso.com nie istnieje już w katalogu
* **Wynik**: "J.Smith@contoso.com", jeśli wartość UPN John.Smith@contoso.com już istnieje w katalogu
* **Wynik**: "Jo.Smith@contoso.com", jeśli powyższe dwie wartości nazwy UPN znajdują się już w katalogu


## <a name="next-steps"></a>Następne kroki 

- [Co to jest inicjowanie obsługi?](what-is-provisioning.md)
- [Co to jest Azure AD Connect aprowizacji w chmurze?](what-is-cloud-provisioning.md)
